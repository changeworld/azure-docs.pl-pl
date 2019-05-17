---
title: Standardowe formaty i kodeki Encoder - Azure
description: Ten temat zawiera omówienie kodera w warstwie standardowa formaty i kodeki usługi.
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
ms.date: 02/10/2019
ms.author: juliako;anilmur
ms.openlocfilehash: 730ff68e70999307417eea276761d56f4a44046a
ms.sourcegitcommit: 17411cbf03c3fa3602e624e641099196769d718b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2019
ms.locfileid: "65520011"
---
# <a name="standard-encoder-formats-and-codecs"></a>Standardowe formaty i kodeki Encoder

Ten artykuł zawiera listę najbardziej typowych importu i eksportu formatów plików, które można używać z [StandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset). Aby uzyskać informacje na temat tworzenia niestandardowych ustawień wstępnych przy użyciu **StandardEncoderPreset**, zobacz [tworzenie przekształcenia przy użyciu niestandardowego ustawienia wstępnego](customize-encoder-presets-how-to.md).

## <a name="input-containerfile-formats"></a>Dane wejściowe formaty kontenerów/plików

| Formaty plików (rozszerzenia plików) | Obsługiwane |
| --- | --- |
| FLV (z koderów-dekoderów H.264 i AAC) (FLV) |Tak |
| MXF    (.mxf) |Tak |
| GXF    (.gxf) |Tak |
| MPEG2-PS, MPEG2-TS, 3GP (TS, PS, 3GP, 3GPP, mpg) |Tak |
| Windows Media Video (WMV) / ASF (WMV, ASF) |Tak |
| AVI (nieskompresowany 8-bitowy/10-bitowy) (AVI) |Tak |
| MP4 (.mp4, .m4a, .m4v)/ISMV (.isma, .ismv) |Tak |
| [Microsoft Digital Video Recording](https://msdn.microsoft.com/library/windows/desktop/dd692984) (dvr ms) |Tak |
| Matroska/WebM (mkv) |Tak |
| WAVE/WAV (wav) |Tak |
| QuickTime (.mov) |Tak |

> [!NOTE]
> 
> 

### <a name="audio-formats-in-input-containers"></a>Formaty audio w wejściowych kontenerach

Koder w warstwie standardowa obsługuje następujące formaty audio w wejściowych kontenerach wykonywania:

* Pliki MXF, GXF i QuickTime, które mają ścieżki audio z przeplotem stereo lub 5.1 próbek

lub

* Pliki MXF, GXF i QuickTime, w których dane audio są przenoszone jako osobne ścieżki PCM, ale mapowanie kanałów (na stereo lub 5.1) można określić na podstawie metadanych pliku

## <a name="input-video-codecs"></a>Dane wejściowe koderów-dekoderów wideo
| Dane wejściowe koderów-dekoderów wideo | Obsługiwane |
| --- | --- |
| AVC 8-bitowy/10-bitowy, maksymalnie 4:2:2, wraz z AVCIntra |8-bitowy 4:2:0 oraz 4:2:2 |
| Avid DNxHD (in MXF) |Tak |
| DVCPro/DVCProHD (in MXF) |Tak |
| Cyfrowy video (DV) (w plikach AVI) |Tak |
| JPEG 2000 |Tak |
| MPEG-2 (profil 422 i wysoki poziom; wraz z wariantami, takich jak XDCAM, XDCAM HD, XDCAM IMX, CableLabs® oraz D10) |Maksymalnie profil 422 |
| MPEG-1 |Tak |
| VC-1/WMV9 |Tak |
| Canopus Centrali/HQX |Nie |
| MPEG-4 Part 2 |Tak |
| [Theora](https://en.wikipedia.org/wiki/Theora) |Tak |
| Yuv420 bez kompresji lub mezzanine |Tak |
| Apple ProRes 422 |Tak |
| Apple ProRes 422 LT |Tak |
| Apple ProRes 422 Centralą |Tak |
| Apple ProRes Proxy |Tak |
| Apple ProRes 4444 |Tak |
| XQ 4444 ProRes firmy Apple |Tak |
| HEVC/H.265| Główny profil|

## <a name="input-audio-codecs"></a>Dane wejściowe audio kodery-dekodery
| Dane wejściowe Audio kodery-dekodery | Obsługiwane |
| --- | --- |
| AAC (AAC-LC, AAC-HE oraz AAC-HEv2; maksymalnie 5.1) |Tak |
| MPEG Layer 2 |Tak |
| Mp3 (MPEG-1 Audio warstwa 3) |Tak |
| Program Windows Media Audio |Tak |
| WAV/PCM |Tak |
| [FLAC](https://en.wikipedia.org/wiki/FLAC)</a> |Tak |
| [Dziele](https://go.microsoft.com/fwlink/?LinkId=822667) |Tak |
| [Vorbis](https://en.wikipedia.org/wiki/Vorbis)</a> |Tak |
| AMR (adaptacyjne Multi-Rate) |Tak |
| AES (SMPTE 331 M oraz 302 M, AES3-2003) |Nie |
| Dolby® E |Nie |
| Dolby® Digital (AC3) |Nie |
| Dolby® Digital Plus (E-AC3) |Nie |

## <a name="output-formats-and-codecs"></a>Formaty danych wyjściowych i kodery-dekodery
W poniższej tabeli wymieniono formaty koderów-dekoderów i plików, które są obsługiwane w przypadku eksportu.

| Format pliku | Kodera-dekodera wideo | Audio Codec |
| --- | --- | --- |
| MP4 <br/><br/>(w tym kontenerów w formacie MP4 o różnych szybkościach transmisji bitów) |H.264 (wysoki, główne i profile odniesienia) |AAC-LC, HE-AAC v1, HE-AAC v2 |
| MPEG2-TS |H.264 (wysoki, główne i profile odniesienia) |AAC-LC, HE-AAC v1, HE-AAC v2 |

## <a name="next-steps"></a>Kolejne kroki

[Tworzenie przekształcenia przy użyciu niestandardowego ustawienia wstępnego](customize-encoder-presets-how-to.md)
