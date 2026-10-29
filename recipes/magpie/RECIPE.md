# magpie

```toml
format = "recipkg/1"

[package]
name = "magpie"
version = "1.3.7"
kind = "software"
description = "完整重建 Rust Magpie：GPUI、CLI、多平台与低内存约束。"
entry = "BUILD_PROMPT.md"

[dependencies.interfaces]
source = "../magpie-interfaces"

[dependencies.quality]
source = "../software-quality"

[dependencies.design]
git = "https://github.com/recipkg/design-directions.git"
rev = "6fdba350d1effa2d50dc0185095bb91ca7a699ee"
```

同仓配方；Rust 源码位于 `generated/magpie-rs-source/` 子模块中，它不是配方依赖。旧 docs 子模块已按用户要求移除，需求内容保存在本配方及递归依赖中。此版本是配方版本，不表示软件已达到 1.0。
