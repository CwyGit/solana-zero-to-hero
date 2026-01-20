# 08-重新初始化攻击 - 防止账户复用！♻️

嘿，小伙伴！👋

**重新初始化攻击**允许攻击者重新初始化已存在的账户，覆盖原有数据！

**比喻说明：** 就像有人把你的房子当空房重新装修！

---

## 🎯 什么是重新初始化攻击？

**问题：** 程序没有检查账户是否已初始化就进行初始化操作。

**危险：**
- 覆盖现有数据
- 更改所有权
- 窃取资源

---

## ⚠️ 漏洞示例

```rust
pub fn initialize(ctx: Context<Initialize>) -> Result<()> {
    // ❌ 没有检查是否已初始化
    ctx.accounts.data.owner = ctx.accounts.user.key();
    Ok(())
}
```

---

## ✅ 正确做法

**检查是否已初始化：**

```rust
pub fn initialize(ctx: Context<Initialize>) -> Result<()> {
    // ✅ 检查
    if ctx.accounts.data.is_initialized {
        return Err(ProgramError::AccountAlreadyInitialized.into());
    }
    
    ctx.accounts.data.owner = ctx.accounts.user.key();
    ctx.accounts.data.is_initialized = true;
    Ok(())
}
```

**Anchor自动处理：** 使用`init`约束！

---

**现在小伙伴们懂了吧？** 防止账户被重新初始化！♻️

---

**最后更新**：2026年1月9日  
**制作人**：bruceCao