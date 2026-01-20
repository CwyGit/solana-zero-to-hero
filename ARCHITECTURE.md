# 🏗️ Solana 中文开发者训练营 - 仓库架构设计

## 📍 仓库位置

```
H:\c_to_rust2026\Solana技术训练营-莫式风格\solana-chinese-bootcamp\
```

> [!NOTE]
> 这是一个独立的 GitHub 仓库目录，与原有项目分离，便于独立管理和发布。

---

## 📁 目录架构

```
solana-chinese-bootcamp/
│
├── 📄 README.md                    # 项目介绍 (中英双语)
├── 📄 README_EN.md                 # 英文版 README
├── 📄 LICENSE                      # CC BY-NC-SA 4.0
├── 📄 CONTRIBUTING.md              # 贡献指南
├── 📄 CODE_OF_CONDUCT.md           # 行为准则
├── 📄 CHANGELOG.md                 # 更新日志
│
├── 📁 .github/                     # GitHub 配置
│   ├── 📁 ISSUE_TEMPLATE/
│   │   ├── bug_report.md
│   │   └── feature_request.md
│   ├── 📄 PULL_REQUEST_TEMPLATE.md
│   └── 📁 workflows/               # CI/CD
│       └── link-check.yml
│
├── 📁 assets/                      # 品牌资源
│   ├── 📁 logo/
│   │   ├── logo.png
│   │   └── logo.svg
│   ├── 📁 banners/
│   │   └── banner.png
│   └── 📁 diagrams/                # 架构图等
│
├── 📁 courses/                     # 📚 课程内容
│   ├── 📄 README.md                # 课程索引
│   │
│   ├── 📁 01-blockchain-and-solana/
│   │   ├── 📄 README.md
│   │   ├── 01-什么是区块链.md
│   │   ├── 02-区块链演进历程.md
│   │   └── ...
│   │
│   ├── 📁 02-tokens-on-solana/
│   ├── 📁 03-nfts-on-solana/
│   ├── 📁 04-anchor-for-dummies/
│   ├── 📁 05-spl-token-with-anchor/
│   ├── 📁 06-pinocchio-for-dummies/
│   ├── 📁 07-testing-with-mollusk/
│   ├── 📁 08-testing-with-litesvm/
│   ├── 📁 09-testing-with-surfpool/
│   ├── 📁 10-program-security/
│   ├── 📁 11-token-2022-program/
│   ├── 📁 12-token-2022-with-web3js/
│   ├── 📁 13-token-2022-with-anchor/
│   ├── 📁 14-spl-token-with-web3js/
│   ├── 📁 15-secp256r1-on-solana/
│   ├── 📁 16-winternitz-signatures/
│   ├── 📁 17-introduction-to-assembly/
│   ├── 📁 18-instruction-introspection/
│   ├── 📁 19-create-sdk-with-codama/
│   └── 📁 20-solana-pay/
│
├── 📁 challenges/                  # 🎯 挑战内容
│   ├── 📄 README.md                # 挑战索引
│   │
│   ├── 📁 01-anchor-vault/
│   │   ├── 📄 README.md            # 挑战说明
│   │   ├── 📄 CHALLENGE.md         # 挑战详情
│   │   └── 📁 solution/            # 参考答案
│   │
│   ├── 📁 02-anchor-escrow/
│   ├── 📁 03-anchor-flash-loan/
│   ├── 📁 04-anchor-memo/
│   ├── 📁 05-pinocchio-vault/
│   ├── 📁 06-pinocchio-escrow/
│   ├── 📁 07-pinocchio-flash-loan/
│   ├── 📁 08-pinocchio-memo/
│   ├── 📁 09-pinocchio-secp256r1-vault/
│   ├── 📁 10-pinocchio-quantum-vault/
│   ├── 📁 11-pinocchio-amm/
│   ├── 📁 12-assembly-memo/
│   ├── 📁 13-assembly-timeout/
│   ├── 📁 14-assembly-slippage/
│   └── 📁 15-typescript-mint-spl-token/
│
└── 📁 docs/                        # 补充文档
    ├── 📄 learning-path.md         # 学习路径
    ├── 📄 faq.md                   # 常见问题
    ├── 📄 environment-setup.md     # 环境配置
    └── 📄 glossary.md              # 术语表
```

---

## 🎨 命名规范

### 目录命名
- **格式**: `XX-short-name-in-english`
- **示例**: `01-blockchain-and-solana`, `05-pinocchio-vault`
- 数字前缀便于排序，英文名便于 URL 引用

### 文件命名
- **课程章节**: `01-章节标题.md`, `02-章节标题.md`
- **挑战说明**: `README.md` (概览) + `CHALLENGE.md` (详细)

---

## 📊 内容来源映射

| 原目录 | 新目录 |
|--------|--------|
| `courses (课程)/01-区块链与Solana入门-blockchain-and-solana` | `courses/01-blockchain-and-solana` |
| `courses (课程)/02-Solana代币-tokens-on-solana` | `courses/02-tokens-on-solana` |
| `challenges (挑战)/01-anchor-escrow (Anchor托管)` | `challenges/02-anchor-escrow` |
| ... | ... |

---

## ❓ 待确认事项

1. **仓库名称**: `solana-chinese-bootcamp` 是否满意？
2. **是否包含代码解决方案**: 挑战的 `solution/` 目录是否公开？
3. **是否需要 GitHub Pages**: 可以自动生成文档网站

---

*架构设计完成后，继续执行内容复制和整理*
