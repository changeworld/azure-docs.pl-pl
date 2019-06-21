---
title: Azure Media Services w element LiveEvent typy | Dokumentacja firmy Microsoft
description: W tym artykule przedstawiono szczegółowe tabelę, która porównuje typy element LiveEvent.
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
ms.date: 06/13/2019
ms.author: juliako
ms.openlocfilehash: 93f01513841d1174fea796f1615ab05df0a41af4
ms.sourcegitcommit: 6e6813f8e5fa1f6f4661a640a49dc4c864f8a6cb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/17/2019
ms.locfileid: "67150378"
---
# <a name="live-event-types-comparison"></a>Porównanie typów zdarzeń na żywo

W usłudze Azure Media Services [wydarzenie na żywo](https://docs.microsoft.com/rest/api/media/liveevents) może być jednym z dwóch typów: kodowanie na żywo oraz pass-through. 

## <a name="types-comparison"></a>Porównanie typów 

W poniższej tabeli porównano funkcje typy wydarzenie na żywo. Typy są ustawiane podczas tworzenia przy użyciu [LiveEventEncodingType](https://docs.microsoft.com/rest/api/media/liveevents/create#liveeventencodingtype):

* **LiveEventEncodingType.None** — lokalny koder na żywo wysyła strumień wielu szybkości transmisji bitów. Pozyskiwane strumienie przechodzi przez wydarzenie na żywo bez dalszego przetwarzania. 
* **LiveEventEncodingType.Standard** — w środowisku lokalnym koder na żywo wysyła strumień o pojedynczej szybkości transmisji bitów do wydarzenie na żywo i Media Services tworzy wiele strumieni szybkości transmisji bitów. Jeśli źródło danych wkład jest 720 lub wyższa rozdzielczość **Default720p** ustawienie wstępne będzie kodowanie zestaw par rozpoznawania/szybkości transmisji bitów 6 (szczegóły wykonaj w dalszej części tego artykułu).
* **LiveEventEncodingType.Premium1080p** — w środowisku lokalnym koder na żywo wysyła strumień o pojedynczej szybkości transmisji bitów do wydarzenie na żywo i Media Services tworzy wiele strumieni szybkości transmisji bitów. Ustawienie wstępne Default1080p określa zestaw danych wyjściowych pary rozpoznawania/szybkości transmisji bitów (szczegóły wykonaj w dalszej części tego artykułu). 

| Cecha | Przekazywane zdarzenia na żywo | Standardowy lub Premium1080p zdarzenia na żywo |
| --- | --- | --- |
| Pojedyncza szybkość transmisji bitów w danych wejściowych jest zakodowany do wielokrotnych w chmurze |Nie |Yes |
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
| Możliwość przekazywania napisy kodowane CEA za pośrednictwem osadzonego 608/708 etykiety danych|Tak|Tak|
| Obsługa wstawiania plansz|Nie|Nie|
| Obsługa ad sygnalizowanie za pośrednictwem interfejsu API| Nie|Nie|
| Obsługa ad sygnalizowanie za pośrednictwem SCTE 35 wewnątrzpasmowe wiadomości|Yes|Tak|
| Zdolność odzyskania po krótkie wstrzymania w udziale kanału informacyjnego|Tak|Częściowe|
| Obsługa niejednolitego GOPs danych wejściowych|Tak|Nie — dane wejściowe muszą naprawiona GOP czas trwania|
| Obsługa dane wejściowe szybkość klatek zmiennej|Yes|Nie — dane wejściowe muszą zostać usunięte, szybkości klatek. Niewielkich zmian są dopuszczalne na przykład podczas sceny wysokiej ruchu. Ale wkład źródła danych nie można porzucić szybkość klatek (na przykład do 15 klatek na sekundę).|
| Auto bliskie zdarzenia na żywo, gdy dane wejściowe źródła danych zostaną utracone|Nie|Po 12 godzinach, jeśli nie ma żadnych LiveOutput uruchamiania|

## <a name="system-presets"></a>Ustawienia systemu

Rozdzielczości i szybkości transmisji, zawarte w danych wyjściowych z kodera na żywo są określane przez [presetName](https://docs.microsoft.com/rest/api/media/liveevents/create#liveeventencoding). Jeśli przy użyciu **Standard** live encoder (LiveEventEncodingType.Standard), a następnie *Default720p* ustawienie wstępne określa zestaw par rozpoznawania/szybkości transmisji bitów 6 opisane poniżej. W przeciwnym razie, jeśli za pomocą **Premium1080p** live encoder (LiveEventEncodingType.Premium1080p), a następnie *Default1080p* ustawienie wstępne określa zestaw danych wyjściowych pary rozpoznawania/szybkości transmisji bitów.

> [!NOTE]
> Nie można zastosować Default1080p wstępnie ustawione to zdarzenie na żywo, jeśli jego została skonfigurowana standardowa kodowanie na żywo — otrzymają komunikat o błędzie. Możesz również otrzymają komunikat o błędzie, Jeśli spróbujesz zastosować Default720p predefiniowany Premium1080p koder na żywo.

### <a name="output-video-streams-for-default720p"></a>Default720p danych wyjściowych strumieni wideo

Jeśli źródło danych wkład jest 720 lub wyższa rozdzielczość **Default720p** ustawienie wstępne koduje nowe źródło danych w następujących warstwach 6. W poniższej tabeli, szybkości transmisji bitów w KB/s, MaxFPS reprezentuje ten maksymalny dozwolony szybkość (w klatek na sekundę), profil reprezentuje profil H.264 używany.

| Szybkość transmisji bitów | Szerokość | Wysokość | MaxFPS | Profil |
| --- | --- | --- | --- | --- |
| 3500 |1280 |720 |30 |Wysoka |
| 2200 |960 |540 |30 |Wysoka |
| 1350 |704 |396 |30 |Wysoka |
| 850 |512 |288 |30 |Wysoka |
| 550 |384 |216 |30 |Wysoka |
| 200 |340 |192 |30 |Wysoka |

> [!NOTE]
> Jeśli trzeba dostosować ustawienia wstępne kodowania na żywo, otwórz bilet pomocy technicznej za pośrednictwem witryny Azure Portal. Musisz określić odpowiednią tabelę z rozdzielczością i szybkościami transmisji bitów. Sprawdź, czy istnieje tylko jedna warstwa dla rozdzielczości 720p i maksymalnie 6 warstw. Również określić, czy żądasz ustawienie wstępne standardowego kodera na żywo.
> Określone wartości różnych i rozdzielczości mogą być dostosowywane wraz z upływem czasu

### <a name="output-video-streams-for-default1080p"></a>Default1080p danych wyjściowych strumieni wideo

Jeśli jest źródło wkład w rozdzielczości 1080p **Default1080p** ustawienie wstępne koduje nowe źródło danych w następujących warstwach 6.

| Szybkość transmisji bitów | Szerokość | Wysokość | MaxFPS | Profil |
| --- | --- | --- | --- | --- |
| 5500 |1920 |1080 |30 |Wysoka |
| 3000 |1280 |720 |30 |Wysoka |
| 1600 |960 |540 |30 |Wysoka |
| 800 |640 |360 |30 |Wysoka |
| 400 |480 |270 |30 |Wysoka |
| 200 |320 |180 |30 |Wysoka |

> [!NOTE]
> Jeśli trzeba dostosować ustawienia wstępne kodowania na żywo, otwórz bilet pomocy technicznej za pośrednictwem witryny Azure Portal. Musisz określić odpowiednią tabelę z rozdzielczością i szybkościami transmisji bitów. Sprawdź, czy jest tylko jedna warstwa 1080 p i maksymalnie 6 warstwy. Również określić, że żądasz ustawienie wstępne Premium1080p koder na żywo.
> Określone wartości różnych i rozdzielczości mogą być dostosowywane wraz z upływem czasu.

### <a name="output-audio-stream-for-default720p-and-default1080p"></a>Dane wyjściowe Audio Stream Default720p i Default1080p

Dla obu *Default720p* i *Default1080p* wstępne audio jest zakodowany w celu stereo AAC-LC na 128 Kb/s. Częstotliwość próbkowania jest zgodna z ścieżki audio w udziale, źródła danych.

## <a name="implicit-properties-of-the-live-encoder"></a>Niejawne właściwości koder na żywo

Właściwości koder na żywo, które mogą być jawnie kontrolowane za pomocą ustawienia wstępnego — takie jak liczba warstw, rozdzielczości i szybkości transmisji opisano w poprzedniej sekcji. W tej sekcji wyjaśnia niejawne właściwości.

### <a name="group-of-pictures-gop-duration"></a>Grupy obrazów (GOP) czasu trwania

Koder na żywo następuje [GOP](https://en.wikipedia.org/wiki/Group_of_pictures) struktury wkładu, źródła danych — oznacza, że warstwy danych wyjściowych będzie miał ten sam czas trwania GOP. W związku z tym zalecane jest, aby skonfigurować lokalny koder w celu utworzenia udziału kanału informacyjnego ma ustaloną GOP czas trwania (zwykle 2 sekundy). Pozwoli to zagwarantować, że strumieni HLS i MPEG DASH wychodzące z usługi również ma ustaloną GOP czasów trwania. Małe różnice w czasie trwania GOP prawdopodobnie może być tolerowana przez większość urządzeń.

### <a name="frame-rate"></a>Szybkość klatek

Koder na żywo jest również zgodna czasów trwania poszczególnych klatki wideo w udziale źródła danych — co oznacza, że warstwy danych wyjściowych będzie mieć ramek o tym samym czasie trwania. Z tego powodu zaleca się konfigurowania koder środowiska lokalnego do produkcji wkład źródła danych, który ma ustaloną szybkość klatek (maksymalnie 30 klatek na sekundę). Pozwoli to zagwarantować, że strumieni HLS i MPEG DASH wychodzące z usługi również ma ustaloną czasów trwania szybkości klatek. Małe zmiany szybkości klatek może być tolerowana przez większość urządzeń, ale nie ma żadnej gwarancji, że koder na żywo generuje danych wyjściowych do odtworzenia. Koder na żywo w środowisku lokalnym powinna nie być porzuca ramek (np.) w warunkach niskiego poziomu baterii) lub zróżnicowanie szybkość klatek w dowolny sposób.

### <a name="resolution-of-contribution-feed-and-output-layers"></a>Rozpoznawanie wkładu źródła danych, a dane wyjściowe warstwy

Koder na żywo jest skonfigurowany w celu uniknięcia upconverting wkład źródła danych. W rezultacie maksymalną rozdzielczość warstwy danych wyjściowych nie przekroczy, udział, źródła danych.

Na przykład Jeśli wyślesz wkład kanału informacyjnego 720 p, aby skonfigurowane dla Default1080p wydarzenie na żywo funkcji live encoding, dane wyjściowe będą mieć tylko 5 warstw, poczynając od 720p na 3 MB/s, przechodząc w dół, 1080 p w elemencie 200 KB/s. Lub Jeśli wyślesz kanału informacyjnego 360 p wkład do wydarzenie na żywo skonfigurowane dla standardowych kodowanie na żywo, dane wyjściowe będą zawierać warstwy 3 (w rozdzielczości 288p, 216p i 192p). W przypadku wymiaru degeneracji Jeśli wyślesz wkład Kanał informacyjny, powiedz, 160 x 90 pikseli do standardowego kodera na żywo dane wyjściowe będą zawierać jedną warstwę w rozdzielczości 160 x 90 w tej samej szybkości transmisji bitów jak w przypadku udziału źródła danych.

### <a name="bitrate-of-contribution-feed-and-output-layers"></a>Szybkość transmisji bitów wkładu źródła danych, a dane wyjściowe warstwy

Koder na żywo jest skonfigurowany do uznaje ustawień szybkości transmisji bitów w ustawienie wstępne, niezależnie od szybkości transmisji bitów wkładu źródła danych. W rezultacie szybkości transmisji bitów warstw danych wyjściowych może przekroczyć wkładu źródła danych. Na przykład, jeśli wyślesz w udziale, źródła danych z rozdzielczością 720p 1 MB/s, warstwy danych wyjściowych pozostaną takie same jak w [tabeli](live-event-types-comparison.md#output-video-streams-for-default720p) powyżej.

## <a name="next-steps"></a>Kolejne kroki

[Omówienie transmisji strumieniowej na żywo](live-streaming-overview.md)
