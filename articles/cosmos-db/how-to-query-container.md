---
title: Wykonywanie zapytań dla kontenerów w usłudze Azure Cosmos DB
description: Dowiedz się, jak wykonywać zapytania dla kontenerów w usłudze Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: mjbrown
ms.openlocfilehash: 799fa43ad6ff12e5fa84326cbb41842e76daff12
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70092966"
---
# <a name="query-an-azure-cosmos-container"></a>Wykonywanie zapytania dotyczącego kontenera usługi Azure Cosmos

W tym artykule wyjaśniono, jak wykonywać zapytania dla kontenera (kolekcji, grafu lub tabeli) w usłudze Azure Cosmos DB.

## <a name="in-partition-query"></a>Zapytanie wewnątrz partycji

Gdy wykonujesz zapytanie o dane z kontenerów, jeśli zapytanie ma określony filtr klucza partycji, usługa Azure Cosmos DB obsługuje to zapytanie automatycznie. Kieruje ona zapytanie do partycji odpowiadających wartościom klucza partycji określonym w filtrze. Na przykład poniższe zapytanie jest kierowane do partycji `DeviceId`, która przechowuje wszystkie dokumenty odpowiadające wartości klucza partycji `XMS-0001`.

```csharp
// Query using partition key into a class called, DeviceReading
IQueryable<DeviceReading> query = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("myDatabaseName", "myCollectionName"))
    .Where(m => m.MetricType == "Temperature" && m.DeviceId == "XMS-0001");
```

## <a name="cross-partition-query"></a>Zapytanie obejmujące wiele partycji

Poniższe zapytanie nie ma filtra klucza partycji (`DeviceId`) i jest rozsyłane do wszystkich partycji, w których jest uruchamiane względem indeksu partycji. Aby uruchomić zapytanie w partycjach, ustaw `EnableCrossPartitionQuery` na wartość true (lub `x-ms-documentdb-query-enablecrosspartition` w interfejsie API REST).

Właściwość EnableCrossPartitionQuery przyjmuje wartość logiczną. Jeśli zapytanie nie ma klucza partycji, to — w przypadku ustawienia wartości true — usługa Azure Cosmos DB przekazuje zapytanie do partycji. Pojedyncze zapytania są wysyłane do wszystkich partycji. Aby odczytać wyniki zapytania, aplikacje klienckie powinny używać wyników z FeedResponse i sprawdzać właściwość ContinuationToken. Aby odczytać wszystkie wyniki, należy przeprowadzać iteracje na danych do momentu przybrania wartości null przez właściwość ContinuationToken. 

```csharp
// Query across partition keys into a class called, DeviceReading
IQueryable<DeviceReading> crossPartitionQuery = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("myDatabaseName", "myCollectionName"),
    new FeedOptions { EnableCrossPartitionQuery = true })
    .Where(m => m.MetricType == "Temperature" && m.MetricValue > 100);
```

Usługa Azure Cosmos DB obsługuje dla kontenerów funkcje agregujące COUNT, MIN, MAX oraz AVG przy użyciu języka SQL. Funkcje agregujące dla kontenerów są dostępne w zestawach SDK w wersji 1.12.0 i późniejszych. Zapytania muszą zawierać pojedynczy operator agregacji oraz pojedynczą wartość w projekcji.

## <a name="parallel-cross-partition-query"></a>Zapytanie równoległe obejmujące wiele partycji

Zestawy Azure Cosmos DB SDK w wersji 1.9.0 i nowszych obsługują opcje równoległego wykonywania zapytań. Zapytania równoległe obejmujące wiele partycji umożliwiają wykonywanie zapytań o wiele partycji z małym opóźnieniem. Na przykład poniższe zapytanie zostało skonfigurowane do uruchomienia równoległego w ramach partycji.

```csharp
// Cross-partition Order By Query with parallel execution
IQueryable<DeviceReading> crossPartitionQuery = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("myDatabaseName", "myCollectionName"),  
    new FeedOptions { EnableCrossPartitionQuery = true, MaxDegreeOfParallelism = 10, MaxBufferedItemCount = 100})
    .Where(m => m.MetricType == "Temperature" && m.MetricValue > 100)
    .OrderBy(m => m.MetricValue);
```

Możesz zarządzać równoległym wykonywaniem zapytań przez dostrojenie następujących parametrów:

- **MaxDegreeOfParallelism**: ustawia maksymalną liczbę równoczesnych połączeń sieciowych z partycjami kontenera. Jeśli ustawisz tę właściwość na wartość -1, zestaw SDK będzie zarządzać stopniem równoległości. Jeśli element  `MaxDegreeOfParallelism`  nie zostanie określony lub ustawiony na 0 (wartość domyślna), zostanie nawiązane jedno połączenie sieciowe z partycjami kolekcji.

- **MaxBufferedItemCount**: wyznacza kompromis między wykorzystaniem pamięci po stronie klienta i opóźnieniem zapytań. Jeśli ta opcja zostanie pominięta lub ustawiona na wartość -1, zestaw SDK będzie zarządzać liczbą elementów buforowanych podczas równoległego wykonywania zapytań.

Jeśli kolekcja ma taki sam stan, zapytanie równoległe zwraca wyniki w tej samej kolejności jak wykonanie szeregowe. W przypadku wykonywania zapytań między partycjami, które uwzględniają operatory sortowania (ORDER BY, TOP), zestaw SDK usługi Azure Cosmos DB uruchamia zapytanie równolegle w partycjach. Powoduje to scalenie częściowo posortowanych wyników po stronie klienta w celu wygenerowania globalnie uporządkowanych wyników.

## <a name="next-steps"></a>Następne kroki

Zobacz następujące artykuły, aby dowiedzieć się więcej na temat partycjonowania w usłudze Azure Cosmos DB:

- [Partitioning in Azure Cosmos DB (Partycjonowanie w usłudze Azure Cosmos DB)](partitioning-overview.md)
- [Synthetic partition keys in Azure Cosmos DB (Syntetyczne klucze partycji w usłudze Azure Cosmos DB)](synthetic-partition-keys.md)
