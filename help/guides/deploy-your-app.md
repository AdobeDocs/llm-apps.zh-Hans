---
title: 部署您的应用程序
description: 了解如何使用LLM应用程序UI将Adobe LLM应用程序部署到暂存和生产环境。
source-git-commit: 1a99e2e80e50a3bcf9ce6fb910365202bf06e113
workflow-type: tm+mt
source-wordcount: '359'
ht-degree: 0%

---


# 部署您的应用程序

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps]当前在Beta中。
>
>此处显示的功能、工作流和UI不一定表示产品的最终状态。 要加入Beta，请发送电子邮件至llm-apps-beta@adobe.com 。

编写处理程序代码并将其推送到链接的存储库后，即可从[!DNL LLM Apps] UI部署应用程序。

## 开始部署

导航到“应用程序详细信息”页面。 单击右上角的&#x200B;**[!UICONTROL 部署]**&#x200B;按钮：

![应用程序详细信息 — 准备部署](/help/assets/guide-deploy/app-detail-deploy-ready.png)

这将打开部署对话框。 从下拉列表中选择目标环境：

![部署对话框 — 选择目标环境](/help/assets/guide-deploy/deploy-pipeline-dropdown.png)

单击&#x200B;**[!UICONTROL 部署]**&#x200B;启动管道。 四个步骤是：

1. **收集凭据** — 读取应用程序元数据，生成[!DNL GitHub]令牌，并从控制台API获取运行时凭据。
2. **触发生成管道** — 将所有参数发送到生成管道。
3. **克隆和生成** — 管道克隆您的存储库，从UI元数据生成`actions.json`，运行`npm install`和webpack以生成`dist/index.js`。
4. **部署到运行时** — 将捆绑包部署到应用程序的[!DNL Adobe I/O Runtime]命名空间。

启动后，管道将自动运行并显示实时进度：

![部署管道正在运行](/help/assets/guide-deploy/deploy-pipeline-deploying.png)

>[!NOTE]
>
>如果某个操作在UI中具有元数据，但在存储库中没有匹配的处理程序文件，则仍会注册该操作。 调用使用默认的存根处理程序，直到您添加真正的代码为止。

## 成功部署后

完成所有步骤后，该对话框会显示&#x200B;**部署成功**&#x200B;的确认以及已部署的URL和工件详细信息：

![部署成功](/help/assets/guide-deploy/app-detail-deploy-finish.png)

单击&#x200B;**关闭**&#x200B;以关闭对话框。 在“应用程序详细信息”页面上向下滚动到&#x200B;**[!UICONTROL 测试应用程序]**&#x200B;部分：

![测试应用程序 — 已部署的URL](/help/assets/guide-deploy/test-app-deployed.png)

每个环境（**暂存**&#x200B;和&#x200B;**生产**）都显示[!DNL Adobe I/O Runtime]上的MCP服务器URL。 这是您在注册应用程序时提供给LLM平台的URL。 单击&#x200B;**复制URL**&#x200B;以将其复制到剪贴板。

下面的&#x200B;**部署历史记录**&#x200B;部分保存了跨环境的每个部署的完整日志：

![部署历史记录](/help/assets/guide-deploy/deployment-history.png)

每一行显示目标&#x200B;**环境** （暂存或生产）、**状态** （成功或失败）以及&#x200B;**部署于**&#x200B;日期。 您可以使用此表跟踪部署的时间，并验证
最新部署成功。

