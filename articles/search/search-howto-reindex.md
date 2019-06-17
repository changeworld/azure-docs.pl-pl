---
title: Ponowne kompilowanie indeksu usługi Azure Search, lub Odśwież zawartość do przeszukiwania — usługa Azure Search
description: Dodawanie nowych elementów, a następnie zaktualizować istniejące elementy lub dokumentów lub usuwanie przestarzałych dokumentów w ponownej pełnej kompilacji lub częściowe Indeksowanie przyrostowe, w celu odświeżenia indeksu usługi Azure Search.
services: search
author: HeidiSteen
manager: cgronlun
ms.service: search
ms.topic: conceptual
ms.date: 02/13/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 2595912732389c8a415d1854a84a7b9c182e4dc7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60871152"
---
# <a name="how-to-rebuild-an-azure-search-index"></a>Jak odbudować indeksu usługi Azure Search

W tym artykule wyjaśniono, jak odbudować indeksu usługi Azure Search, okoliczności, w których są wymagane ponowne kompilowanie i zalecenia dotyczące eliminacji wpływ ponowne kompilowanie dla żądań zapytań trwających.

A *odbudować* odnosi się do porzucania i ponownego tworzenia struktury danych fizycznych skojarzonych z indeksu, w tym wszystkie pola na podstawie odwróconą indeksy. W usłudze Azure Search nie można porzucić i ponownie utwórz poszczególne pola. Do ponownego utworzenia indeksu, należy usunąć wszystkie magazyny pola, ponownie utworzony na podstawie istniejących lub poprawionego schematu indeksu, a następnie wypełniona przy użyciu przeniesiony do indeksu i pobierane z zewnętrznych źródeł danych. Często służąca do reorganizacji indeksów w czasie projektowania, ale również może być konieczne odbudowanie indeksu poziomie produkcyjnym w celu uwzględnienia zmian strukturalnych, takich jak dodawanie typów złożonych lub Dodawanie pola do sugestory.

W przeciwieństwie do ponowne kompilowanie przyjmujące indeksu w trybie offline *odświeżania danych* działa jako zadanie w tle. Możesz dodawać, Usuń i Zamień dokumenty przy minimalnym zakłóceniu obciążeń związanych z zapytaniami, mimo że wykonywanie zapytań zwykle trwa dłużej. Aby uzyskać więcej informacji na temat aktualizowania indeksowanie zawartości, zobacz [Add, Update lub usuwanie dokumentów](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents).

## <a name="rebuild-conditions"></a>Ponownie skompiluj warunków

| Warunek | Opis |
|-----------|-------------|
| Zmiana definicji pola | Zmiana nazwy pola, typ danych lub określonych [atrybutami indeksu](https://docs.microsoft.com/rest/api/searchservice/create-index) (wyszukiwanie, filtrowanie, sortowanie, tworzenie aspektów) wymaga ponownej pełnej kompilacji. |
| Przypisz Analizator do pola | [Analizatory](search-analyzers.md) są zdefiniowane w indeksie, a następnie przypisywane do pól. W dowolnym momencie można dodać nową definicję analizatora do indeksu, ale można je tylko *przypisać* analizator po utworzeniu pola. Dotyczy to zarówno **analizatora** i **indexAnalyzer** właściwości. **SearchAnalyzer** właściwość jest wyjątek (można przypisać tę właściwość do istniejącego pola). |
| Aktualizowanie lub usuwanie definicji analyzer w ramach indeksu | Nie można usunąć ani zmieniać istniejącej analizator konfiguracji (analizator, tokenizatora, filtr tokenu lub char filtr) w indeksie, chyba, że odbudować całego indeksu. |
| Dodaj pole do sugestora | Jeśli pole już istnieje i chcesz dodać ją do [Sugestory](index-add-suggesters.md) konstruowania, należy odbudować indeksu. |
| Usuń pole | Aby fizycznie Usuń wszystkie ślady pola, musisz odbudować indeksu. Po bezpośrednim ponowna kompilacja nie jest możliwe, można modyfikować kodu aplikacji, aby wyłączyć dostęp do pola "usunięta". Fizycznie definicję pola i zawartość pozostają w indeksie aż do następnego ponowną kompilację, po zastosowaniu schematu, które pomija pola w danym. |
| Przełącznik warstwy | Jeśli potrzebujesz większej pojemności, nie istnieje żadne uaktualnienia w miejscu. Nowa usługa jest tworzony w nowym punkcie pojemności, a indeksy muszą zostać skompilowane od podstaw w nowej wersji usługi. |

Inne zmiany można wprowadzić bez wpływu na istniejących struktur fizycznych. Ściślej mówiąc, wykonaj następujące zmiany *nie* wymagają odbudowywanie indeksu:

+ Dodawanie nowego pola
+ Ustaw **pobieranie** atrybutu istniejącego pola
+ Ustaw **searchAnalyzer** na istniejącego pola
+ Dodaj nową definicję analyzer w ramach indeksu
+ Dodawanie, aktualizowanie lub usuwanie profile oceniania
+ Dodawanie, aktualizowanie lub usuwanie ustawień specyfikacji CORS
+ Dodawanie, aktualizowanie lub usuwanie synonymMaps

Po dodaniu nowego pola, istniejących dokumentów indeksowanych podano wartość null dla nowego pola. Podczas odświeżania danych w przyszłości wartości z zewnętrznego źródła danych, Zastąp wartości null, dodawane przez usługę Azure Search. Aby uzyskać więcej informacji na temat aktualizowania indeksowanie zawartości, zobacz [Add, Update lub usuwanie dokumentów](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents).

## <a name="partial-or-incremental-indexing"></a>Indeksowanie częściowego lub przyrostowe

W usłudze Azure Search nie możesz kontrolować indeksowanie poszczególnych pól, wybierając Usuń lub Utwórz ponownie określonych pól. Podobnie, nie istnieje wbudowany mechanizm dla [indeksować dokumenty na podstawie kryteriów](https://stackoverflow.com/questions/40539019/azure-search-what-is-the-best-way-to-update-a-batch-of-documents). Wszelkie wymagania dotyczące indeksowania oparte na kryteria muszą zostać spełnione poprzez kod niestandardowy.

Co można zrobić, jest jednak *Odśwież dokumenty* w indeksie. W przypadku wielu rozwiązań wyszukiwania zewnętrzne źródło danych jest nietrwały, a synchronizacja między źródłem danych i indeksu wyszukiwania jest powszechną praktyką. W kodzie, wywołania [Add, Update lub usuwanie dokumentów](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) operacji lub [odpowiednik .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.indexesoperationsextensions.createorupdate?view=azure-dotnet) można zaktualizować indeksu zawartości lub aby dodać wartości dla nowego pola.

## <a name="partial-indexing-with-indexers"></a>Partial indeksowania przy użyciu indeksatorów

[Indeksatory](search-indexer-overview.md) upraszcza zadanie odświeżania danych. Indeksator może indeksowanie tylko jedną tabelę lub widok w zewnętrznym źródle danych. Indeksowanie wiele tabel, najprostszą metodą jest utworzenie widoku, który łączy projektów i tabele kolumny, które mają być indeksowane. 

Korzystając z indeksatorów, które przeszukują zewnętrznych źródeł danych, sprawdź, czy kolumna "znacznik limitu górnego" w danych źródłowych. Jeśli istnieje, służy do zmiany przyrostowe wykrywania przez pobieranie tylko te wiersze zawierające nowy lub poprawiony zawartości. Aby uzyskać [usługi Azure Blob storage](search-howto-indexing-azure-blob-storage.md#incremental-indexing-and-deletion-detection), `lastModified` pole jest używane. Na [usługi Azure Table storage](search-howto-indexing-azure-tables.md#incremental-indexing-and-deletion-detection), `timestamp` pełni tę samą funkcję. Podobnie, zarówno [indeksator usługi Azure SQL Database](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#capture-new-changed-and-deleted-rows) i [indeksator usługi Azure Cosmos DB](search-howto-index-cosmosdb.md#indexing-changed-documents) mają pola Oflagowanie aktualizacji wiersza. 

Aby uzyskać więcej informacji na temat indeksatorów, zobacz [omówienie indeksatorów](search-indexer-overview.md) i [zresetować indeksatora interfejsu API REST](https://docs.microsoft.com/rest/api/searchservice/reset-indexer).

## <a name="how-to-rebuild-an-index"></a>Jak odbudować indeksu

Plan w pełni częste, ponownie kompiluje podczas tworzenia active, gdy indeks schematy są w stanie strumienia. W przypadku aplikacji już w środowisku produkcyjnym zaleca się utworzenie nowego indeksu równolegle działającą istniejący indeks, aby uniknąć przestoju zapytania.

Uprawnienia odczytu i zapisu na poziomie usługi są wymagane do aktualizacji indeksu. 

Nie można odbudować indeksu w portalu. Programowo, można wywołać [aktualizacji indeksu — interfejs API REST](https://docs.microsoft.com/rest/api/searchservice/update-index) lub [równoważnych interfejsów API platformy .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.iindexesoperations.createorupdatewithhttpmessagesasync?view=azure-dotnet) dla pełnej rekompilacji. Żądanie aktualizacji indeksu jest taka sama jak [utworzyć indeks interfejsu API REST](https://docs.microsoft.com/rest/api/searchservice/create-index), ale ma inny kontekst.

Poniższy przepływ pracy jest ukierunkowane pod kątem interfejsu API REST, ale informacje dotyczą zestawu SDK platformy .NET.

1. Podczas ponownego wykorzystania, nazwę indeksu [Usuń istniejący indeks](https://docs.microsoft.com/rest/api/searchservice/delete-index). 

   Wszelkie zapytania dla tego indeksu, od razu są opuszczane. Usuwanie indeksu jest nieodwracalne niszczenie magazynu fizycznego dla kolekcji pól i innych konstrukcji. Upewnij się, że jesteś wyczyść na temat skutków usuwania indeksu, zanim ją upuścisz. 

2. Formułowanie [Aktualizowanie indeksu](https://docs.microsoft.com/rest/api/searchservice/update-index) żądanie do punktu końcowego usługi, klucz interfejsu API i [klucz administratora](https://docs.microsoft.com/azure/search/search-security-api-keys). Klucz administratora jest wymagana dla operacji zapisu.

3. W treści żądania Udostępnij schematu indeksu na definicje pól zmienione lub zmodyfikowane. Treść żądania zawiera schemat indeksu, a także tworzy do oceniania, profile, analizatory, sugestory i opcje CORS. Wymagania dotyczące schematu są udokumentowane w artykule [Create Index](https://docs.microsoft.com/rest/api/searchservice/create-index).

4. Wyślij [Aktualizowanie indeksu](https://docs.microsoft.com/rest/api/searchservice/update-index) żądania odbudować fizycznych wyrażenie indeksu w usłudze Azure Search. 

5. [Ładowanie indeksu z dokumentami](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) ze źródła zewnętrznego.

Podczas tworzenia indeksu magazynu fizycznego jest przydzielany dla każdego pola w schemacie indeksu z odwróconą Indeks utworzony dla każdego pola, którą można przeszukiwać. Pola, które nie znajdują się wyszukiwanie mogą być używane w filtry lub wyrażeń, ale nie mają odwrócona, indeksy i czy nie pełnotekstowego lub rozmyte przeszukiwania. Na odbudowywanie indeksu usunąć i ponownie utworzyć te indeksy odwróconą na podstawie schematu indeksu, należy podać.

Podczas ładowania indeks każdego pola odwróconą indeks został wypełniony wszystkie wyrazy unikatowy, tokenami z poszczególnych dokumentów, map do dokumentu odpowiednich identyfikatorów. Na przykład podczas indeksowania zestawu danych hotele, odwrócony Indeks utworzony dla pola miejscowości może zawierać warunki dla Seattle, Portland i tak dalej. Dokumenty, które zawierają Seattle lub Portland w polu Miasto musi ich identyfikator dokumentu, na liście obok termin. W przypadku dowolnego [Dodawanie, aktualizowanie lub usuwanie](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) operacji i Lista identyfikatorów dokumentów są odpowiednio aktualizowane.

> [!NOTE]
> Jeśli rygorystyczne wymagania umowy SLA, można rozważyć aprowizacji nową usługę w szczególności dla tej pracy, programować za i indeksowania, pojawiają się w pełną izolację od indeksu produkcji. Osobna usługa działa na swój własny sprzęt, eliminując możliwości rywalizacji o zasoby. Po zakończeniu tworzenia będzie albo pozostanie nowego indeksu w miejscu, przekierowywanie zapytań do nowego punktu końcowego i indeks lub należy uruchomić kod zakończenia publikowania poprawione indeksu w usłudze Azure Search, oryginalnym. Nie istnieje obecnie mechanizm przenoszenia indeksu gotowych do użycia do innej usługi.

## <a name="view-updates"></a>Wyświetl aktualizacje

Możesz rozpocząć wykonywanie zapytania dotyczącego indeksu, jak tylko pierwszy dokument jest ładowany. Jeśli znasz identyfikator dokumentu [interfejsu API REST dokumentu wyszukiwania](https://docs.microsoft.com/rest/api/searchservice/lookup-document) zwraca określonego dokumentu. Do testowania w szerszym powinien poczekaj, aż indeks jest w pełni załadowane i następnie użyj zapytania, aby sprawdzić kontekstu, które powinny być widoczne.

## <a name="see-also"></a>Zobacz także

+ [Omówienie indeksatora](search-indexer-overview.md)
+ [Indeks dużych zestawów danych na dużą skalę](search-howto-large-index.md)
+ [Indeksowanie w portalu](search-import-data-portal.md)
+ [Indeksator usługi Azure SQL Database](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
+ [Indeksator usługi Azure Cosmos DB](search-howto-index-cosmosdb.md)
+ [Indeksator usługi Azure Blob Storage](search-howto-indexing-azure-blob-storage.md)
+ [Indeksator usługi Azure Table Storage](search-howto-indexing-azure-tables.md)
+ [Zabezpieczenia w usłudze Azure Search](search-security-overview.md)