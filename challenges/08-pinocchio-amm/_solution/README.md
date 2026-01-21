# 🎯 Pinocchio AMM挑战 - 参考答案

嘿，小伙伴！👋

这是最具挑战性的 **AMM（自动做市商）** 实现！

**比喻说明**：AMM就像自动售货机，按照数学公式自动定价和交易！

---

## 📋 题目回顾

**目标**：实现恒定乘积做市商（x * y = k）

**核心功能**：
1. **Initialize**：创建流动性池
2. **Deposit**：添加流动性
3. **Withdraw**：移除流动性
4. **Swap**：代币交换

---

## 🧠 核心公式

### 恒定乘积公式

```
x * y = k
```

- `x`：代币A的储备量
- `y`：代币B的储备量
- `k`：常数（每次交易后保持不变）

### Swap计算

```rust
// 用户想用 dx 个代币A 换 代币B
// dy = y - k / (x + dx)
// dy = y * dx / (x + dx)  // 简化后
```

---

## 💻 核心代码

### Pool状态

```rust
pub struct Pool {
    pub token_a_mint: Pubkey,
    pub token_b_mint: Pubkey,
    pub token_a_reserve: u64,
    pub token_b_reserve: u64,
    pub lp_supply: u64,
    pub fee_bps: u16,  // 手续费（基点）
    pub bump: u8,
}
```

### Swap逻辑

```rust
pub fn swap(
    pool: &mut Pool,
    amount_in: u64,
    is_a_to_b: bool,
) -> Result<u64, ProgramError> {
    let (reserve_in, reserve_out) = if is_a_to_b {
        (pool.token_a_reserve, pool.token_b_reserve)
    } else {
        (pool.token_b_reserve, pool.token_a_reserve)
    };
    
    // 计算手续费
    let fee = amount_in * pool.fee_bps as u64 / 10000;
    let amount_in_after_fee = amount_in - fee;
    
    // 恒定乘积公式
    let amount_out = reserve_out * amount_in_after_fee / (reserve_in + amount_in_after_fee);
    
    // 更新储备
    if is_a_to_b {
        pool.token_a_reserve += amount_in;
        pool.token_b_reserve -= amount_out;
    } else {
        pool.token_b_reserve += amount_in;
        pool.token_a_reserve -= amount_out;
    }
    
    Ok(amount_out)
}
```

---

## 🔑 关键点

| 知识点 | 说明 |
|--------|------|
| 恒定乘积 | x*y=k 保持不变 |
| 滑点 | 大额交易价格偏移 |
| LP Token | 流动性证明代币 |
| 手续费 | 激励流动性提供者 |

---

## ⚠️ 常见错误

1. **精度丢失** → 使用u128中间计算
2. **滑点保护缺失** → 添加min_amount_out
3. **重入攻击** → 先更新状态再转账

---

**制作人**：bruceCao  
**难度**：⭐⭐⭐⭐⭐（高级）
