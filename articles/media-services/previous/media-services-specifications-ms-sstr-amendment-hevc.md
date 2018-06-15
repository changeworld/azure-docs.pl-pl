---
title: Azure Media Services - Smooth Streaming zmiany protokołu (MS-SSTR) dla HEVC | Dokumentacja firmy Microsoft
description: Określenie tej wartości opisano protokołu i format pofragmentowane na podstawie MP4 transmisja strumieniowa na żywo z HEVC w usłudze Azure Media Services. Jest to zmiana documentaiton protokołu Smooth Streaming (MS-SSTR) na dołączenie obsługi pozyskiwania HEVC i przesyłania strumieniowego. Zmiany wymagane w celu dostarczenia HEVC są określone w tym artykule, chyba że zostały "(bez zmian)" wskazuje tekst jest kopiowany tylko wyjaśnienia.
services: media-services
documentationcenter: ''
author: cenkdin
manager: cfowler
editor: ''
ms.assetid: f27d85de-2cb8-4269-8eed-2efb566ca2c6
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/07/2018
ms.author: johndeu;
ms.openlocfilehash: 78ec0e3ee4304e820bf64afa26440380887630a1
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
ms.locfileid: "33790510"
---
# <a name="smooth-streaming-protocol-ms-sstr-amendment-for-hevc"></a>Płynnego przesyłania strumieniowego zmiany protokołu (MS-SSTR) dla HEVC

## <a name="1-introduction"></a>Wprowadzenie 1 

Ten artykuł zawiera szczegółowe zmiany można zastosować do specyfikacji Smooth Streaming protokołu [MS-SSTR] umożliwiające Smooth Streaming z HEVC zakodowane wideo. W tej specyfikacji możemy konspektu tylko zmiany wymagane do świadczenia HEVC kodera-dekodera wideo. Artykuł wykonuje ten sam schemat numerowania jako specification [MS-SSTR]. Pusty nagłówki przedstawione w artykule są przekazywane do zorientowania czytelnika swoją pozycję w specification [MS-SSTR].  "(Bez zmian)" oznacza, że tekst jest kopiowany wyjaśnienie wyłącznie do celów.

Artykuł zawiera wymagania techniczne implementacji do sygnalizacji HEVC kodera-dekodera wideo w manifeście Smooth Streaming i odwołania normatywne zostaną zaktualizowane, aby odwołać bieżące standardy MPEG, które obejmują HEVC, wspólnego szyfrowania HEVC i pole Aby było spójne z najnowszej specyfikacji nazwy formatu pliku multimedialnego Base ISO zostały zaktualizowane. 

Przywoływanego specyfikacji Smooth Streaming protokołu [MS-SSTR] opisuje format użyty do dostarczenia na żywo i na żądanie nośnika cyfrowego, takich jak audio i wideo, w następujących sposobów: z kodera do serwera sieci web z serwera na inny serwer i z od serwera do klienta HTTP.
Użycie MPEG-4 ([[MPEG4 RA])](http://go.microsoft.com/fwlink/?LinkId=327787)-dostarczania struktury oparte na danych za pośrednictwem protokołu HTTP umożliwia bezproblemowe przełączenie w najbliższym czasie rzeczywistym między różne poziomy jakości zawartości multimedialnej skompresowane. Wynik jest stałe odtwarzania obsługę użytkowników końcowych klienta HTTP, nawet jeśli sieć i warunki renderowania wideo zmienić komputer kliencki lub urządzenie.

## <a name="11-glossary"></a>1.1 słownik 

Poniższe terminy są zdefiniowane w *[MS-GLOS]*:

>   **powszechnie Unikatowy identyfikator unikatowy identyfikator globalny (GUID) (UUID)**

Poniższe terminy są specyficzne dla tego dokumentu:

>  **czas kompozycji:** czasu próbki są prezentowane po stronie klienta, zgodnie z definicją w [[ISO/IEC-14496-12].](http://go.microsoft.com/fwlink/?LinkId=183695)

>   **CENC**: szyfrowania Common Encryption, zgodnie z definicją w wydanie drugie [normą ISO/IEC 23001-7].

>   **Czas dekodowania:** próbkę wymagany jest czas ma być zdekodowany na kliencie, zgodnie z definicją w [[normą ISO/IEChttp://go.microsoft.com/fwlink/?LinkId=18369514496-12].](http://go.microsoft.com/fwlink/?LinkId=183695)

**fragment:** niezależnie do pobrania jednostki **nośnika** który składa się z co najmniej jeden **przykłady**.

>   **HEVC:** wysoką wydajność wideo kodowania, zgodnie z definicją w [ISO/IEC 23008-2]

>   **Manifest:** metadane dotyczące **prezentacji** który temu klient może wysyłać żądania **nośnika**. **nośnik:** skompresowanych danych audio, wideo i tekst używany przez klienta do odtwarzania **prezentacji**. **Media format:** dobrze zdefiniowany format służący do reprezentowania audio i wideo jako skompresowany **próbki**.

>   **prezentacja:** zbiór wszystkich **strumienie** i pokrewne metadane potrzebne do odtwarzania pojedynczego filmu. **żądanie:** HTTP komunikatów wysłanych z klienta do serwera, zgodnie z definicją w [[specyfikacją RFC2616].](http://go.microsoft.com/fwlink/?LinkId=90372) **odpowiedź:** komunikat HTTP wysyłane z serwera do klienta, zgodnie z definicją w [[specyfikacją RFC2616].](http://go.microsoft.com/fwlink/?LinkId=90372)

>   **przykład:** najmniejsza jednostka podstawowych (na przykład ramki), w którym **nośnika** są przechowywane i przetwarzane.

>   **MOŻE SHOULD, musi, nie powinien, nie może:** te warunki (wszystkie wersaliki) są używane zgodnie z opisem w [[RFC2119].](http://go.microsoft.com/fwlink/?LinkId=90317) Wszystkie instrukcje używania zachowanie opcjonalne albo mogą SHOULD lub nie powinien.

## <a name="12-references"></a>1.2 odwołań 
-----------

>   Odwołania do dokumentacji otwartych specyfikacji firmy Microsoft nie dołączaj roku publikowania, ponieważ są łącza do najnowszej wersji dokumentów, które są często aktualizowane. Odwołania do innych dokumentów obejmują publikowania roku, gdy jest ona dostępna.

 ### <a name="121-normative-references"></a>1.2.1 odwołania normatywne 

>  [MS-SSTR] Funkcje Smooth Streaming protokołu *v20140502* [ http://download.microsoft.com/download/9/5/E/95EF66AF-9026-4BB0-A41D-A4F81802D92C/PDF [MS-SSTR]](http://download.microsoft.com/download/9/5/E/95EF66AF-9026-4BB0-A41D-A4F81802D92C/%5bMS-SSTR%5d.pdf)

>   [NORMĄ ISO/IEC 14496 12] Międzynarodowej Organizacji Normalizacyjnej, "technologii informatycznych — kodowania obiektów audio-wizualnej — część 12: ISO podstawowego formatu pliku multimedialnego", ISO/IEC 14496-12:2014, wersji 4, a także sprostowania 1, zmiany 1 i 2.
>   <http://standards.iso.org/ittf/PubliclyAvailableStandards/c061988_ISO_IEC_14496-12_2012.zip>

>   [NORMĄ ISO/IEC 14496-15] Międzynarodowej Organizacji Normalizacyjnej, "technologii informatycznych — kodowania obiektów audio-wizualnej — część 15: karetki NAL jednostki strukturę wideo w formacie ISO do pliku podstawowego nośnika", ISO 14496-15:2015, wersji 3.
>   <http://www.iso.org/iso/home/store/catalogue_tc/catalogue_detail.htm?csnumber=65216>

>   [ISO/IEC 23008-2] Technologii informatycznych — wysokiej wydajności kodowania i nośniki dostarczania w heterogenicznych środowiskach — część 2: kodowanie wideo wysokiej wydajności: 2013 lub najnowszej wersji   <http://standards.iso.org/ittf/PubliclyAvailableStandards/c035424_ISO_IEC_23008-2_2013.zip>

>   [NORMĄ ISO/IEC 23001-7] Technologii informatycznych — technologii systemów MPEG — część 7: szyfrowania Common encryption w ISO podstawowa pliki w formacie pliku nośnika, 2:2015 CENC Edition <http://www.iso.org/iso/catalogue_detail.htm?csnumber=65271>

>   [RFC 6381] 6381 dokumentów RFC organizacji IETF, "" Koderów-dekoderów"i"Profilów"Parametry"pojemnik"typów nośników" <http://tools.ietf.org/html/rfc6381>

>   [MPEG4 RA] Urzędem rejestrowania MP4 "MP4REG" [http://www.mp4ra.org   ](http://go.microsoft.com/fwlink/?LinkId=327787)

>   [RFC2119] Bradner, S., "słowa kluczowe do użycia w dokumentach RFC na wskazywanie poziomów wymaganie", BCP 14, 2119 RFC, marca 1997,   [http://www.rfc-editor.org/rfc/rfc2119.txt   ](http://go.microsoft.com/fwlink/?LinkId=90317)

### <a name="122-informative-references"></a>1.2.2 szczegółowy odwołań 

>   [MS-GLOS] Firma Microsoft Corporation "*Windows protokoły słownik wzorca*."

>   [RFC3548] Ed. Josefsson, S., "Base16 Base32 i kodowania danych Base64", RFC 3548, lipca 2003 [http://www.ietf.org/rfc/rfc3548.txt   ](http://go.microsoft.com/fwlink/?LinkId=90432)

>   [RFC5234] Crocker D., Ed. i Overell, P., "rozszerzony BNF dla specyfikacji Składnia: ABNF", 68, RFC 5234, stycznia 2008 Standard   [http://www.rfc-editor.org/rfc/rfc5234.txt   ](http://go.microsoft.com/fwlink/?LinkId=123096)


## <a name="13-overview"></a>1.3 — omówienie 
---------

>   Jedynie zmian wprowadzonych w specyfikacji Smooth Streaming wymagane dostarczania HEVC podano poniżej. Nagłówki sekcji bez zmian są wyświetlane do obsługi lokalizacji, w którym związane są odwołania specyfikacji Smooth Streaming [MS-SSTR].

## <a name="14-relationship-to-other-protocols"></a>1.4 relacji z innymi protokołami 
--------------------------------

## <a name="15-prerequisitespreconditions"></a>1.5 wymagania wstępne/warunki wstępne 
----------------------------

## <a name="16-applicability-statement"></a>W wersji 1.6 stosowanie — instrukcja 
------------------------

## <a name="17-versioning-and-capability-negotiation"></a>1,7 przechowywanie wersji i negocjacji możliwości 
--------------------------------------

## <a name="18-vendor-extensible-fields"></a>1.8 dostawcy rozszerzonego pola 
-------------------------

>   Stosuje się następujące metody zidentyfikować strumieni w formacie wideo HEVC:

>   * **Niestandardowe kody opisowe dotyczące formatów multimediów:** ta możliwość jest zapewniana przez **FourCC** pola określone w sekcji *2.2.2.5*.
>   Implementacje można upewnij się, czy rozszerzenia nie powodują konfliktu MPEG4-RA, jak określono w zarejestrowani kody rozszerzenia [[ISO/IEC-14496-12] ](http://go.microsoft.com/fwlink/?LinkId=183695)

## <a name="19-standards-assignments"></a>1.9 przydziały standardów 
----------------------

# <a name="2-messages"></a>2 wiadomości 

## <a name="21-transport"></a>2.1 transport 
----------

## <a name="22-message-syntax"></a>2.2 składnia komunikat 
---------------

### <a name="221-manifest-request"></a>2.2.1 żądanie manifestu 

### <a name="222-manifest-response"></a>2.2.2 odpowiedź manifestu 

#### <a name="2221-smoothstreamingmedia"></a>2.2.2.1 SmoothStreamingMedia 

>   **MinorVersion (zmienna):** wersja pomocnicza komunikat odpowiedzi manifestu. MUSI być równa 2. (Brak zmian)

>   **Skali czasu (zmienna):** skali czasu trwania, określony atrybut jako liczbę przyrostów w ciągu sekundy. Wartość domyślna to
>   10000000. (Brak zmian)

>   Zalecana wartość to 90000 reprezentujący dokładny czas trwania klatek i fragmentów zawierających ułamkowych szybkość klatek wideo (na przykład 30/1.001 Hz).

#### <a name="2222-protectionelement"></a>2.2.2.2 ProtectionElement 

ProtectionElement jest obecny, gdy wspólnego szyfrowania (CENC) została zastosowana do strumieni wideo lub audio. Strumienie HEVC szyfrowane odpowiadają Common Encryption wydanie 2 [normą ISO/IEC 23001-7]. Wycinek danych tylko VCL NAL jednostki są szyfrowane.

#### <a name="2223-streamelement"></a>2.2.2.3 StreamElement 

>   **StreamTimeScale (zmienna):** skali czasu dla czasu trwania wartości i godziny w tym strumieniu określony jako liczbę przyrostów w ciągu sekundy. Wartość 90000 jest zalecane dla strumieni HEVC. Wartość dopasowania częstotliwości próbkowania fali (na przykład 48000 lub 44100) jest zalecane dla strumieni audio.

##### <a name="22231-streamprotectionelement"></a>2.2.2.3.1 StreamProtectionElement

#### <a name="2224-urlpattern"></a>2.2.2.4 UrlPattern 

#### <a name="225-trackelement"></a>2.2.5 TrackElement 

>   **FourCC (zmienna):** kod czterech znaków określający, który format nośnika jest używany dla każdej próbki. Następujące zakres wartości jest zarezerwowana mają następujące znaczenie semantyczne:

>  * "hev1": przykłady wideo dla tej ścieżki, użyj HEVC wideo, w formacie "hev1" przykładowy opis określony w [ISO/IEC-14496-15].

>   **CodecPrivateData (zmienna):** danych, która określa parametry specyficzne dla formatu multimediów i wspólne dla wszystkich próbek w ścieżce, reprezentowany jako ciąg kodowany hex bajtów. Format i semantyczne sekwencji bajtów zależy od wartości **FourCC** pola w następujący sposób:

>   * Gdy TrackElement opisuje wideo HEVC **FourCC** pole jest równa **"hev1"** i;

>   **CodecPrivateData** pole zawiera ciąg szesnastkowy na stałe reprezentację następująca sekwencja bajtów, określona w ABNF [[RFC5234]:](http://go.microsoft.com/fwlink/?LinkId=123096) (bez zmian z MS SSTR)

>   * %x 00 %x 00 %x 00 %x 01 SPSField %x 00% 00-x %x 00 %x 01 PPSField

>   * SPSField zawiera wartość parametru sekwencji (SPS).

>   * PPSField zawiera wartość parametru wycinek (PPS).

>   Uwaga: Wideo parametru Ustaw (wiceprezesi ds) nie jest zawarta w CodecPrivateData, ale powinny być zawarte w nagłówku pliku przechowywanych plików w polu "hvcC". Systemów przy użyciu Smooth Streaming Protocol sygnalizuje, dodatkowe parametry dekodowania (na przykład HEVC warstwy) przy użyciu atrybutu niestandardowego "kodery-dekodery."

##### <a name="22251-customattributeselement"></a>2.2.2.5.1 CustomAttributesElement 

#### <a name="226-streamfragmentelement"></a>2.2.6 StreamFragmentElement 

>   **MajorVersion w SmoothStreamingMedia** pola musi być równa 2, i **MinorVersion** pola musi mieć wartość 2. (Brak zmian)

##### <a name="22261-trackfragmentelement"></a>2.2.2.6.1 TrackFragmentElement 

### <a name="223-fragment-request"></a>2.2.3 fragment żądania 

>   **Uwaga**: żądany domyślny format media **MinorVersion** 2 i "hev1" jest "iso8" brand ISO Base formatu pliku multimedialnego określonego w [normą ISO/IEC 14496 12] ISO Base Media plik formatu czwarty Edition, a [normą ISO/IEC 23001-7] Wydanie drugie wspólnego szyfrowania.

### <a name="224-fragment-response"></a>2.2.4 fragmentu odpowiedzi 

#### <a name="2241-moofbox"></a>2.2.4.1 MoofBox 

#### <a name="2242-mfhdbox"></a>2.2.4.2 MfhdBox 

#### <a name="2243-trafbox"></a>2.2.4.3 TrafBox 

#### <a name="2244-tfxdbox"></a>2.2.4.4 TfxdBox 

>   **TfxdBox** jest przestarzały i jej funkcji zastąpiony przez Śledź fragmentu dekodowania czasu pole (tfdt) określone w sekcji [normą ISO/IEC 14496 12] 8.8.12.

>   **Uwaga**: klient może obliczania czasu trwania fragmentu przez zsumowanie okresów próbki wymienione w polu Uruchom śledzenie (trun) lub mnożąc liczbę próbek czasu próbki domyślny czas trwania. BaseMediaDecodeTime w czasie trwania "tfdt" oraz fragment jest równe parametr adresu URL czasu dla następnego fragmentu.

>   Producent odwołanie okno czasu (prft) powinien zostać wstawiony przed polem fragmentu Movie (moof) zgodnie z potrzebami, aby wskazać odpowiadający Czas dekodowania ścieżki fragmentu pierwszego przykładu odwołuje się pole fragmentu filmu jako czas UTC określonego w [normą ISO/IEC 14496 sekcja-12] 8.16.5.

#### <a name="2245-tfrfbox"></a>2.2.4.5 TfrfBox 

>   **TfrfBox** jest przestarzały i jej funkcji zastąpiony przez Śledź fragmentu dekodowania czasu pole (tfdt) określone w sekcji [normą ISO/IEC 14496 12] 8.8.12.

>   **Uwaga**: klient może obliczania czasu trwania fragmentu przez zsumowanie okresów próbki wymienione w polu Uruchom śledzenie (trun) lub mnożąc liczbę próbek czasu próbki domyślny czas trwania. BaseMediaDecodeTime w czasie trwania "tfdt" oraz fragment jest równe parametr adresu URL czasu dla następnego fragmentu. Wyszukiwać czy adresy są przestarzałe, ponieważ ich opóźnienie transmisja strumieniowa na żywo.

#### <a name="2246-tfhdbox"></a>2.2.4.6 TfhdBox 

>   **TfhdBox** i domyślne wartości na metadanych próbki we fragmencie hermetyzowania pola pokrewne. Składnia **TfhdBox** pole jest podzbiorem strict składni pola nagłówka fragmentu ścieżki zdefiniowane w [[ISO/IEC-14496-12]](http://go.microsoft.com/fwlink/?LinkId=183695) 8.8.7 sekcji.

>   **BaseDataOffset (8 bajtów):** przesunięcie, w bajtach, od początku **MdatBox** pole do pola próbki **MdatBox** pola. Aby nastąpiło to ograniczenie, musi zostać ustawiona flaga domyślne base to moof (0x020000).

#### <a name="2247-trunbox"></a>2.2.4.7 TrunBox 

>   **TrunBox** i hermetyzowania pola pokrewne na przykład metadanych dla żądanego fragmentu. Składnia **TrunBox** jest podzbiorem strict wersji 1 Śledź fragmentu pole Uruchom zdefiniowane w [[ISO/IEC-14496-](http://go.microsoft.com/fwlink/?LinkId=183695)*12]* 8.8.8 sekcji.

>   **SampleCompositionTimeOffset (4 bajty):** przesunięcie czasu kompozycji próbki każdej próbki ustawione tak, aby podczas prezentacji pierwszego przykładu przedstawioną w fragment jest taki sam, jak podczas dekodowania pierwszego przykładu zdekodowana. Stosuje się przesunięcia kompozycji ujemna próbki wideo,

>   zgodnie z definicją w [[ISO/IEC-14496-12].](http://go.microsoft.com/fwlink/?LinkId=183695)

>   Uwaga: To pozwala uniknąć błąd synchronizacji wideo spowodowane opóźnionych audio równe największy opóźnienie usunięcia buforu dekodowane obrazu wideo i obsługuje chronometrażu prezentacji między alternatywnych fragmentów, które mogą mieć różne usuwania opóźnienia.

>   Składnia pola zdefiniowane w tej sekcji, określona w ABNF [[RFC5234]](http://go.microsoft.com/fwlink/?LinkId=123096) pozostaje bez zmian, z wyjątkiem w następujący sposób:

>   SampleCompositionTimeOffset = SIGNED_INT32

#### <a name="2248-mdatbox"></a>2.2.4.8 MdatBox 

#### <a name="2249-fragment-response-common-fields"></a>2.2.4.9 fragmentu odpowiedzi wspólnych pól 

### <a name="225-sparse-stream-pointer"></a>2.2.5 wskaźnik rozrzedzony strumień 

### <a name="226-fragment-not-yet-available"></a>2.2.6 fragmentu nie jest jeszcze dostępna 

### <a name="227-live-ingest"></a>2.2.7 pozyskiwania na żywo 

#### <a name="2271-filetype"></a>2.2.7.1 FileType 

>   **Typ pliku (zmienna):** Określa podtyp i planowane wykorzystanie MPEG-4 ([[MPEG4 RA])](http://go.microsoft.com/fwlink/?LinkId=327787) plików i atrybuty wysokiego poziomu.

>   **MajorBrand (zmienna):** głównych marki pliku multimedialnego. MUSI być ustawiona na "isml."

>   **MinorVersion (zmienna):** wersji pomocniczej pliku multimedialnego. MUSI być równa 1.

>   **CompatibleBrands (zmienna):** określa obsługiwanych marek MPEG-4.
>   MUSI zawierać "ccff" i "iso8."

>   Składnia pola zdefiniowane w tej sekcji, określona w ABNF [[RFC5234]](http://go.microsoft.com/fwlink/?LinkId=123096) wygląda następująco:

    FileType = MajorBrand MinorVersion CompatibleBrands
    MajorBrand = STRING_UINT32
    MinorVersion = STRING_UINT32
    CompatibleBrands = "ccff" "iso8" 0\*(STRING_UINT32)

**Uwaga**: marek zgodności "ccff" i "iso8" wskazują, że fragmenty jest zgodne z "Wspólnego kontenera pliku formatu" i Common Encryption [normą ISO/IEC 23001-7] oraz ISO Base Media plik formatu wersji 4 [normą ISO/IEC 14496 12].

#### <a name="2272-streammanifestbox"></a>2.2.7.2 StreamManifestBox 

##### <a name="22721-streamsmil"></a>2.2.7.2.1 StreamSMIL 

#### <a name="2273-liveservermanifestbox"></a>2.2.7.3 LiveServerManifestBox 

##### <a name="22731-livesmil"></a>2.2.7.3.1 LiveSMIL 

#### <a name="2274-moovbox"></a>2.2.7.4 MoovBox 

#### <a name="2275-fragment"></a>2.2.7.5 fragment 

##### <a name="22751-track-fragment-extended-header"></a>2.2.7.5.1 Śledź fragmentu rozszerzony nagłówka 

### <a name="228-server-to-server-ingest"></a>2.2.8 pozyskiwania serwer serwer 

# <a name="3-protocol-details"></a>Szczegóły protokołu 3 


## <a name="31-client-details"></a>3.1 szczegóły klienta 

### <a name="311-abstract-data-model"></a>3.1.1 Model danych abstrakcyjny 

#### <a name="3111-presentation-description"></a>3.1.1.1 opis elementu prezentacji 

>   Element danych opis prezentacji hermetyzuje wszystkie metadane prezentacji.

>   Metadane prezentacji: Zestaw metadane, które są wspólne dla wszystkich strumieni w prezentacji. Metadane prezentacji obejmuje następujące pola określone w sekcji *2.2.2.1*:

>   * **MajorVersion**
>   * **MinorVersion**
>   * **TimeScale**
>   * **Czas trwania**
>   * **IsLive**
>   * **LookaheadCount**
>   * **DVRWindowLength**

>   Prezentacje zawierający zestaw HEVC strumienie są:

    MajorVersion = 2
    MinorVersion = 2

>   LookaheadCount = 0 (Uwaga: pola przestarzała)

>   NALEŻY także ustawić prezentacji:

    TimeScale = 90000

>   Kolekcja strumienia: Kolekcję elementów opis strumienia danych, zgodnie z sekcją *3.1.1.1.2*.

>   Opis ochrony: Kolekcję elementów opis metadanych systemu ochrony danych, zgodnie z sekcją *3.1.1.1.1*.

##### <a name="31111-protection-system-metadata-description"></a>3.1.1.1.1 Opis metadanych systemu ochrony 

>   Element danych opis metadanych systemu ochrony hermetyzuje metadanych specyficznych dla pojedynczego systemu ochrony zawartości. (Brak zmian)

>   Opis nagłówka ochrony: Ochrona zawartości metadanych, które odnoszą się do pojedynczego systemu ochrony zawartości. Opis elementu nagłówka ochrony obejmuje następujące pola określone w sekcji *2.2.2.2*:

>   * **SystemID**
>   * **ProtectionHeaderContent**

##### <a name="31112-stream-description"></a>3.1.1.1.2 Opis elementu strumienia 

###### <a name="311121-track-description"></a>3.1.1.1.2.1 opis elementu Śledź 

###### <a name="3111211-custom-attribute-description"></a>3.1.1.1.2.1.1 Opis atrybutu niestandardowego 

##### <a name="3113-fragment-reference-description"></a>3.1.1.3 opis elementu fragmentu odwołania 

###### <a name="31131-track-specific-fragment-reference-description"></a>3.1.1.3.1 Opis odwołania fragmentu specyficzne dla ścieżki 

#### <a name="3112-fragment-description"></a>3.1.1.2 Opis elementu fragmentu 

##### <a name="31121-sample-description"></a>3.1.1.2.1 opis elementu próbki 

### <a name="312-timers"></a>3.1.2 czasomierze 

### <a name="313-initialization"></a>3.1.3 inicjowania 

### <a name="314-higher-layer-triggered-events"></a>3.1.4 wyższej warstwie wyzwalane zdarzenia 

#### <a name="3141-open-presentation"></a>3.1.4.1 Otwórz prezentacji 

#### <a name="3142-get-fragment"></a>3.1.4.2 pobrać fragmentu 

#### <a name="3143-close-presentation"></a>3.1.4.3 Zamknij prezentacji 

### <a name="315-processing-events-and-sequencing-rules"></a>3.1.5 przetwarzania zdarzeń i sekwencjonowania reguły 

#### <a name="3151-manifest-request-and-manifest-response"></a>3.1.5.1 manifestu żądania i odpowiedzi manifestu 

#### <a name="3152-fragment-request-and-fragment-response"></a>3.1.5.2 fragmentu żądania i odpowiedzi fragmentu

## <a name="32-server-details"></a>3.2 szczegóły serwera

## <a name="33-live-encoder-details"></a>3.3 szczegóły kodera na żywo 

# <a name="4-protocol-examples"></a>Przykłady protokołu 4 

# <a name="5-security"></a>5 zabezpieczeń 

## <a name="51-security-considerations-for-implementers"></a>5.1 uwagi dotyczące obiektów implementujących zabezpieczeń 
-----------------------------------------

>   Jeśli zawartość transportowane przy użyciu tego protokołu wysokiej wartości handlowej, System ochrony zawartości należy zapobiec nieautoryzowanemu użyciu zawartości. **ProtectionElement** mogą być używane do przenoszenia metadanych związanych z użyciem systemu ochrony zawartości. Zawartość chronionego audio i wideo są szyfrowane zgodnie z określonym MPEG wspólnego szyfrowania wydanie drugie: 2015 [normą ISO/IEC 23001-7].

>   **Uwaga**: wideo HEVC wycinek danych tylko VCL NALs jest zaszyfrowany. Wycinek nagłówki i innych NALs są dostępne dla aplikacji prezentacji przed ich odszyfrowywania. w zabezpieczoną ścieżką wideo zaszyfrowanych informacji nie jest dostępne dla aplikacji prezentacji.

# <a name="52-index-of-security-parameters"></a>5.2 indeksu parametrów zabezpieczeń 
-----------------------------


| **Parametr zabezpieczeń**  | **Section**         |
|-------------------------|---------------------|
| ProtectionElement       | *2.2.2.2*           |
| Typowe pola szyfrowania | *[ISO/IEC 23001-7]* |

# <a name="53-common-encryption-boxes"></a>5.3 typowe pola szyfrowania
-----------------------

Następujące pola mogą występować w odpowiedzi fragmentu po zastosowaniu Common Encryption i są określone w [normą ISO/IEC 23001-7] lub [normą ISO/IEC 14496 12]:

1.  Pole nagłówka określonych ochrony systemu (pssh)

2.  Przykładowe okno szyfrowania (senc)

3.  Przykładowe pomocnicze przesunięcie pole informacje (saio)

4.  Przykładowe pomocnicze informacji rozmiar pola (saiz)

5.  W polu Opis grupy przykład (sgpd)

6.  Przykładowe okno grupy (sbgp)

-----------------------

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

[image1]: ./media/media-services-fmp4-live-ingest-overview/media-services-image1.png
[image2]: ./media/media-services-fmp4-live-ingest-overview/media-services-image2.png
[image3]: ./media/media-services-fmp4-live-ingest-overview/media-services-image3.png
[image4]: ./media/media-services-fmp4-live-ingest-overview/media-services-image4.png
[image5]: ./media/media-services-fmp4-live-ingest-overview/media-services-image5.png
[image6]: ./media/media-services-fmp4-live-ingest-overview/media-services-image6.png
[image7]: ./media/media-services-fmp4-live-ingest-overview/media-services-image7.png
