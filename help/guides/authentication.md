---
title: 使用您自己的身份提供程序验证最终用户
description: 为您的Adobe LLM应用程序启用最终用户身份验证，以便受支持的LLM平台在调用受保护的操作之前，将用户与您的身份提供程序登录。
source-git-commit: fd41dbcabc4db0cae766de19cb042d7c85d8b7aa
workflow-type: tm+mt
source-wordcount: '2149'
ht-degree: 0%
---

# 使用您自己的身份提供程序验证最终用户 {#authentication}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps]当前在Beta中。
>
>此处显示的功能、工作流和UI不一定表示产品的最终状态。 要加入Beta，请发送电子邮件至llm-apps-beta@adobe.com 。

默认情况下，应用程序上的每个操作都是公开的：任何具有MCP服务器URL的LLM平台都可以调用它，并且您的处理程序无法识别最终用户。

当操作需要知道哪个最终用户请求时（例如，返回其订单、权利或帐户详细信息），启用身份验证。 LLM平台使用&#x200B;**您的**&#x200B;身份提供程序(IdP)登录用户，通过每次调用发送生成的访问令牌，并且您的处理程序接收已验证的身份。

**历程：**&#x200B;复制资源标识符→配置您的身份提供程序→启用身份验证→为→部署的每个操作设置身份验证模式→读取处理程序中的身份→测试受保护的应用程序。

这是一个高级分支，不是首次运行历程的一部分。 完成[自动创建您的第一个应用](/help/guides/create-app.md)并先[部署您的应用](/help/guides/deploy-your-app.md)。

## 工作原理

您自带身份提供程序。 您部署的应用程序只是OAuth 2.1 **资源服务器** — 它验证您的授权服务器颁发的令牌。 它从不发出令牌，并且[!DNL Adobe]从不存储您的客户端ID或客户端密钥。

```
┌── Your identity provider ───────────────────────────────────────────────┐
│  Authorization server — you own it                                      │
│  Issues access tokens, holds the user directory, defines the scopes     │
└─────────────────────────────────────────────────────────────────────────┘
        ▲  2  user signs in, platform gets an access token
        │                                    │
        │  1  platform discovers your        │  3  every tools/call carries
        │     authorization server from      │     Authorization: Bearer <token>
        │     your app's metadata            ▼
┌── LLM platform (ChatGPT, Claude, …) ────────────────────────────────────┐
└─────────────────────────────────────────────────────────────────────────┘
                                             │
                                             ▼
┌── Your LLM App on Adobe I/O Runtime ────────────────────────────────────┐
│  Resource server — verifies the token's signature, issuer, audience,    │
│  and expiry, then enforces the auth mode you set for each action        │
│                                                                         │
│  Your handler reads the verified identity from its second argument      │
└─────────────────────────────────────────────────────────────────────────┘
```

为每个环境&#x200B;**配置了身份验证**。 **[!UICONTROL 暂存]**&#x200B;和&#x200B;**[!UICONTROL 生产]**&#x200B;保留独立的设置，因此您可以在&#x200B;**[!UICONTROL 生产]**&#x200B;上启用该配置之前，针对开发IdP租户验证该配置。

## 开始之前

- OAuth 2.1或OpenID Connect身份提供程序，它发出使用非对称算法签名的&#x200B;**JWT**&#x200B;访问令牌。 不支持不透明令牌和HMAC签名的令牌。 查看[令牌要求](/help/reference/authentication-reference.md#token-requirements)。
- 管理员访问该身份提供程序，以便您可以注册API和客户端。
- 您的应用程序至少部署到了您配置的环境一次。 部署的MCP服务器URL是您的令牌的作用域必须设置的值。

## 复制资源标识符

应用程序的&#x200B;**资源标识符**&#x200B;是其MCP服务器URL。 您的身份提供程序为此应用程序发出的每个访问令牌都必须将该确切URL命名为其受众 — 该绑定可阻止针对其他服务所生成的令牌针对您的应用程序重播。

1. 打开“应用程序详细信息”页面。
2. 滚动到&#x200B;**[!UICONTROL 测试应用程序]**。
3. 在您配置的环境中，选择&#x200B;**[!UICONTROL 复制URL]**。

![应用程序详细信息 — 复制暂存MCP服务器URL](/help/assets/guide-onboarding-agent/app-mcp-url.png)

保留此值：在下一步中，您的身份提供程序中需要它。 粘贴复制的URL，而不是重新键入。 受众检查是一个完全匹配的字符串，包括任何路径组件，因此单个字符的差异会导致每个令牌验证失败。

>[!NOTE]
>
>每个环境都有自己的MCP服务器URL，因此也有自己的受众。 分别配置&#x200B;**[!UICONTROL 暂存]**&#x200B;和&#x200B;**[!UICONTROL 生产]**。

## 配置您的身份提供程序

具体步骤因提供商而异，但每个提供商需要相同的四个要素。

1. **将应用程序注册为API（资源）。** 将其标识符（提供程序在令牌的`aud`声明中的值）设置为您复制的MCP服务器URL。 提供商为此字段设置不同的标签，通常为&#x200B;*标识符*&#x200B;或&#x200B;*受众*。 请勿使用通用值，如`api`；标识符必须对此应用程序是唯一的，或者可以为其他服务颁发的令牌针对此应用程序重播。
2. **定义要用于闸门操作的范围**，例如`orders:read`或`profile:read`。 每个有意义的权限使用一个范围，因此操作只会请求它需要什么。
3. **支持PKCE。** LLM平台在每个授权请求上发送带有`code_challenge_method=S256`的`code_challenge`，因此您的授权服务器必须支持S256 PKCE并在其元数据中通告`"code_challenge_methods_supported": ["S256"]`。
4. **允许LLM平台注册为客户端。** 支持的LLM平台会针对您的授权服务器创建自己的OAuth客户端，因此，如果您的提供商提供了动态客户端注册，请启用动态客户端注册。 否则，在平台上设置连接器期间，手动创建公共客户端并提供其客户端ID（和密码，仅当您的提供商需要机密客户端身份验证时）。 在平台文档中注册重定向URI；对于[!DNL Claude]的托管表面`https://claude.ai/api/mcp/auth_callback`。 某些平台为用户创建的每个连接器发出不同的重定向URI，其中[!DNL ChatGPT]个，因此从连接器设置屏幕中读取值并在首次登录之前注册它。 未注册的重定向URI会导致授权服务器彻底拒绝授权请求。

>[!IMPORTANT]
>
>您的身份提供程序的颁发者、JWKS、授权和令牌端点必须都可以通过公共HTTPS访问。 LLM平台和已部署的应用程序都会直接从提供商获取元数据，因此VPN或IP 允许列表背后的身份提供商无法完成登录。 供应商面前的防火墙或Web应用程序防火墙是一个常见原因，即使您的应用程序本身可以访问也会中断流量。

## 启用身份验证

1. 在左侧导航中，选择&#x200B;**[!UICONTROL 设置]**，然后打开&#x200B;**[!UICONTROL 身份验证]**&#x200B;选项卡。
2. 在&#x200B;**[!UICONTROL Workspace]**&#x200B;中，选择&#x200B;**[!UICONTROL 暂存]**&#x200B;或&#x200B;**[!UICONTROL 生产]**。
3. 打开&#x200B;**[!UICONTROL 启用身份验证]**。
4. 在&#x200B;**[!UICONTROL 核心设置]**&#x200B;下，输入：
   - **[!UICONTROL 颁发者]** — 您的身份提供程序的颁发者URL，它也是每个令牌的`iss`声明中的值。 此插件是必需的，必须是HTTPS，并且还将作为应用程序的授权服务器发布，这样LLM平台就可以发现将用户发送到何处。 每个应用程序仅支持一个标识提供程序。
   - **[!UICONTROL 支持的范围]** — 允许此应用程序的操作所需的每个范围。 镜像您在身份提供程序中定义的范围。
5. **[!UICONTROL 高级设置]**&#x200B;是可选的。 仅当您的签名密钥不在授权服务器的元数据播发位置时，才设置&#x200B;**[!UICONTROL JWKS URI]**；否则，应用程序会自动发现它们。
6. 选择&#x200B;**[!UICONTROL 保存]**。

![身份验证 — 启用身份验证并完成核心设置](/help/assets/guide-authentication/auth-core-settings.png)

有关每个字段接受的内容，请参阅[身份验证设置](/help/reference/authentication-reference.md#authentication-settings)。

## 为每个操作选择身份验证模式

当您打开&#x200B;**[!UICONTROL 启用身份验证]**&#x200B;时，当前设置为&#x200B;**[!UICONTROL 无]**&#x200B;的每个操作都会更改为&#x200B;**[!UICONTROL 必需]**。 在&#x200B;**[!UICONTROL 每个操作的配置]**&#x200B;下，查看该分配并设置每个操作所需的模式：

| 模式 | 行为 |
|------|----------|
| **[!UICONTROL 无]** | 公开。 在没有令牌的情况下可调用操作。 |
| **[!UICONTROL 必需]** | 门禁。 只能通过有效令牌调用操作，该令牌包含您为其列出的每个范围。 未经身份验证的呼叫者将被要求登录。 |
| **[!UICONTROL 可选]** | 可匿名调用，但操作也声明支持登录。 您的处理程序将根据调用决定是提供通用结果还是要求用户登录以获取个性化结果。 |

![身份验证 — 为每个操作设置身份验证模式和范围](/help/assets/guide-authentication/auth-per-action.png)

操作已设置为&#x200B;**[!UICONTROL 必需]**&#x200B;或&#x200B;**[!UICONTROL 可选]**，请保留其现有模式。

对于&#x200B;**[!UICONTROL 必需]**&#x200B;或&#x200B;**[!UICONTROL 可选]**&#x200B;操作，请添加所需的&#x200B;**[!UICONTROL 范围]**。 每个范围必须已出现在上面支持的&#x200B;**[!UICONTROL 范围]**&#x200B;中；否则，应用程序将需要它不向LLM平台通告的权限。 在解决不匹配问题之前，会阻止保存。

**[!UICONTROL 支持的作用域]**&#x200B;是此列表的权威。 如果从其中删除范围，则在您进行更改后，会立即从每个需要该范围的操作中删除该范围 — 因此，首先在该处添加一个范围，然后将其分配给某个操作。

**[!UICONTROL 所有操作都需要身份验证]**&#x200B;将每个操作设置为&#x200B;**[!UICONTROL 必需]**。 清除它会将所有操作都返回到&#x200B;**[!UICONTROL 无]**。

完成后，选择&#x200B;**[!UICONTROL 保存]**。 身份验证模式和范围更改与应用程序级别设置一起保存。

>[!IMPORTANT]
>
>关闭&#x200B;**[!UICONTROL 启用身份验证]**&#x200B;将丢弃所选环境的此每次操作配置 — 每个操作的模式和范围都已清除，不会记忆。 从全部 — **[!UICONTROL 必需]**&#x200B;重新将其打开。

>[!NOTE]
>
>将每个操作设置为&#x200B;**[!UICONTROL 无]**&#x200B;不会禁用身份验证。 在该状态下不会拒绝任何调用，但应用程序仍会将您的授权服务器通告给LLM平台，因此客户端可以为用户提供登录，该登录不会授予额外访问权限。 要使应用完全公开，请关闭&#x200B;**[!UICONTROL 启用身份验证]**&#x200B;并部署。

支持跨一个应用程序混合模式（某些操作为公共操作，其他操作为选通操作），[!DNL ChatGPT]单独应用每个操作的模式：只有选通操作会提示用户登录。

>[!IMPORTANT]
>
>[!DNL Claude]为异常。 它按连接器而不是按操作应用身份验证，因此，如果应用程序上的任何操作设置为&#x200B;**[!UICONTROL 必需]**&#x200B;或&#x200B;**[!UICONTROL 可选]**，[!DNL Claude]将要求用户登录后再使用连接器，包括设置为&#x200B;**[!UICONTROL 无]**&#x200B;的操作。 若要为[!DNL Claude]用户保留公共操作，请将其托管在单独的应用程序上。

## 部署更改

身份验证更改将在下次部署此应用程序时生效。 **再次部署应用程序**&#x200B;到您配置的环境。 请参阅[部署您的应用程序](/help/guides/deploy-your-app.md)。

您的MCP服务器URL不会发生更改，因此您已经创建的任何插件或连接器将继续工作。 它现在处于封闭状态，因此用户在下次使用它时会被要求登录。

## 读取处理程序中的身份

经过验证的身份将作为第二个参数到达您的处理程序。 无论操作的身份验证模式如何，每当调用方发送有效令牌时，它都会存在，因此&#x200B;**[!UICONTROL 可选]**&#x200B;操作可以在令牌存在时个性化其结果，而在令牌不存在时仍会返回结果。

使用`getAuthenticatedUser`读取登录用户：

```javascript
const { getAuthenticatedUser } = require('@adobe/llm-apps-runtime');

module.exports = async ({ orderId }, extra) => {
  const userId = getAuthenticatedUser(extra);

  if (!userId) {
    return { content: [{ type: 'text', text: 'Sign in to see your orders.' }] };
  }

  const order = await fetchOrderForUser(userId, orderId);

  return {
    content: [{ type: 'text', text: `Order ${order.id} is ${order.status}.` }],
    structuredContent: order
  };
};
```

您无需自己验证令牌。 对于&#x200B;**[!UICONTROL Required]**&#x200B;操作，运行时将阻止每个缺少包含您列出的范围的有效令牌的调用，因此该处理程序只为授权的调用方运行。 当您想要在权限上分支而不是依赖闸道时，例如，在&#x200B;**[!UICONTROL 可选]**&#x200B;操作中使用`hasScope`。

**[!UICONTROL 可选]**&#x200B;操作可以通过返回`extra.challengeAuth()`要求用户登录中间对话。 这仅适用于&#x200B;**[!UICONTROL 可选]**&#x200B;操作：

```javascript
module.exports = async ({ signIn }, extra) => {
  if (signIn && !extra.authInfo) {
    return extra.challengeAuth({
      error: 'invalid_token',
      errorDescription: 'Sign in to see member pricing.'
    });
  }

  return {
    content: [{
      type: 'text',
      text: extra.authInfo ? await memberDeals() : await publicDeals()
    }]
  };
};
```

决定是否从显式输入参数呈报（如`signIn`在此所做的那样），而不是通过检查用户的措辞呈报。

设置`error`以匹配您报告的条件。 当调用方没有有效的会话且需要登录时，使用`invalid_token`，如上面的示例所示；当调用方已登录但令牌缺少操作所需的范围时，使用`insufficient_scope`。 LLM平台会选择用户看到的提示的措辞，以及不同平台在该值中的不同程度，从而发送准确描述该条件的代码。

仅在您需要的身份确实丢失时进行质询，就像此处的`!extra.authInfo`检查一样。 通过登录无法满足无条件挑战的处理程序，因此要求用户在每次调用时再次进行身份验证。

>[!NOTE]
>
>在[!DNL ChatGPT]上，以这种方式登录要求用户重新连接连接器，而不是授予附加权限。 在[!DNL Claude]上，用户在任何操作运行之前登录，因此操作无需引发操作。

保留标识服务器端。 仅将构件所需的传递到`structuredContent`，并且绝不要将访问令牌放在该处 — 请参阅[自定义生成的处理程序](/help/guides/customize-handler.md)。

有关完整合同，请参阅[处理程序身份验证API](/help/reference/authentication-reference.md#handler-auth-api)。

## 测试受保护的应用程序

您的现有插件或连接器会在部署后选取更改。 要从头开始设置：

### [!DNL ChatGPT]

在&#x200B;**[!UICONTROL 新建插件]**&#x200B;对话框中，设置&#x200B;**[!UICONTROL 身份验证]**&#x200B;以匹配您配置应用程序操作的方式：

| 您应用程序的操作 | 选择 |
|--------------------|--------|
| 全部设置为&#x200B;**[!UICONTROL 无]** | **[!UICONTROL 无身份验证]** |
| 全部设置为&#x200B;**[!UICONTROL 必需]** | **[!UICONTROL OAuth]** |
| 任何其他组合 | **[!UICONTROL 混合]** |

![ChatGPT — 选择插件的身份验证模式](/help/assets/guide-authentication/chatgpt-authentication-mode.png)

**[!UICONTROL Optional]**&#x200B;操作始终接受匿名调用，因此包含该操作的应用程序永远不会处于完全限定的状态 — 即使每个操作都设置为&#x200B;**[!UICONTROL Optional]**，请选择&#x200B;**[!UICONTROL Mixed]**。 只有&#x200B;**[!UICONTROL 必需]**&#x200B;拒绝未经身份验证的呼叫者。

查看[测试ChatGPT插件](/help/guides/test-in-chatgpt.md)以了解对话框的其余部分。

### [!DNL Claude]

添加自定义连接器，然后选择&#x200B;**[!UICONTROL 连接]**&#x200B;并完成您的身份提供程序显示的登录。 没有要进行的身份验证选择 — [!DNL Claude]在任意操作被选中的情况下选通整个连接器。 查看[测试Claude连接器](/help/guides/test-in-claude.md)。

### 验证

- 平台会将您重定向到您自己的身份提供程序的登录页面。
- 受保护的操作在登录后返回特定于用户的数据。
- 注销后，受保护操作会提示您登录。
- 在[!DNL ChatGPT]上，设置为&#x200B;**[!UICONTROL 无]**&#x200B;的操作仍然会在未登录的情况下响应。 在[!DNL Claude]上，整个连接器处于封闭状态。

如果登录未开始或令牌被拒绝，请参阅[疑难解答](/help/reference/troubleshooting.md#authentication)。

## 安全指南

- 授予每个操作所需的最窄范围。 不要在所有操作中重复使用一个宽泛的范围。
- 在身份提供程序和LLM平台的连接器配置中保留客户端密钥。 切勿将其置于操作元数据、处理程序代码、小组件JavaScript或源代码控制中。
- 将令牌声明视为来自外部系统的输入。 验证从`authInfo.extra`中读取的任何内容，然后将其用于查询。
- 进行授权和身份验证。 有效的令牌可证明用户的身份，而不是证明他们可能会看到特定记录 — 请在返回数据之前检查处理程序中的所有权。
- 不记录令牌、完整的声明集或用户标识符。
- 返回安全错误。 请勿将上游标识提供程序响应或栈叠跟踪呈现给用户。
- 在&#x200B;**[!UICONTROL 生产]**&#x200B;上启用身份验证之前，针对非生产身份提供程序租户设置和验证&#x200B;**[!UICONTROL 阶段]**。

## 后续内容

- [身份验证引用](/help/reference/authentication-reference.md) — 字段、令牌要求和平台行为。
- [自定义生成的处理程序](/help/guides/customize-handler.md) — 从处理程序调用受保护的上游API。
- [部署您的应用程序](/help/guides/deploy-your-app.md)。
