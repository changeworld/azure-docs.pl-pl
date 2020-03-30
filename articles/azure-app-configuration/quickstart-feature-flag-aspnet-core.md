---
title: Szybki start do dodawania flag funkcji do ASP.NET Core
description: Dodawanie flag funkcji do ASP.NET aplikacji Core i zarządzania nimi za pomocą konfiguracji aplikacji platformy Azure
author: lisaguthrie
ms.service: azure-app-configuration
ms.topic: quickstart
ms.date: 01/14/2020
ms.author: lcozzens
ms.openlocfilehash: d8582dfc796fe3e87b8bdc5be763dddfb5d0176b
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "80245416"
---
# <a name="quickstart-add-feature-flags-to-an-aspnet-core-app"></a>Szybki start: dodawanie flag funkcji do aplikacji ASP.NET Core

W tym przewodniku Szybki start utworzysz kompleksową implementację zarządzania funkcjami w aplikacji ASP.NET Core przy użyciu konfiguracji aplikacji platformy Azure. Użyjesz usługi konfiguracji aplikacji, aby centralnie przechowywać wszystkie flagi funkcji i kontrolować ich stany. 

Biblioteki zarządzania funkcjami .NET Core rozszerzają strukturę o kompleksową obsługę flag funkcji. Biblioteki te są zbudowane na systemie konfiguracji .NET Core. Bezproblemowo integrują się z konfiguracją aplikacji za pośrednictwem dostawcy konfiguracji .NET Core.

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure — [utwórz bezpłatną subskrypcję](https://azure.microsoft.com/free/)
- [.NET Core SDK](https://dotnet.microsoft.com/download).

## <a name="create-an-app-configuration-store"></a>Tworzenie sklepu konfiguracji aplikacji

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Wybierz **pozycję Menedżer** > operacji **+Dodaj,** aby dodać flagę operacji o nazwie `Beta`.

    > [!div class="mx-imgBorder"]
    > ![Włącz flagę funkcji o nazwie Beta](media/add-beta-feature-flag.png)

    Zostaw `label` niezdefiniowane na razie. Wybierz **pozycję Zastosuj,** aby zapisać nową flagę operacji.

## <a name="create-an-aspnet-core-web-app"></a>Tworzenie aplikacji internetowej ASP.NET Core

Użyj [interfejsu wiersza polecenia .NET Core (CLI),](https://docs.microsoft.com/dotnet/core/tools/) aby utworzyć nowy projekt aplikacji sieci Web ASP.NET Core MVC. Zaletą korzystania z interfejsu wiersza polecenia .NET Core zamiast programu Visual Studio jest to, że interfejs wiersza polecenia .NET Core jest dostępny na platformach Windows, macOS i Linux.

1. Utwórz nowy folder dla projektu. Dla tego szybkiego startu, nazwij go *TestFeatureFlags*.

1. W nowym folderze uruchom następujące polecenie, aby utworzyć nowy projekt aplikacji sieci Web ASP.NET Core MVC:

   ```    
   dotnet new mvc --no-https
   ```

## <a name="add-secret-manager"></a>Dodawanie narzędzia Secret Manager

Aby użyć Menedżera programu `UserSecretsId` Tajnego, dodaj element do pliku *csproj.*

1. Otwórz plik *csproj.*

1.  Dodaj `UserSecretsId` element, jak pokazano tutaj. Można użyć tego samego identyfikatora GUID lub zastąpić tę wartość własną.

    > [!IMPORTANT]
    > `CreateHostBuilder`zastępuje `CreateWebHostBuilder` w .NET Core 3.0.  Wybierz poprawną składnię na podstawie środowiska.

    #### <a name="net-core-2x"></a>[.NET Rdzeń 2.x](#tab/core2x)

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

    #### <a name="net-core-3x"></a>[.NET Rdzeń 3.x](#tab/core3x)

    ```xml
    <Project Sdk="Microsoft.NET.Sdk.Web">

        <PropertyGroup>
            <TargetFramework>netcoreapp3.1</TargetFramework>
            <UserSecretsId>79a3edd0-2092-40a2-a04d-dcb46d5ca9ed</UserSecretsId>
        </PropertyGroup>

    </Project>
    ```
    ---

1. Zapisz plik *csproj.*

Narzędzie Secret manager przechowuje poufne dane potrzebne w pracy deweloperskiej poza Twoim drzewem projektu. Takie podejście zapobiega przypadkowemu ujawnieniu wpisów tajnych aplikacji w kodzie źródłowym.

> [!TIP]
> Aby dowiedzieć się więcej o Secret Manager, zobacz [Bezpieczne przechowywanie tajemnic aplikacji w rozwoju w ASP.NET Core](https://docs.microsoft.com/aspnet/core/security/app-secrets).

## <a name="connect-to-an-app-configuration-store"></a>Łączenie się ze sklepem konfiguracji aplikacji

1. Dodaj odwołanie `Microsoft.Azure.AppConfiguration.AspNetCore` do `Microsoft.FeatureManagement.AspNetCore` i pakietów NuGet, uruchamiając następujące polecenia:

    ```dotnetcli
    dotnet add package Microsoft.Azure.AppConfiguration.AspNetCore
    dotnet add package Microsoft.FeatureManagement.AspNetCore
    ```

1. Uruchom następujące polecenie, aby przywrócić pakiety dla projektu:

    ```dotnetcli
    dotnet restore
    ```

1. Dodaj wpis tajny o nazwie **ConnectionStrings:AppConfig** do narzędzia Secret Manager.

    Ten klucz tajny zawiera parametry połączenia, aby uzyskać dostęp do magazynu konfiguracji aplikacji. Zastąp `<your_connection_string>` wartość w poniższym poleceniu ciągiem połączenia dla magazynu konfiguracji aplikacji. Ciąg połączenia można znaleźć w obszarze **Klucze dostępu** w witrynie Azure portal.

    To polecenie należy wykonać w tym samym katalogu, w którym znajduje się plik *csproj*.

    ```dotnetcli
    dotnet user-secrets set ConnectionStrings:AppConfig <your_connection_string>
    ```

    Secret Manager służy tylko do testowania aplikacji sieci web lokalnie. Podczas wdrażania aplikacji w [usłudze Azure App Service](https://azure.microsoft.com/services/app-service), na przykład, należy użyć ustawienia aplikacji o nazwie **Parametry połączenia** w usłudze App Service zamiast używać Menedżera tajnego do przechowywania ciągu połączenia.

    Dostęp do tego klucza tajnego można uzyskać za pomocą interfejsu API konfiguracji aplikacji. Dwukropek (:) działa w nazwie konfiguracji z interfejsem API konfiguracji aplikacji na wszystkich obsługiwanych platformach. Zobacz [Konfiguracja według środowiska](https://docs.microsoft.com/aspnet/core/fundamentals/configuration).

1. Zaktualizuj metodę, `CreateWebHostBuilder` aby `config.AddAzureAppConfiguration()` użyć konfiguracji aplikacji, wywołując metodę.
    
    > [!IMPORTANT]
    > `CreateHostBuilder`zastępuje `CreateWebHostBuilder` w .NET Core 3.0.  Wybierz poprawną składnię na podstawie środowiska.

    #### <a name="net-core-2x"></a>[.NET Rdzeń 2.x](#tab/core2x)
    
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

    #### <a name="net-core-3x"></a>[.NET Rdzeń 3.x](#tab/core3x)
    
    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
        {
            var settings = config.Build();
            config.AddAzureAppConfiguration(options => {
                options.Connect(settings["ConnectionStrings:AppConfig"])
                    .UseFeatureFlags();
            });
        })
        .UseStartup<Startup>());
    ```
    ---

1. Otwórz *Startup.cs*i dodaj odwołania do menedżera funkcji .NET Core:

    ```csharp
    using Microsoft.FeatureManagement;
    ```

1. Zaktualizuj metodę, `ConfigureServices` aby `services.AddFeatureManagement()` dodać obsługę flagi funkcji, wywołując metodę. Opcjonalnie można dołączyć dowolny filtr do użycia z `services.AddFeatureFilter<FilterType>()`flagami operacji, wywołując:

    #### <a name="net-core-2x"></a>[.NET Rdzeń 2.x](#tab/core2x)
    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_2);        
        services.AddFeatureManagement();
    }
    ```
    #### <a name="net-core-3x"></a>[.NET Rdzeń 3.x](#tab/core3x)
    ```csharp    
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddControllersWithViews();
        services.AddFeatureManagement();
    }
    ```
    ---

1. Zaktualizuj `Configure` metodę, aby dodać oprogramowanie pośredniczące, aby umożliwić odświeżanie wartości flagi funkcji w cyklicznym odstępie czasu, podczas gdy aplikacja sieci web ASP.NET Core nadal odbiera żądania.
    
    #### <a name="net-core-2x"></a>[.NET Rdzeń 2.x](#tab/core2x)
    ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
            if (env.IsDevelopment())
            {
                app.UseDeveloperExceptionPage();
            }
            else
            {
                app.UseExceptionHandler("/Home/Error");
            }

            app.UseStaticFiles();
            app.UseCookiePolicy();
            app.UseAzureAppConfiguration();
            app.UseMvc(routes =>
            {
                routes.MapRoute(
                    name: "default",
                    template: "{controller=Home}/{action=Index}/{id?}");
            });
    }
    ```
    #### <a name="net-core-3x"></a>[.NET Rdzeń 3.x](#tab/core3x)
    ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
            if (env.IsDevelopment())
            {
                app.UseDeveloperExceptionPage();
            }
            else
            {
                app.UseExceptionHandler("/Home/Error");
            }
            app.UseStaticFiles();
            app.UseRouting();
            app.UseAuthorization();
            app.UseEndpoints(endpoints =>
            {
                endpoints.MapControllerRoute(
                    name: "default",
                    pattern: "{controller=Home}/{action=Index}/{id?}");
            });
            app.UseAzureAppConfiguration();
    }
    ```
    ---

1. Dodawanie pliku *MyFeatureFlags.cs:*

    ```csharp
    namespace TestFeatureFlags
    {
        public enum MyFeatureFlags
        {
            Beta
        }
    }
    ```

1. Dodaj *BetaController.cs* do katalogu *Kontrolery:*

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

1. Otwórz *_ViewImports.cshtml* w katalogu *Widoki* i dodaj pomocnika tagu menedżera funkcji:

    ```html
    @addTagHelper *, Microsoft.FeatureManagement.AspNetCore
    ```

1. Otwórz *_Layout.cshtml* w*katalogu* Udostępniony widok *i*\\zastąp `<body>`  >  `<header>` `<nav>` kod kreskowy pod następującym kodem:

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

1. Utwórz katalog *beta* w obszarze *Widoki* i dodaj do niego *plik Index.cshtml:*

    ```html
    @{
        ViewData["Title"] = "Beta Home Page";
    }

    <h1>
        This is the beta website.
    </h1>
    ```

## <a name="build-and-run-the-app-locally"></a>Lokalne kompilowanie i uruchamianie aplikacji

1. Aby utworzyć aplikację przy użyciu interfejsu wiersza polecenia .NET Core, uruchom następujące polecenie w powłoce poleceń:

    ```
    dotnet build
    ```

1. Po pomyślnym zakończeniu kompilacji uruchom następujące polecenie, aby uruchomić aplikację sieci web lokalnie:

    ```
    dotnet run
    ```

1. Otwórz okno przeglądarki i `https://localhost:5000`przejdź do , który jest domyślnym adresem URL aplikacji internetowej hostowanego lokalnie.
    Jeśli pracujesz w usłudze Azure Cloud Shell, wybierz przycisk *Podgląd sieci Web,* po którym następuje *configure*.  Po wyświetleniu monitu wybierz port 5000.

    ![Przycisk Znajdź podgląd sieci Web](./media/quickstarts/cloud-shell-web-preview.png)

    Twoja przeglądarka powinna wyświetlać stronę podobną do poniższej.
    ![Lokalne uruchamianie aplikacji z przewodnika Szybki start](./media/quickstarts/aspnet-core-feature-flag-local-before.png)

1. Zaloguj się do [Portalu Azure](https://portal.azure.com). Wybierz **pozycję Wszystkie zasoby**i wybierz wystąpienie sklepu konfiguracja aplikacji utworzone w przewodniku Szybki start.

1. Wybierz **pozycję Menedżer funkcji**i zmień stan klucza **beta** na **Włączone**.

1. Wróć do wiersza polecenia `dotnet` i anuluj uruchomiony proces, naciskając klawisz `Ctrl-C`.  Uruchom ponownie `dotnet run`aplikację za pomocą programu .

1. Odśwież stronę przeglądarki, aby zobaczyć nowe ustawienia konfiguracji.

    ![Lokalne uruchamianie aplikacji z przewodnika Szybki start](./media/quickstarts/aspnet-core-feature-flag-local-after.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start utworzono nowy sklep konfiguracji aplikacji i użyto go do zarządzania funkcjami w aplikacji sieci web ASP.NET Core za pośrednictwem [bibliotek zarządzania funkcjami](https://go.microsoft.com/fwlink/?linkid=2074664).

- Dowiedz się więcej o [zarządzaniu funkcjami](./concept-feature-management.md).
- [Zarządzanie flagami funkcji](./manage-feature-flags.md).
- [Użyj flag funkcji w aplikacji ASP.NET Core](./use-feature-flags-dotnet-core.md).
- [Używanie dynamicznej konfiguracji w aplikacji platformy ASP.NET Core](./enable-dynamic-configuration-aspnet-core.md)
