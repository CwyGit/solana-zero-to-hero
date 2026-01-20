# 01-Litesvm 101 - 轻量级SVM！💨

嘿，小伙伴！👋

**Litesvm**是一个Solana程序的**轻量级测试框架**，它把SVM（Solana Virtual Machine）封装得简单易用！

**比喻说明：** 就像迷你版的Solana，专门用来测试，快速又方便！

---

## 🎯 什么是Litesvm？

**Litesvm**提供了一个精简但功能强大的Solana测试环境，让你可以：

✅ **快速测试** - 比完整validator快得多  
✅ **简单API** - 易学易用  
✅ **完整功能** - 支持大部分Solana特性  

**比喻：** 就像玩具车vs真车，功能类似但更轻便！

---

## 🚀 为什么选择Litesvm？

### 优势对比

| 特性 | Litesvm | solana-test-validator |
|------|---------|----------------------|
| 速度 | 极快⚡ | 较慢 |
| 资源占用 | 很低 | 较高 |
| 设置复杂度 | 简单 | 复杂 |
| 适用场景 | 单元测试 | 集成测试 |

**小伙伴们要特别注意啦：**

> 💡 Litesvm适合**快速迭代开发**，而不是完整的集成测试！

---

## 📦 安装

```bash
cargo add litesvm --dev
```

---

## 🔧 基本用法

### 1. 创建LiteSVM实例

```rust
use litesvm::LiteSVM;

let mut svm = LiteSVM::new();
```

**作用：** 创建一个轻量级的SVM环境！

---

### 2. 加载程序

```rust
svm.add_program_from_file(program_id, "path/to/program.so");
```

---

### 3. 执行交易

```rust
let result = svm.send_transaction(transaction);
```

---

## 💡 学习建议

**本章重点：**
- ✓ 理解Litesvm的定位
- ✓ 知道何时使用
- ✓ 掌握基本API

**下一章：** 看Rust和TypeScript的具体使用！

---

**现在小伙伴们懂了吧？** Litesvm让测试变得轻量快速！💨

---

**最后更新**：2026年1月9日  
**制作人**：bruceCao  
**学习建议**：Litesvm很简单，快速上手！