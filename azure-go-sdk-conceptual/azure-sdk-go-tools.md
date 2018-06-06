---
title: 面向 Go 开发人员的工具
description: 操作 Azure SDK for Go 和 Azure 服务时可用的工具
author: sptramer
ms.author: sttramer
manager: carmonm
ms.date: 01/30/2018
ms.topic: conceptual
ms.prod: azure
ms.technology: azure-sdk-go
ms.devlang: go
ms.openlocfilehash: 1e122ab161766023ea146329a5edb13143699b8b
ms.sourcegitcommit: b81b17cbb934399c195bfdcb87137aee935f5234
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/04/2018
ms.locfileid: "34755526"
---
# <a name="tools-for-developers-using-the-azure-sdk-for-go"></a>使用 Azure SDK for Go 的开发人员可用的工具

若要有效编写 Go 代码并使其密切配合 Azure 服务，可以使用以下建议的工具。

## <a name="azure-cli-20"></a>Azure CLI 2.0

Azure CLI 2.0 提供一个命令行接口用于在订阅中创建和配置 Azure 资源。 借助 CLI 可以快速开始生成通用和共享的 Azure 资源，以便专注于服务的更复杂用法。 CLI 提供查询和筛选功能，使用它可以通过管道将输出直接传送到偏好的命令行工具。 可在本地系统上以 Docker 映像的形式或者通过 [Azure Cloud Shell](https://docs.microsoft.com/en-us/azure/cloud-shell/overview) 来安装 CLI。

> [!div class="nextstepaction"]
> [安装 Azure CLI 2.0](/cli/azure/install-azure-cli)

## <a name="visual-studio-code"></a>Visual Studio Code

Visual Studio Code 是一个轻型编辑器，它通过扩展针对 Go 语言提供全面的支持。 这些扩展包括对自动填充、`impl` 模板、重构和调试等功能的支持。 Visual Studio Code 还为常用的开发人员工具（例如源代码管理）提供许多扩展，甚至提供扩展来直接与 Azure 服务交互。 Microsoft 维护一个官方的元扩展，其中包括这些 Azure 扩展，以及 Azure CLI 的交互接口。

* [安装 Visual Studio Code](https://code.visualstudio.com/Download)
* [获取 Visual Studio Code Go 扩展](https://code.visualstudio.com/docs/languages/go)
* [获取 Azure 工具扩展](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-azureextensionpack)

## <a name="dependency-management-with-dep"></a>使用 dep 进行依赖关系管理

由于尚未官方的解决方案，我们可以通过多种方法来管理包的依赖关系以及执行 Go 引用 (vendor)。 执行此管理的建议方法是使用 `dep` 依赖关系管理器。 Azure SDK for Go 使用 dep 执行引用，并且保证使用 dep 可以正确获取其他任何项目的依赖关系。

> [!div class="nextstepaction"]
> [获取 dep 依赖关系管理器](https://github.com/tools/godep)