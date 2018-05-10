---
title: Koderów-dekoderów i formaty Media Encoder Standard
description: Ten temat zawiera omówienie koderów-dekoderów i formaty Media Encoder Standard.
services: media-services
documentationcenter: ''
author: juliako
manager: cfowler
editor: ''
ms.assetid: f334b1ce-2f56-4968-a019-f0a2b0016d9f
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: juliako;anilmur
ms.openlocfilehash: 5e71714f94cf148895585e8de88eaf995f0791fb
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="media-encoder-standard-formats-and-codecs"></a>Formaty i kodeki narzędzia Media Encoder Standard
Ten dokument zawiera listę typowych importu i eksportu formatów plików, które można używać z Media Encoder Standard.

## <a name="input-containerfile-formats"></a>Kontener/formatów wejściowych
| Formaty plików (rozszerzenia plików) | Obsługiwane |
| --- | --- | --- | --- |
| FLV (za pomocą koderów-dekoderów H.264 i AAC) (.flv) |Yes |
| MXF (.mxf) |Yes |
| GXF (.gxf) |Yes |
| PS MPEG2, MPEG2 TS 3GP (TS, PS, .3gp, .3gpp, mpg) |Yes |
| Program Windows Media Video (WMV) / ASF (WMV, .asf) |Yes |
| AVI (nieskompresowanych 8-bitową/10 bitów) (AVI) |Yes |
| MP4 (plik MP4, .m4a, M4V) / ISMV (.isma, .ismv) |Yes |
| [Microsoft cyfrowy wideo Recording(DVR-MS)](https://msdn.microsoft.com/library/windows/desktop/dd692984) (dvr ms) |Yes |
| Matroska/WebM (.mkv) |Yes |
| WAVE/WAV (wav) |Yes |
| QuickTime (mov) |Yes |

> [!NOTE]
> Powyżej znajduje się lista rozszerzeń plików najczęściej spotykanych. Media Encoder Standard obsługi wielu innych (na przykład: .m2ts, .mpeg2video, .qt). Jeśli spróbujesz kodowania pliku i otrzymasz komunikat o błędzie dotyczący format nie jest obsługiwane, podaj swoją opinię [tutaj](https://feedback.azure.com/forums/169396-media-services/category/144411-encoding-and-processing/).
> 
> 

### <a name="audio-formats-in-input-containers"></a>Formaty audio w kontenerach wejściowych
Media Encoder Standard obsługuje wykonywanie następujących formatów audio w kontenerach wejściowych:

* MXF, GXF i QuickTime pliki, które mają ścieżki audio z przeplotem stereo lub 5.1 próbki

lub

* Pliki MXF, GXF i QuickTime dźwięk jest przenoszona jako oddzielne ścieżki PCM, ale dla których kanału mapowanie programu (stereo lub 5.1) można określić na podstawie metadanych pliku

Pomoc techniczna dla mapowania kanału jawne/dostarczone przez użytkownika będzie świadczona w najbliższej przyszłości.

## <a name="input-video-codecs"></a>Dane wejściowe kodery-dekodery wideo
| Dane wejściowe kodery-dekodery wideo | Obsługiwane |
| --- | --- | --- | --- |
| AVC 8-bitowych/10-bitowy, maksymalnie 4:2:2, w tym AVCIntra |8 bitów 4:2:0 a 4:2:2 |
| Avid DNxHD (w MXF) |Yes |
| DVCPro/DVCProHD (in MXF) |Yes |
| Cyfrowy wideo (w plikach AVI) |Yes |
| JPEG 2000 |Yes |
| MPEG-2 (maksymalnie 422 profilu i wysokiego poziomu, m.in wariantów, takich jak XDCAM, XDCAM HD XDCAM IMX, CableLabs® i D10) |Do 422 profilu |
| MPEG-1 |Yes |
| VC-1/WMV9 |Yes |
| Canopus HQ/HQX |Nie |
| MPEG-4 część 2 |Yes |
| [Theora](https://en.wikipedia.org/wiki/Theora) |Yes |
| YUV420 nieskompresowane lub mezzanine |Yes |
| Apple ProRes 422 |Yes |
| Apple ProRes 422 LT |Yes |
| HQ ProRes 422 firmy Apple |Yes |
| Serwer Proxy ProRes firmy Apple |Yes |
| Apple ProRes 4444 |Yes |
| XQ 4444 ProRes firmy Apple |Yes |

## <a name="input-audio-codecs"></a>Dane wejściowe Audio koderów-dekoderów
| Dane wejściowe Audio koderów-dekoderów | Obsługiwane |
| --- | --- | --- | --- |
| AAC (AAC-LC, AAC HE i AAC-HEv2; maksymalnie 5.1) |Yes |
| MPEG warstwy 2 |Yes |
| Mp3 (MPEG-1 Audio warstwy 3) |Yes |
| Program Windows Media Audio |Yes |
| WAV/PCM |Yes |
| [FLAC](https://en.wikipedia.org/wiki/FLAC)</a> |Yes |
| [Dziele](http://go.microsoft.com/fwlink/?LinkId=822667) |Yes |
| [Vorbis](https://en.wikipedia.org/wiki/Vorbis)</a> |Yes |
| AMR (adaptacyjnej szybkości wielu) |Yes |
| AES (SMPTE 331 M i 302 M, AES3 2003) |Nie |
| Dolby® E |Nie |
| Dolby® Digital (AC3) |Nie |
| Cyfrowy Dolby® Plus (E-AC3) |Nie |

## <a name="output-formats-and-codecs"></a>Koderów-dekoderów i formatów wyjściowych
Poniższa tabela zawiera listę formatów koderów-dekoderów i plików, które są obsługiwane w przypadku eksportowania.

| Format pliku | Kodera-dekodera wideo | Kodera-dekodera audio |
| --- | --- | --- |
| MP4 <br/><br/>(w tym kontenery MP4 wielokrotnej szybkości transmisji bitów) |H.264 (wysoki, Main i profile linii bazowej) |AAC-LC, HE-AAC v1, v2 HE-AAC |
| MPEG2 TS |H.264 (wysoki, Main i profile linii bazowej) |AAC-LC, HE-AAC v1, v2 HE-AAC |

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Zobacz także
[Kodowanie zawartości na żądanie za pomocą usługi Azure Media Services](media-services-encode-asset.md)

[Sposób kodowania przy użyciu Media Encoder Standard](media-services-dotnet-encode-with-media-encoder-standard.md)

