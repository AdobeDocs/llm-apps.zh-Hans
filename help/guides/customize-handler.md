---
title: 自定义生成的操作处理程序
description: 了解Adobe LLM应用程序处理程序合同，替换生成的示例数据，并使处理程序输出与其小部件保持一致。
source-git-commit: eec74b87457bc852d7a8dd0e46c2a4385a93ae0a
workflow-type: tm+mt
source-wordcount: '542'
ht-degree: 0%

---


# 自定义生成的处理程序 {#customize-generated-handler}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps]当前在Beta中。
>
>此处显示的功能、工作流和UI不一定表示产品的最终状态。 要加入Beta，请发送电子邮件至llm-apps-beta@adobe.com 。

载入代理为每个生成的操作创建一个工作处理程序。 处理程序最初会返回示例数据，以便您测试整个体验。

使用本指南了解处理程序合同，并将示例数据替换为您的API或数据源。

**历程：**&#x200B;查找生成的处理程序→了解其输入和结果，→连接系统→保持Widget合同在测试和部署→保持一致。

## 查找生成的处理程序

打开载入期间选定的处理程序存储库：

```text
actions/
└── <action-name>/
    └── index.js
```

匹配测试单独存储：

```text
test/
└── actions/
    └── <action-name>.test.js
```

编辑生成的`index.js`。 请勿更改运行时文件，如`entry.js`。

## 处理程序合同

每个处理程序导出一个异步函数：

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

函数接收`args`对象并返回结果对象。

### 输入： `args`

`args`包含为[!DNL LLM Apps]中的操作定义的参数。

对于具有`category`和`query`参数的操作：

```javascript
module.exports = async ({ category = '', query = '' } = {}) => {
  // Use the validated action arguments.
};
```

当操作元数据包括`inputSchema`时，运行时将验证输入架构，就像部署后一样。 没有`actions.json`的本地处理程序发现不应用架构验证。 处理程序应始终强制实施业务规则，例如支持的值、最大长度以及允许的组合。

### 输出： `content`

始终返回`content`。 它是LLM平台和不显示小部件的主机读取的内容部分的数组。

```javascript
content: [
  {
    type: 'text',
    text: 'Found 3 products matching your search.'
  }
]
```

保持此响应简洁。 不包括凭据、内部错误或用户无权查看的数据。

### 输出： `structuredContent`

当操作具有小部件时，返回`structuredContent`。 它必须是纯对象，而不是空数组。

```javascript
structuredContent: {
  products: [
    {
      id: 'P-100',
      name: 'Frescopa House Blend',
      price: '$14.99'
    }
  ],
  total: 1
}
```

`structuredContent`将发送到构件，而不是发送到LLM。 仅返回界面所需的字段。

对于纯文本操作，可以忽略`structuredContent`。

## 处理程序 — 小部件合同

处理程序和构件共享一个协定：`structuredContent`的形状。

```text
Action arguments
      ↓
Handler
      ├── content → LLM text response
      └── structuredContent → Widget
                                  ↓
                           bridge.toolResult
```

该构件从LLM应用程序SDK Bridge中读取处理程序结果：

```javascript
export default async function decorate(block, bridge) {
  const result = await bridge.toolResult;
  const products = result?.structuredContent?.products ?? [];

  // Render products.
}
```

如果处理程序返回：

```javascript
structuredContent: {
  products: [...],
  total: 3
}
```

构件必须读取`structuredContent.products`和`structuredContent.total`。

更改字段名称或类型可能会破坏构件。 同时更新处理程序、小部件和测试。

## 替换示例数据

生成的处理程序通常包含内存中的示例数组。 将该数据查找替换为对您的系统的服务器端调用。

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

  const products = payload.products.map(({ id, name, price }) => ({
    id,
    name,
    price
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

在处理程序中保留受保护的网络访问。 切勿将API凭据放入小组件JavaScript或源代码控制中。

## 处理预期状态

保留每个结果的可预测输出形状。

### 找到个结果

```javascript
{
  content: [{ type: 'text', text: 'Found 3 products.' }],
  structuredContent: { products: [...], total: 3 }
}
```

### 无结果

```javascript
{
  content: [{ type: 'text', text: 'No matching products were found.' }],
  structuredContent: { products: [], total: 0 }
}
```

构件现在可以呈现空状态，而无需猜测`products`是否存在。

对于服务故障，返回或抛出安全错误，而不公开栈栈跟踪、令牌、内部主机或上游响应主体。

## 测试合同

每当处理程序更改时，请更新生成的测试。 封面:

- 参数有效且无效。
- “结果”和“无结果”状态。
- API失败和超时。
- API响应的格式不正确。
- `content`始终存在。
- `structuredContent`是纯对象。
- 构件所需的形状。

运行：

```bash
npm test
```

有关本地MCP测试，请参阅[本地处理程序开发和测试](/help/reference/development.md)。

## 部署更改

1. 提交并推送处理程序更改。
2. 如果数据形状发生更改，请更新并推送构件。
3. [将应用程序](/help/guides/deploy-your-app.md)部署到暂存环境。
4. [测试ChatGPT插件](/help/guides/test-in-chatgpt.md)。
5. 暂存成功后，部署到生产环境。

接下来，请参阅[自定义生成的构件](/help/guides/widgets.md)。
