---
title: Usługa Azure Cosmos DB zasady indeksowania
description: Dowiedz się, jak skonfigurować i zmienić ustawienie domyślne zasady dla automatycznego indeksowania i większą wydajność w usłudze Azure Cosmos DB indeksowania.
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/14/2019
ms.author: thweiss
ms.openlocfilehash: 3f19668cc4fb4f4f4a900c157aa79de83ad1b79b
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/17/2019
ms.locfileid: "67163722"
---
# <a name="indexing-policies-in-azure-cosmos-db"></a>Zasady indeksowania w usłudze Azure Cosmos DB

W usłudze Azure Cosmos DB każdy kontener ma zasady indeksowania, który określa, jak powinno być indeksowane elementy kontenera. Domyślnych zasad indeksowania dla nowo utworzony indeksy kontenery dla każdej właściwości każdego elementu, wymuszanie zakresem indeksów dla dowolnego ciągu lub liczby, i Indeksy przestrzenne dla dowolnego obiektu GeoJSON wpisz punkt. Dzięki temu można uzyskać wysoką wydajność zapytań bez myśleć o indeksowania i zarządzania indeksami ponoszonych z góry kosztów.

W niektórych sytuacjach można zmienić to zachowanie automatyczne do własnych wymagań. Można dostosować zasady indeksowania kontenera, ustawiając jego *indeksowania tryb*i Dołącz lub Wyklucz *ścieżki właściwości*.

> [!NOTE]
> Metoda aktualizacji zasad indeksowania opisanych w tym artykule dotyczy tylko do bazy danych SQL Azure Cosmos DB (rdzenie) interfejsu API.

## <a name="indexing-mode"></a>Tryb indeksowania

Usługa Azure Cosmos DB obsługuje dwa tryby indeksowania:

- **Spójne**: Jeśli zasady indeksowania kontenera jest ustawiony na spójność, indeks jest aktualizowany synchronicznie, jak tworzenie, aktualizowanie lub usuwanie elementów. Oznacza to, że spójność odczytu zapytań będzie [spójności skonfigurowany dla konta](consistency-levels.md).

- **Brak**: Jeśli zasady indeksowania kontenera jest ustawiona na wartość None, indeksowanie jest efektywnie wyłączona w danym kontenerze. Jest to często używane, gdy kontener jest używana jako czysty parach klucz wartość bez konieczności indeksów pomocniczych. Może również pomóc, przyspieszanie zbiorcze operacje wstawiania.

## <a name="including-and-excluding-property-paths"></a>Uwzględnianie i wykluczanie właściwości ścieżki

Niestandardowe zasady indeksowania można określić ścieżki właściwości, które są jawnie dołączone lub wykluczone z indeksowania. Optymalizując liczba ścieżek, które są indeksowane, można zmniejszyć ilość miejsca używanego przez kontener i zmniejszaj opóźnienia operacji zapisu. Te ścieżki są zdefiniowane następujące [metody opisanej w sekcji Przegląd indeksowania](index-overview.md#from-trees-to-property-paths) z następującymi dodatkami:

- Ścieżka prowadząca do wartość skalarną (ciąg lub liczba) kończy się ciągiem `/?`
- elementy tablicy są adresowane ze sobą za pośrednictwem `/[]` notacji (zamiast `/0`, `/1` itp.)
- `/*` symbol wieloznaczny może służyć do dopasowania wszelkie elementy poniżej węzła

W powyższym przykładzie ten sam ponownie:

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

- `headquarters`firmy `employees` ścieżka `/headquarters/employees/?`
- `locations`" `country` ścieżka `/locations/[]/country/?`
- Ścieżka do niczego w obszarze `headquarters` jest `/headquarters/*`

Gdy ścieżka jest jawnie uwzględniony w zasady indeksowania, ma również określenie, jakie typy indeksu powinny być stosowane do tej ścieżki, jak i dla każdego typu indeksu, typ danych, ten indeks ma zastosowanie do:

| Typ indeksu | Dozwolone typy danych elementów docelowych |
| --- | --- |
| Zakres | Ciąg lub liczba |
| Przestrzenne | Punkt, LineString lub wielokąta |

Na przykład firma Microsoft może obejmować `/headquarters/employees/?` ścieżki i określić, że `Range` indeksu powinny być stosowane w tej ścieżce dla obu `String` i `Number` wartości.

### <a name="includeexclude-strategy"></a>Uwzględnianie/wykluczanie strategii

Wszystkie zasady indeksowania musi zawierać ścieżkę katalogu głównego `/*` jako dołączone lub wykluczone ścieżki.

- Podaj ścieżkę katalogu głównego selektywnie wykluczyć ścieżek, które nie muszą być indeksowane. Jest to zalecane podejście, ponieważ umożliwia ona aktywnie indeksu nowej właściwości, które mogą zostać dodane do modelu usługi Azure Cosmos DB.
- Wyklucz ścieżka katalogu głównego, aby uwzględnić ścieżki, które muszą zostać pomyślnie zindeksowane.

- Dla ścieżki na zwykłe znaki, które obejmują: znaki alfanumeryczne i _ (podkreślenie), nie trzeba wprowadzić ciąg ścieżki w całym podwójnego cudzysłowu (na przykład, "/ path /?"). Ścieżek przy użyciu innych znaków specjalnych, musisz wprowadzić ciąg ścieżki w całym podwójnych cudzysłowów (na przykład, "/\"abc ścieżki\"/?"). Jeśli oczekujesz znaki specjalne w ścieżce można udosłownić każdej ścieżce dla bezpieczeństwa. Funkcjonalnie go nie wprowadzać wszelkie różnice ucieczki każdej ścieżce Vs tylko tych, które zawierają znaki specjalne.

Zobacz [w tej sekcji](how-to-manage-indexing-policy.md#indexing-policy-examples) Przykłady zasad indeksowania.

## <a name="composite-indexes"></a>Indeksy złożone

Wysyła kwerendę, która `ORDER BY` dwóch lub więcej właściwości wymagają indeksie złożonym. Obecnie indeksy złożone są wykorzystywane tylko przez wielu `ORDER BY` zapytania. Domyślnie żadne indeksy złożone są zdefiniowane, zaleca się [dodawać indeksy złożone](how-to-manage-indexing-policy.md#composite-indexing-policy-examples) zgodnie z potrzebami.

Podczas definiowania indeksu złożonego, należy określić:

- Co najmniej dwóch ścieżki właściwości. Kolejność, w którym właściwość ścieżki są definiowane jest ważna.
- Kolejność (rosnąco lub malejąco).

Następujące kwestie są używane, gdy przy użyciu indeksów złożonych:

- Jeśli ścieżki indeksu złożonego pasuje do sekwencji właściwości w klauzuli ORDER BY, następnie indeksie złożonym nie może obsługiwać zapytania

- Kolejność ścieżek indeksu złożonego (rosnący lub malejący) również powinna odpowiadać kolejność w klauzuli ORDER BY.

- Indeks złożony obsługuje również klauzuli ORDER BY w kolejności przeciwnej we wszystkich ścieżkach.

Rozważmy następujący przykład, w którym jeden indeks jest zdefiniowany we właściwościach, b i c:

| **Indeks złożony**     | **Przykładowe `ORDER BY` zapytania**      | **Obsługiwane przez indeks?** |
| ----------------------- | -------------------------------- | -------------- |
| ```(a asc, b asc)```         | ```ORDER BY  a asc, b asc```        | ```Yes```            |
| ```(a asc, b asc)```          | ```ORDER BY  b asc, a asc```        | ```No```             |
| ```(a asc, b asc)```          | ```ORDER BY  a desc, b desc```      | ```Yes```            |
| ```(a asc, b asc)```          | ```ORDER BY  a asc, b desc```       | ```No```             |
| ```(a asc, b asc, c asc)``` | ```ORDER BY  a asc, b asc, c asc``` | ```Yes```            |
| ```(a asc, b asc, c asc)``` | ```ORDER BY  a asc, b asc```        | ```No```            |

Należy dostosować zasady indeksowania, więc może obsługiwać wszystkie niezbędne `ORDER BY` zapytania.

## <a name="modifying-the-indexing-policy"></a>Modyfikowanie zasad indeksowania

W dowolnym momencie można zaktualizować zasad indeksowania kontenera [przy użyciu witryny Azure portal lub jednego z obsługiwanych zestawów SDK](how-to-manage-indexing-policy.md). Aktualizacja zasad indeksowania wyzwala transformacji z indeksu starej na nową jest wykonywane online, a w miejscu (aby nie dodatkowego miejsca do magazynowania jest używany podczas operacji). Indeks stare zasady efektywnie jest przekształcana do nowych zasad bez wywierania wpływu na dostępność zapisu lub przepływnością aprowizowaną dla kontenera. Przekształcenie indeksu jest operacją asynchroniczną, a czas potrzebny do ukończenia zależy od aprowizowanej przepływności, liczby elementów i ich rozmiaru. 

> [!NOTE]
> Podczas ponownego indeksowania, jest w toku, zapytania mogą nie zwracać pasujących wyników i zrobi to bez zwracania błędów. Oznacza to, że wyniki zapytania może nie być spójna ukończenie przekształcania indeksu. Istnieje możliwość śledzić postęp przekształcania indeksu [przy użyciu jednego z zestawów SDK](how-to-manage-indexing-policy.md).

Jeśli nowe zasady indeksowania tryb jest ustawiony na spójność, nie inne zmiany zasad indeksowania mogą być stosowane w trakcie przekształcenia indeksu. Uruchamianie transformację indeksu można anulować, ustawiając zasady indeksowania tryb Brak (co natychmiast będzie się zmniejszać indeks).

## <a name="indexing-policies-and-ttl"></a>Zasady indeksowania i czas wygaśnięcia

[Czas wygaśnięcia (TTL) funkcja](time-to-live.md) wymaga indeksowanie będzie aktywny w kontenerze jest włączona. Oznacza to, że:

- nie jest możliwe aktywować TTL w kontenerze, w których tryb indeksowania ma wartość None,
- nie jest możliwe ustawić tryb indeksowania None w kontenerze, w których czas wygaśnięcia jest aktywowana.

W scenariuszach, gdzie ma właściwość ścieżki musi zostać pomyślnie zindeksowane, ale wymagane jest czas wygaśnięcia można użyć zasad indeksowania, przy użyciu:

- Tryb indeksowania, ustawiony na spójność, oraz
- Brak dołączone ścieżki i
- `/*` jako tylko wykluczone ścieżki.

## <a name="obsolete-attributes"></a>Przestarzałe atrybutów

Podczas pracy z zasady indeksowania, mogą wystąpić następujące atrybuty, które obecnie są przestarzałe:

- `automatic` wartość logiczna zdefiniowano w katalogu głównym zasad indeksowania. Teraz jest ignorowany i może być równa `true`, gdy narzędzie używasz go wymaga.
- `precision` jest liczbą zdefiniowany na poziomie indeksu dla dołączone ścieżki. Teraz jest ignorowany i może być równa `-1`, gdy narzędzie używasz go wymaga.
- `hash` jest rodzajem indeksu, która została zastąpiona rodzaj zakresu.

## <a name="next-steps"></a>Kolejne kroki

Więcej informacji na temat indeksowania znajdziesz w następujących artykułach:

- [Omówienie indeksowania](index-overview.md)
- [Jak zarządzać zasad indeksowania](how-to-manage-indexing-policy.md)
