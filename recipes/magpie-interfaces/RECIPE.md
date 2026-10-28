# magpie-interfaces

```toml
format = "recipkg/1"

[package]
name = "magpie-interfaces"
version = "1.0.0"
kind = "software"
description = "重建 Magpie GPUI、托盘、CLI、TUI、Web 与本地化界面。"
entry = "BUILD_PROMPT.md"

[dependencies.gateway]
source = "../magpie-gateway"
```

同仓配方；源码和 docs 子模块只是迁移证据，不是配方依赖。此版本是配方版本，不表示软件已达到 1.0。
