---
title: 基础架构设置
description: 使用由Adobe LLM应用程序管理的基础架构或您自己的已添加到Cloud Manager的存储库来创建一个LLM应用程序。
source-git-commit: 9b2ec369baba5d533088d2dd4adefcb80f5c9727
workflow-type: tm+mt
source-wordcount: '1947'
ht-degree: 1%
---

# 基础架构设置 {#create-first-app}

>[!IMPORTANT]
>
>所有AEM Sites Cloud Service客户都可以使用LLM应用程序来开发和管理自己的应用程序。 计数开始后，每个应用程序调用都将计入客户的AEM Sites内容请求。 Adobe将在计数生效之前公告计数率和开始日期。

基础架构是应用程序的技术基础：存储其代码的存储库，以及运行其操作处理程序并将其构件交付给用户的服务。

如[概述](/help/overview/overview.md#key-concepts)中所述，**操作处理程序**&#x200B;执行一项任务，如查找产品，而&#x200B;**小组件**&#x200B;在对话中将结果显示为交互式体验。

[!DNL LLM Apps]将处理程序和小组件源代码存储在两个不同的存储库中：

- **操作存储库** — 包含服务器端处理程序及其测试。
- **小组件存储库** — 包含交互式小组件的代码和样式。

例如，产品搜索处理程序可检索匹配的产品，其小组件将它们显示为产品卡。 存储库存储该代码；Adobe I/O Runtime运行已部署的处理程序，Edge Delivery Services提供构件。

安装向导会准备这些存储库，并连接应用程序所需的服务。 从以下两个选项中选择一个：

- **[!DNL LLM Apps]托管基础结构**&#x200B;是快速入门的方法。 Adobe会为您的操作处理程序和构件创建和托管存储库，因此您无需自行设置存储库。
- **您自己的存储库**&#x200B;将您应用程序的代码保留在您拥有的存储库中，已添加到[!DNL Cloud Manager]。 它们可以托管在[!DNL GitHub]、[!DNL GitHub Enterprise]、[!DNL GitLab]、[!DNL Bitbucket]或[!DNL Azure DevOps]上。 该平台会使用所需的项目文件初始化空存储库，因此您无需预先准备操作或构件代码。

**在本指南中：**&#x200B;了解如何选择基础结构选项并使用安装向导创建您的应用程序。 安装后，继续[创建操作](/help/guides/creating-actions.md)。

创建应用程序不会&#x200B;**生成**&#x200B;开始操作。 从应用程序页面单独开始生成操作。

## 打开创建应用程序向导

打开[Adobe LLM应用程序](https://experience.adobe.com/llm-apps)。 从欢迎页面中，选择&#x200B;**[!UICONTROL 开始使用]**。 如果您已经打开了应用程序，请使用应用程序选择器并选择&#x200B;**[!UICONTROL 新建应用程序]**。

**[!UICONTROL 创建LLM应用程序]**&#x200B;向导以&#x200B;**[!UICONTROL 开始使用]**&#x200B;开始。 选择如何设置技术环境。

![创建您的LLM应用程序 — 开始使用让我们处理它并使用我的Cloud Manager设置；红色的大纲标记了自己的存储库选项](/help/assets/guide-infrastructure/setup-choice.png)

| 基础架构选项 | 在向导中选择 | 它的含义 |
|---|---|---|
| **[!DNL LLM Apps]托管基础结构** | **[!UICONTROL 让我们处理它]** | Adobe可为您的应用程序创建和管理存储库及连接的服务。 可应请求访问代码。 |
| **您自己的存储库** | **[!UICONTROL 使用我的Cloud Manager设置]** | 您选择两个添加到[!DNL Cloud Manager]的存储库：一个用于操作处理程序，另一个用于小组件。 如果它们是空的，则平台会初始化它们。 |

这两个选项都支持以后自动生成操作。 这两个选项都不需要手动编写操作。

## 使用[!DNL LLM Apps]托管基础结构 {#adobe-managed-infrastructure}

使用此选项可让Adobe为您的应用程序设置和管理存储库和连接的服务。 您不需要自己创建存储库或安装[!DNL GitHub]应用。

1. 选择&#x200B;**[!UICONTROL 让我们处理它]**。
2. 选择&#x200B;**[!UICONTROL 下一步]**。
3. 完成[应用详细信息](#app-details)。
4. 选择&#x200B;**[!UICONTROL 创建应用程序]**。

在Adobe准备存储库并连接构件站点时，应用程序页面显示&#x200B;**[!UICONTROL 正在设置您的应用程序]**。 UI指示设置通常需要1-2分钟时间；操作、测试和部署在设置准备就绪之前不可用。

![应用程序设置 — Adobe正在准备应用程序基础结构](/help/assets/guide-infrastructure/provisioning.png)

如果安装程序失败，请读取错误并选择&#x200B;**[!UICONTROL 重试]**。 如果页面无法检查进度，安装程序将在后台继续；请选择&#x200B;**[!UICONTROL 再次检查]**&#x200B;以重新连接。

安装完成后，继续执行[创建操作](/help/guides/creating-actions.md)。

## 使用您自己的存储库 {#your-own-repositories}

选择&#x200B;**[!UICONTROL 使用我的Cloud Manager设置]**，然后选择&#x200B;**[!UICONTROL 下一步]**。 此路径包含四个向导步骤：

**开始→用操作基础结构设置→Widget基础结构设置→应用程序详细信息。**

[!DNL LLM Apps]使用您的Adobe帐户通过[!DNL Cloud Manager]访问您的存储库。 您未安装[!DNL GitHub]应用。

### 开始之前 {#own-repositories-prerequisites}

- 访问应用程序将所属的[!DNL Cloud Manager]程序。
- 两个存储库，一个用于操作处理程序，另一个用于小组件。 空存储库没有问题，包括仅包含自述文件的Git主机的存储库。
- 两个存储库[都作为专用存储库添加到Cloud Manager](https://experienceleague.adobe.com/en/docs/experience-manager-cloud-manager/content/managing-code/external-repositories)，所有权已验证，状态为&#x200B;**就绪**。 [!DNL Cloud Manager]必须能够向其写入；只读访问令牌不够。

仅列出您自己托管的存储库。 不支持[!DNL Cloud Manager]中由Adobe托管的存储库。

#### 您的Widget存储库是一个Edge Delivery Services站点 {#widget-repository-is-an-eds-site}

小组件从[!DNL Edge Delivery Services] (EDS)网站提供，您的小组件存储库保存该网站的代码。 确保Widget存储库属于EDS站点，或将要属于EDS站点。 有两种方法可以设置一个：

- **任何Git提供商，带您自己的Git。** 在[!DNL Cloud Manager]中创建EDS站点，并使用&#x200B;**自带Git**&#x200B;将其链接到您的存储库。 [!DNL Cloud Manager]然后将存储库的代码同步到站点。 请在启动向导之前执行此操作。 查看[自带Git](https://www.aem.live/developer/byo-git)。
- **具有AEM代码同步的[!DNL GitHub]存储库。** 自行设置EDS站点，如[Edge Delivery Services教程](https://www.aem.live/developer/tutorial)中所述。 该向导将指导您完成安装AEM代码同步并在验证存储库后检查访问权限。

构件步骤将检测适用的情况。 请参阅[设置构件基础结构](#set-up-the-widget-infrastructure)。

>[!TIP]
>
>即使贵公司已拥有EDS站点，请为您的[!DNL LLM Apps]小组件创建一个单独的EDS站点。

>[!NOTE]
>
>对于空存储库，验证会写入初始项目文件。 在选择&#x200B;**[!UICONTROL 验证存储库]**&#x200B;之前，请选择用于此应用的存储库。

### 设置操作基础结构

操作存储库包含在LLM平台调用操作时运行的服务器端处理程序和测试。

1. 在&#x200B;**[!UICONTROL Cloud Manager项目]**&#x200B;下，选择包含操作存储库的项目。
2. 在&#x200B;**[!UICONTROL Cloud Manager存储库]**&#x200B;下，选择操作存储库。 如果缺少该列表，请在[!DNL Cloud Manager]中添加它并&#x200B;**[!UICONTROL 转到Cloud Manager]**，然后刷新该列表。
3. 选择&#x200B;**[!UICONTROL 验证存储库]**。 验证使用Adobe LLM应用程序样板初始化空存储库。 现有存储库必须包含兼容的操作项目。
4. 显示&#x200B;**[!UICONTROL 存储库已验证]**&#x200B;时，选择&#x200B;**[!UICONTROL 下一步]**。

![操作基础结构设置 — 已选择Cloud Manager项目和存储库，已验证存储库](/help/assets/guide-infrastructure/action-repository.png)

平台将生成的操作代码推送到该存储库的分支。 选择&#x200B;**[!UICONTROL 标记为已审阅]**&#x200B;将合并分支。 请参阅[创建操作](/help/guides/creating-actions.md)。

### 设置构件基础设施 {#set-up-the-widget-infrastructure}

小组件是附加到操作的可选可视化响应，例如产品卡或预订表单。 Edge Delivery Services (EDS)从使用您的小组件存储库的站点为它们提供服务。

1. 在&#x200B;**[!UICONTROL 构件源]**&#x200B;下，选择&#x200B;**[!UICONTROL 手动选择构件存储库]**。

   ![构件基础设施设置 — 手动选择构件存储库的构件源](/help/assets/guide-infrastructure/widget-source.png)

2. 在&#x200B;**[!UICONTROL Cloud Manager程序]**&#x200B;下，选择包含小组件存储库的程序。 它可能与操作存储库的程序不同。
3. 在&#x200B;**[!UICONTROL Edge Delivery Services存储库]**&#x200B;下，选择小组件存储库。 请勿选择操作存储库。
4. 选择&#x200B;**[!UICONTROL 验证存储库]**。 验证使用AEM样板初始化空存储库。 现有存储库必须是兼容的EDS项目。

后续操作取决于构件存储库的托管位置。

#### 其他提供商上的Widget存储库（自带Git） {#widget-repository-byog}

对于[!DNL GitLab]、[!DNL Bitbucket]、[!DNL Azure DevOps]或[!DNL GitHub Enterprise]上的存储库，验证会找到[!DNL Cloud Manager]链接到存储库的EDS站点。 向导显示&#x200B;**[!UICONTROL Edge Delivery Services站点]**&#x200B;下的站点。

![构件基础架构设置 — Cloud Manager已验证构件存储库并将其同步到Edge Delivery Services站点](/help/assets/guide-infrastructure/widget-repository.png)

[!DNL Cloud Manager]将存储库的代码同步到该站点，因此您不需要AEM代码同步。 选择&#x200B;**[!UICONTROL 下一步]**。

如果验证报告未链接任何站点，请使用&#x200B;**自带Git**&#x200B;将存储库链接到[!DNL Cloud Manager]中的EDS站点，然后再次验证。 查看[自带Git](https://www.aem.live/developer/byo-git)。

#### GitHub上的构件存储库 {#widget-repository-github}

[!DNL GitHub]存储库使用AEM代码同步来发布其代码。 验证后，该向导显示&#x200B;**[!UICONTROL 安装AEM代码同步]**。 使用相同的Adobe帐户来访问[!DNL LLM Apps]、AEM站点访问权限和DA写入权限。

##### 设置AEM站点访问 {#aem-site-access}

**新安装：**&#x200B;选择&#x200B;**[!UICONTROL 安装AEM代码同步]**&#x200B;并授予它访问构件存储库的权限。 完成安装确认页面上的安装向导，将自己添加为站点管理员。

**现有EDS站点：**&#x200B;保留AEM Code Sync安装，并确认它涵盖所选的Widget存储库。 要求现有站点管理员在`https://tools.aem.live/tools/user-admin/index.html/?org=<org>&site=<site>`打开AEM用户管理员，并添加具有`admin`角色的Adobe帐户。 您无需重新安装“代码同步”。

将`<org>`和`<site>`替换为您的AEM组织和网站名称。 站点管理员可以确认这些值。

##### 授予DA写入权限 {#da-write-access}

对于新站点和现有站点，请要求DA组织管理员：

1. 在`https://da.live/config#/<org>/`处打开DA配置，将`<org>`替换为存储网站内容的DA组织。
2. 打开&#x200B;**权限**&#x200B;表。 如果表不存在，请选择&#x200B;**[!UICONTROL 添加表]**，将其命名为`permissions`，并添加列标题&#x200B;**路径**、**组**&#x200B;和&#x200B;**操作**。 您还可以添加一个&#x200B;**注释**&#x200B;列来说明每个规则。
3. 添加一行以授予您的Adobe帐户对网站中所有内容的写入访问权限：在&#x200B;**路径**&#x200B;中使用网站的内容路径，在&#x200B;**组**&#x200B;中使用您的Adobe帐户电子邮件，在&#x200B;**操作**&#x200B;中使用您的`write`帐户。 保存配置。

   ![DA权限表，其中显示了“组”列中的示例帐户和“操作”列中的写入权限](/help/assets/guide-infrastructure/da-write-permissions.png)

   屏幕截图使用虚构的帐户。 将`widget-author@example.com`替换为您的Adobe帐户电子邮件，并使用您网站的内容路径而不是组织范围的`/ + **`示例。 `CONFIG`行适用于配置管理员；您无需更改它即可授予内容访问权限。

AEM站点角色不授予DA写入权限。 您不需要组织范围的写入权限。

有关配置DA权限的详细信息，请参阅[权限和安全性](https://www.aem.live/docs/ew/administering/permissions)。

##### 验证AEM设置 {#validate-aem-setup}

1. 返回到[!DNL LLM Apps]并选择&#x200B;**[!UICONTROL 验证AEM设置]**。
2. 显示&#x200B;**[!UICONTROL AEM安装验证]**&#x200B;时，选择&#x200B;**[!UICONTROL 下一步]**。

如果验证失败，请按照消息检查[AEM站点访问](#aem-site-access)或[DA写入访问](#da-write-access)，然后再次验证。

>[!NOTE]
>
>此向导将&#x200B;**[!UICONTROL 使用Cloud Manager Edge Delivery Services]**&#x200B;标记为&#x200B;**[!UICONTROL 即将推出]**，不允许进行选择。 使用手动构件存储库选项。

## 输入应用程序详细信息 {#app-details}

两个基础结构选项均已完成，并显示&#x200B;**[!UICONTROL 应用详细信息]**。

![应用程序详细信息 — 输入的LLM应用程序名称、Cloud Manager存储库中的AEM程序集以及Application Insights数据区域](/help/assets/guide-infrastructure/app-details.png)

1. 输入&#x200B;**[!UICONTROL LLM应用名称]**。 使用标识品牌或体验的名称，如&#x200B;*Frescopa Coffee*。
2. 选择应用程序所属的&#x200B;**[!UICONTROL AEM程序]**。 如果您只能访问一个程序，向导会自动选择该程序。 对于您自己的存储库，项目是操作存储库的项目，不能在此处更改。
3. 在&#x200B;**[!UICONTROL 应用程序分析数据区域]**&#x200B;下，选择&#x200B;**[!UICONTROL 美国(US)]**&#x200B;或&#x200B;**[!UICONTROL 欧洲(EU)]**。
4. 选择&#x200B;**[!UICONTROL 创建应用程序]**。

>[!IMPORTANT]
>
>创建应用程序后，无法更改AEM程序和应用程序分析数据区域。 区域可确定应用程序Application Insights数据存储的位置。

如果没有可用的AEM程序，请咨询您的管理员以获取访问权限。 如果加载程序失败，请选择&#x200B;**[!UICONTROL 重试加载程序]**。

向导会在创建后打开应用程序页面。 使用[!DNL LLM Apps]托管基础结构，等待基础结构设置完成后再继续。

要稍后检查存储库，请打开应用程序的&#x200B;**[!UICONTROL 设置]**。 **[!UICONTROL 存储库]**&#x200B;部分显示每个存储库的程序和名称，对于使用自带Git的小组件存储库，显示其EDS网站和来源。 创建应用程序后，无法更改存储库。

![应用程序设置 — 包含样板和Edge Delivery Services存储库的存储库部分，包括链接的网站和来源](/help/assets/guide-infrastructure/settings-repositories.png)

之前使用Adobe LLM应用程序[!DNL GitHub]应用程序创建的应用程序将继续使用其现有存储库。

## 下一步：创建操作

您的应用程序现在具有基础架构，但尚未生成任何操作。 继续执行[创建操作](/help/guides/creating-actions.md)以分析网站或选择手动创建操作。

有关操作元数据、处理程序、小部件和已部署的MCP服务器之间的关系，请参阅[如何将应用程序连接在一起](/help/guides/app-architecture.md)。
