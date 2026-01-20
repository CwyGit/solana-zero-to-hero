# 测试你的 Anchor 程序 - 代码的"安全网" 🛡️

嘿，小伙伴！👋

写完程序代码，接下来最重要的事情是什么？

**答案：测试！测试！测试！**

## 💭 为什么测试这么重要？

你可能在想："程序写完了，看起来能跑，还需要测试吗？"

**危险！** 这是最危险的想法！

**痛点案例：**

2022年，Wormhole 跨链桥因为一个**未经测试的代码**被黑，损失 **3.2亿美元**！  
如果有完善的测试，这个漏洞在部署前就能被发现。

**比喻说明：** 测试就像飞机的安全检查，不测试就上天（部署主网）就是在拿生命开玩笑！

---

## 🎯 本章你会学到什么？

学完这一章，你将掌握：

- ✅ **TypeScript 测试** - 最常用的测试方法
- ✅ **Mollusk 测试** - Rust 单元测试框架
- ✅ **LiteSVM 测试** - 快速的 TypeScript 测试
- ✅ **本地验证器** - 搭建测试环境
- ✅ **Surfnet** - 模拟主网环境

**小伙伴们要特别注意啦：** 彻底的测试可以防止财务损失，建立用户信托，并确保您的程序在所有条件下都能正确运行。

> **在主网部署之前进行严格测试！**

---

## 🧪 TypeScript 测试 - 最常用的方法

### 为什么用 TypeScript？

TypeScript 测试是**最常见的方法**，为什么？

**理由很简单：** 
- 你的 dApp 客户端无论如何都需要用 TypeScript
- 这样可以**同时开发测试和客户端代码**

**比喻说明：** 就像边做菜边尝味道，能及时发现问题！

### TypeScript 测试的优势

✅ **模拟真实的客户端交互** - 测试真实用户会怎么用  
✅ **测试复杂的工作流和边界情况** - 各种极端情况都能测  
✅ **对 API 更改提供即时反馈** - 改了代码马上知道有没有问题  
✅ **验证成功和错误场景** - 成功的路径要测，失败的路径也要测

### 如何使用？

**好消息：** 每个 Anchor CLI 项目都包含一个测试文件夹，其中有一个**准备好测试的 TypeScript 文件**！

运行测试超级简单：

```bash
anchor test
```

**疑问：** 这个命令做了什么？

这个命令会：
1. 编译你的程序
2. 启动本地验证器
3. 部署程序到本地验证器
4. 运行测试文件
5. 关闭本地验证器

**比喻说明：** 就像自动化的质检流水线，一键搞定所有检查！

### 本地网络测试

> 通过在配置中将集群设置为 `localnet` 来在本地网络上运行。这会启动一个本地验证器并向提供者钱包添加 **1,000 SOL**。

**小伙伴们要特别注意啦：** 如果您需要包含数据的额外账户，请查看后面关于[运行本地验证器](#本地验证器-的强大功能)的内容。

---

## 🦀 Mollusk 测试 - Rust 单元测试框架

### 什么是 Mollusk？

当您需要对测试环境或复杂程序状态设置进行**精细控制**时，Mollusk 提供了解决方案。

**比喻说明：** 如果 TypeScript 测试是"黑盒测试"（从外部测试），Mollusk 就是"白盒测试"（从内部测试）。

### Mollusk 的优势

Mollusk 是一个专为 Solana 程序构建的 **Rust 测试框架**。它使您能够：

- ✅ 在**没有网络开销**的情况下测试程序逻辑（超快！）
- ✅ 轻松设置复杂的账户状态
- ✅ 比完整的集成测试运行**更快**的测试
- ✅ 模拟特定的区块链条件和边界情况

**现在小伙伴们懂了吧？** Mollusk 适合快速测试程序的内部逻辑！

### 如何使用？

**第一步：** 创建一个新的 Anchor 程序（使用 Mollusk 模板）：

```bash
anchor init <name-of-the-project> --test-template mollusk
```

**第二步：** 运行测试：

```bash
anchor test
```

**就这么简单！**

我们在 [Mollusk 测试课程](/zh-CN/courses/testing-with-mollusk) 中详细介绍了如何编写 Mollusk 测试。

---

## ⚡ LiteSVM 测试 - 超快的 TypeScript 测试

### 什么是 LiteSVM？

当您需要像 Mollusk 一样对程序状态进行**细粒度控制**，但使用 **TypeScript** 时，LiteSVM 提供了最佳解决方案！

**比喻说明：** LiteSVM 就像"涡轮增压版的 TypeScript 测试" - 既有 TypeScript 的便利，又有 Mollusk 的速度！

### LiteSVM 的优势

LiteSVM 是一个轻量级测试框架，可以直接在您的测试过程中运行 Solana 虚拟机。它使您能够：

- ✅ **比传统框架显著加快测试速度**（比 `solana-program-test` 快很多）
- ✅ 精确操控账户状态和系统变量
- ✅ **跨多种语言**进行测试：TypeScript、Rust 和 Python
- ✅ 轻松模拟复杂的区块链条件和边界情况

**关键优势：** LiteSVM 通过将虚拟机嵌入到您的测试中，消除了验证器的开销，在不牺牲测试准确性的情况下提供了快速开发周期所需的速度。

**比喻说明：** 就像赛车的轻量化改装，去掉不必要的重量，速度快多了！

### 如何使用？

**第一步：** 安装 `anchor-litesvm` 包：

```bash
npm install git:https://github.com/LiteSVM/anchor-litesvm
```

**第二步：** 将默认的 Anchor 提供程序更改为 `LiteSVMProvider`：

```ts
test("anchor", async () => {
    const client = fromWorkspace("target/types/<program-name>.ts");
    const provider = new LiteSVMProvider(client);  // 使用 LiteSVMProvider
    const program = new Program<Puppet>(IDL, provider);

    // program.methods..
})
```

**就这么简单！** 你可以使用之前学到的[客户端设置](/zh-CN/courses/anchor-for-dummies/client-side-development)与 LiteSVM 一起使用。

我们在 [LiteSVM 测试课程](/zh-CN/courses/testing-with-litesvm) 中全面介绍了 LiteSVM 测试。

---

## 🖥️ 本地验证器 - 你的"私人区块链"

### 什么是本地验证器？

本地验证器就像一个**充当您个人区块链沙盒的验证器**，在本地镜像主网行为。

**比喻说明：** 就像飞行员的模拟器，在安全的环境中练习，不会有真实的风险！

**好消息：** 当您将集群设置为 `localnet` 时，这会**自动发生**。

### 本地验证器的特点

本地验证器操作一个简化的 Solana 账本，并预装了原生程序。

**默认情况：**
- ✅ 仅存储测试数据
- ❌ 无法访问现有主网账户

**限制：** 这限制了使用已建立协议的测试（比如你想测试和 Jupiter 交互的代码）。

**不过别担心！** 我们可以**克隆主网账户**到本地验证器！

---

### 配置本地验证器

在 `Anchor.toml` 的 `[test]` 部分下自定义您的验证器：

```toml
[test]
startup_wait = 10000  # 等待验证器启动的时间（毫秒）
```

**`startup_wait` 的作用：**

这个标志会延迟验证器启动，这在克隆多个增加加载时间的账户时非常有用。

**比喻说明：** 就像电脑启动时加载很多程序，需要等待一会儿才能用！

---

### 克隆主网账户

使用 `[test.validator]` 配置克隆现有主网账户和程序：

```toml
[test.validator]
url = "https://api.mainnet-beta.solana.com"  # 主网 RPC 地址

[[test.validator.clone]]
address = "7NL2qWArf2BbEBBH1vTRZCsoNqFATTddH6h8GkVvrLpG"  # 账户地址1

[[test.validator.clone]]
address = "2RaN5auQwMdg5efgCaVqpETBV8sacWGR8tkK4m9kjo5r"  # 账户地址2

[[test.validator.clone]]
address = "metaqbxxUerdq28cj1RbAWkYQm3ybzjb6a8bt518x1s"  # Metaplex 程序
```

**`clone` 部分的作用：**

会从指定的集群中**复制账户**。当某个账户链接到由 "BPF 可升级加载器" 管理的程序时，Anchor 会自动克隆相关的程序数据账户。

**举例：** 如果你要测试和 Metaplex 交互的代码，就需要克隆 Metaplex 程序！

---

### 加载本地账户数据

使用 `account` 标志从 JSON 文件加载本地账户：

```toml
[[test.validator.account]]
address = "Ev8WSPQsGb4wfjybqff5eZNcS3n6HaMsBkMk9suAiuM"
filename = "some_account.json"
```

**使用场景：** 这种方法非常适合测试预配置的账户状态或主网中不存在的特定配置。

**比喻说明：** 就像游戏的"存档加载"，直接加载特定的游戏状态！

---

## 🌊 Surfnet - 终极测试工具

### 痛点：测试复杂协议太麻烦

测试依赖于跨程序调用（CPI）的 Solana 程序通常需要开发者从主网中导出账户和程序，就像我们在本地验证器部分看到的那样。

**问题：** 这种方法适用于少量账户，但在测试像 **Jupiter** 这样复杂的程序时完全不可行！

**为什么？** 因为 Jupiter 可能依赖于：
- 40+ 个账户
- 8+ 个程序

**一个一个配置？** 我晕，这不是等于没说吗？太麻烦了！

---

### Surfnet 的魔法

**Surfnet** 是 `solana-test-validator` 的替代方案，允许开发者使用**按需获取**的主网账户在本地模拟程序。

**比喻说明：** 就像"云同步"，需要什么账户就自动从主网拉取什么账户！

### 如何使用？

**第一步：** 安装 surfpool

通过官方[安装页面](https://docs.surfpool.run/install)安装 surfpool

**第二步：** 启动 Surfnet

```bash
surfpool start
```

**第三步：** 连接到 Surfnet

```ts
const connection = new Connection("http://localhost:8899", "confirmed");
```

**就这么简单！** 现在你的测试可以访问主网的所有账户了！

我们在 [Surfnet 测试课程](/zh-CN/courses/testing-with-surfpool) 中详细介绍了 Surfnet 的设置和使用。

---

## 📊 测试方法对比

### 如何选择？

| 测试方法 | 速度 | 语言 | 适用场景 |
|---------|------|------|---------|
| **TypeScript** | ⭐⭐⭐ | TypeScript | 标准测试，模拟真实用户交互 |
| **Mollusk** | ⭐⭐⭐⭐⭐ | Rust | 快速单元测试，精细控制 |
| **LiteSVM** | ⭐⭐⭐⭐ | TypeScript/Rust/Python | 快速集成测试，灵活控制 |
| **本地验证器** | ⭐⭐⭐ | TypeScript | 需要克隆主网账户 |
| **Surfnet** | ⭐⭐⭐⭐ | TypeScript | 测试复杂协议（如Jupiter） |

**推荐组合：**

- **日常开发**：TypeScript 测试（快速反馈）
- **单元测试**：Mollusk（快速，细粒度）
- **集成测试**：LiteSVM（快速，真实环境）
- **复杂测试**：Surfnet（模拟主网）

---

## 💡 最佳实践

### 1. 测试驱动开发（TDD）

**推荐流程：**
1. 先写测试（定义预期行为）
2. 再写代码（实现功能）
3. 运行测试（验证正确性）
4. 重构代码（优化实现）

**比喻说明：** 就像先画设计图，再盖房子！

---

### 2. 覆盖所有情况

**必测场景：**
- ✅ **成功路径** - 正常流程能跑通
- ✅ **失败路径** - 错误情况能正确处理
- ✅ **边界情况** - 极端值能正确处理
- ✅ **权限检查** - 未授权操作被拒绝

**小伙伴们要特别注意啦：** 很多人只测试成功路径，忽略失败路径，这是**最危险的**！

---

### 3. 自动化测试

**推荐：** 使用 CI/CD 自动运行测试

**好处：**
- 每次提交代码自动测试
- 防止有 bug 的代码合并
- 保持代码质量

---

## 💡 学习建议

### 本章重点回顾

**必须掌握：**
1. ✓ TypeScript 测试的基本用法
2. ✓ 如何运行 `anchor test`
3. ✓ 本地验证器的配置
4. ✓ 克隆主网账户

**了解即可：**
- Mollusk 测试（Rust 单元测试）
- LiteSVM 测试（快速集成测试）
- Surfnet（复杂协议测试）

### 学习方法建议

**循序渐进：**
1. **第一遍**：学会写基本的 TypeScript 测试
2. **第二遍**：学会配置本地验证器
3. **第三遍**：探索高级测试方法（Mollusk/LiteSVM/Surfnet）

**不要试图一次掌握所有工具！**

很多小伙伴学习的时候，想一次性学会所有测试工具，事实上这是效率最低的学习方法。先掌握 TypeScript 测试，其他的需要时再学！

---

## 🎯 下一步

学完这一章，你应该：

- ✓ 理解测试的重要性
- ✓ 能写基本的 TypeScript 测试
- ✓ 知道如何配置本地验证器
- ✓ 了解不同测试工具的优缺点

**准备好了吗？** 下一章我们将学习如何**部署你的 Anchor 程序**到主网！

---

**测试是保护你程序的最后一道防线！** 千万不要跳过测试就部署到主网，那是在拿用户的钱开玩笑！🛡️

---

**最后更新**：2026年1月9日  
**制作风格**：莫式风格  
**学习建议**：测试是必须掌握的技能，从 TypeScript 测试开始练习！