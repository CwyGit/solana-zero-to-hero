# 03-所有权检查 - 验证身份！🆔

嘿，小伙伴！👋

**所有权检查**是Solana程序安全的第一道防线。它们验证账户是否由预期的程序拥有！

**比喻说明：** 就像检查钥匙是不是配这把锁，必须匹配才行！

---

## 🎯 什么是所有权检查？

### AccountInfo结构

每个账户都有一个`owner`字段，标识哪个程序控制该账户。

**检查内容：**
- ✅ 账户的owner字段
- ✅ 是否匹配预期的program_id

**比喻：** 就像房产证，证明这个房子是谁的！

---

## ⚠️ 为什么重要？

### 痛点：假冒账户

**没有owner检查：** 攻击者可以创建"完美副本"账户！

**后果：**
- 相同的数据结构
- 正确的discriminator
- 所有字段都匹配
- **但由错误的程序控制！**

**比喻：** 就像假身份证，看起来一模一样但是假的！

---

## 🔓 漏洞示例

### 易受攻击的代码

```rust
#[program]
pub mod insecure_check {
    pub fn instruction(ctx: Context<Instruction>) -> Result<()> {
        let account_data = ctx.accounts.program_account.try_borrow_data()?;
        let mut account_data_slice: &[u8] = &account_data;
        let account_state = ProgramAccount::try_deserialize(&mut account_data_slice)?;

        // ✅ 验证owner字段
        if account_state.owner != ctx.accounts.owner.key() {
            return Err(ProgramError::InvalidArgument.into());
        }

        // ❌ 但没有检查账户的program owner！
        
        Ok(())
    }
}

#[derive(Accounts)]
pub struct Instruction<'info> {
    pub owner: Signer<'info>,
    #[account(mut)]
    /// CHECK: 危险！没有owner检查
    pub program_account: UncheckedAccount<'info>,
}
```

**问题在哪？** 虽然检查了数据，但没检查账户owner！

---

## 🕵️ 攻击方式

**攻击者可以：**
1. 创建一个相同数据结构的账户
2. 填充正确的owner字段
3. 传递给你的指令
4. 你的程序会"相信"这个假账户

**比喻：** 就像伪造一个看起来真实的合同！

---

## ✅ 正确做法

### 方法1：使用Account类型（推荐）

```rust
#[derive(Accounts)]
pub struct Instruction<'info> {
    pub owner: Signer<'info>,
    #[account(mut)]
    pub program_account: Account<'info, ProgramAccount>, // ✅ 自动检查
}
```

**好处：** Anchor自动验证owner！

---

### 方法2：添加owner约束

```rust
#[derive(Accounts)]
pub struct Instruction<'info> {
    pub owner: Signer<'info>,
    #[account(mut, owner = ID)]  // ✅ 明确owner
    /// CHECK: 已添加owner检查
    pub program_account: UncheckedAccount<'info>,
}
```

---

### 方法3：手动检查

```rust
pub fn instruction(ctx: Context<Instruction>) -> Result<()> {
    // ✅ 手动验证owner
    if ctx.accounts.program_account.owner != &ID {
        return Err(ProgramError::IncorrectProgramId.into());
    }
    
    // 现在可以安全使用了
    Ok(())
}
```

---

## 🛠️ Pinocchio实现

在Pinocchio中使用`is_owned_by()`：

```rust
if !self.accounts.program_account.is_owned_by(&ID) {
    return Err(ProgramError::IncorrectProgramId.into());
}
```

---

## 🎯 重要例外

### 修改账户数据时

**好消息：** Solana运行时自动阻止程序写入它们不拥有的账户！

**但是：** 读取操作和验证逻辑仍需自己检查！

**比喻：** 就像你不能改别人的房子，但能看能参观，所以看的时候要小心！

---

## 💡 学习建议

### 何时需要owner检查

**必须检查：**
- ✅ 读取账户数据时
- ✅ 验证账户状态时
- ✅ 基于账户做决策时

**可以跳过：**
- ✅ 只写入自己拥有的账户时（运行时保护）

---

## ❓ 常见问题

### Q1: 为什么数据验证不够？

**答：** 因为攻击者可以完美复制数据！

数据验证只检查内容，**不检查来源**！

**比喻：** 就像检查合同内容对了，但不知道是真合同还是复印件！

---

### Q2: Account类型真的自动检查吗？

**答：** 是的！

Anchor的`Account<'info, T>`会：
1. 检查discriminator
2. **检查owner**
3. 反序列化数据

---

### Q3: UncheckedAccount什么时候用？

**答：** 只有在你**完全知道自己在做什么**时！

常见场景：
- 系统程序账户
- 其他已知程序的账户
- 你会手动检查的情况

---

## 🎯 最佳实践

### 检查清单

**部署前验证：**
- [ ] 所有程序账户都有owner检查
- [ ] 使用Account类型（而非UncheckedAccount）
- [ ] 或手动添加owner验证
- [ ] 理解每个UncheckedAccount的风险

---

### 代码审查要点

**看到这些要警惕：**
- ❌ `UncheckedAccount`没有owner检查
- ❌ 只验证数据，不验证owner
- ❌ 假设账户是可信的

---

**现在小伙伴们懂了吧？** Owner检查防止假冒账户！🆔

**比喻总结：** 就像查验房产证，确认房子真的是你的！

---

**最后更新**：2026年1月9日  
**制作风格**：莫式风格  
**学习建议**：owner检查和signer检查一样重要，缺一不可！