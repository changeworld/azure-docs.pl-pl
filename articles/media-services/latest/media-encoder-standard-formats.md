---
title: Formaty koderów standardowych i kodery-dekoder na platformie Azure
description: Ten artykuł zawiera listę najpopularniejszych formatów importu i eksportu plików, których można używać z programem StandardEncoderPreset.
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
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79251364"
---
# <a name="standard-encoder-formats-and-codecs"></a>Standardowe formaty i kodeki kodera

Ten artykuł zawiera listę najpopularniejszych formatów importu i eksportu plików, których można używać z programem [StandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset). Aby uzyskać informacje na temat tworzenia niestandardowych ustawień wstępnych przy użyciu programu **StandardEncoderPreset**, zobacz [Tworzenie przekształcenia z niestandardowym ustawieniem wstępnym](customize-encoder-presets-how-to.md).

## <a name="input-containerfile-formats"></a>Dane wejściowe w formacie kontenera/pliku

| Formaty plików (rozszerzenia plików) | Obsługiwane |
| --- | --- |
| FLV (z kodekami H. 264 i AAC) (. FLV) |Yes |
| MXF    (.mxf) |Yes |
| GXF    (.gxf) |Yes |
| MPEG2-PS, MPEG2-TS, 3GP (. TS,. PS,. 3gp,. 3GPP,. mpg) |Yes |
| Windows Media Video (WMV)/ASF (. wmv,. ASF) |Yes |
| AVI (nieskompresowany 8bit/10bit) (AVI) |Yes |
| MP4 (. mp4,. M4A,. m4v)/ISMV (. ISMA,. ISMV) |Yes |
| [Cyfrowe nagrywanie wideo firmy Microsoft (DVR-MS)](https://msdn.microsoft.com/library/windows/desktop/dd692984) (. DVR-MS) |Yes |
| Matroska/WebM (. mkv) |Yes |
| WAVE/WAV (wav) |Yes |
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
| Avid DNxHD (w MXF) |Yes |
| DVCPro/DVCProHD (in MXF) |Yes |
| Digital Video (DV) (w plikach AVI) |Yes |
| JPEG 2000 |Yes |
| MPEG-2 (do 422 profilu i wysokiego poziomu), w tym wariantów, takich jak XDCAM, XDCAM HD, XDCAM IMX, CableLabs® i D10) |Do 422 profilu |
| MPEG-1 |Yes |
| VC-1/WMV9 |Yes |
| Canopus CENTRALĄ/HQX |Nie |
| MPEG-4 — część 2 |Yes |
| [Theora](https://en.wikipedia.org/wiki/Theora) |Yes |
| YUV420 unskompresowano lub Mezzanine |Yes |
| Apple ProRes 422 |Yes |
| Apple ProRes 422 LT |Yes |
| Apple ProRes 422 CENTRALĄ |Yes |
| Serwer proxy Apple ProRes |Yes |
| Apple ProRes 4444 |Yes |
| Apple ProRes 4444 XQ |Yes |
| HEVC/H.265| Profil główny|

## <a name="input-audio-codecs"></a>Kodery-dekoder Audio Input
| Kodery-dekoder Audio Input | Obsługiwane |
| --- | --- |
| AAC (AAC-LC, AAC-IT i AAC-HEv2; do 5,1) |Yes |
| MPEG Layer 2 |Yes |
| MP3 (warstwa audio MPEG-1) |Yes |
| Dźwięk Windows Media |Yes |
| WAV/PCM |Yes |
| [FLAC](https://en.wikipedia.org/wiki/FLAC)</a> |Yes |
| [Opus](https://go.microsoft.com/fwlink/?LinkId=822667) |Yes |
| [Vorbis](https://en.wikipedia.org/wiki/Vorbis)</a> |Yes |
| AMR (adaptacyjna wielowskaźnikowa) |Yes |
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
