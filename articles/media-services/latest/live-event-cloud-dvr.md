---
title: Azure Media Services w element LiveEvent i funkcji DVR w chmurze | Dokumentacja firmy Microsoft
description: W tym artykule opisano, co to jest LiveOutput i jak używać funkcji DVR w chmurze.
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
ms.date: 11/28/2018
ms.author: juliako
ms.openlocfilehash: 8df43a9b2c518e77d14dd5cb392b042b0b4846e2
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/14/2018
ms.locfileid: "53407970"
---
# <a name="using-a-cloud-dvr"></a>Korzystanie z funkcji DVR w chmurze

A [LiveOutput](https://docs.microsoft.com/rest/api/media/liveoutputs) umożliwia sterowanie właściwości wychodzącej transmisji strumieniowej na żywo, takie jak ilość strumienia jest rejestrowane (na przykład pojemność funkcji DVR w chmurze) i czy osoby oglądające można uruchomić, oglądając transmisji strumieniowej na żywo. Relacja między **element LiveEvent** i jego **LiveOutput**s jest podobny do tradycyjnych telewizyjnych emisji, według której kanału (**element LiveEvent**) reprezentuje stałą strumień i nagranie wideo (**LiveOutput**) obejmuje zasięgiem segmencie określony czas (na przykład wieczorami wiadomości od 18:30:00 do 19:00:00). Można rejestrować za pomocą cyfrowego rejestratora wideo (DVR) telewizyjnych — funkcji równoważnej w LiveEvents odbywa się za pomocą właściwości ArchiveWindowLength. Nadszedł czas przedział czasu ISO 8601 (na przykład PTHH:MM:SS) Określa pojemność DVR, która może być równa z co najmniej 3 minuty maksymalnie 25 godzin.

## <a name="liveoutput"></a>LiveOutput

**ArchiveWindowLength** wartość określa, jak daleko wstecz w czasie, a przeglądarka może wyszukać z bieżącego położenia na żywo.  **ArchiveWindowLength** określa również, jak długo mogą być manifesty na klienta.

Załóżmy, że strumieniowe gra football i ma **ArchiveWindowLength** tylko przez 30 minut. Viewer, który rozpoczyna się, oglądając zdarzenia 45 minut, po rozpoczęciu gry można zwrócić do co najwyżej znacznik 15-minutowy. Twoje **LiveOutput**pod kątem gra będzie kontynuowane do czasu **element LiveEvent** zostanie zatrzymana, ale zawartości, który przypada poza **ArchiveWindowLength** jest stale odrzucana z Magazyn i jest nieodwracalne. W tym przykładzie wideo między początkiem zdarzenia, a znak 15-minutowy będą mogły zostać wyczyszczone z Twojej DVR i z kontenera magazynu obiektów blob na potrzeby [zasobów](https://docs.microsoft.com/rest/api/media/assets). Archiwum nie jest możliwe do odzyskania i zostanie usunięty z kontenera w usłudze Azure blob storage.

Każdy **LiveOutput** jest skojarzony z **zasobów**, która jest używana do rejestrowania wideo do kontenera magazynu skojarzonego obiektu blob platformy Azure. Aby opublikować LiveOutput, należy utworzyć **StreamingLocator** tego **zasobów**. Po utworzeniu [StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators), możesz tworzyć adresu URL przesyłania strumieniowego, zapewniające widzów.

A **element LiveEvent** obsługuje maksymalnie trzy jednocześnie uruchomione **LiveOutput**s, aby można było utworzyć maksymalnie 3 nagrania/archiwami z jednym strumień na żywo. Umożliwia to w razie potrzeby publikowanie i archiwizację różnych części wydarzenia. Załóżmy, że należy emisji 24 x 7 liniowej Kanał informacyjny na żywo, a następnie utwórz "nagrań" różnych programach przez cały dzień, aby zaoferować klientom jako zawartość na żądanie do wyświetlania zapoznać się ze zmianami. W tym scenariuszu należy najpierw utworzyć głównej LiveOutput, okno archiwum krótki równej 1 godz lub mniej — jest to podstawowy transmisji strumieniowej na żywo, czy dostroić przeglądającym. Należy utworzyć **StreamingLocator** tego **LiveOutput** i opublikujesz je do aplikacji lub witryny sieci web jako źródło danych "Live". Gdy **element LiveEvent** jest uruchomiona, można programowo utworzyć drugi współbieżnych **LiveOutput** na początku programu (lub podać niektóre uchwytów należy wcześnie w ciągu 5 minut można przycięcia później). To drugie **LiveOutput** 5 minut po zakończeniu program może zostać usunięty. Ten drugi **zasobów**, można utworzyć nową **StreamingLocator** Aby opublikować ten program jako elementu zawartości na żądanie w katalogu aplikacji. Ten proces można powtarzać wielokrotnie inne ograniczenia dotyczące programów lub najważniejsze funkcje, które chcesz udostępnić jako filmów wideo na żądanie, wszystko to "Live" źródła danych od pierwszego **LiveOutput** emisji liniowej kanału informacyjnego w dalszym ciągu. 

> [!NOTE]
> **LiveOutput**s start przy tworzeniu i Zatrzymaj po usunięciu. Po usunięciu **LiveOutput**, nie powoduje usunięcia podstawowych **zasobów** i zawartości w elemencie zawartości.  

## <a name="next-steps"></a>Kolejne kroki

- [Omówienie transmisji strumieniowej na żywo](live-streaming-overview.md)
- [Samouczek transmisji strumieniowej na żywo](stream-live-tutorial-with-api.md)

