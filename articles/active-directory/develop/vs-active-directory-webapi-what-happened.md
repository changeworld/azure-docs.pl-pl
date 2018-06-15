---
title: Zmiany wprowadzone do projektu WebAPI podczas łączenia z usługą Azure AD
description: W tym artykule opisano, co się dzieje z projektu WebAPI podczas łączenia z usługą Azure AD za pomocą programu Visual Studio
services: active-directory
author: ghogen
manager: douge
ms.assetid: 57630aee-26a2-4326-9dbb-ea2a66daa8b0
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure
ms.topic: conceptual
ms.date: 03/12/2018
ms.author: ghogen
ms.custom: aaddev
ms.openlocfilehash: b8303ccf6fc96e8d0708c9e414fdf511e0cf2fdf
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2018
ms.locfileid: "31785165"
---
# <a name="what-happened-to-my-webapi-project-visual-studio-azure-active-directory-connected-service"></a>Co się stało z projektu WebAPI (Visual Studio usługi Azure Active Directory połączenia usługi)

> [!div class="op_single_selector"]
> - [Wprowadzenie](vs-active-directory-webapi-getting-started.md)
> - [Co się stało](vs-active-directory-webapi-what-happened.md)

W tym artykule identyfikuje dokładne zmiany wprowadzone do projektów platformy ASP.NET WebAPI, aplikacji jednej strony ASP.NET i interfejsu API platformy ASP.NET Azure, podczas dodawania [usługi Azure Active Directory połączenia usługi przy użyciu programu Visual Studio](vs-active-directory-add-connected-service.md). Ma również zastosowanie do usługi mobilnej Azure ASP.NET projektów programu Visual Studio 2015.

Aby uzyskać informacje na temat pracy z podłączonej usługi, zobacz [wprowadzenie](vs-active-directory-webapi-getting-started.md).

## <a name="added-references"></a>Dodano odwołań

Wpływa na *.NET odwołań pliku projektu) i `packages.config` (NuGet odwołania).

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

Dodatkowe informacje w przypadku wybrania **odczytuj dane katalogu** opcji:

| Typ | Informacje ogólne |
| --- | --- |
| .NET; NuGet | EntityFramework |
| .NET        | EntityFramework.SqlServer (Visual Studio 2015 tylko) |
| .NET; NuGet | Microsoft.Azure.ActiveDirectory.GraphClient |
| .NET; NuGet | Microsoft.Data.Edm |
| .NET; NuGet | Microsoft.Data.OData |
| .NET; NuGet | Microsoft.Data.Services.Client |
| .NET; NuGet | Microsoft.IdentityModel.Clients.ActiveDirectory |
| .NET        | Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms<br>(Visual Studio 2015 tylko) |
| .NET; NuGet | System.Spatial |

Zostaną usunięte następujące odwołania (ASP.NET 4 projektów, jak w programie Visual Studio 2015):

| Typ | Informacje ogólne |
| --- | --- |
| .NET; NuGet | Microsoft.AspNet.Identity.Core |
| .NET; NuGet | Microsoft.AspNet.Identity.EntityFramework |
| .NET; NuGet | Microsoft.AspNet.Identity.Owin |

## <a name="project-file-changes"></a>Zmiany w pliku projektu

- Ustaw właściwość `IISExpressSSLPort` różne liczby.
- Ustaw właściwość `WebProject_DirectoryAccessLevelKey` 0 lub 1 w przypadku wybrania **odczytuj dane katalogu** opcji.
- Ustaw właściwość `IISUrl` do `https://localhost:<port>/` gdzie `<port>` odpowiada `IISExpressSSLPort` wartość.

## <a name="webconfig-or-appconfig-changes"></a>zmiany w pliku Web.config lub app.config

- Dodano następujące pozycje konfiguracji:

    ```xml
    <appSettings>
        <add key="ida:ClientId" value="<ClientId from the new Azure AD app>" />
        <add key="ida:Tenant" value="<your selected Azure domain>" />
        <add key="ida:Audience" value="<your selected domain + / + project name>" />
    </appSettings>
    ```

- Visual Studio 2017 r z tylko: dodano także następujący wpis w obszarze `<appSettings>`"

    ```xml
    <add key="ida:MetadataAddress" value="<domain URL + /federationmetadata/2007-06/federationmetadata.xml>" />
    ```

- Dodaje `<dependentAssembly>` elementy w obszarze `<runtime><assemblyBinding>` węzeł `System.IdentityModel.Tokens.Jwt`.

- W przypadku wybrania **odczytuj dane katalogu** , należy dodać następujący wpis konfiguracyjny w obszarze `<appSettings>`:

    ```xml
    <add key="ida:Password" value="<Your Azure AD app's new password>" />
    ```

## <a name="code-changes-and-additions"></a>Zmiany kodu

- Dodaje `[Authorize]` atrybutu `Controllers/ValueController.cs` i istniejących kontrolerów.

- Dodaje klasę uruchamiania uwierzytelniania `App_Start/Startup.Auth.cs`, zawierający Logika uruchamiania dla uwierzytelniania usługi Azure AD lub odpowiednio zmienione. W przypadku wybrania **odczytuj dane katalogu** opcji, ten plik zawiera także kod, aby otrzymać kod OAuth i exchange on dla tokenu dostępu.

- (Visual Studio 2015 z platformy ASP.NET 4 tylko aplikacji) Usunięte `App_Start/IdentityConfig.cs` i dodać `Controllers/AccountController.cs`, `Models/IdentityModel.cs`, i `Providers/ApplicationAuthProvider.cs`.

- Dodaje `Connected Services/AzureAD/ConnectedService.json` (Visual Studio 2017 r) lub `Service References/Azure AD/ConnectedService.json` (Visual Studio 2015), zawierający informacje używanym przez program Visual Studio do śledzenia Dodawanie podłączonej usługi.

### <a name="file-backup-visual-studio-2015"></a>Tworzenie kopii zapasowej plików (Visual Studio 2015)

Podczas dodawania podłączonej usługi, Visual Studio 2015 tworzy kopię zapasową plików zmienione i usunięte. Wszystkie pliki dotyczy są zapisywane w folderze `Backup/AzureAD`. Visual Studio 2017 nie tworzy kopie zapasowe.

- `Startup.cs`
- `App_Start\IdentityConfig.cs`
- `App_Start\Startup.Auth.cs`
- `Controllers\AccountController.cs`
- `Controllers\ManageController.cs`
- `Models\IdentityModels.cs`
- `Models\ApplicationOAuthProvider.cs`

## <a name="changes-on-azure"></a>Zmiany na platformie Azure

- Utworzona aplikacja Azure AD w domenie wybranego podczas dodawania podłączonej usługi.
- Zaktualizowano aplikację do uwzględnienia **odczytuj dane katalogu** uprawnienia, jeśli ta opcja została wybrana.

[Dowiedz się więcej o usłudze Azure Active Directory](https://azure.microsoft.com/services/active-directory/).

## <a name="next-steps"></a>Kolejne kroki

- [Scenariusze uwierzytelniania usługi Azure Active Directory](active-directory-authentication-scenarios.md)
- [Dodaj logowanie z firmy Microsoft do aplikacji sieci web platformy ASP.NET](guidedsetups/active-directory-aspnetwebapp-v1.md)