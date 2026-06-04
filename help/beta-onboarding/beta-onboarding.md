---
title: Beta入门
description: Adobe LLM应用程序作为Beta计划参与者快速入门。
source-git-commit: f144ccfc0ede6c556ccf4d99173f91d372add6f7
workflow-type: tm+mt
source-wordcount: '1545'
ht-degree: 0%

---


>[!IMPORTANT]
>
>**免责声明：**&#x200B;这是[!DNL LLM Apps]的测试版本。 此处显示的功能、工作流和UI不一定表示应用程序或产品的最终状态。

>[!NOTE]
>
>在开始之前，请确保满足所有[先决条件](/help/beta-onboarding/prerequisites.md)。

作为Beta计划参与者，您将收到一封电子邮件，其中包含两个zip存档和一个应用程序配置参考。 请按照以下步骤让您的应用程序上线。

## 开始之前

在深入研究这些步骤之前，请熟悉本指南中使用的关键概念。 这样可节省您的时间，并帮助所有内容点击到位。

**LLM应用程序** — 用户在[!DNL ChatGPT]或其他LLM平台中与之交互的品牌助理。

**操作** — 您的应用程序提供的功能。 例如，“查找分销商”或“浏览产品”。 当用户提出相关问题时，LLM会调用每个操作。

**操作处理程序** — 调用操作时运行的代码。 它可以调用您的API、获取实时数据或返回静态数据。 Adobe提供的示例处理程序返回硬编码数据，因此您可以在连接真正的后端之前端到端地验证设置。

**小组件** — 向用户显示的可视响应 — 卡片、轮播、表格或任何在LLM文本回复旁边呈现的自定义UI。

**应用程序配置引用** — Adobe提供的文件，可告知您在设置应用程序时为每个操作确切输入的内容。


## 步骤1：将提供的存档推送到[!DNL GitHub]

Adobe通过电子邮件提供两个zip存档：

- **应用程序代码** (`<project-name>.zip`) — 在[!DNL Adobe I/O Runtime]上运行并为应用程序逻辑提供支持的操作处理程序。 您将按原样部署这些内容，以使应用程序端到端地工作，然后稍后更新它们以连接您的实际后端。
- **EDS项目** (`<project-name>-eds.zip`) — 小组件的前端代码。 Adobe为您预建了这些功能；这是您的代码库，可根据您的品牌来拥有、自定义和设置样式。

在[!DNL GitHub]上创建&#x200B;**两个新的空存储库**（每个存档一个），然后解压缩每个存档并将其推送。 我们建议在相应的zip文件（`<project-name>`代表应用程序代码，`<project-name>-eds`代表EDS项目）之后命名每个存储库。

`<your-github-org>`是指您的个人[!DNL GitHub]用户名或[!DNL GitHub]组织 — 由哪个帐户拥有存储库。

**应用程序代码存储库** — 解压缩存档，初始化本地Git存储库，并将其推送到[!DNL GitHub]：

```bash
# Unzip and enter the folder
unzip <project-name>.zip
cd <project-name>

# Initialize and push
git init
git add .
git commit -m "first commit"
git branch -M main
git remote add origin git@github.com:<your-github-org>/<your-repo>.git
git push -u origin main
```

**EDS存储库** — 对EDS存档重复相同的步骤，指向第二个存储库：

```bash
unzip <project-name>-eds.zip
cd <project-name>-eds

git init
git add .
git commit -m "first commit"
git branch -M main
git remote add origin git@github.com:<your-github-org>/<your-eds-repo>.git
git push -u origin main
```

## 步骤2：创建LLM应用程序

导航到[experience.adobe.com/llm-apps/](https://experience.adobe.com/llm-apps/)并单击&#x200B;**[!UICONTROL 创建LLM应用程序]**。

![应用页面 — 尚未创建任何应用](/help/assets/guide-create-app/first-load.png)

使用应用程序配置参考中&#x200B;**[!UICONTROL 应用程序详细信息]**&#x200B;部分的值填写&#x200B;**[!UICONTROL 应用程序详细信息]**：

- **[!UICONTROL LLM应用名称]**
- **[!UICONTROL LLM应用程序说明]**
- **[!UICONTROL 您的网站]**

![创建应用程序对话框](/help/assets/guide-create-app/app-details-1.png)

在&#x200B;**[!UICONTROL Analytics数据区域]**&#x200B;下，选择将存储分析数据的区域。 创建应用程序后，无法更改此&#x200B;**&#x200B;**。

>[!IMPORTANT]
>
>创建应用程序后，无法更改Analytics数据区域。

![Analytics数据区域下拉列表](/help/assets/guide-create-app/app-details-analytics-dropdown.png)

在&#x200B;**存储库**&#x200B;下，选择[!DNL GitHub]组织和您之前推送的&#x200B;**应用程序代码存储库**。

>[!NOTE]
>
>如果这是您首次设置应用程序，则您的[!DNL GitHub]组织将不会出现在列表中。 单击&#x200B;**[!UICONTROL 连接其他GitHub组织]**&#x200B;以链接您的组织并授予对存储库的访问权限。

![创建应用程序对话框 — 已链接的存储库](/help/assets/guide-create-app/app-details-repo-linked.png)

取消选中&#x200B;**[!UICONTROL 根据您的网站自动建议操作]** — 您将手动配置操作。

接受&#x200B;**[!UICONTROL Adobe Developer条款]**，然后单击&#x200B;**[!UICONTROL 创建应用程序]**。

![正在创建应用程序 — 正在加载屏幕](/help/assets/guide-create-app/app-loading.png)

![应用程序详细信息页面](/help/assets/guide-create-app/app-detail-top.png)


## 步骤3：启用构件

在此步骤中，您将设置Adobe提供的EDS项目，并通过[!DNL DA.live] — Adobe的创作和CDN层发布该项目。 每个发布的文档都成为在调用操作时向用户显示的构件。

### 步骤3.1：将EDS存储库连接到[!DNL DA.live]

1. 转到[github.com/apps/aem-code-sync](https://github.com/apps/aem-code-sync)。 如果尚未安装应用程序，请单击&#x200B;**[!UICONTROL 安装]**。 如果已安装，请单击&#x200B;**[!UICONTROL 配置]**&#x200B;并将`<your-eds-repo>`添加到其可以访问的存储库列表中。
2. 安装后，您将登陆到&#x200B;**[!DNL AEM Code Sync]注册的**&#x200B;确认页面。 在“创建内容”→**下面的**&#x200B;下，单击[!DNL DA.live]链接。
3. 在&#x200B;**演示内容**&#x200B;屏幕上，选择&#x200B;**无**&#x200B;并单击&#x200B;**制作一些精彩的内容**。
4. 您将被带入网站的[!DNL DA.live]作者视图。

### 步骤3.2：为每个操作创建一个[!DNL DA.live]文档

在[!DNL DA.live]中，您需要为每个操作&#x200B;**创建一个文档**。 发布后，每个文档都成为在调用该操作时向用户显示的构件。

对于每个操作：

1. 在[!DNL DA.live]中，在网站的根目录下创建一个新文档，并按照应用程序配置引用中指定的方式将其命名（请参阅&#x200B;**[!DNL DA.live]文档**&#x200B;部分）。
2. 在文档中，使用左侧边栏并单击&#x200B;**[!UICONTROL 块]**&#x200B;插入新块。
3. 将块标头设置为应用程序配置引用中指定的块名称（请参阅&#x200B;**[!DNL DA.live]文档**&#x200B;部分）。
4. 使用&#x200B;**[!UICONTROL 发布]**&#x200B;按钮（顶部工具栏中的纸飞机图标）发布文档。

发布后，可在`https://main--<your-eds-repo>--<your-github-org>.aem.live/<document-name>`访问每个文档。 在步骤4中配置每个操作时，您将在&#x200B;**[!UICONTROL 小组件URL]**&#x200B;字段中输入此URL。


### 步骤3.3：为EDS站点配置CORS标头

要允许LLM平台跨域加载小组件，您需要向EDS网站添加`Access-Control-Allow-Origin`标头。

转到[tools.aem.live/tools/headers-edit/index.html](https://tools.aem.live/tools/headers-edit/index.html)上的&#x200B;**HTTP标头编辑器**。

1. 输入您的&#x200B;**组织** (`<your-github-org>`)和&#x200B;**站点** （您的EDS存储库名称），然后单击&#x200B;**[!UICONTROL 提取]**。 系统将提示您验证并授权访问您的网站。
2. 在路径`/**`下，单击&#x200B;**[!UICONTROL 添加标头]**。
3. 将标头名称设置为`Access-Control-Allow-Origin`，将值设置为`*`。
4. 单击&#x200B;**[!UICONTROL 保存]**。

有关[!DNL AEM Edge Delivery Services]中自定义HTTP标头的完整文档，请参阅[aem.live/docs/custom-headers](https://www.aem.live/docs/custom-headers)。

保存标头后，会触发代码同步以将更改传播到所有文件：

```bash
curl -X POST "https://admin.hlx.page/code/<your-github-org>/<your-eds-repo>/main/*"
```


## 步骤4：添加操作

在[LLM应用程序UI](https://experience.adobe.com/llm-apps/)中，打开应用程序并在左侧边栏中导航到&#x200B;**[!UICONTROL 操作]**。 单击&#x200B;**+**&#x200B;以创建新操作。 对应用程序配置引用中描述的每个操作重复执行上述操作（请参阅&#x200B;**操作1**、**操作2**、**操作3**&#x200B;部分）。

![操作页面 — 还没有操作](/help/assets/guide-create-action/actions-empty.png)

### “操作”选项卡

- **操作名称**&#x200B;和&#x200B;**描述** — 由LLM平台用来决定何时调用操作。 使用应用程序配置参考中&#x200B;**操作选项卡**&#x200B;部分的确切值。
- **输入参数** — 每个参数的名称、类型和描述。 使用应用程序配置引用中&#x200B;**操作选项卡**&#x200B;部分的值。

![创建操作 — 基本信息](/help/assets/guide-create-action/action-basic-info.png)

### “小组件元数据”选项卡

- **类型** — 选择&#x200B;**[!UICONTROL EDS]**。

展开&#x200B;**[!UICONTROL CSP配置]**&#x200B;并填写：

- **[!UICONTROL CSP — 连接域]** — 使用应用程序配置引用中&#x200B;**小组件元数据选项卡**&#x200B;部分的值。
- **[!UICONTROL CSP — 资源域]** — 使用应用程序配置引用中&#x200B;**小组件元数据选项卡**&#x200B;部分的值。

![创建操作 — 构件元数据](/help/assets/guide-create-action/widget-metadata.png)

![创建操作 — 权限和CSP](/help/assets/guide-create-action/widget-permissions-csp.png)

### Widget Builder选项卡

在&#x200B;**[!UICONTROL 构件源]**&#x200B;下，选择&#x200B;**[!UICONTROL 使用现有构件]**，然后填写：

- **[!UICONTROL 脚本URL]** — 使用应用程序配置引用中&#x200B;**小组件元数据选项卡**&#x200B;部分的值。
- **[!UICONTROL 构件URL]** — 使用应用程序配置引用中&#x200B;**构件元数据选项卡**&#x200B;部分的值。

单击&#x200B;**[!UICONTROL 创建操作]**。 该操作将作为卡片显示在“操作”页面上，具有&#x200B;**[!UICONTROL EDS]**&#x200B;徽章和参数计数。

![操作页面 — 已创建操作](/help/assets/guide-create-action/actions-with-action.png)


## 步骤5：部署

配置所有操作后，转到“应用程序详细信息”页面，然后单击右上角的&#x200B;**[!UICONTROL 部署]**。

![应用程序详细信息 — 准备部署](/help/assets/guide-deploy/app-detail-deploy-ready.png)

选择目标环境并单击&#x200B;**[!UICONTROL 部署]**。 管道将运行四个步骤：准备凭据、开始部署、从存储库构建应用程序以及发布到[!DNL Adobe I/O Runtime]。

![部署管道正在运行](/help/assets/guide-deploy/deploy-pipeline-deploying.png)

完成后，滚动到“应用程序详细信息”页面上的&#x200B;**[!UICONTROL 测试应用程序]**&#x200B;部分，并复制&#x200B;**[!UICONTROL MCP服务器URL]** — 您将需要它在[!DNL ChatGPT]中注册应用程序。

![部署成功](/help/assets/guide-deploy/app-detail-deploy-finish.png)

![测试应用程序 — 已部署的URL](/help/assets/guide-deploy/test-app-deployed.png)


## 步骤6：将应用程序添加到[!DNL ChatGPT]

将自定义应用添加到[!DNL ChatGPT]需要&#x200B;**Pro**、**Business**&#x200B;或&#x200B;**Enterprise**&#x200B;订阅。 免费和加号计划不支持自定义MCP应用程序。

1. 在[!DNL ChatGPT]中，单击您的个人资料头像，然后转到&#x200B;**[!UICONTROL 设置]**。

   ![ChatGPT — “设置”菜单](/help/assets/guide-test-chatgpt/chatgpt-settings-menu.png)

2. 在侧栏中选择&#x200B;**[!UICONTROL 应用程序]**，单击&#x200B;**[!UICONTROL 高级设置]**，然后启用&#x200B;**[!UICONTROL 开发人员模式]**。

   ![ChatGPT — 已启用开发人员模式](/help/assets/guide-test-chatgpt/chatgpt-developer-mode.png)

3. 转到[!UICONTROL 应用&#x200B;]&#x200B;**→的**&#x200B;[!UICONTROL &#x200B;设置]，然后单击&#x200B;**[!UICONTROL 创建应用]**。

   ![ChatGPT — 创建应用程序对话框](/help/assets/guide-test-chatgpt/chatgpt-create-app.png)

4. 粘贴从[!DNL LLM Apps]复制的&#x200B;**[!UICONTROL MCP服务器URL]**，将&#x200B;**[!UICONTROL 身份验证]**&#x200B;设置为&#x200B;*无身份验证*，选中确认复选框，然后单击&#x200B;**创建**。

您的应用程序显示在&#x200B;**[!UICONTROL 启用的应用程序]**&#x200B;下，带有&#x200B;**[!UICONTROL DEV]**&#x200B;徽章。

![ChatGPT — 已启用应用程序](/help/assets/guide-test-chatgpt/chatgpt-app-enabled.png)

开始新对话，使用&#x200B;**+**&#x200B;按钮或键入&#x200B;**@**&#x200B;后跟应用名称附加应用，然后询问与配置的操作之一匹配的问题。

![ChatGPT — 从菜单中选择应用程序](/help/assets/guide-test-chatgpt/chatgpt-select-app.png)

![ChatGPT — 操作结果](/help/assets/guide-test-chatgpt/chatgpt-response.png)

## 后续内容

您部署的示例应用程序使用硬编码数据。 要将其转换为生产就绪体验，请执行以下操作：

- **连接API** — 更新应用程序代码存储库中的操作处理程序以调用真正的API、数据库或服务。 每个处理程序都位于`actions/<action-name>/index.js`。
- **查看和优化小组件** — 打开您的EDS项目，调整块样式和布局以匹配您的品牌，验证小组件是否正确呈现在实时数据中。
- **重新部署** — 更新处理程序和小组件后，请将更改推送到[!DNL GitHub]，然后单击[!DNL LLM Apps] UI中的&#x200B;**[!UICONTROL 部署]**&#x200B;以发布新版本。
- **提交以供发布** — 如果您对体验感到满意，请通过[!DNL ChatGPT]插件或连接器发布过程提交您的应用程序以供审阅。 Adobe不控制此过程 — 有关提交要求和时间线，请参阅LLM平台的文档。
