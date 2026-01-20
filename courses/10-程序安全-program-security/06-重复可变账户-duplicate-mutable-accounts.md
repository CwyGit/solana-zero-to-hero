# 06-重复可变账户 - 防止双花！💰

嘿，小伙伴！👋

**重复可变账户**漏洞允许攻击者传入同一个账户两次，绕过安全检查！

**比喻说明：** 就像一个人用同一张身份证既当买家又当卖家！

---

## 🎯 什么是重复可变账户？

**问题：** 同一个账户在指令中出现多次，且都是可变的。

**危险：** 可能导致：
- 资金被盗
- 状态不一致
- 双花攻击

---

## ⚠️ 漏洞示例

```rust
pub fn transfer(
    ctx: Context<Transfer>,
    amount: u64,
) -> Result<()> {
    // 如果from和to是同一个账户会怎样？
    ctx.accounts.from.balance -= amount;
    ctx.accounts.to.balance += amount;
    Ok(())
}
```

**攻击：** 传入相同账户作为from和to，余额不变但通过了检查！

---

## ✅ 防护方法

**检查账户是否相同：**

```rust
if ctx.accounts.from.key() == ctx.accounts.to.key() {
    return Err(ProgramError::InvalidArgument.into());
}
```

---

**现在小伙伴们懂了吧？** 防止账户重复使用！💰

---

**最后更新**：2026年1月9日  
**制作风格**：莫式风格