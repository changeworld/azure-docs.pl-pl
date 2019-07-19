---
title: Zmiany wprowadzone w projekcie WebAPI podczas nawiązywania połączenia z usługą Azure AD
description: Opisuje, co się dzieje z projektem WebAPI podczas nawiązywania połączenia z usługą Azure AD za pomocą programu Visual Studio
services: active-directory
author: ghogen
manager: douge
ms.service: active-directory
ms.subservice: develop
ms.assetid: 57630aee-26a2-4326-9dbb-ea2a66daa8b0
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 03/12/2018
ms.author: ghogen
ms.custom: aaddev, vs-azure
ms.collection: M365-identity-device-management
ms.openlocfilehash: 19a36ca4b194d8031b4a263a092ecb52be74cdd0
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/18/2019
ms.locfileid: "68324289"
---
# <a name="what-happened-to-my-webapi-project-visual-studio-azure-active-directory-connected-service"></a>Co się stało z moim projektem WebAPI (usługa połączona Azure Active Directory Visual Studio)

> [!div class="op_single_selector"]
> - [Wprowadzenie](vs-active-directory-webapi-getting-started.md)
> - [Co się stało](vs-active-directory-webapi-what-happened.md)

W tym artykule opisano dokładne zmiany wprowadzone do ASP.NET WebAPI, ASP.NET aplikację jednostronicową i ASP.NET projekty interfejsu API platformy Azure podczas dodawania [usługi połączonej Azure Active Directory przy użyciu programu Visual Studio](vs-active-directory-add-connected-service.md). Dotyczy również projektów usługi mobilnej platformy Azure ASP.NET w programie Visual Studio 2015.

Aby uzyskać informacje na temat pracy z połączoną usługą, zobacz [wprowadzenie](vs-active-directory-webapi-getting-started.md).

## <a name="added-references"></a>Dodano odwołania

Dotyczy pliku projektu *. NET References i `packages.config` (odwołania NuGet).

| Type | Tematy pomocy |
| --- | --- |
| .NET; NuGet | Microsoft.Owin |
| .NET; NuGet | Microsoft.Owin.Host.SystemWeb |
| .NET; NuGet | Microsoft.Owin.Security |
| .NET; NuGet | Microsoft.Owin.Security.ActiveDirectory |
| .NET; NuGet | Microsoft.Owin.Security.Jwt |
| .NET; NuGet | Microsoft.Owin.Security.OAuth |
| .NET; NuGet | Owin |
| .NET; NuGet | System.IdentityModel.Tokens.Jwt |

Dodatkowe odwołania w przypadku wybrania opcji **Odczytaj dane katalogu** :

| Type | Tematy pomocy |
| --- | --- |
| .NET; NuGet | EntityFramework |
| .NET        | EntityFramework. SqlServer (tylko w programie Visual Studio 2015) |
| .NET; NuGet | Microsoft.Azure.ActiveDirectory.GraphClient |
| .NET; NuGet | Microsoft.Data.Edm |
| .NET; NuGet | Microsoft.Data.OData |
| .NET; NuGet | Microsoft.Data.Services.Client |
| .NET; NuGet | Microsoft.IdentityModel.Clients.ActiveDirectory |
| .NET        | Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms<br>(Tylko w programie Visual Studio 2015) |
| .NET; NuGet | System.Spatial |

Następujące odwołania są usuwane (tylko projekty ASP.NET 4, jak w programie Visual Studio 2015):

| Type | Tematy pomocy |
| --- | --- |
| .NET; NuGet | Microsoft.AspNet.Identity.Core |
| .NET; NuGet | Microsoft.AspNet.Identity.EntityFramework |
| .NET; NuGet | Microsoft.AspNet.Identity.Owin |

## <a name="project-file-changes"></a>Zmiany w pliku projektu

- Ustaw właściwość `IISExpressSSLPort` na wartość DISTINCT.
- Ustaw właściwość `WebProject_DirectoryAccessLevelKey` na 0 lub 1, jeśli wybrano opcję Odczytaj **dane katalogu** .
- Ustaw właściwość `IISUrl` na gdzie `https://localhost:<port>/` `<port>` pasuje dowartości.`IISExpressSSLPort`

## <a name="webconfig-or-appconfig-changes"></a>zmiany pliku Web. config lub App. config

- Dodano następujące wpisy konfiguracji:

    ```xml
    <appSettings>
        <add key="ida:ClientId" value="<ClientId from the new Azure AD app>" />
        <add key="ida:Tenant" value="<your selected Azure domain>" />
        <add key="ida:Audience" value="<your selected domain + / + project name>" />
    </appSettings>
    ```

- Tylko program Visual Studio 2017: Dodano także następujący wpis w pozycji `<appSettings>`"

    ```xml
    <add key="ida:MetadataAddress" value="<domain URL + /federationmetadata/2007-06/federationmetadata.xml>" />
    ```

- Dodano `<dependentAssembly>` elementy `System.IdentityModel.Tokens.Jwt`w węźle dla. `<runtime><assemblyBinding>`

- W przypadku wybrania opcji Odczytaj **dane katalogu** dodano następujący wpis konfiguracyjny poniżej `<appSettings>`:

    ```xml
    <add key="ida:Password" value="<Your Azure AD app's new password>" />
    ```

## <a name="code-changes-and-additions"></a>Zmiany i dodatki kodu

- Dodano atrybut do `Controllers/ValueController.cs` i wszystkich innych istniejących kontrolerów. `[Authorize]`

- Dodano klasę uruchamiania uwierzytelniania, `App_Start/Startup.Auth.cs`zawierającą logikę uruchamiania dla uwierzytelniania usługi Azure AD lub odpowiednio ją zmodyfikowano. W przypadku wybrania opcji **Czytaj dane katalogu** ten plik zawiera również kod umożliwiający otrzymanie kodu OAuth i wymienianie go z tokenem dostępu.

- (Tylko program Visual Studio 2015 z aplikacją ASP.NET 4) Usunięty `App_Start/IdentityConfig.cs` i dodany `Controllers/AccountController.cs`, `Models/IdentityModel.cs`, i `Providers/ApplicationAuthProvider.cs`.

- Dodano `Connected Services/AzureAD/ConnectedService.json` (Visual Studio 2017) lub `Service References/Azure AD/ConnectedService.json` (Visual Studio 2015) zawierający informacje używane przez program Visual Studio do śledzenia dodawania połączonej usługi.

### <a name="file-backup-visual-studio-2015"></a>Kopia zapasowa plików (Visual Studio 2015)

Podczas dodawania połączonej usługi program Visual Studio 2015 tworzy kopie zapasowe zmienionych i usuniętych plików. Wszystkie odnośne pliki są zapisywane w folderze `Backup/AzureAD`. Program Visual Studio 2017 nie tworzy kopii zapasowych.

- `Startup.cs`
- `App_Start\IdentityConfig.cs`
- `App_Start\Startup.Auth.cs`
- `Controllers\AccountController.cs`
- `Controllers\ManageController.cs`
- `Models\IdentityModels.cs`
- `Models\ApplicationOAuthProvider.cs`

## <a name="changes-on-azure"></a>Zmiany na platformie Azure

- Utworzono aplikację usługi Azure AD w domenie wybranej podczas dodawania połączonej usługi.
- Aplikacja została zaktualizowana w celu uwzględnienia uprawnienia **Odczyt danych katalogu** , jeśli ta opcja została wybrana.

[Dowiedz się więcej o Azure Active Directory](https://azure.microsoft.com/services/active-directory/).

## <a name="next-steps"></a>Następne kroki

- [Scenariusze uwierzytelniania dla Azure Active Directory](authentication-scenarios.md)
- [Dodawanie logowania z firmą Microsoft do aplikacji sieci Web ASP.NET](quickstart-v1-aspnet-webapp.md)
