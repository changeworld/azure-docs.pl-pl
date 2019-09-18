---
title: Application Insights platformy Azure dla aplikacji ASP.NET Core | Microsoft Docs
description: Monitoruj ASP.NET Core aplikacje sieci Web pod kątem dostępności, wydajności i użycia.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 3b722e47-38bd-4667-9ba4-65b7006c074c
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: mbullwin
ms.openlocfilehash: a48c2fdcce5126747f00cd3b901839864d438346
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/17/2019
ms.locfileid: "71058275"
---
# <a name="application-insights-for-aspnet-core-applications"></a>Application Insights aplikacji ASP.NET Core

W tym artykule opisano sposób włączania Application Insights dla aplikacji [ASP.NET Core](https://docs.microsoft.com/aspnet/core) . Po wykonaniu instrukcji przedstawionych w tym artykule Application Insights będzie zbierać żądania, zależności, wyjątki, liczniki wydajności, pulsy i dzienniki z aplikacji ASP.NET Core.

Przykład będziemy używać tutaj, gdy jest to [aplikacja MVC](https://docs.microsoft.com/aspnet/core/tutorials/first-mvc-app) , której `netcoreapp2.2`dotyczy. Te instrukcje można zastosować do wszystkich aplikacji ASP.NET Core.

## <a name="supported-scenarios"></a>Obsługiwane scenariusze

[Zestaw Application Insights SDK dla ASP.NET Core](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) może monitorować aplikacje niezależnie od tego, gdzie lub jak są uruchamiane. Jeśli aplikacja działa i ma łączność sieciową z platformą Azure, można zbierać dane telemetryczne. Monitorowanie Application Insights jest obsługiwane wszędzie tam, gdzie jest obsługiwany program .NET Core. Centrum pomocy technicznej:
* **System operacyjny**: Windows, Linux lub Mac.
* **Metoda hostingu**: W procesie lub w trakcie procesu. 
* **Metoda wdrożenia**: Platforma zależna lub samodzielna.
* **Serwer sieci Web**: Usługi IIS (Internet Information Server) lub Kestrel. 
* **Platforma hostingu**: Web Apps funkcja Azure App Service, maszyna wirtualna platformy Azure, platforma Docker, usługa Azure Kubernetes Service (AKS) i tak dalej.
* **ŚRODOWISKO IDE**: Visual Studio, VS Code lub wiersz polecenia.

> [!NOTE]
> Jeśli używasz ASP.NET Core 3,0 — wersja zapoznawcza wraz z Application Insights, użyj wersji [2.8.0-beta2](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore/2.8.0-beta2) lub nowszej. Jest to jedyna wersja znana pod kątem prawidłowego działania z ASP.NET Core 3,0. Ponadto program Visual Studio oparty na dołączaniu nie jest jeszcze obsługiwany w przypadku aplikacji ASP.NET Core 3,0.

## <a name="prerequisites"></a>Wymagania wstępne

- Działająca aplikacja ASP.NET Core. Jeśli musisz utworzyć aplikację ASP.NET Core, postępuj zgodnie z tym [samouczkiem ASP.NET Core](https://docs.microsoft.com/aspnet/core/getting-started/).
- Prawidłowy klucz Instrumentacji Application Insights. Ten klucz jest wymagany do wysyłania wszelkich danych telemetrycznych do Application Insights. Jeśli musisz utworzyć nowy zasób Application Insights, aby uzyskać klucz instrumentacji, zobacz [Tworzenie zasobu Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource).

## <a name="enable-application-insights-server-side-telemetry-visual-studio"></a>Włączanie telemetrii po stronie serwera Application Insights (Visual Studio)

1. Otwórz projekt w programie Visual Studio.

    > [!TIP]
    > Jeśli chcesz, możesz skonfigurować kontrolę źródła dla projektu, aby można było śledzić wszystkie zmiany, które Application Insights. Aby włączyć kontrolę źródła, wybierz pozycję **plik** > **Dodaj do kontroli źródła**.

2. Wybierz pozycję **projekt** > **Dodaj Telemetria usługi Application Insights**.

3. Wybierz pozycję **Rozpocznij**. Tekst tego zaznaczenia może się różnić w zależności od używanej wersji programu Visual Studio. W niektórych starszych wersjach zamiast tego jest używany przycisk **Rozpocznij bezpłatnie** .

4. Wybierz subskrypcję. Następnie wybierz pozycję**Rejestr** **zasobów** > .

5. Po dodaniu Application Insights do projektu upewnij się, że korzystasz z najnowszej stabilnej wersji zestawu SDK. Przejdź do **projektu** > **Zarządzanie pakietami** > NuGet**Microsoft. ApplicationInsights. AspNetCore**. Jeśli zachodzi taka potrzeba, wybierz pozycję **Aktualizuj**.

     ![Zrzut ekranu przedstawiający miejsce wybrania pakietu Application Insights na potrzeby aktualizacji](./media/asp-net-core/update-nuget-package.png)

6. Jeśli wykonano opcjonalną poradę i dodano projekt do kontroli źródła, przejdź do pozycji **Wyświetl** > **Team Explorer** > **zmiany**. Następnie wybierz każdy plik, aby wyświetlić widok diff o zmianach wprowadzonych przez Application Insights dane telemetryczne.

## <a name="enable-application-insights-server-side-telemetry-no-visual-studio"></a>Włącz Application Insights dane telemetryczne po stronie serwera (bez programu Visual Studio)

1. Zainstaluj [pakiet NuGet zestawu Application Insights SDK dla ASP.NET Core](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore). Zalecamy, aby zawsze używać najnowszej stabilnej wersji. Znajdź pełne informacje o wersji zestawu SDK w [repozytorium GitHub Open Source](https://github.com/Microsoft/ApplicationInsights-aspnetcore/releases).

    Poniższy przykład kodu pokazuje zmiany, które mają zostać dodane do `.csproj` pliku projektu.

    ```xml
        <ItemGroup>
          <PackageReference Include="Microsoft.ApplicationInsights.AspNetCore" Version="2.7.0" />
        </ItemGroup>
    ```

2. Dodaj `services.AddApplicationInsightsTelemetry();` do`ConfigureServices()` metody w`Startup` klasie, tak jak w poniższym przykładzie:

    ```csharp
        // This method gets called by the runtime. Use this method to add services to the container.
        public void ConfigureServices(IServiceCollection services)
        {
            // The following line enables Application Insights telemetry collection.
            services.AddApplicationInsightsTelemetry();
    
            // This code adds other services for your application.
            services.AddMvc();
        }
    ```

3. Skonfiguruj klucz Instrumentacji.

    Chociaż można podać klucz Instrumentacji jako argument `AddApplicationInsightsTelemetry`, zalecamy określenie klucza Instrumentacji w konfiguracji. Poniższy przykład kodu pokazuje, jak określić klucz Instrumentacji w programie `appsettings.json`. Upewnij się `appsettings.json` , że podczas publikowania jest kopiowany do folderu głównego aplikacji.

    ```json
        {
          "ApplicationInsights": {
            "InstrumentationKey": "putinstrumentationkeyhere"
          },
          "Logging": {
            "LogLevel": {
              "Default": "Warning"
            }
          }
        }
    ```

    Alternatywnie Określ klucz Instrumentacji w jednej z następujących zmiennych środowiskowych:

    * `APPINSIGHTS_INSTRUMENTATIONKEY`

    * `ApplicationInsights:InstrumentationKey`

    Na przykład:

    * `SET ApplicationInsights:InstrumentationKey=putinstrumentationkeyhere`

    * `SET APPINSIGHTS_INSTRUMENTATIONKEY=putinstrumentationkeyhere`

    `APPINSIGHTS_INSTRUMENTATIONKEY` Zazwyczaj określa klucz Instrumentacji dla aplikacji wdrożonych do Web Apps.

    > [!NOTE]
    > Klucz Instrumentacji określony w kodzie usługi WINS przez zmienną `APPINSIGHTS_INSTRUMENTATIONKEY`środowiskową, która jest usługą WINS w porównaniu z innymi opcjami.

## <a name="run-your-application"></a>Uruchamianie aplikacji

Uruchom aplikację i wprowadź do niej żądania. Dane telemetryczne powinny teraz przepływać do Application Insights. Zestaw Application Insights SDK automatycznie zbiera następujące dane telemetryczne.

|Żądania/zależności |Szczegóły|
|---------------|-------|
|Żądania | Przychodzące żądania sieci Web do aplikacji. |
|HTTP lub HTTPS | Wywołania wykonane przy `HttpClient`użyciu. |
|SQL | Wywołania wykonane przy `SqlClient`użyciu. |
|[Azure Storage](https://www.nuget.org/packages/WindowsAzure.Storage/) | Wywołania wykonane za pomocą klienta usługi Azure Storage. |
|[Zestaw SDK klienta EventHubs](https://www.nuget.org/packages/Microsoft.Azure.EventHubs) | Wersja 1.1.0 i nowsza. |
|[Zestaw SDK klienta ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus)| Wersja 3.0.0 i nowsza. |
|Azure Cosmos DB | Śledzone automatycznie tylko wtedy, gdy jest używany protokół HTTP/HTTPS. Application Insights nie przechwytuje trybu TCP. |

### <a name="performance-counters"></a>Liczniki wydajności

Obsługa [liczników wydajności](https://azure.microsoft.com/documentation/articles/app-insights-web-monitor-performance/) w ASP.NET Core jest ograniczona:

* Wersje SDK 2.4.1 i nowsze zbierają liczniki wydajności, jeśli aplikacja działa w usłudze Azure Web Apps (Windows).
* Wersja zestawu SDK 2.7.1 i nowsze zbiera liczniki wydajności, jeśli aplikacja działa w systemie Windows i `NETSTANDARD2.0` w wersji docelowej lub nowszej.
* W przypadku aplikacji przeznaczonych dla .NET Framework wszystkie wersje zestawu SDK obsługują liczniki wydajności.
* Wersje zestawu SDK 2.8.0-beta3 i nowsze obsługują licznik procesora/pamięci w systemie Linux. Żaden inny licznik nie jest obsługiwany w systemie Linux. Zalecanym sposobem uzyskiwania liczników systemowych w systemie Linux (i innych środowiskach innych niż Windows) jest użycie [EventCounters](#eventcounter)

### <a name="eventcounter"></a>EventCounter

[EventCounter](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.Tracing/documentation/EventCounterTutorial.md), to międzyplatformowa Metoda publikowania i używania liczników w programie .NET/.NET Core. Chociaż ta funkcja istniała wcześniej, nie było żadnych wbudowanych dostawców, którzy opublikowały te liczniki. Począwszy od platformy .NET Core 3,0, kilka liczników jest publikowanych z pola, takich jak liczniki CLR, liczniki ASP.NET Core itd.

Wersje zestawu SDK 2.8.0-beta3 i nowsze obsługują kolekcję EventCounters. Domyślnie zestaw SDK zbiera następujące liczniki, a te liczniki mogą być badane w Eksplorator metryk lub przy użyciu zapytania analizy w tabeli PerformanceCounter. Nazwa liczników będzie mieć postać "Kategoria | Licznik ".

|Kategoria | Licznik|
|---------------|-------|
|System. Runtime | Użycie procesora CPU |
|System. Runtime | zestaw roboczy |
|System. Runtime | GC-sterta-rozmiar |
|System. Runtime | Gen-0-GC-Count |
|System. Runtime | Gen-1-GC-Count |
|System. Runtime | Gen-2-GC-Count |
|System. Runtime | czas pracy w pamięci podręcznej |
|System. Runtime | rozmiar generacji (0) |
|System. Runtime | Gen-1-size |
|System. Runtime | Gen-2 — rozmiar |
|System. Runtime | LOH — rozmiar |
|System. Runtime | Alokacja — stawka |
|System. Runtime | liczba zestawów |
|System. Runtime | Liczba wyjątków |
|System. Runtime | wątków — liczba wątków |
|System. Runtime | monitorowanie-blokowanie-rywalizacja-liczba |
|System. Runtime | puli wątków — długość kolejki |
|System. Runtime | z puli wątków — liczba elementów zakończonych |
|System. Runtime | Active-Timer-Count |
|Microsoft.AspNetCore.Hosting | Liczba żądań na sekundę |
|Microsoft.AspNetCore.Hosting | łącznie — żądania |
|Microsoft.AspNetCore.Hosting | bieżące żądania |
|Microsoft.AspNetCore.Hosting | Nieudane żądania |

### <a name="ilogger-logs"></a>Dzienniki ILogger

[Dzienniki ILogger](https://docs.microsoft.com/azure/azure-monitor/app/ilogger) o ważności `Warning` lub większe są automatycznie przechwytywane w wersjach zestawu SDK 2.7.0-beta3 i nowszych.

### <a name="live-metrics"></a>Metryki na żywo

Wyświetlenie telemetrii w portalu może potrwać kilka minut. Aby szybko upewnić się, że wszystko działa, najlepszym rozwiązaniem jest korzystanie z [metryk na żywo](https://docs.microsoft.com/azure/application-insights/app-insights-live-stream) podczas wykonywania żądań do uruchomionej aplikacji.

## <a name="enable-client-side-telemetry-for-web-applications"></a>Włącz telemetrię po stronie klienta dla aplikacji sieci Web

Powyższe kroki są wystarczające, aby ułatwić rozpoczęcie zbierania danych telemetrycznych po stronie serwera. Jeśli aplikacja zawiera składniki po stronie klienta, wykonaj kolejne kroki, aby rozpocząć zbieranie danych [telemetrycznych użycia](https://docs.microsoft.com/azure/azure-monitor/app/usage-overview).

1. W `_ViewImports.cshtml`, Dodaj iniekcję:

    ```cshtml
        @inject Microsoft.ApplicationInsights.AspNetCore.JavaScriptSnippet JavaScriptSnippet
    ```

2. W `_Layout.cshtml`programie Wstaw `HtmlHelper`nakońcusekcji, ale przed jakimkolwiek innym skryptem. `<head>` Jeśli chcesz zgłosić dowolną niestandardową telemetrię JavaScript ze strony, wsuń ją po tym fragmencie kodu:

    ```cshtml
        @Html.Raw(JavaScriptSnippet.FullScript)
        </head>
    ```

Nazwy `.cshtml` plików, do których odwołuje się wcześniej, pochodzą z domyślnego szablonu aplikacji MVC. Ostatecznie, jeśli chcesz prawidłowo włączyć monitorowanie po stronie klienta dla aplikacji, fragment kodu JavaScript musi znajdować się w `<head>` sekcji każdej strony aplikacji, która ma być monitorowana. Ten cel można osiągnąć w przypadku tego szablonu aplikacji przez dodanie fragmentu kodu JavaScript `_Layout.cshtml`do programu. 

Jeśli projekt nie zawiera `_Layout.cshtml`, nadal możesz dodać [monitorowanie po stronie klienta](https://docs.microsoft.com/azure/azure-monitor/app/website-monitoring). Można to zrobić przez dodanie fragmentu kodu JavaScript do pliku równoważnego, który kontroluje `<head>` wszystkie strony w aplikacji. Można też dodać fragment kodu do wielu stron, ale to rozwiązanie jest trudne do utrzymania i zwykle nie jest to zalecane.

## <a name="configure-the-application-insights-sdk"></a>Konfigurowanie zestawu SDK Application Insights

Można dostosować zestaw Application Insights SDK dla ASP.NET Core, aby zmienić konfigurację domyślną. Użytkownicy zestawu SDK Application Insights ASP.NET mogą znać zmianę konfiguracji przy użyciu `ApplicationInsights.config` lub modyfikując. `TelemetryConfiguration.Active` Konfigurację można zmienić inaczej dla ASP.NET Core. Dodaj zestaw ASP.NET Core SDK do aplikacji i skonfiguruj go przy ASP.NET Core użyciu wbudowanego iniekcji [zależności](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection). Prawie wszystkie zmiany konfiguracji w `ConfigureServices()` metodzie `Startup.cs` klasy, chyba że nastąpi przekierowanie w inny sposób. Poniższe sekcje zawierają więcej informacji.

> [!NOTE]
> W przypadku aplikacji ASP.NET Core zmiana konfiguracji przez modyfikację `TelemetryConfiguration.Active` nie jest obsługiwana.

### <a name="using-applicationinsightsserviceoptions"></a>Korzystanie z ApplicationInsightsServiceOptions

Kilka typowych ustawień można zmodyfikować, przechodząc `ApplicationInsightsServiceOptions` do `AddApplicationInsightsTelemetry`, jak w poniższym przykładzie:

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        Microsoft.ApplicationInsights.AspNetCore.Extensions.ApplicationInsightsServiceOptions aiOptions
                    = new Microsoft.ApplicationInsights.AspNetCore.Extensions.ApplicationInsightsServiceOptions();
        // Disables adaptive sampling.
        aiOptions.EnableAdaptiveSampling = false;

        // Disables QuickPulse (Live Metrics stream).
        aiOptions.EnableQuickPulseMetricStream = false;
        services.AddApplicationInsightsTelemetry(aiOptions);
    }
```

Pełna lista ustawień w programie`ApplicationInsightsServiceOptions`

|Ustawienie | Opis | Domyślny
|---------------|-------|-------
|EnableQuickPulseMetricStream | Włącz/Wyłącz funkcję LiveMetrics | true
|EnableAdaptiveSampling | Włącz/Wyłącz próbkowanie adaptacyjne | true
|EnableHeartbeat | Funkcja włączania/wyłączania pulsu, która okresowo (15 min domyślnie) wysyła metrykę niestandardową o nazwie "HeartBeatState", podając informacje o środowisku uruchomieniowym, takim jak wersja platformy .NET, informacje dotyczące środowiska platformy Azure, jeśli ma to zastosowanie itd. | true
|AddAutoCollectedMetricExtractor | Włącz/Wyłącz Ekstraktor AutoCollectedMetrics, który jest TelemetryProcessor, który wysyła wstępnie zagregowane metryki dotyczące żądań/zależności przed pobraniem próbek. | true
|RequestCollectionOptions.TrackExceptions | Włącz/Wyłącz raportowanie nieobsłużonego śledzenia wyjątków przez moduł kolekcji żądań. | wartość false w programie STANDARDowym 2.0 (ponieważ wyjątki są śledzone za pomocą ApplicationInsightsLoggerProvider), true w przeciwnym razie.

Zobacz [ustawienia konfigurowalne w `ApplicationInsightsServiceOptions` programie](https://github.com/microsoft/ApplicationInsights-aspnetcore/blob/develop/src/Microsoft.ApplicationInsights.AspNetCore/Extensions/ApplicationInsightsServiceOptions.cs) , aby uzyskać najbardziej aktualną listę.

### <a name="sampling"></a>Próbkowanie

Zestaw Application Insights SDK dla ASP.NET Core obsługuje próbkowanie stałe i adaptacyjne. Próbkowanie adaptacyjne jest domyślnie włączone. 

Aby uzyskać więcej informacji, zobacz [Konfigurowanie adaptacyjnego próbkowania dla aplikacji ASP.NET Core](../../azure-monitor/app/sampling.md#configuring-adaptive-sampling-for-aspnet-core-applications).

### <a name="adding-telemetryinitializers"></a>Dodawanie TelemetryInitializers

[Inicjatory](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#add-properties-itelemetryinitializer) telemetrii umożliwiają definiowanie właściwości globalnych, które są wysyłane ze wszystkimi danymi telemetrycznymi.

Dodaj nowe `TelemetryInitializer` `DependencyInjection` do kontenera, jak pokazano w poniższym kodzie. Zestaw SDK automatycznie pobiera dowolnych `TelemetryInitializer` dodanych `DependencyInjection` do kontenera.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddSingleton<ITelemetryInitializer, MyCustomTelemetryInitializer>();
    }
```

### <a name="removing-telemetryinitializers"></a>Usuwanie TelemetryInitializers

Inicjatory telemetrii są obecne domyślnie. Aby usunąć wszystkie lub określonych inicjatorów telemetrii, użyj następującego przykładowego kodu *po* wywołaniu `AddApplicationInsightsTelemetry()`.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetry();

        // Remove a specific built-in telemetry initializer
        var tiToRemove = services.FirstOrDefault<ServiceDescriptor>
                         (t => t.ImplementationType == typeof(AspNetCoreEnvironmentTelemetryInitializer));
        if (tiToRemove != null)
        {
            services.Remove(tiToRemove);
        }

        // Remove all initializers
        // This requires importing namespace by using Microsoft.Extensions.DependencyInjection.Extensions;
        services.RemoveAll(typeof(ITelemetryInitializer));
    }
```

### <a name="adding-telemetry-processors"></a>Dodawanie procesorów telemetrii

Do `TelemetryConfiguration` przy użyciu metody `AddApplicationInsightsTelemetryProcessor` rozszerzenia w systemie `IServiceCollection`można dodać niestandardowe procesory telemetrii. W [zaawansowanych scenariuszach filtrowania](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#filtering-itelemetryprocessor) używasz procesorów telemetrycznych, aby umożliwić bardziej bezpośrednią kontrolę nad tym, co zostało dołączone lub wykluczone z danych telemetrycznych wysyłanych do usługi Application Insights. Użyj poniższego przykładu.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        // ...
        services.AddApplicationInsightsTelemetry();
        services.AddApplicationInsightsTelemetryProcessor<MyFirstCustomTelemetryProcessor>();

        // If you have more processors:
        services.AddApplicationInsightsTelemetryProcessor<MySecondCustomTelemetryProcessor>();
    }
```

### <a name="configuring-or-removing-default-telemetrymodules"></a>Konfigurowanie lub usuwanie TelemetryModules domyślnego

Application Insights używa modułów telemetrycznych do automatycznego zbierania przydatnej telemetrii dotyczącej konkretnych obciążeń, bez konieczności ręcznego śledzenia przez użytkownika.

Następujące moduły automatycznego zbierania są domyślnie włączone. Te moduły są odpowiedzialne za automatyczne zbieranie danych telemetrycznych. Można je wyłączyć lub skonfigurować, aby zmienić zachowanie domyślne.

* `RequestTrackingTelemetryModule`— Zbiera RequestTelemetry z przychodzących żądań sieci Web.
* `DependencyTrackingTelemetryModule`— Zbiera DependencyTelemetry z wychodzących wywołań http i wywołań SQL.
* `PerformanceCollectorModule`— Zbiera dane liczniki wydajności systemu Windows.
* `QuickPulseTelemetryModule`— Zbiera dane telemetryczne na potrzeby wyświetlania w portalu metryk na żywo.
* `AppServicesHeartbeatTelemetryModule`— Zbiera kiery (wysyłane jako metryki niestandardowe) o Azure App Service środowisku, w którym aplikacja jest hostowana.
* `AzureInstanceMetadataTelemetryModule`— Zbiera kiery (wysyłane jako metryki niestandardowe) o środowisku maszyny wirtualnej platformy Azure, w którym aplikacja jest hostowana.
* `EventCounterCollectionModule`-Zbiera [EventCounters.](#eventcounter).. Ten moduł jest nową funkcją i jest dostępny w zestawie SDK w wersji 2.8.0-beta3 lub nowszej.

Aby skonfigurować wszystkie ustawienia `TelemetryModule`domyślne, należy użyć metody `ConfigureTelemetryModule<T>` rozszerzenia `IServiceCollection`na, jak pokazano w poniższym przykładzie.

```csharp
using Microsoft.ApplicationInsights.DependencyCollector;
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector;

    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetry();

        // The following configures DependencyTrackingTelemetryModule.
        // Similarly, any other default modules can be configured.
        services.ConfigureTelemetryModule<DependencyTrackingTelemetryModule>((module, o) =>
                        {
                            module.EnableW3CHeadersInjection = true;
                        });

        // The following removes all default counters from EventCounterCollectionModule, and adds a single one.
        services.ConfigureTelemetryModule<EventCounterCollectionModule>(
                            (module, o) =>
                            {
                                module.Counters.Clear();
                                module.Counters.Add(new EventCounterCollectionRequest("System.Runtime", "gen-0-size"));
                            }
                        );

        // The following removes PerformanceCollectorModule to disable perf-counter collection.
        // Similarly, any other default modules can be removed.
        var performanceCounterService = services.FirstOrDefault<ServiceDescriptor>(t => t.ImplementationType == typeof(PerformanceCollectorModule));
        if (performanceCounterService != null)
        {
         services.Remove(performanceCounterService);
        }
    }
```

### <a name="configuring-a-telemetry-channel"></a>Konfigurowanie kanału telemetrii

Domyślny kanał to `ServerTelemetryChannel`. Można go zastąpić, jak pokazano w poniższym przykładzie.

```csharp
using Microsoft.ApplicationInsights.Channel;

    public void ConfigureServices(IServiceCollection services)
    {
        // Use the following to replace the default channel with InMemoryChannel.
        // This can also be applied to ServerTelemetryChannel.
        services.AddSingleton(typeof(ITelemetryChannel), new InMemoryChannel() {MaxTelemetryBufferCapacity = 19898 });

        services.AddApplicationInsightsTelemetry();
    }
```

### <a name="disable-telemetry-dynamically"></a>Dynamiczne wyłączanie telemetrii

Jeśli chcesz warunkowo i dynamicznie wyłączyć telemetrię, możesz rozwiązać `TelemetryConfiguration` wystąpienie z kontenerem iniekcji zależności ASP.NET Core dowolnym miejscu w kodzie i ustawić `DisableTelemetry` dla niego flagę.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetry();
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env, TelemetryConfiguration configuration)
    {
        configuration.DisableTelemetry = true;
        ...
    }
```

Należy zauważyć, że powyższe nie zapobiega zbieraniu danych telemetrycznych przez moduły Autocollection. Tylko wysyłanie danych telemetrycznych do Application Insights jest wyłączone z powyższym podejściem. Jeśli określony moduł autokolekcjonowania nie jest potrzebny, najlepszym rozwiązaniem jest [usunięcie modułu telemetrii](#configuring-or-removing-default-telemetrymodules)

## <a name="frequently-asked-questions"></a>Często zadawane pytania

### <a name="how-can-i-track-telemetry-thats-not-automatically-collected"></a>Jak mogę śledzić dane telemetryczne, które nie są zbierane automatycznie?

Pobierz wystąpienie programu `TelemetryClient` przy użyciu iniekcji konstruktora i Wywołaj wymaganą `TrackXXX()` metodę. Nie zalecamy tworzenia nowych `TelemetryClient` wystąpień w aplikacji ASP.NET Core. Pojedyncze wystąpienie `TelemetryClient` jest już zarejestrowane `DependencyInjection` w kontenerze, które jest udostępniane `TelemetryConfiguration` z resztą telemetrii. Tworzenie nowego `TelemetryClient` wystąpienia jest zalecane tylko wtedy, gdy wymaga konfiguracji, która jest oddzielona od reszty telemetrii. 

Poniższy przykład pokazuje, jak śledzić dodatkową telemetrię z kontrolera.

```csharp
using Microsoft.ApplicationInsights;

public class HomeController : Controller
{
    private TelemetryClient telemetry;

    // Use constructor injection to get a TelemetryClient instance.
    public HomeController(TelemetryClient telemetry)
    {
        this.telemetry = telemetry;
    }

    public IActionResult Index()
    {
        // Call the required TrackXXX method.
        this.telemetry.TrackEvent("HomePageRequested");
        return View();
    }
```

Aby uzyskać więcej informacji na temat niestandardowego raportowania danych w Application Insights, zobacz [Application Insights informacje o interfejsie API metryk niestandardowych](https://docs.microsoft.com/azure/azure-monitor/app/api-custom-events-metrics/).

### <a name="some-visual-studio-templates-used-the-useapplicationinsights-extension-method-on-iwebhostbuilder-to-enable-application-insights-is-this-usage-still-valid"></a>Niektóre szablony programu Visual Studio używały metody rozszerzenia UseApplicationInsights () w IWebHostBuilder, aby umożliwić Application Insights. Czy to użycie jest nadal ważne?

Tak, włączenie Application Insights przy użyciu tej metody jest prawidłowe. Ta technika jest używana w dołączaniu do programu Visual Studio i w rozszerzeniach Web Apps. Zaleca się jednak używanie `services.AddApplicationInsightsTelemetry()` , ponieważ zapewnia przeciążenia w celu kontrolowania niektórych konfiguracji. Obie metody wykonują te same operacje wewnętrznie, dlatego jeśli nie musisz stosować konfiguracji niestandardowej, możesz wywołać dowolną metodę.

`IWebHostBuilder`Program jest zastępowany `IHostBuilder` w programie w ASP.NET Core 3,0 i aby uniknąć pomyłek, Application Insights wersja 2.8.0-beta3 lub nowszym oznacza metodę UseApplicationInsights () jako przestarzałą i zostanie usunięta w następnej wersji głównej.

### <a name="im-deploying-my-aspnet-core-application-to-web-apps-should-i-still-enable-the-application-insights-extension-from-web-apps"></a>Wdrażam aplikację ASP.NET Core w Web Apps. Czy mimo to włączyć rozszerzenie Application Insights z poziomu Web Apps?

Jeśli zestaw SDK został zainstalowany w czasie kompilacji, jak pokazano w tym artykule, nie musisz włączać [rozszerzenia Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps) w portalu App Service. Nawet jeśli rozszerzenie jest zainstalowane, zostanie wycofane po wykryciu, że zestaw SDK został już dodany do aplikacji. Jeśli włączysz Application Insights z rozszerzenia, nie musisz instalować i aktualizować zestawu SDK. Jeśli jednak włączysz Application Insights, wykonując instrukcje podane w tym artykule, masz większą elastyczność, ponieważ:

   * Dane telemetryczne Application Insights będą nadal działały w:
       * Wszystkie systemy operacyjne, w tym Windows, Linux i Mac.
       * Wszystkie tryby publikowania, w tym samodzielny lub zależny od platformy.
       * Wszystkie platformy docelowe, łącznie z pełnymi .NET Framework.
       * Wszystkie opcje hostingu, w tym Web Apps, maszyny wirtualne, Linux, kontenery, usługa Azure Kubernetes oraz hosting spoza platformy Azure.
       * Wszystkie wersje programu .NET Core, w tym wersje zapoznawcze.
   * Dane telemetryczne są widoczne lokalnie podczas debugowania z programu Visual Studio.
   * Za pomocą `TrackXXX()` interfejsu API można śledzić dodatkową telemetrię niestandardową.
   * Masz pełną kontrolę nad konfiguracją.

### <a name="can-i-enable-application-insights-monitoring-by-using-tools-like-status-monitor"></a>Czy można włączyć monitorowanie Application Insights przy użyciu narzędzi takich jak monitor stanu?

Nie. [Monitor stanu](https://docs.microsoft.com/azure/azure-monitor/app/monitor-performance-live-website-now) i [Monitor stanu v2](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-overview) obsługują obecnie tylko ASP.NET 4. x.

### <a name="is-application-insights-automatically-enabled-for-my-aspnet-core-20-application"></a>Czy Application Insights jest automatycznie włączona dla aplikacji My ASP.NET Core 2,0?

Pakiet `Microsoft.AspNetCore.All` 2.1.0 2,0 Application Insights zawiera zestaw SDK (wersja). Jeśli aplikacja zostanie uruchomiona w debugerze programu Visual Studio, program Visual Studio włącza Application Insights i wyświetla dane telemetryczne lokalnie w samym środowisku IDE. Telemetria nie została wysłana do usługi Application Insights, jeśli nie określono klucza Instrumentacji. Zalecamy wykonanie instrukcji przedstawionych w tym artykule, aby włączyć Application Insights nawet dla aplikacji 2,0.

### <a name="if-i-run-my-application-in-linux-are-all-features-supported"></a>Czy w przypadku uruchamiania aplikacji w systemie Linux są obsługiwane wszystkie funkcje?

Tak. Obsługa funkcji dla zestawu SDK jest taka sama na wszystkich platformach z następującymi wyjątkami:

* Liczniki wydajności są obsługiwane tylko w systemie Windows.
* Mimo że `ServerTelemetryChannel` jest włączona domyślnie, jeśli aplikacja działa w systemie Linux lub MacOS, kanał nie tworzy automatycznie lokalnego folderu magazynu, aby zapewnić telemetrię tymczasowo w przypadku problemów z siecią. Z powodu tego ograniczenia dane telemetryczne są tracone w przypadku wystąpienia tymczasowych problemów z siecią lub serwerem. Aby obejść ten problem, należy skonfigurować lokalny folder dla kanału:

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;

    public void ConfigureServices(IServiceCollection services)
    {
        // The following will configure the channel to use the given folder to temporarily
        // store telemetry items during network or Application Insights server issues.
        // User should ensure that the given folder already exists
        // and that the application has read/write permissions.
        services.AddSingleton(typeof(ITelemetryChannel),
                                new ServerTelemetryChannel () {StorageFolder = "/tmp/myfolder"});
        services.AddApplicationInsightsTelemetry();
    }
```

## <a name="open-source-sdk"></a>Zestaw SDK open source

[Odczytuj i współtworzyć kod](https://github.com/Microsoft/ApplicationInsights-aspnetcore#recent-updates).

## <a name="video"></a>Połączenia wideo

- Zapoznaj się z tym zewnętrznym filmem krok po kroku, aby [skonfigurować Application Insights z użyciem platformy .NET Core i programu Visual Studio](https://www.youtube.com/watch?v=NoS9UhcR4gA&t) od podstaw.
- Zapoznaj się z tym zewnętrznym filmem krok po kroku, aby [skonfigurować Application Insights przy użyciu platformy .NET Core i Visual Studio Code](https://youtu.be/ygGt84GDync) od podstaw.

## <a name="next-steps"></a>Następne kroki

* [Eksploruj przepływy użytkowników](../../azure-monitor/app/usage-flows.md) , aby zrozumieć, jak użytkownicy nawigują przez aplikację.
* [Skonfiguruj kolekcję migawek](https://docs.microsoft.com/azure/application-insights/app-insights-snapshot-debugger) , aby zobaczyć stan kodu źródłowego i zmiennych w momencie zgłaszania wyjątku.
* [Użyj interfejsu API,](../../azure-monitor/app/api-custom-events-metrics.md) aby wysyłać własne zdarzenia i metryki w celu uzyskania szczegółowego widoku wydajności i użycia aplikacji.
* Korzystaj z [testów dostępności](../../azure-monitor/app/monitor-web-app-availability.md) , aby stale sprawdzać swoją aplikację na całym świecie.
* [Wstrzykiwanie zależności w ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection)
