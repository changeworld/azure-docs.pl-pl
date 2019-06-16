---
title: Konfigurowanie powiadomień dotyczących kondycji usługi platformy Azure istniejących systemów zarządzania problem przy użyciu elementu webhook
description: Uzyskaj Spersonalizowane powiadomienia dotyczące zdarzenia dotyczące kondycji usługi do istniejącej systemie zarządzania problemami.
author: stephbaron
ms.author: stbaron
ms.topic: conceptual
ms.service: service-health
ms.workload: Supportability
ms.date: 3/27/2018
ms.openlocfilehash: 3e9a564310d750e63c9cf81d19f698e75712d09a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67067188"
---
# <a name="configure-health-notifications-for-existing-problem-management-systems-using-a-webhook"></a>Konfigurowanie powiadomień dotyczących kondycji dla istniejących systemów zarządzania problem przy użyciu elementu webhook

W tym artykule pokazano, jak skonfigurować alerty usługi kondycji do przesyłania danych za pośrednictwem elementów Webhook do istniejącego systemu powiadomień.

Obecnie można skonfigurować alerty dotyczące kondycji usługi, tak, że gdy zdarzenie w usłudze Azure wystąpi Odbieraj powiadomienia w wiadomościach SMS lub wiadomości e-mail.
Jednak może masz już istniejący system powiadomienia zewnętrzne w miejscu, które chcesz użyć.
W tym dokumencie przedstawiono najważniejsze elementy ładunek elementu webhook i jak można utworzyć niestandardowe alerty, które otrzymaj powiadomienie, gdy napotkasz problemy z usługą.

Jeśli chcesz korzystać z integracji wstępnie skonfigurowane, zobacz jak:
* [Konfigurowanie alertów za pomocą usługi ServiceNow](service-health-alert-webhook-servicenow.md)
* [Konfigurowanie alertów za pomocą usługi PagerDuty](service-health-alert-webhook-pagerduty.md)
* [Konfigurowanie alertów za pomocą OpsGenie](service-health-alert-webhook-opsgenie.md)

### <a name="watch-an-introductory-video"></a>Obejrzyj klip wideo

>[!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2OtUV]

## <a name="configuring-a-custom-notification-using-the-service-health-webhook-payload"></a>Konfigurowanie niestandardowych powiadomienia za pomocą ładunek elementu webhook usługi kondycji
Aby skonfigurować integrację własnych niestandardowych elementów webhook, należy przeanalizować ładunku JSON, który jest wysyłany podczas powiadomień dotyczących kondycji usługi.

Szukaj [tutaj, aby zobaczyć przykład](../azure-monitor/platform/activity-log-alerts-webhook.md) określenie, co `ServiceHealth` wygląda ładunek elementu webhook.

Wykrywanie, jest to alert kondycji usługi, analizując `context.eventSource == "ServiceHealth"`. W tym miejscu właściwości, które są najbardziej odpowiednie do pozyskiwania to:
 * `data.context.activityLog.status`
 * `data.context.activityLog.level`
 * `data.context.activityLog.subscriptionId`
 * `data.context.activityLog.properties.title`
 * `data.context.activityLog.properties.impactStartTime`
 * `data.context.activityLog.properties.communication`
 * `data.context.activityLog.properties.impactedServices`
 * `data.context.activityLog.properties.trackingId`

## <a name="creating-a-direct-link-to-the-service-health-dashboard-for-an-incident"></a>Tworzenie bezpośredni link do pulpitu nawigacyjnego kondycji usług dla incydentu
Aby utworzenie bezpośredniego łącza do pulpitu nawigacyjnego kondycji usług na komputerze lub urządzeniu przenośnym, generowanie wyspecjalizowane adresu URL. Użyj `trackingId`, a także pierwsze i ostatnie trzy cyfry Twojego `subscriptionId`, w celu utworzenia:
```
https://app.azure.com/h/<trackingId>/<first and last three digits of subscriptionId>
```

Na przykład jeśli Twoja `subscriptionId` jest `bba14129-e895-429b-8809-278e836ecdb3` i `trackingId` jest `0DET-URB`, to jest adres URL Twojej usługi kondycji:

```
https://app.azure.com/h/0DET-URB/bbadb3
```

## <a name="using-the-level-to-detect-the-severity-of-the-issue"></a>Przy użyciu poziomu, aby wykryć ważność problemu
Z ważność najniższej do najwyższej ważności `level` właściwość w ładunku może być dowolny z `Informational`, `Warning`, `Error`, i `Critical`.

## <a name="parsing-the-impacted-services-to-understand-the-full-scope-of-the-incident"></a>Podczas analizowania objęte wpływem usług, aby zrozumieć pełny zakres zdarzenia
Alerty dotyczące kondycji usługi może wcześniej poinformować o problemach w wielu regionach i usług. Aby uzyskać szczegółowe informacje, należy przeanalizować wartości `impactedServices`.
Zawartość wewnątrz [JSON poprzedzone znakiem zmiany znaczenia](https://json.org/) string, gdy o niezmienionym znaczeniu, zawiera inny obiekt JSON, który może zostać przeanalizowany regularnie.

```json
{"data.context.activityLog.properties.impactedServices": "[{\"ImpactedRegions\":[{\"RegionName\":\"Australia East\"},{\"RegionName\":\"Australia Southeast\"}],\"ServiceName\":\"Alerts & Metrics\"},{\"ImpactedRegions\":[{\"RegionName\":\"Australia Southeast\"}],\"ServiceName\":\"App Service\"}]"}
```

staje się:

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

Pokazuje to, że nie istnieją problemy z "Metryki i alerty" w południowo-wschodnia i Australia Wschodnia, jak również problemy z "App Service" w Australii południowo-wschodnia.


## <a name="testing-your-webhook-integration-via-an-http-post-request"></a>Testowanie integracji usługi elementu webhook, za pomocą żądania HTTP POST
1. Utwórz ładunek kondycji usługi, które mają zostać wysłane. Można znaleźć, ładunek elementu webhook przykład usługi kondycji w [alerty dzienników elementy Webhook dla aktywności platformy Azure](../azure-monitor/platform/activity-log-alerts-webhook.md).

2. Utwórz żądanie HTTP POST w następujący sposób:

    ```
    POST        https://your.webhook.endpoint

    HEADERS     Content-Type: application/json

    BODY        <service health payload>
    ```
3. Powinien zostać wyświetlony `2XX - Successful` odpowiedzi.

4. Przejdź do [PagerDuty](https://www.pagerduty.com/) aby upewnić się, że integracji usługi zostało pomyślnie skonfigurowane.

## <a name="next-steps"></a>Kolejne kroki
- Przegląd [schemat elementów webhook alertu dziennika aktywności](../azure-monitor/platform/activity-log-alerts-webhook.md). 
- Dowiedz się więcej o [usługi powiadomień dotyczących kondycji](../azure-monitor/platform/service-notifications.md).
- Dowiedz się więcej o [grup akcji](../azure-monitor/platform/action-groups.md).