---
title: 自定义生成的EDS小组件
description: 了解和自定义由Adobe LLM应用程序自动创建的Edge Delivery Services构件。
source-git-commit: 0f9dd0a5b1008e183eb8ab5a4c2eae34a78923bb
workflow-type: tm+mt
source-wordcount: '1065'
ht-degree: 0%
---

# 自定义生成的构件 {#customize-generated-widget}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps]当前在Beta中。
>
>此处显示的功能、工作流和UI不一定表示产品的最终状态。 要加入Beta，请发送电子邮件至llm-apps-beta@adobe.com 。

>[!NOTE]
>
>本指南假定您基本熟悉Adobe Edge Delivery Services (EDS)。 如果您是EDS新手，请在自定义构件之前阅读[EDS开发人员教程](https://www.aem.live/developer/tutorial)和[浏览块](https://www.aem.live/docs/exploring-blocks)。 这些资源涵盖块、`decorate`函数和EDS项目结构。

成功生成后，平台会提供一个链接到[!DNL LLM Apps]中操作的EDS小组件。 当连接到支持的LLM平台时，该构件呈现用于预览的代表性数据并读取处理程序结果。 在自定义构件之前，请查看生成的设计和行为。

首先测试生成的构件。 然后自定义其数据约定、交互和可视化设计。

**在本指南中：**&#x200B;了解如何查找生成的构件，并根据操作数据和品牌设计对其进行调整。 然后，在本地预览您的更改，并在部署的应用程序中测试这些更改。

## 查找生成的构件

打开[基础结构设置](/help/guides/create-app.md)期间连接的EDS存储库。 如果您使用Adobe托管基础结构，请先请求代码访问权限，然后再继续。 每个生成的构件都是一个EDS块：

```text
blocks/
└── <action-slug>/
    ├── <action-slug>.js
    └── <action-slug>.css
```

- JavaScript文件会读取操作结果并构建界面。
- CSS文件控制布局、响应式行为和可视化设计。
- 生成的拉取请求会显示为该操作创建的确切文件。

概要文件使用连字符：代码标识符为`search_products`的操作使用诸如`blocks/search-products/`之类的块。 生成的构件页面通常为`/eds-widgets/search-products-demo`；在操作元数据中使用实际的&#x200B;**[!UICONTROL 构件URL]**。

该平台还会配置构件URL和支持的SDK文件。 您无需再创建一个EDS项目或重新输入构件URL即可自定义生成的构件。

## LLM应用程序SDK如何连接构件

`@adobe/llmapps-sdk`包将EDS小组件连接到LLM主机。 它在生成的EDS存储库中的入口点包括：

```text
scripts/
├── aem-embed.js
└── llmapps-sdk.js
```

`aem-embed.js`建立主机连接，加载EDS页面，并调用您的块：

```javascript
export default async function decorate(block, bridge) {
  // Customize the widget here.
}
```

嵌入将获取构件页面的`.plain.html`内容并导入其块。 您不在块中导入SDK。 嵌入自动提供`bridge`；正常的EDS页面加载不提供该功能。 桥允许构件：

- 使用`bridge.toolResult`读取处理程序结果。
- 使用`bridge.applyHostStyles()`应用主机样式。
- 继续与`bridge.sendMessage()`的对话。
- 使用`bridge.callTool()`调用另一个操作。
- 保持其大小与`bridge.autoResize()`同步。

本指南介绍常见的网桥方法。 查看完整API的[`@adobe/llmapps-sdk`包](https://www.npmjs.com/package/@adobe/llmapps-sdk)。

## 了解数据合同

操作处理程序返回`structuredContent`，块从`bridge.toolResult`读取它。

```javascript
// Handler result
return {
  content: [{ type: 'text', text: `Found ${records.length} products.` }],
  structuredContent: { arguments: { query: 'coffee' }, records }
};
```

```javascript
// EDS block
const PREVIEW_RESULT = {
  records: [{ id: 'P-100', name: 'Frescopa House Blend' }]
};

export default async function decorate(block, bridge) {
  const live = bridge?.isConnected && bridge.hostContext?.preview !== true;
  const result = live ? await bridge.toolResult : null;
  const data = live ? result?.structuredContent : PREVIEW_RESULT;
  const records = Array.isArray(data?.records) ? data.records : [];
  // Render records or an error state when result?.isError is true.
}
```

在此示例中，`PREVIEW_RESULT`是该块的本地示例数据对象。 当前生成的Widget使用`structuredContent.arguments`和`structuredContent.records`；检查生成的文件以确定确切的形状。 切勿将空的实时结果替换为预览记录。

`bridge.toolResult`仅解析第一个结果。 如果小组件保持已装载状态并且必须处理以后的结果，则使用`bridge.onToolResult(callback)`。

更改`structuredContent`时，同时更新处理程序和构件。 不要假定结构化数据在模型中是隐藏的；它是主机操作结果的一部分。 请参阅[为完整的返回协定自定义生成的处理程序](/help/guides/customize-handler.md)。

## 安全地呈现外部数据

将处理程序输出视为不受信任的数据。 首选使用DOM API，如`textContent`，而不是将响应值插入到`innerHTML`中。

```javascript
function createProductCard(product, bridge) {
  const card = document.createElement('article');
  card.className = 'product-card';

  const title = document.createElement('h3');
  title.textContent = String(product.name ?? 'Product');

  const button = document.createElement('button');
  button.type = 'button';
  button.textContent = 'Tell me more';
  button.disabled = !bridge?.isConnected;
  const status = document.createElement('p');
  status.setAttribute('role', 'status');
  button.addEventListener('click', async () => {
    if (bridge?.isConnected && typeof product.id === 'string') {
      try {
        await bridge.sendMessage(`Show me details for product ${product.id}`);
      } catch {
        status.textContent = 'Unable to send the request. Try again.';
      }
    }
  });

  card.append(title, button, status);
  return card;
}
```

在将URL分配到`href`或`src`之前验证URL，并且仅允许体验所需的协议。

## 使用主机桥

嵌入将桥传递给`decorate(block, bridge)`。 使用`bridge?.isConnected`保护交互式调用，以便在直接EDS预览期间也呈现块。 桥接对象可以在独立模式下存在而无需连接；在独立模式下等待`toolResult`永远不会解析。 LLM应用预览还设置`bridge.hostContext.preview`以允许块选择示例数据。

### 应用主机样式

```javascript
if (bridge?.isConnected) {
  bridge.applyHostStyles();
}
```

方法应用可用的主机CSS变量和字体样式。 应用主机样式是可选的：生成的构件可以使用自己的设计令牌。 嵌入在其影子主体上维护`.light`和`.dark`类。 当您的块也需要对主题更改做出反应时，请使用`bridge.hostContext.theme`和`bridge.onContextChange(callback)`。

### 发送跟进消息

```javascript
if (bridge?.isConnected) {
  await bridge.sendMessage('Show me similar products.');
}
```

使用`sendMessage`在交互后继续对话。

### 直接调用操作

```javascript
if (bridge?.isConnected) {
  const result = await bridge.callTool('search_products', { query: 'coffee' });
  // Re-render this widget from result.structuredContent, or show result.isError.
}
```

对于需要操作结果的显式交互，请使用`callTool`。 传递实际目标操作的代码标识符，而不是其块文件夹名称或显示名称；`search_products`就是一个示例。 主机必须支持操作调用，并且目标操作必须允许应用程序可见性。 调用操作不会自动重新渲染当前构件。 仅传递验证的值并处理被拒绝的调用，而不会公开内部详细信息。

如果目标操作的可见性需要更改，请仅在&#x200B;**[!UICONTROL 操作详细信息]**&#x200B;下编辑该操作，保存更改，然后重新部署到您正在测试的环境。

### 保持构件大小同步

```javascript
if (bridge?.isConnected) {
  bridge.autoResize(block);
}
```

若要让主机响应内容更改，请在初始渲染后调用`autoResize`。 方法会返回一个清理函数，该函数将在移除构件时停止观察。

## 预览更改

生成的块包括用于桥缺失、断开连接或标记为预览时直接预览的示例数据。 将示例数据回退与活动空和错误状态分开。

要在本地预览EDS项目，请执行以下操作：

```bash
npm install -g @adobe/aem-cli
aem up
```

打开本地服务器上操作的构件URL中的路径，例如`http://localhost:3000/eds-widgets/search-products-demo`，而不仅仅是站点根路径。 验证：

- 空、加载、成功和错误状态。
- 长文本和缺少可选字段。
- 键盘导航和可见焦点。
- 浅色和深色主题。
- 窄而宽的布局。

直接EDS预览不会测试主机握手或实时操作数据。 在支持的LLM平台上发布EDS更改并通过实时`structuredContent`进行测试。

## 发布自定义项

1. 提交并推送EDS更改。
2. 验证构件页面以及更改的脚本和样式在配置的EDS URL中是否可用。 通过EDS发布工作流发布内容更改。
3. 如果您更改了处理程序或操作元数据，请推送这些更改并将应用程序部署到暂存环境。
4. 在支持的LLM平台上测试该操作和构件。 查看ChatGPT中的[测试](/help/guides/test-in-chatgpt.md)或Claude中的[测试](/help/guides/test-in-claude.md)。
5. 测试成功后，根据需要将处理程序和元数据更改部署到生产环境。

EDS发布与应用程序部署不同。 如果Stage和Production使用相同的EDS URL，则这些URL上的小组件更改会影响这两个环境；部署到Stage不会创建孤立的小组件副本。

## 其他EDS设置

如果您自己创建了操作或者希望集成兼容的现有EDS站点，请参阅[自带EDS项目](/help/guides/bring-your-own-eds.md)。
