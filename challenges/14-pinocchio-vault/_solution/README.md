# 🎯 Pinocchio金库挑战 - 参考答案

嘿，小伙伴！👋

这是 **Pinocchio Vault（金库）** 挑战的参考答案！

**比喻说明**：和Anchor版本功能相同，但使用更底层的Pinocchio框架，追求极致性能！

---

## 📋 与Anchor版本的区别

| 方面 | Anchor | Pinocchio |
|------|--------|-----------|
| 代码量 | 少 | 多 |
| 性能 | 有开销 | 零开销 |
| 安全检查 | 自动 | 手动 |
| 学习曲线 | 友好 | 陡峭 |

---

## 💻 核心代码

### 账户验证（手动）

```rust
use pinocchio::{
    account_info::AccountInfo,
    program_error::ProgramError,
    pubkey::Pubkey,
};

pub fn process_deposit(
    accounts: &[AccountInfo],
    amount: u64,
) -> Result<(), ProgramError> {
    let [signer, vault, system_program] = accounts else {
        return Err(ProgramError::NotEnoughAccountKeys);
    };
    
    // 手动验证签名者
    if !signer.is_signer() {
        return Err(ProgramError::MissingRequiredSignature);
    }
    
    // 手动验证PDA
    let (expected_vault, bump) = Pubkey::find_program_address(
        &[b"vault", signer.key().as_ref()],
        &crate::ID,
    );
    if vault.key() != &expected_vault {
        return Err(ProgramError::InvalidSeeds);
    }
    
    // 执行转账...
    
    Ok(())
}
```

---

## 🔑 关键点

1. **手动验证所有账户**：无自动检查
2. **直接操作lamports**：更高效
3. **使用invoke_signed**：PDA签名

---

## ⚠️ 常见错误

1. **忘记验证签名者** → 未授权访问
2. **PDA验证遗漏** → 伪造账户攻击
3. **lamports操作溢出** → 使用checked_add

---

**制作人**：bruceCao  
**难度**：⭐⭐⭐（中等）
