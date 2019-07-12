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
ms.openlocfilehash: 78c64786f523aa424e8a9816e42db70e2a2997c2
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2019
ms.locfileid: "67798467"
---
# <a name="tutorial-use-dynamic-configuration-in-an-aspnet-core-app"></a>Samouczek: Używanie dynamicznej konfiguracji w aplikacji platformy ASP.NET Core

Platforma ASP.NET Core ma system podłączanych konfiguracji, który może odczytać dane konfiguracyjne z różnych źródeł. Może obsługiwać zmiany na bieżąco bez powodowania ponowne uruchomienie aplikacji. Platforma ASP.NET Core obsługuje wiązania ustawienia konfiguracji do silnie typizowanej klasy .NET. Jego wprowadza je w kodzie za pomocą różnych `IOptions<T>` wzorców. Jedną z tych wzorców w szczególności `IOptionsSnapshot<T>`powoduje automatyczne ponowne załadowanie konfiguracji aplikacji, po zmianie danych bazowych. Wzorzec `IOptionsSnapshot<T>` możesz wprowadzać do kontrolerów w aplikacji, aby uzyskiwać dostęp do najnowszej konfiguracji przechowywanej w usłudze Azure App Configuration.

Możesz też skonfigurować biblioteki klienta aplikacji konfiguracji platformy ASP.NET Core można odświeżyć zestaw ustawień konfiguracji dynamicznie za pomocą oprogramowania pośredniczącego. Tak długo, jak aplikacja sieci web w dalszym ciągu otrzymywać żądania, ustawienia konfiguracji w dalszym ciągu zostaje zaktualizowana za pomocą magazynu konfiguracji.

Aby zachować ustawienia zostały zaktualizowane i unikaj zbyt wielu wywołań do magazynu konfiguracji, pamięć podręczna jest używana dla każdego ustawienia. Dopóki wartość ustawienia w pamięci podręcznej wygasł, operacja odświeżania nie aktualizuje wartości, nawet wtedy, gdy wartość została zmieniona w magazynie konfiguracji. Domyślny czas wygaśnięcia dla każdego żądania jest 30 sekund, ale może zostać zastąpiona, jeśli jest to wymagane.

W tym samouczku pokazano, jak zaimplementować dynamiczne aktualizacje konfiguracji w swoim kodzie. Opiera się on na aplikacji internetowej wprowadzonej w przewodnikach Szybki start. Przed kontynuowaniem należy zakończyć [tworzenie aplikacji platformy ASP.NET Core z konfiguracji aplikacji](./quickstart-aspnet-core-app.md) pierwszy.

Wykonaj kroki w tym samouczku, można użyć dowolnego edytora kodu. [Visual Studio Code](https://code.visualstudio.com/) doskonałym rozwiązaniem, która jest dostępna w Windows, macOS i platformy Linux.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Skonfiguruj aplikację, aby zaktualizować jej konfiguracji w odpowiedzi na zmiany w magazynie konfiguracji aplikacji.
> * Wstrzyknąć najnowszą konfigurację w kontrolerów danej aplikacji.

## <a name="prerequisites"></a>Wymagania wstępne

Aby skorzystać z tego samouczka, należy zainstalować [zestawu .NET Core SDK](https://dotnet.microsoft.com/download).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="reload-data-from-app-configuration"></a>Ponowne ładowanie danych z usługi App Configuration

1. Otwórz *Program.cs*i zaktualizuj `CreateWebHostBuilder` metody w celu dodania `config.AddAzureAppConfiguration()` metody.

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
                                      .Register("TestApp:Settings:Message")
                           });
                }
            })
            .UseStartup<Startup>();
    ```

    `ConfigureRefresh` Metoda jest używana do określenia ustawień, używane do aktualizowania danych konfiguracji z magazynu konfiguracji aplikacji, po wyzwoleniu operacji odświeżania. Aby rzeczywiście zainicjuje operację odświeżania, oprogramowanie pośredniczące odświeżania musi zostać skonfigurowany dla aplikacji odświeżyć dane konfiguracji, po wystąpieniu wszelkie zmiany.

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

3. Otwórz *Startup.cs*i zaktualizuj `ConfigureServices` metodę, aby powiązać dane konfiguracji do `Settings` klasy.

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

4. Aktualizacja `Configure` metody w celu dodania oprogramowaniu pośredniczącym, aby umożliwić ustawienia konfiguracji zarejestrowany dla Odśwież, aby być aktualizowane, gdy aplikacja internetowa ASP.NET Core w dalszym ciągu otrzymywać żądania.

    ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        app.UseAzureAppConfiguration();
        app.UseMvc();
    }
    ```
    
    Oprogramowanie pośredniczące korzysta z konfiguracji odświeżanie określony w `AddAzureAppConfiguration` method in Class metoda `Program.cs` wyzwalanie odświeżania dla każdego żądania odebranego przez aplikację internetową platformy ASP.NET Core. Dla każdego żądania jest wyzwalany, operacja odświeżania i biblioteki klienckiej sprawdza, jeśli wartość w pamięci podręcznej dla ustawień konfiguracji zarejestrowanych wygasły. Dla pamięci podręcznej wartości, które wygasły wartości ustawień są aktualizowane przy użyciu magazynu konfiguracji aplikacji, a pozostałe wartości pozostają niezmienione.
    
    > [!NOTE]
    > Domyślny czas wygaśnięcia pamięci podręcznej ustawienie konfiguracji to 30 sekund, ale może być zastąpiona przez wywołanie metody `SetCacheExpiration` metody w inicjatorze opcje przekazywany jako argument do `ConfigureRefresh` metody.

## <a name="use-the-latest-configuration-data"></a>Używanie najnowszych danych konfiguracji

1. Otwórz *HomeController.cs* w katalogu kontrolery i Dodaj odwołanie do `Microsoft.Extensions.Options` pakietu.

    ```csharp
    using Microsoft.Extensions.Options;
    ```

2. Aktualizacja `HomeController` klasy, aby otrzymać `Settings` za pomocą iniekcji zależności. Ponadto upewnij korzystanie z jej wartości.

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

3. Otwórz *Index.cshtml* w widokach > Strona główna katalogu i zastąp jego zawartość następującym skryptem:

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

4. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Wybierz **wszystkie zasoby**i wybierz wystąpienie magazynu konfiguracji aplikacji, które utworzono w przewodniku Szybki Start.

5. Wybierz **Eksplorator konfiguracji**i zaktualizuj wartości następujących kluczy:

    | Klucz | Wartość |
    |---|---|
    | TestAppSettings:BackgroundColor | zielony |
    | TestAppSettings:FontColor | lightGray |
    | TestAppSettings:Message | Dane z usługi Azure App Configuration — teraz z aktualizacjami na żywo! |

6. Odśwież stronę przeglądarki, aby zobaczyć nowe ustawienia konfiguracji. Więcej niż jednego odświeżenia strony przeglądarki może wymagać zmiany zostaną odzwierciedlone.

    ![Lokalne odświeżanie aplikacji z przewodnika Szybki start](./media/quickstarts/aspnet-core-app-launch-local-after.png)
    
    > [!NOTE]
    > Ponieważ ustawienia konfiguracji są buforowane, domyślny czas wygaśnięcia równej 30 sekund, wszelkie zmiany wprowadzone do ustawienia w konfiguracji sklepu byłyby tylko widoczne w aplikacji sieci web po pamięci podręcznej wygasł.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku dodano tożsamość usługi zarządzanej przez platformę Azure, aby usprawnić dostęp do usługi App Configuration i poprawić zarządzanie poświadczeniami w Twojej aplikacji. Aby dowiedzieć się więcej o tym, jak używać konfiguracji aplikacji, przejdź do przykładów interfejsu wiersza polecenia platformy Azure.

> [!div class="nextstepaction"]
> [Przykłady interfejsu wiersza polecenia](./cli-samples.md)
