---
author: sptramer
ms.author: sttramer
manager: carmonm
ms.date: 09/05/2018
ms.topic: include
ms.prod: azure
ms.technology: azure-cli
ms.openlocfilehash: 5df14f939efdd0550b49261c88c8dc6518ada459
ms.sourcegitcommit: 8b9e10b960150dc08f046ab840d6a5627410db29
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/07/2018
ms.locfileid: "44059234"
---
<span data-ttu-id="6e305-101">[Azure SDK for Go](https://github.com/Azure/azure-sdk-for-go) 与 Go 1.8 版和更高版本兼容。</span><span class="sxs-lookup"><span data-stu-id="6e305-101">The [Azure SDK for Go](https://github.com/Azure/azure-sdk-for-go) is compatible with Go versions 1.8 and higher.</span></span> <span data-ttu-id="6e305-102">对于使用 [Azure Stack 配置文件](/azure/azure-stack/user/azure-stack-version-profiles-go)的环境，最低需要 Go 版本 1.9。</span><span class="sxs-lookup"><span data-stu-id="6e305-102">For environments using [Azure Stack Profiles](/azure/azure-stack/user/azure-stack-version-profiles-go), Go version 1.9 is the minimum requirement.</span></span>
<span data-ttu-id="6e305-103">如果需要安装 Go，请按照 [Go 安装说明](https://golang.org/doc/install)进行操作。</span><span class="sxs-lookup"><span data-stu-id="6e305-103">If you need to install Go, follow [the Go installation instructions](https://golang.org/doc/install).</span></span>

<span data-ttu-id="6e305-104">可以通过 `go get` 下载 Azure SDK for Go 及其依赖项。</span><span class="sxs-lookup"><span data-stu-id="6e305-104">You can download the Azure SDK for Go and its dependencies via `go get`.</span></span>

```bash
go get -u -d github.com/Azure/azure-sdk-for-go/...
```

> [!WARNING]
> <span data-ttu-id="6e305-105">请务必在 URL 中将 `Azure` 大写。</span><span class="sxs-lookup"><span data-stu-id="6e305-105">Make sure that you capitalize `Azure` in the URL.</span></span> <span data-ttu-id="6e305-106">否则，在使用该 SDK 时，可能导致大小写相关的导入问题。</span><span class="sxs-lookup"><span data-stu-id="6e305-106">Doing otherwise can cause case-related import problems when working with the SDK.</span></span> <span data-ttu-id="6e305-107">此外，还需要在导入语句中将 `Azure` 大写。</span><span class="sxs-lookup"><span data-stu-id="6e305-107">You also need to capitalize `Azure` in your import statements.</span></span>
