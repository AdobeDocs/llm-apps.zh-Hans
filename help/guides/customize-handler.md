---
title: 自定义生成的操作处理程序
description: 了解Adobe LLM应用程序处理程序合同，替换生成的示例数据，并使处理程序输出与其小部件保持一致。
source-git-commit: 0f9dd0a5b1008e183eb8ab5a4c2eae34a78923bb
workflow-type: tm+mt
source-wordcount: '880'
ht-degree: 0%
---

# 自定义生成的处理程序 {#customize-generated-handler}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps]当前在Beta中。
>
>此处显示的功能、工作流和UI不一定表示产品的最终状态。 要加入Beta，请发送电子邮件至llm-apps-beta@adobe.com 。

平台会为每个生成的操作创建一个工作处理程序。 处理程序最初会返回用于测试完整体验的示例数据。

**在本指南中：**&#x200B;了解如何查找生成的处理程序、如何将其示例数据替换为对您的系统的调用，以及如何使其响应与构件兼容。 然后测试和部署更改。

## 查找生成的处理程序

打开[基础结构设置](/help/guides/create-app.md)期间连接的处理程序存储库。 如果您使用Adobe托管基础结构，请先请求代码访问权限，然后再继续。

```text
actions/
└── <action-slug>/
    ├── index.js
    └── data.json
```

匹配的测试位于单独的目录中：

```text
test/
└── actions/
    └── <action-slug>.test.js
```

使用生成的拉取请求来查找确切的文件。 文件夹概要文件使用连字符：平台在`actions/search-products/`下生成了一个代码标识符为`search_products`的操作。 运行时可识别断字表单以及与该标识符完全匹配的文件夹。

编辑生成的`index.js`和任何关联的示例数据。 请勿更改运行时文件，如`entry.js`。

## 处理程序合同

每个处理程序导出一个异步函数：

```javascript
module.exports = async (args, extra) => {
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

对于具有输入架构的操作，该函数接收`args`对象和可选运行时上下文`extra`，并返回结果对象。 将`extra`用于运行时帮助程序，如`getVariable(extra, 'PRODUCT_API_ORIGIN')`；应用程序变量不会通过`process.env`自动公开。 请参阅[配置应用程序变量和密钥](/help/guides/app-variables.md)。

### 输入： `args`

`args`包含为[!DNL LLM Apps]中的操作定义的参数。

对于具有`category`和`query`参数的操作：

```javascript
module.exports = async ({ category = '', query = '' } = {}, extra) => {
  // Validate business rules before using the arguments.
};
```

通过操作元数据，运行时将声明的输入架构转换为对受支持规则的验证，包括必填字段、原始类型、枚举和数组项。 运行时未执行完整的JSON架构验证。 检查约束，例如字符串长度、数字范围和处理程序中的嵌套对象属性。

没有架构元数据的本地发现与部署不相等：运行时未以正常`(args, extra)`形式传递操作参数。 使用直接处理程序测试或代表性本地`actions.json`元数据测试参数。 请参阅[本地处理程序开发和测试](/help/reference/development.md)。

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

保持文本响应简洁。 不包括凭据、内部错误或用户无权查看的数据。

### 输出： `structuredContent`

当操作具有小部件时，返回`structuredContent`。 它必须是纯对象，而不是空数组。

```javascript
structuredContent: {
  arguments: { query: 'coffee' },
  records: [
    {
      id: 'P-100',
      name: 'Frescopa House Blend',
      price: '$14.99'
    }
  ]
}
```

`structuredContent`是交付给主机的MCP操作结果的一部分。 请勿假设模型或用户隐藏了该变量。 仅返回体验、从不凭据或专用服务器配置所需的授权数据。

对于纯文本操作，返回`structuredContent`是可选的。

## 处理程序 — 小部件合同

处理程序和构件共享一个协定：`structuredContent`的形状。

| 处理程序输出 | 用途 |
|---|---|
| `content` | 模型可读响应和纯文本回退 |
| `structuredContent` | 数据已传送到主机并由小组件通过`bridge.toolResult`读取 |

该构件从LLM应用程序SDK Bridge中读取处理程序结果：

```javascript
export default async function decorate(block, bridge) {
  if (!bridge?.isConnected || bridge.hostContext?.preview === true) {
    // Render the block's local preview data instead.
    return;
  }
  const result = await bridge.toolResult;
  const records = result?.structuredContent?.records ?? [];

  // Render records.
}
```

当前生成的处理程序使用包含`arguments`和`records`的对象。 现有应用程序可以使用不同的形状，因此在编辑之前请检查您生成的文件。 更改字段名称或类型可能会破坏构件。 同时更新处理程序、小部件和测试。

## 替换示例数据

生成的处理程序从`data.json`加载代表性记录。 将示例数据查找替换为对您的系统的服务器端调用。 以下示例使用公共HTTPS目录API并从应用程序变量中读取其非敏感来源。 将响应验证调整为适合您的API。

```javascript
const { getVariable } = require('@adobe/llm-apps-runtime');

module.exports = async ({ query = '' } = {}, extra) => {
  const normalizedQuery = typeof query === 'string' ? query.trim() : '';
  if (!normalizedQuery || normalizedQuery.length > 200) {
    return {
      content: [{ type: 'text', text: 'Enter a valid product search.' }],
      structuredContent: { arguments: {}, records: [] },
      isError: true
    };
  }

  const knownArguments = { query };
  try {
    const apiOrigin = getVariable(extra, 'PRODUCT_API_ORIGIN');
    if (!apiOrigin) {
      throw new Error('Missing configuration');
    }
    const origin = new URL(apiOrigin);
    if (origin.protocol !== 'https:' || origin.username || origin.password) {
      throw new Error('Invalid API origin');
    }
    const url = new URL('/v1/products', origin);
    url.searchParams.set('query', normalizedQuery);

    const response = await fetch(url, {
      signal: AbortSignal.timeout(8000)
    });
    if (!response.ok) {
      throw new Error('Request failed');
    }
    const payload = await response.json();
    if (!payload || !Array.isArray(payload.products)
        || !payload.products.every((product) =>
          product
          && typeof product.id === 'string'
          && typeof product.name === 'string'
          && typeof product.price === 'string')) {
      throw new Error('Unexpected response');
    }

    const records = payload.products.map(({ id, name, price }) => ({
      id,
      name,
      price
    }));

    return {
      content: [
        { type: 'text', text: `Found ${records.length} matching products.` }
      ],
      structuredContent: { arguments: knownArguments, records }
    };
  } catch {
    return {
      content: [{ type: 'text', text: 'Product search is unavailable. Try again later.' }],
      structuredContent: { arguments: knownArguments, records: [] },
      isError: true
    };
  }
};
```

在处理程序中保留受保护的网络访问。 切勿将API凭据放入小组件JavaScript或源代码控制中。 有关代表登录用户发出的请求，请参阅[对最终用户进行身份验证](/help/guides/authentication.md)。

有关您的处理程序所需的非敏感设置，如服务URL，请参阅[配置应用程序变量和密钥](/help/guides/app-variables.md)。 为每个环境单独配置变量。 变量更改将在下次部署时生效。 当前UI仅支持添加变量；请勿使用变量存储API凭据。

## 处理预期状态

保留每个结果的可预测输出形状。

### 找到个结果

```javascript
{
  content: [{ type: 'text', text: 'Found 3 products.' }],
  structuredContent: { arguments: { query: 'coffee' }, records: [...] }
}
```

### 无结果

```javascript
{
  content: [{ type: 'text', text: 'No matching products were found.' }],
  structuredContent: { arguments: { query: 'coffee' }, records: [] }
}
```

可预测的结果形状允许构件呈现空状态，而无需猜测`records`是否存在。

对于服务故障，返回带有`isError: true`的安全错误结果，如示例所示。 检查小部件中的`result.isError`，以便将失败的请求显示为成功的空搜索。 不要公开栈栈跟踪、凭据、内部主机或上游响应主体。

## 测试合同

每当处理程序更改时，请更新生成的测试。 封面:

- 参数有效且无效。
- “结果”和“无结果”状态。
- API失败和超时。
- API响应的格式不正确。
- 每个结果中都存在`content`。
- `structuredContent`的纯对象。
- 构件所需的形状。

运行存储库配置的测试套件：

```bash
npm test
```

如果生成的测试导入`node:test`而不是Jest，请使用`node --test test/actions/<action-slug>.test.js`运行它，并验证其导入路径是否从`test/actions/`解析。 使用适合该文件的测试运行器；样板的`npm test`命令运行Jest。

有关本地MCP测试，请参阅[本地处理程序开发和测试](/help/reference/development.md)。

## 部署更改

1. 提交并推送处理程序更改。
2. 如果更改数据形状，请更新并推送构件。
3. [将应用程序](/help/guides/deploy-your-app.md)部署到暂存环境。
4. 在支持的LLM平台中测试。 查看ChatGPT中的[测试](/help/guides/test-in-chatgpt.md)或Claude中的[测试](/help/guides/test-in-claude.md)。
5. 测试在暂存环境中通过后，部署到生产环境。

接下来，请参阅[自定义生成的构件](/help/guides/widgets.md)。
