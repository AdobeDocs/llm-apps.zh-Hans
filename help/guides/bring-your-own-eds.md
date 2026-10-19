---
title: 自带Edge Delivery Services项目
description: 将现有Adobe Edge Delivery Services项目连接到Adobe LLM Apps操作。
hidefromtoc: true
source-git-commit: 0f9dd0a5b1008e183eb8ab5a4c2eae34a78923bb
workflow-type: tm+mt
source-wordcount: '866'
ht-degree: 1%
---

# 自带EDS项目 {#bring-your-own-eds}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps]当前在Beta中。
>
>此处显示的功能、工作流和UI不一定表示产品的最终状态。 要加入Beta，请发送电子邮件至llm-apps-beta@adobe.com 。

使用本指南连接兼容的现有Edge Delivery Services (EDS)项目或向您自己创建的操作添加构件。 现有项目是可选的。 如果您设置自己的基础结构，则默认路径从两个空存储库开始，平台在[基础结构设置](/help/guides/create-app.md)期间初始化这两个存储库。

如果平台自动创建了小组件，请改为遵循[自定义生成的小组件](/help/guides/widgets.md)。 生成的项目已包含此处所述的SDK文件、块、内容和操作配置。

**在本指南中：**&#x200B;了解如何准备EDS项目、安装SDK以及构建和发布构件。 然后，将构件连接到操作并在部署的应用程序中对其进行测试。

## 开始之前

您需要：

- 与AEM样板兼容的EDS存储库，已安装[AEM Code Sync](https://github.com/apps/aem-code-sync)并配置内容源。
- 在该存储库中添加依赖项和创建块的权限。
- 为EDS站点配置响应标头的权限。
- [!DNL LLM Apps]中带有返回`structuredContent`的处理程序的操作。

链接现有存储库时，平台会检查`scripts/aem.js`、`blocks/`、`styles/styles.css`和`head.html`。 嵌入还导入`scripts/scripts.js`并调用其导出的`decorateMain`函数以准备块。 保持EDS块加载合同不变。 仅所需文件无法确认高度自定义的项目是否可以正确呈现。

## 安装LLM应用程序SDK

从EDS项目根目录：

```bash
npm install @adobe/llmapps-sdk
```

包的安装后挂接将构件入口点、桥接实施和支持的Adobe库复制到项目中：

```text
scripts/
├── aem-embed.js
├── llmapps-sdk.js
├── alloy-base.min.js
└── alloy-standalone.min.js
```

操作的脚本URL指向`scripts/aem-embed.js`，而不是`scripts/llmapps-sdk.js`。 验证安装是否复制了SDK文件，然后提交这些文件并将其与项目一起推送。 安装或更新SDK时，挂接会覆盖复制的文件；请勿自定义复制的SDK文件。

## 创建构件块

为操作创建块：

```text
blocks/
└── search-products/
    ├── search-products.js
    └── search-products.css
```

导出标准EDS `decorate`函数，并将连接的桥接器作为第二个参数：

```javascript
const PREVIEW_RESULT = {
  records: [{ id: 'P-100', name: 'Frescopa House Blend' }]
};

export default async function decorate(block, bridge) {
  if (bridge?.isConnected) {
    bridge.applyHostStyles();
  }

  const live = bridge?.isConnected && bridge.hostContext?.preview !== true;
  const result = live ? await bridge.toolResult : null;
  const data = live ? result?.structuredContent : PREVIEW_RESULT;
  const records = Array.isArray(data?.records) ? data.records : [];

  if (result?.isError) {
    const message = document.createElement('p');
    message.textContent = 'Product search is unavailable. Try again later.';
    block.replaceChildren(message);
    bridge.autoResize(block);
    return;
  }

  const list = document.createElement('ul');
  records.forEach((product) => {
    const item = document.createElement('li');
    item.textContent = String(product?.name ?? 'Product');
    list.append(item);
  });

  if (records.length === 0) {
    const item = document.createElement('li');
    item.textContent = 'No matching products were found.';
    list.append(item);
  }
  block.replaceChildren(list);

  if (bridge?.isConnected) {
    bridge.autoResize(block);
  }
}
```

使用对文本值进行编码的DOM API。 请勿将外部数据关联到HTML中。

该示例需要`structuredContent.records`。 匹配处理程序返回的属性名称和记录字段。 检查`bridge.isConnected`，而不只是检查网桥是否存在： `toolResult`从未在独立模式下解析。 在LLM应用程序预览中，当`bridge.hostContext.preview`指示预览模式时，使用块的示例数据。

## 创作和发布构件页面

为构件创建一个EDS页面，并将块添加到该页面。 发布页面。

页面URL将成为该操作的构件URL。 例如，默认生成的配置使用EDS预览原点：

```text
https://main--<repo>--<owner>.aem.page/eds-widgets/search-products-demo
```

在该处发布页面和资产时，EDS实时源（如`https://main--<repo>--<owner>.aem.live`）也可正常工作。 使用实际站点来源；配置的EDS站点可以具有与GitHub存储库和所有者不同的站点和组织名称。

验证将`.plain.html`附加到小组件URL是否返回页面片段，以及页面是否包含`search-products`块。 页面路径不需要与操作标识符匹配，但一致的约定使项目更容易维护。

## 配置CORS

该小组件跨源加载EDS页面、脚本、样式、块和媒体。 配置EDS站点的标头：

```json
{
  "/**": [
    {
      "key": "access-control-allow-origin",
      "value": "<allowed-host-origin>"
    }
  ]
}
```

使用支持的LLM平台所需的实际构件沙盒原点，而不仅仅是聊天网站的原点。 验证`.plain.html`响应和导入的JavaScript、样式、字体和媒体上的CORS。

平台的自动EDS设置为公共小部件交付配置`access-control-allow-origin: *`，并且可以替换更小的现有值。 仅将该策略用于刻意使用公共资源，而不使用经过认证的跨域请求，并且您的安全要求允许这样做。 如果您管理受限制的现有站点，请在连接该站点之前查看其策略；不要公开私有站点内容以使构件正常工作。

有关EDS配置详细信息，请参阅[配置服务](https://aem.live/docs/config-service-setup)。

## 配置操作

在[!DNL LLM Apps]中，打开操作并选择&#x200B;**[!UICONTROL LLM构件详细信息]**。 在&#x200B;**[!UICONTROL 构件信息]**&#x200B;下，将&#x200B;**[!UICONTROL 构件类型]**&#x200B;设置为&#x200B;**[!UICONTROL EDS]**，然后展开&#x200B;**[!UICONTROL 模板URL]**。

输入：

- **[!UICONTROL 脚本URL]**

  ```text
  https://main--<repo>--<owner>.aem.page/scripts/aem-embed.js
  ```

- **[!UICONTROL 小组件URL]**

  ```text
  https://main--<repo>--<owner>.aem.page/eds-widgets/search-products-demo
  ```

将示例替换为上面验证的工作站点原点和页面。 使用最低权限配置CSP域和浏览器权限。 对于连接（如页面片段获取）和资源（如块脚本和样式），必须允许EDS源。 仅当小组件要求时才添加其他源。

选择&#x200B;**[!UICONTROL 保存更改]**，然后重新部署到正在测试的环境。 在部署之前，保存的构件配置不会更新正在运行的MCP服务器。

有关字段定义，请参阅[操作和小组件字段](/help/reference/reference-docs.md)。

## 测试集成

1. 直接预览EDS页面并验证其示例数据回退。
2. 在本地测试处理程序，并将其`structuredContent`与块预期的形状进行比较。
3. 将处理程序和操作元数据部署到暂存环境。 EDS代码和内容发布是一个单独的步骤。
4. 从支持的LLM平台调用操作。 查看ChatGPT中的[测试](/help/guides/test-in-chatgpt.md)或Claude中的[测试](/help/guides/test-in-claude.md)。
5. 验证加载、成功、空和错误状态。

如果页面直接工作但不在LLM平台中，请检查CORS、CSP、HTTPS URL和`structuredContent`形状。 请参阅[疑难解答](/help/reference/troubleshooting.md)。
