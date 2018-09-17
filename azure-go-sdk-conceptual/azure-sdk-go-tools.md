---
title: 使用 Azure SDK for Go 的开发人员可用的工具
description: 操作 Azure SDK for Go 和 Azure 服务时可用的工具
author: sptramer
ms.author: sttramer
manager: carmonm
ms.date: 09/05/2018
ms.topic: conceptual
ms.prod: azure
ms.technology: azure-sdk-go
ms.devlang: go
ms.openlocfilehash: 70cf7d645f47df29e8e42599a0acd75858144783
ms.sourcegitcommit: 8b9e10b960150dc08f046ab840d6a5627410db29
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/07/2018
ms.locfileid: "44059197"
---
# <a name="tools-for-developers-using-the-azure-sdk-for-go"></a><span data-ttu-id="7b768-103">使用 Azure SDK for Go 的开发人员可用的工具</span><span class="sxs-lookup"><span data-stu-id="7b768-103">Tools for developers using the Azure SDK for Go</span></span>

<span data-ttu-id="7b768-104">若要有效编写 Go 代码并使其密切配合 Azure 服务，可以使用以下建议的工具。</span><span class="sxs-lookup"><span data-stu-id="7b768-104">To be effective at writing Go code and have it work seamlessly with Azure services, here are some recommended tools.</span></span>

## <a name="azure-cli"></a><span data-ttu-id="7b768-105">Azure CLI</span><span class="sxs-lookup"><span data-stu-id="7b768-105">Azure CLI</span></span>

<span data-ttu-id="7b768-106">Azure CLI 提供一个用于在订阅中创建和配置 Azure 资源的命令行接口。</span><span class="sxs-lookup"><span data-stu-id="7b768-106">The Azure CLI provides a command-line interface to create and configure Azure resources in your subscriptions.</span></span> <span data-ttu-id="7b768-107">借助 CLI 可以快速开始生成通用和共享的 Azure 资源，以便专注于服务的更复杂用法。</span><span class="sxs-lookup"><span data-stu-id="7b768-107">The CLI can help you get started building common and shared Azure resources quickly, so that you can focus on more complex usage of services.</span></span> <span data-ttu-id="7b768-108">CLI 提供查询和筛选功能，使用它可以通过管道将输出直接传送到偏好的命令行工具。</span><span class="sxs-lookup"><span data-stu-id="7b768-108">The CLI has query and filtering features so you can pipe output directly to your favorite command-line tools.</span></span> <span data-ttu-id="7b768-109">可在本地系统上以 Docker 映像的形式或者通过 [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) 来安装 CLI。</span><span class="sxs-lookup"><span data-stu-id="7b768-109">The CLI is available for installation on your local system, as a Docker image, or through [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview).</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="7b768-110">安装 Azure CLI</span><span class="sxs-lookup"><span data-stu-id="7b768-110">Install the Azure CLI</span></span>](/cli/azure/install-azure-cli)

## <a name="visual-studio-code"></a><span data-ttu-id="7b768-111">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7b768-111">Visual Studio Code</span></span>

<span data-ttu-id="7b768-112">Visual Studio Code 是一个提供 Go 支持的轻量级编辑器。</span><span class="sxs-lookup"><span data-stu-id="7b768-112">Visual Studio Code is a lightweight editor that offers Go support.</span></span> <span data-ttu-id="7b768-113">此扩展提供自动完成、`impl` 模板、重构和调试等功能。</span><span class="sxs-lookup"><span data-stu-id="7b768-113">This extension offers features like autocomplete, `impl` templates, refactoring, and debugging.</span></span> <span data-ttu-id="7b768-114">Visual Studio Code 还支持编辑器内的源代码管理访问，以及使用 Azure 服务的扩展。</span><span class="sxs-lookup"><span data-stu-id="7b768-114">Visual Studio Code also offers support for in-editor access to source control, and extensions for working with Azure services.</span></span>

* [<span data-ttu-id="7b768-115">安装 Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7b768-115">Install Visual Studio Code</span></span>](https://code.visualstudio.com/Download)
* [<span data-ttu-id="7b768-116">获取 Visual Studio Code Go 扩展</span><span class="sxs-lookup"><span data-stu-id="7b768-116">Get the Visual Studio Code Go extension</span></span>](https://code.visualstudio.com/docs/languages/go)
* [<span data-ttu-id="7b768-117">获取 Visual Studio Azure 工具扩展</span><span class="sxs-lookup"><span data-stu-id="7b768-117">Get the Visual Studio Code Azure Tools extension</span></span>](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-azureextensionpack)

## <a name="cicd-with-azure-devops-project"></a><span data-ttu-id="7b768-118">将 Azure DevOps Project 与 CI/CD 配合使用</span><span class="sxs-lookup"><span data-stu-id="7b768-118">CI/CD with Azure DevOps Project</span></span>

<span data-ttu-id="7b768-119">使用 Azure DevOps Project 管道可以为 Go 应用程序设置持续集成系统。</span><span class="sxs-lookup"><span data-stu-id="7b768-119">Azure DevOps Project pipelines allow you to set up a continuous integration system for your Go applications.</span></span> <span data-ttu-id="7b768-120">它所需要的只是一个 git 存储库，你可以直接在 Azure 上进行部署和测试。</span><span class="sxs-lookup"><span data-stu-id="7b768-120">All it takes is a git repo, and you can deploy and test directly on Azure.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="7b768-121">了解如何使用 Azure DevOps Project 创建 CI/CD 管道</span><span class="sxs-lookup"><span data-stu-id="7b768-121">Learn how to create a CI/CD pipeline with Azure DevOps Project</span></span>](/azure/devops-project/azure-devops-project-go)

## <a name="dependency-management-with-dep"></a><span data-ttu-id="7b768-122">使用 dep 进行依赖关系管理</span><span class="sxs-lookup"><span data-stu-id="7b768-122">Dependency management with dep</span></span>

<span data-ttu-id="7b768-123">Azure SDK for Go 使用 dep 进行依赖关系管理。</span><span class="sxs-lookup"><span data-stu-id="7b768-123">The Azure SDK for Go uses dep for dependency management.</span></span> <span data-ttu-id="7b768-124">使用 dep 命令可以提取 Go 应用程序的供应商需求、避免版本冲突并确保项目正常运行。</span><span class="sxs-lookup"><span data-stu-id="7b768-124">The dep command allows you to pull and vendor requirements for your Go application, avoiding version conflicts and ensuring that your project works correctly.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="7b768-125">获取 dep 依赖关系管理器</span><span class="sxs-lookup"><span data-stu-id="7b768-125">Get the dep dependency manager</span></span>](https://github.com/golang/dep)
