---
title: Opis schematu elementu webhook używanego w alertach dziennika aktywności
description: Dowiedz się więcej o schemacie JSON, który jest publikowany w adresie URL elementu webhook po aktywowaniu alertu dziennika aktywności.
ms.topic: conceptual
ms.date: 03/31/2017
ms.subservice: alerts
ms.openlocfilehash: c076b8dcea350f9ddd66977e89ce99b81f377b17
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77669050"
---
# <a name="webhooks-for-azure-activity-log-alerts"></a>Elementów Webhook dla alertów dziennika aktywności platformy Azure
W ramach definicji grupy akcji można skonfigurować punkty końcowe elementu webhook do odbierania powiadomień alertów dziennika aktywności. Za pomocą elementów webhook można kierować te powiadomienia do innych systemów w celu przetwarzania końcowego lub akcji niestandardowych. W tym artykule pokazano, jak wygląda ładunek wpisu HTTP do elementu webhook.

Aby uzyskać więcej informacji na temat alertów dziennika aktywności, zobacz jak [tworzyć alerty dziennika aktywności platformy Azure](activity-log-alerts.md).

Aby uzyskać informacje na temat grup akcji, zobacz jak [tworzyć grupy akcji](../../azure-monitor/platform/action-groups.md).

> [!NOTE]
> Można również użyć [wspólnego schematu alertów](https://aka.ms/commonAlertSchemaDocs), który zapewnia zaletę posiadania jednego rozszerzalnego i ujednoliconego ładunku alertów we wszystkich usługach alertów w usłudze Azure Monitor dla integracji elementu webhook. [Dowiedz się więcej o definicji schematów alertów.](https://aka.ms/commonAlertSchemaDefinitions)


## <a name="authenticate-the-webhook"></a>Uwierzytelnij element webhook
Element webhook można opcjonalnie używać autoryzacji opartej na tokenie do uwierzytelniania. Identyfikator URI elementu webhook jest zapisywany przy `https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue`identyfikatorze tokenu, na przykład .

## <a name="payload-schema"></a>Schemat ładunku
Ładunek JSON zawarty w operacji POST różni się w zależności od pola data.context.activityLog.eventSource ładunku.

### <a name="common"></a>Wspólne

```json
{
    "schemaId": "Microsoft.Insights/activityLogs",
    "data": {
        "status": "Activated",
        "context": {
            "activityLog": {
                "channels": "Operation",
                "correlationId": "6ac88262-43be-4adf-a11c-bd2179852898",
                "eventSource": "Administrative",
                "eventTimestamp": "2017-03-29T15:43:08.0019532+00:00",
                "eventDataId": "8195a56a-85de-4663-943e-1a2bf401ad94",
                "level": "Informational",
                "operationName": "Microsoft.Insights/actionGroups/write",
                "operationId": "6ac88262-43be-4adf-a11c-bd2179852898",
                "status": "Started",
                "subStatus": "",
                "subscriptionId": "52c65f65-0518-4d37-9719-7dbbfc68c57a",
                "submissionTimestamp": "2017-03-29T15:43:20.3863637+00:00",
                ...
            }
        },
        "properties": {}
    }
}
```

### <a name="administrative"></a>Administracyjne

```json
{
    "schemaId": "Microsoft.Insights/activityLogs",
    "data": {
        "status": "Activated",
        "context": {
            "activityLog": {
                "authorization": {
                    "action": "Microsoft.Insights/actionGroups/write",
                    "scope": "/subscriptions/52c65f65-0518-4d37-9719-7dbbfc68c57b/resourceGroups/CONTOSO-TEST/providers/Microsoft.Insights/actionGroups/IncidentActions"
                },
                "claims": "{...}",
                "caller": "me@contoso.com",
                "description": "",
                "httpRequest": "{...}",
                "resourceId": "/subscriptions/52c65f65-0518-4d37-9719-7dbbfc68c57b/resourceGroups/CONTOSO-TEST/providers/Microsoft.Insights/actionGroups/IncidentActions",
                "resourceGroupName": "CONTOSO-TEST",
                "resourceProviderName": "Microsoft.Insights",
                "resourceType": "Microsoft.Insights/actionGroups"
            }
        },
        "properties": {}
    }
}
```

### <a name="security"></a>Zabezpieczenia

```json
{
    "schemaId":"Microsoft.Insights/activityLogs",
    "data":{"status":"Activated",
        "context":{
            "activityLog":{
                "channels":"Operation",
                "correlationId":"2518408115673929999",
                "description":"Failed SSH brute force attack. Failed brute force attacks were detected from the following attackers: [\"IP Address: 01.02.03.04\"].  Attackers were trying to access the host with the following user names: [\"root\"].",
                "eventSource":"Security",
                "eventTimestamp":"2017-06-25T19:00:32.607+00:00",
                "eventDataId":"Sec-07f2-4d74-aaf0-03d2f53d5a33",
                "level":"Informational",
                "operationName":"Microsoft.Security/locations/alerts/activate/action",
                "operationId":"Sec-07f2-4d74-aaf0-03d2f53d5a33",
                "properties":{
                    "attackers":"[\"IP Address: 01.02.03.04\"]",
                    "numberOfFailedAuthenticationAttemptsToHost":"456",
                    "accountsUsedOnFailedSignInToHostAttempts":"[\"root\"]",
                    "wasSSHSessionInitiated":"No","endTimeUTC":"06/25/2017 19:59:39",
                    "actionTaken":"Detected",
                    "resourceType":"Virtual Machine",
                    "severity":"Medium",
                    "compromisedEntity":"LinuxVM1",
                    "remediationSteps":"[In case this is an Azure virtual machine, add the source IP to NSG block list for 24 hours (see https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/)]",
                    "attackedResourceType":"Virtual Machine"
                },
                "resourceId":"/subscriptions/12345-5645-123a-9867-123b45a6789/resourceGroups/contoso/providers/Microsoft.Security/locations/centralus/alerts/Sec-07f2-4d74-aaf0-03d2f53d5a33",
                "resourceGroupName":"contoso",
                "resourceProviderName":"Microsoft.Security",
                "status":"Active",
                "subscriptionId":"12345-5645-123a-9867-123b45a6789",
                "submissionTimestamp":"2017-06-25T20:23:04.9743772+00:00",
                "resourceType":"MICROSOFT.SECURITY/LOCATIONS/ALERTS"
            }
        },
        "properties":{}
    }
}
```

### <a name="recommendation"></a>Zalecenie

```json
{
    "schemaId":"Microsoft.Insights/activityLogs",
    "data":{
        "status":"Activated",
        "context":{
            "activityLog":{
                "channels":"Operation",
                "claims":"{\"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress\":\"Microsoft.Advisor\"}",
                "caller":"Microsoft.Advisor",
                "correlationId":"123b4c54-11bb-3d65-89f1-0678da7891bd",
                "description":"A new recommendation is available.",
                "eventSource":"Recommendation",
                "eventTimestamp":"2017-06-29T13:52:33.2742943+00:00",
                "httpRequest":"{\"clientIpAddress\":\"0.0.0.0\"}",
                "eventDataId":"1bf234ef-e45f-4567-8bba-fb9b0ee1dbcb",
                "level":"Informational",
                "operationName":"Microsoft.Advisor/recommendations/available/action",
                "properties":{
                    "recommendationSchemaVersion":"1.0",
                    "recommendationCategory":"HighAvailability",
                    "recommendationImpact":"Medium",
                    "recommendationName":"Enable Soft Delete to protect your blob data",
                    "recommendationResourceLink":"https://portal.azure.com/#blade/Microsoft_Azure_Expert/RecommendationListBlade/recommendationTypeId/12dbf883-5e4b-4f56-7da8-123b45c4b6e6",
                    "recommendationType":"12dbf883-5e4b-4f56-7da8-123b45c4b6e6"
                },
                "resourceId":"/subscriptions/12345-5645-123a-9867-123b45a6789/resourceGroups/contoso/providers/microsoft.storage/storageaccounts/contosoStore",
                "resourceGroupName":"CONTOSO",
                "resourceProviderName":"MICROSOFT.STORAGE",
                "status":"Active",
                "subStatus":"",
                "subscriptionId":"12345-5645-123a-9867-123b45a6789",
                "submissionTimestamp":"2017-06-29T13:52:33.2742943+00:00",
                "resourceType":"MICROSOFT.STORAGE/STORAGEACCOUNTS"
            }
        },
        "properties":{}
    }
}
```

### <a name="servicehealth"></a>UsługiZdrowie

```json
{
    "schemaId": "Microsoft.Insights/activityLogs",
    "data": {
        "status": "Activated",
        "context": {
            "activityLog": {
            "channels": "Admin",
            "correlationId": "bbac944f-ddc0-4b4c-aa85-cc7dc5d5c1a6",
            "description": "Active: Virtual Machines - Australia East",
            "eventSource": "ServiceHealth",
            "eventTimestamp": "2017-10-18T23:49:25.3736084+00:00",
            "eventDataId": "6fa98c0f-334a-b066-1934-1a4b3d929856",
            "level": "Informational",
            "operationName": "Microsoft.ServiceHealth/incident/action",
            "operationId": "bbac944f-ddc0-4b4c-aa85-cc7dc5d5c1a6",
            "properties": {
                "title": "Virtual Machines - Australia East",
                "service": "Virtual Machines",
                "region": "Australia East",
                "communication": "Starting at 02:48 UTC on 18 Oct 2017 you have been identified as a customer using Virtual Machines in Australia East who may receive errors starting Dv2 Promo and DSv2 Promo Virtual Machines which are in a stopped &quot;deallocated&quot; or suspended state. Customers can still provision Dv1 and Dv2 series Virtual Machines or try deploying Virtual Machines in other regions, as a possible workaround. Engineers have identified a possible fix for the underlying cause, and are exploring implementation options. The next update will be provided as events warrant.",
                "incidentType": "Incident",
                "trackingId": "0NIH-U2O",
                "impactStartTime": "2017-10-18T02:48:00.0000000Z",
                "impactedServices": "[{\"ImpactedRegions\":[{\"RegionName\":\"Australia East\"}],\"ServiceName\":\"Virtual Machines\"}]",
                "defaultLanguageTitle": "Virtual Machines - Australia East",
                "defaultLanguageContent": "Starting at 02:48 UTC on 18 Oct 2017 you have been identified as a customer using Virtual Machines in Australia East who may receive errors starting Dv2 Promo and DSv2 Promo Virtual Machines which are in a stopped &quot;deallocated&quot; or suspended state. Customers can still provision Dv1 and Dv2 series Virtual Machines or try deploying Virtual Machines in other regions, as a possible workaround. Engineers have identified a possible fix for the underlying cause, and are exploring implementation options. The next update will be provided as events warrant.",
                "stage": "Active",
                "communicationId": "636439673646212912",
                "version": "0.1.1"
            },
            "status": "Active",
            "subscriptionId": "45529734-0ed9-4895-a0df-44b59a5a07f9",
            "submissionTimestamp": "2017-10-18T23:49:28.7864349+00:00"
        }
    },
    "properties": {}
    }
}
```

Aby uzyskać szczegółowe informacje o schemacie alertów dziennika zdarzeń powiadomień o kondycji usługi, zobacz [Powiadomienia o kondycji usługi](../../azure-monitor/platform/service-notifications.md). Ponadto dowiedz się, jak [skonfigurować powiadomienia dotyczące kondycji usługi webhook za pomocą istniejących rozwiązań do zarządzania problemami.](../../service-health/service-health-alert-webhook-guide.md)

### <a name="resourcehealth"></a>Zdrowie zasobów

```json
{
    "schemaId": "Microsoft.Insights/activityLogs",
    "data": {
        "status": "Activated",
        "context": {
            "activityLog": {
                "channels": "Admin, Operation",
                "correlationId": "a1be61fd-37ur-ba05-b827-cb874708babf",
                "eventSource": "ResourceHealth",
                "eventTimestamp": "2018-09-04T23:09:03.343+00:00",
                "eventDataId": "2b37e2d0-7bda-4de7-ur8c6-1447d02265b2",
                "level": "Informational",
                "operationName": "Microsoft.Resourcehealth/healthevent/Activated/action",
                "operationId": "2b37e2d0-7bda-489f-81c6-1447d02265b2",
                "properties": {
                    "title": "Virtual Machine health status changed to unavailable",
                    "details": "Virtual machine has experienced an unexpected event",
                    "currentHealthStatus": "Unavailable",
                    "previousHealthStatus": "Available",
                    "type": "Downtime",
                    "cause": "PlatformInitiated"
                },
                "resourceId": "/subscriptions/<subscription Id>/resourceGroups/<resource group>/providers/Microsoft.Compute/virtualMachines/<resource name>",
                "resourceGroupName": "<resource group>",
                "resourceProviderName": "Microsoft.Resourcehealth/healthevent/action",
                "status": "Active",
                "subscriptionId": "<subscription Id>",
                "submissionTimestamp": "2018-09-04T23:11:06.1607287+00:00",
                "resourceType": "Microsoft.Compute/virtualMachines"
            }
        }
    }
}
```

| Nazwa elementu | Opis |
| --- | --- |
| status |Używane do alertów metryk. Zawsze ustawiono na "aktywowane" dla alertów dziennika aktywności. |
| kontekst |Kontekst zdarzenia. |
| nazwa zasobu |Dostawca zasobów zasobu, na który ma wpływ. |
| warunek Typ |Zawsze "Zdarzenie". |
| name |Nazwa reguły alertu. |
| id |Identyfikator zasobu alertu. |
| description |Opis alertu ustawiony podczas tworzenia alertu. |
| subscriptionId |Identyfikator subskrypcji platformy Azure. |
| sygnatura czasowa |Czas, w którym zdarzenie zostało wygenerowane przez usługę Platformy Azure, która przetworzyła żądanie. |
| resourceId |Identyfikator zasobu, na który ma wpływ. |
| resourceGroupName |Nazwa grupy zasobów zasobu, do przypadku tego wpływu. |
| properties |Zestaw `<Key, Value>` par `Dictionary<String, String>`(czyli), który zawiera szczegółowe informacje o zdarzeniu. |
| event |Element, który zawiera metadane o zdarzeniu. |
| autoryzacja |Właściwości kontroli dostępu oparte na rolach zdarzenia. Te właściwości zwykle obejmują akcję, rolę i zakres. |
| category |Kategoria wydarzenia. Obsługiwane wartości obejmują administracyjne, alert, zabezpieczenia, servicehealth i zalecenia. |
| Obiekt wywołujący |Adres e-mail użytkownika, który wykonał operację, oświadczenie UPN lub oświadczenie nazwy SPN na podstawie dostępności. Może być null dla niektórych wywołań systemowych. |
| correlationId |Zazwyczaj identyfikator GUID w formacie ciągu. Zdarzenia z correlationId należą do tej samej większej akcji i zwykle współużytkująid correlationId. |
| eventDescription (opis zdarzeń) |Statyczny opis tekstowy zdarzenia. |
| identyfikator danych zdarzeń |Unikatowy identyfikator zdarzenia. |
| Eventsource |Nazwa usługi platformy Azure lub infrastruktury, która wygenerowała zdarzenie. |
| HttpRequest (Prośba o |Żądanie zwykle zawiera clientRequestId, clientIpAddress i http metody (na przykład PUT). |
| poziom |Jedna z następujących wartości: Krytyczna, Błąd, Ostrzeżenie i Informacje. |
| operationId |Zazwyczaj identyfikator GUID współużytkowane przez zdarzenia odpowiadające pojedynczej operacji. |
| operationName |Nazwa operacji. |
| properties |Właściwości zdarzenia. |
| status |Ciąg. Stan operacji. Typowe wartości obejmują rozpoczęte, w toku, powiodło się, nie powiodło się, aktywne i rozwiązane. |
| Podstanu |Zwykle zawiera kod stanu HTTP odpowiedniego wywołania REST. Może również zawierać inne ciągi, które opisują podstatus. Typowe podstatusy: OK (kod stanu HTTP: 200), utworzony (kod stanu HTTP: 201), zaakceptowany (kod stanu HTTP: 202), brak zawartości (kod stanu HTTP: 204), złe żądanie (kod stanu HTTP: 400), nieodebrania (kod stanu HTTP: 404), konflikt (kod stanu HTTP: 409 ), Wewnętrzny błąd serwera (kod stanu HTTP: 500), usługa niedostępna (kod stanu HTTP: 503) i limit czasu bramy (kod stanu HTTP: 504). |

Aby uzyskać szczegółowe informacje o schemacie wszystkich innych alertów dziennika aktywności, zobacz [Omówienie dziennika aktywności platformy Azure](../../azure-monitor/platform/platform-logs-overview.md).

## <a name="next-steps"></a>Następne kroki
* [Dowiedz się więcej o dzienniku aktywności](../../azure-monitor/platform/platform-logs-overview.md).
* [Wykonywanie skryptów automatyzacji platformy Azure (Runbooks) w alertach platformy Azure](https://go.microsoft.com/fwlink/?LinkId=627081).
* [Użyj aplikacji logiki, aby wysłać wiadomość SMS za pośrednictwem usługi Twilio z alertu platformy Azure.](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app) W tym przykładzie jest dla alertów metryki, ale można go zmodyfikować do pracy z alertem dziennika aktywności.
* [Użyj aplikacji logiki, aby wysłać komunikat Slack z alertu platformy Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app). W tym przykładzie jest dla alertów metryki, ale można go zmodyfikować do pracy z alertem dziennika aktywności.
* [Użyj aplikacji logiki, aby wysłać wiadomość do kolejki platformy Azure z alertu platformy Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app). W tym przykładzie jest dla alertów metryki, ale można go zmodyfikować do pracy z alertem dziennika aktywności.

