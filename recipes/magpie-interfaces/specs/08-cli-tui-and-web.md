# 08 · Desktop、Web、TUI 与 CLI

## REQ-080：运行形态

- 优先级：核心。macOS、Windows、Linux 支持完整桌面 App（独立窗口、托盘/菜单栏快捷面板）和 Gateway。提供无需 GUI 的 CLI、TUI、浏览器 Web UI、headless/服务模式及 Docker 镜像。
- GUI 与 `magpie web` 使用同一用户数据、页面/API 与 Gateway 状态；`magpie serve` 仅启动 Gateway，不意外拉起 GUI。`magpie` 无参数正常进入 GUI（可用安装包/启动器运行）。
- App 同一用户只运行一个实例，托盘/菜单栏显示 Gateway 状态；系统 sleep、退出/重启时停服务，并按配置 launch-at-login。
- 正常验收：Linux 无桌面环境运行 `magpie web` 或 TUI，可操作同一 gateway/provider 配置。
- 失败验收：页面加载失败或 GUI 崩溃不损坏后端文件；服务退出时客户端收到明确状态。

## REQ-081：桌面导航和设置

- 优先级：核心。主导航提供 Agents、Providers/Subscriptions、Models、Routing、Usage、Sessions、Library、Plugins、Tools、Settings 等产品能力。根据当前服务可用性展示空态、加载、错误、无权限和离线状态。
- Settings 有清晰分页/分组，搜索可定位 provider/gateway/privacy/theme/sync 等选项；修改后反馈是否已保存、是否要重启 Agent 或重新加载页面。
- tray 快捷面板显示可用额度/服务状态和常用入口，关闭快捷 panel 不终止 Gateway；全窗布局在窄屏下转为抽屉/可折叠导航。
- 正常验收：改一个 Gateway 设置，页面 API 和本地 CLI 立即读到相同结果。
- 失败验收：API 操作失败保持当前编辑值/显示错误，不把暂存值作为已保存成功。

## REQ-082：浏览器 Web UI

- 优先级：核心。`magpie web` 在本机默认 `http://127.0.0.1:3430` 提供页面，能无桌面地维护 providers、agents、routing、usage、settings、backup 等。
- 支持 reverse proxy / HTTPS 部署，登录状态使用独立 Web key/短生命周期 session cookie；对跨源 API、WebSocket、cookie/SameSite、CSRF、Origin/Host 做一致授权检查。
- Web UI 依赖 Gateway/HTTP API，路由可访问但尚未准备好的页面应说明服务未启动/不支持，不能卡无限加载。
- 正常验收：从登录后的手机浏览器连接同一 LAN 上显式开启的 Web UI，用专属 key 完成登录和只读查看。
- 失败验收：没有 web key 的未授权来源无法通过 API、资源 URI 或 WebSocket 读取私有数据。

## REQ-083：TUI

- 优先级：一般。终端界面可看 Provider、accounts、models、routes/groups、usage/quota 和系统状态；支持表格/详情/刷新/添加/删除等能力。
- 窄终端保持可读，长值换行或横向滚动；键盘操作和 help 清晰，非交互标准输入时 CLI 命令仍可用。
- TUI 与 GUI/CLI 调用相同 API/domain logic，成功编辑后不用单独的第二份缓存来冒充持久化。
- 正常验收：增加 Provider 后从 GUI 重新进入能看到同一 Provider。
- 失败验收：quota 请求失败时显示最近更新时间和错误而不清除已知状态。

## REQ-084：CLI 接口

- 优先级：核心。提供可预测、有 `--help` 的命令行入口，用于：
  - Providers/presets/account 登录、模型目录、刷新、key/header、启停、测试；
  - models/groups 的查询、增删改、权重、rules、routing/stays；
  - agents 状态/安装/更新/模型/连接/启动；
  - usage、quota refresh、会话/route 检查、搜索和 `tool`/MCP 测试；
  - plugins 的查找/安装/更新/enable/disable/sign-in；
  - library instructions/skills/MCP/projects；
  - Gateway、Web、TUI、Settings、backup/restore/sync、诊断。
- 子命令、help 和退出码对用户稳定；纯 stdout 输出可管道处理。对破坏性更改提供清楚的命令目标与回显，敏感 token 参数不得回显/记入 shell history（优先从 stdin/secure prompt 接收）。
- 结构化输出遵循明确格式（如 `--json`、`--csv`），支持 non-interactive 操作和明确错误码；普通输出不混入进度条/日志。
- 正常验收：`magpie provider models deepseek --json` 输出有效模型 JSON，`magpie usage --csv` 输出筛选后的记录。
- 失败验收：缺参数/未知命令提示正确用法并返回非零状态，不改配置。

## REQ-085：Agent 状态、更新和配置流程

- 优先级：一般。Agents 页面列出全部受支持/探测到的编码 Agent、其 CLI 版本、安装来源/文件位置、连接状态、当前模型、安装/更新/重启方式、Wsl/不同 app instance 状态。
- 不需要的 Agent 可直接从模型清单隐藏；清单和 agent 路径不覆盖用户自定义配置。CLI 更新策略尊重原安装器；为无安装 Agent 显示官方安装方式。
- 在 Agent 配置应用前预览要改动的字段、原值和目标值。切换/断开后只恢复 Magpie 管理的字段。
- 正常验收：只装了 Claude Code CLI 的设备不显示一个可配置到另一台机器的“本地 Claude Code”。
- 失败验收：Agent 已卸载但遗留目录时，不向无程序目标写入不可读取设置。

## REQ-086：应用更新和可诊断错误

- 优先级：一般。展示版本号、当前更新频道、发布说明和可用升级；支持用户选择 auto/ask/manual 方式。升级失败保留当前版本并给出日志入口。
- 错误提示包含操作、目标、可恢复建议与支持所需 request id；提供诊断导出前隐藏密钥/token/个人正文。
- 用户可选择复制/查看运行日志；默认不在错误弹窗显示完整 HTTP authorization header。
- 正常验收：升级被网络/代码签名拒绝后仍能启动当前版本并查看失败理由。
- 失败验收：无网络时显示版本状态未知，不称为已升级到最新。
