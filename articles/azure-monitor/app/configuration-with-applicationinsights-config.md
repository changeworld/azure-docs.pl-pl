---
title: Plik ApplicationInsights.config dokumentacja — Azure | Dokumentacja firmy Microsoft
description: Włącz lub wyłącz modułów gromadzenia danych i Dodawanie liczników wydajności oraz innych parametrów.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 6e397752-c086-46e9-8648-a1196e8078c2
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/19/2018
ms.reviewer: olegan
ms.author: mbullwin
ms.openlocfilehash: 1a5b6d435dcc82b59c30302f9cd711975864594c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60901914"
---
# <a name="configuring-the-application-insights-sdk-with-applicationinsightsconfig-or-xml"></a>Konfigurowanie zestawu SDK usługi Application Insights za pomocą pliku ApplicationInsights.config lub xml
Zestaw SDK .NET usługi Application Insights składa się kilka pakietów NuGet. [Pakiet podstawowego](https://www.nuget.org/packages/Microsoft.ApplicationInsights) zapewnia interfejs API do wysyłania danych telemetrycznych do usługi Application Insights. [Dodatkowe pakiety](https://www.nuget.org/packages?q=Microsoft.ApplicationInsights) Podaj dane telemetryczne *modułów* i *inicjatory* dla automatycznego śledzenia danych telemetrycznych z aplikacji i jej kontekstu. Dostosowując plik konfiguracji, można włączyć lub wyłączyć inicjatory i moduły danych telemetrycznych i ustawić parametry dla niektórych z nich.

Plik konfiguracji ma nazwę `ApplicationInsights.config` lub `ApplicationInsights.xml`, w zależności od typu aplikacji. Jest automatycznie dodawany do projektu po użytkownik [zainstalować większość wersji zestawu SDK][start]. Jest także dodawane do aplikacji sieci web przez [Monitora stanu na serwerze IIS][redfield], lub po wybraniu usługi Application Insights [rozszerzenie dla witryny internetowej platformy Azure lub maszynie Wirtualnej](azure-web-apps.md).

Nie ma pliku równoważne do kontroli [zestawu SDK na stronie sieci web][client].

W tym dokumencie opisano sekcje, które widać w konfiguracji, plików, sposobie ich kontrolować składników zestawu SDK, i pakiety NuGet ładowania tych składników.

> [!NOTE]
> Plik ApplicationInsights.config i .xml instrukcje nie dotyczą platformy .NET Core SDK. Dla zmian w aplikacji .NET Core zwykle używany plik appsettings.json. Na przykład można znaleźć w [dokumentacji rozszerzenia Snapshot Debugger.](https://docs.microsoft.com/azure/application-insights/app-insights-snapshot-debugger)

## <a name="telemetry-modules-aspnet"></a>Moduły danych telemetrycznych (ASP.NET)
Każdy moduł danych telemetrycznych zbiera określonego typu danych i wysyła te dane przy użyciu podstawowego interfejsu API. Moduły są instalowane przez różne pakiety NuGet, które również dodać wymagane wiersze do pliku Config.

Jest węzłem w pliku konfiguracji dla każdego modułu. Aby wyłączyć moduł, usunąć węzła lub komentarz dotyczący działanie.

### <a name="dependency-tracking"></a>Śledzenie zależności
[Śledzenia zależności](../../azure-monitor/app/asp-net-dependencies.md) gromadzi dane telemetryczne o wywołaniach aplikacji sprawia, że do baz danych i usługami zewnętrznymi i baz danych. Aby zezwolić na ten moduł do pracy na serwerze usług IIS, musisz [Zainstaluj Monitor stanu][redfield]. Aby użyć go w usłudze Azure web apps lub maszyn wirtualnych, [wybierz rozszerzenie usługi Application Insights](azure-web-apps.md).

Można także napisać swoje własne zależności śledzenia kodu za pomocą [TrackDependency API](../../azure-monitor/app/api-custom-events-metrics.md#trackdependency).

* `Microsoft.ApplicationInsights.DependencyCollector.DependencyTrackingTelemetryModule`
* [Microsoft.ApplicationInsights.DependencyCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector) NuGet package.

### <a name="performance-collector"></a>Wydajność modułu zbierającego
[Zbiera dane liczników wydajności systemu](../../azure-monitor/app/performance-counters.md) takie jak procesor CPU, pamięci i sieci obciążenia z instalacji usług IIS. Możesz określić, które liczniki, aby zebrać, łącznie z liczników wydajności, które zostały skonfigurowane samodzielnie.

* `Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule`
* [Microsoft.ApplicationInsights.PerfCounterCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector) NuGet package.

### <a name="application-insights-diagnostics-telemetry"></a>Telemetria usługi Application Insights diagnostyki
`DiagnosticsTelemetryModule` Zgłasza błędy w sam kod Instrumentacji usługi Application Insights. Na przykład jeśli kod nie może uzyskiwać dostęp do liczników wydajności lub `ITelemetryInitializer` zgłasza wyjątek. Telemetria śledzenia śledzone przez ten moduł zostanie wyświetlona w [wyszukiwaniu diagnostycznym][diagnostic]. Wysyła dane diagnostyczne do dc.services.vsallin.net.

* `Microsoft.ApplicationInsights.Extensibility.Implementation.Tracing.DiagnosticsTelemetryModule`
* [Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights) pakietu NuGet. Jeśli zainstalowano tylko ten pakiet, plik ApplicationInsights.config nie jest automatycznie tworzony.

### <a name="developer-mode"></a>Tryb dewelopera
`DeveloperModeWithDebuggerAttachedTelemetryModule` Wymusza usługi Application Insights `TelemetryChannel` do wysyłania danych natychmiast, telemetrii jednego elementu w czasie, gdy debuger jest dołączony do procesu aplikacji. Zmniejsza to ilość czasu między odnalezieniem, kiedy aplikacji śledzi telemetrii i kiedy pojawia się w portalu usługi Application Insights. Powoduje znaczne obciążenie przepustowości procesora CPU i sieci.

* `Microsoft.ApplicationInsights.WindowsServer.DeveloperModeWithDebuggerAttachedTelemetryModule`
* [Application Insights w systemie Windows Server](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/) pakietu NuGet

### <a name="web-request-tracking"></a>Żądania sieci Web śledzenia
Raporty [kod odpowiedzi czasu i wynik](../../azure-monitor/app/asp-net.md) żądań HTTP.

* `Microsoft.ApplicationInsights.Web.RequestTrackingTelemetryModule`
* [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) NuGet package

### <a name="exception-tracking"></a>Śledzenie wyjątków
`ExceptionTrackingTelemetryModule` śledzi nieobsługiwanych wyjątków w aplikacji sieci web. Zobacz [błędy i wyjątki][exceptions].

* `Microsoft.ApplicationInsights.Web.ExceptionTrackingTelemetryModule`
* [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) NuGet package
* `Microsoft.ApplicationInsights.WindowsServer.UnobservedExceptionTelemetryModule` -śledzi [niezauważalne wyjątki zadań](https://blogs.msdn.com/b/pfxteam/archive/2011/09/28/task-exception-handling-in-net-4-5.aspx).
* `Microsoft.ApplicationInsights.WindowsServer.UnhandledExceptionTelemetryModule` -śledzi nieobsługiwanych wyjątków dla ról procesów roboczych, usług systemu windows i aplikacji konsoli.
* [Application Insights w systemie Windows Server](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/) pakietu NuGet.

### <a name="eventsource-tracking"></a>Źródła zdarzeń śledzenia
`EventSourceTelemetryModule` Umożliwia skonfigurowanie źródła zdarzeń zdarzenia do wysłania do usługi Application Insights jako ślady. Aby uzyskać informacji na temat śledzenia zdarzeń EventSource, zobacz [przy użyciu zdarzeń EventSource](../../azure-monitor/app/asp-net-trace-logs.md#using-eventsource-events).

* `Microsoft.ApplicationInsights.EventSourceListener.EventSourceTelemetryModule`
* [Microsoft.ApplicationInsights.EventSourceListener](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener) 

### <a name="etw-event-tracking"></a>Śledzenie zdarzeń ETW.
`EtwCollectorTelemetryModule` Umożliwia skonfigurowanie zdarzenia z dostawców ETW do wysłania do usługi Application Insights jako ślady. Aby uzyskać informacji na temat śledzenia zdarzeń ETW, zobacz [zdarzenia ETW za pomocą](../../azure-monitor/app/asp-net-trace-logs.md#using-etw-events).

* `Microsoft.ApplicationInsights.EtwCollector.EtwCollectorTelemetryModule`
* [Microsoft.ApplicationInsights.EtwCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EtwCollector) 

### <a name="microsoftapplicationinsights"></a>Microsoft.ApplicationInsights
Pakiet Microsoft.ApplicationInsights zawiera [core API](https://msdn.microsoft.com/library/mt420197.aspx) zestawu SDK. Użyj innych modułów danych telemetrycznych i można także [użyj go do zdefiniowania własnej telemetrii](../../azure-monitor/app/api-custom-events-metrics.md).

* Nie wpisu w pliku ApplicationInsights.config.
* [Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights) pakietu NuGet. Po zainstalowaniu tylko tym NuGet, jest generowany nie pliku Config.

## <a name="telemetry-channel"></a>Kanał danych telemetrycznych
Kanał danych telemetrycznych zarządza buforowanie i przesyłanie danych telemetrycznych do usługi Application Insights.

* `Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel` jest domyślnym kanale usługi. Buforuje dane w pamięci.
* `Microsoft.ApplicationInsights.PersistenceChannel` stanowi alternatywę dla aplikacji konsoli. Ono zapisane unflushed danych w magazynie trwałym, aplikacja zostanie zamknięte w dół, gdy będzie wysyłać je, gdy aplikacja jest uruchamiana ponownie.

## <a name="telemetry-initializers-aspnet"></a>Telemetry Initializers (ASP.NET)
Inicjatory telemetrii ustawić właściwości kontekstu, które są wysyłane wraz z każdym elementem telemetrii.

Możesz [napisać własne inicjatory](../../azure-monitor/app/api-filtering-sampling.md#add-properties) można ustawić właściwości kontekstu.

Standardowa inicjatory jest ustawiony przez pakiety Web lub WindowsServer NuGet:

* `AccountIdTelemetryInitializer` Ustawia właściwość AccountId.
* `AuthenticatedUserIdTelemetryInitializer` Ustawia właściwość AuthenticatedUserId według stawki ustalonej przez zestaw SDK języka JavaScript.
* `AzureRoleEnvironmentTelemetryInitializer` aktualizacje `RoleName` i `RoleInstance` właściwości `Device` kontekst dla wszystkich elementów danych telemetrycznych informacjami wyodrębnionymi z środowiska uruchomieniowego platformy Azure.
* `BuildInfoConfigComponentVersionTelemetryInitializer` aktualizacje `Version` właściwość `Component` kontekst dla wszystkich elementów danych telemetrycznych z wartością wyodrębnione z `BuildInfo.config` plik utworzony przez program MS Build.
* `ClientIpHeaderTelemetryInitializer` aktualizacje `Ip` właściwość `Location` na podstawie kontekstu wszystkie elementy danych telemetrycznych `X-Forwarded-For` nagłówka HTTP żądania.
* `DeviceTelemetryInitializer` aktualizuje następujące właściwości `Device` kontekst dla wszystkich elementów telemetrii.
  * `Type` jest ustawiona na "Komputer"
  * `Id` jest ustawiona na nazwę domeny komputera, na którym działa aplikacja sieci web.
  * `OemName` ma ustawioną wartość wyodrębnione z `Win32_ComputerSystem.Manufacturer` pola przy użyciu usługi WMI.
  * `Model` ma ustawioną wartość wyodrębnione z `Win32_ComputerSystem.Model` pola przy użyciu usługi WMI.
  * `NetworkType` ma ustawioną wartość wyodrębnione z `NetworkInterface`.
  * `Language` jest ustawiona na nazwę `CurrentCulture`.
* `DomainNameRoleInstanceTelemetryInitializer` aktualizacje `RoleInstance` właściwość `Device` kontekst dla wszystkich elementów danych telemetrycznych z nazwą domeny komputera, na którym działa aplikacja sieci web.
* `OperationNameTelemetryInitializer` aktualizacje `Name` właściwość `RequestTelemetry` i `Name` właściwość `Operation` kontekstu wszystkie elementy dane telemetryczne oparte na metodę HTTP, a także nazwy programu ASP.NET MVC kontroler i akcję wywoływaną, aby przetworzyć żądanie.
* `OperationIdTelemetryInitializer` lub `OperationCorrelationTelemetryInitializer` aktualizacje `Operation.Id` właściwości kontekstu wszystkich elementów danych telemetrycznych śledzone podczas obsługi żądania z automatycznie wygenerowanego `RequestTelemetry.Id`.
* `SessionTelemetryInitializer` aktualizacje `Id` właściwość `Session` kontekst dla wszystkich elementów danych telemetrycznych z wartością wyodrębnione z `ai_session` wygenerowane przez kod Instrumentacji ApplicationInsights JavaScript w przeglądarce użytkownika w pliku cookie.
* `SyntheticTelemetryInitializer` lub `SyntheticUserAgentTelemetryInitializer` aktualizacje `User`, `Session`, i `Operation` kontekstów właściwości wszystkich elementów danych telemetrycznych śledzone podczas obsługi żądania z syntetycznego źródła, takich jak dostępności testu lub wyszukiwanie bot aparatu. Domyślnie [Eksploratora metryk](../../azure-monitor/app/metrics-explorer.md) syntetyczne dane telemetryczne nie są wyświetlane.

    `<Filters>` Ustaw identyfikujące właściwości żądania.
* `UserTelemetryInitializer` aktualizacje `Id` i `AcquisitionDate` właściwości `User` kontekst dla wszystkich elementów danych telemetrycznych z wartościami wyodrębnione z `ai_user` wygenerowane przez kod Instrumentacji Application Insights JavaScript, które są uruchomione w użytkownika w pliku cookie Przeglądarka.
* `WebTestTelemetryInitializer` Ustawia identyfikator użytkownika, identyfikator sesji i właściwości syntetycznego źródła dla tego pochodzą z żądań HTTP [testy dostępności](../../azure-monitor/app/monitor-web-app-availability.md).
  `<Filters>` Ustaw identyfikujące właściwości żądania.

Dla aplikacji .NET działających w usłudze Service Fabric, możesz dołączyć `Microsoft.ApplicationInsights.ServiceFabric` pakietu NuGet. Ten pakiet zawiera `FabricTelemetryInitializer`, która dodaje właściwości usługi Service Fabric do elementów telemetrii. Aby uzyskać więcej informacji, zobacz [strony GitHub](https://github.com/Microsoft/ApplicationInsights-ServiceFabric/blob/master/README.md) informacje o właściwościach dodane przez ten pakiet NuGet.

## <a name="telemetry-processors-aspnet"></a>Dane telemetryczne procesorów (ASP.NET)
Procesory danych telemetrycznych można filtrować i zmodyfikować każdego elementu telemetrii, po prostu przed wysłaniem go z zestawu SDK do portalu.

Możesz [zapisu procesorów telemetrii](../../azure-monitor/app/api-filtering-sampling.md#filtering).

#### <a name="adaptive-sampling-telemetry-processor-from-200-beta3"></a>Próbkowanie adaptacyjne telemetrii procesor (2.0.0-beta3)
Ta opcja jest domyślnie włączona. Jeśli Twoja aplikacja wysyła dużo danych telemetrycznych, to procesor usuwa część z nich.

```xml

    <TelemetryProcessors>
      <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
        <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
      </Add>
    </TelemetryProcessors>

```

Parametr zawiera element docelowy, który algorytm próbuje osiągnąć. Każde wystąpienie zestawu SDK działa niezależnie, więc jeśli serwer jest w klastrze składającym się z kilku komputerach, rzeczywistą ilość danych telemetrycznych będzie mnożona odpowiednio.

[Dowiedz się więcej na temat próbkowania](../../azure-monitor/app/sampling.md).

#### <a name="fixed-rate-sampling-telemetry-processor-from-200-beta1"></a>Stałej częstotliwość próbkowania danych telemetrycznych procesor (2.0.0-beta1)
Również jest standardem [próbkowania procesora telemetrii](../../azure-monitor/app/api-filtering-sampling.md) (z 2.0.1):

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
Parametry te wpływają na jak przechowywania oraz opróżniania danych telemetrycznych, które są zbierane z zestawu SDK Java.

#### <a name="maxtelemetrybuffercapacity"></a>MaxTelemetryBufferCapacity
Liczba elementów danych telemetrycznych, które mogą być przechowywane w magazynie w pamięci zestawu SDK. Po osiągnięciu tej liczby buforów danych telemetrycznych jest opróżniany — czyli elementy dane telemetryczne są wysyłane do serwera usługi Application Insights.

* Minimalna: 1
* Maks.: 1000
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
Określa, jak często dane są przechowywane w magazynie w pamięci powinna opróżniany (wysłane do usługi Application Insights).

* Minimalna: 1
* Maks.: 300
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
Określa maksymalny rozmiar w Megabajtach, która jest przydzielona do magazynu trwałego na dysku lokalnym. Ten magazyn jest używany dla utrwalanie elementów danych telemetrycznych, które nie może zostać przesłana do punktu końcowego usługi Application Insights. Po spełnieniu rozmiar magazynu zostaną odrzucone nowych elementów telemetrii.

* Minimalna: 1
* Maks.: 100
* Domyślne: 10

```

   <ApplicationInsights>
      ...
      <Channel>
        <MaxTransmissionStorageCapacityInMB>50</MaxTransmissionStorageCapacityInMB>
      </Channel>
      ...
   </ApplicationInsights>
```

#### <a name="local-forwarder"></a>Lokalna usługa przesyłania dalej

[Lokalne usługi przesyłania dalej](opencensus-local-forwarder.md) Agent, który gromadzi informacje o usłudze Application Insights lub [OpenCensus](https://opencensus.io/) dane telemetryczne z różnych zestawów SDK i struktur i kieruje je do usługi Application Insights. Jest w stanie działać w obszarze Windows i Linux. W połączeniu z zestawu SDK Java usługi Application Insights lokalnej usługi przesyłania dalej zapewnia pełną obsługę [metryki na żywo](../../azure-monitor/app/live-stream.md) i próbkowanie adaptacyjne.

```xml
<Channel type="com.microsoft.applicationinsights.channel.concrete.localforwarder.LocalForwarderTelemetryChannel">
<EndpointAddress><!-- put the hostname:port of your LocalForwarder instance here --></EndpointAddress>

<!-- The properties below are optional. The values shown are the defaults for each property -->

<FlushIntervalInSeconds>5</FlushIntervalInSeconds><!-- must be between [1, 500]. values outside the bound will be rounded to nearest bound -->
<MaxTelemetryBufferCapacity>500</MaxTelemetryBufferCapacity><!-- units=number of telemetry items; must be between [1, 1000] -->
</Channel>
```

Jeśli używasz modułu uruchamiającego SpringBoot, Dodaj następujący element do pliku konfiguracji (pliku application.properties):

```yml
azure.application-insights.channel.local-forwarder.endpoint-address=<!--put the hostname:port of your LocalForwarder instance here-->
azure.application-insights.channel.local-forwarder.flush-interval-in-seconds=<!--optional-->
azure.application-insights.channel.local-forwarder.max-telemetry-buffer-capacity=<!--optional-->
```

Wartości domyślne są takie same w przypadku konfiguracji SpringBoot pliku application.properties i plik applicationinsights.xml.

## <a name="instrumentationkey"></a>InstrumentationKey
Określa zasób usługi Application Insights, w którym dane są wyświetlane. Zazwyczaj należy utworzyć osobny zasób z oddzielny klucz dla każdej aplikacji.

Jeśli chcesz ustawić klucz dynamicznie — na przykład, jeśli chcesz wysłać wyniki z aplikacji do różnych zasobów — można pominąć klucz z pliku konfiguracji i ustaw go w kodzie.

Aby ustawić klucz dla wszystkich wystąpień TelemetryClient, moduły standardowe dane telemetryczne, Ustaw klucz w TelemetryConfiguration.Active. Wykonaj następujące czynności w metodzie inicjalizacji, takich jak pliku global.aspx.cs usługi sieci Web platformy ASP.NET:

```csharp

    protected void Application_Start()
    {
      Microsoft.ApplicationInsights.Extensibility.
        TelemetryConfiguration.Active.InstrumentationKey =
          // - for example -
          WebConfigurationManager.AppSettings["ikey"];
      //...
```

Jeśli chcesz wysłać zestaw określonych zdarzeń do innego zasobu, można ustawić klucza dla określonych TelemetryClient:

```csharp

    var tc = new TelemetryClient();
    tc.Context.InstrumentationKey = "----- my key ----";
    tc.TrackEvent("myEvent");
    // ...

```

Aby uzyskać nowy klucz [utworzenia nowego zasobu w portalu Application Insights][new].



## <a name="applicationid-provider"></a>Identyfikator aplikacji dostawcy

_Dostępne począwszy od v2.6.0_

Ten dostawca ma na celu wyszukać identyfikator aplikacji na podstawie klucza instrumentacji. Identyfikator aplikacji jest uwzględniona w RequestTelemetry i DependencyTelemetry i używane do określania korelacji w portalu.

Ta opcja jest dostępna, ustawiając `TelemetryConfiguration.ApplicationIdProvider` w kodzie lub w pliku konfiguracyjnym.

### <a name="interface-iapplicationidprovider"></a>Interfejs: IApplicationIdProvider

```csharp
public interface IApplicationIdProvider
{
    bool TryGetApplicationId(string instrumentationKey, out string applicationId);
}
```


Firma Microsoft oferuje dwie implementacje w [Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights) zestawu sdk: `ApplicationInsightsApplicationIdProvider` i `DictionaryApplicationIdProvider`.

### <a name="applicationinsightsapplicationidprovider"></a>ApplicationInsightsApplicationIdProvider

Jest to otokę nasze interfejsy API, profilu. Będzie ona ograniczać żądania i wyników do pamięci podręcznej.

Ten dostawca jest dodawany do pliku konfiguracji po zainstalowaniu albo [Microsoft.ApplicationInsights.DependencyCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector) lub [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/)

Ta klasa ma właściwość Opcjonalna `ProfileQueryEndpoint`.
Domyślnie jest ono ustawione na `https://dc.services.visualstudio.com/api/profiles/{0}/appId`.
Jeśli musisz skonfigurować serwer proxy dla tej konfiguracji, firma Microsoft zaleca pośredniczenie podstawowy adres i tym "/api/profile/{0}/appId". Należy pamiętać, że "{0}" zostanie zastąpiony w czasie wykonywania żądaniem przy użyciu klucza instrumentacji.

#### <a name="example-configuration-via-applicationinsightsconfig"></a>Przykładową konfigurację za pomocą pliku ApplicationInsights.config:
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

To statyczny dostawcy, który będzie zależny od skonfigurowanego klucza Instrumentacji / par identyfikator aplikacji.

Ta klasa posiada właściwości `Defined`, czyli Dictionary < string, string > klucza Instrumentacji do par identyfikator aplikacji.

Ta klasa ma właściwość Opcjonalna `Next` której można skonfigurować do użycia podczas klucz Instrumentacji jest żądane innego dostawcy, który nie istnieje w konfiguracji.

#### <a name="example-configuration-via-applicationinsightsconfig"></a>Przykładową konfigurację za pomocą pliku ApplicationInsights.config:
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

[api]: ../../azure-monitor/app/api-custom-events-metrics.md
[client]: ../../azure-monitor/app/javascript.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[exceptions]: ../../azure-monitor/app/asp-net-exceptions.md
[netlogs]: ../../azure-monitor/app/asp-net-trace-logs.md
[new]: ../../azure-monitor/app/create-new-resource.md 
[redfield]: ../../azure-monitor/app/monitor-performance-live-website-now.md
[start]: ../../azure-monitor/app/app-insights-overview.md
