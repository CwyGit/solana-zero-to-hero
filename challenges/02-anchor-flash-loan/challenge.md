# Anchor闪电贷挑战 - 指令内省实战！⚡

嘿，小伙伴！👋

欢迎来到**Anchor闪电贷挑战**！这是最酷的DeFi功能之一！

**比喻说明：** 就像交易的"X光视野"，程序可以透视整个交易！

---

## 🎯 什么是指令内省？

**指令内省**是一项强大的功能，它允许区块链程序检查和分析同一交易包中的其他指令。

**核心能力：**
- 📊 查看交易中的所有指令
- 🔮 "前瞻性"看到尚未执行的指令
- 🧠 根据交易后续操作做出智能决策

---

## ⚡ 什么是闪电贷？

**闪电贷**是仅存在于单笔交易范围内的独特贷款类型！

### 工作流程

**三步走：**
1. **借款（Borrow）** - 交易开始时，即时借入大量资金
2. **使用（Use）** - 在同一交易中使用资金交易、套利
3. **还款（Repay）** - 交易结束前，归还贷款+少量费用

**比喻：** 就像瞬间借钱做生意，交易完成前必须还上，否则整个交易取消！

---

## 🔐 原子性保证

**关键机制：** 依赖区块链交易的原子性

**原子性特点：**
- ✅ 要么全部成功
- ✅ 要么全部失败回滚
- ✅ 没有中间状态

**零风险：** 
- 贷款方： 要么得到还款，要么贷款从未发生
- 借款方：可以无抵押借巨额资金

---

## 📦 项目设置

### 安装步骤

```bash
# 创建新项目
anchor init blueshift_anchor_flash_loan
cd blueshift_anchor_flash_loan

# 添加依赖
cargo add anchor-spl
```

**依赖说明：**
- `anchor-spl` - 处理SPL代币的工具

---

## 💻 项目模板

### lib.rs框架

```rust
use anchor_lang::prelude::*;
use anchor_spl::{
  token::{Token, TokenAccount, Mint, Transfer, transfer}, 
  associated_token::AssociatedToken
}; 
use anchor_lang::{
  Discriminator,
  solana_program::sysvar::instructions::{
      ID as INSTRUCTIONS_SYSVAR_ID,
      load_instruction_at_checked
  }
};

declare_id!("22222222222222222222222222222222222222222222");

#[program]
pub mod blueshift_anchor_flash_loan {
  use super::*;

  pub fn borrow(ctx: Context<Loan>, borrow_amount: u64) -> Result<()> {
    // borrow logic...
    Ok(())
  }

  pub fn repay(ctx: Context<Loan>) -> Result<()> {
    // repay logic...
    Ok(())
  }
}

#[derive(Accounts)]
pub struct Loan<'info> {
  // loan accounts...
}

#[error_code]
pub enum ProtocolError {
  // error enum..
}
```

**重要：** 程序ID必须设为`22222222222222222222222222222222222222222222`

---

## 📊 账户结构

**Loan上下文：** `borrow`和`repay`使用相同账户结构

### 账户说明

**核心账户：**
- `borrower` - 请求闪电贷的用户
- `protocol` - 拥有协议流动性池的PDA
- `mint` - 被借用的代币

**代币账户：**
- `borrower_ata` - 借款人的关联代币账户
- `protocol_ata` - 协议的关联代币账户

**系统账户：**
- `instructions` - 用于内省的指令Sysvar账户
- `token_program`、`associated_token_program`、`system_program`

---

### 完整代码

```rust
#[derive(Accounts)]
pub struct Loan<'info> {
  #[account(mut)]
  pub borrower: Signer<'info>,
  
  #[account(
    seeds = [b"protocol".as_ref()],
    bump,
  )]
  pub protocol: SystemAccount<'info>,

  pub mint: Account<'info, Mint>,
  
  #[account(
    init_if_needed,
    payer = borrower,
    associated_token::mint = mint,
    associated_token::authority = borrower,
  )]
  pub borrower_ata: Account<'info, TokenAccount>,
  
  #[account(
    mut,
    associated_token::mint = mint,
    associated_token::authority = protocol,
  )]
  pub protocol_ata: Account<'info, TokenAccount>,

  #[account(address = INSTRUCTIONS_SYSVAR_ID)]
  /// CHECK: InstructionsSysvar account
  instructions: UncheckedAccount<'info>,
  
  pub token_program: Program<'info, Token>,
  pub associated_token_program: Program<'info, AssociatedToken>,
  pub system_program: Program<'info, System>
}
```

**关键约束：**
- `protocol` - 使用seeds确保确定性地址
- `borrower_ata` - 使用`init_if_needed`自动创建
- `instructions` - 验证正确的系统账户地址

---

## ⚠️ 错误处理

闪电贷需要精确验证，全面的错误枚举：

```rust
#[error_code]
pub enum ProtocolError {
    #[msg("Invalid instruction")]
    InvalidIx,
    #[msg("Invalid instruction index")]
    InvalidInstructionIndex,
    #[msg("Invalid amount")]
    InvalidAmount,
    #[msg("Not enough funds")]
    NotEnoughFunds,
    #[msg("Program Mismatch")]
    ProgramMismatch,
    #[msg("Invalid program")]
    InvalidProgram,
    #[msg("Invalid borrower ATA")]
    InvalidBorrowerAta,
    #[msg("Invalid protocol ATA")]
    InvalidProtocolAta,
    #[msg("Missing repay instruction")]
    MissingRepayIx,
    #[msg("Missing borrow instruction")]
    MissingBorrowIx,
    #[msg("Overflow")]
    Overflow,
}
```

---

## 💡 学习建议

### 前置知识

📖 **强烈建议：** 先学习[指令内省课程](/zh-CN/courses/instruction-introspection)

### 实现步骤

1. **理解账户结构** - Loan上下文
2. **实现borrow指令** - 借款逻辑
3. **实现repay指令** - 还款逻辑  
4. **添加内省验证** - 检查交易完整性
5. **测试验证** - 确保原子性

---

## 🤔 思考题

**程序还无法完全编译。您能找出原因吗？**

**提示：** 检查导入、账户约束和错误处理！

---

**准备好了吗？** 实现你的闪电贷协议！⚡

**提示：** 查看pages目录了解borrow和repay的详细实现！

**难度评级：** ⭐⭐⭐⭐⭐ (高级)  
**预计时间：** 6-8小时  
**先修知识：** Anchor进阶、指令内省

---

**最后更新**：2026年1月10日  
**制作风格**：莫式风格  
**基于：** 原始challenge.mdx (184行技术文档)
