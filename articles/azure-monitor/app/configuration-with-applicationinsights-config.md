---
title: ApplicationInsights. config — informacje o platformie Azure | Microsoft Docs
description: Włączanie lub wyłączanie modułów zbierania danych oraz Dodawanie liczników wydajności i innych parametrów.
ms.topic: conceptual
ms.date: 05/22/2019
ms.reviewer: olegan
ms.openlocfilehash: b2c407036277b17c0f8c08f3261c932a6dc66624
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79276181"
---
# <a name="configuring-the-application-insights-sdk-with-applicationinsightsconfig-or-xml"></a>Konfigurowanie zestawu SDK usługi Application Insights za pomocą pliku ApplicationInsights.config lub xml
Zestaw SDK Application Insights platformy .NET składa się z kilku pakietów NuGet. [Pakiet Core](https://www.nuget.org/packages/Microsoft.ApplicationInsights) udostępnia interfejs API do wysyłania danych telemetrycznych do Application Insights. [Dodatkowe pakiety](https://www.nuget.org/packages?q=Microsoft.ApplicationInsights) udostępniają *moduły* telemetrii i *inicjatory* umożliwiające automatyczne śledzenie danych telemetrycznych z aplikacji i jej kontekstu. Dostosowując plik konfiguracji, można włączać lub wyłączać moduły telemetrii i inicjatory oraz ustawiać parametry dla niektórych z nich.

Plik konfiguracji ma nazwę `ApplicationInsights.config` lub `ApplicationInsights.xml`, w zależności od typu aplikacji. Jest on automatycznie dodawany do projektu podczas [instalowania większości wersji zestawu SDK][start]. Domyślnie w przypadku korzystania z zautomatyzowanego środowiska z projektów szablonów programu Visual Studio, które obsługują **dodawanie > Telemetria usługi Application Insights**, plik ApplicationInsights. config jest tworzony w folderze głównym projektu i gdy jest on zgodny, jest kopiowany do folderu bin. Jest ona również dodawana do aplikacji sieci Web przez [Monitor stanu na serwerze IIS][redfield]. Plik konfiguracji jest ignorowany, jeśli jest używane [rozszerzenie witryny sieci Web platformy Azure](azure-web-apps.md) lub [rozszerzenia dla maszyny wirtualnej platformy Azure i zestawu skalowania maszyn wirtualnych](azure-vm-vmss-apps.md) .

Nie istnieje odpowiedni plik do sterowania [zestawem SDK na stronie sieci Web][client].

W tym dokumencie opisano sekcje, które są widoczne w pliku konfiguracji, sposób sterowania składnikami zestawu SDK i pakiety NuGet, które ładują te składniki.

> [!NOTE]
> Instrukcje ApplicationInsights. config i. XML nie mają zastosowania do zestaw .NET Core SDK. Aby skonfigurować aplikacje platformy .NET Core, postępuj zgodnie z [tym](../../azure-monitor/app/asp-net-core.md) przewodnikiem.

## <a name="telemetry-modules-aspnet"></a>Moduły telemetrii (ASP.NET)
Każdy moduł telemetrii zbiera dane określonego typu i używa podstawowego interfejsu API do wysyłania danych. Moduły są instalowane przez różne pakiety NuGet, co powoduje również dodanie wymaganych wierszy do pliku. config.

W pliku konfiguracji dla każdego modułu znajduje się węzeł. Aby wyłączyć moduł, Usuń węzeł lub Skomentuj go.

### <a name="dependency-tracking"></a>Śledzenie zależności
[Śledzenie zależności](../../azure-monitor/app/asp-net-dependencies.md) zbiera dane telemetryczne dotyczące wywołań aplikacji do baz danych i usług zewnętrznych oraz baz danych. Aby zezwolić na działanie tego modułu na serwerze IIS, należy [zainstalować Monitor stanu][redfield].

Możesz również napisać własny kod śledzenia zależności przy użyciu [interfejsu API TrackDependency](../../azure-monitor/app/api-custom-events-metrics.md#trackdependency).

* `Microsoft.ApplicationInsights.DependencyCollector.DependencyTrackingTelemetryModule`
* Pakiet NuGet [Microsoft. ApplicationInsights. DependencyCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector) .

Zależności można zbierać automatycznie bez modyfikowania kodu przy użyciu dołączania (bez kodu). Aby używać go w usłudze Azure Web Apps, Włącz [rozszerzenie Application Insights](azure-web-apps.md). Aby użyć go na maszynie wirtualnej platformy Azure lub zestawu skalowania maszyn wirtualnych platformy Azure, Włącz [rozszerzenie monitorowanie aplikacji dla maszyny wirtualnej i zestawu skalowania maszyn wirtualnych](azure-vm-vmss-apps.md).

### <a name="performance-collector"></a>Moduł zbierający wydajności
[Zbiera liczniki wydajności systemu](../../azure-monitor/app/performance-counters.md) , takie jak procesor CPU, pamięć i obciążenie sieci z instalacji usług IIS. Można określić, które liczniki mają być zbierane, w tym liczniki wydajności, które zostały przez Ciebie skonfigurowane.

* `Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule`
* Pakiet NuGet [Microsoft. ApplicationInsights. PerfCounterCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector) .

### <a name="application-insights-diagnostics-telemetry"></a>Dane telemetryczne diagnostyki Application Insights
`DiagnosticsTelemetryModule` raportuje błędy w samym kodzie Instrumentacji Application Insights. Na przykład, jeśli kod nie może uzyskać dostępu do liczników wydajności lub jeśli `ITelemetryInitializer` zgłasza wyjątek. Dane telemetryczne śledzenia śledzone przez ten moduł pojawiają się w [wyszukiwaniu diagnostycznym][diagnostic].

```
* `Microsoft.ApplicationInsights.Extensibility.Implementation.Tracing.DiagnosticsTelemetryModule`
* [Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights) NuGet package. If you only install this package, the ApplicationInsights.config file is not automatically created.
```

### <a name="developer-mode"></a>Tryb dewelopera
`DeveloperModeWithDebuggerAttachedTelemetryModule` zmusza Application Insights `TelemetryChannel` do natychmiastowego wysłania danych, po dołączeniu debugera do procesu aplikacji. Pozwala to skrócić czas od momentu, w którym aplikacja śledzi dane telemetryczne i pojawia się w portalu Application Insights. Powoduje znaczne obciążenie przepustowości procesora i sieci.

* `Microsoft.ApplicationInsights.WindowsServer.DeveloperModeWithDebuggerAttachedTelemetryModule`
* [Application Insights systemu Windows Server](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/) Pakiet NuGet

### <a name="web-request-tracking"></a>Śledzenie żądań sieci Web
Raportuje [czas odpowiedzi i kod wyniku](../../azure-monitor/app/asp-net.md) żądań HTTP.

* `Microsoft.ApplicationInsights.Web.RequestTrackingTelemetryModule`
* Pakiet NuGet [Microsoft. ApplicationInsights. Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web)

### <a name="exception-tracking"></a>Śledzenie wyjątków
`ExceptionTrackingTelemetryModule` śledzi Nieobsłużone wyjątki w aplikacji sieci Web. Zobacz [błędy i wyjątki][exceptions].

* `Microsoft.ApplicationInsights.Web.ExceptionTrackingTelemetryModule`
* Pakiet NuGet [Microsoft. ApplicationInsights. Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web)
* `Microsoft.ApplicationInsights.WindowsServer.UnobservedExceptionTelemetryModule` — śledzi [niezauważalne wyjątki zadań](https://blogs.msdn.com/b/pfxteam/archive/2011/09/28/task-exception-handling-in-net-4-5.aspx).
* `Microsoft.ApplicationInsights.WindowsServer.UnhandledExceptionTelemetryModule` — śledzi Nieobsłużone wyjątki dla ról procesów roboczych, usług systemu Windows i aplikacji konsolowych.
* [Application Insights systemu Windows Server](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/) Pakiet NuGet.

### <a name="eventsource-tracking"></a>Śledzenie zdarzeń EventSource
`EventSourceTelemetryModule` umożliwia skonfigurowanie zdarzeń EventSource do wysłania do Application Insights jako śladów. Aby uzyskać informacje dotyczące śledzenia zdarzeń EventSource, zobacz [Korzystanie z zdarzeń EventSource](../../azure-monitor/app/asp-net-trace-logs.md#use-eventsource-events).

* `Microsoft.ApplicationInsights.EventSourceListener.EventSourceTelemetryModule`
* [Microsoft. ApplicationInsights. EventSourceListener](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener) 

### <a name="etw-event-tracking"></a>Śledzenie zdarzeń ETW
`EtwCollectorTelemetryModule` umożliwia skonfigurowanie zdarzeń od dostawców ETW do wysłania do Application Insights jako śladów. Aby uzyskać informacje dotyczące śledzenia zdarzeń ETW, zobacz [Korzystanie z zdarzeń ETW](../../azure-monitor/app/asp-net-trace-logs.md#use-etw-events).

* `Microsoft.ApplicationInsights.EtwCollector.EtwCollectorTelemetryModule`
* [Microsoft. ApplicationInsights. EtwCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EtwCollector) 

### <a name="microsoftapplicationinsights"></a>Microsoft.ApplicationInsights
Pakiet Microsoft. ApplicationInsights udostępnia [podstawowy interfejs API](https://msdn.microsoft.com/library/mt420197.aspx) zestawu SDK. Inne moduły telemetrii używają tego programu i można również [użyć go do zdefiniowania własnej telemetrii](../../azure-monitor/app/api-custom-events-metrics.md).

* Brak wpisu w pliku ApplicationInsights. config.
* Pakiet NuGet [Microsoft. ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights) . Jeśli instalujesz tylko ten pakiet NuGet, plik. config nie zostanie wygenerowany.

## <a name="telemetry-channel"></a>Kanał telemetrii
[Kanał telemetrii](telemetry-channels.md) zarządza buforowaniem i przesyłaniem danych telemetrycznych do usługi Application Insights.

* `Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel` to domyślny kanał dla aplikacji sieci Web. Buforuje dane w pamięci i korzysta z mechanizmów ponawiania prób i lokalnego magazynu dyskowego w celu zapewnienia większej niezawodnego dostarczania danych telemetrycznych.
* `Microsoft.ApplicationInsights.InMemoryChannel` jest lekkim kanałem telemetrii, który jest używany, jeśli nie skonfigurowano żadnego innego kanału. 

## <a name="telemetry-initializers-aspnet"></a>Telemetry Initializers (ASP.NET)
Inicjatory telemetrii ustawiają właściwości kontekstu, które są wysyłane wraz z każdym elementem telemetrii.

Można [napisać własne inicjatory](../../azure-monitor/app/api-filtering-sampling.md#add-properties) , aby ustawić właściwości kontekstu.

Standardowe inicjatory są ustawiane przez pakiety NuGet sieci Web lub WindowsServer:

* `AccountIdTelemetryInitializer` ustawia właściwość AccountId.
* `AuthenticatedUserIdTelemetryInitializer` ustawia właściwość AuthenticatedUserId jako ustawioną przez zestaw SDK języka JavaScript.
* `AzureRoleEnvironmentTelemetryInitializer` aktualizuje właściwości `RoleName` i `RoleInstance` kontekstu `Device` dla wszystkich elementów telemetrycznych o informacjach wyodrębnionych ze środowiska uruchomieniowego platformy Azure.
* `BuildInfoConfigComponentVersionTelemetryInitializer` aktualizuje właściwość `Version` kontekstu `Component` dla wszystkich elementów telemetrii o wartości wyodrębnionej z pliku `BuildInfo.config` utworzonego przez kompilację MS.
* `ClientIpHeaderTelemetryInitializer` aktualizacje `Ip` kontekstu `Location` wszystkich elementów telemetrii na podstawie nagłówka HTTP `X-Forwarded-For` żądania.
* `DeviceTelemetryInitializer` aktualizuje następujące właściwości kontekstu `Device` dla wszystkich elementów telemetrii.
  * `Type` jest ustawiona na "PC"
  * `Id` jest ustawiona na nazwę domeny komputera, na którym działa aplikacja sieci Web.
  * `OemName` jest ustawiona na wartość wyodrębnioną z pola `Win32_ComputerSystem.Manufacturer` za pomocą usługi WMI.
  * `Model` jest ustawiona na wartość wyodrębnioną z pola `Win32_ComputerSystem.Model` za pomocą usługi WMI.
  * `NetworkType` jest ustawiona na wartość wyodrębnioną z `NetworkInterface`.
  * `Language` jest ustawiona na nazwę `CurrentCulture`.
* `DomainNameRoleInstanceTelemetryInitializer` aktualizuje właściwość `RoleInstance` kontekstu `Device` dla wszystkich elementów telemetrii nazwą domeny komputera, na którym działa aplikacja sieci Web.
* `OperationNameTelemetryInitializer` aktualizuje właściwość `Name` `RequestTelemetry` oraz Właściwość `Name` kontekstu `Operation` wszystkich elementów telemetrii na podstawie metody HTTP, a także nazwy kontrolera i akcji ASP.NET MVC, które zostały wywołane w celu przetworzenia żądania.
* `OperationIdTelemetryInitializer` lub `OperationCorrelationTelemetryInitializer` aktualizuje Właściwość kontekstu `Operation.Id` wszystkich elementów telemetrii śledzonych podczas obsługi żądania z automatycznie wygenerowanym `RequestTelemetry.Id`.
* `SessionTelemetryInitializer` aktualizuje właściwość `Id` kontekstu `Session` dla wszystkich elementów telemetrii z wartością wyodrębnioną z `ai_session` pliku cookie wygenerowanego przez kod Instrumentacji JavaScript ApplicationInsights uruchomiony w przeglądarce użytkownika.
* `SyntheticTelemetryInitializer` lub `SyntheticUserAgentTelemetryInitializer` aktualizuje właściwości kontekstów `User`, `Session`i `Operation` wszystkich elementów telemetrii śledzonych podczas obsługi żądania ze źródła syntetycznego, takiego jak Test dostępności lub bot aparatu wyszukiwania. Domyślnie [Eksplorator metryk](../../azure-monitor/app/metrics-explorer.md) nie wyświetla syntetycznej danych telemetrycznych.

    `<Filters>` ustawia właściwości identyfikujące żądania.
* `UserTelemetryInitializer` aktualizuje właściwości `Id` i `AcquisitionDate` kontekstu `User` dla wszystkich elementów telemetrycznych z wartościami wyodrębnionymi z `ai_user` pliku cookie wygenerowanych przez kod Instrumentacji języka JavaScript, uruchomiony w przeglądarce użytkownika.
* `WebTestTelemetryInitializer` ustawia identyfikator użytkownika, identyfikator sesji i syntetyczne właściwości źródła dla żądań HTTP, które pochodzą z [testów dostępności](../../azure-monitor/app/monitor-web-app-availability.md).
  `<Filters>` ustawia właściwości identyfikujące żądania.

W przypadku aplikacji .NET działających w Service Fabric można uwzględnić pakiet `Microsoft.ApplicationInsights.ServiceFabric` NuGet. Ten pakiet zawiera `FabricTelemetryInitializer`, które dodaje właściwości Service Fabric do elementów telemetrii. Aby uzyskać więcej informacji, zobacz [stronę usługi GitHub](https://github.com/Microsoft/ApplicationInsights-ServiceFabric/blob/master/README.md) dotyczącą właściwości dodanych przez ten pakiet NuGet.

## <a name="telemetry-processors-aspnet"></a>Procesory telemetrii (ASP.NET)
Procesory telemetrii mogą filtrować i modyfikować poszczególne elementy telemetrii tuż przed wysłaniem ich z zestawu SDK do portalu.

Można [napisać własne procesory telemetrii](../../azure-monitor/app/api-filtering-sampling.md#filtering).

#### <a name="adaptive-sampling-telemetry-processor-from-200-beta3"></a>Procesor telemetrii próbkowania adaptacyjnego (z 2.0.0-beta3)
Ta opcja jest domyślnie włączona. Jeśli aplikacja wysyła wiele danych telemetrycznych, ten procesor usuwa niektóre z nich.

```xml

    <TelemetryProcessors>
      <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
        <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
      </Add>
    </TelemetryProcessors>

```

Parametr zawiera element docelowy, który ma zostać osiągnięty przez algorytm. Każde wystąpienie zestawu SDK działa niezależnie, więc jeśli serwer jest klastrem kilku maszyn, rzeczywista ilość danych telemetrycznych zostanie odpowiednio pomnożona.

[Dowiedz się więcej na temat próbkowania](../../azure-monitor/app/sampling.md).

#### <a name="fixed-rate-sampling-telemetry-processor-from-200-beta1"></a>Procesor telemetrii próbkowania o stałym współczynniku (z 2.0.0-Beta1)
Istnieje również standardowy [procesor próbkowania telemetrii](../../azure-monitor/app/api-filtering-sampling.md) (z 2.0.1):

```XML

    <TelemetryProcessors>
     <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.SamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">

     <!-- Set a percentage close to 100/N where N is an integer. -->
     <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 20, 1 (=100/100), 0.1 (=100/1000) -->
     <SamplingPercentage>10</SamplingPercentage>
     </Add>
   </TelemetryProcessors>

```



## <a name="channel-parameters-java"></a>Parametry kanału (Java)
Te parametry mają wpływ na sposób przechowywania i opróżniania danych telemetrycznych zbieranych przez program Java SDK.

#### <a name="maxtelemetrybuffercapacity"></a>MaxTelemetryBufferCapacity
Liczba elementów telemetrycznych, które mogą być przechowywane w magazynie w pamięci zestawu SDK. Po osiągnięciu tego numeru bufor telemetrii jest opróżniany — oznacza to, że elementy telemetrii są wysyłane do serwera Application Insights.

* Min.: 1
* Maks.: 1000
* Wartość domyślna: 500

```

  <ApplicationInsights>
      ...
      <Channel>
       <MaxTelemetryBufferCapacity>100</MaxTelemetryBufferCapacity>
      </Channel>
      ...
  </ApplicationInsights>
```

#### <a name="flushintervalinseconds"></a>FlushIntervalInSeconds
Określa, jak często dane przechowywane w magazynie w pamięci powinny być opróżniane (wysyłane do Application Insights).

* Min.: 1
* Maks.: 300
* Wartość domyślna: 5

```

    <ApplicationInsights>
      ...
      <Channel>
        <FlushIntervalInSeconds>100</FlushIntervalInSeconds>
      </Channel>
      ...
    </ApplicationInsights>
```

#### <a name="maxtransmissionstoragecapacityinmb"></a>MaxTransmissionStorageCapacityInMB
Określa maksymalny rozmiar w MB przydzielony do magazynu trwałego na dysku lokalnym. Ten magazyn służy do utrwalania elementów telemetrycznych, które nie zostały przesłane do punktu końcowego Application Insights. Po spełnieniu rozmiaru magazynu nowe elementy telemetrii zostaną odrzucone.

* Min.: 1
* Maks.: 100
* Wartość domyślna: 10

```

   <ApplicationInsights>
      ...
      <Channel>
        <MaxTransmissionStorageCapacityInMB>50</MaxTransmissionStorageCapacityInMB>
      </Channel>
      ...
   </ApplicationInsights>
```

## <a name="instrumentationkey"></a>InstrumentationKey
Określa zasób Application Insights, w którym będą wyświetlane dane. Zwykle tworzony jest osobny zasób z osobnym kluczem dla każdej aplikacji.

Jeśli chcesz ustawić klucz dynamicznie — na przykład jeśli chcesz wysłać wyniki z aplikacji do różnych zasobów, możesz pominąć klucz z pliku konfiguracji i ustawić go w kodzie.

Aby ustawić klucz dla wszystkich wystąpień TelemetryClient, w tym standardowych modułów telemetrycznych. Zrób to w metodzie inicjującej, takiej jak global.aspx.cs w usłudze ASP.NET:

```csharp
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.ApplicationInsights;

    protected void Application_Start()
    {
        TelemetryConfiguration configuration = TelemetryConfiguration.CreateDefault();
        configuration.InstrumentationKey = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx";
        var telemetryClient = new TelemetryClient(configuration);
   
```

Jeśli chcesz tylko wysłać określony zestaw zdarzeń do innego zasobu, możesz ustawić klucz dla określonego TelemetryClient:

```csharp

    var tc = new TelemetryClient();
    tc.Context.InstrumentationKey = "----- my key ----";
    tc.TrackEvent("myEvent");
    // ...

```

Aby uzyskać nowy klucz, [Utwórz nowy zasób w portalu Application Insights][new].



## <a name="applicationid-provider"></a>Dostawca identyfikatorów aplikacji

_Dostępne począwszy od 2.6.0 v_

Ten dostawca polega na wyszukiwaniu identyfikatora aplikacji opartego na kluczu Instrumentacji. Identyfikator aplikacji jest zawarty w RequestTelemetry i DependencyTelemetry i służy do określania korelacji w portalu.

Jest to dostępne przez ustawienie `TelemetryConfiguration.ApplicationIdProvider` w kodzie lub w pliku config.

### <a name="interface-iapplicationidprovider"></a>Interfejs: IApplicationIdProvider

```csharp
public interface IApplicationIdProvider
{
    bool TryGetApplicationId(string instrumentationKey, out string applicationId);
}
```


Udostępniamy dwie implementacje w zestawie SDK [Microsoft. ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights) : `ApplicationInsightsApplicationIdProvider` i `DictionaryApplicationIdProvider`.

### <a name="applicationinsightsapplicationidprovider"></a>ApplicationInsightsApplicationIdProvider

Jest to otoka otaczająca nasz interfejs API profilu. Będzie ograniczać żądania i przechować wyniki.

Ten dostawca jest dodawany do pliku konfiguracji podczas instalowania [programu Microsoft. ApplicationInsights. DependencyCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector) lub [Microsoft. ApplicationInsights. Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/)

Ta klasa ma opcjonalną Właściwość `ProfileQueryEndpoint`.
Domyślnie jest to wartość `https://dc.services.visualstudio.com/api/profiles/{0}/appId`.
Jeśli musisz skonfigurować serwer proxy dla tej konfiguracji, zalecamy adresowanie serwera proxy, a w tym "/API/Profiles/{0}/appId". Należy pamiętać, że element "{0}" został zastąpiony w czasie wykonywania na żądanie z kluczem Instrumentacji.

#### <a name="example-configuration-via-applicationinsightsconfig"></a>Przykładowa konfiguracja za pośrednictwem ApplicationInsights. config:
```xml
<ApplicationInsights>
    ...
    <ApplicationIdProvider Type="Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId.ApplicationInsightsApplicationIdProvider, Microsoft.ApplicationInsights">
        <ProfileQueryEndpoint>https://dc.services.visualstudio.com/api/profiles/{0}/appId</ProfileQueryEndpoint>
    </ApplicationIdProvider>
    ...
</ApplicationInsights>
```

#### <a name="example-configuration-via-code"></a>Przykładowa konfiguracja za pośrednictwem kodu:
```csharp
TelemetryConfiguration.Active.ApplicationIdProvider = new ApplicationInsightsApplicationIdProvider();
```

### <a name="dictionaryapplicationidprovider"></a>DictionaryApplicationIdProvider

Jest to dostawca statyczny, który będzie zależeć od skonfigurowanych par kluczy/identyfikatorów aplikacji.

Ta klasa ma właściwość `Defined`, która jest ciągiem < String, ciąg > klucza instrumentacji do par identyfikatorów aplikacji.

Ta klasa ma opcjonalną Właściwość `Next` której można użyć do skonfigurowania innego dostawcy do użycia, gdy żądany jest klucz instrumentacji, który nie istnieje w konfiguracji.

#### <a name="example-configuration-via-applicationinsightsconfig"></a>Przykładowa konfiguracja za pośrednictwem ApplicationInsights. config:
```xml
<ApplicationInsights>
    ...
    <ApplicationIdProvider Type="Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId.DictionaryApplicationIdProvider, Microsoft.ApplicationInsights">
        <Defined>
            <Type key="InstrumentationKey_1" value="ApplicationId_1"/>
            <Type key="InstrumentationKey_2" value="ApplicationId_2"/>
        </Defined>
        <Next Type="Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId.ApplicationInsightsApplicationIdProvider, Microsoft.ApplicationInsights" />
    </ApplicationIdProvider>
    ...
</ApplicationInsights>
```

#### <a name="example-configuration-via-code"></a>Przykładowa konfiguracja za pośrednictwem kodu:
```csharp
TelemetryConfiguration.Active.ApplicationIdProvider = new DictionaryApplicationIdProvider{
 Defined = new Dictionary<string, string>
    {
        {"InstrumentationKey_1", "ApplicationId_1"},
        {"InstrumentationKey_2", "ApplicationId_2"}
    }
};
```




## <a name="next-steps"></a>Następne kroki
[Dowiedz się więcej o interfejsie API][api].

<!--Link references-->

[api]: ../../azure-monitor/app/api-custom-events-metrics.md
[client]: ../../azure-monitor/app/javascript.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[exceptions]: ../../azure-monitor/app/asp-net-exceptions.md
[netlogs]: ../../azure-monitor/app/asp-net-trace-logs.md
[new]: ../../azure-monitor/app/create-new-resource.md 
[redfield]: ../../azure-monitor/app/monitor-performance-live-website-now.md
[start]: ../../azure-monitor/app/app-insights-overview.md
