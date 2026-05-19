# Week 2-4 完整计划：共学营 × 黑客松

> 目标：参加黑客松并争取获奖
> 赛道：Agentic Commerce / Payment
> 项目：Sentinel — AI Agent for DeFi（DeepSeek → SmartAccount.sol → Uniswap V3）

---

## 整体时间线

| 周次 | 阶段 | 日期 | 核心任务 |
|------|------|------|---------|
| Week 2 | 共学营 | 5.24-5.30 | 交叉研究 + 方向选择 + Sentinel升级 |
| Week 3 | 共学营+黑客松 | 5.31-6.6 | 实践深化 + 黑客松启动 + 打磨项目 |
| Week 4 | 黑客松 | 6.7-6.13 | 冲刺开发 + 提交 + Demo展示 |

## 每日时间分配（马德里时区）

- **10:00-13:00** 深度工作 → 核心开发/学习
- **14:00-15:30** 轻任务 → 文档/笔记/整理
- **16:00-20:00** 驾校（不可用）
- **21:00-22:30** 轻任务 → 课程提交/社区互动

---

## 🟡 Week 2（5.24-5.30）｜AI×Web3 交叉研究 + 方向选择

### 课程要求

- 画 AI×Web3 问题地图（5+方向）
- 选 2 个方向写"为什么不是纯 AI / 纯 Web3"
- 选 1 个主线方向做 proposal
- 比较 2 个相关协议/标准（如 x402 vs ERC-8004）
- 产出：方向选择 + 问题拆解/流程图 + 项目 proposal

### 每日任务

#### 5.24（六）— 协议学习日

- [ ] 读 x402 Docs（`docs.x402.org`）— agent 支付入口
- [ ] 读 ERC-8004 / ERC-8183 — agent trust、job、escrow、evaluator
- [ ] 读 Cobo CAW 文档（`cobo.com/products/agentic-wallet`）— agent 钱包权限控制
- [ ] 做笔记：每个协议解决 commerce 链路中的哪一段

#### 5.25（日）— 方向选择日

- [ ] 画 AI×Web3 问题地图（5+方向，每个写 AI 作用 + Web3 机制）
- [ ] 确定主线：Agentic Commerce / Payment
- [ ] 写一句说明：为什么选这个方向（真实用户 + 不可替代性）

#### 5.26（一）— Proposal 日

- [ ] 写 proposal 初稿：目标用户、真实场景、最小功能、验证方式、主要风险
- [ ] 比较 x402 vs MPP vs ERC-8004（解决支付/验证/身份/结算/仲裁中的哪一段）
- [ ] 整理成文档提交

#### 5.27（二）— 账户抽象学习日

- [ ] 学 ERC-4337 原理（Alchemy AA 入门）
- [ ] 学 Session Key 概念（Biconomy Smart Account docs）
- [ ] 理解：为什么 Session Key 是 AI×Web3 权限分层的关键

#### 5.28（三）— 多模型实现日

- [ ] 学 OpenRouter 接入（一个 API 多模型切换）
- [ ] Sentinel 升级：加 GPT-4o 做 cross-check（DeepSeek 解析 → GPT-4o 验证 → 不一致时人工确认）

#### 5.29（四）— 前端改版日

- [ ] 让 CC 重做 Sentinel 前端（agent 活动流 + NL 输入框）
- [ ] 你 review 代码，确保理解 CC 改了什么

#### 5.30（五）— 整理提交日

- [ ] 整理 Week 2 产出 → ai-web3-school repo
- [ ] 更新 README 进度

### Week 2 产出清单

- [x] 问题地图（5+方向）
- [x] 主线选择说明（Agentic Commerce）
- [x] 项目 proposal 初稿
- [x] 协议对比文档（x402 vs MPP vs ERC-8004）
- [x] Sentinel 升级：动态 gas + 多模型 cross-check
- [x] 前端改版（CC 执行，你 review）

### 推荐阅读

| 资源 | 链接 | 用途 |
|------|------|------|
| x402 Docs | `docs.x402.org` | 机器/agent 支付入口 |
| ERC-8004 | `eips.ethereum.org/EIPS/eip-8004` | agent trust 协议 |
| ERC-8183 | `eips.ethereum.org/EIPS/eip-8183` | agent job/escrow |
| Cobo CAW | `cobo.com/products/agentic-wallet` | agent 钱包权限 |
| MPP | `docs.stripe.com/payments/machine/mpp` | Machine Payments Protocol |
| ERC-4337 | `alchemy.com/overviews/what-is-account-abstraction` | 账户抽象 |
| Biconomy | `docs.biconomy.io` | Session Key 实现 |
| OpenRouter | `openrouter.ai/docs` | 多模型 API |

---

## 🟠 Week 3（5.31-6.6）｜共学营深化 + 黑客松启动

### 课程要求

- 围绕 Week 2 方向做案例拆解和技术路径讨论
- 完成小型实战练习（agent workflow + 钱包确认、链上收据等）
- 检查项目薄弱环节
- 确定赛道、题目、proposal、组队
- 产出：项目方向 + 实战练习记录 + Hackathon 准备清单

### 每日任务

#### 5.31（六）— 案例拆解日

- [ ] 拆解 1-2 个获奖/经典项目（DAOGenie、MiniDAO、或 ETHGlobal finalist）
- [ ] 分析：它们怎么同时用 AI + Web3、解决什么问题、技术栈是什么
- [ ] 写项目拆解文档

#### 6.1（日）— Session Key 实现日

- [ ] Sentinel 升级：实现 Session Key 机制（给 agent 临时权限，过期自动失效）
- [ ] Sentinel 升级：错误自动诊断 + 重试（tx 失败时自动分析原因）
- [ ] 这是黑客松的杀手级功能点

#### 6.2（一）— 合约增强日

- [ ] Sentinel 升级：动态 slippage 保护（消除 amountOutMinimum=0）
- [ ] Sentinel 升级：多 token 支持（消除 WETH→USDC 硬编码）
- [ ] 补 Python 测试（guardrails + executor pytest）

#### 6.3（二）— 前端完善日（CC 执行）

- [ ] 前端：agent 活动流（实时展示推理过程 + guardrail 决策）
- [ ] 前端：交易历史列表（带 Etherscan 链接）
- [ ] 前端：风险仪表盘（日限额进度 + 异常检测）
- [ ] 你 review CC 代码

#### 6.4（三）— Proposal 终稿日

- [ ] 写 Hackathon proposal 终稿
- [ ] 写一句话 pitch（英文 + 中文）
- [ ] 画架构图（SVG/PNG，不用 ASCII）

#### 6.5（四）— 材料整理日

- [ ] 整理提交材料清单
- [ ] 确认所有 tx hash、合约地址、Etherscan 链接可用
- [ ] 检查 repo 结构、README 质量

#### 6.6（五）— 端到端验证日

- [ ] 本地跑通完整 demo 流程（NL → guardrail → 确认 → 链上执行 → 前端展示）
- [ ] 录一次 dry run，记下需要修的问题

### Week 3 产出清单

- [x] 1-2 个项目拆解文档
- [x] 实战练习记录（Session Key、错误恢复）
- [x] Sentinel 完整升级版（多模型 + 权限 + 测试）
- [x] 前端完整版（活动流 + 可视化 + 历史）
- [x] Hackathon proposal 终稿
- [x] 一句话 pitch + 架构图
- [x] 提交材料清单

---

## 🔴 Week 4（6.7-6.13）｜Hackathon 冲刺 + 提交

### 课程要求

- 集中开发完成核心功能、基础测试、README、演示流程
- Mentor office hour
- 整理 repo、demo link、视频、测试网地址或其他可验证材料
- 通过 X/Twitter 发布项目提交帖 @ 官方账号及赞助方

### 每日任务

#### 6.7（六）— 收尾开发日

- [ ] 最终 bug 修复 + 代码清理
- [ ] README 美化 + 部署文档（让别人能 clone → 跑通）
- [ ] 清理中文 TODO 注释、CLAUDE.md 等个人文件

#### 6.8（日）— Demo 录制日

- [ ] 录制 60 秒 demo 视频
  - 开头 5 秒：一句话 pitch
  - 20 秒：NL 输入 → agent 解析 → guardrail 检查
  - 15 秒：人工确认 → 链上执行 → 区块浏览器验证
  - 15 秒：前端展示（活动流 + 仪表盘）
  - 5 秒：总结 + 为什么需要 AI×Web3
- [ ] 写 X 提交帖（英文为主，可附中文）

#### 6.9（一）— Mentor 反馈日

- [ ] 参加 Mentor office hour
- [ ] 根据反馈做最后调整
- [ ] 端到端测试：新用户 clone → 按 README → 跑通

#### 6.10（二）— 正式提交日

- [ ] 最终提交：
  - 项目名称 + 一句话定义
  - GitHub repo（clean README + 部署文档）
  - Live demo link（如有）
  - 60 秒 demo 视频
  - 合约地址 + 测试网 tx hash
- [ ] 发 X 帖子：
  - @aiweb3school
  - @LXDAO_Official
  - @ETHPanda_Org
  - 对应赞助方账号
  - `#AIxWeb3School`

#### 6.11-13 — 收尾

- [ ] 社区互动（回复评论、转发）
- [ ] 等待结果

### Week 4 最终提交清单

- [ ] 项目名称 + 一句话定义
- [ ] GitHub repo（README + 部署文档）
- [ ] Live demo link
- [ ] 60 秒 demo 视频
- [ ] 合约地址 + 测试网 tx hash
- [ ] X 提交帖 @ 官方 + 赞助方
- [ ] `#AIxWeb3School` hashtag

---

## 🏗️ Sentinel 技术升级路线

```
当前状态（Week 1 结束）
├── ✅ SmartAccount.sol（owner/agent、日限额、Foundry 14 测试）
├── ✅ Agent（DeepSeek intent + guardrails + executor）
├── ✅ 裸 Scaffold-ETH 2 前端
└── ✅ Sepolia 部署

Week 2 升级
├── 🔧 修 5 个 bug（env 变量名、前端地址、swap 硬编码、slippage、正则）
├── ➕ 动态 gas 估算
├── ➕ 多模型 cross-check（DeepSeek + GPT-4o）
└── ➕ 前端改版（CC：NL 输入框 + agent 活动流）

Week 3 升级
├── ➕ Session Key 机制（ERC-4337）
├── ➕ 错误自动诊断 + 重试
├── ➕ 动态 slippage + 多 token 支持
├── ➕ Python 测试（guardrails + executor）
├── ➕ 前端完善（交易历史 + 风险仪表盘 + guardrail 可视化）
└── ➕ 架构图（SVG）

Week 4 收尾
├── 🔧 代码清理 + README 美化
├── 📹 60 秒 demo 视频
├── 📝 X 提交帖
└── 🚀 正式提交
```

---

## 📚 学习资源索引

### Agent 方向

| 主题 | 资源 | 时间 |
|------|------|------|
| Agent 架构 | LangChain Agents 文档 | 2h |
| Structured Output | DeepSeek function calling | 1h |
| 多模型策略 | OpenRouter docs | 1h |
| 错误恢复 | LangGraph retry/fallback | 1h |
| Agent 评估 | precision/recall + 混淆矩阵 | 1h |

### 合约方向

| 主题 | 资源 | 时间 |
|------|------|------|
| ERC-4337 | Alchemy AA 入门 | 2h |
| Session Key | Biconomy Smart Account | 1h |
| Gas 机制 | EIP-1559 详解 | 1h |
| OpenZeppelin | AccessControl + ReentrancyGuard | 1h |
| Safe 多签 | Safe{Wallet} docs | 1h |

### 前端方向（够用就行）

| 主题 | 资源 | 时间 |
|------|------|------|
| SE-2 hooks | scaffold-eth-2 GitHub 源码 | 2h |
| 前后端交互 | REST API / WebSocket / SSE | 1h |
| Next.js 基础 | 能看懂 CC 代码即可 | 2h |

---

## 🎯 你的核心优势

1. **已有完整项目**（Sentinel）— 多数人 Week 3 才开始写代码
2. **合约层扎实**（Foundry 测试 + Sepolia 部署）— 合约是多数人的短板
3. **Agent 实战经验**（DeepSeek + guardrails + eval）— 不是从零开始
4. **Hermes Agent 熟练**— 本身就是 AI×Web3 的活案例
5. **独立开发者**— 不需要组队协调，决策快

## ⚠️ 需要警惕的风险

1. **前端是最大短板**— 依赖 CC，需要你有能力 review 和 debug
2. **不要过度优化**— 黑客松看的是"能跑 + 有亮点"，不是生产级
3. **时间管理**— 驾校占用下午 4 小时，深度工作集中在上午
4. **Week 1 收尾**— 还有 ~180 分的课程任务没交，别忘了补

---

*文档创建时间：2026-05-20*
*项目：Sentinel — AI Agent for DeFi*
*赛道：Agentic Commerce / Payment*
