# 📘 Anchor开发教程 - 学习笔记

> 🎯 **一句话总结**：掌握Anchor框架的四大宏、账户系统、PDA、CPI，用"自动挡"方式高效开发Solana智能合约。

---

## 📋 知识点速查表

| 知识点 | 核心概念 | 关键记忆点 |
|--------|----------|------------|
| `declare_id!()` | 程序身份证 | 唯一的program_id |
| `#[program]` | 程序目录 | 包含所有指令入口 |
| `#[derive(Accounts)]` | 账户管家 | 自动验证账户约束 |
| `#[error_code]` | 错误翻译器 | 可读的错误信息 |
| PDA | 程序派生地址 | 无私钥，程序可签名 |
| Discriminator | 区分符 | 8字节账户类型标识 |
| CPI | 跨程序调用 | 最多4层深度 |
| LazyAccount | 惰性加载 | 只读，节省CU |

---

## 🔑 核心概念详解

### 1️⃣ Anchor四大宏

```rust
// 1. 程序ID声明
declare_id!("Your_Program_ID_Here");

// 2. 程序模块
#[program]
pub mod my_program {
    pub fn my_instruction(ctx: Context<MyContext>) -> Result<()> {
        Ok(())
    }
}

// 3. 账户验证结构
#[derive(Accounts)]
pub struct MyContext<'info> {
    #[account(mut)]
    pub signer: Signer<'info>,
}

// 4. 自定义错误
#[error_code]
pub enum MyError {
    #[msg("Invalid operation")]
    InvalidOperation,
}
```

---

### 2️⃣ 账户结构

**Solana账户基础结构**：
```rust
struct Account {
    lamports: u64,       // 余额
    data: Vec<u8>,       // 数据
    owner: Pubkey,       // 拥有者程序
    executable: bool,    // 是否可执行
}
```

**四种账户类型**：
| 类型 | 说明 | 所有者 |
|------|------|--------|
| 系统账户 | 钱包 | System Program |
| Token账户 | 代币余额 | Token Program |
| Mint账户 | 代币铸币厂 | Token Program |
| 程序账户 | 自定义数据 | 你的程序 |

---

### 3️⃣ 程序账户生命周期

**创建**：
```rust
#[account(
    init,
    payer = user,
    space = 8 + MyData::INIT_SPACE
)]
pub my_account: Account<'info, MyData>,
```

**修改大小**：
```rust
#[account(
    mut,
    realloc = new_size,
    realloc::payer = user,
    realloc::zero = true
)]
```

**关闭（回收租金）**：
```rust
#[account(
    mut,
    close = user
)]
```

---

### 4️⃣ PDA（程序派生地址）

**核心特性**：
- 确定性：相同种子→相同地址
- 无私钥：不能被外部控制
- 程序签名：创建它的程序可代签

```rust
// 定义PDA
#[account(
    seeds = [b"vault", user.key().as_ref()],
    bump
)]
pub vault: SystemAccount<'info>,

// 用PDA签名CPI
invoke_signed(
    &instruction,
    &accounts,
    &[&[b"vault", user.as_ref(), &[bump]]]
)?;
```

---

### 5️⃣ 常用账户约束

| 约束 | 语法 | 用途 |
|------|------|------|
| `mut` | `#[account(mut)]` | 标记可变 |
| `init` | `#[account(init, payer, space)]` | 创建账户 |
| `close` | `#[account(close = target)]` | 关闭账户 |
| `seeds/bump` | `#[account(seeds, bump)]` | PDA验证 |
| `has_one` | `#[account(has_one = field)]` | 关联验证 |
| `constraint` | `#[account(constraint = expr)]` | 自定义条件 |

---

### 6️⃣ Token账户操作

```rust
use anchor_spl::token::{Token, TokenAccount, Mint};

#[derive(Accounts)]
pub struct TokenTransfer<'info> {
    #[account(mut)]
    pub from: Account<'info, TokenAccount>,
    #[account(mut)]
    pub to: Account<'info, TokenAccount>,
    pub authority: Signer<'info>,
    pub token_program: Program<'info, Token>,
}
```

**InterfaceAccount**：同时支持Token和Token-2022！

---

### 7️⃣ CPI（跨程序调用）

```rust
// 构建CPI上下文
let cpi_ctx = CpiContext::new(
    token_program.to_account_info(),
    Transfer {
        from: from_account,
        to: to_account,
        authority: authority,
    }
);

// 执行CPI
token::transfer(cpi_ctx, amount)?;
```

**约束**：最多4层（A→B→C→D）

---

## 💻 关键代码模板

### 完整程序结构
```rust
use anchor_lang::prelude::*;

declare_id!("Your_ID");

#[program]
pub mod my_program {
    use super::*;
    
    pub fn initialize(ctx: Context<Initialize>) -> Result<()> {
        ctx.accounts.data.value = 0;
        Ok(())
    }
}

#[derive(Accounts)]
pub struct Initialize<'info> {
    #[account(init, payer = user, space = 8 + 8)]
    pub data: Account<'info, MyData>,
    #[account(mut)]
    pub user: Signer<'info>,
    pub system_program: Program<'info, System>,
}

#[account]
pub struct MyData {
    pub value: u64,
}
```

---

## 📝 学习检查清单

### 基础概念
- [ ] 能说出四大宏的名称和作用
- [ ] 理解账户所有权和权限模型
- [ ] 知道如何计算账户空间大小

### 实战技能
- [ ] 能创建、修改、关闭程序账户
- [ ] 能使用PDA派生确定性地址
- [ ] 能进行Token账户的CPI操作

### 安全意识
- [ ] 理解为什么需要账户约束
- [ ] 知道`/// CHECK`注释的含义
- [ ] 了解签名者验证的重要性

---

## 🔗 延伸阅读

- [Anchor官方文档](https://www.anchor-lang.com/)
- [Anchor账户约束完整列表](https://www.anchor-lang.com/docs/account-constraints)
- [SPL Token文档](https://spl.solana.com/token)

---

**学习建议**：先跑通一个简单的Vault程序，再逐步添加复杂功能！
