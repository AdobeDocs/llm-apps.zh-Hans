---
title: 升级现有应用程序
description: 在启用Adobe或Customer Journey Analytics之前，更新旧版Adobe Analytics LLM应用程序的运行时和EDS SDK 。
source-git-commit: 557173e8d95325b980efac4d72c06bb3d90dcd72
workflow-type: tm+mt
source-wordcount: '793'
ht-degree: 0%
---

# 升级现有应用程序 {#upgrade-existing-app}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps]当前在Beta中。
>
>此处显示的功能、工作流和UI不一定表示产品的最终状态。 要加入Beta，请发送电子邮件至llm-apps-beta@adobe.com 。

仅当您的应用程序使用1.2.0之前的`@adobe/llm-apps-runtime`或其EDS小组件使用0.1.13之前的LLM应用程序SDK时，才使用此指南。 已满足两个要求的应用可以直接进入[设置Adobe Analytics和Customer Journey Analytics](/help/guides/analytics-setup.md)。

**在本指南中：**&#x200B;了解如何更新和验证旧版应用程序的运行时和EDS SDK。 然后返回到Analytics设置指南以启用Analytics并验证收藏集。

## 开始之前 {#before-you-begin}

您需要访问处理程序存储库，对于具有EDS小组件的应用程序，您需要访问链接到您的应用程序的EDS存储库。 如果您的应用程序使用Adobe托管的基础架构，请在更新之前请求代码访问权限。 请参阅[使用Adobe托管基础结构](/help/guides/create-app.md#adobe-managed-infrastructure)。

无需创建其他应用程序。 仅更新受支持版本以下的组件。 您可以重复使用符合[Analytics设置要求](/help/guides/analytics-setup.md#setup-checklist)的现有Adobe配置。

对于具有EDS小组件的应用程序，请先发布并验证SDK更新，然后合并运行时更新并重新部署应用程序。 这两个存储库更新可以单独准备，但在测试启用了Analytics的部署之前必须都可用。

## 检查并更新LLM应用程序操作存储库 {#upgrade-runtime}

1. 打开链接到应用程序的处理程序存储库并安装其依赖项：

   ```bash
   npm install
   ```

1. 检查已安装的运行时：

   ```bash
   npm ls @adobe/llm-apps-runtime
   ```

1. 如果运行时版本低于1.2.0，请安装当前版本：

   ```bash
   npm install @adobe/llm-apps-runtime@latest
   npm ls @adobe/llm-apps-runtime
   ```

   确认安装的版本是1.2.0或更高版本。 如果您的应用程序已使用支持的更高版本，请保留该版本。

1. 确认运行时依赖项及其解析的版本满足最低要求。 应用程序自己的版本与运行时依赖关系不同。
1. 运行存储库的检查。 对于LLM应用程序样板：

   ```bash
   npm test -- --runInBand
   npm run build
   npm run lint
   ```

   在部署之前调查故障。 如果对原始版本的检查已失败，请将该基线与升级引入的任何失败分开记录。 成功的本地生成不会验证Analytics收集。

1. 提交依赖项更新及其锁定文件，然后将更新推送并合并到处理程序存储库的默认分支（通常为`main`）。 部署该分支的克隆。 仅存在于本地或未合并的功能分支上的更新不包括在部署中。

   当存在锁定文件时，部署将安装具有`npm ci`的依赖项，这将使用记录的版本。 重新部署不会自动升级运行时。 在重新部署之前，请在GitHub中的默认分支上确认升级的依赖项和已解析的版本。

## 检查并更新LLM应用程序构件存储库 {#upgrade-sdk}

对于具有EDS小组件的应用程序，请检查EDS存储库中的SDK以及服务器运行时。 更新一个存储库不会更新另一个存储库。

某些EDS存储库包含复制的SDK脚本，这些脚本不具有npm依赖关系。 浏览器直接加载提供的脚本，因此缺少包条目并不意味着缺少SDK。 使用项目的SDK更新流程刷新复制的SDK分发。 对于将SDK作为复制的脚本进行维护的项目，不需要添加永久性npm依赖项。

对于通过npm管理SDK的项目：

1. 从EDS存储库根目录中，安装当前SDK并确认其版本：

   ```bash
   npm install --save-dev @adobe/llmapps-sdk@latest
   npm ls @adobe/llmapps-sdk
   ```

   使用0.1.13或更高版本。 如果您的项目已经使用了一个受支持的更高版本，请保留该版本。

1. 验证安装是否刷新了提供的SDK分发。 安装挂接会覆盖复制的SDK脚本，因此在提交之前请查看任何本地自定义设置。 仅更新依赖关系声明不会更新EDS提供的脚本。 请参阅[安装LLM应用SDK](/help/guides/bring-your-own-eds.md#install-the-llm-apps-sdk)。
1. 运行EDS存储库的检查，如`npm run lint`，并检查现有构件是否仍呈现和响应交互。
1. 提交刷新的SDK分发以及任何依赖项和锁定文件更新。 EDS提供复制的脚本，而不是直接提供已安装的npm包。

在任一更新方法之后，将更新推送并合并到操作的&#x200B;**[!UICONTROL 脚本URL]**&#x200B;使用的EDS分支中。 验证URL是否提供更新后的SDK以及是否已成功加载其支持库。 如果URL使用您的EDS Live网站，请完成该网站的发布步骤。

## 下一步 {#next-step}

返回[设置Adobe Analytics和Customer Journey Analytics](/help/guides/analytics-setup.md)。 所有应用程序都必须启用Analytics并部署才能收集事件；仅升级运行时或SDK不会启用收集。

保存Analytics设置后，部署到暂存环境和[验证新的操作调用和小组件交互事件](/help/guides/analytics-setup.md#verify)，然后再部署到生产环境。 确认操作和构件仍然有效，并且单个链接选择可生成一个构件交互事件。

在启用了Analytics的部署之后，Analytics会开始收集新的交互。 升级不会重新构建在启用集合之前发生的交互。
