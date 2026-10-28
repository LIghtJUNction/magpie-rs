# 09 · 安全、隐私与运行环境

## REQ-090：凭据存放和界面遮蔽

- 优先级：核心。Provider API key、OAuth/access/refresh token、Gateway keys、Web keys 和 WebDAV/S3/GitHub credential 在安全储存层/本机凭据库保存，按平台使用受支持的加密存储；配置里尽量仅存引用。
- UI 输入的 secrets 默认遮蔽，复制/reveal 需用户明确操作；日志、CLI默认输出、诊断和普通导出不可含完整凭据。替换凭据后立即用新值请求并显式报告保存情况。
- 本机私有配置文件设合理权限；备份额外用 passphrase encryption；远端通过 TLS。不能将加密 credential 和 archive 的明文副本并存。
- 正常验收：显示 Provider详情时 key 只有尾部掩码；用户替换后详情/下一次调用使用新值。
- 失败验收：安全存储不可读时停止导出/同步/认证操作并说明，不把空 key 误认为用户删除。

## REQ-091：网络入口、鉴权和限流

- 优先级：核心。默认仅本机 loopback；开启 LAN/公网监听时要求用户明确启用并配置访问 key。每个 Gateway key 可指定模型/组白名单、account/Provider 约束、日周月请求/Token/费用 cap、并发和限速。
- Authorization、CSRF、Origin、Host、可信 proxy、web UI cookie、WebSocket 和 CORS 按一套 source-of-truth 检查；不得仅凭未经验证 `X-Forwarded-*` 或相同 Host 免认证。
- 在 Gateway 进入模型上游前完成权限校验。Key 被暂停/删除后新请求即时拒绝。Admin-only quota/history/user routes 不向普通客户端暴露。
- 正常验收：loopback client 本机免 key；远程 LAN 请求只对有效 key 放行并遵守其模型和额度限制。
- 失败验收：无效 key、origin/host spoofing、CSRF 或 client credential 窃取尝试按失败处理并记录 request id，不返回敏感数据。

## REQ-092：Privacy 默认和本机数据

- 优先级：高。prompt/reply 正文记录默认关闭；开启前用户可看到保存位置、保留期/空间上限、进入同步/归档范围与删除方式。Session 元数据与消息正文分开。
- 生产日志仅保留操作/路由/错误元数据，对 URL query、authorization、OAuth回调参数、响应正文和用户 prompt 做排除或 mask。Usage/route trace 在用户授权下可关联 ID，但不可暗中记录正文。
- 操作用户 Agent配置前明确作用范围、备份/撤销方式、相关文件路径。工具从外部导入内容时不把内容当控制指令。
- 正常验收：新安装没有 prompt archive，默认 Logs 不含 messages。
- 失败验收：设置保存失败时 UI 明确显示，不能提示保护设置已成功启用。

## REQ-093：Private URL、代理和 SSRF

- 优先级：核心。支持用户配置上游 proxy 和 Agent 专属代理；显示可用性/失败来源，不泄漏 proxy username/password。保留 DNS/no_proxy 与 loopback 语义。
- 可下载 plugin/catalog/MCP/search page/远程 provider 时，用户可按场景配置私有 hostname/allowlist。对协议、跳转、DNS rebind、私网 IP、file/local scheme 和 credentials in URL 做检查，并对外发响应设置大小/超时。
- 正常验收：用户明确配置的私有 Git/npm/relay 地址可访问并显示来源。
- 失败验收：不可信 webhook/MCP/page 将 HTTP redirect 指向本机 admin endpoint 时，下载器阻止请求并提示 SSRF policy。

## REQ-094：多平台和 WSL 边界

- 优先级：核心。支持 macOS、Windows、Linux；Docker/NAS、headless Linux、WSL distro、Termux 可运行 Gateway/Web/CLI 中受支持的运行模式。桌面平台差异包含 menu bar/tray、系统凭据库和进程更新规则。
- WSL Agent 作为独立运行环境探测 distro 内 binary、home、模型设置和 session。列举 distro 不启动已停止 distro；写入文件只针对用户选定 distro；不能把本机环境变量、目录或 windows token 写进 WSL。
- 本机仅在检测到 Agent CLI/app 确实可用时将其列为 target；共享的 app data folder 可由其它同厂 app 使用，显示来源。停止或卸载的软件路径不作为“活动 Agent”。
- 正常验收：Windows/WSL 中 Claude Code 两套配置可各自连接 Gateway、拥有正确会话归属。
- 失败验收：WSL distro 停止时 UI 显示 last-seen/asleep，不自动启动；系统代理配置不完整时不假称 Agent 经代理连接。

## REQ-095：隐私工具与外部上游

- 优先级：高。外发请求只提供选择的模型、必要的 Agent tool/header 与用户明确启用的插件/搜索服务。不会把其它 Provider 密钥、Agent 指令文件或 Library skills 无差别复制到上游。
- 自动分类、MCP search、图片/视频等工具如需调用外部模型/Provider，应显示使用哪个账户/模型，并遵循 Usage、key 权限、成本限制和用户对 tool 的禁用状态。
- 正常验收：用户请求 image generation 后 Usage 能指出用哪个模型与 Provider。
- 失败验收：用户关闭 search/tool 时，上游不再接收相应隐式工具调用。
