# Magpie Recipkg 配方

从 [magpie/BUILD_PROMPT.md](magpie/BUILD_PROMPT.md) 开始。完整目标保留 93 条需求、Rust 主实现、GPUI、独立 CLI、低内存、多平台，以及英文/简中/繁中/日文/德文本地化。

递归关系为 `magpie → magpie-interfaces → magpie-gateway → magpie-core`，根包另依赖 `software-quality` 质量合同。每个目录是有效软件配方；全部材料为 Markdown，源代码与 docs 子模块仅作迁移时的观察来源。来源哈希和当前未完成能力见 [EVIDENCE.md](magpie/EVIDENCE.md)，需求覆盖见 [COVERAGE.md](magpie/COVERAGE.md)。

安装或选择可用的 Recipkg CLI 后，在仓库根执行：

```sh
recipkg check recipes/magpie
recipkg pack recipes/magpie -o ../magpie-1.2.1.recipe.tar.gz
recipkg build ../magpie-1.2.1.recipe.tar.gz --output ../magpie-build.md
```

输出路径应尚不存在；归档和生成实现都放到源码/配方目录之外。当前机器的CLI不在PATH。相邻Recipkg在迁移期间调整了生成源码路径；本轮最终使用交付目录内冻结的CLI快照完成校验，准确来源与哈希见MIGRATION.md。日常使用先安装/定位有效Recipkg并检查--help，不假设根目录仍有src或构建清单。

20 张历史界面图片已提炼为 [UI_REFERENCE.md](magpie-interfaces/UI_REFERENCE.md) 的文字层级、交互要求及原始哈希，图片不打包。包结构验证和提示词生成只证明配方可解析、递归材料可交付；所有软件运行验收仍须在独立重建后执行。


本轮使用最新双语迁移技能，更新项与来源见 [MIGRATION.md](magpie/MIGRATION.md)；后续源码位置见 [GENERATED_LAYOUT.md](magpie/GENERATED_LAYOUT.md)，本地/发布状态见 [PUBLISHING.md](magpie/PUBLISHING.md)。没有迁移或提交根目录现存Rust原型。
