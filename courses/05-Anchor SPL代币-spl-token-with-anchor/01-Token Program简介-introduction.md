# 01-Token Program简介 - 用Anchor玩转代币！🪙

嘿，小伙伴！👋

![SPL Token with Anchor](/graphics/course-banners/spl-token-with-anchor.png)

欢迎来到**Anchor SPL代币**课程！

**本课程目标：** 学习如何用Anchor框架操作SPL Token Program！

---

## 🎯 本章你会学到什么？

- ✅ SPL Token Program是什么
- ✅ 如何安装anchor-spl
- ✅ 用Anchor创建Mint账户
- ✅ 用Anchor创建Token账户
- ✅ 用Anchor创建Associated Token账户

---

## 💡 SPL Token Program回顾

在Solana上，所有与代币相关的操作都由两个程序处理：

1. **[SPL Token Program](https://github.com/solana-program/token)** - 原始版本
2. **[Token2022 Program](/zh-cn/courses/token-2022-program)** - 扩展版本

**作用：** 这是Solana的原生代币框架，定义了所有代币的创建、管理和转移方式！

**核心特性：** 这是一个**单一的统一程序**，处理整个网络中的所有代币操作，确保一致性和互操作性！

> ✅ 在Solana上为所有代币提供单一统一接口的决定，创造了一种可以在所有dApp（去中心化应用程序）和集成（如钱包等）中复制的简单实现！

**比喻说明：** 就像国家统一的货币管理系统，所有货币都遵循同一套规则！

---

## 📦 安装anchor-spl

### 什么是anchor-spl？

**定义：** 对于Anchor，所有与代币相关的内容都可以在`anchor-spl` crate中找到！

**如何安装：** 在初始化了一个`Anchor`工作区后，我们可以这样做：

```bash
cargo add anchor-spl
```

**就这么简单！**

---

### 前置要求

> ⚠️ **小伙伴们要特别注意啦：** 如果您不熟悉`Anchor`，我们建议您先完成我们的[Anchor入门课程](/zh-cn/courses/introduction-to-anchor)再继续！

**为什么？** 本课程假设你已经了解Anchor的基本概念！

---

## 🏗️ Anchor的魔法：宏

### 复杂性问题

**传统方式的痛点：** 初始化账户需要大量样板代码，很繁琐！

**Anchor的解决方案：** 如果您熟悉`Anchor`，您会知道它们有一组**宏**，可以帮助用户抽象掉与初始化账户相关的许多复杂性！

**应用范围：** 同样适用于`Mint`、`Token`和`Associated Token`账户！

**比喻说明：** 就像用自动洗衣机代替手洗，省事多了！

---

## 🪙 创建Mint账户

### 用Anchor轻松创建

得益于`Anchor`提供的宏，我们可以轻松创建一个`Mint`账户！

**代码示例：**

```rust
#[derive(Accounts)]
pub struct CreateMint<'info> {
    #[account(mut)]
    pub signer: Signer<'info>,
    #[account(
        init,
        payer = signer,
        mint::decimals = 6,
        mint::authority = signer.key(),
    )]
    pub mint: Account<'info, Mint>,
    pub system_program: Program<'info, System>,
    pub token_program: Program<'info, Token>,
}
```

### 代码解析

**核心宏：** `#[account(...)]`

**关键参数：**
- `init` - 初始化新账户
- `payer = signer` - 谁支付租金
- `mint::decimals = 6` - 小数位设置为6
- `mint::authority = signer.key()` - 铸造权限归签名者

**比喻说明：** 就像填表格创建银行账户，Anchor帮你把复杂的部分都简化了！

---

## 💰 创建Token账户

### 同样简单

同样适用于`Token`账户！通过宏创建`Token`账户的方式如下：

**代码示例：**

```rust
#[derive(Accounts)]
pub struct CreateToken<'info> {
    #[account(mut)]
    pub signer: Signer<'info>,
    pub mint: Account<'info, Mint>,
    #[account(
        mut,
        token::mint = mint,
        token::authority = signer,
    )]
    pub token: Account<'info, TokenAccount>,
    pub system_program: Program<'info, System>,
    pub token_program: Program<'info, Token>,
}
```

### 代码解析

**关键参数：**
- `token::mint = mint` - 指定这个Token账户持有哪种代币
- `token::authority = signer` - 谁拥有这个Token账户

**比喻说明：** 就像在银行开个账户专门存某种货币！

---

## 🔗 创建Associated Token账户

### 默认账户

同样适用于`Associated Token`账户！

**与Token账户的区别：** 创建方式与创建`Token`账户类似，唯一的区别在于**约束条件**！

**代码示例：**

```rust
#[derive(Accounts)]
pub struct CreateToken<'info> {
    #[account(mut)]
    pub signer: Signer<'info>,
    pub mint: Account<'info, Mint>,
    #[account(
        mut,
        associated_token::mint = mint,
        associated_token::authority = signer,
    )]
    pub token: Account<'info, TokenAccount>,
    pub system_program: Program<'info, System>,
    pub token_program: Program<'info, Token>,
    pub associated_token_program: Program<'info, AssociatedToken>,
}
```

### 关键区别

**注意差异：**
- `token::` → `associated_token::`
- 需要额外的`associated_token_program`

**为什么？** Associated Token账户是特殊的PDA，需要专门的程序处理！

---

## 💡 学习建议

### 本章重点回顾

**必须掌握：**
1. ✓ 如何安装anchor-spl
2. ✓ 三种账户的创建方式（Mint、Token、Associated Token）
3. ✓ 宏的基本用法

**了解即可：**
- 宏的底层实现细节

---

## 🎯 下一步

学完这一章，你应该：

- ✓ 理解Anchor如何简化SPL Token操作
- ✓ 知道如何创建三种账户
- ✓ 准备好学习具体的Token操作

**准备好了吗？** 下一章我们将学习**Mint To指令** - 如何铸造代币！

---

**是不是感觉Anchor让代币开发变简单了？** 这就是框架的威力！🪙

---

**最后更新**：2026年1月9日  
**制作人**：bruceCao  
**学习建议**：这一章是基础，务必理解三种账户的创建方式！