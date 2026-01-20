# 10-Mint Close Authority扩展 - 可关闭的Mint！🗑️

嘿，小伙伴！👋

**Mint Close Authority扩展**允许关闭不再使用的mint账户并回收租金！

**比喻说明：** 就像注销不用的银行卡退回押金！

---

## 🎯 什么时候有用？

**场景：**
- 🧪 测试代币清理
- 💸 回收租金SOL
- 🧹 账户清理

**条件：** 供应量必须为0！

---

## 🚀 使用方法

```typescript
// 1. 创建带此扩展的Mint
const instruction = createInitializeMintCloseAuthorityInstruction(
    mint,
    closeAuthority
);

// 2. 当供应量为0时关闭
const closeInstruction = createCloseMintInstruction(
    mint,
    destination, // 接收租金的账户
    closeAuthority
);
```

---

**现在小伙伴们懂了吧？** 可以回收租金很环保！🗑️

---

**最后更新**：2026年1月9日  
**制作风格**：莫式风格
