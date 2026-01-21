# 🎯 Pinocchio Secp256r1金库挑战 - 参考答案

使用 **Secp256r1** 签名实现兼容 WebAuthn 的金库！

---

## 📋 背景

**为什么用 Secp256r1？**
- 兼容 WebAuthn/Passkey
- 支持硬件安全密钥
- 无需管理私钥

---

## 💻 核心代码

```rust
use solana_secp256r1_program;

pub fn verify_and_withdraw(
    accounts: &[AccountInfo],
    signature: &[u8; 64],
    message: &[u8; 32],
) -> Result<(), ProgramError> {
    // 1. 调用 Secp256r1 预编译验证签名
    let verify_ix = secp256r1_program::verify_signature(
        &public_key,
        message,
        signature,
    );
    
    invoke(&verify_ix, &[])?;
    
    // 2. 验证通过后执行取款
    // ...
    
    Ok(())
}
```

---

**制作人**：bruceCao  
**难度**：⭐⭐⭐⭐（进阶）
