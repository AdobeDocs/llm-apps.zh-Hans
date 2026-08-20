---
title: Adobe LLM应用程序概述
description: 了解什么是Adobe LLM应用程序、其工作方式以及您需要什么来开始使用。
source-git-commit: 2f3480b3667a6ab7c4ed65b999eed4638c383edb
workflow-type: tm+mt
source-wordcount: '969'
ht-degree: 1%

---


# Adobe LLM应用程序 — 概述 {#adobe-llm-apps-an-overview}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps]当前在Beta中。
>
>此处显示的功能、工作流和UI不一定表示产品的最终状态。 要加入Beta，请发送电子邮件至llm-apps-beta@adobe.com 。

## 什么是[!DNL Adobe LLM Apps]？

[!DNL Adobe LLM Apps]允许您的品牌在AI助理（如[!DNL ChatGPT]）内提供有用的操作，如产品发现、可用性检查或服务预订。

[!DNL LLM Apps]位于[experience.adobe.com](https://experience.adobe.com/#/@llmapps/llm-apps/)。

## 您可以使用[!DNL LLM Apps]做什么

- **创建品牌拥有的LLM操作** — 定义要在AI助理中激活的特定业务流程（例如，*计划一个测试驱动器*，*比较产品*，*预订服务*）。
- **生成交互式LLM小组件** — 在[!DNL GitHub]存储库中创建作为AEM组件管理的可视化UI组件（产品卡、预订表单、商店定位器）。
- **维护集中式品牌治理** — 作者和开发人员保留对LLM平台内公开的所有内容、副本和视觉效果的完全控制权，审批通过AEM进行管理。
- **部署到暂存和生产** — 受控部署管道允许您在暂存环境中测试体验，然后再升级到生产。
- **操作级别的控制可见性** — 部署后，可以在不重新部署整个应用程序的情况下打开或关闭各个操作。
- **衡量推动决策的因素** — 内置分析（由Adobe Customer Journey Analytics提供支持）表面操作触发器计数、成功率、放弃率、热门用户提示和可见度分数。

## 为什么[!DNL LLM Apps]重要

LLM交互与传统搜索有着根本的不同。 平均LLM会话的持续时间是传统搜索会话的四倍。 超过40%的消费者依赖人工智能工具做出复杂的购买决策。 如果没有[!DNL LLM Apps]，您可能会赢得提及但失去客户。 [!DNL LLM Apps]确保您的品牌不仅可见，而且可在用户准备做出决定的确切时刻操作。

## 重要概念 {#key-concepts}

### LLM应用程序

用户在[!DNL ChatGPT]或其他LLM平台内与之交互的品牌助理。 它将您的所有操作组合在一起，并作为一个单元进行部署。

### 操作 {#actions}

应用提供的功能，如&#x200B;*查找分发服务器*&#x200B;或&#x200B;*浏览产品*。 当请求与其描述匹配时，LLM平台将调用操作。 操作元数据在[!DNL LLM Apps]中管理，而它的处理程序是您[!DNL GitHub]存储库中的代码。

### 操作处理程序

调用操作时运行的服务器端函数。 它可以验证输入、调用API并返回文本和结构化数据。

### 小组件 {#widgets-eds}

随LLM回复一起显示的可视响应，如信息卡、轮盘或表格。 生成的构件是您拥有的[!DNL Edge Delivery Services] (EDS)存储库中的块。

### MCP服务器

部署后公开的端点。 受支持的LLM平台连接到此端点，以发现和调用您的操作。

## 工作原理

从较高的层次来看，会发生三件事：你告诉[!DNL LLM Apps]你的品牌提供了什么，它就会变成一个AI助理可以做的事情，你的客户就会得到一个真正的答案 — 就在聊天室里。

```
┌────────────────────┐          ┌────────────────────┐          ┌────────────────────┐
│     Your brand     │          │      LLM Apps      │          │    AI assistant    │
│                    │          │                    │          │                    │
│   What you offer   │   ───▶   │  Turns that into   │   ───▶   │ Answers with your  │
│  and how you help  │          │    something AI    │          │    brand, live     │
│  customers today   │          │     can act on     │          │  inside the chat   │
└────────────────────┘          └────────────────────┘          └────────────────────┘
```

想要技术细节 — 您构建什么，以及各个部分如何拼合？ 查看[应用程序如何连接在一起](/help/guides/app-architecture.md)。

## 要求 {#requirements}

在创建应用程序之前，请完成以下所有要求。

### Adobe Developer Console

您的Adobe IMS组织必须有权访问[[!DNL App Builder]](https://developer.adobe.com/app-builder/docs/intro_and_overview/)。 您需要&#x200B;**开发人员**&#x200B;或&#x200B;**系统管理员**&#x200B;角色。

要验证您的访问权限，请打开[Adobe Developer Console](https://developer.adobe.com/console)。 “快速入门”屏幕确认您具有所需的访问权限。

![Adobe Developer Console — 确认开发人员访问权限的快速入门屏幕](/help/assets/overview/dev-console-access-granted.png)

如果您看到&#x200B;**受限访问**，请联系您的IMS组织管理员并请求开发人员角色。

![Adobe Developer Console — 访问受限消息](/help/assets/overview/dev-console-access-denied.png)

### [!DNL GitHub]

您需要一个[!DNL GitHub]帐户，以便&#x200B;**可以**&#x200B;执行以下操作。 这是权限检查 — 不要安装任何内容：

- 在拥有应用程序的帐户或组织中创建两个存储库。
- 稍后在设置过程中安装[!DNL GitHub]应用，或者拥有可以批准这些应用的组织管理员。

要验证存储库创建权限，请打开[github.com/new](https://github.com/new)，并确认目标帐户或组织出现在&#x200B;**所有者**&#x200B;下。

![GitHub — 选择存储库所有者](/help/assets/overview/github-repo-owner-dropdown.png)

对于组织拥有的存储库，组织管理员可能需要批准[!DNL GitHub]应用。

>[!NOTE]
>
>这是权限检查，而不是设置步骤。 尚未安装任何[!DNL GitHub]应用 — [自动创建您的第一个应用](/help/guides/create-app.md)将指导您完成每个应用的安装，每个应用均位于您创建的确切存储库所需的范围内。

### 网站

您需要一个公共HTTPS网站，以表示应用程序应支持的产品、服务或任务。 该平台分析该网站以建议操作并创建具有代表性的示例数据。

请勿使用公开机密或受访问控制的信息的网站。

### 用于测试的[!DNL ChatGPT]或[!DNL Claude]

要完成入门教程，请使用支持且启用了开发人员模式的[!DNL ChatGPT]计划，或者使用支持且启用了自定义连接器的[!DNL Claude]计划。 Workspace或组织管理员可以限制访问权限。 查看ChatGPT中的[测试](/help/guides/test-in-chatgpt.md#plan-requirements)或Claude中的[测试](/help/guides/test-in-claude.md#plan-requirements)。

## 选择您的历程 {#choose-your-journey}

### &#x200B;1. 构建和启动您的第一个应用程序

从[生成并启动您的第一个应用程序](/help/guides/create-app.md)开始。 此历程从两个空存储库开始，以作为受支持的LLM平台（如[!DNL ChatGPT]）中的插件测试的生产就绪应用程序结束。

### &#x200B;2. 自定义生成的应用程序

当平台自动创建应用程序并且您希望替换示例行为时，请选择此历程：

1. [自定义生成的处理程序](/help/guides/customize-handler.md)以连接您的API并定义每个操作返回的数据。
2. [自定义生成的构件](/help/guides/widgets.md)以使用该数据并应用您的交互和设计。

### &#x200B;3. 从头开始添加新操作

选择[从头开始添加新操作](/help/guides/create-action.md)以定义新元数据、编写处理程序、连接构件、测试和部署操作。

### &#x200B;4. 连接现有EDS项目

当您已经拥有EDS网站或未自动构建应用程序时，请选择[连接现有的EDS项目](/help/guides/bring-your-own-eds.md)。

每个历程都使用共享的[部署](/help/guides/deploy-your-app.md)步骤，然后[ChatGPT插件测试](/help/guides/test-in-chatgpt.md)或[克劳德连接器测试](/help/guides/test-in-claude.md)。

