---
title: 使用 Azure SDK for Go 进行身份验证
description: 了解 Azure SDK for Go 中提供的身份验证方法及其用法。
services: azure
author: sptramer
ms.author: sttramer
ms.date: 04/03/2018
ms.topic: article
ms.service: azure
ms.devlang: go
manager: carmonm
ms.openlocfilehash: 39f9dc5a7cdf9ab84cfd9264446bacb31392ca80
ms.sourcegitcommit: 59d2b4c9d8da15fbbd15e36551093219fdaf256e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2018
---
# <a name="authentication-methods-in-the-azure-sdk-for-go"></a><span data-ttu-id="0dced-103">Azure SDK for Go 中的身份验证方法</span><span class="sxs-lookup"><span data-stu-id="0dced-103">Authentication methods in the Azure SDK for Go</span></span>

<span data-ttu-id="0dced-104">Azure SDK for Go 提供各种身份验证类型和方法供应用程序使用。</span><span class="sxs-lookup"><span data-stu-id="0dced-104">The Azure SDK for Go offers a variety of authentication types and methods that your application can use.</span></span> <span data-ttu-id="0dced-105">支持的身份验证方法包括从环境变量提取信息，以及基于 Web 的交互式身份验证等等。</span><span class="sxs-lookup"><span data-stu-id="0dced-105">Supported authentication methods range from pulling information from environment variables to interactive web-based authentication.</span></span> <span data-ttu-id="0dced-106">本文介绍该 SDK 中提供的身份验证类型及其用法。</span><span class="sxs-lookup"><span data-stu-id="0dced-106">This article introduces you to the available types of authentication in the SDK, and the methods for using them.</span></span> <span data-ttu-id="0dced-107">此外，还阐述了有关选择最适合应用程序的身份验证类型的最佳做法。</span><span class="sxs-lookup"><span data-stu-id="0dced-107">You'll also learn best practices for selecting which authentication type is right for your application.</span></span>

## <a name="available-authentication-types-and-methods"></a><span data-ttu-id="0dced-108">可用的身份验证类型和方法</span><span class="sxs-lookup"><span data-stu-id="0dced-108">Available authentication types and methods</span></span>

<span data-ttu-id="0dced-109">Azure SDK for Go 提供使用不同凭据集的多种身份验证类型。</span><span class="sxs-lookup"><span data-stu-id="0dced-109">The Azure SDK for Go offers several different types of authentication, using different credentials sets.</span></span> <span data-ttu-id="0dced-110">可通过不同的身份验证方法使用其中的每种身份验证类型，该 SDK 也正是通过这些方法将这些凭据用作输入。</span><span class="sxs-lookup"><span data-stu-id="0dced-110">Each of these authentication types are available through different authentication methods, which are how the SDK takes these credentials as input.</span></span> <span data-ttu-id="0dced-111">下表描述了可用的身份验证类型，以及在哪些场合下建议让应用程序使用这些类型。</span><span class="sxs-lookup"><span data-stu-id="0dced-111">The following table describes the available types of authentication and situations in which they're recommended for use by your application.</span></span>

| <span data-ttu-id="0dced-112">身份验证类型</span><span class="sxs-lookup"><span data-stu-id="0dced-112">Authentication type</span></span> | <span data-ttu-id="0dced-113">建议的使用场合...</span><span class="sxs-lookup"><span data-stu-id="0dced-113">Recommended when...</span></span> |
|---------------------|---------------------|
| <span data-ttu-id="0dced-114">基于证书的身份验证</span><span class="sxs-lookup"><span data-stu-id="0dced-114">Certificate-based authentication</span></span> | <span data-ttu-id="0dced-115">具有一个针对 Azure Active Directory (AAD) 用户或服务主体配置的 X509 证书。</span><span class="sxs-lookup"><span data-stu-id="0dced-115">You have an X509 certificate that was configured for an Azure Active Directory (AAD) user or service principal.</span></span> <span data-ttu-id="0dced-116">有关详细信息，请参阅 [Azure Active Directory 中基于证书的身份验证入门]。</span><span class="sxs-lookup"><span data-stu-id="0dced-116">To learn more, see [Get started with certificate-based authentication in Azure Active Directory].</span></span> |
| <span data-ttu-id="0dced-117">客户端凭据</span><span class="sxs-lookup"><span data-stu-id="0dced-117">Client credentials</span></span> | <span data-ttu-id="0dced-118">已配置一个服务主体，该服务主体是针对此应用程序或它所属的应用程序类设置的。</span><span class="sxs-lookup"><span data-stu-id="0dced-118">You have a configured service principal that is set up for this application or a class of applications it belongs to.</span></span> <span data-ttu-id="0dced-119">有关详细信息，请参阅[使用 Azure CLI 2.0 创建服务主体]。</span><span class="sxs-lookup"><span data-stu-id="0dced-119">To learn more, see [Create a service principal with Azure CLI 2.0].</span></span> |
| <span data-ttu-id="0dced-120">托管服务标识 (MSI)</span><span class="sxs-lookup"><span data-stu-id="0dced-120">Managed Service Identity (MSI)</span></span> | <span data-ttu-id="0dced-121">应用程序在使用托管服务标识 (MSI) 配置的 Azure 资源中运行。</span><span class="sxs-lookup"><span data-stu-id="0dced-121">Your application is running on an Azure resource that has been configured with Managed Service Identity (MSI).</span></span> <span data-ttu-id="0dced-122">有关详细信息，请参阅 [Azure 资源的托管服务标识 (MSI)]。</span><span class="sxs-lookup"><span data-stu-id="0dced-122">To learn more, see [Managed Service Identity (MSI) for Azure resources].</span></span> |
| <span data-ttu-id="0dced-123">设备令牌</span><span class="sxs-lookup"><span data-stu-id="0dced-123">Device token</span></span> | <span data-ttu-id="0dced-124">应用程序预期只以交互方式使用，并且包含各种用户，这些用户有可能来自多个 AAD 租户。</span><span class="sxs-lookup"><span data-stu-id="0dced-124">Your application is meant to be used interactively __only__ and will have a variety of users, potentially from multiple AAD tenants.</span></span> <span data-ttu-id="0dced-125">用户可以访问 Web 浏览器进行登录。</span><span class="sxs-lookup"><span data-stu-id="0dced-125">Users have access to a web browser to log in.</span></span> <span data-ttu-id="0dced-126">有关详细信息，请参阅[使用设备令牌身份验证](#use-device-token-authentication)。</span><span class="sxs-lookup"><span data-stu-id="0dced-126">For more information, see [Use device token authentication](#use-device-token-authentication).</span></span>|
| <span data-ttu-id="0dced-127">用户名/密码</span><span class="sxs-lookup"><span data-stu-id="0dced-127">Username/password</span></span> | <span data-ttu-id="0dced-128">某个交互式应用程序无法使用其他任何身份验证方法。</span><span class="sxs-lookup"><span data-stu-id="0dced-128">You have an interactive application that cannot use any other authentication method.</span></span> <span data-ttu-id="0dced-129">没有为用户 AAD 登录启用多重身份验证。</span><span class="sxs-lookup"><span data-stu-id="0dced-129">Your users do not have multi-factor authentication enabled for their AAD login.</span></span> |

> [!IMPORTANT]
> <span data-ttu-id="0dced-130">如果使用的身份验证类型不是客户端凭据，则必须在 Azure Active Directory 中注册应用程序。</span><span class="sxs-lookup"><span data-stu-id="0dced-130">If you use an authentication type other than client credentials, your application must be registered in Azure Active Directory.</span></span> <span data-ttu-id="0dced-131">有关操作方法，请参阅[将应用程序与 Azure Active Directory 集成](/azure/active-directory/develop/active-directory-integrating-applications)。</span><span class="sxs-lookup"><span data-stu-id="0dced-131">To learn how, see [Integrating applications with Azure Active Directory](/azure/active-directory/develop/active-directory-integrating-applications).</span></span>

> [!NOTE]
> <span data-ttu-id="0dced-132">除非有特殊要求，否则请避免使用用户名/密码身份验证。</span><span class="sxs-lookup"><span data-stu-id="0dced-132">Unless you have special requirements, avoid username/password authentication.</span></span> <span data-ttu-id="0dced-133">在适合使用基于用户的登录的情况下，通常可用改用设备令牌身份验证。</span><span class="sxs-lookup"><span data-stu-id="0dced-133">In situations where user-based login is appropriate, device token authentication can usually be used instead.</span></span>

[Azure Active Directory 中基于证书的身份验证入门]: /azure/active-directory/active-directory-certificate-based-authentication-get-started
[Get started with certificate-based authentication in Azure Active Directory]: /azure/active-directory/active-directory-certificate-based-authentication-get-started
[使用 Azure CLI 2.0 创建服务主体]: /cli/azure/create-an-azure-service-principal-azure-cli
[Create a service principal with Azure CLI 2.0]: /cli/azure/create-an-azure-service-principal-azure-cli
[Azure 资源的托管服务标识 (MSI)]: /azure/active-directory/managed-service-identity/overview
[Managed Service Identity (MSI) for Azure resources]: /azure/active-directory/managed-service-identity/overview

<span data-ttu-id="0dced-137">可通过不同的方法使用这些身份验证类型。</span><span class="sxs-lookup"><span data-stu-id="0dced-137">These authentication types are available through different methods.</span></span> <span data-ttu-id="0dced-138">[_基于环境的身份验证_](#use-environment-based-authentication)直接从程序的环境读取凭据。</span><span class="sxs-lookup"><span data-stu-id="0dced-138">[_Environment-based authentication_](#use-environment-based-authentication) reads credentials directly from the program's environment.</span></span> <span data-ttu-id="0dced-139">[_基于文件的身份验证_](#use-file-based-authentication)加载包含服务主体凭据的文件。</span><span class="sxs-lookup"><span data-stu-id="0dced-139">[_File-based authentication_](#use-file-based-authentication) loads a file containing service principal credentials.</span></span> <span data-ttu-id="0dced-140">[_基于客户端的身份验证_](#use-an-authentication-client)使用 Go 代码中的对象，你负责在程序执行期间提供凭据。</span><span class="sxs-lookup"><span data-stu-id="0dced-140">[_Client-based authentication_](#use-an-authentication-client) uses an object in Go code and makes you responsible for providing the credentials during program execution.</span></span> <span data-ttu-id="0dced-141">最后，[_设备令牌身份验证_](#use-device-token-authentication)要求用户使用令牌通过 Web 浏览器以交互方式登录，并且不能与基于环境或基于文件的身份验证配合使用。</span><span class="sxs-lookup"><span data-stu-id="0dced-141">Finally, [_Device token authentication_](#use-device-token-authentication) requires users to log in interactively through a web browser with a token, and cannot be used with environment- or file-based authentication.</span></span>

<span data-ttu-id="0dced-142">所有身份验证函数和类型都在 `github.com/Azure/go-autorest/autorest/azure/auth` 包中提供。</span><span class="sxs-lookup"><span data-stu-id="0dced-142">All authentication functions and types are available in the `github.com/Azure/go-autorest/autorest/azure/auth` package.</span></span>

> [!NOTE]
> <span data-ttu-id="0dced-143">除非有特殊要求，否则请避免使用基于客户端的身份验证。</span><span class="sxs-lookup"><span data-stu-id="0dced-143">Unless you have special requirements, avoid client-based authentication.</span></span> <span data-ttu-id="0dced-144">这种身份验证方法很容易引起错误的做法。</span><span class="sxs-lookup"><span data-stu-id="0dced-144">This method of authentication encourages bad practices.</span></span> <span data-ttu-id="0dced-145">具体而言，使用基于客户端的身份验证容易导致凭据硬编码。</span><span class="sxs-lookup"><span data-stu-id="0dced-145">In particular, using client-based authentication makes it tempting to hard-code credentials.</span></span> <span data-ttu-id="0dced-146">此外，如果身份验证要求发生变化，可能无法使用将来的 SDK 版本编写身份验证的自定义代码。</span><span class="sxs-lookup"><span data-stu-id="0dced-146">Writing custom code for authentication may also break under future SDK releases if authentication requirements change.</span></span>

## <a name="use-environment-based-authentication"></a><span data-ttu-id="0dced-147">使用基于环境的身份验证</span><span class="sxs-lookup"><span data-stu-id="0dced-147">Use environment-based authentication</span></span>

<span data-ttu-id="0dced-148">如果在严格受控的环境（例如容器）中运行应用程序，基于环境的身份验证是自然而然的选择。</span><span class="sxs-lookup"><span data-stu-id="0dced-148">If you're running your application in a tightly controlled environment such as in a container, environment-based authentication is a natural choice.</span></span> <span data-ttu-id="0dced-149">可以在运行应用程序之前配置 shell 环境，然后，Go SDK 会在运行时读取这些环境变量，以便在 Azure 中进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="0dced-149">You configure the shell environment before running your application and the Go SDK reads these environment variables at runtime to authenticate with Azure.</span></span> 

<span data-ttu-id="0dced-150">基于环境的身份验证支持除设备令牌以外的所有身份验证方法，其评估顺序如下：客户端凭据、证书、用户名/密码和托管服务标识 (MSI)。</span><span class="sxs-lookup"><span data-stu-id="0dced-150">Environment-based authentication has support for all authentication methods except device tokens, evaluated in the following order: Client credentials, certificates, username/password, and Managed Service Identity (MSI).</span></span> <span data-ttu-id="0dced-151">如果未设置所需的环境变量，或者 SDK 遭到身份验证服务的拒绝，则会尝试下一个身份验证类型。</span><span class="sxs-lookup"><span data-stu-id="0dced-151">If a required environment variable is unset or the SDK gets a refusal from the authentication service, the next authentication type is tried.</span></span> <span data-ttu-id="0dced-152">如果 SDK 无法从环境进行身份验证，则会返回错误。</span><span class="sxs-lookup"><span data-stu-id="0dced-152">If the SDK cannot authenticate from the environment, it returns an error.</span></span>

<span data-ttu-id="0dced-153">下表详细描述了需要为基于环境的身份验证支持的每种身份验证类型设置的环境变量。</span><span class="sxs-lookup"><span data-stu-id="0dced-153">The following table details the environment variables that need to be set for each authentication type supported by environment-based authentication.</span></span>

| <span data-ttu-id="0dced-154">身份验证类型</span><span class="sxs-lookup"><span data-stu-id="0dced-154">Authentication type</span></span> | <span data-ttu-id="0dced-155">环境变量</span><span class="sxs-lookup"><span data-stu-id="0dced-155">Environment variable</span></span> | <span data-ttu-id="0dced-156">说明</span><span class="sxs-lookup"><span data-stu-id="0dced-156">Description</span></span> |
| ------------------- | -------------------- | ----------- |
| <span data-ttu-id="0dced-157">__客户端凭据__</span><span class="sxs-lookup"><span data-stu-id="0dced-157">__Client credentials__</span></span> | `AZURE_TENANT_ID` | <span data-ttu-id="0dced-158">服务主体所属的 Active Directory 租户的 ID。</span><span class="sxs-lookup"><span data-stu-id="0dced-158">The ID for the Active Directory tenant that the service principal belongs to.</span></span> |
| | `AZURE_CLIENT_ID` | <span data-ttu-id="0dced-159">服务主体的名称或 ID。</span><span class="sxs-lookup"><span data-stu-id="0dced-159">The name or ID of the service principal.</span></span> |
| | `AZURE_CLIENT_SECRET` | <span data-ttu-id="0dced-160">与服务主体关联的机密。</span><span class="sxs-lookup"><span data-stu-id="0dced-160">The secret associated with the service principal.</span></span> |
| <span data-ttu-id="0dced-161">证书</span><span class="sxs-lookup"><span data-stu-id="0dced-161">__Certificate__</span></span> | `AZURE_TENANT_ID` | <span data-ttu-id="0dced-162">证书注册到的 Active Directory 租户的 ID。</span><span class="sxs-lookup"><span data-stu-id="0dced-162">The ID for the Active Directory tenant that the certificate is registered with.</span></span> |
| | `AZURE_CLIENT_ID` | <span data-ttu-id="0dced-163">与证书关联的应用程序客户端 ID。</span><span class="sxs-lookup"><span data-stu-id="0dced-163">The application client ID associated with the certificate.</span></span> |
| | `AZURE_CERTIFICATE_PATH` | <span data-ttu-id="0dced-164">客户端证书文件的路径。</span><span class="sxs-lookup"><span data-stu-id="0dced-164">The path to the client certificate file.</span></span> |
| | `AZURE_CERTIFICATE_PASSWORD` | <span data-ttu-id="0dced-165">客户端证书的密码。</span><span class="sxs-lookup"><span data-stu-id="0dced-165">The password for the client certificate.</span></span> |
| <span data-ttu-id="0dced-166">__用户名/密码__</span><span class="sxs-lookup"><span data-stu-id="0dced-166">__Username/Password__</span></span> | `AZURE_TENANT_ID` | <span data-ttu-id="0dced-167">用户所属的 Active Directory 租户的 ID。</span><span class="sxs-lookup"><span data-stu-id="0dced-167">The ID for the Active Directory tenant that the user belongs to.</span></span> |
| | `AZURE_CLIENT_ID` | <span data-ttu-id="0dced-168">应用程序客户端 ID。</span><span class="sxs-lookup"><span data-stu-id="0dced-168">The application client ID.</span></span> |
| | `AZURE_USERNAME` | <span data-ttu-id="0dced-169">用于登录的用户名。</span><span class="sxs-lookup"><span data-stu-id="0dced-169">The username to log in with.</span></span> |
| | `AZURE_PASSWORD` | <span data-ttu-id="0dced-170">用于登录的密码。</span><span class="sxs-lookup"><span data-stu-id="0dced-170">The password to log in with.</span></span> |
| <span data-ttu-id="0dced-171">__MSI__</span><span class="sxs-lookup"><span data-stu-id="0dced-171">__MSI__</span></span> | | <span data-ttu-id="0dced-172">MSI 不要求设置任何凭据。</span><span class="sxs-lookup"><span data-stu-id="0dced-172">MSI does not require any credentials to be set.</span></span> <span data-ttu-id="0dced-173">应用程序必须在配置为使用 MSI 的 Azure 资源中运行。</span><span class="sxs-lookup"><span data-stu-id="0dced-173">The application must be running on an Azure resource configured to use MSI.</span></span> <span data-ttu-id="0dced-174">有关详细信息，请参阅 [Azure 资源的托管服务标识 (MSI)]。</span><span class="sxs-lookup"><span data-stu-id="0dced-174">For details, see [Managed Service Identity (MSI) for Azure resources].</span></span> |

<span data-ttu-id="0dced-175">如果需要连接到除默认 Azure 公有云以外的云或管理终结点，则还可以设置以下环境变量。</span><span class="sxs-lookup"><span data-stu-id="0dced-175">If you need to connect to a cloud or management endpoint other than the default Azure public cloud, you can also set the following environment variables.</span></span> <span data-ttu-id="0dced-176">设置这些环境变量的最常见原因是要使用 Azure Stack、不同地理区域中的云，或 Azure 经典部署模型。</span><span class="sxs-lookup"><span data-stu-id="0dced-176">The most common reasons to set them are if you use Azure Stack, a cloud in a different geographic region, or the Azure Classic deployment model.</span></span>

| <span data-ttu-id="0dced-177">环境变量</span><span class="sxs-lookup"><span data-stu-id="0dced-177">Environment variable</span></span> | <span data-ttu-id="0dced-178">说明</span><span class="sxs-lookup"><span data-stu-id="0dced-178">Description</span></span>  |
|----------------------|--------------|
| `AZURE_ENVIRONMENT` | <span data-ttu-id="0dced-179">要连接到的云环境的名称。</span><span class="sxs-lookup"><span data-stu-id="0dced-179">The name of the cloud environment to connect to.</span></span> |
| `AZURE_AD_RESOURCE` | <span data-ttu-id="0dced-180">连接时使用的 Active Directory 资源 ID。</span><span class="sxs-lookup"><span data-stu-id="0dced-180">The Active Directory resource ID to use when connecting.</span></span> <span data-ttu-id="0dced-181">这应该是指向管理终结点的 URI。</span><span class="sxs-lookup"><span data-stu-id="0dced-181">This should be a URI pointing to your management endpoint.</span></span> |

<span data-ttu-id="0dced-182">使用基于环境的身份验证时，请调用 [NewAuthorizerFromEnvironment](https://godoc.org/github.com/Azure/go-autorest/autorest/azure/auth#NewAuthorizerFromEnvironment) 函数来获取授权者对象。</span><span class="sxs-lookup"><span data-stu-id="0dced-182">When using environment-based authentication, call the [NewAuthorizerFromEnvironment](https://godoc.org/github.com/Azure/go-autorest/autorest/azure/auth#NewAuthorizerFromEnvironment) function to get your authorizer object.</span></span> <span data-ttu-id="0dced-183">然后，在客户端的 `Authorizer` 属性中设置此对象，以允许客户端访问 Azure。</span><span class="sxs-lookup"><span data-stu-id="0dced-183">This object is then set on the `Authorizer` property of clients to allow them access to Azure.</span></span>

```go
import "github.com/Azure/go-autorest/autorest/azure/auth"
authorizer, err := auth.NewAuthorizerFromEnvironment()
```

## <a name="use-file-based-authentication"></a><span data-ttu-id="0dced-184">使用基于文件的身份验证</span><span class="sxs-lookup"><span data-stu-id="0dced-184">Use file-based authentication</span></span>

<span data-ttu-id="0dced-185">只能配合 [Azure CLI 2.0](/cli/azure) 生成的、以本地文件格式存储的客户端凭据使用基于文件的身份验证。</span><span class="sxs-lookup"><span data-stu-id="0dced-185">File-based authentication only works with client credentials when they are stored in a local file format generated by [the Azure CLI 2.0](/cli/azure).</span></span> <span data-ttu-id="0dced-186">在创建新服务主体时，可以使用 `--sdk-auth` 参数轻松创建此文件。</span><span class="sxs-lookup"><span data-stu-id="0dced-186">You can easily create this file when creating a new service principal with the `--sdk-auth` parameter.</span></span> <span data-ttu-id="0dced-187">如果打算使用基于文件的身份验证，请确保在创建服务主体时提供此参数。</span><span class="sxs-lookup"><span data-stu-id="0dced-187">If you plan on using file-based authentication, make sure that this argument is provided when creating a service principal.</span></span> <span data-ttu-id="0dced-188">由于 CLI 在 `stdout` 中列显输出，因此会将输出重定向到某个文件。</span><span class="sxs-lookup"><span data-stu-id="0dced-188">Since the CLI prints output to `stdout`, redirect output to a file.</span></span>

```azurecli
az ad sp create-for-rbac --sdk-auth > azure.auth
```

<span data-ttu-id="0dced-189">将 `AZURE_AUTH_LOCATION` 环境变量设置为授权文件所在的位置。</span><span class="sxs-lookup"><span data-stu-id="0dced-189">Set the `AZURE_AUTH_LOCATION` environment variable to where the authorization file is located.</span></span> <span data-ttu-id="0dced-190">应用程序会读取此环境变量，并且会分析其中包含的凭据。</span><span class="sxs-lookup"><span data-stu-id="0dced-190">This environment variable is read by the application, and the credentials within it are parsed.</span></span> <span data-ttu-id="0dced-191">如果需要在运行时选择授权文件，请使用 [os.Setenv](https://golang.org/pkg/os/#Setenv) 函数来操作程序的环境。</span><span class="sxs-lookup"><span data-stu-id="0dced-191">If you need to select the authorization file at runtime, manipulate the program's environment using the [os.Setenv](https://golang.org/pkg/os/#Setenv) function.</span></span>

<span data-ttu-id="0dced-192">若要加载身份验证信息，请调用 [NewAuthorizerFromFile](https://godoc.org/github.com/Azure/go-autorest/autorest/azure/auth#NewAuthorizerFromFile) 函数。</span><span class="sxs-lookup"><span data-stu-id="0dced-192">To load the authentication information, call the [NewAuthorizerFromFile](https://godoc.org/github.com/Azure/go-autorest/autorest/azure/auth#NewAuthorizerFromFile) function.</span></span> <span data-ttu-id="0dced-193">与基于环境的授权不同，基于文件的授权需要一个资源终结点。</span><span class="sxs-lookup"><span data-stu-id="0dced-193">Unlike environment-based authorization, file-based authorization requires a resource endpoint.</span></span>

```go
import "github.com/Azure/go-autorest/autorest/azure/auth"
authorizer, err := NewAuthorizerFromFile(azure.PublicCloud.ResourceManagerEndpoint)
```

<span data-ttu-id="0dced-194">有关使用服务主体和管理其访问权限的详细信息，请参阅[使用 Azure CLI 2.0 创建服务主体]。</span><span class="sxs-lookup"><span data-stu-id="0dced-194">For more on using service principals and managing their access permissions, see [Create a service principal with Azure CLI 2.0].</span></span>

## <a name="use-device-token-authentication"></a><span data-ttu-id="0dced-195">使用设备令牌身份验证</span><span class="sxs-lookup"><span data-stu-id="0dced-195">Use device token authentication</span></span>

<span data-ttu-id="0dced-196">如果希望用户以交互方式登录，最好是通过设备令牌身份验证提供该功能。</span><span class="sxs-lookup"><span data-stu-id="0dced-196">If you want users to log in interactively, the best way to offer that capability is through device token authentication.</span></span> <span data-ttu-id="0dced-197">此身份验证流将为用户传递一个可粘贴到 Microsoft 登录站点的令牌，然后，用户可在该站点上使用 Azure Active Directory (AAD) 帐户登录。</span><span class="sxs-lookup"><span data-stu-id="0dced-197">This authentication flow passes the user a token to paste into a Microsoft login site, where they then log in with an Azure Active Directory (AAD) account.</span></span> <span data-ttu-id="0dced-198">与标准的用户名/密码身份验证不同，此身份验证方法支持已启用多重身份验证的帐户。</span><span class="sxs-lookup"><span data-stu-id="0dced-198">This authentication method supports accounts that have multi-factor authentication enabled, unlike standard username/password authentication.</span></span>

<span data-ttu-id="0dced-199">若要使用设备令牌身份验证，请使用 [NewDeviceFlowConfig](https://godoc.org/github.com/Azure/go-autorest/autorest/azure/auth#NewDeviceFlowConfig) 函数创建一个 [DeviceFlowConfig](https://godoc.org/github.com/Azure/go-autorest/autorest/azure/auth#DeviceFlowConfig) 授权者。</span><span class="sxs-lookup"><span data-stu-id="0dced-199">To use device token authentication, create a [DeviceFlowConfig](https://godoc.org/github.com/Azure/go-autorest/autorest/azure/auth#DeviceFlowConfig) authorizer with the [NewDeviceFlowConfig](https://godoc.org/github.com/Azure/go-autorest/autorest/azure/auth#NewDeviceFlowConfig) function.</span></span> <span data-ttu-id="0dced-200">针对生成的对象调用 [Authorizer](https://godoc.org/github.com/Azure/go-autorest/autorest/azure/auth#DeviceFlowConfig.Authorizer) 以启动身份验证过程。</span><span class="sxs-lookup"><span data-stu-id="0dced-200">Call [Authorizer](https://godoc.org/github.com/Azure/go-autorest/autorest/azure/auth#DeviceFlowConfig.Authorizer) on the resulting object to start the authentication process.</span></span> <span data-ttu-id="0dced-201">在整个身份验证流完成之前，设备流身份验证会一直阻止程序的执行。</span><span class="sxs-lookup"><span data-stu-id="0dced-201">Device flow authentication blocks program execution until the whole authentication flow is complete.</span></span>

```go
import "github.com/Azure/go-autorest/autorest/azure/auth"
deviceConfig := auth.NewDeviceFlowConfig(applicationID, tenantID)
authorizer, err := deviceConfig.Authorizer()
```

## <a name="use-an-authentication-client"></a><span data-ttu-id="0dced-202">使用身份验证客户端</span><span class="sxs-lookup"><span data-stu-id="0dced-202">Use an authentication client</span></span>

<span data-ttu-id="0dced-203">如果需要特定的身份验证类型，并且愿意让程序执行相应的操作以从用户加载身份验证信息，则可以使用任何符合 [auth.AuthorizerConfig](https://godoc.org/github.com/Azure/go-autorest/autorest/azure/auth#AuthorizerConfig) 接口的客户端。</span><span class="sxs-lookup"><span data-stu-id="0dced-203">If you require a specific type of authentication and are willing to have your program do the work to load authentication information from the user, you can use any client that conforms to the [auth.AuthorizerConfig](https://godoc.org/github.com/Azure/go-autorest/autorest/azure/auth#AuthorizerConfig) interface.</span></span> <span data-ttu-id="0dced-204">如果想要使用交互式程序、使用专用配置文件，或者某项要求导致无法使用其他身份验证方法，那么，请使用实现此接口的身份验证类型。</span><span class="sxs-lookup"><span data-stu-id="0dced-204">Use a type that implements this interface when you want an interactive program, use specialized configuration files, or have a requirement that prevents you from using another authentication method.</span></span>

> [!WARNING]
> <span data-ttu-id="0dced-205">切勿在应用程序中对 Azure 凭据进行硬编码。</span><span class="sxs-lookup"><span data-stu-id="0dced-205">Never hard-code Azure credentials into an application.</span></span> <span data-ttu-id="0dced-206">将机密放入应用程序二进制文件会使攻击者更容易提取这些机密，不管应用程序是否正在运行。</span><span class="sxs-lookup"><span data-stu-id="0dced-206">Putting secrets into an application binary makes it easier for an attacker to extract them, whether the application is running or not.</span></span> <span data-ttu-id="0dced-207">这会将凭据授权的所有 Azure 资源置于风险之中！</span><span class="sxs-lookup"><span data-stu-id="0dced-207">This puts all Azure resources the credentials are authorized for at risk!</span></span>

<span data-ttu-id="0dced-208">下表列出了 SDK 中符合 `AuthorizerConfig` 接口的类型。</span><span class="sxs-lookup"><span data-stu-id="0dced-208">The following table lists the types in the SDK that conform to the `AuthorizerConfig` interface.</span></span>

| <span data-ttu-id="0dced-209">身份验证类型</span><span class="sxs-lookup"><span data-stu-id="0dced-209">Authentication type</span></span> | <span data-ttu-id="0dced-210">授权者类型</span><span class="sxs-lookup"><span data-stu-id="0dced-210">Authorizer type</span></span> |
|---------------------|-----------------------|
| <span data-ttu-id="0dced-211">基于证书的身份验证</span><span class="sxs-lookup"><span data-stu-id="0dced-211">Certificate-based authentication</span></span> | <span data-ttu-id="0dced-212">[ClientCertificateConfig]</span><span class="sxs-lookup"><span data-stu-id="0dced-212">[ClientCertificateConfig]</span></span> |
| <span data-ttu-id="0dced-213">客户端凭据</span><span class="sxs-lookup"><span data-stu-id="0dced-213">Client credentials</span></span> | <span data-ttu-id="0dced-214">[ClientCredentialsConfig]</span><span class="sxs-lookup"><span data-stu-id="0dced-214">[ClientCredentialsConfig]</span></span> |
| <span data-ttu-id="0dced-215">托管服务标识 (MSI)</span><span class="sxs-lookup"><span data-stu-id="0dced-215">Managed Service Identity (MSI)</span></span> | <span data-ttu-id="0dced-216">[MSIConfig]</span><span class="sxs-lookup"><span data-stu-id="0dced-216">[MSIConfig]</span></span> |
| <span data-ttu-id="0dced-217">用户名/密码</span><span class="sxs-lookup"><span data-stu-id="0dced-217">Username/password</span></span> | <span data-ttu-id="0dced-218">[UsernamePasswordConfig]</span><span class="sxs-lookup"><span data-stu-id="0dced-218">[UsernamePasswordConfig]</span></span> |

[ClientCertificateConfig]: https://godoc.org/github.com/Azure/go-autorest/autorest/azure/auth#ClientCertificateConfig
[ClientCredentialsConfig]: https://godoc.org/github.com/Azure/go-autorest/autorest/azure/auth#ClientCredentialsConfig
[MSIConfig]: https://godoc.org/github.com/Azure/go-autorest/autorest/azure/auth#MSIConfig
[DeviceFlowConfig]: https://godoc.org/github.com/Azure/go-autorest/autorest/azure/auth#DeviceFlowConfig
[UsernamePasswordConfig]: https://godoc.org/github.com/Azure/go-autorest/autorest/azure/auth#UsernamePasswordConfig

<span data-ttu-id="0dced-223">使用关联的 `New` 函数创建一个验证器，然后针对生成的对象调用 `Authorize` 以执行身份验证。</span><span class="sxs-lookup"><span data-stu-id="0dced-223">Create an authenticator with its associated `New` function, and then call `Authorize` on the resulting object to perform authentication.</span></span> <span data-ttu-id="0dced-224">例如，若要使用基于证书的身份验证，请执行以下调用：</span><span class="sxs-lookup"><span data-stu-id="0dced-224">For example, to use certificate-based authentication:</span></span>

```go
import "github.com/Azure/go-autorest/autorest/azure/auth"
certificateAuthorizer := auth.NewClientCertificateConfig(certificatePath, certificatePassword, clientID, tenantID)
authorizerToken, err := certificateAuthorizer.Authorize()
```
