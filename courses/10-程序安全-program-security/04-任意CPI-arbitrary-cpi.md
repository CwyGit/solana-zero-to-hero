# 04-任意CPI - 防止恶意调用！☎️

嘿，小伙伴！👋

**任意CPI（Cross-Program Invocation）** 是指程序调用其他程序时，没有验证目标程序的身份！

**比喻说明：** 就像拨打陌生电话号码转账，不知道对方是银行还是骗子！

---

## 🎯 什么是CPI？

**CPI = 跨程序调用**

允许你的程序调用其他程序的功能。

**比喻：** 就像公司之间的业务合作，A公司调用B公司的服务！

---

## ⚠️ 任意CPI的危险

### 痛点：调用错误的程序

**如果不验证目标程序：**
- ❌ 可能调用恶意程序
- ❌ 资金可能被窃取
- ❌ 数据可能被篡改

**比喻：** 就像把钱转给假银行！

---

## 🔓 漏洞示例

### 易受攻击的代码

```rust
pub fn transfer_tokens(
    ctx: Context<Transfer>,
    amount: u64,
) -> Result<()> {
    // ❌ 危险！没有验证token_program是否正确
    token::transfer(
        CpiContext::new(
            ctx.accounts.token_program.to_account_info(),
            token::Transfer {
                from: ctx.accounts.from.to_account_info(),
                to: ctx.accounts.to.to_account_info(),
                authority: ctx.accounts.authority.to_account_info(),
            },
        ),
        amount,
    )?;
    Ok(())
}

#[derive(Accounts)]
pub struct Transfer<'info> {
    /// CHECK: 危险！未验证
    pub token_program: UncheckedAccount<'info>,
    // ...
}
```

**问题：** 攻击者可以传入假的token_program！

---

## 🕵️ 攻击方式

**攻击者可以：**
1. 创建一个假的"token program"
2. 这个假程序看起来像真的
3. 但实际上会窃取资金
4. 你的程序会调用这个假程序

**比喻：** 就像打电话给假银行，你以为在转账，其实在被骗！

---

## ✅ 正确做法

### 方法1：使用Program类型

```rust
#[derive(Accounts)]
pub struct Transfer<'info> {
    pub token_program: Program<'info, Token>, // ✅ 自动验证
    // ...
}
```

**好处：** Anchor自动验证program ID！

---

### 方法2：手动验证program ID

```rust
pub fn transfer_tokens(
    ctx: Context<Transfer>,
    amount: u64,
) -> Result<()> {
    // ✅ 验证program ID
    if ctx.accounts.token_program.key() != &token::ID {
        return Err(ProgramError::IncorrectProgramId.into());
    }
    
    // 现在安全了
    token::transfer(
        CpiContext::new(
            ctx.accounts.token_program.to_account_info(),
            token::Transfer {
                from: ctx.accounts.from.to_account_info(),
                to: ctx.accounts.to.to_account_info(),
                authority: ctx.accounts.authority.to_account_info(),
            },
        ),
        amount,
    )?;
    Ok(())
}
```

---

## 💡 学习建议

### CPI安全检查清单

**每次CPI调用前：**
- [ ] 验证目标program ID
- [ ] 使用Program类型（Anchor）
- [ ] 或手动检查program.key()
- [ ] 确认CPI账户也经过验证

---

## ❓ 常见问题

### Q1: 为什么不能信任输入的program？

**答：** 因为用户可以传任何账户！

就像你不能相信陌生人说"我是银行"！

---

### Q2: Program类型做了什么？

**答：** 自动验证program ID匹配预期！

类似于：
```rust
if account.key() != expected_program_id {
    return Err(...);
}
```

---

### Q3: 所有CPI都需要检查吗？

**答：** 是的！

特别是涉及：
- Token转账
- 资金操作
- 权限修改

---

## 🎯 实战建议

### 常见CPI目标

**需要验证的程序：**
- ✅ Token Program
- ✅ Token2022 Program
- ✅ Associated Token Program
- ✅ System Program
- ✅ 任何其他程序

---

**现在小伙伴们懂了吧？** 任意CPI就像给陌生人打电话转账！☎️

**记住：** 永远验证program ID！

---

**最后更新**：2026年1月9日  
**制作人**：bruceCao