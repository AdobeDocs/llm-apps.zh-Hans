---
title: 使用您自己的身份提供程序验证最终用户
description: 为您的Adobe LLM应用程序启用最终用户身份验证，以便受支持的LLM平台在调用受保护的操作之前，将用户与您的身份提供程序登录。
source-git-commit: 0f9dd0a5b1008e183eb8ab5a4c2eae34a78923bb
workflow-type: tm+mt
source-wordcount: '2136'
ht-degree: 0%
---

# 使用您自己的身份提供程序验证最终用户 {#authentication}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps]当前在Beta中。
>
>此处显示的功能、工作流和UI不一定表示产品的最终状态。 要加入Beta，请发送电子邮件至llm-apps-beta@adobe.com 。

默认情况下，应用程序中的每个操作都是公开的。 任何带有MCP服务器URL的LLM平台都可以调用这些操作，并且您的处理程序无法识别最终用户。

当操作需要知道哪个最终用户请求时（例如，返回其订单、权利或帐户详细信息），启用身份验证。 LLM平台使用&#x200B;**您的**&#x200B;身份提供程序(IdP)登录用户，通过每次调用发送生成的访问令牌，并且您的处理程序接收已验证的身份。

**在本指南中：**&#x200B;了解如何连接您的身份提供程序并选择需要登录的操作。 在您的处理程序中使用经过验证的身份，部署应用程序，并测试登录和受保护的操作。

身份验证设置是一项高级任务。 首先完成[基础结构设置](/help/guides/create-app.md)、[创建操作](/help/guides/creating-actions.md)和[部署您的应用程序](/help/guides/deploy-your-app.md)。

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

为每个环境&#x200B;**配置身份验证**。 **[!UICONTROL 暂存]**&#x200B;和&#x200B;**[!UICONTROL 生产]**&#x200B;保持独立的设置。 在&#x200B;**[!UICONTROL 生产]**&#x200B;上启用身份验证之前，请验证针对开发IdP租户的配置。

## 开始之前

- OAuth 2.1或OpenID Connect身份提供程序，它发出使用非对称算法签名的&#x200B;**JWT**&#x200B;访问令牌。 不支持不透明令牌和HMAC签名的令牌。 查看[令牌要求](/help/reference/authentication-reference.md#token-requirements)。
- 管理员访问该身份提供程序以注册API和客户端。
- 您的应用程序至少部署到了您配置的环境一次。 部署的MCP服务器URL是您的令牌的作用域必须设置的值。

## 复制资源标识符

应用程序的&#x200B;**资源标识符**&#x200B;是其MCP服务器URL。 您的身份提供程序为该应用程序颁发的每个访问令牌都必须将该确切URL命名为其受众。 此受众绑定禁止使用为其他服务颁发的令牌。

1. 打开“应用程序详细信息”页面。
2. 滚动到&#x200B;**[!UICONTROL 测试应用程序]**。
3. 在您配置的环境中，选择&#x200B;**[!UICONTROL 复制URL]**。

![应用程序详细信息 — 复制暂存MCP服务器URL](/help/assets/guide-onboarding-agent/app-mcp-url.png)

在下一步中，保留身份提供程序配置的复制的MCP服务器URL。 粘贴URL而不是重新键入。 受众检查需要完全匹配的字符串，包括路径。 单个字符的差异会导致每个令牌的验证失败。

>[!NOTE]
>
>每个环境都有自己的MCP服务器URL，因此也有自己的受众。 分别配置&#x200B;**[!UICONTROL 暂存]**&#x200B;和&#x200B;**[!UICONTROL 生产]**。

## 配置您的身份提供程序

步骤因提供商而异。 配置以下四个项目。

1. **将应用程序注册为API（资源）。** 将其标识符（提供程序在令牌的`aud`声明中的值）设置为复制的MCP服务器URL。 提供商通常为此字段&#x200B;*标识符*&#x200B;或&#x200B;*受众*&#x200B;设置标签。 使用对应用程序唯一的标识符，而不是通用值（如`api`），以防止使用为其他服务颁发的令牌。
2. **定义保护操作的范围**，例如`orders:read`或`profile:read`。 为每个有意义的权限使用一个范围，以便每个操作仅请求所需的访问权限。
3. **支持PKCE。** LLM平台在每个授权请求上发送带有`code_challenge_method=S256`的`code_challenge`，因此您的授权服务器必须支持S256 PKCE并在其元数据中通告`"code_challenge_methods_supported": ["S256"]`。
4. **选择LLM平台标识其OAuth客户端的方式。** 如果您的提供商支持平台的要求、动态客户端注册(DCR)或预注册的OAuth客户端，请使用客户端ID元数据文档(CIMD)。 仅在您选择的方法需要客户端ID和密码时才提供它们。 注册平台记录或显示的确切重定向URI；对于[!DNL Claude]的托管表面`https://claude.ai/api/mcp/auth_callback`。 [!DNL ChatGPT]可能使用稳定或连接器特定的回调，具体取决于提供商的广告功能，因此请复制其显示的URI而不是猜测。 请参阅当前[ChatGPT身份验证文档](https://developers.openai.com/plugins/build/auth)和[克劳德身份验证文档](https://claude.com/docs/connectors/building/authentication)。

>[!IMPORTANT]
>
>您的身份提供程序的颁发者、JWKS、授权和令牌端点必须都可以通过公共HTTPS访问。 LLM平台和已部署的应用程序都会直接从提供商获取元数据。 阻止这些请求的VPN、IP允许列表、防火墙或Web应用程序防火墙可阻止登录，即使应用程序本身仍可访问。

## 启用身份验证

1. 在左侧导航中，选择&#x200B;**[!UICONTROL 设置]**，然后打开&#x200B;**[!UICONTROL 身份验证]**&#x200B;选项卡。
2. 在&#x200B;**[!UICONTROL Workspace]**&#x200B;中，选择&#x200B;**[!UICONTROL 暂存]**&#x200B;或&#x200B;**[!UICONTROL 生产]**。
3. 打开&#x200B;**[!UICONTROL 启用身份验证]**。
4. 在&#x200B;**[!UICONTROL 核心设置]**&#x200B;下，输入：
   - **[!UICONTROL 颁发者]** — 您的身份提供程序的HTTPS颁发者URL，也是它在每个令牌的`iss`声明中的值。 平台需要此URL并将颁发者发布为应用程序的LLM客户端发现授权服务器。 每个应用程序支持一个身份提供程序。
   - **[!UICONTROL 支持的范围]** — 允许此应用程序的操作所需的每个范围。 镜像您在身份提供程序中定义的范围。
5. **[!UICONTROL 高级设置]**&#x200B;是可选的。 仅当您的签名密钥不在授权服务器的元数据播发位置时，才设置&#x200B;**[!UICONTROL JWKS URI]**；否则，应用程序会自动发现它们。
6. 选择&#x200B;**[!UICONTROL 保存]**。

![身份验证 — 启用身份验证并完成核心设置](/help/assets/guide-authentication/auth-core-settings.png)

有关字段要求，请参阅[身份验证设置](/help/reference/authentication-reference.md#authentication-settings)。

## 为每个操作选择身份验证模式

当您打开&#x200B;**[!UICONTROL 启用身份验证]**&#x200B;时，当前设置为&#x200B;**[!UICONTROL 无]**&#x200B;的每个操作都会更改为&#x200B;**[!UICONTROL 必需]**。 在&#x200B;**[!UICONTROL 每个操作的配置]**&#x200B;下，查看该分配并设置每个操作所需的模式：

| 模式 | 行为 |
|------|----------|
| **[!UICONTROL 无]** | 公开。 呼叫者不需要令牌。 |
| **[!UICONTROL 必需]** | 受保护。 呼叫者需要一个包含每个已列出范围的有效令牌。 运行时会向未经身份验证的呼叫者发起登录挑战。 |
| **[!UICONTROL 可选]** | 可匿名调用，但操作也声明支持登录。 您的处理程序将根据调用决定是提供通用结果还是要求用户登录以获取个性化结果。 |

![身份验证 — 为每个操作设置身份验证模式和范围](/help/assets/guide-authentication/auth-per-action.png)

操作已设置为&#x200B;**[!UICONTROL 必需]**&#x200B;或&#x200B;**[!UICONTROL 可选]**，请保留其现有模式。

对于&#x200B;**[!UICONTROL 必需]**&#x200B;或&#x200B;**[!UICONTROL 可选]**&#x200B;操作，请添加所需的&#x200B;**[!UICONTROL 范围]**。 每个范围必须已出现在受支持的&#x200B;**[!UICONTROL 范围]**&#x200B;中，以便应用程序向LLM平台通告每个所需的权限。 在解决任何范围不匹配之前，平台会阻止保存。

**[!UICONTROL 支持的作用域]**&#x200B;控制可用的作用域。 从该列表中删除作用域会立即从需要它的每个操作中删除该作用域。 在将范围分配给操作之前，将每个范围添加到列表中。

选择&#x200B;**[!UICONTROL 需要对所有操作进行身份验证]**&#x200B;将&#x200B;**[!UICONTROL 无]**&#x200B;操作更改为&#x200B;**[!UICONTROL 必需]**，但保留已设置为&#x200B;**[!UICONTROL 可选]**&#x200B;的操作。 若要让每个操作都需要登录，请单独将任何剩余的&#x200B;**[!UICONTROL 可选]**&#x200B;行更改为&#x200B;**[!UICONTROL 必需]**。 清除该复选框会将所有操作都返回到&#x200B;**[!UICONTROL 无]**。

完成后，选择&#x200B;**[!UICONTROL 保存]**。 身份验证模式和范围更改与应用程序级别设置一起保存。

>[!IMPORTANT]
>
>关闭&#x200B;**[!UICONTROL 启用身份验证]**&#x200B;将清除所选环境的每个操作的身份验证模式和作用域。 平台不保留这些设置。 再次打开身份验证会将所有操作设置为&#x200B;**[!UICONTROL 必需]**。

>[!NOTE]
>
>将每个操作设置为&#x200B;**[!UICONTROL 无]**&#x200B;不会禁用身份验证。 无需在该状态下登录任何操作，但应用程序仍会向LLM平台通告您的授权服务器。 客户端可以提供不授予额外访问权限的登录。 要使应用完全公开，请关闭&#x200B;**[!UICONTROL 启用身份验证]**&#x200B;并部署。

[!DNL ChatGPT]支持一个应用程序中的混合身份验证模式，并单独应用每个操作的模式。 公共操作不会提示登录；受保护操作会根据需要进行提示。

[!DNL Claude]还通过&#x200B;**延迟身份验证**&#x200B;支持混合应用。 如果在连接器设置期间选择了&#x200B;**[!UICONTROL 需要时登录]**，则公共操作仍可匿名使用，并且在调用&#x200B;**[!UICONTROL Required]**&#x200B;操作时，平台会要求登录。 其操作全部为&#x200B;**[!UICONTROL 必需]**&#x200B;的应用程序需要先登录，然后才能使用。 请参阅[LLM平台行为](/help/reference/authentication-reference.md#platform-behavior)，以了解所需操作和请求登录的可选处理程序之间的区别。

>[!NOTE]
>
>当前运行时支持此混合身份验证行为，已针对版本1.1.5进行验证。 部署较旧的应用程序可能需要运行时更新和重新部署。 某些UI构建仍显示旧版警告，指出克劳德需要对整个连接器进行身份验证。 验证已部署的运行时并使用克劳德当前的&#x200B;**[!UICONTROL 在需要时登录]**&#x200B;选项，而不是依赖警告。

## 部署更改

身份验证更改将在下次部署此应用程序时生效。 **再次部署应用程序**&#x200B;到您配置的环境。 请参阅[部署您的应用程序](/help/guides/deploy-your-app.md)。

您的MCP服务器URL未更改。 刷新连接的元数据，并验证其身份验证设置是否与应用程序的新配置匹配。 当某个平台的身份验证方法发生更改时，该平台可能会要求您重新连接或重新创建连接。

## 读取处理程序中的身份

无论操作的身份验证模式如何，每当调用方发送有效令牌时，经过验证的标识都会作为第二个参数到达您的处理程序。 **[!UICONTROL 可选]**&#x200B;操作可以使用该标识在可用时个性化结果，否则返回匿名结果。

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

运行时将为您验证令牌。 对于&#x200B;**[!UICONTROL 必需]**&#x200B;操作，它会阻止每个在列出的范围中缺少有效令牌的调用。 该处理程序仅针对授权的调用方运行。 使用`hasScope`根据权限选择处理程序行为 — 例如，在&#x200B;**[!UICONTROL 可选]**&#x200B;操作中。

若要在对话期间请求登录，请从处理程序返回`extra.challengeAuth()`。 辅助函数仅适用于&#x200B;**[!UICONTROL 可选]**&#x200B;操作：

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

使用显式输入参数（如本示例中的`signIn`）来决定何时请求身份验证。 请勿从用户的措辞推断该决策。

设置`error`以匹配报告的条件。 当调用方没有有效的会话且需要登录时，使用`invalid_token`，如上面的示例所示。 当调用方登录但令牌缺少所需的范围时，请使用`insufficient_scope`。 每个LLM平台都选择提示措辞以及如何响应错误代码。 发送准确描述情况的代码。

仅当缺少所需的身份时才请求身份验证，在此示例中为`!extra.authInfo`检查。 登录不能解决无条件质询：处理程序在每次调用时再次请求身份验证。

>[!NOTE]
>
>`extra.challengeAuth()`返回[!DNL ChatGPT]的工具结果质询。 相反，[!DNL Claude]需要在处理程序运行之前进行HTTP身份验证质询。 对于需要跨支持的平台登录的操作，请使用&#x200B;**[!UICONTROL 必需的]**&#x200B;操作。 如果需要，为公共体验保留单独的&#x200B;**[!UICONTROL 无]**&#x200B;操作。

保留标识服务器端。 仅将小组件需要的数据传递到`structuredContent`。 绝不要在`structuredContent`中包含访问令牌。 请参阅[自定义生成的处理程序](/help/guides/customize-handler.md)。

有关完整合同，请参阅[处理程序身份验证API](/help/reference/authentication-reference.md#handler-auth-api)。

## 测试受保护的应用程序

您的现有插件或连接器在部署后使用更新的配置。 要创建新连接，请按照适用于您平台的说明操作：

### [!DNL ChatGPT]

在&#x200B;**[!UICONTROL 新建插件]**&#x200B;对话框中，设置&#x200B;**[!UICONTROL 身份验证]**&#x200B;以匹配您配置应用程序操作的方式：

| 您应用程序的操作 | 选择 |
|--------------------|--------|
| 全部设置为&#x200B;**[!UICONTROL 无]** | **[!UICONTROL 无身份验证]** |
| 全部设置为&#x200B;**[!UICONTROL 必需]** | **[!UICONTROL OAuth]** |
| 任何其他组合 | **[!UICONTROL 混合]** |

**[!UICONTROL Optional]**&#x200B;操作始终接受匿名调用，因此包含该操作的应用程序永远不会处于完全限定的状态 — 即使每个操作都设置为&#x200B;**[!UICONTROL Optional]**，请选择&#x200B;**[!UICONTROL Mixed]**。 只有&#x200B;**[!UICONTROL 必需]**&#x200B;拒绝未经身份验证的呼叫者。

查看[测试ChatGPT插件](/help/guides/test-in-chatgpt.md)以了解对话框的其余部分。

### [!DNL Claude]

添加自定义连接器并查看其身份验证设置。 对于公共/受保护的混合应用，请选择&#x200B;**[!UICONTROL 需要时登录]**（可用时）。 若要在连接器使用前请求登录，请选择&#x200B;**[!UICONTROL 立即登录]**。 该对话框因帐户或组织而异；请参阅[测试Claude连接器](/help/guides/test-in-claude.md)。

### 验证

- 平台会将您重定向到您自己的身份提供程序的登录页面。
- 受保护的操作在登录后返回特定于用户的数据。
- 注销后，受保护操作会提示您登录。
- 在混合应用中，设置为&#x200B;**[!UICONTROL 无]**&#x200B;的操作仍会在未登录的情况下响应。 在[!DNL Claude]中，通过&#x200B;**[!UICONTROL 在需要时登录]**&#x200B;来验证公共操作。
- 登录后，重试受保护的操作，并确认运行时强制实施预期的权限。

如果登录未开始或令牌被拒绝，请参阅[疑难解答](/help/reference/troubleshooting.md#authentication)。

## 安全指南

- 授予每个操作所需的最窄范围。 不要在所有操作中重复使用一个宽泛的范围。
- 在身份提供程序和LLM平台的连接器配置中保留客户端密钥。 切勿将其置于操作元数据、处理程序代码、小组件JavaScript或源代码控制中。
- 将令牌声明视为来自外部系统的输入。 在查询中使用来自`authInfo.extra`的值之前，请验证这些值。
- 进行授权和身份验证。 有效的令牌可证明用户的身份，而不是读取特定记录的权限。 在返回数据之前，请检查处理程序中的所有权。
- 不记录令牌、完整的声明集或用户标识符。
- 返回安全错误。 请勿将上游标识提供程序响应或栈叠跟踪呈现给用户。
- 在&#x200B;**[!UICONTROL 生产]**&#x200B;上启用身份验证之前，针对非生产身份提供程序租户设置和验证&#x200B;**[!UICONTROL 阶段]**。

## 后续内容

- [身份验证引用](/help/reference/authentication-reference.md) — 字段、令牌要求和平台行为。
- [自定义生成的处理程序](/help/guides/customize-handler.md) — 从处理程序调用受保护的上游API。
- [部署您的应用程序](/help/guides/deploy-your-app.md)。
