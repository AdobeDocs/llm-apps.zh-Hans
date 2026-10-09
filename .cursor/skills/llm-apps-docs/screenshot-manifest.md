---
source-git-commit: 41bd4b6239171c7a3af7dc6349eaa3cbb880449c
workflow-type: tm+mt
source-wordcount: '1279'
ht-degree: 0%
---
# 屏幕快照清单

捕获收件箱： `docs-captures/<YYYY-MM-DD>/`

仅捕获实质上帮助用户制定决策或验证状态的检查点。

Source文件名不需要与最终文件名匹配。 该技能按可见的UI状态绘制屏幕截图，保留原始文件，并使用以下名称创建经过清理的副本。

下面的每个指南都声明其自己的输出目录。 使用捕获所属的节的。

# 入门指南

输出目录： `help/assets/guide-onboarding-agent/`

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

# 身份验证指南

输出目录： `help/assets/guide-authentication/`

由[authentication.md](../../../help/guides/authentication.md)引用。

**[!UICONTROL 复制资源标识符]**步骤重用入门指南的
`app-mcp-url.png`. 不要再捕获它。

此部分中的每次捕获都会显示安全配置。 保存前蒙版：

- **[!UICONTROL 颁发者]** URL以及标识身份提供程序或其供应商的任何主机名。
- 无论出现在何处，MCP服务器URL都完整。
- 租户、客户端和组织标识符。
- 帐户名称、头像和电子邮件。

使用字段必须保持清晰的中性占位符值 — 例如
`https://auth.example.com`. 作用域名称应作为通用示例读取，例如`orders:read`。

## 必需捕获

### `auth-core-settings.png`

- 状态： **[!UICONTROL 设置]** > **[!UICONTROL 身份验证]**，启用了&#x200B;**[!UICONTROL 启用身份验证]**，已填充&#x200B;**[!UICONTROL 核心设置]**。
- 包括： **[!UICONTROL Workspace]**&#x200B;选取器，显示&#x200B;**[!UICONTROL 阶段]**、**[!UICONTROL 在其开启状态下启用身份验证]**、**[!UICONTROL 颁发者]**&#x200B;和受支持的&#x200B;**[!UICONTROL 作用域]**，它们至少包含两个作用域。
- 包括折叠的&#x200B;**[!UICONTROL 高级设置]**&#x200B;控件，以便读者可以看到&#x200B;**[!UICONTROL JWKS URI]**&#x200B;是可选的，位于其所在位置。
- 掩码：颁发者主机名。
- 替换文本： `Authentication — enable authentication and complete the core settings`

2026年8月25日被俘。 裁剪以放置空画布；无需进行掩蔽，因为
**[!UICONTROL 颁发者]**&#x200B;在产品中设置为`https://auth.example.com`之前
捕获。 希望如此，而不是以后编辑图像。 **[!UICONTROL 支持的范围]**保留
一个范围(`read:all`)；两个范围可以更好地说明该字段，但这不值得
自己重新捕获。

### `auth-per-action.png`

- 状态：启用身份验证后&#x200B;**[!UICONTROL 每个操作的配置]**，模式刻意混合。
- 包括：至少三个操作，每个模式一个 — **[!UICONTROL 无]**、**[!UICONTROL 必需]**&#x200B;和&#x200B;**[!UICONTROL 可选]** — 以及在选中的操作上填充的&#x200B;**[!UICONTROL 作用域]**&#x200B;列。
- 包括： **[!UICONTROL 需要对所有操作进行身份验证]**，最好处于其不确定状态，这是混合配置所生成的。
- 仅使用夹具操作名称。
- 替换文本： `Authentication — set an auth mode and scopes for each action`

2026年8月25日被俘。 只裁切，没有遮盖物。 显示所有三种模式，填充模式
**[!UICONTROL 作用域]**&#x200B;单元格和&#x200B;**[!UICONTROL 需要对其中的所有操作]**进行身份验证
不确定的状态，将`Test Action 1/2/3`作为夹具名称。

裁切&#x200B;**内部**设置面板自己的容器边框 — 每个边框都有一个全高1px规则
将捕获的一侧作为一条散点线而保留在帧中时，将捕获的边缘作为一条散点线而显示
图像。

产品自身关于每个连接器应用[!DNL Claude]身份验证的警告为
在此选项卡上在两个捕获轮次**中未观察到**，因此此处不需要它。 此
指南改用散文陈述该行为。 如果后续版本中确实存在警告，
将其捕获为`auth-claude-warning.png`并添加一个条目。

### `chatgpt-authentication-mode.png`

- 状态：已打开带有&#x200B;**[!UICONTROL 身份验证]**&#x200B;下拉列表的&#x200B;**[!UICONTROL 新插件]**&#x200B;对话框。
- 包括：所有三个值 — **[!UICONTROL 无身份验证]**、**[!UICONTROL 混合]**&#x200B;和&#x200B;**[!UICONTROL OAuth]** — 因此，可以根据实际控件检查指南中的映射表。
- 掩码： MCP服务器URL和浏览器URL中的任何连接器标识符。
- 替换文本： `ChatGPT — select the authentication mode for the plugin`

采用与入门指南的`chatgpt-new-plugin.png`相同的方式进行构建：对话框卡片使用
页面周围仍会显示一条边距，左右大约40像素。 不裁剪刷新到
卡片。

捕获了2026-08-25（浅色模式），以匹配文档中的其他所有捕获。 此
下拉列表包含**[!UICONTROL 服务器URL]**字段，因此MCP URL不可读 — 但是
它半透明材料让现场内容的模糊图像流经旁边的
选项。 三个未加亮显示的行用面板填充及其标签重新绘制
重新呈现，这将删除它。 通过取样，而不是通过目测来验证：出血足够微弱，
miss，它是MCP服务器URL。

请注意，实时控件提供&#x200B;**4个**&#x200B;值 — **[!UICONTROL OAuth]**，**[!UICONTROL 访问权限
令牌/API密钥]**、**[!UICONTROL 无身份验证]**&#x200B;和&#x200B;**[!UICONTROL 混合]**。 指南的映射
该表仅涵盖应用程序的身份验证模式可以映射到的三个区域（虽然正确，但是未映射）
将下拉列表描述为有三个选项。

## 可选捕获

仅当散文证明不足时添加：

- `auth-scope-blocked.png` — **[!UICONTROL 保存]**&#x200B;被阻止，因为某个操作需要&#x200B;**[!UICONTROL 支持的作用域]**&#x200B;中缺少作用域。 对于疑难解答条目非常有用。
- 对话中间登录提示会引发&#x200B;**[!UICONTROL 可选]**&#x200B;操作。 平台拥有的UI，它经常更改，已在散文中进行描述。

不捕获身份提供方自己的登录页。 它标识了此文档未命名的供应商。

# 应用程序变量指南

输出目录： `help/assets/guide-app-variables/`

由[app-variables.md](../../../help/guides/app-variables.md)引用。

在&#x200B;**[!UICONTROL 阶段]**&#x200B;工作区中使用值为`Good day`的夹具变量`GREETING_PREFIX`。 变量值在表中可见，因此切勿捕获实际设置。

## 必需捕获

### `variables-empty.png`

- 状态： **[!UICONTROL 设置]** > **[!UICONTROL 变量和密钥]**，在&#x200B;**[!UICONTROL 阶段]**&#x200B;中没有变量。
- 包括：设置导航、**[!UICONTROL Workspace]**&#x200B;选取器和&#x200B;**[!UICONTROL 添加]**。
- 替换文本： `Variables & Secrets — empty Stage workspace with the Add button`

2026年10月5日被俘。 裁剪以放置空画布；没有要遮盖的内容。

### `add-variable-dialog.png`

- 状态： **[!UICONTROL 添加变量或密码]**&#x200B;对话框在保存之前已填充。
- 包括：尚不支持&#x200B;*密钥*&#x200B;通知、**[!UICONTROL 名称]** `GREETING_PREFIX`、**[!UICONTROL 类型]** **[!UICONTROL 变量]**&#x200B;和&#x200B;**[!UICONTROL 值]** `Good day`。
- 替换文本： `Add Variable or Secret — GREETING_PREFIX set to Good day`

2026年10月5日被俘。 已裁切到对话框下方；没有要遮盖的内容。

### `variable-added.png`

- 状态：保存后的变量表，具有一`GREETING_PREFIX`行。
- 包括： **[!UICONTROL 名称]**、**[!UICONTROL 类型]**、**[!UICONTROL 值]**、**[!UICONTROL 上次更新时间]**&#x200B;以及复制、编辑和删除控件。
- 替换文本： `Variables & Secrets — GREETING_PREFIX saved in the Stage workspace`

2026年10月5日被俘。 裁剪以放置空画布；没有要遮盖的内容。

### `update-variable-dialog.png`

- 状态： **[!UICONTROL 用**[!UICONTROL &#x200B;当前值&#x200B;]**`Good day`和**[!UICONTROL &#x200B;新值&#x200B;]**`Howdy`更新GREETING_PREFIX]**&#x200B;对话框。
- 替换文本： `Update GREETING_PREFIX — change the value from Good day to Howdy`

2026年10月5日被俘。 裁剪了对话框下方的剪切页面标题和空叠加；绘制了`Howdy`之后的文本脱字符号。 没有可遮盖的。

### `delete-variable-dialog.png`

- 状态： **[!UICONTROL 删除GREETING_PREFIX？]** 确认对话框。
- 替换文本： `Delete GREETING_PREFIX — confirm the permanent deletion`

2026年10月5日被俘。 已裁剪对话框下方的空叠加图；没有要遮盖的对象。
