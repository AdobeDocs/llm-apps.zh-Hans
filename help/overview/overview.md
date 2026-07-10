---
title: Adobe LLM应用程序概述
description: 了解什么是Adobe LLM应用程序、其工作方式以及您需要什么来开始使用。
source-git-commit: 344c5457eb79a19b1dae823732a1cd9866dcd9dc
workflow-type: tm+mt
source-wordcount: '831'
ht-degree: 1%

---


# Adobe LLM应用程序 — 概述 {#adobe-llm-apps-an-overview}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps]当前在Beta中。
>
>此处显示的功能、工作流和UI不一定表示产品的最终状态。 要加入Beta，请发送电子邮件至llm-apps-beta@adobe.com 。

## 什么是[!DNL Adobe LLM Apps]？

[!DNL Adobe LLM Apps]允许您的品牌直接在AI助理（如[!DNL ChatGPT]或Claude）中公开关键操作，如产品发现、可用性检查或服务预订。 您的品牌不必在人工智能生成的答案中被被动提及，而是可以指导客户完成真实的业务流，而无需他们离开对话。

[!DNL LLM Apps]位于[experience.adobe.com/llm-apps](https://experience.adobe.com/llm-apps)。

## 您可以使用[!DNL LLM Apps]做什么

- **创建品牌拥有的LLM操作** — 定义要在AI助理中激活的特定业务流程（例如，*计划一个测试驱动器*，*比较产品*，*预订服务*）。
- **生成交互式LLM小组件** — 在[!DNL GitHub]存储库中创建作为AEM组件管理的可视化UI组件（产品卡、预订表单、商店定位器）。
- **维护集中式品牌治理** — 作者和开发人员保留对LLM平台内公开的所有内容、副本和视觉效果的完全控制权，审批通过AEM进行管理。
- **部署到暂存和生产** — 受控部署管道允许您在暂存环境中测试体验，然后再升级到生产。
- **操作级别的控制可见性** — 部署后，可以在不重新部署整个应用程序的情况下打开或关闭各个操作。
- **衡量推动决策的因素** — 内置分析（由Adobe Customer Journey Analytics提供支持）表面操作触发器计数、成功率、放弃率、热门用户提示和可见度分数。

## 为什么[!DNL LLM Apps]重要

LLM交互与传统搜索有着根本的不同。 平均[!DNL ChatGPT]会话的持续时间是传统搜索会话的四倍。 超过40%的消费者依赖人工智能工具做出复杂的购买决策。 如果没有[!DNL LLM Apps]，您可能会赢得提及但失去客户。 [!DNL LLM Apps]确保您的品牌不仅可见，而且可在用户准备做出决定的确切时刻操作。

## 重要概念

**LLM应用程序** — 用户在[!DNL ChatGPT]或其他LLM平台中与之交互的品牌助理。 它将您的所有操作组合在一起，并作为一个单元进行部署。

**操作** — 您的应用程序提供的功能。 例如，“查找分销商”或“浏览产品”。 当用户提出相关问题时，LLM会调用每个操作。 每个操作包含两个部分：在[!DNL LLM Apps] UI中管理的元数据（名称、描述、参数）以及在[!DNL GitHub]中管理的处理程序（您的代码）。

**操作处理程序** — 调用操作时运行的代码。 它可以调用您的API、获取实时数据或返回静态数据。 处理程序位于`actions/<name>/index.js`的[!DNL GitHub]存储库中。

**小组件** — 向用户显示的可视响应 — 卡片、轮播、表格或任何在LLM文本回复旁边呈现的自定义UI。 小组件是在[!DNL Edge Delivery Services] (EDS)网站上托管的HTML页面。

## 工作原理

下图显示了各个部分的组合方式 — 从在UI中定义应用程序到在LLM平台中查看结果。

```
┌─────────────────────────────────────────────────────────────┐
│                      LLM Apps UI                            │
│  ┌──────────┐   ┌──────────┐   ┌───────────────────────┐    │
│  │   App    │──▶│ Actions  │──▶│ Metadata + Widget cfg │    │
│  └──────────┘   └──────────┘   └───────────┬───────────┘    │
└─────────────────────────────────────────── │ ────────────-──┘
                                             │ deploy
                                             ▼
┌─────────────────────────────────────────────────────────────┐
│                  Adobe I/O Runtime                          │
│               MCP Server (auto-generated)                   │
│  ┌───────────────┐ ┌──────────────────┐ ┌───────────────┐   │
│  │ search-       │ │ get-product-     │ │ find-where-   │   │
│  │ products      │ │ details          │ │ to-buy        │   │
│  └───────────────┘ └──────────────────┘ └───────────────┘   │
└──────────────────────────────┬──────────────────────────────┘
                               │ MCP protocol
                               ▼
┌─────────────────────────────────────────────────────────────┐
│                        ChatGPT                              │
│  Conversation                                               │
│  ┌───────────────────────────────────────────────────────┐  │
│  │  EDS Widget                                           │  │
│  │  Product carousel, store locator, detail card ...     │  │
│  └───────────────────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────────────┘
```

## 先决条件

### Adobe Developer Console

您需要使用Adobe IMS组织中的&#x200B;**开发人员**&#x200B;角色（或&#x200B;**系统管理员**&#x200B;角色）访问[Adobe Developer Console](https://developer.adobe.com/console)。 确保您的组织有权访问[[!DNL App Builder]](https://developer.adobe.com/app-builder/docs/intro_and_overview/)。

要进行验证，请转到[developer.adobe.com/console](https://developer.adobe.com/console)。 如果您看到“快速入门”屏幕，则表明您的权限设置正确。

![Adobe Developer Console — 确认开发人员访问权限的快速入门屏幕](/help/assets/overview/dev-console-access-granted.png)

如果您看到的是&#x200B;**受限访问**&#x200B;消息，则您没有开发人员角色。 请联系您的IMS组织管理员以请求访问权限。

![Adobe Developer Console — 访问受限消息](/help/assets/overview/dev-console-access-denied.png)

### [!DNL GitHub]

您需要在组织中拥有以下权限的[!DNL GitHub]帐户：

- **创建存储库** — 您需要在组织中创建两个存储库：一个用于应用程序代码，另一个用于EDS项目。 要进行验证，请转到[github.com/new](https://github.com/new) — 如果您可以从&#x200B;**所有者**&#x200B;下拉列表中选择您的组织，则您具有权限。

  ![GitHub新存储库所有者下拉列表显示组织选择](/help/assets/overview/github-repo-owner-dropdown.png)

- **安装[!DNL GitHub]应用** — 您需要相应的权限才能在组织上安装[!DNL GitHub]应用。 请参阅安装GitHub应用程序的[要求](https://docs.github.com/en/apps/using-github-apps/installing-a-github-app-from-a-third-party#requirements-to-install-a-github-app)。

### AEM Sites与[!DNL Edge Delivery Services]

操作构件托管在&#x200B;**Adobe Experience Manager [!DNL Edge Delivery Services] (EDS)**&#x200B;上。 您的组织需要包含[!DNL Edge Delivery Services]的AEM Sites许可证。 您的EDS组织中必须具有&#x200B;**管理员**&#x200B;角色。

要进行验证，请转到[EDS用户管理工具](https://tools.aem.live/tools/user-admin/index.html)，输入组织名称，将&#x200B;**站点**&#x200B;留空，然后单击&#x200B;**获取用户**。 在列表中查找您的帐户，并确认它显示&#x200B;**管理员**&#x200B;徽章。

![EDS用户管理员工具显示具有管理员角色的用户](/help/assets/overview/eds-user-admin.png)

### LLM平台（用于测试）

要测试已部署的应用程序，您需要一个支持允许自定义MCP应用程序并启用&#x200B;**开发人员模式**&#x200B;的订阅层。 例如，[!DNL ChatGPT]需要&#x200B;**Pro**、**Business**&#x200B;或&#x200B;**Enterprise / Edu**&#x200B;订阅。

## 开始使用

考虑使用案例，[创建应用程序](/help/guides/create-app.md)以开始生成和部署您的[!DNL LLM Apps]体验。

