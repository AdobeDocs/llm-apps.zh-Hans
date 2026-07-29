---
title: 部署您的应用程序
description: 了解如何使用LLM应用程序UI将Adobe LLM应用程序部署到暂存和生产环境。
source-git-commit: bb3d8a02f22a91ceeeba5999453aeb4221060f80
workflow-type: tm+mt
source-wordcount: '322'
ht-degree: 0%

---


# 部署您的应用程序 {#deploy-your-app}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps]当前在Beta中。
>
>此处显示的功能、工作流和UI不一定表示产品的最终状态。 要加入Beta，请发送电子邮件至llm-apps-beta@adobe.com 。

编写处理程序代码并将其推送到链接的存储库后，即可从[!DNL LLM Apps] UI部署应用程序。

这是每个历程的共享步骤。 部署后，继续[测试ChatGPT插件](/help/guides/test-in-chatgpt.md)或[测试克劳德连接器](/help/guides/test-in-claude.md)。

## 开始部署

打开“应用程序详细信息”页面，然后选择&#x200B;**[!UICONTROL 部署]**。

选择目标环境，然后选择&#x200B;**[!UICONTROL 部署]**。

![部署 — 选择目标环境](/help/assets/guide-onboarding-agent/deploy-stage.png)

部署运行四个步骤：

1. **正在准备** — 检索部署应用程序所需的配置。
2. **开始部署** — 开始后台部署过程。
3. **生成应用程序** — 安装依赖项并生成最新的存储库代码。
4. **发布** — 将应用程序发布到[!DNL Adobe I/O Runtime]。

![部署 — 部署管道正在运行](/help/assets/guide-onboarding-agent/deploy-running.png)

>[!NOTE]
>
>如果某个操作在UI中具有元数据，但在存储库中没有匹配的处理程序文件，则仍会注册该操作。 调用使用默认的存根处理程序，直到您添加真正的代码为止。

## 成功部署后

完成所有步骤后，对话框显示&#x200B;**部署成功**。

![部署 — 部署成功](/help/assets/guide-onboarding-agent/deploy-successful.png)

单击&#x200B;**关闭**&#x200B;以关闭对话框。 在“应用程序详细信息”页面上向下滚动到&#x200B;**[!UICONTROL 测试应用程序]**&#x200B;部分：

![应用程序详细信息 — 复制MCP服务器URL](/help/assets/guide-onboarding-agent/app-mcp-url.png)

每个已部署环境都显示一个MCP服务器URL。 选择&#x200B;**[!UICONTROL 复制URL]**，然后使用它在目标LLM平台中创建插件。

**部署历史记录**&#x200B;部分显示最近10个部署：

![部署历史记录](/help/assets/guide-deploy/deployment-history.png)

每一行显示目标&#x200B;**环境** （暂存或生产）、**状态** （成功或失败）以及&#x200B;**部署于**&#x200B;日期。 您可以使用此表跟踪部署的时间，并验证
最新部署成功。

## 下一步

- [将已部署的应用作为ChatGPT插件进行测试](/help/guides/test-in-chatgpt.md)。
- [将已部署的应用程序作为Claude连接器进行测试](/help/guides/test-in-claude.md)。

