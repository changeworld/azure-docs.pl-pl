---
title: Akcje elementu Webhook dla dziennika alertów w alertach Azure
description: W tym artykule opisano, jak reguły alertu dziennika przy użyciu dziennika analityka lub application insights przeprowadzi wypychanie danych jako elementu webhook HTTP i szczegóły różne dostosowania możliwe.
author: msvijayn
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: vinagara
ms.component: alerts
ms.openlocfilehash: 304476e2d6862fbb6a859ae6fefe96d177b1111b
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35264259"
---
# <a name="webhook-actions-for-log-alert-rules"></a>Akcje elementu Webhook dla dziennika reguły alertów
Gdy [alert jest tworzony na platformie Azure ](monitor-alerts-unified-usage.md), masz możliwość [konfigurowania za pomocą grup akcji](monitoring-action-groups.md) przeprowadzenie jedną lub więcej akcji.  W tym artykule opisano webhook różne akcje, które są dostępne i szczegółowe informacje na temat konfigurowania niestandardowego elementu webhook opartych na formacie JSON.


## <a name="webhook-actions"></a>Akcje elementu Webhook

Akcje elementu Webhook umożliwiają wywołanie procesu zewnętrznego przez pojedyncze żądanie HTTP POST.  Usługa wywoływana powinien obsługuje elementów webhook i określić, jak używa żadnych ładunku odbiera.   Przykłady użycia elementu webhook w odpowiedzi na alert wysyłania wiadomości [Slack](http://slack.com) lub Tworzenie zdarzenia w [PagerDuty](http://pagerduty.com/).  

Akcje elementu Webhook wymagają właściwości w poniższej tabeli:

| Właściwość | Opis |
|:--- |:--- |
| Adres URL elementu Webhook |Adres URL elementu webhook. |
| Niestandardowy ładunek JSON |Niestandardowy ładunek do wysłania z elementu webhook, po wybraniu tej opcji podczas tworzenia alertu. Szczegóły dostępne pod adresem [Zarządzanie alertami korzystanie z alertów Azure ](monitor-alerts-unified-usage.md) |

> [!NOTE]
> Testowanie przycisk elementu Webhook obok *Uwzględnij niestandardowy ładunek JSON dla elementu webhook* opcja dla dziennika alertu, wyzwoli fikcyjny wywołanie test adresu URL elementu webhook. Nie zawiera danych rzeczywistych i przedstawiciela używany w przypadku alertów dziennika schematu JSON. 

Elementów Webhook obejmują adres URL i zapisany w formacie JSON, które to dane wysyłane do zewnętrznych usługi ładunku.  Domyślnie ładunek zawiera wartości w poniższej tabeli: możesz zastąpić ten ładunku niestandardowego własny.  W takim przypadku służy zmiennych w tabeli dla każdego z parametrów do uwzględnienia w niestandardowy ładunek ich wartości.


| Parametr | Zmienna | Opis |
|:--- |:--- |:--- |
| AlertRuleName |#alertrulename |Nazwa reguły alertów. |
| Ważność |#severity |Ważność, ustaw dla alertu wypalane dziennika. |
| AlertThresholdOperator |#thresholdoperator |Operator próg dla reguł alertów.  *Większa niż* lub *mniej niż*. |
| AlertThresholdValue |#thresholdvalue |Wartość progowa reguły alertów. |
| LinkToSearchResults |#linktosearchresults |Link do portalu analityka, która zwraca odpowiednie rekordy z kwerendy utworzony alert. |
| ResultCount |#searchresultcount |Liczba rekordów w wynikach wyszukiwania. |
| Godzina zakończenia interwał wyszukiwania |#searchintervalendtimeutc |Godziny zakończenia dla zapytania w formacie UTC, format — mm/dd/rrrr gg: mm: ss AM/PM. |
| Interwał wyszukiwania |#searchinterval |Przedział czasu dla alertu reguły, format — hh: mm:. |
| Wartość StartTime interwał wyszukiwania |#searchintervalstarttimeutc |Godziny rozpoczęcia dla zapytania w formacie UTC, format — mm/dd/rrrr gg: mm: ss AM/PM. 
| SearchQuery |#searchquery |Dziennik wyszukiwania używane przez reguły alertów. |
| Wynikówwyszukiwania |"IncludeSearchResults": true|Rekordów zwróconych przez kwerendę jako tabelę JSON, ograniczona do pierwszych 1000 rekordów; Jeśli "IncludeSearchResults": true został dodany w niestandardowych definicji elementu webhook JSON jako właściwość najwyższego poziomu. |
| WorkspaceID |#workspaceid |Identyfikator obszaru roboczego analizy dzienników. |
| Identyfikator aplikacji |#applicationid |Identyfikator Twojej aplikacji szczegółowe informacje o aplikacji. |
| Identyfikator subskrypcji |#subscriptionid |Identyfikator subskrypcji platformy Azure używana z usługi Application Insights. 


Można na przykład określić następujące niestandardowy ładunek, który zawiera jeden parametr o nazwie *tekstu*.  Usługa, która wywołuje ten element webhook czy oczekiwano tego parametru.

```json

    {
        "text":"#alertrulename fired with #searchresultcount over threshold of #thresholdvalue."
    }
```
Ten przykładowy ładunek może rozpoznać przypominać następujące przy wysyłaniu do elementu webhook.

```json
    {
        "text":"My Alert Rule fired with 18 records over threshold of 10 ."
    }
```
Wszystkie zmienne w niestandardowych elementu webhook zawiera określony w obudowie JSON, takie jak "#searchinterval", wynikowe elementu webhook ma też danych zmiennej wewnątrz obudowy, takich jak "00: 05:00".

Aby dołączyć wyniki wyszukiwania niestandardowy ładunek, upewnij się, że **IncudeSearchResults** jest ustawiony jako właściwość najwyższego poziomu w ładunku json. 

## <a name="sample-payloads"></a>Ładunki próbki
W tej sekcji przedstawiono przykładowy ładunek dla elementu webhook dla dziennika alertów, włączając podczas ładunku jest standardowe i niestandardowe jej.

> [!NOTE]
> Aby zapewnić zgodność z poprzednimi wersjami, jest taka sama jak ładunek standardowego elementu webhook dla alertów za pomocą usługi Analiza dzienników Azure [analizy dzienników alertów zarządzania](../log-analytics/log-analytics-alerts-creating.md). Nawet w przypadku alertów dziennika za pomocą [usługi Application Insights](../application-insights/app-insights-analytics.md), ładunku standardowego elementu webhook jest oparta na schemat grupy działań.

### <a name="standard-webhook-for-log-alerts"></a>Standardowa elementu Webhook dla dziennika alertów 
Oba te przykłady zostały podane fikcyjny ładunku z tylko dwa kolumnami i wierszami.

#### <a name="log-alert-for-azure-log-analytics"></a>Alert dziennika dla usługi Analiza dzienników Azure
Poniżej przedstawiono ładunku przykładowe działania standardowe elementu webhook *bez niestandardowych opcji Json* używany przez alerty na podstawie analizy dziennika.

```json
{
    "WorkspaceId":"12345a-1234b-123c-123d-12345678e",
    "AlertRuleName":"AcmeRule","SearchQuery":"search *",
    "SearchResult":
        {
        "tables":[
                    {"name":"PrimaryResult","columns":
                        [
                        {"name":"$table","type":"string"},
                        {"name":"Id","type":"string"},
                        {"name":"TimeGenerated","type":"datetime"}
                        ],
                    "rows":
                        [
                            ["Fabrikam","33446677a","2018-02-02T15:03:12.18Z"],
                            ["Contoso","33445566b","2018-02-02T15:16:53.932Z"]
                        ]
                    }
                ]
        },
    "SearchIntervalStartTimeUtc": "2018-03-26T08:10:40Z",
    "SearchIntervalEndtimeUtc": "2018-03-26T09:10:40Z",
    "AlertThresholdOperator": "Greater Than",
    "AlertThresholdValue": 0,
    "ResultCount": 2,
    "SearchIntervalInSeconds": 3600,
    "LinkToSearchResults": "https://workspaceID.portal.mms.microsoft.com/#Workspace/search/index?_timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
    "Description": null,
    "Severity": "Warning"
 }
 ```   

#### <a name="log-alert-for-azure-application-insights"></a>Alert dziennika dla usługi Azure Application Insights
Oto ładunku próbki dla elementu webhook standardowe *bez niestandardowych opcji Json* używanego dla dziennika alerty na podstawie szczegółowych informacji aplikacji.
    
```json
{
    "schemaId":"Microsoft.Insights/LogAlert","data":
    { 
    "SubscriptionId":"12345a-1234b-123c-123d-12345678e",
    "AlertRuleName":"AcmeRule","SearchQuery":"search *",
    "SearchResult":
        {
        "tables":[
                    {"name":"PrimaryResult","columns":
                        [
                        {"name":"$table","type":"string"},
                        {"name":"Id","type":"string"},
                        {"name":"TimeGenerated","type":"datetime"}
                        ],
                    "rows":
                        [
                            ["Fabrikam","33446677a","2018-02-02T15:03:12.18Z"],
                            ["Contoso","33445566b","2018-02-02T15:16:53.932Z"]
                        ]
                    }
                ]
        },
    "SearchIntervalStartTimeUtc": "2018-03-26T08:10:40Z",
    "SearchIntervalEndtimeUtc": "2018-03-26T09:10:40Z",
    "AlertThresholdOperator": "Greater Than",
    "AlertThresholdValue": 0,
    "ResultCount": 2,
    "SearchIntervalInSeconds": 3600,
    "LinkToSearchResults": "https://analytics.applicationinsights.io/subscriptions/12345a-1234b-123c-123d-12345678e/?query=search+*+&timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
    "Description": null,
    "Severity": "Error",
    "ApplicationId": "123123f0-01d3-12ab-123f-abc1ab01c0a1"
    }
}
```

#### <a name="log-alert-with-custom-json-payload"></a>Alert dziennika z niestandardowy ładunek JSON
Na przykład aby utworzyć niestandardowy ładunek, zawierający tylko nazwę alertu i wyniki wyszukiwania, można użyć następujących czynności: 

```json
    {
       "alertname":"#alertrulename",
       "IncludeSearchResults":true
    }
```

Poniżej przedstawiono przykładowe ładunku dla akcji niestandardowej elementu webhook dla wszystkich alertów dziennika.
    
```json
    {
    "alertname":"AcmeRule","IncludeSearchResults":true,
    "SearchResult":
        {
        "tables":[
                    {"name":"PrimaryResult","columns":
                        [
                        {"name":"$table","type":"string"},
                        {"name":"Id","type":"string"},
                        {"name":"TimeGenerated","type":"datetime"}
                        ],
                    "rows":
                        [
                            ["Fabrikam","33446677a","2018-02-02T15:03:12.18Z"],
                            ["Contoso","33445566b","2018-02-02T15:16:53.932Z"]
                        ]
                    }
                ]
        }
    }
```


## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o [rejestrowania alertów w alertach Azure ](monitor-alerts-unified-log.md)
- Tworzenie i zarządzanie nimi [grupy akcji na platformie Azure](monitoring-action-groups.md)
- Dowiedz się więcej o [usługi Application Insights](../application-insights/app-insights-analytics.md)
- Dowiedz się więcej o [analizy dzienników](../log-analytics/log-analytics-overview.md). 