---
title: Zmiany wprowadzone w projektach webapi podczas łączenia się z usługą Azure AD
description: W tym artykule opisano, co dzieje się z projektem WebAPI podczas łączenia się z usługą Azure AD przy użyciu programu Visual Studio
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
ms.openlocfilehash: 595a89ca58e970a9c886d0b6c2dd05aecd1411ef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77159390"
---
# <a name="what-happened-to-my-webapi-project-visual-studio-azure-active-directory-connected-service"></a>Co się stało z moim projektem WebAPI (usługa połączona z usługą Visual Studio Azure Active Directory)

> [!div class="op_single_selector"]
> - [Wprowadzenie](vs-active-directory-webapi-getting-started.md)
> - [Co się stało](vs-active-directory-webapi-what-happened.md)

W tym artykule identyfikowane są dokładne zmiany wprowadzone w ASP.NET webapi, ASP.NET aplikacji jednostronicowej i ASP.NET projektów interfejsu API platformy Azure podczas dodawania [połączonej usługi Azure Active Directory przy użyciu programu Visual Studio](vs-active-directory-add-connected-service.md). Dotyczy również projektów usługi Azure Mobile w programie ASP.NET usługi Azure w programie Visual Studio 2015.

Aby uzyskać informacje na temat pracy z połączoną usługą, zobacz [Wprowadzenie](vs-active-directory-webapi-getting-started.md).

## <a name="added-references"></a>Dodano referencje

Wpływa na pliki projektu *.NET `packages.config` odwołania) i (Odwołania NuGet).

| Typ | Tematy pomocy |
| --- | --- |
| .NET; Nuget | Microsoft.Owin |
| .NET; Nuget | Microsoft.Owin.Host.SystemWeb |
| .NET; Nuget | Microsoft.Owin.Security |
| .NET; Nuget | Microsoft.Owin.Security.ActiveDirectory |
| .NET; Nuget | Microsoft.Owin.Security.Jwt |
| .NET; Nuget | Microsoft.Owin.Security.OAuth |
| .NET; Nuget | Okręg wyborczy Owin |
| .NET; Nuget | System.IdentityModel.Tokens.Jwt |

Dodatkowe odwołania, jeśli wybrano opcję **Odczyt danych katalogu:**

| Typ | Tematy pomocy |
| --- | --- |
| .NET; Nuget | Elementframework |
| .NET        | EntityFramework.SqlServer (tylko program Visual Studio 2015) |
| .NET; Nuget | Microsoft.Azure.ActiveDirectory.GraphClient |
| .NET; Nuget | Microsoft.Data.Edm |
| .NET; Nuget | Microsoft.Data.OData |
| .NET; Nuget | Microsoft.Data.Services.Klient |
| .NET; Nuget | Microsoft.IdentityModel.Clients.ActiveDirectory |
| .NET        | Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms<br>(Tylko program Visual Studio 2015) |
| .NET; Nuget | System.Przestrzenny |

Następujące odwołania są usuwane (ASP.NET tylko 4 projekty, jak w programie Visual Studio 2015):

| Typ | Tematy pomocy |
| --- | --- |
| .NET; Nuget | Microsoft.AspNet.Identity.Core |
| .NET; Nuget | Microsoft.AspNet.Identity.EntityFramework |
| .NET; Nuget | Microsoft.AspNet.Identity.Owin |

## <a name="project-file-changes"></a>Zmiany w pliku projektu

- Ustaw właściwość `IISExpressSSLPort` na odrębną liczbę.
- Ustaw właściwość `WebProject_DirectoryAccessLevelKey` na 0 lub 1, jeśli wybrano opcję **Odczyt danych katalogu.**
- Ustaw `IISUrl` właściwość `https://localhost:<port>/` `<port>` na `IISExpressSSLPort` miejsce, w którym jest zgodna z wartością.

## <a name="webconfig-or-appconfig-changes"></a>web.config lub app.config zmiany

- Dodano następujące wpisy konfiguracji:

    ```xml
    <appSettings>
        <add key="ida:ClientId" value="<ClientId from the new Azure AD app>" />
        <add key="ida:Tenant" value="<your selected Azure domain>" />
        <add key="ida:Audience" value="<your selected domain + / + project name>" />
    </appSettings>
    ```

- Tylko visual studio 2017: dodano `<appSettings>`również następujący wpis w obszarze "

    ```xml
    <add key="ida:MetadataAddress" value="<domain URL + /federationmetadata/2007-06/federationmetadata.xml>" />
    ```

- Dodano `<dependentAssembly>` elementy `<runtime><assemblyBinding>` pod `System.IdentityModel.Tokens.Jwt`węzłem dla .

- Jeśli wybrano opcję **Odczyt danych katalogu,** `<appSettings>`dodano następujący wpis konfiguracji w obszarze:

    ```xml
    <add key="ida:Password" value="<Your Azure AD app's new password>" />
    ```

## <a name="code-changes-and-additions"></a>Zmiany i uzupełnienia kodu

- Dodano `[Authorize]` atrybut `Controllers/ValueController.cs` i inne istniejące kontrolery.

- Dodano klasę uruchamiania `App_Start/Startup.Auth.cs`uwierzytelniania , zawierającą logikę uruchamiania dla uwierzytelniania usługi Azure AD lub odpowiednio ją zmodyfikował. Jeśli wybrano opcję **Odczyt danych katalogu,** ten plik zawiera również kod do odbierania kodu OAuth i wymiany go na token dostępu.

- (Visual Studio 2015 tylko z aplikacją ASP.NET 4) Usunięto `App_Start/IdentityConfig.cs` i dodano `Controllers/AccountController.cs`, `Models/IdentityModel.cs`i `Providers/ApplicationAuthProvider.cs`.

- Dodano `Connected Services/AzureAD/ConnectedService.json` (Visual Studio 2017) lub `Service References/Azure AD/ConnectedService.json` (Visual Studio 2015), zawierające informacje używane przez program Visual Studio do śledzenia dodania połączonej usługi.

### <a name="file-backup-visual-studio-2015"></a>Kopia zapasowa plików (Visual Studio 2015)

Podczas dodawania połączonej usługi program Visual Studio 2015 tworzą kopie zapasowe zmienionych i usuniętych plików. Wszystkie pliki, których dotyczy `Backup/AzureAD`problem, są zapisywane w folderze . Visual Studio 2017 nie tworzy kopii zapasowych.

- `Startup.cs`
- `App_Start\IdentityConfig.cs`
- `App_Start\Startup.Auth.cs`
- `Controllers\AccountController.cs`
- `Controllers\ManageController.cs`
- `Models\IdentityModels.cs`
- `Models\ApplicationOAuthProvider.cs`

## <a name="changes-on-azure"></a>Zmiany na platformie Azure

- Utworzono aplikację usługi Azure AD w domenie wybranej podczas dodawania połączonej usługi.
- Zaktualizowano aplikację o uprawnienie **Odczyt danych katalogu,** jeśli ta opcja została zaznaczona.

[Dowiedz się więcej o usłudze Azure Active Directory](https://azure.microsoft.com/services/active-directory/).

## <a name="next-steps"></a>Następne kroki

- [Scenariusze uwierzytelniania dla usługi Azure Active Directory](authentication-scenarios.md)
- [dodawanie logowania przy użyciu konta Microsoft do aplikacji internetowej ASP.NET](quickstart-v2-aspnet-webapp.md)
