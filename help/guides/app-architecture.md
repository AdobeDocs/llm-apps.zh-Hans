---
title: 应用程序如何连接在一起
description: 更仔细地了解您拥有的各个部分（操作元数据、处理程序代码和小部件）如何在构建时和运行时整合到一个正在运行的LLM应用程序中。
source-git-commit: 2f3480b3667a6ab7c4ed65b999eed4638c383edb
workflow-type: tm+mt
source-wordcount: '335'
ht-degree: 0%

---


# 应用程序如何连接在一起 {#app-architecture}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps]当前在Beta中。
>
>此处显示的功能、工作流和UI不一定表示产品的最终状态。 要加入Beta，请发送电子邮件至llm-apps-beta@adobe.com 。

## 一句话

**LLM应用程序**&#x200B;是您发布到单个终结点的一组&#x200B;**操作**（每个操作都是通过&#x200B;**模型上下文协议**&#x200B;或&#x200B;**MCP**&#x200B;公开的工具）。 像[!DNL ChatGPT]这样的聊天主机发现这些工具，在对话中调用这些工具，并呈现&#x200B;**交互式小组件**，结果直接显示在聊天中。

## 整个线路，建造→运行

**图1 — 生成时间。** 您拥有三个单独的表面；平台将它们融合成一个可部署应用程序。

```
┌─────────────────────┐   ┌─────────────────────┐   ┌─────────────────────┐
│ 1  LLM Apps UI      │   │ 2  Action Handler   │   │ 3  Widget repo      │
│                     │   │    repo             │   │                     │
│ Create, edit, and   │   │                     │   │ Each widget is an   │
│ manage your action  │   │ Business logic —    │   │ EDS block,          │
│ definitions here    │   │ built from our      │   │ published to a      │
│ (metadata)          │   │ boilerplate         │   │ public URL on       │
│                     │   │                     │   │ *.aem.page          │
│                     │   │ Returns content     │   │                     │
│                     │   │ (for the LLM) +     │   │                     │
│                     │   │ structuredContent   │   │                     │
│                     │   │ (for the widget)    │   │                     │
└─────────────────────┘   └─────────────────────┘   └─────────────────────┘
           │                         │                         │
           └─────────────────────────┼─────────────────────────┘
                                     ▼
                       ┌─────────────────────────────┐
                       │ LLM Apps deploy pipeline    │
                       │ Combines the 3 surfaces     │
                       │ into one running app        │
                       └─────────────────────────────┘
                                     │
                                     ▼
                 ┌─────────────────────────────────────────┐
                 │ ONE MCP server on Adobe I/O Runtime     │
                 │ https://<ns>.adobeioruntime.net/.../mcp │
                 └─────────────────────────────────────────┘
```

- **LLM应用程序UI** — 在其中创建、编辑和管理每个操作的定义：其&#x200B;**代码标识符**（在此处设置一次的固定概要，例如`my_action`，该概要将整个UI、处理程序和构件中的同一操作绑定在一起）、描述、输入架构、构件选择和CSP/可见性标志。 无代码。
- **操作处理程序存储库** — 用于编写业务逻辑的服务器端存储库（基于样板构建）。 每个处理程序函数返回两个内容：`content` （LLM *读取的纯文本）和`structuredContent` （WIDGET*&#x200B;读取的数据对象）。**
- **构件存储库** — 每个构件都作为块存在并发布到公共`*.aem.page` URL的EDS存储库。 每个块都使用[`@adobe/llmapps-sdk`](https://www.npmjs.com/package/@adobe/llmapps-sdk)，即小部件与主机/服务器之间的桥梁。 它在一个简单的API后面实现&#x200B;**MCP应用程序规范**（底层协议），并抽象LLM主机本身，因此同一个小组件在[!DNL ChatGPT]、[!DNL Claude]、Gemini或任何其他MCP主机中不经修改即可工作。

**图2 — 运行时。** 一旦一台服务器处于活动状态，用户发送的每条消息都将发生什么情况？ 以[!DNL ChatGPT]作为示例主机显示 — 对于任何MCP主机（如[!DNL Claude]）播放相同的序列。

```
┌── ChatGPT  (the MCP host) ──────────────────────────────────────────────┐
│  1  tools/list  >  sees `my_action` + its description + input schema    │
│  2  user asks   >  "I need help with …"                                 │
│  3  model picks >  the description matches -> calls this tool           │
│  4  tools/call  >  { name: "my_action", arguments: {situation, ...} }   │
└─────────────────────────────────────────────────────────────────────────┘
                                     │
                 routes by CODE IDENTIFIER  ->  my_action
                                     ▼
┌── Adobe I/O Runtime ────────────────────────────────────────────────────┐
│  actions/my_action/index.js  --  your handler runs                      │
│  returns  { content -> text for the model , structuredContent -> data } │
└─────────────────────────────────────────────────────────────────────────┘
                                     │
                                     ▼
┌── Rendered inside the conversation ─────────────────────────────────────┐
│  5  render      >  ChatGPT renders the Widget repo's EDS block          │
│  The EDS block reads the structuredContent the Action Handler repo      │
│  returned, and draws the interactive card — live, inside the chat.      │
└─────────────────────────────────────────────────────────────────────────┘
```
