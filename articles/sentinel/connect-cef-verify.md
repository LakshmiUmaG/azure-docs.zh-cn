---
title: 验证与 Azure Sentinel 的连接 |Microsoft Docs
description: 验证安全解决方案的连接性，以确保将 CEF 消息转发到 Azure Sentinel。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/19/2020
ms.author: yelevin
ms.openlocfilehash: f6892f4ebb250290a0faad546fd000530baf4479
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2020
ms.locfileid: "87038165"
---
# <a name="step-3-validate-connectivity"></a>步骤3：验证连接性

部署日志转发器（在步骤1中）并将安全解决方案配置为向其发送 CEF 消息（在步骤2中）后，请按照以下说明验证安全解决方案与 Azure Sentinel 之间的连接。 

## <a name="prerequisites"></a>先决条件

- 你必须在日志转发器计算机上拥有提升的权限（sudo）。

- 你必须在日志转发器计算机上安装 Python。<br>
使用 `python –version` 命令检查。

## <a name="how-to-validate-connectivity"></a>如何验证连接

1. 在 Azure Sentinel 导航菜单中打开 "**日志**"。 使用**CommonSecurityLog**架构运行查询，以查看您的安全解决方案是否正在接收日志。<br>
请注意，在**Log Analytics**中开始显示日志之前，可能需要大约20分钟的时间。 

1. 如果看不到查询中的任何结果，请验证是否正在从安全解决方案生成事件，或尝试生成一些事件，并验证是否将这些事件转发到指定的 Syslog 转发器计算机。 

1. 在日志转发器上运行以下脚本，以检查安全解决方案、日志转发器和 Azure Sentinel 之间的连接。 此脚本将检查守护程序是否正在侦听正确的端口，是否已正确配置转发，以及守护程序与 Log Analytics 代理之间是否阻止通信。 它还会发送模拟消息 "TestCommonEventFormat" 来检查端到端连接。 <br>
 `sudo wget https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_troubleshoot.py&&sudo python cef_troubleshoot.py [WorkspaceID]`

## <a name="validation-script-explained"></a>验证脚本说明

验证脚本执行以下检查：

# <a name="rsyslog-daemon"></a>[rsyslog 后台程序](#tab/rsyslog)

1. 检查文件<br>
    `/etc/opt/microsoft/omsagent/[WorkspaceID]/conf/omsagent.d/security_events.conf`<br>
    exists 和有效。

1. 检查文件是否包含以下文本：

    ```console
    <source>
        type syslog
        port 25226
        bind 127.0.0.1
        protocol_type tcp
        tag oms.security
        format /(?<time>(?:\w+ +){2,3}(?:\d+:){2}\d+|\d{4}-\d{2}-\d{2}T\d{2}:\d{2}:\d{2}.[\w\-\:\+]{3,12}):?\s*(?:(?<host>[^: ]+) ?:?)?\s*(?<ident>.*CEF.+?(?=0\|)|%ASA[0-9\-]{8,10})\s*:?(?<message>0\|.*|.*)/
        <parse>
            message_format auto
        </parse>
    </source>

    <filter oms.security.**>
        type filter_syslog_security
    </filter>
    ```

1. 检查计算机上是否存在可能阻止网络流量的安全增强功能（如主机防火墙）。

1. 检查 syslog 守护程序（rsyslog）是否已正确配置，以将其标识为 CEF 的消息（使用 regex）发送到 TCP 端口25226上的 Log Analytics 代理：

    - 配置文件：`/etc/rsyslog.d/security-config-omsagent.conf`

        ```console
        :rawmsg, regex, "CEF"|"ASA"
        *.* @@127.0.0.1:25226
        ```
  
1. 检查 syslog 后台程序是否正在接收端口514上的数据

1. 检查是否建立了必需的连接：用于接收数据的 tcp 514、用于 syslog 守护程序和 Log Analytics 代理之间的内部通信的 tcp 25226

1. 向 localhost 上的端口514发送模拟数据。 通过运行以下查询，在 Azure Sentinel 工作区中可观察到此数据：

    ```console
    CommonSecurityLog
    | where DeviceProduct == "MOCK"
    ```

# <a name="syslog-ng-daemon"></a>[syslog-ng 守护程序](#tab/syslogng)

1. 检查文件<br>
    `/etc/opt/microsoft/omsagent/[WorkspaceID]/conf/omsagent.d/security_events.conf`<br>
    exists 和有效。

1. 检查文件是否包含以下文本：

    ```console
    <source>
        type syslog
        port 25226
        bind 127.0.0.1
        protocol_type tcp
        tag oms.security
        format /(?<time>(?:\w+ +){2,3}(?:\d+:){2}\d+|\d{4}-\d{2}-\d{2}T\d{2}:\d{2}:\d{2}.[\w\-\:\+]{3,12}):?\s*(?:(?<host>[^: ]+) ?:?)?\s*(?<ident>.*CEF.+?(?=0\|)|%ASA[0-9\-]{8,10})\s*:?(?<message>0\|.*|.*)/
        <parse>
            message_format auto
        </parse>
    </source>

    <filter oms.security.**>
        type filter_syslog_security
    </filter>
    ```

1. 检查计算机上是否存在可能阻止网络流量的安全增强功能（如主机防火墙）。

1. 检查 syslog 后台程序（syslog-ng）是否已正确配置，以将其标识为 CEF 的消息（使用 regex）发送到 TCP 端口25226上的 Log Analytics 代理：

    - 配置文件：`/etc/syslog-ng/conf.d/security-config-omsagent.conf`

        ```console
        filter f_oms_filter {match(\"CEF\|ASA\" ) ;};
        destination oms_destination {tcp(\"127.0.0.1\" port("25226"));};
        log {source(s_src);filter(f_oms_filter);destination(oms_destination);};
        ```

1. 检查 syslog 后台程序是否正在接收端口514上的数据

1. 检查是否建立了必需的连接：用于接收数据的 tcp 514、用于 syslog 守护程序和 Log Analytics 代理之间的内部通信的 tcp 25226

1. 向 localhost 上的端口514发送模拟数据。 通过运行以下查询，在 Azure Sentinel 工作区中可观察到此数据：

    ```console
    CommonSecurityLog
    | where DeviceProduct == "MOCK"
    ```
---

## <a name="next-steps"></a>后续步骤
本文档介绍了如何将 CEF 设备连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：
- 了解如何[洞悉数据和潜在威胁](quickstart-get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](tutorial-detect-threats.md)。
- [使用工作簿](tutorial-monitor-your-data.md)监视数据。

