---
title: 将LLM应用程序作为ChatGPT插件进行测试
description: 从您的Adobe LLM应用程序MCP服务器URL创建一个ChatGPT插件，并在对话中进行测试。
source-git-commit: b7199fbb387d91a5c77deac47a2bc883381931c1
workflow-type: tm+mt
source-wordcount: '335'
ht-degree: 1%

---


# 将您的LLM应用程序作为[!DNL ChatGPT]插件进行测试 {#test-in-chatgpt}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps]当前在Beta中。
>
>此处显示的功能、工作流和UI不一定表示产品的最终状态。 要加入Beta，请发送电子邮件至llm-apps-beta@adobe.com 。

部署后，LLM应用程序会公开MCP服务器URL。 将此URL作为插件添加到[!DNL ChatGPT]，然后测试生成的操作和小组件。

这是构建、自定义或扩展应用程序后的最后一个验证步骤。

## 计划要求

Pro、Plus、Business、Enterprise和Education帐户的Web上提供了开发人员模式。 Workspace管理员可以限制访问。

## 启用开发人员模式

在[!DNL ChatGPT]内：

1. 打开&#x200B;**[!UICONTROL 设置] → [!UICONTROL 安全和登录]**。
2. 打开&#x200B;**[!UICONTROL 开发人员模式]**。

仅在启用开发人员模式后，“插件”页面上的加号按钮才会创建MCP支持的插件。 查看[ChatGPT开发人员模式](https://developers.openai.com/api/docs/guides/developer-mode)。

## 复制MCP服务器URL

在[!DNL LLM Apps]内：

1. 打开“应用程序详细信息”页面。
2. 查找&#x200B;**[!UICONTROL 测试应用程序]**。
3. 在&#x200B;**[!UICONTROL 暂存环境]**&#x200B;下，选择&#x200B;**[!UICONTROL 复制URL]**。

## 创建插件

1. 打开[chatgpt.com/plugins](https://chatgpt.com/plugins)。
2. 在&#x200B;**[!UICONTROL 插件]**&#x200B;选项卡上，选择搜索字段旁边的&#x200B;**+**。

   ![ChatGPT — 插件页面](/help/assets/guide-onboarding-agent/chatgpt-plugins-page.png)

3. 在&#x200B;**[!UICONTROL 新插件]**&#x200B;中，输入：
   - **[!UICONTROL 名称]** — 插件名称。
   - **[!UICONTROL 描述]** — 可选。
   - **[!UICONTROL 连接]** — 选择&#x200B;**[!UICONTROL 服务器URL]**&#x200B;并粘贴MCP服务器URL。
   - **[!UICONTROL 身份验证]** — 选择&#x200B;**[!UICONTROL 无身份验证]**。
4. 选择&#x200B;**[!UICONTROL 我理解并希望继续]**。
5. 选择&#x200B;**[!UICONTROL 创建]**。

   ![ChatGPT — 使用MCP服务器URL](/help/assets/guide-onboarding-agent/chatgpt-new-plugin.png)创建插件

6. 在确认对话框中，选择&#x200B;**[!UICONTROL 连接]**。

   ![ChatGPT — 连接新插件](/help/assets/guide-onboarding-agent/chatgpt-plugin-connect.png)

## 测试插件

1. 开始新聊天。
2. 从“加号”菜单中，选择&#x200B;**[!UICONTROL 开发人员模式]**&#x200B;并选择插件。
3. 提出与生成的操作之一匹配的问题。 例如：*给我看点咖啡。*

![ChatGPT — 生成的LLM应用程序插件响应](/help/assets/guide-onboarding-agent/chatgpt-generated-app.png)

验证：

- [!DNL ChatGPT]调用预期的操作。
- 该构件显示预期的示例数据。
- 文本响应与构件匹配。
- 构件控件按预期工作。

## 后续内容

- [自定义生成的构件](/help/guides/widgets.md)。
- [从头开始创建操作](/help/guides/create-action.md)。
