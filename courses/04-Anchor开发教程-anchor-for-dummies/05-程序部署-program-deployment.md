# 程序部署 - 让你的代码"飞上天" 🚀

嘿，小伙伴！👋

程序写完了，测试也通过了，现在是最激动人心的时刻 - **部署到链上**！

## 💭 部署程序意味着什么？

**比喻说明：** 

就像APP开发：
- **本地开发** = 在你电脑上写代码
- **测试** = 在模拟器里测试
- **部署** = 发布到App Store，全世界都能用！

在 Solana 中：
- **开发** = 写 Anchor 程序
- **测试** = 在本地验证器测试
- **部署** = 发布到 devnet/mainnet，用户可以交互

---

## 🎯 本章你会学到什么？

- ✅ 如何构建和部署程序
- ✅ 如何升级已部署的程序
- ✅ 如何处理部署失败
- ✅ 如何上传 IDL 文件
- ✅ 如何进行可验证构建

---

## 📦 第一步：构建程序

### 构建命令

完成程序后，将其部署到 devnet 或 mainnet，以便用户可以进行交互。

首先，构建您的程序以生成必要的部署文件：

```bash
anchor build
```

**这个命令做了什么？**

这将创建一个 `target/deploy` 文件夹，其中包含：
- `<project-name>.so` - 程序的字节码（可执行文件）
- `<project-name>-keypair.json` - 用于部署的生成密钥对

**比喻说明：** 就像把源代码编译成exe文件，.so文件就是Solana的"可执行程序"！

---

### 获取程序地址

**疑问：** 我的程序会部署到哪个地址？

好问题！运行这个命令查看：

```bash
solana address -k target/deploy/<project-name>-keypair.json
```

**输出示例：** `7NL2qWArf2BbE...（一串地址）`

**小伙伴们要特别注意啦：** 

> 您可以通过替换 `<project-name>-keypair.json` 文件中的密钥对来使用**自定义地址**！

---

### 配置程序ID

在 `lib.rs` 中更新 `declare_id!()` 函数为此地址：

```rust
declare_id!("7NL2qWArf2BbEBBH1vTRZCsoNqFATTddH6h8GkVvrLpG");
```

然后配置您的 `Anchor.toml`，以使用正确的目标集群和程序 ID：

```toml
[provider]
cluster = "devnet"  # 或 "mainnet-beta"

[programs.devnet]
<project-name> = "<PROGRAM_ID>"
```

**现在小伙伴们懂了吧？** 需要在两个地方配置程序ID：代码里和配置文件里！

---

## 🚀 第二步：部署程序

### 部署命令

配置好后，部署超级简单：

```bash
anchor deploy
```

这将使用 `Anchor.toml` 中指定的：
- 地址
- 集群（devnet/mainnet）
- 钱包作为费用支付者

**比喻说明：** 就像一键发布APP到应用商店！

---

## ⚠️ 处理部署失败

### 什么情况会失败？

**常见原因：**
- 网络拥堵
- 余额不足
- 程序太大
- 超时

### 失败后怎么办？

失败的部署会创建持有 lamports 的**中间缓冲账户**。你会看到类似以下的恢复指令：

```
==================================================================================
Recover the intermediate account's ephemeral keypair file with
`solana-keygen recover` and the following 12-word seed phrase:
==================================================================================
valley flat great hockey share token excess clever benefit traffic avocado athlete
==================================================================================
```

**疑问：** 这是啥意思？我的钱是不是丢了？

别怕！你的 lamports 还在，只是锁在缓冲账户里了！

---

### 恢复方案

有两种选择：

#### 方案1：继续部署（推荐）

**第一步：** 恢复密钥对

```bash
solana-keygen recover -o <KEYPAIR_PATH>
```

按提示输入 12 个单词的助记词（上面那12个单词）

**第二步：** 使用缓冲区部署

```bash
solana program deploy ./target/deploy/<project-name>.so \
  --program-id ./target/deploy/<project-name>-keypair.json \
  --buffer ./target/deploy/<buffer>-keypair.json
```

**比喻说明：** 就像快递没送到，重新投递一次！

---

#### 方案2：关闭缓冲区（回收资金）

如果你不想继续部署了，可以关闭缓冲区回收 lamports：

```bash
solana program close <ADDRESS>
```

**比喻说明：** 就像取消订单，退款！

---

## 🔧 高级部署选项

### 通过 Solana CLI 部署

您也可以直接使用 Solana CLI 而不是 Anchor：

```bash
solana program deploy ./target/deploy/<project-name>.so \
  --program-id ./target/deploy/<project-name>-keypair.json
```

**何时使用？** 当你需要更精细的控制时。

---

### 网络拥堵时的优化

在网络拥堵期间，使用以下标志可以**提高部署成功率**：

```bash
solana program deploy ./target/deploy/<project-name>.so \
  --program-id ./target/deploy/<project-name>-keypair.json \
  --with-compute-unit-price 10000 \  # 设置计算单元价格
  --use-rpc \                         # 使用RPC而不是TPU
  --max-sign-attempts 100            # 最大重试次数
```

**参数说明：**
- `--with-compute-unit-price` - 愿意支付的价格（微lamports）
- `--use-rpc` - 将交易发送到 RPC（更稳定）
- `--max-sign-attempts` - 重试次数（网络慢时多试几次）

**比喻说明：** 就像高速公路堵车时，愿意多付点过路费走ETC通道！

---

## 🔄 升级程序

### 为什么需要升级？

程序部署后，你可能需要：
- 修复 bug
- 添加新功能
- 优化性能

### 升级 vs 新部署

**问题：** 默认情况下，`anchor deploy` 会创建一个**新的程序 ID**。

**后果：**
- 所有 PDA 失效（因为PDA依赖程序ID）
- 用户需要更新地址
- 所有代币账户权限需要修改

**解决方案：** 使用 `anchor upgrade` **在保留地址**的同时升级！

---

### Anchor 升级命令

```bash
anchor upgrade target/deploy/<project-name>.so --program-id <PROGRAM_ID>
```

**关键区别：** 使用 `upgrade` 而不是 `deploy`！

---

### 处理程序大小变化

**疑问：** 如果新版本的程序更大怎么办？

好问题！如果新的可执行文件比已部署版本更大，请先**扩展程序账户**：

```bash
solana program extend ./target/deploy/<project-name>.so <ADDITIONAL_BYTES>
```

**举例：**
- 旧版本：10KB
- 新版本：12KB
- 需要扩展：2KB = 2048 bytes

```bash
solana program extend ./target/deploy/my-program.so 2048
```

**比喻说明：** 就像房子不够住，先盖个加建再搬新家具！

---

### 通过 Solana CLI 升级

流程保持不变：**如有需要先扩展，然后部署**

```bash
# 如果需要，先扩展
solana program extend ./target/deploy/<project-name>.so <ADDITIONAL_BYTES>

# 然后部署（会自动升级）
solana program deploy ./target/deploy/<project-name>.so \
  --program-id ./target/deploy/<project-name>-keypair.json
```

---

## 🔒 使程序不可变

### 什么是不可变？

**不可变程序** = 部署后永远不能修改

**为什么要这样？**
- 用户信任（代码不会被偷偷改）
- 去中心化（开发者也改不了）

### 如何设置？

移除升级权限，使您的程序不可变：

```bash
solana program set-upgrade-authority <PROGRAM_ID> --final
```

**小伙伴们要特别注意啦：** 

> ⚠️ 此操作**不可逆**！一旦设置，程序将**永远无法更新**。

**比喻说明：** 就像把软件刻在光盘上，永远改不了了！

**建议：** 确保程序经过**充分测试**和**审计**后再设置为不可变！

---

## 🚚 迁移程序（慎用）

### 什么是迁移？

迁移将程序从一个地址转移到另一个地址。

### 迁移命令

```bash
solana program migrate ./target/deploy/<project-name>.json
```

CLI 会：
1. 关闭旧程序
2. 重新部署到新位置

### ⚠️ 迁移的严重后果

**小伙伴们要特别注意啦：** 

> ⚠️ 迁移会**破坏所有现有的 PDA**，因为它们是从旧的程序 ID 派生的！

**后果：**
- 所有 PDA 地址改变
- 用户必须更新应用程序以使用新地址
- 任何以 PDA 为权限的代币账户的权限都需要修改

**比喻说明：** 就像整个公司搬家，所有地址都变了，通讯录全部要更新！

**建议：** **尽量不要迁移**！除非万不得已。

---

## 📄 IDL 文件 - 程序的"说明书"

### 什么是 IDL？

**IDL (Interface Description Language)** = 接口描述语言

**作用：** 提供了程序指令和账户的标准化 JSON 描述，从而简化了客户端集成。

**比喻说明：** 就像API文档，告诉别人怎么用你的程序！

---

### 上传 IDL

将 IDL 上传到链上，以帮助开发者集成您的程序：

```bash
anchor idl init --filepath target/idl/<program_name>.json <PROGRAM_ID>
```

**为什么要上传？**
- 开发者可以自动生成客户端代码
- 区块浏览器可以解析交易
- 钱包可以展示友好的交易信息

---

### 升级 IDL

重新部署程序后，记得更新链上 IDL：

```bash
anchor idl upgrade --filepath target/idl/<program_name>.json <PROGRAM_ID>
```

**现在小伙伴们懂了吧？** 程序升级了，IDL也要跟着升级！

---

## ✅ 可验证构建 - 证明"我没骗你"

### 为什么需要可验证构建？

**问题：** 用户怎么知道链上的程序和Github的代码是一样的？

**答案：** 可验证构建！

**比喻说明：** 就像食品的可追溯二维码，证明"我卖的和宣传的一样"！

---

### 什么是可验证构建？

**已验证的构建**确保部署在 Solana 上的可执行程序与您代码库中的源代码**一致**。

**验证过程：**
1. 从Github下载源代码
2. 在标准环境中构建
3. 比较链上程序的哈希值
4. 检测是否完全匹配

---

### 为什么需要Docker？

**问题：** 使用 Solana CLI 构建程序可能会将**特定于机器的代码**嵌入到二进制文件中。

**后果：** 在不同机器上编译相同的程序可能会生成**不同的可执行文件**！

**解决方案：** 在固定依赖项的 Docker 容器中进行构建，获得**可重复的结果**。

**比喻说明：** 就像标准化的生产车间，确保每辆车都一模一样！

---

### 可验证构建命令

Anchor 提供了处理构建和 Docker 配置的 CLI 命令：

```bash
anchor build --verifiable
```

这会：
1. 在 Docker 容器中构建
2. 生成可重复的字节码
3. 输出可验证的哈希值

---

### 验证已部署的程序

验证主网部署的程序构建：

```bash
anchor verify -p <lib-name> <program-id>
```

**参数说明：**
- `<lib-name>` - 对应于程序的 `Cargo.toml` 文件中定义的名称
- `<program-id>` - 链上程序的地址

**输出：**
- ✅ `Verification successful!` - 代码和链上程序一致
- ❌ `Verification failed!` - 不一致，有问题！

---

## 💡 部署最佳实践

### 1. 部署前检查清单

在部署到主网前，确保：

- ✅ 所有测试通过
- ✅ 代码经过审计
- ✅ 在 devnet 上充分测试
- ✅ IDL 文件准备好
- ✅ 有足够的 SOL 支付部署费用
- ✅ 备份好密钥对

---

### 2. 部署流程建议

**推荐流程：**
1. **Devnet 测试** - 先在 devnet 充分测试
2. **审计** - 找专业团队审计（如果是重要项目）
3. **Mainnet 部署** - 部署到主网
4. **可升级期** - 保留升级权限一段时间
5. **设为不可变** - 确认没问题后再设为不可变

**比喻说明：** 就像新产品发布，先内测、公测，再正式上线！

---

### 3. 升级策略

**建议：**
- 在合约代码中包含版本号
- 记录每次升级的变更
- 提前通知用户即将升级
- 保留升级权限至少一个月

---

### 4. 安全建议

**关键点：**
- 🔐 妥善保管升级权限密钥
- 🔐 使用多签钱包管理升级权限
- 🔐 定期备份密钥
- 🔐 审计通过后再设为不可变

---

## 💡 学习建议

### 本章重点回顾

**必须掌握：**
1. ✓ `anchor build` 和 `anchor deploy` 命令
2. ✓ 如何配置 Anchor.toml
3. ✓ 如何升级程序（`anchor upgrade`）
4. ✓ 部署失败的恢复方法

**了解即可：**
- Solana CLI 部署
- 程序迁移（尽量不用）
- 可验证构建（高级话题）

---

## 🎯 下一步

学完这一章，你应该：

- ✓ 能构建和部署 Anchor 程序
- ✓ 知道如何升级程序
- ✓ 了解部署失败的处理方法
- ✓ 理解可验证构建的重要性

**准备好了吗？** 下一章我们将学习**客户端开发** - 如何用 TypeScript 与你的程序交互！

---

**记住：测试、审计、验证，然后再部署到主网！** 不要拿用户的资金开玩笑！🛡️

---

**最后更新**：2026年1月9日  
**制作风格**：莫式风格  
**重要提醒**：部署到主网前务必在 devnet 充分测试！