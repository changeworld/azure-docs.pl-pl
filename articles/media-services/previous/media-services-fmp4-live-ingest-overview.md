---
title: Specyfikacja odbierania usługi Azure Media Services na żywo podzielonej zawartości w formacie MP4 | Dokumentacja firmy Microsoft
description: Tej specyfikacji opisuje protokół i format podzielonej zawartości w formacie MP4 oparte na żywo transmisji strumieniowej przyjmowanie danych z usługi Azure Media Services. Za pomocą usługi Azure Media Services do przesyłania strumieniowego wydarzeń na żywo i emisji zawartość w czasie rzeczywistym, korzystając z platformy Azure jako platformy usług w chmurze. W tym dokumencie omówiono również najlepsze wskazówki dotyczące tworzenia wysoce nadmiarowy i niezawodne na żywo pozyskiwać mechanizmami.
services: media-services
documentationcenter: ''
author: cenkdin
manager: femila
editor: ''
ms.assetid: 43fac263-a5ea-44af-8dd5-cc88e423b4de
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: cenkd;juliako
ms.openlocfilehash: b3357436d068396c5c3c4fae10ed6857759c5aed
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61221323"
---
# <a name="azure-media-services-fragmented-mp4-live-ingest-specification"></a>Specyfikacja odbierania usługi Azure Media Services na żywo podzielonej zawartości w formacie MP4 

Tej specyfikacji opisuje protokół i format podzielonej zawartości w formacie MP4 oparte na żywo transmisji strumieniowej przyjmowanie danych z usługi Azure Media Services. Media Services udostępnia usługi przesyłania strumieniowego na żywo, którego klienci mogą przeprowadzać przesyłanie strumieniowe wydarzeń na żywo i emisji zawartość w czasie rzeczywistym, korzystając z platformy Azure jako platformy usług w chmurze. W tym dokumencie omówiono również najlepsze wskazówki dotyczące tworzenia wysoce nadmiarowy i niezawodne na żywo pozyskiwać mechanizmami.

## <a name="1-conformance-notation"></a>1. Notacja zgodności
Klucz wyrazów ",""Nie może," "REQUIRED", "SHALL", "są nie może," "SHOULD", "Nie powinien," "Zalecane", "Maja," i "OPCJONALNY" w tym dokumencie mają być interpretowane tak jak opisano w dokumencie RFC 2119.

## <a name="2-service-diagram"></a>2. Diagram usług
Na poniższym diagramie przedstawiono architekturę wysokiego poziomu usługi transmisji strumieniowej na żywo w usłudze Media Services:

1. Koder na żywo wypycha aktywnych kanałów informacyjnych do kanałów, które są tworzone i aprowizowane za pomocą zestawu SDK usługi Azure Media Services.
1. Kanały, programy i punkty końcowe przesyłania strumieniowego w usłudze Media Services obsługują wszystkie funkcje na żywo przesyłania strumieniowego, w tym pozyskiwanie, formatowanie, DVR, zabezpieczenia, skalowalność i nadmiarowość w chmurze.
1. Opcjonalnie klienci mogą wybrać opcję wdrażania warstwy usługi Azure Content Delivery Network od punktu końcowego przesyłania strumieniowego i punktów końcowych klienta.
1. Strumień punktów końcowych klienta z punktu końcowego przesyłania strumieniowego przy użyciu protokołów HTTP adaptacyjnego przesyłania strumieniowego. Do przykładów należą, Microsoft Smooth Streaming i Dynamic Adaptive Streaming za pośrednictwem protokołu HTTP (DASH lub MPEG-DASH) i Apple HTTP Live Streaming (HLS).

![pozyskiwanie przepływu][image1]

## <a name="3-bitstream-format--iso-14496-12-fragmented-mp4"></a>3. Format Bitstream — ISO 14496 12 pofragmentowany plik MP4
Formatu podczas transmisji strumieniowej na żywo pozyskiwania omówionych w tym dokumencie opiera się na [ISO-14496-12]. Aby uzyskać szczegółowy opis podzielonej zawartości w formacie MP4 i rozszerzeń, zarówno dla wideo na żądanie plików i pozyskiwania transmisji strumieniowej na żywo, zobacz [[MS-SSTR]](https://msdn.microsoft.com/library/ff469518.aspx).

### <a name="live-ingest-format-definitions"></a>Pozyskiwanie na żywo format definicji
Na poniższej liście opisano specjalny format, których definicje, które są stosowane na żywo przesyłanie do usługi Azure Media Services:

1. **Ftyp zawiera informacje**, **aktywne okno manifestu serwera**, i **moov** pola muszą być wysyłane z każdym żądaniem (POST protokołu HTTP). Te pola muszą być wysyłane na początku strumienia i pozyskiwania każdym razem, gdy kodera należy nawiązać ponownie połączenie Aby wznowić działanie usługi stream. Aby uzyskać więcej informacji zobacz sekcja 6 w [1].
1. Sekcja 3.3.2 w [1] określa opcjonalne pole o nazwie **StreamManifestBox** do pozyskiwania na żywo. Z powodu logikę routingu modułu równoważenia obciążenia platformy Azure przy użyciu tego pola jest przestarzały. Pole nie powinien być obecna, gdy są wprowadzane do usługi Media Services. Jeśli to pole jest obecny, Media Services dyskretnie je ignoruje.
1. **TrackFragmentExtendedHeaderBox** pola zdefiniowane w 3.2.3.2 w [1] musi być obecny dla każdego fragmentu.
1. Wersja 2 **TrackFragmentExtendedHeaderBox** pole powinien być używany do generowania segmenty, które mają identyczne adresy URL w wielu centrach danych. Pole indeksu fragmentu jest wymagany w trybie failover dla wielu centrów danych na podstawie indeksu formatów przesyłania strumieniowego, takich jak Apple HLS i MPEG-DASH opartego na indeksie. Aby włączyć tryb failover wielu centrów danych, indeksu fragmentu muszą zostać zsynchronizowane między wieloma koderów i można zwiększyć o 1 dla każdego fragmentu nośnika kolejnych nawet w przypadku awarii lub ponownego uruchomienia kodera.
1. Sekcja 3.3.6 w [1] Określa pole o nazwie **MovieFragmentRandomAccessBox** (**mfra**), mogą być przesyłane na końcu pozyskiwania na żywo do wskazania koniec strumienia (EOS) do kanału. Z powodu logiki pozyskiwania usługi Media Services, przy użyciu EOS jest przestarzałe, a **mfra** polu pozyskiwania na żywo nie należy jej wysyłać. Jeśli wysłany, usługa Media Services dyskretnie je ignoruje. Aby zresetować stan punktem pozyskiwania, zaleca się użycie [zresetować kanał](https://docs.microsoft.com/rest/api/media/operations/channel#reset_channels). Zalecamy również użycie [Zatrzymaj Program](https://msdn.microsoft.com/library/azure/dn783463.aspx#stop_programs) prezentacji i strumienia.
1. Czas trwania fragmentu MP4 powinna być wartością stałą, aby zmniejszyć rozmiar manifesty na kliencie. Stałe MP4, czas trwania fragmentu zwiększa również heurystyki pobierania klienta za pośrednictwem Powtórz tagów. Czas trwania mogą ulegać zmianie w celu kompensacji szybkości klatek nie jest liczbą całkowitą.
1. Czas trwania fragmentu MP4 powinna należeć do zakresu od około 2 – 6 s.
1. MP4 fragmentu sygnatury czasowe i indeksów (**TrackFragmentExtendedHeaderBox** `fragment_ absolute_ time` i `fragment_index`) powinna pojawić się w kolejności rosnącej. Mimo że usługa Media Services jest odporność na zduplikowane fragmenty, ograniczył możliwości, aby zmienić kolejność fragmenty zgodnie z osi czasu nośników.

## <a name="4-protocol-format--http"></a>4. Format protokołu — HTTP
ISO pofragmentowany plik MP4, oparte na żywo pozyskiwania dla usługi Media Services używa standardowych żądania HTTP POST długotrwałych przesyłających dane multimedialne zakodowany, który jest spakowany w podzielonej zawartości w formacie MP4 do usługi. Wysyła każdego HTTP POST kompletna pofragmentowany plik MP4 bitstream ("strumień"), począwszy od początku przy użyciu pól nagłówka (**ftyp zawiera informacje**, **aktywne okno manifestu serwera**, i **moov** pola ) i kontynuować sekwencję fragmentów (**moof** i **mdat** pola). Aby składni adresu URL żądania HTTP POST zobacz sekcję 9.2 w [1]. Przykładem adresu URL przesyłania jest: 

    http://customer.channel.mediaservices.windows.net/ingest.isml/streams(720p)

### <a name="requirements"></a>Wymagania
Poniżej przedstawiono szczegółowe wymagania:

1. Koder powinni uruchomić emisji, wysyłając żądanie HTTP POST z pustą "treść" (zerowej długości) przy użyciu tego samego adresu URL pozyskiwania. Może to ułatwić kodera szybko wykryć, czy punktu końcowego pozyskiwania na żywo jest prawidłowy, a jeśli ma żadnego uwierzytelniania lub inne warunki wymagane. Dla protokołu HTTP serwer nie odesłania odpowiedź HTTP do momentu otrzymania całego żądania, w tym treść wpisu. Ze względu na charakter długotrwałych wydarzenia na żywo bez tego kroku kodera nie można wykryć wszelkie błędy, do momentu zakończenia wysyła wszystkie dane.
1. Koder musi obsługiwać błędy lub wezwań do uwierzytelnienia z powodu (1). Jeśli (1) zakończy się pomyślnie z odpowiedzi 200, będzie kontynuowane.
1. Koder musi rozpoczynać nowe żądanie HTTP POST pofragmentowany strumień MP4. Ładunek musi rozpoczynać się od pola nagłówka, następuje fragmentów. Należy pamiętać, że **ftyp zawiera informacje**, **aktywne okno manifestu serwera**, i **moov** okna (w podanej kolejności) muszą być wysyłane z każdym żądaniem, nawet wtedy, gdy kodera należy nawiązać ponownie połączenie, ponieważ poprzednie żądanie zostało zakończone przed końcem okresu strumienia. 
1. Koder należy użyć kodowanie do przekazywania, ponieważ nie jest możliwe do przewidzenia całej długości zawartości zdarzenia na żywo fragmentaryczne transferu.
1. Po zdarzeniu umieszczeniu po wysłaniu ostatni fragment kodera bez problemu zmieniała musi kończyć się transferu pakietowego kodowanie sekwencji komunikatów (większość stosów klienta HTTP określają je automatycznie). Koder musi poczekaj, aż usługa zwrócić kod ostateczną odpowiedź, a następnie Zakończ połączenie. 
1. Koder należy używaj `Events()` rzeczownik, zgodnie z opisem w do 9.2 w [1] w celu pozyskiwania na żywo do usługi Media Services.
1. Jeśli żądanie HTTP POST kończy lub upłynie limit czasu z powodu błędu protokołu TCP przed końcem okresu strumienia, kodera musi wygenerować żądanie POST, nowe przy użyciu nowego połączenia, a następnie postępuj zgodnie z poprzednim wymagania. Ponadto kodera należy wysłać ponownie poprzednie dwa fragmenty w formacie MP4, dla każdej ścieżki w strumieniu i wznowienie bez wprowadzania ciągłości na osi czasu z nośnika. Ponowne wysyłanie ostatnie dwa fragmenty w formacie MP4, dla każdej ścieżki gwarantuje, że jest bez utraty danych. Innymi słowy Jeśli strumień zawiera ścieżki wideo i audio, a bieżące żądanie POST nie powiodło się, kodera musi ponownie i Wyślij ponownie ostatnie dwa fragmenty ścieżki audio, które wcześniej zostały pomyślnie wysłane, a ostatnie dwa fragmenty filmu wideo śledzenie, które zostały wcześniej pomyślnie wysłane, aby upewnić się, że istnieje bez utraty danych. Koder, musisz utrzymywać "Prześlij dalej" bufor fragmentów nośnika, które wysłaniem po wznowieniu połączenia.

## <a name="5-timescale"></a>5. Skala czasu
[[MS-SSTR] ](https://msdn.microsoft.com/library/ff469518.aspx) w tym artykule opisano sposób użycia skali czasu dla **SmoothStreamingMedia** (sekcja 2.2.2.1) **StreamElement** (sekcja 2.2.2.3) **StreamFragmentElement** () Sekcja 2.2.2.6) i **LiveSMIL** (sekcja 2.2.7.3.1). Jeśli wartość skali czasu nie jest obecny, wartość domyślna używana jest 10 000 000 (10 MHz). Chociaż specyfikacji formatu Smooth Streaming nie blokuje użycia innych wartości skali czasu, większość implementacji kodera użyć to ustawienie domyślne wartości (10 MHz), aby wygenerować Smooth Streaming pozyskiwania danych. Ze względu na [dynamicznego pakowania multimediów platformy Azure](media-services-dynamic-packaging-overview.md) funkcji, zalecamy użycie 90 KHz Skala czasu dla strumieni wideo i 44,1 KHz lub 48.1 KHz dla strumieni audio. Jeśli wartości innego skali czasu są używane do różnych strumieni, muszą być wysyłane skali czasu poziomu usługi stream. Aby uzyskać więcej informacji, zobacz [[MS-SSTR]](https://msdn.microsoft.com/library/ff469518.aspx).     

## <a name="6-definition-of-stream"></a>6. Definicja "stream"
Stream jest podstawową jednostką operacji pozyskiwania na żywo do redagowania prezentacji na żywo, obsługę przesyłania strumieniowego trybu failover i scenariuszy nadmiarowości. Stream jest zdefiniowany jako jeden unikatowy, pofragmentowane bitstream MP4, który może zawierać pojedynczą ścieżkę lub wiele ścieżek. Prezentacji na żywo pełnej może zawierać co najmniej jeden strumień, w zależności od konfiguracji koderów na żywo. Poniższe przykłady pokazują różne opcje do redagowania prezentacji na żywo pełnej za pomocą strumieni.

**Przykład:** 

Klient chce utworzyć prezentacji na żywo przesyłania strumieniowego, który zawiera następujące różnych audio/wideo:

Wideo — 3000 KB/s, 1 500 KB/s, 750 Kb/s

Dźwięk – 128 Kb/s

### <a name="option-1-all-tracks-in-one-stream"></a>Opcja 1: Wszystkie ścieżki w jeden strumień
W przypadku tej opcji pojedynczego kodera generuje wszystkie ścieżki audio/wideo, a razem je do jednego pofragmentowane bitstream MP4. Pofragmentowane bitstream MP4 jest następnie wysyłana przez własne pojedyncze połączenie HTTP POST. W tym przykładzie istnieje tylko jeden strumień na potrzeby prezentacji na żywo.

![Śledź jeden strumieni][image2]

### <a name="option-2-each-track-in-a-separate-stream"></a>Opcja 2: Każdej ścieżki w oddzielnych strumienia
W przypadku tej opcji kodera umieszcza jedną ścieżkę z każdego fragmentu w formacie MP4 bitstream i publikuje wszystkie strumienie za pośrednictwem osobnych połączeń HTTP. Można to zrobić z użyciem kodera w jednej lub wielu koderów. Pozyskiwanie na żywo widzi ta prezentacja na żywo, zgodnie z składa się z czterech strumieni.

![Śledzi oddzielne strumienie][image3]

### <a name="option-3-bundle-audio-track-with-the-lowest-bitrate-video-track-into-one-stream"></a>Opcja 3: Pakiet ścieżki audio z najniższą Ścieżka wideo szybkości transmisji bitów w jeden strumień
W przypadku tej opcji klient zdecyduje się na pakietu ścieżki audio z Ścieżka wideo najniższej szybkości transmisji bitów w jeden fragment w formacie MP4 bitstream i pozostawić inne dwóch ścieżek wideo jako osobne strumieni. 

![Śledzi strumieni audio i wideo][image4]

### <a name="summary"></a>Podsumowanie
Nie jest to pełna lista wszystkich opcji możliwości pozyskiwania, w tym przykładzie. Jak rzeczy zgrupowania ścieżki do strumieni jest obsługiwana przez pozyskiwanie na żywo. Klienci i dostawcy kodera można wybrać własne implementacji na podstawie inżynierów złożoności, pojemność encoder i nadmiarowości i zagadnienia dotyczące trybu failover. Jednak w większości przypadków ma tylko jedną ścieżkę audio w całej prezentacji na żywo. Tak jest ważne, aby zapewnić healthiness strumienia pozyskiwania, który zawiera ścieżkę audio. Ta kwestia często powoduje umieszczenie ścieżki audio w jego własnej strumienia (jak opcja 2) lub tworzenie pakietów go przy użyciu ścieżki wideo najniższej szybkości transmisji bitów (jak opcja 3). Ponadto w przypadku lepsze nadmiarowość i odporność na uszkodzenia, wysyłanie tej samej ścieżki audio w dwóch różnych strumieni (opcja 2 z nadmiarowe ścieżki audio) lub ścieżka audio z co najmniej dwie ścieżki wideo w najniższej szybkości transmisji bitów (opcja 3 z dźwiękiem dołączone do co najmniej dwa grupowania strumieniowe transmisje wideo) zdecydowanie zaleca się na żywo przesyłanie do usługi Media Services.

## <a name="7-service-failover"></a>7. Usługi w tryb failover
Podany rodzaj transmisji strumieniowej na żywo obsługę trybu failover dobre jest krytyczne znaczenie dla zapewnienia dostępności usługi. Usługa Media Services jest przeznaczony do obsługi różnych typów błędów, takich jak błędy sieci, błędy serwera i problemy związane z magazynowaniem. Gdy jest używana w połączeniu z logiką właściwego trybu failover po stronie koder na żywo, klienci mogą osiągnąć wysoce niezawodne usługi transmisji strumieniowej na żywo w chmurze.

W tej sekcji omówiono scenariusze trybu failover usługi. W tym przypadku awaria występuje gdzieś w ramach usługi i ujawnia się jako błąd sieci. Poniżej przedstawiono kilka zaleceń dla implementacji kodera do obsługi usługi w tryb failover:

1. Na użytek 10 sekund limitu czasu podczas nawiązywania połączenia TCP. Jeśli próba nawiązania połączenia trwa dłużej niż 10 sekund, przerwać operację i spróbuj ponownie. 
1. Na użytek krótki limit czasu wysyłania fragmentów komunikatu żądania HTTP. Jeśli czas trwania fragmentu MP4 docelowy jest N sekund, użyj limit czasu wysyłania, od N do 2 sekund N; na przykład jeśli czas trwania fragmentu MP4 6 w ciągu kilku sekund, należy użyć 6 do 12 sekund limitu czasu. Jeśli zostanie przekroczony limit czasu, zresetowania połączenia, Otwórz nowe połączenie, a następnie Wznów strumienia pozyskiwania przy nowym połączeniu. 
1. Obsługa stopniowego buforu, który zawiera ostatnie dwa fragmenty dla każdej ścieżki, które zostały pomyślnie i całkowicie wysłane do usługi.  Czy żądanie HTTP POST dla strumienia jest zakończony przed upływem limitu czasu koniec strumienia, Otwórz nowe połączenie i rozpocząć kolejnego żądania HTTP POST, ponowne wysyłanie nagłówków strumienia, Wyślij ponownie ostatnich dwóch fragmentów dla każdej ścieżki i wznowić strumienia bez wprowadzania d iscontinuity na osi czasu z nośnika. Zmniejsza to ryzyko utraty danych.
1. Zaleca się, że koder nie ogranicza liczby ponownych prób nawiązania połączenia lub wznowić przesyłania strumieniowego po wystąpieniu błędu TCP.
1. Po błędzie TCP:
  
    a. Bieżące połączenie musi być zamknięty, a dla nowych żądań POST protokołu HTTP należy utworzyć nowe połączenie.

    b. Nowe HTTP POST adres URL musi być taka sama jak początkowy adres URL wpisu.
  
    c. Nowe HTTP POST musi zawierać nagłówki strumienia (**ftyp zawiera informacje**, **aktywne okno manifestu serwera**, i **moov** pola), które są identyczne z nagłówków strumień z wpisu w początkowej.
  
    d. Należy ponownie wysłać fragmenty dwa ostatnie wysłane dla każdej ścieżki i przesyłania strumieniowego musi wznowienie bez wprowadzania ciągłości na osi czasu z nośnika. Sygnatury czasowe fragmentu MP4 może się zwiększać w sposób ciągły, nawet w przypadku żądania POST protokołu HTTP.
1. Koder należy zakończyć żądań POST protokołu HTTP, jeśli dane nie są wysyłane z szybkością proporcjonalnie do czasu trwania fragmentu MP4.  Żądanie HTTP POST, które nie wysyłają dane można zapobiec szybko odłączanie kodera w przypadku aktualizacji usługi Media Services. Z tego powodu HTTP POST dla rozrzedzone ścieżki (sygnale reklamowym) powinna być krótkotrwały i przerywa zaraz po rozrzedzone fragment jest wysyłany.

## <a name="8-encoder-failover"></a>8. Koder trybu failover
Koder w tryb failover jest drugi typ scenariusza trybu failover, który należy wykonać w celu dostarczania przez przesyłanie strumieniowe na żywo end-to-end. W tym scenariuszu wystąpi błąd po stronie kodera. 

![Koder trybu failover][image5]

Następujące oczekiwania stosuje się z punktu końcowego pozyskiwania na żywo w przypadku pracy awaryjnej kodera:

1. Nowe wystąpienie kodera należy utworzyć aby kontynuować, przesyłanie strumieniowe, jak pokazano na diagramie (Stream k 3000 wystąpili linia przerywana).
1. Nowa usługa encoder, musisz użyć tego samego adresu URL dla żądań POST protokołu HTTP jako zakończone niepowodzeniem wystąpienie.
1. Koder nowe żądanie POST musi zawierać pola tego samego nagłówka MP4 pofragmentowane jako zakończone niepowodzeniem wystąpienie.
1. Nowa usługa encoder muszą zostać prawidłowo zsynchronizowane z wszystkich innych uruchomionych koderów dla tej samej prezentacji na żywo do generowania zsynchronizowanych próbki audio/wideo o granicach wyrównany fragmentu.
1. Nowy strumień musi być semantycznie równoważne z poprzednim strumienia i wymienne na poziomach nagłówka i fragment.
1. Nowa usługa encoder, należy spróbować zminimalizować ryzyko utraty danych. `fragment_absolute_time` i `fragment_index` nośnika fragmenty należy zwiększyć od punktu, w którym ostatnio zatrzymano kodera. `fragment_absolute_time` i `fragment_index` należy zwiększyć w sposób ciągły, ale dozwolone jest wprowadzenie ciągłości, jeśli to konieczne. Usługi Media Services ignoruje fragmenty, które ma już odbierane i przetwarzane, dlatego zaleca się err obok ponowne wysyłanie fragmentów, niż można wprowadzać przerw na osi czasu z nośnika. 

## <a name="9-encoder-redundancy"></a>9. Nadmiarowość kodera
Krytyczne dla niektórych wydarzeń na żywo, wymagają jeszcze większej dostępności i jakości obsługi, zaleca się użycie aktywne aktywne nadmiarowych koderów w celu uzyskania płynnego trybu failover bez utraty danych.

![Nadmiarowość kodera][image6]

Jak pokazano na poniższym diagramie, dwie grupy koderów wypchnąć dwie kopie każdego strumienia równocześnie do usługi na żywo. Ta konfiguracja jest obsługiwana, ponieważ usługa Media Services można odfiltrować zduplikowane fragmenty, oparte na sygnatury czasowej identyfikator i fragment strumienia. Wynikowy strumień na żywo i archiwum jest pojedynczej kopii wszystkich strumieni, który jest najlepsze możliwe agregacji z dwóch źródeł. Na przykład w hipotetyczny przypadków, tak długo, jak istnieje jeden koder (nie musi być taki sam) jest uruchomiona w dowolnym danym momencie w czasie dla każdego strumienia, wynikowy strumień na żywo z usługi jest ciągłe bez utraty danych. 

Wymagania dotyczące tego scenariusza są prawie taki sam jak wymagania w przypadku ""kodera Praca awaryjna, z wyjątkiem drugi zestaw koderów uruchomionych równocześnie koderów podstawowego.

## <a name="10-service-redundancy"></a>10. Nadmiarowość usług
Dla bardzo nadmiarowe dystrybucji globalnej czasami konieczne jest posiadanie między regionami kopii zapasowej w celu obsługi awarii regionalnej. Rozszerzając topologii "Kodera nadmiarowości", klienci mogą wybrać opcję ma nadmiarowe usługi w różnych regionach, która jest połączona z drugiego zestawu koderów. Klienci mogą również pracować z dostawcą Content Delivery Network do wdrożenia globalnego Traffic Manager przed wdrożeniami usług dwóch bezproblemowo kierować ruch klientów. Wymagania dotyczące koderów są takie same jak w przypadku "Kodera nadmiarowości". Jedynym wyjątkiem jest, że drugi zestaw koderów konieczna będzie wskazywał na żywo z innym pozyskiwania punktu końcowego. Na poniższym diagramie przedstawiono tej konfiguracji:

![Nadmiarowość usług][image7]

## <a name="11-special-types-of-ingestion-formats"></a>11. Specjalne typy formatów pozyskiwania
W tej sekcji omówiono typy specjalne formaty pozyskiwania na żywo, które są przeznaczone do obsługi określonych scenariuszy.

### <a name="sparse-track"></a>Śledź rozrzedzone
Podczas dostarczania prezentacji przesyłania strumieniowego na żywo przy użyciu środowiska rozbudowanych aplikacji klienckich, często jest to niezbędne do przekazywania zdarzenia zsynchronizowany czas lub sygnalizuje wewnątrzpasmowe przy użyciu danych głównym nośnika. Na przykład jest wstawiania reklam na żywo dynamicznych. Sygnalizowanie zdarzenia tego typu różni się od regularnych audio/wideo, przesyłanie strumieniowe ze względu na ich charakter rozrzedzone. Innymi słowy sygnalizowanie danych zwykle nie odbywa się stale i interwał może być trudna do przewidzenia. Pojęcie rozrzedzone śledzenie został zaprojektowany w celu pozyskiwania i emisji wewnątrzpasmowe sygnalizowanie danych.

Poniższe kroki są zalecane wdrożenia służy do wprowadzania rozrzedzone Śledź:

1. Utwórz oddzielne pofragmentowane bitstream MP4, zawierający tylko rozrzedzone ścieżki, bez ścieżki audio/wideo.
1. W **aktywne okno manifestu serwera** zgodnie z definicją w sekcji 6, w [1], należy użyć *parentTrackName* parametru do określenia nazwy ścieżki nadrzędnej. Aby uzyskać więcej informacji zobacz sekcję 4.2.1.2.1.2 w [1].
1. W **aktywne okno manifestu serwera**, **manifestOutput** musi być równa **true**.
1. Ze względu na charakter rozrzedzone sygnalizowanie zdarzenia, zaleca się następujące czynności:
   
    a. Na początku zdarzenia na żywo, kodera wysyła pola nagłówka początkowej do usługi, która umożliwia usługa do zarejestrowania rozrzedzone śledzenie w manifeście klienta.
   
    b. Koder należy zakończyć żądania HTTP POST, gdy dane nie są wysyłane. Długotrwałe POST protokołu HTTP, które nie wysyłają dane można zapobiec szybko odłączanie kodera w przypadku ponownego uruchomienia aktualizacji lub serwer usługi Media Services. W takich przypadkach serwer multimediów jest tymczasowo zablokowany w ramach operacji odbierania dla gniazda.
   
    c. W czasie gdy sygnalizowanie dane nie są dostępne kodera SHOULD Zamknij POST protokołu HTTP żądania. Gdy żądanie POST jest aktywne, kodera powinien wysyłać dane.

    d. Podczas wysyłania rozrzedzone fragmentów, kodera można ustawić jawne nagłówek content-length, jeśli jest ona dostępna.

    e. Wysyłając rozrzedzone fragmentów z nowym połączeniem z kodera na początek wysyłania z pola nagłówka, następuje nowych fragmentów. Są to przypadki, w których pracy awaryjnej się stanie, wewnętrzne i na nowy serwer, który nie znalazła rozrzedzone śledzenie przed ustanawiane jest połączenie. rozrzedzone nowe.

    f. Fragment rozrzedzone śledzenie staje się dostępnych dla klienta, gdy odpowiedni fragment śledzenie nadrzędnego, który ma wartość sygnatury czasowej samym lub większym ma zostać udostępnione do klienta. Na przykład, jeśli rozrzedzone fragment zawiera sygnaturę czasową t = 1000, oczekuje się, że po klient zobaczy "wideo" (przy założeniu nazwa ścieżki nadrzędnej "wideo") fragmentu znacznik czasu: 1000 lub, można pobrać t rozrzedzone fragmentu = 1000. Należy pamiętać, że rzeczywiste sygnału może służyć do innej pozycji na osi czasu prezentacji do ich wyznaczonego celu. W tym przykładzie jest to możliwe, rozrzedzone fragment t = 1000 ma ładunek XML, czyli do wstawiania usługi ad w stanie, który jest kilka sekund później.

    g. Ładunek fragmenty rozrzedzone śledzenie może być w różnych formatach (takich jak XML, tekstowo lub binarnie), w zależności od scenariusza.

### <a name="redundant-audio-track"></a>Nadmiarowe ścieżki audio
W typowym HTTP adaptacyjnego przesyłania strumieniowego scenariuszu (na przykład, Smooth Streaming lub KRESKI) często ma tylko jedną ścieżkę audio w całej prezentacji. W przeciwieństwie do ścieżki wideo, które mają wiele poziomów jakości dla klienta, które można wybierać w warunkach błędu, ścieżki audio może być pojedynczym punktem awarii, jeśli pozyskiwanie strumienia, który zawiera ścieżkę audio jest uszkodzona. 

Aby rozwiązać ten problem, Media Services obsługuje pozyskiwanie na żywo z nadmiarowe ścieżki audio. Chodzi o to, że tej samej ścieżki audio mogą być wysyłane wielokrotnie w różnych strumieni. Mimo że usługa tylko rejestruje ścieżkę audio raz w manifeście klienta, może używać nadmiarowe ścieżki audio jako zapasowe pobierania fragmenty audio, jeśli głównej ścieżki audio ma problemy. Aby pozyskiwać nadmiarowe ścieżki audio, kodera musi:

1. Utworzenie tej samej ścieżki audio w wielu fragmentu MP4 bitstreams. Nadmiarowe ścieżki audio musi być semantycznie równoważne z tej samej sygnatury czasowe fragmentu i musi być wymienne na poziomach nagłówka i fragment.
1. Upewnij się, że wpis "audio" w aktywnego serwera manifestu (sekcja 6 [1]) jest taka sama dla wszystkich nadmiarowe ścieżki audio.

Następującą implementacją zaleca się nadmiarowe ścieżki audio:

1. Wyślij Każda unikatowa ścieżka audio w strumieniu przez siebie. Także wysłać strumień nadmiarowe dla każdego z tych strumieni ścieżki audio, gdy drugi strumienia różni się od pierwszego tylko przez identyfikator w adresie URL POST protokołu HTTP: {protokołu} :// {adres serwera} / {publikowanie path}/Streams({identifier}) punktu.
1. Wysłać dwa najniższej szybkości transmisji wideo za pomocą osobnych strumieni. Każda z tych strumieni powinien też zawierać kopię każdego unikatową ścieżkę audio. Na przykład gdy obsługuje wiele języków, te strumienie może zawierać ścieżki audio, dla każdego języka.
1. Korzystać z wystąpień oddzielny serwer (koder), kodowanie i wysyłanie nadmiarowe strumienie wymienionych w (1) i (2). 

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
