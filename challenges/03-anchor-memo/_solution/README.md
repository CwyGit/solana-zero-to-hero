# 🎯 Anchor备忘录挑战 - 参考答案

嘿，小伙伴！👋

这是 **Anchor Memo（备忘录）** 挑战的完整参考答案！

**比喻说明**：备忘录就像链上的便签纸，永久记录信息！

---

## 📋 题目回顾

**目标**：实现链上备忘录功能

**核心功能**：在交易中记录任意文本信息

---

## 💻 完整代码

```rust
use anchor_lang::prelude::*;

declare_id!("Your_Program_ID");

#[program]
pub mod anchor_memo {
    use super::*;

    pub fn create_memo(ctx: Context<CreateMemo>, message: String) -> Result<()> {
        require!(message.len() <= 280, MemoError::MessageTooLong);
        
        msg!("Memo: {}", message);
        msg!("Author: {}", ctx.accounts.signer.key());
        
        Ok(())
    }
}

#[derive(Accounts)]
pub struct CreateMemo<'info> {
    pub signer: Signer<'info>,
}

#[error_code]
pub enum MemoError {
    #[msg("Message exceeds 280 characters")]
    MessageTooLong,
}
```

---

## 🔑 关键点

1. **使用`msg!`宏**：记录到交易日志
2. **长度验证**：防止过长消息
3. **无需存储**：日志永久保存在链上

---

## ✅ 自测清单

- [ ] 消息成功记录到日志
- [ ] 超长消息被拒绝
- [ ] 可在区块浏览器查看

---

**制作人**：bruceCao  
**最后更新**：2026年1月21日  
**难度**：⭐（入门）
