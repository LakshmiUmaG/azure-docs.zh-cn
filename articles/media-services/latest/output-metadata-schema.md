---
title: Azure 媒体服务输出元数据架构 | Microsoft Docs
description: 本文概述了 Azure 媒体服务输出元数据架构。
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/03/2020
ms.author: juliako
ms.openlocfilehash: ce3d0a5beb5903d29b1deec345cf4673e3492e5d
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2020
ms.locfileid: "87080918"
---
# <a name="output-metadata"></a>输出元数据

编码作业与要在其上执行某些编码任务的输入资产（或资产）相关联。 例如，将 MP4 文件编码为 H.264 MP4 自适应比特率集；创建缩略图；创建叠加。 完成任务后，会生成一个输出资产。  输出资产包含视频、音频、缩略图和其他文件。 输出资产还包含提供输出资产相关元数据的文件。 元数据 JSON 文件的名称采用以下格式： `<source_file_name>_manifest.json` （例如 `BigBuckBunny_manifest.json` ）。 你应在上扫描任何 * _metadata.js，并在内查询 filepath 字符串以查找源文件名（不截断）。

媒体服务不会提前扫描输入资产以生成元数据。 输入元数据仅在工作中处理输入资产时作为项目生成。 因此，会将此项目写入输出资产。 不同的工具用于为输入资产和输出资产生成元数据。 因此，输入元数据的模式与输出元数据略有不同。

本文介绍了输出元数据（ &lt; source_file_name_manifest.js基于）的 JSON 架构的元素和类型 &gt; 。 <!--For information about the file that contains metadata about the input asset, see [Input metadata](input-metadata-schema.md).  -->

可以在本文末尾找到完整的架构代码和 JSON 示例。  

## <a name="assetfile"></a>AssetFile

编码作业的 AssetFile 条目集合。  

| “属性” | 描述 |
| --- | --- |
| **来源** |为生成此 AssetFile 而处理的输入/源媒体文件集合。<br />示例： `"Sources": [{"Name": "Ignite-short_1280x720_AACAudio_3551.mp4"}]`|
| **VideoTracks**|每个物理 AssetFile 都可包含交错成适当容器格式的零个或多个视频轨道。 <br />请参阅[VideoTracks](#videotracks)。 |
| **AudioTracks**|每个物理 AssetFile 都可包含交错成适当容器格式的零个或多个音频轨道。 这是所有这些音频轨道的集合。<br /> 有关详细信息，请参阅[AudioTracks](#audiotracks)。 |
| **名称**<br />必填 |媒体资产文件名。 <br /><br />示例： `"Name": "Ignite-short_1280x720_AACAudio_3551.mp4"`|
| **大小**<br />必填 |资产文件的大小（以字节为单位）。 <br /><br />示例： `"Size": 32414631`|
| **持续时间**<br />必填 |内容播放持续时间。 有关详细信息，请参阅[ISO8601](https://www.iso.org/iso-8601-date-and-time-format.html)格式。 <br /><br />示例： `"Duration": "PT1M10.315S"`|

## <a name="videotracks"></a>VideoTracks 

每个物理 AssetFile 都可包含交错成适当容器格式的零个或多个视频轨道。 **VideoTracks** 元素表示所有视频轨道的集合。  

| “属性” | 说明 |
| --- | --- |
| **Id**<br /> 必填 |此视频轨道的从零开始的索引。**注意：** 此**Id**不一定是在 TrackID 文件中使用的。 <br /><br />示例： `"Id": 1`|
| **FourCC**<br />必填 | Ffmpeg 报告的视频编解码器 FourCC 代码。  <br /><br />示例： `"FourCC": "avc1"`|
| **Profile** |H264 配置文件（仅适用于 H264 编解码器）。  <br /><br />示例： `"Profile": "High"` |
| **级别** |H264 级别（仅适用于 H264 编解码器）。  <br /><br />示例： `"Level": "3.2"`|
| **Width**<br />必填 |编码视频宽度（以像素为单位）。  <br /><br />示例： `"Width": "1280"`|
| **Height**<br />必填 |编码视频高度（以像素为单位）。  <br /><br />示例： `"Height": "720"`|
| **DisplayAspectRatioNumerator**<br />必填|视频显示纵横比分子。  <br /><br />示例： `"DisplayAspectRatioNumerator": 16.0`|
| **DisplayAspectRatioDenominator**<br />必填 |视频显示纵横比分母。  <br /><br />示例： `"DisplayAspectRatioDenominator": 9.0`|
| **Framerate**<br />必填 |采用 .3f 格式测量的视频帧速率。  <br /><br />示例： `"Framerate": 29.970`|
| **率**<br />必填 |从 AssetFile 计算的平均视频比特率（比特/秒）。 只计算基本流有效负载，并且不包括打包开销。  <br /><br />示例： `"Bitrate": 3551567`|
| **TargetBitrate**<br />必填 |通过编码预设请求的此视频轨道的目标平均比特率（以每秒位数为单位）。 <br /><br />示例： `"TargetBitrate": 3520000` |

## <a name="audiotracks"></a>AudioTracks 

每个物理 AssetFile 都可包含交错成适当容器格式的零个或多个音频轨道。 **AudioTracks** 元素表示所有这些音频轨道的集合。  

| “属性”  | 说明 |
| --- | --- |
| **Id**<br />必填  |此音轨的从零开始的索引。**注意：** 这不一定是在 TrackID 文件中使用的。  <br /><br />示例： `"Id": 2`|
| **编解码器**  |音频轨道编解码器字符串。  <br /><br />示例： `"Codec": "aac"`|
| **语言**|示例： `"Language": "eng"`|
| **声道**<br />必填|音频通道数。  <br /><br />示例： `"Channels": 2`|
| **SamplingRate**<br />必填 |音频采样速率（以采样数/秒或 Hz 为单位）。  <br /><br />示例： `"SamplingRate": 48000`|
| **率**<br />必填 |由 AssetFile 计算的平均音频比特率（以比特/秒为单位）。 只计算基本流有效负载，并且不包括打包开销。  <br /><br />示例： `"Bitrate": 128041`|

## <a name="json-schema-example"></a>JSON 架构示例

```json
{
  "AssetFile": [
    {
      "Sources": [
        {
          "Name": "Ignite-short_1280x720_AACAudio_3551.mp4"
        }
      ],
      "VideoTracks": [
        {
          "Id": 1,
          "FourCC": "avc1",
          "Profile": "High",
          "Level": "3.2",
          "Width": "1280",
          "Height": "720",
          "DisplayAspectRatioNumerator": 16.0,
          "DisplayAspectRatioDenominator": 9.0,
          "Framerate": 29.970,
          "Bitrate": 3551567,
          "TargetBitrate": 3520000
        }
      ],
      "AudioTracks": [
        {
          "Id": 2,
          "Codec": "aac",
          "Language": "eng",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128041
        }
      ],
      "Name": "Ignite-short_1280x720_AACAudio_3551.mp4",
      "Size": 32414631,
      "Duration": "PT1M10.315S"
    },
    {
      "Sources": [
        {
          "Name": "Ignite-short_960x540_AACAudio_2216.mp4"
        }
      ],
      "VideoTracks": [
        {
          "Id": 1,
          "FourCC": "avc1",
          "Profile": "High",
          "Level": "3.1",
          "Width": "960",
          "Height": "540",
          "DisplayAspectRatioNumerator": 16.0,
          "DisplayAspectRatioDenominator": 9.0,
          "Framerate": 29.970,
          "Bitrate": 2216326,
          "TargetBitrate": 2210000
        }
      ],
      "AudioTracks": [
        {
          "Id": 2,
          "Codec": "aac",
          "Language": "eng",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128041
        }
      ],
      "Name": "Ignite-short_960x540_AACAudio_2216.mp4",
      "Size": 20680897,
      "Duration": "PT1M10.315S"
    },
    {
      "Sources": [
        {
          "Name": "Ignite-short_640x360_AACAudio_1150.mp4"
        }
      ],
      "VideoTracks": [
        {
          "Id": 1,
          "FourCC": "avc1",
          "Profile": "High",
          "Level": "3.0",
          "Width": "640",
          "Height": "360",
          "DisplayAspectRatioNumerator": 16.0,
          "DisplayAspectRatioDenominator": 9.0,
          "Framerate": 29.970,
          "Bitrate": 1150440,
          "TargetBitrate": 1150000
        }
      ],
      "AudioTracks": [
        {
          "Id": 2,
          "Codec": "aac",
          "Language": "eng",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128041
        }
      ],
      "Name": "Ignite-short_640x360_AACAudio_1150.mp4",
      "Size": 11313920,
      "Duration": "PT1M10.315S"
    },
    {
      "Sources": [
        {
          "Name": "Ignite-short_480x270_AACAudio_722.mp4"
        }
      ],
      "VideoTracks": [
        {
          "Id": 1,
          "FourCC": "avc1",
          "Profile": "High",
          "Level": "2.1",
          "Width": "480",
          "Height": "270",
          "DisplayAspectRatioNumerator": 16.0,
          "DisplayAspectRatioDenominator": 9.0,
          "Framerate": 29.970,
          "Bitrate": 722682,
          "TargetBitrate": 720000
        }
      ],
      "AudioTracks": [
        {
          "Id": 2,
          "Codec": "aac",
          "Language": "eng",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128041
        }
      ],
      "Name": "Ignite-short_480x270_AACAudio_722.mp4",
      "Size": 7554708,
      "Duration": "PT1M10.315S"
    },
    {
      "Sources": [
        {
          "Name": "Ignite-short_320x180_AACAudio_380.mp4"
        }
      ],
      "VideoTracks": [
        {
          "Id": 1,
          "FourCC": "avc1",
          "Profile": "High",
          "Level": "1.3",
          "Width": "320",
          "Height": "180",
          "DisplayAspectRatioNumerator": 16.0,
          "DisplayAspectRatioDenominator": 9.0,
          "Framerate": 29.970,
          "Bitrate": 380655,
          "TargetBitrate": 380000
        }
      ],
      "AudioTracks": [
        {
          "Id": 2,
          "Codec": "aac",
          "Language": "eng",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128041
        }`
      ],
      "Name": "Ignite-short_320x180_AACAudio_380.mp4",
      "Size": 4548932,
      "Duration": "PT1M10.315S"
    }
  ]
}
```

## <a name="next-steps"></a>后续步骤

[从 HTTPS URL 创建作业输入](job-input-from-http-how-to.md)
