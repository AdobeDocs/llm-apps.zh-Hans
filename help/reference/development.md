---
title: 本地处理程序开发和测试
description: Adobe LLM应用程序的处理程序项目结构、本地服务器命令、MCP测试和单元测试。
source-git-commit: 0f9dd0a5b1008e183eb8ab5a4c2eae34a78923bb
workflow-type: tm+mt
source-wordcount: '664'
ht-degree: 0%
---

# 本地处理程序开发和测试 {#development}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps]当前在Beta中。
>
>此处显示的功能、工作流和UI不一定表示产品的最终状态。 要加入Beta，请发送电子邮件至llm-apps-beta@adobe.com 。

在本地开发处理程序时使用此引用。 有关处理程序结果协定，请参阅[自定义生成的处理程序](/help/guides/customize-handler.md)。

## 要求

- Node.js 24或更高版本。
- npm。
- 链接处理程序存储库的本地克隆。

对于Adobe托管的基础架构，请在遵循此参考之前请求存储库访问权限。 本地处理程序执行不需要Adobe凭据，但受保护的上游服务仍需要其自身的授权访问权限。

## 项目结构

当前处理程序样板遵循以下布局：

```
your-llm-app/
├── entry.js                   # Webpack entry — do not modify
├── actions/                   # One folder per action
│   └── echo/
│       └── index.js           # Example handler
├── test/
│   ├── actions/
│   │   └── echo.test.js
│   ├── fixtures/
│   │   └── actions.json
│   ├── html-transform.js
│   ├── jest.setup.js
│   └── server.test.js
├── server/
│   └── local.js               # Local dev server (port 9080)
├── actions.json               # Gitignored — optional local metadata
├── app.config.yaml            # Adobe I/O Runtime config
├── webpack.config.js
└── package.json
```

要点：

- **`entry.js`**&#x200B;是webpack入口点。 在生成时，它将发现每`actions/*/index.js`个文件并将它们捆绑到单个`dist/index.js`中。 请勿修改入口点。
- **`actions.json`**&#x200B;已授权。 部署管道自动从[!DNL LLM Apps]中的操作元数据写入它。
- **测试**&#x200B;位于`test/actions/`下，**不在`actions/`内**。 构建将发现处理程序入口点并捆绑其导入；将测试保留在该发现树之外。
- **操作路径**&#x200B;使用代码标识符或其断字形式。 例如，`search_products`解析为`actions/search_products/index.js`，或者，对于生成的代码，`actions/search-products/index.js`。 显示名称不是查找键。
- **支持数据**（如生成的`data.json`）在处理程序导入时捆绑在一起。

## 本地开发

要在本地开发和测试没有Adobe凭据的处理程序，请运行：

```bash
npm install
npm run dev:local
```

开发命令使用webpack构建项目，并在`http://localhost:9080`上启动纯Node.js HTTP服务器。 在处理程序或本地元数据更改后停止并重新运行`npm run dev:local`；该命令不监视文件。

要使用其他端口，请执行以下操作：

```bash
PORT=9081 npm run dev:local
```

### 本地元数据行为

当前UI不提供`actions.json`下载，即使样板README和运行时启动横幅中提到一个。 本地服务器也启动时没有填充文件：当文件缺失时，生成将创建`{"actions":[]}`，并且运行时回退到处理程序发现。

发现对于列出处理程序很有用，但它不是完整的参数测试模式。 如果没有输入架构，MCP SDK将调用处理程序，并将运行时上下文作为其第一个参数，而不是使用普通`(args, extra)`签名。 SDK不转发提供的操作参数。

对于具有参数的MCP调用，请创建&#x200B;**仅限本地** `actions.json`，其中代表性元数据与操作编辑器匹配。 对于样板`echo`处理程序：

```json
{
  "actions": [
    {
      "name": "echo",
      "description": "Echo the supplied message.",
      "inputSchema": {
        "type": "object",
        "properties": {
          "message": { "type": "string" }
        },
        "required": ["message"]
      }
    }
  ]
}
```

当`actions`数组非空时，运行时仅注册数组中命名的操作。 将每个操作的代码标识符用作`name`。 即使没有参数的操作也需要具有`"properties": {}`的对象架构以保留常规处理程序签名。

本地元数据不会更新UI或部署配置。 运行时将验证受支持的类型、必填字段、枚举和数组项，但不是每个JSON架构约束；验证处理程序中的长度、范围、嵌套对象和业务规则。 样板集成测试使用`test/fixtures/actions.json`作为代表性元数据。

### 本地应用程序变量

应用程序变量通过`extra.variables`而非`process.env`到达处理程序。 用`@adobe/llm-apps-runtime`中的`getVariable(extra, name)`阅读这些内容。 要在构建后模拟非敏感设置，请执行以下操作：

```bash
npm run build
node server/local.js \
  --param 'LLMA_VARIABLE_NAMES=["GREETING_PREFIX"]' \
  --param GREETING_PREFIX=Howdy
```

提供JSON清单和命名值。 在测试其正常`(args, extra)`协定时，使用输入架构在本地`actions.json`中包含处理程序。 命令行标记不会从平台加载应用程序设置。 有关特定于环境的部署设置，请参阅[配置应用程序变量和密钥](/help/guides/app-variables.md)。

### 使用curl进行测试

添加以上本地`echo`元数据后，生成并启动服务器，然后运行：

```bash
# List all registered tools
curl -sX POST "http://localhost:9080" \
  -H 'content-type: application/json' \
  -H 'accept: application/json;q=1.0, text/event-stream;q=0.5' \
  -d '{"jsonrpc":"2.0","id":1,"method":"tools/list","params":{}}'

# Call the boilerplate echo action (returns "Echo: hello")
curl -sX POST "http://localhost:9080" \
  -H 'content-type: application/json' \
  -H 'accept: application/json;q=1.0, text/event-stream;q=0.5' \
  -d '{"jsonrpc":"2.0","id":2,"method":"tools/call","params":{"name":"echo","arguments":{"message":"hello"}}}'
```

### 使用MCP检查器进行测试

```bash
npx @modelcontextprotocol/inspector
```

将&#x200B;**传输类型**&#x200B;设置为`streamable-http`并将&#x200B;**URL**&#x200B;设置为`http://localhost:9080`。

## 测试

处理程序单元测试位于`test/actions/`下，并直接调用导出的函数，无需MCP架构验证：

```javascript
// test/actions/echo.test.js
const handler = require('../../actions/echo/index.js')

test('echoes the message', async () => {
  const result = await handler({ message: 'hello' })
  expect(result.content[0].text).toBe('Echo: hello')
})

test('always returns content parts', async () => {
  const result = await handler({})
  expect(Array.isArray(result.content)).toBe(true)
})
```

运行测试：

```bash
npm test                                 # configured Jest suite
npm test -- --runInBand test/actions/echo.test.js  # one boilerplate action
```

某些生成的测试使用Node的内置测试运行程序而不是Jest。 检查测试导入，并使用匹配的运行程序，例如`node --test test/actions/search-products.test.js`用于导入`node:test`的文件。 将文件放在`test/actions/`下后，验证生成的相对导入。

单元测试本身不会验证元数据、MCP验证、构件加载或主机行为。 使用代表性元数据测试本地端点，然后在支持的LLM平台上测试已部署的应用程序。

在本地测试通过后，推送更改并遵循[部署更改](/help/guides/deploy-your-app.md)。
