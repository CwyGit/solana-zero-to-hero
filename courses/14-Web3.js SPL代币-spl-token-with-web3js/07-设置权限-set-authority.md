# 07-设置权限 - Authority管理！🔐

嘿，小伙伴！👋

**设置权限(Set Authority)**让你转移mint的各种权限！

**比喻说明：** 就像公司换老板！

---

## 🎯 什么是权限？

**类型：**
- 🏭 Mint Authority（铸造权）
- ❄️ Freeze Authority（冻结权）

---

## 💡 使用方法

```typescript
import { setAuthority, AuthorityType } from '@solana/spl-token';

await setAuthority(
    connection,
    payer,
    mint,
    currentAuthority,
    AuthorityType.MintTokens, // 权限类型
    newAuthority // 新权限持有者（null=放弃权限）
);
```

---

## ⚠️ 重要

**放弃权限：** 设置为null后不可恢复！

---

**现在小伙伴们懂了吧？** 权限转移要慎重！🔐

---

**最后更新**：2026年1月10日  
**制作风格**：莫式风格
