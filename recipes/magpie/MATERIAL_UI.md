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

## 2026-11-02 收尾状态

真实 Material 组件和窗口内 Gateway 生命周期已经接入并完成有限本地测试；用户明确否决首版窗口，后续主题与布局修正尚未重新完成原生验收。先按用户要求提交推送、下次继续。多 Agent 连接、路由集成、Usage、Profile、订阅账号、Library、插件等均不是已交付功能。未接入的 Usage 草稿保存在源码独立 WIP 分支，不能据其文件存在提升覆盖状态。

本轮重新查阅标准库目录，现有 design-directions/design-foundations 仍是适用的递归设计合同，不新增无关配方。

本轮推送保存到源码与配方的 `wip/material-desktop-20261102` 分支。远端 main 已有后续 Provider/网关能力与 1.4 配方；下次先整合远端进度，不能用当前旧基线覆盖远端实现与需求。
