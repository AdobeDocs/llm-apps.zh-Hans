---
title: 自动创建您的第一个LLM应用程序
description: 从您的网站创建一个Adobe LLM应用程序，查看生成的操作，将其部署，然后在支持的LLM平台（如ChatGPT）中进行测试。
source-git-commit: f91bb73a39cc5aacf44979ee55dd0ab5f69d4c81
workflow-type: tm+mt
source-wordcount: '1272'
ht-degree: 0%

---


# 自动创建您的第一个应用程序 {#create-first-app}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps]当前在Beta中。
>
>此处显示的功能、工作流和UI不一定表示产品的最终状态。 要加入Beta，请发送电子邮件至llm-apps-beta@adobe.com 。

该平台可将您的网站变成功能齐全的应用程序。 它建议操作、编写处理程序代码和测试、创建EDS小部件，并将生成的文件发送到您拥有的两个[!DNL GitHub]存储库。

生成大约需要15分钟。 在本教程结束时，您将拥有一个已部署的应用程序，您可以在支持的LLM平台（如[!DNL ChatGPT]）中测试该应用程序。

**历程：**&#x200B;确认→创建两个存储库→创建应用程序→查看生成的操作→部署到暂存环境→测试插件→连接生产系统的要求。

## 开始之前

在开始本教程之前，请先完成所有[LLM应用要求](/help/overview/overview.md#requirements)。

本教程将为[Frescopa Coffee](https://frescopa.coffee/)创建一个LLM应用。

## 创建两个空存储库

该平台需要两个空存储库。 在同一个[!DNL GitHub]帐户或组织下创建两者：

- **处理程序存储库** — 存储操作处理程序和测试。 例如，`my-brand-llm-app`。
- **EDS存储库** — 存储生成的构件块和样式。 例如，`my-brand-llm-app-eds`。

前往每个存储库的[github.com/new](https://github.com/new)。

不要使用README、`.gitignore`或许可证初始化任一存储库。 该平台准备了所需的项目结构。

>[!TIP]
>
>使用可标识应用程序和每个存储库用途的存储库名称。 这使得在应用程序创建对话框中更容易识别它们。

## 启动应用程序

1. 打开[Adobe LLM应用程序](https://experience.adobe.com/#/@llmapps/llm-apps/)并选择&#x200B;**[!UICONTROL 创建应用程序]**。
2. 输入&#x200B;**[!UICONTROL LLM应用程序名称]**&#x200B;和可选描述。
3. 选择&#x200B;**[!UICONTROL Analytics区域]**。

   >[!IMPORTANT]
   >
   >创建应用程序后，无法更改Analytics区域。

4. 在&#x200B;**[!UICONTROL 构建我的应用程序]**&#x200B;中，选择&#x200B;**[!UICONTROL 自动构建我的应用程序]**。
5. 在&#x200B;**[!UICONTROL 您的网站]**&#x200B;中，输入包括`https://`协议的网站URL。 该平台会分析此站点，以确定有用的操作和具有代表性的示例结果。

![创建LLM应用程序 — 启用应用程序详细信息并构建我的应用程序](/help/assets/guide-onboarding-agent/app-details-onboarding.png)

## 授予[!DNL LLM Apps]对存储库的访问权限

Adobe LLM Apps [!DNL GitHub]应用程序为您选择的存储库授予[!DNL LLM Apps]访问权限。

>[!NOTE]
>
>连接[!DNL GitHub]组织是一次性设置。 如果该组织已出现在对话框中，请使用&#x200B;**[!UICONTROL 在GitHub]**&#x200B;上管理存储库，而不是再次连接它。

### 连接的组织

如果在创建存储库之前已安装Adobe LLM应用程序[!DNL GitHub]应用程序：

1. 选择连接的组织。
2. 选择&#x200B;**[!UICONTROL 在GitHub上管理存储库]**。
3. 将两个存储库添加到现有的[!DNL GitHub]应用程序安装中。
4. 返回到[!DNL LLM Apps]并刷新存储库列表。

### 仅首次连接

如果组织未显示在对话框中：

1. 选择&#x200B;**[!UICONTROL 连接GitHub组织]**。
2. 安装Adobe LLM应用程序[!DNL GitHub]应用程序。
3. 选择&#x200B;**[!UICONTROL 仅选择存储库]**&#x200B;并选择两个存储库。
4. 返回创建LLM应用程序对话框。

如果无法安装或更新[!DNL GitHub]应用程序，请咨询组织管理员。

## 选择存储库

1. 在&#x200B;**[!UICONTROL 样板存储库]**&#x200B;下，选择组织和空处理程序存储库。
2. 在&#x200B;**[!UICONTROL EDS存储库]**&#x200B;下，选择组织和空的EDS存储库。

   ![构建我的应用程序 — 选择GitHub组织、样板存储库和EDS存储库](/help/assets/guide-onboarding-agent/repos-selected.png)

3. 根据&#x200B;**[!UICONTROL 条款和条件]**，选中&#x200B;**[!UICONTROL 我接受Adobe Developer条款]**。
4. 选择&#x200B;**[!UICONTROL 创建应用程序]**。

## 完成EDS设置

当选定的EDS存储库为空时，[!DNL LLM Apps]会使用AEM样板将其初始化。 然后，该对话框会要求您在尝试再次创建应用程序之前安装AEM代码同步。

1. 在EDS存储库下面的消息中，选择&#x200B;**[!UICONTROL 安装AEM代码同步]**。
2. 在[!DNL GitHub]上，安装AEM Code Sync并授予它访问EDS存储库的权限。

   在&#x200B;**AEM Code Sync已注册**&#x200B;确认页面的&#x200B;**[!UICONTROL 站点用户]**&#x200B;下，选择&#x200B;**[!UICONTROL +添加用户]**&#x200B;并添加用于使用&#x200B;**[!UICONTROL 管理员]**&#x200B;角色登录到[!DNL LLM Apps]的电子邮件地址。 然后选择页面底部的&#x200B;**[!UICONTROL 完成设置]**。

   ![AEM代码同步已注册 — 请将您自己添加为具有管理员角色的网站用户](/help/assets/guide-onboarding-agent/aem-code-sync-site-users-admin.png)

3. 返回创建LLM应用程序对话框。

![创建LLM应用程序 — 初始化了空的EDS存储库，需要AEM代码同步](/help/assets/guide-onboarding-agent/install-aem-code-sync.png)

您必须是EDS站点的管理员。 如果对话框报告您不是管理员：

![创建LLM应用程序 — 需要EDS管理员访问权限](/help/assets/guide-onboarding-agent/eds-admin-required.png)

1. 选择&#x200B;**[!UICONTROL 打开AEM Live Admin]**。
2. 通过单击&#x200B;**[!UICONTROL +添加用户]**&#x200B;按钮，将您添加为EDS站点的管理员。

   ![创建LLM应用程序 — 将您添加为EDS管理员](/help/assets/guide-onboarding-agent/add-eds-admin.png)

3. 返回[!DNL LLM Apps]，刷新EDS存储库，然后再次选择&#x200B;**[!UICONTROL 创建应用程序]**。

在存储库和管理员检查通过后，[!DNL LLM Apps]将创建应用程序并开始生成操作。

## 等待操作生成

从左侧转到&#x200B;**[!UICONTROL 操作]**&#x200B;页面。 “操作”页面显示&#x200B;**在代理分析网站并生成应用程序时发现对话体验的操作**。 生成通常大约需要15分钟。 您可以离开此页面，稍后再返回。

![操作 — 生成推荐](/help/assets/guide-onboarding-agent/actions-generating.png)

在生成期间，[!DNL LLM Apps]：

1. 分析网站并确定有用的客户意图。
2. 创建操作元数据，包括说明和输入参数。
3. 为处理程序存储库中的每个操作生成一个处理程序并进行测试。
4. 为EDS存储库中的每个操作生成一个EDS小组件。
5. 准备操作以供您审阅。

生成的处理程序最初使用从网站派生的示例数据。 它们演示了完整的体验，但未连接到您的生产系统。

## 查看生成的操作

生成完成后，“操作”页面将显示生成的操作和构件预览。 每个操作都有一个&#x200B;**[!UICONTROL AI生成的操作，需要审核]**&#x200B;徽章。

![操作 — 生成的操作已准备好审查](/help/assets/guide-onboarding-agent/actions-ready-for-review.png)

对于每个操作：

1. 选择&#x200B;**[!UICONTROL 审阅]**。
2. 查看名称、描述、参数、注释、生成的处理程序和小组件。
3. 选择&#x200B;**[!UICONTROL 标记为已审阅]**。 这将合并生成的拉取请求。
4. 返回到“操作”页面，然后对其余操作重复此操作。

![生成的操作 — 已准备好标记为已审阅](/help/assets/guide-onboarding-agent/generated-action-review.png)

审核所有操作后，选择&#x200B;**[!UICONTROL 转到应用程序页面]**。

![操作 — 已审阅所有生成的操作](/help/assets/guide-onboarding-agent/actions-reviewed.png)

>[!NOTE]
>
>生成的代码是您拥有的起点。 您可以在查看后更改操作元数据、处理程序、测试、构件JavaScript和小组件样式。

## 部署应用程序

1. 返回到“应用程序详细信息”页面。
2. 选择&#x200B;**[!UICONTROL 部署]**。
3. 选择&#x200B;**[!UICONTROL Stage]**&#x200B;作为目标环境。
4. 选择&#x200B;**[!UICONTROL 部署]**。

![部署 — 选择暂存环境](/help/assets/guide-onboarding-agent/deploy-stage.png)

[!DNL LLM Apps]正在准备、生成和发布应用程序，请稍候。

![部署 — 部署管道正在运行](/help/assets/guide-onboarding-agent/deploy-running.png)

![部署 — 成功的暂存部署](/help/assets/guide-onboarding-agent/deploy-successful.png)

部署后，**[!UICONTROL 测试应用程序]**&#x200B;部分显示暂存MCP服务器URL。 选择&#x200B;**[!UICONTROL 复制URL]**。

![应用程序详细信息 — 复制暂存MCP服务器URL](/help/assets/guide-onboarding-agent/app-mcp-url.png)

## 在[!DNL ChatGPT]中测试

在ChatGPT[&#128279;](/help/guides/test-in-chatgpt.md)中执行测试，以使用暂存MCP服务器URL创建插件。

提出与生成的操作之一匹配的问题。 验证：

- [!DNL ChatGPT]选择所需的操作。
- 构件将渲染并包含预期的示例数据。
- 构件控件产生预期的跟进行为。
- 文本响应准确地总结了结果。

![ChatGPT — 生成的LLM应用程序插件响应](/help/assets/guide-onboarding-agent/chatgpt-generated-app.png)

您现在拥有功能齐全的端到端应用程序。

## 使应用程序做好生产准备

生成的应用程序使用示例数据。 在与客户一起使用之前：

1. **连接您的系统** — [自定义每个生成的处理程序](/help/guides/customize-handler.md)，将样本数据替换为对API或数据源的调用。
2. **保护凭据** — 将API URL和凭据存储在托管运行时配置中，而不是在源代码或小组件JavaScript中。
3. **验证数据** — 验证操作参数和API响应，添加请求超时并返回安全错误消息。
4. **更新构件** — 使每个构件与其处理程序的`structuredContent`保持一致，然后应用您的品牌推广和可访问性要求。 请参阅[自定义生成的构件](/help/guides/widgets.md)。
5. **测试处理程序** — 涵盖有效输入、无效输入、空结果、API失败以及构件预期的数据形状。
6. **在暂存中验证** — 通过[!DNL ChatGPT]插件重新部署和测试每个操作。
7. **部署到生产** — 暂存测试成功后，部署到生产，并使用生产MCP服务器URL创建或更新插件。

若要添加平台未创建的功能，请参阅[从头开始创建操作](/help/guides/create-action.md)。

