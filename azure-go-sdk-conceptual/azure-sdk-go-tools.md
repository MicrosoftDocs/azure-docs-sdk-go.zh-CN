---
title: 面向 Go 开发人员的工具
description: 操作 Azure SDK for Go 和 Azure 服务时可用的工具
author: sptramer
ms.author: sttramer
ms.date: 01/30/2018
ms.topic: article
ms.devlang: go
manager: carmonm
ms.openlocfilehash: 054965eb1ea4f1a7556e2968dfbe07b2db69d26f
ms.sourcegitcommit: fcc1786d59d2e32c97a9a8e0748e06f564a961bd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/23/2018
---
# <a name="tools-for-developers-using-the-azure-sdk-for-go"></a><span data-ttu-id="88d99-103">使用 Azure SDK for Go 的开发人员可用的工具</span><span class="sxs-lookup"><span data-stu-id="88d99-103">Tools for developers using the Azure SDK for Go</span></span>

<span data-ttu-id="88d99-104">若要有效编写 Go 代码并使其密切配合 Azure 服务，可以使用以下建议的工具。</span><span class="sxs-lookup"><span data-stu-id="88d99-104">To be effective at writing Go code and have it work seamlessly with Azure services, here are some recommended tools.</span></span>

## <a name="azure-cli-20"></a><span data-ttu-id="88d99-105">Azure CLI 2.0</span><span class="sxs-lookup"><span data-stu-id="88d99-105">Azure CLI 2.0</span></span>

<span data-ttu-id="88d99-106">Azure CLI 2.0 提供一个命令行接口用于在订阅中创建和配置 Azure 资源。</span><span class="sxs-lookup"><span data-stu-id="88d99-106">The Azure 2.0 CLI provides a command-line interface to create and configure Azure resources in your subscriptions.</span></span> <span data-ttu-id="88d99-107">借助 CLI 可以快速开始生成通用和共享的 Azure 资源，以便专注于服务的更复杂用法。</span><span class="sxs-lookup"><span data-stu-id="88d99-107">The CLI can help you get started building common and shared Azure resources quickly, so that you can focus on more complex usage of services.</span></span> <span data-ttu-id="88d99-108">CLI 提供查询和筛选功能，使用它可以通过管道将输出直接传送到偏好的命令行工具。</span><span class="sxs-lookup"><span data-stu-id="88d99-108">The CLI has query and filtering features so you can pipe output directly to your favorite command-line tools.</span></span> <span data-ttu-id="88d99-109">可在本地系统上以 Docker 映像的形式或者通过 [Azure Cloud Shell](https://docs.microsoft.com/en-us/azure/cloud-shell/overview) 来安装 CLI。</span><span class="sxs-lookup"><span data-stu-id="88d99-109">The CLI is available for installation on your local system, as a Docker image, or through [Azure Cloud Shell](https://docs.microsoft.com/en-us/azure/cloud-shell/overview).</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="88d99-110">安装 Azure CLI 2.0</span><span class="sxs-lookup"><span data-stu-id="88d99-110">Install the Azure CLI 2.0</span></span>](/cli/azure/install-azure-cli)

## <a name="visual-studio-code"></a><span data-ttu-id="88d99-111">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="88d99-111">Visual Studio Code</span></span>

<span data-ttu-id="88d99-112">Visual Studio Code 是一个轻型编辑器，它通过扩展针对 Go 语言提供全面的支持。</span><span class="sxs-lookup"><span data-stu-id="88d99-112">Visual Studio Code is a lightweight editor that has comprehensive support for the Go language through extensions.</span></span> <span data-ttu-id="88d99-113">这些扩展包括对自动填充、`impl` 模板、重构和调试等功能的支持。</span><span class="sxs-lookup"><span data-stu-id="88d99-113">These extensions include support for features like autocomplete, `impl` templates, refactoring, and debugging.</span></span> <span data-ttu-id="88d99-114">Visual Studio Code 还为常用的开发人员工具（例如源代码管理）提供许多扩展，甚至提供扩展来直接与 Azure 服务交互。</span><span class="sxs-lookup"><span data-stu-id="88d99-114">Visual Studio Code also offers many extensions for common developer tools such as source control, and even offers extensions for direct interactions with Azure services.</span></span> <span data-ttu-id="88d99-115">Microsoft 维护一个官方的元扩展，其中包括这些 Azure 扩展，以及 Azure CLI 的交互接口。</span><span class="sxs-lookup"><span data-stu-id="88d99-115">Microsoft maintains an official meta-extension including these Azure extensions, including an interactive interface for the Azure CLI.</span></span>

* [<span data-ttu-id="88d99-116">安装 Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="88d99-116">Install Visual Studio Code</span></span>](https://code.visualstudio.com/Download)
* [<span data-ttu-id="88d99-117">获取 Visual Studio Code Go 扩展</span><span class="sxs-lookup"><span data-stu-id="88d99-117">Get the Visual Studio Code Go extension</span></span>](https://code.visualstudio.com/docs/languages/go)
* [<span data-ttu-id="88d99-118">获取 Azure 工具扩展</span><span class="sxs-lookup"><span data-stu-id="88d99-118">Get the Azure Tools extension</span></span>](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-azureextensionpack)

## <a name="dependency-management-with-dep"></a><span data-ttu-id="88d99-119">使用 dep 进行依赖关系管理</span><span class="sxs-lookup"><span data-stu-id="88d99-119">Dependency management with dep</span></span>

<span data-ttu-id="88d99-120">由于尚未官方的解决方案，我们可以通过多种方法来管理包的依赖关系以及执行 Go 引用 (vendor)。</span><span class="sxs-lookup"><span data-stu-id="88d99-120">There are many ways to manage your package dependencies and do vendoring with Go, since there is no official solution yet.</span></span> <span data-ttu-id="88d99-121">执行此管理的建议方法是使用 `dep` 依赖关系管理器。</span><span class="sxs-lookup"><span data-stu-id="88d99-121">The recommended way to perform this management is with the `dep` dependency manager.</span></span> <span data-ttu-id="88d99-122">Azure SDK for Go 使用 dep 执行引用，并且保证使用 dep 可以正确获取其他任何项目的依赖关系。</span><span class="sxs-lookup"><span data-stu-id="88d99-122">The Azure SDK for Go uses dep for its vendoring, and is guaranteed to correctly get dependencies for any other project using dep.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="88d99-123">获取 dep 依赖关系管理器</span><span class="sxs-lookup"><span data-stu-id="88d99-123">Get the dep dependency manager</span></span>](https://github.com/tools/godep)

## <a name="telemetry-with-application-insights"></a><span data-ttu-id="88d99-124">使用 Application Insights 遥测</span><span class="sxs-lookup"><span data-stu-id="88d99-124">Telemetry with Application Insights</span></span>

<span data-ttu-id="88d99-125">[Application Insights](https://azure.microsoft.com/en-us/services/application-insights/) 是一个分析产品，使用它可以轻松地从应用程序收集遥测信息，并与 Azure 生态系统、Visual Studio Team Services 和 GitHub 集成。</span><span class="sxs-lookup"><span data-stu-id="88d99-125">[Application Insights](https://azure.microsoft.com/en-us/services/application-insights/) is an analytics product that allows you to easily collect telemetry information from your applications and integrates with the Azure ecosystem, Visual Studio Team Services, and GitHub.</span></span> <span data-ttu-id="88d99-126">可以在许多应用程序中使用 Application Insights。Microsoft 提供了 Go SDK 来方便使用 Application Insights。</span><span class="sxs-lookup"><span data-stu-id="88d99-126">It can be used in many applications, and Microsoft offers a Go SDK for working with Application Insights.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="88d99-127">获取适用于 Go SDK 的 Application Insights</span><span class="sxs-lookup"><span data-stu-id="88d99-127">Get the Application Insights for Go SDK</span></span>](https://github.com/Microsoft/ApplicationInsights-Go) 
