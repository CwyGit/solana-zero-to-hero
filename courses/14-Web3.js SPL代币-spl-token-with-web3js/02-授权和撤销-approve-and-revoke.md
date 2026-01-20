# 02-授权和撤销 - 代理管理！🔑

嘿，小伙伴！👋

**授权(Approve)和撤销(Revoke)**让你可以委托别人管理你的代币！

**比喻说明：** 就像给朋友一把备用钥匙，需要时可以收回！

---

## 🎯 什么是授权？

**功能：** 允许其他账户代表你操作代币

**用途：**
- 🤝 DEX交易授权
- 💱 自动交易程序
- 🎮 游戏内代理

---

## 💡 使用方法

```typescript
import { approve, revoke } from '@solana/spl-token';

// 授权
await approve(
    connection,
    payer,
    account, // 你的代币账户
    delegate, // 代理人
    owner, // 你
    amount // 授权额度
);

// 撤销
await revoke(
    connection,
    payer,
    account,
    owner
);
```

---

## ⚠️ 注意事项

**安全提示：**
- 只授权必要的额度
- 及时撤销不需要的授权
- 定期检查授权情况

---

**现在小伙伴们懂了吧？** 授权要谨慎使用！🔑

---

**最后更新**：2026年1月10日  
**制作人**：bruceCao
