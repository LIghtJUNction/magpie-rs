# 验证证据

## 更新日志、完整审计与功能迭代（2026-10-30）

本轮按用户“直接合并”指令，先合并上一轮源码 [PR #2](https://github.com/LIghtJUNction/magpie-rs-source/pull/2) 与主仓 [PR #1](https://github.com/LIghtJUNction/magpie-rs/pull/1)，起点分别为 `338c9328428d95c5cc776c04b713c09c7b9c5d28` 与 `487eda5ef4afce9b96d1305e8d9252bdd05ee828`。本轮新源码合并提交为 `9ff54775bd3aa0c7b9436e06f0c281fa5a7f6166`，父仓保存该准确 gitlink；发布树与验证输入逐一核对。本节是当前快照，下方历史记录不替代本轮边界。

- 原版固定 **0.1.2185 / f1151226262022743bd103a78af41e1004cc6055**。完整阅读官方 237 条发布，包含 1948 基线 1 条、1949–2148 的 199 条及旧源码之后 2149–2185 的 37 条；官方没有 2070。版本摘要歧义回到固定源码核实，逐发布映射在 UPSTREAM_CHANGELOG.md。
- 配方升级 **1.4.0**，core/gateway 均为 **1.1.0**。旧 93 条编号全部保留，新增 MCP Hub、Responses 续接、TTS、只读 Gateway key、Agent 历史读取许可、Responses WebSocket，共 99 条。补录 17 族、69 条公开路由注册，纠正旧版对 Ollama 原生生成、音频、视频来源和两种正文存储的遗漏。登记是目标合同，不是已实现计数。
- 99 条逐一阅读实现与证据，当前为 **39 部分、10 仅组件、50 未实现**；新增六项全未实现。完整入口/边界/下一步见 COVERAGE 和源码两份 46/47 条基线审计；本轮实现增量单独记录，没有将全平台或真实外部验收改成已通过。
- 桌面新增真实 Gateway 页面和自有子进程生命周期：默认启动、启停、实际版本/实例/目录检查、复制地址、本机监听偏好；外部服务只读。私有 stdin lease 在正常退出或持有者异常退出时释放服务；公开 identity 不作为所有权凭证。关闭等待期间不再允许启动，Provider 变化后重新进入会刷新目录。CLI/headless 不初始化 GPUI。
- 已接入 Codex 可以直接换模并保留首次恢复点。CLI/桌面走同一更新事务；SHA-256 revision 绑定完整配置与文件身份，过期预览拒绝，模型不变无写入，用户自有 magpie Provider 不接管。连续换模后离线断开恢复最初字节；更新日志 v2 与恢复映像覆盖明确中断窗口，旧程序遇 v2 拒绝误恢复。
- Chat 参数兼容按 2155 的精确域名与 HTTP 400 诊断规则改名一次，共用 deadline/并发槽，保留其他请求字节；其他错误和已开始 SSE 不重放。未实现原版跨请求参数偏好缓存或通用路由 fallback。目录模式与转发模式现在共用真实 loopback/Host/Origin/代理头边界。
- Linux x86_64、Rust 1.99.1、锁定依赖：workspace **327 passed / 0 failed**；4 个默认忽略入口是父测试会启动的 worker。全 targets Clippy `-D warnings` 与 fmt 通过。实际监督 CLI 的 ready/health/inspect、端口冲突、损坏 store、EOF/重启，以及单独 owner 进程被结束后服务自行退出和端口可复用均通过。最终 CLI 在独立路径构建，避免共享缓存被另一构建替换，SHA-256 `18c294f42a6d6067f6f1f3a1bb788380a5efb5e759590c120cc69f3eb15cc83c`。
- 桌面最终 78 passed / 0 failed；两条实际 CLI opt-in 另行通过，验证自有服务/外部只读及连接→换模→停止→离线恢复。剩余一个安装版 Codex worker 本轮未重跑。独立 workspace 的 visual-test 全 targets 严格 Clippy 和 fmt 通过。实际原生 Scene 生成中文 80 场景、1×/2× 共 160 PNG，覆盖 1280/900/440/360 宽度与浅深主题；逐张尺寸吻合。实际目视复核 5 个代表场景（主审 4 个），最终字节与所看图片一致；没有将全部渲染称为全部视觉通过。窄窗换模按钮已修正可达，Gateway 下方表单仍需滚动；原生窗口交互与用户视觉认可未验收。
- 配方准确版本的 pack、归档 check、unpack、解包目录 check、中文 build，以及 UTF-8/LOCK/需求编号/依赖 pin 核对见主仓 [verification/recipe-1.4.0.md](https://github.com/LIghtJUNction/magpie-rs/blob/main/verification/recipe-1.4.0.md)。源目录离线 check 对未解析 Git 依赖的退出 2 单独说明，不伪报为直接通过。临时归档与生成提示词按发布合同删除。

实现说明与机器证据位于源码 `docs/changelog-parity-2026-10-30.md` 和 `tests/results/changelog-parity-2026-10-30.json`，包括输入散列、各检查结果和原生渲染记录。所有新流程使用合成配置和本机上游；真实外部 Provider/Agent、macOS/Windows/Android、完整 OS 窗口/IME/读屏、用户视觉认可、Release/AUR 与系统安装均未在本轮通过。Windows 配置写入和 Android 原生 GPUI 后端的既有缺口仍保留。


## 共享 Provider 与原版 UI 对齐（2026-10-30）

本轮源码 `452d59e0e1c3d96cd7110ebd6506c38448fa3198` 已发布到独立仓库 `improve/provider-ui-parity` 分支，并建立 [源码 PR #2](https://github.com/LIghtJUNction/magpie-rs-source/pull/2)。远端内容树与本地测试树逐字节相同；父仓库保存准确 gitlink，具体见 `GENERATED_LAYOUT.md`。完整实现说明和机器证据分别位于源码 `docs/provider-ui-parity-2026-10-30.md` 与 `tests/results/provider-ui-parity-2026-10-30.json`。下方此前所有“当前”表述均为对应日期的历史快照，不能替代本轮的实现与验证边界。

- 固定读取原版 yetone/magpie `dca3e2d716fe21bbb3e239f769a8e7861b135c01` 的页面结构与CSS，结合既有原版隔离操作记录及公开浅/深参考。GPUI保持Rust原生实现，继续采用原版连续列表、白灰/炭黑与紫色选中态、宽窗侧栏与中窄顶部导航。
- ProviderStore接入CLI、GPUI及Gateway：自定义Provider新增/编辑/启停/删除、直接密钥输入、系统凭据引用、真实模型刷新和按Provider/model路由。Linux真实Secret Service的合成key跨CLI/Gateway进程读取、轮换即时生效、停用阻止新请求及删除后清理已测；未使用真实账户。
- 4协议模型发现复用同一传输与认证规则，支持Anthropic/Gemini分页，失败/不完整/超限/并发更新不覆盖旧目录。每Provider最多4096模型、全store最多16384、store1MiB；合法最大目录与JSON转义膨胀测试通过，完整HTTP目录上限32MiB，不截断有效模型。
- 本轮Linux x86_64（Ubuntu24.04.3、Rust1.99.1）根workspace全targets 298 passed、4个辅助进程入口按测试设计忽略；全targets Clippy -D warnings与fmt通过。真实Secret Service独立脚本7项检查通过，包括Codex合成配置的dry-run/apply/原字节恢复。已安装Codex客户端、其他系统和真实外部Provider没有在本轮重新执行。
- 独立GPUI workspace使用固定revision `37faef81f74fc1bb08be9939c4cffc8ede6ced2f`；桌面输入/偏好/操作组件68项通过、1个既有可选native-client worker本轮未执行，全目标严格Clippy与fmt通过。实际GPUI Scene覆盖1280×720、900×560、440×560、360×560，浅深色以及Agents、Codex、Providers、编辑表单、凭据区和Settings。中文48场景及德语360px补充12场景的manifest与指纹见源码机器证据。截图使用合成数据，只证明对应离屏像素，不表示原生窗口键盘/IME/读屏或用户视觉认可。
- 保留93个唯一REQ，更新23项有新增证据的状态，未将部分实现标为全部通过。预设/OAuth/多账户、完整Usage/路由/插件/Library/同步、其他Agent写入和桌面Gateway生命周期仍在待办；Windows配置写入仍明确拒绝，新增依赖后的其他平台需复验。
- Linux打包依赖补齐D-Bus，13项现有打包自检、shell语法及本地ELF/Debian依赖解析通过；没有将这些检查等同于真实安装包验收。
- 本轮交付源码与配方审阅分支；未生成或发布多平台Release/AUR，也未替换用户安装。旧性能、Android真机和原版CLI对照结果继续保留为其对应版本的历史证据。

## 2026-10-29 业务与桌面操作快照（历史）

当前源码 pin 为 `f20439bcc03d527f8c713b1b22c066604e16ba7a`，已推送并回读远端 main；父仓库固定此 gitlink。其他尚未提交的并行工作不包含在该 pin。

- bounded loopback HTTP Provider 已真实转发 Chat/Responses JSON及SSE，支持模型重写、固定脱敏错误、有界并发/正文/事件和取消；外部HTTPS、跨协议转换等仍未实现。
- Codex七字段事务支持只读预览、显式保存、归属重读和离线恢复；不变时恢复原字节，漂移时保留外部字段。私有暂存/日志有界，Linux/macOS/Android写入路径已实现，Windows明确禁写；真实断电及全部设备场景未验收。
- 根 workspace 在应用桌面快照适配后77项通过、2个worker入口由父测试实际调用；Clippy、fmt、rustdoc和release通过。26项真实TCP/启动/CLI用例通过，安装版Codex0.165.0经合成本地Provider收到中文Responses流并恢复原配置；原始证据在源码 `tests/results/`。
- GPUI actions r1 已原生release构建并通过全目标Clippy、59项组件测试。新增ignored worker由Python显式运行两次：已有配置和原来不存在的配置，两场景均通过真实ActionsState→CLI bridge→CLI→Gateway→Codex请求，停止Gateway后恢复原字节/不存在；8项协调器自检通过。原始结果为源码 `tests/results/desktop-actions-2026-10-29.json`。这是无窗口业务联调，不是原生窗口/IME/读屏验收。
- CLI release为3,271,792字节，SHA256 `e3463d7717d0f8a73b5ea6d63d363e9835ce1c81dc0afbdb5c83bbf3696b86f1`；不将其与旧空目录版本或完整原版直接比较性能。
- 当前业务的Android四ABI API23 release构建通过；LG G820上ARM64/ARMv7真实JSON/SSE转发通过。设备配置事务因`/data`祖先0771权限保护而在prepare阶段拒绝，未执行apply/restore。外部完整证据在 `/home/lightjunction/.cache/magpie-rs-build/android/validation-20261029-a7f29c61/results.json`；构建业务闭包的根Cargo与crates共21文件与当前逐字节一致。x86/i686无设备执行；旧musl/QEMU结果不冒充当前版本验收。
- 用户已明确否决此前Pro暖白/深墨绿与名录/详情方向，并要求不得逊于原版。新任务22-visual-parity-request revision1已发Pro，对照原版浅/深截图并行做视觉、字体和独立审查。视觉未通过，旧窗口和HTML预览不构成新方案验收。
- Pro visual r2已应用并通过Linux release/fmt/Clippy、59组件及两场景真实Codex业务复验；可复现原生font_probe的75组shaping均无缺字，主字体Adwaita Sans。合成配置的原生预览曾启动，随后用户再次否决；它未获视觉通过。源码证据为 `tests/results/desktop-visual-r2-2026-10-29.json`。
- 用户最新明确功能必须与原版一比一还原，配方1.3.2已补充完整对照验收。23样式/完整功能请求及25标准递归rewrite合同已上传回读并发给Pro；本地并行实现真实GPUI离屏Scene渲染、事务强杀锁恢复、四协议转换与原版基线核对。这一阶段的旧源码在强杀写入后可能遗留需人工处理的协作锁；后续锁恢复结果见下方最新记录。
- AUR/Release尚未发布，未替换安装；全部93REQ及68Agent目标保留，Web/TUI、其他Agent写入、Usage/路由/插件等仍有大量未完成要求。

以下均为历史快照，其旧pin、实现边界和验收状态不描述当前版本。

## 已否决的 Pro UI与基准修正快照（历史）

该次快照源码pin为 `ffe2b6f20bfa06be9cdcd8b1f15a9d8308026725`，已推送并回读main。Pro原文278582字节、SHA256 `6916df85160107a461bd6cb4789e502c53732814855e231322d97b26b87da2be`；交付ZIP SHA256 `c261d8dc5f614abd09a80f4969eff930ea9bee7e563c415a8862e1fd4de6b263`。四份替换文件与归档逐字节一致。

- 原生Linux release构建、fmt与Clippy -D warnings通过，窗口曾启动供用户查看，随后被用户否决，视觉验收未通过。
- Pro提供17张虚构数据HTML设计稿及五语窄窗/键盘验证记录，不能冒充原生截图或系统读屏验收。当前视觉合同见UI_DIRECTION.md。
- CLI测量器已修正，280次隔离启动零错误；新结果、正负对照与主机压力见PERFORMANCE.md及源码原始JSON，不将方法修正称为优化收益。
- 用户新目标为全面超越原版，全部93条需求保留；已继续与Pro协作真实Provider/流式网关/Codex可恢复接入闭环。当前未发布AUR/Release、未替换安装。

以下为较早快照和迁移历史；其旧pin、构建状态与布局不描述当前版本。

## 0.2.0 开发快照（2026-10-28）

独立源码仓已推送并回读 `main` = `4a967ec2eecb165630a7b246546bd3bd6362fc05`，主仓本次提交固定此 gitlink。下文 0.1.0 数据、旧目录与旧暂存状态属于历史记录。

- 本地 workspace 33 项测试通过，CLI clippy 全目标/功能无警告；配置模型目录、HTTP、安装项发现与隐私 fixture 在其中。
- GPUI 本地 release 构建通过并已启动供用户查看。为复用本机缓存，预览构建使用无 LTO、16 codegen units、mold；不将它等同于 CLI 的精简 profile 或各平台正式包。用户否决当前设计，要求调用 Pro 重做，视觉验收未通过。
- 当前 CLI 647,616 字节，SHA-256 `62a94f575cb0df69c9d2186c51ec2ccb04a58a3d7a8b6e340d0659e3f5115a7b`；0.2.0 原始 benchmark JSON 已随源码保存。CLI wait4 RSS 包括 exec 前测量器子进程，不能作为软件本身峰值。
- Android 四目标最新重建通过；ARM64 和 ARMv7 在 LG G820 / Android API34 真机运行并复验目录 JSON、错误路径，x86_64/i686 仅构建。隔离快照起止源码散列一致，设备临时文件已删除；完整本地结果在 `/tmp/magpie-android-0.2.0-u_e9c694/results.json`。
- Linux musl x86_64、AArch64、ARMv7、RISC-V64 曾在本轮较早源码快照构建，后三项经 QEMU 执行 CLI 和 HTTP；不把这一证据当作当前提交的原生硬件或 NixOS 验收。
- 打包脚本 Python 语法、workflow actionlint、desktop entry 校验通过；macOS/Windows 原生构建、AUR 发布、Release 安装包及系统替换安装尚未执行。

## 0.1.0 及迁移历史

# 来源与证据

- 迁移日期：2026-10-28；作者选择同仓 recipes/magpie/，保留当前未提交变更。
- 主仓库：https://github.com/LIghtJUNction/magpie-rs；HEAD `b4bcfe2d28a572ac4c04fd9efa852a6d99f7f428`。当前工作树的 `generated/magpie-rs-source/` 已是独立公开源码子模块，pin 为 `3d02311ac0c01c7dd75821745be62c88cbb82d4d`；父仓库 gitlink 尚未提交或推送。
- 历史 docs 提示词子模块：https://github.com/LIghtJUNction/magpie-prompts；commit `4694b5c8fd0ca66c090a99aca9d82313978b4192`。其原始软件提取基线是 `7b63feb908da1ca20f446b6b8584441e1c934825`。需求转写到 recipes/ 后，按用户要求移除了docs/子模块；原始文件指纹保留在下方历史表。
- 首次迁移使用当时本机 /home/lightjunction/Documents/GITHUB/recipkg/src 的 Python 标准库CLI，通过 PYTHONPATH 启动；当时生成extract检查单、init骨架并运行包结构验证。工具现已调整布局，本轮工具和技能来源见 MIGRATION.md；历史入口不作为当前可用命令。
- 首轮迁移的产品证据来自已有提示词和静态阅读；20张静态图片以带名称的缩略图联络表检查结构/视觉角色。该轮没有运行原产品或Rust软件。本轮新增的有限构建与基准证据见下文；真实Agent/Provider、完整协议和设备兼容仍未验收。

## 当前原型能力与目标区别

源码提供 help/version/status/config path、Codex发现与TOML只读预览（仅local/fixture-a、local/fixture-b；4MiB上限）、结构化错误stderr，以及 loopback 服务的 GET/HEAD /health。`serve --listen` 可指定 loopback 地址；服务默认4worker/8等待、512KiB请求线程栈、16KiB头部/64字段、2秒socket读写超时、1MiB声明请求体上限，尚不读取或转发请求正文。无 Provider/模型时 GET/HEAD `/v1/models` 返回合法空列表；其它 GET/HEAD `/v1/*`、`/v1beta/*` 回501；POST等其它方法回405。status中的not_started不证明另一个进程的真实状态。GPUI只展示静态窗口。

配置写入/所有权日志/恢复、已配置Provider与模型目录、协议转发、Usage、插件、Library、同步、TUI/Web/完整GPUI均未在当前原型完成；完整93条目标继续保留。缺失/错误源码行为只记为已知缺口，不作为降低规格的理由。旧会话中的cargo证据未重跑；本轮新构建、Clippy和格式检查命令及结果见下文。

## 本轮构建与性能测量（2026-10-28）

源码仓库：<https://github.com/LIghtJUNction/magpie-rs-source>，最终 pin `3d02311ac0c01c7dd75821745be62c88cbb82d4d`。基准运行时源码 HEAD 为 `c01f03ac855ff81626638ba5ddbd3c9e630996f4`；之后只更新了 README 与原始 benchmark JSON。源码树散列 `b8564ebac12324fbbf32b93bbccc2c5cf337f1b1ba3ceb288c8a2c1bbadeb9ec`，release CLI SHA-256 `76a375442a8dfe9bb5f29ff47a6cea60ca347d8b1effc89d68a497d1a483236c`，体积577,896字节。4KiB合成配置散列 `f459822bdc56001717b14158963aa4e09cb666f57af20fc28750bdc15938a9d7`；不含真实账号或凭据。

构建与静态检查成功：`CARGO_TARGET_DIR=/tmp/magpie-rs-bench-target cargo build --release --locked -p magpie-cli`、`CARGO_TARGET_DIR=/tmp/magpie-rs-bench-target cargo clippy --release --locked -p magpie-cli -- -D warnings`、`cargo fmt --check --manifest-path Cargo.toml --all`。没有运行完整软件测试套件。

CLI基准每种情况5次预热、30次实测，正确性错误0；网关基准30次启动（3次预热）、100次目录请求和60秒空闲内存观察，错误0。具体数值及限制见PERFORMANCE.md；原始结果在源码仓库 `benchmark/results/`，CLI与网关runner SHA分别为 `d2cdf5f6f2dd7912b3a69a39afde08528dc3b859fa3b6e9632fbae58a1e31b59`、`a803ac5a413bc3642a9678c2186947d3760f3c4772ba0d9e905156ce4be003dd`。环境为Arch Linux 7.2.11、x86_64、Intel i9-12900HX、24逻辑CPU、Rust/Cargo 1.99.0；空闲RSS/PSS中位数2976/779KiB。该证据只覆盖CLI原型与无模型的空目录路径。

## 内容转换

10份行为规格按责任分入core/gateway/interfaces；GLOBAL.md保留全局5条。仅改跨包阅读索引、实现语言覆盖条款，以及截图交付改为文字参考的REQ-100/104。所有93条REQ编号保留。原始未知项完整带入OPEN_QUESTIONS.md。配方不包含源代码、可执行程序、安装脚本、原图片或Base64。MIT许可保留在各包LICENSE.md。

## 历史源码快照SHA-256（首轮迁移时）

以下哈希记录首轮迁移观察到的平铺源码快照，不是当前子模块文件散列，也不是让重建者依赖这些实现。

| 路径 | SHA-256 |
| --- | --- |
| `.gitignore` | `09cc8cd0701a56e744c3f6988385854d13d49bdd08a4d3072bc2f11c1ab481e2` |
| `generated/Cargo.lock` | `353fb1803abb4ca420e4e91669f4021e76b6a1dc9cd571d29eb3744c96639481` |
| `generated/Cargo.toml` | `b22cbf888fe8eff0af817e8168ac544a6ef5ab980821952067fcc485c8cd71da` |
| `generated/apps/desktop/Cargo.lock` | `4f1badfbf618b5ee6e4733029464c145baea465d96d8ee23371603215633f01b` |
| `generated/apps/desktop/Cargo.toml` | `7d4d6b2259411f50b6061a923ddeea5e6173a3ae93ae9c234f62db14756462d1` |
| `generated/apps/desktop/src/main.rs` | `edad3d1ef5047b534456cafb81b909099dff3ff1130cdb411efe3812fcbd1838` |
| `generated/crates/magpie-cli/Cargo.toml` | `339e2a847efb3dd49b8c99bb5ea4e8f3df8f9a345697b665cd000b4a83d7e127` |
| `generated/crates/magpie-cli/src/main.rs` | `88c86a4195b6f2ebdd7a313b3de27ccc0fd28e5cd6e362003e7799691fda2a53` |
| `generated/crates/magpie-core/Cargo.toml` | `9418a45c2dfb17aa07c9f106495919e9015ade229a41d2f21743531707af1277` |
| `generated/crates/magpie-core/src/agents.rs` | `ccda4ec8e21020fefa1b86bf92c29955a4eb13b5622ca418f9477b3e07d23c37` |
| `generated/crates/magpie-core/src/codex_config.rs` | `41acbced4b7edbf612829c434217292d45786020a12c860f6232dd70d3c6ca32` |
| `generated/crates/magpie-core/src/gateway.rs` | `b530eca5a75c63c32c357135ed1d8f6900706348e16aa8a4ad8a4ec8910564c7` |
| `generated/crates/magpie-core/src/lib.rs` | `18d9f42fd96103150f0a46ed4e82a61ac47b4530c3c6888dc180f5c6e4277619` |
| `generated/crates/magpie-core/src/paths.rs` | `34b4d3b6bd93d818df4ae9de3ba874690fac5727aff2aafae5f269f50281b817` |
| `generated/crates/magpie-service/Cargo.toml` | `cf23f0a1c916056ff132928adbbb4167b1f06e01d4e14fb8e4519f2c0425abb0` |
| `generated/crates/magpie-service/src/gateway.rs` | `5ebf9ea1dd24d44baf60d3a4e111e751a5baac379e6a9c994139b8f33b801f19` |
| `generated/crates/magpie-service/src/http.rs` | `e7eff208876fe2c0587eab4ea234894b1ee0b39bbde336b022c5ec961595e73e` |
| `generated/crates/magpie-service/src/lib.rs` | `b4a28d9dd1c075994577a99fa150c6f003bec22bdb4f90c4ea240420c359ba5e` |

在该历史步骤中，17个生成源码/清单/锁文件移入 `generated/`，移动前后逐文件SHA-256相同。后续实现改动和独立仓库发布以本节“本轮构建与性能测量”所列 pin 为准。

## 已移除来源的历史指纹

以下提示词原始文件来自 docs 子模块 commit `4694b5c8fd0ca66c090a99aca9d82313978b4192`，其原始软件提取基线为 `7b63feb908da1ca20f446b6b8584441e1c934825`。用户要求移除 docs/ 后，工作树已不再保留这些文件；相应需求内容已迁入 `recipes/`。`.gitmodules` 哈希记录的是移除 docs 子模块前的配置文件。

| 历史路径 | SHA-256 |
| --- | --- |
| `.gitmodules` | `a7f8ec107733a25bfb8f26c0d8c70cd8265913dcd17b1f5bdd8d4c626f839fb7` |
| `docs/prompt/BUILD_PROMPT.md` | `6bedddfc1cb6ddc21b71d037d41ce5dd8b2e60e500bb34e2e3e56a9f3e738949` |
| `docs/prompt/specs/01-product-and-agents.md` | `8ec202dd29744b43be5b63b1d23f6f96e131c58ec86a2effb121ba592976e258` |
| `docs/prompt/specs/02-providers-and-accounts.md` | `622b002005999e16ed4df555e6adc8e8ecbdfd9db5a4f16dda21bbd29058987e` |
| `docs/prompt/specs/03-gateway-and-protocols.md` | `f189a922e5efd2ac3b1bfecc1487ceea446be157a70fd9c93198d8dd261e344a` |
| `docs/prompt/specs/04-routing-and-failover.md` | `2d861feaccefc1c448be6c87fba4397d99c922bb496a422773a1aea724d85cb8` |
| `docs/prompt/specs/05-usage-and-sessions.md` | `99b87b4076d92cafa929ef6ea9b54d1d79a7e2fa6a4dc5799cf8aa4fc1b3573e` |
| `docs/prompt/specs/06-plugins-and-tools.md` | `a011e730dec484d101d7de2a549aeb6b5b9fcfd1f3e17c984ff28fc259d581d4` |
| `docs/prompt/specs/07-library-sync-and-backup.md` | `96ef63012e5cd44e7b77c4d6242392c622bde438062655827bf90d48e6fc5f7b` |
| `docs/prompt/specs/08-cli-tui-and-web.md` | `c738936571aae7229e3300df9c2a36d7c6406283914cb71c0b1c80408e478f05` |
| `docs/prompt/specs/09-privacy-security-and-platforms.md` | `87f076d09e2454274a340ac96b5b34fa07715d33a55118c06424bcfd41cc69ee` |
| `docs/prompt/specs/10-visual-and-localization.md` | `465b680b55f232a021e54c1f890304773ab003bace068c8de83e4d2e5d2acf22` |
Git暂存索引条目摘要（git ls-files --stage stdout SHA-256）：`6066d3d7f0129c030358dc574ec07fc772d272812a4c27beca0310ceb7ad9092`。用户随后要求移除 `native/`、`internal/`、`docs/`、`build/`、`apps/`。按要求删除目录；取消 docs gitlink 与 `.gitmodules`，并将剩余受跟踪的 build/native 条目从索引移除。此前已暂存的旧源码删除保留；未提交、推送或改写其他暂存项。

迁移快照曾记录 `target/release/magpie` 为573,056字节、SHA-256 `171528903ba57f26156732c18f12fa2ad0fa3b12415cd0dfc957189e7e2f6e18`。这是历史文件，不对应本轮发布源码；当前可复现的CLI体积与散列见上文，均不代表完整产品安装包或内存预算。


## 按新指南重新迁移

本轮保留上面首次观察快照，重新比较其源文件和暂存索引指纹，没有源码漂移；新增内容与工具/技能的准确版本见MIGRATION.md。新增quality依赖是有效Markdown配方，生成源码布局和topic发布要求是交付合同，不声称已有生成实现或已发布。没有新增软件运行/性能验收结论。

## 原版完整对照与标准库合同（2026-10-29）

新增 ORIGINAL_PARITY.md 保存固定读取时的完整观察：本机原版 self-version 0.1.1948（不同于 pacman 包版本）、26 张官网图片来源/指纹、41 个页面操作、93 REQ/68 Agent 基线和30个额外或更细观察。帮助/静态截图均不等于原版业务实测；差异、无法核验项和原版源码/账号未读取的边界保留。

配方1.3.3复用固定design-directions及递归foundation，完整材料已读，具体作用/备选/验收见UI_DIRECTION.md。包含完整观察与设计依赖的归档已生成，离线check和完整rewrite提示词的检查分别记录。两标准库包当前pin没有独立LICENSE/COVERAGE，不编造其覆盖和授权。

Pro第三轮已回写27原版对照设计（Drive 1JmaVpvEunCXVnwLdny_Lz1Og0TMgbYEC）；它是设计合同，不能当原生Scene或用户认可。24观察和25完整上下文已更新同一文件ID并读回，25当前对应配方1.3.3。任务中途界面曾报告delivery timeout，已通过26续接现有工作并要求尽早回传代码检查点；33源码交付与实际合并验证尚未完成。历史30/31/32保留。该条记录时源码gitlink为dd5eed87b6d14213aeb604b50dd23d6d25a03f66，新锁实现与GPUI渲染工具尚未发布；最新结果见下方。完整功能与视觉验收继续未完成。

## 强杀恢复与原生 Scene（2026-10-29）

源码提交 `4f689fa0d268ed623f4cf1de94caa04ab14140e5` 已推送：Codex 事务使用系统锁，进程退出后内核释放持锁，保留同一 inode 的旧锁入口防止旧客户端并发写；未知旧锁仍不自动删除。隔离 SIGKILL 组件测试12项、事务集成测试48项通过，辅助进程入口按测试设计忽略；整工作区并行测试通过，Core Clippy通过。macOS/Android原生、Windows写入及非协作方最后一次检查与rename之间的竞争仍未通过。

新增可选测试 feature 导出实际 GPUI Scene，默认构建不启用；900×560 合成数据的旧r2界面已实际渲染，PNG SHA-256 `777d6aff0ec457e63c8e8e89a17ccd15b48a2d38ce7995eb8a0b43342cd23c56`。GUI组件59项及visual-test全target Clippy通过；Scene不是实际窗口输入/IME/读屏或用户认可，r2仍被否决。逐项证据见源码 `tests/results/crash-lock-and-native-scene-2026-10-29.json`。

## 依赖修复（2026-10-29）

源码提交 `2ae5b9a1cfaff04325642e9f288f5debfd8c9577` 将 Gateway 的 bytes 从 1.10.1 固定到 1.11.1，以包含 GHSA-434x-w66g-qw3r 修复。独立工作树、无账号合成 fixture 的全工作区测试101项通过，4个辅助进程入口按设计忽略；service 全target Clippy通过。该结果不宣称本产品的漏洞可利用性已证实、跨平台均已验收，GitHub远端告警重新扫描状态待观察。测试工作树已移除。证据见源码 `tests/results/bytes-upgrade-2026-10-29.json`。

## Agent 可执行文件候选发现（2026-10-29）

源码提交 `4c60f72e6a85a9b86dad6f2d81623a5b02b46bc4` 已推送。68 项目录接入 CLI/桌面共用发现入口，保留原先五项顺序（其中 Zed 是宿主），新增八项无歧义的可执行文件候选；同名候选与扫描错误单独报告，不将命中当成已连接或配置可用。Linux 合成 PATH 的 Core 测试118项通过、3个辅助入口忽略，Core 全target Clippy通过；其他 Agent 配置适配、真实系统及其他操作系统仍需验收。证据见源码 `tests/results/agent-discovery-2026-10-29.json`。

## 原版 CLI 隔离空状态观察（2026-10-29）

原版 `magpie 0.1.1948` 在无账户、无网络、独立 HOME 和空 PATH 的 46 次调用与隔离、退出码、复现方法记于 `ORIGINAL_CLI_BEHAVIOR.md`。`agents` 显示全部68项；`ls` 的空态行为随 Codex 目录存在性变化，sessions JSON及 usage CSV 有不同结构。仅为空状态证据，不证明修改、账号业务或重建兼容已通过。

## GPUI 自定义尺寸渲染工具（2026-10-29）

源码提交 `71d53e0a8823000e7494a280b6323b1200e4d0b9` 增加 `render_preview --size WIDTHxHEIGHT`；在隔离工作树基线 `2ae5b9a` 的旧 r2 UI 上，实际离屏场景生成 1440×900 一幅和 360×760 浅/深色六幅，并核对图像尺寸；越界输入退出1。测试夹具无真实账号，临时工作树已移除。旧 r2 已被用户否决，这不是 Pro r3、原生窗口交互或用户视觉验收。证据见源码 `tests/results/preview-viewport-2026-10-29.json`。

## 路由策略纯计算核心（2026-10-29）

源码提交 `4545f18e4617b4d5124004b16d81deea15f7684e` 包含有界组展开和七种候选选择策略；Linux 合成账户快照的36项测试、Core 全target Clippy和格式检查通过。无真实账户或网络；当前模块尚未接入网关、持久化、重试或用户界面，不能视为 REQ-040–045 完整验收。证据见源码 `tests/results/routing-core-2026-10-29.json`。

## 四协议转换与回环网关（2026-10-29）

源码提交 `cc22e7dacc5794d1b20765d5c1c383a8a734a5b1` 已推送；四协议转换接入 Gateway。Linux 合成回环 HTTP/SSE、工具调用、取消、错误脱敏及纯转换合计54项 service 测试，另有 Clippy 和格式检查通过。不支持无损转换的输入明确拒绝；完整官方字段、真实外部上游/账户、Provider 存储接线和其他系统未验收。活跃请求仍上限4、总超时120秒、空闲超时10秒，未宣称性能提升。证据见源码 `tests/results/protocol-gateway-2026-10-29.json`。

源码 `cc22e7dacc5794d1b20765d5c1c383a8a734a5b1` 的无显示 Linux 工作区随后统一运行 `cargo test --offline --locked --workspace --all-targets`：215 项通过，4 个辅助进程入口按设计忽略。该命令不覆盖独立桌面工作区，也不代表未合并的 Pro 版本或完整产品通过。

## 打包安全与短时转发基线（2026-10-29）

源码提交 `2bbdff72818a94c4ba4ae9e8e72b22cf8ce9ead2` 增加有界基准运行器、离线许可证库存及构建/输出隔离检查。13项打包和8项基准合成检查、本机 shell 语法检查通过；缓存中的Linux、Windows、macOS依赖文本与来源清单分别核验，系统库/SDK及许可解释仍有边界，不能推断真实安装包合规或可用。AUR 未发布、安装事务和多平台包未验证。短时基线及旧版运行器字节未保留的限制见 `PERFORMANCE.md` 与源码 `benchmark/results/forwarding-2026-10-29-short.json`；其他证据见 `tests/results/packaging-and-benchmark-2026-10-29.json`。

转发基准对源码 `2bbdff7` 二进制首次尝试时因网关健康信息新增字段被基准程序的整对象比较误拒绝（`candidate_admission_limits_changed`），未进入负载阶段；失败原始记录保存在工作区外。源码 `0f1a8f74615858a60f1ac6a9cae4a52b72f2940c` 修正为验证三个实际准入值并允许额外字段，8项基准合成检查通过；后续真实短测另行记录。

修正后的已提交基准 runner `0f1a8f7` 对本机从源码 `2bbdff7` 构建的 release CLI 完成新一轮短测，正确性为 true、失败0。二进制与runner散列、每格数据、采样范围及不可推断的事项保存在源码 `benchmark/results/forwarding-2026-10-29-current-short.json`；原始带主机遥测记录保留在源码树外。仅为单次短时参考。

## 原版 CLI Agent 空状态（2026-10-29）

源码 `241388874b0b7051c5e441daa0b2f725192c2c86` 已将隔离原版观察用于实际 CLI：无候选时 `agents` 的68行输出、空 `ls` 退出1及 Codex 仅有状态目录的提示通过进程 fixture；`agents --json` 的既有空结构不变。Linux CLI 全target测试27项通过、1辅助入口忽略，Clippy通过。真实已安装 Agent 的原版非空显示和其他命令仍需重建，未测试其他系统。证据见源码 `tests/results/original-cli-empty-2026-10-29.json`。

源码 `241388874b0b7051c5e441daa0b2f725192c2c86` 的 Linux 无显示工作区随后统一运行 `cargo test --offline --locked --workspace --all-targets`：235 项通过、4个辅助入口忽略；不包括独立桌面工作区或尚未合并的 Pro 版本。

## 旧网关配置协议回归修复（2026-10-29）

此前扩展四协议后，未指定上游协议的旧配置错误地将 Messages 发往上游：新增回归先收到502、预期501，原 first-loop 仅25/26通过。源码 `812756d81253c3a798d328bd99fb6209fa37270c` 恢复旧入口限制，合成 fixture 验证 Messages/Gemini 拒绝且零上游请求；service 55项、严格 Clippy 与单次26项 first-loop TCP回归通过。另有早先报告的首个 SSE 失败事件响应头时序不确定，单次通过不代表其已修复。证据见源码 `tests/results/protocol-legacy-guard-2026-10-29.json`。

## 外部构建路径判定修复（2026-10-29）

Linux 上空的祖先 `.git` 目录曾使合法外部缓存路径被误拒绝。源码 `dd35372e5e603b32aa670f5ce7f29b6a6346d8bb` 将打包和基准共用的判断改为验证实际 Git 元数据，保留符号链接及无法判断时的拒绝。合成真实仓库/子模块、工作树、裸仓库与异常情况 14 项、打包 13 项和基准工具 8 项通过；证据见源码 `tests/results/external-paths-2026-10-29.json`。未验证 Windows/macOS 或旧 Git；路径检查与后续 I/O 之间的祖先路径替换竞争仍存在。

## 原版 Agent 合成候选列表（2026-10-29）

源码 `72358f5d97cd109809d37eab82fa521681aeb0e7` 在已观察 Linux 空配置条件下，按原版 CLI 的字节展示 Claude、Codex、Gemini 的单独和三者同时存在场景；`agents` 68 行顺序、尾空格和 `ls` 状态/路径/退出码均以隔离原版样本为准。CLI 36 项通过、1 个由父测试调用的 helper 忽略，fmt 和严格 Clippy 通过；合成未观察场景及其他命令与上一 Rust 基线 245 次输出/退出码对照一致。原版观察见 ORIGINAL_CLI_BEHAVIOR.md，运行证据见源码 `tests/results/original-agent-candidates-2026-10-29.json`。两两组合、真实配置/账户、其他平台以及完整 Agent 功能未验收。

## SSE 首事件错误导致响应头丢失（2026-10-29）

原始代码可在 body 首次 poll 就报错，Hyper 在发送头之前结束连接：合成原 test06 30次中1次 RemoteDisconnected，确定性模拟在错误时写出0字节。源码 `c1caccb47b683b427cb53d56a0827e352c2137b4` 等待实际 I/O flush 后传播 SSE body 错误，不注入事件、不伪造成功，也不泄露上游错误正文。主源码的 service 56项、严格 Clippy、first-loop 26项、同二进制重复原错误测试1000次和640请求错误矩阵均通过；旧失败记录仍保留。可重放脚本和输入散列见源码 `tests/results/sse-header-flush-2026-10-29.json`。本轮仅 Linux 合成 loopback；Pro Provider 合并版本、真实 TLS 和其他平台尚未复验。

## SSE 修复后转发短测（2026-10-29）

源码 `c1caccb47b683b427cb53d56a0827e352c2137b4` 构建的 release CLI SHA-256 为 `69aa485d9cb58a1ccb1c93fb84174541daad755325eef59d4ff879831b12ea94`，3,682,928 字节。Linux 合成 loopback，12格各5秒、长流4秒：3150次成功、1156次按并发限制拒绝、0错误，另外取消5次；16并发 SSE 成功12/拒绝36。1256次采样目标 RSS 最大6020KiB、swap 最大3612KiB，采样间隔 p95约110ms。启动负载约10.2/24逻辑CPU，客户端每秒最多20轮，非容量或完整产品验收；无交替A/B，不得声称提速或退化。可复核汇总见源码 `benchmark/results/forwarding-2026-10-29-sse-flush-short.json`，含主机遥测的原始结果留在仓外。

## Codex 锁描述符释放（2026-10-29）

源码 `00dc72411d82891d4006548c6b31fb11b3f6ac5a` 在正常结束事务时显式释放 OS 锁，再关闭描述符。合成测试复现原 guard 关闭而共享描述符仍在时下一次误报 LockBusy；修复后同一保留副本不再阻止新 guard，持锁期间排他仍有效。修复文件与独立合并版字节一致，后者 core 8线程10轮各74项通过、完整工作区8线程289项通过；详见源码 `tests/results/codex-lock-release-2026-10-29.json`。Pro 原始并行失败没有锁持有者现场，原因不能确定为该路径；进程被强杀时 Drop 无法执行，遗留的继承描述符仍可使锁持续到关闭。仅 Linux 合成 fixture 验证。

## 两两 Agent 候选及默认路径（2026-10-29）

源码 `f20439bcc03d527f8c713b1b22c066604e16ba7a` 按40次原版合成样本补齐三种两两候选、默认 Codex 路径和目录缺失/空目录的输出，renderer/进程字节均对照。主源码 CLI 38项通过、1 helper 忽略，fmt和严格 Clippy 通过；独立样本的611组对照保留此前不适用分支行为。证据见 `ORIGINAL_CLI_BEHAVIOR.md` 和源码 `tests/results/original-agent-pairs-default-2026-10-29.json`。两两候选加空 Codex 目录、真实配置和账户仍未验收，Pro候选合并后的完整 CLI 回归待重跑。
