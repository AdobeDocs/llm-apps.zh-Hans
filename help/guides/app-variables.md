---
title: 配置应用程序变量和密钥
description: 将特定于环境的变量添加到Adobe LLM应用程序应用程序中，在操作处理程序中读取这些变量，进行部署并解决常见问题。
source-git-commit: 141d7a263a6937299b3ff52bdcc7c16197e55632
workflow-type: tm+mt
source-wordcount: '1158'
ht-degree: 1%
---

# 配置应用程序变量和密钥 {#app-variables}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps]当前在Beta中。
>
>此处显示的功能、工作流和UI不一定表示产品的最终状态。 要加入Beta，请发送电子邮件至llm-apps-beta@adobe.com 。

使用变量和密钥配置应用程序，而无需在其操作处理程序中对值进行硬编码。 例如，使用暂存中的测试服务和生产中的实时服务，设置应用程序调用的产品目录API的URL，而不更改处理程序代码。

变量包含非敏感设置。 密钥用于敏感值，如API密钥和访问令牌。

>[!IMPORTANT]
>
>当前，仅支持&#x200B;**个变量**。 计划在未来版本中提供秘密支持。 在此之前，**不存储**&#x200B;变量中的&#x200B;**密码、API密钥、访问令牌或其他敏感信息**：它们的值在设置表中可见且可复制。

**历程：**&#x200B;添加变量或密钥→在处理程序中读取它→部署→测试。

## 开始之前 {#before-you-begin}

您需要：

- **访问您应用程序的处理程序存储库**，以便您可以根据需要更新处理程序以读取变量。
- **`@adobe/llm-apps-runtime`1.1.0或更高版本**。 自2026年8月起创建的应用程序已包含该功能。

要检查版本，请在处理程序存储库中运行以下命令：

```bash
npm ls @adobe/llm-apps-runtime
```

如果版本低于1.1.0，请升级该版本，然后提交并推送`package.json`和`package-lock.json`：

```bash
npm install @adobe/llm-apps-runtime@latest
```

## 管理变量 {#manage-variables}

每个环境&#x200B;**[!UICONTROL Stage]**&#x200B;或&#x200B;**[!UICONTROL Production]**&#x200B;都有自己的变量，因此在添加、更新或删除变量之前请检查&#x200B;**[!UICONTROL Workspace]**。 更改将在下次将应用程序部署到该环境时生效。

### 添加变量 {#add-variable-to-app}

本指南使用值为`Good day`的名为`GREETING_PREFIX`的变量作为非敏感示例，覆盖处理程序的默认问候语`Hello`。 添加变量&#x200B;**不会**&#x200B;自动更改操作：处理程序&#x200B;**必须**&#x200B;读取它。

#### 步骤1：在UI中添加变量 {#add-a-variable}

1. 打开您的应用，然后在左侧导航中选择&#x200B;**[!UICONTROL 设置]**。
2. 打开&#x200B;**[!UICONTROL 变量和密钥]**&#x200B;选项卡。



3. 在&#x200B;**[!UICONTROL Workspace]**&#x200B;中，选择&#x200B;**[!UICONTROL 暂存]**&#x200B;或&#x200B;**[!UICONTROL 生产]**。
4. 选择&#x200B;**[!UICONTROL 添加]**。

   ![变量和密钥 — 使用“添加”按钮清空阶段工作区](/help/assets/guide-app-variables/variables-empty.png)

5. 在对话框中，输入：
   - **[!UICONTROL 名称]**： `GREETING_PREFIX`。
   - **[!UICONTROL 类型]**：保持选择&#x200B;**[!UICONTROL 变量]**。
   - **[!UICONTROL 值]**： `Good day`。

   ![添加变量或密码 — GREETING_PREFIX设置为正常日期](/help/assets/guide-app-variables/add-variable-dialog.png)

6. 选择要保存的&#x200B;**[!UICONTROL 添加]**。

该变量出现在表中，具有其&#x200B;**[!UICONTROL 名称]**、**[!UICONTROL 类型]**、**[!UICONTROL 值]**&#x200B;和&#x200B;**[!UICONTROL 上次更新时间]**&#x200B;日期。 如果需要复制值，请使用值旁边的复制控件。

![变量和密钥 — 在暂存工作区中保存了GREETING_PREFIX](/help/assets/guide-app-variables/variable-added.png)



>[!IMPORTANT]
>
>保存会将变量添加到选定环境的配置中。 它&#x200B;**不会**&#x200B;更新已部署的应用程序，直到您再次部署为止。

#### 步骤2：读取处理程序中的变量 {#use-a-variable}

在处理程序存储库中，打开`actions/<action-name>/index.js`。 确保处理程序接受第二个参数`extra`，并使用`getVariable`读取变量：

```javascript
const { getVariable } = require('@adobe/llm-apps-runtime');

module.exports = async ({ name = 'there' } = {}, extra) => {
  const prefix = getVariable(extra, 'GREETING_PREFIX') || 'Hello';

  return {
    content: [{ type: 'text', text: `${prefix}, ${name}!` }]
  };
};
```

使用值`Good day`，将`name`设置为`Ada`的调用返回`Good day, Ada!`，而不是默认的`Hello, Ada!`。 如果您稍后将值更改为`Howdy`，则问候语会在您重新部署后发生更改，而无需进行其他处理程序编辑。

代码&#x200B;**中的名称必须**&#x200B;与UI中的名称完全匹配。 如果未设置变量，`getVariable`将返回`undefined`。 确定您的操作是可以使用合适的默认值（如示例所示），还是应该返回一个明确的错误，因为它需要设置。

>[!NOTE]
>
>变量仅在应用程序的操作处理程序中可用；它们&#x200B;**不**&#x200B;自动可用于小组件。

当处理程序更改准备就绪时，提交这些更改并将其推送到应用程序的处理程序存储库。 下一个部署使用最新的推送代码。 要了解有关编辑处理程序的详细信息，请参阅[自定义生成的处理程序](/help/guides/customize-handler.md)。

#### 步骤3：部署和测试 {#deploy-and-verify}

1. [将您的应用程序](/help/guides/deploy-your-app.md)部署到您在&#x200B;**[!UICONTROL Workspace]**&#x200B;中选择的相同环境。
2. 在将`name`设置为`Ada`的情况下，从支持的LLM平台中调用操作。 请参阅[测试ChatGPT插件](/help/guides/test-in-chatgpt.md)或[测试Claude连接器](/help/guides/test-in-claude.md)。
3. 确认响应为`Good day, Ada!`。 这可以确认处理程序读取您配置的变量并覆盖其默认问候语。

要配置其他环境，请在&#x200B;**[!UICONTROL Workspace]**&#x200B;中选择该环境，使用相应的值重复安装，然后在其中部署和验证。


>[!NOTE]
>
>暂存和生产具有&#x200B;**独立的配置**。 一个环境中的更改&#x200B;**不会**&#x200B;影响另一个环境。 如有必要，在两个环境中使用相同的名称，并为每个环境选择适当的值。

>[!TIP]
>
>要在部署之前进行本地测试，请参阅[本地处理程序开发和测试](/help/reference/development.md)，并将变量传递到本地服务器：
>
>`node server/local.js --param 'LLMA_VARIABLE_NAMES=["GREETING_PREFIX"]' --param GREETING_PREFIX=Howdy`

### 更新变量 {#update-or-delete}

1. 在变量行中选择编辑控件。
2. 查看变量的&#x200B;**[!UICONTROL 当前值]**&#x200B;并输入&#x200B;**[!UICONTROL 新值]**。

   ![更新GREETING_PREFIX — 将值从“Good day”更改为“Howdy”](/help/assets/guide-app-variables/update-variable-dialog.png)

3. 选择&#x200B;**[!UICONTROL 更新]**。
4. 再次将应用程序部署到同一环境，并验证更改后的行为。

更新只会更改值。 无法重命名变量&#x200B;****；若要使用其他名称，请删除现有变量并添加新变量，然后更新处理程序以读取新名称。

### 删除变量 {#delete-a-variable}

1. 检查是否有任何操作仍需要变量。 如有必要，请先更新并推送处理程序&#x200B;**1**。
2. 选择变量行上的删除控件。 要删除多个条目，请选中它们的复选框，然后选择&#x200B;**[!UICONTROL 删除]**。
3. 在确认对话框中查看名称，然后选择&#x200B;**[!UICONTROL 删除]**。

   ![删除GREETING_PREFIX — 确认永久删除](/help/assets/guide-app-variables/delete-variable-dialog.png)

4. 再次将应用程序部署到同一环境。

>[!IMPORTANT]
>
>删除&#x200B;**CANNOT**&#x200B;无法撤消，因此请确保选择了正确的变量。 已部署的应用程序会保留其现有配置，直到下一次部署为止。 部署后，处理程序&#x200B;**不再接收**&#x200B;已删除的变量，因此需要它的操作可能会失败。

## 规则和限制 {#good-to-know}

| 项目 | 规则 |
|------|------|
| 名称 | 最多64个字符：大写字母、数字和下划线，而不是以数字开头。 **每个应用程序和环境必须**&#x200B;是唯一的，并且必须与处理程序匹配。 |
| 保留名称 | 以`LLMA_`和`MCP_SERVER_URL`开头的名称。 |
| 值 | **必填**，最多500个字符。 前导空格和尾随空格将被删除。 |
| 限制 | 每个应用程序和环境50个变量。 |
| 可见性 | 变量值是可见且可复制的。 计划的密码支持会隐藏已保存的值。 |
| 更改 | 在下一个部署到选定环境时生效。 |

## 疑难解答 {#verify-configuration}

| 您所看到的内容 | 要做什么 |
|--------------|------------|
| **[!UICONTROL 添加]**&#x200B;已禁用，并且页面显示&#x200B;**[!UICONTROL Workspace限制已达到]** | 删除不再需要的变量。 |
| *仅使用大写字母、数字和下划线* | 重命名，例如`API_BASE_URL`。 |
| *此名称由平台保留* | 选择不以`LLMA_`开头且不是`MCP_SERVER_URL`的名称。 |
| *具有此名称的变量已存在* | 请更新现有变量。 |
| *此变量刚在其他地方修改过* | 刷新页面并重试。 |
| *无法加载变量* | 重新加载页面。 如果此问题仍然存在，请检查您是否具有应用程序的访问权限。 |
| 操作不使用新值 | 检查您是否已在将变量保存到您测试的同一环境&#x200B;**之后部署**，是否已推送处理程序更改，以及名称是否与&#x200B;**完全匹配**。 **[!UICONTROL 上次更新时间]**&#x200B;显示的是保存该值的时间，而不是部署该值的时间。 |
| `getVariable is not a function` | 您的应用程序使用的运行时低于1.1.0。 按照[中的说明进行升级，在开始](#before-you-begin)之前，请先进行升级，然后再进行部署。 |
| 删除变量后，操作失败 | 将变量添加回来，或者更新处理程序使其不再需要，然后部署。 |

## 后续内容 {#whats-next}

- [自定义生成的处理程序](/help/guides/customize-handler.md)
- [部署您的应用程序](/help/guides/deploy-your-app.md)
