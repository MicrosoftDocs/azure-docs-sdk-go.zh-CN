---
title: "安装 Azure SDK for Go"
description: "如何安装、引用 (vendor) 和配置 Azure SDK for Go。"
keywords: azure, sdk, go, golang, azure sdk
author: sptramer
ms.author: sttramer
ms.date: 01/30/2018
ms.topic: article
ms.devlang: go
manager: routlaw
ms.openlocfilehash: 7fc0a3ff71b0b06f616ae43cff311352fe873345
ms.sourcegitcommit: 890f5f01a70e7e376e6bb98a2030afbfc016f538
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/01/2018
---
# <a name="installing-the-azure-sdk-for-go"></a><span data-ttu-id="1de21-104">安装 Azure SDK for Go</span><span class="sxs-lookup"><span data-stu-id="1de21-104">Installing the Azure SDK for Go</span></span>

<span data-ttu-id="1de21-105">欢迎使用 Azure SDK for Go！</span><span class="sxs-lookup"><span data-stu-id="1de21-105">Welcome to the Azure SDK for Go!</span></span> <span data-ttu-id="1de21-106">使用此 SDK 可以管理 Go 应用程序中的 Azure 服务并与之交互。</span><span class="sxs-lookup"><span data-stu-id="1de21-106">This SDK allows you to manage and interact with Azure services from your Go applications.</span></span>

## <a name="get-the-azure-sdk-for-go"></a><span data-ttu-id="1de21-107">获取 Azure SDK for Go</span><span class="sxs-lookup"><span data-stu-id="1de21-107">Get the Azure SDK for Go</span></span>

[!INCLUDE [azure-sdk-go-get](includes/azure-sdk-go-get.md)]

<span data-ttu-id="1de21-108">使用 Azure 存储 Blob 需要单独的 SDK。</span><span class="sxs-lookup"><span data-stu-id="1de21-108">Working with Azure Storage Blobs requires a separate SDK.</span></span>

```bash
go get -u -d github.com/Azure/azure-storage-blob-go/...
```

## <a name="vendoring-the-azure-sdk-for-go"></a><span data-ttu-id="1de21-109">引用 (vendor) Azure SDK for Go</span><span class="sxs-lookup"><span data-stu-id="1de21-109">Vendoring the Azure SDK for Go</span></span>

<span data-ttu-id="1de21-110">可以通过 [dep](https://github.com/golang/dep) 引用 Azure SDK for Go。</span><span class="sxs-lookup"><span data-stu-id="1de21-110">The Azure SDK for Go may be vendored through [dep](https://github.com/golang/dep).</span></span> <span data-ttu-id="1de21-111">出于稳定原因，我们建议引用它。</span><span class="sxs-lookup"><span data-stu-id="1de21-111">For stability reasons, vendoring is recommended.</span></span> <span data-ttu-id="1de21-112">若要使用 `dep` 支持，请将 `github.com/Azure/azure-sdk-for-go` 添加到 `Gopkg.toml` 的 `[[constraint]]` 节。</span><span class="sxs-lookup"><span data-stu-id="1de21-112">In order to use `dep` support, add `github.com/Azure/azure-sdk-for-go` to a `[[constraint]]` section of your `Gopkg.toml`.</span></span> <span data-ttu-id="1de21-113">例如，若要引用版本 `14.0.0`，请添加以下条目：</span><span class="sxs-lookup"><span data-stu-id="1de21-113">For example, to vendor on version `14.0.0`, add the following entry:</span></span>

```
[[constraint]]
name = "github.com/Azure/azure-sdk-for-go"
version = "14.0.0"
```

## <a name="including-the-azure-sdk-for-go-in-your-project"></a><span data-ttu-id="1de21-114">在项目中包含 Azure SDK for Go</span><span class="sxs-lookup"><span data-stu-id="1de21-114">Including the Azure SDK for Go in your project</span></span>

<span data-ttu-id="1de21-115">若要通过 Go 代码使用 Azure 服务，请导入所要交互的任何服务和所需的 `autorest` 模块。</span><span class="sxs-lookup"><span data-stu-id="1de21-115">To use Azure services from your Go code, import any services you interact with and the required `autorest` modules.</span></span>
<span data-ttu-id="1de21-116">可以从 GoDoc 获取[可用服务](https://godoc.org/github.com/Azure/azure-sdk-for-go)和 [AutoRest 包](https://godoc.org/github.com/Azure/go-autorest)的完整可用模块列表。</span><span class="sxs-lookup"><span data-stu-id="1de21-116">You get a complete list of the available modules from GoDoc for [available services](https://godoc.org/github.com/Azure/azure-sdk-for-go) and [AutoRest packages](https://godoc.org/github.com/Azure/go-autorest).</span></span> <span data-ttu-id="1de21-117">需要在 `go-autorest` 中使用的最常见包为：</span><span class="sxs-lookup"><span data-stu-id="1de21-117">The most common packages you need from `go-autorest` are:</span></span>

| <span data-ttu-id="1de21-118">程序包</span><span class="sxs-lookup"><span data-stu-id="1de21-118">Package</span></span> | <span data-ttu-id="1de21-119">说明</span><span class="sxs-lookup"><span data-stu-id="1de21-119">Description</span></span> |
|---------|-------------|
| <span data-ttu-id="1de21-120">[github.com/Azure/go-autorest/autorest][autorest]</span><span class="sxs-lookup"><span data-stu-id="1de21-120">[github.com/Azure/go-autorest/autorest][autorest]</span></span> | <span data-ttu-id="1de21-121">用于处理服务客户端身份验证的对象</span><span class="sxs-lookup"><span data-stu-id="1de21-121">Objects for handling service client authentication</span></span> |
| <span data-ttu-id="1de21-122">[github.com/Azure/go-autorest/autorest/azure][autorest/azure]</span><span class="sxs-lookup"><span data-stu-id="1de21-122">[github.com/Azure/go-autorest/autorest/azure][autorest/azure]</span></span> | <span data-ttu-id="1de21-123">用于与 Azure 服务交互的常量</span><span class="sxs-lookup"><span data-stu-id="1de21-123">Constants for interactions with Azure services</span></span> |
| <span data-ttu-id="1de21-124">[github.com/Azure/go-autorest/autorest/adal][autorest/adal]</span><span class="sxs-lookup"><span data-stu-id="1de21-124">[github.com/Azure/go-autorest/autorest/adal][autorest/adal]</span></span> | <span data-ttu-id="1de21-125">用于访问 Azure 服务的身份验证机制</span><span class="sxs-lookup"><span data-stu-id="1de21-125">Authentication mechanisms for accessing Azure services</span></span> |
| <span data-ttu-id="1de21-126">[github.com/Azure/go-autorest/autorest/to][autorest/to]</span><span class="sxs-lookup"><span data-stu-id="1de21-126">[github.com/Azure/go-autorest/autorest/to][autorest/to]</span></span> | <span data-ttu-id="1de21-127">用于使用 Azure SDK 数据结构的类型断言帮助器</span><span class="sxs-lookup"><span data-stu-id="1de21-127">Type assertion helpers for working with Azure SDK data structures</span></span> |

[autorest]: https://godoc.org/github.com/Azure/go-autorest/autorest
[autorest/azure]: https://godoc.org/github.com/Azure/go-autorest/autorest/azure
[autorest/adal]: https://godoc.org/github.com/Azure/go-autorest/autorest/adal
[autorest/to]: https://godoc.org/github.com/Azure/go-autorest/autorest/to

<span data-ttu-id="1de21-128">Azure 模块服务的版本控制独立于模块的 SDK API。</span><span class="sxs-lookup"><span data-stu-id="1de21-128">Modules for Azure services are versioned independently from the SDK APIs for them.</span></span> <span data-ttu-id="1de21-129">这些版本是模块导入路径的一部分，来自_服务版本_或_配置文件_。</span><span class="sxs-lookup"><span data-stu-id="1de21-129">These versions are part of the module import path, and are from either a _service version_ or a _profile_.</span></span> <span data-ttu-id="1de21-130">目前，我们建议将特定的服务版本用于开发和发布。</span><span class="sxs-lookup"><span data-stu-id="1de21-130">Currently, it is recommended that you use a specific service version for both development and release.</span></span> <span data-ttu-id="1de21-131">服务位于 `services` 模块下面。</span><span class="sxs-lookup"><span data-stu-id="1de21-131">Services are located under the `services` module.</span></span> <span data-ttu-id="1de21-132">导入的完整路径是服务名称，后接 `YYYY-MM-DD` 格式的版本，再后接服务名称。</span><span class="sxs-lookup"><span data-stu-id="1de21-132">The full path for the import is the name of the service, followed by the version in `YYYY-MM-DD` format, followed by the service name again.</span></span> <span data-ttu-id="1de21-133">例如，若要包含计算服务的 `2017-03-30` 版本：</span><span class="sxs-lookup"><span data-stu-id="1de21-133">For example, to include the `2017-03-30` version of the Compute service:</span></span>

```go
import "github.com/Azure/azure-sdk-for-go/services/compute/mgmt/2017-03-30/compute"
```

<span data-ttu-id="1de21-134">目前，除外另有原因，否则我们建议使用最新版本的服务。</span><span class="sxs-lookup"><span data-stu-id="1de21-134">Right now it is recommended that you use the latest version of a service, unless you have a reason to do otherwise.</span></span>

<span data-ttu-id="1de21-135">如果需要服务的统一快照，也可以选择单个配置文件版本。</span><span class="sxs-lookup"><span data-stu-id="1de21-135">If you need a collective snapshot of services, you can also select a single profile version.</span></span> <span data-ttu-id="1de21-136">目前，唯一锁定的配置文件为版本 `2017-03-30`，其中可能不包含服务的最新功能。</span><span class="sxs-lookup"><span data-stu-id="1de21-136">Right now, the only locked profile is version `2017-03-30`, which may not have the latest features of services.</span></span> <span data-ttu-id="1de21-137">配置文件位于 `profiles` 模块下，其版本采用 `YYYY-MM-DD` 格式。</span><span class="sxs-lookup"><span data-stu-id="1de21-137">Profiles are located under the `profiles` module, with their version in the `YYYY-MM-DD` format.</span></span> <span data-ttu-id="1de21-138">服务根据其配置文件版本分组。</span><span class="sxs-lookup"><span data-stu-id="1de21-138">Services are grouped under their profile version.</span></span> <span data-ttu-id="1de21-139">例如，若要从 `2017-03-09` 配置文件导入 Azure 资源管理模块：</span><span class="sxs-lookup"><span data-stu-id="1de21-139">For example, to import the Azure Resources management module from the `2017-03-09` profile:</span></span>

```go
import "github.com/Azure/azure-sdk-for-go/profiles/2017-03-09/resources/mgmt/resources"
```

> [!WARNING]
> <span data-ttu-id="1de21-140">还可以使用 `preview` 和 `latest` 配置文件，</span><span class="sxs-lookup"><span data-stu-id="1de21-140">There are also `preview` and `latest` profiles available.</span></span> <span data-ttu-id="1de21-141">但不建议使用。</span><span class="sxs-lookup"><span data-stu-id="1de21-141">Using them is not recommended.</span></span> <span data-ttu-id="1de21-142">这些配置文件的版本会不断滚动更新，服务的行为随时可能更改。</span><span class="sxs-lookup"><span data-stu-id="1de21-142">These profiles are rolling versions and service behavior may change at any time.</span></span>

## <a name="next-steps"></a><span data-ttu-id="1de21-143">后续步骤</span><span class="sxs-lookup"><span data-stu-id="1de21-143">Next steps</span></span>

<span data-ttu-id="1de21-144">若要开始使用 Azure SDK for Go，请从快速入门着手。</span><span class="sxs-lookup"><span data-stu-id="1de21-144">To begin using the Azure SDK for Go, try out a quickstart.</span></span>

* [<span data-ttu-id="1de21-145">从模板部署虚拟机</span><span class="sxs-lookup"><span data-stu-id="1de21-145">Deploy a virtual machine from a template</span></span>](azure-sdk-go-qs-vm.md)
* [<span data-ttu-id="1de21-146">使用 Azure SDK for Go 将对象传输到 Azure Blob 存储</span><span class="sxs-lookup"><span data-stu-id="1de21-146">Transfer objects to Azure Blob Storage with the Azure Blob SDK for Go</span></span>](/azure/storage/blobs/storage-quickstart-blobs-go?toc=%2fgo%2fazure%2ftoc.json)
* [<span data-ttu-id="1de21-147">连接到 Azure Database for PostgreSQL</span><span class="sxs-lookup"><span data-stu-id="1de21-147">Connect to Azure Database for PostgreSQL</span></span>](/azure/postgresql/connect-go?toc=%2fgo%2fazure%2ftoc.json)

<span data-ttu-id="1de21-148">若要立即在 Go SDK 中开始使用其他服务，请查看一些可用的示例代码。</span><span class="sxs-lookup"><span data-stu-id="1de21-148">If you want to get started with other services in the Go SDK immediately, take a look at some of the available sample code.</span></span>

* [<span data-ttu-id="1de21-149">在 Azure 服务中进行身份验证</span><span class="sxs-lookup"><span data-stu-id="1de21-149">Authenticate with Azure services</span></span>](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/iam)
* [<span data-ttu-id="1de21-150">使用 SSH 身份验证部署新虚拟机</span><span class="sxs-lookup"><span data-stu-id="1de21-150">Deploy new virtual machines with SSH authentication</span></span>](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/compute)
* [<span data-ttu-id="1de21-151">将容器映像部署到 Azure 容器实例</span><span class="sxs-lookup"><span data-stu-id="1de21-151">Deploy a container image to Azure Container Instances</span></span>](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/containerinstance)
* [<span data-ttu-id="1de21-152">在 Azure Kubernetes 服务中创建群集</span><span class="sxs-lookup"><span data-stu-id="1de21-152">Create a cluster in Azure Kubernetes Service</span></span>](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/containerservice)
* [<span data-ttu-id="1de21-153">使用 Azure 存储服务</span><span class="sxs-lookup"><span data-stu-id="1de21-153">Work with Azure Storage services</span></span>](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/storage)
* [<span data-ttu-id="1de21-154">Azure SDK for Go 的所有示例</span><span class="sxs-lookup"><span data-stu-id="1de21-154">All samples for the Azure SDK for Go</span></span>](https://github.com/azure-samples/azure-sdk-for-go-samples)
