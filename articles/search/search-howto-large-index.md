---
title: Odbuduj usługi Azure Search index lub odświeżania można wyszukiwać zawartość | Dokumentacja firmy Microsoft
description: Dodawanie nowych elementów, zaktualizować istniejące elementy lub dokumenty lub usuwanie przestarzałych dokumentów w ponownej pełnej kompilacji lub częściowe przyrostowe indeksowania do odświeżenia indeksu usługi Azure Search.
services: search
author: HeidiSteen
manager: cgronlun
ms.service: search
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: heidist
ms.openlocfilehash: cb99096c1217fca1527b17946dc12390ddf3f62c
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34655713"
---
# <a name="how-to-scale-out-indexing-in-azure-seearch"></a>Sposób skalowania w poziomie indeksowania w Azure Seearch

Wzrostu ilości danych lub przetwarzania zmieniających się potrzeb, może się okazać, że proste [odbudowuje i ponowne indeksowanie zadania](search-howto-reindex.md) nie są wystarczająco. 

Pierwszym krokiem kierunku zwiększone zapotrzebowanie, zaleca się zwiększenie [skalowanie i pojemność](search-capacity-planning.md) w granicach istniejącą usługę. 

Drugi etap, jeśli można użyć [indeksatory](search-indexer-overview.md), dodaje mechanizmy skalowalnych indeksowania. Indeksatory® mają wbudowane harmonogramu, która umożliwia zbiorczym limit indeksowania w regularnych odstępach czasu lub rozszerzyć przetwarzania poza okno 24-godzinnym. Ponadto podczas parowania z definicji źródła danych, indeksatorów pomagają osiągnąć formularza równoległości partycjonowanie danych i używając harmonogramów można wykonać jednocześnie.

### <a name="scheduled-indexing-for-large-data-sets"></a>Zaplanowane indeksowania dla dużych zestawów danych

Jest ważne mechanizm dla przetwarzania dużych zestawów danych i analiz działa wolno, takich jak analiza obrazu w potoku kognitywnych wyszukiwania. Przetwarzanie indeksatora działa w 24-godzinnym przedziale czasu. Jeśli przetwarzanie kończy się niepowodzeniem, aby zakończyć w ciągu 24 godzin, zachowania planowania indeksatora pracować korzyści. 

Zgodnie z projektem zaplanowane uruchamia indeksowania w określonych odstępach czasu, za pomocą zadania, kończenie zwykle przed wznowieniem w następnym zaplanowanym interwale. Jednak jeśli przetwarzanie nie zakończy się w przedziale czasu, indeksatora zatrzymuje (ponieważ zabrakło czasu). W następnym interwale czasowym wznawia przetwarzania, którym ostatnio przerwał, o zachowywaniu systemu śledzenia gdzie występuje. 

W praktyce, w przypadku obciążeń indeksu spanning kilka dni możesz umieścić indeksatora zgodnie z harmonogramem 24-godzinnym. Podczas indeksowania wznawia dla następnego stint 24-godzinnym, uruchamia ponownie w ostatnich znanych dobrej dokumentu. W ten sposób indeksatora może współpracować sposób zaległości dokumentu przez szereg dni, dopóki wszystkie dokumenty nieprzetworzone są przetwarzane. Aby uzyskać więcej informacji na temat tej metody, zobacz [indeksowania dużych zestawów danych](search-howto-indexing-azure-blob-storage.md#indexing-large-datasets)

<a name="parallel-indexing"></a>

## <a name="parallel-indexing"></a>Indeksowanie równoległych

Aby skonfigurować równoległego indeksowania strategii jest drugi wybór. W przypadku nietypowy praktyce znacznym indeksowania wymagania, na przykład Rozpoznawania na zeskanowane dokumenty w potoku kognitywnych wyszukiwania równoległego indeksowania strategii może być odpowiednie podejście do określonego celu. W potoku wzbogacenia kognitywnych wyszukiwania analizy obrazu i przetwarzania języka naturalnego jest długo działające. Równoległe indeksowania od usługi, która nie obsługuje jednocześnie żądań zapytań może być wygodną opcją do pracy nad dużą porcję powolna przetwarzania zawartości. 

Strategii w celu równoległego przetwarzania ma następujące elementy:

+ Podzielenie źródła danych między wiele kontenerów lub wielu wirtualnych folderów wewnątrz tego samego kontenera. 
+ Mapowania poszczególnych mini zestawu danych, aby [źródła Data](https://docs.microsoft.com/rest/api/searchservice/create-data-source), sparowanym na własnym [indeksatora](https://docs.microsoft.com/rest/api/searchservice/create-indexer).
+ Kognitywnych wyszukiwania odwoływać się takie same [skillset](https://docs.microsoft.com/rest/api/searchservice/create-skillset) w każdej definicji indeksatora.
+ Zapis do tego samego indeksu wyszukiwania programu docelowego. 
+ Planowanie wszystkich indeksatorów w tym samym czasie.

> [!Note]
> Usługa Azure Search nie obsługuje przypisywanie replik partycji lub do konkretnych obciążeń. Ryzyko duże równoczesnych indeksowania jest nadmiernego obciążania systemu szkody wydajności zapytania. Jeśli masz środowisko testowe, wdrożenie równoległe indeksowania najpierw zrozumieć skutków ubocznych.

## <a name="configure-parallel-indexing"></a>Konfigurowanie indeksowania równoległych

Dla indeksatorów wydajności przetwarzania jest luźno na podstawie jednego podsystemu indeksatora dla każdej jednostki usługi (SU) używany przez usługi wyszukiwania. Wiele równoczesnych indeksatorów są możliwe w usługach Azure Search udostępniane w warstwach Basic lub Standard, o co najmniej dwie repliki. 

1. W [portalu Azure](https://portal.azure.com), na pulpicie nawigacyjnym usługi wyszukiwania **omówienie** strony, sprawdź **warstwa cenowa** o potwierdzenie może obsłużyć, równoległe indeksowania. Zarówno Basic i Standard warstwy oferują wiele replik.

2. W **ustawienia** > **skali**, [zwiększenia replik](search-capacity-planning.md) celu równoległego przetwarzania: jedna replika dodatkowych dla poszczególnych obciążeń indeksatora. Pozostaw wystarczającą ilość dla istniejącego woluminu zapytania. Ograniczania obciążeń zapytania do indeksowania nie jest dobrym zależnościami.

3. Dystrybucja danych do wielu kontenerów na poziomie, który indeksatory usługi Azure Search może nawiązać połączenie. Może to być wiele tabel w bazie danych SQL Azure, wiele kontenerów w magazynie obiektów Blob platformy Azure lub wieloma kolekcjami. Zdefiniuj jeden obiekt źródła danych dla każdej tabeli lub kontenera.

4. Tworzenie i Planowanie wielu indeksatory równolegle:

   + Przykładowa usługi za pomocą sześć replik. Skonfiguruj sześciu indeksatorów, każdą z nich zamapowane do źródła danych zawierającego szóstego jednego zestawu danych dla 6 sposób podziału całego zestawu danych. 

   + Wskazuje indeks tej samej każdego indeksatora. Do tej samej skillset obciążeń kognitywnych wyszukiwania wskazują każdego indeksatora.

   + W ramach każdej definicji indeksatora zaplanować tego samego wzorca wykonywania środowiska wykonawczego. Na przykład `"schedule" : { "interval" : "PT8H", "startTime" : "2018-05-15T00:00:00Z" }` tworzy harmonogram na 2018-05-15 na wszystkich indeksatorów, uruchomiony w odstępach czasu ośmiu godzin.

W zaplanowanym terminie wszystkie indeksatory rozpocząć wykonywania, podczas ładowania danych, stosowanie wzbogacenia (Jeśli skonfigurowano potoku wyszukiwania kognitywnych) i zapisywanie do indeksu. Usługa Azure Search nie blokuje indeksu dla aktualizacji. Równoczesnych zapisów są zarządzane z ponów próbę, jeżeli zapisu konkretnym kończy się niepowodzeniem przy pierwszej próbie.

> [!Note]
> Gdy zwiększyć repliki, należy rozważyć zwiększenie liczby partycji, jeśli jest zaprojektowana znacznie zwiększyć rozmiar indeksu. Partycje przechowywania wycinków indeksowana zawartość; masz więcej partycji, ma mniejszy wycinek każdego z nich do przechowywania.

## <a name="see-also"></a>Zobacz także

+ [Omówienie indeksatora](search-indexer-overview.md)
+ [Indeksowanie w portalu](search-import-data-portal.md)
+ [Indeksator bazy danych SQL Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
+ [Indeksator usługi Azure Cosmos DB](search-howto-index-cosmosdb.md)
+ [Indeksator usługi Azure Blob Storage](search-howto-indexing-azure-blob-storage.md)
+ [Indeksator usługi Azure Table Storage](search-howto-indexing-azure-tables.md)
+ [Zabezpieczeń w usłudze Azure Search](search-security-overview.md)