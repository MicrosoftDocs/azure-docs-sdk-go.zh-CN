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
ms.openlocfilehash: e530d944deca40e9e6c29b6c2768e2367822714e
ms.sourcegitcommit: aaa8c37880332625f858a38f5918e6cf581bf48d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/15/2018
---
# <a name="quickstart-deploy-an-azure-virtual-machine-from-a-template-with-the-azure-sdk-for-go"></a><span data-ttu-id="73a55-104">快速入门：使用 Azure SDK for Go 从模板部署 Azure 虚拟机</span><span class="sxs-lookup"><span data-stu-id="73a55-104">Quickstart: Deploy an Azure virtual machine from a template with the Azure SDK for Go</span></span>

<span data-ttu-id="73a55-105">本快速入门重点介绍如何使用 Azure SDK for Go 从模板部署资源。</span><span class="sxs-lookup"><span data-stu-id="73a55-105">This quickstart focuses on deploying resources from a template with the Azure SDK for Go.</span></span> <span data-ttu-id="73a55-106">模板是 [Azure 资源组](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-overview)中包含的所有资源的快照。</span><span class="sxs-lookup"><span data-stu-id="73a55-106">Templates are snapshots of all of the resources contained within an [Azure resource group](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-overview).</span></span> <span data-ttu-id="73a55-107">在执行有用任务的过程中，你将会不断熟悉该 SDK 的功能和约定。</span><span class="sxs-lookup"><span data-stu-id="73a55-107">Along the way, you'll become familiar with the functionality and conventions of the SDK while performing a useful task.</span></span>

<span data-ttu-id="73a55-108">在本快速入门结束时，将会运行一个可以使用用户名和密码登录的 VM。</span><span class="sxs-lookup"><span data-stu-id="73a55-108">At the end of this quickstart, you have a running VM that you log into with a username and password.</span></span>

[!INCLUDE [quickstarts-free-trial-note](includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](includes/cloud-shell-try-it.md)]

<span data-ttu-id="73a55-109">如果使用本地安装的 Azure CLI，本快速入门需要 CLI 2.0.24 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="73a55-109">If you use a local install of the Azure CLI, this quickstart requires CLI version 2.0.24 or later.</span></span> <span data-ttu-id="73a55-110">请运行 `az --version`，确保 CLI 安装满足此要求。</span><span class="sxs-lookup"><span data-stu-id="73a55-110">Run `az --version` to make sure your CLI install meets this requirement.</span></span> <span data-ttu-id="73a55-111">如需安装或升级，请参阅[安装 Azure CLI 2.0](/cli/azure/install-azure-cli)。</span><span class="sxs-lookup"><span data-stu-id="73a55-111">If you need to install or upgrade, see [Install the Azure CLI 2.0](/cli/azure/install-azure-cli).</span></span>

## <a name="install-the-azure-sdk-for-go"></a><span data-ttu-id="73a55-112">安装 Azure SDK for Go</span><span class="sxs-lookup"><span data-stu-id="73a55-112">Install the Azure SDK for Go</span></span> 

[!INCLUDE [azure-sdk-go-get](includes/azure-sdk-go-get.md)]

## <a name="create-a-service-principal"></a><span data-ttu-id="73a55-113">创建服务主体</span><span class="sxs-lookup"><span data-stu-id="73a55-113">Create a service principal</span></span>

<span data-ttu-id="73a55-114">若要以非交互方式登录到应用程序，需要一个服务主体。</span><span class="sxs-lookup"><span data-stu-id="73a55-114">To log in non-interactively with an application, you need a service principal.</span></span> <span data-ttu-id="73a55-115">服务主体是创建唯一用户标识的基于角色的身份验证 (RBAC) 的一部分。</span><span class="sxs-lookup"><span data-stu-id="73a55-115">Service principals are part of Role-Based Authentication (RBAC), which creates a unique user identity.</span></span> <span data-ttu-id="73a55-116">若要使用 CLI 创建新的服务主体，请运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="73a55-116">To create a new service principal with the CLI, run the following command:</span></span>

```azurecli-interactive
az ad sp create-for-rbac --name az-go-vm-quickstart
```

<span data-ttu-id="73a55-117">请务必记下输出中的 `appId`、`password` 和 `tenant` 值。</span><span class="sxs-lookup"><span data-stu-id="73a55-117">__Make sure__ to record the `appId`, `password`, and `tenant` values in the output.</span></span> <span data-ttu-id="73a55-118">应用程序将使用这些值在 Azure 中进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="73a55-118">These values are used by the application to authenticate with Azure.</span></span>

<span data-ttu-id="73a55-119">有关使用 Azure CLI 2.0 创建和管理服务主体的详细信息，请参阅[使用 Azure CLI 2.0 创建 Azure 服务主体](/cli/azure/create-an-azure-service-principal-azure-cli)。</span><span class="sxs-lookup"><span data-stu-id="73a55-119">For more information on creating and managing Service Principals with the Azure CLI 2.0, see [Create an Azure service principal with Azure CLI 2.0](/cli/azure/create-an-azure-service-principal-azure-cli).</span></span>

## <a name="get-the-code"></a><span data-ttu-id="73a55-120">获取代码</span><span class="sxs-lookup"><span data-stu-id="73a55-120">Get the code</span></span>

<span data-ttu-id="73a55-121">使用 `go get` 获取快速入门代码及其所有依赖项。</span><span class="sxs-lookup"><span data-stu-id="73a55-121">Get the quickstart code and all of its dependencies with `go get`.</span></span>

```bash
go get -u -d github.com/azure-samples/azure-sdk-for-go-samples/quickstart/deploy-vm/...
```

<span data-ttu-id="73a55-122">此代码将会编译，但只有在为其提供有关 Azure 帐户和所创建的服务主体的信息之后，它才能正常运行。</span><span class="sxs-lookup"><span data-stu-id="73a55-122">This code compiles, but doesn't run correctly until you provide it information about your Azure account and the created service principal.</span></span> <span data-ttu-id="73a55-123">`main.go` 中有一个包含 `authInfo` 结构的变量 `config`。</span><span class="sxs-lookup"><span data-stu-id="73a55-123">In `main.go` there is a variable, `config`, which contains an `authInfo` struct.</span></span> <span data-ttu-id="73a55-124">需要替换此结构的字段值才能正常进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="73a55-124">This struct needs to have its field values replaced in order to authenticate correctly.</span></span>

```go
    config = authInfo{ // Your application credentials
        TenantID:               "", // Azure account tenantID
        SubscriptionID:         "", // Azure subscription subscriptionID
        ServicePrincipalID:     "", // Service principal appId
        ServicePrincipalSecret: "", // Service principal password/secret
    }
```

* <span data-ttu-id="73a55-125">`SubscriptionID`：订阅 ID，可以通过 CLI 命令获取</span><span class="sxs-lookup"><span data-stu-id="73a55-125">`SubscriptionID`: Your subscription ID, which can be obtained from the CLI command</span></span>

  ```azurecli-interactive
  az account show --query id -o tsv
  ```

* <span data-ttu-id="73a55-126">`TenantID`：租户 ID，即创建服务主体时记下的 `tenant` 值</span><span class="sxs-lookup"><span data-stu-id="73a55-126">`TenantID`: Your tenant ID, the `tenant` value recorded when creating the service principal</span></span>
* <span data-ttu-id="73a55-127">`ServicePrincipalID`：创建服务主体时记下的 `appId` 值</span><span class="sxs-lookup"><span data-stu-id="73a55-127">`ServicePrincipalID`: The `appId` value recorded when creating the service principal</span></span>
* <span data-ttu-id="73a55-128">`ServicePrincipalSecret`：创建服务主体时记下的 `password` 值</span><span class="sxs-lookup"><span data-stu-id="73a55-128">`ServicePrincipalSecret`: The `password` value recorded when creating the service principal</span></span>

<span data-ttu-id="73a55-129">还需要编辑 `vm-quickstart-params.json` 文件中的某个值。</span><span class="sxs-lookup"><span data-stu-id="73a55-129">You also need to edit a value in the `vm-quickstart-params.json` file.</span></span>

```json
    "vm_password": {
        "value": "_"
    }
```

* <span data-ttu-id="73a55-130">`vm_password`：VM 用户帐户的密码。</span><span class="sxs-lookup"><span data-stu-id="73a55-130">`vm_password`: The password for the VM user account.</span></span> <span data-ttu-id="73a55-131">该密码的长度必须为 6-72 个字符，并且必须包含以下字符中的 3 种：</span><span class="sxs-lookup"><span data-stu-id="73a55-131">It must be 6-72 characters in length and contain 3 of the following characters:</span></span>
  * <span data-ttu-id="73a55-132">小写字母</span><span class="sxs-lookup"><span data-stu-id="73a55-132">A lowercase letter</span></span>
  * <span data-ttu-id="73a55-133">大写字母</span><span class="sxs-lookup"><span data-stu-id="73a55-133">An uppercase letter</span></span>
  * <span data-ttu-id="73a55-134">数字</span><span class="sxs-lookup"><span data-stu-id="73a55-134">A number</span></span>
  * <span data-ttu-id="73a55-135">符号</span><span class="sxs-lookup"><span data-stu-id="73a55-135">A symbol</span></span>

## <a name="running-the-code"></a><span data-ttu-id="73a55-136">运行代码</span><span class="sxs-lookup"><span data-stu-id="73a55-136">Running the code</span></span>

<span data-ttu-id="73a55-137">使用 `go run` 命令运行快速入门。</span><span class="sxs-lookup"><span data-stu-id="73a55-137">Run the quickstart with the `go run` command.</span></span>

```bash
cd $GOPATH/src/github.com/azure-samples/azure-sdk-for-go-samples/quickstart/deploy-vm
go run main.go
```

<span data-ttu-id="73a55-138">如果部署中存在错误，则会显示一条消息，指出发生了问题，但不会提供任何具体的详细信息。</span><span class="sxs-lookup"><span data-stu-id="73a55-138">If there is a failure in the deployment, you get a message indicating that there was an issue, but without any specific details.</span></span> <span data-ttu-id="73a55-139">在 Azure CLI 中使用以下命令获取部署失败详细信息：</span><span class="sxs-lookup"><span data-stu-id="73a55-139">Using the Azure CLI, get the details of the deployment failure with the following command:</span></span>

```azurecli-interactive
az group deployment show -g GoVMQuickstart -n VMDeployQuickstart
```

<span data-ttu-id="73a55-140">如果部署成功，将会显示一条消息，其中提供了用于登录到新建虚拟机的用户名、IP 地址和密码。</span><span class="sxs-lookup"><span data-stu-id="73a55-140">If the deployment is successful, you see a message giving the username, IP address, and password for logging into the newly created virtual machine.</span></span> <span data-ttu-id="73a55-141">通过 SSH 连接到此计算机，以确认它已启动并在运行。</span><span class="sxs-lookup"><span data-stu-id="73a55-141">SSH into this machine to confirm that it is up and running.</span></span>

## <a name="cleaning-up"></a><span data-ttu-id="73a55-142">清理</span><span class="sxs-lookup"><span data-stu-id="73a55-142">Cleaning up</span></span>

<span data-ttu-id="73a55-143">使用 CLI 删除资源组，可以清理在学习本快速入门期间创建的资源。</span><span class="sxs-lookup"><span data-stu-id="73a55-143">Clean up the resources created during this quickstart by deleting the resource group with the CLI.</span></span>

```azurecli-interactive
az group delete -n GoVMQuickstart
```

## <a name="code-in-depth"></a><span data-ttu-id="73a55-144">代码剖析</span><span class="sxs-lookup"><span data-stu-id="73a55-144">Code in depth</span></span>

<span data-ttu-id="73a55-145">快速入门代码可细分为变量块和多个小函数，本部分将逐一介绍。</span><span class="sxs-lookup"><span data-stu-id="73a55-145">What the quickstart code does is broken down into a block of variables and several small functions, each of which are discussed here.</span></span>

### <a name="variable-assignments-and-structs"></a><span data-ttu-id="73a55-146">变量分配和结构</span><span class="sxs-lookup"><span data-stu-id="73a55-146">Variable assignments and structs</span></span>

<span data-ttu-id="73a55-147">由于快速入门代码是自主性的，因此它使用了全局变量，而未使用命令行选项或环境变量。</span><span class="sxs-lookup"><span data-stu-id="73a55-147">Since quickstart is self-contained, it uses global variables rather than command-line options or environment variables.</span></span>

```go
type authInfo struct {
        TenantID               string
        SubscriptionID         string
        ServicePrincipalID     string
        ServicePrincipalSecret string
}
```

<span data-ttu-id="73a55-148">声明了 `authInfo` 结构，以封装 Azure 服务授权所需的全部信息。</span><span class="sxs-lookup"><span data-stu-id="73a55-148">The `authInfo` struct is declared to encapsulate all of the information needed for authorization with Azure services.</span></span>

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

<span data-ttu-id="73a55-149">声明了指定所创建资源的名称的值。</span><span class="sxs-lookup"><span data-stu-id="73a55-149">Values are declared which give the names of created resources.</span></span> <span data-ttu-id="73a55-150">此处还指定了位置，可对其进行更改，以查看部署在其他数据中心的行为方式。</span><span class="sxs-lookup"><span data-stu-id="73a55-150">The location is also specified here, which you can change to see how deployments behave in other datacenters.</span></span> <span data-ttu-id="73a55-151">并非每个数据中心都提供全部所需的资源。</span><span class="sxs-lookup"><span data-stu-id="73a55-151">Not every datacenter has all of the required resources available.</span></span>

<span data-ttu-id="73a55-152">`templateFile` 和 `parametersFile` 常量指向部署所需的文件。</span><span class="sxs-lookup"><span data-stu-id="73a55-152">The `templateFile` and `parametersFile` constants point to the files needed for deployment.</span></span> <span data-ttu-id="73a55-153">稍后将介绍服务主体令牌。`ctx` 变量是网络操作的 [Go 上下文](https://blog.golang.org/context)。</span><span class="sxs-lookup"><span data-stu-id="73a55-153">The service principal token is covered later, and the `ctx` variable is a [Go context](https://blog.golang.org/context) for the network operations.</span></span>

### <a name="init-and-authorization"></a><span data-ttu-id="73a55-154">init() 和授权</span><span class="sxs-lookup"><span data-stu-id="73a55-154">init() and authorization</span></span>

<span data-ttu-id="73a55-155">代码的 `init()` 方法设置授权。</span><span class="sxs-lookup"><span data-stu-id="73a55-155">The `init()` method for the code sets up authorization.</span></span> <span data-ttu-id="73a55-156">由于授权是快速入门中所有操作的前提条件，因此，最好是在初始化过程中设置授权。</span><span class="sxs-lookup"><span data-stu-id="73a55-156">Since authorization is a precondition for everything in the quickstart, it makes sense to have it as part of initialization.</span></span> 

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

<span data-ttu-id="73a55-157">此代码完成两个授权步骤：</span><span class="sxs-lookup"><span data-stu-id="73a55-157">This code completes two steps for authorization:</span></span>

* <span data-ttu-id="73a55-158">通过与 Azure Active Directory 交互来检索 `TenantID` 的 OAuth 配置信息。</span><span class="sxs-lookup"><span data-stu-id="73a55-158">OAuth configuration information for the `TenantID` is retrieved by interfacing with Azure Active Directory.</span></span> <span data-ttu-id="73a55-159">[`azure.PublicCloud`](https://godoc.org/github.com/Azure/go-autorest/autorest/azure#PublicCloud) 对象包含标准 Azure 配置中使用的终结点。</span><span class="sxs-lookup"><span data-stu-id="73a55-159">The [`azure.PublicCloud`](https://godoc.org/github.com/Azure/go-autorest/autorest/azure#PublicCloud) object contains endpoints used in the standard Azure configuration.</span></span>
* <span data-ttu-id="73a55-160">调用了 [`adal.NewServicePrincipalToken()`](https://godoc.org/github.com/Azure/go-autorest/autorest/adal#NewServicePrincipalToken) 函数。</span><span class="sxs-lookup"><span data-stu-id="73a55-160">The [`adal.NewServicePrincipalToken()`](https://godoc.org/github.com/Azure/go-autorest/autorest/adal#NewServicePrincipalToken) function is called.</span></span> <span data-ttu-id="73a55-161">此函数采用 OAuth 信息、服务主体登录名，以及所用的 Azure 管理样式。</span><span class="sxs-lookup"><span data-stu-id="73a55-161">This function takes the OAuth information along with the service principal login, as well as which style of Azure management is being used.</span></span> <span data-ttu-id="73a55-162">除非有具体要求并且知道自己的意图，否则请始终将此值设置为 `.ResourceManagerEndpoint`。</span><span class="sxs-lookup"><span data-stu-id="73a55-162">Unless you have specific requirements and know what you're doing, this value should always be `.ResourceManagerEndpoint`.</span></span>

### <a name="flow-of-operations-in-main"></a><span data-ttu-id="73a55-163">main() 中的操作流</span><span class="sxs-lookup"><span data-stu-id="73a55-163">Flow of operations in main()</span></span>

<span data-ttu-id="73a55-164">`main()` 函数十分简单，它仅指示操作流并执行错误检查。</span><span class="sxs-lookup"><span data-stu-id="73a55-164">The `main()` function is simple, only indicating the flow of operations and performing error-checking.</span></span>

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

<span data-ttu-id="73a55-165">该代码运行的步骤依次为：</span><span class="sxs-lookup"><span data-stu-id="73a55-165">The steps that the code runs through are, in order:</span></span>

* <span data-ttu-id="73a55-166">创建要部署到的资源组 (`createGroup()`)</span><span class="sxs-lookup"><span data-stu-id="73a55-166">Create the resource group to deploy to (`createGroup()`)</span></span>
* <span data-ttu-id="73a55-167">在此组中创建部署 (`createDeployment()`)</span><span class="sxs-lookup"><span data-stu-id="73a55-167">Create the deployment within this group (`createDeployment()`)</span></span>
* <span data-ttu-id="73a55-168">获取并显示已部署 VM 的登录信息 (`getLogin()`)</span><span class="sxs-lookup"><span data-stu-id="73a55-168">Obtain and display login information for the deployed VM (`getLogin()`)</span></span>

### <a name="creating-the-resource-group"></a><span data-ttu-id="73a55-169">创建资源组</span><span class="sxs-lookup"><span data-stu-id="73a55-169">Creating the resource group</span></span>

<span data-ttu-id="73a55-170">`createGroup()` 函数创建资源组。</span><span class="sxs-lookup"><span data-stu-id="73a55-170">The `createGroup()` function creates the resource group.</span></span> <span data-ttu-id="73a55-171">以下调用流和参数演示了 SDK 中构建服务交互的方式。</span><span class="sxs-lookup"><span data-stu-id="73a55-171">Looking at the call flow and arguments demonstrates the way that service interactions are structured in the SDK.</span></span>

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

<span data-ttu-id="73a55-172">与 Azure 服务交互的一般流程为：</span><span class="sxs-lookup"><span data-stu-id="73a55-172">The general flow of interacting with an Azure service is:</span></span>

* <span data-ttu-id="73a55-173">使用 `service.NewXClient()` 方法创建客户端，其中，`X` 是要交互的 `service` 的资源类型。</span><span class="sxs-lookup"><span data-stu-id="73a55-173">Create the client using the `service.NewXClient()` method, where `X` is the resource type of the `service` that you want to interact with.</span></span> <span data-ttu-id="73a55-174">此函数始终采用订阅 ID。</span><span class="sxs-lookup"><span data-stu-id="73a55-174">This function always takes a subscription ID.</span></span>
* <span data-ttu-id="73a55-175">设置客户端的授权方法，使其能够与远程 API 交互。</span><span class="sxs-lookup"><span data-stu-id="73a55-175">Set the authorization method for the client, allowing it to interact with the remote API.</span></span>
* <span data-ttu-id="73a55-176">地对应于远程 API 的客户端上发出方法调用。</span><span class="sxs-lookup"><span data-stu-id="73a55-176">Make the method call on the client corresponding to the remote API.</span></span> <span data-ttu-id="73a55-177">服务客户端方法通常采用资源名称和元数据对象。</span><span class="sxs-lookup"><span data-stu-id="73a55-177">Service client methods usually take the name of the resource and a metadata object.</span></span>

<span data-ttu-id="73a55-178">此处的 [`to.StringPtr()`](https://godoc.org/github.com/Azure/go-autorest/autorest/to#StringPtr) 函数用于执行类型转换。</span><span class="sxs-lookup"><span data-stu-id="73a55-178">The [`to.StringPtr()`](https://godoc.org/github.com/Azure/go-autorest/autorest/to#StringPtr) function is used to perform a type conversion here.</span></span> <span data-ttu-id="73a55-179">SDK 方法的 parameters 结构几乎独占指针，因此，提供这些方法可以简化类型转换。</span><span class="sxs-lookup"><span data-stu-id="73a55-179">The parameters structs for methods of the SDK almost exclusively take pointers, so these methods are provided to make the type conversions easy.</span></span> <span data-ttu-id="73a55-180">请参阅 [autorest/to](https://godoc.org/github.com/Azure/go-autorest/autorest/to) 的文档，了解便捷转换器的完整列表和行为。</span><span class="sxs-lookup"><span data-stu-id="73a55-180">See the documentation for the [autorest/to](https://godoc.org/github.com/Azure/go-autorest/autorest/to) module for the complete list and behavior of convenience converters.</span></span>

<span data-ttu-id="73a55-181">`groupsClient.CreateOrUpdate()` 操作返回一个指针，该指针指向表示资源组的数据结构。</span><span class="sxs-lookup"><span data-stu-id="73a55-181">The `groupsClient.CreateOrUpdate()` operation returns a pointer to a data struct representing the resource group.</span></span> <span data-ttu-id="73a55-182">此类直接返回值表示一个同步的短时间运行操作。</span><span class="sxs-lookup"><span data-stu-id="73a55-182">A direct return value of this kind indicates a short-running operation that is meant to be synchronous.</span></span> <span data-ttu-id="73a55-183">下一部分将演示长时间运行的操作示例，以及如何与其交互。</span><span class="sxs-lookup"><span data-stu-id="73a55-183">In the next section, you'll see an example of a long-running operation and how to interact with them.</span></span>

### <a name="performing-the-deployment"></a><span data-ttu-id="73a55-184">执行部署</span><span class="sxs-lookup"><span data-stu-id="73a55-184">Performing the deployment</span></span>

<span data-ttu-id="73a55-185">创建用于包含资源的组后，可以运行部署。</span><span class="sxs-lookup"><span data-stu-id="73a55-185">Once the group to contain its resources is created, it's time to run the deployment.</span></span> <span data-ttu-id="73a55-186">此代码已分解成多个小段，以突出其逻辑的各个部分。</span><span class="sxs-lookup"><span data-stu-id="73a55-186">This code is broken up into smaller sections to emphasize different parts of its logic.</span></span>


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

<span data-ttu-id="73a55-187">部署文件由 `readJSON` 加载，此处不会讲解相关详细信息。</span><span class="sxs-lookup"><span data-stu-id="73a55-187">The deployment files are loaded by `readJSON`, the details of which are skipped here.</span></span> <span data-ttu-id="73a55-188">此函数返回 `*map[string]interface{}`，即构造资源部署调用的元数据时使用的类型。</span><span class="sxs-lookup"><span data-stu-id="73a55-188">This function returns a `*map[string]interface{}`, the type used in constructing the metadata for the resource deployment call.</span></span>

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

<span data-ttu-id="73a55-189">此代码遵循的模式与创建资源组相同。</span><span class="sxs-lookup"><span data-stu-id="73a55-189">This code follows the same pattern as with creating the resource group.</span></span> <span data-ttu-id="73a55-190">将创建新客户端以便能够在 Azure 中进行身份验证，然后调用一个方法。</span><span class="sxs-lookup"><span data-stu-id="73a55-190">A new client is created, given the ability to authenticate with Azure, and then a method is called.</span></span> <span data-ttu-id="73a55-191">该方法甚至与资源组的对应方法同名 (`CreateOrUpdate`)。</span><span class="sxs-lookup"><span data-stu-id="73a55-191">The method even has the same name (`CreateOrUpdate`) as the corresponding method for resource groups.</span></span> <span data-ttu-id="73a55-192">SDK 中反复出现了此模式。</span><span class="sxs-lookup"><span data-stu-id="73a55-192">This pattern is seen again and again in the SDK.</span></span> <span data-ttu-id="73a55-193">执行类似工作的方法通常是同名的。</span><span class="sxs-lookup"><span data-stu-id="73a55-193">Methods that perform similar work normally have the same name.</span></span>

<span data-ttu-id="73a55-194">最大的差别在于 `deploymentsClient.CreateOrUpdate()` 方法的返回值。</span><span class="sxs-lookup"><span data-stu-id="73a55-194">The biggest difference comes in the return value of the `deploymentsClient.CreateOrUpdate()` method.</span></span> <span data-ttu-id="73a55-195">此值是一个遵循 [Future 设计模式](https://en.wikipedia.org/wiki/Futures_and_promises)的 `Future` 对象。</span><span class="sxs-lookup"><span data-stu-id="73a55-195">This value is a `Future` object, which follows the [future design pattern](https://en.wikipedia.org/wiki/Futures_and_promises).</span></span> <span data-ttu-id="73a55-196">Future 表示 Azure 中的、你在执行其他工作时偶尔想要轮询的长时间运行的操作。</span><span class="sxs-lookup"><span data-stu-id="73a55-196">Futures represent a long-running operation in Azure that you may want to occasionally poll while performing other work.</span></span>

```go
        //...
        err = deploymentFuture.Future.WaitForCompletion(ctx, deploymentsClient.BaseClient.Client)
        if err != nil {
                log.Fatalf("Error while waiting for deployment creation: %v", err)
        }
        return deploymentFuture.Result(deploymentsClient)
}
```

<span data-ttu-id="73a55-197">在此示例中，最恰当的做法就是等待该操作完成。</span><span class="sxs-lookup"><span data-stu-id="73a55-197">For this example, the best thing to do is to wait for the operation to complete.</span></span> <span data-ttu-id="73a55-198">等待 Future 要求提供[上下文对象](https://blog.golang.org/context)，以及创建了 Future 对象的客户端。</span><span class="sxs-lookup"><span data-stu-id="73a55-198">Waiting on a future requires both a [context object](https://blog.golang.org/context) and the client that created the Future object.</span></span> <span data-ttu-id="73a55-199">此处有两个可能的错误来源：尝试调用方法时在客户端造成的错误，以及来自服务器的错误响应。</span><span class="sxs-lookup"><span data-stu-id="73a55-199">There are two possible error sources here: An error caused on the client side when trying to invoke the method, and an error response from the server.</span></span> <span data-ttu-id="73a55-200">后者作为 `deploymentFuture.Result()` 调用的一部分返回。</span><span class="sxs-lookup"><span data-stu-id="73a55-200">The latter is returned as part of the `deploymentFuture.Result()` call.</span></span>

### <a name="obtaining-the-assigned-ip-address"></a><span data-ttu-id="73a55-201">获取分配的 IP 地址</span><span class="sxs-lookup"><span data-stu-id="73a55-201">Obtaining the assigned IP address</span></span>

<span data-ttu-id="73a55-202">若要使用新建的 VM 执行任何操作，需要使用分配的 IP 地址。</span><span class="sxs-lookup"><span data-stu-id="73a55-202">To do anything with the newly created VM, you need the assigned IP address.</span></span> <span data-ttu-id="73a55-203">IP 地址是其自身的独立 Azure 资源，已绑定到网络接口控制器 (NIC) 资源。</span><span class="sxs-lookup"><span data-stu-id="73a55-203">IP addresses are their own separate Azure resource, bound to Network Interface Controller (NIC) resources.</span></span>

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

<span data-ttu-id="73a55-204">此方法依赖于 parameters 文件中存储的信息。</span><span class="sxs-lookup"><span data-stu-id="73a55-204">This method relies on information that is stored in the parameters file.</span></span> <span data-ttu-id="73a55-205">该代码可以直接查询 VM 以获取 VM 的 NIC、查询 NIC 以获取 NIC 的 IP 资源，然后直接查询 IP 资源。</span><span class="sxs-lookup"><span data-stu-id="73a55-205">The code could query the VM directly to get its NIC, query the NIC to get its IP resource, and then query the IP resource directly.</span></span> <span data-ttu-id="73a55-206">有一长串的依赖关系和操作需要解析，因此开销不菲。</span><span class="sxs-lookup"><span data-stu-id="73a55-206">That's a long chain of dependencies and operations to resolve, making it expensive.</span></span> <span data-ttu-id="73a55-207">由于 JSON 信息位于本地，可以改为加载此信息。</span><span class="sxs-lookup"><span data-stu-id="73a55-207">Since the JSON information is local, it can be loaded instead.</span></span>

<span data-ttu-id="73a55-208">同理可从 JSON 加载 VM 用户和密码的值。</span><span class="sxs-lookup"><span data-stu-id="73a55-208">The values for the VM user and password are likewise loaded from the JSON.</span></span>

## <a name="next-steps"></a><span data-ttu-id="73a55-209">后续步骤</span><span class="sxs-lookup"><span data-stu-id="73a55-209">Next steps</span></span>

<span data-ttu-id="73a55-210">在本快速入门中，我们编辑了现有的模板，并通过 Go 部署了该模板。</span><span class="sxs-lookup"><span data-stu-id="73a55-210">In this quickstart, you took an existing template and deployed it through Go.</span></span> <span data-ttu-id="73a55-211">然后，通过 SSH 连接到了新建的 VM，以确保它在运行。</span><span class="sxs-lookup"><span data-stu-id="73a55-211">Then you connected to the newly created VM via SSH to ensure that it's running.</span></span>

<span data-ttu-id="73a55-212">若要继续学习如何通过 Go 在 Azure 环境中使用虚拟机，请参阅[适用于 Go 的 Azure 计算示例](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/compute)，或[适用于 Go 的 Azure 资源管理示例](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/resources)。</span><span class="sxs-lookup"><span data-stu-id="73a55-212">To continue learning about working with virtual machines in the Azure environment with Go, take a look at the [Azure compute samples for Go](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/compute) or [Azure resource management samples for Go](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/resources).</span></span>
