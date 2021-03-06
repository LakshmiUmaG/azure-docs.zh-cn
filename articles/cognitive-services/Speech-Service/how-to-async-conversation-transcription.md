---
title: 异步对话对话-语音服务
titleSuffix: Azure Cognitive Services
description: '了解如何使用语音服务的异步对话脚本。 仅适用于 Java 和 c #。'
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: amishu
zone_pivot_groups: programming-languages-set-twenty-one
ms.openlocfilehash: 65a6fd7e0e1e64a459a46c0f97a2afdbc03573a9
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/28/2020
ms.locfileid: "87284196"
---
# <a name="asynchronous-conversation-transcription"></a>异步对话听录

在本文中，使用**RemoteConversationTranscriptionClient** API 演示了异步对话脚本。 如果已将会话脚本配置为执行异步脚本，并拥有 `conversationId` ，则可以 `conversationId` 使用**RemoteConversationTranscriptionClient** API 获取与关联的脚本。

## <a name="asynchronous-vs-real-time--asynchronous"></a>异步与实时 + 异步

对于异步脚本，会流式传输会话音频，但不需要实时返回脚本。 相反，发送音频后，使用 `conversationId` 的 `Conversation` 来查询异步脚本的状态。 异步脚本准备就绪后，你将获得 `RemoteConversationTranscriptionResult` 。

使用实时和异步，你可以实时地获取脚本，但也可以通过使用进行查询 `conversationId` （类似于异步方案）来获取脚本。

完成异步脚本需要执行两个步骤。 第一步是上传音频，只选择 "异步" 或 "实时" 和 "异步"。 第二步是获取脚本结果。

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/how-to/remote-conversation/csharp/examples.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [prerequisites](includes/how-to/remote-conversation/java/examples.md)]
::: zone-end


## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [浏览 GitHub 上的示例](https://aka.ms/csspeech/samples)
