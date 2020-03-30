---
title: Typy liveevent usługi Azure Media Services | Dokumenty firmy Microsoft
description: W usłudze Azure Media Services zdarzenie na żywo można ustawić na kodowanie *przekazywane* lub *na żywo.* W tym artykule przedstawiono szczegółową tabelę, która porównuje typy zdarzeń na żywo.
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
ms.openlocfilehash: a28d4d96f643c12eeb6aa542db2c6af06f4fd954
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78244640"
---
# <a name="live-event-types-comparison"></a>Porównanie typów zdarzeń na żywo

W usłudze Azure Media Services [zdarzenie na żywo](https://docs.microsoft.com/rest/api/media/liveevents) można ustawić na *przekazywanie* (lokalny koder na żywo wysyła strumień wielu bitrate) lub *kodowanie* na żywo (lokalny koder na żywo wysyła pojedynczy strumień szybkości transmisji bitów). 

W tym artykułach porównano funkcje typów zdarzeń na żywo.

## <a name="types-comparison"></a>Porównanie typów 

W poniższej tabeli porównano funkcje typów zdarzeń na żywo. Typy są ustawiane podczas tworzenia przy użyciu [LiveEventEncodingType:](https://docs.microsoft.com/rest/api/media/liveevents/create#liveeventencodingtype)

* **LiveEventEncodingType.None** — lokalny koder na żywo wysyła strumień wielu szybkości transmisji bitów. Pozyskane strumienie przechodzą przez wydarzenie na żywo bez dalszego przetwarzania. Nazywane również wydarzeniem na żywo.
* **LiveEventEncodingType.Standard** — lokalny koder na żywo wysyła pojedynczy strumień szybkości transmisji bitów do usługi Live Event, a usługa Media Services tworzy wiele strumieni szybkości transmisji bitów. Jeśli plik danych o udziale ma rozdzielczość 720p lub wyższą, ustawienie predefiniowane **Default720p** zakoduje zestaw par 6 rozdzielczości/szybkości transmisji bitów (szczegóły można śledzić w dalszej części artykułu).
* **LiveEventEncodingType.Premium1080p** — lokalny koder na żywo wysyła pojedynczy strumień szybkości transmisji bitów do usługi Live Event, a usługa Media Services tworzy wiele strumieni szybkości transmisji bitów. Ustawienie predefiniowane Default1080p określa wyjściowy zestaw par rozdzielczości/szybkości transmisji bitów (szczegóły znajdują się w dalszej części artykułu). 

| Funkcja | Wydarzenie na żywo | Standardowe lub premium1080p wydarzenie na żywo |
| --- | --- | --- |
| Pojedyncze dane wejściowe o szybkości transmisji bitów są kodowane do wielu szybkości transmisji bitów w chmurze |Nie |Tak |
| Maksymalna rozdzielczość wideo dla kanału wsuwu |4K (4096x2160 przy 60 klatkach na sekundę) |1080p (1920x1088 przy 30 klatkach na sekundę)|
| Zalecane maksymalne warstwy w kanale insekcjowym|Do 12|Jeden dźwięk|
| Maksymalna liczba warstw na wyjściu| Tak samo jak dane wejściowe|Do 6 (patrz Ustawienia systemu poniżej)|
| Maksymalna łączna przepustowość kanału informacyjnego|60 Mb/s|Nie dotyczy|
| Maksymalna szybkość transmisji bitów dla pojedynczej warstwy wkładu |20 Mb/s|20 Mb/s|
| Obsługa wielu ścieżek audio w języku|Tak|Nie|
| Obsługiwane kodeki wideo |H.264/AVC i H.265/HEVC|H.264/AVC|
| Obsługiwane kodeki wideo wyjściowe|Tak samo jak dane wejściowe|H.264/AVC|
| Obsługiwana głębia bitowa wideo, wejście i wyjście|Do 10-bitowe z HDR 10/HLG|8-bitowy|
| Obsługiwane kodeki audio wejściowe|AAC-LC, HE-AAC v1, HE-AAC v2|AAC-LC, HE-AAC v1, HE-AAC v2|
| Obsługiwane kodeki audio wyjściowe|Tak samo jak dane wejściowe|AAC-LC|
| Maksymalna rozdzielczość wideo wyjściowego wideo|Tak samo jak dane wejściowe|Standard - 720p, Premium1080p - 1080p|
| Maksymalna liczba klatek na sekundę wejściowego wideo|60 klatek na sekundę|Standard lub Premium1080p - 30 klatek na sekundę|
| Protokoły wejściowe|RTMP, rozdrobniony-MP4 (płynne przesyłanie strumieniowe)|RTMP, rozdrobniony-MP4 (płynne przesyłanie strumieniowe)|
| Price|Zobacz [stronę z cennikiem](https://azure.microsoft.com/pricing/details/media-services/) i kliknij kartę "Wideo na żywo"|Zobacz [stronę z cennikiem](https://azure.microsoft.com/pricing/details/media-services/) i kliknij kartę "Wideo na żywo"|
| Maksymalny czas wykonywania| 24 godziny x 365 dni, liniowy na żywo | 24 godziny x 365 dni, liniowy na żywo (podgląd)|
| Możliwość przechodzenia przez osadzone dane podpisów CEA 608/708|Tak|Tak|
| Możliwość włączenia transkrypcji na żywo|Tak|Tak|
| Obsługa wstawiania łupków|Nie|Nie|
| Obsługa sygnalizacji reklam za pośrednictwem interfejsu API| Nie|Nie|
| Obsługa sygnalizacji reklamowej za pośrednictwem wiadomości w paśmie SCTE-35|Tak|Tak|
| Możliwość odzyskania sił po krótkich straganach w paszy|Tak|Częściowo|
| Obsługa nieujemnych wejściowych gopów|Tak|Nie – wejście musi mieć stały czas trwania GOP|
| Obsługa wprowadzania zmiennej liczby klatek na sekundę|Tak|Nie – wejście musi być ustaloną szybkością klatek na sekundę. Niewielkie różnice są tolerowane, na przykład podczas scen o dużym ruchu. Ale kanał wkładu nie może obniżyć liczby klatek na sekundę (na przykład do 15 klatek na sekundę).|
| Automatyczne wyłączania zdarzenia na żywo po utracie źródła danych wejściowych|Nie|Po 12 godzinach, jeśli nie działa LiveOutput|

## <a name="system-presets"></a>Ustawienia wstępne systemu

Rozdzielczości i szybkości transmisji bitów zawarte w danych wyjściowych z kodera na żywo są określane przez [presetName](https://docs.microsoft.com/rest/api/media/liveevents/create#liveeventencoding). W przypadku korzystania ze **standardowego** kodera na żywo (LiveEventEncodingType.Standard), ustawienie predefiniowane *Default720p* określa zestaw 6 par rozdzielczości/szybkości transmisji bitów opisanych poniżej. W przeciwnym razie, jeśli używasz **kodera premium1080p** na żywo (LiveEventEncodingType.Premium1080p), a następnie *default1080p* preset określa wyjściowy zestaw par rozdzielczości/szybkości transmisji bitów.

> [!NOTE]
> Nie można zastosować ustawienia domyślnego1080p do zdarzenia na żywo, jeśli zostało ono skonfigurowane dla standardowego kodowania na żywo — zostanie wyświetlony błąd. Pojawi się również błąd, jeśli spróbujesz zastosować ustawienie domyślne720p do kodera premium1080p na żywo.

### <a name="output-video-streams-for-default720p"></a>Wyjściowe strumienie wideo dla default720p

Jeśli plik danych o wkładzie ma rozdzielczość 720p lub wyższą, ustawienie **domyślne720p** zakoduje kanał informacyjny w następujących warstwach 6. W poniższej tabeli Szybkość transmisji bitów jest w kb/s, MaxFPS reprezentuje maksymalną dozwoloną liczbę klatek (w klatkach/sekundę), Profil reprezentuje używany profil H.264.

| Bitrate | impulsów | Właściwość Height | Maksymalna liczba klatek na sekundę | Profil |
| --- | --- | --- | --- | --- |
| 3500 |1280 |720 |30 |Wysoka |
| 2200 |960 |540 |30 |Wysoka |
| 1350 |704 |396 |30 |Wysoka |
| 850 |512 |288 |30 |Wysoka |
| 550 |384 |216 |30 |Wysoka |
| 200 |340 |192 |30 |Wysoka |

> [!NOTE]
> Jeśli chcesz dostosować ustawienia predefiniowane kodowania na żywo, otwórz bilet pomocy technicznej za pośrednictwem witryny Azure Portal. Musisz określić odpowiednią tabelę z rozdzielczością i szybkościami transmisji bitów. Sprawdź, czy istnieje tylko jedna warstwa dla rozdzielczości 720p i maksymalnie 6 warstw. Należy również określić, że żądasz predefiniowane dla standardowego kodera na żywo.
> Określone wartości szybkości transmisji bitów i rozdzielczości mogą być dostosowywane w czasie

### <a name="output-video-streams-for-default1080p"></a>Wyjściowe strumienie wideo dla default1080p

Jeśli plik danych o udziale ma rozdzielczość 1080p, ustawienie **domyślne 1080p** zakoduje kanał informacyjny w następujących warstwach 6.

| Bitrate | impulsów | Właściwość Height | Maksymalna liczba klatek na sekundę | Profil |
| --- | --- | --- | --- | --- |
| 5500 |1920 |1080 |30 |Wysoka |
| 3000 |1280 |720 |30 |Wysoka |
| 1600 |960 |540 |30 |Wysoka |
| 800 |640 |360 |30 |Wysoka |
| 400 |480 |270 |30 |Wysoka |
| 200 |320 |180 |30 |Wysoka |

> [!NOTE]
> Jeśli chcesz dostosować ustawienia predefiniowane kodowania na żywo, otwórz bilet pomocy technicznej za pośrednictwem witryny Azure Portal. Musisz określić odpowiednią tabelę z rozdzielczością i szybkościami transmisji bitów. Sprawdź, czy w rozdzielczości 1080p jest tylko jedna warstwa, a co najwyżej 6 warstw. Należy również określić, że żądasz predefiniowane dla premium1080p koder na żywo.
> Określone wartości szybkości transmisji bitów i rozdzielczości mogą być dostosowywane w czasie.

### <a name="output-audio-stream-for-default720p-and-default1080p"></a>Wyjściowy strumień audio dla default720p i default1080p

W przypadku ustawień domyślnych *720p* i *Default1080p* dźwięk jest kodowany do stereo AAC-LC z prędkością 128 kb/s. Częstotliwość próbkowania wynika z poziomu ścieżki dźwiękowej w paszy wkładu.

## <a name="implicit-properties-of-the-live-encoder"></a>Niejawne właściwości kodera na żywo

W poprzedniej sekcji opisano właściwości kodera na żywo, które mogą być kontrolowane jawnie, za pomocą predefiniowanych — takich jak liczba warstw, rozdzielczości i szybkości transmisji bitów. W tej sekcji wyjaśniono właściwości niejawne.

### <a name="group-of-pictures-gop-duration"></a>Czas trwania grupy zdjęć (GOP)

Koder na żywo podąża za strukturą [GOP](https://en.wikipedia.org/wiki/Group_of_pictures) kanału informacyjnego - co oznacza, że warstwy wyjściowe będą miały ten sam czas trwania GOP. W związku z tym zaleca się skonfigurowanie kodera lokalnego do tworzenia źródła danych o składku, który ma stały czas trwania GOP (zazwyczaj 2 sekundy). Zapewni to, że wychodzące strumienie HLS i MPEG DASH z usługi mają również stałe czasy trwania GOP. Większość urządzeń może tolerować niewielkie różnice w czasie trwania gop.

### <a name="frame-rate"></a>Klatek

Koder na żywo śledzi również czas trwania poszczególnych klatek wideo w kanale insekcjowym - co oznacza, że warstwy wyjściowe będą miały klatki o tym samym czasie trwania. W związku z tym zaleca się skonfigurowanie kodera lokalnego w celu uzyskania źródła danych o insekcji ze stałą szybkością klatek (najwyżej 30 klatek na sekundę). Zapewni to, że wychodzące strumienie HLS i MPEG DASH z usługi mają również stałe czasy trwania klatek. Większość urządzeń może tolerować niewielkie różnice w szybkości klatek, ale nie ma gwarancji, że koder na żywo będzie produkował dane wyjściowe, które będzie odtwarzane poprawnie. Lokalny koder na żywo nie powinien upuszczać ramek (np. w warunkach niskiego poziomu naładowania baterii) lub w jakikolwiek sposób zmieniać liczbę klatek na sekundę.

### <a name="resolution-of-contribution-feed-and-output-layers"></a>Rozdzielczość warstw paszowych i wyjściowych

Koder na żywo jest skonfigurowany tak, aby uniknąć upconverting źródła wkładu. W rezultacie maksymalna rozdzielczość warstw wyjściowych nie przekroczy rozdzielczości kanału informacyjnego.

Na przykład, jeśli wyślesz kanał wpłaty w rozdzielczości 720p do zdarzenia na żywo skonfigurowanego do kodowania na żywo Default1080p, dane wyjściowe będą miały tylko 5 warstw, począwszy od 720p przy 3Mbps, zejściem do 1080p przy 200 kbps. Jeśli wyślesz kanał wpłaty w rozdzielczości 360p do zdarzenia na żywo skonfigurowanego do standardowego kodowania na żywo, dane wyjściowe będą zawierać 3 warstwy (w rozdzielczościach 288p, 216p i 192p). W przypadku degeneracji, jeśli wyślesz kanał wkładu, powiedzmy, 160x90 pikseli do standardowego kodera na żywo, dane wyjściowe będą zawierać jedną warstwę o rozdzielczości 160x90 przy tej samej szybkości transmisji bitów co kanał wkładu.

### <a name="bitrate-of-contribution-feed-and-output-layers"></a>Szybkość transmisji bitów warstw paszowych i wyjściowych

Koder na żywo jest skonfigurowany tak, aby szanował ustawienia szybkości transmisji bitów w ustawieniach predefiniowanych, niezależnie od szybkości transmisji bitów kanału wkładu. W rezultacie szybkość transmisji bitów warstw wyjściowych może przekraczać szybkość podawania. Na przykład jeśli wyślesz kanał wkładu w rozdzielczości 720p przy 1 Mbps, warstwy wyjściowe pozostaną takie same jak w powyższej [tabeli.](live-event-types-comparison.md#output-video-streams-for-default720p)

## <a name="next-steps"></a>Następne kroki

[Omówienie transmisji na żywo](live-streaming-overview.md)
