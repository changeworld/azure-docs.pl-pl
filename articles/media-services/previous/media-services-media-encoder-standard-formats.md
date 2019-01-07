---
title: Usługi Media Encoder Standard formaty i kodery-dekodery - Azure
description: Ten temat zawiera omówienie usługi Media Encoder Standard formaty i kodeki usługi.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/12/2018
ms.author: juliako;anilmur
ms.openlocfilehash: fe1d681fa5f9ab49fec9112398ed03c87c975176
ms.sourcegitcommit: 85d94b423518ee7ec7f071f4f256f84c64039a9d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/14/2018
ms.locfileid: "53384738"
---
# <a name="media-encoder-standard-formats-and-codecs"></a>Formaty i kodeki narzędzia Media Encoder Standard

> [!div class="op_single_selector" title1="Wybierz wersję Media Services, z której korzystasz:"]
> * [Wersja 2](media-services-media-encoder-standard-formats.md)
> * [Wersja 3](../latest/media-encoder-standard-formats.md)

Ten dokument zawiera listę najbardziej typowych importu i eksportu formatów plików, które za pomocą usługi Media Encoder Standard.

## <a name="input-containerfile-formats"></a>Dane wejściowe formaty kontenerów/plików
| Formaty plików (rozszerzenia plików) | Obsługiwane |
| --- | --- | --- | --- |
| FLV (z koderów-dekoderów H.264 i AAC) (FLV) |Yes |
| MXF (mxf) |Yes |
| GXF (gxf) |Yes |
| MPEG2-PS, MPEG2-TS, 3GP (TS, PS, 3GP, 3GPP, mpg) |Yes |
| Windows Media Video (WMV) / ASF (WMV, ASF) |Yes |
| AVI (nieskompresowany 8-bitowy/10-bitowy) (AVI) |Yes |
| MP4 (MP4, M4A, M4V) / ISMV (ISMA, ismv) |Yes |
| [Microsoft Digital Video Recording](https://msdn.microsoft.com/library/windows/desktop/dd692984) (dvr ms) |Yes |
| Matroska/WebM (mkv) |Yes |
| WAVE/WAV (wav) |Yes |
| QuickTime (mov) |Yes |

> [!NOTE]
> Powyżej znajduje się lista najczęściej spotykanych rozszerzeń nazw plików. Usługi Media Encoder Standard obsługuje wiele innych (na przykład: m2ts, mpeg2video, Qt). Jeśli spróbujesz kodowanie pliku i otrzymujesz komunikat o błędzie informacji na temat formatu nie jest obsługiwane, przekazania swojej opinii [tutaj](https://feedback.azure.com/forums/169396-media-services/category/144411-encoding-and-processing/).
> 
> 

### <a name="audio-formats-in-input-containers"></a>Formaty audio w wejściowych kontenerach
Usługi Media Encoder Standard obsługuje następujące formaty audio w wejściowych kontenerach wykonywania:

* Pliki MXF, GXF i QuickTime, które mają ścieżki audio z przeplotem stereo lub 5.1 próbek

lub

* Pliki MXF, GXF i QuickTime, w których dane audio są przenoszone jako osobne ścieżki PCM, ale mapowanie kanałów (na stereo lub 5.1) można określić na podstawie metadanych pliku

## <a name="input-video-codecs"></a>Dane wejściowe koderów-dekoderów wideo
| Dane wejściowe koderów-dekoderów wideo | Obsługiwane |
| --- | --- | --- | --- |
| AVC 8-bitowy/10-bitowy, maksymalnie 4:2:2, wraz z AVCIntra |8-bitowy 4:2:0 oraz 4:2:2 |
| Avid DNxHD (w pliku MXF) |Yes |
| DVCPro/DVCProHD (in MXF) |Yes |
| Cyfrowy video (DV) (w plikach AVI) |Yes |
| JPEG 2000 |Yes |
| MPEG-2 (profil 422 i wysoki poziom; wraz z wariantami, takich jak XDCAM, XDCAM HD, XDCAM IMX, CableLabs® oraz D10) |Maksymalnie profil 422 |
| MPEG-1 |Yes |
| VC-1/WMV9 |Yes |
| Canopus Centrali/HQX |Nie |
| MPEG-4 część 2 |Yes |
| [Theora](https://en.wikipedia.org/wiki/Theora) |Yes |
| Yuv420 bez kompresji lub mezzanine |Yes |
| Apple ProRes 422 |Yes |
| Apple ProRes 422 LT |Yes |
| Apple ProRes 422 Centralą |Yes |
| Serwer Proxy ProRes firmy Apple |Yes |
| Apple ProRes 4444 |Yes |
| XQ 4444 ProRes firmy Apple |Yes |
| HEVC/H.265| Główny profil|

## <a name="input-audio-codecs"></a>Dane wejściowe audio kodery-dekodery
| Dane wejściowe Audio kodery-dekodery | Obsługiwane |
| --- | --- | --- | --- |
| AAC (AAC-LC, AAC-HE oraz AAC-HEv2; maksymalnie 5.1) |Yes |
| MPEG warstwy 2 |Yes |
| Mp3 (MPEG-1 Audio warstwa 3) |Yes |
| Program Windows Media Audio |Yes |
| WAV/PCM |Yes |
| [FLAC](https://en.wikipedia.org/wiki/FLAC)</a> |Yes |
| [Dziele](https://go.microsoft.com/fwlink/?LinkId=822667) |Yes |
| [Vorbis](https://en.wikipedia.org/wiki/Vorbis)</a> |Yes |
| AMR (adaptacyjne Multi-Rate) |Yes |
| AES (SMPTE 331 M oraz 302 M, AES3-2003) |Nie |
| Dolby® E |Nie |
| Dolby® Digital (AC3) |Nie |
| Cyfrowy Dolby® Plus (E-AC3) |Nie |

## <a name="output-formats-and-codecs"></a>Formaty danych wyjściowych i kodery-dekodery
W poniższej tabeli wymieniono formaty koderów-dekoderów i plików, które są obsługiwane w przypadku eksportu.

| Format pliku | Kodera-dekodera wideo | Kodera-dekodera audio |
| --- | --- | --- |
| MP4 <br/><br/>(w tym kontenerów w formacie MP4 o różnych szybkościach transmisji bitów) |H.264 (wysoki, główne i profile odniesienia) |AAC-LC, HE-AAC v1, v2 HE AAC |
| MPEG2-TS |H.264 (wysoki, główne i profile odniesienia) |AAC-LC, HE-AAC v1, v2 HE AAC |

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Zobacz także
[Kodowanie zawartości na żądanie przy użyciu usługi Azure Media Services](media-services-encode-asset.md)

[Jak kodowanie za pomocą usługi Media Encoder Standard](media-services-dotnet-encode-with-media-encoder-standard.md)

