---
title: 为必应自定义搜索配置托管 UI | Microsoft Docs
titleSuffix: Azure Cognitive Services
description: 根据本文的说明为必应自定义搜索配置和集成托管 UI。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: aahi
ms.custom: devx-track-javascript
ms.openlocfilehash: 857195e0befb95035572b5b52a01d151b332518e
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/29/2020
ms.locfileid: "87406344"
---
# <a name="configure-your-hosted-ui-experience"></a>配置托管 UI 体验

必应自定义搜索提供的托管 UI 可以轻松地作为 JavaScript 代码片段集成到网页和 Web 应用程序中。 可以使用必应自定义搜索门户配置 UI 的布局、颜色和搜索选项。



## <a name="configure-the-custom-hosted-ui"></a>配置自定义托管 UI

若要为 Web 应用程序配置托管 UI，请执行以下步骤。 在你进行更改时，右侧的窗格会提供 UI 的预览。 显示的搜索结果不是实例的实际结果。

1. 登录必应自定义搜索[门户](https://customsearch.ai)。  
  
2. 选择必应自定义搜索实例。

3. 单击“托管 UI”选项卡。  
  
4. 选择布局。

    - 搜索栏和结果（默认值）：在其下方显示搜索结果的搜索框。
    - 仅结果：仅显示搜索结果，不显示搜索框。 使用此布局时，必须提供搜索查询 (`&q=<query string>`)。 将查询参数添加到 JavaScript 代码片段或 HTML 终结点链接中的请求 URL。
    - 弹出式窗口：提供搜索框并在滑动叠加中显示搜索结果。

5. 选择颜色主题。 可以通过单击“自定义主题”来自定义适合应用程序的颜色。**** 若要更改颜色，请输入颜色的 RGB HEX 值（例如 `#366eb8`），或者单击颜色预览。

   可以在门户右侧预览所做的更改。 单击“重置为默认值”**** 就会将所做的更改还原为所选主题的默认颜色。

   > [!NOTE]
   > 选择颜色时需考虑到辅助功能。

6. 在“其他配置”下，根据应用提供相应的值****。 这些设置是可选的。 若要查看应用或删除设置后的效果，请查看右侧的预览窗格。 可用配置有：  

7. 输入搜索订阅密钥或从下拉列表中选择一个。 下拉列表的填充内容为帐户的 Azure 帐户订阅的密钥。 请参阅[认知服务 API 帐户](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)。  

8. 如果启用了自动建议，请输入自动建议订阅密钥，或从下拉列表进行选择。 下拉列表的填充内容为帐户的 Azure 帐户订阅的密钥。 自定义自动建议需要特定的订阅层，请参阅[定价](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/)。

[!INCLUDE [publish or revert](./includes/publish-revert.md)]

## <a name="consume-custom-ui"></a>使用自定义 UI

要使用托管 UI，请执行以下任一操作： 

- 在网页中包含该脚本  
  
  ```html
  <html>
      <body>
          <script type="text/javascript" 
              id="bcs_js_snippet"
              src="https://ui.customsearch.ai /api/ux/rendering-js?customConfig=<YOUR-CUSTOM-CONFIG-ID>&market=en-US&safeSearch=Moderate&version=latest&q=">
          </script>
      </body>    
  </html>
  ```

- 或者，在 Web 浏览器中使用以下 URL。   
  
  `https://ui.customsearch.ai/hosted?customConfig=YOUR-CUSTOM-CONFIG-ID`  
  
  > [!NOTE]
  > 根据需要将以下查询参数添加到 URL。 有关这些参数的信息，请参阅[自定义搜索 API](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#query-parameters) 参考。
  >
  > - q
  > - mkt
  > - safesearch
  > - setlang

  > [!IMPORTANT]
  > 该页面无法显示隐私声明或其他注意事项和条款。 适用情况可能会有所不同。  

若要了解自定义配置 ID 等其他信息，请转到“产品”选项卡下的“终结点”********。

## <a name="configuration-options"></a>配置选项

单击“其他配置”并提供值即可配置托管 UI 的行为。**** 这些设置是可选的。 若要查看应用或删除设置后的效果，请查看右侧的预览窗格。 

### <a name="web-search-configurations"></a>Web 搜索配置

- 启用 web 结果：确定是否启用 web 搜索（你将在页面顶部看到 "Web" 选项卡）
- Enable 自动建议：确定是否已启用自定义自动建议（请参阅[定价](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/)以获得额外费用）。
- 每页的 web 结果：要一次显示的 web 搜索结果数（每页最大值为50结果）。
- 图像标题：确定图像是否显示在搜索结果中。

单击“显示高级配置”时将显示以下配置****：

- 突出显示单词：确定是否以粗体显示搜索结果。
- 链接目标：确定当用户单击搜索结果时，网页是在新的浏览器选项卡（空白）还是在同一浏览器选项卡（self）中打开。

### <a name="image-search-configurations"></a>图像搜索配置

- 启用图像结果：确定是否启用了图像搜索（你将在页面顶部看到 "映像" 选项卡）。
- 每页的图像结果数：每次显示的图像搜索结果数（每页最大值为150结果）。

单击“显示高级配置”时将显示以下配置****。  
  
- 启用筛选器：添加筛选器，用户可以使用这些筛选器筛选 Bing 返回的图像。 例如，用户可筛选仅动态 GIF 的结果。

### <a name="video-search-configurations"></a>视频搜索配置

- 启用视频结果：确定是否启用视频搜索（你将在页面顶部看到 "视频" 选项卡）。
- 每页的视频结果：要一次显示的视频搜索结果数（每页最大值为150结果）。

单击“显示高级配置”时将显示以下配置****。  
  
- 启用筛选器：添加筛选器，用户可以使用这些筛选器筛选 Bing 返回的视频。 例如，用户可以筛选具有特定分辨率的视频或过去 24 小时内发现的视频结果。

### <a name="miscellaneous-configurations"></a>其他配置

- 页面标题：在 "搜索结果" 页的标题区中显示的文本（不适用于弹出式布局）。
- 工具栏主题：确定 "搜索结果" 页的标题区的背景色。

如果单击 "**显示高级配置**"，则会显示以下配置。  

|Column1  |Column2  |
|---------|---------|
|搜索框文本占位符   | 在进行输入前搜索框中显示的文本。        |
|标题链接 URL    |标题链接的目标。         |
|徽标 URL     | 标题旁显示的图像。         |
|Favicon    | 浏览器标题栏中显示的图标。          |

仅当通过 HTML 终结点使用托管 UI 时才应用以下配置（使用 JavaScript 片段时不适用）。

- 页面标题
- 工具栏主题
- 标题链接 URL
- 徽标 URL
- Faviicon URL  

## <a name="next-steps"></a>后续步骤

- [使用修饰标记来突出显示文本](../bing-web-search/hit-highlighting.md)
- [网页](./page-webpages.md)
