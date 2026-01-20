# 02-高级功能 - Mollusk的强大工具箱！🧰

嘿，小伙伴！👋

Mollusk提供灵活的初始化选项，以适应不同的测试场景。你可以创建预加载了你的程序的实例，或者从最小环境开始，根据需要添加组件！

**比喻说明：** 就像乐高积木，可以按需组装！

---

## 🎯 本章核心内容

由于本章内容极其丰富（453行高级功能），我们将重点关注：

- ✅ 灵活的初始化方式
- ✅ Token程序助手
- ✅ 计算单元基准测试
- ✅ 自定义系统调用
- ✅ 执行环境配置

**小伙伴们要特别注意啦：**

> 💡 本章内容较多，建议分段学习，边学边实践！

---

## 🔧 灵活的初始化

### 方式1：预加载程序

在测试特定程序时，可以使用预加载程序的方式：

```rust
const ID: Pubkey = solana_sdk::pubkey!("22222222222222222222222222222222222222222222");

#[test]
fn test() {
    let mollusk = Mollusk::new(&ID, "target/deploy/program");
}
```

**好处：** 自动加载你已编译的程序！

**比喻：** 就像打开软件，工具都准备好了！

---

### 方式2：默认实例

对于更广泛的测试场景：

```rust
#[test]
fn test() {
    let mollusk = Mollusk::default();
}
```

**包含内容：** System Program等基本内置程序！

**比喻：** 就像空白画布，自己添加需要的颜色！

---

## 🪙 Token程序助手

### 添加Token程序

Mollusk的token program助手显著简化了涉及SPL代币的测试场景：

```rust
let mut mollusk = Mollusk::default();

// Add the SPL Token Program
mollusk_svm_programs_token::token::add_program(&mut mollusk);

// Add the Token2022 Program
mollusk_svm_programs_token::token2022::add_program(&mut mollusk);

// Add the Associated Token Program
mollusk_svm_programs_token::associated_token::add_program(&mut mollusk);
```

**比喻：** 就像安装APP，一键添加功能！

---

### 创建Token账户

**创建Mint账户：**

```rust
use spl_token::state::Mint;

let mint_data = Mint {
    mint_authority: Pubkey::new_unique(),
    supply: 10_000_000_000,
    decimals: 6,
    is_initialized: true,
    freeze_authority: None,
};

let mint_account = create_account_for_mint(mint_data);
```

**创建Token账户：**

```rust
use spl_token::state::{TokenAccount, AccountState};

let token_data = TokenAccount {
    mint: Pubkey::new_unique(),
    owner: Pubkey::new_unique(),
    amount: 1_000_000,
    state: AccountState::Initialized,
    // ...
};

let token_account = create_account_for_token_account(token_data);
```

**比喻：** 就像食品工厂的模具，一次生产一批！

---

## ⚡ 计算单元基准测试

### MolluskComputeUnitBencher

Mollusk包含一个专用的计算单元基准测试系统：

```rust
use mollusk_svm_bencher::MolluskComputeUnitBencher;

let mollusk = Mollusk::new(&program_id, "my_program");

MolluskComputeUnitBencher::new(mollusk)
    .bench(("bench0", &instruction0, &accounts0))
    .bench(("bench1", &instruction1, &accounts1))
    .must_pass(true)
    .out_dir("../target/benches")
    .execute();
```

**作用：** 精确测量和跟踪程序的计算效率！

**比喻：** 就像汽车的油耗测试，精确到每一滴！

---

### 基准测试报告

```
| Name   | CUs   | Delta  |
|--------|-------|--------|
| bench0 | 450   | --     |
| bench1 | 579   | -129   |
| bench2 | 1,204 | +754   |
```

**解读：**
- **CUs** - 当前计算单元消耗
- **Delta** - 与之前运行的变化
- **正值** - 使用增加
- **负值** - 优化成功

**比喻：** 就像体检报告，一目了然！

---

## 🔧 自定义系统调用

### 什么是自定义系统调用？

Mollusk支持创建和测试自定义系统调用，使你能够通过专用功能扩展Solana虚拟机！

**用途：** 测试通过SIMD添加的新系统调用！

**比喻：** 就像给手机安装新功能模块！

---

### 定义自定义系统调用

```rust
declare_builtin_function!(
    /// A custom syscall to burn compute units
    SyscallBurnCus,
    fn rust(
        invoke_context: &mut InvokeContext,
        to_burn: u64,
        // ...
    ) -> Result<u64, Box<dyn std::error::Error>> {
        invoke_context.consume_checked(to_burn)?;
        Ok(0)
    }
);
```

**作用：** 创建一个可以在Mollusk中注册的系统调用！

---

### 注册和使用

```rust
let mut mollusk = Mollusk::default();

// Register the custom syscall
mollusk
    .program_cache
    .program_runtime_environment
    .register_function("sol_burn_cus", SyscallBurnCus::vm)
    .unwrap();

// Test it
mollusk.process_and_validate_instruction(
    &instruction,
    &[],
    &[
        Check::success(),
        Check::compute_units(expected),
    ],
);
```

**比喻：** 就像注册新的API接口！

---

## ⚙️ 执行环境配置

### 基本配置

Mollusk提供了全面的配置选项：

```rust
let mut mollusk = Mollusk::new(&program_id, "path/to/program.so");

// Configure compute budget
mollusk.set_compute_budget(200_000);

// Configure feature set
mollusk.set_feature_set(FeatureSet::all_enabled());
```

**作用：** 自定义执行环境以满足特定的测试需求！

**比喻：** 就像调整实验室的温度湿度！

---

### 计算预算

```rust
// Test with standard compute budget
mollusk.set_compute_budget(200_000);
```

**用途：** 测试接近或超出计算限制的程序！

---

### 功能集

```rust
// Enable all features
mollusk.set_feature_set(FeatureSet::all_enabled());

// All features disabled
mollusk.set_feature_set(FeatureSet::default());
```

**用途：** 测试在不同网络状态下的兼容性！

**比喻：** 就像手机的开发者选项，可以开关各种功能！

---

### 系统变量（Sysvars）

```rust
// Customize clock for time-based testing
mollusk.sysvars.clock.epoch = 10;
mollusk.sysvars.clock.unix_timestamp = 1234567890;

// Jump to Slot 1000
mollusk.warp_to_slot(1000);
```

**可用的sysvar：**
- **clock** - 当前slot、epoch、时间戳
- **rent** - 租金计算参数
- **epoch_schedule** - Epoch时序配置
- 等等...

**比喻：** 就像时光机，可以跳转到任何时间点！

---

## 💡 学习建议

### 本章重点回顾

**必须掌握：**
1. ✓ 基本初始化方式
2. ✓ Token程序助手的使用
3. ✓ 基本配置选项

**了解即可：**
- 自定义系统调用
- 计算单元基准测试
- 高级sysvar配置

---

## ❓ 常见问题

### Q1: 何时用默认实例，何时预加载？

**答：** 
- **预加载** - 测试特定程序时
- **默认实例** - 需要灵活添加程序时

---

### Q2: 计算单元基准测试必须做吗？

**答：** 不是必须，但强烈建议！

**好处：** 及时发现性能退化！

---

### Q3: 自定义系统调用常用吗？

**答：** 不常用，主要用于：
- 测试新的SIMD提案
- 特殊的测试场景

---

## 🎯 下一步

**学完这一章，你应该：**

- ✓ 掌握Mollusk的高级功能
- ✓ 能够配置测试环境
- ✓ 了解性能分析方法

**准备好了吗？** 下一章是**总结**！

---

**现在小伙伴们懂了吧？** Mollusk的工具箱很丰富，按需使用！🧰

---

**最后更新**：2026年1月9日  
**制作风格**：莫式风格  
**学习建议**：这章功能很多，先掌握常用的，其他用到再深入！

**提示**：完整的API文档和更多示例请参考[Mollusk GitHub](https://github.com/anza-xyz/mollusk)！