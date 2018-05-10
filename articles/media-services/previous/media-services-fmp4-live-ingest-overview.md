---
title: Azure Media Services na żywo pofragmentowane MP4 pozyskiwania specyfikacji | Dokumentacja firmy Microsoft
description: Określenie tej wartości opisano protokołu i format pofragmentowane na podstawie MP4 na żywo przesyłania strumieniowego wprowadzanie do usługi Azure Media Services. Azure Media Services umożliwia strumienia wydarzeń na żywo i emisji zawartości w czasie rzeczywistym za pomocą usługi Azure jako platformy w chmurze. W tym dokumencie omówiono również najlepsze rozwiązania dotyczące tworzenia wysoce nadmiarowe i niezawodne na żywo pozyskiwania mechanizmów.
services: media-services
documentationcenter: ''
author: cenkdin
manager: cfowler
editor: ''
ms.assetid: 43fac263-a5ea-44af-8dd5-cc88e423b4de
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2017
ms.author: cenkd;juliako
ms.openlocfilehash: 367066b0073775ae10fd5e9d084c5bc78ebf6677
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="azure-media-services-fragmented-mp4-live-ingest-specification"></a>Specyfikacja pozyskiwania Azure Media Services MP4 pofragmentowane na żywo
Określenie tej wartości opisano protokołu i format pofragmentowane na podstawie MP4 na żywo przesyłania strumieniowego wprowadzanie do usługi Azure Media Services. Usługa Media Services udostępnia usługi transmisji strumieniowej na żywo, używanego przez klientów do strumienia wydarzeń na żywo i emisji zawartości w czasie rzeczywistym za pomocą usługi Azure jako platformy w chmurze. W tym dokumencie omówiono również najlepsze rozwiązania dotyczące tworzenia wysoce nadmiarowe i niezawodne na żywo pozyskiwania mechanizmów.

## <a name="1-conformance-notation"></a>1. Notacja zgodność
Klucz ",""Nie może," "Wymagane", "SHALL", "są nie może," "SHOULD", "Nie powinien," wyrazy "Zalecane", "Może" i "Opcjonalne" w tym dokumencie mają być interpretowane opisanych w dokumencie RFC 2119.

## <a name="2-service-diagram"></a>2. Diagram usługi
Na poniższym diagramie przedstawiono wysokiego poziomu architektury usługi transmisji strumieniowej na żywo w usłudze Media Services:

1. Koder na żywo wypychanie źródeł danych na żywo do kanałów, które są tworzone i udostępniane za pomocą zestawu SDK usługi Azure Media Services.
2. Kanały, programów i punktów końcowych przesyłania strumieniowego w usłudze Media Services obsłużyć wszystkie na żywo przesyłania strumieniowego funkcje, w tym pozyskiwanie, formatowanie, chmury DVR, zabezpieczenia, skalowalność i nadmiarowość.
3. Opcjonalnie klienci mogą wybrać do wdrożenia warstwa Azure Content Delivery Network między punktu końcowego przesyłania strumieniowego i punktów końcowych klienta.
4. Strumień punktów końcowych klienta z punktu końcowego przesyłania strumieniowego przy użyciu protokołów HTTP adaptacyjnego przesyłania strumieniowego. Przykładami Microsoft Smooth Streaming, dynamiczne adaptacyjne przesyłanie strumieniowe za pośrednictwem protokołu HTTP (DASH lub MPEG-DASH) i Apple HTTP Live Streaming (HLS).

![pozyskiwania przepływu][image1]

## <a name="3-bitstream-format--iso-14496-12-fragmented-mp4"></a>3. Format Bitstream — ISO 14496 12 pofragmentowany plik MP4
Format danych przesyłanych w sieci dla transmisji strumieniowych na żywo pozyskiwania omówione w tym dokumencie jest oparta na [ISO-14496-12]. Aby uzyskać szczegółowy opis pofragmentowane formacie MP4 i rozszerzeń zarówno dla plików wideo na żądanie i wprowadzanie transmisji strumieniowej na żywo, zobacz [[MS-SSTR]](http://msdn.microsoft.com/library/ff469518.aspx).

### <a name="live-ingest-format-definitions"></a>Na żywo pozyskiwania definicje formatu
Poniższa lista zawiera opis format specjalnych definicji, które mają zastosowanie do aktywnego pozyskiwania do usługi Azure Media Services:

1. **Ftyp zawiera informacje**, **aktywne okno manifestu serwera**, i **moov** pola muszą być wysyłane z każdym żądaniem (HTTP POST). Te pola muszą być wysyłane na początku strumienia i pozyskiwania koder należy nawiązać ponownie połączenie wznowienie strumienia za każdym razem. Aby uzyskać więcej informacji zobacz sekcja 6 w [1].
2. 3.3.2 części [1] określa opcjonalne pola o nazwie **StreamManifestBox** dla pozyskiwania na żywo. Z powodu logiki routingu usługi równoważenia obciążenia Azure przy użyciu tego pola jest przestarzały. Pole nie powinien być obecny podczas wprowadzania do usługi Media Services. Jeśli to pole jest obecne, Media Services dyskretnie zignoruje ją.
3. **TrackFragmentExtendedHeaderBox** pola zdefiniowane w 3.2.3.2 [1] musi być obecny dla każdego fragmentu.
4. Wersja 2 **TrackFragmentExtendedHeaderBox** pole powinien być używany do generowania segmentów nośnika, które mają identyczne adresów URL w wielu centrach danych. Pole indeksu fragment jest wymagane w trybie failover między datacenter opartego na indeksie formatów przesyłania strumieniowego, takich jak Apple HLS i MPEG-DASH opartego na indeksie. Aby włączyć tryb failover między datacenter, indeks fragmentu musi synchronizowane między wieloma koderów i zwiększyć o 1 dla każdego fragmentu nośnika kolejnych nawet przez koder ponownego uruchomienia lub w przypadku awarii.
5. Pole o nazwie definiuje 3.3.6 części [1] **MovieFragmentRandomAccessBox** (**mfra**) które mogą być wysyłane na końcu na żywo wprowadzanie wskazująca zakończenia z strumienia (EOS) do kanału. Z powodu logiki pozyskiwania Media Services, przy użyciu EOS jest przestarzałe i **mfra** polu dla wprowadzanie na żywo nie mają być wysyłane. Jeśli wysyłane, Media Services dyskretnie zignoruje ją. Resetowanie stanu punktu pozyskiwania, firma Microsoft zaleca się używanie [Resetowanie kanału](https://docs.microsoft.com/rest/api/media/operations/channel#reset_channels). Zalecamy również użycie [Zatrzymaj Program](https://msdn.microsoft.com/library/azure/dn783463.aspx#stop_programs) do końca prezentacji i strumienia.
6. Czas trwania fragmentu MP4 powinna być stała, aby zmniejszyć rozmiar manifesty na kliencie. Stałe MP4, czas trwania fragmentu zwiększa również Algorytm heurystyczny Pobierz klienta za pośrednictwem Powtórz tagów. Czas trwania mogą ulegać zmianie w celu kompensacji szybkość klatek nie jest liczbą całkowitą.
7. Czas trwania fragmentu MP4 powinna wynosić około 2 do 6 w sekundach.
8. MP4 fragmentu sygnatury czasowe i indeksów (**TrackFragmentExtendedHeaderBox** `fragment_ absolute_ time` i `fragment_index`) powinna odbierane w kolejności rosnącej. Chociaż usługi Media Services jest odporność na zduplikowane fragmenty, ograniczył możliwość zmiany kolejności fragmenty zgodnie z nośnika osi czasu.

## <a name="4-protocol-format--http"></a>4. Format protokołu — HTTP
ISO fragmentacji na podstawie MP4 na żywo pozyskiwania dla usługi Media Services używa standardowego żądania HTTP POST długotrwałe do przesyłania danych zakodowanego nośnika, który jest spakowany w formacie MP4 pofragmentowane usługi. Każdy HTTP POST wysyła kompletna pofragmentowany bitstream MP4 ("strumień"), od początku z pola nagłówka (**ftyp zawiera informacje**, **aktywne okno manifestu serwera**, i **moov** pola ) i kontynuowanie sekwencję fragmenty (**moof** i **mdat** pól). Aby składni adresu URL dla żądania HTTP POST zobacz sekcję 9.2 w [1]. Przykład adresu URL przesyłania to: 

    http://customer.channel.mediaservices.windows.net/ingest.isml/streams(720p)

### <a name="requirements"></a>Wymagania
Poniżej przedstawiono szczegółowe wymagania:

1. Koder należy uruchomić emisję, wysyłając żądanie HTTP POST z pustym "treść" (zerowej długości zawartości) przy użyciu tego samego adresu URL wprowadzanie. Może to ułatwić koder szybko wykryć, czy punkt końcowy na żywo wprowadzanie jest prawidłowy, a jeśli ma żadnego uwierzytelniania lub inne warunki wymagane. Dla protokołu HTTP serwer nie może odesłania odpowiedzi HTTP do momentu otrzymania całego żądania, w tym treść wpisu. Charakter długotrwałe wydarzenia na żywo bez tego kroku koder może nie móc wykrywać wszelkie błędy, aż do zakończeniem wysyłania wszystkie dane.
2. Koder musi obsługiwać żadnych błędów lub wezwań do uwierzytelnienia z powodu (1). Jeśli (1) zakończy się pomyślnie z odpowiedzi 200, będzie kontynuowane.
3. Koder musi rozpoczynać nowe żądanie HTTP POST pofragmentowany strumień MP4. Ładunek musi rozpoczynać się od pola nagłówka, a następnie fragmenty. Należy pamiętać, że **ftyp zawiera informacje**, **aktywne okno manifestu serwera**, i **moov** pola (w podanej kolejności) muszą być wysyłane z każdym żądaniem, nawet jeśli koder należy nawiązać ponownie połączenie, ponieważ poprzednie żądanie zostało przerwane przed końcem strumienia. 
4. Koder muszą używać transferu pakietowego kodowanie przekazywania, ponieważ nie jest możliwe do prognozowania całej długości zawartości wydarzenia na żywo.
5. Po zdarzeniu umieszczeniu po wysłaniu ostatniego fragment koder bezpiecznie musi kończyć się transferu pakietowego Kodowanie komunikatu sekwencji (większość stosy klienta HTTP jego obsługa automatycznie). Koder musi Trwa oczekiwanie na kod odpowiedzi końcowego powrotu usługi, a następnie zakończenie połączenia. 
6. Koder nie może użyj `Events()` rzeczownik zgodnie z opisem w 9.2 w [1] na żywo wprowadzanie do usługi Media Services.
7. Jeśli żądanie HTTP POST kończy lub limitu czasu z powodu błędu TCP przed końcem strumienia, koder musi wygenerowanie nowego żądania POST, używając nowego połączenia, a następnie postępuj zgodnie z powyższych wymagań. Koder musi ponadto Wyślij ponownie poprzednie dwa fragmenty MP4 dla każdej ścieżki w strumieniu i wznowienie bez wprowadzania brak ciągłości w osi czasu nośnika. Ponowne wysyłanie ostatnie dwa fragmenty MP4 dla każdej ścieżki gwarantuje, że jest bez utraty danych. Innymi słowy jeśli zawiera strumień audio i wideo ścieżkę, a nie bieżącego żądania POST, koder musi ponownie i Wyślij ponownie ostatnie dwa fragmenty ścieżki audio, które wcześniej zostały wysłane pomyślnie, a ostatnie dwa fragmenty wideo śledzenie, które zostały pomyślnie wysłane, aby upewnić się, że istnieje nie powoduje utraty danych. Koder muszą zachować "do przodu" bufora fragmentów nośnika, które wysyła go ponownie po wznowieniu połączenia.

## <a name="5-timescale"></a>5. skali czasu
[[MS-SSTR] ](https://msdn.microsoft.com/library/ff469518.aspx) w tym artykule opisano sposób użycia skali czasu dla **SmoothStreamingMedia** (sekcja 2.2.2.1) **StreamElement** (sekcja 2.2.2.3) **StreamFragmentElement** () Sekcja 2.2.2.6) i **LiveSMIL** (2.2.7.3.1 sekcji). Jeśli nie ma wartości skali czasu, wartość domyślna używana jest 10 000 000 (10 MHz). Mimo że specyfikacji formatu Smooth Streaming nie blokuje użycia innych wartości skali czasu, większości implementacji kodera użyć domyślną wartość (10 MHz) do generowania Smooth Streaming pozyskiwania danych. Ze względu na [dynamicznego tworzenia pakietów Azure Media](media-services-dynamic-packaging-overview.md) funkcji zalecane użycie 90 KHz Skala czasu dla strumieni wideo i 44,1 KHz lub 48.1 KHz dla strumieni audio. Jeśli wartości różnych skali czasu są używane do różnych strumieni, należy wysłać strumień poziom skali czasu. Aby uzyskać więcej informacji, zobacz [[MS-SSTR]](https://msdn.microsoft.com/library/ff469518.aspx).     

## <a name="6-definition-of-stream"></a>6. Definicja "strumień"
Strumień to podstawowa jednostka operacji w wprowadzanie na żywo do tworzenia prezentacji na żywo, obsługę przesyłania strumieniowego trybu failover i scenariusze nadmiarowości. Strumień jest zdefiniowany jako jeden unikatowy, pofragmentowane bitstream MP4, który może zawierać pojedynczą ścieżkę lub wiele ścieżek. Prezentacja pełnej na żywo może zawierać jeden lub więcej strumieni, w zależności od konfiguracji koderów na żywo. Poniższe przykłady przedstawiają różne opcje użycia strumieni utworzenie pełnej na żywo prezentacji.

**Przykład:** 

Klient chce utworzyć prezentację transmisji strumieniowej na żywo obejmuje następujące szybkości transmisji bitów audio/wideo:

Wideo — 3000 KB/s, 1500 KB/s, 750 Kb/s

Audio – 128 Kb/s

### <a name="option-1-all-tracks-in-one-stream"></a>Opcja 1: Wszystkie ścieżki w jednego strumienia
W przypadku tej opcji jeden koder generuje wszystkie ścieżki audio/wideo i zawiera ich w jednym pofragmentowane bitstream MP4. Pofragmentowane bitstream MP4 jest następnie wysyłana za pośrednictwem jednego połączenia HTTP POST. W tym przykładzie jest tylko jeden strumień w tej prezentacji na żywo.

![Śledź strumieni-do-jednego.][image2]

### <a name="option-2-each-track-in-a-separate-stream"></a>Opcja 2: Każdej ścieżki w oddzielnych strumienia
W przypadku tej opcji koder umieszcza jedną ścieżkę bitstream każdego fragmentu MP4 i zapisuje wszystkie strumienie przez oddzielne połączenia HTTP. Można to zrobić za pomocą kodera jednego lub wielu koderów. Wprowadzanie na żywo widzi tej prezentacji na żywo, jak składa się z czterech strumieni.

![Śledzi oddzielne strumienie][image3]

### <a name="option-3-bundle-audio-track-with-the-lowest-bitrate-video-track-into-one-stream"></a>Opcja 3: Ścieżka audio pakietu z najniższą Ścieżka wideo szybkości transmisji bitów do jednego strumienia
W przypadku tej opcji odbiorcy zdecydował się pakietu ścieżki audio ze ścieżką wideo najniższa szybkość transmisji bitów w jednym fragmencie MP4 bitstream i pozostawić innych dwóch ścieżek wideo jako osobne strumieni. 

![Śledzi strumieni audio i wideo][image4]

### <a name="summary"></a>Podsumowanie
Nie jest to pełna lista wszystkich opcji wprowadzanie możliwe w ramach tego przykładu. Jak rzeczy zgrupowania ścieżki do strumieni jest obsługiwana przez wprowadzanie na żywo. Klienci i dostawcy kodera można wybrać własne implementacje na podstawie engineering złożoności, pojemność koder i nadmiarowości i zagadnienia dotyczące trybu failover. Jednak w większości przypadków jest tylko jedną ścieżkę audio dla całej prezentacji na żywo. Tak ważne jest zapewnienie healthiness strumienia pozyskiwania, zawierającą ścieżkę audio. Ta kwestia często powoduje umieszczenie ścieżki audio w jego własnej strumienia (jak opcja 2) lub funkcję tworzenia pakietów z Ścieżka wideo najniższa szybkość transmisji bitów (jak opcja 3). Ponadto dla lepszej nadmiarowość i odporność na uszkodzenia, wysyłanie tej samej ścieżki audio w dwóch różnych strumieni (opcja 2 z nadmiarowe ścieżki audio) lub ścieżkę audio przy użyciu co najmniej dwóch ścieżek wideo najniższa szybkość transmisji bitów (opcja 3 o audio dołączone do co najmniej dwa grupowania strumienie wideo) zdecydowanie zaleca się na żywo pozyskiwania do usługi Media Services.

## <a name="7-service-failover"></a>7. Usługa trybu failover
Podany rodzaj transmisja strumieniowa na żywo obsługę pracy awaryjnej dobrej jest istotne dla zapewnienia dostępności usługi. Usługa Media Services jest przeznaczona do obsługi różnych typów awarii, w tym błędy sieci, serwer błędów i problemów z magazynowaniem. Gdy jest używany w połączeniu z logiką prawidłowego trybu failover po stronie kodera na żywo, klientów można osiągnąć wysoce niezawodne usługa transmisji strumieniowej na żywo z chmury.

W tej sekcji omówiono scenariuszach pracy awaryjnej usługi. W takim przypadku niepowodzenia wykonywany jest gdzieś w ramach usługi, i ujawnia się jako błąd sieciowy. Poniżej przedstawiono kilka zaleceń dla implementacji kodera do obsługi trybu failover usługi:

1. Użyj limitu 10 sekundy do nawiązywania połączeń TCP. Jeśli próba nawiązania połączenia trwa dłużej niż 10 sekund, przerwać operację i spróbuj ponownie. 
2. Użyj krótki limit czasu wysyłania żądania HTTP fragmentów komunikatów. Jeśli czas fragmentu MP4 docelowy to N sekund, użyj limit czasu wysyłania, od N do 2 N sekund; na przykład jeśli czas trwania fragmentu MP4 6 sekund, Użyj limitu czasu w sekundach 6 i 12. Jeśli zostanie przekroczony limit czasu, zresetować połączenia, Otwórz nowe połączenie i wznowić strumienia pozyskiwania na nowe połączenie. 
3. Obsługa stopniowego buforu, który ma ostatnich dwóch fragmenty dla każdej ścieżki, które zostały pomyślnie i całkowicie wysłane do usługi.  Jeśli żądanie HTTP POST dla strumienia zostało zakończone lub limit czasu przed koniec strumienia, otworzyć nowe połączenie i rozpocząć kolejnego żądania HTTP POST, Wyślij ponownie nagłówki strumienia wysłania ostatniego dwa fragmenty dla każdej ścieżki i wznowić strumienia bez wprowadzania d iscontinuity na osi czasu nośnika. Zmniejsza ryzyko utraty danych.
4. Zaleca się, że koder nie istnieje limit liczby ponownych prób nawiązania połączenia lub wznowić przesyłania strumieniowego po wystąpieniu błędu TCP.
5. Po wystąpieniu błędu TCP:
  
    a. Należy zamknąć bieżące połączenie, a nowe połączenie musi zostać utworzony dla nowego żądania HTTP POST.

    b. Nowe HTTP POST adres URL musi być taka sama jak początkowego adresu URL przesyłania.
  
    c. Nowe HTTP POST musi zawierać nagłówki strumienia (**ftyp zawiera informacje**, **aktywne okno manifestu serwera**, i **moov** pól), które są takie same jak nagłówków strumienia początkowej POST.
  
    d. Ostatnie dwa fragmenty, wysyłane dla każdej ścieżki, należy ponownie wysłać i przesyłania strumieniowego wznowienia bez wprowadzania brak ciągłości w osi czasu nośnika. Sygnatury czasowe fragmentu MP4 należy zwiększyć stale, nawet przez żądania HTTP POST.
6. Koder należy zakończyć żądanie HTTP POST, jeśli dane nie są wysyłane z szybkością proporcjonalnie do czasu trwania fragmentu MP4.  Żądanie HTTP POST, który nie będzie przesyłał dane mogą uniemożliwić szybko odłączanie koder w przypadku aktualizacji usługi Media Services. Z tego powodu HTTP POST do rozrzedzonych ścieżki (sygnał ad) powinna być krótkotrwały i przerywa jak rozrzedzonych fragmentu są wysyłane.

## <a name="8-encoder-failover"></a>8. Koder trybu failover
Koder pracy awaryjnej jest drugi typ scenariusza trybu failover, który musi być kierowane do dostarczania transmisji strumieniowej na żywo na trasie. W tym scenariuszu wystąpi błąd na stronie kodera. 

![Koder trybu failover][image5]

Następujące oczekiwań Zastosuj z punktu końcowego wprowadzanie na żywo w przypadku pracy awaryjnej kodera:

1. NALEŻY utworzyć nowe wystąpienie kodera kontynuowanie przesyłania strumieniowego, zgodnie z opisami na diagramie (strumienia wideo z linii kreskowanej k 3000).
2. Koder nowe musi używać tego samego adresu URL dla żądania HTTP POST, co wystąpienia nie powiodło się.
3. Koder nowego żądania POST musi zawierać pola tego samego nagłówka MP4 pofragmentowane jako wystąpienie nie powiodło się.
4. Koder nowe musi poprawnie synchronizowane za pomocą wszystkich innych uruchomionych koderów dla tej samej prezentacji na żywo można wygenerować zsynchronizowanej przykłady audio i wideo z granice wyrównany fragmentu.
5. Nowy strumień musi być semantycznie równoważne z poprzednich strumienia i wymienne poziomach nagłówka i fragmentu.
6. Nowy koder należy spróbować zminimalizować ryzyko utraty danych. `fragment_absolute_time` i `fragment_index` nośnika fragmenty należy zwiększyć od punktu, w którym koder ostatnio zatrzymano. `fragment_absolute_time` i `fragment_index` należy zwiększyć w sposób ciągły, ale dozwolone jest wprowadzenie brak ciągłości, jeśli to konieczne. Usługi Media Services ignoruje fragmentów, które ma już odbierane i przetwarzane, więc lepiej błąd boku ponowne wysyłanie fragmenty, niż można wprowadzić przerw na osi czasu nośnika. 

## <a name="9-encoder-redundancy"></a>9. nadmiarowość kodera
Krytyczne dla niektórych wydarzeń na żywo że żądanie nawet wyższej dostępności i jakości obsługi, zaleca się użycie aktywny aktywny nadmiarowych koderów w celu uzyskania płynnego trybu failover bez utraty danych.

![nadmiarowość kodera][image6]

Zgodnie z opisami na tym diagramie, dwie grupy koderów push dwie kopie każdego strumienia jednocześnie w ramach usługi na żywo. Ta konfiguracja jest obsługiwana, ponieważ usługi Media Services można odfiltrować zduplikowane fragmenty oparte na sygnatury czasowej ID i fragment strumienia. Wynikowa strumień na żywo i archiwum jest pojedynczej kopii wszystkich strumieni, który jest najlepsze możliwe agregacji z dwóch źródeł. Na przykład w hipotetyczny przypadków, tak długo, jak istnieje jeden koder (nie musi on być taki sam) jest uruchomiona na dowolnym etapie w czasie dla każdego strumienia, wynikowy strumień na żywo z usługi jest ciągły bez utraty danych. 

Wymagania dotyczące tego scenariusza są prawie takie same, jak wymagania w przypadku "Kodera trybu failover", z wyjątkiem drugi zestaw koderów uruchomionych w tym samym czasie jako podstawowy koderów.

## <a name="10-service-redundancy"></a>10. nadmiarowość usługi
Czasami wysokiej nadmiarowe dystrybucji globalnych, musi mieć między region tworzenia kopii zapasowej do obsługi regionalnej awarii. Rozszerzając topologii "Kodera nadmiarowość", klienci można wybrać wdrożenia nadmiarowe usługi w innym regionie, który jest połączony z drugiego zestawu koderów. Klienci mogą również współpracować z dostawcy Content Delivery Network, aby wdrożyć globalne Menedżera ruchu przed wdrożenia usługi dwóch bezproblemowo przekierowujący ruch z klientów. Wymagania dotyczące koderów są takie same jak przypadek "Kodera nadmiarowość". Jedynym wyjątkiem jest to, że drugi zestaw musi koderów na żywo wskazać inny pozyskiwania punktu końcowego. Na poniższym diagramie przedstawiono Instalatora:

![nadmiarowość usługi][image7]

## <a name="11-special-types-of-ingestion-formats"></a>11. Specjalne rodzaje wprowadzanie formatów
W tej sekcji omówiono specjalne rodzaje formatów wprowadzanie na żywo, które są przeznaczone do obsługi określonych scenariuszy.

### <a name="sparse-track"></a>Śledź rozrzedzony
W celu dostarczenia prezentacji transmisji strumieniowej na żywo przy użyciu środowiska wzbogaconego klienta, często jest niezbędne do przekazywania zdarzenia zsynchronizowany czas lub sygnały wewnątrzpasmowe przy użyciu danych głównych nośnika. Na przykład jest wstawiania reklam na żywo dynamicznych. Ten typ zdarzenia sygnalizowania różni się od przesyłania strumieniowego z powodu natury rozrzedzonych regularne audio/wideo. Innymi słowy sygnalizowania danych zwykle odbywa się stale i interwał może być trudna do przewidzenia. Pojęcie rozrzedzonych Śledź została zaprojektowana w celu pozyskiwania i emisji wewnątrzpasmowe sygnalizowania danych.

Poniższe kroki są zalecane implementacji służy do wprowadzania rozrzedzonych Śledź:

1. Utwórz oddzielne bitstream MP4 pofragmentowane zawierający tylko rozrzedzonych ścieżki, bez ścieżki audio i wideo.
2. W **aktywne okno manifestu serwera** zgodnie z definicją w sekcji 6 w [1], użyj *parentTrackName* parametru w celu określenia nazwy ścieżki nadrzędnej. Aby uzyskać więcej informacji zobacz sekcję 4.2.1.2.1.2 w [1].
3. W **aktywne okno manifestu serwera**, **manifestOutput** musi mieć ustawioną **true**.
4. Rozrzedzony charakter sygnalizowania zdarzenia, zaleca się następujące czynności:
   
    a. Na początku wydarzenia na żywo, koder wysyła pola nagłówka początkowej z usługą, dzięki czemu usługa do zarejestrowania rozrzedzonych śledzenie w manifeście klienta.
   
    b. Koder powinien przerwanie żądanie HTTP POST, gdy dane nie są wysyłane. Długotrwałe HTTP POST nie wysyła dane mogą uniemożliwić szybko odłączanie koder w przypadku ponownego uruchomienia aktualizacji lub serwera usługi Media Services. W takich przypadkach serwer multimediów jest tymczasowo zablokowany w operacji odbierania dla gniazda.
   
    c. W czasie, gdy nie są dostępne dane sygnalizowania koder SHOULD Zamknij POST protokołu HTTP żądania. Gdy żądanie POST jest aktywny, koder należy wysłać danych.

    d. Podczas wysyłania rozrzedzonych fragmenty, koder można ustawić jawne nagłówek content-length, jeśli jest dostępna.

    e. Podczas wysyłania rozrzedzonych fragmenty o nowe połączenie, koder powinno Rozpocznij wysyłanie pól nagłówka, następuje nowych fragmentów. Dotyczy to przypadków, w których trybu failover wykonywany między nimi, i ustanowiono nowe połączenie rozrzedzonych na nowy serwer, który nie otrzymała rozrzedzonych Śledź przed.

    f. Fragment rozrzedzonych Śledź staje się dostępnych dla klienta, gdy odpowiedni fragment Śledź nadrzędnego, który ma wartość równą lub większą sygnatury czasowej jest udostępniana do klienta. Na przykład, jeśli rozrzedzonych fragmentu zawiera sygnaturę czasową t = 1000, oczekuje się, że po klienta widzi "wideo" (przy założeniu nazwa ścieżki nadrzędnej "wideo") fragmentu sygnatury czasowej 1000 lub nowszych, można pobrać fragmentu rozrzedzonych t = 1000. Należy pamiętać, że rzeczywiste sygnał mogłyby zostać użyte do w inne miejsce na osi czasu prezentacji wyznaczone z przeznaczeniem. W tym przykładzie możliwe jest który fragment rozrzedzonych t = 1000 ma ładunek XML, czyli wstawiania ad w pozycji, która jest kilka sekund później.

    g. Ładunek fragmenty Śledź rozrzedzonych może być w różnych formatach (na przykład XML, tekst lub binarny), w zależności od scenariusza.

### <a name="redundant-audio-track"></a>Nadmiarowe ścieżki audio
W typowym HTTP adaptacyjnego przesyłania strumieniowego scenariuszu (na przykład, Smooth Streaming lub kreska) często istnieje tylko jedną ścieżkę audio w całej prezentacji. W przeciwieństwie do ścieżki wideo, które mają wiele poziomów jakości klienta do wyboru w warunkach błędu, ścieżka audio może być pojedynczym punktem awarii, jeśli wprowadzanie strumienia, który zawiera ścieżkę audio zostało przerwane. 

Aby rozwiązać ten problem, usługi Media Services obsługuje wprowadzanie na żywo z nadmiarowe ścieżki audio. Koncepcja jest, że tej samej ścieżki audio mogą być wysyłane wielokrotnie w różnych strumieni. Chociaż usługa tylko rejestruje ścieżki audio raz w manifeście klienta, może używać nadmiarowe ścieżki audio jako zapasowe pobierania audio fragmenty, jeśli głównej ścieżki audio występują problemy. Aby pozyskiwania nadmiarowe ścieżki audio, koder musi:

1. Utwórz tej samej ścieżki audio w wielu fragmentu MP4 bitstreams. Nadmiarowe ścieżki audio musi być semantycznie równoważne z tego samego fragmentu sygnatury czasowe, a wymienne poziomach nagłówka i fragmentu.
2. Upewnij się, że wpis "audio" w aktywnego serwera manifestu (sekcja 6 [1]) jest taka sama dla wszystkich nadmiarowe ścieżki audio.

Nadmiarowe ścieżki audio zaleca się wykonanie następujących:

1. Wyślij każdego unikatową ścieżkę audio w strumieniu samodzielnie. Także wysłać strumień nadmiarowe dla każdego z tych strumieni ścieżki audio, gdy drugi strumienia różni się od pierwszego tylko przez identyfikator w adresie URL HTTP POST: {protokołu} :// {adres serwera} / {publikowanie path}/Streams({identifier}) punktu.
2. Oddzielne strumienie umożliwia wysłanie dwóch najniższy wideo szybkości transmisji bitów. Każdy z tych strumieni powinien również zawierać kopię każdego unikatową ścieżkę audio. Na przykład jeśli obsługuje wiele języków, tych strumieni powinien zawierać ścieżek audio dla każdego języka.
3. Użyj wystąpienia oddzielny serwer (koder) do kodowania i wysyłania strumienia nadmiarowe wspomnianego (1) i (2). 

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
