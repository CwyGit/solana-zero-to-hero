# Make指令 - 创建托管！🔨

嘿，小伙伴！👋

**Make指令**是托管合约的第一步，创建者锁定资金开启交易！

**比喻说明：** 就像把钱锁进保险箱，设好条件等待交易！

---

## 🎯 Make指令功能

现在我们转到`make`指令，位于`make.rs`中，将执行以下操作：

**核心功能：**
1. **初始化托管记录** - 存储所有条款
2. **创建金库** - escrow拥有的mint_a的ATA
3. **转移代币** - 使用CPI调用SPL-Token程序，将创建者的Token A转移到金库

---

## 💡 账户结构

### 需要的账户

**核心账户：**
- `maker` - 决定条款并存入mint_a的用户
- `escrow` - 持有交换条款的账户（创建者、代币铸造、数量）
- `mint_a` - maker存入的代币
- `mint_b` - maker想要交换的代币

**代币账户：**
- `maker_ata_a` - 与maker和mint_a关联的代币账户，用于将代币存入vault
- `vault` - 与escrow和mint_a关联的代币账户，存放存入的代币

**程序账户：**
- `associated_token_program` - 用于创建ATA
- `token_program` - 用于CPI转账
- `system_program` - 用于创建Escrow

---

### 完整代码

```rust
#[derive(Accounts)]
#[instruction(seed: u64)]
pub struct Make<'info> {
    #[account(mut)]
    pub maker: Signer<'info>,
    
    #[account(
        init,
        payer = maker,
        space = Escrow::INIT_SPACE + Escrow::DISCRIMINATOR.len(),
        seeds = [b"escrow", maker.key().as_ref(), seed.to_le_bytes().as_ref()],
        bump,
    )]
    pub escrow: Account<'info, Escrow>,

    /// Token Accounts
    #[account(
        mint::token_program = token_program
    )]
    pub mint_a: InterfaceAccount<'info, Mint>,
    
    #[account(
        mint::token_program = token_program
    )]
    pub mint_b: InterfaceAccount<'info, Mint>,
    
    #[account(
        mut,
        associated_token::mint = mint_a,
        associated_token::authority = maker,
        associated_token::token_program = token_program
    )]
    pub maker_ata_a: InterfaceAccount<'info, TokenAccount>,
    
    #[account(
        init,
        payer = maker,
        associated_token::mint = mint_a,
        associated_token::authority = escrow,
        associated_token::token_program = token_program
    )]
    pub vault: InterfaceAccount<'info, TokenAccount>,

    /// Programs
    pub associated_token_program: Program<'info, AssociatedToken>,
    pub token_program: Interface<'info, TokenInterface>,
    pub system_program: Program<'info, System>,
}
```

**重要提示：** 此指令仅传递一个token_program。由于take操作会转移两个代币铸造的代币，我们必须确保这两个代币铸造都由同一个程序（SPL Token或Token-2022）拥有，否则CPI将失败。

---

## 🔧 实现逻辑

### 1. 填充Escrow

使用`set_inner()`辅助工具填充Escrow数据：

```rust
impl<'info> Make<'info> {
    /// # Create the Escrow
    fn populate_escrow(&mut self, seed: u64, amount: u64, bump: u8) -> Result<()> {
        self.escrow.set_inner(Escrow {
            seed,
            maker: self.maker.key(),
            mint_a: self.mint_a.key(),
            mint_b: self.mint_b.key(),
            receive: amount,
            bump,
        });

        Ok(())
    }
```

**Anchor帮助：** `set_inner()`确保每个字段都已填充

---

### 2. 存入代币

通过`transfer` CPI存入代币：

```rust
    /// # Deposit the tokens
    fn deposit_tokens(&self, amount: u64) -> Result<()> {
        transfer_checked(
            CpiContext::new(
                self.token_program.to_account_info(),
                TransferChecked {
                    from: self.maker_ata_a.to_account_info(),
                    mint: self.mint_a.to_account_info(),
                    to: self.vault.to_account_info(),
                    authority: self.maker.to_account_info(),
                },
            ),
            amount,
            self.mint_a.decimals,
        )?;

        Ok(())
    }
}
```

**Anchor帮助：** `transfer_checked`像系统辅助工具一样封装了Token CPI

---

### 3. Handler函数

在使用辅助工具之前执行检查：

```rust
pub fn handler(ctx: Context<Make>, seed: u64, receive: u64, amount: u64) -> Result<()> {
    // Validate the amount
    require_gt!(receive, 0, EscrowError::InvalidAmount);
    require_gt!(amount, 0, EscrowError::InvalidAmount);

    // Save the Escrow Data
    ctx.accounts.populate_escrow(seed, receive, ctx.bumps.escrow)?;

    // Deposit Tokens
    ctx.accounts.deposit_tokens(amount)?;

    Ok(())
}
```

**验证检查：** 针对`amount`和`receive`参数，确保不传递零值

---

## ⚠️ 重要警告

**SPL Token-2022扩展风险：**

某些扩展功能（转账钩子、保密转账、默认账户状态）可能引入漏洞：
- 阻止转账
- 锁定资金
- 在托管逻辑、金库或CPI中导致资金被抽走

**安全建议：**
- ✅ 确保mint_a和mint_b由同一个代币程序拥有，防止CPI失败
- ✅ 使用经过充分审计的代币（例如USDC、wSOL）来自标准SPL Token程序
- ✅ 避免使用未经验证或复杂的Token-2022铸币

---

**现在小伙伴们懂了吧？** Make指令创建并锁定托管！🔨

---

**最后更新**：2026年1月10日  
**制作风格**：莫式风格  
**基于：** 原始make.mdx (148行技术文档)
