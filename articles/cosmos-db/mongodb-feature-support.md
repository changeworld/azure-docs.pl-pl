---
title: Obsługa funkcji Azure Cosmos DB dla bazy danych MongoDB | Microsoft Docs
description: Dowiedz się więcej o obsłudze funkcji dostępnych w interfejsach API bazy danych MongoDB usługi Azure Cosmos DB dla bazy danych MongoDB w wersji 3.4.
services: cosmos-db
author: alekseys
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-mongo
ms.devlang: na
ms.topic: overview
ms.date: 11/15/2017
ms.author: alekseys
ms.openlocfilehash: 9202e8eb328f098f7ab68a18f4629a95ecc10991
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/05/2018
ms.locfileid: "34796359"
---
# <a name="mongodb-api-support-for-mongodb-features-and-syntax"></a>Obsługa interfejsu API bazy danych MongoDB dla funkcji i składni bazy danych MongoDB

Azure Cosmos DB to rozproszona globalnie, wielomodelowa usługa bazy danych firmy Microsoft. Możesz komunikować się z interfejsem API bazy danych MongoDB za pośrednictwem dowolnych [sterowników](https://docs.mongodb.org/ecosystem/drivers) klienta bazy danych MongoDB typu „open source”. Interfejs API bazy danych MongoDB umożliwia korzystanie z istniejących sterowników klienta dzięki przestrzeganiu [protokołu przewodowego](https://docs.mongodb.org/manual/reference/mongodb-wire-protocol) MongoDB.

Używając interfejsu API bazy danych MongoDB usługi Azure Cosmos DB, można korzystać z dobrze znanych zalet interfejsów API bazy danych MongoDB oraz wszystkich funkcji na poziomie korporacyjnym dostarczanych przez usługę Azure Cosmos DB: [globalnej dystrybucji](distribute-data-globally.md), [automatycznego fragmentowania](partition-data.md), gwarancji dostępności i opóźnień, automatycznego indeksowania każdego pola, szyfrowania magazynowanego, tworzenia kopii zapasowych itd.

## <a name="mongodb-query-language-support"></a>Obsługa języka zapytań bazy danych MongoDB

Interfejs API bazy danych MongoDB usługi Azure Cosmos DB zapewnia niemal pełną obsługę konstrukcji języka zapytań bazy danych MongoDB. Poniżej można znaleźć szczegółową listę obecnie obsługiwanych operacji, operatorów, etapów, poleceń i opcji.


## <a name="database-commands"></a>Polecenia bazy danych

Usługa Azure Cosmos DB obsługuje następujące polecenia bazy danych na wszystkich kontach interfejsu API bazy danych MongoDB. 

### <a name="query-and-write-operation-commands"></a>Polecenia operacji zapytań i zapisu
- delete
- find
- findAndModify
- getLastError
- getMore
- insert
- update

### <a name="authentication-commands"></a>Polecenia uwierzytelniania
- logout
- authenticate
- getnonce

### <a name="administration-commands"></a>Polecenia administracyjne
- dropDatabase
- listCollections
- drop
- create
- filemd5
- createIndexes
- listIndexes
- dropIndexes
- connectionStatus
- reIndex

### <a name="diagnostics-commands"></a>Polecenia diagnostyki
- buildInfo
- collStats
- dbStats
- hostInfo
- listDatabases
- whatsmyuri

<a name="aggregation-pipeline"/>

## <a name="aggregation-pipelinea"></a>Potok agregacji</a>

Usługa Azure Cosmos DB obsługuje potok agregacji w publicznej wersji zapoznawczej. Instrukcje dotyczące dołączenia do publicznej wersji zapoznawczej znajdują się na [blogu platformy Azure](https://aka.ms/mongodb-aggregation).

### <a name="aggregation-commands"></a>Polecenia agregacji
- aggregate
- count
- distinct

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
- $and
- $or
- $not

#### <a name="set-expressions"></a>Stałe wyrażenia
- $setEquals
- $setIntersection
- $setUnion
- $setDifference
- $setIsSubset
- $anyElementTrue
- $allElementsTrue

#### <a name="comparison-expressions"></a>Wyrażenia porównania
- $cmp
- $eq
- $gt
- $gte
- $lt
- $lte
- $ne

#### <a name="arithmetic-expressions"></a>Wyrażenia arytmetyczne
- $abs
- $add
- $ceil
- $divide
- $exp
- $floor
- $ln
- $log
- $log10
- $mod
- $multiply
- $pow
- $sqrt
- $subtract
- $trunc

#### <a name="string-expressions"></a>Wyrażenia ciągu
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
- $in

#### <a name="date-expressions"></a>Wyrażenia daty
- $dayOfYear
- $dayOfMonth
- $dayOfWeek
- $year
- $month
- $week
- $hour
- $minute
- $second
- $millisecond
- $isoDayOfWeek
- $isoWeek

#### <a name="conditional-expressions"></a>Wyrażenia warunkowe
- $cond
- $ifNull

## <a name="aggregation-accumulators"></a>Akumulatory agregacji
- $sum
- $avg
- $first
- $last
- $max
- $min
- $push
- $addToSet

## <a name="operators"></a>Operatory

Poniżej znajdują się obsługiwane operatory razem z odpowiednimi przykładami ich użycia. Warto zapoznać się z przykładowym dokumentem użytym w zapytaniach poniżej:

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
$in | ``` { "Volcano Name": { $in: ["St. Helens", "Rainier", "Glacier Peak"] } } ``` |  | -
$nin | ``` { "Volcano Name": { $nin: ["Lassen Peak", "Hood", "Baker"] } } ``` | | -
$or | ``` { $or: [ { Elevation: { $lt: 4000 } }, { "Volcano Name": "Rainier" } ] } ``` |  | -
$and | ``` { $and: [ { Elevation: { $gt: 4000 } }, { "Volcano Name": "Rainier" } ] } ``` |  | -
$not | ``` { "Elevation": { $not: { $gt: 5000 } } } ```|  | -
$nor | ``` { $nor: [ { "Elevation": { $lt: 4000 } }, { "Volcano Name": "Baker" } ] } ``` |  | -
$exists | ``` { "Status": { $exists: true } } ```|  | -
$type | ``` { "Status": { $type: "string" } } ```|  | -
$mod | ``` { "Elevation": { $mod: [ 4, 0 ] } } ``` |  | -
$regex | ``` { "Volcano Name": { $regex: "^Rain"} } ```|  | -

### <a name="notes"></a>Uwagi

W zapytaniach $regex wyrażenia zakotwiczone z lewej strony umożliwiają wyszukiwanie indeksu. Jednak użycie modyfikatora „i” (wielkość liter nie ma znaczenia) oraz modyfikatora „m” (wiele wierszy) powoduje skanowanie kolekcji we wszystkich wyrażeniach.
Jeśli istnieje potrzeba dołączenia „$” lub „|”, najlepiej utworzyć dwa lub więcej zapytań regex. Jeśli na przykład oryginalne zapytanie jest następujące: ```find({x:{$regex: /^abc$/})``` należy je zmodyfikować w następujący sposób: ```find({x:{$regex: /^abc/, x:{$regex:/^abc$/}})```.
Pierwsza część użyje indeksu, aby ograniczyć wyszukiwanie do dokumentów, które rozpoczynają się od ^abc, a druga część będzie dokładnie dopasowywana do wpisów. Operator kreski „|” działa jako funkcja „or” — zapytanie ```find({x:{$regex: /^abc|^def/})``` pasuje do dokumentów, w których pole „x” ma wartości zaczynające się od „abc” lub „def”. Aby korzystać z indeksu, zaleca się podzielenie zapytania na dwa różne zapytania połączone operatorem $or: ```find( {$or : [{x: $regex: /^abc/}, {$regex: /^def/}] })```.

### <a name="update-operators"></a>Operatory aktualizacji

#### <a name="field-update-operators"></a>Operatory do aktualizacji pól
- $inc
- $mul
- $rename
- $setOnInsert
- $set
- $unset
- $min
- $max
- $currentDate

#### <a name="array-update-operators"></a>Operatory do aktualizacji tablic
- $addToSet
- $pop
- $pullAll
- $pull  (Uwaga: operator $pull z warunkiem nie jest obsługiwany)
- $pushAll
- $push
- $each
- $slice
- $sort
- $position

#### <a name="bitwise-update-operator"></a>Operator do aktualizacji Bitwise
- $bit

### <a name="geospatial-operators"></a>Operatory danych geoprzestrzennych

Operator | Przykład 
--- | --- |
$geoWithin | ```{ "Location.coordinates": { $geoWithin: { $centerSphere: [ [ -121, 46 ], 5 ] } } }``` | Yes
$geoIntersects |  ```{ "Location.coordinates": { $geoIntersects: { $geometry: { type: "Polygon", coordinates: [ [ [ -121.9, 46.7 ], [ -121.5, 46.7 ], [ -121.5, 46.9 ], [ -121.9, 46.9 ], [ -121.9, 46.7 ] ] ] } } } }``` | Yes
$near | ```{ "Location.coordinates": { $near: { $geometry: { type: "Polygon", coordinates: [ [ [ -121.9, 46.7 ], [ -121.5, 46.7 ], [ -121.5, 46.9 ], [ -121.9, 46.9 ], [ -121.9, 46.7 ] ] ] } } } }``` | Yes
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
$text |  | Nieobsługiwane. Zamiast tego użyj operatora $regex 

### <a name="methods"></a>Metody

Obsługiwane są następujące metody:

#### <a name="cursor-methods"></a>Metody kursora

Metoda | Przykład | Uwagi 
--- | --- | --- |
cursor.sort() | ```cursor.sort({ "Elevation": -1 })``` | Dokumenty bez klucza sortowania nie zostaną zwrócone

## <a name="unique-indexes"></a>Indeksy unikatowe

Usługa Azure Cosmos DB indeksuje każde pole w dokumentach, które są domyślnie zapisywane w bazie danych. Indeksy unikatowe zapewniają, że wartości w danym polu nie dublują się we wszystkich dokumentach w kolekcji; również klucz domyślny „_id” jest kluczem unikatowym. Teraz w usłudze Azure Cosmos DB można tworzyć niestandardowe indeksy – za pomocą polecenia createIndex, w którym można używać ograniczenia „unique”.

Indeksy unikatowe są dostępne dla wszystkich kont interfejsu API bazy danych MongoDB.

## <a name="time-to-live-ttl"></a>Czas wygaśnięcia (TTL)

Usługa Azure Cosmos DB obsługuje względny czas wygaśnięcia (TTL) na podstawie znacznika czasu dokumentu. Czas wygaśnięcia można włączyć dla kolekcji interfejsu API bazy danych MongoDB w witrynie [Azure Portal](https://portal.azure.com).

## <a name="user-and-role-management"></a>Zarządzanie użytkownikami i rolami

Usługa Azure Cosmos DB nie obsługuje jeszcze użytkowników i ról. Usługa Azure Cosmos DB obsługuje kontrolę dostępu opartą na rolach (RBAC) oraz hasła/klucze do odczytu-zapisu i tylko do odczytu, które można uzyskać w witrynie [Azure Portal](https://portal.azure.com) (na stronie Parametry połączenia).

## <a name="replication"></a>Replikacja

Usługa Azure Cosmos DB obsługuje automatyczną, natywną replikację na najniższych warstwach. Ta logika została rozszerzona, aby osiągnąć małe opóźnienia oraz replikację globalną. Usługa Azure Cosmos DB nie obsługuje poleceń dotyczących replikacji ręcznej.

## <a name="sharding"></a>Dzielenie na fragmenty

Usługa Azure Cosmos DB obsługuje automatyczne dzielenie na fragmenty po stronie serwera. Usługa Azure Cosmos DB nie obsługuje poleceń dotyczących ręcznego dzielenia na fragmenty.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [używać programu Studio 3T](mongodb-mongochef.md) z interfejsem API dla bazy danych MongoDB.
- Dowiedz się, jak [używać programu Robo 3T](mongodb-robomongo.md) z interfejsem API dla bazy danych MongoDB.
- Eksploruj usługę Azure Cosmos DB, korzystając z pomocy dla protokołów do bazy danych [MongoDB](mongodb-samples.md).
