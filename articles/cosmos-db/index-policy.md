---
title: Azure Cosmos DB zasad indeksowania
description: Dowiedz się, jak skonfigurować i zmienić domyślne zasady indeksowania dla automatycznego indeksowania i zwiększenia wydajności w Azure Cosmos DB.
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/10/2019
ms.author: thweiss
ms.openlocfilehash: 86ac042bdddce36f00be71cc5109618bec909d90
ms.sourcegitcommit: 083aa7cc8fc958fc75365462aed542f1b5409623
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/11/2019
ms.locfileid: "70914170"
---
# <a name="indexing-policies-in-azure-cosmos-db"></a>Zasady indeksowania w Azure Cosmos DB

W Azure Cosmos DB każdy kontener ma zasady indeksowania, które określają sposób indeksowania elementów kontenera. Domyślne zasady indeksowania dla nowo utworzonych kontenerów indeksuje każdą właściwość każdego elementu, wymuszając indeksy zakresów dla dowolnego ciągu lub liczby oraz indeksy przestrzenne dla dowolnego obiektu GEOJSON typu punkt. Dzięki temu można uzyskać wysoką wydajność zapytań bez konieczności zawieszania indeksowania i zarządzania indeksem z góry.

W niektórych sytuacjach może zaistnieć potrzeba zastąpienia tego zachowania automatycznego, aby lepiej spełniało wymagania. Można dostosować zasady indeksowania kontenera, ustawiając jego *tryb indeksowania*i dołączając lub wykluczając *ścieżki właściwości*.

> [!NOTE]
> Metoda aktualizacji zasad indeksowania opisana w tym artykule ma zastosowanie tylko do interfejsu API SQL (Core) Azure Cosmos DB.

## <a name="indexing-mode"></a>Tryb indeksowania

Azure Cosmos DB obsługuje dwa tryby indeksowania:

- **Spójne**: Jeśli zasada indeksowania kontenera jest ustawiona na spójne, indeks zostanie zaktualizowany synchronicznie podczas tworzenia, aktualizowania lub usuwania elementów. Oznacza to, że spójność zapytań odczytu będzie [spójna z konfiguracją dla konta](consistency-levels.md).
- **Brak**: Jeśli dla zasad indeksowania kontenera określono wartość Brak, indeksowanie jest skutecznie wyłączone w tym kontenerze. Jest to często używane, gdy kontener jest używany jako czysty magazyn klucz-wartość bez konieczności stosowania indeksów pomocniczych. Może również ułatwić przyspieszenie operacji wstawiania zbiorczego.

Ponadto należy ustawić właściwość **Automatyczna** w zasadach indeksowania na **wartość true**. Ustawienie dla tej właściwości wartości true umożliwia Azure Cosmos DB Automatyczne indeksowanie dokumentów podczas ich pisania.

## <a name="including-and-excluding-property-paths"></a>Uwzględnianie i wykluczanie ścieżek właściwości

Niestandardowe zasady indeksowania mogą określać ścieżki właściwości, które są jawnie dołączone lub wykluczone z indeksowania. Przez optymalizację liczby indeksowanych ścieżek można obniżyć ilość miejsca używanego przez kontener i poprawić opóźnienia operacji zapisu. Te ścieżki są zdefiniowane po [metodzie opisanej w sekcji Omówienie indeksowania](index-overview.md#from-trees-to-property-paths) z następującymi dodatkami:

- ścieżka prowadząca do wartości skalarnej (ciąg lub Number) ma koniec`/?`
- elementy z tablicy są rozłączone za pośrednictwem `/[]` notacji ( `/0`zamiast `/1` itp.)
- `/*` symbol wieloznaczny może służyć do dopasowania elementów poniżej węzła

Ponowne wykonanie tego samego przykładu:

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

- `headquarters`ścieżka jest`employees``/headquarters/employees/?`

- `locations` "`country` ścieżka jest`/locations/[]/country/?`

- ścieżka do wszystkich elementów w `headquarters` obszarze to`/headquarters/*`

Na przykład możemy dołączyć `/headquarters/employees/?` ścieżkę. Ta ścieżka zapewni, że indeksuje Właściwość Employees, ale nie indeksuje dodatkowych zagnieżdżonych JSON w ramach tej właściwości.

## <a name="includeexclude-strategy"></a>Strategia uwzględniania/wykluczania

Każda zasada indeksowania musi zawierać ścieżkę `/*` katalogu głównego jako dołączoną lub wykluczoną ścieżkę.

- Uwzględnij ścieżkę główną, aby wykluczać ścieżki, które nie muszą być indeksowane. Jest to zalecane podejście, które pozwala Azure Cosmos DB aktywnie indeksować wszelkie nowe właściwości, które mogą zostać dodane do modelu.
- Wyklucz ścieżkę katalogu głównego do selektywnego dołączania ścieżek, które muszą być indeksowane.

- W przypadku ścieżek ze zwykłymi znakami, które zawierają: znaki alfanumeryczne i _ (podkreślenie), nie trzeba określać ciągu ścieżki wokół podwójnych cudzysłowów (na przykład "/Path/?"). W przypadku ścieżek z innymi znakami specjalnymi należy wypróbować ciąg ścieżki wokół podwójnych cudzysłowów (na przykład\""/Path\"-ABC/?"). Jeśli oczekujesz znaków specjalnych w ścieżce, możesz pominąć wszystkie ścieżki w celu zapewnienia bezpieczeństwa. Funkcjonalnie nie ma żadnej różnicy, jeśli wykorzystasz wszystkie ścieżki, a tylko te, które zawierają znaki specjalne.

- Właściwość systemowa "ETag" jest domyślnie wykluczona z indeksowania, chyba że element ETag zostanie dodany do ścieżki dołączonej do indeksowania.

W przypadku dołączania i wykluczania ścieżek mogą wystąpić następujące atrybuty:

- `kind`może być albo `range`. `hash` Funkcja indeksu zakresu zapewnia wszystkie funkcje indeksu wartości skrótu, dlatego zalecamy użycie indeksu zakresu.

- `precision`jest liczbą zdefiniowaną na poziomie indeksu dla dołączonych ścieżek. Wartość `-1` wskazuje maksymalną precyzję. Zalecamy zawsze ustawienie tej wartości na `-1`.

- `dataType`może być albo `String`. `Number` Wskazuje typy właściwości JSON, które będą indeksowane.

Gdy nie zostanie określony, te właściwości będą miały następujące wartości domyślne:

| **Nazwa właściwości**     | **Wartość domyślna** |
| ----------------------- | -------------------------------- |
| `kind`   | `range` |
| `precision`   | `-1`  |
| `dataType`    | `String` i `Number` |

Zapoznaj się z [tą sekcją](how-to-manage-indexing-policy.md#indexing-policy-examples) , aby zapoznać się z przykładami zasad indeksowania obejmującymi i wykluczającymi ścieżki.

## <a name="spatial-indexes"></a>Indeksy przestrzenne

Podczas definiowania ścieżki przestrzennej w zasadach indeksowania należy określić, który indeks ```type``` ma zostać zastosowany do tej ścieżki. Możliwe typy indeksów przestrzennych obejmują:

* Moment

* Tworząc

* MultiPolygon

* LineString

Domyślnie Azure Cosmos DB nie utworzy żadnych indeksów przestrzennych. Jeśli chcesz używać przestrzennych funkcji języka SQL, należy utworzyć indeks przestrzenny dla wymaganych właściwości. Zobacz [tę sekcję](geospatial.md) , aby zapoznać się z przykładami zasad indeksowania służącymi do dodawania indeksów przestrzennych.

## <a name="composite-indexes"></a>Indeksy złożone

Zapytania, które mają `ORDER BY` klauzulę mającą co najmniej dwie właściwości, wymagają złożonego indeksu. Można również zdefiniować indeks złożony, aby zwiększyć wydajność wielu zapytań równości i zakresu. Domyślnie nie są zdefiniowane żadne indeksy złożone, dlatego należy [dodać indeksy złożone](how-to-manage-indexing-policy.md#composite-indexing-policy-examples) zgodnie z wymaganiami.

Podczas definiowania indeksu złożonego należy określić:

- Co najmniej dwie ścieżki właściwości. Sekwencja, w której są zdefiniowane ścieżki właściwości.

- Zamówienie (rosnąco lub malejąco).

> [!NOTE]
> Podczas dodawania indeksu złożonego, podobnie jak w przypadku innych typów indeksów, zapytania mogą zwracać niespójne wyniki w miarę aktualizowania indeksu.

### <a name="order-by-queries-on-multiple-properties"></a>Zaporządkuj według zapytań dotyczących wielu właściwości:

Poniższe zagadnienia są używane podczas używania indeksów złożonych dla zapytań z `ORDER BY` klauzulą z co najmniej dwiema właściwościami:

- Jeśli złożone ścieżki indeksu nie pasują do sekwencji właściwości w `ORDER BY` klauzuli, indeks złożony nie obsługuje zapytania.

- Kolejność ścieżek złożonych indeksów (rosnąco lub malejąco) powinna również być zgodna z `order` `ORDER BY` klauzulą w klauzuli.

- Indeks złożony również obsługuje `ORDER BY` klauzulę z odwrotną kolejnością we wszystkich ścieżkach.

Rozważmy następujący przykład, w którym zdefiniowano indeks złożony w nazwach właściwości, wieku i _ts:

| **Indeks złożony**     | **Przykładowe `ORDER BY` zapytanie**      | **Obsługiwane przez indeks złożony?** |
| ----------------------- | -------------------------------- | -------------- |
| ```(name ASC, age ASC)```   | ```SELECT * FROM c ORDER BY c.name ASC, c.age asc``` | ```Yes```            |
| ```(name ASC, age ASC)```   | ```SELECT * FROM c ORDER BY c.age ASC, c.name asc```   | ```No```             |
| ```(name ASC, age ASC)```    | ```SELECT * FROM c ORDER BY c.name DESC, c.age DESC``` | ```Yes```            |
| ```(name ASC, age ASC)```     | ```SELECT * FROM c ORDER BY c.name ASC, c.age DESC``` | ```No```             |
| ```(name ASC, age ASC, timestamp ASC)``` | ```SELECT * FROM c ORDER BY c.name ASC, c.age ASC, timestamp ASC``` | ```Yes```            |
| ```(name ASC, age ASC, timestamp ASC)``` | ```SELECT * FROM c ORDER BY c.name ASC, c.age ASC``` | ```No```            |

Zasady indeksowania należy dostosować, aby można było obsłużyć wszystkie `ORDER BY` niezbędne zapytania.

### <a name="queries-with-filters-on-multiple-properties"></a>Zapytania z filtrami dla wielu właściwości

Jeśli zapytanie zawiera filtry dla co najmniej dwóch właściwości, warto utworzyć indeks złożony dla tych właściwości.

Rozważmy na przykład następujące zapytanie, które ma filtr równości dla dwóch właściwości:

```sql
SELECT * FROM c WHERE c.name = "John" AND c.age = 18
```

To zapytanie będzie bardziej wydajne, co zajmuje mniej czasu i zużywa mniejszą liczbę jednostek RU, jeśli może wykorzystać indeks złożony w dniu (nazwa ASC, wiek ASC).

Zapytania z filtrami zakresu można także zoptymalizować przy użyciu indeksu złożonego. Jednak zapytanie może mieć tylko jeden filtr zakresu. Filtry zakresu obejmują `>`, `<`, `<=`, `>=`, i `!=`. Filtr zakresu powinien być zdefiniowany jako ostatni w indeksie złożonym.

Rozważ następujące zapytanie z filtrami równości i zakresu:

```sql
SELECT * FROM c WHERE c.name = "John" AND c.age > 18
```

To zapytanie będzie bardziej wydajne z indeksem złożonym w dniu (nazwa ASC, wiek ASC). Jednak zapytanie nie korzysta ze złożonego indeksu w (Age ASC, Name ASC), ponieważ filtry równości muszą być zdefiniowane najpierw w indeksie złożonym.

Poniższe zagadnienia są używane podczas tworzenia indeksów złożonych dla zapytań z filtrami dla wielu właściwości

- Właściwości w filtrze zapytania powinny być zgodne z tymi w indeksie złożonym. Jeśli właściwość znajduje się w indeksie złożonym, ale nie jest uwzględniona w zapytaniu jako filtr, zapytanie nie będzie korzystać z indeksu złożonego.
- Jeśli zapytanie ma dodatkowe właściwości w filtrze, który nie został zdefiniowany w indeksie złożonym, wówczas do obliczenia zapytania zostanie użyta kombinacja indeksów złożonych i zakresów. Będzie to wymagało mniejszej liczby jednostek RU niż w przypadku używania indeksów zakresów.
- Jeśli właściwość ma filtr zakresu (`>`, `<`, `<=` `>=`, lub `!=`), wówczas ta właściwość powinna być zdefiniowana jako Ostatnia w indeksie złożonym. Jeśli zapytanie ma więcej niż jeden filtr zakresu, nie będzie używać indeksu złożonego.
- Podczas tworzenia indeksu złożonego do optymalizowania zapytań z wieloma filtrami `ORDER` indeks złożony nie będzie miał wpływu na wyniki. Ta właściwość jest opcjonalna.
- Jeśli nie zdefiniujesz indeksu złożonego dla zapytania z filtrami dla wielu właściwości, zapytanie będzie nadal kończyło się pomyślnie. Koszt RU zapytania można jednak zmniejszyć przy użyciu indeksu złożonego.

Rozważmy następujące przykłady, w których zdefiniowany jest indeks złożony w polu Nazwa właściwości, wiek i sygnatura czasowa:

| **Indeks złożony**     | **Przykładowe zapytanie**      | **Obsługiwane przez indeks złożony?** |
| ----------------------- | -------------------------------- | -------------- |
| ```(name ASC, age ASC)```   | ```SELECT * FROM c WHERE c.name = "John" AND c.age = 18``` | ```Yes```            |
| ```(name ASC, age ASC)```   | ```SELECT * FROM c WHERE c.name = "John" AND c.age > 18```   | ```Yes```             |
| ```(name DESC, age ASC)```    | ```SELECT * FROM c WHERE c.name = "John" AND c.age > 18``` | ```Yes```            |
| ```(name ASC, age ASC)```     | ```SELECT * FROM c WHERE c.name != "John" AND c.age > 18``` | ```No```             |
| ```(name ASC, age ASC, timestamp ASC)``` | ```SELECT * FROM c WHERE c.name = "John" AND c.age = 18 AND c.timestamp > 123049923``` | ```Yes```            |
| ```(name ASC, age ASC, timestamp ASC)``` | ```SELECT * FROM c WHERE c.name = "John" AND c.age < 18 AND c.timestamp = 123049923``` | ```No```            |

### <a name="queries-with-a-filter-as-well-as-an-order-by-clause"></a>Zapytania z filtrem, a także klauzulą ORDER BY

Jeśli zapytanie filtruje dla jednej lub wielu właściwości i ma inne właściwości w klauzuli Order by, może być pomocne dodanie właściwości w filtrze do `ORDER BY` klauzuli.

Na przykład dodając właściwości w filtrze do klauzuli ORDER BY, można ponownie napisać następujące zapytanie, aby użyć złożonego indeksu:

Zapytanie przy użyciu indeksu zakresu:

```sql
SELECT * FROM c WHERE c.name = "John" ORDER BY c.timestamp
```

Zapytanie przy użyciu indeksu złożonego:

```sql
SELECT * FROM c WHERE c.name = "John" ORDER BY c.name, c.timestamp
```

Te same optymalizacje wzorca i kwerendy mogą być uogólnione dla zapytań z wieloma filtrami równości:

Zapytanie przy użyciu indeksu zakresu:

```sql
SELECT * FROM c WHERE c.name = "John", c.age = 18 ORDER BY c.timestamp
```

Zapytanie przy użyciu indeksu złożonego:

```sql
SELECT * FROM c WHERE c.name = "John", c.age = 18 ORDER BY c.name, c.age, c.timestamp
```

Poniższe zagadnienia są używane podczas tworzenia indeksów złożonych w celu optymalizacji zapytania z klauzulą Filter `ORDER BY` i:

* Jeśli zapytanie filtruje właściwości, należy je najpierw uwzględnić w `ORDER BY` klauzuli.
* Jeśli nie zdefiniujesz indeksu złożonego w zapytaniu z filtrem dla jednej właściwości i oddzielną `ORDER BY` klauzulą używającą innej właściwości, zapytanie będzie nadal kończyć się powodzeniem. Koszt ru zapytania można jednak zmniejszyć przy użyciu indeksu złożonego, szczególnie jeśli właściwość w `ORDER BY` klauzuli ma wysoką Kardynalność.
* Wszystkie zagadnienia dotyczące tworzenia indeksów złożonych `ORDER BY` dla zapytań z wieloma właściwościami, a także zapytania z filtrami dla wielu właściwości nadal mają zastosowanie.


| **Indeks złożony**                      | **Przykładowe `ORDER BY` zapytanie**                                  | **Obsługiwane przez indeks złożony?** |
| ---------------------------------------- | ------------------------------------------------------------ | --------------------------------- |
| ```(name ASC, timestamp ASC)```          | ```SELECT * FROM c WHERE c.name = "John" ORDER BY c.name ASC, c.timestamp ASC``` | `Yes` |
| ```(name ASC, timestamp ASC)```          | ```SELECT * FROM c WHERE c.name = "John" ORDER BY c.timestamp ASC, c.name ASC``` | `No`  |
| ```(name ASC, timestamp ASC)```          | ```SELECT * FROM c WHERE c.name = "John" ORDER BY c.timestamp ASC``` | ```No```   |
| ```(age ASC, name ASC, timestamp ASC)``` | ```SELECT * FROM c WHERE c.age = 18 and c.name = "John" ORDER BY c.age ASC, c.name ASC,c.timestamp ASC``` | `Yes` |
| ```(age ASC, name ASC, timestamp ASC)``` | ```SELECT * FROM c WHERE c.age = 18 and c.name = "John" ORDER BY c.timestamp ASC``` | `No` |

## <a name="modifying-the-indexing-policy"></a>Modyfikowanie zasad indeksowania

Zasady indeksowania kontenera można aktualizować w dowolnym momencie przy [użyciu Azure Portal lub jednego z obsługiwanych zestawów SDK](how-to-manage-indexing-policy.md). Aktualizacja zasad indeksowania wyzwala transformację ze starego indeksu do nowego, który jest wykonywany w trybie online i w miejscu (dlatego w trakcie operacji nie jest używane dodatkowe miejsce do magazynowania). Stary indeks zasad jest efektywnie przekształcany w nowe zasady bez wpływu na dostępność zapisu lub przepływność zainicjowaną w kontenerze. Przekształcanie indeksów jest operacją asynchroniczną i czas potrzebny do ukończenia zależy od przepływności, liczby elementów i ich rozmiaru.

> [!NOTE]
> Gdy trwa ponowne indeksowanie, zapytania mogą nie zwracać wszystkich pasujących wyników i nie będą zwracać żadnych błędów. Oznacza to, że wyniki zapytania mogą nie być spójne do momentu zakończenia transformacji indeksu. Istnieje możliwość śledzenia postępu transformacji indeksu przy [użyciu jednego z zestawów SDK](how-to-manage-indexing-policy.md).

Jeśli nowy tryb zasad indeksowania jest ustawiony na spójne, nie można zastosować żadnej innej zmiany zasad indeksowania, gdy trwa przekształcanie indeksu. Uruchomioną transformację indeksu można anulować, ustawiając tryb zasad indeksowania na brak (co spowoduje natychmiastowe porzucenie indeksu).

## <a name="indexing-policies-and-ttl"></a>Zasady indeksowania i czas wygaśnięcia

[Funkcja czasu wygaśnięcia (TTL, Time-to-Live)](time-to-live.md) wymaga, aby indeksowanie było aktywne w kontenerze, w którym jest włączona. Oznacza to, że:

- nie można aktywować czasu wygaśnięcia w kontenerze, w którym tryb indeksowania jest ustawiony na none.
- nie można ustawić trybu indeksowania na brak w kontenerze, w którym jest aktywowany czas wygaśnięcia.

W przypadku scenariuszy, w których nie ma potrzeby indeksowania ścieżki właściwości, ale czas wygaśnięcia jest wymagany, można użyć zasad indeksowania z:

- Tryb indeksowania ustawiony na spójne i
- Brak dołączonej ścieżki i
- `/*`jako jedyna wykluczona ścieżka.

## <a name="next-steps"></a>Następne kroki

Więcej informacji na temat indeksowania znajdziesz w następujących artykułach:

- [Omówienie indeksowania](index-overview.md)
- [Jak zarządzać zasadami indeksowania](how-to-manage-indexing-policy.md)
