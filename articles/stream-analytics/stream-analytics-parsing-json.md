---
title: Analizowanie JSON i AVRO w usłudze Azure Stream Analytics
description: W tym artykule opisano sposób działania na złożonych typach danych, takich jak tablice, JSON, dane sformatowane w formacie CSV.
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.topic: conceptual
ms.date: 01/29/2020
ms.openlocfilehash: 73905483850a47a9d036bef1b9e1ee60d3484555
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77484591"
---
# <a name="parse-json-and-avro-data-in-azure-stream-analytics"></a>Analizowanie danych JSON i Avro w usłudze Azure Stream Analytics

Usługa Azure Stream Analytics obsługuje przetwarzanie zdarzeń w formatach danych CSV, JSON i Avro. Zarówno JSON, jak i Avro mogą być skonstruowane i zawierać pewne złożone typy, takie jak obiekty zagnieżdżone (rekordy) i tablice. 

>[!NOTE]
>Pliki AVRO utworzone przez Event Hub Capture używają określonego formatu, który wymaga użycia niestandardowej funkcji *deserializatora.* Aby uzyskać więcej informacji, zobacz [Odczyt wprowadzania danych w dowolnym formacie przy użyciu deserializatorów niestandardowych platformy .NET](https://docs.microsoft.com/azure/stream-analytics/custom-deserializer-examples).



## <a name="record-data-types"></a>Rejestrowanie typów danych
Typy danych rekordów są używane do reprezentowania tablic JSON i Avro, gdy odpowiednie formaty są używane w strumieniach danych wejściowych. Przykłady te pokazują czujnik próbki, który odczytuje zdarzenia wejściowe w formacie JSON. Oto przykład pojedynczego zdarzenia:

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

### <a name="access-nested-fields-in-known-schema"></a>Dostęp do pól zagnieżdżonych w znanym schemacie
Użyj notacji kropkowej (.), aby łatwo uzyskać dostęp do zagnieżdżonych pól bezpośrednio z zapytania. Na przykład ta kwerenda wybiera współrzędne szerokości i długości geograficznej w obszarze Location właściwości w poprzednich danych JSON. Notacja kropki może służyć do poruszania się po wielu poziomach, jak pokazano poniżej.

```SQL
SELECT
    DeviceID,
    Location.Lat,
    Location.Long,
    SensorReadings.Temperature,
    SensorReadings.SensorMetadata.Version
FROM input
```

Rezultatem jest:

|Deviceid|Łat|Długi|Temperatura|Wersja|
|-|-|-|-|-|
|12345|47|122|80|1.2.45|


### <a name="select-all-properties"></a>Zaznacz wszystkie właściwości
Można wybrać wszystkie właściwości zagnieżdżonego rekordu za pomocą symbolu wieloznacznego "*". Rozważmy następujący przykład:

```SQL
SELECT
    DeviceID,
    Location.*
FROM input
```

Rezultatem jest:

|Deviceid|Łat|Długi|
|-|-|-|
|12345|47|122|


### <a name="access-nested-fields-when-property-name-is-a-variable"></a>Uzyskiwanie dostępu do pól zagnieżdżonych, gdy nazwa właściwości jest zmienną

Użyj [GetRecordPropertyValue](https://docs.microsoft.com/stream-analytics-query/getrecordpropertyvalue-azure-stream-analytics) funkcji, jeśli nazwa właściwości jest zmienną. Pozwala to na tworzenie zapytań dynamicznych bez twardych nazw właściwości.

Załóżmy na przykład, że przykładowy strumień danych **musi być połączony z danymi referencyjnymi** zawierającymi progi dla każdego czujnika urządzenia. Poniżej przedstawiono fragment takich danych referencyjnych.

```json
{
    "DeviceId" : "12345",
    "SensorName" : "Temperature",
    "Value" : 85
},
{
    "DeviceId" : "12345",
    "SensorName" : "Humidity",
    "Value" : 65
}
```

Celem jest dołączenie do naszego przykładowego zestawu danych z góry artykułu do tych danych referencyjnych i wytłumienie jednego zdarzenia dla każdej miary czujnika powyżej progu. Oznacza to, że nasze pojedyncze zdarzenie powyżej może generować wiele zdarzeń wyjściowych, jeśli wiele czujników przekracza odpowiednie progi, dzięki sprzężeń. Aby uzyskać podobne wyniki bez sprzężenia, zapoznaj się z poniższą sekcją.

```SQL
SELECT
    input.DeviceID,
    thresholds.SensorName,
    "Alert : Sensor above threshold" AS AlertMessage
FROM input      -- stream input
JOIN thresholds -- reference data input
ON
    input.DeviceId = thresholds.DeviceId
WHERE
    GetRecordPropertyValue(input.SensorReadings, thresholds.SensorName) > thresholds.Value
```

**GetRecordPropertyValue** wybiera właściwość w *SensorReadings*, która nazwa pasuje do nazwy właściwości pochodzącej z danych referencyjnych. Następnie skojarzona wartość z *SensorReadings* jest wyodrębniany.

Rezultatem jest:

|Deviceid|Nazwa czujnika|AlertMessage|
|-|-|-|
|12345|Wilgotność|Alert : Czujnik powyżej progu|

### <a name="convert-record-fields-into-separate-events"></a>Konwertowanie pól rekordów na oddzielne zdarzenia

Aby przekonwertować pola rekordów na oddzielne zdarzenia, użyj operatora [APPLY](https://docs.microsoft.com/stream-analytics-query/apply-azure-stream-analytics) wraz z funkcją [GetRecordProperties.](https://docs.microsoft.com/stream-analytics-query/getrecordproperties-azure-stream-analytics)

Z oryginalnych danych przykładowych, następujące zapytanie może służyć do wyodrębniania właściwości do różnych zdarzeń.

```SQL
SELECT
    event.DeviceID,
    sensorReading.PropertyName,
    sensorReading.PropertyValue
FROM input as event
CROSS APPLY GetRecordProperties(event.SensorReadings) AS sensorReading
```

Rezultatem jest:

|Deviceid|Nazwa czujnika|AlertMessage|
|-|-|-|
|12345|Temperatura|80|
|12345|Wilgotność|70|
|12345|Niestandardowysensor01|5|
|12345|Niestandardowysensor02|99|
|12345|SensorMetadata|[obiekt]|

Korzystanie [z](https://docs.microsoft.com/stream-analytics-query/with-azure-stream-analytics), to jest następnie możliwe do kierowania tych wydarzeń do różnych miejsc docelowych:

```SQL
WITH Stage0 AS
(
    SELECT
        event.DeviceID,
        sensorReading.PropertyName,
        sensorReading.PropertyValue
    FROM input as event
    CROSS APPLY GetRecordProperties(event.SensorReadings) AS sensorReading
)

SELECT DeviceID, PropertyValue AS Temperature INTO TemperatureOutput FROM Stage0 WHERE PropertyName = 'Temperature'
SELECT DeviceID, PropertyValue AS Humidity INTO HumidityOutput FROM Stage0 WHERE PropertyName = 'Humidity'
```

### <a name="parse-json-record-in-sql-reference-data"></a>Analizuj rekord JSON w danych referencyjnych SQL
Podczas korzystania z usługi Azure SQL Database jako danych referencyjnych w zadaniu, jest możliwe, aby kolumna, która ma dane w formacie JSON. Przykład przedstawiono poniżej.

|Deviceid|Dane|
|-|-|
|12345|{"klucz" : "value1"}|
|54321|{"klucz" : "value2"}|

Rekord JSON można przeanalizować w kolumnie *Dane,* zapisując prostą funkcję zdefiniowaną przez użytkownika w języku JavaScript.

```javascript
function parseJson(string) {
return JSON.parse(string);
}
```

Następnie można utworzyć krok w zapytaniu usługi Stream Analytics, jak pokazano poniżej, aby uzyskać dostęp do pól rekordów JSON.

 ```SQL
 WITH parseJson as
 (
 SELECT DeviceID, udf.parseJson(sqlRefInput.Data) as metadata,
 FROM sqlRefInput
 )
 
 SELECT metadata.key
 INTO output
 FROM streamInput
 JOIN parseJson 
 ON streamInput.DeviceID = parseJson.DeviceID
```

## <a name="array-data-types"></a>Typy danych tablicy

Typy danych tablicy są uporządkowanym zbiorem wartości. Niektóre typowe operacje na wartości tablicy są wyszczególnione poniżej. W tych przykładach użyto funkcji [GetArrayElement](https://docs.microsoft.com/stream-analytics-query/getarrayelement-azure-stream-analytics), [GetArrayElements](https://docs.microsoft.com/stream-analytics-query/getarrayelements-azure-stream-analytics), [GetArrayLength](https://docs.microsoft.com/stream-analytics-query/getarraylength-azure-stream-analytics)i [apply](https://docs.microsoft.com/stream-analytics-query/apply-azure-stream-analytics) operatora.

Oto przykład pojedynczego zdarzenia. Oba `CustomSensor03` `SensorMetadata` i są typu **tablicy:**

```json
{
    "DeviceId" : "12345",
    "SensorReadings" :
    {
        "Temperature" : 80,
        "Humidity" : 70,
        "CustomSensor01" : 5,
        "CustomSensor02" : 99,
        "CustomSensor03": [12,-5,0]
     },
    "SensorMetadata":[
        {          
            "smKey":"Manufacturer",
            "smValue":"ABC"                
        },
        {
            "smKey":"Version",
            "smValue":"1.2.45"
        }
    ]
}
```

### <a name="working-with-a-specific-array-element"></a>Praca z określonym elementem tablicy

Wybierz element tablicy przy określonym indeksie (wybierając pierwszy element tablicy):

```SQL
SELECT
    GetArrayElement(SensorReadings.CustomSensor03, 0) AS firstElement
FROM input
```

Rezultatem jest:

|firstElement (element)|
|-|
|12|

### <a name="select-array-length"></a>Wybierz długość tablicy

```SQL
SELECT
    GetArrayLength(SensorReadings.CustomSensor03) AS arrayLength
FROM input
```

Rezultatem jest:

|tablicaLength|
|-|
|3|

### <a name="convert-array-elements-into-separate-events"></a>Konwertowanie elementów tablicy na oddzielne zdarzenia

Wybierz cały element tablicy jako poszczególne zdarzenia. Zastosuj [APPLY](https://docs.microsoft.com/stream-analytics-query/apply-azure-stream-analytics) operator wraz z Funkcją wbudowaną [GetArrayElements](https://docs.microsoft.com/stream-analytics-query/getarrayelements-azure-stream-analytics) wyodrębnia wszystkie elementy tablicy jako poszczególne zdarzenia:

```SQL
SELECT
    DeviceId,
    CustomSensor03Record.ArrayIndex,
    CustomSensor03Record.ArrayValue
FROM input
CROSS APPLY GetArrayElements(SensorReadings.CustomSensor03) AS CustomSensor03Record

```

Rezultatem jest:

|DeviceId|Arrayindex|Arrayvalue|
|-|-|-|
|12345|0|12|
|12345|1|-5|
|12345|2|0|

```SQL
SELECT   
    i.DeviceId, 
    SensorMetadataRecords.ArrayValue.smKey as smKey,
    SensorMetadataRecords.ArrayValue.smValue as smValue
FROM input i
CROSS APPLY GetArrayElements(SensorMetadata) AS SensorMetadataRecords
 ```
 
Rezultatem jest:

|DeviceId|klawisz smKey|smValue (smValue)|
|-|-|-|
|12345|Producent|ABC|
|12345|Wersja|1.2.45|

Jeśli wyodrębnione pola muszą pojawić się w kolumnach, możliwe jest przestawienie zestawu danych przy użyciu składni [WITH](https://docs.microsoft.com/stream-analytics-query/with-azure-stream-analytics) oprócz operacji [JOIN.](https://docs.microsoft.com/stream-analytics-query/join-azure-stream-analytics) To sprzężenie będzie wymagać warunku [granicy czasu,](https://docs.microsoft.com/stream-analytics-query/join-azure-stream-analytics#BKMK_DateDiff) który zapobiega powielaniu:

```SQL
WITH DynamicCTE AS (
    SELECT   
        i.DeviceId,
        SensorMetadataRecords.ArrayValue.smKey as smKey,
        SensorMetadataRecords.ArrayValue.smValue as smValue
    FROM input i
    CROSS APPLY GetArrayElements(SensorMetadata) AS SensorMetadataRecords 
)

SELECT
    i.DeviceId,
    i.Location.*,
    V.smValue AS 'smVersion',
    M.smValue AS 'smManufacturer'
FROM input i
LEFT JOIN DynamicCTE V ON V.smKey = 'Version' and V.DeviceId = i.DeviceId AND DATEDIFF(minute,i,V) BETWEEN 0 AND 0 
LEFT JOIN DynamicCTE M ON M.smKey = 'Manufacturer' and M.DeviceId = i.DeviceId AND DATEDIFF(minute,i,M) BETWEEN 0 AND 0
```

Rezultatem jest:

|DeviceId|Łat|Długi|smVersion (wersja smVersion)|smProdukt|
|-|-|-|-|-|
|12345|47|122|1.2.45|ABC|

## <a name="see-also"></a>Zobacz też
[Typy danych w usłudze Azure Stream Analytics](https://docs.microsoft.com/stream-analytics-query/data-types-azure-stream-analytics)
