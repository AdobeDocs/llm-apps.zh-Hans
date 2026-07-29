---
title: 自带Edge Delivery Services项目
description: 将现有Adobe Edge Delivery Services项目连接到Adobe LLM Apps操作。
source-git-commit: eec74b87457bc852d7a8dd0e46c2a4385a93ae0a
workflow-type: tm+mt
source-wordcount: '472'
ht-degree: 2%

---


# 自带EDS项目 {#bring-your-own-eds}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps]当前在Beta中。
>
>此处显示的功能、工作流和UI不一定表示产品的最终状态。 要加入Beta，请发送电子邮件至llm-apps-beta@adobe.com 。

如果您已有Edge Delivery Services (EDS)项目，或者您在没有载入代理的情况下创建应用程序，请阅读本指南。

如果载入代理已创建您的小组件，请改为遵循[自定义生成的小组件](/help/guides/widgets.md)。 生成的项目已包含此处所述的SDK文件、块、内容和操作配置。

**历程：**&#x200B;准备EDS项目→安装SDK →生成和发布块→配置操作→部署和测试。

## 开始之前

您需要：

- 安装了[AEM代码同步](https://github.com/apps/aem-code-sync)的EDS存储库。
- 在该存储库中添加依赖项和创建块的权限。
- 为EDS站点配置响应标头的权限。
- [!DNL LLM Apps]中带有返回`structuredContent`的处理程序的操作。

## 安装LLM应用程序SDK

从EDS项目根目录：

```bash
npm install @adobe/llmapps-sdk
```

该资源包会将构件入口点并桥接实施复制到项目中：

```text
scripts/
├── aem-embed.js
└── llmapps-sdk.js
```

该操作使用的脚本URL指向`scripts/aem-embed.js`。

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
export default async function decorate(block, bridge) {
  if (bridge) {
    bridge.applyHostStyles();
  }

  const result = bridge ? await bridge.toolResult : null;
  const products = result?.structuredContent?.products ?? [];

  const list = document.createElement('ul');
  products.forEach((product) => {
    const item = document.createElement('li');
    item.textContent = String(product.name ?? 'Product');
    list.append(item);
  });

  block.replaceChildren(list);

  if (bridge) {
    bridge.autoResize(block);
  }
}
```

使用对文本值进行编码的DOM API。 请勿将外部数据关联到HTML中。

## 创作和发布构件页面

为构件创建一个EDS页面，并将块添加到该页面。 发布页面。

实时页面URL将成为该操作的构件URL：

```text
https://main--<repo>--<owner>.aem.live/<widget-page>
```

页面路径不需要与操作名称匹配，但一致的约定使项目更容易维护。

## 配置CORS

该构件加载EDS页面以及跨源的脚本、样式、块和媒体。 配置EDS站点的标头：

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

使用您支持的LLM平台所需的特定主机来源。 仅当小组件刻意公开、不使用经过认证的跨域请求且您的安全要求允许时，才使用`*`。

有关EDS配置详细信息，请参阅[配置服务](https://aem.live/docs/config-service-setup)。

## 配置操作

在[!DNL LLM Apps]中，打开操作并选择&#x200B;**[!UICONTROL 小组件元数据]**。

输入：

- **[!UICONTROL 脚本URL]**

  ```text
  https://main--<repo>--<owner>.aem.live/scripts/aem-embed.js
  ```

- **[!UICONTROL 小组件URL]**

  ```text
  https://main--<repo>--<owner>.aem.live/<widget-page>
  ```

使用最低权限配置CSP域和浏览器权限。 仅添加构件所需的源和功能。

有关字段定义，请参阅[操作和小组件字段](/help/reference/reference-docs.md)。

## 测试集成

1. 直接预览EDS页面并验证其示例数据回退。
2. 在本地测试处理程序，并将其`structuredContent`与块预期的形状进行比较。
3. 将应用程序部署到暂存环境。
4. 从[!DNL ChatGPT]调用操作。
5. 验证加载、成功、空和错误状态。

如果页面直接工作但不在LLM平台中，请检查CORS、CSP、HTTPS URL和`structuredContent`形状。 请参阅[疑难解答](/help/reference/troubleshooting.md)。
