---
title: 包含文件
description: include 文件
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 05/05/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 95c553d26a3e79b53106b933c629c5884c3e004c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84466930"
---
| 资源 | 目标 | 硬限制 |
|----------|--------------|------------|
| 每个区域的存储同步服务数 | 100存储同步服务 | 是 |
| 每个存储同步服务的同步组数 | 200同步组 | 是 |
| 每个存储同步服务的已注册服务器 | 99 台服务器 | 是 |
| 每个同步组的云终结点数 | 1 个云终结点 | 是 |
| 每个同步组的服务器终结点数 | 50 个服务器终结点 | 否 |
| 每个服务器的服务器终结点数 | 30 个服务器终结点 | 是 |
| 每个同步组的文件系统对象数（目录和文件） | 100000000对象 | 否 |
| 目录中的最大文件系统对象（目录和文件）数 | 5000000对象 | 是 |
| 最大对象（目录和文件）安全描述符大小 | 64 KiB | 是 |
| 文件大小 | 100 GiB | 否 |
| 要进行分层的文件的最小文件大小 | V9.x：基于文件系统群集大小（双文件系统群集大小）。 例如，如果文件系统群集大小为4kb，则文件的最小大小为8kb。<br> V8 及更早版本： 64 KiB  | 是 |

> [!Note]  
> Azure 文件同步终结点可以纵向扩展到 Azure 文件共享的大小。 如果达到 Azure 文件共享大小限制，同步将无法运行。
