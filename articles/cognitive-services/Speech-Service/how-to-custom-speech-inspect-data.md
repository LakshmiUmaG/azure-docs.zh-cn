---
title: 检查自定义语音识别的数据质量 - 语音服务
titleSuffix: Azure Cognitive Services
description: 自定义语音识别提供的工具可以将音频数据与相应的识别结果进行比较，因此可以通过视觉方式检查模型的质量。 你可以播放上传的音频，确定提供的识别结果是否正确。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: erhopf
ms.openlocfilehash: e871d2c8e0fe00fa7db3144a787447163c82e62d
ms.sourcegitcommit: d7fba095266e2fb5ad8776bffe97921a57832e23
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/09/2020
ms.locfileid: "84629035"
---
# <a name="inspect-custom-speech-data"></a>检查自定义语音数据

> [!NOTE]
> 此页假定你已阅读[准备自定义语音识别的测试数据](how-to-custom-speech-test-data.md)，并已上传用于检查的数据集。

自定义语音识别提供的工具可以将音频数据与相应的识别结果进行比较，因此可以通过视觉方式检查模型的质量。 在[自定义语音识别门户](https://speech.microsoft.com/customspeech)中，你可以播放上传的音频，确定提供的识别结果是否正确。 此工具可帮助你检查 Microsoft 的基线语音到文本模型的质量，检查训练的自定义模型，或比较两个模型的脚本。

本文档介绍如何使用以前上传的训练数据以视觉方式检查模型的质量。

此页介绍如何以视觉方式检查 Microsoft 的基线语音转文本模型和/或你训练的自定义模型的质量。 将使用已上传到“数据”选项卡进行测试的数据。 

## <a name="create-a-test"></a>创建测试

按这些说明创建测试：

1. 登录到[自定义语音识别门户](https://speech.microsoft.com/customspeech)。
2. 导航到 "**语音到文本" > 自定义语音 > [项目名称] > 测试**。
3. 单击“添加测试”。 
4. 选择“检查质量(仅音频数据)”。  为测试提供名称和说明，然后选择你的音频数据集。
5. 选择最多两个要测试的模型。
6. 单击“创建”。 

成功创建测试后，你可以看到模型如何转录你指定的音频数据集，或将两个模型中的结果并行进行比较。

[!INCLUDE [service-pricing-advisory](includes/service-pricing-advisory.md)]

## <a name="side-by-side-model-comparisons"></a>并排进行模型比较

当测试状态为“成功”时，请单击测试项名称，查看测试详细信息。  此详细信息页列出了数据集中的所有最谈话，并显示了所比较的两个模型的识别结果。

可以通过切换各种错误类型（包括插入、删除和替换）来查看并排比较的结果。 通过听音频并比较每个列（显示人为标记的听录和两个语音转文本模型的结果）中的识别结果，你可以确定哪个模型符合自己的需求，以及需要在哪些方面进行改进。

并行模型测试可用于验证哪个语音识别模型最适合应用程序。 若要客观度量准确度，需对音频进行听录，并按[评估准确度](how-to-custom-speech-evaluate-data.md)中的说明操作。

## <a name="next-steps"></a>后续步骤

- [评估数据](how-to-custom-speech-evaluate-data.md)
- [训练模型](how-to-custom-speech-train-model.md)
- [改进模型](how-to-custom-speech-improve-accuracy.md)
- [部署模型](how-to-custom-speech-deploy-model.md)

## <a name="additional-resources"></a>其他资源

- [准备自定义语音的测试数据](how-to-custom-speech-test-data.md)
