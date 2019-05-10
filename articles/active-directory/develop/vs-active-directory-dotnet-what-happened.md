---
title: Zmiany wprowadzone do projektu MVC po nawiązaniu połączenia z usługą Azure AD
description: W tym artykule opisano, co się dzieje z projektem MVC, po nawiązaniu połączenia z usługą Azure AD przy użyciu usług połączonych programu Visual Studio
services: active-directory
author: ghogen
manager: douge
ms.assetid: 8b24adde-547e-4ffe-824a-2029ba210216
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 03/12/2018
ms.author: ghogen
ms.custom: aaddev, vs-azure
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1e40a1fd72adf353d5c4d856544482cb7bd68f43
ms.sourcegitcommit: 300cd05584101affac1060c2863200f1ebda76b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2019
ms.locfileid: "65413988"
---
# <a name="what-happened-to-my-mvc-project-visual-studio-azure-active-directory-connected-service"></a>Co się stało z moim projektem MVC (usługa połączona programu Visual Studio usługi Azure Active Directory)?

> [!div class="op_single_selector"]
> - [Wprowadzenie](vs-active-directory-dotnet-getting-started.md)
> - [Co się stało](vs-active-directory-dotnet-what-happened.md)

W tym artykule identyfikuje dokładne zmiany wprowadzone do projektów platformy ASP.NET MVC, podczas dodawania am [usługi Azure Active Directory połączone usługi przy użyciu programu Visual Studio](vs-active-directory-add-connected-service.md).

Aby uzyskać informacje na temat pracy z podłączoną usługę, zobacz [wprowadzenie](vs-active-directory-dotnet-getting-started.md).

## <a name="added-references"></a>Dodano odwołania

Ma wpływ na odwołania *.NET pliku projektu) i `packages.config` (odwołań NuGet).

| Type | Odwołanie |
| --- | --- |
| .NET; NuGet | Microsoft.IdentityModel.Protocol.Extensions |
| .NET; NuGet | Microsoft.Owin |
| .NET; NuGet | Microsoft.Owin.Host.SystemWeb |
| .NET; NuGet | Microsoft.Owin.Security |
| .NET; NuGet | Microsoft.Owin.Security.Cookies |
| .NET; NuGet | Microsoft.Owin.Security.OpenIdConnect |
| .NET; NuGet | Owin |
| .NET        | System.IdentityModel |
| .NET; NuGet | System.IdentityModel.Tokens.Jwt |
| .NET        | System.Runtime.Serialization |

Dodatkowe informacje, w przypadku wybrania **Odczyt danych katalogu** opcji:

| Type | Odwołanie |
| --- | --- |
| .NET; NuGet | EntityFramework |
| .NET        | EntityFramework.SqlServer (Visual Studio 2015 tylko) |
| .NET; NuGet | Microsoft.Azure.ActiveDirectory.GraphClient |
| .NET; NuGet | Microsoft.Data.Edm |
| .NET; NuGet | Microsoft.Data.OData |
| .NET; NuGet | Microsoft.Data.Services.Client |
| .NET; NuGet | Microsoft.IdentityModel.Clients.ActiveDirectory |
| .NET        | Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms (Visual Studio 2015 only) |
| .NET; NuGet | System.Spatial |

Zostaną usunięte następujące odwołania (ASP.NET 4 wyłącznie dla projektów, tak jak w programie Visual Studio 2015):

| Type | Odwołanie |
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
        <add key="ida:AADInstance" value="https://login.microsoftonline.com/" />
        <add key="ida:Domain" value="<your selected Azure domain>" />
        <add key="ida:TenantId" value="<the Id of your selected Azure AD tenant>" />
        <add key="ida:PostLogoutRedirectUri" value="<project start page, such as https://localhost:44335>" />
    </appSettings>
    ```

- Dodano `<dependentAssembly>` elementy w obszarze `<runtime><assemblyBinding>` węzeł `System.IdentityModel.Tokens.Jwt` i `Microsoft.IdentityModel.Protocol.Extensions`.

Dodatkowe zmiany w przypadku wybrania **Odczyt danych katalogu** opcji:

- Dodano następujący wpis konfiguracji w ramach `<appSettings>`:

    ```xml
    <add key="ida:ClientSecret" value="<Azure AD app's new client secret>" />
    ```

- Dodano następujące elementy w obszarze `<configuration>`; wartości różnią się pliku mdf projektu i identyfikator w przypadku katalogu projektu:

    ```xml
    <configSections>
      <!-- For more information on Entity Framework configuration, visit https://go.microsoft.com/fwlink/?LinkID=237468 -->
      <section name="entityFramework" type="System.Data.Entity.Internal.ConfigFile.EntityFrameworkSection, EntityFramework, Version=6.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089" requirePermission="false" />
    </configSections>

    <connectionStrings>
      <add name="DefaultConnection" connectionString="Data Source=(localdb)\MSSQLLocalDB;AttachDbFilename=|DataDirectory|\<project-mdf-file>.mdf;Initial Catalog=<project-catalog-id>;Integrated Security=True" providerName="System.Data.SqlClient" />
    </connectionStrings>

    <entityFramework>
      <defaultConnectionFactory type="System.Data.Entity.Infrastructure.LocalDbConnectionFactory, EntityFramework">
        <parameters>
          <parameter value="mssqllocaldb" />
        </parameters>
      </defaultConnectionFactory>
      <providers>
        <provider invariantName="System.Data.SqlClient" type="System.Data.Entity.SqlServer.SqlProviderServices, EntityFramework.SqlServer" />
      </providers>
    </entityFramework>
    ```

- Dodano `<dependentAssembly>` elementy w obszarze `<runtime><assemblyBinding>` węzeł `Microsoft.Data.Services.Client`, `Microsoft.Data.Edm`, i `Microsoft.Data.OData`.

## <a name="code-changes-and-additions"></a>Zmiany kodu i uzupełnień

- Dodano `[Authorize]` atrybutu `Controllers/HomeController.cs` i istniejących kontrolerów.

- Dodaje klasę początkową uwierzytelniania `App_Start/Startup.Auth.cs`, zawierający Logika uruchamiania uwierzytelniania usługi Azure AD. W przypadku wybrania **Odczyt danych katalogu** opcji ten plik zawiera także kod, aby otrzymać kod OAuth i jego wymiany dla tokenu dostępu.

- Dodaje klasę kontrolera `Controllers/AccountController.cs`, zawierającego `SignIn` i `SignOut` metody.

- Dodano widok częściowy `Views/Shared/_LoginPartial.cshtml`, zawierającą łącze akcji dla `SignIn` i `SignOut`.

- Dodano widok częściowy `Views/Account/SignoutCallback.cshtml`, zawierający kod HTML dla interfejsu wylogowania użytkownika.

- Zaktualizowano `Startup.Configuration` metodę, aby dołączyć wywołanie `ConfigureAuth(app)` Jeśli klasa już istnieje; w przeciwnym razie dodane `Startup` klasa, która zawiera wywołania metody.

- Dodano `Connected Services/AzureAD/ConnectedService.json` (Visual Studio 2017) lub `Service References/Azure AD/ConnectedService.json` (Visual Studio 2015), zawierające informacje, korzystającą z programu Visual Studio w celu śledzić Dodawanie usługi połączonej.

- W przypadku wybrania **Odczyt danych katalogu** opcji dodano `Models/ADALTokenCache.cs` i `Models/ApplicationDbContext.cs` do obsługi buforowaniem tokena. Dodano także dodatkowy kontroler i widok, aby zilustrować uzyskiwania dostępu do informacji o profilu użytkownika przy użyciu programu Azure graph API: `Controllers/UserProfileController.cs`, `Views/UserProfile/Index.cshtml`, i `Views/UserProfile/Relogin.cshtml`

### <a name="file-backup-visual-studio-2015"></a>Tworzenie kopii zapasowej plików (Visual Studio 2015)

Podczas dodawania usługi połączonej, Visual Studio 2015 tworzy kopie zapasowe zmienionych i wykasowane pliki. Wszystkie pliki objęte są zapisywane w folderze `Backup/AzureAD`. Visual Studio 2017 i nowszym nie tworzy kopii zapasowych.

- `Startup.cs`
- `App_Start\IdentityConfig.cs`
- `App_Start\Startup.Auth.cs`
- `Controllers\AccountController.cs`
- `Controllers\ManageController.cs`
- `Models\IdentityModels.cs`
- `Models\ManageViewModels.cs`
- `Views\Shared\_LoginPartial.cshtml`

## <a name="changes-on-azure"></a>Zmiany na platformie Azure

- Utworzyć aplikację usługi Azure AD w domenie, która wybrane podczas dodawania usługi połączonej.
- Zaktualizowano aplikację, uwzględniając **Odczyt danych katalogu** uprawnienia, jeśli wybrano tę opcję.

[Dowiedz się więcej o usłudze Azure Active Directory](https://azure.microsoft.com/services/active-directory/).

## <a name="next-steps"></a>Kolejne kroki

- [Scenariusze uwierzytelniania dla usługi Azure Active Directory](authentication-scenarios.md)
- [Dodawanie logowania z firmą Microsoft do aplikacji sieci web platformy ASP.NET](quickstart-v1-aspnet-webapp.md)
