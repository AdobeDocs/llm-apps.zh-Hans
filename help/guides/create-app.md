---
title: 基础架构设置
description: 使用Adobe托管的基础架构创建一个Adobe LLM应用程序，或提供两个空的GitHub存储库供平台初始化。
source-git-commit: 14d94a68dabddd2f2ef2cef65c51d31645d4a958
workflow-type: tm+mt
source-wordcount: '1337'
ht-degree: 0%
---

# 基础架构设置 {#create-first-app}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps]当前在Beta中。
>
>此处显示的功能、工作流和UI不一定表示产品的最终状态。 要加入Beta，请发送电子邮件至llm-apps-beta@adobe.com 。

基础架构是应用程序的技术基础：存储其代码的存储库，以及运行其操作处理程序并将其构件交付给用户的服务。

如[概述](/help/overview/overview.md#key-concepts)中所述，**操作处理程序**&#x200B;执行一项任务，如查找产品，而&#x200B;**小组件**&#x200B;在对话中将结果显示为交互式体验。

[!DNL LLM Apps]将处理程序和小组件源代码存储在两个单独的[!DNL GitHub]存储库中：

- **操作存储库** — 包含服务器端处理程序及其测试。
- **小组件存储库** — 包含交互式小组件的代码和样式。

例如，产品搜索处理程序可检索匹配的产品，其小组件将它们显示为产品卡。 存储库存储该代码；Adobe I/O Runtime运行已部署的处理程序，Edge Delivery Services提供构件。

安装向导会准备这些存储库，并连接应用程序所需的服务。 我们建议从&#x200B;**Adobe托管基础结构**&#x200B;开始，这是最快入门的方法。 Adobe会为您的操作处理程序和构件创建和托管存储库。 您无需自己设置存储库。

或者，在您自己的帐户或组织中提供两个空的[!DNL GitHub]存储库。 平台会使用所需的项目文件将它们初始化；您无需预先准备操作或构件代码。

**在本指南中：**&#x200B;了解如何选择基础结构选项并使用安装向导创建您的应用程序。 安装后，继续[创建操作](/help/guides/creating-actions.md)。

创建应用程序不会&#x200B;**生成**&#x200B;开始操作。 从应用程序页面单独开始生成操作。

## 打开创建应用程序向导

打开[Adobe LLM应用程序](https://experience.adobe.com/llm-apps)。 从欢迎页面中，选择&#x200B;**[!UICONTROL 开始使用]**。 如果您已经打开了应用程序，请使用应用程序选择器并选择&#x200B;**[!UICONTROL 新建应用程序]**。

**[!UICONTROL 创建LLM应用程序]**&#x200B;向导以&#x200B;**[!UICONTROL 开始使用]**&#x200B;开始。 选择如何设置技术环境。

![创建您的LLM应用程序 — 在Adobe托管的基础架构和连接您自己的存储库之间选择](/help/assets/guide-infrastructure/setup-choice.png)

| 基础架构选项 | 在向导中选择 | 它的含义 |
|---|---|---|
| **Adobe托管基础结构** | **[!UICONTROL 让我们处理它]** | Adobe可为您的应用程序创建和管理存储库及连接的服务。 可应请求访问代码。 |
| **您自己的存储库** | **[!UICONTROL 我自己设置]** | 您提供两个空的GitHub存储库，并通过向导授予访问权限。 该平台初始化一个用于操作处理程序，另一个用于小组件。 |

这两个选项都支持以后自动生成操作。 这两个选项都不需要手动编写操作。

## 使用Adobe托管的基础架构 {#adobe-managed-infrastructure}

使用此选项可让Adobe为您的应用程序设置和管理存储库和连接的服务。 您不需要自己创建存储库或安装[!DNL GitHub]应用。

1. 选择&#x200B;**[!UICONTROL 让我们处理它]**。
2. 选择&#x200B;**[!UICONTROL 下一步]**。
3. 完成[应用详细信息](#app-details)。
4. 选择&#x200B;**[!UICONTROL 创建应用程序]**。

在Adobe准备存储库并连接构件站点时，应用程序页面显示&#x200B;**[!UICONTROL 正在设置您的应用程序]**。 UI指示设置通常需要1-2分钟时间；操作、测试和部署在设置准备就绪之前不可用。

![应用程序设置 — Adobe正在准备应用程序基础结构](/help/assets/guide-infrastructure/provisioning.png)

如果安装程序失败，请读取错误并选择&#x200B;**[!UICONTROL 重试]**。 如果页面无法检查进度，安装程序将在后台继续；请选择&#x200B;**[!UICONTROL 再次检查]**&#x200B;以重新连接。

安装完成后，继续执行[创建操作](/help/guides/creating-actions.md)。

## 连接您自己的存储库 {#your-own-repositories}

选择&#x200B;**[!UICONTROL 我将自行设置它]**，然后选择&#x200B;**[!UICONTROL 下一步]**。 此路径包含四个向导步骤：

**开始→用操作基础结构设置→Widget基础结构设置→应用程序详细信息。**

您需要访问所选存储库和权限才能安装或配置所需的[!DNL GitHub]应用。 对于组织拥有的存储库，管理员可能需要批准访问权限。

创建两个空存储库：一个用于操作处理程序，另一个用于小组件。 不要使用README、许可证或`.gitignore`初始化。 在向导中验证每个存储库时，平台会添加所需的项目结构。

如果您已经具有兼容的操作和EDS项目，则可以改为连接这些项目，但不需要使用现有代码。

>[!NOTE]
>
>对于空存储库，验证会写入初始项目文件。 在选择&#x200B;**[!UICONTROL 验证存储库]**&#x200B;之前，请选择用于此应用的存储库。

### 设置操作基础结构

操作存储库包含在LLM平台调用操作时运行的服务器端处理程序和测试。

1. 在&#x200B;**[!UICONTROL 操作源]**&#x200B;下，选择&#x200B;**[!UICONTROL GitHub]**。 其他代码源当前不可用。
2. 回答&#x200B;**[!UICONTROL 您是否已创建GitHub存储库？]**：
   - 选择&#x200B;**[!UICONTROL 是，我将使用已创建的存储库]**&#x200B;连接兼容的现有项目或空存储库。
   - 选择&#x200B;**[!UICONTROL 我还没有存储库]**，然后选择&#x200B;**[!UICONTROL 在GitHub上创建存储库]**，以创建空的操作存储库。 之后返回向导。
3. 将存储库连接到Adobe LLM应用程序[!DNL GitHub]应用程序：
   - 如果未连接任何组织，请选择&#x200B;**[!UICONTROL 在GitHub上安装]**。
   - 如果已连接组织，请选择&#x200B;**[!UICONTROL 连接存储库]**&#x200B;并将存储库添加到该安装。
4. 在[!DNL GitHub]上，选择&#x200B;**[!UICONTROL 仅选择存储库]**，并仅授予对此应用程序使用的存储库的访问权限。
5. 返回到向导。 在&#x200B;**[!UICONTROL 选择存储库]**&#x200B;下，选择&#x200B;**[!UICONTROL 组织]**&#x200B;和&#x200B;**[!UICONTROL 存储库]**。
6. 选择&#x200B;**[!UICONTROL 验证存储库]**。 验证使用Adobe LLM应用程序样板初始化空存储库。 现有存储库必须包含兼容的操作项目。
7. 验证成功后，选择&#x200B;**[!UICONTROL 下一步]**。

![操作基础结构设置 — 选择GitHub并连接操作存储库](/help/assets/guide-infrastructure/action-repository.png)

如果缺少组织或存储库，请在其选取器中使用&#x200B;**[!UICONTROL 连接其他组织]**&#x200B;或&#x200B;**[!UICONTROL 连接其他存储库]**。 更改[!DNL GitHub]上的访问权限后，选择&#x200B;**[!UICONTROL 刷新组织和存储库]**。

### 设置构件基础设施

小组件是附加到操作的可选可视化响应，例如产品卡或预订表单。 向导将连接[!DNL Edge Delivery Services] (EDS)存储库以托管它们。

1. 在&#x200B;**[!UICONTROL 构件源]**&#x200B;下，选择&#x200B;**[!UICONTROL 手动选择构件存储库]**。
2. 在&#x200B;**[!UICONTROL 代码源]**&#x200B;下，选择&#x200B;**[!UICONTROL GitHub]**。
3. 选择是否已具有存储库。 如果没有，请选择&#x200B;**[!UICONTROL 在GitHub上创建存储库]**，创建一个空的小组件存储库，然后返回到向导。
4. 为此存储库安装或配置Adobe LLM应用程序[!DNL GitHub]应用程序，就像您为操作存储库所做的那样。
5. 选择其&#x200B;**[!UICONTROL 组织]**&#x200B;和&#x200B;**[!UICONTROL 存储库]**。 请勿选择操作存储库。
6. 选择&#x200B;**[!UICONTROL 验证存储库]**。 验证使用AEM样板初始化空存储库。 现有存储库必须是兼容的EDS项目。
7. 选择&#x200B;**[!UICONTROL 安装AEM代码同步]**&#x200B;并授予AEM代码同步访问构件存储库的权限。
8. 在安装确认页面上，使用安装向导将自己添加为EDS站点的管理员。
9. 返回到[!DNL LLM Apps]并选择&#x200B;**[!UICONTROL 验证AEM代码同步安装]**。
10. 当显示&#x200B;**[!UICONTROL 已安装的AEM代码同步]**&#x200B;时，请选择&#x200B;**[!UICONTROL 下一步]**。

>[!NOTE]
>
>此向导将&#x200B;**[!UICONTROL 使用Cloud Manager Edge Delivery Services]**&#x200B;标记为&#x200B;**[!UICONTROL 即将推出]**，不允许进行选择。 使用手动构件存储库选项。

如果验证未检测到AEM代码同步，请确认AEM代码同步有权访问选定的构件存储库，并且您已将自己添加为站点管理员。 然后重新验证。

## 输入应用程序详细信息 {#app-details}

两个基础结构选项均已完成，并显示&#x200B;**[!UICONTROL 应用详细信息]**。

![应用程序详细信息 — 输入应用程序名称，选择AEM程序，然后选择Analytics数据区域](/help/assets/guide-infrastructure/app-details.png)

1. 输入&#x200B;**[!UICONTROL LLM应用名称]**。 使用标识品牌或体验的名称，如&#x200B;*Frescopa Coffee*。
2. 选择应用程序所属的&#x200B;**[!UICONTROL AEM程序]**。 如果您只能访问一个程序，向导会自动选择该程序。
3. 在&#x200B;**[!UICONTROL Analytics数据区域]**&#x200B;下，选择&#x200B;**[!UICONTROL 美国（美国）]**&#x200B;或&#x200B;**[!UICONTROL 欧洲（欧盟）]**。
4. 选择&#x200B;**[!UICONTROL 创建应用程序]**。

>[!IMPORTANT]
>
>创建应用程序后，无法更改AEM程序和Analytics数据区域。 Analytics区域可确定应用程序分析数据的存储位置。

如果没有可用的AEM程序，请咨询您的管理员以获取访问权限。 如果加载程序失败，请选择&#x200B;**[!UICONTROL 重试加载程序]**。

向导会在创建后打开应用程序页面。 使用Adobe托管基础架构，等待基础架构设置完成后再继续。

## 下一步：创建操作

您的应用程序现在具有基础架构，但尚未生成任何操作。 继续执行[创建操作](/help/guides/creating-actions.md)以分析网站或选择手动创建操作。

有关操作元数据、处理程序、小部件和已部署的MCP服务器之间的关系，请参阅[如何将应用程序连接在一起](/help/guides/app-architecture.md)。
