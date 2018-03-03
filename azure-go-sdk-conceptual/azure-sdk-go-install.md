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
# <a name="installing-the-azure-sdk-for-go"></a>安装 Azure SDK for Go

欢迎使用 Azure SDK for Go！ 使用此 SDK 可以管理 Go 应用程序中的 Azure 服务并与之交互。

## <a name="get-the-azure-sdk-for-go"></a>获取 Azure SDK for Go

[!INCLUDE [azure-sdk-go-get](includes/azure-sdk-go-get.md)]

使用 Azure 存储 Blob 需要单独的 SDK。

```bash
go get -u -d github.com/Azure/azure-storage-blob-go/...
```

## <a name="vendoring-the-azure-sdk-for-go"></a>引用 (vendor) Azure SDK for Go

可以通过 [dep](https://github.com/golang/dep) 引用 Azure SDK for Go。 出于稳定原因，我们建议引用它。 若要使用 `dep` 支持，请将 `github.com/Azure/azure-sdk-for-go` 添加到 `Gopkg.toml` 的 `[[constraint]]` 节。 例如，若要引用版本 `14.0.0`，请添加以下条目：

```
[[constraint]]
name = "github.com/Azure/azure-sdk-for-go"
version = "14.0.0"
```

## <a name="including-the-azure-sdk-for-go-in-your-project"></a>在项目中包含 Azure SDK for Go

若要通过 Go 代码使用 Azure 服务，请导入所要交互的任何服务和所需的 `autorest` 模块。
可以从 GoDoc 获取[可用服务](https://godoc.org/github.com/Azure/azure-sdk-for-go)和 [AutoRest 包](https://godoc.org/github.com/Azure/go-autorest)的完整可用模块列表。 需要在 `go-autorest` 中使用的最常见包为：

| 程序包 | 说明 |
|---------|-------------|
| [github.com/Azure/go-autorest/autorest][autorest] | 用于处理服务客户端身份验证的对象 |
| [github.com/Azure/go-autorest/autorest/azure][autorest/azure] | 用于与 Azure 服务交互的常量 |
| [github.com/Azure/go-autorest/autorest/adal][autorest/adal] | 用于访问 Azure 服务的身份验证机制 |
| [github.com/Azure/go-autorest/autorest/to][autorest/to] | 用于使用 Azure SDK 数据结构的类型断言帮助器 |

[autorest]: https://godoc.org/github.com/Azure/go-autorest/autorest
[autorest/azure]: https://godoc.org/github.com/Azure/go-autorest/autorest/azure
[autorest/adal]: https://godoc.org/github.com/Azure/go-autorest/autorest/adal
[autorest/to]: https://godoc.org/github.com/Azure/go-autorest/autorest/to

Azure 模块服务的版本控制独立于模块的 SDK API。 这些版本是模块导入路径的一部分，来自_服务版本_或_配置文件_。 目前，我们建议将特定的服务版本用于开发和发布。 服务位于 `services` 模块下面。 导入的完整路径是服务名称，后接 `YYYY-MM-DD` 格式的版本，再后接服务名称。 例如，若要包含计算服务的 `2017-03-30` 版本：

```go
import "github.com/Azure/azure-sdk-for-go/services/compute/mgmt/2017-03-30/compute"
```

目前，除外另有原因，否则我们建议使用最新版本的服务。

如果需要服务的统一快照，也可以选择单个配置文件版本。 目前，唯一锁定的配置文件为版本 `2017-03-30`，其中可能不包含服务的最新功能。 配置文件位于 `profiles` 模块下，其版本采用 `YYYY-MM-DD` 格式。 服务根据其配置文件版本分组。 例如，若要从 `2017-03-09` 配置文件导入 Azure 资源管理模块：

```go
import "github.com/Azure/azure-sdk-for-go/profiles/2017-03-09/resources/mgmt/resources"
```

> [!WARNING]
> 还可以使用 `preview` 和 `latest` 配置文件， 但不建议使用。 这些配置文件的版本会不断滚动更新，服务的行为随时可能更改。

## <a name="next-steps"></a>后续步骤

若要开始使用 Azure SDK for Go，请从快速入门着手。

* [从模板部署虚拟机](azure-sdk-go-qs-vm.md)
* [使用 Azure SDK for Go 将对象传输到 Azure Blob 存储](/azure/storage/blobs/storage-quickstart-blobs-go?toc=%2fgo%2fazure%2ftoc.json)
* [连接到 Azure Database for PostgreSQL](/azure/postgresql/connect-go?toc=%2fgo%2fazure%2ftoc.json)

若要立即在 Go SDK 中开始使用其他服务，请查看一些可用的示例代码。

* [在 Azure 服务中进行身份验证](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/iam)
* [使用 SSH 身份验证部署新虚拟机](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/compute)
* [将容器映像部署到 Azure 容器实例](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/containerinstance)
* [在 Azure Kubernetes 服务中创建群集](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/containerservice)
* [使用 Azure 存储服务](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/storage)
* [Azure SDK for Go 的所有示例](https://github.com/azure-samples/azure-sdk-for-go-samples)
