---
title: Formaty Media Encoder Premium w przepływie pracy i koderów-dekoderów | Dokumentacja firmy Microsoft
description: Ten temat zawiera omówienie koderów-dekoderów i formaty Media Encoder Premium przepływu pracy formatów
services: media-services
documentationcenter: ''
author: juliako
manager: erik43
editor: ''
ms.assetid: b197fce8-3b9b-4189-8d08-486810c0426f
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/09/2017
ms.author: juliako;anilmur
ms.openlocfilehash: 68ba76ee18b3acc1c2825a217641c21a799132c1
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34638592"
---
# <a name="media-encoder-premium-workflow-formats-and-codecs"></a>Koderów-dekoderów i formaty Media Encoder Premium w przepływie pracy
> [!NOTE]
> Odpowiedzi na pytania koder premium, poczty e-mail mepd@microsoft.com.
> 
> Procesor multimediów Media Encoder Premium w przepływie pracy omówione w tym temacie nie jest dostępna w Chinach. 
> 
> 

Ten dokument zawiera listę formatów plików wejściowych i wyjściowych i koderów-dekoderów, które są obsługiwane w wersji zapoznawczej **Media Encoder Premium w przepływie pracy** kodera.

[Koderów-dekoderów i formatów danych wejściowych Worflow Premium kodera multimediów](#input_formats)

[Koderów-dekoderów i formatów wyjściowych Worflow Premium kodera multimediów](#output_formats)

**Przepływ pracy Premium kodera multimediów** obsługuje kodowane opisanego w [to](#closed_captioning) sekcji. 

## <a id="input_formats"></a>Media Encoder Premium w przepływie pracy wprowadź koderów-dekoderów i formaty
W poniższej sekcji przedstawiono formatów koderów-dekoderów i plików, które obsługuje ten procesor multimediów jako dane wejściowe.

### <a name="input-containerfile-formats"></a>Kontener/formatów wejściowych
* Adobe® Flash® F4V
* MXF/SMPTE 377M
* GXF
* Strumienie transportu MPEG-2
* Strumienie programu MPEG-2
* MPEG-4/MP4
* ASF Media systemu Windows
* AVI (nieskompresowanych 8-bitową/10 bity)

### <a name="input-video-codecs"></a>Dane wejściowe kodery-dekodery wideo
* AVC 8-bitowych/10-bitowy, maksymalnie 4:2:2, w tym AVCIntra
* Avid DNxHD (w MXF)
* DVCPro/DVCProHD (in MXF)
* HEVC/H.265, Main i profil Main 10
* JPEG2000
* MPEG-2 (maksymalnie 422 profilu i wysokiego poziomu, m.in wariantów, takich jak XDCAM, XDCAM HD, XDCAM IMX, CableLabs® i D10)
* MPEG-1
* Windows Media wideo/VC-1

### <a name="input-audio-codecs"></a>Dane wejściowe Audio koderów-dekoderów
* AES (SMPTE 331 M i 302 M, AES3 2003)
* Dolby® E
* Dolby® Digital (AC3)
* AAC (AAC-LC, AAC HE i AAC-HEv2; maksymalnie 5.1)
* MPEG warstwy 2
* Mp3 (MPEG-1 Audio warstwy 3)
* Program Windows Media Audio
* WAV/PCM

## <a id="output_format"></a>Koderów-dekoderów i formatów wyjściowych Media Encoder Premium przepływu pracy
W poniższej sekcji przedstawiono formatów koderów-dekoderów i plików, które są obsługiwane jako dane wyjściowe z tego nośnika procesora.

### <a name="output-containerfile-formats"></a>Kontener i plik formatów
* Adobe® Flash® F4V
* MXF (OP1a, XDCAM i AS02)
* DPP (w tym AS11)
* GXF
* MPEG-4/MP4
* ASF Media systemu Windows
* AVI (nieskompresowanych 8-bitową/10 bity)
* Płynnego przesyłania strumieniowego Format pliku (PIFF 1.3)
* MPEG-TS 

### <a name="output-video-codecs"></a>Dane wyjściowe kodery-dekodery wideo
* AVC (H.264; 8-bitową; do profilu wysokiego poziomu 5.2; 4 K bardzo HD; Wewnątrz AVC)
* Avid DNxHD (w MXF)
* DVCPro/DVCProHD (in MXF)
* MPEG-2 (maksymalnie 422 profilu i wysokiego poziomu, m.in wariantów, takich jak XDCAM, XDCAM HD, XDCAM IMX, CableLabs® i D10)
* MPEG-1
* Windows Media wideo/VC-1
* Tworzenie miniatur JPEG

### <a name="output-audio-codecs"></a>Dane wyjściowe Audio koderów-dekoderów
* AES (SMPTE 331 M i 302 M, AES3 2003)
* Dolby® Digital (AC3)
* Dolby® Digital Plus (E-AC3) do 7.1
* AAC (AAC-LC, AAC HE i AAC-HEv2; maksymalnie 5.1)
* MPEG warstwy 2
* Mp3 (MPEG-1 Audio warstwy 3)
* Program Windows Media Audio

>[!NOTE]
>Gdy na cyfrowe® Dolby (AC3), dane wyjściowe można zapisywać tylko do pliku ISO MP4.

## <a id="closed_captioning"></a>Obsługa kodowane
Na pozyskiwania, **Media Encoder Premium w przepływie pracy** obsługuje:

1. Pliki SCC
2. Pliki SMPTE TT
3. CEA-608/CEA-708 — jako dane użytkownika (SEI wiadomości H.264 podstawowe strumieni, ATSC/53, SCTE20) lub przekazane jako dodatkowe dane w plikach MXF/GXF
4. Pliki napisów STL

W danych wyjściowych dostępne są następujące opcje:

1. CEA-608 do tłumaczenia CEA 708
2. Przekazuj CEA-608/CEA-708 (osadzone w wiadomości SEI strumieniami H.264 lub przekazane jako dodatkowe dane w plikach MXF)
3. SCC
4. Tekst upłynął SMPTE (ze źródła CEA 608 na SMPTE RP2052; w tym tworzenia pliku DFXP)
5. Podtytuł SRT pliku
6. DVB podtytuł strumieni

Uwaga: nie wszystkie powyższe formatów wyjściowych są obsługiwane w celu dostarczania za pośrednictwem przesyłania strumieniowego w usłudze Azure Media Services.

## <a name="known-issues"></a>Znane problemy
Jeśli wejściowy plik wideo nie zawiera kodowane się, dane wyjściowe zasobów będzie nadal zawierać pusty plik TTML. 

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

