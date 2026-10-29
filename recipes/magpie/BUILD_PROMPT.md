# 重建 Magpie

实现跨平台 AI 编程 Agent、Provider、账户、模型目录、网关、路由、Usage、Library 和同步管理软件。交付 CLI 与 GPUI 桌面应用，并完成规格中的托盘、Web、TUI 模式。现有合同包含 93 条 REQ 和 68 类 Agent，是必须保留的基线；针对选定原版发现的额外能力继续补充，不把该数量当作功能上限。不能把当前 Rust 原型的能力当成完成范围。

## 用户已确认的要求

- 与原版 Magpie 的完整功能一比一还原重建：全部页面、68 个 Agent、Provider/账户/模型、协议网关、路由、Usage/会话、插件、Library/同步/备份、CLI/TUI/Web/托盘及设置逐项对照。Rust/GPUI、低内存和跨平台是实现约束，不构成删减功能的理由。当前只有 Codex 的有限闭环，未实现项保持待完成；按钮、截图、目录和“只读”占位不能代替实际行为。
- 主实现语言 Rust；GUI 必须使用 GPUI。不得自行改成其他语言或以 WebView 替代桌面框架。平台工具、外部 runtime 和 FFI 可作为必要前提，需说明用途。
- 本体与内存占用在满足全部功能后尽可能精简；避免 CLI 链接 GPUI、默认常驻无用服务、无界缓存、完整请求/流的重复缓冲。不要以删需求实现精简。
- 平台：Linux（含 Arch Linux、Nix/NixOS）、macOS、Windows、Android/Termux，兼顾 headless、容器/NAS、WSL；CPU 尽可能广。至少规划 x86_64、AArch64、ARMv7、i686、RISC-V 64 的适用运行模式。最低 OS/设备版本未知，发布时按工具链和依赖明确支持矩阵。
- Android CLI/headless 和 Android 原生 GPUI GUI 必须分开标记；当前固定 GPUI 没有 Android 平台后端。调查并记录原生 GUI 可行性，不得伪称 Android 桌面后端已存在，也不把这个缺口隐藏为整体完成。
- 清晰、轻量、适中密度的任务界面；深浅主题、可读状态、键盘操作。根据真实工作流设计，避免无关 KPI 大卡片和通用模板页面。截图的顶部标签页与文字规格的宽屏侧栏存在差异：以 REQ-101 的响应式导航和任务可达性为准，不要求逐像素复刻。
- 桌面页以紧凑 Agent 行、当前模型和可执行动作组织；配置路径、底层状态与诊断按需展开，不能用一整页原始字段替代产品界面。先对照已有 Magpie 视觉参考检查排版、间距、颜色、宽窄布局和实际渲染，再进入桌面发布；只显示真实数据和已实现交互。
- 用户已否决此前名录/详情式视觉，要求 UI 不得逊于原版并由 Pro 重新美化。以清楚的 Agent 行列表、行内选模与真实动作作为日常主任务；连接设置和诊断按需打开，保持窄窗任务可达与焦点恢复。按稳定 ID 保留选择和复制对象；缺失字段显示“未设置”，不推断为默认模型或在线。具体对照和视觉验收见 UI_DIRECTION.md；完整产品各页与导航要求保留。
- 沿用英文、简体中文、繁体中文、日文、德文；英文 fallback、本地时区及 locale 日期数字，按 REQ-102 验收。没有新增 RTL 要求。
- 同仓布局，根配方 recipes/magpie/；保留当前源码和 Git 改动。交付归档不包含源码、截图或原项目调用包装器。

## 阅读顺序与递归合同

1. 本文、GLOBAL.md、DEPENDENCIES.md、PERFORMANCE.md、OPTIMIZATION.md、MIGRATION.md、GENERATED_LAYOUT.md、PUBLISHING.md、ORIGINAL_PARITY.md、ORIGINAL_CLI_BEHAVIOR.md、ORIGINAL_WEB_BEHAVIOR.md、PARITY_VERIFICATION.md。
2. 依赖 `interfaces`：完整阅读其 BUILD_PROMPT.md、ACCEPTANCE.md、specs/08、specs/10、UI_REFERENCE.md。
3. `interfaces` 的依赖 `gateway`：完整阅读其入口、验收及 specs/03、05、06。
4. `gateway` 的依赖 `core`：完整阅读其入口、验收及 specs/01、02、04、07、09。
5. 依赖 `quality`：完整阅读 `.deps/quality/BUILD_PROMPT.md` 与 ACCEPTANCE.md，将设计、代码组织、错误/取消处理、性能测量要求应用到所有软件组件；它是质量合同，不是运行时库或另一个服务。项目明确的 Rust/GPUI、任务视觉方向和更具体的性能合同优先。
6. 依赖 `design`：完整阅读 `.deps/design/BUILD_PROMPT.md`、DIRECTIONS.md、EVALUATION.md、ACCEPTANCE.md 和 SOURCES.md，再阅读其 `.deps/foundation/` 的全部设计合同。按 UI_DIRECTION.md 中的产品取舍应用到 GPUI 主窗/面板、Web 和 TUI；CLI 采用适用的信息层级、文案与反馈部分。它们是设计与验收合同，不是运行时库；原版行为、用户指定 Rust/GPUI 和既有视觉基线优先。
7. 根 ACCEPTANCE.md、COVERAGE.md；EVIDENCE.md 与 OPEN_QUESTIONS.md 解释来源及未验证边界，不得当作既有实现成功证据。

归档内路径分别是 `.deps/interfaces/`、`.deps/interfaces/.deps/gateway/`、`.deps/interfaces/.deps/gateway/.deps/core/`。作者目录里按各 RECIPE.md 的相对 source 找到同名材料。产品软件依赖都要实际重建并集成，不得只放占位 API；quality 和 design（含递归 foundation）依赖应落实为可检查的质量/设计要求，不生成占位程序。具体模块组织可调整，但数据、授权、账务和配置所有权必须只有一致的事实来源。

核心产出 Agent/Provider/route/settings 状态；网关使用该状态执行鉴权、路由、转发、计量与插件；所有界面调用同一状态/服务。集成通过标准：CLI 改模型后 GPUI/TUI/Web 看见相同状态；请求按所选模型实际到本地 fixture 并有一次最终 Usage；断开只恢复 Magpie 拥有的字段。

## 冲突与交付

用户约束优先于旧 GLOBAL.md 的实现自由度。其余行为以对应编号规格为准。文字视觉参考替代图片交付。未知参数和外部协议版本先确认或标为未验证，不猜常量。GLOBAL.md 中的模型例子是固定历史样例，不表示当前供应商承诺。

在此仓库继续开发时，生成实现源码位于 `generated/magpie-rs-source/` 固定提交的 Git 子模块中。先核对子模块 remote、pin、工作区状态和 EVIDENCE.md，再阅读并复用现有 Rust 原型，逐项对照 93 条 REQ；不能把原型状态当作需求已完成。具体工作约定见 GENERATED_LAYOUT.md。`generated/` 根目录只放源码仓库子模块，不放平铺源码；源码、缓存和运行结果都不写入 `recipes/`，配方归档只能包含配方 Markdown 材料。

检验配方能否独立重建时，在独立目录只根据归档内完整配方、递归材料及明确的外部运行前提生成实现；不得读取或复用本项目的生成源码、原版二进制或历史实现。之后可将生成的软件与固定原版在隔离合成输入上对照，原版只作为验收参考，不作为生成实现的运行时依赖。独立重建通过与现有原型通过分别记录。

在该源码子模块内交付可复现构建说明、运行/安装方式、平台能力矩阵、93 条 REQ 状态、自动化/人工验收和资源测量记录。Nix/Arch 包装也要给可重复安装/卸载流程。引用外部工具不意味着已经把它打进配方。迁移与 Recipkg 解析成功不证明生成软件已完成。

## 发行渠道

用户要求多个子 Agent 并行迭代，并授权将 `magpie-rs-bin`（固定发行二进制）和 `magpie-rs-git`（源码构建）发布到 AUR，然后在本机安装其中一个版本。两个变体提供 `magpie` CLI，与彼此以及原 `magpie`、`magpie-git`、`magpie-bin` 包互斥；保留用户配置，不用包安装脚本迁移或删除登录材料。Linux 桌面入口使用独立的 Magpie Rust 名称并实际运行 GPUI。

在主项目 GitHub Release 发布多平台安装包：Linux 系统包、macOS 安装包、Windows 安装器，并对适用架构提供 CLI 归档；Android/Termux 的 CLI 与 GPUI 原生 GUI 能力分开标注。每个包标明版本、CPU/OS、最低前提、CLI/GUI内容、SHA-256、对应源码提交和安装/卸载方法。尚未完成全量功能时使用明确的早期预发布说明。构建成功、公开附件、AUR索引可见、本机安装、设备执行分别核验，不把单一状态视为全部完成。

当前界面方向与具体响应式、状态/焦点规则见 [UI_DIRECTION.md](UI_DIRECTION.md)；完整功能、导航和平台合同继续有效。
