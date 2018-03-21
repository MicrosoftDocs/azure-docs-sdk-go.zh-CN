---
title: "通过 Go 部署 Azure 虚拟机"
description: "使用 Azure SDK for Go 部署虚拟机。"
keywords: "azure, 虚拟机, vm, go, golang, azure sdk"
author: sptramer
ms.author: sttramer
ms.date: 02/08/2018
ms.topic: quickstart
ms.devlang: go
manager: routlaw
ms.openlocfilehash: ae460dbf21b13c40f3d564274f8b790afe005aae
ms.sourcegitcommit: af3473779cd7c2978f290fbdc51ee15eb1130840
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/15/2018
---
# <a name="quickstart-deploy-an-azure-virtual-machine-from-a-template-with-the-azure-sdk-for-go"></a>快速入门：使用 Azure SDK for Go 从模板部署 Azure 虚拟机

本快速入门重点介绍如何使用 Azure SDK for Go 从模板部署资源。 模板是 [Azure 资源组](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-overview)中包含的所有资源的快照。 在执行有用任务的过程中，你将会不断熟悉该 SDK 的功能和约定。

在本快速入门结束时，将会运行一个可以使用用户名和密码登录的 VM。

[!INCLUDE [quickstarts-free-trial-note](includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](includes/cloud-shell-try-it.md)]

如果使用本地安装的 Azure CLI，本快速入门需要 CLI 2.0.24 或更高版本。 请运行 `az --version`，确保 CLI 安装满足此要求。 如需安装或升级，请参阅[安装 Azure CLI 2.0](/cli/azure/install-azure-cli)。

## <a name="install-the-azure-sdk-for-go"></a>安装 Azure SDK for Go 

[!INCLUDE [azure-sdk-go-get](includes/azure-sdk-go-get.md)]

## <a name="create-a-service-principal"></a>创建服务主体

若要以非交互方式登录到应用程序，需要一个服务主体。 服务主体是基于角色的访问控制 (RBAC) 的一部分，该访问控制创建唯一用户标识。 若要使用 CLI 创建新的服务主体，请运行以下命令：

```azurecli-interactive
az ad sp create-for-rbac --name az-go-vm-quickstart
```

请务必记下输出中的 `appId`、`password` 和 `tenant` 值。 应用程序将使用这些值在 Azure 中进行身份验证。

有关使用 Azure CLI 2.0 创建和管理服务主体的详细信息，请参阅[使用 Azure CLI 2.0 创建 Azure 服务主体](/cli/azure/create-an-azure-service-principal-azure-cli)。

## <a name="get-the-code"></a>获取代码

使用 `go get` 获取快速入门代码及其所有依赖项。

```bash
go get -u -d github.com/azure-samples/azure-sdk-for-go-samples/quickstart/deploy-vm/...
```

此代码将会编译，但只有在为其提供有关 Azure 帐户和所创建的服务主体的信息之后，它才能正常运行。 `main.go` 中有一个包含 `authInfo` 结构的变量 `config`。 需要替换此结构的字段值才能正常进行身份验证。

```go
    config = authInfo{ // Your application credentials
        TenantID:               "", // Azure account tenantID
        SubscriptionID:         "", // Azure subscription subscriptionID
        ServicePrincipalID:     "", // Service principal appId
        ServicePrincipalSecret: "", // Service principal password/secret
    }
```

* `SubscriptionID`：订阅 ID，可以通过 CLI 命令获取

  ```azurecli-interactive
  az account show --query id -o tsv
  ```

* `TenantID`：租户 ID，即创建服务主体时记下的 `tenant` 值
* `ServicePrincipalID`：创建服务主体时记下的 `appId` 值
* `ServicePrincipalSecret`：创建服务主体时记下的 `password` 值

还需要编辑 `vm-quickstart-params.json` 文件中的某个值。

```json
    "vm_password": {
        "value": "_"
    }
```

* `vm_password`：VM 用户帐户的密码。 该密码的长度必须为 12-72 个字符，并且必须包含以下字符中的 3 种：
  * 小写字母
  * 大写字母
  * 数字
  * 符号

## <a name="running-the-code"></a>运行代码

使用 `go run` 命令运行快速入门。

```bash
cd $GOPATH/src/github.com/azure-samples/azure-sdk-for-go-samples/quickstart/deploy-vm
go run main.go
```

如果部署中存在错误，则会显示一条消息，指出发生了问题，但不会提供任何具体的详细信息。 在 Azure CLI 中使用以下命令获取部署失败详细信息：

```azurecli-interactive
az group deployment show -g GoVMQuickstart -n VMDeployQuickstart
```

如果部署成功，将会显示一条消息，其中提供了用于登录到新建虚拟机的用户名、IP 地址和密码。 通过 SSH 连接到此计算机，以确认它已启动并在运行。

## <a name="cleaning-up"></a>清理

使用 CLI 删除资源组，可以清理在学习本快速入门期间创建的资源。

```azurecli-interactive
az group delete -n GoVMQuickstart
```

## <a name="code-in-depth"></a>代码剖析

快速入门代码可细分为变量块和多个小函数，本部分将逐一介绍。

### <a name="variable-assignments-and-structs"></a>变量分配和结构

由于快速入门代码是自主性的，因此它使用了全局变量，而未使用命令行选项或环境变量。

```go
type authInfo struct {
        TenantID               string
        SubscriptionID         string
        ServicePrincipalID     string
        ServicePrincipalSecret string
}
```

声明了 `authInfo` 结构，以封装 Azure 服务授权所需的全部信息。

```go
const (
    resourceGroupName     = "GoVMQuickstart"
    resourceGroupLocation = "eastus"

    deploymentName = "VMDeployQuickstart"
    templateFile   = "vm-quickstart-template.json"
    parametersFile = "vm-quickstart-params.json"
)

var (
    config = authInfo{ // Your application credentials
        TenantID:               "", // Azure account tenantID
        SubscriptionID:         "", // Azure subscription subscriptionID
        ServicePrincipalID:     "", // Service principal appId
        ServicePrincipalSecret: "", // Service principal password/secret
    }

    ctx = context.Background()

    token *adal.ServicePrincipalToken
)
```

声明了指定所创建资源的名称的值。 此处还指定了位置，可对其进行更改，以查看部署在其他数据中心的行为方式。 并非每个数据中心都提供全部所需的资源。

`templateFile` 和 `parametersFile` 常量指向部署所需的文件。 稍后将介绍服务主体令牌。`ctx` 变量是网络操作的 [Go 上下文](https://blog.golang.org/context)。

### <a name="init-and-authorization"></a>init() 和授权

代码的 `init()` 方法设置授权。 由于授权是快速入门中所有操作的前提条件，因此，最好是在初始化过程中设置授权。 

```go
// Authenticate with the Azure services over OAuth, using a service principal.
func init() {
    oauthConfig, err := adal.NewOAuthConfig(azure.PublicCloud.ActiveDirectoryEndpoint, config.TenantID)
    if err != nil {
        log.Fatalf("Failed to get OAuth config: %v\n", err)
    }
    token, err = adal.NewServicePrincipalToken(
        *oauthConfig,
        config.ServicePrincipalID,
        config.ServicePrincipalSecret,
        azure.PublicCloud.ResourceManagerEndpoint)
    if err != nil {
        log.Fatalf("faled to get token: %v\n", err)
    }
}
```

此代码完成两个授权步骤：

* 通过与 Azure Active Directory 交互来检索 `TenantID` 的 OAuth 配置信息。 [`azure.PublicCloud`](https://godoc.org/github.com/Azure/go-autorest/autorest/azure#PublicCloud) 对象包含标准 Azure 配置中使用的终结点。
* 调用了 [`adal.NewServicePrincipalToken()`](https://godoc.org/github.com/Azure/go-autorest/autorest/adal#NewServicePrincipalToken) 函数。 此函数采用 OAuth 信息、服务主体登录名，以及所用的 Azure 管理样式。 除非有具体要求并且知道自己的意图，否则请始终将此值设置为 `.ResourceManagerEndpoint`。

### <a name="flow-of-operations-in-main"></a>main() 中的操作流

`main()` 函数十分简单，它仅指示操作流并执行错误检查。

```go
func main() {
    group, err := createGroup()
    if err != nil {
        log.Fatalf("failed to create group: %v", err)
    }
    log.Printf("created group: %v\n", *group.Name)

    log.Println("starting deployment")
    result, err := createDeployment()
    if err != nil {
        log.Fatalf("Failed to deploy correctly: %v", err)
    }
    log.Printf("Completed deployment: %v", *result.Name)
    getLogin()
}
```

该代码运行的步骤依次为：

* 创建要部署到的资源组 (`createGroup()`)
* 在此组中创建部署 (`createDeployment()`)
* 获取并显示已部署 VM 的登录信息 (`getLogin()`)

### <a name="creating-the-resource-group"></a>创建资源组

`createGroup()` 函数创建资源组。 以下调用流和参数演示了 SDK 中构建服务交互的方式。

```go
func createGroup() (group resources.Group, err error) {
        groupsClient := resources.NewGroupsClient(config.SubscriptionID)
        groupsClient.Authorizer = autorest.NewBearerAuthorizer(token)

        return groupsClient.CreateOrUpdate(
                ctx,
                resourceGroupName,
                resources.Group{
                        Location: to.StringPtr(resourceGroupLocation)})
}
```

与 Azure 服务交互的一般流程为：

* 使用 `service.NewXClient()` 方法创建客户端，其中，`X` 是要交互的 `service` 的资源类型。 此函数始终采用订阅 ID。
* 设置客户端的授权方法，使其能够与远程 API 交互。
* 地对应于远程 API 的客户端上发出方法调用。 服务客户端方法通常采用资源名称和元数据对象。

此处的 [`to.StringPtr()`](https://godoc.org/github.com/Azure/go-autorest/autorest/to#StringPtr) 函数用于执行类型转换。 SDK 方法的 parameters 结构几乎独占指针，因此，提供这些方法可以简化类型转换。 请参阅 [autorest/to](https://godoc.org/github.com/Azure/go-autorest/autorest/to) 的文档，了解便捷转换器的完整列表和行为。

`groupsClient.CreateOrUpdate()` 操作返回一个指针，该指针指向表示资源组的数据结构。 此类直接返回值表示一个同步的短时间运行操作。 下一部分将演示长时间运行的操作示例，以及如何与其交互。

### <a name="performing-the-deployment"></a>执行部署

创建用于包含资源的组后，可以运行部署。 此代码已分解成多个小段，以突出其逻辑的各个部分。


```go
func createDeployment() (deployment resources.DeploymentExtended, err error) {
    template, err := readJSON(templateFile)
    if err != nil {
        return
    }
    params, err := readJSON(parametersFile)
    if err != nil {
        return
    }

        // ...
```

部署文件由 `readJSON` 加载，此处不会讲解相关详细信息。 此函数返回 `*map[string]interface{}`，即构造资源部署调用的元数据时使用的类型。

```go
        // ...
        
        deploymentsClient := resources.NewDeploymentsClient(config.SubscriptionID)
        deploymentsClient.Authorizer = autorest.NewBearerAuthorizer(token)

        deploymentFuture, err := deploymentsClient.CreateOrUpdate(
                ctx,
                resourceGroupName,
                deploymentName,
                resources.Deployment{
                        Properties: &resources.DeploymentProperties{
                                Template:   template,
                                Parameters: params,
                                Mode:       resources.Incremental,
                        },
                },
        )
        if err != nil {
                log.Fatalf("Failed to create deployment: %v", err)
        }
        //...
```

此代码遵循的模式与创建资源组相同。 将创建新客户端以便能够在 Azure 中进行身份验证，然后调用一个方法。 该方法甚至与资源组的对应方法同名 (`CreateOrUpdate`)。 SDK 中反复出现了此模式。 执行类似工作的方法通常是同名的。

最大的差别在于 `deploymentsClient.CreateOrUpdate()` 方法的返回值。 此值是一个遵循 [Future 设计模式](https://en.wikipedia.org/wiki/Futures_and_promises)的 `Future` 对象。 Future 表示 Azure 中的、你在执行其他工作时偶尔想要轮询的长时间运行的操作。

```go
        //...
        err = deploymentFuture.Future.WaitForCompletion(ctx, deploymentsClient.BaseClient.Client)
        if err != nil {
                log.Fatalf("Error while waiting for deployment creation: %v", err)
        }
        return deploymentFuture.Result(deploymentsClient)
}
```

在此示例中，最恰当的做法就是等待该操作完成。 等待 Future 要求提供[上下文对象](https://blog.golang.org/context)，以及创建了 Future 对象的客户端。 此处有两个可能的错误来源：尝试调用方法时在客户端造成的错误，以及来自服务器的错误响应。 后者作为 `deploymentFuture.Result()` 调用的一部分返回。

### <a name="obtaining-the-assigned-ip-address"></a>获取分配的 IP 地址

若要使用新建的 VM 执行任何操作，需要使用分配的 IP 地址。 IP 地址是其自身的独立 Azure 资源，已绑定到网络接口控制器 (NIC) 资源。

```go
func getLogin() {
        params, err := readJSON(parametersFile)
        if err != nil {
                log.Fatalf("Unable to read parameters. Get login information with `az network public-ip list -g %s", resourceGroupName)
        }

        addressClient := network.NewPublicIPAddressesClient(config.SubscriptionID)
        addressClient.Authorizer = autorest.NewBearerAuthorizer(token)
        ipName := (*params)["publicIPAddresses_QuickstartVM_ip_name"].(map[string]interface{})
        ipAddress, err := addressClient.Get(ctx, resourceGroupName, ipName["value"].(string), "")
        if err != nil {
                log.Fatalf("Unable to get IP information. Try using `az network public-ip list -g %s", resourceGroupName)
        }

        vmUser := (*params)["vm_user"].(map[string]interface{})
        vmPass := (*params)["vm_password"].(map[string]interface{})

        log.Printf("Log in with ssh: %s@%s, password: %s",
                vmUser["value"].(string),
                *ipAddress.PublicIPAddressPropertiesFormat.IPAddress,
                vmPass["value"].(string))
}
```

此方法依赖于 parameters 文件中存储的信息。 该代码可以直接查询 VM 以获取 VM 的 NIC、查询 NIC 以获取 NIC 的 IP 资源，然后直接查询 IP 资源。 有一长串的依赖关系和操作需要解析，因此开销不菲。 由于 JSON 信息位于本地，可以改为加载此信息。

同理可从 JSON 加载 VM 用户和密码的值。

## <a name="next-steps"></a>后续步骤

在本快速入门中，我们编辑了现有的模板，并通过 Go 部署了该模板。 然后，通过 SSH 连接到了新建的 VM，以确保它在运行。

若要继续学习如何通过 Go 在 Azure 环境中使用虚拟机，请参阅[适用于 Go 的 Azure 计算示例](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/compute)，或[适用于 Go 的 Azure 资源管理示例](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/resources)。
