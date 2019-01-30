---
title: 安装 Azure SDK for Go
description: 如何安装、引用 (vendor) 和配置 Azure SDK for Go。
author: sptramer
ms.author: sttramer
manager: carmonm
ms.date: 03/14/2018
ms.topic: conceptual
ms.prod: azure
ms.technology: azure-sdk-go
ms.devlang: go
ms.openlocfilehash: 2799e3a6c637036eeaf7b20adf8aa55a8a4ab400
ms.sourcegitcommit: 4db332f5e43a5b43032ff9017805d5fd5a650d86
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/28/2019
ms.locfileid: "55145526"
---
# <a name="install-the-azure-sdk-for-go"></a><span data-ttu-id="09b51-103">安装 Azure SDK for Go</span><span class="sxs-lookup"><span data-stu-id="09b51-103">Install the Azure SDK for Go</span></span>

<span data-ttu-id="09b51-104">欢迎使用 Azure SDK for Go！</span><span class="sxs-lookup"><span data-stu-id="09b51-104">Welcome to the Azure SDK for Go!</span></span> <span data-ttu-id="09b51-105">使用该 SDK 可以管理 Go 应用程序中的 Azure 服务并与之交互。</span><span class="sxs-lookup"><span data-stu-id="09b51-105">The SDK allows you to manage and interact with Azure services from your Go applications.</span></span>

## <a name="get-the-azure-sdk-for-go"></a><span data-ttu-id="09b51-106">获取 Azure SDK for Go</span><span class="sxs-lookup"><span data-stu-id="09b51-106">Get the Azure SDK for Go</span></span>

[!INCLUDE [azure-sdk-go-get](includes/azure-sdk-go-get.md)]

<span data-ttu-id="09b51-107">某些 Azure 服务具有其自己的 Go SDK，并未包括在核心 Azure SDK for Go 包中。</span><span class="sxs-lookup"><span data-stu-id="09b51-107">Some Azure services have their own Go SDK and aren't included in the core Azure SDK for Go package.</span></span> <span data-ttu-id="09b51-108">下表列出了具有其自己的 SDK 的服务及其包名称。</span><span class="sxs-lookup"><span data-stu-id="09b51-108">The following table lists the services with their own SDKs and their package names.</span></span> <span data-ttu-id="09b51-109">我们已考虑将这些包置于预览版中。</span><span class="sxs-lookup"><span data-stu-id="09b51-109">These packages are all considered to be in preview.</span></span>

| <span data-ttu-id="09b51-110">服务</span><span class="sxs-lookup"><span data-stu-id="09b51-110">Service</span></span> | <span data-ttu-id="09b51-111">程序包</span><span class="sxs-lookup"><span data-stu-id="09b51-111">Package</span></span> |
|---------|---------|
| <span data-ttu-id="09b51-112">Blob 存储</span><span class="sxs-lookup"><span data-stu-id="09b51-112">Blob Storage</span></span> | [<span data-ttu-id="09b51-113">github.com/Azure/azure-storage-blob-go</span><span class="sxs-lookup"><span data-stu-id="09b51-113">github.com/Azure/azure-storage-blob-go</span></span>](https://github.com/Azure/azure-storage-blob-go) |
| <span data-ttu-id="09b51-114">文件存储</span><span class="sxs-lookup"><span data-stu-id="09b51-114">File Storage</span></span> | [<span data-ttu-id="09b51-115">github.com/Azure/azure-storage-file-go</span><span class="sxs-lookup"><span data-stu-id="09b51-115">github.com/Azure/azure-storage-file-go</span></span>](https://github.com/Azure/azure-storage-file-go) |
| <span data-ttu-id="09b51-116">存储队列</span><span class="sxs-lookup"><span data-stu-id="09b51-116">Storage Queue</span></span> | [<span data-ttu-id="09b51-117">github.com/Azure/azure-storage-queue-go</span><span class="sxs-lookup"><span data-stu-id="09b51-117">github.com/Azure/azure-storage-queue-go</span></span>](https://github.com/Azure/azure-storage-queue-go) |
| <span data-ttu-id="09b51-118">事件中心</span><span class="sxs-lookup"><span data-stu-id="09b51-118">Event Hub</span></span> | [<span data-ttu-id="09b51-119">github.com/Azure/azure-event-hubs-go</span><span class="sxs-lookup"><span data-stu-id="09b51-119">github.com/Azure/azure-event-hubs-go</span></span>](https://github.com/Azure/azure-event-hubs-go) |
| <span data-ttu-id="09b51-120">服务总线</span><span class="sxs-lookup"><span data-stu-id="09b51-120">Service Bus</span></span> | [<span data-ttu-id="09b51-121">github.com/Azure/azure-service-bus-go</span><span class="sxs-lookup"><span data-stu-id="09b51-121">github.com/Azure/azure-service-bus-go</span></span>](https://github.com/Azure/azure-service-bus-go) |
| <span data-ttu-id="09b51-122">Application Insights</span><span class="sxs-lookup"><span data-stu-id="09b51-122">Application Insights</span></span> | [<span data-ttu-id="09b51-123">github.com/Microsoft/ApplicationInsights-go</span><span class="sxs-lookup"><span data-stu-id="09b51-123">github.com/Microsoft/ApplicationInsights-go</span></span>](https://github.com/Microsoft/ApplicationInsights-go) |

## <a name="vendor-the-azure-sdk-for-go"></a><span data-ttu-id="09b51-124">引用 (Vendor) Azure SDK for Go</span><span class="sxs-lookup"><span data-stu-id="09b51-124">Vendor the Azure SDK for Go</span></span>

<span data-ttu-id="09b51-125">可以通过 [dep](https://github.com/golang/dep) 引用 Azure SDK for Go。</span><span class="sxs-lookup"><span data-stu-id="09b51-125">The Azure SDK for Go may be vendored through [dep](https://github.com/golang/dep).</span></span> <span data-ttu-id="09b51-126">出于稳定原因，我们建议引用它。</span><span class="sxs-lookup"><span data-stu-id="09b51-126">For stability reasons, vendoring is recommended.</span></span> <span data-ttu-id="09b51-127">若要在自己的项目中使用 `dep`，请将 `github.com/Azure/azure-sdk-for-go` 添加到 `Gopkg.toml` 的 `[[constraint]]` 节。</span><span class="sxs-lookup"><span data-stu-id="09b51-127">To use `dep` in your own project, add `github.com/Azure/azure-sdk-for-go` to a `[[constraint]]` section of your `Gopkg.toml`.</span></span> <span data-ttu-id="09b51-128">例如，若要引用版本 `14.0.0`，请添加以下条目：</span><span class="sxs-lookup"><span data-stu-id="09b51-128">For example, to vendor on version `14.0.0`, add the following entry:</span></span>

```toml
[[constraint]]
name = "github.com/Azure/azure-sdk-for-go"
version = "14.0.0"
```

## <a name="include-the-azure-sdk-for-go-in-your-project"></a><span data-ttu-id="09b51-129">在项目中包含 Azure SDK for Go</span><span class="sxs-lookup"><span data-stu-id="09b51-129">Include the Azure SDK for Go in your project</span></span>

<span data-ttu-id="09b51-130">若要通过 Go 代码使用 Azure 服务，请导入所要交互的任何服务和所需的 `autorest` 模块。</span><span class="sxs-lookup"><span data-stu-id="09b51-130">To use Azure services from your Go code, import any services you interact with and the required `autorest` modules.</span></span>
<span data-ttu-id="09b51-131">可以从 GoDoc 获取[可用服务](https://godoc.org/github.com/Azure/azure-sdk-for-go)和 [AutoRest 包](https://godoc.org/github.com/Azure/go-autorest)的完整可用模块列表。</span><span class="sxs-lookup"><span data-stu-id="09b51-131">You get a complete list of the available modules from GoDoc for [available services](https://godoc.org/github.com/Azure/azure-sdk-for-go) and [AutoRest packages](https://godoc.org/github.com/Azure/go-autorest).</span></span> <span data-ttu-id="09b51-132">需要在 `go-autorest` 中使用的最常见包为：</span><span class="sxs-lookup"><span data-stu-id="09b51-132">The most common packages you need from `go-autorest` are:</span></span>

| <span data-ttu-id="09b51-133">程序包</span><span class="sxs-lookup"><span data-stu-id="09b51-133">Package</span></span> | <span data-ttu-id="09b51-134">说明</span><span class="sxs-lookup"><span data-stu-id="09b51-134">Description</span></span> |
|---------|-------------|
| <span data-ttu-id="09b51-135">[github.com/Azure/go-autorest/autorest][autorest]</span><span class="sxs-lookup"><span data-stu-id="09b51-135">[github.com/Azure/go-autorest/autorest][autorest]</span></span> | <span data-ttu-id="09b51-136">用于处理服务客户端身份验证的对象</span><span class="sxs-lookup"><span data-stu-id="09b51-136">Objects for handling service client authentication</span></span> |
| <span data-ttu-id="09b51-137">[github.com/Azure/go-autorest/autorest/azure][autorest/azure]</span><span class="sxs-lookup"><span data-stu-id="09b51-137">[github.com/Azure/go-autorest/autorest/azure][autorest/azure]</span></span> | <span data-ttu-id="09b51-138">用于与 Azure 服务交互的常量</span><span class="sxs-lookup"><span data-stu-id="09b51-138">Constants for interactions with Azure services</span></span> |
| <span data-ttu-id="09b51-139">[github.com/Azure/go-autorest/autorest/adal][autorest/adal]</span><span class="sxs-lookup"><span data-stu-id="09b51-139">[github.com/Azure/go-autorest/autorest/adal][autorest/adal]</span></span> | <span data-ttu-id="09b51-140">用于访问 Azure 服务的身份验证机制</span><span class="sxs-lookup"><span data-stu-id="09b51-140">Authentication mechanisms for accessing Azure services</span></span> |
| <span data-ttu-id="09b51-141">[github.com/Azure/go-autorest/autorest/to][autorest/to]</span><span class="sxs-lookup"><span data-stu-id="09b51-141">[github.com/Azure/go-autorest/autorest/to][autorest/to]</span></span> | <span data-ttu-id="09b51-142">用于使用 Azure SDK 数据结构的类型断言帮助器</span><span class="sxs-lookup"><span data-stu-id="09b51-142">Type assertion helpers for working with Azure SDK data structures</span></span> |

[autorest]: https://godoc.org/github.com/Azure/go-autorest/autorest
[autorest/azure]: https://godoc.org/github.com/Azure/go-autorest/autorest/azure
[autorest/adal]: https://godoc.org/github.com/Azure/go-autorest/autorest/adal
[autorest/to]: https://godoc.org/github.com/Azure/go-autorest/autorest/to

<span data-ttu-id="09b51-143">Go 包和 Azure 服务独立进行版本控制。</span><span class="sxs-lookup"><span data-stu-id="09b51-143">Go packages and Azure services are versioned independently.</span></span> <span data-ttu-id="09b51-144">服务版本是模块导入路径的一部分，位于 `services` 模块下方。</span><span class="sxs-lookup"><span data-stu-id="09b51-144">The service versions are part of the module import path, underneath the `services` module.</span></span> <span data-ttu-id="09b51-145">模块的完整路径是服务名称，后接 `YYYY-MM-DD` 格式的版本，再后接服务名称。</span><span class="sxs-lookup"><span data-stu-id="09b51-145">The full path for the module is the name of the service, followed by the version in `YYYY-MM-DD` format, followed by the service name again.</span></span> <span data-ttu-id="09b51-146">例如，若要导入计算服务的 `2017-03-30` 版本：</span><span class="sxs-lookup"><span data-stu-id="09b51-146">For example, to import the `2017-03-30` version of the Compute service:</span></span>

```go
import "github.com/Azure/azure-sdk-for-go/services/compute/mgmt/2017-03-30/compute"
```

<span data-ttu-id="09b51-147">建议你在开始开发时使用最新版本的服务，并保持一致。</span><span class="sxs-lookup"><span data-stu-id="09b51-147">It's recommended that you use the latest version of a service when starting development and keep it consistent.</span></span>
<span data-ttu-id="09b51-148">即使在此期间没有 Go SDK 更新，服务要求也可能会在版本之间发生变化，从而可能破坏你的代码。</span><span class="sxs-lookup"><span data-stu-id="09b51-148">Service requirements may change between versions that could break your code, even if there are no Go SDK updates during that time.</span></span>

<span data-ttu-id="09b51-149">如果需要服务的统一快照，也可以选择单个配置文件版本。</span><span class="sxs-lookup"><span data-stu-id="09b51-149">If you need a collective snapshot of services, you can also select a single profile version.</span></span> <span data-ttu-id="09b51-150">目前，唯一锁定的配置文件为版本 `2017-03-09`，其中可能不包含服务的最新功能。</span><span class="sxs-lookup"><span data-stu-id="09b51-150">Right now, the only locked profile is version `2017-03-09`, which may not have the latest features of services.</span></span> <span data-ttu-id="09b51-151">配置文件位于 `profiles` 模块下，其版本采用 `YYYY-MM-DD` 格式。</span><span class="sxs-lookup"><span data-stu-id="09b51-151">Profiles are located under the `profiles` module, with their version in the `YYYY-MM-DD` format.</span></span> <span data-ttu-id="09b51-152">服务根据其配置文件版本分组。</span><span class="sxs-lookup"><span data-stu-id="09b51-152">Services are grouped under their profile version.</span></span> <span data-ttu-id="09b51-153">例如，若要从 `2017-03-09` 配置文件导入 Azure 资源管理模块：</span><span class="sxs-lookup"><span data-stu-id="09b51-153">For example, to import the Azure Resources management module from the `2017-03-09` profile:</span></span>

```go
import "github.com/Azure/azure-sdk-for-go/profiles/2017-03-09/resources/mgmt/resources"
```

> [!WARNING]
> <span data-ttu-id="09b51-154">还可以使用 `preview` 和 `latest` 配置文件，</span><span class="sxs-lookup"><span data-stu-id="09b51-154">There are also `preview` and `latest` profiles available.</span></span> <span data-ttu-id="09b51-155">但不建议使用。</span><span class="sxs-lookup"><span data-stu-id="09b51-155">Using them is not recommended.</span></span> <span data-ttu-id="09b51-156">这些配置文件的版本会不断滚动更新，服务的行为随时可能更改。</span><span class="sxs-lookup"><span data-stu-id="09b51-156">These profiles are rolling versions and service behavior may change at any time.</span></span>

## <a name="next-steps"></a><span data-ttu-id="09b51-157">后续步骤</span><span class="sxs-lookup"><span data-stu-id="09b51-157">Next steps</span></span>

<span data-ttu-id="09b51-158">若要开始使用 Azure SDK for Go，请从快速入门着手。</span><span class="sxs-lookup"><span data-stu-id="09b51-158">To begin using the Azure SDK for Go, try out a quickstart.</span></span>

* [<span data-ttu-id="09b51-159">从模板部署虚拟机</span><span class="sxs-lookup"><span data-stu-id="09b51-159">Deploy a virtual machine from a template</span></span>](azure-sdk-go-qs-vm.md)
* [<span data-ttu-id="09b51-160">使用 Azure SDK for Go 将对象传输到 Azure Blob 存储</span><span class="sxs-lookup"><span data-stu-id="09b51-160">Transfer objects to Azure Blob Storage with the Azure Blob SDK for Go</span></span>](/azure/storage/blobs/storage-quickstart-blobs-go?toc=%2fgo%2fazure%2ftoc.json)
* [<span data-ttu-id="09b51-161">连接到 Azure Database for PostgreSQL</span><span class="sxs-lookup"><span data-stu-id="09b51-161">Connect to Azure Database for PostgreSQL</span></span>](/azure/postgresql/connect-go?toc=%2fgo%2fazure%2ftoc.json)

<span data-ttu-id="09b51-162">若要立即在 Go SDK 中开始使用其他服务，请查看一些可用的示例代码。</span><span class="sxs-lookup"><span data-stu-id="09b51-162">If you want to get started with other services in the Go SDK immediately, take a look at some of the available sample code.</span></span>

* [<span data-ttu-id="09b51-163">在 Azure 服务中进行身份验证</span><span class="sxs-lookup"><span data-stu-id="09b51-163">Authenticate with Azure services</span></span>](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/internal/iam)
* [<span data-ttu-id="09b51-164">使用 SSH 身份验证部署新虚拟机</span><span class="sxs-lookup"><span data-stu-id="09b51-164">Deploy new virtual machines with SSH authentication</span></span>](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/compute)
* [<span data-ttu-id="09b51-165">将容器映像部署到 Azure 容器实例</span><span class="sxs-lookup"><span data-stu-id="09b51-165">Deploy a container image to Azure Container Instances</span></span>](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/compute)
* [<span data-ttu-id="09b51-166">在 Azure Kubernetes 服务中创建群集</span><span class="sxs-lookup"><span data-stu-id="09b51-166">Create a cluster in Azure Kubernetes Service</span></span>](https://github.com/Azure-Samples/azure-sdk-for-go-samples/blob/master/compute)
* [<span data-ttu-id="09b51-167">使用 Azure 存储服务</span><span class="sxs-lookup"><span data-stu-id="09b51-167">Work with Azure Storage services</span></span>](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/storage)
* [<span data-ttu-id="09b51-168">Azure SDK for Go 的所有示例</span><span class="sxs-lookup"><span data-stu-id="09b51-168">All samples for the Azure SDK for Go</span></span>](https://github.com/azure-samples/azure-sdk-for-go-samples)
