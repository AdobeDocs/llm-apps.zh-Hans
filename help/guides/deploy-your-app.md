---
title: 部署您的应用程序
description: 了解如何使用LLM应用程序UI将Adobe LLM应用程序部署到暂存和生产环境。
source-git-commit: a48b4022979d9a13b4fc3aa870c4ee6375e8bc13
workflow-type: tm+mt
source-wordcount: '412'
ht-degree: 0%
---

# 部署您的应用程序 {#deploy-your-app}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps]当前在Beta中。
>
>此处显示的功能、工作流和UI不一定表示产品的最终状态。 要加入Beta，请发送电子邮件至llm-apps-beta@adobe.com 。

审查生成的操作或将自己的处理程序代码推送到链接的存储库后，从[!DNL LLM Apps]部署应用程序。 对于生成的操作，请在部署之前查看并合并生成的工作。

**在本指南中：**&#x200B;了解如何将应用程序部署到暂存环境，检查部署结果，并复制其MCP服务器URL以进行测试。 测试成功后部署到生产环境。

## 开始部署

打开“应用程序详细信息”页面，然后选择&#x200B;**[!UICONTROL 部署]**。

选择目标环境，然后选择&#x200B;**[!UICONTROL 部署]**。

如果您的处理程序使用[应用程序变量](/help/guides/app-variables.md)，请在部署之前为目标环境配置它们。 添加、更新或删除的变量会在此部署中生效；暂存和生产具有独立的值。

![部署 — 选择目标环境](/help/assets/guide-onboarding-agent/deploy-stage.png)

部署运行四个步骤：

1. **正在准备** — 检索部署应用程序所需的配置。
2. **开始部署** — 开始后台部署过程。
3. **生成应用程序** — 安装依赖项并生成最新的存储库代码。
4. **发布** — 将应用程序发布到[!DNL Adobe I/O Runtime]。

>[!NOTE]
>
>如果某个操作在UI中具有元数据，但存储库中没有匹配的处理程序文件，则部署仍会注册该操作。 调用使用默认的存根处理程序，直到您添加真正的代码为止。

## 成功部署后

完成所有步骤后，对话框显示&#x200B;**部署成功**。

要关闭对话框，请选择&#x200B;**关闭**。 在“应用程序详细信息”页面上，滚动到&#x200B;**[!UICONTROL 测试应用程序]**：

![应用程序详细信息 — 复制MCP服务器URL](/help/assets/guide-onboarding-agent/app-mcp-url.png)

每个已部署环境都显示一个MCP服务器URL。 选择&#x200B;**[!UICONTROL 复制URL]**，然后使用它在目标LLM平台中创建插件或连接器。

**部署历史记录**&#x200B;部分显示最近10个部署：

![部署历史记录](/help/assets/guide-deploy/deployment-history.png)

每一行显示目标&#x200B;**环境** （暂存或生产）、**状态** （成功或失败）和&#x200B;**部署于**&#x200B;日期。 使用表可跟踪部署日期并验证最新部署是否成功。

## 部署配置更改

对操作元数据、应用程序页面的&#x200B;**[!UICONTROL 可见性]**&#x200B;切换、身份验证和应用程序变量所做的更改将在下次部署到该环境时生效。 保存设置本身不会更新已部署的MCP服务器。

每次都检查目标环境。 部署到暂存环境不会将相同的更改部署到生产环境。

## 下一步

- [将已部署的应用作为ChatGPT插件进行测试](/help/guides/test-in-chatgpt.md)。
- [将已部署的应用程序作为Claude连接器进行测试](/help/guides/test-in-claude.md)。
