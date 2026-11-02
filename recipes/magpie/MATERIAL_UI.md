# Material 原生前端与网关操作

用户于 2026-11-02 指定 gpui-kit-material，并指出现有功能过少、UI 尚未重构。本轮将它作为实际 Rust/Cargo 外部依赖，不只复制配色或绘制效果图。已有 design/quality 递归配方继续适用；组件库不是 Recipkg 软件配方。

## 依赖与兼容

- Material 来源：`https://github.com/mizorewww/gpui-kit-material`，审阅提交 `47895b92558883d24e19d54fdeb876ed551d9394`，MIT AND Apache-2.0；处于 0.1 预发布阶段，不能宣称组件已达到其上游生产验收标准。
- 按该提交的 consumer manifest 统一 GPUI Kit、Base 与 GPUI Pre 的类型及补丁。桌面需要 Rust 1.99.1；CLI 的独立工作区及无显示/GPU运行约束保留。
- Material Button、导航和主题应实际用于产品入口；保留现有多语言输入、配置预览、显式写入、漂移检测和恢复行为。不得以新外观删除已有能力。

## 可观察的交付

- Agents、Providers、Gateway、Settings 使用统一的 Material 语义颜色、按钮状态和形状；宽窗导航和窄窗入口均可用。
- 在窗口内添加/编辑/测试 Provider，启动网关后查询实际模型目录，再对隔离 Codex 配置预览、保存和恢复；持久化来自原有共享状态。
- 网关启动失败显示原因并允许重试；端口被占用不显示已启动。停止和窗口退出只管理本窗口启动的子进程，不终止用户独立启动的网关。
- 浅/深主题、五语、360/440/900px布局、键盘焦点、取消和错误输入保留。窗口测试与组件测试分别记录；真实账户、其他操作系统和用户视觉认可另列。

测试与证据写入 `COVERAGE.md`，原始日志和截图存于源码树外。未实现的 Routing、Usage、Library、插件、同步和其余平台保持待完成，不以占位页面计为实现。

## 2026-11-02 整合状态

远端 main 的共享 ProviderStore、系统凭据、偏好和监督网关已与 Material 前端整合；旧原型保存在历史分支，不以旧实现覆盖新增功能。实际接入 Material Button/NavigationRail/主题桥接，并保留未保存草稿的取消/关窗确认。

workspace 327 项与桌面 79 项通过；80 场景真实 GPUI 像素已导出，其中代表场景已目视检查。原生 Wayland 实际跑通新增/刷新 Provider、共享目录、模型 a→b 更新、预览不写与断开后原字节恢复、自有网关启停、关闭回收及主题语言保存；重启显示已保存的德语深色。原生输入法完整行为、读屏和用户对本轮视觉的认可仍未通过。

Routing、Usage、Profile、订阅账号、Library、插件和多 Agent 接入等完整目标均保留，未接入的 Usage 草稿独立保存。三条近期原版 issue 的状态和后续合成验收见 ISSUE_FEEDBACK.md；完整需求仍为 99 项。
