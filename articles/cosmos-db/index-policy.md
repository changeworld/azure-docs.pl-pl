---
title: Zasady indeksowania usługi Azure Cosmos DB
description: Dowiedz się, jak skonfigurować i zmienić domyślne zasady indeksowania dla automatycznego indeksowania i większej wydajności w usłudze Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/26/2020
ms.author: tisande
ms.openlocfilehash: 930f156ebec76be860e7af02d41540ce67982f92
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80292058"
---
# <a name="indexing-policies-in-azure-cosmos-db"></a>Zasady indeksowania w usłudze Azure Cosmos DB

W usłudze Azure Cosmos DB każdy kontener ma zasady indeksowania, które określają, jak elementy kontenera powinny być indeksowane. Domyślna zasada indeksowania dla nowo utworzonych kontenerów indeksuje każdą właściwość każdego elementu, wymuszając indeksy zakresu dla dowolnego ciągu lub liczby oraz indeksy przestrzenne dla dowolnego obiektu GeoJSON typu Point. Dzięki temu można uzyskać wysoką wydajność kwerendy bez konieczności myślenia o indeksowania i zarządzania indeksem z góry.

W niektórych sytuacjach może być potrzebne zastąpienie tego automatycznego zachowania zachowaniem lepiej dostosowanym do wymagań. Zasady indeksowania kontenera można dostosować, ustawiając jego *tryb indeksowania*oraz dołączając lub wykluczając *ścieżki właściwości.*

> [!NOTE]
> Metoda aktualizowania zasad indeksowania opisanych w tym artykule dotyczy tylko interfejsu API SQL (Core) usługi Azure Cosmos DB.

## <a name="indexing-mode"></a>Tryb indeksowania

Usługa Azure Cosmos DB obsługuje dwa tryby indeksowania:

- **Spójne:** Indeks jest aktualizowany synchronicznie podczas tworzenia, aktualizowania lub usuwania elementów. Oznacza to, że spójność zapytań odczytu będzie [spójność skonfigurowana dla konta](consistency-levels.md).
- **Brak:** Indeksowanie jest wyłączone w kontenerze. Jest to często używane, gdy kontener jest używany jako magazyn czystej wartości klucza bez konieczności indeksów pomocniczych. Może również służyć do poprawy wydajności operacji zbiorczych. Po zakończeniu operacji zbiorczych tryb indeksu można ustawić na Spójne, a następnie monitorować przy użyciu [IndexTransformationProgress](how-to-manage-indexing-policy.md#use-the-net-sdk-v2) do czasu zakończenia.

> [!NOTE]
> Usługa Azure Cosmos DB obsługuje również tryb indeksowania z opóźnieniem. Indeksowanie z opóźnieniem wykonuje aktualizacje indeksu na znacznie niższym poziomie priorytetu, gdy aparat nie wykonuje żadnej innej pracy. Może to spowodować **niespójne lub niekompletne** wyniki kwerendy. Jeśli planujesz kwerendy kontenera usługi Cosmos, nie należy wybierać indeksowanie z opóźnieniem.

Domyślnie zasada indeksowania jest `automatic`ustawiona na . Osiąga się to poprzez `automatic` ustawienie właściwości w zasadach indeksowania na `true`. Ustawienie tej `true` właściwości, aby umożliwia usługi Azure CosmosDB automatycznie indeksować dokumenty, ponieważ są one zapisywane.

## <a name="including-and-excluding-property-paths"></a><a id="include-exclude-paths"></a>Włączanie ścieżek właściwości i wykluczanie

Niestandardowe zasady indeksowania można określić ścieżki właściwości, które są jawnie uwzględnione lub wykluczone z indeksowania. Optymalizując liczbę ścieżek, które są indeksowane, można zmniejszyć ilość miejsca do magazynowania używanego przez kontener i poprawić opóźnienie operacji zapisu. Ścieżki te są definiowane [zgodnie z metodą opisaną w sekcji przegląd indeksowania](index-overview.md#from-trees-to-property-paths) z następującymi dodatkami:

- ścieżka prowadząca do wartości skalarnej (ciągu lub liczby) kończy się`/?`
- elementy z tablicy są `/[]` adresowane razem poprzez `/0` `/1` notację (zamiast itp.)
- symbol `/*` wieloznaczny może być używany do dopasowania dowolnych elementów poniżej węzła

Biorąc ten sam przykład ponownie:

```
    {
        "locations": [
            { "country": "Germany", "city": "Berlin" },
            { "country": "France", "city": "Paris" }
        ],
        "headquarters": { "country": "Belgium", "employees": 250 }
        "exports": [
            { "city": "Moscow" },
            { "city": "Athens" }
        ]
    }
```

- `employees` ścieżka `headquarters`jest`/headquarters/employees/?`

- ' `locations` `country` ścieżka jest`/locations/[]/country/?`

- droga do wszystkiego, pod czym `headquarters` jest`/headquarters/*`

Na przykład możemy uwzględnić ścieżkę. `/headquarters/employees/?` Ta ścieżka zapewni, że indeksujemy właściwość pracowników, ale nie indeksuje dodatkowych zagnieżdżonych JSON w ramach tej właściwości.

## <a name="includeexclude-strategy"></a>Uwzględnij/wyklucz strategię

Wszystkie zasady indeksowania muszą zawierać `/*` ścieżkę główną jako ścieżkę dołączona lub wykluczoną.

- Dołącz ścieżkę główną, aby selektywnie wykluczyć ścieżki, które nie muszą być indeksowane. Jest to zalecane podejście, ponieważ umożliwia usługi Azure Cosmos DB proaktywnie indeksować wszystkie nowe właściwości, które mogą być dodawane do modelu.
- Wyklucz ścieżkę główną, aby selektywnie uwzględnić ścieżki, które muszą być indeksowane.

- W przypadku ścieżek ze zwykłymi znakami, które zawierają: znaki alfanumeryczne i _ (podkreślenie), nie trzeba uciekać ciąg ścieżki wokół cudzysłowów (na przykład "/path/?"). W przypadku ścieżek z innymi znakami specjalnymi należy uciec od ciągu\"ścieżki wokół\"cudzysłowów (na przykład "/ path-abc /?"). Jeśli spodziewasz się znaków specjalnych na swojej drodze, możesz uciec z każdej ścieżki dla bezpieczeństwa. Funkcjonalnie nie ma żadnej różnicy, jeśli uciec każdej ścieżki Vs tylko te, które mają znaki specjalne.

- Właściwość `_etag` systemowa jest domyślnie wykluczona z indeksowania, chyba że etag zostanie dodany do dołączonej ścieżki indeksowania.

- Jeśli tryb indeksowania jest ustawiony na **spójny**, właściwości `id` systemu i `_ts` są automatycznie indeksowane.

W przypadku włączania ścieżek i wykluczania można napotkać następujące atrybuty:

- `kind`może być `range` albo `hash`lub . Funkcja indeksu zakresu zapewnia wszystkie funkcje indeksu mieszania, dlatego zalecamy użycie indeksu zakresu.

- `precision`jest liczbą zdefiniowaną na poziomie indeksu dla uwzględnionych ścieżek. Wartość wskazuje `-1` maksymalną precyzję. Zalecamy zawsze ustawienie `-1`tej wartości na .

- `dataType`może być `String` albo `Number`lub . Wskazuje typy właściwości JSON, które będą indeksowane.

Jeśli nie zostanie określony, te właściwości będą miały następujące wartości domyślne:

| **Nazwa właściwości**     | **Wartość domyślna** |
| ----------------------- | -------------------------------- |
| `kind`   | `range` |
| `precision`   | `-1`  |
| `dataType`    | `String` i `Number` |

Zobacz [tę sekcję,](how-to-manage-indexing-policy.md#indexing-policy-examples) aby zapoznać się z przykładami zasad indeksowania, aby uwzględnić ścieżki i wykluczyć.

## <a name="spatial-indexes"></a>Indeksy przestrzenne

Podczas definiowania ścieżki przestrzennej w zasadach indeksowania ```type``` należy zdefiniować, który indeks powinien być stosowany do tej ścieżki. Możliwe typy indeksów przestrzennych obejmują:

* Punkt

* Wielokąt

* Multipolygon

* Linestring

Usługa Azure Cosmos DB domyślnie nie tworzy żadnych indeksów przestrzennych. Jeśli chcesz użyć funkcji wbudowanych sql przestrzennych, należy utworzyć indeks przestrzenny na wymaganych właściwości. Zobacz [tę sekcję,](geospatial.md) aby dodawać przykłady zasad indeksowania do dodawania indeksów przestrzennych.

## <a name="composite-indexes"></a>Indeksy złożone

Kwerendy, które `ORDER BY` mają klauzulę z co najmniej dwie właściwości wymagają indeksu złożonego. Można również zdefiniować indeks złożony, aby poprawić wydajność wielu zapytań równości i zakresu. Domyślnie nie zdefiniowano żadnych indeksów złożonych, więc należy [dodać indeksy złożone w](how-to-manage-indexing-policy.md#composite-indexing-policy-examples) razie potrzeby.

Podczas definiowania indeksu złożonego należy określić:

- Dwie lub więcej ścieżek właściwości. Kolejność, w której ścieżki właściwości są zdefiniowane, ma znaczenie.

- Kolejność (rosnąca lub malejąca).

> [!NOTE]
> Po dodaniu indeksu złożonego kwerenda będzie korzystać z istniejących indeksów zakresu, dopóki nie zostanie ukończony nowy dodatek indeksu złożonego. W związku z tym po dodaniu indeksu złożonego, nie może natychmiast obserwować poprawę wydajności. Możliwe jest śledzenie postępu transformacji indeksu [za pomocą jednego z SDK](how-to-manage-indexing-policy.md).

### <a name="order-by-queries-on-multiple-properties"></a>KOLEJNOŚĆ według zapytań dotyczących wielu właściwości:

Następujące zagadnienia są używane podczas korzystania z indeksów złożonych dla kwerend z klauzulą `ORDER BY` z co najmniej dwiema właściwościami:

- Jeśli złożone ścieżki indeksu nie są zgodne z `ORDER BY` sekwencją właściwości w klauzuli, indeks złożony nie może obsługiwać kwerendy.

- Kolejność złożonych ścieżek indeksu (rosnąco lub malejąco) powinna również odpowiadać `order` klauzuli w klauzuli. `ORDER BY`

- Indeks złożony obsługuje `ORDER BY` również klauzulę o odwrotnej kolejności na wszystkich ścieżkach.

Rozważmy następujący przykład, w którym indeks złożony jest zdefiniowany na nazwę właściwości, wiek i _ts:

| **Indeks złożony**     | **Przykładowa `ORDER BY` kwerenda**      | **Obsługiwane przez Composite Index?** |
| ----------------------- | -------------------------------- | -------------- |
| ```(name ASC, age ASC)```   | ```SELECT * FROM c ORDER BY c.name ASC, c.age asc``` | ```Yes```            |
| ```(name ASC, age ASC)```   | ```SELECT * FROM c ORDER BY c.age ASC, c.name asc```   | ```No```             |
| ```(name ASC, age ASC)```    | ```SELECT * FROM c ORDER BY c.name DESC, c.age DESC``` | ```Yes```            |
| ```(name ASC, age ASC)```     | ```SELECT * FROM c ORDER BY c.name ASC, c.age DESC``` | ```No```             |
| ```(name ASC, age ASC, timestamp ASC)``` | ```SELECT * FROM c ORDER BY c.name ASC, c.age ASC, timestamp ASC``` | ```Yes```            |
| ```(name ASC, age ASC, timestamp ASC)``` | ```SELECT * FROM c ORDER BY c.name ASC, c.age ASC``` | ```No```            |

Należy dostosować zasady indeksowania, dzięki czemu `ORDER BY` można obsługiwać wszystkie niezbędne zapytania.

### <a name="queries-with-filters-on-multiple-properties"></a>Zapytania z filtrami na wielu właściwościach

Jeśli kwerenda ma filtry na co najmniej dwie właściwości, może być przydatne do utworzenia indeksu złożonego dla tych właściwości.

Rozważmy na przykład następującą kwerendę, która ma filtr równości na dwóch właściwościach:

```sql
SELECT * FROM c WHERE c.name = "John" AND c.age = 18
```

Ta kwerenda będzie bardziej wydajne, biorąc mniej czasu i zużywa mniej RU, jeśli jest w stanie wykorzystać złożony indeks na (nazwa ASC, wiek ASC).

Zapytania z filtrami zakresu można również zoptymalizować za pomocą indeksu złożonego. Jednak kwerenda może mieć tylko jeden zakres filtru. Filtry zakresu `>` `<`obejmują `<=` `>=`, `!=`, , i . Filtr zakresu powinien być zdefiniowany jako ostatni w indeksie złożonym.

Należy wziąć pod uwagę następujące zapytanie z filtrami równości i zakresu:

```sql
SELECT * FROM c WHERE c.name = "John" AND c.age > 18
```

Ta kwerenda będzie bardziej wydajne z indeksu złożonego na (nazwa ASC, wiek ASC). Jednak kwerenda nie będzie korzystać z indeksu złożonego na (wiek ASC, nazwa ASC), ponieważ filtry równości muszą być zdefiniowane najpierw w indeksie złożonym.

Podczas tworzenia indeksów złożonych dla kwerend z filtrami dotyczącymi wielu właściwości używane są następujące zagadnienia: następujące zagadnienia:

- Właściwości w filtrze kwerendy powinny być zgodne z właściwościami w indeksie złożonym. Jeśli właściwość znajduje się w indeksie złożonym, ale nie jest uwzględniona w kwerendzie jako filtr, kwerenda nie będzie korzystać z indeksu złożonego.
- Jeśli kwerenda ma dodatkowe właściwości w filtrze, które nie zostały zdefiniowane w indeksie złożonym, do oceny kwerendy zostanie użyta kombinacja indeksów złożonych i zakresowych. Będzie to wymagało mniejszej liczby RU niż wyłącznie przy użyciu indeksów zakresu.
- Jeśli właściwość ma filtr`>` `<`zakresu `<=` `>=`( `!=`, , , , lub ), to ta właściwość powinna być zdefiniowana jako ostatnia w indeksie złożonym. Jeśli kwerenda ma więcej niż jeden filtr zakresu, nie będzie korzystać z indeksu złożonego.
- Podczas tworzenia indeksu złożonego w celu optymalizacji zapytań z wieloma filtrami indeks `ORDER` złożony nie będzie miał wpływu na wyniki. Ta właściwość jest opcjonalna.
- Jeśli nie zdefiniujesz indeksu złożonego dla kwerendy z filtrami na wiele właściwości, kwerenda będzie nadal sukces. Jednak koszt RU kwerendy można zmniejszyć za pomocą indeksu złożonego.

Rozważmy następujące przykłady, w których indeks złożony jest zdefiniowany na nazwę właściwości, wiek i sygnatura czasowa:

| **Indeks złożony**     | **Przykładowa kwerenda**      | **Obsługiwane przez Composite Index?** |
| ----------------------- | -------------------------------- | -------------- |
| ```(name ASC, age ASC)```   | ```SELECT * FROM c WHERE c.name = "John" AND c.age = 18``` | ```Yes```            |
| ```(name ASC, age ASC)```   | ```SELECT * FROM c WHERE c.name = "John" AND c.age > 18```   | ```Yes```             |
| ```(name DESC, age ASC)```    | ```SELECT * FROM c WHERE c.name = "John" AND c.age > 18``` | ```Yes```            |
| ```(name ASC, age ASC)```     | ```SELECT * FROM c WHERE c.name != "John" AND c.age > 18``` | ```No```             |
| ```(name ASC, age ASC, timestamp ASC)``` | ```SELECT * FROM c WHERE c.name = "John" AND c.age = 18 AND c.timestamp > 123049923``` | ```Yes```            |
| ```(name ASC, age ASC, timestamp ASC)``` | ```SELECT * FROM c WHERE c.name = "John" AND c.age < 18 AND c.timestamp = 123049923``` | ```No```            |

### <a name="queries-with-a-filter-as-well-as-an-order-by-clause"></a>Zapytania z filtrem, a także z klauzulą ORDER BY

Jeśli kwerenda filtruje jedną lub więcej właściwości i ma różne właściwości w klauzuli ORDER BY, może `ORDER BY` być pomocne dodanie właściwości w filtrze do klauzuli.

Na przykład przez dodanie właściwości w filtrze do order by klauzuli, następujące zapytanie może być przepisany do wykorzystania indeksu złożonego:

Kwerenda używająca indeksu zakresu:

```sql
SELECT * FROM c WHERE c.name = "John" ORDER BY c.timestamp
```

Kwerenda przy użyciu indeksu złożonego:

```sql
SELECT * FROM c WHERE c.name = "John" ORDER BY c.name, c.timestamp
```

Ten sam wzorzec i optymalizacje zapytań można uogólnić dla kwerend z wieloma filtrami równości:

Kwerenda używająca indeksu zakresu:

```sql
SELECT * FROM c WHERE c.name = "John", c.age = 18 ORDER BY c.timestamp
```

Kwerenda przy użyciu indeksu złożonego:

```sql
SELECT * FROM c WHERE c.name = "John", c.age = 18 ORDER BY c.name, c.age, c.timestamp
```

Podczas tworzenia indeksów złożonych używane są następujące zagadnienia w `ORDER BY` celu optymalizacji kwerendy za pomocą filtru i klauzuli:

* Jeśli kwerenda filtruje właściwości, powinny one być `ORDER BY` uwzględnione najpierw w klauzuli.
* Jeśli nie zdefiniujesz indeksu złożonego w kwerendzie `ORDER BY` z filtrem na jednej właściwości i osobnej klauzuli przy użyciu innej właściwości, kwerenda będzie nadal sukces. Jednak koszt RU kwerendy można zmniejszyć za pomocą indeksu złożonego, `ORDER BY` szczególnie jeśli właściwość w klauzuli ma wysoką kardynalność.
* Wszystkie zagadnienia dotyczące tworzenia indeksów złożonych dla `ORDER BY` kwerend z wieloma właściwościami, a także kwerend z filtrami na wiele właściwości nadal mają zastosowanie.


| **Indeks złożony**                      | **Przykładowa `ORDER BY` kwerenda**                                  | **Obsługiwane przez Composite Index?** |
| ---------------------------------------- | ------------------------------------------------------------ | --------------------------------- |
| ```(name ASC, timestamp ASC)```          | ```SELECT * FROM c WHERE c.name = "John" ORDER BY c.name ASC, c.timestamp ASC``` | `Yes` |
| ```(name ASC, timestamp ASC)```          | ```SELECT * FROM c WHERE c.name = "John" ORDER BY c.timestamp ASC, c.name ASC``` | `No`  |
| ```(name ASC, timestamp ASC)```          | ```SELECT * FROM c WHERE c.name = "John" ORDER BY c.timestamp ASC``` | ```No```   |
| ```(age ASC, name ASC, timestamp ASC)``` | ```SELECT * FROM c WHERE c.age = 18 and c.name = "John" ORDER BY c.age ASC, c.name ASC,c.timestamp ASC``` | `Yes` |
| ```(age ASC, name ASC, timestamp ASC)``` | ```SELECT * FROM c WHERE c.age = 18 and c.name = "John" ORDER BY c.timestamp ASC``` | `No` |

## <a name="modifying-the-indexing-policy"></a>Modyfikowanie zasad indeksowania

Zasady indeksowania kontenera można zaktualizować w dowolnym momencie [przy użyciu witryny Azure portal lub jednego z obsługiwanych zestawów SDK.](how-to-manage-indexing-policy.md) Aktualizacja zasad indeksowania wyzwala transformację ze starego indeksu do nowego, który jest wykonywany w trybie online i w miejscu (więc nie dodatkowe miejsce do magazynowania jest zużywane podczas operacji). Indeks starych zasad jest skutecznie przekształcany do nowych zasad bez wpływu na dostępność zapisu lub przepływność aprowizowana w kontenerze. Transformacja indeksu jest operacją asynchronizacyjną, a czas potrzebny do ukończenia zależy od aprowizowanej przepływności, liczby elementów i ich rozmiaru.

> [!NOTE]
> Podczas dodawania zakresu lub indeksu przestrzennego kwerendy mogą nie zwracać wszystkich pasujących wyników i będą to robić bez zwracania błędów. Oznacza to, że wyniki kwerendy mogą nie być spójne, dopóki transformacja indeksu nie zostanie zakończona. Możliwe jest śledzenie postępu transformacji indeksu [za pomocą jednego z SDK](how-to-manage-indexing-policy.md).

Jeśli tryb nowej zasady indeksowania jest ustawiony na Spójne, nie można zastosować żadnej innej zmiany zasad indeksowania podczas przekształcania indeksu. Bieżąca transformacja indeksu można anulować, ustawiając tryb zasad indeksowania na Brak (który natychmiast spadnie indeksu).

## <a name="indexing-policies-and-ttl"></a>Zasady indeksowania i czasu wygaśnięcia

[Funkcja Czas wygaśnięcia (TTL)](time-to-live.md) wymaga, aby indeksowanie było aktywne w kontenerze, który jest włączony. Oznacza to, że:

- nie jest możliwe aktywowanie czasu wygaśnięcia w kontenerze, w którym tryb indeksowania jest ustawiony na Brak,
- nie jest możliwe ustawienie trybu indeksowania na Brak w kontenerze, w którym jest aktywowany czas wygaśnięcia.

W scenariuszach, w których nie ma ścieżki właściwości musi być indeksowane, ale TTL jest wymagane, można użyć zasad indeksowania z:

- tryb indeksowania ustawiony na Spójny, oraz
- nie zawiera ścieżki, oraz
- `/*`jako jedyna wykluczona ścieżka.

## <a name="next-steps"></a>Następne kroki

Więcej informacji na temat indeksowania znajdziesz w następujących artykułach:

- [Omówienie indeksowania](index-overview.md)
- [Jak zarządzać zasadami indeksowania](how-to-manage-indexing-policy.md)
