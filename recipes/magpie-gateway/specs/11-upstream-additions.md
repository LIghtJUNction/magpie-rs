# 11 · 固定原版 0.1.2185 的新增与补全合同

本文件将官方 0.1.1948 二进制之后、截至 0.1.2185 的缺失用户能力写成稳定需求。目标源码固定为 `f1151226262022743bd103a78af41e1004cc6055`；完整日志、两种基线及逐版本映射见主配方 UPSTREAM_CHANGELOG.md。REQ-105–110 是新增编号，旧 93 个编号全部保留。

**当前状态：本文件首次写入时，以下六条均未实现、未验收。** 这是目标合同，不是通过报告；随后实现程度逐条更新主配方 COVERAGE.md，不能把某个底层模块或合成上游测试等同于整项完成。GPUI、独立 CLI/headless、未来 Web/TUI 共用状态与权限，继续执行 REQ-010。原版 Go 实现只作只读行为依据，不能成为 Rust 的运行依赖；既有 Node 插件架构保留。

## REQ-105：Library MCP Hub

- 优先级：核心。依赖 REQ-013/014 的配置归属与恢复、REQ-030/035/091 的 Gateway 生命周期与权限、REQ-070/072 的 Library MCP 服务器、REQ-077 的本机设置和 REQ-082/084 的多入口。
- 官方依据：[0.1.1950 提交 e0ea15b8e882f2a485d4cc3d5659352b19758236](https://github.com/yetone/magpie/commit/e0ea15b8e882f2a485d4cc3d5659352b19758236)，固定 [docs/subsystems/mcp-hub.md](https://github.com/yetone/magpie/blob/f1151226262022743bd103a78af41e1004cc6055/docs/subsystems/mcp-hub.md)。后续 2079 调整 Connect 界面，2170 补工具调用行。

### 用户行为

Library → MCP 提供默认关闭的 MCP Hub 开关。用户启用后，支持 HTTP MCP 的 Agent 以一个名为 `magpie` 的服务器入口访问已勾选给它的 Library 服务器；Agent 专属路径为 `/mcp/magpie/hub/<agent>`。其他应用可连接 `/mcp/magpie/hub`，访问 Library 中的服务器。CLI 提供等价 `magpie library mcp hub on|off` 和状态查询。

界面显示实际服务的服务器、工具和 Agent 数量、可复制地址及 Connect 窗口。Connect 的 Agents 页来自实际发现且支持 MCP 的 Agent，显示已连接、未连接和保持各服务器独立配置的状态，支持逐项及全部连接、搜索和折叠；改变设置后窗口保持打开，状态按保存结果更新。Other apps 页提供适用的 JSON 配置；原生桌面可调用预定义的 Cursor/VS Code 安装链接，浏览器只复制配置。不能接受页面传入任意外部 URI 来启动应用。

服务器工具以 `<server>__<tool>` 名称暴露，名称规范化并在长度限制内稳定消除冲突。保留原工具描述、输入 schema 和工具结果。Library 禁用的工具不列出也不能调用；Agent 只能调用它被分配的服务器工具。支持 initialize、ping、tools/list 和 tools/call；不把未实现的 resources/prompts 假报为支持。

### 授权、失败与状态保留

- 启用开关是用户授权共享已配置 Library 服务器；不能借接口传入任意 shell 命令、环境变量或新服务器定义。提示远程有效 Gateway key 能以当前用户权限调用本机命令型服务器。远程鉴权、Origin/Host、来源限制及每次调用的预算/速率/并发均在运行工具前检查。REQ-108 的只读 key 不允许 MCP 调用。
- 不支持 HTTP MCP 的 Agent、用户自己已有同名 `magpie` 入口、Library 自身存在同名服务器时，保留独立服务器配置并给出具体原因，不能覆盖用户内容。写入和关闭恢复都走可识别所有权、漂移检测及事务规则；WSL/LAN 使用对应可达地址和正确认证，不能给远端写本机兼容假 key。
- 命令型后端按 Library 服务器共享一个进程，首次 list/call 才启动，空闲十分钟后结束；下一次调用可重新启动。HTTP/SSE 后端带已配置 header 和 MCP 登录，已失效的 MCP session 仅有限重建。编辑、移除、撤销分配、关闭 Hub 或关闭 Gateway 及时清理其拥有的进程树；另一入口的状态变化也能被服务进程感知。
- 服务器 tools/list 有限等待，失败或超时的服务器可从当次结果中省略，并给出可见失败状态；不能将部分结果写回覆盖完整 Library 配置。工具调用有有限总时间，超时返回工具错误；进程退出在下一调用可重新启动，不能无限重试。缓存只在服务器声明工具变化、配置改变或 Hub 关闭后失效，缓存不得越过工具禁用及 Agent 权限检查。
- 请求体有 4 MiB 上限，列举和调用并发、子进程输出、stderr 摘要及等待队列均有界。日志只记服务器、工具、来源、时长及脱敏错误，不写参数、完整结果、环境凭据。每次调用在 Requests 独立可追踪；不重复计入模型 token/费用总数。

### 验收

- 正常：用两个隔离 stdio/HTTP MCP fixture，两个 Agent 共同使用同一后端；各自看到准确子集、工具名与 schema；连续调用只创建一个进程。GUI/CLI 切换、复制地址和 Connect 状态一致。
- 失败：不存在的命令、后端登录失效、list 超时、call 超时、子进程异常退出分别返回可操作状态；关闭 Hub 后无子进程遗留；错误不含 fixture secret。写配置失败保留原文件和开关状态，不显示假成功。
- 边界：foreign/null Origin、伪 Host、无效 key、只读 key、超过 4 MiB、工具名冲突、禁用工具按原名调用、跨 Agent 调用都被正确处理。用户自有 `magpie` 入口与不支持 HTTP 的 Agent 不被改写。另一进程关闭、修改或移除服务器后，下一次访问和后台清理均收敛；断开恢复只处理自己拥有的内容。

## REQ-106：Responses previous_response_id 续接

- 优先级：核心。扩充 REQ-031/032/037；沿用 REQ-035/091 的调用者权限和 REQ-056/092 的正文保护。
- 官方依据：[0.1.2065 提交 ba3a7384fb2a37211f367140398f3a5dd8132cbf](https://github.com/yetone/magpie/commit/ba3a7384fb2a37211f367140398f3a5dd8132cbf)，`internal/gateway/prev_response.go`、`prev_response_test.go` 及固定 [gateway-routing.md](https://github.com/yetone/magpie/blob/f1151226262022743bd103a78af41e1004cc6055/docs/subsystems/gateway-routing.md)。

### 用户行为

Responses HTTP/SSE 客户端可在后续请求中仅提交新 input 与 `previous_response_id`。Gateway 识别已保留回复，将先前输入与输出按顺序还原，再进入通常的路由、协议翻译、工具处理和隐私遮蔽；因此换 Provider 或换协议也保留上下文。工具调用与结果、图像、reasoning 等已支持内容不能仅因采用续接而丢失。

缓存只接纳 HTTP 200、含有效回复 ID、已形成可使用终态且未 failed/cancelled 的回复；断流和失败尝试不成为可续接历史。`store: false` 不保存 HTTP 续接历史。缓存仅在内存中，默认六小时 TTL、256 MiB 容量并按 LRU 淘汰；单个回复用于提取 output 的读取不超过 64 MiB，超限只跳过缓存，不能截断本来应发给用户的回复。

### 授权、失败与状态保留

- 缓存以调用者 Gateway key、远端 Magpie 归属及必要的无 key 远程来源隔离；知道别人的 response ID 不等于获得历史。密钥禁用或授权范围变化仍在每次续接重新检查。ID 不写原始凭据，不把缓存当成持久会话档案或同步数据。
- 缓存保持客户端内容语义；每次还原后按当前隐私设置重新遮蔽。不能因缓存由先前请求创建而绕过当前过滤、模型限制、预算或路由规则。
- 对转换或不支持上游续接的路由，未知、过期、被淘汰、来自其他调用者或重启后丢失的 ID 返回 OpenAI 形态的 400 `previous_response_not_found`，且不发送不完整上下文。原生 Responses relay 可以把未知 ID 原样传给上游，让上游判断。
- 对已知会存储 Responses 的 API key 上游（原版包括 OpenAI、Azure 和 Ark 付费 `/api/v3`），只有先前回复由同一 Provider、同一有效 key 提供且未关闭 store，才可把已还原历史重新压成上游 ID 加新 input。订阅或插件不得只凭名称假定持久存储能力。上游明确拒绝该 ID 时，使用已还原完整上下文作一次受同一 deadline 约束的 fallback；不能无条件重复每个请求。
- 重启、退出和淘汰都允许续接缓存丢失；无持久化成功假象。内存统计包含被引用的历史片段，不允许通过长链或多分支绕过容量上限。请求、回复提取、缓存队列和并发有界，失败日志不含旧正文。

### 验收

- 正常：HTTP JSON 和 SSE 各完成两轮，第二轮只传新 input；fixture 验证其收到顺序正确的全部上下文，跨协议和换 Provider 都不丢工具或图像。可存储上游在同 key 下只收到增量；上游拒绝 ID 后收到一次完整上下文。
- 失败：错误或断开的首轮不可续接；翻译路由未知 ID 为指定 400 且上游调用计数为零；原生 relay 未知 ID 原样转发。第二次 fallback 仍失败时停止，不循环。
- 边界：不同 key/远端来源不能互读；key 轮换、停用、TTL、LRU、服务重启、`store:false`、超大回复和长链都验证。更改遮蔽设置后续接按新设置生效；被淘汰不能影响已经完成的客户端回复。

## REQ-107：Text-to-speech 转发

- 优先级：核心。扩充 REQ-031/032/033、REQ-040–045 的路由与 fallback、REQ-050 的用量和 REQ-056 的隐私处理。原 `03` 中排除 TTS 的句子不再有效。
- 官方依据：[0.1.2050 提交 f6263439af6151c539a0e55a21cc8eb55c10dade](https://github.com/yetone/magpie/commit/f6263439af6151c539a0e55a21cc8eb55c10dade)，`internal/gateway/speech.go`、`speech_test.go`、`embeddings.go` 和固定 `docs/reference.md` 的 speech 条目。

### 用户行为

提供 `POST /v1/audio/speech` 及相应 bare 路径 `/audio/speech`。接收 OpenAI 风格 JSON：`model`、`input`、`voice`、`response_format`、`speed`、`instructions`、`stream_format`；`model` 使用统一 Provider/model 或组规则。仅把 model 改成实际上游 ID，其余已支持及厂商扩展字段保持原值，例如 `voice_id` 和 `ref_audio`。

使用选中 Provider 的兼容 speech 端点，遵守其账户/key、暂停、预算、速率、并发、fallback 和能力限制。返回供应商原有 Content-Type 和形态：mp3/opus/aac/flac/wav/pcm 音频、`stream_format=sse` 的事件流，或包含 base64 音频的厂商 JSON。成功回复边读边发，不等待整段音频，也不强制上游按 JSON 返回。UI/CLI 的目录与状态不能把只有语音能力的模型虚构为聊天模型。

### 授权、失败与状态保留

- 与模型请求共用 Gateway 鉴权及 caller attribution；只读 key、未授权模型、被停用 Provider 在发出上游请求前拒绝。请求不引入远程下载任意 URL 的旁路；厂商支持的音频引用仍按既有网络与内容规则处理。
- 若用户已启用隐私遮蔽，只处理 `input` 和 `instructions` 文本；不能改 voice、格式、二进制/音频引用和其它非用户文本。音频中读出的占位符不能恢复为原始敏感文本，界面需诚实说明该效果。
- 无兼容 speech 端点或上游不支持时返回对应错误；不暗中把文本发送给另一个未授权服务。Fallback 只在尚未向客户端发出成功响应字节且现有路由允许时进行，已开始流式音频不得重播。
- 客户端取消、慢读取、上游停顿和服务关闭均释放上游连接、并发槽与缓冲区。请求体、错误体、缓冲、总 deadline 及队列有界，不能把整段音频聚集到内存。SSE 的 usage 从有限窗口提取，音频本身不落日志。
- Requests/Route 记录 `speech` 操作、最终 Provider/model、尝试及实际计量；失败尝试与最终成功的计数遵守 REQ-033，不重复计费。配置与用户模型选择不因一次不支持或网络失败被清空。

### 验收

- 正常：真实 HTTP fixture 分别流式返回音频、SSE 和厂商 JSON，第一块在上游完成前到达客户端；模型名正确改写，voice、speed、格式和扩展字段保留，归属头与用量正确。
- 失败：无端点、401、429、5xx、慢首包、半途断流及客户端取消返回定义的状态并释放资源。失败不泄露 fixture key、input 或 instructions；不能把合成测试标成真实 OpenAI/Groq/Mistral 验收。
- 边界：极长音频不触发全量缓冲；SSE usage 横跨 chunk 也能按上限处理；已输出第一块后不调用第二 Provider。启用与关闭遮蔽时只对应文本字段改变，音频和厂商扩展语义不变。

## REQ-108：只读 Gateway key

- 优先级：核心。补全 REQ-035/039/091；权限与预算、模型、账户范围是独立维度。
- 官方依据：[0.1.2159 提交 f2c6831a31819de888f53974a84aba895aafb658](https://github.com/yetone/magpie/commit/f2c6831a31819de888f53974a84aba895aafb658)，固定 [internal/gateway/readonly.go](https://github.com/yetone/magpie/blob/f1151226262022743bd103a78af41e1004cc6055/internal/gateway/readonly.go) 与 `readonly_test.go`。

### 用户行为

Gateway key 新建时可选择只读，已有命名 key 可切换 full/read-only。CLI 提供 `magpie gateway-key add <name> --read-only`、`magpie gateway-key access <id> [read-only|full]` 及不含原始 secret 的 ACCESS 状态列；GUI 提供标签和菜单。调用者使用同一 secret 访问获准统计接口，状态修改在下一请求生效。

允许的读取集合为 GET `/`、`/v1`（及其规范化末尾斜线形式）、`/v1/magpie/quotas`、`/v1/magpie/quotas/history`、`/v1/magpie/quotas/cards`、`/v1/magpie/usage`、`/v1/magpie/usage/requests`、`/v1/magpie/limit` 和 `/v1/magpie/concurrency`。未附加其它范围限制的只读 key 可读该 Magpie 的用量摘要和账户额度；如果同时设预算、模型或账户限制，继续遵守其可见性规则。不能把只读自动等同为仅看本 key 用量。

### 授权、失败与状态保留

- 基于路由器实际匹配的 method 和 handler 身份采用明确 allowlist；未来新路由默认拒绝。所有消费、MCP、WebSocket upgrade、厂商 backend、写入、额度刷新以及 `/v1/models`、Ollama 目录、trace/route、profiling 都不在允许集合。别名、大小写/编码/斜线处理不能绕过授权。
- 在读取正文、预留预算/并发、解析模型、创建 socket、执行插件或连接上游之前，返回 403 和稳定 `gateway_key_read_only` 类错误。合法 key 为只读不意味着失效；拒绝说明需要 full access，不能回显 secret 或用自动提权“修复”。
- 只读仍需要有效、启用的 Gateway key，遵守来源和浏览器保护。管理页面登录 key 与 Gateway key 不互换。内部分享 key 若供本机已接入 Agent 使用，不允许改成只读而破坏内部接入，界面清楚说明限制。
- 配置采用版本化、原子写入；保存失败保留旧权限。更旧实现不认识权限字段时应拒绝使用或迁移，不能无声当作 full access。原版明确存在降级版本忽略新字段的风险，这里用既有 REQ-091 的 fail-closed 约束避免权限放大。
- 修改、停用或删除只读 key 在下一请求生效；统计响应不带其它 key 的 secret、Provider 密钥或完整 prompt。请求日志可记录非秘密 key ID/名称。

### 验收

- 正常：full key 能执行获准模型请求；切 read-only 后上述统计 GET 成功，模型请求立即被拒且上游计数不增加；切回 full 恢复。GUI、CLI 与服务状态一致。
- 失败：key 无效/禁用、配置保存冲突或损坏、未授权来源与浏览器访问分别处理；失败不变更旧权限，错误不含 secret。旧 schema 读取只读权限不得降级为 full。
- 边界：枚举所有注册路由和允许方法，确认每一条均有允许或拒绝结论；新增路由若没有显式分类，测试必须失败。覆盖 loopback/LAN、别名、编码、非规范路径、HEAD/OPTIONS、WebSocket、所有 MCP、POST usage ingestion 和 quota refresh，以及只读叠加账户/模型限制。

## REQ-109：读取 Agent 既有会话的明确许可

- 优先级：核心。扩充 REQ-050/054/077/092。此许可独立于 Gateway conversation recording、Usage request archive、Agent 配置读取和账户登录权限，不能用其中任一开关代替。
- 官方依据：[0.1.2085 提交 af8f3a939979401e9c534bd002bcaa50bd990a85](https://github.com/yetone/magpie/commit/af8f3a939979401e9c534bd002bcaa50bd990a85)，`internal/sessions/access.go`、`internal/settings/settings.go`、`internal/gui/sessions_reads.go` 及固定 [gateway-sessions.md](https://github.com/yetone/magpie/blob/f1151226262022743bd103a78af41e1004cc6055/docs/subsystems/gateway-sessions.md)。

### 用户行为

新安装在获准前不列举、解析或展示已有 Agent 会话正文。Sessions 首次进入显示许可卡片，列出通过目录存在性检查发现的 Agent，默认关闭，用户可允许所选或全部。Settings → Privacy 提供各 Agent 开关和“以后发现的 Agent”默认项；CLI 的 `magpie sessions read [on|off all|<agent>…]` 显示和修改同一设置。未选择时 CLI/TUI 空状态说明原因及如何授权，不把“未允许”误报为没有会话。

授权状态包含是否已询问、全部/未来 Agent 默认及按 Agent 覆盖；明确单项优先于全局默认。它只保存在当前机器，不被同步或备份恢复中的他机设置开启。许可卡片为列出候选而最多 stat 其目录，不能先扫描正文再询问；停止的 WSL distro 不为了显示候选而启动或读取，允许全部的含义需覆盖其后实际发现项。

### 授权、失败与状态保留

- gate 同时位于各 Agent 的 leaf lister、聚合器、索引构建和持有旧会话句柄的内容读取入口。Sessions、Usage 中来自 Agent 文件的调用、搜索、trace、标题、子 Agent transcript 和跨 Agent 续接都受约束；仅从界面隐藏一行不够。
- 关闭某 Agent 后，旧缓存、已列出的 session ID、搜索结果及直接内容 API 都不能继续读取或展示其会话。设置变化不删除 Agent 原文件；需要删除本机副本时提供独立操作，不把撤权等同于删除历史。
- 旧安装迁移只执行一次：已有历史缓存证明先前在读取会话且尚无此设置，可保持原全允许；新安装默认不允许。一旦存在持久选择，后来生成缓存、普通设置保存、同步或恢复都不能覆盖。不能把迁移检测失败或设置写入失败当成“已获准”；不可写时本次运行保持不读取并显示原因。
- Gateway 自己的 usage ledger、投影和已录制会话按其原开关处理，不受“读取别人已有会话”的许可替代；Agent 配置/登录文件仍遵守各自独立授权。原版为额度最近使用与 Codex 安全重启读取极少量时间/最后一行的例外，只能以同等必要性明确列出，不得借此遍历或展示未允许的历史。
- 错误、状态与遥测不得泄露被拒绝 Agent 的标题、路径正文或缓存内容。开启许可不自动开启正文录制、外发遥测或 request archive。

### 验收

- 正常：全新隔离用户目录中放有真实格式 fixture，授权前读取计数为零；允许一个 Agent 后只有该 Agent 能列表、搜索、统计和续接。GUI/CLI/TUI 的选择与提示一致。
- 失败：设置不可写、损坏、并发保存冲突和未知 Agent ID 均不扩张许可；授权保存失败后正文读取计数仍为零。文件不存在或格式错误与“未允许”分别报告。
- 边界：先读取并缓存后撤权，再用旧 ID、旧句柄、搜索、Usage、trace 和 carry-over 访问均拒绝；按 Agent 覆盖胜过 all；重启、同步、restore、后出现的缓存、WSL 与新发现 Agent 不篡改持久选择。旧安装迁移不重复，Agent 文件不被删除或修改。

## REQ-110：Responses WebSocket

- 优先级：核心。扩充 REQ-031/032/037，复用 REQ-030/035/091 的服务与权限；与 REQ-106 的全局 HTTP 续接缓存分开。原 `03` 的 Responses 一律 426 排除条款失效。
- 官方依据：[0.1.2119 提交 ffa07395eb274cbe6cea299d825bf4f5c58db6e2](https://github.com/yetone/magpie/commit/ffa07395eb274cbe6cea299d825bf4f5c58db6e2)，`internal/gateway/responses_ws.go` 及其测试，固定 [gateway-routing.md](https://github.com/yetone/magpie/blob/f1151226262022743bd103a78af41e1004cc6055/docs/subsystems/gateway-routing.md)。

### 用户行为

`/v1/responses`、`/responses` 和 `/backend-api/codex/responses` 接受兼容 Codex 的 Responses WebSocket upgrade。每个 `response.create` 文本 frame 表示一个 turn，复用 HTTP 的模型、路由、转换、工具、masking、计量与错误行为；响应事件逐个以文本 frame 返回。普通 JSON 上游回复也能转换为相应 Responses 事件。

socket 保留最近两个可用回复的输入与输出，支持该 socket 内的 `previous_response_id`，即使客户端指定 `store:false`；这是当前连接临时上下文，不能进入 REQ-106 的 HTTP 持久于连接的缓存。后续 turn 还原完整对话后路由，允许改变 Provider/账户。此能力减少客户端到 Gateway 的上传，不宣称已优化 Gateway 到所有上游的全量上传。

`generate:false` 的 warmup 在本地返回 `response.created` 和 `response.completed`，不触发上游；`response.interrupt` 可取消指定正在执行的回复，返回 accepted 与 interrupted incomplete，错误 ID 或无运行 turn 返回 failed。必要的 turn-state、模型标记、目录 etag 和限额信息以 Codex 可理解的 metadata/rate-limit frame 传回。

### 授权、失败与状态保留

- upgrade 和**每一个 turn**都经过同一完整权限入口，包括连接来源、Host/Origin、key 是否启用、只读状态、模型/账户、预算、速率及并发；不能把建立连接时的一次授权永久复用。key 停用、权限收紧或来源限制修改后下一 turn 拒绝。只读 key 在 upgrade 前拒绝。
- `response.create` 只将协议允许的 metadata 映射为内部 turn header，不能允许 frame 覆盖鉴权或可信代理头。未知、跨 socket、失败回复或淘汰的 previous ID 返回 `previous_response_not_found`，不串用另一连接的历史。
- 每个 turn 的模型请求保持原请求总 deadline，客户端断开或 interrupt 取消上游；长连接本身不长期占住单个模型并发槽。frame、等待队列、每 socket 历史、总 socket 数和总内存有界；其它 frame 在当前 turn 后依协议顺序处理，不能无界积压。
- 被拒绝的 turn 用带 status 和客户端可理解 error 的 frame 返回；Retry-After、限额等只使用允许的 header。上游失败 frame 不产生可续接历史；流没有终态就断开时关闭 1011。binary frame 关闭 1003，不把任意字节当作 JSON。
- 服务开始 drain 后拒绝新 socket 503；已存在 socket 在当前 turn 按有界关闭策略完成后以 1001 结束，并释放全部连接和上下文。socket 断开或 Gateway 重启后临时历史消失，配置、Agent 模型选择及已完成用量不被回滚。

### 验收

- 正常：Codex 协议 fixture 在一个 socket 连续多轮，验证 warmup 零上游调用、后续 delta 还原、Provider 切换、工具/图像和 metadata。HTTP/SSE 原有路径保持兼容，不以 HTTP fallback 冒充 WebSocket 通过。
- 失败：无效 key、foreign Origin、只读权限、未知 previous ID、上游失败、缺终态、binary frame 分别得到定义的拒绝/关闭；interruption 确实取消 fixture 上游，并使容量可供下一请求。
- 边界：连接建立后停用 key 或收紧模型/预算，下一 turn 被拒；跨 socket ID 不可复用；连续三轮验证只保留规定窗口；超大 frame、慢客户端、队列饱和及 shutdown drain 有明确上限与资源释放，不能留孤儿任务或复用被撤销的权限。

## 共同完成条件

每条需求完成时，提交相应正常、失败、边界的自动化证据与适用平台的真实交互验证。需要真实账户、外部客户端或系统集成而尚未运行的项目明确列为未验收，不能删除合同来使测试变绿。新增功能必须接入已有状态、Provider、授权、流控与配置归属机制，避免第二套互相不一致的网络规则、凭据读取或模型目录。
