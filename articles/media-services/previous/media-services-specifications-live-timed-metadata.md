---
title: Usługi Azure Media Services — sygnalizowanie metadanych upłynął limit czasu w transmisji strumieniowej na żywo | Dokumentacja firmy Microsoft
description: Ta specyfikacja opisano dwa tryby, które są obsługiwane przez usługę Media Services sygnalizowanie metadanych czasu w ramach transmisji strumieniowej na żywo. Obejmuje to obsługę sygnały ogólnego metadanych Przekroczono limit czasu, a także SCTE 35 sygnalizowanie do wstawienia splice — usługi ad.
services: media-services
documentationcenter: ''
author: johndeu
manager: femila
editor: johndeu
ms.assetid: 265b94b1-0fb8-493a-90ec-a4244f51ce85
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: johndeu;
ms.openlocfilehash: 10dbf7e8cf67ab721cf525d4a1e7594473592bd4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61459117"
---
# <a name="signaling-timed-metadata-in-live-streaming"></a>Sygnalizowanie metadanych czasowych w transmisji strumieniowej na żywo 


## <a name="1-introduction"></a>Wprowadzenie 1 
W celu ułatwienia wstawiania reklam lub zdarzenia niestandardowe na odtwarzaczu klienta, zastosowań studyjnych często upewnij użycie metadanych czasu osadzony w wideo. Aby włączyć te scenariusze, Media Services zapewnia obsługę dla transportu czasu metadanych wraz z nośnika, oraz z punktem pozyskiwania kanału przesyłania strumieniowego na żywo do aplikacji klienckiej.
To specyfikacja przedstawiono dwa tryby, obsługiwanych przez usługi multimediów dla czasu metadane w na żywo transmisji strumieniowej sygnały:

1. Sygnalizowanie [SCTE 35], która heeds zalecane praktyki opisane przez [SCTE 67]

2. Element ogólny upłynął limit czasu sygnalizowanie trybu wiadomości, które nie są metadane [SCTE 35]

### <a name="12-conformance-notation"></a>1.2 Notacja zgodność
Słowa kluczowe "musisz", "Nie", "Wymagane", "SHALL", "Nie jest", "SHOULD", "Nie powinien", "Zalecane", "Może być" i "OPCJONALNY" w tym dokumencie mają być interpretowane zgodnie z opisem w dokumencie RFC 2119

### <a name="13-terms-used"></a>1.3 terminy używane

| Termin              | Definicja                                                                                                                                                                                                                       |
|-------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Czas prezentacji | Czas zdarzenia jest prezentowana w przeglądarce. Godziny reprezentuje moment na osi czasu nośników, że przeglądarka będą w nim wyświetlane zdarzenia. Na przykład podczas prezentacji wiadomości polecenia splice_info() SCTE 35 jest splice_time(). |
| Godzina nadejścia      | Czas, która pojawi się komunikat o zdarzeniu. Czas zwykle różni się od czasu prezentacja wydarzenia, ponieważ komunikaty o zdarzeniach są wysyłane z wyprzedzeniem prezentacji zdarzenia.                                     |
| Śledź rozrzedzone      | nośnik śledzenia, które nie jest ciągły i jest czas jest zsynchronizowany ze ścieżką nadrzędną lub formantu.                                                                                                                                    |
| Origin            | Usługa przesyłania strumieniowego multimediów platformy Azure                                                                                                                                                                                                |
| Obiekt Sink kanału      | Usługa Azure Media usługi transmisji strumieniowej na żywo                                                                                                                                                                                           |
| HLS               | Protokół Apple HTTP Live Streaming.                                                                                                                                                                                               |
| DASH              | Dynamiczne adaptacyjnego przesyłania strumieniowego za pośrednictwem protokołu HTTP                                                                                                                                                                                             |
| Płynne            | Bezproblemowe protokołu przesyłania strumieniowego                                                                                                                                                                                                        |
| MPEG2-TS          | Strumienie 2 transportu MPEG                                                                                                                                                                                                         |
| RTMP              | Protokół multimedialnych w czasie rzeczywistym                                                                                                                                                                                                    |
| uimsbf            | Liczba całkowita bez znaku, najbardziej znaczący bit najpierw.                                                                                                                                                                                    |

-----------------------

## <a name="2-timed-metadata-ingest"></a>2 metadanych Przekroczono limit czasu odbierania
## <a name="21-rtmp-ingest"></a>2.1 pozyskiwania RTMP
RTMP obsługuje sygnały czasu metadanych wysyłane jako komunikaty sygnalizacji AMF osadzane strumienia RTMP. Mogą być wysyłane wiadomości wskaźnika chwilę, zanim rzeczywistego zdarzenia lub splice wstawianiem musi nastąpić. Aby zapewnić obsługę tego scenariusza, rzeczywisty czas splice lub segmentów są wysyłane wiadomości ma. Aby uzyskać więcej informacji zobacz [AMF0].

W poniższej tabeli opisano format ładunku komunikatu AMF, która będzie obsługiwać usługi Media Services.

Nazwa komunikatu AMF może służyć do odróżniania wiele strumieni zdarzeń z tego samego typu.  Komunikaty [SCTE 35] Nazwa komunikatu AMF musi być "onAdCue", zgodnie z zaleceniami w [SCTE 67].  Wszystkie pola, które nie są wymienione poniżej powinny być ignorowane, tak aby innowacji ten projekt nie jest zabronione w przyszłości.

### <a name="signal-syntax"></a>Składnia sygnału
Tryb prosty RTMP Media Services obsługuje pojedynczą wiadomość sygnalizacji AMF o nazwie "onAdCue" w następującym formacie:

### <a name="simple-mode"></a>Tryb prosty

| Nazwa pola | Typ pola | Wymagana? | Opisy                                                                                                             |
|------------|------------|----------|--------------------------------------------------------------------------------------------------------------------------|
| Podpowiedź        | String     | Wymagane | Komunikat zdarzenia.  Musi być "SpliceOut", aby wyznaczyć tryb prosty splice.                                              |
| id         | String     | Wymagane | Unikatowy identyfikator, który opisujący splice lub segmentu. Określa wystąpienie tego komunikatu                            |
| czas trwania   | Liczba     | Wymagane | Czas trwania splice. Jednostki są ułamków sekund.                                                                |
| elapsed    | Liczba     | Optional (Opcjonalność) | Gdy sygnał jest jest powtarzany w celu zapewnienia obsługi Obejrzyj, w tym polu są prezentacji czas, jaki upłynął od chwili rozpoczęcia splice. Jednostki są ułamków sekund. Podczas korzystania z trybu simple ta wartość nie powinna przekraczać splice oryginalnego czasu trwania.                                                  |
| time       | Liczba     | Wymagane | Jest czas splice, w czasie prezentacji. Jednostki są ułamków sekund.                                     |

---------------------------

### <a name="scte-35-mode"></a>Tryb SCTE 35

| Nazwa pola | Typ pola | Wymagana? | Opisy                                                                                                             |
|------------|------------|----------|--------------------------------------------------------------------------------------------------------------------------|
| Podpowiedź        | String     | Wymagane | Komunikat zdarzenia.  Komunikaty [SCTE 35], musi to być base64 pliku binarnego (4648 RFC organizacji IETF) zakodowane splice_info_section() w kolejności wiadomości można wysyłać do klientów HLS i Smooth, Dash zgodne z [SCTE 67].                                              |
| type       | String     | Wymagane | Nazwa URN lub adres URL identyfikujący schemat wiadomości. Komunikaty [SCTE 35] musi to być "urn: scte:scte35:2013a:bin", aby komunikaty wysyłane do klientów HLS i Smooth, Dash zgodne z [SCTE 67].  |
| id         | String     | Wymagane | Unikatowy identyfikator, który opisujący splice lub segmentu. Określa wystąpienie tej wiadomości.  Komunikaty z semantyką równoważne mają taką samą wartość.|
| czas trwania   | Liczba     | Wymagane | Czas trwania zdarzenia lub ad splice — segmentu, jeśli jest znany. Jeśli jest nieznana, wartość powinna być 0.                                                                 |
| elapsed    | Liczba     | Optional (Opcjonalność) | Gdy w celu Obejrzyj powtarza się sygnale reklamowym [SCTE 35] i to pole będzie prezentacji czas, jaki upłynął od chwili rozpoczęcia splice. Jednostki są ułamków sekund. W trybie [SCTE 35] Ta wartość może przekroczyć oryginalnego określonego czasu trwania splice lub segmentu.                                                  |
| time       | Liczba     | Wymagane | Splice — czas prezentacji zdarzenia lub ad.  Czas prezentacji i czas trwania należy wyrównać za pomocą punktów dostępu Stream (SAP) typu 1 lub 2, zgodnie z definicją w [ISO-14496-12] załącznika. Dla ruchu wychodzącego HLS czas i czas trwania powinno odpowiadać granice segmentu. Prezentacja czas i czas trwania komunikaty o różnych zdarzeniach w tej samej strumienia zdarzeń nie może nakładać. Jednostki są ułamków sekund.

---------------------------

#### <a name="211-cancellation-and-updates"></a>2.1.1 unieważnieniu i aktualizacje

Wiadomości mogą być anulowane lub zaktualizowane przez wielu operacji wysyłania wiadomości z tym samym czasie prezentacji i identyfikatora. Czas prezentacji i identyfikator jednoznacznego zidentyfikowania zdarzenia, a ostatni komunikat odebrany przez czas określonych prezentacji, który spełnia ograniczenia wstępne wdrożenie jest komunikat, który jest podjęte. Zaktualizowane zdarzenie zastępuje wszelkie wcześniej Odebrane komunikaty. Ograniczenie wstępne wdrożenie jest czterech sekund. Co najmniej czterech sekund przed chwilą prezentacji Odebrane komunikaty zostaną podjęte.

## <a name="22-fragmented-mp4-ingest-smooth-streaming"></a>2.2 odbierania podzielonej zawartości w formacie MP4 (Smooth Streaming)
Można znaleźć [LIVE FMP4] pozyskiwania wymagania dotyczące transmisji strumieniowej na żywo. Poniższe sekcje zawierają szczegółowe informacje dotyczące pozyskiwania metadanych prezentacji Przekroczono limit czasu.  Przekroczono limit czasu prezentacji metadanych są pozyskiwane jako rozrzedzony śledzenie, która jest zdefiniowana w obu na żywo manifestu polu Serwer (patrz MS SSTR) i pole Movie (moov).  Poszczególne fragmenty rozrzedzone składa się filmu fragmentu pola (moof) oraz nośnika danych ("mdat"), których pole "mdat" jest komunikatu binarnego.

#### <a name="221-live-server-manifest-box"></a>2.2.1 Live Server Manifest Box
Rozrzedzony śledzenie musi być zadeklarowana w polu manifestu serwera na żywo z \<textstream\> wejścia i musi mieć następujące atrybuty zestawu:

| **Nazwa atrybutu** | **Typ pola** | **Wymagane?** | **Opis**                                                                                                                                                                                                                                                 |
|--------------------|----------------|---------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| systemBitrate      | Liczba         | Wymagane      | MUSI być "0", wskazującą śledzenie przy użyciu nieznanego, zmiennej szybkości transmisji bitów.                                                                                                                                                                                                 |
| parentTrackName    | String         | Wymagane      | MUSI być nazwą ścieżki nadrzędnej, kody czasowe rozrzedzone śledzenia są wyrównane do skali czasu. Śledź nadrzędny nie może być rozrzedzone śledzenia.                                                                                                                    |
| manifestOutput     | Boolean        | Wymagane      | MUSI być "prawda", aby wskazać, czy śledzenie rozrzedzone zostanie osadzony w manifest Smooth klienta.                                                                                                                                                               |
| Podtyp            | String         | Wymagane      | MUSI być czterech znaków kodu "Dane".                                                                                                                                                                                                                         |
| Schemat             | String         | Wymagane      | MUSI być adres URL lub URN identyfikowanie schemat wiadomości. Komunikaty [SCTE 35] musi to być "urn: scte:scte35:2013a:bin", aby komunikaty wysyłane do klientów HLS i Smooth, Dash zgodne z [SCTE 67]. |
| trackName          | String         | Wymagane      | MUSI być nazwą ścieżki rozrzedzone. TrackName może służyć do odróżniania wiele strumieni zdarzeń z tego samego schematu. Każdego strumienia zdarzeń unikatowy musi mieć nazwę unikatową ścieżkę.                                                                           |
| Skala czasu          | Liczba         | Optional (Opcjonalność)      | MUSI być skali czasu ścieżki nadrzędnej.                                                                                                                                                                                                                      |

-------------------------------------

### <a name="222-movie-box"></a>2.2.2 pole filmu

Pole Movie (moov) jest zgodna na żywo manifestu polu serwer jako części nagłówka strumienia dla ścieżki rozrzedzone.

Pole "moov" powinien zawierać **TrackHeaderBox (tkhd)** polu zgodnie z definicją w [ISO-14496-12], z następującymi ograniczeniami:

| **Nazwa pola** | **Typ pola**          | **Wymagane?** | **Opis**                                                                                                |
|----------------|-------------------------|---------------|----------------------------------------------------------------------------------------------------------------|
| czas trwania       | 64-bitowej nieoznaczonej liczby całkowitej | Wymagane      | POWINIEN mieć wartość 0, ponieważ pole śledzenia ma zero próbki, a całkowity czas trwania próbek w polu ścieżki wynosi 0. |

-------------------------------------

Pole "moov" powinien zawierać **HandlerBox (hdlr)** zgodnie z definicją w [ISO-14496-12], z następującymi ograniczeniami:

| **Nazwa pola** | **Typ pola**          | **Wymagane?** | **Opis**   |
|----------------|-------------------------|---------------|-------------------|
| handler_type   | 32-bitowej nieoznaczonej liczby całkowitej | Wymagane      | POWINNO być 'meta'. |

-------------------------------------

Pole "stsd" powinien zawierać MetaDataSampleEntry pole o nazwie kodowania, zgodnie z definicją w [ISO-14496-12].  Na przykład wiadomości SCTE 35 kodowania nazwa powinna być "scte".

### <a name="223-movie-fragment-box-and-media-data-box"></a>2.2.3 pola Fragment filmu oraz danych multimediów

Śledzenie rozrzedzone fragmenty składają się z polem Fragment filmu (moof) i pola danych nośnika (mdat).

Pole MovieFragmentBox (moof) musi zawierać **TrackFragmentExtendedHeaderBox (uuid)** polu zgodnie z definicją w [MS-SSTR] przy użyciu następujących pól:

| **Nazwa pola**         | **Typ pola**          | **Wymagane?** | **Opis**                                                                               |
|------------------------|-------------------------|---------------|-----------------------------------------------------------------------------------------------|
| fragment_absolute_time | 64-bitowej nieoznaczonej liczby całkowitej | Wymagane      | MUSI być Godzina nadejścia zdarzenia. Ta wartość jest wyrównywany wiadomości ze ścieżką nadrzędną.   |
| fragment_duration      | 64-bitowej nieoznaczonej liczby całkowitej | Wymagane      | MUSI być czas trwania zdarzenia. Czas trwania może być równa zero, aby wskazać, że czas trwania jest nieznany. |

------------------------------------


Pole MediaDataBox (mdat) musi mieć następujący format:

| **Nazwa pola**          | **Typ pola**                   | **Wymagane?** | **Opis**                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
|-------------------------|----------------------------------|---------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| version                 | 32-bitowa liczba całkowita bez znaku (uimsbf) | Wymagane      | Określa format zawartości pola "mdat". Nierozpoznany wersje będą ignorowane. Obecnie tylko obsługiwana wersja to 1.                                                                                                                                                                                                                                                                                                                                                      |
| id                      | 32-bitowa liczba całkowita bez znaku (uimsbf) | Wymagane      | Określa wystąpienie tej wiadomości. Komunikaty z semantyką równoważne mają taką samą wartość; Przetwarzanie dowolnego pola komunikatu jedno zdarzenie o tym samym identyfikatorze jest wystarczająca.                                                                                                                                                                                                                                                                                                            |
| presentation_time_delta | 32-bitowa liczba całkowita bez znaku (uimsbf) | Wymagane      | Suma fragment_absolute_time, określone w TrackFragmentExtendedHeaderBox i presentation_time_delta musi być czas prezentacji zdarzenia. Czas prezentacji i czas trwania należy wyrównać za pomocą punktów dostępu Stream (SAP) typu 1 lub 2, zgodnie z definicją w [ISO-14496-12] załącznika. Dla ruchu wychodzącego HLS czas i czas trwania powinno odpowiadać granice segmentu. Prezentacja czas i czas trwania komunikaty o różnych zdarzeniach w tej samej strumienia zdarzeń nie może nakładać. |
| message                 | tablica bajtów                       | Wymagane      | Komunikat zdarzenia. Komunikatów [SCTE 35] komunikat jest splice_info_section() binarne, chociaż [SCTE 67] zaleca się coś innego. Komunikaty [SCTE 35] musi to być splice_info_section() w kolejności wiadomości można wysyłać do klientów HLS i Smooth, Dash zgodne z [SCTE 67]. Komunikaty [SCTE 35] binarne splice_info_section() to ładunek pole "mdat", a nie jest zakodowany w formacie base64.                                                            |

------------------------------


### <a name="224-cancellation-and-updates"></a>2.2.4 unieważnieniu i aktualizacje
Wiadomości mogą być anulowane lub zaktualizowane przez wielu operacji wysyłania wiadomości z tym samym czasie prezentacji i identyfikatora.  Czas prezentacji i identyfikator jednoznacznego zidentyfikowania zdarzenia. Ostatni komunikat odebrany czas określonych prezentacji spełniającego ograniczenia wstępne wdrożenie jest komunikat, który jest podjęte. Zaktualizowano komunikat zastępuje wszelkie wcześniej Odebrane komunikaty.  Ograniczenie wstępne wdrożenie jest czterech sekund. Co najmniej czterech sekund przed chwilą prezentacji Odebrane komunikaty zostaną podjęte. 


## <a name="3-timed-metadata-delivery"></a>3 upłynął limit czasu dostarczania metadanych

Dane strumienia zdarzeń jest nieprzezroczysta dla usługi Media Services. Usługi Media Services przekazuje zaledwie trzech rodzajów informacji między punktu końcowego pozyskiwania i punkt końcowy klienta. Następujące właściwości są dostarczane do klienta, zgodnie z [SCTE 67] i/lub klienta przez protokół przesyłania strumieniowego:

1.  Schemat — URN lub adres URL identyfikujący schemat wiadomości.

2.  Czas prezentacji — godzina prezentacji zdarzenia na osi czasu nośników.

3.  Czas trwania — czas trwania zdarzenia.

4.  Identyfikator — opcjonalnie Unikatowy identyfikator zdarzenia.

5.  Komunikat — dane zdarzenia.


## <a name="31-smooth-streaming-delivery"></a>3.1 smooth dostarczania transmisji strumieniowej

Zapoznaj się z rozrzedzone śledzenie obsłudze szczegółów, w [MS-SSTR].

#### <a name="smooth-client-manifest-example"></a>Przykład manifestu Smooth klienta
~~~ xml
<?xml version=”1.0” encoding=”utf-8”?>
<SmoothStreamingMedia MajorVersion=”2” MinorVersion=”0” TimeScale=”10000000” IsLive=”true” Duration=”0”
  LookAheadFragmentCount=”2” DVRWindowLength=”6000000000”>

  <StreamIndex Type=”video” Name=”video” Subtype=”” Chunks=”0” TimeScale=”10000000”
    Url=”QualityLevels({bitrate})/Fragments(video={start time})”>
    <QualityLevel Index=”0” Bitrate=”230000”
      CodecPrivateData=”250000010FC3460B50878A0B5821FF878780490800704704DC0000010E5A67F840” FourCC=”WVC1”
      MaxWidth=”364” MaxHeight=”272”/>
    <QualityLevel Index=”1” Bitrate=”305000”
      CodecPrivateData=”250000010FC3480B50878A0B5821FF87878049080894E4A7640000010E5A67F840” FourCC=”WVC1”
      MaxWidth=”364” MaxHeight=”272”/>
    <c t=”0” d=”20000000” r=”300” />
  </StreamIndex>
  <StreamIndex Type=”audio” Name=”audio” Subtype=”” Chunks=”0” TimeScale=”10000000”
    Url=”QualityLevels({bitrate})/Fragments(audio={start time})”>
    <QualityLevel Index=”0” Bitrate=”96000” CodecPrivateData=”1000030000000000000000000000E00042C0”
      FourCC=”WMAP” AudioTag=”354” Channels=”2” SamplingRate=”44100” BitsPerSample=”16” PacketSize=”4459”/>
    <c t=”0” d=”20000000” r=”300” />
  </StreamIndex>
  <StreamIndex Type=”text” Name=”scte35-event-stream” Subtype=”DATA” Chunks=”0” TimeScale=”10000000”
    ParentStreamIndex=”video” ManifestOutput=”true” 
    Url=”QualityLevels({bitrate})/Fragments(captions={start time})”>
    <QualityLevel Index=”0” Bitrate=”0” CodecPrivateData=”” FourCC=””>
      <CustomAttributes>
        <Attribute Name=”Scheme” Value=”urn:scte:scte35:2013a:bin”/>
      </CustomAttributes>
    </QualityLevel>
    <!-- <f> contains base-64 encoded splice_info_section() message 
    <c t=”600000000” d=”300000000”>
<f>PD94bWwgdmVyc2lvbj0iMS4wIiBlbmNvZGluZz0idXRmLTgiPz48QWNxdWlyZWRTaWduYWwgeG1sbnM9InVybjpjYWJsZWxhYnM6bWQ6eHNkOnNpZ25hbGluZzozLjAiIGFjcXVpc2l0aW9uUG9pbnRJZGVudGl0eT0iRVNQTl9FYXN0X0FjcXVpc2l0aW9uX1BvaW50XzEiIGFjcXVpc2l0aW9uU2lnbmFsSUQ9IjRBNkE5NEVFLTYyRkExMUUxQjFDQTg4MkY0ODI0MDE5QiIgYWNxdWlzaXRpb25UaW1lPSIyMDEyLTA5LTE4VDEwOjE0OjI2WiI+PFVUQ1BvaW50IHV0Y1BvaW50PSIyMDEyLTA5LTE4VDEwOjE0OjM0WiIvPjxTQ1RFMzVQb2ludERlc2NyaXB0b3Igc3BsaWNlQ29tbWFuZFR5cGU9IjUiPjxTcGxpY2VJbnNlcnQgc3BsaWNlRXZlbnRJRD0iMzQ0NTY4NjkxIiBvdXRPZk5ldHdvcmtJbmRpY2F0b3I9InRydWUiIHVuaXF1ZVByb2dyYW1JRD0iNTUzNTUiIGR1cmF0aW9uPSJQVDFNMFMiIGF2YWlsTnVtPSIxIiBhdmFpbHNFeHBlY3RlZD0iMTAiLz48L1NDVEUzNVBvaW50RGVzY3JpcHRvcj48U3RyZWFtVGltZXM+PFN0cmVhbVRpbWUgdGltZVR5cGU9IkhTUyIgdGltZVZhbHVlPSI1MTUwMDAwMDAwMDAiLz48L1N0cmVhbVRpbWVzPjwvQWNxdWlyZWRTaWduYWw+</f>
    </c>
    <c t=”1200000000” d=”400000000”>      <f>PD94bWwgdmVyc2lvbj0iMS4wIiBlbmNvZGluZz0idXRmLTgiPz48QWNxdWlyZWRTaWduYWwgeG1sbnM9InVybjpjYWJsZWxhYnM6bWQ6eHNkOnNpZ25hbGluZzozLjAiIGFjcXVpc2l0aW9uUG9pbnRJZGVudGl0eT0iRVNQTl9FYXN0X0FjcXVpc2l0aW9uX1BvaW50XzEiIGFjcXVpc2l0aW9uU2lnbmFsSUQ9IjRBNkE5NEVFLTYyRkExMUUxQjFDQTg4MkY0ODI0MDE5QiIgYWNxdWlzaXRpb25UaW1lPSIyMDEyLTA5LTE4VDEwOjE0OjI2WiI+PFVUQ1BvaW50IHV0Y1BvaW50PSIyMDEyLTA5LTE4VDEwOjE0OjM0WiIvPjxTQ1RFMzVQb2ludERlc2NyaXB0b3Igc3BsaWNlQ29tbWFuZFR5cGU9IjUiPjxTcGxpY2VJbnNlcnQgc3BsaWNlRXZlbnRJRD0iMzQ0NTY4NjkxIiBvdXRPZk5ldHdvcmtJbmRpY2F0b3I9InRydWUiIHVuaXF1ZVByb2dyYW1JRD0iNTUzNTUiIGR1cmF0aW9uPSJQVDFNMFMiIGF2YWlsTnVtPSIxIiBhdmFpbHNFeHBlY3RlZD0iMTAiLz48L1NDVEUzNVBvaW50RGVzY3JpcHRvcj48U3RyZWFtVGltZXM+PFN0cmVhbVRpbWUgdGltZVR5cGU9IkhTUyIgdGltZVZhbHVlPSI1MTYyMDAwMDAwMDAiLz48L1N0cmVhbVRpbWVzPjwvQWNxdWlyZWRTaWduYWw+</f>
    </c>
  </StreamIndex>
</SmoothStreamingMedia> 
~~~ 

## <a name="32--apple-hls-delivery"></a>3.2, Apple HLS dostarczania
Przekroczono limit czasu metadanych dla firmy Apple HTTP Live Streaming (HLS) może być osadzony w listy odtwarzania segmentów w niestandardowy tag M3U.  Warstwa aplikacji można przeanalizować lista odtwarzania M3U i przetworzyć M3U tagów. Usługa Azure Media Services nastąpi osadzenie czasu metadanych w tagu EXT-X-sygnalizacji zdefiniowane w [HLS].  Tag EXT-X-wskaźnika jest obecnie używany przez DynaMux dla komunikatów typu ADI3.  Do obsługi komunikatów SCTE 35 SCTE 35 i innych, należy ustawić atrybuty znacznika EXT-X-sygnalizacji zgodnie z definicją poniżej:

| **Nazwa atrybutu** | **Typ**                      | **Wymagane?**                             | **Opis**                                                                                                                                                                                                                                                                      |
|--------------------|-------------------------------|-------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| PODPOWIEDŹ                | ciąg w cudzysłowach                 | Wymagane                                  | Komunikat zakodowany jako ciąg w formacie base64, zgodnie z opisem w [IETF RFC 4648](https://tools.ietf.org/html/rfc4648). Komunikaty [SCTE 35] to splice_info_section() zakodowane w formacie base64.                                                                                                |
| TYP               | ciąg w cudzysłowach                 | Wymagane                                  | Nazwa URN lub adres URL identyfikujący schemat wiadomości. Komunikaty [SCTE 35] typu przyjmuje specjalna wartość "scte35".                                                                                                                                |
| ID                 | ciąg w cudzysłowach                 | Wymagane                                  | Unikatowy identyfikator zdarzenia. Jeśli nie określono Identyfikatora, gdy wiadomości są pozyskiwane, usługi Azure Media Services generuje unikatowy identyfikator.                                                                                                                                          |
| CZAS TRWANIA           | dziesiętna liczba zmiennoprzecinkowa | Wymagane                                  | Czas trwania zdarzenia. Jeśli jest nieznana, wartość powinna być 0. Jednostki są factional sekund.                                                                                                                                                                                           |
| UPŁYNĘŁO            | dziesiętna liczba zmiennoprzecinkowa | Opcjonalne, ale wymagane dla oknem kroczącym | Jeśli do obsługi przesuwającego się okna prezentacji powtarza się sygnał i to pole musi być prezentacji czas, jaki upłynął od chwili rozpoczęcia zdarzenia. Jednostki są ułamków sekund. Ta wartość może przekroczyć oryginalnego określonego czasu trwania splice lub segmentu. |
| CZAS               | dziesiętna liczba zmiennoprzecinkowa | Wymagane                                  | Godzina prezentacji zdarzenia. Jednostki są ułamków sekund.                                                                                                                                                                                                                    |


Warstwa aplikacji odtwarzacza HLS zostanie użyty typ do identyfikowania format wiadomości, dekodowania wiadomości, zastosuj konwersje niezbędne czasu i przetworzyć zdarzenia.  Zdarzenia są synchronizowane w listy odtwarzania segmentów ścieżki nadrzędnej czasu zgodnie z sygnatura czasowa zdarzenia.  Żelem krzemionkowy przed dokładnością segmentu (#EXTINF tag).

#### <a name="hls-segment-playlist-example"></a>Przykład listy odtwarzania segmentów HLS
~~~
#EXTM3U
#EXT-X-VERSION:4
#EXT-X-ALLOW-CACHE:NO
#EXT-X-MEDIA-SEQUENCE:346
#EXT-X-TARGETDURATION:6
#EXT-X-I-FRAMES-ONLY
#EXT-X-PROGRAM-DATE-TIME:2018-12-13T15:54:19.462Z
#EXTINF:4.000000,no-desc
KeyFrames(video_track=15447164594627600,format=m3u8-aapl)
#EXTINF:6.000000,no-desc
KeyFrames(video_track=15447164634627600,format=m3u8-aapl)
#EXT-X-CUE:ID="1026",TYPE="scte35",DURATION=30.000000,TIME=1544716520.022760,CUE="/DAlAAAAAAAAAP/wFAUAAAQCf+//KRjAfP4AKTLgAAAAAAAAVYsh2w=="
#EXTINF:6.000000,no-desc
KeyFrames(video_track=15447165474627600,format=m3u8-aapl)
~~~

#### <a name="hls-message-handling"></a>HLS komunikatu obsługi

Zdarzenia są sygnalizowane w listy odtwarzania segmentów każdej ścieżki audio i wideo. Położenie znaczników EXT-X-sygnalizacji zawsze musi być albo bezpośrednio przed wykonaniem HLS pierwszy segment (w przypadku splice się lub uruchom segmentu) czy po ostatniej HLS segmentu (w przypadku splice w lub na końcu segmentu), na które odnoszą się jego atrybuty oraz czas trwania, co jest wymagane przez [ HLS].

Po włączeniu przesuwającego się okna prezentacji tag EXT-X-sygnalizacji powtarza się wystarczająco często, splice lub segmentu jest zawsze w pełni opisane w listy odtwarzania segmentów, należy użyć atrybutu UPŁYNĘŁO, aby wskazać czas splice i segment zawiera byli aktywni, zgodnie z wymogami [HLS].

Po włączeniu przesuwającego się okna prezentacji tagi EXT-X-sygnalizacji są usuwane z listy odtwarzania segmentów, gdy czas multimediów, które odnoszą się do została wycofana, poza przesuwającego się okna prezentacji.

## <a name="33--dash-delivery"></a>3.3 DASH dostarczania
[Kreska] zawiera trzy sposoby sygnału zdarzenia:

1.  Zdarzenia sygnalizowano w MPD
2.  Zdarzenia sygnalizowane wewnątrzpasmowe w reprezentacji (przy użyciu zdarzeń okna komunikatu (emsg)
3.  Kombinacja 1 i 2

Zdarzenia sygnalizowano w MPD są przydatne do przesyłania strumieniowego wideo na żądanie, ponieważ klienci mają dostęp do wszystkich zdarzeń, natychmiast, gdy zostanie pobrana MPD. Rozwiązanie wewnątrzpasmowe jest przydatne w przypadku strumieniowych na żywo, ponieważ nie trzeba ponownie pobrać MPD klientów. Dla opartych na czasie segmentacji klient określa adres URL następny segment przez dodanie czasu trwania i sygnaturę czasową bieżącego segmentu. Żądanie kończy się niepowodzeniem, klient zakłada ciągłości i pliki do pobrania MPD, ale w przeciwnym razie nadal przesyłania strumieniowego bez pobierania MPD.

Usługa Azure Media Services będą nie zarówno sygnalizowanie MPD i wewnątrzpasmowe Sygnalizowanie przy użyciu zdarzeń okno komunikatu.

#### <a name="mpd-signaling"></a>Sygnalizowanie MPD

Zdarzenia są sygnalizowane w MPD używając elementu EventStream elementu, który pojawia się w elemencie okresu.

Element elementu EventStream ma następujące atrybuty:

| **Nazwa atrybutu** | **Typ**                | **Wymagane?** | **Opis**                                                                                                                                                                                                                                                                                   |
|--------------------|-------------------------|---------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| scheme_id_uri      | string                  | Wymagane      | Identyfikuje schematu wiadomości. Schemat jest równa wartości atrybutu schemat w polu na żywo manifestu serwera. Wartość powinna być URN lub adres URL identyfikujący system wiadomości; na przykład, "urn: scte:scte35:2013a:bin".                                                                |
| value              | string                  | Optional (Opcjonalność)      | Wartość dodatkowy ciąg znaków używany przez właścicieli schematu w celu dostosowania semantyki wiadomości. W celu dokonania rozróżnienia wielu strumieni zdarzeń z tego samego schematu, wartość musi być równa nazwę strumienia zdarzeń (trackName dla protokołu Smooth pozyskiwania lub AMF nazwa komunikatu dla protokołu RTMP pozyskiwania). |
| Skala czasu          | 32-bitowej nieoznaczonej liczby całkowitej | Wymagane      | Skala czasu, w dziesięciomilionowych częściach sekundy na sekundę, czas i czas trwania pól w polu "emsg".                                                                                                                                                                                                       |


Zero lub więcej elementów zdarzeń są zawarte w elemencie elementu EventStream i mają następujące atrybuty:

| **Nazwa atrybutu**  | **Typ**                | **Wymagane?** | **Opis**                                                                                                                                                                                                             |
|---------------------|-------------------------|---------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| presentation_time   | 64-bitowej nieoznaczonej liczby całkowitej | Optional (Opcjonalność)      | MUSI być typu media prezentacji godzina zdarzenia względem początku okresu. Czas prezentacji i czas trwania należy wyrównać za pomocą punktów dostępu Stream (SAP) typu 1 lub 2, zgodnie z definicją w [ISO-14496-12] załącznika. |
| czas trwania            | 32-bitowej nieoznaczonej liczby całkowitej | Optional (Opcjonalność)      | Czas trwania zdarzenia. Jeśli czas trwania jest nieznany, to musi zostać pominięty.                                                                                                                                                 |
| id                  | 32-bitowej nieoznaczonej liczby całkowitej | Optional (Opcjonalność)      | Określa wystąpienie tej wiadomości. Komunikaty z semantyką równoważne mają taką samą wartość. Jeśli nie określono Identyfikatora, gdy wiadomości są pozyskiwane, usługi Azure Media Services generuje unikatowy identyfikator.             |
| Wartość elementu zdarzeń | string                  | Wymagane      | Komunikat zdarzenia jako ciąg base64, zgodnie z opisem w [IETF RFC 4648](https://tools.ietf.org/html/rfc4648).                                                                                                                   |

#### <a name="xml-syntax-and-example-for-dash-manifest-mpd-signaling"></a>Składnia XML i przykład DASH manifestu sygnalizacja (MPD)

~~~ xml
<!-- XML Syntax -->
<xs:complexType name=”EventStreamType”>
  <xs:sequence>
    <xs:element name=”Event” type=”EventType” minOccurs=”0” maxOccurs=”unbounded”/>
    <xs:any namespace=”##other” processContents=”lax” minOccurs=”0” maxOccurs=”unbounded”/>
  </xs:sequence>
  <xs:attribute ref=”xlink:href”/>
  <xs:attribute ref=”xlink:actuate” default=”onRequest”/>
  <xs:attribute name=”schemeIdUri” type=”xs:anyURI” use=”required”/>
  <xs:attribute name=”value” type=”xs:string”/>
  <xs:attribute name=”timescale” type=”xs:unsignedInt”/>
</xs:complexType>
<!-- Event -->
<xs:complexType name=”EventType”>
  <xs:sequence>
    <xs:any namespace=”##other” processContents=”lax” minOccurs=”0” maxOccurs=”unbounded”/>
  </xs:sequence>
  <xs:attribute name=”presentationTime” type=”xs:unsignedLong” default=”0”/>
  <xs:attribute name=”duration” type=”xs:unsignedLong”/>
  <xs:attribute name=”id” type=”xs:unsignedInt”/>
  <xs:anyAttribute namespace=”##other” processContents=”lax”/>
</xs:complexType><?xml version=”1.0” encoding=”utf-8”?>


<!-- Example Section in MPD -->
  <EventStream schemeIdUri="urn:scte:scte35:2013a:bin" value="scte35_track_001_000" timescale="10000000">
        <Event presentationTime="15447165200227600" duration="300000000" id="1026">/DAlAAAAAAAAAP/wFAUAAAQCf+//KRjAfP4AKTLgAAAAAAAAVYsh2w==</Event>
        <Event presentationTime="15447166250227600" duration="300000000" id="1027">/DAlAAAAAAAAAP/wFAUAAAQDf+//KaeGwP4AKTLgAAAAAAAAn75a3g==</Event>
        <Event presentationTime="15447167300227600" duration="600000000" id="1028">/DAlAAAAAAAAAP/wFAUAAAQEf+//KjkknP4AUmXAAAAAAAAAWcEldA==</Event>
        <Event presentationTime="15447168350227600" duration="600000000" id="1029">/DAlAAAAAAAAAP/wFAUAAAQFf+//KslyqP4AUmXAAAAAAAAAvKNt0w==</Event>
        <Event presentationTime="15447169400227600" duration="300000000" id="1030">/DAlAAAAAAAAAP/wFAUAAAQGf+//K1mIvP4AKTLgAAAAAAAAt2zEbw==</Event>
        <Event presentationTime="15447170450227600" duration="600000000" id="1031">/DAlAAAAAAAAAP/wFAUAAAQHf+//K+hc/v4AUmXAAAAAAAAANNRzVw==</Event>
    </EventStream>
~~~

>[!NOTE]
>Należy zauważyć, że ten presentationTime po raz prezentacji wydarzenia nie Godzina nadejścia wiadomości.
>

### <a name="431-in-band-event-message-box-signaling"></a>4.3.1 zdarzeń wewnątrzpasmowe komunikatu sygnalizowanie pole
Strumienia zdarzeń w paśmie wymaga MPD posiadania elementu InbandEventStream na poziomie zestawu dostosowania.  Ten element ma atrybut schemeIdUri obowiązkowe i atrybut opcjonalny skali czasu, które również znajdują się w przypadku okna komunikatu (emsg).  Okna komunikatów zdarzeń o identyfikatorach schematu, które nie są zdefiniowane w MPD nie powinien być obecny. Jeśli klient DASH wykryje zdarzenie okno komunikatu przy użyciu schematu, który nie jest zdefiniowany w MPD, klient powinien go zignorować.
Okno komunikatu o zdarzeniu (emsg) zapewnia sygnalizowanie ogólnego zdarzenia powiązane z czasem prezentacji nośnika. Jeśli jest obecny, dowolnego pola "emsg" jest umieszczana przed dowolnego pola "moof".

### <a name="dash-event-message-box-emsg"></a>Okno komunikatu zdarzeń DASH "emsg"
~~~
Box Type: `emsg’
Container: Segment
Mandatory: No
Quantity: Zero or more
~~~

~~~ c
aligned(8) class DASHEventMessageBox extends FullBox(‘emsg’, version = 0, flags =0) 
{
    string scheme_id_uri;
    string value;
    unsigned int(32) timescale;
    unsigned int(32) presentation_time_delta;
    unsigned int(32) event_duration;
    unsigned int(32) id;
    unsigned int(8) message_data[];
}
~~~

Pola DASHEventMessageBox są zdefiniowane poniżej:

| **Nazwa pola**          | **Typ pola**          | **Wymagane?** | **Opis**                                                                                                                                                                                                                                                                                                                                                    |
|-------------------------|-------------------------|---------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| scheme_id_uri           | string                  | Wymagane      | Identyfikuje schematu wiadomości. Schemat jest równa wartości atrybutu schemat w polu na żywo manifestu serwera. Wartość powinna być URN lub adres URL identyfikujący schemat wiadomości. Komunikaty [SCTE 35] trwa to specjalna wartość "urn: scte:scte35:2013a:bin", mimo że [SCTE 67] zaleca się coś innego. |
| Wartość                   | string                  | Wymagane      | Wartość dodatkowy ciąg znaków używany przez właścicieli schematu w celu dostosowania semantyki wiadomości. W celu dokonania rozróżnienia wielu strumieni zdarzeń z tego samego schematu, wartość będzie ustawiana na nazwę strumienia zdarzeń (trackName dla protokołu Smooth pozyskiwania lub AMF nazwa komunikatu dla protokołu RTMP pozyskiwania).                                                                  |
| Skala czasu               | 32-bitowej nieoznaczonej liczby całkowitej | Wymagane      | Skala czasu, w dziesięciomilionowych częściach sekundy na sekundę, czas i czas trwania pól w polu "emsg".                                                                                                                                                                                                                                                                        |
| Presentation_time_delta | 32-bitowej nieoznaczonej liczby całkowitej | Wymagane      | Różnica czasu prezentacji nośnika podczas prezentacji wydarzenia i Najwcześniejsza godzina prezentacji, w tym segmencie. Czas prezentacji i czas trwania należy wyrównać za pomocą punktów dostępu Stream (SAP) typu 1 lub 2, zgodnie z definicją w [ISO-14496-12] załącznika.                                                                                            |
| event_duration          | 32-bitowej nieoznaczonej liczby całkowitej | Wymagane      | Czas trwania zdarzenia lub 0xFFFFFFFF, aby określić Nieznany czas trwania.                                                                                                                                                                                                                                                                                          |
| Identyfikator                      | 32-bitowej nieoznaczonej liczby całkowitej | Wymagane      | Określa wystąpienie tej wiadomości. Komunikaty z semantyką równoważne mają taką samą wartość. Jeśli nie określono Identyfikatora, gdy wiadomości są pozyskiwane, usługi Azure Media Services generuje unikatowy identyfikator.                                                                                                                                                    |
| Message_data            | tablica bajtów              | Wymagane      | Komunikat zdarzenia. Komunikaty [SCTE 35] danych komunikatu jest binarny splice_info_section(), chociaż [SCTE 67] zaleca się coś innego.                                                                                                                                                                                                                                 |

### <a name="332-dash-message-handling"></a>3.3.2 KRESKI Obsługa komunikatów

Zdarzenia są sygnalizowane wewnątrzpasmowe, w polu "emsg" dla ścieżki audio i wideo.  Sygnalizowanie występuje dla segmentu wszystkie żądania, dla których presentation_time_delta wynosi 15 sekund lub mniej. Po włączeniu przesuwającego się okna prezentacji komunikaty o zdarzeniach są usuwane z MPD, gdy sumę czas i czas trwania komunikatów o zdarzeniach jest krótszy niż czas dane multimedialne w manifeście.  Innymi słowy komunikaty o zdarzeniach są usuwane z manifestu, gdy czas multimediów, do którego odnoszą się została wycofana, poza przesuwającego się okna prezentacji.

## <a name="4-scte-35-ingest"></a>4 SCTE 35 pozyskiwania

Komunikaty [SCTE 35] są pozyskiwane w formacie binarnym, przy użyciu schematu **"urn: scte:scte35:2013a:bin"** dla protokołu Smooth pozyskiwania i typ **"scte35"** dla protokołu RTMP pozyskiwania. Aby ułatwić konwersji chronometrażu [SCTE 35], która jest oparta na MPEG-2 transport stream prezentacji sygnatur czasowych (pkt), mapowanie między pkt (pts_time + pts_adjustment splice_time()) i oś czasu multimediów są dostarczane przez (czas prezentacja wydarzenia pole fragment_absolute_time dla protokołu Smooth pozyskiwać i pozyskiwania pole czasu protokołu RTMP). Mapowanie jest konieczne, ponieważ 33-bitową wartością pkt najedzie na około na 26,5 godzin.

Pozyskiwanie Smooth Streaming wymaga, które musi zawierać pola danych (mdat) **splice_info_section()** zdefiniowany w tabeli 8-1 [SCTE 35]. Dla protokołu RTMP pozyskiwania, base64encoded ma ustawioną wartość atrybutu sygnalizacji komunikatu AMF **splice_info_section()**. Jeśli komunikaty ma format opisany powyżej, te są wysyłane do klientów HLS i Smooth, Dash zgodne z [SCTE 67].


## <a name="5-references"></a>Odwołania do 5

**[SCTE 35]**  ANSI/SCTE 35 2013a — Program cyfrowego wstawiania kolejka komunikat dla kabel, 2013a

**[SCTE 67]**  ANSI/SCTE 67 2014 — zalecana praktyka dla SCTE 35: Cyfrowy Program wstawiania kolejka komunikatów kabla

**[KRESKA]**  ISO/IEC 23009 1 2014 — informacje o technologii — dynamiczne adaptacyjnego przesyłania strumieniowego za pośrednictwem protokołu HTTP (DASH) — część 1: Prezentacja Media opis segmentu formatów i wydanie 2

**[HLS]**  ["HTTP transmisja strumieniowa na żywo", draft-pantos-http-live-streaming-14, 14 października 2014 roku](http://tools.ietf.org/html/draft-pantos-http-live-streaming-14)

**[MS-SSTR]**  ["Zestawu Microsoft Smooth Streaming Protocol", 15 maja 2014 r.](https://download.microsoft.com/download/9/5/E/95EF66AF-9026-4BB0-A41D-A4F81802D92C/%5bMS-SSTR%5d.pdf)

**[AMF0]**  ["AMF0 Format komunikatu akcji"](https://download.macromedia.com/pub/labs/amf/amf0_spec_121207.pdf)

**[NA ŻYWO — FMP4]**  [Specyfikacja odbierania podzielonej zawartości w formacie MP4 na żywo w usłudze azure Media Services](https://docs.microsoft.com/azure/media-services/media-services-fmp4-live-ingest-overview)

**[ISO-14496-12]** ISO/IEC 14496-12: Część 12 ISO podstawowy media format plików, czwarty wersji 2012-07-15.

**[RTMP]**  ["Firmy adobe w czasie rzeczywistym komunikatów protokołu", 21 grudnia 2012](https://www.adobe.com/devnet/rtmp.html) 

------------------------------------------

## <a name="next-steps"></a>Kolejne kroki
Wyświetl usługi Media Services ścieżki szkoleniowe.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
