---
title: Akcje elementu Webhook dla alertów dziennika w alertach Azure
description: W tym artykule opisano sposób tworzenia reguł alertów dzienników przy użyciu obszaru roboczego usługi Log Analytics lub usługi Application Insights, jak alert przekazujący dane jako element webhook protokołu HTTP i szczegółowe informacje o różnych dostosowania, które są możliwe.
author: msvijayn
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: vinagara
ms.subservice: alerts
ms.openlocfilehash: 6aa007c621e76cb0c188a7dab6279fd9e387b2b3
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67705189"
---
# <a name="webhook-actions-for-log-alert-rules"></a>Akcje elementu Webhook dla reguł alertów dzienników
Gdy [alertu dziennika jest tworzony na platformie Azure](alerts-log.md), masz możliwość [jego konfigurowania przy użyciu grup akcji](action-groups.md) przeprowadzić co najmniej jednej akcji. Ten artykuł zawiera opis akcji różnych elementów webhook, które są dostępne i pokazuje, jak skonfigurować niestandardowe elementu webhook opartych na formacie JSON.

> [!NOTE]
> Możesz również użyć [wspólny schemat alertu](https://aka.ms/commonAlertSchemaDocs) usługi integracji elementu webhook. Wspólny schemat alertu zawiera zaletą jednym ładunku alertu rozszerzalny i ujednolicone we wszystkich usługach alertu w usłudze Azure Monitor. [Więcej informacji na temat wspólnej definicji schematów alertu.](https://aka.ms/commonAlertSchemaDefinitions)

## <a name="webhook-actions"></a>Akcje elementu Webhook

Za pomocą akcji elementów webhook możesz wywołać procesu zewnętrznego za pośrednictwem pojedynczego żądania HTTP POST. Usługa, która jest wywoływana należy Obsługa elementów webhook i określić, jak używać dowolnej ładunku otrzymywanych.

Akcje elementu Webhook wymagają właściwości w poniższej tabeli.

| Właściwość | Opis |
|:--- |:--- |
| **Adres URL elementu Webhook** |Adres URL elementu webhook. |
| **Niestandardowy ładunek JSON** |Niestandardowy ładunek do wysyłania za pomocą elementu webhook, gdy ta opcja zostanie wybrana podczas tworzenia alertu. Aby uzyskać więcej informacji, zobacz [alertów dzienników Zarządzaj](alerts-log.md).|

> [!NOTE]
> **Widoku elementu Webhook** przycisk obok **Uwzględnij niestandardowy ładunek JSON dla elementu webhook** opcję dla alertu dziennika zawiera przykładowy ładunek elementu webhook do dostosowania, który został dostarczony. Nie zawiera rzeczywistych danych, ale jest reprezentatywna schematu JSON, który jest używany w przypadku alertów dzienników. 

Elementy Webhook obejmują adres URL i ładunek zapisany w formacie JSON, że dane wysłane do usługi zewnętrznej. Domyślny ładunek zawiera wartości w poniższej tabeli. Można zastąpić ten ładunek niestandardowe samodzielnie. W takim przypadku należy użyć zmiennych w tabeli dla każdego z parametrów ich wartości mają zostać uwzględnione w niestandardowy ładunek.


| Parametr | Zmienna | Opis |
|:--- |:--- |:--- |
| *AlertRuleName* |#alertrulename |Nazwa reguły alertu. |
| *Ważność* |#severity |Ważność ustawić alert wyzwolenia dziennika. |
| *AlertThresholdOperator* |#thresholdoperator |Operator progu dla reguły alertu, który używa większe lub mniejsze niż. |
| *AlertThresholdValue* |#thresholdvalue |Wartość progowa dla reguły alertu. |
| *LinkToSearchResults* |#linktosearchresults |Link do portalu analiza, które zwraca rekordy kwerendę, która utworzyła alert. |
| *ResultCount* |#searchresultcount |Liczba rekordów w wynikach wyszukiwania. |
| *Czas zakończenia interwał wyszukiwania* |#searchintervalendtimeutc |Czas zakończenia dla zapytania w formacie UTC, z formatem mm/dd/rrrr gg: mm: ss AM/PM. |
| *Interwał wyszukiwania* |#searchinterval |Przedział czasu dla reguły alertu, w formacie: mm: ss. |
| *Godzina rozpoczęcia interwału wyszukiwania* |#searchintervalstarttimeutc |Godzina rozpoczęcia dla zapytania w formacie UTC, z formatem mm/dd/rrrr gg: mm: ss AM/PM. 
| *SearchQuery* |#searchquery |Zapytanie wyszukiwania w dzienniku używane przez reguły alertu. |
| *SearchResults* |"IncludeSearchResults": true|Rekordów zwróconych przez zapytanie w postaci tabeli JSON, ograniczona do pierwszych 1000 rekordów, jeśli "IncludeSearchResults": true zostanie dodany do niestandardowych definicji elementu webhook JSON jako właściwość najwyższego poziomu. |
| *Typ alertu*| #alerttype | Typ reguł alertów dzienników, skonfigurowany jako [pomiar metryki](alerts-unified-log.md#metric-measurement-alert-rules) lub [liczba wyników](alerts-unified-log.md#number-of-results-alert-rules).|
| *WorkspaceID* |#workspaceid |Identyfikator obszaru roboczego usługi Log Analytics. |
| *Identyfikator aplikacji* |#applicationid |Identyfikator usługi Application Insights aplikacji. |
| *Subscription ID (Identyfikator subskrypcji)* |#subscriptionid |Identyfikator subskrypcji platformy Azure używane. 

> [!NOTE]
> *LinkToSearchResults* przekazuje parametry, takie jak *SearchQuery*, *StartTime interwał wyszukiwania*, i *czas zakończenia interwału wyszukiwania* w adresie URL na platformie Azure Portal pod kątem wyświetlania w sekcji analizy. Witryna Azure portal ma limit rozmiaru identyfikatora URI to około 2000 znaków. Portal będzie *nie* otwieranie linków dostępnych w alertach, jeśli wartości parametrów przekracza limit. Można ręcznie wprowadź szczegóły, aby wyświetlić wyniki w portalu usługi analiza. Alternatywnie można użyć [interfejsu API REST Application Insights Analytics](https://dev.applicationinsights.io/documentation/Using-the-API) lub [interfejsu API REST usługi Log Analytics](/rest/api/loganalytics/) programowo pobrać wyniki. 

Na przykład można określić następujące niestandardowy ładunek, który zawiera jeden parametr o nazwie *tekstu*. Ten parametr oczekuje, że usługa, która wywołuje tego elementu webhook.

```json

    {
        "text":"#alertrulename fired with #searchresultcount over threshold of #thresholdvalue."
    }
```
Ten przykładowy ładunek jest rozpoznawana jako podobną do następującej po wysłaniu do elementu webhook:

```json
    {
        "text":"My Alert Rule fired with 18 records over threshold of 10 ."
    }
```
Ponieważ wszystkie zmienne w niestandardowych elementów webhook, muszą być określone w obudowie JSON, takich jak "#searchinterval," wynikowy element webhook ma również danych zmiennej wewnątrz obudów, takie jak "00: 05:00."

Aby dołączyć wyniki wyszukiwania niestandardowy ładunek, upewnij się, że **IncludeSearchResults** jest ustawiony jako właściwości najwyższego poziomu w ładunku JSON. 

## <a name="sample-payloads"></a>Przykładowych ładunków
W tej sekcji przedstawiono przykładowe ładunków dla elementy webhook dla alertów dzienników. Ładunki przykładowe obejmują przykłady po ładunek, standard i kiedy jest niestandardowy.

### <a name="standard-webhook-for-log-alerts"></a>Standardowa elementu webhook dla dziennika alertów 
Obu tych przykładach mają fikcyjnego ładunku z tylko dwie kolumny i dwa wiersze.

#### <a name="log-alert-for-log-analytics"></a>Alert dziennika dla usługi Log Analytics
Poniższy przykładowy ładunek jest akcją elementu webhook standardowa *bez niestandardowych opcji JSON* używanego do obsługi alerty oparte na usłudze Log Analytics:

```json
{
    "SubscriptionId":"12345a-1234b-123c-123d-12345678e",
    "AlertRuleName":"AcmeRule",
    "SearchQuery":"Perf | where ObjectName == \"Processor\" and CounterName == \"% Processor Time\" | summarize AggregatedValue = avg(CounterValue) by bin(TimeGenerated, 5m), Computer",
    "SearchIntervalStartTimeUtc": "2018-03-26T08:10:40Z",
    "SearchIntervalEndtimeUtc": "2018-03-26T09:10:40Z",
    "AlertThresholdOperator": "Greater Than",
    "AlertThresholdValue": 0,
    "ResultCount": 2,
    "SearchIntervalInSeconds": 3600,
    "LinkToSearchResults": "https://portal.azure.com/#Analyticsblade/search/index?_timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
    "Description": "log alert rule",
    "Severity": "Warning",
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
    "WorkspaceId":"12345a-1234b-123c-123d-12345678e",
    "AlertType": "Metric measurement"
 }
 ```

> [!NOTE]
> Wartość pola "Waga" mogą ulec zmianie, jeśli masz [przełączona z preferencjami API](alerts-log-api-switch.md) dla dziennika alertów w usłudze Log Analytics.


#### <a name="log-alert-for-application-insights"></a>Alert dziennika dla usługi Application Insights
Poniższy przykładowy ładunek jest standardowa elementu webhook *bez niestandardowych opcji JSON* gdy jest używana dla dziennika alertów oparte na usłudze Application Insights:
    
```json
{
    "schemaId":"Microsoft.Insights/LogAlert","data":
    { 
    "SubscriptionId":"12345a-1234b-123c-123d-12345678e",
    "AlertRuleName":"AcmeRule",
    "SearchQuery":"requests | where resultCode == \"500\"",
    "SearchIntervalStartTimeUtc": "2018-03-26T08:10:40Z",
    "SearchIntervalEndtimeUtc": "2018-03-26T09:10:40Z",
    "AlertThresholdOperator": "Greater Than",
    "AlertThresholdValue": 0,
    "ResultCount": 2,
    "SearchIntervalInSeconds": 3600,
    "LinkToSearchResults": "https://portal.azure.com/AnalyticsBlade/subscriptions/12345a-1234b-123c-123d-12345678e/?query=search+*+&timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
    "Description": null,
    "Severity": "3",
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
    "ApplicationId": "123123f0-01d3-12ab-123f-abc1ab01c0a1",
    "AlertType": "Number of results"
    }
}
```

#### <a name="log-alert-with-custom-json-payload"></a>Alertu dziennika za pomocą niestandardowy ładunek JSON
Na przykład aby utworzyć niestandardowy ładunek, który zawiera tylko nazwę alertu i wyniki wyszukiwania, służy następujące czynności: 

```json
    {
       "alertname":"#alertrulename",
       "IncludeSearchResults":true
    }
```

Poniższy przykładowy ładunek jest akcją elementu webhook niestandardowego dowolnego alertu dziennika:
    
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
- Dowiedz się więcej o [alerty dzienników w alertach Azure](alerts-unified-log.md).
- Zrozumienie sposobu [Zarządzanie alertami dziennika na platformie Azure](alerts-log.md).
- Tworzenie i zarządzanie nimi [grup akcji na platformie Azure](action-groups.md).
- Dowiedz się więcej o [usługi Application Insights](../../azure-monitor/app/analytics.md).
- Dowiedz się więcej o [rejestrowania zapytań](../log-query/log-query-overview.md). 

