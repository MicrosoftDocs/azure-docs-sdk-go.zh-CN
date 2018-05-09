---
author: sptramer
ms.author: sttramer
manager: carmonm
ms.date: 02/14/2018
ms.topic: include
ms.prod: azure
ms.technology: azure-cli
ms.openlocfilehash: ddd58efdbc0c2d3ded068a9bebf2466db702566f
ms.sourcegitcommit: f08abf902b48f8173aa6e261084ff2cfc9043305
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/03/2018
---
<span data-ttu-id="f9a4d-101">[Azure SDK for Go](https://github.com/Azure/azure-sdk-for-go) 与 Go 1.8 和更高版本兼容。</span><span class="sxs-lookup"><span data-stu-id="f9a4d-101">The [Azure SDK for Go](https://github.com/Azure/azure-sdk-for-go) is compatible with Go versions 1.8 and later.</span></span> <span data-ttu-id="f9a4d-102">对于使用 [Azure Stack 配置文件](https://docs.microsoft.com/en-us/azure/azure-stack/azure-stack-version-profiles)的环境，最低需要 Go 版本 1.9。</span><span class="sxs-lookup"><span data-stu-id="f9a4d-102">For environments using [Azure Stack Profiles](https://docs.microsoft.com/en-us/azure/azure-stack/azure-stack-version-profiles), Go version 1.9 is the minimum requirement.</span></span>
<span data-ttu-id="f9a4d-103">如果系统上没有可用的 Go，请遵循 [Go 安装说明](https://golang.org/doc/install)。</span><span class="sxs-lookup"><span data-stu-id="f9a4d-103">If you do not have Go available on your system, follow [the Go installation instructions](https://golang.org/doc/install).</span></span>

<span data-ttu-id="f9a4d-104">可以通过 `go get` 获取 Azure SDK for Go 及其依赖项。</span><span class="sxs-lookup"><span data-stu-id="f9a4d-104">You can obtain the Azure SDK for Go and its dependencies via `go get`.</span></span>

```bash
go get -u -d github.com/Azure/azure-sdk-for-go/...
```

> [!WARNING]
> <span data-ttu-id="f9a4d-105">请务必在 URL 中将 `Azure` 大写。</span><span class="sxs-lookup"><span data-stu-id="f9a4d-105">Make sure that you capitalize `Azure` in the URL.</span></span> <span data-ttu-id="f9a4d-106">否则，在使用该 SDK 时，可能导致大小写相关的导入问题。</span><span class="sxs-lookup"><span data-stu-id="f9a4d-106">Doing otherwise can cause case-related import problems when working with the SDK.</span></span> <span data-ttu-id="f9a4d-107">此外，还需要在导入语句中将 `Azure` 大写。</span><span class="sxs-lookup"><span data-stu-id="f9a4d-107">You also need to capitalize `Azure` in your import statements.</span></span>

