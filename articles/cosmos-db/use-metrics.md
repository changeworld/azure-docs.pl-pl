---
title: Monitorowanie i debugowaniu za pomocą metryk w usłudze Azure Cosmos DB | Dokumentacja firmy Microsoft
description: Metryki w usłudze Azure Cosmos DB do debugowania typowe problemy i monitorowania bazy danych.
keywords: metrics
services: cosmos-db
author: kanshiG
manager: kfile
editor: ''
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 09/25/2017
ms.author: govindk
ms.openlocfilehash: 792e0b3f8fdfe4ab1b79fec5f45d0587033eca0d
ms.sourcegitcommit: ebd06cee3e78674ba9e6764ddc889fc5948060c4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/07/2018
ms.locfileid: "44055203"
---
# <a name="monitoring-and-debugging-with-metrics-in-azure-cosmos-db"></a>Monitorowanie i debugowaniu za pomocą metryk w usłudze Azure Cosmos DB

Usługa Azure Cosmos DB zawiera metryki dotyczące przepływności, magazynu, spójnością, dostępnością i opóźnieniem. [Witryny Azure portal](https://portal.azure.com) zapewnia zagregowany widok z tych wskaźników; w przypadku bardziej szczegółowych metryk, klienckiego zestawu SDK i [dzienniki diagnostyczne](./logging.md) są dostępne.

W tym artykule przedstawiono typowe przypadki użycia i jak metryki usługi Azure Cosmos DB umożliwia analizowanie i debugowanie tych problemów. Metryki są zbierane, co pięć minut i są przechowywane przez 7 dni.

## <a name="understanding-how-many-requests-are-succeeding-or-causing-errors"></a>Informacje o ile żądań jest powodzeniem lub powoduje błędy

Aby rozpocząć, przejdź do [witryny Azure portal](https://portal.azure.com) i przejdź do **metryki** bloku. W bloku Znajdź **liczba żądań, które przekroczyły pojemność na minutę** wykresu. Ten wykres przedstawia minutę przez minutę łączna liczba żądań, posegmentowana według kodu stanu. Aby uzyskać więcej informacji na temat kodów stanu HTTP, zobacz [kodów stanu HTTP dla usługi Azure Cosmos DB](https://docs.microsoft.com/rest/api/cosmos-db/http-status-codes-for-cosmosdb).

Najczęściej kod statusu błędu jest 429 (ocenić, ograniczając ograniczania), co oznacza, że żądania do usługi Azure Cosmos DB są przekroczenia aprowizowanej przepływności. Najbardziej typowe rozwiązaniem jest [skalować jednostki zarezerwowane](./set-throughput.md) dla danej kolekcji.

![Liczba żądań na minutę](media/use-metrics/metrics-12.png)

## <a name="determining-the-throughput-distribution-across-partitions"></a>Określająca dystrybucji przepływności na partycje

Dobre Kardynalność klucze partycji jest istotne dla skalowalnej aplikacji. Aby określić rozkład przepływność dowolnego kontener podzielony na partycje z podziałem na partycje, przejdź do **blok metryk** w [witryny Azure portal](https://portal.azure.com). W **przepływności** karcie Podział magazynu jest wyświetlany w **maks. zużycie RU/s na każdą fizyczną partycję** wykresu. Poniższa ilustracja przedstawia przykład niską dystrybucji danych, co dowodzi niesymetryczne partycji na końcu z lewej strony. 

![Jedna partycja, widzisz duże obciążenie na 15:05:00](media/use-metrics/metrics-17.png)

Rozkład normalny przepływności może spowodować, że *gorąca* partycji, co może spowodować żądania ograniczone i może wymagać ponownego dzielenia na partycje. Aby uzyskać więcej informacji na temat partycjonowania w usłudze Azure Cosmos DB, zobacz [partycji i skali w usłudze Azure Cosmos DB](./partition-data.md).

## <a name="determining-the-storage-distribution-across-partitions"></a>Określająca dystrybucji magazynu na partycje

Dobre Kardynalność partycji jest istotne dla skalowalnej aplikacji. Aby określić rozkład przepływność dowolnego kontener podzielony na partycje z podziałem na partycje, przejdź do bloku metryk w [witryny Azure portal](https://portal.azure.com). Na karcie przepływności Podział magazynu jest wyświetlana w maks. zużycie RU/s według poszczególnych wykresów fizyczną partycję. Poniższa ilustracja przedstawia niską dystrybucji danych, co dowodzi niesymetryczne partycji na końcu z lewej strony. 

![Przykład dystrybucji niską danych](media/use-metrics/metrics-07.png)

Można główna przyczyna, w których klucza partycji jest pochylanie dystrybucji, klikając na partycji na wykresie. 

![Klucz partycji jest pochylanie dystrybucji](media/use-metrics/metrics-05.png)

Po identyfikacji klucza partycji, który jest przyczyną pochylenia dystrybucji, może być na partycje kontenera przy użyciu bardziej rozproszonymi klucza partycji. Aby uzyskać więcej informacji na temat partycjonowania w usłudze Azure Cosmos DB, zobacz [partycji i skali w usłudze Azure Cosmos DB](./partition-data.md).

## <a name="comparing-data-size-against-index-size"></a>Porównując rozmiar danych względem rozmiar indeksu

W usłudze Azure Cosmos DB całkowita ilość miejsca wykorzystanych jest kombinacja rozmiar danych i rozmiar indeksu. Zazwyczaj rozmiar indeksu jest ułamek rozmiaru danych. W bloku metryk w [witryny Azure portal](https://portal.azure.com), karty magazyn przedstawia podział użycia magazynu na podstawie danych i indeksu. Obraz (być może) również z zestawu SDK, można znaleźć bieżące użycie pamięci masowej w kolekcji, przeczytaj.
```csharp
// Measure the document size usage (which includes the index size)  
ResourceResponse<DocumentCollection> collectionInfo = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("db", "coll")); 
 Console.WriteLine("Document size quota: {0}, usage: {1}", collectionInfo.DocumentQuota, collectionInfo.DocumentUsage);
``` 
Jeśli chcesz zaoszczędzić miejsce na indeks, można dostosować [zasad indeksowania](./indexing-policies.md).

## <a name="debugging-why-queries-are-running-slow"></a>Debugowanie, dlatego zapytania są powolne

W zestawami SDK interfejsu API SQL usługi Azure Cosmos DB zapewnia statystyk wykonywania zapytań. 

```csharp
IDocumentQuery<dynamic> query = client.CreateDocumentQuery(
 UriFactory.CreateDocumentCollectionUri(DatabaseName, CollectionName), 
 "SELECT * FROM c WHERE c.city = 'Seattle'", 
 new FeedOptions 
 { 
 PopulateQueryMetrics = true, 
 MaxItemCount = -1, 
 MaxDegreeOfParallelism = -1, 
 EnableCrossPartitionQuery = true 
 }).AsDocumentQuery();
FeedResponse<dynamic> result = await query.ExecuteNextAsync();

// Returns metrics by partition key range Id 
IReadOnlyDictionary<string, QueryMetrics> metrics = result.QueryMetrics;
```

*QueryMetrics* zawiera szczegółowe informacje na jak długo trwało każdy składnik kwerendy do wykonania. Najbardziej typowe przyczyny do uruchomienia długie zapytania są skanowania (zapytanie nie może korzystać z indeksów), które mogą być rozwiązane za pomocą lepsze warunku filtru.

## <a name="next-steps"></a>Kolejne kroki

Teraz, wyjaśniono, jak monitorować i debugowanie problemów przy użyciu metryk w witrynie Azure portal, można dowiedzieć się więcej na temat zwiększania wydajności bazy danych, czytając następujące artykuły:

* [Wydajność i skalę, testowanie za pomocą usługi Azure Cosmos DB](performance-testing.md)
* [Porady dotyczące wydajności usługi Azure Cosmos DB](performance-tips.md)
