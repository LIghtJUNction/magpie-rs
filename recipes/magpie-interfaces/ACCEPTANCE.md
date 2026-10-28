# 分项验收

迁移时全部未执行。使用一次性隔离 HOME/Agent 目录、虚构密钥、本地可记录请求的上游 fixture；仅对明确授权的真实集成使用账户。软件重建后，每条记录版本/平台、初始数据、操作、实际结果和未验证原因。

先读取本包所有 specs。每条 REQ 的全部正文是验收合同，下面列出原规格明确给出的正常/失败/边界场景；不能只检查成功路径。对于 API/账号/文件权限，fixture 要能注入 401/429/5xx、超时、损坏数据、并发修改和取消，并检查请求次数及文件前后内容。UI 用真实构建运行，在宽屏、窄屏及键盘流程下验收。

## REQ-080：运行形态

规格：[specs/08-cli-tui-and-web.md](specs/08-cli-tui-and-web.md)。状态：未执行。

- 正常验收：Linux 无桌面环境运行 `magpie web` 或 TUI，可操作同一 gateway/provider 配置。
- 失败验收：页面加载失败或 GUI 崩溃不损坏后端文件；服务退出时客户端收到明确状态。

## REQ-081：桌面导航和设置

规格：[specs/08-cli-tui-and-web.md](specs/08-cli-tui-and-web.md)。状态：未执行。

- 正常验收：改一个 Gateway 设置，页面 API 和本地 CLI 立即读到相同结果。
- 失败验收：API 操作失败保持当前编辑值/显示错误，不把暂存值作为已保存成功。

## REQ-082：浏览器 Web UI

规格：[specs/08-cli-tui-and-web.md](specs/08-cli-tui-and-web.md)。状态：未执行。

- 正常验收：从登录后的手机浏览器连接同一 LAN 上显式开启的 Web UI，用专属 key 完成登录和只读查看。
- 失败验收：没有 web key 的未授权来源无法通过 API、资源 URI 或 WebSocket 读取私有数据。

## REQ-083：TUI

规格：[specs/08-cli-tui-and-web.md](specs/08-cli-tui-and-web.md)。状态：未执行。

- 正常验收：增加 Provider 后从 GUI 重新进入能看到同一 Provider。
- 失败验收：quota 请求失败时显示最近更新时间和错误而不清除已知状态。

## REQ-084：CLI 接口

规格：[specs/08-cli-tui-and-web.md](specs/08-cli-tui-and-web.md)。状态：未执行。

- 正常验收：`magpie provider models deepseek --json` 输出有效模型 JSON，`magpie usage --csv` 输出筛选后的记录。
- 失败验收：缺参数/未知命令提示正确用法并返回非零状态，不改配置。

## REQ-085：Agent 状态、更新和配置流程

规格：[specs/08-cli-tui-and-web.md](specs/08-cli-tui-and-web.md)。状态：未执行。

- 正常验收：只装了 Claude Code CLI 的设备不显示一个可配置到另一台机器的“本地 Claude Code”。
- 失败验收：Agent 已卸载但遗留目录时，不向无程序目标写入不可读取设置。

## REQ-086：应用更新和可诊断错误

规格：[specs/08-cli-tui-and-web.md](specs/08-cli-tui-and-web.md)。状态：未执行。

- 正常验收：升级被网络/代码签名拒绝后仍能启动当前版本并查看失败理由。
- 失败验收：无网络时显示版本状态未知，不称为已升级到最新。

## REQ-100：视觉方向

规格：[specs/10-visual-and-localization.md](specs/10-visual-and-localization.md)。状态：未执行。

- 正常验收：Providers、Routing、Usage 等同一主题的面板、标签和状态色具有一致的层级。
- 失败验收：暗色主题下保留背景的 SVG/icon 或低对比状态不可辨识时提供合适的 variant。

## REQ-101：App 和紧凑 Panel 布局

规格：[specs/10-visual-and-localization.md](specs/10-visual-and-localization.md)。状态：未执行。

- 正常验收：宽窗、窄窗、360px panel 都可查看 account 名称、quota reset 和刷新控件。
- 失败验收：错误长文本不会覆盖刷新按钮或阻止用户关闭/返回。

## REQ-102：语言

规格：[specs/10-visual-and-localization.md](specs/10-visual-and-localization.md)。状态：未执行。

- 正常验收：switch locale 后 UI 和主要状态提示一致，不混入半个未翻译 key。
- 失败验收：未知系统 locale 选择英文且不让启动时崩溃。

## REQ-103：可访问性与操作反馈

规格：[specs/10-visual-and-localization.md](specs/10-visual-and-localization.md)。状态：未执行。

- 正常验收：全键盘可添加/保存 Provider 与关闭 dialog；没有 mouse-only 动作。
- 失败验收：后台异步更新状态时不会突移焦点/丢失用户当前输入。

## REQ-104：参考截图与产品数据

规格：[specs/10-visual-and-localization.md](specs/10-visual-and-localization.md)。状态：未执行。

- 正常验收：使用 `UI_REFERENCE.md` 的 Providers 与 Routing 文字层级作为参考，重建使用独立占位名称。
- 失败验收：文字参考缺失或矛盾时报告材料问题，不依赖原仓库图片或网络盗链；不得以下载原版资源代替设计。
