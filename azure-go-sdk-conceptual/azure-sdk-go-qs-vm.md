---
title: 通过 Go 部署 Azure 虚拟机
description: 使用 Azure SDK for Go 部署虚拟机。
author: sptramer
ms.author: sttramer
manager: carmonm
ms.date: 07/13/2018
ms.topic: quickstart
ms.prod: azure
ms.technology: azure-sdk-go
ms.service: virtual-machines
ms.devlang: go
ms.openlocfilehash: 6b1de35748fb7694d45715fa7f028d5730530d2e
ms.sourcegitcommit: d1790b317a8fcb4d672c654dac2a925a976589d4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/14/2018
ms.locfileid: "39039550"
---
# <a name="quickstart-deploy-an-azure-virtual-machine-from-a-template-with-the-azure-sdk-for-go"></a>快速入门：使用 Azure SDK for Go 从模板部署 Azure 虚拟机

本快速入门重点介绍如何使用 Azure SDK for Go 从模板部署资源。 模板是 [Azure 资源组](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)中包含的所有资源的快照。 在执行有用任务的过程中，你将会不断熟悉该 SDK 的功能和约定。

在本快速入门结束时，将会运行一个可以使用用户名和密码登录的 VM。

[!INCLUDE [quickstarts-free-trial-note](includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](includes/cloud-shell-try-it.md)]

如果使用本地安装的 Azure CLI，本快速入门需要 CLI __2.0.28__ 或更高版本。 请运行 `az --version`，确保 CLI 安装满足此要求。 如需安装或升级，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。

## <a name="install-the-azure-sdk-for-go"></a>安装 Azure SDK for Go

[!INCLUDE [azure-sdk-go-get](includes/azure-sdk-go-get.md)]

## <a name="create-a-service-principal"></a>创建服务主体

若要以非交互方式通过应用程序登录到 Azure，需要一个服务主体。 服务主体是基于角色的访问控制 (RBAC) 的一部分，该访问控制创建唯一用户标识。 若要使用 CLI 创建新的服务主体，请运行以下命令：

```azurecli-interactive
az ad sp create-for-rbac --name az-go-vm-quickstart --sdk-auth > quickstart.auth
```

将环境变量 `AZURE_AUTH_LOCATION` 设为此文件的完整路径。 然后，SDK 会直接从此文件查找并读取凭据，而无需做出任何更改或者记录服务主体中的信息。

## <a name="get-the-code"></a>获取代码

使用 `go get` 获取快速入门代码及其所有依赖项。

```bash
go get -u -d github.com/azure-samples/azure-sdk-for-go-samples/quickstarts/deploy-vm/...
```

如果正确设置了 `AZURE_AUTH_LOCATION` 变量，则无需进行任何源代码修改。 当程序运行时，它会从此处加载所有必要的身份验证信息。

## <a name="running-the-code"></a>运行代码

使用 `go run` 命令运行快速入门。

```bash
cd $GOPATH/src/github.com/azure-samples/azure-sdk-for-go-samples/quickstarts/deploy-vm
go run main.go
```

如果部署发生失败，则会显示一条消息，指出发生了问题，但可能不会包含足够的详细信息。 在 Azure CLI 中使用以下命令获取部署失败的完整详细信息：

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

### <a name="variables-constants-and-types"></a>变量、常量和类型

由于快速入门代码是独立性的，因此使用了全局常量和变量。

```go
const (
    resourceGroupName     = "GoVMQuickstart"
    resourceGroupLocation = "eastus"

    deploymentName = "VMDeployQuickstart"
    templateFile   = "vm-quickstart-template.json"
    parametersFile = "vm-quickstart-params.json"
)

// Information loaded from the authorization file to identify the client
type clientInfo struct {
    SubscriptionID string
    VMPassword     string
}

var (
    ctx        = context.Background()
    clientData clientInfo
    authorizer autorest.Authorizer
)
```

声明了指定所创建资源的名称的值。 此处还指定了位置，可对其进行更改，以查看部署在其他数据中心的行为方式。 并非每个数据中心都提供全部所需的资源。

声明 `clientInfo` 类型的目的是封装必须从身份验证文件独立加载的所有信息，以便在 SDK 中设置客户端，以及设置 VM 密码。

`templateFile` 和 `parametersFile` 常量指向部署所需的文件。 Go SDK 将会配置 `authorizer` 以进行身份验证，`ctx` 变量是用于网络操作的 [Go 上下文](https://blog.golang.org/context)。

### <a name="authentication-and-initialization"></a>身份验证和初始化

`init` 函数设置身份验证。 由于身份验证是快速入门中所有操作的前提条件，因此，最好是在初始化过程中设置身份验证。 身份验证还会从身份验证文件中加载一些所需的信息，以配置客户端和 VM。

```go
func init() {
    var err error
    authorizer, err = auth.NewAuthorizerFromFile(azure.PublicCloud.ResourceManagerEndpoint)
    if err != nil {
        log.Fatalf("Failed to get OAuth config: %v", err)
    }

    authInfo, err := readJSON(os.Getenv("AZURE_AUTH_LOCATION"))
    clientData.SubscriptionID = (*authInfo)["subscriptionId"].(string)
    clientData.VMPassword = (*authInfo)["clientSecret"].(string)
}
```

首先，调用 [auth.NewAuthorizerFromFile](https://godoc.org/github.com/Azure/go-autorest/autorest/azure/auth#NewAuthorizerFromFile) 以从 `AZURE_AUTH_LOCATION` 中的文件加载身份验证信息。 接下来，通过 `readJSON` 函数（此处已省略）手动加载此文件，以拉取以下两个所需值来运行程序的剩余部分：客户端的订阅 ID，以及服务主体的机密（也用作 VM 的密码）。

> [!WARNING]
> 为了简化快速入门的内容，我们重复使用了服务主体密码。 请注意，在生产环境中，切勿重复使用可用于访问 Azure 资源的密码。

### <a name="flow-of-operations-in-main"></a>main() 中的操作流

`main` 函数十分简单，它仅指示操作流并执行错误检查。

```go
func main() {
    group, err := createGroup()
    if err != nil {
        log.Fatalf("failed to create group: %v", err)
    }
    log.Printf("Created group: %v", *group.Name)

    log.Printf("Starting deployment: %s", deploymentName)
    result, err := createDeployment()
    if err != nil {
        log.Fatalf("Failed to deploy: %v", err)
    }
    if result.Name != nil {
        log.Printf("Completed deployment %v: %v", deploymentName, *result.Properties.ProvisioningState)
    } else {
        log.Printf("Completed deployment %v (no data returned to SDK)", deploymentName)
    }
    getLogin()
}
```

该代码运行的步骤依次为：

* 创建要部署到的资源组 (`createGroup`)
* 在此组中创建部署 (`createDeployment`)
* 获取并显示已部署 VM 的登录信息 (`getLogin`)

### <a name="creating-the-resource-group"></a>创建资源组

`createGroup` 函数创建资源组。 以下调用流和参数演示了 SDK 中构建服务交互的方式。

```go
func createGroup() (group resources.Group, err error) {
    groupsClient := resources.NewGroupsClient(clientData.SubscriptionID)
    groupsClient.Authorizer = authorizer

        return groupsClient.CreateOrUpdate(
                ctx,
                resourceGroupName,
                resources.Group{
                        Location: to.StringPtr(resourceGroupLocation)})
}
```

与 Azure 服务交互的一般流程为：

* 使用 `service.New*Client()` 方法创建客户端，其中，`*` 是要交互的 `service` 的资源类型。 此函数始终采用订阅 ID。
* 设置客户端的授权方法，使其能够与远程 API 交互。
* 地对应于远程 API 的客户端上发出方法调用。 服务客户端方法通常采用资源名称和元数据对象。

此处的 [`to.StringPtr`](https://godoc.org/github.com/Azure/go-autorest/autorest/to#StringPtr) 函数用于执行类型转换。 SDK 方法的参数几乎独占指针，因此，我们提供了便捷的方法来简化类型转换。 请参阅 [autorest/to](https://godoc.org/github.com/Azure/go-autorest/autorest/to) 模块的文档，了解便捷转换器的完整列表及其行为。

`groupsClient.CreateOrUpdate` 方法返回一个指针，该指针指向表示资源组的数据类型。 此类直接返回值表示一个同步的短时间运行操作。 下一部分将演示长时间运行的操作示例，以及如何与其交互。

### <a name="performing-the-deployment"></a>执行部署

创建资源组后，可以运行部署。 此代码已分解成多个小段，以突出其逻辑的各个部分。

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
    (*params)["vm_password"] = map[string]string{
        "value": clientData.VMPassword,
    }
        // ...
```

部署文件由 `readJSON` 加载，此处不会讲解相关详细信息。 此函数返回 `*map[string]interface{}`，即构造资源部署调用的元数据时使用的类型。 VM 的密码也是在部署参数中手动设置的。

```go
        // ...

    deploymentsClient := resources.NewDeploymentsClient(clientData.SubscriptionID)
    deploymentsClient.Authorizer = authorizer

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
        return
    }
```

此代码遵循的模式与创建资源组相同。 将创建新客户端以便能够在 Azure 中进行身份验证，然后调用一个方法。
该方法甚至与资源组的对应方法同名 (`CreateOrUpdate`)。 在整个 SDK 中都可以看到此模式。
执行类似工作的方法通常是同名的。

最大的差别在于 `deploymentsClient.CreateOrUpdate` 方法的返回值。 此值的类型为 [Future](https://godoc.org/github.com/Azure/go-autorest/autorest/azure#Future)，该类型遵循 [Future 设计模式](https://en.wikipedia.org/wiki/Futures_and_promises)。 Future 表示 Azure 中长时间运行的，可以轮询、取消或阻止其完成的操作。

```go
        //...
    err = deploymentFuture.Future.WaitForCompletion(ctx, deploymentsClient.BaseClient.Client)
    if err != nil {
        return
    }
    deployment, err = deploymentFuture.Result(deploymentsClient)

    // Work around possible bugs or late-stage failures
    if deployment.Name == nil || err != nil {
        deployment, _ = deploymentsClient.Get(ctx, resourceGroupName, deploymentName)
    }
    return
```

在此示例中，最恰当的做法就是等待该操作完成。 等待 Future 要求提供[上下文对象](https://blog.golang.org/context)，以及创建了 `Future` 的客户端。 此处有两个可能的错误来源：尝试调用方法时在客户端造成的错误，以及来自服务器的错误响应。 后者作为 `deploymentFuture.Result` 调用的一部分返回。

检索部署信息后，可通过某种方法来解决可能的 bug：通过手动调用 `deploymentsClient.Get` 来清空部署信息，以确保填充数据。

### <a name="obtaining-the-assigned-ip-address"></a>获取分配的 IP 地址

若要使用新建的 VM 执行任何操作，需要使用分配的 IP 地址。 IP 地址是其自身的独立 Azure 资源，已绑定到网络接口控制器 (NIC) 资源。

```go
func getLogin() {
    params, err := readJSON(parametersFile)
    if err != nil {
        log.Fatalf("Unable to read parameters. Get login information with `az network public-ip list -g %s", resourceGroupName)
    }

    addressClient := network.NewPublicIPAddressesClient(clientData.SubscriptionID)
    addressClient.Authorizer = authorizer
    ipName := (*params)["publicIPAddresses_QuickstartVM_ip_name"].(map[string]interface{})
    ipAddress, err := addressClient.Get(ctx, resourceGroupName, ipName["value"].(string), "")
    if err != nil {
        log.Fatalf("Unable to get IP information. Try using `az network public-ip list -g %s", resourceGroupName)
    }

    vmUser := (*params)["vm_user"].(map[string]interface{})

    log.Printf("Log in with ssh: %s@%s, password: %s",
        vmUser["value"].(string),
        *ipAddress.PublicIPAddressPropertiesFormat.IPAddress,
        clientData.VMPassword)
}
```

此方法依赖于 parameters 文件中存储的信息。 该代码可以直接查询 VM 以获取 VM 的 NIC、查询 NIC 以获取 NIC 的 IP 资源，然后直接查询 IP 资源。 有一长串的依赖关系和操作需要解析，因此开销不菲。 由于 JSON 信息位于本地，可以改为加载此信息。

此外，可以从 JSON 加载 VM 用户的值。 前面已从身份验证文件加载 VM 密码。

## <a name="next-steps"></a>后续步骤

在本快速入门中，我们编辑了现有的模板，并通过 Go 部署了该模板。 然后，通过 SSH 连接到了新建的 VM，以确保它在运行。

若要继续学习如何通过 Go 在 Azure 环境中使用虚拟机，请参阅[适用于 Go 的 Azure 计算示例](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/compute)，或[适用于 Go 的 Azure 资源管理示例](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/resources)。

若要详细了解 SDK 中提供的身份验证方法及其支持的身份验证类型，请参阅[使用 Azure SDK for Go 进行身份验证](azure-sdk-go-authorization.md)。
