---
title: Rozwiązywanie problemów z kwerendą podczas korzystania z Azure Cosmos DB
description: Dowiedz się, jak identyfikować, diagnozować i rozwiązywać problemy z Azure Cosmos DB zapytań SQL.
author: timsander1
ms.service: cosmos-db
ms.topic: troubleshooting
ms.date: 02/10/2020
ms.author: tisande
ms.subservice: cosmosdb-sql
ms.reviewer: sngun
ms.openlocfilehash: 0dd3cb12c52e23a0a8acd57bf401ba68acfb9925
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/26/2020
ms.locfileid: "77623689"
---
# <a name="troubleshoot-query-issues-when-using-azure-cosmos-db"></a>Rozwiązywanie problemów z kwerendą podczas korzystania z Azure Cosmos DB

W tym artykule przedstawiono ogólne zalecane podejście do rozwiązywania problemów z zapytaniami w Azure Cosmos DB. Mimo że kroki opisane w tym dokumencie nie należy traktować jako "catch all" w przypadku potencjalnych problemów z zapytaniami, w tym miejscu zostały uwzględnione najbardziej typowe porady dotyczące wydajności. Należy używać tego dokumentu jako początku do rozwiązywania problemów z niską lub kosztowną kwerendą w interfejsie API Azure Cosmos DB Core (SQL). [Dzienników diagnostycznych](cosmosdb-monitor-resource-logs.md) można także użyć do identyfikowania zapytań, które są wolne lub zużywają znaczną przepływność.

Optymalizacje zapytań można w szerokim zakresie klasyfikować w Azure Cosmos DB: optymalizacje, które zmniejszają opłaty za zapytania i optymalizacje jednostki żądania (RU), które po prostu skracają opóźnienia. Zmniejszając opłatę RU dla kwerendy, prawie zmniejsza się również opóźnienia.

W tym dokumencie przedstawiono przykłady, które można odtworzyć przy użyciu zestawu danych [odżywiania](https://github.com/CosmosDB/labs/blob/master/dotnet/setup/NutritionData.json) .

## <a name="important"></a>Ważne

- Aby uzyskać najlepszą wydajność, postępuj zgodnie ze [wskazówkami dotyczącymi wydajności](performance-tips.md).
    > [!NOTE] 
    > System Windows 64 — przetwarzanie hosta bitowego jest zalecane w celu zwiększenia wydajności. Zestaw SDK SQL zawiera natywną bibliotekę serviceinterop. dll umożliwiającą analizowanie i optymalizowanie zapytań lokalnie i jest obsługiwana tylko na platformie Windows x64. W przypadku systemu Linux i innych nieobsługiwanych platform, na których nie jest dostępny element serviceinterop. dll, spowoduje to dodatkowe wywołanie sieciowe do bramy w celu uzyskania zoptymalizowanego zapytania. 
- Zapytanie Cosmos DB nie obsługuje minimalnej liczby elementów.
    - Kod powinien obsługiwać wszystkie rozmiary stron od 0 do maksymalnej liczby elementów
    - Liczba elementów na stronie może i zmieni się bez żadnych powiadomień.
- W przypadku zapytań oczekiwano pustych stron i mogą one występować w dowolnym momencie. 
    - Powodem, że puste strony są uwidocznione w zestawach SDK, umożliwiają anulowanie zapytania. Sprawia również, że zestaw SDK wykonuje wiele wywołań sieciowych.
    - Puste strony mogą być wyświetlane w istniejących obciążeniach, ponieważ partycja fizyczna jest podzielona na Cosmos DB. Pierwsza partycja ma teraz 0 wyników, co powoduje wyświetlenie pustej strony.
    - Puste strony są spowodowane przez zaplecze zastępujące zapytanie, ponieważ zapytanie trwa dłużej niż określony czas w zapleczu do pobrania dokumentów. Jeśli Cosmos DB zastąpi zapytanie, spowoduje to zwrócenie tokenu kontynuacji, co pozwoli na kontynuowanie zapytania. 
- Upewnij się, że wszystkie zapytania są całkowicie opróżniane. Zapoznaj się z przykładami zestawu SDK i użyj pętli while w `FeedIterator.HasMoreResults`, aby opróżnić całe zapytanie.

### <a name="obtaining-query-metrics"></a>Uzyskiwanie metryk zapytania:

Podczas optymalizowania zapytania w Azure Cosmos DB, pierwszym krokiem jest zawsze [uzyskanie metryk zapytania](profile-sql-api-query.md) dla zapytania. Są one również dostępne za pomocą Azure Portal, jak pokazano poniżej:

[![uzyskać metryki zapytań](./media/troubleshoot-query-performance/obtain-query-metrics.png)](./media/troubleshoot-query-performance/obtain-query-metrics.png#lightbox)

Po uzyskaniu metryk zapytania Porównaj liczbę pobranych dokumentów z liczbą dokumentów wyjściowych dla zapytania. Użyj tego porównania, aby zidentyfikować odpowiednie sekcje poniżej.

Liczba pobranych dokumentów to liczba dokumentów wymaganych do załadowania zapytania. Liczba dokumentów wyjściowych to liczba dokumentów, które były zbędne dla wyników zapytania. Jeśli liczba pobieranych dokumentów jest znacznie wyższa niż liczba dokumentów wyjściowych, wówczas była co najmniej jedna część zapytania, która nie mogła użyć indeksu i jest wymagana do skanowania.

Możesz odwoływać się do poniższej sekcji, aby poznać odpowiednie optymalizacje zapytań dla danego scenariusza:

### <a name="querys-ru-charge-is-too-high"></a>Zbyt wysoka opłata za zapytanie RU

#### <a name="retrieved-document-count-is-significantly-greater-than-output-document-count"></a>Liczba pobranych dokumentów jest znacznie większa niż liczba dokumentów wyjściowych

- [Dołącz wymagane ścieżki do zasad indeksowania](#include-necessary-paths-in-the-indexing-policy)

- [Informacje o tym, które funkcje systemowe wykorzystują indeks](#understand-which-system-functions-utilize-the-index)

- [Zapytania z klauzulą Filter i ORDER BY](#queries-with-both-a-filter-and-an-order-by-clause)

- [Optymalizowanie wyrażeń SPRZĘŻENIa przy użyciu podzapytania](#optimize-join-expressions-by-using-a-subquery)

<br>

#### <a name="retrieved-document-count-is-approximately-equal-to-output-document-count"></a>Liczba pobranych dokumentów jest w przybliżeniu równa liczbie dokumentów wyjściowych

- [Unikaj zapytań między partycjami](#avoid-cross-partition-queries)

- [Filtry dla wielu właściwości](#filters-on-multiple-properties)

- [Zapytania z klauzulą Filter i ORDER BY](#queries-with-both-a-filter-and-an-order-by-clause)

<br>

### <a name="querys-ru-charge-is-acceptable-but-latency-is-still-too-high"></a>Opłata za usługę RU dla zapytania jest akceptowalna, ale opóźnienie jest wciąż zbyt wysokie

- [Zwiększ bliskość](#improve-proximity)

- [Zwiększ przepływność aprowizacji](#increase-provisioned-throughput)

- [Zwiększ MaxConcurrency](#increase-maxconcurrency)

- [Zwiększ MaxBufferedItemCount](#increase-maxbuffereditemcount)

## <a name="queries-where-retrieved-document-count-exceeds-output-document-count"></a>Zapytania, w przypadku których liczba pobranych dokumentów przekracza liczbę dokumentów wyjściowych

 Liczba pobranych dokumentów to liczba dokumentów wymaganych do załadowania zapytania. Liczba dokumentów wyjściowych to liczba dokumentów, które były zbędne dla wyników zapytania. Jeśli liczba pobieranych dokumentów jest znacznie wyższa niż liczba dokumentów wyjściowych, wówczas była co najmniej jedna część zapytania, która nie mogła użyć indeksu i jest wymagana do skanowania.

 Poniżej znajduje się przykładowe zapytanie skanowania, które nie było całkowicie obsługiwane przez indeks.

Kwerenda:

 ```sql
SELECT VALUE c.description
FROM c
WHERE UPPER(c.description) = "BABYFOOD, DESSERT, FRUIT DESSERT, WITHOUT ASCORBIC ACID, JUNIOR"
 ```

Metryki zapytań:

```
Retrieved Document Count                 :          60,951
Retrieved Document Size                  :     399,998,938 bytes
Output Document Count                    :               7
Output Document Size                     :             510 bytes
Index Utilization                        :            0.00 %
Total Query Execution Time               :        4,500.34 milliseconds
  Query Preparation Times
    Query Compilation Time               :            0.09 milliseconds
    Logical Plan Build Time              :            0.05 milliseconds
    Physical Plan Build Time             :            0.04 milliseconds
    Query Optimization Time              :            0.01 milliseconds
  Index Lookup Time                      :            0.01 milliseconds
  Document Load Time                     :        4,177.66 milliseconds
  Runtime Execution Times
    Query Engine Times                   :          322.16 milliseconds
    System Function Execution Time       :           85.74 milliseconds
    User-defined Function Execution Time :            0.00 milliseconds
  Document Write Time                    :            0.01 milliseconds
Client Side Metrics
  Retry Count                            :               0
  Request Charge                         :        4,059.95 RUs
```

Liczba pobranych dokumentów (60 951) jest znacznie większa niż liczba dokumentów wyjściowych (7), więc to zapytanie jest konieczne do przeprowadzenia skanowania. W takim przypadku Funkcja systemowa [Upper ()](sql-query-upper.md) nie korzysta z indeksu.

## <a name="include-necessary-paths-in-the-indexing-policy"></a>Dołącz wymagane ścieżki do zasad indeksowania

Zasady indeksowania powinny obejmować wszystkie właściwości zawarte w `WHERE` klauzule, klauzule `ORDER BY`, `JOIN`i większość funkcji systemowych. Ścieżka określona w zasadach indeksu powinna być taka sama jak właściwość w dokumentach JSON (z uwzględnieniem wielkości liter).

Jeśli uruchamiamy proste zapytanie w zestawie danych [odżywiania](https://github.com/CosmosDB/labs/blob/master/dotnet/setup/NutritionData.json) , obserwujemy znacznie mniejszą opłatą za ru, gdy właściwość w klauzuli `WHERE` jest indeksowana.

### <a name="original"></a>Oryginał

Kwerenda:

```sql
SELECT * FROM c WHERE c.description = "Malabar spinach, cooked"
```

Zasady indeksowania:

```json
{
    "indexingMode": "consistent",
    "automatic": true,
    "includedPaths": [
        {
            "path": "/*"
        }
    ],
    "excludedPaths": [
        {
            "path": "/description/*"
        }
    ]
}
```

**Opłata za ru:** 409,51 jednostek ru

### <a name="optimized"></a>Optymalizacja

Zaktualizowane zasady indeksowania:

```json
{
    "indexingMode": "consistent",
    "automatic": true,
    "includedPaths": [
        {
            "path": "/*"
        }
    ],
    "excludedPaths": []
}
```

**Opłata za ru:** 2,98 jednostek ru

W dowolnej chwili można dodać do zasad indeksowania dodatkowe właściwości bez wpływu na dostępność lub wydajność. Jeśli dodasz nową właściwość do indeksu, zapytania, które używają tej właściwości, będą natychmiast używały nowego dostępnego indeksu. Zapytanie będzie używać nowego indeksu podczas kompilowania. W efekcie wyniki zapytania mogą być niespójne, ponieważ trwa ponowne kompilowanie indeksu. Jeśli nowa właściwość jest indeksowana, zapytania, które używają tylko istniejących indeksów, nie będą miały na nie oddziaływać podczas ponownego kompilowania indeksu. [Postęp przekształcania indeksów można śledzić](https://docs.microsoft.com/azure/cosmos-db/how-to-manage-indexing-policy#use-the-net-sdk-v3).

## <a name="understand-which-system-functions-utilize-the-index"></a>Informacje o tym, które funkcje systemowe wykorzystują indeks

Jeśli wyrażenie można przetłumaczyć na zakres wartości ciągu, może ono korzystać z indeksu. W przeciwnym razie nie może.

Poniżej znajduje się lista funkcji ciągów, które mogą korzystać z indeksu:

- STARTSWITH(wyrażenie_ciągu, wyrażenie_ciągu)
- LEFT(wyrażenie_ciągu, wyrażenie_liczbowe) = wyrażenie_ciągu
- SUBSTRING(wyrażenie_ciągu, wyrażenie_liczbowe, wyrażenie_liczbowe) = wyrażenie_ciągu, ale tylko w przypadku, gdy pierwsze wyrażenie_liczbowe ma wartość 0

Niektóre typowe funkcje systemowe, które nie używają indeksu i muszą ładować każdy dokument poniżej:

| **Funkcja systemowa**                     | **Pomysły dotyczące optymalizacji**             |
| --------------------------------------- |------------------------------------------------------------ |
| CONTAINS                                | Użyj Azure Search do wyszukiwania pełnotekstowego                        |
| GÓRNY/DOLNY                             | Zamiast używać funkcji system do normalizacji danych za każdym razem w przypadku porównań, zamiast tego należy znormalizować wielkość liter po wstawieniu. Następnie zapytanie takie jak ```SELECT * FROM c WHERE UPPER(c.name) = 'BOB'``` po prostu zostanie ```SELECT * FROM c WHERE c.name = 'BOB'``` |
| Funkcje matematyczne (inne niż zagregowane) | Jeśli potrzebujesz często obliczać wartość w zapytaniu, Rozważ przechowywanie tej wartości jako właściwości w dokumencie JSON. |

------

Inne części zapytania nadal mogą używać indeksu pomimo funkcji systemowych, które nie używają indeksu.

## <a name="queries-with-both-a-filter-and-an-order-by-clause"></a>Zapytania z klauzulą Filter i ORDER BY

Zapytania z filtrem i klauzulą `ORDER BY` zwykle wykorzystują indeks zakresu, ale będą bardziej wydajne, jeśli będą mogły być obsługiwane z indeksu złożonego. Oprócz modyfikowania zasad indeksowania należy dodać wszystkie właściwości w indeksie złożonym do klauzuli `ORDER BY`. Ta modyfikacja zapytania zapewni użycie indeksu złożonego.  Można obserwować wpływ przez uruchomienie zapytania na zestawie danych [odżywiania](https://github.com/CosmosDB/labs/blob/master/dotnet/setup/NutritionData.json) .

### <a name="original"></a>Oryginał

Kwerenda:

```sql
SELECT * FROM c WHERE c.foodGroup = "Soups, Sauces, and Gravies" ORDER BY c._ts ASC
```

Zasady indeksowania:

```json
{

        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[]
}
```

**Opłata za ru:** 44,28 jednostek ru

### <a name="optimized"></a>Optymalizacja

Zaktualizowane zapytanie (zawiera obie właściwości w klauzuli `ORDER BY`):

```sql
SELECT * FROM c
WHERE c.foodGroup = “Soups, Sauces, and Gravies”
ORDER BY c.foodGroup, c._ts ASC
```

Zaktualizowane zasady indeksowania:

```json
{  
        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[],
        "compositeIndexes":[  
            [  
                {  
                    "path":"/foodGroup",
                    "order":"ascending"
        },
                {  
                    "path":"/_ts",
                    "order":"ascending"
                }
            ]
        ]
    }

```

**Opłata za ru:** 8,86 jednostek ru

## <a name="optimize-join-expressions-by-using-a-subquery"></a>Optymalizowanie wyrażeń SPRZĘŻENIa przy użyciu podzapytania
Podzapytania z wieloma wartościami mogą optymalizować wyrażenia `JOIN` przez wypychanie predykatów po każdym wyrażeniu SELECT-wielu, a nie po wszystkich sprzężeniach krzyżowych w klauzuli `WHERE`.

Rozważ następujące zapytanie:

```sql
SELECT Count(1) AS Count
FROM c
JOIN t IN c.tags
JOIN n IN c.nutrients
JOIN s IN c.servings
WHERE t.name = 'infant formula' AND (n.nutritionValue > 0
AND n.nutritionValue < 10) AND s.amount > 1
```

**Opłata za ru:** 167,62 jednostek ru

W przypadku tego zapytania indeks będzie pasować do dowolnego dokumentu, który ma tag o nazwie "Formuła niemowląt", nutritionValue większym niż 0 i obsłużyć wartość większą niż 1. Wyrażenie `JOIN` w tym miejscu będzie przekroczyć iloczyn wszystkich elementów tagów, składników odżywczych i obsługuje tablice dla każdego pasującego dokumentu przed zastosowaniem filtra. Klauzula `WHERE` następnie zastosuje predykat filtru dla każdej krotki `<c, t, n, s>`.

Na przykład, jeśli pasujący dokument ma 10 elementów w każdej z trzech tablic, zostanie rozszerzony na 1 x 10 x 10 x 10 (czyli 1 000) krotek. Użycie podkwerend tutaj może pomóc w filtrowaniu sprzężonych elementów tablicy przed dołączeniem do następnego wyrażenia.

To zapytanie jest równoważne poprzedniemu, ale używa podzapytań:

```sql
SELECT Count(1) AS Count
FROM c
JOIN (SELECT VALUE t FROM t IN c.tags WHERE t.name = 'infant formula')
JOIN (SELECT VALUE n FROM n IN c.nutrients WHERE n.nutritionValue > 0 AND n.nutritionValue < 10)
JOIN (SELECT VALUE s FROM s IN c.servings WHERE s.amount > 1)
```

**Opłata za ru:** 22,17 jednostek ru

Załóżmy, że tylko jeden element w tablicy tagów pasuje do filtru i istnieje pięć elementów dla obydwu składników pokarmowych i obsługujących tablice. Wyrażenia `JOIN` będą następnie rozszerzane na 1 x 1 x 5 x 5 = 25 elementów, w przeciwieństwie do 1 000 elementów w pierwszym zapytaniu.

## <a name="queries-where-retrieved-document-count-is-equal-to-output-document-count"></a>Zapytania, w przypadku których liczba pobranych dokumentów jest równa liczbie dokumentów wyjściowych

Jeśli liczba pobranych dokumentów jest w przybliżeniu równa liczbie dokumentów wyjściowych, oznacza to, że zapytanie nie było wymagane do skanowania wielu zbędnych dokumentów. W przypadku wielu zapytań, takich jak te, które używają słowa kluczowego TOP, liczba pobranych dokumentów może przekroczyć liczbę dokumentów wyjściowych o 1. Nie powinno to być przyczyną problemu.

## <a name="avoid-cross-partition-queries"></a>Unikaj zapytań między partycjami

Azure Cosmos DB używa [partycjonowania](partitioning-overview.md) do skalowania poszczególnych kontenerów jako jednostki żądania i magazynu danych. Każda partycja fizyczna ma oddzielny i niezależny indeks. Jeśli zapytanie ma filtr równości pasujący do klucza partycji kontenera, wystarczy sprawdzić indeks odpowiedniej partycji. Ta optymalizacja zmniejsza łączną liczbę wymaganych zapytań.

Jeśli masz dużą liczbę zainicjowanych przez niego jednostek RU (ponad 30 000) lub dużą ilość przechowywanych danych (ponad 100 GB), być może masz wystarczająco duży rozmiar kontenera, aby zobaczyć znaczną redukcję opłat za zapytania RU.

Na przykład jeśli utworzymy kontener z kluczem partycji żywności, następujące zapytania będą wymagały tylko sprawdzenia pojedynczej partycji fizycznej:

```sql
SELECT * FROM c
WHERE c.foodGroup = "Soups, Sauces, and Gravies" and c.description = "Mushroom, oyster, raw"
```

Te zapytania byłyby również zoptymalizowane przez dołączenie klucza partycji do zapytania:

```sql
SELECT * FROM c
WHERE c.foodGroup IN("Soups, Sauces, and Gravies", "Vegetables and Vegetable Products") and c.description = "Mushroom, oyster, raw"
```

Zapytania, które mają filtry zakresu w kluczu partycji lub nie mają żadnych filtrów w kluczu partycji, muszą mieć wartość "wentylator-out" i sprawdzać każdy indeks partycji fizycznej pod kątem wyników.

```sql
SELECT * FROM c
WHERE c.description = "Mushroom, oyster, raw"
```

```sql
SELECT * FROM c
WHERE c.foodGroup > "Soups, Sauces, and Gravies" and c.description = "Mushroom, oyster, raw"
```

## <a name="filters-on-multiple-properties"></a>Filtry dla wielu właściwości

Zapytania z filtrami dla wielu właściwości zwykle wykorzystują indeks zakresu, ale będą bardziej wydajne, jeśli będą mogły być obsługiwane z indeksu złożonego. W przypadku małych ilości danych Ta optymalizacja nie będzie miała znaczącego wpływu. Mogą jednak okazać się przydatne w przypadku dużych ilości danych. Można zoptymalizować maksymalnie jeden filtr nierówności na indeks złożony. Jeśli zapytanie ma wiele filtrów bez równości, należy wybrać jedną z nich, która będzie korzystać z indeksu złożonego. Reszta będzie nadal korzystać z indeksów zakresów. Filtr bez równości musi być zdefiniowany jako ostatni w indeksie złożonym. [Dowiedz się więcej na temat indeksów złożonych](index-policy.md#composite-indexes)

Poniżej przedstawiono kilka przykładów zapytań, które można zoptymalizować za pomocą indeksu złożonego:

```sql
SELECT * FROM c
WHERE c.foodGroup = "Vegetables and Vegetable Products" AND c._ts = 1575503264
```

```sql
SELECT * FROM c
WHERE c.foodGroup = "Vegetables and Vegetable Products" AND c._ts > 1575503264
```

Oto odpowiedni indeks złożony:

```json
{  
        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[],
        "compositeIndexes":[  
            [  
                {  
                    "path":"/foodGroup",
                    "order":"ascending"
                },
                {  
                    "path":"/_ts",
                    "order":"ascending"
                }
            ]
        ]
}
```

## <a name="optimizations-that-reduce-query-latency"></a>Optymalizacje, które zmniejszają opóźnienie zapytania:

W wielu przypadkach opłata za usługę RU może być akceptowalna, ale opóźnienie zapytania jest wciąż zbyt wysokie. Poniższe sekcje zawierają omówienie wskazówek dotyczących skracania opóźnień zapytań. W przypadku uruchomienia tego samego zapytania wiele razy w tym samym zestawie danych będzie on miał te same opłaty RU za każdym razem. Opóźnienia zapytań mogą jednak różnić się między wykonaniami zapytań.

## <a name="improve-proximity"></a>Zwiększ bliskość

Zapytania uruchamiane z innego regionu niż konto Azure Cosmos DB będą miały wyższy czas oczekiwania niż w przypadku uruchomienia w tym samym regionie. Na przykład jeśli uruchomiono kod na komputerze stacjonarnym, należy oczekiwać opóźnienia na dziesiątki lub setki (lub więcej) milisekund, jeśli zapytanie pochodzi z maszyny wirtualnej w tym samym regionie platformy Azure co Azure Cosmos DB. Ogólnie [rozpowszechniać dane w Azure Cosmos DB](distribute-data-globally.md) , aby zapewnić, że dane będą widoczne bliżej Twojej aplikacji.

## <a name="increase-provisioned-throughput"></a>Zwiększ przepływność aprowizacji

W Azure Cosmos DB zainicjowana przepływność jest mierzona w jednostkach żądania (RU). Załóżmy, że masz zapytanie, które zużywa 5 jednostek przepływności. Na przykład jeśli zainicjujesz 1 000 RU, będzie można uruchamiać zapytanie 200 razy na sekundę. Jeśli podjęto próbę uruchomienia zapytania, gdy nie jest dostępna wystarczająca przepływność, Azure Cosmos DB zwróci błąd HTTP 429. Wszystkie bieżące zestawy SDK interfejsu API (SQL) na początku zostaną automatycznie ponowić próbę wykonania tego zapytania po upływie krótkiego okresu. Żądania ograniczone przez dłuższy czas mogą zwiększyć ilość czasu oczekiwania na zainicjowaną przepływność zapytań. Można obserwować [łączną liczbę żądań z ograniczeniami](use-metrics.md#understand-how-many-requests-are-succeeding-or-causing-errors) w bloku metryk Azure Portal.

## <a name="increase-maxconcurrency"></a>Zwiększ MaxConcurrency

Zapytania równoległe działają przez wykonywanie zapytań na wielu partycjach równolegle. Jednak dane z pojedynczej kolekcji partycjonowanej są pobierane sekwencyjnie w odniesieniu do zapytania. Dlatego dostosowanie MaxConcurrency do liczby partycji ma największą szansę na osiągnięcie najbardziej wydajnego zapytania, pod warunkiem, że wszystkie inne warunki systemu pozostają takie same. Jeśli nie znasz liczby partycji, możesz ustawić dużą liczbę MaxConcurrency (lub MaxDegreesOfParallelism w starszych wersjach SDK), a system wybierze minimalną (liczbę partycji, dane wejściowe podane przez użytkownika) jako maksymalny stopień równoległości.

## <a name="increase-maxbuffereditemcount"></a>Zwiększ MaxBufferedItemCount

Zapytania są zaprojektowane w celu wstępnego pobrania wyników, podczas gdy bieżąca partia wyników jest przetwarzana przez klienta. Wstępne pobieranie pomaga w ogólnym ulepszaniu opóźnienia zapytania. Ustawienie MaxBufferedItemCount ogranicza liczbę wstępnie pobranych wyników. Ustawiając tę wartość na oczekiwaną liczbę zwracanych wyników (lub wyższą liczbę), zapytanie może otrzymywać maksymalne korzyści wynikające z wstępnego pobierania. Ustawienie wartości-1 umożliwia systemowi automatyczne decydowanie o liczbie elementów do buforowania.

## <a name="next-steps"></a>Następne kroki
Zapoznaj się z poniższymi dokumentami, jak mierzyć jednostek ru na zapytanie, uzyskać statystyki wykonywania w celu dostrajania zapytań i nie tylko:

* [Pobieranie metryk wykonywania zapytań SQL przy użyciu zestawu .NET SDK](profile-sql-api-query.md)
* [Tuning query performance with Azure Cosmos DB (Dostosowywanie wydajności zapytań w usłudze Azure Cosmos DB)](sql-api-sql-query-metrics.md)
* [Porady dotyczące wydajności dla zestawu .NET SDK](performance-tips.md)
