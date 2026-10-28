# 整机验收

以下端到端场景尚未执行。COVERAGE.md 覆盖全部 93 条 REQ；三个递归组件的 ACCEPTANCE.md 保存各条正常/失败/边界场景。当前只对 REQ-011、REQ-030、REQ-034 的有限组件路径做了构建和基准检查，详见 EVIDENCE.md；这些结果不代表完整用户流程或供应商转发验收通过。

## 已执行的组件级检查

- Linux x86_64 release CLI 构建成功；`cargo clippy --release --locked -p magpie-cli -- -D warnings`、workspace 格式检查，以及 `cargo fmt --manifest-path apps/desktop/Cargo.toml --check`（GPUI package）成功。
- 合成 Codex TOML fixture 的 CLI Agent 发现路径、status JSON 和 config path JSON 均通过输出校验；没有读取真实用户配置，也没有验收真实 Agent 或多入口状态一致性（REQ-011 部分）。
- 真实 CLI 进程启动 loopback 服务，健康检查成功，`GET /v1/models` 返回合法的空目录响应；30 次服务启动与 100 次目录请求均无正确性错误。只验证无 Provider/模型时的空目录路径，配置模型的列举和完整 REQ-034 仍未实现。
- 上述服务启动和健康检查只覆盖 Linux loopback 的基本路径，端口冲突、退出/崩溃恢复、GPUI 生命周期集成和跨平台行为未执行（REQ-030 部分）。
- 上述性能测量仅覆盖当前实现子集；其余端到端用例、其他系统/CPU、GPUI交互、真实 Agent 与 Provider 均未执行。

## 隔离环境

创建一次性用户配置目录和 Agent fixtures；禁止使用当前用户真实的 ~/.codex、密钥库或登录材料。准备已知版本的 Codex/Claude/OpenCode 与本地 HTTP 上游 A/B，模型 local/a、local/b。A 可按用例返回 429/401/超时/损坏流；B 返回固定文本“fixture-ok”、已知 usage 和可记录的调用计数。所有公网/真实账户验收另列，fixture 不证明供应商兼容。

1. **发现和配置保护（001、002、011—014）**：Codex 配置含 model、用户 provider、Header、注释与未知字段；扫描、预览、Connect，再添加无关 Header、Disconnect。目标字段恢复原值，新增 Header/注释/未知字段保留。分别注入不可读、无效 TOML、符号链接、读取后改写和写中断；必须拒绝/合并或保持完整旧/新文件，不能清空。
2. **真实网关与一致状态（003、010、030、034、080—085）**：经 CLI 创建 local Provider 并选择 local/a；GPUI/TUI/Web 刷新显示同一设置。启动后 GET /v1/models，目录含 local/a；从配置好的 Agent 发起新会话，A 计数加一并返回 fixture-ok。再占用 3425 启动，必须显示端口失败而非健康。
3. **模型可见性/Profile（004、005、015—019、022）**：只在 Codex 隐藏 local/b，另一 Agent 仍可见，直接指定 local/b 仍可请求。保存 Work，更改两个 Agent 模型和可见列表后应用 Work；全部恢复；一个目标文件只读时不能报告全成功，保留恢复状态。
4. **重试与账务（040—046、050、059）**：order 组 [local/a,local/b]，A 首次 429，B 200。发一次固定 request/session ID，A、B 各一次，客户端收到成功；请求汇总一次成功且 retried，attempt 两条，不重复计 token。所有成员失败、空组及循环组分别给明确错误，有界终止。
5. **协议/流（031—033）**：分别发送 Chat、Responses、Messages、Gemini 的非流及 stream fixture，含工具调用/结果、system/developer、reasoning 和已知 usage；与固定上游负载语义对应，工具参数不变，结束事件/错误符合客户端协议。中途取消后上游连接停止，缓冲释放；不支持字段明确报错。
6. **授权（035—039、090—093）**：loopback 无 key 正常；显式 LAN 分享后远程无 key 为 401/403，受限 key 只允许 local/a，local/b 在上游计数增加前拒绝。撤销 key 后下一请求拒绝。伪 Host、Origin:null、伪转发头和 Web cookie 冒充 Gateway key 均不能越权。私网跳转在未授权下载场景被阻止。
7. **Usage/会话/隐私（051—058、079、092）**：已知 usage 的同 session 两轮请求产生一条会话、两条请求与一致汇总。无 session ID 不虚构归属。默认持久化/日志中不存在测试正文标记“private-fixture-123”。开启指定目录录制后新请求可见，关闭并清理后正文删除、元数据保留。空间达到显式配置上限仍能转发。
8. **额度与价格（020—029、052、057）**：账户日 cap 20，前20次按规定服务，第21次拒绝并说明重置；额度刷新离线保留旧值与时间且标记未知/过期。价格未知不显示0；修改价格后历史成本政策可追踪。重复 seat 不重复汇总。
9. **插件退休交接（025、060—069）**：用隔离旧账户 fixture 安装对应社区插件，Move 后当前所有者唯一，请求只到插件一次。迁移/refresh 故障保留账户材料并解释未就绪；停用/删除后停止服务，重装可恢复。hook 抛错/取消/超时不残留注册或重复调用。真实 npm/订阅验收仍须单独运行。
10. **Library/MCP/备份（070—078）**：本地受控 stdio MCP 返回已知两项 tools/list，写入 Codex/Claude 兼容配置；移除只撤销自己条目。备份 Library/Profile 后在第二隔离配置目录恢复，数据一致且 LAN/recording 等本机开关不被开启。错误口令、损坏分片/索引、远端403、并发冲突都停止并保留两端旧数据。
11. **搜索/媒体/路由决策（043、047—049、065、095）**：受控 classifier 返回合法/非法label和超时，合法可解释命中，非法按规定fallback而非执行URL；关闭search/tool后上游不再接收隐式调用。搜索/音频/媒体输入按规格保留，限制输出尺寸与超时，不用真实公网结果冒充fixture。
12. **视觉与语言（100—104）**：真实 GPUI 宽窗/窄窗、360/440px panel、深浅主题、五种 locale 分别浏览 Agents/Providers/Routing/Usage/表单。键盘添加/保存/关闭弹层，焦点保持且错误可读；切换语言保留未保存输入，未知 locale 回退英文。与 UI_REFERENCE.md 的层级对照，截图测试数据不得成为默认账号。
13. **运行与包装（080、086、094）**：在各目标真实系统安装、启动、停止、升级失败回退、卸载；CLI 无显示服务器/GPU运行；Nix/Arch 安装可复现。Windows/WSL 配置分别归属正确环境，不启动睡眠distro。Android CLI/Termux、Android原生GUI、Web访问三种状态单独记录。cargo check 不替代以上运行验收。
14. **性能**：执行 PERFORMANCE.md 的固定工作负载，记录 RSS/PSS、峰值、包体、冷/热启动、p50/p95与硬件/数据量。数字预算未知时报告实测并明确待确认，不能凭线程数或二进制体积宣称低内存目标已通过。

性能项同时按 [OPTIMIZATION.md](OPTIMIZATION.md) 核对计时边界、错误计数、有效工作量、至少5轮前后交替对比、瓶颈和回归结论。预算尚未制定或数据不足时标记待确认/未测量，不写性能通过。

完成后提交逐 REQ 的通过/失败/未运行表，附测试命令、fixture版本/散列、设备/OS、原始观察与已知限制。未运行的外部兼容或硬件场景不得标为通过。


## 新指南的交付检查

- 对quality依赖逐项记录实际设计、代码和性能证据，未执行项保留为未验证。
- 实现源码位于 `generated/magpie-rs-source/` 子模块内，不在 `recipes/` 内；核验 parent index 中 gitlink mode `160000`、remote URL、pin 和 clean/dirty 状态。
- 若实际发布到GitHub，回读recipkg topic且保留已有topic；只做本地迁移时明确记为发布前待办。
- check、pack、归档check、build通过只表示结构与材料交付，不表示软件、外部账号、硬件或性能通过。

## 发行验收

- AUR可查询 `magpie-rs-bin` 和 `magpie-rs-git`，PKGBUILD 与 .SRCINFO 一致，固定版校验和匹配公开Release附件；源码版实际编译当前源码并记录最终VCS版本。
- 包声明提供 `magpie` 并与原版及另一Rust变体互斥。一次pacman事务完成原版替换；用户配置与登录数据保留。安装后命令和桌面入口归属新包，CLI和GPUI分别启动。
- Release指向准确源码/父仓库提交，附件含平台/架构与校验和。各平台记录构建、安装器内容检查和实际执行状态；AndroidCLI归档不冒充APK，unsigned安装器不冒充已签名或公证。
