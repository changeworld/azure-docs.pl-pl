---
title: Zmień strumienie w interfejsie API Azure Cosmos DB dla MongoDB
description: Dowiedz się, jak używać usługi Change Streams n Azure Cosmos DB API MongoDB, aby uzyskać zmiany wprowadzone do danych.
author: srchi
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 11/16/2019
ms.author: srchi
ms.openlocfilehash: ec1ec1a8a80953f8988355341ee7128bd29b982d
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/19/2020
ms.locfileid: "77467781"
---
# <a name="change-streams-in-azure-cosmos-dbs-api-for-mongodb"></a>Zmień strumienie w interfejsie API Azure Cosmos DB dla MongoDB

Obsługa [kanałów zmian](change-feed.md) w interfejsie API Azure Cosmos DB dla MongoDB jest dostępna za pomocą interfejsu API zmiany strumieni. Za pomocą interfejsu API Zmień strumienie aplikacje mogą pobrać zmiany wprowadzone do kolekcji lub do elementów w jednym fragmentu. Później można wykonać dalsze czynności na podstawie wyników. Zmiany elementów w kolekcji są przechwytywane w kolejności ich modyfikacji, a porządek sortowania jest gwarantowany na klucz fragmentu.

> [!NOTE]
> Aby można było używać strumieni zmian, należy utworzyć konto z wersją 3,6 interfejsu API Azure Cosmos DB dla MongoDB lub nowszej wersji. W przypadku uruchomienia przykładów zmiany strumienia dla starszej wersji może zostać wyświetlony komunikat o błędzie `Unrecognized pipeline stage name: $changeStream`. 

Poniższy przykład pokazuje, jak pobrać strumienie zmian dla wszystkich elementów w kolekcji. Ten przykład umożliwia utworzenie kursora do oglądania elementów, gdy są one wstawiane, aktualizowane lub zastępowane. Aby uzyskać strumienie zmian, wymagane są $match etap, etap $project i opcja fullDocument. Obserwowanie operacji usuwania przy użyciu strumieni zmian nie jest obecnie obsługiwane. Obejście tego problemu pozwala na dodanie znacznika miękkiego do elementów, które są usuwane. Na przykład, można dodać atrybut w elemencie o nazwie "usunięty" i ustawić go na "true" i ustawić wartość TTL dla elementu, aby można go było automatycznie usunąć, a także śledzić.

```javascript
var cursor = db.coll.watch(
    [
        { $match: { "operationType": { $in: ["insert", "update", "replace"] } } },
        { $project: { "_id": 1, "fullDocument": 1, "ns": 1, "documentKey": 1 } }
    ],
    { fullDocument: "updateLookup" });

while (!cursor.isExhausted()) {
    if (cursor.hasNext()) {
        printjson(cursor.next());
    }
}
```

Poniższy przykład pokazuje, jak uzyskać zmiany elementów w jednym fragmentu. Ten przykład pobiera zmiany elementów, które mają klucz fragmentu równy "a", a wartość klucza fragmentu równą "1".

```javascript
var cursor = db.coll.watch(
    [
        { 
            $match: { 
                $and: [
                    { "fullDocument.a": 1 }, 
                    { "operationType": { $in: ["insert", "update", "replace"] } }
                ]
            }
        },
        { $project: { "_id": 1, "fullDocument": 1, "ns": 1, "documentKey": 1} }
    ],
    { fullDocument: "updateLookup" });

```

## <a name="current-limitations"></a>Bieżące ograniczenia

W przypadku korzystania ze strumieni zmian stosowane są następujące ograniczenia:

* Właściwości `operationType` i `updateDescription` nie są jeszcze obsługiwane w dokumencie wyjściowym.
* Typy operacji `insert`, `update`i `replace` są obecnie obsługiwane. Operacja usuwania lub inne zdarzenia nie są jeszcze obsługiwane.

Ze względu na te ograniczenia wymagane są opcje etap $match, etap $project i fullDocument, jak pokazano w poprzednich przykładach.

## <a name="error-handling"></a>Obsługa błędów

Podczas używania strumieni zmian są obsługiwane następujące kody błędów i komunikaty:

* **Kod błędu HTTP 429** — gdy strumień zmiany jest ograniczany, zwraca pustą stronę.

* **NamespaceNotFound (OperationType unvalidate)** — w przypadku uruchomienia strumienia zmian w kolekcji, która nie istnieje lub jeśli kolekcja została porzucona, zwracany jest błąd `NamespaceNotFound`. Ponieważ właściwość `operationType` nie może zostać zwrócona w dokumencie wyjściowym, a nie `operationType Invalidate` błędu, zwracany jest błąd `NamespaceNotFound`.

## <a name="next-steps"></a>Następne kroki

* [Użyj czasu wygaśnięcia, aby automatycznie wygasać dane w interfejsie API Azure Cosmos DB MongoDB](mongodb-time-to-live.md)
* [Indeksowanie w interfejsie API Azure Cosmos DB dla MongoDB](mongodb-indexing.md)
