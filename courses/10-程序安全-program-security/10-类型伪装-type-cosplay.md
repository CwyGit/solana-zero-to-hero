# 10-类型伪装 - 防止账户冒充！🎭

嘿，小伙伴！👋

**类型伪装**攻击允许攻击者传入错误类型的账户，绕过类型检查！

**比喻说明：** 就像拿驾照冒充身份证！

---

## 🎯 什么是类型伪装？

**问题：** 程序没有验证账户的discriminator，接受了错误类型的账户。

**危险：**
- 类型混淆
- 数据误读
- 逻辑错误

---

## ⚠️ 漏洞示例

```rust
// ❌ 使用UncheckedAccount，没有类型验证
pub struct Process<'info> {
    /// CHECK: 危险！
    pub account: UncheckedAccount<'info>,
}
```

**攻击：** 传入任何账户，只要数据结构相似就行！

---

## ✅ 正确做法

**使用Account类型：**

```rust
pub struct Process<'info> {
    pub account: Account<'info, MyData>, // ✅ 自动验证类型
}
```

**Anchor自动检查discriminator！**

---

## 💡 Discriminator是什么？

**账户类型标识符：** 前8字节存储账户类型信息。

**Anchor自动：**
- 在init时设置discriminator
- 在读取时验证discriminator

---

**现在小伙伴们懂了吧？** 类型验证防止账户冒充！🎭

---

**最后更新**：2026年1月9日  
**制作人**：bruceCao