# 11-Non-Transferable扩展 - 灵魂绑定！⛓️

嘿，小伙伴！👋

**Non-Transferable扩展**创建不可转移的代币，永远绑定在账户上！

**比喻说明：** 就像驾照，只属于你不能转移！

---

## 🎯 什么是Non-Transferable？

**功能：** 代币无法转账

**可以做：**
- ✅ Mint（铸造）
- ✅ Burn（销毁）  
- ❌ Transfer（转账）

---

## 💡 使用场景

**最适合：**
- 🎓 证书凭证
- 🏅 成就徽章
- 🆔 身份标识
- 👤 灵魂绑定代币(SBT)

---

## 🚀 使用方法

```typescript
const extension = ExtensionType.NonTransferable;

// 创建时添加扩展
const mint = await createMintWithExtension(
    connection,
    payer,
    [extension]
);
```

---

**现在小伙伴们懂了吧？** 灵魂绑定很特别！⛓️

---

**最后更新**：2026年1月9日  
**制作人**：bruceCao
