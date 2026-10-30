# 本地状态与发布检查

## 2026-10-30 审阅分支

共享Provider与原版UI对齐源码已发布至 `improve/provider-ui-parity`，commit `452d59e0e1c3d96cd7110ebd6506c38448fa3198`，对应 [源码 PR #2](https://github.com/LIghtJUNction/magpie-rs-source/pull/2)。父仓库本轮固定同一gitlink并更新配方1.3.8、覆盖表及证据；main与此前发布历史没有改写。GitHub主仓的 `recipkg` topic已重新回读确认保留。

源码内容树 `b414809af6585abdeba9c1e925d5e57f8f74869d` 与本地已测试树一致。源码、父配方分别建立审阅PR；多平台Release、AUR及用户安装均未在本轮执行。当前打包声明补齐Linux D-Bus依赖，其他平台/完整安装流程仍需对应验证。后文各pin和发布说明保留为历史记录。

独立源码仓为 <https://github.com/LIghtJUNction/magpie-rs-source>。用户已授权本轮迭代后发布源码、主仓配方、多平台 Release，以及 `magpie-rs-bin` / `magpie-rs-git` AUR 包，并安装其中一个互斥变体。独立源码仓 `main` 已推送并回读 `4a967ec2eecb165630a7b246546bd3bd6362fc05`；主仓本次提交保存配方与该源码指针。用户已否决当前 UI 并要求 Pro 重做，AUR、Release 与替换安装均尚未执行，需分别记录后续证据。

发布准备时通过 `gh api repos/LIghtJUNction/magpie-rs --jq '{name,topics}'` 查询到主仓库 topics 为空。根据最新版迁移指南，GitHub 上发布 Recipkg 配方的仓库必须带 `recipkg` topic，并保留已有其他 topics。主仓发布前执行并回读：

```sh
gh repo edit LIghtJUNction/magpie-rs --add-topic recipkg
gh repo view LIghtJUNction/magpie-rs --json repositoryTopics
```

本轮已添加并通过 GitHub API 回读 `recipkg` topic；源码仓只承载 Rust 实现，不是 Recipkg 配方仓。添加 topic 使 Recipkg search 能发现候选，不保证配方有效、可信或兼容。

发布配方前，对准备发布的准确版本运行 `recipkg check`、临时目录 `pack`、归档 `check` 和 `build`，核对完整递归材料、LOCK、许可证、来源及未验证项。临时归档放在源码仓库之外，验证后删除。配方负载不含源码、PNG、二进制、凭据、执行钩子或文件系统链接。

本提交的配方已完成目录 check、临时 pack、归档 check 和从归档 build；归档 47 个常规文件均为 Markdown，不含文件系统链接。临时归档和提示词已删除。

本轮新增Pro UI及测量器修正源码，已推送并回读 `ffe2b6f20bfa06be9cdcd8b1f15a9d8308026725`。主仓本提交更新配方1.3.1与源码指针。桌面用户认可、真实闭环及完整产品仍在推进，AUR/Release和系统替换安装尚未完成。
