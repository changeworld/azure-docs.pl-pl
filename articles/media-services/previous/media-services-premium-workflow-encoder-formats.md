---
title: Media Encoder Premium Workflow formaty i kodery-dekodery | Dokumentacja firmy Microsoft
description: Ten temat zawiera omówienie formatów Media Encoder Premium przepływu pracy formaty i kodery-dekodery
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
ms.author: juliako;anilmur
ms.openlocfilehash: 25f32750b612bb66f23eb19c378f7935689f3a73
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61463093"
---
# <a name="media-encoder-premium-workflow-formats-and-codecs"></a>Media Encoder Premium Workflow formaty i kodery-dekodery

> [!NOTE]
> Pytania Encoder w warstwie premium, wiadomość e-mail na adres mepd@microsoft.com.
> 
> Procesor multimediów Media Encoder Premium Workflow omówione w tym temacie nie jest dostępna w Chinach. 

Ten dokument zawiera listę plików wejściowych i wyjściowych formatów i koderów-dekoderów, które są obsługiwane w wersji zapoznawczej **Media Encoder Premium Workflow** kodera.

[Media Encoder Premium Workflow danych wejściowych, formaty i kodery-dekodery](#input_formats)

Formaty danych wyjściowych programu Media Encoder Premium przepływu pracy i kodery-dekodery

**Media Encoder Premium Workflow** obsługuje kodowanych opisanego w [to](#closed_captioning) sekcji. 

## <a id="input_formats"></a>Media Encoder Premium Workflow danych wejściowych, formaty i kodery-dekodery

W poniższej sekcji przedstawiono formaty koderów-dekoderów i plików, które to procesor multimediów obsługuje jako dane wejściowe.

### <a name="input-containerfile-formats"></a>Dane wejściowe formaty kontenerów/plików

* Adobe® Flash® F4V
* MXF/SMPTE 377M
* GXF
* Strumienie transportu MPEG-2
* Strumienie programu MPEG-2
* MPEG-4/MP4
* Windows Media/ASF
* AVI (nieskompresowany 8-bitowy/10-bitowy)

### <a name="input-video-codecs"></a>Dane wejściowe koderów-dekoderów wideo

* AVC 8-bitowy/10-bitowy, maksymalnie 4:2:2, wraz z AVCIntra
* Avid DNxHD (in MXF)
* DVCPro/DVCProHD (in MXF)
* HEVC/H.265, główne i profil Main 10
* JPEG2000
* MPEG-2 (profil 422 i wysoki poziom; wraz z wariantami, takich jak XDCAM, XDCAM HD, XDCAM IMX, CableLabs® oraz D10)
* MPEG-1
* Windows Media Video/VC-1

### <a name="input-audio-codecs"></a>Dane wejściowe Audio kodery-dekodery

* AES (SMPTE 331 M oraz 302 M, AES3-2003)
* Dolby® E
* Dolby® Digital (AC3)
* AAC (AAC-LC, AAC-HE oraz AAC-HEv2; maksymalnie 5.1)
* MPEG Layer 2
* Mp3 (MPEG-1 Audio warstwa 3)
* Program Windows Media Audio
* WAV/PCM

## <a id="output_format"></a>Formaty danych wyjściowych programu Media Encoder Premium przepływu pracy i kodery-dekodery

W poniższej sekcji przedstawiono formaty koderów-dekoderów i plików, które są obsługiwane jako dane wyjściowe to procesor multimediów.

### <a name="output-containerfile-formats"></a>Formaty kontenerów/plików danych wyjściowych

* Adobe® Flash® F4V
* MXF (OP1a, XDCAM i AS02)
* DPP (w tym AS11)
* GXF
* MPEG-4/MP4
* Windows Media/ASF
* AVI (nieskompresowany 8-bitowy/10-bitowy)
* Bezproblemowe przesyłania strumieniowego Format pliku (PIFF 1.3)
* MPEG-TS 

### <a name="output-video-codecs"></a>Dane wyjściowe koderów-dekoderów wideo

* AVC (H.264; 8-bitową; do profilu wysokiego poziomu 5.2; 4 K najwyższej jakości HD; Wewnątrz AVC)
* Avid DNxHD (in MXF)
* DVCPro/DVCProHD (in MXF)
* MPEG-2 (profil 422 i wysoki poziom; wraz z wariantami, takich jak XDCAM, XDCAM HD, XDCAM IMX, CableLabs® oraz D10)
* MPEG-1
* Windows Media Video/VC-1
* Tworzenie miniatur JPEG
* — HEVC (H.265; 8-bitową i 10-bitowy, główne i profilu 10 główny)

  Obsługa HDR 10 jest dostępny w niektórych scenariuszach, skontaktuj się z pomocą mepd@microsoft.com Aby uzyskać więcej informacji


### <a name="output-audio-codecs"></a>Kodery-dekodery Audio w danych wyjściowych

* AES (SMPTE 331 M oraz 302 M, AES3-2003)
* Dolby® Digital (AC3)
* Dolby® Digital Plus (E-AC3) do 7.1
* AAC (AAC-LC, AAC-HE oraz AAC-HEv2; maksymalnie 5.1)
* MPEG Layer 2
* Mp3 (MPEG-1 Audio warstwa 3)
* Program Windows Media Audio

>[!NOTE]
>Jeśli użytkownik kodowanie Dolby® Digital (AC3), dane wyjściowe można zapisać tylko do pliku ISO MP4.

## <a id="closed_captioning"></a>Obsługa kodowane

Na przyjęcie, **Media Encoder Premium Workflow** obsługuje:

1. Pliki SCC
2. Pliki SMPTE TT
3. Napisy kodowane CEA-608/napisy kodowane CEA-708 — przenoszone jako dane użytkownika (SEI wiadomości H.264 podstawowe strumieni, ATSC/53, SCTE20) lub przekazane jako dodatkowe dane w plikach MXF/GXF
4. Pliki podtytuł STL

W danych wyjściowych dostępne są następujące opcje:

1. Napisy kodowane CEA-608 tłumaczenia napisy kodowane CEA-708
2. Napisy kodowane CEA-608/napisy kodowane CEA-708 przekazywania (osadzony w wiadomości SEI strumieniami H.264 lub przekazane jako dodatkowe dane w plikach MXF)
3. SCC
4. Tekst SMPTE upłynął limit czasu (od źródła napisy kodowane CEA-608 na SMPTE RP2052, w tym tworzenia pliku DFXP)
5. Plik z napisami SRT
6. Strumienie podtytuł DVB

> [!NOTE]
> Nie wszystkie powyższe formaty danych wyjściowych są obsługiwane dla dostawy za pośrednictwem przesyłania strumieniowego w usłudze Azure Media Services.

## <a name="known-issues"></a>Znane problemy

Jeśli wejściowy plik wideo zawiera napisy kodowane dane wyjściowe zasobów będzie nadal zawierać pusty plik TTML. 

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

