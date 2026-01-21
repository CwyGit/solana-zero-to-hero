# 🎯 Anchor托管挑战 - 参考答案

嘿，小伙伴！👋

这是 **Anchor Escrow（托管）** 挑战的完整参考答案！

**比喻说明**：托管就像淘宝的担保交易，买卖双方的资金由平台暂时保管，交易完成后再分发！

---

## 📋 题目回顾

**目标**：实现一个代币托管交换系统

**核心功能**：
1. **Make**：创建者存入代币A，设置想要的代币B数量
2. **Take**：接受者存入代币B，获取代币A
3. **Refund**：创建者取消，退回代币A

---

## 🧠 解题思路

### 交易流程图

```
Maker: 有代币A，想要代币B
  ↓ Make (锁定代币A)
Escrow PDA: 保管代币A，记录条款
  ↓ Take (Taker存入代币B)
结果: Maker得到B，Taker得到A
```

---

## 💻 完整代码

### 1. 状态定义

```rust
#[account]
#[derive(InitSpace)]
pub struct Escrow {
    pub seed: u64,
    pub maker: Pubkey,
    pub mint_a: Pubkey,
    pub mint_b: Pubkey,
    pub receive: u64,  // 期望收到的代币B数量
    pub bump: u8,
}
```

---

### 2. Make指令

```rust
#[derive(Accounts)]
#[instruction(seed: u64)]
pub struct Make<'info> {
    #[account(mut)]
    pub maker: Signer<'info>,
    
    #[account(
        init,
        payer = maker,
        space = 8 + Escrow::INIT_SPACE,
        seeds = [b"escrow", maker.key().as_ref(), seed.to_le_bytes().as_ref()],
        bump,
    )]
    pub escrow: Account<'info, Escrow>,
    
    pub mint_a: InterfaceAccount<'info, Mint>,
    pub mint_b: InterfaceAccount<'info, Mint>,
    
    #[account(mut, associated_token::mint = mint_a, associated_token::authority = maker)]
    pub maker_ata_a: InterfaceAccount<'info, TokenAccount>,
    
    #[account(init, payer = maker, associated_token::mint = mint_a, associated_token::authority = escrow)]
    pub vault: InterfaceAccount<'info, TokenAccount>,
    
    pub token_program: Interface<'info, TokenInterface>,
    pub associated_token_program: Program<'info, AssociatedToken>,
    pub system_program: Program<'info, System>,
}

pub fn make(ctx: Context<Make>, seed: u64, receive: u64, amount: u64) -> Result<()> {
    // 1. 保存托管信息
    ctx.accounts.escrow.set_inner(Escrow {
        seed,
        maker: ctx.accounts.maker.key(),
        mint_a: ctx.accounts.mint_a.key(),
        mint_b: ctx.accounts.mint_b.key(),
        receive,
        bump: ctx.bumps.escrow,
    });
    
    // 2. 转移代币A到金库
    transfer_checked(
        CpiContext::new(ctx.accounts.token_program.to_account_info(), TransferChecked {
            from: ctx.accounts.maker_ata_a.to_account_info(),
            mint: ctx.accounts.mint_a.to_account_info(),
            to: ctx.accounts.vault.to_account_info(),
            authority: ctx.accounts.maker.to_account_info(),
        }),
        amount,
        ctx.accounts.mint_a.decimals,
    )?;
    
    Ok(())
}
```

---

### 3. Take指令

```rust
pub fn take(ctx: Context<Take>) -> Result<()> {
    let escrow = &ctx.accounts.escrow;
    let signer_seeds: &[&[&[u8]]] = &[&[
        b"escrow",
        escrow.maker.as_ref(),
        &escrow.seed.to_le_bytes(),
        &[escrow.bump],
    ]];
    
    // 1. Taker转代币B给Maker
    transfer_checked(...)?;
    
    // 2. Vault转代币A给Taker（PDA签名）
    transfer_checked(
        CpiContext::new_with_signer(..., signer_seeds),
        vault_amount,
        mint_a.decimals,
    )?;
    
    // 3. 关闭金库账户
    close_account(CpiContext::new_with_signer(..., signer_seeds))?;
    
    Ok(())
}
```

---

### 4. Refund指令

```rust
pub fn refund(ctx: Context<Refund>) -> Result<()> {
    // 验证只有maker可以退款
    // 转回代币A给maker
    // 关闭escrow和vault账户
    Ok(())
}
```

---

## 🔑 关键点解析

| 知识点 | 说明 |
|--------|------|
| InterfaceAccount | 同时支持Token和Token-2022 |
| transfer_checked | 带精度验证的转账 |
| close_account | 关闭账户回收租金 |
| PDA签名 | 程序代表Escrow签名 |

---

## ⚠️ 常见错误

1. **忘记验证mint匹配** → 使用`has_one`约束
2. **PDA种子顺序错误** → 必须和定义一致
3. **未关闭账户** → 浪费租金

---

## ✅ 自测清单

- [ ] Make成功创建Escrow和Vault
- [ ] Take完成双向代币交换
- [ ] Refund只允许Maker操作
- [ ] 所有账户正确关闭

---

**制作人**：bruceCao  
**最后更新**：2026年1月21日  
**难度**：⭐⭐⭐（中等）
