---
title: 将 HDFS CLI 与 Azure Data Lake Storage Gen2 配合使用
description: 使用 Hadoop 分布式文件系统 (HDFS) CLI Azure Data Lake Storage Gen2。 创建一个容器，获取文件或目录的列表，等等。
services: storage
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 12/06/2018
ms.author: normesta
ms.subservice: data-lake-storage-gen2
ms.reviewer: artek
ms.openlocfilehash: 1d720aed44358dd314bc4226adb9ad517139cd18
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2020
ms.locfileid: "87836303"
---
# <a name="using-the-hdfs-cli-with-data-lake-storage-gen2"></a>将 HDFS CLI 与 Data Lake Storage Gen2 配合使用

可以使用命令行界面来访问并管理存储帐户中的数据，就像像使用 [Hadoop 分布式文件系统 (HDFS)](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html) 一样。 本文提供了一些有助于入门的示例。

HDInsight 可以访问在本地附加到计算节点的分布式容器。 可以使用与 HDFS 以及 Hadoop 支持的其他文件系统直接交互的 shell 来访问此容器。

有关 HDFS CLI 的详细信息，请参阅[官方文档](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html)和[hdfs 权限指南](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html)

>[!NOTE]
>如果你使用的是 Azure Databricks 而不是 HDInsight，并且想要通过使用命令行界面与数据进行交互，则可以使用 Databricks CLI 与 Databricks 文件系统进行交互。 请参阅[DATABRICKS CLI](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html)。

## <a name="use-the-hdfs-cli-with-an-hdinsight-hadoop-cluster-on-linux"></a>在 Linux 上结合使用 HDFS CLI 和 HDInsight Hadoop 群集

首先建立[对服务的远程访问](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-information#remote-access-to-services)。 如果选择了 [SSH](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix)，则示例 PowerShell 代码将如下所示：

```powershell
#Connect to the cluster via SSH.
ssh sshuser@clustername-ssh.azurehdinsight.net
#Execute basic HDFS commands. Display the hierarchy.
hdfs dfs -ls /
#Create a sample directory.
hdfs dfs -mkdir /samplefolder
```
可以在 Azure 门户中 HDInsight 群集边栏选项卡的“SSH + 群集登录”部分中找到连接字符串。 SSH 凭据是在创建群集时指定的。

>[!IMPORTANT]
>创建群集后便开始 HDInsight 群集计费，删除群集后停止计费。 HDInsight 群集按分钟收费，因此不再需要使用群集时，应将其删除。 若要了解如何删除群集，请参阅我们的[有关该主题的文章](../../hdinsight/hdinsight-delete-cluster.md)。 但是，即使删除了 HDInsight 群集，在启用了 Data Lake Storage Gen2 的存储帐户中存储的数据仍然会保留。

## <a name="create-a-container"></a>创建容器

`hdfs dfs -D "fs.azure.createRemoteFileSystemDuringInitialization=true" -ls abfs://<container-name>@<storage-account-name>.dfs.core.windows.net/`

* 将 `<container-name>` 占位符替换为你要为容器指定的名称。

* 将 `<storage-account-name>` 占位符替换为存储帐户的名称。

## <a name="get-a-list-of-files-or-directories"></a>获取文件或目录列表

`hdfs dfs -ls <path>`

将 `<path>` 占位符替换为容器或容器文件夹的 URI。

例如： `hdfs dfs -ls abfs://my-file-system@mystorageaccount.dfs.core.windows.net/my-directory-name`

## <a name="create-a-directory"></a>创建目录

`hdfs dfs -mkdir [-p] <path>`

将 `<path>` 占位符替换为根容器名称或容器中的文件夹。

例如： `hdfs dfs -mkdir abfs://my-file-system@mystorageaccount.dfs.core.windows.net/`

## <a name="delete-a-file-or-directory"></a>删除文件或目录

`hdfs dfs -rm <path>`

将 `<path>` 占位符替换为要删除的文件或文件夹的 URI。

例如： `hdfs dfs -rmdir abfs://my-file-system@mystorageaccount.dfs.core.windows.net/my-directory-name/my-file-name`

## <a name="display-the-access-control-lists-acls-of-files-and-directories"></a>显示文件和目录的访问控制列表 (ACL)

`hdfs dfs -getfacl [-R] <path>`

示例：

`hdfs dfs -getfacl -R /dir`

请参阅 [getfacl](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#getfacl)

## <a name="set-acls-of-files-and-directories"></a>设置文件和目录的 ACL

`hdfs dfs -setfacl [-R] [-b|-k -m|-x <acl_spec> <path>]|[--set <acl_spec> <path>]`

示例：

`hdfs dfs -setfacl -m user:hadoop:rw- /file`

请参阅 [setfacl](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#setfacl)

## <a name="change-the-owner-of-files"></a>更改文件的所有者

`hdfs dfs -chown [-R] <new_owner>:<users_group> <URI>`

请参阅 [chown](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#chown)

## <a name="change-group-association-of-files"></a>更改文件的组关联

`hdfs dfs -chgrp [-R] <group> <URI>`

请参阅 [chgrp](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#chgrp)

## <a name="change-the-permissions-of-files"></a>更改文件的权限

`hdfs dfs -chmod [-R] <mode> <URI>`

请参阅 [chmod](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#chmod)

若要查看命令的完整列表，请访问 [Apache Hadoop 2.4.1 文件系统 Shell 指南](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html)网站。

## <a name="next-steps"></a>后续步骤

* [在 Azure Databricks 中使用支持 Azure Data Lake Storage Gen2 的帐户](./data-lake-storage-quickstart-create-databricks-account.md)

* [了解文件和目录上的访问控制列表](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)
