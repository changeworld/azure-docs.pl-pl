---
title: Optymalizacja jednostek żądania i kosztów, aby uruchamiać zapytania w usłudze Azure Cosmos DB
description: Dowiedz się, jak ocenić opłat za jednostki żądań dla zapytania i zoptymalizować zapytania pod względem wydajności i kosztów.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: rimman
ms.openlocfilehash: 2d1ac054abf4bb8228bdb5cc20d79cb751af7a33
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65967443"
---
# <a name="optimize-query-cost-in-azure-cosmos-db"></a>Optymalizuj koszt zapytania w usłudze Azure Cosmos DB

Usługa Azure Cosmos DB oferuje bogaty zestaw operacji bazy danych, takich jak relacyjne i hierarchiczne zapytania, które działają na elementy w kontenerze. Koszt związany z każdą z tych operacji zależy od procesora CPU, we/wy i pamięci wymaganej do ukończenia tej operacji. Zamiast myśleć o zasobach i zarządzaniu nimi sprzętu można traktować jednostek żądań (RU) jako pojedynczej mierze zasobów wymaganych do wykonywania różnych operacji bazy danych ma obsługiwać żądania. W tym artykule opisano, jak do opłat za jednostki żądań dla zapytania, oceny i optymalizowania zapytań pod względem wydajności i kosztów. 

Zapytania w usłudze Azure Cosmos DB są zazwyczaj uporządkowane od najszybszego/nabardziej efektywnego do wolniejszego/mniej efektywnego pod względem przepustowości w następujący sposób:  

* Operacja pobrania pojedynczego klucza partycji i klucz elementu.

* Zapytanie z klauzulą filtru w ramach pojedynczego klucza partycji.

* Zapytania, bez klauzuli filtru równości lub zakres dowolne właściwości.

* Zapytania, bez filtrów.

Zapytania, które odczytują dane z co najmniej jedną partycję pociągnąć za sobą wyższe opóźnienie i korzystać z większej liczby jednostek żądania. Ponieważ każda partycja zawiera, automatyczne indeksowanie wszystkich właściwości, zapytanie może ich obsługa jest wydajna z indeksu. Istnieje możliwość zapytań, które używają wielu partycjach szybciej przy użyciu opcji równoległości. Aby dowiedzieć się więcej na temat partycjonowania i klucze partycji, zobacz [partycjonowanie w usłudze Azure Cosmos DB](partitioning-overview.md).

## <a name="evaluate-request-unit-charge-for-a-query"></a>Ocena dla zapytania, opłata za jednostkę żądania

Gdy dane są przechowywane w kontenerach usługi Azure Cosmos, można użyć Eksploratora danych w witrynie Azure portal do tworzenia i uruchamiania zapytań. Koszt zapytania można także uzyskać za pomocą Eksploratora danych. Ta metoda zapewni Ci poczucie rzeczywiste opłaty związane z typowych zapytań i operacji, które system obsługuje.

Koszt zapytania można także uzyskać programistycznie przy użyciu zestawów SDK. Do mierzenia obciążenie związane z każdej operacji, takich jak tworzenie, aktualizowanie lub sprawdzanie usunięcia `x-ms-request-charge` nagłówek, korzystając z interfejsu API REST. Jeśli używasz platformy .NET lub zestawu Java SDK `RequestCharge` właściwość jest równoważna właściwości do pobrania opłata za żądanie wyrażana i ta właściwość jest obecny w ramach ResourceResponse lub FeedResponse.

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

## <a name="factors-influencing-request-unit-charge-for-a-query"></a>Czynniki wpływające na opłat za jednostkę żądania dla zapytania

Jednostki żądań zapytań są zależne od szeregu czynników. Na przykład liczba elementów w usłudze Azure Cosmos załadowany/zwrócone, Liczba wyszukiwań względem indeksu, kompilowanie zapytania czasu itp. szczegółowe informacje. Usługa Azure Cosmos DB gwarantuje tego samego zapytania podczas wykonywania na tych samych danych zużyje zawsze taką samą liczbę jednostek żądania, nawet w przypadku wykonania Powtórz tę procedurę. Profilu zapytania przy użyciu metryk wykonywania zapytania daje on dobry pogląd zasad jak upłynęły jednostek żądania.  

W niektórych przypadkach może zostać wyświetlony ciąg 200 i 429 odpowiedzi oraz jednostki zmiennych wniosek stronicowane wykonywania zapytań, określonego, ponieważ kwerendy będą uruchamiane tak szybko, jak to możliwe, w oparciu o dostępne (RUS). Wykonywanie zapytania, Podziel na wielu stronach/rund między serwerem a klientem może zostać wyświetlony. Na przykład 10 000 elementów mogą być zwracane jako wiele stron, każdy obciążany obliczenia wykonywane dla tej strony. Jeśli suma, na tych stronach, powinna pojawić się taką samą liczbę jednostek zarezerwowanych podczas całego zapytania.  

## <a name="metrics-for-troubleshooting"></a>Metryki dotyczące rozwiązywania problemów

Wydajność i przepływność, głównie używane w zapytaniach, funkcje zdefiniowane przez użytkownika (UDF) jest zależna od treści funkcji. Najprostszym sposobem, aby dowiedzieć się, ile czasu wykonywania zapytania odbywa się w funkcji zdefiniowanej przez użytkownika i liczba RU, jest przez włączenie metryk zapytania. Jeśli używasz zestawu SDK platformy .NET, poniżej przedstawiono przykładowe zapytanie metryki zwrócony przez zestaw SDK:

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

## <a name="best-practices-to-cost-optimize-queries"></a>Najlepsze rozwiązania w celu koszt Optymalizowanie zapytań 

Podczas optymalizacji zapytań dotyczących kosztów, należy wziąć pod uwagę następujące najlepsze rozwiązania:

* **Kolokacja wielu typów jednostek**

   Spróbuj przekazać wiele typów jednostki w pojedynczym lub mniejszą liczbę kontenerów. Ta metoda zapewnia korzyści, nie tylko z punktu widzenia cen, ale także do wykonywania zapytań i transakcji. Zapytania są ograniczone do jednego kontenera; i transakcje niepodzielne za pośrednictwem wielu rekordów przy użyciu procedur składowanych/wyzwalacze są ograniczone do klucza partycji w ramach jednego kontenera. Kolokowanie jednostek w tym samym kontenerze pozwala zmniejszyć liczbę sieci dwustronne do interpretowania relacji między rekordami. Więc go zwiększa wydajność end-to-end, umożliwia transakcje niepodzielne za pośrednictwem wielu rekordów większy zestaw danych, a w rezultacie obniża koszty. Jeśli kolokowanie wielu typów jednostek w ciągu jednego lub mniejszej liczby kontenerów jest trudne dla danego scenariusza to, że jest przeprowadzana migracja istniejącej aplikacji i nie chcesz wprowadzać żadnych zmian kodu — należy następnie rozważyć inicjowania obsługi administracyjnej Przepływność na poziomie bazy danych.  

* **Pomiar i dostrajania dla żądania niższe jednostek na sekundę użycia**

   Złożoność zapytania ma wpływ na liczbę jednostek żądań (ru) są używane dla operacji. Liczba predykatów, rodzaj predykaty, liczba funkcji zdefiniowanych przez użytkownika oraz rozmiar zestawu danych źródłowych. Wszystkie te czynniki mają wpływ na koszt operacje zapytań. 

   Opłata za żądanie wyrażana zwrócony w nagłówku żądania wskazuje koszt określonego zapytania. Na przykład jeśli zapytanie zwraca elementy 1 KB 1000, koszty działania to 1000. Jako takie w ciągu sekundy, serwer honoruje tylko dwa takich żądań przed kolejnymi żądaniami ograniczania szybkości. Aby uzyskać więcej informacji, zobacz [jednostek żądania](request-units.md) artykułu i Kalkulator jednostki żądania. 

## <a name="next-steps"></a>Kolejne kroki

Następnie możesz przejść do Dowiedz się więcej o optymalizacji kosztów w usłudze Azure Cosmos DB z następujących artykułów:

* Dowiedz się więcej o [jak Cosmos Azure działa cennik](how-pricing-works.md)
* Dowiedz się więcej o [Optymalizacja na potrzeby programowania i testowania](optimize-dev-test.md)
* Dowiedz się więcej o [informacje o rachunku Azure Cosmos DB](understand-your-bill.md)
* Dowiedz się więcej o [optymalizacji kosztów przepustowości](optimize-cost-throughput.md)
* Dowiedz się więcej o [optymalizacji kosztów magazynowania](optimize-cost-storage.md)
* Dowiedz się więcej o [optymalizacji koszt odczyty i zapisy](optimize-cost-reads-writes.md)
* Dowiedz się więcej o [optymalizacji kosztów kont usługi Azure Cosmos w wielu regionach](optimize-cost-regions.md)
* Dowiedz się więcej o [wydajności rezerwowej usługi Azure Cosmos DB](cosmos-db-reserved-capacity.md)

