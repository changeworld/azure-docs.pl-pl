---
title: Omówienie dynamicznego tworzenia pakietów usługi Azure Media Services | Dokumentacja firmy Microsoft
description: Artykuł zawiera omówienie dynamicznego tworzenia pakietów w usłudze Azure Media Services.
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 06/03/2019
ms.author: juliako
ms.openlocfilehash: 4836ec4bb66bbf8ced921dd1095665d004f8a28b
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/03/2019
ms.locfileid: "67542571"
---
# <a name="dynamic-packaging"></a>Dynamiczne tworzenie pakietów

Usługa content protection formatów różne technologie klienta (na przykład iOS i konsoli XBOX) i Microsoft Azure Media Services może służyć do dostarczania wiele nośnika źródłowego formatów plików, przesyłanie strumieniowe formatów multimediów. Ci klienci zrozumienie różnych protokołów, na przykład dla systemu iOS wymaga formatu HTTP Live Streaming (HLS) i konsolach Xbox wymagają Smooth Streaming. Jeśli masz zestaw z adaptacyjną szybkością transmisji bitów (bitów) w formacie MP4 (ISO Base nośników 14496-12) plików lub zestaw Smooth Streaming pliki adaptacyjną szybkością transmisji bitów, które mają służyć do klientów, którzy zrozumieć HLS, MPEG DASH lub Smooth Streaming, możesz korzystać z zalet  *dynamiczne tworzenie pakietów*. Obsługiwane są SD/HD/UHD - 4K, opakowywanie jest niezależny od rozdzielczości wideo.

W usłudze Media Services [punkt końcowy przesyłania strumieniowego](streaming-endpoint-concept.md) reprezentuje dynamiczny (just-in-time) pakowania i pochodzenia usługę która umożliwia dostarczanie zawartości na żywo i na żądanie bezpośrednio do aplikacji odtwarzacza klienta, przy użyciu jednego z typowych przesyłania strumieniowego Protokoły multimediów (HLS lub DASH). Funkcję dynamicznego tworzenia pakietów jest funkcją, która jest dostępna na wszystkich standardowych **punkty końcowe przesyłania strumieniowego** (standardowa / Premium). 

Aby móc korzystać z dynamicznego tworzenia pakietów, musisz mieć **zasobów** z zestawu plików MP4 i przesyłania strumieniowego pliki konfiguracyjne wymagane przez funkcję dynamicznego tworzenia pakietów usługi Media Services. Jednym ze sposobów uzyskiwania plików jest kodowanie pliku mezzanine (źródłowego) za pomocą usługi Media Services. Aby udostępnić pliki wideo w zakodowanym elementem zawartości do klientów do odtwarzania, należy utworzyć **lokalizatora przesyłania strumieniowego** i tworzenie adresów URL przesyłania strumieniowego. Następnie w oparciu o formatu określonego w manifeście klienta przesyłania strumieniowego (HLS, DASH lub Smooth), strumień jest dostarczany za pomocą wybranego protokołu.

Dzięki temu wystarczy przechowywać i opłacać pliki w jednym formacie magazynu, a usługa Media Services utworzy oraz udostępni właściwą odpowiedź na podstawie żądań klienta. 

W usłudze Media Services funkcję dynamicznego tworzenia pakietów jest używany, czy są przesyłania strumieniowego na żywo lub na żądanie. 

> [!NOTE]
> Obecnie nie można zarządzać zasobami w wersji 3 z witryny Azure Portal. Użyj [interfejsu API REST](https://aka.ms/ams-v3-rest-ref), [interfejsu wiersza polecenia](https://aka.ms/ams-v3-cli-ref) lub jednego z obsługiwanych [zestawów SDK](media-services-apis-overview.md#sdks).

## <a name="on-demand-streaming-workflow"></a>Przepływ pracy transmisji strumieniowej na żądanie

Oto typowy przepływ pracy dla usługi Media Services przesyłania strumieniowego na żądanie za pomocą funkcji dynamicznego tworzenia pakietów:

1. Przekaż plik danych wejściowych lub źródła (o nazwie *mezzanine* pliku). Przykłady obejmują plik MP4, MOV lub MXF. 
1. Kodowanie pliku mezzanine do H.264 MP4 o adaptacyjnej szybkości transmisji bitów zestawów. 
1. Opublikuj element zawartości zawierający adaptacyjną szybkością transmisji bitów, zestawu plików MP4. Możesz opublikować, tworząc Lokalizator przesyłania strumieniowego.
1. Tworzenie adresów URL, których platformą docelową w różnych formatach (HLS, MPEG-DASH i Smooth Streaming). Punkt końcowy przesyłania strumieniowego odpowiada obsługująca prawidłowy manifest i żądań dla różnych formatów.

Ten diagram przedstawia przepływ pracy przesyłania strumieniowego na żądanie za pomocą funkcji dynamicznego tworzenia pakietów:

![Diagram przepływu pracy dla przesyłania strumieniowego na żądanie za pomocą funkcji dynamicznego tworzenia pakietów](./media/dynamic-packaging-overview/media-services-dynamic-packaging.png)

## <a name="live-streaming-workflow"></a>Przepływ pracy transmisji strumieniowej na żywo

Wydarzenie na żywo może być jednym z dwóch typów: kodowanie przekazywania lub na żywo. 

Oto typowy przepływ pracy przesyłania strumieniowego na żywo, funkcję dynamicznego tworzenia pakietów:

1. Tworzenie [zdarzenia na żywo](live-events-outputs-concept.md).
1. Pobierz adres URL pozyskiwania i skonfiguruj swoje lokalny koder wysyłać wkład źródła danych przy użyciu adresu URL.
1. Adres URL (wersja zapoznawcza) i weryfikować, czy odebrano danych wejściowych z kodera.
1. Utwórz nowy zasób.
1. Utwórz na żywo dane wyjściowe i użyj nazwy zasobu, który został utworzony.<br />Strumienia na żywo dane wyjściowe są archiwizowane do niego.
1. Utwórz Lokalizator przesyłania strumieniowego za pomocą wbudowanych typów zasad przesyłania strumieniowego.<br />Jeśli zamierzasz szyfrowanie zawartości, zapoznaj się z [Omówienie ochrony zawartości](content-protection-overview.md).
1. Lista ścieżek na Lokalizator przesyłania strumieniowego można pobrać adresy URL do użycia.
1. Pobierz nazwę hosta punktu końcowego przesyłania strumieniowego, które mają być przesyłane strumieniowo z.
1. Tworzenie adresów URL, których platformą docelową w różnych formatach (HLS, MPEG-DASH i Smooth Streaming). Punkt końcowy przesyłania strumieniowego odpowiada obsługująca prawidłowy manifest i żądań dla różnych formatów.

Ten diagram przedstawia przepływ pracy transmisji strumieniowych na żywo, funkcję dynamicznego tworzenia pakietów:

![Diagram przepływu pracy do przekazywania kodowania za pomocą funkcji dynamicznego tworzenia pakietów](./media/live-streaming/pass-through.svg)

Aby dowiedzieć się, jak transmisja strumieniowa na żywo w wersji 3 usługa Media Services, zobacz [na żywo, przesyłanie strumieniowe Przegląd](live-streaming-overview.md).

## <a name="delivery-protocols"></a>Protokoły dostarczania

Można użyć tych protokołów dostarczania zawartości w funkcji dynamicznego tworzenia pakietów usługi Media Services:

|Protocol|Przykład|
|---|---|
|HLS V4 |`https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=m3u8-aapl)`|
|HLS V3 |`https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=m3u8-aapl-v3)`|
|HLS CMAF| `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=m3u8-cmaf)`|
|MPEG-DASH CSF| `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=mpd-time-csf)` |
|MPEG-DASH CMAF|`https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=mpd-time-cmaf)` |
|Smooth Streaming| `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest`|

## <a name="encode-to-adaptive-bitrate-mp4s"></a>Wykonaj kodowanie do każdego pliku MP4 z adaptacyjną szybkością transmisji bitów

Następujące artykuły pokazują przykłady [jak kodować wideo za pomocą usługi Media Services](encoding-concept.md):

* [Kodowanie z adresu URL HTTPS przy użyciu wbudowanych ustawień wstępnych](job-input-from-http-how-to.md)
* [Kodowanie pliku lokalnego za pomocą wbudowanych ustawień wstępnych](job-input-from-local-file-how-to.md)
* [Tworzenie niestandardowego ustawienia wstępnego pod kątem określonych wymagań scenariusza lub urządzenia](customize-encoder-presets-how-to.md)

Przejrzyj listę rzeczy, Media Encoder Standard [formaty i kodery-dekodery](media-encoder-standard-formats.md).

## <a name="video-codecs"></a>Koderów-dekoderów wideo

Dynamiczne tworzenie pakietów obsługuje następujące koderów-dekoderów wideo:
* Pliki w formacie MP4, które zawierają film wideo, który jest zaszyfrowana przy użyciu [H.264](https://en.m.wikipedia.org/wiki/H.264/MPEG-4_AVC) (AVC MPEG-4 lub AVC1) lub [H.265](https://en.m.wikipedia.org/wiki/High_Efficiency_Video_Coding) (— HEVC, hev1, lub hvc1).

## <a name="audio-codecs"></a>Kodery-dekodery audio

Dynamiczne tworzenie pakietów obsługuje następujące protokoły audio:
* Pliki w formacie MP4
* Wiele ścieżki audio

Dynamiczne tworzenie pakietów nie obsługuje plików, które zawierają [Dolby Digital](https://en.wikipedia.org/wiki/Dolby_Digital) audio (AC3) (jest to starszy koder-dekoder).

### <a name="mp4-files"></a>Pliki w formacie MP4

Dynamiczne tworzenie pakietów obsługuje pliki w formacie MP4, które zawierają dźwięk, który jest zakodowany z następujących protokołów: 

* [AAC](https://en.wikipedia.org/wiki/Advanced_Audio_Coding) (AAC-LC, HE-AAC v1 lub v2 HE AAC)
* [Dolby cyfrowych oraz](https://en.wikipedia.org/wiki/Dolby_Digital_Plus) (rozszerzony AC-3 lub E AC3)
* Dolby Atmos<br />
   Przesyłanie strumieniowe zawartości Dolby Atmos jest obsługiwane pod kątem obsługi standardów, takich jak protokół MPEG-DASH wspólne przesyłanie strumieniowe formatu (CSF) lub wspólnej Media aplikacji formatu (CMAF) pofragmentowany plik MP4, a następnie za pośrednictwem protokołu HTTP Live Streaming (HLS) przy użyciu CMAF.

* [DTS](https://en.wikipedia.org/wiki/DTS_%28sound_system%29)<br />
   Kodery-dekodery typy Wdrożeń obsługiwane przez formaty pakowania kreska-CSF, DASH CMAF, HLS M2TS i HLS CMAF są:  

    * Używanie usług DTS Otocz cyfrowych (dtsc)
    * DTS HD o wysokiej rozdzielczości i wzorzec DTS HD Audio (dtsh)
    * Używanie usług DTS Express (dtse)
    * DTS HD bezstratne (nie-rdzeniowe) (dtsl)

### <a name="multiple-audio-tracks"></a>Wiele ścieżki audio

Dynamiczne tworzenie pakietów obsługuje multi ścieżki audio HLS danych wyjściowych (w wersji 4 lub nowszej) służącą do strumieniowego przesyłania zasoby, które mają wiele ścieżki audio z wieloma koderów-dekoderów i języków.

## <a name="dynamic-encryption"></a>Szyfrowanie dynamiczne

Możesz użyć *szyfrowania dynamicznego* dynamiczne szyfrowanie zawartości na żywo lub na żądanie przy użyciu algorytmu AES-128, ani żadnego z trzech głównych prawami cyfrowymi systemów zarządzania (prawami cyfrowymi DRM): PlayReady firmy Microsoft, Google Widevine i FairPlay firmy Apple. Media Services udostępnia również usługę dostarczania kluczy AES i technologii DRM licencje do autoryzowanych klientów. Aby uzyskać więcej informacji, zobacz [szyfrowania dynamicznego](content-protection-overview.md).

## <a name="manifest-examples"></a>Przykłady manifestu 
 
W dynamicznego tworzenia pakietów usługi Media Services, przesyłania strumieniowego manifesty na kliencie dla protokołu HLS, MPEG-DASH i Smooth Streaming są generowane dynamicznie oparciu o wybór formatu w adresie URL. Aby uzyskać więcej informacji, zobacz [protokołów dostarczania](#delivery-protocols). 

Plik manifestu obejmują przesyłanie strumieniowe metadane, takie jak typu ścieżki (audio, wideo lub tekst), śledzenie, nazwa, rozpoczęcia i czas zakończenia, szybkości transmisji bitów (właściwości), Śledź języków, okna prezentacji (przesuwanego okna stały czas trwania) i kodera-dekodera wideo (FourCC). Informuje również gracza, aby pobrać następny fragment, podając informacje o następnej rozgrywane fragmentów wideo, które są dostępne i ich lokalizacji. Fragmenty (lub segmentów) są rzeczywiste "fragmentów" zawartości wideo.

### <a name="hls"></a>HLS

Oto przykład HLS plik manifestu, jest określana skrótem HLS odtwarzania wzorca: 

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

### <a name="mpeg-dash"></a>MPEG-DASH

Poniżej przedstawiono przykładowy plik manifestu MPEG-DASH, nazywany także MPEG-DASH Media prezentacji opis (MPD):

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

Oto przykład Smooth Streaming pliku manifestu:

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

Aby kontrolować liczbę ścieżek, formatów, szybkości transmisji i prezentacji okna czasowe, które są wysyłane do graczy, można użyć, filtrowanie dynamiczne z funkcji pakowania dynamicznego usługi Media Services. Aby uzyskać więcej informacji, zobacz [wstępnie filtrowanie manifesty za pomocą funkcji dynamicznego pakowania](filters-dynamic-manifest-overview.md).

## <a name="more-information"></a>Więcej informacji

Zapoznaj się z [społeczności usługi Azure Media Services](media-services-community.md) wyświetlić różne sposoby zadawaj pytania, Prześlij opinię i pobrać aktualizacje o usłudze Media Services.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, jak [przekazywanie, kodowanie i przesyłanie strumieniowe filmów wideo](stream-files-tutorial-with-api.md).

