---
title: Indeks dużych zestawów danych przy użyciu indeksatorów wbudowanych — usługa Azure Search
description: Poznaj strategie opracowywania dużych ilości danych indeksowania lub wymaga dużej mocy obliczeniowej indeksowania w trybie wsadowym, zasoby i techniki zaplanowane, równoległych i rozproszonych, indeksowanie.
services: search
author: HeidiSteen
manager: cgronlun
ms.service: search
ms.topic: conceptual
ms.date: 12/19/2018
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 8c067b6e238fab2970e5e40f0660a5c7555a8f2e
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/20/2019
ms.locfileid: "67302227"
---
# <a name="how-to-index-large-data-sets-in-azure-search"></a>Jak indeksować dużych zestawów danych w usłudze Azure Search

Wzrostu ilości danych lub przetwarzania zmienią się potrzeby, może się okazać, że domyślny strategie indeksowania nie są już praktyczne. Usługi Azure Search jest kilka metod spełnić większych zestawów danych — od strukturą żądania przekazania danych, za pomocą indeksatora specyficznymi dla źródła dla zaplanowanych, jak i rozproszonych obciążeń.

Te same techniki dla dużych ilości danych dotyczą również długotrwałe procesy. W szczególności kroki opisane w [równoległe indeksowania](#parallel-indexing) są przydatne podczas indeksowania wymaga dużej mocy obliczeniowej, takich jak analiza obrazu lub języka naturalnego, w [potoków wyszukiwania kognitywnego](cognitive-search-concept-intro.md).

## <a name="batch-indexing"></a>Indeksowanie usługi Batch

Jednym z najprostszym mechanizmy indeksowania większy zestaw danych jest można przesłać wielu dokumentów lub rekordy w ramach pojedynczego żądania. Tak długo, jak ładunek całego jest w obszarze 16 MB, żądanie może obsługiwać maksymalnie 1000 dokumentów w operacji przekazywania zbiorczej. Zakładając, że [apletu Dodaj lub interfejsu API REST dokumentów aktualizacji](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents), czy pakiet 1000 dokumentów w treści żądania.

Indeksowanie usługi Batch jest implementowana dla poszczególnych żądań przy użyciu REST lub platformy .NET lub przy użyciu indeksatorów. Indeksatory kilka działają różne limity. W szczególności indeksowanie obiektów Blob platformy Azure ustawia rozmiar partii w 10 dokumentów za większy rozmiar średni dokumentu. Dla indeksatorów, na podstawie [tworzenie indeksatora interfejsu API REST](https://docs.microsoft.com/rest/api/searchservice/Create-Indexer ), można ustawić `BatchSize` argumentu, aby dostosować to ustawienie, aby lepiej dopasować je do właściwości danych. 

> [!NOTE]
> Aby zachować rozmiaru dokumentu,, pamiętaj, aby wykluczyć nie umożliwia zadawania zapytań dane z żądania. Obrazy i inne dane binarne nie są bezpośrednio można wyszukiwać i nie powinny być zapisane w indeksie. Do integracji danych nie umożliwia zadawania zapytań w wynikach wyszukiwania, należy zdefiniować niemożliwych pola, które przechowuje odwołanie adresu URL do zasobu.

## <a name="add-resources"></a>Dodaj zasoby

Usługi, które są udostępniane w jednym z [ceny warstwy standardowej](search-sku-tier.md) często mają niewykorzystane pojemność magazynu i obciążeń (zapytania lub indeksowania), co sprawia, że [zwiększenie liczby partycji i replik ](search-capacity-planning.md) to oczywiste rozwiązanie spełnić większych zestawów danych. Aby uzyskać najlepsze wyniki, należy oba zasoby: partycji dla magazynu i repliki dla pracy pozyskiwania danych.

Zwiększa replik i partycji są płatnych zdarzeń, które zwiększają kosztów, ale chyba że są stale indeksowania, w obszarze maksymalnego obciążenia, można dodawać skalę na czas trwania procesu indeksowania, a następnie Dostosuj poziomów zasobów w dół, po indeksowania zostało zakończone.

## <a name="use-indexers"></a>Korzystać z indeksatorów

[Indeksatory](search-indexer-overview.md) służą do przeszukuje zewnętrzne źródła danych dla zawartość do przeszukiwania. Mimo, że nie jest specjalnie przeznaczony do indeksowania na dużą skalę, kilka możliwości indeksatora są szczególnie przydatne w przypadku uwzględniania większych zestawów danych:

+ Transfery danych umożliwiają działka limit indeksowania w regularnych odstępach czasu, aby rozłożyć wraz z upływem czasu.
+ Zaplanowane indeksowania może wznowić w momencie ostatni znany zatrzymywania. Jeśli źródło danych nie są w pełni przeszukiwane w okresie 24-godzinnym, indeksator zostanie wznowiona indeksowania w dniu, dwa w wszędzie tam, gdzie zostało przerwane.
+ Partycjonowanie danych w mniejszej poszczególnych źródeł danych umożliwia przetwarzanie równoległe. Możesz przerwać dużych zestawów danych mniejszych zestawów danych, a następnie utwórz wiele definicji źródła danych, które mogą być indeksowane równolegle.

> [!NOTE]
> Indeksatory są źródła dotyczące danych, dzięki czemu przy użyciu podejścia indeksatora jest tylko do wybranych źródeł danych na platformie Azure: [Baza danych SQL](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md), [magazynu obiektów Blob](search-howto-indexing-azure-blob-storage.md), [Table storage](search-howto-indexing-azure-tables.md), [Cosmos DB](search-howto-index-cosmosdb.md).

## <a name="scheduled-indexing"></a>Zaplanowane indeksowania

Planowanie indeksatora jest ważnym mechanizmem do przetwarzania dużych zestawów danych, a także wolno działające procesy, takich jak analiza obrazu w usłudze wyszukiwania poznawczego potoku. Indeksator przetwarzania działa w okresie 24-godzinnym. Jeśli przetwarzanie zakończy się, aby zakończyć w ciągu 24 godzin, wykorzystaj pracować zachowania planowania indeksatora. 

Zgodnie z projektem należy zaplanować indeksowanie rozpoczyna się w określonych odstępach czasu, za pomocą zadania zazwyczaj ukończenie przed wznowieniem w następnym zaplanowanym interwale. Jednak jeśli przetwarzanie zakończy się w przedziale czasu, indeksator przestaje (ponieważ zabrakło czasu). W następnym interwale czasowym wznawia przetwarzania tam, gdzie ją ostatnia przerwaliśmy, za pomocą zachowania systemu prowadnicy gdzie występuje. 

W praktyce, w przypadku obciążeń indeksu obejmujące kilka dni można umieścić indeksator zgodnie z harmonogramem 24-godzinnym. Podczas indeksowania wznawia na następny cykl 24-godzinnym, uruchamia ponownie w ostatnich znanych dokumentu dobre. W ten sposób indeksatora pracować jego sposób za pomocą zaległości dokumentu przez szereg dni, po których przetwarzane są wszystkie nieprzetworzone dokumenty. Aby uzyskać więcej informacji na temat tego podejścia, zobacz [indeksowania dużych zestawów danych w usłudze Azure Blob storage](search-howto-indexing-azure-blob-storage.md#indexing-large-datasets). Aby uzyskać więcej informacji na temat ustawiania harmonogramy ogólnie rzecz biorąc, zobacz [tworzenie indeksatora interfejsu API REST](https://docs.microsoft.com/rest/api/searchservice/Create-Indexer#request-syntax) lub zobacz [sposób tworzenia harmonogramu indeksatorów usługi Azure Search](search-howto-schedule-indexers.md).

<a name="parallel-indexing"></a>

## <a name="parallel-indexing"></a>Indeksowanie równoległe

Równoległa indeksowania strategii opiera się na indeksowanie wiele źródeł danych spójnie, gdzie każda definicja źródła danych określa podzbiór danych. 

Nietypowy, wymaga dużej mocy obliczeniowej indeksowania wymagania — takich jak optyczne rozpoznawanie znaków w zeskanowane dokumenty w usłudze wyszukiwania poznawczego potoku, analizy obrazów lub przetwarzania języka naturalnego - równoległego indeksowania strategia jest często właściwe podejście do ukończenia proces długo działających w możliwie najkrótszym czasie. Jeśli można wyeliminować, lub Zmniejsz żądań zapytań, równoległe Indeksowanie to usługa, która jednocześnie nie obsługuje zapytań jest najlepszym rozwiązaniem strategii do pracy nad dużą porcję wolne przetwarzania zawartości. 

Przetwarzanie równoległe ma następujące elementy:

+ Należy podzielić dane źródłowe między wiele kontenerów lub wielu wirtualnych folderów, w tym samym kontenerze. 
+ Mapowanie mini zestawów danych do jego własnej [źródła danych](https://docs.microsoft.com/rest/api/searchservice/create-data-source)parowania na własnej [indeksatora](https://docs.microsoft.com/rest/api/searchservice/create-indexer).
+ Usługa cognitive search odwołania takie same [zestawu umiejętności](https://docs.microsoft.com/rest/api/searchservice/create-skillset) w Każda definicja indeksatora.
+ Zapisywanie w tym samym docelowym indeksem wyszukiwania. 
+ Planowanie wszystkich indeksatorów, aby go uruchomić w tym samym czasie.

> [!NOTE]
> Usługa Azure Search nie obsługuje przypisywanie repliki lub partycje do konkretnych obciążeń. Ryzyko związane z dużymi indeksowania współbieżnych jest nadmiernego obciążania systemu na szkodę wydajności zapytań. W przypadku środowiska testowego wdrożenia równoległego indeksowania najpierw zrozumieć skutków ubocznych.

### <a name="how-to-configure-parallel-indexing"></a>Jak skonfigurować indeksowania równoległe

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
