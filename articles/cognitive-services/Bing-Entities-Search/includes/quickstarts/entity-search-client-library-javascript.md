---
title: 必应实体搜索 JavaScript 客户端库快速入门
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/06/2020
ms.author: aahi
ms.custom: devx-track-javascript
ms.openlocfilehash: f5a3b48fd6be48d468b66009a77de100962be10d
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/29/2020
ms.locfileid: "87405054"
---
使用本快速入门开始通过适用于 JavaScript 的必应实体搜索客户端库来搜索实体。 虽然必应实体搜索具有与大多数编程语言兼容的 REST API，但该客户端库提供了一种简单方法来将服务集成到应用程序中。 可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/entitySearch.js) 上找到此示例的源代码。

## <a name="prerequisites"></a>先决条件

* 最新版本的 [Node.js](https://nodejs.org/en/download/)。

* [适用于 Node.js 的必应实体搜索 SDK](https://www.npmjs.com/package/@azure/cognitiveservices-entitysearch)

若要安装必应实体搜索 SDK，请执行以下操作：

1. 在开发环境中运行 `npm install ms-rest-azure`。
2. 在开发环境中运行 `npm install @azure/cognitiveservices-entitysearch`。

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](~/includes/cognitive-services-bing-entity-search-signup-requirements.md)]


## <a name="create-and-initialize-the-application"></a>创建并初始化应用程序

1. 在你喜欢使用的 IDE 或编辑器中创建新的 JavaScript 文件，然后添加以下要求。

    ```javascript
    const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
    const EntitySearchAPIClient = require('@azure/cognitiveservices-entitysearch');
    ```

2. 使用你的订阅密钥创建 `CognitiveServicesCredentials` 的实例。 然后，使用该实例创建搜索客户端的实例。

    ```javascript
    let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
    let entitySearchApiClient = new EntitySearchAPIClient(credentials);
    ```

## <a name="send-a-request-and-receive-a-response"></a>发送请求并接收响应

1. 通过 `entitiesOperations.search()` 发送实体搜索请求。 在收到响应后，输出 `queryContext`、返回的结果数以及第一个结果的说明。

    ```javascript
    entitySearchApiClient.entitiesOperations.search('seahawks').then((result) => {
        console.log(result.queryContext);
        console.log(result.entities.value);
        console.log(result.entities.value[0].description);
    }).catch((err) => {
        throw err;
    });
    ```

<!-- Removing until we can replace with a sanitized version.
![Entity results](media/entity-search-sdk-node-quickstart-results.png)
-->

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [创建单页 Web 应用](../../tutorial-bing-entities-search-single-page-app.md)

* [什么是必应实体搜索 API？](../../overview.md)