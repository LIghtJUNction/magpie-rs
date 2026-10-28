# 重建 Magpie 用户入口

实现 specs/08-cli-tui-and-web.md、10-visual-and-localization.md，完整阅读 UI_REFERENCE.md 与 ACCEPTANCE.md。桌面主界面/托盘使用 GPUI；CLI、本地服务、TUI 和 Web 的行为按规格保留。

## gateway 依赖合同

依赖 `gateway` 位于归档 .deps/gateway/，包含协议/Usage/插件和它的 core 依赖。先完成它们的真实状态与操作，所有入口使用同一保存与授权路径；禁止为 UI 增加独立的 Provider/路由/额度真值。集成验收：CLI 创建 Provider 后 GPUI/Web/TUI 显示相同数据；端口冲突显示失败；一次请求与 Usage/route 页面和 API 对应。

## 呈现与能力

Rust 桌面 GUI 必须为 GPUI；CLI 可单独构建运行，不依赖显示服务器或 GPU。桌面正常启动服务、headless serve 不打开 GUI。完整应用无参数 GUI 与无桌面 CLI 分发的启动行为需明确，不得让服务模式意外打开窗口。

保留 Agents/Providers/Gateway/Routing/Usage/Sessions/Library/Plugins/Settings 导航、紧凑 panel、picker、表单、空态/加载/失败/保存反馈。正文写真实用户可理解的状态，不在产品流程露出内部构建/线程/依赖细节。可用性来自实际探测，未知不显示为成功。

支持 en、zh-CN、zh-TW、ja、de；系统 locale 未识别回退英文，切换保持输入和焦点，使用本地时区与 locale 日期数字。深浅主题、键盘、360/440px panel、宽窄布局、screen reader label、reduce motion 按规格验收。视觉由文字参考重建，不要求外部图片。

CLI stdout 只输出命令结果/所选结构化格式；错误 stderr + 稳定非零退出码，敏感参数不回显。Web 登录 key 与 Gateway key 分离，外部入口提供相同授权与错误语义。

平台标记按实际能力：Linux/Arch/Nix、macOS、Windows 桌面；Android/Termux 先有真实 CLI/headless。GPUI 原生 Android 后端是明确未解决项；不要用手机浏览器访问 Web 冒充原生 GPUI。跨平台编译不等同打包安装/真实运行。
