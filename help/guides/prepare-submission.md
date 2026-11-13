---
title: 准备应用程序以提交ChatGPT
description: 使用Adobe LLM应用程序为您的应用程序准备提交包、准备报表和测试录制，然后在OpenAI门户中提交应用程序。
source-git-commit: faa25ef012ea10a592fcc79460aa5ca7ad537cf8
workflow-type: tm+mt
source-wordcount: '1453'
ht-degree: 0%
---

# 准备应用程序以提交ChatGPT {#prepare-submission}

>[!IMPORTANT]
>
>所有AEM Sites Cloud Service客户都可以使用LLM应用程序来开发和管理自己的应用程序。 计数开始后，每个应用程序调用都将计入客户的AEM Sites内容请求。 Adobe将在计数生效之前公告计数率和开始日期。

若要在[!DNL ChatGPT]插件目录中列出您的应用程序，请将提交包上载到OpenAI。 [!DNL LLM Apps]帮助您准备它。 它会从您的应用程序创建包ZIP，向您部署的应用程序发送测试请求，并记录结果以便您检查。 然后，在OpenAI门户中上传包并完成提交。

**在本指南中：**&#x200B;了解如何在[!DNL LLM Apps]中为您的应用程序准备提交包，以及下一步在OpenAI门户中做什么。

>[!NOTE]
>
>[!DNL LLM Apps]未将您的应用程序提交到OpenAI。 在OpenAI门户中进行开发人员和域验证、连接测试、审查和发布。 查看[上载并提交插件](https://developers.openai.com/plugins/deploy/submission)。

## 开始之前

- [将您的应用程序](/help/guides/deploy-your-app.md)部署到&#x200B;**生产**。 [!DNL LLM Apps]将其测试请求发送到应用程序的生产版本。
- 确保审核并启用您要发布的操作。
- 准备好您的出版商名称、支持页面的URL、隐私策略和服务条款。
- 准备两个图标：徽标和编辑器图标。 每个文件都必须是正方形的非动画PNG，每面64-4096像素，且不得大于1 MiB。 仅使用您有权发布的图像。
- 如果您的应用程序[对最终用户](/help/guides/authentication.md)进行身份验证，请在您的身份提供程序中为[!DNL ChatGPT]注册OAuth客户端，除非您的身份提供程序支持动态客户端注册(DCR)或客户端ID元数据文档(CIMD)。

## 打开“准备提交”

首次将应用程序部署到生产环境后，应用程序页面会在&#x200B;**[!UICONTROL 测试应用程序]**&#x200B;下显示&#x200B;**[!UICONTROL 准备提交]**&#x200B;卡。

![准备提交 — 生产准备状态和设置](/help/assets/guide-submission/submission-card.png)

选择&#x200B;**[!UICONTROL 设置]**。 稍后，信息卡上的按钮将更改为显示下一步，如&#x200B;**[!UICONTROL 查看进度]**&#x200B;或&#x200B;**[!UICONTROL 查看结果]**。

将打开&#x200B;**[!UICONTROL 准备提交]**&#x200B;对话框。 它包含三个部分：**[!UICONTROL 设置和准备]**、**[!UICONTROL 结果]**&#x200B;和&#x200B;**[!UICONTROL 运行历史记录]**。

## 添加您的提交详细信息

在&#x200B;**[!UICONTROL 中设置和准备]**：

1. 在&#x200B;**[!UICONTROL Publisher &amp; legal]**&#x200B;下，输入&#x200B;**[!UICONTROL Publisher name]**、**[!UICONTROL Support URL]**、**[!UICONTROL 隐私策略URL]**&#x200B;和&#x200B;**[!UICONTROL 服务条款URL]**。
   - 对于&#x200B;**[!UICONTROL 发布者名称]**，在OpenAI门户中输入要验证为发布者的公用名称。
   - 对于&#x200B;**[!UICONTROL 支持URL]**，请输入支持或联系页面的URL。 不接受电子邮件地址。
   - 隐私策略必须描述您的应用程序、身份验证、小组件和Analytics收集和使用的数据。
2. 在&#x200B;**[!UICONTROL 批准的图标]**&#x200B;下，选择&#x200B;**[!UICONTROL 选择徽标]**，然后选择&#x200B;**[!UICONTROL 选择编辑器图标]**，并为每个图标选择一个PNG文件。
3. 如果您的应用程序使用身份验证，请打开&#x200B;**[!UICONTROL OAuth客户端注册]**，然后选择[!DNL ChatGPT]获取OAuth客户端的方式。 仅选择您的身份提供程序支持的选项：
   - **[!UICONTROL 已提供]** — 您已为[!DNL ChatGPT]注册客户端。 输入其&#x200B;**[!UICONTROL 注册的客户端ID]**。 请勿输入您的Adobe IMS客户端ID。
   - **[!UICONTROL DCR]** - [!DNL ChatGPT]使用动态客户端注册注册客户端。
   - **[!UICONTROL CIMD]** - [!DNL ChatGPT]使用客户端ID元数据文档标识自身。

   [!DNL LLM Apps]从不要求客户端密钥。
4. 或者，打开&#x200B;**[!UICONTROL 其他提交详细信息]**，然后输入列表文本、包名称和版本、购买详细信息、托管的演示URL、受支持的国家/地区和发行说明。 如果将某个字段留空，[!DNL LLM Apps]将起草文本。 无法草稿的任何内容稍后出现在&#x200B;**[!UICONTROL 下提交]**&#x200B;之前。
5. 选择&#x200B;**[!UICONTROL 保存更改]**。

![设置和准备 — 发布者和法律详细信息、批准的图标以及保存更改](/help/assets/guide-submission/submission-setup.png)

准备仅使用您保存的详细信息和图标。

>[!TIP]
>
>OpenAI审阅者需要您应用程序的演示视频（最好记录在[!DNL ChatGPT]中），并且需要他们能够打开的URL。 [!DNL LLM Apps]中的测试录制供您自己检查，不会替换此演示。 录制演示，将其上载到审阅者可以访问的位置，并在准备包之前在&#x200B;**[!UICONTROL 托管的演示URL]**&#x200B;中输入其URL。

## 准备包

在&#x200B;**[!UICONTROL 准备您的包]**&#x200B;下，检查&#x200B;**[!UICONTROL 准备就绪]**。 **[!UICONTROL 准备就绪]**&#x200B;表示您保存的详细信息符合要求。 否则，部分将列出缺少的内容，例如所需的详细信息或成功的生产部署。

1. 阅读&#x200B;**[!UICONTROL 准备]**&#x200B;时发生的情况。 [!DNL LLM Apps]创建包并向已部署的应用程序发送测试请求。 这可能需要几分钟，并且测试请求会显示在应用程序的分析中。
2. 选择&#x200B;**[!UICONTROL 我知道这将调用我部署的应用程序以准备审阅证据。]** 复选框。
3. 选择&#x200B;**[!UICONTROL 准备包]**。

![准备包 — 准备就绪，选中确认复选框，然后准备包](/help/assets/guide-submission/submission-prepare.png)

在准备运行时，对话框显示&#x200B;**[!UICONTROL 正在准备]**。 [!DNL LLM Apps]使用您选择&#x200B;**[!UICONTROL 准备包]**&#x200B;时使用的应用和详细信息。

![正在准备 — 进度条和开始时间](/help/assets/guide-submission/submission-progress.png)

您可以关闭对话框并稍后返回。 准备在后台继续，应用程序页面上的信息卡显示&#x200B;**[!UICONTROL 正在准备包]**，直到它完成。 如果准备失败，将保留已保存的详细信息。

## 查看结果

准备完成后，打开&#x200B;**[!UICONTROL 结果]**。 部分顶部总结了结果。 **[!UICONTROL 准备工作需要引起注意]**&#x200B;意味着[!DNL LLM Apps]创建了草稿包，但发现了必须首先修复的问题，如占位符法律链接。

![结果 — 准备摘要、下载报告、下载包和下载录制](/help/assets/guide-submission/submission-results.png)

- **[!UICONTROL 包和报表]** — 选择&#x200B;**[!UICONTROL 下载包]**&#x200B;以获取提交ZIP，选择&#x200B;**[!UICONTROL 下载报表]**&#x200B;以获取准备报表。 **[!UICONTROL 下载包]**&#x200B;仅在准备生成完整包时可用。
- **[!UICONTROL 审阅录制]** — 选择&#x200B;**[!UICONTROL 下载录制]**&#x200B;并观看视频，确认您的应用程序运行正常。 一个视频可以包含多个测试用例。
- **[!UICONTROL 测试用例]** — 对于每个测试用例，检查已发送的请求及其状态。 打开&#x200B;**[!UICONTROL 预期行为]**，查看应用应该做什么。 某些测试用例会询问您的应用程序不支持的内容。 对于这些情况，应用程序应在不调用操作的情况下拒绝请求。

![测试用例 — 包含请求、预期行为和记录状态的审阅用例](/help/assets/guide-submission/submission-test-cases.png)

录制来自[!DNL LLM Apps]测试环境。 这些并不表示[!DNL ChatGPT]已验证您的应用程序或OpenAI已批准该应用程序。

**[!UICONTROL 在提交之前]**&#x200B;列出您仍需要确认或输入的详细信息，例如包版本、应用是否涉及购买、托管的演示URL、受支持的国家/地区和发行说明。 在列表下方，每个检查都显示&#x200B;**通过**、**警告**&#x200B;或&#x200B;**失败**。 展开一项检查以查看详细信息。 要修复详细信息，请选择&#x200B;**[!UICONTROL 更新详细信息]**，保存更改，然后再次准备包。

![在提交之前 — 剩余的详细信息、更新详细信息以及包检查](/help/assets/guide-submission/submission-before-submit.png)

如果在准备后更改应用或详细信息，**[!UICONTROL 结果]**&#x200B;将显示自此次运行&#x200B;]**以来更改的**[!UICONTROL &#x200B;应用或输入。 选择&#x200B;**[!UICONTROL 再次准备]**&#x200B;以从当前应用程序创建新包和详细信息。 **[!UICONTROL 运行历史记录]**&#x200B;列出了之前的准备工作。 打开一个不会更改您保存的详细信息。

## 在OpenAI门户中提交

包完成后，下载该包，然后按照OpenAI文档中的[上载并提交插件](https://developers.openai.com/plugins/deploy/submission)至：

1. 上传ZIP并修复自动检查发现的任何问题。
2. 连接MCP服务器并完成域验证。
3. 输入审阅信息，包括审阅人的登录详细信息（如果您的应用程序需要登录），然后提交应用程序以供审阅。
4. 发布批准的插件。

验证域：

1. 上传包后，复制OpenAI门户显示的挑战令牌。
2. 在[!DNL LLM Apps]中，打开应用程序的&#x200B;**[!UICONTROL 设置]** > **[!UICONTROL 提交]**。
3. 在&#x200B;**[!UICONTROL 域验证]**&#x200B;下，将该令牌粘贴到&#x200B;**[!UICONTROL OpenAI质询令牌]**&#x200B;中，然后选择&#x200B;**[!UICONTROL 保存]**。
4. [再次将您的应用程序](/help/guides/deploy-your-app.md)部署到生产环境。 令牌仅在下次部署后发布。
5. 返回到OpenAI门户并完成验证。

[!DNL LLM Apps]在您的应用程序域上的`/.well-known/openai-apps-challenge`提供令牌，因此您无需自行托管。

### 顺利查看的提示

- **检查每个操作的身份验证。** 当OpenAI门户扫描您的MCP服务器时，它会调用每个操作。 如果某个操作被标记为需要身份验证，但调用无法进行身份验证，则扫描将失败。 然后，您需要修复应用程序，准备新包，然后重新上传。
- **留出时间进行审阅。** 提交后，OpenAI会先审核您的应用程序，然后才能发布该应用程序，并通过电子邮件发送反馈。 在计划发布日期时，请将此时间考虑在内。
- **列表详细信息更改时上传新包。** 发布后，[!DNL ChatGPT]会在您重新部署时自动选取对MCP服务器所做的更改。 对列表详细信息或其他包元数据的更改需要新包。 在[!DNL LLM Apps]中再次准备它并将其上传到OpenAI门户。

## 相关

- [部署您的应用程序](/help/guides/deploy-your-app.md)
- [将LLM应用程序作为ChatGPT插件进行测试](/help/guides/test-in-chatgpt.md)
- [配置ChatGPT扩展](/help/guides/chatgpt-extensions.md)
