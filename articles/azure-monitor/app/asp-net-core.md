---
title: Usługa Azure Application Insights dla aplikacji platformy ASP.NET Core | Dokumentacja firmy Microsoft
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
ms.openlocfilehash: 5ea7ec41ccc721e8eafda56aa7463505ba089845
ms.sourcegitcommit: 441e59b8657a1eb1538c848b9b78c2e9e1b6cfd5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2019
ms.locfileid: "67827809"
---
# <a name="application-insights-for-aspnet-core-applications"></a>Usługa Application Insights dla aplikacji platformy ASP.NET Core

W tym artykule opisano sposób włączania usługi Application Insights dla [platformy ASP.NET Core](https://docs.microsoft.com/aspnet/core) aplikacji. Po wykonaniu instrukcji zawartych w tym artykule, Application Insights będzie zbierać żądania, zależności, wyjątki, liczniki wydajności, pulsów i dzienniki z aplikacji ASP.NET Core. 

W przykładzie w tym miejscu użyjemy [aplikacji MVC](https://docs.microsoft.com/aspnet/core/tutorials/first-mvc-app) przeznaczonego `netcoreapp2.2`. Instrukcje te można zastosować do wszystkich aplikacji platformy ASP.NET Core.

## <a name="supported-scenarios"></a>Obsługiwane scenariusze

[Zestaw Application Insights SDK dla platformy ASP.NET Core](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) można monitorować aplikacje niezależnie od tego, gdzie i jak działają. Jeśli aplikacja jest uruchomiona i ma połączenie sieciowe na platformie Azure, można zbierać dane telemetryczne. Monitorowania usługi Application Insights jest obsługiwana, wszędzie tam, gdzie platformy .NET Core jest obsługiwane. Obsługa obejmuje:
* **System operacyjny**: Windows, Linux i Mac.
* **Metoda hostingu**: W procesie lub poza procesem. 
* **Metoda wdrażania**: Framework zależnych lub niezależna.
* **Serwer sieci Web**: Usługi IIS (Internet Information Server) lub Kestrel. 
* **Platforma hostingu**: Funkcja Web Apps usługi Azure App Service, maszyny Wirtualnej platformy Azure, Docker, usługi Azure Kubernetes Service (AKS) i tak dalej.
* **IDE**: Visual Studio, program VS Code lub wiersza polecenia.

## <a name="prerequisites"></a>Wymagania wstępne

- Działająca aplikacja platformy ASP.NET Core. Jeśli potrzebujesz do tworzenia aplikacji platformy ASP.NET Core, postępuj zgodnie z tym [samouczek platformy ASP.NET Core](https://docs.microsoft.com/aspnet/core/getting-started/).
- Nieprawidłowy klucz Instrumentacji usługi Application Insights. Ten klucz jest wymagany do wysłania żadnych danych telemetrycznych do usługi Application Insights. Jeśli musisz utworzyć nowy zasób usługi Application Insights można pobrać klucza, zobacz Instrumentacji [Utwórz zasób usługi Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource).

## <a name="enable-application-insights-server-side-telemetry-visual-studio"></a>Włączanie danych telemetrycznych usługi Application Insights po stronie serwera (Visual Studio)

1. Otwórz swój projekt w programie Visual Studio.

    > [!TIP]
    > Jeśli chcesz, możesz skonfigurować kontroli źródła dla projektu, dzięki czemu można śledzić wszystkie zmiany, które czynią usługę Application Insights. Aby włączyć kontrolę źródła, wybierz **pliku** > **Dodaj do kontroli źródła**.

2. Wybierz **projektu** > **Dodaj Telemetrię usługi Application Insights**.

3. Wybierz **wprowadzenie**. Ten wybór tekstu może się różnić w zależności od używanej wersji programu Visual Studio. Niektóre starsze wersje użyj **Rozpocznij za darmo** zamiast tego przycisku.

4. Wybierz subskrypcję. Następnie wybierz pozycję **zasobów** > **zarejestrować**.

5. Po dodaniu usługi Application Insights do projektu, sprawdź, upewnij się, że używasz najnowszej wersji stabilne zestawu SDK. Przejdź do **projektu** > **Zarządzaj pakietami NuGet** > **Microsoft.ApplicationInsights.AspNetCore**. Jeśli zachodzi potrzeba, wybierz opcję **aktualizacji**.

     ![Zrzut ekranu przedstawiający miejsce wybrać pakiet usługi Application Insights dla aktualizacji](./media/asp-net-core/update-nuget-package.png)

6. Jeśli a następnie opcjonalnie porady i dodać projekt do kontroli źródła, przejdź do strony **widoku** > **Team Explorer** > **zmiany**. Następnie zaznacz każdy plik, aby wyświetlić diff zmiany wprowadzone przez telemetria usługi Application Insights.

## <a name="enable-application-insights-server-side-telemetry-no-visual-studio"></a>Włączanie danych telemetrycznych usługi Application Insights po stronie serwera (nie programu Visual Studio)

1. Zainstaluj [pakietu NuGet zestawu SDK usługi Application Insights dla platformy ASP.NET Core](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore). Firma Microsoft zaleca, zawsze używaj najnowszej stabilnej wersji. Znajdź pełne uwagi do wersji zestawu SDK na [repozytorium GitHub typu open-source](https://github.com/Microsoft/ApplicationInsights-aspnetcore/releases).

    Poniższy przykładowy kod pokazuje zmiany, które mają zostać dodane do projektu `.csproj` pliku.

    ```xml
        <ItemGroup>
          <PackageReference Include="Microsoft.ApplicationInsights.AspNetCore" Version="2.7.0" />
        </ItemGroup>
    ```

2. Dodaj `services.AddApplicationInsightsTelemetry();` do `ConfigureServices()` method in Class metoda swoje `Startup` klasy, jak w poniższym przykładzie:

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

3. Konfigurowanie klucza instrumentacji.

    Chociaż można podać klucz Instrumentacji jako argument do `AddApplicationInsightsTelemetry`, firma Microsoft zaleca, aby określić klucz Instrumentacji w konfiguracji. Poniższy przykład kodu pokazuje, jak określić klucz Instrumentacji w `appsettings.json`. Upewnij się, że `appsettings.json` jest kopiowany do folderu głównego aplikacji podczas publikowania.

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

    Alternatywnie określ klucz Instrumentacji w jednym z następujących zmiennych środowiskowych:

    * `APPINSIGHTS_INSTRUMENTATIONKEY`

    * `ApplicationInsights:InstrumentationKey`

    Na przykład:

    * `SET ApplicationInsights:InstrumentationKey=putinstrumentationkeyhere`

    * `SET APPINSIGHTS_INSTRUMENTATIONKEY=putinstrumentationkeyhere`

    Zazwyczaj `APPINSIGHTS_INSTRUMENTATIONKEY` Określa klucz Instrumentacji w przypadku aplikacji wdrożonych w usłudze Web Apps.

    > [!NOTE]
    > Klucz instrumentacji, który jest określony w usłudze wins kodu za pośrednictwem zmiennej środowiskowej `APPINSIGHTS_INSTRUMENTATIONKEY`, który wins przez inne opcje.

## <a name="run-your-application"></a>Uruchamianie aplikacji

Uruchom aplikację i wysyłać żądania do niego. Teraz przepływu danych telemetrycznych do usługi Application Insights. Zestaw SDK usługi Application Insights automatycznie zbiera następujące dane telemetryczne.

|Żądania/zależności |Szczegóły|
|---------------|-------|
|Żądania | Przychodzące żądania sieci web do aplikacji. |
|HTTP lub HTTPS | Wywołania z `HttpClient`. |
|SQL | Wywołania z `SqlClient`. |
|[Azure Storage](https://www.nuget.org/packages/WindowsAzure.Storage/) | Wywołania z klientem usługi Azure Storage. |
|[Zestaw SDK klienta EventHubs.](https://www.nuget.org/packages/Microsoft.Azure.EventHubs) | Wersji 1.1.0 lub nowszy. |
|[Zestaw SDK klienta usługi ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus)| W wersji 3.0.0 lub nowszy. |
|Azure Cosmos DB | Śledzone automatycznie tylko wtedy, gdy jest używany protokół HTTP/HTTPS. Usługa Application Insights nie przechwytuje trybu TCP. |

### <a name="performance-counters"></a>Liczniki wydajności

Obsługa [liczniki wydajności](https://azure.microsoft.com/documentation/articles/app-insights-web-monitor-performance/) jest ograniczona w programie ASP.NET Core:

   * Zestaw SDK w wersji 2.4.1 i liczniki później zbierania wydajności, jeśli aplikacja jest uruchomiona w aplikacjach sieci Web (Windows).
   * 2\.7.0-beta3 wersji zestawu SDK i nowszych zbierania wydajności liczników, jeśli aplikacja jest uruchomiona w Windows i elementy docelowe `NETSTANDARD2.0` lub nowszej.
   * Dla aplikacji przeznaczonych dla platformy .NET Framework wszystkie wersje zestawu SDK obsługuje liczników wydajności.
 
Ten artykuł będzie aktualizowany, gdy zostanie dodana jego obsługa licznika wydajności w systemie Linux.

### <a name="ilogger-logs"></a>Dzienniki ILogger

[Dzienniki ILogger](https://docs.microsoft.com/azure/azure-monitor/app/ilogger) o ważności `Warning` lub nowszej, są automatycznie przechwytywane w 2.7.0-beta3 wersji zestawu SDK i nowszych wersjach.

### <a name="live-metrics"></a>Metryki na żywo

Może upłynąć kilka minut, zanim dane telemetryczne, który rozpoczyna się pojawiają się w portalu. Aby szybko upewnić się, że wszystko działa, jest najlepiej użyć [metryki na żywo](https://docs.microsoft.com/azure/application-insights/app-insights-live-stream) podczas wprowadzania żądań do uruchomionej aplikacji.

## <a name="enable-client-side-telemetry-for-web-applications"></a>Włącz telemetrii po stronie klienta dla aplikacji sieci web

Te czynności są wystarczająco dużo, aby pomóc Ci rozpocząć zbieranie danych telemetrycznych po stronie serwera. Jeśli aplikacja ma składniki po stronie klienta, wykonaj następujące kroki, aby rozpocząć zbieranie [dane telemetryczne użycia](https://docs.microsoft.com/azure/azure-monitor/app/usage-overview).

1. W `_ViewImports.cshtml`, Dodaj iniekcji:

    ```cshtml
        @inject Microsoft.ApplicationInsights.AspNetCore.JavaScriptSnippet JavaScriptSnippet
    ```

2. W `_Layout.cshtml`, Wstaw `HtmlHelper` na końcu `<head>` sekcji, ale przed dowolnego innego skryptu. Aby zgłosić żadnych niestandardowych danych telemetrycznych JavaScript na stronie, wstrzyknięcia go po tym fragmencie kodu:

    ```cshtml
        @Html.Raw(JavaScriptSnippet.FullScript)
        </head>
    ```

`.cshtml` Nazw plików, do których odwołuje się wcześniej pochodzą z domyślnego szablonu aplikacji MVC. Ostatecznie, jeśli chcesz prawidłowo włączyć monitorowania po stronie klienta dla aplikacji, fragment kodu JavaScript musi znajdować się w `<head>` części każdej strony aplikacji, którą chcesz monitorować. Można osiągnąć ten cel, aby ten szablon aplikacji przez dodanie fragment kodu JavaScript, aby `_Layout.cshtml`. 

Jeśli projekt nie zawiera `_Layout.cshtml`, nadal możesz dodać [monitorowania po stronie klienta](https://docs.microsoft.com/azure/azure-monitor/app/website-monitoring). Można to zrobić, dodając fragment kodu JavaScript do pliku równoważne, który kontroluje `<head>` wszystkich stron w aplikacji. Lub możesz dodać fragment kodu do wielu stron, ale to rozwiązanie jest trudne w utrzymaniu i ogólnie nie zalecamy tego.

## <a name="configure-the-application-insights-sdk"></a>Konfigurowanie zestawu SDK usługi Application Insights

Można dostosować zestaw Application Insights SDK dla platformy ASP.NET Core zmienić konfigurację domyślną. Użytkowników zestawu SDK usługi Application Insights ASP.NET może znać zmiany konfiguracji przy użyciu `ApplicationInsights.config` lub modyfikując `TelemetryConfiguration.Active`. Możesz zmienić konfigurację inaczej dla platformy ASP.NET Core. Dodawanie platformy ASP.NET Core SDK do aplikacji i skonfigurować go za pomocą wbudowanych w platformy ASP.NET Core [wstrzykiwanie zależności](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection). Wprowadzić prawie wszystkie zmiany w konfiguracji w `ConfigureServices()` metody usługi `Startup.cs` klasy, chyba że jest skierowany w inny sposób. Poniższe sekcje zapewniają więcej informacji.

> [!NOTE]
> W aplikacji platformy ASP.NET Core, zmiany konfiguracji, modyfikując `TelemetryConfiguration.Active` nie jest obsługiwane.

### <a name="using-applicationinsightsserviceoptions"></a>Using ApplicationInsightsServiceOptions

Kilka typowych ustawień można modyfikować, przekazując `ApplicationInsightsServiceOptions` do `AddApplicationInsightsTelemetry`, jak w tym przykładzie:

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

Aby uzyskać więcej informacji, zobacz [konfigurowalnych ustawień w `ApplicationInsightsServiceOptions` ](https://github.com/microsoft/ApplicationInsights-aspnetcore/blob/develop/src/Microsoft.ApplicationInsights.AspNetCore/Extensions/ApplicationInsightsServiceOptions.cs).

### <a name="sampling"></a>Próbkowanie

Zestaw SDK usługi Application Insights dla platformy ASP.NET Core obsługuje zarówno szybkościami transmisji i próbkowanie adaptacyjne. Próbkowanie adaptacyjne jest domyślnie włączona. 

Aby uzyskać więcej informacji, zobacz [skonfigurować próbkowania adaptacyjnego w przypadku aplikacji platformy ASP.NET Core](../../azure-monitor/app/sampling.md#configuring-adaptive-sampling-for-aspnet-core-applications).

### <a name="adding-telemetryinitializers"></a>Dodawanie TelemetryInitializers

Użyj [inicjatory telemetrii](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#add-properties-itelemetryinitializer) kiedy chcesz zdefiniować właściwości globalne, które są wysyłane z wszystkie dane telemetryczne.

Dodaj dowolną nową `TelemetryInitializer` do `DependencyInjection` kontenera, jak pokazano w poniższym kodzie. Zestaw SDK automatycznie przejmuje on dowolnej `TelemetryInitializer` który został dodany do `DependencyInjection` kontenera.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddSingleton<ITelemetryInitializer, MyCustomTelemetryInitializer>();
    }
```

### <a name="removing-telemetryinitializers"></a>Usuwanie TelemetryInitializers

Inicjatory dane telemetryczne są domyślnie obecne. Aby usunąć wszystkie lub inicjatory określone dane telemetryczne, użyj następującego przykładowego kodu *po* wywołujesz `AddApplicationInsightsTelemetry()`.

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

### <a name="adding-telemetry-processors"></a>Zainstalowanie dodatkowych procesorów telemetrii

Można dodać niestandardowych danych telemetrycznych procesorów, którą należy `TelemetryConfiguration` przy użyciu metody rozszerzenia `AddApplicationInsightsTelemetryProcessor` na `IServiceCollection`. Użyj danych telemetrycznych procesorów w [zaawansowane scenariusze filtrowania](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#filtering-itelemetryprocessor) umożliwiający bardziej bezpośrednią kontrolę nad czym ma dołączone lub wykluczone z danych telemetrycznych, możesz wysłać do usługi Application Insights. Skorzystaj z poniższego przykładu.

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

Usługa Application Insights są używane moduły danych telemetrycznych do [przydatne informacje były zbierane automatycznie](https://docs.microsoft.com/azure/azure-monitor/app/auto-collect-dependencies) o konkretnych obciążeń, bez konieczności dodatkowej konfiguracji.

Następujące moduły automatyczne kolekcji są domyślnie włączone. Te moduły są odpowiedzialne za automatyczne zbieranie danych telemetrycznych. Można wyłączyć lub skonfigurować je, aby zmienić ich zachowanie domyślne.

* `RequestTrackingTelemetryModule`
* `DependencyTrackingTelemetryModule`
* `PerformanceCollectorModule`
* `QuickPulseTelemetryModule`
* `AppServicesHeartbeatTelemetryModule`
* `AzureInstanceMetadataTelemetryModule`

Aby skonfigurować wszelkie domyślne `TelemetryModule`, należy użyć metody rozszerzenia `ConfigureTelemetryModule<T>` na `IServiceCollection`, jak pokazano w poniższym przykładzie.

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

### <a name="configuring-a-telemetry-channel"></a>Konfigurowanie kanału danych telemetrycznych

Jest w domyślnym kanale `ServerTelemetryChannel`. Można go zastąpić, co ilustruje poniższy przykład.

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

### <a name="disable-telemetry-dynamically"></a>Wyłączanie telemetrii dynamicznie

Jeśli chcesz wyłączyć telemetrię warunkowo i dynamicznie, może zostać rozwiązany `TelemetryConfiguration` wystąpienia z kontenera iniekcji zależności platformy ASP.NET Core w dowolnym miejscu w kodzie i ustaw `DisableTelemetry` flagi na nim.

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

## <a name="frequently-asked-questions"></a>Często zadawane pytania

### <a name="how-can-i-track-telemetry-thats-not-automatically-collected"></a>Jak mogę śledzić dane telemetryczne, które są automatycznie zbierane?

Pobranie wystąpienia `TelemetryClient` przez przy użyciu iniekcji konstruktora i wywołać wymagane `TrackXXX()` metody na nim. Nie zaleca się tworzenie nowych `TelemetryClient` wystąpień w aplikacji ASP.NET Core. Pojedyncze wystąpienie `TelemetryClient` jest już zarejestrowany w `DependencyInjection` kontenera, które udostępnia `TelemetryConfiguration` z pozostałą częścią danych telemetrycznych. Tworzenie nowego `TelemetryClient` wystąpienia jest zalecane tylko wtedy, jeśli wymaga on konfiguracji, który jest oddzielony od pozostałej części danych telemetrycznych. 

Poniższy przykład pokazuje, jak śledzić dodatkowe dane telemetryczne z kontrolera.

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

Aby uzyskać więcej informacji na temat danych niestandardowych raportów w usłudze Application Insights, zobacz [metryki niestandardowe w usłudze Application Insights dokumentacja interfejsu API](https://docs.microsoft.com/azure/azure-monitor/app/api-custom-events-metrics/).

### <a name="some-visual-studio-templates-used-the-useapplicationinsights-extension-method-on-iwebhostbuilder-to-enable-application-insights-is-this-usage-still-valid"></a>Niektóre szablony programu Visual Studio umożliwiają metody rozszerzenia UseApplicationInsights() na IWebHostBuilder usługi Application Insights. To obciążenie jest nadal ważny?

Tak, włączenie usługi Application Insights przy użyciu tej metody jest nieprawidłowa. Ta technika jest używany w dołączanie do programu Visual Studio i rozszerzenia aplikacji sieci Web. Jednak firma Microsoft zaleca używanie `services.AddApplicationInsightsTelemetry()` ponieważ zapewnia przeciążenia do kontrolowania pewnych czynności konfiguracyjnych. Obie metody te same czynności wykonasz wewnętrznie, jeśli nie potrzebujesz zastosować niestandardową konfigurację, można więc wywoływać jednej z metod.

### <a name="im-deploying-my-aspnet-core-application-to-web-apps-should-i-still-enable-the-application-insights-extension-from-web-apps"></a>Czy mogę jestem wdrażanie Moja aplikacja platformy ASP.NET Core w usłudze Web Apps. Czy mogę nadal należy włączyć rozszerzenie usługi Application Insights z aplikacji sieci Web?

Zestaw SDK jest zainstalowany w czasie kompilacji, jak pokazano w tym artykule, nie trzeba włączyć rozszerzenie usługi Application Insights z poziomu portalu usługi App Service. Nawet jeśli rozszerzenie jest zainstalowane, będzie wycofania kiedy wykryje, że zestaw SDK został już dodany do aplikacji. Po włączeniu usługi Application Insights z rozszerzenia nie trzeba zainstalować i zaktualizować zestaw SDK. Jednak jeśli włączysz usługi Application Insights, wykonując instrukcje przedstawione w tym artykule, masz większą elastyczność, ponieważ:

   * Telemetria usługi Application Insights będą nadal działać:
       * Wszystkie systemy operacyjne, w tym Windows i Linux oraz komputery Mac.
       * Opublikuj wszystkie tryby, uwzględniając samodzielne lub framework zależnych.
       * Wszystkich docelowych platform, w tym pełny program .NET Framework.
       * Wszystkie opcje hostingu, w tym aplikacje sieci Web, maszyn wirtualnych, Linux, kontenerów, usługi Azure Kubernetes Service i hostingu spoza platformy Azure.
   * Telemetrię można wyświetlić lokalnie, podczas debugowania w programie Visual Studio.
   * Dodatkowe niestandardowych danych telemetrycznych można śledzić za pomocą `TrackXXX()` interfejsu API.
   * Masz pełną kontrolę nad konfiguracją.

### <a name="can-i-enable-application-insights-monitoring-by-using-tools-like-status-monitor"></a>Można włączyć monitorowanie za pomocą narzędzi, takich jak Monitor stanu usługi Application Insights?

Nie. [Monitor stanu](https://docs.microsoft.com/azure/azure-monitor/app/monitor-performance-live-website-now) i [v2 Monitora stanu](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-overview) obecnie obsługują aplikacje ASP.NET 4.x tylko.

### <a name="is-application-insights-automatically-enabled-for-my-aspnet-core-20-application"></a>Usługa Application Insights automatycznie włączono dla mojej aplikacji ASP.NET Core 2.0?

`Microsoft.AspNetCore.All` 2.0 meta Microsoft.aspnetcore.all uwzględnione SDK usługi Application Insights (wersja 2.1.0). Po uruchomieniu aplikacji w debugerze programu Visual Studio, Visual Studio umożliwia usługi Application Insights i przedstawia dane telemetryczne lokalnie w bezpośrednio ze środowiska IDE. Dane telemetryczne nie wysłano do usługi Application Insights, o ile nie określono klucza instrumentacji. Firma Microsoft zaleca, wykonując instrukcje przedstawione w tym artykule, aby włączyć usługę Application Insights, nawet w przypadku 2.0 aplikacji.

### <a name="if-i-run-my-application-in-linux-are-all-features-supported"></a>Jeśli Moja aplikacja w systemie Linux, czy wszystkie funkcje są obsługiwane?

Tak. Obsługa funkcji zestawu SDK jest taka sama na wszystkich platformach, z następującymi wyjątkami:

* Liczniki wydajności są obsługiwane tylko w Windows.
* Mimo że `ServerTelemetryChannel` jest włączona domyślnie, jeśli aplikacja jest uruchomiona w systemie Linux lub MacOS, kanał nie tworzy automatycznie folder lokalny magazyn do przechowywania danych telemetrycznych tymczasowo, jeśli występują problemy z siecią. Ze względu na to ograniczenie dane telemetryczne zostaną utracone, gdy tymczasowe problemy sieci lub serwera. Aby obejść ten problem, skonfiguruj folder lokalny dla kanału:

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

## <a name="open-source-sdk"></a>Zestaw SDK typu open-source

[Odczytywanie i Współtworzenie kodu](https://github.com/Microsoft/ApplicationInsights-aspnetcore#recent-updates).

## <a name="video"></a>Połączenia wideo

- Zapoznaj się z to zewnętrzny wideo krok po kroku, aby [Konfigurowanie usługi Application Insights przy użyciu platformy .NET Core i Visual Studio](https://www.youtube.com/watch?v=NoS9UhcR4gA&t) od podstaw.
- Zapoznaj się z to zewnętrzny wideo krok po kroku, aby [Konfigurowanie usługi Application Insights przy użyciu platformy .NET Core i Visual Studio Code](https://youtu.be/ygGt84GDync) od podstaw.

## <a name="next-steps"></a>Następne kroki

* [Zapoznaj się z przepływem użytkownika](../../azure-monitor/app/usage-flows.md) Aby zrozumieć, jak użytkownicy nawigują w aplikacjach.
* [Konfigurowanie zbierania migawek](https://docs.microsoft.com/azure/application-insights/app-insights-snapshot-debugger) Aby wyświetlić stan zmiennych i kod źródłowy w tej chwili, zgłaszany jest wyjątek.
* [Za pomocą interfejsu API](../../azure-monitor/app/api-custom-events-metrics.md) do wysłania własnych zdarzeń i metryk do wyświetlenia szczegółowych informacji o wydajności i użycia Twojej aplikacji.
* Użyj [testy dostępności](../../azure-monitor/app/monitor-web-app-availability.md) do sprawdzenia aplikacji stale z całego świata.
* [Wstrzykiwanie zależności w programie ASP.NET Core](https://docs.microsoft.com/aspnet/fundamentals/dependency-injection)
