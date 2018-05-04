---
title: Azure Media Services - sygnalizowania upłynął metadanych w transmisji strumieniowej na żywo | Dokumentacja firmy Microsoft
description: Określenie tej wartości opisano dwa tryby, które są obsługiwane przez usługę Media Services sygnalizowania czasu metadanych w ramach transmisji strumieniowych na żywo. Dotyczy to również obsługi sygnałów ogólnego metadanych czasu, a także SCTE 35 sygnalizowania wstawić łączenia ad.
services: media-services
documentationcenter: ''
author: cenkdin
manager: cfowler
editor: johndeu
ms.assetid: 265b94b1-0fb8-493a-90ec-a4244f51ce85
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/17/2018
ms.author: johndeu;
ms.openlocfilehash: 9c8472e74cab779e417e68316a6125d40410ef1c
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="signaling-timed-metadata-in-live-streaming"></a>Sygnalizowanie upłynął metadanych w transmisji strumieniowej na żywo


## <a name="1-introduction"></a>Wprowadzenie 1 
W celu umożliwienia wstawiania reklam lub zdarzeń niestandardowych na odtwarzacz klienta, często nadawców upewnij stosowania metadanych czasu osadzony w wideo. Aby włączyć takie scenariusze, Media Services zapewnia obsługę transportu czasu metadanych wraz z nośnika, od momentu pozyskiwania kanału transmisji strumieniowej na żywo do aplikacji klienckiej.
Tej specyfikacji zawiera dwa tryby, obsługiwanych przez usługę Media Services przekroczono metadanych w live sygnały przesyłania strumieniowego:

1. [SCTE 35] sygnalizowania, który heeds zalecane praktyki otoczony [SCTE 67]

2. Element ogólny upłynął sygnalizowania trybu wiadomości, które nie są metadanych [SCTE 35]

### <a name="12-conformance-notation"></a>1.2 Notacja zgodność
Słowa kluczowe "musisz", "Nie", "Wymagane", "SHALL", "Nie jest", "SHOULD", "Nie powinien", "Zalecane", "Może" i "OPTIONAL", w tym dokumencie są należy interpretować zgodnie z opisem w dokumencie RFC 2119

### <a name="13-terms-used"></a>1.3 terminów używanych

| Termin              | Definicja                                                                                                                                                                                                                       |
|-------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Czas prezentacji | Czas, jaki zdarzenia jest prezentowana w przeglądarce. Czas reprezentuje moment na osi czasu nośnika, że przeglądarka zobaczyć zdarzenia. Na przykład czas prezentacji wiadomości polecenia splice_info() SCTE 35 jest splice_time(). |
| Godzina nadejścia      | Czas przychodzący komunikat o zdarzeniu. Czas zwykle różni się od czasu prezentacji zdarzenia, ponieważ wcześniejsze prezentacji zdarzenia są wysyłane wiadomości zdarzenie.                                     |
| Śledź rozrzedzony      | nośnik śledzenia, które nie jest ciągły i jest czas został zsynchronizowany ze ścieżką nadrzędną lub formantu.                                                                                                                                    |
| Origin            | Usługi przesyłania strumieniowego multimediów Azure                                                                                                                                                                                                |
| Zbiornik kanału      | Usługa transmisji strumieniowej na żywo multimediów Azure                                                                                                                                                                                           |
| HLS               | Protokół Apple HTTP transmisji strumieniowej na żywo                                                                                                                                                                                               |
| DASH              | Dynamiczne adaptacyjne przesyłanie strumieniowe za pośrednictwem protokołu HTTP                                                                                                                                                                                             |
| Smooth            | Smooth protokołu przesyłania strumieniowego                                                                                                                                                                                                        |
| MPEG2 TS          | Strumienie 2 transportu MPEG                                                                                                                                                                                                         |
| RTMP              | Protokół multimediów w czasie rzeczywistym                                                                                                                                                                                                    |
| uimsbf            | Liczba całkowita bez znaku, najbardziej znaczących bitów najpierw.                                                                                                                                                                                    |

-----------------------

## <a name="2-timed-metadata-ingest"></a>Pozyskiwania 2 czasu metadanych
## <a name="21-rtmp-ingest"></a>2.1 pozyskiwania RTMP
RTMP obsługuje wysyłane jako wiadomości sygnalizacji AMF osadzone w strumieniu RTMP sygnały czasu metadanych. W komunikatach sygnalizacji mogą być wysyłane pewien czas, zanim rzeczywistego zdarzenia lub wstawiania łączenia ad musi nastąpić. Aby obsługiwać ten scenariusz, rzeczywisty czas łączenia lub segmentu są wysyłane wiadomości utrwalenie. Aby uzyskać więcej informacji zobacz [AMF0].

W poniższej tabeli opisano format AMF ładunek komunikatu, który będzie pozyskiwania Media Services.

Nazwa komunikatu AMF może służyć do odróżnienia wielu strumieni zdarzeń z tego samego typu.  Komunikaty [SCTE 35] Nazwa komunikatu AMF musi być "onAdCue", zgodnie z zaleceniami [SCTE 67].  Wszystkie pola, które nie są wymienione poniżej muszą być ignorowane, dzięki czemu innowacji ten projekt nie jest zabronione w przyszłości.

### <a name="signal-syntax"></a>Składnia sygnału
Tryb prosty RTMP Media Services obsługuje pojedynczą wiadomość sygnalizacji AMF o nazwie "onAdCue" o następującym formacie:

### <a name="simple-mode"></a>Tryb prosty

| Nazwa pola | Typ pola | Wymagana? | Opisy                                                                                                             |
|------------|------------|----------|--------------------------------------------------------------------------------------------------------------------------|
| wskaźnik        | Ciąg     | Wymagane | Komunikaty o zdarzeniach.  Powinien być "SpliceOut", aby określić tryb prosty splice.                                              |
| id         | Ciąg     | Wymagane | Unikatowy identyfikator opisujący łączenia lub segmentu. Identyfikuje wystąpienie tego komunikatu                            |
| Czas trwania   | Liczba     | Wymagane | Czas trwania łączenia. Jednostki są ułamkowych części sekundy.                                                                |
| elapsed    | Liczba     | Optional (Opcjonalność) | Gdy sygnał jest zajdą w celu zapewnienia obsługi dostrajania, to pole jest czas prezentacji, który upłynął od chwili rozpoczęcia łączenia. Jednostki są ułamkowych części sekundy. Podczas korzystania z trybu simple, ta wartość nie może przekraczać oryginalnego czas trwania łączenia.                                                  |
| time       | Liczba     | Wymagane | Się podczas łączenia, w czasie prezentacji. Jednostki są ułamkowych części sekundy.                                     |

---------------------------

### <a name="scte-35-mode"></a>Tryb SCTE 35

| Nazwa pola | Typ pola | Wymagana? | Opisy                                                                                                             |
|------------|------------|----------|--------------------------------------------------------------------------------------------------------------------------|
| wskaźnik        | Ciąg     | Wymagane | Komunikaty o zdarzeniach.  Komunikaty [SCTE 35], musi to być base64 pliku binarnego (grupa robocza IETF RFC 4648) zakodowany splice_info_section() w poszukiwaniu komunikatów do wysłania do HLS i Smooth, Dash klientów z zasadami [SCTE 67].                                              |
| type       | Ciąg     | Wymagane | URN lub adres URL identyfikujący system komunikatów; na przykład "urn: przykład: sygnalizowania: 1.0".  Komunikaty [SCTE 35] musi to być "urn: scte:scte35:2013a:bin", aby wiadomości można wysyłać do klientów HLS i Smooth, Dash z aktualizacjami [SCTE 67].  |
| id         | Ciąg     | Wymagane | Unikatowy identyfikator opisujący łączenia lub segmentu. Identyfikuje wystąpienie tej wiadomości.  Komunikaty z semantyki równoważne mają taką samą wartość.|
| Czas trwania   | Liczba     | Wymagane | Czas trwania zdarzenia lub ad łączenia segmentu, jeśli znane. Jeśli jest nieznany, wartość musi mieć wartość 0.                                                                 |
| elapsed    | Liczba     | Optional (Opcjonalność) | Gdy w celu dostrojenia powtarza się sygnał ad [SCTE 35] i to pole będzie prezentacji czas, który upłynął od chwili rozpoczęcia łączenia. Jednostki są ułamkowych części sekundy. W trybie [SCTE 35] Ta wartość może przekroczyć oryginalnego podany czas trwania łączenia lub segmentu.                                                  |
| time       | Liczba     | Wymagane | Godzina prezentacji łączenia zdarzeń lub ad.  Prezentacja czas i czas trwania powinno odpowiadać z punktów dostępu strumienia (SAP) typu 1 lub 2, zgodnie z definicją w [ISO-14496-12] załącznika. Za wyjście HLS czas i czas trwania powinno odpowiadać granice segmentu. Czas prezentacji i czas trwania zdarzeń komunikatów w tym samym strumienia zdarzeń muszą nie mogą się pokrywać. Jednostki są ułamkowych części sekundy.

---------------------------

#### <a name="211-cancelation-and-updates"></a>2.1.1 anulowanie i aktualizacje

Komunikaty można anulować lub zaktualizować wysyłając wiele komunikatów z tym samym czasie prezentacji i identyfikator. Czas prezentacji i identyfikator jednoznacznego zidentyfikowania zdarzenia i ostatniego komunikatu Odebrano raz określonych prezentacji spełniającego ograniczenia wstępne zbiorczego jest komunikat, który jest reagować. Zaktualizowano zdarzenie zastępuje wszystkie wcześniej odebranej wiadomości. Ograniczenie wstępne zbiorczego jest cztery sekundy. Co najmniej cztery sekund przed czasem prezentacji odebrane wiadomości będzie reagować.

## <a name="22-fragmented-mp4-ingest-smooth-streaming"></a>2.2 pozyskiwania pofragmentowane MP4 (Smooth Streaming)
Można znaleźć [LIVE FMP4] dla pozyskiwania wymagania dotyczące strumień na żywo. Poniższe sekcje zawierają szczegółowe informacje dotyczące pozyskiwania metadanych czasu prezentacji.  Przekroczono prezentacji metadanych jest pozyskanych jako ścieżkę rozrzedzonych jest zdefiniowany w obu Live manifestu okno serwera (zobacz MS SSTR), a pole Movie (moov).  Każdy fragment rozrzedzonych składa się z filmu fragmentu pola (moof) oraz nośnika danych ("mdat"), pole "mdat" w przypadku komunikatu binarnego.

#### <a name="221-live-server-manifest-box"></a>2.2.1 okno manifestu serwera na żywo
Śledź rozrzedzonych musi zostać zadeklarowany w polu Live manifestu serwera z \<textstream\> wpis, a musi mieć ustawiony atrybut następujące:

| **Nazwa atrybutu** | **Typ pola** | **Wymagane?** | **Opis**                                                                                                                                                                                                                                                 |
|--------------------|----------------|---------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| systemBitrate      | Liczba         | Wymagane      | MUSI mieć wartość "0", wskazujący ścieżkę z nieznanego, zmienne szybkości transmisji bitów.                                                                                                                                                                                                 |
| parentTrackName    | Ciąg         | Wymagane      | MUSI być nazwą ścieżki nadrzędnej, Śledź rozrzedzonych kody czasu są wyrównane skali czasu. Śledź nadrzędnego nie może być ścieżką rozrzedzonych.                                                                                                                    |
| manifestOutput     | Wartość logiczna        | Wymagane      | MUSI być "prawda", aby wskazać, że rozrzedzonych Śledź zostanie osadzony w manifeście Smooth klienta.                                                                                                                                                               |
| Podtyp            | Ciąg         | Wymagane      | MUSI być znakiem cztery kodu "Dane".                                                                                                                                                                                                                         |
| Schemat             | Ciąg         | Wymagane      | MUSI być URN lub adresem URL identyfikujący system komunikatów; na przykład "urn: przykład: sygnalizowania: 1.0". Komunikaty [SCTE 35] musi to być "urn: scte:scte35:2013a:bin", aby wiadomości można wysyłać do klientów HLS i Smooth, Dash z aktualizacjami [SCTE 67]. |
| TrackName          | Ciąg         | Wymagane      | MUSI być nazwą ścieżki rozrzedzone. TrackName może służyć do odróżnienia wielu strumieni zdarzeń z tego samego schematu. Każdego strumienia zdarzeń unikatowy musi mieć nazwę unikatową ścieżkę.                                                                           |
| skali czasu          | Liczba         | Optional (Opcjonalność)      | MUSI być skali czasu ścieżki nadrzędnej.                                                                                                                                                                                                                      |

-------------------------------------

### <a name="222-movie-box"></a>2.2.2 pole film

Pole Movie (moov) jest zgodna Live manifestu okno serwera jako części nagłówka strumienia dla ścieżki rozrzedzone.

Pole "moov" powinien zawierać **TrackHeaderBox (tkhd)** polu zgodnie z definicją w [ISO-14496-12], z następującymi ograniczeniami:

| **Nazwa pola** | **Typ pola**          | **Wymagane?** | **Opis**                                                                                                |
|----------------|-------------------------|---------------|----------------------------------------------------------------------------------------------------------------|
| Czas trwania       | 64-bitowej liczby całkowitej bez znaku | Wymagane      | MUSI mieć wartość 0, ponieważ pole ścieżki ma zerowy przykłady i całkowity czas trwania próbek w polu ścieżki wynosi 0. |
-------------------------------------

Pole "moov" powinien zawierać **HandlerBox (hdlr)** zgodnie z definicją w [ISO-14496-12], z następującymi ograniczeniami:

| **Nazwa pola** | **Typ pola**          | **Wymagane?** | **Opis**   |
|----------------|-------------------------|---------------|-------------------|
| handler_type   | 32-bitowa liczba całkowita bez znaku | Wymagane      | POWINIEN być 'meta'. |
-------------------------------------

Pole "stsd" powinien zawierać MetaDataSampleEntry pole o nazwie kodowania, zgodnie z definicją w [ISO-14496-12].  Na przykład wiadomości SCTE 35 kodowania nazwa powinna być "scte".

### <a name="223-movie-fragment-box-and-media-data-box"></a>2.2.3 pola fragmentu filmu oraz nośnika danych

Śledź rozrzedzonych fragmenty składają się z polem fragmentu Movie (moof) i pola danych nośnika (mdat).

Pole MovieFragmentBox (moof) musi zawierać **TrackFragmentExtendedHeaderBox (uuid)** polu zgodnie z definicją w [MS-SSTR] z następującymi polami:

| **Nazwa pola**         | **Typ pola**          | **Wymagane?** | **Opis**                                                                               |
|------------------------|-------------------------|---------------|-----------------------------------------------------------------------------------------------|
| fragment_absolute_time | 64-bitowej liczby całkowitej bez znaku | Wymagane      | MUSI być Godzina nadejścia zdarzenia. Ta wartość jest wyrównywany komunikat ze ścieżką nadrzędną.   |
| fragment_duration      | 64-bitowej liczby całkowitej bez znaku | Wymagane      | MUSI być czas trwania zdarzenia. Czas trwania może być równa zero, aby wskazać, że czas trwania jest nieznany. |

------------------------------------


Pole MediaDataBox (mdat) musi mieć następujący format:

| **Nazwa pola**          | **Typ pola**                   | **Wymagane?** | **Opis**                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
|-------------------------|----------------------------------|---------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| wersja                 | 32-bitową liczbę całkowitą bez znaku (uimsbf) | Wymagane      | Określa format zawartości pola "mdat". Nierozpoznany wersji zostaną zignorowane. Obecnie tylko obsługiwana wersja to 1.                                                                                                                                                                                                                                                                                                                                                      |
| id                      | 32-bitową liczbę całkowitą bez znaku (uimsbf) | Wymagane      | Identyfikuje wystąpienie tej wiadomości. Komunikaty z semantyki równoważne mają taką samą wartość; Przetwarzanie dowolnego pola wiadomości jedno zdarzenie o tym samym identyfikatorze jest wystarczająca.                                                                                                                                                                                                                                                                                                            |
| presentation_time_delta | 32-bitową liczbę całkowitą bez znaku (uimsbf) | Wymagane      | Suma fragment_absolute_time, określona w TrackFragmentExtendedHeaderBox i presentation_time_delta musi być czasu prezentacji zdarzenia. Prezentacja czas i czas trwania powinno odpowiadać z punktów dostępu strumienia (SAP) typu 1 lub 2, zgodnie z definicją w [ISO-14496-12] załącznika. Za wyjście HLS czas i czas trwania powinno odpowiadać granice segmentu. Czas prezentacji i czas trwania zdarzeń komunikatów w tym samym strumienia zdarzeń muszą nie mogą się pokrywać. |
| message                 | Tablica bajtów                       | Wymagane      | Komunikaty o zdarzeniach. Komunikatów [SCTE 35] wiadomość jest splice_info_section() binarnego, mimo że [SCTE 67] zaleca coś innego. Komunikaty [SCTE 35] musi to być splice_info_section() w poszukiwaniu komunikatów do wysłania do HLS i Smooth, Dash klientów z zasadami [SCTE 67]. Komunikatów [SCTE 35] splice_info_section() binarny jest ładunku pole "mdat", a nie jest kodowany w standardzie base64.                                                            |

------------------------------


### <a name="224-cancelation-and-updates"></a>2.2.4 Anulowanie i aktualizacje
Komunikaty można anulować lub zaktualizować wysyłając wiele komunikatów z tym samym czasie prezentacji i identyfikator.  Czas prezentacji i identyfikator jednoznacznego zidentyfikowania zdarzenia. Ostatni komunikat raz określonych prezentacji, spełniającego ograniczenia wstępne zbiorczego jest komunikat, który jest reagować. Zaktualizowano komunikat zastępuje wszystkie wcześniej odebranej wiadomości.  Ograniczenie wstępne zbiorczego jest cztery sekundy. Co najmniej cztery sekund przed czasem prezentacji odebrane wiadomości będzie reagować. 


## <a name="3-timed-metadata-delivery"></a>3 upłynął dostarczania metadanych

Dane strumienia zdarzeń jest nieprzezroczysta dla usługi Media Services. Usługa Media Services jedynie przekazuje trzy informacje między punktem końcowym pozyskiwanie i punktu końcowego klienta. Następujące właściwości są dostarczane do klienta, zgodnie z [SCTE 67] i/lub klienta do przesyłania strumieniowego protokołu:

1.  Schemat — URN lub adres URL identyfikujący schemat wiadomości.

2.  Czas prezentacji — czas prezentacji zdarzenia na osi czasu nośnika.

3.  Czas trwania — czas trwania zdarzenia.

4.  Identyfikator — opcjonalny unikalny identyfikator zdarzenia.

5.  Komunikat — dane zdarzenia.


## <a name="31-smooth-streaming-delivery"></a>3.1 płynnego przesyłania strumieniowego dostarczania

Odwoływać się do ścieżki rozrzedzonych Obsługa szczegóły w [MS-SSTR].

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

## <a name="32--apple-hls-delivery"></a>3.2 Apple HLS dostarczania
Przekroczono metadanych dla Apple HTTP Live Streaming (HLS) może być osadzony w odtwarzania segmentu w niestandardowych tagu M3U.  Warstwy aplikacji można przeanalizować odtwarzania M3U i przetworzyć M3U tagów. Usługa Azure Media Services będą Osadź metadane czasu w tagu EXT-X-sygnalizacji zdefiniowane w [HLS].  Tag EXT-X-wskaźnika jest obecnie używana przez DynaMux komunikatów typu ADI3.  Aby umożliwić obsługę wiadomości SCTE 35 SCTE 35 i nie, należy ustawić atrybuty znacznika EXT-X-sygnalizacji zdefiniowane poniżej:

| **Nazwa atrybutu** | **Typ**                      | **Wymagane?**                             | **Opis**                                                                                                                                                                                                                                                                      |
|--------------------|-------------------------------|-------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| WSKAŹNIK                | ciąg w cudzysłowie                 | Wymagane                                  | Komunikat został zakodowany jako ciąg base64 zgodnie z opisem w [IETF RFC 4648](http://tools.ietf.org/html/rfc4648). Komunikaty [SCTE 35] to splice_info_section() kodowany w standardzie base64.                                                                                                |
| TYP               | ciąg w cudzysłowie                 | Wymagane                                  | URN lub adres URL identyfikujący system komunikatów; na przykład "urn: przykład: sygnalizowania: 1.0". Komunikaty [SCTE 35] Typ ma specjalna wartość "scte35".                                                                                                                                |
| ID                 | ciąg w cudzysłowie                 | Wymagane                                  | Unikatowy identyfikator zdarzenia. Jeśli nie określono Identyfikatora, gdy komunikat jest pozyskanych, usługi Azure Media Services wygeneruje Unikatowy identyfikator.                                                                                                                                          |
| CZAS TRWANIA           | dziesiętny liczb zmiennoprzecinkowych | Wymagane                                  | Czas trwania zdarzenia. Jeśli jest nieznany, wartość musi mieć wartość 0. Jednostki są factional sekund.                                                                                                                                                                                           |
| ELAPSED            | dziesiętny liczb zmiennoprzecinkowych | Opcjonalne, lecz wymagane dla przesuwanego okna | Jeśli do obsługi metodzie przesuwanego okna prezentacji powtarza się sygnał i to pole musi zawierać czas prezentacji, który upłynął od chwili rozpoczęcia zdarzenia. Jednostki są ułamkowych części sekundy. Ta wartość może przekroczyć oryginalnego podany czas trwania łączenia lub segmentu. |
| CZAS               | dziesiętny liczb zmiennoprzecinkowych | Wymagane                                  | Czas prezentacji zdarzenia. Jednostki są ułamkowych części sekundy.                                                                                                                                                                                                                    |


Warstwa aplikacji odtwarzacza HLS zostanie użyty typ zidentyfikować format wiadomości, zdekodować komunikatu, zastosuj konwersje niezbędne czasu i przetwarzanie zdarzenia.  Zdarzenia są synchronizacji z listy odtwarzania segmentu ścieżki nadrzędnej czasu zgodnie z sygnaturą czasową zdarzenia.  Dodaje się one przed najbliższej segmentu (#EXTINF tag).

#### <a name="hls-segment-playlist-example"></a>Przykład listy odtwarzania segmentu HLS
~~~
#EXTM3U
#EXT-X-VERSION:4
#EXT-X-ALLOW-CACHE:NO
#EXT-X-MEDIA-SEQUENCE:0
#EXT-X-TARGETDURATION:6
#EXT-X-PROGRAM-DATE-TIME:1970-01-01T00:00:00.000+00:00
#EXTINF:6.000000,no-desc
Fragments(video=0,format=m3u8-aapl)
#EXTINF:6.000000,no-desc
Fragments(video=60000000,format=m3u8-aapl)
#EXTINF:6.000000,no-desc
#EXT-X-CUE: ID=”metadata-12.000000”,TYPE=”urn:example:signaling:1.0”,TIME=”12.000000”, DURATION=”18.000000”,CUE=”HrwOi8vYmWVkaWEvhhaWFRlRDa=”
Fragments(video=120000000,format=m3u8-aapl)
#EXTINF:6.000000,no-desc
Fragments(video=180000000,format=m3u8-aapl)
#EXTINF:6.000000,no-desc
Fragments(video=240000000,format=m3u8-aapl)
#EXTINF:6.000000,no-desc
Fragments(video=300000000,format=m3u8-aapl)
#EXTINF:6.000000,no-desc
Fragments(video=360000000,format=m3u8-aapl)
#EXT-X-CUE: ID=”metadata-42.000000”,TYPE=”urn:example:signaling:1.0”,TIME=”42.000000”, DURATION=”60.000000”,CUE=”PD94bWwgdm0iMS4wIiBlbmNvpD4=”
#EXTINF:6.000000,no-desc
Fragments(video=420000000,format=m3u8-aapl)
#EXTINF:6.000000,no-desc
Fragments(video=480000000,format=m3u8-aapl)
…
~~~

#### <a name="hls-message-handling"></a>Obsługa komunikatów HLS

Zdarzenia są sygnalizowane listy odtwarzania segmentu każdej ścieżki audio i wideo. Położenie znaczników EXT-X-sygnalizacji zawsze musi być bezpośrednio przed pierwszym HLS segmentu (w przypadku łączenia się lub uruchom segmentu) lub od razu po ostatnim HLS segmentu (w przypadku łączenia w lub zakończenia segmentu) do którego odwołuje się jego atrybuty oraz czas trwania, co jest wymagane przez [ HLS].

Po włączeniu metodzie przesuwanego okna prezentacji tag EXT-X-sygnalizacji powtarza się wystarczająco często czy łączenia lub segmentu jest zawsze szczegółowo opisane odtwarzania segmentu i należy użyć atrybutu UPŁYNĘŁO wskazująca czas łączenia lub ma segmentu były aktywne, co jest wymagane przez [HLS].

Po włączeniu metodzie przesuwanego okna prezentacji tagi EXT-X-sygnalizacji są usuwane z listy odtwarzania segmentu, gdy czas nośnika, które odnoszą się do została wycofana poza metodzie przesuwanego okna prezentacji.

## <a name="33--dash-delivery"></a>3.3 DASH dostarczania
[DASH] zapewnia trzy sposoby sygnału zdarzenia:

1.  Zdarzenia sygnalizuje w MPD
2.  Zdarzenia sygnalizuje wewnątrzpasmowe w reprezentacji (przy użyciu pola wiadomości zdarzenia (emsg)
3.  Kombinacja 1 i 2

Zdarzenia sygnalizuje w MPD są przydatne w przypadku VOD przesyłania strumieniowego, ponieważ klienci mają dostęp do wszystkich zdarzeń, natychmiast po pobraniu MPD. Rozwiązanie wewnątrzpasmowe jest przydatne w przypadku strumieniowych na żywo, ponieważ klienci nie trzeba ponownie pobrać MPD. W przypadku segmentacji na podstawie czasu klienta Określa adres URL dla następnego segmentu przez dodanie czasu trwania i sygnatury czasowej bieżącego segmentu. Że żądanie nie powiedzie się, klient założono brak ciągłości i pobiera MPD, ale w przeciwnym razie nadal przesyłania strumieniowego bez pobierania MPD.

Usługa Azure Media Services będą nie zarówno sygnalizowania MPD i wewnątrzpasmowe sygnalizowania, za pomocą pola wiadomości zdarzenie.

#### <a name="mpd-signaling"></a>Sygnalizowanie MPD

Zdarzenia są sygnalizowane w MPD przy użyciu elementu EventStream, który znajduje się w elemencie okresu.

EventStream element ma następujące atrybuty:

| **Nazwa atrybutu** | **Typ**                | **Wymagane?** | **Opis**                                                                                                                                                                                                                                                                                   |
|--------------------|-------------------------|---------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| scheme_id_uri      | ciąg                  | Wymagane      | Identyfikuje system komunikatu. Schemat ma ustawioną wartość atrybutu schematu w polu manifestu serwera na żywo. Wartość powinna być URN lub adres URL identyfikujący system komunikatów; na przykład "urn: przykład: sygnalizowania: 1.0".                                                                |
| wartość              | ciąg                  | Optional (Opcjonalność)      | Wartość dodatkowy ciąg używany przez właścicieli schematu w celu dostosowania semantyki wiadomości. W celu rozróżnienia wielu strumieni zdarzeń z tego samego schematu, wartość musi mieć ustawioną nazwę strumienia zdarzeń (trackName dla Smooth pozyskiwania lub AMF komunikat nazwę RTMP pozyskiwania). |
| skali czasu          | 32-bitowa liczba całkowita bez znaku | Wymagane      | Skali czasu, w impulsów na sekundę, czas i czas trwania pola w polu "emsg".                                                                                                                                                                                                       |


Zero lub więcej elementów zdarzenia są zawarte w elemencie EventStream i mają następujące atrybuty:

| **Nazwa atrybutu**  | **Typ**                | **Wymagane?** | **Opis**                                                                                                                                                                                                             |
|---------------------|-------------------------|---------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| presentation_time   | 64-bitowej liczby całkowitej bez znaku | Optional (Opcjonalność)      | MUSI być nośnika czas prezentacji zdarzenia względem początek okresu. Prezentacja czas i czas trwania powinno odpowiadać z punktów dostępu strumienia (SAP) typu 1 lub 2, zgodnie z definicją w [ISO-14496-12] załącznika. |
| Czas trwania            | 32-bitowa liczba całkowita bez znaku | Optional (Opcjonalność)      | Czas trwania zdarzenia. Jeśli czas trwania jest nieznany, to musi być pominięte.                                                                                                                                                 |
| id                  | 32-bitowa liczba całkowita bez znaku | Optional (Opcjonalność)      | Identyfikuje wystąpienie tej wiadomości. Komunikaty z semantyki równoważne mają taką samą wartość. Jeśli nie określono Identyfikatora, gdy komunikat jest pozyskanych, usługi Azure Media Services wygeneruje Unikatowy identyfikator.             |
| Wartość elementu zdarzeń | ciąg                  | Wymagane      | Komunikat zdarzenia jako ciąg base64, zgodnie z opisem w [IETF RFC 4648](http://tools.ietf.org/html/rfc4648).                                                                                                                   |

#### <a name="xml-syntax-and-example-for-dash-manifest-mpd-signaling"></a>Przykład dla kreska i składnia XML manifestu sygnalizacja (MPD)

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

<EventStream schemeIdUri=”urn:example:signaling:1.0” timescale=”1000” value=”player-statistics”>
  <Event presentationTime=”0” duration=”10000” id=”0”> PD94bWwgdmVyc2lvbj0iMS4wIiBlbmNvZGluZz0idXRmLTgiPz48QWNxdWlyZWRTaWduYWwgeG1sbnM9InVybjpjYWJsZWxhYnM6bWQ6eHNkOnNpZ25hbGluZzozLjAiIGFjcXVpc2l0aW9uUG9pbnRJZGVudGl0eT0iRVNQTl9FYXN0X0FjcXVpc2l0aW9uX1BvaW50XzEiIGFjcXVpc2l0aW9uU2lnbmFsSUQ9IjRBNkE5NEVFLTYyRkExMUUxQjFDQTg4MkY0ODI0MDE5QiIgYWNxdWlzaXRpb25UaW1lPSIyMDEyLTA5LTE4VDEwOjE0OjI2WiI+PFVUQ1BvaW50IHV0Y1BvaW50PSIyMDEyLTA5LTE4VDEwOjE0OjM0WiIvPjxTQ1RFMzVQb2ludERlc2NyaXB0b3Igc3BsaWNlQ29tbWFuZFR5cGU9IjUiPjxTcGxpY2VJbnNlcnQgc3BsaWNlRXZlbnRJRD0iMzQ0NTY4NjkxIiBvdXRPZk5ldHdvcmtJbmRpY2F0b3I9InRydWUiIHVuaXF1ZVByb2dyYW1JRD0iNTUzNTUiIGR1cmF0aW9uPSJQVDFNMFMiIGF2YWlsTnVtPSIxIiBhdmFpbHNFeHBlY3RlZD0iMTAiLz48L1NDVEUzNVBvaW50RGVzY3JpcHRvcj48U3RyZWFtVGltZXM+PFN0cmVhbVRpbWUgdGltZVR5cGU9IkhTUyIgdGltZVZhbHVlPSI1MTUwMDAwMDAwMDAiLz48L1N0cmVhbVRpbWVzPjwvQWNxdWlyZWRTaWduYWw+</Event>
  <Event presentationTime=”20000” duration=”10000” id=”1”> PD94bWwgdmVyc2lvbj0iMS4wIiBlbmNvZGluZz0idXRmLTgiPz48QWNxdWlyZWRTaWduYWwgeG1sbnM9InVybjpjYWJsZWxhYnM6bWQ6eHNkOnNpZ25hbGluZzozLjAiIGFjcXVpc2l0aW9uUG9pbnRJZGVudGl0eT0iRVNQTl9FYXN0X0FjcXVpc2l0aW9uX1BvaW50XzEiIGFjcXVpc2l0aW9uU2lnbmFsSUQ9IjRBNkE5NEVFLTYyRkExMUUxQjFDQTg4MkY0ODI0MDE5QiIgYWNxdWlzaXRpb25UaW1lPSIyMDEyLTA5LTE4VDEwOjE0OjI2WiI+PFVUQ1BvaW50IHV0Y1BvaW50PSIyMDEyLTA5LTE4VDEwOjE0OjM0WiIvPjxTQ1RFMzVQb2ludERlc2NyaXB0b3Igc3BsaWNlQ29tbWFuZFR5cGU9IjUiPjxTcGxpY2VJbnNlcnQgc3BsaWNlRXZlbnRJRD0iMzQ0NTY4NjkxIiBvdXRPZk5ldHdvcmtJbmRpY2F0b3I9InRydWUiIHVuaXF1ZVByb2dyYW1JRD0iNTUzNTUiIGR1cmF0aW9uPSJQVDFNMFMiIGF2YWlsTnVtPSIxIiBhdmFpbHNFeHBlY3RlZD0iMTAiLz48L1NDVEUzNVBvaW50RGVzY3JpcHRvcj48U3RyZWFtVGltZXM+PFN0cmVhbVRpbWUgdGltZVR5cGU9IkhTUyIgdGltZVZhbHVlPSI1MTYyMDAwMDAwMDAiLz48L1N0cmVhbVRpbWVzPjwvQWNxdWlyZWRTaWduYWw+</Event>
</EventStream>
~~~

>[!NOTE]
>Należy zauważyć, że ten presentationTime to czas prezentacji zdarzenia, nie Godzina nadejścia wiadomości.
>

### <a name="431-in-band-event-message-box-signaling"></a>4.3.1 zdarzeń wewnątrzpasmowe komunikatu sygnalizowania pole
Strumienia zdarzeń w paśmie wymaga MPD ma InbandEventStream element na poziomie ustawić dostosowania.  Ten element ma schemeIdUri obowiązkowy atrybut i atrybut opcjonalny skali czasu, które również znajdują się w przypadku pola wiadomości (emsg).  Pola komunikatów zdarzeń o identyfikatorach schematu, które nie są zdefiniowane w MPD nie powinny znajdować się. Jeśli klient DASH wykryje komunikat zdarzenia ze schematem, który nie jest zdefiniowany w MPD, klient powinien go zignorować.
Okno komunikatu o zdarzeniu (emsg) zapewnia sygnalizowania ogólnego zdarzeń związanych z czasem prezentacji nośnika. Jeśli jest obecny, dowolnego pola "emsg" jest umieszczana przed dowolnego pola "moof".

### <a name="dash-event-message-box-emsg"></a>Okno komunikatu zdarzenia DASH "emsg"
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

Pola DASHEventMessageBox zdefiniowano poniżej:

| **Nazwa pola**          | **Typ pola**          | **Wymagane?** | **Opis**                                                                                                                                                                                                                                                                                                                                                    |
|-------------------------|-------------------------|---------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| scheme_id_uri           | ciąg                  | Wymagane      | Identyfikuje system komunikatu. Schemat ma ustawioną wartość atrybutu schematu w polu manifestu serwera na żywo. Wartość powinna być URN lub adres URL identyfikujący system komunikatów; na przykład "urn: przykład: sygnalizowania: 1.0". Komunikaty [SCTE 35] trwa to specjalna wartość "urn: scte:scte35:2013a:bin", mimo że [SCTE 67] zaleca coś innego. |
| Wartość                   | ciąg                  | Wymagane      | Wartość dodatkowy ciąg używany przez właścicieli schematu w celu dostosowania semantyki wiadomości. W celu rozróżnienia wielu strumieni zdarzeń z tego samego schematu, wartość będzie ustawiana na nazwę strumienia zdarzeń (trackName dla Smooth pozyskiwania lub AMF komunikat nazwę RTMP pozyskiwania).                                                                  |
| skali czasu               | 32-bitowa liczba całkowita bez znaku | Wymagane      | Skali czasu, w impulsów na sekundę, czas i czas trwania pola w polu "emsg".                                                                                                                                                                                                                                                                        |
| Presentation_time_delta | 32-bitowa liczba całkowita bez znaku | Wymagane      | Nośnik różnica czasu prezentacji podczas prezentacji zdarzenia i najwcześniejszą godzinę prezentacji w tym segmencie. Prezentacja czas i czas trwania powinno odpowiadać z punktów dostępu strumienia (SAP) typu 1 lub 2, zgodnie z definicją w [ISO-14496-12] załącznika.                                                                                            |
| event_duration          | 32-bitowa liczba całkowita bez znaku | Wymagane      | Czas trwania zdarzenia lub 0xFFFFFFFF, aby określić Nieznany czas trwania.                                                                                                                                                                                                                                                                                          |
| Identyfikator                      | 32-bitowa liczba całkowita bez znaku | Wymagane      | Identyfikuje wystąpienie tej wiadomości. Komunikaty z semantyki równoważne mają taką samą wartość. Jeśli nie określono Identyfikatora, gdy komunikat jest pozyskanych, usługi Azure Media Services wygeneruje Unikatowy identyfikator.                                                                                                                                                    |
| Message_data            | Tablica bajtów              | Wymagane      | Komunikaty o zdarzeniach. Komunikatów [SCTE 35] danymi wiadomości jest splice_info_section() binarnego, chociaż [SCTE 67] zaleca coś innego.                                                                                                                                                                                                                                 |

### <a name="332-dash-message-handling"></a>3.3.2 kreska komunikat — Obsługa

Zdarzenia są sygnalizowane wewnątrzpasmowe, w polu "emsg" dla ścieżki audio i wideo.  Sygnalizowanie występuje na wszystkie segmenty żądania, dla których presentation_time_delta wynosi 15 sekund lub mniej. Po włączeniu metodzie przesuwanego okna prezentacji komunikaty o zdarzeniach są usuwane z MPD, gdy sumę czas i czas trwania komunikatu zdarzeń jest krótszy niż czas danych nośnika w manifeście.  Innymi słowy komunikaty o zdarzeniach są usuwane z manifestu, gdy czas nośników, do którego odwołuje się operacja wycofana poza metodzie przesuwanego okna prezentacji.

## <a name="4-scte-35-ingest"></a>4 SCTE 35 pozyskiwania

Komunikaty [SCTE 35] są pozyskanych w formacie binarnym przy użyciu schematu **"urn: scte:scte35:2013a:bin"** dla Smooth pozyskiwania i typ **"scte35"** dla protokołu RTMP pozyskiwania. Ułatwia konwersję chronometrażu [SCTE 35], który jest oparty na MPEG-2 transport stream prezentacji sygnatury czasowe (PTS), mapowanie między PTS (pts_time + pts_adjustment splice_time()) i oś czasu nośnika jest zapewniana przez (czas prezentacji zdarzeń pole fragment_absolute_time Smooth pozyskiwania i pozyskiwania pole czasu dla protokołu RTMP). Mapowanie jest niezbędne, ponieważ 33-bitową wartość PTS najedzie na około na 26,5 godzin.

Pozyskiwania Smooth Streaming wymaga, które musi zawierać pola danych (mdat) **splice_info_section()** zdefiniowany w tabeli 8-1 [SCTE 35]. Dla protokołu RTMP pozyskiwania, atrybut sygnalizacji komunikatu AMF jest ustawiony na base64encoded **splice_info_section()**. Jeśli komunikaty ma format opisany powyżej, są one wysyłane klientom HLS i Smooth, Dash z aktualizacjami [SCTE 67].


## <a name="5-references"></a>Odwołania do 5

**[SCTE 35]**  ANSI/SCTE 35 2013a — Program cyfrowe wstawiania kolejka wiadomości kabla, 2013a

**[SCTE 67]**  ANSI/SCTE 67 2014 — zalecana praktyka dla SCTE 35: Program cyfrowych wstawiania kolejka komunikatów kabla

**[DASH]**  Dynamicznie 2014 — technologii informatycznych — 23009 1 ISO/IEC adaptacyjne przesyłanie strumieniowe za pośrednictwem protokołu HTTP (kreska) — część 1: formatów multimediów prezentacji opis i segmentu, wydanie 2

**[HLS]**  ["HTTP transmisja strumieniowa na żywo", draft-pantos-http-live-streaming-14, 14 października 2014 r.](http://tools.ietf.org/html/draft-pantos-http-live-streaming-14)

**[MS-SSTR]**  ["Microsoft Smooth Streaming protokół", 15 maja 2014](http://download.microsoft.com/download/9/5/E/95EF66AF-9026-4BB0-A41D-A4F81802D92C/%5bMS-SSTR%5d.pdf)

**[AMF0]**  ["AMF0 Format komunikatu akcji"](http://download.macromedia.com/pub/labs/amf/amf0_spec_121207.pdf)

**[LIVE-FMP4]**  [Specyfikacji pozyskiwania MP4 pofragmentowane na żywo w usłudze azure Media Services](https://docs.microsoft.com/azure/media-services/media-services-fmp4-live-ingest-overview)

**[ISO-14496-12]**  ISO/IEC 14496-12: podstawowy nośnik ISO 12 części pliku w formacie, czwarty wersji 2012-07-15.

**[RTMP]**  ["Adobe w czasie rzeczywistym obsługi wiadomości protokołu", 21 grudnia 2012](https://www.adobe.com/devnet/rtmp.html) 

------------------------------------------

## <a name="next-steps"></a>Kolejne kroki
Ścieżki szkoleniowe dotyczące usługi Media widoku.

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
