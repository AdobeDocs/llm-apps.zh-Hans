---
title: 在ChatGPT中测试
description: 了解如何将已部署的Adobe LLM应用程序添加到ChatGPT并在实际对话中进行测试。
source-git-commit: 483a71f5f1de5caf1bd89b26f4d67d2d5a0aa15a
workflow-type: tm+mt
source-wordcount: '798'
ht-degree: 2%

---


# 在[!DNL ChatGPT]中测试

>[!IMPORTANT]
>
>**免责声明：**&#x200B;这是[!DNL LLM Apps]的测试版本。 此处显示的功能、工作流和UI不一定表示应用程序或产品的最终状态。

>[!NOTE]
>
>本指南以[!DNL ChatGPT]为例。 一般步骤（注册MCP服务器URL和在对话中进行测试）也适用于其他LLM平台，但设置流程和UI会有所不同。

成功部署后，您的应用程序将在[!DNL Adobe I/O Runtime]上运行，并公开MCP服务器URL。 本指南向您说明如何将其添加到[!DNL ChatGPT]并在实际对话中进行测试。

## 计划要求

将自定义开发人员应用程序添加到[!DNL ChatGPT]受OpenAI的订阅层控制 — 这不是[!DNL LLM Apps]限制，而是OpenAI当前管理对自定义MCP应用程序访问的方式。

| [!DNL ChatGPT]计划 | 自定义MCP应用程序 |
|--------------|-----------------|
| 免费 | 不可用 |
| 转到 | 不可用 |
| 加号 | 不可用 |
| Pro | 可用 |
| 商务 | 可用 |
| 企业/教育 | 可用 |

>[!NOTE]
>
>如果您使用免费、转到或加号计划，则&#x200B;**无法将您部署的应用程序**&#x200B;添加到[!DNL ChatGPT]。 升级到&#x200B;**Pro**&#x200B;或要求贵组织的管理员在&#x200B;**Business**&#x200B;或&#x200B;**Enterprise**&#x200B;工作区中启用它。

## 启用开发人员模式

要添加自定义MCP应用，必须在[!DNL ChatGPT]帐户中启用&#x200B;**开发人员模式**。 关注
执行以下步骤来验证和启用它。

### 打开设置

单击左下角的配置文件头像，然后单击“**[!UICONTROL 设置]**”。

![ChatGPT — “设置”菜单](/help/assets/guide-test-chatgpt/chatgpt-settings-menu.png)

### 导航到应用程序

在“设置”对话框中，选择左侧边栏中的&#x200B;**[!UICONTROL 应用程序]**。 单击底部的&#x200B;**[!UICONTROL 高级设置]**。

![ChatGPT — 应用程序设置](/help/assets/guide-test-chatgpt/chatgpt-apps-settings.png)

### 打开开发人员模式

确保&#x200B;**[!UICONTROL 开发人员模式]**&#x200B;切换打开（蓝色）。 这允许您注册自定义的未验证MCP服务器URL。

>[!NOTE]
>
>开发人员模式被标记为&#x200B;*提升的风险*，因为它允许尚未由OpenAI审阅的应用程序。 [!DNL ChatGPT]自动禁用使用开发人员模式应用的对话的内存。

![ChatGPT — 已启用开发人员模式](/help/assets/guide-test-chatgpt/chatgpt-developer-mode.png)

## 将你的应用添加到[!DNL ChatGPT]

### 复制MCP服务器URL

转到[!DNL LLM Apps]中的&#x200B;**应用程序详细信息**&#x200B;页面，并找到&#x200B;**[!UICONTROL 测试应用程序]**&#x200B;部分。 复制&#x200B;**暂存**&#x200B;或&#x200B;**生产** URL — 它看起来像：

```
https://<namespace>.adobeioruntime.net/api/v1/web/llm-apps/mcp
```

### 打开“应用程序”页面

在[!DNL ChatGPT]中，转到[!UICONTROL 应用程序&#x200B;]**→的**[!UICONTROL &#x200B;设置]。

![ChatGPT — 应用程序页面](/help/assets/guide-test-chatgpt/chatgpt-apps-page.png)

### 创建新应用程序

在“高级设置”行中单击&#x200B;**[!UICONTROL 创建应用程序]**。

![ChatGPT — 创建应用程序对话框](/help/assets/guide-test-chatgpt/chatgpt-create-app.png)

填写以下内容：

| 字段 | 值 |
|-------|-------|
| **图标** | 可选 — 上传128x128 PNG（最大10 KB） |
| **名称** | 您应用程序的显示名称（例如，*我的品牌应用程序*） |
| **描述** | 有关应用程序功能的简短描述 |
| **MCP服务器URL** | 粘贴[!DNL LLM Apps]中的URL |
| **[!UICONTROL 身份验证]** | 选择&#x200B;*无身份验证* |

选中&#x200B;**我了解并想要继续**复选框 — 确认MCP服务器
尚未由OpenAI审阅 — 然后单击**创建**。

### 验证是否已启用应用程序

创建后，您的应用程序将显示在具有&#x200B;**[!UICONTROL DEV]**&#x200B;徽章的&#x200B;**[!UICONTROL 启用的应用程序]**&#x200B;下，确认其处于活动状态。

>[!NOTE]
>
>您的应用程序也出现在&#x200B;**草稿**&#x200B;下 — 这些是您在开发人员模式下创建的专用应用程序，仅对您的帐户可见。

您的应用现在可以在[!DNL ChatGPT]对话中使用。

![ChatGPT — 已启用应用程序](/help/assets/guide-test-chatgpt/chatgpt-app-enabled.png)

## 在对话中测试

应用启用后，在[!DNL ChatGPT]中开始新对话。 在提出问题之前，请使用以下两种方法之一附加您的应用程序。

### 选项1 — 从菜单中选择

单击聊天输入中的&#x200B;**+**&#x200B;按钮，然后单击&#x200B;**更多**&#x200B;以展开可用工具的完整列表。 从列表中选择应用程序以将其附加到当前对话。

![ChatGPT — 从菜单中选择应用程序](/help/assets/guide-test-chatgpt/chatgpt-select-app.png)

### 选项2 — 使用@mention

在聊天输入中键入&#x200B;**@**，然后从下拉菜单中选择您的应用程序。 这会附加内联应用程序，您可以在同一条消息中继续键入您的问题。

>[!NOTE]
>
>在同一应用上再次使用&#x200B;**@mention**&#x200B;将取消选择该应用并将其从对话中删除。

![ChatGPT —@mention用应用程序](/help/assets/guide-test-chatgpt/chatgpt-mention-app.png)

选择后，该应用程序将内联附加，您可以在同一消息中键入您的问题：

![ChatGPT — 通过@mention](/help/assets/guide-test-chatgpt/chatgpt-mention.png)附加的应用程序

### 查看结果

附加应用程序后，键入与配置的操作之一一致的问题 — 例如，*“显示您的产品”。* [!DNL ChatGPT]将其与相关操作匹配，提取输入参数，在[!DNL Adobe I/O Runtime]上调用您的处理程序，并呈现结果：

![ChatGPT — 操作结果](/help/assets/guide-test-chatgpt/chatgpt-response.png)

响应包括：

- **EDS小组件** — 具有图像、评级和操作按钮的富UI组件。
- **文本响应** — 在小组件下，[!DNL ChatGPT]使用您的处理程序返回的`content`
制定自然语言的结果摘要。
- **状态指示器** — 您在“创建操作”对话框中配置的&#x200B;*调用状态文本*。

## 后续内容

- **添加更多操作** — 在UI中定义其他操作、编写其处理程序并重新部署。
- **部署到生产环境** — 如果您在暂存环境中进行了测试，请部署到生产环境，以获得实时体验。
- **与您的团队共享** — 使用“应用程序详细信息”页面上的&#x200B;**复制URL**&#x200B;与团队成员共享MCP服务器URL。

