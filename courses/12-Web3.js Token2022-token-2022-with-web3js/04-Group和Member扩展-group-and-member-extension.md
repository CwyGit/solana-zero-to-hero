# 04-Group和Member扩展 - NFT集合管理！🖼️

嘿，小伙伴！👋

**Group和Member扩展**让你可以创建和管理代币集合，就像NFT集合一样！

**比喻说明：** 就像相册（Group）和照片（Member）的关系！

---

## 🎯 什么是Group和Member？

**Group（组）：** 代币集合的容器
**Member（成员）：** 集合中的单个代币

**关系：**
- 一个Group可以有多个Member
- 一个Member只能属于一个Group

**比喻：** 就像班级和学生的关系！

---

## 🚀 使用场景

**最适合：**
- 🖼️ NFT集合
- 🎮 游戏道具系列
- 🎫 会员卡体系

---

## 💡 创建Group

```typescript
// 创建带Group扩展的Mint
const groupMint = await createMintWithGroup(
    connection,
    payer,
    authority,
    maxSize: 1000 // 最大成员数
);
```

---

## 🎯 添加Member

```typescript
// 创建Member并加入Group
const memberMint = await createMintAsMember(
    connection,
    payer,
    groupMint,
    memberNumber: 1
);
```

---

**现在小伙伴们懂了吧？** Group管理代币集合很方便！🖼️

---

**最后更新**：2026年1月9日  
**制作风格**：莫式风格
