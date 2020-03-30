---
title: Akcje elementu Webhook dla alertów dziennika w alertach platformy Azure
description: W tym artykule opisano sposób tworzenia reguły alertów dziennika przy użyciu obszaru roboczego usługi Log Analytics lub usługi Application Insights, jak alert wypycha dane jako element webhook HTTP oraz szczegóły różnych dostosowań, które są możliwe.
author: yanivlavi
ms.author: yalavi
services: monitoring
ms.topic: conceptual
ms.date: 06/25/2019
ms.subservice: alerts
ms.openlocfilehash: 7b1956ad2bf9bf38ba9edc4c7234078557564071
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77667707"
---
# <a name="webhook-actions-for-log-alert-rules"></a>Akcje elementu Webhook dla reguł alertów dziennika
Po [utworzeniu alertu dziennika](alerts-log.md)na platformie Azure można [go skonfigurować przy użyciu grup akcji](action-groups.md) w celu wykonania jednej lub kilku akcji. W tym artykule opisano różne akcje elementu webhook, które są dostępne i pokazano, jak skonfigurować niestandardowy element webhook oparty na JSON.

> [!NOTE]
> Można również użyć [wspólnego schematu alertów](https://aka.ms/commonAlertSchemaDocs) dla integracji elementu webhook. Wspólny schemat alertów zapewnia tę zaletę, że ma jeden rozszerzalny i ujednolicony ładunek alertów we wszystkich usługach alertów w usłudze Azure Monitor.Należy pamiętać, że wspólny schemat alertów nie jest honorowany niestandardowej opcji JSON dla alertów dziennika. Jest on odracza do wspólnego ładunku schematu alertu, jeśli jest zaznaczona niezależnie od dostosowania, które mogły być wykonane na poziomie reguły alertu. [Dowiedz się więcej o definicji schematów alertów.](https://aka.ms/commonAlertSchemaDefinitions)

## <a name="webhook-actions"></a>Akcje elementów webhook

Za pomocą akcji webhook można wywołać proces zewnętrzny za pośrednictwem jednego żądania HTTP POST. Usługa, która jest wywoływana powinna obsługiwać webhooks i określić, jak korzystać z ładunku odbiera.

Akcje elementu webhook wymagają właściwości w poniższej tabeli.

| Właściwość | Opis |
|:--- |:--- |
| **Webhook URL** |Adres URL elementu webhook. |
| **Niestandardowy ładunek JSON** |Niestandardowy ładunek do wysłania za pomocą elementu webhook, gdy ta opcja jest wybrana podczas tworzenia alertu. Aby uzyskać więcej informacji, zobacz [Zarządzanie alertami dziennika](alerts-log.md).|

> [!NOTE]
> Przycisk **Wyświetl element webhook** obok opcji **Dołącz niestandardowy ładunek JSON dla elementu webhook** dla alertu dziennika wyświetla przykładowy ładunek elementu webhook dla dostosowywania, który został dostarczony. Nie zawiera rzeczywistych danych, ale jest reprezentatywna dla schematu JSON, który jest używany do alertów dziennika. 

Elementy webhook zawierają adres URL i ładunek sformatowany w JSON, że dane wysyłane do usługi zewnętrznej. Domyślnie ładunek zawiera wartości w poniższej tabeli. Możesz zastąpić ten ładunek własnym ładunkiem. W takim przypadku należy użyć zmiennych w tabeli dla każdego z parametrów, aby uwzględnić ich wartości w ładunku niestandardowego.


| Parametr | Zmienna | Opis |
|:--- |:--- |:--- |
| *Nazwa alertu* |#alertrulename |Nazwa reguły alertu. |
| *Ważność* |#severity |Ustawiono ważność dla alertu odpalony dziennik. |
| *AlertThresholdOperator* |#thresholdoperator |Operator progu dla reguły alertu, która używa większa lub mniejsza niż. |
| *AlertThresholdValue* |#thresholdvalue |Wartość progowa dla reguły alertu. |
| *Wyniki LinkToSearch* |#linktosearchresults |Łącze do portalu Analytics, który zwraca rekordy z kwerendy, która utworzyła alert. |
| *WynikCount* |#searchresultcount |Liczba rekordów w wynikach wyszukiwania. |
| *Czas zakończenia interwału wyszukiwania* |#searchintervalendtimeutc |Czas zakończenia kwerendy w formacie UTC, z formatem mm/dd/yyyy HH:mm:ss AM/PM. |
| *Interwał wyszukiwania* |#searchinterval |Przedział czasu dla reguły alertu z formatem HH:mm:ss. |
| *Czas rozpoczęcia interwału wyszukiwania* |#searchintervalstarttimeutc |Godzina rozpoczęcia kwerendy w formacie UTC, w formacie mm/dd/yyyy HH:mm:ss AM/PM. 
| *Kwerenda wyszukiwania* |#searchquery |Kwerenda wyszukiwania dziennika używana przez regułę alertu. |
| *Wyniki wyszukiwania* |"IncludeSearchResults": prawda|Rekordy zwracane przez kwerendę jako tabela JSON, ograniczone do pierwszych 1000 rekordów, jeśli "IncludeSearchResults": true jest dodawany w niestandardowej definicji elementu webhook JSON jako właściwość najwyższego poziomu. |
| *Typ alertu*| #alerttype | Typ reguły alertu dziennika skonfigurowany jako [pomiar metryki](alerts-unified-log.md#metric-measurement-alert-rules) lub [Liczba wyników](alerts-unified-log.md#number-of-results-alert-rules).|
| *Obszar roboczyID* |#workspaceid |Identyfikator obszaru roboczego usługi Log Analytics. |
| *Identyfikator aplikacji* |#applicationid |Identyfikator aplikacji usługi Application Insights. |
| *Subscription ID (Identyfikator subskrypcji)* |#subscriptionid |Identyfikator używanej subskrypcji platformy Azure. 

> [!NOTE]
> *LinkToSearchResults* przekazuje parametry, takie jak *SearchQuery*, *Search Interval StartTime*i *Czas zakończenia interwału wyszukiwania* w adresie URL do witryny Azure portal do przeglądania w sekcji Analytics. Portal Azure ma limit rozmiaru identyfikatora URI wynoszący około 2000 znaków. Portal *nie* otworzy łączy podanych w alertach, jeśli wartości parametrów przekroczą limit. Możesz ręcznie wprowadzić szczegóły, aby wyświetlić wyniki w portalu Analytics. Można też użyć [interfejsu API REST usługi Application Analytics lub](https://dev.applicationinsights.io/documentation/Using-the-API) interfejsu API REST usługi analizy [dziennika,](/rest/api/loganalytics/) aby programowo pobierać wyniki. 

Na przykład można określić następujący ładunek niestandardowy, który zawiera pojedynczy parametr o nazwie *tekst*. Usługa, która wywołuje ten element webhook oczekuje tego parametru.

```json

    {
        "text":"#alertrulename fired with #searchresultcount over threshold of #thresholdvalue."
    }
```
W tym przykładzie ładunek rozwiązuje się z czymś następującym, gdy jest wysyłany do elementu webhook:

```json
    {
        "text":"My Alert Rule fired with 18 records over threshold of 10 ."
    }
```
Ponieważ wszystkie zmienne w niestandardowym elementów webhook muszą być określone w obudowie JSON, takich jak "#searchinterval", wynikowy element webhook ma również zmienne dane wewnątrz obudów, takich jak "00:05:00."

Aby uwzględnić wyniki wyszukiwania w ładunku niestandardowego, upewnij się, że **IncludeSearchResults** jest ustawiona jako właściwość najwyższego poziomu w ładunku JSON. 

## <a name="sample-payloads"></a>Przykładowe ładunki
W tej sekcji przedstawiono przykładowe ładunki dla zestawów webhook dla alertów dziennika. Przykładowe ładunki obejmują przykłady, gdy ładunek jest standardowy i gdy jest niestandardowy.

### <a name="standard-webhook-for-log-alerts"></a>Standardowy element webhook dla alertów dziennika 
Oba te przykłady mają atrapę ładunku tylko dwie kolumny i dwa wiersze.

#### <a name="log-alert-for-log-analytics"></a>Alert dziennika dla usługi Log Analytics
Następująca próbka ładunku jest dla standardowej akcji elementu webhook *bez niestandardowej opcji JSON,* która jest używana dla alertów opartych na usłudze Log Analytics:

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
> Wartość pola "Ważność" może ulec zmianie, jeśli [zmienisz preferencję interfejsu API](alerts-log-api-switch.md) dla alertów dziennika w usłudze Log Analytics.


#### <a name="log-alert-for-application-insights"></a>Alert dziennika dla usługi Application Insights
Następująca próbka ładunku jest dla standardowego elementu webhook *bez niestandardowej opcji JSON,* gdy jest on używany do alertów dziennika opartych na usłudze Application Insights:
    
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

#### <a name="log-alert-with-custom-json-payload"></a>Alert dziennika z niestandardowym ładunkiem JSON
Na przykład, aby utworzyć ładunek niestandardowy, który zawiera tylko nazwę alertu i wyniki wyszukiwania, można użyć następujących czynności: 

```json
    {
       "alertname":"#alertrulename",
       "IncludeSearchResults":true
    }
```

Następująca próbka ładunku jest dla niestandardowej akcji elementu webhook dla dowolnego alertu dziennika:
    
```json
    {
    "alertname":"AcmeRule","IncludeSearchResults":true,
    "SearchResults":
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
- Dowiedz się więcej o [alertach dziennika w alertach platformy Azure](alerts-unified-log.md).
- Dowiedz się, jak [zarządzać alertami dziennika na platformie Azure](alerts-log.md).
- Tworzenie grup akcji i zarządzanie nimi [na platformie Azure](action-groups.md).
- Dowiedz się więcej o [usłudze Application Insights](../../azure-monitor/app/analytics.md).
- Dowiedz się więcej o [zapytaniach dziennika](../log-query/log-query-overview.md). 

