# 需求覆盖索引

99 个唯一 REQ（原有 93 项与新增 105–110）；这是完整功能审计和需求追踪，不表示软件全部通过验收。所有条目均保留完整端到端验收要求。2026-10-30 增加共享 Provider、系统凭据、实时网关与原生 UI 的实现证据；此前被否决的 UI 不作为通过基线，本轮实际 GPUI 渲染仍需用户视觉认可。

配方 1.3.5 新增 [逐任务对照与证据规则](PARITY_VERIFICATION.md)，按此表的编号核对覆盖索引，93 项均有任务映射；这是配方追踪覆盖，不表示实现覆盖或原版行为已经全部采集。原版用户任务的未知项、独立重建、完整平台及真实账户仍须逐项实测。

## 本轮逐项审计（固定原版 0.1.2185）

原有 93 条逐项审计已在源码仓库的 `docs/feature-audit-core-2026-10-30.md`（46 条）和 `docs/feature-audit-interfaces-2026-10-30.md`（47 条）记录入口、实际行为、已有证据、缺口和下一条验收；两份审计以本轮修改前的合并提交为基线。当前增量结果另见 `docs/changelog-parity-2026-10-30.md`，不可将基线缺口或旧测试结果套到未验证的新代码。

状态口径：**部分**表示已有可使用的子流程，但完整合同尚未通过；**仅组件**表示有局部计算/默认保护，尚未贯通产品工作流；**未实现**表示该用户能力没有实现。全量基线为部分 39 项、仅组件 10 项、未实现 50 项（含新增六项），没有一项达到全平台、全部入口和真实外部兼容的完整验收。不能据此换算完成百分比。

新增编号由 [官方更新日志逐发布映射](UPSTREAM_CHANGELOG.md) 确认。237 条发布包含旧二进制基线一条与之后 236 条；相对旧源码 dca3 的增量为 37 条。旧编号和 68 Agent 最低目录全部保留。

| REQ | 需求 | 所属配方 | 归档内规格 | 状态 |
| --- | --- | --- | --- | --- |
| REQ-001 | 只显示可用 Agent | magpie | `./GLOBAL.md` | 部分：CLI 使用隔离 PATH 验证只列出已安装支持项；桌面用户验收未通过 |
| REQ-002 | 保持用户已有数据 | magpie | `./GLOBAL.md` | 部分：Codex首次恢复点、连续换模、未知字段保留、完整文件revision与中断恢复fixture已测；其他Agent、多文件事务、真实断电及全部平台未验收 |
| REQ-003 | 显式连接与断开 | magpie | `./GLOBAL.md` | 部分：CLI/桌面支持Codex连接、已连接换模、预览保存与离线恢复；真实CLI桥接闭环已测，其他Agent和原生窗口操作未验收 |
| REQ-004 | 统一模型选择和每 Agent 可见列表 | magpie | `./GLOBAL.md` | 部分：Codex 真实模型选择使用统一 Provider/model 目录并虚拟化长列表；逐 Agent 可见性和其他 Agent 应用未实现 |
| REQ-005 | 用户可保存和切换 Profile | magpie | `./GLOBAL.md` | 未实现：CLI 和桌面均无 Magpie Profile 存储、命令或页面；现有 Codex 单配置事务不包含跨 Agent 设置快照与切换。 |
| REQ-010 | 多个入口反映同一状态 | magpie-core | `.deps/interfaces/.deps/gateway/.deps/core/specs/01-product-and-agents.md` | 部分：CLI/GPUI/Gateway共用ProviderStore与Codex事务；新增桌面拥有的服务与只读外部服务状态；Web/TUI/托盘及全产品共享状态仍缺 |
| REQ-011 | 发现和列出 Agent | magpie-core | `.deps/interfaces/.deps/gateway/.deps/core/specs/01-product-and-agents.md` | 部分：68项目录中保留原五项并新增八项无歧义 PATH 候选，CLI/桌面共用入口与隔离 fixture 已测；命中不等于配置可用，其他 Agent 配置和完整操作流程未实现 |
| REQ-012 | 兼容 Agent 的公开配置格式 | magpie-core | `.deps/interfaces/.deps/gateway/.deps/core/specs/01-product-and-agents.md` | 部分：Codex公开TOML配置的有界读取和事务已测；其他Agent格式及版本兼容未实现 |
| REQ-013 | 只做精确、可恢复的字段编辑 | magpie-core | `.deps/interfaces/.deps/gateway/.deps/core/specs/01-product-and-agents.md` | 部分：Codex精确字段事务、首次原配置保留、换模v2 journal、私有恢复映像及并发/中断fixture已测；真实断电、Windows写入和其他Agent未验收 |
| REQ-014 | 接入、漂移和断开 | magpie-core | `.deps/interfaces/.deps/gateway/.deps/core/specs/01-product-and-agents.md` | 部分：Codex连接后可直接换模，SHA-256 revision绑定完整配置/文件身份拒绝过期预览；连续更新与断开恢复首次配置、user-owned magpie不接管已测；其他Agent及完整窗口流程未验收 |
| REQ-015 | Agent 专属字段和 Profile 保留 | magpie-core | `.deps/interfaces/.deps/gateway/.deps/core/specs/01-product-and-agents.md` | 仅组件：Codex `model_reasoning_effort` 可读取展示；连接/断开不改它，且保留原配置的 `[profiles]`。没有 effort 等字段的编辑入口或 Magpie Profile。 |
| REQ-016 | 跨 Agent 模型目录 | magpie-core | `.deps/interfaces/.deps/gateway/.deps/core/specs/01-product-and-agents.md` | 部分：统一Provider/model目录接CLI/Gateway/桌面虚拟选择器；Codex已托管换模沿用实际Gateway并可保存；其他Agent原生目录、能力/价格及可见性未实现 |
| REQ-017 | 每 Agent 模型可见性 | magpie-core | `.deps/interfaces/.deps/gateway/.deps/core/specs/01-product-and-agents.md` | 未实现：无 Agent 级可见性存储或 CLI/GUI入口；当前目录只有Provider 全局 enabled 与 model 列表。停用 Provider 同时阻止新请求，不是 Picker 隐藏。 |
| REQ-018 | 保存、应用和删除 Profile | magpie-core | `.deps/interfaces/.deps/gateway/.deps/core/specs/01-product-and-agents.md` | 未实现：CLI、桌面和托盘没有 Magpie Profile 的保存、应用或删除入口；Codex 配置事务只保留用户原有的 `[profiles]`，不生成或应用跨 Agent 快照。 |
| REQ-019 | 变化重扫和状态反馈 | magpie-core | `.deps/interfaces/.deps/gateway/.deps/core/specs/01-product-and-agents.md` | 部分：Snapshot刷新、缺失文件不保留旧托管值、ActionsState重读归属已测；完整变化监控与窗口焦点未验收 |
| REQ-020 | Provider 目录和新增 | magpie-core | `.deps/interfaces/.deps/gateway/.deps/core/specs/02-providers-and-accounts.md` | 部分：原生自定义Provider、4种单一协议选择、直接key输入及系统凭据存储、无key本地服务、启停/删除已实现；完整预设/Region/订阅/高级选项与真实账户未验收 |
| REQ-021 | 多个上游密钥和 Provider 编辑 | magpie-core | `.deps/interfaces/.deps/gateway/.deps/core/specs/02-providers-and-accounts.md` | 部分：单凭据编辑/轮换/清除与事务清理已测，新请求即时读取新key；多key/账户排序、Usage归属、请求测试及高级选项未实现 |
| REQ-022 | 模型列表和目录元数据 | magpie-core | `.deps/interfaces/.deps/gateway/.deps/core/specs/02-providers-and-accounts.md` | 部分：4协议真实合成HTTP模型发现、Anthropic/Gemini分页、失败保留旧目录与revision冲突已测；公共价格/能力元数据、过滤规则和自动刷新未实现 |
| REQ-023 | 用户可导入现有 Provider | magpie-core | `.deps/interfaces/.deps/gateway/.deps/core/specs/02-providers-and-accounts.md` | 未实现：无 import 命令/页面/URI处理；Codex读取只取选中字段，不导入 `model_providers` 或 sign-in材料。 |
| REQ-024 | 发现已登录订阅 | magpie-core | `.deps/interfaces/.deps/gateway/.deps/core/specs/02-providers-and-accounts.md` | 未实现：共享 Agent 探测仅检查可执行文件元数据，不读取登录；Provider 配置与凭据模块没有 OAuth 账户、订阅发现开关、原生 CLI 生成适配或 token 刷新。 |
| REQ-025 | 已弃用订阅的社区插件交接 | magpie-core | `.deps/interfaces/.deps/gateway/.deps/core/specs/02-providers-and-accounts.md` | 未实现：没有插件host、retired订阅库、Move/Adopt/removed状态。普通Provider启停不是插件所有权转移。 |
| REQ-026 | 套餐、额度和余额 | magpie-core | `.deps/interfaces/.deps/gateway/.deps/core/specs/02-providers-and-accounts.md` | 未实现：没有额度或余额读取命令、Usage 额度卡；核心纯内存路由模块只接收调用者提供的额度、预算和观察时间快照，不采集真实套餐或余额。 |
| REQ-027 | 账户顺序和约束 | magpie-core | `.deps/interfaces/.deps/gateway/.deps/core/specs/02-providers-and-accounts.md` | 未实现：共享 Provider 配置每条只有一份凭据，只能停用整条 Provider；核心纯内存路由可筛选账户目标、暂停、权限与预算，但未连接真实账户源，也没有多账户排序和约束入口。 |
| REQ-028 | 计划与本机订阅使用限制 | magpie-core | `.deps/interfaces/.deps/gateway/.deps/core/specs/02-providers-and-accounts.md` | 未实现：无 `accounts import`、订阅前提/风险确认/Cloud Project字段。 |
| REQ-029 | 账号和模型状态对外说明 | magpie-core | `.deps/interfaces/.deps/gateway/.deps/core/specs/02-providers-and-accounts.md` | 部分：Provider启用状态、模型数、凭据来源及公开CLI目录一致；账户/额度/迁移/Agent使用归属及完整错误分类未实现 |
| REQ-030 | 本地 Gateway 生命周期 | magpie-gateway | `.deps/interfaces/.deps/gateway/specs/03-gateway-and-protocols.md` | 部分：桌面默认管理自有Gateway、实际服务版本/身份、启停/冲突/EOF及owner异常退出回收已测，外部服务只读；托盘/Web/TUI、共享全设置及其他OS生命周期未验收 |
| REQ-031 | 请求入口与模型标识 | magpie-gateway | `.deps/interfaces/.deps/gateway/specs/03-gateway-and-protocols.md` | 部分：四协议对话HTTP/SSE及Provider/model路由已测；count_tokens/countTokens、音频、embeddings/rerank、图像/视频、Ollama生成、完整目录/别名与Codex backend仍缺，105–110另列 |
| REQ-032 | 协议转换和流式行为 | magpie-gateway | `.deps/interfaces/.deps/gateway/specs/03-gateway-and-protocols.md` | 部分：四协议转换与Chat输出上限按真实主机/明确400的一次兼容重放已测，共享deadline/permit且不重放已开始流；完整字段事件、其他API、偏好学习及真实上游未验收 |
| REQ-033 | 错误、状态码和请求归属 | magpie-gateway | `.deps/interfaces/.deps/gateway/specs/03-gateway-and-protocols.md` | 部分：协议化脱敏错误、Provider/model归属头、单次参数兼容重放、超时/取消/并发拒绝已测；全路由尝试链、允许响应头、Usage账务和真实上游兼容未完成 |
| REQ-034 | 模型目录契约 | magpie-gateway | `.deps/interfaces/.deps/gateway/specs/03-gateway-and-protocols.md` | 部分：共享目录、显式刷新原子更新、失败保留旧模型及16384模型边界已测；持久stale/成功时间、Codex/Gemini/Muse/Ollama/单模型目录与完整能力元数据未实现 |
| REQ-035 | 本机访问和远程鉴权 | magpie-gateway | `.deps/interfaces/.deps/gateway/specs/03-gateway-and-protocols.md` | 部分：转发与旧catalog均按真实peer、精确Host、Origin和代理头检查本机免认证；LAN分享、远程key/只读key、allowlist和TLS管理未实现 |
| REQ-036 | Agent 连接到网关 | magpie-gateway | `.deps/interfaces/.deps/gateway/specs/03-gateway-and-protocols.md` | 部分：安装版Codex0.165在隔离HOME经真实Gateway收到中文Responses流并恢复；其他Agent/真实账户未验收 |
| REQ-037 | 会话、账户固定和观测 API | magpie-gateway | `.deps/interfaces/.deps/gateway/specs/03-gateway-and-protocols.md` | 未实现：Gateway 转发遇到 `X-Magpie-Session` 或 `X-Magpie-Account` 返回 501；没有账户固定或 `/v1/magpie/route` 查询，也不根据提示词猜测会话。 |
| REQ-038 | 远程 magpie | magpie-gateway | `.deps/interfaces/.deps/gateway/specs/03-gateway-and-protocols.md` | 未实现：可手工把兼容HTTPS地址当普通自定义Provider，但没有Remote Magpie类型、授权目录协商、间隔刷新或session继承。 |
| REQ-039 | Gateway 的附加查询接口 | magpie-gateway | `.deps/interfaces/.deps/gateway/specs/03-gateway-and-protocols.md` | 未实现：Gateway 的 `/health` 提供基础服务状态与传输计数；Usage/请求明细、额度历史/卡片、账户并发及 key 限额查询 API 尚未实现，未知路径明确拒绝。 |
| REQ-040 | 建立模型路由组 | magpie-core | `.deps/interfaces/.deps/gateway/.deps/core/specs/04-routing-and-failover.md` | 仅组件：核心纯内存路由模块（`routing.rs`）可构建聊天或决策组、嵌套展开和启停成员，拒绝空组、缺失引用、循环及重复目标；没有 CLI/GUI 持久化入口，Gateway 也未调用它。 |
| REQ-041 | 路由策略 | magpie-core | `.deps/interfaces/.deps/gateway/.deps/core/specs/04-routing-and-failover.md` | 仅组件：核心纯内存路由实现 smart/order/rotate/usage/pace/weight/fast 七种策略及 fast 别名，调度状态只在 Router 实例中；Free first 和 Manual 尚无实现，策略设置、持久化与实际网关调度未接通。 |
| REQ-042 | 选择可服务成员和 account 顺序 | magpie-core | `.deps/interfaces/.deps/gateway/.deps/core/specs/04-routing-and-failover.md` | 仅组件：核心路由根据传入快照过滤暂停、权限、预算及未知或过期额度，优先选择非套餐受限成员；软限速下的承接与休眠须有明确策略。实际 Gateway 仍只按启用 Provider 精确选择模型，未接真实账户调度。 |
| REQ-043 | 按请求上下文匹配路由规则 | magpie-core | `.deps/interfaces/.deps/gateway/.deps/core/specs/04-routing-and-failover.md` | 未实现：没有路由规则存储、编辑器或分类器；核心路由的 Context 只接受调用方输入，不解析提示词意图或调用分类模型，Gateway 也未调用该路由引擎。 |
| REQ-044 | Session affinity 和重置 | magpie-core | `.deps/interfaces/.deps/gateway/.deps/core/specs/04-routing-and-failover.md` | 未实现：Gateway 拒绝会话指定请求头，核心路由没有会话与账户的持久绑定或重置流程；轮转和权重调度的内部状态不能提供会话粘性。 |
| REQ-045 | 重试和 Fallback 边界 | magpie-core | `.deps/interfaces/.deps/gateway/.deps/core/specs/04-routing-and-failover.md` | 未实现：Gateway 禁止 HTTP 客户端隐式重试；现有 Chat 仅在 HTTP 400 明确拒绝当前输出上限字段并要求替代字段时，向同一上游改写参数重发一次。通用错误分类重试和跨成员 Fallback 链仍缺，核心路由只计算选择；凭据轮换竞争时的重读也不是上游重试。 |
| REQ-046 | 实时路由解释和历史 | magpie-core | `.deps/interfaces/.deps/gateway/.deps/core/specs/04-routing-and-failover.md` | 仅组件：核心路由的 Decision/Exclusion 可解释一次纯计算选择及排除原因，Gateway `/health` 仅有聚合传输计数；没有 Routing 页面、路由事件流或请求/会话历史。 |
| REQ-047 | System One / 模型决策 | magpie-core | `.deps/interfaces/.deps/gateway/.deps/core/specs/04-routing-and-failover.md` | 未实现：核心路由只保证决策组不会混入聊天组展开；没有 System One Provider、服务接口、分类模型调用或用户开关。 |
| REQ-048 | Web 搜索配置和 Provider 代搜 | magpie-core | `.deps/interfaces/.deps/gateway/.deps/core/specs/04-routing-and-failover.md` | 未实现：无searcher配置、CLI search、搜索代理或多key调度。协议中的普通tool消息转换不代表执行搜索服务。 |
| REQ-049 | Agent 工具服务 | magpie-core | `.deps/interfaces/.deps/gateway/.deps/core/specs/04-routing-and-failover.md` | 未实现：没有 MCP 服务、`tools`/`tool` 命令、`web_fetch` 或媒体生成；服务端协议转换模块只转译客户端已提供的工具 schema、调用和结果，不执行工具。 |
| REQ-050 | 汇总 Usage | magpie-gateway | `.deps/interfaces/.deps/gateway/specs/05-usage-and-sessions.md` | 仅组件：服务端协议转换模块保留部分 usage 字段，Gateway 记录传输完成与失败次数；没有请求及尝试归属的持久 Usage 账本，也没有 Usage 页面或 CLI。 |
| REQ-051 | 额度和窗口 | magpie-gateway | `.deps/interfaces/.deps/gateway/specs/05-usage-and-sessions.md` | 未实现：Provider 模型刷新只更新模型列表；没有额度查询、账户时间窗口快照、重置时间或额度刷新状态。 |
| REQ-052 | Cap、预算和计算 | magpie-gateway | `.deps/interfaces/.deps/gateway/specs/05-usage-and-sessions.md` | 仅组件：核心纯内存路由可根据调用方提供的限制和预算快照筛选候选；尚无可设置、持久保存、并发扣减或重置的预算系统。 |
| REQ-053 | 查询和分析 | magpie-gateway | `.deps/interfaces/.deps/gateway/specs/05-usage-and-sessions.md` | 仅组件：Gateway `/health` 提供当前活动请求数和累计传输计数；没有 Usage 条件筛选、时序图、费用分析或请求明细。 |
| REQ-054 | Session 列表 | magpie-gateway | `.deps/interfaces/.deps/gateway/specs/05-usage-and-sessions.md` | 未实现：没有 Gateway 与原生 Agent 会话的索引或稳定合并；现有转发服务不保存稳定会话记录，也不读取 Agent 原生会话历史。 |
| REQ-055 | 消息内容、记录开关与保留 | magpie-gateway | `.deps/interfaces/.deps/gateway/specs/05-usage-and-sessions.md` | 仅组件：Gateway 转发路径默认不持久保存请求或响应正文；没有显式录制流程、保留期和容量管理，也没有删除会话时联动删除正文的能力。 |
| REQ-056 | 隐私遮蔽 | magpie-gateway | `.deps/interfaces/.deps/gateway/specs/05-usage-and-sessions.md` | 未实现：桌面 Provider 密钥输入遮蔽保护凭据呈现，Gateway 错误脱敏保护错误响应；尚无提示词遮蔽规则或向上游发送前的文本替换流程。 |
| REQ-057 | Pricing | magpie-gateway | `.deps/interfaces/.deps/gateway/specs/05-usage-and-sessions.md` | 未实现：共享 Provider 配置保存模型 ID，不含价格元数据；没有价格目录、币种、历史价格或用户价格覆盖。 |
| REQ-058 | 导出 | magpie-gateway | `.deps/interfaces/.deps/gateway/specs/05-usage-and-sessions.md` | 未实现：Provider JSON 列表不是 Usage/quota/session CSV/JSON 导出。 |
| REQ-059 | Quota/Usage API 与请求追踪 | magpie-gateway | `.deps/interfaces/.deps/gateway/specs/05-usage-and-sessions.md` | 未实现：Gateway 查询目前覆盖健康和模型目录，`/health` 明确标记 `usage_accounting: not_implemented`；没有 Usage/Quota 产品 API 或请求与尝试的账务追踪。 |
| REQ-060 | 内置 Provider 与社区插件边界 | magpie-gateway | `.deps/interfaces/.deps/gateway/specs/06-plugins-and-tools.md` | 未实现：Provider 存储、CLI 和桌面管理的是自定义 API 上游；没有插件来源、服务所有权或退役订阅向社区插件交接的状态。 |
| REQ-061 | 社区 Provider 插件查找和安装 | magpie-gateway | `.deps/interfaces/.deps/gateway/specs/06-plugins-and-tools.md` | 未实现：没有 npm 安装器、索引或 Plugins 实际页面。 |
| REQ-062 | 社区 Provider 插件运行 | magpie-gateway | `.deps/interfaces/.deps/gateway/specs/06-plugins-and-tools.md` | 未实现：没有 Node 插件宿主或版本化 Provider RPC；桌面 CLI 桥接只执行 Magpie 自身命令，不提供插件运行和崩溃隔离工作流。 |
| REQ-063 | 订阅账号转移 | magpie-gateway | `.deps/interfaces/.deps/gateway/specs/06-plugins-and-tools.md` | 未实现：无 migrations 状态或 Move/Adopt 路径。 |
| REQ-064 | 插件登录和模型目录 | magpie-gateway | `.deps/interfaces/.deps/gateway/specs/06-plugins-and-tools.md` | 未实现：系统 API key 存储和显式模型发现只服务自定义 Provider；没有插件 OAuth、设备或浏览器登录、重新授权及模型/额度回传流程。 |
| REQ-065 | Web 和其它扩展工具 | magpie-gateway | `.deps/interfaces/.deps/gateway/specs/06-plugins-and-tools.md` | 未实现：没有 MCP transport、tools schema 或 web connector 工作流。 |
| REQ-066 | 社区 Provider 插件可观测性和移除 | magpie-gateway | `.deps/interfaces/.deps/gateway/specs/06-plugins-and-tools.md` | 未实现：不存在插件版本/移除记录和 CLI。 |
| REQ-067 | Magpie 内置功能插件 | magpie-gateway | `.deps/interfaces/.deps/gateway/specs/06-plugins-and-tools.md` | 未实现：core/service 中的 Rust 模块为静态实现；没有具备配置 schema、依赖关系、注册位置和启停/释放生命周期的功能插件运行时。 |
| REQ-068 | Gateway extension points | magpie-gateway | `.deps/interfaces/.deps/gateway/specs/06-plugins-and-tools.md` | 未实现：Gateway 使用固定请求处理链，没有可注册或卸载的扩展钩子，也没有 JavaScript 中间件宿主；观察、选路、遮蔽、预算和工具能力尚无扩展工作流。 |
| REQ-069 | 插件种类和状态区分 | magpie-gateway | `.deps/interfaces/.deps/gateway/specs/06-plugins-and-tools.md` | 未实现：没有 feature / Provider / MCP / Agent 扩展分层 UI/CLI。 |
| REQ-070 | 统一 Library | magpie-core | `.deps/interfaces/.deps/gateway/.deps/core/specs/07-library-sync-and-backup.md` | 未实现：没有 Library 存储、分发事务或页面；Codex 配置事务只处理接入与模型等托管字段，不分发 instructions、skills 或 MCP 配置。 |
| REQ-071 | Instructions、skills 和 Agent 级操作 | magpie-core | `.deps/interfaces/.deps/gateway/.deps/core/specs/07-library-sync-and-backup.md` | 未实现：无具名 instruction set、marker、skills catalog 或加载方式。 |
| REQ-072 | MCP servers | magpie-core | `.deps/interfaces/.deps/gateway/.deps/core/specs/07-library-sync-and-backup.md` | 未实现：无 server 分配、Agent 格式导出、连接测试或 OAuth。 |
| REQ-073 | Projects | magpie-core | `.deps/interfaces/.deps/gateway/.deps/core/specs/07-library-sync-and-backup.md` | 未实现：无 project 身份、目标路径保护或自有文件/ignore 记录。 |
| REQ-074 | RTK 输出压缩 | magpie-core | `.deps/interfaces/.deps/gateway/.deps/core/specs/07-library-sync-and-backup.md` | 未实现：无 RTK 探测、配置或启动包装。 |
| REQ-075 | 本地文件备份和可选区段 | magpie-core | `.deps/interfaces/.deps/gateway/.deps/core/specs/07-library-sync-and-backup.md` | 仅组件：Codex 配置事务的私有恢复映像只用于受控配置恢复，Provider 存储只保存当前公共配置；没有带口令加密、可选区段和恢复预览的产品备份文件。 |
| REQ-076 | 加密远端同步 | magpie-core | `.deps/interfaces/.deps/gateway/.deps/core/specs/07-library-sync-and-backup.md` | 未实现：没有 WebDAV、S3 对象存储或 GitHub 同步连接器，也没有远端同步配置或后台同步器。 |
| REQ-077 | 每台电脑保留的设置 | magpie-core | `.deps/interfaces/.deps/gateway/.deps/core/specs/07-library-sync-and-backup.md` | 部分：桌面在本机持久保存主题、五语语言偏好及 Gateway 监听地址，过期修订版本的保存冲突已测；独立 CLI 地址仍由 `--listen` 指定，完整共享网络设置、跨机同步/恢复时的本机字段保留规则及其他本机设置未实现。 |
| REQ-078 | 远端内容和恢复保护 | magpie-core | `.deps/interfaces/.deps/gateway/.deps/core/specs/07-library-sync-and-backup.md` | 未实现：Provider 存储与桌面偏好的格式校验、修订版本冲突检查只覆盖本地文件；尚无远端错误页、损坏或跨代分片的拒绝流程，也没有 ETag/SHA 条件提交和远端替换保护。 |
| REQ-079 | Usage 数据与请求档案 | magpie-core | `.deps/interfaces/.deps/gateway/.deps/core/specs/07-library-sync-and-backup.md` | 未实现：没有独立密封 Usage/正文 archive 或目的地。 |
| REQ-080 | 运行形态 | magpie-interfaces | `.deps/interfaces/specs/08-cli-tui-and-web.md` | 部分：独立headless CLI与GPUI、桌面自有Gateway进程/正常及异常退出回收已测；CLI无参仍help，单实例/托盘/Web/TUI/daemon及跨OS实装未完成 |
| REQ-081 | 桌面导航和设置 | magpie-interfaces | `.deps/interfaces/specs/08-cli-tui-and-web.md` | 部分：Agents/Providers/Settings/Gateway四页真实行为，新增网关启停、只读外部服务、实际版本/模型数、地址保存及进入时刷新；其余原版页面、Settings全设置/搜索和快捷键未实现 |
| REQ-082 | 浏览器 Web UI | magpie-interfaces | `.deps/interfaces/specs/08-cli-tui-and-web.md` | 未实现：无 `web` 分派、3430 页面服务、路由或 Web key/session。 |
| REQ-083 | TUI | magpie-interfaces | `.deps/interfaces/specs/08-cli-tui-and-web.md` | 未实现：无 `tui` 分派或终端 UI 库/页面。 |
| REQ-084 | CLI 接口 | magpie-interfaces | `.deps/interfaces/specs/08-cli-tui-and-web.md` | 部分：已有Provider/Codex/目录/serve命令，新增set-model revision预览应用和supervised ready协议；原版完整命令/别名/CSV/daemon/Web/TUI仍未对齐 |
| REQ-085 | Agent 状态、更新和配置流程 | magpie-interfaces | `.deps/interfaces/specs/08-cli-tui-and-web.md` | 部分：CLI/桌面共用 PATH 探测与配置快照，显示已命中的执行路径；Codex 支持归属/漂移状态、预览、连接、同一连接换模和离线断开恢复。刷新只重读状态；Agent 安装/升级、完整版本与来源识别、WSL/多实例及其他 Agent 配置流程未实现。 |
| REQ-086 | 应用更新和可诊断错误 | magpie-interfaces | `.deps/interfaces/specs/08-cli-tui-and-web.md` | 部分：版本和安全错误、Gateway真实服务版本及端口占用等反馈已实现；更新频道、auto/ask/manual、签名下载、切换/回滚和完整诊断导出未实现 |
| REQ-090 | 凭据存放和界面遮蔽 | magpie-core | `.deps/interfaces/.deps/gateway/.deps/core/specs/09-privacy-security-and-platforms.md` | 部分：直接key输入遮蔽、Secret Service实测、公共配置仅存引用、轮换回收/失败回滚与脱敏已测；macOS/Windows凭据后端及原生读屏未验收 |
| REQ-091 | 网络入口、鉴权和限流 | magpie-core | `.deps/interfaces/.deps/gateway/.deps/core/specs/09-privacy-security-and-platforms.md` | 部分：两种Gateway模式均有真实loopback peer/Host/Origin/proxy guard及有界连接/请求，目录模式差距本轮已补；远程key、只读allowlist、管理API/CSRF与LAN权限未实现 |
| REQ-092 | Privacy 默认和本机数据 | magpie-core | `.deps/interfaces/.deps/gateway/.deps/core/specs/09-privacy-security-and-platforms.md` | 部分：目前没有提示词档案或正文日志产品；Gateway 返回脱敏错误，显式 Codex 变更提供目标路径、预览和恢复。桌面已有外观、语言和本机 Gateway 地址设置，尚无 Privacy 录制、保留期、容量及删除流程。 |
| REQ-093 | Private URL、代理和 SSRF | magpie-core | `.deps/interfaces/.deps/gateway/.deps/core/specs/09-privacy-security-and-platforms.md` | 部分：Provider HTTPS、loopback HTTP、URL结构校验、localhost固定解析、拒绝重定向/隐式环境代理与self-target已测；显式代理、远程分享及全部网络组合未实现 |
| REQ-094 | 多平台和 WSL 边界 | magpie-core | `.deps/interfaces/.deps/gateway/.deps/core/specs/09-privacy-security-and-platforms.md` | 部分：旧版本Android四ABI/双ARM真机证据保留为历史；本轮Linux x86_64已测，新增keyring依赖后的macOS/Windows/Android/NixOS/WSL未复验，Windows配置写入仍拒绝 |
| REQ-095 | 隐私工具与外部上游 | magpie-core | `.deps/interfaces/.deps/gateway/.deps/core/specs/09-privacy-security-and-platforms.md` | 部分：实时 Provider 解析和显式模型刷新只读取选中 Provider 的凭据；Gateway 自行构造上游认证，不转发客户端 Cookie/Authorization，也不遍历 Library 或其他 Agent 文件。除正常转发外，只有明确模型刷新另行出网；自动分类、搜索和媒体工具的隐私调用链未实现。 |
| REQ-100 | 视觉方向 | magpie-interfaces | `.deps/interfaces/specs/10-visual-and-localization.md` | 部分：四页继续对齐原版紧凑列表、白灰/炭黑/紫色与五语布局，新增Gateway和换模场景；完整页面、自定义字体、真实窗口与用户视觉认可未完成 |
| REQ-101 | App 和紧凑 Panel 布局 | magpie-interfaces | `.deps/interfaces/specs/10-visual-and-localization.md` | 部分：主窗1280/900/440/360布局及Gateway owned/external/error、Codex换模场景已实现；真实快捷Panel/托盘、额度账户内容及全部缩放/焦点验收未完成 |
| REQ-102 | 语言 | magpie-interfaces | `.deps/interfaces/specs/10-visual-and-localization.md` | 部分：五语文本、CJK/grapheme输入及主题语言持久化/冲突已测；原生IME、完整日期数字本地化未验收 |
| REQ-103 | 可访问性与操作反馈 | magpie-interfaces | `.deps/interfaces/specs/10-visual-and-localization.md` | 部分：新增关闭期间禁止重启lease、运行地址不被偏好重载冒充、外部服务禁停、错误保留和换模revision；组件/真实CLI路径已测，原生全键盘/IME/读屏及全产品可访问性未验收 |
| REQ-104 | 参考截图与产品数据 | magpie-interfaces | `.deps/interfaces/specs/10-visual-and-localization.md` | 部分：依据固定原版来源和配方文字层级，新GPUI Scene明确使用隔离合成数据；未将参考截图作为业务数据或当前产品像素证据 |
| REQ-105 | Library MCP Hub | magpie-gateway | `.deps/interfaces/.deps/gateway/specs/11-upstream-additions.md` | 未实现：尚无Hub聚合入口、共享MCP后端、Agent作用域工具表及连接恢复。 |
| REQ-106 | Responses previous_response_id 续接 | magpie-gateway | `.deps/interfaces/.deps/gateway/specs/11-upstream-additions.md` | 未实现：尚无调用者隔离的有界HTTP历史缓存、续接还原及未知ID/native relay处理。 |
| REQ-107 | Text-to-speech 转发 | magpie-gateway | `.deps/interfaces/.deps/gateway/specs/11-upstream-additions.md` | 未实现：尚无audio/speech路由；音频、语音SSE、厂商JSON及其计量/取消未实现。 |
| REQ-108 | 只读 Gateway key | magpie-gateway | `.deps/interfaces/.deps/gateway/specs/11-upstream-additions.md` | 未实现：尚无Gateway key持久权限或只读路由allowlist；本机无key访问不能替代。 |
| REQ-109 | 读取 Agent 既有会话的明确许可 | magpie-gateway | `.deps/interfaces/.deps/gateway/specs/11-upstream-additions.md` | 未实现：尚无Sessions读取与按Agent许可；不读取正文的现状不等同授权/撤销/迁移流程。 |
| REQ-110 | Responses WebSocket | magpie-gateway | `.deps/interfaces/.deps/gateway/specs/11-upstream-additions.md` | 未实现：当前upgrade仍明确426，未实现多turn、连接内续接、interrupt及每turn重新鉴权。 |

## 设计合同依赖（2026-10-29）

配方 1.3.3 增加固定版本 design-directions 及递归 design-foundations，已完成来源/全部正文阅读与产品作用范围、备选取舍和验收合同集成。现有 93 REQ/68 Agent 是基线，原版额外观察仍需纳入；全部功能及用户视觉认可继续未完成。依赖归档与实际界面/功能验证分别记录，不能将设计材料阅读算作通过。
