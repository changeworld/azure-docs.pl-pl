---
title: Indeksowanie w usłudze Azure Cosmos DB
description: Dowiedz się, jak działa indeksowanie w usłudze Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/10/2018
ms.author: mjbrown
ms.openlocfilehash: c0525cfba16fb61f8388ae4d6a693be3bb71674c
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/14/2018
ms.locfileid: "51629068"
---
# <a name="indexing-in-azure-cosmos-db"></a>Indeksowanie w usłudze Azure Cosmos DB

Usługa Azure Cosmos DB jest niezależny od schematów bazą danych i pozwala szybko iterować w swojej aplikacji bez konieczności zarządzania schematami lub indeksami. Domyślnie usługa Azure Cosmos DB automatycznie indeksuje wszystkie elementy w kontenerze bez konieczności schematu lub indeksów pomocniczych, od deweloperów.

## <a name="items-as-trees"></a>Elementy jako drzewa

Przy wyświetlaniu elementów w kontenerze jako dokumenty JSON i przedstawiania ich jako drzewa, usługi Azure Cosmos DB normalizuje struktury i wartości wystąpienia różnych elementów do ujednolicenie koncepcji **dynamicznie zakodowany w strukturze ścieżki** . W tej reprezentacji każdej etykiety w dokumencie JSON, która zawiera nazwy właściwości i ich wartości staje się węzła drzewa. Pozostawia drzewa zawierają rzeczywiste wartości i pośredniego węzły zawierają informacje o schemacie. Poniższy obraz przedstawia drzew utworzone dla dwóch elementów (1 i 2) w kontenerze:

![Reprezentacja drzewa dla dwóch różnych elementów w kontenerze usługi Azure Cosmos](./media/index-overview/indexing-as-tree.png)

Węzeł główny pseudo jest tworzony jako element nadrzędny w węzłach rzeczywiste odpowiadający etykiet w dokumencie poniżej. Struktury danych zagnieżdżonych dysku hierarchii drzewa. Pośredni sztuczne węzły z etykietą przy użyciu wartości liczbowych (na przykład, 0, 1,...) są wykorzystywane do reprezentowania wyliczeń i tablicy wskaźników.

## <a name="index-paths"></a>Indeks ścieżki

Usługa Azure Cosmos DB projektów elementów jako dokumenty JSON i indeksu jako drzewa. Następnie można dostroić zasady dla ścieżki w drzewie. Istnieje możliwość dołączania lub wykluczania ścieżki z indeksowania. To może oferować zapisu ulepszoną wydajność i zmniejszyć magazyn indeksów dla scenariuszy, w którym wzorców zapytań są znane wyprzedzeniem. Aby dowiedzieć się więcej, zobacz [ścieżki indeksu](index-paths.md).

## <a name="indexing-under-the-hood"></a>Indeksowanie: Kulisy

Bazy danych Cosmos Azure ma zastosowanie, automatycznego indeksowania danych, w której jest indeksowana każdej ścieżce w drzewie, o ile nie skonfigurowano wykluczać określone ścieżki.

Usługi Azure Cosmos, którą baza danych wykorzystuje odwrócony indeksu struktury danych do przechowywania informacji o poszczególnych elementów i ułatwienia reprezentacji wydajne dla zapytań. Drzewo indeksu jest dokument, który jest konstruowany przy użyciu sumę wszystkich drzew reprezentujących poszczególnych elementów w kontenerze. W drzewie indeks zwiększa rozmiar wraz z upływem czasu nowe elementy są dodane lub zaktualizowane istniejące elementy w kontenerze. W przeciwieństwie do indeksowania relacyjnej bazy danych, usługi Azure Cosmos DB nie ponownie uruchomić indeksowanie od podstaw, ponieważ wprowadzono nowe pola, nowe elementy są dodawane do istniejącej struktury. 

Każdy węzeł w drzewie indeksu jest wpisu indeksu, zawierające wartości etykiety i pozycji o nazwie terminu i identyfikatory elementów o nazwie wpisy. Wpisy w nawiasach klamrowych (na przykład {1,2}) na rysunku odwróconą indeksu odnoszą się do elementów, takich jak dokument1 i Document2 zawierający wartość danego etykiety. Ważne domniemanie, w jednolity sposób traktowania zarówno schematu, etykiet, jak i wartości wystąpienia to wszystko, co jest pakowany wewnątrz duży indeks. Wartość wystąpienia, która jest nadal liści nie jest powtarzany, może być w różne role dla elementów, za pomocą innego schematu, etykiet, ale jest tę samą wartość. Na poniższej ilustracji przedstawiono odwróconą indeksowania dla różnych elementów:

![Indeksowanie kulisy, odwrócony indeksu](./media/index-overview/inverted-index.png)

> [!NOTE]
> Odwróconą indeks może wyglądać podobnie do indeksowania struktur, używane w aparatu wyszukiwania w domenie pobierania informacji. Azure Cosmos DB przy użyciu tej metody umożliwia wyszukiwanie bazy danych dla każdego elementu, niezależnie od jego strukturę schematu.

Ścieżka znormalizowana indeks koduje do przodu ścieżki od katalogu głównego dla wartości, oraz informacje o typie wartości. Ścieżka i wartości są kodowane zapewnienie różnego rodzaju indeksowania, np. zakres, typów przestrzennych. Kodowanie wartość służy do Podaj unikatową wartość lub w skład zestawu ścieżek.

## <a name="querying-with-indexes"></a>Wykonywanie zapytania dla indeksów

Indeks odwróconą umożliwia zapytaniom identyfikowanie dokumentów, szybko zgodne w predykacie zapytania. Przez traktowanie schematu i wystąpienia wartości równomiernie pod względem ścieżki, odwrócony indeks znajduje się również drzewa. W związku z tym, indeksu i wyniki można szeregować do prawidłowy dokument JSON, a zwracany jako same dokumenty zwrócone w reprezentacji drzewa. Ta metoda umożliwia recursing za pośrednictwem wyników dla dodatkowych zapytań. Na poniższym obrazie przedstawiono przykład indeksowania w zapytaniu punkt:  

![Przykład zapytania punktu](./media/index-overview/index-point-query.png)

Zakres kwerendy GermanTax jest wykonywane w ramach przetwarzania zapytań funkcji zdefiniowanej przez użytkownika. Funkcja zdefiniowana przez użytkownika jest wszelkie zarejestrowane, funkcja języka Javascript, zapewniające zaawansowanych funkcji logiki programowania zintegrowane w zapytaniu. Na poniższym obrazie przedstawiono przykład indeksowania w zapytaniu zakresu:

![Przykład zapytania zakresu](./media/index-overview/index-range-query.png)

## <a name="next-steps"></a>Kolejne kroki

Więcej informacji na temat indeksowania w następujących artykułach:

- [Zasady indeksowania](index-policy.md)
- [Typy indeksu](index-types.md)
- [Indeks ścieżki](index-paths.md)
- [Jak zarządzać zasad indeksowania](how-to-manage-indexing-policy.md)