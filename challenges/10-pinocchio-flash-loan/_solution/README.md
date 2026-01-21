# 🎯 Pinocchio闪电贷挑战 - 参考答案

使用 Pinocchio 实现高性能闪电贷！

---

## 📋 核心要点

与 Anchor 版本逻辑相同，重点：
1. 指令内省验证还款
2. 手动账户验证
3. 零运行时开销

---

## 💻 关键代码

```rust
// 使用 sysvar::instructions 进行内省
let instructions_sysvar = &accounts[INSTRUCTIONS_ACCOUNT_INDEX];
let current_index = get_current_instruction_index(instructions_sysvar)?;

// 验证后续指令包含还款
for i in (current_index + 1).. {
    let ix = get_instruction_at(i, instructions_sysvar)?;
    if is_repay_instruction(&ix) {
        break;
    }
}
```

---

**制作人**：bruceCao  
**难度**：⭐⭐⭐⭐（进阶）
