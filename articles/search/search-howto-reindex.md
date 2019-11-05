---
title: Kompiluj ponownie indeks Wyszukiwanie poznawcze platformy Azure
titleSuffix: Azure Cognitive Search
description: Dodaj nowe elementy, zaktualizuj istniejące elementy lub dokumenty lub Usuń przestarzałe dokumenty z pełnego przebudowy lub częściowego indeksowania przyrostowego, aby odświeżyć indeks Wyszukiwanie poznawcze platformy Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 1301ee2494aa24720905b9e3e68edce04fb17e72
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73466447"
---
# <a name="how-to-rebuild-an-azure-cognitive-search-index"></a>Jak skompilować indeks Wyszukiwanie poznawcze platformy Azure

W tym artykule wyjaśniono, jak ponownie skompilować indeks usługi Azure Wyszukiwanie poznawcze, sytuacje, w których są wymagane ponowne kompilacje oraz Zalecenia dotyczące łagodzenia wpływu rekompilacji na bieżące żądania zapytań.

*Odbudowanie* odwołuje się do porzucania i ponownego tworzenia fizycznych struktur danych skojarzonych z indeksem, w tym wszystkich odwróconych indeksów opartych na polach. W usłudze Azure Wyszukiwanie poznawcze nie można porzucić i ponownie utworzyć poszczególnych pól. Aby ponownie skompilować indeks, należy usunąć wszystkie magazyny pól, utworzyć je ponownie na podstawie istniejącego lub zmienionego schematu indeksu, a następnie ponownie wypełnić dane wypchnięte do indeksu lub pobrać ze źródeł zewnętrznych. Często można ponownie skompilować indeksy podczas opracowywania, ale może być również konieczne odbudowanie indeksu poziomu produkcyjnego w celu uwzględnienia zmian strukturalnych, takich jak dodawanie typów złożonych lub dodawanie pól do sugestii.

W przeciwieństwie do rekompilacji, które mają indeks w trybie offline, *odświeżanie danych* jest uruchamiane jako zadanie w tle. Można dodawać, usuwać i zamieniać dokumenty z minimalnymi zakłóceniami w celu wykonywania zapytań, chociaż zapytania zazwyczaj trwają dłużej. Aby uzyskać więcej informacji na temat aktualizowania zawartości indeksu, zobacz [Dodawanie, aktualizowanie lub usuwanie dokumentów](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents).

## <a name="rebuild-conditions"></a>Warunki ponownego kompilowania

| Warunek | Opis |
|-----------|-------------|
| Zmiana definicji pola | Zmiana nazwy pola, typu danych lub określonych [atrybutów indeksu](https://docs.microsoft.com/rest/api/searchservice/create-index) (wyszukiwanie, filtrowanie, sortowanie, tworzenie i tworzenie) wymaga pełnej kompilacji. |
| Przypisz Analizator do pola | [Analizatory](search-analyzers.md) są zdefiniowane w indeksie, a następnie przypisywane do pól. W dowolnym momencie możesz dodać nową definicję analizatora do indeksu, ale można *przypisać* Analizator tylko po utworzeniu pola. Dotyczy to zarówno **analizatora** , jak i właściwości **indexAnalyzer** . Właściwość **searchAnalyzer** jest wyjątkiem (można przypisać tę właściwość do istniejącego pola). |
| Aktualizowanie lub usuwanie definicji analizatora w indeksie | Nie można usunąć ani zmienić istniejącej konfiguracji analizatora (analizator, tokenizatora, filtr tokenu lub filtr znaków) w indeksie, chyba że cały indeks zostanie odbudowany. |
| Dodawanie pola do sugestii | Jeśli pole już istnieje i chcesz dodać je do konstrukcji [sugerującej](index-add-suggesters.md) , należy ponownie skompilować indeks. |
| Usuń pole | Aby fizycznie usunąć wszystkie ślady pola, należy ponownie skompilować indeks. Gdy bezpośrednie ponowne kompilowanie nie jest praktyczne, można zmodyfikować kod aplikacji, aby wyłączyć dostęp do pola "usunięte". Fizycznie definicja pola i zawartość pozostają w indeksie do momentu kolejnej ponownej kompilacji, gdy zastosuje schemat, który pomija pole w danym momencie. |
| Przełącz warstwy | Jeśli potrzebujesz większej pojemności, w Azure Portal nie ma żadnego uaktualnienia w miejscu. Należy utworzyć nową usługę, a indeksy muszą być kompilowane od podstaw w nowej usłudze. Aby ułatwić automatyzację tego procesu, możesz użyć przykładowego kodu **index-Backup-Restore** w ramach tego [przykładowego repozytorium platformy Azure wyszukiwanie poznawcze .NET](https://github.com/Azure-Samples/azure-search-dotnet-samples). Ta aplikacja spowoduje utworzenie kopii zapasowej indeksu w serii plików JSON, a następnie ponowne utworzenie indeksu w określonej usłudze wyszukiwania.|

Wszelkie inne modyfikacje mogą być wprowadzane bez wpływu na istniejące struktury fizyczne. W szczególnych przypadkach następujące zmiany nie *wymagają* odbudowania indeksu:

+ Dodaj nowe pole
+ Ustaw atrybut możliwy do **pobierania** dla istniejącego pola
+ Ustaw **searchAnalyzer** dla istniejącego pola
+ Dodaj nową definicję analizatora w indeksie
+ Dodawanie, aktualizowanie lub usuwanie profilów oceniania
+ Dodawanie, aktualizowanie lub usuwanie ustawień mechanizmu CORS
+ Dodawanie, aktualizowanie lub usuwanie synonymMaps

Po dodaniu nowego pola, istniejące indeksowane dokumenty otrzymują wartość null dla nowego pola. W przypadku późniejszego odświeżenia danych wartości z zewnętrznych danych źródłowych zastąpią wartości null dodane przez Wyszukiwanie poznawcze platformy Azure. Aby uzyskać więcej informacji na temat aktualizowania zawartości indeksu, zobacz [Dodawanie, aktualizowanie lub usuwanie dokumentów](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents).

## <a name="partial-indexing"></a>Częściowe indeksowanie

Na platformie Azure Wyszukiwanie poznawcze nie można kontrolować indeksowania dla poszczególnych pól, a także usuwać lub tworzyć ponownie określone pola. Podobnie nie istnieje wbudowany mechanizm [indeksowania dokumentów na podstawie kryteriów](https://stackoverflow.com/questions/40539019/azure-search-what-is-the-best-way-to-update-a-batch-of-documents). Wszelkie wymagania dotyczące indeksowania opartego na kryteriach muszą być spełnione przez kod niestandardowy.

Jednak można to łatwo zrobić, *odświeżając dokumenty* w indeksie. W przypadku wielu rozwiązań wyszukiwania zewnętrzne dane źródłowe są nietrwałe, a synchronizacja między danymi źródłowymi a indeksem wyszukiwania jest powszechną procedurą. W kodzie, wywołaj operację [Dodawanie, aktualizowanie lub usuwanie dokumentów](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) lub [równoważnej oprogramowaniu .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.indexesoperationsextensions.createorupdate?view=azure-dotnet) , aby zaktualizować zawartość indeksu lub dodać wartości dla nowego pola.

## <a name="partial-indexing-with-indexers"></a>Częściowe indeksowanie za pomocą indeksatorów

[Indeksatory](search-indexer-overview.md) upraszczają zadanie odświeżania danych. Indeksator może indeksować tylko jedną tabelę lub widok w zewnętrznym źródle danych. Aby indeksować wiele tabel, najprostszym podejściem jest utworzenie widoku, który sprzęga tabele i projekty kolumn, które mają być indeksowane. 

W przypadku używania indeksatorów, które przeszukują zewnętrzne źródła danych, należy sprawdzić, czy w danych źródłowych znajduje się kolumna "górny znacznik". Jeśli taki istnieje, można go użyć do wykrywania zmian przyrostowych przez pobranie tylko tych wierszy zawierających nową lub poprawioną zawartość. W przypadku [usługi Azure Blob Storage](search-howto-indexing-azure-blob-storage.md#incremental-indexing-and-deletion-detection)używane jest pole `lastModified`. W [usłudze Azure Table storage](search-howto-indexing-azure-tables.md#incremental-indexing-and-deletion-detection)`timestamp` służy do tego samego celu. Podobnie zarówno [Azure SQL Database Indexer](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#capture-new-changed-and-deleted-rows) , Azure Cosmos DB jak i [indeksator](search-howto-index-cosmosdb.md#indexing-changed-documents) mają pola do oflagowania aktualizacji wierszy. 

Aby uzyskać więcej informacji na temat indeksatorów, zobacz [Omówienie indeksatora](search-indexer-overview.md) i [Resetowanie interfejsu API REST indeksatora](https://docs.microsoft.com/rest/api/searchservice/reset-indexer).

## <a name="how-to-rebuild-an-index"></a>Jak ponownie skompilować indeks

Planuj częste, pełne ponowne kompilacje podczas aktywnego programowania, gdy schematy indeksów są w stanie strumieniowym. W przypadku aplikacji znajdujących się już w środowisku produkcyjnym zalecamy utworzenie nowego indeksu, który jest uruchamiany obok istniejącego indeksu, aby uniknąć przestojów zapytań.

Uprawnienia do odczytu i zapisu na poziomie usługi są wymagane na potrzeby aktualizacji indeksu. 

Nie można odbudować indeksu w portalu. Programowo można wywołać [interfejs API REST indeksu aktualizacji](https://docs.microsoft.com/rest/api/searchservice/update-index) lub [równoważne interfejsy API platformy .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.iindexesoperations.createorupdatewithhttpmessagesasync?view=azure-dotnet) na potrzeby pełnej kompilacji. Żądanie indeksu aktualizacji jest identyczne, aby można było [utworzyć interfejs API REST indeksu](https://docs.microsoft.com/rest/api/searchservice/create-index), ale ma inny kontekst.

Następujący przepływ pracy jest rozliczany z interfejsem API REST, ale stosuje się jednakowo do zestawu .NET SDK.

1. Podczas ponownego używania nazwy indeksu [Porzuć istniejący indeks](https://docs.microsoft.com/rest/api/searchservice/delete-index). 

   Wszystkie zapytania ukierunkowane na ten indeks zostaną natychmiast usunięte. Usuwanie indeksu jest nieodwracalne, co niszczy magazyn fizyczny dla kolekcji pól i innych konstrukcji. Pamiętaj, aby usunąć indeks przed jego usunięciem. 

2. Formułowanie żądania [indeksu aktualizacji](https://docs.microsoft.com/rest/api/searchservice/update-index) przy użyciu punktu końcowego usługi, klucza API i [klucza administratora](https://docs.microsoft.com/azure/search/search-security-api-keys). Dla operacji zapisu wymagany jest klucz administratora.

3. W treści żądania Podaj schemat indeksu ze zmienionym lub zmodyfikowanym definicją pola. Treść żądania zawiera schemat indeksu, a także konstrukcje dotyczące profilów oceniania, analizatorów, sugestii i opcji CORS. Wymagania schematu są udokumentowane w temacie [create index](https://docs.microsoft.com/rest/api/searchservice/create-index).

4. Wyślij żądanie [indeksu aktualizacji](https://docs.microsoft.com/rest/api/searchservice/update-index) w celu odbudowania fizycznego wyrażenia indeksu w usłudze Azure wyszukiwanie poznawcze. 

5. [Załaduj indeks z dokumentami](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) ze źródła zewnętrznego.

Podczas tworzenia indeksu magazyn fizyczny jest przypisywany dla każdego pola w schemacie indeksu z odwróconym indeksem utworzonym dla każdego pola z możliwością wyszukiwania. Pola, które nie są możliwe do wyszukiwania, mogą być używane w filtrach lub wyrażeniach, ale nie mają odwróconych indeksów i nie są pełnotekstowe ani rozmyte. W przypadku ponownego kompilowania indeksu te odwrócone indeksy są usuwane i ponownie tworzone na podstawie pożądanego schematu indeksu.

Po załadowaniu indeksu, odwrócony indeks każdego pola jest wypełniany wszystkimi unikatowymi wyrazami tokenów z każdego dokumentu, z mapą odpowiadającymi identyfikatorami dokumentów. Na przykład podczas indeksowania zestawu danych hoteli, odwrócony Indeks utworzony dla pola miasto może zawierać terminy dla Seattle, Portland i tak dalej. Dokumenty, które zawierają Seattle lub Portland w polu miasto, będą miały swój identyfikator dokumentu na liście wraz z terminem. Na każdej operacji [dodawania, aktualizowania lub usuwania](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) zostanie odpowiednio zaktualizowana lista warunki i identyfikator dokumentu.

> [!NOTE]
> Jeśli masz rygorystyczne wymagania umowy SLA, możesz rozważyć zainicjowanie obsługi nowej usługi przeznaczonej dla tej pracy, z uwzględnieniem tworzenia i indeksowania w pełnej izolacji z poziomu indeksu produkcyjnego. Oddzielna usługa jest uruchamiana na własnym sprzęcie, eliminując wszelką możliwość rywalizacji o zasoby. Po zakończeniu opracowywania należy pozostawić nowy indeks, przekierować zapytania do nowego punktu końcowego i indeksu lub uruchomić gotowy kod, aby opublikować poprawiony indeks w oryginalnej usłudze Azure Wyszukiwanie poznawcze. Obecnie nie ma mechanizmu przechodzenia gotowego do użycia indeksu do innej usługi.

## <a name="view-updates"></a>Wyświetl aktualizacje

Możesz rozpocząć wykonywanie zapytań względem indeksu zaraz po załadowaniu pierwszego dokumentu. Jeśli znasz identyfikator dokumentu, [interfejs API REST dokumentu wyszukiwania](https://docs.microsoft.com/rest/api/searchservice/lookup-document) zwraca określony dokument. W celu szerszego testowania należy zaczekać, aż indeks zostanie całkowicie załadowany, a następnie użyć zapytań w celu sprawdzenia kontekstu, który powinien być widoczny.

## <a name="see-also"></a>Zobacz też

+ [Omówienie indeksatora](search-indexer-overview.md)
+ [Indeksowanie dużych zestawów danych na dużą skalę](search-howto-large-index.md)
+ [Indeksowanie w portalu](search-import-data-portal.md)
+ [Azure SQL Database indeksator](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
+ [Indeksator usługi Azure Cosmos DB](search-howto-index-cosmosdb.md)
+ [Indeksator usługi Azure Blob Storage](search-howto-indexing-azure-blob-storage.md)
+ [Indeksator usługi Azure Table Storage](search-howto-indexing-azure-tables.md)
+ [Zabezpieczenia w usłudze Azure Wyszukiwanie poznawcze](search-security-overview.md)