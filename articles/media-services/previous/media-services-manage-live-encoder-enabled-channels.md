---
title: Transmisja strumieniowa przy użyciu usługi Azure Media Services do tworzenia strumieni o wielokrotnej szybkości transmisji bitów na żywo | Dokumentacja firmy Microsoft
description: 'W tym temacie opisano, jak skonfigurować kanału, który odbiera strumień na żywo o pojedynczej szybkości transmisji bitów z kodera lokalnego i wykonuje następnie kodowanie na żywo do strumienia o adaptacyjnej szybkości transmisji bitów z usługi Media Services. Strumień następnie zostanie dostarczona do klienta odtwarzanie aplikacji za pośrednictwem jednego lub więcej punkty końcowe przesyłania strumieniowego, przy użyciu jednej z następujących protokołów adaptacyjną: HLS, Smooth Stream, MPEG DASH.'
services: media-services
documentationcenter: ''
author: anilmur
manager: cfowler
editor: ''
ms.assetid: 30ce6556-b0ff-46d8-a15d-5f10e4c360e2
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2018
ms.author: juliako;anilmur
ms.openlocfilehash: 5aa6f629b04a4c187a43b13c929a122a6304c575
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34639439"
---
# <a name="live-streaming-using-azure-media-services-to-create-multi-bitrate-streams"></a>Transmisja strumieniowa na żywo korzystająca z usługi Azure Media Services do tworzenia strumieni o różnej szybkości transmisji bitów

> [!NOTE]
> Uruchamianie kanałów na żywo 12 maja 2018 będzie już obsługi strumienia transportowego RTP/MPEG-2 protokołu pozyskiwania. Przeprowadź migrację z RTP/MPEG-2 RTMP lub pofragmentowany MP4 (Smooth Streaming) pozyskiwania protokołów.

## <a name="overview"></a>Przegląd
W konsoli usługi Azure Media Services (AMS) **kanału** reprezentuje potok przetwarzania zawartości transmisji strumieniowej na żywo. A **kanału** odbiera na żywo wejściowych strumieni w jeden z dwóch sposobów:

* Na lokalny koder na żywo wysyła strumień o pojedynczej szybkości transmisji bitów do kanału, który jest skonfigurowany do przeprowadzania kodowania na żywo w usłudze Media Services w jednym z następujących formatów: RTMP lub Smooth Streaming (pofragmentowany MP4). Kanał wykonuje następnie kodowanie na żywo przychodzącego strumienia o pojedynczej szybkości transmisji bitów do postaci strumienia wideo o różnych szybkościach transmisji bitów (adaptacyjnej szybkości transmisji bitów). Po odebraniu żądania usługa Media Services dostarcza strumień do klientów.
* Na lokalny koder na żywo wysyła różnych szybkościach transmisji bitów **RTMP** lub **Smooth Streaming** (pofragmentowany MP4) do kanału, który nie jest włączony do przeprowadzania kodowania na żywo przy użyciu usługi AMS. Pozyskiwane strumienie są przekazywane za pośrednictwem **kanału**s bez dalszego przetwarzania. Ta metoda jest wywoływana **przekazywanego**. Można użyć następujących koderów na żywo, które udostępniają Smooth Streaming wielokrotnej szybkości transmisji bitów: MediaExcel, Ateme, Wyobraź sobie komunikacji, Envivio, Cisco i Elemental. Następujące kodery na żywo wysyłają pliki RTMP: Adobe Flash Media na żywo kodera (FMLE), Telestream Wirecast, Haivision, Teradek i Tricaster koderów.  Koder na żywo może także wysłać strumień o pojedynczej szybkości transmisji bitów do kanału, który nie obsługuje kodowania na żywo, nie jest to jednak zalecane. Po odebraniu żądania usługa Media Services dostarcza strumień do klientów.
  
  > [!NOTE]
  > Metoda przekazywania to najbardziej ekonomiczne rozwiązanie wygaśnięcia przesyłania strumieniowego.
  > 
  > 

Począwszy od Media Services 2.10 wersji, podczas tworzenia kanału, można określić w jaki sposób chcesz dla kanału do odbierania strumienia wejściowego oraz czy ma dla kanału do przeprowadzania kodowania na żywo strumienia. Dostępne są dwie opcje:

* **Brak** — podać tę wartość, jeśli planujesz używać na lokalny koder na żywo, którego dane wyjściowe obejmują wielokrotnej szybkości transmisji bitów stream (strumień przekazującego). W takim przypadku strumienia przychodzącego przekazywane do danych wyjściowych bez żadnych kodowania. Jest to zachowanie kanału przed 2.10 wersji.  Aby uzyskać szczegółowe informacje na temat pracy z kanałami tego typu, zobacz [transmisję strumieniową na żywo za pomocą koderów lokalnych, które tworzą strumienie o różnych szybkościach transmisji bitów](media-services-live-streaming-with-onprem-encoders.md).
* **Standardowe** — wybierz tę wartość, jeśli planujesz używać usługi Media Services do kodowania transmisji strumieniowej na żywo pojedynczej szybkości transmisji bitów do strumienia o wielokrotnej szybkości transmisji bitów. Należy pamiętać, że istnieje rozliczeń wpływ kodowanie na żywo i należy pamiętać, że pozostawienie kanał kodowania na żywo w stanie "Uruchomiona" spowoduje naliczenie opłat rozliczeń.  Zaleca się natychmiast zatrzymać kanałów uruchomione po zakończeniu zdarzenia transmisji strumieniowej na żywo, aby zapobiec zmianom bardzo co godzinę.

> [!NOTE]
> W tym temacie omówiono atrybuty kanałami obsługującymi kodowanie na żywo (**standardowe** typ kodowania). Więcej informacji dotyczących pracy z kanałami, które nie są włączone kodowanie na żywo, zobacz [transmisję strumieniową na żywo za pomocą koderów lokalnych, które tworzą strumienie o różnych szybkościach transmisji bitów](media-services-live-streaming-with-onprem-encoders.md).
> 
> Upewnij się przejrzeć [zagadnienia](media-services-manage-live-encoder-enabled-channels.md#Considerations) sekcji.
> 
> 

## <a name="billing-implications"></a>Implikacje rozliczeń
Kanał kodowania na żywo rozpoczyna rozliczeń jak przejść stanu do "Uruchomiona" jest za pośrednictwem interfejsu API.   Można również wyświetlić stan w portalu Azure lub za pomocą narzędzia Azure Media Services Explorer (http://aka.ms/amse).

W poniższej tabeli przedstawiono sposób mapowania stanów kanału do stanów rozliczeń w portalu interfejsu API i usługi Azure. Należy zauważyć, że Stany różni się od interfejsu API i Portal UX. Jak najszybciej kanał jest w stanie "Uruchomiona" za pośrednictwem interfejsu API, lub w stanie "Gotowe" lub "Streaming" w portalu Azure, rozliczeń będzie aktywny.
Zatrzymał kanału z rozliczeniami można dodatkowo, należy zatrzymać kanału, za pomocą interfejsu API lub w portalu Azure.
Jest odpowiedzialny za zatrzymywanie kanałów, gdy wszystko będzie gotowe z kanału na żywo kodowania.  Brak zatrzymał kanału kodowania spowoduje dalsze rozliczeń.

### <a id="states"></a>Stany kanału oraz sposobu mapowania ich na tryb rozliczeń
Bieżący stan kanału. Możliwe wartości obejmują:

* **Zatrzymano**. Jest to stan początkowy kanału po jego utworzeniu (chyba że automatyczne uruchamianie zostało wybrane w portalu). Karta nie występuje w tym stanie. W tym stanie właściwości kanału mogą być aktualizowane, ale transmisja strumieniowa jest niedozwolona.
* **Uruchamianie**. Kanał jest uruchamiany. Karta nie występuje w tym stanie. W tym stanie nie są dozwolone ani aktualizacje, ani transmisja strumieniowa. Jeśli wystąpi błąd, kanał wróci do stanu Zatrzymany.
* **Uruchomiona**. Kanał może przetwarzać transmisje strumieniowe na żywo. Jest on teraz rozliczeń użycia. Musisz zatrzymać kanału, aby uniemożliwić dalsze rozliczeń. 
* **Zatrzymywanie**. Kanał jest zatrzymywany. Karta nie występuje w tym stan przejściowy. W tym stanie nie są dozwolone ani aktualizacje, ani transmisja strumieniowa.
* **Usuwanie**. Kanał jest usuwany. Karta nie występuje w tym stan przejściowy. W tym stanie nie są dozwolone ani aktualizacje, ani transmisja strumieniowa.

W tabeli poniżej pokazano, jak stany kanału przekładają się na naliczanie opłat. 

| Stan kanału | Wskaźniki w interfejsie użytkownika portalu | Jest to karta? |
| --- | --- | --- |
| Uruchamianie |Uruchamianie |Nie (stan przejściowy) |
| Działanie |Gotowy (brak uruchomionych programów)<br/>lub<br/>Transmisja strumieniowa (co najmniej jeden uruchomiony program) |TAK |
| Zatrzymywanie |Zatrzymywanie |Nie (stan przejściowy) |
| Zatrzymano |Zatrzymano |Nie |

### <a name="automatic-shut-off-for-unused-channels"></a>Automatyczne wyłączania nieużywanych kanałów
Począwszy od 25 stycznia 2016 Media Services wprowadzanie aktualizacji, która automatycznie zatrzyma kanał (kodowanie na żywo włączone) po jego działaniu w stanie nieużywane przez dłuższy okres. Dotyczy to kanałów, które nie mają żadnych aktywnych programów i nie uzyskały wejściowych wkład źródła danych przez dłuższy czas.

Próg okres nieużywane nominalnie jest 12 godzin, ale może ulec zmianie.

## <a name="live-encoding-workflow"></a>Przepływ pracy kodowania na żywo
Poniższy diagram przedstawia na żywo przepływ pracy transmisji strumieniowej, gdzie kanał odbiera strumień o pojedynczej szybkości transmisji bitów w jednym z następujących protokołów: RTMP lub Smooth Streaming; koduje go następnie strumienia do strumienia o wielokrotnej szybkości transmisji bitów. 

![Przepływ pracy na żywo][live-overview]

## <a id="scenario"></a>Typowy scenariusz transmisji strumieniowej na żywo
Poniżej przedstawiono ogólne etapy tworzenia typowych aplikacji transmisji strumieniowej na żywo.

> [!NOTE]
> Obecnie maksymalny zalecany czas trwania wydarzenia na żywo wynosi 8 godzin. Skontaktuj się z amslived@microsoft.com Jeśli chcesz uruchomić kanał na dłuższe okresy. Należy pamiętać, że istnieje rozliczeń wpływ kodowanie na żywo i należy pamiętać, że pozostawienie kanał kodowania na żywo w stanie "Uruchomiona" będą naliczane co godzinę opłat rozliczeń.  Zaleca się natychmiast zatrzymać kanałów uruchomione po zakończeniu zdarzenia transmisji strumieniowej na żywo, aby zapobiec zmianom bardzo co godzinę. 
> 
> 

1. Podłącz kamerę wideo do komputera. Uruchom i skonfiguruj na lokalny koder na żywo, który wysyła strumień **pojedynczego** szybkości transmisji bitów w jednym z następujących protokołów: RTMP lub Smooth Streaming. 
   
    Ten krok można również wykonać po utworzeniu kanału.
2. Utwórz i uruchom kanał. 
3. Pobierz adres URL pozyskiwania kanału. 
   
    Koder na żywo używa adresu URL pozyskiwania do wysyłania strumienia do kanału.
4. Pobierz adres URL podglądu kanału. 
   
    Użyj tego adresu URL, aby sprawdzić, czy kanał prawidłowo odbiera strumień na żywo.
5. Utwórz program. 
   
    Korzystając z portalu Azure, tworzenia program tworzy także zasób. 
   
    Podczas korzystania z zestawu .NET SDK lub REST konieczne utworzenie elementu zawartości i określanie użycia tego zasobu, podczas tworzenia programu. 
6. Opublikuj zasób skojarzony z programem.   
   
    >[!NOTE]
    >Po utworzeniu konta usługi AMS zostanie do niego dodany **domyślny** punkt końcowy przesyłania strumieniowego mający stan **Zatrzymany**. Punkt końcowy przesyłania strumieniowego, z którego chcesz strumieniowo przesyłać zawartość, musi mieć stan **Uruchomiony**. 
    
7. Uruchom program, gdy wszystko będzie gotowe do rozpoczęcia przesyłania strumieniowego i archiwizacji.
8. Opcjonalnie można przesłać do kodera na żywo sygnał o rozpoczęciu reklamy. Reklama jest wstawiana do strumienia wyjściowego.
9. Zatrzymaj program w dowolnym momencie, w którym chcesz zatrzymać przesyłanie strumieniowe i archiwizowanie wydarzenia.
10. Usuń program (opcjonalnie można również usunąć zasób).   

> [!NOTE]
> Jest bardzo ważne nie zapomnij zatrzymał kanału kodowanie na żywo. Należy pamiętać, że istnieje co godzinę rozliczeń wpływ kodowanie na żywo i należy pamiętać, że pozostawienie kanał kodowania na żywo w stanie "Uruchomiona" spowoduje naliczenie opłat rozliczeń.  Zaleca się natychmiast zatrzymać kanałów uruchomione po zakończeniu zdarzenia transmisji strumieniowej na żywo, aby zapobiec zmianom bardzo co godzinę. 
> 
> 

## <a id="channel"></a>Kanał danych wejściowych (pozyskiwania) konfiguracji
### <a id="Ingest_Protocols"></a>Pozyskiwania protokołu przesyłania strumieniowego
Jeśli **typu kodera** ustawiono **standardowe**, prawidłowe opcje to:

* Pojedynczej szybkości transmisji bitów **RTMP**
* Pojedynczej szybkości transmisji bitów **pofragmentowany MP4** (Smooth Streaming)

#### <a id="single_bitrate_RTMP"></a>Pojedyncza szybkość transmisji bitów RTMP
Kwestie do rozważenia:

* Przychodzący strumień nie może zawierać wideo o różnych szybkościach transmisji bitów
* Strumienia wideo powinien mieć średniej szybkości transmisji bitów poniżej 15 MB/s
* Strumień audio powinny mieć średniej szybkości transmisji bitów poniżej 1 MB/s
* Kodeki obsługiwane są następujące:
* MPEG-4 AVC / wideo H.264
* Linii bazowej, Main, wysokiej profilu (8-bitową 4:2:0)
* Wysoka profilu 10 (10-bitowy 4:2:0)
* Wysoka 422 profilu (10-bitowy 4:2:2)
* Audio AAC-LC MPEG-2
* Mono, Stereo, przestrzennego (5.1, 7.1)
* częstotliwość próbkowania 44,1 kHz
* OPAKOWYWANIE ADTS styl MPEG-2
* Następujące kodery zalecane:
* Telestream Wirecast
* Koder na żywo Flash nośnika

#### <a name="single-bitrate-fragmented-mp4-smooth-streaming"></a>Pojedyncza szybkość transmisji bitów podzielonej zawartości w formacie MP4 (Smooth Streaming)
Typowy przypadek użycia:

Wykorzystanie lokalnych koderów na żywo z dostawców takich jak stanie wolnym technologii, Ericsson, Ateme, Envivio, aby wysłać strumień wejściowy za pośrednictwem Internetu Otwórz w pobliżu Azure centrum danych.

Kwestie do rozważenia:

Sam jak w przypadku [pojedyncza szybkość transmisji bitów RTMP](media-services-manage-live-encoder-enabled-channels.md#single_bitrate_RTMP).

#### <a name="other-considerations"></a>Inne zagadnienia
* Nie można zmienić protokołu wejściowego, gdy kanał lub skojarzone z nim programy są uruchomione. Jeśli potrzebujesz różnych protokołów, utwórz osobny kanał dla każdego protokołu wejściowego.
* Maksymalna rozdzielczość w przypadku przychodzących strumienia wideo wynosi 1920 x 1080 pikseli i co najwyżej 60 pola/sekundę Jeśli naprzemiennych lub 30 klatek na sekundę Jeśli progresywnego.

### <a name="ingest-urls-endpoints"></a>Pozyskiwania adresów URL (punkty końcowe)
Kanał zawiera ono wejściowy punkt końcowy (adres URL pozyskiwania) określenie w kodera na żywo, więc może wypchnąć koder strumieni dla kanałów.

Adresy URL pozyskiwania można uzyskać po utworzeniu kanału. Aby uzyskać te adresy URL, kanał nie muszą być w **systemem** stanu. Gdy wszystko będzie gotowe uruchomić przekazywanie danych do kanału, który musi być w **systemem** stanu. Po uruchomieniu kanału wprowadzania danych można wyświetlić podgląd strumienia za pomocą adresu URL w wersji zapoznawczej.

Istnieje opcja wprowadzania pofragmentowany MP4 (Smooth Streaming) strumień na żywo za pośrednictwem połączenia SSL. Pozyskiwanie za pośrednictwem protokołu SSL, upewnij się, że aktualizacja adresu URL pozyskiwania HTTPS. Należy zauważyć, że obecnie AMS nie obsługuje protokołu SSL z domen niestandardowych.  

### <a name="allowed-ip-addresses"></a>Dozwolonych adresów IP
Można zdefiniować adresy IP, które mogą publikować pliki wideo w tym kanale. Dozwolone adresy IP można określić jako pojedynczy adres IP (np. „10.0.0.1”), zakres adresów IP przy użyciu adresu IP i maski podsieci CIDR (np. „10.0.0.1/22”) lub zakres adresów IP przy użyciu adresu IP i maski podsieci w zapisie kropkowo-cyfrowym (np. „10.0.0.1(255.255.252.0)”).

Jeśli adresy IP nie zostaną określone i brakuje definicji reguły, to żaden adres IP nie będzie dozwolony. Aby zezwolić na jakikolwiek adres IP, utwórz regułę i ustaw wartość 0.0.0.0/0.

## <a name="channel-preview"></a>Podgląd kanału
### <a name="preview-urls"></a>Adresy URL w wersji zapoznawczej
Kanały zapewniają punktu końcowego podglądu (adres URL w wersji zapoznawczej), używanej do wyświetlania i weryfikowania strumienia przed dalszego przetwarzania i dostarczania.

Po utworzeniu kanału możesz uzyskać adres URL w wersji zapoznawczej. Aby uzyskać adres URL, kanał nie muszą być w **systemem** stanu.

Po uruchomieniu kanału wprowadzania danych można wyświetlić podgląd strumienia.

> [!NOTE]
> Obecnie podglądu strumienia tylko mogą być dostarczane w pofragmentowany MP4 (Smooth Streaming) format bez względu na określony typ danych wejściowych. Można użyć [ http://smf.cloudapp.net/healthmonitor ](http://smf.cloudapp.net/healthmonitor) player do testowania Smooth Stream. Aby wyświetlić strumienia umożliwia także odtwarzacza hostowanej w portalu Azure.
> 
> 

### <a name="allowed-ip-addresses"></a>Dozwolone adresy IP
Można zdefiniować adresy IP, które mogą łączyć się z punktem końcowym w wersji zapoznawczej. Jeśli nie zawiera adresów IP nie zostaną określone może być dowolny adres IP. Dozwolone adresy IP można określić jako pojedynczy adres IP (np. „10.0.0.1”), zakres adresów IP przy użyciu adresu IP i maski podsieci CIDR (np. „10.0.0.1/22”) lub zakres adresów IP przy użyciu adresu IP i maski podsieci w zapisie kropkowo-cyfrowym (np. „10.0.0.1(255.255.252.0)”).

## <a name="live-encoding-settings"></a>Ustawienia kodowania na żywo
W tej sekcji opisano, jak ustawień kodera na żywo w kanale można dostosować, gdy **typu kodowania** kanał jest ustawiony na wartość **standardowe**.

> [!NOTE]
> Twoim kanale informacyjnym udział może zawierać tylko pojedynczą ścieżkę audio — wprowadzania wielu ścieżek audio nie jest obecnie obsługiwany. Twoją kodowanie na żywo z [live lokalnymi koduje](media-services-live-streaming-with-onprem-encoders.md), możesz wysłać udziału źródła strumieniowego w protokole Smooth Streaming zawierający wiele ścieżek audio.
> 
> 

### <a name="ad-marker-source"></a>Źródło znacznika usługi AD
Można określić źródło sygnałów znaczników reklamowych. Wartość domyślna to **interfejsu Api**, co oznacza, że koder na żywo w kanale powinien nasłuchiwać asynchronicznego **interfejsu API znacznika reklamy**.

### <a name="cea-708-closed-captions"></a>CEA 708 zamknięte podpisy
Opcjonalna Flaga, która określa, że koder na żywo będzie ignorował wszystkie dane podpisy CEA 708 osadzony w przychodzących wideo. Jeśli flaga jest ustawiona na false (ustawienie domyślne), koder wykryje i ponownie wstawić CEA 708 danych do strumieni wideo w danych wyjściowych.

#### <a name="index"></a>Indeks
Zaleca się wysyłanie w jednym programie strumień transportowy (SPTS). Jeśli strumień wejściowy zawiera wiele programów, analizuje tabeli mapy programu (rata) w danych wejściowych kodera na żywo w kanale, który identyfikuje dane wejściowe, które mają nazwę typu strumienia ADTS AAC MPEG-2 lub AC-3, System A AC 3 systemu-B lub prywatnej PES MPEG-2 lub MPEG-1 Audio lub Audio MPEG-2 i rozmieszcza je w kolejności określonej w konto Liczony od zera indeks jest następnie używany do odebrania wpis n-ty percentyl, w tym układ.

#### <a name="language"></a>Język
Identyfikator języka strumieniem audio, zgodne z normą ISO 639-2, takich jak ENG. Jeśli nie istnieje, wartość domyślna to i (niezdefiniowanej).

### <a id="preset"></a>Ustawienie systemu
Określa ustawienie wstępne do użycia przez koder na żywo w tym kanale. Obecnie jest jedyną dozwoloną wartość **Default720p** (ustawienie domyślne).

Należy pamiętać, że jeśli potrzebujesz predefiniowanych, należy skontaktować się amslived@microsoft.com.

**Default720p** będzie kodowanie wideo do następujących warstwy 7.

#### <a name="output-video-stream"></a>Wideo strumienia wyjściowego.
| Szybkość transmisji bitów | Szerokość | Wysokość | MaxFPS | Profil | Nazwa strumienia wyjściowego |
| --- | --- | --- | --- | --- | --- |
| 3500 |1280 |720 |30 |Wysoka |Video_1280x720_3500kbps |
| 2200 |960 |540 |30 |Główny |Video_960x540_2200kbps |
| 1350 |704 |396 |30 |Główny |Video_704x396_1350kbps |
| 850 |512 |288 |30 |Główny |Video_512x288_850kbps |
| 550 |384 |216 |30 |Główny |Video_384x216_550kbps |
| 350 |340 |192 |30 |Punkt odniesienia |Video_340x192_350kbps |
| 200 |340 |192 |30 |Punkt odniesienia |Video_340x192_200kbps |

#### <a name="output-audio-stream"></a>Audio strumienia wyjściowego.
Dźwięk jest zakodowany do stereo AAC-LC na 64 KB/s, częstotliwość próbkowania 44,1 kHz.

## <a name="signaling-advertisements"></a>Reklamy sygnalizowania
Kanał ma Live Encoding włączone, ma składnika w Twojej potok, który ma przetwarzania obrazu, a można manipulować go. Może sygnalizować, dla kanału wstawić typu i/lub anonsów do strumienia wychodzącego adaptacyjną szybkością transmisji bitów. Typu są nadal obrazów, które można użyć, aby pokrywał się wejściowych źródła na żywo w niektórych przypadkach (na przykład podczas podziału komercyjnych). Anonsowanie sygnałów, są sygnały zsynchronizowany czas, który osadzanie w strumienia wychodzącego mówić odtwarzacza wideo podjęcie działań specjalne — tak aby przełączyć się do anonsu w odpowiednim czasie. Zobacz to [blogu](https://codesequoia.wordpress.com/2014/02/24/understanding-scte-35/) Omówienie mechanizmu sygnalizowania SCTE 35 do tego celu. Oto typowy scenariusz, które można zaimplementować w zdarzenia na żywo.

1. Ma widzów pobrania obrazu zdarzenia wstępnego przed rozpoczęciem zdarzenia.
2. Ma widzów Pobierz obraz po zdarzeń po zakończeniu zdarzenia.
3. Ma widzów Pobierz obraz zdarzenie błędu, jeśli występuje problem podczas zdarzenia (na przykład awarii zasilania w stadium).
4. Wysyłanie obrazu AD-BREAK, aby ukryć wydarzenia na żywo, źródła danych podczas podziału komercyjnych.

Poniżej przedstawiono właściwości, które można ustawić podczas reklamy sygnalizowania. 

### <a name="duration"></a>Czas trwania
Czas w sekundach komercyjnych podziału. Musi to być niezerową wartość dodatnią, aby rozpocząć komercyjnych przerwę. Podczas podziału komercyjnych jest w toku, a czas trwania jest ustawione na zero z CueId dopasowania podziału komercyjnych w toku, anulowania tego podziału, a następnie.

### <a name="cueid"></a>CueId
Unikatowy identyfikator dla końca komercyjnych na użytek aplikacji podjęcie odpowiedniej akcji. Musi być dodatnią liczbą całkowitą. Można ustawić tej wartości losowych dodatnią liczbą całkowitą lub skorzystać z nadrzędnego systemu do śledzenia identyfikatorów wskaźnika. Należy upewnić się do normalizacji identyfikatorów dowolnym dodatnie liczby całkowite przed przesłaniem za pośrednictwem interfejsu API.

### <a name="show-slate"></a>Pokaż łupków
Opcjonalny. Sygnały kodera na żywo, aby przełączyć się do [domyślne łupków](media-services-manage-live-encoder-enabled-channels.md#default_slate) obrazu podczas podziału handlowych i Ukryj przychodzące źródła wideo. Podczas łupków również wyciszeniu dźwięku. Domyślnie jest **false**. 

Obraz używany będzie określony za pomocą zasobów domyślne łupków właściwość identyfikatora w czasie tworzenia kanału. Łupków będzie rozciągany w taki sposób, aby dopasować rozmiar wyświetlania obrazu. 

## <a name="insert-slate--images"></a>Wstawianie obrazów łupków
Aby przełączyć się do obrazu łupków można zasygnalizować kodera na żywo w kanale. Można go również zostać zgłoszony do końca łupków w toku. 

Koder na żywo można skonfigurować tak, aby przełączyć się do obrazu łupków i Ukryj przychodzące sygnał wideo w niektórych sytuacjach — na przykład podczas podziału ad. Jeśli nie skonfigurowano takie łupków, wejściowy plik wideo nie jest maskowane podczas tego podziału ad.

### <a name="duration"></a>Czas trwania
Czas trwania łupków, w sekundach. Musi to być niezerową wartość dodatnią, aby uruchomić łupków. Jeśli istnieje łupków w toku, a czas trwania zero jest określony, że łupków w toku zostanie zakończona.

### <a name="insert-slate-on-ad-marker"></a>Wstaw planszę w miejscu znacznika reklamy
Gdy ma wartość true, to ustawienie konfiguruje kodera na żywo można wstawić obrazu łupków podczas podziału ad. Wartość domyślna to true. 

### <a id="default_slate"></a>Identyfikator zasobu domyślny łupków

Opcjonalny. Określa identyfikator zasobu zasobów usługi nośnika, który zawiera obraz łupków. Domyślna ma wartość null. 


>[!NOTE] 
>Przed utworzeniem kanału, należy przesłać łupków obrazu z następującymi ograniczeniami jako zasób dedykowanych (żadne inne pliki powinna być w tym zasobów). Ten obraz jest używany tylko wtedy, gdy kodera na żywo jest wstawianie łupków z powodu przerwania ad lub został jawnie zasygnalizują, aby wstawić łupków. Koder na żywo można także przejść do trybu łupków podczas niektórych warunków błędów — na przykład jeśli sygnału wejściowego zostaną utracone. Nie ma żadnych opcji obraz niestandardowy koder na żywo przechodzi takich 'utraty sygnału wejściowego' stanu. Dla tej funkcji, można głosować [tutaj](https://feedback.azure.com/forums/169396-azure-media-services/suggestions/10190457-define-custom-slate-image-on-a-live-encoder-channel).


* Co najwyżej 1920 x 1080 pikseli rozdzielczość.
* Co najwyżej 3 (MB) rozmiar.
* Nazwa pliku musi mieć rozszerzenie *.jpg.
* Obraz, należy przekazać do elementu zawartości jako AssetFile tylko w tym zasobów i AssetFile ten powinien być oznaczony jako plik podstawowy. Zasób nie może być szyfrowany w magazynie.

Jeśli **domyślne łupków identyfikator zasobu** nie zostanie określona, i **Wstaw łupków na znacznika ad** ma ustawioną wartość **true**, domyślnego obrazu usługi Azure Media Services będą używane do ukrycia wejściowy plik wideo strumień. Podczas łupków również wyciszeniu dźwięku. 

## <a name="channels-programs"></a>Programy kanału
Kanał jest skojarzony z programami, które umożliwiają kontrolowanie publikowania i przechowywania segmentów strumienia na żywo. Kanały zarządzają programami. Relacja kanału i programu jest bardzo podobny do tradycyjnych multimediach, gdzie kanał ma stały strumień zawartości, a program obejmuje niektóre zdarzenia czasowe na tym kanale.

Można określić liczbę godzin, aby zachować zarejestrowaną zawartość na potrzeby programu przez ustawienie długości **Okna archiwum**. Ta wartość musi mieścić się w zakresie od 5 minut do maksymalnie 25 godzin. Długość okna archiwum określa również dostępny dla klientów zakres cofania odtwarzania pliku od bieżącego momentu transmisji na żywo. Programy mogą być transmitowane w określonym czasie, ale zawartość, która wykracza poza długość okna, jest stale odrzucana. Wartość tej właściwości określa również, jak długie mogą być manifesty na kliencie.

Każdy program jest skojarzony z zasobem, który zapisuje zawartość przesyłana strumieniowo. Zasób jest mapowana do bloku kontenera obiektów blob na koncie magazynu Azure i pliki w elementach zawartości są przechowywane jako obiekty BLOB w tym kontenerze. Aby opublikować program, więc klienci mogą wyświetlić strumienia należy utworzyć Lokalizator OnDemand dla skojarzonego elementu zawartości. Lokalizator umożliwia utworzenie adresu URL przesyłania strumieniowego, który można udostępnić klientom.

Kanał obsługuje maksymalnie trzy jednocześnie uruchomione programy, dzięki czemu można tworzyć wiele archiwów tego samego strumienia przychodzącego. Umożliwia to w razie potrzeby publikowanie i archiwizację różnych części wydarzenia. Na przykład zgodnie z wymaganiami biznesowymi potrzebna jest archiwizacja sześciu godzin programu, ale do emisji przeznaczonych jest tylko ostatnich 10 minut. W tym celu należy utworzyć dwa jednocześnie uruchomione programy. Jeden z programów jest skonfigurowany na potrzeby archiwizacji sześciu godzin zdarzenia, ale ten program nie jest publikowany. Drugi z programów jest skonfigurowany w celu archiwizacji 10 minut wydarzenia i ten program zostanie opublikowany.

Nie należy używać ponownie istniejących programów dla nowych zdarzeń. Zamiast tego należy utworzyć i uruchomić nowy program dla każdego zdarzenia, zgodnie z opisem w sekcji programowania aplikacji przesyłania strumieniowego na żywo.

Uruchom program, gdy wszystko będzie gotowe do rozpoczęcia przesyłania strumieniowego i archiwizacji. Zatrzymaj program w dowolnym momencie, w którym chcesz zatrzymać przesyłanie strumieniowe i archiwizowanie wydarzenia. 

Aby usunąć zarchiwizowaną zawartość, zatrzymaj i usuń program, a następnie usuń skojarzony element zawartości. Nie można usunąć elementu zawartości, jeśli jest on używany przez program. Najpierw należy usunąć program. 

Nawet po zatrzymaniu i usunięciu programu użytkownicy będą mogli przesyłać strumieniowo zarchiwizowaną zawartość wideo na żądanie tak długo, jak zasoby nie zostaną usunięte.

Jeśli chcesz zachować zarchiwizowaną zawartość, ale bez udostępniania jej do przesyłania strumieniowego, usuń lokalizator przesyłania strumieniowego.

## <a name="getting-a-thumbnail-preview-of-a-live-feed"></a>Pobieranie miniatury podglądu źródła na żywo
Jeśli kodowanie na żywo jest włączona, teraz można uzyskać podgląd źródła danych na żywo, jak osiągnie kanału. Może to być przydatnym narzędziem do sprawdzenia, czy Twoje źródło na żywo jest rzeczywiście osiągnięcia kanału. 

## <a id="states"></a>Stany kanału oraz sposobu mapowania stanów trybu rozliczeń
Bieżący stan kanału. Możliwe wartości obejmują:

* **Zatrzymano**. To jest wstępny stan kanału po jego utworzeniu. W tym stanie właściwości kanału mogą być aktualizowane, ale transmisja strumieniowa jest niedozwolona.
* **Uruchamianie**. Kanał jest uruchamiany. W tym stanie nie są dozwolone ani aktualizacje, ani transmisja strumieniowa. Jeśli wystąpi błąd, kanał wróci do stanu Zatrzymany.
* **Uruchomiona**. Kanał może przetwarzać transmisje strumieniowe na żywo.
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
> Obecnie średnią start kanał jest około 2 minuty, ale czasami może potrwać maksymalnie 20 + minut. Resetowanie kanału może potrwać do 5 minut.
> 
> 

## <a id="Considerations"></a>Zagadnienia dotyczące
* Gdy kanałem **standardowe** typ kodowania napotyka utracie źródła danych wejściowych źródło/udział, jego kompensuje go przez zamianę źródła audio/wideo łupków błąd i wyciszenia. Kanał będą w dalszym ciągu Emituj łupków, dopóki nie wznawia strumieniowego źródła danych wejściowych/udziału. Zaleca się, że kanału na żywo nie należy pozostawiać w stanie przez czas dłuższy niż 2 godziny. Zachowania kanału po ponownym nawiązaniu połączenia wejściowy nie jest gwarantowana poza ten punkt, nie jest jego zachowanie w odpowiedzi na polecenie resetowania. Musisz zatrzymać kanału, należy ją usunąć i utworzyć nowy.
* Nie można zmienić protokołu wejściowego, gdy kanał lub skojarzone z nim programy są uruchomione. Jeśli potrzebujesz różnych protokołów, utwórz osobny kanał dla każdego protokołu wejściowego.
* Za każdym razem, gdy skonfigurujesz kodera na żywo, wywołaj **zresetować** metody w kanale. Zanim je zresetujesz kanału, należy zatrzymać program. Po zresetowaniu kanału, ponownie uruchom program.
* Kanał można zatrzymać tylko wtedy, gdy jest on w stanie uruchomiona, a wszystkie programy na kanale zostały zatrzymane.
* Domyślnie 5 kanałów można dodawać tylko do konta usługi Media Services. Jest to przydziału elastycznego na wszystkich nowych kont. Aby uzyskać więcej informacji, zobacz [przydziały i ograniczenia](media-services-quotas-and-limitations.md).
* Nie można zmienić protokołu wejściowego, gdy kanał lub skojarzone z nim programy są uruchomione. Jeśli potrzebujesz różnych protokołów, utwórz osobny kanał dla każdego protokołu wejściowego.
* Rozliczenie jest przeprowadzane tylko w przypadku kanału **systemem** stanu. Aby uzyskać więcej informacji, zapoznaj się [to](media-services-manage-live-encoder-enabled-channels.md#states) sekcji.
* Obecnie maksymalny zalecany czas trwania wydarzenia na żywo wynosi 8 godzin. Napisz na adres amslived@microsoft.com, jeśli potrzebujesz uruchomić kanał na dłuższy czas.
* Upewnij się, że ma punktu końcowego przesyłania strumieniowego, z którego chcesz zawartości strumienia w **systemem** stanu.
* Ustawienie wstępne kodowania używa pojęcie "maksymalną szybkość" 30 klatek na sekundę. Dlatego w przypadku danych wejściowych jest 60 klatek na sekundę / 59.94i, ramki wejściowe są porzucony/dezaktywuje-interlaced do 30/29,97 kl. / s. Jeśli dane wejściowe są 50 klatek na sekundę/50i, ramki wejściowe są porzucony/dezaktywuje-interlaced do 25 kl. / s. Jeśli dane wejściowe są 25 kl. / s, dane wyjściowe pozostaje w 25 kl. / s.
* Nie zapomnij o zatrzymanie YOUR kanały po zakończeniu. Jeśli nie, będą nadal rozliczeń.

## <a name="known-issues"></a>Znane problemy
* Czas uruchamiania kanału została zwiększona do średnio 2 minuty, ale w czasie z rosnącego zapotrzebowania trwało maksymalnie 20 + minut.
* Obrazy łupków powinna być zgodna z opisem ograniczenia [tutaj](media-services-manage-live-encoder-enabled-channels.md#default_slate). Jeśli nastąpi próba utworzenia kanału z łupków domyślny, który jest większy niż 1920 x 1080 pikseli, żądanie zostanie ostatecznie błąd.
* Ponownie... nie zapomnij kanały YOUR STOP, po zakończeniu przesyłania strumieniowego. Jeśli nie, będą nadal rozliczeń.

## <a name="next-step"></a>Następny krok
Przejrzyj ścieżki szkoleniowe dotyczące usługi Media Services.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-topics"></a>Powiązane tematy
[Dostarczanie wydarzeń na żywo przesyłania strumieniowego w usłudze Azure Media Services](media-services-overview.md)

[Tworzenia kanałów wykonujących kodowanie na żywo z wystąpieniu szybkości transmisji bitów do strumienia o adaptacyjnej szybkości transmisji bitów z portalu](media-services-portal-creating-live-encoder-enabled-channel.md)

[Tworzenia kanałów wykonujących kodowanie na żywo z wystąpieniu szybkości transmisji bitów do strumienia o adaptacyjnej szybkości transmisji bitów przy użyciu zestawu .NET SDK](media-services-dotnet-creating-live-encoder-enabled-channel.md)

[Zarządzanie kanałami z interfejsu API REST](https://docs.microsoft.com/rest/api/media/operations/channel)
 
[Pojęcia dotyczące usługi Media Services](media-services-concepts.md)

[Specyfikacja pozyskiwania MP4 pofragmentowane na żywo w usłudze Azure Media Services](media-services-fmp4-live-ingest-overview.md)

[live-overview]: ./media/media-services-manage-live-encoder-enabled-channels/media-services-live-streaming-new.png

