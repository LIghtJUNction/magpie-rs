# 原版隔离 Web 行为观察

2026-10-29，原版 `/usr/bin/magpie` 自报 `0.1.1948`，SHA-256 `9aa760cbac67f375a810fcf15b9adf04edcb2cc7de64a9ebb0963da15c1d3eb7`。观察对象是该本机二进制的 Web 页面，不代表官网截图的版本，也不是 GPUI 桌面窗口。隔离 HOME/XDG、空 PATH 和合成空 Codex 目录；无真实账号或密钥。原版运行在独立网络命名空间，仅有 loopback；通过本机受限桥访问，浏览器使用专用 `.localhost` 子域以避免与用户其他本地应用共享该 host 的 Cookie。桥把 Host/Origin 转为原版 loopback 地址、限制外连并注入 CSP；这些是观察条件，不能据此断定原版自身的安全策略，也可能改变依赖源地址或外部资源的行为。

以下为可见控件、提示和明确列出的少量合成操作；未观察到的业务、真实登录、真实 Provider、其他语言、OS、浏览器窗口尺寸、失败恢复及进程重启均未通过。文案说明不等于实际网络请求或数据持久化验证。

## Agent 与导航

以一次性密钥登录后，主导航显示 Agents、Sessions、Library、Providers、Routing、Gateway、Usage、Masking、Plugins。空合成 Codex 目录使 Agents 显示 Codex `CLI not found`；页面还有两项新手进度、添加 Provider、Manage agents、安装其他 Agent 展开项及 Profiles。该样本有目录而没有可执行文件，不证明完全无配置目录时的桌面、Web 或其他入口行为。

## Provider：目录、表单与合成保存

空 Provider 页直接展示添加目录，分为订阅、Vendors、Relays、On this machine、Remote magpie 和自定义入口；另有 Import。输入 `DeepSeek` 时只看到对应厂商；输入 `fixture-nonexistent-xyz` 时显示无匹配，并提供按自定义 Provider 添加或查找插件的入口。

自定义表单可见名称、Base URL、直接输入 API key、显示/隐藏、图标、模型、获取模型及 Advanced/More endpoints。协议选项为 OpenAI compatible、OpenAI Responses、Anthropic compatible、Gemini compatible、System One。原版界面称密钥保存在 `~/.config/magpie/providers.json`、仅用户可读且不读取 shell；本次未填写密钥，不能据此断定实际加密、读取权限或其他平台行为。重建应支持用户直接输入密钥并遵守本配方的安全合同，不强制采用该存储格式。

Advanced 页面文字列出额外 Header、Extra body、Proxy、Concurrency、Requests per minute、Queue size/Queue wait、Price rate、Agent User-Agent、Rests、Account balance、Context window、Max output、Compact at。其说明中，额外 body 的原有请求字段默认保留，`!name` 表示替换；代理默认选 Global，User-Agent 传递默认关闭、Rests 默认开启；空/零并发及速率不限制，空/零队列大小不设界，空/零队列等待表示一直等待。此处为原版界面声明，不是本次实际流量验证，也不豁免重建的内存与安全要求。More endpoints 可见 Anthropic、Responses、Gemini、Models、Catalog、Balance URL、Balance method 和 Balance field。

按 Add 而不填写名称时提示 `Give it a name` 并聚焦名称；只填名称再按 Add 时提示 `A base URL is needed` 并聚焦地址。填写名称 `Fixture Relay`、地址 `http://127.0.0.1:42389/v1`、模型 `fixture-model`，不填 key，添加后列表显示名称、host、一个模型、`no key` 和启用开关。详情的 ID 为 `fixture-relay`，提示模型以 `fixture-relay/…` 供 Agent 选择；Picked only 选中。刷新页面后名称、地址和模型仍在。关闭开关后界面显示停止向 Agent 提供模型、请求不再发往此项，且保留密钥和设置；页面重载仍显示关闭，列表将其放进折叠的 `TURNED OFF 1`。这验证了页面重载，不验证服务进程重启或实际转发。

关闭后隔离目录 `config/magpie/providers.json` 的权限为 `0600`、大小 276 字节、SHA-256 `6fc687d49d307f1e7d6ce9ca9cda27dae010f53151ac2dcb72705a12ad4ce387`，内容仅为合成输入：

```json
{
  "providers": [
    {
      "id": "fixture-relay",
      "name": "Fixture Relay",
      "icon": "generic",
      "key": "",
      "chat": "http://127.0.0.1:42389/v1",
      "baseAPI": "chat",
      "models": ["fixture-model"],
      "off": true
    }
  ]
}
```

展示 JSON 为便于阅读而压缩了 `models` 数组的空白，SHA-256 对应原文件字节。添加后 Usage 的 Requests 页面出现一次 Magpie 自身 `model_list` 探测；隔离日志记录 `GET /v1/models` 到上述私有 loopback、连接拒绝，零 token。无法从此单次观察确定所有模型输入情况下的触发时机；它不是外部 Provider 请求，也不代表模型发现成功。

## Routing：观察到的额外策略和状态

无可用 Provider 时，Routing 显示等待请求、无请求、三个计数均为零、Routing groups 和创建入口；自动发现组开关及名称显示选项均可见。创建表单支持显式模型和匹配后续模型的 `*`/`re:` 模式。原版界面列出 Smart、In order、In turn、Least used、Weekly pace、Free first、Speed first、By weight、Manual 九种选择：已有七种策略的语义仍按递归规格检查；新增 Free first 与 Manual 是已经观察到的额外要求。

选择 Free first 后，说明称优先使用不消耗订阅额度或 credits 的成员，再用其他成员，成员内部仍按自身 Provider 账户/key 策略；选择 Manual 后，说明称使用用户在组卡片上选择的模型，失败不自动切换其他成员。两种策略仅观察了界面说明，没有发送请求，实际识别、优先顺序与故障表现仍未验收。

表单初始值包括 Smart、rate limited 保持位置、首响应 Wait、所有成员暂时繁忙后重试 1 分钟、最大成员上下文、Stays Auto、成员共有的推理等级；每项含可选值。空组点击 Add 提示 `A group needs a model in it` 并聚焦添加模型。改变策略后点 Cancel 显示丢弃未保存修改的确认；确认丢弃后回到原列表，本次未创建组。

## Usage：一个受限空状态

Overview 提供 Today/7 days/30 days/All/自定义时间、刷新与价格估计；它声明只计 Gateway calls，session-log calls 在 Requests，本地拒绝不计总数。Requests 可切 All/Failed/Retried、搜索、导出 CSV、Tokens/Cost/Requests/Speed 视图，并在本次显示上面的本机 model-list 失败记录。模型、时延、账务、图表、导出和真实上游的完整数据语义仍须在受控请求下逐项核对。

## Settings 与结束

Settings 依次可见 General、Allowances、Network and sharing、Models、Privacy、Observability、Sync and backup、About。General 中 Appearance 初始为 System，色盲模式和减少动画为 Off，Language 为 System，Gateway mode 为 Automatic；该 fixture 因发现合成 Codex 目录而显示当前模式为 off。另可见界面/代码字体、模型名称中的 Provider、阻止休眠设置；未改变或保存这些设置。

本轮 run `m28-34k65ir4` 于 2026-10-29 00:05:19 UTC 启动、00:13:45 UTC 显式停止；记录显示原版子进程收到终止信号、临时 fixture 已删除，宿主端口 53957 不再监听。原用户 GUI 进程仍在。本机保留不含登录 key 的受限启动/停止元数据与任务专用桥脚本；浏览器测试使用独立 host 的一次性会话，停止后其 key 已失效。
