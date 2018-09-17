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
# <a name="tools-for-developers-using-the-azure-sdk-for-go"></a>使用 Azure SDK for Go 的开发人员可用的工具

若要有效编写 Go 代码并使其密切配合 Azure 服务，可以使用以下建议的工具。

## <a name="azure-cli"></a>Azure CLI

Azure CLI 提供一个用于在订阅中创建和配置 Azure 资源的命令行接口。 借助 CLI 可以快速开始生成通用和共享的 Azure 资源，以便专注于服务的更复杂用法。 CLI 提供查询和筛选功能，使用它可以通过管道将输出直接传送到偏好的命令行工具。 可在本地系统上以 Docker 映像的形式或者通过 [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) 来安装 CLI。

> [!div class="nextstepaction"]
> [安装 Azure CLI](/cli/azure/install-azure-cli)

## <a name="visual-studio-code"></a>Visual Studio Code

Visual Studio Code 是一个提供 Go 支持的轻量级编辑器。 此扩展提供自动完成、`impl` 模板、重构和调试等功能。 Visual Studio Code 还支持编辑器内的源代码管理访问，以及使用 Azure 服务的扩展。

* [安装 Visual Studio Code](https://code.visualstudio.com/Download)
* [获取 Visual Studio Code Go 扩展](https://code.visualstudio.com/docs/languages/go)
* [获取 Visual Studio Azure 工具扩展](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-azureextensionpack)

## <a name="cicd-with-azure-devops-project"></a>将 Azure DevOps Project 与 CI/CD 配合使用

使用 Azure DevOps Project 管道可以为 Go 应用程序设置持续集成系统。 它所需要的只是一个 git 存储库，你可以直接在 Azure 上进行部署和测试。

> [!div class="nextstepaction"]
> [了解如何使用 Azure DevOps Project 创建 CI/CD 管道](/azure/devops-project/azure-devops-project-go)

## <a name="dependency-management-with-dep"></a>使用 dep 进行依赖关系管理

Azure SDK for Go 使用 dep 进行依赖关系管理。 使用 dep 命令可以提取 Go 应用程序的供应商需求、避免版本冲突并确保项目正常运行。

> [!div class="nextstepaction"]
> [获取 dep 依赖关系管理器](https://github.com/golang/dep)
