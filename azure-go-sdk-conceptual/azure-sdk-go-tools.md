---
title: 面向 Go 开发人员的工具
description: 操作 Azure SDK for Go 和 Azure 服务时可用的工具
author: sptramer
ms.author: sttramer
manager: carmonm
ms.date: 07/13/2018
ms.topic: conceptual
ms.prod: azure
ms.technology: azure-sdk-go
ms.devlang: go
ms.openlocfilehash: dfa3912ac13e6f6d52d607f9dcc150f3a5b57602
ms.sourcegitcommit: d1790b317a8fcb4d672c654dac2a925a976589d4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/14/2018
ms.locfileid: "39039499"
---
# <a name="tools-for-developers-using-the-azure-sdk-for-go"></a><span data-ttu-id="78297-103">使用 Azure SDK for Go 的开发人员可用的工具</span><span class="sxs-lookup"><span data-stu-id="78297-103">Tools for developers using the Azure SDK for Go</span></span>

<span data-ttu-id="78297-104">若要有效编写 Go 代码并使其密切配合 Azure 服务，可以使用以下建议的工具。</span><span class="sxs-lookup"><span data-stu-id="78297-104">To be effective at writing Go code and have it work seamlessly with Azure services, here are some recommended tools.</span></span>

## <a name="azure-cli"></a><span data-ttu-id="78297-105">Azure CLI</span><span class="sxs-lookup"><span data-stu-id="78297-105">Azure CLI</span></span>

<span data-ttu-id="78297-106">Azure CLI 提供一个用于在订阅中创建和配置 Azure 资源的命令行接口。</span><span class="sxs-lookup"><span data-stu-id="78297-106">The Azure CLI provides a command-line interface to create and configure Azure resources in your subscriptions.</span></span> <span data-ttu-id="78297-107">借助 CLI 可以快速开始生成通用和共享的 Azure 资源，以便专注于服务的更复杂用法。</span><span class="sxs-lookup"><span data-stu-id="78297-107">The CLI can help you get started building common and shared Azure resources quickly, so that you can focus on more complex usage of services.</span></span> <span data-ttu-id="78297-108">CLI 提供查询和筛选功能，使用它可以通过管道将输出直接传送到偏好的命令行工具。</span><span class="sxs-lookup"><span data-stu-id="78297-108">The CLI has query and filtering features so you can pipe output directly to your favorite command-line tools.</span></span> <span data-ttu-id="78297-109">可在本地系统上以 Docker 映像的形式或者通过 [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) 来安装 CLI。</span><span class="sxs-lookup"><span data-stu-id="78297-109">The CLI is available for installation on your local system, as a Docker image, or through [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview).</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="78297-110">安装 Azure CLI</span><span class="sxs-lookup"><span data-stu-id="78297-110">Install the Azure CLI</span></span>](/cli/azure/install-azure-cli)

## <a name="visual-studio-code"></a><span data-ttu-id="78297-111">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="78297-111">Visual Studio Code</span></span>

<span data-ttu-id="78297-112">Visual Studio Code 是一个轻型编辑器，它通过扩展针对 Go 语言提供全面的支持。</span><span class="sxs-lookup"><span data-stu-id="78297-112">Visual Studio Code is a lightweight editor that has comprehensive support for the Go language through extensions.</span></span> <span data-ttu-id="78297-113">这些扩展包括对自动填充、`impl` 模板、重构和调试等功能的支持。</span><span class="sxs-lookup"><span data-stu-id="78297-113">These extensions include support for features like autocomplete, `impl` templates, refactoring, and debugging.</span></span> <span data-ttu-id="78297-114">Visual Studio Code 还为常用的开发人员工具（例如源代码管理）提供许多扩展，甚至提供扩展来直接与 Azure 服务交互。</span><span class="sxs-lookup"><span data-stu-id="78297-114">Visual Studio Code also offers many extensions for common developer tools such as source control, and even offers extensions for direct interactions with Azure services.</span></span> <span data-ttu-id="78297-115">Microsoft 维护一个官方的元扩展，其中包括这些 Azure 扩展，以及 Azure CLI 的交互接口。</span><span class="sxs-lookup"><span data-stu-id="78297-115">Microsoft maintains an official meta-extension including these Azure extensions, including an interactive interface for the Azure CLI.</span></span>

* [<span data-ttu-id="78297-116">安装 Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="78297-116">Install Visual Studio Code</span></span>](https://code.visualstudio.com/Download)
* [<span data-ttu-id="78297-117">获取 Visual Studio Code Go 扩展</span><span class="sxs-lookup"><span data-stu-id="78297-117">Get the Visual Studio Code Go extension</span></span>](https://code.visualstudio.com/docs/languages/go)
* [<span data-ttu-id="78297-118">获取 Azure 工具扩展</span><span class="sxs-lookup"><span data-stu-id="78297-118">Get the Azure Tools extension</span></span>](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-azureextensionpack)

## <a name="cicd-with-azure-devops-project"></a><span data-ttu-id="78297-119">将 Azure DevOps Project 与 CI/CD 配合使用</span><span class="sxs-lookup"><span data-stu-id="78297-119">CI/CD with Azure DevOps Project</span></span>

<span data-ttu-id="78297-120">使用 Azure DevOps Project 管道，可以为 Go 应用程序设置持续生成和部署。</span><span class="sxs-lookup"><span data-stu-id="78297-120">With the Azure DevOps Project pipeline, you can set up a continuous build and deploy for your Go applications.</span></span> <span data-ttu-id="78297-121">只需一个可用 git 存储库，便可进行设置，以便直接在 Azure 资源上部署和测试。</span><span class="sxs-lookup"><span data-stu-id="78297-121">All you need is an available git repo, and you can set up to deploy and test directly on your Azure resources.</span></span> <span data-ttu-id="78297-122">配置管道易于创建和管理，并且由于它直接在 Azure 上预配，因此你可以采用处理其他 Azure 资源的同一方式控制它。</span><span class="sxs-lookup"><span data-stu-id="78297-122">The configuration pipeline is easy to create and manage, and since it's provisioned directly on Azure, you can control it in the same way that you handle your other Azure resources.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="78297-123">了解如何使用 Azure DevOps Project 创建 CI/CD 管道</span><span class="sxs-lookup"><span data-stu-id="78297-123">Learn how to create a CI/CD pipeline with Azure DevOps Project</span></span>](/devops-project/azure-devops-project-go)

## <a name="dependency-management-with-dep"></a><span data-ttu-id="78297-124">使用 dep 进行依赖关系管理</span><span class="sxs-lookup"><span data-stu-id="78297-124">Dependency management with dep</span></span>

<span data-ttu-id="78297-125">由于尚未官方的解决方案，我们可以通过多种方法来管理包的依赖关系以及执行 Go 引用 (vendor)。</span><span class="sxs-lookup"><span data-stu-id="78297-125">There are many ways to manage your package dependencies and do vendoring with Go, since there is no official solution yet.</span></span> <span data-ttu-id="78297-126">执行此管理的建议方法是使用 `dep` 依赖关系管理器。</span><span class="sxs-lookup"><span data-stu-id="78297-126">The recommended way to perform this management is with the `dep` dependency manager.</span></span> <span data-ttu-id="78297-127">Azure SDK for Go 使用 dep 执行引用，并且保证使用 dep 可以正确获取其他任何项目的依赖关系。</span><span class="sxs-lookup"><span data-stu-id="78297-127">The Azure SDK for Go uses dep for its vendoring, and is guaranteed to correctly get dependencies for any other project using dep.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="78297-128">获取 dep 依赖关系管理器</span><span class="sxs-lookup"><span data-stu-id="78297-128">Get the dep dependency manager</span></span>](https://github.com/golang/dep)
