# 新版迁移指南对照记录

## 已有选择继续生效

| 问题 | 本次采用的答案 |
| --- | --- |
| 语言 | Rust主实现，GPUI桌面；不允许无声改换主语言/GUI框架 |
| 产品目标 | 完整93REQ的Agent、Provider、网关、路由、Usage、插件、Library、同步和多入口管理；低内存、精简本体不删功能 |
| 审美 | 真实任务的信息层级、适中密度、深浅主题、键盘/窄屏；沿用文字视觉参考，不套通用dashboard |
| 国际化 | en、zh-CN、zh-TW、ja、de；英文fallback、locale数字日期及本地时区；没有新增RTL要求 |
| 平台 | Linux/Arch/Nix、macOS、Windows、Android/Termux、headless/容器/WSL；CPU广覆盖。最低版本/真实设备未知 |
| 性能 | 用户给出低内存/精简目标；绝对预算未知。2026-10-28已测CLI与空模型目录子集，完整负载和A/B对照未完成 |
| 布局 | 用户明确选择同仓recipes/magpie；`generated/` 是容器，Rust源码位于独立子模块 `generated/magpie-rs-source/` |

## 本轮程序与证据边界

已重新读取个人安装技能，并发现它仍是旧单语文本。按用户随后“更新技能”指令，使用canonical Recipkg技能源及CLI skills sync生成的同内容文件，更新个人migrate-to-recipkg英文/中文两份。没有手工改写上游指令，没有安装暂存目录中的其它技能。

canonical英文指南新增双语extract、search、复用有效配方、发布recipkg topic及generated实现布局。中文指南也增加topic与generated约定。本次同时审阅两份，保留两者共同合同，英文中的进一步流程约束也纳入检查。

迁移期间相邻Recipkg仓库的CLI先后位于generated/recipkg-cli/src、generated/python-reference/src、generated/src。前两次路径失效时明确报No module named recipkg，未将失败记为校验成功。最终从当时可用generated/src/recipkg固定校验工具到交付目录tool-runtime，之后仅用该快照运行最终校验；不修改相邻仓库，不恢复其删除源码。工具快照不进入Magpie归档。下面是实际使用文件哈希，不把父仓库HEAD冒充这些未提交文件的完整快照。

已使用extract --lang zh-CN重新生成并阅读观察检查单。复用现有有效四个Magpie配方，无须init覆盖已有目录。使用search software-quality --limit 5得到“No matching repositories”；不把搜索不到当作不存在，随后直接检查canonical仓库中有效software-quality配方的三份Markdown并按本地快照复用，来源见该包SOURCE.md。

在首次重新迁移阶段，原型能力/缺口只按静态源码记录；当时未运行原软件、Rust原型、真实Agent、外部Provider、性能基线或设备验收。10份规格和93REQ正文、五语/视觉与安全边界均保留。该阶段的发布和性能状态为历史快照；2026-10-28后续实现与测量见本文件末尾及EVIDENCE.md。

## 技能与CLI指纹

| 文件 | SHA-256 |
| --- | --- |
| `canonical migrate-to-recipkg/SKILL.md` | `c4dc34d0aa8cea092945fe1d53283f69a3dd69b973825437291b0ec726d7f126` |
| `canonical migrate-to-recipkg/SKILL.zh-CN.md` | `d2f63701a03e92fa386af8e4b1869b5cbd03d7a94995420ea4e3a59821eb2f25` |
| `tool-runtime/recipkg/cli.py` | `f95b6da31281782b55951c85021fdfcc66d8834cd0f433c4a675c87f51da4d03` |
| `tool-runtime/recipkg/package.py` | `47c3cd2e1239ff7646b9080b84491f6eccae2da183409bd275f7961a1a278cbe` |
| `tool-runtime/recipkg/sources.py` | `8a0ab63b5dea1e9a2efda9c2e627eb79fda88e634c5b3a7829f913c826bba493` |
| `tool-runtime/recipkg/skills.py` | `9e4caeafa043b728957020424d17668b5785365fee4f4270e5ebd6643daa4cd9` |
| `tool-runtime/recipkg/search.py` | `875c19afde7339559793ca235065951421fd9ffb65a4b7fa2a2c7e62582993d3` |

工具父仓库观察HEAD：`c1300de130b297445c92ebefeebe7817d816b491`。

Magpie观察HEAD：`b4bcfe2d28a572ac4c04fd9efa852a6d99f7f428`。

个人安装技能先由CLI同步；之后按用户要求补充归档只作临时校验，以及同仓时generated/位于项目根并与recipes/并列。当前个人双语版本因此包含本地流程约定，不再与canonical逐字节相同。技能同步仅涉及migrate-to-recipkg，未安装rewrite-to-recipkg或test-feedback。

## 最终指南增量

最终核对上游技能再次更新，已按最新正文改为直接generated/承载生成源码，不再套实现名称目录；主仓库保持magpie-rs，不追加-recipkg。重新用CLI sync生成并同步个人双语技能，哈希表已记录最终版本。为避免旧工具资源产生旧布局说明，仅在外部工具快照中从canonical源刷新技能/模板Markdown，工具Python源码未改写。原始工具快照来源与最终资源哈希分别记录在交付目录审计文件中。

## 按最新版纠正源码目录

用户指出 `generated/` 应实际存放生成源码。复读当前 Recipkg 中英文技能后，确认同仓布局下实现源码应在项目目录的 `generated/`，而 Recipkg 配方负载必须保持 Markdown-only。按此将根目录的 Cargo workspace、CLI/core/service crates 和 GPUI app 的 17 个源码、manifest、lock 文件移到 `generated/`，逐文件 SHA-256 一致。Cargo 忽略的构建缓存保留原位；配方归档范围不含 generated。没有改源码内容、提交或创建 gitlink。

随后用户明确要求移除旧的 `native/`、`internal/`、`docs/`、`build/`、`apps/`。这五个目录已删除；docs 子模块与根 `.gitmodules` 一并移除，Git 暂存索引同步取消该 gitlink 并删除剩余受跟踪的 build/native 资源。`recipes/` 与 `generated/` 保留。Recipkg canonical 中英文迁移技能及本机安装版均已更新：明确源码实际归入根 `generated/`、按用户指定清理旧源码目录、更新子模块引用，并仅在用户要求时保留归档。

## 按最新版生成目录规则完成迁移（2026-10-28）

重新读取 canonical `migrate-to-recipkg/SKILL.md` 与 `PROJECT_SKILLS.md`。当前规则要求 `generated/` 仅作为容器，实际源码必须位于仓库与目录同名的独立 Git 子模块。用户授权新建并发布源码仓；据此创建公开仓库 <https://github.com/LIghtJUNction/magpie-rs-source>，默认分支 `main`，最终源码 pin 为 `3d02311ac0c01c7dd75821745be62c88cbb82d4d`。旧平铺实现已移入该仓，代码/manifest/lock 内容经目录比对；补入 README、MIT license、忽略规则和基准说明，更新并保留原始基准 JSON。父仓库 `generated/magpie-rs-source` 已成为 mode `160000` 子模块；父仓库自身仍有既存暂存删除与其他本地变更，gitlink 尚未提交或推送。

根配方、11个 canonical Recipkg 技能 gitlink 和源码子模块分别保留。`apps/desktop` 是源码仓库内的 GPUI 实现目录；根 Recipkg 仓库的 `generated/` 只含命名子模块，不含平铺源码、build、site 或 target 产物。

本轮新增 `GET/HEAD /v1/models` 的空目录响应，并允许 CLI `serve --listen` 指定 loopback 地址。Linux release build、Clippy、fmt检查通过。CLI与空目录网关基线的 workload、硬件、散列、限制见 PERFORMANCE.md 与 EVIDENCE.md；尚未完成Provider转发、真实模型目录、GPUI交互、其他平台或CPU验收，也没有足够交替轮次证明性能优化收益。

该源仓是源码发布；父 `magpie-rs` 配方改动仍是本地工作树状态，没有提交或推送。

## Recipkg结构校验

本轮最后一次校验：`recipkg check recipes/magpie` 返回 `OK magpie@1.2.1 (software)`；临时 `recipkg pack` 成功，随后对生成归档再次 `recipkg check` 也返回 `OK magpie@1.2.1 (software)`。`recipkg build <archive> --lang zh-CN` 成功渲染完整提示词，包含93条需求、子模块布局和性能合同。归档共47个条目，全部为 `.md`，没有源码子模块、Git元数据、二进制或构建产物。临时归档和提示词在 `/tmp` 校验目录，完成后删除。

结构校验不证明93条需求、完整软件实现、硬件兼容或性能预算通过。主仓库配方尚未发布；`recipkg` topic 保持在 PUBLISHING.md 中的发布前待办状态。


## 本次授权提交与源码更新

按用户“先提交推送”授权，保存既有旧源码/文档清理和完整配方，源码更新固定到 `4a967ec2eecb165630a7b246546bd3bd6362fc05`。源码仓已经推送，主仓本次提交包含对应 gitlink。`generated/` 仍仅作为子模块容器，配方归档不带生成实现。UI 已被用户否决并委托 Pro；AUR、Release 和替换安装留待后续完成，不将构建配置或源码推送写成发布成功。
