---
title: 将LLM应用程序作为ChatGPT插件进行测试
description: 从您的Adobe LLM应用程序MCP服务器URL创建一个ChatGPT插件，并在对话中进行测试。
source-git-commit: 0f9dd0a5b1008e183eb8ab5a4c2eae34a78923bb
workflow-type: tm+mt
source-wordcount: '546'
ht-degree: 0%
---

# 将您的LLM应用程序作为[!DNL ChatGPT]插件进行测试 {#test-in-chatgpt}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps]当前在Beta中。
>
>此处显示的功能、工作流和UI不一定表示产品的最终状态。 要加入Beta，请发送电子邮件至llm-apps-beta@adobe.com 。

部署后，LLM应用程序会公开MCP服务器URL。 将此URL作为插件添加到[!DNL ChatGPT]，然后测试生成的操作和小组件。

**在本指南中：**&#x200B;了解如何将部署的应用程序连接到[!DNL ChatGPT]并在对话中测试其操作和小组件。 更改应用程序后刷新连接。

## 计划要求

使用允许添加自定义MCP服务器的帐户和工作区。 可用性和管理员控制因帐户和工作区而异。 请参阅OpenAI当前的[连接并测试您的插件](https://developers.openai.com/plugins/deploy/connect-chatgpt)文档。

## 检查对自定义MCP服务器的访问权限

打开[ChatGPT插件](https://chatgpt.com/plugins)并检查您的帐户是否允许添加自定义MCP服务器。 如果您的帐户需要开发人员模式，请使用帐户的控件启用该模式。 如果自定义MCP服务器选项缺失或受阻，请咨询您的工作区管理员。

## 复制MCP服务器URL

在[!DNL LLM Apps]内：

1. 打开“应用程序详细信息”页面。
2. 查找&#x200B;**[!UICONTROL 测试应用程序]**。
3. 在&#x200B;**[!UICONTROL 暂存环境]**&#x200B;下，选择&#x200B;**[!UICONTROL 复制URL]**。

## 创建插件

1. 打开[chatgpt.com/plugins](https://chatgpt.com/plugins)。
2. 选择&#x200B;**+**，然后选择&#x200B;**[!UICONTROL 添加自定义MCP服务器]**。
3. 配置连接：
   - **[!UICONTROL 名称]** — 插件名称。
   - **[!UICONTROL 描述]** — 可选。
   - 选择公共服务器连接方法，并输入应用程序的完整HTTPS MCP服务器URL。
   - 选择与您的应用程序匹配的身份验证方法。 对于已禁用身份验证的公共应用，在提供的&#x200B;**[!UICONTROL 无身份验证]**&#x200B;处使用。

   >[!NOTE]
   >
   >如果已启用最终用户身份验证，请在每个操作均设置为&#x200B;**[!UICONTROL 必需]**&#x200B;时，选择&#x200B;**[!UICONTROL OAuth]**，或为公共操作和受保护操作的组合选择&#x200B;**[!UICONTROL 混合]**，其中提供了这些选项。 查看[使用您自己的身份提供程序对最终用户进行身份验证](/help/guides/authentication.md)。

4. 选择&#x200B;**[!UICONTROL 我理解并希望继续]**。
5. 选择&#x200B;**[!UICONTROL 创建作为插件]**。
6. 查看发现的工具和元数据，然后安装或连接生成的插件。 如果您的应用程序需要完成登录。

OpenAI拥有设置UI，其布局可能因帐户而异。 如果显示的标签不同，请按照当前[连接说明](https://developers.openai.com/plugins/deploy/connect-chatgpt)操作。

## 测试插件

1. 开始新聊天。
2. 在提示框中键入`@`并选择已安装的插件。
3. 提出与生成的操作之一匹配的问题。 例如：*给我看点咖啡。*

![说明性ChatGPT响应 — Frescopa操作返回对话中的产品卡](/help/assets/guide-onboarding-agent/chatgpt-generated-app.png)

验证：

- [!DNL ChatGPT]调用预期的操作。
- 该构件显示预期的示例数据。
- 文本响应与构件匹配。
- 构件控件按预期工作。

此外，还要测试无效的输入、空结果、不相关的不调用应用程序的提示以及更改数据的操作。 审查每个确认和身份验证提示，而不是提前批准所有请求。

## 应用程序更改后刷新

更改操作描述、输入、身份验证或构件配置后：

1. 将更新的应用程序部署到同一环境。
2. 在[ChatGPT插件](https://chatgpt.com/plugins)上打开其连接，然后选择&#x200B;**[!UICONTROL 刷新]**。
3. 验证播发操作是否已更改。
4. 开始新对话并重复受影响的测试。

## 后续内容

- [自定义生成的构件](/help/guides/widgets.md)。
- [从头开始创建操作](/help/guides/create-action.md)。
