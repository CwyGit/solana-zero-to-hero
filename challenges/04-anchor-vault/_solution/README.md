# 🎯 Anchor金库挑战 - 参考答案

嘿，小伙伴！👋

这是 **Anchor Vault（金库）** 挑战的完整参考答案！

**比喻说明**：金库就像银行的保险箱，用户可以存入和取出代币，程序负责安全管理！

---

## 📋 题目回顾

**目标**：使用 Anchor 框架实现一个安全的代币金库系统

**核心功能**：
1. **存款（Deposit）**：用户存入 SOL 到金库
2. **取款（Withdraw）**：用户从金库取回 SOL

---

## 🧠 解题思路

### 第一步：分析需求

**疑问**：金库需要存储什么？

1. **金库 PDA**：存放 SOL 的账户（无私钥，程序控制）
2. **用户关联**：每个用户有自己的金库

**比喻**：就像银行，每个客户有自己的保险箱编号！

---

### 第二步：设计架构

```
用户钱包 ←→ 金库程序 ←→ 金库PDA账户
                ↓
           安全验证
```

**PDA 种子设计**：
- `seeds = [b"vault", user.key().as_ref()]`
- 每个用户有唯一的金库地址

---

### 第三步：指令设计

| 指令 | 功能 | 关键点 |
|------|------|--------|
| `deposit` | 存入SOL | 系统转账到PDA |
| `withdraw` | 取出SOL | PDA签名转账 |

---

## 💻 完整代码

### 1. 程序入口 `lib.rs`

```rust
use anchor_lang::prelude::*;

declare_id!("Your_Program_ID_Here");

#[program]
pub mod anchor_vault {
    use super::*;

    pub fn deposit(ctx: Context<VaultAction>, amount: u64) -> Result<()> {
        // 系统转账：用户 → 金库
        let cpi_context = CpiContext::new(
            ctx.accounts.system_program.to_account_info(),
            anchor_lang::system_program::Transfer {
                from: ctx.accounts.signer.to_account_info(),
                to: ctx.accounts.vault.to_account_info(),
            },
        );
        anchor_lang::system_program::transfer(cpi_context, amount)?;
        
        msg!("存款成功！金额: {} lamports", amount);
        Ok(())
    }

    pub fn withdraw(ctx: Context<VaultAction>, amount: u64) -> Result<()> {
        // PDA签名转账：金库 → 用户
        let signer_seeds: &[&[&[u8]]] = &[&[
            b"vault",
            ctx.accounts.signer.key.as_ref(),
            &[ctx.bumps.vault],
        ]];

        let cpi_context = CpiContext::new_with_signer(
            ctx.accounts.system_program.to_account_info(),
            anchor_lang::system_program::Transfer {
                from: ctx.accounts.vault.to_account_info(),
                to: ctx.accounts.signer.to_account_info(),
            },
            signer_seeds,
        );
        anchor_lang::system_program::transfer(cpi_context, amount)?;
        
        msg!("取款成功！金额: {} lamports", amount);
        Ok(())
    }
}
```

---

### 2. 账户结构

```rust
#[derive(Accounts)]
pub struct VaultAction<'info> {
    #[account(mut)]
    pub signer: Signer<'info>,

    #[account(
        mut,
        seeds = [b"vault", signer.key().as_ref()],
        bump,
    )]
    pub vault: SystemAccount<'info>,

    pub system_program: Program<'info, System>,
}
```

**小伙伴们要特别注意啦**：

| 字段 | 说明 | 约束 |
|------|------|------|
| `signer` | 操作者，必须是签名者 | `mut` 因为要扣款 |
| `vault` | 金库PDA | `seeds` + `bump` 验证 |
| `system_program` | 系统程序 | 用于转账 |

---

### 3. 可选：金库状态账户

如果需要记录存款历史或状态：

```rust
#[account]
#[derive(InitSpace)]
pub struct VaultState {
    pub owner: Pubkey,
    pub total_deposited: u64,
    pub bump: u8,
}
```

---

## 🔑 关键点解析

### 1. PDA签名的魔法

**疑问**：PDA没有私钥，怎么签名？

**答案**：使用 `invoke_signed` 或 Anchor 的 `CpiContext::new_with_signer`！

```rust
// 关键：提供 signer_seeds
let signer_seeds: &[&[&[u8]]] = &[&[
    b"vault",                        // 种子1
    ctx.accounts.signer.key.as_ref(), // 种子2
    &[ctx.bumps.vault],              // bump字节
]];
```

**比喻**：程序知道金库的"密码"（种子+bump），可以代表金库签名！

---

### 2. 为什么用 SystemAccount？

**选择**：
- `SystemAccount<'info>` - 只存 SOL，无自定义数据
- `Account<'info, VaultState>` - 存 SOL + 自定义数据

**本挑战用 SystemAccount 就够了**，因为只需要存取 SOL！

---

### 3. 存款 vs 取款的区别

| 操作 | 谁付款 | 谁签名 | CPI方法 |
|------|--------|--------|---------|
| 存款 | 用户 | 用户 | `CpiContext::new` |
| 取款 | 金库PDA | 程序代签 | `CpiContext::new_with_signer` |

---

## ⚠️ 常见错误

### 错误1：忘记 `mut` 约束

```rust
// ❌ 错误
pub signer: Signer<'info>,

// ✅ 正确
#[account(mut)]
pub signer: Signer<'info>,
```

**原因**：转账会改变账户余额，必须标记为可变！

---

### 错误2：PDA签名种子顺序错误

```rust
// ❌ 错误：顺序必须和 seeds 定义一致
let signer_seeds = &[&[
    ctx.accounts.signer.key.as_ref(), // 顺序错了！
    b"vault",
    &[ctx.bumps.vault],
]];

// ✅ 正确
let signer_seeds = &[&[
    b"vault",
    ctx.accounts.signer.key.as_ref(),
    &[ctx.bumps.vault],
]];
```

---

### 错误3：余额不足未检查

```rust
// ✅ 添加余额检查
require!(
    ctx.accounts.vault.lamports() >= amount,
    VaultError::InsufficientFunds
);
```

---

## ✅ 自测清单

### 编译检查
- [ ] `anchor build` 成功
- [ ] 无编译警告

### 功能检查
- [ ] 存款后金库余额增加
- [ ] 取款后用户余额增加
- [ ] 只有所有者能取款

### 安全检查
- [ ] PDA种子包含用户公钥
- [ ] 取款使用PDA签名
- [ ] 余额不足时正确报错

---

## 💡 进阶思考

### 如何支持多种代币？

使用 `anchor_spl::token` 替代系统转账：

```rust
use anchor_spl::token::{Token, TokenAccount, transfer};

#[account(
    mut,
    associated_token::mint = mint,
    associated_token::authority = vault,
)]
pub vault_ata: Account<'info, TokenAccount>,
```

---

### 如何添加取款限额？

在状态账户中记录限额：

```rust
#[account]
pub struct VaultState {
    pub daily_limit: u64,
    pub withdrawn_today: u64,
    pub last_withdrawal_day: i64,
}
```

---

## 🎯 运行测试

```bash
# 构建程序
anchor build

# 运行测试
anchor test

# 部署到 devnet
anchor deploy --provider.cluster devnet
```

---

**现在小伙伴们懂了吧？** 金库挑战的核心就是 PDA + 系统转账！🏦

---

**制作人**：bruceCao  
**最后更新**：2026年1月21日  
**难度**：⭐⭐（入门）
