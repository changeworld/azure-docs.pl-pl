---
title: Usługa Azure Media Services wydarzenie na żywo i funkcji DVR w chmurze | Dokumentacja firmy Microsoft
description: W tym artykule opisano nowości na żywo dane wyjściowe i sposobu używania funkcji DVR w chmurze.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 01/14/2019
ms.author: juliako
ms.openlocfilehash: 4dd14587ec7e1473953981c1ef8c32c59eb9a1d6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60322339"
---
# <a name="using-a-cloud-dvr"></a>Korzystanie z funkcji DVR w chmurze

A [na żywo dane wyjściowe](https://docs.microsoft.com/rest/api/media/liveoutputs) umożliwia sterowanie właściwości wychodzącej transmisji strumieniowej na żywo, takie jak ilość strumienia jest rejestrowane (na przykład pojemność funkcji DVR w chmurze) i czy osoby oglądające można uruchomić, oglądając transmisji strumieniowej na żywo. Relacja między **wydarzenie na żywo** i jego **na żywo dane wyjściowe**s jest podobny do tradycyjnych telewizyjnych emisji, według której kanału (**wydarzenie na żywo**) reprezentuje stałą strumień i nagranie wideo (**na żywo dane wyjściowe**) obejmuje zasięgiem segmencie określony czas (na przykład wieczorami wiadomości od 18:30:00 do 19:00:00). Można rejestrować za pomocą cyfrowego rejestratora wideo (DVR) telewizyjnych — funkcji równoważnej zdarzeń na żywo odbywa się za pomocą właściwości ArchiveWindowLength. Nadszedł czas przedział czasu ISO 8601 (na przykład PTHH:MM:SS) Określa pojemność DVR, która może być równa z co najmniej 3 minuty maksymalnie 25 godzin.

## <a name="live-output"></a>Dane wyjściowe na żywo

**ArchiveWindowLength** wartość określa, jak daleko wstecz w czasie, a przeglądarka może wyszukać z bieżącego położenia na żywo.  **ArchiveWindowLength** określa również, jak długo mogą być manifesty na klienta.

Załóżmy, że strumieniowe gra football i ma **ArchiveWindowLength** tylko przez 30 minut. Viewer, który rozpoczyna się, oglądając zdarzenia 45 minut, po rozpoczęciu gry można zwrócić do co najwyżej znacznik 15-minutowy. Twoje **na żywo dane wyjściowe**pod kątem gra będzie kontynuowane do czasu **wydarzenie na żywo** zostanie zatrzymana, ale zawartości, który przypada poza **ArchiveWindowLength** jest stale odrzucana z Magazyn i jest nieodwracalne. W tym przykładzie wideo między początkiem zdarzenia, a znak 15-minutowy będą mogły zostać wyczyszczone z Twojej DVR i z kontenera magazynu obiektów blob na potrzeby [zasobów](https://docs.microsoft.com/rest/api/media/assets). Archiwum nie jest możliwe do odzyskania i zostanie usunięty z kontenera w usłudze Azure blob storage.

Każdego **na żywo dane wyjściowe** jest skojarzony z **zasobów**, która jest używana do rejestrowania wideo do kontenera magazynu skojarzonego obiektu blob platformy Azure. Aby opublikować dane wyjściowe na żywo, musisz utworzyć **lokalizatora przesyłania strumieniowego** tego **zasobów**. Po utworzeniu [lokalizatora przesyłania strumieniowego](https://docs.microsoft.com/rest/api/media/streaminglocators), możesz tworzyć adresu URL przesyłania strumieniowego, zapewniające widzów.

A **wydarzenie na żywo** obsługuje maksymalnie trzy jednocześnie uruchomione **na żywo dane wyjściowe**s, aby można było utworzyć maksymalnie 3 nagrania/archiwami z jednym strumień na żywo. Umożliwia to w razie potrzeby publikowanie i archiwizację różnych części wydarzenia. Załóżmy, że należy emisji 24 x 7 liniowej Kanał informacyjny na żywo, a następnie utwórz "nagrań" różnych programach przez cały dzień, aby zaoferować klientom jako zawartość na żądanie do wyświetlania zapoznać się ze zmianami. W tym scenariuszu należy najpierw utworzyć podstawowe wyjście na żywo, okno archiwum krótki równej 1 godz lub mniej — jest to podstawowy transmisji strumieniowej na żywo, czy dostroić przeglądającym. Należy utworzyć **lokalizatora przesyłania strumieniowego** tego **na żywo dane wyjściowe** i opublikujesz je do aplikacji lub witryny sieci web jako źródło danych "Live". Gdy **wydarzenie na żywo** jest uruchomiona, można programowo utworzyć drugi współbieżnych **na żywo dane wyjściowe** na początku programu (lub podać niektóre uchwytów należy wcześnie w ciągu 5 minut można przycięcia później). To drugie **na żywo dane wyjściowe** 5 minut po zakończeniu program może zostać usunięty. Ten drugi **zasobów**, można utworzyć nową **lokalizatora przesyłania strumieniowego** Aby opublikować ten program jako elementu zawartości na żądanie w katalogu aplikacji. Ten proces można powtarzać wielokrotnie inne ograniczenia dotyczące programów lub najważniejsze funkcje, które chcesz udostępnić jako filmów wideo na żądanie, wszystko to "Live" źródła danych od pierwszego **na żywo dane wyjściowe** emisji liniowej kanału informacyjnego w dalszym ciągu. 

> [!NOTE]
> **Dane wyjściowe na żywo**s start przy tworzeniu i Zatrzymaj po usunięciu. Po usunięciu **na żywo dane wyjściowe**, nie powoduje usunięcia podstawowych **zasobów** i zawartości w elemencie zawartości. 
>
> Po opublikowaniu **na żywo dane wyjściowe** zasobów przy użyciu **lokalizatora przesyłania strumieniowego**, **wydarzenie na żywo** (maksymalna długość okna DVR) nadal będzie widoczny do momentu **Lokalizatora przesyłania strumieniowego**jego wygaśnięcia lub usunięcia, zależnie co nastąpi wcześniej.

## <a name="next-steps"></a>Kolejne kroki

- [Omówienie transmisji strumieniowej na żywo](live-streaming-overview.md)
- [Samouczek transmisji strumieniowej na żywo](stream-live-tutorial-with-api.md)

