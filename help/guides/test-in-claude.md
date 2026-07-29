---
title: 以Claude连接器测试您的LLM应用程序
description: 从您的Adobe LLM应用程序MCP服务器URL创建一个Claude连接器，并在对话中进行测试。
source-git-commit: bb3d8a02f22a91ceeeba5999453aeb4221060f80
workflow-type: tm+mt
source-wordcount: '399'
ht-degree: 1%

---


# 将您的LLM应用程序作为[!DNL Claude]连接器进行测试 {#test-in-claude}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps]当前在Beta中。
>
>此处显示的功能、工作流和UI不一定表示产品的最终状态。 要加入Beta，请发送电子邮件至llm-apps-beta@adobe.com 。

部署后，LLM应用程序会公开MCP服务器URL。 将此URL作为自定义连接器添加到[!DNL Claude]，然后测试生成的操作和小组件。

这是构建、自定义或扩展应用程序后的最后一个验证步骤。

## 计划要求

使用远程MCP的自定义连接器可在[!DNL Claude]、[!DNL Claude] Desktop和Cowork上免费使用、Pro、Max、Team和Enterprise计划。 免费计划帐户限制为一个自定义连接器。 对于团队和企业组织，“所有者”或“主要所有者”必须先启用连接器，其他成员才能使用它们。

## 复制MCP服务器URL

在[!DNL LLM Apps]内：

1. 打开“应用程序详细信息”页面。
2. 查找&#x200B;**[!UICONTROL 测试应用程序]**。
3. 在&#x200B;**[!UICONTROL 暂存环境]**&#x200B;下，选择&#x200B;**[!UICONTROL 复制URL]**。

## 添加自定义连接器

1. 打开[claude.ai/new?modal=add-custom-connector](https://claude.ai/new?modal=add-custom-connector#settings/customize-connectors)。 这将直接打开&#x200B;**[!UICONTROL 添加自定义连接器]**&#x200B;对话框。
2. 输入：
   - **[!UICONTROL 名称]** — 连接器名称。
   - **[!UICONTROL 远程MCP服务器URL]** — 您复制的MCP服务器URL。
3. 选择&#x200B;**[!UICONTROL 添加]**。

   ![Claude — 添加自定义连接器对话框](/help/assets/guide-test-claude/claude-add-custom-connector.png)

>[!NOTE]
>
>仅使用来自您信任的开发人员的连接器。 人无法控制开发人员提供哪些工具，也无法验证这些工具是否按预期工作或不会更改。

## 允许生成的工具

每个生成的操作都列在连接器页面上的&#x200B;**[!UICONTROL 工具权限]**&#x200B;下。 默认情况下，新工具设置为&#x200B;**[!UICONTROL 需要批准]**，这将提示您批准测试期间的每次调用。

将每个工具（或整个&#x200B;**[!UICONTROL 交互式工具]**&#x200B;组）设置为&#x200B;**[!UICONTROL 始终允许]**，以便测试不会因审批提示而中断。

![Claude — 将工具权限设置为“始终允许”](/help/assets/guide-test-claude/claude-tool-permissions.png)

## 测试连接器

1. 开始新聊天。
2. 在消息框中选择&#x200B;**+**（或类型`/`），将鼠标悬停在&#x200B;**[!UICONTROL 连接器]**&#x200B;上，然后打开您为此对话添加的连接器。

   ![Claude — 为对话启用连接器](/help/assets/guide-test-claude/claude-enable-connector-chat.png)

3. 提出与生成的操作之一匹配的问题。 例如：*给我看点咖啡。*

验证：

- [!DNL Claude]调用预期的操作。
- 该构件显示预期的示例数据。
- 文本响应与构件匹配。
- 构件控件按预期工作。

## 后续内容

- [自定义生成的构件](/help/guides/widgets.md)。
- [从头开始创建操作](/help/guides/create-action.md)。
