---
title: Przewodnik Szybki Start dotyczący dodawania flag funkcji do ASP.NET Core | Microsoft Docs
description: Przewodnik Szybki Start dotyczący dodawania flag funkcji do ASP.NET Core aplikacji i zarządzania nimi w konfiguracji aplikacji platformy Azure
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: csharp
ms.topic: quickstart
ms.tgt_pltfrm: ASP.NET Core
ms.workload: tbd
ms.date: 04/19/2019
ms.author: yegu
ms.openlocfilehash: 41947de18ae27b41d046fd4358f0039a1b56374d
ms.sourcegitcommit: e72073911f7635cdae6b75066b0a88ce00b9053b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/19/2019
ms.locfileid: "68347912"
---
# <a name="quickstart-add-feature-flags-to-an-aspnet-core-app"></a>Szybki start: Dodawanie flag funkcji do aplikacji ASP.NET Core

W tym przewodniku szybki start dołączysz konfigurację aplikacji platformy Azure do aplikacji sieci Web ASP.NET Core, aby utworzyć kompleksową implementację zarządzania funkcjami. Za pomocą usługi konfiguracji aplikacji można centralnie przechowywać wszystkie flagi funkcji i kontrolować ich Stany. 

Biblioteki zarządzania funkcjami platformy .NET Core poszerzają platformę Dzięki kompleksowemu obsłudze flagi funkcji. Te biblioteki są zbudowane na podstawie systemu konfiguracji platformy .NET Core. Zapewniają one bezproblemowe integrację z konfiguracją aplikacji za pomocą dostawcy konfiguracji platformy .NET Core.

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/)
- [Zestaw .NET Core SDK](https://dotnet.microsoft.com/download).

## <a name="create-an-app-configuration-store"></a>Tworzenie magazynu konfiguracji aplikacji

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Wybierz pozycję **Menedżer** > funkcji **+ Utwórz** , aby dodać następujące flagi funkcji:

    | Klucz | Stan |
    |---|---|
    | Wersja beta | Wyłączone |

## <a name="create-an-aspnet-core-web-app"></a>Tworzenie aplikacji internetowej ASP.NET Core

Aby utworzyć nowy projekt aplikacji sieci Web MVC ASP.NET Core, należy użyć [interfejsu wiersza polecenia platformy .NET Core](https://docs.microsoft.com/dotnet/core/tools/) . Zaletą korzystania z interfejs wiersza polecenia platformy .NET Core zamiast programu Visual Studio jest to, że interfejs wiersza polecenia platformy .NET Core jest dostępny na platformach Windows, macOS i Linux.

1. Utwórz nowy folder dla projektu. W tym przewodniku szybki start nadaj mu nazwę *TestFeatureFlags*.

1. W nowym folderze Uruchom następujące polecenie, aby utworzyć nowy projekt aplikacji sieci Web ASP.NET Core MVC:

   ```    
   dotnet new mvc
   ```

## <a name="add-secret-manager"></a>Dodawanie narzędzia Secret Manager

Dodaj do projektu [Narzędzie do zarządzania kluczami tajnymi](https://docs.microsoft.com/aspnet/core/security/app-secrets) . Narzędzie Secret Manager przechowuje dane poufne do pracy programistycznej poza drzewem projektu. Takie podejście zapobiega przypadkowemu ujawnieniu wpisów tajnych aplikacji w kodzie źródłowym.

1. Otwórz plik *. csproj* .
1. `UserSecretsId` Dodaj element, jak pokazano w poniższym przykładzie, i Zastąp jego wartość własnym, który zazwyczaj jest identyfikatorem GUID:

    ```xml
    <Project Sdk="Microsoft.NET.Sdk.Web">

    <PropertyGroup>
        <TargetFramework>netcoreapp2.1</TargetFramework>
        <UserSecretsId>79a3edd0-2092-40a2-a04d-dcb46d5ca9ed</UserSecretsId>
    </PropertyGroup>

    <ItemGroup>
        <PackageReference Include="Microsoft.AspNetCore.App" />
        <PackageReference Include="Microsoft.AspNetCore.Razor.Design" Version="2.1.2" PrivateAssets="All" />
    </ItemGroup>

    </Project>
    ```

1. Zapisz plik.

## <a name="connect-to-an-app-configuration-store"></a>Nawiązywanie połączenia z magazynem konfiguracji aplikacji

1. Dodaj odwołanie do `Microsoft.Azure.AppConfiguration.AspNetCore` pakietu NuGet, uruchamiając następujące polecenie:

    ```
    dotnet add package Microsoft.Azure.AppConfiguration.AspNetCore --version 2.0.0-preview-009200001-7
    ```

1. Uruchom następujące polecenie, aby przywrócić pakiety dla projektu:

    ```
    dotnet restore
    ```

1. Dodaj wpis tajny o nazwie **ConnectionStrings:AppConfig** do narzędzia Secret Manager.

    Ten wpis tajny zawiera parametry połączenia w celu uzyskania dostępu do magazynu konfiguracji aplikacji. Zastąp `<your_connection_string>` wartość w poniższym poleceniu parametrami połączenia dla magazynu konfiguracji aplikacji.

    To polecenie należy wykonać w tym samym katalogu, w którym znajduje się plik *csproj*.

    ```
    dotnet user-secrets set ConnectionStrings:AppConfig <your_connection_string>
    ```

    Do lokalnego testowania aplikacji sieci Web służy program Secret Manager. Po wdrożeniu aplikacji do [Azure App Service](https://azure.microsoft.com/services/app-service)można na przykład użyć ustawienia aplikacji o nazwie **parametry połączenia** w App Service zamiast używać Menedżera wpisów tajnych do przechowywania parametrów połączenia.

    Możesz uzyskać dostęp do tego wpisu tajnego za pomocą interfejsu API konfiguracji aplikacji. Dwukropek (:) działa w nazwie konfiguracji z interfejsem API konfiguracji aplikacji na wszystkich obsługiwanych platformach. Zobacz [Konfiguracja według środowiska](https://docs.microsoft.com/aspnet/core/fundamentals/configuration).

1. Otwórz *program.cs*i Dodaj odwołanie do dostawcy konfiguracji aplikacji .NET Core:

    ```csharp
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

1. Zaktualizuj metodę, aby użyć konfiguracji aplikacji przez `config.AddAzureAppConfiguration()` wywołanie metody. `CreateWebHostBuilder`

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();
                config.AddAzureAppConfiguration(options => {
                    options.Connect(settings["ConnectionStrings:AppConfig"])
                           .UseFeatureFlags();
                });
            })
            .UseStartup<Startup>();
    ```

1. Otwórz *Startup.cs*i Dodaj odwołania do programu .NET Core Feature Manager:

    ```csharp
    using Microsoft.FeatureManagement;
    ```

1. Zaktualizuj metodę, aby dodać obsługę flagi funkcji przez `services.AddFeatureManagement()` wywołanie metody. `ConfigureServices` Opcjonalnie możesz dołączyć dowolny filtr, który ma być używany z flagami funkcji przez `services.AddFeatureFilter<FilterType>()`wywołanie:

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddFeatureManagement();
    }
    ```

1. `Configure` Zaktualizuj metodę, aby dodać oprogramowanie pośredniczące, aby umożliwić odświeżanie wartości flagi funkcji w cyklicznym interwale, podczas gdy aplikacja sieci Web ASP.NET Core nadal otrzymuje żądania.

    ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        app.UseAzureAppConfiguration();
        app.UseMvc();
    }
    ```

1. Dodaj plik *MyFeatureFlags.cs* :

    ```csharp
    namespace TestFeatureFlags
    {
        public enum MyFeatureFlags
        {
            Beta
        }
    }
    ```

1. Dodaj *BetaController.cs* do katalogu *controllers* :

    ```csharp
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.FeatureManagement;
    using Microsoft.FeatureManagement.Mvc;

    namespace TestFeatureFlags.Controllers
    {
        public class BetaController: Controller
        {
            private readonly IFeatureManager _featureManager;

            public BetaController(IFeatureManagerSnapshot featureManager)
            {
                _featureManager = featureManager;
            }

            [FeatureGate(MyFeatureFlags.Beta)]
            public IActionResult Index()
            {
                return View();
            }
        }
    }
    ```

1. Otwórz *_ViewImports. cshtml* w katalogu *widoków* i Dodaj pomocnika tagów programu Feature Manager:

    ```html
    @addTagHelper *, Microsoft.FeatureManagement.AspNetCore
    ```

1. Otwórz *_Layout. cshtml* w udostępnionym katalogu *widoki*\\ i Zastąp `<nav>` kod paska pod `<body>`  >  `<header>` następującym kodem:

    ```html
    <nav class="navbar navbar-expand-sm navbar-toggleable-sm navbar-light bg-white border-bottom box-shadow mb-3">
        <div class="container">
            <a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">TestFeatureFlags</a>
            <button class="navbar-toggler" type="button" data-toggle="collapse" data-target=".navbar-collapse" aria-controls="navbarSupportedContent"
            aria-expanded="false" aria-label="Toggle navigation">
            <span class="navbar-toggler-icon"></span>
            </button>
            <div class="navbar-collapse collapse d-sm-inline-flex flex-sm-row-reverse">
                <ul class="navbar-nav flex-grow-1">
                    <li class="nav-item">
                        <a class="nav-link text-dark" asp-area="" asp-controller="Home" asp-action="Index">Home</a>
                    </li>
                    <feature name="Beta">
                    <li class="nav-item">
                        <a class="nav-link text-dark" asp-area="" asp-controller="Beta" asp-action="Index">Beta</a>
                    </li>
                    </feature>
                    <li class="nav-item">
                        <a class="nav-link text-dark" asp-area="" asp-controller="Home" asp-action="Privacy">Privacy</a>
                    </li>
                </ul>
            </div>
        </div>
    </nav>
    ```

1. Utwórz katalog *beta* w obszarze *widoki* i Dodaj do niego plik *index. cshtml* :

    ```html
    @{
        ViewData["Title"] = "Beta Home Page";
    }

    <h1>
        This is the beta website.
    </h1>
    ```

## <a name="build-and-run-the-app-locally"></a>Lokalne kompilowanie i uruchamianie aplikacji

1. Aby skompilować aplikację przy użyciu interfejs wiersza polecenia platformy .NET Core, uruchom następujące polecenie w powłoce poleceń:

    ```
    dotnet build
    ```

1. Po pomyślnym zakończeniu kompilacji Uruchom następujące polecenie, aby uruchomić aplikację sieci Web lokalnie:

    ```
    dotnet run
    ```

1. Otwórz okno przeglądarki i przejdź do `https://localhost:5001`, który jest domyślnym adresem URL aplikacji sieci Web hostowanej lokalnie.

    ![Lokalne uruchamianie aplikacji z przewodnika Szybki start](./media/quickstarts/aspnet-core-feature-flag-local-before.png)

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Wybierz pozycję **wszystkie zasoby**, a następnie wybierz wystąpienie magazynu konfiguracji aplikacji utworzone w ramach przewodnika Szybki Start.

1. Wybierz pozycję **Menedżer funkcji**, a następnie Zmień stan klucza **beta** **na:**

    | Klucz | Stan |
    |---|---|
    | Wersja beta | Włączone |

1. Odśwież stronę przeglądarki, aby zobaczyć nowe ustawienia konfiguracji.

    ![Lokalne uruchamianie aplikacji z przewodnika Szybki start](./media/quickstarts/aspnet-core-feature-flag-local-after.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Kolejne kroki

W tym przewodniku szybki start utworzono nowy magazyn konfiguracji aplikacji, który będzie używany do zarządzania funkcjami w ASP.NET Core aplikacji sieci Web za pośrednictwem [bibliotek zarządzania](https://go.microsoft.com/fwlink/?linkid=2074664)funkcjami.

- Dowiedz się więcej o [zarządzaniu funkcjami](./concept-feature-management.md).
- [Zarządzaj flagami funkcji](./manage-feature-flags.md).
- [Użyj flag funkcji w aplikacji ASP.NET Core](./use-feature-flags-dotnet-core.md).
