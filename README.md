# Magpie Rust

Rust 重建版 Magpie：独立 CLI、本地网关与 GPUI 桌面界面。当前为早期版本，完整功能合同仍包含 93 条需求，实际状态见 [覆盖索引](recipes/magpie/COVERAGE.md)。

## 安装

本提交是开发快照，AUR 与多平台安装包尚未发布，UI 正在交给 Pro 重新设计。正式发布后可从 [Releases](https://github.com/LIghtJUNction/magpie-rs/releases) 下载对应系统与 CPU 的安装包并核验 SHA-256。Linux、macOS、Windows 的 CLI 和桌面支持范围以每次发布说明为准；Android 制品提供 CLI，不含 GPUI APK。

Arch Linux 计划提供：`paru -S magpie-rs-bin` 安装发布二进制，或 `paru -S magpie-rs-git` 从源码构建。两个包都提供 `magpie` 命令，并与原版 `magpie`、`magpie-git`、`magpie-bin` 互斥。包替换保留用户配置。

```sh
magpie --help
magpie gui
magpie agents --json
magpie models --catalog ./catalog.toml --json
magpie serve --catalog ./catalog.toml
```

静态模型目录可以列出 Provider/model ID。上游请求转发、登录、配置写入、Usage、插件和同步仍在开发中；请按发布说明判断当前版本适用范围。

## 项目布局

- `recipes/`：纯 Markdown 重建规范及递归依赖。
- `generated/magpie-rs-source/`：固定提交的 [独立 Rust 源码仓库](https://github.com/LIghtJUNction/magpie-rs-source) Git 子模块。
- `.agents/skills/`：固定提交的 Recipkg 共享技能子模块。

```sh
git clone --recurse-submodules https://github.com/LIghtJUNction/magpie-rs.git
cd magpie-rs/generated/magpie-rs-source
CARGO_TARGET_DIR=/tmp/magpie-rs-build cargo build --release --locked -p magpie-cli
```

[性能合同与实测](recipes/magpie/PERFORMANCE.md)记录负载、机器、资源占用及未验证项。Release、AUR 发布和设备运行分别记录证据。
