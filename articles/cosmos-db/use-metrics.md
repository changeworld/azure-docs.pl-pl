---
title: Monitorowanie i debugowanie za pomocą metryk w usłudze Azure Cosmos DB
description: Użyj metryki w usłudze Azure Cosmos DB do debugowania typowych problemów i monitorowania bazy danych.
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/18/2019
ms.openlocfilehash: b65bc6097d4841c79a68d4313ac7a3f89f6d1dbb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80065930"
---
# <a name="monitor-and-debug-with-metrics-in-azure-cosmos-db"></a>Monitorowanie i debugowanie za pomocą metryk w usłudze Azure Cosmos DB

Usługa Azure Cosmos DB udostępnia metryki dotyczące przepływności, magazynu, spójności, dostępności i opóźnienia. Witryna Azure Portal oferuje zagregowany widok tych metryk. Możesz również wyświetlić metryki usługi Azure Cosmos DB z poziomu interfejsu API usługi Azure Monitor. Aby dowiedzieć się, jak wyświetlić metryki z monitora platformy Azure, zobacz [Pobierz metryki z usługi Azure Monitor](cosmos-db-azure-monitor-metrics.md) artykułu. 

W tym artykule otoczy się typowe przypadki użycia i jak metryki usługi Azure Cosmos DB mogą służyć do analizowania i debugowania tych problemów. Dane są zbierane co pięć minut i są przechowywane przez siedem dni.

## <a name="view-metrics-from-azure-portal"></a>Wyświetlanie metryk z witryny Azure Portal

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com/).

1. Otwórz okienko **Metryki.** Domyślnie okienko metryki pokazuje metryki magazynu, indeksu, jednostek żądań dla wszystkich baz danych na koncie usługi Azure Cosmos. Można filtrować te metryki według bazy danych, kontenera lub regionu. Można również filtrować metryki w określonym czasie ziarnistość. Więcej szczegółów na temat przepływności, magazynu, dostępności, opóźnienia i spójności metryki są dostępne na oddzielnych kartach. 

   ![Metryki wydajności usługi Cosmos DB w witrynie Azure portal](./media/use-metrics/performance-metrics.png)

W okienku **Metryki** dostępne są następujące metryki: 

* **Metryki przepływności** — ta metryka pokazuje liczbę żądań zużytych lub nie powiodło się (kod odpowiedzi 429), ponieważ przepływność lub pojemność magazynu aprowizowana dla kontenera została przekroczona.

* **Metryki magazynu** — ta metryka pokazuje rozmiar danych i użycie indeksu.

* **Metryki dostępności** — ta metryka pokazuje procent pomyślnych żądań w całkowitej liczbie żądań na godzinę. Wskaźnik powodzenia jest zdefiniowany przez instaly instalowe ND bazy danych usługi Azure Cosmos.

* **Metryki opóźnienia** — ta metryka pokazuje opóźnienie odczytu i zapisu obserwowane przez usługę Azure Cosmos DB w regionie, w którym działa twoje konto. Można wizualizować opóźnienie w różnych regionach dla konta replikowanego geograficznie. Ta metryka nie reprezentuje opóźnienia żądania end-to-end.

* **Metryki spójności** — ta metryka pokazuje, jak ostateczna jest spójność dla wybranego modelu spójności. W przypadku kont wieloregionacyjnych ta metryka pokazuje również opóźnienie replikacji między wybranymi regionami.

* **Metryki systemu** — ta metryka pokazuje, ile żądań metadanych są obsługiwane przez partycję główną. Pomaga również zidentyfikować ograniczone żądania.

W poniższych sekcjach wyjaśniono typowe scenariusze, w których można użyć metryk usługi Azure Cosmos DB. 

## <a name="understand-how-many-requests-are-succeeding-or-causing-errors"></a>Dowiedz się, ile żądań jest powiodujących się lub powodujących błędy

Aby rozpocząć, przejdź do [witryny Azure portal](https://portal.azure.com) i przejdź do bloku **Metryki.** W bloku znajdź **Liczba żądań przekroczonych pojemności na wykres 1-minutowy. Ten wykres przedstawia liczbę żądań sumy minuta po minucie podzielonych na segmenty według kodu stanu. Aby uzyskać więcej informacji na temat kodów stanu HTTP, zobacz [Kody stanu HTTP dla usługi Azure Cosmos DB](https://docs.microsoft.com/rest/api/cosmos-db/http-status-codes-for-cosmosdb).

Najczęstszy kod stanu błędu to 429 (ograniczenie szybkości/ograniczanie przepustowości). Ten błąd oznacza, że żądania do usługi Azure Cosmos DB są więcej niż aprowizowana przepływność. Najczęstszym rozwiązaniem tego problemu jest [skalowanie w górę procesorów RU](./set-throughput.md) dla danej kolekcji.

![Liczba żądań na minutę](media/use-metrics/metrics-12.png)

## <a name="determine-the-throughput-distribution-across-partitions"></a>Określanie dystrybucji przepływności między partycjami

Posiadanie dobrej kardynalności kluczy partycji jest niezbędne dla każdej skalowalnej aplikacji. Aby określić dystrybucję przepływności dowolnego kontenera podzielonego na partycje, przejdź do **bloku Metryki** w [witrynie Azure portal.](https://portal.azure.com) Na karcie **Przepływność** podział magazynu jest wyświetlany w **maksymalnej zużywanej ru/sekundy przez każdy wykres partycji fizycznej.** Poniższa grafika ilustruje przykład słabego rozkładu danych, jak pokazano przez przekrzywiony partycji po lewej stronie.

![Pojedyncza partycja widząc intensywne użycie na 15:05](media/use-metrics/metrics-17.png)

Nierównomierny rozkład przepływności może spowodować *partycje gorące,* co może spowodować ograniczone żądania i może wymagać ponownego partycjonowania. Aby uzyskać więcej informacji na temat partycjonowania w usłudze Azure Cosmos DB, zobacz [Partycja i skalowanie w usłudze Azure Cosmos DB](./partition-data.md).

## <a name="determine-the-storage-distribution-across-partitions"></a>Określanie dystrybucji magazynu między partycjami

Posiadanie dobrej kardynalności partycji jest niezbędne dla każdej skalowalnej aplikacji. Aby określić dystrybucję magazynu dowolnego kontenera podzielonego na partycje, przejdź do bloku Metryki w [witrynie Azure portal.](https://portal.azure.com) Na karcie Magazyn podział magazynu jest wyświetlany na wykresie Dane + Magazyn indeksu zużywany przez najlepsze klucze partycji. Poniższa grafika ilustruje słabą dystrybucję przechowywania danych, jak pokazano na przekrzywionej partycji po lewej stronie.

![Przykład słabego rozkładu danych](media/use-metrics/metrics-07.png)

Możesz główną przyczynę, który klucz partycji jest wypaczanie dystrybucji, klikając na partycji na wykresie.

![Klucz partycji wypacza dystrybucję](media/use-metrics/metrics-05.png)

Po zidentyfikowaniu, który klucz partycji powoduje pochylenie w dystrybucji, może być konieczna partycjonowanie kontenera z kluczem partycji bardziej rozproszonej. Aby uzyskać więcej informacji na temat partycjonowania w usłudze Azure Cosmos DB, zobacz [Partycja i skalowanie w usłudze Azure Cosmos DB](./partition-data.md).

## <a name="compare-data-size-against-index-size"></a>Porównanie rozmiaru danych z rozmiarem indeksu

W usłudze Azure Cosmos DB całkowita ilość zużytego magazynu jest kombinacją rozmiaru danych i rozmiaru indeksu. Zazwyczaj rozmiar indeksu jest ułamek rozmiaru danych. W metrics bloku w [witrynie Azure portal,](https://portal.azure.com)magazyn kartę prezentuje podział zużycia magazynu na podstawie danych i indeksu.

```csharp
// Measure the document size usage (which includes the index size)  
ResourceResponse<DocumentCollection> collectionInfo = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"));
 Console.WriteLine("Document size quota: {0}, usage: {1}", collectionInfo.DocumentQuota, collectionInfo.DocumentUsage);
```

Jeśli chcesz zaoszczędzić miejsce na indeksie, możesz dostosować [zasady indeksowania](index-policy.md).

## <a name="debug-why-queries-are-running-slow"></a>Debugowanie, dlaczego kwerendy działają wolno

W zestawach SDK interfejsu API SQL usługa Azure Cosmos DB udostępnia statystyki wykonywania zapytań.

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

*QueryMetrics* zawiera szczegółowe informacje na temat czasu każdego składnika kwerendy trwało do wykonania. Najczęstszą główną przyczyną długotrwałych zapytań jest skanowanie, co oznacza, że kwerenda nie może wykorzystać indeksów. Ten problem można rozwiązać za pomocą lepszego warunku filtru.

## <a name="next-steps"></a>Następne kroki

Teraz dowiesz się, jak monitorować i debugować problemy przy użyciu metryk dostępnych w witrynie Azure portal. Możesz dowiedzieć się więcej na temat poprawy wydajności bazy danych, czytając następujące artykuły:

* Aby dowiedzieć się, jak wyświetlić metryki z monitora platformy Azure, zobacz [Pobierz metryki z usługi Azure Monitor](cosmos-db-azure-monitor-metrics.md) artykułu. 
* [Testowanie wydajności i skalowania za pomocą usługi Azure Cosmos DB](performance-testing.md)
* [Porady dotyczące wydajności usługi Azure Cosmos DB](performance-tips.md)
