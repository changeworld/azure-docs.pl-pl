---
title: Tworzenie odtwarzania wideo na żądanie za pomocą funkcji zmiany czasu i wyjść na żywo
titleSuffix: Azure Media Services
description: W tym artykule opisano sposób używania przesunięcia czasu i wyjść na żywo do rejestrowania strumieni na żywo i tworzenia odtwarzania na żądanie.
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
ms.openlocfilehash: 4c7618b60e5fd86a9b8b3f22fb3333c00cfdfa61
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74899793"
---
# <a name="use-time-shifting-and-live-outputs-to-create-on-demand-video-playback"></a>Tworzenie odtwarzania wideo na żądanie za pomocą funkcji zmiany czasu i wyjść na żywo

W usłudze Azure Media Services obiekt [danych wyjściowych na żywo](https://docs.microsoft.com/rest/api/media/liveoutputs) jest jak cyfrowy rejestrator wideo, który przechwytuje i rejestruje strumień na żywo do zasobu na koncie usługi Media Services. Zarejestrowana zawartość jest zachowywana w kontenerze [zdefiniowanym](https://docs.microsoft.com/rest/api/media/assets) przez zasób zasobów (kontener znajduje się na koncie usługi Azure Storage dołączonym do konta). Dane wyjściowe na żywo umożliwia również sterowanie niektórymi właściwościami wychodzącego strumienia na żywo, na przykład dużą część strumienia przechowywana w nagraniu archiwum (na przykład pojemność rejestratora DVR w chmurze) lub kiedy widzowie mogą rozpocząć oglądanie transmisji na żywo. Archiwum na dysku jest okrągłe archiwum "okno", który zawiera tylko ilość zawartości, która jest określona w **archiveWindowLength** właściwości Live Output. Zawartość, która wypada poza to okno jest automatycznie odrzucana z kontenera magazynu i nie można jej odzyskać. Wartość archiveWindowLength reprezentuje czas trwania iso-8601 (na przykład PTHH:MM:SS), który określa pojemność rejestratora. Wartość można ustawić z co najmniej trzech minut do maksymalnie 25 godzin.

Relacja między wydarzeniem na żywo a jego wyjściami na żywo jest podobna do tradycyjnej transmisji telewizyjnej, ponieważ kanał (wydarzenie na żywo) reprezentuje stały strumień wideo, a nagranie (wyjście na żywo) jest ograniczone do określonego segmentu czasu (na przykład wiadomości wieczorne z 18:30 do 19:00). Po strumieniu płynącym do zdarzenia na żywo, można rozpocząć zdarzenie przesyłania strumieniowego, tworząc zasób, wyjście na żywo i lokalizator przesyłania strumieniowego. Wyjście na żywo zarchiwizuje strumień i udostępni go widzom za pośrednictwem [punktu końcowego przesyłania strumieniowego.](https://docs.microsoft.com/rest/api/media/streamingendpoints) W wydarzeniu na żywo można utworzyć wiele wyjść na żywo (maksymalnie maksymalnie trzy) o różnych długościach i ustawieniach archiwum. Aby uzyskać informacje na temat przepływu pracy przesyłania strumieniowego na żywo, zobacz sekcję [ogólne kroki.](live-streaming-overview.md#general-steps)

## <a name="using-a-dvr-during-an-event"></a>Korzystanie z rejestratora podczas zdarzenia

W tej sekcji omówiono sposób używania rejestratora DVR podczas zdarzenia, aby kontrolować, jakie części strumienia są dostępne dla "przewijania do tyłu".

Wartość `archiveWindowLength` określa, jak daleko wstecz w czasie widz może przejść od bieżącej pozycji na żywo. Wartość `archiveWindowLength` określa również, jak długo manifesty klienta mogą rosnąć.

Załóżmy, że transmitujesz mecz piłki `ArchiveWindowLength` nożnej i ma tylko 30 minut. Widz, który rozpocznie oglądanie wydarzenia 45 minut po rozpoczęciu gry, może wrócić do co najwyżej 15-minutowego znaku. Twoje wyjścia na żywo w grze będą kontynuowane do czasu zatrzymania wydarzenia na żywo. Zawartość, która nie wchodzi w zakres archiwumWindowLength jest stale odrzucana z magazynu i nie można jej odzyskać. W tym przykładzie wideo między rozpoczęciem zdarzenia i 15-minutowy znak zostałby usunięty z rejestratora DVR i z kontenera w magazynie obiektów blob dla zasobu. Archiwum nie można odzyskać i jest usuwany z kontenera w magazynie obiektów blob platformy Azure.

Wydarzenie na żywo obsługuje maksymalnie trzy jednocześnie uruchomione wyjścia na żywo (można utworzyć co najwyżej 3 nagrania/archiwa z jednego strumienia na żywo w tym samym czasie). Ta obsługa umożliwia publikowanie i archiwizowanie różnych części zdarzenia w razie potrzeby. Załóżmy, że musisz emitować liniowy kanał na żywo 24x7 i tworzyć "nagrania" różnych programów przez cały dzień, aby oferować klientom treści na żądanie do oglądania nadrabiania zaległości. W tym scenariuszu najpierw należy utworzyć podstawowe dane wyjściowe na żywo z krótkim oknem archiwum 1 godziny lub mniej, jest to podstawowy strumień na żywo, który widzowie będą dostroić. Należy utworzyć lokalizator przesyłania strumieniowego dla tego wyjścia na żywo i opublikować go w aplikacji lub witrynie sieci Web jako "Live" źródła danych. Gdy zdarzenie na żywo jest uruchomiony, można programowo utworzyć drugi równoczesnych danych wyjściowych na żywo na początku programu (lub 5 minut wcześniej, aby zapewnić niektóre uchwyty do przycięcia później). To drugie wyjście na żywo można usunąć 5 minut po zakończeniu programu. Za pomocą tego drugiego zasobu można utworzyć nowy lokalizator przesyłania strumieniowego, aby opublikować ten program jako zasób na żądanie w katalogu aplikacji. Możesz powtórzyć ten proces wiele razy dla innych granic programu lub wyróżnień, które chcesz udostępnić jako filmy na żądanie, podczas gdy kanał "Na żywo" z pierwszego live output będzie nadal emitował kanał liniowy.

## <a name="creating-an-archive-for-on-demand-playback"></a>Tworzenie archiwum do odtwarzania na żądanie

Zasób, który jest archiwizowanie na żywo automatycznie staje się zasobem na żądanie po usunięciu danych wyjściowych na żywo. Przed zatrzymaniem zdarzenia na żywo należy usunąć wszystkie dane wyjściowe na żywo. Można użyć opcjonalnej flagi [removeOutputsOnStop,](https://docs.microsoft.com/rest/api/media/liveevents/stop#request-body) aby automatycznie usunąć wyjścia na żywo na przystanku.

Nawet po zatrzymaniu i usunięciu zdarzenia użytkownicy mogą przesyłać strumieniowo zarchiwizowanej zawartości jako wideo na żądanie, o ile nie usuniesz zasobu. Zasób nie powinien być usuwany, jeśli jest używany przez zdarzenie; zdarzenie musi zostać usunięte w pierwszej kolejności.

Jeśli zasób danych na żywo został opublikowany przy użyciu lokalizatora przesyłania strumieniowego, zdarzenie na żywo (do długości okna DVR) będzie nadal widoczne do czasu wygaśnięcia lub usunięcia lokalizatora przesyłania strumieniowego, w zależności od tego, co nastąpi wcześniej.

Aby uzyskać więcej informacji, zobacz:

- [Omówienie transmisji na żywo](live-streaming-overview.md)
- [Samouczek przesyłania strumieniowego na żywo](stream-live-tutorial-with-api.md)

> [!NOTE]
> Po usunięciu danych wyjściowych na żywo nie usuwasz bazowego zasobu i zawartości zasobu.

## <a name="next-steps"></a>Następne kroki

* [Subclip swoje filmy](subclip-video-rest-howto.md).
* [Zdefiniuj filtry dla zasobów](filters-dynamic-manifest-rest-howto.md).
