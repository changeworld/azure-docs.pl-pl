---
title: Mapa aplikacji w usłudze Azure Application Insights | Dokumentacja firmy Microsoft
description: Monitorowanie aplikacji złożonych topologii z mapy aplikacji
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 3bf37fe9-70d7-4229-98d6-4f624d256c36
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 03/15/2019
ms.reviewer: sdash
ms.author: mbullwin
ms.openlocfilehash: ba4643118c5d90b91c3e51d569e9a628c84159fc
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2019
ms.locfileid: "65780028"
---
# <a name="application-map-triage-distributed-applications"></a>Mapa aplikacji: Klasyfikacja aplikacji rozproszonych

Mapa aplikacji pomaga wąskich gardeł wydajności dodatkowych lub niepowodzenie obszarów nadmiernej aktywności dotyczące wszystkich składników aplikacji rozproszonej. Każdy węzeł na mapie reprezentuje składnik aplikacji lub jej zależności ma kondycji kluczowy wskaźnik wydajności i alerty stanu. Kliknij z dowolnego składnika do bardziej szczegółowe dane diagnostyczne, takie jak zdarzenia usługi Application Insights. Jeśli aplikacja korzysta z usług platformy Azure, możesz również kliknąć za pośrednictwem do diagnostyki platformy Azure, takie jak zalecenia usługi Advisor z bazy danych SQL.

## <a name="what-is-a-component"></a>Co to jest składnikiem?

Składniki są niezależne wdrażanym poszczególnych części aplikacji rozproszonych mikrousług. Zespoły deweloperów i operacyjne mają widoczność na poziomie kodu lub dostęp do telemetrii wygenerowanej przez te składniki aplikacji. 

* Składniki różnią się od "obserwowanych" zależności zewnętrzne, takie jak SQL, itp. Centrum zdarzeń, które zespół/organizacji nie mogą mieć dostęp do (kod lub telemetrii).
* Składniki są uruchomione na dowolną liczbę wystąpień kontenera server/roli.
* Składniki mogą być oddzielnych kluczy Instrumentacji usługi Application Insights (nawet jeśli różnią się subskrypcje) lub różne role, raportowanie do jednego klucza Instrumentacji usługi Application Insights. Środowisko podglądu tak mapy przedstawiono składniki, niezależnie od tego, jak ustawić.

## <a name="composite-application-map"></a>Mapa aplikacji złożonej

Możesz zobaczyć topologia pełnej aplikacji na wielu poziomach składników powiązane aplikacji. Składniki mogą być, różne zasoby usługi Application Insights lub różne role w pojedynczym zasobie. Mapa aplikacji umożliwia znalezienie składników przez następujące HTTP zależności wywołań między serwerami za pomocą zainstalować zestaw Application Insights SDK. 

To środowisko rozpoczyna się od progresywnego odnajdywania składników. Podczas pierwszego ładowania mapy aplikacji, zestawu zapytań jest wyzwalany, aby odnaleźć składników powiązane z tego składnika. Przycisk w lewym górnym rogu zostanie zaktualizowana o liczbę składników w aplikacji, zgodnie z ich odnalezieniu. 

Po kliknięciu przycisku "Aktualizuj składniki mapy", mapy zostaną odświeżone przy użyciu wszystkich składników wykryte do momentu na tym etapie. W zależności od stopnia złożoności aplikacji może to potrwać chwilę, aby załadować.

Jeśli wszystkie składniki są ról w ramach pojedynczego zasobu usługi Application Insights, ten krok odnajdowania nie jest wymagane. Ładowania początkowego dla takich aplikacji będzie miał wszystkie jego składniki.

![Zrzut ekranu z mapy aplikacji](media/app-map/app-map-001.png)

Jednym z celów klucza z tego środowiska jest możliwe do wizualizacji złożonych topologii z setkami składników.

Kliknij dowolny składnik, aby zobaczyć powiązane szczegółowe informacje, a następnie przejdź do wydajności i dokładniejszej klasyfikacji błędów danego składnika.

![Okno wysuwane](media/app-map/application-map-002.png)

### <a name="investigate-failures"></a>Zbadaj błędy

Wybierz **zbadaj błędy** można uruchomić z okienka błędów.

![Zrzut ekranu przedstawiający zbadaj błędy przycisku](media/app-map/investigate-failures.png)

![Zrzut ekranu przedstawiający obsługi błędów](media/app-map/failures.png)

### <a name="investigate-performance"></a>Zbadaj wydajność

Rozwiązywanie problemów z wydajnością, wybierz **Zbadaj wydajność**.

![Zrzut ekranu przedstawiający badanie wydajności przycisku](media/app-map/investigate-performance.png)

![Zrzut ekranu przedstawiający wydajność](media/app-map/performance.png)

### <a name="go-to-details"></a>Przejdź do szczegółów

Wybierz **przejdź do szczegółów** Eksplorowanie środowiska transakcji end-to-end, które może zaoferować widoków do poziomu stosu wywołań.

![Zrzut ekranu przedstawiający przycisk Przejdź do szczegółów](media/app-map/go-to-details.png)

![Zrzut ekranu przedstawiający szczegóły transakcji end-to-end](media/app-map/end-to-end-transaction.png)

### <a name="view-in-analytics"></a>Wyświetl w usłudze Analytics

Kliknij, aby zapytania i danych aplikacji dalsze badanie **Wyświetl w obszarze analiza**.

![Zrzut ekranu przedstawiający widok przycisk Analiza](media/app-map/view-in-analytics.png)

![Zrzut ekranu przedstawiający środowisko analizy](media/app-map/analytics.png)

### <a name="alerts"></a>Alerty

Zaznacz, aby wyświetlić aktywne alerty i podstawowych reguł, które alerty wyzwalane **alerty**.

![Zrzut ekranu przedstawiający przycisk alertów](media/app-map/alerts.png)

![Zrzut ekranu przedstawiający środowisko analizy](media/app-map/alerts-view.png)

## <a name="set-cloud-role-name"></a>Nazwa roli w chmurze zestawu

Mapa aplikacji używa **Nazwa roli w chmurze** właściwość do identyfikacji składniki na mapie. Zestaw SDK usługi Application Insights automatycznie dodaje właściwości nazwy roli chmury do telemetrii wyemitowane przez składniki. Na przykład zestaw SDK doda nazwa witryny sieci web lub nazwa roli usługi do chmury roli nazwy właściwości. Jednakże istnieją przypadki, w których możesz chcieć zastąpić wartość domyślną. Aby zastąpić nazwę roli w chmurze i zmiany, jakie wyświetlona na mapie aplikacji:

### <a name="net"></a>.NET

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

**Ładowanie usługi inicjatora**

In ApplicationInsights.config:

```xml
    <ApplicationInsights>
      <TelemetryInitializers>
        <!-- Fully qualified type name, assembly name: -->
        <Add Type="CustomInitializer.Telemetry.MyTelemetryInitializer, CustomInitializer"/>
        ...
      </TelemetryInitializers>
    </ApplicationInsights>
```

Alternatywna metoda jest utworzenie wystąpienia inicjatora w kodzie, na przykład w pliku Global.aspx.cs:

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;

    protected void Application_Start()
    {
        // ...
        TelemetryConfiguration.Active.TelemetryInitializers.Add(new MyTelemetryInitializer());
    }
```

### <a name="nodejs"></a>Node.js

```javascript
var appInsights = require("applicationinsights");
appInsights.setup('INSTRUMENTATION_KEY').start();
appInsights.defaultClient.context.tags["ai.cloud.role"] = "your role name";
appInsights.defaultClient.context.tags["ai.cloud.roleInstance"] = "your role instance";
```

### <a name="alternate-method-for-nodejs"></a>Alternatywna metoda dla środowiska Node.js

```javascript
var appInsights = require("applicationinsights");
appInsights.setup('INSTRUMENTATION_KEY').start();

appInsights.defaultClient.addTelemetryProcessor(envelope => {
    envelope.tags["ai.cloud.role"] = "your role name";
    envelope.tags["ai.cloud.roleInstance"] = "your role instance"
});
```

### <a name="java"></a>Java

Jeśli używasz aplikacji Spring Boot za pomocą Application Insights platformy Spring Boot starter tylko wymagana zmiana jest można ustawić nazwę niestandardowego dla aplikacji w pliku application.properties pliku.

`spring.application.name=<name-of-app>`

Spring Boot starter automatycznie przypisze Nazwa roli w chmurze na wartość wprowadzona dla właściwości spring.application.name.

Aby uzyskać więcej informacji na temat języka Java korelacji i sposobie konfigurowania roli w chmurze nazwy do wyewidencjonowania aplikacje inne niż SpringBoot to [sekcji](https://docs.microsoft.com/azure/application-insights/application-insights-correlation#role-name) na korelacji.

### <a name="clientbrowser-side-javascript"></a>/ Przeglądarki klienta kodu JavaScript

```javascript
appInsights.queue.push(() => {
appInsights.context.addTelemetryInitializer((envelope) => {
  envelope.tags["ai.cloud.role"] = "your role name";
  envelope.tags["ai.cloud.roleInstance"] = "your role instance";
});
});
```

### <a name="understanding-cloud-role-name-within-the-context-of-the-application-map"></a>Nazwa roli w kontekście mapy aplikacji w chmurze opis

W miarę jak myśleć o **Nazwa roli w chmurze**, może być przydatne do wyszukiwania na mapie aplikacji, który ma wiele nazw ról chmura istnieje:

![Zrzut ekranu z mapy aplikacji](media/app-map/cloud-rolename.png)

Na mapie aplikacji nad każdym z nazwami w polach zielony to chmury roli nazwy wartości dla różnych aspektów korzystania z tej konkretnej aplikacji rozproszonej. Aby dla tej aplikacji jego role składają się z: `Authentication`, `acmefrontend`, `Inventory Management`, `Payment Processing Worker Role`. 

W przypadku tej aplikacji każda z tych nazw ról w chmurze również reprezentuje inny unikatowy zasób usługi Application Insights z własnych kluczy instrumentacji. Ponieważ właściciela tej aplikacji ma dostęp do każdej z tych czterech różnych zasobów usługi Application Insights, mapa aplikacji będzie mógł łączyć mapę relacji podstawowej.

Aby uzyskać [oficjalne definicje](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/39a5ef23d834777eefdd72149de705a016eb06b0/Schema/PublicSchema/ContextTagKeys.bond#L93):

```
   [Description("Name of the role the application is a part of. Maps directly to the role name in azure.")]
    [MaxStringLength("256")]
    705: string      CloudRole = "ai.cloud.role";
    
    [Description("Name of the instance where the application is running. Computer name for on-premises, instance name for Azure.")]
    [MaxStringLength("256")]
    715: string      CloudRoleInstance = "ai.cloud.roleInstance";
```

Alternatywnie **wystąpienie roli w chmurze** mogą być przydatne w scenariuszach gdzie **Nazwa roli w chmurze** informuje, ten problem występuje w sieci web frontonu, ale może być uruchomiony sieci web frontonu w wiele ze zrównoważonym obciążeniem serwerów tak możliwość przejść głębiej za pośrednictwem zapytania Kusto warstwy i, wiedząc, jeśli ten problem ma wpływ na wszystkie wystąpienia sieci web frontonu serwerów/lub jedna z tych metod może być bardzo ważne.

Scenariusz, w którym możesz chcieć zastąpić wartość dla wystąpienia roli w chmurze może być, jeśli Twoja aplikacja działa w środowisku konteneryzowanych gdzie wystarczy znajomość poszczególnych serwerów może nie być wystarczająco dużo informacji, aby zlokalizować danego problemu.

Aby uzyskać więcej informacji o tym, jak zastąpić właściwości nazwy roli chmury z inicjatorami telemetrii, zobacz [dodać właściwości: ITelemetryInitializer](api-filtering-sampling.md#add-properties-itelemetryinitializer).

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli występują problemy z mapy aplikacji, aby działać zgodnie z oczekiwaniami, wykonaj poniższe kroki:

### <a name="general"></a>Ogólne

1. Upewnij się, że używasz oficjalnie obsługiwanego zestawu SDK. Nieobsługiwane zestawy SDK/zestawy SDK społeczności mogą nie obsługiwać korelacji.

    Zapoznaj się z tym [artykułu](https://docs.microsoft.com/azure/application-insights/app-insights-platforms) Aby uzyskać listę obsługiwanych zestawów SDK.

2. Uaktualnij wszystkie składniki do najnowszej wersji zestawu SDK.

3. Jeśli używasz usługi Azure Functions przy użyciu C#Przeprowadź uaktualnienie do [funkcje w wersji 2](https://docs.microsoft.com/azure/azure-functions/functions-versions).

4. Upewnij się, [Nazwa roli w chmurze](#set-cloud-role-name) jest poprawnie skonfigurowana.

5. Jeśli brakuje zależności, upewnij się, że znajduje się ona na liście [automatycznie zebranych zależności](https://docs.microsoft.com/azure/application-insights/auto-collect-dependencies). W przeciwnym razie możesz w dalszym ciągu śledzić ją ręcznie przy użyciu [wywołania zależności śledzenia](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackdependency).

### <a name="too-many-nodes-on-the-map"></a>Zbyt wielu węzłom na mapie

Mapa aplikacji tworzy węzeł aplikacji Każda unikatowa nazwa roli w chmurze w telemetrii żądania i węzeł zależności Każda unikatowa kombinacja typu docelowego i nazwa roli w chmurze w telemetrii zależności. W przypadku ponad 10 000 węzłów w ramach telemetrii, mapa aplikacji nie będzie można pobrać wszystkie węzły i łącza, więc mapa będzie niekompletna. W takim przypadku zostanie wyświetlony komunikat z ostrzeżenie, podczas wyświetlania mapy.

Ponadto mapy aplikacji obsługuje maksymalnie 1000 osobnych węzłach niezgrupowane renderowane tylko raz. Mapa aplikacji zmniejsza wizualnej złożoności przez grupowanie zależności, które mają ten sam typ a obiektami wywołującymi, ale jeśli telemetrii ma zbyt wiele nazw ról unikatowy chmury lub zbyt wiele typów zależności, tej grupy będzie niewystarczająca i Mapa nie będzie można renderowania.

Aby rozwiązać ten problem, musisz zmienić swoje Instrumentacji do prawidłowo ustawiona nazwa roli w chmurze, typ zależności i pól docelowych zależności.

* Docelowy zależności powinny reprezentować nazwę logiczną zależności. W wielu przypadkach jest równoważne z serwera lub nazwa zasobu zależności. Na przykład w przypadku zależności HTTP jest ustawiana zgodnie z nazwą hosta. Nie powinna zawierać unikatowe identyfikatory lub parametry, które zmieniają się między żądaniami do innego.

* Typ zależności powinny reprezentować zależność typu logicznego. Na przykład HTTP, SQL lub obiektów Blob platformy Azure są typów typowych zależności. Nie powinna zawierać unikatowych identyfikatorów.

* Celem Nazwa roli w chmurze jest opisana w [powyżej sekcji](https://docs.microsoft.com/azure/azure-monitor/app/app-map#set-cloud-role-name).

## <a name="portal-feedback"></a>Opinie w portalu

Aby przekazać opinię, użyj opcji opinii.

![Obraz MapLink-1](./media/app-map/14-updated.png)

## <a name="next-steps"></a>Kolejne kroki

* [Zrozumienie korelację](https://docs.microsoft.com/azure/application-insights/application-insights-correlation)
