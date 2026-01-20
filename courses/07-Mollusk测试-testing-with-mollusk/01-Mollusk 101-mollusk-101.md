# 01-Mollusk 101 - 轻量级测试框架！🧪

嘿，小伙伴！👋

![Mollusk 101](/graphics/course-banners/testing-with-mollusk.png)

高效测试Solana程序需要一个能够平衡**速度、精确性和洞察力**的框架！

在开发复杂的程序逻辑时，你需要一个既能**快速迭代**又不牺牲测试边界情况或准确测量性能能力的环境！

**比喻说明：** 就像汽车的模拟驾驶舱，可以在安全环境中快速测试各种情况！

**小伙伴们要特别注意啦：**

> 💡 这一章内容是之前Pinocchio课程第06章测试部分的扩展版！通过本章，你将深入掌握Mollusk的完整用法！

---

## 🎯 理想的Solana测试框架

理想的Solana测试框架应具备以下三个基本功能：

### 1. 快速执行 ⚡

**目标：** 实现快速开发周期！

**比喻：** 就像F1赛车的快速进站，秒级完成！

---

### 2. 灵活的账户状态操作 🎮

**目标：** 全面测试边界情况！

**比喻：** 就像游戏的沙盒模式，可以随意调整环境！

---

### 3. 详细的性能指标 📊

**目标：** 提供优化洞察！

**比喻：** 就像健身APP，精确追踪每一个数据！

---

## 🐚 什么是Mollusk？

[Mollusk](https://github.com/anza-xyz/mollusk)是由[Anza](https://x.com/anza_xyz)团队的[Joe Caulfield](https://x.com/realbuffalojoe)创建并维护的一个轻量级Solana程序测试工具！

**核心特点：**
- 提供直接的程序执行接口
- 无需完整validator运行时的开销
- 使用低级SVM组件构建执行管道

**比喻：** 就像单元测试，只测试你关心的部分！

---

## 🚀 Mollusk的优势

### 1. 卓越的性能

**实现方式：** 排除Agave validator实现中的`AccountsDB`和`Bank`等重量级组件！

**结果：** 测试速度远超完整validator！

**比喻：** 就像轻装上阵，跑得更快！

---

### 2. 精确的账户控制

**特点：** 需要显式的账户配置

**优势：** 
- ✅ 精确控制账户状态
- ✅ 测试难以重现的场景

**比喻：** 就像摄影棚，可以完全控制光线！

---

### 3. 全面的配置选项

**支持：**
- 计算预算调整
- 功能集修改
- Sysvar自定义

**管理方式：** 通过`Mollusk`结构直接管理，并可使用内置的辅助函数进行修改！

---

## 📦 安装Mollusk

###将主Mollusk crate添加到您的项目中：

```bash
cargo add mollusk-svm --dev
```

**Note:**根据需要包含特定程序的辅助工具：

```bash
cargo add mollusk-svm-programs-memo mollusk-svm-programs-token --dev
```

**作用：** 这些额外的crate提供了预配置的辅助工具，用于标准的Solana程序！

**小伙伴们要特别注意啦：**

> 💡 `--dev`标志通过将它们添加到`[dev-dependencies]`部分中来保持程序二进制文件的轻量化！

---

## 🔧 基本设置

### 声明program_id

```rust
use mollusk_svm::Mollusk;
use solana_sdk::pubkey::Pubkey;

const ID: Pubkey = solana_sdk::pubkey!("22222222222222222222222222222222222222222222");
```

---

### 创建Mollusk实例

```rust
#[test]
fn test() {
    // Omit the `.so` file extension
    let mollusk = Mollusk::new(&ID, "target/deploy/program");
}
```

**注意：** 省略`.so`文件扩展名，Mollusk会自动添加！

---

## 🎮 四个主要API方法

在测试中，我们可以使用以下四种主要API方法之一：

### 1. process_instruction

**作用：** 处理指令并返回结果

```rust
mollusk.process_instruction(&instruction, &accounts);
```

---

### 2. process_and_validate_instruction

**作用：** 处理指令并对结果执行一系列检查

```rust
mollusk.process_and_validate_instruction(
    &instruction,
    &accounts,
    &[Check::success()],
);
```

**特点：** 如果任何检查失败则触发panic！

---

### 3. process_instruction_chain

**作用：** 处理一系列指令并返回结果

```rust
mollusk.process_instruction_chain(&[
    (&instruction1, &accounts1),
    (&instruction2, &accounts2)
]);
```

---

### 4. process_and_validate_instruction_chain

**作用：** 处理一系列指令并对每个结果执行检查

```rust
mollusk.process_and_validate_instruction_chain(&[
    (&instruction1, &accounts1, &[Check::success()]),
    (&instruction2, &accounts2, &[Check::success()]),
]);
```

---

## 📝 构建账户

### 基本账户类型

**最基本的账户类型是`SystemAccount`，它有两种主要变体：**

#### 1. 付款账户（Payer Account）

```rust
let payer = Pubkey::new_unique();
let payer_account = Account::new(100_000_000, 0, &system_program::id());
```

**用途：** 用于资助程序账户的创建或lamport转账！

---

#### 2. 未初始化账户

```rust
let default_account = Account::default();
```

**用途：** 等待在指令中初始化的程序账户！

---

### ProgramAccount

对于包含数据的`ProgramAccounts`：

```rust
let data = vec![
    // Your serialized account data
];
let lamports = mollusk
    .sysvars
    .rent
    .minimum_balance(data.len());

let program_account = Pubkey::new_unique();
let program_account_account = Account {
    lamports,
    data,
    owner: ID,
    executable: false,
    rent_epoch: 0,
};
```

---

### 账户数组

创建账户后，将它们编译成Mollusk所需的格式：

```rust
let accounts = [
    (user, user_account),
    (program_account, program_account_account)
];
```

---

## 📋 构建指令

基本的指令结构：

```rust
use solana_sdk::instruction::{Instruction, AccountMeta};

let instruction = Instruction::new_with_bytes(
    ID, // Your program's ID
    &[0], // Instruction data (discriminator + parameters)
    vec![AccountMeta::new(payer, true)], // Account metadata
);
```

---

### 指令数据

指令数据必须包括：
- 指令区分符
- 指令所需的任何参数

对于Anchor程序，默认区分符是从指令名称派生的8字节值！

---

### Anchor判别器生成

```rust
use sha2::{Sha256, Digest};

pub fn get_anchor_discriminator_from_name(name: &str) -> [u8; 8] {
    let mut hasher = Sha256::new();
    hasher.update(format!("global:{}", name));
    let result = hasher.finalize();

    [
        result[0], result[1], result[2], result[3],
        result[4], result[5], result[6], result[7],
    ]
}

let instruction_data = &[
    &get_anchor_discriminator_from_name("deposit"),
    &1_000_000u64.to_le_bytes()[..],
].concat();
```

---

### AccountMeta

根据账户权限使用适当的构造函数：

```rust
// 可变账户
AccountMeta::new(pubkey, is_signer)

// 只读账户
AccountMeta::new_readonly(pubkey, is_signer)
```

**is_signer参数：** 指示账户是否必须签署交易！

---

## ✅ 验证系统

### Check类型

```rust
mollusk.process_and_validate_instruction(
    &instruction,
    &accounts,
    &[
        Check::success(), // 验证交易成功
        Check::compute_units(5_000), // 期望特定的CU使用
        Check::account(&payer).data(&expected_data).build(), // 验证账户数据
        Check::account(&payer).owner(&ID).build(), // 验证账户owner
        Check::account(&payer).lamports(expected_lamports).build(), // 检查lamport余额
    ],
);
```

**小伙伴们要特别注意啦：**

> 💡 我们可以通过将多个检查"捆绑"在一起，对同一账户执行多个检查！

---

### 验证失败场景

```rust
mollusk.process_and_validate_instruction(
    &instruction,
    &accounts,
    &[
        Check::err(ProgramError::MissingRequiredSignature), // 期望特定错误
    ],
);
```

---

## 💡 学习建议

### 本章重点回顾

**必须掌握：**
1. ✓ Mollusk的基本概念
2. ✓ 四个主要API方法
3. ✓ 账户和指令的构建
4. ✓ 验证系统的使用

**了解即可：**
- Anchor判别器生成
- 复杂的验证场景

---

## 🎯 下一步

**学完这一章，你应该：**

- ✓ 理解Mollusk的优势
- ✓ 掌握基本用法
- ✓ 能够编写简单测试

**准备好了吗？** 下一章我们将学习**高级功能**！

---

**现在小伙伴们懂了吧？** Mollusk让测试变得又快又准！🧪

---

**最后更新**：2026年1月9日  
**制作风格**：莫式风格  
**学习建议**：先掌握基本用法，再深入高级功能！

**提示**：完整的代码示例请参考[Mollusk文档](https://github.com/anza-xyz/mollusk)！