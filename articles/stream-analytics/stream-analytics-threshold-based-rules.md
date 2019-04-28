---
title: Zasady można konfigurować na podstawie progów procesu w usłudze Azure Stream Analytics
description: W tym artykule opisano, jak użyć danych referencyjnych do osiągnięcia rozwiązanie alertów, który ma skonfigurowaną wartość progową, na podstawie reguł w usłudze Azure Stream Analytics.
services: stream-analytics
author: rockboyfor
ms.author: v-yeche
manager: digimobile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
origin.date: 04/30/2018
ms.date: 08/20/2018
ms.openlocfilehash: ce2cf6ebdfd74549114e94e4c7356e387576d3c8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60761730"
---
# <a name="process-configurable-threshold-based-rules-in-azure-stream-analytics"></a>Proces można skonfigurować zasady oparte na wartościach progowych w usłudze Azure Stream Analytics
W tym artykule opisano, jak użyć danych referencyjnych do osiągnięcia rozwiązanie alertów, który używa można skonfigurować zasady oparte na wartościach progowych w usłudze Azure Stream Analytics.

## <a name="scenario-alerting-based-on-adjustable-rule-thresholds"></a>Scenariusz: Alerty na podstawie zmienianych reguły progów
Może być konieczne generuje alert, ponieważ dane wyjściowe, gdy zdarzenia przychodzące przesyłane strumieniowo osiągnięcia określonej wartości lub w przypadku, gdy zagregowane wartości na podstawie przychodzącego strumienia zdarzeń przekroczy określony próg. Prosta konfiguracja zapytania usługi Stream Analytics, które porównywana wartość statyczny próg, który nie jest stała i uprzednio określonym go. Stały próg mogą być zakodowane do przesyłania strumieniowego składnia zapytania przy użyciu prostego porównanie numeryczne (większe niż, mniejsze i równości).

W niektórych przypadkach wartości progowe muszą być łatwiej można skonfigurować bez konieczności edytowania składnia zapytania za każdym razem, zmienia wartość progową. W innych przypadkach może być konieczne wiele urządzeń lub użytkowników przetwarzane przez tego samego zapytania z każdym z nich o wartości innej wartości progowej, dla każdego rodzaju urządzenia. 

Ten wzorzec może służyć do dynamicznego skonfigurowania progów, selektywnie wskazać jakiego rodzaju urządzenia, wartość progową, który ma zastosowanie, filtrując dane wejściowe i selektywnie wskazać pola do uwzględnienia w danych wyjściowych.

## <a name="recommended-design-pattern"></a>Zalecany wzorzec projektowania
Użyj wejściowych danych referencyjnych do zadania usługi Stream Analytics jako odnośnik progi alertów:
- Store wartości progowe w danych referencyjnych, jedna wartość na klucz.
- Dołącz do przesyłania strumieniowego danych zdarzeń wejściowych z danymi referencyjnymi w kolumnie klucza.
- Jako wartość progowa, należy użyć wartości kluczem z danych referencyjnych.

## <a name="example-data-and-query"></a>Przykładowe dane i zapytania
W tym przykładzie alerty są generowane, gdy jest zgodna z agregowania danych przesyłanych strumieniowo z urządzeń, w oknie minutę długich wartości określone w regule, podana jako dane referencyjne.

W zapytaniu każdy identyfikator urządzenia i każdej metricName pod identyfikatorem urządzenia można skonfigurować z zakresu od 0 do 5 wymiarów GROUP BY. Tylko zdarzenia o odpowiedniej wartości filtru są zgrupowane. Po zgrupowane, agregacji w trybie okna Min, Max, Avg, są obliczane przez okno wirowania 60 sekund. Z wartości zagregowanych filtry, które następnie są obliczane dla każdego skonfigurowanego progu w dokumentacji, aby wygenerować zdarzenie wyjściowe alertu.

Na przykład załóżmy, istnieje zadanie usługi Stream Analytics, które ma wejściowych danych referencyjnych, o nazwie **reguły**i przesyłanie strumieniowe danych wejściowych o nazwie **metryki**. 

## <a name="reference-data"></a>Dane referencyjne
Tymi danymi referencyjnymi w przykładzie pokazano, jak mogą być reprezentowane reguły oparte na wartościach progowych. Plik JSON zawiera dane referencyjne i zostanie zapisany w usłudze Azure blob storage i tego kontenera magazynu obiektów blob jest używany jako dane wejściowe odwołanie do danych o nazwie **reguły**. Można zastąpić ten plik JSON, a następnie zastąpić konfigurację reguły, gdy czas, bez zatrzymywania i uruchamiania zadania przesyłania strumieniowego.

- Przykładowa reguła jest używana do reprezentowania zmienianych alertu, gdy przekroczy procesora CPU (średnio jest większa niż lub równe) wartość `90` procent. `value` Pole jest konfigurowalne, zgodnie z potrzebami.
- Zwróć uwagę, reguła ma **operator** pola, które są dynamicznie jest interpretowany przy użyciu składni zapytań na później `AVGGREATEROREQUAL`. 
- Reguła filtruje dane klucza wymiaru `2` wartością `C1`. Inne pola są pusty ciąg, wskazujący na nie, aby filtrować strumień wejściowy według tych pól zdarzeń. Aby filtrować inne pola dopasowania, zgodnie z potrzebami, można skonfigurować dodatkowe reguły procesora CPU.
- Nie wszystkie kolumny są mają zostać uwzględnione w zdarzeniu alertu w danych wyjściowych. W tym przypadku `includedDim` klucza numer `2` jest włączona `TRUE` do reprezentowania, że w zdarzenia kwalifikujące dane wyjściowe zostaną uwzględnione numer pola 2 danych zdarzenia w strumieniu. Inne pola nie są uwzględnione w alertach danych wyjściowych, ale można dostosować listę pól.

```json
{
    "ruleId": 1234, 
    "deviceId" : "978648", 
    "metricName": "CPU", 
    "alertName": "hot node AVG CPU over 90",
    "operator" : "AVGGREATEROREQUAL",
    "value": 90, 
    "includeDim": {
        "0": "FALSE", 
        "1": "FALSE", 
        "2": "TRUE", 
        "3": "FALSE", 
        "4": "FALSE"
    },
    "filter": {
        "0": "", 
        "1": "",
        "2": "C1", 
        "3": "", 
        "4": ""
    }    
}
```

## <a name="example-streaming-query"></a>Przykładowe zapytanie przesyłania strumieniowego
To przykładowe zapytanie usługi Stream Analytics łączy **reguły** dane referencyjne w przykładzie powyżej, aby strumień wejściowy danych o nazwie **metryki**.

```sql
WITH transformedInput AS
(
    SELECT
        dim0 = CASE rules.includeDim.[0] WHEN 'TRUE' THEN metrics.custom.dimensions.[0].value ELSE NULL END,
        dim1 = CASE rules.includeDim.[1] WHEN 'TRUE' THEN metrics.custom.dimensions.[1].value ELSE NULL END,
        dim2 = CASE rules.includeDim.[2] WHEN 'TRUE' THEN metrics.custom.dimensions.[2].value ELSE NULL END,
        dim3 = CASE rules.includeDim.[3] WHEN 'TRUE' THEN metrics.custom.dimensions.[3].value ELSE NULL END,
        dim4 = CASE rules.includeDim.[4] WHEN 'TRUE' THEN metrics.custom.dimensions.[4].value ELSE NULL END,
        metric = metrics.metric.value,
        metricName = metrics.metric.name,
        deviceId = rules.deviceId, 
        ruleId = rules.ruleId, 
        alertName = rules.alertName,
        ruleOperator = rules.operator, 
        ruleValue = rules.value
    FROM 
        metrics
        timestamp by eventTime
    JOIN 
        rules
        ON metrics.deviceId = rules.deviceId AND metrics.metric.name = rules.metricName
    WHERE
        (rules.filter.[0] = '' OR metrics.custom.filters.[0].value = rules.filter.[0]) AND 
        (rules.filter.[1] = '' OR metrics.custom.filters.[1].value = rules.filter.[1]) AND
        (rules.filter.[2] = '' OR metrics.custom.filters.[2].value = rules.filter.[2]) AND
        (rules.filter.[3] = '' OR metrics.custom.filters.[3].value = rules.filter.[3]) AND
        (rules.filter.[4] = '' OR metrics.custom.filters.[4].value = rules.filter.[4])
)

SELECT
    System.Timestamp as time, 
    transformedInput.deviceId as deviceId,
    transformedInput.ruleId as ruleId,
    transformedInput.metricName as metric,
    transformedInput.alertName as alert,
    AVG(metric) as avg,
    MIN(metric) as min, 
    MAX(metric) as max, 
    dim0, dim1, dim2, dim3, dim4
FROM
    transformedInput
GROUP BY
    transformedInput.deviceId,
    transformedInput.ruleId,
    transformedInput.metricName,
    transformedInput.alertName,
    dim0, dim1, dim2, dim3, dim4,
    ruleOperator, 
    ruleValue, 
    TumblingWindow(second, 60)
HAVING
    (
        (ruleOperator = 'AVGGREATEROREQUAL' AND avg(metric) >= ruleValue) OR
        (ruleOperator = 'AVGEQUALORLESS' AND avg(metric) <= ruleValue) 
    )
```

## <a name="example-streaming-input-event-data"></a>Przykład przesyłania strumieniowego danych zdarzenia wejściowe
W tym przykładzie dane JSON reprezentuje **metryki** wprowadzanie danych, który jest używany w powyższym zapytaniu przesyłania strumieniowego. 

- Trzy przykłady zdarzeń są wyświetlane w ciągu 1 minuty przedział czasu, wartość `T14:50`. 
- Wszystkie trzy mają taką samą `deviceId` wartość `978648`.
- Wartości metryk użycia Procesora różnią się w każdym przypadku `98`, `95`, `80` odpowiednio. Tylko pierwsze dwa przykładowe zdarzenia przekracza procesora CPU reguły alertów ustalone w regule.
- Pole includeDim w regule alertu został klucza liczby 2. Nosi nazwę odpowiedniego pola klucza 2 w takie zdarzenia `NodeName`. Trzy przykłady zdarzeń mają wartości `N024`, `N024`, i `N014` odpowiednio. W danych wyjściowych widać tylko węzeł `N024` jak jest wyłącznie dane, które spełnia kryteria alertu dla wysokie użycie procesora CPU. `N014` nie spełnia górna wartość progowa procesora CPU.
- Reguła alertu jest skonfigurowany przy użyciu `filter` tylko na klucza numer 2, który odpowiada `cluster` pola w zdarzeniach próbki. Wszystkie zdarzenia przykład trzech mają wartość `C1` i pasował do kryteriów filtrowania.

```json
{
    "eventTime": "2018-04-30T14:50:23.1324132Z",
    "deviceId": "978648",
    "custom": {
        "dimensions": {
            "0": {
                "name": "NodeType",
                "value": "N1"
            },
            "1": {
                "name": "Cluster",
                "value": "C1"
            },
            "2": {
                "name": "NodeName",
                "value": "N024"
            }
        },
        "filters": {
            "0": {
                "name": "application",
                "value": "A1"
            },
            "1": {
                "name": "deviceType",
                "value": "T1"
            },
            "2": {
                "name": "cluster",
                "value": "C1"
            },
            "3": {
                "name": "nodeType",
                "value": "N1"
            }
        }
    },
    "metric": {
        "name": "CPU",
        "value": 98,
        "count": 1.0,
        "min": 98,
        "max": 98,
        "stdDev": 0.0
    }
}
{
    "eventTime": "2018-04-30T14:50:24.1324138Z",
    "deviceId": "978648",
    "custom": {
        "dimensions": {
            "0": {
                "name": "NodeType",
                "value": "N2"
            },
            "1": {
                "name": "Cluster",
                "value": "C1"
            },
            "2": {
                "name": "NodeName",
                "value": "N024"
            }
        },
        "filters": {
            "0": {
                "name": "application",
                "value": "A1"
            },
            "1": {
                "name": "deviceType",
                "value": "T1"
            },
            "2": {
                "name": "cluster",
                "value": "C1"
            },
            "3": {
                "name": "nodeType",
                "value": "N2"
            }
        }
    },
    "metric": {
        "name": "CPU",
        "value": 95,
        "count": 1,
        "min": 95,
        "max": 95,
        "stdDev": 0
    }
}
{
    "eventTime": "2018-04-30T14:50:37.1324130Z",
    "deviceId": "978648",
    "custom": {
        "dimensions": {
            "0": {
                "name": "NodeType",
                "value": "N3"
            },
            "1": {
                "name": "Cluster",
                "value": "C1 "
            },
            "2": {
                "name": "NodeName",
                "value": "N014"
            }
        },
        "filters": {
            "0": {
                "name": "application",
                "value": "A1"
            },
            "1": {
                "name": "deviceType",
                "value": "T1"
            },
            "2": {
                "name": "cluster",
                "value": "C1"
            },
            "3": {
                "name": "nodeType",
                "value": "N3"
            }
        }
    },
    "metric": {
        "name": "CPU",
        "value": 80,
        "count": 1,
        "min": 80,
        "max": 80,
        "stdDev": 0
    }
}
```

## <a name="example-output"></a>Przykładowe dane wyjściowe
W tym przykładzie danych wyjściowych, pokazuje dane JSON, pojedyncze zdarzenie alertu został utworzony na podstawie procesora CPU próg reguły zdefiniowane w danych referencyjnych. Zdarzenie wyjściowe zawiera nazwę alertu, a także zagregowane (wartość minimalna, maksymalna średni) pól uważane za. Dane wyjściowe dane zdarzenia zawierają pola klucza numer 2 `NodeName` wartość `N024` ze względu na konfigurację reguły. (Kod JSON został zmieniony w celu Pokaż podziałów wiersza, aby zwiększyć czytelność).

```JSON
{"time":"2018-05-01T02:03:00.0000000Z","deviceid":"978648","ruleid":1234,"metric":"CPU",
"alert":"hot node AVG CPU over 90","avg":96.5,"min":95.0,"max":98.0,
"dim0":null,"dim1":null,"dim2":"N024","dim3":null,"dim4":null}
```
<!--Update_Description: updat meta properties, wording update-->