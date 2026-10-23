---
title: 设置Adobe Analytics和Customer Journey Analytics
description: 通过Adobe数据流将Adobe Experience Platform LLM应用程序事件转发到您自己的Adobe Analytics报表包和Customer Journey Analytics数据视图。
source-git-commit: 557173e8d95325b980efac4d72c06bb3d90dcd72
workflow-type: tm+mt
source-wordcount: '2962'
ht-degree: 2%
---

# 设置Adobe Analytics和Customer Journey Analytics {#analytics-setup}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps]当前在Beta中。
>
>此处显示的功能、工作流和UI不一定表示产品的最终状态。 要加入Beta，请发送电子邮件至llm-apps-beta@adobe.com 。

将您应用的事件发送到[!DNL Adobe Analytics]、[!DNL Customer Journey Analytics]或两者。 [!DNL LLM Apps]通过您拥有的数据流将事件转发到[!DNL Adobe Experience Platform] Edge Network。 数据流将事件路由到您添加到它的每个服务。

- **[!DNL Adobe Analytics]**&#x200B;报告LLM平台调用的操作、用户选择的链接构件以及每个调用背后的意图。
- **[!DNL Customer Journey Analytics]**&#x200B;在您基于[!DNL Experience Platform]数据构建的数据视图中，按人员分析相同的事件。

此设置与[!DNL LLM Apps]中的内置分析不同。

**在本指南中：**&#x200B;了解[!DNL LLM Apps]在[!DNL Experience Platform]、[!DNL Adobe Analytics]和[!DNL Customer Journey Analytics]中需要哪些设置，以及指向每个产品屏幕的Experience League过程的链接。 接下来，在应用程序中启用Analytics，部署到暂存环境，并在部署到生产环境之前验证数据。

## 设置核对清单 {#setup-checklist}

完成为您使用的服务标记为&#x200B;**必需**&#x200B;的每个步骤。 跳过的步骤很少显示错误。 相反，数据并未到达。

| 步骤 | [!DNL Adobe Analytics] | [!DNL Customer Journey Analytics] |
|---|---|---|
| [1. 创建身份命名空间](#identity-namespace) | 必需 | 必需 |
| [2. 创建架构](#schema) | 必需 | 必需 |
| [3. 创建数据集](#dataset) | 不需要 | 必需 |
| [4. 准备报表包](#report-suite) | 必需 | 不需要 |
| [5. 创建数据流](#datastream) | 必需 | 必需 |
| [6. 启用Analytics并部署到暂存](#enable-analytics) | 必需 | 必需 |
| [7. 创建连接和数据视图](#cja) | 不需要 | 必需 |
| [8. 验证数据并部署到生产环境](#verify) | 必需 | 必需 |

在同一[!DNL Experience Platform]沙盒中创建命名空间、架构、数据集和数据流。 在生产中使用您计划从中报告的沙盒和数据流。 在应用程序中保存数据流后，请与LLM应用程序团队联系以进行更改。

## 开始之前 {#before-you-begin}

您需要：

- **支持的运行时和SDK。** 您的处理程序存储库必须使用`@adobe/llm-apps-runtime` 1.2.0或更高版本。 对于EDS小组件，请使用LLM应用程序SDK 0.1.13或更高版本。 若要在启用Analytics之前将旧版应用程序更新为最新的运行时和SDK，请按照[升级现有应用程序](/help/guides/upgrade-existing-app.md)中的说明进行操作。 满足这些要求的应用程序不需要升级。
- **代理字段组。** 必须为代理信息和对话事件字段组启用您的组织。 如果向架构添加字段组时未显示这些字段组，请联系您的Adobe客户团队。 请参阅[实施对话分析](https://experienceleague.adobe.com/en/docs/analytics-platform/using/conversation-insights/implement#schema-and-datasets)。 操作调用记录在代理信息的`agenticExperience.tools`字段中，该字段位于[!DNL Experience Platform]的Beta中。
- **[!DNL Experience Platform]权限**，用于在沙盒中创建身份命名空间、架构、数据集和数据流。
- **用于[!DNL Adobe Analytics]：**&#x200B;管理报表包和处理规则的权限。 默认情况下，产品管理员具有这两个角色。
- **对于[!DNL Customer Journey Analytics]：**&#x200B;产品管理员创建连接和数据视图的访问权限。

## 您的应用程序发送的内容 {#what-your-app-sends}

启用Analytics和部署后，您的应用程序会发送两种事件：

| 事件 | `eventType` | 发送时间 | 包括 |
|---|---|---|---|
| 操作调用 | `mcp.tool_call` | LLM平台调用应用程序的某个操作，包括失败或返回登录提示的调用。 | 操作的代码标识符以及调用是否失败。 用户意图（当操作收集它并且平台提供意图时）。 当平台提供时：平台名称、对话ID、平台的用户ID、区域设置和组织，以及用户的大致位置。 |
| 构件交互 | `web.webinteraction.linkClicks` | 有人在Edge Delivery Services小组件中选择链接或按钮。 | 链接或按钮标签，最多100个字符。 当由操作调用提供时，其对话ID和轮询ID。 Adobe Web SDK还添加了构件框架URL、屏幕和浏览器窗口大小、本地时间和时区，以及它自己的版本。 |

在[!DNL Adobe Analytics]中，每个操作调用都计为名为`mcp:<code identifier>`的页面的页面视图，而每个小组件交互都计为链接点击。 查看Adobe Analytics中的[Edge Network事件类型](https://experienceleague.adobe.com/en/docs/analytics/implementation/aep-edge/hit-types)。

每个action调用和每个Widget交互都是[!DNL Experience Platform]数据集中的一个[!DNL Adobe Analytics]点击和一个事件。 在高流量应用程序上，在启用Analytics之前，请检查您的合同是否涵盖此流量。

在阅读报告时，请牢记以下详细信息：

- **失败的调用包含登录提示。** 如果某些操作需要登录，则预期这些操作会有更高的失败率。
- **未记录某些被拒绝的呼叫。** 当[!DNL ChatGPT]以外的平台调用需要在没有有效登录或所需范围的情况下登录的操作时，调用在操作运行之前被拒绝，并且不发送任何事件。 在每次操作都需要登录的应用程序上，任何没有登录的调用都会以相同的方式被拒绝。
- **用户意图经常缺失。** 平台决定是否提供该服务，因此许多呼叫没有服务，显示为&#x200B;**未指定**。
- **平台详细信息不尽相同。** 目前，[!DNL ChatGPT]提供所有这些功能。 对于[!DNL Claude]，仅记录平台名称。
- **小组件标签来自页面。** 要设置稳定标签，请将`data-analytics`属性添加到链接或按钮。 否则，会使用其可见文本。

要选择收集用户意图的操作，请参阅[配置Analytics](/help/guides/create-action.md#configure-analytics)。

>[!NOTE]
>
>这些事件可以包括平台用户ID和大致位置。 在启用Analytics之前，请查看您的隐私策略和数据治理设置。

### 事件如何识别人员 {#identities}

| 事件 | 主要身份 | 命名空间 |
|---|---|---|
| 操作调用 | LLM平台中的用户ID（当平台提供） | `MCPHOSTUSER` |
| 构件交互 | Edge Network在操作调用期间发出的Experience Cloud ID (ECID)。 `MCPHOSTUSER` ID在可用时作为辅助标识包括在内。 | `ECID` |

某些平台（如[!DNL Claude]）不提供用户ID。 来自这些平台的操作调用没有标识，因此[!DNL Customer Journey Analytics]将跳过它们。

>[!NOTE]
>
>每个操作调用均会获得一个新的ECID，并且构件交互会使用显示该构件的操作调用的ECID。 因此：
>
>- [!DNL Customer Journey Analytics]将用户的操作调用计为一个人，但将其小组件的交互计为每个操作调用的不同人。
>- [!DNL Adobe Analytics]可能会将每个操作调用计为新访客。

## 步骤1：创建身份命名空间 {#identity-namespace}

**对于** [!DNL Adobe Analytics]和[!DNL Customer Journey Analytics]是必需的。

操作调用识别`MCPHOSTUSER`命名空间中的人员，因此命名空间必须存在于沙盒中。 使用以下设置创建自定义命名空间：

| 设置 | 值 |
|---|---|
| 显示名称 | `MCP host user`或您选择的其他名称 |
| 身份符号 | `MCPHOSTUSER`，与显示的完全相同 |
| 身份标识类型 | **[!UICONTROL 单个跨设备ID]** |

有关过程，请参阅[创建自定义命名空间](https://experienceleague.adobe.com/en/docs/experience-platform/identity/features/namespaces#create-namespaces)。

## 第2步：创建架构 {#schema}

**对于** [!DNL Adobe Analytics]和[!DNL Customer Journey Analytics]是必需的。

数据流和数据集都使用此架构。 使用以下设置创建架构：

| 设置 | 值 |
|---|---|
| 架构类型 | **[!UICONTROL 标准]**，使用&#x200B;**[!UICONTROL 手动]**&#x200B;选项创建 |
| 基类 | **[!UICONTROL 体验活动]** |
| 字段组 | 代理信息、对话事件、Web详细信息、环境详细信息以及实施详细信息 |
| 显示名称 | 任何名称，例如`AEM LLM Apps` |

>[!IMPORTANT]
>
>添加全部五个字段组。 [!DNL LLM Apps]需要代理信息和对话事件才能进行操作调用。 其他三个包含事件也包含的“页面”、“链接”、“设备”、“环境”和“实施”字段。 如果缺少字段组，[!DNL Experience Platform]可以删除这些字段或拒绝事件。

您不需要Adobe Analytics ExperienceEvent字段组。 用户意图改为通过处理规则达到[!DNL Adobe Analytics]。 请参阅[步骤4](#report-suite)。

有关过程，请参阅[创建新架构](https://experienceleague.adobe.com/en/docs/experience-platform/xdm/ui/resources/schemas#create)和[添加字段组](https://experienceleague.adobe.com/en/docs/experience-platform/xdm/ui/resources/schemas#add-field-groups)。

## 步骤3：创建数据集 {#dataset}

**需要：** [!DNL Customer Journey Analytics]。

数据集将您的应用程序事件存储在[!DNL Experience Platform]中。 使用以下设置创建数据集：

| 设置 | 值 |
|---|---|
| 创建方法 | **[!UICONTROL 从架构创建数据集]** |
| 架构 | [步骤2](#schema)中的架构 |
| 名称 | 任何名称，例如`AEM LLM Apps` |

有关过程，请参阅[创建数据集](https://experienceleague.adobe.com/en/docs/experience-platform/catalog/datasets/user-guide#create)。

## 步骤4：准备报表包 {#report-suite}

**需要：** [!DNL Adobe Analytics]。

[!DNL Adobe Analytics]自动映射页面名称和链接字段，因此`mcp:<code identifier>`页面和小组件链接点击不需要额外设置。 用户意图需要一个eVar和一个处理规则。

### 创建或选择报表包 {#create-report-suite}

创建报表包，或选择现有报表包。 记下完整的报表包ID，包括[!DNL Adobe Analytics]添加的前缀。 您需要[步骤5](#datastream)中的ID。

有关过程，请参阅[创建报表包](https://experienceleague.adobe.com/en/docs/analytics/admin/admin-tools/manage-report-suites/c-new-report-suite/t-create-a-report-suite)。

### 启用用户意图eVar {#user-intent-evar}

在报表包的转化变量中，使用以下设置启用eVar：

| 设置 | 值 |
|---|---|
| eVar | EVAR3 |
| 名称 | `User Intent` |
| 类型 | **[!UICONTROL 文本字符串]** |
| 分配 | **[!UICONTROL 最近（最后一个）]** |
| 过期时间 | **[!UICONTROL 访问]** |
| 状态 | **[!UICONTROL 已启用]** |

如果报表包已使用eVar3，请选择另一个未使用的eVar，然后在处理规则中使用该eVar。

有关过程，请参阅[添加或编辑转化变量](https://experienceleague.adobe.com/en/docs/analytics/admin/admin-tools/manage-report-suites/edit-report-suite/conversion-variables/conversion-var-admin#add-or-edit-conversion-variables)。

### 创建处理规则 {#processing-rule}

[!DNL Adobe Analytics]将用户意图作为上下文数据变量接收，而不是作为eVar接收。 要了解原因，请参阅[隐式映射](https://experienceleague.adobe.com/en/docs/analytics/implementation/aep-edge/xdm-var-mapping#implicit-mapping)。 创建一个将用户意图复制到eVar中的处理规则：

- **条件：**&#x200B;上下文数据变量`a.x.conversation.prompt.raw.0.purpose`等于`intents`。
- **操作：**&#x200B;使用上下文数据变量`a.x.conversation.prompt.raw.0.text`覆盖&#x200B;**用户意图(eVar3)**&#x200B;的值。

如果没有此规则，**用户意图**&#x200B;维度将保留为空。 该规则仅适用于保存后收集的数据。 构件交互不带有用户意图，因此规则不会更改它们。

其他字段，例如调用失败还是平台名称，也作为上下文数据变量到达。 要在[!DNL Adobe Analytics]中报告这些规则，请以相同方式创建更多处理规则。

有关过程，请参阅[处理规则概述](https://experienceleague.adobe.com/en/docs/analytics/admin/admin-tools/manage-report-suites/edit-report-suite/report-suite-general/processing-rules/pr-overview)和[处理规则用例](https://experienceleague.adobe.com/en/docs/analytics/admin/admin-tools/manage-report-suites/edit-report-suite/report-suite-general/processing-rules/pr-use-cases)。

## 步骤5：创建数据流 {#datastream}

**对于** [!DNL Adobe Analytics]和[!DNL Customer Journey Analytics]是必需的。

数据流接收您应用程序的事件并将它们转发到您添加的每个服务。

### 创建数据流 {#create-datastream}

使用以下设置创建数据流：

| 设置 | 值 |
|---|---|
| 名称 | 任何名称，例如`AEM LLM Apps` |
| 映射架构 | [步骤2](#schema)中的架构 |

在与架构相同的沙盒中创建数据流。 创建数据流后，无法更改该沙盒。

有关过程，请参阅[创建数据流](https://experienceleague.adobe.com/en/docs/experience-platform/datastreams/configure#create)。

### 添加服务 {#add-services}

为每个目标添加服务：

| 服务 | 设置 | 需要 |
|---|---|---|
| **[!UICONTROL Adobe Analytics]** | **[!UICONTROL 已启用]**。 在&#x200B;**[!UICONTROL 报表包ID]**&#x200B;中，输入[步骤4](#report-suite)中的ID。 | [!DNL Adobe Analytics] |
| **[!UICONTROL Adobe Experience Platform]** | **[!UICONTROL 已启用]**。 在&#x200B;**[!UICONTROL 事件数据集]**&#x200B;中，从[步骤3](#dataset)中选择该数据集。 保持其他选项处于清除状态。 | [!DNL Customer Journey Analytics] |

![添加服务 — 使用示例报表包ID](/help/assets/guide-analytics/datastream-analytics-service.png)启用Adobe Analytics

![Adobe Experience Platform服务 — 选定事件数据集并清除其他选项](/help/assets/guide-analytics/datastream-platform-service.png)

有关过程，请参阅[将服务添加到数据流](https://experienceleague.adobe.com/en/docs/experience-platform/datastreams/configure#add-services)、[Adobe Analytics设置](https://experienceleague.adobe.com/en/docs/experience-platform/datastreams/configure#analytics)和[Adobe Experience Platform设置](https://experienceleague.adobe.com/en/docs/experience-platform/datastreams/configure#aep)。

然后从数据流详细信息中复制&#x200B;**[!UICONTROL 数据流ID]**。 在[步骤6](#enable-analytics)中需要它。

![数据流详细信息 — 已启用Adobe Analytics和Adobe Experience Platform服务，侧面板中具有示例数据流ID和映射架构](/help/assets/guide-analytics/datastream-services.png)

## 步骤6：启用Analytics并部署到暂存环境 {#enable-analytics}

**对于** [!DNL Adobe Analytics]和[!DNL Customer Journey Analytics]是必需的。

>[!IMPORTANT]
>
>保存后，数据流在“应用程序设置”中为只读。 联系LLM应用程序团队以更改它或关闭Analytics。 在保存之前检查数据流ID。 您仍然可以在数据流本身中更改报表包和数据集。

1. 打开您的应用，然后在左侧导航中选择&#x200B;**[!UICONTROL 设置]**。
2. 选择&#x200B;**[!UICONTROL Analytics]**&#x200B;选项卡。
3. 打开&#x200B;**[!UICONTROL 启用Analytics]**。
4. 在&#x200B;**[!UICONTROL 数据流ID]**&#x200B;中，粘贴[步骤5](#datastream)中的ID。
5. 在&#x200B;**[!UICONTROL 要求]**&#x200B;下，选择&#x200B;**[!UICONTROL 查看详细信息]**，并确认您的应用程序满足每个要求。

   ![应用程序设置，Analytics选项卡 — 启用Analytics已启用，空的数据流ID字段和扩展的要求列表](/help/assets/guide-analytics/analytics-settings.png)

6. 选择&#x200B;**[!UICONTROL 保存更改]**。
7. [将您的应用程序](/help/guides/deploy-your-app.md)部署到暂存环境。 此设置将在下次部署到每个环境时生效。

>[!NOTE]
>
>数据流适用于整个应用程序。 暂存环境和生产环境将事件发送到相同的数据流，并且事件不记录发送它们的环境。 您在暂存环境上的测试调用将显示在与生产流量相同的报表中。

## 步骤7：创建连接和数据视图 {#cja}

**需要：** [!DNL Customer Journey Analytics]。

### 创建连接 {#connection}

使用以下设置从[步骤3](#dataset)创建连接并添加数据集：

| 设置 | 值 |
|---|---|
| 沙盒 | 包含您的数据集的沙盒 |
| 平均每日事件数 | 与预期卷匹配的范围 |
| 数据集 | [步骤3](#dataset)中的数据集 |
| 人员 ID | **[!UICONTROL 标识映射]** |
| 使用主要身份标识命名空间 | 已选定 |
| 时间戳 | **[!UICONTROL 时间戳]** |
| 数据源类型 | **[!UICONTROL 其他]** |
| 数据源说明 | 任何描述，例如`LLM Apps events` |
| 导入新数据 | **[!UICONTROL 导入所有新数据]**&#x200B;已打开 |
| 数据集回填 | **[!UICONTROL 回填所有现有数据]**&#x200B;已启用（如果数据集已包含要分析的事件） |

>[!IMPORTANT]
>
>打开&#x200B;**[!UICONTROL 导入所有新数据]**。 此设置会创建一个持续连接，以便在Workspace中自动添加新事件。

在此人员ID中，[!DNL Customer Journey Analytics]使用每个事件的主标识作为人员，并跳过没有主标识的事件。 查看[事件如何识别人员](#identities)。

有关过程，请参阅[创建连接](https://experienceleague.adobe.com/en/docs/analytics-platform/using/cja-connections/create-connection#create-connection)、[事件数据集设置](https://experienceleague.adobe.com/en/docs/analytics-platform/using/cja-connections/create-connection#event-dataset-settings)和[将身份映射用作人员ID](https://experienceleague.adobe.com/en/docs/analytics-platform/using/cja-connections/create-connection#id-map)。

### 创建数据视图 {#data-view}

使用以下设置创建连接的数据视图：

| 设置 | 值 |
|---|---|
| 连接 | 您刚刚创建的连接 |
| 名称 | 任何名称，例如`LLM Apps` |
| 组件 | 要报告的维度和量度。 请参阅下表。 |

将这些架构字段添加为组件：

| 报告 | 架构字段 | 添加为 |
|---|---|---|
| 操作 | `agenticExperience.tools.name` | 维度 |
| 失败的操作调用 | `agenticExperience.tools.failed` | 维度 |
| 失败原因 | `agenticExperience.tools.errorReason` | 维度 |
| 平台 | `agenticExperience.agents.name` | 维度 |
| 用户意图 | `conversation.prompt.raw.text` | 维度 |
| 对话 | `conversation.conversationID` | 维度 |
| 构件标签 | `web.webInteraction.name` | 维度 |

添加文本字段（如用户意图）作为维度。 添加为量度的文本字段只能计数，按其值划分时，显示&#x200B;**无效**。

有关过程，请参阅[配置数据视图](https://experienceleague.adobe.com/en/docs/analytics-platform/using/cja-dataviews/create-dataview#configure)和[组件](https://experienceleague.adobe.com/en/docs/analytics-platform/using/cja-dataviews/create-dataview#components)。

## 步骤8：验证数据并部署到生产环境 {#verify}

**对于** [!DNL Adobe Analytics]和[!DNL Customer Journey Analytics]是必需的。

从支持的LLM平台在暂存应用程序中调用操作，并在其小部件中选择链接。 查看ChatGPT中的[测试](/help/guides/test-in-chatgpt.md)或Claude中的[测试](/help/guides/test-in-claude.md)。 然后检查您设置的每个服务：

| 服务 | 检查位置 | 期待完成的任务 |
|---|---|---|
| [!DNL Experience Platform] | **[!UICONTROL 数据集活动]**，针对来自[步骤3](#dataset)的数据集 | 新摄取的批次，没有失败的批次 |
| [!DNL Adobe Analytics] | 使用[第4](#report-suite)步中的报表包并带有自由格式表的Workspace项目 | 在&#x200B;**用户意图**&#x200B;维度中，小组件交互的&#x200B;**[!UICONTROL 页面]**&#x200B;行名为`mcp:<code identifier>`，**[!UICONTROL 自定义链接]**&#x200B;行，意图文本 |
| [!DNL Customer Journey Analytics] | 使用[步骤7](#cja)中的数据视图的Workspace项目 | 操作调用和小组件交互的&#x200B;**[!UICONTROL 事件]** |

每个服务都会延迟处理数据，因此新事件不会立即显示。

要生成报告，请参阅[在Adobe Analytics中创建项目](https://experienceleague.adobe.com/en/docs/analytics/analyze/analysis-workspace/build-workspace-project/create-projects)、[自由格式表概述](https://experienceleague.adobe.com/en/docs/analytics/analyze/analysis-workspace/visualizations/freeform-table/freeform-table)和[在Customer Journey Analytics中创建项目](https://experienceleague.adobe.com/en/docs/analytics-platform/using/cja-workspace/build-workspace-project/create-projects)。

当数据正确时，[将您的应用程序](/help/guides/deploy-your-app.md)部署到生产环境。

## 限制 {#limitations}

- **小组件交互拆分为许多人。** 每个操作调用都会获得一个新的ECID，因此[!DNL Customer Journey Analytics]会将来自不同操作调用的构件交互计为不同的人员。 查看[事件如何识别人员](#identities)。
- **没有指向您网站的链接。** 当有人浏览指向您网站的小组件链接时，网站访问未链接到对话。
- **暂存和生产共享报告。** 两个环境都会发送到相同的数据流，而事件不会记录发送它们的环境。
- **某些平台不发送标识。** 未提供用户ID的平台（如[!DNL Claude]）的操作调用在[!DNL Customer Journey Analytics]中被跳过。
- **每个操作调用一圈。** 每个操作调用均记录为其自身的对话轮次，因此触发多个操作的提示将计为多个轮次。

## 疑难解答 {#troubleshooting}

| 您所看到的内容 | 检查内容 |
|---|---|
| 数据集不接收批次 | 在将Analytics设置保存到要测试的环境后，您部署了应用程序。 数据流ID正确。 数据流具有数据集中的&#x200B;**[!UICONTROL Adobe Experience Platform]**&#x200B;服务。 |
| 批处理失败 | 该架构包括[步骤2](#schema)中的所有五个字段组。 |
| [!DNL Adobe Analytics]没有数据 | 数据流使用正确的报表包ID启用了&#x200B;**[!UICONTROL Adobe Analytics]**&#x200B;服务。 |
| **页面**&#x200B;显示`mcp:<code identifier>`，但&#x200B;**用户意图**，并且操作字段为空 | 您的处理程序存储库使用`@adobe/llm-apps-runtime` 1.2.0或更高版本，并且您在升级后进行了部署。 在开始之前[&#128279;](#before-you-begin)，请参阅。 |
| **用户意图**&#x200B;对于某些操作为空 | [步骤4](#processing-rule)中的处理规则完全按照所示使用上下文数据变量（小写形式）和正确的eVar。 该操作已打开&#x200B;**[!UICONTROL 收集用户意图]**。 该规则仅适用于保存后收集的数据。 |
| [!DNL Customer Journey Analytics]显示的事件比数据集少 | **[!UICONTROL 导入所有新数据]**&#x200B;已打开。 跳过了没有主标识的事件，例如来自不提供用户ID的平台的操作调用。 |
| 您无法更改数据流ID或关闭Analytics | 激活后，这些设置在“应用程序设置”中处于只读状态。 请与LLM应用程序团队联系以获取帮助。 要将数据路由到其他位置，请更改数据流本身的报表包或数据集。 |

## 后续内容 {#whats-next}

- [为操作配置分析](/help/guides/create-action.md#configure-analytics)
- [部署您的应用程序](/help/guides/deploy-your-app.md)
