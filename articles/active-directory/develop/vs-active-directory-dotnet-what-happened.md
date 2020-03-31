---
title: Zmiany wprowadzone w projekcie MVC podczas łączenia się z usługą Azure AD
description: W tym artykule opisano, co dzieje się z projektem MVC podczas łączenia się z usługą Azure AD przy użyciu połączonych usług programu Visual Studio
author: ghogen
manager: jillfra
ms.assetid: 8b24adde-547e-4ffe-824a-2029ba210216
ms.prod: visual-studio-windows
ms.technology: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 03/12/2018
ms.author: ghogen
ms.custom: aaddev, vs-azure
ms.openlocfilehash: ac38adba4ca0d978dd48a546bed1b1faf4fe40fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79036955"
---
# <a name="what-happened-to-my-mvc-project-visual-studio-azure-active-directory-connected-service"></a>Co się stało z moim projektem MVC (usługa połączona z usługą Visual Studio Azure Active Directory)?

> [!div class="op_single_selector"]
> - [Wprowadzenie](vs-active-directory-dotnet-getting-started.md)
> - [Co się stało](vs-active-directory-dotnet-what-happened.md)

W tym artykule identyfikowane są dokładne zmiany wprowadzone w projekcie ASP.NET MVC podczas dodawania [połączonej usługi Azure Active Directory przy użyciu programu Visual Studio.](vs-active-directory-add-connected-service.md)

Aby uzyskać informacje na temat pracy z połączoną usługą, zobacz [Wprowadzenie](vs-active-directory-dotnet-getting-started.md).

## <a name="added-references"></a>Dodano referencje

Wpływa na plik projektu (odwołania*.NET) i `packages.config` (odwołania NuGet).

| Typ | Tematy pomocy |
| --- | --- |
| .NET; Nuget | Microsoft.IdentityModel.Protocol.Extensions |
| .NET; Nuget | Microsoft.Owin |
| .NET; Nuget | Microsoft.Owin.Host.SystemWeb |
| .NET; Nuget | Microsoft.Owin.Security |
| .NET; Nuget | Microsoft.Owin.Security.Cookies |
| .NET; Nuget | Microsoft.Owin.Security.OpenIdConnect |
| .NET; Nuget | Okręg wyborczy Owin |
| .NET        | System.identitymodel |
| .NET; Nuget | System.IdentityModel.Tokens.Jwt |
| .NET        | System.Runtime.Serialization |

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
| .NET        | Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms (tylko program Visual Studio 2015) |
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
        <add key="ida:AADInstance" value="https://login.microsoftonline.com/" />
        <add key="ida:Domain" value="<your selected Azure domain>" />
        <add key="ida:TenantId" value="<the Id of your selected Azure AD tenant>" />
        <add key="ida:PostLogoutRedirectUri" value="<project start page, such as https://localhost:44335>" />
    </appSettings>
    ```

- Dodano `<dependentAssembly>` elementy `<runtime><assemblyBinding>` pod `System.IdentityModel.Tokens.Jwt` węzłem dla i `Microsoft.IdentityModel.Protocol.Extensions`.

Dodatkowe zmiany po wybraniu opcji **Odczyt danych katalogu:**

- Dodano następujący wpis `<appSettings>`konfiguracji w:

    ```xml
    <add key="ida:ClientSecret" value="<Azure AD app's new client secret>" />
    ```

- Dodano następujące elementy `<configuration>`w obszarze ; wartości dla pliku project-mdf i project-catalog-id będą się różnić:

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

- Dodano `<dependentAssembly>` elementy `<runtime><assemblyBinding>` pod `Microsoft.Data.Services.Client`węzłem dla , `Microsoft.Data.Edm`i `Microsoft.Data.OData`.

## <a name="code-changes-and-additions"></a>Zmiany i uzupełnienia kodu

- Dodano `[Authorize]` atrybut `Controllers/HomeController.cs` i inne istniejące kontrolery.

- Dodano klasę uruchamiania `App_Start/Startup.Auth.cs`uwierzytelniania, zawierającą logikę uruchamiania dla uwierzytelniania usługi Azure AD. Jeśli wybrano opcję **Odczyt danych katalogu,** ten plik zawiera również kod do odbierania kodu OAuth i wymiany go na token dostępu.

- Dodano klasę kontrolera, `Controllers/AccountController.cs` `SignIn` zawierającą i `SignOut` metody.

- Dodano widok częściowy, `Views/Shared/_LoginPartial.cshtml`zawierający łącze akcji dla `SignIn` i `SignOut`.

- Dodano widok `Views/Account/SignoutCallback.cshtml`częściowy, zawierający kod HTML dla interfejsu użytkownika wylogowania.

- Zaktualizowano `Startup.Configuration` metodę, aby `ConfigureAuth(app)` uwzględnić wywołanie, jeśli klasa już istniała; w przeciwnym `Startup` razie dodano klasę, która zawiera wywołania metody.

- Dodano `Connected Services/AzureAD/ConnectedService.json` (Visual Studio 2017) lub `Service References/Azure AD/ConnectedService.json` (Visual Studio 2015), zawierające informacje używane przez program Visual Studio do śledzenia dodania połączonej usługi.

- Jeśli wybrano opcję Odczyt `Models/ADALTokenCache.cs` danych `Models/ApplicationDbContext.cs` **katalogu,** dodano i do obsługi buforowania tokenów. Dodano również dodatkowy kontroler i widok ilustrujący dostęp do informacji o `Controllers/UserProfileController.cs` `Views/UserProfile/Index.cshtml`profilu użytkownika przy użyciu interfejsów API wykresu platformy Azure: , i`Views/UserProfile/Relogin.cshtml`

### <a name="file-backup-visual-studio-2015"></a>Kopia zapasowa plików (Visual Studio 2015)

Podczas dodawania połączonej usługi program Visual Studio 2015 tworzą kopie zapasowe zmienionych i usuniętych plików. Wszystkie pliki, których dotyczy `Backup/AzureAD`problem, są zapisywane w folderze . Visual Studio 2017 i nowsze nie tworzy kopii zapasowych.

- `Startup.cs`
- `App_Start\IdentityConfig.cs`
- `App_Start\Startup.Auth.cs`
- `Controllers\AccountController.cs`
- `Controllers\ManageController.cs`
- `Models\IdentityModels.cs`
- `Models\ManageViewModels.cs`
- `Views\Shared\_LoginPartial.cshtml`

## <a name="changes-on-azure"></a>Zmiany na platformie Azure

- Utworzono aplikację usługi Azure AD w domenie wybranej podczas dodawania połączonej usługi.
- Zaktualizowano aplikację o uprawnienie **Odczyt danych katalogu,** jeśli ta opcja została zaznaczona.

[Dowiedz się więcej o usłudze Azure Active Directory](https://azure.microsoft.com/services/active-directory/).

## <a name="next-steps"></a>Następne kroki

- [Scenariusze uwierzytelniania dla usługi Azure Active Directory](authentication-scenarios.md)
- [dodawanie logowania przy użyciu konta Microsoft do aplikacji internetowej ASP.NET](quickstart-v2-aspnet-webapp.md)
