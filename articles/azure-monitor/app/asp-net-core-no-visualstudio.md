---
title: Usługi Azure Application Insights dla platformy ASP.NET Core bez programu Visual Studio | Dokumentacja firmy Microsoft
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
ms.date: 04/03/2019
ms.author: cithomas
ms.openlocfilehash: 8243523887ec9861459b2d196126237cf89bad97
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60691346"
---
# <a name="application-insights-for-aspnet-core-applications"></a>Usługa Application Insights dla aplikacji platformy ASP.NET Core

W tym artykule opisano włączanie usługi Application Insights dla [platformy ASP.NET Core](https://docs.microsoft.com/aspnet/core/?view=aspnetcore-2.2) aplikacji bez korzystania z programu Visual Studio IDE. W przypadku programu Visual Studio IDE instalować, Usuń [to](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) dokument zawiera szczegółowe instrukcje programu Visual Studio. Usługi Application Insights, wykonując instrukcje w tym artykule, rozpocznie zbieranie żądania, zależności, wyjątki, liczniki wydajności, pulsów i dzienniki z aplikacji ASP.NET Core. Przykładowa aplikacja używana jest [aplikacji MVC](https://docs.microsoft.com/aspnet/core/tutorials/first-mvc-app/?view=aspnetcore-2.2) przeznaczonych dla `netcoreapp2.2`, ale tu instrukcje mają zastosowanie do wszystkich aplikacji programu ASP.NET Core. Wszelkie wyjątki są nazywane, jeśli ma to zastosowanie.

## <a name="supported-scenarios"></a>Obsługiwane scenariusze

Application Insights SDK (Software Development Kit) dla platformy ASP.NET Core można monitorować aplikacje niezależnie od tego, gdzie i jak aplikacja jest uruchomiona. Jeśli aplikacja jest uruchomiona i ma połączenie sieciowe z usługi Application Insights, dane telemetryczne powinien być zbierane. Ta obsługa obejmuje, ale nie jest ograniczona do dowolnego systemu operacyjnego (Windows, Linux, Mac), metody hostowania (vs w procesie poza procesem), metoda wdrażania (niezależna vs zależny od struktury), serwer sieci Web (IIS, Kestrel), platformy (aplikacje sieci Web platformy Azure, w przypadku maszyn wirtualnych platformy Azure Docker, AKS itd.) lub w środowisku IDE (wiersz polecenia programu Visual Studio, program VS Code).

## <a name="prerequisites"></a>Wymagania wstępne

- Ć funkcjonalną aplikację ASP.NET Core. Postępuj zgodnie z [to](https://docs.microsoft.com/aspnet/core/getting-started/) przewodnika, aby utworzyć aplikację ASP.NET Core, jeśli to konieczne.
- Prawidłowy klucz usługi Application Insights instrumentacji, który jest wymagany do wysyłania żadnych danych telemetrycznych do usługi Application Insights. Postępuj zgodnie z [te](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource) instrukcjami, aby utworzyć nowy zasób usługi Application Insights, w razie potrzeby i uzyskiwanie klucza instrumentacji.

## <a name="enabling-application-insights-server-side-telemetry"></a>Włączanie Telemetria usługi Application Insights po stronie serwera

1. Zainstaluj zestaw SDK usługi Application Insights dla platformy ASP.NET Core, która jest regularnie pakietu NuGet. Zalecane jest, aby zawsze używać najnowszej stabilnej wersji. Niektóre funkcje opisane w tym artykule mogą nie być dostępne w starszych wersjach. Pełne uwagi do wersji zestawu SDK można znaleźć [tutaj](https://github.com/Microsoft/ApplicationInsights-aspnetcore/releases). 

Poniżej pokazano zmiany, które mają zostać dodane do pliku csproj projektu.

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

3. Ustaw klucz instrumentacji.

   Choć jest możliwe zapewnienie klucz Instrumentacji jako argument do `services.AddApplicationInsightsTelemetry("putinstrumentationkeyhere");`, zalecane jest określenie klucza Instrumentacji w konfiguracji. Poniżej pokazano, jak określić klucz Instrumentacji w `appsettings.json`. Upewnij się, że `appsettings.json` jest kopiowany do folderu głównego aplikacji podczas publikowania.

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

    Alternately, the instrumentation key can also be specified in either of the following environment variables.
    APPINSIGHTS_INSTRUMENTATIONKEY
    ApplicationInsights:InstrumentationKey

    Example:
    `SET ApplicationInsights:InstrumentationKey=putinstrumentationkeyhere`

`APPINSIGHTS_INSTRUMENTATIONKEY` Zazwyczaj służy do określania klucza Instrumentacji w przypadku aplikacji wdrożonych w usłudze Azure Web Apps.

> [!NOTE]
> Klucz instrumentacji, który jest określony w usłudze wins kodu za pośrednictwem zmiennej środowiskowej `APPINSIGHTS_INSTRUMENTATIONKEY`, który wins przez inne opcje.

4. Uruchom aplikację i wysyłać żądania do niego. Dane telemetryczne, powinny teraz rozpocząć przepływać do usługi Application Insights. Następujące dane telemetryczne są automatycznie gromadzone przez zestaw SDK usługi Application Insights.

    1. Żądania — web przychodzące żądania do aplikacji.
    1. Zależności
        1. Przy użyciu wywołania HTTP/Https `HttpClient`
        1. Wywołania SQL z `SqlClient`
        1. Wywołania usługi Azure storage za pomocą [klienta usługi Storage platformy Azure](https://www.nuget.org/packages/WindowsAzure.Storage/)*
        1. [Zestaw SDK klienta usługi EventHub](https://www.nuget.org/packages/Microsoft.Azure.EventHubs) wersji 1.1.0 lub nowszej
        1. [Zestaw SDK klienta usługi ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus) wersji 3.0.0 lub nowszej

             * Usługa azure Cosmos DB są automatycznie śledzone tylko wtedy, gdy jest używany protokół HTTP/HTTPS. Nie można przechwycić trybu TCP przez usługę Application Insights.


    1. [Liczniki wydajności](https://azure.microsoft.com/documentation/articles/app-insights-web-monitor-performance/)
        1. Obsługa liczników wydajności w programie ASP.NET Core jest ograniczone do następujących
            1. Zestaw SDK w wersji 2.4.1 powyżej zbiera liczników wydajności, jeśli aplikacja jest uruchomiona w usłudze Azure Web App (Windows)
            1. 2.7.0-beta3 wersji zestawu SDK i powyżej zbiera liczników wydajności, jeśli aplikacja jest uruchomiona w Windows i określanie wartości docelowej `NETSTANDARD2.0` lub nowszej.
            1. Dla aplikacji przeznaczonych na pełny program .NET Framework liczniki wydajności są obsługiwane we wszystkich wersjach zestawu SDK.

            Ten dokument będzie aktualizowany, gdy zostanie dodana jego obsługa licznika wydajności w systemie Linux.
    1. [Metryki na żywo](https://docs.microsoft.com/azure/application-insights/app-insights-live-stream)
    1. `ILogger` Dzienniki ważność `Warning` lub nowszym są automatycznie przechwycone z zestawu SDK w wersji 2.7.0-beta3 lub nowszej. Dowiedz się więcej [tutaj](https://docs.microsoft.com/azure/azure-monitor/app/ilogger).

Może upłynąć kilka minut, zanim dane telemetryczne rozpocząć, pojawiają się w portalu. Można szybko sprawdzić, czy wszystko działa, jest najlepiej użyć [metryki na żywo](https://docs.microsoft.com/azure/application-insights/app-insights-live-stream), podczas wprowadzania żądań do uruchomionej aplikacji.

## <a name="send-ilogger-logs-to-application-insights"></a>Wysyłanie dzienników ILogger do usługi Application Insights

Usługa Application Insights obsługuje przechwytywania dzienniki wysłane za pośrednictwem ILogger. Przeczytaj pełną dokumentację [tutaj](https://docs.microsoft.com/azure/azure-monitor/app/ilogger).

## <a name="enable-client-side-telemetry-for-web-applications"></a>Włącz dane telemetryczne po stronie klienta dla aplikacji sieci Web

Powyższe kroki są wystarczające, aby rozpocząć zbieranie danych telemetrycznych po stronie serwera. Jeśli aplikacja ma składniki po stronie klienta, a następnie wykonaj poniższe kroki, aby rozpocząć zbieranie [dane telemetryczne użycia](https://docs.microsoft.com/azure/azure-monitor/app/usage-overview) z tego miejsca.

1. W _ViewImports.cshtml Dodaj iniekcji:

```cshtml
    @inject Microsoft.ApplicationInsights.AspNetCore.JavaScriptSnippet JavaScriptSnippet
```

2. _Layout.cshtml, Wstaw HtmlHelper na końcu `<head>` sekcji, ale przed dowolnego innego skryptu. Wszelkie niestandardowej telemetrii JavaScript, który chcesz sporządzić raport na stronie należy wprowadzony po tym fragmencie kodu:

```cshtml
    @Html.Raw(JavaScriptSnippet.FullScript)
    </head>
```

Modyfikowanie nazwy plików zgodnie z rzeczywiste aplikacje. Nazwy używane powyżej pochodzących od domyślnego szablonu aplikacji MVC.

## <a name="configuring-application-insights-sdk"></a>Konfigurowanie zestawu SDK usługi Application Insights

Zestaw SDK usługi Application Insights dla platformy ASP.NET Core można dostosować w taki sposób, aby zmienić konfigurację domyślną. Użytkownicy z zestawu SDK usługi Application Insights ASP.NET może być zaznajomieni z używaniem konfiguracji `ApplicationInsights.config`, lub modyfikując `TelemetryConfiguration.Active`. Dla platformy ASP.NET Core konfiguracja jest implementowana w różny sposób. Zestaw SDK dla platformy ASP.NET Core zostanie dodany do aplikacji przy użyciu wbudowanych w platformy ASP.NET Core [DependencyInjection](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection) mechanizm i konfigurowanie takie same będą również stosowane DependencyInjection. Niemal wszystkie zmiany konfiguracji są wykonywane w `ConfigureServices()` metody usługi `Startup.cs` klasy, chyba że określono inaczej. Postępuj zgodnie z sekcji poniżej, aby dowiedzieć się więcej.

> [!NOTE]
> Należy pamiętać, że modyfikowania konfiguracji, modyfikując jest `TelemetryConfiguration.Active` nie jest zalecane w aplikacjach ASP.NET Core.

### <a name="configuring-using-applicationinsightsserviceoptions"></a>Konfigurowanie przy użyciu ApplicationInsightsServiceOptions

Istnieje możliwość zmodyfikować kilka typowych ustawień przez przekazanie `ApplicationInsightsServiceOptions` do `services.AddApplicationInsightsTelemetry();`. Poniżej przedstawiono przykład.

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

Dokładna lista konfigurowalnych ustawień w `ApplicationInsightsServiceOptions` można znaleźć [tutaj](https://github.com/Microsoft/ApplicationInsights-aspnetcore/blob/f0b8631e482d25982eb52092103b34e3ff6e5fef/src/Microsoft.ApplicationInsights.AspNetCore/Extensions/ApplicationInsightsServiceOptions.cs).

### <a name="sampling"></a>Próbkowanie

Zestaw SDK usługi Application Insights dla platformy ASP.NET Core obsługuje zarówno FixedRate i próbkowanie adaptacyjne. Próbkowanie adaptacyjne jest domyślnie włączona. Postępuj zgodnie z [to](../../azure-monitor/app/sampling.md#configuring-adaptive-sampling-for-aspnet-core-applications) dokumentu, aby dowiedzieć się, jak skonfigurować próbkowania dla aplikacji platformy ASP.NET Core.

### <a name="adding-telemetryinitializers"></a>Dodawanie TelemetryInitializers

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

Telemetria niestandardowa procesory mogą być dodawane do `TelemetryConfiguration` przy użyciu metody rozszerzenia `AddApplicationInsightsTelemetryProcessor` na `IServiceCollection`. Skorzystaj z poniższego przykładu.

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

W przypadku następujących modułów kolekcji automatycznie są domyślnie włączone i jest odpowiedzialny za automatyczne zbieranie danych telemetrycznych. Może być wyłączone i skonfigurowany tak, aby zmienić zachowanie domyślne.

1. `RequestTrackingTelemetryModule`
1. `DependencyTrackingTelemetryModule`
1. `PerformanceCollectorModule`
1. `QuickPulseTelemetryModule`
1. `AppServicesHeartbeatTelemetryModule`
1. `AzureInstanceMetadataTelemetryModule`

Aby skonfigurować wszelkie domyślne `TelemetryModule`, należy użyć metody rozszerzenia `ConfigureTelemetryModule<T>` na `IServiceCollection` jak pokazano w poniższym przykładzie.

```csharp
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
    public void ConfigureServices(IServiceCollection services)
    {
        // use the following to replace the default channel with InMemoryChannel.
        // this can also be applied to ServerTelemetryChannel as well.
        services.AddSingleton(typeof(ITelemetryChannel), new InMemoryChannel() {MaxTelemetryBufferCapacity = 19898 });

        services.AddApplicationInsightsTelemetry();
    }
```

## <a name="frequently-asked-questions"></a>Często zadawane pytania

*1. Chcę śledzić dodatkowe dane telemetryczne oprócz dane telemetryczne zbierane automatycznie. Jak to zrobić?*

* Uzyskać wystąpienia `TelemetryClient` przez przy użyciu iniekcji konstruktora i wywołać wymagane `TrackXXX()` metody na nim. Nie zaleca się tworzenia nowych `TelemetryClient` wystąpień w aplikacji platformy ASP.NET Core, jako pojedyncze wystąpienie `TelemetryClient` jest już zarejestrowany w kontenerze DI udostępnia `TelemetryConfiguration` z pozostałą częścią danych telemetrycznych. Tworzenie nowego `TelemetryClient` wystąpienia jest zalecane tylko wtedy, gdy musi on mieć osobne konfiguracji od pozostałej części danych telemetrycznych. Poniższy przykład pokazuje, jak śledzić dodatkowe dane telemetryczne z kontrolera.

```csharp
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

 Zapoznaj się [metryki niestandardowe w usłudze Application Insights dokumentacja interfejsu API](https://docs.microsoft.com/azure/azure-monitor/app/api-custom-events-metrics/) opisu danych niestandardowych raportów w usłudze Application Insights.

*2. Niektóre szablony programu Visual Studio umożliwiają — metoda rozszerzenia UseApplicationInsights() na IWebHostBuilder usługi Application Insights. To obciążenie jest nadal ważny?*

* Włączenie usługi Application Insights przy użyciu tej metody jest prawidłowy i jest używany w wdrażania programu Visual Studio oraz z rozszerzeń aplikacji sieci Web platformy Azure. Jednak zaleca się używanie `services.AddApplicationInsightsTelemery()` ponieważ oferuje przeciążenia do kontrolowania pewnych czynności konfiguracyjnych. Obie metody wewnętrznie działa tak samo, tak, jeśli brak niestandardowej konfiguracji mają być stosowane, wywołujący albo jest w dobrym stanie.

*3. Czy mogę jestem wdrażanie Moja aplikacja platformy ASP.NET Core w usłudze Azure Web Apps. Czy mogę nadal należy włączyć rozszerzenie usługi Application Insights z aplikacji sieci Web?*

* Jeśli zestaw SDK jest zainstalowany w czasie kompilacji, jak pokazano w tym artykule, nie ma potrzeby można włączyć rozszerzenia usługi Application Insights z poziomu portalu aplikacji sieci Web. Nawet po zainstalowaniu rozszerzenia go będzie wycofywania, gdy wykryje, że zestaw SDK został już dodany do aplikacji. Włączenie usługi Application Insights z poziomu rozszerzenia zwalnia z instalowania i aktualizowania zestawu SDK do aplikacji. Jednak włączenie usługi Application Insights, zgodnie z tego artykułu jest bardziej elastyczna poniżej powodów.
    1. Telemetria usługi Application Insights będą w dalszym ciągu działać w
        1. Wszystkie systemy operacyjne — Windows, Linux oraz komputerów Mac.
        1. Publikowanie wszystkich tryb — samodzielne lub zależny od struktury.
        1. Wszystkich docelowych platform, w tym pełny program .NET Framework.
        1. Wszystkie opcje hostingu — aplikacja internetowa platformy Azure, maszyny wirtualne, Linux, kontenerów, AKS, spoza platformy Azure.
    1. Dane telemetryczne są widoczne lokalnie, podczas debugowania w programie Visual Studio.
    1. Umożliwia używanie dodatkowych niestandardowej telemetrii śledzenia `TrackXXX()` interfejsu API.
    1. Ma pełną kontrolę nad konfiguracją.

*4. Można włączyć monitorowanie usługi Application Insights przy użyciu narzędzi, takich jak Monitor stanu?*

* Nie. [Monitor stanu](https://docs.microsoft.com/azure/azure-monitor/app/monitor-performance-live-website-now) i nadchodzących zastąpienia [IISConfigurator](https://github.com/Microsoft/ApplicationInsights-Announcements/issues/21) aktualnie obsługuje tylko platformy ASP.NET. Dokument będzie aktualizowany podczas obsługi dla aplikacji platformy ASP.NET Core jest dostępna.

*5. Mam aplikację ASP.NET Core w wersji 2.0? Nie ma usługi Application Insights automatycznie włączone dla nich bez mnie niczym zajęty?*

* `Microsoft.AspNetCore.All` 2.0 meta Microsoft.aspnetcore.all uwzględniona zestawu SDK usługi Application Insights (wersja 2.1.0), a po uruchomieniu aplikacji w debugerze programu Visual Studio, Visual Studio umożliwia usługi application insights i przedstawia dane telemetryczne lokalnie w bezpośrednio ze środowiska IDE. Telemetria nie została wysłana do usługi Application Insights, chyba że jawnie określony klucz instrumentacji. Firma Microsoft zaleca się postępuj zgodnie z instrukcjami w tym artykule, aby włączyć usługę Application Insights, nawet w przypadku 2.0 aplikacji.

*6. Czy mogę uruchomić moją aplikację w systemie Linux. Są wszystkie funkcje obsługiwane w systemie Linux oraz?*

* Tak. Obsługa funkcji zestawu SDK jest taka sama we wszystkich platform, z następującymi wyjątkami:
    1. Liczniki wydajności nie są jeszcze obsługiwane w inne niż Windows. Ten dokument będzie aktualizowany podczas dodawania pomoc techniczna Linux support.
    1. Mimo że `ServerTelemetryChannel` jest włączona domyślnie, jeśli aplikacja jest uruchomiona w innych niż windows, kanał nie tworzy automatycznie folder lokalny magazyn do przechowywania danych telemetrycznych tymczasowo, jeśli występują problemy z siecią. To ograniczenie powoduje, że dane telemetryczne, zostanie utracone w przypadku tymczasowego sieci lub problemy z serwerem. Obejście tego problemu jest dla użytkownika skonfigurować lokalny folder dla kanału, jak pokazano poniżej.

```csharp
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
