---
title: Formaty koderów standardowych i kodery-dekoder na platformie Azure
description: Ten temat zawiera omówienie standardowych formatów koderów i koderów-dekoder.
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
ms.openlocfilehash: 195bbb70ac5062c6bbf6034e6a6e9abac018c62f
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/31/2019
ms.locfileid: "68677971"
---
# <a name="standard-encoder-formats-and-codecs"></a>Standardowe formaty i kodeki kodera

Ten artykuł zawiera listę najpopularniejszych formatów importu i eksportu plików, których można używać z programem [StandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset). Aby uzyskać informacje na temat tworzenia niestandardowych ustawień wstępnych przy użyciu programu **StandardEncoderPreset**, zobacz [Tworzenie przekształcenia z](customize-encoder-presets-how-to.md)niestandardowym ustawieniem wstępnym.

## <a name="input-containerfile-formats"></a>Dane wejściowe w formacie kontenera/pliku

| Formaty plików (rozszerzenia plików) | Obsługiwane |
| --- | --- |
| FLV (z kodekami H. 264 i AAC) (. FLV) |Tak |
| MXF    (.mxf) |Tak |
| GXF    (.gxf) |Tak |
| MPEG2-PS, MPEG2-TS, 3GP (. TS,. PS,. 3gp,. 3GPP,. mpg) |Yes |
| Windows Media Video (WMV)/ASF (. wmv,. ASF) |Tak |
| AVI (nieskompresowany 8bit/10bit) (AVI) |Tak |
| MP4 (. mp4,. M4A,. m4v)/ISMV (. ISMA,. ISMV) |Tak |
| [Cyfrowe nagrywanie wideo firmy Microsoft (DVR-MS)](https://msdn.microsoft.com/library/windows/desktop/dd692984) (. DVR-MS) |Tak |
| Matroska/WebM (. mkv) |Yes |
| WAVE/WAV (wav) |Tak |
| QuickTime (. mov) |Yes |

### <a name="audio-formats-in-input-containers"></a>Formaty audio w kontenerach wejściowych

Koder w warstwie Standardowa obsługuje następujące formaty audio w kontenerach wejściowych:

* Pliki MXF, GXF i QuickTime, które mają ścieżki audio z przeplotem stereo lub 5,1 próbek

lub

* Pliki MXF, GXF i QuickTime, w których dźwięk jest przenoszony jako oddzielne ścieżki PCM, ale można ustalić mapowanie kanału (do stereo lub 5,1) z metadanych plików

## <a name="input-video-codecs"></a>Kodery-dekoder wideo
| Kodery-dekoder wideo | Obsługiwane |
| --- | --- |
| AVC 8-bitowy/10-bitowy, do 4:2:2, włącznie z AVCIntra |8 bitów 4:2:0 i 4:2:2 |
| Avid DNxHD (w MXF) |Tak |
| DVCPro/DVCProHD (in MXF) |Tak |
| Digital Video (DV) (w plikach AVI) |Tak |
| JPEG 2000 |Yes |
| MPEG-2 (do 422 profilu i wysokiego poziomu), w tym wariantów, takich jak XDCAM, XDCAM HD, XDCAM IMX, CableLabs® i D10) |Do 422 profilu |
| MPEG-1 |Tak |
| VC-1/WMV9 |Tak |
| Canopus CENTRALĄ/HQX |Nie |
| MPEG-4 — część 2 |Tak |
| [Theora](https://en.wikipedia.org/wiki/Theora) |Tak |
| YUV420 unskompresowano lub Mezzanine |Tak |
| Apple ProRes 422 |Yes |
| Apple ProRes 422 LT |Tak |
| Apple ProRes 422 CENTRALĄ |Yes |
| Serwer proxy Apple ProRes |Tak |
| Apple ProRes 4444 |Tak |
| Apple ProRes 4444 XQ |Yes |
| HEVC/H.265| Profil główny|

## <a name="input-audio-codecs"></a>Kodery-dekoder Audio Input
| Kodery-dekoder Audio Input | Obsługiwane |
| --- | --- |
| AAC (AAC-LC, AAC-IT i AAC-HEv2; do 5,1) |Tak |
| MPEG Layer 2 |Yes |
| MP3 (warstwa audio MPEG-1) |Tak |
| Dźwięk Windows Media |Tak |
| WAV/PCM |Yes |
| [FLAC](https://en.wikipedia.org/wiki/FLAC)</a> |Tak |
| [Opus](https://go.microsoft.com/fwlink/?LinkId=822667) |Tak |
| [Vorbis](https://en.wikipedia.org/wiki/Vorbis)</a> |Tak |
| AMR (adaptacyjna wielowskaźnikowa) |Tak |
| AES (SMPTE 331M i 302M, AES3-2003) |Nie |
| Dolby® E |Nie |
| Dolby® Digital (AC3) |Nie |
| Dolby® Digital Plus (E-AC3) |Nie |

## <a name="output-formats-and-codecs"></a>Formaty i kodeki wyjściowe
W poniższej tabeli wymieniono kodery-dekoder i formaty plików, które są obsługiwane do eksportowania.

| Format pliku | Koder-dekoder wideo | Koder-dekoder audio |
| --- | --- | --- |
| MP4 <br/><br/>(w tym kontenery MP4 o wieloszybkości transmisji bitów) |H. 264 (profile o wysokim, głównym i bazowym) |AAC-LC, HE-AAC v1, HE-AAC v2 |
| MPEG2-TS |H. 264 (profile o wysokim, głównym i bazowym) |AAC-LC, HE-AAC v1, HE-AAC v2 |

## <a name="next-steps"></a>Następne kroki

[Tworzenie przekształcenia z niestandardowym ustawieniem wstępnym](customize-encoder-presets-how-to.md)
