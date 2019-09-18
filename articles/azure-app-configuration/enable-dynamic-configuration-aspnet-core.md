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
ms.devlang: csharp
ms.topic: tutorial
ms.date: 02/24/2019
ms.author: yegu
ms.custom: mvc
ms.openlocfilehash: 235b55bcd727e3e3ea947ce086209e0a94f70752
ms.sourcegitcommit: 8ef0a2ddaece5e7b2ac678a73b605b2073b76e88
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/17/2019
ms.locfileid: "71076383"
---
# <a name="tutorial-use-dynamic-configuration-in-an-aspnet-core-app"></a>Samouczek: Używanie dynamicznej konfiguracji w aplikacji platformy ASP.NET Core

ASP.NET Core ma podłączany System konfiguracyjny, który może odczytywać dane konfiguracji z różnych źródeł. Może obsłużyć zmiany na bieżąco bez powodowania ponownego uruchomienia aplikacji. ASP.NET Core obsługuje powiązanie ustawień konfiguracji z silnie wpisanąmi klasami platformy .NET. Wprowadza je do kodu przy użyciu różnych `IOptions<T>` wzorców. Jeden z tych wzorców, `IOptionsSnapshot<T>`w odmierzeniu, automatycznie ładuje konfigurację aplikacji, gdy zmieniają się dane bazowe. Wzorzec `IOptionsSnapshot<T>` możesz wprowadzać do kontrolerów w aplikacji, aby uzyskiwać dostęp do najnowszej konfiguracji przechowywanej w usłudze Azure App Configuration.

Możesz również skonfigurować konfigurację aplikacji ASP.NET Coreą bibliotekę klienta, aby odświeżyć zestaw ustawień konfiguracji dynamicznie przy użyciu oprogramowania pośredniczącego. Tak długo, jak aplikacja internetowa nadal otrzymuje żądania, ustawienia konfiguracji nadal są aktualizowane w magazynie konfiguracji.

Aby zachować zaktualizowane ustawienia i uniknąć zbyt wielu wywołań w magazynie konfiguracji, dla każdego ustawienia zostanie użyta pamięć podręczna. Do momentu wygaśnięcia zbuforowanej wartości ustawienia operacja odświeżania nie aktualizuje wartości, nawet jeśli wartość została zmieniona w magazynie konfiguracji. Domyślny czas wygaśnięcia dla każdego żądania wynosi 30 sekund, ale w razie potrzeby można go zastąpić.

W tym samouczku pokazano, jak zaimplementować dynamiczne aktualizacje konfiguracji w swoim kodzie. Opiera się on na aplikacji internetowej wprowadzonej w przewodnikach Szybki start. Przed kontynuowaniem najpierw Zakończ [Tworzenie aplikacji ASP.NET Coreej z konfiguracją aplikacji](./quickstart-aspnet-core-app.md) .

Aby wykonać kroki opisane w tym samouczku, można użyć dowolnego edytora kodu. [Visual Studio Code](https://code.visualstudio.com/) jest doskonałym rozwiązaniem dostępnym na platformach Windows, MacOS i Linux.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Skonfiguruj swoją aplikację w celu zaktualizowania jej konfiguracji w odpowiedzi na zmiany w magazynie konfiguracji aplikacji.
> * Wstaw najnowszą konfigurację z kontrolerów aplikacji.

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać ten samouczek, zainstaluj [zestaw .NET Core SDK](https://dotnet.microsoft.com/download).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="reload-data-from-app-configuration"></a>Ponowne ładowanie danych z usługi App Configuration

1. Otwórz *program.cs*i zaktualizuj `CreateWebHostBuilder` `config.AddAzureAppConfiguration()` metodę, aby dodać metodę.

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
                               refresh.Register("TestApp:Settings:BackgroundColor")
                                      .Register("TestApp:Settings:FontColor")
                                      .Register("TestApp:Settings:Message");
                           });
                });
            })
            .UseStartup<Startup>();
    ```

    Ta `ConfigureRefresh` Metoda służy do określania ustawień służących do aktualizowania danych konfiguracji z magazynem konfiguracji aplikacji w przypadku wyzwolenia operacji odświeżania. W celu faktycznego wyzwolenia operacji odświeżania należy skonfigurować oprogramowanie pośredniczące odświeżania, aby aplikacja mogła odświeżyć dane konfiguracji w przypadku wystąpienia zmiany.

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

3. Otwórz *Startup.cs*i zaktualizuj `ConfigureServices` metodę, aby powiązać dane `Settings` konfiguracji z klasą.

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

4. `Configure` Zaktualizuj metodę, aby dodać oprogramowanie pośredniczące, aby umożliwić zaktualizowanie ustawień konfiguracji na potrzeby odświeżania, gdy aplikacja sieci Web ASP.NET Core nadal otrzymuje żądania.

    ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        app.UseAzureAppConfiguration();
        app.UseMvc();
    }
    ```
    
    Oprogramowanie pośredniczące używa konfiguracji odświeżania określonej w `AddAzureAppConfiguration` metodzie w programie `Program.cs` , aby wyzwolić odświeżanie dla każdego żądania odebranego przez aplikację sieci Web ASP.NET Core. Dla każdego żądania jest wyzwalana operacja odświeżania, a Biblioteka klienta sprawdza, czy wartość pamięci podręcznej dla zarejestrowanych ustawień konfiguracji wygasła. W przypadku wartości z pamięci podręcznej, które wygasły, wartości ustawień są aktualizowane w magazynie konfiguracji aplikacji, a pozostałe wartości pozostają niezmienione.
    
    > [!NOTE]
    > Domyślny czas wygaśnięcia pamięci podręcznej dla ustawienia konfiguracji wynosi 30 sekund, ale można go zastąpić, wywołując `SetCacheExpiration` metodę z inicjatora opcji przekazaną jako argument `ConfigureRefresh` do metody.

## <a name="use-the-latest-configuration-data"></a>Używanie najnowszych danych konfiguracji

1. Otwórz *HomeController.cs* w katalogu controllers i Dodaj odwołanie do `Microsoft.Extensions.Options` pakietu.

    ```csharp
    using Microsoft.Extensions.Options;
    ```

2. Zaktualizuj klasę `HomeController` , aby otrzymywać `Settings` dane przy użyciu iniekcji zależności, i użyj jej wartości.

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

3. Otwórz *index. cshtml* w widokach > katalog macierzysty i Zastąp jego zawartość następującym skryptem:

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

1. Aby skompilować aplikację przy użyciu interfejs wiersza polecenia platformy .NET Core, uruchom następujące polecenie w powłoce poleceń:

        dotnet build

2. Po pomyślnym zakończeniu kompilacji Uruchom następujące polecenie, aby uruchomić aplikację sieci Web lokalnie:

        dotnet run

3. Otwórz okno przeglądarki i przejdź do `http://localhost:5000`, który jest domyślnym adresem URL aplikacji sieci Web hostowanej lokalnie.

    ![Lokalne uruchamianie aplikacji z przewodnika Szybki start](./media/quickstarts/aspnet-core-app-launch-local-before.png)

4. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Wybierz pozycję **wszystkie zasoby**, a następnie wybierz wystąpienie magazynu konfiguracji aplikacji utworzone w ramach przewodnika Szybki Start.

5. Wybierz pozycję **Eksplorator konfiguracji**i zaktualizuj wartości następujących kluczy:

    | Klucz | Value |
    |---|---|
    | TestApp:Settings:BackgroundColor | zielony |
    | TestApp:Settings:FontColor | lightGray |
    | TestApp:Settings:Message | Dane z usługi Azure App Configuration — teraz z aktualizacjami na żywo! |

6. Odśwież stronę przeglądarki, aby zobaczyć nowe ustawienia konfiguracji. Aby zmiany zostały odzwierciedlone, może być wymagana więcej niż jedno odświeżenie strony przeglądarki.

    ![Lokalne odświeżanie aplikacji z przewodnika Szybki start](./media/quickstarts/aspnet-core-app-launch-local-after.png)
    
    > [!NOTE]
    > Ponieważ ustawienia konfiguracji są buforowane przy użyciu domyślnego czasu wygaśnięcia wynoszącego 30 sekund, wszelkie zmiany wprowadzone w ustawieniach w magazynie konfiguracji aplikacji byłyby odzwierciedlone w aplikacji sieci Web tylko wtedy, gdy pamięć podręczna wygasła.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Następne kroki

W tym samouczku dodano tożsamość usługi zarządzanej przez platformę Azure, aby usprawnić dostęp do usługi App Configuration i poprawić zarządzanie poświadczeniami w Twojej aplikacji. Aby dowiedzieć się więcej na temat korzystania z konfiguracji aplikacji, przejdź do przykładów interfejsu wiersza polecenia platformy Azure.

> [!div class="nextstepaction"]
> [Przykłady interfejsu wiersza polecenia](./cli-samples.md)
