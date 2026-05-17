# Prompt 记录

## Week 1 — 学习相关 Prompts

### 1. 学习计划生成
```
请阅读这个文档：ai-web3-school-learning-agent.md
然后帮我开始 AI × Web3 School 的学习计划。

我的情况：
- 水平：高级，已有 Sentinel 项目经验（AI Agent for DeFi）
- 背景：AI + Web3 双边经验
- 时间：每周 8-15 小时
- 目标：深入研究 AI × Web3 交叉方向
```

### 2. 基础概念理解
```
请帮我理解以下概念，并说明它们在 AI × Web3 交叉中的作用：
1. LLM 的工作原理和局限性
2. Agent 的核心组件（状态管理、记忆、工具调用、guardrails）
3. 账户抽象（ERC-4337）如何让 AI Agent 管理智能账户
4. 为什么 AI Agent 需要 guardrails 和 human-in-the-loop
```

### 3. Sentinel 项目分析
```
请分析 Sentinel 项目（MrtWallace/sentinel）的架构：
1. 画出完整的 workflow 流程图
2. 标注每个节点的日志、失败点、回滚策略、人工确认点
3. 对比"纯人工" vs "AI 辅助" vs "Sentinel 自动化"的差异
4. 提出改进建议
```

### 4. Prompt vs Workflow vs Agent 对比
```
请用同一个任务（例如"部署一个 ERC-20 代币合约"）分别用三种方式完成：
1. 纯 Prompt：一次性生成
2. Workflow：预定义流程
3. Agent + Skills：动态规划

对比它们在代码生成、上下文保持、工具调用、可调试性、风险控制上的差异。
```

### 5. Foundry vs Hardhat 对比
```
请对比 Foundry 和 Hardhat 在以下维度的差异：
1. 开发体验（编译、测试、部署）
2. 测试框架（Solidity 测试 vs JavaScript 测试）
3. 性能（编译速度、测试速度）
4. 生态系统（插件、工具链）
5. 适用场景

并用一个简单的 ERC-20 合约作为示例，展示两种工具的使用方式。
```

---

> **Last updated**: 2026-05-17
