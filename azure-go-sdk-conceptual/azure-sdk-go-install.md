---
title: 安装 Azure SDK for Go
description: 如何安装、引用 (vendor) 和配置 Azure SDK for Go。
author: sptramer
ms.author: sttramer
ms.date: 03/14/2018
ms.topic: article
ms.devlang: go
manager: carmonm
ms.openlocfilehash: a6a92e080aea1a92f47a9d7083f133ca05a47541
ms.sourcegitcommit: 26520a8c6e812facb5b9432d68c370fa23c99888
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2018
---
# <a name="install-the-azure-sdk-for-go"></a><span data-ttu-id="f18c7-103">安装 Azure SDK for Go</span><span class="sxs-lookup"><span data-stu-id="f18c7-103">Install the Azure SDK for Go</span></span>

<span data-ttu-id="f18c7-104">欢迎使用 Azure SDK for Go！</span><span class="sxs-lookup"><span data-stu-id="f18c7-104">Welcome to the Azure SDK for Go!</span></span> <span data-ttu-id="f18c7-105">使用该 SDK 可以管理 Go 应用程序中的 Azure 服务并与之交互。</span><span class="sxs-lookup"><span data-stu-id="f18c7-105">The SDK allows you to manage and interact with Azure services from your Go applications.</span></span>

## <a name="get-the-azure-sdk-for-go"></a><span data-ttu-id="f18c7-106">获取 Azure SDK for Go</span><span class="sxs-lookup"><span data-stu-id="f18c7-106">Get the Azure SDK for Go</span></span>

[!INCLUDE [azure-sdk-go-get](includes/azure-sdk-go-get.md)]

<span data-ttu-id="f18c7-107">使用 Azure 存储 Blob 需要单独的 SDK。</span><span class="sxs-lookup"><span data-stu-id="f18c7-107">Working with Azure Storage Blobs requires a separate SDK.</span></span>

```bash
go get -u -d github.com/Azure/azure-storage-blob-go/...
```

## <a name="vendor-the-azure-sdk-for-go"></a><span data-ttu-id="f18c7-108">引用 (Vendor) Azure SDK for Go</span><span class="sxs-lookup"><span data-stu-id="f18c7-108">Vendor the Azure SDK for Go</span></span>

<span data-ttu-id="f18c7-109">可以通过 [dep](https://github.com/golang/dep) 引用 Azure SDK for Go。</span><span class="sxs-lookup"><span data-stu-id="f18c7-109">The Azure SDK for Go may be vendored through [dep](https://github.com/golang/dep).</span></span> <span data-ttu-id="f18c7-110">出于稳定原因，我们建议引用它。</span><span class="sxs-lookup"><span data-stu-id="f18c7-110">For stability reasons, vendoring is recommended.</span></span> <span data-ttu-id="f18c7-111">若要使用 `dep` 支持，请将 `github.com/Azure/azure-sdk-for-go` 添加到 `Gopkg.toml` 的 `[[constraint]]` 节。</span><span class="sxs-lookup"><span data-stu-id="f18c7-111">In order to use `dep` support, add `github.com/Azure/azure-sdk-for-go` to a `[[constraint]]` section of your `Gopkg.toml`.</span></span> <span data-ttu-id="f18c7-112">例如，若要引用版本 `14.0.0`，请添加以下条目：</span><span class="sxs-lookup"><span data-stu-id="f18c7-112">For example, to vendor on version `14.0.0`, add the following entry:</span></span>

```
[[constraint]]
name = "github.com/Azure/azure-sdk-for-go"
version = "14.0.0"
```

## <a name="include-the-azure-sdk-for-go-in-your-project"></a><span data-ttu-id="f18c7-113">在项目中包含 Azure SDK for Go</span><span class="sxs-lookup"><span data-stu-id="f18c7-113">Include the Azure SDK for Go in your project</span></span>

<span data-ttu-id="f18c7-114">若要通过 Go 代码使用 Azure 服务，请导入所要交互的任何服务和所需的 `autorest` 模块。</span><span class="sxs-lookup"><span data-stu-id="f18c7-114">To use Azure services from your Go code, import any services you interact with and the required `autorest` modules.</span></span>
<span data-ttu-id="f18c7-115">可以从 GoDoc 获取[可用服务](https://godoc.org/github.com/Azure/azure-sdk-for-go)和 [AutoRest 包](https://godoc.org/github.com/Azure/go-autorest)的完整可用模块列表。</span><span class="sxs-lookup"><span data-stu-id="f18c7-115">You get a complete list of the available modules from GoDoc for [available services](https://godoc.org/github.com/Azure/azure-sdk-for-go) and [AutoRest packages](https://godoc.org/github.com/Azure/go-autorest).</span></span> <span data-ttu-id="f18c7-116">需要在 `go-autorest` 中使用的最常见包为：</span><span class="sxs-lookup"><span data-stu-id="f18c7-116">The most common packages you need from `go-autorest` are:</span></span>

| <span data-ttu-id="f18c7-117">程序包</span><span class="sxs-lookup"><span data-stu-id="f18c7-117">Package</span></span> | <span data-ttu-id="f18c7-118">说明</span><span class="sxs-lookup"><span data-stu-id="f18c7-118">Description</span></span> |
|---------|-------------|
| <span data-ttu-id="f18c7-119">[github.com/Azure/go-autorest/autorest][autorest]</span><span class="sxs-lookup"><span data-stu-id="f18c7-119">[github.com/Azure/go-autorest/autorest][autorest]</span></span> | <span data-ttu-id="f18c7-120">用于处理服务客户端身份验证的对象</span><span class="sxs-lookup"><span data-stu-id="f18c7-120">Objects for handling service client authentication</span></span> |
| <span data-ttu-id="f18c7-121">[github.com/Azure/go-autorest/autorest/azure][autorest/azure]</span><span class="sxs-lookup"><span data-stu-id="f18c7-121">[github.com/Azure/go-autorest/autorest/azure][autorest/azure]</span></span> | <span data-ttu-id="f18c7-122">用于与 Azure 服务交互的常量</span><span class="sxs-lookup"><span data-stu-id="f18c7-122">Constants for interactions with Azure services</span></span> |
| <span data-ttu-id="f18c7-123">[github.com/Azure/go-autorest/autorest/adal][autorest/adal]</span><span class="sxs-lookup"><span data-stu-id="f18c7-123">[github.com/Azure/go-autorest/autorest/adal][autorest/adal]</span></span> | <span data-ttu-id="f18c7-124">用于访问 Azure 服务的身份验证机制</span><span class="sxs-lookup"><span data-stu-id="f18c7-124">Authentication mechanisms for accessing Azure services</span></span> |
| <span data-ttu-id="f18c7-125">[github.com/Azure/go-autorest/autorest/to][autorest/to]</span><span class="sxs-lookup"><span data-stu-id="f18c7-125">[github.com/Azure/go-autorest/autorest/to][autorest/to]</span></span> | <span data-ttu-id="f18c7-126">用于使用 Azure SDK 数据结构的类型断言帮助器</span><span class="sxs-lookup"><span data-stu-id="f18c7-126">Type assertion helpers for working with Azure SDK data structures</span></span> |

[autorest]: https://godoc.org/github.com/Azure/go-autorest/autorest
[autorest/azure]: https://godoc.org/github.com/Azure/go-autorest/autorest/azure
[autorest/adal]: https://godoc.org/github.com/Azure/go-autorest/autorest/adal
[autorest/to]: https://godoc.org/github.com/Azure/go-autorest/autorest/to

<span data-ttu-id="f18c7-127">Azure 模块服务的版本控制独立于模块的 SDK API。</span><span class="sxs-lookup"><span data-stu-id="f18c7-127">Modules for Azure services are versioned independently from the SDK APIs for them.</span></span> <span data-ttu-id="f18c7-128">这些版本是模块导入路径的一部分，来自_服务版本_或_配置文件_。</span><span class="sxs-lookup"><span data-stu-id="f18c7-128">These versions are part of the module import path, and are from either a _service version_ or a _profile_.</span></span> <span data-ttu-id="f18c7-129">目前，我们建议将特定的服务版本用于开发和发布。</span><span class="sxs-lookup"><span data-stu-id="f18c7-129">Currently, it is recommended that you use a specific service version for both development and release.</span></span> <span data-ttu-id="f18c7-130">服务位于 `services` 模块下面。</span><span class="sxs-lookup"><span data-stu-id="f18c7-130">Services are located under the `services` module.</span></span> <span data-ttu-id="f18c7-131">导入的完整路径是服务名称，后接 `YYYY-MM-DD` 格式的版本，再后接服务名称。</span><span class="sxs-lookup"><span data-stu-id="f18c7-131">The full path for the import is the name of the service, followed by the version in `YYYY-MM-DD` format, followed by the service name again.</span></span> <span data-ttu-id="f18c7-132">例如，若要包含计算服务的 `2017-03-30` 版本：</span><span class="sxs-lookup"><span data-stu-id="f18c7-132">For example, to include the `2017-03-30` version of the Compute service:</span></span>

```go
import "github.com/Azure/azure-sdk-for-go/services/compute/mgmt/2017-03-30/compute"
```

<span data-ttu-id="f18c7-133">目前，除外另有原因，否则我们建议使用最新版本的服务。</span><span class="sxs-lookup"><span data-stu-id="f18c7-133">Right now it is recommended that you use the latest version of a service, unless you have a reason to do otherwise.</span></span>

<span data-ttu-id="f18c7-134">如果需要服务的统一快照，也可以选择单个配置文件版本。</span><span class="sxs-lookup"><span data-stu-id="f18c7-134">If you need a collective snapshot of services, you can also select a single profile version.</span></span> <span data-ttu-id="f18c7-135">目前，唯一锁定的配置文件为版本 `2017-03-30`，其中可能不包含服务的最新功能。</span><span class="sxs-lookup"><span data-stu-id="f18c7-135">Right now, the only locked profile is version `2017-03-30`, which may not have the latest features of services.</span></span> <span data-ttu-id="f18c7-136">配置文件位于 `profiles` 模块下，其版本采用 `YYYY-MM-DD` 格式。</span><span class="sxs-lookup"><span data-stu-id="f18c7-136">Profiles are located under the `profiles` module, with their version in the `YYYY-MM-DD` format.</span></span> <span data-ttu-id="f18c7-137">服务根据其配置文件版本分组。</span><span class="sxs-lookup"><span data-stu-id="f18c7-137">Services are grouped under their profile version.</span></span> <span data-ttu-id="f18c7-138">例如，若要从 `2017-03-09` 配置文件导入 Azure 资源管理模块：</span><span class="sxs-lookup"><span data-stu-id="f18c7-138">For example, to import the Azure Resources management module from the `2017-03-09` profile:</span></span>

```go
import "github.com/Azure/azure-sdk-for-go/profiles/2017-03-09/resources/mgmt/resources"
```

> [!WARNING]
> <span data-ttu-id="f18c7-139">还可以使用 `preview` 和 `latest` 配置文件，</span><span class="sxs-lookup"><span data-stu-id="f18c7-139">There are also `preview` and `latest` profiles available.</span></span> <span data-ttu-id="f18c7-140">但不建议使用。</span><span class="sxs-lookup"><span data-stu-id="f18c7-140">Using them is not recommended.</span></span> <span data-ttu-id="f18c7-141">这些配置文件的版本会不断滚动更新，服务的行为随时可能更改。</span><span class="sxs-lookup"><span data-stu-id="f18c7-141">These profiles are rolling versions and service behavior may change at any time.</span></span>

## <a name="next-steps"></a><span data-ttu-id="f18c7-142">后续步骤</span><span class="sxs-lookup"><span data-stu-id="f18c7-142">Next steps</span></span>

<span data-ttu-id="f18c7-143">若要开始使用 Azure SDK for Go，请从快速入门着手。</span><span class="sxs-lookup"><span data-stu-id="f18c7-143">To begin using the Azure SDK for Go, try out a quickstart.</span></span>

* [<span data-ttu-id="f18c7-144">从模板部署虚拟机</span><span class="sxs-lookup"><span data-stu-id="f18c7-144">Deploy a virtual machine from a template</span></span>](azure-sdk-go-qs-vm.md)
* [<span data-ttu-id="f18c7-145">使用 Azure SDK for Go 将对象传输到 Azure Blob 存储</span><span class="sxs-lookup"><span data-stu-id="f18c7-145">Transfer objects to Azure Blob Storage with the Azure Blob SDK for Go</span></span>](/azure/storage/blobs/storage-quickstart-blobs-go?toc=%2fgo%2fazure%2ftoc.json)
* [<span data-ttu-id="f18c7-146">连接到 Azure Database for PostgreSQL</span><span class="sxs-lookup"><span data-stu-id="f18c7-146">Connect to Azure Database for PostgreSQL</span></span>](/azure/postgresql/connect-go?toc=%2fgo%2fazure%2ftoc.json)

<span data-ttu-id="f18c7-147">若要立即在 Go SDK 中开始使用其他服务，请查看一些可用的示例代码。</span><span class="sxs-lookup"><span data-stu-id="f18c7-147">If you want to get started with other services in the Go SDK immediately, take a look at some of the available sample code.</span></span>

* [<span data-ttu-id="f18c7-148">在 Azure 服务中进行身份验证</span><span class="sxs-lookup"><span data-stu-id="f18c7-148">Authenticate with Azure services</span></span>](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/iam)
* [<span data-ttu-id="f18c7-149">使用 SSH 身份验证部署新虚拟机</span><span class="sxs-lookup"><span data-stu-id="f18c7-149">Deploy new virtual machines with SSH authentication</span></span>](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/compute)
* [<span data-ttu-id="f18c7-150">将容器映像部署到 Azure 容器实例</span><span class="sxs-lookup"><span data-stu-id="f18c7-150">Deploy a container image to Azure Container Instances</span></span>](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/containerinstance)
* [<span data-ttu-id="f18c7-151">在 Azure Kubernetes 服务中创建群集</span><span class="sxs-lookup"><span data-stu-id="f18c7-151">Create a cluster in Azure Kubernetes Service</span></span>](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/containerservice)
* [<span data-ttu-id="f18c7-152">使用 Azure 存储服务</span><span class="sxs-lookup"><span data-stu-id="f18c7-152">Work with Azure Storage services</span></span>](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/storage)
* [<span data-ttu-id="f18c7-153">Azure SDK for Go 的所有示例</span><span class="sxs-lookup"><span data-stu-id="f18c7-153">All samples for the Azure SDK for Go</span></span>](https://github.com/azure-samples/azure-sdk-for-go-samples)
