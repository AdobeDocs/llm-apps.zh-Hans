---
source-git-commit: bb3d8a02f22a91ceeeba5999453aeb4221060f80
workflow-type: tm+mt
source-wordcount: '398'
ht-degree: 0%

---
# 入门屏幕快照清单

捕获收件箱： `docs-captures/<YYYY-MM-DD>/`

输出目录： `help/assets/guide-onboarding-agent/`

仅捕获实质上帮助用户制定决策或验证状态的检查点。

Source文件名不需要与最终文件名匹配。 该技能按可见的UI状态绘制屏幕截图，保留原始文件，并使用以下名称创建经过清理的副本。

## 必需捕获

### `app-details-onboarding.png`

- 状态：已选择应用程序名称、分析区域和&#x200B;**自动构建我的应用程序**。
- 包括：应用程序详细信息、分析区域，以及“构建我的应用程序”的开头。
- 替换文本： `Create LLM App — app details and Build My App enabled`

### `install-aem-code-sync.png`

- 状态：使用AEM样板初始化的空EDS存储库；需要AEM代码同步。
- 包括： EDS存储库验证消息和安装链接。
- 替换文本： `Create LLM App — empty EDS repository initialized and AEM Code Sync required`

### `eds-admin-required.png`

- 状态：已安装AEM Code Sync，但当前用户不是EDS站点管理员。
- 包括：完整的验证消息和&#x200B;**打开AEM Live Admin**。
- 替换文本： `Create LLM App — EDS administrator access required`

### `actions-generating.png`

- 状态：载入时的操作页面。
- 包括：进度消息和生成步骤。
- 替换文本： `Actions — generating recommendations`

### `actions-ready-for-review.png`

- 状态：载入完成之后和批准之前生成的操作列表。
- 包括：操作名称、生成的/审阅状态和审阅控制。
- 仅使用夹具内容。
- 替换文本： `Actions — generated actions ready for review`

### `generated-action-review.png`

- 状态：有一名代表生成了操作。
- 包括：操作和构件元数据导航、处理程序生成结果和&#x200B;**标记为已审阅**。
- 掩码：存储库所有者（如有必要）。
- 替换文本： `Generated action — ready to mark as reviewed`

### `actions-reviewed.png`

- 状态：已审核每个生成的操作。
- 包括：**所有操作都已审核**、操作徽章和&#x200B;**转到应用程序页面**。
- 替换文本： `Actions — all generated actions reviewed`

### `deploy-stage.png`

- 状态：开始之前的部署对话框。
- 包括：暂存目标环境和&#x200B;**部署**。
- 替换文本： `Deploy — select the Stage environment`

### `deploy-running.png`

- 状态：部署管道正在运行。
- 包括：准备、开始、构建和发布步骤。
- 替换文本： `Deploy — deployment pipeline running`

### `deploy-successful.png`

- 状态：暂存部署成功。
- 包括：环境和成功状态。
- 掩码：运行时命名空间、完整MCP URL、ID、时间戳（如果识别）。
- 替换文本： `Deploy — successful staging deployment`

### `app-mcp-url.png`

- 状态：在部署后测试应用程序部分。
- 包括：暂存环境、**复制URL**&#x200B;和成功的部署历史记录。
- 掩码： MCP服务器URL。
- 替换文本： `App Detail — copy the staging MCP server URL`

### `chatgpt-plugins-page.png`

- 状态： ChatGPT插件页面。
- 包括：“插件”选项卡、“搜索”和“创建”按钮。
- 替换文本： `ChatGPT — Plugins page`

### `chatgpt-new-plugin.png`

- 状态：新建插件对话框。
- 包括：名称、说明、服务器URL、身份验证、确认和创建。
- 掩码： MCP服务器URL。
- 替换文本： `ChatGPT — create a plugin with the MCP server URL`

### `chatgpt-plugin-connect.png`

- 状态：插件创建后确认。
- 包括：**添加 <plugin> 到ChatGPT **和**&#x200B;连接&#x200B;**。
- 掩码：浏览器URL和连接器标识符。
- 替换文本： `ChatGPT — connect the new plugin`

### `chatgpt-generated-app.png`

- 状态：在ChatGPT中调用的夹具插件。
- 包括：附加的应用程序、生成的构件和文本响应。
- 排除：对话历史记录、帐户名和不相关的应用程序。
- 替换文本： `ChatGPT — generated LLM App plugin response`

## 可选捕获

仅当文章无法清楚地解释决策时，才添加捕获：

- GitHub应用程序存储库访问权限选择。
- 故障排除的载入状态失败。
- 插件图标上传。

请勿为已用散文清除的静态字段列表添加屏幕截图。
