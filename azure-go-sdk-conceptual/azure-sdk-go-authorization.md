---
title: 使用 Azure SDK for Go 进行身份验证
description: 了解 Azure SDK for Go 中提供的身份验证方法及其用法。
services: azure
author: sptramer
ms.author: sttramer
manager: carmonm
ms.date: 09/05/2018
ms.topic: conceptual
ms.technology: azure-sdk-go
ms.devlang: go
ms.component: authentication
ms.openlocfilehash: c2c3dccfa8da5cfe57fee0b90139002068982560
ms.sourcegitcommit: 887b15afcdeaf926a5f3d21b64e4045167fd062c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/21/2018
ms.locfileid: "49481976"
---
# <a name="authentication-methods-in-the-azure-sdk-for-go"></a><span data-ttu-id="57a8d-103">Azure SDK for Go 中的身份验证方法</span><span class="sxs-lookup"><span data-stu-id="57a8d-103">Authentication methods in the Azure SDK for Go</span></span>

<span data-ttu-id="57a8d-104">Azure SDK for Go 提供多种方式来使用 Azure 进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="57a8d-104">The Azure SDK for Go offers multiple ways to authenticate with Azure.</span></span> <span data-ttu-id="57a8d-105">可通过不同的身份验证_方法_调用这些身份验证_类型_。</span><span class="sxs-lookup"><span data-stu-id="57a8d-105">These authentication _types_ are invoked through different authentication _methods_.</span></span> <span data-ttu-id="57a8d-106">本文介绍可用的类型、方法以及如何选择最适合应用程序的方法。</span><span class="sxs-lookup"><span data-stu-id="57a8d-106">This article covers the available types, methods, and how to choose which are best for your application.</span></span>

## <a name="available-authentication-types-and-methods"></a><span data-ttu-id="57a8d-107">可用的身份验证类型和方法</span><span class="sxs-lookup"><span data-stu-id="57a8d-107">Available authentication types and methods</span></span>

<span data-ttu-id="57a8d-108">Azure SDK for Go 提供使用不同凭据集的多种身份验证类型。</span><span class="sxs-lookup"><span data-stu-id="57a8d-108">The Azure SDK for Go offers several different types of authentication, using different credentials sets.</span></span> <span data-ttu-id="57a8d-109">可通过不同的身份验证方法使用每种身份验证类型，该 SDK 也正是通过这些方法将这些凭据用作输入。</span><span class="sxs-lookup"><span data-stu-id="57a8d-109">Each authentication type is available through different authentication methods, which are how the SDK takes these credentials as input.</span></span> <span data-ttu-id="57a8d-110">下表描述了可用的身份验证类型，以及在哪些场合下建议让应用程序使用这些类型。</span><span class="sxs-lookup"><span data-stu-id="57a8d-110">The following table describes the available types of authentication and situations in which they're recommended for use by your application.</span></span>

| <span data-ttu-id="57a8d-111">身份验证类型</span><span class="sxs-lookup"><span data-stu-id="57a8d-111">Authentication type</span></span> | <span data-ttu-id="57a8d-112">建议的使用场合...</span><span class="sxs-lookup"><span data-stu-id="57a8d-112">Recommended when...</span></span> |
|---------------------|---------------------|
| <span data-ttu-id="57a8d-113">基于证书的身份验证</span><span class="sxs-lookup"><span data-stu-id="57a8d-113">Certificate-based authentication</span></span> | <span data-ttu-id="57a8d-114">具有一个针对 Azure Active Directory (AAD) 用户或服务主体配置的 X509 证书。</span><span class="sxs-lookup"><span data-stu-id="57a8d-114">You have an X509 certificate that was configured for an Azure Active Directory (AAD) user or service principal.</span></span> <span data-ttu-id="57a8d-115">有关详细信息，请参阅 [Azure Active Directory 中基于证书的身份验证入门]。</span><span class="sxs-lookup"><span data-stu-id="57a8d-115">To learn more, see [Get started with certificate-based authentication in Azure Active Directory].</span></span> |
| <span data-ttu-id="57a8d-116">客户端凭据</span><span class="sxs-lookup"><span data-stu-id="57a8d-116">Client credentials</span></span> | <span data-ttu-id="57a8d-117">已配置一个服务主体，该服务主体是针对此应用程序或它所属的应用程序类设置的。</span><span class="sxs-lookup"><span data-stu-id="57a8d-117">You have a configured service principal that is set up for this application or a class of applications it belongs to.</span></span> <span data-ttu-id="57a8d-118">有关详细信息，请参阅[使用 Azure CLI 创建服务主体]。</span><span class="sxs-lookup"><span data-stu-id="57a8d-118">To learn more, see [Create a service principal with Azure CLI].</span></span> |
| <span data-ttu-id="57a8d-119">Azure 资源的托管标识</span><span class="sxs-lookup"><span data-stu-id="57a8d-119">Managed identities for Azure resources</span></span> | <span data-ttu-id="57a8d-120">应用程序在已使用托管标识配置的 Azure 资源上运行。</span><span class="sxs-lookup"><span data-stu-id="57a8d-120">Your application is running on an Azure resource that has been configured with a managed identity.</span></span> <span data-ttu-id="57a8d-121">若要了解详细信息，请参阅 [Azure 资源的托管标识]。</span><span class="sxs-lookup"><span data-stu-id="57a8d-121">To learn more, see [Managed identities for Azure resources].</span></span> |
| <span data-ttu-id="57a8d-122">设备令牌</span><span class="sxs-lookup"><span data-stu-id="57a8d-122">Device token</span></span> | <span data-ttu-id="57a8d-123">你的应用程序应__仅__以交互方式使用。</span><span class="sxs-lookup"><span data-stu-id="57a8d-123">Your application is meant to be used interactively __only__.</span></span> <span data-ttu-id="57a8d-124">用户可能已启用多重身份验证。</span><span class="sxs-lookup"><span data-stu-id="57a8d-124">Users may have multi-factor authentication enabled.</span></span> <span data-ttu-id="57a8d-125">用户可以访问用于登录的 Web 浏览器。</span><span class="sxs-lookup"><span data-stu-id="57a8d-125">Users have access to a web browser to sign in.</span></span> <span data-ttu-id="57a8d-126">有关详细信息，请参阅[使用设备令牌身份验证](#use-device-token-authentication)。</span><span class="sxs-lookup"><span data-stu-id="57a8d-126">For more information, see [Use device token authentication](#use-device-token-authentication).</span></span>|
| <span data-ttu-id="57a8d-127">用户名/密码</span><span class="sxs-lookup"><span data-stu-id="57a8d-127">Username/password</span></span> | <span data-ttu-id="57a8d-128">某个交互式应用程序无法使用其他任何身份验证方法。</span><span class="sxs-lookup"><span data-stu-id="57a8d-128">You have an interactive application that can't use any other authentication method.</span></span> <span data-ttu-id="57a8d-129">用户没有为其 AAD 登录启用多重身份验证。</span><span class="sxs-lookup"><span data-stu-id="57a8d-129">Your users don't have multi-factor authentication enabled for their AAD sign-in.</span></span> |

> [!IMPORTANT]
> <span data-ttu-id="57a8d-130">如果使用的身份验证类型不是客户端凭据，则必须在 Azure Active Directory 中注册应用程序。</span><span class="sxs-lookup"><span data-stu-id="57a8d-130">If you use an authentication type other than client credentials, your application must be registered in Azure Active Directory.</span></span> <span data-ttu-id="57a8d-131">有关操作方法，请参阅[将应用程序与 Azure Active Directory 集成](/azure/active-directory/develop/active-directory-integrating-applications)。</span><span class="sxs-lookup"><span data-stu-id="57a8d-131">To learn how, see [Integrating applications with Azure Active Directory](/azure/active-directory/develop/active-directory-integrating-applications).</span></span>
>
> [!NOTE]
> <span data-ttu-id="57a8d-132">除非有特殊要求，否则请避免使用用户名/密码身份验证。</span><span class="sxs-lookup"><span data-stu-id="57a8d-132">Unless you have special requirements, avoid username/password authentication.</span></span> <span data-ttu-id="57a8d-133">在适合使用基于用户的登录的情况下，通常可用改用设备令牌身份验证。</span><span class="sxs-lookup"><span data-stu-id="57a8d-133">In situations where user-based sign in is appropriate, device token authentication can usually be used instead.</span></span>

[Azure Active Directory 中基于证书的身份验证入门]: /azure/active-directory/active-directory-certificate-based-authentication-get-started
[Get started with certificate-based authentication in Azure Active Directory]: /azure/active-directory/active-directory-certificate-based-authentication-get-started
[使用 Azure CLI 创建服务主体]: /cli/azure/create-an-azure-service-principal-azure-cli
[Create a service principal with Azure CLI]: /cli/azure/create-an-azure-service-principal-azure-cli
[Azure 资源的托管标识]: /azure/active-directory/managed-identities-azure-resources/overview
[Managed identities for Azure resources]: /azure/active-directory/managed-identities-azure-resources/overview

<span data-ttu-id="57a8d-137">可通过不同的方法使用这些身份验证类型。</span><span class="sxs-lookup"><span data-stu-id="57a8d-137">These authentication types are available through different methods.</span></span>

* <span data-ttu-id="57a8d-138">[_基于环境的身份验证_](#use-environment-based-authentication)直接从程序的环境读取凭据。</span><span class="sxs-lookup"><span data-stu-id="57a8d-138">[_Environment-based authentication_](#use-environment-based-authentication) reads credentials directly from the program's environment.</span></span>
* <span data-ttu-id="57a8d-139">[_基于文件的身份验证_](#use-file-based-authentication)加载包含服务主体凭据的文件。</span><span class="sxs-lookup"><span data-stu-id="57a8d-139">[_File-based authentication_](#use-file-based-authentication) loads a file containing service principal credentials.</span></span>
* <span data-ttu-id="57a8d-140">[_基于客户端的身份验证_](#use-an-authentication-client)使用代码中的对象，使你负责在程序执行期间提供凭据。</span><span class="sxs-lookup"><span data-stu-id="57a8d-140">[_Client-based authentication_](#use-an-authentication-client) uses an object in code and makes you responsible for providing the credentials during program execution.</span></span>
* <span data-ttu-id="57a8d-141">[_设备令牌身份验证_](#use-device-token-authentication)要求用户以交互方式通过 Web 浏览器使用令牌登录。</span><span class="sxs-lookup"><span data-stu-id="57a8d-141">[_Device token authentication_](#use-device-token-authentication) requires users to sign in interactively through a web browser with a token.</span></span>

<span data-ttu-id="57a8d-142">所有身份验证函数和类型都在 `github.com/Azure/go-autorest/autorest/azure/auth` 包中提供。</span><span class="sxs-lookup"><span data-stu-id="57a8d-142">All authentication functions and types are available in the `github.com/Azure/go-autorest/autorest/azure/auth` package.</span></span>

> [!NOTE]
> <span data-ttu-id="57a8d-143">除非有特殊要求，否则请避免使用基于客户端的身份验证。</span><span class="sxs-lookup"><span data-stu-id="57a8d-143">Unless you have special requirements, avoid client-based authentication.</span></span> <span data-ttu-id="57a8d-144">这种身份验证方法很容易引起错误的做法。</span><span class="sxs-lookup"><span data-stu-id="57a8d-144">This method of authentication encourages bad practices.</span></span> <span data-ttu-id="57a8d-145">具体而言，使用基于客户端的身份验证容易导致凭据硬编码。</span><span class="sxs-lookup"><span data-stu-id="57a8d-145">In particular, using client-based authentication makes it tempting to hard-code credentials.</span></span> <span data-ttu-id="57a8d-146">此外，如果身份验证要求发生变化，可能无法使用将来的 SDK 版本编写身份验证的自定义代码。</span><span class="sxs-lookup"><span data-stu-id="57a8d-146">Writing custom code for authentication may also break under future SDK releases if authentication requirements change.</span></span>

## <a name="use-environment-based-authentication"></a><span data-ttu-id="57a8d-147">使用基于环境的身份验证</span><span class="sxs-lookup"><span data-stu-id="57a8d-147">Use environment-based authentication</span></span>

<span data-ttu-id="57a8d-148">如果在受控设置中运行应用程序，则基于环境的身份验证是一种自然选择。</span><span class="sxs-lookup"><span data-stu-id="57a8d-148">If you're running your application in a controlled setting, environment-based authentication is a natural choice.</span></span> <span data-ttu-id="57a8d-149">使用此身份验证方法，可以在运行应用程序之前配置 shell 环境。</span><span class="sxs-lookup"><span data-stu-id="57a8d-149">With this authentication method, you configure the shell environment before running your application.</span></span> <span data-ttu-id="57a8d-150">在运行时，Go SDK 读取这些环境变量，以使用 Azure 进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="57a8d-150">At runtime, the Go SDK reads these environment variables to authenticate with Azure.</span></span>

<span data-ttu-id="57a8d-151">基于环境的身份验证支持除设备令牌以外的所有身份验证方法，其评估顺序如下：</span><span class="sxs-lookup"><span data-stu-id="57a8d-151">Environment-based authentication has support for all authentication methods except device tokens, evaluated in the following order:</span></span>

* <span data-ttu-id="57a8d-152">客户端凭据</span><span class="sxs-lookup"><span data-stu-id="57a8d-152">Client credentials</span></span>
* <span data-ttu-id="57a8d-153">X509 证书</span><span class="sxs-lookup"><span data-stu-id="57a8d-153">X509 certificates</span></span>
* <span data-ttu-id="57a8d-154">用户名/密码</span><span class="sxs-lookup"><span data-stu-id="57a8d-154">Username/password</span></span>
* <span data-ttu-id="57a8d-155">Azure 资源的托管标识</span><span class="sxs-lookup"><span data-stu-id="57a8d-155">Managed identities for Azure resources</span></span>

<span data-ttu-id="57a8d-156">如果身份验证类型具有未设置值或被拒绝，则该 SDK 会自动尝试下一种身份验证类型。</span><span class="sxs-lookup"><span data-stu-id="57a8d-156">If an authentication type has unset values or is refused, the SDK automatically tries the next authentication type.</span></span> <span data-ttu-id="57a8d-157">当没有更多类型可供尝试时，SDK 会返回错误。</span><span class="sxs-lookup"><span data-stu-id="57a8d-157">When no more types are available to try, the SDK returns an error.</span></span>

<span data-ttu-id="57a8d-158">下表详细描述了需要为基于环境的身份验证支持的每种身份验证类型设置的环境变量。</span><span class="sxs-lookup"><span data-stu-id="57a8d-158">The following table details the environment variables that need to be set for each authentication type supported by environment-based authentication.</span></span>


|  <span data-ttu-id="57a8d-159">身份验证类型</span><span class="sxs-lookup"><span data-stu-id="57a8d-159">Authentication type</span></span>   |     <span data-ttu-id="57a8d-160">环境变量</span><span class="sxs-lookup"><span data-stu-id="57a8d-160">Environment variable</span></span>     |                                                                                                     <span data-ttu-id="57a8d-161">Description</span><span class="sxs-lookup"><span data-stu-id="57a8d-161">Description</span></span>                                                                                                      |
|------------------------|------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="57a8d-162">**客户端凭据**</span><span class="sxs-lookup"><span data-stu-id="57a8d-162">**Client credentials**</span></span> |      `AZURE_TENANT_ID`       |                                                                    <span data-ttu-id="57a8d-163">服务主体所属的 Active Directory 租户的 ID。</span><span class="sxs-lookup"><span data-stu-id="57a8d-163">The ID for the Active Directory tenant that the service principal belongs to.</span></span>                                                                     |
|                        |      `AZURE_CLIENT_ID`       |                                                                                       <span data-ttu-id="57a8d-164">服务主体的名称或 ID。</span><span class="sxs-lookup"><span data-stu-id="57a8d-164">The name or ID of the service principal.</span></span>                                                                                       |
|                        |    `AZURE_CLIENT_SECRET`     |                                                                                  <span data-ttu-id="57a8d-165">与服务主体关联的机密。</span><span class="sxs-lookup"><span data-stu-id="57a8d-165">The secret associated with the service principal.</span></span>                                                                                   |
|    <span data-ttu-id="57a8d-166">**证书**</span><span class="sxs-lookup"><span data-stu-id="57a8d-166">**Certificate**</span></span>     |      `AZURE_TENANT_ID`       |                                                                   <span data-ttu-id="57a8d-167">证书注册到的 Active Directory 租户的 ID。</span><span class="sxs-lookup"><span data-stu-id="57a8d-167">The ID for the Active Directory tenant that the certificate is registered with.</span></span>                                                                    |
|                        |      `AZURE_CLIENT_ID`       |                                                                              <span data-ttu-id="57a8d-168">与证书关联的应用程序客户端 ID。</span><span class="sxs-lookup"><span data-stu-id="57a8d-168">The application client ID associated with the certificate.</span></span>                                                                              |
|                        |   `AZURE_CERTIFICATE_PATH`   |                                                                                       <span data-ttu-id="57a8d-169">客户端证书文件的路径。</span><span class="sxs-lookup"><span data-stu-id="57a8d-169">The path to the client certificate file.</span></span>                                                                                       |
|                        | `AZURE_CERTIFICATE_PASSWORD` |                                                                                       <span data-ttu-id="57a8d-170">客户端证书的密码。</span><span class="sxs-lookup"><span data-stu-id="57a8d-170">The password for the client certificate.</span></span>                                                                                       |
| <span data-ttu-id="57a8d-171">**用户名/密码**</span><span class="sxs-lookup"><span data-stu-id="57a8d-171">**Username/Password**</span></span>  |      `AZURE_TENANT_ID`       |                                                                           <span data-ttu-id="57a8d-172">用户所属的 Active Directory 租户的 ID。</span><span class="sxs-lookup"><span data-stu-id="57a8d-172">The ID for the Active Directory tenant that the user belongs to.</span></span>                                                                           |
|                        |      `AZURE_CLIENT_ID`       |                                                                                              <span data-ttu-id="57a8d-173">应用程序客户端 ID。</span><span class="sxs-lookup"><span data-stu-id="57a8d-173">The application client ID.</span></span>                                                                                              |
|                        |       `AZURE_USERNAME`       |                                                                                            <span data-ttu-id="57a8d-174">用于登录的用户名。</span><span class="sxs-lookup"><span data-stu-id="57a8d-174">The username to sign in with.</span></span>                                                                                             |
|                        |       `AZURE_PASSWORD`       |                                                                                            <span data-ttu-id="57a8d-175">用于登录的密码。</span><span class="sxs-lookup"><span data-stu-id="57a8d-175">The password to sign in with.</span></span>                                                                                             |
|  <span data-ttu-id="57a8d-176">**托管的标识**</span><span class="sxs-lookup"><span data-stu-id="57a8d-176">**Managed identity**</span></span>  |                              | <span data-ttu-id="57a8d-177">托管标识身份验证不需要凭据。</span><span class="sxs-lookup"><span data-stu-id="57a8d-177">No credentials are needed for managed identity authentication.</span></span> <span data-ttu-id="57a8d-178">应用程序必须在配置为使用托管标识的资源上运行。</span><span class="sxs-lookup"><span data-stu-id="57a8d-178">The application must be running on an Azure resource configured to use managed identities.</span></span> <span data-ttu-id="57a8d-179">有关详细信息，请参阅 [Azure 资源的托管标识]。</span><span class="sxs-lookup"><span data-stu-id="57a8d-179">For details, see [Managed identities for Azure resources].</span></span> |

<span data-ttu-id="57a8d-180">若要连接到默认 Azure 公有云以外的云或管理终结点，请设置以下环境变量。</span><span class="sxs-lookup"><span data-stu-id="57a8d-180">To connect to a cloud or management endpoint other than the default Azure public cloud, set the following environment variables.</span></span> <span data-ttu-id="57a8d-181">最常见的原因是要使用 Azure Stack、不同地理区域中的云，或经典部署模型。</span><span class="sxs-lookup"><span data-stu-id="57a8d-181">The most common reasons are if you use Azure Stack, a cloud in a different geographic region, or the classic deployment model.</span></span>

| <span data-ttu-id="57a8d-182">环境变量</span><span class="sxs-lookup"><span data-stu-id="57a8d-182">Environment variable</span></span> | <span data-ttu-id="57a8d-183">Description</span><span class="sxs-lookup"><span data-stu-id="57a8d-183">Description</span></span>  |
|----------------------|--------------|
| `AZURE_ENVIRONMENT` | <span data-ttu-id="57a8d-184">要连接到的云环境的名称。</span><span class="sxs-lookup"><span data-stu-id="57a8d-184">The name of the cloud environment to connect to.</span></span> |
| `AZURE_AD_RESOURCE` | <span data-ttu-id="57a8d-185">连接时要使用的 Active Directory 资源 ID，作为管理终结点的 URI。</span><span class="sxs-lookup"><span data-stu-id="57a8d-185">The Active Directory resource ID to use when connecting, as a URI to your management endpoint.</span></span> |

<span data-ttu-id="57a8d-186">使用基于环境的身份验证时，请调用 [NewAuthorizerFromEnvironment](https://godoc.org/github.com/Azure/go-autorest/autorest/azure/auth#NewAuthorizerFromEnvironment) 函数来获取授权者对象。</span><span class="sxs-lookup"><span data-stu-id="57a8d-186">When using environment-based authentication, call the [NewAuthorizerFromEnvironment](https://godoc.org/github.com/Azure/go-autorest/autorest/azure/auth#NewAuthorizerFromEnvironment) function to get your authorizer object.</span></span> <span data-ttu-id="57a8d-187">然后，在客户端的 `Authorizer` 属性中设置此对象，以允许客户端访问 Azure。</span><span class="sxs-lookup"><span data-stu-id="57a8d-187">This object is then set on the `Authorizer` property of clients to allow them access to Azure.</span></span>

```go
import "github.com/Azure/go-autorest/autorest/azure/auth"
authorizer, err := auth.NewAuthorizerFromEnvironment()
```

### <a name="authentication-on-azure-stack"></a><span data-ttu-id="57a8d-188">Azure Stack 上的身份验证</span><span class="sxs-lookup"><span data-stu-id="57a8d-188">Authentication on Azure Stack</span></span>

<span data-ttu-id="57a8d-189">若要在 Azure Stack 上进行身份验证，需要设置以下变量：</span><span class="sxs-lookup"><span data-stu-id="57a8d-189">To authenticate on Azure Stack, you need to set the following variables:</span></span>

| <span data-ttu-id="57a8d-190">环境变量</span><span class="sxs-lookup"><span data-stu-id="57a8d-190">Environment variable</span></span> | <span data-ttu-id="57a8d-191">Description</span><span class="sxs-lookup"><span data-stu-id="57a8d-191">Description</span></span>  |
|----------------------|--------------|
| `AZURE_AD_ENDPOINT` | <span data-ttu-id="57a8d-192">Active Directory 终结点。</span><span class="sxs-lookup"><span data-stu-id="57a8d-192">The Active Directory endpoint.</span></span> |
| `AZURE_AD_RESOURCE` | <span data-ttu-id="57a8d-193">Active Directory 资源 ID。</span><span class="sxs-lookup"><span data-stu-id="57a8d-193">The Active Directory resource ID.</span></span> |

<span data-ttu-id="57a8d-194">可以从 Azure Stack 元数据信息中检索这些变量。</span><span class="sxs-lookup"><span data-stu-id="57a8d-194">These variables can be retrieved from Azure Stack metadata information.</span></span> <span data-ttu-id="57a8d-195">若要检索元数据，请在 Azure Stack 环境中打开 Web 浏览器并使用 url：`(ResourceManagerURL)/metadata/endpoints?api-version=1.0`</span><span class="sxs-lookup"><span data-stu-id="57a8d-195">To retrieve the metadata, open a web browser in your Azure Stack environment and use the url: `(ResourceManagerURL)/metadata/endpoints?api-version=1.0`</span></span>

<span data-ttu-id="57a8d-196">`ResourceManagerURL` 因 Azure Stack 部署的区域名称、计算机名称和外部完全限定的域名 (FQDN) 而异：</span><span class="sxs-lookup"><span data-stu-id="57a8d-196">The `ResourceManagerURL` varies based on the region name, machine name, and external fully qualified domain name (FQDN) of your Azure Stack deployment:</span></span>

| <span data-ttu-id="57a8d-197">环境</span><span class="sxs-lookup"><span data-stu-id="57a8d-197">Environment</span></span> | <span data-ttu-id="57a8d-198">ResourceManagerURL</span><span class="sxs-lookup"><span data-stu-id="57a8d-198">ResourceManagerURL</span></span> |
|----------------------|--------------|
| <span data-ttu-id="57a8d-199">开发工具包</span><span class="sxs-lookup"><span data-stu-id="57a8d-199">Development Kit</span></span> | `https://management.local.azurestack.external/` |
| <span data-ttu-id="57a8d-200">集成系统</span><span class="sxs-lookup"><span data-stu-id="57a8d-200">Integrated Systems</span></span> | `https://management.(region).ext-(machine-name).(FQDN)` |

<span data-ttu-id="57a8d-201">有关如何在 Azure Stack 上使用 Azure SDK for Go 的详细信息，请参阅[在 Azure Stack 中将 API 版本配置文件与 Go 配合使用](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-version-profiles-go)</span><span class="sxs-lookup"><span data-stu-id="57a8d-201">For more information on how to use the Azure SDK for Go on Azure Stack, see [Use API version profiles with Go in Azure Stack](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-version-profiles-go)</span></span>

## <a name="use-file-based-authentication"></a><span data-ttu-id="57a8d-202">使用基于文件的身份验证</span><span class="sxs-lookup"><span data-stu-id="57a8d-202">Use file-based authentication</span></span>

<span data-ttu-id="57a8d-203">基于文件的身份验证使用由 [Azure CLI](/cli/azure) 生成的文件格式。</span><span class="sxs-lookup"><span data-stu-id="57a8d-203">File-based authentication uses a file format generated by [the Azure CLI](/cli/azure).</span></span> <span data-ttu-id="57a8d-204">在创建新服务主体时，可以使用 `--sdk-auth` 参数轻松创建此文件。</span><span class="sxs-lookup"><span data-stu-id="57a8d-204">You can easily create this file when creating a new service principal with the `--sdk-auth` parameter.</span></span> <span data-ttu-id="57a8d-205">如果打算使用基于文件的身份验证，请确保在创建服务主体时提供此参数。</span><span class="sxs-lookup"><span data-stu-id="57a8d-205">If you plan on using file-based authentication, make sure that this argument is provided when creating a service principal.</span></span> <span data-ttu-id="57a8d-206">由于 CLI 在 `stdout` 中列显输出，因此会将输出重定向到某个文件。</span><span class="sxs-lookup"><span data-stu-id="57a8d-206">Since the CLI prints output to `stdout`, redirect output to a file.</span></span>

```azurecli
az ad sp create-for-rbac --sdk-auth > azure.auth
```

<span data-ttu-id="57a8d-207">将 `AZURE_AUTH_LOCATION` 环境变量设置为授权文件所在的位置。</span><span class="sxs-lookup"><span data-stu-id="57a8d-207">Set the `AZURE_AUTH_LOCATION` environment variable to where the authorization file is located.</span></span> <span data-ttu-id="57a8d-208">应用程序会读取此环境变量，并且会分析其中包含的凭据。</span><span class="sxs-lookup"><span data-stu-id="57a8d-208">This environment variable is read by the application, and the credentials within it are parsed.</span></span> <span data-ttu-id="57a8d-209">如果需要在运行时选择授权文件，请使用 [os.Setenv](https://golang.org/pkg/os/#Setenv) 函数来操作程序的环境。</span><span class="sxs-lookup"><span data-stu-id="57a8d-209">If you need to select the authorization file at runtime, manipulate the program's environment using the [os.Setenv](https://golang.org/pkg/os/#Setenv) function.</span></span>

<span data-ttu-id="57a8d-210">若要加载身份验证信息，请调用 [NewAuthorizerFromFile](https://godoc.org/github.com/Azure/go-autorest/autorest/azure/auth#NewAuthorizerFromFile) 函数。</span><span class="sxs-lookup"><span data-stu-id="57a8d-210">To load the authentication information, call the [NewAuthorizerFromFile](https://godoc.org/github.com/Azure/go-autorest/autorest/azure/auth#NewAuthorizerFromFile) function.</span></span> <span data-ttu-id="57a8d-211">与基于环境的授权不同，基于文件的授权需要一个资源终结点。</span><span class="sxs-lookup"><span data-stu-id="57a8d-211">Unlike environment-based authorization, file-based authorization requires a resource endpoint.</span></span>

```go
import "github.com/Azure/go-autorest/autorest/azure/auth"
authorizer, err := NewAuthorizerFromFile(azure.PublicCloud.ResourceManagerEndpoint)
```

<span data-ttu-id="57a8d-212">有关使用服务主体和管理其访问权限的详细信息，请参阅[使用 Azure CLI 创建服务主体]。</span><span class="sxs-lookup"><span data-stu-id="57a8d-212">For more on using service principals and managing their access permissions, see [Create a service principal with Azure CLI].</span></span>

## <a name="use-device-token-authentication"></a><span data-ttu-id="57a8d-213">使用设备令牌身份验证</span><span class="sxs-lookup"><span data-stu-id="57a8d-213">Use device token authentication</span></span>

<span data-ttu-id="57a8d-214">如果希望用户以交互方式登录，最好是通过设备令牌身份验证提供该功能。</span><span class="sxs-lookup"><span data-stu-id="57a8d-214">If you want users to sign in interactively, the best way is through device token authentication.</span></span> <span data-ttu-id="57a8d-215">此身份验证流将为用户传递一个可粘贴到 Microsoft 登录站点的令牌，然后，用户可在该站点上使用 Azure Active Directory (AAD) 帐户进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="57a8d-215">This authentication flow passes the user a token to paste into a Microsoft sign-in site, where they then authenticate with an Azure Active Directory (AAD) account.</span></span> <span data-ttu-id="57a8d-216">与标准的用户名/密码身份验证不同，此身份验证方法支持已启用多重身份验证的帐户。</span><span class="sxs-lookup"><span data-stu-id="57a8d-216">This authentication method supports accounts that have multi-factor authentication enabled, unlike standard username/password authentication.</span></span>

<span data-ttu-id="57a8d-217">若要使用设备令牌身份验证，请使用 [NewDeviceFlowConfig](https://godoc.org/github.com/Azure/go-autorest/autorest/azure/auth#NewDeviceFlowConfig) 函数创建一个 [DeviceFlowConfig](https://godoc.org/github.com/Azure/go-autorest/autorest/azure/auth#DeviceFlowConfig) 授权者。</span><span class="sxs-lookup"><span data-stu-id="57a8d-217">To use device token authentication, create a [DeviceFlowConfig](https://godoc.org/github.com/Azure/go-autorest/autorest/azure/auth#DeviceFlowConfig) authorizer with the [NewDeviceFlowConfig](https://godoc.org/github.com/Azure/go-autorest/autorest/azure/auth#NewDeviceFlowConfig) function.</span></span> <span data-ttu-id="57a8d-218">针对生成的对象调用 [Authorizer](https://godoc.org/github.com/Azure/go-autorest/autorest/azure/auth#DeviceFlowConfig.Authorizer) 以启动身份验证过程。</span><span class="sxs-lookup"><span data-stu-id="57a8d-218">Call [Authorizer](https://godoc.org/github.com/Azure/go-autorest/autorest/azure/auth#DeviceFlowConfig.Authorizer) on the resulting object to start the authentication process.</span></span> <span data-ttu-id="57a8d-219">在整个身份验证流完成之前，设备流身份验证会一直阻止程序的执行。</span><span class="sxs-lookup"><span data-stu-id="57a8d-219">Device flow authentication blocks program execution until the whole authentication flow is complete.</span></span>

```go
import "github.com/Azure/go-autorest/autorest/azure/auth"
deviceConfig := auth.NewDeviceFlowConfig(applicationID, tenantID)
authorizer, err := deviceConfig.Authorizer()
```

## <a name="use-an-authentication-client"></a><span data-ttu-id="57a8d-220">使用身份验证客户端</span><span class="sxs-lookup"><span data-stu-id="57a8d-220">Use an authentication client</span></span>

<span data-ttu-id="57a8d-221">如果需要特定的身份验证类型，并且愿意让程序执行相应的操作以从用户加载身份验证信息，则可以使用任何符合 [auth.AuthorizerConfig](https://godoc.org/github.com/Azure/go-autorest/autorest/azure/auth#AuthorizerConfig) 接口的客户端。</span><span class="sxs-lookup"><span data-stu-id="57a8d-221">If you require a specific type of authentication and are willing to have your program do the work to load authentication information from the user, you can use any client that conforms to the [auth.AuthorizerConfig](https://godoc.org/github.com/Azure/go-autorest/autorest/azure/auth#AuthorizerConfig) interface.</span></span> <span data-ttu-id="57a8d-222">在以下情况下使用实现此接口的类型：</span><span class="sxs-lookup"><span data-stu-id="57a8d-222">Use a type that implements this interface when you:</span></span>

* <span data-ttu-id="57a8d-223">编写交互式程序</span><span class="sxs-lookup"><span data-stu-id="57a8d-223">Write an interactive program</span></span>
* <span data-ttu-id="57a8d-224">使用专用的配置文件</span><span class="sxs-lookup"><span data-stu-id="57a8d-224">Use specialized configuration files</span></span>
* <span data-ttu-id="57a8d-225">有阻止使用内置身份验证方法的要求</span><span class="sxs-lookup"><span data-stu-id="57a8d-225">Have a requirement that prevents using a built-in authentication method</span></span>

> [!WARNING]
> <span data-ttu-id="57a8d-226">切勿在应用程序中对 Azure 凭据进行硬编码。</span><span class="sxs-lookup"><span data-stu-id="57a8d-226">Never hard-code Azure credentials into an application.</span></span> <span data-ttu-id="57a8d-227">将机密放入应用程序二进制文件会使攻击者更容易提取这些机密，不管应用程序是否正在运行。</span><span class="sxs-lookup"><span data-stu-id="57a8d-227">Putting secrets into an application binary makes it easier for an attacker to extract them, whether the application is running or not.</span></span> <span data-ttu-id="57a8d-228">这会将凭据授权的所有 Azure 资源置于风险之中！</span><span class="sxs-lookup"><span data-stu-id="57a8d-228">This puts all Azure resources the credentials are authorized for at risk!</span></span>

<span data-ttu-id="57a8d-229">下表列出了 SDK 中符合 `AuthorizerConfig` 接口的类型。</span><span class="sxs-lookup"><span data-stu-id="57a8d-229">The following table lists the types in the SDK that conform to the `AuthorizerConfig` interface.</span></span>

| <span data-ttu-id="57a8d-230">身份验证类型</span><span class="sxs-lookup"><span data-stu-id="57a8d-230">Authentication type</span></span> | <span data-ttu-id="57a8d-231">授权者类型</span><span class="sxs-lookup"><span data-stu-id="57a8d-231">Authorizer type</span></span> |
|---------------------|-----------------------|
| <span data-ttu-id="57a8d-232">基于证书的身份验证</span><span class="sxs-lookup"><span data-stu-id="57a8d-232">Certificate-based authentication</span></span> | <span data-ttu-id="57a8d-233">[ClientCertificateConfig]</span><span class="sxs-lookup"><span data-stu-id="57a8d-233">[ClientCertificateConfig]</span></span> |
| <span data-ttu-id="57a8d-234">客户端凭据</span><span class="sxs-lookup"><span data-stu-id="57a8d-234">Client credentials</span></span> | <span data-ttu-id="57a8d-235">[ClientCredentialsConfig]</span><span class="sxs-lookup"><span data-stu-id="57a8d-235">[ClientCredentialsConfig]</span></span> |
| <span data-ttu-id="57a8d-236">Azure 资源的托管标识</span><span class="sxs-lookup"><span data-stu-id="57a8d-236">Managed identities for Azure resources</span></span> | <span data-ttu-id="57a8d-237">[MSIConfig]</span><span class="sxs-lookup"><span data-stu-id="57a8d-237">[MSIConfig]</span></span> |
| <span data-ttu-id="57a8d-238">用户名/密码</span><span class="sxs-lookup"><span data-stu-id="57a8d-238">Username/password</span></span> | <span data-ttu-id="57a8d-239">[UsernamePasswordConfig]</span><span class="sxs-lookup"><span data-stu-id="57a8d-239">[UsernamePasswordConfig]</span></span> |

[ClientCertificateConfig]: https://godoc.org/github.com/Azure/go-autorest/autorest/azure/auth#ClientCertificateConfig
[ClientCredentialsConfig]: https://godoc.org/github.com/Azure/go-autorest/autorest/azure/auth#ClientCredentialsConfig
[MSIConfig]: https://godoc.org/github.com/Azure/go-autorest/autorest/azure/auth#MSIConfig
[DeviceFlowConfig]: https://godoc.org/github.com/Azure/go-autorest/autorest/azure/auth#DeviceFlowConfig
[UsernamePasswordConfig]: https://godoc.org/github.com/Azure/go-autorest/autorest/azure/auth#UsernamePasswordConfig

<span data-ttu-id="57a8d-244">使用关联的 `New` 函数创建一个验证器，然后针对生成的对象调用 `Authorize` 进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="57a8d-244">Create an authenticator with its associated `New` function, and then call `Authorize` on the resulting object to authenticate.</span></span> <span data-ttu-id="57a8d-245">例如，若要使用基于证书的身份验证，请执行以下调用：</span><span class="sxs-lookup"><span data-stu-id="57a8d-245">For example, to use certificate-based authentication:</span></span>

```go
import "github.com/Azure/go-autorest/autorest/azure/auth"
certificateAuthorizer := auth.NewClientCertificateConfig(certificatePath, certificatePassword, clientID, tenantID)
authorizerToken, err := certificateAuthorizer.Authorize()
```
