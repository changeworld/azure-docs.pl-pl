---
title: Użyj konfiguracji dla środowiska
titleSuffix: Azure App Configuration
description: Użyj etykiet, aby podać wartości konfiguracji dla środowiska
ms.service: azure-app-configuration
author: lisaguthrie
ms.topic: conceptual
ms.date: 3/12/2020
ms.author: lcozzens
ms.openlocfilehash: d1a3323154fc73b453e041a064cfcd36299b8a08
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80056797"
---
# <a name="use-labels-to-enable-different-configurations-for-different-environments"></a>Użyj etykiet, aby włączyć różne konfiguracje dla różnych środowisk

Wiele aplikacji musi używać różnych konfiguracji dla różnych środowisk. Załóżmy, że aplikacja ma wartość konfiguracji, która definiuje parametry połączenia do użycia dla jego bazy danych zaplecza. Deweloperzy aplikacji używają innej bazy danych niż używana w produkcji. Parametry połączenia bazy danych używane przez aplikację musi ulec zmianie w miarę przenoszenia aplikacji z rozwoju do produkcji.

W konfiguracji aplikacji platformy Azure można użyć *etykiet* do definiowania różnych wartości dla tego samego klucza. Na przykład można zdefiniować pojedynczy klucz o różnych wartościach *dla rozwoju* i *produkcji*. Można określić, które etykiety mają być ładowane podczas łączenia się z konfiguracją aplikacji.

Aby zademonstrować tę funkcję, zmodyfikujemy aplikację sieci web utworzoną w [przewodniku Szybki start: utwórz aplikację ASP.NET Core z konfiguracją aplikacji platformy Azure,](./quickstart-aspnet-core-app.md) aby używać różnych ustawień konfiguracji do tworzenia i produkcji. Przed kontynuowaniem należy ukończyć program Szybki start.

## <a name="specify-a-label-when-adding-a-configuration-value"></a>Określanie etykiety podczas dodawania wartości konfiguracji

W witrynie Azure portal przejdź do **Eksploratora konfiguracji** i znajdź klucz *TestApp:Settings:FontColor* utworzony w przewodniku Szybki start. Wybierz jego menu kontekstowe, a następnie kliknij przycisk **Dodaj wartość**.

> [!div class="mx-imgBorder"]
> ![Element menu Dodaj wartość](media/labels-add-value.png)

Na ekranie **Dodaj wartość** wprowadź **wartość** **czerwoną** i **etykietę** **rozwoju**. Pozostaw **typ zawartości** pusty. Wybierz przycisk **Zastosuj**.

## <a name="loading-configuration-values-with-a-specified-label"></a>Ładowanie wartości konfiguracji z określoną etykietą

Domyślnie usługa Konfiguracja aplikacji platformy Azure ładuje tylko wartości konfiguracji bez etykiety. Jeśli zdefiniowano etykiety dla wartości konfiguracji, należy określić etykiety, które mają być używane podczas łączenia się z konfiguracją aplikacji.

W ostatniej sekcji utworzono inną wartość konfiguracji dla środowiska *deweloperskiego.* Zmienną `HostingEnvironment.EnvironmentName` służy dynamicznie określać, w którym środowisku aplikacja jest aktualnie uruchomiona. Aby dowiedzieć się więcej, zobacz [Używanie wielu środowisk w ASP.NET Core](/aspnet/core/fundamentals/environments).

Załaduj wartości konfiguracji z etykietą odpowiadającą `Select` bieżącemu środowisku, przekazując nazwę środowiska do metody:

```csharp
        public static IHostBuilder CreateHostBuilder(string[] args) =>
            Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();
                config.AddAzureAppConfiguration(options =>
                    options
                        .Connect(Environment.GetEnvironmentVariable("AppConfigConnectionString"))
                        // Load configuration values with no label
                        .Select(KeyFilter.Any, LabelFilter.Null)
                        // Override with any configuration values specific to current hosting env
                        .Select(KeyFilter.Any, hostingContext.HostingEnvironment.EnvironmentName)
                );
            })
            .UseStartup<Startup>());
```

> [!IMPORTANT]
> Powyższy fragment kodu ładuje ciąg połączenia konfiguracja aplikacji `AppConfigConnectionString`ze zmiennej środowiskowej o nazwie . Upewnij się, że ta zmienna środowiskowa jest ustawiona prawidłowo.

Metoda `Select` jest wywoływana dwa razy. Za pierwszym razem ładuje wartości konfiguracji bez etykiety. Następnie ładuje wartości konfiguracji z etykietą odpowiadającą bieżącemu środowisku. Te wartości specyficzne dla środowiska zastępują wszystkie odpowiadające im wartości bez etykiety. Nie trzeba definiować wartości specyficzne dla środowiska dla każdego klucza. Jeśli klucz nie ma wartości z etykietą odpowiadającą bieżącemu środowisku, używana jest wartość bez etykiety.

## <a name="testing-in-different-environments"></a>Testowanie w różnych środowiskach

Aby przetestować różne wartości `launchSettings.json` konfiguracji, `Properties` otwórz plik w katalogu. Znajdź `config` wpis `profiles`w obszarze . W `environmentVariables` sekcji ustaw `ASPNETCORE_ENVIRONMENT` zmienną `Production`na .

Po ustawieniu nowych wartości skompiluj i uruchom aplikację.

```dotnetcli
dotnet build
dotnet run
```

Przejdź do pliku `http://localhost:5000`. Zauważysz, że kolor czcionki jest czarny.

![Aplikacja sieci Web uruchomiona z konfiguracją produkcyjną](media/labels-website-prod.png)

Teraz `launchSettings.json` zaktualizuj, aby ustawić zmienną `ASPNETCORE_ENVIRONMENT` na `Development`. Uruchom ponownie polecenie `dotnet run`. Zauważysz, że kolor czcionki jest teraz czerwony. Dzieje się tak, ponieważ aplikacja `TestApp:Settings:FontColor` używa teraz `Development` wartości, która ma etykietę. Wszystkie inne wartości konfiguracji pozostają takie same jak ich wartości produkcji.

![Aplikacja sieci Web uruchomiona z konfiguracją programistycznej](media/labels-website-dev.png)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Konfiguracja w ASP.NET Core](/aspnet/core/fundamentals/configuration/)
