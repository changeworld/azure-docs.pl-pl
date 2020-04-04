---
title: Indeksowanie w interfejsie API usługi Azure Cosmos DB dla usługi MongoDB
description: Przedstawia omówienie możliwości indeksowania za pomocą interfejsu API usługi Azure Cosmos DB dla mongodb.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 04/03/2020
author: timsander1
ms.author: tisande
ms.openlocfilehash: f3f369928270c77557337bfdb1037cc5174c39f2
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80637958"
---
# <a name="indexing-using-azure-cosmos-dbs-api-for-mongodb"></a>Indeksowanie przy użyciu interfejsu API usługi Azure Cosmos DB dla usługi MongoDB

Interfejs API usługi Azure Cosmos DB dla mongodb wykorzystuje podstawowe możliwości zarządzania indeksem usługi Azure Cosmos DB. Ten dokument koncentruje się na jak dodać indeksy przy użyciu interfejsu API usługi Azure Cosmos DB dla mongodb. Można również przeczytać [omówienie indeksowania w usłudze Azure Cosmos DB,](index-overview.md) który jest istotny dla wszystkich interfejsów API.

## <a name="indexing-for-version-36"></a>Indeksowanie dla wersji 3.6

Pole `_id` jest zawsze automatycznie indeksowane i nie można go upuścić. Interfejs API usługi Azure Cosmos DB dla mongodb `_id` automatycznie wymusza unikatowość pola na klucz niezależnego fragmentu.

Aby zaindeksować dodatkowe pola, należy zastosować polecenia zarządzania indeksami mongodb. Podobnie jak w MongoDB, tylko `_id` pole jest indeksowane automatycznie. Ta domyślna zasada indeksowania różni się od interfejsu API SQL usługi Azure Cosmos DB, który domyślnie indeksuje wszystkie pola.

Aby zastosować sortowanie do kwerendy, należy utworzyć indeks na polach używanych w operacji sortowania.

## <a name="index-types"></a>Typy indeksów

### <a name="single-field"></a>Pojedyncze pole

Indeksy można tworzyć w dowolnym pojedynczym polu. Kolejność sortowania indeksu pojedynczego pola nie ma znaczenia. Następujące polecenie utworzy indeks w `name`polu:

`db.coll.createIndex({name:1})`

Jedno zapytanie będzie korzystać z wielu indeksów pojedynczego pola, jeśli są dostępne. Można utworzyć maksymalnie 500 pojedynczych indeksów pól na kontener.

### <a name="compound-indexes-36"></a>Indeksy złożone (3.6)

Indeksy złożone są obsługiwane dla kont korzystających z protokołu 3.6 wire. W indeksie złożonym można uwzględnić maksymalnie 8 pól. W przeciwieństwie do MongoDB, należy utworzyć indeks złożony tylko wtedy, gdy kwerenda musi skutecznie sortować na wielu polach jednocześnie. W przypadku kwerend z wieloma filtrami, które nie muszą sortować, należy utworzyć wiele indeksów pojedynczych pól zamiast jednego indeksu złożonego.

Następujące polecenie utworzy indeks złożony `name` na `age`polach i:

`db.coll.createIndex({name:1,age:1})`

Indeksy złożone mogą służyć do efektywnego sortowania w wielu polach jednocześnie, takich jak:

`db.coll.find().sort({name:1,age:1})`

Powyższy indeks złożony może być również używany do efektywnego sortowania kwerendy z odwrotną kolejnością sortowania we wszystkich polach. Oto przykład:

`db.coll.find().sort({name:-1,age:-1})`

Jednak sekwencja ścieżek w indeksie złożonym musi dokładnie odpowiadać kwerendzie. Oto przykład kwerendy, która wymagałaby dodatkowego indeksu złożonego:

`db.coll.find().sort({age:1,name:1})`

### <a name="multikey-indexes"></a>Indeksy multikey

Usługa Azure Cosmos DB tworzy indeksy multikey do indeksowania zawartości przechowywanej w tablicach. Jeśli indeksujesz pole z wartością tablicy, usługa Azure Cosmos DB automatycznie indeksuje każdy element w tablicy.

### <a name="geospatial-indexes"></a>Indeksy geoprzestrzenne

Wielu operatorów geoprzestrzennych skorzysta z indeksów geoprzestrzennych. Obecnie interfejs API usługi Azure Cosmos DB `2dsphere` dla mongodb obsługuje indeksy. `2d`indeksy nie są jeszcze obsługiwane.

Oto przykład tworzenia indeksu geoprzestrzennego w `location` polu:

`db.coll.createIndex({ location : "2dsphere" })`

### <a name="text-indexes"></a>Indeksy tekstowe

Indeksy tekstu nie są obecnie obsługiwane. W przypadku zapytań tekstowych dotyczących ciągów należy użyć integracji [usługi Azure Cognitive Search](https://docs.microsoft.com/azure/search/search-howto-index-cosmosdb) z usługą Azure Cosmos DB.

## <a name="index-properties"></a>Właściwości indeksu

Następujące operacje są typowe dla obu kont obsługujących protokół przewodowy w wersji 3.6 i kont obsługujących wcześniejsze wersje protokołu przewodowego. Możesz również dowiedzieć się więcej o [obsługiwanych indeksach i właściwościach indeksowanych](mongodb-feature-support-36.md#indexes-and-index-properties).

### <a name="unique-indexes"></a>Indeksy unikatowe

[Indeksy unikatowe](unique-keys.md) są przydatne w przypadkach, w których należy wymusić, aby dwa dokumenty (lub więcej) nie zawierały tej samej wartości dla pola indeksowanego.

>[!Important]
> Unikatowe indeksy można utworzyć tylko wtedy, gdy kolekcja jest pusta (nie zawiera żadnych dokumentów).

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

Dla kolekcji podzielonej na fragmenty tworzenie unikatowego indeksu wymaga zapewnienia klucza niezależnego fragmentu (partycji). Ujmując to innymi słowami, wszystkie indeksy unikatowe w kolekcji udostępnionej są indeksami złożonymi, w których jedno z pól jest kluczem partycji.

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

### <a name="ttl-indexes"></a>Indeksy czasu wygaśnięcia

Aby włączyć wygasanie dokumentu w określonej kolekcji, należy utworzyć [„Indeks czasu wygaśnięcia”](../cosmos-db/time-to-live.md). Indeks czasu wygaśnięcia jest indeksem dla pola _ts z wartością „expireAfterSeconds”.

Przykład:

```JavaScript
globaldb:PRIMARY> db.coll.createIndex({"_ts":1}, {expireAfterSeconds: 10})
```

Poprzednie polecenie spowoduje usunięcie dowolnych dokumentów w kolekcji ```db.coll```, które nie zostały zmodyfikowane w ciągu ostatnich 10 sekund.

> [!NOTE]
> **_ts** jest polem specyficznym dla usługi Azure Cosmos DB i nie jest dostępne dla klientów mongodb. Jest zastrzeżoną właściwością (systemową), która zawiera sygnaturę czasową ostatniej modyfikacji dokumentu.

## <a name="track-the-index-progress"></a>Śledzenie postępu indeksu

Wersja 3.6 interfejsu API usługi Azure Cosmos DB dla `currentOp()` kont MongoDB obsługuje polecenie śledzenia postępu indeksu w wystąpieniu bazy danych. To polecenie zwraca dokument zawierający informacje o operacjach w toku wystąpienia bazy danych. Polecenie `currentOp` służy do śledzenia wszystkich operacji w toku w natywnym MongoDB, podczas gdy w interfejsie API usługi Azure Cosmos DB dla mongodb, to polecenie obsługuje tylko śledzenie operacji indeksu.

Oto kilka przykładów, które pokazują, jak używać `currentOp` polecenia do śledzenia postępu indeksu:

* Pobierz postęp indeksu dla kolekcji:

   ```shell
   db.currentOp({"command.createIndexes": <collectionName>, "command.$db": <databaseName>})
   ```

* Pobierz postęp indeksu dla wszystkich kolekcji w bazie danych:

  ```shell
  db.currentOp({"command.$db": <databaseName>})
  ```

* Pobierz postęp indeksu dla wszystkich baz danych i kolekcji na koncie usługi Azure Cosmos:

  ```shell
  db.currentOp({"command.createIndexes": { $exists : true } })
  ```

Szczegóły postępu indeksu zawierają procent postępu dla bieżącej operacji indeksu. W poniższym przykładzie przedstawiono format dokumentu wyjściowego dla różnych etapów postępu indeksu:

1. Jeśli operacja indeksu na kolekcji "foo" i "bar" bazy danych, która ma 60 % indeksowania zakończone będzie miał następujący dokument wyjściowy. `Inprog[0].progress.total`pokazuje 100 jako cel ukończenia.

   ```json
   {
        "inprog" : [
        {
                ………………...
                "command" : {
                        "createIndexes" : foo
                        "indexes" :[ ],
                        "$db" : bar
                },
                "msg" : "Index Build (background) Index Build (background): 60 %",
                "progress" : {
                        "done" : 60,
                        "total" : 100
                },
                …………..…..
        }
        ],
        "ok" : 1
   }
   ```

2. Dla operacji indeksu, który właśnie rozpoczął się na "foo" kolekcji i "bar" bazy danych, dokument wyjściowy może pokazać 0% postępu, dopóki nie osiągnie mierzalny poziom.

   ```json
   {
        "inprog" : [
        {
                ………………...
                "command" : {
                        "createIndexes" : foo
                        "indexes" :[ ],
                        "$db" : bar
                },
                "msg" : "Index Build (background) Index Build (background): 0 %",
                "progress" : {
                        "done" : 0,
                        "total" : 100
                },
                …………..…..
        }
        ],
       "ok" : 1
   }
   ```

3. Po zakończeniu operacji indeksu w toku dokument wyjściowy pokazuje puste operacje inprog.

   ```json
   {
      "inprog" : [],
      "ok" : 1
   }
   ```

### <a name="background-index-updates"></a>Aktualizacje indeksu w tle

Niezależnie od wartości określonej dla **właściwości indeksu tła** aktualizacje indeksu są zawsze wykonywane w tle. Aktualizacje indeksu zużywają ru przy niższym priorytecie niż inne operacje bazy danych. W związku z tym zmiany indeksu nie spowoduje żadnych przestojów dla zapisów, aktualizacji lub usuwa.

Podczas dodawania nowego indeksu kwerendy natychmiast go wykorzystają. Oznacza to, że kwerendy mogą nie zwracać wszystkich pasujących wyników i będą to robić bez zwracania błędów. Po zakończeniu transformacji indeksu wyniki kwerendy będą spójne. Można [śledzić postęp indeksu](#track-the-index-progress).

## <a name="migrating-collections-with-indexes"></a>Migrowanie kolekcji z indeksami

Obecnie tworzenie indeksów unikatowych jest możliwe tylko wtedy, gdy kolekcja nie zawiera dokumentów. Popularne narzędzia migracji bazy danych MongoDB podejmują próby tworzenia indeksów unikatowych po zaimportowaniu danych. Aby obejść ten problem, zaleca się, aby użytkownicy ręcznie utworzyć odpowiednie kolekcje i unikatowe ```mongorestore``` indeksy, zamiast zezwalać `--noIndexRestore` na narzędzie migracji (dla tego zachowania jest osiągany przy użyciu flagi w wierszu polecenia).

## <a name="indexing-for-version-32"></a>Indeksowanie dla wersji 3.2

W przypadku kont usługi Azure Cosmos DB zgodnych z wersją protokołu przewodowego MongoDB 3.2 dostępne funkcje indeksowania i wartości domyślne są różne. Możesz [sprawdzić wersję swojego konta](mongodb-feature-support-36.md#protocol-support). Możesz uaktualnić do wersji 3.6, składając [wniosek o pomoc techniczną.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)

Jeśli używasz wersji 3.2, w tej sekcji przedstawiono kluczowe różnice w wersji 3.6.

### <a name="dropping-the-default-indexes-32"></a>Upuszczanie indeksów domyślnych (3.2)

W przeciwieństwie do wersji 3.6 interfejsu API usługi Azure Cosmos DB dla mongodb, wersja 3.2 domyślnie indeksuje każdą właściwość. Następujące polecenie może służyć do upuszczenia ```coll```tych domyślnych indeksów dla kolekcji:

```JavaScript
> db.coll.dropIndexes()
{ "_t" : "DropIndexesResponse", "ok" : 1, "nIndexesWas" : 3 }
```

Po upuszczeniu indeksów domyślnych można dodać dodatkowe indeksy, jak to miało miejsce w wersji 3.6.

### <a name="compound-indexes-32"></a>Indeksy złożone (3.2)

Indeksy złożone przechowują odwołania do wielu pól dokumentu. Jeśli chcesz utworzyć indeks złożony, uaktualnij do wersji 3.6, składając [wniosek o pomoc techniczną.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)

## <a name="next-steps"></a>Następne kroki

* [Indeksowanie w usłudze Azure Cosmos DB](../cosmos-db/index-policy.md)
* [Expire data in Azure Cosmos DB automatically with time to live](../cosmos-db/time-to-live.md) (Automatyczne wygasanie danych w usłudze Azure Cosmos DB przy użyciu czasu wygaśnięcia)
