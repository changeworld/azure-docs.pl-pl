---
title: Indeksowanie w interfejsie API usługi Azure Cosmos DB dla bazy danych MongoDB
description: Zawiera omówienie możliwości indeksowania za pomocą interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: conceptual
origin.date: 12/26/2018
ms.date: 01/21/2019
author: rockboyfor
ms.author: v-yeche
ms.openlocfilehash: de037316efa50dd25ea04c370fa0e5878fb52ba1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60448507"
---
# <a name="indexing-using-azure-cosmos-dbs-api-for-mongodb"></a>Indeksowanie przy użyciu interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB

Interfejs API danych usługi Azure Cosmos DB, bazy danych mongodb wykorzystuje możliwości automatycznego zarządzania indeksem usługi Cosmos DB. W rezultacie użytkownicy mają dostęp do domyślnych zasad usługi Cosmos DB indeksowania. Tak Jeśli żadne indeksy zostały zdefiniowane przez użytkownika lub żadne indeksy nie zostały usunięte, następnie wszystkie pola będą się domyślnie automatycznie indeksowane po wstawieniu do kolekcji. W większości przypadków zaleca się użycie domyślnych zasad indeksowania ustawionych dla konta.

## <a name="dropping-the-default-indexes"></a>Usuwanie indeksów domyślnych

Następujące polecenie umożliwia usunięcie indeksów domyślnych dla kolekcji ```coll```:

```JavaScript
> db.coll.dropIndexes()
{ "_t" : "DropIndexesResponse", "ok" : 1, "nIndexesWas" : 3 }
```

## <a name="creating-compound-indexes"></a>Tworzenie indeksów złożonych

Indeksy złożone przechowują odwołania do wielu pól dokumentu. Logicznie są ekwiwalentem tworzenia wielu pojedynczych indeksów dla poszczególnych pól. Aby skorzystać z optymalizacji zapewnianych przez techniki indeksowania usługi Cosmos DB, zalecamy utworzenie wielu pojedynczych indeksów zamiast jednego indeksu złożonego (nieunikatowego).

## <a name="creating-unique-indexes"></a>Tworzenie indeksów unikatowych

[Indeksy unikatowe](unique-keys.md) są przydatne w przypadkach, w których należy wymusić, aby dwa dokumenty (lub więcej) nie zawierały tej samej wartości dla pola indeksowanego. 
>[!important] 
> Obecnie indeksy unikatowe można tworzyć tylko wtedy, gdy kolekcja jest pusta (nie zawiera dokumentów). 

Następujące polecenie tworzy indeks unikatowy dla pola "student_id":

```JavaScript
globaldb:PRIMARY> db.coll.createIndex( { "student_id" : 1 }, {unique:true} ) 
{
        "_t" : "CreateIndexesResponse",
        "ok" : 1,
        "createdCollectionAutomatically" : false,
        "numIndexesBefore" : 1,
        "numIndexesAfter" : 4
}
```

W przypadku kolekcji udostępnionych, zgodnie z zachowaniem bazy danych MongoDB, tworzenie indeksów unikatowych wymaga podania klucza fragmentu (partycji). Ujmując to innymi słowami, wszystkie indeksy unikatowe w kolekcji udostępnionej są indeksami złożonymi, w których jedno z pól jest kluczem partycji.

Następujące polecenie spowoduje utworzenie kolekcji udostępnionej ```coll``` (klucz fragmentu to ```university```) z indeksem unikatowym dla pól student_id i university:

```JavaScript
globaldb:PRIMARY> db.runCommand({shardCollection: db.coll._fullName, key: { university: "hashed"}});
{
        "_t" : "ShardCollectionResponse",
        "ok" : 1,
        "collectionsharded" : "test.coll"
}
globaldb:PRIMARY> db.coll.createIndex( { "student_id" : 1, "university" : 1 }, {unique:true})
{
        "_t" : "CreateIndexesResponse",
        "ok" : 1,
        "createdCollectionAutomatically" : false,
        "numIndexesBefore" : 3,
        "numIndexesAfter" : 4
}
```

W powyższym przykładzie w przypadku pominięcia klauzuli ```"university":1``` dojdzie do błędu i zwrócenia następującego komunikatu:

```"cannot create unique index over {student_id : 1.0} with shard key pattern { university : 1.0 }"```

## <a name="ttl-indexes"></a>Indeksy czasu wygaśnięcia

Aby włączyć wygasanie dokumentu w określonej kolekcji, należy utworzyć [„Indeks czasu wygaśnięcia”](../cosmos-db/time-to-live.md). Indeks czasu wygaśnięcia jest indeksem dla pola _ts z wartością „expireAfterSeconds”.

Przykład:
```JavaScript
globaldb:PRIMARY> db.coll.createIndex({"_ts":1}, {expireAfterSeconds: 10})
```

Poprzednie polecenie spowoduje usunięcie dowolnych dokumentów w kolekcji ```db.coll```, które nie zostały zmodyfikowane w ciągu ostatnich 10 sekund. 

> [!NOTE]
> **_ts** jest polem właściwym dla usługi Cosmos DB i nie można do niego uzyskać dostępu z klientów bazy danych MongoDB. Jest zastrzeżoną właściwością (systemową), która zawiera sygnaturę czasową ostatniej modyfikacji dokumentu.
>

## <a name="migrating-collections-with-indexes"></a>Migrowanie kolekcji z indeksami

Obecnie tworzenie indeksów unikatowych jest możliwe tylko wtedy, gdy kolekcja nie zawiera dokumentów. Popularne narzędzia migracji bazy danych MongoDB podejmują próby tworzenia indeksów unikatowych po zaimportowaniu danych. Aby obejść ten problem, zaleca się, aby użytkownicy ręcznie tworzyli odpowiednie kolekcje i indeksy unikatowe, zamiast pozwalać na to narzędziom do migracji (w przypadku narzędzia ```mongorestore``` to zachowanie jest osiągane poprzez użycie flagi --noIndexRestore w wierszu polecenia).

## <a name="next-steps"></a>Kolejne kroki
* [Indeksowanie w usłudze Azure Cosmos DB](../cosmos-db/index-policy.md)
* [Expire data in Azure Cosmos DB automatically with time to live](../cosmos-db/time-to-live.md) (Automatyczne wygasanie danych w usłudze Azure Cosmos DB przy użyciu czasu wygaśnięcia)

<!-- Update_Description: update meta properties  -->