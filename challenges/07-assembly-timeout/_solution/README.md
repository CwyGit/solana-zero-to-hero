# 🎯 Assembly超时检查挑战 - 参考答案

使用汇编实现高效的时间戳检查！

---

## 📋 用途

防止过期交易执行，例如：
- 订单超时取消
- 限时优惠验证
- DeFi交易截止

---

## 💻 核心代码

```rust
use solana_program::clock::Clock;

pub fn check_timeout(deadline: i64) -> Result<(), ProgramError> {
    let clock = Clock::get()?;
    let current_time = clock.unix_timestamp;
    
    // 高效比较
    if current_time > deadline {
        return Err(ProgramError::Custom(1)); // Timeout
    }
    
    Ok(())
}

// 汇编优化版本
pub fn check_timeout_asm(current: i64, deadline: i64) -> bool {
    unsafe {
        let result: u64;
        core::arch::asm!(
            "cmp {current}, {deadline}",
            "setl {result:l}",
            current = in(reg) current,
            deadline = in(reg) deadline,
            result = out(reg) result,
        );
        result != 0
    }
}
```

---

**制作人**：bruceCao  
**难度**：⭐⭐⭐⭐（高级）
