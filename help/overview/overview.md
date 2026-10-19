---
title: Adobe LLM应用程序概述
description: 了解什么是Adobe LLM应用程序、其工作方式以及如何开始构建应用程序。
source-git-commit: a48b4022979d9a13b4fc3aa870c4ee6375e8bc13
workflow-type: tm+mt
source-wordcount: '621'
ht-degree: 0%
---

# Adobe LLM应用程序 — 概述 {#adobe-llm-apps-an-overview}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps]当前在Beta中。
>
>此处显示的功能、工作流和UI不一定表示产品的最终状态。 要加入Beta，请发送电子邮件至llm-apps-beta@adobe.com 。

## 什么是[!DNL Adobe LLM Apps]？

[!DNL Adobe LLM Apps]允许您的品牌在受支持的AI助理（如[!DNL ChatGPT]和[!DNL Claude]）内提供有用的操作，如产品发现、可用性检查或服务预订。

[!DNL LLM Apps]位于[experience.adobe.com/llm-apps](https://experience.adobe.com/llm-apps)。

## 您可以使用[!DNL LLM Apps]做什么

- **创建品牌拥有的LLM操作** — 定义要在AI助理中激活的特定业务流程（例如，*计划一个测试驱动器*，*比较产品*，*预订服务*）。
- **生成交互式LLM小组件** — 在[!DNL GitHub]存储库中创建作为AEM组件管理的可视化UI组件（产品卡、预订表单、商店定位器）。
- **维护集中式品牌治理** — 作者和开发人员保留对LLM平台内公开的所有内容、副本和视觉效果的完全控制权，审批通过AEM进行管理。
- **部署到暂存和生产** — 受控部署管道允许您在暂存环境中测试体验，然后再升级到生产。
- **操作级别的控制可见性** — 启用或禁用单个操作，然后部署应用程序以应用更改。
- **衡量推动决策的因素** — 内置分析（由Adobe Customer Journey Analytics提供支持）表面操作触发器计数、成功率、放弃率、热门用户提示和可见度分数。

## 为什么[!DNL LLM Apps]重要

LLM交互与传统搜索有着根本的不同。 平均LLM会话的持续时间是传统搜索会话的四倍。 超过40%的消费者依赖人工智能工具做出复杂的购买决策。 如果没有[!DNL LLM Apps]，您可能会赢得提及但失去客户。 [!DNL LLM Apps]确保您的品牌不仅可见，而且可在用户准备做出决定的确切时刻操作。

## 重要概念 {#key-concepts}

### LLM应用程序

用户在[!DNL ChatGPT]或其他LLM平台内与之交互的品牌助理。 它将您的所有操作组合在一起，并作为一个单元进行部署。

### 操作 {#actions}

应用提供的功能，如&#x200B;*查找分发服务器*&#x200B;或&#x200B;*浏览产品*。 当请求与其描述匹配时，LLM平台将调用操作。 [!DNL LLM Apps]管理操作元数据，[!DNL GitHub]存储库存储处理程序代码。

### 操作处理程序

LLM平台调用操作时运行的服务器端函数。 使用处理程序验证输入，调用API，并返回文本和结构化数据。

### 小组件 {#widgets-eds}

随LLM回复一起显示的可视响应，如信息卡、轮盘或表格。 生成的构件是[!DNL Edge Delivery Services] (EDS)存储库中的块。 借助Adobe托管的基础架构，Adobe可为试用准备并托管存储库。 或者，连接您自己的存储库。

### MCP服务器

部署后公开的端点。 受支持的LLM平台连接到此端点，以发现和调用您的操作。

## 工作原理

您在[!DNL LLM Apps]中定义您品牌的功能。 平台将这些功能作为操作提供，AI助手使用这些操作响应对话中的客户请求。

```
┌────────────────────┐          ┌────────────────────┐          ┌────────────────────┐
│     Your brand     │          │      LLM Apps      │          │    AI assistant    │
│                    │          │                    │          │                    │
│   What you offer   │   ───▶   │  Turns that into   │   ───▶   │ Answers with your  │
│  and how you help  │          │    something AI    │          │    brand, live     │
│  customers today   │          │     can act on     │          │  inside the chat   │
└────────────────────┘          └────────────────────┘          └────────────────────┘
```

有关要构建的内容以及组件如何协作的技术详细信息，请参阅[应用程序如何连接在一起](/help/guides/app-architecture.md)。

## 开始使用 {#choose-your-journey}

创建应用程序和创建其操作是单独的步骤：

1. [设置基础结构](/help/guides/create-app.md)。 使用Adobe托管的基础架构进行试用，或通过创建应用程序向导提供两个空的[!DNL GitHub]存储库。 该平台初始化一个用于操作处理程序，另一个用于小组件。
2. [创建您的操作](/help/guides/creating-actions.md)。 让平台分析您的网站并生成操作，或自行创建操作。
3. [部署和测试应用程序](/help/guides/deploy-your-app.md)。 审阅生成的工作，部署到暂存环境，并在支持的LLM平台中进行测试。

自动生成为您提供了示例行为的起点。 在将应用提供给客户之前，请[将处理程序连接到系统](/help/guides/customize-handler.md)和[自定义小组件](/help/guides/widgets.md)。
