---
title: Dynamiczne pakowanie w usłudze Azure Media Services w wersji 3
titleSuffix: Azure Media Services
description: Ten artykuł zawiera omówienie dynamicznego pakowania w usłudze Azure Media Services.
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
ms.date: 03/17/2020
ms.author: juliako
ms.openlocfilehash: ae049d7486007696d8038eb4e6593cf996df659e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80372610"
---
# <a name="dynamic-packaging-in-media-services-v3"></a>Dynamiczne pakowanie w programie Media Services w wersji 3

Usługi Microsoft Azure Media Services mogą być używane do kodowania wielu formatów plików źródłowych multimediów. Dostarcza je za pośrednictwem różnych protokołów przesyłania strumieniowego, z lub bez ochrony zawartości, aby dotrzeć do wszystkich głównych urządzeń (takich jak urządzenia z systemem iOS i Android). Ci klienci rozumieją różne protokoły. Na przykład system iOS wymaga strumieni, które mają być dostarczane w formacie HTTP Live Streaming (HLS), a urządzenia z systemem Android obsługują hls, a także MPEG DASH.

W usłudze Media Services [punkt końcowy przesyłania strumieniowego](streaming-endpoint-concept.md) reprezentuje dynamiczną (just-in-time) usługę pakowania i pochodzenia, która może dostarczać zawartość na żywo i na żądanie bezpośrednio do aplikacji odtwarzacza klienckiego. Używa jednego ze wspólnych protokołów multimediów strumieniowych wymienionych w poniższej sekcji. Dynamiczne tworzenie pakietów to funkcja, w którą są standardowo wyposażone wszystkie punkty końcowe przesyłania strumieniowego (w warstwie Standard lub Premium).

> [!NOTE]
> Za pomocą [witryny Azure Portal](https://portal.azure.com/) można zarządzać [zdarzeniami na żywo](live-events-outputs-concept.md)w wersji 3 , [wyświetlać zasoby w](assets-concept.md)wersji 3 , uzyskać informacje o uzyskiwaniu dostępu do interfejsów API. W przypadku wszystkich innych zadań zarządzania (na przykład Przekształceń i zadań) użyj [interfejsu API REST,](https://docs.microsoft.com/rest/api/media/) [interfejsu wiersza polecenia](https://aka.ms/ams-v3-cli-ref)lub jednego z [obsługiwanych pakietów SDK.](media-services-apis-overview.md#sdks)

## <a name="to-prepare-your-source-files-for-delivery"></a><a id="delivery-protocols"/>Aby przygotować pliki źródłowe do dostarczenia

Aby korzystać z funkcji Dynamic Packaging, należy [zakodować](encoding-concept.md) plik antresoli (źródłowej) w zestawie plików MP4 o wielu szybkościach transmisji bitów (ISO Base Media 14496-12). Musisz mieć [zasób](assets-concept.md) z zakodowanym plikiem MP4 i plikami konfiguracyjnymi przesyłania strumieniowego wymaganymi przez usługę Media Services Dynamic Packaging. Z tego zestawu plików MP4 można używać dynamicznego pakowania do dostarczania wideo za pośrednictwem protokołów multimediów strumieniowych opisanych poniżej.

> [!TIP]
> Jednym ze sposobów uzyskania mp4 i przesyłania strumieniowego plików konfiguracyjnych jest [zakodowanie pliku antresoli za pomocą programu Media Services.](#encode-to-adaptive-bitrate-mp4s) 

Aby udostępnić klientom filmy w zakodowanym zasobie do odtwarzania, należy utworzyć [lokalizator przesyłania strumieniowego](streaming-locators-concept.md) i utworzyć adresy URL przesyłania strumieniowego. Następnie na podstawie określonego formatu w manifeście klienta przesyłania strumieniowego (HLS, MPEG DASH lub Smooth Streaming) otrzymasz strumień w wybranym protokole.

Dzięki temu wystarczy przechowywać i opłacać pliki w jednym formacie magazynu, a usługa Media Services utworzy oraz udostępni właściwą odpowiedź na podstawie żądań klienta.

Jeśli zawartość ma być chroniona przy użyciu szyfrowania dynamicznego usługi Media Services, zobacz [Protokoły przesyłania strumieniowego i typy szyfrowania](content-protection-overview.md#streaming-protocols-and-encryption-types).

### <a name="hls-protocol"></a>Protokół HLS

Klient przesyłania strumieniowego może określić następujące formaty HLS:

|Protocol (Protokół)|Przykład|
|---|---|
|HLS V4 |`https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=m3u8-aapl)`||
|HLS V3 |`https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=m3u8-aapl-v3)`||
|HLS CMAF| `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=m3u8-cmaf)`||

### <a name="mpeg-dash-protocol"></a>Protokół MPEG-DASH

Klient przesyłania strumieniowego może określić następujące formaty MPEG-DASH:

|Protocol (Protokół)|Przykład|
|---|---|
|MPEG-DASH CSF| `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=mpd-time-csf)` ||
|MPEG-DASH CMAF|`https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=mpd-time-cmaf)` ||

### <a name="smooth-streaming-protocol"></a>Protokół Płynna transmisja strumieniowa

Klient przesyłania strumieniowego może określić następujące formaty płynnego przesyłania strumieniowego:

|Protocol (Protokół)|Uwagi/przykłady| 
|---|---|
|Smooth Streaming| `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest`||
|Smooth Streaming 2.0 (manifest starszej wersji)|Domyślnie format manifestu Płynne przesyłanie strumieniowe zawiera znacznik powtarzalny (r-tag). Jednak niektórzy gracze nie `r-tag`obsługują . Klienci z tymi odtwarzaczami mogą używać formatu, który wyłącza r-tag:<br/><br/>`https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=fmp4-v20)`|

> [!NOTE]
> Płynne przesyłanie strumieniowe wymaga obecności zarówno dźwięku, jak i wideo w strumieniu.

## <a name="on-demand-streaming-workflow"></a>Przepływ pracy przesyłania strumieniowego na żądanie

Poniższe kroki pokazują wspólny przepływ pracy przesyłania strumieniowego usługi Media Services, w którym jest używane dynamiczne pakowanie wraz z koderem standardowym w usłudze Azure Media Services.

1. Przekaż plik wejściowy, taki jak plik QuickTime/MOV lub MXF. Ten plik jest również określany jako antresola lub plik źródłowy. Aby uzyskać listę obsługiwanych formatów, zobacz [Formaty obsługiwane przez koder standardowy](media-encoder-standard-formats.md).
1. [Zakoduj](#encode-to-adaptive-bitrate-mp4s) plik mezzanine do adaptacyjnego zestawu bitrate H.264/AAC MP4.
1. Opublikuj zasób wyjściowy zawierający adaptacyjny zestaw MP4 z szybkością transmisji bitów. Publikujesz, tworząc lokalizator przesyłania strumieniowego.
1. Twórz adresy URL, które są przeznaczone dla różnych formatów (HLS, MPEG-DASH i Smooth Streaming). **Punkt końcowy przesyłania strumieniowego** zajmie się obsługą poprawnego manifestu i żądań dla wszystkich tych różnych formatów.

Na poniższym diagramie przedstawiono przesyłanie strumieniowe na żądanie z przepływem pracy dynamiczne pakowanie.

![Diagram przepływu pracy do przesyłania strumieniowego na żądanie z dynamicznym pakowaniem](./media/dynamic-packaging-overview/media-services-dynamic-packaging.svg)

### <a name="encode-to-adaptive-bitrate-mp4s"></a>Kodowanie do adaptacyjnych bitrate MP4

W poniższych artykułach przedstawiono przykłady [kodowania wideo za pomocą usługi Media Services:](encoding-concept.md)

* [Kodowanie z adresu URL HTTPS przy użyciu wbudowanych ustawień predefiniowanych](job-input-from-http-how-to.md).
* [Kodowanie pliku lokalnego przy użyciu wbudowanych ustawień predefiniowanych](job-input-from-local-file-how-to.md).
* [Skompiluj niestandardowe ustawienia wstępne, aby kierować reklamy na określony scenariusz lub wymagania urządzenia](customize-encoder-presets-how-to.md).

Zobacz listę [formatów koderów standardowych i kodeków](media-encoder-standard-formats.md).

## <a name="live-streaming-workflow"></a>Przepływ pracy przesyłania strumieniowego na żywo

Zdarzenie na żywo można ustawić na *przekazywanie* (lokalny koder na żywo wysyła strumień wielu bitrate) lub *kodowanie* na żywo (lokalny koder na żywo wysyła pojedynczy strumień szybkości transmisji bitów). 

Oto typowy przepływ pracy do przesyłania strumieniowego na żywo z dynamicznym pakowaniem:

1. Utwórz [wydarzenie na żywo](live-events-outputs-concept.md).
1. Pobierz adres URL pozyskiwania i skonfiguruj koder lokalny, aby używał adresu URL do wysyłania pliku danych o programie.
1. Pobierz adres URL wersji zapoznawczej i użyj go, aby sprawdzić, czy dane wejściowe z kodera są odbierane.
1. Utwórz nowy zasób.
1. Utwórz dane wyjściowe na żywo i użyj utworzonej nazwy zasobu.<br />Dane wyjściowe na żywo archiwizuje strumień do zasobu.
1. Utwórz lokalizator przesyłania strumieniowego z wbudowanymi typami zasad przesyłania strumieniowego.<br />Jeśli zamierzasz zaszyfrować zawartość, przejrzyj [omówienie ochrony zawartości](content-protection-overview.md).
1. Wyświetl listę ścieżek w lokalizatorze przesyłania strumieniowego, aby uzyskać adresy URL do użycia.
1. Pobierz nazwę hosta dla punktu końcowego przesyłania strumieniowego, z którego chcesz przesyłać strumieniowo.
1. Twórz adresy URL, które są przeznaczone dla różnych formatów (HLS, MPEG-DASH i Smooth Streaming). Punkt końcowy przesyłania strumieniowego zajmuje się obsługiwanie poprawne manifestu i żądań dla różnych formatów.

Ten diagram przedstawia przepływ pracy przesyłania strumieniowego na żywo za pomocą dynamicznego pakowania:

![Diagram przepływu pracy do kodowania przekazywanego za pomocą dynamicznego pakowania](./media/live-streaming/pass-through.svg)

Aby uzyskać informacje na temat przesyłania strumieniowego na żywo w umiań Media Services w wersji 3, zobacz [Omówienie przesyłania strumieniowego na żywo](live-streaming-overview.md).

## <a name="video-codecs-supported-by-dynamic-packaging"></a>Kodeki wideo obsługiwane przez dynamic packaging

Dynamic Packaging obsługuje pliki MP4, które zawierają wideo, które jest zakodowane z [H.264](https://en.m.wikipedia.org/wiki/H.264/MPEG-4_AVC) (MPEG-4 AVC lub AVC1) lub [H.265](https://en.m.wikipedia.org/wiki/High_Efficiency_Video_Coding) (HEVC, hev1 lub hvc1).

> [!NOTE]
> W przypadku opakowania dynamicznego przetestowano rozdzielczość do 4K i liczbę klatek do 60 klatek na sekundę. [Koder Premium](https://docs.microsoft.com/azure/media-services/previous/media-services-encode-asset#media-encoder-premium-workflow) obsługuje kodowanie do H.265 za pośrednictwem starszych interfejsów API w wersji 2.

## <a name="audio-codecs-supported-by-dynamic-packaging"></a><a id="audio-codecs"/>Kodeki audio obsługiwane przez dynamic packaging

Dynamiczne pakowanie obsługuje dźwięk zakodowany za pomocą następujących protokołów:

* [AAC](https://en.wikipedia.org/wiki/Advanced_Audio_Coding) (AAC-LC, HE-AAC v1 lub HE-AAC v2)
* [Dolby Digital Plus](https://en.wikipedia.org/wiki/Dolby_Digital_Plus) (Enhanced AC-3 lub E-AC3)
* Dolby Atmos<br />
   Przesyłanie strumieniowe zawartości Dolby Atmos jest obsługiwane dla standardów, takich jak protokół MPEG-DASH z interfejsem MP4 common streaming format (CSF) lub common media application format (CMAF) oraz za pośrednictwem protokołu HTTP Live Streaming (HLS) z CMAF.

* [Dts](https://en.wikipedia.org/wiki/DTS_%28sound_system%29)<br />
   Kodeki DTS obsługiwane przez formaty opakowań DASH-CSF, DASH-CMAF, HLS-M2TS i HLS-CMAF to:  

    * Cyfrowy dźwięk przestrzenny DTS (dtsc)
    * DTS-HD Wysoka rozdzielczość i DTS-HD Master Audio (dtsh)
    * DTS Express (dtse)
    * DTS-HD Bezstratny (bez rdzenia) (dtsl)

Funkcja Dynamic Packaging obsługuje wiele ścieżek audio z programem DASH lub HLS (wersja 4 lub nowsza) do przesyłania strumieniowego zasobów, które mają wiele ścieżek audio z wieloma kodekami i językami.

### <a name="additional-notes"></a>Uwagi dodatkowe

Funkcja Dynamic Packaging nie obsługuje plików zawierających dźwięk [Dolby Digital](https://en.wikipedia.org/wiki/Dolby_Digital) (AC3) (jest to starszy kodek).

> [!NOTE]
> [Koder Premium](https://docs.microsoft.com/azure/media-services/previous/media-services-encode-asset#media-encoder-premium-workflow) obsługuje kodowanie do dolby digital plus za pośrednictwem starszych interfejsów API w wersji 2.

## <a name="manifests"></a>Manifesty

W programie Media Services Dynamic Packaging manifesty klienta przesyłania strumieniowego dla HLS, MPEG-DASH i Smooth Streaming są generowane dynamicznie na podstawie selektora formatu w adresie URL.  

Plik manifestu zawiera metadane przesyłania strumieniowego, takie jak typ ścieżki (audio, wideo lub tekst), nazwa ścieżki, czas rozpoczęcia i zakończenia, szybkość transmisji bitów (cechy), języki ścieżek, okno prezentacji (okno przesuwne o stałym czasie trwania) i kodek wideo (FourCC). Nakazuje również odtwarzaczowi pobranie następnego fragmentu, dostarczając informacji o następnych dostępnych fragmentach wideo i ich lokalizacji. Fragmenty (lub segmenty) są rzeczywistymi "fragmentami" treści wideo.

### <a name="examples"></a>Przykłady

#### <a name="hls"></a>HLS

Oto przykład pliku manifestu HLS, zwanego również główną listą odtwarzania HLS: 

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

#### <a name="mpeg-dash"></a>MPEG-DASH

Oto przykład pliku manifestu MPEG-DASH, zwanego również opisem prezentacji multimediów MPEG-DASH(MPD):

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
#### <a name="smooth-streaming"></a>Smooth Streaming

Oto przykład pliku manifestu Smooth Streaming:

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

### <a name="naming-of-tracks-in-the-manifest"></a>Nazywanie utworów w manifeście

Jeśli nazwa ścieżki audio jest określona w pliku .ism, program Media Services dodaje `Label` element w `AdaptationSet` ramach, aby określić informacje tekstowe dla określonej ścieżki dźwiękowej. Przykład wyjściowego manifestu DASH:

```xml
<AdaptationSet codecs="mp4a.40.2" contentType="audio" lang="en" mimeType="audio/mp4" subsegmentAlignment="true" subsegmentStartsWithSAP="1">
  <Label>audio_track_name</Label>
  <Role schemeIdUri="urn:mpeg:dash:role:2011" value="main"/>
  <Representation audioSamplingRate="48000" bandwidth="131152" id="German_Forest_Short_Poem_english-en-68s-2-lc-128000bps_seg">
    <BaseURL>German_Forest_Short_Poem_english-en-68s-2-lc-128000bps_seg.mp4</BaseURL>
  </Representation>
</AdaptationSet>
```

Odtwarzacz może użyć `Label` elementu do wyświetlenia w interfejsie użytkownika.

### <a name="signaling-audio-description-tracks"></a>Sygnalizacja ścieżek opisu audio

Możesz dodać ścieżkę narracji do filmu, aby pomóc klientom niedowidzącym śledzić nagranie wideo, słuchając narracji. Należy opisać ścieżkę audio jako opis audio w manifeście. Aby to zrobić, dodaj parametry "dostępność" i "rola" do pliku .ism. Twoim obowiązkiem jest prawidłowe ustawienie tych parametrów, aby zasygnalizować ścieżkę audio jako opis audio. Na przykład `<param name="accessibility" value="description" />` dodaj `<param name="role" value="alternate"` i do pliku .ism dla określonej ścieżki audio. 

Aby uzyskać więcej informacji, zobacz [jak zasygnalizować przykład opisowej ścieżki dźwiękowej.](signal-descriptive-audio-howto.md)

#### <a name="smooth-streaming-manifest"></a>Manifest płynnego przesyłania strumieniowego

Jeśli odtwarzasz strumień płynnego przesyłania strumieniowego, `Accessibility` manifest `Role` będzie nosił wartości i atrybuty dla tej ścieżki dźwiękowej. Na przykład `Role="alternate" Accessibility="description"` zostanie dodany `StreamIndex` w elemencie, aby wskazać, że jest to opis audio.

#### <a name="dash-manifest"></a>Manifest DASH

W przypadku manifestu DASH dodane zostaną następujące dwa elementy, aby zasygnalizować opis audio:

```xml
<Accessibility schemeIdUri="urn:mpeg:dash:role:2011" value="description"/>
<Role schemeIdUri="urn:mpeg:dash:role:2011" value="alternate"/>
```

#### <a name="hls-playlist"></a>Lista odtwarzania HLS

W przypadku HLS `(format=m3u8-cmaf)`v7 i `AUTOSELECT=YES,CHARACTERISTICS="public.accessibility.describes-video"` powyżej jego lista odtwarzania będzie nosić, gdy ścieżka opisu audio jest sygnalizowana.

#### <a name="example"></a>Przykład

Aby uzyskać więcej informacji, zobacz [Jak zasygnalizować ścieżki opisu audio](signal-descriptive-audio-howto.md).

## <a name="dynamic-manifest"></a>Manifest dynamiczny

Aby kontrolować liczbę ścieżek, formatów, szybkości transmisji bitów i okien czasu prezentacji wysyłanych do odtwarzaczy, można użyć filtrowania dynamicznego za pomocą pakietu pakietowego dynamicznego usługi Media Services. Aby uzyskać więcej informacji, zobacz [Wstępne filtrowanie manifestów za pomocą dynamicznego pakowacza](filters-dynamic-manifest-overview.md).

## <a name="dynamic-encryption"></a>Szyfrowanie dynamiczne

*Szyfrowanie dynamiczne* umożliwia dynamiczne szyfrowanie zawartości na żywo lub na żądanie za pomocą systemu AES-128 lub dowolnego z trzech głównych systemów zarządzania prawami cyfrowymi (DRM): Microsoft PlayReady, Google Widevine i Apple FairPlay. Usługa Media Services zapewnia również usługę dostarczania kluczy AES i licencji DRM autoryzowanym klientom. Aby uzyskać więcej informacji, zobacz [szyfrowanie dynamiczne](content-protection-overview.md).

> [!NOTE]
> Widevine jest usługą świadczoną przez Google Inc. i podlega warunkom korzystania z usługi oraz Polityce prywatności Firmy Google, Inc.

## <a name="more-information"></a>Więcej informacji

Zapoznaj się ze [społecznością usługi Azure Media Services,](media-services-community.md) aby zobaczyć różne sposoby zadawania pytań, przekazywania opinii i otrzymywać aktualizacje dotyczące usługi Media Services.

## <a name="need-help"></a>Potrzebujesz pomocy?

Możesz otworzyć bilet pomocy technicznej, przechodząc do [nowego żądania pomocy technicznej](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Następne kroki

[Przekazywanie, kodowanie i przesyłanie strumieniowe klipów wideo](stream-files-tutorial-with-api.md)
