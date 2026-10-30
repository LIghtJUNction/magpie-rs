# 本地状态与发布检查

## 2026-10-30 更新日志迭代与直接合并

用户本轮已明确授权直接合并。上一轮源码 PR #2 已合入 main，merge commit `338c9328428d95c5cc776c04b713c09c7b9c5d28`；父仓 PR #1 已合入 main，merge commit `487eda5ef4afce9b96d1305e8d9252bdd05ee828`。以下旧“审阅分支”章节是此前状态。

本轮先发布并合并源码，再将父仓 gitlink 固定为 `9ff54775bd3aa0c7b9436e06f0c281fa5a7f6166`，配方升级 1.4.0。新源码内容树与本地验证输入核对一致；源码 GitHub PR 为 `https://github.com/LIghtJUNction/magpie-rs-source/pull/3`。父仓本提交保存完整配方、审计、验证记录和 gitlink，按同一直接合并授权发布，不改写历史或无关技能 pin。

发布前重新回读主仓 topics 为 `recipkg`，保持原值。准确配方的验证记录在主仓 `verification/recipe-1.4.0.md`；完整递归归档只含 Markdown 与 LOCK，不带源码、截图、二进制或文件系统链接。归档完整性与软件完成程度分开：当前 99 条需求中仍有 50 条未实现，另 49 条仅部分或组件级。

本轮没有生成新的多平台安装包、发布 Release/AUR 或替换用户安装。桌面截图是原生 GPUI 合成数据离屏场景，实际窗口与用户视觉验收继续独立记录。后文既有渠道授权和历史证据保留，不冒充本轮已执行。


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
