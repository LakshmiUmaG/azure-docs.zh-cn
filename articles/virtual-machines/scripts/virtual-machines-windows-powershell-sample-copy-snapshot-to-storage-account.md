---
title: 将快照作为 VHD 导出/复制到不同区域中的存储帐户 - PowerShell 示例
description: Azure PowerShell 脚本示例 - 将快照作为 VHD 导出/复制到不同区域中的存储帐户
services: virtual-machines-windows
documentationcenter: storage
author: ramankumarlive
manager: kavithag
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 06/05/2017
ms.author: ramankum
ms.openlocfilehash: 9fd987a1cafc3d81da4c5d4777cef39f2c1b824b
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2020
ms.locfileid: "87010047"
---
# <a name="exportcopy-managed-snapshots-as-vhd-to-a-storage-account-in-different-region-with-powershell"></a>使用 PowerShell 将托管快照作为 VHD 导出/复制到不同区域中的存储帐户

此脚本将托管快照导出到不同区域中的存储帐户。 它首先会生成快照的 SAS URI，然后使用它将快照复制到不同区域中的存储帐户。 使用此脚本将托管磁盘的备份保留在灾难恢复的不同区域。  

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 

## <a name="sample-script"></a>示例脚本

[!code-powershell[main](../../../powershell_scripts/virtual-machine/copy-snapshot-to-storage-account/copy-snapshot-to-storage-account.ps1 "Copy snapshot")]


## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令为托管快照生成 SAS URI，并使用 SAS URI 将快照复制到存储帐户。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 注释 |
|---|---|
| [Grant-AzSnapshotAccess](/powershell/module/az.compute/new-azdisk) | 生成快照的 SAS URI，用于将快照复制到存储帐户。 |
| [New-AzStorageContext](/powershell/module/az.storage/new-azstoragecontext) | 使用帐户名和密钥创建存储帐户上下文。 此上下文可用于对存储帐户执行读/写操作。 |
| [Start-AzStorageBlobCopy](/powershell/module/az.storage/start-azstorageblobcopy) | 将快照的基础 VHD 复制到存储帐户 |

## <a name="next-steps"></a>后续步骤

[从 VHD 创建托管磁盘](virtual-machines-windows-powershell-sample-create-managed-disk-from-vhd.md?toc=%2fpowershell%2fmodule%2ftoc.json)

[从托管磁盘创建虚拟机](./virtual-machines-windows-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fpowershell%2fmodule%2ftoc.json)

有关 Azure PowerShell 模块的详细信息，请参阅 [Azure PowerShell 文档](/powershell/azure/)。

可以在 [Azure Windows VM 文档](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)中找到其他虚拟机 PowerShell 脚本示例。
