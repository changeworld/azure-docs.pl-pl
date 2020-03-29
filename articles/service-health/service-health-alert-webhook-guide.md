---
title: Wysyłanie powiadomień usługi Azure Service Health za pośrednictwem elementów webhook
description: Wysyłanie spersonalizowanych powiadomień o zdarzeniach kondycji usługi do istniejącego systemu zarządzania problemami.
ms.topic: conceptual
ms.service: service-health
ms.date: 3/27/2018
ms.openlocfilehash: 2609a267bd151354f83482ab16c4b9345aa88cc4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062852"
---
# <a name="use-a-webhook-to-configure-health-notifications-for-problem-management-systems"></a>Konfigurowanie powiadomień o kondycji dla systemów zarządzania problemami za pomocą elementu webhook

W tym artykule pokazano, jak skonfigurować alerty usługi Azure Service Health do wysyłania danych za pośrednictwem łączy web do istniejącego systemu powiadomień.

Alerty kondycji usługi można skonfigurować, aby powiadamiać cię za pomocą wiadomości tekstowej lub wiadomości e-mail, gdy dotyczy cię zdarzenie usługi platformy Azure.

Ale może już mieć istniejący zewnętrzny system powiadomień w miejscu, które wolisz używać. W tym artykule identyfikowane najważniejsze części ładunku elementu webhook. I opisano jak utworzyć niestandardowe alerty, aby powiadomić o wystąpieniu odpowiednich problemów z usługą.

Aby użyć wstępnie skonfigurowanego integracji, zobacz:
* [Konfigurowanie alertów za pomocą servicenow](service-health-alert-webhook-servicenow.md)
* [Konfigurowanie alertów za pomocą programu PagerDuty](service-health-alert-webhook-pagerduty.md)
* [Konfigurowanie alertów za pomocą operacji OpsGenie](service-health-alert-webhook-opsgenie.md)

**Obejrzyj film wprowadzający:**

>[!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2OtUV]

## <a name="configure-a-custom-notification-by-using-the-service-health-webhook-payload"></a>Konfigurowanie niestandardowego powiadomienia przy użyciu ładunku elementu webhook kondycji usługi
Aby skonfigurować własną niestandardową integrację elementu webhook, należy przeanalizować ładunek JSON, który jest wysyłany za pośrednictwem powiadomienia kondycji usługi.

Zobacz [przykład](../azure-monitor/platform/activity-log-alerts-webhook.md) `ServiceHealth` ładunku elementu webhook.

Możesz potwierdzić, że jest to alert `context.eventSource == "ServiceHealth"`kondycji usługi, patrząc na . Najbardziej odpowiednie są następujące właściwości:
- **data.context.activityLog.status**
- **data.context.activityLog.level**
- **data.context.activityLog.subscriptionId**
- **data.context.activityLog.properties.title**
- **data.context.activityLog.properties.impactStartTime**
- **data.context.activityLog.properties.communication**
- **data.context.activityLog.properties.impactedSługi**
- **data.context.activityLog.properties.trackingId**

## <a name="create-a-link-to-the-service-health-dashboard-for-an-incident"></a>Tworzenie łącza do pulpitu nawigacyjnego kondycji usługi dla zdarzenia
Bezpośrednie łącze do pulpitu nawigacyjnego kondycji usługi można utworzyć na komputerze lub urządzeniu przenośnym, generując wyspecjalizowany adres URL. Użyj *trackingId* i pierwsze trzy i ostatnie trzy cyfry *subscriptionId* w tym formacie:

https://app.azure.com/h/<i></i>*&lt;trackingId&gt;*/*&lt;pierwsze trzy i trzy ostatnie cyfry identyfikatora&gt; subskrypcji*

Jeśli na przykład identyfikator *subskrypcji* to bba14129-e895-429b-8809-278e836ecdb3, a *identyfikator śledzenia* to 0DET-URB, adres URL kondycji usługi to:

https<i></i>://app.azure.com/h/0DET-URB/bbadb3

## <a name="use-the-level-to-detect-the-severity-of-the-issue"></a>Użyj poziomu, aby wykryć ważność problemu
Od najniższej do najwyższej ważności właściwością **level** w ładunku może być *informacja,* *ostrzeżenie,* *błąd*lub *krytyczna.*

## <a name="parse-the-impacted-services-to-determine-the-incident-scope"></a>Przeanalizować usługi, na które ma wpływ, aby określić zakres incydentu
Alerty kondycji usługi mogą informować o problemach w wielu regionach i usługach. Aby uzyskać pełne szczegóły, należy przeanalizować `impactedServices`wartość .

Zawartość, która znajduje się wewnątrz jest ciąg [JSON](https://json.org/) escaped, który, gdy unescaped, zawiera inny obiekt JSON, które mogą być regularnie analizowane. Przykład:

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

W tym przykładzie pokazano problemy dla:
- "Alerty & metryki" w Australii Wschodniej i Australii Południowo-Wschodniej.
- "App Service" w Australii Południowo-Wschodniej.

## <a name="test-your-webhook-integration-via-an-http-post-request"></a>Testowanie integracji elementu webhook za pomocą żądania HTTP POST

Wykonaj następujące kroki:

1. Utwórz ładunek kondycji usługi, który chcesz wysłać. Zobacz przykładowy ładunek elementu webhook kondycji usługi w [elementach Webhook dla alertów dziennika aktywności platformy Azure.](../azure-monitor/platform/activity-log-alerts-webhook.md)

1. Utwórz żądanie HTTP POST w następujący sposób:

    ```
    POST        https://your.webhook.endpoint

    HEADERS     Content-Type: application/json

    BODY        <service health payload>
    ```
   Powinieneś otrzymać odpowiedź "2XX - Udane".

1. Przejdź do [PagerDuty,](https://www.pagerduty.com/) aby potwierdzić, że integracja została pomyślnie skonfigurowana.

## <a name="next-steps"></a>Następne kroki
- Przejrzyj [schemat programu webhook alertu dziennika aktywności](../azure-monitor/platform/activity-log-alerts-webhook.md). 
- Dowiedz się więcej o [powiadomieniach o kondycji usługi](../azure-monitor/platform/service-notifications.md).
- Dowiedz się więcej o [grupach akcji](../azure-monitor/platform/action-groups.md).