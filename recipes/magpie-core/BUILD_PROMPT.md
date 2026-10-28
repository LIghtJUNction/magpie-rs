# 重建 Magpie 核心

用 Rust 实现可被 CLI 与后台服务共用的状态与操作核心。完整阅读 specs/01-product-and-agents.md、02-providers-and-accounts.md、04-routing-and-failover.md、07-library-sync-and-backup.md、09-privacy-security-and-platforms.md 和 ACCEPTANCE.md。

职责是 Agent 发现/能力/精确编辑/回滚/漂移，Provider/账户/catalog/quota，路由规则/组/affinity/fallback 选择，Library/MCP/projects/备份同步和本机隐私安全策略。该配方没有递归配方依赖。

向服务层暴露类型清楚的状态读取、显式变更、配置预览、提交/恢复、模型能力查询、授权策略、路由计划和变更通知。接口不要求网络或 GPUI 初始化；状态区分缺失、未知、过期、不可读、冲突和成功。模型 ID 使用 provider/model、组使用 group/id；账户暂停/退出要及时反映，失败不能转换成空设置。

配置提交必须检测读取后的第三方修改并原子替换，先持久化恢复所需的所有权记录。Disconnect 仅恢复仍匹配 Magpie 最近写入值的字段，保留用户后来改动。同步与备份使用同一数据规则，保留每台机器专属值。安全存储故障使认证/同步停止，不使用空密钥继续。

保持显式版本的数据格式和迁移策略；只持久化运行必需数据。不得加载 GPUI；避免全目录/全部会话常驻内存。Rust 主实现、低内存、多平台约束贯穿全部模块。供应商、Agent schema 与插件版本需在实现时确认，不能从历史样例猜最新兼容性。

与上层集成：相同操作从 CLI、GPUI、TUI、Web 进入后必须产生同一保存状态；网关从核心读取授权/路由/catalog，不复制另一套真值。错误和事件不能带原始密钥、prompt 正文或无关账号数据。
