# Agent 记忆系统优化实践：从诊断到三层架构

> 基于 Hermes Agent + Hindsight 的实际优化案例，涵盖 Agent Memory 全景分析、Hindsight 内部机制深度解析、以及三层记忆架构的设计与实施。

---

## 一、为什么需要优化 Agent 记忆系统？

AI Agent 的记忆系统决定了它能否在长期交互中保持上下文连贯性。当前主流方案各有短板：

| 方案 | 核心问题 |
|------|---------|
| 纯上下文窗口 | token 上限硬性限制，超出即遗忘 |
| 传统 RAG | 存原文 chunk，碎片化，无去重 |
| 知识图谱 | 维护成本高，schema 设计难 |
| 全量注入 | 每轮都塞所有记忆，token 浪费严重 |

本文记录了一次完整的 Agent 记忆系统优化实践，从问题诊断到架构设计，最终实现 **每轮 token 开销降低 38%**。

---

## 二、Agent Memory 系统全景

### 2.1 六大架构

| 架构 | 原理 | 代表产品 | 优点 | 缺点 |
|------|------|---------|------|------|
| **Context Window** | 对话历史直接塞进 prompt | ChatGPT | 零延迟、无依赖 | 硬性 token 上限 |
| **RAG-based** | 文档 → chunk → embedding → 向量检索 | LangChain, LlamaIndex | 理论无限容量 | 碎片化、跨 session 关联难 |
| **Structured** | 实体/关系 → 知识图谱 → 图查询 | Zep v2, GraphRAG | 关系推理强 | 维护成本高 |
| **Memory-as-a-Service** | 自动从对话提取 facts → 分类存储 | Mem0, Hindsight | 开箱即用 | 黑箱、依赖 LLM 质量 |
| **Agentic/Self-Editing** | Agent 自主决定存/改/删 | MemGPT/Letta | 最灵活 | 依赖 Agent 判断力 |
| **Tiered Memory** | 短期→中期→长期分层 | MemGPT | 最接近人脑 | 实现复杂 |

### 2.2 产品对比

| 产品 | 核心机制 | 开源 | 适合场景 |
|------|---------|------|---------|
| **MemGPT/Letta** | 分层 + 自编辑 | ✅ | 需要 OS 级内存管理的复杂 Agent |
| **Mem0** | 自动提取 + RAG | ✅ | 快速给现有 Agent 加记忆 |
| **Zep** | 图记忆 + 时序 | ✅ | 需要关系推理的商业应用 |
| **Hindsight** | 自动提取 + consolidation + 知识图谱 | ✅ | 隐私敏感、本地部署 |
| **LangChain Memory** | 多种策略组合 | ✅ | 已在 LangChain 生态中的项目 |

---

## 三、Hindsight 深度解析

Hindsight 是本文实践的记忆系统。通过源码分析和 API 测试，完整了解了其内部机制。

### 3.1 完整数据流

```
原始对话
    │
    ▼  LLM 提取（fact_extraction.py）
    │  调用 DeepSeek，从对话中提取关键事实
    │  输出两种 fact_type：
    │  ├── "world"     → 关于用户/外部世界的客观事实
    │  └── "assistant" → AI 自己的动作/决定/发现
    │
    │  存入 memory_units 表（原始 fact）
    │
    ▼  Consolidation（consolidator.py，异步 worker）
    │  把多个相关 fact 合并成精炼的 observation
    │  LLM 理解后重写，不是简单拼接
    │  规则：同一方面 → UPDATE，新方面 → CREATE，矛盾 → DELETE
    │
    │  存入 observations 表（合并后的稳定事实）
    │
    ▼  Mental Model / Reflect（按需触发）
    │  用户定义查询，LLM 从所有记忆中合成高层总结
    │
    │  存入 mental_models 表（经验总结）
    │
    ▼  召回（每轮或按需）
    │  embedding + 向量搜索 + 图遍历 + 实体匹配
    │  注入到当前对话的上下文中
```

### 3.2 三种记忆类型

| 类型 | 来源 | 说明 |
|------|------|------|
| **world** | LLM 直接提取 | 对话中的客观事实/世界知识 |
| **observation** | Consolidation 合并 | 多个 fact 合并后的精炼事实（不是原始提取物） |
| **experience** | Mental Model Reflect | 通过 LLM 反思生成的高层经验 |

**关键理解：**
- observation 不是原始提取物，而是 consolidation 的产物
- experience 是 mental model 的输出，需要触发，不是自动生成
- world 是原始提取物，LLM 直接从对话中标记为"世界知识"

### 3.3 Consolidation 合并机制

Consolidation 不是简单的"拼在一起"，而是 **LLM 理解后重写**：

```
Step 1: 收集新提取的 fact
Step 2: 向量搜索已有的相关 observation
Step 3: 把新 fact + 已有 observation 一起发给 LLM
Step 4: LLM 返回 JSON：
  ├── CREATE: 全新方面 → 新建 observation
  ├── UPDATE: 同一方面 → 更新已有 observation
  └── DELETE: 矛盾或重复 → 删除旧 observation
Step 5: 写入 observations 表
```

关键设计：
- 每个 observation 只跟踪一个特定方面（一个实体、一个数量、一个关系）
- 同一方面的新 fact 会 UPDATE 旧 observation，不会创建重复
- observation 有 `source_memory_ids` 追溯来源 fact
- `proof_count` 记录支持该 observation 的 fact 数量
- LLM 会重写成干净散文，不复制原始 fact 的元数据

### 3.4 存储结构

```
memory_units 表（原始 fact）
├── fact_type: "world" | "assistant"
├── text: 原始提取的事实
├── entities: 实体列表
├── occurred_start/end: 时间
├── embedding: 向量（bge-small-en-v1.5）
└── tags: session:xxx 等标签

observations 表（consolidation 合并结果）
├── fact_type: "observation"
├── text: 精炼后的单一事实
├── source_memory_ids: 来源 fact 的 UUID 列表
├── proof_count: 支持该 observation 的 fact 数量
└── tags: 从 source fact 继承

mental_models 表（reflect 合成结果）
├── fact_type: "experience"
├── text: LLM 合成的高层经验
└── query: 触发这个 mental model 的查询

entities 表（实体图谱）
├── canonical_name: 标准化名称
├── mention_count: 被提及次数
├── observations: 关联的 observation 列表
└── 链接：temporal / semantic / entity / caused_by
```

### 3.5 召回机制

```
用户消息到达
    │
    ▼  截取最近 N 字符作为查询
    │
    ▼  调用 Hindsight API recall
    │  参数：
    │  ├── budget: low/mid/high → 控制搜索深度
    │  ├── max_tokens → 返回结果的总 token 上限
    │  ├── types → 按类型过滤（observation/world/experience）
    │  └── tags → 按标签过滤
    │
    ▼  API 内部流程：
    │  1. 查询 → embedding（本地模型，免费）
    │  2. 向量搜索 memory_units + observations
    │  3. 图遍历：temporal/semantic/entity 链接扩展
    │  4. 实体匹配：查询中的实体名 → 关联 observation
    │  5. 合并去重 + 按相关度排序
    │  6. 截断到 max_tokens 预算
    │
    ▼  返回结果，注入到当前对话上下文
```

**注意：** API 的 `limit` 和 `threshold` 参数实际上被忽略，返回数量由 `max_tokens` 控制。

### 3.6 LLM 调用链

```
每次 retain（定期触发）
├── ① fact extraction  → 调 LLM（DeepSeek）提取 fact     ← 有成本
└── ② consolidation    → 调 LLM 合并 fact → observation  ← 有成本

每次 recall（每轮或按需）
└── ③ embedding        → 本地模型（bge-small-en-v1.5）   ← 免费

手动 reflect（按需）
└── ④ reflect          → 调 LLM 合成经验                 ← 高成本（~78K input）
```

Hindsight 的"智能"全部来自 LLM 调用。embedding 只做向量化，语义理解、去重、合并、重写全靠 LLM。如果 LLM API 挂了，retain 和 consolidation 会失败，但 recall（纯向量搜索）仍可用。

### 3.7 Hindsight vs 传统 RAG

| | 传统 RAG | Hindsight |
|---|---|---|
| 数据源 | 文档 chunk 直接存 | 结构化知识图谱 |
| 检索 | embedding 相似度 | embedding + 图遍历 + 实体匹配 |
| 存储 | 原文 chunk | LLM 提取的 fact + consolidation 的 observation |
| 去重 | 无（同一 chunk 可重复返回） | consolidation 自动合并，proof_count 追踪 |
| 更新 | 重新 embed 整个文档 | observation 可 UPDATE/DELETE |
| 质量 | 依赖 chunk 切分质量 | 依赖 LLM 提取质量 |

**核心区别：** RAG 是"存原文搜原文"，Hindsight 是"存理解后的知识搜知识"。

---

## 四、优化诊断

### 4.1 每轮 Token 消耗分布

通过分析 session 文件，量化了每轮的固定开销：

| 组件 | Token | 占比 |
|------|-------|------|
| Tool 定义 (35个) | 12,731 | 69.6% |
| Skills 列表 | 3,227 | 17.6% |
| MEMORY.md | 574 | 3.1% |
| USER.md | 361 | 2.0% |
| 静态人格 | 639 | 3.5% |
| Hindsight config | 52 | 0.3% |
| **合计** | **~17,000** | — |

**关键发现：** Memory 本身只占 ~988 tokens (5.4%)，真正的 token 大户是 Tool 定义 (12.7K) 和 Skills (4K)。

### 4.2 工具使用分析

扫描 200 个 session 的实际 tool 调用记录：

| 分类 | 工具数 | 说明 |
|------|--------|------|
| 高频（>100 次） | 8 | terminal, web_search, browser, execute_code 等 |
| 偶尔（3-100 次） | 12 | search_files, delegate_task, session_search 等 |
| 罕见（<3 次） | 5 | vision, hindsight_reflect, cronjob 等 |
| **从未使用** | **45** | rl_training, kanban, yuanbao, feishu, homeassistant 等 |

### 4.3 Hindsight 问题诊断

通过 API 测试和源码分析发现：

| 问题 | 严重度 | 说明 |
|------|--------|------|
| observation/experience 双重存储 | 高 | 同一件事存两遍，26 条语义重叠 |
| limit 参数无效 | 中 | 请求 limit=5 返回 75 条 |
| auto_recall 每轮注入 | 高 | 不管对话是否需要历史，都注入 ~4K tokens |
| 记忆增长过快 | 中 | retain_every_n_turns=1，日增 ~80 条 |
| 无定期清理机制 | 中 | 过时记忆长期占用上下文 |

---

## 五、三层记忆架构设计

### 5.1 设计思路

参考 MemGPT 的分层思路，结合 Hindsight 的特性，设计了三层架构：

```
┌─────────────────────────────────────────────────┐
│  热层（每轮注入 ~1,150 tokens）                    │
│  ├── 环境配置、API 引用、核心偏好                   │
│  ├── 用户画像                                      │
│  └── 从 Hindsight 蒸馏的高频知识                   │
│  特点：稳定、手动维护、prefix-cache 友好             │
└─────────────────────────────────────────────────┘
           │
           │  Agent 判断需要时调用
           ▼
┌─────────────────────────────────────────────────┐
│  温层（按需注入 ~3,072 tokens）                    │
│  └── Hindsight recall — 跨 session 记忆           │
│  触发条件：用户提到"上次/之前"、继续未完成的工作、  │
│  跨 session 项目上下文、不在热层中的用户习惯        │
│  特点：自动提取、consolidation 去重、图结构关联      │
└─────────────────────────────────────────────────┘
           │
           │  手动搜索
           ▼
┌─────────────────────────────────────────────────┐
│  冷层（不注入，按需搜索）                          │
│  ├── 完整笔记（Obsidian notes）                    │
│  └── 对话记录（Session files）                     │
│  特点：不主动注入，需要时精确搜索                    │
└─────────────────────────────────────────────────┘
```

### 5.2 为什么选择这个方案

| 方案 | 优点 | 缺点 | 是否采用 |
|------|------|------|---------|
| 全量注入（当前） | 简单 | 每轮浪费 ~3K tokens | ❌ |
| 纯 RAG 替代 | 理论无限容量 | 碎片化、无 consolidation | ❌ |
| 自建分层 | 完全可控 | 开发成本高 | ❌ |
| 关闭 auto_recall + 蒸馏 | 利用已有基础设施 | 需要 agent 判断力 | ✅ |
| 等 PR #31163（渐进式披露） | 最优解 | 需要等上游 | 后续升级 |

选择"关闭 auto_recall + 蒸馏"的原因：
- Hindsight 已经安装运行，有 242 条记忆和 204 个实体
- 内置 memory 文件是 prefix-cache 友好的稳定注入
- 不需要自建 RAG 或知识图谱
- PR #31163 合并后再升级，当前方案是最佳过渡

---

## 六、执行

### 6.1 P0: Hindsight 去重

**问题：** retain_every_n_turns=1，每轮都存，3 天堆了 242 条记忆。

**方案：** 增大 retain 间隔，从每轮改为每 5 轮。

**效果：** 记忆增长速度降低 80%。

### 6.2 P1: Memory 瘦身

**问题：** 两个 memory store 都 98% 满了，无法存入新信息。API key 明文存储浪费空间。

**方案：**
- 删除过时条目（一次性问题、已完成任务）
- API key 迁移到 .env 文件，memory 中只存引用链接
- 合并重叠条目

**效果：** 两个 store 从 98% 降到 70-74%，腾出 ~880 字符空间。

### 6.3 P2: Tool 精简

**问题：** 35 个 tool 每轮注入 ~12,731 tokens，其中 45 个从未使用。

**方案：** 关闭 6 个明确不需要的 toolset（强化学习、看板、视频分析、跨平台消息、AI 画图、语音合成）。

**效果：** Tool 数量从 35 降到 ~29，省 ~4,400 tokens/轮。

### 6.4 P3: Hindsight 召回质量优化

**问题：** recall 每轮注入 4,096 tokens，limit 参数无效，observation/experience 重复。

**方案：**
- max_tokens 从 4096 降到 3072（砍 25%，不砍 50% 更安全）
- 过滤 experience 类型（保留 observation + world，消除 26 条重复）
- 保留 mid budget（low 会丢跨 session 关联）

**效果：** 每轮 recall 从 ~4,096 降到 ~3,072 tokens，重复消除。

### 6.5 P4: 三层记忆架构

**问题：** auto_recall=true，不管对话内容是否需要历史，每轮都注入 ~3,072 tokens。

**方案：**
1. 关闭 auto_recall（温层不再每轮注入）
2. 热层加入 recall 触发规则（agent 知道什么时候该调用）
3. 创建 HOT_INSIGHTS.md 作为蒸馏目标
4. 合并每周 cron：Memory 审计 + Hindsight 蒸馏 + 数据治理 + 健康检查

**数据治理规则：**
- >30 天未 recall 的记忆 → 打 `cold:auto` 标签（软删除）
- 保护：技术知识标签 + proof_count>2 的记忆
- 合并高度相似 observation（前 60 字符匹配 → 保留 proof_count 高的）

**效果：**
- 日常对话：~1,150 tokens（只有热层）
- 涉及历史：~1,150 + ~3,072 = ~4,222 tokens（热层 + 按需温层）
- 深度搜索：按需调用冷层

---

## 七、优化效果总结

| 指标 | 优化前 | 优化后 | 改善 |
|------|--------|--------|------|
| Memory store 使用率 | 98% | 70-74% | -24~28% |
| Hindsight retain 频率 | 每轮 | 每 5 轮 | -80% |
| Hindsight recall | 每轮自动 | 按需 | 日常省 3,072 tokens |
| Hindsight recall token | 4,096 | 3,072 | -25% |
| Hindsight recall 类型 | 全部 | observation+world | 过滤 experience |
| Tool 数量 | 35 | ~29 | -6 个 |
| Tool token 消耗 | ~12,731 | ~8,300 | -35% |
| **日常每轮 token 开销** | **~17,000** | **~10,500** | **-38%** |
| 定期维护 | 无 | 每周自动四合一 | 新增 |

---

## 八、待完成与未来方向

| 项目 | 状态 | 说明 |
|------|------|------|
| 渐进式披露（PR #31163） | 等待合并 | 自动按需加载 tool，合并后可进一步省 ~3K tokens |
| Skills 瘦身 | 等 PR 合并 | 当前 ~3,200 tokens/轮，可按需加载 |
| 上下文压缩调优 | 按需 | 长对话时可调整压缩阈值 |
| 模型成本优化 | 未开始 | 压缩/cron 可用更便宜的模型 |

---

## 九、技术参考

### 关键配置文件

```
~/.hermes/config.yaml          # Hermes 主配置
~/.hermes/.env                 # API keys
~/.hermes/hindsight/config.json # Hindsight 配置
~/.hermes/memories/MEMORY.md   # 热层：环境/配置
~/.hermes/memories/USER.md     # 热层：用户画像
~/.hermes/memories/HOT_INSIGHTS.md # 热层：蒸馏知识
```

### Hindsight 配置参考

```json
{
  "mode": "local_external",
  "api_url": "http://localhost:8888",
  "auto_retain": true,
  "auto_recall": false,
  "retain_every_n_turns": 5,
  "recall_budget": "mid",
  "recall_max_tokens": 3072,
  "recall_types": ["observation", "world"],
  "recall_max_input_chars": 600
}
```

### 核心 API 端点

```
GET  /health                              # 健康检查
GET  /v1/default/banks/{bank_id}/stats    # 银行统计
POST /v1/default/banks/{bank_id}/memories/recall  # 召回
POST /v1/default/banks/{bank_id}/reflect  # 反思合成
GET  /v1/default/banks/{bank_id}/memories/list    # 记忆列表
GET  /v1/default/banks/{bank_id}/entities         # 实体列表
GET  /v1/default/banks/{bank_id}/graph            # 图结构
```
