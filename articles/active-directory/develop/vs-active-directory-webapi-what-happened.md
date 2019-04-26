---
title: Zmiany wprowadzone do projektów WebAPI, po nawiązaniu połączenia z usługą Azure AD
description: W tym artykule opisano, co się dzieje z projektem WebAPI, po nawiązaniu połączenia z usługą Azure AD za pomocą programu Visual Studio
services: active-directory
author: ghogen
manager: douge
ms.assetid: 57630aee-26a2-4326-9dbb-ea2a66daa8b0
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 03/12/2018
ms.author: ghogen
ms.custom: aaddev, vs-azure
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7f4946251cf72d7869ec5fc2f0fd844b9c06ac34
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60353282"
---
# <a name="what-happened-to-my-webapi-project-visual-studio-azure-active-directory-connected-service"></a>Co się stało z moim projektem WebAPI (usługa połączona programu Visual Studio usługi Azure Active Directory)

> [!div class="op_single_selector"]
> - [Wprowadzenie](vs-active-directory-webapi-getting-started.md)
> - [Co się stało](vs-active-directory-webapi-what-happened.md)

W tym artykule identyfikuje dokładne zmiany wprowadzone do projektów ASP.NET WebAPI, aplikacji jednej strony ASP.NET i ASP.NET interfejsu API usługi Azure, podczas dodawania [usługi Azure Active Directory połączone usługi przy użyciu programu Visual Studio](vs-active-directory-add-connected-service.md). Również ma zastosowanie do projektów usługi mobilnej Azure ASP.NET w programie Visual Studio 2015.

Aby uzyskać informacje na temat pracy z podłączoną usługę, zobacz [wprowadzenie](vs-active-directory-webapi-getting-started.md).

## <a name="added-references"></a>Dodano odwołania

Ma wpływ na odwołania *.NET pliku projektu) i `packages.config` (odwołań NuGet).

| Type | Informacje ogólne |
| --- | --- |
| .NET; NuGet | Microsoft.Owin |
| .NET; NuGet | Microsoft.Owin.Host.SystemWeb |
| .NET; NuGet | Microsoft.Owin.Security |
| .NET; NuGet | Microsoft.Owin.Security.ActiveDirectory |
| .NET; NuGet | Microsoft.Owin.Security.Jwt |
| .NET; NuGet | Microsoft.Owin.Security.OAuth |
| .NET; NuGet | Owin |
| .NET; NuGet | System.IdentityModel.Tokens.Jwt |

Dodatkowe informacje, w przypadku wybrania **Odczyt danych katalogu** opcji:

| Type | Informacje ogólne |
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

Zostaną usunięte następujące odwołania (ASP.NET 4 wyłącznie dla projektów, tak jak w programie Visual Studio 2015):

| Type | Informacje ogólne |
| --- | --- |
| .NET; NuGet | Microsoft.AspNet.Identity.Core |
| .NET; NuGet | Microsoft.AspNet.Identity.EntityFramework |
| .NET; NuGet | Microsoft.AspNet.Identity.Owin |

## <a name="project-file-changes"></a>Zmiany w plikach projektu

- Ustaw właściwość `IISExpressSSLPort` do liczby różne.
- Ustaw właściwość `WebProject_DirectoryAccessLevelKey` na 0 lub 1 w przypadku wybrania **Odczyt danych katalogu** opcji.
- Ustaw właściwość `IISUrl` do `https://localhost:<port>/` gdzie `<port>` odpowiada `IISExpressSSLPort` wartość.

## <a name="webconfig-or-appconfig-changes"></a>plik Web.config lub app.config zmiany

- Dodano następujące pozycje konfiguracji:

    ```xml
    <appSettings>
        <add key="ida:ClientId" value="<ClientId from the new Azure AD app>" />
        <add key="ida:Tenant" value="<your selected Azure domain>" />
        <add key="ida:Audience" value="<your selected domain + / + project name>" />
    </appSettings>
    ```

- Program Visual Studio 2017 tylko: Dodano także następujący wpis w obszarze `<appSettings>`"

    ```xml
    <add key="ida:MetadataAddress" value="<domain URL + /federationmetadata/2007-06/federationmetadata.xml>" />
    ```

- Dodano `<dependentAssembly>` elementy w obszarze `<runtime><assemblyBinding>` węzeł `System.IdentityModel.Tokens.Jwt`.

- W przypadku wybrania **Odczyt danych katalogu** opcji, dodano następujący wpis konfiguracji w ramach `<appSettings>`:

    ```xml
    <add key="ida:Password" value="<Your Azure AD app's new password>" />
    ```

## <a name="code-changes-and-additions"></a>Zmiany kodu i uzupełnień

- Dodano `[Authorize]` atrybutu `Controllers/ValueController.cs` i istniejących kontrolerów.

- Dodaje klasę początkową uwierzytelniania `App_Start/Startup.Auth.cs`, zawierający Logika uruchamiania uwierzytelniania usługi Azure AD lub odpowiednio modyfikowane. W przypadku wybrania **Odczyt danych katalogu** opcji ten plik zawiera także kod, aby otrzymać kod OAuth i jego wymiany dla tokenu dostępu.

- (Program visual Studio 2015 za pomocą platformy ASP.NET 4 tylko aplikacja) Usunięte `App_Start/IdentityConfig.cs` i dodać `Controllers/AccountController.cs`, `Models/IdentityModel.cs`, i `Providers/ApplicationAuthProvider.cs`.

- Dodano `Connected Services/AzureAD/ConnectedService.json` (Visual Studio 2017) lub `Service References/Azure AD/ConnectedService.json` (Visual Studio 2015), zawierające informacje, korzystającą z programu Visual Studio w celu śledzić Dodawanie usługi połączonej.

### <a name="file-backup-visual-studio-2015"></a>Tworzenie kopii zapasowej plików (Visual Studio 2015)

Podczas dodawania usługi połączonej, Visual Studio 2015 tworzy kopie zapasowe zmienionych i wykasowane pliki. Wszystkie pliki objęte są zapisywane w folderze `Backup/AzureAD`. Program Visual Studio 2017 nie tworzy kopii zapasowych.

- `Startup.cs`
- `App_Start\IdentityConfig.cs`
- `App_Start\Startup.Auth.cs`
- `Controllers\AccountController.cs`
- `Controllers\ManageController.cs`
- `Models\IdentityModels.cs`
- `Models\ApplicationOAuthProvider.cs`

## <a name="changes-on-azure"></a>Zmiany na platformie Azure

- Utworzyć aplikację usługi Azure AD w domenie, która wybrane podczas dodawania usługi połączonej.
- Zaktualizowano aplikację, uwzględniając **Odczyt danych katalogu** uprawnienia, jeśli wybrano tę opcję.

[Dowiedz się więcej o usłudze Azure Active Directory](https://azure.microsoft.com/services/active-directory/).

## <a name="next-steps"></a>Kolejne kroki

- [Scenariusze uwierzytelniania dla usługi Azure Active Directory](authentication-scenarios.md)
- [Dodawanie logowania z firmą Microsoft do aplikacji sieci web platformy ASP.NET](quickstart-v1-aspnet-webapp.md)
