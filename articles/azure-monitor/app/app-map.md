---
title: Mapa aplikacji w usłudze Azure Application Insights | Dokumenty firmy Microsoft
description: Monitorowanie złożonych topologii aplikacji za pomocą mapy aplikacji
ms.topic: conceptual
ms.date: 03/15/2019
ms.reviewer: sdash
ms.openlocfilehash: dce2fdbe7e0c390309be38d2ebab4c73dbb4ed2e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77666279"
---
# <a name="application-map-triage-distributed-applications"></a>Mapa aplikacji: Aplikacje rozproszone triage

Mapa aplikacji pomaga wykryć wąskie gardła wydajności lub punkty aktywne awarie we wszystkich składnikach aplikacji rozproszonej. Każdy węzeł na mapie reprezentuje składnik aplikacji lub jego zależności; i ma wskaźnik KPI kondycji i alerty stanu. Można kliknąć z dowolnego składnika do bardziej szczegółowej diagnostyki, takich jak zdarzenia usługi Application Insights. Jeśli aplikacja korzysta z usług platformy Azure, można również kliknąć do diagnostyki platformy Azure, takich jak zalecenia klasyfikatora bazy danych SQL.

## <a name="what-is-a-component"></a>Co to jest składnik?

Składniki są niezależnie wdrażalne części aplikacji rozproszonych/mikrousług. Deweloperzy i zespoły operacyjne mają wgląd na poziomie kodu lub dostęp do danych telemetrycznych generowanych przez te składniki aplikacji. 

* Składniki różnią się od "obserwowanych" zależności zewnętrznych, takich jak SQL, EventHub itp., do których twój zespół/organizacja może nie mieć dostępu (kod lub dane telemetryczne).
* Składniki są uruchamiane na dowolnej liczbie wystąpień serwera/roli/kontenera.
* Składniki mogą być oddzielne klucze instrumentacji usługi Application Insights (nawet jeśli subskrypcje są różne) lub różne role raportowania do jednego klucza instrumentacji usługi Application Insights. Środowisko mapy podglądu pokazuje składniki niezależnie od tego, jak są one skonfigurowane.

## <a name="composite-application-map"></a>Złożona mapa aplikacji

Można zobaczyć pełną topologię aplikacji na wielu poziomach powiązanych składników aplikacji. Składniki mogą być różne zasoby usługi Application Insights lub różne role w jednym zasobie. Mapa aplikacji wyszukuje składniki, wykonując wywołania zależności HTTP między serwerami z zainstalowanym zestawem SDK usługi Application Insights. 

To doświadczenie zaczyna się od stopniowego odnajdywania składników. Podczas pierwszego ładowania mapy aplikacji, zestaw zapytań jest wyzwalany, aby odkryć składniki związane z tym składnikiem. Przycisk w lewym górnym rogu zostanie zaktualizowany z liczbą składników w aplikacji, gdy zostaną wykryte. 

Po kliknięciu przycisku "Aktualizuj komponenty mapy" mapa jest odświeżana ze wszystkimi komponentami wykrytymi do tego momentu. W zależności od złożoności aplikacji może to potrwać minutę, aby załadować.

Jeśli wszystkie składniki są rolami w ramach jednego zasobu usługi Application Insights, ten krok odnajdywania nie jest wymagany. Początkowe obciążenie dla takiej aplikacji będzie miało wszystkie jego składniki.

![Mapa aplikacji Zrzut z ekranu](media/app-map/app-map-001.png)

Jednym z kluczowych celów tego doświadczenia jest możliwość wizualizacji złożonych topologii za pomocą setek komponentów.

Kliknij dowolny składnik, aby zobaczyć powiązane szczegółowe informacje i przejdź do wydajności i wydajności triage środowiska dla tego składnika.

![Okno wysuwane](media/app-map/application-map-002.png)

### <a name="investigate-failures"></a>Badanie błędów

Wybierz **opcję zbadaj błędy** uruchamiania okienka błędów.

![Zrzut ekranu przedstawiający przycisk Zbadaj błędy](media/app-map/investigate-failures.png)

![Zrzut ekranu przedstawiający błędy](media/app-map/failures.png)

### <a name="investigate-performance"></a>Badanie wydajności

Aby rozwiązać problemy z wydajnością, wybierz **opcję zbadaj wydajność**.

![Zrzut ekranu przedstawiający przycisk zbadaj wydajność](media/app-map/investigate-performance.png)

![Zrzut ekranu przedstawiający wydajność](media/app-map/performance.png)

### <a name="go-to-details"></a>Przejdź do szczegółów

Wybierz **przejdź do szczegółów,** aby eksplorować kompleksowe środowisko transakcji, które mogą oferować widoki w dół do poziomu stosu wywołań.

![Zrzut ekranu przedstawiający przycisk przejdź do szczegółów](media/app-map/go-to-details.png)

![Zrzut ekranu przedstawiający szczegóły transakcji typu end-to-end](media/app-map/end-to-end-transaction.png)

### <a name="view-logs-analytics"></a>Wyświetlanie dzienników (Analytics)

Aby zbadać i zbadać dane aplikacji, kliknij **widok w dziennikach (Analytics)**.

![Zrzut ekranu przedstawiający widok w przycisku analizy](media/app-map/view-logs.png)

![Zrzut ekranu przedstawiający środowisko analityczne. Wykres liniowy podsumowujący średni czas trwania odpowiedzi żądania w ciągu ostatnich 12 godzin.](media/app-map/log-analytics.png)

### <a name="alerts"></a>Alerty

Aby wyświetlić aktywne alerty i podstawowe reguły, które powodują wyzwolenie alertów, wybierz **alerty**.

![Przycisk Zrzut ekranu przedstawiający alerty](media/app-map/alerts.png)

![Zrzut ekranu przedstawiający doświadczenie analityczne](media/app-map/alerts-view.png)

## <a name="set-cloud-role-name"></a>Ustawianie nazwy roli w chmurze

Mapa aplikacji używa właściwości **nazwa roli chmury** do identyfikowania składników na mapie. SDK usługi Application Insights automatycznie dodaje właściwość nazwy roli chmury do danych telemetrycznych emitowanych przez składniki. Na przykład SDK doda nazwę witryny sieci web lub nazwę roli usługi do właściwości nazwy roli w chmurze. Istnieją jednak przypadki, w których można zastąpić wartość domyślną. Aby zastąpić nazwę roli w chmurze i zmienić to, co zostanie wyświetlone na mapie aplikacji:

### <a name="netnet-core"></a>Rdzeń .NET/.NET

**Napisz niestandardowy telemetryInitializer jak poniżej.**

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.Extensibility;

namespace CustomInitializer.Telemetry
{
    public class MyTelemetryInitializer : ITelemetryInitializer
    {
        public void Initialize(ITelemetry telemetry)
        {
            if (string.IsNullOrEmpty(telemetry.Context.Cloud.RoleName))
            {
                //set custom role name here
                telemetry.Context.Cloud.RoleName = "Custom RoleName";
                telemetry.Context.Cloud.RoleInstance = "Custom RoleInstance";
            }
        }
    }
}
```

**ASP.NET aplikacji: Ładowanie inicjatora do aktywnej konfiguracji telemetryczności**

W aplikacjiInsights.config :

```xml
    <ApplicationInsights>
      <TelemetryInitializers>
        <!-- Fully qualified type name, assembly name: -->
        <Add Type="CustomInitializer.Telemetry.MyTelemetryInitializer, CustomInitializer"/>
        ...
      </TelemetryInitializers>
    </ApplicationInsights>
```

Alternatywną metodą ASP.NET aplikacji sieci Web jest tworzenie wystąpienia inicjatora w kodzie, na przykład w Global.aspx.cs:

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;

    protected void Application_Start()
    {
        // ...
        TelemetryConfiguration.Active.TelemetryInitializers.Add(new MyTelemetryInitializer());
    }
```

> [!NOTE]
> Dodawanie inicjatora `ApplicationInsights.config` przy `TelemetryConfiguration.Active` użyciu lub przy użyciu jest nieprawidłowe dla aplikacji ASP.NET Core. 

**aplikacje ASP.NET Core: Ładowanie inicjatora do konfiguracji telemetryczności**

Dla [aplikacji ASP.NET Core](asp-net-core.md#adding-telemetryinitializers) dodawanie `TelemetryInitializer` nowego odbywa się przez dodanie go do kontenera iniekcji zależności, jak pokazano poniżej. Odbywa się `ConfigureServices` to w `Startup.cs` metodzie swojej klasy.

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;
 public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<ITelemetryInitializer, MyTelemetryInitializer>();
}
```

### <a name="nodejs"></a>Node.js

```javascript
var appInsights = require("applicationinsights");
appInsights.setup('INSTRUMENTATION_KEY').start();
appInsights.defaultClient.context.tags["ai.cloud.role"] = "your role name";
appInsights.defaultClient.context.tags["ai.cloud.roleInstance"] = "your role instance";
```

### <a name="alternate-method-for-nodejs"></a>Alternatywna metoda dla node.js

```javascript
var appInsights = require("applicationinsights");
appInsights.setup('INSTRUMENTATION_KEY').start();

appInsights.defaultClient.addTelemetryProcessor(envelope => {
    envelope.tags["ai.cloud.role"] = "your role name";
    envelope.tags["ai.cloud.roleInstance"] = "your role instance"
});
```

### <a name="java"></a>Java

Począwszy od aplikacji Application Insights Java SDK 2.5.0, `<RoleName>` można `ApplicationInsights.xml` określić nazwę roli w chmurze, dodając do pliku, np.

```XML
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings" schemaVersion="2014-05-30">
   <InstrumentationKey>** Your instrumentation key **</InstrumentationKey>
   <RoleName>** Your role name **</RoleName>
   ...
</ApplicationInsights>
```

Jeśli używasz Spring Boot z rozrusznikiem spring boot aplikacji Insights, jedyną wymaganą zmianą jest ustawienie nazwy niestandardowej aplikacji w pliku application.properties.

`spring.application.name=<name-of-app>`

Rozrusznik rozruchu sprężyny automatycznie przypisze nazwę roli w chmurze do wartości wprowadzonej dla właściwości spring.application.name.

### <a name="clientbrowser-side-javascript"></a>JavaScript po stronie klienta/przeglądarki

```javascript
appInsights.queue.push(() => {
appInsights.addTelemetryInitializer((envelope) => {
  envelope.tags["ai.cloud.role"] = "your role name";
  envelope.tags["ai.cloud.roleInstance"] = "your role instance";
});
});
```

### <a name="understanding-cloud-role-name-within-the-context-of-the-application-map"></a>Opis nazwy roli chmury w kontekście mapy aplikacji

Jeśli chodzi o sposób myślenia o **nazwie roli w chmurze,** pomocne może być przyjrzenie się mapie aplikacji, która ma wiele nazw ról w chmurze:

![Mapa aplikacji Zrzut z ekranu](media/app-map/cloud-rolename.png)

W mapie aplikacji powyżej każdej z nazw w zielonych polach są wartości nazwy roli chmury dla różnych aspektów tej konkretnej aplikacji rozproszonej. Więc dla tej aplikacji jego `Authentication` `acmefrontend`role `Inventory Management`składają `Payment Processing Worker Role`się z: , , , a . 

W przypadku tej aplikacji każda z tych nazw ról w chmurze reprezentuje również inny unikatowy zasób usługi Application Insights z własnymi kluczami instrumentacji. Ponieważ właściciel tej aplikacji ma dostęp do każdego z tych czterech różnych zasobów usługi Application Insights, Mapa aplikacji jest w stanie zszyć mapę podstawowych relacji.

W przypadku [oficjalnych definicji:](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/39a5ef23d834777eefdd72149de705a016eb06b0/Schema/PublicSchema/ContextTagKeys.bond#L93)

```
   [Description("Name of the role the application is a part of. Maps directly to the role name in azure.")]
    [MaxStringLength("256")]
    705: string      CloudRole = "ai.cloud.role";
    
    [Description("Name of the instance where the application is running. Computer name for on-premises, instance name for Azure.")]
    [MaxStringLength("256")]
    715: string      CloudRoleInstance = "ai.cloud.roleInstance";
```

Alternatywnie **wystąpienie roli w chmurze** może być pomocne w scenariuszach, w których **nazwa roli w chmurze** informuje, że problem znajduje się gdzieś w witrynie frontonu sieci Web, ale możesz uruchamiać fronton sieci Web na wielu serwerach z równoważeniem obciążenia, dzięki czemu można drążyć w warstwie głębiej za pośrednictwem zapytań Kusto i wiedząc, czy problem ma wpływ na wszystkie serwery/wystąpienia frontonu sieci Web lub tylko jeden może być niezwykle ważny.

Scenariusz, w którym można zastąpić wartość wystąpienia roli w chmurze może być, jeśli aplikacja jest uruchomiona w środowisku konteneryzowanym, gdzie po prostu wiedząc, że pojedynczy serwer może nie być wystarczająca ilość informacji, aby zlokalizować dany problem.

Aby uzyskać więcej informacji na temat zastępowania właściwości nazwy roli chmury za pomocą inicjatorów telemetrii, zobacz [Dodawanie właściwości: ITelemetryInitializer](api-filtering-sampling.md#addmodify-properties-itelemetryinitializer).

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli masz problemy z uzyskaniem mapy aplikacji do pracy zgodnie z oczekiwaniami, spróbuj wykonać następujące kroki:

### <a name="general"></a>Ogólne

1. Upewnij się, że używasz oficjalnie obsługiwanego zestawu SDK. Nieobsługiwane zestawy SDK/zestawy SDK społeczności mogą nie obsługiwać korelacji.

    Zobacz ten [artykuł,](https://docs.microsoft.com/azure/application-insights/app-insights-platforms) aby uzyskać listę obsługiwanych sdków.

2. Uaktualnij wszystkie składniki do najnowszej wersji SDK.

3. Jeśli używasz usługi Azure Functions z c#, uaktualnij do [funkcji V2](https://docs.microsoft.com/azure/azure-functions/functions-versions).

4. Potwierdź, że [nazwa roli w chmurze](#set-cloud-role-name) jest poprawnie skonfigurowana.

5. Jeśli brakuje zależności, upewnij się, że znajduje się ona na liście [automatycznie zebranych zależności](https://docs.microsoft.com/azure/application-insights/auto-collect-dependencies). W przeciwnym razie możesz w dalszym ciągu śledzić ją ręcznie przy użyciu [wywołania zależności śledzenia](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackdependency).

### <a name="too-many-nodes-on-the-map"></a>Zbyt wiele węzłów na mapie

Mapa aplikacji konstruuje węzeł aplikacji dla każdej unikatowej nazwy roli chmury obecnej w danych telemetrycznych żądania i węzła zależności dla każdej unikatowej kombinacji typu, obiektu docelowego i nazwy roli w chmurze w telemetrii zależności. Jeśli w danych telemetrycznych znajduje się więcej niż 10 000 węzłów, mapa aplikacji nie będzie mogła pobrać wszystkich węzłów i łączy, więc mapa będzie niekompletna. W takim przypadku podczas przeglądania mapy pojawi się komunikat ostrzegawczy.

Ponadto mapa aplikacji obsługuje tylko do 1000 oddzielnych węzłów rozgrupowanych renderowanych jednocześnie. Mapa aplikacji zmniejsza złożoność wizualną, grupując zależności razem, które mają ten sam typ i wywołujących, ale jeśli dane telemetryczne mają zbyt wiele unikatowych nazw ról w chmurze lub zbyt wiele typów zależności, to grupowanie będzie niewystarczające, a mapa nie będzie w stanie Renderowania.

Aby rozwiązać ten problem, musisz zmienić instrumentację, aby poprawnie ustawić nazwę roli w chmurze, typ zależności i pola docelowe zależności.

* Obiekt docelowy zależności powinien reprezentować logiczną nazwę zależności. W wielu przypadkach jest odpowiednikiem nazwy serwera lub zasobu zależności. Na przykład w przypadku zależności HTTP jest ustawiona na nazwa hosta. Nie powinien zawierać unikatowych identyfikatorów lub parametrów, które zmieniają się z jednego żądania na inne.

* Typ zależności powinien reprezentować typ logiczny zależności. Na przykład HTTP, SQL lub Azure Blob są typowe typy zależności. Nie powinien zawierać unikatowych identyfikatorów.

* Cel nazwy roli w chmurze jest opisany w [powyższej sekcji](https://docs.microsoft.com/azure/azure-monitor/app/app-map#set-cloud-role-name).

## <a name="portal-feedback"></a>Opinie portalu

Aby przekazać opinię, użyj opcji opinii.

![Zdjęcie MapLink-1](./media/app-map/14-updated.png)

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej o tym, jak działa korelacja w usłudze Application Insights, zapoznaj się z [artykułem korelacji danych telemetrycznych](correlation.md).
* [Środowisko diagnostyczne transakcji end-to-end](transaction-diagnostics.md) koreluje dane telemetryczne po stronie serwera z wszystkich składników monitorowanych usługi Application Insights w jednym widoku.
* Zaawansowane scenariusze korelacji w ASP.NET Core i ASP.NET zapoznaj się z artykułem [operacji niestandardowych śledzenia.](custom-operations-tracking.md)
