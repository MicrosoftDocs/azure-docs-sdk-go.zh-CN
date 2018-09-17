---
title: 通过 Go 部署 Azure 虚拟机
description: 使用 Azure SDK for Go 部署虚拟机。
author: sptramer
ms.author: sttramer
manager: carmonm
ms.date: 09/05/2018
ms.topic: quickstart
ms.technology: azure-sdk-go
ms.service: virtual-machines
ms.devlang: go
ms.openlocfilehash: a7970be0857fd414d776241b033af0c23457790c
ms.sourcegitcommit: 8b9e10b960150dc08f046ab840d6a5627410db29
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/07/2018
ms.locfileid: "44059129"
---
# <a name="quickstart-deploy-an-azure-virtual-machine-from-a-template-with-the-azure-sdk-for-go"></a><span data-ttu-id="9af5d-103">快速入门：使用 Azure SDK for Go 从模板部署 Azure 虚拟机</span><span class="sxs-lookup"><span data-stu-id="9af5d-103">Quickstart: Deploy an Azure virtual machine from a template with the Azure SDK for Go</span></span>

<span data-ttu-id="9af5d-104">本快速入门演示了如何使用 Azure SDK for Go 从 Azure 资源管理器模板部署资源。</span><span class="sxs-lookup"><span data-stu-id="9af5d-104">This quickstart shows you how to deploy resources from an Azure Resource Manager template, using the Azure SDK for Go.</span></span> <span data-ttu-id="9af5d-105">模板是 [Azure 资源组](/azure/azure-resource-manager/resource-group-overview)中所有资源的快照。</span><span class="sxs-lookup"><span data-stu-id="9af5d-105">Templates are snapshots of all of the resources within an [Azure resource group](/azure/azure-resource-manager/resource-group-overview).</span></span> <span data-ttu-id="9af5d-106">在此过程中，你将会不断熟悉该 SDK 的功能和约定。</span><span class="sxs-lookup"><span data-stu-id="9af5d-106">Along the way, you'll become familiar with the functionality and conventions of the SDK.</span></span>

<span data-ttu-id="9af5d-107">在本快速入门结束时，将会运行一个可以使用用户名和密码登录的 VM。</span><span class="sxs-lookup"><span data-stu-id="9af5d-107">At the end of this quickstart, you have a running VM that you log into with a username and password.</span></span>

> [!NOTE]
> <span data-ttu-id="9af5d-108">若要在不使用资源管理器模板的情况下在 Go 中创建 VM，请参阅[命令性示例](https://github.com/Azure-Samples/azure-sdk-for-go-samples/blob/master/compute/vm.go)，该示例演示如何使用 SDK构建和配置所有 VM 资源。</span><span class="sxs-lookup"><span data-stu-id="9af5d-108">To see the creation of a VM in Go without the use of a Resource Manager template, there is an [imperative sample](https://github.com/Azure-Samples/azure-sdk-for-go-samples/blob/master/compute/vm.go) demonstrating how to build and configure all VM resources with the SDK.</span></span> <span data-ttu-id="9af5d-109">使用此示例中的模板可以专注于 SDK 约定，而无需深入了解有关 Azure 服务体系结构的详细信息。</span><span class="sxs-lookup"><span data-stu-id="9af5d-109">Using a template in this sample allows a focus on SDK conventions without getting into too many details about Azure service architecture.</span></span>

[!INCLUDE [quickstarts-free-trial-note](includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](includes/cloud-shell-try-it.md)]

<span data-ttu-id="9af5d-110">如果使用本地安装的 Azure CLI，本快速入门需要 CLI __2.0.28__ 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="9af5d-110">If you use a local install of the Azure CLI, this quickstart requires CLI version __2.0.28__ or later.</span></span> <span data-ttu-id="9af5d-111">请运行 `az --version`，确保 CLI 安装满足此要求。</span><span class="sxs-lookup"><span data-stu-id="9af5d-111">Run `az --version` to make sure your CLI install meets this requirement.</span></span> <span data-ttu-id="9af5d-112">如需进行安装或升级，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。</span><span class="sxs-lookup"><span data-stu-id="9af5d-112">If you need to install or upgrade, see [Install the Azure CLI](/cli/azure/install-azure-cli).</span></span>

## <a name="install-the-azure-sdk-for-go"></a><span data-ttu-id="9af5d-113">安装 Azure SDK for Go</span><span class="sxs-lookup"><span data-stu-id="9af5d-113">Install the Azure SDK for Go</span></span>

[!INCLUDE [azure-sdk-go-get](includes/azure-sdk-go-get.md)]

## <a name="create-a-service-principal"></a><span data-ttu-id="9af5d-114">创建服务主体</span><span class="sxs-lookup"><span data-stu-id="9af5d-114">Create a service principal</span></span>

<span data-ttu-id="9af5d-115">若要以非交互方式通过应用程序登录到 Azure，需要一个服务主体。</span><span class="sxs-lookup"><span data-stu-id="9af5d-115">To sign in non-interactively to Azure with an application, you need a service principal.</span></span> <span data-ttu-id="9af5d-116">服务主体是基于角色的访问控制 (RBAC) 的一部分，该访问控制创建唯一用户标识。</span><span class="sxs-lookup"><span data-stu-id="9af5d-116">Service principals are part of role-based access control (RBAC), which creates a unique user identity.</span></span> <span data-ttu-id="9af5d-117">若要使用 CLI 创建新的服务主体，请运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="9af5d-117">To create a new service principal with the CLI, run the following command:</span></span>

```azurecli-interactive
az ad sp create-for-rbac --sdk-auth > quickstart.auth
```

<span data-ttu-id="9af5d-118">将环境变量 `AZURE_AUTH_LOCATION` 设为此文件的完整路径。</span><span class="sxs-lookup"><span data-stu-id="9af5d-118">Set the environment variable `AZURE_AUTH_LOCATION` to be the full path to this file.</span></span> <span data-ttu-id="9af5d-119">然后，SDK 会直接从此文件查找并读取凭据，而无需做出任何更改或者记录服务主体中的信息。</span><span class="sxs-lookup"><span data-stu-id="9af5d-119">Then the SDK locates and reads the credentials directly from this file, without you having to make any changes or record information from the service principal.</span></span>

## <a name="get-the-code"></a><span data-ttu-id="9af5d-120">获取代码</span><span class="sxs-lookup"><span data-stu-id="9af5d-120">Get the code</span></span>

<span data-ttu-id="9af5d-121">使用 `go get` 获取快速入门代码及其所有依赖项。</span><span class="sxs-lookup"><span data-stu-id="9af5d-121">Get the quickstart code and all of its dependencies with `go get`.</span></span>

```bash
go get -u -d github.com/azure-samples/azure-sdk-for-go-samples/quickstarts/deploy-vm/...
```

<span data-ttu-id="9af5d-122">如果正确设置了 `AZURE_AUTH_LOCATION` 变量，则无需进行任何源代码修改。</span><span class="sxs-lookup"><span data-stu-id="9af5d-122">You don't need to make any source code modifications if the `AZURE_AUTH_LOCATION` variable is properly set.</span></span> <span data-ttu-id="9af5d-123">当程序运行时，它会从此处加载所有必要的身份验证信息。</span><span class="sxs-lookup"><span data-stu-id="9af5d-123">When the program runs, it loads all the necessary authentication information from there.</span></span>

## <a name="running-the-code"></a><span data-ttu-id="9af5d-124">运行代码</span><span class="sxs-lookup"><span data-stu-id="9af5d-124">Running the code</span></span>

<span data-ttu-id="9af5d-125">使用 `go run` 命令运行快速入门。</span><span class="sxs-lookup"><span data-stu-id="9af5d-125">Run the quickstart with the `go run` command.</span></span>

```bash
cd $GOPATH/src/github.com/azure-samples/azure-sdk-for-go-samples/quickstarts/deploy-vm
go run main.go
```

<span data-ttu-id="9af5d-126">如果部署成功，将会显示一条消息，其中提供了用于登录到新建虚拟机的用户名、IP 地址和密码。</span><span class="sxs-lookup"><span data-stu-id="9af5d-126">If the deployment is successful, you see a message giving the username, IP address, and password for logging into the newly created virtual machine.</span></span> <span data-ttu-id="9af5d-127">通过 SSH 连接到此计算机，以查看它是否已启动并在运行。</span><span class="sxs-lookup"><span data-stu-id="9af5d-127">SSH into this machine to see if it's up and running.</span></span> 

## <a name="cleaning-up"></a><span data-ttu-id="9af5d-128">清理</span><span class="sxs-lookup"><span data-stu-id="9af5d-128">Cleaning up</span></span>

<span data-ttu-id="9af5d-129">使用 CLI 删除资源组，可以清理在学习本快速入门期间创建的资源。</span><span class="sxs-lookup"><span data-stu-id="9af5d-129">Clean up the resources created during this quickstart by deleting the resource group with the CLI.</span></span>

```azurecli-interactive
az group delete -n GoVMQuickstart
```

<span data-ttu-id="9af5d-130">同时删除已创建的服务主体。</span><span class="sxs-lookup"><span data-stu-id="9af5d-130">Also delete the service principal that was created.</span></span> <span data-ttu-id="9af5d-131">在 `quickstart.auth` 文件中，`clientId` 有一个 JSON 密钥。</span><span class="sxs-lookup"><span data-stu-id="9af5d-131">In the `quickstart.auth` file, there's a JSON key for `clientId`.</span></span> <span data-ttu-id="9af5d-132">将此值复制到 `CLIENT_ID_VALUE` 环境变量并运行以下 Azure CLI 命令：</span><span class="sxs-lookup"><span data-stu-id="9af5d-132">Copy this value to the `CLIENT_ID_VALUE` environment variable and run the following Azure CLI command:</span></span>

```azurecli-interactive
az ad sp delete --id ${CLIENT_ID_VALUE}
```

<span data-ttu-id="9af5d-133">其中你从 `quickstart.auth` 提供 `CLIENT_ID_VALUE` 的值。</span><span class="sxs-lookup"><span data-stu-id="9af5d-133">Where you supply the value for `CLIENT_ID_VALUE` from `quickstart.auth`.</span></span>

> [!WARNING]
> <span data-ttu-id="9af5d-134">未能删除此应用程序的服务主体将使其在 Azure Active Directory 租户中处于活动状态。</span><span class="sxs-lookup"><span data-stu-id="9af5d-134">Failing to delete the service principal for this application leaves it active in your Azure Active Directory tenant.</span></span>
> <span data-ttu-id="9af5d-135">虽然服务主体的名称和密码都是作为 UUID 生成的，但请确保通过删除任何未使用的服务主体和 Azure Active Directory 应用程序来遵循良好的安全做法。</span><span class="sxs-lookup"><span data-stu-id="9af5d-135">While both the name and password for the service principal are generated as UUIDs, make sure that you follow good security practices by deleting any unused service principals and Azure Active Directory Applications.</span></span>

## <a name="code-in-depth"></a><span data-ttu-id="9af5d-136">代码剖析</span><span class="sxs-lookup"><span data-stu-id="9af5d-136">Code in depth</span></span>

<span data-ttu-id="9af5d-137">快速入门代码可细分为变量块和多个小函数，本部分将逐一介绍。</span><span class="sxs-lookup"><span data-stu-id="9af5d-137">What the quickstart code does is broken down into a block of variables and several small functions, each of which are discussed here.</span></span>

### <a name="variables-constants-and-types"></a><span data-ttu-id="9af5d-138">变量、常量和类型</span><span class="sxs-lookup"><span data-stu-id="9af5d-138">Variables, constants, and types</span></span>

<span data-ttu-id="9af5d-139">由于快速入门代码是独立性的，因此使用了全局常量和变量。</span><span class="sxs-lookup"><span data-stu-id="9af5d-139">Since quickstart is self-contained, it uses global constants and variables.</span></span>

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

<span data-ttu-id="9af5d-140">声明了指定所创建资源的名称的值。</span><span class="sxs-lookup"><span data-stu-id="9af5d-140">Values are declared which give the names of created resources.</span></span> <span data-ttu-id="9af5d-141">此处还指定了位置，可对其进行更改，以查看部署在其他数据中心的行为方式。</span><span class="sxs-lookup"><span data-stu-id="9af5d-141">The location is also specified here, which you can change to see how deployments behave in other datacenters.</span></span> <span data-ttu-id="9af5d-142">并非每个数据中心都提供全部所需的资源。</span><span class="sxs-lookup"><span data-stu-id="9af5d-142">Not every datacenter has all of the required resources available.</span></span>

<span data-ttu-id="9af5d-143">`clientInfo` 类型保存从身份验证文件加载的信息，以便在 SDK 中设置客户端并设置 VM 密码。</span><span class="sxs-lookup"><span data-stu-id="9af5d-143">The `clientInfo` type holds the information loaded from the authentication file to set up clients in the SDK and set the VM password.</span></span>

<span data-ttu-id="9af5d-144">`templateFile` 和 `parametersFile` 常量指向部署所需的文件。</span><span class="sxs-lookup"><span data-stu-id="9af5d-144">The `templateFile` and `parametersFile` constants point to the files needed for deployment.</span></span> <span data-ttu-id="9af5d-145">Go SDK 将会配置 `authorizer` 以进行身份验证，`ctx` 变量是用于网络操作的 [Go 上下文](https://blog.golang.org/context)。</span><span class="sxs-lookup"><span data-stu-id="9af5d-145">The `authorizer` will be configured by the Go SDK for authentication, and the `ctx` variable is a [Go context](https://blog.golang.org/context) for the network operations.</span></span>

### <a name="authentication-and-initialization"></a><span data-ttu-id="9af5d-146">身份验证和初始化</span><span class="sxs-lookup"><span data-stu-id="9af5d-146">Authentication and initialization</span></span>

<span data-ttu-id="9af5d-147">`init` 函数设置身份验证。</span><span class="sxs-lookup"><span data-stu-id="9af5d-147">The `init` function sets up authentication.</span></span> <span data-ttu-id="9af5d-148">由于身份验证是快速入门中所有操作的前提条件，因此，最好是在初始化过程中设置身份验证。</span><span class="sxs-lookup"><span data-stu-id="9af5d-148">Since authentication is a precondition for everything in the quickstart, it makes sense to have it as part of initialization.</span></span> <span data-ttu-id="9af5d-149">身份验证还会从身份验证文件中加载一些所需的信息，以配置客户端和 VM。</span><span class="sxs-lookup"><span data-stu-id="9af5d-149">It also loads some information needed from the authentication file to configure clients and the VM.</span></span>

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

<span data-ttu-id="9af5d-150">首先，调用 [auth.NewAuthorizerFromFile](https://godoc.org/github.com/Azure/go-autorest/autorest/azure/auth#NewAuthorizerFromFile) 以从 `AZURE_AUTH_LOCATION` 中的文件加载身份验证信息。</span><span class="sxs-lookup"><span data-stu-id="9af5d-150">First, [auth.NewAuthorizerFromFile](https://godoc.org/github.com/Azure/go-autorest/autorest/azure/auth#NewAuthorizerFromFile) is called to load the authentication information from the file located at `AZURE_AUTH_LOCATION`.</span></span> <span data-ttu-id="9af5d-151">接下来，通过 `readJSON` 函数（此处已省略）手动加载此文件，以拉取以下两个所需值来运行程序的剩余部分：客户端的订阅 ID，以及服务主体的机密（也用作 VM 的密码）。</span><span class="sxs-lookup"><span data-stu-id="9af5d-151">Next, this file is loaded manually by the `readJSON` function (omitted here) to pull the two values needed to run the rest of the program: The subscription ID of the client, and the service principal's secret, which is also used for the VM's password.</span></span>

> [!WARNING]
> <span data-ttu-id="9af5d-152">为了简化快速入门的内容，我们重复使用了服务主体密码。</span><span class="sxs-lookup"><span data-stu-id="9af5d-152">To keep the quickstart simple, the service principal password is reused.</span></span> <span data-ttu-id="9af5d-153">请注意，在生产环境中，切勿重复使用可用于访问 Azure 资源的密码。</span><span class="sxs-lookup"><span data-stu-id="9af5d-153">In production, take care to __never__ reuse a password which gives access to your Azure resources.</span></span>

### <a name="flow-of-operations-in-main"></a><span data-ttu-id="9af5d-154">main() 中的操作流</span><span class="sxs-lookup"><span data-stu-id="9af5d-154">Flow of operations in main()</span></span>

<span data-ttu-id="9af5d-155">`main` 函数十分简单，它仅指示操作流并执行错误检查。</span><span class="sxs-lookup"><span data-stu-id="9af5d-155">The `main` function is simple, only indicating the flow of operations and performing error-checking.</span></span>

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

<span data-ttu-id="9af5d-156">该代码运行的步骤依次为：</span><span class="sxs-lookup"><span data-stu-id="9af5d-156">The steps that the code runs through are, in order:</span></span>

* <span data-ttu-id="9af5d-157">创建要部署到的资源组 (`createGroup`)</span><span class="sxs-lookup"><span data-stu-id="9af5d-157">Create the resource group to deploy to (`createGroup`)</span></span>
* <span data-ttu-id="9af5d-158">在此组中创建部署 (`createDeployment`)</span><span class="sxs-lookup"><span data-stu-id="9af5d-158">Create the deployment within this group (`createDeployment`)</span></span>
* <span data-ttu-id="9af5d-159">获取并显示已部署 VM 的登录信息 (`getLogin`)</span><span class="sxs-lookup"><span data-stu-id="9af5d-159">Get and display login information for the deployed VM (`getLogin`)</span></span>

### <a name="create-the-resource-group"></a><span data-ttu-id="9af5d-160">创建资源组</span><span class="sxs-lookup"><span data-stu-id="9af5d-160">Create the resource group</span></span>

<span data-ttu-id="9af5d-161">`createGroup` 函数创建资源组。</span><span class="sxs-lookup"><span data-stu-id="9af5d-161">The `createGroup` function creates the resource group.</span></span> <span data-ttu-id="9af5d-162">以下调用流和参数演示了 SDK 中构建服务交互的方式。</span><span class="sxs-lookup"><span data-stu-id="9af5d-162">Looking at the call flow and arguments demonstrates the way that service interactions are structured in the SDK.</span></span>

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

<span data-ttu-id="9af5d-163">与 Azure 服务交互的一般流程为：</span><span class="sxs-lookup"><span data-stu-id="9af5d-163">The general flow of interacting with an Azure service is:</span></span>

* <span data-ttu-id="9af5d-164">使用 `service.New*Client()` 方法创建客户端，其中，`*` 是要交互的 `service` 的资源类型。</span><span class="sxs-lookup"><span data-stu-id="9af5d-164">Create the client using the `service.New*Client()` method, where `*` is the resource type of the `service` that you want to interact with.</span></span> <span data-ttu-id="9af5d-165">此函数始终采用订阅 ID。</span><span class="sxs-lookup"><span data-stu-id="9af5d-165">This function always takes a subscription ID.</span></span>
* <span data-ttu-id="9af5d-166">设置客户端的授权方法，使其能够与远程 API 交互。</span><span class="sxs-lookup"><span data-stu-id="9af5d-166">Set the authorization method for the client, allowing it to interact with the remote API.</span></span>
* <span data-ttu-id="9af5d-167">地对应于远程 API 的客户端上发出方法调用。</span><span class="sxs-lookup"><span data-stu-id="9af5d-167">Make the method call on the client corresponding to the remote API.</span></span> <span data-ttu-id="9af5d-168">服务客户端方法通常采用资源名称和元数据对象。</span><span class="sxs-lookup"><span data-stu-id="9af5d-168">Service client methods usually take the name of the resource and a metadata object.</span></span>

<span data-ttu-id="9af5d-169">此处的 [`to.StringPtr`](https://godoc.org/github.com/Azure/go-autorest/autorest/to#StringPtr) 函数用于执行类型转换。</span><span class="sxs-lookup"><span data-stu-id="9af5d-169">The [`to.StringPtr`](https://godoc.org/github.com/Azure/go-autorest/autorest/to#StringPtr) function is used to perform a type conversion here.</span></span> <span data-ttu-id="9af5d-170">SDK 方法的参数几乎独占指针，因此，我们提供了便捷的方法来简化类型转换。</span><span class="sxs-lookup"><span data-stu-id="9af5d-170">The parameters for SDK methods almost exclusively take pointers, so convenience methods are provided to make the type conversions easy.</span></span> <span data-ttu-id="9af5d-171">请参阅 [autorest/to](https://godoc.org/github.com/Azure/go-autorest/autorest/to) 模块的文档，了解便捷转换器的完整列表及其行为。</span><span class="sxs-lookup"><span data-stu-id="9af5d-171">See the documentation for the [autorest/to](https://godoc.org/github.com/Azure/go-autorest/autorest/to) module for the complete list of convenience converters and their behavior.</span></span>

<span data-ttu-id="9af5d-172">`groupsClient.CreateOrUpdate` 方法返回一个指针，该指针指向表示资源组的数据类型。</span><span class="sxs-lookup"><span data-stu-id="9af5d-172">The `groupsClient.CreateOrUpdate` method returns a pointer to a data type representing the resource group.</span></span> <span data-ttu-id="9af5d-173">此类直接返回值表示一个同步的短时间运行操作。</span><span class="sxs-lookup"><span data-stu-id="9af5d-173">A direct return value of this kind indicates a short-running operation that is meant to be synchronous.</span></span> <span data-ttu-id="9af5d-174">下一部分将演示长时间运行的操作示例，以及如何与其交互。</span><span class="sxs-lookup"><span data-stu-id="9af5d-174">In the next section, you'll see an example of a long-running operation and how to interact with it.</span></span>

### <a name="perform-the-deployment"></a><span data-ttu-id="9af5d-175">执行部署</span><span class="sxs-lookup"><span data-stu-id="9af5d-175">Perform the deployment</span></span>

<span data-ttu-id="9af5d-176">创建资源组后，可以运行部署。</span><span class="sxs-lookup"><span data-stu-id="9af5d-176">Once the resource group is created, it's time to run the deployment.</span></span> <span data-ttu-id="9af5d-177">此代码已分解成多个小段，以突出其逻辑的各个部分。</span><span class="sxs-lookup"><span data-stu-id="9af5d-177">This code is broken up into smaller sections to emphasize different parts of its logic.</span></span>

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

<span data-ttu-id="9af5d-178">部署文件由 `readJSON` 加载，此处不会讲解相关详细信息。</span><span class="sxs-lookup"><span data-stu-id="9af5d-178">The deployment files are loaded by `readJSON`, the details of which are skipped here.</span></span> <span data-ttu-id="9af5d-179">此函数返回 `*map[string]interface{}`，即构造资源部署调用的元数据时使用的类型。</span><span class="sxs-lookup"><span data-stu-id="9af5d-179">This function returns a `*map[string]interface{}`, the type used in constructing the metadata for the resource deployment call.</span></span> <span data-ttu-id="9af5d-180">VM 的密码也是在部署参数中手动设置的。</span><span class="sxs-lookup"><span data-stu-id="9af5d-180">The VM's password is also set manually on the deployment parameters.</span></span>

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

<span data-ttu-id="9af5d-181">此代码遵循的模式与创建资源组相同。</span><span class="sxs-lookup"><span data-stu-id="9af5d-181">This code follows the same pattern as creating the resource group.</span></span> <span data-ttu-id="9af5d-182">将创建新客户端以便能够在 Azure 中进行身份验证，然后调用一个方法。</span><span class="sxs-lookup"><span data-stu-id="9af5d-182">A new client is created, given the ability to authenticate with Azure, and then a method is called.</span></span>
<span data-ttu-id="9af5d-183">该方法甚至与资源组的对应方法同名 (`CreateOrUpdate`)。</span><span class="sxs-lookup"><span data-stu-id="9af5d-183">The method even has the same name (`CreateOrUpdate`) as the corresponding method for resource groups.</span></span> <span data-ttu-id="9af5d-184">在整个 SDK 中都可以看到此模式。</span><span class="sxs-lookup"><span data-stu-id="9af5d-184">This pattern is seen throughout the SDK.</span></span>
<span data-ttu-id="9af5d-185">执行类似工作的方法通常是同名的。</span><span class="sxs-lookup"><span data-stu-id="9af5d-185">Methods that perform similar work normally have the same name.</span></span>

<span data-ttu-id="9af5d-186">最大的差别在于 `deploymentsClient.CreateOrUpdate` 方法的返回值。</span><span class="sxs-lookup"><span data-stu-id="9af5d-186">The biggest difference comes in the return value of the `deploymentsClient.CreateOrUpdate` method.</span></span> <span data-ttu-id="9af5d-187">此值的类型为 [Future](https://godoc.org/github.com/Azure/go-autorest/autorest/azure#Future)，该类型遵循 [Future 设计模式](https://en.wikipedia.org/wiki/Futures_and_promises)。</span><span class="sxs-lookup"><span data-stu-id="9af5d-187">This value is of the [Future](https://godoc.org/github.com/Azure/go-autorest/autorest/azure#Future) type, which follows the [future design pattern](https://en.wikipedia.org/wiki/Futures_and_promises).</span></span> <span data-ttu-id="9af5d-188">Future 表示 Azure 中长时间运行的，可以轮询、取消或阻止其完成的操作。</span><span class="sxs-lookup"><span data-stu-id="9af5d-188">Futures represent a long-running operation in Azure that you can poll, cancel, or block on their completion.</span></span>

```go
        //...
    err = deploymentFuture.Future.WaitForCompletion(ctx, deploymentsClient.BaseClient.Client)
    if err != nil {
        return
    }
    return deploymentFuture.Result(deploymentsClient)
}
```

<span data-ttu-id="9af5d-189">在此示例中，最恰当的做法就是等待该操作完成。</span><span class="sxs-lookup"><span data-stu-id="9af5d-189">For this example, the best thing to do is to wait for the operation to complete.</span></span> <span data-ttu-id="9af5d-190">等待 Future 要求提供[上下文对象](https://blog.golang.org/context)，以及创建了 `Future` 的客户端。</span><span class="sxs-lookup"><span data-stu-id="9af5d-190">Waiting on a future requires both a [context object](https://blog.golang.org/context) and the client that created the `Future`.</span></span> <span data-ttu-id="9af5d-191">此处有两个可能的错误来源：尝试调用方法时在客户端造成的错误，以及来自服务器的错误响应。</span><span class="sxs-lookup"><span data-stu-id="9af5d-191">There are two possible error sources here: An error caused on the client side when trying to invoke the method, and an error response from the server.</span></span> <span data-ttu-id="9af5d-192">后者作为 `deploymentFuture.Result` 调用的一部分返回。</span><span class="sxs-lookup"><span data-stu-id="9af5d-192">The latter is returned as part of the `deploymentFuture.Result` call.</span></span>

### <a name="get-the-assigned-ip-address"></a><span data-ttu-id="9af5d-193">获取分配的 IP 地址</span><span class="sxs-lookup"><span data-stu-id="9af5d-193">Get the assigned IP address</span></span>

<span data-ttu-id="9af5d-194">若要使用新建的 VM 执行任何操作，需要使用分配的 IP 地址。</span><span class="sxs-lookup"><span data-stu-id="9af5d-194">To do anything with the newly created VM, you need the assigned IP address.</span></span> <span data-ttu-id="9af5d-195">IP 地址是其自身的独立 Azure 资源，已绑定到网络接口控制器 (NIC) 资源。</span><span class="sxs-lookup"><span data-stu-id="9af5d-195">IP addresses are their own separate Azure resource, bound to Network Interface Controller (NIC) resources.</span></span>

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

<span data-ttu-id="9af5d-196">此方法依赖于 parameters 文件中存储的信息。</span><span class="sxs-lookup"><span data-stu-id="9af5d-196">This method relies on information that is stored in the parameters file.</span></span> <span data-ttu-id="9af5d-197">该代码可以直接查询 VM 以获取 VM 的 NIC、查询 NIC 以获取 NIC 的 IP 资源，然后直接查询 IP 资源。</span><span class="sxs-lookup"><span data-stu-id="9af5d-197">The code could query the VM directly to get its NIC, query the NIC to get its IP resource, and then query the IP resource directly.</span></span> <span data-ttu-id="9af5d-198">有一长串的依赖关系和操作需要解析，因此开销不菲。</span><span class="sxs-lookup"><span data-stu-id="9af5d-198">That's a long chain of dependencies and operations to resolve, making it expensive.</span></span> <span data-ttu-id="9af5d-199">由于 JSON 信息位于本地，可以改为加载此信息。</span><span class="sxs-lookup"><span data-stu-id="9af5d-199">Since the JSON information is local, it can be loaded instead.</span></span>

<span data-ttu-id="9af5d-200">此外，可以从 JSON 加载 VM 用户的值。</span><span class="sxs-lookup"><span data-stu-id="9af5d-200">The value for the VM user is also loaded from the JSON.</span></span> <span data-ttu-id="9af5d-201">前面已从身份验证文件加载 VM 密码。</span><span class="sxs-lookup"><span data-stu-id="9af5d-201">The VM password was loaded earlier from the authentication file.</span></span>

## <a name="next-steps"></a><span data-ttu-id="9af5d-202">后续步骤</span><span class="sxs-lookup"><span data-stu-id="9af5d-202">Next steps</span></span>

<span data-ttu-id="9af5d-203">在本快速入门中，我们编辑了现有的模板，并通过 Go 部署了该模板。</span><span class="sxs-lookup"><span data-stu-id="9af5d-203">In this quickstart, you took an existing template and deployed it through Go.</span></span> <span data-ttu-id="9af5d-204">然后，通过 SSH 连接到了新建的 VM。</span><span class="sxs-lookup"><span data-stu-id="9af5d-204">Then you connected to the newly created VM via SSH.</span></span>

<span data-ttu-id="9af5d-205">若要继续学习如何通过 Go 在 Azure 环境中使用虚拟机，请参阅[适用于 Go 的 Azure 计算示例](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/compute)，或[适用于 Go 的 Azure 资源管理示例](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/resources)。</span><span class="sxs-lookup"><span data-stu-id="9af5d-205">To continue learning about working with virtual machines in the Azure environment with Go, take a look at the [Azure compute samples for Go](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/compute) or [Azure resource management samples for Go](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/resources).</span></span>

<span data-ttu-id="9af5d-206">若要详细了解 SDK 中提供的身份验证方法及其支持的身份验证类型，请参阅[使用 Azure SDK for Go 进行身份验证](azure-sdk-go-authorization.md)。</span><span class="sxs-lookup"><span data-stu-id="9af5d-206">To learn more about the available authentication methods in the SDK, and which authentication types they support, see [Authentication with the Azure SDK for Go](azure-sdk-go-authorization.md).</span></span>
