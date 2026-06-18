---
title: 设置构件(EDS)
description: 了解如何设置Edge Delivery Services构件项目并实施块合同，以在LLM平台内呈现可视化响应。
source-git-commit: 1a99e2e80e50a3bcf9ce6fb910365202bf06e113
workflow-type: tm+mt
source-wordcount: '1226'
ht-degree: 1%

---


# 设置构件(EDS)

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps]当前在Beta中。
>
>此处显示的功能、工作流和UI不一定表示产品的最终状态。 要加入Beta，请发送电子邮件至llm-apps-beta@adobe.com 。

本指南介绍如何端到端地构建EDS小组件：从在[!DNL LLM Apps] UI中配置您的操作，到设置您的EDS项目，再到编写在LLM平台中呈现您的数据的块代码。 有关高级概述，请参阅[核心概念](/help/overview/overview.md#widgets-eds)。

## [!DNL LLM Apps]SDK

所有内容均以[`@adobe/llmapps-sdk`](https://www.npmjs.com/package/@adobe/llmapps-sdk) npm包开头。 SDK是JavaScript库，为构件和LLM主机之间的双向通信渠道提供支持。

SDK还提供了`aem-embed.js` — 特定于EDS的入口点，用于将SDK插入标准EDS块管道。 当您`npm install @adobe/llmapps-sdk`时，安装后脚本会自动将两个文件复制到您的项目中：

```
scripts/
└── llm-apps/
    ├── aem-embed.js     ← EDS widget entry point, ships with the SDK
    └── llmapps-sdk.js   ← core SDK, loaded internally by aem-embed.js
```

在EDS项目中，**您从不直接在块代码中使用SDK。** `aem-embed.js`创建和管理SDK连接，并将完全连接的`LLMApp`实例作为`decorate(block, bridge)`中的`bridge`参数传递给您的块。 完整的SDK API在`bridge`上可用 — 无需导入。

如果您正在构建不带EDS **的小部件**（标准捆绑程序或TypeScript项目），则可以直接使用SDK：

```javascript
import { LLMApp } from '@adobe/llmapps-sdk';

const app = new LLMApp({ appInfo: { name: 'MyWidget', version: '1.0.0' } });
await app.connect();

const { structuredContent } = await app.toolResult;
```

## 这一切如何融为一体

当AI调用您的操作并且处理程序返回`structuredContent`时，LLM平台在对话中呈现一个交互式构件。 三点因素共同促成了这一工作：

**1} UI** — 在创建操作时，在“小组件”元数据选项卡中输入&#x200B;**[!UICONTROL 脚本URL]**&#x200B;和&#x200B;**[!UICONTROL 小组件URL]**。 [!DNL LLM Apps]脚本URL指向`aem-embed.js` — SDK附带的文件，该文件位于`scripts/llm-apps/aem-embed.js`的EDS存储库中。 这会告知LLM平台在调用操作时要加载哪个脚本。

**`aem-embed.js`** — LLM平台将此脚本加载到沙盒小组件表面。 `aem-embed.js`是一个自定义HTML元素(`<aem-embed>`)，它充当Widget的EDS感知入口点。 它使用SDK与LLM主机执行握手，禁止正常的EDS页面管道（无页眉/页脚），从小组件URL中提取EDS页面内容，运行EDS块管道，并向每个块的`decorate()`函数提供实时`bridge`对象。

**您的块代码** — 您编写了一个标准EDS块以导出`decorate(block, bridge)`函数。 `bridge`是连接的SDK实例，它为您提供操作的结构化结果，并允许您将消息发送回对话。

## 添加到现有EDS项目

如果您已有EDS项目，则只有两个步骤才能开始编写块。

1. 安装`@adobe/llmapps-sdk`。 安装后脚本将`aem-embed.js`和`llmapps-sdk.js`复制到`scripts/llm-apps/`中：

   ```bash
   npm install @adobe/llmapps-sdk
   ```

2. 配置CORS标头，以便LLM平台可以跨源加载您的构件页面和脚本 — 请参阅下面的[配置CORS标头](#configure-cors-headers)。

然后，按照[`decorate(block, bridge)`合同](#the-decorateblock-bridge-contract)创建块，创作构件页面，并在“创建操作”对话框中输入URL。

## 设置新的EDS项目

### 创建存储库

1. 基于[AEM样板](https://github.com/adobe/aem-boilerplate)模板创建新的[!DNL GitHub]存储库。
2. 将[AEM代码同步GitHub应用程序](https://github.com/apps/aem-code-sync)添加到存储库。
3. 安装AEM CLI以进行本地开发： `npm install -g @adobe/aem-cli`。
4. 安装`@adobe/llmapps-sdk`。 安装后脚本将`aem-embed.js`和`llmapps-sdk.js`复制到`scripts/llm-apps/`中：

   ```bash
   npm install @adobe/llmapps-sdk
   ```

有关EDS项目的完整指南，请参阅[AEM开发人员教程](https://www.aem.live/developer/tutorial)和[项目剖析](https://www.aem.live/developer/anatomy-of-a-project)。

设置后，您的EDS站点将位于：

- **预览：** `https://main--<repo>--<owner>.aem.page/`
- **上线：** `https://main--<repo>--<owner>.aem.live/`

### 存储库结构

```
my-brand-eds/
├── scripts/
│   ├── llm-apps/
│   │   ├── aem-embed.js           # Widget entry point — copied by post-install
│   │   └── llmapps-sdk.js         # Core SDK — copied by post-install
│   ├── aem.js                     # AEM core library
│   └── scripts.js                 # Site-level decoration and loading
├── blocks/
│   └── search-products/           # One folder per widget block
│       ├── search-products.js
│       └── search-products.css
├── styles/
│   └── styles.css
├── head.html
└── package.json
```

### 配置CORS标头

EDS构件页面由LLM平台加载到沙盒构件表面中。 EDS网站必须返回正确的`access-control-allow-origin`标头，以便主机可以跨域获取您的小组件内容。

标头是通过`admin.hlx.page`上的AEM管理面板使用[配置服务](https://aem.live/docs/config-service-setup)配置的。 为构件页面和SDK脚本所在的路径添加自定义响应标头：

```json
{
  "/<your-widget-pages-path>/**": [
    { "key": "access-control-allow-origin", "value": "*" }
  ],
  "/scripts/**": [
    { "key": "access-control-allow-origin", "value": "*" }
  ]
}
```

>[!NOTE]
>
>对于`.aem.live`域上的公共小部件内容，使用`*`作为原始值是可接受的。 如果您的网站包含受保护的内容，请将来源限制为特定域。

### 创建构件页面

在EDS创作工具中创建页面，并将块添加到该页面中。 页面URL将成为您在操作中配置的&#x200B;**[!UICONTROL 小组件URL]** — 这是操作和块之间的唯一连接。 您的块和操作名称之间没有命名要求。

![EDS创作 — 块已添加到构件页面](/help/assets/guide-widget/aem-author.png)

### 在创建操作对话框中输入URL

设置EDS存储库后，在创建操作时转到&#x200B;**小组件元数据→模板URL**：

**[!UICONTROL 脚本URL]** — 指向EDS存储库中的`aem-embed.js`。 对于同一EDS项目中的每个操作，此值都相同：

```
https://main--<repo>--<owner>.aem.live/scripts/llm-apps/aem-embed.js
```

**[!UICONTROL 构件URL]** — 您为此构件创建的EDS页面的URL。 每个操作的唯一值：

```
https://main--<repo>--<owner>.aem.live/<path-to-your-widget-page>
```

LLM平台从脚本URL加载`aem-embed.js`。 然后`aem-embed.js`从构件URL提取`.plain.html`以获取您的块内容。

## 数据流

从您的处理程序到渲染小部件的完整路径：

1. **操作处理程序**&#x200B;返回`structuredContent`：

```javascript
// actions/search-products/index.js
return {
  structuredContent: {
    products: [
      { id: 'COF-001', name: 'Single Origin Ethiopian Coffee', price: '$18', rating: 4.7 },
      { id: 'COF-002', name: 'Colombia Huila Natural', price: '$22', rating: 4.5 },
    ],
    total: 2,
    category: 'coffee'
  }
};
```

1. **LLM平台**&#x200B;打开一个小组件表面，并从脚本URL加载`aem-embed.js`。

1. **`aem-embed.js`**&#x200B;通过SDK连接到主机，从小组件URL获取`.plain.html`，运行EDS块管道，并在您的块上调用`decorate(block, bridge)`。

1. **您的块**&#x200B;从`bridge.toolResult`读取数据并呈现UI。

1. **用户交互**&#x200B;触发`bridge.sendMessage(...)`或`bridge.callTool(...)`，向对话发送跟进。

## `decorate(block, bridge)`合同

每个EDS小组件块都应该导出默认`decorate`函数。 这是用第二个参数扩展的标准EDS块签名 — 连接的`bridge`，它是一个具有完整API可用的[`LLMApp`](https://www.npmjs.com/package/@adobe/llmapps-sdk) SDK实例：

```javascript
export default async function decorate(block, bridge) {
  // ...
}
```

仅在LLM平台小组件表面内运行时，`bridge`才存在。 始终保护您的桥接调用，这样当直接在浏览器或本地开发服务器中预览时，您的块也会呈现。

### 从操作结果渲染数据

`bridge.toolResult`是一个Promise，它解析为您的处理程序返回的完整结果，包括`structuredContent`。

```javascript
const SAMPLE_PRODUCTS = [
  { id: 'COF-001', name: 'Single Origin Ethiopian Coffee', price: '$18', rating: 4.7 },
];

export default async function decorate(block, bridge) {
  let products = SAMPLE_PRODUCTS;

  if (bridge) {
    const result = await bridge.toolResult;
    products = result?.structuredContent?.products ?? [];
  }

  block.innerHTML = products.map(p => `
    <div class="product-card">
      <h3>${p.name}</h3>
      <p class="price">${p.price}</p>
      <button data-id="${p.id}">Tell me more</button>
    </div>
  `).join('');
}
```

### 应用主机主题

在`decorate`的早期调用`bridge.applyHostStyles()`以将主机的CSS变量和字体（浅色/深色主题、排版规则）插入小组件。 这可使您的小组件与周围的LLM平台UI在视觉上保持一致。

```javascript
export default async function decorate(block, bridge) {
  if (bridge) {
    bridge.applyHostStyles();
  }
  // ...
}
```

要在运行时对主题更改做出反应（例如，当用户在浅色模式和深色模式之间切换时），请执行以下操作：

```javascript
if (bridge) {
  bridge.onContextChange(ctx => {
    block.dataset.theme = ctx.theme; // 'light' | 'dark'
  });
}
```

### 发送跟进消息

`bridge.sendMessage(text)`在对话中插入用户消息。 这是小组件触发进一步人工智能交互的主要方式，例如，当用户单击产品卡询问详细信息时。

```javascript
block.querySelectorAll('button[data-id]').forEach(btn => {
  btn.addEventListener('click', () => {
    bridge.sendMessage(`Show me details for product ${btn.dataset.id}`);
  });
});
```

### 直接调用另一个操作

`bridge.callTool(name, args)`从小组件中调用另一个操作，而不处理用户消息。 用于按需加载相关数据。

```javascript
btn.addEventListener('click', async () => {
  const result = await bridge.callTool('get-product-details', { id: product.id });
  renderDetails(result.structuredContent);
});
```

### 自动调整构件大小

LLM平台会根据您报告的内容调整构件大小。 使用`bridge.autoResize(element)`在内容更改时保持小组件高度同步 — 它在内部使用`ResizeObserver`。 在初始渲染后调用它：

```javascript
export default async function decorate(block, bridge) {
  // ... render content ...

  if (bridge) {
    bridge.autoResize(block);
  }
}
```

或者手动报告固定大小：

```javascript
bridge.reportSize(block.offsetWidth, block.offsetHeight);
```

### 预览模式和本地开发

直接在浏览器中或本地开发服务器上预览EDS页面时，`bridge`为`undefined`。 使用上面显示的示例数据回退模式，以便您的块无需实时处理程序即可立即呈现。

启动本地开发服务器：

```bash
npm install -g @adobe/aem-cli
aem up
```

这将打开`http://localhost:3000`，您可以在其中导航到小组件页面，并查看使用示例数据呈现的块。 对块JS和CSS所做的更改会立即反映出来。

## 后续步骤

- [指南：编写操作处理程序](/help/guides/write-action-handler.md)

