# 重建 Magpie Gateway

用 Rust 完成 specs/03-gateway-and-protocols.md、05-usage-and-sessions.md、06-plugins-and-tools.md、11-upstream-additions.md；完整阅读 UPSTREAM_ROUTES.md 与 ACCEPTANCE.md。新增规格按原版 0.1.2185 固定源码和官方日志提取，保留已有需求编号。

## core 依赖合同

依赖 `core` 是完整 magpie-core 软件配方，归档位于 .deps/core/。先重建它的 Agent/Provider/catalog/路由/安全/Library 状态操作。Gateway 通过它执行授权与选路、读取凭据引用和变更通知；不复制 Provider 数据库，不以 Host 或伪造转发头获得本机免认证。集成验收：暂停账户/撤销 key 后下一请求拒绝或换到获准账户，CLI 和服务读相同 quota snapshot。

## 本层接口与工作流

实现默认 127.0.0.1:3425 的生命周期；支持规格规定的 Chat、Responses HTTP/SSE/WebSocket、Messages、Gemini、语音输入/输出、模型目录和 Ollama 原生 Chat/Generate/NDJSON。旧配方的 Responses upgrade 一律 426 与排除 TTS 约定已被 REQ-107/110 取代，Ollama 原生生成的旧排除也按固定源码纠正；Rust 目前缺失时必须明确报告未实现。按客户端协议一次转换，保留工具、reasoning、usage 和上下文；取消和背压传播到上游，禁止完整 SSE 多份缓冲和无限重试。

UPSTREAM_ROUTES.md 登记 17 个路由族、69 条唯一注册模式，补齐图片生成/编辑、embedding/rerank、视频任务、精确别名、客户端握手、专用模型目录、Codex backend、搜索/MCP、额度刷新与观测入口。该数量是待重建目标，不能当作 Rust 已实现数量。逐族执行其正常、失败和边界验收；mux 未限制 method 不等于 handler 接受任意方法。未采集的完整 schema 与客户端样本先补证据，不猜字段，不退化成任意反向代理。

每个 request 与 attempt 有稳定关联；最终成功/失败只计一次，上游尝试分别计量，未知 usage/价格保持 unknown。会话只来自确认的原生 ID/header，prompt 正文默认不记录。实现 Usage 查询、导出、保留、quota、masking 与受限 key 可见范围。

支持社区 OpenCode 插件与内置 feature plugin 的不同生命周期/状态/扩展点。退休订阅由其插件执行登录/刷新/请求/Usage；迁移单向且保留原登录材料。具体 npm 包、JS runtime 与服务账号是外部前提，不是配方依赖。超时、取消、插件卸载和异常都释放注册项/连接/资源，不重复转发。

向 interfaces 层提供一致、受授权的服务状态、变更与查询操作；任何网络管理端点都必须执行来源/Host/Origin/CSRF/授权边界。HTTP 与内嵌库可共享引擎，不要求某个 Web 框架或线程池。以低内存与有界并发设计，所有限制和超时要可说明、可验收。
