---
title: Zmienianie strumieni w interfejsie API usługi Azure Cosmos DB dla usługi MongoDB
description: Dowiedz się, jak korzystać ze strumieni zmian w interfejsie API usługi Azure Cosmos DB dla usługi MongoDB, aby uzyskać zmiany wprowadzone w danych.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: tisande
ms.openlocfilehash: ecfa98241f74aac43a827b645a6ed877624d643d
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437803"
---
# <a name="change-streams-in-azure-cosmos-dbs-api-for-mongodb"></a>Zmienianie strumieni w interfejsie API usługi Azure Cosmos DB dla usługi MongoDB

[Obsługa kanału informacyjnego zmiany](change-feed.md) w interfejsie API usługi Azure Cosmos DB dla usługi MongoDB jest dostępna przy użyciu interfejsu API strumieni zmian. Za pomocą interfejsu API strumieni zmian, aplikacje można uzyskać zmiany wprowadzone do kolekcji lub elementów w jednym niezależnego fragmentu. Później można podjąć dalsze działania na podstawie wyników. Zmiany elementów w kolekcji są przechwytywane w kolejności ich czasu modyfikacji i kolejność sortowania jest gwarantowana dla klucza niezależnego fragmentu.

> [!NOTE]
> Aby użyć strumieni zmian, utwórz konto w wersji 3.6 interfejsu API usługi Azure Cosmos DB dla usługi MongoDB lub nowszej wersji. Po uruchomieniu przykłady strumienia zmian względem wcześniejszej `Unrecognized pipeline stage name: $changeStream` wersji, może zostać wyświetlony błąd.

## <a name="current-limitations"></a>Bieżące ograniczenia

Następujące ograniczenia mają zastosowanie podczas korzystania ze strumieni zmian:

* Właściwości `operationType` `updateDescription` i właściwości nie są jeszcze obsługiwane w dokumencie wyjściowym.
* `insert`Typy `update`operacji `replace` i operacje są obecnie obsługiwane. Operacja usuwania lub inne zdarzenia nie są jeszcze obsługiwane.

Ze względu na te ograniczenia wymagane są opcje $match, $project i fullDocument, jak pokazano w poprzednich przykładach.

W przeciwieństwie do źródła danych w interfejsie API SQL usługi Azure Cosmos DB nie istnieje oddzielna [biblioteka procesora kanału informacyjnego zmian,](change-feed-processor.md) która umożliwia korzystanie ze strumieni zmian lub potrzebę kontenera dzierżawy. Obecnie nie ma obsługi [wyzwalaczy usługi Azure Functions](change-feed-functions.md) do przetwarzania strumieni zmian.

## <a name="error-handling"></a>Obsługa błędów

Następujące kody błędów i komunikaty są obsługiwane podczas korzystania ze strumieni zmian:

* **Kod błędu HTTP 16500** — gdy strumień zmian jest ograniczany, zwraca pustą stronę.

* **NamespaceNotFound (OperationType Invalidate)** — po uruchomieniu strumienia zmian w kolekcji, która `NamespaceNotFound` nie istnieje lub jeśli kolekcja zostanie porzucona, zwracany jest błąd. Ponieważ `operationType` nie można zwrócić właściwości w dokumencie wyjściowym, `operationType Invalidate` zamiast `NamespaceNotFound` błędu zwracany jest błąd.

## <a name="examples"></a>Przykłady

W poniższym przykładzie pokazano, jak uzyskać strumienie zmian na wszystkie elementy w kolekcji. W tym przykładzie tworzy kursor do oglądania elementów, gdy są one wstawiane, aktualizowane lub zastępowane. Etap, `$match` `$project` etap i `fullDocument` opcja są wymagane, aby uzyskać strumienie zmian. Obserwowanie operacji usuwania przy użyciu strumieni zmian nie jest obecnie obsługiwane. Aby obejść ten problem, można dodać znacznik miękki do elementów, które są usuwane. Na przykład można dodać atrybut w elemencie o nazwie "usunięte". Jeśli chcesz usunąć element, możesz ustawić "usunięte" `true` i ustawić czas wygaśnięcia elementu. Ponieważ aktualizacja "usunięte" `true` do jest aktualizacja, ta zmiana będzie widoczna w strumieniu zmian.

### <a name="javascript"></a>Javascript:

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

### <a name="c"></a>C#:

```csharp
var pipeline = new EmptyPipelineDefinition<ChangeStreamDocument<BsonDocument>>()
    .Match(change => change.OperationType == ChangeStreamOperationType.Insert || change.OperationType == ChangeStreamOperationType.Update || change.OperationType == ChangeStreamOperationType.Replace)
    .AppendStage<ChangeStreamDocument<BsonDocument>, ChangeStreamDocument<BsonDocument>, BsonDocument>(
    "{ $project: { '_id': 1, 'fullDocument': 1, 'ns': 1, 'documentKey': 1 }}");

var options = new ChangeStreamOptions{
        FullDocument = ChangeStreamFullDocumentOption.UpdateLookup
    };

var enumerator = coll.Watch(pipeline, options).ToEnumerable().GetEnumerator();

while (enumerator.MoveNext()){
        Console.WriteLine(enumerator.Current);
    }

enumerator.Dispose();
```

## <a name="changes-within-a-single-shard"></a>Zmiany w jednym niezależnuchy

W poniższym przykładzie pokazano, jak uzyskać zmiany elementów w ramach jednego fragmentu. W tym przykładzie pobiera zmiany elementów, które mają klucz niezależnego fragmentu równa "a" i wartość klucza niezależnego fragmentu równa "1". Istnieje możliwość, aby różne klienci odczytywania zmian z różnych fragmentów równolegle.

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

## <a name="next-steps"></a>Następne kroki

* [Automatyczne wykorzystywanie czasu do automatycznego wygaśnięcia danych w interfejsie API usługi Azure Cosmos DB dla usługi MongoDB](mongodb-time-to-live.md)
* [Indeksowanie w interfejsie API usługi Azure Cosmos DB dla usługi MongoDB](mongodb-indexing.md)
