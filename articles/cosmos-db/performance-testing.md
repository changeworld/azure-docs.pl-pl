---
title: Wydajność i skalę, testowanie za pomocą usługi Azure Cosmos DB
description: Dowiedz się, jak skalować i wydajności, testowanie za pomocą usługi Azure Cosmos DB. Następnie będziesz w stanie ocenić funkcje usługi Azure Cosmos DB dla scenariuszy aplikacji o wysokiej wydajności.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: 0ac257d4eb9fb9e26739f1a63049751f9298efb5
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/19/2019
ms.locfileid: "69616786"
---
# <a name="performance-and-scale-testing-with-azure-cosmos-db"></a>Wydajność i skalę, testowanie za pomocą usługi Azure Cosmos DB

Testowanie wydajności i skali jest krokiem podczas tworzenia aplikacji. W przypadku wielu aplikacji warstwy bazy danych, ma znaczący wpływ na ogólną wydajność i skalowalność. Dlatego jest kluczowym elementem testowania wydajnościowego. [Usługa Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) zaprojektowanemu pod kątem elastycznego skalowania i przewidywalną wydajność. Te funkcje umożliwiają doskonałe rozwiązanie dla aplikacji wymagających warstwę bazy danych o wysokiej wydajności. 

Ten artykuł stanowi odwołanie dla deweloperów, implementowanie zestawy testów wydajności dla obciążeń usługi Azure Cosmos DB. On również może służyć do oceny usługi Azure Cosmos DB w przypadku scenariuszy aplikacji o wysokiej wydajności. Koncentruje się głównie na wydajność w izolowanej testowanie bazy danych, ale również zawiera najlepsze rozwiązania dotyczące aplikacji produkcyjnych.

Po przeczytaniu tego artykułu, będziesz mieć możliwość odpowiedzieć na następujące pytania: 

* Gdzie można znaleźć Przykładowa aplikacja kliencka platformy .NET do testowania wydajnościowego usługi Azure Cosmos DB? 
* Jak osiągnąć poziomy wysoką przepływność dzięki usłudze Azure Cosmos DB z mojej aplikacji klienta?

Aby rozpocząć pracę z kodem, należy pobrać projekt z [testowania próbki wydajności usługi Azure Cosmos DB](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/documentdb-benchmark). 

> [!NOTE]
> Celem tej aplikacji jest przedstawienie sposobu uzyskania najlepszej wydajności z usługi Azure Cosmos DB przy użyciu niewielkiej liczby komputerów klienckich. Celem próbki jest nie osiągać szczytowe pojemność przepływności usługi Azure Cosmos DB, (które można skalować bez żadnych ograniczeń).
> 
> 

Jeśli potrzebujesz opcji konfiguracji po stronie klienta w celu poprawy wydajności usługi Azure Cosmos DB, zobacz [porady dotyczące wydajności usługi Azure Cosmos DB](performance-tips.md).

## <a name="run-the-performance-testing-application"></a>Uruchamianie aplikacji do testowania wydajnościowego
Aby skompilować i uruchomić przykład .NET jest najszybszym sposobem na rozpoczęcie pracy, zgodnie z opisem w poniższych krokach. Można również przejrzeć kod źródłowy i zaimplementować podobne konfiguracje w aplikacjach klienckich.

**Krok 1:** Pobierz projekt z [przykładu Azure Cosmos DB test wydajnościowy](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/documentdb-benchmark)lub rozwidlenie repozytorium GitHub.

**Krok 2:** Zmodyfikuj ustawienia dla EndpointUrl, AuthorizationKey, CollectionThroughput i DocumentTemplate (opcjonalnie) w pliku App. config.

> [!NOTE]
> Przed udostępnieniem kolekcji o wysokiej przepływności, można znaleźć [stronę z cennikiem](https://azure.microsoft.com/pricing/details/cosmos-db/) do szacowania kosztów w jednej kolekcji. Usługa Azure Cosmos DB opłaty naliczane są, Magazyn i przepływność niezależnie od siebie w systemie godzinowym. Możesz zaoszczędzić koszty, usuwając lub obniżając przepływność kontenerów usługi Azure Cosmos po przetestowaniu.
> 
> 

**Krok 3.** Kompiluj i uruchom aplikację konsolową z wiersza polecenia. Powinny zostać wyświetlone dane wyjściowe podobne do następujących:

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


**Krok 4 (w razie potrzeby):** Raportowana przepływność (RU/s) z narzędzia powinna być taka sama lub wyższa od alokowanej przepływności kolekcji lub zestawu kolekcji. Jeśli nie jest, zwiększenie DegreeOfParallelism w małych odstępach może pomóc Ci osiągnięcia limitu. Jeśli płaskowyżach przepływność z aplikacji klienckich, należy uruchomić wiele wystąpień aplikacji, na maszynach klienckich w dodatkowych. Jeśli potrzebujesz pomocy dotyczącej tego kroku, Wyślij wiadomość e-mail askcosmosdb@microsoft.com lub pliku biletu pomocy technicznej z [witryny Azure portal](https://portal.azure.com).

Po utworzeniu jest uruchomiona aplikacja, możesz wypróbować różne [zasady indeksowania](index-policy.md) i [poziomów spójności](consistency-levels.md) Aby zrozumieć ich wpływ na przepływność i opóźnienie. Możesz również przejrzeć kod źródłowy i zaimplementować podobne konfiguracje własne zestawy testów lub aplikacje produkcyjne.

## <a name="next-steps"></a>Kolejne kroki
W tym artykule zobaczyliśmy, jak można wykonać wydajność i skalę, testowanie za pomocą usługi Azure Cosmos DB za pomocą aplikacji konsoli .NET. Aby uzyskać więcej informacji zobacz następujące artykuły:

* [Testowanie przykładowej Azure wydajności usługi Cosmos DB](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/documentdb-benchmark)
* [Opcje konfiguracji klienta, aby poprawić wydajność usługi Azure Cosmos DB](performance-tips.md)
* [Partycjonowanie po stronie serwera w usłudze Azure Cosmos DB](partition-data.md)


