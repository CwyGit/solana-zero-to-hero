# 08-转账 - Transfer代币！💸

嘿，小伙伴！👋

**转账(Transfer)**是最基本的代币操作！

**比喻说明：** 就像银行转账！

---

## 🎯 什么是转账？

**功能：** 将代币从A账户转到B账户

**用途：**
- 💸 支付
- 🎁 赠送
- 💱 交易

---

## 💡 使用方法

```typescript
import { transfer } from '@solana/spl-token';

await transfer(
    connection,
    payer,
    source, // 源账户
    destination, // 目标账户
    owner, // 源账户所有者
    amount // 数量
);
```

---

## ❓ 常见问题

### Q: 转账需要什么？

**答：** 
- 源账户有足够余额
- 目标账户已创建
- 有owner签名

---

**现在小伙伴们懂了吧？** 转账很简单！💸

---

**最后更新**：2026年1月10日  
**制作风格**：莫式风格
