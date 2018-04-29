---
title: Odwołanie ApplicationInsights.config - Azure | Dokumentacja firmy Microsoft
description: Włącz lub wyłącz modułów zbierania danych i Dodaj liczniki wydajności i innych parametrów.
services: application-insights
documentationcenter: ''
author: OlegAnaniev-MSFT
editor: mrbullwinkle
manager: carmonm
ms.assetid: 6e397752-c086-46e9-8648-a1196e8078c2
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 05/03/2017
ms.author: mbullwin
ms.openlocfilehash: 94b6864bec157694e0192597c0fecfa0d3e407ec
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2018
---
# <a name="configuring-the-application-insights-sdk-with-applicationinsightsconfig-or-xml"></a>Konfigurowanie zestawu SDK usługi Application Insights za pomocą pliku ApplicationInsights.config lub xml
Zestaw SDK usługi Application Insights .NET składa się z liczby pakietów NuGet. [Pakiet podstawowy](http://www.nuget.org/packages/Microsoft.ApplicationInsights) udostępnia interfejs API wysyłania danych telemetrycznych do usługi Application Insights. [Dodatkowe pakiety](http://www.nuget.org/packages?q=Microsoft.ApplicationInsights) Podaj dane telemetryczne *modułów* i *inicjatory* automatycznie śledzenia dane telemetryczne z aplikacji i jej kontekstu. Dostosowując plik konfiguracji, można włączyć lub wyłączyć inicjatory i moduły danych telemetrycznych i ustaw parametry dla niektórych z nich.

Plik konfiguracji ma nazwę `ApplicationInsights.config` lub `ApplicationInsights.xml`, w zależności od typu aplikacji. Jest automatycznie dodawany do projektu po możesz [zainstalować większość wersji zestawu SDK][start]. Jest także dodawane do aplikacji sieci web przez [Monitor stanu na serwerze IIS][redfield], lub po wybraniu usługi Application Insights [rozszerzenia dla witryny sieci Web platformy Azure lub wirtualna](app-insights-azure-web-apps.md).

Nie ma równoważny plik do kontroli [SDK na stronie sieci web][client].

W tym dokumencie opisano sekcje, które widać w konfiguracji plików i sposobu ich kontrolowania składniki zestawu SDK, i które pakiety NuGet obciążenia tych składników.

> [!NOTE]
> ApplicationInsights.config i XML instrukcje nie dotyczą zestawu SDK .NET Core. Wprowadzenie zmian w aplikacji platformy .NET Core używamy są zazwyczaj plik appsettings.json. Na przykład można znaleźć w [dokumentacją debugera migawki.](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-snapshot-debugger#configure-snapshot-collection-for-aspnet-core-20-applications)

## <a name="telemetry-modules-aspnet"></a>Moduły danych telemetrycznych (ASP.NET)
Każdy moduł telemetrii zbiera określonego typu danych i wysyła dane przy użyciu core interfejsu API. Moduły są instalowane przez różne pakiety NuGet, które również dodać wymagane wiersze do pliku .config.

Brak węzła w pliku konfiguracji dla każdego modułu. Aby wyłączyć moduł, Usuń węzeł lub komentarz go.

### <a name="dependency-tracking"></a>Śledzenia zależności
[Śledzenia zależności](app-insights-asp-net-dependencies.md) zbiera dane telemetryczne dotyczące wywołania aplikacji sprawia, że do baz danych i baz danych i usług zewnętrznych. Aby umożliwić tego modułu do pracy w serwerze IIS, musisz [Zainstaluj Monitor stanu][redfield]. Aby użyć go w aplikacji sieci web platformy Azure lub maszyn wirtualnych, [wybierz rozszerzenia usługi Application Insights](app-insights-azure-web-apps.md).

Można również napisać własny zależności śledzenia kodu za pomocą [TrackDependency API](app-insights-api-custom-events-metrics.md#trackdependency).

* `Microsoft.ApplicationInsights.DependencyCollector.DependencyTrackingTelemetryModule`
* [Microsoft.ApplicationInsights.DependencyCollector](http://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector) NuGet package.

### <a name="performance-collector"></a>Moduł zbierający wydajności
[Zbiera dane liczników wydajności systemu](app-insights-performance-counters.md) na przykład procesora CPU, pamięci i sieci obciążenia z instalacji usług IIS. Można określić które liczniki do zbierania, łącznie z liczników wydajności, które zostały skonfigurowane samodzielnie.

* `Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule`
* [Microsoft.ApplicationInsights.PerfCounterCollector](http://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector) NuGet package.

### <a name="application-insights-diagnostics-telemetry"></a>Diagnostyka Telemetrię usługi Application Insights
`DiagnosticsTelemetryModule` Raporty błędów w kodzie Instrumentacji usługi Application Insights samej siebie. Na przykład jeśli kod nie może uzyskać dostępu do liczników wydajności lub `ITelemetryInitializer` zgłasza wyjątek. Dane telemetryczne śledzenia śledzone przez ten moduł jest wyświetlana w [diagnostycznych wyszukiwania][diagnostic]. Wysyła dane diagnostyczne do dc.services.vsallin.net.

* `Microsoft.ApplicationInsights.Extensibility.Implementation.Tracing.DiagnosticsTelemetryModule`
* [Microsoft.ApplicationInsights](http://www.nuget.org/packages/Microsoft.ApplicationInsights) pakietu NuGet. Po zainstalowaniu tylko ten pakiet, plik ApplicationInsights.config nie jest tworzony automatycznie.

### <a name="developer-mode"></a>Tryb dewelopera
`DeveloperModeWithDebuggerAttachedTelemetryModule` Wymusza usługi Application Insights `TelemetryChannel` Aby wysłać dane od razu, telemetrii jeden element w czasie, gdy debuger jest dołączony do procesu aplikacji. Zmniejsza to ilość czasu od chwili, gdy aplikacja śledzi telemetrii i wyświetlanym na portalu Application Insights. Powoduje znaczne obciążenie procesora CPU i sieci przepustowości.

* `Microsoft.ApplicationInsights.WindowsServer.DeveloperModeWithDebuggerAttachedTelemetryModule`
* [Application Insights w systemie Windows Server](http://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/) pakietu NuGet

### <a name="web-request-tracking"></a>Śledzenie żądań sieci Web
Raporty [kod odpowiedzi czasu i wynik](app-insights-asp-net.md) żądań HTTP.

* `Microsoft.ApplicationInsights.Web.RequestTrackingTelemetryModule`
* [Microsoft.ApplicationInsights.Web](http://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) NuGet package

### <a name="exception-tracking"></a>Śledzenie wyjątków
`ExceptionTrackingTelemetryModule` śledzi nieobsługiwanych wyjątków w aplikacji sieci web. Zobacz [błędy i wyjątki][exceptions].

* `Microsoft.ApplicationInsights.Web.ExceptionTrackingTelemetryModule`
* [Microsoft.ApplicationInsights.Web](http://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) NuGet package
* `Microsoft.ApplicationInsights.WindowsServer.UnobservedExceptionTelemetryModule` -śledzi [być niezauważalna wyjątki zadań](http://blogs.msdn.com/b/pfxteam/archive/2011/09/28/task-exception-handling-in-net-4-5.aspx).
* `Microsoft.ApplicationInsights.WindowsServer.UnhandledExceptionTelemetryModule` -śledzi nieobsługiwanych wyjątków dla procesu roboczego ról, usług systemu windows i aplikacji konsoli.
* [Application Insights w systemie Windows Server](http://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/) pakietu NuGet.

### <a name="eventsource-tracking"></a>Źródła zdarzeń śledzenia
`EventSourceTelemetryModule` Umożliwia skonfigurowanie EventSource zdarzenia mają być wysyłane do usługi Application Insights jako dane śledzenia. Informacje dotyczące śledzenia zdarzeń źródła zdarzeń, zobacz [za pomocą zdarzeń EventSource](app-insights-asp-net-trace-logs.md#using-eventsource-events).

* `Microsoft.ApplicationInsights.EventSourceListener.EventSourceTelemetryModule`
* [Microsoft.ApplicationInsights.EventSourceListener](http://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener) 

### <a name="etw-event-tracking"></a>Śledzenie zdarzeń ETW.
`EtwCollectorTelemetryModule` Umożliwia skonfigurowanie zdarzeń od dostawcy ETW do wysłania do usługi Application Insights jako dane śledzenia. Aby uzyskać informacji na temat śledzenia zdarzeń funkcji ETW, zobacz [przy użyciu zdarzenia ETW](app-insights-asp-net-trace-logs.md#using-etw-events).

* `Microsoft.ApplicationInsights.EtwCollector.EtwCollectorTelemetryModule`
* [Microsoft.ApplicationInsights.EtwCollector](http://www.nuget.org/packages/Microsoft.ApplicationInsights.EtwCollector) 

### <a name="microsoftapplicationinsights"></a>Microsoft.ApplicationInsights
Udostępnia pakiet Microsoft.ApplicationInsights [core API](https://msdn.microsoft.com/library/mt420197.aspx) zestawu SDK. Użyj innych modułów telemetrii i można również [użyj go do zdefiniowania własnych telemetrii](app-insights-api-custom-events-metrics.md).

* Nie wpisu w ApplicationInsights.config.
* [Microsoft.ApplicationInsights](http://www.nuget.org/packages/Microsoft.ApplicationInsights) pakietu NuGet. Po zainstalowaniu właśnie ta NuGet, nie pliku .config jest generowany.

## <a name="telemetry-channel"></a>Kanał telemetrii
Kanał danych telemetrycznych zarządza buforowania i przekazywania telemetrii usługi Application Insights.

* `Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel` jest domyślnym kanale dla usług. Buforuje dane w pamięci.
* `Microsoft.ApplicationInsights.PersistenceChannel` jest to alternatywa dla aplikacji konsoli. Gdy aplikacji zostanie zamknięte w dół i wysyła go po uruchomieniu aplikacji ponownie może pomóc zaoszczędzić unflushed danych do magazynu trwałego.

## <a name="telemetry-initializers-aspnet"></a>Telemetry Initializers (ASP.NET)
Inicjatory telemetrii ustawiania właściwości kontekstu, które są wysyłane wraz ze wszystkich elementów telemetrii.

Możesz [napisać własny inicjatory](app-insights-api-filtering-sampling.md#add-properties) można ustawić właściwości kontekstu.

Inicjatory standardowe jest ustawiony przez pakiety sieci Web lub Windows Server NuGet:

* `AccountIdTelemetryInitializer` Ustawia właściwość AccountId.
* `AuthenticatedUserIdTelemetryInitializer` Ustawia właściwość AuthenticatedUserId zgodnie z ustawieniami JavaScript SDK.
* `AzureRoleEnvironmentTelemetryInitializer` aktualizacje `RoleName` i `RoleInstance` właściwości `Device` kontekst dla wszystkich elementów telemetrii informacjami wyodrębnionymi z środowiska uruchomieniowego platformy Azure.
* `BuildInfoConfigComponentVersionTelemetryInitializer` aktualizacje `Version` właściwość `Component` kontekst dla wszystkich elementów danych telemetrycznych z wartością wyodrębniony z `BuildInfo.config` za pomocą MS Build.
* `ClientIpHeaderTelemetryInitializer` aktualizacje `Ip` właściwość `Location` na podstawie kontekstu wszystkich elementów telemetrii `X-Forwarded-For` nagłówka HTTP żądania.
* `DeviceTelemetryInitializer` aktualizacje następujących właściwości `Device` kontekst dla wszystkich elementów telemetrii.
  * `Type` ma ustawioną wartość "Komputer"
  * `Id` ustawiono nazwę domeny komputera, na którym działa aplikacja sieci web.
  * `OemName` ma ustawioną wartość wyodrębniony z `Win32_ComputerSystem.Manufacturer` pola przy użyciu usługi WMI.
  * `Model` ma ustawioną wartość wyodrębniony z `Win32_ComputerSystem.Model` pola przy użyciu usługi WMI.
  * `NetworkType` ma ustawioną wartość wyodrębniony z `NetworkInterface`.
  * `Language` ustawiono nazwę `CurrentCulture`.
* `DomainNameRoleInstanceTelemetryInitializer` aktualizacje `RoleInstance` właściwość `Device` kontekst dla wszystkich elementów danych telemetrycznych z nazwą domeny komputera, na którym działa aplikacja sieci web.
* `OperationNameTelemetryInitializer` aktualizacje `Name` właściwość `RequestTelemetry` i `Name` właściwość `Operation` kontekstu wszystkich elementów telemetrii oparte na metodę HTTP, jak również nazwy platformy ASP.NET MVC kontroler i akcja wywoływane w celu przetworzenia żądania.
* `OperationIdTelemetryInitializer` lub `OperationCorrelationTelemetryInitializer` aktualizacje `Operation.Id` właściwości kontekstu wszystkich elementów telemetrii śledzone podczas obsługi żądania z automatycznie wygenerowanym `RequestTelemetry.Id`.
* `SessionTelemetryInitializer` aktualizacje `Id` właściwość `Session` kontekst dla wszystkich elementów danych telemetrycznych z wartością wyodrębniony z `ai_session` wygenerowane przez kod Instrumentacji ApplicationInsights JavaScript w przeglądarce plików cookie.
* `SyntheticTelemetryInitializer` lub `SyntheticUserAgentTelemetryInitializer` aktualizacje `User`, `Session` i `Operation` kontekstów właściwości wszystkich elementów telemetrii śledzone podczas przetwarzania żądania z syntetycznego źródła, takich jak dostępności testu lub bot aparatu wyszukiwania. Domyślnie [Eksploratora metryk](app-insights-metrics-explorer.md) syntetycznych telemetrii nie są wyświetlane.

    `<Filters>` Ustawić identyfikowanie właściwości żądania.
* `UserTelemetryInitializer` aktualizacje `Id` i `AcquisitionDate` właściwości `User` kontekst dla wszystkich elementów danych telemetrycznych z wartościami wyodrębniony z `ai_user` wygenerowane przez kod instrumentacji aplikacji JavaScript wgląd w użytkownika w pliku cookie Przeglądarka.
* `WebTestTelemetryInitializer` Ustawia identyfikator użytkownika, identyfikator sesji i właściwości syntetycznego źródła dla tego dostarczanych w żądaniach HTTP [testów dostępności](app-insights-monitor-web-app-availability.md).
  `<Filters>` Ustawić identyfikowanie właściwości żądania.

Dla aplikacji .NET działających w sieci szkieletowej usług, można dołączyć `Microsoft.ApplicationInsights.ServiceFabric` pakietu NuGet. Ten pakiet zawiera `FabricTelemetryInitializer`, która dodaje właściwości sieci szkieletowej usług do elementów telemetrii. Aby uzyskać więcej informacji, zobacz [GitHub strony](https://go.microsoft.com/fwlink/?linkid=848457) o właściwościach dodane przez ten pakiet NuGet.

## <a name="telemetry-processors-aspnet"></a>Dane telemetryczne procesorów (ASP.NET)
Dane telemetryczne procesorów można filtrować i zmodyfikować każdy element telemetrii tuż przed wysłaniem z zestawu SDK do portalu.

Możesz [zapisu procesorów telemetrii](app-insights-api-filtering-sampling.md#filtering).

#### <a name="adaptive-sampling-telemetry-processor-from-200-beta3"></a>Procesor telemetrii próbkowania adaptacyjną (od 2.0.0-beta3)
Ta opcja jest domyślnie włączona. Jeśli aplikacja wyśle dużej ilości danych telemetrii, spowoduje usunięcie tego procesora, część z nich.

```xml

    <TelemetryProcessors>
      <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
        <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
      </Add>
    </TelemetryProcessors>

```

Parametr zawiera element docelowy, który algorytm próbuje osiągnąć. Każde wystąpienie zestawu SDK działa niezależnie, więc jeśli serwer działa w klastrze kilka maszyn, rzeczywista ilość danych telemetrycznych będzie mnożona odpowiednio.

[Dowiedz się więcej o próbkowania](app-insights-sampling.md).

#### <a name="fixed-rate-sampling-telemetry-processor-from-200-beta1"></a>Procesor telemetrii stałej częstotliwość próbkowania (od 2.0.0-beta1)
Istnieje również standard [próbkowanie procesora telemetrii](app-insights-api-filtering-sampling.md) (od 2.0.1):

```XML

    <TelemetryProcessors>
     <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.SamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">

     <!-- Set a percentage close to 100/N where N is an integer. -->
     <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 20, 1 (=100/100), 0.1 (=100/1000) -->
     <SamplingPercentage>10</SamplingPercentage>
     </Add>
   </TelemetryProcessors>

```



## <a name="channel-parameters-java"></a>Parametrów kanału (Java)
Parametry te wpływa na sposób zestawu Java SDK należy przechowywać i opróżnić zbiera dane telemetryczne.

#### <a name="maxtelemetrybuffercapacity"></a>MaxTelemetryBufferCapacity
Liczba elementów dane telemetryczne, które mogą być przechowywane w magazynie w pamięci w zestawie SDK. Po osiągnięciu tej liczby jest opróżniany buforu telemetrii — to znaczy elementy dane telemetryczne są wysyłane do serwera usługi Application Insights.

* Min: 1
* Maksymalna liczba: 1000
* Domyślne: 500

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
Określa, jak często dane, które są przechowywane w magazynie w pamięci mają zostać opróżnione (wysłane do usługi Application Insights).

* Min: 1
* Maksymalna liczba: 300
* Domyślne: 5

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
Określa maksymalny rozmiar w MB, który jest przydzielony do magazynu trwałego na dysku lokalnym. Magazyn ten jest używany dla trwałych elementów telemetrii, które nie mają być przekazywane do punktu końcowego usługi Application Insights. Po spełnieniu rozmiar magazynu nowych elementów telemetrii zostaną odrzucone.

* Min: 1
* Maksymalna: 100
* Domyślny: 10

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
Określa zasób usługi Application Insights, w którym dane są wyświetlane. Zwykle tworzenia oddzielnych zasobu, za pomocą osobnych klucza dla poszczególnych aplikacji.

Jeśli chcesz ustawić klucz dynamicznie — na przykład, jeśli chcesz wysłać wyniki z aplikacji do różnych zasobów — można pominąć klucz z pliku konfiguracji i ustaw go w kodzie.

Aby ustawić klucz dla wszystkich wystąpień TelemetryClient, moduły standardowe telemetrii ustawić klucza TelemetryConfiguration.Active. Wykonaj następujące czynności w metodzie inicjowania, takich jak pliku global.aspx.cs w usługi ASP.NET:

```csharp

    protected void Application_Start()
    {
      Microsoft.ApplicationInsights.Extensibility.
        TelemetryConfiguration.Active.InstrumentationKey =
          // - for example -
          WebConfigurationManager.Settings["ikey"];
      //...
```

Jeśli chcesz wysłać określonych zdarzeń do różnych zasobów, można ustawić klucza dla określonych TelemetryClient:

```csharp

    var tc = new TelemetryClient();
    tc.Context.InstrumentationKey = "----- my key ----";
    tc.TrackEvent("myEvent");
    // ...

```

Aby uzyskać nowy klucz [utworzyć nowy zasób w portalu usługi Application Insights][new].



## <a name="applicationid-provider"></a>Identyfikator aplikacji dostawcy

_Dostępne od wersji v2.6.0_

Ten dostawca ma na celu wyszukania identyfikator aplikacji na podstawie klucza instrumentacji. Identyfikator aplikacji jest włączona RequestTelemetry DependencyTelemetry oraz używane do określenia korelacji w portalu.

Ten element jest dostępny przez ustawienie `TelemetryConfiguration.ApplicationIdProvider` w kodzie, lub w pliku konfiguracyjnym.

### <a name="interface-iapplicationidprovider"></a>Interfejs: IApplicationIdProvider

```csharp
public interface IApplicationIdProvider
{
    bool TryGetApplicationId(string instrumentationKey, out string applicationId);
}
```


Firma Microsoft udostępnia dwa implementacje w [Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights) zestawu sdk: `ApplicationInsightsApplicationIdProvider` i `DictionaryApplicationIdProvider`.

### <a name="applicationinsightsapplicationidprovider"></a>ApplicationInsightsApplicationIdProvider

Jest to otokę interfejsach Api profilu. Będzie ona ograniczenie przepustowości żądań i wyniki pamięci podręcznej.

Ten dostawca został dodany do pliku konfiguracji po zainstalowaniu albo [Microsoft.ApplicationInsights.DependencyCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector) lub [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/)

Ta klasa ma właściwość Opcjonalna `ProfileQueryEndpoint`.
Domyślnie ta jest równa `https://dc.services.visualstudio.com/api/profiles/{0}/appId`.
Jeśli musisz skonfigurować serwer proxy dla tej konfiguracji, zalecamy pośredniczenie adres podstawowy i włączając "/ api/profile / {0} / appId". Należy pamiętać, że "{0}" jest zastępowana w czasie wykonywania na żądanie klucza instrumentacji.

#### <a name="example-configuration-via-applicationinsightsconfig"></a>Przykładowa konfiguracja za pomocą ApplicationInsights.config:
```xml
<ApplicationInsights>
    ...
    <ApplicationIdProvider Type="Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId.ApplicationInsightsApplicationIdProvider, Microsoft.ApplicationInsights">
        <ProfileQueryEndpoint>https://dc.services.visualstudio.com/api/profiles/{0}/appId</ProfileQueryEndpoint>
    </ApplicationIdProvider>
    ...
</ApplicationInsights>
```

#### <a name="example-configuration-via-code"></a>Przykładowa konfiguracja za pomocą kodu:
```csharp
TelemetryConfiguration.Active.ApplicationIdProvider = new ApplicationInsightsApplicationIdProvider();
```

### <a name="dictionaryapplicationidprovider"></a>DictionaryApplicationIdProvider

Jest to statyczne dostawcy, który będzie zależeć skonfigurowany klucz Instrumentacji / pary identyfikator aplikacji.

Ta klasa ma właściwość `Defined` czyli słownik < string, string > klucza Instrumentacji z parami identyfikator aplikacji.

Ta klasa ma właściwość Opcjonalna `Next` które mogą służyć do konfigurowania innego dostawcy do użycia podczas klucza Instrumentacji jest żądane, który nie istnieje w konfiguracji.

#### <a name="example-configuration-via-applicationinsightsconfig"></a>Przykładowa konfiguracja za pomocą ApplicationInsights.config:
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

#### <a name="example-configuration-via-code"></a>Przykładowa konfiguracja za pomocą kodu:
```csharp
TelemetryConfiguration.Active.ApplicationIdProvider = new DictionaryApplicationIdProvider{
 Defined = new Dictionary<string, string>
    {
        {"InstrumentationKey_1", "ApplicationId_1"},
        {"InstrumentationKey_2", "ApplicationId_2"}
    }
};
```




## <a name="next-steps"></a>Kolejne kroki
[Dowiedz się więcej o interfejsie API][api].

<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[client]: app-insights-javascript.md
[diagnostic]: app-insights-diagnostic-search.md
[exceptions]: app-insights-asp-net-exceptions.md
[netlogs]: app-insights-asp-net-trace-logs.md
[new]: app-insights-create-new-resource.md
[redfield]: app-insights-monitor-performance-live-website-now.md
[start]: app-insights-overview.md
