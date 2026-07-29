---
title: Adobe LLM应用程序概述
description: 了解什么是Adobe LLM应用程序、其工作方式以及您需要什么来开始使用。
source-git-commit: 8b4027d0fd73b8134a7478a5044f992e6cf03024
workflow-type: tm+mt
source-wordcount: '972'
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

LLM交互与传统搜索有着根本的不同。 平均[!DNL ChatGPT]会话的持续时间是传统搜索会话的四倍。 超过40%的消费者依赖人工智能工具做出复杂的购买决策。 如果没有[!DNL LLM Apps]，您可能会赢得提及但失去客户。 [!DNL LLM Apps]确保您的品牌不仅可见，而且可在用户准备做出决定的确切时刻操作。

## 重要概念 {#key-concepts}

### LLM应用程序

用户在[!DNL ChatGPT]或其他LLM平台内与之交互的品牌助理。 它将您的所有操作组合在一起，并作为一个单元进行部署。

### 载入代理

通过&#x200B;**[!UICONTROL 自动构建我的应用程序]**&#x200B;启动的引导式应用程序构建工作流。 它分析您的网站，提出操作，并为每个操作生成处理程序和小组件。

### 操作 {#actions}

应用提供的功能，如&#x200B;*查找分发服务器*&#x200B;或&#x200B;*浏览产品*。 当请求与其描述匹配时，LLM平台将调用操作。 操作元数据在[!DNL LLM Apps]中管理，而它的处理程序是您[!DNL GitHub]存储库中的代码。

### 操作处理程序

调用操作时运行的服务器端函数。 它可以验证输入、调用API并返回文本和结构化数据。

### 小组件 {#widgets-eds}

随LLM回复一起显示的可视响应，如信息卡、轮盘或表格。 生成的构件是您拥有的[!DNL Edge Delivery Services] (EDS)存储库中的块。

### MCP服务器

部署后公开的端点。 受支持的LLM平台连接到此端点，以发现和调用您的操作。

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

## 要求 {#requirements}

在创建应用程序之前，请完成以下所有要求。

### Adobe Developer Console

您的Adobe IMS组织必须有权访问[[!DNL App Builder]](https://developer.adobe.com/app-builder/docs/intro_and_overview/)。 您需要&#x200B;**开发人员**&#x200B;或&#x200B;**系统管理员**&#x200B;角色。

要验证您的访问权限，请打开[Adobe Developer Console](https://developer.adobe.com/console)。 “快速入门”屏幕确认您具有所需的访问权限。

![Adobe Developer Console — 确认开发人员访问权限的快速入门屏幕](/help/assets/overview/dev-console-access-granted.png)

如果您看到&#x200B;**受限访问**，请联系您的IMS组织管理员并请求开发人员角色。

![Adobe Developer Console — 访问受限消息](/help/assets/overview/dev-console-access-denied.png)

### [!DNL GitHub]

您需要一个[!DNL GitHub]帐户能够：

- 在拥有应用程序的帐户或组织中创建两个存储库。
- 安装或请求安装Adobe LLM应用程序[!DNL GitHub]应用程序。
- 安装或请求安装适用于EDS存储库的AEM代码同步。

要验证存储库创建权限，请打开[github.com/new](https://github.com/new)，并确认目标帐户或组织出现在&#x200B;**所有者**&#x200B;下。

![GitHub — 选择存储库所有者](/help/assets/overview/github-repo-owner-dropdown.png)

对于组织拥有的存储库，组织管理员可能需要批准[!DNL GitHub]应用。 仅向LLM应用程序使用的存储库授予每个应用程序访问权限。

### AEM Sites与Edge Delivery Services

您的组织需要包含Edge Delivery Services (EDS)的Adobe Experience Manager Sites许可证。 您还需要具有从构件存储库创建的EDS站点的管理员访问权限。

要验证访问权限，请打开[EDS用户管理工具](https://tools.aem.live/tools/user-admin/index.html)，输入组织名称，然后获取用户。 确认您的帐户具有&#x200B;**管理员**&#x200B;徽章。

### 网站

您需要一个公共HTTPS网站，以表示应用程序应支持的产品、服务或任务。 载入代理将分析此网站以建议操作并创建具有代表性的示例数据。

请勿使用公开机密或受访问控制的信息的网站。

### [!DNL ChatGPT]以进行测试

要完成入门教程，请使用支持的[!DNL ChatGPT]计划并启用开发人员模式。 Workspace管理员可以限制访问。 查看ChatGPT中的[测试](/help/guides/test-in-chatgpt.md#plan-requirements)。

## 选择您的历程 {#choose-your-journey}

### &#x200B;1. 构建和启动您的第一个应用程序

从[生成并启动您的第一个应用程序](/help/guides/create-app.md)开始。 此历程从两个空存储库开始，以经测试为[!DNL ChatGPT]插件的生产就绪应用程序结束。

### &#x200B;2. 自定义生成的应用程序

在载入代理创建了应用程序并且您希望替换示例行为时，选择此历程：

1. [自定义生成的处理程序](/help/guides/customize-handler.md)以连接您的API并定义每个操作返回的数据。
2. [自定义生成的构件](/help/guides/widgets.md)以使用该数据并应用您的交互和设计。

### &#x200B;3. 从头开始添加新操作

选择[从头开始添加新操作](/help/guides/create-action.md)以定义新元数据、编写处理程序、连接构件、测试和部署操作。

### &#x200B;4. 连接现有EDS项目

当您已经拥有EDS网站或未使用入门代理时，请选择[连接现有的EDS项目](/help/guides/bring-your-own-eds.md)。

每个历程都使用共享的[部署](/help/guides/deploy-your-app.md)和[ChatGPT插件测试](/help/guides/test-in-chatgpt.md)步骤。

