---
title: 身份验证引用
description: Adobe LLM应用程序中用于最终用户身份验证的字段定义、令牌要求、发现端点、处理程序API和LLM平台行为。
source-git-commit: 0f9dd0a5b1008e183eb8ab5a4c2eae34a78923bb
workflow-type: tm+mt
source-wordcount: '1428'
ht-degree: 2%
---

# 身份验证引用 {#authentication-reference}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps]当前在Beta中。
>
>此处显示的功能、工作流和UI不一定表示产品的最终状态。 要加入Beta，请发送电子邮件至llm-apps-beta@adobe.com 。

此页用于查找验证字段和协定。 有关设置说明，请参阅[使用您自己的身份提供程序对最终用户进行身份验证](/help/guides/authentication.md)。

## 身份验证设置 {#authentication-settings}

打开&#x200B;**[!UICONTROL 设置]** > **[!UICONTROL 身份验证]**。 每个环境都存储自己的设置。 使用&#x200B;**[!UICONTROL Workspace]**&#x200B;选取器选择要编辑的环境；保存设置不会影响其他环境。

| 字段 | 必需 | 描述 |
|-------|----------|-------------|
| **[!UICONTROL Workspace]** | — | 身份验证设置的环境： **[!UICONTROL 暂存]**&#x200B;或&#x200B;**[!UICONTROL 生产]** |
| **[!UICONTROL 启用身份验证]** | — | 主开关。 关闭时，无论验证模式如何，每个操作都是公开的 |
| **[!UICONTROL 颁发者]** | 是 | 您的身份提供程序的颁发者URL和预期的`iss`声明。 必须为HTTPS。 还作为此应用程序的授权服务器发布。 每个应用程序一个身份提供程序 |
| **[!UICONTROL 支持的范围]** | 否 | 此应用程序的操作可能需要的整套范围。 作为应用程序支持的范围发布到LLM平台 |
| **[!UICONTROL JWKS URI]** | 否 | 高级。 签名密钥集的HTTPS URL。 仅当它与授权服务器的元数据播发内容不同时才需要 |

### 验证规则

| 规则 | 效果 |
|------|--------|
| **[!UICONTROL 颁发者]**&#x200B;为空，而&#x200B;**[!UICONTROL 启用身份验证]**&#x200B;处于打开状态 | 保存被阻止 |
| **[!UICONTROL 颁发者]**&#x200B;或&#x200B;**[!UICONTROL JWKS URI]**&#x200B;不是HTTPS URL | 保存被阻止 |
| 操作需要&#x200B;**[!UICONTROL 支持的作用域]**&#x200B;中缺少作用域 | 在添加作用域或从操作中删除它之前，保存操作将被阻止 |
| 范围已从支持的&#x200B;**[!UICONTROL 范围]**&#x200B;中删除 | 它将从需要它的每个操作中立即删除，而无需等待保存 |
| **[!UICONTROL 支持的范围]**&#x200B;为空 | 无法授予任何范围，因此将删除操作中已有的任何范围。 在这种情况下，不显示警告 |
| `offline_access`在&#x200B;**[!UICONTROL 支持的作用域]**&#x200B;中或在操作中列出 | 在部署应用程序时删除，而不考虑大小写或周围是否有空格，这样设置页面就可以显示已部署应用程序没有的作用范围。 `offline_access`向授权服务器请求刷新令牌，而不是授予对此应用的访问权限，因此它不是此应用通告的范围。 您无需将其列出 — LLM平台直接从您的授权服务器请求它 |

**[!UICONTROL 颁发者]**&#x200B;上的尾随斜杠已标准化。 `iss`比较允许存在差异，因此始终包含尾随斜杠的提供程序中的令牌仍会进行验证。

## 身份验证模式 {#auth-modes}

在&#x200B;**[!UICONTROL 每个操作配置]**&#x200B;下为每个操作设置。

| 模式 | 需要令牌 | 处理程序接收标识 | 在平台上广告为 |
|------|----------------|---------------------------|-------------------------------|
| **[!UICONTROL 无]** | 否 | 仅当调用方提供有效令牌时 | `noauth` |
| **[!UICONTROL 必需]** | 是，每个列出的范围都有 | 始终 | `oauth2` |
| **[!UICONTROL 可选]** | 否 | 当存在有效令牌时 | `noauth` 和 `oauth2` |

**[!UICONTROL Required]**&#x200B;操作的处理程序从不在没有有效、范围正确的令牌的情况下运行。 **[!UICONTROL Optional]**&#x200B;操作的处理程序始终运行，并且可能会要求使用`extra.challengeAuth()`本身登录。

因此，**[!UICONTROL 必需]**&#x200B;是唯一拒绝未经身份验证的呼叫者的模式。 仅当每个操作都是&#x200B;**[!UICONTROL 必需的]**&#x200B;时，应用才会拒绝所有匿名调用。 如果任何操作为&#x200B;**[!UICONTROL None]**&#x200B;或&#x200B;**[!UICONTROL Optional]**，则应用程序将使用混合身份验证，因为至少有一个操作接受匿名调用。

只有在&#x200B;**[!UICONTROL 启用身份验证]**&#x200B;打开时，身份验证模式才会生效。 所做的更改将在应用程序下次部署时生效。

更改身份验证交换机也会重写每个操作的模式：

| 切换更改 | 对每次操作模式的影响 |
|---------------|----------------------------|
| 从关闭到打开 | 每个&#x200B;**[!UICONTROL 无]**&#x200B;操作都变为&#x200B;**[!UICONTROL 必需]**。 操作已&#x200B;**[!UICONTROL 必需]**&#x200B;或&#x200B;**[!UICONTROL 可选]**&#x200B;保留其模式 |
| 开启到关闭 | 将为该环境清除每个操作的模式和范围。 如果再次打开开关，配置将不会恢复 |

切换&#x200B;**[!UICONTROL Workspace]**&#x200B;从不重写模式。 选取器加载其他环境的已保存配置而不更改它。

如果启用&#x200B;**[!UICONTROL 身份验证]**，并且每个操作都设置为&#x200B;**[!UICONTROL 无]**，则应用不需要身份验证，但仍会发布其授权服务器以进行发现。 要使应用程序完全公开，请关闭开关。

应用程序可以对各个操作使用不同的身份验证模式。 查看[LLM平台行为](/help/reference/authentication-reference.md#platform-behavior)，了解每个平台如何应用它们。

## 令牌要求 {#token-requirements}

您的身份提供程序必须颁发满足以下所有要求的访问令牌。 运行时将未通过任何检查的令牌视为不存在：调用方未经过身份验证，**[!UICONTROL Required]**&#x200B;操作会要求调用方登录。

| 要求 | 详细信息 |
|-------------|--------|
| 格式化 | 已签署JWT。 不支持不透明令牌 |
| 签名算法 | `RS256`、`RS384`、`RS512`、`ES256`、`ES384`、`ES512`、`PS256`、`PS384`或`PS512`。 HMAC算法（如`HS256`）被拒绝 |
| `iss` | 必须匹配&#x200B;**[!UICONTROL 颁发者]** |
| `aud` | 必须包含应用程序的资源标识符 — 该环境的MCP服务器URL |
| `exp` | 必定是未来的 |
| `scope` 或 `scp` | 运行时读取以空格分隔的`scope`字符串，或回退到`scp`中的字符串数组。 对于根据每个操作要求检查的范围，请使用这些形状之一 |
| `sub` | 您的处理程序通过`getAuthenticatedUser`读取的用户标识符 |
| 传输 | `Authorization: Bearer <token>`请求标头 |

您的提供商包括的任何其他平面声明（例如`tenant`或`email`）都会传递给您的处理程序。 嵌套对象将被删除，长字符串值将被截断。

## 身份提供程序发现 {#discovery}

您的应用程序发布自己的[RFC 9728](https://datatracker.ietf.org/doc/html/rfc9728)受保护资源元数据，以便LLM平台能够找到您的授权服务器。 您不需要创建、托管或配置受保护的资源元数据。

您的身份提供程序必须提供以下发现信息：

| 要求 | 详细信息 |
|-------------|--------|
| 授权服务器元数据 | 授权服务器必须发布LLM客户端的发现元数据。 除非设置了&#x200B;**[!UICONTROL JWKS URI]**，否则此应用的运行时将专门查找[RFC 8414](https://datatracker.ietf.org/doc/html/rfc8414) OAuth授权服务器元数据以找到`jwks_uri`；它不会回退到OIDC发现路径 |
| 具有路径的颁发者 | 知名区段位于路径之前，而不是路径之后。 位于`https://auth.example.com/oauth2/default`的颁发者在`https://auth.example.com/.well-known/oauth-authorization-server/oauth2/default`提供其元数据 |
| 直接签名密钥URL | 当密钥托管在其他位置或提供程序仅公开OIDC发现时，请设置&#x200B;**[!UICONTROL JWKS URI]**。 这会绕过密钥的运行时发现，而不是LLM客户端所需的发现 |

## 处理程序身份验证API {#handler-auth-api}

包`@adobe/llm-apps-runtime`导出以下帮助程序。 每个辅助函数都接受`extra`，这是处理程序所接收的第二个参数。

| 辅助函数 | 返回 |
|--------|---------|
| `getAuthenticatedUser(extra)` | 登录用户的`sub`声明，或呼叫未经身份验证时的`undefined` |
| `hasScope(extra, scope)` | 调用方的令牌携带`scope`时`true` |

验证令牌信息在`extra.authInfo`中可用，对于未经身份验证的调用，该信息为`undefined`。

| 属性 | 描述 |
|----------|-------------|
| `authInfo.token` | 原始持有者令牌。 请勿将其记录或返回给客户端 |
| `authInfo.clientId` | `client_id`或`azp`声明，或`unknown` |
| `authInfo.scopes` | 已授予的作用域数组 |
| `authInfo.expiresAt` | 令牌过期，如`exp`声明 |
| `authInfo.resource` | 针对令牌进行验证的应用程序的资源标识符 |
| `authInfo.extra` | `sub`加上您的身份提供程序包含的任何其他平面声明 |

`extra.challengeAuth(options)`仅可用于&#x200B;**[!UICONTROL 可选]**&#x200B;操作。 从您的处理程序返回其结果，要求用户登录而不是返回内容。

辅助函数返回[!DNL ChatGPT]的工具结果质询。 [!DNL Claude]需要传输级别的HTTP质询。 运行时提供对&#x200B;**[!UICONTROL 必需的]**&#x200B;操作的挑战，但不通过可选处理程序帮助程序。

| 选项 | 描述 |
|--------|-------------|
| `error` | [RFC 6750](https://datatracker.ietf.org/doc/html/rfc6750)持有者错误代码： `invalid_token`、`insufficient_scope`或`invalid_request`。 默认为`insufficient_scope` |
| `errorDescription` | 向用户显示的消息。 默认使用通用登录提示 |
| `scope` | 要请求的以空格分隔的作用域。 省略它以使平台回退到应用程序支持的范围 |

>[!IMPORTANT]
>
>始终显式设置`error`。 `invalid_token`用于没有有效会话的调用方，而`insufficient_scope`仅用于令牌有效但缺少所需范围的调用方。 运行时将值传递给LLM平台，后者选择登录提示的措辞。 发送准确描述条件的代码，而不是使用首选提示发送的代码。

## LLM平台行为 {#platform-behavior}

两个平台都支持同一应用程序中的公共操作和受保护操作，但它们的登录触发器有所不同。

| 行为 | [!DNL ChatGPT] | [!DNL Claude] |
|----------|----------------|---------------|
| 混合身份验证 | 公共操作以匿名方式工作；**[!UICONTROL 必需]**&#x200B;操作请求登录 | 通过&#x200B;**[!UICONTROL 在需要时登录]**，公共操作以匿名方式工作，并且受保护呼叫开始登录 |
| 具有每个操作&#x200B;**[!UICONTROL 的应用程序]** | 配置OAuth身份验证 | 使用前需要身份验证 |
| 连接器设置 | 对公共应用使用&#x200B;**[!UICONTROL 无身份验证]**，当每个操作为&#x200B;**[!UICONTROL 必需]**&#x200B;时&#x200B;**[!UICONTROL OAuth]**，否则使用&#x200B;**[!UICONTROL 混合]**，其中提供这些选项 | 查看检测到的设置。 选择&#x200B;**[!UICONTROL 需要时登录]**&#x200B;用于混合应用，或&#x200B;**[!UICONTROL 立即登录]**&#x200B;用于前端登录 |
| 缺少标识或所需的范围 | 运行时返回平台的身份验证质询 | 运行时返回HTTP 401以查找缺少/无效的标识，或返回HTTP 403以查找范围不足的质询，查找缺少所需范围的有效令牌 |
| 可选处理程序的`extra.challengeAuth()` | 可以开始重新身份验证 | 成功的HTTP工具结果响应不会启动登录。 对可移植登录门使用&#x200B;**[!UICONTROL 必需的]**&#x200B;操作 |

有关当前客户端行为，请参阅[Claude延迟身份验证](https://claude.com/docs/connectors/building/lazy-authentication)和[ChatGPT身份验证](https://developers.openai.com/plugins/build/auth)。 连接器UI和策略可能因帐户和组织而异。


## 相关

- [使用您自己的身份提供程序验证最终用户](/help/guides/authentication.md)
- [操作和构件字段](/help/reference/reference-docs.md)
- [疑难解答](/help/reference/troubleshooting.md#authentication)
