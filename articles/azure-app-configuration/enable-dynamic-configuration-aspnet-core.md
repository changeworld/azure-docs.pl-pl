---
title: Samouczek dotyczący używania dynamicznej konfiguracji usługi Azure App Configuration w aplikacji platformy ASP.NET Core | Microsoft Docs
description: Z tego samouczka dowiesz się, jak dynamicznie aktualizować dane konfiguracji dla aplikacji platformy ASP.NET Core
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: na
ms.topic: tutorial
ms.date: 02/24/2019
ms.author: yegu
ms.custom: mvc
ms.openlocfilehash: 44ae922b182874eef378d4868fb278c3c76252db
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/26/2019
ms.locfileid: "56884417"
---
# <a name="tutorial-use-dynamic-configuration-in-an-aspnet-core-app"></a>Samouczek: Używanie dynamicznej konfiguracji w aplikacji platformy ASP.NET Core

Platforma ASP.NET Core ma system podłączanej konfiguracji, który potrafi odczytywać dane konfiguracji z różnych źródeł, a także obsługiwać zmiany na bieżąco bez powodowania ponownego uruchamiania aplikacji. Obsługuje powiązywanie ustawień konfiguracji z silnie typizowanymi klasami platformy .NET i wprowadzanie ich do Twojego kodu za pomocą różnych wzorców `IOptions<T>`. Jeden z tych wzorców, a konkretnie `IOptionsSnapshot<T>`, zapewnia automatyczne ponowne ładowanie konfiguracji aplikacji po zmianie danych bazowych. Wzorzec `IOptionsSnapshot<T>` możesz wprowadzać do kontrolerów w aplikacji, aby uzyskiwać dostęp do najnowszej konfiguracji przechowywanej w usłudze Azure App Configuration. Dodatkowo możesz skonfigurować bibliotekę kliencką platformy ASP.NET Core usługi App Configuration, aby stale monitorować i pobierać wszelkie zmiany w magazynie konfiguracji aplikacji poprzez sondowanie w interwałach, które zdefiniujesz.

W tym samouczku pokazano, jak zaimplementować dynamiczne aktualizacje konfiguracji w swoim kodzie. Opiera się on na aplikacji internetowej wprowadzonej w przewodnikach Szybki start. Przed kontynuacją należy ukończyć [tworzenie aplikacji platformy ASP.NET Core z użyciem usługi App Configuration](./quickstart-aspnet-core-app.md).

Do wykonania kroków tego przewodnika Szybki start możesz użyć dowolnego edytora kodu. Doskonałym wyborem jest program [Visual Studio Code](https://code.visualstudio.com/), dostępny na platformach Windows, macOS i Linux.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Konfigurowanie aplikacji w celu aktualizowania jej konfiguracji w odpowiedzi na zmiany w magazynie konfiguracji aplikacji
> * Wprowadzanie najnowszej konfiguracji w kontrolerach aplikacji

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik Szybki start, zainstaluj zestaw [.NET Core SDK](https://dotnet.microsoft.com/download).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="reload-data-from-app-configuration"></a>Ponowne ładowanie danych z usługi App Configuration

1. Otwórz plik *Program.cs* i zaktualizuj metodę `CreateWebHostBuilder`, dodając metodę `config.AddAzureAppConfiguration()`.

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();
                config.AddAzureAppConfiguration(o => o.Connect(settings["ConnectionStrings:AppConfig"])
                    .Watch("TestApp:Settings:BackgroundColor", TimeSpan.FromSeconds(1))
                    .Watch("TestApp:Settings:FontColor", TimeSpan.FromSeconds(1))
                    .Watch("TestApp:Settings:Message", TimeSpan.FromSeconds(1)));
            })
            .UseStartup<Startup>();
    ```

    Drugim parametrem metody `.Watch` jest interwał sondowania, z którym biblioteka kliencka platformy ASP.NET wykonuje zapytania kierowane do magazynu konfiguracji aplikacji, aby sprawdzić, czy wprowadzono jakieś zmiany do konkretnego ustawienia konfiguracji.

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

3. Otwórz plik *Startup.cs* i zaktualizuj metodę `ConfigureServices`, aby powiązać dane konfiguracji z klasą `Settings`.

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.Configure<Settings>(Configuration.GetSection("TestApp:Settings"));

        services.Configure<CookiePolicyOptions>(options =>
        {
            options.CheckConsentNeeded = context => true;
            options.MinimumSameSitePolicy = SameSiteMode.None;
        });

        services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
    }
    ```

## <a name="use-the-latest-configuration-data"></a>Używanie najnowszych danych konfiguracji

1. Otwórz plik *HomeController.cs* w katalogu *Controllers* i zaktualizuj klasę `HomeController`, aby odbierać element `Settings` przez iniekcję zależności oraz użyć jego wartości.

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

2. Otwórz plik *Index.cshtml* w katalogu *Views* > *Home* i zastąp jego zawartość następującą:

    ```html
    <!DOCTYPE html>
    <html lang="en">
    <style>
        body {
            background-color: @ViewData["BackgroundColor"]
        }
        h1 {
            color: @ViewData["FontColor"];
            font-size: @ViewData["FontSize"];
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

1. Aby skompilować aplikację przy użyciu interfejsu wiersza polecenia platformy .NET Core, wykonaj następujące polecenie w powłoce poleceń:

        dotnet build

2. Po pomyślnym zakończeniu kompilacji wykonaj następujące polecenie w celu lokalnego uruchomienia aplikacji internetowej:

        dotnet run

3. Uruchom okno przeglądarki i przejdź pod adres `http://localhost:5000`, który jest domyślnym adresem URL aplikacji internetowej hostowanej lokalnie.

    ![Lokalne uruchamianie aplikacji z przewodnika Szybki start](./media/quickstarts/aspnet-core-app-launch-local-before.png)

4. Zaloguj się do witryny [Azure Portal](https://aka.ms/azconfig/portal), kliknij pozycję **Wszystkie zasoby**, a następnie wybierz wystąpienie magazynu konfiguracji aplikacji, które zostało utworzone w przewodniku Szybki start.

5. Kliknij pozycję **Eksplorator kluczy/wartości** i zaktualizuj wartości następujących kluczy:

    | Klucz | Wartość |
    |---|---|
    | TestAppSettings:BackgroundColor | blue |
    | TestAppSettings:FontColor | lightGray |
    | TestAppSettings:Message | Dane z usługi Azure App Configuration — teraz z aktualizacjami na żywo! |

6. Odśwież stronę przeglądarki, aby zobaczyć nowe ustawienia konfiguracji.

    ![Lokalne odświeżanie aplikacji z przewodnika Szybki start](./media/quickstarts/aspnet-core-app-launch-local-after.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Następne kroki

W tym samouczku dodano tożsamość usługi zarządzanej przez platformę Azure, aby usprawnić dostęp do usługi App Configuration i poprawić zarządzanie poświadczeniami w Twojej aplikacji. Aby dowiedzieć się więcej o korzystaniu z usługi App Configuration, przejdź do przykładów interfejsu wiersza polecenia platformy Azure.

> [!div class="nextstepaction"]
> [Przykłady interfejsu wiersza polecenia](./cli-samples.md)
