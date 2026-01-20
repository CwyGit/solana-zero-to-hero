# 05-Approve和Revoke指令 - 授权管理！👥

嘿，小伙伴！👋

**本章内容：** Approve（批准）和Revoke（撤销）两个指令！

---

## 📋 Approve指令 - 授权代理

### 什么是Approve？

**作用：** 批准允许**代理人**代表账户所有者转移特定数量的代币！

**好处：** 使得程序化的代币转移成为可能，而无需授予完整的账户控制权！

**机制：** 我们设置一个"批准"的金额，代理人只能转移不超过该金额的代币！

**比喻说明：** 就像给秘书授权，可以代你签字花钱，但有额度限制！

---

## 🚫 Revoke指令 - 撤销授权

### 什么是Revoke？

**作用：** 撤销会移除当前代理人对账户的权限，将完整的控制权还给账户所有者！

**特性：**
- ⚡ 立即取消任何现有的代理权限
- 🔐 只有账户所有者可以撤销代理
- ❌ 代理人本身无法撤销

**比喻说明：** 就像收回授权书，秘书立即失去签字权！

---

## 🎯 前提条件

在我们可以批准或撤销任何代币账户之前，我们需要已经完成以下操作：

### 1. Mint账户

✅ 初始化一个`Mint`账户

---

### 2. Token账户

✅ 初始化一个`Token`账户或`Associated Token`账户，我们将对其进行控制

---

## ⚠️ 小数位处理

**小伙伴们要特别注意啦：**

我们批准的代币数量是根据小数位数"标准化"的！

**举例：**
- 小数位：6
- 想批准：1个代币
- 实际填：1,000,000（1_000_000）

---

## 💻 Approve代码示例

以下是CPI到`approve()`指令的样子：

```rust
approve(
    CpiContext::new(
        ctx.accounts.token_program.to_account_info(),
        Approve {
            to: ctx.accounts.token_account.to_account_info(),
            delegate: ctx.accounts.delegate.to_account_info(),
            authority: ctx.accounts.authority.to_account_info(),
        },
    ),
    &1_000_000,
)?;
```

### Approve结构

**包含3个账户：**

1. **to** - Token账户（被授权的账户）
2. **delegate** - 代理人（获得授权的人）
3. **authority** - 权限账户（Token账户的owner）

---

## 💻 Revoke代码示例

以下是CPI到`revoke()`指令的样子：

```rust
revoke(
    CpiContext::new(
        ctx.accounts.token_program.to_account_info(),
        Revoke {
            source: ctx.accounts.token_account.to_account_info(),
            authority: ctx.accounts.authority.to_account_info(),
        },
    ),
)?;
```

### Revoke结构

**包含2个账户：**

1. **source** - Token账户（要撤销授权的账户）
2. **authority** - 权限账户（Token账户的owner）

---

## ❓ 常见问题

### Q1: delegate可以做什么？

**答：** delegate可以代表owner转移代币，但有额度限制！

**限制：** 只能转移approve时指定的数量！

---

### Q2: 可以有多个delegate吗？

**答：** 不能！一个Token账户同时只能有一个delegate！

**新approve会覆盖旧delegate！**

---

### Q3: delegate可以撤销自己吗？

**答：** 不能！只有owner可以revoke！

---

### Q4: approve后金额可以修改吗？

**答：** 可以！再次approve就会覆盖之前的额度！

---

## 💡 学习建议

### 本章重点回顾

**必须掌握：**
1. ✓ Approve和Revoke的作用
2. ✓ delegate的限制
3. ✓ 只能有一个delegate

**了解即可：**
- delegate的高级应用场景

---

## 🎯 下一步

**准备好了吗？** 下一章我们将学习**Set Authority指令** - 如何转移权限！

---

**现在小伙伴们懂了吧？** Approve是授权，Revoke是撤销！👥

---

**最后更新**：2026年1月9日  
**制作人**：bruceCao  
**学习建议**：Approve在DeFi中很常用，要理解透彻！