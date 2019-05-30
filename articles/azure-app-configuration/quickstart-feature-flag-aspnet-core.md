---
title: 'Szybki Start: Dodawanie flag funkcji do platformy ASP.NET Core | Dokumentacja firmy Microsoft'
description: 'Szybki Start: Dodawanie flag funkcji do aplikacji platformy ASP.NET Core i zarządzanie nimi w konfiguracji aplikacji platformy Azure'
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
ms.openlocfilehash: f83283dbf2a78b3717ba719f21dd9249a8a09d1f
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/30/2019
ms.locfileid: "66393295"
---
# <a name="quickstart-add-feature-flags-to-an-aspnet-core-app"></a>Szybki start: Dodawanie flag funkcji do aplikacji platformy ASP.NET Core

Przy łączeniu aplikacji z konfiguracji aplikacji platformy Azure, można włączyć funkcji zarządzania w programie ASP.NET Core. Służy to zarządzana Usługa do przechowywania wszystkie flagi funkcji i centralnie kontrolować ich Stany. Ten przewodnik Szybki Start pokazano, jak włączanie konfiguracji aplikacji do platformy ASP.NET Core aplikacji sieci web, aby utworzyć implementacja end-to-end w funkcji zarządzania.

Biblioteki .NET Core funkcja zarządzania rozszerzyć środowisko z obsługą flagi funkcji kompleksowe. Biblioteki te są tworzone na podstawie systemu konfiguracji platformy .NET Core. Mogą bezproblemowo integrują się z konfiguracji aplikacji za pośrednictwem dostawcy konfiguracji platformy .NET Core.

Wykonaj kroki w tym przewodniku Szybki Start, można użyć dowolnego edytora kodu. [Visual Studio Code](https://code.visualstudio.com/) jest doskonałą opcją dostępne w Windows, macOS i platformy Linux.

## <a name="prerequisites"></a>Wymagania wstępne

Ten przewodnik Szybki Start, instaluje [zestawu .NET Core SDK](https://dotnet.microsoft.com/download).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-an-app-configuration-store"></a>Utwórz magazyn konfiguracji aplikacji

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Wybierz **funkcji Menedżera** >  **+ Utwórz** można dodać następujących flag funkcji:

    | Klucz | Stan |
    |---|---|
    | Beta | Wyłączone |

## <a name="create-an-aspnet-core-web-app"></a>Tworzenie aplikacji internetowej ASP.NET Core

Możesz użyć [platformy .NET Core interfejsu wiersza polecenia (CLI)](https://docs.microsoft.com/dotnet/core/tools/) do utworzenia nowego projektu aplikacji sieci web platformy ASP.NET Core MVC. Zaletą używania interfejsu wiersza polecenia platformy .NET Core, zamiast programu Visual Studio jest interfejs wiersza polecenia platformy .NET Core jest dostępna w Windows, macOS i platformy Linux.

1. Utwórz nowy folder dla projektu. W tym przewodniku Szybki Start, nadaj jej nazwę *TestFeatureFlags*.

1. W nowym folderze uruchom następujące polecenie, aby utworzyć nowy projekt aplikacji sieci web platformy ASP.NET Core MVC:

   ```    
   dotnet new mvc
   ```

## <a name="add-secret-manager"></a>Dodawanie narzędzia Secret Manager

Dodaj [narzędzie Menedżer klucz tajny](https://docs.microsoft.com/aspnet/core/security/app-secrets) do projektu. Narzędzie Menedżer klucz tajny przechowuje danymi poufnymi w przypadku tworzonym spoza Twojej drzewa projektu. Takie podejście zapobiega przypadkowemu ujawnieniu wpisów tajnych aplikacji w kodzie źródłowym.

1. Otwórz *.csproj* pliku.
1. Dodaj `UserSecretsId` elementu, jak pokazano w poniższym przykładzie i zastąp jego wartość swoją własną, który zazwyczaj jest identyfikatorem GUID:

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

## <a name="connect-to-an-app-configuration-store"></a>Łączenie z magazynem konfiguracji aplikacji

1. Dodaj odwołania do `Microsoft.Extensions.Configuration.AzureAppConfiguration` i `Microsoft.FeatureManagement` pakietów NuGet, uruchamiając następujące polecenia:

    ```
    dotnet add package Microsoft.Extensions.Configuration.AzureAppConfiguration --version 1.0.0-preview-008520001

    dotnet add package Microsoft.FeatureManagement.AspNetCore --version 1.0.0-preview-008560001-910
    ```

1. Uruchom następujące polecenie, aby przywrócić pakiety dla projektu:

    ```
    dotnet restore
    ```

1. Dodaj wpis tajny o nazwie **ConnectionStrings:AppConfig** do narzędzia Secret Manager.

    Ten wpis tajny zawiera parametry połączenia, aby dostęp do magazynu konfiguracji aplikacji. Zastąp `<your_connection_string>` wartość w następującym poleceniu przy użyciu parametrów połączenia dla magazynu konfiguracji aplikacji.

    To polecenie należy wykonać w tym samym katalogu, w którym znajduje się plik *csproj*.

    ```
    dotnet user-secrets set ConnectionStrings:AppConfig <your_connection_string>
    ```

    Używasz klucza tajnego Manager tylko w celu testowania aplikacji sieci web w środowisku lokalnym. Podczas wdrażania aplikacji do [usługi Azure App Service](https://azure.microsoft.com/services/app-service), na przykład użyć aplikacji, ustawienie o nazwie **parametry połączenia** w usłudze App Service, zamiast używania Menedżera klucz tajny, aby przechowywać parametry połączenia.

    Możesz uzyskać dostęp do tego wpisu tajnego za pomocą interfejsu API konfiguracji aplikacji. Dwukropek (:) działanie jest nazwa konfiguracji za pomocą interfejsu API konfiguracji aplikacji na wszystkich obsługiwanych platformach. Zobacz [konfiguracji przez środowisko](https://docs.microsoft.com/aspnet/core/fundamentals/configuration).

1. Otwórz *Program.cs*i Dodaj odwołanie do dostawcy konfiguracji aplikacji programu .NET Core:

    ```csharp
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

1. Aktualizacja `CreateWebHostBuilder` metodę, aby można było używać konfiguracji aplikacji, wywołując `config.AddAzureAppConfiguration()` metody.

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

1. Otwórz *Startup.cs*i dodaj odwołania do Menedżera funkcji platformy .NET Core:

    ```csharp
    using Microsoft.FeatureManagement.AspNetCore;
    ```

1. Aktualizacja `ConfigureServices` metody w celu dodania flagi funkcji obsługi przez wywołanie metody `services.AddFeatureManagement()` metody. Opcjonalnie można dodać filtr, który ma być używana przy użyciu flag funkcji przez wywołanie metody `services.AddFeatureFilter<FilterType>()`:

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddFeatureManagement();
    }
    ```

1. Dodaj *MyFeatureFlags.cs* pliku:

    ```csharp
    namespace TestFeatureFlags
    {
        public enum MyFeatureFlags
        {
            Beta
        }
    }
    ```

1. Dodaj *BetaController.cs* do *kontrolerów* katalogu:

    ```csharp
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.FeatureManagement.AspNetCore;

    namespace TestFeatureFlags.Controllers
    {
        public class BetaController: Controller
        {
            private readonly IFeatureManager _featureManager;

            public BetaController(IFeatureManagerSnapshot featureManager)
            {
                _featureManager = featureManager;
            }

            [Feature(MyFeatureFlags.Beta)]
            public IActionResult Index()
            {
                return View();
            }
        }
    }
    ```

1. Otwórz *_ViewImports.cshtml* w *widoków* katalogu i Dodaj Pomocnik tagu Menedżer funkcji:

    ```html
    @addTagHelper *, Microsoft.FeatureManagement.AspNetCore
    ```

1. Otwórz *_Layout.cshtml* w *widoków*\\*Shared* katalogu i Zastąp `<nav>` kodu kreskowego w obszarze `<body>`  >  `<header>` następującym kodem:

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

1. Tworzenie *Beta* katalogu w ramach *widoków* i Dodaj *Index.cshtml* do niego:

    ```html
    @{
        ViewData["Title"] = "Beta Home Page";
    }

    <h1>
        This is the beta website.
    </h1>
    ```

## <a name="build-and-run-the-app-locally"></a>Lokalne kompilowanie i uruchamianie aplikacji

1. Aby skompilować aplikację przy użyciu interfejsu wiersza polecenia platformy .NET Core, uruchom następujące polecenie w powłoce poleceń:

    ```
    dotnet build
    ```

1. Po pomyślnym zakończeniu kompilacji, uruchom następujące polecenie, aby lokalnie uruchomić aplikację sieci web:

    ```
    dotnet run
    ```

1. Otwórz okno przeglądarki i przejdź do `https://localhost:5001`, która jest domyślny adres URL aplikacji sieci web hostowanych lokalnie.

    ![Lokalne uruchamianie aplikacji z przewodnika Szybki start](./media/quickstarts/aspnet-core-feature-flag-local-before.png)

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Wybierz **wszystkie zasoby**i wybierz wystąpienie magazynu konfiguracji aplikacji, utworzonej w przewodniku Szybki Start.

1. Wybierz **funkcji Menedżera**i Zmień stan **Beta** klucza **na**:

    | Klucz | Stan |
    |---|---|
    | Beta | Włączone |

1. Odśwież stronę przeglądarki, aby zobaczyć nowe ustawienia konfiguracji.

    ![Lokalne uruchamianie aplikacji z przewodnika Szybki start](./media/quickstarts/aspnet-core-feature-flag-local-after.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Kolejne kroki

W tym przewodniku Szybki Start utworzono nowy magazyn konfiguracji aplikacji i umożliwia zarządzanie funkcjami w aplikacji sieci web platformy ASP.NET Core za pomocą [biblioteki zarządzania funkcją](https://go.microsoft.com/fwlink/?linkid=2074664).

- Dowiedz się więcej o [funkcji zarządzania](./concept-feature-management.md).
- [Zarządzanie flagami funkcji](./manage-feature-flags.md).
- [Użyj flagi funkcji w aplikacji ASP.NET Core](./use-feature-flags-dotnet-core.md).
