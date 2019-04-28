---
title: Porównanie platformy Azure na żądanie koderów multimediów | Dokumentacja firmy Microsoft
description: W tym temacie porównano możliwości kodowania **Media Encoder Standard** i **Media Encoder Premium Workflow**.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: a79437c0-4832-423a-bca8-82632b2c47cc
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2019
ms.author: juliako;anilmur
ms.openlocfilehash: bb827b80f79a53f30074b9230efe3e2049471051
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61465715"
---
# <a name="comparison-of-azure-on-demand-media-encoders"></a>Porównanie platformy Azure na żądanie koderów multimediów  

W tym temacie porównano możliwości kodowania **Media Encoder Standard** i **Media Encoder Premium Workflow**.

## <a name="video-and-audio-processing-capabilities"></a>Możliwości przetwarzania audio i wideo

W poniższej tabeli porównano funkcje między Media Encoder Standard (MES) i Media Encoder Premium Workflow (MEPW). 

|Możliwości|Usługa Media Encoder Standard|Przepływ pracy usługi Media Encoder w warstwie Premium|
|---|---|---|
|Zastosuj logikę warunkową podczas kodowania<br/>(na przykład, jeśli dane wejściowe są HD, następnie zakodować 5.1 audio)|Nie|Yes|
|Podpisy kodowane|Nie|[Tak](media-services-premium-workflow-encoder-formats.md#closed_captioning)|
|[Dolby® Professional Loudness Correction](https://www.dolby.com/us/en/technologies/dolby-professional-loudness-solutions.pdf)<br/> za pomocą Intelligence™ dialogu|Nie|Yes|
|Usuwania przeplotu, odwrotność telecine|Podstawowa|Jakości odpowiedniej do emisji|
|Wykrywania i usuwania czarne obramowanie <br/>(pillarboxes letterboxes)|Nie|Yes|
|Generowanie miniatur|[Tak](media-services-dotnet-generate-thumbnail-with-mes.md)|[Tak](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4)|
|Wycinka/przycinania i łączenie — wideo|[Tak](media-services-advanced-encoding-with-mes.md#trim_video)|Yes|
|Nakładki audio lub wideo|[Tak](media-services-advanced-encoding-with-mes.md#overlay)|[Tak](media-services-media-encoder-premium-workflow-multiplefilesinput.md#example-1--overlay-an-image-on-top-of-the-video)|
|Nakładki grafiki|Od źródła obrazu|Ze źródeł tekstowych i obrazów|
|Wielu wersji językowych audio|Ograniczone|[Tak](media-services-media-encoder-premium-workflow-multiplefilesinput.md#example-2--multiple-audio-language-encoding)|

## <a id="billing"></a>Licznik rozliczeń używana przez każdego kodera
| Nazwa procesora multimediów | Odpowiednie ceny | Uwagi |
| --- | --- | --- |
| **Usługa Media Encoder Standard** |KODER |Metody kodowania zadania jest naliczana na podstawie całkowity czas trwania w minutach, z plików multimedialnych generowane jako dane wyjściowe, zgodnie ze stawką określoną [tutaj][1], w kolumnie KODERA. |
| **Przepływ pracy usługi Media Encoder w warstwie Premium** |KODER W WARSTWIE PREMIUM |Metody kodowania zadania jest naliczana na podstawie całkowity czas trwania w minutach, z plików multimedialnych generowane jako dane wyjściowe, zgodnie ze stawką określoną [tutaj][1], w kolumnie KODER w warstwie PREMIUM. |

## <a name="input-containerfile-formats"></a>Dane wejściowe formaty kontenerów/plików
| Dane wejściowe formaty kontenerów/plików | Usługa Media Encoder Standard | Przepływ pracy usługi Media Encoder w warstwie Premium |
| --- | --- | --- |
| Adobe® Flash® F4V |Yes |Yes |
| MXF/SMPTE 377M |Yes |Yes |
| GXF |Yes |Yes |
| Strumienie transportu MPEG-2 |Yes |Yes |
| Strumienie programu MPEG-2 |Yes |Yes |
| MPEG-4/MP4 |Yes |Yes |
| Windows Media/ASF |Yes |Yes |
| AVI (nieskompresowany 8-bitowy/10-bitowy) |Yes |Yes |
| 3GPP/3GPP2 |Yes |Nie |
| Bezproblemowe przesyłania strumieniowego Format pliku (PIFF 1.3) |Yes |Nie |
| [Microsoft Digital Video Recording(DVR-MS)](https://msdn.microsoft.com/library/windows/desktop/dd692984) |Yes |Nie |
| Matroska/WebM |Yes |Nie |
| QuickTime (.mov) |Yes |Nie |

## <a name="input-video-codecs"></a>Dane wejściowe koderów-dekoderów wideo
| Dane wejściowe koderów-dekoderów wideo | Usługa Media Encoder Standard | Przepływ pracy usługi Media Encoder w warstwie Premium |
| --- | --- | --- |
| AVC 8-bitowy/10-bitowy, maksymalnie 4:2:2, wraz z AVCIntra |8-bitowy 4:2:0 oraz 4:2:2 |Yes |
| Avid DNxHD (in MXF) |Yes |Yes |
| DVCPro/DVCProHD (in MXF) |Yes |Yes |
| JPEG2000 |Yes |Yes |
| MPEG-2 (profil 422 i wysoki poziom; wraz z wariantami, takich jak XDCAM, XDCAM HD, XDCAM IMX, CableLabs® oraz D10) |Maksymalnie profil 422 |Yes |
| MPEG-1 |Yes |Yes |
| Windows Media Video/VC-1 |Yes |Yes |
| Canopus Centrali/HQX |Nie |Nie |
| MPEG-4 Part 2 |Yes |Nie |
| [Theora](https://en.wikipedia.org/wiki/Theora) |Yes |Nie |
| Apple ProRes 422 |Yes |Nie |
| Apple ProRes 422 LT |Yes |Nie |
| Apple ProRes 422 Centralą |Yes |Nie |
| Apple ProRes Proxy |Yes |Nie |
| Apple ProRes 4444 |Yes |Nie |
| XQ 4444 ProRes firmy Apple |Yes |Nie |
| HEVC/H.265|Główny profil|Główne i profilu Main 10|

## <a name="input-audio-codecs"></a>Dane wejściowe audio kodery-dekodery
| Dane wejściowe Audio kodery-dekodery | Usługa Media Encoder Standard | Przepływ pracy usługi Media Encoder w warstwie Premium |
| --- | --- | --- |
| AES (SMPTE 331 M oraz 302 M, AES3-2003) |Nie |Yes |
| Dolby® E |Nie |Yes |
| Dolby® Digital (AC3) |Nie |Yes |
| Dolby® Digital Plus (E-AC3) |Nie |Yes |
| AAC (AAC-LC, AAC-HE oraz AAC-HEv2; maksymalnie 5.1) |Yes |Yes |
| MPEG Layer 2 |Yes |Yes |
| Mp3 (MPEG-1 Audio warstwa 3) |Yes |Yes |
| Program Windows Media Audio |Yes |Yes |
| WAV/PCM |Yes |Yes |
| [FLAC](https://en.wikipedia.org/wiki/FLAC)</a> |Yes |Nie |
| [Dziele](https://en.wikipedia.org/wiki/Opus_\(audio_format\)) |Yes |Nie |
| [Vorbis](https://en.wikipedia.org/wiki/Vorbis)</a> |Yes |Nie |

## <a name="output-containerfile-formats"></a>Formaty kontenerów/plików danych wyjściowych
| Formaty kontenerów/plików danych wyjściowych | Usługa Media Encoder Standard | Przepływ pracy usługi Media Encoder w warstwie Premium |
| --- | --- | --- |
| Adobe® Flash® F4V |Nie |Yes |
| MXF (OP1a, XDCAM i AS02) |Nie |Yes |
| DPP (w tym AS11) |Nie |Yes |
| GXF |Nie |Yes |
| MPEG-4/MP4 |Yes |Yes |
| MPEG-TS |Yes |Yes |
| Windows Media/ASF |Nie |Yes |
| AVI (nieskompresowany 8-bitowy/10-bitowy) |Nie |Yes |
| Bezproblemowe przesyłania strumieniowego Format pliku (PIFF 1.3) |Nie |Yes |

## <a name="output-video-codecs"></a>Dane wyjściowe koderów-dekoderów wideo
| Dane wyjściowe koderów-dekoderów wideo | Usługa Media Encoder Standard | Przepływ pracy usługi Media Encoder w warstwie Premium |
| --- | --- | --- |
| AVC (H.264; 8-bitową; do profilu wysokiego poziomu 5.2; 4 K najwyższej jakości HD; Wewnątrz AVC) |Tylko 8-bitowy 4:2:0 |Yes |
| — HEVC (H.265; 8-bitową i 10-bitowy)  |Nie |Yes |
| Avid DNxHD (in MXF) |Nie |Yes |
| MPEG-2 (profil 422 i wysoki poziom; wraz z wariantami, takich jak XDCAM, XDCAM HD, XDCAM IMX, CableLabs® oraz D10) |Nie |Yes |
| MPEG-1 |Nie |Yes |
| Windows Media Video/VC-1 |Nie |Yes |
| Tworzenie miniatur JPEG |Yes |Yes |
| Tworzenie miniatur PNG |Yes |Yes |
| Tworzenie miniatur BMP |Yes |Nie |

## <a name="output-audio-codecs"></a>Kodery-dekodery audio w danych wyjściowych
| Kodery-dekodery Audio w danych wyjściowych | Usługa Media Encoder Standard | Przepływ pracy usługi Media Encoder w warstwie Premium |
| --- | --- | --- |
| AES (SMPTE 331 M oraz 302 M, AES3-2003) |Nie |Yes |
| Dolby® Digital (AC3) |Nie |Yes |
| Dolby® Digital Plus (E-AC3) do 7.1 |Nie |Yes |
| AAC (AAC-LC, AAC-HE oraz AAC-HEv2; maksymalnie 5.1) |Yes |Yes |
| MPEG Layer 2 |Nie |Yes |
| Mp3 (MPEG-1 Audio warstwa 3) |Nie |Yes |
| Program Windows Media Audio |Nie |Yes |

>[!NOTE]
>Jeśli użytkownik kodowanie Dolby® Digital (AC3), dane wyjściowe można zapisać tylko do pliku ISO MP4.

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-articles"></a>Pokrewne artykuły:
* [Wykonywania zaawansowanych zadań kodowania, dostosowywanie ustawień wstępnych usługi Media Encoder Standard](media-services-custom-mes-presets-with-dotnet.md)
* [Przydziały i ograniczenia](media-services-quotas-and-limitations.md)

<!--Reference links in article-->
[1]: https://azure.microsoft.com/pricing/details/media-services/
