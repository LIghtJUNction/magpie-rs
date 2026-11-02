# 03 · 本地 Gateway 和外部协议

完整公开接口登记见 [UPSTREAM_ROUTES.md](../UPSTREAM_ROUTES.md)：它依据固定 0.1.2185 源码补录 17 个路由族、69 条唯一注册模式，并映射本文件、core 与新增合同。下方主表是主要入口索引，不能用于排除补录中的别名、图片/视频、embedding/rerank、MCP、握手、专用目录或观测 API。登记和待执行验收不代表当前 Rust 实现状态；状态以主配方 COVERAGE 的逐条证据为准。

## REQ-030：本地 Gateway 生命周期

- 优先级：核心。GUI 默认启动 Gateway；也可以由 `magpie serve`、`magpie tui` 或 headless/Web 模式单独启动。
- 默认 Gateway 监听 loopback `127.0.0.1:3425`；用户可通过设置/环境值改变监听地址。只在 loopback 时，本机 Agent 可使用网关，不需单独创建 key。网络监听必须明确开启。
- 返回给 Agent 的 base URL 使用该 Gateway 的协议和端口。GUI 页面端口默认 `3430`，可通过 Web 启动参数修改；页面服务与 Gateway 可由一个可公开 URL 的反向代理入口共同提供。
- 运行状态显示服务是否启动、监听地址、当前版本和错误。正常退出停止服务并通知客户端；崩溃/重启后不破坏 Agent 配置。
- 桌面管理自己启动的服务；已有外部服务只能查询，不能因为地址相同就接管或停止。服务版本必须来自服务端，不能用本机 CLI 版本冒充。启动成功要求服务初始化完成且健康与目录可读，单纯子进程存活或端口监听不等于可用。桌面正常退出和异常消失均释放自己拥有的服务，停止/启动失败不改写 Agent 配置。
- 正常验收：启动桌面 App 后，本机 Agent 可请求 `http://127.0.0.1:3425/v1/models`。
- 失败验收：端口被占用、地址非法或证书缺失时启动失败并给出地址/端口原因，不假报可用。

## REQ-031：请求入口与模型标识

- 优先级：核心。对外模型名为 `provider/model`；路由组名为 `group/<id>`，也可按明确定义的组名称请求。
- 支持：

| API | Gateway 路径 | 行为 |
| --- | --- | --- |
| OpenAI Chat Completions | `POST /v1/chat/completions` | 接收对话、工具、图像等支持字段；支持 stream |
| OpenAI Responses | `POST /v1/responses` | HTTP/SSE，续接按 REQ-106，WebSocket 按 REQ-110 |
| Anthropic Messages | `POST /v1/messages` | 支持 stream 与 `/v1/messages/count_tokens` |
| Gemini | `/v1beta/models/{model}:generateContent`、`:streamGenerateContent`、`:countTokens` | 支持 Gemini 请求/响应结构 |
| Speech-to-text | `/v1/audio/transcriptions`、`/v1/audio/translations` | 接收 OpenAI multipart 表单并透传音频 |
| Text-to-speech | `POST /v1/audio/speech` | 按 REQ-107 转发音频或语音事件流 |
| 模型目录 | `GET /v1/models`、`GET /v1beta/models`、`GET /v1/codex/models` | 返回可选模型、名称、reasoning 能力和 Agent 所需的目录字段 |
| Ollama 目录兼容 | `GET /api/version`、`GET /api/tags`、`POST /api/show` | 列出模型和能力；具体方法与别名按固定原版路由登记 |
| Ollama 原生生成 | `POST /api/chat`、`POST /api/generate` | 转换为 Chat 请求，返回 Ollama JSON/流式 NDJSON；原版 0.1.1948 与 0.1.2185 均已支持，旧排除条款无效 |

- 模型列表中的 reasoning 能力和 level 反映模型或组可共同支持的等级。组的 `native_endpoints` 只标注无需翻译的协议；不支持的协议不得假装支持。
- Speech 输入请求保留音频字节和用户选择的输出格式，只将模型名换为上游 id；对提示词字段按隐私遮蔽设置处理。无兼容端点或无此能力时返回上游兼容错误。语音输出按 REQ-107，不能以旧排除条款删掉该能力。
- 正常验收：Agent 以 `anthropic/claude-...` 名称请求，Gateway 按 Provider 路由并以 Anthropic Messages 结构返回。
- 边界验收：裸模型 ID 在多 Provider 可用时按组/配置的确定规则路由；不能因两个 Provider 的模型名相同而随机选择。

## REQ-032：协议转换和流式行为

- 优先级：核心。对每个请求识别客户端协议和上游协议；能原生提供就直接使用该协议，不能时翻译一次。不可把请求同时重复发送到两个接口来“尝试兼容”。
- 在转换范围内保留 system/developer/user/assistant 消息、工具 schema 与调用/结果、图像、reasoning、模型 effort、温度/输出上限、停止条件、会话上下文和 usage。仅针对真实上游不接受的参数做有记录的删除/转换。
- Stream 请求以目标协议的事件/SSE 格式返回内容增量、工具增量、用量、结束原因和错误；非流式请求保持该协议的单响应形态。首 token 之前的路由状态必须能被状态 API 读取。
- Ollama `/api/chat` 和 `/api/generate` 使用其公开请求/响应结构，流式返回逐行 JSON（NDJSON），不把 SSE 原文发给 Ollama 客户端。按实际能力保留消息、工具、options、用量和终态，不能只提供目录却宣称兼容原生生成。
- 任何网关转译行为都不能更改模型用户文本或工具参数，除非用户显式启用了 `09` 中的隐私遮蔽。
- Chat 输出上限兼容（原版 0.1.2155）：只有上游主机是 `openai.com` 或其真正子域时预先采用 `max_completion_tokens`；第三方兼容端点保持原有字段。任意 Chat 上游只有在返回 400 且明确拒绝当前字段、要求改用另一字段时，才允许在尚未发送客户端响应的条件下交换 `max_tokens`/`max_completion_tokens` 并重试一次。总 deadline 和并发槽不重置；其他 400、认证/配额错误、模糊正文、双字段冲突和已开始流式输出均不得触发重放。测试必须含类似 `openai.com.example.org` 的反例。
- 正常验收：将 OpenAI Chat 请求发到仅支持 Anthropic Messages 的自定义 Provider，工具调用流按 Anthropic 协议返回；反向也成立。
- 失败验收：不支持的字段/多模态类型要么按已定义映射保留语义，要么返回明确的兼容性错误，不能静默丢掉调用内容。

## REQ-033：错误、状态码和请求归属

- 优先级：核心。
- 错误响应保持客户端协议能理解的 JSON/SSE 形式，至少区分未认证、模型不存在/不可见、配额耗尽、速率限制、上游 4xx/5xx、网络/超时和内部错误。错误日志须关联用户可见请求 ID、Provider、模型和尝试序列。
- 最终由任一 retry/fallback 成功的请求，Agent 收到成功响应。只有最终 Agent 看到错误时才把请求计为失败；失败尝试可单独显示并注明 handed-on/fallback。
- 成功/失败响应携带 `X-Magpie-Provider` 和 `X-Magpie-Model`，值分别为实际 Provider 与 `provider/model`。调用工具和上游重试的账务/日志归属不能重复。
- 正常验收：第一成员 429 后第二成员成功，Agent 只看到成功，Route 记录两个尝试。
- 失败验收：所有成员失败时，最终错误包含可操作类别并保留客户端协议，而不是泄露密钥、完整 prompt 或响应正文。

## REQ-034：模型目录契约

- 优先级：核心。
- `/v1/models` 给出 Provider/model id、显示信息、reasoning、supported reasoning levels、原生直通 API 等能力。Catalog 包括 Provider 查询结果和公共目录的模型能力补充；用户可过滤单 Agent 的列表。
- 同一模型被多个 Provider 服务时，Catalog ID 仍能指出来源。一个 Agent 直接点名的模型可被处理，即使“only-picked”目录不向该 Agent 展示它。
- 模型目录不可用时返回最后一次有效数据及可识别的 stale 状态；刷新 Provider 或所有 Provider 不应清空仍有效模型。
- 正常验收：`GET /v1/models` 显示三个已配置 Provider 的已暴露模型；Codex 专用目录提供 Codex 可读字段。
- 失败验收：模型目录服务器离线，响应仍有缓存模型且不会将 stale 列表写回 Agent 为“空”。

## REQ-035：本机访问和远程鉴权

- 优先级：核心。
- 本机直连 loopback 免 Gateway key；按真实连接来源判定远程/代理请求，不仅看 Host 字符串或调用者给的伪造 header。
- 开启局域网分享或将 Gateway 绑定到非 loopback 时，远程客户端必须提供一个启用的 Gateway key；根据 key 的模型、Provider/账户、预算、速率、并发限制授权。没有 key 或请求未获准模型时在进入 Provider 前拒绝。
- Web UI 有独立页面登录 key/认证 Cookie；该凭据不是 Gateway key。网页登录不得绕过管理员 API 认证。客户端 key 不能读取全部 Provider 密钥。
- 支持来源 allowlist；无效/不可读 allowlist 不得变成全开放。TLS 证书/私钥和反向代理信任需由用户显式配置。
- `MAGPIE_TRUST_PROXY=1` 只在受控反代已执行客户端鉴权时使用；真实客户端 IP 只从可信 proxy header 读取。
- 正常验收：loopback 无 key 成功；远程无 key 为 401/403；有效 key 但受限模型被拒。
- 失败验收：Host/DNS rebinding、`Origin: null`、伪造转发头或未授权来源不得获得本机免认证访问。

## REQ-036：Agent 连接到网关

- 优先级：核心。
- 对标准 OpenAI、Anthropic 和 Gemini 客户端展示以下兼容设置：

| 客户端协议 | Base URL | 本机示例认证值 |
| --- | --- | --- |
| OpenAI | `http://127.0.0.1:3425/v1` | `OPENAI_API_KEY=magpie` |
| Anthropic | `http://127.0.0.1:3425` | `ANTHROPIC_API_KEY=magpie` |
| Gemini | `http://127.0.0.1:3425` | `GEMINI_API_KEY=magpie` |

- “magpie”只是本机兼容值；启用网络访问时改用真实 Gateway key。支持第三方 Agent 通过 base URL 使用 Gateway，即使没有专门的 Agent 卡片。
- 对使用 Ollama endpoint 的 Agent，给它 Gateway base `http://127.0.0.1:3425`；根据客户端实际调用选择原生 `/api/chat`、`/api/generate` 或 OpenAI Chat 入口，不能强行假定所有客户端都会改走 Chat Completions。
- 正常验收：用仅 base URL 配置的客户端在本机请求模型，不需供应商密钥。
- 失败验收：把本机明文 key 复制到远程配置不能绕过 Gateway key 权限限制。

## REQ-037：会话、账户固定和观测 API

- 优先级：一般。
- 请求的会话身份只能来自 Agent 的 session header 或 `X-Magpie-Session`，不能从 prompt、用户 key 或客户端 IP 猜测。服务端返回该 ID 的 `X-Magpie-Session`，便于浏览器 CORS 读取。
- `X-Magpie-Account: <email|login|id>` 限定只用指定订阅账户；未知、暂停或无该模型权限的账户返回错误，不回退到别的账户。该 header 不转发到上游。
- `GET /v1/magpie/route?session=...` 返回最近一次路由尝试、请求模型、规则、effort、实际成员、尝试失败、最终响应状态和服务者；路由尚未发生时 route 为 null。
- 向另一个可访问的 Magpie 传递 session header 时保留同一会话归属。
- 正常验收：指定一个有效账户只由它服务；请求另一个有效账户时响应来源是所指定的那个。
- 失败验收：请求无 session header 不虚构 ID，也不记录成一个可列出的假会话。

## REQ-038：远程 magpie

- 优先级：一般。
- 可将一台 Magpie 配为 Remote Provider；调用远端已授权模型/分组，并将远端回复当成本机请求的上游。用户可选择远端 base URL、密钥、同步间隔和模型可见范围。
- 请求只走远端公开兼容接口；如本机请求已是 Messages、Responses 或 Chat，远端支持对应接口时使用同一接口，避免两次设备间转码。远端禁用模型、密钥或服务缺失时返回对应错误。
- 不向远端复制本机 Agent 凭据、其它 Provider key 或 prompt 录制数据。认证信息不进入应用日志。
- 正常验收：本机 Agent 请求 remote/ 模型，远端记录相同 model 和 session ID。
- 失败验收：远端版本、模型或 API 不兼容时明确标记，保留上一次缓存目录而不把它宣称为实时可用。

## REQ-039：Gateway 的附加查询接口

- 优先级：一般。服务端可暴露 `/v1/magpie/usage`、`/v1/magpie/usage/requests`、`/v1/magpie/quotas`、`/v1/magpie/quotas/history`、`/v1/magpie/quotas/cards`、`/v1/magpie/concurrency`、`/v1/magpie/limit` 和 `POST /v1/magpie/quotas/refresh`。查询与写入的 method、trace 及 usage ingestion 按 UPSTREAM_ROUTES 的精确登记区分，不因路径相同而共享只读权限。
- 查询接口使用与 Web UI/CLI 一致的时间窗口、筛选、价格、预算和隐藏数据规则；分页有明确上限。
- 受限 Gateway key 只能看到其自身用量与明确授权的额度卡；不能读取 Provider 全局 quota、其它客户端用量或其它账户信息。没有权限的额度端点返回稳定的 `gateway_key_held` 类错误。
- 上述数据限制指预算、模型或账户等具体 holds；只读权限是禁止消费的独立维度，按 REQ-108 验收。没有附加数据限制的只读 key 可读全局 usage/额度，不能把所有 readonly key 误收窄为仅自身数据，也不能让它执行生成请求。
- 正常验收：客户端 key 读取自身 usage；额度受限 key 看不到其它 key 的用量。
- 失败验收：无效参数返回 4xx 并保持状态不变；该页面的浏览器 key 不能冒充 Gateway key。
