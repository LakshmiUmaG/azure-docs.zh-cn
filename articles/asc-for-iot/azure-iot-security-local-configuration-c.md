---
title: 安全代理本地配置（C）
description: 了解适用于 C 的代理本地配置的 Azure 安全中心。
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 2cf6a49b-5d35-491f-abc3-63ec24eb4bc2
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/26/2019
ms.author: mlottner
ms.openlocfilehash: 842a69c27ceb0d56df5a7b49eb9922b88d8d4b32
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85206931"
---
# <a name="understanding-the-localconfigurationjson-file---c-agent"></a>了解 LocalConfiguration.json 文件 - C 代理

用于 IoT security agent 的 Azure 安全中心使用本地配置文件中的配置。
在代理启动时，安全代理读取一次配置。
本地配置文件中的配置包含身份验证配置和其他代理相关配置。
文件包含 JSON 表示法中的 "键值" 对中的配置，并在安装代理时填充配置。

默认情况下，该文件位于：/var/ASCIoTAgent/LocalConfiguration.json

重新启动代理时，会对配置文件进行更改。

## <a name="security-agent-configurations-for-c"></a>C 的安全代理配置

| 配置名称 | 可能值 | 详细信息 |
|:-----------|:---------------|:--------|
| AgentId | GUID | 代理唯一标识符 |
| TriggerdEventsInterval | ISO8601 字符串 | 触发事件集合的计划程序间隔 |
| ConnectionTimeout | ISO8601 字符串 | 到 IoThub 的连接超时之前的时间段 |
| 身份验证 | JsonObject | 身份验证配置。 此对象包含针对 IoTHub 进行身份验证所需的所有信息 |
| 标识 | "DPS"、"SecurityModule"、"Device" | 身份验证标识-DPS 如果通过 DPS 进行身份验证，则通过安全模块凭据或设备进行身份验证（如果使用设备凭据进行身份验证） SecurityModule |
| AuthenticationMethod | "SasToken"、"New-selfsignedcertificate" | 用于身份验证的用户机密-如果使用密钥是对称密钥，请选择 "SasToken"，如果密码是自签名证书，请选择 "自签名证书"  |
| 文件路径 | 文件路径（字符串） | 包含身份验证密钥的文件的路径 |
| HostName | 字符串 | Azure iot 中心的主机名。 通常 <我的中心>。 azure-devices.net |
| DeviceId | 字符串 | 设备的 ID （在 Azure IoT 中心注册） |
| DPS | JsonObject | DPS 相关配置 |
| IDScope | 字符串 | DPS 的 ID 范围 |
| RegistrationId | 字符串  | DPS 设备注册 ID |
| Logging | JsonObject | 代理记录器相关配置 |
| SystemLoggerMinimumSeverity | 0 <= number <= 4 | 日志消息等于或高于此严重级别将记录到/var/log/syslog （0是最低严重性） |
| DiagnosticEventMinimumSeverity | 0 <= number <= 4 | 日志消息等于或高于此严重级别将作为诊断事件发送（0是最低严重性） |

## <a name="security-agent-configurations-code-example"></a>安全代理配置代码示例

```json
{
    "Configuration" : {
        "AgentId" : "b97faf0a-0f57-471f-9dab-46a8e1764946",
        "TriggerdEventsInterval" : "PT2M",
        "ConnectionTimeout" : "PT30S",
        "Authentication" : {
            "Identity" : "Device",
            "AuthenticationMethod" : "SasToken",
            "FilePath" : "/path/to/my/SymmetricKey",
            "DeviceId" : "my-device",
            "HostName" : "my-iothub.azure-devices.net",
            "DPS" : {
                "IDScope" : "",
                "RegistrationId" : ""
            }
        },
        "Logging": {
            "SystemLoggerMinimumSeverity": 0,
            "DiagnoticEventMinimumSeverity": 2
        }
    }
}
```

## <a name="next-steps"></a>后续步骤

- 阅读 Azure 安全中心以获取 IoT 服务[概述](overview.md)
- 详细了解用于 IoT[体系结构](architecture.md)的 Azure 安全中心
- 为 IoT[服务](quickstart-onboard-iot-hub.md)启用 Azure 安全中心
- 阅读 Azure 安全中心以获取 IoT 服务[常见问题解答](resources-frequently-asked-questions.md)
- 了解如何访问[原始安全数据](how-to-security-data-access.md)
- 了解[建议](concept-recommendations.md)
- 了解安全[警报](concept-security-alerts.md)
