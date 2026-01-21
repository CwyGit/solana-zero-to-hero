# 🎯 Pinocchio备忘录挑战 - 参考答案

使用 Pinocchio 实现极简备忘录！

---

## 💻 完整代码

```rust
use pinocchio::{
    account_info::AccountInfo,
    msg,
    program_error::ProgramError,
};

pub fn process_memo(
    accounts: &[AccountInfo],
    data: &[u8],
) -> Result<(), ProgramError> {
    let signer = &accounts[0];
    
    if !signer.is_signer() {
        return Err(ProgramError::MissingRequiredSignature);
    }
    
    // 将数据作为UTF-8字符串输出
    if let Ok(message) = std::str::from_utf8(data) {
        msg!("Memo: {}", message);
    }
    
    Ok(())
}
```

---

**制作人**：bruceCao  
**难度**：⭐⭐（入门）
