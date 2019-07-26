---
title: Azure Cosmos DB zasad indeksowania
description: Dowiedz się, jak skonfigurować i zmienić domyślne zasady indeksowania dla automatycznego indeksowania i zwiększenia wydajności w Azure Cosmos DB.
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.author: thweiss
ms.openlocfilehash: 01e3e1f1c9bffee0604de1260e8e466f5b1d229d
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/24/2019
ms.locfileid: "68467876"
---
# <a name="indexing-policies-in-azure-cosmos-db"></a>Zasady indeksowania w Azure Cosmos DB

W Azure Cosmos DB każdy kontener ma zasady indeksowania, które określają sposób indeksowania elementów kontenera. Domyślne zasady indeksowania dla nowo utworzonych kontenerów indeksuje każdą właściwość każdego elementu, wymuszając indeksy zakresów dla dowolnego ciągu lub liczby oraz indeksy przestrzenne dla dowolnego obiektu GEOJSON typu punkt. Dzięki temu można uzyskać wysoką wydajność zapytań bez konieczności zawieszania indeksowania i zarządzania indeksem z góry.

W niektórych sytuacjach może zaistnieć potrzeba zastąpienia tego zachowania automatycznego, aby lepiej spełniało wymagania. Można dostosować zasady indeksowania kontenera, ustawiając jego *tryb indeksowania*i dołączając lub wykluczając *ścieżki właściwości*.

> [!NOTE]
> Metoda aktualizacji zasad indeksowania opisana w tym artykule ma zastosowanie tylko do interfejsu API SQL (Core) Azure Cosmos DB.

## <a name="indexing-mode"></a>Tryb indeksowania

Azure Cosmos DB obsługuje dwa tryby indeksowania:

- **Spójne**: Jeśli zasada indeksowania kontenera jest ustawiona na spójne, indeks zostanie zaktualizowany synchronicznie podczas tworzenia, aktualizowania lub usuwania elementów. Oznacza to, że spójność zapytań odczytu będzie spójna z [konfiguracją dla konta](consistency-levels.md).

- **Brak**: Jeśli dla zasad indeksowania kontenera określono wartość Brak, indeksowanie jest skutecznie wyłączone w tym kontenerze. Jest to często używane, gdy kontener jest używany jako czysty magazyn klucz-wartość bez konieczności stosowania indeksów pomocniczych. Może również ułatwić przyspieszenie operacji wstawiania zbiorczego.

## <a name="including-and-excluding-property-paths"></a>Uwzględnianie i wykluczanie ścieżek właściwości

Niestandardowe zasady indeksowania mogą określać ścieżki właściwości, które są jawnie dołączone lub wykluczone z indeksowania. Przez optymalizację liczby indeksowanych ścieżek można obniżyć ilość miejsca używanego przez kontener i poprawić opóźnienia operacji zapisu. Te ścieżki są zdefiniowane po [metodzie opisanej w sekcji Omówienie indeksowania](index-overview.md#from-trees-to-property-paths) z następującymi dodatkami:

- ścieżka prowadząca do wartości skalarnej (ciąg lub Number) ma koniec`/?`
- elementy z tablicy są rozłączone za pośrednictwem `/[]` notacji ( `/0`zamiast `/1` itp.)
- `/*` symbol wieloznaczny może służyć do dopasowania elementów poniżej węzła

Ponowne wykonanie tego samego przykładu:

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

- `headquarters`ścieżka jest`employees``/headquarters/employees/?`
- `locations` "`country` ścieżka jest`/locations/[]/country/?`
- ścieżka do wszystkich elementów w `headquarters` obszarze to`/headquarters/*`

Jeśli ścieżka jest jawnie dołączona do zasad indeksowania, należy również określić, które typy indeksów mają być stosowane do tej ścieżki i dla każdego typu indeksu, do których ma zastosowanie ten indeks:

| Typ indeksu | Dozwolone docelowe typy danych |
| --- | --- |
| Zakres | Ciąg lub cyfra |
| Przestrzenne | Point, LineString lub Wielokąt |

`/headquarters/employees/?` Na przykład możemy dołączyć ścieżkę i określić `Range` , że indeks ma być stosowany do tej ścieżki dla obu `String` i `Number` wartości.

### <a name="includeexclude-strategy"></a>Strategia uwzględniania/wykluczania

Każda zasada indeksowania musi zawierać ścieżkę `/*` katalogu głównego jako dołączoną lub wykluczoną ścieżkę.

- Uwzględnij ścieżkę główną, aby wykluczać ścieżki, które nie muszą być indeksowane. Jest to zalecane podejście, które pozwala Azure Cosmos DB aktywnie indeksować wszelkie nowe właściwości, które mogą zostać dodane do modelu.
- Wyklucz ścieżkę katalogu głównego do selektywnego dołączania ścieżek, które muszą być indeksowane.

- W przypadku ścieżek ze zwykłymi znakami, które zawierają: znaki alfanumeryczne i _ (podkreślenie), nie trzeba określać ciągu ścieżki wokół podwójnych cudzysłowów (na przykład "/Path/?"). W przypadku ścieżek z innymi znakami specjalnymi należy wypróbować ciąg ścieżki wokół podwójnych cudzysłowów (na przykład\""/Path\"-ABC/?"). Jeśli oczekujesz znaków specjalnych w ścieżce, możesz pominąć wszystkie ścieżki w celu zapewnienia bezpieczeństwa. Funkcjonalnie nie ma żadnej różnicy, jeśli wykorzystasz wszystkie ścieżki, a tylko te, które zawierają znaki specjalne.

- Właściwość systemowa "ETag" jest domyślnie wykluczona z indeksowania, chyba że element ETag zostanie dodany do ścieżki dołączonej do indeksowania.

Zobacz [tę sekcję](how-to-manage-indexing-policy.md#indexing-policy-examples) , aby zapoznać się z przykładami zasad indeksowania.

## <a name="composite-indexes"></a>Indeksy złożone

Zapytanie, `ORDER BY` że co najmniej dwie właściwości wymagają indeksu złożonego. Obecnie indeksy złożone są wykorzystywane wyłącznie przez wiele `ORDER BY` zapytań. Domyślnie nie są zdefiniowane żadne indeksy złożone, dlatego należy [dodać indeksy złożone](how-to-manage-indexing-policy.md#composite-indexing-policy-examples) zgodnie z wymaganiami.

Podczas definiowania indeksu złożonego należy określić:

- Co najmniej dwie ścieżki właściwości. Sekwencja, w której są zdefiniowane ścieżki właściwości.
- Zamówienie (rosnąco lub malejąco).

Podczas używania indeksów złożonych są używane następujące zagadnienia:

- Jeśli złożone ścieżki indeksu nie pasują do sekwencji właściwości w klauzuli ORDER BY, a następnie indeks złożony nie obsługuje zapytania

- Kolejność ścieżek indeksu złożonego (rosnąco lub malejąco) powinna również być zgodna z kolejnością w klauzuli ORDER BY.

- Indeks złożony również obsługuje klauzulę ORDER BY z odwrotną kolejnością we wszystkich ścieżkach.

Rozważmy następujący przykład, w którym indeks złożony jest zdefiniowany w właściwościach a, b i c:

| **Indeks złożony**     | **Przykładowe `ORDER BY` zapytanie**      | **Obsługiwane przez indeks?** |
| ----------------------- | -------------------------------- | -------------- |
| ```(a asc, b asc)```         | ```ORDER BY  a asc, b asc```        | ```Yes```            |
| ```(a asc, b asc)```          | ```ORDER BY  b asc, a asc```        | ```No```             |
| ```(a asc, b asc)```          | ```ORDER BY  a desc, b desc```      | ```Yes```            |
| ```(a asc, b asc)```          | ```ORDER BY  a asc, b desc```       | ```No```             |
| ```(a asc, b asc, c asc)``` | ```ORDER BY  a asc, b asc, c asc``` | ```Yes```            |
| ```(a asc, b asc, c asc)``` | ```ORDER BY  a asc, b asc```        | ```No```            |

Zasady indeksowania należy dostosować, aby można było obsłużyć wszystkie `ORDER BY` niezbędne zapytania.

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

## <a name="obsolete-attributes"></a>Przestarzałe atrybuty

Podczas pracy z zasadami indeksowania mogą wystąpić następujące atrybuty, które są obecnie przestarzałe:

- `automatic`jest wartością logiczną zdefiniowaną w elemencie głównym zasad indeksowania. Ta wartość jest teraz ignorowana i można ją `true`ustawić na wartość, gdy używane narzędzie go wymaga.
- `precision`jest liczbą zdefiniowaną na poziomie indeksu dla dołączonych ścieżek. Ta wartość jest teraz ignorowana i można ją `-1`ustawić na wartość, gdy używane narzędzie go wymaga.
- `hash`jest rodzajem indeksu, który jest obecnie zastępowany przez rodzaj zakresu.

## <a name="next-steps"></a>Kolejne kroki

Więcej informacji na temat indeksowania znajdziesz w następujących artykułach:

- [Omówienie indeksowania](index-overview.md)
- [Jak zarządzać zasadami indeksowania](how-to-manage-indexing-policy.md)
