---
title: Porównanie koderów multimediów na żądanie na platformie Azure | Microsoft Docs
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
ms.author: juliako
ms.reviewer: anilmur
ms.openlocfilehash: 4767f7bb5ba02c838c0e21721e55a6564a14acd1
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/15/2019
ms.locfileid: "69016656"
---
# <a name="comparison-of-azure-on-demand-media-encoders"></a>Porównanie koderów multimediów na żądanie na platformie Azure  

W tym temacie porównano możliwości kodowania **Media Encoder Standard** i **Media Encoder Premium Workflow**.

## <a name="video-and-audio-processing-capabilities"></a>Możliwości przetwarzania wideo i audio

Poniższa tabela zawiera porównanie funkcji Media Encoder Standard (MES) i Media Encoder Premium Workflow (MEPW). 

|Możliwość|Usługa Media Encoder Standard|Przepływ pracy usługi Media Encoder w warstwie Premium|
|---|---|---|
|Stosowanie logiki warunkowej podczas kodowania<br/>(na przykład jeśli dane wejściowe to HD, a następnie kodowanie 5,1 audio)|Nie|Tak|
|Napisy|Nie|[Tak](media-services-premium-workflow-encoder-formats.md#closed_captioning)|
|[Korekcja głośności Dolby® Professional](https://www.dolby.com/us/en/technologies/dolby-professional-loudness-solutions.pdf)<br/> za pomocą™ analizy dialogu|Nie|Tak|
|Usuwanie przeplotu, inverse telecine|Podstawowa|Jakość emisji|
|Wykrywaj i usuwaj czarne obramowania <br/>(pillarboxes, letterboxes)|Nie|Tak|
|Generowanie miniatur|[Tak](media-services-dotnet-generate-thumbnail-with-mes.md)|[Tak](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4)|
|Przycinanie/przycinanie i łączenie filmów wideo|[Tak](media-services-advanced-encoding-with-mes.md#trim_video)|Tak|
|Nakładki audio lub wideo|[Tak](media-services-advanced-encoding-with-mes.md#overlay)|[Tak](media-services-media-encoder-premium-workflow-multiplefilesinput.md#example-1--overlay-an-image-on-top-of-the-video)|
|Nakładki grafiki|Ze źródeł obrazu|Ze źródeł obrazów i tekstu|
|Wiele ścieżek języka audio|Ograniczona|[Tak](media-services-media-encoder-premium-workflow-multiplefilesinput.md#example-2--multiple-audio-language-encoding)|

## <a id="billing"></a>Licznik rozliczeń używany przez każdy koder
| Nazwa procesora multimediów | Odpowiednie ceny | Uwagi |
| --- | --- | --- |
| **Usługa Media Encoder Standard** |POMOCĄ |Opłaty za zadania kodowania będą naliczone na podstawie łącznego czasu trwania (w minutach) wszystkich plików multimedialnych utworzonych jako dane wyjściowe według stawki określonej w [tym miejscu][1]w kolumnie koder. |
| **Przepływ pracy usługi Media Encoder w warstwie Premium** |KODER W WARSTWIE PREMIUM |Opłaty za zadania kodowania będą naliczone na podstawie łącznego czasu trwania (w minutach) wszystkich plików multimedialnych produkowanych jako dane wyjściowe według stawki określonej w [tym miejscu][1]w kolumnie kodera Premium. |

## <a name="input-containerfile-formats"></a>Dane wejściowe w formacie kontenera/pliku
| Dane wejściowe w formacie kontenera/pliku | Usługa Media Encoder Standard | Przepływ pracy usługi Media Encoder w warstwie Premium |
| --- | --- | --- |
| Adobe® Flash® F4V |Tak |Tak |
| MXF/SMPTE 377M |Tak |Tak |
| GXF |Tak |Tak |
| Strumienie transportowe MPEG-2 |Tak |Tak |
| Strumienie programu MPEG-2 |Tak |Tak |
| MPEG-4/MP4 |Tak |Tak |
| Windows Media/ASF |Tak |Tak |
| AVI (nieskompresowany 8bit/10bit) |Tak |Tak |
| 3GPP/3GPP2 |Tak |Nie |
| Smooth Streaming format pliku (PIFF 1,3) |Tak |Nie |
| [Cyfrowe nagrywanie wideo firmy Microsoft (DVR-MS)](https://msdn.microsoft.com/library/windows/desktop/dd692984) |Tak |Nie |
| Matroska/WebM |Tak |Nie |
| QuickTime (. mov) |Tak |Nie |

## <a name="input-video-codecs"></a>Kodery-dekoder wideo
| Kodery-dekoder wideo | Usługa Media Encoder Standard | Przepływ pracy usługi Media Encoder w warstwie Premium |
| --- | --- | --- |
| AVC 8-bitowy/10-bitowy, do 4:2:2, włącznie z AVCIntra |8 bitów 4:2:0 i 4:2:2 |Tak |
| Avid DNxHD (w MXF) |Tak |Tak |
| DVCPro/DVCProHD (in MXF) |Tak |Tak |
| JPEG2000 |Tak |Tak |
| MPEG-2 (do 422 profilu i wysokiego poziomu), w tym różne warianty, takie jak XDCAM, XDCAM HD, XDCAM IMX, CableLabs® i D10) |Do 422 profilu |Tak |
| MPEG-1 |Tak |Tak |
| Windows Media Video/VC-1 |Tak |Tak |
| Canopus CENTRALĄ/HQX |Nie |Nie |
| MPEG-4 — część 2 |Tak |Nie |
| [Theora](https://en.wikipedia.org/wiki/Theora) |Tak |Nie |
| Apple ProRes 422 |Tak |Nie |
| Apple ProRes 422 LT |Tak |Nie |
| Apple ProRes 422 CENTRALĄ |Tak |Nie |
| Serwer proxy Apple ProRes |Tak |Nie |
| Apple ProRes 4444 |Tak |Nie |
| Apple ProRes 4444 XQ |Tak |Nie |
| HEVC/H.265|Profil główny|Główny i główny 10 profilu|

## <a name="input-audio-codecs"></a>Kodery-dekoder Audio Input
| Kodery-dekoder Audio Input | Usługa Media Encoder Standard | Przepływ pracy usługi Media Encoder w warstwie Premium |
| --- | --- | --- |
| AES (SMPTE 331M i 302M, AES3-2003) |Nie |Tak |
| Dolby® E |Nie |Tak |
| Dolby® Digital (AC3) |Nie |Tak |
| Dolby® Digital Plus (E-AC3) |Nie |Tak |
| AAC (AAC-LC, AAC-IT i AAC-HEv2; do 5,1) |Tak |Tak |
| MPEG Layer 2 |Tak |Tak |
| MP3 (warstwa audio MPEG-1) |Tak |Tak |
| Dźwięk Windows Media |Tak |Tak |
| WAV/PCM |Tak |Tak |
| [FLAC](https://en.wikipedia.org/wiki/FLAC)</a> |Tak |Nie |
| [Opus](https://en.wikipedia.org/wiki/Opus_\(audio_format\)) |Tak |Nie |
| [Vorbis](https://en.wikipedia.org/wiki/Vorbis)</a> |Tak |Nie |

## <a name="output-containerfile-formats"></a>Pliki/kontenery wyjściowe
| Pliki/kontenery wyjściowe | Usługa Media Encoder Standard | Przepływ pracy usługi Media Encoder w warstwie Premium |
| --- | --- | --- |
| Adobe® Flash® F4V |Nie |Tak |
| MXF (OP1a, XDCAM i AS02) |Nie |Tak |
| DPP (łącznie z AS11) |Nie |Tak |
| GXF |Nie |Tak |
| MPEG-4/MP4 |Tak |Tak |
| MPEG-TS |Tak |Tak |
| Windows Media/ASF |Nie |Tak |
| AVI (nieskompresowany 8bit/10bit) |Nie |Tak |
| Smooth Streaming format pliku (PIFF 1,3) |Nie |Tak |

## <a name="output-video-codecs"></a>Wyjściowe kodery-dekoder wideo
| Wyjściowe kodery-dekoder wideo | Usługa Media Encoder Standard | Przepływ pracy usługi Media Encoder w warstwie Premium |
| --- | --- | --- |
| AVC (H. 264; 8-bitowy; maksymalnie wysoki profil, poziom 5,2; 4K Ultra HD; AVC — Intra) |Tylko 8 bitów 4:2:0 |Tak |
| HEVC (H. 265; 8-bitowe i 10-bitowe;)  |Nie |Tak |
| Avid DNxHD (w MXF) |Nie |Tak |
| MPEG-2 (do 422 profilu i wysokiego poziomu), w tym różne warianty, takie jak XDCAM, XDCAM HD, XDCAM IMX, CableLabs® i D10) |Nie |Tak |
| MPEG-1 |Nie |Tak |
| Windows Media Video/VC-1 |Nie |Tak |
| Tworzenie miniatur JPEG |Tak |Tak |
| Tworzenie miniatur PNG |Tak |Tak |
| Tworzenie miniatur BMP |Tak |Nie |

## <a name="output-audio-codecs"></a>Wyjściowe kodery-dekoder audio
| Wyjściowe kodery-dekoder audio | Usługa Media Encoder Standard | Przepływ pracy usługi Media Encoder w warstwie Premium |
| --- | --- | --- |
| AES (SMPTE 331M i 302M, AES3-2003) |Nie |Tak |
| Dolby® Digital (AC3) |Nie |Tak |
| Dolby® Digital Plus (E-AC3) do 7,1 |Nie |Tak |
| AAC (AAC-LC, AAC-IT i AAC-HEv2; do 5,1) |Tak |Tak |
| MPEG Layer 2 |Nie |Tak |
| MP3 (warstwa audio MPEG-1) |Nie |Tak |
| Dźwięk Windows Media |Nie |Tak |

>[!NOTE]
>W przypadku kodowania do Dolby® Digital (AC3) dane wyjściowe mogą być zapisywane tylko w pliku MP4 ISO.

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-articles"></a>Pokrewne artykuły:
* [Wykonywanie zaawansowanych zadań kodowania przez dostosowanie Media Encoder Standard ustawień wstępnych](media-services-custom-mes-presets-with-dotnet.md)
* [Limity przydziału i ograniczenia](media-services-quotas-and-limitations.md)

<!--Reference links in article-->
[1]: https://azure.microsoft.com/pricing/details/media-services/
