# 02-签名者检查 - 第一道防线！🔐

嘿，小伙伴！👋

**签名者检查**是手写签名的数字等价物，它们证明账户持有人确实授权了交易！

**比喻说明：** 就像你去银行取钱必须签名一样，区块链上也需要数字签名！

---

## 🎯 为什么需要签名者检查？

### 痛点：任何人都能操作你的账户？

**想象一下：** 如果没有签名者检查，任何人都可以转移你的资产，就像你的银行卡没有密码！

**小伙伴们要特别注意啦：**

> ⚠️ 缺少签名者检查的后果是灾难性的：
> - 未经授权的访问
> - 账户资金被盗
> - 程序状态完全失控

---

## 🔓 漏洞示例

### 易受攻击的代码

```rust
#[program]
pub mod insecure_update {
    pub fn update_ownership(ctx: Context<UpdateOwnership>) -> Result<()> {
        ctx.accounts.program_account.owner = ctx.accounts.new_owner.key();
        Ok(())
    }
}

#[derive(Accounts)]
pub struct UpdateOwnership<'info> {
    /// CHECK: 注意！这里没有检查签名
    pub owner: UncheckedAccount<'info>,
    /// CHECK: 新所有者
    pub new_owner: UncheckedAccount<'info>,
    #[account(mut, has_one = owner)]
    pub program_account: Account<'info, ProgramAccount>,
}
```

**看起来安全吗？** 不！有致命缺陷！

---

## 🕵️ 攻击方式

**攻击者可以：**
1. 找到任何程序账户
2. 读取当前owner的公钥
3. 构造交易，假装是owner
4. 将自己设置为new_owner
5. **无需真实owner签名**就能提交！

**结果：** 账户被劫持！

**比喻：** 就像小偷拿着你的身份证复印件就能取走你的钱！

---

## ✅ 正确做法

### 方法1：使用Signer类型

```rust
#[derive(Accounts)]
pub struct UpdateOwnership<'info> {
    pub owner: Signer<'info>,  // ✅ 正确！
    /// CHECK: 新所有者
    pub new_owner: UncheckedAccount<'info>,
    #[account(mut, has_one = owner)]
    pub program_account: Account<'info, ProgramAccount>,
}
```

---

### 方法2：添加signer约束

```rust
#[derive(Accounts)]
pub struct UpdateOwnership<'info> {
    #[account(signer)]  // ✅ 添加约束
    /// CHECK: 所有者
    pub owner: UncheckedAccount<'info>,
    // ...
}
```

---

### 方法3：手动检查

```rust
pub fn update_ownership(ctx: Context<UpdateOwnership>) -> Result<()> {
    // ✅ 手动检查
    if !ctx.accounts.owner.is_signer {
        return Err(ProgramError::MissingRequiredSignature.into());
    }
    
    ctx.accounts.program_account.owner = ctx.accounts.new_owner.key();
    Ok(())
}
```

---

## 🛠️ Pinocchio实现

在Pinocchio中，必须在指令逻辑中检查：

```rust
if !self.accounts.owner.is_signer() {
    return Err(ProgramError::MissingRequiredSignature.into());
}
```

**比喻：** Pinocchio就像手动挡，你需要自己检查每一步！

---

## 💡 学习建议

### 记住这些要点

**必须检查签名的情况：**
- ✅ 转移所有权
- ✅ 修改权限
- ✅ 资金转移
- ✅ 敏感数据更新

**检查方法优先级：**
1. `Signer<'info>` - 最推荐
2. `#[account(signer)]` - 也很好
3. `is_signer` - 手动检查

---

## ❓ 常见问题

### Q1: has_one约束不够吗？

**答：** 不够！

`has_one`只检查公钥匹配，**不检查签名**！

**比喻：** 就像检查身份证号码对了，但没验证是不是本人！

---

### Q2: 什么时候必须用Signer？

**答：** 任何涉及权限的操作！

特别是：
- 所有权转移
- 权限修改
- 资金操作

---

### Q3: UncheckedAccount安全吗？

**答：** 很危险！

除非你**完全知道自己在做什么**，否则避免使用！

---

## 🎯 实战建议

**部署前检查清单：**
- [ ] 所有owner账户都是Signer类型
- [ ] 所有authority账户都有签名检查
- [ ] 敏感操作都验证了签名
- [ ] 没有使用UncheckedAccount（除非必要）

---

**现在小伙伴们懂了吧？** 签名者检查是安全的第一道防线！🔐

**比喻总结：** 就像门锁，虽然简单但必不可少！

---

**最后更新**：2026年1月9日  
**制作人**：bruceCao  
**学习建议**：这是安全基础，务必牢记！