---
title: Omówienie dynamicznego tworzenia pakietów usługi Azure Media Services | Dokumentacja firmy Microsoft
description: Temat zawiera omówienie funkcji dynamicznego tworzenia pakietów w usłudze Media Services.
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/25/2019
ms.author: juliako
ms.openlocfilehash: f20a242fc5f674738cde6af7d2797205f8298514
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/26/2019
ms.locfileid: "56871319"
---
# <a name="dynamic-packaging"></a>Dynamiczne tworzenie pakietów

Usługa content protection formatów różne technologie klienta (na przykład iOS i konsoli XBOX) i Microsoft Azure Media Services może służyć do dostarczania wiele nośnika źródłowego formatów plików, przesyłanie strumieniowe formatów multimediów. Ci klienci zrozumienie różnych protokołów, na przykład dla systemu iOS wymaga formatu HTTP Live Streaming (HLS) i konsolach Xbox wymagają Smooth Streaming. Jeśli masz zestaw z adaptacyjną szybkością transmisji bitów (bitów) w formacie MP4 (ISO Base nośników 14496-12) plików lub zestaw Smooth Streaming pliki adaptacyjną szybkością transmisji bitów, które mają służyć do klientów, którzy zrozumieć HLS, MPEG DASH lub Smooth Streaming, możesz korzystać z zalet dynamiczny Pakowanie. Obsługiwane są SD/HD/UHD - 4K, opakowywanie jest niezależny od rozdzielczości wideo.

[Punkty końcowe przesyłania strumieniowego](streaming-endpoint-concept.md) usługa funkcję dynamicznego tworzenia pakietów w usłudze Media Services umożliwia dostarczanie zawartości multimedialnej dla graczy klienta. Funkcję dynamicznego tworzenia pakietów jest funkcją, które standardowo wszystkie punkty końcowe przesyłania strumieniowego (standardowy lub Premium). Nie ma żadnych dodatkowych kosztów związanych z tej funkcji usługi Media Services v3. Za pomocą funkcji dynamicznego tworzenia pakietów wymagany jest element zawartości zawierający zestaw plików MP4 o plikach manifestu. Następnie w oparciu o formatu określonego w manifeście lub fragment żądania, strumień jest dostarczany za pomocą wybranego protokołu. Dzięki temu wystarczy przechowywać i opłacać pliki w jednym formacie magazynu, a usługa Media Services utworzy oraz udostępni właściwą odpowiedź na podstawie żądań klienta.

W usłudze Media Services funkcję dynamicznego tworzenia pakietów jest używany zarówno są przesyłania strumieniowego na żądanie i na żywo.

Na poniższym diagramie przedstawiono przesyłania strumieniowego na żądanie za pomocą funkcji dynamicznego tworzenia pakietów przepływu pracy.

![Dynamiczne kodowania](./media/dynamic-packaging-overview/media-services-dynamic-packaging.svg)

## <a name="common-video-on-demand-workflow"></a>Typowy przepływ pracy z wideo na żądanie

Poniżej przedstawiono typowe Media Services, przepływ pracy transmisji strumieniowej użycia funkcji dynamicznego tworzenia pakietów.

1. Przekaż plik wejściowy (nazywane plik mezzanine). Na przykład H.264, MP4 lub WMV (Aby uzyskać listę obsługiwanych formatów, zobacz [formaty obsługiwane przez Media Encoder Standard](media-encoder-standard-formats.md).
2. Kodowanie pliku mezzanine do H.264 MP4 o adaptacyjnej szybkości transmisji bitów zestawów.
3. Opublikuj element zawartości zawierający adaptacyjną szybkością transmisji bitów, zestawu plików MP4.
4. Tworzenie adresów URL, których platformą docelową w różnych formatach (HLS, Dash i Smooth Streaming). Punkt końcowy przesyłania strumieniowego będzie uwzględniać obsługująca prawidłowy manifest oraz żądań dotyczących tych różnych formatach. Na przykład:

    - HLS: `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=m3u8-aapl)`
    - Dash: `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=mpd-time-csf)`
    - Gładki: `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest`

## <a name="video-codecs-supported-by-dynamic-packaging"></a>Koderów-dekoderów wideo obsługiwanych przez funkcję dynamicznego tworzenia pakietów

Dynamiczne tworzenie pakietów obsługuje pliki w formacie MP4, które zawierają zakodowane przy użyciu [H.264](https://en.m.wikipedia.org/wiki/H.264/MPEG-4_AVC) (AVC MPEG-4 lub AVC1), [H.265](https://en.m.wikipedia.org/wiki/High_Efficiency_Video_Coding) (— HEVC, hev1 lub hvc1).

## <a name="audio-codecs-supported-by-dynamic-packaging"></a>Kodery-dekodery audio obsługiwane przez funkcję dynamicznego tworzenia pakietów

Dynamiczne tworzenie pakietów obsługuje pliki w formacie MP4, które zawierają audio zakodowane za pomocą [AAC](https://en.wikipedia.org/wiki/Advanced_Audio_Coding) (AAC-LC, HE-AAC v1, v2 HE AAC), [Dolby Digital Plus](https://en.wikipedia.org/wiki/Dolby_Digital_Plus) (Enhanced AC-3 lub E AC3), lub [DTS](https://en.wikipedia.org/wiki/DTS_%28sound_system%29) (DTS Express, DTS LBR, DTS HD, HD DTS bezstratne).

> [!NOTE]
> Dynamiczne tworzenie pakietów nie obsługuje plików, które zawierają [Dolby Digital](https://en.wikipedia.org/wiki/Dolby_Digital) audio (AC3) (jest to starszy koder-dekoder).

## <a name="next-steps"></a>Kolejne kroki

[Przekazywanie, kodowanie, filmy wideo usługi stream](stream-files-tutorial-with-api.md)

