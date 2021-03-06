---
title: Interfejs API usługi Azure Cosmos DB dla mongodb (wersja 3.6) obsługiwane funkcje i składni
description: Dowiedz się więcej o interfejsie API usługi Azure Cosmos DB dla obsługiwanych funkcji i składni usługi MongoDB (wersja 3.6).
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: overview
ms.date: 01/15/2020
author: sivethe
ms.author: sivethe
ms.openlocfilehash: fe80b7343f67b06981707081931e35142985688e
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2020
ms.locfileid: "80668794"
---
# <a name="azure-cosmos-dbs-api-for-mongodb-36-version-supported-features-and-syntax"></a>Interfejs API usługi Azure Cosmos DB dla bazy danych MongoDB (wersja 3.6): obsługiwane funkcje i składnia

Azure Cosmos DB to rozproszona globalnie, wielomodelowa usługa bazy danych firmy Microsoft. Interfejs API usługi Azure Cosmos DB dla bazy danych MongoDB można komunikować się przy użyciu dowolnego [sterownika](https://docs.mongodb.org/ecosystem/drivers)klienta mongodb typu open source. Interfejs API usługi Azure Cosmos DB dla bazy danych MongoDB umożliwia korzystanie z istniejących sterowników klienta dzięki przestrzeganiu [protokołu przewodowego](https://docs.mongodb.org/manual/reference/mongodb-wire-protocol) MongoDB.

Korzystając z interfejsu API usługi Azure Cosmos DB dla mongodb, można korzystać z zalet MongoDB, który jesteś przyzwyczajony do, ze wszystkimi możliwościami przedsiębiorstwa, które zapewnia usługi Cosmos DB: [dystrybucja](distribute-data-globally.md) [globalna, automatyczne dzielenie na fragmenty,](partition-data.md)gwarancje dostępności i opóźnienia, szyfrowanie w spoczynku, kopie zapasowe i wiele więcej.

## <a name="protocol-support"></a>Obsługa protokołu

Interfejs API usługi Azure Cosmos DB dla bazy danych MongoDB jest domyślnie zgodny z serwerem MongoDB w wersji **3.6** dla nowych kont. Poniżej wymieniono obsługiwane operacje wraz z ewentualnymi ograniczeniami lub wyjątkami. Dowolny sterownik klienta działający zgodnie z tymi protokołami umożliwia połączenie z interfejsem API usługi Azure Cosmos DB dla bazy danych MongoDB. Należy zauważyć, że podczas korzystania z interfejsu API usługi Azure Cosmos DB dla kont MongoDB, wersja 3.6 kont mają punkt końcowy w formacie, `*.mongo.cosmos.azure.com` podczas gdy wersja 3.2 kont mają punkt końcowy w formacie `*.documents.azure.com`.

## <a name="query-language-support"></a>Obsługa języka zapytań

Interfejs API usługi Azure Cosmos DB dla bazy danych MongoDB zapewnia niemal pełną obsługę konstrukcji języka zapytań bazy danych MongoDB. Poniżej znajduje się szczegółowa lista aktualnie obsługiwanych operacji, operatorów, etapów, poleceń i opcji.

## <a name="database-commands"></a>Polecenia bazy danych

Interfejs API usługi Azure Cosmos DB dla bazy danych MongoDB obsługuje następujące polecenia bazy danych:

### <a name="query-and-write-operation-commands"></a>Polecenia operacji zapytań i zapisu

|Polecenie  |Obsługiwane |
|---------|---------|
|delete | Tak |
|find | Tak     |
|findAndModify | Tak  |
|getLastError|   Tak |
|getMore  |  Tak  |
|getPrevError | Nie  |
|insert  |   Tak  |
|parallelCollectionScan  | Tak   |
|resetError |    Nie  |
|update  |   Tak  |
|[Zmiana strumieni](mongodb-change-streams.md)  |  Tak  |
|GridFS (FS) |   Tak  |

### <a name="authentication-commands"></a>Polecenia uwierzytelniania

|Polecenie  |Obsługiwane |
|---------|---------|
|authenticate    |   Tak      |
|logout    |      Tak   |
|getnonce   |    Tak     |


### <a name="administration-commands"></a>Polecenia administracyjne

|Polecenie  |Obsługiwane |
|---------|---------|
|Kolekcje ograniczone   |   Nie      |
|cloneCollectionAsCapped     |   Nie      |
|collMod (collMod)     |   Nie      |
|collMod: expireAfterSeconds   |   Nie      |
|convertToCaped (ConvertToCaped)   |  Nie       |
|copydb (kopia db)     |  Nie       |
|create   |    Tak     |
|createIndexes     |  Tak       |
|currentOp     |  Tak       |
|drop     |   Tak      |
|dropDatabase     |  Tak       |
|dropIndexes     |   Tak      |
|filemd5    |   Tak      |
|killCursors ( killCursors )    |  Tak       |
|killOp (zabijOp)     |   Nie      |
|listCollections     |  Tak       |
|listDatabases     |  Tak       |
|listIndexes     |  Tak       |
|reIndex     |    Tak     |
|renameCollection (zmiana nazwy)     |    Nie     |
|connectionStatus    |     Nie    |

### <a name="diagnostics-commands"></a>Polecenia diagnostyki

|Polecenie  |Obsługiwane |
|---------|---------|
|buildInfo         |   Tak      |
|collStats    |  Tak       |
|connPoolStats (connPoolStats)     |  Nie       |
|connectionStatus     |  Nie       |
|Datasize     |   Nie      |
|dbHash (dbHash)    |    Nie     |
|dbStats     |   Tak      |
|Wyjaśnić     | Nie        |
|wyjaśnić: executionStats     |     Nie    |
|funkcje     |    Nie     |
|hostInfo     |   Nie      |
|listDatabases         |   Tak      |
|listaZakłady     |  Nie       |
|Profiler     |  Nie       |
|status serwera     |  Nie       |
|top     |    Nie     |
|whatsmyuri     |   Tak      |

<a name="aggregation-pipeline"/>

## <a name="aggregation-pipelinea"></a>Potok agregacji</a>

### <a name="aggregation-commands"></a>Polecenia agregacji

|Polecenie  |Obsługiwane |
|---------|---------|
|aggregate |   Tak  |
|count     |   Tak  |
|distinct  | Tak |
|mapaReduce | Nie |

### <a name="aggregation-stages"></a>Etapy agregacji

|Polecenie  |Obsługiwane |
|---------|---------|
|$collStats    |Nie|
|$project    |Tak|
|$match    |Tak|
|$redact|    Tak|
|$limit    |Tak|
|$skip    |Tak|
|$unwind|    Tak|
|$group    |    Tak|
|$sample|        Tak|
|$sort    |Tak|
|$geoNear|    Nie|
|$lookup    |    Tak|
|$out        |Tak|
|$indexStats|        Nie|
|$facet    |Nie|
|$bucket|    Nie|
|$bucketAuto|    Nie|
|$sortByCount|    Tak|
|$addFields    |Tak|
|$replaceRoot|    Tak|
|$count    |Tak|
|$currentOp|    Nie|
|$listLocalSessions    |Nie|
|$listSessions    |Nie|
|$graphLookup    |Nie|

### <a name="boolean-expressions"></a>Wyrażenia logiczne

|Polecenie  |Obsługiwane |
|---------|---------|
|$and| Tak|
|$or|Tak|
|$not|Tak|

### <a name="set-expressions"></a>Stałe wyrażenia

|Polecenie  |Obsługiwane |
|---------|---------|
| $setEquals | Tak|
|$setIntersection|Tak|
| $setUnion|Tak|
| $setDifference|Tak|
| $setIsSubset|Tak|
| $anyElementTrue|Tak|
| $allElementsTrue|Tak|

### <a name="comparison-expressions"></a>Wyrażenia porównania

|Polecenie  |Obsługiwane |
|---------|---------|
|$cmp     |  Tak       |
|$eq|    Tak| 
|$gt |    Tak| 
|$gte|    Tak| 
|$lt    |Tak|
|$lte|    Tak| 
|$ne    |    Tak| 
|$in    |    Tak| 
|$nin    |    Tak| 

### <a name="arithmetic-expressions"></a>Wyrażenia arytmetyczne

|Polecenie  |Obsługiwane |
|---------|---------|
|$abs |  Tak       |
| $add |  Tak       |
| $ceil |  Tak       |
| $divide |  Tak       |
| $exp |  Tak       |
| $floor |  Tak       |
| $ln |  Tak       |
| $log |  Tak       |
| $log10 |  Tak       |
| $mod |  Tak       |
| $multiply |  Tak       |
| $pow |  Tak       |
| $sqrt |  Tak       |
| $subtract |  Tak       |
| $trunc |  Tak       |

### <a name="string-expressions"></a>Wyrażenia ciągu

|Polecenie  |Obsługiwane |
|---------|---------|
|$concat |  Tak       |
| $indexOfBytes|  Tak       |
| $indexOfCP|  Tak       |
| $split|  Tak       |
| $strLenBytes|  Tak       |
| $strLenCP|  Tak       |
| $strcasecmp|  Tak       |
| $substr|  Tak       |
| $substrBytes|  Tak       |
| $substrCP|  Tak       |
| $toLower|  Tak       |
| $toUpper|  Tak       |

### <a name="text-search-operator"></a>Operator wyszukiwania tekstu

|Polecenie  |Obsługiwane |
|---------|---------|
| $meta | Nie|

### <a name="array-expressions"></a>Wyrażenia tablicy

|Polecenie  |Obsługiwane |
|---------|---------|
|$arrayElemAt    |    Tak|
|$arrayToObject|    Tak|
|$concatArrays    |    Tak|
|$filter    |    Tak|
|$indexOfArray    |Tak|
|$isArray    |    Tak|
|$objectToArray    |Tak|
|$range    |Tak|
|$reverseArray    |    Tak|
|$reduce|    Tak|
|$size    |    Tak|
|$slice    |    Tak|
|$zip    |    Tak|
|$in    |    Tak|

### <a name="variable-operators"></a>Operatory zmiennych

|Polecenie  |Obsługiwane |
|---------|---------|
|$map    |Nie|
|$let    |Tak|

### <a name="system-variables"></a>Zmienne systemu

|Polecenie  |Obsługiwane |
|---------|---------|
|$CURRENT usd|    Tak|
|$DESCEND usd|        Tak|
|$KEEP usd        |Tak|
|$PRUNE usd    |    Tak|
|$REMOVE usd    |Tak|
|$ROOT usd        |Tak|

### <a name="literal-operator"></a>Operator dosłowny

|Polecenie  |Obsługiwane |
|---------|---------|
|$literal    |Tak|

### <a name="date-expressions"></a>Wyrażenia daty

|Polecenie  |Obsługiwane |
|---------|---------|
|$dayOfYear    |Tak    |
|$dayOfMonth|    Tak    |
|$dayOfWeek    |Tak    |
|$year    |Tak    |
|$month    |Tak|    
|$week    |Tak    |
|$hour    |Tak    |
|$minute|    Tak|    
|$second    |Tak    |
|$millisecond|    Tak|    
|$dateToString    |Tak    |
|$isoDayOfWeek    |Tak    |
|$isoWeek    |Tak    |
|$dateFromParts|    Nie|    
|$dateToParts    |Nie    |
|$dateFromString|    Nie|
|$isoWeekYear    |Tak    |

### <a name="conditional-expressions"></a>Wyrażenia warunkowe

|Polecenie  |Obsługiwane |
|---------|---------|
| $cond| Tak|
| $ifNull| Tak|
| $switch |Tak|

### <a name="data-type-operator"></a>Operator typu danych

|Polecenie  |Obsługiwane |
|---------|---------|
| $type| Tak|

### <a name="accumulator-expressions"></a>Wyrażenia akumulatora

|Polecenie  |Obsługiwane |
|---------|---------|
|$sum    |Tak    |
|$avg    |Tak    |
|$first|    Tak|
|$last    |Tak    |
|$max    |Tak    |
|$min    |Tak    |
|$push|    Tak|
|$addToSet|    Tak|
|$stdDevPop|    Nie    |
|$stdDevSamp|    Nie|

### <a name="merge-operator"></a>Operator scalania

|Polecenie  |Obsługiwane |
|---------|---------|
| $mergeObjects | Tak|

## <a name="data-types"></a>Typy danych

|Polecenie  |Obsługiwane |
|---------|---------|
|Double    |Tak    |
|Ciąg    |Tak    |
|Obiekt    |Tak    |
|Tablica    |Tak    |
|Dane binarne    |Tak|    
|ObjectId    |Tak    |
|Wartość logiczna    |Tak    |
|Data    |Tak    |
|Null    |Tak    |
|32-bitowa ćda (int)    |Tak    |
|Znacznik czasu    |Tak    |
|64-bitowa ć całkowita (długa)    |Tak    |
|MinKey (Klucz min.)    |Tak    |
|Klawisz MaxKey    |Tak    |
|Dziesiętny128    |Tak|    
|Wyrażenie regularne    |Tak|
|JavaScript    |Tak|
|JavaScript (z zakresem)|    Tak    |
|Niezdefiniowane    |Tak    |

## <a name="indexes-and-index-properties"></a>Indeksy i właściwości indeksu

### <a name="indexes"></a>Indeksy

|Polecenie  |Obsługiwane |
|---------|---------|
|Indeks pojedynczego pola    |Tak    |
|Indeks złożony    |Tak    |
|Indeks multikey    |Tak    |
|Indeks tekstu    |Nie|
|Sfera 2dsphere    |Tak    |
|Indeks 2d    |Nie    |
|Indeks mieszania    | Tak|

### <a name="index-properties"></a>Właściwości indeksu

|Polecenie  |Obsługiwane |
|---------|---------|
|TTL|    Tak    |
|Unikatowe    |Tak|
|Częściowo|    Nie|
|Bez uwzględniania wielkości liter    |Nie|
|Rozrzedzone    |Nie |
|Tło|    Tak |

## <a name="operators"></a>Operatory

### <a name="logical-operators"></a>Operatory logiczne

|Polecenie  |Obsługiwane |
|---------|---------|
|$or    |    Tak|
|$and    |    Tak|
|$not    |    Tak|
|$nor    |    Tak| 

### <a name="element-operators"></a>Operatory elementów

|Polecenie  |Obsługiwane |
|---------|---------|
|$exists|    Tak|
|$type    |    Tak|

### <a name="evaluation-query-operators"></a>Operatory zapytań ewaluacyjnych

|Polecenie  |Obsługiwane |
|---------|---------|
|$expr    |    Nie|
|$jsonSchema    |    Nie|
|$mod    |    Tak|
|$regex |    Tak|
|$text    | Nie (nie jest obsługiwany. Zamiast tego użyj $regex).)| 
|$where    |Nie| 

W kwerendach $regex wyrażenia zakotwiczone w lewo umożliwiają wyszukiwanie indeksu. Jednak użycie modyfikatora „i” (wielkość liter nie ma znaczenia) oraz modyfikatora „m” (wiele wierszy) powoduje skanowanie kolekcji we wszystkich wyrażeniach.

Jeśli istnieje potrzeba dołączenia „$” lub „|”, najlepiej utworzyć dwa lub więcej zapytań regex. Na przykład, biorąc pod ```find({x:{$regex: /^abc$/})```uwagę następujące oryginalne zapytanie: , musi być modyfikowany w następujący sposób:

```find({x:{$regex: /^abc/, x:{$regex:/^abc$/}})```.

Pierwsza część użyje indeksu, aby ograniczyć wyszukiwanie do dokumentów, które rozpoczynają się od ^abc, a druga część będzie dokładnie dopasowywana do wpisów. Operator kreski „|” działa jako funkcja „or” — zapytanie ```find({x:{$regex: /^abc|^def/})``` pasuje do dokumentów, w których pole „x” ma wartości zaczynające się od „abc” lub „def”. Aby korzystać z indeksu, zaleca się podzielenie zapytania na dwa różne zapytania połączone operatorem $or: ```find( {$or : [{x: $regex: /^abc/}, {$regex: /^def/}] })```.

### <a name="array-operators"></a>Operatory macierzy

|Polecenie  |Obsługiwane | 
|---------|---------|
| $all | Tak| 
| $elemMatch | Tak| 
| $size | Tak | 

### <a name="comment-operator"></a>Operator komentarza

|Polecenie  |Obsługiwane | 
|---------|---------|
$comment |Tak| 

### <a name="projection-operators"></a>Operatory projekcyjne

|Polecenie  |Obsługiwane |
|---------|---------|
|$elemMatch    |Tak|
|$meta|    Nie|
|$slice    | Tak|

### <a name="update-operators"></a>Operatory aktualizacji

#### <a name="field-update-operators"></a>Operatory do aktualizacji pól

|Polecenie  |Obsługiwane |
|---------|---------|
|$inc    |    Tak|
|$mul    |    Tak|
|$rename    |    Tak|
|$setOnInsert|    Tak|
|$set    |Tak|
|$unset| Tak|
|$min    |Tak|
|$max    |Tak|
|$currentDate    | Tak|

#### <a name="array-update-operators"></a>Operatory do aktualizacji tablic

|Polecenie  |Obsługiwane |
|---------|---------|
|$    |Tak|
|$[]|    Tak|
|$[<identifier>]|    Tak|
|$addToSet    |Tak|
|$pop    |Tak|
|$pullAll|    Tak|
|$pull    |Tak|
|$push    |Tak|
|$pushAll| Tak|


#### <a name="update-modifiers"></a>Modyfikatory aktualizacji

|Polecenie  |Obsługiwane |
|---------|---------|
|$each    |    Tak|
|$slice    |Tak|
|$sort    |Tak|
|$position    |Tak|

#### <a name="bitwise-update-operator"></a>Operator do aktualizacji Bitwise

|Polecenie  |Obsługiwane |
|---------|---------|
| $bit    |    Tak|    
|$bitsAllSet    |    Nie|
|$bitsAnySet    |    Nie|
|$bitsAllClear    |Nie|
|$bitsAnyClear    |Nie|

### <a name="geospatial-operators"></a>Operatory danych geoprzestrzennych

Operator | Obsługiwane| 
--- | --- |
$geoWithin | Tak |
$geoIntersects | Tak | 
$near |  Tak |
$nearSphere |  Tak |
$geometry |  Tak |
$minDistance | Tak |
$maxDistance | Tak |
$center | Tak |
$centerSphere | Tak |
$box | Tak |
$polygon |  Tak |

## <a name="cursor-methods"></a>Metody kursora

|Polecenie  |Obsługiwane |
|---------|---------|
|cursor.batchSize()    |    Tak|
|kursor.close()    |Tak|
|kursor.isClosed()|        Tak|
|kursor.sortation()|    Nie|
|kursor.comment()    |Tak|
|kursor.count()    |Tak|
|kursor.explain()|    Nie|
|kursor.forEach()    |Tak|
|kursor.hasNastępny()    |Tak|
|kursor.hint()    |Tak|
|kursor.isWyczesane()|    Tak|
|kursor.itcount()    |Tak|
|kursor.limit()    |Tak|
|kursor.map()    |Tak|
|cursor.maxScan()    |Tak|
|cursor.maxTimeMS()|    Tak|
|kursor.max()    |Tak|
|kursor.min()    |Tak|
|kursor.next()|    Tak|
|plik cursor.noCursorTimeout()    |Nie|
|kursor.objsLeftInBatch()    |Tak|
|kursor.pretty()|    Tak|
|kursor.readConcern()|    Tak|
|plik cursor.readPref()        |Tak|
|plik cursor.returnKey()    |Nie|
|kursor.showRecordId()|    Nie|
|kursor.size()    |Tak|
|kursor.skip()    |Tak|
|cursor.sort()    |    Tak|
|kursor.tailable()|    Nie|
|kurs.toArray()    |Tak|

## <a name="sort-operations"></a>Operacje sortowania

W przypadku używania operacji `findOneAndUpdate` obsługiwane są operacje sortowania względem pojedynczego pola, ale nie względem wielu pól.

## <a name="unique-indexes"></a>Indeksy unikatowe

Unikatowe indeksy zapewniają, że określone pole nie ma zduplikowanych wartości we wszystkich dokumentach w kolekcji, podobnie jak unikatowość jest zachowywana na domyślnym kluczu "_id". Indeksy niestandardowe można tworzyć w usłudze Cosmos DB za pomocą polecenia createIndex, w tym ograniczenia "unikatowy".

## <a name="time-to-live-ttl"></a>Czas wygaśnięcia (TTL)

Usługa Cosmos DB obsługuje czas wygaśnięcia (TTL) na podstawie znacznika czasu dokumentu. Czas wygaśnięcia można włączyć dla kolekcji, przechodząc do witryny [Azure Portal](https://portal.azure.com).

## <a name="user-and-role-management"></a>Zarządzanie użytkownikami i rolami

Usługa Cosmos DB nie obsługuje jeszcze użytkowników i ról. Jednak usługa Cosmos DB obsługuje sterowanie dostępem oparte na rolach (RBAC) oraz hasła/klucze tylko do odczytu i do odczytu, które można uzyskać za pośrednictwem [witryny Azure portal](https://portal.azure.com) (strona Parametry połączenia).

## <a name="replication"></a>Replikacja

Usługa Cosmos DB obsługuje automatyczną, natywną replikację na najniższych warstwach. Ta logika została rozszerzona, aby osiągnąć małe opóźnienia oraz replikację globalną. Usługa Cosmos DB nie obsługuje poleceń dotyczących replikacji ręcznej.

## <a name="write-concern"></a>Ustawienie Write Concern

Niektóre aplikacje polegają na ustawieniu [Write Concern](https://docs.mongodb.com/manual/reference/write-concern/), które określa liczbę odpowiedzi wymaganych podczas operacji zapisu. Ze względu na sposób obsługi replikacji w tle w usłudze Cosmos DB wszystkie operacje zapisu mają domyślnie automatycznie ustawioną opcję Quorum (Kworum). Ustawienia Write Concern określone w kodzie klienta są ignorowane. Aby dowiedzieć się więcej, zobacz [Maksymalizowanie dostępności i wydajności za pomocą poziomów spójności](consistency-levels.md).

## <a name="sharding"></a>Dzielenie na fragmenty

Usługa Azure Cosmos DB obsługuje automatyczne dzielenie na fragmenty po stronie serwera. Automatycznie zarządza tworzeniem, umieszczaniem i równoważeniem fragmentów. Usługa Azure Cosmos DB nie obsługuje ręcznych poleceń dzielenia na fragmenty, co oznacza, że nie trzeba wywoływać poleceń, takich jak addShard, balancerStart, moveChunk itp. Wystarczy określić klucz niezależnego fragmentu podczas tworzenia kontenerów lub wykonywania zapytań o dane.

## <a name="sessions"></a>Sesje

Usługa Azure Cosmos DB nie obsługuje jeszcze poleceń sesji po stronie serwera.

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać więcej informacji, sprawdź [funkcje wersji Mongo 3.6](https://devblogs.microsoft.com/cosmosdb/azure-cosmos-dbs-api-for-mongodb-now-supports-server-version-3-6/)
- Dowiedz się, jak [korzystać z programu Studio 3T](mongodb-mongochef.md) za pomocą interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB.
- Dowiedz się, jak [korzystać z programu Robo 3T](mongodb-robomongo.md) za pomocą interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB.
- Eksploruj [przykłady](mongodb-samples.md) bazy danych MongoDB za pomocą interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB.

<sup>Uwaga: W tym artykule opisano funkcję usługi Azure Cosmos DB, która zapewnia zgodność protokołu przewodowego z bazami danych MongoDB. Firma Microsoft nie uruchamia baz danych MongoDB w celu świadczenia tej usługi. Usługa Azure Cosmos DB nie jest powiązana z mongodb, inc.</sup>
