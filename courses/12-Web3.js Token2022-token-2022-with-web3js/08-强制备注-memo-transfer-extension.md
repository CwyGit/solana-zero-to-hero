# 08-Memo Transfer扩展 - 强制备注！📝

嘿，小伙伴！👋

**Memo Transfer扩展**要求所有转账必须包含备注信息！

**比喻说明：** 就像转账必须写用途！

---

## 🎯 什么是Memo Transfer？

**功能：** 强制转账附带备注

**用途：**
- 📝 交易追踪
- 💼 合规要求
- 🏦 会计记录

---

## 💡 使用方法

```typescript
// 1. 启用Memo Transfer
const enableInstruction = createEnableRequiredMemoTransfersInstruction(
    tokenAccount,
    owner
);

// 2. 转账时必须包含Memo
const memoInstruction = createMemoInstruction(
    "Payment for order #12345"
);

// 3. 放在同一个交易中
const transaction = new Transaction()
    .add(memoInstruction)
    .add(transferInstruction);
```

---

**现在小伙伴们懂了吧？** Memo让交易更清晰！📝

---

**最后更新**：2026年1月9日  
**制作风格**：莫式风格
