---
title: Monitorowanie i debugowanie za pomocą metryki w usłudze Azure DB rozwiązania Cosmos | Dokumentacja firmy Microsoft
description: Metryki w usłudze Azure DB rozwiązania Cosmos do debugowania typowe problemy i monitorowania bazy danych.
keywords: metrics
services: cosmos-db
author: gnot
manager: kfile
editor: ''
documentationcenter: ''
ms.assetid: ''
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: govindk
ms.openlocfilehash: a2934e636166867ce158d79897d8f1fccb1699fc
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="monitoring-and-debugging-with-metrics-in-azure-cosmos-db"></a>Monitorowanie i debugowanie za pomocą metryki w usłudze Azure DB rozwiązania Cosmos

Azure DB rozwiązania Cosmos zawiera metryki dotyczące przepływności, magazynu, spójności, dostępnością i opóźnieniem. [Portalu Azure](https://portal.azure.com) zapewnia zagregowany widok tych metryk; dla bardziej szczegółowego metryki, klient zestawu SDK i [dzienników diagnostycznych](./logging.md) są dostępne.

Aby uzyskać przegląd nowe metryki i Dowiedz się, Znajdź gorących partycji w bazie danych, obejrzyj następujące wideo piątek z Azure:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Azure-Cosmos-DB-Get-the-Most-Out-of-Provisioned-Throughput/player]
> 

W tym artykule przedstawiono typowe przypadki użycia i jak bazy danych Azure rozwiązania Cosmos metryk umożliwia analizowanie i debugowanie tych problemów. Metryki są zbierane co pięć minut i są przechowywane przez 7 dni.

## <a name="understanding-how-many-requests-are-succeeding-or-causing-errors"></a>Opis, ile żądań są pomyślne lub powoduje błędy

Aby rozpocząć, przejdź do [portalu Azure](https://portal.azure.com) i przejdź do **metryki** bloku. W bloku, Znajdź **liczba żądań przekroczyła pojemności na 1 minutę** wykresu. Ten wykres pokazuje minutę za minutę całkowita liczba żądań segmentowanych przez kod stanu. Aby uzyskać więcej informacji o kodach stanu HTTP, zobacz [kodów stanu HTTP dla bazy danych Azure rozwiązania Cosmos](https://docs.microsoft.com/rest/api/cosmos-db/http-status-codes-for-cosmosdb).

Najbardziej typowe kod stanu błędu jest 429 (ograniczanie), co oznacza, że żądania do bazy danych Azure rozwiązania Cosmos mają przekraczającą udostępnionej przepływności. Najbardziej typowe rozwiązaniem jest [skalowanie w górę RUs](./set-throughput.md) dla danej kolekcji.

![Liczba żądań na minutę](media/use-metrics/metrics-12.png)

## <a name="determining-the-throughput-distribution-across-partitions"></a>Określanie dystrybucji przepływności na partycje

Dobrym Kardynalność kluczy partycji jest istotne dla dowolnej skalowalnych aplikacji. Aby ustalić dystrybucji przepływność kolekcji partycjonowanych podziale na partycje, przejdź do **bloku metryki** w [portalu Azure](https://portal.azure.com). W **przepływności** karcie Podział magazynu jest wyświetlany w obszarze **maksymalna liczba zużywane RU/sekundę przy każdej partycji fizycznej** wykresu. Poniższa ilustracja przedstawia przykład niską dystrybucji danych oznaką spowodowałoby zafałszowanie partycji w lewo. 

![Jedna partycja wyświetlanie duże obciążenie na 15:05:00](media/use-metrics/metrics-17.png)

Nierówna przepływności dystrybucji może spowodować *gorących* partycje, które mogą skutkować żądań ograniczeniem przepustowości i może wymagać ponownego dzielenia na partycje. Aby uzyskać więcej informacji na temat partycjonowania w usłudze Azure DB rozwiązania Cosmos, zobacz [partycji i skali w usłudze Azure DB rozwiązania Cosmos](./partition-data.md).

## <a name="determining-the-storage-distribution-across-partitions"></a>Określanie dystrybucji magazynu na partycje

Dobrym Kardynalność partycji jest istotne dla dowolnej skalowalnych aplikacji. Aby ustalić dystrybucji przepływność kolekcji partycjonowanych podziale na partycje, przejdź do bloku metryki w [portalu Azure](https://portal.azure.com). Na karcie przepływności Podział magazynu jest wyświetlana w maksymalnej liczby używane przez każdy wykres partycji fizycznej RU/sekundę. Poniższa ilustracja przedstawia niską dystrybucji danych oznaką spowodowałoby zafałszowanie partycji w lewo. 

![Przykład dystrybucji niską danych](media/use-metrics/metrics-07.png)

Można główna przyczyna klucz partycji, do którego jest pochylanie dystrybucji, klikając na partycji na wykresie. 

![Klucz partycji jest pochylanie dystrybucji](media/use-metrics/metrics-05.png)

Po identyfikacji klucza partycji, która powoduje pochylenie w dystrybucji, może być na partycje kolekcji z bardziej rozproszonymi klucza partycji. Aby uzyskać więcej informacji na temat partycjonowania w usłudze Azure DB rozwiązania Cosmos, zobacz [partycji i skali w usłudze Azure DB rozwiązania Cosmos](./partition-data.md).

## <a name="comparing-data-size-against-index-size"></a>Porównywanie rozmiar danych przed rozmiar indeksu

W usłudze Azure DB rozwiązania Cosmos całkowita użytego miejsca do magazynowania jest kombinacja rozmiar danych i rozmiar indeksu. Zazwyczaj rozmiar indeksu jest część rozmiar danych. W bloku metryki w [portalu Azure](https://portal.azure.com), karta Magazyn ilustrację podział użycia magazynu na podstawie danych i indeks. Obraz (może być) można również z zestawu SDK, można znaleźć bieżące użycie magazynu za pomocą kolekcji, do odczytu.
```csharp
// Measure the document size usage (which includes the index size)  
ResourceResponse<DocumentCollection> collectionInfo = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("db", "coll")); 
 Console.WriteLine("Document size quota: {0}, usage: {1}", collectionInfo.DocumentQuota, collectionInfo.DocumentUsage);
``` 
Jeśli chcesz zaoszczędzić miejsce na indeks, można dostosować [indeksowania zasad](./indexing-policies.md).

## <a name="debugging-why-queries-are-running-slow"></a>Debugowanie, dlatego zapytania są przetwarzane wolno

W z zestawów SDK interfejsu API SQL Azure DB rozwiązania Cosmos zapewnia statystyk wykonywania zapytań. 

```csharp
IDocumentQuery<dynamic> query = client.CreateDocumentQuery(
 UriFactory.CreateDocumentCollectionUri(DatabaseName, CollectionName), 
 “SELECT * FROM c WHERE c.city = ‘Seattle’”, 
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

*QueryMetrics* zawiera szczegółowe informacje na jak długo trwało każdego składnika zapytania do wykonania. Najczęstszą przyczyną głównego długo działa zapytania są skanowania (zapytania nie może korzystać z indeksów), które mogą zostać rozwiązane lepsze warunek filtru.

## <a name="next-steps"></a>Kolejne kroki

Teraz, kiedy znasz już jak monitorować i debugowaniu problemów przy użyciu metryk dostępnych w portalu Azure, możesz dowiedzieć się więcej na temat zwiększania wydajności bazy danych, przeczytaj następujące artykuły:

* [Wydajność i skalę testowania z bazy danych Azure rozwiązania Cosmos](performance-testing.md)
* [Porady dotyczące wydajności dla bazy danych Azure rozwiązania Cosmos](performance-tips.md)
