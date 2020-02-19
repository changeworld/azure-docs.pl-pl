---
title: Interfejs API Azure Cosmos DB dla MongoDB (wersja 3,6) obsługiwane funkcje i składnia
description: Dowiedz się więcej o obsługiwanych funkcjach i składni interfejsu API Azure Cosmos DB (wersja 3,6).
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: overview
ms.date: 01/15/2020
author: sivethe
ms.author: sivethe
ms.openlocfilehash: cde731f9d9e673446bc4d08117004b028db2a7f9
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/19/2020
ms.locfileid: "77462466"
---
# <a name="azure-cosmos-dbs-api-for-mongodb-36-version-supported-features-and-syntax"></a>Azure Cosmos DB API for MongoDB (wersja 3,6): obsługiwane funkcje i składnia

Azure Cosmos DB to rozproszona globalnie, wielomodelowa usługa bazy danych firmy Microsoft. Można komunikować się z interfejsem API Azure Cosmos DB dla MongoDB przy użyciu dowolnego ze [sterowników](https://docs.mongodb.org/ecosystem/drivers)klienta MongoDB typu open source. Interfejs API usługi Azure Cosmos DB dla bazy danych MongoDB umożliwia korzystanie z istniejących sterowników klienta dzięki przestrzeganiu [protokołu przewodowego](https://docs.mongodb.org/manual/reference/mongodb-wire-protocol) MongoDB.

Za pomocą interfejsu API Azure Cosmos DB dla MongoDB można korzystać z zalet MongoDB, z których korzystasz, ze wszystkimi możliwościami przedsiębiorstwa, które Cosmos DB oferuje: [globalna dystrybucja](distribute-data-globally.md), [Automatyczna fragmentowania](partition-data.md), dostępność i gwarancje opóźnienia, szyfrowanie w spoczynku, tworzenie kopii zapasowych i wiele innych.

## <a name="protocol-support"></a>Obsługa protokołu

Interfejs API Azure Cosmos DB dla MongoDB jest domyślnie zgodny z serwerem MongoDB w wersji **3,6** dla nowych kont. Poniżej wymieniono obsługiwane operacje wraz z ewentualnymi ograniczeniami lub wyjątkami. Dowolny sterownik klienta działający zgodnie z tymi protokołami umożliwia połączenie z interfejsem API usługi Azure Cosmos DB dla bazy danych MongoDB. Należy pamiętać, że w przypadku korzystania z interfejsu API Azure Cosmos DB dla kont MongoDB wersja 3,6 kont ma punkt końcowy w formacie, `*.mongo.cosmos.azure.com` natomiast 3,2 wersja konta ma punkt końcowy w formacie `*.documents.azure.com`.

## <a name="query-language-support"></a>Obsługa języka zapytań

Interfejs API usługi Azure Cosmos DB dla bazy danych MongoDB zapewnia niemal pełną obsługę konstrukcji języka zapytań bazy danych MongoDB. Poniżej znajdziesz szczegółową listę obecnie obsługiwanych operacji, operatorów, etapów, poleceń i opcji.

## <a name="database-commands"></a>Polecenia bazy danych

Interfejs API usługi Azure Cosmos DB dla bazy danych MongoDB obsługuje następujące polecenia bazy danych:

### <a name="query-and-write-operation-commands"></a>Polecenia operacji zapytań i zapisu

|Polecenie  |Obsługiwane |
|---------|---------|
|delete | Yes |
|find | Yes     |
|findAndModify | Yes  |
|getLastError|   Yes |
|getMore  |  Yes  |
|getPrevError | Nie  |
|insert  |   Yes  |
|parallelCollectionScan  | Yes   |
|resetError |   Nie  |
|update  |   Yes  |
|[Zmień strumienie](mongodb-change-streams.md)  |  Yes  |
|GridFS |   Yes  |

### <a name="authentication-commands"></a>Polecenia uwierzytelniania

|Polecenie  |Obsługiwane |
|---------|---------|
|authenticate    |   Yes      |
|logout    |      Yes   |
|getnonce   |    Yes     |


### <a name="administration-commands"></a>Polecenia administracyjne

|Polecenie  |Obsługiwane |
|---------|---------|
|Kolekcje z ograniczeniami   |   Nie      |
|cloneCollectionAsCapped     |   Nie      |
|collMod     |   Nie      |
|collMod: expireAfterSeconds   |   Nie      |
|convertToCapped   |  Nie       |
|copydb     |  Nie       |
|create   |    Yes     |
|createIndexes     |  Yes       |
|currentOp     |  Yes       |
|drop     |   Yes      |
|dropDatabase     |  Yes       |
|dropIndexes     |   Yes      |
|filemd5    |   Yes      |
|killCursors    |  Yes       |
|killOp     |   Nie      |
|listCollections     |  Yes       |
|listDatabases     |  Yes       |
|listIndexes     |  Yes       |
|reIndex     |    Yes     |
|Nazwa nazwy     |    Nie     |
|connectionStatus    |     Nie    |

### <a name="diagnostics-commands"></a>Polecenia diagnostyki

|Polecenie  |Obsługiwane |
|---------|---------|
|buildInfo       |   Yes      |
|collStats    |  Yes       |
|connPoolStats     |  Nie       |
|connectionStatus     |  Nie       |
|dataSize     |   Nie      |
|dbhash    |    Nie     |
|dbStats     |   Yes      |
|uzasadnieni     | Nie        |
|Wyjaśnij: executionStats     |     Nie    |
|funkcje     |    Nie     |
|hostInfo     |   Nie      |
|listDatabases       |   Yes      |
|listCommands     |  Nie       |
|profiler     |  Nie       |
|serverStatus     |  Nie       |
|top     |    Nie     |
|whatsmyuri     |   Yes      |

<a name="aggregation-pipeline"/>

## <a name="aggregation-pipelinea"></a>Potok agregacji</a>

### <a name="aggregation-commands"></a>Polecenia agregacji

|Polecenie  |Obsługiwane |
|---------|---------|
|aggregate |   Yes  |
|count     |   Yes  |
|distinct  | Yes |
|mapReduce | Nie |

### <a name="aggregation-stages"></a>Etapy agregacji

|Polecenie  |Obsługiwane |
|---------|---------|
|$collStats |Nie|
|$project   |Yes|
|$match |Yes|
|$redact|   Yes|
|$limit |Yes|
|$skip  |Yes|
|$unwind|   Yes|
|$group |   Yes|
|$sample|       Yes|
|$sort  |Yes|
|$geoNear|  Nie|
|$lookup    |   Yes|
|$out       |Yes|
|$indexStats|       Nie|
|$facet |Nie|
|$bucket|   Nie|
|$bucketAuto|   Nie|
|$sortByCount|  Yes|
|$addFields |Yes|
|$replaceRoot|  Yes|
|$count |Yes|
|$currentOp|    Nie|
|$listLocalSessions |Nie|
|$listSessions  |Nie|
|$graphLookup   |Nie|

### <a name="boolean-expressions"></a>Wyrażenia logiczne

|Polecenie  |Obsługiwane |
|---------|---------|
|$and| Yes|
|$or|Yes|
|$not|Yes|

### <a name="set-expressions"></a>Stałe wyrażenia

|Polecenie  |Obsługiwane |
|---------|---------|
| $setEquals | Yes|
|$setIntersection|Yes|
| $setUnion|Yes|
| $setDifference|Yes|
| $setIsSubset|Yes|
| $anyElementTrue|Yes|
| $allElementsTrue|Yes|

### <a name="comparison-expressions"></a>Wyrażenia porównania

|Polecenie  |Obsługiwane |
|---------|---------|
|$cmp     |  Yes       |
|$eq|   Yes| 
|$gt |  Yes| 
|$gte|  Yes| 
|$lt    |Yes|
|$lte|  Yes| 
|$ne    |   Yes| 
|$in    |   Yes| 
|$nin   |   Yes| 

### <a name="arithmetic-expressions"></a>Wyrażenia arytmetyczne

|Polecenie  |Obsługiwane |
|---------|---------|
|$abs |  Yes       |
| $add |  Yes       |
| $ceil |  Yes       |
| $divide |  Yes       |
| $exp |  Yes       |
| $floor |  Yes       |
| $ln |  Yes       |
| $log |  Yes       |
| $log10 |  Yes       |
| $mod |  Yes       |
| $multiply |  Yes       |
| $pow |  Yes       |
| $sqrt |  Yes       |
| $subtract |  Yes       |
| $trunc |  Yes       |

### <a name="string-expressions"></a>Wyrażenia ciągu

|Polecenie  |Obsługiwane |
|---------|---------|
|$concat |  Yes       |
| $indexOfBytes|  Yes       |
| $indexOfCP|  Yes       |
| $split|  Yes       |
| $strLenBytes|  Yes       |
| $strLenCP|  Yes       |
| $strcasecmp|  Yes       |
| $substr|  Yes       |
| $substrBytes|  Yes       |
| $substrCP|  Yes       |
| $toLower|  Yes       |
| $toUpper|  Yes       |

### <a name="text-search-operator"></a>Operator wyszukiwania tekstu

|Polecenie  |Obsługiwane |
|---------|---------|
| $meta | Nie|

### <a name="array-expressions"></a>Wyrażenia tablicy

|Polecenie  |Obsługiwane |
|---------|---------|
|$arrayElemAt   |   Yes|
|$arrayToObject|    Yes|
|$concatArrays  |   Yes|
|$filter    |   Yes|
|$indexOfArray  |Yes|
|$isArray   |   Yes|
|$objectToArray |Yes|
|$range |Yes|
|$reverseArray  |   Yes|
|$reduce|   Yes|
|$size  |   Yes|
|$slice |   Yes|
|$zip   |   Yes|
|$in    |   Yes|

### <a name="variable-operators"></a>Operatory zmiennych

|Polecenie  |Obsługiwane |
|---------|---------|
|$map   |Nie|
|$let   |Yes|

### <a name="system-variables"></a>Zmienne systemu

|Polecenie  |Obsługiwane |
|---------|---------|
|$ $CURRENT| Yes|
|$ $DESCEND|     Yes|
|$ $KEEP     |Yes|
|$ $PRUNE    |   Yes|
|$ $REMOVE   |Yes|
|$ $ROOT     |Yes|

### <a name="literal-operator"></a>Operator literału

|Polecenie  |Obsługiwane |
|---------|---------|
|$literal   |Yes|

### <a name="date-expressions"></a>Wyrażenia daty

|Polecenie  |Obsługiwane |
|---------|---------|
|$dayOfYear |Yes    |
|$dayOfMonth|   Yes |
|$dayOfWeek |Yes    |
|$year  |Yes    |
|$month |Yes|   
|$week  |Yes    |
|$hour  |Yes    |
|$minute|   Yes|    
|$second    |Yes    |
|$millisecond|  Yes|    
|$dateToString  |Yes    |
|$isoDayOfWeek  |Yes    |
|$isoWeek   |Yes    |
|$dateFromParts|    Nie| 
|$dateToParts   |Nie |
|$dateFromString|   Nie|
|$isoWeekYear   |Yes    |

### <a name="conditional-expressions"></a>Wyrażenia warunkowe

|Polecenie  |Obsługiwane |
|---------|---------|
| $cond| Yes|
| $ifNull| Yes|
| $switch |Yes|

### <a name="data-type-operator"></a>Operator typu danych

|Polecenie  |Obsługiwane |
|---------|---------|
| $type| Yes|

### <a name="accumulator-expressions"></a>Wyrażenia akumulowana

|Polecenie  |Obsługiwane |
|---------|---------|
|$sum   |Yes    |
|$avg   |Yes    |
|$first|    Yes|
|$last  |Yes    |
|$max   |Yes    |
|$min   |Yes    |
|$push| Yes|
|$addToSet| Yes|
|$stdDevPop|    Nie  |
|$stdDevSamp|   Nie|

### <a name="merge-operator"></a>Operator scalania

|Polecenie  |Obsługiwane |
|---------|---------|
| $mergeObjects | Yes|

## <a name="data-types"></a>Typy danych

|Polecenie  |Obsługiwane |
|---------|---------|
|Podwójne |Yes    |
|Ciąg |Yes    |
|Obiekt |Yes    |
|Tablica  |Yes    |
|Binary Data    |Yes|   
|ObjectId   |Yes    |
|Wartość logiczna    |Yes    |
|Date   |Yes    |
|Null   |Yes    |
|32-bitowa liczba całkowita (int)   |Yes    |
|Znacznik czasu  |Yes    |
|64-bitowa liczba całkowita (Long)  |Yes    |
|MinKey |Yes    |
|MaxKey |Yes    |
|Decimal128 |Yes|   
|Wyrażenie regularne |Yes|
|JavaScript |Yes|
|JavaScript (z zakresem)|   Yes |
|Niezdefiniowane  |Yes    |

## <a name="indexes-and-index-properties"></a>Indeksy i właściwości indeksu

### <a name="indexes"></a>Indeksy

|Polecenie  |Obsługiwane |
|---------|---------|
|Indeks jednego pola |Yes    |
|Indeks złożony |Yes    |
|Indeks MultiKey |Yes    |
|Indeks tekstu |Nie|
|2dsphere   |Yes    |
|Indeks 2D   |Nie |
|Indeks z wartością skrótu   | Yes|

### <a name="index-properties"></a>Właściwości indeksu

|Polecenie  |Obsługiwane |
|---------|---------|
|TTL|   Yes |
|Unikatowe |Yes|
|Częściowo|   Nie|
|Bez uwzględniania wielkości liter   |Nie|
|Rozrzedzone |Nie |
|Tło|    Yes |

## <a name="operators"></a>Operatory

### <a name="logical-operators"></a>Operatory logiczne

|Polecenie  |Obsługiwane |
|---------|---------|
|$or    |   Yes|
|$and   |   Yes|
|$not   |   Yes|
|$nor   |   Yes| 

### <a name="element-operators"></a>Operatory elementu

|Polecenie  |Obsługiwane |
|---------|---------|
|$exists|   Yes|
|$type  |   Yes|

### <a name="evaluation-query-operators"></a>Operatory zapytań oceny

|Polecenie  |Obsługiwane |
|---------|---------|
|$expr  |   Nie|
|$jsonSchema    |   Nie|
|$mod   |   Yes|
|$regex |   Yes|
|$text  | Nie (nieobsługiwane. Użyj zamiast tego $regex.)| 
|$where |Nie| 

W zapytaniach $regex wyrażenia zakotwiczone w lewo umożliwiają wyszukiwanie indeksu. Jednak użycie modyfikatora „i” (wielkość liter nie ma znaczenia) oraz modyfikatora „m” (wiele wierszy) powoduje skanowanie kolekcji we wszystkich wyrażeniach.

Jeśli istnieje potrzeba dołączenia „$” lub „|”, najlepiej utworzyć dwa lub więcej zapytań regex. Na przykład, uwzględniając następujące oryginalne zapytanie: ```find({x:{$regex: /^abc$/})```, należy je zmodyfikować w następujący sposób:

```find({x:{$regex: /^abc/, x:{$regex:/^abc$/}})```.

Pierwsza część użyje indeksu, aby ograniczyć wyszukiwanie do dokumentów, które rozpoczynają się od ^abc, a druga część będzie dokładnie dopasowywana do wpisów. Operator kreski „|” działa jako funkcja „or” — zapytanie ```find({x:{$regex: /^abc|^def/})``` pasuje do dokumentów, w których pole „x” ma wartości zaczynające się od „abc” lub „def”. Aby korzystać z indeksu, zaleca się podzielenie zapytania na dwa różne zapytania połączone operatorem $or: ```find( {$or : [{x: $regex: /^abc/}, {$regex: /^def/}] })```.

### <a name="array-operators"></a>Operatory tablic

|Polecenie  |Obsługiwane | 
|---------|---------|
| $all | Yes| 
| $elemMatch | Yes| 
| $size | Yes | 

### <a name="comment-operator"></a>Operator komentarza

|Polecenie  |Obsługiwane | 
|---------|---------|
$comment |Yes| 

### <a name="projection-operators"></a>Operatory projekcji

|Polecenie  |Obsługiwane |
|---------|---------|
|$elemMatch |Yes|
|$meta| Nie|
|$slice | Yes|

### <a name="update-operators"></a>Operatory aktualizacji

#### <a name="field-update-operators"></a>Operatory do aktualizacji pól

|Polecenie  |Obsługiwane |
|---------|---------|
|$inc   |   Yes|
|$mul   |   Yes|
|$rename    |   Yes|
|$setOnInsert|  Yes|
|$set   |Yes|
|$unset| Yes|
|$min   |Yes|
|$max   |Yes|
|$currentDate   | Yes|

#### <a name="array-update-operators"></a>Operatory do aktualizacji tablic

|Polecenie  |Obsługiwane |
|---------|---------|
|$  |Yes|
|$[]|   Yes|
|$ [<identifier>]|   Yes|
|$addToSet  |Yes|
|$pop   |Yes|
|$pullAll|  Yes|
|$pull  |Yes|
|$push  |Yes|
|$pushAll| Yes|


#### <a name="update-modifiers"></a>Aktualizuj Modyfikatory

|Polecenie  |Obsługiwane |
|---------|---------|
|$each  |   Yes|
|$slice |Yes|
|$sort  |Yes|
|$position  |Yes|

#### <a name="bitwise-update-operator"></a>Operator do aktualizacji Bitwise

|Polecenie  |Obsługiwane |
|---------|---------|
| $bit  |   Yes|    
|$bitsAllSet    |   Nie|
|$bitsAnySet    |   Nie|
|$bitsAllClear  |Nie|
|$bitsAnyClear  |Nie|

### <a name="geospatial-operators"></a>Operatory danych geoprzestrzennych

Operator | Obsługiwane| 
--- | --- |
$geoWithin | Yes |
$geoIntersects | Yes | 
$near |  Yes |
$nearSphere |  Yes |
$geometry |  Yes |
$minDistance | Yes |
$maxDistance | Yes |
$center | Yes |
$centerSphere | Yes |
$box | Yes |
$polygon |  Yes |

## <a name="cursor-methods"></a>Metody kursora

|Polecenie  |Obsługiwane |
|---------|---------|
|Cursor. batchSize () |   Yes|
|Cursor. Close () |Yes|
|Cursor. IsClosed ()|     Yes|
|Cursor. Collation ()|    Nie|
|Cursor. comment ()   |Yes|
|Cursor. Count () |Yes|
|Cursor. Wyjaśnij ()|  Nie|
|Cursor. forEach ()   |Yes|
|Cursor. hasNext ()   |Yes|
|Cursor. Hint ()  |Yes|
|Cursor. iswyczerpaład ()|  Yes|
|Cursor. itcount ()   |Yes|
|Cursor. limit () |Yes|
|Cursor. map ()   |Yes|
|Cursor. maxScan ()   |Yes|
|Cursor. maxTimeMS ()|    Yes|
|Cursor. max ()   |Yes|
|Cursor. min ()   |Yes|
|Cursor. Next ()| Yes|
|Cursor. noCursorTimeout ()   |Nie|
|Cursor. objsLeftInBatch ()   |Yes|
|Cursor. dość ()|   Yes|
|Cursor. readConcern ()|  Yes|
|Cursor. readPref ()      |Yes|
|Cursor. returnKey () |Nie|
|Cursor. showRecordId ()| Nie|
|Cursor. size ()  |Nes|
|Cursor. Skip ()  |Yes|
|cursor.sort()  |   Yes|
|Cursor. Add()| Nie|
|Cursor. ToArray — ()   |Yes|

## <a name="sort-operations"></a>Operacje sortowania

W przypadku używania operacji `findOneAndUpdate` obsługiwane są operacje sortowania względem pojedynczego pola, ale nie względem wielu pól.

## <a name="unique-indexes"></a>Indeksy unikatowe

Indeksy unikatowe zapewniają, że wartości w danym polu nie dublują się we wszystkich dokumentach w kolekcji; również klucz domyślny „_id” jest kluczem unikatowym. W usłudze Cosmos DB można tworzyć niestandardowe indeksy, używając polecenia createIndex, w którym można stosować ograniczenie „unique”.

## <a name="time-to-live-ttl"></a>Czas wygaśnięcia (TTL)

Usługa Cosmos DB obsługuje czas wygaśnięcia (TTL) na podstawie znacznika czasu dokumentu. Czas wygaśnięcia można włączyć dla kolekcji, przechodząc do witryny [Azure Portal](https://portal.azure.com).

## <a name="user-and-role-management"></a>Zarządzanie użytkownikami i rolami

Usługa Cosmos DB nie obsługuje jeszcze użytkowników i ról. Jednakże Cosmos DB obsługuje funkcję kontroli dostępu opartej na rolach (RBAC) oraz hasła do odczytu i zapisu oraz klucze, które można uzyskać za pośrednictwem [Azure Portal](https://portal.azure.com) (strona parametrów połączenia).

## <a name="replication"></a>Replikacja

Usługa Cosmos DB obsługuje automatyczną, natywną replikację na najniższych warstwach. Ta logika została rozszerzona, aby osiągnąć małe opóźnienia oraz replikację globalną. Usługa Cosmos DB nie obsługuje poleceń dotyczących replikacji ręcznej.

## <a name="write-concern"></a>Ustawienie Write Concern

Niektóre aplikacje polegają na ustawieniu [Write Concern](https://docs.mongodb.com/manual/reference/write-concern/), które określa liczbę odpowiedzi wymaganych podczas operacji zapisu. Ze względu na sposób obsługi replikacji w tle w usłudze Cosmos DB wszystkie operacje zapisu mają domyślnie automatycznie ustawioną opcję Quorum (Kworum). Ustawienia Write Concern określone w kodzie klienta są ignorowane. Aby dowiedzieć się więcej, zobacz [Maksymalizowanie dostępności i wydajności za pomocą poziomów spójności](consistency-levels.md).

## <a name="sharding"></a>Dzielenie na fragmenty

Usługa Azure Cosmos DB obsługuje automatyczne dzielenie na fragmenty po stronie serwera. Zarządza on fragmentu tworzeniem, umieszczaniem i zrównoważeniem automatycznie. Azure Cosmos DB nie obsługuje ręcznych poleceń fragmentowania, co oznacza, że nie trzeba Wywoływanie poleceń takich jak addShard, balancerStart, moveChunk itp. Wystarczy określić klucz fragmentu podczas tworzenia kontenerów lub wykonywania zapytań dotyczących danych.

## <a name="sessions"></a>Sesje

Azure Cosmos DB nie obsługuje jeszcze poleceń sesji po stronie serwera.

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać więcej informacji, zobacz [funkcje wersji Mongo 3,6](https://devblogs.microsoft.com/cosmosdb/azure-cosmos-dbs-api-for-mongodb-now-supports-server-version-3-6/)
- Dowiedz się, jak [korzystać z programu Studio 3T](mongodb-mongochef.md) za pomocą interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB.
- Dowiedz się, jak [korzystać z programu Robo 3T](mongodb-robomongo.md) za pomocą interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB.
- Eksploruj [przykłady](mongodb-samples.md) bazy danych MongoDB za pomocą interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB.

<sup>Uwaga: w tym artykule opisano funkcję Azure Cosmos DB, która zapewnia zgodność protokołu telekomunikacyjnych z bazami danych MongoDB. Firma Microsoft nie uruchamia MongoDB baz danych w celu zapewnienia tej usługi. Azure Cosmos DB nie jest powiązany z MongoDB, Inc.</sup>
