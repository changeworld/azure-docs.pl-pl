---
title: Porównanie koderów multimediów platformy Azure na żądanie | Dokumenty firmy Microsoft
description: W tym temacie porównano możliwości kodowania **programu Media Encoder Standard** i Media **Encoder Premium Workflow**.
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
ms.author: juliako
ms.reviewer: anilmur
ms.openlocfilehash: 4767f7bb5ba02c838c0e21721e55a6564a14acd1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "69016656"
---
# <a name="comparison-of-azure-on-demand-media-encoders"></a>Porównanie koderów multimediów platformy Azure na żądanie  

W tym temacie porównano możliwości kodowania **programu Media Encoder Standard** i Media **Encoder Premium Workflow**.

## <a name="video-and-audio-processing-capabilities"></a>Możliwości przetwarzania obrazu i dźwięku

W poniższej tabeli porównano funkcje między standardem mes (Media Encoder Standard) i przepływem pracy mecoder premium (MEPW). 

|Możliwości|Usługa Media Encoder Standard|Przepływ pracy usługi Media Encoder w warstwie Premium|
|---|---|---|
|Stosowanie logiki warunkowej podczas kodowania<br/>(na przykład, jeśli dane wejściowe to HD, należy zakodować dźwięk 5.1)|Nie|Tak|
|Podpisy kodowane|Nie|[Tak](media-services-premium-workflow-encoder-formats.md#closed_captioning)|
|[Dolby® Profesjonalna korekta głośności](https://www.dolby.com/us/en/technologies/dolby-professional-loudness-solutions.pdf)<br/> z Wywiadem Dialogu™|Nie|Tak|
|De-przeplot, odwrotny telecine|Podstawowa (Basic)|Jakość transmisji|
|Wykrywanie i usuwanie czarnych obramowań <br/>(pillarboxy, skrzynki na listy)|Nie|Tak|
|Generowanie miniatur|[Tak](media-services-dotnet-generate-thumbnail-with-mes.md)|[Tak](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4)|
|Przycinanie/przycinanie i szycie filmów|[Tak](media-services-advanced-encoding-with-mes.md#trim_video)|Tak|
|Nakładki audio lub wideo|[Tak](media-services-advanced-encoding-with-mes.md#overlay)|[Tak](media-services-media-encoder-premium-workflow-multiplefilesinput.md#example-1--overlay-an-image-on-top-of-the-video)|
|Nakładki grafiki|Ze źródeł obrazów|Ze źródeł obrazów i tekstu|
|Wiele ścieżek języka audio|Ograniczone|[Tak](media-services-media-encoder-premium-workflow-multiplefilesinput.md#example-2--multiple-audio-language-encoding)|

## <a name="billing-meter-used-by-each-encoder"></a><a id="billing"></a>Miernik rozliczeniowy używany przez każdy koder
| Nazwa procesora multimediów | Obowiązujące ceny | Uwagi |
| --- | --- | --- |
| **Usługa Media Encoder Standard** |Kodera |Kodowanie Zadania będą naliczane na podstawie całkowitego czasu trwania, w minutach, wszystkich plików multimedialnych wyprodukowanych jako dane wyjściowe, z szybkością określoną [w tym miejscu,][1]w kolumnie ENCODER. |
| **Przepływ pracy usługi Media Encoder w warstwie Premium** |KODER PREMIUM |Kodowanie Zadania będą naliczane na podstawie całkowitego czasu trwania, w minutach, wszystkich plików multimedialnych wyprodukowanych jako dane wyjściowe, z szybkością określoną [tutaj,][1]w kolumnie PREMIUM ENCODER. |

## <a name="input-containerfile-formats"></a>Formaty kontenerów/plików wejściowych
| Formaty wejściowych kontenerów/plików | Usługa Media Encoder Standard | Przepływ pracy usługi Media Encoder w warstwie Premium |
| --- | --- | --- |
| Adobe® Flash® F4V |Tak |Tak |
| MXF/SMPTE 377M |Tak |Tak |
| Gxf |Tak |Tak |
| Strumienie transportowe MPEG-2 |Tak |Tak |
| Strumienie programów MPEG-2 |Tak |Tak |
| MPEG-4/MP4 |Tak |Tak |
| Windows Media/ASF |Tak |Tak |
| AVI (Nieskompresowany 8bit/10bit) |Tak |Tak |
| 3GPP/3GPP2 |Tak |Nie |
| Płynny format pliku strumieniowego (PIFF 1.3) |Tak |Nie |
| [Cyfrowe nagrywanie wideo firmy Microsoft (DVR-MS)](https://msdn.microsoft.com/library/windows/desktop/dd692984) |Tak |Nie |
| Matroska/WebM |Tak |Nie |
| QuickTime (mov) |Tak |Nie |

## <a name="input-video-codecs"></a>Wejściowe kodeki wideo
| Kodery-dekodery wejściowych danych wideo | Usługa Media Encoder Standard | Przepływ pracy usługi Media Encoder w warstwie Premium |
| --- | --- | --- |
| AVC 8-bitowy/10-bitowy, do 4:2:2, wraz z AVCIntra |8-bitowy 4:2:0 oraz 4:2:2 |Tak |
| Avid DNxHD (w pliku MXF) |Tak |Tak |
| DVCPro/DVCProHD (w pliku MXF) |Tak |Tak |
| JPEG2000 |Tak |Tak |
| MPEG-2 (do 422 profile i wysokiego poziomu; w tym warianty takie jak XDCAM, XDCAM HD, XDCAM IMX, CableLabs® i D10) |Maksymalnie profil 422 |Tak |
| MPEG-1 |Tak |Tak |
| Windows Media Wideo/VC-1 |Tak |Tak |
| Canopus HQ/HQX |Nie |Nie |
| MPEG-4 część 2 |Tak |Nie |
| [Theora](https://en.wikipedia.org/wiki/Theora) |Tak |Nie |
| Apple ProRes 422 |Tak |Nie |
| Apple ProRes 422 LT |Tak |Nie |
| Apple ProRes 422 HQ |Tak |Nie |
| Apple ProRes Proxy |Tak |Nie |
| Apple ProRes 4444 |Tak |Nie |
| Apple ProRes 4444 XQ |Tak |Nie |
| HEVC/H.265|Profil główny|Profil główny i główny 10|

## <a name="input-audio-codecs"></a>Wejściowe kodeki audio
| Kodery-dekodery wejściowych danych audio | Usługa Media Encoder Standard | Przepływ pracy usługi Media Encoder w warstwie Premium |
| --- | --- | --- |
| AES (SMPTE 331M oraz 302M, AES3-2003) |Nie |Tak |
| Dolby® E |Nie |Tak |
| Dolby® Digital (AC3) |Nie |Tak |
| Dolby® Digital Plus (E-AC3) |Nie |Tak |
| AAC (AAC-LC, AAC-HE oraz AAC-HEv2; maksymalnie 5.1) |Tak |Tak |
| MPEG Layer 2 |Tak |Tak |
| MP3 (MPEG-1 Audio Layer 3) |Tak |Tak |
| Windows Media Audio |Tak |Tak |
| WAV/PCM |Tak |Tak |
| [Flac](https://en.wikipedia.org/wiki/FLAC)</a> |Tak |Nie |
| [Opus](https://en.wikipedia.org/wiki/Opus_\(audio_format\)) |Tak |Nie |
| [Vorbis](https://en.wikipedia.org/wiki/Vorbis)</a> |Tak |Nie |

## <a name="output-containerfile-formats"></a>Formaty kontenerów/plików wyjściowych
| Formaty kontenera/pliku wyjściowego | Usługa Media Encoder Standard | Przepływ pracy usługi Media Encoder w warstwie Premium |
| --- | --- | --- |
| Adobe® Flash® F4V |Nie |Tak |
| MXF (OP1a, XDCAM i AS02) |Nie |Tak |
| DPP (w tym AS11) |Nie |Tak |
| Gxf |Nie |Tak |
| MPEG-4/MP4 |Tak |Tak |
| MPEG-TS |Tak |Tak |
| Windows Media/ASF |Nie |Tak |
| AVI (Nieskompresowany 8bit/10bit) |Nie |Tak |
| Płynny format pliku strumieniowego (PIFF 1.3) |Nie |Tak |

## <a name="output-video-codecs"></a>Kodeki wideo wyjściowe
| Kodeki wideo wyjściowe | Usługa Media Encoder Standard | Przepływ pracy usługi Media Encoder w warstwie Premium |
| --- | --- | --- |
| AVC (H.264; 8-bit; do High Profile, Level 5.2; 4K Ultra HD; AVC Intra) |Tylko 8 bitów 4:2:0 |Tak |
| HEVC (H.265; 8-bitowy i 10-bitowy;)  |Nie |Tak |
| Avid DNxHD (w pliku MXF) |Nie |Tak |
| MPEG-2 (do 422 profile i wysokiego poziomu; w tym warianty takie jak XDCAM, XDCAM HD, XDCAM IMX, CableLabs® i D10) |Nie |Tak |
| MPEG-1 |Nie |Tak |
| Windows Media Wideo/VC-1 |Nie |Tak |
| Tworzenie miniatur w formacie JPEG |Tak |Tak |
| Tworzenie miniatur PNG |Tak |Tak |
| Tworzenie miniatur BMP |Tak |Nie |

## <a name="output-audio-codecs"></a>Kodeki audio wyjściowe
| Kodeki audio wyjściowych | Usługa Media Encoder Standard | Przepływ pracy usługi Media Encoder w warstwie Premium |
| --- | --- | --- |
| AES (SMPTE 331M oraz 302M, AES3-2003) |Nie |Tak |
| Dolby® Digital (AC3) |Nie |Tak |
| Dolby® Digital Plus (E-AC3) do 7.1 |Nie |Tak |
| AAC (AAC-LC, AAC-HE oraz AAC-HEv2; maksymalnie 5.1) |Tak |Tak |
| MPEG Layer 2 |Nie |Tak |
| MP3 (MPEG-1 Audio Layer 3) |Nie |Tak |
| Windows Media Audio |Nie |Tak |

>[!NOTE]
>Jeśli kodujesz do dolby® Digital (AC3), dane wyjściowe mogą być zapisywane tylko w pliku ISO MP4.

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-articles"></a>Pokrewne artykuły:
* [Wykonywanie zaawansowanych zadań kodowania przez dostosowywanie ustawień predefiniowanych programu Media Encoder Standard](media-services-custom-mes-presets-with-dotnet.md)
* [Przydziały i ograniczenia](media-services-quotas-and-limitations.md)

<!--Reference links in article-->
[1]: https://azure.microsoft.com/pricing/details/media-services/
