---
title: Porównanie Azure na żądanie nośnika koderów | Dokumentacja firmy Microsoft
description: W tym temacie porównuje kodowania możliwości **Media Encoder Standard** i **Media Encoder Premium w przepływie pracy**.
services: media-services
documentationcenter: ''
author: juliako
manager: cfowler
editor: ''
ms.assetid: a79437c0-4832-423a-bca8-82632b2c47cc
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/05/2017
ms.author: juliako;anilmur
ms.openlocfilehash: cf420c6b5f72b2109016bdb8b86d6cfcc506f4e5
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34639524"
---
# <a name="comparison-of-azure-on-demand-media-encoders"></a>Porównanie Azure na żądanie nośnika koderów

W tym temacie porównuje kodowania możliwości **Media Encoder Standard** i **Media Encoder Premium w przepływie pracy**.

## <a name="video-and-audio-processing-capabilities"></a>Możliwości przetwarzania audio i wideo

W poniższej tabeli porównano funkcje między Media Encoder Standard (rynkowej) a Media Encoder Premium przepływu pracy (MEPW). 

|Możliwości|Usługa Media Encoder Standard|Przepływ pracy usługi Media Encoder w warstwie Premium|
|---|---|---|
|Zastosuj logikę warunkową podczas kodowania<br/>(na przykład, jeśli dane wejściowe są HD, następnie zakodować 5.1 audio)|Nie|Yes|
|Kodowane|Nie|[Tak](media-services-premium-workflow-encoder-formats.md#closed_captioning)|
|[Dolby® Professional głośności korekty](http://www.dolby.com/us/en/technologies/dolby-professional-loudness-solutions.pdf)<br/> z Intelligence™ dialog|Nie|Yes|
|Telecine usuwania przeplotu, odwrotność funkcji|Podstawowa|Jakość emisji|
|Wykryć i usunąć czarne ramki <br/>(pillarboxes letterboxes)|Nie|Yes|
|Generowanie miniatur|[Tak](media-services-dotnet-generate-thumbnail-with-mes.md)|[Tak](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4)|
|Wycinka/przycinanie i łączenia plików wideo|[Tak](media-services-advanced-encoding-with-mes.md#trim_video)|Yes|
|Nakładki audio i wideo|[Tak](media-services-advanced-encoding-with-mes.md#overlay)|[Tak](media-services-media-encoder-premium-workflow-multiplefilesinput.md#example-1--overlay-an-image-on-top-of-the-video)|
|Nakładki grafiki|Od źródła obrazu|Od źródła obrazu i tekstu|
|Wiele wersji językowych audio|Ograniczone|[Tak](media-services-media-encoder-premium-workflow-multiplefilesinput.md#example-2--multiple-audio-language-encoding)|

## <a id="billing"></a>Licznik rozliczeń używane przez każdego kodera
| Nazwa procesora nośnika | Uzyskać odpowiednie ceny | Uwagi |
| --- | --- | --- |
| **Usługa Media Encoder Standard** |KODER |Kodowanie zadania zostanie naliczona opłata oparte na całkowity czas trwania (w minutach) wszystkie pliki multimedialne utworzone jako dane wyjściowe z szybkością określoną [tutaj][1], w kolumnie KODERA. |
| **Przepływ pracy usługi Media Encoder w warstwie Premium** |KODER PREMIUM |Kodowanie zadania zostanie naliczona opłata oparte na całkowity czas trwania (w minutach) wszystkie pliki multimedialne utworzone jako dane wyjściowe z szybkością określoną [tutaj][1], w kolumnie KODER PREMIUM. |

## <a name="input-containerfile-formats"></a>Kontener/formatów wejściowych
| Kontener/formatów wejściowych | Usługa Media Encoder Standard | Przepływ pracy usługi Media Encoder w warstwie Premium |
| --- | --- | --- |
| Adobe® Flash® F4V |Yes |Yes |
| MXF/SMPTE 377M |Yes |Yes |
| GXF |Yes |Yes |
| Strumienie transportu MPEG-2 |Yes |Yes |
| Strumienie programu MPEG-2 |Yes |Yes |
| MPEG-4/MP4 |Yes |Yes |
| ASF Media systemu Windows |Yes |Yes |
| AVI (nieskompresowanych 8-bitową/10 bity) |Yes |Yes |
| 3GPP/3GPP2 |Yes |Nie |
| Płynnego przesyłania strumieniowego Format pliku (PIFF 1.3) |Yes |Nie |
| [Microsoft cyfrowy wideo Recording(DVR-MS)](https://msdn.microsoft.com/library/windows/desktop/dd692984) |Yes |Nie |
| Matroska/WebM |Yes |Nie |
| QuickTime (mov) |Yes |Nie |

## <a name="input-video-codecs"></a>Dane wejściowe kodery-dekodery wideo
| Dane wejściowe kodery-dekodery wideo | Usługa Media Encoder Standard | Przepływ pracy usługi Media Encoder w warstwie Premium |
| --- | --- | --- |
| AVC 8-bitowych/10-bitowy, maksymalnie 4:2:2, w tym AVCIntra |8 bitów 4:2:0 a 4:2:2 |Yes |
| Avid DNxHD (w MXF) |Yes |Yes |
| DVCPro/DVCProHD (in MXF) |Yes |Yes |
| JPEG2000 |Yes |Yes |
| MPEG-2 (maksymalnie 422 profilu i wysokiego poziomu, m.in wariantów, takich jak XDCAM, XDCAM HD, XDCAM IMX, CableLabs® i D10) |Do 422 profilu |Yes |
| MPEG-1 |Yes |Yes |
| Windows Media wideo/VC-1 |Yes |Yes |
| Canopus HQ/HQX |Nie |Nie |
| MPEG-4 część 2 |Yes |Nie |
| [Theora](https://en.wikipedia.org/wiki/Theora) |Yes |Nie |
| Apple ProRes 422 |Yes |Nie |
| Apple ProRes 422 LT |Yes |Nie |
| HQ ProRes 422 firmy Apple |Yes |Nie |
| Serwer Proxy ProRes firmy Apple |Yes |Nie |
| Apple ProRes 4444 |Yes |Nie |
| XQ 4444 ProRes firmy Apple |Yes |Nie |
| HEVC/H.265|Główny profil|Główne i profilu Main 10|

## <a name="input-audio-codecs"></a>Dane wejściowe audio koderów-dekoderów
| Dane wejściowe Audio koderów-dekoderów | Usługa Media Encoder Standard | Przepływ pracy usługi Media Encoder w warstwie Premium |
| --- | --- | --- |
| AES (SMPTE 331 M i 302 M, AES3 2003) |Nie |Yes |
| Dolby® E |Nie |Yes |
| Dolby® Digital (AC3) |Nie |Yes |
| Cyfrowy Dolby® Plus (E-AC3) |Nie |Yes |
| AAC (AAC-LC, AAC HE i AAC-HEv2; maksymalnie 5.1) |Yes |Yes |
| MPEG warstwy 2 |Yes |Yes |
| Mp3 (MPEG-1 Audio warstwy 3) |Yes |Yes |
| Program Windows Media Audio |Yes |Yes |
| WAV/PCM |Yes |Yes |
| [FLAC](https://en.wikipedia.org/wiki/FLAC)</a> |Yes |Nie |
| [Dziele](https://en.wikipedia.org/wiki/Opus_\(audio_format\)) |Yes |Nie |
| [Vorbis](https://en.wikipedia.org/wiki/Vorbis)</a> |Yes |Nie |

## <a name="output-containerfile-formats"></a>Kontener i plik formatów
| Kontener i plik formatów | Usługa Media Encoder Standard | Przepływ pracy usługi Media Encoder w warstwie Premium |
| --- | --- | --- |
| Adobe® Flash® F4V |Nie |Yes |
| MXF (OP1a, XDCAM i AS02) |Nie |Yes |
| DPP (w tym AS11) |Nie |Yes |
| GXF |Nie |Yes |
| MPEG-4/MP4 |Yes |Yes |
| MPEG-TS |Yes |Yes |
| ASF Media systemu Windows |Nie |Yes |
| AVI (nieskompresowanych 8-bitową/10 bity) |Nie |Yes |
| Płynnego przesyłania strumieniowego Format pliku (PIFF 1.3) |Nie |Yes |

## <a name="output-video-codecs"></a>Dane wyjściowe kodery-dekodery wideo
| Dane wyjściowe kodery-dekodery wideo | Usługa Media Encoder Standard | Przepływ pracy usługi Media Encoder w warstwie Premium |
| --- | --- | --- |
| AVC (H.264; 8-bitową; do profilu wysokiego poziomu 5.2; 4 K bardzo HD; Wewnątrz AVC) |Tylko 8 bitów 4:2:0 |Yes |
| HEVC (H.265; 8-bitową i 10-bitowy)  |Nie |Yes |
| Avid DNxHD (w MXF) |Nie |Yes |
| MPEG-2 (maksymalnie 422 profilu i wysokiego poziomu, m.in wariantów, takich jak XDCAM, XDCAM HD, XDCAM IMX, CableLabs® i D10) |Nie |Yes |
| MPEG-1 |Nie |Yes |
| Windows Media wideo/VC-1 |Nie |Yes |
| Tworzenie miniatur JPEG |Yes |Yes |
| Tworzenie miniatur PNG |Yes |Yes |
| Tworzenie miniatur BMP |Yes |Nie |

## <a name="output-audio-codecs"></a>Dane wyjściowe audio koderów-dekoderów
| Dane wyjściowe Audio koderów-dekoderów | Usługa Media Encoder Standard | Przepływ pracy usługi Media Encoder w warstwie Premium |
| --- | --- | --- |
| AES (SMPTE 331 M i 302 M, AES3 2003) |Nie |Yes |
| Dolby® Digital (AC3) |Nie |Yes |
| Dolby® Digital Plus (E-AC3) do 7.1 |Nie |Yes |
| AAC (AAC-LC, AAC HE i AAC-HEv2; maksymalnie 5.1) |Yes |Yes |
| MPEG warstwy 2 |Nie |Yes |
| Mp3 (MPEG-1 Audio warstwy 3) |Nie |Yes |
| Program Windows Media Audio |Nie |Yes |

>[!NOTE]
>Gdy na cyfrowe® Dolby (AC3), dane wyjściowe można zapisywać tylko do pliku ISO MP4.

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-articles"></a>Pokrewne artykuły:
* [Wykonywania zaawansowanych zadań kodowania dostosowując ustawienia standardu Media Encoder Standard](media-services-custom-mes-presets-with-dotnet.md)
* [Przydziały i ograniczenia](media-services-quotas-and-limitations.md)

<!--Reference links in article-->
[1]: http://azure.microsoft.com/pricing/details/media-services/
