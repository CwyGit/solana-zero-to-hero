# 🛠️ Solana/Anchor 开发环境搭建指南

> 基于 Windows + WSL Ubuntu 24.04 的完整踩坑记录

---

## 📋 最终工作环境

| 组件 | 版本 | 说明 |
|------|------|------|
| WSL | Ubuntu 24.04 | Windows子系统Linux |
| Rust | 1.92.0 | 系统Rust |
| Solana CLI | 3.0.13 | Agave客户端 |
| Platform-tools | v1.51 | 内置 Rust 1.84.1 |
| Anchor CLI | 0.32.1 | 通过AVM安装 |
| AVM | latest | Anchor版本管理器 |

---

## 🚨 关键问题与解决方案

### 问题1：Rust版本不兼容

**症状**：
```
error: could not compile `typenum` (build script)
error: indexmap@2.13.0 requires rustc 1.82
```

**原因**：
- SBF工具链使用旧版平台工具（1.79.0）
- 新版依赖（如 indexmap 2.13.0）需要 Rust 1.82+

**解决方案**：
```bash
# 使用完整路径调用 cargo-build-sbf
/home/bruce/.local/share/solana/install/active_release/bin/cargo-build-sbf
```
这会自动使用 platform-tools v1.51（内置 Rust 1.84.1）。

---

### 问题2：Anchor版本不匹配

**症状**：
```
WARNING: `anchor-lang` version(0.31.1) and the current CLI version(0.32.1) don't match.
```

**解决方案**：
在 `Cargo.toml` 中保持版本一致：
```toml
[dependencies]
anchor-lang = "0.32.1"  # 与CLI版本一致
```

在 `Anchor.toml` 中指定版本：
```toml
[toolchain]
channel = "1.79.0"
anchor_version = "0.32.1"
```

---

### 问题3：WSL PATH问题

**症状**：
```
bash: anchor: command not found
bash: solana: command not found
```

**解决方案**：
在 `~/.bashrc` 中添加：
```bash
export PATH="$HOME/.avm/bin:$HOME/.local/share/solana/install/active_release/bin:$HOME/.cargo/bin:$PATH"
```

或在命令行中临时设置：
```bash
export PATH=/home/bruce/.avm/bin:/home/bruce/.local/share/solana/install/active_release/bin:/home/bruce/.cargo/bin:$PATH
```

---

## 🔧 完整安装流程

### Step 1: 安装 Rust
```bash
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
source ~/.cargo/env
rustc --version  # 验证
```

### Step 2: 安装 Solana CLI
```bash
sh -c "$(curl -sSfL https://release.anza.xyz/stable/install)"
# 添加到 PATH
export PATH="$HOME/.local/share/solana/install/active_release/bin:$PATH"
solana --version  # 验证
```

### Step 3: 安装 AVM 和 Anchor
```bash
cargo install --git https://github.com/coral-xyz/anchor avm --force
avm install latest
avm use latest
anchor --version  # 验证
```

### Step 4: 验证完整环境
```bash
rustc --version
solana --version
anchor --version
cargo build-sbf --version
```

---

## 📦 项目构建命令

### 标准构建流程
```bash
cd /mnt/h/your/project/path

# 方法1: 使用 anchor build
anchor build

# 方法2: 直接使用 cargo-build-sbf（推荐，更可靠）
/home/bruce/.local/share/solana/install/active_release/bin/cargo-build-sbf \
  --sbf-out-dir ./target/deploy
```

### 从 PowerShell 调用 WSL 构建
```powershell
wsl -d Ubuntu-24.04 -- bash -lc "cd /mnt/h/项目路径 && /home/bruce/.local/share/solana/install/active_release/bin/cargo-build-sbf"
```

---

## 📁 Anchor 项目结构

```
project/
├── Anchor.toml              # Anchor 配置
├── Cargo.toml               # 工作空间配置
├── programs/
│   └── program_name/
│       ├── Cargo.toml       # 程序依赖
│       └── src/
│           └── lib.rs       # 主程序代码
└── target/
    └── deploy/
        └── program_name.so  # 编译输出
```

---

## ⚠️ 常见陷阱

| 陷阱 | 原因 | 解决 |
|------|------|------|
| `command not found` | PATH未设置 | 使用完整路径或更新~/.bashrc |
| 版本不兼容 | SBF工具链过旧 | 使用最新Solana CLI |
| IDL脚本失败 | 缺少系统工具 | 安装 `build-essential coreutils` |
| 重复安装失败 | 缓存冲突 | 删除 `~/.cargo/.package-cache` |

---

## 🚀 快速构建脚本

创建 `build.sh`：
```bash
#!/bin/bash
export PATH=/home/bruce/.avm/bin:/home/bruce/.local/share/solana/install/active_release/bin:/home/bruce/.cargo/bin:$PATH

PROJECT_DIR=$1
if [ -z "$PROJECT_DIR" ]; then
    echo "Usage: ./build.sh <project_path>"
    exit 1
fi

cd "$PROJECT_DIR"
cargo-build-sbf --sbf-out-dir ./target/deploy
echo "Build complete! Output: $PROJECT_DIR/target/deploy/*.so"
```

---

## 📝 提交到 Blueshift

1. 构建完成后找到 `.so` 文件
2. 访问 https://learn.blueshift.gg/zh-CN/challenges/
3. 选择挑战 → "Take Challenge"
4. 上传 `.so` 文件
5. 等待自动测试结果

---

**制作人**：bruceCao  
**最后更新**：2026年1月22日  
**环境验证**：Anchor Vault 挑战构建成功 ✅
