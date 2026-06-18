---
title: Adobe LLM应用程序先决条件
description: 在Adobe LLM应用程序Beta入门培训课程之前需要设置的内容。
source-git-commit: 1a99e2e80e50a3bcf9ce6fb910365202bf06e113
workflow-type: tm+mt
source-wordcount: '571'
ht-degree: 1%

---


# Adobe LLM应用程序先决条件 {#prerequisites-for-adobe-llm-apps}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps]当前在Beta中。
>
>此处显示的功能、工作流和UI不一定表示产品的最终状态。 要加入Beta，请发送电子邮件至llm-apps-beta@adobe.com 。

在Adobe的[!DNL Adobe LLM Apps]入门培训课程之前，确认您已具备以下条件。 如果可能，请运行下面的验证步骤 — 结果会告诉您需要加入聊天室的人员，而不是您是否可以继续。

## Adobe Developer Console

您需要使用Adobe IMS组织中的&#x200B;**开发人员**&#x200B;角色（或&#x200B;**系统管理员**&#x200B;角色）访问[Adobe Developer Console](https://developer.adobe.com/console)。 确保您的组织有权访问[[!DNL App Builder]](https://developer.adobe.com/app-builder/docs/intro_and_overview/)。

要进行验证，请转到[developer.adobe.com/console](https://developer.adobe.com/console)。 如果您看到“快速入门”屏幕，则表明您的权限设置正确。

![Adobe Developer Console — 确认开发人员访问权限的快速入门屏幕](/help/assets/overview/dev-console-access-granted.png)

如果您看到的是&#x200B;**受限访问**&#x200B;消息，则您没有开发人员角色。 邀请您的IMS组织管理员参加入门会议。

![Adobe Developer Console — 访问受限消息](/help/assets/overview/dev-console-access-denied.png)

## [!DNL GitHub]

您需要在组织中拥有以下权限的[!DNL GitHub]帐户：

- **创建存储库** — 您需要在组织中创建两个存储库：一个用于应用程序代码，另一个用于EDS项目。 要进行验证，请转到[github.com/new](https://github.com/new) — 如果您可以从&#x200B;**所有者**&#x200B;下拉列表中选择您的组织，则您具有权限。

  ![GitHub新存储库所有者下拉列表显示组织选择](/help/assets/overview/github-repo-owner-dropdown.png)

- **安装[!DNL GitHub]应用** — 您需要相应的权限才能在组织上安装[!DNL GitHub]应用。 请参阅安装GitHub应用程序的[要求](https://docs.github.com/en/apps/using-github-apps/installing-a-github-app-from-a-third-party#requirements-to-install-a-github-app)。

**在加入会话之前验证您的权限**

在与Adobe会面之前，请运行此快速检查。 结果会告诉您哪些人需要待在房间中，而不是您是否可以继续。

1. 转到[github.com/new](https://github.com/new)，选择您的组织作为所有者，并创建名为`llm-apps-test`的存储库。
2. 转到[Adobe LLM Apps权限检查器](https://github.com/apps/adobe-llm-apps-permission-checker/installations/new)安装页面，并仅为`llm-apps-test`存储库安装应用程序。

| 结果 | 它的含义 | 操作 |
|---|---|---|
| 两个步骤都成功 | 您具有所需的权限 | 您已准备好入门培训课程 |
| 步骤2显示&#x200B;**请求**，而不是&#x200B;**安装** | 您没有安装[!DNL GitHub]应用的权限 | 邀请您的[!DNL GitHub]组织管理员参加入门培训会议 |

完成后，删除`llm-apps-test`存储库并从组织设置中卸载权限检查器应用。

## AEM Sites与[!DNL Edge Delivery Services]

操作构件托管在&#x200B;**Adobe Experience Manager [!DNL Edge Delivery Services] (EDS)**&#x200B;上。 您的组织需要包含[!DNL Edge Delivery Services]的AEM Sites许可证。 您的EDS组织中必须具有&#x200B;**管理员**&#x200B;角色。

要进行验证，请转到[EDS用户管理工具](https://tools.aem.live/tools/user-admin/index.html)，输入组织名称，将&#x200B;**站点**&#x200B;留空，然后单击&#x200B;**获取用户**。 在列表中查找您的帐户，并确认它显示&#x200B;**管理员**&#x200B;徽章。

![EDS用户管理员工具显示具有管理员角色的用户](/help/assets/overview/eds-user-admin.png)

如果您还没有EDS组织，则无需执行任何操作 — 在新用户引导过程中将为您创建一个组织。

## LLM平台（用于测试）

要测试已部署的应用程序，您需要一个支持允许自定义MCP应用程序并启用&#x200B;**开发人员模式**&#x200B;的订阅层。 例如，[!DNL ChatGPT]需要&#x200B;**Pro**、**Business**&#x200B;或&#x200B;**Enterprise / Edu**&#x200B;订阅。
