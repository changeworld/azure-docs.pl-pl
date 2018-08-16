---
title: Dowiedz się, jak ustawić wartość czasu wygaśnięcia dla dokumentów bazy danych usługi Cosmos Azure utworzonych za pośrednictwem interfejsu API bazy danych MongoDB w celu automatycznego przeczyszczania ich z systemu po upływie określonego czasu.
description: Dokumentacja funkcji czasu wygaśnięcia (TTL) poszczególnych dokumentów w bazie danych MongoDB.
services: cosmos-db
author: orestis-ms
manager: kfile
editor: ''
ms.service: cosmos-db
ms.devlang: javascript
ms.topic: quickstart
ms.date: 08/10/2018
ms.author: orkostak
ms.openlocfilehash: 0bf8891e28897321dbfbad4486dd11c0b5ee4264
ms.sourcegitcommit: 17fe5fe119bdd82e011f8235283e599931fa671a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/11/2018
ms.locfileid: "40043159"
---
# <a name="expire-data-in-azure-cosmos-db-mongodb-api"></a>Wygasanie danych w interfejsie API bazy danych MongoDB w usłudze Azure Cosmos DB

Funkcjonalność czasu wygaśnięcia (TTL, time-to-live) umożliwia automatyczne wygasanie danych bazy danych. Interfejs API bazy danych MongoDB korzysta z funkcji czasu wygaśnięcia usługi Azure Cosmos DB. Obsługiwane są dwa tryby: ustawianie domyślnej wartości czasu wygaśnięcia dla całej kolekcji oraz ustawianie wartości czasu wygaśnięcia dla poszczególnych dokumentów. Logika zarządzająca indeksami czasu wygaśnięcia i wartościami czasu wygaśnięcia dla poszczególnych dokumentów w interfejsie API bazy danych MongoDB jest [taka sama jak w usłudze Azure Cosmos DB](../cosmos-db/mongodb-indexing.md).

## <a name="ttl-indexes"></a>Indeksy czasu wygaśnięcia
Aby włączyć czas wygaśnięcia dla całej kolekcji, należy utworzyć [„Indeks czasu wygaśnięcia”](../cosmos-db/mongodb-indexing.md). Indeks czasu wygaśnięcia jest indeksem dla pola _ts z wartością „expireAfterSeconds”.

Przykład:
```JavaScript
globaldb:PRIMARY> db.coll.createIndex({"_ts":1}, {expireAfterSeconds: 10})
{
        "_t" : "CreateIndexesResponse",
        "ok" : 1,
        "createdCollectionAutomatically" : true,
        "numIndexesBefore" : 1,
        "numIndexesAfter" : 4
}
```

Polecenie w powyższym przykładzie powoduje utworzenie indeksu z funkcjonalnością czasu wygaśnięcia. Po utworzeniu indeksu z bazy danych będą automatycznie usuwane wszystkie dokumenty w kolekcji, które nie zostały zmodyfikowane w ciągu ostatnich 10 sekund. 

> [!NOTE]
> **_ts** jest polem właściwym dla usługi Cosmos DB i nie można do niego uzyskać dostępu z klientów bazy danych MongoDB. Jest zastrzeżoną właściwością (systemową), która zawiera sygnaturę czasową ostatniej modyfikacji dokumentu.
>
    
Dodatkowo — przykład w języku C#: 
```C# 
var options = new CreateIndexOptions {ExpireAfter = TimeSpan.FromSeconds(10)}; 
var field = new StringFieldDefinition<BsonDocument>("_ts"); 
var indexDefinition = new IndexKeysDefinitionBuilder<BsonDocument>().Ascending(field); 
await collection.Indexes.CreateOneAsync(indexDefinition, options); 
``` 

## <a name="set-time-to-live-value-for-a-document"></a>Ustawianie czasu wygaśnięcia dla dokumentu 
Obsługiwane są również wartości czasu wygaśnięcia dla poszczególnych dokumentów. Dokumenty muszą zawierać na poziomie głównym właściwość „ttl” (małe litery), a dla danej kolekcji musi być utworzony indeks TTL, zgodnie z opisem powyżej. Wartości czasu wygaśnięcia ustawione dla poszczególnych dokumentów zastępują wartość czasu wygaśnięcia dla kolekcji.

Wartość czasu wygaśnięcia musi być typu int32. Ewentualnie może być wartością typu int64 mieszczącą się w granicach typu int32 lub typu double bez części dziesiętnej mieszczącą się w granicach typu int32. Wartości właściwości czasu wygaśnięcia, które nie są zgodne z tymi specyfikacjami, są dozwolone, ale nie są traktowane jako znaczące wartości czasu wygaśnięcia dokumentów.

Wartość czasu wygaśnięcia dokumentu jest opcjonalna. Do kolekcji można wstawiać dokumenty bez wartości czasu wygaśnięcia.  W takim przypadku stosowana jest wartość czasu wygaśnięcia kolekcji. 

Poniższe dokumenty mają prawidłowe wartości czasu wygaśnięcia. Po wstawieniu dokumentów ich wartości czasu wygaśnięcia zastępują wartości czasu wygaśnięcia kolekcji. Dokumenty zostaną więc usunięte po 20 sekundach.  

```JavaScript 
globaldb:PRIMARY> db.coll.insert({id:1, location: "Paris", ttl: 20.0}) 
globaldb:PRIMARY> db.coll.insert({id:1, location: "Paris", ttl: NumberInt(20)}) 
globaldb:PRIMARY> db.coll.insert({id:1, location: "Paris", ttl: NumberLong(20)}) 
```

Poniższe dokumenty mają nieprawidłowe wartości czasu wygaśnięcia. Dokumenty zostaną wstawione, ale ich wartości czasu wygaśnięcia nie będą stosowane. Dokumenty zostaną więc usunięte po 10 sekundach z powodu wartości czasu wygaśnięcia kolekcji. 

```JavaScript 
globaldb:PRIMARY> db.coll.insert({id:1, location: "Paris", ttl: 20.5}) //TTL value contains non-zero decimal part. 
globaldb:PRIMARY> db.coll.insert({id:1, location: "Paris", ttl: NumberLong(2147483649)}) //TTL value is greater than Int32.MaxValue (2,147,483,648). 
``` 

## <a name="how-to-activate-the-per-document-ttl-feature"></a>Jak aktywować funkcję czasu wygaśnięcia poszczególnych dokumentów
Funkcję czasu wygaśnięcia poszczególnych dokumentów można aktywować za pomocą karty funkcji w wersji zapoznawczej konta interfejsu API bazy danych MongoDB w witrynie Azure Portal.

![Zrzut ekranu przedstawiający aktywację funkcji czasu wygaśnięcia poszczególnych dokumentów w portalu](./media/mongodb-ttl/mongodb_portal_ttl.png) 

## <a name="next-steps"></a>Następne kroki
* [Automatyczne wygasanie danych w kolekcjach usługi Azure Cosmos DB przy użyciu czasu wygaśnięcia](../cosmos-db/time-to-live.md)
* [Indeksowanie w interfejsie API bazy danych MongoDB w usłudze Azure Cosmos DB](../cosmos-db/mongodb-indexing.md)
