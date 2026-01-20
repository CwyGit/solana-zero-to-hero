# 📘 Anchor SPL代币 - 学习笔记

> 🎯 **一句话总结**：使用anchor_spl库简化Token操作，掌握Mint、Transfer、Burn等CPI调用。

---

## 📋 关键操作速查

| 操作 | 函数 | 用途 |
|------|------|------|
| `mint_to` | 铸造代币 | 增加供应量 |
| `transfer` | 转移代币 | 账户间转账 |
| `burn` | 销毁代币 | 减少供应量 |
| `approve` | 授权委托 | 允许他人操作 |
| `freeze` | 冻结账户 | 禁止转账 |

---

## 💻 核心代码模板

```rust
use anchor_spl::token::{self, Mint, Token, TokenAccount, MintTo, Transfer};

#[derive(Accounts)]
pub struct MintTokens<'info> {
    #[account(mut)]
    pub mint: Account<'info, Mint>,
    #[account(mut)]
    pub to: Account<'info, TokenAccount>,
    pub authority: Signer<'info>,
    pub token_program: Program<'info, Token>,
}

pub fn mint_tokens(ctx: Context<MintTokens>, amount: u64) -> Result<()> {
    let cpi_ctx = CpiContext::new(
        ctx.accounts.token_program.to_account_info(),
        MintTo {
            mint: ctx.accounts.mint.to_account_info(),
            to: ctx.accounts.to.to_account_info(),
            authority: ctx.accounts.authority.to_account_info(),
        },
    );
    token::mint_to(cpi_ctx, amount)?;
    Ok(())
}
```

---

## 📝 学习检查清单

- [ ] 会用anchor_spl进行Token的CPI操作
- [ ] 理解mint_to需要mint_authority
- [ ] 知道如何创建ATA(关联代币账户)
- [ ] 能区分Token Program和Token-2022

---

**学习建议**：实现一个完整的代币空投程序！
