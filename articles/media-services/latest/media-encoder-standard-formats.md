---
title: Standardowe formaty koderów i kodeki — Azure
description: Ten artykuł zawiera listę najpopularniejszych formatów plików importu i eksportu, których można używać w standardencoderPreset.
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
ms.author: juliako
ms.reviewer: anilmur
ms.openlocfilehash: f1d4d4f4006702ebe0d057e56cf24a022e73b83e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79251364"
---
# <a name="standard-encoder-formats-and-codecs"></a>Standardowe formaty koderów i kodeki

Ten artykuł zawiera listę najpopularniejszych formatów plików importu i eksportu, których można używać w [standardencoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset). Aby uzyskać informacje na temat tworzenia niestandardowych ustawień predefiniowanych przy użyciu **standardencoderPreset**, zobacz [Tworzenie transformacji z niestandardowym ustawieniem predefiniowane](customize-encoder-presets-how-to.md).

## <a name="input-containerfile-formats"></a>Formaty kontenerów/plików wejściowych

| Formaty plików (rozszerzenia nazw plików) | Obsługiwane |
| --- | --- |
| FLV (z koderami-dekoderami H.264 i AAC) (flv) |Tak |
| MXF (mxf) |Tak |
| GXF (gxf) |Tak |
| MPEG2-PS, MPEG2-TS, 3GP (ts, ps, 3gp, 3gpp, mpg) |Tak |
| Windows Media Video (WMV)/ASF (wmv, asf) |Tak |
| AVI (nieskompresowany 8-bitowy/10-bitowy) (avi) |Tak |
| MP4 (mp4, m4a, m4v)/ISMV (isma, ismv) |Tak |
| [Microsoft Digital Video Recording (DVR-MS)](https://msdn.microsoft.com/library/windows/desktop/dd692984) (dvr-ms) |Tak |
| Matroska/WebM (mkv) |Tak |
| WAVE/WAV (wav) |Tak |
| QuickTime (mov) |Tak |

### <a name="audio-formats-in-input-containers"></a>Formaty audio w kontenerach wejściowych

Standard Encoder obsługuje przenoszenie następujących formatów audio w kontenerach wejściowych:

* Pliki MXF, GXF i QuickTime, które mają ścieżki audio z przeplatane próbki stereo lub 5.1 próbek

lub

* Pliki MXF, GXF i QuickTime, w których dane audio są przenoszone jako osobne ścieżki PCM, ale mapowanie kanałów (na stereo lub 5.1) może zostać ustalone na podstawie metadanych pliku

## <a name="input-video-codecs"></a>Wejściowe kodeki wideo
| Wejściowe kodeki wideo | Obsługiwane |
| --- | --- |
| AVC 8-bitowy/10-bitowy, do 4:2:2, wraz z AVCIntra |8-bitowy 4:2:0 oraz 4:2:2 |
| Avid DNxHD (w pliku MXF) |Tak |
| DVCPro/DVCProHD (w pliku MXF) |Tak |
| Digital video (DV) (w plikach AVI) |Tak |
| JPEG 2000 |Tak |
| MPEG-2 (maksymalny profil 422 i wysoki poziom; wraz z wariantami, takimi jak XDCAM, XDCAM HD, XDCAM IMX, CableLabs® oraz D10) |Maksymalnie profil 422 |
| MPEG-1 |Tak |
| VC-1/WMV9 |Tak |
| Canopus HQ/HQX |Nie |
| MPEG-4 część 2 |Tak |
| [Theora](https://en.wikipedia.org/wiki/Theora) |Tak |
| YUV420 bez kompresji lub mezzanine |Tak |
| Apple ProRes 422 |Tak |
| Apple ProRes 422 LT |Tak |
| Apple ProRes 422 HQ |Tak |
| Apple ProRes Proxy |Tak |
| Apple ProRes 4444 |Tak |
| Apple ProRes 4444 XQ |Tak |
| HEVC/H.265| Profil główny|

## <a name="input-audio-codecs"></a>Wejściowe kodeki audio
| Kodery-dekodery wejściowych danych audio | Obsługiwane |
| --- | --- |
| AAC (AAC-LC, AAC-HE oraz AAC-HEv2; maksymalnie 5.1) |Tak |
| MPEG Layer 2 |Tak |
| MP3 (MPEG-1 Audio Layer 3) |Tak |
| Windows Media Audio |Tak |
| WAV/PCM |Tak |
| [Flac](https://en.wikipedia.org/wiki/FLAC)</a> |Tak |
| [Opus](https://go.microsoft.com/fwlink/?LinkId=822667) |Tak |
| [Vorbis](https://en.wikipedia.org/wiki/Vorbis)</a> |Tak |
| AMR (Adaptive Multi-Rate) |Tak |
| AES (SMPTE 331M oraz 302M, AES3-2003) |Nie |
| Dolby® E |Nie |
| Dolby® Digital (AC3) |Nie |
| Dolby® Digital Plus (E-AC3) |Nie |

## <a name="output-formats-and-codecs"></a>Formaty wyjściowe i kodeki
W poniższej tabeli wymieniono kodeki i formaty plików, które są obsługiwane do eksportu.

| Format pliku | Kodek wideo | Kodek audio |
| --- | --- | --- |
| MP4 <br/><br/>(w tym wielodyskowych kontenerów MP4) |H.264 (profile wysokie, główne i wyjściowe) |AAC-LC, HE-AAC v1, HE-AAC v2 |
| MPEG2-TS |H.264 (profile wysokie, główne i wyjściowe) |AAC-LC, HE-AAC v1, HE-AAC v2 |

## <a name="next-steps"></a>Następne kroki

[Tworzenie transformacji przy niestandardowych ustawieniach predefiniowanych](customize-encoder-presets-how-to.md)
