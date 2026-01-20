# 07-PDA共享 - 防止权限滥用！🔑

嘿，小伙伴！👋

**PDA共享**漏洞允许攻击者使用共享的PDA访问他们不应该访问的资源！

**比喻说明：** 就像公共厕所的钥匙，不应该用来开私人房间！

---

## 🎯 什么是PDA共享？

**问题：** 多个用户共享同一个PDA地址。

**危险：** 一个用户可能访问其他用户的资源！

---

## ⚠️ 漏洞示例

```rust
// 错误：只用program作为种子
let (pda, bump) = Pubkey::find_program_address(
    &[b"vault"],
    program_id
);
```

**问题：** 所有用户共享这个PDA！

---

## ✅ 正确做法

**包含用户特定的种子：**

```rust
// 正确：包含用户pubkey
let (pda, bump) = Pubkey::find_program_address(
    &[b"vault", user.key().as_ref()],
    program_id
);
```

---

**现在小伙伴们懂了吧？** PDA要用户专属！🔑

---

**最后更新**：2026年1月9日  
**制作人**：bruceCao