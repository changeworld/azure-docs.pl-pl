---
title: Indeksowanie w interfejsie API Azure Cosmos DB dla MongoDB
description: Przedstawia omówienie możliwości indeksowania przy użyciu interfejsu API Azure Cosmos DB MongoDB.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 12/26/2018
author: sivethe
ms.author: sivethe
ms.openlocfilehash: e51e96c0c553bcf37284878cab11f3ec592ddd05
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/22/2019
ms.locfileid: "72753391"
---
# <a name="indexing-using-azure-cosmos-dbs-api-for-mongodb"></a>Indeksowanie przy użyciu interfejsu API Azure Cosmos DB dla MongoDB

Interfejs API Azure Cosmos DB dla MongoDB korzysta z funkcji automatycznego zarządzania indeksami Cosmos DB. W związku z tym użytkownicy mają dostęp do domyślnych zasad indeksowania Cosmos DB. Tak więc, jeśli nie zdefiniowano żadnych indeksów przez użytkownika lub żadne indeksy nie zostały porzucone, wszystkie pola będą automatycznie indeksowane domyślnie po wstawieniu do kolekcji. W większości przypadków zaleca się użycie domyślnych zasad indeksowania ustawionych dla konta.

## <a name="indexing-for-version-36"></a>Indeksowanie dla wersji 3,6

Konta obsługujące protokół komunikacyjny w wersji 3,6 zapewniają inne domyślne zasady indeksowania niż zasady udostępniane przez wcześniejsze wersje. Domyślnie tylko pole _id jest indeksowane. Aby zindeksować dodatkowe pola, użytkownik musi zastosować polecenia MongoDB index Management. Aby zastosować sortowanie do zapytania, obecnie należy utworzyć indeks dla pól używanych w operacji sortowania.

### <a name="dropping-the-default-indexes-36"></a>Porzucanie indeksów domyślnych (3,6)

W przypadku kont obsługujących protokół komunikacyjny w wersji 3,6 jedynym domyślnym indeksem jest _id, którego nie można porzucić.

### <a name="creating-a-compound-index-36"></a>Tworzenie indeksu złożonego (3,6)

Prawdziwe indeksy złożone są obsługiwane dla kont korzystających z protokołu sieci 3,6. Następujące polecenie spowoduje utworzenie indeksu złożonego dla pól "a" i "b": `db.coll.createIndex({a:1,b:1})`

Indeksy złożone mogą służyć do wydajnego sortowania wielu pól jednocześnie, takich jak: `db.coll.find().sort({a:1,b:1})`

## <a name="indexing-for-version-32"></a>Indeksowanie dla wersji 3,2

### <a name="dropping-the-default-indexes-32"></a>Porzucanie indeksów domyślnych (3,2)

Następujące polecenie umożliwia usunięcie indeksów domyślnych dla kolekcji ```coll```:

```JavaScript
> db.coll.dropIndexes()
{ "_t" : "DropIndexesResponse", "ok" : 1, "nIndexesWas" : 3 }
```

### <a name="creating-a-compound-index-32"></a>Tworzenie indeksu złożonego (3,2)

Indeksy złożone przechowują odwołania do wielu pól dokumentu. Logicznie są ekwiwalentem tworzenia wielu pojedynczych indeksów dla poszczególnych pól. Aby skorzystać z optymalizacji zapewnianych przez techniki indeksowania usługi Cosmos DB, zalecamy utworzenie wielu pojedynczych indeksów zamiast jednego indeksu złożonego (nieunikatowego).

## <a name="common-indexing-operations"></a>Typowe operacje indeksowania

Następujące operacje są wspólne dla obu kont obsługujących protokół komunikacyjny w wersji 3,6 i konta obsługujące starsze wersje protokołu transportowego. 

## <a name="creating-unique-indexes"></a>Tworzenie indeksów unikatowych

[Indeksy unikatowe](unique-keys.md) są przydatne w przypadkach, w których należy wymusić, aby dwa dokumenty (lub więcej) nie zawierały tej samej wartości dla pola indeksowanego.

>[!Important]
> Obecnie indeksy unikatowe można tworzyć tylko wtedy, gdy kolekcja jest pusta (nie zawiera dokumentów).

Następujące polecenie tworzy unikatowy indeks w polu "student_id":

```shell
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

```shell
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

## <a name="migrating-collections-with-indexes"></a>Migrowanie kolekcji z indeksami

Obecnie tworzenie indeksów unikatowych jest możliwe tylko wtedy, gdy kolekcja nie zawiera dokumentów. Popularne narzędzia migracji bazy danych MongoDB podejmują próby tworzenia indeksów unikatowych po zaimportowaniu danych. Aby obejść ten problem, zaleca się, aby użytkownicy ręcznie utworzyli odpowiednie kolekcje i indeksy unikatowe, zamiast zezwalać na działanie narzędzia migracji (aby ```mongorestore``` to zachowanie zostanie osiągnięte przy użyciu flagi `--noIndexRestore` w wierszu polecenia).

## <a name="next-steps"></a>Następne kroki

* [Indeksowanie w usłudze Azure Cosmos DB](../cosmos-db/index-policy.md)
* [Expire data in Azure Cosmos DB automatically with time to live](../cosmos-db/time-to-live.md) (Automatyczne wygasanie danych w usłudze Azure Cosmos DB przy użyciu czasu wygaśnięcia)
