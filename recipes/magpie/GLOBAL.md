# magpie 功能重建提示词

## 1. 任务

仅凭本提示词包，实现一个可运行的软件，重建 **magpie** 在源码基线 `7b63feb908da1ca20f446b6b8584441e1c934825` 中可观察到的产品功能。产品将多种 AI 编程 Agent、Provider、订阅账号和模型集中管理；它会按用户选择安全地配置 Agent，并提供一个兼容多个模型 API 的本地网关、故障转移、用量与会话管理。

这是一份完整范围规格，不是 MVP。本文件和各递归依赖的 `specs/` 中的全部要求都属于目标范围；“核心/一般/细节”只表示实现顺序，不能据此省略功能。随包的文字视觉参考用于理解界面状态，不替代行为规格。

## 2. 必须阅读的规格

| 顺序 | 路径 | 覆盖范围 |
| --- | --- | --- |
| 1 | `.deps/interfaces/.deps/gateway/.deps/core/specs/01-product-and-agents.md` | 产品对象、界面入口、Agent 目录和配置兼容 |
| 2 | `.deps/interfaces/.deps/gateway/.deps/core/specs/02-providers-and-accounts.md` | Provider、密钥、模型、订阅登录、额度 |
| 3 | `.deps/interfaces/.deps/gateway/specs/03-gateway-and-protocols.md` | 本地网关、API、翻译、认证和远程接入 |
| 4 | `.deps/interfaces/.deps/gateway/.deps/core/specs/04-routing-and-failover.md` | 路由组、规则、账户排序、重试、搜索与分类 |
| 5 | `.deps/interfaces/.deps/gateway/specs/05-usage-and-sessions.md` | 统计、请求、上下文、会话、导入与录制 |
| 6 | `.deps/interfaces/.deps/gateway/specs/06-plugins-and-tools.md` | 插件生命周期、扩展点、工具和迁移 |
| 7 | `.deps/interfaces/.deps/gateway/.deps/core/specs/07-library-sync-and-backup.md` | 指令库、MCP、技能、配置同步和备份 |
| 8 | `.deps/interfaces/specs/08-cli-tui-and-web.md` | 命令行、TUI、浏览器界面、配置入口 |
| 9 | `.deps/interfaces/.deps/gateway/.deps/core/specs/09-privacy-security-and-platforms.md` | 隐私、安全、运行方式、安装与更新 |
| 10 | `.deps/interfaces/specs/10-visual-and-localization.md` | 可观察页面、交互状态、截图和文字要求 |

以上规格及 `.deps/interfaces/UI_REFERENCE.md` 中的全部文字视觉参考共同组成完整任务。出现重复定义时，以对应功能规格中带 REQ 编号的条款为准；跨规格通用边界以本文件为准。

## 3. 范围和一致性

| 项目 | 目标 |
| --- | --- |
| 产品名称 | magpie |
| 入口 | 原生桌面窗口、系统托盘/菜单栏面板、浏览器 Web UI、TUI、CLI、本地 HTTP 网关 |
| 目标环境 | macOS、Windows、Linux；另有 headless、Docker、WSL 和 Termux 使用方式 |
| 人类用户 | 使用一个或多个代码 Agent、模型 Provider 或订阅账号，希望统一挑选模型、管理配置、路由和用量的人 |
| 一致性 | 在固定平台、Agent/Provider 版本和外部服务可用条件下，满足所有已确认的行为、公开协议、数据转换、界面状态与配置兼容要求 |
| 完成状态 | 可运行程序、可复现构建/启动说明、实际测试结果、每个 REQ 的实现状态和未完成项 |

原提示词允许任意实现；本次用户要求覆盖该自由度：主实现使用 Rust，桌面 GUI 使用 GPUI，CLI 独立可用，内存与本体尽可能精简。数据库、进程模型和内部目录仍由重建者选择。不得因此改变用户可观察规则。以下是必须兼容的外部边界：

1. 本包定义的 Agent 配置格式、文件语义、公共命令行为、模型 ID 约定和 HTTP API。
2. Agent 配置中用户已有值、格式和内容的保留规则。
3. Provider 原生 API、OpenAI Chat Completions、OpenAI Responses、Anthropic Messages、Gemini API，以及注明的音频接口。
4. `provider/model` 和 `group/<id>` 模型名；错误、流式输出、工具调用、reasoning 与计量结果要符合相应规格。
5. 社区插件、MCP 工具和已有同步/备份数据的外部兼容边界。仅因原版使用某种语言、库、数据库或目录名，不构成技术要求。

## 4. 共用名词

- **Agent**：第三方 CLI、桌面 App、IDE 插件或服务端程序；magpie 只管理被发现的 Agent。
- **Provider**：可产生模型回答的 API、订阅账户、插件或本地模型服务。
- **账户/密钥**：Provider 的一个可独立计额或轮换的登录或 API 凭据。不得把原始凭据写入用量记录。
- **模型**：由 Provider 提供的上游模型；在 magpie 中以 `provider/model` 表示。
- **路由组**：一组模型的可选统一别名，Agent 只需配置组名即可。
- **网关**：供本机和获准远程客户端使用的兼容 HTTP 服务；负责认证、选择模型、转发、适配协议和记录元数据。
- **本机值**：只应保存在当前机器上的网络分享、Web 登录密钥、原生应用路径、允许列表或本机交互录制同意。同步和备份恢复不得擅自打开它们。

## 5. 全局行为规则

### REQ-001：只显示可用 Agent

- 优先级：核心。
- 用户打开 Agent 列表时，显示本机已安装或已经配置、且其配置可以被识别的 Agent；未安装或未配置者不伪装成已连接。
- 已发现但暂时无法读取的配置必须展示为未知/错误，不能当作空配置。用户可以重新扫描、修复路径或断开集成。
- 正常验收：安装并配置 Codex 后，列表出现 Codex 和其当前模型；移除配置或取消连接后按该 Agent 的断开语义隐藏或标记未连接。
- 失败验收：配置文件不可读或格式损坏时，原配置不被覆盖，显示可操作错误。

### REQ-002：保持用户已有数据

- 优先级：核心。
- 除明确选择的模型/字段和本规格要求写入的网关连接字段外，不改变用户设置、注释、顺序、格式、密钥、路由和自定义 Provider。
- 修改前保存可恢复的原值；写入应具备原子性或等效的无半文件保证。读失败、解析失败、校验失败或写失败不能被解释为空值、默认值或成功。
- 默认不得覆盖用户手动配置的值。断开/改回 Agent 原生默认时，恢复被 magpie 替换的原值。
- 正常验收：只更换 Codex 的 `model`，其他 TOML 内容保持语义和值不变；重新格式化不是本软件要求。
- 边界验收：保存中断后，磁盘上仍是旧文件或完整新文件，不能留下截断配置。

### REQ-003：显式连接与断开

- 优先级：核心。
- 用户切换 Agent 的连接开关或执行 Connect 时，把 Agent 的模型/API 地址/认证改为可用的 magpie Provider/模型目录；如果 Agent 需要重启，清楚提示何时生效。
- Disconnect 恢复之前由 magpie 修改的字段；保留用户后来在其它字段上的修改。用户选了 Agent 自带默认值时，该 Agent 离开 magpie。
- 正常验收：连接一个支持 OpenAI 兼容 API 的 Agent 后，Agent 能看到 magpie 模型；断开后原来的 Provider 和模型恢复。
- 失败验收：目标应用正在写配置、格式不支持或字段冲突时，不覆盖文件，并解释修复步骤。

### REQ-004：统一模型选择和每 Agent 可见列表

- 优先级：核心。
- 已启用模型以 `provider/model` 唯一标识；选取后同步到所有适用 Agent 的原生选择项。
- 每个 Agent 可隐藏指定模型、Provider、模型家族或路由组。可启用“仅显示我选中的模型”：启用时当前选项成为允许列表，新发现的模型默认隐藏，用户明确选中后才出现；按名字直接指定的模型仍可被请求。
- 正常验收：某模型从 Codex 的列表中隐藏后，其它 Agent 仍可使用。
- 边界验收：Provider 上游移除了模型时，明确刷新后清理过期的显式选择；单次请求返回模型退役错误时按 `02` 中的保留规则处理。

### REQ-005：用户可保存和切换 Profile

- 优先级：一般。
- Profile 保存所有 Agent 的模型/字段设置和每 Agent 模型可见性。切换 Profile 在一次操作中恢复这组选择；当前正在应用的 Profile 有一致标记。
- 新建或覆盖 Profile 前显示名称及覆盖影响；删除 Profile 需明确确认。失败时不留下半应用状态。
- 正常验收：保存 Work，再切到 Focus，再切回 Work，Agent 显示原来的模型和字段。

## 6. 领域规格索引

每个子规格都必须完整实现，至少覆盖正常输入与适用的失败/边界情形：

- REQ-010—019：Agent 发现、配置编辑、模型列表和连接，见 `.deps/interfaces/.deps/gateway/.deps/core/specs/01-product-and-agents.md`。
- REQ-020—029：Provider、订阅账户、模型目录和额度，见 `.deps/interfaces/.deps/gateway/.deps/core/specs/02-providers-and-accounts.md`。
- REQ-030—039：Gateway API、协议适配、认证和远程部署，见 `.deps/interfaces/.deps/gateway/specs/03-gateway-and-protocols.md`。
- REQ-040—049：Routing、路由组、规则、账户挑选、重试和搜索，见 `.deps/interfaces/.deps/gateway/.deps/core/specs/04-routing-and-failover.md`。
- REQ-050—059：Usage、请求记录、统计、Session、转录和本地录制，见 `.deps/interfaces/.deps/gateway/specs/05-usage-and-sessions.md`。
- REQ-060—069：插件加载、生命周期、迁移、工具和插件 UI，见 `.deps/interfaces/.deps/gateway/specs/06-plugins-and-tools.md`。
- REQ-070—079：指令库、MCP/技能、跨机备份与同步，见 `.deps/interfaces/.deps/gateway/.deps/core/specs/07-library-sync-and-backup.md`。
- REQ-080—089：CLI、TUI、Web UI 和导入链接，见 `.deps/interfaces/specs/08-cli-tui-and-web.md`。
- REQ-090—099：隐私、网络边界、更新与平台，见 `.deps/interfaces/.deps/gateway/.deps/core/specs/09-privacy-security-and-platforms.md`。
- REQ-100—109：视觉、交互、文本和本地化，见 `.deps/interfaces/specs/10-visual-and-localization.md`。

## 7. 公开验收场景

这些示例是构建时公开样例，不是独立评估保留集。

### EXAMPLE-001：更换 Agent 模型

- 初始：Provider `deepseek` 已配置并有模型 `deepseek/deepseek-v4-pro`；Codex 已发现，当前模型为 `openai/gpt-6`。
- 操作：在 Agent 页面把 Codex 模型改成 `deepseek/deepseek-v4-pro`。
- 结果：Codex 的模型设置变成该模型，未变更字段保持原值；本地 Gateway 仍监听；Codex 下一次新会话使用 DeepSeek。

### EXAMPLE-002：一个用量窗口耗尽后自动回退

- 初始：同一订阅 Provider 有账户 A、B；路由组按可用额度排序；A 的模型请求返回已耗尽的 429，B 可回答。
- 操作：Agent 请求 `group/daily-coding`。
- 结果：A 被暂时跳过并留下失败尝试记录，B 回答；Agent 收到成功响应；最终请求在 Usage 中计为一次已回答的重试请求，而不是一次失败请求。

### EXAMPLE-003：局域网客户端权限

- 初始：网关只允许 loopback 请求；用户打开共享、创建有预算的命名 Gateway key、设置模型范围。
- 操作：远程客户端使用该 key 请求允许模型，再请求未授权模型。
- 结果：前者正常路由，后者在送到 Provider 前被拒绝。停止共享后远端请求无法再使用本机 Gateway。

### EXAMPLE-004：本地对话录制默认关闭

- 初始：新安装，未打开本地 Gateway 对话录制。
- 操作：通过 Gateway 完成一次有 Session ID 的生成。
- 结果：记录用量元数据，但不保存请求/回复文本。用户明确同意开启录制后，界面说明保存范围、最长保留期和本机存储位置；用户关闭并清理后文本被删除，Usage 元数据继续保留。

### EXAMPLE-005：保存并应用 Profile

- 初始：两个 Agent 使用不同模型，且有一个 Agent 的模型仅显示用户挑选的项目。
- 操作：保存 Profile `Work`，更改两者设置，再应用 `Work`。
- 结果：模型和每 Agent 可见列表恢复到 `Work`；应用过程中某文件写入失败时，显示失败并保留可恢复状态。

## 8. 构建和交付

交付完整源码、依赖/构建说明、各目标平台可用的运行方式、用户手册、自动化与人工测试记录、已知限制。按 REQ 编号逐项写明通过、失败或未运行；列出测试输入、环境和观察结果。

可以使用合成凭据和本地 HTTP fixtures。不得把真实用户账号、API key、会话、提示词或未脱敏的截图放入交付。OAuth/外部 Provider 的真实登录若缺少账号或服务条件，标为未运行，不可用模拟成功冒充真实集成通过。

不得读取、下载、导入、链接或调用原软件的源码、可执行程序、数据目录或网络实例来代替实现。不得把原软件实现藏进依赖或包装器。使用 interfaces 依赖随包提供的文字视觉参考；不要求或携带截图。独立重建的完成不等于与原版通过实际差异对比；只有评估者完成独立运行和比较后才能声称验证通过。
