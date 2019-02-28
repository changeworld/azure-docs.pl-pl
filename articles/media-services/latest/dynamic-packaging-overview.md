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
ms.date: 02/26/2019
ms.author: juliako
ms.openlocfilehash: cd4eacc918acdf50bb256077030b86e121f663f0
ms.sourcegitcommit: 1afd2e835dd507259cf7bb798b1b130adbb21840
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/28/2019
ms.locfileid: "56985805"
---
# <a name="dynamic-packaging"></a>Dynamiczne tworzenie pakietów

Usługa content protection formatów różne technologie klienta (na przykład iOS i konsoli XBOX) i Microsoft Azure Media Services może służyć do dostarczania wiele nośnika źródłowego formatów plików, przesyłanie strumieniowe formatów multimediów. Ci klienci zrozumienie różnych protokołów, na przykład dla systemu iOS wymaga formatu HTTP Live Streaming (HLS) i konsolach Xbox wymagają Smooth Streaming. Jeśli masz zestaw z adaptacyjną szybkością transmisji bitów (bitów) w formacie MP4 (ISO Base nośników 14496-12) plików lub zestaw Smooth Streaming pliki adaptacyjną szybkością transmisji bitów, które mają służyć do klientów, którzy zrozumieć HLS, MPEG DASH lub Smooth Streaming, możesz korzystać z zalet dynamiczny Pakowanie. Obsługiwane są SD/HD/UHD - 4K, opakowywanie jest niezależny od rozdzielczości wideo.

[Punkty końcowe przesyłania strumieniowego](streaming-endpoint-concept.md) usługa funkcję dynamicznego tworzenia pakietów w usłudze Media Services umożliwia dostarczanie zawartości multimedialnej dla graczy klienta. Funkcję dynamicznego tworzenia pakietów jest funkcją, która jest dostępna na wszystkich standardowych **punkty końcowe przesyłania strumieniowego** (standardowa / Premium). Nie ma żadnych dodatkowych kosztów związanych z tej funkcji usługi Media Services v3. 

Aby móc korzystać z **funkcję dynamicznego tworzenia pakietów**, musisz mieć **zasobów** z zestawu plików MP4 i plikach manifestu. Jednym ze sposobów przekazania plików jest Koduj plik (źródłowy) mezzanine za pomocą usługi Media Services. Aby w zasobie (z każdego pliku MP4 z zakodowanym i manifesty serwera i klienta) dostępna dla klientów w celu odtwarzania filmów wideo, należy utworzyć **lokalizatora przesyłania strumieniowego** i późniejszego kompilowania adresów URL przesyłania strumieniowego. Następnie w oparciu o formatu określonego w manifeście klienta, strumień jest dostarczany za pomocą wybranego protokołu.

Dzięki temu wystarczy przechowywać i opłacać pliki w jednym formacie magazynu, a usługa Media Services utworzy oraz udostępni właściwą odpowiedź na podstawie żądań klienta. 

W usłudze Media Services funkcję dynamicznego tworzenia pakietów jest używany, czy są przesyłania strumieniowego na żywo lub na żądanie. Na poniższym diagramie przedstawiono przesyłania strumieniowego na żądanie za pomocą funkcji dynamicznego tworzenia pakietów przepływu pracy.

![Dynamiczne kodowania](./media/dynamic-packaging-overview/media-services-dynamic-packaging.svg)

## <a name="common-video-on-demand-workflow"></a>Typowy przepływ pracy z wideo na żądanie

Poniżej przedstawiono typowe Media Services, przepływ pracy transmisji strumieniowej użycia funkcji dynamicznego tworzenia pakietów.

1. Przekaż plik wejściowy (nazywane plik mezzanine). Na przykład H.264, MP4 lub WMV (Aby uzyskać listę obsługiwanych formatów, zobacz [formaty obsługiwane przez Media Encoder Standard](media-encoder-standard-formats.md).
2. Kodowanie pliku mezzanine do H.264 MP4 o adaptacyjnej szybkości transmisji bitów zestawów.
3. Opublikuj element zawartości zawierający adaptacyjną szybkością transmisji bitów, zestawu plików MP4. Możesz opublikować, tworząc **lokalizatora przesyłania strumieniowego**.
4. Tworzenie adresów URL, których platformą docelową w różnych formatach (HLS, Dash i Smooth Streaming). **Punkt końcowy przesyłania strumieniowego** będzie zajmie się obsługująca prawidłowy manifest oraz żądań dotyczących tych różnych formatach.

## <a name="encode-to-adaptive-bitrate-mp4s"></a>Wykonaj kodowanie do każdego pliku MP4 z adaptacyjną szybkością transmisji bitów

Aby uzyskać informacje o [jak kodować wideo za pomocą usługi Media Services](encoding-concept.md), zobacz następujące przykłady:

* [Kodowanie z adresu URL HTTPS, przy użyciu wbudowanych ustawień wstępnych](job-input-from-http-how-to.md)
* [Kodowanie pliku lokalnego za pomocą wbudowanych ustawień wstępnych](job-input-from-local-file-how-to.md)
* [Tworzenie niestandardowego ustawienia wstępnego pod kątem określonych wymagań scenariusza lub urządzenia](customize-encoder-presets-how-to.md)

Wykaz usługi Media Encoder Standard formaty i kodeki narzędzia, zobacz [formaty i kodery-dekodery](media-encoder-standard-formats.md)

## <a name="delivery-protocols"></a>Protokoły dostarczania

|Protokół|Przykład|
|---|---|
|HLS V4 |`https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=m3u8-aapl)`|
|HLS V3 |`https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=m3u8-aapl-v3)`|
|HLS CMAF| `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=m3u8-cmaf)`|
|MPEG DASH CSF| `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=mpd-time-csf)` |
|MPEG DASH CMAF|`https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=mpd-time-cmaf)` |
|Smooth Streaming| `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest`|

## <a name="video-codecs-supported-by-dynamic-packaging"></a>Koderów-dekoderów wideo obsługiwanych przez funkcję dynamicznego tworzenia pakietów

Dynamiczne tworzenie pakietów obsługuje pliki w formacie MP4, które zawierają zakodowane przy użyciu [H.264](https://en.m.wikipedia.org/wiki/H.264/MPEG-4_AVC) (AVC MPEG-4 lub AVC1), [H.265](https://en.m.wikipedia.org/wiki/High_Efficiency_Video_Coding) (— HEVC, hev1 lub hvc1).

## <a name="audio-codecs-supported-by-dynamic-packaging"></a>Kodery-dekodery audio obsługiwane przez funkcję dynamicznego tworzenia pakietów

Dynamiczne tworzenie pakietów obsługuje pliki w formacie MP4, które zawierają audio zakodowane za pomocą [AAC](https://en.wikipedia.org/wiki/Advanced_Audio_Coding) (AAC-LC, HE-AAC v1, v2 HE AAC), [Dolby Digital Plus](https://en.wikipedia.org/wiki/Dolby_Digital_Plus) (Enhanced AC-3 lub E AC3), lub [DTS](https://en.wikipedia.org/wiki/DTS_%28sound_system%29) (DTS Express, DTS LBR, DTS HD, HD DTS bezstratne).

> [!NOTE]
> Dynamiczne tworzenie pakietów nie obsługuje plików, które zawierają [Dolby Digital](https://en.wikipedia.org/wiki/Dolby_Digital) audio (AC3) (jest to starszy koder-dekoder).

## <a name="manifest-files-overview"></a>Przegląd plików manifestu

A **manifestu** (odtwarzania), (na podstawie tekstu lub opartego na języku XML) obejmują przesyłanie strumieniowe metadane, takie jak: śledzenie typu (audio, wideo lub tekst), Śledź nazwę, godzina rozpoczęcia i zakończenia, szybkości transmisji bitów (właściwości), Śledź języków, okna prezentacji z (przesuwanie Okno stały czas trwania), kodera-dekodera wideo (FourCC). Informuje również gracza, aby pobrać następny fragment, podając informacje o następnej rozgrywane fragmenty wideo dostępne i ich lokalizacji. Fragmenty (lub segmentów) są rzeczywiste "fragmentów" zawartości wideo.

Oto przykład HLS pliku manifestu: 

```
#EXT-X-MEDIA:TYPE=AUDIO,GROUP-ID="audio",NAME="aac_eng_2_128041_2_1",LANGUAGE="eng",DEFAULT=YES,AUTOSELECT=YES,URI="QualityLevels(128041)/Manifest(aac_eng_2_128041_2_1,format=m3u8-aapl)"
#EXT-X-STREAM-INF:BANDWIDTH=536209,RESOLUTION=320x180,CODECS="avc1.64000d,mp4a.40.2",AUDIO="audio"
QualityLevels(380658)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=536209,RESOLUTION=320x180,CODECS="avc1.64000d",URI="QualityLevels(380658)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=884474,RESOLUTION=480x270,CODECS="avc1.640015,mp4a.40.2",AUDIO="audio"
QualityLevels(721426)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=884474,RESOLUTION=480x270,CODECS="avc1.640015",URI="QualityLevels(721426)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=1327838,RESOLUTION=640x360,CODECS="avc1.64001e,mp4a.40.2",AUDIO="audio"
QualityLevels(1155246)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=1327838,RESOLUTION=640x360,CODECS="avc1.64001e",URI="QualityLevels(1155246)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=2414544,RESOLUTION=960x540,CODECS="avc1.64001f,mp4a.40.2",AUDIO="audio"
QualityLevels(2218559)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=2414544,RESOLUTION=960x540,CODECS="avc1.64001f",URI="QualityLevels(2218559)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=3805301,RESOLUTION=1280x720,CODECS="avc1.640020,mp4a.40.2",AUDIO="audio"
QualityLevels(3579378)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=3805301,RESOLUTION=1280x720,CODECS="avc1.640020",URI="QualityLevels(3579378)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=139017,CODECS="mp4a.40.2",AUDIO="audio"
QualityLevels(128041)/Manifest(aac_eng_2_128041_2_1,format=m3u8-aapl)
```

## <a name="next-steps"></a>Kolejne kroki

[Przekazywanie, kodowanie, filmy wideo usługi stream](stream-files-tutorial-with-api.md)

