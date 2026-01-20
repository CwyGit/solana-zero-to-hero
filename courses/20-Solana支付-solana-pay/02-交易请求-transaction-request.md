# 02-交易请求 - Transaction Request！📋

嘿，小伙伴！👋

Solana Pay交易请求，灵活的支付方式！

**比喻说明：** 像生成支付二维码！

---

## 🎯 什么是交易请求？

**功能：** 生成自定义交易

**用途：**
- 🎫 购票支付
- 🎮 游戏内购
- 📦 复杂交易

---

## 💡 使用方法

```typescript
const txRequest = encodeURL({
    link: new URL(apiUrl),
    label: "商品名称",
    message: "支付说明"
});
```

---

**现在小伙伴们懂了吧？** 交易请求很灵活！📋

---

**最后更新**：2026年1月10日  
**制作人**：bruceCao
