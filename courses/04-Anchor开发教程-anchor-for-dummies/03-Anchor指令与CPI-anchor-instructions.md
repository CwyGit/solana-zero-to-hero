# Anchor 指令与 CPI - 程序的"功能菜单" 📋

嘿，小伙伴！👋

学完了账户，现在我们要学习 Anchor 程序的核心 - **指令（Instructions）**！

## 💭 指令是什么？为什么这么重要？

你可能在想："指令？听起来好正式..."

**别怕！** 我晕，这不是等于没说吗？好吧，给大家打个比方。

**比喻说明：** 

指令就像餐厅的**菜单**：
- 你点"宫保鸡丁"（调用 `deposit` 指令）→ 厨房就给你做宫保鸡丁
- 你点"麻婆豆腐"（调用 `withdraw` 指令）→ 厨房就给你做麻婆豆腐

在 Solana 中：
- **程序** = 餐厅（提供服务的地方）
- **指令** = 菜单上的菜品（可以执行的操作）
- **调用指令** = 点菜（告诉程序你要做什么）

**核心概念：** 指令是 Solana 程序的**构建模块**，定义了可以执行的操作。

---

## 🎯 本章你会学到什么？

学完这一章，你将掌握：

- ✅ **如何定义指令** - 编写程序的"菜单"
- ✅ **Context 的秘密** - 指令的"食材清单"
- ✅ **三种组织方式** - 简单到复杂的代码结构
- ✅ **指令参数** - 传递数据给指令
- ✅ **CPI（跨程序调用）** - 让程序互相合作
- ✅ **错误处理** - 优雅地处理问题

---

## 📝 指令的基本结构

### 最简单的指令

在 Anchor 中，指令是使用 `#[program]` 模块和单个指令函数定义的：

```rust
use anchor_lang::prelude::*;

#[program]
pub mod my_program {
    use super::*;

    pub fn initialize(ctx: Context<Initialize>, data: u64) -> Result<()> {
        // 指令逻辑写在这里
        Ok(())
    }
}
```

**分析：**
- `#[program]` - 告诉 Anchor "这是程序模块"
- `pub fn initialize` - 这是一个公开的指令函数，叫 `initialize`
- `ctx: Context<Initialize>` - 第一个参数，包含账户等信息
- `data: u64` - 第二个参数，这个指令需要的数据
- `Result<()>` - 返回结果（成功或失败）

**现在小伙伴们懂了吧？** 写指令就像定义一个函数，第一个参数总是 `Context`！

---

## 🔍 Context：指令的"万能钥匙"

### Context 里有什么？

每个指令函数都会接收一个 `Context` 结构体作为其第一个参数。

**疑问：** Context 到底包含什么？

好问题！Context 包含了指令需要的**所有资源**：

```rust
Context {
    accounts,           // 传递给指令的账户
    program_id,         // 程序的公钥
    remaining_accounts, // 额外的账户（动态的）
    bumps,             // PDA 的 bump seeds
}
```

**比喻说明：** 就像厨师做菜需要的"工具箱"：
- `accounts` = 食材（番茄、鸡蛋）
- `program_id` = 厨师的工牌（证明身份）
- `remaining_accounts` = 额外的调料（可选）
- `bumps` = 秘密配方参数

### 如何使用 Context？

访问 Context 中的内容非常简单：

```rust
pub fn some_instruction(ctx: Context<SomeContext>) -> Result<()> {
    // 访问账户
    let account_1 = &ctx.accounts.account_1;
    let account_2 = &ctx.accounts.account_2;
    
    // 访问程序 ID
    let program = ctx.program_id;
    
    // 访问剩余账户
    for remaining_account in ctx.remaining_accounts {
        // 处理额外的账户
    }
    
    // 访问 PDA 的 bump
    let bump = ctx.bumps.pda_account;
    
    Ok(())
}
```

**大家先记住这个模式：** 通过 `ctx.accounts.xxx` 访问账户，后面我们会经常用到！

---

## 🏷️ 指令鉴别器（Discriminator）

### 什么是鉴别器？

与账户类似，Anchor 中的指令也使用**鉴别器**来标识不同的指令类型。

**默认鉴别器：** `sha256("global:<instruction_name>")[0..8]`（8字节前缀）

**注意：** 指令名称应为 **snake_case** 格式（小写+下划线）。

**举例：**
```rust
pub fn deposit_tokens(ctx: Context<Deposit>) -> Result<()> {
    // 鉴别器会是 sha256("global:deposit_tokens") 的前8字节
    Ok(())
}
```

### 自定义鉴别器

你也可以为指令指定自定义鉴别器：

```rust
#[instruction(discriminator = 1)]
pub fn custom_discriminator(ctx: Context<Custom>) -> Result<()> {
    // 使用自定义的鉴别器
    Ok(())
}
```

**小伙伴们要特别注意啦：** 就像账户鉴别器一样，指令鉴别符也必须唯一！

---

## 📐 三种指令组织方式：从简单到专业

### 疑问：代码应该怎么组织？

你可以以不同的方式编写指令，根据程序的复杂性和您的编码风格选择合适的方式。

### 方式1：内联指令逻辑（适合简单程序）

**适用场景：** 指令逻辑很简单，只有几行代码

```rust
pub fn initialize(ctx: Context<Transfer>, amount: u64) -> Result<()> {
    // 转账代币的逻辑
    ctx.accounts.from.amount -= amount;
    ctx.accounts.to.amount += amount;

    // 关闭代币账户的逻辑
    ctx.accounts.from.close()?;
    
    Ok(())
}
```

**优点：** 简单直接，一目了然  
**缺点：** 代码多了就乱，不好维护

**比喻说明：** 就像在客厅做饭，东西少还行，东西多了就乱套了！

---

### 方式2：独立模块实现（适合复杂程序）

**适用场景：** 程序很复杂，需要分文件组织

```rust
// 在独立文件: transfer.rs
pub fn execute(ctx: Context<Transfer>, amount: u64) -> Result<()> {
    // 转账代币的逻辑
    transfer_tokens(&ctx, amount)?;

    // 关闭代币账户的逻辑
    close_token_account(&ctx)?;
 
    Ok(())
}

// 在 lib.rs 中
pub fn transfer(ctx: Context<Transfer>, amount: u64) -> Result<()> {
    transfer::execute(ctx, amount)  // 调用独立模块的函数
}
```

**优点：** 代码组织清晰，易于维护  
**缺点：** 需要在多个文件间切换

**比喻说明：** 就像有独立的厨房，做饭更专业！

---

### 方式3：独立上下文实现（推荐！）

**适用场景：** 复杂指令，需要多个辅助函数

这是**最专业**的方式！把逻辑移到上下文结构的实现中：

```rust
// 在独立文件: transfer.rs
pub fn execute(ctx: Context<Transfer>, amount: u64) -> Result<()> {
    ctx.accounts.transfer_tokens(amount)?;  // 调用 Context 的方法
    ctx.accounts.close_token_account()?;    // 调用 Context 的方法
 
    Ok(())
}

impl<'info> Transfer<'info> {
    /// 转账代币
    pub fn transfer_tokens(&mut self, amount: u64) -> Result<()> {
        // 转账代币的具体逻辑
        self.from.amount -= amount;
        self.to.amount += amount;

        Ok(())
    }

    /// 关闭代币账户
    pub fn close_token_account(&mut self) -> Result<()> {
        // 关闭账户的具体逻辑
        self.from.close()?;
        
        Ok(())
    }
}

// 在 lib.rs 中
pub fn transfer(ctx: Context<Transfer>, amount: u64) -> Result<()> {
    transfer::execute(ctx, amount)
}
```

**优点：**
- ✅ 代码组织非常清晰
- ✅ 每个函数职责单一
- ✅ 容易测试
- ✅ 可重用性高
- ✅ 便于添加文档

**缺点：** 需要写更多代码（但值得！）

**比喻说明：** 就像米其林餐厅，每道工序都有专门的人负责！

**现在小伙伴们懂了吧？** 方式3虽然代码多一点，但专业程序都这么写！

---

## 📦 指令参数：传递数据给指令

### 基本类型参数

指令可以接受上下文之外的参数。Anchor 会**自动序列化和反序列化**它们！

**支持的基本类型：**

```rust
pub fn complex_instruction(
    ctx: Context<Complex>,
    amount: u64,           // 数字
    pubkey: Pubkey,        // 公钥
    vec_data: Vec<u8>,     // 字节数组
) -> Result<()> {
    // 使用这些参数
    Ok(())
}
```

**分析：** Anchor 支持所有 Rust 的基本类型和常见的 Solana 类型！

---

### 自定义类型参数

你也可以传递自定义类型，但它们必须实现 `AnchorSerialize` 和 `AnchorDeserialize`：

```rust
#[derive(AnchorSerialize, AnchorDeserialize)]
pub struct InstructionData {
    pub field1: u64,
    pub field2: String,
}

pub fn custom_type_instruction(
    ctx: Context<Custom>,
    data: InstructionData,  // 自定义类型
) -> Result<()> {
    // 使用 data.field1 和 data.field2
    Ok(())
}
```

**比喻说明：** 就像点外卖可以备注"少辣、多醋"，指令也可以接受额外的配置！

---

## 💡 最佳实践：写出专业的指令

### 1. 保持指令专注

**原则：** 每个指令应专注于做好**一件事**。

**❌ 不好的例子：**
```rust
pub fn do_everything(ctx: Context<DoEverything>) -> Result<()> {
    // 创建账户
    // 转账
    // 销毁代币
    // 关闭账户
    // ... 做了太多事情！
    Ok(())
}
```

**✅ 好的例子：**
```rust
pub fn deposit(ctx: Context<Deposit>, amount: u64) -> Result<()> {
    // 只负责存款
    Ok(())
}

pub fn withdraw(ctx: Context<Withdraw>, amount: u64) -> Result<()> {
    // 只负责取款
    Ok(())
}
```

**比喻说明：** 就像餐厅的菜单，"宫保鸡丁"就是"宫保鸡丁"，不会又是"宫保鸡丁"又是"麻婆豆腐"！

---

### 2. 使用上下文实现

**为什么？** 对于复杂的指令，使用 Context 实现方法可以：
- 保持代码组织有序
- 更容易测试
- 提高可重用性
- 便于添加文档

---

### 3. 完善的错误处理

**重要性：** 始终使用适当的错误处理并返回**有意义的错误信息**！

```rust
#[error_code]
pub enum TransferError {
    #[msg("Insufficient balance")]
    InsufficientBalance,
    #[msg("Invalid amount")]
    InvalidAmount,
}

impl<'info> Transfer<'info> {
    pub fn transfer_tokens(&mut self, amount: u64) -> Result<()> {
        require!(amount > 0, TransferError::InvalidAmount);
        require!(
            self.source.amount >= amount,
            TransferError::InsufficientBalance
        );

        // 转账逻辑
        Ok(())
    }
}
```

**疑问：** 为什么要自定义错误？

好问题！对比一下：
- ❌ 原生错误: `Error: Program failed with error code 6000`
- ✅ 自定义错误: `Error: Insufficient balance`

**现在小伙伴们懂了吧？** 第二个错误一看就知道是余额不足！

---

### 4. 写好文档

**重要性：** 始终为您的指令逻辑编写文档！

```rust
impl<'info> Transfer<'info> {
    /// # 转账代币
    /// 
    /// 从源账户转账到目标账户
    /// 
    /// # 参数
    /// - `amount`: 转账数量，必须大于0
    /// 
    /// # 错误
    /// - `InvalidAmount`: 金额无效（<=0）
    /// - `InsufficientBalance`: 余额不足
    pub fn transfer_tokens(&mut self, amount: u64) -> Result<()> {
        // 实现
        Ok(())
    }
}
```

**小伙伴们要特别注意啦：** 好的文档能帮未来的你（和你的队友）理解代码！

---

## 🔗 CPI (跨程序调用) - 让程序互相合作

### 什么是 CPI？

**CPI (Cross-Program Invocation)** 是指一个程序调用另一个程序的指令的过程。

**比喻说明：** 

想象一个商业街：
- 你的餐厅（你的程序）需要食材
- 你给供货商打电话（CPI）
- 供货商送来食材（另一个程序执行指令）

在 Solana 中：
- **你的程序** 调用 **Token Program** 转账代币
- **你的程序** 调用 **System Program** 创建账户

**核心概念：** CPI 使得 Solana 程序具有**可组合性** - 程序之间可以互相调用！

---

### 如何使用 CPI？

Anchor 提供了一种通过 `CpiContext` 进行 CPI 的便捷方式。

**小伙伴们要特别注意啦：** 

- 系统程序的 CPI：`use anchor_lang::system_program::*`
- SPL 代币程序的 CPI：`use anchor_spl::token::*`

---

### 基本 CPI 示例：转账 Lamports

```rust
use anchor_lang::system_program::{transfer, Transfer};

pub fn transfer_lamport(ctx: Context<TransferLamport>, amount: u64) -> Result<()> {
    // 1. 准备 CPI 需要的账户
    let cpi_accounts = Transfer {
        from: ctx.accounts.from.to_account_info(),
        to: ctx.accounts.to.to_account_info(),
    };
    
    // 2. 准备 CPI 程序
    let cpi_program = ctx.accounts.system_program.to_account_info();
    
    // 3. 创建 CPI Context
    let cpi_ctx = CpiContext::new(cpi_program, cpi_accounts);
    
    // 4. 调用 CPI
    transfer(cpi_ctx, amount)?;
    
    Ok(())
}
```

**分析：** 在这个例子中，我们调用系统程序的转账指令来转移 lamports。

**步骤拆解：**
1. 准备账户（转出账户 + 转入账户）
2. 准备程序（系统程序）
3. 创建 CPI Context
4. 执行调用

**比喻说明：** 就像打电话，先查号码（准备账户），拨号（创建Context），说话（执行调用）！

---

### PDA 签名的 CPI

当需要 PDA 签名时，使用 `CpiContext::new_with_signer`：

```rust
use anchor_lang::system_program::{transfer, Transfer};

pub fn transfer_lamport_with_pda(
    ctx: Context<TransferLamportWithPda>, 
    amount: u64
) -> Result<()> {
    // 准备 PDA 的签名种子
    let seeds = &[
        b"vault".as_ref(),
        &[ctx.bumps.vault],  // bump seed
    ];
    let signer = &[&seeds[..]];
    
    // 准备 CPI 账户
    let cpi_accounts = Transfer {
        from: ctx.accounts.vault.to_account_info(),  // PDA 账户
        to: ctx.accounts.recipient.to_account_info(),
    };
    
    // 准备 CPI 程序
    let cpi_program = ctx.accounts.system_program.to_account_info();
    
    // 创建带签名者的 CPI Context
    let cpi_ctx = CpiContext::new_with_signer(cpi_program, cpi_accounts, signer);
    
    // 执行 CPI
    transfer(cpi_ctx, amount)?;
    
    Ok(())
}
```

**关键区别：** 使用 `new_with_signer` 并传入 `signer`！

**为什么需要？** 因为 PDA 没有私钥，需要程序代表它签名！

**比喻说明：** 就像自动售货机（PDA）没有钥匙，但售货机的管理程序（你的程序）可以控制它！

---

## ❗ 错误处理：优雅地处理问题

### 自定义错误系统

Anchor 为指令提供了一个强大的错误处理系统：

```rust
#[error_code]
pub enum MyError {
    #[msg("Custom error message")]
    CustomError,
    
    #[msg("Another error with value: {0}")]
    ValueError(u64),
}

pub fn handle_errors(ctx: Context<HandleErrors>, value: u64) -> Result<()> {
    // 检查条件，不满足就返回错误
    require!(value > 0, MyError::CustomError);
    require!(value < 100, MyError::ValueError(value));
    
    Ok(())
}
```

**分析：**
- `#[error_code]` - 定义错误枚举
- `#[msg("...")]` - 错误描述信息
- `require!(条件, 错误)` - 检查条件，不满足就报错

**比喻说明：** 就像餐厅的服务员，遇到问题（超过营业时间、菜卖完了）会礼貌地告诉你具体原因！

---

## 💡 学习建议

### 本章重点回顾

**必须掌握：**
1. ✓ 指令的基本结构（Context + 参数 + 返回值）
2. ✓ Context 包含什么（accounts, program_id, remaining_accounts, bumps）
3. ✓ 三种组织方式（内联/模块/Context实现）
4. ✓ 基本的 CPI 用法
5. ✓ 错误处理

**推荐使用：**
- ✓ Context 实现方式（专业！）
- ✓ 自定义错误（清晰！）
- ✓ 完善的文档（友好！）

### 学习方法建议

**循序渐进：**
1. **第一遍**：理解指令的基本概念（什么是指令，怎么定义）
2. **第二遍**：学会写简单的指令（内联方式）
3. **第三遍**：进阶到 Context 实现方式
4. **第四遍**：掌握 CPI

**不要试图一次记住所有细节！**

很多小伙伴学习的时候，喜欢在第一遍就对每一个细节都搞清楚，事实上这是效率最低的学习方法。

---

## 🎯 下一步

学完这一章，你应该：

- ✓ 能定义基本的指令
- ✓ 理解 Context 的作用
- ✓ 知道如何组织指令代码
- ✓ 能进行简单的 CPI
- ✓ 会使用自定义错误

**准备好了吗？** 下一章我们将学习如何**测试你的 Anchor 程序**！

---

**是不是对指令有了全新的理解呢？** 指令就是程序的"菜单"，掌握了指令，你就能让程序"做事"了！💪

---

**最后更新**：2026年1月9日  
**制作风格**：莫式风格  
**学习建议**：本章是 Anchor 开发的核心，建议多练习写指令！