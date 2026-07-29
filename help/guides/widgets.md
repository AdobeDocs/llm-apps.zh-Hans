---
title: 自定义生成的EDS小组件
description: 了解和自定义由Adobe LLM应用程序自动创建的Edge Delivery Services构件。
source-git-commit: bb3d8a02f22a91ceeeba5999453aeb4221060f80
workflow-type: tm+mt
source-wordcount: '646'
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
>本指南假定您基本熟悉Adobe Edge Delivery Services (EDS)。 如果您是EDS新手，请先阅读[EDS开发人员教程](https://www.aem.live/developer/tutorial)和[浏览块](https://www.aem.live/docs/exploring-blocks)以了解要点（块、`decorate`函数和EDS项目结构），然后再自定义构件。

该平台为每个生成的操作创建一个EDS构件。 构件已接收操作结果，呈现示例数据，应用主机样式，并链接到[!DNL LLM Apps]中的操作。

首先测试生成的构件。 然后自定义其数据约定、交互和可视化设计。

**历程：**&#x200B;查找生成的块→调整其数据协定→安全自定义→本地预览→部署和测试。

## 查找生成的构件

打开您在创建应用程序时选择的EDS存储库。 每个生成的构件都是一个EDS块：

```text
blocks/
└── <action-name>/
    ├── <action-name>.js
    └── <action-name>.css
```

- JavaScript文件会读取操作结果并构建界面。
- CSS文件控制布局、响应式行为和可视化设计。
- 生成的拉取请求会显示为该操作创建的确切文件。

该平台还会配置构件URL和支持的SDK文件。 您无需再创建一个EDS项目或重新输入这些值即可自定义生成的构件。

## LLM应用程序SDK如何连接构件

`@adobe/llmapps-sdk`包将EDS小组件连接到LLM主机。 生成的EDS存储库包括：

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

您不在块中导入SDK。 已自动提供连接的`bridge`。 它允许构件：

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
  content: [{ type: 'text', text: `Found ${products.length} products.` }],
  structuredContent: { products, total: products.length }
};
```

```javascript
// EDS block
export default async function decorate(block, bridge) {
  const result = bridge ? await bridge.toolResult : null;
  const products = result?.structuredContent?.products ?? [];
  // Render products.
}
```

更改`structuredContent`时，同时更新处理程序和构件。 请参阅[为完整的返回协定自定义生成的处理程序](/help/guides/customize-handler.md)。

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
  button.addEventListener('click', () => {
    if (bridge && product.id) {
      bridge.sendMessage(`Show me details for product ${String(product.id)}`);
    }
  });

  card.append(title, button);
  return card;
}
```

在将URL分配到`href`或`src`之前验证URL，并且仅允许体验所需的协议。

## 使用主机桥

EDS将连接的桥接器传递给`decorate(block, bridge)`。 保护桥接调用，以便在直接EDS预览期间也呈现块。

### 应用主机样式

```javascript
if (bridge) {
  bridge.applyHostStyles();
}
```

这会应用主机排版规则和主题变量。 您的小组件CSS应同时支持浅色和深色主机主题。

### 发送跟进消息

```javascript
await bridge.sendMessage('Show me similar products.');
```

当交互操作应继续对话时，请使用`sendMessage`。

### 调用其他操作

```javascript
const result = await bridge.callTool('get-product-details', {
  id: product.id
});
```

对于需要其他操作结果的显式交互，请使用`callTool`。 仅传递验证的值并处理故障，而不公开内部详细信息。

### 保持构件大小同步

```javascript
if (bridge) {
  bridge.autoResize(block);
}
```

在初始渲染后调用`autoResize`，以便主机能够响应内容更改。

## 预览更改

生成的块应包含在`bridge`不可用时直接预览的示例数据。

要在本地预览EDS项目，请执行以下操作：

```bash
npm install -g @adobe/aem-cli
aem up
```

在`http://localhost:3000`处打开生成的构件页面。 验证：

- 空、加载、成功和错误状态。
- 长文本和缺少可选字段。
- 键盘导航和可见焦点。
- 浅色和深色主题。
- 窄而宽的布局。

然后将应用程序部署到暂存环境，并在LLM平台上使用实时`structuredContent`进行测试。

## 发布自定义项

1. 提交并推送EDS更改。
2. 如果更改了数据形状，请提交并推送匹配的处理程序更改。
3. 将应用程序部署到暂存环境。
4. 在[!DNL ChatGPT]中测试操作和小组件。
5. 将验证的版本提升至生产环境。

## 其他EDS设置

如果您未自动构建应用程序或希望集成现有的EDS站点，请参阅[自带EDS项目](/help/guides/bring-your-own-eds.md)。
