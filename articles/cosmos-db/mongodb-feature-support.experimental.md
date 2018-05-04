---
title: Azure obsługę funkcji rozwiązania Cosmos DB dla bazy danych MongoDB | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o obsłudze funkcji interfejsu API Azure rozwiązania Cosmos bazy danych MongoDB zapewnia 3.4 bazy danych MongoDB.
services: cosmos-db
author: alekseys
manager: kfile
documentationcenter: ''
ms.assetid: 29b6547c-3201-44b6-9e0b-e6f56e473e24
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: alekseys
ms.openlocfilehash: 0bae274a7bf849267aed92d1372bc26e86227cb2
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2018
---
# <a name="mongodb-api-support-for-mongodb-features-and-syntax"></a>Obsługa interfejsu API bazy danych MongoDB dla funkcji i składni bazy danych MongoDB

Azure Cosmos DB to rozproszona globalnie, wielomodelowa usługa bazy danych firmy Microsoft. Użytkownik może komunikować się z bazy danych MongoDB API za pomocą dowolnego klienta bazy danych MongoDB typu open source [sterowniki](https://docs.mongodb.org/ecosystem/drivers). Interfejs API bazy danych MongoDB umożliwia korzystanie z istniejących sterowników klienta przez do bazy danych MongoDB [okablować protokołu](https://docs.mongodb.org/manual/reference/mongodb-wire-protocol).

Za pomocą interfejsu API Azure rozwiązania Cosmos bazy danych MongoDB, można korzystać z zalet interfejsów API bazy danych MongoDB użyto, biorąc pod uwagę możliwości przedsiębiorstwa zawiera bazy danych Azure rozwiązania Cosmos: [globalne dystrybucji](distribute-data-globally.md), [automatycznego dzielenia na fragmenty](partition-data.md), gwarancje dostępności i czas oczekiwania, automatycznego indeksowania każdego pola, szyfrowanie w rest, kopie zapasowe i o wiele więcej.

## <a name="mongodb-query-language-support"></a>Obsługa języków kwerendy bazy danych MongoDB

Interfejs API Azure rozwiązania Cosmos bazy danych MongoDB zapewnia obsługę kompleksowe konstrukcji języka zapytań bazy danych MongoDB. Poniżej można znaleźć szczegółową listę aktualnie obsługiwanych operacji, operatory, etapy, poleceń i opcji.


## <a name="database-commands"></a>Polecenia bazy danych

Azure DB rozwiązania Cosmos obsługuje następujące polecenia bazy danych na wszystkich kontach API bazy danych MongoDB. 

### <a name="query-and-write-operation-commands"></a>Polecenia operacji zapytania i zapisu
- usuwanie
- Znajdź
- findAndModify
- getLastError
- getMore
- Wstaw
- Aktualizacja

### <a name="authentication-commands"></a>Polecenia uwierzytelniania
- wyloguj
- uwierzytelniania
- getnonce

### <a name="administration-commands"></a>Polecenia administracyjne
- dropDatabase
- listCollections
- Upuść
- create
- filemd5
- createIndexes
- listIndexes
- dropIndexes
- ConnectionStatus
- Ponowna indeksacja

### <a name="diagnostics-commands"></a>Diagnostyka poleceń
- buildInfo
- collStats
- dbStats
- hostInfo
- listDatabases
- whatsmyuri

<a name="aggregation-pipeline"/>

## <a name="aggregation-pipelinea"></a>Potok agregacji</a>

Azure DB rozwiązania Cosmos obsługuje potoku agregacji w publicznej wersji zapoznawczej. Zobacz [Azure blog](https://aka.ms/mongodb-aggregation) instrukcje na temat dołączyć do publicznej wersji zapoznawczej.

### <a name="aggregation-commands"></a>Polecenia agregacji
- agregacji
- liczba
- Różne

### <a name="aggregation-stages"></a>Etapy agregacji
- $project
- $match
- $limit
- $skip
- $unwind
- $group
- $sample
- $sort
- $lookup
- $out
- $count
- $addFields

### <a name="aggregation-expressions"></a>Wyrażenia agregacji

#### <a name="boolean-expressions"></a>Wyrażenia logiczne
- $i
- $lub
- $not

#### <a name="set-expressions"></a>Zestaw wyrażeń
- $setEquals
- $setIntersection
- $setUnion
- $setDifference
- $setIsSubset
- $anyElementTrue
- $allElementsTrue

#### <a name="comparison-expressions"></a>Porównywanie wyrażeń
- $cmp
- $eq
- $gt
- $gte
- $lt
- $lte
- $ne

#### <a name="arithmetic-expressions"></a>Wyrażenia arytmetyczne
- $abs
- Dodaj $
- $ceil
- $divide
- $exp
- $floor
- $ln
- $log
- $log10
- $mod
- $mnożenia
- $pow
- $sqrt
- odejmowanie $
- $trunc

#### <a name="string-expressions"></a>Wyrażenia parametrów
- $concat
- $indexOfBytes
- $indexOfCP
- $split
- $strLenBytes
- $strLenCP
- $strcasecmp
- $substr
- $substrBytes
- $substrCP
- $toLower
- $toUpper

#### <a name="array-expressions"></a>Wyrażenia tablicy
- $arrayElemAt
- $concatArrays
- $filter
- $indexOfArray
- $isArray
- $range
- $reverseArray
- $size
- $slice
- $w

#### <a name="date-expressions"></a>Data wyrażenia
- $dayOfYear
- $dayOfMonth
- $dayOfWeek
- $year
- $month
- $week
- $hour
- $minute
- $drugi
- $millisecond
- $isoDayOfWeek
- $isoWeek

#### <a name="conditional-expressions"></a>Wyrażenia warunkowe
- $cond
- $ifNull

## <a name="aggregation-accumulators"></a>Akumulatory agregacji
- $sum
- $avg
- $pierwszy
- $ostatniego
- $max
- $min
- $push
- $addToSet

## <a name="operators"></a>Operatory

Następujące operatory są obsługiwane przez odpowiednie przykłady ich użycia. Należy wziąć pod uwagę ten przykładowy dokument używane w zapytaniach poniżej:

```json
{
  "Volcano Name": "Rainier",
  "Country": "United States",
  "Region": "US-Washington",
  "Location": {
    "type": "Point",
    "coordinates": [
      -121.758,
      46.87
    ]
  },
  "Elevation": 4392,
  "Type": "Stratovolcano",
  "Status": "Dendrochronology",
  "Last Known Eruption": "Last known eruption from 1800-1899, inclusive"
}
```

Operator | Przykład |
--- | --- |
$eq | ``` { "Volcano Name": { $eq: "Rainier" } } ``` |  | -
$gt | ``` { "Elevation": { $gt: 4000 } } ``` |  | -
$gte | ``` { "Elevation": { $gte: 4392 } } ``` |  | -
$lt | ``` { "Elevation": { $lt: 5000 } } ``` |  | -
$lte | ``` { "Elevation": { $lte: 5000 } } ``` | | -
$ne | ``` { "Elevation": { $ne: 1 } } ``` |  | -
$w | ``` { "Volcano Name": { $in: ["St. Helens", "Rainier", "Glacier Peak"] } } ``` |  | -
$nin | ``` { "Volcano Name": { $nin: ["Lassen Peak", "Hood", "Baker"] } } ``` | | -
$lub | ``` { $or: [ { Elevation: { $lt: 4000 } }, { "Volcano Name": "Rainier" } ] } ``` |  | -
$i | ``` { $and: [ { Elevation: { $gt: 4000 } }, { "Volcano Name": "Rainier" } ] } ``` |  | -
$not | ``` { "Elevation": { $not: { $gt: 5000 } } } ```|  | -
$ani | ``` { $nor: [ { "Elevation": { $lt: 4000 } }, { "Volcano Name": "Baker" } ] } ``` |  | -
istnieje $ | ``` { "Status": { $exists: true } } ```|  | -
$type | ``` { "Status": { $type: "string" } } ```|  | -
$mod | ``` { "Elevation": { $mod: [ 4, 0 ] } } ``` |  | -
$regex | ``` { "Volcano Name": { $regex: "^Rain"} } ```|  | -

### <a name="notes"></a>Uwagi

W zapytaniach $regex zakotwiczony lewej strony wyrażenia Zezwalaj indeksu wyszukiwania. Jednak za pomocą "i" modyfikator (Ignorowanie) i 'M ' modyfikator (multiline) powoduje, że skanowanie kolekcji w wszystkie wyrażenia.
Gdy istnieje potrzeba zawierają "$" lub "|", zaleca się tworzenie zapytań regex dwie (lub więcej). Na przykład podane następujące oryginalne zapytanie: ```find({x:{$regex: /^abc$/})```, musi on być modyfikowany w następujący sposób: ```find({x:{$regex: /^abc/, x:{$regex:/^abc$/}})```.
Pierwsza część użyje ograniczyć wyszukiwanie do tych dokumentów, począwszy od indeksu ^ abc i drugiej części będą zgodne dokładne wpisów. Pasek operator "|" działa jako funkcję "lub" - zapytanie ```find({x:{$regex: /^abc|^def/})``` odpowiada dokumentów, w którym polu "x" zawiera wartości, które zaczynają się od "abc" albo "def". Korzystanie z indeksu, zaleca się break zapytanie na dwie różne połączonych $lub operator: ```find( {$or : [{x: $regex: /^abc/}, {$regex: /^def/}] })```.

### <a name="update-operators"></a>Operatory aktualizacji

#### <a name="field-update-operators"></a>Pola, operatory aktualizacji
- $inc
- $mul
- $rename
- $setOnInsert
- $set
- Nie ustawiono $
- $min
- $max
- $currentDate

#### <a name="array-update-operators"></a>Operatory aktualizacji tablicy
- $addToSet
- $pop
- $pullAll
- $pull (Uwaga: $pull z warunkiem nie jest obsługiwane)
- $pushAll
- $push
- $każdego
- $slice
- $sort
- $position

#### <a name="bitwise-update-operator"></a>Aktualizacja z bitowego operatora
- $bit

### <a name="geospatial-operators"></a>Operatory lokalizacji geograficznych

Operator | Przykład 
--- | --- |
$geoWithin | ```{ "Location.coordinates": { $geoWithin: { $centerSphere: [ [ -121, 46 ], 5 ] } } }``` | Yes
$geoIntersects |  ```{ "Location.coordinates": { $geoIntersects: { $geometry: { type: "Polygon", coordinates: [ [ [ -121.9, 46.7 ], [ -121.5, 46.7 ], [ -121.5, 46.9 ], [ -121.9, 46.9 ], [ -121.9, 46.7 ] ] ] } } } }``` | Yes
$pobliżu | ```{ "Location.coordinates": { $near: { $geometry: { type: "Polygon", coordinates: [ [ [ -121.9, 46.7 ], [ -121.5, 46.7 ], [ -121.5, 46.9 ], [ -121.9, 46.9 ], [ -121.9, 46.7 ] ] ] } } } }``` | Yes
$nearSphere | ```{ "Location.coordinates": { $nearSphere : [ -121, 46  ], $maxDistance: 0.50 } }``` | Yes
$geometry | ```{ "Location.coordinates": { $geoWithin: { $geometry: { type: "Polygon", coordinates: [ [ [ -121.9, 46.7 ], [ -121.5, 46.7 ], [ -121.5, 46.9 ], [ -121.9, 46.9 ], [ -121.9, 46.7 ] ] ] } } } }``` | Yes
$minDistance | ```{ "Location.coordinates": { $nearSphere : { $geometry: {type: "Point", coordinates: [ -121, 46 ]}, $minDistance: 1000, $maxDistance: 1000000 } } }``` | Yes
$maxDistance | ```{ "Location.coordinates": { $nearSphere : [ -121, 46  ], $maxDistance: 0.50 } }``` | Yes
$center | ```{ "Location.coordinates": { $geoWithin: { $center: [ [-121, 46], 1 ] } } }``` | Yes
$centerSphere | ```{ "Location.coordinates": { $geoWithin: { $centerSphere: [ [ -121, 46 ], 5 ] } } }``` | Yes
$box | ```{ "Location.coordinates": { $geoWithin: { $box:  [ [ 0, 0 ], [ -122, 47 ] ] } } }``` | Yes
$polygon | ```{ "Location.coordinates": { $near: { $geometry: { type: "Polygon", coordinates: [ [ [ -121.9, 46.7 ], [ -121.5, 46.7 ], [ -121.5, 46.9 ], [ -121.9, 46.9 ], [ -121.9, 46.7 ] ] ] } } } }``` | Yes

## <a name="additional-operators"></a>Dodatkowe operatory

Operator | Przykład | Uwagi 
--- | --- | --- |
$all | ```{ "Location.coordinates": { $all: [-121.758, 46.87] } }``` | 
$elemMatch | ```{ "Location.coordinates": { $elemMatch: {  $lt: 0 } } }``` |  
$size | ```{ "Location.coordinates": { $size: 2 } }``` | 
$comment |  ```{ "Location.coordinates": { $elemMatch: {  $lt: 0 } }, $comment: "Negative values"}``` | 
$text |  | Nieobsługiwane. Zamiast tego użyj $regex 

### <a name="methods"></a>Metody

Obsługiwane są następujące metody:

#### <a name="cursor-methods"></a>Metody kursora

Metoda | Przykład | Uwagi 
--- | --- | --- |
cursor.sort() | ```cursor.sort({ "Elevation": -1 })``` | Dokumenty bez klucza sortowania nie zwracana

## <a name="unique-indexes"></a>Unikatowe indeksy

Azure DB rozwiązania Cosmos indeksuje każdego pola w dokumentach, które są domyślnie zapisywane w bazie danych. Unikatowe indeksy upewnij się, że określone pole nie zawiera zduplikowane wartości dla wszystkich dokumentów w kolekcji, podobnie jak unikatowości sposób są zachowywane w kluczu domyślne "_id". Teraz można utworzyć niestandardowy indeksy w usłudze Azure DB rozwiązania Cosmos za pomocą polecenia createIndex, w tym ograniczenia "unique".

Unikatowe indeksy są dostępne dla wszystkich kont API bazy danych MongoDB.

## <a name="time-to-live-ttl"></a>Czas wygaśnięcia (TTL)

Azure DB rozwiązania Cosmos obsługuje względny czas wygaśnięcia (TTL) oparte na sygnatury czasowej dokumentu. Czas wygaśnięcia można włączyć dla interfejsu API bazy danych MongoDB kolekcji za pomocą [portalu Azure](https://portal.azure.com).

## <a name="user-and-role-management"></a>Zarządzanie użytkownikami i rola

Azure DB rozwiązania Cosmos nie obsługuje jeszcze użytkowników i ról. Kontrola dostępu oparta na rolach (RBAC) obsługuje bazę danych systemu Azure rozwiązania Cosmos i do odczytu / zapisu i tylko do odczytu haseł/kluczy, które można uzyskać za pośrednictwem [portalu Azure](https://portal.azure.com) (strona parametry połączenia).

## <a name="replication"></a>Replikacja

Azure DB rozwiązania Cosmos obsługuje automatyczne natywnego replikacji w najniższej warstwy. Istotą takiej logiki jest rozszerzony limit do osiągnięcia replikacji małe opóźnienia, globalnych. Azure DB rozwiązania Cosmos nie obsługuje polecenia ręczna replikacja.

## <a name="sharding"></a>Dzielenie na fragmenty

Azure DB rozwiązania Cosmos obsługuje dzielenia na fragmenty automatycznego, po stronie serwera. Azure DB rozwiązania Cosmos nie obsługuje polecenia ręcznego dzielenia na fragmenty.

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się, jak [Użyj Studio 3T](mongodb-mongochef.md) z interfejsu API dla bazy danych MongoDB.
- Dowiedz się, jak [Użyj Robo 3T](mongodb-robomongo.md) z interfejsu API dla bazy danych MongoDB.
- Eksploruj bazy danych Azure rozwiązania Cosmos z obsługą protokołu bazy danych mongodb [przykłady](mongodb-samples.md).
