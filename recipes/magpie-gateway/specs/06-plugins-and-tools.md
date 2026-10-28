# 06 · 插件和扩展

## REQ-060：内置 Provider 与社区插件边界

- 优先级：核心。展示的 Provider 种类可从内置 API Provider 或扩展插件提供，用户可在对应页面看出运行来源和 owner。
- 本基线中以下订阅已迁移并退役；由对应社区 OpenCode 插件负责授权、models、requests、usage/quota 和错误：

| 订阅 id | 插件 npm 包 |
| --- | --- |
| `commandcode-plan` | `@magpie-community/opencode-commandcode-auth` |
| `cursor` | `@magpie-community/opencode-cursor-auth` |
| `devin` | `@magpie-community/opencode-devin-auth` |
| `factory` | `@magpie-community/opencode-factory-auth` |
| `grok` | `@magpie-community/opencode-grok-auth` |
| `kiro` | `@magpie-community/opencode-kiro-auth` |
| `mimo-app` | `@magpie-community/opencode-mimo-auth` |
| `qoder`, `qoder-cn` | `@magpie-community/opencode-qoder-auth` |
| `workbuddy`, `workbuddy-ai` | `@magpie-community/opencode-workbuddy-auth` |
| `zcode` | `@magpie-community/opencode-zcode-auth` |
| `zed` | `@magpie-community/opencode-zed-auth` |

- 退役 id 的旧账号凭证搬迁代码仅用于将账号迁入插件；普通 built-in translator、sign-in、quota 和 gateway 请求路径不得重新启用。
- 不在内置二进制实现绕过这些插件的登录、token refresh、请求签名或封装其供应商订阅通道。具体供应商条款和接入由用户本人确认。
- 正常验收：插件版 Zed 的登录/模型/请求都交给 `opencode-zed-auth`，插件关闭时 Provider 不可用并解释原因。
- 失败验收：插件 host 未运行，不伪装成 built-in service 能接管。

## REQ-061：社区 Provider 插件查找和安装

- 优先级：核心。Plugins 页面区分 Magpie 内置功能插件和社区 Provider 插件。Provider 插件可按 name/id 搜索、查看包描述、版本、作者/来源与最低兼容版本。
- 社区 Provider 插件从 npm 包安装并存储在 Magpie 用户数据目录；启动或 update 失败时保留最后可用插件，显式显示错误和版本。插件 min version 与 manifest compatibility 共同决定可用状态。
- 支持查看 changelog/版本、update one/all、更新锁定策略。自动更新需受最小版本约束，不能降低用户锁定版本。
- 正常验收：按包名安装插件后，Packages/Plugins 列表和插件 host 一致；升级成功后版本号更新。
- 失败验收：包不存在、下载/解析失败、版本不兼容时保留上一个有效版本和可读错误。

## REQ-062：社区 Provider 插件运行

- 优先级：核心。社区 Provider 插件通过 Node 插件 host 执行；host 与 Magpie 之间有版本化协议，提供 provider id、设置 schema、sign-in、models、fetch、quota/usage、错误与状态回传。
- host 只向 provider 插件协议提供服务该 provider 所需的凭据/请求字段；不把其它 Provider token、Gateway keys 或录制正文列入协议 payload。插件是用户安装的第三方可执行包；不要把 Node 子进程描述成 OS sandbox。
- 在 host 崩溃后 GUI、Gateway 和其它 Provider 仍可显示状态并继续工作；该订阅单独显示不可用。
- 正常验收：一个插件 host 崩溃只使该插件不可用，状态栏中给出失败详情。
- 失败验收：未知的插件消息版本被隔离拒绝，不崩溃整个 Magpie，也不在协议降级时把密钥转到插件日志。

## REQ-063：订阅账号转移

- 优先级：核心。
- 从旧内置账号迁到插件时，UI 显示旧订阅 id、目标 plugin、将复制的凭证/账户数量、版本/状态和可恢复提示；移转后 `/api/providers` 的 `move.state` 是 `plugin`，对应 provider id 出现在 `onPlugins`。
- “用户安装了同一插件”也视为 hand-over：无旧账号时立即切换；存在旧账号时 hourly migration runner 继续搬迁。若插件已在自己的 `-plugin` id 登录，则不重复复制。失败后按保存的重试策略再次尝试并保留失败原因。
- 这些旧 id 已退役；满足最小插件版本的用户和新用户由插件提供。新用户在 Add 页不应添加退役 builtin，应在 Plugins 页面安装。
- 用户移除插件会记录 `removed`，保留插件侧账号状态；重新安装插件后订阅恢复。关闭插件会同步停用该订阅。
- 正常验收：`migrations.json` 显示迁移状态，旧 saved account 确认由插件 sign-in 可用后计为已迁移。
- 失败验收：迁移失败时，不要删除旧账号数据，也不要报告为已由插件服务；“Move to plugin”可再次尝试。

## REQ-064：插件登录和模型目录

- 优先级：核心。支持 plugin sign-in 的 oauth/device/browser 或 API key 流程。弹出登录 URI 前说明目标插件；完成后插件返回账户展示名称、状态与账户 key 的安全存储引用。
- 为账号执行 validate/refresh/reauthorize，并显示登录要求、权限/订阅类型和到期/错误状态。Access/refresh token 不出现在 UI 以外的普通日志或导出中。
- 插件提供模型和能力目录、usage/quota snapshot、请求限流/错误原因；用户可禁用某个模型或 plugin provider。
- 正常验收：插件授权成功后模型可在 `/v1/models` 及相关 Agent models 设置中出现。
- 失败验收：re-auth 失败时账号仍保留、显式要求用户重新登录、不循环弹窗。

## REQ-065：Web 和其它扩展工具

- 优先级：一般。扩展工具包括用户自定义 MCP server、Agent tools 和 web search connector；插件/工具状态与 Provider 插件分开展示并可单独禁用。
- MCP server 支持 stdio/HTTP transport（按实现支持的范围），配置 command、args、headers/environment 和所需权限；敏感环境值单独安全存储，界面显示 connected/starting/error。
- 工具发现 schema 要验证、不允许以工具名覆盖内置关键路由；超时/取消应传到 Gateway/Agent。web_search/web_fetch 可按 `04` 的配置连真实搜索服务。
- 正常验收：添加一个 MCP tool 后 Agent 清单可读取，设置变化后正常 reconnect。
- 失败验收：启动超时/无效 schema 只禁用这个 server，并说明命令、退出码或 protocol error，不将其当普通 chat 响应。

## REQ-066：社区 Provider 插件可观测性和移除

- 优先级：一般。Plugins 页面和 CLI 能显示 plugin id、npm version、minimum version、enabled/moved/removed status、last result、last update 和错误数。诊断包需隐藏 tokens。
- 用户移除 plugin 前列出受影响 subscription、插件账号和配置路径；操作完成后 UI 展示插件不可用和恢复方式。卸载可逆数据留存时间与空间路径可见。
- 用户重装后可清楚区分保留下来的 moved state 与新授权账号；不能把新安装的 plugin 等同于自动恢复了外部订阅会话。
- 正常验收：插件移除后仍保留明确的移除记录，并且 Provider 列表解释不再服务。
- 失败验收：plugin 状态服务不可读时显示 unknown/error，而不是 enabled/healthy。

## REQ-067：Magpie 内置功能插件

- 优先级：核心。Magpie 自身的模块页/功能可作为内置 feature plugins 注册；它们与 npm Provider plugins 是两种不同扩展。Feature plugin 声明 id、API compatibility、requirements/provided services、settings schema、页面 slots 和生命周期。
- 运行时根据启用配置和服务依赖 reconcile；状态分别显示 `off`、`pending`、`active`、`failed`。依赖不满足的 plugin 留在 pending 并说明等待项；版本范围不兼容/Apply 错误为 failed。
- 可开关不代表所有插件都可关；`Essential` 或因正在使用而必须加载的 feature plugin 要提示原因。关闭插件撤回它注册的 service/page/extension，以逆序 dispose；重新开启后重建。
- 正常验收：Routing feature plugin 因一个 routing group 正在使用显示为 required/active；删除最后一个 group 后允许关闭。
- 失败验收：一个 plugin Apply panic/error 后回滚自己已注册项，不阻止其他 feature plugin 启动。

## REQ-068：Gateway extension points

- 优先级：一般。内置 feature plugin 可贡献 observer/OTel、routing strategy、request middleware、privacy masking、tools/web search、budget/key limits 等 extension point。自定义网关中间件可作为用户安装的 JS middleware 插件运行。
- Extension 注册和 dispose 由 Magpie plugin runtime 管理；运行顺序按配置/服务声明确定。请求只经过一次匹配的处理链，报错能指出 extension id 与 hook。
- 选择 search/routing/masking/budget 功能时自动满足其依赖加载；这些功能关闭时其 extension 不再接收新请求。
- 正常验收：启用一个 JS middleware 后 `/v1/chat/completions` 请求被 observer 识别并按其 hook 定义继续或拒绝。
- 失败验收：某个 middleware 抛错时关联 request id 并按其配置的失败策略处理，不重复转发一次请求。

## REQ-069：插件种类和状态区分

- 优先级：核心。UI/CLI 明确标识 plugin 是内置 feature、Provider/OpenCode community plugin、MCP server 还是 Agent 扩展；各自版本、宿主、账户和停用语义分开显示。
- 一个 feature plugin 开关不等于停用同名社区 npm plugin；卸载 OpenCode Provider plugin 不应删除 Magpie 的 Routing/Usage 页面，反之亦然。
- 正常验收：用户搜索 `cursor` 时能区分内置的状态页与社区 `opencode-cursor-auth` 登录插件。
- 失败验收：状态 API 返回来源不明时标为 unknown，不把一个宿主的 healthy 状态复制到其他 plugin 类型。
