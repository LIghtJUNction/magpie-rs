# Magpie 原版 CLI 安全隔离行为基线

采集日期：2026-10-29（Asia/Taipei，UTC+08:00）。实际执行窗口：`2026-10-29T05:29:45.631528+08:00` 至 `2026-10-29T05:32:58.466320+08:00`。目录名 `20261028-…` 沿用既有交接目录，不表示本次在当地 10 月 28 日执行。

本文件只记录已安装原版在空合成状态中的本地帮助、列表及查看行为。未修改项目代码、配方或 COVERAGE.md；未执行 Cargo、构建、下载、真实账户/Provider 操作、GUI/Web/TUI、服务启动或业务请求。本次观察**不是原版完整业务验收**，也不是 Rust 实现通过兼容验收的证明。

## 主要结果

- 共 46 次有效 Magpie 调用：13 次帮助、1 次版本、32 次本地列表/查看；45 次退出 0，1 次退出 1；无超时、无输出截停、无异常剩余子进程。所有 stdout/stderr 都来自空私有 fixture。
- 完全不存在 Codex 状态目录时，`agents` 仍列出 68 个受支持条目，均为 `not detected`；`ls` 没有 stdout，stderr 为 `magpie: no supported agents found on this machine`，退出 1。预建空 `CODEX_HOME` 目录会改变探测结果：Codex 为 `CLI not found`，`ls` 退出 0。
- `sessions --json` 的空值为数组 `[]`；`sessions --days 1 --json` 的空值为含日期、汇总、days/models/folders 的对象。`usage --csv` 输出 47 列表头，无数据行。
- `--version` 和全部 32 次本地查看都生成 `plugins.yml`（167 bytes）及两个零字节锁文件；13 次帮助均未留下文件。这里的“只读”指命令用途，不等于该原版完全没有首次初始化写入。
- 每条调用均使用新 user/net/PID/mount namespace 与新的 24 MiB tmpfs；文件大小硬限 8 MiB。网络 socket 系统调用额外返回 EPERM，IPv4/IPv6/Unix socket 创建检查均为 errno 1；前后网络快照一致、无 socket、无网络流量。

## 原版身份与参考材料

- 执行文件：`/usr/bin/magpie`，普通 ELF 文件，`66600984` bytes；运行时实测 `magpie 0.1.1948`，退出 0。
- SHA-256：`9aa760cbac67f375a810fcf15b9adf04edcb2cc7de64a9ebb0963da15c1d3eb7`；ELF BuildID：`b4926bee8abaf5b360a456e3ffd638ea3af5b945`。采集前后校验一致。
- 宿主 `pacman -Qo /usr/bin/magpie` 显示归属 `magpie-git 0.1.1463.r0.g2a3d9f5a-1`。包数据库版本与运行时自报版本不同；本行为基线固定在上面的二进制 SHA-256 和运行时版本，不将旧包号当作实际原版版本。
- 隔离参考：`28-original-web-evidence/sandbox_web.py`。其旧 `m28-dqhcqavp/completion.json` 为 `status=stopped`、`temporary_fixture_removed=true`；本次实际检查 `/tmp/m28-dqhcqavp` 不存在。未重启旧实例，未连接其 Web、桥接或控制 socket。
- 命令依据：同一二进制本次根级及子命令 `--help`；另外阅读了既有 `24-original-parity-evidence/original-help*.txt`。原版根帮助明确说明 `-h/--help anywhere does nothing else`。本次未执行帮助示例中出现的 add/set/install/login/sync 等写入或联网命令。
- 需求对照仅参考 `recipes/magpie/BUILD_PROMPT.md`、根验收/ORIGINAL_PARITY 及 `recipes/magpie-interfaces/specs/08-cli-tui-and-web.md` 的 REQ-084；没有实施代码变更，未将本观察提升为任何 REQ 全量通过。

## 隔离、限制和状态边界

宿主应用文件只挂载 `/usr -> /usr`，且为只读。根挂载及 `/proc`、`/dev` 目录只读；`/lib`、`/lib64` 为 `/usr/lib` 的链接。bubblewrap 另外提供私有 proc、最小设备节点和私有 devpts；未暴露 GPU、真实 HOME、真实 `/etc`、用户 bus、宿主运行目录、凭据或原项目。`/etc/passwd`、group、hosts、nsswitch.conf、空 resolv.conf 均由合成 memfd 提供，不是宿主配置。普通可写文件仅能留在 `/fixture`。设备字符节点不计入普通文件容量。

所有 HOME/XDG 路径和工作目录均在同一 `/fixture` tmpfs；`/tmp`、`/run` 也是指向 fixture 的链接。`CODEX_HOME` 是合成路径，第 1–42 次预建空目录，第 43–46 次保持该路径不存在，其他条件相同。没有放入 Agent 配置、会话、账户、Provider、模型或 Usage 记录。每个调用均从零普通文件开始，互不复用前一调用的初始化结果。

合成环境（包括执行器添加的合成 PWD）：

```json
{
  "HOME": "/fixture/home",
  "CODEX_HOME": "/fixture/codex",
  "XDG_CONFIG_HOME": "/fixture/config",
  "XDG_CACHE_HOME": "/fixture/cache",
  "XDG_DATA_HOME": "/fixture/data",
  "XDG_STATE_HOME": "/fixture/state",
  "XDG_RUNTIME_DIR": "/fixture/run",
  "PATH": "/fixture/bin",
  "TMPDIR": "/fixture/tmp",
  "LANG": "C.UTF-8",
  "TZ": "Asia/Taipei",
  "MAGPIE_NO_ACCOUNTS": "1",
  "PWD": "/fixture/work"
}
```

限制为：`RLIMIT_FSIZE=8388608:8388608`，`RLIMIT_CORE=0:0`，`RLIMIT_NOFILE=128:128`；`/fixture` tmpfs 容量 `25165824` bytes；umask `077`。每条 Magpie 命令 10 秒超时，宿主执行器再以 16 秒收尾；单个 stdout/stderr 上限各 64 KiB，超限即杀本次命令树。实测没有碰到任何限制。按运行期间与退出后扫描，普通文件逻辑大小峰值 167 bytes，最大单文件 167 bytes；硬容量上限由内核执行，不依赖轮询。执行器临时证据总量小于 1 MiB，本次未生成或下载超过 100 MB 的文件。

socket、socketpair、connect、bind、listen、sendto、sendmsg、sendmmsg 均被 seccomp 拒绝（EPERM）；因此即使某读取路径尝试后台网络初始化，也无法发出请求。`MAGPIE_NO_ACCOUNTS=1` 另外禁止账户发现。`PATH=/fixture/bin` 指向空目录，Agent/RTK/systemctl 是否可用的结论只适用于此合成环境。输出出现本地网关 URL 或官网文字只是文本，未请求这些地址。

准备时有两次 bubblewrap 参数拒绝：`--disable-userns requires --unshare-user`。这两次在 Magpie 启动前退出；随后显式加入 `--unshare-user`，没有降低隔离条件。它们不计入 46 次有效调用。

## 全部调用与短输出

表中 argv 是实际应用参数数组；所有应用路径均为绝对路径 `/usr/bin/magpie`，不是通过 PATH 查找。短输出以 JSON 字符串保留 stdout/stderr 区别及换行；长输出截断有标记，摘要不替换原程序含义。完整输出 SHA-256 和字节数见后面的逐次审计表。所有记录的 stderr 为空，唯独 #44 的空目录 `ls` 如表所示。

| # | fixture | argv | exit | stdout 摘录 | stderr 摘录 |
|---|---|---|---:|---|---|
| 01 | 空 Codex 目录 | `["/usr/bin/magpie", "--version"]` | 0 | `"magpie 0.1.1948\n"` | `""` |
| 02 | 空 Codex 目录 | `["/usr/bin/magpie", "--help"]` | 0 | `"magpie — one place to pick every agent's model\n\n  magpie                          open the app: a window plus a menu bar icon\n  magpie tray                     start in the menu bar only\n  magpie panel                    open the … [摘要截断]"` | `""` |
| 03 | 空 Codex 目录 | `["/usr/bin/magpie", "plugin", "--help"]` | 0 | `"usage: magpie plugin [list] [--json]\n       magpie plugin add <name \| npm \| git \| path> install a community plugin by its name (workbuddy, think-tags, Qoder), an OpenCode provider plugin, a pi package or a gateway hook/middleware … [摘要截断]"` | `""` |
| 04 | 空 Codex 目录 | `["/usr/bin/magpie", "library", "--help"]` | 0 | `"  magpie library                     what the library gives each agent: instructions, MCP servers, skills\n  magpie library sync                write it into the agents again (after one is installed, or edited by hand)\n  magpie lib… [摘要截断]"` | `""` |
| 05 | 空 Codex 目录 | `["/usr/bin/magpie", "usage", "--help"]` | 0 | `"  magpie usage [today\|7d\|30d\|all] tokens and cost per agent, model and subscription account (30d)\n  magpie usage --csv [--account <name>] [today\|7d\|30d\|all]   every request as CSV (or one account's): the model asked for, sent and … [摘要截断]"` | `""` |
| 06 | 空 Codex 目录 | `["/usr/bin/magpie", "sessions", "--help"]` | 0 | `"usage: magpie sessions [--days N\|today\|all] [--model <model>] [--folder <folder>] [--limit N] [--json]\n"` | `""` |
| 07 | 空 Codex 目录 | `["/usr/bin/magpie", "daemon", "--help"]` | 0 | `"  magpie daemon [install\|uninstall\|start\|stop\|restart\|status]\n                                  magpie serve as a service of yours, from boot or login and started again when it stops (systemd --user on Linux, launchd on the Mac)\n … [摘要截断]"` | `""` |
| 08 | 空 Codex 目录 | `["/usr/bin/magpie", "autostart", "--help"]` | 0 | `"  magpie autostart [on\|off]       open magpie (in the menu bar) when you log in, or say whether it does\n"` | `""` |
| 09 | 空 Codex 目录 | `["/usr/bin/magpie", "visible", "--help"]` | 0 | `"usage:\n  magpie visible                          which models each agent is shown\n  magpie visible <agent> <name>[,<name>…] show the agent only these: families (a tag set with\n                                          magpie provi… [摘要截断]"` | `""` |
| 10 | 空 Codex 目录 | `["/usr/bin/magpie", "codex", "--help"]` | 0 | `"Codex\n\n  magpie codex                  show it\n  magpie codex <model>          set its model   (magpie models codex lists them)\n  magpie codex <field>          show one field\n  magpie codex <field> <value>  set another field\n  mag… [摘要截断]"` | `""` |
| 11 | 空 Codex 目录 | `["/usr/bin/magpie", "ls", "--help"]` | 0 | `"  magpie ls                       list detected agents and their settings\n"` | `""` |
| 12 | 空 Codex 目录 | `["/usr/bin/magpie", "groups", "--help"]` | 0 | `"usage:\n  magpie groups                           list routing groups: yours, then those magpie found\n  magpie group <id>                       show one group and its models (also: magpie group show <id>)\n  magpie group add <name> … [摘要截断]"` | `""` |
| 13 | 空 Codex 目录 | `["/usr/bin/magpie", "gateway-key", "--help"]` | 0 | `"  magpie gateway-key list\|add <name>\|rotate <id>\|remove <id>   manage the keys clients use to call a shared gateway\n  magpie gateway-key limit <id> [list\|off\|reset [n]\|remove <n>\|[add] day\|week\|month\|<N>d\|<N>h --tokens N --cost US… [摘要截断]"` | `""` |
| 14 | 空 Codex 目录 | `["/usr/bin/magpie", "agents", "--help"]` | 0 | `"  magpie agents                   list every supported agent\n"` | `""` |
| 15 | 空 Codex 目录 | `["/usr/bin/magpie", "agents"]` | 0 | `"  Claude Code               not detected   \n  Claude Desktop            not detected   \n  Claude Science            not detected   \n  Codex                     CLI not found  /fixture/codex/config.toml\n  Gemini… [摘要截断]"` | `""` |
| 16 | 空 Codex 目录 | `["/usr/bin/magpie", "ls"]` | 0 | `"  Codex  CLI not found  /fixture/codex/config.toml\n"` | `""` |
| 17 | 空 Codex 目录 | `["/usr/bin/magpie", "profiles"]` | 0 | `"no profiles yet · magpie save <name>\n"` | `""` |
| 18 | 空 Codex 目录 | `["/usr/bin/magpie", "groups"]` | 0 | `"no routing groups yet · magpie group add <name> models=<m1>,<m2> · magpie group help\n"` | `""` |
| 19 | 空 Codex 目录 | `["/usr/bin/magpie", "models"]` | 0 | `"no models yet · add a provider first: magpie provider add deepseek sk-…\n"` | `""` |
| 20 | 空 Codex 目录 | `["/usr/bin/magpie", "models", "codex"]` | 0 | `"! codex is shown none of them: nothing is in  · magpie visible codex all shows it every model\n  codex is shown every provider's models · magpie visible codex <family>,… narrows it\n  http://127.0.0.1:3425/v1\n"` | `""` |
| 21 | 空 Codex 目录 | `["/usr/bin/magpie", "visible"]` | 0 | `"  every agent is shown every model · magpie visible <agent> <family>,… narrows one\n"` | `""` |
| 22 | 空 Codex 目录 | `["/usr/bin/magpie", "codex"]` | 0 | `"  Codex  CLI not found  /fixture/codex/config.toml\n"` | `""` |
| 23 | 空 Codex 目录 | `["/usr/bin/magpie", "codex", "effort"]` | 0 | `"Codex effort default\n"` | `""` |
| 24 | 空 Codex 目录 | `["/usr/bin/magpie", "library"]` | 0 | `"Instructions\n  none yet · magpie library instructions set <file>\nMCP servers\n  none yet\nSkills\n  none yet\n  kept in /fixture/config/magpie/library · magpie library help\n"` | `""` |
| 25 | 空 Codex 目录 | `["/usr/bin/magpie", "library", "instructions"]` | 0 | `""` | `""` |
| 26 | 空 Codex 目录 | `["/usr/bin/magpie", "library", "rtk"]` | 0 | `"! rtk isn't installed — https://www.rtk-ai.app\n  none of the agents here is one RTK has a hook for\n"` | `""` |
| 27 | 空 Codex 目录 | `["/usr/bin/magpie", "plugin", "list"]` | 0 | `"No plugins. Add one: magpie plugin add <npm package \| git repo \| path>\n"` | `""` |
| 28 | 空 Codex 目录 | `["/usr/bin/magpie", "plugin", "list", "--json"]` | 0 | `"[]\n"` | `""` |
| 29 | 空 Codex 目录 | `["/usr/bin/magpie", "plugin", "builtin"]` | 0 | `"● budget     on           Key limits\n● gateway    on           Gateway page\n● groups     on           Routing groups\n● library    on           Library\n● middleware on           JS gateway plugins\n● otel       on           Observability\n● redact     on           Privacy masking\n● rotation   on           Routing strategies\n● routing    on           Routing\n● search     on           Web search\n● sessions   on           Sessions\n● tools      on           Plugin tools\n● usage      on           Usage\n● warmups    on           Warm-up and check-in\nin /fixture/config/magpie/plugins.yml\n"` | `""` |
| 30 | 空 Codex 目录 | `["/usr/bin/magpie", "plugin", "dump", "--json"]` | 0 | `"{\n  \"file\": \"/fixture/config/magpie/plugins.yml\",\n  \"there\": true,\n  \"preset\": \"all\",\n  \"api\": \"1.0\",\n  \"plugins\": [\n    {\n      \"id\": \"budget\",\n      \"enabled\": true,\n      \"source\": \"default\",\n      \"chosen\": true,\n      \"inUse\"… [摘要截断]"` | `""` |
| 31 | 空 Codex 目录 | `["/usr/bin/magpie", "gateway-key", "list"]` | 0 | `"ID  NAME  STATE  GATEWAY KEY  LIMIT  MODELS  ACCOUNTS\n"` | `""` |
| 32 | 空 Codex 目录 | `["/usr/bin/magpie", "usage"]` | 0 | `"no calls last 30 days · route an agent through magpie and its usage shows up here\n  /fixture/config/magpie/usage.jsonl\n"` | `""` |
| 33 | 空 Codex 目录 | `["/usr/bin/magpie", "usage", "today"]` | 0 | `"no calls today · route an agent through magpie and its usage shows up here\n  /fixture/config/magpie/usage.jsonl\n"` | `""` |
| 34 | 空 Codex 目录 | `["/usr/bin/magpie", "usage", "all"]` | 0 | `"no calls all time · route an agent through magpie and its usage shows up here\n  /fixture/config/magpie/usage.jsonl\n"` | `""` |
| 35 | 空 Codex 目录 | `["/usr/bin/magpie", "usage", "--csv"]` | 0 | `"time,agent,requested_model,provider,host,model,served_model,swapped,effort,input_tokens,output_tokens,cache_write_tokens,cache_read_tokens,reasoning_tokens,cost_usd,duration_ms,ttft_ms,status,error,session,kind,provider_key_id,provider_key_name,provider_account,route_id,request_id,endpoint,error_message,error_type,source,rejected,session_provider,session_account,session_official_login,caller_key_id,caller_key_name,response_id,upstream,cache_write_5m_tokens,cache_write_1h_tokens,tries,handed_on,tier_asked,tier_sent,tier_served,upstream_stop,credits\n"` | `""` |
| 36 | 空 Codex 目录 | `["/usr/bin/magpie", "usage", "--last", "24h"]` | 0 | `"no calls last 1 day · route an agent through magpie and its usage shows up here\n  /fixture/config/magpie/usage.jsonl\n"` | `""` |
| 37 | 空 Codex 目录 | `["/usr/bin/magpie", "usage", "--days", "1"]` | 0 | `"no calls last 1 day · route an agent through magpie and its usage shows up here\n  /fixture/config/magpie/usage.jsonl\n"` | `""` |
| 38 | 空 Codex 目录 | `["/usr/bin/magpie", "sessions"]` | 0 | `"no sessions yet · Claude Code's, Codex's, OpenCode's and Pi's sessions on this computer show up here\n  ~/.claude · /fixture/codex\n"` | `""` |
| 39 | 空 Codex 目录 | `["/usr/bin/magpie", "sessions", "--json"]` | 0 | `"[]\n"` | `""` |
| 40 | 空 Codex 目录 | `["/usr/bin/magpie", "sessions", "--days", "1", "--json"]` | 0 | `"{\n  \"from\": \"2026-10-29\",\n  \"to\": \"2026-10-29\",\n  \"input\": 0,\n  \"output\": 0,\n  \"cache_read\": 0,\n  \"cache_write\": 0,\n  \"cost\": 0,\n  \"unpriced\": [],\n  \"active\": 0,\n  \"days_used\": 0,\n  \"days\": [\n    {\n      \"date\": \"2026-10-29\",\n      \"input\": 0,\n      \"output\": 0,\n      \"cache_read\": 0,\n      \"cache_write\": 0,\n      \"cost\": 0,\n      \"active\": 0\n    }\n  ],\n  \"models\": [],\n  \"folders\": []\n}\n"` | `""` |
| 41 | 空 Codex 目录 | `["/usr/bin/magpie", "autostart"]` | 0 | `"magpie doesn't open at login\n"` | `""` |
| 42 | 空 Codex 目录 | `["/usr/bin/magpie", "daemon", "status"]` | 0 | `"the magpie service isn't installed · magpie daemon install\n"` | `""` |
| 43 | Codex 目录不存在 | `["/usr/bin/magpie", "agents"]` | 0 | `"  Claude Code               not detected  \n  Claude Desktop            not detected  \n  Claude Science            not detected  \n  Codex                     not detected  \n  Gemini CLI                not detect… [摘要截断]"` | `""` |
| 44 | Codex 目录不存在 | `["/usr/bin/magpie", "ls"]` | 1 | `""` | `"magpie: no supported agents found on this machine\n"` |
| 45 | Codex 目录不存在 | `["/usr/bin/magpie", "codex", "effort"]` | 0 | `"Codex effort default\n"` | `""` |
| 46 | Codex 目录不存在 | `["/usr/bin/magpie", "codex"]` | 0 | `"  Codex  —  ·  effort —  /fixture/codex/config.toml\n"` | `""` |

## 可观察默认行为与兼容注意点

| 项目 | 原版实际行为 | 对兼容实现/验收的含义 |
|---|---|---|
| agents / ls | `agents` 输出全部 68 项；`ls` 只输出探测项，真正无探测项时 exit=1/stderr | 两者不能当同义词；空列表不能一律退出 0 |
| Codex 目录存在性 | 空 `CODEX_HOME` 目录就足以让探测列表出现 Codex，显示 CLI not found；目录不存在则 not detected | 测试夹具本身会改变发现状态；这个条目不证明 Agent 已安装或已登录 |
| Agent 单字段 | 两种 fixture 下 `codex effort` 都输出 `Codex effort default`、exit=0；不存在目录时 `codex` 显示 model/effort 为破折号 | default 文本不等于读到了有效配置；不能把默认读取成功推断成已安装 |
| 帮助 vs 普通查看 | 13 个帮助调用不落文件；版本及 32 个查看调用都有同组初始化文件 | 命令用途只读与无文件系统副作用应分开验收；不要求新实现照搬无关写入，但必须明确差异 |
| Profiles / groups / models | 空态均为人类可读引导文字、exit=0 | 未文档化的统一 JSON/通用 status 子命令不能凭空视为原版接口 |
| models codex | 无任何 Provider/模型，仍输出可见性说明及 `http://127.0.0.1:3425/v1` | 只是默认 URL 文本，不能当成网关监听或健康证明；`nothing is in ` 的字段为空是原版所见文案 |
| 可见性 | `visible` 空态为 every agent is shown every model | 未收窄与模型目录非空是不同状态 |
| Usage 默认窗口 | 无参数为 last 30 days；today 为本地今日；all 为 all time；--last 24h 与 --days 1 均为 last 1 day | 不能用统一默认 7 天替代；本次只有空态，不证明有记录时的边界筛选 |
| CSV | `usage --csv` 空态也有固定顺序 47 列，无数据行；stdout 没有混入提示或日志 | 不能返回 []、空字符串或改列序冒充兼容 |
| Sessions JSON | 无日期汇总参数时 []；--days 1 时含 from/to/输入输出/cache/费用/days/models/folders 的对象 | 两种接口形状需分开解析；不能因为都带 --json 而假设固定数组 |
| 插件列表 | plugin list --json 为 []；plugin builtin 却有 14 个 on 项；dump preset=all/api=1.0 | 社区包列表和内置功能注册不是同一清单；active/on 不证明网络服务已经运行 |
| Library | library 显示 Instructions/MCP servers/Skills 空态；library instructions 成功且 stdout 为空 | 未配置共享指令时空字符串是成功状态 |
| 自启动/daemon 状态 | autostart 输出未开启；daemon status 输出未安装，二者 exit=0 | 状态为 off/未安装不自动等于 CLI 失败；本次没有查询宿主 user service |
| PATH 的影响 | library rtk 显示 rtk 不在 PATH；其他 Agent CLI 无法从空 PATH 找到 | 这是隔离默认行为，不是对宿主安装状态的断言 |
| 根帮助与子帮助 | 根帮助末尾只列 13 个简写入口，agents 实列 68 个；sessions 子帮助额外列出 --limit N | 根帮助摘要不是支持清单的上限；只证明 --limit 的文档存在，本次未测试其语义 |

这些是本次原版观察及对接口消费方的要求，不是对当前 Rust CLI 的实测差异：本次未执行、构建或改动 Rust 实现。尚未测过的未知参数/缺参错误、损坏配置、权限不足、有记录筛选、并发、修改恢复等仍然未验证。

## 结构化输出和名称细节

`usage --csv` 的精确表头（47 列，0 数据行）：

```csv
time,agent,requested_model,provider,host,model,served_model,swapped,effort,input_tokens,output_tokens,cache_write_tokens,cache_read_tokens,reasoning_tokens,cost_usd,duration_ms,ttft_ms,status,error,session,kind,provider_key_id,provider_key_name,provider_account,route_id,request_id,endpoint,error_message,error_type,source,rejected,session_provider,session_account,session_official_login,caller_key_id,caller_key_name,response_id,upstream,cache_write_5m_tokens,cache_write_1h_tokens,tries,handed_on,tier_asked,tier_sent,tier_served,upstream_stop,credits
```

`sessions --json`：

```json
[]
```

`sessions --days 1 --json`（TZ=Asia/Taipei）：

```json
{
  "from": "2026-10-29",
  "to": "2026-10-29",
  "input": 0,
  "output": 0,
  "cache_read": 0,
  "cache_write": 0,
  "cost": 0,
  "unpriced": [],
  "active": 0,
  "days_used": 0,
  "days": [
    {
      "date": "2026-10-29",
      "input": 0,
      "output": 0,
      "cache_read": 0,
      "cache_write": 0,
      "cost": 0,
      "active": 0
    }
  ],
  "models": [],
  "folders": []
}
```

`plugin dump --json` 的实测默认结果：

```json
{
  "file": "/fixture/config/magpie/plugins.yml",
  "there": true,
  "preset": "all",
  "api": "1.0",
  "plugins": [
    {
      "id": "budget",
      "enabled": true,
      "source": "default",
      "chosen": true,
      "inUse": 0,
      "state": "active"
    },
    {
      "id": "gateway",
      "enabled": true,
      "source": "default",
      "chosen": true,
      "inUse": 0,
      "config": {
        "callsPage": 20
      },
      "configSources": {
        "callsPage": "default"
      },
      "state": "active"
    },
    {
      "id": "groups",
      "enabled": true,
      "source": "default",
      "chosen": true,
      "inUse": 0,
      "state": "active"
    },
    {
      "id": "library",
      "enabled": true,
      "source": "default",
      "chosen": true,
      "inUse": 0,
      "state": "active"
    },
    {
      "id": "middleware",
      "enabled": true,
      "source": "default",
      "chosen": true,
      "inUse": 0,
      "state": "active"
    },
    {
      "id": "otel",
      "enabled": true,
      "source": "default",
      "chosen": true,
      "inUse": 0,
      "state": "active"
    },
    {
      "id": "redact",
      "enabled": true,
      "source": "default",
      "chosen": true,
      "inUse": 0,
      "state": "active"
    },
    {
      "id": "rotation",
      "enabled": true,
      "source": "default",
      "chosen": true,
      "inUse": 0,
      "state": "active"
    },
    {
      "id": "routing",
      "enabled": true,
      "source": "default",
      "chosen": true,
      "inUse": 0,
      "state": "active"
    },
    {
      "id": "search",
      "enabled": true,
      "source": "default",
      "chosen": true,
      "inUse": 0,
      "state": "active"
    },
    {
      "id": "sessions",
      "enabled": true,
      "source": "default",
      "chosen": true,
      "inUse": 0,
      "state": "active"
    },
    {
      "id": "tools",
      "enabled": true,
      "source": "default",
      "chosen": true,
      "inUse": 0,
      "state": "active"
    },
    {
      "id": "usage",
      "enabled": true,
      "source": "default",
      "chosen": true,
      "inUse": 0,
      "state": "active"
    },
    {
      "id": "warmups",
      "enabled": true,
      "source": "default",
      "chosen": true,
      "inUse": 0,
      "state": "active"
    }
  ]
}
```

`agents` 在 Codex 目录不存在时的完整名称/空态列表（68 行）：

```text
  Claude Code               not detected  
  Claude Desktop            not detected  
  Claude Science            not detected  
  Codex                     not detected  
  Gemini CLI                not detected  
  Antigravity CLI           not detected  
  OpenCode                  not detected  
  OpenChamber               not detected  
  MiMo Code                 not detected  
  DevEco Code               not detected  
  Pi                        not detected  
  StepCode                  not detected  
  MothX                     not detected  
  PenguinHarness            not detected  
  Aside                     not detected  
  OmO                       not detected  
  Prime Agent               not detected  
  Goose                     not detected  
  Cursor CLI                not detected  
  Cursor Private Inference  not detected  
  Zed                       not detected  
  VS Code                   not detected  
  VS Code Insiders          not detected  
  VSCodium                  not detected  
  Zoo Code                  not detected  
  Copilot (JetBrains)       not detected  
  JetBrains Air             not detected  
  Copilot CLI               not detected  
  Crush                     not detected  
  DeepSeek Harness          not detected  
  Reasonix Studio           not detected  
  Command Code              not detected  
  fx                        not detected  
  omp                       not detected  
  Devin                     not detected  
  Hermes Agent              not detected  
  Mister Morph              not detected  
  Kimi Code                 not detected  
  Qwen Code                 not detected  
  Muse Code                 not detected  
  Empryo                    not detected  
  Ante                      not detected  
  AstrBot                   not detected  
  jcode                     not detected  
  MiniMax Code              not detected  
  Droid                     not detected  
  Cline                     not detected  
  Qoder                     not detected  
  Qoder CN                  not detected  
  Grok Build                not detected  
  Grok App                  not detected  
  ZCode                     not detected  
  WorkBuddy                 not detected  
  WorkBuddy AI              not detected  
  CodeBuddy                 not detected  
  Pencil                    not detected  
  OpenPencil                not detected  
  NewMax                    not detected  
  Raycast                   not detected  
  Kiro Crew                 not detected  
  T3 Code                   not detected  
  OpenHanako                not detected  
  AtomCode                  not detected  
  Snow CLI                  not detected  
  Alma                      not detected  
  Cindy                     not detected  
  AionUi                    not detected  
  Lody                      not detected
```

## 自动生成的文件

| 相对 `/fixture` 的路径 | 大小 bytes | SHA-256 |
|---|---:|---|
| `config/magpie/caller-keys.json.lock` | 0 | `e3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855` |
| `config/magpie/plugins.yml` | 167 | `f82def14b02909ffe03272df201f09b3e47b2028db2ae845325e7f2e9961113c` |
| `config/magpie/plugins.yml.lock` | 0 | `e3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855` |

46 次中，13 次帮助调用结束时无普通文件；其余 33 次得到上面同一集合及相同哈希。仅登记合成文件名、大小、摘要，没有保留任何凭据文件内容。没有创建 usage.jsonl、Codex config.toml、账户、Provider、会话或 profile 文件。程序输出指向这些路径不表示文件存在。

## 逐次隔离与输出审计

下表保存实际主机 bwrap PID、guest net/PID namespace、stdout/stderr UTF-8 字节 SHA-256、输出字节数。全部 bwrap 退出码为 0、全部 guest 用户/网络/PID/挂载 namespace 与宿主不同。全部命令前普通文件为空；全部网络前后快照一致，lo 收发包及字节均为 0，TCP/TCP6/UDP/UDP6 表均只有表头；全部套接字创建测试 EPERM。每次退出后 bwrap PID 均已消失。

首次 namespace 对照：

```json
{
  "host": {
    "user": "user:[4026531837]",
    "net": "net:[4026531833]",
    "pid": "pid:[4026531836]",
    "mnt": "mnt:[4026531832]",
    "ipc": "ipc:[4026531839]",
    "uts": "uts:[4026531838]",
    "cgroup": "cgroup:[4026531835]"
  },
  "guest": {
    "user": "user:[4026533853]",
    "net": "net:[4026533740]",
    "pid": "pid:[4026533737]",
    "mnt": "mnt:[4026533729]",
    "ipc": "ipc:[4026533736]",
    "uts": "uts:[4026533735]",
    "cgroup": "cgroup:[4026533738]"
  },
  "socket_creation_errno": {
    "2": 1,
    "10": 1,
    "1": 1
  }
}
```

| # | bwrap PID | guest net | guest PID namespace | stdout bytes / SHA-256 | stderr bytes / SHA-256 |
|---|---:|---|---|---|---|
| 01 | 720731 | `net:[4026533740]` | `pid:[4026533737]` | 16 / `fbab48ed35591f7205ac0bd63a4b780c4a09d874ec3996580bec3710ffef36cc` | 0 / `e3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855` |
| 02 | 720770 | `net:[4026533740]` | `pid:[4026533737]` | 11767 / `fea3899d6d2410e8d6506ab32f7f9c3fa552c174442a88a063c5af4b448cca50` | 0 / `e3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855` |
| 03 | 721717 | `net:[4026533742]` | `pid:[4026533740]` | 1552 / `085720008b9326cc82a4b932e6922697df3baded23580ffd4272016bf2504df6` | 0 / `e3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855` |
| 04 | 721741 | `net:[4026533856]` | `pid:[4026533758]` | 2188 / `422c04f9d94a5939cddd04366370b347340fbc7a81531fccae8092d7dcd725cc` | 0 / `e3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855` |
| 05 | 721758 | `net:[4026533743]` | `pid:[4026533741]` | 417 / `8bc7efe7cc74a8a3e3797842c20772e57ed4100efa3135670bc7943242756cc6` | 0 / `e3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855` |
| 06 | 721776 | `net:[4026533761]` | `pid:[4026533758]` | 103 / `a0224bee806f3fb4ae4bf72d2d158e6d701f8f60494694a932736910b1190684` | 0 / `e3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855` |
| 07 | 721792 | `net:[4026533745]` | `pid:[4026533743]` | 643 / `92c0a5d4adae237840596818b77b75e830f002e9b819100a2017d1ea51473825` | 0 / `e3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855` |
| 08 | 721816 | `net:[4026533761]` | `pid:[4026533744]` | 104 / `fe5fbc0f9d408f2a1f104fbffd64776f77b9e3daf0053ec6a3ee8568456eb673` | 0 / `e3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855` |
| 09 | 721833 | `net:[4026533745]` | `pid:[4026533742]` | 1384 / `d055c96992f631ecc0462b569a54f5a78f12cd07ee649e3f3af8e09379cd02a1` | 0 / `e3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855` |
| 10 | 721852 | `net:[4026533761]` | `pid:[4026533744]` | 451 / `6c139ee324f62b2716fdc0b4658f1fa2ed47bef083101810a256f4917bf1bae9` | 0 / `e3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855` |
| 11 | 721891 | `net:[4026533747]` | `pid:[4026533745]` | 74 / `3e09c4f5ffad2a6b26c20b42c7c66a58cda309b9a5140fe8b9b5bb0f6734d0b0` | 0 / `e3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855` |
| 12 | 721910 | `net:[4026533761]` | `pid:[4026533744]` | 9965 / `c576706b8f4eb8a1d023f51868e394e32b10c812668391d43546ca601d950ed3` | 0 / `e3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855` |
| 13 | 721932 | `net:[4026533748]` | `pid:[4026533746]` | 553 / `58fe13f5e745b0e1547b7164445b0b300ef8e629991d5b6404a38d7eda86d667` | 0 / `e3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855` |
| 14 | 721951 | `net:[4026533761]` | `pid:[4026533744]` | 61 / `8dc312edada9f9e659835a35e70e305dcfc55acc4ab54c95b9456083fbbb3088` | 0 / `e3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855` |
| 15 | 723099 | `net:[4026533742]` | `pid:[4026533740]` | 3018 / `b80e0f4d6d53183bf12666ff1e5487a732d47c18bd4fd2a6ba5dde25ca2aab86` | 0 / `e3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855` |
| 16 | 723119 | `net:[4026533856]` | `pid:[4026533758]` | 51 / `04859ac994a9a4cbc1a34526542aa1559d219e4a2a286ae1ce3329aaf071fe71` | 0 / `e3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855` |
| 17 | 723140 | `net:[4026533744]` | `pid:[4026533742]` | 38 / `8ea73690ec16ec0d8c336edf897da31c70e3e4f783e71ff59777dd10ed1a7b06` | 0 / `e3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855` |
| 18 | 723157 | `net:[4026533758]` | `pid:[4026533743]` | 87 / `9a821e4dc418dcefff88ae0c65cfd3e97bb82ca75d038420e0c7b15ec91b9c0b` | 0 / `e3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855` |
| 19 | 723180 | `net:[4026533745]` | `pid:[4026533742]` | 75 / `6d8517f679b702c4c334b7250df37973abd599b42679b00be4a0aed9e59a7cdc` | 0 / `e3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855` |
| 20 | 723199 | `net:[4026533758]` | `pid:[4026533743]` | 211 / `641d7a7fb005ea047cba51d70748641f1db52f58037153660a749fa0098525e4` | 0 / `e3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855` |
| 21 | 723220 | `net:[4026533745]` | `pid:[4026533742]` | 86 / `99be7bc5335ab7e3ffdd293e82f3d31426aefc429973e5f850dffcec7f0ce575` | 0 / `e3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855` |
| 22 | 723238 | `net:[4026533758]` | `pid:[4026533743]` | 51 / `04859ac994a9a4cbc1a34526542aa1559d219e4a2a286ae1ce3329aaf071fe71` | 0 / `e3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855` |
| 23 | 723257 | `net:[4026533745]` | `pid:[4026533742]` | 21 / `da11fb1127a100d18d97dbbfc5bc4f27a8e30ddea716ad4632883e4bc4be866b` | 0 / `e3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855` |
| 24 | 723275 | `net:[4026533758]` | `pid:[4026533743]` | 171 / `dbe30830fc32552608b5e1249906894b4abeb7308ac2011fd8acf41cbcd32694` | 0 / `e3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855` |
| 25 | 723299 | `net:[4026533745]` | `pid:[4026533742]` | 0 / `e3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855` | 0 / `e3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855` |
| 26 | 723322 | `net:[4026533761]` | `pid:[4026533744]` | 101 / `7d1a5aa961b824409406507c37a554ff0132dc06afb775cc93febe36f8ceb3f1` | 0 / `e3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855` |
| 27 | 723344 | `net:[4026533746]` | `pid:[4026533742]` | 71 / `7794e5c34868f9c5e7d89f09c978941e77cddc82cddd7cd58796b6af1a2b776d` | 0 / `e3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855` |
| 28 | 723361 | `net:[4026533761]` | `pid:[4026533744]` | 3 / `37517e5f3dc66819f61f5a7bb8ace1921282415f10551d2defa5c3eb0985b570` | 0 / `e3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855` |
| 29 | 723380 | `net:[4026533745]` | `pid:[4026533742]` | 613 / `6d45ea6fbd1d76ffe976a291616ea4a27da22c5bc28f9c7f38e98ab9916cbc3d` | 0 / `e3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855` |
| 30 | 723399 | `net:[4026533761]` | `pid:[4026533744]` | 2332 / `5c84d759740a246f6ed3a2e97d36fb5a2224ad170bfba391c06057b3756018bb` | 0 / `e3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855` |
| 31 | 723416 | `net:[4026533745]` | `pid:[4026533741]` | 54 / `c801f5cf52495a93b3d2d51bb257a0a1d4ff9b6cd7eb6f529a2d71eb544b1eea` | 0 / `e3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855` |
| 32 | 723434 | `net:[4026533761]` | `pid:[4026533744]` | 120 / `3343461f42234ba85f3ba141526907ad9a8de1ce9c56a7c53fce61e041331560` | 0 / `e3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855` |
| 33 | 723452 | `net:[4026533746]` | `pid:[4026533743]` | 113 / `509f45c5137a31ae59ec452583baca3da9620170afbb28b9d1bb1fde9225c5ec` | 0 / `e3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855` |
| 34 | 723505 | `net:[4026533761]` | `pid:[4026533744]` | 116 / `31f6455958c042ed32f30a32d714ed2d46f9bf36283507696f4a1f43c1a450b8` | 0 / `e3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855` |
| 35 | 723540 | `net:[4026533745]` | `pid:[4026533742]` | 554 / `510cc4e91a9edc7562cf54bb7ad3597ebd8ef9570eab6262124b064b07c2cdf4` | 0 / `e3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855` |
| 36 | 723558 | `net:[4026533760]` | `pid:[4026533743]` | 118 / `d2fa7b3c8e29b273e7975d67f88710400031d0f9ba2cbc8b50f72f2c1d2f4bce` | 0 / `e3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855` |
| 37 | 723577 | `net:[4026533745]` | `pid:[4026533742]` | 118 / `d2fa7b3c8e29b273e7975d67f88710400031d0f9ba2cbc8b50f72f2c1d2f4bce` | 0 / `e3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855` |
| 38 | 723598 | `net:[4026533761]` | `pid:[4026533744]` | 132 / `d64bdf01ad410c9c86b0bcfc52bb55b746fea29c78470f3eeeb506899cc64db4` | 0 / `e3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855` |
| 39 | 723616 | `net:[4026533745]` | `pid:[4026533741]` | 3 / `37517e5f3dc66819f61f5a7bb8ace1921282415f10551d2defa5c3eb0985b570` | 0 / `e3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855` |
| 40 | 723633 | `net:[4026533760]` | `pid:[4026533742]` | 390 / `4569cb5e4b8d7e72596c8ee4c82f81ea863618eb582b2f65b98fa5c10d632887` | 0 / `e3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855` |
| 41 | 723668 | `net:[4026533746]` | `pid:[4026533744]` | 29 / `cfc27c2cbdfdebf4ca28b85de2f7e5043b9f415026dee1cd609e1cd2ec7e2515` | 0 / `e3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855` |
| 42 | 723706 | `net:[4026533745]` | `pid:[4026533741]` | 60 / `b48163acf779a39423abff9c1838516eb9045e5b6b4dec8d32629995cdaaa0f8` | 0 / `e3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855` |
| 43 | 730332 | `net:[4026533740]` | `pid:[4026533737]` | 2924 / `8e01e78fb86e3fb8cd2493aeecccde601b3d898c3a9d2e1fd1589b97b854fa0f` | 0 / `e3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855` |
| 44 | 730375 | `net:[4026533852]` | `pid:[4026533754]` | 0 / `e3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855` | 50 / `622c0d989a6bf9be2f9d0329b60ab390114c83951484a132b05b796cae36edf0` |
| 45 | 730396 | `net:[4026533742]` | `pid:[4026533740]` | 21 / `da11fb1127a100d18d97dbbfc5bc4f27a8e30ddea716ad4632883e4bc4be866b` | 0 / `e3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855` |
| 46 | 731776 | `net:[4026533740]` | `pid:[4026533737]` | 57 / `962e722102caea103b6750ad379d6af3a5cba262c406510b98435071896ff772` | 0 / `e3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855` |

单次耗时仅为采集诊断数据，未建立性能基线，也没有在这类受限沙箱中推断正常桌面或网络模式的资源表现。

## 实际指令和复现方法

有效调用的逐字 argv 已列于前表。以下是首次调用的 bubblewrap 前缀；数字 FD 指向当次由执行器产生的合成配置及 seccomp memfd，不是可复用的宿主文件路径：

```json
[
  "/usr/bin/bwrap",
  "--unshare-user",
  "--unshare-all",
  "--disable-userns",
  "--new-session",
  "--die-with-parent",
  "--clearenv",
  "--cap-drop",
  "ALL",
  "--ro-bind",
  "/usr",
  "/usr",
  "--symlink",
  "usr/lib",
  "/lib",
  "--symlink",
  "usr/lib",
  "/lib64",
  "--proc",
  "/proc",
  "--remount-ro",
  "/proc",
  "--dev",
  "/dev",
  "--remount-ro",
  "/dev",
  "--size",
  "25165824",
  "--tmpfs",
  "/fixture",
  "--perms",
  "0700",
  "--dir",
  "/fixture/home",
  "--perms",
  "0700",
  "--dir",
  "/fixture/codex",
  "--perms",
  "0700",
  "--dir",
  "/fixture/config",
  "--perms",
  "0700",
  "--dir",
  "/fixture/cache",
  "--perms",
  "0700",
  "--dir",
  "/fixture/data",
  "--perms",
  "0700",
  "--dir",
  "/fixture/state",
  "--perms",
  "0700",
  "--dir",
  "/fixture/run",
  "--perms",
  "0700",
  "--dir",
  "/fixture/bin",
  "--perms",
  "0700",
  "--dir",
  "/fixture/tmp",
  "--perms",
  "0700",
  "--dir",
  "/fixture/work",
  "--symlink",
  "/fixture/tmp",
  "/tmp",
  "--symlink",
  "/fixture/run",
  "/run",
  "--dir",
  "/etc",
  "--ro-bind-data",
  "4",
  "/etc/passwd",
  "--ro-bind-data",
  "5",
  "/etc/group",
  "--ro-bind-data",
  "6",
  "/etc/hosts",
  "--ro-bind-data",
  "7",
  "/etc/nsswitch.conf",
  "--ro-bind-data",
  "8",
  "/etc/resolv.conf",
  "--setenv",
  "HOME",
  "/fixture/home",
  "--setenv",
  "CODEX_HOME",
  "/fixture/codex",
  "--setenv",
  "XDG_CONFIG_HOME",
  "/fixture/config",
  "--setenv",
  "XDG_CACHE_HOME",
  "/fixture/cache",
  "--setenv",
  "XDG_DATA_HOME",
  "/fixture/data",
  "--setenv",
  "XDG_STATE_HOME",
  "/fixture/state",
  "--setenv",
  "XDG_RUNTIME_DIR",
  "/fixture/run",
  "--setenv",
  "PATH",
  "/fixture/bin",
  "--setenv",
  "TMPDIR",
  "/fixture/tmp",
  "--setenv",
  "LANG",
  "C.UTF-8",
  "--setenv",
  "TZ",
  "Asia/Taipei",
  "--setenv",
  "MAGPIE_NO_ACCOUNTS",
  "1",
  "--seccomp",
  "3",
  "--remount-ro",
  "/",
  "--chdir",
  "/fixture/work"
]
```

运行形式为上述前缀加 `/usr/bin/python3 -B -c <GUEST> /usr/bin/magpie <已列参数>`。GUEST 在启动 Magpie 前核对隔离、空状态、容量和 socket 拒绝，使用非交互 stdin，采集 stdout/stderr/退出码并限制时长，结束后清理该私有 PID namespace 中自己的进程。没有启动 shell，也没有放开继承宿主环境。

下面保留本次临时执行器文本以便审阅/复现，工作副本会清理。复现时应将其置于新建的 mode-0700 私有临时目录，只传入前表已确认的只读 argv，并在结束后删除该目录；此脚本本身不会自动删除留作审阅的 result-*.json。第 43–46 次是导入模块后设置 `CREATE_CODEX_DIR=False` 再调用 `run([...])`。不要在宿主裸跑这些 Magpie 指令。

临时执行器最终文本 SHA-256：`7da0df8e8ff42a5d8a90c1becd0d65effa3e0defefec0720431e2d0ee43fb4ff`。

<details>
<summary>临时执行器完整文本</summary>

```python
#!/usr/bin/python3
import ctypes, datetime, errno, hashlib, json, os, pathlib, resource, signal, subprocess, sys, time
ROOT = pathlib.Path(__file__).resolve().parent
FILE_CAP = 8 * 1024 * 1024
TOTAL_CAP = 24 * 1024 * 1024
CREATE_CODEX_DIR = True
GUEST = r"""
import datetime, errno, hashlib, json, os, pathlib, resource, selectors, signal, socket, subprocess, sys, time
resource.setrlimit(resource.RLIMIT_FSIZE, (8388608, 8388608))
resource.setrlimit(resource.RLIMIT_CORE, (0, 0))
resource.setrlimit(resource.RLIMIT_NOFILE, (128, 128))
os.umask(0o077)
base = pathlib.Path('/fixture')
def snap():
    files=[]
    for p in sorted(base.rglob('*')):
        if p.is_file() and not p.is_symlink():
            s=p.stat()
            files.append({'path':str(p),'bytes':s.st_size,'sha256':hashlib.sha256(p.read_bytes()).hexdigest()})
    return files
def network():
    return {k:pathlib.Path('/proc/net/'+v).read_text() for k,v in [('interfaces','dev'),('ipv4_routes','route'),('ipv6_routes','ipv6_route'),('tcp','tcp'),('tcp6','tcp6'),('udp','udp'),('udp6','udp6')]}
ns={n:os.readlink('/proc/self/ns/'+n) for n in ['user','net','pid','mnt','ipc','uts','cgroup']}
net_before=network()
interfaces=[line.split(':')[0].strip() for line in net_before['interfaces'].splitlines()[2:] if ':' in line]
assert interfaces==['lo'], interfaces
assert not any(line.split()[1]=='00000000' and line.split()[2]!='00000000' for line in net_before['ipv4_routes'].splitlines()[1:])
assert not pathlib.Path('/home/lightjunction').exists()
assert not pathlib.Path('/etc/resolv.conf').read_text().strip()
assert pathlib.Path('/fixture/bin').is_dir() and not list(pathlib.Path('/fixture/bin').iterdir())
assert not any(k.startswith(('DBUS','DISPLAY','WAYLAND','SSH','HTTP_PROXY','HTTPS_PROXY','ALL_PROXY')) for k in os.environ)
assert os.statvfs(base).f_blocks * os.statvfs(base).f_frsize == 25165824
socket_checks={}
for family in [socket.AF_INET,socket.AF_INET6,socket.AF_UNIX]:
    try:
        s=socket.socket(family,socket.SOCK_STREAM)
    except OSError as e:
        socket_checks[str(family)] = e.errno
        assert e.errno==errno.EPERM
    else:
        s.close()
        raise RuntimeError('socket_not_blocked')
before=snap()
start=time.monotonic()
p=subprocess.Popen(sys.argv[1:], stdin=subprocess.DEVNULL,stdout=subprocess.PIPE,stderr=subprocess.PIPE,start_new_session=True)
selector=selectors.DefaultSelector()
selector.register(p.stdout,selectors.EVENT_READ,'stdout')
selector.register(p.stderr,selectors.EVENT_READ,'stderr')
streams={'stdout':bytearray(),'stderr':bytearray()}
seen={'stdout':0,'stderr':0}
reason=None
peak_bytes=0
peak_file=0
while selector.get_map():
    files=snap()
    peak_bytes=max(peak_bytes,sum(f['bytes'] for f in files))
    peak_file=max([peak_file]+[f['bytes'] for f in files])
    if peak_bytes>25165824 or peak_file>8388608:
        reason='size_limit'
    if time.monotonic()-start>10:
        reason='timeout_10s'
    if reason:
        try: os.killpg(p.pid,signal.SIGKILL)
        except ProcessLookupError: pass
    for key,_ in selector.select(0.04):
        data=os.read(key.fileobj.fileno(),16384)
        if not data:
            selector.unregister(key.fileobj)
            key.fileobj.close()
            continue
        seen[key.data]+=len(data)
        remaining=max(0,65536-len(streams[key.data]))
        streams[key.data].extend(data[:remaining])
        if seen[key.data]>65536:
            reason='output_limit_64KiB'
    if reason and time.monotonic()-start>12:
        break
p.wait(timeout=2)
# Remove even background children belonging to the command's private session.
try: os.killpg(p.pid,signal.SIGKILL)
except ProcessLookupError: pass
remaining=[]
for proc in pathlib.Path('/proc').iterdir():
    if proc.name.isdecimal() and int(proc.name) not in (1,os.getpid()):
        try:
            stat=(proc/'stat').read_text().split(') ',1)[1].split()
            if stat[0]!='Z': remaining.append(int(proc.name))
        except FileNotFoundError: pass
if remaining:
    for pid in remaining:
        try: os.kill(pid,signal.SIGKILL)
        except ProcessLookupError: pass
    reason=reason or 'unexpected_remaining_processes'
after=snap()
peak_bytes=max(peak_bytes,sum(f['bytes'] for f in after))
peak_file=max([peak_file]+[f['bytes'] for f in after])
result={'argv':sys.argv[1:],'exit_code':p.returncode,'duration_ms':round((time.monotonic()-start)*1000,2),'termination':reason,'stdout':streams['stdout'].decode('utf-8','replace'),'stderr':streams['stderr'].decode('utf-8','replace'),'output_bytes':seen,'namespaces':ns,'network_before':net_before,'network_after':network(),'socket_errno_checks':socket_checks,'mountinfo':pathlib.Path('/proc/self/mountinfo').read_text(),'environment':dict(os.environ),'rlimit_fsize':list(resource.getrlimit(resource.RLIMIT_FSIZE)),'tmpfs_capacity_bytes':os.statvfs(base).f_blocks*os.statvfs(base).f_frsize,'files_before':before,'files_after':after,'peak_observed_file_bytes':peak_file,'peak_observed_total_bytes':peak_bytes,'unexpected_processes':remaining}
print(json.dumps(result,ensure_ascii=False),flush=True)
"""

def now():
    return datetime.datetime.now(datetime.timezone.utc).isoformat()

def seccomp():
    lib=ctypes.CDLL('libseccomp.so.2',use_errno=True)
    lib.seccomp_init.argtypes=[ctypes.c_uint32]
    lib.seccomp_init.restype=ctypes.c_void_p
    lib.seccomp_syscall_resolve_name.argtypes=[ctypes.c_char_p]
    lib.seccomp_syscall_resolve_name.restype=ctypes.c_int
    lib.seccomp_rule_add.argtypes=[ctypes.c_void_p,ctypes.c_uint32,ctypes.c_int,ctypes.c_uint]
    lib.seccomp_export_bpf.argtypes=[ctypes.c_void_p,ctypes.c_int]
    lib.seccomp_release.argtypes=[ctypes.c_void_p]
    ctx=lib.seccomp_init(0x7fff0000)
    if not ctx: raise RuntimeError('seccomp_init_failed')
    fd=os.memfd_create('magpie-cli36-no-network',0)
    blocked=['socket','socketpair','connect','bind','listen','sendto','sendmsg','sendmmsg']
    try:
        for name in blocked:
            nr=lib.seccomp_syscall_resolve_name(name.encode())
            if nr<0 or lib.seccomp_rule_add(ctx,0x50000|errno.EPERM,nr,0):
                raise RuntimeError('seccomp_rule_failed:'+name)
        if lib.seccomp_export_bpf(ctx,fd): raise RuntimeError('seccomp_export_failed')
    finally: lib.seccomp_release(ctx)
    os.lseek(fd,0,os.SEEK_SET)
    return fd,blocked

def run(args):
    fds=[]
    sc,blocked=seccomp()
    fds.append(sc)
    command=['/usr/bin/bwrap','--unshare-user','--unshare-all','--disable-userns','--new-session','--die-with-parent','--clearenv','--cap-drop','ALL','--ro-bind','/usr','/usr','--symlink','usr/lib','/lib','--symlink','usr/lib','/lib64','--proc','/proc','--remount-ro','/proc','--dev','/dev','--remount-ro','/dev','--size',str(TOTAL_CAP),'--tmpfs','/fixture']
    for d in ['home','codex','config','cache','data','state','run','bin','tmp','work']:
        if d == 'codex' and not CREATE_CODEX_DIR: continue
        command+=['--perms','0700','--dir','/fixture/'+d]
    command+=['--symlink','/fixture/tmp','/tmp','--symlink','/fixture/run','/run','--dir','/etc']
    synthetic={'passwd':f'fixture:x:{os.getuid()}:{os.getgid()}:Synthetic:/fixture/home:/usr/bin/false\n','group':f'fixture:x:{os.getgid()}:\n','hosts':'127.0.0.1 localhost\n::1 localhost\n','nsswitch.conf':'passwd: files\ngroup: files\nhosts: files\n','resolv.conf':''}
    for name,content in synthetic.items():
        fd=os.memfd_create('magpie-cli36-'+name,0)
        os.write(fd,content.encode())
        os.lseek(fd,0,os.SEEK_SET)
        fds.append(fd)
        command+=['--ro-bind-data',str(fd),'/etc/'+name]
    env={'HOME':'/fixture/home','CODEX_HOME':'/fixture/codex','XDG_CONFIG_HOME':'/fixture/config','XDG_CACHE_HOME':'/fixture/cache','XDG_DATA_HOME':'/fixture/data','XDG_STATE_HOME':'/fixture/state','XDG_RUNTIME_DIR':'/fixture/run','PATH':'/fixture/bin','TMPDIR':'/fixture/tmp','LANG':'C.UTF-8','TZ':'Asia/Taipei','MAGPIE_NO_ACCOUNTS':'1'}
    for k,v in env.items(): command+=['--setenv',k,v]
    command+=['--seccomp',str(sc),'--remount-ro','/','--chdir','/fixture/work','/usr/bin/python3','-B','-c',GUEST,'/usr/bin/magpie']+args
    host_ns={n:os.readlink('/proc/self/ns/'+n) for n in ['user','net','pid','mnt','ipc','uts','cgroup']}
    before=now()
    p=None
    try:
        p=subprocess.Popen(command,stdin=subprocess.DEVNULL,stdout=subprocess.PIPE,stderr=subprocess.PIPE,pass_fds=fds,start_new_session=True,env={'PATH':'/usr/bin','LANG':'C.UTF-8'})
        pid=p.pid
        try:
            out,err=p.communicate(timeout=16)
        except subprocess.TimeoutExpired:
            p.kill()
            out,err=p.communicate(timeout=3)
            raise RuntimeError('host_supervisor_timeout:'+str(pid))
        if p.returncode:
            raise RuntimeError('bwrap_failed:'+str(p.returncode)+':'+err.decode('utf-8','replace')[:5000])
        result=json.loads(out)
        for n in ['user','net','pid','mnt']:
            if result['namespaces'][n]==host_ns[n]: raise RuntimeError('namespace_shared:'+n)
        result.update(fixture_variant='precreated-empty-codex-dir' if CREATE_CODEX_DIR else 'absent-codex-dir',started_utc=before,ended_utc=now(),bwrap_pid=pid,bwrap_exit_code=p.returncode,bwrap_stderr=err.decode('utf-8','replace'),host_namespaces=host_ns,host_pid_removed=not pathlib.Path('/proc',str(pid)).exists(),socket_syscalls_blocked=blocked,bwrap_prefix=command[:command.index('/usr/bin/python3')],guest_harness_sha256=hashlib.sha256(GUEST.encode()).hexdigest())
        index=len(list(ROOT.glob('result-*.json')))+1
        destination=ROOT/f'result-{index:03}.json'
        destination.write_text(json.dumps(result,ensure_ascii=False,indent=2)+'\n')
        destination.chmod(0o600)
        print(json.dumps({'result':destination.name,'argv':result['argv'],'exit_code':result['exit_code'],'duration_ms':result['duration_ms'],'termination':result['termination'],'stdout':result['stdout'][:1800],'stderr':result['stderr'][:1000],'files_after':[{'path':x['path'],'bytes':x['bytes']} for x in result['files_after']],'host_pid_removed':result['host_pid_removed']},ensure_ascii=False),flush=True)
    finally:
        if p and p.poll() is None:
            p.kill()
            p.wait(timeout=3)
        for fd in fds: os.close(fd)

if __name__=='__main__': run(sys.argv[1:])
```

</details>

## 清理证据

下方记录在写入主体、删除本次私有工作目录后追加；旧证据与原项目均不在清理范围。

```json
{
  "checked_utc": "2026-10-28T21:37:27.134093+00:00",
  "temporary_host_work_directory": "/tmp/magpie-cli36-lpvtms_n",
  "temporary_host_work_directory_removed": true,
  "temporary_work_files_removed": 47,
  "temporary_work_logical_bytes_removed": 517172,
  "all_46_tracked_bwrap_pids_absent": true,
  "remaining_processes_in_tracked_guest_user_net_pid_mnt_namespaces": [],
  "all_46_bwrap_exit_codes_zero": true,
  "all_46_guest_process_lists_empty_after_command": true,
  "fixture_backing": "per-invocation private tmpfs; released with sandbox mount namespace",
  "host_fixture_bind_mounts": 0,
  "all_network_snapshots_unchanged": true,
  "all_inet_inet6_unix_socket_creation_tests_errno_eperm": true,
  "previous_web_fixture_still_absent": true,
  "project_code_or_configuration_written": false,
  "remaining_deliverable": "/home/lightjunction/.cache/magpie-rs-release/pro-ui-handoff/20261028-magpie-product-superiority/36-original-cli-behavior.md"
}
```

本次仅保留此 Markdown 交付物；临时执行器及 46 份运行记录工作副本已经删除，必要短输出、哈希、进程和隔离证据已写入本文。没有清理旧 28/24 证据，没有改变宿主安装、服务、设置、账户或项目工作区。
