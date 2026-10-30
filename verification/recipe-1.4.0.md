# 配方 1.4.0 发布验证

实际执行时间：2026-10-30T10:19:13.293900+00:00。对应源码提交 `9ff54775bd3aa0c7b9436e06f0c281fa5a7f6166`。本记录位于配方负载之外，避免归档散列自引用。

## 结果

| 检查 | 实际结果 |
| --- | --- |
| 源目录离线 check | 预期退出 2：Git 依赖需显式 pack/install；没有隐式网络解析。 |
| pack → 归档 check → unpack → 解包目录 check → build | 五步均退出 0；构建中文完整提示词。 |
| 递归归档 | 73 个常规 UTF-8 Markdown 文件、7 个配方包、0 个链接；72 个 LOCK 文件散列逐一吻合。 |
| 需求完整性 | 99 个唯一定义与 99 行覆盖表；旧 93 个编号全部保留，新增 105–110 全部明确未实现。 |
| 覆盖分类 | 39 部分、10 仅组件、50 未实现；不计算软件完成百分比。 |
| 原版材料 | 237 个唯一发布，官方缺失的 2070 未补造；17 路由族、69 注册模式（27 GET、32 POST、10 mux 未限定方法）。 |
| 设计依赖 | 原有 design-directions / design-foundations 固定提交不变。 |
| 清理 | 临时归档、解包副本和生成提示词已删除；保留验证记录。 |

## 版本与散列

- Recipkg CLI：0.1.1，源码 `f6f5983d558937677c712fea291c65b376487568`。
- 本地配方输入：55 个 Markdown，按排序的 `sha256  path\n` 清单计算聚合 SHA-256 `0818672dafadb41fcd4be72e246c685acb806bd0d7ecd0e78b264461261ab78a`。
- 归档：256548 bytes，SHA-256 `c591fe8fa765885294ee09818600f1defbed3b2d8d2c77888b47eca8a13438e3`。
- 完整中文提示词：667664 bytes，SHA-256 `437c0716f83117b19d3f08193be4b453eeea53103ce3c30e8b30f498b437d8f0`。

| 包 | 版本 | 来源/固定提交 |
| --- | --- | --- |
| magpie | 1.4.0 | `.` |
| design-directions | 0.1.2 | `6fdba350d1effa2d50dc0185095bb91ca7a699ee` |
| design-foundations | 0.1.1 | `8c5bf1f1e40719789cf51bc0dfd8f5aeecae0538` |
| magpie-interfaces | 1.0.0 | `../magpie-interfaces` |
| magpie-gateway | 1.1.0 | `../magpie-gateway` |
| magpie-core | 1.1.0 | `../magpie-core` |
| software-quality | 0.1.0 | `../software-quality` |

## 本地配方输入清单

清单只标识本仓库输入；递归 Git 依赖由上表完整提交及归档 LOCK 标识。源码子模块、图片和本验证文件均不进入配方。

| 路径 | SHA-256 |
| --- | --- |
| `recipes/README.md` | `defe790aa780e34c50c610f28e2f8b46f50defcd498e10cd6c2d86511063e042` |
| `recipes/magpie/ACCEPTANCE.md` | `26c22a0a0dd31fec294e6598b4492c32fc483dcab7d7b678ecd92fe98266d87f` |
| `recipes/magpie/AGENTS.md` | `d4d3a909df11aa6503e1cfcdeb04bbb8ef4eed168727ec31a0e3c0a1a348e162` |
| `recipes/magpie/BUILD_PROMPT.md` | `2938da16f77af97c3c3b8615360b1b12c3388db6f6294b8db95744513a513100` |
| `recipes/magpie/COVERAGE.md` | `beef2d04a4bdd8c5c583596ea9cb2e9aeb9a9d8cab78faf6029f02cd7d3ef091` |
| `recipes/magpie/DEPENDENCIES.md` | `0d490d6220c518b608965728400ad30d699e0f045a7bae42b0a7a8aa690bfd15` |
| `recipes/magpie/EVIDENCE.md` | `e926d09af877ecdae270040f49f549ada855965331eb33cedcee103af1b3255f` |
| `recipes/magpie/GENERATED_LAYOUT.md` | `e8a309a81944902eb548bb7c2576699654dc77bacd0d05047e73c470b1ec1097` |
| `recipes/magpie/GLOBAL.md` | `c40f6fb1d0cd25c389e697493d3a0c18f1f0b057f83527403811ded19cac431a` |
| `recipes/magpie/LICENSE.md` | `79d2c8444715d4bc453ec4f8a0aaf2051a4c1ee5ac08f5bd2e5848aef87c7572` |
| `recipes/magpie/MIGRATION.md` | `faff3f7e1a389c4e334a8a6a623b081b9e17cace7ceeb9184f8f0f1b0f025d29` |
| `recipes/magpie/OPEN_QUESTIONS.md` | `4bd88ffb0f91b0d1938530d3678a0dd3f42c8d3e82a785106fbaedbe8dc06731` |
| `recipes/magpie/OPTIMIZATION.md` | `24be3e307d0c27a1304ae55ee44688e1a8e4ecc388ef40a400bb6aedc4c92e41` |
| `recipes/magpie/ORIGINAL_CLI_BEHAVIOR.md` | `15abc02c7c7c1d828b6383232e5a8a5128339524af8db26cbc669fa21f0825dc` |
| `recipes/magpie/ORIGINAL_PARITY.md` | `95b959c5ae323ca2c5f7f55290701395ed8d4cb5ad2c000facbfe5bf5acf33d5` |
| `recipes/magpie/ORIGINAL_WEB_BEHAVIOR.md` | `6ac15312a639cca4565c16a0a46f986bcc973cdb91efeb30a17f8e6c974a9412` |
| `recipes/magpie/PARITY_VERIFICATION.md` | `c4cf0d9b0e6c7004bf91f1456edd7f65cc5586641d067acdd05cd99058c83f29` |
| `recipes/magpie/PERFORMANCE.md` | `17b31d8a3a9da1425747cd3c571b306e711e6b56dbd679c829a81cf9092ae293` |
| `recipes/magpie/PUBLISHING.md` | `4cb65b55f6fa1255357ed6ab55944ca4b35895b9db5316a231814cb07b3753bc` |
| `recipes/magpie/RECIPE.md` | `765bc671fc01bad3ae454621715ac193bd39a73d044d1a47b054a77262a2db60` |
| `recipes/magpie/UI_DIRECTION.md` | `95a41b01d1722815b049aa7a75435640d7f4bd80150d9586a093f27a26e0f406` |
| `recipes/magpie/UPSTREAM_CHANGELOG.md` | `74ab565ccc676d2a3eaa71bc6e79d1e29f14e51f2d15dd5a6e4f6b3b029039e4` |
| `recipes/magpie-core/ACCEPTANCE.md` | `2a507399b68999878de060f0ea89d1e99fca896f5c4ba26a1ca3dbd62eb5010a` |
| `recipes/magpie-core/AGENTS.md` | `30c35871cdb4743aecd547032af3c8546770c535d84fe16352986658373e108b` |
| `recipes/magpie-core/BUILD_PROMPT.md` | `25703e12c8410f958600d832d3957f026b59271467784074d399014107d1368e` |
| `recipes/magpie-core/LICENSE.md` | `79d2c8444715d4bc453ec4f8a0aaf2051a4c1ee5ac08f5bd2e5848aef87c7572` |
| `recipes/magpie-core/RECIPE.md` | `9c33174837f8472a736fa1a52410587947f25fd9eaf1400108b46a32e8978cf4` |
| `recipes/magpie-core/specs/01-product-and-agents.md` | `5afa6209e571feb183c7315481ca4d2510a227497635383ae580b8f4ed2a1fe1` |
| `recipes/magpie-core/specs/02-providers-and-accounts.md` | `2b49e12dc13fc068ad9ee3d356a4ffd271075cfbbeea2b441794400291c8f5c3` |
| `recipes/magpie-core/specs/04-routing-and-failover.md` | `7fd54eba4a40c1eefacc19506aaacf8f41c3357acf3f53defb3406ab0390127b` |
| `recipes/magpie-core/specs/07-library-sync-and-backup.md` | `8c1b3c0c26e7a15878a03854a15f5584feee0e723c9525612d429f382eb2059f` |
| `recipes/magpie-core/specs/09-privacy-security-and-platforms.md` | `87f076d09e2454274a340ac96b5b34fa07715d33a55118c06424bcfd41cc69ee` |
| `recipes/magpie-gateway/ACCEPTANCE.md` | `a020e9db51e04e4f3577fd18c569acf6ace58e5e0699785f958f2a8d728fa8a4` |
| `recipes/magpie-gateway/AGENTS.md` | `30c35871cdb4743aecd547032af3c8546770c535d84fe16352986658373e108b` |
| `recipes/magpie-gateway/BUILD_PROMPT.md` | `e558896480c62d0aca9de84faa8aef0ca29b97685190a6956fcdadbe46c74133` |
| `recipes/magpie-gateway/LICENSE.md` | `79d2c8444715d4bc453ec4f8a0aaf2051a4c1ee5ac08f5bd2e5848aef87c7572` |
| `recipes/magpie-gateway/RECIPE.md` | `d645382539234cd2c48198f37a4b01d13216dcd317c4daa497f8f27273a1bc7d` |
| `recipes/magpie-gateway/UPSTREAM_ROUTES.md` | `517e7fff33c696744554c42f66334627828a65e1543198bfd461cfc95e885782` |
| `recipes/magpie-gateway/specs/03-gateway-and-protocols.md` | `ccb05785263a3ae4c5e14ed3dfa71972c59697bd9ef2edf40ed4f4b4f2a2e27e` |
| `recipes/magpie-gateway/specs/05-usage-and-sessions.md` | `99b87b4076d92cafa929ef6ea9b54d1d79a7e2fa6a4dc5799cf8aa4fc1b3573e` |
| `recipes/magpie-gateway/specs/06-plugins-and-tools.md` | `a011e730dec484d101d7de2a549aeb6b5b9fcfd1f3e17c984ff28fc259d581d4` |
| `recipes/magpie-gateway/specs/11-upstream-additions.md` | `3f030282c3b9ef932cf62c8df7feca0c4e309e01781f4556f62943acfa4ea175` |
| `recipes/magpie-interfaces/ACCEPTANCE.md` | `1cafb79593fd64d2ee8ba0bd1dbaf332ca5c91bcee772e4482a8709524cbc43d` |
| `recipes/magpie-interfaces/AGENTS.md` | `30c35871cdb4743aecd547032af3c8546770c535d84fe16352986658373e108b` |
| `recipes/magpie-interfaces/BUILD_PROMPT.md` | `ff601e1ed55faafe4495e439c64660cace1159e67877a159c1745252e8ef3e46` |
| `recipes/magpie-interfaces/LICENSE.md` | `79d2c8444715d4bc453ec4f8a0aaf2051a4c1ee5ac08f5bd2e5848aef87c7572` |
| `recipes/magpie-interfaces/RECIPE.md` | `0cd57745f70aa715ba799408b8ff778464e51ff0595046c91a4ddbb3a02af334` |
| `recipes/magpie-interfaces/UI_REFERENCE.md` | `034c51390a06b3d2ac5757acaa00ddd618f2d681f3c509fc7576bb7f59899fef` |
| `recipes/magpie-interfaces/specs/08-cli-tui-and-web.md` | `c738936571aae7229e3300df9c2a36d7c6406283914cb71c0b1c80408e478f05` |
| `recipes/magpie-interfaces/specs/10-visual-and-localization.md` | `c4358192bd42c40eaea5d10e9746d7101e67a87e733026400658915302b9b47f` |
| `recipes/software-quality/ACCEPTANCE.md` | `556b007e62be3bd0681ab0e3904502e8134ef072ba0d2995771edb8731cd7587` |
| `recipes/software-quality/BUILD_PROMPT.md` | `342501e49cedbed36af247eec925c35a3c52cd9831d0da9169c999082d18cb47` |
| `recipes/software-quality/LICENSE.md` | `97016fcf641f0ebb5c3fb6a0eda2e2bec82aecd443f3acd24a536499d5fa28a4` |
| `recipes/software-quality/RECIPE.md` | `ff02ece72586830b0db2c30ab9f0c6ee332d11632d1f8bd28ac76906499f3d89` |
| `recipes/software-quality/SOURCE.md` | `e8e39b90e1321436ceb0054e20d911b1f64fd5cc254fc53e1017efac2fdb8048` |

## 验证边界

此结果只证明准确版本的配方结构、递归材料、完整提示词与完整性校验。没有按归档重新独立生成全部软件，也不替代真实账户、平台安装、原生窗口交互、用户视觉或性能验收。实现与测试状态见源码本轮记录及主配方 COVERAGE/EVIDENCE。

