# 02-CPI Guard扩展 - Anchor实现 🛡️

嘿，小伙伴！👋

用Anchor实现CPI Guard扩展，类型安全又方便！

---

## 🚀 Anchor优势

**自动处理：**
- ✅ 账户验证
- ✅ 类型安全
- ✅ 错误处理

---

## 💡 实现示例

```rust
use anchor_spl::token_2022::Token2022;

#[derive(Accounts)]
pub struct EnableCpiGuard<'info> {
    #[account(mut)]
    pub token_account: Account<'info, TokenAccount>,
    pub owner: Signer<'info>,
    pub token_program: Program<'info, Token2022>,
}
```

---

**最后更新**：2026年1月9日  
**制作人**：bruceCao
