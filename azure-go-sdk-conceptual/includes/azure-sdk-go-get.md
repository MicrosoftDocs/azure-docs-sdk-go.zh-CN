---
author: sptramer
ms.author: sttramer
manager: carmonm
ms.date: 02/14/2018
ms.topic: include
ms.prod: azure
ms.technology: azure-cli
ms.openlocfilehash: d021dd8ef4744b7c50b296b231bf63481f92411a
ms.sourcegitcommit: 2a3bd491e087a1d0e7d269bed896c029357d62a6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/12/2018
ms.locfileid: "38988056"
---
[Azure SDK for Go](https://github.com/Azure/azure-sdk-for-go) 与 Go 1.8 和更高版本兼容。 对于使用 [Azure Stack 配置文件](https://docs.microsoft.com/azure/azure-stack/azure-stack-version-profiles)的环境，最低需要 Go 版本 1.9。
如果系统上没有可用的 Go，请遵循 [Go 安装说明](https://golang.org/doc/install)。

可以通过 `go get` 获取 Azure SDK for Go 及其依赖项。

```bash
go get -u -d github.com/Azure/azure-sdk-for-go/...
```

> [!WARNING]
> 请务必在 URL 中将 `Azure` 大写。 否则，在使用该 SDK 时，可能导致大小写相关的导入问题。 此外，还需要在导入语句中将 `Azure` 大写。
