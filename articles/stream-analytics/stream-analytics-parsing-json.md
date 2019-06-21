---
title: Analizowanie JSON i AVRO w usłudze Azure Stream Analytics
description: W tym artykule opisano, jak może działać w złożonych typach danych takich jak tablice, JSON, CSV sformatowany dane.
services: stream-analytics
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 84d2d43074b149ed029b1f70323cf4fe896e530d
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/20/2019
ms.locfileid: "67293588"
---
# <a name="parse-json-and-avro-data-in-azure-stream-analytics"></a>Analizowanie danych JSON i Avro w usłudze Azure Stream Analytics

Usługa Azure Stream Analytics obsługuje przetwarzanie zdarzeń w pliku CSV, JSON i Avro formaty danych. Dane JSON i Avro mogą zawierać złożonych typów, takich jak zagnieżdżone obiekty (rekordy) i tablice.

## <a name="array-data-types"></a>Tablica typów danych

Typy danych tablicy są uporządkowany zbiór wartości. Niektóre typowe operacje na wartości tablicy są szczegółowo opisane poniżej. W poniższych przykładach założono zdarzenia wejściowe ma właściwość o nazwie "arrayField" oznacza to typ danych tablicy.

Te przykłady, użyj funkcji [GetArrayElement](https://msdn.microsoft.com/azure/stream-analytics/reference/getarrayelement-azure-stream-analytics), [GetArrayElements](https://msdn.microsoft.com/azure/stream-analytics/reference/getarrayelements-azure-stream-analytics), [GetArrayLength](https://msdn.microsoft.com/azure/stream-analytics/reference/getarraylength-azure-stream-analytics)i [Zastosuj](https://msdn.microsoft.com/azure/stream-analytics/reference/apply-azure-stream-analytics) operatora.

## <a name="examples"></a>Przykłady
Wybierz element tablicy od określonego indeksu (zaznaczając pierwszy element tablicy):

```SQL
SELECT
    GetArrayElement(arrayField, 0) AS firstElement
FROM input
```

Wybierz długość tablicy:

```SQL
SELECT
    GetArrayLength(arrayField) AS arrayLength
FROM input
```

Wybierz wszystkie elementu tablicy poszczególne zdarzenia. [Zastosuj](https://msdn.microsoft.com/azure/stream-analytics/reference/apply-azure-stream-analytics) operator wraz z [GetArrayElements](https://msdn.microsoft.com/azure/stream-analytics/reference/getarrayelements-azure-stream-analytics) wbudowanej funkcji wyodrębnia wszystkie elementy tablicy jako pojedyncze zdarzenia:

```SQL
SELECT
    arrayElement.ArrayIndex,
    arrayElement.ArrayValue
FROM input as event
CROSS APPLY GetArrayElements(event.arrayField) AS arrayElement
```

## <a name="record-data-types"></a>Typy danych rekordu
Typy danych rekordu są używane do reprezentowania tablice JSON i Avro, gdy odpowiadające im formaty są używane w strumieniach danych wejściowych. Przykłady te pokazują czujnik przykład odczytuje zdarzenia wejściowe w formacie JSON. Oto przykład pojedyncze zdarzenie:

```json
{
    "DeviceId" : "12345",
    "Location" :
    {
        "Lat": 47,
        "Long": 122
    },
    "SensorReadings" :
    {
        "Temperature" : 80,
        "Humidity" : 70,
        "CustomSensor01" : 5,
        "CustomSensor02" : 99
    }
}
```

## <a name="examples"></a>Przykłady
Użyj notacji z kropką (.), aby uzyskać dostęp do pól zagnieżdżonych. Na przykład ta kwerenda wybiera współrzędne długości i szerokości geograficznej, w obszarze właściwości Location w poprzednim dane JSON:

```SQL
SELECT
    DeviceID,
    Location.Lat,
    Location.Long
FROM input
```

Użyj [funkcji GetRecordPropertyValue](https://msdn.microsoft.com/azure/stream-analytics/reference/getrecordpropertyvalue-azure-stream-analytics) działać, jeśli nazwa właściwości jest nieznany. Na przykład Wyobraź sobie, że strumień danych przykładowych, musi być łączone z odwołania zawierającego progi danych dla każdego czujników urządzenia:

```json
{
    "DeviceId" : "12345",
    "SensorName" : "Temperature",
    "Value" : 75
}
```

```SQL
SELECT
    input.DeviceID,
    thresholds.SensorName
FROM input
JOIN thresholds
ON
    input.DeviceId = thresholds.DeviceId
WHERE
    GetRecordPropertyValue(input.SensorReadings, thresholds.SensorName) > thresholds.Value
```

Aby przekonwertować pola rekordu odrębne zdarzenia, należy użyć [Zastosuj](https://msdn.microsoft.com/azure/stream-analytics/reference/apply-azure-stream-analytics) operator wraz z [GetRecordProperties](https://msdn.microsoft.com/azure/stream-analytics/reference/getrecordproperties-azure-stream-analytics) funkcji. Na przykład aby przekonwertować strumień przykładowe strumień zdarzeń za pomocą odczyty czujników poszczególnych, to zapytanie może służyć:

```SQL
SELECT
    event.DeviceID,
    sensorReading.PropertyName,
    sensorReading.PropertyValue
FROM input as event
CROSS APPLY GetRecordProperties(event.SensorReadings) AS sensorReading
```

Można wybrać wszystkie właściwości zagnieżdżone rekordu przy użyciu ' *' symboli wieloznacznych. Rozważmy następujący przykład:

```SQL
SELECT input.SensorReadings.*
FROM input
```

Wynik jest:

```json
{
    "Temperature" : 80,
    "Humidity" : 70,
    "CustomSensor01" : 5,
    "CustomSensor022" : 99
}
```

## <a name="see-also"></a>Zobacz też
[Typy danych w usłudze Azure Stream Analytics](https://docs.microsoft.com/en-us/stream-analytics-query/data-types-azure-stream-analytics)
