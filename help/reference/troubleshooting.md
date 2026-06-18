---
title: Adobe LLM应用程序疑难解答
description: 解决构建、部署和测试Adobe LLM应用程序时出现的常见问题。
source-git-commit: 98d5590c927bf8ffad54061ee027664452c129c1
workflow-type: tm+mt
source-wordcount: '439'
ht-degree: 0%

---


# 疑难解答 {#troubleshooting}

>[!IMPORTANT]
>
>**免责声明：**&#x200B;这是[!DNL LLM Apps]的测试版本。 此处显示的功能、工作流和UI不一定表示应用程序或产品的最终状态。

## 常见问题

| 症状 | 可能的原因 | 尝试什么 |
|---------|----------------|-------------|
| 应用程序未显示在LLM平台中 | 您的LLM平台订阅不支持自定义MCP应用程序，或者未启用开发人员模式 | 验证您的计划是否支持自定义MCP应用程序。 在&#x200B;**设置→应用程序→高级设置中启用开发人员模式** |
| LLM平台中出现“无法连接”错误 | MCP服务器URL不正确或部署失败 | 从“应用程序详细信息”页面中双击该URL。 检查部署历史记录是否有故障 |
| 未调用操作 | LLM平台无法将用户的问题与您的操作相匹配 | 使用`@YourApp`显式调用它。 改进操作描述以帮助模型匹配意图 |
| 构件未呈现 | EDS小组件URL或CSP域配置错误 | 在“创建操作”对话框中验证脚本URL和构件嵌入URL。 检查CSP资源和连接域是否包含您的EDS源 |
| 空响应或错误响应 | 处理程序存在错误或缺失 | 首先使用`npm start`在本地测试。 查看[本地开发](/help/reference/development.md#local-development) |
| 小组件已加载，但不显示数据 | `structuredContent`形状与块期望的形状不匹配 | 在块的`decorate`函数中记录`bridge.toolResult`，并与处理程序输出进行比较 |
| 在“克隆并构建”时部署失败 | 存储库中出现`npm install`或webpack生成错误 | 在本地运行`npm install && npm run build`以重现错误 |
| 部署在“收集凭据”失败 | 存储库未链接或Developer Console项目配置错误 | 验证是否在“应用程序详细信息”设置页面上链接了存储库 |
| 加载构件时出现CORS错误 | EDS站点缺少`access-control-allow-origin`标头 | 通过`admin.hlx.page`配置CORS标头 |
| 保存CORS标头时，HTTP标头编辑器返回`404 Error updating config: config not found` | 站点配置缺少`headers`节 | 请参阅下面的[初始化EDS站点配置标头部分](#initialize-the-eds-site-config-headers-section) |
| 构件在预览中呈现，但在LLM平台中则不呈现 | 块在预览模式下回退到示例数据，但实时数据失败 | 使用MCP检查器或CURL对实际`structuredContent`进行测试 |

## 初始化EDS站点配置标头部分

如果HTTP标头编辑器返回`404 Error updating config: config not found`，则站点配置缺少`headers`节。 手动修复：

1. 转到[tools.aem.live/tools/headers-edit/index.html](https://tools.aem.live/tools/headers-edit/index.html)，输入您的组织和网站，然后单击&#x200B;**[!UICONTROL 提取]**。
2. 打开浏览器DevTools（“网络”选项卡），并从Fetch请求复制`x-auth-token`标头的值。
3. 检索当前站点配置：

   ```bash
   curl -H "x-auth-token: $TOKEN" \
     https://admin.hlx.page/config/<your-github-org>/sites/<your-eds-repo>.json > config.json
   ```

4. 打开`config.json`并将`"headers": {}`添加到JSON对象。
5. 将更新的配置发布回：

   ```bash
   curl -X POST \
     -H "x-auth-token: $TOKEN" \
     -H "Content-Type: application/json" \
     -d @config.json \
     "https://admin.hlx.page/config/<your-github-org>/sites/<your-eds-repo>.json"
   ```

6. 重新加载标头编辑器并正常保存`Access-Control-Allow-Origin`标头。

