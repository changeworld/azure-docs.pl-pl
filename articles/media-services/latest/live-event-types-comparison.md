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
ms.openlocfilehash: bd4899374c06246ddd4d5fa81d0f6e3a6a1e7017
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67075008"
---
# <a name="live-event-types-comparison"></a>Porównanie typów zdarzeń na żywo

W usłudze Azure Media Services [wydarzenie na żywo](https://docs.microsoft.com/rest/api/media/liveevents) może być jednym z dwóch typów: kodowanie na żywo oraz pass-through. 

## <a name="types-comparison"></a>Porównanie typów 

W poniższej tabeli porównano funkcje typy wydarzenie na żywo.

| Cecha | Przekazywane zdarzenia na żywo | Standardowy lub Premium1080p zdarzenia na żywo |
| --- | --- | --- |
| Pojedyncza szybkość transmisji bitów w danych wejściowych jest zakodowany do wielokrotnych w chmurze |Nie |Tak |
| Maksymalna rozdzielczość wideo dla udziału kanału informacyjnego |4K (na 60 klatek na sekundę 4096 x 2160) |1080p (1920 x 1088 na 30 klatek na sekundę)|
| Zalecana maksymalna warstwy w udziale kanału informacyjnego|Do 12|Jedną ścieżkę dźwiękową|
| Maksymalna warstwy w danych wyjściowych| Takie same jak dane wejściowe|Do 6 (zobacz poniżej ustawienia systemu)|
| Maksymalna przepustowość agregacji wkładu kanału informacyjnego|60 MB/s|ND|
| Maksymalna szybkość transmisji bitów dla pojedynczej warstwy w udziale |20 MB/s|20 MB/s|
| Obsługa wielu języka ścieżki audio|Tak|Nie|
| Obsługiwane wejściowych koderów-dekoderów wideo |Koder H.264/AVC i H.265/HEVC|KODER H.264/AVC|
| Dane wyjściowe obsługiwanych koderów-dekoderów wideo|Takie same jak dane wejściowe|KODER H.264/AVC|
| Obsługiwana głębokość bitów wideo, dane wejściowe i wyjściowe|Maksymalnie 10-bitowy tym HDR 10/HLG|8-bitowa|
| Obsługiwane wejściowe audio kodery-dekodery|AAC-LC, HE-AAC v1, HE-AAC v2|AAC-LC, HE-AAC v1, HE-AAC v2|
| Dane wyjściowe obsługiwanych koderów-dekoderów audio|Takie same jak dane wejściowe|AAC-LC|
| Maksymalna rozdzielczość wideo wyjścia wideo|Takie same jak dane wejściowe|W warstwie standardowa — 720p Premium1080p - 1080p|
| Maksymalna szybkość wejściowy plik wideo|60 klatek na sekundę|Standardowy lub Premium1080p - 30 klatek/sekundę|
| Protokoły danych wejściowych|RTMP, fragmentacji — w formacie MP4 (Smooth Streaming)|RTMP, fragmentacji — w formacie MP4 (Smooth Streaming)|
| Cena|Zobacz [stronę z cennikiem](https://azure.microsoft.com/pricing/details/media-services/) i kliknij kartę "Wideo na żywo"|Zobacz [stronę z cennikiem](https://azure.microsoft.com/pricing/details/media-services/) i kliknij kartę "Wideo na żywo"|
| Maksymalny czas działania| 24 godz. x 365 dni na żywo liniowego | Maksymalnie 24 godziny|
| Możliwość przekazywania napisy kodowane CEA za pośrednictwem osadzonego 608/708 etykiety danych|Yes|Yes|
| Obsługa wstawiania plansz|Nie|Nie|
| Obsługa ad sygnalizowanie za pośrednictwem interfejsu API| Nie|Nie|
| Obsługa ad sygnalizowanie za pośrednictwem SCTE 35 wewnątrzpasmowe wiadomości|Tak|Tak|
| Zdolność odzyskania po krótkie wstrzymania w udziale kanału informacyjnego|Yes|Częściowe|
| Obsługa niejednolitego GOPs danych wejściowych|Tak|Nie — dane wejściowe muszą naprawiona GOP czas trwania|
| Obsługa dane wejściowe szybkość klatek zmiennej|Tak|Nie — dane wejściowe muszą zostać usunięte, szybkości klatek. Niewielkich zmian są dopuszczalne na przykład podczas sceny wysokiej ruchu. Ale wkład źródła danych nie można porzucić szybkość klatek (na przykład do 15 klatek na sekundę).|
| Auto bliskie zdarzenia na żywo, gdy dane wejściowe źródła danych zostaną utracone|Nie|Po 12 godzinach, jeśli nie ma żadnych LiveOutput uruchamiania|

## <a name="system-presets"></a>Ustawienia systemu

Rozdzielczości i szybkości transmisji, zawarte w danych wyjściowych z kodera na żywo jest określana przez [presetName](https://docs.microsoft.com/rest/api/media/liveevents/create#liveeventencoding). Korzystania z **standardowa** live encoder (LiveEventEncodingType.Standard), a następnie *Default720p* ustawienie wstępne określa zestaw par 6 rozpoznawania/bitowy współczynnik opisane poniżej. W przeciwnym razie, jeśli za pomocą **Premium1080p** live encoder (LiveEventEncodingType.Premium1080p), a następnie *Default1080p* specifiesthe wstępnie zdefiniowanych danych wyjściowych zestaw par szybkości rozwiązywania/bitowe. 

### <a name="output-video-streams-for-default720p"></a>Default720p danych wyjściowych strumieni wideo

**Default720p** będzie zakodować wejściowy plik wideo do następujących warstw 6.

| Szybkość transmisji bitów | Szerokość | Wysokość | MaxFPS | Profil | Nazwa Stream wyjściowego |
| --- | --- | --- | --- | --- | --- |
| 3500 |1280 |720 |30 |Wysoka |Video_1280x720_3500kbps |
| 2200 |960 |540 |30 |Wysoka |Video_960x540_2200kbps |
| 1350 |704 |396 |30 |Wysoka |Video_704x396_1350kbps |
| 850 |512 |288 |30 |Wysoka |Video_512x288_850kbps |
| 550 |384 |216 |30 |Wysoka |Video_384x216_550kbps |
| 200 |340 |192 |30 |Wysoka |Video_340x192_200kbps |

> [!NOTE]
> Jeśli trzeba dostosować ustawienia wstępne kodowania na żywo, otwórz bilet pomocy technicznej za pośrednictwem witryny Azure Portal. Musisz określić odpowiednią tabelę z rozdzielczością i szybkościami transmisji bitów. Sprawdź, czy istnieje tylko jedna warstwa dla rozdzielczości 720p i maksymalnie 6 warstw. Również określić, czy żądasz ustawienie wstępne standardowego kodera na żywo.

### <a name="output-video-streams-for-default1080p"></a>Default1080p danych wyjściowych strumieni wideo

**Default1080p** będzie zakodować wejściowy plik wideo do następujących warstw 6.

| Szybkość transmisji bitów | Szerokość | Wysokość | MaxFPS | Profil | Nazwa Stream wyjściowego |
| --- | --- | --- | --- | --- | --- |
| 5500 |1920 |1080 |30 |Wysoka |Video_1920x1080_5500kbps |
| 3000 |1280 |720 |30 |Wysoka |Video_1280x720_3000kbps |
| 1600 |960 |540 |30 |Wysoka |Video_960x540_1600kbps |
| 800 |640 |360 |30 |Wysoka |Video_640x360_800kbps |
| 400 |480 |270 |30 |Wysoka |Video_480x270_400kbps |
| 200 |320 |180 |30 |Wysoka |Video_320x180_200kbps |

> [!NOTE]
> Jeśli trzeba dostosować ustawienia wstępne kodowania na żywo, otwórz bilet pomocy technicznej za pośrednictwem witryny Azure Portal. Musisz określić odpowiednią tabelę z rozdzielczością i szybkościami transmisji bitów. Sprawdź, czy jest tylko jedna warstwa 1080 p i maksymalnie 6 warstwy. Również określić, że żądasz ustawienie wstępne Premium1080p koder na żywo.

### <a name="output-audio-stream-for-default720p-and-default1080p"></a>Dane wyjściowe Audio Stream Default720p i Default1080p

Dla obu *Default720p* i *Default1080p* wstępne audio jest zakodowany w celu stereo AAC-LC na 128 Kb/s, współczynnik 48 kHz pobierania próbek.

## <a name="next-steps"></a>Kolejne kroki

[Omówienie transmisji strumieniowej na żywo](live-streaming-overview.md)
