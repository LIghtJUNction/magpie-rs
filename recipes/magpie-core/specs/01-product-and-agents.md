# 01 · 产品入口与 Agent 集成

## 产品对象

magpie 是一个管理本机多种 AI 编程 Agent 的应用。它读取已安装 Agent 的状态，把 Provider 的模型呈现在统一列表里，并将用户选中的模型写回相应 Agent。它也可把 Agent 接到本机 Gateway，以便 Agent 通过同一模型目录请求不同供应商。Agent 页面、托盘快速面板、浏览器界面和 TUI 呈现同一套设置；各入口的可操作范围见 `08-cli-tui-and-web.md`。

## REQ-010：多个入口反映同一状态

- 优先级：核心。证据：README、CLI 帮助、`assets/ui/agents-*`、`assets/ui/panel-*`。
- 提供桌面窗口、系统托盘/菜单栏快速面板、浏览器 UI、TUI 和 CLI。用户在任一入口修改 Provider、Agent 模型、Profile 或路由配置后，其他入口刷新时看到同一状态。
- 桌面端提供「打开 magpie」、刷新、设置、退出、显示/隐藏面板和开机启动入口。面板是紧凑的 Agent 行列表，可选模型/字段，支持 Profile、打开主窗口和退出。
- 若只有 TUI/服务进程在运行，它承担 Gateway 服务；该进程退出后向它连接的 Agent 明确说明服务不可用。
- 正常验收：CLI 设置 Codex 模型后，Agent 页面和面板显示新值。
- 失败验收：GUI 后端暂时不可达时，不显示成功保存；刷新后以实际保存状态为准。

## REQ-011：发现和列出 Agent

- 优先级：核心。Agent 目录来自本文件；只能将检测结果标为已安装/已连接。
- 扫描平台默认配置位置及 Agent 自己提供的环境变量/配置路径。列表中显示 Agent 名称、连接状态、当前模型、支持字段以及其专属模型列表入口。
- 用户可重新扫描、查看单个 Agent 状态、把列表中的 Agent 接入或断开。仅支持集成或仅提供导入链接的 Agent，要在 UI/CLI 中说明其能力边界。
- 当前基线的 Agent 名称包括：Claude Code、Claude Desktop、Claude Science、Codex、Gemini CLI、Antigravity CLI、OpenCode、OpenChamber、MiMo Code、DevEco Code、Pi、oh-my-pi、StepCode、MothX、PenguinHarness、Aside、OmO、Prime Agent、Goose、Cursor CLI、Cursor Private Inference、Zed、VS Code Chat、VS Code Insiders Chat、VSCodium Chat、Zoo Code、JetBrains Air、Copilot (JetBrains)、Copilot CLI、Crush、DeepSeek Harness、Reasonix Studio、Command Code、fx、Devin、Hermes Agent、Mister Morph、Kimi Code、jcode、Qwen Code、Muse Code、Empryo、Ante、AstrBot、MiniMax Code、Droid (Factory)、Cline CLI、Qoder CLI、Qoder CN CLI、Grok Build、Grok App、ZCode、WorkBuddy、WorkBuddy AI、CodeBuddy Code、Raycast、Kiro Crew、OpenPencil、NewMax、T3 Code、OpenHanako、AtomCode、Snow CLI、Alma、Pencil、Cindy、Lody、AionUi。
- 正常验收：空白新机器不显示未安装 Agent；安装一个受支持 Agent 并建立配置后，重新扫描出现对应行。
- 失败验收：路径不存在、配置损坏或权限不足不等价于 Agent 未安装；显示区别和可修复路径。

## REQ-012：兼容 Agent 的公开配置格式

- 优先级：核心。Agent 文件路径、字段和原生选择器是其应用间兼容边界。按操作系统、Agent 版本和该 Agent 的环境变量选择实际文件；优先遵循 Agent 本身的目录变量。
- 至少兼容下面这些主要配置。当前默认路径在表中给出；`~` 代表用户配置目录，Windows 上须映射到该 App 的真实用户目录。

| Agent | 当前基线使用的配置入口 | 由 magpie 管理的字段或方式 |
| --- | --- | --- |
| Claude Code | `~/.claude/settings.json` | Provider、模型、Opus/Sonnet/Haiku/Fable tier、Auto 模式；订阅登录可以由 magpie 发起 |
| Claude Desktop | macOS `~/Library/Application Support/Claude/` 和 `Claude-3p/configLibrary/`；Linux `~/.config/Claude/`；Windows `%LOCALAPPDATA%` 对应数据目录或 MSIX 包数据 | Gateway Provider、tier、subagent 模型/effort；MSIX 和 Desktop 版本会改变真实目录 |
| Codex | `~/.codex/config.toml` | Provider、模型、effort |
| Gemini CLI | `~/.gemini/settings.json`、`~/.gemini/.env` | 认证和模型 |
| OpenCode | `~/.config/opencode/opencode.json` 或 `.jsonc`；可由 `OPENCODE_CONFIG_DIR` 改目录 | 模型、small 模型 |
| OpenChamber / MiMo Code / DevEco Code | 各自的 `preferences.json` / `mimocode.jsonc` / `deveco.jsonc`；DevEco 可用 `DEVECO_CONFIG_DIR` | 模型和 small 模型；通常复用 OpenCode Provider |
| Pi | `~/.pi/agent/settings.json` | 模型 |
| StepCode | `~/.stepcode/config.toml`、`models.json`；`STEP_CODING_AGENT_DIR` 或 `STEPCODE_CONFIG_DIR` | 默认 Provider 与模型 |
| MothX | `~/.mothx/settings.json` 或 `MOTHX_DIR` | Provider、模型、thinking level |
| PenguinHarness | `~/.penguin/data/default_project/.project_config.toml`；`PENGUIN_HOME` / `PENGUIN_PROJECT_ID` | Provider 和默认模型 |
| Aside | `~/.aside/u/0/settings.json`、`models.json` | 模型及 effort 档位 |
| OmO / Prime Agent | `~/.omo/agent/settings.json`、`~/.prime/agent/settings.json` 及各自模型目录变量 | 默认模型；Prime 还管理 thinking |
| Goose / Cursor CLI / Zed | `~/.config/goose/config.yaml` / `~/.cursor/cli-config.json` / `~/.config/zed/settings.json` | 模型；Zed 使用自己的 Provider 配置格式 |
| VS Code Chat / Insiders / VSCodium Chat | 各自 `User/settings.json` 与 `chatLanguageModels.json`，并处理独立用户 Profile | Chat 默认模型、magpie Custom Endpoint 和 Provider 模型；保留其它 Profile |
| Zoo Code | VS Code 用户设置及 Zoo Code 自己的 import 文件 | OpenAI-compatible Provider，由 Zoo Code 启动时导入 |
| Copilot JetBrains | GitHub Copilot language-server 的 `byok.json` | 添加/移除 magpie BYOK Provider 的专属字段；不覆盖用户 Provider |
| JetBrains Air | `acp.json` 和相邻的 magpie OpenCode 配置 | 通过 Agent Client Protocol 把模型菜单接至 magpie |
| Copilot CLI / Crush / DeepSeek Harness | 各自的 settings/profile/provider 文件 | 模型、大小模型或 effort；DeepSeek Harness 还需其 Gateway key 配置 |
| Reasonix Studio | `~/.reasonix/config.toml` 与 `.env` | Executor、Planner、effort |
| Command Code / fx / oh-my-pi / Devin | 各自的 settings、provider 或模型文件 | 模型；fx 使用 keyless magpie Provider |
| Hermes Agent / Mister Morph / Kimi Code / Qwen Code / Muse Code | 各自 config；按产品自有目录变量解析 | 模型；按 Agent 规则设置 Responses、OpenAI-compatible 或原生 Provider 项 |
| Empryo / Ante / AstrBot / jcode | 各自的 JSON/TOML Provider 配置；AstrBot 支持 `$ASTRBOT_ROOT` | 默认模型或 Provider；AstrBot Docker 安装不属于本机发现 |
| MiniMax Code / Droid / Cline / Qoder / Qoder CN | 各自模型或自定义 Provider 配置 | 模型和适用的 effort；需 Agent 计划支持 BYOK 时提示该前提 |
| Grok Build / Grok App | 各自 TOML 和 App 数据目录；Grok App 可由 `$GROK_APP_HOME` 指定 | 模型、effort 或自定义 Provider；断开时恢复原路由，保留用户在 App 中改过的模型 |
| ZCode / WorkBuddy / WorkBuddy AI / CodeBuddy Code | 各自 Provider/model JSON；按各自目录变量解析 | Provider、模型或当前会话模型 |
| Raycast / Kiro Crew / OpenPencil / NewMax | Raycast YAML、Kiro Crew JSON、OpenPencil settings、NewMax settings 数据 | 添加 magpie Provider；保留其他项。OpenPencil/NewMax 正在运行时拒绝冲突写入 |
| T3 Code / OpenHanako / AtomCode / Snow CLI | 各自的 settings/catalog/API 入口 | 模型、effort 或模型 Profile；仅兼容本机可访问入口 |
| Alma | Alma 本机 API，默认 loopback `23001` | 默认模型及自动模式下的图片生成模型 |
| Lody / AionUi / Pencil / Cindy | 应用自己的导入/添加 Provider 确认流程 | 仅按其公开导入契约添加；不能把它们假装成直接改写配置文件的集成 |

- 若 Agent 对模型名的格式有额外要求（`provider/model`、内部 Provider id、模型家族名或专用目录），必须输出 Agent 能读的原生结构，并让 Usage 仍能识别调用来源。
- 正常验收：Codex 的 TOML 配置、Claude Code 的 JSON 配置和 OpenCode 的 JSONC 配置都可接入；每个 Agent 的模型在其原生 `/model` 或模型选择器中可用。
- 失败验收：目标格式含未知字段时保留它们；未知字段不能因 round-trip 被删除。

## REQ-013：只做精确、可恢复的字段编辑

- 优先级：核心。只更改用户明确操作的字段及为了连接所必需的网关字段。
- 保留注释、排序、缩进、用户自定义 Header/Provider、插件设置、非目标字段和文件格式。可在不影响兼容性的情况下格式化，但验收以所有原值均保留为准。
- 先备份或记住原字段。写入采用原子替换；检测文件在读取后被第三方改动时重新读取合并或拒绝，不用旧快照覆盖较新的用户值。
- 用户保存的值优先于 magpie 默认。用户设置的模型或环境变量不得被默认连接设置覆盖。
- 正常验收：在含注释、重复 key、未知扩展字段的配置中仅改模型，其他内容保留。
- 失败验收：解析错误、不可读、只读、路径不安全或文件被 Agent 锁定时，显示失败并保留原内容。

## REQ-014：接入、漂移和断开

- 优先级：核心。
- Connect 为目标 Agent 写入 magpie Provider/模型目录和网关地址，按 Agent 要求提示重启或开始新会话。Disconnect 仅撤销由 magpie 写入的字段并恢复先前值；第三方已经修改的用户字段必须保留。
- 界面可比较 Agent 当前值与 magpie 最近写入值，显示 drift 状态；用户可选择重新应用自己的设置或保留 Agent 当前值。
- 多账户/多 Profile Agent 应分别处理各自配置，不能把一个 Profile 的设置误写到其它 Profile。
- 正常验收：连接 Codex、用户手工改一个无关 Header、再断开，Header 不丢失，原模型恢复。
- 失败验收：Agent 进程正在覆盖文件时，连接操作等待有界时间或拒绝，不能以部分写入结束。

## REQ-015：Agent 专属字段和 Profile 保留

- 优先级：一般。
- 除模型外，仅展示目标 Agent 确实支持的字段，例如 reasoning effort、Claude tier/fast/auto、small/large/subagent model、Pi thinking 等。不得把一个 Agent 的字段错误显示为所有 Agent 的公共字段。
- 每个字段的可选值、默认值、显示名与 Provider 能力匹配。对 `default`/恢复操作，退回 Agent 自己的默认值，并把 magpie 写入的旧值移除。
- Profile 同时保存 Agent 专属字段、连接/模型选择与可见性；恢复值应经过目标 Agent 能力检查。
- 正常验收：Codex 的 effort 字段只出现其有效等级；关闭 Provider 后不留已失效选择。
- 失败验收：Agent 升级后字段不再受支持时显示不兼容状态并保留备份，不写入猜测值。

## REQ-016：跨 Agent 模型目录

- 优先级：核心。
- Agent 的模型列表从已启用的 Provider 和路由组生成；显示可读名称、Provider、模型能力/effort、上下文窗口和价格（有数据时）。支持搜索及按 Provider/家族/组筛选。
- 用户选择后立即写入对应 Agent 设置；新模型目录刷新后可以在下次刷新/打开时出现。不同 Agent 仅显示自身能调用的协议/功能。
- 正常验收：在同一个模型列表中选择不同 Provider 的两个模型，两个 Agent 原生配置分别更新。
- 边界验收：模型名相同但来自不同 Provider 时仍可区分；明确的 Agent 自定义模型名保持原样。

## REQ-017：每 Agent 模型可见性

- 优先级：一般。
- 支持从单个 Agent 的目录隐藏某模型、Provider、模型家族或路由组；另有仅显示用户挑选项的模式。隐藏是 Picker/列表过滤，不得阻止显式模型 ID 请求。
- 开启“仅显示挑选项”时，现有选择成为准入集合；之后出现的新模型默认不进入该 Agent，用户逐项允许后才显示。`--show-new` 恢复新模型自动显示。
- 正常验收：Codex 隐藏 `vendor/model-a`，其列表不显示它，另一个 Agent 仍有它；显式请求该模型仍成功。
- 失败验收：无法读取可见性设置时，保留上次有效集合并提示错误，不把所有模型误设为可见或全部隐藏。

## REQ-018：保存、应用和删除 Profile

- 优先级：一般。
- 一条 Profile 是用户命名的一组 Agent 设置快照；切换时批量应用。GUI 显示当前应用 Profile，CLI `profiles` 与托盘显示相同状态。
- 应用前计算变更；若部分 Agent 文件不允许写，展示失败的 Agent 和恢复状态，不谎报全部成功。用户可保存当前状态、覆盖同名 Profile、应用和删除。
- 正常验收：保存 `Work`，切换两个 Agent 的模型后应用，两个模型均恢复。
- 边界验收：Profile 中某 Agent 已卸载时，其余 Agent 仍可应用；缺失 Agent 设置保留在 Profile 里，重装后可恢复。

## REQ-019：变化重扫和状态反馈

- 优先级：一般。
- 对受支持文件的外部改动，重新读取实际值并更新 Agent 列表，显示已连接、断开、配置不一致或不可读取的状态。刷新按钮提供手动重扫。
- 每项保存反馈说明写入是否成功、何时生效及是否需重启。异步更新期间展示加载/错误/成功状态。
- 正常验收：用户在 Codex 自己的编辑器改模型后刷新，magpie 显示该模型。
- 失败验收：重扫期间文件短暂不完整时保留最后可解析值，再重试；不得显示伪造的默认模型。
