---
title: Zrozumienie schematu elementu webhook, używane w alertów dziennika aktywności
description: Dowiedz się więcej na temat schematu JSON, który opublikował do adresu URL elementu webhook, gdy aktywuje alertu dziennika aktywności.
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/31/2017
ms.author: johnkem
ms.subservice: alerts
ms.openlocfilehash: 63f59d59712d851f9bb7ace27335fe665a598f9f
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2019
ms.locfileid: "66477921"
---
# <a name="webhooks-for-azure-activity-log-alerts"></a>Elementy Webhook dla alertów dziennika aktywności platformy Azure
Jako część definicji grupy akcji można skonfigurować elementu webhook punktów końcowych, aby otrzymywać powiadomienia o alertach dziennika aktywności. Przy użyciu elementów webhook można kierować te powiadomienia do innych systemów w zakresie przetwarzania końcowego lub niestandardowej akcji. Ten artykuł pokazuje, jak wygląda ładunek HTTP POST do elementu webhook.

Aby uzyskać więcej informacji na temat alertów dziennika aktywności, zobacz instrukcje [Tworzenie alertów dziennika aktywności platformy Azure](activity-log-alerts.md).

Aby uzyskać informacje na temat grup akcji, zobacz instrukcje [tworzenie grup akcji](../../azure-monitor/platform/action-groups.md).

> [!NOTE]
> Można również użyć [wspólny schemat alertu](https://aka.ms/commonAlertSchemaDocs), zapewniającą zaletą pojedynczej rozszerzalne i ujednolicone ładunku alertu przez ten alert usługi w usłudze Azure Monitor, usługi integracji elementu webhook. [Więcej informacji na temat wspólnej definicji schematów alertu.](https://aka.ms/commonAlertSchemaDefinitions)


## <a name="authenticate-the-webhook"></a>Uwierzytelnianie elementu webhook
Element webhook opcjonalnie użyć uwierzytelniania opartego na tokenach autoryzacji. Element webhook, identyfikator URI zapisywany jest przy użyciu tokenu Identyfikatora, na przykład `https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue`.

## <a name="payload-schema"></a>Ładunek schematu
Ładunek JSON zawarte w operacji POST różnią się w zależności w polu data.context.activityLog.eventSource ładunku.

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
### <a name="servicehealth"></a>ServiceHealth
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

### <a name="resourcehealth"></a>ResourceHealth
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

Określonego schematu szczegółowe informacje na temat alertów dzienników aktywności usługi kondycji powiadomień, [usługi powiadomień dotyczących kondycji](../../azure-monitor/platform/service-notifications.md). Dowiedz się również, jak [Konfigurowanie powiadomień webhook o kondycji usługi przy użyciu istniejących rozwiązań zarządzania problem](../../service-health/service-health-alert-webhook-guide.md).

Określonego schematu szczegółowe informacje na temat wszystkich innych alertów dziennika aktywności, [Przegląd dziennika aktywności platformy Azure](../../azure-monitor/platform/activity-logs-overview.md).

| Nazwa elementu | Opis |
| --- | --- |
| status |Używane dla alertów dotyczących metryk. Zawsze wartość "aktywowano" w przypadku alertów dzienników aktywności. |
| Kontekst |Kontekst zdarzenia. |
| resourceProviderName |Dostawca zasobów zasób objęty wpływem. |
| conditionType |Zawsze "zdarzenie". |
| name |Nazwa reguły alertu. |
| id |Identyfikator zasobu alertu. |
| description |Opis alertu, ustawić po utworzeniu alertu. |
| subscriptionId |Identyfikator subskrypcji platformy Azure. |
| timestamp |Czas generowania zdarzenia według usługi platformy Azure, który przetwarzał żądanie. |
| resourceId |Identyfikator zasobu zasób objęty wpływem. |
| resourceGroupName |Nazwa grupy zasobów zasób objęty wpływem. |
| properties |Zestaw `<Key, Value>` pary (czyli `Dictionary<String, String>`) zawierającą szczegółowe informacje o zdarzeniu. |
| zdarzenie |Element, który zawiera metadane dotyczące zdarzenia. |
| Autoryzacja |Kontrola dostępu oparta na rolach właściwości zdarzenia. Zazwyczaj są to właściwości akcji, roli i zakresu. |
| category |Kategoria zdarzenia. Obsługiwane wartości to administracyjne, alertów, zabezpieczeń, ServiceHealth i zalecenia. |
| Obiekt wywołujący |Adres e-mail użytkownika, który wykonał operację, oświadczenia nazwy UPN lub nazwy SPN oświadczenia na podstawie dostępności. Może mieć wartości null dla niektórych wywołań systemowych. |
| correlationId |Zazwyczaj identyfikator GUID w formacie ciągu. Zdarzenia przy użyciu correlationId należą do tego samego działania większych i zazwyczaj udostępnianie correlationId. |
| eventDescription |Statyczny tekst opisu zdarzenia. |
| eventDataId |Unikatowy identyfikator zdarzenia. |
| eventSource |Nazwa usługi platformy Azure lub infrastruktury, który wygenerował zdarzenie. |
| httpRequest |Żądanie zawiera zazwyczaj clientRequestId clientIpAddress i metodę HTTP (na przykład umieścić). |
| poziom |Jeden z następujących wartości: Krytyczny, błąd, ostrzeżenie i informacyjne. |
| operationId |Zazwyczaj identyfikator GUID współużytkowane przez zdarzenia odpowiadający jednej operacji. |
| operationName |Nazwa operacji. |
| properties |Właściwości zdarzenia. |
| status |ciąg. Stan operacji. Typowe wartości to uruchomiona, w toku, zakończone powodzeniem, nie powiodło się, aktywny i rozwiązany. |
| subStatus |Zazwyczaj zawiera kod stanu HTTP odpowiedniego wywołania REST. Może to również obejmować inne ciągi, które opisują podstanu. Typowe wartości podstanu to OK (kod stanu HTTP: 200), utworzone (kod stanu HTTP: 201) zaakceptowane (kod stanu HTTP: 202), żadnej zawartości (kod stanu HTTP: 204), nieprawidłowe żądanie (kod stanu HTTP: 400), nie znaleziono (kod stanu HTTP: 404) konflikt (kod stanu HTTP: 409), wewnętrzny błąd serwera (kod stanu HTTP: 500), Usługa niedostępna (kod stanu HTTP: 503) i limit czasu bramy (kod stanu HTTP: 504). |

## <a name="next-steps"></a>Kolejne kroki
* [Dowiedz się więcej o dzienniku aktywności](../../azure-monitor/platform/activity-logs-overview.md).
* [Wykonywanie skryptów usługi Azure automation (elementy Runbook) na temat alertów platformy Azure](https://go.microsoft.com/fwlink/?LinkId=627081).
* [Wysyłać wiadomość SMS za pośrednictwem usługi Twilio, z poziomu alertu platformy Azure przy użyciu aplikacji logiki](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app). Ten przykład dotyczy alertów dotyczących metryk, ale może być zmodyfikowana, aby pracować alertu dziennika aktywności.
* [Korzystanie z aplikacji logiki, aby wysłać wiadomość Slack z poziomu alertu usługi Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app). Ten przykład dotyczy alertów dotyczących metryk, ale może być zmodyfikowana, aby pracować alertu dziennika aktywności.
* [Użyj aplikacji logiki, aby wysłać komunikat do kolejki platformy Azure z poziomu alertu usługi Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app). Ten przykład dotyczy alertów dotyczących metryk, ale może być zmodyfikowana, aby pracować alertu dziennika aktywności.

