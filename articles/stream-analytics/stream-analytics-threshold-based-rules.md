---
title: Proces skonfigurowaną wartość progową, na podstawie reguł w Azure Stream Analytics
description: W tym artykule opisano sposób używania danych referencyjnych do osiągnięcia rozwiązanie alertów, który ma skonfigurowaną wartość progową, na podstawie reguł w Azure Stream Analytics.
services: stream-analytics
author: zhongc
ms.author: zhongc
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/30/2018
ms.openlocfilehash: 1c131c2c9ca12556c1d2cd52e7976d2f4272a0c8
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/03/2018
ms.locfileid: "32767973"
---
# <a name="process-configurable-threshold-based-rules-in-azure-stream-analytics"></a>Proces można skonfigurować reguły progu w Azure Stream Analytics
W tym artykule opisano sposób używania danych referencyjnych do osiągnięcia rozwiązanie alertów, które używa można skonfigurować reguły progu w Azure Stream Analytics.

## <a name="scenario-alerting-based-on-adjustable-rule-thresholds"></a>Scenariusz: Alerty na podstawie reguł regulowany progów
Konieczne może być wywoływać alert jako dane wyjściowe podczas zdarzenia przychodzące przesyłanej strumieniowo osiągnięto określonej wartości lub wartości zagregowanej na podstawie przychodzącego strumienia zdarzeń przekroczy określony próg. Prosta konfiguracja porównać wartości do statycznego progu, stałe i wstępnie określone zapytanie Stream Analytics go. Stały próg może być ustalony do przesyłania strumieniowego składnia zapytania przy użyciu prostego porównania wartości liczbowych (większa niż mniej niż i równości).

W niektórych przypadkach wartości progowe muszą być łatwiej można skonfigurować bez konieczności edytowania składnia zapytania każdym razem, gdy zmienia się wartość progową. W innych przypadkach może być konieczne wiele urządzeń lub użytkowników, przetwarzane przez tego samego zapytania z każdym z nich o różnych progiem na każdego rodzaju urządzenia. 

Ten wzorzec umożliwia dynamiczne skonfigurować progi, selektywnie jakiego rodzaju urządzenia próg stosuje filtrowanie danych wejściowych lub selektywnie wybrać pola, które mają być uwzględnione w danych wyjściowych.

## <a name="recommended-design-pattern"></a>Wzorzec projektowy zalecane
Użyj odwołania danych wejściowych do zadania usługi analiza strumienia jako odnośnik progi alertów:
- Przechowywanie wartości progowe w danych referencyjnych, jedna wartość na klucz.
- Dołącz strumieniowego przesyłania zdarzeń wprowadzania danych do danych referencyjnych na kolumny klucza.
- Jako wartość progowa, należy użyć wartości kluczem z danych referencyjnych.

## <a name="example-data-and-query"></a>Przykładowe dane i zapytań
W tym przykładzie alerty są generowane, gdy wartości zagregowanej przesyłanie strumieniowe w z urządzeń, w oknie minutę długości danych jest zgodna z wartości określone w regule podana jako dane referencyjne.

W zapytaniu, dla każdego deviceId i każdego metricName w obszarze deviceId można skonfigurować z zakresu od 0 do 5 wymiarów GROUP BY. Tylko zdarzenia o odpowiedniej wartości filtru są grupowane. Po grupowane, agregacjami Min, Max, Avg, są obliczane przez okno wirowania 60 sekund. Filtry zagregowane wartości następnie są obliczane zgodnie z harmonogramem skonfigurowany próg odwołania, aby wygenerować zdarzenie wyjściowe alertu.

Na przykład załóżmy jest zadanie usługi analiza strumienia, który ma wejściowych danych referencyjnych, o nazwie **reguły**i przesyłania strumieniowego danych wejściowych o nazwie **metryki**. 

## <a name="reference-data"></a>Dane referencyjne
Dane referencyjne to przykład pokazuje, jak można reprezentowane reguły oparte na wartościach progowych. Plik JSON przechowuje dane referencyjne i jest zapisany w magazynie obiektów blob Azure, a ten kontener magazynu obiektów blob służy jako dane wejściowe odwołanie do danych o nazwie **reguły**. Można zastąpić ten plik JSON, a następnie zastąpić konfigurację reguły, zgodnie z upływem czasu, bez zatrzymywania i uruchamiania zadania przesyłania strumieniowego.

- Przykładowa reguła jest używana do reprezentowania regulowany alertu, gdy procesora CPU przekracza (średnia jest większa niż lub równe) wartość `90` procent. `value` Pole jest konfigurowalne, zgodnie z potrzebami.
- Zwróć uwagę, reguła ma **operator** pola, które dynamicznie interpretowany w składni zapytania później `AVGGREATEROREQUAL`. 
- Reguła filtruje dane dla klucza wymiaru `2` o wartości `C1`. Pozostałe pola są pusty ciąg wskazujący nie, aby filtrować strumień wejściowy według pól tych zdarzeń. Aby filtrować inne pola dopasowania, w razie potrzeby można skonfigurować dodatkowe reguły procesora CPU.
- Nie wszystkie kolumny są do uwzględnienia w zdarzeniu alertu danych wyjściowych. W takim przypadku `includedDim` klucza numer `2` jest włączona `TRUE` do reprezentowania, że czy numer pola 2 w strumieniu danych dotyczących zdarzeń zostaną uwzględnione w kwalifikujących się dane wyjściowe zdarzenia. Pozostałe pola nie znajdują się w danych wyjściowych alertu, ale można dostosować w oknie Lista pól.


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
To przykładowe zapytanie Stream Analytics łączy **reguły** odwoływał się do danych w przykładzie powyżej, aby wejściowego strumienia danych o nazwie **metryki**.

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

## <a name="example-streaming-input-event-data"></a>Przykład strumieniowych danych zdarzenia wejściowego
W tym przykładzie dane JSON reprezentuje **metryki** wprowadzanie danych, który jest używany w powyższym zapytaniu przesyłania strumieniowego. 

- Trzy takie zdarzenia są wyświetlane w ramach timespan 1 minutę, wartość `T14:50`. 
- Wszystkie trzy mają taki sam `deviceId` wartość `978648`.
- Wartości metryki Procesora różnią się w każdym przypadku `98`, `95`, `80` odpowiednio. Tylko pierwsze dwa przykłady zdarzeń przekracza Procesora reguły alertów ustalone w regule.
- Pole includeDim w regule alertu został klucza numer 2. Odpowiednie pole klucza 2 w takie zdarzenia nosi nazwę `NodeName`. Przykład trzech zdarzeń mają wartości `N024`, `N024`, i `N014` odpowiednio. W danych wyjściowych, zostanie wyświetlony tylko węzeł `N024` jak jest tylko dane kryteria alertu dla Procesora wysoki. `N014` nie spełnia Wysoki próg procesora CPU.
- Skonfigurowano regułę alertu `filter` tylko dla klucza numer 2, który odpowiada `cluster` pola w zdarzeniach próbki. Wszystkie zdarzenia przykładzie trzy mają wartość `C1` i spełniających kryteria filtru.

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
W tym przykładzie danych wyjściowych pokazuje danych JSON pojedyncze zdarzenie alertu został utworzony na podstawie procesora CPU próg reguły zdefiniowane w danych referencyjnych. Zdarzenie wyjściowe zawiera nazwę alertu, a także zagregowane (średni, min, max) pól traktowane jako. Dane zdarzenia dane wyjściowe obejmują pola klucza numer 2 `NodeName` wartość `N024` z powodu konfiguracji reguły. (Dane JSON został zmieniony w celu Pokaż podziałów wierszy dla czytelności).

```JSON
{"time":"2018-05-01T02:03:00.0000000Z","deviceid":"978648","ruleid":1234,"metric":"CPU",
"alert":"hot node AVG CPU over 90","avg":96.5,"min":95.0,"max":98.0,
"dim0":null,"dim1":null,"dim2":"N024","dim3":null,"dim4":null}
```