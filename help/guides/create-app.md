---
title: 创建应用程序
description: 了解如何创建首个LLM应用程序并将其链接到您的GitHub存储库。
source-git-commit: 1a99e2e80e50a3bcf9ce6fb910365202bf06e113
workflow-type: tm+mt
source-wordcount: '745'
ht-degree: 0%

---


# 创建应用程序

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps]当前在Beta中。
>
>此处显示的功能、工作流和UI不一定表示产品的最终状态。 要加入Beta，请发送电子邮件至llm-apps-beta@adobe.com 。

>[!NOTE]
>
>如果您是&#x200B;**Beta计划参与者**，请改用[Beta入门指南](/help/beta-onboarding/beta-onboarding.md) — 它涵盖您的特定应用程序的端到端完整设置。

>[!NOTE]
>
>在开始之前，请确保满足所有[先决条件](/help/overview/overview.md#prerequisites)。

本指南将指导您完成创建第一个[!DNL Adobe LLM Apps] — 从空状态到链接到[!DNL GitHub]存储库的完全配置项目。

## 打开[!DNL LLM Apps]

导航到[experience.adobe.com/llm-apps](https://experience.adobe.com/llm-apps)。 如果尚未创建应用程序，您会看到首次加载页面，提示您创建第一个应用程序。

![应用页面 — 尚未创建任何应用](/help/assets/guide-create-app/first-load.png)

左侧边栏允许您在&#x200B;**[!UICONTROL 应用程序]**&#x200B;和&#x200B;**[!UICONTROL 操作]**&#x200B;之间导航。 单击&#x200B;**[!UICONTROL 创建应用程序]**&#x200B;以开始。

## 填写应用程序详细信息

创建应用程序对话框会打开全屏。

![创建应用程序对话框](/help/assets/guide-create-app/app-details-1.png)

输入以下内容：

- **[!UICONTROL LLM应用程序名称]**（必需） — 应用程序的显示名称。 只允许使用字母、数字和空格。
- **[!UICONTROL LLM应用程序说明]** — 对应用程序功能的简短说明。 例如，*帮助用户通过LLM平台*&#x200B;发现产品和预订服务。
- **[!UICONTROL 您的网站]**（必需） — 您的品牌网站的URL。 [!DNL LLM Apps]使用此项自动创建预配置的操作。

## 选择分析数据区域

选择将存储此应用程序的分析数据的区域。

>[!IMPORTANT]
>
>创建应用程序后，无法更改Analytics数据区域。

![Analytics数据区域下拉列表](/help/assets/guide-create-app/app-details-analytics-dropdown.png)

**Analytics区域**&#x200B;下拉列表默认为&#x200B;**美国（美国）**。 可用选项为&#x200B;**美国(US)**&#x200B;和&#x200B;**欧洲(EU)**。 在继续操作之前，请选择最符合您的数据派驻要求的区域。

## 链接[!DNL GitHub]存储库

在应用详细信息下方，您可以链接[!DNL GitHub]存储库。 此存储库是您的操作处理程序代码所在的位置 — 当LLM平台调用您的应用程序时，JavaScript会在[!DNL Adobe I/O Runtime]上运行的`actions/`文件夹下运行。

如果这是您第一次这样做，则列表中不会显示存储库。 您需要在组织上安装&#x200B;**[!DNL Adobe LLM Apps Link]** [!DNL GitHub]应用：

1. 单击对话框底部的&#x200B;**在Github上管理存储库**。
2. 这将在新选项卡中打开[!DNL Adobe LLM Apps Link] [!DNL GitHub]应用程序页面。

   ![Adobe LLM应用程序链接 — GitHub应用程序安装页面](/help/assets/guide-create-app/github-app-install.png)

3. 单击&#x200B;**[!UICONTROL 安装]**&#x200B;并选择您的[!DNL GitHub]组织。
4. 在&#x200B;**[!UICONTROL 存储库访问权限]**&#x200B;下，选择&#x200B;**仅选择存储库**，然后选择将托管应用程序代码的存储库。

   ![Adobe LLM Apps链接 — 存储库访问](/help/assets/guide-create-app/github-repo-access.png)

5. 单击&#x200B;**[!UICONTROL 保存]**。 返回到“创建应用程序”对话框 — 您的存储库现在显示在&#x200B;**选择存储库**&#x200B;下拉列表中。
6. 选择您要使用的存储库。

![创建应用程序对话框 — 已链接的存储库](/help/assets/guide-create-app/app-details-repo-linked.png)

>[!NOTE]
>
>您可以在应用程序创建期间跳过链接存储库，并稍后从应用程序设置中执行此操作。 但是，在链接存储库之前，您无法部署。

## 创建应用程序

单击&#x200B;**[!UICONTROL 创建应用程序]**。 在Developer Console中创建项目时，会显示一个加载屏幕。

![正在创建应用程序 — 正在加载屏幕](/help/assets/guide-create-app/app-loading.png)

完成后，您将被重定向到&#x200B;**应用程序详细信息**&#x200B;页面。

## 应用程序详细信息页面

“应用程序详细信息”页面是管理应用程序的中心枢纽。

![应用程序详细信息页面 — 热门部分](/help/assets/guide-create-app/app-detail-top.png)

### 应用程序横幅

![应用横幅](/help/assets/guide-create-app/app-banner.png)

顶部的彩色横幅显示当前选择的应用程序，包括应用程序头像、名称、描述以及用于在各应用程序之间切换的下拉列表。 滚动时，横幅在顶部保持固定状态。

### 页面标题和操作

![应用横幅](/help/assets/guide-create-app/page-title.png)

在横幅下方，您会看到应用程序名称作为标题，其中包含以下操作按钮：

- **...** （更多操作） — 创建新应用程序或删除当前应用程序。
- **[!UICONTROL 设置]** — 配置链接的存储库和其他选项。
- **[!UICONTROL 部署]** — 将应用程序部署到[!DNL Adobe I/O Runtime]（在链接存储库之前禁用）。

### 应用程序信息卡

![应用信息卡](/help/assets/guide-create-app/app-info-card.png)

此信息卡总结了您应用程序的密钥元数据：名称、描述、状态标记（**未部署**&#x200B;或&#x200B;**已部署**）、应用程序ID和创建日期。 它还会显示两个链接的存储库：

- **处理程序存储库** — 操作处理程序代码所在的位置（JavaScript在[!DNL Adobe I/O Runtime]上运行）。
- **EDS存储库** — 小部件UI所在的位置（[!DNL Edge Delivery Services]提供的块和样式）。

### 操作、测试应用程序和部署历史记录

![应用程序详细信息页面 — 底部部分](/help/assets/guide-create-app/app-detail-bottom.png)

在信息卡下方，您可以找到三个部分：

- **[!UICONTROL 操作]** — 列出为您的应用程序定义的操作处理程序。 单击&#x200B;**转到操作**&#x200B;以导航到操作页面。
- **[!UICONTROL 测试应用程序]** — 部署后，显示暂存和生产环境的MCP服务器URL。
- **部署历史记录** — 跨具有状态和日期的环境跟踪每个部署。

## 后续步骤

- [指南：创建操作](/help/guides/create-action.md) — 定义具有元数据和小组件设置的操作。

