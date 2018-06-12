---
title: Wywołanie elementu webhook na alert dziennik aktywności platformy Azure (klasyczne)
description: Dowiedz się, jak trasy zdarzenia dziennika działania innych usług dla akcji niestandardowej. Na przykład można wysłać wiadomości SMS, dziennika błędów lub powiadamiania zespołu za pomocą rozmowy lub usługą obsługi wiadomości.
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 01/23/2017
ms.author: johnkem
ms.component: alerts
ms.openlocfilehash: e825d0f2487c20c8c7f3d210d7180b07742d7173
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35262464"
---
# <a name="call-a-webhook-on-an-azure-activity-log-alert"></a>Wywołanie elementu webhook na alert dziennik aktywności platformy Azure
Elementów webhook służy do kierowania powiadomień o alertach Azure z innymi systemami przetwarzania końcowego lub akcji niestandardowych. Elementu webhook na alert służy do kierowania go do usługi, które wysyłanie wiadomości SMS do dziennika błędów do powiadamiania zespołu za pomocą rozmowy lub usług obsługi wiadomości, lub dla różnych innych działań. Alert dziennika aktywności można również skonfigurować do wysyłania wiadomości e-mail, gdy alert jest aktywny.

W tym artykule opisano sposób ustawiania elementu webhook ma być wywoływana po uruchamiany alertu dziennika aktywności platformy Azure. Pokazuje też, jak wygląda ładunek dla HTTP POST do elementu webhook. Informacje o konfiguracji i schematu alert metryki Azure, zobacz [skonfigurować elementu webhook na alert metryki Azure](insights-webhooks-alerts.md). 

> [!NOTE]
> Obecnie funkcja, która obsługuje wywoływania elementu webhook dla alertu dziennik aktywności platformy Azure jest w wersji zapoznawczej.
>
>

Alert dziennika aktywności można skonfigurować przy użyciu [poleceń cmdlet programu Azure PowerShell](insights-powershell-samples.md#create-metric-alerts), [interfejsu wiersza polecenia i platform](insights-cli-samples.md#work-with-alerts), lub [interfejsów API REST Monitor Azure](https://msdn.microsoft.com/library/azure/dn933805.aspx). Obecnie nie można użyć portalu Azure, aby skonfigurować alert dziennika aktywności.

## <a name="authenticate-the-webhook"></a>Uwierzytelnianie elementu webhook
Element webhook może uwierzytelnić przy użyciu jednej z następujących metod:

* **Autoryzacji opartej na tokenach**. Identyfikator URI elementu webhook jest zapisywany przy użyciu identyfikatora tokenu. Na przykład: `https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue`
* **Podstawowe autoryzacji**. Element webhook identyfikatora URI jest zapisywany przy użyciu nazwy użytkownika i hasła. Na przykład: `https://userid:password@mysamplealert/webcallback?someparamater=somevalue&foo=bar`

## <a name="payload-schema"></a>Schemat ładunku
Operację POST zawiera następujące ładunek JSON i schematu dla wszystkich działań opartych na dzienniku alertów. Ten schemat jest podobny do dla alerty na podstawie metryki.

```json
{
    "WebhookName": "Alert1515526229589",
    "RequestBody": {
        "schemaId": "Microsoft.Insights/activityLogs",
        "data": {
            "status": "Activated",
            "context": {
                "activityLog": {
                    "authorization": {
                        "action": "Microsoft.Compute/virtualMachines/deallocate/action",
                        "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoVM/providers/Microsoft.Compute/virtualMachines/ContosoVM1"
                    },
                    "channels": "Operation",
                    "claims": {
                        "aud": "https://management.core.windows.net/",
                        "iss": "https://sts.windows.net/00000000-0000-0000-0000-000000000000/",
                        "iat": "1234567890",
                        "nbf": "1234567890",
                        "exp": "1234567890",
                        "aio": "Y2NgYBD8ZLlhu27JU6WZsXemMIvVAAA=",
                        "appid": "00000000-0000-0000-0000-000000000000",
                        "appidacr": "2",
                        "e_exp": "262800",
                        "http://schemas.microsoft.com/identity/claims/identityprovider": "https://sts.windows.net/00000000-0000-0000-0000-000000000000/",
                        "http://schemas.microsoft.com/identity/claims/objectidentifier": "00000000-0000-0000-0000-000000000000",
                        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "00000000-0000-0000-0000-000000000000",
                        "http://schemas.microsoft.com/identity/claims/tenantid": "00000000-0000-0000-0000-000000000000",
                        "uti": "XnCk46TrDkOQXwo49Y8fAA",
                        "ver": "1.0"
                    },
                    "caller": "00000000-0000-0000-0000-000000000000",
                    "correlationId": "00000000-0000-0000-0000-000000000000",
                    "description": "",
                    "eventSource": "Administrative",
                    "eventTimestamp": "2018-01-09T20:11:25.8410967+00:00",
                    "eventDataId": "00000000-0000-0000-0000-000000000000",
                    "level": "Informational",
                    "operationName": "Microsoft.Compute/virtualMachines/deallocate/action",
                    "operationId": "00000000-0000-0000-0000-000000000000",
                    "resourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoVM/providers/Microsoft.Compute/virtualMachines/ContosoVM1",
                    "resourceGroupName": "ContosoVM",
                    "resourceProviderName": "Microsoft.Compute",
                    "status": "Succeeded",
                    "subStatus": "",
                    "subscriptionId": "00000000-0000-0000-0000-000000000000",
                    "submissionTimestamp": "2018-01-09T20:11:40.2986126+00:00",
                    "resourceType": "Microsoft.Compute/virtualMachines"
                }
            },
            "properties": {}
        }
    },
    "RequestHeader": {
        "Expect": "100-continue",
        "Host": "s1events.azure-automation.net",
        "User-Agent": "IcMBroadcaster/1.0",
        "X-CorrelationContext": "RkkKACgAAAACAAAAEADlBbM7x86VTrHdQ2JlmlxoAQAQALwazYvJ/INPskb8S5QzgDk=",
        "x-ms-request-id": "00000000-0000-0000-0000-000000000000"
    }
}
```

| Nazwa elementu | Opis |
| --- | --- |
| status |Używany w przypadku alertów metryki. Dla alertów dziennika aktywności zawsze ustawiony na aktywny.|
| Kontekst |Kontekst zdarzenia. |
| Dziennik aktywności | Właściwości dziennika zdarzeń.|
| Autoryzacji |Właściwości zdarzenia kontroli dostępu opartej na rolach (RBAC). Zazwyczaj są to właściwości **akcji**, **roli**, i **zakres**. |
| action | Akcja przechwycone przez alert. |
| scope | Zakres alert (zasobów).|
| kanały | Limit czasu operacji. |
| Oświadczenia | Zbiór danych, ponieważ odnosi się do oświadczenia. |
| element wywołujący |Identyfikator GUID lub nazwa użytkownika, który wykonał działanie, oświadczenia UPN lub oświadczeń głównej nazwy usługi na podstawie dostępności. Może to być wartość null dla niektórych wywołań systemowych. |
| correlationId |Zazwyczaj identyfikator GUID w postaci ciągu. Zdarzenia z **correlationId** należą do tego samego działania większy. Zazwyczaj mają takie same **correlationId** wartość. |
| description |Opis alertu, która została ustawiona podczas tworzenia alertu. |
| Źródła zdarzeń |Nazwa usługi Azure lub infrastruktury, który wygenerował zdarzenie. |
| eventTimestamp |Godzina wystąpienia zdarzenia. |
| eventDataId |Unikatowy identyfikator zdarzenia. |
| poziom |Jedną z następujących wartości: krytyczny, błąd, ostrzeżenie, informacyjny lub pełne. |
| operationName |Nazwa operacji. |
| operationId |Zazwyczaj identyfikator GUID współużytkowany zdarzenia. Zazwyczaj identyfikator GUID odpowiada jednej operacji. |
| resourceId |Identyfikator zasobu zasobu. |
| resourceGroupName |Nazwa grupy zasobów dla zasobu. |
| resourceProviderName |Dostawca zasobów zasobu. |
| status |Wartość ciągu, która wskazuje stan operacji. Typowe wartości to rozpoczęte, w toku, zakończyło się pomyślnie, nie powiodło się, aktywnych i rozwiązanych. |
| subStatus |Zwykle zawiera kod stanu HTTP odpowiedniego wywołania REST. Może również zawierać innych ciągów, które opisują podstanu. Typowe wartości podstanu obejmują OK (kod stanu HTTP: 200), utworzony (kod stanu HTTP: 201), akceptowane (kod stanu HTTP: 202), nie zawartości (kod stanu HTTP: 204), nieprawidłowe żądanie (kod stanu HTTP: 400), nie znaleziono (kod stanu HTTP: 404), konflikt (kod stanu HTTP: 409), wewnętrzny błąd serwera (kod stanu HTTP: 500), Usługa niedostępna (kod stanu HTTP: 503) i limit czasu bramy (kod stanu HTTP : 504). |
| subscriptionId |Identyfikator subskrypcji platformy Azure. |
| submissionTimestamp |Czas, w którym to zdarzenie zostało wygenerowane przez usługę Azure, który przetwarzał żądanie. |
| Typ zasobu | Typ zasobu, który wygenerował zdarzenie.|
| properties |Zestaw pary klucz wartość zawiera informacje o zdarzeniu. Na przykład `Dictionary<String, String>`. |

## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się więcej o [dziennik aktywności](monitoring-overview-activity-logs.md).
* Dowiedz się, jak [wykonywanie skryptów automatyzacji Azure (elementów runbook) Azure alerty](http://go.microsoft.com/fwlink/?LinkId=627081).
* Dowiedz się, jak [użycie aplikacji logiki do wysyłania wiadomości SMS za pośrednictwem usługi Twilio z poziomu alertu Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app). W tym przykładzie jest metryki alertów, ale zmodyfikować, aby pracować z alert dziennika aktywności.
* Dowiedz się, jak [użyj aplikacji logiki, aby wysłać wiadomość Slack z poziomu alertu Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app). W tym przykładzie jest metryki alertów, ale zmodyfikować, aby pracować z alert dziennika aktywności.
* Dowiedz się, jak [użyj aplikacji logiki, aby wysłać komunikat do kolejki systemu Azure z poziomu alertu Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app). W tym przykładzie jest metryki alertów, ale zmodyfikować, aby pracować z alert dziennika aktywności.
