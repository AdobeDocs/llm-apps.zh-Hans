---
title: 以Claude连接器测试您的LLM应用程序
description: 从您的Adobe LLM应用程序MCP服务器URL创建一个Claude连接器，并在对话中进行测试。
source-git-commit: 0f9dd0a5b1008e183eb8ab5a4c2eae34a78923bb
workflow-type: tm+mt
source-wordcount: '676'
ht-degree: 0%
---

# 将您的LLM应用程序作为[!DNL Claude]连接器进行测试 {#test-in-claude}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps]当前在Beta中。
>
>此处显示的功能、工作流和UI不一定表示产品的最终状态。 要加入Beta，请发送电子邮件至llm-apps-beta@adobe.com 。

部署后，LLM应用程序会公开MCP服务器URL。 将此URL作为自定义连接器添加到[!DNL Claude]，然后测试生成的操作和小组件。

**在本指南中：**&#x200B;了解如何将部署的应用程序添加为[!DNL Claude]连接器，选择其登录行为，并在对话中测试其操作和小组件。

对于具有公用和受保护操作的应用程序，[!DNL Claude]支持延迟登录，直到调用受保护操作为止。 其操作都需要进行身份验证的应用程序需要先登录，然后才能使用。 查看[使用您自己的身份提供程序对最终用户进行身份验证](/help/guides/authentication.md)。

## 计划要求

使用远程MCP的自定义连接器可用于免费、Pro、Max、团队和企业计划。 免费帐户支持一个自定义连接器。 在“团队”和“企业”中，授权的所有者在成员连接到组织之前为其添加连接器。 企业自定义角色也可以授予该权限。

有关计划和组织控制，请参阅[Claude当前的自定义连接器说明](https://claude.com/docs/connectors/custom/add-unlisted)。 远程MCP端点必须可从Claude的基础架构中访问，而不仅仅是从本地浏览器中访问。

## 复制MCP服务器URL

在[!DNL LLM Apps]内：

1. 打开“应用程序详细信息”页面。
2. 查找&#x200B;**[!UICONTROL 测试应用程序]**。
3. 在&#x200B;**[!UICONTROL 暂存环境]**&#x200B;下，选择&#x200B;**[!UICONTROL 复制URL]**。

## 添加自定义连接器

1. 打开[自定义>连接器](https://claude.ai/customize/connectors)。 对于组织范围的连接器，授权所有者将改为打开[组织设置>连接器](https://claude.ai/admin-settings/connectors)。
2. 为组织流程选择&#x200B;**[!UICONTROL 添加自定义连接器]**，或&#x200B;**[!UICONTROL 添加]** > **[!UICONTROL 自定义]** > **[!UICONTROL Web]**。
3. 如果需要，请输入连接器名称，并粘贴完整的HTTPS MCP服务器URL。
4. 如果对话框包含多个步骤，请选择&#x200B;**[!UICONTROL 继续]**&#x200B;并查看检测到的身份验证设置：
   - 已禁用身份验证的公共应用的&#x200B;**[!UICONTROL 无法登录]**。
   - **[!UICONTROL 需要时登录]**，以使用公开和受保护操作的应用程序。
   - **[!UICONTROL 立即登录]**&#x200B;以在连接器使用之前请求登录。
5. 对于OAuth，请选择您的身份提供程序支持的客户端身份方法。 如果您使用预注册的客户端，则只能在连接器的OAuth设置中输入其详细信息，而不是在URL或操作元数据中输入其详细信息。
6. 选择&#x200B;**[!UICONTROL 添加]**。 对于组织连接器，每个成员随后会从自己的连接器列表中选择&#x200B;**[!UICONTROL 连接]**。

某些帐户显示带有&#x200B;**[!UICONTROL 高级设置]**&#x200B;的单屏幕对话框，而其他帐户则显示多步流程。 如果您的布局不同，请使用[当前平台指令](https://claude.com/docs/connectors/custom/add-unlisted#choose-authentication-settings)。

>[!NOTE]
>
>仅使用来自您信任的开发人员的连接器。 人情无法控制开发人员提供哪些工具，也无法保证这些工具能够按预期工作或保持不变。

## 允许生成的工具

[!DNL Claude]在连接器页面上的&#x200B;**[!UICONTROL 工具权限]**&#x200B;下列出了每个生成的操作。 新工具默认为&#x200B;**[!UICONTROL 需要批准]**，提示您在测试期间批准每个调用。

对于更改数据或执行敏感操作的操作，保持启用审批提示。 在适合您的测试时，使用&#x200B;**[!UICONTROL 始终只允许]**&#x200B;执行特定的受信任、低风险操作。

## 测试连接器

1. 开始新聊天。
2. 在消息框中选择&#x200B;**+**（或类型`/`），将鼠标悬停在&#x200B;**[!UICONTROL 连接器]**&#x200B;上，然后打开您为此对话添加的连接器。
3. 提出与生成的操作之一匹配的问题。 例如：*给我看点咖啡。*

验证：

- [!DNL Claude]调用预期的操作。
- 该构件显示预期的示例数据。
- 文本响应与构件匹配。
- 构件控件按预期工作。

对于混合身份验证，请首先在不登录的情况下调用公共操作。 然后，调用受保护的操作，完成登录提示，并确认相同的请求随后会成功。 测试缺少权限以及无效的输入。

更改应用程序后，重新部署并重复受影响的测试。 如果需要更改连接器的已保存身份验证设置，[!DNL Claude]可能需要您移除并重新添加连接器。

## 后续内容

- [自定义生成的构件](/help/guides/widgets.md)。
- [从头开始创建操作](/help/guides/create-action.md)。
