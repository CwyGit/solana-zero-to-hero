# 🎯 Solana Bootcamp 2026 S1 - 学习指南

> 完成 6 个挑战中的任意 4 个即可毕业！

---

## 📋 挑战任务对照表

| # | 官方挑战 | 对应教程 | 参考答案 | 难度 |
|---|----------|----------|----------|------|
| 1 | [铸造 SPL Token](https://learn.blueshift.gg/zh-CN/challenges/typescript-mint-an-spl-token) | [课程02: Solana代币](../courses/02-Solana代币-tokens-on-solana/) | [答案](./15-typescript-mint-an-spl-token/_solution/README.md) | ⭐ |
| 2 | [Anchor 金库](https://learn.blueshift.gg/zh-CN/challenges/anchor-vault) | [课程04: Anchor开发](../courses/04-Anchor开发教程-anchor-for-dummies/) | [答案](./04-anchor-vault/_solution/README.md) | ⭐⭐ |
| 3 | [Anchor 托管](https://learn.blueshift.gg/zh-CN/challenges/anchor-escrow) | [课程04-05: Anchor+SPL](../courses/05-Anchor SPL代币-spl-token-with-anchor/) | [答案](./01-anchor-escrow/_solution/README.md) | ⭐⭐⭐ |
| 4 | [Pinocchio 金库](https://learn.blueshift.gg/zh-CN/challenges/pinocchio-vault) | [课程06: Pinocchio](../courses/06-Pinocchio开发教程-pinocchio-for-dummies/) | [答案](./14-pinocchio-vault/_solution/README.md) | ⭐⭐⭐ |
| 5 | [Pinocchio 托管](https://learn.blueshift.gg/zh-CN/challenges/pinocchio-escrow) | [课程06: Pinocchio](../courses/06-Pinocchio开发教程-pinocchio-for-dummies/) | [答案](./09-pinocchio-escrow/_solution/README.md) | ⭐⭐⭐⭐ |
| 6 | [Pinocchio AMM](https://learn.blueshift.gg/zh-CN/challenges/pinocchio-amm) (可选) | [课程06: Pinocchio](../courses/06-Pinocchio开发教程-pinocchio-for-dummies/) | [答案](./08-pinocchio-amm/_solution/README.md) | ⭐⭐⭐⭐⭐ |

---

## 🚀 推荐学习路径（4周计划）

### Week 1: TypeScript + Anchor基础
```
Day 1-2: 学习课程01-02 (区块链基础+代币)
Day 3:   完成挑战1 - 铸造SPL Token ✅
Day 4-5: 学习课程04 (Anchor基础)
Day 6-7: 完成挑战2 - Anchor金库 ✅
```

### Week 2: Anchor进阶
```
Day 1-3: 学习课程04账户+指令章节
Day 4-5: 学习课程05 (Anchor SPL)
Day 6-7: 完成挑战3 - Anchor托管 ✅
```

### Week 3: Pinocchio入门
```
Day 1-3: 学习课程06 (Pinocchio基础)
Day 4-5: 对比Anchor实现
Day 6-7: 完成挑战4 - Pinocchio金库 ✅
```

### Week 4: 毕业设计（可选进阶）
```
如果已完成4个挑战，可以:
- 继续完成Pinocchio托管
- 准备毕业设计
- 参加黑客松
```

---

## 📚 每个挑战的学习资源

### 挑战1: 铸造 SPL Token

**前置知识**：
- [x] 了解Solana账户模型
- [x] 了解SPL Token标准

**学习资料**：
| 资源 | 路径 |
|------|------|
| 课程教材 | `courses/02-Solana代币-tokens-on-solana/` |
| 学习笔记 | `courses/_study-notes/02-Solana代币.md` |
| 参考答案 | `challenges/15-typescript-mint-an-spl-token/_solution/` |

**提交方式**：在 Blueshift 平台运行 TypeScript 代码

---

### 挑战2: Anchor 金库

**前置知识**：
- [x] Rust 基本语法
- [x] Anchor 四大宏

**学习资料**：
| 资源 | 路径 |
|------|------|
| 课程教材 | `courses/04-Anchor开发教程-anchor-for-dummies/` |
| 学习笔记 | `courses/_study-notes/04-Anchor开发教程.md` |
| 参考答案 | `challenges/04-anchor-vault/_solution/` |

**关键点**：
- PDA派生地址
- 系统转账CPI
- PDA签名取款

---

### 挑战3: Anchor 托管

**前置知识**：
- [x] Anchor 账户约束
- [x] SPL Token CPI

**学习资料**：
| 资源 | 路径 |
|------|------|
| 课程教材 | `courses/05-Anchor SPL代币-spl-token-with-anchor/` |
| 原始解析 | `challenges/01-anchor-escrow/pages/` |
| 参考答案 | `challenges/01-anchor-escrow/_solution/` |

**关键点**：
- Make/Take/Refund 三个指令
- Token账户创建和转账
- 账户关闭回收租金

---

### 挑战4: Pinocchio 金库

**前置知识**：
- [x] 熟悉Anchor金库实现
- [x] 了解Pinocchio框架

**学习资料**：
| 资源 | 路径 |
|------|------|
| 课程教材 | `courses/06-Pinocchio开发教程-pinocchio-for-dummies/` |
| 学习笔记 | `courses/_study-notes/06-Pinocchio开发教程.md` |
| 参考答案 | `challenges/14-pinocchio-vault/_solution/` |

**关键点**：
- 手动账户验证
- invoke_signed调用
- 零运行时开销

---

## 🎓 毕业条件

- ✅ 完成 6 个任务中的**任意 4 个**
- 🎁 获得 Solana Bootcamp NFT
- 💰 获得 Solana 生态项目空投

---

## 📝 提交流程

1. **挑战提交**：
   - 访问 [Blueshift](https://learn.blueshift.gg/zh-CN)
   - 选择对应挑战
   - 构建程序 `anchor build`
   - 上传 `.so` 文件测试

2. **毕业设计**（可选但推荐）：
   - Fork [官方仓库](https://github.com/Solana-ZH/Solana-bootcamp-2026-s1)
   - 在 `finalProject/` 下创建你的项目介绍
   - 提交 Pull Request

3. **填写毕业问卷**：
   - [问卷链接](https://forms.gle/JeLiYUYfgZwAJUf16)

---

## 🔗 重要链接

| 资源 | 链接 |
|------|------|
| 官方仓库 | https://github.com/Solana-ZH/Solana-bootcamp-2026-s1 |
| 学习平台 | https://learn.blueshift.gg/zh-CN |
| 毕业问卷 | https://forms.gle/JeLiYUYfgZwAJUf16 |

---

**制作人**：bruceCao  
**微信**：zgrbruce123  
**Twitter**：[@sycbruce](https://twitter.com/sycbruce)
