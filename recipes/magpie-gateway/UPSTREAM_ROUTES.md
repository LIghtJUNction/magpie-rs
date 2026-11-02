# 固定原版 Gateway 公开路由合同补录

## 范围、版本与证据

本文件补充 [03 Gateway 合同](specs/03-gateway-and-protocols.md)、[11 新增能力合同](specs/11-upstream-additions.md) 及既有 core/工具需求，不新增 REQ 编号，不替代各条 REQ 的完整验收。目标为 `yetone/magpie` 提交 **`f1151226262022743bd103a78af41e1004cc6055`**（主配方固定的 0.1.2185 源码基线），采集日为 2026-10-30。

依据是该提交的 [Gateway 实际路由注册][routes]、[Gateway 路由文档][routing] 以及本文件逐族引用的处理器。在线按提交读取后，重新计算本地研究缓存的 Git blob SHA：`gateway.go` 为 `2ac025c3d26f5278ef70440d272b428b19f74f28`，`gateway-routing.md` 为 `12c92388e4c3680f2fce97e8bb88da0d95b26974`，均与 GitHub 返回值一致。缓存文件名或发布版号本身不作为版本证明。

**证据等级：固定源码与文档观察；本文件没有上游二进制实测、真实账户验收或 Rust 重建通过结论。** 所列正常、失败、边界场景是待执行验收。精确字段尚未核实的地方明确列为待采集；不能把“注册了处理器”或“底层函数存在”登记为完整兼容。

本次整理仅增加 UTF-8 Markdown 行为合同，不引入 Go 实现或新的运行依赖。已有 core 依赖、许可与来源记录仍由主配方维护。本次没有重新核验全部上游许可证，也没有完成独立配方归档重建。

## 登记口径与共同边界

本表登记 **17 个主要路由族、69 条唯一注册模式**：显式 `GET` 27 条，显式 `POST` 32 条，未在 mux 层限制方法的 `ANY` 10 条。`ANY` 只表示原版 `HandleFunc` 的注册字符串没有 method，**不表示处理器允许任意 HTTP 方法**；实际允许的方法在各族正文说明。Go 注册中的 `/{$}`、`/v1/{$}` 分别记为精确路径 `/`、`/v1/`，不记成子路径通配。

Gemini 的三个操作共用一个 `POST /v1beta/models/{call...}` 注册，统计为一条；Codex backend 前缀也只统计一条。JSON-RPC method、查询参数、实际模型 ID、框架自动处理的 HEAD/CORS、同一 handler 的子操作均不增加注册条数。各个明确别名则各记一条。

固定原版 `routes()` 共有 77 条注册。本表没有展开另外 8 条：Claude 订阅本机进程查询、heap/CPU/goroutine 诊断共 4 条，Omarchy 主题 GET/OPTIONS 共 2 条，Claude 订阅 MCP 工具调用回调（run token）1 条，兜底未知路径 handler 1 条。它们仍是主配方的待审范围；不因本表聚焦公开推理/目录/工具而删除。Go 专属诊断名称不能直接冒充 Rust 的已实现能力。

共同合同：

- 路由和别名执行相同的来源、Host、Origin、Gateway key、模型/账户范围、限额与并发检查。loopback 的判断以真实连接来源和代理边界为依据，不能只看 URL、Host 或自报 header。Web UI 登录凭据与 Gateway key 仍是两种权限。适用 REQ-035/091，以及每个具体 handler 的更严限制。
- REQ-108 的只读 key 依据实际匹配路由判断。模型目录、计数、推理、MCP、WebSocket、backend、usage ingestion 和 quota refresh 均不能因其看似“查询”或改用别名而绕过消费/能力限制。只读 key 与模型/账户/预算 holds 不能混为同一维度。
- API 失败不得将 Provider、Agent 配置或最后有效目录覆盖成空值，也不得偷偷改变用户所选模型。允许保留有界的失败诊断、请求记录和明确归属的任务状态。重试、fallback、计量、session/account 归属继续执行 REQ-032/033/037/045/050/059。
- 各请求体、解压后内容、响应、等待队列、工具输出和后台任务都必须有明确上限。取消和背压沿请求链传播；已经向客户端发出的流或已确认创建的媒体任务不能自动重放。重建可采用不同内部结构，但不能用截断数据、双发请求或无限缓存伪造兼容。
- 只有本表实际登记的别名是固定原版依据。**没有注册 `POST /messages/count_tokens`、`GET /models/{id...}` 或 `POST /systemone`；也没有凭这些处理器推导出 `/v1/videos` 的 DELETE、remix、列表等操作。** 未采集的 API 不应静默转成通用代理。

## 路由族 1：服务信息与客户端握手

映射：REQ-030/031/036/091/108。来源：[注册与 `info`][routes]。

| 注册 method | 路径 | 处理器/用途 |
| --- | --- | --- |
| `GET` | `/` | `info`，精确根路径 |
| `GET` | `/v1` | `info`，兼容客户端检查 base URL |
| `GET` | `/v1/` | `info`，精确带斜线的 base URL |
| `GET` | `/api/hello` | Claude Desktop 第三方 Gateway 握手 |

可重建行为：前三者返回 JSON，已核实字段为 `name`、`version`、`models`（数量）、`window`、`apis`（路径数组）；只有符合 `local` 条件的调用者得到 `pid` 和 `serves`。`/api/hello` 返回 `name: "magpie"` 和运行服务的 `version`。这些是服务端信息，不能用访问它的本机 CLI 版本替代。返回一份信息对象也不单独证明全部 Provider 或真实 Agent 已可用。

- 正常：分别访问四个路径，版本来自同一运行实例；用依赖 base URL 检查的客户端完成握手。
- 失败：错误来源或未经授权的网络请求不能因为访问 root/hello 就得到本机特权信息；服务启动失败不能凭静态 JSON 宣告就绪。
- 边界：区分 `/v1`、`/v1/` 与未登记子路径；远端回复不得带本机 `pid/serves`。`window` 的完整含义及各客户端握手的真实样本仍待采集。

## 路由族 2：通用、单模型和 Agent 专用目录

映射：REQ-016/017/031/034/036/091/108。来源：[注册、`modelObject`、`models`、`model`、`geminiModels`][routes]、[Codex 目录][codex-catalog]、[Muse 目录][muse]。

| 注册 method | 路径 | 处理器/用途 |
| --- | --- | --- |
| `GET` | `/v1/models` | `models` |
| `GET` | `/models` | `models`，明确别名 |
| `GET` | `/v1/models/{id...}` | `model`，可容纳含斜线的模型 ID |
| `GET` | `/muse-code/models` | `museModels` |
| `GET` | `/v1/codex/models` | `codexCatalog` |
| `GET` | `/v1beta/models` | `geminiModels` |

可重建行为：

- 通用 JSON 目录是 `object: "list"`、`data` 数组、`has_more: false`；非空时有 `first_id/last_id`。`?format=text` 返回每行一个可见模型 ID。单模型路径返回模型对象，找不到时为 OpenAI 形态 404。不能让 list 和 detail 走不同权限数据库。
- 默认模型对象至少有 `id`、`object/type: "model"`、`owned_by`、`display_name`、`reasoning`、`supported_reasoning_levels: [{"effort": ...}]`、`reasoning_levels: [...]`。窗口已知时有 `context_window/context_length/max_input_tokens`，输出上限有 `max_output_tokens`；按实际能力提供 `native_endpoints`、`modalities.input/output`。通用 chat 条目还告知 `tool_call`、`supported_parameters`；标签、价格、service tiers 和 Agent 特定能力只在对应条件下附加。未知能力不能编造为已支持。
- 目录按请求 Agent 和 Gateway key 的可见范围生成。显式请求未被 only-picked 列出的模型是否可服务，仍按 REQ-017/034 的授权与显式点名规则判断，不能用 picker 隐藏代替服务端权限。
- Codex 使用 `{"models":[...]}`，不是 OpenAI `data` 列表。固定源码限制回复为 1 MiB；放不下的条目被省略并通过 `X-Magpie-Left-Out` 告知数量，不能截断 JSON，也不能把省略结果写回 Provider/Agent 配置。Codex 单条模型的完整 schema、排序优先级和全部默认值仍待采集。
- Muse 使用 OpenAI 外层列表，单条包含 `metadata["muse-code"]`，提供 `name/family/is_hidden`、attachment/reasoning/tool_call、输入输出 modalities、`limit.context/output`、options、variants 等客户端字段。窗口未知时固定原版采用 128000；输出未知时采用不超过窗口的 32000。客户端缺少该目录或必要 limit 时会拒绝开始会话，普通 ID 列表不能代替它。
- Gemini 目录外层为 `{"models":[...]}`；单条 `geminiModel` 的完整字段和兼容默认值本轮未逐项核验，仍待采集。

- 正常：三个 Provider、不同模型能力、不同 Agent 可见范围和两个受限 key 的目录结果一致；Codex/Muse/Gemini 用各自真实解析器读取成功。
- 失败：目录刷新失败仍保留最后有效配置并区分 stale；具体 stale 的 wire 字段尚未核实，不能发明原版字段名。越权 key 不能借 detail 或 Agent 专用目录获取隐藏模型元数据。
- 边界：Codex 目录越过 1 MiB 时检查完整 JSON、明确省略计数与持久配置不变；模型 ID 含斜线、裸 ID 设置、旧/新组别名不能造成不同授权结果。`auto/<model>`、旧 `group/auto-<slug>`、扁平 `~` 和 Claude 版本拼写的解析按 [模型解析说明][routing] 补入既有 REQ-031/040，不能从路径通配自行推导。

## 路由族 3：OpenAI Chat Completions

映射：REQ-031/032/033/036/037/091。来源：[两个注册共用 `handle(provider.Chat)`][routes]、[协议/路由说明][routing]。

| 注册 method | 路径 | 处理器/用途 |
| --- | --- | --- |
| `POST` | `/v1/chat/completions` | Chat 请求 |
| `POST` | `/chat/completions` | 同一处理器的明确别名 |

可重建行为：JSON 请求带模型和 messages；非流式为 Chat completion，stream 为相应事件流。模型解析、工具、reasoning、usage、图像及跨协议转换执行 REQ-032 全文。别名改变的是入口拼写，不改变模型 ID、鉴权、尝试次数或计量归属。输出 token 字段兼容重试按 REQ-032 的明确上游拒绝条件，不扩展成遇任意 400 重放。

- 正常：同一合成对话、工具调用及流式输入分别请求两入口，除动态 ID/时刻外得到等价协议内容，上游各被调用一次。
- 失败：非法 JSON、未获准模型、上游认证失败和中途断流保留错误阶段；不能先报成功再把失败文本包装成 assistant 回复。
- 边界：取消、背压、零内容但有 tool calls、reasoning-only 增量和 usage-only 末帧均验证；完整参数兼容矩阵尚需协议 fixture，不由本表宣告全字段通过。

## 路由族 4：Responses HTTP/SSE 与 WebSocket

映射：REQ-031/032/033/036/037/106/110/091/108。来源：[注册与 `responsesOverHTTP`][routes]、[固定 WebSocket/续接说明][routing]。

| 注册 method | 路径 | 处理器/用途 |
| --- | --- | --- |
| `POST` | `/v1/responses` | Responses HTTP/SSE |
| `POST` | `/responses` | HTTP/SSE 别名 |
| `GET` | `/v1/responses` | WebSocket upgrade 或非 upgrade 拒绝 |
| `GET` | `/responses` | 同上，明确别名 |

可重建行为：POST 接受 Responses 请求；input、工具结果、reasoning 和流式事件使用 Responses 形态。`previous_response_id` 的作用域、TTL、失效行为与 `store:false` 按 REQ-106；WebSocket 的 `response.create`、顺序、取消和错误按 REQ-110。普通 GET 返回 405 并指出 POST；升级请求由 WebSocket 合同处理，不能把它当普通 GET 查询或无鉴权隧道。

- 正常：两种拼写的 POST/SSE、两个 GET upgrade 及跨 turn 续接保持相同模型/权限边界。
- 失败：未知或他人 response ID、失效续接和失败终态不能成为历史；普通 GET 与 unsupported upgrade 的错误必须区分，遵守 REQ-106/110 具体规则。
- 边界：开启流后不能重放当前 turn；只读 key 不能靠 GET upgrade 获得推理。具体事件 schema 以 REQ-110 的进一步固定 fixture 为准，本文不新增猜测事件。

## 路由族 5：Anthropic Messages 与精确 token 计数入口

映射：REQ-031/032/033/036/037/091/108。来源：[Messages 注册、`countTokens/countOn`][routes]。

| 注册 method | 路径 | 处理器/用途 |
| --- | --- | --- |
| `POST` | `/v1/messages` | `keepsSignIn(handle(Anthropic))` |
| `POST` | `/messages` | 同一登录处理的 Messages 别名 |
| `POST` | `/v1/messages/count_tokens` | `keepsSignIn(countTokens)` |

可重建行为：Messages 的消息块、tool_use/tool_result、thinking、非流式/流式和错误保持 Anthropic 形态。计数响应为 `input_tokens`。固定源码仅在可用且获准的 Anthropic 端点上原生计数；不把 fallback 到另一模型的 tokenizer 当原模型计数。一些订阅/插件走本地估计，不能另用它们的 OAuth token 开辟未授权 HTTP 计数通道。无可用计数候选、无即时 RPM 空位或明确缺失计数接口时可本地估计；普通认证/参数拒绝不能一律掩盖成成功估计。

- 正常：Messages 两别名行为相同；原生计数返回真实 fixture 数值，本地估计分支没有上游请求。
- 失败：计数端点 404/405/501 或明确“不支持 token counting”的 400，与模型/图片/API key 参数错误的 400/401 区分；后者不得因字符串模糊匹配而假成功。
- 边界：请求 `/messages/count_tokens` 应保持未登记行为；受限模型、暂停模型、only-read key 在计数前检查。估计并非供应商精确 tokenizer，不在 UI 或文档中冒称精确。

## 路由族 6：Gemini 生成、流式与计数操作

映射：REQ-031/032/033/036/037/091/108。来源：[实际 `gemini/geminiCount`][routes]。

| 注册 method | 路径 | 处理器/用途 |
| --- | --- | --- |
| `POST` | `/v1beta/models/{call...}` | 模型及操作由最后一个冒号拆分 |

已核实的操作只有 `{model}:generateContent`、`{model}:streamGenerateContent`、`{model}:countTokens`。模型名本身可能含冒号，不能按第一个冒号切分。生成和流式操作沿同一模型路由，保持 Gemini contents/parts、工具和响应形态。计数先执行模型与停用权限检查，再从原始提示本地估计；接受外层 `generateContentRequest` 时取其内部，返回 `{"totalTokens": ...}`，不向上游发送计数请求。

- 正常：同一内容的三个操作命中正确分支；计数 fixture 证明没有网络副作用。
- 失败：无操作分隔符或未知操作返回 Gemini 形态 404；非法 JSON、模型/权限错误不进入生成服务。
- 边界：含斜线/冒号的模型 ID、snake/camel 字段兼容与工具参数文本分别验证；其他 Gemini API 方法、batch/caching 等未被此通配自动承诺，完整支持矩阵仍待采集。

## 路由族 7：Ollama 目录与原生生成

映射：REQ-031/032/033/034/036/091/108。来源：[注册][routes]、[Ollama 目录处理器][ollama]、[转换行为说明][routing]。

| 注册 method | 路径 | 处理器/用途 |
| --- | --- | --- |
| `GET` | `/api/version` | Ollama 协议兼容版本 |
| `GET` | `/api/tags` | Ollama 目录 |
| `POST` | `/api/show` | 单模型能力 |
| `POST` | `/api/chat` | `ollamaChat(false)` |
| `POST` | `/api/generate` | `ollamaChat(true)` |

可重建行为：`/api/version` 的 `version` 是固定原版使用的 Ollama 兼容值 `0.12.6`，不是 Magpie 发布号。tags 为 `{"models":[...]}`，条目含 `name/model`、digest/details/capabilities 等。show 接受 `model`，兼容旧 `name`，返回 capabilities、model_info 等；`general.architecture` 为 `magpie`，上下文未知采用 32768，保留旧 CLI 读取的 parameter_count/embedding_length 字段。不存在一个“GET /api/show”功能合同。

chat/generate 被转换成 Chat 请求并复用原路由；支持消息图片、thinking、工具调用/结果、format、think 与明确映射的 options。默认流式 NDJSON，`stream:false` 为单个对象；末条有 `done`、`done_reason`、token 计数，错误为 `{"error": ...}` 并保留状态。空消息/prompt 是 Ollama warm/load 请求，按 `done_reason: "load"` 处理。不能将这些入口替换为要求用户改用 OpenAI 的提示。

- 正常：Ollama 客户端完成 version → tags → POST show → 原生 chat/generate；流式和非流式都可解析，工具结果能匹配调用。
- 失败：缺模型、非法 JSON、未知模型和无匹配 tool call 的工具结果返回明确错误；目录或生成失败不删除原模型。
- 边界：`:latest` 仅在真实完整名称不存在时回退裸 ID；模型本身就以该后缀命名时不误改。`num_ctx/top_k` 等未映射的 Ollama 本地参数不能宣称已生效，精确 options 矩阵按固定转换样本补齐。

## 路由族 8：Embeddings 与 Rerank

映射：REQ-031/032/033/034/037/059/091/095。来源：[注册][routes]、[retrieval 实现][retrieval]。

| 注册 method | 路径 | 处理器/用途 |
| --- | --- | --- |
| `POST` | `/v1/embeddings` | `retrieve("/embeddings", "embeddings")` |
| `POST` | `/embeddings` | 同一处理器别名 |
| `POST` | `/v1/rerank` | `retrieve("/rerank", "rerank")` |
| `POST` | `/rerank` | 同一处理器别名 |

可重建行为：接收 JSON 对象且要求非空 model。Embeddings 使用 OpenAI 形态，rerank 使用 Cohere/Jina/Voyage 共用的形态；两者都不是 chat，不能经过 chat 消息转换。请求剩余选项交给模型对应 Provider 的 OpenAI-style base 加相应 operation，model 改为上游 ID；按既有隐私规则处理文本，供应商回复保留其协议内容。请求、尝试、token 与路由分别记录。固定源码 `retrieveTimeout` 为 3 分钟；实际部署的总 deadline、重试和响应大小还必须有界。

- 正常：四入口各发同一组含扩展选项的 fixture，观察上游模型 ID、其余 JSON 类型/值及原样结果；确认没有额外 chat 请求。
- 失败：非对象 JSON、空 model 在转发前拒绝；上游 401/429/5xx、超时和错误正文不被包装成空 vectors 或空 rerank 成功。
- 边界：大批量、数字精度、encoding_format 和文档回传的确切兼容范围仍待上游样本验证；在取得样本前不能把字段透传等同于所有供应商均接受。更改权限后下一请求只使用仍获准成员。

## 路由族 9：图片生成与编辑 HTTP API

映射：REQ-031/032/033/037/049/059/091/093/095。来源：[注册][routes]、[`drawing/readDrawing/images`][images]。

| 注册 method | 路径 | 处理器/用途 |
| --- | --- | --- |
| `POST` | `/v1/images/generations` | `images(false)` |
| `POST` | `/images/generations` | 生成别名 |
| `POST` | `/v1/images/edits` | `images(true)` |
| `POST` | `/images/edits` | 编辑别名 |

可重建行为：接收 JSON 或 multipart。已核实字段有 `model/prompt/size/quality/background/output_format/response_format/n`；JSON 的 image/images/mask 可给图片来源，multipart 支持 image、image[] 文件与 mask。prompt 不能为空；n 不大于 4，未给或不大于 0 时原版取 1；edit 缺少输入图片为 400。省略 model 时按已有图片模型设置选择，不是新建默认 Provider。按所选 Provider 的图片能力适配 images、Gemini 或明确支持的 chat 图片 API。

原版成功 JSON 包含 `created/model/data/usage`；每张图给 `url` 或 `b64_json`，内联图还带 `mime_type`，可能带 `revised_prompt`，模型附带文本时有 `text`。不能把生成结果保存到 Agent 项目后才宣称 HTTP 成功；文件落盘属于明确调用该能力的 CLI/工具流程。

- 正常：JSON 生成、multipart 编辑、多个参考图、URL/b64 响应各有 fixture；上游模型和最终实际 Provider 一致，临时 multipart 文件被清理。
- 失败：无 prompt、edit 无图片、n 超限、禁用/无权模型和 vendor 错误明确返回，失败不清除图片模型设置；图片下载失败不静默丢弃结果。
- 边界：调用者 URL、重定向、DNS 变化与供应商返回 URL 服从 REQ-093；不能因请求来自 loopback 就任意访问私网。完整 MIME/size/quality/mask 供应商矩阵、所有图片 JSON 来源写法仍待采集，不将所有字段当成所有上游均支持。

## 路由族 10：视频启动、状态与内容

映射：REQ-031/032/033/037/049/059/091/093/095。来源：[注册][routes]、[完整视频处理器][videos]、[账户与 caller 说明][routing]。

| 注册 method | 路径 | 处理器/用途 |
| --- | --- | --- |
| `POST` | `/v1/videos` | `videosCreate` |
| `POST` | `/videos` | 启动别名 |
| `GET` | `/v1/videos/{id}` | `videosGet` |
| `GET` | `/videos/{id}` | 状态别名 |
| `GET` | `/v1/videos/{id}/content` | `videosContent` |
| `GET` | `/videos/{id}/content` | 内容别名 |

可重建行为与必要 shape：

- 启动接受 JSON 或 multipart，读取 model、prompt、seconds、size、input_reference、last_frame、reference_images；保留自定义 OpenAI-style 视频上游所需的其他字段。prompt 必须非空。seconds 可由 `6`、`"6"`、`6.0`、`6e0` 表达，归一为正整数；无穷、非整数、非法数字、超出范围拒绝。供应商实际长度限制仍由它判断。
- 请求创建任务后返回任务 ID，后续 poll/content 使用该 ID。原生供应商被规范成 `object: "video"`、id、created_at、status、progress、model，按已知数据附加 prompt/size/seconds/error；状态包括 queued/in_progress/completed/failed。OpenAI-style/Remote 回复保留供应商对象并替换本地 id/model，不能假定所有原生字段与所有上游附加字段完全相同。
- 明确的 video 能力可来自订阅、特定计划、MiniMax API key、Remote Magpie、自定义 Provider 或插件。自定义模型需目录 `kind: "video"` 或用户显式标记；插件按声明的视频输出能力。不能仅凭模型名含 video 推断能力，也不能只允许订阅账户。
- ID 绑定 Provider、供应商任务和必要的 key/account 标记。自定义/插件/OpenAI-style 任务、MiniMax 及混合计划按原版对应分支保留发起时 key/account；原生订阅还核对 signer，账户更换或发起 key 不再启用时不得悄悄换一个账户取回任务。每次状态/内容请求重新检查当前 caller 的模型和账户权限。OpenAI-style/Remote 分支在 caller 有模型限制而供应商未说明任务模型时拒绝；原生 `videosGet/videosContent` 分支缺 model 时则采用 `grok-imagine-video` 再执行权限检查。必须分别保留这两种固定源码行为，不能误写成所有分支均拒绝缺失 model，也不能靠猜 ID 绕过账户与模型权限。
- **不要混淆三种归属**：上游任务所属 key/account；当前 Gateway caller 的许可与用量标签；MCP 后台 job/file 的创建 KeyID。直接 HTTP video API 的本轮源码依据是 task 绑定加当前模型/账户 holds，不足以宣称“一切任务都仅创建 Gateway key 可读”。MCP 媒体 job/file 的严格创建 KeyID 隔离在路由族 14 单列。
- 视频启动形成一次媒体 Route/Usage 归属；轮询和下载不被当作新的生成任务重复计费。固定源码每次 vendor start/status/content 调用上限为 5 分钟，内容转发上限 512 MiB；任务生成期间不靠保持一次 POST 连接直到完成。供应商任务寿命与重启后的所有账户组合仍待实测。

- 正常：创建 → queued/in_progress → completed → content，两个别名组保持同一任务；上游两账户 fixture 验证后续访问绑定，保持 prompt/额外字段、最终 Provider/model 和 caller 归属。
- 失败：无能力、创建拒绝、账户撤销、任务过期、未知 ID、供应商坏 JSON 分别保留错误；未完成的原生任务读取 content 为 409，完成但没有内容 URL 为 502。失败轮询不重建任务，不将失败改写为 completed。
- 边界：创建后切换 Provider 首选 key，再 poll/content；当前 caller 被撤销该账户或模型；供应商响应遗漏 model；跨 Gateway key 的允许/拒绝按真实权限测试，不冒用 MCP 的 owner-only 结论。下载的私网/重定向与超过 512 MiB 分支要阻断或有界失败，配置和已有 task ID 保留。

## 路由族 11：音频输入与语音输出

映射：REQ-031/032/033/037/049/059/091/095/107。来源：[注册][routes]、[retrieval/audio 行为说明][routing]、REQ-107。

| 注册 method | 路径 | 处理器/用途 |
| --- | --- | --- |
| `POST` | `/v1/audio/transcriptions` | `transcribe` |
| `POST` | `/audio/transcriptions` | 转录别名 |
| `POST` | `/v1/audio/translations` | `transcribe` |
| `POST` | `/audio/translations` | 翻译别名 |
| `POST` | `/v1/audio/speech` | `speak` |
| `POST` | `/audio/speech` | 语音输出别名 |

可重建行为：音频输入使用 OpenAI multipart，保留音频字节、boundary 和用户输出格式，改变上游模型名，并仅按隐私规则处理 prompt。语音输出使用 JSON；模型名改为上游 ID，input/instructions 按设置遮蔽。回复可能是音频或 SSE，要边读边传；不能完整缓冲后才假装流式。其他字段、格式及各供应商限制沿 REQ-107，未采集格式不能默默转换或假报支持。

- 正常：各路径与别名使用同一个音频 fixture，比较传出音频 SHA 和文本字段；speech 分块到达时客户端及时收到，provider/model 归属一致。
- 失败：无兼容端点、无权模型、multipart 损坏、401/429/5xx、音频中途断开明确失败，禁止已开始输出后的重播。
- 边界：未知长度上传、大小上限、客户端取消、SSE 文本与二进制分别处理；完整 response_format/content-type 矩阵仍待采集并与真实 SDK 验证。

## 路由族 12：System One 决策

映射：REQ-031/033/037/047/059/091/095。来源：[固定注册][routes]、[Remote/决策行为说明][routing]；既有 [REQ-047](../magpie-core/specs/04-routing-and-failover.md)。

| 注册 method | 路径 | 处理器/用途 |
| --- | --- | --- |
| `POST` | `/v1/systemone` | `serveSystemOne` |

可重建行为：调用 decision 模型或 decision group，保留决策状态、问题及上游结果，不当成聊天生成；账户、模型权限和尝试归属与普通请求一致。决策组只含决策模型，分类 timeout 覆盖整个候选尝试，不能每次失败重新获得完整等待时间。请求/回复的**完整 JSON shape、状态字段与概率校验本轮尚未逐项采集**，必须在实现前补固定 handler/test fixture，不能用通用 Chat schema 代替。

- 正常：按已补采的固定决策请求样本得到可用决策与同一条 Route，单 Provider 多 key 和决策组都执行既有顺序。
- 失败：聊天请求误投决策组、非法选项/坏结果、全部账户拒绝和分类超时明确失败；失败不把空决策写成用户新规则。
- 边界：只读或受限 key 不得消费分类模型；候选切换不延长总 timeout。未登记 `/systemone` 别名。

## 路由族 13：Codex Web Search 协议

映射：REQ-031/033/037/048/049/091/095。来源：[注册][routes]、[Alpha Search handler][alpha-search]。

| 注册 method | 路径 | 处理器/用途 |
| --- | --- | --- |
| `POST` | `/v1/alpha/search` | `alphaSearch` |
| `POST` | `/alpha/search` | 明确别名 |

可重建行为：这是 Codex `web.run` 的协议，不是任意网页代理。已核实请求读 `model`、`commands.search_query`、`commands.image_query`；查询条目读 `q/domains`。回复包含 `output`，可能有 `encrypted_output`。ChatGPT 账户模型使用其 backend search；其他模型的 search_query 使用 Magpie 已配置搜索。工具操作不能完成时可在 HTTP 200 的 output 中说明，避免 Codex 将失败状态视为整 turn 终止；外层未认证/越权拒绝仍按权限合同处理，不能一概伪装成 200。

- 正常：两个路径分别测试 ChatGPT backend 与普通搜索 fixture，返回 Codex 能读取的输出并保持 caller/session 和选定账户。
- 失败：无搜索能力、服务故障在工具结果中有明确原因；Gateway 认证失败/受限模型在调用工具前拒绝，不能泄露搜索服务 key。
- 边界：原生账户切换的 401/403/429/5xx 与普通搜索的失败不同；image_query 的所有 fallback 能力、encrypted_output 内部格式与完整 commands schema 尚未核验，按 opaque 数据保留，不能臆造解密或兼容结果。

## 路由族 14：独立搜索、视觉、媒体与插件 MCP

映射：REQ-031/049/065/068/091/093/095/108。来源：[注册][routes]、[搜索/视觉/图片 MCP 文档][routing]、[插件工具 handler][tools-mcp]、[图片 MCP handler][image-mcp]。

| 注册 method | 路径 | 处理器实际方法/用途 |
| --- | --- | --- |
| `ANY` | `/mcp/magpie/web-search` | POST JSON-RPC，搜索与抓取 |
| `ANY` | `/mcp/magpie/vision` | POST JSON-RPC，图片理解 |
| `ANY` | `/mcp/magpie/image` | POST JSON-RPC，生成与后台结果 |
| `ANY` | `/mcp/magpie/image/files/{name}` | GET/HEAD 图片读取，其余 405 |
| `ANY` | `/mcp/magpie/tools` | POST JSON-RPC，用户插件工具 |

可重建行为：

- 独立 MCP 服务支持 Streamable HTTP 的 initialize、ping、tools/list、tools/call；JSON-RPC 请求或 batch 返回 JSON，notifications 为 202。这些独立服务没有自己的 SSE session，GET/DELETE 为 405；文件路由例外，明确只接受 GET/HEAD。不能仅因 mux 是 ANY 就开放任意方法。
- web-search 提供 web_search/web_fetch，vision 提供 analyze_image，image 提供 generate_image/generate_video/generation_result；tools 列出插件实际提供的工具和 schema，并在插件关闭时不列出、不执行。它们不被 Library Hub 开关替代，也不自动列入某 Agent 的 Hub 分配。
- web_fetch 按调用者提供的 URL 下载公开地址，解析/固定 DNS，每次跳转重新检查；最多 30 秒、5 MiB、五次跳转。HTML 变为可读 Markdown，其他文本保留，非文本/HTTP 错误为工具错误；支持 offset/max_chars（默认 20000、最多 100000）。未知编码/抓取边界仍需 fixture 验证。
- 工具调用保留当前 caller/key 并单独预留限额。默认没有调用即不执行模型或插件。有效 Gateway key 不自动允许浏览器 Origin；MCP 按已有允许来源规则再检查，不能把 localhost 网页等同本机 Agent。
- 搜索请求体上限 1 MiB；视觉和图片 MCP 32 MiB；插件工具 4 MiB。超大 batch 必须在任何子调用前整体拒绝。完整工具输入 schema 和所有 JSON-RPC 扩展 method 仍待采集；已见 `magpie/media_model` 也不是一条新的 HTTP 路由。
- 图片工具保留 in-process 请求的 caller/key，使用相同 images/video API。图片 job 最长 15 分钟，20 秒内尚未完成则返回可查询 ID；输出可含 MCP image content（单图最多 5 MB）与文件 URL。文件保存在内存中最多 1 小时、合计 256 MB，job/file 按创建 Gateway KeyID 隔离；别人的文件即使知道 name 也返回 404。GET/HEAD 带正确 Content-Type、nosniff、private cache 和 Content-Length；HEAD 无正文。文件和任务不写入 Agent 项目。视频返回的内容 URL 仍执行视频自身权限。

- 正常：分别发现并调用五个路径的正确能力；相同输入通过 MCP 与 HTTP 媒体入口保持模型、权限和计量归属，GET/HEAD 文件长度一致；工具禁用后立即失效。
- 失败：无搜索器/视觉模型、媒体关闭、插件离线、超时、坏 JSON-RPC、超体积、未知工具和任务过期明确返回错误；失败不清空插件/Library 配置。
- 边界：两个 key 互换 job/file ID、未允许 Origin、私网跳转、超大 batch、后台任务超时及客户端取消分别验收。MCP 图片异步任务可按已定义契约继续，但不能将这一例外推及所有模型流或插件副作用；未核实的恰好执行一次语义保持未知。

## 路由族 15：Library MCP 代理、命令入口与 Hub

映射：REQ-031/065/072/091/095/105/108。来源：[注册][routes]、[独立 MCP 代理/命令 handler][mcp-proxy]、[Hub handler][hub-mcp] 和 REQ-105。

| 注册 method | 路径 | 处理器实际方法/用途 |
| --- | --- | --- |
| `ANY` | `/mcp/{name}` | 已配置并登录的远程 MCP proxy |
| `ANY` | `/mcp/run/{name}` | Library 提供的本机命令 MCP handler |
| `ANY` | `/mcp/magpie/hub/{agent}` | POST JSON-RPC，Agent 分配子集 |
| `ANY` | `/mcp/magpie/hub` | POST JSON-RPC，Other apps Hub |

可重建行为：

- `/mcp/{name}` 使用保存的 MCP 登录代远端签名，支持远端的 POST JSON/事件流、GET 事件流、DELETE session 结束及 Mcp-Session-Id。name 解析已配置服务器，不能把它解释成任意 URL/凭据。其 Origin 规则比普通本机 HTTP 更严：有 Origin 的网页请求被拒绝；远端调用还需共享与有效 key。
- `/mcp/run/{name}` 只服务本机 Agent，要求真实 local、没有 Origin、loopback Host；即使远端有 key 也不能使用该入口启动本机 Library 命令。运行函数由 Library 提供，未提供时 404；不接受请求直接传新 command/env。此 handler 的全部方法/session 行为本轮未采集，不能从 ANY 推定支持远端代理的所有方法。
- Hub 默认关闭，Agent 路径仅暴露被分配的 Library 服务器，Other apps 路径按 REQ-105 暴露 Library 集合。工具名为 `<server>__<tool>`，保留 schema/result，不能声称支持尚未实现的 resources/prompts。开关、禁用、分配、进程共享/退出和跨入口变更严格按 REQ-105，不能复用独立插件 MCP 的名单代替 Library。
- Hub 请求体 4 MiB；固定 handler 的列举等待 40 秒、调用等待 50 秒。其他生命周期约束沿 REQ-105。命令入口更严的本机限制不能被 Hub 的授权模型抹平，Hub 本身的远端共享也必须来自显式启用与独立权限。

- 正常：同一已登录远端 MCP 通过 proxy 完成会话；本机命令由既有 Library 配置启动；两个 Agent 的 Hub 工具子集准确且共享后端进程。
- 失败：登录失效、缺命令、Hub 关闭、工具禁用、后端超时或 malformed schema 不改写原 Library/Agent 配置，不让其他服务器一起变成失效。
- 边界：远端 key 访问 run、网页访问 proxy、Agent A 调 B 未分配工具、同名独立 MCP 与 Hub 路径冲突均拒绝或正确隔离；不能通过一个泛化 `/mcp/*` handler 擦除各入口差异。

## 路由族 16：Codex 内置 Provider backend 适配

映射：REQ-012/016/031/032/033/036/037/091/106/110。来源：[前缀注册][routes]、[Codex backend handler][codex-backend]。

| 注册 method | 路径 | 处理器/用途 |
| --- | --- | --- |
| `ANY` | `/backend-api/codex/` | `codexBackend` 前缀处理器 |

可重建行为：Codex 内置 OpenAI Provider 通过其 openai_base_url 到此，保留用户自身登录；属于它的原生模型请求转向对应 backend，Magpie 模型进入共享路由，模型列表合并兼容条目。Responses HTTP/续接/upgrade 与 REQ-106/110 相连，但这些只是此前缀的一部分。

该入口是 **Codex 协议与账户适配器，不是任意反向代理**。目标主机、认证、允许操作由真实 backend/Provider 配置与账户决定，不能由请求传一个 URL、Host 或路径后缀就任意转发。当前只确认前缀注册、原生/Magpie 模型分流、目录及 Responses 关联；完整子路径、method、compaction 与其他 backend 操作 shape 仍待逐项采集，不能凭前缀匹配宣告全套 backend 已兼容。

- 正常：使用隔离的 native backend 与 Magpie 上游 fixture，明确记录每种模型去哪一个、使用哪类授权，Responses 经此入口仍满足相同流式与 caller 契约。
- 失败：登录失效、越权模型、未知/未实现子操作和不支持 upgrade 明确失败，不清除用户原生登录，不回退到另一个未授权账户。
- 边界：请求试图注入目标 URL、转发凭据到其他主机、借 backend 路径绕过 readonly/model holds 时应拒绝。只有采集并验证的操作才进入完成范围，不能把“万能透传成功”当验收。

## 路由族 17：Usage、Quota、Route 与 Trace

映射：REQ-033/037/039/046/050/051/052/053/059/091/108。来源：[注册与状态处理器][routes]、[Remote API 权限说明][routing]、[额度卡路径常量][remote-quotas]。

| 注册 method | 路径 | 处理器/用途 |
| --- | --- | --- |
| `GET` | `/v1/magpie/quotas` | `quotas` |
| `GET` | `/v1/magpie/usage` | `usageSummary` |
| `GET` | `/v1/magpie/usage/requests` | `usageRequests` |
| `POST` | `/v1/magpie/usage/requests` | `usageIngest`，写入而非查询 |
| `GET` | `/v1/magpie/quotas/history` | `quotasHistory` |
| `GET` | `/v1/magpie/quotas/cards` | `quotaCards` |
| `POST` | `/v1/magpie/quotas/refresh` | `quotaCardsRefresh` |
| `GET` | `/v1/magpie/route` | `sessionRoute` |
| `GET` | `/v1/magpie/trace` | `gatewayTrace` |
| `GET` | `/v1/magpie/concurrency` | `concurrency` |
| `GET` | `/v1/magpie/limit` | `keyLimit` |

可重建行为：

- 查询使用与 UI/CLI 一致的时间窗口、筛选与账务来源。`/route?session=...` 按 REQ-037 返回同一会话最近路由，尚无路由为 null；不得从 prompt/IP/用户 key 伪造 session。各 usage/quota 完整 JSON schema、分页字段、ingestion 请求/响应 shape 本轮未逐项核实，保留为采集阻塞，不能以空对象/零值定义兼容。
- quota cards GET 只读已有缓存，不为了列目录自动询问供应商。**刷新准确路径是 `/v1/magpie/quotas/refresh`，不是 `/quotas/cards/refresh`**；`?provider=` 与 `&user=` 选择重读对象，不给 provider 时重读全部再返回卡片。离线失败保留最后有效卡片与未知/stale 语义，不能把一次失败存成额度归零。
- 受模型/账户/预算 holds 的 key 不能获得全局额度、账户和并发信息，使用稳定 `gateway_key_held` 类错误；没有这些 holds 的只读 key 仍按 REQ-108 允许读取规定统计。limit 只反映当前请求携带的 key，不能从 query 指定任意其他 key；固定原版同时保留 `limit` 与多窗口 `limits`。
- trace 比一般统计严格：只允许真实 local，拒绝带转发头的 loopback、LAN/共享请求，即使持有效 key；它可能包含账户/email、会话、工作目录末两段及休眠原因，不能假称匿名公共遥测。按 `after/wait` 可长轮询；具体结构和等待边界仍待采集。它不包含 prompt/key，并带服务进程 pid/serves。
- POST usage ingestion 与 POST quota refresh 都是写入/外部工作的入口；REQ-108 明确拒绝 readonly。ingestion 的去重与验证需核实后落实 REQ-059，不能把外部任意数字无验证计入总账。

- 正常：CLI/UI/API 对同一 synthetic request/attempt fixture 得到一致统计；GET cards 没有上游调用，显式 refresh 才有；route/trace 保持同一个可确认 session 与实际 Provider。
- 失败：非法筛选、坏 ingestion、无效/受限 key、quota 离线分别明确失败或保留旧值，不清空历史、泄露其他账户或把无价格记为免费。
- 边界：readonly 与 holds 的组合、POST/GET 方法互换、别名/编码路径、跨 key limit 查询、长轮询取消和重复 ingestion 都需验收；未明确采集的格式与幂等条件保持未知。

## 尚需采集与完成判定

本文件补齐的是固定原版的入口边界与已观察行为。以下内容仍阻止“全部公开 API 已完整对齐”的结论：

| 待采集项 | 已有依据 | 仍缺内容 |
| --- | --- | --- |
| 专用目录 | Codex/Muse/Gemini 路径、外层与部分元数据 | Codex 完整条目 schema/排序；Gemini 单条完整字段；真实 Agent 多版本解析 |
| 四协议生成与转换 | 入口、别名及既有 REQ-032/106/110 | 全参数/多模态/事件/错误/取消矩阵及真实上游限制 |
| 检索、图片、音视频 | 请求处理方式、关键字段、media 生命周期 | 完整供应商选项、格式/编码、账户组合与实际 SDK 行为 |
| System One 与 Alpha Search | 决策语义、search 已读字段、明确路径 | 决策完整 JSON；Alpha 完整 commands/image fallback/encrypted_output 契约 |
| MCP 与后台副作用 | 各路径独立权限、核心 method、已知界限 | 全工具 schema、run handler 方法/session、私有扩展、故障窗口的副作用/结果一致性 |
| Codex backend | 固定前缀、分流目的、Responses 关联 | 全部子操作/method/shape，不能泛化任意反代 |
| Usage/Quota/Trace | 权限、读/写区别、准确刷新路径 | 全 schema、筛选/分页、ingestion 去重与 trace 长轮询的确切边界 |

验收应使用隔离 HOME、合成账户、明确请求记录的本地上游，保留 method/path、headers、必要原始字节、状态/响应、请求次数、配置前后状态和重启后结果；真实 Provider/Agent 结果单独记录，不能由 synthetic 成功推定。字段未核实先补固定证据或标记未实现，不能以推测 JSON 建一个看似成功的 endpoint。

统计核验：本文件每个路由表只列一条 method/path 注册一行，共 **69 行、17 个路由族；27 GET + 32 POST + 10 ANY**。这一计数表示已登记原版目标，不表示 Rust 已实现 69 条，不表示所有 handler 分支均已采集或验收。实现覆盖继续逐条回填主配方已有 REQ；不得另造一套 REQ 或删掉未完成能力。

## 固定资料索引

[routes]: https://github.com/yetone/magpie/blob/f1151226262022743bd103a78af41e1004cc6055/internal/gateway/gateway.go
[routing]: https://github.com/yetone/magpie/blob/f1151226262022743bd103a78af41e1004cc6055/docs/subsystems/gateway-routing.md
[codex-catalog]: https://github.com/yetone/magpie/blob/f1151226262022743bd103a78af41e1004cc6055/internal/gateway/codex_catalog.go
[muse]: https://github.com/yetone/magpie/blob/f1151226262022743bd103a78af41e1004cc6055/internal/gateway/muse.go
[ollama]: https://github.com/yetone/magpie/blob/f1151226262022743bd103a78af41e1004cc6055/internal/gateway/ollama.go
[retrieval]: https://github.com/yetone/magpie/blob/f1151226262022743bd103a78af41e1004cc6055/internal/gateway/embeddings.go
[images]: https://github.com/yetone/magpie/blob/f1151226262022743bd103a78af41e1004cc6055/internal/gateway/draw.go
[videos]: https://github.com/yetone/magpie/blob/f1151226262022743bd103a78af41e1004cc6055/internal/gateway/video.go
[alpha-search]: https://github.com/yetone/magpie/blob/f1151226262022743bd103a78af41e1004cc6055/internal/gateway/codex_search.go
[tools-mcp]: https://github.com/yetone/magpie/blob/f1151226262022743bd103a78af41e1004cc6055/internal/gateway/tools_mcp.go
[image-mcp]: https://github.com/yetone/magpie/blob/f1151226262022743bd103a78af41e1004cc6055/internal/gateway/image_mcp.go
[mcp-proxy]: https://github.com/yetone/magpie/blob/f1151226262022743bd103a78af41e1004cc6055/internal/gateway/mcp.go
[hub-mcp]: https://github.com/yetone/magpie/blob/f1151226262022743bd103a78af41e1004cc6055/internal/gateway/hub_mcp.go
[codex-backend]: https://github.com/yetone/magpie/blob/f1151226262022743bd103a78af41e1004cc6055/internal/gateway/codex_backend.go
[remote-quotas]: https://github.com/yetone/magpie/blob/f1151226262022743bd103a78af41e1004cc6055/internal/provider/remote_quotas.go
