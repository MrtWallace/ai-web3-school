# Week 1 — Day 1-2 完成总结

## ✅ 已完成任务

### 1. GitHub Repo 创建
- **链接**: https://github.com/MrtWallace/ai-web3-school
- **Commits**: 3 个有意义的提交
  1. `feat: 初始化 AI × Web3 School 学习 repo`
  2. `docs: 添加 prompts 记录和 .gitignore`
  3. `feat: 添加可交互概念卡片`

### 2. Repo 结构
```
ai-web3-school/
├── README.md                    # 个人介绍 + 学习目标 + 进度追踪
├── resources.md                 # 资源收集 + Follow List
├── .gitignore                   # 忽略依赖和构建产物
├── prompts/
│   └── week1-prompts.md         # 学习相关 Prompt 记录
├── logs/
│   └── week1-agent-log.md       # Learning Agent 使用日志
├── demos/
│   └── concept-cards.html       # 可交互概念卡片
└── week1/
    ├── concepts.md              # AI × Web3 核心概念说明
    ├── web3-lab/
    │   └── practice-record.md   # 测试网实践记录
    └── cross-experiment/
        └── sentinel-workflow.md # Sentinel 交叉实验流程拆解
```

### 3. Learning Agent 配置
- **工具**: Hermes Agent (mimo-v2.5-pro)
- **配置文件**: `ai-web3-school-learning-agent.md`
- **使用记录**: `logs/week1-agent-log.md`

### 4. 可交互产物
- **概念卡片**: `demos/concept-cards.html`
- 包含 12 个核心概念（8 AI + 6 Web3 + 3 交叉）
- 响应式设计，支持移动端

---

## 📊 进度追踪

### Week 1 交付清单

| # | 交付物 | 状态 |
|:--|:--|:--|
| 1 | `ai-web3-school` repo + README + 3+ commits | ✅ |
| 2 | Learning agent 配置记录（Hermes） | ✅ |
| 3 | 测试网实践记录（已有 Sentinel，补充笔记） | ⏳ |
| 4 | 基础概念说明（LLM/workflow/agent/钱包/签名/交易/合约） | ✅ |
| 5 | 交叉实验说明（Sentinel workflow 拆解 + 流程图） | ✅ |
| 6 | **高级 A** — Prompt vs Workflow vs Agent 对比 | ⏳ |
| 7 | **高级 B** — Foundry vs Hardhat + 权限设计 | ⏳ |
| 8 | **高级 D** — EVM/Gas/共识 理论笔记 | ⏳ |
| 9 | 行业观察 Follow List + 3-5 条笔记 | ⏳ |

---

## 🚀 下一步：Day 3-4

### 高级轨道 E — 交叉实验升级

**目标**: 把 Sentinel 项目正式化为标准 workflow

**任务**:
1. 画出完整的 workflow 流程图（Mermaid 或手绘）
2. 标注每个节点的日志、失败点、回滚策略、人工确认点
3. 对比"纯人工" vs "AI 辅助" vs "Sentinel 自动化"的差异

**产出文件**: `week1/cross-experiment/sentinel-workflow.md`（已创建框架，需补充细节）

---

## 📝 学习笔记

### 今日收获
1. **AI × Web3 的核心是交叉点**: 不是两个独立赛道，而是 AI Agent 自主执行链上操作
2. **Guardrails 是关键**: AI Agent 必须有边界，涉及资产、密钥、权限的操作必须人工确认
3. **账户抽象是基础设施**: ERC-4337 让 AI Agent 可以管理智能账户，Session Key 限制权限
4. **Workflow vs Agent 的选择**: 流程固定用 Workflow，需要动态规划用 Agent

### 待深入研究
- [ ] ZKML — 可验证 AI 推理
- [ ] Autonomous Agent DAO — AI Agent 自主治理
- [ ] On-chain AI Analytics — 链上数据分析
- [ ] Verifiable Trading Agent — 可验证交易 Agent

---

> **Last updated**: 2026-05-17
