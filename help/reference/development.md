---
title: Adobe LLM应用程序的开发
description: Adobe LLM应用程序处理程序代码的项目结构、本地开发工作流和测试设置。
source-git-commit: 51ffb31eec82f9639bd7ade9052d61028c262d0e
workflow-type: tm+mt
source-wordcount: '318'
ht-degree: 4%

---


# 开发 {#development}

>[!IMPORTANT]
>
>**免责声明：**&#x200B;这是[!DNL LLM Apps]的测试版本。 此处显示的功能、工作流和UI不一定表示应用程序或产品的最终状态。

此部分介绍了处理程序项目结构、本地开发工作流和测试设置。 有关处理程序协定和示例代码，请参阅[编写操作处理程序](/help/guides/write-action-handler.md)。

## 项目结构

您的链接存储库遵循以下布局：

```
your-llm-app/
├── entry.js                   # Webpack entry — do not modify
├── actions/                   # One folder per action
│   ├── search-products/
│   │   └── index.js           # Handler (async function)
│   ├── get-product-details/
│   │   └── index.js
│   └── echo/
│       └── index.js
├── test/
│   ├── actions/
│   │   └── search-products.test.js
│   ├── fixtures/
│   │   └── actions.json
│   ├── html-transform.js
│   ├── jest.setup.js
│   └── server.test.js
├── server/
│   └── local.js               # Local dev server (port 9080)
├── actions.json               # Gitignored — local copy of UI metadata
├── app.config.yaml            # Adobe I/O Runtime config
├── webpack.config.js
└── package.json
```

要点：

- **`entry.js`**&#x200B;是webpack入口点。 在生成时，它会发现每`actions/*/index.js`个文件并将它们捆绑到单个`dist/index.js`中。 请勿修改。
- **`actions.json`**&#x200B;已授权。 从UI中的“操作”页面下载以进行本地开发。 对于部署，管道会自动从API写入它。
- **测试**&#x200B;位于`test/actions/`下，**不在`actions/`内**。 Webpack将`actions/`下的所有内容捆绑到已部署的工件中 — 共同定位测试会将它们发往[!DNL Adobe I/O Runtime]。

## 本地开发

您可以在本地开发和测试处理程序，而无需使用Adobe凭据：

```bash
npm install
npm run dev:local
```

这将使用webpack生成项目，并在`http://localhost:9080`上启动纯Node.js HTTP服务器。 服务器自动发现`actions/`下的处理程序文件，并将它们注册为MCP工具。

### 下载`actions.json`

若要让本地服务器知道您的操作元数据（名称、描述、输入架构），请从[!DNL LLM Apps] UI的“操作”页面下载`actions.json`，并将其放在存储库根目录下。 如果没有它，服务器将发现您的处理程序，但使用最少的元数据注册它们。

您还可以将`actions.example.json`复制到`actions.json`作为起点。

### 使用curl进行测试

```bash
# List all registered tools
curl -sX POST "http://localhost:9080" \
  -H 'content-type: application/json' \
  -H 'accept: application/json;q=1.0, text/event-stream;q=0.5' \
  -d '{"jsonrpc":"2.0","id":1,"method":"tools/list","params":{}}'

# Call the search-products action
curl -sX POST "http://localhost:9080" \
  -H 'content-type: application/json' \
  -H 'accept: application/json;q=1.0, text/event-stream;q=0.5' \
  -d '{"jsonrpc":"2.0","id":2,"method":"tools/call","params":{"name":"search-products","arguments":{"category":"bagged-coffee"}}}'
```

### 使用MCP检查器进行测试

```bash
npx @modelcontextprotocol/inspector
```

将&#x200B;**传输类型**&#x200B;设置为`streamable-http`并将&#x200B;**URL**&#x200B;设置为`http://localhost:9080`。

## 测试

处理程序单元测试位于`test/actions/`下并镜像`actions/`布局：

```javascript
// test/actions/search-products.test.js
const handler = require('../../actions/search-products/index.js')

test('returns all products when no filter is given', async () => {
  const result = await handler({})
  expect(result.content[0].text).toContain('product')
  expect(result.structuredContent.products.length).toBeGreaterThan(0)
})

test('filters by category', async () => {
  const result = await handler({ category: 'bagged-coffee' })
  expect(result.structuredContent.products.every(
    (p) => p.category === 'bagged-coffee'
  )).toBe(true)
})

test('filters by query', async () => {
  const result = await handler({ query: 'dark-roast' })
  expect(result.structuredContent.products.length).toBeGreaterThan(0)
})

test('returns empty result for unknown category', async () => {
  const result = await handler({ category: 'nonexistent' })
  expect(result.structuredContent.products).toHaveLength(0)
})
```

运行测试：

```bash
npm test                                      # all tests
npx jest test/actions/search-products        # one action only
```

## 部署

您不会手动生成或部署。 有关部署管道的完整演练，请参阅[部署您的应用程序](/help/guides/deploy-your-app.md)。

您的日常工作流程是：

| 步骤 | 操作 |
|------|--------|
| &#x200B;1. 编写或编辑处理程序 | `actions/<name>/index.js` |
| &#x200B;2. 下载元数据 | “操作”页面→ **下载actions.json** |
| &#x200B;3. 本地测试 | `npm run dev:local` |
| &#x200B;4. 推送代码 | `git push` |
| &#x200B;5. 部署 | **[!UICONTROL 部署]**→应用程序详细信息页面 |

