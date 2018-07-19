---
title: Ponownie skompiluj usługi Azure Search zawartość do przeszukiwania indeksu lub odświeżania | Dokumentacja firmy Microsoft
description: Dodawanie nowych elementów, a następnie zaktualizować istniejące elementy lub dokumentów lub usuwanie przestarzałych dokumentów w ponownej pełnej kompilacji lub częściowe Indeksowanie przyrostowe, w celu odświeżenia indeksu usługi Azure Search.
services: search
author: HeidiSteen
manager: cgronlun
ms.service: search
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: heidist
ms.openlocfilehash: 0dd7a5d5159144c6b1a050ff4c0443b181976738
ms.sourcegitcommit: b9786bd755c68d602525f75109bbe6521ee06587
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/18/2018
ms.locfileid: "39124958"
---
# <a name="how-to-scale-out-indexing-in-azure-search"></a>Jak skalowalnego w poziomie indeksowanie w usłudze Azure Search

Wzrostu ilości danych lub przetwarzania zmienią się potrzeby, może się okazać, że prosty [ponowne kompilowanie i indeksowanie zadań](search-howto-reindex.md) nie są wystarczające. 

Jako pierwszy krok w kierunku spotkania zwiększone zapotrzebowanie, zaleca się zwiększenie [skalowalność i wydajność](search-capacity-planning.md) w granicach istniejącej usługi. 

Drugi etap, jeśli użyjesz [indeksatory](search-indexer-overview.md), dodaje mechanizmy skalowalnych indeksowania. Indeksatory pochodzą z wbudowanych harmonogram, który pozwala na działka limit indeksowania w regularnych odstępach czasu lub rozszerzyć przetwarzanie ponad 24-godzinnego przedziału czasu. Ponadto w połączeniu z definicji źródła danych, indeksatorów pomóc Ci osiągnąć formularza równoległości, partycjonowanie danych i używając harmonogramów można wykonać równolegle.

### <a name="scheduled-indexing-for-large-data-sets"></a>Zaplanowane indeksowania dla dużych zestawów danych

Planowanie jest ważnym mechanizmem do przetwarzania dużych zestawów danych i analiz wolno działające, takich jak analiza obrazu w usłudze wyszukiwania poznawczego potoku. Indeksator przetwarzania działa w okresie 24-godzinnym. Jeśli przetwarzanie zakończy się, aby zakończyć w ciągu 24 godzin, wykorzystaj pracować zachowania planowania indeksatora. 

Zgodnie z projektem należy zaplanować indeksowanie rozpoczyna się w określonych odstępach czasu, za pomocą zadania zazwyczaj ukończenie przed wznowieniem w następnym zaplanowanym interwale. Jednak jeśli przetwarzanie zakończy się w przedziale czasu, indeksator przestaje (ponieważ zabrakło czasu). W następnym interwale czasowym wznawia przetwarzania tam, gdzie ją ostatnia przerwaliśmy, za pomocą zachowania systemu prowadnicy gdzie występuje. 

W praktyce, w przypadku obciążeń indeksu obejmujące kilka dni można umieścić indeksator zgodnie z harmonogramem 24-godzinnym. Podczas indeksowania wznawia dla następnego firmą 24-godzinnego, uruchamia ponownie w ostatnich znanych dokumentu dobre. W ten sposób indeksatora pracować jego sposób za pomocą zaległości dokumentu przez szereg dni, po których przetwarzane są wszystkie nieprzetworzone dokumenty. Aby uzyskać więcej informacji na temat tego podejścia, zobacz [indeksowania dużych zestawów danych](search-howto-indexing-azure-blob-storage.md#indexing-large-datasets)

<a name="parallel-indexing"></a>

## <a name="parallel-indexing"></a>Indeksowanie równoległe

Drugi to skonfigurować równoległego indeksowania strategii. Nietypowy wymaga dużej mocy obliczeniowej, indeksowanie wymaganiach, takich jak optyczne rozpoznawanie znaków w zeskanowane dokumenty w usłudze wyszukiwania poznawczego potoku indeksowania strategii równoległego może być właściwe podejście do określonego celu. W usłudze wyszukiwania poznawczego wzbogacony potok obrazów, analizy i przetwarzania języka naturalnego są długo działające. Równoległe Indeksowanie to usługa, która jednocześnie nie obsługuje żądań zapytań może być rentowną opcją pracy nad dużą porcję wolne przetwarzania zawartości. 

Strategia do przetwarzania równoległego ma następujące elementy:

+ Podziel dane źródłowe między wiele kontenerów lub wielu wirtualnych folderów, w tym samym kontenerze. 
+ Mapowanie każdego mini zestawu danych, aby [źródło daty](https://docs.microsoft.com/rest/api/searchservice/create-data-source)parowania na własnej [indeksatora](https://docs.microsoft.com/rest/api/searchservice/create-indexer).
+ Usługa cognitive search odwołania takie same [zestawu umiejętności](https://docs.microsoft.com/rest/api/searchservice/create-skillset) w Każda definicja indeksatora.
+ Zapisywanie w tym samym docelowym indeksem wyszukiwania. 
+ Planowanie wszystkich indeksatorów, aby go uruchomić w tym samym czasie.

> [!Note]
> Usługa Azure Search nie obsługuje przypisywanie repliki lub partycje do konkretnych obciążeń. Ryzyko związane z dużymi indeksowania współbieżnych jest nadmiernego obciążania systemu na szkodę wydajności zapytań. W przypadku środowiska testowego wdrożenia równoległego indeksowania najpierw zrozumieć skutków ubocznych.

## <a name="configure-parallel-indexing"></a>Konfigurowanie indeksowania równoległe

Dla indeksatorów wydajności przetwarzania luźno opiera się na jednym podsystemie indeksatora dla każdej jednostki usługi (SU) używane przez usługi wyszukiwania. Wiele współbieżnych indeksatorów są możliwe w przypadku usług Azure Search aprowizowanych w warstwach Basic lub Standard, o co najmniej dwie repliki. 

1. W [witryny Azure portal](https://portal.azure.com), na pulpicie nawigacyjnym usługi wyszukiwania **Przegląd** strony wyboru **warstwa cenowa** aby upewnić się, może obsłużyć, równoległe indeksowania. Warstwy Basic i Standard oferują wiele replik.

2. W **ustawienia** > **skalowania**, [zwiększenia replik](search-capacity-planning.md) do przetwarzania równoległego: jedna replika dodatkowe dla poszczególnych obciążeń indeksatora. Pozostaw wystarczającą liczbę dla istniejącego woluminu zapytania. Poświęcania obciążeń związanych z zapytaniami indeksowania nie jest dobry kosztem.

3. Globalna dystrybucja danych w wielu kontenerach na poziomie, który może osiągnąć indeksatorów usługi Azure Search. Może to być wielu tabel w usłudze Azure SQL Database, wiele kontenerów usługi Azure Blob Storage lub wieloma kolekcjami. Zdefiniuj jeden obiekt źródła danych dla każdej tabeli lub kontenera.

4. Utwórz i zaplanować wiele indeksatorów do równoległego uruchamiania:

   + Załóżmy usługi za pomocą sześciu replik. Skonfiguruj sześć indeksatorów, każdy z nich mapowane na źródło danych zawierające szósta część zestawu danych do podziału sposób 6 z cały zestaw danych. 

   + Wskazuje każdego indeksatora tego samego indeksu. W przypadku obciążeń wyszukiwania kognitywnego wskaż każdego indeksatora tego samego zestawu umiejętności.

   + W ramach Każda definicja indeksatora zaplanowanie wykonania tego samego wzorca. Na przykład `"schedule" : { "interval" : "PT8H", "startTime" : "2018-05-15T00:00:00Z" }` utworzenie harmonogramu na 2018-05-15 na wszystkie indeksatory, uruchomiony w odstępach czasu z ośmiu godzin.

W zaplanowanym czasie wszystkie indeksatory rozpocząć wykonywania, podczas ładowania danych, stosowanie wzbogacenia (Jeśli skonfigurowano potoku usłudze wyszukiwania poznawczego) i zapisywanie do indeksu. Usługa Azure Search nie blokuje indeksu dla aktualizacji. Współbieżne operacje są zarządzane przy użyciu ponawiania prób w przypadku określonego zapisu kończy się niepowodzeniem przy pierwszej próbie.

> [!Note]
> W przypadku zwiększenia replik, należy rozważyć, zwiększając liczbę partycji, jeśli pozwoli znacznie zwiększyć rozmiar indeksu. Partycje przechowywania wycinki indeksowanej zawartości; masz więcej partycji, mniejszy wycinek, każdy z nich ma do przechowywania.

## <a name="see-also"></a>Zobacz także

+ [Omówienie indeksatora](search-indexer-overview.md)
+ [Indeksowanie w portalu](search-import-data-portal.md)
+ [Indeksator usługi Azure SQL Database](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
+ [Indeksator usługi Azure Cosmos DB](search-howto-index-cosmosdb.md)
+ [Indeksator usługi Azure Blob Storage](search-howto-indexing-azure-blob-storage.md)
+ [Indeksator usługi Azure Table Storage](search-howto-indexing-azure-tables.md)
+ [Zabezpieczenia w usłudze Azure Search](search-security-overview.md)