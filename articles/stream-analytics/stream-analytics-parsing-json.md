---
title: Analizowanie JSON i AVRO w usłudze Azure Stream Analytics
description: W tym artykule opisano, jak może działać w złożonych typach danych takich jak tablice, JSON, CSV sformatowany dane.
services: stream-analytics
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.topic: conceptual
ms.date: 06/21/2019
ms.openlocfilehash: daf5b97e4ac586f89e5964ee16ee73c86f59b01d
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/22/2019
ms.locfileid: "67329359"
---
# <a name="parse-json-and-avro-data-in-azure-stream-analytics"></a>Analizowanie danych JSON i Avro w usłudze Azure Stream Analytics

Usługa Azure Stream Analytics obsługuje przetwarzanie zdarzeń w pliku CSV, JSON i Avro formaty danych. Dane JSON i Avro mogą mieć strukturę i zawierają niektóre typy złożone, np. zagnieżdżone obiekty (rekordy) i tablice. 




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
        "CustomSensor02" : 99,
        "SensorMetadata" : 
        {
        "Manufacturer":"ABC",
        "Version":"1.2.45"
        }
    }
}
```


### <a name="access-nested-fields-in-known-schema"></a>Zagnieżdżone dostęp do pól w znanym schematem
Użyj notacji z kropką (.), aby łatwo uzyskać dostęp do pól zagnieżdżonych, bezpośrednio z zapytania. Na przykład to zapytanie wybiera współrzędne długości i szerokości geograficznej, w obszarze właściwości Location w poprzednim dane JSON. Zapisu kropkowego może służyć do nawigacji wiele poziomów, jak pokazano poniżej.

```SQL
SELECT
    DeviceID,
    Location.Lat,
    Location.Long,
    SensorReadings.SensorMetadata.Version
FROM input
```

### <a name="select-all-properties"></a>Zaznacz wszystkie właściwości
Można wybrać wszystkie właściwości zagnieżdżone rekordu przy użyciu ' *' symboli wieloznacznych. Rozważmy następujący przykład:

```SQL
SELECT input.Location.*
FROM input
```

Wynik jest:

```json
{
    "Lat" : 47,
    "Long" : 122
}
```


### <a name="access-nested-fields-when-property-name-is-a-variable"></a>Dostęp zagnieżdżone pola, gdy nazwa właściwości jest zmienną
Użyj [funkcji GetRecordPropertyValue](https://docs.microsoft.com/stream-analytics-query/getmetadatapropertyvalue) działać, jeśli nazwa właściwości jest to zmienna. 

Na przykład Wyobraź sobie, że strumień danych przykładowych, musi być łączone z odwołania zawierającego progi danych dla każdego czujników urządzenia. Poniżej przedstawiono fragment takich danych referencyjnych.

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
FROM input      -- stream input
JOIN thresholds -- reference data input
ON
    input.DeviceId = thresholds.DeviceId
WHERE
    GetRecordPropertyValue(input.SensorReadings, thresholds.SensorName) > thresholds.Value
    -- the where statement selects the property value coming from the reference data
```

### <a name="convert-record-fields-into-separate-events"></a>Konwertuj pola rekordu w oddzielnych zdarzenia
Aby przekonwertować pola rekordu odrębne zdarzenia, należy użyć [Zastosuj](https://docs.microsoft.com/stream-analytics-query/apply-azure-stream-analytics) operator wraz z [GetRecordProperties](https://docs.microsoft.com/stream-analytics-query/getrecordproperties-azure-stream-analytics) funkcji. Na przykład jeśli poprzedni przykład kilka rekordów dla SensorReading, następujące zapytanie może służyć do wyodrębnić je do różnych zdarzeń:

```SQL
SELECT
    event.DeviceID,
    sensorReading.PropertyName,
    sensorReading.PropertyValue
FROM input as event
CROSS APPLY GetRecordProperties(event.SensorReadings) AS sensorReading
```



## <a name="array-data-types"></a>Tablica typów danych

Typy danych tablicy są uporządkowany zbiór wartości. Niektóre typowe operacje na wartości tablicy są szczegółowo opisane poniżej. W poniższych przykładach założono zdarzenia wejściowe ma właściwość o nazwie "arrayField" oznacza to typ danych tablicy.

Te przykłady, użyj funkcji [GetArrayElement](https://docs.microsoft.com/stream-analytics-query/getarrayelement-azure-stream-analytics), [GetArrayElements](https://docs.microsoft.com/stream-analytics-query/getarrayelements-azure-stream-analytics), [GetArrayLength](https://docs.microsoft.com/stream-analytics-query/getarraylength-azure-stream-analytics)i [Zastosuj](https://docs.microsoft.com/stream-analytics-query/apply-azure-stream-analytics) operatora.

### <a name="working-with-a-specific-array-element"></a>Praca z określonego elementu tablicy
Wybierz element tablicy od określonego indeksu (zaznaczając pierwszy element tablicy):

```SQL
SELECT
    GetArrayElement(arrayField, 0) AS firstElement
FROM input
```

### <a name="select-array-length"></a>Wybierz długość tablicy

```SQL
SELECT
    GetArrayLength(arrayField) AS arrayLength
FROM input
```

### <a name="convert-array-elements-into-separate-events"></a>Konwertuj elementy tablicy w oddzielnych zdarzenia
Wybierz wszystkie elementu tablicy poszczególne zdarzenia. [Zastosuj](https://docs.microsoft.com/stream-analytics-query/apply-azure-stream-analytics) operator wraz z [GetArrayElements](https://docs.microsoft.com/stream-analytics-query/getarrayelements-azure-stream-analytics) wbudowanej funkcji wyodrębnia wszystkie elementy tablicy jako pojedyncze zdarzenia:

```SQL
SELECT
    arrayElement.ArrayIndex,
    arrayElement.ArrayValue
FROM input as event
CROSS APPLY GetArrayElements(event.arrayField) AS arrayElement
```


## <a name="see-also"></a>Zobacz też
[Typy danych w usłudze Azure Stream Analytics](https://docs.microsoft.com/stream-analytics-query/data-types-azure-stream-analytics)
