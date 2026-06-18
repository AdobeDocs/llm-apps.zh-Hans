---
title: 编写操作处理程序
description: 了解如何为Adobe LLM应用程序编写操作处理程序，包括处理程序合同、structuredContent和工作示例。
source-git-commit: 1a99e2e80e50a3bcf9ce6fb910365202bf06e113
workflow-type: tm+mt
source-wordcount: '719'
ht-degree: 0%

---


# 编写操作处理程序

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps]当前在Beta中。
>
>此处显示的功能、工作流和UI不一定表示产品的最终状态。 要加入Beta，请发送电子邮件至llm-apps-beta@adobe.com 。

在[!DNL Adobe LLM Apps] UI中创建操作后，元数据存储在[!DNL LLM Apps] API中，但尚未包含代码。 本指南将指导您编写在LLM平台（如[!DNL ChatGPT]或Claude）调用您的操作时运行的处理程序函数。

有关项目布局、本地开发和测试的详细信息，请参阅[开发](/help/reference/development.md)。

## 开发人员合同

您只编写处理程序。 所有其他功能（操作名称、描述、输入架构、注释、构件可见性、权限、CSP）都位于[!DNL LLM Apps] UI中，并在部署时自动交付到运行时。 您永远不会在存储库中手动编辑元数据，也永远不会在代码中注册工具。

| 关注 | 它所在的位置 |
|---------|----------------|
| 元数据（名称、描述、架构、构件设置） | [!DNL LLM Apps] UI — 保存在API中 |
| 处理程序代码（运行的函数） | 您的[!DNL GitHub]存储库 — `actions/<name>/index.js` |
| `actions.json` （元数据快照） | 由部署管道编写；从UI下载以供本地开发 |

## 快速入门

链接存储库需要项目结构才能编写处理程序。 克隆&#x200B;**[Adobe LLM应用程序模板](https://github.com/Adobe-AIFoundations/llm-apps-boilerplate)**&#x200B;以空起点开始。

将内容推送到您在应用程序创建期间链接的存储库（例如，`your-org/your-repo`）。

代码就绪后，运行：

```bash
npm install
```

这将安装所有依赖项，包括[`@adobe/llm-apps-runtime`](https://www.npmjs.com/package/@adobe/llm-apps-runtime) — 处理MCP协议通信、操作发现和请求路由的运行时。 您不直接与运行时交互；`entry.js`在生成时使用该运行库。

>[!TIP]
>
>如果您使用[Claude代码](https://claude.ai/code)或[Cursor](https://cursor.com)，则样板将包含位于`.claude/skills/llm-apps-action-author/`的现成的Claude技能。 它可以构建新操作、生成测试文件、验证处理程序形状并引导您完成处理程序契约 — 所有这些都来自您的编辑器。 若要使用它，请让Claude *“添加一个名为search-products的操作”*，它会自动遵循正确的项目约定。

## 处理程序合同

处理程序是位于`actions/<name>/index.js`的单个文件，用于导出一个异步函数：

```javascript
module.exports = async (args) => {
  return {
    content: [{ type: 'text', text: 'response for the LLM' }],
    structuredContent: { /* data for the widget */ }
  }
}
```

函数将操作的输入参数作为纯对象进行接收 — 这些参数是在“创建操作”对话框中定义的参数。 服务器在调用处理程序之前根据输入架构验证它们。

### `content` （必需）

发送到LLM和纯文本主机的内容部分的数组。 LLM平台将读取这一点来制定其响应。

```javascript
content: [
  { type: 'text', text: 'Found 5 products matching category "bagged-coffee".' }
]
```

始终返回`content` — 它是任何主机的通用回退。

### `structuredContent`

发送到小部件的纯JavaScript对象。 此数据具有&#x200B;**零令牌成本** — 它由EDS小组件块使用，以呈现丰富的UI，如产品轮播或映射。

```javascript
structuredContent: {
  products: [
    { name: 'Product A', category: 'bagged-coffee', imageUrl: '...' },
    { name: 'Product B', category: 'bagged-coffee', imageUrl: '...' }
  ],
  total: 2,
  category: 'bagged-coffee'
}
```

结构由您决定 — 它必须通过`bridge.toolResult`匹配您的EDS小组件块的预期内容。

>[!IMPORTANT]
>
>`structuredContent`必须是纯对象，而不是空数组。

### `_meta`（可选）

与结果一起发送的其他元数据。 `openai/widgetDescription`键可告知LLM平台如何呈现构件：

```javascript
_meta: {
  'openai/widgetDescription': 'The widget displays a scrollable product carousel. '
    + 'Do NOT repeat the product list. Instead, highlight one or two recommendations.'
}
```

## 示例：搜索产品处理程序

以下是`search-products`处理程序示例。 它接受可选的`category`过滤器和自由文本`query`，搜索产品目录，并返回LLM的文本摘要和构件轮播的结构化数据。

>[!NOTE]
>
>为了简单起见，此示例使用硬编码的product阵列。 在实际的应用程序中，您通常会调用自己的产品API或数据库来动态获取结果。

```javascript
// actions/search-products/index.js

const PRODUCTS = [
  {
    name: 'Product A',
    description: 'A short description of Product A.',
    category: 'bagged-coffee',
    sub_category: 'dark-roast',
    image_url: 'https://www.example.com/products/product-a/hero.jpg',
    url: 'https://www.example.com/products/product-a',
    productId: 'PROD-001',
    rating: 4.7,
    reviewCount: 58
  },
  // ... more products
];

const WIDGET_DESCRIPTION = 'The widget displays a scrollable product carousel '
  + 'with images, star ratings, and review counts. Do NOT repeat the product list.';

module.exports = async ({ category = '', query = '' } = {}) => {
  let results = PRODUCTS;

  if (category) {
    const categoryLower = category.toLowerCase();
    results = results.filter((p) =>
      p.category.toLowerCase().includes(categoryLower)
      || p.sub_category.toLowerCase().includes(categoryLower)
    );
  }

  if (query) {
    const queryLower = query.toLowerCase();
    results = results.filter((p) =>
      p.name.toLowerCase().includes(queryLower)
      || p.description.toLowerCase().includes(queryLower)
    );
  }

  const products = results.map((p) => ({
    productId: p.productId,
    name: p.name,
    shortDescription: p.description,
    category: p.category,
    rating: p.rating,
    reviewCount: p.reviewCount,
    imageUrl: p.image_url,
    productUrl: p.url,
  }));

  if (products.length === 0) {
    return {
      content: [{ type: 'text', text: `No products found for "${category}".` }],
      structuredContent: { products: [], total: 0, category: null },
      _meta: { 'openai/widgetDescription': WIDGET_DESCRIPTION }
    };
  }

  return {
    content: [
      { type: 'text', text: `Found ${products.length} product(s) in "${category}".` }
    ],
    structuredContent: { products, total: products.length, category },
    _meta: { 'openai/widgetDescription': WIDGET_DESCRIPTION }
  };
};
```

**运行时发生的情况：**

1. 用户请求LLM平台&#x200B;*“给我看你的咖啡产品”。*
2. LLM平台与&#x200B;*搜索产品*&#x200B;的意图匹配并提取`category`。
3. MCP服务器使用`{ category: 'bagged-coffee' }`调用您的处理程序。
4. 您的处理程序将筛选目录并返回`content`（LLM的文本摘要）+ `structuredContent`（小组件的产品数组）。
5. LLM平台显示文本响应，并将结构化数据传递到EDS小组件，该小组件将呈现产品轮播。

## 如果缺少处理程序，该怎么办？

如果您在UI中定义了操作，但尚未创建处理程序文件，则该操作仍会在部署时注册。 调用将使用默认存根处理程序，该处理程序会返回空内容，直到您添加实际代码为止。 这意味着您可以先在UI中定义所有操作，然后递增地实施它们。

