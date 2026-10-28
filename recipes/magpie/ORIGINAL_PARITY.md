# 原版功能与视觉基线核对

记录日期：2026-10-29 Asia/Taipei；截图索引及功能核对矩阵已完成。Rust 静态核对固定到 `dd5eed87b6d14213aeb604b50dd23d6d25a03f66`。配方以 `24-original-parity-evidence/recipe-manifest.json` 的逐文件读取时 SHA-256 为边界，根配方/覆盖表的已知并行更新不暂停任务。

本次仅执行原版 version/help、读取公开官方静态网页/图片、读取本仓历史公开资产元数据和当前 Rust 固定提交。68 Agent / 93 REQ 是保留的最低基线，不是能力上限；Rust、GPUI、低内存和多平台目标继续有效；此前两轮 Pro 视觉均未通过。未读取真实账户/配置，未启动原版 GUI/服务，未进行 Provider 请求、编译、测试、提交或仓库修改。

## 1. 可直接交接的公开截图索引

来源链：本仓历史 `b4bcfe2:README.md` 的官网/Docs 链接 → 官网公开页面中的字面 `src`/图片字符串 → 同源静态 PNG。没有按文件名猜测任何未列出的 URL。公开网页原文、时间、SHA-256、图片声明页和实际 PNG 均保存在同名 evidence 目录。官方网页与安装版不是同一版本证据；以下是公开设计参照，不是当前安装版实机截图。

官网入口：<https://usemagpie.ai/>；文档入口：<https://usemagpie.ai/docs/start>；Intent：<https://usemagpie.ai/docs/intent>；Import：<https://usemagpie.ai/docs/import>；Privacy：<https://usemagpie.ai/docs/privacy>。

| 参照文件 | 准确公开 URL | 声明页 | 像素尺寸 | 实际 SHA-256 | 与 UI_REFERENCE 指纹 |
| --- | --- | --- | --- | --- | --- |
| add-light.png | <https://usemagpie.ai/img/add-light.png> | /docs/start | 2080×1250 | `fc143fa366512b7c3982b6fa8eecd681c2d9403e8be8c9c671aa81095b2f4513` | 一致 |
| agents-dark.png | <https://usemagpie.ai/img/agents-dark.png> | /docs/start | 2080×1256 | `bc6ebf6b8b8c3fe9340178cfc737c04ded8b92108c26882f77441b551124d25b` | 一致 |
| agents-light.png | <https://usemagpie.ai/img/agents-light.png> | /docs/start | 2080×1256 | `5c725927f84244624e3e6be79d966fab401eb98baba78ec05e964c0f9c7d2fa8` | 一致 |
| font-settings.png | 未发现官方精确 URL，不猜链 | 历史 `docs/screenshots/font-settings.png` 元数据；UI_REFERENCE | 未核验 | 历史记录 `9abec57166de5b6a83d833c55158dc5c9fbe88c6ca68fb2c489bb3c994013d57`，此次无文件字节核验 | 未核验 |
| import-light.png | <https://usemagpie.ai/img/import-light.png> | /docs/import | 1280×1120 | `b1aec57a7a5d89459692ee08b254124ff707e00b6d610a9919939265c0d2cbcb` | 一致 |
| intent-rules-light.png | <https://usemagpie.ai/img/intent-rules-light.png> | /docs/intent | 2060×1590 | `b901e0bb8f3210e35996ee83f040e3af3f4b54682f32681f9e72bbb0cfa90450` | 一致 |
| intent-trace-light.png | <https://usemagpie.ai/img/intent-trace-light.png> | /docs/intent | 2064×898 | `ce688dfb5f61f59c5520038116ffbe135e5b34a14e2408ea93615265838389b5` | 一致 |
| masking-dark.png | <https://usemagpie.ai/img/masking-dark.png> | /, /docs/privacy | 1912×1084 | `b167e71ccec5e6e50e4ef6c9135ffa9f1a2c0dee35d6f64e68d09482cb03fd71` | 一致 |
| masking-light.png | <https://usemagpie.ai/img/masking-light.png> | /, /docs/privacy | 1912×1084 | `e6a33c54239d6fc424d4b66a3c36780345f31f07533b511f38beacba3d9209c7` | 一致 |
| nested-routing-light.png | <https://usemagpie.ai/img/nested-routing-light.png> | /docs/start | 2080×1330 | `bda2e8ecffb2474708876a9fd33f4d77f1ec87539830058271a87f5ea7a6def2` | 一致 |
| panel-dark.png | <https://usemagpie.ai/img/panel-dark.png> | / | 880×940 | `c2b590bf9c0085befc7ec0d98153abaaea7b5c065830b8813d841e22ac1ee380` | 一致 |
| panel-light.png | <https://usemagpie.ai/img/panel-light.png> | / | 880×940 | `1cc98f14f7fd2a6b2861573c3b6f09131ad7d00018fc14308be59b0bc14dc4e0` | 一致 |
| picker-dark.png | <https://usemagpie.ai/img/picker-dark.png> | / | 880×1110 | `30e467acd6ca6f67faefc54f6572d43981eecdfca41544b6c99b086d7d90365d` | 一致 |
| picker-light.png | <https://usemagpie.ai/img/picker-light.png> | / | 880×1110 | `37bb4b6bc43351bb8e1c5b42632493ea915449da4b5feb3317e428db530f0d57` | 一致 |
| providers-dark.png | <https://usemagpie.ai/img/providers-dark.png> | / | 2080×1014 | `e8ebf956768bd0bfe5850beda7074a759d510670ecddc5079210f7d229639dee` | 一致 |
| providers-light.png | <https://usemagpie.ai/img/providers-light.png> | / | 2080×1014 | `e028740d51b9f8dfd215e131957d63877c95922b849c21c968eb98757afdfe80` | 一致 |
| routing-dark.png | <https://usemagpie.ai/img/routing-dark.png> | /docs/start | 2080×1996 | `c101ae629b12f3e436f42cabaedd04ceba6a4cbb5a5b22aa3c66ed58607c8f9e` | 一致 |
| routing-light.png | <https://usemagpie.ai/img/routing-light.png> | /docs/start | 2080×1914 | `a78070e928bcf16b30b39fc96f0acbe670f1595e0b5399e7cd3dbc4f854a7792` | 一致 |
| usage-dark.png | <https://usemagpie.ai/img/usage-dark.png> | /docs/start | 2080×842 | `91a9b236b49b88ffc8279f6ac3042a4041f1179a0f0e2f9a2c4026fade83cbda` | 一致 |
| usage-light.png | <https://usemagpie.ai/img/usage-light.png> | /docs/start | 2080×842 | `51836723d01a3637594c702be20a61da4272d7ffdd1e1b12bbf72b377a9a3d2d` | 一致 |

同一批官方页面额外声明的深色/路由补充图片：

| 文件 | 准确公开 URL | 尺寸 | SHA-256 |
| --- | --- | --- | --- |
| add-dark.png | <https://usemagpie.ai/img/add-dark.png> | [2080, 1250] | `485719694ea8a0882458c95dcd6a50205fd84bf8815f5a16e06ab6115b3105ba` |
| import-dark.png | <https://usemagpie.ai/img/import-dark.png> | [1280, 1120] | `a6b93fa8b1d50896a182eae4cb12b4aec85d2e20c5306c922607d130966ea82a` |
| intent-routing-hit-dark.png | <https://usemagpie.ai/img/intent-routing-hit-dark.png> | [2080, 1116] | `fe74dc2e88e2f7388ff03208ce56c67fdc1914776150721afb406a64c0c5e108` |
| intent-routing-hit-light.png | <https://usemagpie.ai/img/intent-routing-hit-light.png> | [2080, 1116] | `20f900ac3131875e6e678999e026e8a1c9ffa36ccfa81eac91d40d3ac319edc5` |
| intent-rules-dark.png | <https://usemagpie.ai/img/intent-rules-dark.png> | [2060, 1590] | `21c9a6d85360fdb0dd0354d92aaf73036991886353334394a614ac7b99faf896` |
| intent-trace-dark.png | <https://usemagpie.ai/img/intent-trace-dark.png> | [2064, 898] | `0feecef973dc9d758cf6c77ccdf65fd4d38bdd148a2429c8ea02b6569e46668f` |
| nested-routing-dark.png | <https://usemagpie.ai/img/nested-routing-dark.png> | [2080, 1330] | `c650221dd3b68f83add432bf181a7d6447c75b6b4fc8401fb1a15b384ca444b5` |

本批图片成功下载 26 张，共 5,018,470 字节；单图片上限 1.5 MiB、并发 4，来源名单固定为上述 HTML 已列出的 26 个地址，总新增数据设定在 100 MB 以下。保留图片作为用户需要的参照交付物，不创建归档。

Font Settings 只有配方历史 SHA-256 和已删除公开图片路径的 Git 元数据。未找到精确官方网页 URL，未获取字节，也未拿其它设置页冒充。普通 Settings 页面、空态、加载/失败、键盘/IME/读屏和真实窄屏行为仍不能由这些公开静态图证明。

## 2. 原版安装包与纯帮助观察

`/usr/bin/magpie --version` 实际输出 `magpie 0.1.1948`（退出 0）。pacman 所属包为 `magpie-git 0.1.1463.r0.g2a3d9f5a-1`；包版本不等于程序自报版本，也不等于配方提取基线 `7b63feb908da1ca20f446b6b8584441e1c934825`。

所有原版命令均经 Bubblewrap：独立 network/IPC/PID 等命名空间、清空环境、仅 /usr 只读映射；空白 HOME、临时 /tmp 和 /run；无用户数据目录、D-Bus、显示服务或真实账号可见。完整启动前缀及 stdout/退出码在 `original-help.json`，纯帮助文本在 `original-help*.txt`。

帮助保证 `-h`/`--help` 在任意位置只显示帮助。本轮只调用这类 help/version 参数；界面和业务行为尚未实际执行，help 声明不能记为业务通过。

## 3. 证据口径与固定 Rust 来源

本轮原版观察对象是本机可执行程序自报的 **0.1.1948**，以及在本轮读取的官网公开资料。旧提取基线、历史截图和包管理器版本分别登记，不能混为同一发布版本。网站没有给这些图片标注程序 build ID，图片与本机原版是否来自同一构建未知。

- **H（实际帮助输出）**：本机原版在隔离环境运行 version/help；仅证明命令声明和公开选项。没有执行帮助中展示的业务示例。
- **V（公开静态图片）**：已查看与配方指纹一致的 19 张原图；证明该图片显示的页面结构，不证明菜单可操作、异步状态、配置读写或安装版 GUI。
- **C（保留的合同）**：完整根配方 → interfaces → gateway → core，以及 quality；包含 93 个唯一 REQ 与 68 个 Agent 的最低基线。矩阵中的正常/失败结果是待验收条件，不是原版已实测结果。
- **S（固定 Rust 静态观察）**：下表固定提交的源码及 README。没有编译或运行 Rust。源码/配方已有测试和设备结果只作为既有记录，未在本轮复跑。
- **U（未核验）**：实际原版业务、真实账号、未取得的页面图和平台/设备交互；没有证据就保留 U。所有功能行的原版业务成功、失败和跨入口一致性均为 U。

所有 S 引用均属于 `generated/magpie-rs-source` 的 `dd5eed87b6d14213aeb604b50dd23d6d25a03f66`；与正在变化的工作树隔离。以下路径后的行号用于定位，没有导出或恢复旧原版实现源码。

| 来源 ID | 固定文件与定位 | 可以支持的观察 |
| --- | --- | --- |
| S1 | `crates/magpie-core/src/agents.rs:63`、`:124` | 只枚举 Claude Code、Gemini CLI、OpenCode、Codex、Zed 五种 PATH 程序；仅 Codex 进一步读取配置 |
| S2 | `crates/magpie-core/src/codex_transaction.rs:27`、`:184`、`:200`、`:214`、`:224`；`README.md:92` | Codex 七字段 prepare/commit/status/restore；所有权、漂移/恢复的有限基础；README 明确 Windows 禁写和最终检查到 rename 的竞态边界 |
| S3 | `crates/magpie-core/src/catalog.rs:17`、`:119`；`README.md:56` | 公开 TOML 模型目录，凭据字段拒绝，启动时加载有界不可变快照；不是 Provider 数据管理或自动上游发现 |
| S4 | `crates/magpie-cli/src/main.rs:49`、`:75`、`:690` | CLI 只有 agents/config/models/gateway/gui/serve/status 等分派；无参数显示帮助，不是原版无参数打开 App |
| S5 | `crates/magpie-service/src/forward.rs:1`、`:78`、`:334`、`:369`、`:387`、`:953` | 单个字面 loopback HTTP Provider；Chat/Responses 同协议 JSON/SSE；会话/账户 header 明确返回未实现；健康计数明确不是 Usage |
| S6 | `apps/desktop/src/ui.rs:36`、`:1534`、`:1736`、`:1749`、`:2398` | 页面枚举只有 Agents/Settings；其余七项是 future_pages；Settings 只有本次会话主题/语言；Picker 具备有限目录搜索 |
| S7 | `apps/desktop/src/main.rs:1`、`:25`、`:50` | GPUI 独立窗口、网关另启；无 Android 后端入口；窗口最小值不能证明真实 360px 可用性 |
| S8 | `apps/desktop/src/snapshot.rs:36`；`apps/desktop/src/actions_state.rs:60`、`:182`、`:195` | 发现快照与 Codex 真实 CLI 动作桥；搜索、目录时效、预览、显式保存、取消与恢复的基础 |
| S9 | `README.md:31`、`:104`、`:115`；`Cargo.toml:1` | 已有子集与未完成项的自述；CLI workspace 排除 GPUI；既有测试记录不等于本轮重跑 |

## 4. 完整页面与关键操作矩阵

下表覆盖产品任务；页面名是功能入口，不强制每项各占一个顶级标签。H 文件名是 evidence 目录内 `original-help-<命令>.txt`；V 名称对应第 1 节图片。H/V 之外的控制、恢复和一致性语义来自 C，全部保留待完成。列中的结果与失败要求均为目标，原版本轮未执行这些操作。

| 页面 / 任务 | 入口、关键操作与预期结果 | 失败 / 恢复 / 数据保留要求 | 原版可用参照 | 固定 Rust 缺口 | REQ |
| --- | --- | --- | --- | --- | --- |
| 主窗与全局导航 | 无参数 App、打开主窗、刷新、设置、退出；所有功能可达 | 服务离线不能假保存；宽窄窗仍可返回；退出/托盘生命周期明确 | H: 顶层；V: agents 两主题 | S4 无参数只帮助，S6 仅 Agents/Settings；完整导航待建 | 010,080,081,101 |
| Agents 发现列表 | 按实际安装/可读状态显示身份、当前模型、支持字段；重扫 | 损坏/未知/不可读与未安装分开，不把静态目录标成在线 | H: agents/各 Agent；V: agents | S1 仅五种发现、Codex 配置；其余至少63项发现及完整状态待建 | 001,011,019,085 |
| Agents 连接与原生默认 | Connect、选择 Default、Disconnect，提示生效/重启方式 | 预览、原子写、归属、漂移；恢复只撤销自有字段，外部修改保留 | H: claude/codex 等；V: agents 开关 | S2/S8 只有 Codex 子集；全 Agent 可恢复配置待建 | 002,003,012,013,014 |
| Agent 专属设置 | effort/tier/small/subagent/auto 等按真实支持项呈现 | 字段升级不兼容要报错，不把 Codex 的字段套给全部 Agent | H: 13 种已读 Agent 帮助；V: panel | 只有 Codex 显式 model/provider/effort 读取；专属字段编辑与更多 Agent 待建 | 015,085 |
| 行内模型 Picker | 搜索、当前项、原生 Default、Provider 分组与能力筛选，保存后生效 | 目录失败保留有效值；相同裸模型要区分 Provider；Esc/焦点/输入保留 | H: models/model；V: picker 两主题 | S8 有配置目录查询/搜索/稳定 ID 选择；缺完整能力、分组和多 Agent 原生目录 | 004,016,022,034,103 |
| 每 Agent 可见性 | 按模型/Provider/家族/组隐藏；only-picked/show-new | 影响列表；显式 ID 仍可请求；读取失败不清空准入集 | H: visible；C: 004/017 | S3 静态公共目录，没有完整每 Agent 可见性写入和一致授权 | 004,017 |
| Profiles | 保存/覆盖/应用/删除命名快照；面板/CLI 显示同一当前项 | 覆盖/删除确认；部分文件失败不能报全成功；卸载 Agent 设置保留 | H: 顶层 save/use/profiles/rm；V: agents/panel 底部 | 无 Profile 状态、批量应用或恢复 UI | 005,018 |
| 快捷 Panel / 托盘 | 刷新、按行选模/字段、额度状态、Profile、打开主窗、退出 | 360/440px 保留主任务；关面板不关 Gateway；刷新不丢焦点 | H: 顶层 tray/panel/autostart；V: panel 两主题 | S7 只有独立主窗口；没有实际托盘/菜单栏/Panel 生命周期 | 010,080,101 |
| Providers 列表 | 看身份、host、模型数、使用它的 Agent、遮蔽 key、账户/额度状态；详情/启停/移除 | 未知不是在线；删除不删除 Usage 身份；无账号显示添加空态 | H: providers/provider；V: providers 两主题 | S3 的 public catalog 不是 Provider 管理；S6 页面未实现 | 020,021,029 |
| Add Provider | 搜索 vendor/relay/local/订阅预设、region、自定义 endpoints/header/模型 | 字段错误就近显示且保留输入；本地服务不强制 key；不试发真实 key | H: provider/presets；V: add-light | 无完整新增/编辑/启停/安全持久化表单；退役订阅必须走插件 | 020,025,090 |
| Provider 编辑与多 key | 添加/排序/暂停/轮换 key；价格、UA、header、过滤/刷新与 fallback | 单字段修改保留其他项；测试失败保留配置；不自动删除失效 key | H: provider；C: 021 | S5 单环境变量凭据引用和单 Provider；多 key、测试及编辑待建 | 021,026,027 |
| Import Provider | 预览来源、名称、遮蔽 key、URL/模型、冲突，再确认添加 | 来源不可读/仅 env 引用跳过解释；非法 URI/注入拒绝；不持续镜像登录 | H: 顶层 import；V: import-light；官方 /docs/import | 无导入 URI/源应用预览和持久化流程 | 023,084 |
| Subscriptions / Accounts | 发现开关、添加登录、切换/别名/暂停/移除、project/套餐信息 | 发现禁用仍保留 API Provider；过期/权限错需重登；不采集真实账户作测试 | H: accounts；C: 024/028 | 无 OAuth、官方 CLI 订阅、账户发现和续期体系 | 024,027,028,029 |
| 订阅迁移 / Move | 显示 retired owner、目标插件、影响账户，Move/Adopt 后唯一服务者 | 失败保留登录材料；关闭/移除插件停止服务并可恢复；不能复活退休 builtin | H: plugin move/migrate；C: 025/060/063 | 社区 host、迁移状态及相关 UI 均待建；截图旧订阅卡不等于当前实现 | 025,060,063 |
| Models 管理 | 查询真实上游目录/公共元数据、选择/glob/regex、名称/effort/价格覆盖 | 刷新失败保留 stale 列表；退役请求错误和确认刷新删除分开 | H: models/model/visible；C: 022 | S3 有界启动快照；无上游自动发现/刷新缓存、全能力和覆盖编辑 | 016,022,034,057 |
| Gateway 设置 / 生命周期 | 监听地址、启动停止、端口错误、标准客户端 base、GUI 默认服务 | 端口/TLS 错误不能显示健康；服务故障保留 Agent 配置；CLI 不拉显示服务 | H: serve/daemon；C: 030/036 | S5 只有 loopback 服务；S7 GUI 要另启网关，完整生命周期缺失 | 030,036,080 |
| Gateway 协议 | Chat/Responses/Messages/Gemini、音频、目录/Ollama 目录；流/工具/reasoning | 取消释放上游；只转换一次；不支持载荷明确拒绝；最终请求只算一次 | H: serve（启动声明）；C: specs03 | S5 仅 Chat/Responses 同协议 HTTP/SSE；外部 HTTPS/转译/其余端点待建 | 031,032,033,034 |
| LAN / Gateway keys | 显式分享、命名 key/轮换/模型及账户范围、预算/限流/allowlist | 无权限在上游前拒绝；撤销立即生效；Host/Origin/CSRF/代理不能越权 | H: gateway-key/web/serve；C:035/091 | 字面 loopback 限制是子集保护；没有 LAN、受限 key、Web 认证体系 | 035,039,090,091 |
| Remote Magpie | 远端 base/key/模型范围/刷新间隔，保持协议与 session | 不复制其他凭据/录制；远端不兼容保留 stale 目录与可读错误 | C: 038；H: web/serve 只覆盖入口 | S5 拒绝非 loopback HTTP；Remote Provider 未实现 | 037,038 |
| Routing groups | 新建/复制/改名/排序/启停/隐藏自动组/恢复，model 和 nested group 区分 | 空组/循环组拒绝；listed 与直接显式请求语义保留 | H: group/groups；V: routing/nested-routing | 无组状态和 CRUD、自动组或路由图 | 040,046 |
| Routing 策略与账户 | smart/order/rotate/usage/pace/weight/fast；stays，成员/账号排除与预算 | 无 quota 不当无限；401/429/拒绝/暂时网络分开；有界 fallback/取消 | H: group；V: routing；C:041/042/044/045 | S5 没有多成员、账户顺序、affinity、重试/fallback 和完整账务 | 041,042,044,045 |
| Intent Rules / Decision | 规则排序、token/image/agent/目录/时间/effort/intent，classifier 可选 | 未知 label/超时按规则 fallback；不重复分类、不执行输出为 URL；取消可恢复 | H: group-rule；V: intent-rules/trace；官方 /docs/intent | 规则编辑、分类调用、System One 和 decision 目录均待建 | 043,047 |
| Routing live / trace | 展示请求/组/命中原因、成员尝试、失败/回答者；历史/session 筛选与隐藏邮箱 | 日志读失败显示不完整；最终 retry 成功不能算失败；unknown 标题不虚构 | V: routing/intent-trace；C:037/046 | S5 拒绝 session/account header，只有运输计数；无 route API/历史视图 | 033,037,046 |
| Usage 总览 | 时间范围、上游余额/额度、token/cache/reasoning/calls、费用/图表 | 未知值保留 unknown；估算费用不是发票；来源时间明确 | H: usage/quota；V: usage 两主题 | 无统一 Usage/账务；/health 明示 usage_accounting=not_implemented | 050,051,053,057 |
| Quota / Budget | 窗口/重置/刷新/别名、账户/key cap 与费用预算、credits 选择 | 离线保留上次读数与时间；重复 seat 不重复计；超限明确重置点 | H: quota/gateway-key；C:026/027/052 | 无 quota 存储、seat 合并、预算执行与卡片 | 026,027,051,052 |
| Usage 明细 / 导出 | 按 Provider/account/Agent/purpose/source/retry/time 查询，CSV/JSON | 聚合/明细/CLI/API 同源；request/attempt 去重；导出不含 secrets/正文 | H: usage；C:058/059 | 未实现查询、明细、分页/导出及受限 key 过滤 | 053,058,059 |
| Sessions | 合并原生会话和 Gateway 元数据，按 Agent/模型/目录/时间搜索与分组 | 无原生 ID 不猜；失效文件不是空会话；删除关联正文受策略约束 | H: sessions；C:054/055 | 无 Sessions 页面、原生会话合并与持久化；S5 session header 未实现 | 037,054,055 |
| Recording / 隐私数据 | 默认不录正文，明确开启、位置/期限/容量/删除，元数据与正文分开 | 磁盘上限停止/清最旧但继续转发；同步不能自动开启本机录制 | C:055/079/092；官方 Privacy 只作公开说明 | S5 不记录正文是有限基础；完整录制设置/清理/档案流程待建 | 055,079,092 |
| Masking | 启用/规则/范围/版本/保护次数、按 request/value 类别查阅 | 不存秘密原值；不支持的多模态不假称覆盖；规则错误 fail-closed | V: masking 两主题；官方 /docs/privacy | 无 masking middleware、规则引擎或页面 | 056,068,092 |
| Library 总览 / Instructions | 具名指令 set、链接已有文件、分配 Agent/默认、同步 | marker 外文本保留，移除只撤自有条目，读失败不覆盖 | H: library；C:070/071 | Library 仅在 future_pages；状态及事务应用待建 | 070,071 |
| Library Skills / Hooks / Subagents | 创建/导入/安装/更新/分组/每 Agent How；适用的 hooks/commands/subagents | 保留用户自有技能；已卸载 Agent 的旧条目仍可管理；冲突不误覆盖 | H: library；C:071 | 完整 Library 数据、技能来源/版本与目标格式均待建 | 071,073 |
| Library MCP / Projects / RTK | stdio/HTTP/SSE 定义、握手/tools-list/超时；按项目分配；RTK 配置 | 401 不报健康；路径冲突拒绝；不删除相邻用户文件；缺工具不写无效命令 | H: library-mcp/library；C:072/073/074 | MCP 测试/授权、项目目标和 RTK 安装/配置未实现 | 065,072,073,074 |
| Plugins 社区 / Pi / Provider | 搜索、包描述/版本/兼容、安装/更新/开关/登录/移除 | 更新失败保留有效版；host 崩溃隔离；凭据只给所属 Provider；移除留恢复材料 | H: plugin；C:061/062/064/066 | 无 Node host、npm/Pi 插件和社区生命周期 UI | 060,061,062,064,066,069 |
| Plugins 内置 Feature / Middleware | dependency reconcile、off/pending/active/failed、required、extension 注册/撤销 | Apply 失败回滚；逆序 dispose；不重复转发；类型与社区插件区分 | H: plugin builtin/preset/dump；C:067/068/069 | 无 feature/plugin runtime 或扩展链 | 067,068,069 |
| Tools / Search / Images / Video | 列工具 schema、MCP 调用、search fallback/多 key、媒体指定输出 | 参数先验、超时取消、失败清理；关闭工具停止隐式请求；Usage 归属 | H: tools/tool/search；C:048/049/095 | 无工具服务/search connector/媒体与相关设置 | 048,049,065,095 |
| Settings General / Appearance / Font / Locale | 分组/搜索、主题/字体/五语、启动/退出；保存/重启提示 | 切换保留输入焦点与本地时区数字；未保存不假成功；font 精确图未知 | C:081/100-103；Font 历史指纹；H: 顶层 | S6 仅会话级主题/语言；无完整保存、字体自定义、搜索和设置分组 | 081,100,101,102,103 |
| Settings Privacy / Proxy / Network | 发现开关、secret 遮蔽、安全存储、proxy/private-host/分享/Origin 策略 | 存储失败停止敏感操作；跳转/DNS/URL 严格边界；本机专属值不传播 | H: accounts discovery/web；C:090-095 | 有限 loopback/脱敏基础，完整密钥库/代理/SSRF 设置和认证待建 | 090,091,092,093,095 |
| Settings Backup / Restore / Sync | 选区段、口令备份、预览/恢复/Undo；WebDAV/S3/GitHub 多机同步 | 坏口令/索引/版本/ETag 冲突全停；不清空缺失区段；本机开关保留 | H: backup/restore/webdav/s3/github；C:075-079 | 无备份格式、加密、Undo、远端同步与合并流程 | 075,076,077,078,079 |
| Updates / Diagnostics | 版本/频道/auto/notify、失败理由/日志、脱敏诊断 | 更新失败保留可启动版本；离线未知；不回显 auth/prompt | H: update；顶层 heap/cpu/goroutines（未执行）；C:086 | 打包准备不等于更新系统；无完整更新/运行诊断 UI | 086,090 |
| Web / TUI / Headless | 专属 Web 登录、TLS/反代、无显示同状态编辑；窄终端键盘/help | 未授权资源/API/WebSocket 拒绝；服务断开有明确错误 | H: web/tui/serve/daemon；C:080/082/083 | CLI 独立、loopback serve 为子集；没有完整 Web/TUI/认证/同状态入口 | 010,080,082,083,084 |
| 平台 / WSL / 安装卸载 | Arch/Nix/macOS/Windows/Android CLI 与桌面分别交付；WSL 每 distro 归属 | 不唤醒 asleep distro；不混账号/路径；Android Web 不是原生 GPUI | H: 顶层运行说明；C:094；既有设备记录单独保留 | S7 Android GUI 不支持；其它系统原生验收/完整包及 WSL 待完成 | 080,086,094 |

### 所有页面都保留的状态和一致性验收

加载、空态、保存中、失败、成功、离线/未知、stale、无权限、取消/结果不确定、配置漂移必须有可读文本和稳定位置；表单失败保留输入，后台刷新不夺焦点。确认修改后，CLI、GPUI、Panel、Web、TUI 刷新同一核心保存状态，Gateway 直接使用该状态；每行任务均需检查这一点，不能用独立 UI 缓存显示成功。恢复应保留无关字段/注释、用户后来写入和所有本机专属开关。纯截图与纯 help 均不能证明这些路径已通过。

## 5. 视觉对照中的实际观察与差异

已逐张查看上述 19 张指纹一致的公开图。Agents 两主题显示统一行列表：身份/次级状态左对齐，模型目录入口、行内模型选择与开关对齐在右；Profile 在底部。Panel 将模型和 Agent 特有字段压入同一行。Picker 有上方搜索、原生 Default、当前模型勾选与 Provider 分组，还出现左侧窄图标筛选栏；这些都是信息结构参照，键盘行为仍待实际验收。

Providers 是单一横向行列表，身份、host/模型数量、使用方和遮蔽 key 状态有清楚列关系；Add 在列表下方。Routing 用 Agent → Magpie → 成员的连线说明尝试关系，下面保留文字因果过程和组清单。Nested Routing 的子组展开仍与下面解释区分开。Usage 把时间筛选、估值总额、上游余额、小型 token/cache/reasoning/calls 摘要和日期柱状图依次排列。Masking 分开保护次数/关系图、筛选和请求条目，图下注明没有保留原值。以上不是图片中的模型、账号、余额或计数的当前真实性证明。

需要明确保留的差异：

1. **截图不是同一时刻的完整导航。** Agents 图有 Sessions/Plugins，部分 Providers/Routing/Usage 图只有较少标签。不能据旧图删除完整功能；宽屏侧栏与窄屏折叠仍按 REQ-101。
2. **Add 旧图与退休合同不同。** 图片仍出现 Cursor/Grok/Devin 等订阅入口；新的退休插件所有权语义按完整合同保留。图只用于分组、密度与表单层次。
3. **导入图的存储提示不是安全审计。** 图片可见本机 JSON 存储提示，不证明系统密钥库、磁盘加密、权限或安全恢复；REQ-090 继续保留，无法由图判通过。
4. **分辨率不能当布局断点。** Panel/Picker 文件为 880px 宽，官方 HTML 显示尺寸声明为 440px；真实 360px/440px 可操作性、缩放和原生 GPUI 仍待验收。
5. **准确字体/token/动画未知。** 可观察到无衬线文字、中性浅/深表面、小面积选择强调和细分隔；不反推精确字体家族、字号、颜色 token 或时间。Font Settings 未得到本轮文件字节。
6. **交互状态缺图。** 当前公开图不能证明空态/坏字段/保存中/取消/漂移、长德文/中文、IME、读屏、焦点或 reduce motion。需真实 GPUI 场景渲染和实际窗口对照；此前两轮 Pro 视觉仍未通过。

## 6. 全部 93 REQ 的任务、结果与缺口

这是最低基线逐项映射，完整正文仍在读取时哈希固定的 recipe-inputs 中。正常和失败列取自对应 REQ 的公开验收条款；**不是原版本轮实际执行结果**。H/V 是可定位的声明/图证据，C 是合同，实际业务统一 U。没有任何整条 REQ 在本轮被标为功能通过。

| REQ / 功能 | 入口 / 原版证据 | 正常任务与预期结果（待验收） | 失败或边界结果（待验收） | 固定 Rust 状态 / 缺口 | 合同定位 |
| --- | --- | --- | --- | --- | --- |
| REQ-001 只显示可用 Agent | Agents/Picker/Panel；H: 顶层、各 Agent、visible/models；V；C | 安装并配置 Codex 后，列表出现 Codex 和其当前模型；移除配置或取消连接后按该 Agent 的断开语义隐藏或标记未连接。 | 配置文件不可读或格式损坏时，原配置不被覆盖，显示可操作错误。 | 五种 PATH 发现与 Codex 配置读取；其余支持目录及原生窗口验收未完成（S1/S8）。 | recipes/magpie/GLOBAL.md:59 |
| REQ-002 保持用户已有数据 | Agents/Picker/Panel；H: 顶层、各 Agent、visible/models；V；C | 只更换 Codex 的 `model`，其他 TOML 内容保持语义和值不变；重新格式化不是本软件要求。 | 保存中断后，磁盘上仍是旧文件或完整新文件，不能留下截断配置。 | Codex 七字段保护/恢复基础；其他 Agent、全故障和真实数据保护未验收（S2）。 | recipes/magpie/GLOBAL.md:67 |
| REQ-003 显式连接与断开 | Agents/Picker/Panel；H: 顶层、各 Agent、visible/models；V；C | 连接一个支持 OpenAI 兼容 API 的 Agent 后，Agent 能看到 magpie 模型；断开后原来的 Provider 和模型恢复。 | 目标应用正在写配置、格式不支持或字段冲突时，不覆盖文件，并解释修复步骤。 | Codex 显式 prepare/commit/restore 与 CLI 桥；其他连接和原生窗口未完成（S2/S8）。 | recipes/magpie/GLOBAL.md:76 |
| REQ-004 统一模型选择和每 Agent 可见列表 | Agents/Picker/Panel；H: 顶层、各 Agent、visible/models；V；C | 某模型从 Codex 的列表中隐藏后，其它 Agent 仍可使用。 | Provider 上游移除了模型时，明确刷新后清理过期的显式选择；单次请求返回模型退役错误时按 `02` 中的保留规则处理。 | 有公共目录快照；缺多 Agent 选模和每 Agent 可见性（S3/S9）。 | recipes/magpie/GLOBAL.md:84 |
| REQ-005 用户可保存和切换 Profile | Agents/Picker/Panel；H: 顶层、各 Agent、visible/models；V；C | 保存 Work，再切到 Focus，再切回 Work，Agent 显示原来的模型和字段。 | 依完整 REQ 正文的错误/边界验收 | 无命名 Profile 保存/切换/覆盖确认（S4/S9）。 | recipes/magpie/GLOBAL.md:92 |
| REQ-010 多个入口反映同一状态 | Agents/Picker/Panel；H: 顶层、各 Agent、visible/models；V；C | CLI 设置 Codex 模型后，Agent 页面和面板显示新值。 | GUI 后端暂时不可达时，不显示成功保存；刷新后以实际保存状态为准。 | Codex CLI/Snapshot 同源基础；无完整 Panel/Web/TUI 一致状态（S8/S9）。 | recipes/magpie-core/specs/01-product-and-agents.md:7 |
| REQ-011 发现和列出 Agent | Agents/Picker/Panel；H: 顶层、各 Agent、visible/models；V；C | 空白新机器不显示未安装 Agent；安装一个受支持 Agent 并建立配置后，重新扫描出现对应行。 | 路径不存在、配置损坏或权限不足不等价于 Agent 未安装；显示区别和可修复路径。 | 仅五种安装探测和 Codex 配置；完整 68 项发现/状态待建（S1）。 | recipes/magpie-core/specs/01-product-and-agents.md:16 |
| REQ-012 兼容 Agent 的公开配置格式 | Agents/Picker/Panel；H: 顶层、各 Agent、visible/models；V；C | Codex 的 TOML 配置、Claude Code 的 JSON 配置和 OpenCode 的 JSONC 配置都可接入；每个 Agent 的模型在其原生 `/model` 或模型选择器中可用。 | 目标格式含未知字段时保留它们；未知字段不能因 round-trip 被删除。 | 仅 Codex TOML 读取/事务，其他公开格式与版本矩阵待建（S1/S2）。 | recipes/magpie-core/specs/01-product-and-agents.md:25 |
| REQ-013 只做精确、可恢复的字段编辑 | Agents/Picker/Panel；H: 顶层、各 Agent、visible/models；V；C | 在含注释、重复 key、未知扩展字段的配置中仅改模型，其他内容保留。 | 解析错误、不可读、只读、路径不安全或文件被 Agent 锁定时，显示失败并保留原内容。 | Codex 有所有权/漂移/原字节恢复；全部 Agent/Windows/断电仍待完成（S2/S9）。 | recipes/magpie-core/specs/01-product-and-agents.md:66 |
| REQ-014 接入、漂移和断开 | Agents/Picker/Panel；H: 顶层、各 Agent、visible/models；V；C | 连接 Codex、用户手工改一个无关 Header、再断开，Header 不丢失，原模型恢复。 | Agent 进程正在覆盖文件时，连接操作等待有界时间或拒绝，不能以部分写入结束。 | Codex 接入/状态/恢复基础；多 Profile 和其余 Agent 未完成（S2/S8）。 | recipes/magpie-core/specs/01-product-and-agents.md:75 |
| REQ-015 Agent 专属字段和 Profile 保留 | Agents/Picker/Panel；H: 顶层、各 Agent、visible/models；V；C | Codex 的 effort 字段只出现其有效等级；关闭 Provider 后不留已失效选择。 | Agent 升级后字段不再受支持时显示不兼容状态并保留备份，不写入猜测值。 | Codex model/provider/effort 读取不等于专属字段编辑；Profile 保留待建（S1/S8）。 | recipes/magpie-core/specs/01-product-and-agents.md:84 |
| REQ-016 跨 Agent 模型目录 | Agents/Picker/Panel；H: 顶层、各 Agent、visible/models；V；C | 在同一个模型列表中选择不同 Provider 的两个模型，两个 Agent 原生配置分别更新。 | 模型名相同但来自不同 Provider 时仍可区分；明确的 Agent 自定义模型名保持原样。 | 配置目录与 Picker 搜索基础；缺跨 Provider 能力/价格与多 Agent 原生目录（S3/S8）。 | recipes/magpie-core/specs/01-product-and-agents.md:93 |
| REQ-017 每 Agent 模型可见性 | Agents/Picker/Panel；H: 顶层、各 Agent、visible/models；V；C | Codex 隐藏 `vendor/model-a`，其列表不显示它，另一个 Agent 仍有它；显式请求该模型仍成功。 | 无法读取可见性设置时，保留上次有效集合并提示错误，不把所有模型误设为可见或全部隐藏。 | 无每 Agent 隐藏/only-picked/show-new 持久化与列表投影（S3/S4）。 | recipes/magpie-core/specs/01-product-and-agents.md:101 |
| REQ-018 保存、应用和删除 Profile | Agents/Picker/Panel；H: 顶层、各 Agent、visible/models；V；C | 保存 `Work`，切换两个 Agent 的模型后应用，两个模型均恢复。 | Profile 中某 Agent 已卸载时，其余 Agent 仍可应用；缺失 Agent 设置保留在 Profile 里，重装后可恢复。 | 无 Profile 快照、批量预览/应用及部分失败恢复（S4/S9）。 | recipes/magpie-core/specs/01-product-and-agents.md:109 |
| REQ-019 变化重扫和状态反馈 | Agents/Picker/Panel；H: 顶层、各 Agent、visible/models；V；C | 用户在 Codex 自己的编辑器改模型后刷新，magpie 显示该模型。 | 重扫期间文件短暂不完整时保留最后可解析值，再重试；不得显示伪造的默认模型。 | 有刷新/最后可读值/事务重读基础；完整文件变化监控和窗口反馈未验收（S8）。 | recipes/magpie-core/specs/01-product-and-agents.md:117 |
| REQ-020 Provider 目录和新增 | Providers/Add/Import；H: provider/accounts/quota；V；C | 加 DeepSeek 只需输入 key；加本地 Ollama 可不填 key；加自定义兼容服务需同时设地址和模型。 | 网址 scheme、密钥字段或必需的 base URL 不合法时拒绝保存并指出字段；不向任意地址发送真实 key 进行校验。 | catalog/forward 文件仅为开发输入；无完整预设、自定义新增、启停/移除（S3/S5/S6）。 | recipes/magpie-core/specs/02-providers-and-accounts.md:3 |
| REQ-021 多个上游密钥和 Provider 编辑 | Providers/Add/Import；H: provider/accounts/quota；V；C | 轮换第一把 key 后新请求使用新 key，历史仍归属于原 key。 | 上游拒绝 key 时保留配置并标识失败，不能把 Provider 删除或把空列表当成功。 | 单环境变量凭据引用；无多 key 编辑/轮换/暂停、Provider 测试与独立刷新（S5/S9）。 | recipes/magpie-core/specs/02-providers-and-accounts.md:13 |
| REQ-022 模型列表和目录元数据 | Providers/Add/Import；H: provider/accounts/quota；V；C | 刷新后出现新增模型并按配置自动包含；手工选择的模型可从列表移除或加入。 | 目录服务不可达时保留上次成功列表并标记数据陈旧，不清空模型。 | 只加载公共静态目录；上游发现、自动包含、stale 刷新和元数据覆盖待建（S3）。 | recipes/magpie-core/specs/02-providers-and-accounts.md:22 |
| REQ-023 用户可导入现有 Provider | Providers/Add/Import；H: provider/accounts/quota；V；C | 导入 Codex 的 OpenAI-compatible Provider，确认后出现在 Provider 列表。 | 来源文件不可读、只含 env key 引用或内容不完整时跳过该项并说明原因，不清空原设置。 | 无现有 Provider/URI 导入预览、冲突与确认流程（S4/S9）。 | recipes/magpie-core/specs/02-providers-and-accounts.md:32 |
| REQ-024 发现已登录订阅 | Providers/Add/Import；H: provider/accounts/quota；V；C | 在 Claude Code 签入的账户出现在 Provider 列表，其原生模型可由另一 Agent 经 Gateway 使用。 | token 过期、账号退出、企业域/Cloud Project 不匹配时显示登录或权限错误，不能无限刷新或把账户判成有额度。 | 无订阅发现开关、OAuth/官方客户端账户和刷新体系（S9）。 | recipes/magpie-core/specs/02-providers-and-accounts.md:41 |
| REQ-025 已弃用订阅的社区插件交接 | Providers/Add/Import；H: provider/accounts/quota；V；C | 安装 `opencode-cursor-auth` 后，Cursor 订阅显示为插件提供，Gateway 把请求交给插件。 | 迁移失败时不丢原账户材料，标记失败并在间隔后重试；旧内置代码已退休的账户明确标记暂不可服务。 | 无退休订阅 owner/Move/社区插件迁移；旧图不能作为恢复 builtin 的依据（S9）。 | recipes/magpie-core/specs/02-providers-and-accounts.md:51 |
| REQ-026 套餐、额度和余额 | Providers/Add/Import；H: provider/accounts/quota；V；C | 账户额度降低后下一请求避开账户，并在重置后恢复。 | 额度读取失败时将状态标为未知并保留上次已知值及时间，不把“读取失败”当成无限额度。 | 无上游套餐、余额/窗口、刷新或预算执行（S5/S9）。 | recipes/magpie-core/specs/02-providers-and-accounts.md:76 |
| REQ-027 账户顺序和约束 | Providers/Add/Import；H: provider/accounts/quota；V；C | 将一个账户暂停，只对该账户发出的新请求被绕过，另一个未暂停账户继续服务。 | 身份读不全或两个座席无法确认相同，展示为未知/独立，不基于邮箱相似度擅自合并。 | 无多账号顺序/座席去重/暂停/并发队列规则（S5/S9）。 | recipes/magpie-core/specs/02-providers-and-accounts.md:86 |
| REQ-028 计划与本机订阅使用限制 | Providers/Add/Import；H: provider/accounts/quota；V；C | 导入多账户文件后保留每个独立团队席位；重复账号不重复添加。 | 只含过期 access token 而无续期信息的登录拒绝导入并说明原因。 | 无账号导入/Cloud Project/套餐条件与必要确认（S9）。 | recipes/magpie-core/specs/02-providers-and-accounts.md:95 |
| REQ-029 账号和模型状态对外说明 | Providers/Add/Import；H: provider/accounts/quota；V；C | 账户登录失效的错误旁给重登入口；一个模型被拒但账户其他模型可用时不把整个账户停用。 | 状态源无法读取时显示未知，禁止静默清空账户或额度信息。 | 仅配置/transport 健康基础；无完整账户、模型、迁移错误分类（S5/S9）。 | recipes/magpie-core/specs/02-providers-and-accounts.md:104 |
| REQ-030 本地 Gateway 生命周期 | Gateway；H: serve/web/gateway-key（启动/权限声明）；C（协议实际行为 U） | 启动桌面 App 后，本机 Agent 可请求 `http://127.0.0.1:3425/v1/models`。 | 端口被占用、地址非法或证书缺失时启动失败并给出地址/端口原因，不假报可用。 | 有 loopback CLI 服务；GUI 仍需另启网关，完整退出/托盘/安装恢复待建（S5/S7）。 | recipes/magpie-gateway/specs/03-gateway-and-protocols.md:3 |
| REQ-031 请求入口与模型标识 | Gateway；H: serve/web/gateway-key（启动/权限声明）；C（协议实际行为 U） | Agent 以 `anthropic/claude-...` 名称请求，Gateway 按 Provider 路由并以 Anthropic Messages 结构返回。 | 裸模型 ID 在多 Provider 可用时按组/配置的确定规则路由；不能因两个 Provider 的模型名相同而随机选择。 | 仅 Chat/Responses JSON/SSE；Messages/Gemini/音频/专用目录与外部 HTTPS 待建（S5）。 | recipes/magpie-gateway/specs/03-gateway-and-protocols.md:12 |
| REQ-032 协议转换和流式行为 | Gateway；H: serve/web/gateway-key（启动/权限声明）；C（协议实际行为 U） | 将 OpenAI Chat 请求发到仅支持 Anthropic Messages 的自定义 Provider，工具调用流按 Anthropic 协议返回；反向也成立。 | 不支持的字段/多模态类型要么按已定义映射保留语义，要么返回明确的兼容性错误，不能静默丢掉调用内容。 | 同协议有界流透传；无跨协议一次翻译及完整多模态/工具映射（S5）。 | recipes/magpie-gateway/specs/03-gateway-and-protocols.md:32 |
| REQ-033 错误、状态码和请求归属 | Gateway；H: serve/web/gateway-key（启动/权限声明）；C（协议实际行为 U） | 第一成员 429 后第二成员成功，Agent 只看到成功，Route 记录两个尝试。 | 所有成员失败时，最终错误包含可操作类别并保留客户端协议，而不是泄露密钥、完整 prompt 或响应正文。 | 有固定脱敏错误和实际来源响应头；无完整 attempt/request/Usage 最终状态归属（S5）。 | recipes/magpie-gateway/specs/03-gateway-and-protocols.md:41 |
| REQ-034 模型目录契约 | Gateway；H: serve/web/gateway-key（启动/权限声明）；C（协议实际行为 U） | `GET /v1/models` 显示三个已配置 Provider 的已暴露模型；Codex 专用目录提供 Codex 可读字段。 | 模型目录服务器离线，响应仍有缓存模型且不会将 stale 列表写回 Agent 为“空”。 | 有配置模型 /v1/models 和 inspect；无上游发现、Codex/Gemini 目录与 stale 缓存刷新（S3/S5）。 | recipes/magpie-gateway/specs/03-gateway-and-protocols.md:50 |
| REQ-035 本机访问和远程鉴权 | Gateway；H: serve/web/gateway-key（启动/权限声明）；C（协议实际行为 U） | loopback 无 key 成功；远程无 key 为 401/403；有效 key 但受限模型被拒。 | Host/DNS rebinding、`Origin: null`、伪造转发头或未授权来源不得获得本机免认证访问。 | 仅 loopback 来源/Host/Origin 限制；没有 LAN key、Web key 与代理信任授权（S5）。 | recipes/magpie-gateway/specs/03-gateway-and-protocols.md:59 |
| REQ-036 Agent 连接到网关 | Gateway；H: serve/web/gateway-key（启动/权限声明）；C（协议实际行为 U） | 用仅 base URL 配置的客户端在本机请求模型，不需供应商密钥。 | 把本机明文 key 复制到远程配置不能绕过 Gateway key 权限限制。 | Codex 可恢复写入 loopback 接入；其他 Agent/协议/远端 key 流程待建（S2/S5）。 | recipes/magpie-gateway/specs/03-gateway-and-protocols.md:70 |
| REQ-037 会话、账户固定和观测 API | Gateway；H: serve/web/gateway-key（启动/权限声明）；C（协议实际行为 U） | 指定一个有效账户只由它服务；请求另一个有效账户时响应来源是所指定的那个。 | 请求无 session header 不虚构 ID，也不记录成一个可列出的假会话。 | X-Magpie-Account/Session 在当前 forward 路径明确返回未实现；无 route API（S5）。 | recipes/magpie-gateway/specs/03-gateway-and-protocols.md:86 |
| REQ-038 远程 magpie | Gateway；H: serve/web/gateway-key（启动/权限声明）；C（协议实际行为 U） | 本机 Agent 请求 remote/ 模型，远端记录相同 model 和 session ID。 | 远端版本、模型或 API 不兼容时明确标记，保留上一次缓存目录而不把它宣称为实时可用。 | 仅接受字面 loopback HTTP base；Remote Magpie/TLS/session 传递未实现（S5）。 | recipes/magpie-gateway/specs/03-gateway-and-protocols.md:96 |
| REQ-039 Gateway 的附加查询接口 | Gateway；H: serve/web/gateway-key（启动/权限声明）；C（协议实际行为 U） | 客户端 key 读取自身 usage；额度受限 key 看不到其它 key 的用量。 | 无效参数返回 4xx 并保持状态不变；该页面的浏览器 key 不能冒充 Gateway key。 | forward GET 除 health/models 外为未实现；无受限 usage/quota/concurrency API（S5）。 | recipes/magpie-gateway/specs/03-gateway-and-protocols.md:105 |
| REQ-040 建立模型路由组 | Routing/Intent/Tools；H: group/group-rule/search/tools；V:路由；C | Agent 选择 `group/opus-anywhere` 后，组内模型按配置作为可替代成员运行。 | 组不存在、空组或环形嵌套引用被清楚拒绝，不能递归卡死。 | 无路由组 CRUD/嵌套/自动组/decision 组和 cycle 检查（S4/S9）。 | recipes/magpie-core/specs/04-routing-and-failover.md:3 |
| REQ-041 路由策略 | Routing/Intent/Tools；H: group/group-rule/search/tools；V:路由；C | `order` 组的 A 返回 429、B 返回 200 时调用顺序为 A、B，Usage 的最终请求是成功重试。 | weight=3 对 weight=1 的两个成员，在足够请求样本上约按 3:1 平滑分布；不能一次长批处理导致 4 连续偏置。 | 无七种组策略、成员权重及 stays 会话策略（S5/S9）。 | recipes/magpie-core/specs/04-routing-and-failover.md:12 |
| REQ-042 选择可服务成员和 account 顺序 | Routing/Intent/Tools；H: group/group-rule/search/tools；V:路由；C | 同一订阅两个账户中，一个模型计划不含该模型，另一个包含；请求必须优先选后者。 | 账户刚在原生 App 中切换/注销、额度状态陈旧或权限不明时，显示状态未知或过期并跳过必要请求，不编造余额。 | 无多账号额度/计划/cap/sink 排除与 fallback 顺序（S5/S9）。 | recipes/magpie-core/specs/04-routing-and-failover.md:31 |
| REQ-043 按请求上下文匹配路由规则 | Routing/Intent/Tools；H: group/group-rule/search/tools；V:路由；C | 对“fix the login bug”配置 bug 标签的 classifier 返回 `bug`，组路由选择 bug 成员，并能查看规则命中解释。 | classifier 返回未知 label、超时或被拒时不循环调用；保留原请求在指定 fallback 下运行或返回明确 classifier error。 | 无可编辑规则、意图分类、命中解释和失败默认策略（S5/S9）。 | recipes/magpie-core/specs/04-routing-and-failover.md:39 |
| REQ-044 Session affinity 和重置 | Routing/Intent/Tools；H: group/group-rule/search/tools；V:路由；C | 连续两轮都在同一 session 且首个 Provider 可用时使用同一账户。 | 没有 session id 时不根据 prompt 猜测会话；粘性缓存损坏时按明确默认策略而非任意 key。 | 无会话 affinity；session header 明确未实现（S5）。 | recipes/magpie-core/specs/04-routing-and-failover.md:48 |
| REQ-045 重试和 Fallback 边界 | Routing/Intent/Tools；H: group/group-rule/search/tools；V:路由；C | 模型 A 429、模型 B 200，Agent 无感知切换；请求计一次成功并标记 retried。 | 所有成员都拒绝 prompt 内容时，不把所有账户休眠多日；错误指出内容/模型拒绝类别。 | 无组/账号故障转移与完整 retry 账务边界；单上游失败不是 fallback（S5）。 | recipes/magpie-core/specs/04-routing-and-failover.md:57 |
| REQ-046 实时路由解释和历史 | Routing/Intent/Tools；H: group/group-rule/search/tools；V:路由；C | A 速率限制、B 回答后，live 视图先显示 A 的 rate limited，再显示 B answering/answered。 | 日志不可读时标明不完整/未知，不显示空白为“没有请求”。 | 无 Routing 页面、live/历史/会话 trace 与多维筛选（S6/S9）。 | recipes/magpie-core/specs/04-routing-and-failover.md:67 |
| REQ-047 System One / 模型决策 | Routing/Intent/Tools；H: group/group-rule/search/tools；V:路由；C | 请求一个已定义 choices 的分类，返回其中之一并显示对应 route decision。 | 无效 JSON、错误 choice 或上游超时时转为明确定义的 fallback，不把不可信输出当作模型 id/URL 执行。 | 无 System One/decision 模型组、输出校验与回退（S5/S9）。 | recipes/magpie-core/specs/04-routing-and-failover.md:76 |
| REQ-048 Web 搜索配置和 Provider 代搜 | Routing/Intent/Tools；H: group/group-rule/search/tools；V:路由；C | 第一个服务无结果，第二个返回结果，Agent 得到搜索结果。 | API key 被拒时不得永久删除它或错误报告为有效结果。 | 无 Provider search/外部 searcher 排序、多 key 和关闭策略（S9）。 | recipes/magpie-core/specs/04-routing-and-failover.md:84 |
| REQ-049 Agent 工具服务 | Routing/Intent/Tools；H: group/group-rule/search/tools；V:路由；C | Agent 通过 MCP 搜索网页并收到标题/URL/摘要，`magpie tool generate_image ... -o file.png` 在成功后写出文件。 | MCP 未连接、模型不支持视觉或媒体 Provider 返回错误时，给可读错误并清理未完成临时文件。 | 无 MCP 工具服务与图片/视频生成、文件保存流程（S9）。 | recipes/magpie-core/specs/04-routing-and-failover.md:92 |
| REQ-050 汇总 Usage | Usage/Sessions/Privacy；H: usage/quota/sessions/model；V:usage/masking；C | 一笔由上游响应返回 token 数量的成功请求，在 Provider、Model、Account 和总览上总计一致。 | 一笔失败或已重试请求不会将失败尝试重复计入最终成功调用；用量未返回时标成 unknown。 | /health 明示 usage_accounting 未实现；transport counters 不能当账单（S5）。 | recipes/magpie-gateway/specs/05-usage-and-sessions.md:3 |
| REQ-051 额度和窗口 | Usage/Sessions/Privacy；H: usage/quota/sessions/model；V:usage/masking；C | CLI 与 Usage 卡读取同一 quota snapshot，额度和重置时间相同。 | 离线刷新不把旧额度清零或显示为刚刷新。 | 无 quota snapshot/窗口/刷新/陈旧状态与本地时区展示（S9）。 | recipes/magpie-gateway/specs/05-usage-and-sessions.md:12 |
| REQ-052 Cap、预算和计算 | Usage/Sessions/Privacy；H: usage/quota/sessions/model；V:usage/masking；C | 设置某账户每日 cap 到 20 次后，达到第 20 次拒绝新请求并说明重置时间。 | 价格表缺失、额度 endpoint 失败或币种未知时不误报“仍在预算内”。 | 无周期 token/费用 cap、预算聚合和路由筛选（S5/S9）。 | recipes/magpie-gateway/specs/05-usage-and-sessions.md:20 |
| REQ-053 查询和分析 | Usage/Sessions/Privacy；H: usage/quota/sessions/model；V:usage/masking；C | 筛选指定 account/time 后，明细行和卡片汇总相符。 | 自定义时间区间无数据时显示无记录与筛选上下文，不沿用旧图表数。 | 无 Usage 页面、多维筛选/分组/图表和数据库查询（S6/S9）。 | recipes/magpie-gateway/specs/05-usage-and-sessions.md:28 |
| REQ-054 Session 列表 | Usage/Sessions/Privacy；H: usage/quota/sessions/model；V:usage/masking；C | 同一 Agent session 多轮 Gateway 请求列为一条 session，并显示 request count。 | 缺少原生 session id 时显示“无法确认”并用可追踪的请求 id，不伪造可跨机器稳定的 id。 | 无原生 Agent/Gateway session 合并、搜索/分类与稳定归属（S5/S6）。 | recipes/magpie-gateway/specs/05-usage-and-sessions.md:36 |
| REQ-055 消息内容、记录开关与保留 | Usage/Sessions/Privacy；H: usage/quota/sessions/model；V:usage/masking；C | 默认新安装打开 Session 页面只见元数据，不见消息文本；用户开启后新请求可查阅，按 retain limit 删除。 | 磁盘容量达到上限时停止记录/清理最旧数据并告知，Gateway 本身仍可以正常转发。 | 当前转发不写正文；完整可选录制/保留/删除/磁盘上限流程待建（S5/S9）。 | recipes/magpie-gateway/specs/05-usage-and-sessions.md:44 |
| REQ-056 隐私遮蔽 | Usage/Sessions/Privacy；H: usage/quota/sessions/model；V:usage/masking；C | 向上游的文本不再包含配置中匹配的明文，而本机可选明文记录的权限和保留策略明确。 | 规则语法错误或遮蔽服务不可用时按 fail-closed 处理，提示用户，而非安静发送原文。 | 无 masking 规则、审计计数和 fail-closed 中间件（S5/S9）。 | recipes/magpie-gateway/specs/05-usage-and-sessions.md:53 |
| REQ-057 Pricing | Usage/Sessions/Privacy；H: usage/quota/sessions/model；V:usage/masking；C | 用户修改自定义模型价格后，过去数据可明确区分使用历史价格还是按新价格重算。 | 未知模型 pricing 显示 `unknown`，不让费用图表以零误导。 | 目录没有完整定价状态；价格覆盖、历史价和成本来源待建（S3/S9）。 | recipes/magpie-gateway/specs/05-usage-and-sessions.md:61 |
| REQ-058 导出 | Usage/Sessions/Privacy；H: usage/quota/sessions/model；V:usage/masking；C | 一份 CSV 在表格软件中打开时数据没有错位，汇总值与页面筛选结果一致。 | 没有数据或写文件失败时给具体错误，不保留截断的成功文件。 | 无 Usage/quota/session CSV/JSON 筛选导出和原子文件结果（S4/S9）。 | recipes/magpie-gateway/specs/05-usage-and-sessions.md:69 |
| REQ-059 Quota/Usage API 与请求追踪 | Usage/Sessions/Privacy；H: usage/quota/sessions/model；V:usage/masking；C | 控制台与 HTTP API 用相同筛选查询得到相同的聚合数。 | 上游 usage 返回两次或使用两种协议时，去重依据是 request/attempt id，不是脆弱的内容相同判断。 | 无统一 usage/quota API、request/attempt 账务和按 key 过滤（S5）。 | recipes/magpie-gateway/specs/05-usage-and-sessions.md:76 |
| REQ-060 内置 Provider 与社区插件边界 | Plugins；H: plugin；C（全部运行行为 U） | 插件版 Zed 的登录/模型/请求都交给 `opencode-zed-auth`，插件关闭时 Provider 不可用并解释原因。 | 插件 host 未运行，不伪装成 built-in service 能接管。 | 无社区 Provider 插件执行边界和退休 builtin owner（S9）。 | recipes/magpie-gateway/specs/06-plugins-and-tools.md:3 |
| REQ-061 社区 Provider 插件查找和安装 | Plugins；H: plugin；C（全部运行行为 U） | 按包名安装插件后，Packages/Plugins 列表和插件 host 一致；升级成功后版本号更新。 | 包不存在、下载/解析失败、版本不兼容时保留上一个有效版本和可读错误。 | 无插件目录/安装/兼容/更新锁定和失败保留旧版（S9）。 | recipes/magpie-gateway/specs/06-plugins-and-tools.md:27 |
| REQ-062 社区 Provider 插件运行 | Plugins；H: plugin；C（全部运行行为 U） | 一个插件 host 崩溃只使该插件不可用，状态栏中给出失败详情。 | 未知的插件消息版本被隔离拒绝，不崩溃整个 Magpie，也不在协议降级时把密钥转到插件日志。 | 无版本化 Node host 协议、凭据最小化与崩溃隔离（S9）。 | recipes/magpie-gateway/specs/06-plugins-and-tools.md:35 |
| REQ-063 订阅账号转移 | Plugins；H: plugin；C（全部运行行为 U） | `migrations.json` 显示迁移状态，旧 saved account 确认由插件 sign-in 可用后计为已迁移。 | 迁移失败时，不要删除旧账号数据，也不要报告为已由插件服务；“Move to plugin”可再次尝试。 | 无订阅 Move/Adopt 状态与单向恢复迁移 runner（S9）。 | recipes/magpie-gateway/specs/06-plugins-and-tools.md:43 |
| REQ-064 插件登录和模型目录 | Plugins；H: plugin；C（全部运行行为 U） | 插件授权成功后模型可在 `/v1/models` 及相关 Agent models 设置中出现。 | re-auth 失败时账号仍保留、显式要求用户重新登录、不循环弹窗。 | 无插件 OAuth/device/browser/key 登录及目录/额度回传（S9）。 | recipes/magpie-gateway/specs/06-plugins-and-tools.md:53 |
| REQ-065 Web 和其它扩展工具 | Plugins；H: plugin；C（全部运行行为 U） | 添加一个 MCP tool 后 Agent 清单可读取，设置变化后正常 reconnect。 | 启动超时/无效 schema 只禁用这个 server，并说明命令、退出码或 protocol error，不将其当普通 chat 响应。 | 无外部 MCP/web connector 状态、schema 校验与取消传播（S9）。 | recipes/magpie-gateway/specs/06-plugins-and-tools.md:61 |
| REQ-066 社区 Provider 插件可观测性和移除 | Plugins；H: plugin；C（全部运行行为 U） | 插件移除后仍保留明确的移除记录，并且 Provider 列表解释不再服务。 | plugin 状态服务不可读时显示 unknown/error，而不是 enabled/healthy。 | 无插件版本/错误/移除影响/数据留存与重装流程（S9）。 | recipes/magpie-gateway/specs/06-plugins-and-tools.md:69 |
| REQ-067 Magpie 内置功能插件 | Plugins；H: plugin；C（全部运行行为 U） | Routing feature plugin 因一个 routing group 正在使用显示为 required/active；删除最后一个 group 后允许关闭。 | 一个 plugin Apply panic/error 后回滚自己已注册项，不阻止其他 feature plugin 启动。 | 无 feature plugin 服务依赖 reconcile 和生命周期（S9）。 | recipes/magpie-gateway/specs/06-plugins-and-tools.md:77 |
| REQ-068 Gateway extension points | Plugins；H: plugin；C（全部运行行为 U） | 启用一个 JS middleware 后 `/v1/chat/completions` 请求被 observer 识别并按其 hook 定义继续或拒绝。 | 某个 middleware 抛错时关联 request id 并按其配置的失败策略处理，不重复转发一次请求。 | 无 extension point 注册/dispose、JS middleware 和单次处理链（S9）。 | recipes/magpie-gateway/specs/06-plugins-and-tools.md:85 |
| REQ-069 插件种类和状态区分 | Plugins；H: plugin；C（全部运行行为 U） | 用户搜索 `cursor` 时能区分内置的状态页与社区 `opencode-cursor-auth` 登录插件。 | 状态 API 返回来源不明时标为 unknown，不把一个宿主的 healthy 状态复制到其他 plugin 类型。 | 无 feature/Provider/MCP/Agent 扩展的独立状态与 UI（S6/S9）。 | recipes/magpie-gateway/specs/06-plugins-and-tools.md:93 |
| REQ-070 统一 Library | Library/Settings；H: library/backup/restore/webdav/s3/github；C | 将一条 skill 装给 Codex 和 Claude Code 后，两边使用各自格式而语义相同。 | 配置目标无法读取时，不把它当空文件覆盖；显示路径和读/解析错误。 | 无统一 Library 数据/目标跟踪/事务写入和自有内容清理（S9）。 | recipes/magpie-core/specs/07-library-sync-and-backup.md:3 |
| REQ-071 Instructions、skills 和 Agent 级操作 | Library/Settings；H: library/backup/restore/webdav/s3/github；C | 卸载一个 Agent 后，它持有的既有 Library 项仍列出且可选择移除；未卸载 Agent 看不到那一项。 | 第三方 Agent 覆写 marker 外内容时，同步不还原整份旧配置。 | 无具名 instructions、skills、hooks/subagents 的原生格式应用（S9）。 | recipes/magpie-core/specs/07-library-sync-and-backup.md:11 |
| REQ-072 MCP servers | Library/Settings；H: library/backup/restore/webdav/s3/github；C | stdio server 返回一页 `tools/list`，Library 显示数量并将定义写入目标 Agent 配置。 | 超时标明在哪一步等待多久；401/403 不回报健康；一个失效 MCP 不影响其他服务器。 | 无 MCP 配置分配、握手/tools-list、OAuth 和步骤超时（S9）。 | recipes/magpie-core/specs/07-library-sync-and-backup.md:20 |
| REQ-073 Projects | Library/Settings；H: library/backup/restore/webdav/s3/github；C | 为一个项目安排 skills 后，只在该 project 的 Agent session 中出现。 | 目标路径变成一个 Agent 自己的指令或 MCP 文件时拒绝写入而不移动/覆盖原文件。 | 无 project 身份/目录保护/按 Agent 安装和自有文件跟踪（S9）。 | recipes/magpie-core/specs/07-library-sync-and-backup.md:28 |
| REQ-074 RTK 输出压缩 | Library/Settings；H: library/backup/restore/webdav/s3/github；C | 应用一个已知支持的 RTK 配置，原命令的结果和 exit code 保持相同但输出更短。 | RTK 不存在时 Agent 命令仍正常执行或清楚提示用户。 | 无 RTK 探测/安装/配置/退出码保留流程（S9）。 | recipes/magpie-core/specs/07-library-sync-and-backup.md:36 |
| REQ-075 本地文件备份和可选区段 | Library/Settings；H: library/backup/restore/webdav/s3/github；C | 把 Library 和 profiles 备份后在新配置目录还原，选择区段都可回读。 | 口令错误、版本新于本客户端、损坏文件或任一必需的本地 JSON 不可读时，中止全部写入并保留本机状态。 | 无加密备份分区、Restore Preview 和 Undo（S9）。 | recipes/magpie-core/specs/07-library-sync-and-backup.md:44 |
| REQ-076 加密远端同步 | Library/Settings；H: library/backup/restore/webdav/s3/github；C | 一台电脑修改 agents 配置，两台机同步后另一台看到该变更且未触碰其本机专属设置。 | 远端不可读、数据损坏、权限失败或冲突时停止并保留本地与远端旧版本，不把文件解释成空配置。 | 无 WebDAV/S3/GitHub 同步、分区合并和条件写入（S9）。 | recipes/magpie-core/specs/07-library-sync-and-backup.md:52 |
| REQ-077 每台电脑保留的设置 | Library/Settings；H: library/backup/restore/webdav/s3/github；C | 换机恢复 profile/provider groups 但不自动开启局域网共享或替换本机窗口尺寸。 | 字段新增或本客户端不能识别时仅忽略/保留该字段而不是清空 settings。 | 无同步/恢复时保留本机专属值的完整字段策略（S9）。 | recipes/magpie-core/specs/07-library-sync-and-backup.md:61 |
| REQ-078 远端内容和恢复保护 | Library/Settings；H: library/backup/restore/webdav/s3/github；C | 远端 404 经过权限和 branch check 后视为首份；明确 empty repository 路径只初始化默认分支。 | 远端返回无效/损坏索引时不尝试以本机空配置覆盖。 | 无远端 schema/版本/分片校验与错误内容保护（S9）。 | recipes/magpie-core/specs/07-library-sync-and-backup.md:69 |
| REQ-079 Usage 数据与请求档案 | Library/Settings；H: library/backup/restore/webdav/s3/github；C | 同步 usage 后，另一台电脑看到用量趋势，但它的 local gateway recording switch 没有被打开。 | 没有 archive destination 时不写本机 prompt body；达到容量限制后移除最旧请求并继续转发。 | 无独立 Usage/正文档案同步与默认关闭/容量策略（S9）。 | recipes/magpie-core/specs/07-library-sync-and-backup.md:77 |
| REQ-080 运行形态 | 运行/Settings；H: 顶层、web/tui/serve/update；C | Linux 无桌面环境运行 `magpie web` 或 TUI，可操作同一 gateway/provider 配置。 | 页面加载失败或 GUI 崩溃不损坏后端文件；服务退出时客户端收到明确状态。 | CLI 与 GPUI 分离；桌面不自启完整服务，无 Web/TUI/托盘单实例生命周期（S4/S7/S9）。 | recipes/magpie-interfaces/specs/08-cli-tui-and-web.md:3 |
| REQ-081 桌面导航和设置 | 运行/Settings；H: 顶层、web/tui/serve/update；C | 改一个 Gateway 设置，页面 API 和本地 CLI 立即读到相同结果。 | API 操作失败保持当前编辑值/显示错误，不把暂存值作为已保存成功。 | 仅 Agents/Settings 两个实际页面；缺完整导航、设置搜索与保存反馈（S6）。 | recipes/magpie-interfaces/specs/08-cli-tui-and-web.md:11 |
| REQ-082 浏览器 Web UI | 运行/Settings；H: 顶层、web/tui/serve/update；C | 从登录后的手机浏览器连接同一 LAN 上显式开启的 Web UI，用专属 key 完成登录和只读查看。 | 没有 web key 的未授权来源无法通过 API、资源 URI 或 WebSocket 读取私有数据。 | 无 Web UI、独立登录 key/会话、授权资源与浏览器流程（S4/S9）。 | recipes/magpie-interfaces/specs/08-cli-tui-and-web.md:19 |
| REQ-083 TUI | 运行/Settings；H: 顶层、web/tui/serve/update；C | 增加 Provider 后从 GUI 重新进入能看到同一 Provider。 | quota 请求失败时显示最近更新时间和错误而不清除已知状态。 | 无 TUI 表格/编辑/刷新与同域状态（S4/S9）。 | recipes/magpie-interfaces/specs/08-cli-tui-and-web.md:27 |
| REQ-084 CLI 接口 | 运行/Settings；H: 顶层、web/tui/serve/update；C | `magpie provider models deepseek --json` 输出有效模型 JSON，`magpie usage --csv` 输出筛选后的记录。 | 缺参数/未知命令提示正确用法并返回非零状态，不改配置。 | 现有少数 CLI 命令不能替代原版广泛公开命令；别名/参数/help 兼容待建（S4）。 | recipes/magpie-interfaces/specs/08-cli-tui-and-web.md:35 |
| REQ-085 Agent 状态、更新和配置流程 | 运行/Settings；H: 顶层、web/tui/serve/update；C | 只装了 Claude Code CLI 的设备不显示一个可配置到另一台机器的“本地 Claude Code”。 | Agent 已卸载但遗留目录时，不向无程序目标写入不可读取设置。 | 缺 Agent 安装来源/更新/实例/WSL、全部配置预览与真实版本流程（S1/S8）。 | recipes/magpie-interfaces/specs/08-cli-tui-and-web.md:50 |
| REQ-086 应用更新和可诊断错误 | 运行/Settings；H: 顶层、web/tui/serve/update；C | 升级被网络/代码签名拒绝后仍能启动当前版本并查看失败理由。 | 无网络时显示版本状态未知，不称为已升级到最新。 | 无完整更新频道/下载/失败回退与脱敏诊断导出（S9）。 | recipes/magpie-interfaces/specs/08-cli-tui-and-web.md:58 |
| REQ-090 凭据存放和界面遮蔽 | Privacy/Network/Platforms；H: web/gateway-key/accounts；C；业务 U | 显示 Provider详情时 key 只有尾部掩码；用户替换后详情/下一次调用使用新值。 | 安全存储不可读时停止导出/同步/认证操作并说明，不把空 key 误认为用户删除。 | 只有 env 引用与有限私有事务文件；完整跨平台安全凭据库/reveal/备份待建（S2/S5/S9）。 | recipes/magpie-core/specs/09-privacy-security-and-platforms.md:3 |
| REQ-091 网络入口、鉴权和限流 | Privacy/Network/Platforms；H: web/gateway-key/accounts；C；业务 U | loopback client 本机免 key；远程 LAN 请求只对有效 key 放行并遵守其模型和额度限制。 | 无效 key、origin/host spoofing、CSRF 或 client credential 窃取尝试按失败处理并记录 request id，不返回敏感数据。 | loopback 限制是子集；无远程 key scope、预算/撤销、Web/WS 统一授权（S5）。 | recipes/magpie-core/specs/09-privacy-security-and-platforms.md:11 |
| REQ-092 Privacy 默认和本机数据 | Privacy/Network/Platforms；H: web/gateway-key/accounts；C；业务 U | 新安装没有 prompt archive，默认 Logs 不含 messages。 | 设置保存失败时 UI 明确显示，不能提示保护设置已成功启用。 | forward 不记正文且错误脱敏；完整 Privacy 设置/保留/导出/同步约束待建（S5/S6）。 | recipes/magpie-core/specs/09-privacy-security-and-platforms.md:19 |
| REQ-093 Private URL、代理和 SSRF | Privacy/Network/Platforms；H: web/gateway-key/accounts；C；业务 U | 用户明确配置的私有 Git/npm/relay 地址可访问并显示来源。 | 不可信 webhook/MCP/page 将 HTTP redirect 指向本机 admin endpoint 时，下载器阻止请求并提示 SSRF policy。 | 拒绝非字面 loopback 不是完整 proxy/URL/SSRF/可信私网下载实现（S5/S9）。 | recipes/magpie-core/specs/09-privacy-security-and-platforms.md:27 |
| REQ-094 多平台和 WSL 边界 | Privacy/Network/Platforms；H: web/gateway-key/accounts；C；业务 U | Windows/WSL 中 Claude Code 两套配置可各自连接 Gateway、拥有正确会话归属。 | WSL distro 停止时 UI 显示 last-seen/asleep，不自动启动；系统代理配置不完整时不假称 Agent 经代理连接。 | 平台有各自既有部分记录；本轮不运行设备；WSL/全平台安装及原生 GUI 未验收（S7/S9）。 | recipes/magpie-core/specs/09-privacy-security-and-platforms.md:34 |
| REQ-095 隐私工具与外部上游 | Privacy/Network/Platforms；H: web/gateway-key/accounts；C；业务 U | 用户请求 image generation 后 Usage 能指出用哪个模型与 Provider。 | 用户关闭 search/tool 时，上游不再接收相应隐式工具调用。 | 无外部分类/search/MCP/媒体调用授权、成本与禁用状态全链路（S9）。 | recipes/magpie-core/specs/09-privacy-security-and-platforms.md:42 |
| REQ-100 视觉方向 | 全页/设置；V:19图；H: 顶层；C；实际交互 U | Providers、Routing、Usage 等同一主题的面板、标签和状态色具有一致的层级。 | 暗色主题下保留背景的 SVG/icon 或低对比状态不可辨识时提供合适的 variant。 | 两轮 Pro 视觉均被否决；源码/字体检查不构成当前真实窗口视觉通过（S6/S9）。 | recipes/magpie-interfaces/specs/10-visual-and-localization.md:3 |
| REQ-101 App 和紧凑 Panel 布局 | 全页/设置；V:19图；H: 顶层；C；实际交互 U | 宽窗、窄窗、360px panel 都可查看 account 名称、quota reset 和刷新控件。 | 错误长文本不会覆盖刷新按钮或阻止用户关闭/返回。 | 有最小窗口/布局代码；没有实际 Panel/托盘与全页窄屏验收（S6/S7）。 | recipes/magpie-interfaces/specs/10-visual-and-localization.md:11 |
| REQ-102 语言 | 全页/设置；V:19图；H: 顶层；C；实际交互 U | switch locale 后 UI 和主要状态提示一致，不混入半个未翻译 key。 | 未知系统 locale 选择英文且不让启动时崩溃。 | 有五语与输入基础；当前设置为 SessionOnly，持久化/locale 数字时区等待建（S6/S9）。 | recipes/magpie-interfaces/specs/10-visual-and-localization.md:19 |
| REQ-103 可访问性与操作反馈 | 全页/设置；V:19图；H: 顶层；C；实际交互 U | 全键盘可添加/保存 Provider 与关闭 dialog；没有 mouse-only 动作。 | 后台异步更新状态时不会突移焦点/丢失用户当前输入。 | 有焦点/aria/取消代码基础；原生 IME/读屏/缩放/reduce motion 与全产品待验收（S6/S8）。 | recipes/magpie-interfaces/specs/10-visual-and-localization.md:27 |
| REQ-104 参考截图与产品数据 | 全页/设置；V:19图；H: 顶层；C；实际交互 U | 使用 `UI_REFERENCE.md` 的 Providers 与 Routing 文字层级作为参考，重建使用独立占位名称。 | 文字参考缺失或矛盾时报告材料问题，不依赖原仓库图片或网络盗链；不得以下载原版资源代替设计。 | 本轮取得19张匹配图供参照；不等于 Rust 页面/真实数据或原生渲染通过（S6/S9）。 | recipes/magpie-interfaces/specs/10-visual-and-localization.md:34 |

## 7. 全部 68 Agent 的最低集成基线

以下不是本机 0.1.1948 的完整支持上限。本轮只运行 help，不运行 `agents`、`ls` 或状态查询；不能从帮助结尾的 13 个快捷名推断只有 13 个 Agent，也不能从未探测的命令推断该版本不支持其它 Agent。Claude Science、Antigravity CLI、Cursor Private Inference 等在旧最低目录中出现但没有精确入口细节，继续保留待补，不编造路径或 schema。

每个 Agent 最终都需要独立检验：安装/配置/不可读状态、原生字段/版本、只改目标字段、预览/写入/原子失败、漂移/恢复、模型可见性/Profile、多入口一致、原生新会话生效和适用 OS/WSL。只提供导入的 Agent 按它自己的确认契约验收。下表全部实际原版配置和客户端运行均为 U；没有读取用户配置。

| # / Agent | 合同入口与主要操作 | 本机原版本轮证据 | 固定 Rust 状态 | 保留缺口 |
| --- | --- | --- | --- | --- |
| 01 Claude Code | ~/.claude/settings.json；Provider/model、tier、subagent/auto、专属 effort；恢复自有字段 | H: claude；fields: model, effort, ultracode, opus, sonnet, haiku, fable, subagent, opus_effort, sonnet_effort, haiku_effort, fable_effort, subagent_effort, auto_mode, login；实际业务 U | 仅 PATH 安装发现；未读/未写其配置（S1/S8） | 专属配置、连接/断开、恢复及完整工作流 |
| 02 Claude Desktop | 各 OS Claude/Claude-3p 数据目录，含 Windows MSIX 实例；Gateway Provider、tier、subagent/effort；按版本/实例处理 | C: 最低目录；本机命令/配置/业务 U | 无专属发现/配置集成（S1/S9） | 发现与适用集成全流程，或该 Agent 的公开导入流程 |
| 03 Claude Science | 最低目录已列名；精确公开路径/schema 待补；保留发现/适用字段/连接恢复目标，不猜实现 | C: 最低目录；本机命令/配置/业务 U | 无专属发现/配置集成（S1/S9） | 发现与适用集成全流程，或该 Agent 的公开导入流程 |
| 04 Codex | ~/.codex/config.toml；遵循 CODEX_HOME；Provider/model/effort；有预览、写入、漂移和恢复 | H: codex；fields: model, effort, subagent, subagent_model, subagent_effort, memories, login；实际业务 U | 部分：TOML 读取与七字段事务/桌面桥（S1/S2/S8） | 更多原生字段、完整目录/可见性/Profile/OS 与窗口验收 |
| 05 Gemini CLI | ~/.gemini/settings.json 与 .env；认证与 model；保留用户环境值 | H: gemini；fields: provider, model；实际业务 U | 仅 PATH 安装发现；未读/未写其配置（S1/S8） | 专属配置、连接/断开、恢复及完整工作流 |
| 06 Antigravity CLI | 最低目录已列名；精确公开路径/schema 待补；保留发现/适用字段/连接恢复目标，不猜实现 | V: Agents 图出现此行；本轮 CLI/schema/业务 U | 无专属发现/配置集成（S1/S9） | 发现与适用集成全流程，或该 Agent 的公开导入流程 |
| 07 OpenCode | ~/.config/opencode/opencode.json/jsonc；OPENCODE_CONFIG_DIR；model/small；保留 JSONC 和自定义 Provider | H: opencode；fields: model, small；实际业务 U | 仅 PATH 安装发现；未读/未写其配置（S1/S8） | 专属配置、连接/断开、恢复及完整工作流 |
| 08 OpenChamber | preferences.json（精确平台目录待确认）；model/small；适用时复用 OpenCode Provider | C: 最低目录；本机命令/配置/业务 U | 无专属发现/配置集成（S1/S9） | 发现与适用集成全流程，或该 Agent 的公开导入流程 |
| 09 MiMo Code | mimocode.jsonc（精确平台目录待确认）；model/small；适用时复用 OpenCode Provider | H: mimocode；fields: model, small；实际业务 U | 无专属发现/配置集成（S1/S9） | 发现与适用集成全流程，或该 Agent 的公开导入流程 |
| 10 DevEco Code | deveco.jsonc；DEVECO_CONFIG_DIR；model/small；保留其他 Provider | H: deveco；fields: model, small；实际业务 U | 无专属发现/配置集成（S1/S9） | 发现与适用集成全流程，或该 Agent 的公开导入流程 |
| 11 Pi | ~/.pi/agent/settings.json；model 与原生支持的 thinking/effort | H: pi；fields: model, effort；实际业务 U | 无专属发现/配置集成（S1/S9） | 发现与适用集成全流程，或该 Agent 的公开导入流程 |
| 12 oh-my-pi | 各原生 settings/provider/model 文件；精确目录待确认；model；fx 为 keyless Magpie Provider | C: 最低目录；本机命令/配置/业务 U | 无专属发现/配置集成（S1/S9） | 发现与适用集成全流程，或该 Agent 的公开导入流程 |
| 13 StepCode | ~/.stepcode/config.toml、models.json；Agent 自有目录变量；默认 Provider/model | C: 最低目录；本机命令/配置/业务 U | 无专属发现/配置集成（S1/S9） | 发现与适用集成全流程，或该 Agent 的公开导入流程 |
| 14 MothX | ~/.mothx/settings.json 或 MOTHX_DIR；Provider/model/thinking | C: 最低目录；本机命令/配置/业务 U | 无专属发现/配置集成（S1/S9） | 发现与适用集成全流程，或该 Agent 的公开导入流程 |
| 15 PenguinHarness | PENGUIN_HOME / PENGUIN_PROJECT_ID 下 project TOML；Provider/默认 model；按 project 身份 | C: 最低目录；本机命令/配置/业务 U | 无专属发现/配置集成（S1/S9） | 发现与适用集成全流程，或该 Agent 的公开导入流程 |
| 16 Aside | ~/.aside/u/0/settings.json、models.json；model/effort；额外字段见本机 H，需再验真实行为 | H: aside；fields: model, effort, image, fast, standard, deep, visual, account；实际业务 U | 无专属发现/配置集成（S1/S9） | 发现与适用集成全流程，或该 Agent 的公开导入流程 |
| 17 OmO | ~/.omo/agent/settings.json 与模型目录变量；默认 model | C: 最低目录；本机命令/配置/业务 U | 无专属发现/配置集成（S1/S9） | 发现与适用集成全流程，或该 Agent 的公开导入流程 |
| 18 Prime Agent | ~/.prime/agent/settings.json 与模型目录变量；默认 model/thinking | C: 最低目录；本机命令/配置/业务 U | 无专属发现/配置集成（S1/S9） | 发现与适用集成全流程，或该 Agent 的公开导入流程 |
| 19 Goose | ~/.config/goose/config.yaml；model；本机 H 还声明 effort | H: goose；fields: model, effort；实际业务 U | 无专属发现/配置集成（S1/S9） | 发现与适用集成全流程，或该 Agent 的公开导入流程 |
| 20 Cursor CLI | ~/.cursor/cli-config.json；model；恢复原值 | H: cursor；fields: model；实际业务 U | 无专属发现/配置集成（S1/S9） | 发现与适用集成全流程，或该 Agent 的公开导入流程 |
| 21 Cursor Private Inference | 最低目录已列名；精确公开路径/schema 待补；保留发现/适用字段/连接恢复目标，不猜实现 | C: 最低目录；本机命令/配置/业务 U | 无专属发现/配置集成（S1/S9） | 发现与适用集成全流程，或该 Agent 的公开导入流程 |
| 22 Zed | ~/.config/zed/settings.json；model；使用 Zed 原生 Provider 格式 | H: zed；fields: model；实际业务 U | 仅 PATH 安装发现；未读/未写其配置（S1/S8） | 专属配置、连接/断开、恢复及完整工作流 |
| 23 VS Code Chat | 各 User/settings.json 与 chatLanguageModels.json，独立 Profile；默认 Chat model/custom endpoint；保留其余 Profile | C: 最低目录；本机命令/配置/业务 U | 无专属发现/配置集成（S1/S9） | 发现与适用集成全流程，或该 Agent 的公开导入流程 |
| 24 VS Code Insiders Chat | 各 User/settings.json 与 chatLanguageModels.json，独立 Profile；默认 Chat model/custom endpoint；保留其余 Profile | C: 最低目录；本机命令/配置/业务 U | 无专属发现/配置集成（S1/S9） | 发现与适用集成全流程，或该 Agent 的公开导入流程 |
| 25 VSCodium Chat | 各 User/settings.json 与 chatLanguageModels.json，独立 Profile；默认 Chat model/custom endpoint；保留其余 Profile | C: 最低目录；本机命令/配置/业务 U | 无专属发现/配置集成（S1/S9） | 发现与适用集成全流程，或该 Agent 的公开导入流程 |
| 26 Zoo Code | VS Code 用户设置与 Zoo Code import 文件；OpenAI compatible Provider；由目标启动导入 | C: 最低目录；本机命令/配置/业务 U | 无专属发现/配置集成（S1/S9） | 发现与适用集成全流程，或该 Agent 的公开导入流程 |
| 27 JetBrains Air | acp.json 及相邻 Magpie OpenCode 配置；Agent Client Protocol 模型菜单 | C: 最低目录；本机命令/配置/业务 U | 无专属发现/配置集成（S1/S9） | 发现与适用集成全流程，或该 Agent 的公开导入流程 |
| 28 Copilot (JetBrains) | GitHub Copilot language-server byok.json；仅 Magpie BYOK Provider 自有字段 | C: 最低目录；本机命令/配置/业务 U | 无专属发现/配置集成（S1/S9） | 发现与适用集成全流程，或该 Agent 的公开导入流程 |
| 29 Copilot CLI | 各 settings/profile/provider 格式；精确版本/目录待确认；model、适用 small/effort；DeepSeek Harness 还需 Gateway key | H: copilot；fields: model, effort；实际业务 U | 无专属发现/配置集成（S1/S9） | 发现与适用集成全流程，或该 Agent 的公开导入流程 |
| 30 Crush | 各 settings/profile/provider 格式；精确版本/目录待确认；model、适用 small/effort；DeepSeek Harness 还需 Gateway key | H: crush；fields: model, small, effort；实际业务 U | 无专属发现/配置集成（S1/S9） | 发现与适用集成全流程，或该 Agent 的公开导入流程 |
| 31 DeepSeek Harness | 各 settings/profile/provider 格式；精确版本/目录待确认；model、适用 small/effort；DeepSeek Harness 还需 Gateway key | C: 最低目录；本机命令/配置/业务 U | 无专属发现/配置集成（S1/S9） | 发现与适用集成全流程，或该 Agent 的公开导入流程 |
| 32 Reasonix Studio | ~/.reasonix/config.toml 与 .env；Executor/Planner/effort | C: 最低目录；本机命令/配置/业务 U | 无专属发现/配置集成（S1/S9） | 发现与适用集成全流程，或该 Agent 的公开导入流程 |
| 33 Command Code | 各原生 settings/provider/model 文件；精确目录待确认；model；fx 为 keyless Magpie Provider | C: 最低目录；本机命令/配置/业务 U | 无专属发现/配置集成（S1/S9） | 发现与适用集成全流程，或该 Agent 的公开导入流程 |
| 34 fx | 各原生 settings/provider/model 文件；精确目录待确认；model；fx 为 keyless Magpie Provider | C: 最低目录；本机命令/配置/业务 U | 无专属发现/配置集成（S1/S9） | 发现与适用集成全流程，或该 Agent 的公开导入流程 |
| 35 Devin | 各原生 settings/provider/model 文件；精确目录待确认；model；fx 为 keyless Magpie Provider | C: 最低目录；本机命令/配置/业务 U | 无专属发现/配置集成（S1/S9） | 发现与适用集成全流程，或该 Agent 的公开导入流程 |
| 36 Hermes Agent | 各自配置与目录变量；精确版本路径待确认；按原生 Responses/OpenAI compatible/Provider 结构写 model | V: Agents 图出现此行；本轮 CLI/schema/业务 U | 无专属发现/配置集成（S1/S9） | 发现与适用集成全流程，或该 Agent 的公开导入流程 |
| 37 Mister Morph | 各自配置与目录变量；精确版本路径待确认；按原生 Responses/OpenAI compatible/Provider 结构写 model | C: 最低目录；本机命令/配置/业务 U | 无专属发现/配置集成（S1/S9） | 发现与适用集成全流程，或该 Agent 的公开导入流程 |
| 38 Kimi Code | 各自配置与目录变量；精确版本路径待确认；按原生 Responses/OpenAI compatible/Provider 结构写 model | V: Agents 图出现此行；本轮 CLI/schema/业务 U | 无专属发现/配置集成（S1/S9） | 发现与适用集成全流程，或该 Agent 的公开导入流程 |
| 39 jcode | 各 JSON/TOML Provider 格式；精确目录待确认；默认 model/Provider | C: 最低目录；本机命令/配置/业务 U | 无专属发现/配置集成（S1/S9） | 发现与适用集成全流程，或该 Agent 的公开导入流程 |
| 40 Qwen Code | 各自配置与目录变量；精确版本路径待确认；按原生 Responses/OpenAI compatible/Provider 结构写 model | C: 最低目录；本机命令/配置/业务 U | 无专属发现/配置集成（S1/S9） | 发现与适用集成全流程，或该 Agent 的公开导入流程 |
| 41 Muse Code | 各自配置与目录变量；精确版本路径待确认；按原生 Responses/OpenAI compatible/Provider 结构写 model | C: 最低目录；本机命令/配置/业务 U | 无专属发现/配置集成（S1/S9） | 发现与适用集成全流程，或该 Agent 的公开导入流程 |
| 42 Empryo | 各 JSON/TOML Provider 格式；精确目录待确认；默认 model/Provider | C: 最低目录；本机命令/配置/业务 U | 无专属发现/配置集成（S1/S9） | 发现与适用集成全流程，或该 Agent 的公开导入流程 |
| 43 Ante | 各 JSON/TOML Provider 格式；精确目录待确认；默认 model/Provider | C: 最低目录；本机命令/配置/业务 U | 无专属发现/配置集成（S1/S9） | 发现与适用集成全流程，或该 Agent 的公开导入流程 |
| 44 AstrBot | JSON/TOML Provider 配置；支持 ASTRBOT_ROOT；默认 model/Provider；Docker 安装不等于本机发现 | C: 最低目录；本机命令/配置/业务 U | 无专属发现/配置集成（S1/S9） | 发现与适用集成全流程，或该 Agent 的公开导入流程 |
| 45 MiniMax Code | 各自模型或自定义 Provider 配置；model/适用 effort；BYOK 套餐条件可见 | C: 最低目录；本机命令/配置/业务 U | 无专属发现/配置集成（S1/S9） | 发现与适用集成全流程，或该 Agent 的公开导入流程 |
| 46 Droid (Factory) | 各自模型或自定义 Provider 配置；model/适用 effort；BYOK 套餐条件可见 | C: 最低目录；本机命令/配置/业务 U | 无专属发现/配置集成（S1/S9） | 发现与适用集成全流程，或该 Agent 的公开导入流程 |
| 47 Cline CLI | 各自模型或自定义 Provider 配置；model/适用 effort；BYOK 套餐条件可见 | C: 最低目录；本机命令/配置/业务 U | 无专属发现/配置集成（S1/S9） | 发现与适用集成全流程，或该 Agent 的公开导入流程 |
| 48 Qoder CLI | 各自模型或自定义 Provider 配置；model/适用 effort；BYOK 套餐条件可见 | C: 最低目录；本机命令/配置/业务 U | 无专属发现/配置集成（S1/S9） | 发现与适用集成全流程，或该 Agent 的公开导入流程 |
| 49 Qoder CN CLI | 各自模型或自定义 Provider 配置；model/适用 effort；BYOK 套餐条件可见 | C: 最低目录；本机命令/配置/业务 U | 无专属发现/配置集成（S1/S9） | 发现与适用集成全流程，或该 Agent 的公开导入流程 |
| 50 Grok Build | 各自 TOML/App 目录；Grok App 支持 GROK_APP_HOME；model/effort/自定义 Provider；保留 App 后改路由 | C: 最低目录；本机命令/配置/业务 U | 无专属发现/配置集成（S1/S9） | 发现与适用集成全流程，或该 Agent 的公开导入流程 |
| 51 Grok App | 各自 TOML/App 目录；Grok App 支持 GROK_APP_HOME；model/effort/自定义 Provider；保留 App 后改路由 | C: 最低目录；本机命令/配置/业务 U | 无专属发现/配置集成（S1/S9） | 发现与适用集成全流程，或该 Agent 的公开导入流程 |
| 52 ZCode | 各 Provider/model JSON 和目录变量；Provider/model/适用当前会话 model | C: 最低目录；本机命令/配置/业务 U | 无专属发现/配置集成（S1/S9） | 发现与适用集成全流程，或该 Agent 的公开导入流程 |
| 53 WorkBuddy | 各 Provider/model JSON 和目录变量；Provider/model/适用当前会话 model | C: 最低目录；本机命令/配置/业务 U | 无专属发现/配置集成（S1/S9） | 发现与适用集成全流程，或该 Agent 的公开导入流程 |
| 54 WorkBuddy AI | 各 Provider/model JSON 和目录变量；Provider/model/适用当前会话 model | C: 最低目录；本机命令/配置/业务 U | 无专属发现/配置集成（S1/S9） | 发现与适用集成全流程，或该 Agent 的公开导入流程 |
| 55 CodeBuddy Code | 各 Provider/model JSON 和目录变量；Provider/model/适用当前会话 model | C: 最低目录；本机命令/配置/业务 U | 无专属发现/配置集成（S1/S9） | 发现与适用集成全流程，或该 Agent 的公开导入流程 |
| 56 Raycast | 原生 YAML；添加 Magpie Provider，保留其他项 | C: 最低目录；本机命令/配置/业务 U | 无专属发现/配置集成（S1/S9） | 发现与适用集成全流程，或该 Agent 的公开导入流程 |
| 57 Kiro Crew | 原生 JSON；添加 Magpie Provider，保留其他项 | C: 最低目录；本机命令/配置/业务 U | 无专属发现/配置集成（S1/S9） | 发现与适用集成全流程，或该 Agent 的公开导入流程 |
| 58 OpenPencil | 各应用 settings 数据；添加 Provider；应用正在写数据时拒绝冲突 | C: 最低目录；本机命令/配置/业务 U | 无专属发现/配置集成（S1/S9） | 发现与适用集成全流程，或该 Agent 的公开导入流程 |
| 59 NewMax | 各应用 settings 数据；添加 Provider；应用正在写数据时拒绝冲突 | C: 最低目录；本机命令/配置/业务 U | 无专属发现/配置集成（S1/S9） | 发现与适用集成全流程，或该 Agent 的公开导入流程 |
| 60 T3 Code | 各自 settings/catalog/API 入口；model/effort/model Profile；仅本机可访问入口 | C: 最低目录；本机命令/配置/业务 U | 无专属发现/配置集成（S1/S9） | 发现与适用集成全流程，或该 Agent 的公开导入流程 |
| 61 OpenHanako | 各自 settings/catalog/API 入口；model/effort/model Profile；仅本机可访问入口 | C: 最低目录；本机命令/配置/业务 U | 无专属发现/配置集成（S1/S9） | 发现与适用集成全流程，或该 Agent 的公开导入流程 |
| 62 AtomCode | 各自 settings/catalog/API 入口；model/effort/model Profile；仅本机可访问入口 | C: 最低目录；本机命令/配置/业务 U | 无专属发现/配置集成（S1/S9） | 发现与适用集成全流程，或该 Agent 的公开导入流程 |
| 63 Snow CLI | 各自 settings/catalog/API 入口；model/effort/model Profile；仅本机可访问入口 | C: 最低目录；本机命令/配置/业务 U | 无专属发现/配置集成（S1/S9） | 发现与适用集成全流程，或该 Agent 的公开导入流程 |
| 64 Alma | 本机 API，合同默认 loopback 23001；默认 model 与 auto 图片 model | C: 最低目录；本机命令/配置/业务 U | 无专属发现/配置集成（S1/S9） | 发现与适用集成全流程，或该 Agent 的公开导入流程 |
| 65 Pencil | 各应用公开导入/添加 Provider 确认流程；仅按导入契约添加；不可伪装成直接配置写入 | C: 最低目录；本机命令/配置/业务 U | 无专属发现/配置集成（S1/S9） | 发现与适用集成全流程，或该 Agent 的公开导入流程 |
| 66 Cindy | 各应用公开导入/添加 Provider 确认流程；仅按导入契约添加；不可伪装成直接配置写入 | C: 最低目录；本机命令/配置/业务 U | 无专属发现/配置集成（S1/S9） | 发现与适用集成全流程，或该 Agent 的公开导入流程 |
| 67 Lody | 各应用公开导入/添加 Provider 确认流程；仅按导入契约添加；不可伪装成直接配置写入 | C: 最低目录；本机命令/配置/业务 U | 无专属发现/配置集成（S1/S9） | 发现与适用集成全流程，或该 Agent 的公开导入流程 |
| 68 AionUi | 各应用公开导入/添加 Provider 确认流程；仅按导入契约添加；不可伪装成直接配置写入 | C: 最低目录；本机命令/配置/业务 U | 无专属发现/配置集成（S1/S9） | 发现与适用集成全流程，或该 Agent 的公开导入流程 |

## 8. 本轮额外或更细的公开能力观察（最低基线以外继续保留）

93 REQ / 68 Agent 是保留底线，不能当成上限。下列 OBS 是 **0.1.1948 实际 help 的声明**，包含旧 REQ 未明确枚举或仅概括覆盖的能力；不擅自将其映射为已完成 REQ，也不因为旧规格未逐字列出就删掉。优先保留为后续差异核验输入，实际业务、持久化、Provider 兼容和各平台均 U。没有打开真实账户、运行工具/更新/重置或启动后台服务。

| 观察 ID | 额外 / 更细的公开能力 | 准确帮助证据 | Rust 差距与后续核验边界 |
| --- | --- | --- | --- |
| OBS-01 | `model disable/enable/disabled` 对模型/模式执行全局拒绝；与每 Agent 隐藏不同 | H: model | Rust 无此状态和全入口 403 规则；需核验分组/显式 ID/目录一致性 |
| OBS-02 | `model wire/wires` 改上游请求名；Usage alias 合并统计但不改变路由；显示 suffix on/own/off | H: model | Rust 只有当前单 Provider 模型改写；缺可编辑 wire/alias/suffix 和历史费用归属 |
| OBS-03 | 模型 context/output/vision/reasoning spec 覆盖、通配 Provider 覆盖和 reset 优先级 | H: model | 公共 catalog 未提供这组覆盖状态/原生列表投影；行为 U |
| OBS-04 | `model compact` 及 Agent 页 Long conversations、Provider Compact at 优先级 | H: model | 缺压缩窗口策略与 Codex/Claude 原生配置；实际阈值/兼容需另验 |
| OBS-05 | 分级 pricing、cache read/write、不同 cache 时长价格；未知价格与真实免费价分开 | H: model | 完整 pricing/历史价未建；帮助数值是格式示例，不是当前供应商价格 |
| OBS-06 | `routing=manual` / `group pick` 只用指定成员，其他成员/规则不接管 | H: group | 当前无组引擎；需和 strict stays、普通 fallback 明确区分 |
| OBS-07 | `group template smart/thrifty/steady` 用已有模型建模板，并处理重名 | H: group | 无模板/组 CRUD；不能用预填假模型代替 |
| OBS-08 | 成员 glob/regex 动态匹配；模型可以固定 effort、多等级或 fast；nested group 帮助给出 8 层边界 | H: group | 所有成员展开、能力校验、更新和递归边界待建 |
| OBS-09 | group id 改名、自动组开关、隐藏后恢复和模型移出组行为 | H: group | 缺稳定 ID/引用更新与现有 Agent 指向处理 |
| OBS-10 | `effort=auto/agent` 与 classifier 对回合推理级别决策；用户 none 不被擅自开启 | H: group | 无 classifier/effort 决策；需要记录 asked 与 actually sent |
| OBS-11 | rule `pause=`、时段/星期/时区、folder/except-folder、compact、多个 use 回退及 use-effort | H: group-rule | 缺规则引擎；help 区分回合匹配和每请求 pause，实际持久化/trace 待验 |
| OBS-12 | 顶层 `group add` help 出现 `free`，详细 group help 还出现 manual；详细策略段与顶层枚举并不完全一致 | H: 顶层、group | `free` 实际接受/选择语义 U；不能只凭枚举猜规则或删去原有七种策略 |
| OBS-13 | `quota reset`、auto-reset week/expiring；帮助明确有一次性额度和不可撤销语义 | H: quota | Rust 没有此能力；本轮未运行；后续必须保留显式确认和默认开关边界 |
| OBS-14 | `quota wait` 允许脚本等待额度恢复，指定 timeout/quiet，帮助列退出码 | H: quota / quota-wait | Rust 无额度等待/取消；需核验等待不影响状态和真实供应商刷新 |
| OBS-15 | quota history 帮助给 45 天保存、UTC 窗口分代；单卡 refresh 与读数缓存边界 | H: quota | 与本机 Usage 时间展示、窗口来源和 stale 一致性待建/待验 |
| OBS-16 | quota alert 按消耗百分比/余额提醒；credits 和自动 reset 为不同账户选择 | H: quota | 无通知/配额策略；未知余额与价格不得补零 |
| OBS-17 | `accounts switch-only codex` 只切 Agent 原生登录、请求直达供应商；账户每日 check-in | H: accounts | 无账户运行模式和通知；直连模式不能伪称 Gateway 账务覆盖 |
| OBS-18 | Codex subagent/subagent_model/subagent_effort/memories/login；Claude ultracode/各 tier effort；Aside image/fast/standard/deep/visual/account 等字段 | H: codex/claude/aside；13 种 Agent help | 超出当前 Rust model/provider/effort 子集；具体 schema 与当前客户端版本仍 U |
| OBS-19 | `claude-code install/remove/runs` 管理官方客户端；runs 可按机器内存自动限制并发 | H: claude-code | Rust 无官方订阅客户端管理；没有下载/安装/测量，不猜内存公式 |
| OBS-20 | 社区插件还支持 Pi packages、JS middleware/hooks、options、use 指定服务来源 | H: plugin | 无相关运行时；不能只做 npm Provider 登录覆盖这些扩展 |
| OBS-21 | builtin preset essentials/all、plugins.yml 分层 `dump`、required/pending 状态查看 | H: plugin | 缺内置插件和层级配置事实来源；help 不是 runtime 通过 |
| OBS-22 | Library skill link/copy 默认或逐 Agent How；use-library/keep-own 冲突，gone/all 删除与保留本地编辑 | H: library | 无冲突和所有权操作；不得将 Library 简化为只读文件浏览器 |
| OBS-23 | Web gateway mode 可隐藏 Agents/Sessions/Library，并由 Settings General 控制；无 Agent 时可自动启用 | H: web | 完整产品功能仍保留，此模式是显式运行场景；Rust Web 未建 |
| OBS-24 | Web help 声明持久 Web key 时 400 天登录；旧 C 合同要求短生命周期 session cookie | H: web；C:082 | **保留版本/安全合同差异，不静默改弱 C。** 实际 cookie/撤销/续期未观察，待独立协议检查 |
| OBS-25 | daemon 的 systemd user/launchd 安装启动恢复、同进程 Web、持久 key 与 TLS 开关 | H: daemon/serve | Rust 没有 daemon 控制；打包脚本或服务文件存在不算实际安装运行 |
| OBS-26 | 更新 stable/every 频道、mirror/proxy、auto download/notify/间隔和 SHA-256 验证说明 | H: update | Rust 无完整更新器；未查询 latest、未下载/更新任何程序 |
| OBS-27 | Tools 支持输出目录/多张命名、已有文件不覆盖、JSON 输出不落文件、参数在调用前校验 | H: tools/tool | Rust 工具体系未建；需实际文件/取消/参数及不同返回类型验收 |
| OBS-28 | search 优先 Provider 自带，再 Provider 代搜，再配置 searcher；开关保留 key、URL 和多 key 休眠策略 | H: search | 无搜索链；帮助中的免费额度/时间参数不当成当前服务保证 |
| OBS-29 | heap/cpu/goroutines 通过本机 Gateway 导出运行诊断，避免覆盖已有文件 | H: heap/cpu/goroutines | Rust 需要提供等价可诊断能力，不必复刻 Go 内部类型；本轮未采集真实进程/堆/栈 |
| OBS-30 | statusline 显示会话模型和账户额度；quota、sessions 和独立 JSON/CSV 脚本流 | H: statusline/quota/sessions/usage | Rust 缺用户任务的可管道输出/关联状态；保留原公开命令兼容目标 |

## 9. 低内存、多平台与视觉的完成边界

本轮没有性能或设备运行数据，不作原版/Rust 谁更小、更快的结论。原版二进制 66,600,984 字节（SHA-256 `9aa760cbac67f375a810fcf15b9adf04edcb2cc7de64a9ebb0963da15c1d3eb7`）只是这台机器的安装文件身份；不可拿当前功能子集二进制或一次空闲 RSS 直接比较完整产品。

| 模式 / 平台 | 保留目标 | 本轮证据与未完成边界 |
| --- | --- | --- |
| CLI / headless | 无显示/GPU 初始化，完整 CLI/服务业务，低内存有界队列/缓存/流 | S4/S5/S9 有子集结构；未运行 Rust，原版只执行 help/version |
| Linux / Arch / Nix/NixOS | CLI、GPUI、托盘/Web/TUI/服务、实际包安装升级卸载 | 本轮不构建/安装；原生窗口、NixOS 和发行验收 U |
| macOS / Windows | 原生 GPUI、menu/tray、凭据库、更新/安装器；Windows 配置事务完整 | README 仍明确 Windows 事务禁写；全部原生环境验收 U |
| Android / Termux | CLI/headless 与原生 GPUI GUI 分列，设备低内存运行 | 配方既有双 ARM 转发/四 ABI 构建记录保留，不冒充本轮执行；Android 事务曾因祖先权限拒绝，GUI 后端未解决 |
| WSL / Docker / NAS | 各自模式明确、独立环境/路径/账号和停止状态 | 本轮只看 H/C，未运行容器或唤醒 distro；不能用宿主能力推断来宾 |
| CPU | x86_64、AArch64、ARMv7、i686、RISC-V64 的适用模式与最低前提 | 旧构建/QEMU 记录不等于固定提交全部原生通过；逐目标继续保留 |
| 低内存 / 包体 | 等价完整业务、同设备同负载、完整进程树/外部 runtime，记录 RSS/PSS/峰值/取消回落 | PERFORMANCE 的历史子集测量和候选预算不是用户已确认阈值，也不是优化胜出；完整 A/B 未做 |
| UI 视觉 | 两主题/宽窄窗/五语/真实 GPUI 场景与窗口，不逊于原版并获用户认可 | 两轮 Pro 仍被否决；HTML、字体 shaping、编译或组件检查均不代替视觉验收 |

## 10. 无法核验项与需要后续完成的证据

1. 安装版 0.1.1948 的全部真实页面、完整支持 Agent 列表、实际操作成功/失败/恢复和跨入口一致性。13 个帮助快捷名是帮助摘录，不能据此压缩 68 Agent，也不能封顶可能更多 Agent。
2. 版本对齐：包管理器版本、程序自报版本、旧合同提取提交和官网截图没有已证实的一一构建对应关系。已保留具体版本/哈希，未猜测“谁最新”或版本先后。
3. Settings 完整分页/Font Settings 图、Providers 详情/账号/额度/Plugins/Library/Sessions/Web/TUI 的真实静态图；本轮已找到的截图仍只覆盖第 1 节列出的视图。没有精确 URL 的资产不猜链、不恢复已删源码或新建归档。
4. 可访问性、原生字体实际解析、布局断点、动画、IME、屏幕阅读器、真实键盘/焦点、加载/空/错误/保存/漂移/长标签。公开图无法独立证明它们。
5. 任何真实 OAuth/订阅/外部 Provider、真实 Agent 用户配置、新旧备份兼容、远端服务/网络策略和平台安全存储。保留为后续隔离 fixtures/独立账户与设备验收，未接触用户账户。
6. 额外 OBS 中帮助枚举不一致、Web 会话期限与旧安全合同不同等问题；保留冲突，后续固定版本独立观察，不能用推断修订成已验证行为。

## 11. 阅读指纹、证据文件与本轮执行记录

本轮按照 root BUILD_PROMPT 的递归链读取 GLOBAL、DEPENDENCIES、PERFORMANCE、OPTIMIZATION、MIGRATION、GENERATED_LAYOUT、PUBLISHING，interfaces 的入口/验收/spec08/spec10/UI_REFERENCE，gateway 的入口/验收/spec03/spec05/spec06，core 的入口/验收/spec01/spec02/spec04/spec07/spec09，quality 的入口/验收/SOURCE，以及根 ACCEPTANCE/COVERAGE/EVIDENCE/OPEN_QUESTIONS/UI_DIRECTION。全文以外部 recipe-inputs 快照和下列读取时散列定位；全部 93 REQ 正文保留，矩阵不替代合同。

外部 evidence 目录：`24-original-parity-evidence/`。其中 public-images/*.png 是用户需要的公开参照交付物；HTML 是公开来源原文；JSON 是 SHA-256、结果、命令和矩阵索引。没有创建源码归档、恢复旧源码、运行原版服务/GUI、运行真实 Provider 请求、编译/测试或写入任何仓库文件。临时沙盒 HOME/网络环境随进程销毁，没有应清理的生成构建物；这些小型交付证据继续保留。

| 证据 | 内容 |
| --- | --- |
| `recipe-manifest.json` + `recipe-inputs/` | 每个配方文件的读取时间、bytes、SHA-256 及准确字节；已知并行后续变更不混入这一快照 |
| `official-pages.json` / `official-extra-pages.json` + `official-*.html` | 五个准确公开页面、读取时间/最终 URL/状态/HTML SHA 和字面图片 URL |
| `public-images.json` + `public-images/` | 26 个已声明静态图片 URL、声明页、实际 bytes/尺寸/散列、19 张历史指纹匹配 |
| `historical-public-asset-metadata.txt` | 本仓历史 README 的官网入口链、Font Settings 图片 Git 路径/blob/size；未导出旧源码 |
| `original-binary-metadata.json` | 本机程序路径、字节数、SHA、自报版本和包归属 |
| `original-help.json` + `original-help*.txt` | 68 条保存的纯 help/version 命令记录、隔离启动前缀、stdout/stderr/退出码/输出 SHA；全部退出 0。记录条数不是 Agent 支持数；初次只读 version 探查同样为 0.1.1948 |
| `page-operation-matrix.json` | 41 项页面/任务/恢复/缺口映射 |
| `requirements-matrix.json` | 93 个最低 REQ、合同定位、正常/失败条件、原版观察边界与固定 Rust 缺口 |
| `agents-matrix.json` | 68 个最低 Agent、已知公开配置/原版帮助证据/Rust 状态，未知项不猜路径 |

配方读取时指纹：

| 路径 | SHA-256 | bytes |
| --- | --- | --- |
| `recipes/README.md` | `defe790aa780e34c50c610f28e2f8b46f50defcd498e10cd6c2d86511063e042` | 1836 |
| `recipes/magpie/ACCEPTANCE.md` | `13f0aba780640a929b8c5f98cc19e0220a4347d62821d50dc0af5e8b8f9ee0a1` | 9468 |
| `recipes/magpie/AGENTS.md` | `d4d3a909df11aa6503e1cfcdeb04bbb8ef4eed168727ec31a0e3c0a1a348e162` | 1244 |
| `recipes/magpie/BUILD_PROMPT.md` | `80112708f9ee6e729662043f76be62831d14e3fadab4bc5d1acf92e6a07e50c5` | 7255 |
| `recipes/magpie/COVERAGE.md` | `7afbd7f42d7b812f037ffbd5ac98d0592e68399d4ff6c997810e5ca3201d1adb` | 15880 |
| `recipes/magpie/DEPENDENCIES.md` | `d2e49a68e50ad1d2890ae38a2135e92ee070526cde89c058c292d3758c125922` | 2469 |
| `recipes/magpie/EVIDENCE.md` | `5527d7e57c9be3d519941a405be2c11f3e9f0938b03019a42a564453ff9c987c` | 15405 |
| `recipes/magpie/GENERATED_LAYOUT.md` | `734f37ef730affe42537a57074fd578026b6870b20b8e7b864c084afc4f61b72` | 1634 |
| `recipes/magpie/GLOBAL.md` | `c40f6fb1d0cd25c389e697493d3a0c18f1f0b057f83527403811ded19cac431a` | 13462 |
| `recipes/magpie/LICENSE.md` | `79d2c8444715d4bc453ec4f8a0aaf2051a4c1ee5ac08f5bd2e5848aef87c7572` | 1063 |
| `recipes/magpie/MIGRATION.md` | `faff3f7e1a389c4e334a8a6a623b081b9e17cace7ceeb9184f8f0f1b0f025d29` | 8659 |
| `recipes/magpie/OPEN_QUESTIONS.md` | `ca9a9cc908d5b15ffe2e769390b1aac9fe894482a2780e7911370eeb1e533368` | 4056 |
| `recipes/magpie/OPTIMIZATION.md` | `24be3e307d0c27a1304ae55ee44688e1a8e4ecc388ef40a400bb6aedc4c92e41` | 5825 |
| `recipes/magpie/PERFORMANCE.md` | `1a91dc6f7b76163433dc09a57cc6c8042b2388cef89de77a01d17628239eaba6` | 8831 |
| `recipes/magpie/PUBLISHING.md` | `0e29d5f65b14759471b0a5ff3dd5d907726d62f4b8156d0588cebe63698ae433` | 1972 |
| `recipes/magpie/RECIPE.md` | `aef80d09299aa5547ffbc04ce75afa49f25f0da83ee1ea61de55b449709c3e4b` | 591 |
| `recipes/magpie/UI_DIRECTION.md` | `26f1376e46c1fd681331c5b48027e059a6474c472fd8e66315b31d9f41c5e3cf` | 3066 |
| `recipes/magpie-core/ACCEPTANCE.md` | `2a507399b68999878de060f0ea89d1e99fca896f5c4ba26a1ca3dbd62eb5010a` | 18873 |
| `recipes/magpie-core/AGENTS.md` | `30c35871cdb4743aecd547032af3c8546770c535d84fe16352986658373e108b` | 832 |
| `recipes/magpie-core/BUILD_PROMPT.md` | `25703e12c8410f958600d832d3957f026b59271467784074d399014107d1368e` | 1846 |
| `recipes/magpie-core/LICENSE.md` | `79d2c8444715d4bc453ec4f8a0aaf2051a4c1ee5ac08f5bd2e5848aef87c7572` | 1063 |
| `recipes/magpie-core/RECIPE.md` | `d4064ca4ec23cab61e1b09ba6b60e0247e2f12df5d943d8e23a16df1ace4e2f2` | 374 |
| `recipes/magpie-core/specs/01-product-and-agents.md` | `8ec202dd29744b43be5b63b1d23f6f96e131c58ec86a2effb121ba592976e258` | 13802 |
| `recipes/magpie-core/specs/02-providers-and-accounts.md` | `622b002005999e16ed4df555e6adc8e8ecbdfd9db5a4f16dda21bbd29058987e` | 11828 |
| `recipes/magpie-core/specs/04-routing-and-failover.md` | `2d861feaccefc1c448be6c87fba4397d99c922bb496a422773a1aea724d85cb8` | 9698 |
| `recipes/magpie-core/specs/07-library-sync-and-backup.md` | `96ef63012e5cd44e7b77c4d6242392c622bde438062655827bf90d48e6fc5f7b` | 8833 |
| `recipes/magpie-core/specs/09-privacy-security-and-platforms.md` | `87f076d09e2454274a340ac96b5b34fa07715d33a55118c06424bcfd41cc69ee` | 5120 |
| `recipes/magpie-gateway/ACCEPTANCE.md` | `d1fbd862bb8b25043458e5fa644d0922e60ffaa6135dee367572e951b721bf43` | 12140 |
| `recipes/magpie-gateway/AGENTS.md` | `30c35871cdb4743aecd547032af3c8546770c535d84fe16352986658373e108b` | 832 |
| `recipes/magpie-gateway/BUILD_PROMPT.md` | `2d8fa246240bcb8d10527399f72830f2653709566c725393cdcfc6b92295d44c` | 1999 |
| `recipes/magpie-gateway/LICENSE.md` | `79d2c8444715d4bc453ec4f8a0aaf2051a4c1ee5ac08f5bd2e5848aef87c7572` | 1063 |
| `recipes/magpie-gateway/RECIPE.md` | `1020b147afd0dc5440a30c97d668508fa4a08d59aecac243480169f6ec5aa22a` | 415 |
| `recipes/magpie-gateway/specs/03-gateway-and-protocols.md` | `f189a922e5efd2ac3b1bfecc1487ceea446be157a70fd9c93198d8dd261e344a` | 10923 |
| `recipes/magpie-gateway/specs/05-usage-and-sessions.md` | `99b87b4076d92cafa929ef6ea9b54d1d79a7e2fa6a4dc5799cf8aa4fc1b3573e` | 8165 |
| `recipes/magpie-gateway/specs/06-plugins-and-tools.md` | `a011e730dec484d101d7de2a549aeb6b5b9fcfd1f3e17c984ff28fc259d581d4` | 9703 |
| `recipes/magpie-interfaces/ACCEPTANCE.md` | `1cafb79593fd64d2ee8ba0bd1dbaf332ca5c91bcee772e4482a8709524cbc43d` | 4908 |
| `recipes/magpie-interfaces/AGENTS.md` | `30c35871cdb4743aecd547032af3c8546770c535d84fe16352986658373e108b` | 832 |
| `recipes/magpie-interfaces/BUILD_PROMPT.md` | `ff601e1ed55faafe4495e439c64660cace1159e67877a159c1745252e8ef3e46` | 2082 |
| `recipes/magpie-interfaces/LICENSE.md` | `79d2c8444715d4bc453ec4f8a0aaf2051a4c1ee5ac08f5bd2e5848aef87c7572` | 1063 |
| `recipes/magpie-interfaces/RECIPE.md` | `0cd57745f70aa715ba799408b8ff778464e51ff0595046c91a4ddbb3a02af334` | 432 |
| `recipes/magpie-interfaces/UI_REFERENCE.md` | `034c51390a06b3d2ac5757acaa00ddd618f2d681f3c509fc7576bb7f59899fef` | 6543 |
| `recipes/magpie-interfaces/specs/08-cli-tui-and-web.md` | `c738936571aae7229e3300df9c2a36d7c6406283914cb71c0b1c80408e478f05` | 5877 |
| `recipes/magpie-interfaces/specs/10-visual-and-localization.md` | `c4358192bd42c40eaea5d10e9746d7101e67a87e733026400658915302b9b47f` | 3937 |
| `recipes/software-quality/ACCEPTANCE.md` | `556b007e62be3bd0681ab0e3904502e8134ef072ba0d2995771edb8731cd7587` | 2190 |
| `recipes/software-quality/BUILD_PROMPT.md` | `342501e49cedbed36af247eec925c35a3c52cd9831d0da9169c999082d18cb47` | 5636 |
| `recipes/software-quality/LICENSE.md` | `97016fcf641f0ebb5c3fb6a0eda2e2bec82aecd443f3acd24a536499d5fa28a4` | 1070 |
| `recipes/software-quality/RECIPE.md` | `ff02ece72586830b0db2c30ab9f0c6ee332d11632d1f8bd28ac76906499f3d89` | 438 |
| `recipes/software-quality/SOURCE.md` | `e8e39b90e1321436ceb0054e20d911b1f64fd5cc254fc53e1017efac2fdb8048` | 706 |

公开页面读取时指纹（网页不提供安装版 build 对应关系）：

| 准确 URL | HTTP | SHA-256 | 读取 UTC |
| --- | --- | --- | --- |
| <https://usemagpie.ai/> | 200 | `c6e4e1aa4da3dc21b9e08b97536b0311e039062daa811342f380267120e3d3bf` | 2026-10-28T20:12:15.880504+00:00 |
| <https://usemagpie.ai/docs/start> | 200 | `13888d12d3e95c490c738e9a578999f58a62ee02ec54105fbcbbd352b8b4f229` | 2026-10-28T20:12:15.880504+00:00 |
| <https://usemagpie.ai/docs/intent> | 200 | `32d89735c00a101f6eb11613fd3782775c7beda57161c8aa2fc7c204a962b3d8` | 2026-10-28T20:13:35.773680+00:00 |
| <https://usemagpie.ai/docs/import> | 200 | `75d25f71ad6873a757635b971735422c1d51bf9557c2dc8a1d0fb401c7276f6a` | 2026-10-28T20:13:35.761700+00:00 |
| <https://usemagpie.ai/docs/privacy> | 200 | `c633a343afae1a75fa1af5d91ef2990ebae0c9fa0cf04c6110d6b0a68fd3eb95` | 2026-10-28T20:13:35.896949+00:00 |

本次只读基线材料已经可交接；完整产品、原版真实差异执行及用户视觉认可仍是待完成目标。93 REQ / 68 Agent 保留为最低清单，额外观察项继续纳入后续工作。
