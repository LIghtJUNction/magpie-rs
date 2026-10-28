# 本地状态与发布检查

独立源码仓为 <https://github.com/LIghtJUNction/magpie-rs-source>。用户已授权本轮迭代后发布源码、主仓配方、多平台 Release，以及 `magpie-rs-bin` / `magpie-rs-git` AUR 包，并安装其中一个互斥变体。独立源码仓 `main` 已推送并回读 `4a967ec2eecb165630a7b246546bd3bd6362fc05`；主仓本次提交保存配方与该源码指针。用户已否决当前 UI 并要求 Pro 重做，AUR、Release 与替换安装均尚未执行，需分别记录后续证据。

发布准备时通过 `gh api repos/LIghtJUNction/magpie-rs --jq '{name,topics}'` 查询到主仓库 topics 为空。根据最新版迁移指南，GitHub 上发布 Recipkg 配方的仓库必须带 `recipkg` topic，并保留已有其他 topics。主仓发布前执行并回读：

```sh
gh repo edit LIghtJUNction/magpie-rs --add-topic recipkg
gh repo view LIghtJUNction/magpie-rs --json repositoryTopics
```

本轮已添加并通过 GitHub API 回读 `recipkg` topic；源码仓只承载 Rust 实现，不是 Recipkg 配方仓。添加 topic 使 Recipkg search 能发现候选，不保证配方有效、可信或兼容。

发布配方前，对准备发布的准确版本运行 `recipkg check`、临时目录 `pack`、归档 `check` 和 `build`，核对完整递归材料、LOCK、许可证、来源及未验证项。临时归档放在源码仓库之外，验证后删除。配方负载不含源码、PNG、二进制、凭据、执行钩子或文件系统链接。

本提交的配方已完成目录 check、临时 pack、归档 check 和从归档 build；归档 47 个常规文件均为 Markdown，不含文件系统链接。临时归档和提示词已删除。
