---
title: Poprawka dotycząca protokołu płynnego przesyłania strumieniowego (MS-SSTR) dla hevc - Azure
description: W tej specyfikacji opisano protokół i format pofragmentowanego przesyłania strumieniowego na żywo opartego na formacie MP4 z usługą HEVC w usłudze Azure Media Services. Tylko zmiany wymagane do dostarczenia HEVC są określone w tym artykule, z wyjątkiem były "(No Change)" wskazuje tekst jest kopiowany tylko w celu wyjaśnienia.
services: media-services
documentationcenter: ''
author: johndeu
manager: femila
editor: ''
ms.assetid: f27d85de-2cb8-4269-8eed-2efb566ca2c6
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/19/2019
ms.author: johndeu
ms.openlocfilehash: be4009d418f2f8f3dff755e2e990efee593f070b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76514225"
---
# <a name="smooth-streaming-protocol-ms-sstr-amendment-for-hevc"></a>Zmiana protokołu płynnego przesyłania strumieniowego (MS-SSTR) dla HEVC 

## <a name="1-introduction"></a>1 Wprowadzenie 

Ten artykuł zawiera szczegółowe zmiany, które mają być stosowane do specyfikacji protokołu Smooth Streaming Protocol [MS-SSTR], aby umożliwić płynne przesyłanie strumieniowe wideo zakodowanego hevc. W tej specyfikacji przedstawiamy tylko zmiany wymagane do dostarczenia kodeka wideo HEVC. Artykuł jest zgodny z tym samym schematem numerowania, co specyfikacja [MS-SSTR]. Puste nagłówki przedstawione w całym artykule są dostarczane, aby zorientować czytelnika do ich pozycji w specyfikacji [MS-SSTR].  "(No Change)" oznacza, że tekst jest kopiowany wyłącznie w celu wyjaśnienia.

Artykuł zawiera techniczne wymagania dotyczące implementacji sygnalizacji kodeka wideo HEVC (przy użyciu ścieżek w formacie "hev1" lub "hvc1") w manifeście Smooth Streaming, a odniesienia normatywne są aktualizowane w celu odniesienia do aktualnych standardów MPEG, które obejmują HEVC, wspólne szyfrowanie HEVC i nazwy pól dla formatu iso base media file file zostały zaktualizowane, aby były zgodne z najnowszymi specyfikacjami. 

Przywołana specyfikacja protokołu Smooth Streaming Protocol [MS-SSTR] opisuje format przewodowy używany do dostarczania nośników cyfrowych na żywo i na żądanie, takich jak audio i wideo, w następujący sposób: od kodera do serwera sieci web, z serwera na inny serwer oraz z serwera do klienta HTTP.
Korzystanie z MPEG-4[([MPEG4-RA])](https://go.microsoft.com/fwlink/?LinkId=327787)oparte na dostarczaniu struktury danych za pośrednictwem protokołu HTTP umożliwia bezproblemowe przełączanie w czasie zbliżonym do rzeczywistego między różnymi poziomami jakości skompresowanej zawartości multimedialnej. Rezultatem jest stałe środowisko odtwarzania dla użytkownika końcowego klienta HTTP, nawet jeśli warunki renderowania sieci i wideo zmieniają się dla komputera klienckiego lub urządzenia.

## <a name="11-glossary"></a>1.1 Słowniczek 

W *[MS-GLOS]* zdefiniowano następujące terminy:

>   **globalnie unikatowy identyfikator (GUID) uniwersalny unikatowy identyfikator (UUID)**

Następujące terminy są specyficzne dla tego dokumentu:

>  **czas kompozycji:** Czas prezentacji próbki u klienta, zgodnie z definicją w [[ISO/IEC-14496-12]](https://go.microsoft.com/fwlink/?LinkId=183695).
> 
>   **CENC**: Szyfrowanie powszechne, zgodnie z definicją w [ISO/IEC 23001-7] Wydanie drugie.
> 
>   **czas dekodowania:** Czas, w której próbka musi być zdekodowana na kliencie, zgodnie z definicją [w [ISO/IEC 14496-12:2008]](https://go.microsoft.com/fwlink/?LinkId=183695).

**fragment:** Niezależnie do pobrania jednostka **nośnika,** która składa się z jednej lub więcej **próbek**.

>   **HEVC:** Kodowanie wideo o wysokiej wydajności, zgodnie z definicją w [ISO/IEC 23008-2]
> 
>   **manifest:** Metadane dotyczące **prezentacji,** która umożliwia klientowi składanie żądań **dotyczących nośnika**. **nośniki:** Skompresowane dane audio, wideo i tekstowe używane przez klienta do odtwarzania **prezentacji**. **format nośnika:** Dobrze zdefiniowany format reprezentujący dźwięk lub wideo jako skompresowaną **próbkę**.
> 
>   **prezentacja:** Zestaw wszystkich **strumieni** i powiązanych metadanych potrzebnych do odtwo niezłego filmu. **wniosek:** Komunikat HTTP wysłany przez klienta do serwera, zgodnie z definicją w **odpowiedzi** [[RFC2616]:](https://go.microsoft.com/fwlink/?LinkId=90372) Wiadomość HTTP wysłana z serwera do klienta, zgodnie z definicją w [[RFC2616]](https://go.microsoft.com/fwlink/?LinkId=90372)
> 
>   **próbka:** Najmniejsza jednostka podstawowa (np. ramka), w której **nośnik** jest przechowywany i przetwarzany.
> 
>   **MOŻE, POWINIEN, MUSI, NIE POWINIEN, NIE MOŻE:** Terminy te (we wszystkich caps) są używane zgodnie z opisem w [[RFC2119]](https://go.microsoft.com/fwlink/?LinkId=90317) Wszystkie instrukcje zachowania opcjonalne używać albo MOŻE, POWINIEN lub NIE POWINIEN.

## <a name="12-references"></a>1.2 Referencje

>   Odwołania do dokumentacji Microsoft Open Specifications nie obejmują roku publikacji, ponieważ łącza są do najnowszej wersji dokumentów, które są często aktualizowane. Odwołania do innych dokumentów obejmują rok publikacji, gdy jeden jest dostępny.

### <a name="121-normative-references"></a>1.2.1 Referencje normatywne 

>  [MS-SSTR] Protokół płynnego przesyłania strumieniowego *w wersji 20140502*[https://msdn.microsoft.com/library/ff469518.aspx](https://msdn.microsoft.com/library/ff469518.aspx)
> 
>   [ISO/IEC 14496-12] Międzynarodowa Organizacja Normalizacyjna, "Information technology -- Kodowanie obiektów audiowizualnych -- Część 12: ISO Base Media File Format", ISO/IEC 14496-12:2014, Edition 4, Plus Corrigendum 1, poprawki 1 & 2.
>   <https://standards.iso.org/ittf/PubliclyAvailableStandards/c061988_ISO_IEC_14496-12_2012.zip>
> 
>   [ISO/IEC 14496-15] Międzynarodowa Organizacja Normalizacyjna, "Technologia informacyjna -- Kodowanie obiektów audiowizualnych -- Część 15: Przewóz ustrukturyzowanych wideo jednostki NAL w formacie ISO Base Media File Format", ISO 14496-15:2015, Wydanie 3.
>   <https://www.iso.org/iso/home/store/catalogue_tc/catalogue_detail.htm?csnumber=65216>
> 
>   [ISO/IEC 23008-2] Technologia informacyjna - Wysoka wydajność kodowania i dostarczania multimediów w środowiskach heterogenicznych - Część 2: Wysoka wydajność kodowania wideo: 2013 lub najnowsza edycja<https://standards.iso.org/ittf/PubliclyAvailableStandards/c035424_ISO_IEC_23008-2_2013.zip>
> 
>   [ISO/IEC 23001-7] Technologia informacyjna — Technologie systemów MPEG — Część 7: Wspólne szyfrowanie w plikach w formacie media iso, CENC Edition 2:2015<https://www.iso.org/iso/catalogue_detail.htm?csnumber=65271>
> 
>   [RFC-6381] IETF RFC-6381, "Kodeki" i "Profile" Parametry dla "Bucket" Typy nośników"<https://tools.ietf.org/html/rfc6381>
> 
>   [MPEG4-RA] Urząd Rejestracji MP4, "MP4REG",[http://www.mp4ra.org](https://go.microsoft.com/fwlink/?LinkId=327787)
> 
>   [RFC2119] Bradner, S., "Key words for use in RfC to Indicate Requirement Levels", BCP 14, RFC 2119, marzec 1997,[https://www.rfc-editor.org/rfc/rfc2119.txt](https://go.microsoft.com/fwlink/?LinkId=90317)

### <a name="122-informative-references"></a>1.2.2 Odniesienia informacyjne 

>   [MS-GLOS] Microsoft Corporation, "*Słownik wzorcowy protokołów systemu Windows*."
> 
>   [RFC3548] Josefsson, S., Ed., "The Base16, Base32, and Base64 Data Encodings", RFC 3548, lipiec 2003,[https://www.ietf.org/rfc/rfc3548.txt](https://go.microsoft.com/fwlink/?LinkId=90432)
> 
>   [RFC5234] Crocker, D., Ed., and Overell, P., "Augmented BNF for Syntax Specifications: ABNF", STD 68, RFC 5234, styczeń 2008,[https://www.rfc-editor.org/rfc/rfc5234.txt](https://go.microsoft.com/fwlink/?LinkId=123096)


## <a name="13-overview"></a>1.3 Przegląd 

>   Poniżej podano tylko zmiany w specyfikacji Smooth Streaming wymagane do dostarczenia hevc. Niezmienione nagłówki sekcji są wyświetlane w celu utrzymania lokalizacji w specyfikacji płynnego przesyłania strumieniowego, do którego istnieje odwołanie [MS-SSTR].

## <a name="14-relationship-to-other-protocols"></a>1.4 Związek z innymi protokołami 

## <a name="15-prerequisitespreconditions"></a>1.5 Wymagania wstępne/Warunki wstępne 

## <a name="16-applicability-statement"></a>1.6 Oświadczenie o zastosowaniu 

## <a name="17-versioning-and-capability-negotiation"></a>1.7 Przechowywanie wersji i negocjacje w sprawie możliwości 

## <a name="18-vendor-extensible-fields"></a>1.8 Pola rozszerzalne dostawcy 

>   Należy zastosować następującą metodę identyfikacji strumieni przy użyciu formatu wideo HEVC:
> 
>   * **Niestandardowe kody opisowe dla formatów multimediów:** Ta możliwość jest dostępna w polu **FourCC,** jak określono w sekcji *2.2.2.5*.
>   Realizatorzy mogą zapewnić, że rozszerzenia nie są w konflikcie, rejestrując kody rozszerzeń w MPEG4-RA, jak określono w [[ISO/IEC-14496-12]](https://go.microsoft.com/fwlink/?LinkId=183695)

## <a name="19-standards-assignments"></a>1.9 Przydziały standardów 

## <a name="2-messages"></a>2 Wiadomości 

## <a name="21-transport"></a>2.1 Transport

## <a name="22-message-syntax"></a>2.2 Składnia komunikatów 

### <a name="221-manifest-request"></a>2.2.1 Prośba manifestu 

### <a name="222-manifest-response"></a>2.2.2 Reakcja manifestu 

#### <a name="2221-smoothstreamingmedia"></a>2.2.2.1 SmoothStreamingMedia 

>   **MinorVersion (zmienna):** Wersja pomocnicza komunikatu Manifest Response. MUSI być ustawiona na 2. (Bez zmian)
> 
>   **Skala czasu (zmienna):** Skala czasu duration atrybutu, określony jako liczba przyrostów w ciągu jednej sekundy. Wartością domyślną jest .
> 1. (Bez zmian)
> 
>    Zalecana wartość to 90000 dla reprezentujących dokładny czas trwania klatek wideo i fragmentów zawierających ułamkową liczbę klatek na sekundę (na przykład 30/1,001 Hz).

#### <a name="2222-protectionelement"></a>2.2.2.2 OchronaElement 

Funkcja ProtectionElement jest obecna, gdy do strumieni wideo lub audio zastosowano wspólne szyfrowanie (CENC). Strumienie szyfrowane HEVC muszą być zgodne z common encryption 2nd edition [ISO/IEC 23001-7]. Szyfrowane są tylko dane plasterków w jednostkach NAL VCL.

#### <a name="2223-streamelement"></a>2.2.2.3 StreamElement 

>   **StreamTimeScale (zmienna):** Skala czasu dla wartości czasu trwania i czasu w tym strumieniu, określona jako liczba przyrostów w ciągu jednej sekundy. Wartość 90000 jest zalecana dla strumieni HEVC. Wartość odpowiadająca częstotliwości próbkowania przebiegu (na przykład 48000 lub 44100) jest zalecana dla strumieni audio.

##### <a name="22231-streamprotectionelement"></a>2.2.2.3.1 StreamProtectionElement

#### <a name="2224-urlpattern"></a>2.2.2.4 UrlPattern 

#### <a name="225-trackelement"></a>2.2.5 TrackElement 

>   **FourCC (zmienna):** Czteroznakowy kod identyfikujący format nośnika jest używany dla każdej próbki. Następujący zakres wartości jest zarezerwowany z następującymi znaczeniami semantycznymi:
> 
> * "hev1": Próbki wideo dla tego utworu używają wideo HEVC, przy użyciu przykładowego formatu opisu 'hev1, określonego w [ISO/IEC-14496-15].
>
> * "hvc1": Próbki wideo dla tego utworu używają wideo HEVC, przy użyciu przykładowego formatu opisu "hvc1" określonego w [ISO/IEC-14496-15].
> 
>   **CodecPrivateData (zmienna):** Dane określające parametry specyficzne dla formatu nośnika i wspólne dla wszystkich próbek na ścieżce, reprezentowane jako ciąg bajtów kodowanych szesnastkowych. Format i semantyczne znaczenie sekwencji bajtów różni się w zależności od wartości pola **FourCC** w następujący sposób:
> 
>   * Gdy TrackElement opisuje film HEVC, pole **FourCC** jest równe **"hev1"** lub **"hvc1"**
> 
>   Pole **CodecPrivateData** zawiera szesnastkową reprezentację ciągu następującej sekwencji bajtów, określoną w ABNF [[RFC5234]:](https://go.microsoft.com/fwlink/?LinkId=123096) (bez zmiany z MS-SSTR)
> 
>   * %x00 %x00 %x00 %x00 %x01 SPSField %x00 %x00 %x00 %x01 PPSField
> 
>   * SPSField zawiera zestaw parametrów sekwencji (SPS).
> 
>   * PPSField zawiera zestaw parametrów plasterka (PPS).
> 
>   Uwaga: Zestaw parametrów wideo (VPS) nie jest zawarty w CodecPrivateData, ale powinien być zawarty w nagłówku pliku przechowywanych plików w polu "hvcC". Systemy korzystające z protokołu Smooth Streaming Protocol muszą sygnalizować dodatkowe parametry dekodowania (na przykład warstwę HEVC) przy użyciu "kodeków" atrybutów niestandardowych.

##### <a name="22251-customattributeselement"></a>2.2.2.5.1 CustomAttributesElement 

#### <a name="226-streamfragmentelement"></a>2.2.6 StreamFragmentElement 

>   **Pole MajorVersion firmy SmoothStreamingMedia** musi być ustawione na 2, a pole **MinorVersion** musi być ustawione na 2. (Bez zmian)

##### <a name="22261-trackfragmentelement"></a>2.2.2.6.1 TrackFragmentElement 

### <a name="223-fragment-request"></a>2.2.3 Żądanie fragmentu 

>   **Uwaga:** Domyślnym formatem nośnika wymaganym dla **MinorVersion** 2 i 'hev1' lub 'hvc1' jest format iso8' marki ISO Base Media File Format określony w [ISO/IEC 14496-12] ISO Base Media File Format Fourth Edition i [ISO/IEC 23001-7] Common Encryption Second Edition.

### <a name="224-fragment-response"></a>2.2.4 Reakcja fragmentu 

#### <a name="2241-moofbox"></a>2.2.4.1 MoofBox 

#### <a name="2242-mfhdbox"></a>2.2.4.2 MfhdBox 

#### <a name="2243-trafbox"></a>2.2.4.3 TrafBox 

#### <a name="2244-tfxdbox"></a>2.2.4.4 Skrzynka TfxdBox 

>   **TfxdBox** jest przestarzały, a jego funkcja jest zastępowana przez pole czasowe dekodowania fragmentu ścieżki ('tfdt') określone w sekcji [ISO/IEC 14496-12] 8.8.12.
> 
>   **Uwaga:** Klient może obliczyć czas trwania fragmentu, sumując czas trwania próbki wymieniony w polu przebiegu śledzenia ('trun') lub mnożąc liczbę próbek razy w przypadku domyślnego czasu trwania próbki. BaseMediaDecodeTime w 'tfdt' plus czas trwania fragmentu jest równa parametr czasu adresu URL dla następnego fragmentu.
> 
>   Pole czasu referencyjnego producenta ("prft") powinno zostać wstawione przed polem fragmentu filmu ("moof"), w razie potrzeby, w celu wskazania czasu UTC odpowiadającego czasowi dekodowania fragmentu toru pierwszej próbki, do której odwołuje się Pole Fragment filmu, jak określono w sekcji [ISO/IEC 14496-12] 8.16.5.

#### <a name="2245-tfrfbox"></a>2.2.4.5 TfrfBox 

>   **TfrfBox** jest przestarzały, a jego funkcja jest zastępowana przez pole czasowe dekodowania fragmentu ścieżki ('tfdt') określone w sekcji [ISO/IEC 14496-12] 8.8.12.
> 
>   **Uwaga:** Klient może obliczyć czas trwania fragmentu, sumując czas trwania próbki wymieniony w polu przebiegu śledzenia ('trun') lub mnożąc liczbę próbek razy w przypadku domyślnego czasu trwania próbki. BaseMediaDecodeTime w 'tfdt' plus czas trwania fragmentu jest równa parametr czasu adresu URL dla następnego fragmentu. Spójrz w przyszłość adresy są przestarzałe, ponieważ opóźniają przesyłanie strumieniowe na żywo.

#### <a name="2246-tfhdbox"></a>2.2.4.6 TfhdBox 

>   **TfhdBox** i pola pokrewne hermetyzują domyślne dla metadanych próbki w fragmencie. Składnia pola **TfhdBox** jest ścisłym podzbiorem składni pola nagłówka fragmentu ścieżki zdefiniowanego w sekcji [[ISO/IEC-14496-12]](https://go.microsoft.com/fwlink/?LinkId=183695) 8.8.7.
> 
>   **BaseDataOffset (8 bajtów):** Przesunięcie w bajtach od początku pola **MdatBox** do pola przykładowego w polu **MdatBox.** Aby zasygnalizować to ograniczenie, należy ustawić domyślną flagę base-is-moof (0x020000).

#### <a name="2247-trunbox"></a>2.2.4.7 TrunBox 

>   **TrunBox** i pola pokrewne hermetyzują na przykładowe metadane żądanego fragmentu. Składnia **TrunBox** jest ścisłym podzbiorem pola przebiegu fragmentu ścieżki w wersji 1 zdefiniowanym w sekcji [[ISO/IEC-14496-](https://go.microsoft.com/fwlink/?LinkId=183695)*12]* sekcja 8.8.8.
> 
>   **SampleCompositionTimeOffset (4 bajty):** Przesunięcie czasu składu próbki każdej próbki dostosowane tak, aby czas prezentacji pierwszej prezentowanej próbki we fragmencie był równy czasowi dekodowania pierwszej próbki zdekodowanej. Stosuje się ujemne przesunięcia składu próbki wideo,
> 
>   zgodnie z definicją [w [ISO/IEC-14496-12].](https://go.microsoft.com/fwlink/?LinkId=183695)
> 
>   Uwaga: Pozwala to uniknąć błędu synchronizacji wideo spowodowanego opóźnieniem dźwięku wideo równym największemu zdekodowanemu opóźnieniu usunięcia buforu obrazu i utrzymuje czas prezentacji między alternatywnymi fragmentami, które mogą mieć różne opóźnienia usuwania.
> 
>   Składnia pól zdefiniowanych w tej sekcji, określona w ABNF [[RFC5234],](https://go.microsoft.com/fwlink/?LinkId=123096) pozostaje taka sama, z wyjątkiem następujących czynności:
> 
>   SampleCompositionTimeOffset = SIGNED_INT32

#### <a name="2248-mdatbox"></a>2.2.4.8 MdatBox 

#### <a name="2249-fragment-response-common-fields"></a>2.2.4.9 Wspólne pola odpowiedzi fragmentów 

### <a name="225-sparse-stream-pointer"></a>2.2.5 Wskaźnik strumienia rozrzedzony 

### <a name="226-fragment-not-yet-available"></a>2.2.6 Fragment nie jest jeszcze dostępny 

### <a name="227-live-ingest"></a>2.2.7 Połk na żywo 

#### <a name="2271-filetype"></a>2.2.7.1 Typ pliku 

>   **FileType (zmienna):** określa podtyp i zamierzone zastosowanie pliku MPEG-4 ([[MPEG4-RA])](https://go.microsoft.com/fwlink/?LinkId=327787) oraz atrybutów wysokiego poziomu.
> 
>   **MajorBrand (zmienna):** Główna marka pliku multimedialnego. MUSI być ustawiona na "isml".
> 
>   **MinorVersion (zmienna):** Pomocnicza wersja pliku multimedialnego. MUSI być ustawiona na 1.
> 
>   **CompatibleBrands (zmienna):** Określa obsługiwane marki MPEG-4.
>   MUSI zawierać "ccff" i "iso8".
> 
>   Składnia pól zdefiniowanych w tej sekcji, określona w ABNF [[RFC5234],](https://go.microsoft.com/fwlink/?LinkId=123096) jest następująca:

    FileType = MajorBrand MinorVersion CompatibleBrands
    MajorBrand = STRING_UINT32
    MinorVersion = STRING_UINT32
    CompatibleBrands = "ccff" "iso8" 0\*(STRING_UINT32)

**Uwaga:** Marki zgodności 'ccff' i 'iso8' wskazują, że fragmenty są zgodne z "Wspólnym formatem plików kontenerów" i powszechnym szyfrowaniem [ISO/IEC 23001-7] i ISO Base Media File Format Edition 4 [ISO/IEC 14496-12].

#### <a name="2272-streammanifestbox"></a>2.2.7.2 StreamManifestBox 

##### <a name="22721-streamsmil"></a>2.2.7.2.1 StreamSMIL 

#### <a name="2273-liveservermanifestbox"></a>2.2.7.3 LiveServerManifestBox 

##### <a name="22731-livesmil"></a>2.2.7.3.1 LiveSMIL 

#### <a name="2274-moovbox"></a>2.2.7.4 MoovBox 

#### <a name="2275-fragment"></a>2.2.7.5 Fragment 

##### <a name="22751-track-fragment-extended-header"></a>2.2.7.5.1 Rozszerzony nagłówek fragmentu ścieżki 

### <a name="228-server-to-server-ingest"></a>2.2.8 Połknąć serwer do serwera 

## <a name="3-protocol-details"></a>3 Szczegóły protokołu 


## <a name="31-client-details"></a>3.1 Dane klienta 

### <a name="311-abstract-data-model"></a>3.1.1 Abstrakcyjny model danych 

#### <a name="3111-presentation-description"></a>3.1.1.1 Opis prezentacji 

>   Element danych Opis prezentacji hermetyzuje wszystkie metadane dla prezentacji.
> 
>   Metadane prezentacji: zestaw metadanych, który jest wspólny dla wszystkich strumieni w prezentacji. Metadane prezentacji składają się z następujących pól, określonych w sekcji *2.2.2.1:*
> 
> * **MajorVersion (Wersja główna)**
> * **MinorVersion (Wersja pomocnicza)**
> * **Skala czasu**
> * **Czas trwania**
> * **IsLive (IsLive)**
> * **LookaheadCount (Liczba nagłówków)**
> * **DVRWindowLength**
> 
>   Prezentacje zawierające strumienie HEVC powinny być ustawione:

    MajorVersion = 2
    MinorVersion = 2

>   LookaheadCount = 0 (Uwaga: Pola przestarzałe)
> 
>   Prezentacje powinny również ustawić:

    TimeScale = 90000

>   Kolekcja strumienia: zbiór elementów danych opisu strumienia, jak określono w sekcji *3.1.1.1.2*.
> 
>   Opis ochrony: Zbiór elementów danych opisu metadanych systemu ochrony, jak określono w sekcji *3.1.1.1.1*.

##### <a name="31111-protection-system-metadata-description"></a>3.1.1.1.1 Opis metadanych systemu ochrony 

>   Element danych Opis metadanych systemu ochrony hermetyzuje metadane specyficzne dla jednego systemu ochrony zawartości. (Bez zmian)
> 
>   Opis nagłówka ochrony: metadane ochrony zawartości, które odnoszą się do jednego systemu ochrony zawartości. Nagłówek ochrony Opis obejmuje następujące pola, określone w ppkt *2.2.2.2:*
> 
>   * **SystemID**
>   * **OchronaHeaderContent**

##### <a name="31112-stream-description"></a>3.1.1.1.2 Opis strumienia 

###### <a name="311121-track-description"></a>3.1.1.1.2.1 Opis toru 

###### <a name="3111211-custom-attribute-description"></a>3.1.1.1.2.1.1 Opis atrybutu niestandardowego 

##### <a name="3113-fragment-reference-description"></a>3.1.1.3 Opis odniesienia fragmentu 

###### <a name="31131-track-specific-fragment-reference-description"></a>3.1.1.3.1 Opis fragmentu specyficznego dla toru 

#### <a name="3112-fragment-description"></a>3.1.1.2 Opis fragmentu 

##### <a name="31121-sample-description"></a>3.1.1.2.1 Opis próbki 

### <a name="312-timers"></a>3.1.2 Czasomierze 

### <a name="313-initialization"></a>3.1.3 Inicjowanie 

### <a name="314-higher-layer-triggered-events"></a>3.1.4 Zdarzenia wyzwalane w wyższej warstwie 

#### <a name="3141-open-presentation"></a>3.1.4.1 Otwarta prezentacja 

#### <a name="3142-get-fragment"></a>3.1.4.2 Pobierz fragment 

#### <a name="3143-close-presentation"></a>3.1.4.3 Zamknięcie prezentacji 

### <a name="315-processing-events-and-sequencing-rules"></a>3.1.5 Przetwarzanie zdarzeń i reguł sekwencjonowania 

#### <a name="3151-manifest-request-and-manifest-response"></a>3.1.5.1 Żądanie manifestu i manifestu odpowiedź 

#### <a name="3152-fragment-request-and-fragment-response"></a>3.1.5.2 Żądanie fragmentu i odpowiedź fragmentu

## <a name="32-server-details"></a>3.2 Szczegóły serwera

## <a name="33-live-encoder-details"></a>3.3 Szczegóły kodera na żywo 

## <a name="4-protocol-examples"></a>4 Przykłady protokołów 

## <a name="5-security"></a>5 Bezpieczeństwo 

## <a name="51-security-considerations-for-implementers"></a>5.1 Zagadnienia dotyczące zabezpieczeń dla realizatorów

>   Jeśli zawartość transportowana przy użyciu tego protokołu ma wysoką wartość komercyjną, system ochrony zawartości powinien być używany w celu zapobiegania nieautoryzowanemu użyciu zawartości. **ProtectionElement** może służyć do przenoszenia metadanych związanych z korzystaniem z systemu ochrony zawartości. Chroniona zawartość audio i wideo musi być szyfrowana zgodnie z przepisami MPEG Common Encryption Second Edition: 2015 [ISO/IEC 23001-7].
> 
>   **Uwaga:** W przypadku wideo HEVC zaszyfrowane są tylko dane plasterków w listach NALs VCL. Nagłówki plasterków i inne listy NALS są dostępne dla aplikacji do prezentacji przed odszyfrowywaniem. w bezpiecznej ścieżce wideo zaszyfrowane informacje nie są dostępne dla aplikacji do prezentacji.

## <a name="52-index-of-security-parameters"></a>5.2 Indeks parametrów zabezpieczeń 


| **Parametr zabezpieczeń**  | **Section**         |
|-------------------------|---------------------|
| OchronaElement       | *2.2.2.2*           |
| Typowe pola szyfrowania | *[ISO/IEC 23001-7]* |

## <a name="53-common-encryption-boxes"></a>5.3 Typowe skrzynki szyfrujące

Następujące pola mogą znajdować się w odpowiedziach fragmentów po zastosowaniu szyfrowania wspólnego i są określone w [ISO/IEC 23001-7] lub [ISO/IEC 14496-12]:

1.  Pole nagłówka specyficzne dla systemu ochrony ("pssh")

2.  Przykładowe pole szyfrowania ('senc')

3.  Przykładowe pole przesunięcia informacji pomocniczych ('saio')

4.  Przykładowy pomocniczy pojemnik na informacje ('saiz')

5.  Przykładowe pole opisu grupy ('sgpd')

6.  Przykład do skrzynki grupowej ('sbgp')

---

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

[image1]: ./media/media-services-fmp4-live-ingest-overview/media-services-image1.png
[image2]: ./media/media-services-fmp4-live-ingest-overview/media-services-image2.png
[image3]: ./media/media-services-fmp4-live-ingest-overview/media-services-image3.png
[image4]: ./media/media-services-fmp4-live-ingest-overview/media-services-image4.png
[image5]: ./media/media-services-fmp4-live-ingest-overview/media-services-image5.png
[image6]: ./media/media-services-fmp4-live-ingest-overview/media-services-image6.png
[image7]: ./media/media-services-fmp4-live-ingest-overview/media-services-image7.png
