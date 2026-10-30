# 07 · Library、跨 Agent 同步与备份

## REQ-070：统一 Library

- 优先级：核心。提供一个资料库来维护 Agent 指令集、MCP servers、skills、hooks、subagents、技能分组和 projects。用户创建/编辑一次，Magpie 按各 Agent 的配置格式安装。
- Library 是可导入/导出的用户数据；其文件存放在用户配置目录。UI 与 CLI 的更改按事务串行应用，状态更新可恢复。
- 只移除 Magpie 自己写入的内容。系统跟踪每个已应用项、agent、project 和自己建立的文件/marker；用户手动添加的相邻配置始终保留。
- 正常验收：将一条 skill 装给 Codex 和 Claude Code 后，两边使用各自格式而语义相同。
- 失败验收：配置目标无法读取时，不把它当空文件覆盖；显示路径和读/解析错误。

## REQ-071：Instructions、skills 和 Agent 级操作

- 优先级：核心。Instructions 有多个具名 set，可被单 Agent 选择或套用为默认。Magpie 写入的段落由 marker 包住；agents 文件其它部分原样保留。允许用户链接已有的 instruction 文件。
- Skills 页面可以创建、导入、安装、更新、删除 skill；分组筛选；控制每个 Agent 是否获取某 skill，单独按 Agent 设加载方式（How）；识别 skills catalog 中远端版本并更新。
- Agent-specific Hooks/commands/subagents 如果该 Agent 支持，按其原生 schema 管理；未知/用户部分留存。技能安装来源/版本/Agent 和更新时间可见。
- 对已卸载 Agent 的旧 Library 项仍显示在哪些项目中，支持明确移除；不把“这台电脑没有该程序”误判为“用户不再需要这个 skill”。
- 正常验收：卸载一个 Agent 后，它持有的既有 Library 项仍列出且可选择移除；未卸载 Agent 看不到那一项。
- 失败验收：第三方 Agent 覆写 marker 外内容时，同步不还原整份旧配置。

## REQ-072：MCP servers

- 优先级：核心。可新增 command/stdio 或 HTTP/SSE URL MCP server；配置 command、args、env/headers、timeout、enabled 和适用 Agent。格式导出到目标 Agent 的用户级配置或项目级配置。
- 可测试 server 的连接、握手和 `tools/list`，展示连接状态/登录要求、工具名及摘要；status 检查有 step 和 timeout，不因“未检查”显示“已连接”。支持 MCP OAuth 需要重新授权时明确提示。
- 只将用户明确分配的 server 写给 Agent；项目级 server 写进该 Agent 可读取的位置，使用 Agent 确认/approval 的原生流程。
- 正常验收：stdio server 返回一页 `tools/list`，Library 显示数量并将定义写入目标 Agent 配置。
- 失败验收：超时标明在哪一步等待多久；401/403 不回报健康；一个失效 MCP 不影响其他服务器。

## REQ-073：Projects

- 优先级：一般。Library project 绑定目录，挑选可供指定 Agent 使用的 skills 和 MCP servers；允许重复命名的 project 通过绝对目录区分。
- 写入目标目录中的 Agent 标准文件/目录；Magpie 记录自己放入的文件并按需维护 `.gitignore`。删除 project 只清理自己创建的文件/引用。
- 在新增写入前，确认 project dir 不等于 Agent 配置本身、Library 根目录或链接到其 own config 的路径。WSL project 保存 distro/路径身份，不从本机 Shell 环境推断 distro 的 home。
- 正常验收：为一个项目安排 skills 后，只在该 project 的 Agent session 中出现。
- 失败验收：目标路径变成一个 Agent 自己的指令或 MCP 文件时拒绝写入而不移动/覆盖原文件。

## REQ-074：RTK 输出压缩

- 优先级：一般。Library 可为支持的 Agent 配置 magpie 的 RTK 命令行输出压缩工具，帮助模型少读取重复的 shell 输出。
- 展示启用范围、agent 配置状态、版本和修复方式；没有安装/对应平台不支持时不写入无效命令。
- 其效果不应改动命令的实际运行、exit code、cwd 或用户原命令参数，只裁剪/压缩回给模型阅读的文本。
- 正常验收：应用一个已知支持的 RTK 配置，原命令的结果和 exit code 保持相同但输出更短。
- 失败验收：RTK 不存在时 Agent 命令仍正常执行或清楚提示用户。

## REQ-075：本地文件备份和可选区段

- 优先级：核心。Settings/CLI 支持将 Magpie 设置导出为可口令保护的备份文件，按区段选择 providers/accounts、settings、profiles、agents、library、usage/quota。还原前留有本地 Undo snapshot。
- 显示备份时间/版本/包含区段、口令验证状态；导入不可信或损坏数据前解析并验证全部区段。文件目标可写入失败时不留下部分成功备份。
- 不能因为备份缺少某一个可选区段就清空本机对应设置；用户在 Restore UI 明确选择要替换的部分。Undo 能恢复 Restore 前的设置。
- 正常验收：把 Library 和 profiles 备份后在新配置目录还原，选择区段都可回读。
- 失败验收：口令错误、版本新于本客户端、损坏文件或任一必需的本地 JSON 不可读时，中止全部写入并保留本机状态。

## REQ-076：加密远端同步

- 优先级：核心。可将加密备份在两台电脑之间同步，支持 WebDAV、S3、GitHub repo、Nutstore/Nextcloud 等兼容 WebDAV 的服务。此功能同步的是 Magpie 配置区段，不是任意目录或 Git 工作树。
- 每个服务配置保存独立 endpoint/owner/repo/branch/prefix 及凭据；用户选择同步间隔（默认 3 分钟）、手动同步或关闭自动同步。新增 computer 可加入远端设置并保留本机 Undo。
- 多机修改按分区 hash/版本合并：仅本地改动上行，仅远端改动下行，同时改动使用 last modified 规则且保存被替换版本。通过 ETag/SHA 条件更新避免静默覆盖并处理并发冲突。
- 默认备份内容加密；GitHub 备份 repository 必须是 private。禁止将 passphrase、token、provider keys 明文发到 remote；token 与加密 passphrase 必须是不同凭据。
- 正常验收：一台电脑修改 agents 配置，两台机同步后另一台看到该变更且未触碰其本机专属设置。
- 失败验收：远端不可读、数据损坏、权限失败或冲突时停止并保留本地与远端旧版本，不把文件解释成空配置。

## REQ-077：每台电脑保留的设置

- 优先级：核心。同步共享配置时保留该电脑的窗口/主题/字体、proxy、Gateway port/bind、局域网分享 enabled/allowlist、private hosts、WSL/lookups、原生终端选择、recording 等本机开关。
- Provider sign-in/account 选择可按设置 mirror 规则决定是否跨机传播。共享开关不会跟着备份在另一台电脑打开。默认 LAN gateway key 与本机的暴露设置分开管理。
- 具体保留项在 Restore Preview 标示来源；不因比较值不同就同步覆盖本机专属设置。
- 正常验收：换机恢复 profile/provider groups 但不自动开启局域网共享或替换本机窗口尺寸。
- 失败验收：字段新增或本客户端不能识别时仅忽略/保留该字段而不是清空 settings。

## REQ-078：远端内容和恢复保护

- 优先级：核心。同步读取本地和远端之前验证文件内容、schema 与 version；HTML/XML/其他服务错误页、空文件和不属于 Magpie 的文件均不当作“首次备份”。
- 用户要以本机设置替代错误远端文件时，UI/CLI 明确指出目标 URL/repo/path，先保留旧文件，然后执行替换。GitHub branch/repo 错误提示使用相应恢复指引。
- 大数据分片写入时，同一 commit 包含所有分片与索引；读取校验每个 SHA/size。不能把不同代的分片拼成文件。
- 正常验收：远端 404 经过权限和 branch check 后视为首份；明确 empty repository 路径只初始化默认分支。
- 失败验收：远端返回无效/损坏索引时不尝试以本机空配置覆盖。

## REQ-079：Usage 数据与请求档案

- 优先级：一般。Usage/quota 可在共享设置备份之外，以各自密封文件单独同步和选择包含范围。将请求正文归档默认关闭；用户明确开启的正文归档与 usage 统计分离，并有本地/远端、大小上限和删除策略。
- 录制请求正文的 switch 属于每台电脑的本机设置，不由同步打开；有 S3 bucket 的用户可明确选择 S3 archive，否则仅在用户显式启用时写本机 private directory。
- Request archive 与 REQ-055 的 Gateway conversation recording 是独立开关和保存路径。官方 0.1.1948 已有无 S3 时本机私有归档、默认 1 GiB 上限和最旧请求淘汰；不能用会话录制的 7 天/256 MiB 规则覆盖它，也不能因为已有 usage 元数据就认定已保存请求正文。
- 正常验收：同步 usage 后，另一台电脑看到用量趋势，但它的 local gateway recording switch 没有被打开。
- 失败验收：归档未开启时不写本机 prompt body；明确开启但未配置 S3 时按本机归档策略处理。目的地写入失败有反馈且不泄露正文；达到容量限制后移除最旧请求并继续转发。
