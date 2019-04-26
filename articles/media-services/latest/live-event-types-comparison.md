---
title: Azure Media Services w element LiveEvent typy | Dokumentacja firmy Microsoft
description: W tym artykule przedstawiono szczegółowe tabelę, która porównywanie typów element LiveEvent.
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
ms.date: 03/01/2019
ms.author: juliako
ms.openlocfilehash: 9952a7bbac1eb79de0d3425f839e3bd30196844e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60322288"
---
# <a name="live-event-types-comparison"></a>Porównanie typów zdarzeń na żywo

W usłudze Azure Media Services [wydarzenie na żywo](https://docs.microsoft.com/rest/api/media/liveevents) może być jednym z dwóch typów: kodowanie na żywo oraz pass-through. 

## <a name="types-comparison"></a>Porównanie typów 

W poniższej tabeli porównano funkcje dwa typy wydarzenie na żywo.

| Cecha | Przekazywane zdarzenia na żywo | Wydarzenie na żywo standardowa |
| --- | --- | --- |
| Pojedyncza szybkość transmisji bitów w danych wejściowych jest zakodowany do wielokrotnych w chmurze |Nie |Yes |
| Maksymalna rozdzielczość wideo dla udziału kanału informacyjnego |4K (na 60 klatek na sekundę 4096 x 2160) |1080p (1920 x 1088 na 30 klatek na sekundę)|
| Zalecana maksymalna warstwy w udziale kanału informacyjnego|Do 12|Jedną ścieżkę dźwiękową|
| Maksymalna warstwy w danych wyjściowych| Takie same jak dane wejściowe|Do 6 (zobacz poniżej ustawienia systemu)|
| Maksymalna przepustowość agregacji wkładu kanału informacyjnego|60 MB/s|ND|
| Maksymalna szybkość transmisji bitów dla pojedynczej warstwy w udziale |20 MB/s|20 MB/s|
| Obsługa wielu języka ścieżki audio|Yes|Nie|
| Obsługiwane wejściowych koderów-dekoderów wideo |Koder H.264/AVC i H.265/HEVC|KODER H.264/AVC|
| Dane wyjściowe obsługiwanych koderów-dekoderów wideo|Takie same jak dane wejściowe|KODER H.264/AVC|
| Obsługiwana głębokość bitów wideo, dane wejściowe i wyjściowe|Maksymalnie 10-bitowy tym HDR 10/HLG|8-bitowa|
| Obsługiwane wejściowe audio kodery-dekodery|AAC-LC, HE-AAC v1, HE-AAC v2|AAC-LC, HE-AAC v1, HE-AAC v2|
| Dane wyjściowe obsługiwanych koderów-dekoderów audio|Takie same jak dane wejściowe|AAC-LC|
| Maksymalna rozdzielczość wideo wyjścia wideo|Takie same jak dane wejściowe|720p (o 30 klatek na sekundę)|
| Protokoły danych wejściowych|RTMP, fragmentacji — w formacie MP4 (Smooth Streaming)|RTMP, fragmentacji — w formacie MP4 (Smooth Streaming)|
| Cena|Zobacz [stronę z cennikiem](https://azure.microsoft.com/pricing/details/media-services/) i kliknij kartę "Wideo na żywo"|Zobacz [stronę z cennikiem](https://azure.microsoft.com/pricing/details/media-services/) i kliknij kartę "Wideo na żywo"|
| Maksymalny czas działania| 24 godz. x 365 dni na żywo liniowego | Maksymalnie 24 godziny|
| Możliwość przekazywania napisy kodowane CEA za pośrednictwem osadzonego 608/708 etykiety danych|Yes|Yes|
| Obsługa wstawiania plansz|Nie|Nie|
| Obsługa ad sygnalizowanie za pośrednictwem interfejsu API| Nie|Nie|
| Obsługa ad sygnalizowanie za pośrednictwem SCTE 35 wewnątrzpasmowe wiadomości|Yes|Yes|
| Zdolność odzyskania po krótkie wstrzymania w udziale kanału informacyjnego|Yes|Nie (wydarzenie na żywo rozpocznie się slating ponad 6 sekund bez danych wejściowych)|
| Obsługa niejednolitego GOPs danych wejściowych|Yes|Nie — dane wejściowe muszą naprawiona GOP czas trwania|
| Obsługa dane wejściowe szybkość klatek zmiennej|Yes|Nie — dane wejściowe muszą zostać usunięte, szybkości klatek. Niewielkich zmian są dopuszczalne na przykład podczas sceny wysokiej ruchu. Ale wkład źródła danych nie można porzucić szybkość klatek (na przykład do 15 klatek na sekundę).|
| Auto bliskie zdarzenia na żywo, gdy dane wejściowe źródła danych zostaną utracone|Nie|Po 12 godzinach, jeśli nie ma żadnych LiveOutput uruchamiania|

## <a name="system-presets"></a>Ustawienia systemu

W przypadku korzystania z kodowania na żywo (wydarzenie na żywo z ustawieniem **Standardowy**) ustawienie wstępne kodowania definiuje sposób kodowania przychodzącego strumienia w wielu różnych szybkościach transmisji bitów lub warstwach. Obecnie jedyną dozwoloną wartość dla ustawienia wstępnego jest *Default720p* (ustawienie domyślne).

**Default720p** będzie kodowania wideo na następujące warstwy 6.

### <a name="output-video-stream"></a>Dane wyjściowe Stream wideo

| Szybkość transmisji bitów | Szerokość | Wysokość | MaxFPS | Profil | Nazwa Stream wyjściowego |
| --- | --- | --- | --- | --- | --- |
| 3500 |1280 |720 |30 |Wysoka |Video_1280x720_3500kbps |
| 2200 |960 |540 |30 |Wysoka |Video_960x540_2200kbps |
| 1350 |704 |396 |30 |Wysoka |Video_704x396_1350kbps |
| 850 |512 |288 |30 |Wysoka |Video_512x288_850kbps |
| 550 |384 |216 |30 |Wysoka |Video_384x216_550kbps |
| 200 |340 |192 |30 |Wysoka |Video_340x192_200kbps |

> [!NOTE]
> Jeśli chcesz użyć niestandardowego ustawienia wstępnego kodowania na żywo, wyślij wiadomość na adres amshelp@microsoft.com. Musisz określić odpowiednią tabelę z rozdzielczością i szybkościami transmisji bitów. Sprawdź, czy istnieje tylko jedna warstwa dla rozdzielczości 720p i maksymalnie 6 warstw.

### <a name="output-audio-stream"></a>Dane wyjściowe Audio Stream

Dźwięk jest zakodowany w celu stereo AAC-LC na 128 Kb/s, częstotliwość próbkowania kHz 48.

## <a name="next-steps"></a>Kolejne kroki

[Omówienie transmisji strumieniowej na żywo](live-streaming-overview.md)
