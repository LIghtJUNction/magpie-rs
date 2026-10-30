# 需求覆盖索引

93 个唯一 REQ；这里只表示已迁移到配方，不表示软件全部通过验收。所有条目均保留完整端到端验收要求。2026-10-30 增加共享 Provider、系统凭据、实时网关与原生 UI 的实现证据；此前被否决的 UI 不作为通过基线，本轮实际 GPUI 渲染仍需用户视觉认可。

配方 1.3.5 新增 [逐任务对照与证据规则](PARITY_VERIFICATION.md)，按此表的编号核对覆盖索引，93 项均有任务映射；这是配方追踪覆盖，不表示实现覆盖或原版行为已经全部采集。原版用户任务的未知项、独立重建、完整平台及真实账户仍须逐项实测。

| REQ | 需求 | 所属配方 | 归档内规格 | 状态 |
| --- | --- | --- | --- | --- |
| REQ-001 | 只显示可用 Agent | magpie | `./GLOBAL.md` | 部分：CLI 使用隔离 PATH 验证只列出已安装支持项；桌面用户验收未通过 |
| REQ-002 | 保持用户已有数据 | magpie | `./GLOBAL.md` | 部分：Codex隔离配置保留注释/未知字段与原字节恢复已测；其他Agent、真实用户数据及全部异常未验收 |
| REQ-003 | 显式连接与断开 | magpie | `./GLOBAL.md` | 部分：CLI及桌面ActionsState→真实CLI的预览/保存/离线恢复已测；两种原配置状态通过真实Codex，原生窗口操作未验收 |
| REQ-004 | 统一模型选择和每 Agent 可见列表 | magpie | `./GLOBAL.md` | 部分：Codex 真实模型选择使用统一 Provider/model 目录并虚拟化长列表；逐 Agent 可见性和其他 Agent 应用未实现 |
| REQ-005 | 用户可保存和切换 Profile | magpie | `./GLOBAL.md` | 未执行 |
| REQ-010 | 多个入口反映同一状态 | magpie-core | `.deps/interfaces/.deps/gateway/.deps/core/specs/01-product-and-agents.md` | 部分：CLI/GPUI/Gateway 共用版本化 ProviderStore，跨进程保存、刷新、轮换及下一请求生效已测；Codex配置闭环保留，Web/TUI及其他产品状态未齐备 |
| REQ-011 | 发现和列出 Agent | magpie-core | `.deps/interfaces/.deps/gateway/.deps/core/specs/01-product-and-agents.md` | 部分：68项目录中保留原五项并新增八项无歧义 PATH 候选，CLI/桌面共用入口与隔离 fixture 已测；命中不等于配置可用，其他 Agent 配置和完整操作流程未实现 |
| REQ-012 | 兼容 Agent 的公开配置格式 | magpie-core | `.deps/interfaces/.deps/gateway/.deps/core/specs/01-product-and-agents.md` | 部分：Codex公开TOML配置的有界读取和事务已测；其他Agent格式及版本兼容未实现 |
| REQ-013 | 只做精确、可恢复的字段编辑 | magpie-core | `.deps/interfaces/.deps/gateway/.deps/core/specs/01-product-and-agents.md` | 部分：Codex七个字段、同文件系统私有事务、原字节/漂移选择性恢复及并发/SIGKILL后锁释放与中断恢复fixture已测；真实断电、Windows写入和其他Agent未验收 |
| REQ-014 | 接入、漂移和断开 | magpie-core | `.deps/interfaces/.deps/gateway/.deps/core/specs/01-product-and-agents.md` | 部分：Codex接入/归属/漂移/恢复的组件及CLI fixture、强杀后恢复已测；真实Codex两场景离线恢复通过，窗口与其他Agent未验收 |
| REQ-015 | Agent 专属字段和 Profile 保留 | magpie-core | `.deps/interfaces/.deps/gateway/.deps/core/specs/01-product-and-agents.md` | 未执行 |
| REQ-016 | 跨 Agent 模型目录 | magpie-core | `.deps/interfaces/.deps/gateway/.deps/core/specs/01-product-and-agents.md` | 部分：统一 Provider/model 目录接入 CLI、Gateway、Codex桌面选择器；其他 Agent 模型适配和覆盖未齐备 |
| REQ-017 | 每 Agent 模型可见性 | magpie-core | `.deps/interfaces/.deps/gateway/.deps/core/specs/01-product-and-agents.md` | 未执行 |
| REQ-018 | 保存、应用和删除 Profile | magpie-core | `.deps/interfaces/.deps/gateway/.deps/core/specs/01-product-and-agents.md` | 未执行 |
| REQ-019 | 变化重扫和状态反馈 | magpie-core | `.deps/interfaces/.deps/gateway/.deps/core/specs/01-product-and-agents.md` | 部分：Snapshot刷新、缺失文件不保留旧托管值、ActionsState重读归属已测；完整变化监控与窗口焦点未验收 |
| REQ-020 | Provider 目录和新增 | magpie-core | `.deps/interfaces/.deps/gateway/.deps/core/specs/02-providers-and-accounts.md` | 部分：原生自定义Provider、4种单一协议选择、直接key输入及系统凭据存储、无key本地服务、启停/删除已实现；完整预设/Region/订阅/高级选项与真实账户未验收 |
| REQ-021 | 多个上游密钥和 Provider 编辑 | magpie-core | `.deps/interfaces/.deps/gateway/.deps/core/specs/02-providers-and-accounts.md` | 部分：单凭据编辑/轮换/清除与事务清理已测，新请求即时读取新key；多key/账户排序、Usage归属、请求测试及高级选项未实现 |
| REQ-022 | 模型列表和目录元数据 | magpie-core | `.deps/interfaces/.deps/gateway/.deps/core/specs/02-providers-and-accounts.md` | 部分：4协议真实合成HTTP模型发现、Anthropic/Gemini分页、失败保留旧目录与revision冲突已测；公共价格/能力元数据、过滤规则和自动刷新未实现 |
| REQ-023 | 用户可导入现有 Provider | magpie-core | `.deps/interfaces/.deps/gateway/.deps/core/specs/02-providers-and-accounts.md` | 未执行 |
| REQ-024 | 发现已登录订阅 | magpie-core | `.deps/interfaces/.deps/gateway/.deps/core/specs/02-providers-and-accounts.md` | 未执行 |
| REQ-025 | 已弃用订阅的社区插件交接 | magpie-core | `.deps/interfaces/.deps/gateway/.deps/core/specs/02-providers-and-accounts.md` | 未执行 |
| REQ-026 | 套餐、额度和余额 | magpie-core | `.deps/interfaces/.deps/gateway/.deps/core/specs/02-providers-and-accounts.md` | 未执行 |
| REQ-027 | 账户顺序和约束 | magpie-core | `.deps/interfaces/.deps/gateway/.deps/core/specs/02-providers-and-accounts.md` | 未执行 |
| REQ-028 | 计划与本机订阅使用限制 | magpie-core | `.deps/interfaces/.deps/gateway/.deps/core/specs/02-providers-and-accounts.md` | 未执行 |
| REQ-029 | 账号和模型状态对外说明 | magpie-core | `.deps/interfaces/.deps/gateway/.deps/core/specs/02-providers-and-accounts.md` | 部分：Provider启用状态、模型数、凭据来源及公开CLI目录一致；账户/额度/迁移/Agent使用归属及完整错误分类未实现 |
| REQ-030 | 本地 Gateway 生命周期 | magpie-gateway | `.deps/interfaces/.deps/gateway/specs/03-gateway-and-protocols.md` | 部分：真实TCP启动、健康/冲突/限额及共享Provider运行时更新已测；桌面启停/托盘生命周期和当前版本各系统安装恢复未验收 |
| REQ-031 | 请求入口与模型标识 | magpie-gateway | `.deps/interfaces/.deps/gateway/specs/03-gateway-and-protocols.md` | 部分：Provider/model选择、实时多Provider路由、HTTPS传输规则和四协议合成HTTP/SSE回归已测；外部TLS真实账户及全部字段兼容未验收 |
| REQ-032 | 协议转换和流式行为 | magpie-gateway | `.deps/interfaces/.deps/gateway/specs/03-gateway-and-protocols.md` | 部分：四协议12种定向纯转换、实际合成HTTP/SSE转换、取消/错误及共享Provider接线回归通过；原生首SSE错误竞态既有修复保留，官方全部字段/事件和真实上游未验收 |
| REQ-033 | 错误、状态码和请求归属 | magpie-gateway | `.deps/interfaces/.deps/gateway/specs/03-gateway-and-protocols.md` | 部分：固定脱敏错误、上游错误/不完整流与并发拒绝已测；完整请求归属/Usage和外部上游兼容未实现 |
| REQ-034 | 模型目录契约 | magpie-gateway | `.deps/interfaces/.deps/gateway/specs/03-gateway-and-protocols.md` | 部分：启用Provider目录统一CLI/HTTP/桌面inspect，显式模型发现原子更新，16384模型及JSON转义膨胀边界已测；configured保持not_probed，自动刷新/完整元数据未实现 |
| REQ-035 | 本机访问和远程鉴权 | magpie-gateway | `.deps/interfaces/.deps/gateway/specs/03-gateway-and-protocols.md` | 部分：仅字面loopback与Host/Origin等访问限制已测；LAN分享、受限key和远程鉴权未实现 |
| REQ-036 | Agent 连接到网关 | magpie-gateway | `.deps/interfaces/.deps/gateway/specs/03-gateway-and-protocols.md` | 部分：安装版Codex0.165在隔离HOME经真实Gateway收到中文Responses流并恢复；其他Agent/真实账户未验收 |
| REQ-037 | 会话、账户固定和观测 API | magpie-gateway | `.deps/interfaces/.deps/gateway/specs/03-gateway-and-protocols.md` | 未执行 |
| REQ-038 | 远程 magpie | magpie-gateway | `.deps/interfaces/.deps/gateway/specs/03-gateway-and-protocols.md` | 未执行 |
| REQ-039 | Gateway 的附加查询接口 | magpie-gateway | `.deps/interfaces/.deps/gateway/specs/03-gateway-and-protocols.md` | 未执行 |
| REQ-040 | 建立模型路由组 | magpie-core | `.deps/interfaces/.deps/gateway/.deps/core/specs/04-routing-and-failover.md` | 部分：内存中有界组/嵌套校验已测；持久化管理及网关未接入 |
| REQ-041 | 路由策略 | magpie-core | `.deps/interfaces/.deps/gateway/.deps/core/specs/04-routing-and-failover.md` | 部分：七种旧策略的合成快照纯计算36项测试通过；原版隔离 Web 另显示 Free first、Manual，两项实际网关语义尚未验收；实际网关选择尚未接入 |
| REQ-042 | 选择可服务成员和 account 顺序 | magpie-core | `.deps/interfaces/.deps/gateway/.deps/core/specs/04-routing-and-failover.md` | 部分：未知额度、权限与限制等候选过滤已测；真实账户快照及网关未接入 |
| REQ-043 | 按请求上下文匹配路由规则 | magpie-core | `.deps/interfaces/.deps/gateway/.deps/core/specs/04-routing-and-failover.md` | 未执行 |
| REQ-044 | Session affinity 和重置 | magpie-core | `.deps/interfaces/.deps/gateway/.deps/core/specs/04-routing-and-failover.md` | 未执行 |
| REQ-045 | 重试和 Fallback 边界 | magpie-core | `.deps/interfaces/.deps/gateway/.deps/core/specs/04-routing-and-failover.md` | 未执行 |
| REQ-046 | 实时路由解释和历史 | magpie-core | `.deps/interfaces/.deps/gateway/.deps/core/specs/04-routing-and-failover.md` | 未执行 |
| REQ-047 | System One / 模型决策 | magpie-core | `.deps/interfaces/.deps/gateway/.deps/core/specs/04-routing-and-failover.md` | 未执行 |
| REQ-048 | Web 搜索配置和 Provider 代搜 | magpie-core | `.deps/interfaces/.deps/gateway/.deps/core/specs/04-routing-and-failover.md` | 未执行 |
| REQ-049 | Agent 工具服务 | magpie-core | `.deps/interfaces/.deps/gateway/.deps/core/specs/04-routing-and-failover.md` | 未执行 |
| REQ-050 | 汇总 Usage | magpie-gateway | `.deps/interfaces/.deps/gateway/specs/05-usage-and-sessions.md` | 未执行 |
| REQ-051 | 额度和窗口 | magpie-gateway | `.deps/interfaces/.deps/gateway/specs/05-usage-and-sessions.md` | 未执行 |
| REQ-052 | Cap、预算和计算 | magpie-gateway | `.deps/interfaces/.deps/gateway/specs/05-usage-and-sessions.md` | 未执行 |
| REQ-053 | 查询和分析 | magpie-gateway | `.deps/interfaces/.deps/gateway/specs/05-usage-and-sessions.md` | 未执行 |
| REQ-054 | Session 列表 | magpie-gateway | `.deps/interfaces/.deps/gateway/specs/05-usage-and-sessions.md` | 未执行 |
| REQ-055 | 消息内容、记录开关与保留 | magpie-gateway | `.deps/interfaces/.deps/gateway/specs/05-usage-and-sessions.md` | 未执行 |
| REQ-056 | 隐私遮蔽 | magpie-gateway | `.deps/interfaces/.deps/gateway/specs/05-usage-and-sessions.md` | 未执行 |
| REQ-057 | Pricing | magpie-gateway | `.deps/interfaces/.deps/gateway/specs/05-usage-and-sessions.md` | 未执行 |
| REQ-058 | 导出 | magpie-gateway | `.deps/interfaces/.deps/gateway/specs/05-usage-and-sessions.md` | 未执行 |
| REQ-059 | Quota/Usage API 与请求追踪 | magpie-gateway | `.deps/interfaces/.deps/gateway/specs/05-usage-and-sessions.md` | 未执行 |
| REQ-060 | 内置 Provider 与社区插件边界 | magpie-gateway | `.deps/interfaces/.deps/gateway/specs/06-plugins-and-tools.md` | 未执行 |
| REQ-061 | 社区 Provider 插件查找和安装 | magpie-gateway | `.deps/interfaces/.deps/gateway/specs/06-plugins-and-tools.md` | 未执行 |
| REQ-062 | 社区 Provider 插件运行 | magpie-gateway | `.deps/interfaces/.deps/gateway/specs/06-plugins-and-tools.md` | 未执行 |
| REQ-063 | 订阅账号转移 | magpie-gateway | `.deps/interfaces/.deps/gateway/specs/06-plugins-and-tools.md` | 未执行 |
| REQ-064 | 插件登录和模型目录 | magpie-gateway | `.deps/interfaces/.deps/gateway/specs/06-plugins-and-tools.md` | 未执行 |
| REQ-065 | Web 和其它扩展工具 | magpie-gateway | `.deps/interfaces/.deps/gateway/specs/06-plugins-and-tools.md` | 未执行 |
| REQ-066 | 社区 Provider 插件可观测性和移除 | magpie-gateway | `.deps/interfaces/.deps/gateway/specs/06-plugins-and-tools.md` | 未执行 |
| REQ-067 | Magpie 内置功能插件 | magpie-gateway | `.deps/interfaces/.deps/gateway/specs/06-plugins-and-tools.md` | 未执行 |
| REQ-068 | Gateway extension points | magpie-gateway | `.deps/interfaces/.deps/gateway/specs/06-plugins-and-tools.md` | 未执行 |
| REQ-069 | 插件种类和状态区分 | magpie-gateway | `.deps/interfaces/.deps/gateway/specs/06-plugins-and-tools.md` | 未执行 |
| REQ-070 | 统一 Library | magpie-core | `.deps/interfaces/.deps/gateway/.deps/core/specs/07-library-sync-and-backup.md` | 未执行 |
| REQ-071 | Instructions、skills 和 Agent 级操作 | magpie-core | `.deps/interfaces/.deps/gateway/.deps/core/specs/07-library-sync-and-backup.md` | 未执行 |
| REQ-072 | MCP servers | magpie-core | `.deps/interfaces/.deps/gateway/.deps/core/specs/07-library-sync-and-backup.md` | 未执行 |
| REQ-073 | Projects | magpie-core | `.deps/interfaces/.deps/gateway/.deps/core/specs/07-library-sync-and-backup.md` | 未执行 |
| REQ-074 | RTK 输出压缩 | magpie-core | `.deps/interfaces/.deps/gateway/.deps/core/specs/07-library-sync-and-backup.md` | 未执行 |
| REQ-075 | 本地文件备份和可选区段 | magpie-core | `.deps/interfaces/.deps/gateway/.deps/core/specs/07-library-sync-and-backup.md` | 未执行 |
| REQ-076 | 加密远端同步 | magpie-core | `.deps/interfaces/.deps/gateway/.deps/core/specs/07-library-sync-and-backup.md` | 未执行 |
| REQ-077 | 每台电脑保留的设置 | magpie-core | `.deps/interfaces/.deps/gateway/.deps/core/specs/07-library-sync-and-backup.md` | 部分：本机桌面主题和五语偏好持久化及旧revision冲突已测；完整同步边界、其他本机设置未实现 |
| REQ-078 | 远端内容和恢复保护 | magpie-core | `.deps/interfaces/.deps/gateway/.deps/core/specs/07-library-sync-and-backup.md` | 未执行 |
| REQ-079 | Usage 数据与请求档案 | magpie-core | `.deps/interfaces/.deps/gateway/.deps/core/specs/07-library-sync-and-backup.md` | 未执行 |
| REQ-080 | 运行形态 | magpie-interfaces | `.deps/interfaces/specs/08-cli-tui-and-web.md` | 部分：无显示CLI及独立GPUI组件、实际离屏Scene通过；桌面完整窗口/托盘/后台生命周期、Web/TUI未验收 |
| REQ-081 | 桌面导航和设置 | magpie-interfaces | `.deps/interfaces/specs/08-cli-tui-and-web.md` | 部分：Agents/Providers/Settings真实入口、宽侧栏与中窄顶部导航、主题语言保存已实现；原版全部页面/设置和系统快捷键未齐备 |
| REQ-082 | 浏览器 Web UI | magpie-interfaces | `.deps/interfaces/specs/08-cli-tui-and-web.md` | 未执行 |
| REQ-083 | TUI | magpie-interfaces | `.deps/interfaces/specs/08-cli-tui-and-web.md` | 未执行 |
| REQ-084 | CLI 接口 | magpie-interfaces | `.deps/interfaces/specs/08-cli-tui-and-web.md` | 部分：原版agents/ls有限候选输出字节对照保留，新增Provider CRUD/启停/模型刷新及共享models/serve；原版全部命令和非空真实账户输出未完整对齐 |
| REQ-085 | Agent 状态、更新和配置流程 | magpie-interfaces | `.deps/interfaces/specs/08-cli-tui-and-web.md` | 未执行 |
| REQ-086 | 应用更新和可诊断错误 | magpie-interfaces | `.deps/interfaces/specs/08-cli-tui-and-web.md` | 未执行 |
| REQ-090 | 凭据存放和界面遮蔽 | magpie-core | `.deps/interfaces/.deps/gateway/.deps/core/specs/09-privacy-security-and-platforms.md` | 部分：直接key输入遮蔽、Secret Service实测、公共配置仅存引用、轮换回收/失败回滚与脱敏已测；macOS/Windows凭据后端及原生读屏未验收 |
| REQ-091 | 网络入口、鉴权和限流 | magpie-core | `.deps/interfaces/.deps/gateway/.deps/core/specs/09-privacy-security-and-platforms.md` | 未执行 |
| REQ-092 | Privacy 默认和本机数据 | magpie-core | `.deps/interfaces/.deps/gateway/.deps/core/specs/09-privacy-security-and-platforms.md` | 未执行 |
| REQ-093 | Private URL、代理和 SSRF | magpie-core | `.deps/interfaces/.deps/gateway/.deps/core/specs/09-privacy-security-and-platforms.md` | 部分：Provider HTTPS、loopback HTTP、URL结构校验、localhost固定解析、拒绝重定向/隐式环境代理与self-target已测；显式代理、远程分享及全部网络组合未实现 |
| REQ-094 | 多平台和 WSL 边界 | magpie-core | `.deps/interfaces/.deps/gateway/.deps/core/specs/09-privacy-security-and-platforms.md` | 部分：旧版本Android四ABI/双ARM真机证据保留为历史；本轮Linux x86_64已测，新增keyring依赖后的macOS/Windows/Android/NixOS/WSL未复验，Windows配置写入仍拒绝 |
| REQ-095 | 隐私工具与外部上游 | magpie-core | `.deps/interfaces/.deps/gateway/.deps/core/specs/09-privacy-security-and-platforms.md` | 未执行 |
| REQ-100 | 视觉方向 | magpie-interfaces | `.deps/interfaces/specs/10-visual-and-localization.md` | 部分：按原版白灰/炭黑/紫色及连续列表重整，真实GPUI多尺寸浅深Scene已检查；完整页面与用户视觉认可未通过，旧被否决方案不作通过证据 |
| REQ-101 | App 和紧凑 Panel 布局 | magpie-interfaces | `.deps/interfaces/specs/10-visual-and-localization.md` | 部分：1280宽侧栏、900顶部、360/440窄窗的Agents/Providers/表单/Settings原生Scene已渲染；托盘Panel、quota等全产品场景未实现 |
| REQ-102 | 语言 | magpie-interfaces | `.deps/interfaces/specs/10-visual-and-localization.md` | 部分：五语文本、CJK/grapheme输入及主题语言持久化/冲突已测；原生IME、完整日期数字本地化未验收 |
| REQ-103 | 可访问性与操作反馈 | magpie-interfaces | `.deps/interfaces/specs/10-visual-and-localization.md` | 部分：稳定焦点/取消/保存失败保留输入/不确定结果重读及密钥不可复制的组件路径已测；原生全键盘、IME/读屏及全产品辅助技术未验收 |
| REQ-104 | 参考截图与产品数据 | magpie-interfaces | `.deps/interfaces/specs/10-visual-and-localization.md` | 部分：依据固定原版来源和配方文字层级，新GPUI Scene明确使用隔离合成数据；未将参考截图作为业务数据或当前产品像素证据 |

## 设计合同依赖（2026-10-29）

配方 1.3.3 增加固定版本 design-directions 及递归 design-foundations，已完成来源/全部正文阅读与产品作用范围、备选取舍和验收合同集成。现有 93 REQ/68 Agent 是基线，原版额外观察仍需纳入；全部功能及用户视觉认可继续未完成。依赖归档与实际界面/功能验证分别记录，不能将设计材料阅读算作通过。
