# 10 · 视觉、交互与本地化

## REQ-100：视觉方向

- 优先级：一般。使用 Magpie 公开参考界面作为产品信息架构与视觉方向：清晰、轻量、信息密度适中，列表与状态可以迅速扫读。本配方的静态界面文字参考见 `UI_REFERENCE.md`；原图不进入 Markdown 配方。
- 照片/屏幕截图只作参考，不是运行证据、业务数据或产品资产授权以外的背景；样例 Provider/accounts 用虚构/占位数据，重建界面使用自身真实本地数据。
- 浅色/深色主题均可用，颜色对比适合状态/文字；交互元素 hover/focus/disabled 有明显差异，采用系统 reduce-motion preference。字体可自定义，保留多语言/等宽代码可读性。
- 正常验收：Providers、Routing、Usage 等同一主题的面板、标签和状态色具有一致的层级。
- 失败验收：暗色主题下保留背景的 SVG/icon 或低对比状态不可辨识时提供合适的 variant。

## REQ-101：App 和紧凑 Panel 布局

- 优先级：一般。桌面主窗与快捷面板共享设计系统。宽屏使用侧边导航；窄屏使用抽屉/折叠导航并保留主任务；快捷 panel 适配约 440 px 和 360 px 宽度。
- Panel 内的 quota refresh、Provider/account 状态和额度重置时间必须有文字标签与无障碍名称；不能只用裸图标表达。焦点可见、弹层可键盘退出，刷新完成不把光标丢到页面顶部。
- 页面加载、保存、刷新、错误、空态和 pending 状态位置一致。表格行和卡片展示重要身份信息，别用颜色单独表达失败。
- 正常验收：宽窗、窄窗、360px panel 都可查看 account 名称、quota reset 和刷新控件。
- 失败验收：错误长文本不会覆盖刷新按钮或阻止用户关闭/返回。

## REQ-102：语言

- 优先级：一般。UI 提供英文、简体中文、繁体中文、日文和德文；可手动切换或按系统语言选择。缺失翻译退回英文。日期/数字使用选择的 locale 和本地时区。
- 所有用户可见字符串外置翻译；变量插值、复数、路径/代码/用户原字符串不翻译；长中文/德文文字不能截断关键按钮。
- 语言切换即时重绘页面并保留 form state/focus；页面重启后保持用户选项。
- 正常验收：switch locale 后 UI 和主要状态提示一致，不混入半个未翻译 key。
- 失败验收：未知系统 locale 选择英文且不让启动时崩溃。

## REQ-103：可访问性与操作反馈

- 优先级：一般。可键盘操作、语义化按钮和输入、合理 Tab 顺序、可读 screen reader label、对比度/缩放、尊重系统 reduce motion。错误消息提供可操作文本；更新中控件标记状态。
- 状态 badge 不仅依赖红/绿，quota reset、key hidden、route status 有可读标签；hover-only 信息可键盘读取。
- 正常验收：全键盘可添加/保存 Provider 与关闭 dialog；没有 mouse-only 动作。
- 失败验收：后台异步更新状态时不会突移焦点/丢失用户当前输入。

## REQ-104：参考截图与产品数据

- 优先级：一般。视觉参考资产包括 Agents、Panel、Picker、Providers、Routing、Usage、Nested Routing、Masking、Intent Rules/Trace、Add/Import 和 Font settings 的公开静态截图；对应界面的文字层级、视觉角色及原始图片 SHA-256 在 `UI_REFERENCE.md` 列出；文字参考是本配方随包交付的替代材料。
- 图中的测试数据不能当作默认账号或线上状态；重建示例若需要数据则使用明确标注的 demo fixture，并与用户本机数据隔离。
- 正常验收：使用 `UI_REFERENCE.md` 的 Providers 与 Routing 文字层级作为参考，重建使用独立占位名称。
- 失败验收：文字参考缺失或矛盾时报告材料问题，不依赖原仓库图片或网络盗链；不得以下载原版资源代替设计。
