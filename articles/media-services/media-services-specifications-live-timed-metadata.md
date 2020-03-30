---
title: Usługi Azure Media Services — sygnalizowanie metadanych czasowych w przesyłaniu strumieniowym na żywo
description: W tej specyfikacji opisano metody sygnalizacji metadanych czasowych podczas pozyskiwania i przesyłania strumieniowego do usługi Azure Media Services. Obejmuje to obsługę ogólnych sygnałów metadanych czasowych (ID3), a także sygnalizacji SCTE-35 do wstawiania reklam i sygnalizacji stanu splatania.
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
ms.date: 08/22/2019
ms.author: johndeu
ms.openlocfilehash: 551fb0cb9f3745a62d5d84f2c4878bbbbe5ad9a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79137326"
---
# <a name="signaling-timed-metadata-in-live-streaming"></a>Sygnalizowanie metadanych z czasem w transmisji strumieniowej na żywo 

Ostatnia aktualizacja: 2019-08-22

### <a name="conformance-notation"></a>Notacja zgodności

Słowa kluczowe "MUSI", "NIE WOLNO", "WYMAGANE", "NIE", "NIE POWINIEN", "NIE POWINIEN", "ZALECANE", "MAJ" i "FAKULTATYWNE" w niniejszym dokumencie należy interpretować w sposób opisany w RFC 2119

## <a name="1-introduction"></a>1. Wprowadzenie 

Aby zasygnalizować wstawianie reklam lub niestandardowych zdarzeń metadanych w odtwarzaczu klienckim, nadawcy często korzystają z terminowych metadanych osadzonych w filmie. Aby włączyć te scenariusze, usługa Media Services zapewnia obsługę transportu metadanych czasowych z punktu pozyskiwania kanału przesyłania strumieniowego na żywo do aplikacji klienckiej.
W tej specyfikacji przedstawiono kilka trybów, które są obsługiwane przez usługę Media Services dla metadanych czasowych w sygnałach przesyłania strumieniowego na żywo.

1. [SCTE-35] sygnalizując, że jest zgodny ze standardami określonymi przez [SCTE-35], [SCTE-214-1], [SCTE-214-3] i [RFC8216]

2. [SCTE-35] sygnalizując, że jest zgodny ze starszą specyfikacją [Adobe-Primetime] dla sygnalizacji reklam RTMP.
   
3. Ogólny tryb sygnalizacji metadanych z określonym czasem dla wiadomości, które **nie** są [SCTE-35] i mogą przenosić [ID3v2] lub inne niestandardowe schematy zdefiniowane przez dewelopera aplikacji.

## <a name="11-terms-used"></a>1.1 Użyte terminy

| Termin                | Definicja                                                                                                                                                                                                                                    |
| ------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Przerwa w reklamie            | Lokalizacja lub punkt w czasie, w którym jedna lub więcej reklam może być zaplanowana do emisji; tak samo jak możliwość skorzystania i umieszczenia.                                                                                                                     |
| Usługa podejmowania decyzji o reklamie | usługa zewnętrzna, która decyduje o tym, które reklamy i czas trwania będą wyświetlane użytkownikowi. Usługi są zazwyczaj świadczone przez partnera i są poza zakresem dla tego dokumentu.                                                                    |
| Cue                 | Wskazanie czasu i parametrów nadchodzącej przerwy reklamowej. Pamiętaj, że wskazówki mogą wskazywać na oczekujące przejście na przerwę reklamy, oczekujące na przełączenie na następną reklamę w przerwie reklamy i oczekujące na przejście z przerwy reklamy na główną treść.           |
| Packager            | Usługa Azure Media Services "Streaming Endpoint" zapewnia dynamiczne możliwości pakowania dash i HLS i jest określany jako "Packager" w branży mediów.                                                                              |
| Czas prezentacji   | Czas, w której zdarzenie jest prezentowane widzowi. Czas reprezentuje moment na osi czasu nośnika, że widz będzie zobaczyć zdarzenie. Na przykład czas prezentacji komunikatu polecenia SCTE-35 splice_info() jest splice_time(). |
| Godzina przyjazdu        | Czas, w której pojawia się komunikat o zdarzeniu. Czas jest zazwyczaj różni się od czasu prezentacji zdarzenia, ponieważ wiadomości o zdarzeniach są wysyłane przed czasem prezentacji zdarzenia.                                                    |
| Ścieżka rozrzedzona        | ścieżka nośnika, która nie jest ciągła i jest synchronizowana z czasem z ścieżką nadrzędną lub kontrolną.                                                                                                                                                  |
| Origin              | Usługa przesyłania strumieniowego multimediów platformy Azure                                                                                                                                                                                                             |
| Umywalka kanału        | Usługa przesyłania strumieniowego multimediów platformy Azure                                                                                                                                                                                                        |
| HLS                 | Protokół przesyłania strumieniowego na żywo apple HTTP                                                                                                                                                                                                            |
| Dash                | Dynamiczne adaptacyjne przesyłanie strumieniowe przez HTTP                                                                                                                                                                                                          |
| Gładkie              | Protokół płynnego przesyłania strumieniowego                                                                                                                                                                                                                     |
| MPEG2-TS            | Strumienie transportowe MPEG 2                                                                                                                                                                                                                      |
| RTMP                | Protokół multimedialny w czasie rzeczywistym                                                                                                                                                                                                                 |
| uimsbf              | Niepodpisana liczna liczna liczna, najpierw najważniejsza część.                                                                                                                                                                                                 |

---

## <a name="12-normative-references"></a>1.2 Referencje normatywne

Poniższe dokumenty zawierają przepisy, które poprzez odniesienie w niniejszym tekście stanowią przepisy niniejszego dokumentu. Wszystkie dokumenty podlegają rewizji przez organy normalizacyjne, a czytelnicy są zachęcani do zbadania możliwości zastosowania najnowszych wydań dokumentów wymienionych poniżej. Czytelnikom przypomina się również, że nowsze wersje dokumentów, do których istnieją odwołania, mogą nie być zgodne z tą wersją specyfikacji metadanych czasowych dla usługi Azure Media Services.


| Standardowa          | Definicja                                                                                                                                                                                                     |
| ----------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [Adobe-Primetime] | [Primetime Digital Program Wstawianie Specyfikacja sygnalizacji 1.2](https://www.adobe.com/content/dam/acom/en/devnet/primetime/PrimetimeDigitalProgramInsertionSignalingSpecification.pdf)                       |
| [Adobe-Flash-AS]  | [Odwołanie do języka flash actionscript](https://help.adobe.com/archive/en_US/as2/flashlite_2.x_3.x_aslr.pdf)                                                                                                   |
| [AMF0]            | ["Format komunikatu akcji AMF0"](https://download.macromedia.com/pub/labs/amf/amf0_spec_121207.pdf)                                                                                                              |
| [DASH-IF-IOP]     | DASH Industry Forum Interop Wytyczne v 4.2[https://dashif-documents.azurewebsites.net/DASH-IF-IOP/master/DASH-IF-IOP.html](https://dashif-documents.azurewebsites.net/DASH-IF-IOP/master/DASH-IF-IOP.html)    |
| [HLS-TMD]         | Metadane czasowe dla transmisji strumieniowej na żywo HTTP -[https://developer.apple.com/streaming](https://developer.apple.com/streaming)                                                                                        |
| [CMAF-ID3]        | [Metadane czasowe w formacie common media application (CMAF)](https://github.com/AOMediaCodec/id3-emsg)                                                                                                        |
| [ID3v2]           | ID3 Tag w wersji 2.4.0[http://id3.org/id3v2.4.0-structure](http://id3.org/id3v2.4.0-structure)                                                                                                                |
| [ISO-14496-12]    | ISO/IEC 14496-12: Część 12 Format pliku nośnika podstawowego ISO, FourthEdition 2012-07-15                                                                                                                                 |
| [MPEGDASH]        | Technologia informacyjna - Dynamiczne adaptacyjne przesyłanie strumieniowe za pośrednictwem protokołu HTTP (DASH) - Część 1: Opis prezentacji multimediów i formaty segmentów. maja 2014 r. Opublikowane. Adres url:https://www.iso.org/standard/65274.html         |
| [MPEGCMAF]        | Technologia informacyjna - Format aplikacji multimedialnych (MPEG-A) - Część 19: Wspólny format aplikacji multimedialnych (CMAF) dla nośników segmentowych. stycznia 2018 r. Opublikowane. Adres url:https://www.iso.org/standard/71975.html |
| [MPEGCENC]        | Technologia informacyjna - technologie systemów MPEG - Część 7: Wspólne szyfrowanie w plikach w formacie iso w formacie multimedialnym. lutego 2016 r. Opublikowane. Adres url:https://www.iso.org/standard/68042.html                   |
| [MS-SSTR]         | ["Microsoft Smooth Streaming Protocol", 15 maja 2014 r.](https://docs.microsoft.com/openspecs/windows_protocols/ms-sstr/8383f27f-7efe-4c60-832a-387274457251)                                                     |
| [MS-SSTR-Ingest]  | [Specyfikacja pochłoniętych plików MP4 w usłudze AZURE Media Services](https://docs.microsoft.com/azure/media-services/media-services-fmp4-live-ingest-overview)                                                      |
| [RFC8216]         | R. Pantos, wyd.; W. Maj. Transmisja na żywo http. sierpnia 2017 r. Informacyjne. [https://tools.ietf.org/html/rfc8216](https://tools.ietf.org/html/rfc8216)                                                            |
| [RFC4648]         | Kodowania danych Base16, Base32 i Base64 -[https://tools.ietf.org/html/rfc4648](https://tools.ietf.org/html/rfc4648)                                                                                     |
| [RTMP]            | ["Adobe's Real-Time Messaging Protocol", 21 grudnia 2012 r.](https://www.adobe.com/devnet/rtmp.html)                                                                                                            |
| [SCTE-35-2019]    | SCTE 35: 2019 - Digital Program Insertion Cueing Message for Cable -https://www.scte.org/SCTEDocs/Standards/ANSI_SCTE%2035%202019r1.pdf                                                                       |
| [SCTE-214-1]      | SCTE 214-1 2016 – MPEG DASH dla usług kablowych opartych na protokonach IP Część 1: Ograniczenia i rozszerzenia MPD                                                                                                                 |
| [SCTE-214-3]      | SCTE 214-3 2015 MPEG DASH dla usług kablowych opartych na protokoczowym cz.3: Profil DASH/FF                                                                                                                                  |
| [SCTE-224]        | SCTE 224 2018r1 – interfejs planowania zdarzeń i powiadomień                                                                                                                                                  |
| [SCTE-250]        | Interfejs API zarządzania zdarzeniami i sygnalizacją (ESAM)                                                                                                                                                                      |

---


## <a name="2-timed-metadata-ingest"></a>2. Czasowe połknienia metadanych

Usługa Azure Media Services obsługuje metadane w czasie rzeczywistym w czasie rzeczywistym dla protokołów [RTMP] i Smooth Streaming [MS-SSTR-INGEST]. Metadane w czasie rzeczywistym mogą służyć do definiowania zdarzeń niestandardowych za pomocą własnych unikatowych schematów niestandardowych (JSON, Binary, XML), a także formatów zdefiniowanych w branży, takich jak ID3 lub SCTE-35 do sygnalizacji reklam w strumieniu emisji. 

Ten artykuł zawiera szczegółowe informacje dotyczące sposobu wysyłania niestandardowych sygnałów metadanych z określonymi w czasie przy użyciu obsługiwanych protokołów pozyskiwania usługi Azure Media Services. W artykule wyjaśniono również, w jaki sposób manifesty dla HLS, DASH i Smooth Streaming są ozdobione sygnałami metadanych czasowych, a także jak są przenoszone w paśmie, gdy zawartość jest dostarczana przy użyciu segmentów CMAF (FRAGMENTY MP4) lub Transport Stream (TS) dla HLS. 

Typowe scenariusze przypadków użycia metadanych z terminami obejmują:

 - SCTE-35 sygnalizuje przerwy reklamowe w wydarzeniu na żywo lub transmisji liniowej
 - Metadane niestandardowego identyfikatora ID3, które mogą wyzwalać zdarzenia w aplikacji klienckiej (przeglądarka, iOS lub Android)
 - Niestandardowe zdefiniowane metadane JSON, Binary lub XML do wyzwalania zdarzeń w aplikacji klienckiej
 - Telemetria z kodera na żywo, kamery IP lub drona
 - Zdarzenia z kamery IP, takie jak ruch, wykrywanie twarzy itp.
 - Informacje o położeniu geograficznym z kamery akcji, drona lub poruszającego się urządzenia
 - Teksty piosenek
 - Granice programu na liniowym kanale na żywo
 - Obrazy lub rozszerzone metadane, które mają być wyświetlane w kanale na żywo
 - Wyniki sportowe lub informacje o zegarze gry
 - Interaktywne pakiety reklamowe wyświetlane obok wideo w przeglądarce
 - Quizy lub ankiety
  
Zdarzenia na żywo usługi Azure Media Services i packager są w stanie odbierać te sygnały metadanych i konwertować je na strumień metadanych, który może dotrzeć do aplikacji klienckich przy użyciu protokołów opartych na standardach, takich jak HLS i DASH.


## <a name="21-rtmp-timed-metadata"></a>2.1 Metadane z identyfikatorem RTMP

Protokół [RTMP] umożliwia przesyłanie sygnałów metadanych czasowych dla różnych scenariuszy, w tym metadanych niestandardowych i sygnałów reklamowych SCTE-35. 

Sygnały reklamowe (komunikaty sygnałowe) są wysyłane jako komunikaty wskazujące [AMF0] osadzone w strumieniu [RTMP]. Komunikaty sygnalizacji mogą być wysyłane jakiś czas przed wystąpieniem rzeczywistego zdarzenia lub sygnału splatania reklam [SCTE35]. Aby obspoprzez ten scenariusz, rzeczywista sygnatura czasowa prezentacji zdarzenia jest wysyłana w komunikacie sygnalizacji. Aby uzyskać więcej informacji, zobacz [AMF0].

Następujące polecenia [AMF0] są obsługiwane przez usługę Azure Media Services dla pozyskiwania RTMP:

- **onUserDataEvent** — używane dla metadanych niestandardowych lub metadanych czasowych [ID3v2]
- **onAdCue** - służy przede wszystkim do sygnalizowania możliwości umieszczenia reklamy w strumieniu na żywo. Obsługiwane są dwie formy sygnalizacji, tryb prosty i tryb "SCTE-35". 
- **onCuePoint** — obsługiwane przez niektóre lokalne kodery sprzętowe, takie jak koder Elemental Live, do sygnalizowania komunikatów [SCTE35]. 
  

W poniższej tabeli opisano format ładunku komunikatów AMF, który usługa Media Services będzie łatać dla trybów komunikatów "simple" i [SCTE35].

Nazwa komunikatu [AMF0] może służyć do rozróżniania wielu strumieni zdarzeń tego samego typu.  Zarówno w przypadku komunikatów [SCTE-35], jak i "prostego" i "prostego" nazwa komunikatu AMF MUSI być "onAdCue" zgodnie ze specyfikacją [Adobe-Primetime].  Wszystkie pola, które nie zostały wymienione poniżej, będą ignorowane przez usługę Azure Media Services przy pozyskiwania.

## <a name="211-rtmp-with-custom-metadata-using-onuserdataevent"></a>2.1.1 RTMP z niestandardowymi metadanymi przy użyciu "onUserDataEvent"

Jeśli chcesz podać niestandardowe źródła metadanych z kodera nadrzędnego, kamery IP, drona lub urządzenia przy użyciu protokołu RTMP, użyj polecenia wiadomości "onUserDataEvent" [AMF0].

Polecenie wiadomości danych **"onUserDataEvent"** MUSI zawierać ładunek wiadomości z następującą definicją, która ma zostać przechwycona przez usługę Media Services i zapakowana w format pliku w paśmie, a także manifesty dla HLS, DASH i Smooth Streaming.
Zaleca się wysyłanie komunikatów metadanych czasowych nie częściej niż raz na 0,5 sekundy (500ms) lub mogą wystąpić problemy ze stabilnością strumienia na żywo. Każda wiadomość może agregować metadane z wielu ramek, jeśli trzeba podać metadane na poziomie ramki. W przypadku wysyłania strumieni o wielu szybkościach transmisji bitów zaleca się również podanie metadanych na pojedynczej szybkości transmisji bitów tylko w celu zmniejszenia przepustowości i uniknięcia zakłóceń w przetwarzaniu wideo/audio. 

Ładunek dla **"onUserDataEvent"** powinien być komunikatem w formacie XML [MPEGDASH]. Ułatwia to przekazywanie w niestandardowych zdefiniowanych schematach, które mogą być przenoszone w ładunkach "emsg" w paśmie dla zawartości CMAF [MPEGCMAF], która jest dostarczana za przez protokoły HLS lub DASH. Każdy komunikat DASH Event Stream zawiera schemeIdUri, który działa jako identyfikator schematu wiadomości URN i definiuje ładunek wiadomości. Niektóre schematy,https://aomedia.org/emsg/ID3takie jak " " dla [ID3v2] lub **urn:scte35:2013:bin** dla [SCTE-35] są standaryzowane przez konsorcja branżowe w celu interoperacyjności. Każdy dostawca aplikacji może zdefiniować własny schemat niestandardowy przy użyciu adresu URL, który kontroluje (domena należąca do niego) i może podać specyfikację pod tym adresem URL, jeśli zechce. Jeśli gracz ma program obsługi dla zdefiniowanego schematu, jest to jedyny składnik, który musi zrozumieć ładunek i protokół.

Schemat ładunku XML [MPEG-DASH] EventStream jest zdefiniowany jako (fragment dash ISO-IEC-23009-1-3rd Edition). Należy zauważyć, że tylko jeden "EventType" na "EventStream" jest obsługiwany w tej chwili. Tylko pierwszy element **Zdarzenia** będą przetwarzane, jeśli wiele zdarzeń są podane w **EventStream**.

```xml
  <!-- Event Stream -->
  <xs:complexType name="EventStreamType">
    <xs:sequence>
      <xs:element name="Event" type="EventType" minOccurs="0" maxOccurs="unbounded"/>
      <xs:any namespace="##other" processContents="lax" minOccurs="0" maxOccurs="unbounded"/>
    </xs:sequence>
    <xs:attribute ref="xlink:href"/>
    <xs:attribute ref="xlink:actuate" default="onRequest"/>
    <xs:attribute name="schemeIdUri" type="xs:anyURI" use="required"/>
    <xs:attribute name="value" type="xs:string"/>
    <xs:attribute name="timescale" type="xs:unsignedInt"/>
  </xs:complexType>
  <!-- Event  -->
  <xs:complexType name="EventType">
    <xs:sequence>
      <xs:any namespace="##other" processContents="lax" minOccurs="0" maxOccurs="unbounded"/>
    </xs:sequence>
    <xs:attribute name="presentationTime" type="xs:unsignedLong" default="0"/>
    <xs:attribute name="duration" type="xs:unsignedLong"/>
    <xs:attribute name="id" type="xs:unsignedInt"/>
    <xs:attribute name="contentEncoding" type="ContentEncodingType"/>
    <xs:attribute name="messageData" type="xs:string"/>
    <xs:anyAttribute namespace="##other" processContents="lax"/>
  </xs:complexType>
```


### <a name="example-xml-event-stream-with-id3-schema-id-and-base64-encoded-data-payload"></a>Przykładowy strumień zdarzeń XML o identyfikatorze schematu ID3 i ładunku danych zakodowanym w formacie base4.  
```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <EventStream schemeIdUri="https://aomedia.org/emsg/ID3">
         <Event contentEncoding="Base64">
          -- base64 encoded ID3v2 full payload here per [CMAF-TMD] --
         </Event>
   <EventStream>
```

### <a name="example-event-stream-with-custom-schema-id-and-base64-encoded-binary-data"></a>Przykładowy strumień zdarzeń z niestandardowym identyfikatorem schematu i danymi binarnymi zakodowanymi w formacie base4  
```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <EventStream schemeIdUri="urn:example.org:custom:binary">
         <Event contentEncoding="Base64">
          -- base64 encoded custom binary data message --
         </Event>
   <EventStream>
```

### <a name="example-event-stream-with-custom-schema-id-and-custom-json"></a>Przykładowy strumień zdarzeń z niestandardowym identyfikatorem schematu i niestandardowym jsonem  
```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <EventStream schemeIdUri="urn:example.org:custom:JSON">
         <Event>
          [
            {"key1" : "value1"},
            {"key2" : "value2"}
          ]
         </Event>
   <EventStream>
```

### <a name="built-in-supported-scheme-id-uris"></a>Wbudowane obsługiwane identyfikatory identyfikatorów URI systemu
| Identyfikator identyfikatora schematu                 | Opis                                                                                                                                                                                                                                          |
| ----------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| https:\//aomedia.org/emsg/ID3 | W tym artykule opisano, jak metadane [ID3v2] mogą być przenoszone jako metadane czasowe w mp4 pofragmentowanym formacie MP4 zgodnym z cmaf [MPEGCMAF]. Aby uzyskać więcej informacji, zobacz [metadane czasowe w formacie common media application (CMAF)](https://github.com/AOMediaCodec/id3-emsg) |

### <a name="event-processing-and-manifest-signaling"></a>Przetwarzanie zdarzeń i sygnalizacja manifestu

Po otrzymaniu prawidłowego zdarzenia **"onUserDataEvent"** usługa Azure Media Services będzie szukać prawidłowego ładunku XML zgodnego z eventstreamtypem (zdefiniowanym w programie [MPEGDASH] ), przeanalizować ładunek XML i przekonwertować go na pole 'emsg fragmentu MP4 [MPEGCMAF] w wersji 1 do przechowywania w archiwum na żywo i przesyłania do pakietu Usług multimedialnych.   Packageer wykryje pole 'emsg' w transmisji na żywo i:

- a) "dynamicznie pakować" go do segmentów TS w celu dostarczenia klientom HLS zgodnie ze specyfikacją metadanych HLS czasowych [HLS-TMD], lub
- b) przekazać go do dostawy we fragmentach CMAF za pośrednictwem HLS lub DASH, lub 
- (c) przekształcić go w rzadki sygnał toru do dostarczenia za pośrednictwem Smooth Streaming [MS-SSTR].

Oprócz pakietów CMAF lub SES PES w formacie "emsg" w paśmie dla HLS, manifesty dash (MPD) i Smooth Streaming będą zawierać odniesienie do strumieni zdarzeń w paśmie (znanych również jako rzadka ścieżka strumienia w Smooth Streaming). 

Poszczególne zdarzenia lub ich ładunki danych nie są dane wyjściowe bezpośrednio w manifestach HLS, DASH lub Smooth. 

### <a name="additional-informational-constraints-and-defaults-for-onuserdataevent-events"></a>Dodatkowe ograniczenia informacyjne i wartości domyślne dla zdarzeń zdarzenia onUserDataEvent

- Jeśli skala czasu nie jest ustawiona w elemencie EventStream, skala czasu RTMP 1 kHz jest używana domyślnie
- Dostarczanie wiadomości onUserDataEvent jest ograniczona do raz na 500ms max. Częste wysyłanie zdarzeń może mieć wpływ na przepustowość i stabilność transmisji na żywo.

## <a name="212-rtmp-ad-cue-signaling-with-onadcue"></a>2.1.2 Sygnalizacja sygnalizacji reklamy RTMP z "onAdCue"

Usługa Azure Media Services może nasłuchiwać i odpowiadać na kilka typów komunikatów [AMF0], które mogą służyć do sygnalizowania różnych zsynchronizowanych metadanych w czasie rzeczywistym w strumieniu na żywo.  Specyfikacja [Adobe-Primetime] definiuje dwa typy sygnałów o nazwie "prosty" i "SCTE-35". W trybie "prosty" usługa Media Services obsługuje pojedynczy komunikat sygnalizacji AMF o nazwie "onAdCue" przy użyciu ładunku zgodnego z poniższą tabelą zdefiniowaną dla sygnału "Tryb prosty".  

W poniższej sekcji przedstawiono ładunek "prosty" w trybie RTMP, który może być używany do sygnalizowania podstawowego sygnału reklamowego "spliceOut", który zostanie przeniesiony do manifestu klienta dla HLS, DASH i Microsoft Smooth Streaming. Jest to bardzo przydatne w scenariuszach, w których klient nie ma złożonego systemu sygnalizacji reklamowej scte-35 opartego na reklamie lub wstawianiu i używa podstawowego kodera lokalnego do wysyłania wiadomości za pośrednictwem interfejsu API. Zazwyczaj koder lokalny będzie obsługiwać interfejs API oparty na rest, aby wyzwolić ten sygnał, który będzie również "splice-condition" strumienia wideo, wstawiając ramkę IDR do wideo i uruchamianie nowego GOP.

## <a name="213--rtmp-ad-cue-signaling-with-onadcue---simple-mode"></a>2.1.3 Sygnalizacja sygnalizacji reklamy RTMP z "onAdCue" - Tryb prosty

| Nazwa pola | Typ pola | Wymagana? | Opisy                                                                                                                                                                                                                                                                        |
| ---------- | ---------- | --------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| type       | Ciąg     | Wymagany  | Komunikat o zdarzeniu.  Musi być "SpliceOut", aby wyznaczyć prosty tryb splice.                                                                                                                                                                                                         |
| id         | Ciąg     | Wymagany  | Unikatowy identyfikator opisujący splot lub segment. Identyfikuje to wystąpienie wiadomości                                                                                                                                                                                       |
| czas trwania   | Liczba     | Wymagany  | Czas trwania splice. Jednostki są ułamkowe sekundy.                                                                                                                                                                                                                           |
| elapsed    | Liczba     | Optional (Opcjonalność)  | Gdy sygnał jest powtarzany w celu obsługi dostrojenia, pole to jest czasem prezentacji, który upłynął od rozpoczęcia spania. Jednostki są ułamkowe sekundy. W trybie prostym wartość ta nie powinna przekraczać pierwotnego czasu trwania splice. |
| time       | Liczba     | Wymagany  | Jest to czas splice, w czasie prezentacji. Jednostki są ułamkowe sekundy.                                                                                                                                                                                                |

---
 
#### <a name="example-mpeg-dash-manifest-output-when-using-adobe-rtmp-simple-mode"></a>Przykładowy wyjście manifestu MPEG DASH podczas korzystania z trybu prostego Adobe RTMP

Zobacz przykład [3.3.2.1 MPEG DASH .mpd EventStream przy użyciu trybu prostego Adobe](#3321-example-mpeg-dash-mpd-manifest-signaling-of-rtmp-streaming-using-adobe-simple-mode)

Zobacz przykład [3.3.3.1 DASH manifest z pojedynczym okresem i Adobe tryb prosty](#3331-example-mpeg-dash-manifest-mpd-with-single-period-eventstream-using-adobe-simple-mode-signals)

#### <a name="example-hls-manifest-output-when-using-adobe-rtmp-simple-mode"></a>Przykładowe wyjście manifestu HLS podczas korzystania z trybu prostego Adobe RTMP

Zobacz przykład [3.2.2 Manifest HLS przy użyciu trybu prostego Adobe i tagu EXT-X-CUE](#322-apple-hls-with-adobe-primetime-ext-x-cue-legacy)

## <a name="214-rtmp-ad-cue-signaling-with-onadcue---scte-35-mode"></a>2.1.4 Sygnalizacja sygnalizacji reklamy RTMP z "onAdCue" - Tryb SCTE-35

Podczas pracy z bardziej zaawansowanym przepływem pracy produkcji emisji, który wymaga, aby pełny komunikat o ładunku SCTE-35 był przenoszony do manifestu HLS lub DASH, najlepiej jest użyć "trybu SCTE-35" specyfikacji [Adobe-Primetime].  Ten tryb obsługuje sygnały SCTE-35 w paśmie wysyłanym bezpośrednio do lokalnego kodera na żywo, który następnie koduje sygnały do strumienia RTMP przy użyciu "trybu SCTE-35" określonego w specyfikacji [Adobe-Primetime]. 

Zazwyczaj komunikaty SCTE-35 mogą pojawiać się tylko w wejściach strumienia transportu MPEG-2 (TS) na koderze lokalnym. Skontaktuj się z producentem kodera, aby uzyskać szczegółowe informacje na temat konfigurowania pozyskiwania strumienia transportu zawierającego SCTE-35 i włączania go do przejścia do RTMP w trybie Adobe SCTE-35.

W tym scenariuszu następujące ładunek MUSI być wysyłany z kodera lokalnego przy użyciu typu komunikatu **"onAdCue"** [AMF0].

| Nazwa pola | Typ pola | Wymagana? | Opisy                                                                                                                                                                                                                                                                                                                                                                                                        |
| ---------- | ---------- | --------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Cue        | Ciąg     | Wymagany  | Komunikat o zdarzeniu.  W przypadku komunikatów [SCTE-35] musi to być kodowany base64 [RFC4648] splice_info_section() w celu wysyłania wiadomości do klientów HLS, Smooth i Dash.                                                                                                                                                                                                                               |
| type       | Ciąg     | Wymagany  | Urn lub adres URL identyfikujący schemat wiadomości. W przypadku wiadomości [SCTE-35] **powinno** to być **"scte35",** aby wiadomości były wysyłane do klientów HLS, Smooth i Dash, zgodnie z [Adobe-Primetime]. Opcjonalnie urna "urn:scte:scte35:2013:bin" może być również używana do sygnalizowania komunikatu [SCTE-35].                                                                                                        |
| id         | Ciąg     | Wymagany  | Unikatowy identyfikator opisujący splot lub segment. Identyfikuje to wystąpienie wiadomości.  Wiadomości z równoważną semantyką mają taką samą wartość.                                                                                                                                                                                                                                                       |
| czas trwania   | Liczba     | Wymagany  | Czas trwania zdarzenia lub segmentu splatanego reklam, jeśli jest znany. Jeśli nie jest znana, wartość **powinna wynosić** 0.                                                                                                                                                                                                                                                                                                                    |
| elapsed    | Liczba     | Optional (Opcjonalność)  | Po powtórzeniu sygnału reklamowego [SCTE-35] w celu dostrojenia się, pole to jest czasem prezentacji, jaki upłynął od rozpoczęcia spania. Jednostki są ułamkowe sekundy. W trybie [SCTE-35] wartość ta może przekroczyć oryginalny określony czas trwania splice lub segmentu.                                                                                                                   |
| time       | Liczba     | Wymagany  | Czas prezentacji wydarzenia lub splatanych reklam.  Czas prezentacji i czas trwania **powinny być** zgodne z punktami dostępu strumieniowego (SAP) typu 1 lub 2, zgodnie z definicją zawartą w załączniku I [ISO-14496-12]. W przypadku wyjścia HLS czas i czas trwania **powinny być** wyrównane z granicami segmentu. Czas prezentacji i czas trwania różnych komunikatów zdarzeń w ramach tego samego strumienia zdarzeń NIE MOGĄ się nakładać. Jednostki są ułamkowe sekundy. |

---

#### <a name="example-mpeg-dash-mpd-manifest-with-scte-35-mode"></a>Przykładowy manifest MPEG DASH .mpd w trybie SCTE-35
Patrz [Sekcja 3.3.3.2 Przykład manifestu DASH z SCTE-35](#3332-example-mpeg-dash-manifest-mpd-with-multi-period-eventstream-using-adobe-scte35-mode-signaling)

#### <a name="example-hls-manifest-m3u8-with-scte-35-mode-signal"></a>Przykładowy manifest HLS .m3u8 z sygnałem trybu SCTE-35
Patrz [sekcja 3.2.1.1.1 przykład manifest HLS z SCTE-35](#3211-example-hls-manifest-m3u8-showing-ext-x-daterange-signaling-of-scte-35)

## <a name="215-rtmp-ad-signaling-with-oncuepoint-for-elemental-live"></a>2.1.5 Sygnalizacja reklamy RTMP z "onCuePoint" dla Elemental Live

Koder Żywicieli żywej obsługuje znaczniki reklam w sygnale RTMP. Usługa Azure Media Services obsługuje obecnie tylko typ znacznika reklamy "onCuePoint" dla RTMP.  Można to włączyć w ustawieniach grupy Adobe RTMP w ustawieniach kodera Elemental Media Live lub interfejsie API, ustawiając "**ad_markers**" na "onCuePoint".  Szczegółowe informacje można znaleźć w dokumentacji Elemental Live. Włączenie tej funkcji w grupie RTMP spowoduje przekazanie sygnałów SCTE-35 do wyjść RTMP firmy Adobe do przetworzenia przez usługę Azure Media Services.

Typ komunikatu "onCuePoint" jest zdefiniowany w programie [Adobe-Flash-AS] i ma następującą strukturę ładunku wysyłanego z danych wyjściowych RTMP na żywo elementarnej.


| Właściwość   | Opis                                                                                                                                                                                                                     |
| ---------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| name       | Nazwa powinna być '**scte35**' przez Elemental Live.                                                                                                                                                                              |
| time       | Czas w sekundach, w którym wystąpił punkt sygnalizacji w pliku wideo podczas osi czasu                                                                                                                                           |
| type       | Typ punktu sygnalizacji powinien być ustawiony na "**zdarzenie**".                                                                                                                                                                             |
| parameters | Tablica zespolona ciągów pary nazwa/wartość zawierająca informacje z komunikatu SCTE-35, w tym identyfikator i czas trwania. Te wartości są analizowane przez usługę Azure Media Services i zawarte w znaczniku dekoracji manifestu. |


Gdy używany jest ten tryb znacznika reklamy, dane wyjściowe manifestu HLS są podobne do trybu Adobe "Simple".


#### <a name="example-mpeg-dash-mpd-single-period-adobe-simple-mode-signals"></a>Przykład mpeg DASH MPD, pojedynczy okres, sygnały adobe w trybie prostym

~~~ xml
<?xml version="1.0" encoding="utf-8"?>
<MPD xmlns="urn:mpeg:dash:schema:mpd:2011" 
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" profiles="urn:mpeg:dash:profile:isoff-live:2011" type="dynamic" publishTime="2020-01-07T18:58:03Z" minimumUpdatePeriod="PT0S" timeShiftBufferDepth="PT58M56S" availabilityStartTime="2020-01-07T17:44:47Z" minBufferTime="PT7S">
    <Period start="PT0S">
        <EventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="scte35" timescale="10000000">
            <Event presentationTime="1583497601000000" duration="300000000" id="1085900"/>
            <Event presentationTime="1583500901666666" duration="300000000" id="1415966"/>
            <Event presentationTime="1583504202333333" duration="300000000" id="1746033"/>
            <Event presentationTime="1583507502666666" duration="300000000" id="2076066"/>
            <Event presentationTime="1583510803333333" duration="300000000" id="2406133"/>
            <Event presentationTime="1583514104000000" duration="300000000" id="2736200"/>
            <Event presentationTime="1583517404666666" duration="300000000" id="3066266"/>
            <Event presentationTime="1583520705333333" duration="300000000" id="3396333"/>
            <Event presentationTime="1583524006000000" duration="300000000" id="3726400"/>
            <Event presentationTime="1583527306666666" duration="300000000" id="4056466"/>
            <Event presentationTime="1583530607333333" duration="300000000" id="4386533"/>
        </EventStream>
        <AdaptationSet id="1" group="1" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="video" mimeType="video/mp4" codecs="avc1.4D400C" maxWidth="256" maxHeight="144" startWithSAP="1">
            <InbandEventStream schemeIdUri="urn:mpeg:dash:event:2012" value="1"/>
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="scte35"/>
            <SegmentTemplate timescale="10000000" presentationTimeOffset="1583486678426666" media="QualityLevels($Bandwidth$)/Fragments(video=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(video=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="1583495318000000" d="64000000" r="34"/>
                    <S d="43000000"/>
                    <S d="21000000"/>
                    <!-- ... Truncated for brevity of sample-->

                </SegmentTimeline>
            </SegmentTemplate>
            <ProducerReferenceTime id="1583495318000000" type="0" wallClockTime="2020-01-07T17:59:10.957Z" presentationTime="1583495318000000"/>
            <Representation id="1_V_video_3750956353252827751" bandwidth="149952" width="256" height="144"/>
        </AdaptationSet>
        <AdaptationSet id="2" group="5" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="audio" mimeType="audio/mp4" codecs="mp4a.40.2" lang="en">
            <InbandEventStream schemeIdUri="urn:mpeg:dash:event:2012" value="1"/>
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="scte35"/>
            <Label>ambient</Label>
            <SegmentTemplate timescale="10000000" presentationTimeOffset="1583486678426666" media="QualityLevels($Bandwidth$)/Fragments(ambient=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(ambient=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="1583495254426666" d="64000000" r="35"/>
                    <S d="43093334"/>
                    <S d="20906666"/>
                    <!-- ... Truncated for brevity of sample-->

                </SegmentTimeline>
            </SegmentTemplate>
            <ProducerReferenceTime id="1583495254426666" type="0" wallClockTime="2020-01-07T17:59:04.600Z" presentationTime="1583495254426666"/>
            <Representation id="5_A_ambient_9125670592623055209" bandwidth="96000" audioSamplingRate="48000"/>
        </AdaptationSet>
    </Period>
</MPD>
~~~

#### <a name="example-hls-playlist-adobe-simple-mode-signals-using-ext-x-cue-tag-truncated--for-brevity"></a>Przykładowa lista odtwarzania HLS, sygnały trybu prostego Adobe za pomocą znacznika EXT-X-CUE (obcięty "..." zwięzłości)

Poniższy przykład przedstawia dane wyjściowe z pakietu pakietowego dynamicznego usługi Media Services dla strumienia pozyskiwania RTMP przy użyciu "prostych" sygnałów trybu Adobe i starszego tagu [Adobe-Primetime] EXT-X-CUE.  

~~~
#EXTM3U
#EXT-X-VERSION:8
#EXT-X-MEDIA-SEQUENCE:0
#EXT-X-TARGETDURATION:7
#EXT-X-INDEPENDENT-SEGMENTS
#EXT-X-PROGRAM-DATE-TIME:2020-01-07T17:44:47Z
#EXTINF:6.400000,no-desc
Fragments(video=1583486742000000,format=m3u8-aapl-v8)
#EXTINF:6.400000,no-desc
Fragments(video=1583486806000000,format=m3u8-aapl-v8)
...
#EXTINF:6.166667,no-desc
Fragments(video=1583487638000000,format=m3u8-aapl-v8)
#EXT-X-CUE:ID=95766,TYPE="SpliceOut",DURATION=30.000000,TIME=158348769.966667
#EXTINF:0.233333,no-desc
Fragments(video=1583487699666666,format=m3u8-aapl-v8)
#EXT-X-CUE:ID=95766,TYPE="SpliceOut",DURATION=30.000000,TIME=158348769.966667,ELAPSED=0.233333
#EXTINF:6.400000,no-desc
Fragments(video=1583487702000000,format=m3u8-aapl-v8)
#EXT-X-CUE:ID=95766,TYPE="SpliceOut",DURATION=30.000000,TIME=158348769.966667,ELAPSED=6.633333
#EXTINF:6.400000,no-desc
Fragments(video=1583487766000000,format=m3u8-aapl-v8)
#EXT-X-CUE:ID=95766,TYPE="SpliceOut",DURATION=30.000000,TIME=158348769.966667,ELAPSED=13.033333
#EXTINF:6.400000,no-desc
Fragments(video=1583487830000000,format=m3u8-aapl-v8)
#EXT-X-CUE:ID=95766,TYPE="SpliceOut",DURATION=30.000000,TIME=158348769.966667,ELAPSED=19.433333
#EXTINF:6.400000,no-desc
Fragments(video=1583487894000000,format=m3u8-aapl-v8)
#EXT-X-CUE:ID=95766,TYPE="SpliceOut",DURATION=30.000000,TIME=158348769.966667,ELAPSED=25.833333
#EXTINF:4.166667,no-desc
Fragments(video=1583487958000000,format=m3u8-aapl-v8)
#EXTINF:2.233333,no-desc
Fragments(video=1583487999666666,format=m3u8-aapl-v8)
#EXTINF:6.400000,no-desc
Fragments(video=1583488022000000,format=m3u8-aapl-v8)
...

~~~

### <a name="216-cancellation-and-updates"></a>2.1.6 Anulowanie i aktualizacje

Wiadomości można anulować lub zaktualizować, wysyłając wiele wiadomości o tym samym czasie prezentacji i identyfikatorze. Czas prezentacji i identyfikator jednoznacznie identyfikują zdarzenie, a ostatnia wiadomość odebrana dla określonego czasu prezentacji, który spełnia ograniczenia przed rolką, jest komunikatem, który jest uruchamiany. Zaktualizowane zdarzenie zastępuje wszystkie wcześniej odebrane wiadomości. Ograniczenie przed toczenia wynosi cztery sekundy. Wiadomości odebrane co najmniej cztery sekundy przed czasem prezentacji zostaną zrealizowane.

## <a name="22-fragmented-mp4-ingest-smooth-streaming"></a>2.2 Rozdrobniony mp4 ingest (płynne przesyłanie strumieniowe)

Informacje na temat wymagań dotyczących pozyskiwania strumienia na żywo można znaleźć w usłudze [MS-SSTR-INGEST]. W poniższych sekcjach podano szczegółowe informacje dotyczące pozyskiwania metadanych prezentacji z odpowiednim czasem.  Metadane prezentacji z czasem są połykane jako ścieżka rozrzedzona, która jest zdefiniowana zarówno w polu manifestu serwera na żywo (zobacz MS-SSTR), jak i w polu filmu ("moov").  

Każdy fragment rozrzedzony składa się z pola fragmentu filmu ("moof") i skrzynki danych multimedialnych ('mdat'), gdzie pole 'mdat' jest wiadomością binarną.

W celu uzyskania dokładnego wstawiania reklam, koder MUSI podzielić fragment w czasie prezentacji, w którym sygnał jest wymagany do wstawienia.  Należy utworzyć nowy fragment, który rozpoczyna się od nowo utworzonej ramki IDR lub punktów dostępu do strumienia (SAP) typu 1 lub 2, zgodnie z definicją zawartą w załączniku I [ISO-14496-12]. Dzięki temu pakiet azure media do prawidłowego generowania manifestu HLS i DASH manifest wielooklatowy, gdzie nowy okres rozpoczyna się w klatce dokładne splot uwarunkowane czas prezentacji.

### <a name="221-live-server-manifest-box"></a>2.2.1 Pole manifestu serwera na żywo

Ścieżka rozrzedzona **MUSI** być zadeklarowana w polu Manifest serwera na żywo z wpisem ** \<\> strumienia tekstu** i **musi** mieć ustawiony następujący zestaw atrybutów:

| **Nazwa atrybutu** | **Typ pola** | **Wymagane?** | **Opis**                                                                                                                                                                                                              |
| ------------------ | -------------- | ------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| systemBitrate      | Liczba         | Wymagany      | **MUSI** być "0", wskazujący ścieżkę o nieznanej, zmiennej szybkości transmisji bitów.                                                                                                                                                          |
| nazwa śledzenia nadrzędnego    | Ciąg         | Wymagany      | **MUSI** być nazwą ścieżki nadrzędnej, do której rozrzedzone kody czasu ścieżki są wyrównane. Ścieżka nadrzędna nie może być ścieżką rozrzedzona.                                                                             |
| manifestOutput     | Wartość logiczna        | Wymagany      | **MUSI** być "true", aby wskazać, że ścieżka rozrzedzone zostaną osadzone w smooth manifestu klienta.                                                                                                                        |
| Podtypu            | Ciąg         | Wymagany      | **MUSI** być czteroznakowym kodem "DATA".                                                                                                                                                                                  |
| Schemat             | Ciąg         | Wymagany      | **MUSI** być urn lub adres URL identyfikujący schemat wiadomości. W przypadku wiadomości [SCTE-35] **musi** to być "urn:scte:scte35:2013:bin", aby wiadomości były wysyłane do klientów HLS, Smooth i Dash zgodnie z [SCTE-35]. |
| nazwa utworu          | Ciąg         | Wymagany      | **MUSI** być nazwą toru rozrzedzone. TrackName może służyć do rozróżniania wielu strumieni zdarzeń z tego samego schematu. Każdy **unikatowy** strumień zdarzeń MUSI mieć unikatową nazwę ścieżki.                                |
| skala czasu          | Liczba         | Optional (Opcjonalność)      | **MUSI** być skalą czasu ścieżki nadrzędnej.                                                                                                                                                                               |

---

### <a name="222-movie-box"></a>2.2.2 Skrzynka filmowa

Pole filmu ("moov") jest zgodne z polem manifestu serwera na żywo jako część nagłówka strumienia dla rzadkiej ścieżki.

Pole "moov" **powinno** zawierać pole **TrackHeaderBox ('tkhd')** zdefiniowane w [ISO-14496-12] z następującymi ograniczeniami:

| **Nazwa pola** | **Typ pola**          | **Wymagane?** | **Opis**                                                                                                    |
| -------------- | ----------------------- | ------------- | ------------------------------------------------------------------------------------------------------------------ |
| czas trwania       | 64-bitowa niepodpisana całkowitej liczby | Wymagany      | **Powinna wynosić** 0, ponieważ pole toru ma zero próbek, a całkowity czas trwania próbek w polu ścieżki wynosi 0. |

---

Pole "moov" **powinno** zawierać **pole HandlerBox ("hdlr")** zdefiniowane w [ISO-14496-12] z następującymi ograniczeniami:

| **Nazwa pola** | **Typ pola**          | **Wymagane?** | **Opis**       |
| -------------- | ----------------------- | ------------- | --------------------- |
| handler_type   | 32-bitowa niepodpisana gnilizna | Wymagany      | **POWINNY** być "meta". |

---

Pole 'stsd' **POWINNO** zawierać pole MetaDataSampleEntry z nazwą kodowania zdefiniowaną w [ISO-14496-12].  Na przykład dla wiadomości SCTE-35 nazwa kodowania **powinna** być "scte".

### <a name="223-movie-fragment-box-and-media-data-box"></a>2.2.3 Skrzynka fragmentu filmu i skrzynka danych multimediów

Fragmenty ścieżek sparse składają się z pola fragmentu filmu ("moof") i skrzynki danych multimedialnych ("mdat").

> [!NOTE]
> W celu uzyskania dokładnego wstawiania reklam, koder MUSI podzielić fragment w czasie prezentacji, w którym sygnał jest wymagany do wstawienia.  Należy utworzyć nowy fragment, który rozpoczyna się od nowo utworzonej ramki IDR lub punktów dostępu do strumienia (SAP) typu 1 lub 2, zgodnie z definicją w załączniku I [ISO-14496-12]
> 

Pole MovieFragmentBox ('moof') **MUSI** zawierać pole **TrackFragmentExtendedHeaderBox ('uuid')** zdefiniowane w [MS-SSTR] z następującymi polami:

| **Nazwa pola**         | **Typ pola**          | **Wymagane?** | **Opis**                                                                                           |
| ---------------------- | ----------------------- | ------------- | --------------------------------------------------------------------------------------------------------- |
| fragment_absolute_time | 64-bitowa niepodpisana całkowitej liczby | Wymagany      | **MUSI** to być czas przybycia wydarzenia. Ta wartość wyrównuje wiadomość z ścieżką nadrzędną.           |
| fragment_duration      | 64-bitowa niepodpisana całkowitej liczby | Wymagany      | **MUSI** być czas trwania zdarzenia. Czas trwania może być zero, aby wskazać, że czas trwania jest nieznany. |

---


Pole MediaDataBox ('mdat') **MUSI** mieć następujący format:

| **Nazwa pola**          | **Typ pola**                   | **Wymagane?** | **Opis**                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| ----------------------- | -------------------------------- | ------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| version                 | 32-bitowa niepodpisana kreśleń całkowitych (uimsbf) | Wymagany      | Określa format zawartości pola "mdat". Nierozpoznane wersje zostaną zignorowane. Obecnie jedyną obsługiwani wersją jest 1.                                                                                                                                                                                                                                                                                                                                                                      |
| id                      | 32-bitowa niepodpisana kreśleń całkowitych (uimsbf) | Wymagany      | Identyfikuje to wystąpienie wiadomości. Komunikaty z równoważną semantyką mają tę samą wartość; oznacza to, że przetwarzanie dowolnego okna komunikatu zdarzenia o tym samym identyfikatorze jest wystarczające.                                                                                                                                                                                                                                                                                                                            |
| presentation_time_delta | 32-bitowa niepodpisana kreśleń całkowitych (uimsbf) | Wymagany      | Suma fragment_absolute_time, określona w TrackFragmentExtendedHeaderBox i presentation_time_delta **MUSI** być czas prezentacji zdarzenia. Czas prezentacji i czas trwania **powinny być** zgodne z punktami dostępu strumieniowego (SAP) typu 1 lub 2, zgodnie z definicją zawartą w załączniku I [ISO-14496-12]. W przypadku wyjścia HLS czas i czas trwania **powinny być** wyrównane z granicami segmentu. Czas prezentacji i czas trwania różnych komunikatów zdarzeń w ramach tego samego strumienia zdarzeń **NIE MOGĄ** się nakładać. |
| message                 | tablica bajtów                       | Wymagany      | Komunikat o zdarzeniu. W przypadku wiadomości [SCTE-35] wiadomość jest splice_info_section(). W przypadku wiadomości [SCTE-35] **musi** to być splice_info_section() w celu wysyłania wiadomości do klientów HLS, Smooth i Dash zgodnie z [SCTE-35]. W przypadku komunikatów [SCTE-35] binarny splice_info_section() jest ładunkiem pola 'mdat' i **nie** jest zakodowany base64.                                                                                                                     |

---


### <a name="224-cancellation-and-updates"></a>2.2.4 Anulowanie i aktualizacje

Wiadomości można anulować lub zaktualizować, wysyłając wiele wiadomości o tym samym czasie prezentacji i identyfikatorze.  Czas prezentacji i identyfikator jednoznacznie identyfikują zdarzenie. Ostatnia wiadomość odebrana dla określonego czasu prezentacji, który spełnia ograniczenia przed rolką, jest komunikat, który jest uruchamiany. Zaktualizowana wiadomość zastępuje wszystkie wcześniej odebrane wiadomości.  Ograniczenie przed toczenia wynosi cztery sekundy. Wiadomości odebrane co najmniej cztery sekundy przed czasem prezentacji zostaną zrealizowane. 


## <a name="3-timed-metadata-delivery"></a>3 Dostarczanie metadanych z czasem

Dane strumienia zdarzeń są nieprzezroczyste dla usługi Media Services. Usługa Media Services przekazuje tylko trzy informacje między punktu końcowego pozyskiwania i punktu końcowego klienta. Następujące właściwości są dostarczane do klienta, zgodnie z [SCTE-35] i/lub protokołem przesyłania strumieniowego klienta:

1.  Schemat – urn lub adres URL identyfikujący schemat wiadomości.
2.  Czas prezentacji – czas prezentacji wydarzenia na osi czasu nośnika.
3.  Czas trwania – czas trwania wydarzenia.
4.  ID – opcjonalny unikatowy identyfikator zdarzenia.
5.  Komunikat — dane zdarzenia.

## <a name="31-microsoft-smooth-streaming-manifest"></a>3.1 Manifest płynnego przesyłania strumieniowego firmy Microsoft  

Szczegółowe informacje na temat formatowania ścieżki komunikatów rozrzedzonych można znaleźć w serwisie [MS-SSTR]. W przypadku komunikatów [SCTE35] funkcja Płynne przesyłanie strumieniowe spowoduje, że splice_info_section() zakodowane w bazie danych zostanie wyeksponowane w rozrzedzony fragment.
StreamIndex **musi** mieć podtyp "DATA", a atrybuty CustomAttributes **muszą** zawierać atrybut z Name="Schema" i Value="urn:scte:scte35:2013:bin".

#### <a name="smooth-client-manifest-example-showing-base64-encoded-scte35-splice_info_section"></a>Przykład manifestu gładkiego klienta przedstawiający kodowany base64 [SCTE35] splice_info_section()
~~~ xml
<?xml version="1.0" encoding="utf-8"?>
<SmoothStreamingMedia MajorVersion="2" MinorVersion="0" TimeScale="10000000" IsLive="true" Duration="0"
  LookAheadFragmentCount="2" DVRWindowLength="6000000000">

  <StreamIndex Type="video" Name="video" Subtype="" Chunks="0" TimeScale="10000000"
    Url="QualityLevels({bitrate})/Fragments(video={start time})">
    <QualityLevel Index="0" Bitrate="230000"
      CodecPrivateData="250000010FC3460B50878A0B5821FF878780490800704704DC0000010E5A67F840" FourCC="WVC1"
      MaxWidth="364" MaxHeight="272"/>
    <QualityLevel Index="1" Bitrate="305000"
      CodecPrivateData="250000010FC3480B50878A0B5821FF87878049080894E4A7640000010E5A67F840" FourCC="WVC1"
      MaxWidth="364" MaxHeight="272"/>
    <c t="0" d="20000000" r="300" />
  </StreamIndex>
  <StreamIndex Type="audio" Name="audio" Subtype="" Chunks="0" TimeScale="10000000"
    Url="QualityLevels({bitrate})/Fragments(audio={start time})">
    <QualityLevel Index="0" Bitrate="96000" CodecPrivateData="1000030000000000000000000000E00042C0"
      FourCC="WMAP" AudioTag="354" Channels="2" SamplingRate="44100" BitsPerSample="16" PacketSize="4459"/>
    <c t="0" d="20000000" r="300" />
  </StreamIndex>
  <StreamIndex Type="text" Name="scte35-sparse-stream" Subtype="DATA" Chunks="0" TimeScale="10000000"
    ParentStreamIndex="video" ManifestOutput="true" 
    Url="QualityLevels({bitrate})/Fragments(captions={start time})">
    <QualityLevel Index="0" Bitrate="0" CodecPrivateData="" FourCC="">
      <CustomAttributes>
        <Attribute Name="Scheme" Value="urn:scte:scte35:2013:bin"/>
      </CustomAttributes>
    </QualityLevel>
    <!-- The following <c> and <f> fragments contains the base64-encoded [SCTE35] splice_info_section() message -->
    <c t="600000000" d="300000000">    <f>PD94bWwgdmVyc2lvbj0iMS4wIiBlbmNvZGluZz0idXRmLTgiPz48QWNxdWlyZWRTaWduYWwgeG1sbnM9InVybjpjYWJsZWxhYnM6bWQ6eHNkOnNpZ25hbGluZzozLjAiIGFjcXVpc2l0aW9uUG9pbnRJZGVudGl0eT0iRVNQTl9FYXN0X0FjcXVpc2l0aW9uX1BvaW50XzEiIGFjcXVpc2l0aW9uU2lnbmFsSUQ9IjRBNkE5NEVFLTYyRkExMUUxQjFDQTg4MkY0ODI0MDE5QiIgYWNxdWlzaXRpb25UaW1lPSIyMDEyLTA5LTE4VDEwOjE0OjI2WiI+PFVUQ1BvaW50IHV0Y1BvaW50PSIyMDEyLTA5LTE4VDEwOjE0OjM0WiIvPjxTQ1RFMzVQb2ludERlc2NyaXB0b3Igc3BsaWNlQ29tbWFuZFR5cGU9IjUiPjxTcGxpY2VJbnNlcnQgc3BsaWNlRXZlbnRJRD0iMzQ0NTY4NjkxIiBvdXRPZk5ldHdvcmtJbmRpY2F0b3I9InRydWUiIHVuaXF1ZVByb2dyYW1JRD0iNTUzNTUiIGR1cmF0aW9uPSJQVDFNMFMiIGF2YWlsTnVtPSIxIiBhdmFpbHNFeHBlY3RlZD0iMTAiLz48L1NDVEUzNVBvaW50RGVzY3JpcHRvcj48U3RyZWFtVGltZXM+PFN0cmVhbVRpbWUgdGltZVR5cGU9IkhTUyIgdGltZVZhbHVlPSI1MTUwMDAwMDAwMDAiLz48L1N0cmVhbVRpbWVzPjwvQWNxdWlyZWRTaWduYWw+</f>
    </c>
    <c t="1200000000" d="400000000">      <f>PD94bWwgdmVyc2lvbj0iMS4wIiBlbmNvZGluZz0idXRmLTgiPz48QWNxdWlyZWRTaWduYWwgeG1sbnM9InVybjpjYWJsZWxhYnM6bWQ6eHNkOnNpZ25hbGluZzozLjAiIGFjcXVpc2l0aW9uUG9pbnRJZGVudGl0eT0iRVNQTl9FYXN0X0FjcXVpc2l0aW9uX1BvaW50XzEiIGFjcXVpc2l0aW9uU2lnbmFsSUQ9IjRBNkE5NEVFLTYyRkExMUUxQjFDQTg4MkY0ODI0MDE5QiIgYWNxdWlzaXRpb25UaW1lPSIyMDEyLTA5LTE4VDEwOjE0OjI2WiI+PFVUQ1BvaW50IHV0Y1BvaW50PSIyMDEyLTA5LTE4VDEwOjE0OjM0WiIvPjxTQ1RFMzVQb2ludERlc2NyaXB0b3Igc3BsaWNlQ29tbWFuZFR5cGU9IjUiPjxTcGxpY2VJbnNlcnQgc3BsaWNlRXZlbnRJRD0iMzQ0NTY4NjkxIiBvdXRPZk5ldHdvcmtJbmRpY2F0b3I9InRydWUiIHVuaXF1ZVByb2dyYW1JRD0iNTUzNTUiIGR1cmF0aW9uPSJQVDFNMFMiIGF2YWlsTnVtPSIxIiBhdmFpbHNFeHBlY3RlZD0iMTAiLz48L1NDVEUzNVBvaW50RGVzY3JpcHRvcj48U3RyZWFtVGltZXM+PFN0cmVhbVRpbWUgdGltZVR5cGU9IkhTUyIgdGltZVZhbHVlPSI1MTYyMDAwMDAwMDAiLz48L1N0cmVhbVRpbWVzPjwvQWNxdWlyZWRTaWduYWw+</f>
    </c>
  </StreamIndex>
</SmoothStreamingMedia>
~~~

## <a name="32-apple-hls-manifest-decoration"></a>3.2 Jabłko HLS Manifest Dekoracja

Usługa Azure Media Services obsługuje następujące znaczniki manifestu HLS do sygnalizowania informacji o dostępności reklam podczas zdarzenia na żywo lub na żądanie. 

- EXT-X-DATERANGE zgodnie z definicją w Apple HLS [RFC8216]
- EXT-X-CUE zgodnie z definicją w [Adobe-Primetime] - ten tryb jest uważany za "starszy". Klienci powinni przyjąć tag EXT-X-DATERANGE, jeśli to możliwe.

Dane wyjściowe do każdego tagu będą się różnić w zależności od używanego trybu pozyskiwania sygnału. Na przykład przylecienie RTMP w trybie Adobe Simple nie zawiera pełnego ładunku zakodowanego w kodowaniu kodowanym przez SCTE-35.

## <a name="321-apple-hls-with-ext-x-daterange-recommended"></a>3.2.1 Apple HLS z EXT-X-DATERANGE (zalecane)

Specyfikacja Apple HTTP Live Streaming [RFC8216] umożliwia sygnalizowanie wiadomości [SCTE-35]. Wiadomości są wstawiane do listy odtwarzania segmentu w tagu EXT-X-DATERANGE w sekcji [RFC8216] zatytułowanej "Mapowanie SCTE-35 do EXT-X-DATERANGE".  Warstwa aplikacji klienckiej może przeanalizować listę odtwarzania M3U i przetwarzać tagi M3U lub odbierać zdarzenia za pośrednictwem struktury odtwarzacza Apple.  

**Zalecane** podejście w usłudze Azure Media Services (wersja 3 INTERFEJSU API) jest do naśladowania [RFC8216] i użyć ext-X_DATERANGE tag dla [SCTE35] ad avail dekoracji w manifeście.

## <a name="3211-example-hls-manifest-m3u8-showing-ext-x-daterange-signaling-of-scte-35"></a>3.2.1.1 Przykład manifestu HLS .m3u8 przedstawiającego sygnalizację EXT-X-DATERANGE scte-35

Poniższy przykład HLS manifest danych wyjściowych z pakietu pakietowego dynamicznego usługi Media Services pokazuje użycie EXT-X-DATERANGE tag z [RFC8216] sygnalizacji SCTE-35 zdarzeń w strumieniu. Ponadto ten strumień zawiera "legacy" EXT-X-CUE tag dla [Adobe-Primetime].

~~~
#EXTM3U
#EXT-X-VERSION:8
#EXT-X-MEDIA-SEQUENCE:0
#EXT-X-TARGETDURATION:2
#EXT-X-INDEPENDENT-SEGMENTS
#EXT-X-PROGRAM-DATE-TIME:2020-01-07T19:40:50Z
#EXTINF:1.501500,no-desc
Fragments(video=22567545,format=m3u8-aapl-v8)
#EXTINF:1.501500,no-desc
Fragments(video=22702680,format=m3u8-aapl-v8)
#EXTINF:1.501500,no-desc
Fragments(video=22837815,format=m3u8-aapl-v8)
#EXTINF:1.501500,no-desc
Fragments(video=22972950,format=m3u8-aapl-v8)
#EXTINF:1.501500,no-desc
Fragments(video=23108085,format=m3u8-aapl-v8)
#EXTINF:1.234567,no-desc
Fragments(video=23243220,format=m3u8-aapl-v8)
#EXTINF:0.016689,no-desc
Fragments(video=23354331,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=0.000022
#EXTINF:0.250244,no-desc
Fragments(video=23355833,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=0.250267
#EXTINF:0.850856,no-desc
Fragments(video=23378355,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=1.101122
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:10.610Z",SCTE35-IN=0xFC30200000000005DD00FFF00F05000003EA7F4FFE0165E4D3000101010000607CE85A
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=0.000000,TIME=260.610344,CUE="/DAgAAAAAAXdAP/wDwUAAAPqf0/+AWXk0wABAQEAAGB86Fo="
#EXTINF:0.650644,no-desc
Fragments(video=23454932,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=1.751767
#EXTINF:0.050044,no-desc
Fragments(video=23513490,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=1.801811
#EXTINF:1.451456,no-desc
Fragments(video=23517994,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=3.253267
#EXTINF:1.501500,no-desc
Fragments(video=23648625,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=4.754767
#EXTINF:1.501500,no-desc
Fragments(video=23783760,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=6.256267
#EXTINF:1.501500,no-desc
Fragments(video=23918895,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=7.757767
#EXTINF:1.501500,no-desc
Fragments(video=24054030,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=9.259267
#EXTINF:1.501500,no-desc
Fragments(video=24189165,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=10.760767
#EXTINF:1.501500,no-desc
Fragments(video=24324300,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=12.262267
#EXTINF:1.501500,no-desc
Fragments(video=24459435,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=13.763767
#EXTINF:1.501500,no-desc
Fragments(video=24594570,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=15.265267
#EXTINF:1.501500,no-desc
Fragments(video=24729705,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=16.766767
#EXTINF:1.501500,no-desc
Fragments(video=24864840,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=18.268267
#EXTINF:1.501500,no-desc
Fragments(video=24999975,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=19.769767
#EXTINF:1.501500,no-desc
Fragments(video=25135110,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=21.271267
#EXTINF:1.501500,no-desc
Fragments(video=25270245,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=22.772767
#EXTINF:1.501500,no-desc
Fragments(video=25405380,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=24.274267
#EXTINF:1.501500,no-desc
Fragments(video=25540515,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=25.775767
#EXTINF:1.501500,no-desc
Fragments(video=25675650,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=27.277267
#EXTINF:1.501500,no-desc
Fragments(video=25810785,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=28.778767
#EXTINF:1.501500,no-desc
Fragments(video=25945920,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=30.280267
#EXTINF:1.501500,no-desc
Fragments(video=26081055,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=31.781767
#EXTINF:1.501500,no-desc
Fragments(video=26216190,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=33.283267
#EXTINF:1.501500,no-desc
Fragments(video=26351325,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=34.784767
#EXTINF:1.501500,no-desc
Fragments(video=26486460,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=36.286267
#EXTINF:1.501500,no-desc
Fragments(video=26621595,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=37.787767
#EXTINF:1.501500,no-desc
Fragments(video=26756730,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=39.289267
#EXTINF:1.501500,no-desc
Fragments(video=26891865,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=40.790767
#EXTINF:1.501500,no-desc
Fragments(video=27027000,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=42.292267
#EXTINF:1.501500,no-desc
Fragments(video=27162135,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=43.793767
#EXTINF:1.501500,no-desc
Fragments(video=27297270,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=45.295267
#EXTINF:1.501500,no-desc
Fragments(video=27432405,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=46.796767
#EXTINF:1.501500,no-desc
Fragments(video=27567540,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=48.298267
#EXTINF:1.501500,no-desc
Fragments(video=27702675,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=49.799767
#EXTINF:1.501500,no-desc
Fragments(video=27837810,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=51.301267
#EXTINF:1.501500,no-desc
Fragments(video=27972945,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=52.802767
#EXTINF:1.501500,no-desc
Fragments(video=28108080,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=54.304267
#EXTINF:1.501500,no-desc
Fragments(video=28243215,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=55.805767
#EXTINF:1.501500,no-desc
Fragments(video=28378350,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=57.307267
#EXTINF:1.501500,no-desc
Fragments(video=28513485,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=58.808767
#EXTINF:1.501500,no-desc
Fragments(video=28648620,format=m3u8-aapl-v8)

~~~


## <a name="322-apple-hls-with-adobe-primetime-ext-x-cue-legacy"></a>3.2.2 Apple HLS z Adobe Primetime EXT-X-CUE (starsze)

Istnieje również "starsza" implementacja dostępna w usłudze Azure Media Services (wersja 2 i 3 interfejsu API), która używa tagu EXT-X-CUE zgodnie z definicją w trybie [Adobe-Primetime] "SCTE-35 Mode". W tym trybie usługa Azure Media Services osadza kodowane base64 [SCTE-35] splice_info_section() w tagu EXT-X-CUE.  

Tag "legacy" EXT-X-CUE jest zdefiniowany poniżej i może być również normatywny w specyfikacji [Adobe-Primetime]. Powinno to być używane tylko w przypadku starszego sygnalizowania SCTE35 w razie potrzeby, w przeciwnym razie zalecany tag jest zdefiniowany w [RFC8216] jako EXT-X-DATERANGE. 

| **Nazwa atrybutu** | **Typ**                      | **Wymagane?**                             | **Opis**                                                                                                                                                                                                                                                                          |
| ------------------ | ----------------------------- | ----------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Cue                | cytowany ciąg                 | Wymagany                                  | Komunikat zakodowany jako ciąg zakodowany base64, zgodnie z opisem w [RFC4648]. W przypadku komunikatów [SCTE-35] jest to kodowany base64 splice_info_section().                                                                                                                                      |
| TYP               | cytowany ciąg                 | Wymagany                                  | Urn lub adres URL identyfikujący schemat wiadomości. W przypadku komunikatów [SCTE-35] typ przyjmuje specjalną wartość "scte35".                                                                                                                                                                          |
| ID                 | cytowany ciąg                 | Wymagany                                  | Unikatowy identyfikator zdarzenia. Jeśli identyfikator nie jest określony podczas pozyskiwania wiadomości, usługa Azure Media Services wygeneruje unikatowy identyfikator.                                                                                                                                              |
| Długość           | liczba zmiennoprzecinkowa dziesiętna | Wymagany                                  | Czas trwania zdarzenia. Jeśli nie jest znana, wartość **powinna wynosić** 0. Jednostki to sekundy frakcji.                                                                                                                                                                                           |
| Elapsed            | liczba zmiennoprzecinkowa dziesiętna | Opcjonalne, ale wymagane do przesuwania okna | Gdy sygnał jest powtarzany do obsługi okna prezentacji przesuwnej, to pole **MUSI** być czas prezentacji, który upłynął od rozpoczęcia zdarzenia. Jednostki są ułamkowe sekundy. Wartość ta może przekraczać oryginalny określony czas trwania splice lub segmentu. |
| CZAS               | liczba zmiennoprzecinkowa dziesiętna | Wymagany                                  | Czas prezentacji wydarzenia. Jednostki są ułamkowe sekundy.                                                                                                                                                                                                                        |


Warstwa aplikacji odtwarzacza HLS użyje type do identyfikacji formatu wiadomości, dekodowania wiadomości, zastosowania niezbędnych konwersji czasu i przetworzenia zdarzenia.  Zdarzenia są synchronizowane w czasie listy odtwarzania segmentu ścieżki nadrzędnej, zgodnie z sygnaturą czasą zdarzenia.  Są one wstawiane przed najbliższym segmentem (#EXTINF znacznikiem).

### <a name="323-hls-m3u8-manifest-example-using-legacy-adobe-primetime-ext-x-cue"></a>3.2.3 HLS .m3u8 przykład manifestu przy użyciu "Legacy" Adobe Primetime EXT-X-CUE

W poniższym przykładzie przedstawiono dekorację manifestu HLS przy użyciu tagu Adobe Primetime EXT-X-CUE.  Parametr "CUE" zawiera tylko właściwości TYPE i Duration, co oznacza, że było to źródło RTMP za pomocą "prostego" sygnału trybu Adobe.  Jeśli był to sygnał w trybie SCTE-35, tag zawierałby zakodowany w base64 binarny ładunek SCTE-35, jak widać w [przykładzie 3.2.1.1](#3211-example-hls-manifest-m3u8-showing-ext-x-daterange-signaling-of-scte-35).

~~~
#EXTM3U
#EXT-X-VERSION:4
#EXT-X-PLAYLIST-TYPE:VOD
#EXT-X-ALLOW-CACHE:NO
#EXT-X-MEDIA-SEQUENCE:0
#EXT-X-TARGETDURATION:11
#EXT-X-PROGRAM-DATE-TIME:2019-12-10T09:18:14Z
#EXTINF:10.010000,no-desc
Fragments(video=4011540820,format=m3u8-aapl)
#EXTINF:10.010000,no-desc
Fragments(video=4011550830,format=m3u8-aapl)
#EXTINF:10.010000,no-desc
Fragments(video=4011560840,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000
#EXTINF:8.008000,no-desc
Fragments(video=4011570850,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=0.593000
#EXTINF:4.170000,no-desc
Fragments(video=4011578858,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=4.763000
#EXTINF:9.844000,no-desc
Fragments(video=4011583028,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=14.607000
#EXTINF:10.010000,no-desc
Fragments(video=4011592872,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=24.617000
#EXTINF:10.010000,no-desc
Fragments(video=4011602882,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=34.627000
#EXTINF:10.010000,no-desc
Fragments(video=4011612892,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=44.637000
#EXTINF:10.010000,no-desc
Fragments(video=4011622902,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=54.647000
#EXTINF:10.010000,no-desc
Fragments(video=4011632912,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=64.657000
#EXTINF:10.010000,no-desc
Fragments(video=4011642922,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=74.667000
#EXTINF:10.010000,no-desc
Fragments(video=4011652932,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=84.677000
#EXTINF:10.010000,no-desc
Fragments(video=4011662942,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=94.687000
#EXTINF:10.010000,no-desc
Fragments(video=4011672952,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=104.697000
#EXTINF:10.010000,no-desc
Fragments(video=4011682962,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=114.707000
#EXTINF:10.010000,no-desc
Fragments(video=4011692972,format=m3u8-aapl)
#EXTINF:8.008000,no-desc
Fragments(video=4011702982,format=m3u8-aapl)

~~~

### <a name="324-hls-message-handling-for-legacy-adobe-primetime-ext-x-cue"></a>3.2.4 Obsługa wiadomości HLS dla programu "Legacy" Adobe Primetime EXT-X-CUE

Zdarzenia są sygnalizowane na liście odtwarzania segmentu każdej ścieżki wideo i audio. Pozycja znacznika EXT-X-CUE **MUSI** być zawsze bezpośrednio przed pierwszym segmentem HLS (dla wyjścia lub rozpoczęcia segmentu) lub bezpośrednio po ostatnim segmencie HLS (dla splice w lub na końcu segmentu), do którego odnoszą się jego atrybuty TIME i DURATION, zgodnie z wymaganiami [Adobe-Primetime].

Gdy okno prezentacji przesuwnej jest włączone, tag EXT-X-CUE **MUSI** być powtarzany na tyle często, aby splice lub segment był zawsze w pełni opisany na liście odtwarzania segmentu, a atrybut ELAPSED **MUSI** być używany do wskazania czasu, przez jaki splice lub segment był aktywny, zgodnie z wymaganiami [Adobe-Primetime].

Gdy okno prezentacji przesuwnej jest włączone, znaczniki EXT-X-CUE są usuwane z listy odtwarzania segmentu, gdy czas multimediów, do których się odnoszą, został wycofany z okna prezentacji przesuwnej.

## <a name="33-dash-manifest-decoration-mpd"></a>3.3 Dekoracja manifestu DASH (MPD)

[MPEGDASH] udostępnia trzy sposoby sygnalizowania zdarzeń:

1.  Zdarzenia sygnalizowane w odtwarzaczu MPD EventStream
2.  Zdarzenia sygnalizowane w paśmie za pomocą okna komunikatu zdarzenia ('emsg')
3.  Połączenie zarówno 1, jak i 2

Zdarzenia sygnalizowane w MPD EventStream są przydatne do przesyłania strumieniowego VOD, ponieważ klienci mają dostęp do wszystkich zdarzeń, natychmiast po pobraniu mpd. Jest to również przydatne w przypadku sygnalizacji SSAI, gdzie dalszy dostawca SSAI musi przeanalizować sygnały z wieloe okresowego manifestu MPD i dynamicznie wstawić zawartość reklamy.  Rozwiązanie w paście ('emsg')jest przydatne w przypadku przesyłania strumieniowego na żywo, gdzie klienci nie muszą ponownie pobierać mpd lub nie ma manipulacji manifestu SSAI między klientem a źródłem. 

Domyślne zachowanie usługi Azure Media Services dla programu DASH polega na sygnalizowaniu zarówno w strumieniu zdarzeń MPD, jak i w paśmie przy użyciu okna komunikatu zdarzenia ('emsg').

Komunikaty sygnalizacji pochodzące przez [RTMP] lub [MS-SSTR-INGEST] są mapowane na zdarzenia DASH, używając pól "emsg" w paśmie i/lub w strumieniach zdarzeń MPD. 

Sygnalizacja W paśmie SCTE-35 dla DASH jest zgodna z definicją i wymaganiami określonymi w [SCTE-214-3], a także w sekcji [DASH-IF-IOP] 13.12.2 ("Zdarzenia SCTE35"). 

W przypadku wózka w paśmie [SCTE-35] pole komunikat o zdarzeniu ('emsg') używa programuId = "urn:scte:scte35:2013:bin". Dla dekoracji manifestu MPD EventStream schemeId używa "urn:scte:scte35:2014:xml+bin".  Ten format jest reprezentacją XML zdarzenia, która zawiera binarne base64 zakodowane dane wyjściowe pełnego scte-35 komunikat, który dotarł do spożycia. 

Normatywne definicje referencyjne przewozu komunikatów wskazujących [SCTE-35] w DASH są dostępne w [SCTE-214-1] sec 6.7.4 (MPD) i [SCTE-214-3] s 7.3.2 (Przewóz komunikatów sygnalizacji SCTE 35).

### <a name="331-mpeg-dash-mpd-eventstream-signaling"></a>3.3.1 Mpeg DASH (MPD) Sygnalizacja eventstream

Manifest (MPD) dekoracji zdarzeń będą sygnalizowane w MPD przy użyciu EventStream element, który pojawia się w Period elementu. Używany jest "urn:scte35:2014:xml+bin".

> [!NOTE]
> Dla celów zwięzłości [SCTE-35] umożliwia użycie sekcji zakodowanej base64 w Signal.Binary element (a nie Signal.SpliceInfoSection element) jako alternatywa dla przewozu całkowicie analizowane komunikat sygnalizacji.
> Usługa Azure Media Services używa tego podejścia "xml+bin" do sygnalizacji w manifeście MPD.
> Jest to również zalecana metoda stosowana w [DASH-IF-IOP] - patrz sekcja [zatytułowana "Strumienie zdarzeń wstawiania reklam" w wytycznych DASH IF IOP](https://dashif-documents.azurewebsites.net/DASH-IF-IOP/master/DASH-IF-IOP.html#ads-insertion-event-streams)
> 

Element EventStream ma następujące atrybuty:

| **Nazwa atrybutu** | **Typ**                | **Wymagane?** | **Opis**                                                                                                                                                                                                                                                                                                                                                                         |
| ------------------ | ----------------------- | ------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| scheme_id_uri      | ciąg                  | Wymagany      | Identyfikuje schemat wiadomości. Schemat jest ustawiony na wartość atrybutu Scheme w polu Manifest live server. Wartość **powinna** być URN lub adres URL identyfikujący schemat wiadomości; Obsługiwany schemat wyjściowyId powinien być "urn:scte:scte35:2014:xml+bin" na [SCTE-214-1] sec 6.7.4 (MPD), ponieważ usługa obsługuje tylko "xml+bin" w tej chwili dla zwięzłości w mpd. |
| value              | ciąg                  | Optional (Opcjonalność)      | Dodatkowa wartość ciągu używana przez właścicieli schematu do dostosowywania semantyki wiadomości. Aby odróżnić wiele strumieni zdarzeń z tym samym schematem, wartość **MUSI** być ustawiona na nazwę strumienia zdarzeń (trackName for [MS-SSTR-Ingest] lub NAZWA KOMUNIKATU AMF dla pozyskiwania [RTMP].                                                                         |
| Skala czasu          | 32-bitowa niepodpisana gnilizna | Wymagany      | Skala czasu w znacznikach na sekundę.                                                                                                                                                                                                                                                                                                                                                     |


### <a name="332-example-event-streams-for-mpeg-dash"></a>3.3.2 Przykładowe strumienie zdarzeń dla MPEG DASH

#### <a name="3321-example-mpeg-dash-mpd-manifest-signaling-of-rtmp-streaming-using-adobe-simple-mode"></a>3.3.2.1 Przykładowy błąd MPEG DASH .mpd sygnalizując transmisję strumieniową RTMP w trybie prostym Adobe

W poniższym przykładzie przedstawiono fragment EventStream z pakietu pakietowego dynamicznego usługi Media Services dla strumienia RTMP przy użyciu "prostego" sygnału trybu Adobe.

~~~ xml
<!-- Example EventStream element using "urn:com:adobe:dpi:simple:2015" Adobe simple signaling per [Adobe-Primetime] -->
    <EventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="simplesignal" timescale="10000000">
        <Event presentationTime="1583497601000000" duration="300000000" id="1085900"/>
        <Event presentationTime="1583500901666666" duration="300000000" id="1415966"/>
        <Event presentationTime="1583504202333333" duration="300000000" id="1746033"/>
        <Event presentationTime="1583507502666666" duration="300000000" id="2076066"/>
        <Event presentationTime="1583510803333333" duration="300000000" id="2406133"/>
        <Event presentationTime="1583514104000000" duration="300000000" id="2736200"/>
        <Event presentationTime="1583517404666666" duration="300000000" id="3066266"/>
        <Event presentationTime="1583520705333333" duration="300000000" id="3396333"/>
        <Event presentationTime="1583524006000000" duration="300000000" id="3726400"/>
        <Event presentationTime="1583527306666666" duration="300000000" id="4056466"/>
        <Event presentationTime="1583530607333333" duration="300000000" id="4386533"/>
    </EventStream>
~~~

#### <a name="3322-example-mpeg-dash-mpd-manifest-signaling-of-an-rtmp-stream-using-adobe-scte-35-mode"></a>3.3.2.2 Przykładowa sygnalizacja manifestu MPEG DASH .mpd strumienia RTMP w trybie Adobe SCTE-35

W poniższym przykładzie przedstawiono fragment programu EventStream z pakietu pakietowego dynamicznego usługi Media Services dla strumienia RTMP przy użyciu sygnalizacji w trybie Adobe SCTE-35.

~~~ xml
<!-- Example EventStream element using xml+bin style signaling per [SCTE-214-1] -->

      <EventStream schemeIdUri="urn:scte:scte35:2014:xml+bin" value="scte35" timescale="10000000">
        <Event presentationTime="2595092444" duration="11011000" id="1002">
            <Signal xmlns="http://www.scte.org/schemas/35/2016">
                <Binary>/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==</Binary>
            </Signal>
        </Event>
        <Event presentationTime="2606103444" id="1002">
            <Signal xmlns="http://www.scte.org/schemas/35/2016">
                <Binary>/DAgAAAAAAXdAP/wDwUAAAPqf0/+AWXk0wABAQEAAGB86Fo=</Binary>
            </Signal>
        </Event>
      </EventStream>
~~~

> [!IMPORTANT]
> Należy zauważyć, że presentationTime to czas prezentacji zdarzenia [SCTE-35] przetłumaczony na względem czasu rozpoczęcia okresu, a nie czas przybycia wiadomości.
> [MPEGDASH] definiuje Event@presentationTime jako "Określa czas prezentacji zdarzenia względem początku okresu.
> Wartość czasu prezentacji w sekundach jest podziałem wartości tego atrybutu EventStream@timescale i wartości atrybutu.
> Jeśli nie jest obecny, wartość czasu prezentacji jest 0.

#### <a name="3331-example-mpeg-dash-manifest-mpd-with-single-period-eventstream-using-adobe-simple-mode-signals"></a>3.3.3.1 Przykładowy manifest MPEG DASH (MPD) z jednoe okresowym, EventStream, przy użyciu sygnałów trybu prostego Adobe

Poniższy przykład przedstawia dane wyjściowe z pakietu pakietowego dynamicznego usługi Media Services dla źródłowego strumienia RTMP przy użyciu metody sygnału reklamowego w trybie "simple" firmy Adobe. Dane wyjściowe to manifest pojedynczego okresu przedstawiający eventstream przy użyciu identyfikatora Uri schemeid ustawionego na "urn:com:adobe:dpi:simple:2015" i właściwości value ustawioną na "simplesignal".
Każdy prosty sygnał jest dostarczany w @presentationTime @durationevent @id element z , i właściwości wypełnione na podstawie przychodzących sygnałów prostych.

~~~ xml
<?xml version="1.0" encoding="utf-8"?>
<MPD xmlns="urn:mpeg:dash:schema:mpd:2011" 
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" profiles="urn:mpeg:dash:profile:isoff-live:2011" type="static" mediaPresentationDuration="PT28M1.680S" minBufferTime="PT3S">
    <Period>
        <EventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="simplesignal" timescale="1000">
            <Event presentationTime="4011578265" duration="119987" id="4011578265"/>
        </EventStream>
        <AdaptationSet id="1" group="1" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="video" mimeType="video/mp4" codecs="avc1.4D4028" maxWidth="1920" maxHeight="1080" startWithSAP="1">
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="simplesignal"/>
            <ProducerReferenceTime id="4011460740" type="0" wallClockTime="2020-01-25T19:35:54.740Z" presentationTime="4011460740"/>
            <SegmentTemplate timescale="1000" presentationTimeOffset="4011460740" media="QualityLevels($Bandwidth$)/Fragments(video=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(video=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="4011460740" d="2002" r="57"/>
                    <S d="1401"/>
                    <S d="601"/>
                    <S d="2002"/>

                     <!--> ... video segments truncated for sample brevity </-->

                </SegmentTimeline>
            </SegmentTemplate>
            <Representation id="1_V_video_14759481473095519504" bandwidth="6000000" width="1920" height="1080"/>
            <Representation id="1_V_video_1516803357996956148" bandwidth="4000000" codecs="avc1.4D401F" width="1280" height="720"/>
            <Representation id="1_V_video_5430608182379669372" bandwidth="2600000" codecs="avc1.4D401F" width="960" height="540"/>
            <Representation id="1_V_video_3780180650986497347" bandwidth="1000000" codecs="avc1.4D401E" width="640" height="360"/>
            <Representation id="1_V_video_13759117363700265707" bandwidth="699000" codecs="avc1.4D4015" width="480" height="270"/>
            <Representation id="1_V_video_6140004908920393176" bandwidth="400000" codecs="avc1.4D4015" width="480" height="270"/>
            <Representation id="1_V_video_10673801877453424365" bandwidth="200000" codecs="avc1.4D400D" width="320" height="180"/>
        </AdaptationSet>
        <AdaptationSet id="2" group="5" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="audio" mimeType="audio/mp4" codecs="mp4a.40.2">
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="simplesignal"/>
            <ProducerReferenceTime id="4011460761" type="0" wallClockTime="2020-01-25T19:35:54.761Z" presentationTime="4011460761"/>
            <Label>audio</Label>
            <SegmentTemplate timescale="1000" presentationTimeOffset="4011460740" media="QualityLevels($Bandwidth$)/Fragments(audio=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(audio=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="4011460761" d="1984"/>
                    <S d="2005" r="1"/>
                    <S d="2006"/>

                    <!--> ... audio segments truncated for example brevity </-->

                </SegmentTimeline>
            </SegmentTemplate>
            <Representation id="5_A_audio_17504386117102112482" bandwidth="128000" audioSamplingRate="48000"/>
        </AdaptationSet>
    </Period>
</MPD>

~~~

#### <a name="3332-example-mpeg-dash-manifest-mpd-with-multi-period-eventstream-using-adobe-scte35-mode-signaling"></a>3.3.3.2 Przykładowy manifest MPEG DASH (MPD) z wielokątnym, EventStream, przy użyciu sygnalizacji w trybie Adobe SCTE35

Poniższy przykład przedstawia dane wyjściowe z pakietu pakietowego dynamicznego usługi Media Services dla źródłowego strumienia RTMP przy użyciu sygnalizacji trybu Adobe SCTE35.
W takim przypadku manifest danych wyjściowych jest wieloekranowym dash .mpd z eventstream elementu i @schemeIdUri właściwość ustawiona na "urn:scte35:2014:xml+bin" i właściwość ustawiona @value na "scte35". Każdy element zdarzenia w Ramach EventStream zawiera pełny kodowany binarny sygnał SCTE35 zakodowany w base64 

~~~ xml
<?xml version="1.0" encoding="utf-8"?>
<MPD xmlns="urn:mpeg:dash:schema:mpd:2011" 
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" profiles="urn:mpeg:dash:profile:isoff-live:2011" type="dynamic" publishTime="2020-01-07T19:42:44Z" minimumUpdatePeriod="PT0S" timeShiftBufferDepth="PT58M56S" availabilityStartTime="2020-01-07T19:40:50Z" minBufferTime="PT4S">
    <Period start="PT2M48.168S" id="main-content_0">
        <AdaptationSet id="1" group="1" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="video" mimeType="video/mp4" codecs="avc1.640020" maxWidth="1280" maxHeight="720" startWithSAP="1">
            <InbandEventStream schemeIdUri="urn:mpeg:dash:event:2012" value="1"/>
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="amssignal"/>
            <SegmentTemplate timescale="90000" presentationTimeOffset="15135120" media="QualityLevels($Bandwidth$)/Fragments(video=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(video=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="15135120" d="135135" r="59"/>
                    <S d="111111"/>
                    <S d="1502"/>
                </SegmentTimeline>
            </SegmentTemplate>
            <ProducerReferenceTime id="15135120" type="0" wallClockTime="2020-01-07T19:40:50Z" presentationTime="15135120"/>
            <Representation id="1_V_video_5322324134428436312" bandwidth="3500000" width="1280" height="720"/>
            <Representation id="1_V_video_16981495139092747609" bandwidth="2200000" width="960" height="540"/>
            <Representation id="1_V_video_1384718563016940751" bandwidth="1350000" codecs="avc1.64001F" width="704" height="396"/>
            <Representation id="1_V_video_4425970933904124207" bandwidth="850000" codecs="avc1.64001E" width="512" height="288"/>
            <Representation id="1_V_video_11952982975776937431" bandwidth="550000" codecs="avc1.640016" width="384" height="216"/>
            <Representation id="1_V_video_10673801877453424365" bandwidth="200000" codecs="avc1.640015" width="340" height="192"/>
        </AdaptationSet>
        <AdaptationSet id="2" group="5" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="audio" mimeType="audio/mp4" codecs="mp4a.40.5" lang="en">
            <InbandEventStream schemeIdUri="urn:mpeg:dash:event:2012" value="1"/>
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="amssignal"/>
            <Label>audio</Label>
            <SegmentTemplate timescale="44100" presentationTimeOffset="7416208" media="QualityLevels($Bandwidth$)/Fragments(audio=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(audio=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="7417856" d="133120"/>
                    <S d="132096" r="1"/>
                    
                    <!--> ... aduio segments truncated for sample brevity </-->

                </SegmentTimeline>
            </SegmentTemplate>
            <ProducerReferenceTime id="7417856" type="0" wallClockTime="2020-01-07T19:40:50.037Z" presentationTime="7417856"/>
            <Representation id="5_A_audio_17504386117102112482" bandwidth="128000" audioSamplingRate="44100"/>
        </AdaptationSet>
    </Period>
    <Period start="PT4M19.509S" id="scte-35_0">
        <EventStream schemeIdUri="urn:scte:scte35:2014:xml+bin" value="scte35" timescale="10000000">
            <Event presentationTime="2595092444" duration="11011000" id="1002">
                <Signal xmlns="http://www.scte.org/schemas/35/2016">
                    <Binary>/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==</Binary>
                </Signal>
            </Event>
            <Event presentationTime="2606103444" id="1002">
                <Signal xmlns="http://www.scte.org/schemas/35/2016">
                    <Binary>/DAgAAAAAAXdAP/wDwUAAAPqf0/+AWXk0wABAQEAAGB86Fo=</Binary>
                </Signal>
            </Event>
        </EventStream>
        <AdaptationSet id="1" group="1" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="video" mimeType="video/mp4" codecs="avc1.640020" maxWidth="1280" maxHeight="720" startWithSAP="1">
            <InbandEventStream schemeIdUri="urn:mpeg:dash:event:2012" value="1"/>
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="amssignal"/>
            <SegmentTemplate timescale="90000" presentationTimeOffset="23355832" media="QualityLevels($Bandwidth$)/Fragments(video=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(video=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="23355833" d="22522"/>
                    <S d="76577"/>
                </SegmentTimeline>
            </SegmentTemplate>
            <ProducerReferenceTime id="23355833" type="0" wallClockTime="2020-01-07T19:42:21.341Z" presentationTime="23355833"/>
            <Representation id="1_V_video_5322324134428436312" bandwidth="3500000" width="1280" height="720"/>
            <Representation id="1_V_video_16981495139092747609" bandwidth="2200000" width="960" height="540"/>
            <Representation id="1_V_video_1384718563016940751" bandwidth="1350000" codecs="avc1.64001F" width="704" height="396"/>
            <Representation id="1_V_video_4425970933904124207" bandwidth="850000" codecs="avc1.64001E" width="512" height="288"/>
            <Representation id="1_V_video_11952982975776937431" bandwidth="550000" codecs="avc1.640016" width="384" height="216"/>
            <Representation id="1_V_video_10673801877453424365" bandwidth="200000" codecs="avc1.640015" width="340" height="192"/>
        </AdaptationSet>
        <AdaptationSet id="2" group="5" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="audio" mimeType="audio/mp4" codecs="mp4a.40.5" lang="en">
            <InbandEventStream schemeIdUri="urn:mpeg:dash:event:2012" value="1"/>
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="amssignal"/>
            <Label>audio</Label>
            <SegmentTemplate timescale="44100" presentationTimeOffset="11444358" media="QualityLevels($Bandwidth$)/Fragments(audio=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(audio=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="11446272" d="49152"/>
                </SegmentTimeline>
            </SegmentTemplate>
            <ProducerReferenceTime id="11446272" type="0" wallClockTime="2020-01-07T19:42:21.384Z" presentationTime="11446272"/>
            <Representation id="5_A_audio_17504386117102112482" bandwidth="128000" audioSamplingRate="44100"/>
        </AdaptationSet>
    </Period>
    <Period start="PT4M20.610S" id="main-content_1">
        <AdaptationSet id="1" group="1" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="video" mimeType="video/mp4" codecs="avc1.640020" maxWidth="1280" maxHeight="720" startWithSAP="1">
            <InbandEventStream schemeIdUri="urn:mpeg:dash:event:2012" value="1"/>
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="amssignal"/>
            <SegmentTemplate timescale="90000" presentationTimeOffset="23454931" media="QualityLevels($Bandwidth$)/Fragments(video=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(video=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="23454932" d="58558"/>
                    <S d="4504"/>
                    <S d="130631"/>
                    <S d="135135" r="12"/>
                </SegmentTimeline>
            </SegmentTemplate>
            <ProducerReferenceTime id="23454932" type="0" wallClockTime="2020-01-07T19:42:22.442Z" presentationTime="23454932"/>
            <Representation id="1_V_video_5322324134428436312" bandwidth="3500000" width="1280" height="720"/>
            <Representation id="1_V_video_16981495139092747609" bandwidth="2200000" width="960" height="540"/>
            <Representation id="1_V_video_1384718563016940751" bandwidth="1350000" codecs="avc1.64001F" width="704" height="396"/>
            <Representation id="1_V_video_4425970933904124207" bandwidth="850000" codecs="avc1.64001E" width="512" height="288"/>
            <Representation id="1_V_video_11952982975776937431" bandwidth="550000" codecs="avc1.640016" width="384" height="216"/>
            <Representation id="1_V_video_10673801877453424365" bandwidth="200000" codecs="avc1.640015" width="340" height="192"/>
        </AdaptationSet>
        <AdaptationSet id="2" group="5" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="audio" mimeType="audio/mp4" codecs="mp4a.40.5" lang="en">
            <InbandEventStream schemeIdUri="urn:mpeg:dash:event:2012" value="1"/>
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="amssignal"/>
            <Label>audio</Label>
            <SegmentTemplate timescale="44100" presentationTimeOffset="11492916" media="QualityLevels($Bandwidth$)/Fragments(audio=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(audio=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="11495424" d="28672"/>
                    <S d="1024"/>
                    <S d="131072"/>
                    <S d="132096"/>
                    <S d="133120"/>
                    <S d="132096" r="1"/>
                    <S d="133120"/>
                </SegmentTimeline>
            </SegmentTemplate>
            <ProducerReferenceTime id="11495424" type="0" wallClockTime="2020-01-07T19:42:22.499Z" presentationTime="11495424"/>
            <Representation id="5_A_audio_17504386117102112482" bandwidth="128000" audioSamplingRate="44100"/>
        </AdaptationSet>
    </Period>
</MPD>

~~~
### <a name="334-mpeg-dash-in-band-event-message-box-signaling"></a>3.3.4 MPEG DASH Sygnalizacja skrzynki komunikatów w paśmie

Strumień zdarzeń w paśmie wymaga MPD mieć InbandEventStream element na poziomie zestawu adaptacji.  Ten element ma atrybut obowiązkowe schemeIdUri i opcjonalny atrybut skali czasu, które również pojawiają się w oknie komunikatu zdarzenia ('emsg').  Pola komunikatów zdarzeń z identyfikatorami schematu, które nie są zdefiniowane w mpd **nie powinny** być obecne.

Dla wózka w paśmie [SCTE-35] sygnały **MUSZĄ** korzystać z schemeId = "urn:scte:scte35:2013:bin".
Normatywne definicje przewozu komunikatów w paśmie [SCTE-35] są zdefiniowane w [SCTE-214-3] s 7.3.2 (Przewóz komunikatów sygnalizacji SCTE 35).

Poniższe szczegóły przedstawiają konkretne wartości, których klient powinien oczekiwać w "emsg" zgodnie z [SCTE-214-3]:

| **Nazwa pola**          | **Typ pola**          | **Wymagane?** | **Opis**                                                                                                                                                                                                                                                                                        |
| ----------------------- | ----------------------- | ------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| scheme_id_uri           | ciąg                  | Wymagany      | Identyfikuje schemat wiadomości. Schemat jest ustawiony na wartość atrybutu Scheme w polu Manifest live server. Wartość **MUSI** być URN identyfikacji schematu wiadomości. Dla [SCTE-35] wiadomości, to **musi** być "urn:scte:scte35:2013:bin" zgodnie z [SCTE-214-3]          |
| Wartość                   | ciąg                  | Wymagany      | Dodatkowa wartość ciągu używana przez właścicieli schematu do dostosowywania semantyki wiadomości. Aby odróżnić wiele strumieni zdarzeń z tym samym schematem, wartość zostanie ustawiona na nazwę strumienia zdarzeń (trackName for Smooth ingest lub NAZWA KOMUNIKATU AMF dla pozyskiwania RTMP). |
| Skala czasu               | 32-bitowa niepodpisana gnilizna | Wymagany      | Skala czasu w znacznikach na sekundę pól czasów i czasu trwania w polu 'emsg'.                                                                                                                                                                                                            |
| Presentation_time_delta | 32-bitowa niepodpisana gnilizna | Wymagany      | Czas prezentacji mediów delta czasu prezentacji wydarzenia i najwcześniejszego czasu prezentacji w tym segmencie. Czas prezentacji i czas trwania **powinny być** zgodne z punktami dostępu strumieniowego (SAP) typu 1 lub 2, zgodnie z definicją zawartą w załączniku I [ISO-14496-12].                                  |
| event_duration          | 32-bitowa niepodpisana gnilizna | Wymagany      | Czas trwania zdarzenia lub 0xFFFFFFFFFF, aby wskazać nieznany czas trwania.                                                                                                                                                                                                                              |
| Identyfikator                      | 32-bitowa niepodpisana gnilizna | Wymagany      | Identyfikuje to wystąpienie wiadomości. Wiadomości z równoważną semantyką mają taką samą wartość. Jeśli identyfikator nie jest określony podczas pozyskiwania wiadomości, usługa Azure Media Services wygeneruje unikatowy identyfikator.                                                                                        |
| Message_data            | tablica bajtów              | Wymagany      | Komunikat o zdarzeniu. W przypadku komunikatów [SCTE-35] dane wiadomości są splice_info_section() binarnym zgodnie z przepisami [SCTE-214-3]                                                                                                                                                                        |


#### <a name="example-inbandevenstream-entity-for-adobe-simple-mode"></a>Przykładowa jednostka InBandEvenStream dla trybu Adobe Simple
~~~ xml

      <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="amssignal"/>
~~~

### <a name="335-dash-message-handling"></a>3.3.5 Obsługa komunikatów DASH

Wydarzenia są sygnalizowane w paśmie, w polu 'emsg', zarówno dla ścieżek wideo, jak i audio.  Sygnalizacja występuje dla wszystkich żądań segmentu, dla których presentation_time_delta wynosi 15 sekund lub mniej. 

Gdy okno prezentacji przesuwnej jest włączone, komunikaty o zdarzeniach są usuwane z mpd, gdy suma czasu i czasu trwania komunikatu zdarzenia jest mniejsza niż czas danych multimedialnych w manifeście.  Innymi słowy komunikaty o zdarzeniu są usuwane z manifestu, gdy czas nośnika, do którego się odnoszą, został wytoczyny z okna prezentacji przesuwnej.

## <a name="4-scte-35-ingest-implementation-guidance-for-encoder-vendors"></a>4. SCTE-35 Ingest Wytyczne dotyczące wdrażania dla dostawców koderów

Poniższe wskazówki są typowe problemy, które mogą mieć wpływ na implementację tej specyfikacji przez dostawcę kodera.  Poniższe wytyczne zostały zebrane na podstawie opinii partnerów ze świata rzeczywistego, aby ułatwić wdrożenie tej specyfikacji dla innych. 

[SCTE-35] wiadomości są spożywane w formacie binarnym przy użyciu schematu **"urn:scte:scte35:2013:bin"** dla [MS-SSTR-Ingest] i typu **"scte35"** dla [RTMP] pozyskiwania. Aby ułatwić konwersję czasu [SCTE-35], który jest oparty na sygnaturach czasowych prezentacji strumienia transportu MPEG-2 (PTS), mapowanie między PTS (pts_time + pts_adjustment splice_time()) a osią czasu prezentacji nośnika jest dostarczane przez czas prezentacji wydarzenia ( pole fragment_absolute_time dla płynnego pozyskiwania i pole czasowe dla spożycia RTMP). Mapowanie jest konieczne, ponieważ 33-bitowa wartość PTS jest przetaczana co około 26,5 godziny.

Płynne przesyłanie strumieniowe [MS-SSTR-Ingest] wymaga, aby pole danych multimediów ('mdat') **musi** zawierać **splice_info_section()** zdefiniowane w [SCTE-35]. 

W przypadku pozyskiwania RTMP atrybut cue wiadomości AMF jest ustawiony na **splice_info_section()** zakodowany w kodowaniu base64() zdefiniowany w [SCTE-35].  

Gdy wiadomości mają format opisany powyżej, są wysyłane do klientów HLS, Smooth i DASH, jak zdefiniowano powyżej.  

Podczas testowania implementacji za pomocą platformy Azure Media Services, należy rozpocząć testowanie z "pass-through" LiveEvent pierwszy, przed przejściem do testowania na kodowanie LiveEvent.

---

## <a name="change-history"></a>Historia zmian

| Data     | Zmiany                                                                                                             |
| -------- | ------------------------------------------------------------------------------------------------------------------- |
| 07/2/19  | Poprawiono łechtaniec RTMP dla obsługi SCTE35, dodał RTMP "onCuePoint" dla Elemental Live                                  |
| 08/22/19 | Zaktualizowano, aby dodać OnUserDataEvent do RTMP dla metadanych niestandardowych                                                          |
| 1/08/20  | Naprawiono błąd w trybie RTMP Simple i RTMP SCTE35. Zmieniono z "onCuePoint" na "onAdCue". Zaktualizowano tabelę trybu prostego. |

## <a name="next-steps"></a>Następne kroki
Wyświetlanie ścieżek szkoleniowych usługi Media Services.

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
