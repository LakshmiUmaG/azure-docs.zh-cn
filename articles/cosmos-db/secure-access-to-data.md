---
title: 了解如何保护对 Azure Cosmos DB 中数据的访问
description: 了解有关 Azure Cosmos DB 中的访问控制概念，包括主密钥、只读密钥、用户和权限。
author: thomasweiss
ms.author: thweiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 01/21/2020
ms.openlocfilehash: 3a9039470c32b89d398dd41e3df99e91c70d913c
ms.sourcegitcommit: 8def3249f2c216d7b9d96b154eb096640221b6b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/03/2020
ms.locfileid: "87542630"
---
# <a name="secure-access-to-data-in-azure-cosmos-db"></a>保护对 Azure Cosmos DB 中数据的访问

本文概述了如何保护对[Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)中存储的数据的访问。

Azure Cosmos DB 使用两种类型的密钥来验证用户身份并提供其数据和资源的访问权限。 

|密钥类型|资源|
|---|---|
|[主密钥](#master-keys) |用于管理资源：数据库帐户、数据库、用户和权限|
|[资源令牌](#resource-tokens)|用于应用程序资源：容器、文档、附件、存储过程、触发器和 UDF|

<a id="master-keys"></a>

## <a name="master-keys"></a>主密钥

主密钥提供对数据库帐户的所有管理资源的访问权限。 主密钥：

- 提供对帐户、数据库、用户和权限的访问权限。 
- 无法用于提供对容器和文档的精细访问权限。
- 在创建帐户过程中创建。
- 随时可重新生成。

每个帐户包括两个主密钥：主要密钥和辅助密钥。 使用两个密钥的目的是为了能够重新生成或轮换密钥，从而可以持续访问帐户和数据。

Azure Cosmos DB 帐户除了有两个主密钥以外，还有两个只读密钥。 这些只读密钥只允许针对帐户执行读取操作。 只读密钥不提供对资源的读取权限。

可以使用 Azure 门户检索和重新生成主要、辅助、只读和读写主密钥。 有关说明，请参阅[查看、复制和重新生成访问密钥](manage-with-cli.md#regenerate-account-key)。

:::image type="content" source="./media/secure-access-to-data/nosql-database-security-master-key-portal.png" alt-text="Azure 门户中的访问控制 (IAM) - 演示 NoSQL 数据库安全性":::

### <a name="key-rotation"></a>密钥轮换<a id="key-rotation"></a>

轮换主密钥的过程相当简单。 

1. 导航到 Azure 门户以检索辅助密钥。
2. 在应用程序中将主密钥替换为辅助密钥。 确保所有部署中的所有 Cosmos DB 客户端都立即重启，并将开始使用已更新的密钥。
3. 在 Azure 门户中轮换主密钥。
4. 验证新主密钥是否适用于所有资源。 密钥轮换过程可能需要不到一分钟，也可能需要几小时，具体取决于 Cosmos DB 帐户的大小。
5. 将辅助密钥替换为新的主密钥。

:::image type="content" source="./media/secure-access-to-data/nosql-database-security-master-key-rotate-workflow.png" alt-text="Azure 门户中的主密钥轮换 - 演示 NoSQL 数据库安全性" border="false":::

### <a name="code-sample-to-use-a-master-key"></a>有关使用主密钥的代码示例

下面的代码示例演示如何使用 Cosmos DB 帐户终结点和主密钥来实例化 DocumentClient 并创建数据库：

```csharp
//Read the Azure Cosmos DB endpointUrl and authorization keys from config.
//These values are available from the Azure portal on the Azure Cosmos DB account blade under "Keys".
//Keep these values in a safe and secure location. Together they provide Administrative access to your Azure Cosmos DB account.

private static readonly string endpointUrl = ConfigurationManager.AppSettings["EndPointUrl"];
private static readonly string authorizationKey = ConfigurationManager.AppSettings["AuthorizationKey"];

CosmosClient client = new CosmosClient(endpointUrl, authorizationKey);
```

下面的代码示例演示如何使用 Azure Cosmos DB 帐户终结点和主密钥来实例化 `CosmosClient` 对象：

:::code language="python" source="~/cosmosdb-python-sdk/sdk/cosmos/azure-cosmos/samples/access_cosmos_with_resource_token.py" id="configureConnectivity":::

## <a name="resource-tokens"></a>资源令牌<a id="resource-tokens"></a>

资源令牌提供对数据库中应用程序资源的访问权限。 资源令牌：

- 提供对特定容器、分区键、文档、附件、存储过程、触发器和 UDF 的访问权限。
- 向[用户](#users)授予对特定资源的[权限](#permissions)时创建。
- 通过 POST、GET 或 PUT 调用操作权限资源时重新创建。
- 使用专门针对用户、资源和权限构造的哈希资源令牌。
- 生存期受到可自定义的有效期的约束。 默认的有效期限为一小时。 但是，可将令牌生存期显式指定为最长五个小时。
- 可以安全替代主密钥。
- 使客户端能够根据它们的权限读取、写入和删除 Cosmos DB 帐户中的资源。

如果想要为不能通过主密钥得到信任的客户端提供对 Cosmos DB 帐户中资源的访问权限，可以使用资源令牌（通过创建 Cosmos DB 用户和权限来使用）。  

Cosmos DB 资源令牌提供一种安全的替代方案，使客户端能够根据授予的权限读取、写入和删除 Cosmos DB 帐户中的资源，而无需主密钥或只读密钥。

以下是典型的设计模式，通过它可以请求、生成资源令牌并将其提供给客户端：

1. 设置中间层服务，以用于移动应用程序共享用户照片。
2. 中间层服务拥有 Cosmos DB 帐户的主密钥。
3. 照片应用安装在最终用户移动设备上。
4. 登录时，照片应用使用中间层服务建立用户的标识。 这种标识建立机制完全由应用程序决定。
5. 一旦建立标识，中间层服务就会基于标识请求权限。
6. 中间层服务将资源令牌发送回手机应用。
7. 手机应用可以继续使用该资源令牌以该资源令牌定义的权限按照该资源令牌允许的间隔直接访问 Cosmos DB 资源。
8. 资源令牌到期后，后续请求收到 401 未经授权的异常。  此时，手机应用会重新建立标识，并请求新的资源令牌。

    :::image type="content" source="./media/secure-access-to-data/resourcekeyworkflow.png" alt-text="Azure Cosmos DB 资源令牌工作流" border="false":::

资源令牌的生成和管理由本机 Cosmos DB 客户端库处理；但是，如果使用 REST，必须构造请求/身份验证标头。 有关为 REST 创建身份验证标头的详细信息，请参阅 [Cosmos DB 资源的访问控制](/rest/api/cosmos-db/access-control-on-cosmosdb-resources)或我们的 [.NET SDK](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos/src/AuthorizationHelper.cs) 或 [Node.js SDK](https://github.com/Azure/azure-cosmos-js/blob/master/src/auth.ts) 的源代码。

有关用于生成或代理资源令牌的中间层服务的示例，请参阅 [ResourceTokenBroker 应用](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/xamarin/UserItems/ResourceTokenBroker/ResourceTokenBroker/Controllers)。

## <a name="users"></a>用户<a id="users"></a>

Azure Cosmos DB 用户与 Cosmos 数据库相关联。  每个数据库可以包含零个或更多 Cosmos DB 用户。 以下代码示例展示了如何使用 [Azure Cosmos DB .NET SDK v3](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/UserManagement) 创建 Cosmos DB 用户。

```csharp
//Create a user.
Database database = benchmark.client.GetDatabase("SalesDatabase");

User user = await database.CreateUserAsync("User 1");
```

> [!NOTE]
> 每个 Cosmos DB 用户都有一个 ReadAsync() 方法，可以使用此方法检索与用户关联的[权限](#permissions)的列表。

## <a name="permissions"></a>权限<a id="permissions"></a>

权限资源与用户相关联，并在容器以及分区键级别进行分配。 每个用户可能包含零个或多个权限。 用户在尝试访问某个特定容器或访问特定分区键中的数据时需要一个安全令牌，权限资源提供对该安全令牌的访问权限。 权限资源提供两种可用的访问级别：

- 所有：用户对资源拥有完全权限。
- 读取：用户只能读取资源的内容，但无法对资源执行写入、更新或删除操作。

> [!NOTE]
> 为了运行存储过程，用户必须对将在其中运行存储过程的容器具有全部权限。

### <a name="code-sample-to-create-permission"></a>有关创建权限的代码示例

以下代码示例演示如何创建权限资源、读取权限资源的资源令牌以及将权限与上面创建的[用户](#users)关联。

```csharp
// Create a permission on a container and specific partition key value
Container container = client.GetContainer("SalesDatabase", "OrdersContainer");
user.CreatePermissionAsync(
    new PermissionProperties(
        id: "permissionUser1Orders",
        permissionMode: PermissionMode.All,
        container: benchmark.container,
        resourcePartitionKey: new PartitionKey("012345")));
```

### <a name="code-sample-to-read-permission-for-user"></a>有关读取用户权限的代码示例

下面的代码片段展示了如何检索与上面创建的用户关联的权限，并代表用户实例化一个新的 CosmosClient，作用域为单个分区键。

```csharp
//Read a permission, create user client session.
PermissionProperties permissionProperties = await user.GetPermission("permissionUser1Orders")

CosmosClient client = new CosmosClient(accountEndpoint: "MyEndpoint", authKeyOrResourceToken: permissionProperties.Token);
```

## <a name="add-users-and-assign-roles"></a>添加用户和分配角色

若要将 Azure Cosmos DB 帐户读者访问权限添加到用户帐户，请让订阅所有者在 Azure 门户执行以下步骤。

1. 打开 Azure 门户，并选择 Azure Cosmos DB 帐户。
2. 单击“访问控制(IAM)”选项卡，然后单击“+ 添加角色分配”。
3. 在“添加角色分配”窗格中的“角色”框中，选择“Cosmos DB 帐户读者角色”。
4. 在“分配其访问权限”框中，选择“Azure AD 用户、组或应用程序”。
5. 在你想要授予访问权限的目录中选择用户、组或应用程序。  可以通过显示名称、电子邮件地址或对象标识符搜索目录。
    所选用户、组或应用程序会显示在所选成员列表中。
6. 单击“保存” 。

实体现在便可以读取 Azure Cosmos DB 资源。

## <a name="delete-or-export-user-data"></a>删除或导出用户数据

用户可使用 Azure Cosmos DB 搜索、选择、修改和删除数据库或集合中的任何个人数据。 Azure Cosmos DB 提供可用于查找和删除个人数据的 API，但用户应负责使用该 API 并定义擦除个人数据必需的逻辑。 每个多模型 API（SQL、MongoDB、Gremlin、Cassandra、表）都包含不同的语言 SDK，这些 SDK 提供了各种用于搜索和删除个人数据的方法。 还可启用[生存时间 (TTL)](time-to-live.md)功能在指定时间段后自动删除数据，不会产生任何额外费用。

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="next-steps"></a>后续步骤

- 若要详细了解 Cosmos 数据库安全性，请参阅 [Cosmos DB 数据库安全性](database-security.md)。
- 若要了解如何构造 Azure Cosmos DB 授权令牌，请参阅 [Azure Cosmos DB 资源的访问控制](/rest/api/cosmos-db/access-control-on-cosmosdb-resources)。
- 有关包含用户和权限的用户管理示例，请参阅 [.NET SDK v3 用户管理示例](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/UserManagement/UserManagementProgram.cs)
