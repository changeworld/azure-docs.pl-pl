---
title: Diagnozowanie i rozwiązywanie problemów zapytania, korzystając z usługi Azure Cosmos DB
description: Dowiedz się, jak identyfikować, diagnozowanie i rozwiązywanie problemów z zapytania SQL usługi Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: troubleshooting
ms.date: 07/10/2019
ms.author: girobins
ms.subservice: cosmosdb-sql
ms.reviewer: sngun
ms.openlocfilehash: 079e8677febfe6683d4f0e60a0e7ba6b06ea549d
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2019
ms.locfileid: "67835835"
---
# <a name="troubleshoot-query-performance-for-azure-cosmos-db"></a>Rozwiązywanie problemów z wydajnością zapytań dla usługi Azure Cosmos DB
W tym artykule opisano sposób identyfikowania, diagnozowanie i rozwiązywanie problemów z zapytania SQL usługi Azure Cosmos DB. W celu uzyskania optymalnej wydajności zapytań usługi Azure Cosmos DB, wykonaj poniższe kroki rozwiązywania problemów. 

## <a name="collocate-clients-in-same-azure-region"></a>W ten sposób rozmieszczać klientów, w tym samym regionie platformy Azure 
Najniższe możliwe opóźnienie jest osiągane poprzez zapewnienie, że aplikacja wywołująca znajduje się w obrębie tego samego regionu platformy Azure aprowizowane punktu końcowego usługi Azure Cosmos DB. Aby uzyskać listę dostępnych regionów, zobacz [regionów platformy Azure](https://azure.microsoft.com/global-infrastructure/regions/#services) artykułu.

## <a name="check-consistency-level"></a>Sprawdź poziom spójności
[Poziom spójności](consistency-levels.md) może mieć wpływ na wydajność i koszty. Upewnij się, że Twój poziom spójności jest odpowiednia dla danego scenariusza. Aby uzyskać więcej informacji, zobacz [Wybieranie poziomu spójności](consistency-levels-choosing.md).

## <a name="log-query-metrics"></a>Rejestruj metryki zapytania
Użyj `QueryMetrics` rozwiązywać wolno lub kosztowne zapytania. 

  * Ustaw `FeedOptions.PopulateQueryMetrics = true` mieć `QueryMetrics` w odpowiedzi.
  * `QueryMetrics` Klasa ma przeciążony `.ToString()` funkcja, która może być wywołana w celu pobrania reprezentację ciągu `QueryMetrics`. 
  * Metryki może być wykorzystywany do uzyskania poniższe szczegółowe informacje, między innymi: 
  
      * Czy dowolnego określonego składnika w potoku zapytania trwało nienaturalnie długo (w kolejności od setek milisekund lub więcej). 

          * Przyjrzyj się `TotalExecutionTime`.
          * Jeśli `TotalExecutionTime` zapytania jest mniejsza niż czas wykonywania typu end to end, a następnie jest zużywany czas w po stronie klienta lub sieci. Dokładnie sprawdź zlokalizowana klienta i regionu platformy Azure.
      
      * Czy wystąpiły fałszywych alarmów w dokumentach analizowane (jeśli jest to liczba dokumentów z danych wyjściowych jest znacznie mniejsza niż pobierana dokumencie liczba).  

          * Przyjrzyj się `Index Utilization`.
          * `Index Utilization` = (Liczba zwróconych dokumentów / liczba załadowanych dokumentów)
          * Jeśli liczba zwróconych dokumentów jest znacznie mniejsza niż liczba załadowane, następnie wyniki fałszywie dodatnie są analizowane.
          * Ogranicz liczbę dokumentów pobieranych przy użyciu im bardziej doprecyzowane filtry.  

      * Jak poszczególne rund fared (zobacz `Partition Execution Timeline` z ciągiem reprezentującym `QueryMetrics`). 
      * Czy zapytanie używane opłaty wysokiej żądania. 

Aby uzyskać więcej informacji, zobacz [jak uzyskać metryki wykonywania zapytania SQL](profile-sql-api-query.md) artykułu.
      
## <a name="tune-query-feed-options-parameters"></a>Dostosowywanie parametrów kanału informacyjnego opcje kwerendy 
Wydajność zapytań może być dostosowane za pośrednictwem żądania [Opcje źródła danych](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.feedoptions?view=azure-dotnet) parametrów. Spróbuj ustawienie poniższe opcje:

  * Ustaw `MaxDegreeOfParallelism` do-1 najpierw, a następnie porównania wydajności między różne wartości. 
  * Ustaw `MaxBufferedItemCount` do-1 najpierw, a następnie porównania wydajności między różne wartości. 
  * Ustaw `MaxItemCount` na -1.

Podczas porównywania wydajności różnych wartości, spróbuj wartości, takie jak 2, 4, 8, 16 i inne.
 
## <a name="read-all-results-from-continuations"></a>Odczytaj wszystkie wyniki z kontynuacji
Jeśli uważasz, że nie otrzymujesz wszystkie wyniki, upewnij się, w pełni opróżnić kontynuacji. Innymi słowy przechowywać, odczytywanie wyników, gdy token kontynuacji ma więcej dokumentów umożliwiające uzyskanie.

W pełni opróżniania można osiągnąć za pomocą jednej z następujących wzorców:

  * Kontynuuj wyniki przetwarzania podczas kontynuacji nie jest pusty.
  * Kontynuuj, przetwarzanie, gdy zapytanie ma więcej wyników. 

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

## <a name="choose-system-functions-that-utilize-index"></a>Wybierz funkcje systemu, które wykorzystują indeksu
Jeśli wyrażenie mogą być tłumaczone w zakresie wartości ciągu, następnie go mogą korzystać z indeksu; w przeciwnym razie nie. 

Poniżej przedstawiono listę funkcji ciągów, które mogą korzystać z indeksu: 
    
  * STARTSWITH (str_expr, str_expr) 
  * Po lewej stronie (str_expr, num_expr) = str_expr 
  * SUBSTRING (str_expr, num_expr, num_expr) = str_expr, ale tylko wtedy, jeśli pierwszy num_expr wynosi 0 
    
    Poniżej przedstawiono kilka przykładów kwerendy: 
    
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

  * Należy unikać funkcji systemu w filtrze (lub klauzuli WHERE), nie są obsługiwane przez indeks. Przykłady takich funkcji systemu to zawiera, górna, dolna.
  * Jeśli to możliwe, twórz zapytania korzystające z filtru klucza partycji.
  * Aby osiągnąć niezawodnie zapytania należy unikać wywoływania wielkich/małych w filtrze. Zamiast tego należy znormalizować wielkość liter wartości podczas wstawiania. Dla każdej wartości Wstaw wartość z żądanego wielkością liter lub wstawić oryginalnej wartości i wartości z odpowiednią wielkość liter w wyrazie. 

    Na przykład:
    
    ```sql

    SELECT * FROM c WHERE UPPER(c.name) = "JOE"

    ```
    
    Dla tego przypadku store "Jan" napisane wielkimi literami ani nie przechowują oryginalną wartość "Jan" i "Jan". 
    
    Jeśli wielkość liter w wyrazie danych JSON jest znormalizować zapytanie staje się:
    
    ```sql

    SELECT * FROM c WHERE c.name = "JOE"

    ```

    Drugie zapytanie będzie wydajniej, ponieważ nie jest wymagane wykonywanie przekształceń na poszczególne wartości, aby można było porównać wartości, które mają "Jan".

Dla funkcji systemowej więcej szczegółów, zobacz [funkcji systemu](sql-query-system-functions.md) artykułu.

## <a name="check-indexing-policy"></a>Sprawdzanie zasad indeksowania
Aby sprawdzić, czy bieżący [zasady indeksowania](index-policy.md) jest optymalna:

  * Upewnij się, że wszystkie ścieżki JSON używanych w zapytaniach są objęte zasad indeksowania dla odczytów szybciej.
  * Wyklucz ścieżki, które nie są używane w zapytaniach, więcej zapisywanie wydajne.

Aby uzyskać więcej informacji, zobacz [jak można zarządzać zasady indeksowania](how-to-manage-indexing-policy.md) artykułu.

## <a name="spatial-data-check-ordering-of-points"></a>Dane przestrzenne: Sprawdź kolejność punktów
Punkty, w ramach Wielokąt musi być określona w kolejności przeciwnie do ruchu wskazówek zegara. Wielokąt podane w kolejności do ruchu wskazówek zegara reprezentuje odwrotność region znajdujący się w nim.

## <a name="optimize-join-expressions"></a>Optymalizacja sprzężenia wyrażeń
`JOIN` wyrażenia można rozszerzyć do dużych dla wielu produktów. Gdy to możliwe, zapytanie wyszukiwania mniejsze miejsca na za pomocą filtru więcej wąskie.

Podzapytania wielowartościowych można zoptymalizować `JOIN` wyrażeń, wypychając predykatów po każdym wyrażeniu Wybierz wiele, a nie po wszystkich sprzężenia krzyżowe w `WHERE` klauzuli. Aby uzyskać szczegółowy przykład zobacz [zoptymalizować wyrażeń Dołącz](https://docs.microsoft.com/azure/cosmos-db/sql-query-subquery#optimize-join-expressions) artykułu.

## <a name="optimize-order-by-expressions"></a>Optymalizowanie wyrażeń w klauzuli ORDER BY 
`ORDER BY` Zapytanie może to spowodować obniżenie wydajności w przypadku pól rozrzedzony lub nie jest zawarty w zasadach indeksu.

  * Rozrzedzony pól, np. czas zmniejszenie obszaru search możliwie za pomocą filtrów. 
  * Dla jednej właściwości `ORDER BY`, zawierają właściwość w indeks zasadach. 
  * Wiele właściwości `ORDER BY` wyrażeń, zdefiniuj [indeksu złożonego](https://docs.microsoft.com/azure/cosmos-db/index-policy#composite-indexes) w polach posortowana.  

## <a name="many-large-documents-being-loaded-and-processed"></a>Wiele dużych dokumentów są załadowane i przetworzone
Czas i (RUS), które są wymagane przez zapytanie nie są tylko zależy od rozmiaru odpowiedzi, również są zależne od pracy, która jest wykonywane przez potok przetwarzania zapytań. Czas i RUs proporcjonalnie zwiększyć ilość pracy wykonanej przez potok przetwarzania całe zapytanie. Więcej pracy jest wykonywana w dużych dokumentach, dlatego więcej czasu i (RUS) są wymagane do ładować i przetwarzać dużych dokumentów.

## <a name="low-provisioned-throughput"></a>Niski aprowizowanej przepływności.
Upewnij się, że aprowizowana przepływność może obsłużyć obciążenie. Zwiększenie jednostek ru na budżetu, dla których to dotyczy kolekcji.

## <a name="try-upgrading-to-the-latest-sdk-version"></a>Spróbuj go uaktualnić do najnowszej wersji zestawu SDK
Aby określić najnowszą Zobacz SDK [pobierania zestawu SDK i wersji uwagi](sql-api-sdk-dotnet.md) artykułu.

## <a name="next-steps"></a>Następne kroki
Zapoznaj się z dokumentów poniżej na temat mierzenie jednostek żądań na zapytanie, uzyskiwanie statystyk wykonywania można dostrajanie zapytań i nie tylko:

* [Pobierz metryki wykonywania zapytania SQL przy użyciu zestawu .NET SDK](profile-sql-api-query.md)
* [Tuning query performance with Azure Cosmos DB (Dostosowywanie wydajności zapytań w usłudze Azure Cosmos DB)](sql-api-sql-query-metrics.md)
* [Porady dotyczące wydajności dla zestawu .NET SDK](performance-tips.md)