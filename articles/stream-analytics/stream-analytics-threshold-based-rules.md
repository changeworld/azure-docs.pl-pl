---
title: Konfigurowalne reguły oparte na progach w usłudze Azure Stream Analytics
description: W tym artykule opisano, jak używać danych referencyjnych do osiągnięcia rozwiązania alertów, które ma konfigurowalne reguły oparte na progu w usłudze Azure Stream Analytics.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/30/2018
ms.openlocfilehash: 94fdddf11acb6763ed98a4b7e17304fbde0e25dd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75369715"
---
# <a name="process-configurable-threshold-based-rules-in-azure-stream-analytics"></a>Przetwarzanie konfigurowalnych reguł opartych na progach w usłudze Azure Stream Analytics
W tym artykule opisano, jak używać danych referencyjnych do uzyskania rozwiązania alertów, które używa konfigurowalnych reguł opartych na progach w usłudze Azure Stream Analytics.

## <a name="scenario-alerting-based-on-adjustable-rule-thresholds"></a>Scenariusz: Alerty oparte na regulowanych progach reguł
Może być konieczne uzyskanie alertu jako dane wyjściowe, gdy przychodzące przesyłane strumieniowo zdarzenia osiągnęły określoną wartość lub gdy zagregowana wartość oparta na przychodzących zdarzeniach przesyłanych strumieniowo przekracza określony próg. Można łatwo skonfigurować kwerendę usługi Stream Analytics, która porównała wartość z statycznym progiem, który jest stały i z góry określony. Stały próg może być zakodowany na stałe w składni zapytania przesyłania strumieniowego przy użyciu prostych porównań numerycznych (większa niż, mniej niż i równości).

W niektórych przypadkach wartości progowe muszą być łatwiej konfigurowalne bez edytowania składni kwerendy za każdym razem, gdy zmienia się wartość progowa. W innych przypadkach może być konieczne wiele urządzeń lub użytkowników przetworzonych przez tę samą kwerendę z każdego z nich o różnych wartości progowych na każdym rodzaju urządzenia. 

Ten wzorzec może służyć do dynamicznego konfigurowania progów, selektywnego wybierania rodzaju urządzenia, którego próg jest stosowany przez filtrowanie danych wejściowych, i selektywnego wybierania pól, które mają zostać uwzględnione w danych wyjściowych.

## <a name="recommended-design-pattern"></a>Zalecany wzór projektu
Użyj danych wejściowych odniesienia do zadania usługi Stream Analytics jako odnośnika progów alertów:
- Przechowuj wartości progowe w danych referencyjnych, po jednej wartości na klucz.
- Dołącz zdarzenia wejściowe danych przesyłania strumieniowego do danych referencyjnych w kolumnie klucza.
- Użyj wartości klucza z danych referencyjnych jako wartości progowej.

## <a name="example-data-and-query"></a>Przykładowe dane i kwerenda
W tym przykładzie alerty są generowane, gdy agregacja przesyłania strumieniowego danych z urządzeń w oknie minutowym odpowiada określonym wartościom w regule podanej jako dane referencyjne.

W kwerendzie dla każdego identyfikatora urządzenia i każdego identyfikatora metryki pod identyfikatorem deviceId można skonfigurować od 0 do 5 wymiarów do GRUPY WEDŁUG. Tylko zdarzenia o odpowiednich wartościach filtru są grupowane. Po zgrupowane, okna agregaty Min, Max, Avg, są obliczane w 60-sekundowym oknie tumbling. Filtry na zagregowane wartości są następnie obliczane zgodnie ze skonfigurowanym progiem w odwołaniu, aby wygenerować zdarzenie wyjściowe alertu.

Na przykład załóżmy, że istnieje zadanie usługi Stream Analytics, które ma dane referencyjne o nazwie **reguły**i przesyłanie strumieniowe danych wejściowych o nazwie **metryki**. 

## <a name="reference-data"></a>Dane referencyjne
W tym przykładzie dane referencyjne pokazuje, jak reguła oparta na progu może być reprezentowana. Plik JSON przechowuje dane referencyjne i jest zapisywany w magazynie obiektów blob platformy Azure, a ten kontener magazynu obiektów blob jest używany jako dane wejściowe danych referencyjnych o nazwie **reguły**. Można zastąpić ten plik JSON i zastąpić konfigurację reguły w miarę upływu czasu, bez zatrzymywania lub uruchamiania zadania przesyłania strumieniowego.

- Przykładowa reguła jest używana do reprezentowania regulowanego alertu, gdy procesor `90` CPU przekracza (średnia jest większa lub równa) procent wartości. Pole `value` można konfigurować w razie potrzeby.
- Zwróć uwagę, że reguła ma pole **operatora,** które jest dynamicznie interpretowane w składni kwerendy później `AVGGREATEROREQUAL`. 
- Reguła filtruje dane na określonym `2` kluczu wymiaru z wartością `C1`. Inne pola są pusty ciąg, co wskazuje, aby nie filtrować strumienia wejściowego przez te pola zdarzeń. W razie potrzeby można skonfigurować dodatkowe reguły procesora CPU w celu filtrowania innych pasujących pól.
- Nie wszystkie kolumny mają być uwzględnione w zdarzeniu alertu danych wyjściowych. W takim `includedDim` przypadku `2` numer klucza jest włączony `TRUE` w celu reprezentowania tego pola numer 2 danych zdarzeń w strumieniu zostaną uwzględnione w kwalifikujących się zdarzeniach wyjściowych. Pozostałe pola nie są uwzględniane w danych wyjściowych alertów, ale listę pól można dostosować.


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

## <a name="example-streaming-query"></a>Przykładowa kwerenda strumieniowa
W tym przykładzie kwerenda usługi Stream Analytics łączy dane referencyjne **reguł** z powyższego przykładu do strumienia wejściowego danych o nazwie **metryki**.

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

## <a name="example-streaming-input-event-data"></a>Przykład przesyłania strumieniowego danych zdarzeń wejściowych
W tym przykładzie dane JSON reprezentuje **dane wejściowe metryki,** który jest używany w powyższej kwerendy przesyłania strumieniowego. 

- Trzy przykładowe zdarzenia są wyświetlane w ciągu `T14:50`1 minuty, wartość. 
- Wszystkie trzy mają `deviceId` `978648`tę samą wartość .
- Wartości metryki procesora CPU `98` `95`różnią `80` się w każdym zdarzeniu, , odpowiednio. Tylko pierwsze dwa przykładowe zdarzenia przekraczają regułę alertu procesora CPU ustanowioną w regule.
- Pole includeDim w regule alertu było kluczem numer 2. Odpowiednie pole klucza 2 w `NodeName`przykładowych zdarzeniach nosi nazwę . Trzy przykładowe zdarzenia `N024` `N024`mają `N014` wartości , i odpowiednio. W danych wyjściowych widać `N024` tylko węzeł, ponieważ jest to tylko dane, które spełnia kryteria alertu dla wysokiego procesora CPU. `N014`nie spełnia wysokiego progu procesora.
- Reguła alertu jest `filter` skonfigurowana z tylko na klucz numer `cluster` 2, który odpowiada polu w przykładowych zdarzeń. Trzy przykładowe zdarzenia `C1` mają wartość i odpowiadają kryteriom filtru.

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
W tym przykładzie danych wyjściowych JSON pokazuje pojedyncze zdarzenie alertu został wyprodukowany na podstawie reguły progu procesora cpu zdefiniowane w danych referencyjnych. Zdarzenie wyjściowe zawiera nazwę alertu, a także zagregowane (średnia, min, maks.) rozważanych pól. Dane zdarzenia wyjściowego obejmują wartość `NodeName` `N024` klucza pola 2 ze względu na konfigurację reguły. (JSON został zmieniony, aby pokazać podziały wierszy dla czytelności.)

```JSON
{"time":"2018-05-01T02:03:00.0000000Z","deviceid":"978648","ruleid":1234,"metric":"CPU",
"alert":"hot node AVG CPU over 90","avg":96.5,"min":95.0,"max":98.0,
"dim0":null,"dim1":null,"dim2":"N024","dim3":null,"dim4":null}
```
