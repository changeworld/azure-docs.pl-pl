---
title: Tworzenie informacji szczegółowych wideo z istniejących wideo — na platformie Azure
titlesuffix: Azure Media Services
description: W tym temacie pokazano, jak tworzyć i publikować szczegółowe informacje o wideo na podstawie istniejących plików wideo.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.topic: article
ms.date: 02/10/2019
ms.author: juliako
ms.openlocfilehash: 4a65e88e3f94f64a56bde882b535030968ae354d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60560230"
---
# <a name="create-highlights-from-existing-videos"></a>Utwórz najważniejsze wydarzenia z istniejących wideo

W tym temacie pokazano, jak tworzyć i publikować szczegółowe informacje o wideo na podstawie innego pliku wideo.

1. Przejdź do witryny internetowej [Video Indexer](https://www.videoindexer.ai/) i zaloguj się.
2. Znajdź plik wideo, dla którego chcesz utworzyć szczegółowe informacje o wideo.
3. Naciśnij pozycję **Play** (Odtwórz).

    Na stronie zostaną wyświetlone podsumowane szczegółowe informacje dotyczące tego pliku wideo. 

    ![Insights](./media/video-indexer-create-new/video-indexer-summarized-insights.png)
3. Naciśnij przycisk **Edit** (Edytuj).

    Na stronie zostanie wyświetlone pełne zestawienie dotyczące tego pliku wideo. Zestawienie to jest podzielone na bloki. Bloki mają ułatwić nawigowanie po danych. Blok może zostać wyodrębniony na przykład na podstawie zmiany osoby mówiącej lub wystąpienia długiej przerwy. Możesz utworzyć własną listę odtwarzania zawierającą tylko wybrane wypowiedzi. Aby zaprezentować tylko określone części źródłowego nagrania wideo, możesz przefiltrować je według tematów/słów kluczowych, tonacji, osób i osób mówiących. Możesz wybrać opcję wyświetlania tylko transkrypcji lub wyników optycznego rozpoznawania znaków (OCR) z nagrania wideo.    

    ![Insights](./media/video-indexer-create-new/video-indexer-create-new-playlist.png)
4. Utwórz listę odtwarzania.

    Aby dodać lub usunąć wypowiedzi na liście odtwarzania, naciskaj znaki **+**/**-**.
5. Wyświetl podgląd listy odtwarzania.

    Po utworzeniu listy odtwarzania naciśnij przycisk **Preview** (Podgląd).
6. Opublikuj listę odtwarzania.

    Po obejrzeniu podglądu listy odtwarzania możesz ją opublikować.

    Opublikowana lista odtwarzania jest dodawana do listy Twoich szczegółowych informacji o wideo.


## <a name="next-steps"></a>Kolejne kroki 

Po utworzeniu nowej listy odtwarzania możesz kontynuować jej przetwarzanie — zgodnie z opisem w jednym z następujących tematów: 

- [Przetwarzanie zawartości przy użyciu interfejsu REST API usługi Video Indexer](video-indexer-use-apis.md)
- [Osadzanie widgetów wizualnych w aplikacji](video-indexer-embed-widgets.md)

## <a name="see-also"></a>Zobacz także

[Omówienie usługi Video Indexer](video-indexer-overview.md) 
