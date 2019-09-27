---
title: Korzystanie z Azure Media Services przesunięcia czasu i VOD na żądanie Microsoft Docs
description: W tym artykule wyjaśniono, jakie dane wyjściowe są na żywo i jak używać DVR w chmurze.
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
ms.date: 08/27/2019
ms.author: juliako
ms.openlocfilehash: ffcd279830cb49b64ddbb58a888ad7d653918b1b
ms.sourcegitcommit: e1b6a40a9c9341b33df384aa607ae359e4ab0f53
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/27/2019
ms.locfileid: "71338839"
---
# <a name="using-time-shifting-and-live-to-vod-video-on-demand"></a>Używanie przesunięć czasowych i na żywo na VOD (wideo na żądanie)

W Azure Media Services obiekt [danych wyjściowych na żywo](https://docs.microsoft.com/rest/api/media/liveoutputs) przypomina cyfrowy rejestrator wideo, który będzie przechwytywać i rejestrować strumień na żywo do zasobu na koncie Media Services. Zarejestrowana zawartość jest zachowywana w kontenerze zdefiniowanym [przez zasób zasobów](https://docs.microsoft.com/rest/api/media/assets) (kontener znajduje się na koncie usługi Azure Storage podłączonym do Twojego konta). Na żywo dane wyjściowe umożliwiają również kontrolowanie niektórych właściwości wychodzącego strumienia na żywo, takich jak szybkość przesyłania strumienia w archiwum (na przykład pojemność DVR w chmurze), a także to, czy użytkownicy mogą zacząć oglądać strumień na żywo. Archiwum na dysku jest archiwum cykliczne "okno", które zawiera tylko ilość zawartości, która jest określona we właściwości **archiveWindowLength** na żywo danych wyjściowych. Zawartość spoza tego okna jest automatycznie odrzucana z kontenera magazynu i nie można jej odzyskać. Wartość archiveWindowLength reprezentuje czas trwania przedziału ISO 8601 (na przykład PTHH: MM: SS), który określa pojemność DVR i może być ustawiona z co najmniej 3 minut do maksymalnie 25 godzin.

Relacja między wydarzeniem na żywo a jego wyjściem na żywo jest podobna do tradycyjnego rozgłaszania TV, przez co kanał (wydarzenie na żywo) reprezentuje stały strumień wideo, a nagrywanie (wyjście na żywo) jest ograniczone do określonego segmentu czasu (na przykład wieczorem wiadomości od 6:30 do 7:13:00). Gdy strumień przepływa do zdarzenia na żywo, możesz rozpocząć zdarzenie przesyłania strumieniowego, tworząc element zawartości, dane wyjściowe i lokalizator przesyłania strumieniowego. Na żywo wyjście będzie archiwizować strumień i udostępnić je osobom oglądającym za pomocą [punktu końcowego przesyłania strumieniowego](https://docs.microsoft.com/rest/api/media/streamingendpoints). Można utworzyć wiele danych wyjściowych na żywo (maksymalnie trzy) dla zdarzenia na żywo o różnych długościach i ustawieniach archiwum. Informacje o przepływie pracy przesyłania strumieniowego na żywo znajdują się w sekcji [ogólnych kroków](live-streaming-overview.md#general-steps) .

## <a name="using-a-dvr-during-an-event"></a>Używanie DVR w trakcie zdarzenia 

W tej sekcji omówiono sposób użycia DVR podczas zdarzenia do kontrolowania, jakie fragmenty strumienia są dostępne dla przewijania do tyłu.

Wartość archiveWindowLength określa, jak daleko wstecz w czasie przeglądarka może poszukiwać z bieżącej pozycji na żywo. Wartość archiveWindowLength określa również, jak długo mogą się zwiększać manifesty klienta.

Załóżmy, że przesyłasz strumieniowo grę piłkarskią i ArchiveWindowLength tylko 30 minut. Przeglądarka, która zacznie oglądać wydarzenie 45 minut po rozpoczęciu gry, może odwrócić do maksymalnie 15 minut. Dane wyjściowe na żywo dla gry będą kontynuowane do momentu zatrzymania zdarzenia na żywo, ale zawartość, która wykracza poza archiveWindowLength, jest ciągle odrzucana z magazynu i nie jest odzyskiwalna. W tym przykładzie film wideo między początkiem zdarzenia a 15-minutowy znacznik zostałby przeczyszczony z DVR i z kontenera w usłudze BLOB Storage dla elementu zawartości. Archiwum nie jest możliwe do odzyskania i jest usuwane z kontenera w usłudze Azure Blob Storage.

Wydarzenie na żywo obsługuje maksymalnie trzy współbieżnie uruchomione wyniki na żywo (można utworzyć maksymalnie 3 nagrania/archiwa z jednego strumienia na żywo w tym samym czasie). Umożliwia to w razie potrzeby publikowanie i archiwizację różnych części wydarzenia. Załóżmy, że trzeba emitować 24x7 na żywo i utworzyć "nagrania" różnych programów w ciągu dnia, aby zaoferować klientom jako zawartość na żądanie w celu przeprowadzenia wyświetlania. W tym scenariuszu najpierw utworzysz podstawowe wyjście na żywo z krótkim oknem archiwum o wartości 1 godziny lub mniej — jest to podstawowy strumień na żywo, w którym Oglądasz Twoje przeglądarki. Utworzysz lokalizator przesyłania strumieniowego dla tych danych wyjściowych na żywo i opublikujesz go w swojej aplikacji lub witrynie sieci Web jako kanał informacyjny "Live". Gdy uruchomione jest wydarzenie na żywo, można programowo utworzyć dwa współbieżne dane wyjściowe na żywo na początku programu (lub 5 minut na wczesnym etapie, aby udostępnić kilka dojść do późniejszego przycięcia). Te drugie dane wyjściowe można usunąć 5 minut po zakończeniu programu. W tym drugim elemencie zawartości można utworzyć nowy lokalizator przesyłania strumieniowego w celu opublikowania tego programu jako zasobu na żądanie w katalogu aplikacji. Proces ten można powtórzyć wielokrotnie dla innych granic programu lub podświetlenia, które mają być udostępniane jako wideo na żądanie, a wszystko to, gdy kanał informacyjny "Live" z pierwszego wyjścia na żywo kontynuuje emitowanie strumieniowego źródła danych. 

## <a name="creating-an-archive-for-on-demand-playback"></a>Tworzenie archiwum na potrzeby odtwarzania na żądanie

Element zawartości, do której jest archiwizowany na żywo danych wyjściowych, automatycznie będzie zasobem na żądanie po usunięciu danych wyjściowych na żywo. Przed zatrzymaniem zdarzenia na żywo należy usunąć wszystkie dane wyjściowe na żywo. Można użyć opcjonalnej flagi [removeOutputsOnStop](https://docs.microsoft.com/rest/api/media/liveevents/stop#request-body) , aby automatycznie usuwać wyjścia na żywo po zatrzymaniu. 

Nawet po zatrzymaniu i usunięciu zdarzenia użytkownicy będą mogli przesyłać strumieniowo zarchiwizowaną zawartość jako wideo na żądanie tak długo, jak nie usuniesz elementu zawartości. Nie należy usuwać elementu zawartości, jeśli jest on używany przez zdarzenie. najpierw należy usunąć zdarzenie.

Jeśli element zawartości danych wyjściowych na żywo został opublikowany przy użyciu lokalizatora przesyłania strumieniowego, zdarzenie na żywo (do okna DVR) będzie nadal widoczne do momentu wygaśnięcia lub usunięcia lokalizatora przesyłania strumieniowego, w zależności od tego, co nastąpi wcześniej.

Aby uzyskać więcej informacji, zobacz:

- [Omówienie przesyłania strumieniowego na żywo](live-streaming-overview.md)
- [Samouczek przesyłania strumieniowego na żywo](stream-live-tutorial-with-api.md)

> [!NOTE]
> Po usunięciu danych wyjściowych na żywo nie jest usuwany podstawowy element zawartości i zawartość w elemencie zawartości. 

## <a name="next-steps"></a>Następne kroki

* [Przytnij klipy wideo](subclip-video-rest-howto.md).
* [Zdefiniuj filtry dla zasobów](filters-dynamic-manifest-rest-howto.md).
