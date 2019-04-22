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
ms.date: 03/25/2019
ms.author: juliako
ms.openlocfilehash: c9254c8dd629230a549dd95aba9afbd932746007
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "58886458"
---
# <a name="dynamic-packaging"></a>Dynamiczne tworzenie pakietów

Usługa content protection formatów różne technologie klienta (na przykład iOS i konsoli XBOX) i Microsoft Azure Media Services może służyć do dostarczania wiele nośnika źródłowego formatów plików, przesyłanie strumieniowe formatów multimediów. Ci klienci zrozumienie różnych protokołów, na przykład dla systemu iOS wymaga formatu HTTP Live Streaming (HLS) i konsolach Xbox wymagają Smooth Streaming. Jeśli masz zestaw z adaptacyjną szybkością transmisji bitów (bitów) w formacie MP4 (ISO Base nośników 14496-12) plików lub zestaw Smooth Streaming pliki adaptacyjną szybkością transmisji bitów, które mają służyć do klientów, którzy zrozumieć HLS, MPEG DASH lub Smooth Streaming, możesz korzystać z zalet dynamiczny Pakowanie. Obsługiwane są SD/HD/UHD - 4K, opakowywanie jest niezależny od rozdzielczości wideo.

[Punkty końcowe przesyłania strumieniowego](streaming-endpoint-concept.md) usługa funkcję dynamicznego tworzenia pakietów w usłudze Media Services umożliwia dostarczanie zawartości multimedialnej dla graczy klienta. Funkcję dynamicznego tworzenia pakietów jest funkcją, która jest dostępna na wszystkich standardowych **punkty końcowe przesyłania strumieniowego** (standardowa / Premium). 

Aby móc korzystać z **funkcję dynamicznego tworzenia pakietów**, musisz mieć **zasobów** z zestawu plików MP4 i przesyłania strumieniowego pliki konfiguracyjne wymagane przez funkcję dynamicznego tworzenia pakietów usługi Media. Jednym ze sposobów uzyskiwania plików jest kodowanie pliku mezzanine (źródłowego) za pomocą usługi Media Services. Aby udostępnić pliki wideo w zakodowanym elementem zawartości do klientów do odtwarzania, należy utworzyć **lokalizatora przesyłania strumieniowego** i tworzenie adresów URL przesyłania strumieniowego. Następnie w oparciu o formatu określonego w manifeście klienta przesyłania strumieniowego (HLS, DASH lub Smooth), strumień jest dostarczany za pomocą wybranego protokołu.

Dzięki temu wystarczy przechowywać i opłacać pliki w jednym formacie magazynu, a usługa Media Services utworzy oraz udostępni właściwą odpowiedź na podstawie żądań klienta. 

W usłudze Media Services funkcję dynamicznego tworzenia pakietów jest używany, czy są przesyłania strumieniowego na żywo lub na żądanie. 

## <a name="common-on-demand-workflow"></a>Typowy przepływ pracy na żądanie

Poniżej przedstawiono typowe Media Services, przepływ pracy transmisji strumieniowej użycia funkcji dynamicznego tworzenia pakietów.

1. Przekaż plik wejściowy (nazywane plik mezzanine). Na przykład w formacie MP4, MOV lub MXF (Aby uzyskać listę obsługiwanych formatów, zobacz [formaty obsługiwane przez Media Encoder Standard](media-encoder-standard-formats.md).
2. Kodowanie pliku mezzanine do H.264 MP4 o adaptacyjnej szybkości transmisji bitów zestawów.
3. Opublikuj element zawartości zawierający adaptacyjną szybkością transmisji bitów, zestawu plików MP4. Możesz opublikować, tworząc **lokalizatora przesyłania strumieniowego**.
4. Tworzenie adresów URL, których platformą docelową w różnych formatach (HLS, Dash i Smooth Streaming). **Punkt końcowy przesyłania strumieniowego** będzie zajmie się obsługująca prawidłowy manifest oraz żądań dotyczących tych różnych formatach.

Na poniższym diagramie przedstawiono przesyłania strumieniowego na żądanie za pomocą funkcji dynamicznego tworzenia pakietów przepływu pracy.

![Dynamiczne tworzenie pakietów](./media/dynamic-packaging-overview/media-services-dynamic-packaging.svg)

### <a name="encode-to-adaptive-bitrate-mp4s"></a>Wykonaj kodowanie do każdego pliku MP4 z adaptacyjną szybkością transmisji bitów

Aby uzyskać informacje o [jak kodować wideo za pomocą usługi Media Services](encoding-concept.md), zobacz następujące przykłady:

* [Kodowanie z adresu URL HTTPS, przy użyciu wbudowanych ustawień wstępnych](job-input-from-http-how-to.md)
* [Kodowanie pliku lokalnego za pomocą wbudowanych ustawień wstępnych](job-input-from-local-file-how-to.md)
* [Tworzenie niestandardowego ustawienia wstępnego pod kątem określonych wymagań scenariusza lub urządzenia](customize-encoder-presets-how-to.md)

Wykaz usługi Media Encoder Standard formaty i kodeki narzędzia, zobacz [formaty i kodery-dekodery](media-encoder-standard-formats.md)

## <a name="common-live-streaming-workflow"></a>Typowy przepływ pracy transmisji strumieniowej na żywo

Poniżej przedstawiono kroki, aby uzyskać przepływ pracy transmisji strumieniowej na żywo:

1. Tworzenie [wydarzenie na żywo](live-events-outputs-concept.md).
1. Uzyskaj adresy URL pozyskiwania i skonfiguruj swoje lokalny koder wysyłać wkład źródła danych przy użyciu adresu URL.
1. Adres URL (wersja zapoznawcza) i weryfikować, czy rzeczywiście są odbierane dane wejściowe z kodera.
1. Utwórz nową **zasobów**.
1. Tworzenie **na żywo dane wyjściowe** i użyj nazwy zasobu, który został utworzony.<br/>**Na żywo dane wyjściowe** spowoduje zarchiwizowanie strumienia do **zasobów**.
1. Tworzenie **lokalizatora przesyłania strumieniowego** dzięki wbudowanej **przesyłania strumieniowego zasad** typów.<br/>Jeśli zamierzasz szyfrowanie zawartości, zapoznaj się z [Omówienie ochrony zawartości](content-protection-overview.md).
1. Wyświetlanie listy ścieżek na **lokalizatora przesyłania strumieniowego** odzyskać adresy URL do użycia.
1. Pobieranie nazwy hosta dla **punkt końcowy przesyłania strumieniowego** chcesz przesyłać strumieniowo z.
1. Tworzenie adresów URL, których platformą docelową w różnych formatach (HLS, Dash i Smooth Streaming). **Punkt końcowy przesyłania strumieniowego** będzie zajmie się obsługująca prawidłowy manifest oraz żądań dotyczących tych różnych formatach.

Wydarzenie na żywo może być jednym z dwóch typów: kodowanie przekazywania i na żywo. Aby uzyskać szczegółowe informacje o transmisji strumieniowej na żywo w wersji 3 usługa Media Services, zobacz [na żywo, przesyłanie strumieniowe Przegląd](live-streaming-overview.md).

Na poniższym diagramie przedstawiono, przesyłanie strumieniowe na żywo za pomocą funkcji dynamicznego tworzenia pakietów przepływu pracy.

![przekazywane](./media/live-streaming/pass-through.svg)

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

Dynamiczne tworzenie pakietów obsługuje pliki w formacie MP4, które zawierają audio zakodowane za pomocą [AAC](https://en.wikipedia.org/wiki/Advanced_Audio_Coding) (AAC-LC, HE-AAC v1, v2 HE AAC), [Dolby Digital Plus](https://en.wikipedia.org/wiki/Dolby_Digital_Plus)(Enhanced AC-3 lub E AC3), Dolby Atmos lub [DTS](https://en.wikipedia.org/wiki/DTS_%28sound_system%29) (Używanie usług DTS Express, DTS LBR, DTS HD, HD DTS bezstratne). Przesyłanie strumieniowe zawartości Dolby Atmos jest obsługiwane pod kątem obsługi standardów, takich jak MPEG-DASH protokół wspólne przesyłanie strumieniowe formatu (CSF) lub wspólnej Media aplikacji formatu (CMAF) pofragmentowany plik MP4, a następnie za pośrednictwem protokołu HTTP Live Streaming (HLS) przy użyciu CMAF.

> [!NOTE]
> Dynamiczne tworzenie pakietów nie obsługuje plików, które zawierają [Dolby Digital](https://en.wikipedia.org/wiki/Dolby_Digital) audio (AC3) (jest to starszy koder-dekoder).

## <a name="dynamic-encryption"></a>Szyfrowanie dynamiczne

**Szyfrowanie dynamiczne** umożliwia dynamiczne szyfrowanie zawartości na żywo lub na żądanie przy użyciu algorytmu AES-128, ani żadnego z trzech głównych prawami cyfrowymi systemów zarządzania (prawami cyfrowymi DRM): PlayReady firmy Microsoft, Google Widevine i FairPlay firmy Apple. Media Services udostępnia również usługę dostarczania kluczy AES i technologii DRM (PlayReady, Widevine i FairPlay) licencji do autoryzowanych klientów. Aby uzyskać więcej informacji, zobacz [szyfrowania dynamicznego](content-protection-overview.md).

## <a name="manifests"></a>Manifesty 
 
Usługa Media Services obsługuje HLS, MPEG DASH, Smooth Streaming protokołów. Jako część **funkcję dynamicznego tworzenia pakietów**, przesyłania strumieniowego manifesty na kliencie (listy odtwarzania wzorca HLS, DASH Media prezentacji opis (MPD) i Smooth Streaming) generowanych dynamicznie w oparciu o wybór formatu w adresie URL. Zobacz protokoły dostarczania w [w tej sekcji](#delivery-protocols). 

Plik manifestu obejmują przesyłanie strumieniowe metadane, takie jak: śledzenie typu (audio, wideo lub tekst), Śledź nazwę, godzina rozpoczęcia i zakończenia, szybkości transmisji bitów (właściwości), Śledź języków, okna prezentacji (przesuwającego się okna o określonej długości), kodera-dekodera wideo (FourCC). Informuje również gracza, aby pobrać następny fragment, podając informacje o następnej rozgrywane fragmenty wideo dostępne i ich lokalizacji. Fragmenty (lub segmentów) są rzeczywiste "fragmentów" zawartości wideo.

### <a name="hls-master-playlist"></a>Lista odtwarzania wzorzec HLS

Oto przykład HLS pliku manifestu: 

```
#EXTM3U
#EXT-X-VERSION:4
#EXT-X-MEDIA:TYPE=AUDIO,GROUP-ID="audio",NAME="aac_eng_2_128041_2_1",LANGUAGE="eng",DEFAULT=YES,AUTOSELECT=YES,URI="QualityLevels(128041)/Manifest(aac_eng_2_128041_2_1,format=m3u8-aapl)"
#EXT-X-STREAM-INF:BANDWIDTH=536608,RESOLUTION=320x180,CODECS="avc1.64000d,mp4a.40.2",AUDIO="audio"
QualityLevels(381048)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=536608,RESOLUTION=320x180,CODECS="avc1.64000d",URI="QualityLevels(381048)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=884544,RESOLUTION=480x270,CODECS="avc1.640015,mp4a.40.2",AUDIO="audio"
QualityLevels(721495)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=884544,RESOLUTION=480x270,CODECS="avc1.640015",URI="QualityLevels(721495)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=1327398,RESOLUTION=640x360,CODECS="avc1.64001e,mp4a.40.2",AUDIO="audio"
QualityLevels(1154816)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=1327398,RESOLUTION=640x360,CODECS="avc1.64001e",URI="QualityLevels(1154816)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=2413312,RESOLUTION=960x540,CODECS="avc1.64001f,mp4a.40.2",AUDIO="audio"
QualityLevels(2217354)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=2413312,RESOLUTION=960x540,CODECS="avc1.64001f",URI="QualityLevels(2217354)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=3805760,RESOLUTION=1280x720,CODECS="avc1.640020,mp4a.40.2",AUDIO="audio"
QualityLevels(3579827)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=3805760,RESOLUTION=1280x720,CODECS="avc1.640020",URI="QualityLevels(3579827)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=139017,CODECS="mp4a.40.2",AUDIO="audio"
QualityLevels(128041)/Manifest(aac_eng_2_128041_2_1,format=m3u8-aapl)
```

### <a name="dash-media-presentation-description-mpd"></a>Opis prezentacji Media DASH (MPD)

Oto przykład manifestu DASH:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<MPD xmlns="urn:mpeg:dash:schema:mpd:2011" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" profiles="urn:mpeg:dash:profile:isoff-live:2011" type="static" mediaPresentationDuration="PT1M10.315S" minBufferTime="PT7S">
   <Period>
      <AdaptationSet id="1" group="5" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="audio" mimeType="audio/mp4" codecs="mp4a.40.2" lang="en">
         <SegmentTemplate timescale="10000000" media="QualityLevels($Bandwidth$)/Fragments(aac_eng_2_128041_2_1=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(aac_eng_2_128041_2_1=i,format=mpd-time-csf)">
            <SegmentTimeline>
               <S d="60160000" r="10" />
               <S d="41386666" />
            </SegmentTimeline>
         </SegmentTemplate>
         <Representation id="5_A_aac_eng_2_128041_2_1_1" bandwidth="128041" audioSamplingRate="48000" />
      </AdaptationSet>
      <AdaptationSet id="2" group="1" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="video" mimeType="video/mp4" codecs="avc1.640020" maxWidth="1280" maxHeight="720" startWithSAP="1">
         <SegmentTemplate timescale="10000000" media="QualityLevels($Bandwidth$)/Fragments(video=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(video=i,format=mpd-time-csf)">
            <SegmentTimeline>
               <S d="60060000" r="10" />
               <S d="42375666" />
            </SegmentTimeline>
         </SegmentTemplate>
         <Representation id="1_V_video_1" bandwidth="3579827" width="1280" height="720" />
         <Representation id="1_V_video_2" bandwidth="2217354" codecs="avc1.64001F" width="960" height="540" />
         <Representation id="1_V_video_3" bandwidth="1154816" codecs="avc1.64001E" width="640" height="360" />
         <Representation id="1_V_video_4" bandwidth="721495" codecs="avc1.640015" width="480" height="270" />
         <Representation id="1_V_video_5" bandwidth="381048" codecs="avc1.64000D" width="320" height="180" />
      </AdaptationSet>
   </Period>
</MPD>
```
### <a name="smooth-streaming"></a>Smooth Streaming

Oto przykład Smooth Streaming manifestu:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<SmoothStreamingMedia MajorVersion="2" MinorVersion="2" Duration="703146666" TimeScale="10000000">
   <StreamIndex Chunks="12" Type="audio" Url="QualityLevels({bitrate})/Fragments(aac_eng_2_128041_2_1={start time})" QualityLevels="1" Language="eng" Name="aac_eng_2_128041_2_1">
      <QualityLevel AudioTag="255" Index="0" BitsPerSample="16" Bitrate="128041" FourCC="AACL" CodecPrivateData="1190" Channels="2" PacketSize="4" SamplingRate="48000" />
      <c t="0" d="60160000" r="11" />
      <c d="41386666" />
   </StreamIndex>
   <StreamIndex Chunks="12" Type="video" Url="QualityLevels({bitrate})/Fragments(video={start time})" QualityLevels="5">
      <QualityLevel Index="0" Bitrate="3579827" FourCC="H264" MaxWidth="1280" MaxHeight="720" CodecPrivateData="0000000167640020ACD9405005BB011000003E90000EA600F18319600000000168EBECB22C" />
      <QualityLevel Index="1" Bitrate="2217354" FourCC="H264" MaxWidth="960" MaxHeight="540" CodecPrivateData="000000016764001FACD940F0117EF01100000303E90000EA600F1831960000000168EBECB22C" />
      <QualityLevel Index="2" Bitrate="1154816" FourCC="H264" MaxWidth="640" MaxHeight="360" CodecPrivateData="000000016764001EACD940A02FF9701100000303E90000EA600F162D960000000168EBECB22C" />
      <QualityLevel Index="3" Bitrate="721495" FourCC="H264" MaxWidth="480" MaxHeight="270" CodecPrivateData="0000000167640015ACD941E08FEB011000003E90000EA600F162D9600000000168EBECB22C" />
      <QualityLevel Index="4" Bitrate="381048" FourCC="H264" MaxWidth="320" MaxHeight="180" CodecPrivateData="000000016764000DACD941419F9F011000003E90000EA600F14299600000000168EBECB22C" />
      <c t="0" d="60060000" r="11" />
      <c d="42375666" />
   </StreamIndex>
</SmoothStreamingMedia>
```

## <a name="dynamic-manifest"></a>Dynamiczne manifestu

Filtrowanie dynamiczne służy do kontrolowania liczby ścieżek, formatów, szybkości transmisji i prezentacji okna czasowe, które są wysłane do odtwarzaczy. Aby uzyskać więcej informacji, zobacz [filtrów i manifestów dynamicznych](filters-dynamic-manifest-overview.md).

> [!NOTE]
> Obecnie nie można zarządzać zasobami w wersji 3 z witryny Azure Portal. Użyj [interfejsu API REST](https://aka.ms/ams-v3-rest-ref), [interfejsu wiersza polecenia](https://aka.ms/ams-v3-cli-ref) lub jednego z obsługiwanych [zestawów SDK](developers-guide.md).

## <a name="next-steps"></a>Kolejne kroki

[Przekazywanie, kodowanie, filmy wideo usługi stream](stream-files-tutorial-with-api.md)

