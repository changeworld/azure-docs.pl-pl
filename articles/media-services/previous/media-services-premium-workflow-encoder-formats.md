---
title: Formaty i kodeki kodu źródłowe Media Encoder Premium | Dokumenty firmy Microsoft
description: W tym temacie przedstawiono omówienie formatów i kodeków formatów przepływu pracy usługi Media Encoder Premium
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
ms.date: 03/19/2019
ms.author: juliako
ms.reviewer: anilmur
ms.openlocfilehash: 87cd7c63939331190530a46071a6b4c40480562f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79269759"
---
# <a name="media-encoder-premium-workflow-formats-and-codecs"></a>Formaty i kodeki usługi Media Encoder Premium

> [!NOTE]
> Procesor multimediów Media Encoder Premium Workflow omówiony w tym temacie nie jest dostępny w Chinach. 

Ten dokument zawiera listę formatów plików wejściowych i wyjściowych oraz kodeków, które są obsługiwane przez publiczną wersję zapoznawczą **kodera przepływu pracy usługi Media Encoder Premium.**

[Formaty wejściowe i kodeki usługi Media Encoder Premium](#input_formats)

Formaty wyjściowe i kodeki przepływu pracy usługi Media Encoder Premium

**Przepływ pracy usługi Media Encoder Premium** obsługuje napisy kodowane opisane w [tej](#closed_captioning) sekcji. 

## <a name="media-encoder-premium-workflow-input-formats-and-codecs"></a><a id="input_formats"></a>Formaty wejściowe i kodeki usługi Media Encoder Premium

W poniższej sekcji wymieniono kodeki i formaty plików, które ten procesor multimediów obsługuje jako dane wejściowe.

### <a name="input-containerfile-formats"></a>Formaty wejściowych kontenerów/plików

* Adobe® Flash® F4V
* MXF/SMPTE 377M
* Gxf
* Strumienie transportowe MPEG-2
* Strumienie programów MPEG-2
* MPEG-4/MP4
* Windows Media/ASF
* AVI (Nieskompresowany 8bit/10bit)

### <a name="input-video-codecs"></a>Kodery-dekodery wejściowych danych wideo

* AVC 8-bitowy/10-bitowy, do 4:2:2, wraz z AVCIntra
* Avid DNxHD (w pliku MXF)
* DVCPro/DVCProHD (w pliku MXF)
* HEVC/H.265, Profil główny i główny 10
* JPEG2000
* MPEG-2 (do 422 profile i wysokiego poziomu; w tym warianty takie jak XDCAM, XDCAM HD, XDCAM IMX, CableLabs® i D10)
* MPEG-1
* Windows Media Wideo/VC-1

### <a name="input-audio-codecs"></a>Kodery-dekodery wejściowych danych audio

* AES (SMPTE 331M oraz 302M, AES3-2003)
* Dolby® E
* Dolby® Digital (AC3)
* AAC (AAC-LC, AAC-HE oraz AAC-HEv2; maksymalnie 5.1)
* MPEG Layer 2
* MP3 (MPEG-1 Audio Layer 3)
* Windows Media Audio
* WAV/PCM

## <a name="media-encoder-premium-workflow-output-formats-and-codecs"></a><a id="output_format"></a>Formaty wyjściowe i kodeki przepływu pracy usługi Media Encoder Premium

W poniższej sekcji wymieniono kodeki i formaty plików, które są obsługiwane jako dane wyjściowe z tego procesora multimediów.

### <a name="output-containerfile-formats"></a>Formaty kontenera/pliku wyjściowego

* Adobe® Flash® F4V
* MXF (OP1a, XDCAM i AS02)
* DPP (w tym AS11)
* Gxf
* MPEG-4/MP4
* Windows Media/ASF
* AVI (Nieskompresowany 8bit/10bit)
* Płynny format pliku strumieniowego (PIFF 1.3)
* MPEG-TS 

### <a name="output-video-codecs"></a>Kodeki wideo wyjściowe

* AVC (H.264; 8-bit; do High Profile, Level 5.2; 4K Ultra HD; AVC Intra)
* Avid DNxHD (w pliku MXF)
* DVCPro/DVCProHD (w pliku MXF)
* MPEG-2 (do 422 profile i wysokiego poziomu; w tym warianty takie jak XDCAM, XDCAM HD, XDCAM IMX, CableLabs® i D10)
* MPEG-1
* Windows Media Wideo/VC-1
* Tworzenie miniatur w formacie JPEG
* HEVC (H.265; 8-bitowy i 10-bitowy, Główny i główny profil 10)


### <a name="output-audio-codecs"></a>Kodeki audio wyjściowych

* AES (SMPTE 331M oraz 302M, AES3-2003)
* Dolby® Digital (AC3)
* Dolby® Digital Plus (E-AC3) do 7.1
* AAC (AAC-LC, AAC-HE oraz AAC-HEv2; maksymalnie 5.1)
* MPEG Layer 2
* MP3 (MPEG-1 Audio Layer 3)
* Windows Media Audio

>[!NOTE]
>Jeśli kodujesz do dolby® Digital (AC3), dane wyjściowe mogą być zapisywane tylko w pliku ISO MP4.

## <a name="support-for-closed-captioning"></a><a id="closed_captioning"></a>Obsługa napisów kodowych

Przy pozyskiwania przepływ **pracy media koder premium** obsługuje:

1. Pliki SCC
2. Pliki SMPTE-TT
3. CEA-608/CEA-708 – przenoszone jako dane użytkownika (komunikaty SEI strumieni podstawowych H.264, ATSC/53, SCTE20) lub przenoszone jako dane pomocnicze w plikach MXF/GXF
4. Pliki napisów STL

Na wyjściu dostępne są następujące opcje:

1. Tłumaczenie CEA-608 na CEA-708
2. CEA-608/CEA-708 przechodzi (osadzone w komunikatach SEI strumieni podstawowych H.264 lub przenoszone jako dane pomocnicze w plikach MXF)
3. Scc
4. Tekst czasowy SMPTE (ze źródła CEA-608 na SMPTE RP2052; w tym tworzenie plików DFXP)
5. Plik napisów SRT
6. Strumienie napisów DVB

> [!NOTE]
> Nie wszystkie powyższe formaty danych wyjściowych są obsługiwane do dostarczania za pośrednictwem przesyłania strumieniowego w usłudze Azure Media Services.

## <a name="known-issues"></a>Znane problemy

Jeśli wejściowy film wideo nie zawiera napisów kodowych, wyjściowy zasób nadal będzie zawierał pusty plik TTML. 

## <a name="need-help"></a>Potrzebujesz pomocy?

Możesz otworzyć bilet pomocy technicznej, przechodząc do [nowego żądania pomocy technicznej](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)
## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

