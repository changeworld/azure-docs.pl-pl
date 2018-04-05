---
title: Obsługa wielu składników, mikrousług i kontenery Azure Application Insights | Dokumentacja firmy Microsoft
description: Monitorowanie aplikacji, które składają się z wielu składników lub ról, wydajności i użycia.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 05/17/2017
ms.author: mbullwin
ms.openlocfilehash: 9b03aff140eec5b355383447f0a815220d6408e3
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2018
---
# <a name="monitor-multi-component-applications-with-application-insights-preview"></a>Monitorowanie wielu składnika aplikacji za pomocą usługi Application Insights (wersja zapoznawcza)

Można monitorować aplikacje, które składają się z wielu składników serwera, ról lub usług z [Azure Application Insights](app-insights-overview.md). Kondycji składników i relacji między nimi są wyświetlane na mapie jednej aplikacji. Można śledzić poszczególnych działań przez kilka składników z automatycznego korelacji HTTP. Diagnostyka kontenera można zintegrowany i skorelowane z danych telemetrycznych aplikacji. Użyj pojedynczego zasobu usługi Application Insights dla wszystkich składników aplikacji. 

![Mapowanie wielu składnika aplikacji](./media/app-insights-monitor-multi-role-apps/app-map.png)

"Component" używana tutaj oznacza dowolną działa część dużych aplikacji. Na przykład typowa aplikacja biznesowa może składać się z kodu klienta działający w przeglądarkach sieci web, rozmowie z jedną lub więcej usług aplikacji sieci web, które z kolei Użyj ponownie zakończenia usługi. Składniki serwera może być obsługiwana lokalnie na w chmurze lub może być Azure role sieci web i proces roboczy lub może działać w kontenerach, takich jak Docker lub sieci szkieletowej usług. 

### <a name="sharing-a-single-application-insights-resource"></a>Udostępnianie pojedynczego zasobu usługi Application Insights 

W tym miejscu klucza technika jest wysłać dane telemetryczne z każdym składniku aplikacji do tego samego zasobu usługi Application Insights, ale `cloud_RoleName` właściwości odróżnienie składniki, gdy jest to konieczne. Dodaje zestaw SDK usługi Application Insights `cloud_RoleName` Emituj właściwości ze składnikami telemetrii. Na przykład zestawu SDK doda nazwa witryny sieci web lub nazwę roli usługi `cloud_RoleName` właściwości. Można zastąpić tę wartość z telemetryinitializer. Mapowanie aplikacji używa `cloud_RoleName` właściwość do identyfikacji składników na mapie.

Aby uzyskać więcej informacji o tym, jak zastąpić `cloud_RoleName` Zobacz właściwość [dodać właściwości: ITelemetryInitializer](app-insights-api-filtering-sampling.md#add-properties-itelemetryinitializer).  

W niektórych przypadkach to nie może być odpowiednie, a użytkownik może chcieć użyć oddzielnych zasobów dla różnych grup składników. Na przykład może być konieczne używania różnych zasobów zarządzania lub rozliczeń do celów. Korzystanie z osobnych zasobów oznacza, że nie wyświetlane wszystkie składniki, które są wyświetlane w jednym mapowaniu aplikacji; oraz że nie można zbadać elementów w [Analytics](app-insights-analytics.md). Należy również skonfigurować oddzielne zasoby.

Z tym ostrzeżenie przyjmiemy w pozostałej części tego dokumentu, który chcesz wysyłać dane z wielu składników do jednego zasobu usługi Application Insights.

## <a name="configure-multi-component-applications"></a>Konfigurowanie wielu składnika aplikacji

Aby uzyskać mapy wielu składnika aplikacji, należy na osiągnięcie tych celów:

* **Zainstalowanie najnowszej wersji wstępnej** pakiet usługi Application Insights w poszczególnych składników aplikacji. 
* **Udostępnij pojedynczy zasób usługi Application Insights** dla wszystkich składników aplikacji.
* **Włącz złożony mapowanie aplikacji** w bloku podglądów.

Skonfiguruj poszczególnych składników aplikacji przy użyciu metody odpowiedniej dla jego typu. ([ASP.NET](app-insights-asp-net.md), [Java](app-insights-java-get-started.md), [Node.js](app-insights-nodejs.md), [JavaScript](app-insights-javascript.md).)

### <a name="1-install-the-latest-pre-release-package"></a>1. Zainstaluj najnowszy pakiet wersji wstępnej

Zaktualizuj lub instalowanie pakietów usługi Application Insights do projektu dla każdego składnika serwera. Jeśli używasz programu Visual Studio:

1. Kliknij prawym przyciskiem myszy projekt i wybierz **Zarządzaj pakietami NuGet**. 
2. Wybierz **Uwzględnij wersję wstępną**.
3. Jeśli usługi Application Insights pakiety są widoczne w aktualizacji, zaznacz je. 

    W przeciwnym razie przeglądania i instalowania odpowiedniego pakietu:
    
    * Microsoft.ApplicationInsights.WindowsServer
    * Microsoft.ApplicationInsights.ServiceFabric - składników uruchomiony jako gość plików wykonywalnych i kontenery Docker uruchamiania aplikacji w sieci szkieletowej usług
    * Microsoft.ApplicationInsights.ServiceFabric.Native - niezawodnych usług w aplikacjach ServiceFabric
    * Microsoft.ApplicationInsights.Kubernetes składników działających w Docker na Kubernetes

### <a name="2-share-a-single-application-insights-resource"></a>2. Udostępnij pojedynczy zasób usługi Application Insights

* W programie Visual Studio, kliknij prawym przyciskiem myszy projekt i wybierz **Konfiguruj usługę Application Insights**, lub **usługi Application Insights > Konfiguruj**. Dla pierwszego projektu Kreator można utworzyć zasobu usługi Application Insights. W przypadku kolejnych projektów wybrać tego samego zasobu.
* Jeśli nie ma żadnych menu usługi Application Insights, skonfigurować ręcznie:

   1. W [portalu Azure](https://portal,azure.com), otwórz zasobu usługi Application Insights już utworzone dla innego składnika.
   2. W bloku Przegląd, Otwórz listę rozwijaną Essentials kartę i skopiuj **klucza instrumentacji.**
   3. W projekcie Otwórz ApplicationInsights.config i Wstaw: `<InstrumentationKey>your copied key</InstrumentationKey>`

![Skopiuj klucz Instrumentacji do pliku .config](./media/app-insights-monitor-multi-role-apps/copy-instrumentation-key.png)


### <a name="3-enable-composite-application-map"></a>3. Włącz mapowanie złożonych aplikacji

Otwórz zasobów aplikacji w portalu Azure. Podrzędne pozycji konfiguracji, kliknij przycisk Podgląd, aby otworzyć blok podglądów. W bloku podglądy włączyć *złożonego mapowanie aplikacji*.

### <a name="4-enable-docker-metrics-optional"></a>4. Włączyć metryki Docker (opcjonalnie) 

Jeśli składnik jest uruchomiony w Docker hostowanych na maszynie Wirtualnej Windows Azure, można zbierać dodatkowe metryki z kontenera. Wstawianie w Twojej [diagnostyki Azure](../monitoring-and-diagnostics/azure-diagnostics.md) pliku konfiguracji:

```
"DiagnosticMonitorConfiguration": {
        ...
        "sinks": "applicationInsights",
        "DockerSources": {
                "Stats": {
                    "enabled": true,
                    "sampleRate": "PT1M"
                }
            },
        ...
    }
    ...   
    "SinksConfig": {
        "Sink": [{
            "name": "applicationInsights",
            "ApplicationInsights": "<your instrumentation key here>"
        }]
    }
    ...
}

```

## <a name="use-cloudrolename-to-separate-components"></a>Użyj cloud_RoleName do oddzielania składników

`cloud_RoleName` Wszystkie dane telemetryczne zostanie dołączona właściwość. Identyfikuje składnik - roli lub usługi -, który pochodzi telemetrii. (Go nie jest taka sama jak cloud_RoleInstance, która oddziela identyczne ról, które są uruchomione jednocześnie na wielu procesy serwera lub maszyny.)

W portalu możesz filtrować i segment telemetrii za pomocą tej właściwości. W tym przykładzie bloku błędów jest filtrowana w celu wyświetlania tylko informacje z usługi frontonu sieci web, filtrowanie błędów z zaplecza interfejsu API CRM:

![Metryki wykresu segmentowanych przez nazwę roli chmury](./media/app-insights-monitor-multi-role-apps/cloud-role-name.png)

## <a name="trace-operations-between-components"></a>Operacje śledzenia między składnikami

Z jednego składnika można śledzić na inny wywołań podczas przetwarzania indywidualnych operacji.


![Pokaż dane telemetryczne dla operacji](./media/app-insights-monitor-multi-role-apps/show-telemetry-for-operation.png)

Kliknij, aby listę skorelowane dane telemetryczne dla tej operacji na serwerze frontonu sieci web i zaplecza interfejsu API:

![Wyszukaj między składnikami](./media/app-insights-monitor-multi-role-apps/search-across-components.png)


## <a name="next-steps"></a>Kolejne kroki

* [Oddzielne dane telemetryczne z programowanie, testowego i produkcyjnego](app-insights-separate-resources.md)
