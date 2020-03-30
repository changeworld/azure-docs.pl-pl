---
title: Indeks dużych zbiorów danych przy użyciu wbudowanych indeksatorów
titleSuffix: Azure Cognitive Search
description: Strategie indeksowania dużych danych lub indeksowania intensywnie korzystającego z danych obliczeniowych w trybie wsadowym, pozyskiwanie zasobów i techniki indeksowania zaplanowanego, równoległego i rozproszonego.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/17/2019
ms.openlocfilehash: 4ad5e961e390b60784355ff3bc72aca4a2f73e11
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77190963"
---
# <a name="how-to-index-large-data-sets-in-azure-cognitive-search"></a>Jak indeksować duże zestawy danych w usłudze Azure Cognitive Search

W miarę wzrostu ilości danych lub zmiany potrzeb przetwarzania mogą się okazać, że proste lub domyślne strategie indeksowania nie są już praktyczne. W przypadku usługi Azure Cognitive Search istnieje kilka metod mieszczania większych zestawów danych, począwszy od sposobu struktury żądania przekazywania danych, po używanie indeksatora specyficznego dla źródła dla zaplanowanych i rozproszonych obciążeń.

Te same techniki dotyczą również długotrwałych procesów. W szczególności kroki opisane w [indeksowaniu równoległym](#parallel-indexing) są pomocne w indeksowaniu intensywnym obliczeniowo, takim jak analiza obrazu lub przetwarzanie języka naturalnego w [potoku wzbogacania AI.](cognitive-search-concept-intro.md)

W poniższych sekcjach przedstawiono trzy techniki indeksowania dużych ilości danych.

## <a name="option-1-pass-multiple-documents"></a>Opcja 1: Przekazywanie wielu dokumentów

Jednym z najprostszych mechanizmów indeksowania większego zestawu danych jest przesyłanie wielu dokumentów lub rekordów w jednym żądaniu. Tak długo, jak cały ładunek jest poniżej 16 MB, żądanie może obsłużyć do 1000 dokumentów w operacji przekazywania zbiorczego. Te limity mają zastosowanie niezależnie od tego, czy używasz [interfejsu API Dodaj dokumenty REST,](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) czy metody [Indeks](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions.index?view=azure-dotnet) w pliku .NET SDK. Dla obu interfejsów API należy spakować 1000 dokumentów w treści każdego żądania.

Indeksowanie wsadowe jest implementowane dla poszczególnych żądań przy użyciu REST lub .NET lub za pośrednictwem indeksatorów. Kilka indeksatorów działa w różnych granicach. W szczególności indeksowanie obiektów Blob platformy Azure ustawia rozmiar partii na 10 dokumentów w rozpoznawaniu większego średniego rozmiaru dokumentu. W przypadku indeksatorów opartych na [interfejsie API REST programu Create Indexer](https://docs.microsoft.com/rest/api/searchservice/Create-Indexer)można ustawić argument, `BatchSize` aby dostosować to ustawienie tak, aby lepiej odpowiadało właściwościom danych. 

> [!NOTE]
> Aby zachować rozmiar dokumentu w dół, należy unikać dodawania danych niepodjęwalnych do indeksu. Obrazy i inne dane binarne nie są bezpośrednio przeszukiwalne i nie powinny być przechowywane w indeksie. Aby zintegrować dane nierozpytowalne z wynikami wyszukiwania, należy zdefiniować pole niesiewalne na przeszukiwanie, które przechowuje odwołanie do adresu URL zasobu.

## <a name="option-2-add-resources"></a>Opcja 2: Dodawanie zasobów

Usługi, które są aprowizowane w jednej z [standardowych warstw cenowych](search-sku-tier.md) często mają niedostatecznie pojemność zarówno dla magazynu, jak i obciążeń (kwerend lub indeksowania), co sprawia, że [zwiększenie partycji i replik liczy](search-capacity-planning.md) oczywiste rozwiązanie dla pomieścić większe zestawy danych. Aby uzyskać najlepsze wyniki, potrzebujesz zarówno zasobów: partycje do przechowywania i repliki do pracy pozyskiwania danych.

Zwiększenie replik i partycji są zdarzeniami podlegającymi rozliczaniu, które zwiększają koszt, ale jeśli nie są stale indeksowania przy maksymalnym obciążeniu, można dodać skalę na czas trwania procesu indeksowania, a następnie dostosować poziomy zasobów z powrotem w dół po indeksowaniu jest Gotowy.

## <a name="option-3-use-indexers"></a>Opcja 3: Użyj indeksatorów

[Indeksatory](search-indexer-overview.md) są używane do indeksowania obsługiwanych źródeł danych platformy Azure dla zawartości z wyszukuj. Chociaż nie jest specjalnie przeznaczony do indeksowania na dużą skalę, kilka możliwości indeksatora są szczególnie przydatne do przyjmowania większych zestawów danych:

+ Harmonogramy umożliwiają rozstawienie indeksowania w regularnych odstępach czasu, dzięki czemu można je rozłożyć w czasie.
+ Zaplanowane indeksowanie można wznowić w ostatnim znanym punkcie zatrzymania. Jeśli źródło danych nie jest w pełni indeksowane w ciągu okna 24-godzinnego, indeksator wznowi indeksowanie drugiego dnia w dowolnym miejscu, w którym zostało przerwane.
+ Partycjonowanie danych na mniejsze poszczególne źródła danych umożliwia przetwarzanie równoległe. Można podzielić dane źródłowe na mniejsze składniki, takie jak na wiele kontenerów w magazynie obiektów Blob platformy Azure, a następnie utworzyć odpowiednie, wiele [obiektów źródła danych](https://docs.microsoft.com/rest/api/searchservice/create-data-source) w usłudze Azure Cognitive Search, które mogą być indeksowane równolegle.

> [!NOTE]
> Indeksatory są specyficzne dla źródła danych, więc przy użyciu metody indeksatora jest opłacalne tylko dla wybranych źródeł danych na platformie Azure: [Baza danych SQL](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md), magazyn obiektów [Blob](search-howto-indexing-azure-blob-storage.md), [magazyn tabel](search-howto-indexing-azure-tables.md), [Usługi Cosmos DB](search-howto-index-cosmosdb.md).

### <a name="scheduled-indexing"></a>Zaplanowane indeksowanie

Planowanie indeksatora jest ważnym mechanizmem przetwarzania dużych zestawów danych, a także powolnych procesów, takich jak analiza obrazu w potoku wyszukiwania poznawczego. Przetwarzanie indeksatora działa w ciągu 24 godzin. Jeśli przetwarzanie zakończy się niepowodzeniem w ciągu 24 godzin, zachowania planowania indeksatora może działać na twoją korzyść. 

Zgodnie z projektem zaplanowane indeksowanie rozpoczyna się w określonych odstępach czasu, a zadanie zwykle kończy się przed wznowieniem w następnym zaplanowanym interwale. Jednak jeśli przetwarzanie nie zakończy się w ramach interwału, indeksator zatrzymuje się (ponieważ zabrakło czasu). W następnym interwale przetwarzania wznawia, gdzie ostatnio zostało przerwane, z systemem śledzenie, gdzie to nastąpi. 

W praktyce dla obciążeń indeksu obejmujących kilka dni można umieścić indeksatora w harmonogramie 24-godzinnym. Podczas indeksowania wznawia dla następnego cyklu 24-godzinny, uruchamia się ponownie w ostatnim znanym dobrym dokumencie. W ten sposób indeksator może pracować swoją drogę przez zaległości dokumentu przez szereg dni, aż wszystkie nieprzetwochane dokumenty są przetwarzane. Aby uzyskać więcej informacji na temat tego podejścia, zobacz [Indeksowanie dużych zestawów danych w magazynie obiektów Blob platformy Azure.](search-howto-indexing-azure-blob-storage.md#indexing-large-datasets) Aby uzyskać więcej informacji na temat ustawiania harmonogramów w ogóle, zobacz [Tworzenie interfejsu API REST indeksatora](https://docs.microsoft.com/rest/api/searchservice/Create-Indexer) lub zobacz Jak [zaplanować indeksatory dla usługi Azure Cognitive Search](search-howto-schedule-indexers.md).

<a name="parallel-indexing"></a>

### <a name="parallel-indexing"></a>Indeksowanie równoległe

Strategia indeksowania równoległego opiera się na indeksowaniu wielu źródeł danych w zgodzie, gdzie każda definicja źródła danych określa podzbiór danych. 

W przypadku nierutynowych, intensywnie korzystających z obliczeń wymagań indeksowania , takich jak OCR na zeskanowanych dokumentach w potoku wyszukiwania poznawczego, analizie obrazu lub przetwarzaniu języka naturalnego , strategia indeksowania równoległego jest często właściwym podejściem do ukończenia długotrwałego procesu w jak najkrótszym czasie. Jeśli można wyeliminować lub zmniejszyć żądania kwerend, indeksowanie równoległe w usłudze, która nie obsługuje jednocześnie zapytań jest najlepszym rozwiązaniem strategii do pracy za pośrednictwem dużej części zawartości powolnego przetwarzania. 

Przetwarzanie równoległe ma następujące elementy:

+ Podziel dane źródłowe między wiele kontenerów lub wiele folderów wirtualnych wewnątrz tego samego kontenera. 
+ Mapuj każdy mini zestaw danych do własnego [źródła danych,](https://docs.microsoft.com/rest/api/searchservice/create-data-source)sparowany z własnym [indeksatorem.](https://docs.microsoft.com/rest/api/searchservice/create-indexer)
+ W przypadku wyszukiwania poznawczego odwołaj się do tego samego [zestawu umiejętności](https://docs.microsoft.com/rest/api/searchservice/create-skillset) w każdej definicji indeksatora.
+ Zapisz w tym samym indeksie wyszukiwania docelowego. 
+ Zaplanuj uruchamianie wszystkich indeksatorów w tym samym czasie.

> [!NOTE]
> W usłudze Azure Cognitive Search nie można przypisać poszczególnych replik lub partycji do indeksowania lub przetwarzania zapytań. System określa sposób wykorzystania zasobów. Aby zrozumieć wpływ na wydajność kwerendy, można spróbować indeksowania równoległego w środowisku testowym przed wprowadzeniem go do środowiska produkcyjnego.  

### <a name="how-to-configure-parallel-indexing"></a>Jak skonfigurować indeksowanie równoległe

W przypadku indeksatorów pojemność przetwarzania jest luźno oparta na jednym podsystemie indeksatora dla każdej jednostki serwisowej (SU) używanej przez usługę wyszukiwania. Wiele równoczesnych indeksatorów są możliwe w usługach Azure Cognitive Search aprowizowanych w warstwach podstawowych lub standardowych posiadających co najmniej dwie repliki. 

1. W [witrynie Azure portal](https://portal.azure.com), na stronie przegląd pulpitu **nawigacyjnego** usługi wyszukiwania, sprawdź **warstwę cen,** aby potwierdzić, że może pomieścić indeksowanie równoległe. Warstwy podstawowe i standardowe oferują wiele replik.

2. W**skalowaniu** **ustawień** >  [zwiększ liczbę replik](search-capacity-planning.md) do przetwarzania równoległego: jedną dodatkową replikę dla każdego obciążenia indeksatora. Pozostaw wystarczającą liczbę dla istniejącego woluminu kwerendy. Poświęcanie obciążeń zapytań do indeksowania nie jest dobrym kompromisem.

3. Rozesłaj dane do wielu kontenerów na poziomie, do który mogą dotrzeć indeksatory usługi Azure Cognitive Search. Może to być wiele tabel w usłudze Azure SQL Database, wiele kontenerów w magazynie obiektów Blob platformy Azure lub wiele kolekcji. Zdefiniuj jeden obiekt źródła danych dla każdej tabeli lub kontenera.

4. Tworzenie i planowanie wielu indeksatorów do uruchamiania równolegle:

   + Załóżmy, że usługa z sześciu replik. Skonfiguruj sześć indeksatorów, z których każdy jest mapowany na źródło danych zawierające jedną szóstą zestawu danych dla 6-drożnego podziału całego zestawu danych. 

   + Punkt każdy indeksator do tego samego indeksu. W przypadku obciążeń wyszukiwania poznawczego wskaż każdemu indeksatorowi ten sam zestaw umiejętności.

   + W ramach każdej definicji indeksatora zaplanuj ten sam wzorzec wykonywania w czasie wykonywania. Na przykład `"schedule" : { "interval" : "PT8H", "startTime" : "2018-05-15T00:00:00Z" }` tworzy harmonogram 2018-05-15 na wszystkich indeksatorów, uruchomiony w odstępach ośmiu godzin.

W zaplanowanym czasie wszystkie indeksatory rozpoczynają wykonywanie, ładowanie danych, stosowanie wzbogacenia (jeśli skonfigurowano potok wyszukiwania poznawczego) i zapisywanie do indeksu. Usługa Azure Cognitive Search nie blokuje indeksu dla aktualizacji. Zapisy równoczesnych są zarządzane, z ponów spróbuj, jeśli określonego zapisu nie powiedzie się przy pierwszej próbie.

> [!Note]
> Podczas zwiększania replik, należy rozważyć zwiększenie liczby partycji, jeśli rozmiar indeksu jest przewidywane znacznie zwiększyć. Partycje przechowują wycinki zawartości indeksowane; im więcej partycji masz, tym mniejszy wycinek każdy z nich ma do przechowywania.

## <a name="see-also"></a>Zobacz też

+ [Omówienie indeksatora](search-indexer-overview.md)
+ [Indeksowanie w portalu](search-import-data-portal.md)
+ [Indeksator bazy danych SQL platformy Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
+ [Indeksator usługi Azure Cosmos DB](search-howto-index-cosmosdb.md)
+ [Indeksator usługi Azure Blob Storage](search-howto-indexing-azure-blob-storage.md)
+ [Indeksator usługi Azure Table Storage](search-howto-indexing-azure-tables.md)
+ [Zabezpieczenia w usłudze Azure Cognitive Search](search-security-overview.md)
