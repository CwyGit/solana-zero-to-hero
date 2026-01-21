# 🎯 TypeScript铸造SPL代币挑战 - 参考答案

使用 TypeScript 和 @solana/web3.js 铸造代币！

---

## 📋 题目回顾

**目标**：使用 JavaScript/TypeScript 创建并铸造 SPL 代币

---

## 💻 完整代码

```typescript
import {
    Connection,
    Keypair,
    clusterApiUrl,
} from '@solana/web3.js';
import {
    createMint,
    getOrCreateAssociatedTokenAccount,
    mintTo,
} from '@solana/spl-token';

async function main() {
    // 1. 连接到 devnet
    const connection = new Connection(clusterApiUrl('devnet'), 'confirmed');
    
    // 2. 创建付款者钱包
    const payer = Keypair.generate();
    
    // 空投 SOL 用于支付费用
    const airdropSig = await connection.requestAirdrop(
        payer.publicKey,
        2 * 1e9 // 2 SOL
    );
    await connection.confirmTransaction(airdropSig);
    
    // 3. 创建代币 Mint
    const mint = await createMint(
        connection,
        payer,           // 付款者
        payer.publicKey, // 铸造权限
        null,            // 冻结权限 (null = 无)
        9                // 小数位
    );
    console.log('Mint created:', mint.toBase58());
    
    // 4. 创建 Token 账户
    const tokenAccount = await getOrCreateAssociatedTokenAccount(
        connection,
        payer,
        mint,
        payer.publicKey
    );
    console.log('Token account:', tokenAccount.address.toBase58());
    
    // 5. 铸造代币
    await mintTo(
        connection,
        payer,
        mint,
        tokenAccount.address,
        payer,           // 铸造权限
        1_000_000_000    // 1 token (9位小数)
    );
    console.log('Minted 1 token!');
}

main().catch(console.error);
```

---

## 🔑 关键点

| 函数 | 作用 |
|------|------|
| `createMint` | 创建代币铸币厂 |
| `getOrCreateAssociatedTokenAccount` | 获取/创建用户代币账户 |
| `mintTo` | 铸造代币到账户 |

---

## ✅ 运行方式

```bash
# 安装依赖
npm install @solana/web3.js @solana/spl-token

# 运行脚本
npx ts-node mint-token.ts
```

---

**制作人**：bruceCao  
**难度**：⭐（入门）
