# 重建约定

完整阅读 RECIPE.md、BUILD_PROMPT.md、ACCEPTANCE.md 和递归依赖；依赖说明仅在其包作用域内有效。
生成实现源码位于仓库根目录 generated/ 下的独立 Git 子模块 generated/magpie-rs-source/，详见 GENERATED_LAYOUT.md。修改前核对子模块 remote、pin 和工作区；不要把源码文件放到 generated/ 根目录。配方目录只保留 UTF-8 Markdown。
在配方目录之外实现软件。配方内只保留 UTF-8 Markdown，不加入源码、生成物、二进制、文件系统链接或执行钩子。
实现主语言为 Rust；桌面 GUI 为 GPUI。CLI/headless 必须独立于 GPUI 的显示和 GPU 初始化。
以规格定义目标，以 EVIDENCE/未知项定义证据边界。当前源码缺失某功能不代表该需求被删掉。
验收结果逐项记录平台、输入、操作、实际输出及通过/失败/未运行；不得用模拟供应商成功替代真实账户兼容验收。
用户设置和密钥优先，任何配置变更都要可撤销、可识别所有权、检测外部漂移；不要读取真实账号来填充配方。

根配方quality依赖的设计、代码、性能验收适用全部组件；GitHub发布前按PUBLISHING.md检查recipkg topic。
