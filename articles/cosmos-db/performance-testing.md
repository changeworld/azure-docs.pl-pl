---
title: Testowanie wydajności i skalowania za pomocą usługi Azure Cosmos DB
description: Dowiedz się, jak przeprowadzać testy skali i wydajności za pomocą usługi Azure Cosmos DB. Następnie można ocenić funkcjonalność usługi Azure Cosmos DB dla scenariuszy aplikacji o wysokiej wydajności.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: fb510c5628913fb3fa37b572c4409aee5d1028ab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76313760"
---
# <a name="performance-and-scale-testing-with-azure-cosmos-db"></a>Testowanie wydajności i skalowania za pomocą usługi Azure Cosmos DB

Testowanie wydajności i skali jest kluczowym krokiem w rozwoju aplikacji. Dla wielu aplikacji warstwy bazy danych ma znaczący wpływ na ogólną wydajność i skalowalność. W związku z tym jest kluczowym elementem testowania wydajności. [Usługa Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) jest specjalnie stworzona z myślą o skali elastycznej i przewidywalnej wydajności. Te możliwości sprawiają, że doskonale nadaje się do aplikacji, które wymagają warstwy bazy danych o wysokiej wydajności. 

Ten artykuł jest odwołaniem dla deweloperów implementujących zestawy testów wydajności dla ich obciążeń usługi Azure Cosmos DB. Może również służyć do oceny usługi Azure Cosmos DB dla scenariuszy aplikacji o wysokiej wydajności. Koncentruje się przede wszystkim na izolowane testowanie wydajności bazy danych, ale zawiera również najlepsze rozwiązania dla aplikacji produkcyjnych.

Po przeczytaniu tego artykułu będziesz mógł odpowiedzieć na następujące pytania: 

* Gdzie można znaleźć przykładową aplikację kliencką platformy .NET do testowania wydajności usługi Azure Cosmos DB? 
* Jak osiągnąć wysoki poziom przepływności za pomocą usługi Azure Cosmos DB z mojej aplikacji klienckiej?

Aby rozpocząć pracę z kodem, pobierz projekt z [przykładowego testowania wydajności usługi Azure Cosmos DB.](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/documentdb-benchmark) 

> [!NOTE]
> Celem tej aplikacji jest zademonstrowanie, jak uzyskać najlepszą wydajność z usługi Azure Cosmos DB przy niewielkiej liczbie komputerów klienckich. Celem próbki nie jest osiągnięcie maksymalnej przepustowości usługi Azure Cosmos DB (która może być skalowana bez żadnych ograniczeń).
> 
> 

Jeśli szukasz opcji konfiguracji po stronie klienta w celu zwiększenia wydajności usługi Azure Cosmos DB, zobacz [Wskazówki dotyczące wydajności usługi Azure Cosmos DB.](performance-tips.md)

## <a name="run-the-performance-testing-application"></a>Uruchamianie aplikacji do testowania wydajności
Najszybszym sposobem rozpoczęcia pracy jest skompilowanie i uruchomienie próbki .NET, zgodnie z opisem w poniższych krokach. Można również przejrzeć kod źródłowy i zaimplementować podobne konfiguracje we własnych aplikacjach klienckich.

**Krok 1:** Pobierz projekt z [przykładowego testowania wydajności usługi Azure Cosmos DB](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/documentdb-benchmark)lub rozwiń repozytorium GitHub.

**Krok 2:** Zmodyfikuj ustawienia endpointUrl, AuthorizationKey, CollectionThroughput i DocumentTemplate (opcjonalnie) w app.config.

> [!NOTE]
> Przed aprowizowanie kolekcji o wysokiej przepływności, zapoznaj się [z Cennik strony,](https://azure.microsoft.com/pricing/details/cosmos-db/) aby oszacować koszty na kolekcję. Usługa Azure Cosmos DB rozlicza magazyn i przepływność niezależnie od godzinnej. Można zaoszczędzić koszty, usuwając lub obniżając przepływność kontenerów usługi Azure Cosmos po przetestowaniu.
> 
> 

**Krok 3:** Skompiluj i uruchom aplikację konsoli z wiersza polecenia. Powinny być widoczne dane wyjściowe następujące:

    C:\Users\cosmosdb\Desktop\Benchmark>DocumentDBBenchmark.exe
    Summary:
    ---------------------------------------------------------------------
    Endpoint: https://arramacquerymetrics.documents.azure.com:443/
    Collection : db.data at 100000 request units per second
    Document Template*: Player.json
    Degree of parallelism*: -1
    ---------------------------------------------------------------------

    DocumentDBBenchmark starting...
    Found collection data with 100000 RU/s
    Starting Inserts with 100 tasks
    Inserted 4503 docs @ 4491 writes/s, 47070 RU/s (122B max monthly 1KB reads)
    Inserted 17910 docs @ 8862 writes/s, 92878 RU/s (241B max monthly 1KB reads)
    Inserted 32339 docs @ 10531 writes/s, 110366 RU/s (286B max monthly 1KB reads)
    Inserted 47848 docs @ 11675 writes/s, 122357 RU/s (317B max monthly 1KB reads)
    Inserted 58857 docs @ 11545 writes/s, 120992 RU/s (314B max monthly 1KB reads)
    Inserted 69547 docs @ 11378 writes/s, 119237 RU/s (309B max monthly 1KB reads)
    Inserted 80687 docs @ 11345 writes/s, 118896 RU/s (308B max monthly 1KB reads)
    Inserted 91455 docs @ 11272 writes/s, 118131 RU/s (306B max monthly 1KB reads)
    Inserted 102129 docs @ 11208 writes/s, 117461 RU/s (304B max monthly 1KB reads)
    Inserted 112444 docs @ 11120 writes/s, 116538 RU/s (302B max monthly 1KB reads)
    Inserted 122927 docs @ 11063 writes/s, 115936 RU/s (301B max monthly 1KB reads)
    Inserted 133157 docs @ 10993 writes/s, 115208 RU/s (299B max monthly 1KB reads)
    Inserted 144078 docs @ 10988 writes/s, 115159 RU/s (298B max monthly 1KB reads)
    Inserted 155415 docs @ 11013 writes/s, 115415 RU/s (299B max monthly 1KB reads)
    Inserted 166126 docs @ 10992 writes/s, 115198 RU/s (299B max monthly 1KB reads)
    Inserted 173051 docs @ 10739 writes/s, 112544 RU/s (292B max monthly 1KB reads)
    Inserted 180169 docs @ 10527 writes/s, 110324 RU/s (286B max monthly 1KB reads)
    Inserted 192469 docs @ 10616 writes/s, 111256 RU/s (288B max monthly 1KB reads)
    Inserted 199107 docs @ 10406 writes/s, 109054 RU/s (283B max monthly 1KB reads)
    Inserted 200000 docs @ 9930 writes/s, 104065 RU/s (270B max monthly 1KB reads)

    Summary:
    ---------------------------------------------------------------------
    Inserted 200000 docs @ 9928 writes/s, 104063 RU/s (270B max monthly 1KB reads)
    ---------------------------------------------------------------------
    DocumentDBBenchmark completed successfully.
    Press any key to exit...


**Krok 4 (w razie potrzeby):** Przepływność zgłaszane (RU/s) z narzędzia powinny być takie same lub wyższe niż aprowizowana przepływność kolekcji lub zestaw kolekcji. Jeśli tak nie jest, zwiększenie DegreeOfParallelism w małych przyrostach może pomóc w osiągnięciu limitu. Jeśli przepływność z płaskowyżu aplikacji klienta, uruchom wiele wystąpień aplikacji na dodatkowych komputerach klienckich. Jeśli potrzebujesz pomocy z tym plikiem kroku bilet pomocy technicznej z [witryny Azure portal](https://portal.azure.com).

Po uruchomieniu aplikacji można wypróbować różne [zasady indeksowania](index-policy.md) i [poziomy spójności,](consistency-levels.md) aby zrozumieć ich wpływ na przepływność i opóźnienie. Można również przejrzeć kod źródłowy i zaimplementować podobne konfiguracje do własnych zestawów testów lub aplikacji produkcyjnych.

## <a name="next-steps"></a>Następne kroki
W tym artykule przyjrzeliśmy się, jak można wykonać testy wydajności i skalowania za pomocą usługi Azure Cosmos DB przy użyciu aplikacji konsoli .NET. Aby uzyskać więcej informacji zobacz następujące artykuły:

* [Przykład testowania wydajności usługi Azure Cosmos DB](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/documentdb-benchmark)
* [Opcje konfiguracji klienta w celu zwiększenia wydajności usługi Azure Cosmos DB](performance-tips.md)
* [Partycjonowanie po stronie serwera w usłudze Azure Cosmos DB](partition-data.md)


