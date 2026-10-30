# Magpie Rust

![软件与依赖汇聚为可重建的配方](https://raw.githubusercontent.com/recipkg/recipkg/main/assets/recipkg-cover.png)

Rust 重建版 Magpie：独立 CLI、本地网关与 GPUI 桌面界面。当前为早期版本。配方 **1.4.0** 固定对齐原版 **0.1.2185**，保留原有 93 条需求并新增 6 条，共 **99 条**；逐条状态见 [覆盖索引](recipes/magpie/COVERAGE.md)，官方 237 条发布记录的完整映射见 [更新日志对照](recipes/magpie/UPSTREAM_CHANGELOG.md)。

另按固定源码补录 [17 族、69 条 Gateway 路由注册](recipes/magpie-gateway/UPSTREAM_ROUTES.md)，明确别名、方法、权限和待采集的响应形态。该表登记原版目标，当前实现状态仍以逐条覆盖索引为准。

## 安装

本提交是开发快照，AUR 与多平台安装包尚未发布。本轮在共享 Provider、系统凭据与模型发现的基础上，增加桌面 Gateway 启停、已接入 Codex 直接换模和最新 Chat 参数兼容；用户视觉认可与完整产品能力仍在推进。正式发布后可从 [Releases](https://github.com/LIghtJUNction/magpie-rs/releases) 下载对应系统与 CPU 的安装包并核验 SHA-256。Linux、macOS、Windows 的 CLI 和桌面支持范围以每次发布说明为准；Android 制品提供 CLI，不含 GPUI APK。

Arch Linux 计划提供：`paru -S magpie-rs-bin` 安装发布二进制，或 `paru -S magpie-rs-git` 从源码构建。两个包都提供 `magpie` 命令，并与原版 `magpie`、`magpie-git`、`magpie-bin` 互斥。包替换保留用户配置。

```sh
magpie --help
magpie providers --help
magpie gui
magpie agents --json
magpie models --json
magpie serve --listen 127.0.0.1:3425
```

桌面与 CLI 使用同一份 Provider 配置；在 Providers 中添加服务后可刷新真实模型列表。运行中的 Gateway 读取最新配置，支持按 `provider/model` 区分上游，编辑、启停和密钥轮换影响后续请求。直接输入的密钥进入系统凭据存储，公开配置只保留引用。Linux 构建需要 D-Bus 开发库，直接保存密钥需要可用的 Secret Service；headless 环境可主动选择环境变量引用。

桌面默认启动自己拥有的 Gateway；Gateway 页显示服务端实际版本和地址，已有外部服务仅可查看。停止或桌面异常退出会释放自有进程，不能误停独立 `serve`。桌面监听地址作为本机偏好保存，CLI 仍使用显式 `--listen`；改变地址不自动覆写 Agent 配置。已接入的 Codex 可以直接选另一个模型、预览并保存，多次换模后断开仍恢复首次接入前的配置。

当前每个 Provider 选择 OpenAI Chat、OpenAI Responses、Anthropic Messages 或 Gemini 协议；四协议转换和 SSE 有隔离回环测试。原版预设与订阅登录、多账户、完整路由/Usage/插件/同步和其他 Agent 配置写入仍未齐备。新增的 MCP Hub、Responses 续接、TTS、只读 key、会话读取许可和 WebSocket 也继续明确列为未实现。运行方式、验证与边界见[本轮实现记录](https://github.com/LIghtJUNction/magpie-rs-source/blob/9ff54775bd3aa0c7b9436e06f0c281fa5a7f6166/docs/changelog-parity-2026-10-30.md)及[验证证据](recipes/magpie/EVIDENCE.md)。

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

本轮配方的递归归档、完整提示词和 LOCK 验证结果见 [配方 1.4.0 发布验证](verification/recipe-1.4.0.md)。
