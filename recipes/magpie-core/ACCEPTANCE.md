# 分项验收

迁移时全部未执行。使用一次性隔离 HOME/Agent 目录、虚构密钥、本地可记录请求的上游 fixture；仅对明确授权的真实集成使用账户。软件重建后，每条记录版本/平台、初始数据、操作、实际结果和未验证原因。

先读取本包所有 specs。每条 REQ 的全部正文是验收合同，下面列出原规格明确给出的正常/失败/边界场景；不能只检查成功路径。对于 API/账号/文件权限，fixture 要能注入 401/429/5xx、超时、损坏数据、并发修改和取消，并检查请求次数及文件前后内容。UI 用真实构建运行，在宽屏、窄屏及键盘流程下验收。

## REQ-010：多个入口反映同一状态

规格：[specs/01-product-and-agents.md](specs/01-product-and-agents.md)。状态：未执行。

- 正常验收：CLI 设置 Codex 模型后，Agent 页面和面板显示新值。
- 失败验收：GUI 后端暂时不可达时，不显示成功保存；刷新后以实际保存状态为准。

## REQ-011：发现和列出 Agent

规格：[specs/01-product-and-agents.md](specs/01-product-and-agents.md)。状态：未执行。

- 正常验收：空白新机器不显示未安装 Agent；安装一个受支持 Agent 并建立配置后，重新扫描出现对应行。
- 失败验收：路径不存在、配置损坏或权限不足不等价于 Agent 未安装；显示区别和可修复路径。

## REQ-012：兼容 Agent 的公开配置格式

规格：[specs/01-product-and-agents.md](specs/01-product-and-agents.md)。状态：未执行。

- 正常验收：Codex 的 TOML 配置、Claude Code 的 JSON 配置和 OpenCode 的 JSONC 配置都可接入；每个 Agent 的模型在其原生 `/model` 或模型选择器中可用。
- 失败验收：目标格式含未知字段时保留它们；未知字段不能因 round-trip 被删除。

## REQ-013：只做精确、可恢复的字段编辑

规格：[specs/01-product-and-agents.md](specs/01-product-and-agents.md)。状态：未执行。

- 正常验收：在含注释、重复 key、未知扩展字段的配置中仅改模型，其他内容保留。
- 失败验收：解析错误、不可读、只读、路径不安全或文件被 Agent 锁定时，显示失败并保留原内容。

## REQ-014：接入、漂移和断开

规格：[specs/01-product-and-agents.md](specs/01-product-and-agents.md)。状态：未执行。

- 正常验收：连接 Codex、用户手工改一个无关 Header、再断开，Header 不丢失，原模型恢复。
- 失败验收：Agent 进程正在覆盖文件时，连接操作等待有界时间或拒绝，不能以部分写入结束。

## REQ-015：Agent 专属字段和 Profile 保留

规格：[specs/01-product-and-agents.md](specs/01-product-and-agents.md)。状态：未执行。

- 正常验收：Codex 的 effort 字段只出现其有效等级；关闭 Provider 后不留已失效选择。
- 失败验收：Agent 升级后字段不再受支持时显示不兼容状态并保留备份，不写入猜测值。

## REQ-016：跨 Agent 模型目录

规格：[specs/01-product-and-agents.md](specs/01-product-and-agents.md)。状态：未执行。

- 正常验收：在同一个模型列表中选择不同 Provider 的两个模型，两个 Agent 原生配置分别更新。
- 边界验收：模型名相同但来自不同 Provider 时仍可区分；明确的 Agent 自定义模型名保持原样。

## REQ-017：每 Agent 模型可见性

规格：[specs/01-product-and-agents.md](specs/01-product-and-agents.md)。状态：未执行。

- 正常验收：Codex 隐藏 `vendor/model-a`，其列表不显示它，另一个 Agent 仍有它；显式请求该模型仍成功。
- 失败验收：无法读取可见性设置时，保留上次有效集合并提示错误，不把所有模型误设为可见或全部隐藏。

## REQ-018：保存、应用和删除 Profile

规格：[specs/01-product-and-agents.md](specs/01-product-and-agents.md)。状态：未执行。

- 正常验收：保存 `Work`，切换两个 Agent 的模型后应用，两个模型均恢复。
- 边界验收：Profile 中某 Agent 已卸载时，其余 Agent 仍可应用；缺失 Agent 设置保留在 Profile 里，重装后可恢复。

## REQ-019：变化重扫和状态反馈

规格：[specs/01-product-and-agents.md](specs/01-product-and-agents.md)。状态：未执行。

- 正常验收：用户在 Codex 自己的编辑器改模型后刷新，magpie 显示该模型。
- 失败验收：重扫期间文件短暂不完整时保留最后可解析值，再重试；不得显示伪造的默认模型。

## REQ-020：Provider 目录和新增

规格：[specs/02-providers-and-accounts.md](specs/02-providers-and-accounts.md)。状态：未执行。

- 正常验收：加 DeepSeek 只需输入 key；加本地 Ollama 可不填 key；加自定义兼容服务需同时设地址和模型。
- 失败验收：网址 scheme、密钥字段或必需的 base URL 不合法时拒绝保存并指出字段；不向任意地址发送真实 key 进行校验。

## REQ-021：多个上游密钥和 Provider 编辑

规格：[specs/02-providers-and-accounts.md](specs/02-providers-and-accounts.md)。状态：未执行。

- 正常验收：轮换第一把 key 后新请求使用新 key，历史仍归属于原 key。
- 失败验收：上游拒绝 key 时保留配置并标识失败，不能把 Provider 删除或把空列表当成功。

## REQ-022：模型列表和目录元数据

规格：[specs/02-providers-and-accounts.md](specs/02-providers-and-accounts.md)。状态：未执行。

- 正常验收：刷新后出现新增模型并按配置自动包含；手工选择的模型可从列表移除或加入。
- 失败验收：目录服务不可达时保留上次成功列表并标记数据陈旧，不清空模型。

## REQ-023：用户可导入现有 Provider

规格：[specs/02-providers-and-accounts.md](specs/02-providers-and-accounts.md)。状态：未执行。

- 正常验收：导入 Codex 的 OpenAI-compatible Provider，确认后出现在 Provider 列表。
- 失败验收：来源文件不可读、只含 env key 引用或内容不完整时跳过该项并说明原因，不清空原设置。

## REQ-024：发现已登录订阅

规格：[specs/02-providers-and-accounts.md](specs/02-providers-and-accounts.md)。状态：未执行。

- 正常验收：在 Claude Code 签入的账户出现在 Provider 列表，其原生模型可由另一 Agent 经 Gateway 使用。
- 失败验收：token 过期、账号退出、企业域/Cloud Project 不匹配时显示登录或权限错误，不能无限刷新或把账户判成有额度。

## REQ-025：已弃用订阅的社区插件交接

规格：[specs/02-providers-and-accounts.md](specs/02-providers-and-accounts.md)。状态：未执行。

- 正常验收：安装 `opencode-cursor-auth` 后，Cursor 订阅显示为插件提供，Gateway 把请求交给插件。
- 失败验收：迁移失败时不丢原账户材料，标记失败并在间隔后重试；旧内置代码已退休的账户明确标记暂不可服务。

## REQ-026：套餐、额度和余额

规格：[specs/02-providers-and-accounts.md](specs/02-providers-and-accounts.md)。状态：未执行。

- 正常验收：账户额度降低后下一请求避开账户，并在重置后恢复。
- 失败验收：额度读取失败时将状态标为未知并保留上次已知值及时间，不把“读取失败”当成无限额度。

## REQ-027：账户顺序和约束

规格：[specs/02-providers-and-accounts.md](specs/02-providers-and-accounts.md)。状态：未执行。

- 正常验收：将一个账户暂停，只对该账户发出的新请求被绕过，另一个未暂停账户继续服务。
- 失败验收：身份读不全或两个座席无法确认相同，展示为未知/独立，不基于邮箱相似度擅自合并。

## REQ-028：计划与本机订阅使用限制

规格：[specs/02-providers-and-accounts.md](specs/02-providers-and-accounts.md)。状态：未执行。

- 正常验收：导入多账户文件后保留每个独立团队席位；重复账号不重复添加。
- 失败验收：只含过期 access token 而无续期信息的登录拒绝导入并说明原因。

## REQ-029：账号和模型状态对外说明

规格：[specs/02-providers-and-accounts.md](specs/02-providers-and-accounts.md)。状态：未执行。

- 正常验收：账户登录失效的错误旁给重登入口；一个模型被拒但账户其他模型可用时不把整个账户停用。
- 失败验收：状态源无法读取时显示未知，禁止静默清空账户或额度信息。

## REQ-040：建立模型路由组

规格：[specs/04-routing-and-failover.md](specs/04-routing-and-failover.md)。状态：未执行。

- 正常验收：Agent 选择 `group/opus-anywhere` 后，组内模型按配置作为可替代成员运行。
- 失败验收：组不存在、空组或环形嵌套引用被清楚拒绝，不能递归卡死。

## REQ-041：路由策略

规格：[specs/04-routing-and-failover.md](specs/04-routing-and-failover.md)。状态：未执行。

- 正常验收：`order` 组的 A 返回 429、B 返回 200 时调用顺序为 A、B，Usage 的最终请求是成功重试。
- 边界验收：weight=3 对 weight=1 的两个成员，在足够请求样本上约按 3:1 平滑分布；不能一次长批处理导致 4 连续偏置。

## REQ-042：选择可服务成员和 account 顺序

规格：[specs/04-routing-and-failover.md](specs/04-routing-and-failover.md)。状态：未执行。

- 正常验收：同一订阅两个账户中，一个模型计划不含该模型，另一个包含；请求必须优先选后者。
- 失败验收：账户刚在原生 App 中切换/注销、额度状态陈旧或权限不明时，显示状态未知或过期并跳过必要请求，不编造余额。

## REQ-043：按请求上下文匹配路由规则

规格：[specs/04-routing-and-failover.md](specs/04-routing-and-failover.md)。状态：未执行。

- 正常验收：对“fix the login bug”配置 bug 标签的 classifier 返回 `bug`，组路由选择 bug 成员，并能查看规则命中解释。
- 失败验收：classifier 返回未知 label、超时或被拒时不循环调用；保留原请求在指定 fallback 下运行或返回明确 classifier error。

## REQ-044：Session affinity 和重置

规格：[specs/04-routing-and-failover.md](specs/04-routing-and-failover.md)。状态：未执行。

- 正常验收：连续两轮都在同一 session 且首个 Provider 可用时使用同一账户。
- 失败验收：没有 session id 时不根据 prompt 猜测会话；粘性缓存损坏时按明确默认策略而非任意 key。

## REQ-045：重试和 Fallback 边界

规格：[specs/04-routing-and-failover.md](specs/04-routing-and-failover.md)。状态：未执行。

- 正常验收：模型 A 429、模型 B 200，Agent 无感知切换；请求计一次成功并标记 retried。
- 失败验收：所有成员都拒绝 prompt 内容时，不把所有账户休眠多日；错误指出内容/模型拒绝类别。

## REQ-046：实时路由解释和历史

规格：[specs/04-routing-and-failover.md](specs/04-routing-and-failover.md)。状态：未执行。

- 正常验收：A 速率限制、B 回答后，live 视图先显示 A 的 rate limited，再显示 B answering/answered。
- 失败验收：日志不可读时标明不完整/未知，不显示空白为“没有请求”。

## REQ-047：System One / 模型决策

规格：[specs/04-routing-and-failover.md](specs/04-routing-and-failover.md)。状态：未执行。

- 正常验收：请求一个已定义 choices 的分类，返回其中之一并显示对应 route decision。
- 失败验收：无效 JSON、错误 choice 或上游超时时转为明确定义的 fallback，不把不可信输出当作模型 id/URL 执行。

## REQ-048：Web 搜索配置和 Provider 代搜

规格：[specs/04-routing-and-failover.md](specs/04-routing-and-failover.md)。状态：未执行。

- 正常验收：第一个服务无结果，第二个返回结果，Agent 得到搜索结果。
- 失败验收：API key 被拒时不得永久删除它或错误报告为有效结果。

## REQ-049：Agent 工具服务

规格：[specs/04-routing-and-failover.md](specs/04-routing-and-failover.md)。状态：未执行。

- 正常验收：Agent 通过 MCP 搜索网页并收到标题/URL/摘要，`magpie tool generate_image ... -o file.png` 在成功后写出文件。
- 失败验收：MCP 未连接、模型不支持视觉或媒体 Provider 返回错误时，给可读错误并清理未完成临时文件。

## REQ-070：统一 Library

规格：[specs/07-library-sync-and-backup.md](specs/07-library-sync-and-backup.md)。状态：未执行。

- 正常验收：将一条 skill 装给 Codex 和 Claude Code 后，两边使用各自格式而语义相同。
- 失败验收：配置目标无法读取时，不把它当空文件覆盖；显示路径和读/解析错误。

## REQ-071：Instructions、skills 和 Agent 级操作

规格：[specs/07-library-sync-and-backup.md](specs/07-library-sync-and-backup.md)。状态：未执行。

- 正常验收：卸载一个 Agent 后，它持有的既有 Library 项仍列出且可选择移除；未卸载 Agent 看不到那一项。
- 失败验收：第三方 Agent 覆写 marker 外内容时，同步不还原整份旧配置。

## REQ-072：MCP servers

规格：[specs/07-library-sync-and-backup.md](specs/07-library-sync-and-backup.md)。状态：未执行。

- 正常验收：stdio server 返回一页 `tools/list`，Library 显示数量并将定义写入目标 Agent 配置。
- 失败验收：超时标明在哪一步等待多久；401/403 不回报健康；一个失效 MCP 不影响其他服务器。

## REQ-073：Projects

规格：[specs/07-library-sync-and-backup.md](specs/07-library-sync-and-backup.md)。状态：未执行。

- 正常验收：为一个项目安排 skills 后，只在该 project 的 Agent session 中出现。
- 失败验收：目标路径变成一个 Agent 自己的指令或 MCP 文件时拒绝写入而不移动/覆盖原文件。

## REQ-074：RTK 输出压缩

规格：[specs/07-library-sync-and-backup.md](specs/07-library-sync-and-backup.md)。状态：未执行。

- 正常验收：应用一个已知支持的 RTK 配置，原命令的结果和 exit code 保持相同但输出更短。
- 失败验收：RTK 不存在时 Agent 命令仍正常执行或清楚提示用户。

## REQ-075：本地文件备份和可选区段

规格：[specs/07-library-sync-and-backup.md](specs/07-library-sync-and-backup.md)。状态：未执行。

- 正常验收：把 Library 和 profiles 备份后在新配置目录还原，选择区段都可回读。
- 失败验收：口令错误、版本新于本客户端、损坏文件或任一必需的本地 JSON 不可读时，中止全部写入并保留本机状态。

## REQ-076：加密远端同步

规格：[specs/07-library-sync-and-backup.md](specs/07-library-sync-and-backup.md)。状态：未执行。

- 正常验收：一台电脑修改 agents 配置，两台机同步后另一台看到该变更且未触碰其本机专属设置。
- 失败验收：远端不可读、数据损坏、权限失败或冲突时停止并保留本地与远端旧版本，不把文件解释成空配置。

## REQ-077：每台电脑保留的设置

规格：[specs/07-library-sync-and-backup.md](specs/07-library-sync-and-backup.md)。状态：未执行。

- 正常验收：换机恢复 profile/provider groups 但不自动开启局域网共享或替换本机窗口尺寸。
- 失败验收：字段新增或本客户端不能识别时仅忽略/保留该字段而不是清空 settings。

## REQ-078：远端内容和恢复保护

规格：[specs/07-library-sync-and-backup.md](specs/07-library-sync-and-backup.md)。状态：未执行。

- 正常验收：远端 404 经过权限和 branch check 后视为首份；明确 empty repository 路径只初始化默认分支。
- 失败验收：远端返回无效/损坏索引时不尝试以本机空配置覆盖。

## REQ-079：Usage 数据与请求档案

规格：[specs/07-library-sync-and-backup.md](specs/07-library-sync-and-backup.md)。状态：未执行。

- 正常验收：同步 usage 后，另一台电脑看到用量趋势，但它的 local gateway recording switch 没有被打开。
- 失败验收：没有 archive destination 时不写本机 prompt body；达到容量限制后移除最旧请求并继续转发。

## REQ-090：凭据存放和界面遮蔽

规格：[specs/09-privacy-security-and-platforms.md](specs/09-privacy-security-and-platforms.md)。状态：未执行。

- 正常验收：显示 Provider详情时 key 只有尾部掩码；用户替换后详情/下一次调用使用新值。
- 失败验收：安全存储不可读时停止导出/同步/认证操作并说明，不把空 key 误认为用户删除。

## REQ-091：网络入口、鉴权和限流

规格：[specs/09-privacy-security-and-platforms.md](specs/09-privacy-security-and-platforms.md)。状态：未执行。

- 正常验收：loopback client 本机免 key；远程 LAN 请求只对有效 key 放行并遵守其模型和额度限制。
- 失败验收：无效 key、origin/host spoofing、CSRF 或 client credential 窃取尝试按失败处理并记录 request id，不返回敏感数据。

## REQ-092：Privacy 默认和本机数据

规格：[specs/09-privacy-security-and-platforms.md](specs/09-privacy-security-and-platforms.md)。状态：未执行。

- 正常验收：新安装没有 prompt archive，默认 Logs 不含 messages。
- 失败验收：设置保存失败时 UI 明确显示，不能提示保护设置已成功启用。

## REQ-093：Private URL、代理和 SSRF

规格：[specs/09-privacy-security-and-platforms.md](specs/09-privacy-security-and-platforms.md)。状态：未执行。

- 正常验收：用户明确配置的私有 Git/npm/relay 地址可访问并显示来源。
- 失败验收：不可信 webhook/MCP/page 将 HTTP redirect 指向本机 admin endpoint 时，下载器阻止请求并提示 SSRF policy。

## REQ-094：多平台和 WSL 边界

规格：[specs/09-privacy-security-and-platforms.md](specs/09-privacy-security-and-platforms.md)。状态：未执行。

- 正常验收：Windows/WSL 中 Claude Code 两套配置可各自连接 Gateway、拥有正确会话归属。
- 失败验收：WSL distro 停止时 UI 显示 last-seen/asleep，不自动启动；系统代理配置不完整时不假称 Agent 经代理连接。

## REQ-095：隐私工具与外部上游

规格：[specs/09-privacy-security-and-platforms.md](specs/09-privacy-security-and-platforms.md)。状态：未执行。

- 正常验收：用户请求 image generation 后 Usage 能指出用哪个模型与 Provider。
- 失败验收：用户关闭 search/tool 时，上游不再接收相应隐式工具调用。
