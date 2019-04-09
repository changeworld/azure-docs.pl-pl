---
title: Akcje elementu Webhook dla alertów dziennika w alertach Azure
description: W tym artykule opisano, jak regułę alertu dziennika za pomocą log analytics obszar roboczy lub aplikacji szczegółowe informacje, będzie umożliwiać wypychanie powiadomień danych jako element webhook protokołu HTTP i szczegółowe informacje dotyczące różnych dostosowania możliwe.
author: msvijayn
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: vinagara
ms.subservice: alerts
ms.openlocfilehash: 2307fa985c88608d80400c8951c47b9f20caa1dc
ms.sourcegitcommit: 045406e0aa1beb7537c12c0ea1fbf736062708e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/04/2019
ms.locfileid: "59006621"
---
# <a name="webhook-actions-for-log-alert-rules"></a>Akcje elementu Webhook dla reguł alertów dzienników
Gdy [alertu dziennika jest tworzony na platformie Azure](alerts-log.md), masz możliwość [konfigurowanie przy użyciu grup akcji](action-groups.md) przeprowadzić co najmniej jednej akcji.  W tym artykule opisano akcji różnych elementów webhook, które są dostępne i szczegółowe informacje na temat konfigurowania niestandardowego elementu webhook opartych na formacie JSON.


## <a name="webhook-actions"></a>Akcje elementu Webhook

Akcje elementu Webhook umożliwiają wywołanie procesu zewnętrznego za pośrednictwem pojedynczego żądania HTTP POST.  Usługi wywołanego należy Obsługa elementów webhook i określić, jak używa dowolnej ładunku odbierze.    

Akcje elementu Webhook wymagają właściwości w poniższej tabeli:

| Właściwość | Opis |
|:--- |:--- |
| Adres URL elementu webhook |Adres URL elementu webhook. |
| Niestandardowy ładunek JSON |Niestandardowy ładunek do wysyłania za pomocą elementu webhook, po wybraniu tej opcji podczas tworzenia alertu. Szczegóły dostępne pod adresem [Zarządzaj alertów dzienników](alerts-log.md) |

> [!NOTE]
> Widok elementu Webhook przycisk obok *Uwzględnij niestandardowy ładunek JSON dla elementu webhook* opcja dla dziennika alertów, spowoduje wyświetlenie przykładowy ładunek elementu webhook do dostosowania podane. Nie zawiera rzeczywistych danych i przedstawiciel schematu JSON, używany w przypadku alertów dzienników. 

Elementy Webhook obejmują adres URL i ładunek zapisany w formacie JSON, które znajdują się dane wysyłane do usługi zewnętrznej.  Domyślnie ładunek zawiera wartości w tabeli poniżej:  Można zastąpić ten ładunek niestandardowe samodzielnie.  W takim przypadku służy zmiennych w tabeli dla każdego z parametrów do uwzględnienia ich wartości w niestandardowy ładunek.


| Parametr | Zmienna | Opis |
|:--- |:--- |:--- |
| AlertRuleName |#alertrulename |Nazwa reguły alertu. |
| Ważność |#severity |Ważność ustawić alert wyzwolenia dziennika. |
| AlertThresholdOperator |#thresholdoperator |Operator progu dla reguły alertu.  *Większa niż* lub *mniej niż*. |
| AlertThresholdValue |#thresholdvalue |Wartość progowa dla reguły alertu. |
| LinkToSearchResults |#linktosearchresults |Link do portalu analiza, które zwraca rekordy kwerendę, która utworzyła alert. |
| ResultCount |#searchresultcount |Liczba rekordów w wynikach wyszukiwania. |
| Czas zakończenia interwał wyszukiwania |#searchintervalendtimeutc |Czas zakończenia dla zapytania w formacie UTC, format - mm/dd/rrrr gg: mm: ss AM/PM. |
| Interwał wyszukiwania |#searchinterval |Reguła przedział czasu dla alertu, format —: mm: ss. |
| Godzina rozpoczęcia interwału wyszukiwania |#searchintervalstarttimeutc |Godzina rozpoczęcia dla zapytania w formacie UTC, format - mm/dd/rrrr gg: mm: ss AM/PM. 
| SearchQuery |#searchquery |Zapytanie wyszukiwania w dzienniku używane przez reguły alertu. |
| SearchResults |"IncludeSearchResults": true|Rekordów zwróconych przez zapytanie w postaci tabeli JSON, ograniczona do pierwszych 1000 rekordów; Jeśli "IncludeSearchResults": true zostanie dodany do niestandardowych definicji elementu webhook JSON jako właściwość najwyższego poziomu. |
| WorkspaceID |#workspaceid |Identyfikator obszaru roboczego usługi Log Analytics. |
| Identyfikator aplikacji |#applicationid |Identyfikator usługi Application Insights aplikacji. |
| Identyfikator subskrypcji |#subscriptionid |Identyfikator subskrypcji platformy Azure używane z usługą Application Insights. 

> [!NOTE]
> LinkToSearchResults przekazuje parametry, takie jak SearchQuery, StartTime interwał wyszukiwania i wyszukiwanie koniec interwału czasu w adresie URL do witryny Azure portal pod kątem wyświetlania w sekcji analizy. Witryna Azure portal udostępnia identyfikatora URI rozmiar wynoszący około 2000 znaków i zostanie *nie* Otwórz link podany w alertach, wartości parametrów przekroczy limit wymienionych. Użytkownicy mogą ręcznie wprowadź szczegóły, aby wyświetlić wyniki w portalu analiza, lub użyj [interfejsu API REST Application Insights Analytics](https://dev.applicationinsights.io/documentation/Using-the-API) lub [interfejsu API REST usługi Log Analytics](/rest/api/loganalytics/) programowo pobrać wyniki 

Na przykład można określić następujące niestandardowy ładunek, który zawiera jeden parametr o nazwie *tekstu*.  Usługa, która wywołuje ten element webhook będzie oczekiwano tego parametru.

```json

    {
        "text":"#alertrulename fired with #searchresultcount over threshold of #thresholdvalue."
    }
```
Ten przykładowy ładunek może prowadzić do podobną do następującej po wysłaniu do elementu webhook.

```json
    {
        "text":"My Alert Rule fired with 18 records over threshold of 10 ."
    }
```
Wszystkie zmienne w niestandardowych elementu webhook zawiera określone w obudowie JSON, takich jak "#searchinterval", wynikowy element webhook będzie miał danych zmiennej wewnątrz obudowy, takich jak "00: 05:00".

Aby dołączyć wyniki wyszukiwania niestandardowy ładunek, upewnij się, że **IncludeSearchResults** jest ustawiony jako właściwości najwyższego poziomu w ładunku json. 

## <a name="sample-payloads"></a>Przykładowych ładunków
W tej sekcji przedstawiono przykładowy ładunek elementu webhook dla dziennika alertów, w tym przypadku ładunek jest standardowa oraz datę jego niestandardowe.

### <a name="standard-webhook-for-log-alerts"></a>Standardowa elementu Webhook dla dziennika alertów 
Obu tych przykładów ma podać fikcyjny ładunku z jedynymi kolumnami i wierszami dwa.

#### <a name="log-alert-for-azure-log-analytics"></a>Alert dziennika dla platformy Azure usługi Log Analytics
Poniżej przedstawiono przykładowy ładunek działań standardowych elementów webhook *bez niestandardowych opcji Json* używanych na potrzeby alertów na podstawie analizy dzienników.

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

> [!NOTE]
> Wartość pola ważności mogą ulec zmianie, jeśli masz [przełączona z preferencjami API](alerts-log-api-switch.md) dla dziennika alertów w usłudze Log Analytics.


#### <a name="log-alert-for-azure-application-insights"></a>Alert dziennika dla usługi Azure Application Insights
Poniżej przedstawiono przykładowy ładunek standardowego elementu webhook *bez niestandardowych opcji Json* gdy jest używana dla dziennika alertów na podstawie szczegółowych informacji aplikacji.
    
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
    "Severity": "3",
    "ApplicationId": "123123f0-01d3-12ab-123f-abc1ab01c0a1"
    }
}
```

#### <a name="log-alert-with-custom-json-payload"></a>Alertu dziennika za pomocą niestandardowy ładunek JSON
Na przykład aby utworzyć niestandardowy ładunek, który zawiera tylko nazwę alertu i wyniki wyszukiwania, można użyć następujących czynności: 

```json
    {
       "alertname":"#alertrulename",
       "IncludeSearchResults":true
    }
```

Poniżej przedstawiono przykładowy ładunek dla akcji niestandardowej elementu webhook dla dowolnego alertu dziennika.
    
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


## <a name="next-steps"></a>Kolejne kroki
- Dowiedz się więcej o [alerty dzienników w alertów platformy Azure](alerts-unified-log.md)
- Zrozumienie [Zarządzanie alertami dziennika na platformie Azure](alerts-log.md)
- Tworzenie i zarządzanie nimi [grup akcji na platformie Azure](action-groups.md)
- Dowiedz się więcej o [usługi Application Insights](../../azure-monitor/app/analytics.md)
- Dowiedz się więcej o [rejestrowania zapytań](../log-query/log-query-overview.md). 

