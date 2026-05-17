# Week 1 — Web3 测试网实践记录

## 已有基础：Sentinel 项目

### 部署信息
- **合约**: SmartAccount.sol
- **地址**: `0x3350A693619209193B01399e78d5897766c44074`
- **网络**: Sepolia Testnet
- **区块浏览器**: [Etherscan Sepolia](https://sepolia.etherscan.io/address/0x3350A693619209193B01399e78d5897766c44074)

### 合约功能
- Owner + Agent 双角色权限控制
- 每日支出限额（Daily Spend Limit）
- 支持 ETH 转账和 Uniswap V3 swap

### 实际交互记录
1. **ETH 转账**
   - 指令: "Send 0.001 ETH to 0x742d..."
   - 结果: 成功，交易哈希 [待补充]

2. **Uniswap V3 Swap**
   - 指令: "Swap 0.001 ETH to USDC"
   - 结果: 成功，收到 8.02 USDC

3. **Guardrail 拦截**
   - 指令: "Send 5 ETH to 0xf39F... (黑名单地址)"
   - 结果: 被拦截，提示 "address on blacklist"

4. **阈值确认**
   - 指令: "Send 0.05 ETH to 0x... (超过阈值)"
   - 结果: 需要确认，提示 "Amount 0.05 ETH exceeds threshold"

---

## Week 1 补充任务

### 需要补充的记录
- [ ] 钱包地址说明（EOA vs SmartAccount）
- [ ] 交易哈希收集（至少 3 笔）
- [ ] Gas 消耗分析
- [ ] 区块浏览器截图

### 理解验证
- [ ] 解释为什么 Sentinel 使用 SmartAccount 而不是 EOA
- [ ] 解释 Daily Spend Limit 的安全意义
- [ ] 解释 Guardrails 如何防止 AI Agent 越权

---

> **Last updated**: 2026-05-17
