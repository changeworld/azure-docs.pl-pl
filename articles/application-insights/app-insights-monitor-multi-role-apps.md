---
title: Usługa Azure Application Insights, obsługa wielu składników, mikrousługi i kontenery | Dokumentacja firmy Microsoft
description: Monitorowanie aplikacji, które składają się z wielu składników lub ról na potrzeby wydajności i użycia.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/17/2018
ms.author: mbullwin
ms.openlocfilehash: dc776dcfe6fa2e03225649d856ae161c9f23c737
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/01/2018
ms.locfileid: "52727277"
---
# <a name="monitor-multi-component-applications-with-application-insights-preview"></a>Monitorowanie aplikacji wieloskładnikowych za pomocą usługi Application Insights (wersja zapoznawcza)

Można monitorować aplikacje, które składają się z wielu składników serwera, ról lub usługi z [usługi Azure Application Insights](app-insights-overview.md). Kondycja składników i relacje między nimi są wyświetlane na jednej mapie aplikacji. Można śledzić poszczególnych operacji za pomocą wielu składników za pomocą automatycznego korelacji HTTP. Diagnostyki kontenerów można zintegrować i skorelowane z telemetrii aplikacji. Użyj pojedynczego zasobu usługi Application Insights dla wszystkich składników aplikacji. 

![Mapa aplikacji wieloskładnikowych](./media/app-insights-monitor-multi-role-apps/application-map-001.png)

Możemy korzystać "component" oznacza dowolną działa część dużych aplikacji. Na przykład typowa aplikacja biznesowa może zawierać kod klienta działający w przeglądarkach sieci web, rozmawiając z jedną lub więcej usług sieci web aplikacji korzystających z kolei ponownie usługami zaplecza. Składniki serwera może być obsługiwana lokalnie w chmurze, może być role sieć web i procesu roboczego platformy Azure lub może działać w kontenerach, takich jak Docker lub usługi Service Fabric. 

### <a name="sharing-a-single-application-insights-resource"></a>Udostępnianie jednego zasobu usługi Application Insights 

W tym miejscu klucza technika polega na przesyłanie danych telemetrycznych z każdego składnika w aplikacji, aby ten sam zasób usługi Application Insights, ale użyć `cloud_RoleName` właściwości, aby odróżnić składniki, gdy jest to konieczne. Dodaje zestaw SDK usługi Application Insights `cloud_RoleName` wyemitować właściwość do składników danych telemetrycznych. Na przykład dodać zestaw SDK w sytuacji, nazwa witryny sieci web lub nazwę usługi, rola `cloud_RoleName` właściwości. Można zastąpić tę wartość za pomocą parametru telemetryinitializer. Mapa aplikacji używa `cloud_RoleName` właściwość do identyfikacji składniki na mapie.

Aby uzyskać więcej informacji o tym, jak zastąpić `cloud_RoleName` Zobacz właściwość [dodać właściwości: ITelemetryInitializer](app-insights-api-filtering-sampling.md#add-properties-itelemetryinitializer).  

W niektórych przypadkach może to nie być odpowiednie, a użytkownik może chcieć użyć oddzielne zasoby dla różnych grup składników. Może na przykład może być konieczne użycie różnych zasobów do zarządzania lub celów rozliczeniowych.

Z tego ostrzeżenie przyjmiemy w pozostałej części tego dokumentu, które chcesz wysyłać dane z wielu składników do jednego zasobu usługi Application Insights.

## <a name="configure-multi-component-applications"></a>Konfigurowanie aplikacji wieloskładnikowych

Aby uzyskać mapę aplikacji wieloskładnikowych, należy na osiągnięcie tych celów:

* **Instalowanie najnowszej wersji wstępnej** pakiet usługi Application Insights w poszczególnych składników aplikacji. 
* **Udostępniać pojedynczy zasób usługi Application Insights** dla wszystkich składników aplikacji.
* **Włącz Mapa aplikacji złożonej** w bloku wersje zapoznawcze.

Konfigurowania poszczególnych składników aplikacji przy użyciu metody odpowiedniej dla jego typu. ([ASP.NET](app-insights-asp-net.md), [Java](app-insights-java-get-started.md), [Node.js](app-insights-nodejs.md), [JavaScript](app-insights-javascript.md).)

### <a name="1-install-the-latest-pre-release-package"></a>1. Zainstaluj najnowszy pakiet wersji wstępnej

Zaktualizuj lub zainstaluj pakiety usługi Application Insights w projekcie dla każdego składnika serwera. Jeśli używasz programu Visual Studio:

1. Kliknij prawym przyciskiem myszy projekt i wybierz **Zarządzaj pakietami NuGet**. 
2. Wybierz **Uwzględnij wersję wstępną**.
3. Jeśli pakiety usługi Application Insights są wyświetlane w aktualizacji, zaznacz je. 

    W przeciwnym razie Przeglądaj w poszukiwaniu i zainstalowania odpowiedniego pakietu:
    
    * Microsoft.ApplicationInsights.WindowsServer
    * Microsoft.ApplicationInsights.ServiceFabric — dla składników działających jako plików wykonywalnych gościa i kontenerów platformy Docker w aplikacji usługi Service Fabric
    * Microsoft.ApplicationInsights.ServiceFabric.Native — dla usług reliable services w aplikacjach ServiceFabric
    * Microsoft.ApplicationInsights.Kubernetes dla składników działających na platformie Docker na platformie Kubernetes

### <a name="2-share-a-single-application-insights-resource"></a>2. Udostępniać pojedynczy zasób usługi Application Insights

* W programie Visual Studio, kliknij prawym przyciskiem myszy projekt i wybierz **Konfiguruj usługę Application Insights**, lub **usługi Application Insights > Konfiguruj**. Dla pierwszego projektu należy użyć kreatora, aby utworzyć zasób usługi Application Insights. W przypadku kolejnych projektów wybierz ten sam zasób.
* W przypadku menu usługi Application Insights nie ręcznie skonfigurować:

   1. W [witryny Azure portal](https://portal,azure.com), otwórz zasób usługi Application Insights nie zostało utworzone dla innego składnika.
   2. W bloku przeglądu, kartę Otwieranie listy rozwijanej podstawowe elementy i kopiowanie **klucz instrumentacji.**
   3. W projekcie Otwórz plik ApplicationInsights.config i Wstaw: `<InstrumentationKey>your copied key</InstrumentationKey>`

![Skopiuj klucz Instrumentacji do pliku config](./media/app-insights-monitor-multi-role-apps/copy-instrumentation-key.png)


### <a name="3-enable-composite-application-map"></a>3. Włącz Mapa aplikacji złożonej

W witrynie Azure portal otwórz zasób swojej aplikacji. W obszarze Konfiguracja nagłówek podrzędny, kliknij wersji zapoznawczych, aby otworzyć blok wersji zapoznawczych. W bloku wersje zapoznawcze włączyć *Mapa aplikacji złożonej*.

### <a name="4-enable-docker-metrics-optional"></a>4. Włącz metryki platformy Docker (opcjonalnie) 

Jeśli składnik jest uruchomiony na platformie Docker hostowanych na maszynie Wirtualnej Windows Azure, możesz zbierać dodatkowe metryki z kontenera. Wstaw ten w Twojej [Diagnostyka Azure](../monitoring-and-diagnostics/azure-diagnostics.md) pliku konfiguracji:

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

## <a name="use-cloudrolename-to-separate-components"></a>Użyj cloud_RoleName do oddzielenia składników

`cloud_RoleName` Właściwość jest dołączony do wszystkich danych telemetrycznych. Identyfikuje składnik - roli lub usługi -, który pochodzi z danych telemetrycznych. (Nie jest taka sama jak cloud_RoleInstance, który oddziela identyczne role, które działają równolegle na wielu procesów serwera lub maszyn.)

W portalu możesz filtrować lub segmentu danych telemetrycznych za pomocą tej właściwości. W tym przykładzie blok błędów jest filtrowana w celu Pokaż tylko informacje z usługi internetowej frontonu filtrowanie błędów z zaplecza interfejsu API CRM:

![Wykres metryk posegmentowana według nazwy roli w chmurze](./media/app-insights-monitor-multi-role-apps/cloud-role-name.png)

## <a name="trace-operations-between-components"></a>Operacje śledzenia między składnikami

Można śledzić od jednego elementu do innego wywołania wykonywane podczas przetwarzania poszczególnych operacji.


![Pokaż telemetrię dla operacji](./media/app-insights-monitor-multi-role-apps/show-telemetry-for-operation.png)

Kliknij, aby skorelowane listę dane telemetryczne dla tej operacji na serwerze frontonu sieci web i interfejsu API zaplecza:

![Wyszukiwania między składnikami](./media/app-insights-monitor-multi-role-apps/search-across-components.png)


## <a name="next-steps"></a>Kolejne kroki

* [Osobne dane telemetryczne z programowania, testowania i produkcji](app-insights-separate-resources.md)
