---
title: Azure DB rozwiązania Cosmos skali i testowania wydajności | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wykonać testowanie z bazy danych Azure rozwiązania Cosmos wydajności i skalowania
keywords: Testowanie wydajności
services: cosmos-db
author: arramac
manager: jhubbard
editor: ''
documentationcenter: ''
ms.assetid: f4c96ebd-f53c-427d-a500-3f28fe7b11d0
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/29/2017
ms.author: arramac
ms.openlocfilehash: 36665c0c26dad4e3b667b341b63c5859ece7605b
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2018
---
# <a name="performance-and-scale-testing-with-azure-cosmos-db"></a>Wydajność i skalę testowania z bazy danych Azure rozwiązania Cosmos

Skala testowanie wydajności i jest klucza krok w rozwoju aplikacji. Dla wielu aplikacji warstwy bazy danych ma znaczący wpływ na ogólną wydajność i skalowalność. W związku z tym jest składnikiem krytycznym testowania wydajności. [Azure DB rozwiązania Cosmos](https://azure.microsoft.com/services/cosmos-db/) jest dedykowanym elastyczne skalowanie i przewidywalną wydajność. Te możliwości stanowić stanowi doskonałe dopasowanie dla aplikacji, które wymagają warstwy wysokiej wydajności bazy danych. 

W tym artykule jest odwołaniem dla deweloperów wykonania testów wydajności dla obciążeń bazy danych Azure rozwiązania Cosmos. Ponadto umożliwia do oceny bazy danych Azure rozwiązania Cosmos w przypadku scenariuszy wysokiej wydajności aplikacji. Skupiono się głównie na testowania wydajności izolowanej bazy danych, ale zawiera również najlepsze rozwiązania dotyczące aplikacji produkcyjnych.

Po przeczytaniu tego artykułu, będziesz mieć możliwość odpowiedzieć na następujące pytania: 

* Gdzie mogę znaleźć przykładowej aplikacji .NET klienta do testów wydajności bazy danych Azure rozwiązania Cosmos 
* Jak osiągnięcia wysokiej przepływności poziomu z bazy danych Azure rozwiązania Cosmos z mojej aplikacji klienta?

Aby rozpocząć pracę z kodem, Pobierz projektu z [wydajności bazy danych Azure rozwiązania Cosmos testowania próbki](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/documentdb-benchmark). 

> [!NOTE]
> Celem tej aplikacji jest pokazujący sposób uzyskać najlepszą wydajność z bazy danych Azure rozwiązania Cosmos o niewielkiej liczby komputerów klienckich. Celem próbki jest nie do osiągnięcia pojemność przepływności pików bazy danych rozwiązania Cosmos Azure (które można skalować bez żadnych limitów).
> 
> 

Jeśli szukasz opcji konfiguracji po stronie klienta w celu poprawy wydajności bazy danych rozwiązania Cosmos Azure, zobacz [porady dotyczące wydajności bazy danych Azure rozwiązania Cosmos](performance-tips.md).

## <a name="run-the-performance-testing-application"></a>Uruchom testowania aplikacji
Aby skompilować i uruchomić przykładowe .NET jest najszybszym sposobem na rozpoczęcie pracy, zgodnie z opisem w poniższych krokach. Można również przejrzeć kod źródłowy i zaimplementować podobnych konfiguracji własnych aplikacji klienckich.

**Krok 1:** pobrać projekt z [wydajności bazy danych Azure rozwiązania Cosmos testowania próbki](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/documentdb-benchmark), lub rozwidlania repozytorium GitHub.

**Krok 2:** zmodyfikować ustawienia adres URL punktu końcowego, AuthorizationKey CollectionThroughput i DocumentTemplate (opcjonalnie) w pliku App.config.

> [!NOTE]
> Przed udostępnieniem kolekcje z wysokiej przepływności dotyczą [strony cennik](https://azure.microsoft.com/pricing/details/cosmos-db/) oszacowanie kosztów na kolekcję. Azure DB rozwiązania Cosmos rachunków pamięci masowej i przepływność niezależnie co godzinę. Można zmniejszyć koszty przez usunięcie lub obniżenia przepływność kolekcji bazy danych rozwiązania Cosmos Azure po zakończeniu testowania.
> 
> 

**Krok 3:** skompilować i uruchomić aplikację konsoli z wiersza polecenia. Powinny pojawić się dane wyjściowe podobne do następujących:

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


**Krok 4 (Jeśli to konieczne):** przepływność zgłoszone (RU/s) z narzędzia powinny być takie same lub wyższe niż przepływność kolekcji. Jeśli nie, zwiększenie DegreeOfParallelism w małych odstępach mogą ułatwić osiągnie limit. Jeśli płaskowyżach przepływności z aplikacji klienta, należy uruchomić wiele wystąpień aplikacji na komputerach klienckich w dodatkowych. Jeśli potrzebujesz pomocy w ramach tego kroku, Wyślij wiadomość e-mail askcosmosdb@microsoft.com lub pliku biletu pomocy technicznej z [portalu Azure](https://portal.azure.com).

Po utworzeniu aplikacji możesz spróbować innego [zasady indeksowania](indexing-policies.md) i [poziomy spójności](consistency-levels.md) zrozumienie ich wpływ na przepustowości i opóźnień. Można również przejrzeć kod źródłowy i wdrożenia podobnych konfiguracji własne zestawy testów lub aplikacje produkcyjne.

## <a name="next-steps"></a>Kolejne kroki
W tym artykule analizujemy sposobu wykonywania wydajności i skalowania testowanie z bazy danych rozwiązania Cosmos Azure za pomocą aplikacji konsoli .NET. Aby uzyskać więcej informacji zobacz następujące artykuły:

* [Testowanie próbki Azure wydajności bazy danych rozwiązania Cosmos](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/documentdb-benchmark)
* [Opcje konfiguracji klienta, aby zwiększyć wydajność bazy danych Azure rozwiązania Cosmos](performance-tips.md)
* [Partycjonowanie po stronie serwera w usłudze Azure DB rozwiązania Cosmos](partition-data.md)


