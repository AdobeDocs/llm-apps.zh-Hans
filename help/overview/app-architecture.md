---
title: 应用程序如何连接在一起
description: 更仔细地了解您拥有的各个部分（操作元数据、处理程序代码和小部件）如何在构建时和运行时整合到一个正在运行的LLM应用程序中。
source-git-commit: e066f66b37914e2f747176e865e26dcc074bff20
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

**LLM应用程序**&#x200B;是一组&#x200B;**操作**（每个操作都是工具在&#x200B;**模型上公开的）
您发布到单个终结点的上下文协议**，或&#x200B;**MCP**。 聊天主持人
比如[!DNL ChatGPT]将发现这些工具，在对话中调用这些工具，然后渲染
包含结果的**交互小组件** — 直接在聊天中。

## 整个线路，建造→运行

**图1 — 生成时间。** 您拥有三个单独的表面；平台熔丝
将它们整合到一个可部署的应用程序中。

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

- **LLM应用程序UI** — 可在其中创建、编辑和管理每个操作的定义：
其**代码标识符**(您在此处设置过一次的固定概要，例如`my_action`，该
将整个UI、处理程序和小组件中的相同操作绑定在一起)，
描述、输入架构、构件选择和CSP/可见性标记。 无代码。
- **操作处理程序存储库** — 服务器端存储库（基于我们的模板）
编写业务逻辑的位置。 每个处理程序函数都会返回两个内容：
  `content` （纯文本，*LLM*&#x200B;读取）和`structuredContent` (数据对象
  *小组件*&#x200B;读取)。
- **小组件存储库** — 每个小组件都作为一个块存在并获取的EDS存储库
已发布到公共`*.aem.page` URL。 每个块使用
  [`@adobe/llmapps-sdk`](https://www.npmjs.com/package/@adobe/llmapps-sdk)，
在构件和主机/服务器之间架设桥梁。 它实施**MCP应用程序
规范** — 底层协议 — 位于简单API之后，并且
将LLM主机本身抽象化，因此同一个构件在中可以不进行修改
  [!DNL ChatGPT]、[!DNL Claude]、Gemini或任何其他MCP主机。

**图2 — 运行时。** 用户发送的每条消息发生什么情况，一次
一台服务器处于活动状态。 显示[!DNL ChatGPT]作为示例主机 — 
对任何MCP主机（如[!DNL Claude]）播放相同的序列。

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
