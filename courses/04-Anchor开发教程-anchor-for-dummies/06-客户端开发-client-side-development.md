# 客户端开发 - 让用户和你的程序"对话" 💻

嘿，小伙伴！👋

程序部署到链上了，但用户怎么使用呢？这就需要**客户端**！

## 💭 什么是客户端？为什么需要它？

**比喻说明：**

想象一个银行：
- **后端（程序）** = 银行的金库系统
- **前端（客户端）** = ATM机、手机银行APP

用户不会直接操作金库系统，而是通过 ATM 或 APP 与银行交互。

在 Solana 中：
- **程序** = 链上的智能合约（后端）
- **客户端** = TypeScript/JavaScript 代码（前端）
- **用户** = 通过钱包和客户端与程序交互

---

## 🎯 本章你会学到什么？

- ✅ 如何用 TypeScript 与 Anchor 程序交互
- ✅ IDL 文件的作用
- ✅ 如何调用程序指令
- ✅ 如何获取链上数据
- ✅ 如何监听链上事件

---

## 📋 IDL - 程序的"使用说明书"

### 什么是 IDL？

**IDL (Interface Description Language)** = 接口描述语言

Anchor 通过一个与您的程序结构相匹配的 IDL 文件简化了与 Sol ana 程序的客户端交互。

**比喻说明：** IDL 就像电器的说明书：
- 告诉你有哪些功能（指令）
- 每个功能需要什么参数（输入）
- 会产生什么结果（输出）

结合 Anchor 的 TypeScript 库（`@coral-xyz/anchor`），IDL 提供了一种简化的方式来构建指令和交易。

---

### 自动生成的文件

当你运行 `anchor build` 后，Anchor 会自动生成：

- 📄 `target/idl/<program-name>.json` - IDL 文件
- 📄 `target/types/<program-name>.ts` - TypeScript SDK

**这两个文件让客户端开发变得超级简单！**

---

### 项目结构

将这些文件传输到您的 TypeScript 客户端项目：

```
src
├── anchor
│     ├── <program-name>.json    # IDL 文件
│     └── <program-name>.ts      # TypeScript 类型
└── integration.ts                # 程序交互逻辑
```

**现在小伙伴们懂了吧？** `integration.ts` 文件就是你写客户端代码的地方！

---

## 🔌 设置 Provider - 连接到区块链

### 什么是 Provider？

**Provider** 结合了两个东西：
1. **Connection** - 连接到哪个网络（localhost/devnet/mainnet）
2. **Wallet** - 用哪个钱包支付和签署交易

**比喻说明：** 就像手机连WiFi+登录账号，Provider = WiFi连接 + 用户账号

---

### 设置代码

```ts
import { useConnection, useAnchorWallet } from '@solana/wallet-adapter-react';
import { AnchorProvider, setProvider } from '@coral-xyz/anchor';

// 获取连接和钱包
const { connection } = useConnection();
const wallet = useAnchorWallet();

// 创建 Provider
const provider = new AnchorProvider(connection, wallet, {
  commitment: "confirmed",
});

// 设置为默认
setProvider(provider);
```

**小伙伴们要特别注意啦：**

> 来自 `useWallet` 的 `@solana/wallet-adapter-react` hook 与 Anchor 的 Provider 所期望的钱包对象**不兼容**。这就是我们使用 `useAnchorWallet` hook 的原因！

---

## 🎮 Program 对象 - 你的"遥控器"

### 什么是 Program 对象？

Anchor 的 **Program 对象**为与 Solana 程序交互创建了一个自定义 API。

**比喻说明：** 就像电视遥控器，每个按钮对应一个功能！

### Program 对象能做什么？

- 📤 发送交易
- 📥 获取反序列化的账户
- 🔍 解码指令数据
- 📡 订阅账户更改
- 📻 监听事件

---

### 创建 Program 对象

```ts
import { Program } from '@coral-xyz/anchor';
import IDL from './anchor/<program-name>.json';
import type { <ProgramType> } from './anchor/<program-name>';

// 创建 Program 对象
const program = new Program(<program-name> as <ProgramType>);
```

**如果您尚未设置默认提供者**，请显式指定：

```ts
const program = new Program(<program-name> as <ProgramType>, provider);
```

---

## 📞 调用程序指令 - MethodsBuilder

### 什么是 MethodsBuilder？

配置完成后，使用 **Anchor 方法构建器**来构建指令和交易。

`MethodsBuilder` 使用 IDL 提供了一种简化的格式，用于构建调用程序指令的交易。

**比喻说明：** 就像点外卖的APP，选菜（指令）→填地址（账户）→下单（发送交易）

---

### 基本模式

```ts
await program.methods
  .instructionName(instructionDataInputs)  // 指令名称和参数
  .accounts({})                             // 需要的账户
  .signers([])                              // 额外的签名者
  .rpc();                                   // 发送交易
```

**重要细节：**

> API 使用 **camelCase** 命名方式，而不是 Rust 的 snake_case 约定。

**举例：**
- Rust: `deposit_tokens`
- TypeScript: `depositTokens`

通过点语法调用指令名称，并以逗号分隔的值传递参数。

---

### 传递签名者

通过 `.signers()` 传递提供者之外的额外签名者：

```ts
const additionalSigner = Keypair.generate();

await program.methods
  .someInstruction()
  .accounts({})
  .signers([additionalSigner])  // 额外的签名者
  .rpc();
```

---

## 📦 指定账户

### 账户解析

使用点语法在 `MethodsBuilder` 上调用 `.accounts`，并传递一个对象，其中包含指令根据 IDL 期望的每个账户。

**好消息：从 Anchor 0.30.0 开始**

> 可以**自动解析**的账户（如 PDA 或显式地址）包含在 IDL 中，并且在 `.accounts` 调用中**不再需要**！
> 
> `.accountsPartial()` 成为默认值。

**如果要手动传递所有账户**，请使用 `.accountsStrict()`。

---

## 🚀 发送交易的三种方式

### 方式1：`.rpc()` - 最常用

**作用：** 将交易直接发送到区块链

```ts
const txHash = await program.methods
  .deposit(amount)
  .accounts({})
  .rpc();

console.log("Transaction Hash:", txHash);
```

**比喻说明：** 就像一键下单，直接提交！

---

### 方式2：`.transaction()` - 需要后端签名

**使用场景：** 需要后端签名的场景（例如前端用户钱包创建交易，后端密钥对安全签名）

```ts
// 前端：创建交易
const transaction = await program.methods
  .deposit(amount)
  .accounts({})
  .transaction();

// 发送到后端签名...

// 前端：发送交易到链上
await sendTransaction(transaction, connection);
```

**比喻说明：** 就像合同需要多方签字！

---

### 方式3：`.instruction()` - 批量操作

**使用场景：** 要捆绑多个 Anchor 指令

```ts
// 创建第一个指令
const instructionOne = await program.methods
  .depositTokens(amount1)
  .accounts({})
  .instruction();

// 创建第二个指令
const instructionTwo = await program.methods
  .withdrawTokens(amount2)
  .accounts({})
  .instruction();

// 将两个指令添加到一个交易
const transaction = new Transaction().add(instructionOne, instructionTwo);

// 发送交易
await sendTransaction(transaction, connection);
```

**比喻说明：** 就像批量下单，一次处理多个操作！

---

## 📊 获取链上数据

### 痛点：账户太多怎么管理？

当您的程序创建数百个账户时，跟踪它们会变得具有挑战性。

**解决方案：** Program 对象提供了高效获取和筛选程序账户的方法！

---

### 获取所有账户

获取特定账户类型的所有地址：

```ts
const accounts = await program.account.counter.all();

console.log("找到", accounts.length, "个 Counter 账户");
```

---

### 筛选账户

使用 `memcmp` 标志筛选特定账户：

```ts
const accounts = await program.account.counter.all([
  {
    memcmp: {
      offset: 8,  // 跳过 discriminator (8字节)
      bytes: bs58.encode(new BN(0, "le").toArray()),  // 匹配值为0
    },
  },
]);
```

**这个例子：** 获取所有第一个字段等于 0 的 `Counter` 账户。

**比喻说明：** 就像数据库的 WHERE 查询！

---

### 获取单个账户

要检查账户数据是否发生变化，可以使用 `fetch` 获取特定账户的反序列化账户数据：

```ts
const account = await program.account.counter.fetch(ACCOUNT_ADDRESS);

console.log("Counter:", account.count);
```

---

### 批量获取

同时获取多个账户：

```ts
const accounts = await program.account.counter.fetchMultiple([
  ACCOUNT_ADDRESS_ONE,
  ACCOUNT_ADDRESS_TWO,
]);

console.log("Account 1:", accounts[0].count);
console.log("Account 2:", accounts[1].count);
```

**比喻说明：** 就像批量查询快递状态！

---

## 📡 监听链上事件 - 实时更新

### 为什么需要监听？

**痛点：** 与其每次用户连接钱包时都获取链上数据，不如设置监听区块链的系统，并将相关数据存储到数据库中。

**比喻说明：** 就像订阅推送通知，有更新时自动告诉你，不用一直刷新！

---

### 两种监听方法

#### 1. 轮询（Polling）

客户端以一定的时间间隔反复检查新数据。

**缺点：** 服务器无论数据是否发生变化都会返回最新数据，可能会返回重复信息。

**比喻说明：** 就像每分钟刷新一次网页，很费资源！

#### 2. 流式传输（Streaming）- 推荐

服务器仅在更新发生时将数据推送到客户端。

**优点：** 更高效，能够实时传输数据，因为只传输相关的变化。

**比喻说明：** 就像手机推送通知，有更新才通知你！

---

### 使用 Webhooks 监听事件

对于流式传输 Anchor 指令，可以使用监听事件的 **webhooks**，并在事件发生时将其发送到您的服务器。

**举例：** 每当您的市场上发生 NFT 销售时，更新数据库条目。

**小伙伴们要特别注意啦：**

> 对于极低延迟的应用程序（如 5 毫秒的差异也很重要），webhooks 可能无法提供足够的速度。

---

## 🔔 发出事件 - 告诉客户端"发生了什么"

### 两种事件宏

Anchor 提供了两个用于发出事件的宏：

#### 1. `emit!()` - 标准方式

通过 `sol_log_data()` 系统调用直接将事件发出到程序日志中。

**程序端代码：**

```rust
use anchor_lang::prelude::*;

declare_id!("8T7MsCZyzxboviPJg5Rc7d8iqEcDReYR2pkQKrmbg7dy");

#[program]
pub mod event {
    use super::*;

    pub fn emit_event(_ctx: Context<EmitEvent>, input: String) -> Result<()> {
        emit!(CustomEvent { message: input });  // 发出事件
        Ok(())
    }
}

#[derive(Accounts)]
pub struct EmitEvent {}

#[event]
pub struct CustomEvent {
    pub message: String,
}
```

**客户端监听：**

```ts
// 设置监听器（在发送交易之前）
const listenerId = program.addEventListener("customEvent", event => {
  // 处理事件数据
  console.log("Event Data:", event);
});
```

**比喻说明：** 就像广播电台，发出信号，订阅者自动收到！

---

#### 2. `emit_cpi!()` - CPI 方式

通过跨程序调用（CPI）发出事件。事件数据被编码并包含在 CPI 的指令数据中，而不是程序日志中。

**程序端代码：**

```rust
#[program]
pub mod event_cpi {
    use super::*;

    pub fn emit_event(ctx: Context<EmitEvent>, input: String) -> Result<()> {
        emit_cpi!(CustomEvent { message: input });  // CPI 方式发出事件
        Ok(())
    }
}

#[event_cpi]
#[derive(Accounts)]
pub struct EmitEvent {}

#[event]
pub struct CustomEvent {
    pub message: String,
}
```

**客户端解码：**

```ts
// 获取交易数据
const transactionData = await program.provider.connection.getTransaction(
  transactionSignature,
  { commitment: "confirmed" },
);

// 从 CPI 指令数据中解码事件
const eventIx = transactionData.meta.innerInstructions[0].instructions[0];
const rawData = anchor.utils.bytes.bs58.decode(eventIx.data);
const base64Data = anchor.utils.bytes.base64.encode(rawData.subarray(8));
const event = program.coder.events.decode(base64Data);

console.log(event);
```

---

## 💡 最佳实践

### 1. 错误处理

**重要：** 始终处理可能的错误！

```ts
try {
  const txHash = await program.methods
    .deposit(amount)
    .accounts({})
    .rpc();
    
  console.log("Success:", txHash);
} catch (error) {
  console.error("Error:", error);
  // 向用户展示友好的错误信息
}
```

---

### 2. 交易确认

**建议：** 等待交易确认后再更新 UI

```ts
const txHash = await program.methods
  .deposit(amount)
  .accounts({})
  .rpc();

// 等待确认
await connection.confirmTransaction(txHash, "confirmed");

// 更新 UI
```

---

### 3. 缓存数据

**优化：** 将常用数据缓存到本地/数据库

```ts
// 不好的做法：每次都获取
const account = await program.account.counter.fetch(address);

// 好的做法：缓存+监听更新
let cachedAccount = await program.account.counter.fetch(address);

// 监听变化
program.account.counter.subscribe(address, account => {
  cachedAccount = account;  // 更新缓存
});
```

---

## 💡 学习建议

### 本章重点回顾

**必须掌握：**
1. ✓ 如何设置 Provider
2. ✓ 如何创建 Program 对象
3. ✓ 如何调用指令（`.rpc()`）
4. ✓ 如何获取账户数据（`.fetch()`）

**了解即可：**
- 事件监听（`emit!()` / `emit_cpi!()`）
- 批量操作（`.instruction()`）
- 高级筛选（`memcmp`）

---

## 🎯 下一步

学完这一章，你应该：

- ✓ 能用 TypeScript 调用 Anchor 程序
- ✓ 能获取和筛选链上数据
- ✓ 了解事件监听的基本用法

**准备好了吗？** 下一章我们将学习 **Anchor 的高级特性**！

---

**记住：客户端是用户和程序之间的桥梁，要做好错误处理和用户体验！** 💪

---

**最后更新**：2026年1月9日  
**制作人**：bruceCao  
**学习建议**：结合实际项目练习客户端开发！