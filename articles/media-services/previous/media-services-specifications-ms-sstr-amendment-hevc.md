---
title: Usługa Azure Media Services — Smooth Streaming zmiany protokołu (MS-SSTR) dla — HEVC | Dokumentacja firmy Microsoft
description: Tej specyfikacji opisuje protokół i format podzielonej zawartości na podstawie MP4 transmisja strumieniowa na żywo za pomocą — HEVC w usłudze Azure Media Services. Jest to zmiana do dokumentacji protokołu Smooth Streaming (MS-SSTR) do uwzględnienia obsługi — HEVC pozyskiwania i przesyłania strumieniowego. Zmiany wymagane w celu dostarczenia — HEVC są określone w tym artykule, z wyjątkiem były "(bez zmian)" oznacza tekst jest kopiowany tylko wyjaśnienia.
services: media-services
documentationcenter: ''
author: cenkdin
manager: femila
editor: ''
ms.assetid: f27d85de-2cb8-4269-8eed-2efb566ca2c6
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: johndeu;
ms.openlocfilehash: b26252ddda7997bebd730bb4c1007f76b3e645a4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60650713"
---
# <a name="smooth-streaming-protocol-ms-sstr-amendment-for-hevc"></a>Bezproblemowe przesyłania strumieniowego zmiany protokołu (MS-SSTR) dla — HEVC 

## <a name="1-introduction"></a>Wprowadzenie 1 

Ten artykuł zawiera szczegółowe zmiany, które mają być stosowane do specyfikacji Smooth Streaming protokołu [MS-SSTR] umożliwiające Smooth Streaming z — HEVC zakodowane wideo. W tej specyfikacji możemy konturu tylko zmiany wymagane do świadczenia — HEVC kodera-dekodera wideo. Artykuł poniżej tego samego schematu numerowania jako specification [MS-SSTR]. Pusty nagłówki, przedstawione w artykule podano poznaniu czytnik do ich pozycji w specification [MS-SSTR].  "(Bez zmian)" oznacza, że tekst jest kopiowany wyłącznie do celów wyjaśnienia.

Ten artykuł zawiera wymagania implementacji technicznej sygnalizowanie — HEVC kodera-dekodera wideo w manifeście Smooth Streaming i odwołania normatywne są aktualizowane, aby odwoływać się do bieżącego standardów MPEG, które obejmują — HEVC, wspólnego szyfrowania — HEVC i pola nazwy dla formatu plików multimediów Base ISO zostały zaktualizowanie, aby były zgodne z najnowszej specyfikacji. 

Odwołania Smooth Streaming specyfikacji protokołu [MS-SSTR] w tym artykule opisano format użyty w celu dostarczania na żywo i na żądanie multimediów cyfrowych, takich jak audio i wideo, w następujących sposobów: z kodera na serwerze sieci web z serwera do innego serwera, a także z serwer do klienta HTTP.
Korzystanie z MPEG-4 ([[MPEG4 RA])](https://go.microsoft.com/fwlink/?LinkId=327787)-dostarczanie struktury oparte na danych za pośrednictwem protokołu HTTP umożliwia bezproblemowe przełączanie niemal w czasie rzeczywistym między różne poziomy jakości zawartości multimedialnej skompresowany. Wynik jest stałe odtwarzanie dla użytkownika końcowego klienta HTTP, nawet jeśli sieć i renderowanie wideo warunki ulegną zmianie na komputerze klienckim albo urządzeniu.

## <a name="11-glossary"></a>1.1 słownik 

Poniższe terminy są zdefiniowane w *[MS-GLOS]*:

>   **Unikatowy identyfikator globalny (GUID) powszechnie Unikatowy identyfikator (UUID)**

W tym dokumencie dotyczą następujące warunki:

>  **kompozycja czas:** Czas przykład znajduje się po stronie klienta, zgodnie z definicją w [[ISO/IEC-14496-12].](https://go.microsoft.com/fwlink/?LinkId=183695)
> 
>   **CENC**: Common Encryption oraz jako zdefiniowaną w [ISO/IEC 23001-7] wydanie.
> 
>   **Czas dekodowania:** Czas przykład jest musiał zostać zdekodowane z poziomu klienta, zgodnie z definicją w [[ISO/IEC https://go.microsoft.com/fwlink/?LinkId=18369514496-12].](https://go.microsoft.com/fwlink/?LinkId=183695)

**fragment:** Niezależnie do pobrania jednostki **media** który składa się z co najmniej jeden **przykłady**.

>   **— HEVC:** Wysoka wydajność wideo kodowania, zgodnie z definicją w [ISO/IEC 23008-2]
> 
>   **Manifest:** Metadane dotyczące **prezentacji** umożliwiająca klienta wysyłać żądania **media**. **nośnik:** Skompresowanych danych audio, wideo i tekstu używany przez klienta, aby odtworzyć **prezentacji**. **format multimediów:** Dobrze zdefiniowany format służący do reprezentowania audio lub wideo jako skompresowany **przykładowe**.
> 
>   **prezentacja:** Zbiór wszystkich **strumieni** i pokrewne metadane potrzebne do odtwarzania filmu jednego. **request:** Wiadomości HTTP wysłanych z klienta do serwera, zgodnie z definicją w [[specyfikacją RFC2616].](https://go.microsoft.com/fwlink/?LinkId=90372) **response:** Wiadomości HTTP wysyłane z serwera do klienta, zgodnie z definicją w [[specyfikacją RFC2616].](https://go.microsoft.com/fwlink/?LinkId=90372)
> 
>   **Przykład:** Najmniejsza podstawową jednostką (na przykład ramki) w którym **media** są przechowywane i przetwarzane.
> 
>   **MAJA, NALEŻY, NALEŻY, NIE POWINIEN, NIE MOGĄ:** Te warunki (wersalikami) są używane zgodnie z opisem w [[RFC2119].](https://go.microsoft.com/fwlink/?LinkId=90317) Wszystkie instrukcje użytkowania zachowanie opcjonalne albo mogą SHOULD lub nie powinien.

## <a name="12-references"></a>1.2 odwołania

>   Odwołania do dokumentacja specyfikacji Open firmy Microsoft nie dołączaj publikowania roku, ponieważ znajdują się łącza do najnowszej wersji dokumentów, które są często aktualizowane. Odwołania do innych dokumentów obejmują rok publikowania, gdy jest ona dostępna.

### <a name="121-normative-references"></a>1.2.1 odwołania normatywne 

>  [MS-SSTR] Zestaw Smooth Streaming protokołu *v20140502* [https://msdn.microsoft.com/library/ff469518.aspx](https://msdn.microsoft.com/library/ff469518.aspx)
> 
>   [ISO/IEC 14496 12] Międzynarodowej Organizacji Normalizacyjnej, "informatyka — kodowanie obiektów audiowizualnych — część 12: ISO Media podstawowy Format pliku", ISO/IEC 14496-12:2014, w wersji 4, a także sprostowania 1, zmiany 1 i 2.
>   <https://standards.iso.org/ittf/PubliclyAvailableStandards/c061988_ISO_IEC_14496-12_2012.zip>
> 
>   [ISO/IEC 14496 15] Międzynarodowej Organizacji Normalizacyjnej, "informatyka — kodowanie obiektów audiowizualnych — część 15: Powrót karetki NAL jednostki ze strukturą wideo w formacie pliku ISO Base nośnika", ISO 14496-15:2015, wersja 3.
>   <https://www.iso.org/iso/home/store/catalogue_tc/catalogue_detail.htm?csnumber=65216>
> 
>   [ISO/IEC 23008 2] Informatyka — wysokiej wydajności na kodowanie i media dostarczania w heterogenicznych środowiskach — część 2: Wysoka wydajność wideo kodowania: w wersji 2013 lub najnowszych   <https://standards.iso.org/ittf/PubliclyAvailableStandards/c035424_ISO_IEC_23008-2_2013.zip>
> 
>   [ISO/IEC 23001-7] Informatyka — innych technologii systemów MPEG — część 7: Stosowanie szyfrowania Common encryption w ISO media podstawowego pliku pliki w formacie, 2:2015 CENC Edition <https://www.iso.org/iso/catalogue_detail.htm?csnumber=65271>
> 
>   [RFC 6381] 6381 dokumentów RFC organizacji IETF, "" Kodery-dekodery"i"Profile"Parametry"zasobnika"typów nośników" <https://tools.ietf.org/html/rfc6381>
> 
>   [POMOC ZDALNA MPEG4] Urząd rejestrowania w formacie MP4, "MP4REG" [http://www.mp4ra.org](https://go.microsoft.com/fwlink/?LinkId=327787)
> 
>   [RFC2119] Bradner, S., "słów kluczowych do użytku w specyfikacjach RFC na wskazywanie poziomów wymagań", BCP 14, 2119 RFC, marca 1997,   [https://www.rfc-editor.org/rfc/rfc2119.txt](https://go.microsoft.com/fwlink/?LinkId=90317)

### <a name="122-informative-references"></a>1.2.2 informacyjnymi odwołaniami 

>   [MS-GLOS] Firmy Microsoft Corporation "*Windows protokoły słownik wzorzec*."
> 
>   [RFC3548] Wyd Josefsson, S., "Base16 Base32 i kodowania danych Base64", RFC 3548, lipca 2003 [https://www.ietf.org/rfc/rfc3548.txt](https://go.microsoft.com/fwlink/?LinkId=90432)
> 
>   [RFC5234] Crocker D., wyd i Overell, P., "rozszerzony BNF dla specyfikacji składni: ABNF", STD 68, RFC 5234, January 2008,   [https://www.rfc-editor.org/rfc/rfc5234.txt](https://go.microsoft.com/fwlink/?LinkId=123096)


## <a name="13-overview"></a>1.3 — omówienie 

>   Jedynie zmian wprowadzonych do specyfikacji Smooth Streaming, wymaganych do dostarczania — HEVC zostały wyszczególnione poniżej. Nagłówki sekcji niezmienione należą do utrzymania lokalizacji w odwołania Smooth Streaming specification [MS-SSTR].

## <a name="14-relationship-to-other-protocols"></a>1.4 relacji z innymi protokołami 

## <a name="15-prerequisitespreconditions"></a>W wersji 1.5 wymagania wstępne/warunków wstępnych 

## <a name="16-applicability-statement"></a>W wersji 1.6 Instrukcja stosowania 

## <a name="17-versioning-and-capability-negotiation"></a>1.7 przechowywanie wersji i negocjacji możliwości 

## <a name="18-vendor-extensible-fields"></a>1.8 dostawcy rozszerzonego pola 

>   Stosuje się następującą metodę identyfikowania strumieni w formacie wideo — HEVC:
> 
>   * **Niestandardowe kody opisowe formaty multimediów:** Ta funkcja jest dostępna przez **FourCC** pola, jak to określono w sekcji *2.2.2.5*.
>   Implementacje można upewnić się, że rozszerzenia nie powodują konfliktów zarejestrowani kody rozszerzenia MPEG4-RA, jak to określono w [[ISO/IEC-14496-12]](https://go.microsoft.com/fwlink/?LinkId=183695)

## <a name="19-standards-assignments"></a>1.9 przydziały standardów 

## <a name="2-messages"></a>2 wiadomości 

## <a name="21-transport"></a>2.1 transport

## <a name="22-message-syntax"></a>2.2 składnia komunikatu 

### <a name="221-manifest-request"></a>2.2.1 żądanie manifestu 

### <a name="222-manifest-response"></a>2.2.2 odpowiedź manifestu 

#### <a name="2221-smoothstreamingmedia"></a>2.2.2.1 SmoothStreamingMedia 

>   **MinorVersion (zmienna):** Wersja pomocnicza komunikat odpowiedzi z manifestu. MUSI być równa 2. (Nie zmieniać)
> 
>   **Skala czasu (zmienna):** Skala czasu atrybutu czasu trwania w jednej sekundy jako liczbę przyrostów określono. Wartość domyślna to
> 1. (Nie zmieniać)
> 
>    Zalecaną wartością jest 90000 reprezentująca dokładny czas trwania klatki wideo i fragmentów, zawierające ułamkowe szybkość klatek filmu wideo (na przykład, 30/1.001 Hz).

#### <a name="2222-protectionelement"></a>2.2.2.2 ProtectionElement 

ProtectionElement jest obecna, gdy Common Encryption (CENC) zostały doliczone do strumieni audio lub wideo. — HEVC zaszyfrowane strumienie są zgodne z szyfrowania Common Encryption wydanie 2 [ISO/IEC 23001-7]. Wycinek danych tylko VCL NAL jednostki są szyfrowane.

#### <a name="2223-streamelement"></a>2.2.2.3 StreamElement 

>   **StreamTimeScale (zmienna):** Skala czasu dla czasu trwania wartości i godziny w tym strumieniu w jednej sekundy jako liczbę przyrostów określono. Wartość 90000 jest zalecane dla strumieni — HEVC. Wartość dopasowania częstotliwość próbkowania fali (na przykład 48000 lub 44100) jest zalecane dla strumieni audio.

##### <a name="22231-streamprotectionelement"></a>2.2.2.3.1 StreamProtectionElement

#### <a name="2224-urlpattern"></a>2.2.2.4 UrlPattern 

#### <a name="225-trackelement"></a>2.2.5 TrackElement 

>   **FourCC (zmienna):** 4 znakowy kod, który identyfikuje format nośnika jest używany dla każdego przykładu. Następujący zakres wartości jest zarezerwowana mają następujące znaczenie semantyczne:
> 
> * "hev1": Przykłady wideo dla tego Śledź użycie — HEVC wideo, przy użyciu hev1 przykładowe formatu opis określonego w [ISO/IEC-14496-15].
> 
>   **CodecPrivateData (zmienna):** Dane, które określa parametry specyficzne dla formatu multimediów i wspólne dla wszystkich przykładów w ścieżce, reprezentowane jako ciąg kodowany hex bajtów. Format i znaczenia semantycznego sekwencji bajtów zależy od wartości **FourCC** pola w następujący sposób:
> 
>   * Gdy TrackElement opisuje wideo — HEVC **FourCC** pola jest równa **"hev1"** i;
> 
>   **CodecPrivateData** pole zawiera kodowane hex ciąg reprezentujący poniższa sekwencja bajtów, określone w ABNF [[RFC5234]:](https://go.microsoft.com/fwlink/?LinkId=123096) (bez zmian od MS SSTR)
> 
>   * %x 00% 00-x %x 00 %x 01 SPSField % 00-x %x 00% 00-x %x 01 PPSField
> 
>   * SPSField zawiera Ustaw parametr sekwencji (SPS).
> 
>   * PPSField zawiera Ustaw parametr wycinek (PPS).
> 
>   Uwaga: Film wideo parametru zestawu (wiceprezesi ds) nie jest zawarta w CodecPrivateData, ale powinien być zawarty w nagłówku pliku przechowywanych plików w polu "hvcC". Systemy przy użyciu Smooth Streaming protokołu musi sygnał dodatkowe parametry dekodowania (na przykład warstwa — HEVC) za pomocą atrybutu niestandardowego "kodery-dekodery."

##### <a name="22251-customattributeselement"></a>2.2.2.5.1 CustomAttributesElement 

#### <a name="226-streamfragmentelement"></a>2.2.6 StreamFragmentElement 

>   **MajorVersion firmy SmoothStreamingMedia** pola musi być równa 2, a **MinorVersion** pola musi być równa 2. (Nie zmieniać)

##### <a name="22261-trackfragmentelement"></a>2.2.2.6.1 TrackFragmentElement 

### <a name="223-fragment-request"></a>2.2.3 fragment żądania 

>   **Uwaga**: Domyślny format nośnika żądania **MinorVersion** 2 i "hev1" to "iso8" marki ISO Base Media Format pliku określonego w [ISO/IEC 14496 12] ISO Base Media plik formatu wydanie czwarte, a drugi typowe szyfrowanie [ISO/IEC 23001-7] Wydanie.

### <a name="224-fragment-response"></a>2.2.4 fragment odpowiedzi 

#### <a name="2241-moofbox"></a>2.2.4.1 MoofBox 

#### <a name="2242-mfhdbox"></a>2.2.4.2 MfhdBox 

#### <a name="2243-trafbox"></a>2.2.4.3 TrafBox 

#### <a name="2244-tfxdbox"></a>2.2.4.4 TfxdBox 

>   **TfxdBox** jest przestarzały i jej funkcji zastąpione przez śledzenie fragmentu dekodowania pole godziny (tfdt) określone w sekcji [ISO/IEC 14496 12] 8.8.12.
> 
>   **Uwaga**: Klientowi mogą obliczyć czas trwania fragmentu jako suma czasów trwania próbki, wymienione w polu Uruchom śledzenie (trun) lub pomnożenie liczby próbek czas domyślny czas trwania próbki. BaseMediaDecodeTime w czasie trwania "tfdt", a także fragment jest równa parametr czasu adresu URL dla następnego fragmentu.
> 
>   Producent okno czasu odwołania (prft) powinien zostać wstawiony przed polem Fragment filmu (moof), zgodnie z potrzebami, aby wskazać czas UTC, odpowiadający Czas dekodowania Fragment ścieżki pierwszego przykładu odwołuje się polu Fragment filmu jako określone w [14496 ISO/IEC -12] części 8.16.5.

#### <a name="2245-tfrfbox"></a>2.2.4.5 TfrfBox 

>   **TfrfBox** jest przestarzały i jej funkcji zastąpione przez śledzenie fragmentu dekodowania pole godziny (tfdt) określone w sekcji [ISO/IEC 14496 12] 8.8.12.
> 
>   **Uwaga**: Klientowi mogą obliczyć czas trwania fragmentu jako suma czasów trwania próbki, wymienione w polu Uruchom śledzenie (trun) lub pomnożenie liczby próbek czas domyślny czas trwania próbki. BaseMediaDecodeTime w czasie trwania "tfdt", a także fragment jest równa parametr czasu adresu URL dla następnego fragmentu. Spoglądaj w przyszłość czy adresy są przestarzałe, ponieważ ich opóźnienie, przesyłanie strumieniowe na żywo.

#### <a name="2246-tfhdbox"></a>2.2.4.6 TfhdBox 

>   **TfhdBox** i powiązanych pól hermetyzacji wartości domyślne na przykład metadanych w fragmentu. Składnia **TfhdBox** pole jest ścisłym podzbiorem składni pola nagłówka fragmentu śledzenie zdefiniowane w [[ISO/IEC-14496-12]](https://go.microsoft.com/fwlink/?LinkId=183695) 8.8.7 sekcji.
> 
>   **BaseDataOffset (8 bytes):** Przesunięcie w bajtach od początku **MdatBox** pole do pola przykładowe **MdatBox** pola. Celu sygnalizowania, że to ograniczenie, można ustawić flagi domyślne base to moof (0x020000).

#### <a name="2247-trunbox"></a>2.2.4.7 TrunBox 

>   **TrunBox** i hermetyzacji powiązanych pól, na przykład metadanych dla żądanego fragmentu. Składnia **TrunBox** jest podzbiór wersji 1 śledzenie fragmentu pole Uruchom zdefiniowane w [[ISO/IEC-14496-](https://go.microsoft.com/fwlink/?LinkId=183695)*12]* 8.8.8 sekcji.
> 
>   **SampleCompositionTimeOffset (4 bajty):** Przesunięcie przedziału czasu kompozycji każdy przykład ustawione tak, aby podczas prezentacji pierwszego przykładu przedstawiony w fragmentu jest równy Czas dekodowania pierwszy przykład zdekodowany. Stosuje się przesunięcia kompozycji ujemna próbki wideo,
> 
>   zgodnie z definicją w [[ISO/IEC-14496-12].](https://go.microsoft.com/fwlink/?LinkId=183695)
> 
>   Uwaga: To pozwala uniknąć błędów synchronizacji wideo, spowodowane opóźnionych audio równy największych opóźnienie usunięcia buforu zdekodowany obrazu wideo i utrzymuje prezentacji czasu między alternatywnych fragmentów, które mogą mieć różne usuwania opóźnienia.
> 
>   Składnia pól zdefiniowanych w tej sekcji, określone w ABNF [[RFC5234]](https://go.microsoft.com/fwlink/?LinkId=123096) pozostają bez zmian, z wyjątkiem w następujący sposób:
> 
>   SampleCompositionTimeOffset = SIGNED_INT32

#### <a name="2248-mdatbox"></a>2.2.4.8 MdatBox 

#### <a name="2249-fragment-response-common-fields"></a>2.2.4.9 fragmentu typowych pól odpowiedzi 

### <a name="225-sparse-stream-pointer"></a>2.2.5 wskaźnik rozrzedzone Stream 

### <a name="226-fragment-not-yet-available"></a>2.2.6 fragment nie są jeszcze dostępne 

### <a name="227-live-ingest"></a>2.2.7 pozyskiwanie na żywo 

#### <a name="2271-filetype"></a>2.2.7.1 typ pliku 

>   **Typ pliku (zmienna):** Określa podtyp i zamierzone użycie MPEG-4 ([[MPEG4 RA])](https://go.microsoft.com/fwlink/?LinkId=327787) plików i atrybuty wysokiego poziomu.
> 
>   **MajorBrand (zmienna):** Główne marki plików multimedialnych. MUSI być równa "isml."
> 
>   **MinorVersion (zmienna):** Wersja pomocnicza pliku multimedialnego. MUSI być równa 1.
> 
>   **CompatibleBrands (zmienna):** Określa obsługiwane marek MPEG-4.
>   MUSI zawierać "ccff" i "iso8."
> 
>   Składnia pól zdefiniowanych w tej sekcji, określone w ABNF [[RFC5234]](https://go.microsoft.com/fwlink/?LinkId=123096) jest następująca:

    FileType = MajorBrand MinorVersion CompatibleBrands
    MajorBrand = STRING_UINT32
    MinorVersion = STRING_UINT32
    CompatibleBrands = "ccff" "iso8" 0\*(STRING_UINT32)

**Uwaga**: Zgodność marek "ccff" i "iso8" wskazują, że fragmenty są zgodne z "Wspólny Format plików kontenera" i szyfrowania Common Encryption [ISO/IEC 23001-7] i ISO Base Media plik formatu w wersji 4 [ISO/IEC 14496 12].

#### <a name="2272-streammanifestbox"></a>2.2.7.2 StreamManifestBox 

##### <a name="22721-streamsmil"></a>2.2.7.2.1 StreamSMIL 

#### <a name="2273-liveservermanifestbox"></a>2.2.7.3 LiveServerManifestBox 

##### <a name="22731-livesmil"></a>2.2.7.3.1 LiveSMIL 

#### <a name="2274-moovbox"></a>2.2.7.4 MoovBox 

#### <a name="2275-fragment"></a>2.2.7.5 fragment 

##### <a name="22751-track-fragment-extended-header"></a>2.2.7.5.1 Śledź fragmentu rozszerzone nagłówka 

### <a name="228-server-to-server-ingest"></a>2.2.8 pozyskiwanie serwer serwer 

## <a name="3-protocol-details"></a>Szczegóły protokołu 3 


## <a name="31-client-details"></a>3.1 szczegóły klienta 

### <a name="311-abstract-data-model"></a>3.1.1 abstrakcyjny Model danych 

#### <a name="3111-presentation-description"></a>3.1.1.1 Opis prezentacji 

>   Element danych opis prezentacji hermetyzuje wszystkie metadane prezentacji.
> 
>   Metadane prezentacja: Zestaw metadane, które są wspólne dla wszystkich strumieni w prezentacji. Metadane prezentacji obejmuje następujące pola, określone w sekcji *2.2.2.1*:
> 
> * **Brak elementu MajorVersion**
> * **MinorVersion**
> * **TimeScale**
> * **Czas trwania**
> * **IsLive**
> * **LookaheadCount**
> * **DVRWindowLength**
> 
>   Prezentacje zawierający zestaw — HEVC strumienie są:

    MajorVersion = 2
    MinorVersion = 2

>   LookaheadCount = 0 (Note: Pola przestarzałe)
> 
>   Prezentacje należy również ustawić:

    TimeScale = 90000

>   Kolekcja Stream: Zbiór elementów danych opis Stream, zgodnie z sekcją *3.1.1.1.2*.
> 
>   Opis ochrony: Kolekcja elementów opis metadanych System ochrony danych, zgodnie z sekcją *3.1.1.1.1*.

##### <a name="31111-protection-system-metadata-description"></a>3.1.1.1.1 Opis metadanych systemu ochrony 

>   Element danych opis metadanych systemu ochrony hermetyzuje metadanych specyficznych dla pojedynczego systemu ochrony zawartości. (Nie zmieniać)
> 
>   Opis nagłówka ochrony: Usługa Content protection metadanych, które odnoszą się do pojedynczego systemu ochrony zawartości. Opis elementu nagłówka ochrony obejmuje następujące pola, określone w sekcji *2.2.2.2*:
> 
>   * **SystemID**
>   * **ProtectionHeaderContent**

##### <a name="31112-stream-description"></a>3.1.1.1.2 Opis elementu Stream 

###### <a name="311121-track-description"></a>3.1.1.1.2.1 Śledź opis 

###### <a name="3111211-custom-attribute-description"></a>3.1.1.1.2.1.1 Opis atrybutu niestandardowego 

##### <a name="3113-fragment-reference-description"></a>3.1.1.3 Opis odwołania fragmentu 

###### <a name="31131-track-specific-fragment-reference-description"></a>3.1.1.3.1 Opis odwołania fragmentu specyficzne dla śledzenia 

#### <a name="3112-fragment-description"></a>3.1.1.2 Opis fragmentu 

##### <a name="31121-sample-description"></a>3.1.1.2.1 opis przykładu 

### <a name="312-timers"></a>3.1.2 czasomierze 

### <a name="313-initialization"></a>3.1.3 inicjowania 

### <a name="314-higher-layer-triggered-events"></a>3.1.4 wyższej warstwie wyzwalane zdarzenia 

#### <a name="3141-open-presentation"></a>3.1.4.1 Otwórz prezentacji 

#### <a name="3142-get-fragment"></a>3.1.4.2 pobrać fragmentu 

#### <a name="3143-close-presentation"></a>3.1.4.3 Zamknij prezentacji 

### <a name="315-processing-events-and-sequencing-rules"></a>3.1.5 przetwarzania zdarzeń i sekwencjonowania reguły 

#### <a name="3151-manifest-request-and-manifest-response"></a>3.1.5.1 manifestu żądania i odpowiedzi manifestu 

#### <a name="3152-fragment-request-and-fragment-response"></a>3.1.5.2 fragment żądania i odpowiedzi fragmentu

## <a name="32-server-details"></a>3.2 szczegóły serwera

## <a name="33-live-encoder-details"></a>3.3 koder na żywo szczegóły 

## <a name="4-protocol-examples"></a>4 przykłady protokołu 

## <a name="5-security"></a>5 zabezpieczeń 

## <a name="51-security-considerations-for-implementers"></a>5.1 uwagi dotyczące implementacji zabezpieczeń

>   Jeśli zawartość transportowane przy użyciu tego protokołu o wysokiej wartości handlowej, System ochrony zawartości powinien służyć do zapobiec nieautoryzowanemu użyciu zawartości. **ProtectionElement** może służyć do przenoszenia metadane powiązane z zastosowaniem systemu ochrony zawartości. Zawartość chronionego audio i wideo są szyfrowane zgodnie z określonym wspólnej wydanie drugie MPEG szyfrowania: 2015 [ISO/IEC 23001-7].
> 
>   **Uwaga**: Wideo — HEVC wycinek danych tylko VCL NALs są szyfrowane. Nagłówki wycinka i innych NALs są dostępne dla aplikacji prezentacji przed ich odszyfrowywania. w bezpiecznym Ścieżka wideo zaszyfrowanych informacji nie jest dostępne dla aplikacji prezentacji.

## <a name="52-index-of-security-parameters"></a>5.2 indeks parametrów zabezpieczeń 


| **Parametr zabezpieczeń**  | **Section**         |
|-------------------------|---------------------|
| ProtectionElement       | *2.2.2.2*           |
| Typowe pola szyfrowania | *[ISO/IEC 23001-7]* |

## <a name="53-common-encryption-boxes"></a>5.3 typowe pola szyfrowania

Następujące pola mogą występować w odpowiedzi fragmentu szyfrowania Common Encryption jest stosowana, gdy są określone w [ISO/IEC 23001-7] lub [ISO/IEC 14496 12]:

1.  Pole nagłówka określonych ochrony systemu (pssh)

2.  Przykładowe pole szyfrowania (senc)

3.  Przykładowe pomocnicze w ramach przesunięcia pole informacyjne (saio)

4.  Przykładowe pomocnicze informacje o rozmiarze pole (saiz)

5.  Przykładowe pole Opis grupy (sgpd)

6.  Przykładowe pole grupy (sbgp)

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
