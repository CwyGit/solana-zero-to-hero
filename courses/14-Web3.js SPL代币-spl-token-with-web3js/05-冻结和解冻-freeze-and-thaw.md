# 05-冻结和解冻 - 临时锁定！❄️

嘿，小伙伴！👋

**冻结(Freeze)和解冻(Thaw)**让你临时锁定账户的代币！

**比喻说明：** 就像冻结银行卡，需要时解冻！

---

## 🎯 什么是冻结？

**功能：** 临时禁止账户转账

**用途：**
- 🔒 安全保护
- ⚖️ 合规要求
- 🚨 异常处理

---

## 💡 使用方法

```typescript
import { freezeAccount, thawAccount } from '@solana/spl-token';

// 冻结
await freezeAccount(
    connection,
    payer,
    account, // 要冻结的账户
    mint,
    freezeAuthority // 冻结权限
);

// 解冻
await thawAccount(
    connection,
    payer,
    account,
    mint,
    freezeAuthority
);
```

---

## ❓ 常见问题

### Q: 冻结后能取消吗？

**答：** 可以，用thawAccount解冻！

---

**现在小伙伴们懂了吧？** 冻结是临时的！❄️

---

**最后更新**：2026年1月10日  
**制作人**：bruceCao
