---
title: Analizowanie JSON i AVRO w Azure Stream Analytics
description: W tym artykule opisano sposób działania na złożonych typach danych, takich jak tablice, JSON, dane w formacie CSV.
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.topic: conceptual
ms.date: 06/21/2019
ms.openlocfilehash: 1741510c7398ce74da81f006cb4109d9a33f8f9f
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75431607"
---
# <a name="parse-json-and-avro-data-in-azure-stream-analytics"></a>Analizowanie danych JSON i Avro w Azure Stream Analytics

Azure Stream Analytics obsługiwać zdarzenia przetwarzania w formatach danych CSV, JSON i Avro. Dane JSON i Avro mogą być strukturalne i zawierać niektóre złożone typy, takie jak obiekty zagnieżdżone (rekordy) i tablice. 




## <a name="record-data-types"></a>Rejestruj typy danych
Typy danych rekordów służą do reprezentowania tablic JSON i Avro, gdy w strumieniach danych wejściowych są używane odpowiednie formaty. Przykładowe przykłady przedstawiają czujnik próbek, który odczytuje zdarzenia wejściowe w formacie JSON. Oto przykład pojedynczego zdarzenia:

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


### <a name="access-nested-fields-in-known-schema"></a>Dostęp do zagnieżdżonych pól w znanym schemacie
Użyj notacji kropki (.), aby łatwo uzyskać dostęp do zagnieżdżonych pól bezpośrednio z zapytania. Na przykład to zapytanie wybiera współrzędne geograficzne i Długość geograficzna we właściwości Location w poprzednich danych JSON. Notacja kropka może służyć do nawigowania na wielu poziomach, jak pokazano poniżej.

```SQL
SELECT
    DeviceID,
    Location.Lat,
    Location.Long,
    SensorReadings.SensorMetadata.Version
FROM input
```

### <a name="select-all-properties"></a>Zaznacz wszystkie właściwości
Można wybrać wszystkie właściwości zagnieżdżonego rekordu przy użyciu symbolu wieloznacznego "*". Rozważmy następujący przykład:

```SQL
SELECT input.Location.*
FROM input
```

Wynik:

```json
{
    "Lat" : 47,
    "Long" : 122
}
```


### <a name="access-nested-fields-when-property-name-is-a-variable"></a>Dostęp do zagnieżdżonych pól, gdy nazwa właściwości jest zmienną
Użyj funkcji [GetRecordPropertyValue](https://docs.microsoft.com/stream-analytics-query/getmetadatapropertyvalue) , jeśli nazwa właściwości jest zmienną. 

Załóżmy na przykład, że przykładowy strumień danych musi być połączony z danymi referencyjnymi zawierającymi progi dla każdego czujnika urządzenia. Poniżej przedstawiono fragment kodu dotyczący takich danych referencyjnych.

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

### <a name="convert-record-fields-into-separate-events"></a>Konwertuj pola rekordów na oddzielne zdarzenia
Aby przekonwertować pola rekordu na oddzielne zdarzenia, użyj operatora [apply](https://docs.microsoft.com/stream-analytics-query/apply-azure-stream-analytics) razem z funkcją [GetRecordProperties](https://docs.microsoft.com/stream-analytics-query/getrecordproperties-azure-stream-analytics) . Na przykład jeśli poprzedni przykład zawierał kilka rekordów dla SensorReading, można użyć następującego zapytania, aby wyodrębnić je do różnych zdarzeń:

```SQL
SELECT
    event.DeviceID,
    sensorReading.PropertyName,
    sensorReading.PropertyValue
FROM input as event
CROSS APPLY GetRecordProperties(event.SensorReadings) AS sensorReading
```



## <a name="array-data-types"></a>Array — typy danych

Typy danych tablicy to uporządkowana Kolekcja wartości. Poniżej przedstawiono niektóre typowe operacje dotyczące wartości tablicy. W tych przykładach przyjęto założenie, że zdarzenia wejściowe mają właściwość o nazwie "arrayField", która jest typem danych Array.

W tych przykładach użyto funkcji [GetArrayElement](https://docs.microsoft.com/stream-analytics-query/getarrayelement-azure-stream-analytics), [GetArrayElements](https://docs.microsoft.com/stream-analytics-query/getarrayelements-azure-stream-analytics), [GetArrayLength](https://docs.microsoft.com/stream-analytics-query/getarraylength-azure-stream-analytics)i operator [apply](https://docs.microsoft.com/stream-analytics-query/apply-azure-stream-analytics) .

### <a name="working-with-a-specific-array-element"></a>Praca z określonym elementem tablicy
Wybierz element tablicy w określonym indeksie (zaznaczając pierwszy element tablicy):

```SQL
SELECT
    GetArrayElement(arrayField, 0) AS firstElement
FROM input
```

### <a name="select-array-length"></a>Wybierz Długość tablicy

```SQL
SELECT
    GetArrayLength(arrayField) AS arrayLength
FROM input
```

### <a name="convert-array-elements-into-separate-events"></a>Konwertuj elementy tablicy na oddzielne zdarzenia
Zaznacz wszystkie elementy tablicy jako pojedyncze zdarzenia. Operator [apply](https://docs.microsoft.com/stream-analytics-query/apply-azure-stream-analytics) wraz z wbudowaną funkcją [GetArrayElements](https://docs.microsoft.com/stream-analytics-query/getarrayelements-azure-stream-analytics) wyodrębnia wszystkie elementy tablicy jako pojedyncze zdarzenia:

```SQL
SELECT
    arrayElement.ArrayIndex,
    arrayElement.ArrayValue
FROM input as event
CROSS APPLY GetArrayElements(event.arrayField) AS arrayElement
```


## <a name="see-also"></a>Zobacz też
[Typy danych w Azure Stream Analytics](https://docs.microsoft.com/stream-analytics-query/data-types-azure-stream-analytics)
