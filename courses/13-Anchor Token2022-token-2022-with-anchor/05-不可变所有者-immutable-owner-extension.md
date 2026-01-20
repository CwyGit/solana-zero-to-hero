# 不可变所有者-Immutable Owner - Anchor版 

嘿，小伙伴！

用Anchor实现此扩展，更简单更安全！

---

##  Anchor优势

-  自动账户验证
-  类型安全
-  简化的CPI

---

##  使用Anchor

```rust
use anchor_spl::token_2022::Token2022;

// Anchor自动处理大部分验证
#[derive(Accounts)]
pub struct MyInstruction<'info> {
    pub token_program: Program<'info, Token2022>,
    // ... 其他账户
}
```

---

**最后更新**：2026年1月9日  
**制作人**：bruceCao
