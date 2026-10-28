# 文字视觉参考

本次检查了原包20张静态PNG的带名称联络表。它们是历史产品说明，不是此次运行截图；以下文字作为纯Markdown包的独立视觉材料。字体家族/精确尺寸/颜色token/动画时长不可从缩略图确认，保持未知。产品以REQ-100—104的可读性、层级、状态和响应式行为验收。

- 全局：轻量应用壳，截图使用紧凑顶部文字标签与小品牌标识，页边空白适度。浅色白/灰背景、暗色炭黑面板；紫/蓝用于选中项、曲线和少数主动作。分隔线细、圆角克制、阴影主要在弹窗。图标尺寸小，状态靠文字和小标记辅助，避免大面积装饰渐变。目标宽屏侧边导航以REQ-101为准。
- Agents（light/dark）：主内容是单一可扫读行列表。每行左侧图标/名称/次级路径或连接状态，中右侧原生字段和模型picker，最右连接开关；底部Profile切换/保存。重点是模型与连接状态，不是仪表盘统计。
- Panel（light/dark）：独立窄竖窗，上部品牌和刷新/设置等小动作；Agent行占满可用宽度，右侧一至两个窄选择器；底部Profile与打开主窗/退出。360px时名称、状态、当前模型和关键动作必须仍可访问，长内容截断要可展开或读取。
- Picker（light/dark）：挂在当前模型行下的窄弹出列表，搜索位于顶部；原生默认项、当前项与按Provider分组的模型列表分层；选中项柔和紫底/勾选。键盘移动/选择/退出必须有明确焦点，搜索不丢输入。
- Providers（light/dark）：横向Provider行，左侧图标、名称、简短模型数量/连接信息，右侧账号/余额/状态与更多菜单；底部添加入口。没有账号时用空态和明确添加动作，不把历史样例余额填入真实列表。
- Add Provider：轻弹层/面板，顶部标题、搜索、Import/Close；订阅、直连vendor、relay、本地模型分组小卡片。供应商名为主要文字、说明小字；退休订阅应按迁移规则呈现插件来源，不能照旧图展示可用内置实现。
- Import：模态窗口叠加在暗化/模糊的应用后；上部来源说明和温和警示条，中部名称/遮蔽密钥/base URL/模型标签字段，底部Cancel和单个紫色Add动作。错误靠近相关字段，遮蔽与显示动作有可访问名称。
- Routing（light/dark）：路由组卡片从左侧Agent/输入流向中间Magpie/组策略，再到右侧模型成员；连接线表达关系，行内展示成员权重/策略/排除原因。下方“最新请求如何路由”用时间/步骤文字解释，底部其它组简洁列表。失败、备用和answering状态有文字。
- Nested Routing：子组可折叠展开，组与模型有不同图标/缩进，连线保持父子关系可追踪；展开子组不能覆盖下面的路由解释。
- Intent Rules：纵向规则编辑表单，名称、类型/模型、条件、effort/选择、保存/取消层级；条件按组分区，不把高级选项全部同时铺开。Intent Trace沿用路由关系图，并列出匹配原因、实际成员及失败/完成时间。
- Usage（light/dark）：顶部时间范围快捷选项和总费用，接着Provider余额/额度条、小型Tokens/Cache/Reasoning/Calls摘要，下面横向日期柱状图与详细查询入口。图表使用统一紫色系、淡网格、文本刻度；未知余额/费用必须unknown，不按截图值或零补齐。
- Masking（light/dark）：顶部被保护值总数及小趋势，旁边Agent经过本机遮蔽再到Provider的关系图；下方按request/value/rule切换、类别标签及按时间分组的条目。显示保护次数和规则类别而非原始秘密；关闭/失败有可读状态。
- Font Settings：密集但分组的设置表单，多语文本样本、字体选择、字号与权重等控件，右侧细粒度动作对齐；正文/等宽/多语示例均可读，不能用截图字体样本替代可自定义字体。

## 交互验收补充

加载、无数据、保存中、失败、已保存各态占用稳定位置；表单失败保留输入。暗浅主题、禁用/hover/focus可区分；不只靠红绿表达状态。弹出菜单键盘退出，screen reader标签、缩放和reduce motion按完整规格实测。长中文/德文标签及手机号窄屏不挡关键动作。

## 原始图片指纹（仅来源记录）

下列图片不随配方归档交付，也不要求重建者下载；来源为magpie-prompts固定提交4694b5c8fd0ca66c090a99aca9d82313978b4192的prompt/assets/ui/。不得将图片里的用户名、模型可用性、数字或登录状态当成当前事实。

| 原始文件 | SHA-256 |
| --- | --- |
| add-light.png | `fc143fa366512b7c3982b6fa8eecd681c2d9403e8be8c9c671aa81095b2f4513` |
| agents-dark.png | `bc6ebf6b8b8c3fe9340178cfc737c04ded8b92108c26882f77441b551124d25b` |
| agents-light.png | `5c725927f84244624e3e6be79d966fab401eb98baba78ec05e964c0f9c7d2fa8` |
| font-settings.png | `9abec57166de5b6a83d833c55158dc5c9fbe88c6ca68fb2c489bb3c994013d57` |
| import-light.png | `b1aec57a7a5d89459692ee08b254124ff707e00b6d610a9919939265c0d2cbcb` |
| intent-rules-light.png | `b901e0bb8f3210e35996ee83f040e3af3f4b54682f32681f9e72bbb0cfa90450` |
| intent-trace-light.png | `ce688dfb5f61f59c5520038116ffbe135e5b34a14e2408ea93615265838389b5` |
| masking-dark.png | `b167e71ccec5e6e50e4ef6c9135ffa9f1a2c0dee35d6f64e68d09482cb03fd71` |
| masking-light.png | `e6a33c54239d6fc424d4b66a3c36780345f31f07533b511f38beacba3d9209c7` |
| nested-routing-light.png | `bda2e8ecffb2474708876a9fd33f4d77f1ec87539830058271a87f5ea7a6def2` |
| panel-dark.png | `c2b590bf9c0085befc7ec0d98153abaaea7b5c065830b8813d841e22ac1ee380` |
| panel-light.png | `1cc98f14f7fd2a6b2861573c3b6f09131ad7d00018fc14308be59b0bc14dc4e0` |
| picker-dark.png | `30e467acd6ca6f67faefc54f6572d43981eecdfca41544b6c99b086d7d90365d` |
| picker-light.png | `37bb4b6bc43351bb8e1c5b42632493ea915449da4b5feb3317e428db530f0d57` |
| providers-dark.png | `e8ebf956768bd0bfe5850beda7074a759d510670ecddc5079210f7d229639dee` |
| providers-light.png | `e028740d51b9f8dfd215e131957d63877c95922b849c21c968eb98757afdfe80` |
| routing-dark.png | `c101ae629b12f3e436f42cabaedd04ceba6a4cbb5a5b22aa3c66ed58607c8f9e` |
| routing-light.png | `a78070e928bcf16b30b39fc96f0acbe670f1595e0b5399e7cd3dbc4f854a7792` |
| usage-dark.png | `91a9b236b49b88ffc8279f6ac3042a4041f1179a0f0e2f9a2c4026fade83cbda` |
| usage-light.png | `51836723d01a3637594c702be20a61da4272d7ffdd1e1b12bbf72b377a9a3d2d` |
