# 验证证据

## 当前业务与桌面操作快照（2026-10-29）

当前源码 pin 为 `4ff1e55a399bf0946a8afa9abffc33ea0ae83f8e`，已推送并回读远端 main；父仓库固定此 gitlink。子模块另有尚未提交的 benchmark/packaging 工作，未包含在该 pin。

- bounded loopback HTTP Provider 已真实转发 Chat/Responses JSON及SSE，支持模型重写、固定脱敏错误、有界并发/正文/事件和取消；外部HTTPS、跨协议转换等仍未实现。
- Codex七字段事务支持只读预览、显式保存、归属重读和离线恢复；不变时恢复原字节，漂移时保留外部字段。私有暂存/日志有界，Linux/macOS/Android写入路径已实现，Windows明确禁写；真实断电及全部设备场景未验收。
- 根 workspace 在应用桌面快照适配后77项通过、2个worker入口由父测试实际调用；Clippy、fmt、rustdoc和release通过。26项真实TCP/启动/CLI用例通过，安装版Codex0.165.0经合成本地Provider收到中文Responses流并恢复原配置；原始证据在源码 `tests/results/`。
- GPUI actions r1 已原生release构建并通过全目标Clippy、59项组件测试。新增ignored worker由Python显式运行两次：已有配置和原来不存在的配置，两场景均通过真实ActionsState→CLI bridge→CLI→Gateway→Codex请求，停止Gateway后恢复原字节/不存在；8项协调器自检通过。原始结果为源码 `tests/results/desktop-actions-2026-10-29.json`。这是无窗口业务联调，不是原生窗口/IME/读屏验收。
- CLI release为3,271,792字节，SHA256 `e3463d7717d0f8a73b5ea6d63d363e9835ce1c81dc0afbdb5c83bbf3696b86f1`；不将其与旧空目录版本或完整原版直接比较性能。
- 当前业务的Android四ABI API23 release构建通过；LG G820上ARM64/ARMv7真实JSON/SSE转发通过。设备配置事务因`/data`祖先0771权限保护而在prepare阶段拒绝，未执行apply/restore。外部完整证据在 `/home/lightjunction/.cache/magpie-rs-build/android/validation-20261029-a7f29c61/results.json`；构建业务闭包的根Cargo与crates共21文件与当前逐字节一致。x86/i686无设备执行；旧musl/QEMU结果不冒充当前版本验收。
- 用户已明确否决此前Pro暖白/深墨绿与名录/详情方向，并要求不得逊于原版。新任务22-visual-parity-request revision1已发Pro，对照原版浅/深截图并行做视觉、字体和独立审查。视觉未通过，旧窗口和HTML预览不构成新方案验收。
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
