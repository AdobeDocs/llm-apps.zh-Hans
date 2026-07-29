---
title: 从头开始创建操作
description: 定义操作元数据，实施其处理程序，连接EDS小组件，对其进行测试，然后使用Adobe LLM应用程序进行部署。
source-git-commit: 4c259a4587c0a84bb634a9a56c043dfe1cfc31fb
workflow-type: tm+mt
source-wordcount: '1141'
ht-degree: 0%

---


# 从头开始创建操作 {#create-action-from-scratch}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps]当前在Beta中。
>
>此处显示的功能、工作流和UI不一定表示产品的最终状态。 要加入Beta，请发送电子邮件至llm-apps-beta@adobe.com 。

>[!NOTE]
>
>本指南假定您基本熟悉Adobe Edge Delivery Services (EDS)。 如果您是EDS新手，请先阅读[EDS开发人员教程](https://www.aem.live/developer/tutorial)和[浏览块](https://www.aem.live/docs/exploring-blocks)以了解要点（块、`decorate`函数和EDS项目结构），然后再连接构件。

使用本指南可添加载入代理未创建的功能。 您将在[!DNL LLM Apps]中定义操作，在链接的存储库中编写其处理程序，根据需要添加构件，对其进行测试和部署。

**历程：**&#x200B;规划操作→创建其元数据→编写处理程序→连接构件→在本地测试→部署并测试插件。

对于您的第一个应用程序，从[开始使用入门代理创建您的第一个应用程序](/help/guides/create-app.md)。

## 开始之前

您需要：

- 现有的LLM应用程序。
- 链接的处理程序存储库。
- 存储库在本地克隆，并安装了依赖项。
- EDS项目（如果操作显示构件）。
- 用于生产结果的清除API或数据源。

## 规划操作

一个操作应该执行一个清除的用户任务。 在打开UI之前，定义：

- **意图** — 用户试图实现的目标。
- **描述** — LLM平台应何时选择此操作。
- **输入** — 用户需要的最少信息。
- **结果** — 处理程序返回的文本和结构化数据。
- **行为** — 操作是读取数据、更改数据还是调用外部系统。
- **小组件** — 结果是否需要可视化界面。

例如，**搜索产品**&#x200B;操作可以使用：

```text
Intent: Find products matching a category or search phrase
Inputs:
  category: optional string
  query: optional string
Result:
  content: text summary
  structuredContent: products and total count
Behavior: read-only, idempotent, open-world
Widget: product cards
```

将相关但不同的任务分开。 产品搜索和产品购买不应作为一项操作，因为它们具有不同的输入、风险和确认要求。

## 创建操作元数据

打开应用程序并选择&#x200B;**[!UICONTROL 操作]**，然后选择&#x200B;**[!UICONTROL 创建操作]**。

该编辑器包含&#x200B;**[!UICONTROL 操作]**&#x200B;和&#x200B;**[!UICONTROL 小组件元数据]**&#x200B;选项卡。

### 输入基本信息

![创建操作 — 基本信息](/help/assets/guide-create-action/action-basic-info.png)

输入：

- **[!UICONTROL 操作名称]** — 简短的任务名称，如&#x200B;*搜索产品*。
- **[!UICONTROL 描述]** — 说明何时使用操作及其返回的内容。

有用的描述是具体的：

```text
Search the product catalog by category or keyword. Returns matching
products with their names, prices, categories, and image URLs.
```

避免模糊描述，如&#x200B;*获取产品信息*。 LLM平台使用说明来选择不同的操作。

### 选择注释

注释描述该操作的行为：

- **破坏性提示** — 该操作可以删除或永久更改数据。
- **幂等（相同的参数=无额外效果）** — 重复相同的请求具有相同的效果。
- **开放世界提示** — 该操作与外部系统通信。
- **只读提示** — 该操作不会更改数据。

仅选择为true的注释。 例如，产品搜索通常为只读、幂等和开放世界。

### 添加OpenAI元数据

输入在操作运行期间和完成之后显示的短信：

```text
Invoking: Searching products...
Invoked: Products found
```

对于包含小部件的操作，添加&#x200B;**[!UICONTROL 小部件描述]**。 这与操作描述不同：

- **操作描述**&#x200B;可帮助模型决定何时调用操作。
- **构件描述**&#x200B;映射到`_meta["openai/widgetDescription"]`并汇总呈现的组件所显示的内容，从而减少重复旁白。

[!DNL LLM Apps]将此作为组件元数据应用。 不要从处理程序返回它。

### 配置可见性

- **[!UICONTROL 公开到AI模型]**&#x200B;允许模型选择操作。
- **[!UICONTROL 在应用程序表面中显示为构件]**&#x200B;显示配置的构件。

在操作仅返回文本时禁用构件可见性。

### 添加输入参数

为处理程序接受的每个值添加一个参数。 每个参数需要：

- **名称** — 处理程序收到的密钥。
- **类型** — 字符串、数字、整数或布尔值。
- **描述** — 模型应如何提取值。
- **必需** — 是否可以在没有它的情况下运行操作。

对于&#x200B;**搜索产品**：

```text
category
  Type: String
  Required: No
  Description: Product category used to narrow the catalog.

query
  Type: String
  Required: No
  Description: Product name or search phrase.
```

使用稳定的参数名称。 更改名称还需要更改处理程序及其测试。

### 配置analytics

当您希望Analytics包含导致该操作的对话摘要时，启用&#x200B;**[!UICONTROL 收集用户意图]**。

![创建操作 — 用户意图分析](/help/assets/guide-create-action/action-analytics-user-intent.png)

有关完整的字段定义，请参阅[操作和小组件字段](/help/reference/reference-docs.md)。

## 配置构件

跳过此部分以进行纯文本操作。

打开&#x200B;**[!UICONTROL 小组件元数据]**。

![创建操作 — 构件元数据](/help/assets/guide-create-action/widget-metadata.png)

配置：

- **类型** — 选择EDS。
- **构件域** — 托管构件的EDS源。
- **首选边框** — 请求主机中的边框容器。
- **脚本URL** — EDS小组件入口点。
- **小组件URL** — 为此操作发布的EDS页面。

典型URL包括：

```text
Script URL:
https://main--<repo>--<owner>.aem.live/scripts/aem-embed.js

Widget URL:
https://main--<repo>--<owner>.aem.live/<widget-page>
```

仅授予所需的浏览器权限和CSP域。

![创建操作 — 权限和CSP](/help/assets/guide-create-action/widget-permissions-csp.png)

如果EDS项目或构件页面尚不存在，请完成[自带EDS项目](/help/guides/bring-your-own-eds.md)，然后返回到操作。

## 保存操作

选择&#x200B;**[!UICONTROL 新建操作]**。 该操作将显示在带有&#x200B;**未部署**&#x200B;徽章的“操作”页面上。

此时，元数据已存在，但操作仍需要处理程序。

## 实施处理程序

克隆链接的处理程序存储库并安装其依赖项：

```bash
npm install
```

创建：

```text
actions/
└── search-products/
    └── index.js
```

文件夹名称必须与操作编辑器中显示的操作的代码标识符匹配。

有关完整的结果合同和处理程序 — 小部件关系，请参阅[自定义生成的处理程序](/help/guides/customize-handler.md)。

### 处理程序合同

导出一个异步函数：

```javascript
module.exports = async (args) => {
  return {
    content: [
      { type: 'text', text: 'Response for the LLM platform.' }
    ],
    structuredContent: {
      // Data for the widget.
    }
  };
};
```

该处理程序接收在UI中定义的参数。

### 返回`content`

`content`是LLM平台读取的文本回退：

```javascript
content: [
  { type: 'text', text: 'Found 3 matching products.' }
]
```

始终返回有用的`content`，即使操作具有小组件也是如此。

### 返回`structuredContent`

`structuredContent`是构件使用的普通对象：

```javascript
structuredContent: {
  products: [
    { id: 'P-100', name: 'Product A', price: '$20' }
  ],
  total: 1
}
```

形状必须与EDS块从`bridge.toolResult`中读取的内容匹配。

### 连接API

在服务器端处理程序中保留受保护的API访问。 从运行时环境加载配置，并使用固定的HTTPS源。

```javascript
const API_ORIGIN = process.env.PRODUCT_API_ORIGIN;
const API_TOKEN = process.env.PRODUCT_API_TOKEN;

module.exports = async ({ query = '' } = {}) => {
  const normalizedQuery = String(query).trim();
  if (!normalizedQuery || normalizedQuery.length > 200) {
    return {
      content: [{ type: 'text', text: 'Enter a valid product search.' }],
      structuredContent: { products: [], total: 0 }
    };
  }

  if (!API_ORIGIN || !API_TOKEN) {
    throw new Error('Product API configuration is unavailable.');
  }

  const origin = new URL(API_ORIGIN);
  if (origin.protocol !== 'https:') {
    throw new Error('Product API configuration must use HTTPS.');
  }

  const url = new URL('/v1/products', origin);
  url.searchParams.set('query', normalizedQuery);

  const response = await fetch(url, {
    headers: { Authorization: `Bearer ${API_TOKEN}` },
    signal: AbortSignal.timeout(8000)
  });

  if (!response.ok) {
    throw new Error('Product service request failed.');
  }

  const payload = await response.json();
  if (!payload || !Array.isArray(payload.products)
      || !payload.products.every((product) =>
        product
        && typeof product.id === 'string'
        && typeof product.name === 'string'
        && typeof product.price === 'string')) {
    throw new Error('Product service returned an unexpected response.');
  }

  const products = payload.products.map((product) => ({
    id: product.id,
    name: product.name,
    price: product.price
  }));

  return {
    content: [
      { type: 'text', text: `Found ${products.length} matching products.` }
    ],
    structuredContent: {
      products,
      total: products.length
    }
  };
};
```

请勿在源代码、操作元数据、构件JavaScript、日志或面向用户的错误中放入API凭据。

对于生产代码，请在将批准的字段映射到`structuredContent`中之前验证完整的上游响应。

## 添加处理程序测试

创建匹配测试：

```text
test/
└── actions/
    └── search-products.test.js
```

至少测试：

- 输入有效。
- 输入缺失或无效。
- 结果为空。
- API超时或失败。
- API数据的格式不正确。
- 构件应具有`structuredContent`形状。

运行：

```bash
npm test
```

有关项目布局和本地MCP测试，请参阅[本地处理程序开发和测试](/help/reference/development.md)。

## 在本地测试操作

运行：

```bash
npm run dev:local
```

如果没有本地`actions.json`，服务器将发现具有最少元数据和无输入架构验证的处理程序。

使用MCP检查器或`curl`可以：

1. 列出已注册的操作。
2. 使用代表性参数调用新操作。
3. 验证`content`和`structuredContent`。
4. 测试无效和空请求。

## 连接并测试构件

如果操作具有构件：

1. 使构件读取处理程序的`structuredContent`。
2. 使用安全DOM API（如`textContent`）呈现外部值。
3. 添加加载、清空和错误状态。
4. 在本地预览EDS页面。
5. 验证CSP、CORS和小部件URL。

查看[自带EDS项目](/help/guides/bring-your-own-eds.md)。

## 部署和测试

1. 提交并推送处理程序和小组件更改。
2. [将应用程序](/help/guides/deploy-your-app.md)部署到暂存环境。
3. [测试ChatGPT插件](/help/guides/test-in-chatgpt.md)。
4. 验证应该和不应该调用操作的提示。
5. 暂存成功后，部署到生产环境。

如果元数据不存在匹配的处理程序，则部署会使用默认存根注册操作。 添加处理程序，然后才能为用户执行该操作。
- [指南：设置小组件(EDS)](/help/guides/widgets.md)
