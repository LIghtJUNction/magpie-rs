# 生成实现与观察材料

配方是重建规范，同仓位置为 `recipes/magpie/` 及其递归依赖。`generated/` 是源码容器，当前只包含一个固定提交的 Git 子模块：[`generated/magpie-rs-source`](https://github.com/LIghtJUNction/magpie-rs-source)，仓库与目录同名。实现、桌面壳和基准脚本都在该子模块内；不得把普通源码文件放在 `generated/` 根目录，也不得把实现写进配方。

当前 pin 为 `2ae5b9a1cfaff04325642e9f288f5debfd8c9577`。父仓库索引应将该路径记录为 mode `160000`，`.gitmodules` 指向该独立仓库。改代码前检查子模块状态和来源，保留本地修改；源码仓库发布与父仓库 gitlink 更新分开记录。

重建与迭代在 `generated/magpie-rs-source/` 内进行。构建缓存放在 checkout 外，例如 `/tmp/magpie-rs-build`；不得把 `target/`、`build/`、`site/` 等构建/站点产物提交进源码仓库。源码仓包含 Cargo workspace、CLI/core/service crates、`apps/desktop` 中的 GPUI 桌面壳、benchmark 脚本和机器结果。`apps/desktop` 属于独立源码子模块，不是 Recipkg 根目录下的应用目录。

Recipkg 配方归档只包含 `recipes/magpie/` 及其声明的 Markdown 递归依赖，绝不包含 `generated/` 子模块或 Git 元数据。性能结果和实现状态以 `PERFORMANCE.md`、`OPTIMIZATION.md`、`EVIDENCE.md` 为准；子模块 HEAD 不能代替端到端验收。

保留产品名 Magpie 和主仓库名 `magpie-rs`，不追加 `-recipkg`。源码仓库使用小写连字符名 `magpie-rs-source`；它不是另一个 Recipkg 配方仓库。
