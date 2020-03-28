---
title: 'Samouczek: Korzystanie z konfiguracji dynamicznej konfiguracji aplikacji w ASP.NET Core'
titleSuffix: Azure App Configuration
description: Z tego samouczka dowiesz się, jak dynamicznie aktualizować dane konfiguracji dla aplikacji platformy ASP.NET Core
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 02/24/2019
ms.author: lcozzens
ms.custom: mvc
ms.openlocfilehash: e9df6d2e7a8219d16e7b60f7c3b8d826a87e6110
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80348853"
---
# <a name="tutorial-use-dynamic-configuration-in-an-aspnet-core-app"></a>Samouczek: Używanie konfiguracji dynamicznej w aplikacji ASP.NET Core

ASP.NET Core posiada podłączany system konfiguracji, który może odczytywać dane konfiguracyjne z różnych źródeł. Może obsługiwać zmiany dynamicznie bez powodowania ponownego uruchomienia aplikacji. ASP.NET Core obsługuje powiązanie ustawień konfiguracji z silnie wpisanych klas .NET. Wstrzykuje je do kodu `IOptions<T>` przy użyciu różnych wzorców. Jeden z tych wzorców, `IOptionsSnapshot<T>`w szczególności, automatycznie ładuje konfigurację aplikacji po zmianie danych źródłowych. Wzorzec `IOptionsSnapshot<T>` możesz wprowadzać do kontrolerów w aplikacji, aby uzyskiwać dostęp do najnowszej konfiguracji przechowywanej w usłudze Azure App Configuration.

Można również skonfigurować bibliotekę klienta Konfiguracja aplikacji ASP.NET Core, aby dynamicznie odświeżać zestaw ustawień konfiguracji za pomocą oprogramowania pośredniczącego. Ustawienia konfiguracji są aktualizowane z magazynem konfiguracji za każdym razem, o ile aplikacja internetowa odbiera żądania.

Konfiguracja aplikacji automatycznie buforuje każde ustawienie, aby uniknąć zbyt wielu wywołań do magazynu konfiguracji. Operacja odświeżania czeka, aż wartość buforowana ustawienia wygaśnie, aby zaktualizować to ustawienie, nawet wtedy, gdy jego wartość zmieni się w magazynie konfiguracji. Domyślny czas wygaśnięcia pamięci podręcznej wynosi 30 sekund. W razie potrzeby można zastąpić ten czas wygaśnięcia.

W tym samouczku pokazano, jak zaimplementować dynamiczne aktualizacje konfiguracji w swoim kodzie. Opiera się on na aplikacji internetowej wprowadzonej w przewodnikach Szybki start. Przed kontynuowaniem zakończ tworzenie aplikacji ASP.NET Core przy czym najpierw [zostanie utworzona konfiguracja aplikacji.](./quickstart-aspnet-core-app.md)

Aby wykonać czynności opisane w tym samouczku, można użyć dowolnego edytora kodu. [Visual Studio Code](https://code.visualstudio.com/) to doskonała opcja dostępna na platformach Windows, macOS i Linux.

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Skonfiguruj aplikację, aby zaktualizować jej konfigurację w odpowiedzi na zmiany w sklepie konfiguracji aplikacji.
> * Wstrzyknąć najnowszą konfigurację w kontrolerach aplikacji.

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać ten samouczek, zainstaluj [pakiet .NET Core SDK](https://dotnet.microsoft.com/download).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Przed kontynuowaniem zakończ tworzenie aplikacji ASP.NET Core przy czym najpierw [zostanie utworzona konfiguracja aplikacji.](./quickstart-aspnet-core-app.md)

## <a name="add-a-sentinel-key"></a>Dodawanie klucza wartownika

*Klucz wartownika* jest specjalnym kluczem używanym do sygnalizowania zmiany konfiguracji. Aplikacja monitoruje klucz wartownika pod kątem zmian. Po wykryciu zmiany odświeżasz wszystkie wartości konfiguracji. Takie podejście zmniejsza ogólną liczbę żądań złożonych przez aplikację do konfiguracji aplikacji, w porównaniu do monitorowania wszystkich kluczy dla zmian.

1. W portalu Azure wybierz pozycję **Eksplorator konfiguracji > utwórz > wartość klucza**.

1. Dla **klucza**wprowadź *TestApp:Ustawienia:Sentinel*. W polu **Wartość**wprowadź 1. Pozostaw **etykietę** i **typ zawartości** pusty.

1. Wybierz przycisk **Zastosuj**.

## <a name="reload-data-from-app-configuration"></a>Ponowne ładowanie danych z usługi App Configuration

1. Dodaj odwołanie do `Microsoft.Azure.AppConfiguration.AspNetCore` pakietu NuGet, uruchamiając następujące polecenie:

    ```dotnetcli
    dotnet add package Microsoft.Azure.AppConfiguration.AspNetCore
    ```

1. Otwórz *Program.cs*i zaktualizuj `CreateWebHostBuilder` `config.AddAzureAppConfiguration()` metodę, aby dodać metodę.

    #### <a name="net-core-2x"></a>[.NET Rdzeń 2.x](#tab/core2x)

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();

                config.AddAzureAppConfiguration(options =>
                {
                    options.Connect(settings["ConnectionStrings:AppConfig"])
                           .ConfigureRefresh(refresh =>
                                {
                                    refresh.Register("TestApp:Settings:Sentinel", refreshAll: true)
                                           .SetCacheExpiration(new TimeSpan(0, 5, 0));
                                });
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
                    config.AddAzureAppConfiguration(options =>
                    {
                        options.Connect(settings["ConnectionStrings:AppConfig"])
                               .ConfigureRefresh(refresh =>
                                    {
                                        refresh.Register("TestApp:Settings:Sentinel", refreshAll: true)
                                               .SetCacheExpiration(new TimeSpan(0, 5, 0));
                                    });
                    });
                })
            .UseStartup<Startup>());
    ```
    ---

    Metoda `ConfigureRefresh` służy do określania ustawień używanych do aktualizowania danych konfiguracyjnych w magazynie konfiguracji aplikacji po wyzwoleniu operacji odświeżania. Parametr `refreshAll` `Register` metody wskazuje, że wszystkie wartości konfiguracji powinny być odświeżane, jeśli zmieni się klucz wartownika.

    Ponadto `SetCacheExpiration` metoda zastępuje domyślny czas wygaśnięcia pamięci podręcznej 30 sekund, określając zamiast tego czas 5 minut. Zmniejsza to liczbę żądań do konfiguracji aplikacji.

    > [!NOTE]
    > Do celów testowych można obniżyć czas wygaśnięcia pamięci podręcznej.

    Aby faktycznie wyzwolić operację odświeżania, należy skonfigurować odświeżanie oprogramowania pośredniczącego dla aplikacji, aby odświeżyć dane konfiguracyjne w przypadku wystąpienia zmiany. Zobaczysz, jak to zrobić w późniejszym kroku.

2. Dodaj plik *Settings.cs*, który definiuje i implementuje nową klasę `Settings`.

    ```csharp
    namespace TestAppConfig
    {
        public class Settings
        {
            public string BackgroundColor { get; set; }
            public long FontSize { get; set; }
            public string FontColor { get; set; }
            public string Message { get; set; }
        }
    }
    ```

3. Otwórz *Startup.cs*i użyj `IServiceCollection.Configure<T>` w `ConfigureServices` metodzie powiązania danych `Settings` konfiguracji z klasą.

    #### <a name="net-core-2x"></a>[.NET Rdzeń 2.x](#tab/core2x)

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.Configure<Settings>(Configuration.GetSection("TestApp:Settings"));
        services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
    }
    ```

    #### <a name="net-core-3x"></a>[.NET Rdzeń 3.x](#tab/core3x)

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.Configure<Settings>(Configuration.GetSection("TestApp:Settings"));
        services.AddControllersWithViews();
    }
    ```
    ---

4. Zaktualizuj `Configure` `UseAzureAppConfiguration` metodę, dodając oprogramowanie pośredniczące, aby umożliwić aktualizowanie ustawień konfiguracji zarejestrowanych do odświeżania, podczas gdy aplikacja sieci web ASP.NET Core nadal odbiera żądania.


    #### <a name="net-core-2x"></a>[.NET Rdzeń 2.x](#tab/core2x)

    ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        app.UseAzureAppConfiguration();

        services.Configure<CookiePolicyOptions>(options =>
        {
            options.CheckConsentNeeded = context => true;
            options.MinimumSameSitePolicy = SameSiteMode.None;
        });

        app.UseMvc();
    }
    ```

    #### <a name="net-core-3x"></a>[.NET Rdzeń 3.x](#tab/core3x)

    ```csharp
    public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
    {
            if (env.IsDevelopment())
            {
                app.UseDeveloperExceptionPage();
            }
            else
            {
                app.UseExceptionHandler("/Home/Error");
                // The default HSTS value is 30 days. You may want to change this for production scenarios, see https://aka.ms/aspnetcore-hsts.
                app.UseHsts();
            }

            // Add the following line:
            app.UseAzureAppConfiguration();

            app.UseHttpsRedirection();
            
            app.UseStaticFiles();

            app.UseRouting();

            app.UseAuthorization();

            app.UseEndpoints(endpoints =>
            {
                endpoints.MapControllerRoute(
                    name: "default",
                    pattern: "{controller=Home}/{action=Index}/{id?}");
            });
    }
    ```
    ---
    
    Oprogramowanie pośredniczące używa konfiguracji odświeżania określonej w `AddAzureAppConfiguration` metodzie w `Program.cs` wyzwoleniu odświeżenia dla każdego żądania odebranego przez aplikację sieci web ASP.NET Core. Dla każdego żądania wyzwalana jest operacja odświeżania, a biblioteka klienta sprawdza, czy wartość buforowanej dla zarejestrowanego ustawienia konfiguracji wygasła. Jeśli wygasła, jest odświeżona.

## <a name="use-the-latest-configuration-data"></a>Używanie najnowszych danych konfiguracji

1. Otwórz *HomeController.cs* w katalogu Kontrolery i dodaj odwołanie do `Microsoft.Extensions.Options` pakietu.

    ```csharp
    using Microsoft.Extensions.Options;
    ```

2. Zaktualizuj klasę, `HomeController` aby otrzymać `Settings` za pomocą iniekcji zależności i korzystać z jej wartości.

    #### <a name="net-core-2x"></a>[.NET Rdzeń 2.x](#tab/core2x)

    ```csharp
    public class HomeController : Controller
    {
        private readonly Settings _settings;
        public HomeController(IOptionsSnapshot<Settings> settings)
        {
            _settings = settings.Value;
        }

        public IActionResult Index()
        {
            ViewData["BackgroundColor"] = _settings.BackgroundColor;
            ViewData["FontSize"] = _settings.FontSize;
            ViewData["FontColor"] = _settings.FontColor;
            ViewData["Message"] = _settings.Message;

            return View();
        }
    }
    ```

    #### <a name="net-core-3x"></a>[.NET Rdzeń 3.x](#tab/core3x)

    ```csharp
    public class HomeController : Controller
    {
        private readonly Settings _settings;
        private readonly ILogger<HomeController> _logger;

        public HomeController(ILogger<HomeController> logger, IOptionsSnapshot<Settings> settings)
        {
            _logger = logger;
            _settings = settings.Value;
        }

        public IActionResult Index()
        {
            ViewData["BackgroundColor"] = _settings.BackgroundColor;
            ViewData["FontSize"] = _settings.FontSize;
            ViewData["FontColor"] = _settings.FontColor;
            ViewData["Message"] = _settings.Message;

            return View();
        }

        // ...
    }
    ```
    ---



3. Otwórz *plik Index.cshtml* w katalogu Widoki > Strona główna i zastąp jego zawartość następującym skryptem:

    ```html
    <!DOCTYPE html>
    <html lang="en">
    <style>
        body {
            background-color: @ViewData["BackgroundColor"]
        }
        h1 {
            color: @ViewData["FontColor"];
            font-size: @ViewData["FontSize"]px;
        }
    </style>
    <head>
        <title>Index View</title>
    </head>
    <body>
        <h1>@ViewData["Message"]</h1>
    </body>
    </html>
    ```

## <a name="build-and-run-the-app-locally"></a>Lokalne kompilowanie i uruchamianie aplikacji

1. Aby utworzyć aplikację przy użyciu interfejsu wiersza polecenia .NET Core, uruchom następujące polecenie w powłoce poleceń:

        dotnet build

1. Po pomyślnym zakończeniu kompilacji uruchom następujące polecenie, aby uruchomić aplikację sieci web lokalnie:

        dotnet run
1. Otwórz okno przeglądarki i przejdź do adresu `dotnet run` URL wyświetlanego na danych wyjściowych.

    ![Lokalne uruchamianie aplikacji Szybki start](./media/quickstarts/aspnet-core-app-launch-local-before.png)

1. Zaloguj się do [Portalu Azure](https://portal.azure.com). Wybierz **pozycję Wszystkie zasoby**i wybierz wystąpienie sklepu konfiguracja aplikacji utworzone w przewodniku Szybki start.

1. Wybierz **pozycję Eksplorator konfiguracji**i zaktualizuj wartości następujących klawiszy:

    | Klucz | Wartość |
    |---|---|
    | TestApp:Settings:BackgroundColor | green |
    | TestApp:Settings:FontColor | lightGray |
    | TestApp:Settings:Message | Dane z usługi Azure App Configuration — teraz z aktualizacjami na żywo! |
    | TestApp:Ustawienia:Sentinel | 2 |

1. Odśwież stronę przeglądarki, aby zobaczyć nowe ustawienia konfiguracji. Aby zmiany zostały odzwierciedlone, może być konieczne odświeżenie więcej niż jeden raz.

    ![Lokalne uruchamianie zaktualizowanej aplikacji Szybki start](./media/quickstarts/aspnet-core-app-launch-local-after.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Następne kroki

W tym samouczku włączono aplikację sieci Web ASP.NET Core, aby dynamicznie odświeżać ustawienia konfiguracji z konfiguracji aplikacji. Aby dowiedzieć się, jak użyć tożsamości zarządzanej przez platformę Azure, aby usprawnić dostęp do konfiguracji aplikacji, przejdź do następnego samouczka.

> [!div class="nextstepaction"]
> [Integracja tożsamości zarządzanej](./howto-integrate-azure-managed-service-identity.md)
