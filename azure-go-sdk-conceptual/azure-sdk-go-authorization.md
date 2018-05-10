---
title: 使用 Azure SDK for Go 进行身份验证
description: 了解 Azure SDK for Go 中提供的身份验证方法及其用法。
services: azure
author: sptramer
ms.author: sttramer
manager: carmonm
ms.date: 04/03/2018
ms.topic: conceptual
ms.prod: azure
ms.technology: azure-sdk-go
ms.devlang: go
ms.service: active-directory
ms.component: authentication
ms.openlocfilehash: 370f5607b89c0044022f7987d06c3a55c9d6f352
ms.sourcegitcommit: f08abf902b48f8173aa6e261084ff2cfc9043305
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/03/2018
---
# <a name="authentication-methods-in-the-azure-sdk-for-go"></a>Azure SDK for Go 中的身份验证方法

Azure SDK for Go 提供各种身份验证类型和方法供应用程序使用。 支持的身份验证方法包括从环境变量提取信息，以及基于 Web 的交互式身份验证等等。 本文介绍该 SDK 中提供的身份验证类型及其用法。 此外，还阐述了有关选择最适合应用程序的身份验证类型的最佳做法。

## <a name="available-authentication-types-and-methods"></a>可用的身份验证类型和方法

Azure SDK for Go 提供使用不同凭据集的多种身份验证类型。 可通过不同的身份验证方法使用其中的每种身份验证类型，该 SDK 也正是通过这些方法将这些凭据用作输入。 下表描述了可用的身份验证类型，以及在哪些场合下建议让应用程序使用这些类型。

| 身份验证类型 | 建议的使用场合... |
|---------------------|---------------------|
| 基于证书的身份验证 | 具有一个针对 Azure Active Directory (AAD) 用户或服务主体配置的 X509 证书。 有关详细信息，请参阅 [Azure Active Directory 中基于证书的身份验证入门]。 |
| 客户端凭据 | 已配置一个服务主体，该服务主体是针对此应用程序或它所属的应用程序类设置的。 有关详细信息，请参阅[使用 Azure CLI 2.0 创建服务主体]。 |
| 托管服务标识 (MSI) | 应用程序在使用托管服务标识 (MSI) 配置的 Azure 资源中运行。 有关详细信息，请参阅 [Azure 资源的托管服务标识 (MSI)]。 |
| 设备令牌 | 应用程序预期只以交互方式使用，并且包含各种用户，这些用户有可能来自多个 AAD 租户。 用户可以访问 Web 浏览器进行登录。 有关详细信息，请参阅[使用设备令牌身份验证](#use-device-token-authentication)。|
| 用户名/密码 | 某个交互式应用程序无法使用其他任何身份验证方法。 没有为用户 AAD 登录启用多重身份验证。 |

> [!IMPORTANT]
> 如果使用的身份验证类型不是客户端凭据，则必须在 Azure Active Directory 中注册应用程序。 有关操作方法，请参阅[将应用程序与 Azure Active Directory 集成](/azure/active-directory/develop/active-directory-integrating-applications)。

> [!NOTE]
> 除非有特殊要求，否则请避免使用用户名/密码身份验证。 在适合使用基于用户的登录的情况下，通常可用改用设备令牌身份验证。

[Azure Active Directory 中基于证书的身份验证入门]: /azure/active-directory/active-directory-certificate-based-authentication-get-started
[使用 Azure CLI 2.0 创建服务主体]: /cli/azure/create-an-azure-service-principal-azure-cli
[Azure 资源的托管服务标识 (MSI)]: /azure/active-directory/managed-service-identity/overview

可通过不同的方法使用这些身份验证类型。 [_基于环境的身份验证_](#use-environment-based-authentication)直接从程序的环境读取凭据。 [_基于文件的身份验证_](#use-file-based-authentication)加载包含服务主体凭据的文件。 [_基于客户端的身份验证_](#use-an-authentication-client)使用 Go 代码中的对象，你负责在程序执行期间提供凭据。 最后，[_设备令牌身份验证_](#use-device-token-authentication)要求用户使用令牌通过 Web 浏览器以交互方式登录，并且不能与基于环境或基于文件的身份验证配合使用。

所有身份验证函数和类型都在 `github.com/Azure/go-autorest/autorest/azure/auth` 包中提供。

> [!NOTE]
> 除非有特殊要求，否则请避免使用基于客户端的身份验证。 这种身份验证方法很容易引起错误的做法。 具体而言，使用基于客户端的身份验证容易导致凭据硬编码。 此外，如果身份验证要求发生变化，可能无法使用将来的 SDK 版本编写身份验证的自定义代码。

## <a name="use-environment-based-authentication"></a>使用基于环境的身份验证

如果在严格受控的环境（例如容器）中运行应用程序，基于环境的身份验证是自然而然的选择。 可以在运行应用程序之前配置 shell 环境，然后，Go SDK 会在运行时读取这些环境变量，以便在 Azure 中进行身份验证。 

基于环境的身份验证支持除设备令牌以外的所有身份验证方法，其评估顺序如下：客户端凭据、证书、用户名/密码和托管服务标识 (MSI)。 如果未设置所需的环境变量，或者 SDK 遭到身份验证服务的拒绝，则会尝试下一个身份验证类型。 如果 SDK 无法从环境进行身份验证，则会返回错误。

下表详细描述了需要为基于环境的身份验证支持的每种身份验证类型设置的环境变量。

| 身份验证类型 | 环境变量 | 说明 |
| ------------------- | -------------------- | ----------- |
| __客户端凭据__ | `AZURE_TENANT_ID` | 服务主体所属的 Active Directory 租户的 ID。 |
| | `AZURE_CLIENT_ID` | 服务主体的名称或 ID。 |
| | `AZURE_CLIENT_SECRET` | 与服务主体关联的机密。 |
| 证书 | `AZURE_TENANT_ID` | 证书注册到的 Active Directory 租户的 ID。 |
| | `AZURE_CLIENT_ID` | 与证书关联的应用程序客户端 ID。 |
| | `AZURE_CERTIFICATE_PATH` | 客户端证书文件的路径。 |
| | `AZURE_CERTIFICATE_PASSWORD` | 客户端证书的密码。 |
| __用户名/密码__ | `AZURE_TENANT_ID` | 用户所属的 Active Directory 租户的 ID。 |
| | `AZURE_CLIENT_ID` | 应用程序客户端 ID。 |
| | `AZURE_USERNAME` | 用于登录的用户名。 |
| | `AZURE_PASSWORD` | 用于登录的密码。 |
| __MSI__ | | MSI 不要求设置任何凭据。 应用程序必须在配置为使用 MSI 的 Azure 资源中运行。 有关详细信息，请参阅 [Azure 资源的托管服务标识 (MSI)]。 |

如果需要连接到除默认 Azure 公有云以外的云或管理终结点，则还可以设置以下环境变量。 设置这些环境变量的最常见原因是要使用 Azure Stack、不同地理区域中的云，或 Azure 经典部署模型。

| 环境变量 | 说明  |
|----------------------|--------------|
| `AZURE_ENVIRONMENT` | 要连接到的云环境的名称。 |
| `AZURE_AD_RESOURCE` | 连接时使用的 Active Directory 资源 ID。 这应该是指向管理终结点的 URI。 |

使用基于环境的身份验证时，请调用 [NewAuthorizerFromEnvironment](https://godoc.org/github.com/Azure/go-autorest/autorest/azure/auth#NewAuthorizerFromEnvironment) 函数来获取授权者对象。 然后，在客户端的 `Authorizer` 属性中设置此对象，以允许客户端访问 Azure。

```go
import "github.com/Azure/go-autorest/autorest/azure/auth"
authorizer, err := auth.NewAuthorizerFromEnvironment()
```

### <a name="authentication-on-azure-stack"></a>Azure Stack 上的身份验证

若要在 Azure Stack 上进行身份验证，需要设置以下变量：

| 环境变量 | 说明  |
|----------------------|--------------|
| `AZURE_AD_ENDPOINT` | Active Directory 终结点。 |
| `AZURE_AD_RESOURCE` | Active Directory 资源 ID。 |

可以从 Azure Stack 元数据信息中检索这些变量。 若要检索元数据，请在 Azure Stack 环境中打开 Web 浏览器并使用 url：`(ResourceManagerURL)/metadata/endpoints?api-version=1.0`

`ResourceManagerURL` 因 Azure Stack 部署的区域名称、计算机名称和外部完全限定的域名 (FQDN) 而异：

| 环境 | ResourceManagerURL |
|----------------------|--------------|
| 开发工具包 | `https://management.local.azurestack.external/` |
| 集成系统 | `https://management.(region).ext-(machine-name).(FQDN)` |

有关如何在 Azure Stack 上使用适用于 Go 的 Azure SDK 的更多详细信息，请参阅[在 Azure Stack 中将 API 版本配置文件与 Go 配合使用](https://docs.microsoft.com/en-us/azure/azure-stack/user/azure-stack-version-profiles-go)


## <a name="use-file-based-authentication"></a>使用基于文件的身份验证

只能配合 [Azure CLI 2.0](/cli/azure) 生成的、以本地文件格式存储的客户端凭据使用基于文件的身份验证。 在创建新服务主体时，可以使用 `--sdk-auth` 参数轻松创建此文件。 如果打算使用基于文件的身份验证，请确保在创建服务主体时提供此参数。 由于 CLI 在 `stdout` 中列显输出，因此会将输出重定向到某个文件。

```azurecli
az ad sp create-for-rbac --sdk-auth > azure.auth
```

将 `AZURE_AUTH_LOCATION` 环境变量设置为授权文件所在的位置。 应用程序会读取此环境变量，并且会分析其中包含的凭据。 如果需要在运行时选择授权文件，请使用 [os.Setenv](https://golang.org/pkg/os/#Setenv) 函数来操作程序的环境。

若要加载身份验证信息，请调用 [NewAuthorizerFromFile](https://godoc.org/github.com/Azure/go-autorest/autorest/azure/auth#NewAuthorizerFromFile) 函数。 与基于环境的授权不同，基于文件的授权需要一个资源终结点。

```go
import "github.com/Azure/go-autorest/autorest/azure/auth"
authorizer, err := NewAuthorizerFromFile(azure.PublicCloud.ResourceManagerEndpoint)
```

有关使用服务主体和管理其访问权限的详细信息，请参阅[使用 Azure CLI 2.0 创建服务主体]。

## <a name="use-device-token-authentication"></a>使用设备令牌身份验证

如果希望用户以交互方式登录，最好是通过设备令牌身份验证提供该功能。 此身份验证流将为用户传递一个可粘贴到 Microsoft 登录站点的令牌，然后，用户可在该站点上使用 Azure Active Directory (AAD) 帐户登录。 与标准的用户名/密码身份验证不同，此身份验证方法支持已启用多重身份验证的帐户。

若要使用设备令牌身份验证，请使用 [NewDeviceFlowConfig](https://godoc.org/github.com/Azure/go-autorest/autorest/azure/auth#NewDeviceFlowConfig) 函数创建一个 [DeviceFlowConfig](https://godoc.org/github.com/Azure/go-autorest/autorest/azure/auth#DeviceFlowConfig) 授权者。 针对生成的对象调用 [Authorizer](https://godoc.org/github.com/Azure/go-autorest/autorest/azure/auth#DeviceFlowConfig.Authorizer) 以启动身份验证过程。 在整个身份验证流完成之前，设备流身份验证会一直阻止程序的执行。

```go
import "github.com/Azure/go-autorest/autorest/azure/auth"
deviceConfig := auth.NewDeviceFlowConfig(applicationID, tenantID)
authorizer, err := deviceConfig.Authorizer()
```

## <a name="use-an-authentication-client"></a>使用身份验证客户端

如果需要特定的身份验证类型，并且愿意让程序执行相应的操作以从用户加载身份验证信息，则可以使用任何符合 [auth.AuthorizerConfig](https://godoc.org/github.com/Azure/go-autorest/autorest/azure/auth#AuthorizerConfig) 接口的客户端。 如果想要使用交互式程序、使用专用配置文件，或者某项要求导致无法使用其他身份验证方法，那么，请使用实现此接口的身份验证类型。

> [!WARNING]
> 切勿在应用程序中对 Azure 凭据进行硬编码。 将机密放入应用程序二进制文件会使攻击者更容易提取这些机密，不管应用程序是否正在运行。 这会将凭据授权的所有 Azure 资源置于风险之中！

下表列出了 SDK 中符合 `AuthorizerConfig` 接口的类型。

| 身份验证类型 | 授权者类型 |
|---------------------|-----------------------|
| 基于证书的身份验证 | [ClientCertificateConfig] |
| 客户端凭据 | [ClientCredentialsConfig] |
| 托管服务标识 (MSI) | [MSIConfig] |
| 用户名/密码 | [UsernamePasswordConfig] |

[ClientCertificateConfig]: https://godoc.org/github.com/Azure/go-autorest/autorest/azure/auth#ClientCertificateConfig
[ClientCredentialsConfig]: https://godoc.org/github.com/Azure/go-autorest/autorest/azure/auth#ClientCredentialsConfig
[MSIConfig]: https://godoc.org/github.com/Azure/go-autorest/autorest/azure/auth#MSIConfig
[DeviceFlowConfig]: https://godoc.org/github.com/Azure/go-autorest/autorest/azure/auth#DeviceFlowConfig
[UsernamePasswordConfig]: https://godoc.org/github.com/Azure/go-autorest/autorest/azure/auth#UsernamePasswordConfig

使用关联的 `New` 函数创建一个验证器，然后针对生成的对象调用 `Authorize` 以执行身份验证。 例如，若要使用基于证书的身份验证，请执行以下调用：

```go
import "github.com/Azure/go-autorest/autorest/azure/auth"
certificateAuthorizer := auth.NewClientCertificateConfig(certificatePath, certificatePassword, clientID, tenantID)
authorizerToken, err := certificateAuthorizer.Authorize()
```
