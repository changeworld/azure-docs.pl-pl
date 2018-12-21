---
title: Wykonywanie zapytań dla kontenerów w usłudze Azure Cosmos DB
description: Dowiedz się, jak wykonywać zapytania dla kontenerów w usłudze Azure Cosmos DB
services: cosmos-db
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 11/06/2018
ms.author: mjbrown
ms.openlocfilehash: 11c68b61802f6c7b3755da71c176ea777f171e4c
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/14/2018
ms.locfileid: "53409840"
---
# <a name="query-containers-in-azure-cosmos-db"></a>Wykonywanie zapytań dla kontenerów w usłudze Azure Cosmos DB

W tym artykule wyjaśniono, jak wykonywać zapytania dla kontenera (kolekcji, grafu, tabeli) w usłudze Azure Cosmos DB.

## <a name="in-partition-query"></a>Zapytanie wewnątrz partycji

Kiedy wykonujesz zapytanie o dane z kontenerów, jeśli zapytanie ma określony filtr klucza partycji, usługa Azure Cosmos DB automatycznie przekierowuje zapytanie do partycji odpowiadających wartościom klucza partycji określonym w filtrze. Na przykład poniższe zapytanie jest kierowane do partycji DeviceId, która przechowuje wszystkie dokumenty odpowiadające wartości klucza partycji „XMS-0001”.

```csharp
// Query using partition key into a class called, DeviceReading
IQueryable<DeviceReading> query = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("myDatabaseName", "myCollectionName"))
    .Where(m => m.MetricType == "Temperature" && m.DeviceId == "XMS-0001");
```

## <a name="cross-partition-query"></a>Zapytanie obejmujące wiele partycji

Poniższe zapytanie nie ma filtra klucza partycji (DeviceId) i jest rozsyłane do wszystkich partycji, w których jest wykonywane względem indeksu partycji. Aby wykonać zapytanie obejmujące wiele partycji, należy ustawić element **EnableCrossPartitionQuery** na wartość true (lub wartość x-ms-documentdb-query-enablecrosspartition w interfejsie API REST).

```csharp
// Query across partition keys into a class called, DeviceReading
IQueryable<DeviceReading> crossPartitionQuery = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("myDatabaseName", "myCollectionName"),
    new FeedOptions { EnableCrossPartitionQuery = true })
    .Where(m => m.MetricType == "Temperature" && m.MetricValue > 100);
```

Usługa Cosmos DB obsługuje dla kontenerów funkcje agregujące COUNT, MIN, MAX oraz AVG przy użyciu języka SQL. Funkcje agregujące dla kontenerów są dostępne w zestawach SDK w wersji 1.12.0 i nowszych. Zapytania muszą zawierać pojedynczy operator agregacji oraz pojedynczą wartość w projekcji.

## <a name="parallel-cross-partition-query"></a>Zapytanie równoległe obejmujące wiele partycji

Zestawy Cosmos DB SDK w wersji 1.9.0 i wyższych obsługują opcje równoległego wykonywania zapytań.  Zapytania równoległe obejmujące wiele partycji umożliwiają wykonywanie zapytań o wiele partycji z małym opóźnieniem. Na przykład poniższe zapytanie zostało skonfigurowane do uruchomienia równoległego w ramach partycji.

```csharp
// Cross-partition Order By Query with parallel execution
IQueryable<DeviceReading> crossPartitionQuery = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("myDatabaseName", "myCollectionName"),  
    new FeedOptions { EnableCrossPartitionQuery = true, MaxDegreeOfParallelism = 10, MaxBufferedItemCount = 100})
    .Where(m => m.MetricType == "Temperature" && m.MetricValue > 100)
    .OrderBy(m => m.MetricValue);
```

Możesz zarządzać równoległym wykonywaniem zapytań przez dostrojenie następujących parametrów:

- **MaxDegreeOfParallelism**: ustawia maksymalną liczbę równoczesnych połączeń sieciowych z partycjami kontenera. Jeśli ustawisz tę właściwość na wartość -1, stopień równoległości będzie zarządzany przez zestaw SDK. Jeśli element MaxDegreeOfParallelism nie zostanie określony lub będzie ustawiony na wartość 0, czyli wartość domyślną, zostanie nawiązane jedno połączenie sieciowe z partycjami kontenera.

- **MaxBufferedItemCount**: wyznacza kompromis między wykorzystaniem pamięci po stronie klienta i opóźnieniem zapytań. Jeśli ta opcja zostanie pominięta lub ustawiona na wartość -1, liczba elementów buforowanych podczas równoległego wykonywania zapytań będzie zarządzana przez zestaw SDK.

Jeśli kolekcja będzie mieć taki sam stan, zapytanie równoległe zwróci wyniki w tej samej kolejności jak wykonanie szeregowe. Podczas wykonywania zapytań obejmujących wiele partycji z uwzględnieniem operatorów sortowania (ORDER BY i/lub TOP) zestaw SDK usługi Azure Cosmos DB uruchamia zapytania równolegle w partycjach i scala częściowo posortowane wyniki po stronie klienta, aby wygenerować wyniki uporządkowane globalnie.

## <a name="next-steps"></a>Następne kroki

Zobacz następujące artykuły, aby dowiedzieć się więcej na temat partycjonowania w usłudze Cosmos DB:

- [Partitioning in Azure Cosmos DB (Partycjonowanie w usłudze Azure Cosmos DB)](partitioning-overview.md)
- [Synthetic partition keys in Azure Cosmos DB (Syntetyczne klucze partycji w usłudze Azure Cosmos DB)](synthetic-partition-keys.md)
