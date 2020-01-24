---
title: Zmiany wprowadzone do projektów WebAPI podczas nawiązywania połączenia z usługą Azure AD
description: Opisuje, co się dzieje z projektem WebAPI podczas nawiązywania połączenia z usługą Azure AD przy użyciu programu Visual Studio
author: ghogen
manager: jillfra
ms.assetid: 57630aee-26a2-4326-9dbb-ea2a66daa8b0
ms.workload: azure-vs
ms.prod: visual-studio-windows
ms.technology: vs-azure
ms.topic: conceptual
ms.date: 03/12/2018
ms.author: ghogen
ms.custom: aaddev, vs-azure
ms.openlocfilehash: 9858fdb7da80fe03498392ea93bf3c83b8a7e4dd
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2020
ms.locfileid: "76699873"
---
# <a name="what-happened-to-my-webapi-project-visual-studio-azure-active-directory-connected-service"></a>Co się stało z moim projektem WebAPI (usługa połączona Azure Active Directory Visual Studio)

> [!div class="op_single_selector"]
> - [Wprowadzenie](vs-active-directory-webapi-getting-started.md)
> - [Co się stało](vs-active-directory-webapi-what-happened.md)

W tym artykule opisano dokładne zmiany wprowadzone do ASP.NET WebAPI, ASP.NET aplikację jednostronicową i ASP.NET projekty interfejsu API platformy Azure podczas dodawania [usługi połączonej Azure Active Directory przy użyciu programu Visual Studio](vs-active-directory-add-connected-service.md). Dotyczy również projektów usługi mobilnej platformy Azure ASP.NET w programie Visual Studio 2015.

Aby uzyskać informacje na temat pracy z połączoną usługą, zobacz [wprowadzenie](vs-active-directory-webapi-getting-started.md).

## <a name="added-references"></a>Dodano odwołania

Wpływa na plik projektu *. NET References) i `packages.config` (odwołania NuGet).

| Typ | Informacje ogólne |
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

| Typ | Informacje ogólne |
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

| Typ | Informacje ogólne |
| --- | --- |
| .NET; NuGet | Microsoft.AspNet.Identity.Core |
| .NET; NuGet | Microsoft.AspNet.Identity.EntityFramework |
| .NET; NuGet | Microsoft.AspNet.Identity.Owin |

## <a name="project-file-changes"></a>Zmiany w pliku projektu

- Ustaw właściwość `IISExpressSSLPort` na wartość DISTINCT.
- Ustaw właściwość `WebProject_DirectoryAccessLevelKey` na 0 lub 1, jeśli wybrano opcję **Odczytaj dane katalogu** .
- Ustaw właściwość `IISUrl` na `https://localhost:<port>/`, gdzie `<port>` pasuje do wartości `IISExpressSSLPort`.

## <a name="webconfig-or-appconfig-changes"></a>zmiany pliku Web. config lub App. config

- Dodano następujące wpisy konfiguracji:

    ```xml
    <appSettings>
        <add key="ida:ClientId" value="<ClientId from the new Azure AD app>" />
        <add key="ida:Tenant" value="<your selected Azure domain>" />
        <add key="ida:Audience" value="<your selected domain + / + project name>" />
    </appSettings>
    ```

- Tylko program Visual Studio 2017: dodano również następujący wpis w `<appSettings>`"

    ```xml
    <add key="ida:MetadataAddress" value="<domain URL + /federationmetadata/2007-06/federationmetadata.xml>" />
    ```

- Dodano elementy `<dependentAssembly>` w węźle `<runtime><assemblyBinding>` do `System.IdentityModel.Tokens.Jwt`.

- W przypadku wybrania opcji **Odczytaj dane katalogu** dodano następujący wpis konfiguracji w obszarze `<appSettings>`:

    ```xml
    <add key="ida:Password" value="<Your Azure AD app's new password>" />
    ```

## <a name="code-changes-and-additions"></a>Zmiany i dodatki kodu

- Dodano atrybut `[Authorize]` do `Controllers/ValueController.cs` i innych istniejących kontrolerów.

- Dodano klasę uruchamiania uwierzytelniania, `App_Start/Startup.Auth.cs`, zawierającą logikę uruchamiania uwierzytelniania usługi Azure AD lub odpowiednio ją zmodyfikowano. W przypadku wybrania opcji **Czytaj dane katalogu** ten plik zawiera również kod umożliwiający otrzymanie kodu OAuth i wymienianie go z tokenem dostępu.

- (Tylko program Visual Studio 2015 z aplikacją ASP.NET 4) Usunięto `App_Start/IdentityConfig.cs` i dodano `Controllers/AccountController.cs`, `Models/IdentityModel.cs`i `Providers/ApplicationAuthProvider.cs`.

- Dodano `Connected Services/AzureAD/ConnectedService.json` (Visual Studio 2017) lub `Service References/Azure AD/ConnectedService.json` (Visual Studio 2015) zawierające informacje używane przez program Visual Studio do śledzenia dodawania połączonej usługi.

### <a name="file-backup-visual-studio-2015"></a>Kopia zapasowa plików (Visual Studio 2015)

Podczas dodawania połączonej usługi program Visual Studio 2015 tworzy kopie zapasowe zmienionych i usuniętych plików. Wszystkie pliki, których to dotyczy, są zapisywane w folderze `Backup/AzureAD`. Program Visual Studio 2017 nie tworzy kopii zapasowych.

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
