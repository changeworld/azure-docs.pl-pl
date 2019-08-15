---
title: Przesyłanie strumieniowe metadanych Azure Media Services Microsoft Docs
description: Ta specyfikacja zawiera opis metod sygnalizujących metadane czasowe podczas pozyskiwania i przesyłania strumieniowego do Azure Media Services. Obejmuje to obsługę ogólnych sygnałów metadanych (ID3), a także SCTE-35 sygnalizowanie dla operacji wstawiania AD i nałączenia warunków.
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
ms.date: 07/2/2019
ms.author: johndeu
ms.openlocfilehash: 444d5ca996c014bdbf2e62cacf2563c7b63372e4
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/13/2019
ms.locfileid: "69015720"
---
# <a name="signaling-timed-metadata-in-live-streaming"></a>Sygnalizowanie metadanych w czasie przesyłania strumieniowego na żywo 

Ostatnia aktualizacja: 2019-07-02

### <a name="conformance-notation"></a>Notacja zgodności

Kluczowe słowa "musi", "nie może być", "wymagane", ",", ",", "," powinny "," nie powinno być "," zalecane "," maj "i" opcjonalne "w tym dokumencie są interpretowane zgodnie z opisem w dokumencie RFC 2119

## <a name="1-introduction"></a>1. Wprowadzenie 

Aby sygnalizować Wstawianie anonsów lub niestandardowych zdarzeń metadanych w odtwarzaczu klienta, nadawcy często używają metadanych czasowych osadzonych w filmie wideo. Aby włączyć te scenariusze, Media Services zapewnia obsługę transportu metadanych czasowych z punktu pozyskiwania kanału przesyłania strumieniowego na żywo do aplikacji klienckiej.
Ta specyfikacja zawiera kilka trybów, które są obsługiwane przez Media Services dla metadanych czasowych w ramach sygnałów przesyłania strumieniowego na żywo.

1. [SCTE-35] sygnalizowanie zgodne ze standardami opisanymi przez [SCTE-35], [SCTE-214-1], [SCTE-214-3] i [RFC8216]

2. [SCTE-35] sygnalizowanie zgodne ze specyfikacją starszej wersji [Adobe-Primetime] dla sygnałów usługi AD RTMP.
   
3. Ogólny tryb sygnalizowania metadanych w przypadku wiadomości, które **nie** są [SCTE-35] i mogą zawierać [ID3v2] lub inne niestandardowe schematy zdefiniowane przez dewelopera aplikacji.

## <a name="11-terms-used"></a>1,1 warunków użytkowania

| Termin              | Definicja |
|-------------------|------------|
| Przerwanie usługi AD          | Lokalizacja lub punkt w czasie, w którym co najmniej jedna usługa ads może zostać zaplanowana do dostarczenia; taka sama jak w przypadku możliwości dostępn i umieszczania. |
| Usługa decyzji usługi AD| Usługa zewnętrzna, która decyduje, które reklamy i czas trwania będą widoczne dla użytkownika. Usługi są zwykle udostępniane przez partnera i poza zakresem tego dokumentu.|
| Kontrol               | Wskazanie czasu i parametrów nadchodzącego przerwania usługi AD. Należy zauważyć, że wskaźniki mogą wskazywać oczekujące przełączenie do przerwania usługi AD, oczekujące przełączenie do następnej usługi AD w ramach przerwy w usłudze AD i oczekujące przełączenie z usługi AD do głównej zawartości. |
| Pakowarki          | Azure Media Services "punkt końcowy przesyłania strumieniowego" zapewnia dynamiczne możliwości tworzenia pakietów dla ŁĄCZNIKów i HLS oraz jest określana jako "Pakowarka" w branży multimedialnej. 
| Godzina prezentacji | Godzina, o której zdarzenie jest prezentowane w przeglądarce. Czas przedstawia chwilę na osi czasu nośnika, w której podgląd zobaczy zdarzenie. Na przykład czas prezentacji komunikatu polecenia SCTE-35 splice_info () to splice_time (). |
| Czas przybycia      | Godzina nadejścia komunikatu o zdarzeniu. Czas jest zwykle różny od czasu prezentacji zdarzenia, ponieważ komunikaty zdarzeń są wysyłane przed czasem prezentacji zdarzenia.                                     |
| Ścieżka rozrzedzona      | ścieżka nośnika, która nie jest ciągła i jest czas synchronizowana z ścieżką nadrzędną lub śledzeniem kontrolek.                                                                                                                                    |
| Źródło            | Usługa przesyłania strumieniowego multimediów Azure                                                                                                                                                                                                |
| Ujścia kanału      | Usługa Azure Media Live Streaming                                                                                                                                                                                           |
| HLS               | Protokół Apple HTTP Live Streaming                                                                                                                                                                                               |
| DASH              | Dynamiczne adaptacyjne przesyłanie strumieniowe za pośrednictwem protokołu HTTP                                                                                                                                                                                             |
| Płynne            | Protokół Smooth Streaming                                                                                                                                                                                                        |
| MPEG2-TS          | Strumienie transportowe MPEG 2                                                                                                                                                                                                         |
| RTMP              | Protokół multimedialny w czasie rzeczywistym                                                                                                                                                                                                    |
| uimsbf            | Liczba całkowita bez znaku, najbardziej znaczący bit.                                                                                                                                                                                    |

---

## <a name="12-normative-references"></a>1,2 odwołania normatywne

Następujące dokumenty zawierają postanowienia, które za pomocą odwołania w tym tekście stanowią postanowienia tego dokumentu. Wszystkie dokumenty podlegają zmianom w treści standardów, a czytelnicy są zachęcani do zbadania możliwości zastosowania najnowszych wersji dokumentów wymienionych poniżej. Czytelnicy są również przypomnili, że nowsze wersje dokumentów, do których istnieją odwołania, mogą nie być zgodne z tą wersją specyfikacji metadanych czasowych dla Azure Media Services.


|Standardowa (Standard)  |Definicja  |
|---------|---------|
|[Adobe-Primetime] | [Specyfikacja sygnalizowania Primetime Digital Programs 1,2](https://www.adobe.com/content/dam/acom/en/devnet/primetime/PrimetimeDigitalProgramInsertionSignalingSpecification.pdf) |
|[Adobe-Flash AS] | [Dokumentacja języka FLASH ActionScript](https://help.adobe.com/archive/en_US/as2/flashlite_2.x_3.x_aslr.pdf) |
| [AMF0]            | ["Format komunikatu akcji AMF0"](https://download.macromedia.com/pub/labs/amf/amf0_spec_121207.pdf) |
| [ŁĄCZNIK-IF-REDUKCJA]     | KRESKOWANY forum branżowe wskazówki dotyczące międzyoperacyjności v 4,2[https://dashif-documents.azurewebsites.net/DASH-IF-IOP/master/DASH-IF-IOP.html](https://dashif-documents.azurewebsites.net/DASH-IF-IOP/master/DASH-IF-IOP.html) |
| [HLS-TMD]         | Metadane czasu dla HTTP Live Streaming-[https://developer.apple.com/streaming](https://developer.apple.com/streaming) |
| [ID3v2]           | 2\.4.0 tag ID3 wersja[http://id3.org/id3v2.4.0-structure](http://id3.org/id3v2.4.0-structure) |
| [ISO-14496-12]    | ISO/IEC 14496-12: Część 12 format podstawowego pliku multimedialnego ISO, FourthEdition 2012-07-15  |
| [MPEGDASH]        | Technologia informacyjna — dynamiczne adaptacyjne przesyłanie strumieniowe za pośrednictwem protokołu HTTP (KRESKa) — część 1: Opis prezentacji multimediów i formaty segmentów. 2014 maja. Publikacj. ADRES URL: https://www.iso.org/standard/65274.html |
| [MPEGCMAF]        | Technologia informacyjna — format aplikacji multimedialnej (MPEG-A) — część 19: Typowy format aplikacji multimedialnej (CMAF) dla nośnika z segmentacją. Styczeń 2018. Publikacj. ADRES URL: https://www.iso.org/standard/71975.html |
| [MPEGCENC]        | Technologia informacyjna — technologie dla systemu MPEG — część 7: Typowym szyfrowaniem w plikach ISO Base File Format. Luty 2016. Publikacj. ADRES URL: https://www.iso.org/standard/68042.html |
| [MS-SSTR]         | ["Microsoft Smooth Streaming Protocol", 15 maja 2014](https://docs.microsoft.com/openspecs/windows_protocols/ms-sstr/8383f27f-7efe-4c60-832a-387274457251) |
| [MS-SSTR-pozyskiwanie]  | [Azure Media Services pofragmentowana Specyfikacja pozyskiwania na żywo w formacie MP4](https://docs.microsoft.com/azure/media-services/media-services-fmp4-live-ingest-overview) |
| [RFC8216]         | R. Pantos, Ed.; W. mogą. HTTP Live Streaming. 2017 sierpnia. Informacyjną. [https://tools.ietf.org/html/rfc8216](https://tools.ietf.org/html/rfc8216) |
| [RFC4648]         |Kodowanie danych Base16, Base32 i base64 —[https://tools.ietf.org/html/rfc4648](https://tools.ietf.org/html/rfc4648) |
| RTMP            |["Protokół obsługi komunikatów w czasie rzeczywistym firmy Adobe", 21 grudnia 2012](https://www.adobe.com/devnet/rtmp.html)  |
| [SCTE-35-2019]    | SCTE 35: 2019 — wiadomość cueing do wstawiania programów cyfrowych dla kabla https://www.scte.org/SCTEDocs/Standards/ANSI_SCTE%2035%202019r1.pdf  |
| [SCTE-214-1]      | SCTE 214-1 2016 — KRESKa MPEG dla usług kablowych opartych na protokole IP część 1: Ograniczenia i rozszerzenia MPD |
| [SCTE-214-3]      | SCTE 214-3 2015 MPEG PAUZy dla usług kabli opartych na protokole IP część 3: Profil KRESKi/FF |
| [SCTE-224]        | SCTE 224 2018r1 — planowanie zdarzeń i interfejs powiadamiania |
| [SCTE-250]        | Interfejs API zarządzania zdarzeniami i sygnalizacją (ESAM) |

---


## <a name="2-timed-metadata-ingest"></a>2. Pozyskiwanie metadanych z limitem czasu

## <a name="21-rtmp-ingest"></a>2,1 RTMP

[RTMP] zezwala na wysyłanie sygnałów metadanych z limitem czasu jako [AMF0] komunikatów kontrolnych osadzonych w strumieniu [RTMP]. Komunikaty kontrolne mogą być wysyłane jakiś czas przed wystąpieniem zdarzenia "AD splice" lub "[SCTE35]". W celu obsługi tego scenariusza rzeczywista godzina zdarzenia jest wysyłana w ramach komunikatu wskaźnika. Aby uzyskać więcej informacji, zobacz [AMF0].

W poniższych tabelach opisano format ładunku komunikatu AMF, który Media Services zostanie wyzyskany dla trybów komunikatów "Simple" i [SCTE35].

Nazwa komunikatu [AMF0] może być używana do rozróżniania wielu strumieni zdarzeń tego samego typu.  W przypadku komunikatów [SCTE-35] i trybu "Simple" nazwa komunikatu AMF musi być typu "onAdCue", jak jest to wymagane w specyfikacji [Adobe-Primetime].  Wszelkie pola, które nie są wymienione poniżej, zostaną zignorowane przez Azure Media Services w trakcie pozyskiwania.

## <a name="211-rtmp-signal-syntax"></a>2.1.1 — składnia sygnału RTMP

Azure Media Services może nasłuchiwać i odpowiadać na kilka typów komunikatów [AMF0], które mogą służyć do sygnalizowania różnych metadanych zsynchronizowanych w czasie rzeczywistym w strumieniu na żywo.  Specyfikacja [Adobe-Primetime] definiuje dwa typy sygnalizacji o nazwie "Simple" i "SCTE-35". W przypadku trybu "Simple" Media Services obsługuje pojedyncze AMF komunikat o nazwie "onAdCue" przy użyciu ładunku zgodnego z tabelą poniżej zdefiniowanej dla sygnału "tryb prosty".  

W poniższej sekcji przedstawiono ładunek protokołu RTMP "Simple" Mode, który może służyć do sygnalizowania podstawowego sygnału usługi AD "spliceOut", który będzie przenoszony do manifestu klienta dla HLS, ŁĄCZNIKa i Microsoft Smooth Streaming. Jest to bardzo przydatne w scenariuszach, w których klient nie ma złożonego wdrożenia usługi AD lub systemu wstawiania opartego na SCTE-35 i używa podstawowego kodera lokalnego do wysyłania wiadomości kontrolnej za pośrednictwem interfejsu API. Zwykle koder lokalny będzie obsługiwał interfejs API oparty na usłudze REST, aby wyzwolić ten sygnał, który również będzie "odfiltrować" strumień wideo, wstawiając ramkę IDR do filmu wideo i rozpoczynając nową grupę GOP.

## <a name="212--simple-mode-ad-signaling-with-rtmp"></a>2.1.2 tryb proste AD sygnalizowanie przy użyciu protokołu RTMP

| Nazwa pola | Typ pola | Wymagane? | Opisy                                                                                                             |
|------------|------------|----------|--------------------------------------------------------------------------------------------------------------------------|
| kontrol        | String     | Wymagane | Komunikat zdarzenia.  Powinien być "SpliceOut", aby wyznaczyć Metoda łączenia w trybie prostym.                                              |
| id         | String     | Wymagane | Unikatowy identyfikator opisujący metody łączenia lub segmentów. Identyfikuje to wystąpienie komunikatu                            |
| duration   | Number     | Wymagane | Czas trwania łączenia. Jednostki są ułamkami sekund.                                                                |
| elapsed    | Number     | Optional | Gdy sygnał jest powtarzany w celu obsługi dostrajania w, to pole jest ilością czasu prezentacji, który upłynął od początku metody łączenia. Jednostki są ułamkami sekund. W przypadku korzystania z trybu prostego ta wartość nie powinna przekroczyć oryginalnego czasu trwania łączenia.                                                  |
| time       | Number     | Wymagane | Jest to godzina łączenia w czasie prezentacji. Jednostki są ułamkami sekund.                                     |

---
 
## <a name="213-scte-35-mode-ad-signaling-with-rtmp"></a>2.1.3 SCTE-35 tryb usługi AD sygnalizowanie przy użyciu protokołu RTMP

Podczas pracy z bardziej zaawansowanym przepływem pracy produkcyjnym, który wymaga przeprowadzenia pełnego komunikatu ładunku SCTE-35 do manifestu HLS lub PAUZy, najlepiej jest używać trybu "SCTE-35" w specyfikacji [Adobe-Primetime].  Ten tryb obsługuje w-paśmie sygnały SCTE-35 wysyłane bezpośrednio do lokalnego kodera na żywo, który następnie koduje sygnały w strumieniu RTMP przy użyciu trybu "SCTE-35" określonego w specyfikacji [Adobe-Primetime]. 

Zazwyczaj komunikaty SCTE-35 mogą być wyświetlane tylko w danych wejściowych strumienia (TS) w formacie MPEG-2 w koderie lokalnym. Skontaktuj się z producentem kodera, aby uzyskać szczegółowe informacje na temat sposobu konfigurowania pozyskiwania strumienia transportowego, który zawiera SCTE-35 i włącz go na potrzeby przekazywania do usługi RTMP w trybie Adobe SCTE-35.

W tym scenariuszu należy wysłać następujący ładunek z kodera lokalnego przy użyciu typu komunikatu **"onAdCue"** [AMF0].

| Nazwa pola | Typ pola | Wymagane? | Opisy                                                                                                             |
|------------|------------|----------|--------------------------------------------------------------------------------------------------------------------------|
| kontrol        | String     | Wymagane | Komunikat zdarzenia.  W przypadku komunikatów [SCTE-35] musi to być kod binarny szyfrowany algorytmem Base64 [RFC4648] (), aby komunikaty były wysyłane do klientów HLS, gładkich i kresowych.                                              |
| type       | String     | Wymagane | Nazwa URN lub adres URL identyfikujący schemat komunikatów. W przypadku komunikatów [SCTE-35] **powinno** to być **"scte35"** , aby komunikaty były wysyłane do klientów HLS, gładkich i kresowych, w zgodności z [Adobe-Primetime]. Opcjonalnie można również użyć nazwy URN: SCTE: scte35:2013: bin, aby sygnalizować komunikat [SCTE-35]. |
| id         | String     | Wymagane | Unikatowy identyfikator opisujący metody łączenia lub segmentów. Identyfikuje to wystąpienie komunikatu.  Komunikaty o równoważnej semantyce mają tę samą wartość.|
| duration   | Number     | Wymagane | Czas trwania zdarzenia lub segmentu łączenia AD, jeśli jest znany. Jeśli nieznany, wartość **powinna** być równa 0.                                                                 |
| elapsed    | Number     | Optional | Gdy sygnał usługi AD [SCTE-35] jest powtarzany w celu dostrojenia, to pole jest ilością czasu prezentacji, który upłynął od rozpoczęcia łączenia. Jednostki są ułamkami sekund. W trybie [SCTE-35] Ta wartość może przekroczyć pierwotny określony czas trwania łączenia lub segmentu.                                                  |
| time       | Number     | Wymagane | Czas prezentacji zdarzenia lub łączenia z usługą AD.  Czas prezentacji i czas trwania **powinny być** wyrównane z punktami dostępu strumienia (SAP) typu 1 lub 2, zgodnie z definicją w załączniku I [ISO-14496-12]. Dla ruchu wychodzącego HLS, czas i czas trwania **powinny być** wyrównane z granicami segmentów. Czas prezentacji i czas trwania różnych komunikatów o zdarzeniach w ramach tego samego strumienia zdarzeń nie mogą nakładać się na siebie. Jednostki są ułamkami sekund.

---
## <a name="214-elemental-live-oncuepoint-ad-markers-with-rtmp"></a>Znaczniki AD "onCuePoint" "

Dynamiczny koder on-premises Encoder obsługuje znaczniki usługi AD w sygnale RTMP. Azure Media Services obecnie obsługuje tylko typ znacznika "onCuePoint" dla protokołu RTMP.  Tę funkcję można włączyć w ustawieniach grupy firmy Adobe RTMP w ustawieniach usługi Media Encoder na żywo lub w interfejsie API, ustawiając wartość "**ad_markers**" na "onCuePoint".  Aby uzyskać szczegółowe informacje, zapoznaj się z dokumentacją na żywo. Włączenie tej funkcji w grupie RTMP spowoduje przekazanie sygnałów SCTE-35 do danych wyjściowych Adobe RTMP do przetworzenia przez Azure Media Services.

Typ komunikatu "onCuePoint" jest zdefiniowany w [Adobe-Flash-AS] i ma następującą strukturę ładunku, gdy jest wysyłana z elementów danych wyjściowych na żywo.


| Właściwość  |Opis  |
|---------|---------|
|  name     | Nazwa powinna mieć wartość "**scte35**" przez element aktywny. |
|time     |  Czas (w sekundach), po którym punkt kontrolny wystąpił w pliku wideo podczas osi czasu |
| type     | Typ punktu kontrolnego powinien mieć ustawioną wartość "**Event**". |
| parameters | Tablica asocjacyjna ciągów par nazwa/wartość zawierająca informacje z komunikatu SCTE-35, w tym identyfikator i czas trwania. Te wartości są analizowane przez Azure Media Services i zawarte w znaczniku dekoracji manifestu.  |


Gdy jest używany ten tryb znacznika AD, dane wyjściowe manifestu HLS są podobne do trybu "Simple" programu Adobe. 

### <a name="215-cancellation-and-updates"></a>Aktualizacja 2.1.5 i anulowanie

Komunikaty można anulować lub zaktualizować, wysyłając wiele komunikatów z tym samym czasem prezentacji i IDENTYFIKATORem. Czas prezentacji i identyfikator jednoznacznie identyfikują zdarzenie, a ostatni komunikat odebrany dla określonego czasu prezentacji, który spełnia ograniczenia przed przystąpieniem, jest komunikatem, na którym działa. Zaktualizowane zdarzenie zastępuje wszystkie wcześniej odebrane komunikaty. Ograniczenie przed przyrzutem to cztery sekundy. Komunikaty odebrane co najmniej cztery sekundy przed czasem prezentacji zostaną podjęte.

## <a name="22-fragmented-mp4-ingest-smooth-streaming"></a>2,2 fragmentacja MP4 (Smooth Streaming)

Zapoznaj się z artykułem [MS-SSTR-pozyskiwanie], aby uzyskać wymagania dotyczące pozyskiwania strumieniowego na żywo. Poniższe sekcje zawierają szczegółowe informacje dotyczące pozyskiwania metadanych prezentacji czasowej.  Metadane prezentacji czasowej są pozyskiwane jako ścieżka rozrzedzona, która jest definiowana w polu manifestu serwera na żywo (zobacz MS-SSTR) i pole filmu ("Moov").  

Każdy fragment rozrzedzony składa się z pola fragmentu filmu ("moof") i nośnika urządzenie Data Box ("MDAT"), gdzie pole "MDAT" jest wiadomością binarną.

Aby osiągnąć dokładne wstawianie reklam, koder musi podzielić fragment w czasie prezentacji, w którym należy wstawić wskaźnik.  NALEŻY utworzyć nowy fragment, który rozpoczyna się od nowo utworzonej ramki IDR, lub do strumienia punktów dostępu (SAP) typu 1 lub 2, zgodnie z definicją w załączniku I [ISO-14496-12]. Dzięki temu Pakowarka multimediów platformy Azure prawidłowo generuje manifest HLS i pętlę Wieloetapową, w której nowy okres zaczyna się od dokładnej godziny łączenia z warunkiem.

### <a name="221-live-server-manifest-box"></a>Pole manifestu aktywnego serwera

Ścieżka rozrzedzona **musi** być zadeklarowana w polu manifestu serwera na żywo z **\<\>** wpisem textstream i **musi** mieć ustawione następujące atrybuty:

| **Nazwa atrybutu** | **Typ pola** | **Wymagane?** | **Opis**                                                                                                                                                                                                                                                 |
|--------------------|----------------|---------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| systemBitrate      | Number         | Wymagane      | **Musi** mieć wartość "0", co oznacza, że ścieżka z nieznaną zmienną szybkością transmisji bitów.                                                                                                                                                                                                 |
| parentTrackName    | String         | Wymagane      | **Musi** być nazwą ścieżki nadrzędnej, do której są wyrównane Skala czasu ścieżki rozrzedzonej. Ścieżka nadrzędna nie może być ścieżką rozrzedzoną.                                                                                                                    |
| manifestOutput     | Boolean        | Wymagane      | **Musi** mieć wartość "true", aby wskazać, że ścieżka rozrzedzona zostanie osadzona w niezakłóconym manifeście klienta.                                                                                                                                                               |
| Podtyp            | String         | Wymagane      | **Musi** to być czterocyfrowy kod znaku "Data".                                                                                                                                                                                                                        |
| Równaniu             | String         | Wymagane      | **Musi** być identyfikatorem urn lub adresem URL identyfikującym schemat komunikatów. W przypadku komunikatów [SCTE-35] **musi** to być "urn: SCTE: scte35:2013: bin", aby komunikaty były wysyłane do programu HLS, gładkie i przerywane klientów zgodne z programem [SCTE-35]. |
| ścieżka śledzenia          | String         | Wymagane      | **Musi** to być nazwa ścieżki rozrzedzonej. Wartość trackname może być używana do rozróżniania wielu strumieni zdarzeń z tym samym schematem. Każdy unikatowy strumień zdarzeń **musi** mieć unikatową nazwę ścieżki.                                                                           |
| timescale          | Number         | Optional      | **Musi** to być Skala czasu ścieżki nadrzędnej.                                                                                                                                                                                                                      |

---

### <a name="222-movie-box"></a>2.2.2 pole filmu

Pole filmu ("Moov") następuje po polu manifestu serwera na żywo jako część nagłówka strumienia dla ścieżki rozrzedzonej.

Pole "Moov" **powinno** zawierać pole **TrackHeaderBox ("tkhd")** zdefiniowane w [ISO-14496-12] z następującymi ograniczeniami:

| **Nazwa pola** | **Typ pola**          | **Wymagane?** | **Opis**                                                                                                |
|----------------|-------------------------|---------------|----------------------------------------------------------------------------------------------------------------|
| duration       | 64-bitowa liczba całkowita bez znaku | Wymagane      | **Powinna** mieć wartość 0, ponieważ pole śledzenia ma zero próbek i łączny czas trwania próbek w polu śledzenia wynosi 0. |

---

Pole "Moov" **powinno** zawierać element **HandlerBox ("HDLR")** , jak zdefiniowano w [ISO-14496-12] z następującymi ograniczeniami:

| **Nazwa pola** | **Typ pola**          | **Wymagane?** | **Opis**   |
|----------------|-------------------------|---------------|-------------------|
| handler_type   | 32-bitowa liczba całkowita bez znaku | Wymagane      | **Powinien** być "meta". |

---

Pole "stsd" **powinno** zawierać pole MetaDataSampleEntry z nazwą kodową zdefiniowaną w [ISO-14496-12].  Na przykład w przypadku komunikatów SCTE-35 nazwa kodowania **powinna** mieć wartość "SCTE".

### <a name="223-movie-fragment-box-and-media-data-box"></a>Pole fragmentu filmu i urządzenie Data Box multimediów

Fragmenty ścieżki rozrzedzonej składają się z pola fragmentu filmu ("moof") i nośnika urządzenie Data Box ("MDAT").

> [!NOTE]
> Aby osiągnąć dokładne wstawianie reklam, koder musi podzielić fragment w czasie prezentacji, w którym należy wstawić wskaźnik.  NALEŻY utworzyć nowy fragment, który rozpoczyna się od nowo utworzonej ramki IDR, lub do strumienia punktów dostępu (SAP) typu 1 lub 2, jak zdefiniowano w [ISO-14496-12] załącznik I
> 

Pole MovieFragmentBox ("moof") **musi** zawierać pole **TrackFragmentExtendedHeaderBox ("UUID")** zdefiniowane w [MS-SSTR] z następującymi polami:

| **Nazwa pola**         | **Typ pola**          | **Wymagane?** | **Opis**                                                                               |
|------------------------|-------------------------|---------------|-----------------------------------------------------------------------------------------------|
| fragment_absolute_time | 64-bitowa liczba całkowita bez znaku | Wymagane      | **Musi** być czasem przybycia zdarzenia. Ta wartość wyrównuje komunikat z ścieżką nadrzędną.   |
| fragment_duration      | 64-bitowa liczba całkowita bez znaku | Wymagane      | **Musi** to być czas trwania zdarzenia. Czas trwania może być równy zero, aby wskazać, że czas trwania jest nieznany. |

---


Pole MediaDataBox ("MDAT") **musi** mieć następujący format:

| **Nazwa pola**          | **Typ pola**                   | **Wymagane?** | **Opis**                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
|-------------------------|----------------------------------|---------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| version                 | 32-bitowa liczba całkowita bez znaku (uimsbf) | Wymagane      | Określa format zawartości pola "MDAT". Nierozpoznane wersje zostaną zignorowane. Obecnie jedyną obsługiwaną wersją jest 1.                                                                                                                                                                                                                                                                                                                                                      |
| id                      | 32-bitowa liczba całkowita bez znaku (uimsbf) | Wymagane      | Identyfikuje to wystąpienie komunikatu. Komunikaty o równoważnej semantyce muszą mieć taką samą wartość; oznacza to, że przetwarzanie dowolnego jednego okna komunikatu o tym samym identyfikatorze jest wystarczające.                                                                                                                                                                                                                                                                                                            |
| presentation_time_delta | 32-bitowa liczba całkowita bez znaku (uimsbf) | Wymagane      | Suma fragment_absolute_time określona w TrackFragmentExtendedHeaderBox, a presentation_time_delta **musi** być czasem prezentacji zdarzenia. Czas prezentacji i czas trwania **powinny być** wyrównane z punktami dostępu strumienia (SAP) typu 1 lub 2, zgodnie z definicją w załączniku I [ISO-14496-12]. Dla ruchu wychodzącego HLS, czas i czas trwania **powinny być** wyrównane z granicami segmentów. Czas prezentacji i czas trwania różnych komunikatów o zdarzeniach w ramach tego samego strumienia zdarzeń nie **mogą** nakładać się na siebie. |
| message                 | tablica bajtów                       | Wymagane      | Komunikat zdarzenia. W przypadku komunikatów [SCTE-35] komunikat jest binarny splice_info_section (). W przypadku komunikatów [SCTE-35] **musi** to być splice_info_section (), aby komunikaty były wysyłane do klientów HLS, gładkich i kresowych z zachowaniem zgodności z [SCTE-35]. W przypadku komunikatów [SCTE-35] plik binarny splice_info_section () jest ładunkiem pola "MDAT" i **nie** jest kodowany algorytmem Base64.                                                            |

---


### <a name="224-cancellation-and-updates"></a>Aktualizacja 2.2.4 i aktualizacje

Komunikaty można anulować lub zaktualizować, wysyłając wiele komunikatów z tym samym czasem prezentacji i IDENTYFIKATORem.  Godzina i identyfikator prezentacji jednoznacznie identyfikują zdarzenie. Ostatnia wiadomość odebrana dla określonego czasu prezentacji, która spełnia ograniczenia wstępnego przyrzutu, jest komunikatem, na którym jest wykonywane działanie. Zaktualizowany komunikat zastępuje wszystkie odebrane wcześniej komunikaty.  Ograniczenie przed przyrzutem to cztery sekundy. Komunikaty odebrane co najmniej cztery sekundy przed czasem prezentacji zostaną podjęte. 


## <a name="3-timed-metadata-delivery"></a>3 czasowe dostarczanie metadanych

Dane strumienia zdarzeń są nieprzezroczyste dla Media Services. Media Services jedynie przekazuje trzy informacje między punktem końcowym pozyskiwania a punktem końcowym klienta. Następujące właściwości są dostarczane do klienta programu zgodnie z przepisami [SCTE-35] i/lub protokołem przesyłania strumieniowego klienta:

1.  Schemat — nazwa URN lub adres URL identyfikujący schemat komunikatu.
2.  Czas prezentacji — czas prezentacji zdarzenia na osi czasu nośnika.
3.  Czas trwania — czas trwania zdarzenia.
4.  ID — opcjonalny unikatowy identyfikator zdarzenia.
5.  Message — dane zdarzenia.

## <a name="31-microsoft-smooth-streaming-manifest"></a>3,1 manifest Smooth Streaming firmy Microsoft  

Aby uzyskać szczegółowe informacje na temat formatowania ścieżki komunikatów rozrzedzonych, zapoznaj się z tematem obsługa ścieżki rozrzedzonej [MS-SSTR]. W przypadku komunikatów [SCTE35] Smooth Streaming będzie wyprowadzać splice_info_section () zakodowane algorytmem Base64 () do fragmentu rozrzedzonego.
StreamIndex **musi** mieć PODTYP "Data", a CustomAttributes — **musi** zawierać atrybut o nazwie "Schema" i value = "urn: SCTE: scte35:2013: bin".

#### <a name="smooth-client-manifest-example-showing-base64-encoded-scte35-splice_info_section"></a>Przykładowy przykład manifestu klienta z kodowaniem Base64 [SCTE35] splice_info_section ()
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
  <StreamIndex Type=”text” Name=”scte35-sparse-stream” Subtype=”DATA” Chunks=”0” TimeScale=”10000000”
    ParentStreamIndex=”video” ManifestOutput=”true” 
    Url=”QualityLevels({bitrate})/Fragments(captions={start time})”>
    <QualityLevel Index=”0” Bitrate=”0” CodecPrivateData=”” FourCC=””>
      <CustomAttributes>
        <Attribute Name=”Scheme” Value=”urn:scte:scte35:2013:bin”/>
      </CustomAttributes>
    </QualityLevel>
    <!-- The following <c> and <f> fragments contains the base64-encoded [SCTE35] splice_info_section() message -->
    <c t=”600000000” d=”300000000”>    <f>PD94bWwgdmVyc2lvbj0iMS4wIiBlbmNvZGluZz0idXRmLTgiPz48QWNxdWlyZWRTaWduYWwgeG1sbnM9InVybjpjYWJsZWxhYnM6bWQ6eHNkOnNpZ25hbGluZzozLjAiIGFjcXVpc2l0aW9uUG9pbnRJZGVudGl0eT0iRVNQTl9FYXN0X0FjcXVpc2l0aW9uX1BvaW50XzEiIGFjcXVpc2l0aW9uU2lnbmFsSUQ9IjRBNkE5NEVFLTYyRkExMUUxQjFDQTg4MkY0ODI0MDE5QiIgYWNxdWlzaXRpb25UaW1lPSIyMDEyLTA5LTE4VDEwOjE0OjI2WiI+PFVUQ1BvaW50IHV0Y1BvaW50PSIyMDEyLTA5LTE4VDEwOjE0OjM0WiIvPjxTQ1RFMzVQb2ludERlc2NyaXB0b3Igc3BsaWNlQ29tbWFuZFR5cGU9IjUiPjxTcGxpY2VJbnNlcnQgc3BsaWNlRXZlbnRJRD0iMzQ0NTY4NjkxIiBvdXRPZk5ldHdvcmtJbmRpY2F0b3I9InRydWUiIHVuaXF1ZVByb2dyYW1JRD0iNTUzNTUiIGR1cmF0aW9uPSJQVDFNMFMiIGF2YWlsTnVtPSIxIiBhdmFpbHNFeHBlY3RlZD0iMTAiLz48L1NDVEUzNVBvaW50RGVzY3JpcHRvcj48U3RyZWFtVGltZXM+PFN0cmVhbVRpbWUgdGltZVR5cGU9IkhTUyIgdGltZVZhbHVlPSI1MTUwMDAwMDAwMDAiLz48L1N0cmVhbVRpbWVzPjwvQWNxdWlyZWRTaWduYWw+</f>
    </c>
    <c t=”1200000000” d=”400000000”>      <f>PD94bWwgdmVyc2lvbj0iMS4wIiBlbmNvZGluZz0idXRmLTgiPz48QWNxdWlyZWRTaWduYWwgeG1sbnM9InVybjpjYWJsZWxhYnM6bWQ6eHNkOnNpZ25hbGluZzozLjAiIGFjcXVpc2l0aW9uUG9pbnRJZGVudGl0eT0iRVNQTl9FYXN0X0FjcXVpc2l0aW9uX1BvaW50XzEiIGFjcXVpc2l0aW9uU2lnbmFsSUQ9IjRBNkE5NEVFLTYyRkExMUUxQjFDQTg4MkY0ODI0MDE5QiIgYWNxdWlzaXRpb25UaW1lPSIyMDEyLTA5LTE4VDEwOjE0OjI2WiI+PFVUQ1BvaW50IHV0Y1BvaW50PSIyMDEyLTA5LTE4VDEwOjE0OjM0WiIvPjxTQ1RFMzVQb2ludERlc2NyaXB0b3Igc3BsaWNlQ29tbWFuZFR5cGU9IjUiPjxTcGxpY2VJbnNlcnQgc3BsaWNlRXZlbnRJRD0iMzQ0NTY4NjkxIiBvdXRPZk5ldHdvcmtJbmRpY2F0b3I9InRydWUiIHVuaXF1ZVByb2dyYW1JRD0iNTUzNTUiIGR1cmF0aW9uPSJQVDFNMFMiIGF2YWlsTnVtPSIxIiBhdmFpbHNFeHBlY3RlZD0iMTAiLz48L1NDVEUzNVBvaW50RGVzY3JpcHRvcj48U3RyZWFtVGltZXM+PFN0cmVhbVRpbWUgdGltZVR5cGU9IkhTUyIgdGltZVZhbHVlPSI1MTYyMDAwMDAwMDAiLz48L1N0cmVhbVRpbWVzPjwvQWNxdWlyZWRTaWduYWw+</f>
    </c>
  </StreamIndex>
</SmoothStreamingMedia>
~~~

## <a name="32-apple-hls-manifest-decoration"></a>HLS 3,2

Azure Media Services obsługuje następujące Tagi manifestu HLS do sygnalizowania informacji o Dostie usługi AD podczas zdarzenia na żywo lub na żądanie. 

- EXT-X-DATERANGE zgodnie z definicją w Apple HLS [RFC8216]
- EXT-X-CUE as zdefiniowany w [Adobe-Primetime] — ten tryb jest traktowany jako "Starsza". Klienci powinni przyjąć tag EXT-X-DATERANGE, jeśli jest to możliwe.

Dane wyjściowe danych do poszczególnych tagów będą się różnić w zależności od używanego trybu pozyskiwania sygnału. Na przykład tryb pozyskiwania RTMP przy użyciu rozwiązania Adobe Simple nie zawiera pełnego SCTE-35 zakodowanego w formacie base64.

## <a name="321-apple-hls-with-adobe-primetime-ext-x-daterange-recommended"></a>3.2.1 Apple HLS z Adobe Primetime EXT-X-DATERANGE (zalecane)

Specyfikacja Apple HTTP Live Streaming [RFC8216] umożliwia sygnalizowanie komunikatów [SCTE-35]. Komunikaty są wstawiane do listy odtwarzania segmentów w tagu EXT-X-DATERANGE na [RFC8216] z tytułem "mapowanie SCTE-35 na EXT-X-DATERANGE".  Warstwa aplikacji klienta może analizować listę odtwarzania M3U i przetwarzać Tagi M3U lub otrzymywać zdarzenia za pomocą programu Apple Player Framework.  

**Zalecanym** podejściem w Azure Media Services (interfejs API w wersji 3) jest wykonanie polecenia [RFC8216] i użycie taga EXT-X_DATERANGE dla elementu [SCTE35].

## <a name="322-apple-hls-with-adobe-primetime-ext-x-cue-legacy"></a>3.2.2 Apple HLS z Adobe Primetime EXT-X-CUE (starsza wersja)

Istnieje również implementacja "Starsza" w Azure Media Services (interfejs API w wersji 2 i 3), która używa taga "EXT-X" zdefiniowanego w [Adobe-Primetime] "SCTE-35 Mode". W tym trybie Azure Media Services osadzi zakodowane algorytmem Base64 [SCTE-35] splice_info_section () w tagu EXT-X.  

"Stary" "starszy" tag "EXT-X-CUE" jest zdefiniowany poniżej, a także może być przywoływany w specyfikacji [Adobe-Primetime]. Ta wartość powinna być używana tylko w przypadku starszych sygnałów SCTE35, gdy jest to możliwe. w przeciwnym razie zalecany tag jest zdefiniowany w [RFC8216] jako EXT-X-DATERANGE. 

| **Nazwa atrybutu** | **Typ**                      | **Wymagane?**                             | **Opis**                                                                                                                                                                                                                                                                      |
|--------------------|-------------------------------|-------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| KONTROL                | ciąg w cudzysłowie                 | Wymagane                                  | Komunikat zakodowany jako ciąg zakodowany algorytmem Base64, zgodnie z opisem w [RFC4648]. W przypadku komunikatów [SCTE-35] jest to zakodowane w formacie base64 splice_info_section ().                                                                                                |
| TYP               | ciąg w cudzysłowie                 | Wymagane                                  | Nazwa URN lub adres URL identyfikujący schemat komunikatów. W przypadku komunikatów [SCTE-35] typ przyjmuje wartość specjalną "scte35".                                                                                                                                |
| id                 | ciąg w cudzysłowie                 | Wymagane                                  | Unikatowy identyfikator zdarzenia. Jeśli identyfikator nie zostanie określony podczas pozyskiwania wiadomości, Azure Media Services wygeneruje unikatowy identyfikator.                                                                                                                                          |
| TRWANIA           | dziesiętna liczba zmiennoprzecinkowa | Wymagane                                  | Czas trwania zdarzenia. Jeśli nieznany, wartość **powinna** być równa 0. Jednostki są factional s.                                                                                                                                                                                           |
| ELAPSED            | dziesiętna liczba zmiennoprzecinkowa | Opcjonalne, ale wymagane dla przesuwanego okna | Gdy sygnał jest powtarzany do obsługi okna prezentacji ruchomej, to pole **musi** być ilością czasu prezentacji, która upłynęła od momentu rozpoczęcia zdarzenia. Jednostki są ułamkami sekund. Ta wartość może przekroczyć pierwotny określony czas trwania łączenia lub segmentu. |
| TIME               | dziesiętna liczba zmiennoprzecinkowa | Wymagane                                  | Czas prezentacji zdarzenia. Jednostki są ułamkami sekund.                                                                                                                                                                                                                    |


Warstwa aplikacji HLS Player użyje typu, aby zidentyfikować format wiadomości, zdekodować komunikat, zastosować wymagane konwersje czasu i przetworzyć zdarzenie.  Zdarzenia są synchronizowane czasowo na liście odtwarzania segmentu ścieżki nadrzędnej zgodnie z sygnaturą czasową zdarzenia.  Są one wstawiane przed najbliższym segmentem (#EXTINF tagu).

### <a name="323-hls-segment-playlist-example-using-legacy-adobe-primetime-ext-x-cue"></a>3.2.3 przykład HLS z listą odtwarzania segmentu przy użyciu "starsze" Adobe Primetime EXT-X-CUE

Poniższy przykład przedstawia dekorację manifestu HLS przy użyciu znacznika Adobe Primetime EXT-X-CUE.  Parametr "CUE" zawiera pełny zakodowany w formacie Base64 ładunek SCTE-35 splice_info, który wskazuje, że ten sygnał pochodzi z używania protokołu RTMP w trybie sygnału programu Adobe SCTE-35 lub został dostarczony za pośrednictwem Smooth Streaming SCTE-35. 

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

### <a name="324-hls-message-handling-for-legacy-adobe-primetime-ext-x-cue"></a>3.2.4 HLS obsługi komunikatów dla "starszego" elementu Adobe Primetime EXT-X-CUE

Zdarzenia są sygnalizowane w postaci listy odtwarzania segmentów dla każdej ścieżki audio i wideo. Pozycja taga "EXT-X" **musi** zawsze być bezpośrednio przed pierwszym segmentem HLS (w celu oddzielenia lub rozpoczęcia segmentu) lub bezpośrednio po ostatnim segmencie HLS (w przypadku łączenia się z lub końcem segmentu), do którego odwołuje się atrybuty czasu i czasu trwania, jak wymagane przez [Adobe-Primetime].

Gdy przewijane okno prezentacji jest włączone, tag "EXT-X-CUE" **musi** być powtarzany na tyle często, że element splice lub segment jest zawsze w pełni opisany na liście odtwarzania segmentów, a atrybut, który upłynął, **musi** być używany do wskazania, ile czasu Metoda splice lub segment została uaktywniona, zgodnie z wymaganiami [Adobe-Primetime].

Po włączeniu okna prezentacji ruchomej Tagi EXT-X są usuwane z listy odtwarzania segmentów, gdy czas, do którego się odwołuje, została wyniesiona z okna prezentacji przesuwanej.

## <a name="33-dash-manifest-decoration-mpd"></a>Dekoracja manifestu 3,3 (MPD)

[MPEGDASH] oferuje trzy sposoby sygnalizowania zdarzeń:

1.  Zdarzenia sygnalizowane w EventStream MPD
2.  Zdarzenia sygnalizowane w paśmie przy użyciu okna komunikatu o zdarzeniu ("emsg")
3.  Kombinacja wartości 1 i 2

Zdarzenia sygnalizowane w EventStream MPD są przydatne w przypadku przesyłania strumieniowego VOD, ponieważ klienci mają dostęp do wszystkich zdarzeń, natychmiast po pobraniu elementu MPD. Jest on również przydatny do sygnalizowania SSAI, gdzie podrzędny dostawca SSAI musi analizować sygnały z wielookresowego manifestu MPD i dynamicznie wstawiać zawartość usługi AD.  Rozwiązanie w paśmie ("emsg") jest przydatne w przypadku przesyłania strumieniowego na żywo, gdzie klienci nie muszą ponownie pobierać elementu MPD lub nie ma możliwości manipulowania manifestem SSAI między klientem a pochodzeniem. 

Azure Media Services domyślnym zachowaniem ŁĄCZNIKa jest sygnalizowanie zarówno w EventStream MPD, jak i w paśmie przy użyciu okna komunikatu zdarzenia ("emsg").

Komunikaty wskaźnika pozyskiwane za pośrednictwem protokołu [RTMP] lub [MS-SSTR-pozyskiwania] są zamapowane na zdarzenia PAUZy, korzystając z pól emsg ' i/lub w EventStreams. 

W paśmie SCTE-35 sygnalizowanie dla ŁĄCZNIKa następuje po definicji i wymaganiach zdefiniowanych w [SCTE-214-3], a także w sekcji [PAUZa-IF-redukcja] 13.12.2 ("SCTE35 Events"). 

W przypadku korzystania z programu w paśmie [SCTE-35] pole komunikatu o zdarzeniu ("emsg") używa schemeId = "urn: SCTE: scte35:2013: bin". W przypadku dekoracji manifestu MPD EventStream schemeId używa "urn: SCTE: scte35:2014: XML + bin".  Ten format jest reprezentacją XML zdarzenia, która zawiera dane wyjściowe binarnej kodowanej algorytmem Base64 kompletnego komunikatu SCTE-35, który dotarł do pozyskania. 

Definicje odwołania normatywnego przewozu [SCTE-35] są dostępne w ramach [SCTE-214-1] sek 6.7.4 (MPD) i [SCTE-214-3] sek 7.3.2 (przewóz z SCTE 35).

### <a name="331-mpeg-dash-mpd-eventstream-signaling"></a>3.3.1 sygnalizowanie MPEG PAUZy (MPD) EventStream

W manifeście (MPD) dekoracja zdarzeń zostanie zasygnalizowania przy użyciu elementu EventStream, który pojawia się w ramach elementu period. Używany schemeId to "urn: SCTE: scte35:2014: XML + bin".

> [!NOTE]
> Dla celów zwięzłości [SCTE-35] umożliwia użycie sekcji kodowanej algorytmem Base64 w elemencie Signal. Binary (a nie w elemencie Signal. SpliceInfoSection) jako alternatywę dla przewozu całkowicie przeanalizowanego komunikatu wskaźnika.
> Azure Media Services używa tego podejścia "XML + bin" do sygnalizowania w manifeście MPD.
> Jest to również zalecana metoda użyta w [łącznik-IF-redukcja]-Zobacz sekcję zatytułowaną ["strumienie zdarzeń wstawiania usługi AD" kreski, jeśli wytyczne redukcja](https://dashif-documents.azurewebsites.net/DASH-IF-IOP/master/DASH-IF-IOP.html#ads-insertion-event-streams)
> 

Element EventStream ma następujące atrybuty:

| **Nazwa atrybutu** | **Typ**                | **Wymagane?** | **Opis**                                                                                                                                                                                                                                                                                   |
|--------------------|-------------------------|---------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| scheme_id_uri      | ciąg                  | Wymagane      | Identyfikuje schemat komunikatu. Schemat jest ustawiony na wartość atrybutu schematu w polu manifestu serwera na żywo. Wartość **powinna** być identyfikatorem urn lub adresem URL identyfikującym schemat komunikatów; Obsługiwane wyjściowe schemeId powinny mieć wartość "urn: SCTE: scte35:2014: XML + bin" na [SCTE-214-1] s 6.7.4 (MPD), ponieważ usługa obsługuje tylko "XML + bin" w tej chwili dla zwięzłości w MPD.  |
| value              | ciąg                  | Optional      | Dodatkowa wartość ciągu używana przez właścicieli schematu do dostosowywania semantyki wiadomości. Aby można było rozróżnić wiele strumieni zdarzeń z tym samym schematem, wartość **musi** być ustawiona na nazwę strumienia zdarzeń (trackname dla [MS-SSTR-pozyskiwania] lub AMF nazwę komunikatu dla [RTMP] pozyskiwania). |
| Timescale          | 32-bitowa liczba całkowita bez znaku | Wymagane      | Skala czasu w taktach na sekundę.                                                                                                                                                                                                       |


### <a name="332-example-mpeg-dash-manifest-mpd-signaling-of-scte-35-using-eventstream"></a>3.3.2 przykład manifestu rozłącznika MPEG (MPD) sygnalizującego SCTE-35 przy użyciu EventStream

~~~ xml
<!-- Example MPD using xml+bin style signaling per [SCTE-214-1] -->
  <EventStream schemeIdUri="urn:scte:scte35:2014:xml+bin" value="scte35_track_001_000" timescale="10000000">
        <Event presentationTime="15447165200227600" duration="300000000" id="1026">
            <scte35:Signal>
                <scte35:Binary>
                    /DAlAAAAAAAAAP/wFAUAAAQDf+//KaeGwP4AKTLgAAAAAAAAn75a3g==
                </scte35:Binary>
            </scte35:Signal>
        </Event>
        <Event presentationTime="15447166250227600" duration="300000000" id="1027">
           <scte35:Signal>
                <scte35:Binary>
                    /DAlAAAAAAAAAP/wFAUAAAQDf+//KaeGwP4AKTLgAAAAAAAAn75a3g==
                </scte35:Binary>
            </scte35:Signal>
        </Event>
    </EventStream>
~~~

> [!IMPORTANT]
> Należy pamiętać, że presentationTime to czas prezentacji dla zdarzenia [SCTE-35] przetłumaczony na czas rozpoczęcia okresu, a nie czas przybycia komunikatu.
> [MPEGDASH] definiuje Event@presentationTime jako "określa czas prezentacji zdarzenia względem początku okresu.
> Wartość czasu prezentacji w sekundach to podział wartości tego atrybutu i wartość EventStream@timescale atrybutu.
> Jeśli nie istnieje, wartość czasu prezentacji wynosi 0.


### <a name="333-mpeg-dash-in-band-event-message-box-signaling"></a>Sygnalizowanie w oknie komunikatu zdarzenia w paśmie MPEG

Strumień zdarzeń znajdujących się w paśmie wymaga, aby element MPD miał obiekt InbandEventStream na poziomie zestawu adaptacji.  Ten element ma obowiązkowy atrybut schemeIdUri i opcjonalny parametr czasu, który jest również wyświetlany w oknie komunikatu zdarzenia ("emsg").  Pola komunikatów zdarzeń z identyfikatorami schematów, które nie są zdefiniowane w liście dozwolonych, nie **powinny** być obecne.

W przypadku przewozu w paśmie [SCTE-35] sygnały **muszą** używać schemeId = "urn: SCTE: scte35:2013: bin".
Normatywne definicje przewozu [SCTE-35] komunikatów w pasmie są zdefiniowane w [SCTE-214-3] sek 7.3.2 (przewóz komunikatów kontrolnych SCTE 35).

Poniższe szczegóły zawierają informacje o określonych wartościach, które klient powinien oczekiwać w "emsg" w zgodności z [SCTE-214-3]:

| **Nazwa pola**          | **Typ pola**          | **Wymagane?** | **Opis**                                                                                                                                                                                                                                                                                                                                                    |
|-------------------------|-------------------------|---------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| scheme_id_uri           | ciąg                  | Wymagane      | Identyfikuje schemat komunikatu. Schemat jest ustawiony na wartość atrybutu schematu w polu manifestu serwera na żywo. Wartość **musi** być identyfikatorem urn identyfikującym schemat komunikatów. W przypadku komunikatów [SCTE-35] **musi** to być "urn: SCTE: scte35:2013: bin" w zgodności z [SCTE-214-3] |
| Value                   | ciąg                  | Wymagane      | Dodatkowa wartość ciągu używana przez właścicieli schematu do dostosowywania semantyki wiadomości. Aby można było rozróżnić wiele strumieni zdarzeń z tym samym schematem, wartość zostanie ustawiona na nazwę strumienia zdarzeń (trackname dla wiadomości w przypadku pozyskiwania RTMP lub AMF).                                                                  |
| Timescale               | 32-bitowa liczba całkowita bez znaku | Wymagane      | Skala czasu (w taktach na sekundę) pól czas i czas trwania w polu "emsg".                                                                                                                                                                                                                                                                        |
| Presentation_time_delta | 32-bitowa liczba całkowita bez znaku | Wymagane      | Różnica czasu prezentacji nośnika w czasie prezentacji zdarzenia i Najwcześniejszy czas prezentacji w tym segmencie. Czas prezentacji i czas trwania **powinny być** wyrównane z punktami dostępu strumienia (SAP) typu 1 lub 2, zgodnie z definicją w załączniku I [ISO-14496-12].                                                                                            |
| event_duration          | 32-bitowa liczba całkowita bez znaku | Wymagane      | Czas trwania zdarzenia lub 0xFFFFFFFF w celu wskazania nieznanego czasu trwania.                                                                                                                                                                                                                                                                                          |
| Id                      | 32-bitowa liczba całkowita bez znaku | Wymagane      | Identyfikuje to wystąpienie komunikatu. Komunikaty o równoważnej semantyce mają tę samą wartość. Jeśli identyfikator nie zostanie określony podczas pozyskiwania wiadomości, Azure Media Services wygeneruje unikatowy identyfikator.                                                                                                                                                    |
| Message_data            | tablica bajtów              | Wymagane      | Komunikat zdarzenia. W przypadku komunikatów [SCTE-35] dane komunikatu są binarnie splice_info_section () zgodne z [SCTE-214-3] |

### <a name="334-dash-message-handling"></a>Obsługa komunikatów PAUZy 3.3.4

Zdarzenia są sygnalizowane w paśmie w polu "emsg" dla ścieżek wideo i audio.  Sygnalizowanie występuje dla wszystkich żądań segmentów, dla których presentation_time_delta ma 15 sekund lub mniej. 

Po włączeniu okna prezentacji przewijania, komunikaty o zdarzeniach są usuwane z elementu MPD, gdy suma czasu i czasu trwania komunikatu o zdarzeniu jest mniejsza niż godzina danych nośnika w manifeście.  Innymi słowy, komunikaty o zdarzeniach są usuwane z manifestu, gdy czas, do którego się odwołuje, została wyniesiona z okna prezentacji przesuwanej.

## <a name="4-scte-35-ingest-implementation-guidance-for-encoder-vendors"></a>4. SCTE-35 wskazówki dotyczące implementacji dla dostawców koderów

Poniższe wskazówki to typowe problemy, które mogą mieć wpływ na implementację tej specyfikacji przez dostawcę kodera.  Poniższe wskazówki zostały zebrane w oparciu o prawdziwe, ogólnoświatowe Opinie partnerów, aby ułatwić wdrożenie tej specyfikacji dla innych osób. 

[SCTE-35] komunikaty są pozyskiwane w formacie binarnym przy użyciu schematu **"urn: SCTE: scte35:2013: bin"** dla [MS-SSTR-pozyskiwania] i typu **"scte35"** dla [RTMP] pozyskiwania. Aby ułatwić konwersję czasu [SCTE-35], który jest oparty na sygnaturach czasowych prezentacji strumienia w formacie MPEG-2, mapowanie między środkami (pts_time + pts_adjustment splice_time ()) i oś czasu nośnika jest zapewniane przez czas prezentacji zdarzenia ( pole fragment_absolute_time służące do płynnego pozyskiwania i pola czasu dla usługi pozyskiwania RTMP. Mapowanie jest konieczne, ponieważ wartość 33-bitowej wartości "roll" przekracza około 26,5 godzin.

Smooth Streaming pozyskiwania [MS-SSTR-pozyskiwanie] wymaga, aby urządzenie Data Box nośnika ("MDAT ") musiały zawierać **splice_info_section ()** zdefiniowane w [SCTE-35]. 

W przypadku pozyskiwania RTMP atrybut Cue komunikatu AMF jest ustawiany na **splice_info_section ()** zakodowany algorytmem Base64 () zdefiniowanym w [SCTE-35].  

Po powyższym formacie wiadomości są wysyłane do klientów HLS, gładkich i KRESek, zgodnie z definicją powyżej.  

Podczas testowania implementacji przy użyciu platformy Azure Media Services, najpierw Rozpocznij testowanie przy użyciu LiveEvent "pass-through" przed przejściem do testowania na LiveEvent kodowania.

---

## <a name="next-steps"></a>Następne kroki
Wyświetl ścieżki uczenia Media Servicesowego.

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
