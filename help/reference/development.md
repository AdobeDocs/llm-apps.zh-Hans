---
title: 本地处理程序开发和测试
description: Adobe LLM应用程序的处理程序项目结构、本地服务器命令、MCP测试和单元测试。
source-git-commit: eec74b87457bc852d7a8dd0e46c2a4385a93ae0a
workflow-type: tm+mt
source-wordcount: '280'
ht-degree: 1%

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

## 项目结构

您的链接存储库遵循以下布局：

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

- **`entry.js`**&#x200B;是webpack入口点。 在生成时，它会发现每`actions/*/index.js`个文件并将它们捆绑到单个`dist/index.js`中。 请勿修改。
- **`actions.json`**&#x200B;已授权。 部署管道自动从[!DNL LLM Apps]中的操作元数据写入它。
- **测试**&#x200B;位于`test/actions/`下，**不在`actions/`内**。 Webpack将`actions/`下的所有内容捆绑到已部署的工件中 — 共同定位测试会将它们发往[!DNL Adobe I/O Runtime]。

## 本地开发

您可以在本地开发和测试处理程序，而无需使用Adobe凭据：

```bash
npm install
npm run dev:local
```

这将使用webpack生成项目，并在`http://localhost:9080`上启动纯Node.js HTTP服务器。 服务器自动发现`actions/`下的处理程序文件，并将它们注册为MCP工具。

### 本地元数据行为

当前UI不提供`actions.json`下载。 您可以运行没有此文件的本地服务器；它将发现`actions/`下的处理程序并使用最少的元数据注册它们。

如果没有`actions.json`，则不会针对UI输入架构验证本地操作参数。 单元测试和集成测试使用`test/fixtures/actions.json`作为代表性元数据。

### 使用curl进行测试

```bash
# List all registered tools
curl -sX POST "http://localhost:9080" \
  -H 'content-type: application/json' \
  -H 'accept: application/json;q=1.0, text/event-stream;q=0.5' \
  -d '{"jsonrpc":"2.0","id":1,"method":"tools/list","params":{}}'

# Call the boilerplate echo action
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

处理程序单元测试位于`test/actions/`下并镜像`actions/`布局：

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
npm test                                      # all tests
npx jest test/actions/echo                   # one action only
```

在本地测试通过后，推送更改并遵循[部署更改](/help/guides/deploy-your-app.md)。

