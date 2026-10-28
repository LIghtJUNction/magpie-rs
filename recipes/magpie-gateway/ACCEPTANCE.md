# 分项验收

迁移时全部未执行。使用一次性隔离 HOME/Agent 目录、虚构密钥、本地可记录请求的上游 fixture；仅对明确授权的真实集成使用账户。软件重建后，每条记录版本/平台、初始数据、操作、实际结果和未验证原因。

先读取本包所有 specs。每条 REQ 的全部正文是验收合同，下面列出原规格明确给出的正常/失败/边界场景；不能只检查成功路径。对于 API/账号/文件权限，fixture 要能注入 401/429/5xx、超时、损坏数据、并发修改和取消，并检查请求次数及文件前后内容。UI 用真实构建运行，在宽屏、窄屏及键盘流程下验收。

## REQ-030：本地 Gateway 生命周期

规格：[specs/03-gateway-and-protocols.md](specs/03-gateway-and-protocols.md)。状态：未执行。

- 正常验收：启动桌面 App 后，本机 Agent 可请求 `http://127.0.0.1:3425/v1/models`。
- 失败验收：端口被占用、地址非法或证书缺失时启动失败并给出地址/端口原因，不假报可用。

## REQ-031：请求入口与模型标识

规格：[specs/03-gateway-and-protocols.md](specs/03-gateway-and-protocols.md)。状态：未执行。

- 正常验收：Agent 以 `anthropic/claude-...` 名称请求，Gateway 按 Provider 路由并以 Anthropic Messages 结构返回。
- 边界验收：裸模型 ID 在多 Provider 可用时按组/配置的确定规则路由；不能因两个 Provider 的模型名相同而随机选择。

## REQ-032：协议转换和流式行为

规格：[specs/03-gateway-and-protocols.md](specs/03-gateway-and-protocols.md)。状态：未执行。

- 正常验收：将 OpenAI Chat 请求发到仅支持 Anthropic Messages 的自定义 Provider，工具调用流按 Anthropic 协议返回；反向也成立。
- 失败验收：不支持的字段/多模态类型要么按已定义映射保留语义，要么返回明确的兼容性错误，不能静默丢掉调用内容。

## REQ-033：错误、状态码和请求归属

规格：[specs/03-gateway-and-protocols.md](specs/03-gateway-and-protocols.md)。状态：未执行。

- 正常验收：第一成员 429 后第二成员成功，Agent 只看到成功，Route 记录两个尝试。
- 失败验收：所有成员失败时，最终错误包含可操作类别并保留客户端协议，而不是泄露密钥、完整 prompt 或响应正文。

## REQ-034：模型目录契约

规格：[specs/03-gateway-and-protocols.md](specs/03-gateway-and-protocols.md)。状态：未执行。

- 正常验收：`GET /v1/models` 显示三个已配置 Provider 的已暴露模型；Codex 专用目录提供 Codex 可读字段。
- 失败验收：模型目录服务器离线，响应仍有缓存模型且不会将 stale 列表写回 Agent 为“空”。

## REQ-035：本机访问和远程鉴权

规格：[specs/03-gateway-and-protocols.md](specs/03-gateway-and-protocols.md)。状态：未执行。

- 正常验收：loopback 无 key 成功；远程无 key 为 401/403；有效 key 但受限模型被拒。
- 失败验收：Host/DNS rebinding、`Origin: null`、伪造转发头或未授权来源不得获得本机免认证访问。

## REQ-036：Agent 连接到网关

规格：[specs/03-gateway-and-protocols.md](specs/03-gateway-and-protocols.md)。状态：未执行。

- 正常验收：用仅 base URL 配置的客户端在本机请求模型，不需供应商密钥。
- 失败验收：把本机明文 key 复制到远程配置不能绕过 Gateway key 权限限制。

## REQ-037：会话、账户固定和观测 API

规格：[specs/03-gateway-and-protocols.md](specs/03-gateway-and-protocols.md)。状态：未执行。

- 正常验收：指定一个有效账户只由它服务；请求另一个有效账户时响应来源是所指定的那个。
- 失败验收：请求无 session header 不虚构 ID，也不记录成一个可列出的假会话。

## REQ-038：远程 magpie

规格：[specs/03-gateway-and-protocols.md](specs/03-gateway-and-protocols.md)。状态：未执行。

- 正常验收：本机 Agent 请求 remote/ 模型，远端记录相同 model 和 session ID。
- 失败验收：远端版本、模型或 API 不兼容时明确标记，保留上一次缓存目录而不把它宣称为实时可用。

## REQ-039：Gateway 的附加查询接口

规格：[specs/03-gateway-and-protocols.md](specs/03-gateway-and-protocols.md)。状态：未执行。

- 正常验收：客户端 key 读取自身 usage；额度受限 key 看不到其它 key 的用量。
- 失败验收：无效参数返回 4xx 并保持状态不变；该页面的浏览器 key 不能冒充 Gateway key。

## REQ-050：汇总 Usage

规格：[specs/05-usage-and-sessions.md](specs/05-usage-and-sessions.md)。状态：未执行。

- 正常验收：一笔由上游响应返回 token 数量的成功请求，在 Provider、Model、Account 和总览上总计一致。
- 失败验收：一笔失败或已重试请求不会将失败尝试重复计入最终成功调用；用量未返回时标成 unknown。

## REQ-051：额度和窗口

规格：[specs/05-usage-and-sessions.md](specs/05-usage-and-sessions.md)。状态：未执行。

- 正常验收：CLI 与 Usage 卡读取同一 quota snapshot，额度和重置时间相同。
- 失败验收：离线刷新不把旧额度清零或显示为刚刷新。

## REQ-052：Cap、预算和计算

规格：[specs/05-usage-and-sessions.md](specs/05-usage-and-sessions.md)。状态：未执行。

- 正常验收：设置某账户每日 cap 到 20 次后，达到第 20 次拒绝新请求并说明重置时间。
- 失败验收：价格表缺失、额度 endpoint 失败或币种未知时不误报“仍在预算内”。

## REQ-053：查询和分析

规格：[specs/05-usage-and-sessions.md](specs/05-usage-and-sessions.md)。状态：未执行。

- 正常验收：筛选指定 account/time 后，明细行和卡片汇总相符。
- 失败验收：自定义时间区间无数据时显示无记录与筛选上下文，不沿用旧图表数。

## REQ-054：Session 列表

规格：[specs/05-usage-and-sessions.md](specs/05-usage-and-sessions.md)。状态：未执行。

- 正常验收：同一 Agent session 多轮 Gateway 请求列为一条 session，并显示 request count。
- 失败验收：缺少原生 session id 时显示“无法确认”并用可追踪的请求 id，不伪造可跨机器稳定的 id。

## REQ-055：消息内容、记录开关与保留

规格：[specs/05-usage-and-sessions.md](specs/05-usage-and-sessions.md)。状态：未执行。

- 正常验收：默认新安装打开 Session 页面只见元数据，不见消息文本；用户开启后新请求可查阅，按 retain limit 删除。
- 失败验收：磁盘容量达到上限时停止记录/清理最旧数据并告知，Gateway 本身仍可以正常转发。

## REQ-056：隐私遮蔽

规格：[specs/05-usage-and-sessions.md](specs/05-usage-and-sessions.md)。状态：未执行。

- 正常验收：向上游的文本不再包含配置中匹配的明文，而本机可选明文记录的权限和保留策略明确。
- 失败验收：规则语法错误或遮蔽服务不可用时按 fail-closed 处理，提示用户，而非安静发送原文。

## REQ-057：Pricing

规格：[specs/05-usage-and-sessions.md](specs/05-usage-and-sessions.md)。状态：未执行。

- 正常验收：用户修改自定义模型价格后，过去数据可明确区分使用历史价格还是按新价格重算。
- 失败验收：未知模型 pricing 显示 `unknown`，不让费用图表以零误导。

## REQ-058：导出

规格：[specs/05-usage-and-sessions.md](specs/05-usage-and-sessions.md)。状态：未执行。

- 正常验收：一份 CSV 在表格软件中打开时数据没有错位，汇总值与页面筛选结果一致。
- 失败验收：没有数据或写文件失败时给具体错误，不保留截断的成功文件。

## REQ-059：Quota/Usage API 与请求追踪

规格：[specs/05-usage-and-sessions.md](specs/05-usage-and-sessions.md)。状态：未执行。

- 正常验收：控制台与 HTTP API 用相同筛选查询得到相同的聚合数。
- 失败验收：上游 usage 返回两次或使用两种协议时，去重依据是 request/attempt id，不是脆弱的内容相同判断。

## REQ-060：内置 Provider 与社区插件边界

规格：[specs/06-plugins-and-tools.md](specs/06-plugins-and-tools.md)。状态：未执行。

- 正常验收：插件版 Zed 的登录/模型/请求都交给 `opencode-zed-auth`，插件关闭时 Provider 不可用并解释原因。
- 失败验收：插件 host 未运行，不伪装成 built-in service 能接管。

## REQ-061：社区 Provider 插件查找和安装

规格：[specs/06-plugins-and-tools.md](specs/06-plugins-and-tools.md)。状态：未执行。

- 正常验收：按包名安装插件后，Packages/Plugins 列表和插件 host 一致；升级成功后版本号更新。
- 失败验收：包不存在、下载/解析失败、版本不兼容时保留上一个有效版本和可读错误。

## REQ-062：社区 Provider 插件运行

规格：[specs/06-plugins-and-tools.md](specs/06-plugins-and-tools.md)。状态：未执行。

- 正常验收：一个插件 host 崩溃只使该插件不可用，状态栏中给出失败详情。
- 失败验收：未知的插件消息版本被隔离拒绝，不崩溃整个 Magpie，也不在协议降级时把密钥转到插件日志。

## REQ-063：订阅账号转移

规格：[specs/06-plugins-and-tools.md](specs/06-plugins-and-tools.md)。状态：未执行。

- 正常验收：`migrations.json` 显示迁移状态，旧 saved account 确认由插件 sign-in 可用后计为已迁移。
- 失败验收：迁移失败时，不要删除旧账号数据，也不要报告为已由插件服务；“Move to plugin”可再次尝试。

## REQ-064：插件登录和模型目录

规格：[specs/06-plugins-and-tools.md](specs/06-plugins-and-tools.md)。状态：未执行。

- 正常验收：插件授权成功后模型可在 `/v1/models` 及相关 Agent models 设置中出现。
- 失败验收：re-auth 失败时账号仍保留、显式要求用户重新登录、不循环弹窗。

## REQ-065：Web 和其它扩展工具

规格：[specs/06-plugins-and-tools.md](specs/06-plugins-and-tools.md)。状态：未执行。

- 正常验收：添加一个 MCP tool 后 Agent 清单可读取，设置变化后正常 reconnect。
- 失败验收：启动超时/无效 schema 只禁用这个 server，并说明命令、退出码或 protocol error，不将其当普通 chat 响应。

## REQ-066：社区 Provider 插件可观测性和移除

规格：[specs/06-plugins-and-tools.md](specs/06-plugins-and-tools.md)。状态：未执行。

- 正常验收：插件移除后仍保留明确的移除记录，并且 Provider 列表解释不再服务。
- 失败验收：plugin 状态服务不可读时显示 unknown/error，而不是 enabled/healthy。

## REQ-067：Magpie 内置功能插件

规格：[specs/06-plugins-and-tools.md](specs/06-plugins-and-tools.md)。状态：未执行。

- 正常验收：Routing feature plugin 因一个 routing group 正在使用显示为 required/active；删除最后一个 group 后允许关闭。
- 失败验收：一个 plugin Apply panic/error 后回滚自己已注册项，不阻止其他 feature plugin 启动。

## REQ-068：Gateway extension points

规格：[specs/06-plugins-and-tools.md](specs/06-plugins-and-tools.md)。状态：未执行。

- 正常验收：启用一个 JS middleware 后 `/v1/chat/completions` 请求被 observer 识别并按其 hook 定义继续或拒绝。
- 失败验收：某个 middleware 抛错时关联 request id 并按其配置的失败策略处理，不重复转发一次请求。

## REQ-069：插件种类和状态区分

规格：[specs/06-plugins-and-tools.md](specs/06-plugins-and-tools.md)。状态：未执行。

- 正常验收：用户搜索 `cursor` 时能区分内置的状态页与社区 `opencode-cursor-auth` 登录插件。
- 失败验收：状态 API 返回来源不明时标为 unknown，不把一个宿主的 healthy 状态复制到其他 plugin 类型。
