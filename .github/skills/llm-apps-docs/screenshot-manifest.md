---
source-git-commit: a48b4022979d9a13b4fc3aa870c4ee6375e8bc13
workflow-type: tm+mt
source-wordcount: '1052'
ht-degree: 1%
---
# 屏幕快照清单

使用浏览器自动化捕获这些状态
[屏幕截图.md](screenshots.md)。 选择与文章相关的检查点；
此清单未授权资源创建、生成、审批或
部署。

原始捕获停留在`docs-captures/<run-id>/`中。 仅发布以视觉方式审核的内容，
在`help/assets/`下清理的PNG。 在以下情况下，保持文件名处于稳定状态：
正在刷新图像。

## 基础架构设置

文章： `/help/guides/create-app.md`

输出目录： `help/assets/guide-infrastructure/`

| 文件名 | 状态和有用上下文 | 建议的替换文本 |
|---|---|---|
| `setup-choice.png` | 开始使用两个可见的基础架构选项 | 创建您的LLM应用程序 — 选择Adobe托管的基础架构或您自己的存储库 |
| `app-details.png` | 应用程序名称、AEM程序、Analytics区域和不可变设置警告 | 应用程序详细信息 — 输入名称，选择AEM程序，然后选择Analytics区域 |
| `action-repository.png` | 已选择GitHub且拥有存储库的路径以及存储库连接说明 | 操作基础结构设置 — 连接空的或兼容的操作存储库 |
| `provisioning.png` | 正在执行已批准的Adobe托管设置 | 应用程序设置 — Adobe正在准备基础架构 |

排除帐户详细信息、组织ID、实际项目名称和存储库
标识符和应用程序ID。 使用空字段或中性夹具名称，其中
有可能。 存储库验证可以初始化代码，因此请先获得批准
捕获验证结果。

## 创建操作并审查生成的工作

文章： `/help/guides/creating-actions.md`

输出目录： `help/assets/guide-creating-actions/`

| 文件名 | 状态和有用上下文 | 建议的替换文本 |
|---|---|---|
| `build-choice.png` | 自动选择生成操作，输入公共网站，并稳定启动控制（如果包含） | 操作 — 选择自动生成并输入公共网站 |
| `generating.png` | 正在运行生成、时间估计和转至操作 | 操作 — 生成正在运行 |
| `actions-ready-for-review.png` | 夹具操作名称、审核徽章和审核控件 | 操作 — 生成的操作可供审查，并且未部署 |
| `generated-action-review.png` | LLM构件详细信息、操作详细信息、Analytics、生成的拉取请求通知和标记为已审核 | 生成的操作 — 在批准之前检查小部件和处理程序 |

屏蔽私有存储库和拉取请求链接。 裁切生成的个人或
当文章仅需要审核状态时联系人详细信息。 不选择
**标记为已审核**，未经批准。

仅当批准在范围内并且
州确实存在。 不批准已生成的工作以满足屏幕快照要求
检查清单。

## 从头开始创建操作

文章： `/help/guides/create-action.md`

输出目录： `help/assets/guide-create-action/`

| 文件名 | 状态和有用上下文 | 建议的替换文本 |
|---|---|---|
| `action-basic-info.png` | 搜索产品、其派生标识符、有用的描述和只读注释 | 创建新操作 — 基本信息和注释 |
| `action-analytics-user-intent.png` | 收集用户意图和默认的匿名摘要说明 | 创建新操作 — 审查用户意图收集 |
| `widget-metadata.png` | Edge Delivery Services块类型和安全的示例沙盒源 | 创建新操作 — 构件类型和沙盒域 |
| `widget-template-urls.png` | 示例脚本和小部件URL | 创建新操作 — 脚本和小部件URL |
| `widget-permissions.png` | 可选的浏览器权限，未使用的权限关闭 | 创建新操作 — 浏览器权限 |
| `widget-csp.png` | 资源、连接、帧、重定向和基本URI域控制 | 创建新操作 — 内容安全策略域 |

创建对话框具有&#x200B;**操作**&#x200B;和&#x200B;**小组件元数据**选项卡。 保存的编辑器
具有**LLM构件详细信息**、**操作详细信息**&#x200B;和&#x200B;**Analytics**选项卡。 捕获
程序实际使用的布局。 填写示例字段而不保存，然后
放弃编辑。

## 身份验证

文章： `/help/guides/authentication.md`

输出目录： `help/assets/guide-authentication/`

| 文件名 | 状态和有用上下文 | 建议的替换文本 |
|---|---|---|
| `auth-core-settings.png` | 暂存、启用身份验证、`https://auth.example.com`、`catalog:read`、`profile:read`和高级设置 | 身份验证 — 启用身份验证并完成核心设置 |
| `auth-per-action.png` | 显示“无”、“必需”、“可选”和指定范围的夹具操作 | 身份验证 — 为每个操作选择身份验证模式和范围 |

使用未保存的设置并在以后将其丢弃。 验证保存的身份验证
保持不变。 resource-identifier步骤重用
`help/assets/guide-onboarding-agent/app-mcp-url.png`.

如果UI显示的警告与已部署的运行时或当前运行时冲突
平台行为，记录差异。 仅表格裁剪是合适的
在教学模式时，不要在未说明的情况下隐藏相关警告。

## 应用程序变量

文章： `/help/guides/app-variables.md`

输出目录： `help/assets/guide-app-variables/`

使用`GREETING_PREFIX`和`Good day`作为示例值，`Howdy`作为
替换值。 从不捕获实际配置值。

| 文件名 | 状态和有用上下文 | 建议的替换文本 |
|---|---|---|
| `variables-empty.png` | 清空暂存工作区、Workspace选取器和添加 | 变量和密钥 — 空的阶段工作区 |
| `add-variable-dialog.png` | 未保存的GREETING_PREFIX变量和当前支持密码的通知 | 添加变量或密码 — 输入GREETING_PREFIX和“Good day” |
| `variable-added.png` | 具有名称、类型、值、时间戳和行控件的已批准夹具变量 | 变量和密码 — 舞台工作区中的GREETING_PREFIX |
| `update-variable-dialog.png` | 当前值好日子和未保存的新值好日子 | 更新GREETING_PREFIX — 将“Good day”（好日）更改为“Howdy”（好时） |
| `delete-variable-dialog.png` | 确认对话框但不确认删除 | 删除GREETING_PREFIX — 检查删除确认 |

打开对话框不会授权保存或删除。 重复使用安全的现有
夹具变量或创建夹具变量前询问。 仅保留较旧的映像之后
验证其字段和控件是否仍与当前UI匹配。

## 部署和测试

文章： `/help/guides/deploy-your-app.md`

| 输出目录 | 文件名 | 状态和有用上下文 |
|---|---|---|
| `help/assets/guide-onboarding-agent/` | `deploy-stage.png` | 目标环境暂存和部署控制，在开始之前 |
| `help/assets/guide-onboarding-agent/` | `app-mcp-url.png` | 当前测试应用程序信息卡、环境标签和复制URL；屏蔽每个运行时URL |
| `help/assets/guide-deploy/` | `deployment-history.png` | 不含应用程序标识符的环境、状态和部署日期列 |

在允许时，对测试卡和历史记录使用只读现有部署。
除非用户批准，否则不要启动部署以获取这些状态。
仅当状态同时为运行中或成功的部署对话框时才捕获
相关和授权。

对于`/help/guides/test-in-chatgpt.md`，保留的
`help/assets/guide-onboarding-agent/chatgpt-generated-app.png`演示了一个产品
响应。 它不会验证当前的设置UI。 新的ChatGPT或Claude设置
屏幕截图需要当前经过身份验证的访问；否则，请使用正式供应商
说明并忽略未验证的图像。

## 现有捕获来源

2026-10-19捕获运行使用了用户请求的本地UI，并具有匹配的UI/API
源检查。 它不建立生产奇偶校验。

- 原始捕获： `docs-captures/2026-10-19/`及其`full-review/`子目录。
- 较早的或替换的公共图像仍保留在本地`retired/`子目录中。
- 文档固定到达了生成的操作审阅阶段。 无操作
批准、拉取请求合并、部署或保存的身份验证/变量
更改是该捕获运行的一部分。
- 在以下时间之后，将保留以前运行中的变量行、更新和删除图像
根据当前实施检查其控件。
- 未重新捕获第三方设置屏幕。 过时的设置屏幕截图为
已删除，而不是显示为当前状态。

对于每次新刷新，请更新日期、源环境、验证状态，
和任何剩余的验证限制。 不要复制旧的来源
作为新抓获的证据。

### 操作编辑器开关移除，2026-10-19

已从运行中刷新`guide-creating-actions/generated-action-review.png`
删除编辑器的Enabled开关后的本地UI。 应用程序页面的可见性
开关保持操作开/关控制；型号和小组件曝光设置
保留在编辑器中。 存储库链接被屏蔽，帐户导航被屏蔽
已排除。 在此刷新期间未保存、批准、切换或部署任何操作。
原始捕获： `docs-captures/2026-10-19/remove-enabled/`。
