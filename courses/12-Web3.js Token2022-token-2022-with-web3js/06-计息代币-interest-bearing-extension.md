# 06-Interest Bearing扩展 - 计息代币！💹

嘿，小伙伴！👋

**Interest Bearing扩展**让你的代币可以自动计息，就像银行存款！

**比喻说明：** 就像定期存款会产生利息！

---

## 🎯 什么是Interest Bearing？

**功能：** 代币随时间自动计算利息

**特点：**
- 💰 自动计息
- 📈 利率可调整
- 🔢 视觉显示（不实际增加代币）

---

## 💡 工作原理

**重要：** 这只是视觉效果！
- 不生成新代币
- 只是显示时包含利息
- 需要程序支持才有实际作用

**比喻：** 就像账户显示余额+利息！

---

## 🚀 使用方法

```typescript
const instruction = createInitializeInterestBearingMintInstruction(
    mint,
    rateAuthority,
    rate // 利率basis points
);
```

---

**现在小伙伴们懂了吧？** 计息代币有趣又实用！💹

---

**最后更新**：2026年1月9日  
**制作人**：bruceCao
