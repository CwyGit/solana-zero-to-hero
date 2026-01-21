# 🎯 Assembly滑点保护挑战 - 参考答案

使用汇编实现高效的滑点检查！

---

## 📋 什么是滑点？

**滑点**：实际成交价格与预期价格的偏差

```
预期: 100 Token A → 50 Token B
实际: 100 Token A → 48 Token B (4%滑点)
```

---

## 💻 核心代码

```rust
/// 检查滑点是否在允许范围内
pub fn check_slippage_asm(
    expected: u64,
    actual: u64,
    max_slippage_bps: u16,
) -> bool {
    // 滑点计算: (expected - actual) * 10000 / expected <= max_slippage_bps
    unsafe {
        let result: u64;
        core::arch::asm!(
            "sub {diff}, {expected}, {actual}",
            "mul {tmp}, {diff}, 10000",
            "div {result}, {tmp}, {expected}",
            expected = in(reg) expected,
            actual = in(reg) actual,
            diff = out(reg) _,
            tmp = out(reg) _,
            result = out(reg) result,
        );
        result <= max_slippage_bps as u64
    }
}
```

---

**制作人**：bruceCao  
**难度**：⭐⭐⭐⭐⭐（高级）
