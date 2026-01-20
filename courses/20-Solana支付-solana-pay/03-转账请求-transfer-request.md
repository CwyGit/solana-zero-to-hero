# 03-转账请求 - Transfer Request！💸

嘿，小伙伴！👋

Solana Pay转账请求，最简单的支付方式！

**比喻说明：** 像扫码转账！

---

## 🎯 什么是转账请求？

**功能：** 简单的代币转账

**用途：**
- 💰 收款
- 🎁 打赏
- 🛒 商品支付

---

## 💡 使用方法

```typescript
const transferRequest = encodeURL({
    recipient: merchantPublicKey,
    amount: new BigNumber(100),
    splToken: tokenMint,
    label: "商家名称",
    message: "商品描述"
});
```

---

**现在小伙伴们懂了吧？** 转账请求超简单！💸

---

**最后更新**：2026年1月10日  
**制作风格**：莫式风格
