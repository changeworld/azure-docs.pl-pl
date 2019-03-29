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
ms.openlocfilehash: cf872766a18c5691f6c094d71a0c29f6bcf736da
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2019
ms.locfileid: "58579040"
---
# <a name="tutorial-use-dynamic-configuration-in-an-aspnet-core-app"></a>Samouczek: Używanie dynamicznej konfiguracji w aplikacji platformy ASP.NET Core

Platforma ASP.NET Core ma system podłączanych konfiguracji, który może odczytać dane konfiguracyjne z różnych źródeł. Może obsługiwać zmiany na bieżąco bez powodowania ponowne uruchomienie aplikacji. Platforma ASP.NET Core obsługuje wiązania ustawienia konfiguracji do silnie typizowanej klasy .NET. Jego wprowadza je w kodzie za pomocą różnych `IOptions<T>` wzorców. Jedną z tych wzorców w szczególności `IOptionsSnapshot<T>`powoduje automatyczne ponowne załadowanie konfiguracji aplikacji, po zmianie danych bazowych. 

Wzorzec `IOptionsSnapshot<T>` możesz wprowadzać do kontrolerów w aplikacji, aby uzyskiwać dostęp do najnowszej konfiguracji przechowywanej w usłudze Azure App Configuration. Możesz również skonfigurować biblioteki klienta aplikacji konfiguracji platformy ASP.NET Core, stale monitorować i pobrać wszelkie zmiany w konfiguracji sklepu z aplikacjami. Należy zdefiniować okresowe interwał sondowania.

W tym samouczku pokazano, jak zaimplementować dynamiczne aktualizacje konfiguracji w swoim kodzie. Opiera się on na aplikacji internetowej wprowadzonej w przewodnikach Szybki start. Przed kontynuowaniem należy zakończyć [tworzenie aplikacji platformy ASP.NET Core z konfiguracji aplikacji](./quickstart-aspnet-core-app.md) pierwszy.

Wykonaj kroki w tym przewodniku Szybki Start, można użyć dowolnego edytora kodu. [Visual Studio Code](https://code.visualstudio.com/) doskonałym rozwiązaniem, która jest dostępna w Windows, macOS i platformy Linux.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Skonfiguruj aplikację, aby zaktualizować jej konfiguracji w odpowiedzi na zmiany w magazynie konfiguracji aplikacji.
> * Wstrzyknąć najnowszą konfigurację w kontrolerów danej aplikacji.

## <a name="prerequisites"></a>Wymagania wstępne

Ten przewodnik Szybki Start, instaluje [zestawu .NET Core SDK](https://dotnet.microsoft.com/download).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="reload-data-from-app-configuration"></a>Ponowne ładowanie danych z usługi App Configuration

1. Otwórz plik Program.cs i zaktualizuj `CreateWebHostBuilder` metody, dodając `config.AddAzureAppConfiguration()` metody.

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();
                config.AddAzureAppConfiguration(options =>
                    options.Connect(settings["ConnectionStrings:AppConfig"])
                           .Watch("TestApp:Settings:BackgroundColor")
                           .Watch("TestApp:Settings:FontColor")
                           .Watch("TestApp:Settings:Message"));
            })
            .UseStartup<Startup>();
    ```

    Drugi parametr w `.Watch` metodą jest interwał sondowania, jaką Biblioteka klienta ASP.NET zapytania konfiguracji sklepu z aplikacjami. Biblioteka klienta sprawdza, czy ustawienie określonej konfiguracji, aby zobaczyć, czy każda zmiana wystąpił.

2. Dodawanie pliku Settings.cs, który definiuje i implementuje nową `Settings` klasy.

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

3. Otwórz plik Startup.cs i zaktualizuj `ConfigureServices` metodę, aby powiązać dane konfiguracji do `Settings` klasy.

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

1. Otwórz HomeController.cs w katalogu kontrolerów. Aktualizacja `HomeController` klasy, aby otrzymać `Settings` za pomocą iniekcji zależności. Ponadto upewnij korzystanie z jej wartości.

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

2. Otwórz Index.cshtml w widokach > Strona główna katalogu i zastąp jego zawartość następującym skryptem:

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

1. Aby skompilować aplikację przy użyciu interfejsu wiersza polecenia platformy .NET Core, uruchom następujące polecenie w powłoce poleceń:

        dotnet build

2. Po pomyślnym zakończeniu kompilacji, uruchom następujące polecenie, aby lokalnie uruchomić aplikację sieci web:

        dotnet run

3. Otwórz okno przeglądarki i przejdź do `http://localhost:5000`, która jest domyślny adres URL aplikacji sieci web hostowanych lokalnie.

    ![Lokalne uruchamianie aplikacji z przewodnika Szybki start](./media/quickstarts/aspnet-core-app-launch-local-before.png)

4. Zaloguj się w witrynie [Azure Portal](https://aka.ms/azconfig/portal). Wybierz **wszystkie zasoby**i wybierz wystąpienie magazynu konfiguracji aplikacji, które utworzono w przewodniku Szybki Start.

5. Wybierz **Explorer klucz/wartość**i zaktualizuj wartości następujących kluczy:

    | Klucz | Wartość |
    |---|---|
    | TestAppSettings:BackgroundColor | blue |
    | TestAppSettings:FontColor | lightGray |
    | TestAppSettings:Message | Dane z usługi Azure App Configuration — teraz z aktualizacjami na żywo! |

6. Odśwież stronę przeglądarki, aby zobaczyć nowe ustawienia konfiguracji.

    ![Lokalne odświeżanie aplikacji z przewodnika Szybki start](./media/quickstarts/aspnet-core-app-launch-local-after.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku dodano tożsamość usługi zarządzanej przez platformę Azure, aby usprawnić dostęp do usługi App Configuration i poprawić zarządzanie poświadczeniami w Twojej aplikacji. Aby dowiedzieć się więcej o tym, jak używać konfiguracji aplikacji, przejdź do przykładów interfejsu wiersza polecenia platformy Azure.

> [!div class="nextstepaction"]
> [Przykłady interfejsu wiersza polecenia](./cli-samples.md)
