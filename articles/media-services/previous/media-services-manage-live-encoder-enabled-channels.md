---
title: Transmisja strumieniowa przy użyciu usługi Azure Media Services do tworzenia strumieni o różnej szybkości transmisji na żywo | Dokumentacja firmy Microsoft
description: 'W tym temacie opisano sposób konfigurowania kanału, który odbiera strumień na żywo o pojedynczej szybkości transmisji bitów z kodera w środowisku lokalnym i wykonuje następnie kodowanie na żywo do strumienia o adaptacyjnej szybkości transmisji bitów przy użyciu usługi Media Services. Strumień może następnie dostarczane do klienta odtwarzanie aplikacji za pośrednictwem co najmniej jeden punkty końcowe przesyłania strumieniowego, przy użyciu jednej z następujących adaptacyjnych protokołów przesyłania strumieniowego: HLS, Smooth Stream MPEG DASH.'
services: media-services
documentationcenter: ''
author: anilmur
manager: femila
editor: ''
ms.assetid: 30ce6556-b0ff-46d8-a15d-5f10e4c360e2
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako;anilmur
ms.openlocfilehash: c168182f0b34329ed3e72e90ce86456dfbe210ca
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61217267"
---
# <a name="live-streaming-using-azure-media-services-to-create-multi-bitrate-streams"></a>Transmisja strumieniowa na żywo korzystająca z usługi Azure Media Services do tworzenia strumieni o różnej szybkości transmisji bitów

> [!NOTE]
> Od 12 maja 2018 r. Usługa kanały na żywo będzie już obsługę strumienia transportu RTP/MPEG-2 protokołu pozyskiwania. Przeprowadź migrację z protokołu RTP/MPEG-2 do protokołu RTMP lub plików MP4 (Smooth Streaming) protokołów pozyskiwania.

## <a name="overview"></a>Omówienie
W konsoli usługi Azure Media Services (AMS) **kanału** reprezentuje potok przetwarzania zawartości transmisji strumieniowej na żywo. A **kanału** odbiera strumieni danych wejściowych w jeden z dwóch sposobów:

* Lokalny koder na żywo wysyła strumień o pojedynczej szybkości bitów do kanału, który jest skonfigurowany do przeprowadzania kodowania na żywo z usługą Media Services w jednym z następujących formatów: RTMP lub Smooth Streaming (pofragmentowany MP4). Kanał wykonuje następnie kodowanie na żywo przychodzącego strumienia o pojedynczej szybkości transmisji bitów do postaci strumienia wideo o różnych szybkościach transmisji bitów (adaptacyjnej szybkości transmisji bitów). Po odebraniu żądania usługa Media Services dostarcza strumień do klientów.
* Lokalny koder na żywo wysyła różnych szybkościach transmisji bitów **RTMP** lub **Smooth Streaming** (pofragmentowany plik MP4) do kanału, który nie jest skonfigurowany do przeprowadzania kodowania na żywo przy użyciu usługi AMS. Pozyskiwane strumienie są przekazywane za pośrednictwem **kanału**bez dalszego przetwarzania. Ta metoda jest wywoływana **przekazywanego**. Można użyć następujących koderów na żywo, które zwracają Smooth Streaming o różnych szybkościach transmisji bitów: MediaExcel, Ateme, Imagine Communications, Envivio, Cisco i Elemental. Następujące kodery na żywo danych wyjściowych RTMP: Kodery Adobe Flash Media Live Encoder (FMLE), Telestream Wirecast, Haivision, Teradek i Tricaster.  Koder na żywo może także wysłać strumień o pojedynczej szybkości transmisji bitów do kanału, który nie obsługuje kodowania na żywo, nie jest to jednak zalecane. Po odebraniu żądania usługa Media Services dostarcza strumień do klientów.

  > [!NOTE]
  > Metoda przekazywania to najbardziej ekonomiczny sposób transmisja strumieniowa na żywo.
  > 
  > 

Począwszy od Media Services w wersji 2.10 wersji, podczas tworzenia kanału, można określić w jaki sposób chcesz kanału w taki sposób otrzymywać strumień wejściowy i określa, czy chcesz dla kanału do przeprowadzania kodowania na żywo strumienia. Dostępne są dwie opcje:

* **Brak** — określić tę wartość, jeśli zamierzasz używać na lokalny koder na żywo, która zwróci strumień o różnych szybkościach transmisji bitów (strumień przekazującego). W tym przypadku przychodzącego strumienia przekazywane do dane wyjściowe bez żadnych kodowania. Jest to zachowanie kanału przed wydaniem 2.10.  Aby uzyskać szczegółowe informacje na temat pracy z kanałami tego typu, zobacz [transmisja strumieniowa na żywo za pomocą koderów lokalnych, które tworzą strumienie różnej szybkości transmisji bitów](media-services-live-streaming-with-onprem-encoders.md).
* **Standardowa** — wybierz tę wartość, jeśli planujesz używać usługi Media Services do zakodowania transmisji strumieniowej na żywo o pojedynczej szybkości transmisji bitów do strumienia o wielokrotnej szybkości transmisji bitów. Należy pamiętać, że istnieje rozliczeń wpływ kodowanie na żywo i należy pamiętać, że pozostawienie kodowania kanału na żywo w stanie "Uruchomiona" spowoduje naliczenie opłat rozliczeń.  Zaleca się natychmiast zatrzymać kanałów uruchomione po zakończeniu zdarzenia przesyłania strumieniowego na żywo, aby uniknąć dodatkowych stawki godzinowe.

> [!NOTE]
> W tym temacie omówiono atrybuty z kanałami obsługującymi kodowanie na żywo (**standardowa** typ kodowania). Aby dowiedzieć się, jak praca z kanałami obsługującymi kodowanie na żywo nie mają włączonej, zobacz [transmisja strumieniowa na żywo za pomocą koderów lokalnych, które tworzą strumienie różnej szybkości transmisji bitów](media-services-live-streaming-with-onprem-encoders.md).
> 
> Upewnij się zapoznać się z [zagadnienia](media-services-manage-live-encoder-enabled-channels.md#Considerations) sekcji.
> 
> 

## <a name="billing-implications"></a>Opłaty wpływ
Rozpoczyna się kodowania kanału na żywo, rozliczeń, zaraz po zmianie stanu na "Uruchomione" jest za pośrednictwem interfejsu API.   Można również wyświetlić stan, w witrynie Azure portal lub za pomocą narzędzia Azure Media Services Explorer (https://aka.ms/amse).

W poniższej tabeli przedstawiono, jak Stany kanału mapują stanom rozliczeń w portalu usługi interfejsu API i platformy Azure. Stany różnią się nieco między komputerowy Portal i interfejsu API Jak najszybciej kanał jest w stanie "Uruchomiona", za pośrednictwem interfejsu API lub w stanie "Gotowy" lub "Przesyłanie strumieniowe" w witrynie Azure portal rozliczeń będzie aktywny.
Zatrzymanie kanału z rozliczeń można dalej, musisz zatrzymać kanał za pomocą interfejsu API lub w witrynie Azure portal.
Odpowiedzialność za zatrzymywanie kanałów, po wykonaniu kanale kodowania na żywo.  Nie można zatrzymać kodowania kanału spowoduje dalsze rozliczeń.

### <a id="states"></a>Stany kanału oraz sposobu mapowania ich na tryb rozliczeń
Bieżący stan kanału. Możliwe wartości obejmują:

* **Zatrzymano**. To jest wstępny stan kanału po jego utworzeniu (chyba że autostart został wybrany w portalu). Rozliczenia nie występuje w tym stanie. W tym stanie właściwości kanału mogą być aktualizowane, ale transmisja strumieniowa jest niedozwolona.
* **Uruchamianie**. Kanał jest uruchamiany. Rozliczenia nie występuje w tym stanie. W tym stanie nie są dozwolone ani aktualizacje, ani transmisja strumieniowa. Jeśli wystąpi błąd, kanał wróci do stanu Zatrzymany.
* **Uruchamianie**. Kanał może przetwarzać transmisje strumieniowe na żywo. Teraz jest to fakturowania użycia. Należy zatrzymać kanał, aby uniknąć dalszych rozliczeń. 
* **Zatrzymywanie**. Kanał jest zatrzymywany. Rozliczenia nie występuje w tym stanie przejściowym. W tym stanie nie są dozwolone ani aktualizacje, ani transmisja strumieniowa.
* **Usuwanie**. Kanał jest usuwany. Rozliczenia nie występuje w tym stanie przejściowym. W tym stanie nie są dozwolone ani aktualizacje, ani transmisja strumieniowa.

W tabeli poniżej pokazano, jak stany kanału przekładają się na naliczanie opłat. 

| Stan kanału | Wskaźniki w interfejsie użytkownika portalu | Jest to rozliczeń? |
| --- | --- | --- |
| Uruchamianie |Uruchamianie |Nie (stan przejściowy) |
| Działanie |Gotowy (brak uruchomionych programów)<br/>lub<br/>Transmisja strumieniowa (co najmniej jeden uruchomiony program) |TAK |
| Zatrzymywanie |Zatrzymywanie |Nie (stan przejściowy) |
| Zatrzymano |Zatrzymano |Nie |

### <a name="automatic-shut-off-for-unused-channels"></a>Automatycznego wyłączania nieużywanych kanałów
Począwszy od 25 stycznia 2016 r. usługa Media Services wprowadzane aktualizację, która automatycznie zatrzymuje kanału (z funkcją live encoding włączone) po działa ona w stanie nieużywany przez długi czas. Dotyczy to kanałów, które mają nie aktywne programy i którzy nie otrzymali wkład wejściowego źródła danych przez dłuższy czas.

Próg dla nieużywanych przedziale jest nominalnie 12 godzin, ale może ulec zmianie.

## <a name="live-encoding-workflow"></a>Przepływ pracy kodowania na żywo
Poniższy diagram przedstawia na żywo przepływ pracy transmisji strumieniowej, gdzie kanał odbiera strumień o pojedynczej szybkości transmisji bitów w jednym z następujących protokołów: RTMP lub Smooth Streaming; następnie koduje jako strumień na strumień o wielokrotnej szybkości transmisji bitów. 

![Przepływ pracy na żywo][live-overview]

## <a id="scenario"></a>Typowy scenariusz transmisji strumieniowej na żywo
Poniżej przedstawiono ogólne etapy tworzenia typowych aplikacji transmisji strumieniowej na żywo.

> [!NOTE]
> Obecnie maksymalny zalecany czas trwania wydarzenia na żywo wynosi 8 godzin. Napisz na adres amslived@microsoft.com, jeśli potrzebujesz uruchomić kanał na dłuższy czas. Ma to wpływu rozliczeń kodowanie na żywo i należy pamiętać, że pozostawienie kodowania kanału na żywo w stanie "Uruchomiona" zostaną naliczone stawki godzinowe rozliczeń.  Zaleca się natychmiast zatrzymać kanałów uruchomione po zakończeniu zdarzenia przesyłania strumieniowego na żywo, aby uniknąć dodatkowych stawki godzinowe. 

1. Podłącz kamerę wideo do komputera. Uruchom i skonfiguruj lokalny koder na żywo, którego dane wyjściowe **pojedynczego** strumienia szybkości transmisji bitów w jednym z następujących protokołów: RTMP lub Smooth Streaming. 

    Ten krok można również wykonać po utworzeniu kanału.
2. Utwórz i uruchom kanał. 
3. Pobierz adres URL pozyskiwania kanału. 

    Koder na żywo używa adresu URL pozyskiwania do wysyłania strumienia do kanału.
4. Pobierz adres URL podglądu kanału. 

    Użyj tego adresu URL, aby sprawdzić, czy kanał prawidłowo odbiera strumień na żywo.
5. Utwórz program. 

    Korzystając z witryny Azure portal, tworzenie programu tworzy również element zawartości. 

    Podczas korzystania z zestawu SDK platformy .NET lub REST, należy utworzyć element zawartości i określić, czy używać tego zasobu, podczas tworzenia programu. 
6. Opublikuj zasób skojarzony z danym programem.   

    >[!NOTE]
    >Po utworzeniu konta usługi AMS zostanie do niego dodany **domyślny** punkt końcowy przesyłania strumieniowego mający stan **Zatrzymany**. Punkt końcowy przesyłania strumieniowego, z którego chcesz strumieniowo przesyłać zawartość, musi mieć stan **Uruchomiony**. 

7. Uruchom program, gdy wszystko będzie gotowe do rozpoczęcia przesyłania strumieniowego i archiwizacji.
8. Opcjonalnie można przesłać do kodera na żywo sygnał o rozpoczęciu reklamy. Reklama jest wstawiana do strumienia wyjściowego.
9. Zatrzymaj program w dowolnym momencie, w którym chcesz zatrzymać przesyłanie strumieniowe i archiwizowanie wydarzenia.
10. Usuń program (opcjonalnie można również usunąć zasób).   

> [!NOTE]
> Jest to bardzo ważne nie należy zapominać zatrzymać kanału kodowanie na żywo. Należy pamiętać, że ma wpływu kodowanie na żywo rozliczeń co godzinę i należy pamiętać, że pozostawienie kodowania kanału na żywo w stanie "Uruchomiona" spowoduje naliczenie opłat rozliczeń.  Zaleca się natychmiast zatrzymać kanałów uruchomione po zakończeniu zdarzenia przesyłania strumieniowego na żywo, aby uniknąć dodatkowych stawki godzinowe. 
> 
> 

## <a id="channel"></a>Kanał dane wejściowe podane przez (odbieranie) konfiguracji
### <a id="Ingest_Protocols"></a>Pozyskiwanie protokołu przesyłania strumieniowego
Jeśli **typ kodera** ustawiono **standardowa**, prawidłowe opcje to:

* Pojedyncza szybkość transmisji bitów **RTMP**
* Pojedyncza szybkość transmisji bitów **pofragmentowany plik MP4** (Smooth Streaming)

#### <a id="single_bitrate_RTMP"></a>Pojedyncza szybkość transmisji bitów RTMP
Zagadnienia do rozważenia:

* Przychodzący strumień nie może zawierać wideo o różnych szybkościach transmisji bitów
* Strumienia wideo powinien mieć średnia szybkość transmisji bitów poniżej 15 MB/s
* Strumień audio powinny mieć średnia szybkość transmisji bitów poniżej 1 MB/s
* Obsługiwane kodery-dekodery są następujące:
* AVC MPEG-4 / wideo H.264
* Linii bazowej, główny, wysoka profilu (8-bitowy 4:2:0)
* Wysoka profilu 10 (10-bitowy 4:2:0)
* Wysoka profil 422 (10-bitowy 4:2:2)
* Audio AAC-LC MPEG-2
* Narzędzie mono, Stereo, Otocz (5.1, 7.1)
* częstotliwość próbkowania 44,1 kHz
* Pakowanie ADTS styl MPEG-2
* Zalecane kodery obejmują:
* Telestream Wirecast
* Flash Media Live Encoder

#### <a name="single-bitrate-fragmented-mp4-smooth-streaming"></a>Pojedyncza szybkość transmisji bitów podzielonej zawartości w formacie MP4 (Smooth Streaming)
Typowy przypadek użycia:

Lokalne korzystanie koderów na żywo od dostawców, takich jak Elemental technologii Ericsson, Ateme, Envivio do wysyłania strumienia wejściowego za pośrednictwem otwartego Internetu w pobliżu Azure centrum danych.

Zagadnienia do rozważenia:

Takie same jak dla [pojedyncza szybkość transmisji bitów RTMP](media-services-manage-live-encoder-enabled-channels.md#single_bitrate_RTMP).

#### <a name="other-considerations"></a>Inne zagadnienia
* Nie można zmienić protokołu wejściowego, gdy kanał lub skojarzone z nim programy są uruchomione. Jeśli potrzebujesz różnych protokołów, utwórz osobny kanał dla każdego protokołu wejściowego.
* Maksymalna rozdzielczość dla przychodzących strumienia wideo wynosi 1920 x 1080 pikseli i co najwyżej 60 pola na sekundę, jeśli z przeplotem lub 30 klatek na sekundę w przypadku progresywnego.

### <a name="ingest-urls-endpoints"></a>Adresy URL (punkty końcowe) pozyskiwania
Kanał oferuje wejściowy punkt końcowy (adres URL pozyskiwania) określisz w koder na żywo, dzięki czemu można wypchnąć kodera strumienie do kanałów.

Po utworzeniu kanału możesz uzyskać adresy URL pozyskiwania. Aby uzyskać te adresy URL, kanał nie muszą znajdować się w **systemem** stanu. Gdy wszystko jest gotowe rozpocząć wypychanie danych do kanału, musi być w **systemem** stanu. Po uruchomieniu kanału dane wprowadzane można wyświetlać podgląd strumienia za pomocą adresu URL w wersji zapoznawczej.

Istnieje możliwość wprowadzania pofragmentowany plik MP4 (Smooth Streaming) transmisji strumieniowej na żywo za pośrednictwem połączenia SSL. Pozyskanie za pośrednictwem protokołu SSL, upewnij się zaktualizować adres URL pozyskiwania protokołu HTTPS. Obecnie usługa AMS nie obsługuje protokołu SSL z zastosowaniem domen niestandardowych.  

### <a name="allowed-ip-addresses"></a>Dozwolone adresy IP
Można zdefiniować adresy IP, które mogą publikować pliki wideo do tego kanału. Dozwolone IP adresów może być określony jako albo pojedynczy adres IP adresów (na przykład "10.0.0.1"), zakresu adresów IP przy użyciu adresu IP i maski podsieci CIDR (na przykład "10.0.0.1/22") lub adres IP zakresu (na przykład przy użyciu adresu IP i maski podsieci dziesiętna kropkowana , "10.0.0.1(255.255.252.0)').

Jeśli adresy IP nie zostaną określone i brakuje definicji reguły, to żaden adres IP nie będzie dozwolony. Aby zezwolić na jakikolwiek adres IP, utwórz regułę i ustaw wartość 0.0.0.0/0.

## <a name="channel-preview"></a>Podgląd kanału
### <a name="preview-urls"></a>Adresy URL podglądu
Kanały zapewniają punktu końcowego (wersja zapoznawcza) (adres URL w wersji zapoznawczej), która umożliwia podgląd i weryfikować strumień przed dalszym przetwarzaniem i dostarczaniem.

Po utworzeniu kanału możesz uzyskać adres URL podglądu. Aby uzyskać adres URL, kanał nie muszą znajdować się w **systemem** stanu.

Po uruchomieniu kanału dane wprowadzane można wyświetlać podgląd strumienia.

> [!NOTE]
> Obecnie Podgląd strumienia tylko mogą być dostarczane w pofragmentowany plik MP4 (Smooth Streaming) format niezależnie od określonego typu danych wejściowych.  Odtwarzacz hostowany w witrynie Azure portal służy do wyświetlania strumienia.
> 
> 

### <a name="allowed-ip-addresses"></a>Dozwolone adresy IP
Można zdefiniować adresy IP, które mogą nawiązywać połączenie z punktem końcowym (wersja zapoznawcza). Jeśli nie adresy IP są określone, może być dowolny adres IP. Dozwolone IP adresów może być określony jako albo pojedynczy adres IP adresów (na przykład "10.0.0.1"), zakresu adresów IP przy użyciu adresu IP i maski podsieci CIDR (na przykład "10.0.0.1/22") lub adres IP zakresu (na przykład przy użyciu adresu IP i maski podsieci dziesiętna kropkowana , "10.0.0.1(255.255.252.0)').

## <a name="live-encoding-settings"></a>Ustawienia kodowania na żywo
W tej sekcji opisano, jak ustawień kodera na żywo w kanale można dostosować, gdy **typ kodowania** kanału jest równa **standardowa**.

> [!NOTE]
> Twoje źródło danych udział może zawierać tylko pojedynczą ścieżkę audio — wprowadzane z wielu ścieżki audio nie jest obecnie obsługiwane. Podczas wykonywania kodowania na żywo za pomocą [na żywo w środowisku lokalnym koduje](media-services-live-streaming-with-onprem-encoders.md), możesz wysłać wkład kanału informacyjnego w protokole Smooth Streaming, zawierające wiele ścieżki audio.
> 
> 

### <a name="ad-marker-source"></a>Źródło znacznika usługi AD
Można określić źródło sygnałów znaczników reklamowych. Wartość domyślna to **Api**, co oznacza, że koder na żywo w kanale powinien nasłuchiwać asynchronicznego **interfejsu API znacznika reklamy**.

### <a name="cea-708-closed-captions"></a>Napisy kodowane CEA 708 podpisy kodowane
Opcjonalna Flaga, która informuje o tym koder na żywo, aby ignorować wszystkie dane Podpisy napisy kodowane CEA-708 osadzony w przychodzących wideo. Gdy flaga jest ustawiona na wartość false (domyślnie), kodera wykryje i ponownie włóż napisy kodowane CEA-708 danych do strumieni wideo w danych wyjściowych.

#### <a name="index"></a>Indeks
Zalecane jest, aby wysłać w jednym programie strumień transportu (SPTS). Jeśli strumień wejściowy zawiera wiele programów, analizuje tabeli mapy Program (rata) w danych wejściowych kodera na żywo w kanale, który identyfikuje dane wejściowe, które mają nazwę typu stream ADTS AAC MPEG-2 lub AC-3, System A lub AC 3 systemu-B lub prywatnej PES MPEG-2 lub MPEG-1 Audio lub dźwięk w postaci strumieni MPEG-2 i układa je w kolejności określonej w konto Liczony od zera indeks jest następnie używany tak, aby wczytać n tego wpisu w takie rozwiązanie.

#### <a name="language"></a>Język
Identyfikator języka strumienia audio, zgodne z normą ISO 639-2, takie jak ENG. Jeśli nie istnieje, wartość domyślna to i (niezdefiniowany).

### <a id="preset"></a>Ustawienie wstępne systemu
Określa ustawienie wstępne, który będzie używany przez koder na żywo w ramach tego kanału. Obecnie jest jedyną dozwoloną wartość **Default720p** (ustawienie domyślne).

Należy pamiętać, że jeśli potrzebujesz niestandardowych ustawień wstępnych, należy skontaktować się ze amslived@microsoft.com.

**Default720p** będzie kodowania wideo na następujące warstwy 6.

#### <a name="output-video-stream"></a>Dane wyjściowe Stream wideo

| Szybkość transmisji bitów | Szerokość | Wysokość | MaxFPS | Profil | Nazwa Stream wyjściowego |
| --- | --- | --- | --- | --- | --- |
| 3500 |1280 |720 |30 |Wysoka |Video_1280x720_3500kbps |
| 2200 |960 |540 |30 |Wysoka |Video_960x540_2200kbps |
| 1350 |704 |396 |30 |Wysoka |Video_704x396_1350kbps |
| 850 |512 |288 |30 |Wysoka |Video_512x288_850kbps |
| 550 |384 |216 |30 |Wysoka |Video_384x216_550kbps |
| 200 |340 |192 |30 |Wysoka |Video_340x192_200kbps |

#### <a name="output-audio-stream"></a>Dane wyjściowe Audio Stream

Dźwięk jest zakodowany w celu stereo AAC-LC na 128 Kb/s, częstotliwość próbkowania kHz 48.

## <a name="signaling-advertisements"></a>Sygnalizowanie anonsów
Kanał ma włączone kodowanie na żywo, ma składnika w potoku, który jest przetwarzanie filmu wideo, a można manipulować go. Można wysłać sygnału dla kanału wstawiania plansz i/lub reklam do strumienia o adaptacyjnej szybkości transmisji bitów wychodzących. Plansz nadal są obrazy, które można użyć w celu ukrycia się wejściowy Kanał informacyjny na żywo w niektórych przypadkach (na przykład podczas reklamowej). Anonsowanie sygnały, są sygnały zsynchronizowany czas, który osadzania do strumienia wychodzącego mówić odtwarzacz wideo, aby podejmować żadnych specjalnych czynności — tak, aby przełączyć się do anonsu w odpowiednim czasie. Zobacz ten [blogu](https://codesequoia.wordpress.com/2014/02/24/understanding-scte-35/) omówienie mechanizm sygnalizowanie SCTE 35 używanym do tego celu. Poniżej przedstawiono typowy scenariusz, którą można implementować w zdarzenia na żywo.

1. Mieć przeglądającym pobrać obraz zdarzenie wstępne, przed uruchomieniem zdarzenia.
2. Mieć przeglądającym pobrać obraz po zdarzeń po zakończeniu zdarzenia.
3. Mieć przeglądającym uzyskać obraz zdarzenie błędu, jeśli występuje problem podczas zdarzenia (na przykład awarii zasilania w stadion).
4. Wyślij obraz AD-BREAK, aby ukryć zdarzenia na żywo, źródła danych podczas reklamowej.

Poniżej przedstawiono właściwości, które można ustawić podczas sygnalizowanie anonsów. 

### <a name="duration"></a>Czas trwania
Czas w sekundach, komercyjnych podziału. Musi to być dodatnia wartość różna od zera, aby uruchomić reklamowej. Po reklamowej jest w toku, a czas trwania jest ustawiona na zero z CueId dopasowywania w toku reklamowej, a następnie tego podziału zostanie anulowane.

### <a name="cueid"></a>CueId
Unikatowy identyfikator dla reklamowej, do użycia przez aplikację podrzędnego do podjęcia odpowiednich akcji. Musi być dodatnią liczbą całkowitą. Można ustawić tej wartości losowych dodatnią liczbą całkowitą lub użyć nadrzędnego system do śledzenia identyfikatory wskaźnika. Pamiętaj znormalizować wszystkich identyfikatorów do dodatnie liczby całkowite przed przesłaniem za pośrednictwem interfejsu API.

### <a name="show-slate"></a>Pokaż plansz
Opcjonalny. Sygnalizuje koder na żywo, aby przełączyć się do [domyślne plansz](media-services-manage-live-encoder-enabled-channels.md#default_slate) obrazu podczas reklamowej i ukrywanie przychodzących źródła wideo. Dźwięk jest również wyciszone podczas plansz. Wartość domyślna to **false**. 

Obraz używany będzie określony za pomocą elementu zawartości planszy domyślne Właściwość Id w czasie tworzenia kanału. Plansz będzie być rozciągnięty do rozmiaru ekranu rozmiar obrazu. 

## <a name="insert-slate--images"></a>Wstawianie obrazów Plansz
Koder na żywo w kanale mogą być sygnalizowane, aby przełączyć się do obrazu planszy. Go może również być sygnalizowane, aby zakończyć plansz w toku. 

Koder na żywo można skonfigurować tak, aby przejść do obrazu planszy i Ukryj przychodzących sygnał wideo, w niektórych sytuacjach — na przykład podczas przerwy ad. Jeśli nie skonfigurowano takie tablecie, wejściowy plik wideo nie jest maskowane podczas tego podziału usługi ad.

### <a name="duration"></a>Czas trwania
Czas trwania tablecie, w ciągu kilku sekund. Musi to być różna od zera dodatnia, aby uruchomić plansz. Jeśli istnieje plansz w toku, a czas trwania zero jest określony, że plansz w toku zostanie zakończona.

### <a name="insert-slate-on-ad-marker"></a>Wstaw planszę w znacznika reklamy miejscu
Gdy wartość true, to ustawienie służy do konfigurowania koder na żywo, aby wstawić obrazu planszy podczas przerwy ad. Wartość domyślna to true. 

### <a id="default_slate"></a>Identyfikator elementu zawartości planszy domyślne

Opcjonalny. Określa identyfikator zasobu elementu zawartości usługi multimediów, który zawiera obraz planszy. Domyślny ma wartość null. 


> [!NOTE] 
> Przed utworzeniem kanału, obrazu planszy z następującymi ograniczeniami powinny zostać przekazane jako dedykowane trwały (żadne inne pliki nie powinny należeć do tego zasobu). Ten obraz jest używany tylko wtedy, gdy koder na żywo jest wstawiania plansz z powodu przerwania ad lub została jawnie zasygnalizują, aby wstawić tablecie. Obecnie nie ma opcji do użycia niestandardowego obrazu, podczas koder na żywo wejdzie w taki stan usługi "utraty sygnału wejściowego". Dla tej funkcji, możesz głosować [tutaj](https://feedback.azure.com/forums/169396-azure-media-services/suggestions/10190457-define-custom-slate-image-on-a-live-encoder-channel).


* Co najwyżej 1920 x 1080 pikseli w rozdzielczości.
* Maksymalnie 3 (MB) rozmiar.
* Nazwa pliku musi mieć rozszerzenie *.jpg.
* Obraz, który należy przekazać do elementu zawartości w formie tylko AssetFile, zasobów i AssetFile ten powinien być oznaczony jako plik podstawowy. Zasób nie może być szyfrowany w magazynie.

Jeśli **domyślne łupków identyfikator elementu zawartości** nie zostanie określony, i **Wstaw planszę w znacznika reklamy miejscu** jest ustawiona na **true**, domyślnego obrazu usługi Azure Media Services będzie służyć do ukrywania wejściowego filmu wideo strumień. Dźwięk jest również wyciszone podczas plansz. 

## <a name="channels-programs"></a>Programy kanału
Kanał jest skojarzony z programami, które umożliwiają kontrolowanie publikowania i przechowywania segmentów strumienia na żywo. Kanały zarządzają programami. Relacja kanału i programu jest bardzo podobne do tradycyjnych multimediach, gdzie kanał ma stały strumień zawartości, a program obejmuje niektóre zdarzenia czasowe na tym kanale.

Można określić liczbę godzin, aby zachować zarejestrowaną zawartość na potrzeby programu przez ustawienie długości **Okna archiwum**. Ta wartość musi mieścić się w zakresie od 5 minut do maksymalnie 25 godzin. Długość okna archiwizacji również mówią, że maksymalna liczba klientów można wyszukać w czasie od bieżącej pozycji na żywo. Programy mogą być transmitowane w określonym czasie, ale zawartość, która wykracza poza długość okna, jest stale odrzucana. Wartość tej właściwości określa również, jak długie mogą być manifesty na kliencie.

Każdy program jest skojarzony z elementem zawartości, która przechowuje zawartości przesyłanej strumieniowo. Element zawartości jest mapowany do bloku kontenera obiektów blob na koncie usługi Azure Storage, a pliki w elemencie zawartości są przechowywane jako obiekty BLOB w kontenerze. Aby opublikować program, dzięki czemu klienci mogą wyświetlać strumień, należy utworzyć Lokalizator OnDemand dla skojarzonego elementu zawartości. Lokalizator umożliwia utworzenie adresu URL przesyłania strumieniowego, który można udostępnić klientom.

Kanał obsługuje maksymalnie trzy jednocześnie uruchomione programy, dzięki czemu można tworzyć wiele archiwów tego samego strumienia przychodzącego. Umożliwia to w razie potrzeby publikowanie i archiwizację różnych części wydarzenia. Na przykład zgodnie z wymaganiami biznesowymi potrzebna jest archiwizacja sześciu godzin programu, ale do emisji przeznaczonych jest tylko ostatnich 10 minut. W tym celu należy utworzyć dwa jednocześnie uruchomione programy. Jeden z programów jest skonfigurowany na potrzeby archiwizacji sześciu godzin zdarzenia, ale ten program nie jest publikowany. Drugi z programów jest skonfigurowany w celu archiwizacji 10 minut wydarzenia i ten program zostanie opublikowany.

Nie należy używać ponownie istniejących programów dla nowych zdarzeń. Zamiast tego należy utworzyć i uruchomić nowy program dla każdego zdarzenia, zgodnie z opisem w sekcji programowania aplikacji przesyłania strumieniowego na żywo.

Uruchom program, gdy wszystko będzie gotowe do rozpoczęcia przesyłania strumieniowego i archiwizacji. Zatrzymaj program w dowolnym momencie, w którym chcesz zatrzymać przesyłanie strumieniowe i archiwizowanie wydarzenia. 

Aby usunąć zarchiwizowaną zawartość, zatrzymaj i usuń program, a następnie usuń skojarzony element zawartości. Nie można usunąć elementu zawartości, jeśli jest on używany przez program. Najpierw należy usunąć program. 

Nawet po zatrzymaniu i usunięciu programu użytkownicy będą mogli przesyłać strumieniowo zarchiwizowaną zawartość wideo na żądanie tak długo, jak zasoby nie zostaną usunięte.

Jeśli chcesz zachować zarchiwizowaną zawartość, ale bez udostępniania jej do przesyłania strumieniowego, usuń lokalizator przesyłania strumieniowego.

## <a name="getting-a-thumbnail-preview-of-a-live-feed"></a>Pobieranie miniatury podglądu na żywo źródła danych
Gdy kodowanie na żywo jest włączona, możesz teraz uzyskać wersję zapoznawczą Kanał informacyjny na żywo jako osiągnie kanału. Może to być przydatne narzędzie, aby sprawdzić, czy Twoje Kanał informacyjny na żywo faktycznie zbliża się do kanału. 

## <a id="states"></a>Stany kanału i sposobu mapowania stanów przekładają się naliczanie opłat
Bieżący stan kanału. Możliwe wartości obejmują:

* **Zatrzymano**. To jest wstępny stan kanału po jego utworzeniu. W tym stanie właściwości kanału mogą być aktualizowane, ale transmisja strumieniowa jest niedozwolona.
* **Uruchamianie**. Kanał jest uruchamiany. W tym stanie nie są dozwolone ani aktualizacje, ani transmisja strumieniowa. Jeśli wystąpi błąd, kanał wróci do stanu Zatrzymany.
* **Uruchamianie**. Kanał może przetwarzać transmisje strumieniowe na żywo.
* **Zatrzymywanie**. Kanał jest zatrzymywany. W tym stanie nie są dozwolone ani aktualizacje, ani transmisja strumieniowa.
* **Usuwanie**. Kanał jest usuwany. W tym stanie nie są dozwolone ani aktualizacje, ani transmisja strumieniowa.

W tabeli poniżej pokazano, jak stany kanału przekładają się na naliczanie opłat. 

| Stan kanału | Wskaźniki w interfejsie użytkownika portalu | Naliczanie opłat? |
| --- | --- | --- |
| Uruchamianie |Uruchamianie |Nie (stan przejściowy) |
| Działanie |Gotowy (brak uruchomionych programów)<br/>lub<br/>Transmisja strumieniowa (co najmniej jeden uruchomiony program) |Yes |
| Zatrzymywanie |Zatrzymywanie |Nie (stan przejściowy) |
| Zatrzymano |Zatrzymano |Nie |

> [!NOTE]
> Obecnie średnia start kanału to około 2 minuty, ale czasami może potrwać do ponad 20 minut. Resetowanie kanału może potrwać do 5 minut.
> 
> 

## <a id="Considerations"></a>Zagadnienia dotyczące
* Gdy kanał **standardowa** typ kodowania napotyka utraty źródła danych źródła danych wejściowych/wkładu, kompensuje ona go, zastępując plansz błąd i wyciszenia źródła audio/wideo. Kanał będzie emitować tablecie, dopóki dane wejściowe/wkład kanału informacyjnego wznawia. Zaleca się, że kanału na żywo nie może pozostać w stanie przez czas dłuższy niż 2 godziny. Poza ten punkt zachowanie kanału po ponownym nawiązaniu połączenia w danych wejściowych nie ma żadnej gwarancji, żaden nie jest jego zachowanie w odpowiedzi na polecenie resetowania. Trzeba będzie zatrzymać kanał, usuń ją i Utwórz nową.
* Nie można zmienić protokołu wejściowego, gdy kanał lub skojarzone z nim programy są uruchomione. Jeśli potrzebujesz różnych protokołów, utwórz osobny kanał dla każdego protokołu wejściowego.
* Za każdym razem, gdy skonfigurujesz koder na żywo, wywołaj **resetowania** metody w kanale. Przed zresetowaniem kanał, musisz zatrzymać program. Po zresetowaniu kanał, ponownie uruchom program.
* Kanał można zatrzymać tylko wtedy, gdy jest ona w stanie uruchomiona, a wszystkie programy na kanale zostały zatrzymane.
* Domyślnie 5 kanałów można dodać tylko do konta usługi Media Services. Jest to przydziału elastycznego na wszystkich nowych kont. Aby uzyskać więcej informacji, zobacz [przydziały i ograniczenia](media-services-quotas-and-limitations.md).
* Nie można zmienić protokołu wejściowego, gdy kanał lub skojarzone z nim programy są uruchomione. Jeśli potrzebujesz różnych protokołów, utwórz osobny kanał dla każdego protokołu wejściowego.
* Rozliczenie jest przeprowadzane tylko wtedy, gdy kanał jest w **systemem** stanu. Aby uzyskać więcej informacji, zobacz [to](media-services-manage-live-encoder-enabled-channels.md#states) sekcji.
* Obecnie maksymalny zalecany czas trwania wydarzenia na żywo wynosi 8 godzin. Napisz na adres amslived@microsoft.com, jeśli potrzebujesz uruchomić kanał na dłuższy czas.
* Upewnij się, że istnieje punkt końcowy przesyłania strumieniowego, z którego chcesz strumieniowo przesyłać zawartość w **systemem** stanu.
* Ustawienia wstępne kodowania używa pojęcie "maksymalną szybkość" 30 kl. / s. Jeśli dane wejściowe są 60 kl. / s / 59.94i, ramki danych wejściowych porzuconych/cofania-interlaced do 30/29,97 kl. / s. Jeśli dane wejściowe są 50 kl. / s/50i, ramki wejściowe są porzucony/cofania-interlaced do 25 kl. / s. Jeśli dane wejściowe są 25 kl. / s, dane wyjściowe pozostaje na poziomie 25 kl. / s.
* Nie zapomnij o zatrzymanie YOUR kanały po zakończeniu. Jeśli tego nie zrobisz, będą nadal rozliczeń.

## <a name="known-issues"></a>Znane problemy
* Czas uruchamiania kanał został ulepszony, aby średnio 2 minuty, ale czasami z rosnący popyt trwało do ponad 20 minut.
* Obrazy planszy powinna być zgodna ograniczenia [tutaj](media-services-manage-live-encoder-enabled-channels.md#default_slate). Jeśli użytkownik podejmie próbę utworzenia kanału z plansz domyślny, który jest większy niż 1920 x 1080 pikseli, żądanie po pewnym czasie zgłosi błąd.
* Jeszcze raz... nie zapomnij STOP YOUR kanały, po zakończeniu przesyłania strumieniowego. Jeśli tego nie zrobisz, będą nadal rozliczeń.

## <a name="next-step"></a>Następny krok
Przejrzyj ścieżki szkoleniowe dotyczące usługi Media Services.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-topics"></a>Tematy pokrewne
[Dostarczanie wydarzeń transmisji strumieniowej na żywo, za pomocą usługi Azure Media Services](media-services-overview.md)

[Tworzenie kanałów wykonujących kodowanie na żywo z wystąpieniu szybkości transmisji bitów do strumienia o adaptacyjnej szybkości transmisji bitów przy użyciu portalu](media-services-portal-creating-live-encoder-enabled-channel.md)

[Tworzenie kanałów wykonujących kodowanie na żywo z wystąpieniu szybkości transmisji bitów do strumienia o adaptacyjnej szybkości transmisji bitów przy użyciu zestawu .NET SDK](media-services-dotnet-creating-live-encoder-enabled-channel.md)

[Zarządzanie kanałami przy użyciu interfejsu API REST](https://docs.microsoft.com/rest/api/media/operations/channel)

[Pojęcia dotyczące usługi Media Services](media-services-concepts.md)

[Specyfikacja odbierania podzielonej zawartości w formacie MP4 na żywo w usłudze Azure Media Services](media-services-fmp4-live-ingest-overview.md)

[live-overview]: ./media/media-services-manage-live-encoder-enabled-channels/media-services-live-streaming-new.png

