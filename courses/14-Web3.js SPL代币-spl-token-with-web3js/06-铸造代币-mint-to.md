# 06-铸造代币 - Mint新币！⚡

嘿，小伙伴！👋

**铸造(Mint)**让你创造新的代币！

**比喻说明：** 就像印钞厂印钞票！

---

## 🎯 什么是铸造？

**功能：** 增加代币供应量

**用途：**
- 💰 发行新币
- 🎁 奖励分发
- 📈 增加流通

---

## 💡 使用方法

```typescript
import { mintTo } from '@solana/spl-token';

await mintTo(
    connection,
    payer,
    mint, // mint地址
    destination, // 接收账户
    mintAuthority, // 铸造权限
    amount // 数量
);
```

---

## ⚠️ 注意

**权限：** 需要mint authority！

---

**现在小伙伴们懂了吧？** 铸造要有权限！⚡

---

**最后更新**：2026年1月10日  
**制作风格**：莫式风格
