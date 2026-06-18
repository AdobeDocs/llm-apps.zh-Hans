---
title: 创建操作
description: 了解如何在LLM应用程序UI中定义操作，包括元数据、输入参数和构件配置。
source-git-commit: 1a99e2e80e50a3bcf9ce6fb910365202bf06e113
workflow-type: tm+mt
source-wordcount: '900'
ht-degree: 1%

---


# 创建操作

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps]当前在Beta中。
>
>此处显示的功能、工作流和UI不一定表示产品的最终状态。 要加入Beta，请发送电子邮件至llm-apps-beta@adobe.com 。

本指南将指导您完成在[!DNL LLM Apps] UI中定义操作的过程。 有关操作的背景及其工作方式，请参阅[核心概念](/help/overview/overview.md#actions)。

## 打开“操作”页面

导航到左侧边栏中的&#x200B;**[!UICONTROL 操作]**，或单击“应用程序详细信息”页面上的&#x200B;**转到操作**。 如果尚未执行任何操作，则页面将显示空状态。

![操作页面 — 还没有操作](/help/assets/guide-create-action/actions-empty.png)

单击&#x200B;**+创建操作**&#x200B;以打开全屏对话框。

## 操作卡

每个操作都会显示为一个信息卡，其中显示：

- 操作&#x200B;**name**&#x200B;和&#x200B;**description**
- **构件预览图像** — 从构件自动生成，显示LLM平台内的操作输出外观
- **徽章**：构件类型(**[!UICONTROL EDS]**)、部署状态（**未部署**、**部署到暂存**、**部署到生产**）、自上次部署以来修改操作时未部署&#x200B;**更改，以及参数计数**
- **可见性**&#x200B;切换 — 启用或禁用实时终结点上的操作，而不重新部署
- 右上角的&#x200B;**Review**&#x200B;链接可打开操作编辑器

![操作页面 — 操作卡](/help/assets/guide-create-action/action-card.png)

如果自上次部署以来修改了一个或多个操作，“操作”页面的顶部会显示&#x200B;**需要部署**&#x200B;横幅。 重新部署应用程序以应用更改。

## “操作”选项卡

该对话框有两个选项卡： **操作**&#x200B;和&#x200B;**[!UICONTROL 小组件元数据]**。

### 基本信息

![创建操作 — 基本信息](/help/assets/guide-create-action/action-basic-info.png)

- **操作名称**（必需） — 操作的标识符（例如，*搜索产品*）。
- **描述**（必需） — 该操作的明确说明。 LLM平台将使用此项来决定何时调用操作。 例如：*按关键字搜索产品目录。 返回与名称、类别、图像和价格匹配的产品。*
- **注释** — 描述操作行为的可选提示：

  | 注释 | 描述 |
  |-----------|-------------|
  | **破坏性提示** | 操作可修改或删除数据 |
  | **幂等** | 使用相同的参数多次调用该操作会生成相同的结果 |
  | **Open World提示** | 该操作与外部系统交互 |
  | **只读提示** | 该操作只读取数据，从不写入 |

  有关详细信息，请参阅[引用：元数据字段](/help/reference/reference-docs.md)。

### OpenAI元数据

- **调用状态文本** — 操作运行时在LLM平台中显示的消息（最多64个字符）。 示例： *正在加载产品……*
- **调用状态文本** — 操作完成后显示的消息（最多64个字符）。 示例： *产品已加载。*

### 可见性和输入参数

**可见性**&#x200B;控制操作可用位置：

- **向AI模型公开** — AI模型可以调用操作。
- **在应用程序表面中显示为构件** — 该操作将呈现可视化构件。

**输入参数**&#x200B;是LLM平台发送到您的处理程序的值。 模型会自动从用户消息中提取这些参数。 对于&#x200B;*搜索产品*，我们定义：

- **类别** （字符串，可选） — 用于缩小结果的类别筛选器（例如，产品类型或部门）。
- **查询** （字符串，可选） — 自由文本搜索词。

每个参数都有一个&#x200B;**Name**、**Type**(String， Number， Integer， Boolean)、**Description**&#x200B;和&#x200B;**Required**&#x200B;复选框。 单击&#x200B;**+添加**&#x200B;以添加更多参数。

有关更多详细信息，请参阅[引用：操作参数](/help/reference/reference-docs.md)。

### 分析

![创建操作 — analytics用户意图](/help/assets/guide-create-action/action-analytics-user-intent.png)

- **用户意图** — 启用时，将要求[!DNL ChatGPT]总结导致调用此操作的对话。 该摘要将在Analytics中收集和显示，为您提供insight有关触发操作时用户尝试完成的任务的信息。

## “小组件元数据”选项卡

此选项卡配置操作的可视响应在LLM平台中的呈现方式。 有关构件工作方式的完整说明，请参阅[指南：设置构件(EDS)](/help/guides/widgets.md)。

![创建操作 — 构件元数据](/help/assets/guide-create-action/widget-metadata.png)

### 构件信息

- **类型** — 构件技术（当前为&#x200B;**[!UICONTROL EDS]**）。
- **小组件域（沙盒源）** — 托管小组件的源。 将应用程序提交到OpenAI时需要；每个应用程序必须是唯一的。
- **首选边框** — 在边框卡中呈现构件。

### 模板URL

- **[!UICONTROL 脚本URL]** — 引导小部件的入口点，在所有操作之间共享：
  `https://main--<repo>--<owner>.aem.live/scripts/aem-embed.js`
- **Widget嵌入URL** — 此特定操作的EDS页面：
  `https://main--<repo>--<owner>.aem.live/eds-widgets/<action-name>`

### 权限

构件可以访问的硬件和浏览器API：

| 权限 | 描述 |
|-----------|-------------|
| **摄像头** | 访问设备摄像头 |
| **麦克风** | 访问设备麦克风 |
| **地理位置** | 访问用户的位置 |
| **剪贴板** | 从剪贴板读取或写入剪贴板 |

### CSP配置

![创建操作 — 权限和CSP](/help/assets/guide-create-action/widget-permissions-csp.png)

控制构件iframe可以联系的外部域。 每个外部域都必须明确列入允许列表。

| 指令 | 描述 |
|-----------|-------------|
| **资源域** | 静态资产的域 — 图像、字体、脚本、样式 |
| **连接域** | 构件可能通过`fetch`、`XHR`或`WebSocket`联系的域 |
| **帧域** | 允许嵌套iframe的源；添加条目会从OpenAI触发更严格的应用程序审查 |
| **重定向域** | `openExternal`个重定向链接的受信任目标（特定于[!DNL ChatGPT]） |
| **基本URI域** | `base-uri` CSP指令（仅MCP应用SDK，不受[!DNL ChatGPT]支持） |

单击&#x200B;**新建操作**&#x200B;以进行保存。

## 创建操作后

您的操作将作为卡片显示在“操作”页面上：

![操作页面 — 已创建操作](/help/assets/guide-create-action/actions-with-action.png)

每个卡片都显示操作名称、说明、类型徽章(**[!UICONTROL EDS]**)、部署状态（**未部署**）和参数计数。 您可以单击&#x200B;**...**&#x200B;进行编辑或删除，或单击&#x200B;**查看**&#x200B;检查配置。

![应用程序详细信息 — 未部署](/help/assets/guide-create-action/app-detail-not-deployed.png)

操作元数据已保存，但尚未部署任何代码。 要使操作正常运行，您需要：

1. **设置EDS构件** — 请参阅[指南：设置构件(EDS)](/help/guides/widgets.md)。
2. **编写处理程序** — 请参阅[指南：编写操作处理程序](/help/guides/write-action-handler.md)。
3. **[!UICONTROL 部署]** — 请参阅[指南：部署您的应用程序](/help/guides/deploy-your-app.md)。

## 后续步骤

- [指南：设置小组件(EDS)](/help/guides/widgets.md)

