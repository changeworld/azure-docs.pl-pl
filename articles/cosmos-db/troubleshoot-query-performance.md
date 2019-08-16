---
title: Diagnozowanie i rozwiązywanie problemów z kwerendą podczas korzystania z Azure Cosmos DB
description: Dowiedz się, jak identyfikować, diagnozować i rozwiązywać problemy z Azure Cosmos DB zapytań SQL.
author: ginamr
ms.service: cosmos-db
ms.topic: troubleshooting
ms.date: 07/10/2019
ms.author: girobins
ms.subservice: cosmosdb-sql
ms.reviewer: sngun
ms.openlocfilehash: d0dd9a371c4912cae0e74b214c673c629fc1ff55
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/15/2019
ms.locfileid: "69515818"
---
# <a name="troubleshoot-query-performance-for-azure-cosmos-db"></a>Rozwiązywanie problemów z wydajnością zapytań dla Azure Cosmos DB
W tym artykule opisano sposób identyfikowania, diagnozowania i rozwiązywania problemów z Azure Cosmos DB zapytań SQL. Aby osiągnąć optymalną wydajność zapytań Azure Cosmos DB, wykonaj poniższe kroki rozwiązywania problemów. 

## <a name="collocate-clients-in-same-azure-region"></a>Kolokacja klientów w tym samym regionie świadczenia usługi Azure 
Najniższe możliwe opóźnienie jest realizowane przez zagwarantowanie, że aplikacja wywołująca znajduje się w tym samym regionie platformy Azure, co punkt końcowy Azure Cosmos DB aprowizacji. Aby uzyskać listę dostępnych regionów, zobacz artykuł dotyczący [regionów platformy Azure](https://azure.microsoft.com/global-infrastructure/regions/#services) .

## <a name="check-consistency-level"></a>Sprawdź poziom spójności
[Poziom spójności](consistency-levels.md) może mieć wpływ na wydajność i opłaty. Upewnij się, że poziom spójności jest odpowiedni dla danego scenariusza. Aby uzyskać więcej szczegółów, zobacz [Wybieranie poziomu spójności](consistency-levels-choosing.md).

## <a name="log-the-executed-sql-query"></a>Rejestruj wykonane zapytanie SQL 

Wykonane zapytanie SQL można zarejestrować na koncie magazynu lub w tabeli dzienników diagnostycznych. [Dzienniki zapytań SQL za pomocą dzienników diagnostycznych](logging.md#turn-on-logging-in-the-azure-portal) umożliwiają rejestrowanie zasłoniętego zapytania na wybranym koncie magazynu. Pozwala to na wyświetlenie dzienników i znalezienie zapytania, które używa wyższych jednostek ru. Później można użyć identyfikatora działania, aby dopasować rzeczywiste zapytanie w QueryRuntimeStatistics. Zapytanie jest ukrywane ze względów bezpieczeństwa i nazw parametrów zapytania, a ich wartości w klauzulach WHERE są inne niż rzeczywiste nazwy i wartości. Możesz użyć rejestrowania na koncie magazynu, aby zachować długoterminowe przechowywanie wykonanych zapytań.  

## <a name="log-query-metrics"></a>Metryki zapytań dziennika

Służy `QueryMetrics` do rozwiązywania problemów z niską lub kosztowną kwerendą. 

  * `FeedOptions.PopulateQueryMetrics = true` Ustaw`QueryMetrics` na wartość w odpowiedzi.
  * `QueryMetrics`Klasa ma przeciążoną `.ToString()` funkcję, którą można wywołać, aby uzyskać ciąg `QueryMetrics`reprezentujący. 
  * Metryki mogą być wykorzystywane do uzyskiwania następujących szczegółowych informacji, między innymi: 
  
      * Czy dowolny konkretny składnik potoku zapytania trwał czas nieokreślony (w kolejności setek milisekund lub więcej). 

          * Przyjrzyj `TotalExecutionTime`się.
          * `TotalExecutionTime` Jeśli zapytanie jest krótsze niż czas zakończenia wykonywania, czas jest poświęcany na klienta lub w sieci. Należy dokładnie sprawdzić, czy klient i region platformy Azure są rozłączone.
      
      * Czy w analizowanych dokumentach pojawiły się fałszywe pozytywne dane (Jeśli liczba dokumentów wyjściowych jest znacznie mniejsza niż liczba pobieranych dokumentów).  

          * Przyjrzyj `Index Utilization`się.
          * `Index Utilization`= (Liczba zwróconych dokumentów/liczba załadowanych dokumentów)
          * Jeśli liczba zwróconych dokumentów jest znacznie mniejsza niż załadowany numer, to wynik jest analizowany na wartość false.
          * Ogranicz liczbę pobieranych dokumentów przy użyciu wąskich filtrów.  

      * Jak opłaty są nastawione przez poszczególne rundy ( `Partition Execution Timeline` patrz ciąg `QueryMetrics`). 
      * Czy zapytanie zużywał wysokie żądanie. 

Aby uzyskać więcej informacji [, zobacz artykuł jak uzyskać informacje o metrykach wykonywania zapytań SQL](profile-sql-api-query.md) .
      
## <a name="tune-query-feed-options-parameters"></a>Dostrajanie parametrów opcji kanału informacyjnego zapytania 
Wydajność zapytań można dostrajać za pomocą parametrów [opcji kanału informacyjnego](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.feedoptions?view=azure-dotnet) żądania. Spróbuj ustawić poniższe opcje:

  * Najpierw `MaxDegreeOfParallelism` ustaw wartość-1, a następnie Porównaj wydajność między różnymi wartościami. 
  * Najpierw `MaxBufferedItemCount` ustaw wartość-1, a następnie Porównaj wydajność między różnymi wartościami. 
  * Ustaw `MaxItemCount` wartość-1.

Porównując wydajność różnych wartości, wypróbuj wartości, takie jak 2, 4, 8, 16 i inne.
 
## <a name="read-all-results-from-continuations"></a>Odczytywanie wszystkich wyników z kontynuacji
Jeśli uważasz, że nie otrzymujesz wszystkich wyników, pamiętaj o całkowitym opróżnieniu kontynuacji. Innymi słowy, nie przerywaj odczytywania wyników, gdy token kontynuacji ma więcej dokumentów do uzyskania.

Całkowite opróżnienie można osiągnąć, stosując jeden z następujących wzorców:

  * Kontynuuj przetwarzanie wyników, gdy kontynuacja nie jest pusta.
  * Kontynuuj przetwarzanie, gdy zapytanie ma więcej wyników. 

    ```csharp
    // using AsDocumentQuery you get access to whether or not the query HasMoreResults
    // If it does, just call ExecuteNextAsync until there are no more results
    // No need to supply a continuation token here as the server keeps track of progress
    var query = client.CreateDocumentQuery<Family>(collectionLink, options).AsDocumentQuery();
    while (query.HasMoreResults)
    {
        foreach (Family family in await query.ExecuteNextAsync())
        {
            families.Add(family);
        }
    }
    ```

## <a name="choose-system-functions-that-utilize-index"></a>Wybierz funkcje systemowe, które korzystają z indeksu
Jeśli wyrażenie można przetłumaczyć na zakres wartości ciągu, może ono korzystać z indeksu. W przeciwnym razie nie może. 

Poniżej znajduje się lista funkcji ciągów, które mogą korzystać z indeksu: 
    
  * STARTSWITH(wyrażenie_ciągu, wyrażenie_ciągu) 
  * LEFT(wyrażenie_ciągu, wyrażenie_liczbowe) = wyrażenie_ciągu 
  * SUBSTRING(wyrażenie_ciągu, wyrażenie_liczbowe, wyrażenie_liczbowe) = wyrażenie_ciągu, ale tylko w przypadku, gdy pierwsze wyrażenie_liczbowe ma wartość 0 
    
    Poniżej przedstawiono kilka przykładów zapytania: 
    
    ```sql

    -- If there is a range index on r.name, STARTSWITH will utilize the index while ENDSWITH won't 
    SELECT * 
    FROM c 
    WHERE STARTSWITH(c.name, 'J') AND ENDSWITH(c.name, 'n')

    ```
    
    ```sql

    -- LEFT will utilize the index while RIGHT won't 
    SELECT * 
    FROM c 
    WHERE LEFT(c.name, 2) = 'Jo' AND RIGHT(c.name, 2) = 'hn'

    ```

  * Należy unikać funkcji systemowych w filtrze (lub klauzuli WHERE), które nie są obsługiwane przez indeks. Oto kilka przykładów takich funkcji systemowych: zawiera, wielkie i małe.
  * Jeśli to możliwe, twórz zapytania korzystające z filtru klucza partycji.
  * Aby osiągnąć wykonywanie zapytań, należy unikać wywoływania GÓRNych/NIŻSZYch w filtrze. Zamiast tego należy znormalizować wielkość liter podczas wstawiania. Dla każdej wartości Wstaw wartość z pożądaną wielkością liter lub Wstaw zarówno oryginalną, jak i wartość z pożądaną wielkością liter. 

    Na przykład:
    
    ```sql

    SELECT * FROM c WHERE UPPER(c.name) = "JOE"

    ```
    
    W takim przypadku należy zapisać "Jan" lub zapisać "Jan" jako oryginalną wartość i "Jan". 
    
    Jeśli wielkość liter w kodzie JSON jest znormalizowana, zapytanie zostanie:
    
    ```sql

    SELECT * FROM c WHERE c.name = "JOE"

    ```

    Drugie zapytanie będzie bardziej wydajne, ponieważ nie wymaga wykonywania transformacji dla każdej wartości w celu porównania wartości z "Jan".

Aby uzyskać więcej szczegółów dotyczących funkcji systemowych, zobacz artykuł [funkcje systemowe](sql-query-system-functions.md) .

## <a name="check-indexing-policy"></a>Sprawdzanie zasad indeksowania
Aby sprawdzić, czy bieżące [zasady indeksowania](index-policy.md) są optymalne:

  * Upewnij się, że wszystkie ścieżki JSON używane w zapytaniach są zawarte w zasadach indeksowania dla szybszych operacji odczytu.
  * Wyklucz ścieżki nie są używane w zapytaniach w celu wykonania większej liczby operacji zapisu.

Aby uzyskać więcej informacji [, zobacz jak zarządzać artykułem zasad indeksowania](how-to-manage-indexing-policy.md) .

## <a name="spatial-data-check-ordering-of-points"></a>Dane przestrzenne: Sprawdź kolejność punktów
Punkty, w ramach Wielokąt musi być określona w kolejności przeciwnie do ruchu wskazówek zegara. Wielokąt podane w kolejności do ruchu wskazówek zegara reprezentuje odwrotność region znajdujący się w nim.

## <a name="optimize-join-expressions"></a>Optymalizuj wyrażenia SPRZĘŻENIa
`JOIN`wyrażenia można rozwijać na duże produkty krzyżowe. Gdy jest to możliwe, należy wykonać zapytanie dotyczące mniejszej ilości miejsca do wyszukiwania za pośrednictwem bardziej wąskiego filtru.

Podzapytania z wieloma wartościami mogą optymalizować `JOIN` wyrażenia przez wypychanie predykatów po każdym wyrażeniu SELECT-wielu, a nie po wszystkich sprzężeniach krzyżowych `WHERE` w klauzuli. Aby zapoznać się z szczegółowym przykładem, zobacz [Optymalizacja wyrażenia sprzężenia](https://docs.microsoft.com/azure/cosmos-db/sql-query-subquery#optimize-join-expressions) .

## <a name="optimize-order-by-expressions"></a>Optymalizowanie kolejności według wyrażeń 
`ORDER BY`wydajność zapytań może pogorszyć się, jeśli pola są rozrzedzone lub nie znajdują się w zasadach indeksu.

  * W przypadku pól rozrzedzonych, takich jak czas, Zmniejsz ilość miejsca do wyszukiwania tak jak to możliwe za pomocą filtrów. 
  * Dla jednej właściwości `ORDER BY`Dołącz właściwość w zasadach indeksu. 
  * W przypadku wielu `ORDER BY` wyrażeń właściwości Zdefiniuj [indeks złożony](https://docs.microsoft.com/azure/cosmos-db/index-policy#composite-indexes) dla pól, które są sortowane.  

## <a name="many-large-documents-being-loaded-and-processed"></a>Wiele dużych dokumentów ładowanych i przetwarzanych
Czas i jednostek ru, które są wymagane przez zapytanie, nie są zależne od wielkości odpowiedzi, również są zależne od pracy wykonywanej przez potok przetwarzania zapytań. Czas i jednostek ru zwiększają się proporcjonalnie do ilości pracy wykonanej przez cały potok przetwarzania zapytań. Więcej pracy jest wykonywanych w dużych dokumentach, więc więcej czasu i jednostek ru są wymagane do załadowania i przetwarzania dużych dokumentów.

## <a name="low-provisioned-throughput"></a>Niska zainicjowana przepływność
Upewnij się, że obsługiwana przepływność może obsługiwać obciążenie. Zwiększ budżet RU dla kolekcji, których dotyczy problem.

## <a name="try-upgrading-to-the-latest-sdk-version"></a>Spróbuj uaktualnić do najnowszej wersji zestawu SDK
Aby określić najnowszy zestaw SDK, zobacz artykuł [pobieranie zestawu SDK i informacje o wersji](sql-api-sdk-dotnet.md) .

## <a name="next-steps"></a>Następne kroki
Zapoznaj się z poniższymi dokumentami, jak mierzyć jednostek ru na zapytanie, uzyskać statystyki wykonywania w celu dostrajania zapytań i nie tylko:

* [Pobieranie metryk wykonywania zapytań SQL przy użyciu zestawu .NET SDK](profile-sql-api-query.md)
* [Tuning query performance with Azure Cosmos DB (Dostosowywanie wydajności zapytań w usłudze Azure Cosmos DB)](sql-api-sql-query-metrics.md)
* [Porady dotyczące wydajności dla zestawu .NET SDK](performance-tips.md)
