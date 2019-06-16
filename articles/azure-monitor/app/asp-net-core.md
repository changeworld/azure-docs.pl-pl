---
title: Usługi Azure Application Insights dla platformy ASP.NET Core | Dokumentacja firmy Microsoft
description: Monitorowanie aplikacji sieci web platformy ASP.NET Core dla dostępności, wydajności i użycia.
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
ms.openlocfilehash: cb7ace20fd0a59dafff3d7f8240f54c3c8e12492
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66226397"
---
# <a name="application-insights-for-aspnet-core-applications"></a>Usługa Application Insights dla aplikacji platformy ASP.NET Core

W tym artykule opisano sposób włączania usługi Application Insights dla [platformy ASP.NET Core](https://docs.microsoft.com/aspnet/core) aplikacji. Po wykonaniu instrukcji zawartych w tym artykule, Application Insights rozpocznie zbieranie żądania, zależności, wyjątki, liczniki wydajności, pulsów i dzienniki z aplikacji ASP.NET Core. Przykładowa aplikacja jest [aplikacji MVC](https://docs.microsoft.com/aspnet/core/tutorials/first-mvc-app) przeznaczonych dla `netcoreapp2.2`, ale te instrukcje mają zastosowanie do wszystkich aplikacji platformy ASP.NET Core.

## <a name="supported-scenarios"></a>Obsługiwane scenariusze

[Application Insights SDK (Software Development Kit) dla platformy ASP.NET Core](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) można monitorować aplikacje niezależnie od tego, gdzie i jak aplikacja jest uruchomiona. Jeśli aplikacja jest uruchomiona i ma połączenie sieciowe na platformie Azure, można zbierać dane telemetryczne. Oznacza to, że usługi Application Insights jest obsługiwane monitorowanie wszędzie tam, gdzie platformy .NET Core jest obsługiwane. Ta obsługa obejmuje dowolnego systemu operacyjnego (Windows, Linux, Mac), metody hostowania (vs w procesie poza procesem), metoda wdrażania (niezależna vs zależny od struktury), serwer sieci Web (IIS, Kestrel), platformy hostingowej (aplikacje internetowe platformy Azure, maszyny Wirtualnej platformy Azure, Docker, Usługa Azure Kubernetes Service (AKS) itd.) lub w środowisku IDE (wiersz polecenia programu Visual Studio, program VS Code).

## <a name="prerequisites"></a>Wymagania wstępne

- Ć funkcjonalną aplikację ASP.NET Core. Postępuj zgodnie z [przewodnik Wprowadzenie do platformy ASP.NET Core wprowadzenie](https://docs.microsoft.com/aspnet/core/getting-started/) do tworzenia aplikacji platformy ASP.NET Core, jeśli to konieczne.
- Prawidłowy klucz usługi Application Insights instrumentacji, który jest wymagany do wysyłania żadnych danych telemetrycznych do usługi Application Insights. Postępuj zgodnie z [Utwórz zasób instrukcje](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource) do utworzenia nowego zasobu usługi Application Insights, w razie potrzeby i uzyskiwanie klucza instrumentacji.

## <a name="enable-application-insights-server-side-telemetry-visual-studio"></a>Włączanie danych telemetrycznych usługi Application Insights po stronie serwera (Visual Studio)

1. Otwórz swój projekt w programie Visual Studio.

    > [!TIP]
    > Podczas nie krok obowiązkowy może być przydatne do skonfigurowania kontroli źródła w projekcie, co pozwala na śledzenie wszystkich zmian wprowadzonych przez usługę Application Insights. Aby umożliwić wybór kontroli źródła **pliku** > **Dodaj do kontroli źródła**.

2. Wybierz **projektu** > **Dodaj Telemetrię usługi Application Insights**.

3. Wybierz **wprowadzenie**. (W zależności od używanej wersji programu Visual Studio, tekst może się nieco różnić. Niektóre starsze wersje mają **Rozpocznij za darmo** zamiast tego przycisku.)

4. Wybierz swoją subskrypcję, a następnie wybierz **zasobów** > **zarejestrować**.

5. Po dodaniu usługi Application Insights do projektu, sprawdź, że używasz stabilne najnowszą wersję zestawu SDK. Przejdź do **projektu** > **Zarządzaj pakietami NuGet** > **Microsoft.ApplicationInsights.AspNetCore** > w razie potrzeby wybierz **Aktualizacji**.

     ![Zrzut ekranu przedstawiający Zarządzanie ekranu pakietu NuGet za pomocą pakietu usługi Application Insights wybrany do aktualizacji](./media/asp-net-core/update-nuget-package.png)

6. Jeśli następnie opcjonalnie Porada i dodać projekt do kontroli źródła możesz wtedy przejść do **widoku** > **Team Explorer** > **zmiany** i Wybierz każdego pliku dla widoku różnic zmian wprowadzonych od dodania telemetria usługi Application Insights.

## <a name="enable-application-insights-server-side-telemetry-without-visual-studio"></a>Włącz usługę Application Insights telemetrii po stronie serwera (bez programu Visual Studio)

1. Zainstaluj [pakietu NuGet zestawu SDK usługi Application Insights dla platformy ASP.NET Core](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore). Firma Microsoft zaleca, aby zawsze używać najnowszej stabilnej wersji. Pełne uwagi do wersji zestawu SDK można znaleźć na [Otwórz źródło repozytorium GitHub](https://github.com/Microsoft/ApplicationInsights-aspnetcore/releases).

    Poniższy fragment kodu pokazuje zmiany, które mają zostać dodane do projektu `.csproj` pliku.

    ```xml
        <ItemGroup>
          <PackageReference Include="Microsoft.ApplicationInsights.AspNetCore" Version="2.6.1" />
        </ItemGroup>
    ```

2. Dodaj `services.AddApplicationInsightsTelemetry();` do `ConfigureServices()` method in Class metoda swoje `Startup` klasy. Pełny przykład poniżej.

    ```csharp
        // This method gets called by the runtime. Use this method to add services to the container.
        public void ConfigureServices(IServiceCollection services)
        {
            // The following line enables Application Insights telemetry collection.
            services.AddApplicationInsightsTelemetry();
    
            // code adding other services for your application
            services.AddMvc();
        }
    ```

3. Konfigurowanie klucza instrumentacji.

    Choć jest możliwe zapewnienie klucz Instrumentacji jako argument do `AddApplicationInsightsTelemetry`, firma Microsoft zaleca, aby określić klucz Instrumentacji w konfiguracji. Poniżej pokazano, jak określić klucz Instrumentacji w `appsettings.json`. Upewnij się, że `appsettings.json` jest kopiowany do folderu głównego aplikacji podczas publikowania.

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

    Alternatywnie klucz instrumentacji, można również określić w jednym z następujących zmiennych środowiskowych.

    APPINSIGHTS_INSTRUMENTATIONKEY

    ApplicationInsights:InstrumentationKey

    Przykład:

    `SET ApplicationInsights:InstrumentationKey=putinstrumentationkeyhere`

    `SET APPINSIGHTS_INSTRUMENTATIONKEY=putinstrumentationkeyhere`

    `APPINSIGHTS_INSTRUMENTATIONKEY` Zazwyczaj służy do określania klucza Instrumentacji w przypadku aplikacji wdrożonych w usłudze Azure Web Apps.

    > [!NOTE]
    > Klucz instrumentacji, który jest określony w usłudze wins kodu za pośrednictwem zmiennej środowiskowej `APPINSIGHTS_INSTRUMENTATIONKEY`, który wins przez inne opcje.

## <a name="run-your-application"></a>Uruchamianie aplikacji

 Uruchom aplikację i wysyłać żądania do niego. Dane telemetryczne, powinny teraz rozpocząć przepływać do usługi Application Insights. Następujące dane telemetryczne są automatycznie gromadzone przez zestaw SDK usługi Application Insights.

|Requests/Dependencies |Szczegóły|
|---------------|-------|
|Żądania | Przychodzące żądania sieci web do aplikacji. |
|Http/Https | Wywołania z `HttpClient`. |
|SQL | Wywołania z `SqlClient`. |
|[Magazyn platformy Azure](https://www.nuget.org/packages/WindowsAzure.Storage/) | Wywołania z klienta usługi Storage platformy Azure. |
|[Zestaw SDK klienta usługi EventHub](https://www.nuget.org/packages/Microsoft.Azure.EventHubs) | Wersji 1.1.0 lub nowszej. |
|[Zestaw SDK klienta usługi ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus)| W wersji 3.0.0 lub nowszej. |
|Azure Cosmos DB | Tylko śledzone automatycznie w przypadku protokołu HTTP/HTTPS jest używany. Nie można przechwycić trybu TCP przez usługę Application Insights. |

### <a name="performance-counters"></a>Liczniki wydajności

Obsługa [liczniki wydajności](https://azure.microsoft.com/documentation/articles/app-insights-web-monitor-performance/) w programie ASP.NET Core jest ograniczone do następujących

   * Zestaw SDK w wersji 2.4.1 powyżej zbiera liczników wydajności, jeśli aplikacja jest uruchomiona w usłudze Azure Web App (Windows)
   * 2\.7.0-beta3 wersji zestawu SDK i powyżej zbiera liczników wydajności, jeśli aplikacja jest uruchomiona w Windows i określanie wartości docelowej `NETSTANDARD2.0` lub nowszej.
   * Dla aplikacji przeznaczonych dla platformy .NET Framework liczniki wydajności są obsługiwane we wszystkich wersjach zestawu SDK.
   * Ten artykuł będzie aktualizowany, gdy zostanie dodana jego obsługa licznika wydajności w systemie Linux.

### <a name="ilogger-logs"></a>Dzienniki ILogger

[Dzienniki ILogger](https://docs.microsoft.com/azure/azure-monitor/app/ilogger) o ważności `Warning` lub nowszym są automatycznie przechwycone z zestawu SDK w wersji 2.7.0-beta3 lub nowszej.

### <a name="live-metrics"></a>Metryki na żywo

Może upłynąć kilka minut, zanim dane telemetryczne rozpocząć, pojawiają się w portalu. Można szybko sprawdzić, czy wszystko działa, jest najlepiej użyć [metryki na żywo](https://docs.microsoft.com/azure/application-insights/app-insights-live-stream), podczas wprowadzania żądań do uruchomionej aplikacji.

## <a name="enable-client-side-telemetry-for-web-applications"></a>Włącz dane telemetryczne po stronie klienta dla aplikacji sieci Web

Powyższe kroki są wystarczające, aby rozpocząć zbieranie danych telemetrycznych po stronie serwera. Jeśli aplikacja ma składniki po stronie klienta, a następnie wykonaj poniższe kroki, aby rozpocząć zbieranie [dane telemetryczne użycia](https://docs.microsoft.com/azure/azure-monitor/app/usage-overview) z tego miejsca.

1. W `_ViewImports.cshtml`, Dodaj iniekcji:

    ```cshtml
        @inject Microsoft.ApplicationInsights.AspNetCore.JavaScriptSnippet JavaScriptSnippet
    ```

2. W `_Layout.cshtml`, Wstaw HtmlHelper na końcu `<head>` sekcji, ale przed dowolnego innego skryptu. Wszelkie niestandardowej telemetrii JavaScript, który chcesz sporządzić raport na stronie należy wprowadzony po tym fragmencie kodu:

    ```cshtml
        @Html.Raw(JavaScriptSnippet.FullScript)
        </head>
    ```

`.cshtml` Nazwy plików wyżej pochodzą z domyślnego szablonu aplikacji MVC. Ostatecznie, aby prawidłowo włączyć monitorowania po stronie klienta dla aplikacji należy fragment kodu JavaScript, znajdować się w `<head>` części każdej strony aplikacji, którą chcesz monitorować. Ten szablon aplikacji, dodając fragment kodu Javascript, aby `_Layout.cshtml` będzie efektywnie realizację tego celu. Jeśli projekt nie ma dla określonego pliku można dodać [monitorowania po stronie klienta](https://docs.microsoft.com/azure/azure-monitor/app/website-monitoring). Należy po prostu należy do jednej dodać JavaScript równoważne pliku, który kontroluje `<head>` wszystkich stron w aplikacji, lub też można dodać fragment kodu do poszczególnych wiele stron, chociaż jest to trudne w utrzymaniu i zwykle nie jest zalecane.

## <a name="configuring-application-insights-sdk"></a>Konfigurowanie zestawu SDK usługi Application Insights

Zestaw SDK usługi Application Insights dla platformy ASP.NET Core można dostosować w taki sposób, aby zmienić konfigurację domyślną. Użytkownicy z zestawu SDK usługi Application Insights ASP.NET może być zaznajomieni z używaniem konfiguracji `ApplicationInsights.config`, lub modyfikując `TelemetryConfiguration.Active`. Dla platformy ASP.NET Core konfiguracja jest implementowana w różny sposób. ASP.NET Core SDK zostanie dodany do aplikacji i skonfigurować za pomocą wbudowanych w platformy ASP.NET Core [wstrzykiwanie zależności](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection). Niemal wszystkie zmiany konfiguracji są wykonywane w `ConfigureServices()` metody usługi `Startup.cs` klasy, chyba że określono inaczej. Postępuj zgodnie z sekcji poniżej, aby dowiedzieć się więcej.

> [!NOTE]
>  Zmiana konfiguracji, modyfikując `TelemetryConfiguration.Active` nie jest zalecane w aplikacjach ASP.NET Core.

### <a name="configuring-using-applicationinsightsserviceoptions"></a>Konfigurowanie przy użyciu ApplicationInsightsServiceOptions

Istnieje możliwość zmodyfikować kilka typowych ustawień przez przekazanie `ApplicationInsightsServiceOptions` do `AddApplicationInsightsTelemetry`. Poniżej przedstawiono przykład.

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

Dokładna lista konfigurowalnych ustawień w `ApplicationInsightsServiceOptions` można znaleźć [tutaj](https://github.com/microsoft/ApplicationInsights-aspnetcore/blob/develop/src/Microsoft.ApplicationInsights.AspNetCore/Extensions/ApplicationInsightsServiceOptions.cs).

### <a name="sampling"></a>Próbkowanie

Zestaw SDK usługi Application Insights dla platformy ASP.NET Core obsługuje zarówno FixedRate i próbkowanie adaptacyjne. Próbkowanie adaptacyjne jest domyślnie włączona. Postępuj zgodnie z naszym [wskazówki dotyczące próbkowanie adaptacyjne](../../azure-monitor/app/sampling.md#configuring-adaptive-sampling-for-aspnet-core-applications), aby dowiedzieć się, jak skonfigurować próbkowania dla aplikacji platformy ASP.NET Core.

### <a name="adding-telemetryinitializers"></a>Dodawanie TelemetryInitializers

[Inicjatory telemetrii](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#add-properties-itelemetryinitializer) są używane, gdy chcesz zdefiniować właściwości globalne, które są wysyłane z wszystkie dane telemetryczne.

Aby dodać nowy `TelemetryInitializer`, dodaj go do kontenera DependencyInjection, jak pokazano poniżej. `TelemetryInitializer`s dodany do kontenera DependencyInjection zostaną pobrane przez zestaw SDK automatycznie.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddSingleton<ITelemetryInitializer, MyCustomTelemetryInitializer>();
    }
```

### <a name="removing-telemetryinitializers"></a>Usuwanie TelemetryInitializers

Aby usunąć wszystkie lub określone TelemetryInitializers, które są domyślnie obecne, użyj następującego przykładowego kodu **po** wywoływania `AddApplicationInsightsTelemetry()`.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetry();

        // Remove a specific built-in TelemetryInitializer
        var tiToRemove = services.FirstOrDefault<ServiceDescriptor>
                         (t => t.ImplementationType == typeof(AspNetCoreEnvironmentTelemetryInitializer));
        if (tiToRemove != null)
        {
            services.Remove(tiToRemove);
        }

        // Remove all initializers
        // This requires importing namespace using Microsoft.Extensions.DependencyInjection.Extensions;
        services.RemoveAll(typeof(ITelemetryInitializer));
    }
```

### <a name="adding-telemetryprocessors"></a>Dodawanie TelemetryProcessors

Telemetria niestandardowa procesory mogą być dodawane do `TelemetryConfiguration` przy użyciu metody rozszerzenia `AddApplicationInsightsTelemetryProcessor` na `IServiceCollection`. Procesory dane telemetryczne są używane w [zaawansowane scenariusze filtrowania](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#filtering-itelemetryprocessor) umożliwiający bardziej bezpośrednią kontrolę nad czym jest dołączone lub wykluczone z danych telemetrycznych, możesz wysłać do usługi Application Insights. Skorzystaj z poniższego przykładu.

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

### <a name="configuring-or-removing-default-telemetrymodules"></a>Konfigurowanie lub usuwanie domyślnej TelemetryModules

Usługa Application Insights są używane moduły danych telemetrycznych jako sposób [zbieranie automatycznie przydatnych informacji](https://docs.microsoft.com/azure/azure-monitor/app/auto-collect-dependencies) o konkretnych obciążeń, bez konieczności dodatkowej konfiguracji.

W przypadku następujących modułów kolekcji automatycznie są domyślnie włączone i jest odpowiedzialny za automatyczne zbieranie danych telemetrycznych. Może być wyłączone i skonfigurowany tak, aby zmienić zachowanie domyślne.

* `RequestTrackingTelemetryModule`
* `DependencyTrackingTelemetryModule`
* `PerformanceCollectorModule`
* `QuickPulseTelemetryModule`
* `AppServicesHeartbeatTelemetryModule`
* `AzureInstanceMetadataTelemetryModule`

Aby skonfigurować wszelkie domyślne `TelemetryModule`, należy użyć metody rozszerzenia `ConfigureTelemetryModule<T>` na `IServiceCollection` jak pokazano w poniższym przykładzie.

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

        // The following removes PerformanceCollectorModule to disable perf-counter collection.
        // Similarly, any other default modules can be removed.
        var performanceCounterService = services.FirstOrDefault<ServiceDescriptor>(t => t.ImplementationType == typeof(PerformanceCollectorModule));
        if (performanceCounterService != null)
        {
         services.Remove(performanceCounterService);
        }
    }
```

### <a name="configuring-telemetry-channel"></a>Konfigurowanie kanału danych Telemetrycznych

Domyślnym kanale, używany jest `ServerTelemetryChannel`. Może być zastąpiona przez poniższy przykład poniżej.

```csharp
using Microsoft.ApplicationInsights.Channel;

    public void ConfigureServices(IServiceCollection services)
    {
        // use the following to replace the default channel with InMemoryChannel.
        // this can also be applied to ServerTelemetryChannel as well.
        services.AddSingleton(typeof(ITelemetryChannel), new InMemoryChannel() {MaxTelemetryBufferCapacity = 19898 });

        services.AddApplicationInsightsTelemetry();
    }
```

## <a name="frequently-asked-questions"></a>Często zadawane pytania

### <a name="i-want-to-track-additional-telemetry-other-than-the-auto-collected-telemetry-how-do-i-do-it"></a>Chcę śledzić dodatkowe dane telemetryczne inne niż dane telemetryczne zbierane automatycznie. Jak to zrobić?

Uzyskać wystąpienia `TelemetryClient` przez przy użyciu iniekcji konstruktora i wywołać wymagane `TrackXXX()` metody na nim. Nie zaleca się tworzenia nowych `TelemetryClient` wystąpień w aplikacji platformy ASP.NET Core, jako pojedyncze wystąpienie `TelemetryClient` jest już zarejestrowany w kontenerze DI udostępnia `TelemetryConfiguration` z pozostałą częścią danych telemetrycznych. Tworzenie nowego `TelemetryClient` wystąpienia jest zalecane tylko wtedy, gdy musi on mieć osobne konfiguracji od pozostałej części danych telemetrycznych. Poniższy przykład pokazuje, jak śledzić dodatkowe dane telemetryczne z kontrolera.

```csharp
using Microsoft.ApplicationInsights;

public class HomeController : Controller
{
    private TelemetryClient telemetry;

    // use constructor injection to get TelemetryClient instance
    public HomeController(TelemetryClient telemetry)
    {
        this.telemetry = telemetry;
    }

    public IActionResult Index()
    {
        // call required TrackXXX method.
        this.telemetry.TrackEvent("HomePageRequested");
        return View();
    }
```

 Zapoznaj się [metryki niestandardowe w usłudze Application Insights dokumentacja interfejsu API](https://docs.microsoft.com/azure/azure-monitor/app/api-custom-events-metrics/) opis danych niestandardowych raportów w usłudze Application Insights.

### <a name="some-visual-studio-templates-used-useapplicationinsights-extension-method-on-iwebhostbuilder-to-enable-application-insights-is-this-usage-still-valid"></a>Niektóre szablony programu Visual Studio umożliwiają — metoda rozszerzenia UseApplicationInsights() na IWebHostBuilder usługi Application Insights. To obciążenie jest nadal ważny?

Włączenie usługi Application Insights przy użyciu tej metody jest prawidłowy i jest używany w wdrażania programu Visual Studio oraz z rozszerzeń aplikacji sieci Web platformy Azure. Jednak zaleca się używanie `services.AddApplicationInsightsTelemetry()` ponieważ oferuje przeciążenia do kontrolowania pewnych czynności konfiguracyjnych. Obie metody wewnętrznie te same czynności wykonasz, tak, jeśli brak niestandardowej konfiguracji mają być stosowane, wywołujący albo jest w dobrym stanie.

### <a name="i-am-deploying-my-aspnet-core-application-to-azure-web-apps-should-i-still-enable-the-application-insights-extension-from-web-apps"></a>Czy mogę jestem wdrażanie Moja aplikacja platformy ASP.NET Core w usłudze Azure Web Apps. Czy mogę nadal należy włączyć rozszerzenie usługi Application Insights z aplikacji sieci Web?

Jeśli zestaw SDK jest zainstalowany w czasie kompilacji, jak pokazano w tym artykule, nie ma potrzeby można włączyć rozszerzenia usługi Application Insights z poziomu portalu usługi App Service. Nawet jeśli rozszerzenie jest zainstalowane, będzie wycofania kiedy wykryje, że zestaw SDK został już dodany do aplikacji. Włączenie usługi Application Insights z rozszerzenia zwalnia z instalowania i aktualizowania zestawu SDK. Jednak włączenie usługi Application Insights, zgodnie z tego artykułu jest bardziej elastyczna poniżej powodów.
   * Telemetria usługi Application Insights będą nadal działać:
       * Wszystkie systemy operacyjne — Windows, Linux oraz komputerów Mac.
       * Opublikuj wszystkie tryby — samodzielne lub zależny od struktury.
       * Wszystkich docelowych platform, w tym pełny program .NET Framework.
       * Wszystkie opcje hostingu — aplikacja internetowa platformy Azure, maszyny wirtualne, Linux, kontenerów, AKS, spoza platformy Azure.
   * Dane telemetryczne są widoczne lokalnie, podczas debugowania w programie Visual Studio.
   * Umożliwia używanie dodatkowych niestandardowej telemetrii śledzenia `TrackXXX()` interfejsu API.
   * Masz pełną kontrolę nad konfiguracją.

### <a name="can-i-enable-application-insights-monitoring-using-tools-like-status-monitor"></a>Można włączyć monitorowanie usługi Application Insights przy użyciu narzędzi, takich jak Monitor stanu?

Nie. [Monitor stanu](https://docs.microsoft.com/azure/azure-monitor/app/monitor-performance-live-website-now) i nadchodzących zastąpienia [v2 Monitora stanu](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-overview) obecnie obsługuje platformę ASP.NET 4.x tylko.

### <a name="i-have-an-aspnet-core-20-application-isnt-application-insights-automatically-enabled-without-me-doing-anything"></a>Mam aplikację ASP.NET Core 2.0. Nie ma usługi Application Insights automatycznie włączone bez mnie niczym zajęty?

`Microsoft.AspNetCore.All` 2.0 meta Microsoft.aspnetcore.all uwzględniona zestawu SDK usługi Application Insights (wersja 2.1.0), a po uruchomieniu aplikacji w debugerze programu Visual Studio, Visual Studio umożliwia usługi Application Insights i przedstawia dane telemetryczne lokalnie w bezpośrednio ze środowiska IDE. Telemetria nie została wysłana do usługi Application Insights, chyba że jawnie określony klucz instrumentacji. Firma Microsoft zaleca, wykonując instrukcje przedstawione w tym artykule, aby włączyć usługę Application Insights, nawet w przypadku 2.0 aplikacji.

### <a name="i-run-my-application-in-linux-are-all-features-supported-in-linux-as-well"></a>Czy mogę uruchomić moją aplikację w systemie Linux. Są wszystkie funkcje obsługiwane w systemie Linux oraz?

* Tak. Obsługa funkcji zestawu SDK jest taka sama we wszystkich platform, z następującymi wyjątkami:

    * Liczniki wydajności nie są jeszcze obsługiwane w inne niż Windows.
    * Mimo że `ServerTelemetryChannel` jest włączona domyślnie, jeśli aplikacja jest uruchomiona w systemie Linux lub MacOS, kanał nie tworzy automatycznie folder lokalny magazyn do przechowywania danych telemetrycznych tymczasowo, jeśli występują problemy z siecią. To ograniczenie powoduje, że dane telemetryczne, zostanie utracone w przypadku tymczasowego sieci lub problemy z serwerem. Obejście tego problemu jest dla użytkownika skonfigurować lokalny folder dla kanału, jak pokazano poniżej.

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;

    public void ConfigureServices(IServiceCollection services)
    {
        // The following will configure channel to use the given folder to temporarily
        // store telemetry items during network or application insights server issues.
        // User should ensure that the given folder already exists,
        // and that application has read/write permissions.
        services.AddSingleton(typeof(ITelemetryChannel),
                                new ServerTelemetryChannel () {StorageFolder = "/tmp/myfolder"});
        services.AddApplicationInsightsTelemetry();
    }
```

## <a name="open-source-sdk"></a>Zestaw SDK typu open-source
[Odczytywanie i Współtworzenie kodu](https://github.com/Microsoft/ApplicationInsights-aspnetcore#recent-updates).

## <a name="video"></a>Połączenia wideo

- Zewnętrzne wideo krok po kroku dotyczące [Konfigurowanie usługi Application Insights przy użyciu platformy .NET Core i Visual Studio](https://www.youtube.com/watch?v=NoS9UhcR4gA&t) od podstaw.
- Zewnętrzne wideo krok po kroku dotyczące [Konfigurowanie usługi Application Insights przy użyciu platformy .NET Core i Visual Studio Code](https://youtu.be/ygGt84GDync) od podstaw.

## <a name="next-steps"></a>Kolejne kroki
* [Zapoznaj się z przepływy użytkownika](../../azure-monitor/app/usage-flows.md) Aby zrozumieć, jak użytkownicy nawigują w aplikacjach.
* [Konfigurowanie zbierania migawek](https://docs.microsoft.com/azure/application-insights/app-insights-snapshot-debugger) Aby wyświetlić stan zmiennych i kod źródłowy w tej chwili, zgłaszany jest wyjątek.
* [Za pomocą interfejsu API](../../azure-monitor/app/api-custom-events-metrics.md) do wysłania własnych zdarzeń i metryk, aby uzyskać bardziej szczegółowy widok wydajności i użycia Twojej aplikacji.
* Użyj [testy dostępności](../../azure-monitor/app/monitor-web-app-availability.md) do sprawdzenia aplikacji stale z całego świata.
