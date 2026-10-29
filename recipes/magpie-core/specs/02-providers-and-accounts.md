# 02 · Providers、订阅账户和模型目录

## REQ-020：Provider 目录和新增

- 优先级：核心。Provider 是 API 密钥、OAuth/订阅账户、插件、远程 magpie 或本地推理服务。
- Provider 页面显示启用状态、名称、API host、已配置密钥的脱敏尾部、模型数量和正在使用它的 Agent；每个 Provider 有详情/编辑、测试、刷新和移除操作。
- 提供预设厂商与中继服务。当前基线的主要预设：Anthropic、OpenAI、Google Gemini、Google Vertex AI、DeepSeek、xAI、Kimi（中国与全球）、Kimi Code、中国智谱 GLM、Z.ai、MiniMax、StepFun、Xiaomi MiMo、Baidu Qianfan、Tencent Cloud、Huawei Cloud MaaS、Volcengine Ark、Qwen、中国 Qwen Token Plan、Bailian Token Plan、Mistral、Groq、Amazon Bedrock、Bedrock Mantle、Azure OpenAI、Ollama Cloud、Nous Portal、OpenRouter、OpenCode Go、ClinePass、OpenCode Zen、Kilo Gateway、Command Code、Together AI、Fireworks、SiliconFlow、NVIDIA NIM、ModelScope、AiHubMix、PipeLLM、302.AI、CherryIN、鱼鱼连线、TypeSafe Jev、Vercel AI Gateway Jev、Cloudflare Workers AI Jev/Clef、Bailian Decision Model、Qwen Decision Model、Ollama、LM Studio、oMLX、MLX-Serve、Remote magpie。带地区/套餐的项目须提供相应 Region 选择。
- 允许用户增加自定义 Provider：名称、OpenAI-compatible Chat base、Anthropic-compatible base、可选 Responses base、Catalog 来源、可选模型 ID、地区、请求 Header、自定义价格、User-Agent 策略和密钥。
- Add 页的预设应按 vendor、relay/local 等类型分组，可搜索；没有密钥的本地 Provider 不强制要求密钥。未知或已退休预设不能错误显示为仍由内置代码直接服务的账号。
- 固定原版隔离 Web 还显示订阅、厂商、转接、本机及远程 Magpie 分组。搜索无匹配时可直接进入自定义 Provider 或查找插件。自定义表单让用户直接输入密钥，提供 OpenAI compatible、Responses、Anthropic、Gemini 和仅供路由决策的 System One 入口；可选择/手动输入模型。密钥界面输入与安全存储必须实际实现，不能仅用进程环境变量引用代替。原版表单的存储提示及隔离观察见根配方 `ORIGINAL_WEB_BEHAVIOR.md`；这里不要求重建沿用其明文存储方式。
- 正常验收：加 DeepSeek 只需输入 key；加本地 Ollama 可不填 key；加自定义兼容服务需同时设地址和模型。
- 失败验收：网址 scheme、密钥字段或必需的 base URL 不合法时拒绝保存并指出字段；不向任意地址发送真实 key 进行校验。

## REQ-021：多个上游密钥和 Provider 编辑

- 优先级：核心。
- 一个 Provider 可有多个上游密钥/账号；用户可以增删、排序、改名、轮换或暂停。密钥显示遮蔽值，复制/重新显示需显式操作。删除后 Usage 历史仍能识别原密钥身份，但不得显示凭据。
- 支持请求 Header、模型过滤/自动包含模式、手动模型 ID、用户价格、刷新周期、fallback 模型，以及请求 User-Agent 策略。修改某项不应静默清除其它设置。
- 自定义 Provider 的高级选项包括额外 Header 和请求 body、全局/直连/自定义代理、并发、每 key/账户请求速率、队列长度与等待时间、价格倍率、Agent User-Agent、限流休眠、余额认证、上下文及输出/压缩限制。可分别配置 Anthropic、Responses、Gemini、模型列表、目录和余额端点；模型选择包括供应商列表、仅选中项、仅 Usage 与仅经路由组。界面声明的默认值、替换语义和端点行为要通过隔离上游单独验证，不能因为看到控件就声称实现完成。
- 一次 Provider 测试用一个低成本请求检查配置，报告成功、上游状态、错误和耗时；刷新模型列表单独操作。
- 正常验收：轮换第一把 key 后新请求使用新 key，历史仍归属于原 key。
- 失败验收：上游拒绝 key 时保留配置并标识失败，不能把 Provider 删除或把空列表当成功。

## REQ-022：模型列表和目录元数据

- 优先级：核心。
- 在可用时向 Provider 查询真实模型列表；通过公共模型目录补充展示名、能力、reasoning effort、上下文窗口及价格。不能向模型目录提交用户凭据。
- 用户可以选择暴露所有模型、显式选择若干模型，或设置 glob/正则式让后来新增的模型自动暴露。刷新后删除已不再提供的显式选择，但一次请求遇到“deprecated/retired”响应时保留该模型一段时间，直至确认失效或目录刷新规则允许移除。
- Model 名称对 Agent 默认使用 `provider/model`；可给显示名称、价格和 reasoning levels 设置覆盖，重置后回到目录数据。
- 同一真实模型被多个 Provider 提供时，按 Provider 标识区分；价格和能力以该路由对应的有效配置为准。
- 正常验收：刷新后出现新增模型并按配置自动包含；手工选择的模型可从列表移除或加入。
- 失败验收：目录服务不可达时保留上次成功列表并标记数据陈旧，不清空模型。

## REQ-023：用户可导入现有 Provider

- 优先级：一般。
- 可从 Claude Code、Codex、自身支持的 Agent、本机 OpenCode sign-ins 和带 `magpie://import?...` 的链接预览 Provider 配置；显示导入来源、被带入字段和冲突。默认仅读取，不自动持续镜像源应用后续改动。
- OpenCode 的 sign-in 只读；不得把登录材料写回 OpenCode。导入带密钥文件时先让用户确认，成功迁移账号后说明源工具的登录是否会因 token 刷新而被注销。
- `magpie://import` 只按明确允许的 scheme/host/参数解析，允许预设 id、地区、名称、模型、URL 和 key；换行注入、未知预设和非法 URL 必须拒绝。
- 正常验收：导入 Codex 的 OpenAI-compatible Provider，确认后出现在 Provider 列表。
- 失败验收：来源文件不可读、只含 env key 引用或内容不完整时跳过该项并说明原因，不清空原设置。

## REQ-024：发现已登录订阅

- 优先级：核心。仅当用户设置允许时读取 Agent 的本机登录状态。
- 支持发现并复用受支持的 Claude Code、ChatGPT/Codex、GitHub Copilot、Gemini CLI、Antigravity、Devin、Qoder/Qoder CN 等订阅账户。配置为默认开启，用户可在 Privacy 中关闭或运行一次性禁用环境开关；禁用发现不影响 API-key Provider。
- 每账户显示所属 Provider、登录身份、当前套餐/额度和可服务模型。支持新增账户、切换、刷新、别名、暂停、导入或移除。
- 读取 Agent 登录材料时，应尽量使用 Agent 自己的登录文件/系统密钥链和刷新协议；只保存运行所需的最少数据。已退出的账号不继续作为可用 Provider。
- Claude subscription generation 通过本机已安装且登录的官方 Claude CLI；Copilot、Devin、Cursor、Google 等使用各自受支持的 API/客户端认证兼容行为。缺少必要官方客户端/服务条件时明确显示未就绪。
- 正常验收：在 Claude Code 签入的账户出现在 Provider 列表，其原生模型可由另一 Agent 经 Gateway 使用。
- 失败验收：token 过期、账号退出、企业域/Cloud Project 不匹配时显示登录或权限错误，不能无限刷新或把账户判成有额度。

## REQ-025：已弃用订阅的社区插件交接

- 优先级：核心。当前基线已把下面的订阅移到同职能的社区 OpenCode 插件；magpie 侧插件 host/迁移状态仍需支持。移交的插件负责自己的登录、刷新、模型、请求、用量和错误。

| 订阅 id | 插件包 |
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

- 对已经退休的内置订阅，Add Provider 不再展示旧实现；新用户从 Plugins 添加社区插件。老账户未迁移时显示「未迁移」提示和 Move 操作。
- 用户主动 Move 或 Adopt 后记录 `plugin` 所有权；插件手动安装时按账户条件立即或后台交接。迁移账户前后，Provider 模型和调用只能由当前所有者服务。
- 内置服务已退休后的账户若未完成迁移，不得继续伪称可用；展示需要迁移的账户、失败原因和可重试操作。
- 退役迁移是单向的：后台定期对所有仍有内置账号的 retired provider 执行迁移，包括用户先前移回的账户。插件停用时对应订阅停用；删除插件记录 `removed`，保留插件自己的登录数据；重装后可恢复订阅。
- 正常验收：安装 `opencode-cursor-auth` 后，Cursor 订阅显示为插件提供，Gateway 把请求交给插件。
- 失败验收：迁移失败时不丢原账户材料，标记失败并在间隔后重试；旧内置代码已退休的账户明确标记暂不可服务。

## REQ-026：套餐、额度和余额

- 优先级：核心。
- 对支持的订阅/Key 读取额度窗口、剩余量、重置时间、余额、计划 tier、并发/速率限制；记录最近成功读取时间及错误。Usage 卡片、Provider 详情、托盘 Allowances 与 CLI `magpie quota` 使用一致的有效数据。
- 可按账户暂停、限制可用模型、设置每日/每周/月 token 或费用预算、并发和 RPM 上限；管理员可给每个客户端 Gateway key 设置预算、模型、账户限制。
- 账户可以用户自定义别名。重置/刷新后旧历史要保留。达到额度上限前提醒；自动切换账号时不弹出阻断对话框。
- ChatGPT 额度消耗后是否继续用可用 credits 是逐账户选择；关闭后，最新读取窗口达到上限时跳过到重置。只影响 Gateway，不改变 Agent 原生登录状态。
- 正常验收：账户额度降低后下一请求避开账户，并在重置后恢复。
- 失败验收：额度读取失败时将状态标为未知并保留上次已知值及时间，不把“读取失败”当成无限额度。

## REQ-027：账户顺序和约束

- 优先级：一般。
- 用户可指定默认账户、切换 Agent 当前订阅账户、按账户开关服务模型，设置“仅此账号服务/只切换当前 Agent 登录”等语义。
- 同一计费座席若同时以 key 和 OAuth 登录出现，只能在额度汇总中计一次，并可标出座席关联；发生冲突时避免重复计算额度。
- 支持账户并发数、请求速率、等待队列和优先级；同一账户超出限制时进入队列或跳过，符合 Gateway 规则。
- 正常验收：将一个账户暂停，只对该账户发出的新请求被绕过，另一个未暂停账户继续服务。
- 失败验收：身份读不全或两个座席无法确认相同，展示为未知/独立，不基于邮箱相似度擅自合并。

## REQ-028：计划与本机订阅使用限制

- 优先级：一般。
- 对 Claude、Gemini 等因官方客户端/特定套餐受限的订阅，说明需要的客户端、Cloud Project、登录方式和任何风险确认；Google Cloud project 可按账户配置。
- 添加 Antigravity 账户前，出现明确的风险说明与确认。不得把官方允许某账户用于独立产品当作所有账号均安全。
- `magpie accounts import` 支持已文档化的 Codex、Claude、Antigravity、Factory 账号导出；显示文件来源和导入结果，不把不具备续期能力的短期 web-session token 当作可用账户。
- 正常验收：导入多账户文件后保留每个独立团队席位；重复账号不重复添加。
- 失败验收：只含过期 access token 而无续期信息的登录拒绝导入并说明原因。

## REQ-029：账号和模型状态对外说明

- 优先级：一般。
- Provider 页、`magpie providers`、`magpie accounts` 和 quota API 应能说明：服务谁、哪些 Agent 使用、模型清单、额度/余额是否已读、错误和迁移状态。
- 错误需要区分账号不可用、模型不可用、额度耗尽、请求内容被拒、暂时速率限制、代理/网络错误和未知。不能因错误来自某服务就一律把账户标成失效。
- 正常验收：账户登录失效的错误旁给重登入口；一个模型被拒但账户其他模型可用时不把整个账户停用。
- 失败验收：状态源无法读取时显示未知，禁止静默清空账户或额度信息。
