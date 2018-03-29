---
title: Konfigurowanie powiadomień o kondycji istniejących systemów zarządzania problem przy użyciu elementu webhook | Dokumentacja firmy Microsoft
description: Pobierz spersonalizowany powiadomień dotyczących zdarzeń usługi kondycji do systemu zarządzania istniejący problem.
author: shawntabrizi
manager: scotthit
editor: ''
services: service-health
documentationcenter: service-health
ms.assetid: ''
ms.service: service-health
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/27/2018
ms.author: shtabriz
ms.openlocfilehash: 8535caf482b10912e6f7bc6df445756094d7603f
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2018
---
# <a name="configure-health-notifications-for-existing-problem-management-systems-using-a-webhook"></a>Konfigurowanie powiadomień o kondycji istniejących systemów zarządzania problem przy użyciu elementu webhook

W tym artykule pokazano, jak skonfigurować alerty usługi kondycji do przesyłania danych za pomocą elementów Webhook do istniejącego systemu powiadomień.

Obecnie można skonfigurować alerty dotyczące kondycji usługi, aby podczas zdarzenia usługi Azure wystąpi, użytkownik jest informowany za pośrednictwem wiadomości SMS lub wiadomości e-mail.
Jednak może masz już istniejący system zewnętrzne powiadomienia w miejsce którego chcesz użyć.
Ten dokument zawiera najważniejsze części ładunku webhook i tworzenia niestandardowych alerty, Otrzymuj powiadomienia w przypadku problemów dotyczących usługi dotyczy.

Jeśli chcesz użyć wstępnie skonfigurowane integracji, zobacz jak:
* [Konfigurowanie alertów z usługi ServiceNow](service-health-alert-webhook-servicenow.md)
* [Konfigurowanie alertów z PagerDuty](service-health-alert-webhook-pagerduty.md)
* [Konfigurowanie alertów z OpsGenie](service-health-alert-webhook-opsgenie.md)

## <a name="configuring-a-custom-notification-using-the-service-health-webhook-payload"></a>Konfigurowanie niestandardowych powiadomienia za pomocą usługi kondycji elementu webhook ładunku
Aby skonfigurować integrację własnego niestandardowego elementu webhook, należy przeanalizować ładunek JSON, wysyłany podczas powiadomień o kondycji usługi.

Szukaj [tutaj, aby zobaczyć przykład](../monitoring-and-diagnostics/monitoring-activity-log-alerts-webhook.md) co `ServiceHealth` wygląda ładunku elementu webhook.

Może wykryć, analizując jest alertu health service `context.eventSource == "ServiceHealth"`. Dostępne są właściwości, które są najbardziej odpowiednie do pozyskiwania:
 * `data.context.activityLog.status`
 * `data.context.activityLog.level`
 * `data.context.activityLog.subscriptionId`
 * `data.context.activityLog.properties.title`
 * `data.context.activityLog.properties.impactStartTime`
 * `data.context.activityLog.properties.communication`
 * `data.context.activityLog.properties.impactedServices`
 * `data.context.activityLog.properties.trackingId`

## <a name="creating-a-direct-link-to-the-service-health-dashboard-for-an-incident"></a>Utworzenie bezpośredniego łącza do pulpit nawigacyjny kondycji usługi dla incydentu
Można utworzyć bezpośredniego łącza do pulpitu nawigacyjnego usługi kondycji na pulpicie lub przenośne generując specjalne adresu URL. Użyj `trackingId`, oraz imię i nazwisko trzech cyfr Twojego `subscriptionId`, do utworzenia:
```
https://app.azure.com/h/<trackingId>/<first and last three digits of subscriptionId>
```

Na przykład jeśli Twoje `subscriptionId` jest `bba14129-e895-429b-8809-278e836ecdb3` i `trackingId` jest `0DET-URB`, to jest adres URL usługi kondycji:

```
https://app.azure.com/h/0DET-URB/bbadb3
```

## <a name="using-the-level-to-detect-the-severity-of-the-issue"></a>Przy użyciu poziomu do wykrywania ważność problemu
Z najniższą ważności do najwyższej ważności `level` właściwości w ładunku może być dowolny z `Informational`, `Warning`, `Error`, i `Critical`.

## <a name="parsing-the-impacted-services-to-understand-the-full-scope-of-the-incident"></a>Podczas analizowania wpływ na usługi zrozumienie pełnego zakresu zdarzenia
Alerty dotyczące kondycji usługi można informują o problemach w wielu regionach i usług. Aby uzyskać szczegółowe informacje, należy przeanalizować wartość `impactedServices`.
Jest zawartości wewnątrz [JSON wpisywany](http://json.org/) ciąg znaków, gdy niezmienionym znaczeniu, zawiera inny obiekt JSON, który może być analizowana regularnie.

```json
{"data.context.activityLog.properties.impactedServices": "[{\"ImpactedRegions\":[{\"RegionName\":\"Australia East\"},{\"RegionName\":\"Australia Southeast\"}],\"ServiceName\":\"Alerts & Metrics\"},{\"ImpactedRegions\":[{\"RegionName\":\"Australia Southeast\"}],\"ServiceName\":\"App Service\"}]"}
```

Staje się:

```json
[
   {
      "ImpactedRegions":[
         {
            "RegionName":"Australia East"
         },
         {
            "RegionName":"Australia Southeast"
         }
      ],
      "ServiceName":"Alerts & Metrics"
   },
   {
      "ImpactedRegions":[
         {
            "RegionName":"Australia Southeast"
         }
      ],
      "ServiceName":"App Service"
   }
]
```

Oznacza to, że istnieją problemy z "Metryki & alerty" zarówno Australia Wschodnia, jak i południowo-wschodnia, jak również problemy z "App Service" w południowo-wschodnia Australia


## <a name="testing-your-webhook-integration-via-an-http-post-request"></a>Testowanie za pomocą żądania HTTP POST integracją elementu webhook
1. Utwórz ładunku kondycji usługi, który chcesz wysłać. Można znaleźć przykład usługi kondycji elementu webhook ładunku w [elementów Webhook dla działania Azure rejestrowania alertów](../monitoring-and-diagnostics/monitoring-activity-log-alerts-webhook.md).

2. Utwórz żądanie HTTP POST w następujący sposób:

    ```
    POST        https://your.webhook.endpoint

    HEADERS     Content-Type: application/json

    BODY        <service health payload>
    ```
3. Powinien zostać wyświetlony `2XX - Successful` odpowiedzi.

4. Przejdź do [PagerDuty](https://www.pagerduty.com/) aby upewnić się, że integracją zostało pomyślnie skonfigurowane.

## <a name="next-steps"></a>Kolejne kroki
- Przegląd [schemat alertu elementu webhook dziennika aktywności](../monitoring-and-diagnostics/monitoring-activity-log-alerts-webhook.md). 
- Dowiedz się więcej o [usługi powiadomień o kondycji](../monitoring-and-diagnostics/monitoring-service-notifications.md).
- Dowiedz się więcej o [grupy akcji](../monitoring-and-diagnostics/monitoring-action-groups.md).