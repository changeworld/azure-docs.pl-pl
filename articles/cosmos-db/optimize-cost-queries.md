---
title: Optymalizuj koszty i usługi RU/s w celu uruchamiania zapytań w usłudze Azure Cosmos DB
description: Dowiedz się, jak oceniać opłaty jednostkowe żądań dla kwerendy i optymalizować kwerendę pod względem wydajności i kosztów.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: dd75ad4ed1024292868f113e474fe8b8b73679b0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75445129"
---
# <a name="optimize-query-cost-in-azure-cosmos-db"></a>Optymalizacja kosztów zapytania w usłudze Azure Cosmos DB

Usługa Azure Cosmos DB oferuje bogaty zestaw operacji bazy danych, w tym zapytania relacyjne i hierarchiczne, które działają na elementach w kontenerze. Koszt związany z każdą z tych operacji zależy od procesora, danych We/Wy i pamięci wymaganej do wykonania danej operacji. Nie myśl o zasobach sprzętowych i zarządzaniu nimi — zamiast tego pomyśl o jednostce żądania (RU) jako pojedynczej mierze zasobów wymaganych do wykonywania różnych operacji bazy danych i obsługiwania żądań. W tym artykule opisano, jak oszacować opłaty za jednostki żądania dla zapytania i jak zoptymalizować zapytanie pod względem wydajności i kosztów. 

Zapytania w usłudze Azure Cosmos DB są zazwyczaj uporządkowane od najszybszych/najbardziej wydajnych do wolniejszych/mniej wydajnych pod względem przepływności w następujący sposób:  

* GET operacji na jeden klucz partycji i klucz elementu.

* Zapytanie z klauzulą filtru w ramach jednego klucza partycji.

* Kwerenda bez klauzuli filtru równości lub zakresu na dowolnej właściwości.

* Zapytanie bez filtrów.

Kwerendy, które odczytują dane z jednej lub więcej partycji, ponoszą większe opóźnienia i zużywają większą liczbę jednostek żądań. Ponieważ każda partycja ma automatyczne indeksowanie dla wszystkich właściwości, kwerenda może być skutecznie obsługiwana z indeksu. Można wprowadzać kwerendy, które używają wielu partycji szybciej przy użyciu opcji równoległości. Aby dowiedzieć się więcej na temat partycjonowania i kluczy partycji, zobacz [Partycjonowanie w usłudze Azure Cosmos DB](partitioning-overview.md).

## <a name="evaluate-request-unit-charge-for-a-query"></a>Ocena opłaty jednostkowej żądania dla kwerendy

Po zapisaniu niektórych danych w kontenerach usługi Azure Cosmos, można użyć Eksploratora danych w witrynie Azure portal do konstruowania i uruchamiania zapytań. Można również uzyskać koszt zapytań przy użyciu eksploratora danych. Ta metoda daje poczucie rzeczywistych opłat związanych z typowych zapytań i operacji, które obsługuje system.

Można również uzyskać koszt zapytań programowo przy użyciu SDK. Aby zmierzyć obciążenie każdej operacji, takich jak tworzenie, `x-ms-request-charge` aktualizowanie lub usuwanie, sprawdź nagłówek podczas korzystania z interfejsu API REST. Jeśli używasz .NET lub Java SDK, `RequestCharge` właściwość jest równoważną właściwością, aby uzyskać opłatę za żądanie i ta właściwość jest obecna w ResourceResponse lub FeedResponse.

```csharp
// Measure the performance (request units) of writes 
ResourceResponse<Document> response = await client.CreateDocumentAsync(collectionSelfLink, myDocument); 

Console.WriteLine("Insert of an item consumed {0} request units", response.RequestCharge); 

// Measure the performance (request units) of queries 
IDocumentQuery<dynamic> queryable = client.CreateDocumentQuery(collectionSelfLink, queryString).AsDocumentQuery(); 

while (queryable.HasMoreResults) 
     { 
          FeedResponse<dynamic> queryResponse = await queryable.ExecuteNextAsync<dynamic>(); 
          Console.WriteLine("Query batch consumed {0} request units", queryResponse.RequestCharge); 
     }
```

## <a name="factors-influencing-request-unit-charge-for-a-query"></a>Czynniki wpływające na obciążenie jednostkowe żądania zapytania

Jednostki żądań dla kwerend są zależne od wielu czynników. Na przykład liczba załadowanych/zwróconych elementów usługi Azure Cosmos, liczba wyszukiwań względem indeksu, czas kompilacji kwerendy itp. Usługa Azure Cosmos DB gwarantuje, że ta sama kwerenda podczas wykonywania na tych samych danych zawsze będzie zużywać taką samą liczbę jednostek żądań, nawet w przypadku powtórzeń wykonania. Profil kwerendy przy użyciu metryk wykonywania kwerendy daje dobry pomysł, jak jednostki żądania są wydawane.  

W niektórych przypadkach może zostać wyświetlona sekwencja odpowiedzi 200 i 429 i jednostek żądań zmiennych w stronicowanym wykonaniu kwerend, to znaczy, że kwerendy będą uruchamiane tak szybko, jak to możliwe na podstawie dostępnych jednostek RU. Może się pojawić wykonanie kwerendy podzielić się na wiele stron/rund między serwerem a klientem. Na przykład 10 000 elementów może zostać zwróconych jako wiele stron, z których każda jest obciążona na podstawie obliczeń wykonanych dla tej strony. Po zsumować na tych stronach, należy uzyskać taką samą liczbę uli, jak można uzyskać dla całej kwerendy.  

## <a name="metrics-for-troubleshooting"></a>Metryki dotyczące rozwiązywania problemów

Wydajność i przepływność używane przez kwerendy, funkcje zdefiniowane przez użytkownika (UDFs) zależy głównie od treści funkcji. Najprostszym sposobem, aby dowiedzieć się, ile czasu wykonanie kwerendy jest spędzane w UDF i liczba identyfikatorów obiektów innych niż używane, jest włączenie metryk kwerendy. Jeśli używasz .NET SDK, oto przykładowe metryki kwerendy zwracane przez zestaw SDK:

```bash
Retrieved Document Count                 :               1              
Retrieved Document Size                  :           9,963 bytes        
Output Document Count                    :               1              
Output Document Size                     :          10,012 bytes        
Index Utilization                        :          100.00 %            
Total Query Execution Time               :            0.48 milliseconds 
  Query Preparation Times 
    Query Compilation Time               :            0.07 milliseconds 
    Logical Plan Build Time              :            0.03 milliseconds 
    Physical Plan Build Time             :            0.05 milliseconds 
    Query Optimization Time              :            0.00 milliseconds 
  Index Lookup Time                      :            0.06 milliseconds 
  Document Load Time                     :            0.03 milliseconds 
  Runtime Execution Times 
    Query Engine Execution Time          :            0.03 milliseconds 
    System Function Execution Time       :            0.00 milliseconds 
    User-defined Function Execution Time :            0.00 milliseconds 
  Document Write Time                    :            0.00 milliseconds 
  Client Side Metrics 
    Retry Count                          :               1              
    Request Charge                       :            3.19 RUs  
```

## <a name="best-practices-to-cost-optimize-queries"></a>Najważniejsze wskazówki dotyczące optymalizacji kosztów kwerend 

Należy wziąć pod uwagę następujące najlepsze rozwiązania podczas optymalizacji kwerend pod kątem kosztów:

* **Kololokuj wiele typów jednostek**

   Spróbuj współlokować wiele typów jednostek w ramach jednej lub mniejszej liczby kontenerów. Ta metoda daje korzyści nie tylko z punktu widzenia cen, ale także dla wykonywania zapytań i transakcji. Zapytania są ograniczone do jednego kontenera; i niepodzielnych transakcji za pośrednictwem wielu rekordów za pośrednictwem procedur/wyzwalaczy przechowywane są ograniczone do klucza partycji w jednym kontenerze. Kolokowanie jednostek w tym samym kontenerze może zmniejszyć liczbę podróży w obie strony sieci, aby rozwiązać relacje między rekordami. Dlatego zwiększa wydajność end-to-end, umożliwia transakcje niepodzielne za pośrednictwem wielu rekordów dla większego zestawu danych, a w rezultacie obniża koszty. Jeśli kolocacja wielu typów jednostek w ramach jednej lub mniejszej liczby kontenerów jest trudna dla twojego scenariusza, zwykle dlatego, że migrujesz istniejącą aplikację i nie chcesz wprowadzać żadnych zmian w kodzie — należy rozważyć inicjowanie obsługi administracyjnej przepływności na poziomie bazy danych.  

* **Mierzenie i dostrajanie dla niższych jednostek żądań/drugie użycie**

   Złożoność kwerendy wpływa na liczbę jednostek żądań (RUs) są używane dla operacji. Liczba predykatów, charakter predykatów, liczba plików UDF i rozmiar zestawu danych źródłowych. Wszystkie te czynniki wpływają na koszt operacji kwerendy. 

   Opłata za żądanie zwrócona w nagłówku żądania wskazuje koszt danej kwerendy. Na przykład jeśli kwerenda zwraca 1000 elementów 1 KB, koszt operacji wynosi 1000. W związku z tym w ciągu jednej sekundy serwer honoruje tylko dwa takie żądania przed ograniczeniem szybkości kolejnych żądań. Aby uzyskać więcej informacji, zobacz artykuł [jednostek żądań](request-units.md) i kalkulator jednostki żądania. 

## <a name="next-steps"></a>Następne kroki

Następnie możesz dowiedzieć się więcej o optymalizacji kosztów w usłudze Azure Cosmos DB, aby uzyskać następujące artykuły:

* Dowiedz się więcej o [tym, jak działa cennik usługi Azure Cosmos](how-pricing-works.md)
* Dowiedz się więcej o [optymalizacji pod kątem rozwoju i testowania](optimize-dev-test.md)
* Dowiedz się więcej o [opisie rachunku za usługę Azure Cosmos DB](understand-your-bill.md)
* Dowiedz się więcej o [optymalizacji kosztów przepływności](optimize-cost-throughput.md)
* Dowiedz się więcej o [optymalizacji kosztów magazynowania](optimize-cost-storage.md)
* Dowiedz się więcej o [optymalizacji kosztów odczytów i zapisów](optimize-cost-reads-writes.md)
* Dowiedz się więcej o [optymalizowaniu kosztów wieloregionowych kont usługi Azure Cosmos](optimize-cost-regions.md)
* Dowiedz się więcej o [pojemności zarezerwowanej usługi Azure Cosmos DB](cosmos-db-reserved-capacity.md)

