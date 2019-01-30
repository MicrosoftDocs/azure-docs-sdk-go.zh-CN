---
author: sptramer
ms.author: sttramer
manager: carmonm
ms.date: 09/05/2018
ms.topic: include
ms.prod: azure
ms.technology: azure-cli
ms.openlocfilehash: 5df14f939efdd0550b49261c88c8dc6518ada459
ms.sourcegitcommit: 3d26b464f196f8675c636ae792637d4c882fb92c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/14/2018
ms.locfileid: "55145543"
---
[Azure SDK for Go](https://github.com/Azure/azure-sdk-for-go) 与 Go 1.8 版和更高版本兼容。 对于使用 [Azure Stack 配置文件](/azure/azure-stack/user/azure-stack-version-profiles-go)的环境，最低需要 Go 版本 1.9。
如果需要安装 Go，请按照 [Go 安装说明](https://golang.org/doc/install)进行操作。

可以通过 `go get` 下载 Azure SDK for Go 及其依赖项。

```bash
go get -u -d github.com/Azure/azure-sdk-for-go/...
```

> [!WARNING]
> 请务必在 URL 中将 `Azure` 大写。 否则，在使用该 SDK 时，可能导致大小写相关的导入问题。 此外，还需要在导入语句中将 `Azure` 大写。
