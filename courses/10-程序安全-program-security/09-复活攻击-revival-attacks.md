# 09-复活攻击 - 防止僵尸账户！🧟

嘿，小伙伴！👋

**复活攻击**允许攻击者"复活"已关闭的账户，重新使用它们！

**比喻说明：** 就像已经注销的银行卡又被激活！

---

## 🎯 什么是复活攻击？

**问题：** 关闭账户后，没有正确清理discriminator。

**危险：** 攻击者可以：
- 重新使用已关闭账户
- 绕过初始化检查
- 访问旧数据

---

## ⚠️ 漏洞示例

```rust
pub fn close(ctx: Context<Close>) -> Result<()> {
    // ❌ 只转移了lamports，没清理数据
    **ctx.accounts.user.lamports.borrow_mut() += 
        ctx.accounts.data.to_account_info().lamports();
    **ctx.accounts.data.to_account_info().lamports.borrow_mut() = 0;
    Ok(())
}
```

---

## ✅ 正确做法

**方法1：清零discriminator**

```rust
// 设置discriminator为特殊值
ctx.accounts.data.discriminator = [255; 8];
```

**方法2：使用Anchor的close约束**

```rust
#[account(mut, close = user)]
pub data: Account<'info, Data>,
```

**Anchor会自动：**
- 转移lamports
- 清零discriminator

---

**现在小伙伴们懂了吧？** 正确关闭账户很重要！🧟

---

**最后更新**：2026年1月9日  
**制作人**：bruceCao