# 09-Metadata扩展 - 链上元数据！🏷️

嘿，小伙伴！👋

**Metadata扩展**让你直接在mint账户中存储代币元数据！

**比喻说明：** 就像给商品贴标签！

---

## 🎯 什么是Metadata？

**功能：** 在mint中直接存储：
- 📛 名称(name)
- 🔤 符号(symbol)
- 🖼️ URI(图片链接)
- 📝 额外数据

**好处：** 不需要额外程序！

---

## 🚀 使用方法

```typescript
const metadata = {
    name: "My Token",
    symbol: "MTK",
    uri: "https://example.com/metadata.json"
};

// 创建带Metadata的Mint
const mint = await createMintWithMetadata(
    connection,
    payer,
    metadata
);
```

---

**现在小伙伴们懂了吧？** Metadata让代币有身份！🏷️

---

**最后更新**：2026年1月9日  
**制作风格**：莫式风格
