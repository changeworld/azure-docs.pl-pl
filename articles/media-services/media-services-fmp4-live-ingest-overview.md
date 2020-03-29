---
title: Pofragmentowana specyfikacja pozyskiwania plików MP4 w usłudze Azure Media Services | Dokumenty firmy Microsoft
description: W tej specyfikacji opisano protokół i format pofragmentowanego pozyskiwania strumieniowego przesyłania strumieniowego na żywo opartego na formacie MP4 dla usługi Azure Media Services. W tym dokumencie omówiono również najlepsze rozwiązania dotyczące tworzenia wysoce nadmiarowych i niezawodnych mechanizmów pozyskiwania na żywo.
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
ms.author: juliako
ms.openlocfilehash: 507afad294e8233ea4de4130795f29925870fcdf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74888057"
---
# <a name="azure-media-services-fragmented-mp4-live-ingest-specification"></a>Pofragmentowana specyfikacja pozyskiwania na żywo w usłudze Azure Media Services na żywo w usłudze MP4 

W tej specyfikacji opisano protokół i format pofragmentowanego pozyskiwania strumieniowego przesyłania strumieniowego na żywo opartego na formacie MP4 dla usługi Azure Media Services. Usługa Media Services zapewnia usługę przesyłania strumieniowego na żywo, której klienci mogą używać do strumieniowego przesyłania wydarzeń na żywo i emisji zawartości w czasie rzeczywistym przy użyciu platformy Azure jako platformy w chmurze. W tym dokumencie omówiono również najlepsze rozwiązania dotyczące tworzenia wysoce nadmiarowych i niezawodnych mechanizmów pozyskiwania na żywo.

## <a name="1-conformance-notation"></a>1. Notacja zgodności
Słowa kluczowe "MUSI", "NIE WOLNO", "WYMAGANE", "NIE", "NIE POWINIEN", "NIE POWINIEN", "ZALECANE", "MOŻE" i "FAKULTATYWNE" w tym dokumencie należy interpretować tak, jak opisano w RFC 2119.

## <a name="2-service-diagram"></a>2. Schemat serwisowy
Na poniższym diagramie przedstawiono architekturę wysokiego poziomu usługi przesyłania strumieniowego na żywo w usłudze Media Services:

1. Koder na żywo wypycha źródła danych na żywo do kanałów, które są tworzone i aprowizowane za pośrednictwem narzędzia Azure Media Services SDK.
1. Kanały, programy i punkty końcowe przesyłania strumieniowego w usłudze Media Services obsługują wszystkie funkcje przesyłania strumieniowego na żywo, w tym łowianie, formatowanie, rejestrator dvr w chmurze, zabezpieczenia, skalowalność i nadmiarowość.
1. Opcjonalnie klienci mogą wybrać wdrożenie warstwy sieci dostarczania zawartości platformy Azure między punktem końcowym przesyłania strumieniowego a punktami końcowymi klienta.
1. Punkty końcowe klienta strumień z punktu końcowego przesyłania strumieniowego przy użyciu protokołów http adaptive streaming. Przykłady obejmują Microsoft Smooth Streaming, Dynamiczne adaptacyjne przesyłanie strumieniowe przez HTTP (DASH lub MPEG-DASH) i Apple HTTP Live Streaming (HLS).

![przepływ połknienia][image1]

## <a name="3-bitstream-format--iso-14496-12-fragmented-mp4"></a>3. Format bitstream - ISO 14496-12 rozdrobnione MP4
Format przewodowy do pozyskiwania na żywo omówiony w tym dokumencie jest oparty na [ISO-14496-12]. Szczegółowe wyjaśnienie rozdrobnionych formatów i rozszerzeń MP4 zarówno dla plików wideo na żądanie, jak i pozyskiwania na żywo, zobacz [[MS-SSTR]](https://msdn.microsoft.com/library/ff469518.aspx).

### <a name="live-ingest-format-definitions"></a>Definicje formatów pozyskiwania na żywo
Na poniższej liście opisano definicje formatów specjalnych, które mają zastosowanie do pozyskiwania na żywo w usłudze Azure Media Services:

1. **Ftyp**, **Live Server Manifest Box**i pola **moov** muszą być wysyłane z każdym żądaniem (HTTP POST). Te pola muszą być wysyłane na początku strumienia i za każdym razem koder musi ponownie połączyć się, aby wznowić pozyskiwania strumienia. Aby uzyskać więcej informacji, zobacz sekcję 6 w [1].
1. Sekcja 3.3.2 w [1] definiuje opcjonalne pole o nazwie **StreamManifestBox** do pozyskiwania na żywo. Ze względu na logikę routingu modułu równoważenia obciążenia platformy Azure przy użyciu tego pola jest przestarzałe. Pole NIE POWINNO być obecne podczas pozyskiwania do usługi Media Services. Jeśli to pole jest obecne, program Media Services po cichu je ignoruje.
1. **Pole TrackFragmentExtendedHeaderBox** zdefiniowane w 3.2.3.2 w [1] MUSI być obecne dla każdego fragmentu.
1. Wersja 2 **trackfragmentExtendedHeaderBox** pole powinny być używane do generowania segmentów nośników, które mają identyczne adresy URL w wielu centrach danych. Pole indeksu fragmentu jest wymagane dla trybu failover między centrami danych formatów przesyłania strumieniowego opartych na indeksach, takich jak Apple HLS i oparty na indeksie MPEG-DASH. Aby włączyć między centrami danych pracy awaryjnej, indeks fragmentu musi być zsynchronizowany między wieloma koderami i zwiększyć o 1 dla każdego kolejnego fragmentu nośnika, nawet w przypadku ponownego uruchomienia kodera lub awarii.
1. Sekcja 3.3.6 w [1] definiuje pole o nazwie **MovieFragmentRandomAccessBox** (**mfra**), które może być wysyłane na końcu spożycia na żywo, aby wskazać koniec strumienia (EOS) do kanału. Ze względu na logikę pozyskiwania usługi Media Services przy użyciu usługi EOS jest przestarzałe, a pole **mfra** do pozyskiwania na żywo nie powinny być wysyłane. Jeśli zostanie wysłany, media services po cichu ignoruje go. Aby zresetować stan punktu pojania, zaleca się użycie funkcji [Resetowanie kanału](https://docs.microsoft.com/rest/api/media/operations/channel#reset_channels). Zaleca się również użycie [funkcji Zatrzymywania programu](https://msdn.microsoft.com/library/azure/dn783463.aspx#stop_programs) w celu zakończenia prezentacji i strumienia.
1. Czas trwania fragmentu MP4 powinien być stały, aby zmniejszyć rozmiar manifestów klienta. Stały czas trwania fragmentu MP4 poprawia również heurystykę pobierania klienta za pomocą tagów powtarzających. Czas trwania MOŻE się zmieniać, aby skompensować liczbę klatek niekłędziłowych.
1. Czas trwania fragmentu MP4 powinien wynosić od około 2 do 6 sekund.
1. Sygnatury czasowe i indeksy fragmentu MP4 `fragment_index`**(TrackFragmentExtendedHeaderBox** `fragment_ absolute_ time` i ) powinny zostać zrealizowane w kolejności rosnącej. Mimo że usługi Media Services jest odporny na zduplikowane fragmenty, ma ograniczoną możliwość ponownego kolejności fragmentów zgodnie z osi czasu nośnika.

## <a name="4-protocol-format--http"></a>4. Format protokołu – HTTP
Pofragmentowane na żywo źródło danych MP4 w usłudze Media Services używa standardowego długotrwałego żądania HTTP POST do przesyłania do usługi zakodowanych danych multimedialnych spakowanych w pofragmentowanym formacie MP4. Każdy protokół HTTP POST wysyła kompletny, pofragmentowany strumień bitowy MP4 ("strumień"), zaczynając od początku od pól**nagłówka (ftyp**, **Live Server Manifest Box**i **moov** boxy) i kontynuując sekwencję fragmentów (pola**moof** i **mdat).** Aby uzyskać składnię adresu URL dla żądania HTTP POST, zobacz sekcję 9.2 w [1]. Przykładem adresu URL POST jest: 

    http://customer.channel.mediaservices.windows.net/ingest.isml/streams(720p)

### <a name="requirements"></a>Wymagania
Oto szczegółowe wymagania:

1. Koder powinien rozpocząć emisję, wysyłając żądanie HTTP POST z pustą "treścią" (zerową długością zawartości) przy użyciu tego samego adresu URL pozyskiwania. Może to pomóc koderowi szybko wykryć, czy punkt końcowy pozyskiwania na żywo jest prawidłowy i czy wymagane jest uwierzytelnianie lub inne warunki. Zgodnie z protokołem HTTP serwer nie może odesłać odpowiedzi HTTP, dopóki nie zostanie odebrane całe żądanie, w tym treść POST. Biorąc pod uwagę długotrwały charakter zdarzenia na żywo, bez tego kroku koder może nie być w stanie wykryć żadnego błędu, dopóki nie zakończy wysyłania wszystkich danych.
1. Koder MUSI obsługiwać wszelkie błędy lub problemy z uwierzytelnianiem z powodu (1). Jeśli (1) zakończy się powodzeniem z odpowiedzią 200, kontynuuj.
1. Koder musi uruchomić nowe żądanie HTTP POST z pofragmentowanym strumieniem MP4. Ładunek musi zaczynać się od pól nagłówka, a następnie fragmentów. Należy zauważyć, że **ftyp**, **Live Server Manifest Box**i pola **moov** (w tej kolejności) MUSZĄ być wysyłane z każdym żądaniem, nawet jeśli koder musi ponownie połączyć, ponieważ poprzednie żądanie zostało zakończone przed końcem strumienia. 
1. Koder musi używać kodowania transferu fragmentowego do przekazywania, ponieważ nie można przewidzieć całej długości zawartości zdarzenia na żywo.
1. Po zakończeniu zdarzenia, po wysłaniu ostatniego fragmentu koder MUSI bezpiecznie zakończyć sekwencję komunikatów kodowania transferu fragmentarycznego (większość stosów klienta HTTP obsługuje go automatycznie). Koder MUSI czekać na usługę, aby zwrócić kod odpowiedzi końcowej, a następnie zakończyć połączenie. 
1. Koder NIE MOŻE używać `Events()` rzeczownika opisanego w 9.2 in [1] do pozyskiwania na żywo do usługi Media Services.
1. Jeśli żądanie HTTP POST kończy się lub upływa limit czasu z błędem TCP przed końcem strumienia, koder musi wydać nowe żądanie POST przy użyciu nowego połączenia i postępować zgodnie z poprzednimi wymaganiami. Ponadto koder MUSI ponownie wysłać poprzednie dwa fragmenty MP4 dla każdej ścieżki w strumieniu i wznowić bez wprowadzania nieciągłości na osi czasu nośnika. Ponowne wysłanie dwóch ostatnich fragmentów MP4 dla każdej ścieżki gwarantuje, że nie ma utraty danych. Innymi słowy, jeśli strumień zawiera zarówno ścieżkę audio, jak i wideo, a bieżące żądanie POST nie powiedzie się, koder musi ponownie połączyć i ponownie wysłać dwa ostatnie fragmenty ścieżki audio, które zostały wcześniej wysłane, oraz dwa ostatnie fragmenty wideo utwór, który został wcześniej pomyślnie wysłany, aby upewnić się, że nie ma utraty danych. Koder MUSI utrzymywać bufor "do przodu" fragmentów nośnika, który jest ponownie wyzywalany po ponownym połączeniu.

## <a name="5-timescale"></a>5. Skala czasu
[[MS-SSTR]](https://msdn.microsoft.com/library/ff469518.aspx) opisuje wykorzystanie skali czasu dla **SmoothStreamingMedia** (sekcja 2.2.2.1), **StreamElement** (sekcja 2.2.2.3), **StreamFragmentElement** (sekcja 2.2.2.6) i **LiveSMIL** (sekcja 2.2.7.3.1). Jeśli wartość skali czasu nie jest obecna, używana wartość domyślna to 10 000 000 (10 MHz). Chociaż specyfikacja formatu Płynna transmisja strumieniowa nie blokuje użycia innych wartości skali czasu, większość implementacji kodera używa tej wartości domyślnej (10 MHz) do generowania płynnych danych pozyskiwania przesyłania strumieniowego. Ze względu na funkcję [azure media dynamiczne pakowanie,](media-services-dynamic-packaging-overview.md) zaleca się użycie skali czasu 90 KHz dla strumieni wideo i 44,1 KHz lub 48,1 KHz dla strumieni audio. Jeśli dla różnych strumieni są używane różne wartości skali czasu, musi zostać wysłana skala czasu na poziomie strumienia. Aby uzyskać więcej informacji, zobacz [[MS-SSTR]](https://msdn.microsoft.com/library/ff469518.aspx).     

## <a name="6-definition-of-stream"></a>6. Definicja "strumienia"
Strumień jest podstawową jednostką działania w pozyskiwania na żywo do tworzenia prezentacji na żywo, obsługi przesyłania strumieniowego w trybie failover i scenariuszy nadmiarowości. Strumień jest zdefiniowany jako jeden unikatowy, pofragmentowany strumień bitów MP4, który może zawierać jedną ścieżkę lub wiele ścieżek. Pełna prezentacja na żywo może zawierać jeden lub więcej strumieni, w zależności od konfiguracji koderów na żywo. Poniższe przykłady ilustrują różne opcje używania strumieni do tworzenia pełnej prezentacji na żywo.

**Przykład:** 

Klient chce utworzyć prezentację przesyłania strumieniowego na żywo, która zawiera następujące szybkości transmisji bitów audio/wideo:

Wideo – 3000 kb/s, 1500 kb/s, 750 kbps

Dźwięk – 128 kb/s

### <a name="option-1-all-tracks-in-one-stream"></a>Opcja 1: Wszystkie utwory w jednym strumieniu
W tej opcji pojedynczy koder generuje wszystkie ścieżki audio/wideo, a następnie łączy je w jeden rozdrobniony strumień bitowy MP4. Pofragmentowany strumień bitów MP4 jest następnie wysyłany za pośrednictwem jednego połączenia HTTP POST. W tym przykładzie istnieje tylko jeden strumień dla tej prezentacji na żywo.

![Ścieżka streamów-jeden][image2]

### <a name="option-2-each-track-in-a-separate-stream"></a>Opcja 2: Każdy utwór w osobnym strumieniu
W tej opcji koder umieszcza jedną ścieżkę w każdym strumieniu bitów MP4 fragmentu, a następnie księguje wszystkie strumienie za pośrednictwem oddzielnych połączeń HTTP. Można to zrobić za pomocą jednego kodera lub wielu koderów. Na żywo pozyskiwania widzi tej prezentacji na żywo jako składa się z czterech strumieni.

![Utwory oddzielone strumieniami][image3]

### <a name="option-3-bundle-audio-track-with-the-lowest-bitrate-video-track-into-one-stream"></a>Opcja 3: Zestaw ścieżki dźwiękowej z najniższą szybkością transmisji bitów w jednym strumieniu
W tej opcji klient zdecyduje się połączyć ścieżkę audio z ścieżką wideo o najniższej szybkości transmisji bitów w jednym strumieniu bitowym MP4 fragmentu i pozostawić pozostałe dwie ścieżki wideo jako oddzielne strumienie. 

![Strumienie-ścieżki audio i wideo][image4]

### <a name="summary"></a>Podsumowanie
Nie jest to wyczerpująca lista wszystkich możliwych opcji pozyskiwania w tym przykładzie. W rzeczywistości, każda grupa utworów w strumienie jest wspierana przez na żywo spożycia. Klienci i dostawcy koderów mogą wybierać własne implementacje na podstawie złożoności inżynierskiej, pojemności kodera oraz zagadnień nadmiarowości i pracy awaryjnej. Jednak w większości przypadków istnieje tylko jedna ścieżka audio dla całej prezentacji na żywo. Dlatego ważne jest, aby zapewnić zdrowotność strumienia połykającego, który zawiera ścieżkę audio. Ta uwaga często skutkuje umieszczeniem ścieżki dźwiękowej we własnym strumieniu (jak w opcji 2) lub połączeniem jej z ścieżką wideo o najniższej szybkości transmisji bitów (jak w opcji 3). Ponadto, aby uzyskać lepszą redundancję i odporność na uszkodzenia, wysyłając tę samą ścieżkę audio w dwóch różnych strumieniach (opcja 2 z nadmiarowymi ścieżkami audio) lub łącząc ścieżkę audio z co najmniej dwoma ścieżkami wideo o najniższej szybkości transmisji bitów (opcja 3 z dźwiękiem w pakiecie w co najmniej dwóch strumieni wideo) jest wysoce zalecany do pozyskiwania na żywo do usługi Media Services.

## <a name="7-service-failover"></a>7. Usługa awaryjna
Biorąc pod uwagę charakter przesyłania strumieniowego na żywo, dobra obsługa pracy awaryjnej ma kluczowe znaczenie dla zapewnienia dostępności usługi. Usługa Media Services jest przeznaczona do obsługi różnych typów awarii, w tym błędów sieciowych, błędów serwera i problemów z magazynowaniem. W połączeniu z właściwą logiką pracy awaryjnej od strony kodera na żywo klienci mogą uzyskać wysoce niezawodną usługę przesyłania strumieniowego na żywo z chmury.

W tej sekcji omówimy scenariusze pracy awaryjnej usługi. W takim przypadku błąd dzieje się gdzieś w usłudze i objawia się jako błąd sieci. Oto kilka zaleceń dotyczących implementacji kodera do obsługi pracy awaryjnej usługi:

1. Do ustanowienia połączenia TCP użyj 10-sekundowego limitu czasu. Jeśli próba nawiązania połączenia trwa dłużej niż 10 sekund, należy przerwać operację i spróbuj ponownie. 
1. Użyj krótkiego limitu czasu do wysyłania fragmentów wiadomości żądania HTTP. Jeśli docelowy czas trwania fragmentu MP4 wynosi N sekund, użyj limitu czasu wysyłania między N a 2 N sekund; na przykład jeśli czas trwania fragmentu MP4 wynosi 6 sekund, użyj limitu czasu od 6 do 12 sekund. Jeśli wystąpi limit czasu, zresetuj połączenie, otwórz nowe połączenie i wznowij przesyłanie strumienia na nowe połączenie. 
1. Obsługa buforu stopniowego, który ma dwa ostatnie fragmenty dla każdej ścieżki, które zostały pomyślnie i całkowicie wysłane do usługi.  Jeśli żądanie HTTP POST dla strumienia zostało zakończone lub limit czasu przed końcem strumienia, otwórz nowe połączenie i rozpocznij kolejne żądanie HTTP POST, wyślij ponownie nagłówki strumienia, wyślij ponownie dwa ostatnie fragmenty dla każdej ścieżki i wznowij strumień bez wprowadzania nieciągłości na osi czasu mediów. Zmniejsza to ryzyko utraty danych.
1. Zaleca się, że koder NIE ogranicza liczbę ponownych prób ustanowienia połączenia lub wznowienia przesyłania strumieniowego po wystąpieniu błędu TCP.
1. Po błędzie TCP:
  
    a. Bieżące połączenie MUSI zostać zamknięte, a dla nowego żądania HTTP POST musi zostać utworzone nowe połączenie.

    b. Nowy adres URL HTTP POST MUSI być taki sam jak początkowy adres URL POST.
  
    d. Nowy wpis HTTP musi zawierać nagłówki**strumienia (ftyp**, **Pole manifestu serwera na żywo**i pola **moov),** które są identyczne z nagłówkami strumienia w początkowym POST.
  
    d. Ostatnie dwa fragmenty wysłane dla każdej ścieżki muszą być ponownie wysłane, a przesyłanie strumieniowe musi zostać wznowione bez wprowadzania nieciągłości na osi czasu nośnika. Sygnatury czasowe fragmentu MP4 muszą stale rosnąć, nawet w przypadku żądań HTTP POST.
1. Koder powinien zakończyć żądanie HTTP POST, jeśli dane nie są wysyłane z szybkością proporcjonalną do czasu trwania fragmentu MP4.  Żądanie HTTP POST, które nie wysyła danych, może uniemożliwić programowi Media Services szybkie odłączanie się od kodera w przypadku aktualizacji usługi. Z tego powodu ślady HTTP POST dla rzadkich (sygnału reklamy) powinny być krótkotrwałe, kończące się natychmiast po wysłaniu rzadkiego fragmentu.

## <a name="8-encoder-failover"></a>8. Przełącznik awaryjny edcoder
Koder pracy awaryjnej jest drugim rodzajem scenariusza pracy awaryjnej, który musi zostać rozwiązany dla end-to-end dostarczania przesyłania strumieniowego na żywo. W tym scenariuszu warunek błędu występuje po stronie kodera. 

![koder pracy awaryjnej][image5]

Następujące oczekiwania mają zastosowanie z punktu końcowego pozyskiwania na żywo, gdy koder pracy awaryjnej się dzieje:

1. Nowe wystąpienie kodera powinny zostać utworzone w celu kontynuowania przesyłania strumieniowego, jak pokazano na diagramie (Strumień dla wideo 3000k, z linią przerywaną).
1. Nowy koder MUSI używać tego samego adresu URL dla żądań HTTP POST, co wystąpienie, które nie powiodło się.
1. Żądanie post nowego kodera musi zawierać te same pofragmentowane pola nagłówka MP4, co wystąpienie, które nie powiodło się.
1. Nowy koder MUSI być poprawnie zsynchronizowany ze wszystkimi innymi uruchomionymi koderami dla tej samej prezentacji na żywo, aby wygenerować zsynchronizowane próbki audio/wideo z wyrównanymi granicami fragmentu.
1. Nowy strumień MUSI być semantycznie równoważne z poprzedniego strumienia i wymienne na poziomach nagłówka i fragmentu.
1. Nowy koder powinien próbować zminimalizować utratę danych. I `fragment_absolute_time` `fragment_index` fragmenty nośnika powinny wzrosnąć od punktu, w którym koder ostatnio zatrzymany. I `fragment_absolute_time` `fragment_index` powinien rosnąć w sposób ciągły, ale jest dopuszczalne wprowadzenie nieciągłości, jeśli to konieczne. Usługi Media Services ignoruje fragmenty, które zostały już odebrane i przetworzone, więc lepiej jest błądzić po stronie ponownego wysłania fragmentów niż wprowadzać nieciągłości na osi czasu mediów. 

## <a name="9-encoder-redundancy"></a>9. Nadmiarowość kodera
W przypadku niektórych krytycznych zdarzeń na żywo, które wymagają jeszcze wyższej dostępności i jakości środowiska, zalecamy użycie aktywnych zbędnych koderów, aby osiągnąć bezproblemową pracę awaryjną bez utraty danych.

![nadmiarowość cekodera][image6]

Jak pokazano na tym diagramie, dwie grupy koderów wypychają dwie kopie każdego strumienia jednocześnie do usługi na żywo. Ta konfiguracja jest obsługiwana, ponieważ usługa Media Services może odfiltrować zduplikowane fragmenty na podstawie identyfikatora strumienia i sygnatury czasowej fragmentu. Wynikowy strumień na żywo i archiwum jest pojedynczą kopią wszystkich strumieni, która jest najlepszą możliwą agregacją z dwóch źródeł. Na przykład w hipotetycznym przypadku skrajnym, o ile istnieje jeden koder (nie musi być taki sam) uruchomiony w danym momencie dla każdego strumienia, wynikowy strumień na żywo z usługi jest ciągły bez utraty danych. 

Wymagania dla tego scenariusza są prawie takie same jak wymagania w przypadku "Koder awaryjny", z wyjątkiem, że drugi zestaw koderów są uruchomione w tym samym czasie co kodery podstawowe.

## <a name="10-service-redundancy"></a>10. Nadmiarowość usług
W przypadku wysoce nadmiarowej dystrybucji globalnej czasami musisz mieć kopię zapasową między regionami, aby obsłużyć awarie regionalne. Rozszerzając topologię "Nadmiarowość kodera", klienci mogą wybrać opcję wdrożenia usługi nadmiarowej w innym regionie, który jest połączony z drugim zestawem koderów. Klienci mogą również współpracować z dostawcą sieci dostarczania zawartości w celu wdrożenia globalnego menedżera ruchu przed dwoma wdrożeniami usługi, aby bezproblemowo kierować ruch klientów. Wymagania dotyczące koderów są takie same jak w przypadku "Nadmiarowość kodera". Jedynym wyjątkiem jest to, że drugi zestaw koderów musi być wskazywalny na inny punkt końcowy pozyskiwania na żywo. Na poniższym diagramie przedstawiono następującą konfigurację:

![nadmiarowość usług][image7]

## <a name="11-special-types-of-ingestion-formats"></a>11. Specjalne rodzaje formatów połkowych
W tej sekcji omówiono specjalne typy formatów pozyskiwania na żywo, które są przeznaczone do obsługi określonych scenariuszy.

### <a name="sparse-track"></a>Ścieżka rozrzedzona
Podczas dostarczania prezentacji przesyłania strumieniowego na żywo z bogatym doświadczeniem klienta, często konieczne jest przesyłanie zdarzeń synchronizowanych czasem lub sygnałów w paśmie z głównymi danymi multimedialnymi. Przykładem tego jest dynamiczne wstawianie reklam na żywo. Ten rodzaj sygnalizacji zdarzeń różni się od zwykłego przesyłania strumieniowego audio/ wideo ze względu na jego rzadki charakter. Innymi słowy dane sygnalizacji zwykle nie dzieje się w sposób ciągły, a interwał może być trudne do przewidzenia. Koncepcja rzadkiego toru została zaprojektowana do pozyskiwania i emitowania danych sygnalizujących w paśmie.

Następujące kroki są zalecaną implementacją do pozyskiwania rzadkie ścieżki:

1. Utwórz oddzielny fragmentaryczny strumień bitów MP4, który zawiera tylko rzadkie ścieżki, bez ścieżek audio/wideo.
1. W **polu manifestu serwera na żywo,** zgodnie z definicją w sekcji 6 w [1], użyj parametru *parentTrackName,* aby określić nazwę ścieżki nadrzędnej. Więcej informacji można znaleźć w punkcie 4.2.1.2.1.2 w [1].
1. W polu **manifestu serwera na żywo** **manifestOutput** MUSI być ustawiony na **true**.
1. Biorąc pod uwagę rzadki charakter zdarzenia sygnalizacji, zalecamy następujące czynności:
   
    a. Na początku zdarzenia na żywo koder wysyła początkowe pola nagłówka do usługi, co umożliwia usłudze rejestrowanie ścieżki rozrzedzone w manifeście klienta.
   
    b. Koder powinien zakończyć żądanie HTTP POST, gdy dane nie są wysyłane. Długotrwały wpis HTTP, który nie wysyła danych, może uniemożliwić programowi Media Services szybkie odłączanie się od kodera w przypadku aktualizacji usługi lub ponownego uruchomienia serwera. W takich przypadkach serwer multimediów jest tymczasowo zablokowany w operacji odbierania na gnieździe.
   
    d. W czasie, gdy dane sygnalizacji nie są dostępne, koder powinien zamknąć żądanie HTTP POST. Gdy żądanie POST jest aktywne, koder powinien wysyłać dane.

    d. Podczas wysyłania fragmentów rozrzedzone, koder można ustawić jawne nagłówka o długości zawartości, jeśli jest dostępny.

    e. Podczas wysyłania rozrzedzonych fragmentów z nowym połączeniem koder powinien rozpocząć wysyłanie z pól nagłówka, a następnie nowe fragmenty. Jest to w przypadkach, w których przewijanie awaryjne odbywa się między nimi, a nowe połączenie rozrzedzone jest ustanawiane na nowy serwer, który nie widział rozrzedzonej ścieżki przed.

    f. Fragment ścieżki rozrzedzonych staje się dostępny dla klienta, gdy odpowiedni element nadrzędny fragment ścieżki, który ma taką samą lub większą wartość sygnatury czasowej, jest udostępniany klientowi. Na przykład jeśli fragment rozrzedzony ma sygnaturę czasową t=1000, oczekuje się, że po tym, jak klient zobaczy "video" (przy założeniu, że nazwą ścieżki nadrzędnej jest "wideo") sygnatura czasowa fragmentu 1000 lub więcej, może pobrać fragment rozrzedzony t=1000. Należy zauważyć, że rzeczywisty sygnał może być używany do innej pozycji w osi czasu prezentacji dla wyznaczonego celu. W tym przykładzie jest możliwe, że fragment rozrzedzony t=1000 ma ładunek XML, który służy do wstawiania reklamy w pozycji, która jest kilka sekund później.

    g. Ładunek fragmentów ścieżki rozrzedzone może być w różnych formatach (takich jak XML, tekst lub binarny), w zależności od scenariusza.

### <a name="redundant-audio-track"></a>Nadmiarowa ścieżka audio
W typowym scenariuszu przesyłania strumieniowego adaptacyjnego HTTP (na przykład Smooth Streaming lub DASH), często w całej prezentacji znajduje się tylko jedna ścieżka audio. W przeciwieństwie do ścieżek wideo, które mają wiele poziomów jakości dla klienta do wyboru w warunkach błędu, ścieżka audio może być pojedynczym punktem awarii, jeśli połknięcie strumienia zawierającego ścieżkę audio jest przerwane. 

Aby rozwiązać ten problem, program Media Services obsługuje na żywo pozyskiwania nadmiarowych ścieżek audio. Chodzi o to, że ta sama ścieżka audio może być wysyłana wiele razy w różnych strumieniach. Mimo że usługa rejestruje ścieżkę audio tylko raz w manifeście klienta, może używać nadmiarowych ścieżek audio jako kopii zapasowych do pobierania fragmentów audio, jeśli podstawowa ścieżka audio ma problemy. Aby łowić nadmiarowe ścieżki audio, koder musi:

1. Utwórz tę samą ścieżkę audio w wielu strumieniach bitów MP4. Nadmiarowe ścieżki audio MUSZĄ być semantycznie równoważne, z tymi samymi sygnaturami czasowymi fragmentów i być wymienne na poziomach nagłówka i fragmentu.
1. Upewnij się, że wpis "audio" w manifeście live server (sekcja 6 w [1]) jest taki sam dla wszystkich nadmiarowych ścieżek audio.

Zalecana jest następująca implementacja dla nadmiarowych ścieżek audio:

1. Wyślij każdą unikalną ścieżkę audio w strumieniu samodzielnie. Ponadto wyślij nadmiarowy strumień dla każdego z tych strumieni ścieżki audio, gdzie drugi strumień różni się od pierwszego tylko identyfikatorem w adresie URL HTTP POST: {protocol}://{server address}/{publishing point path}/Streams({identifier}).
1. Użyj oddzielnych strumieni, aby wysłać dwie najniższe szybkości transmisji bitów wideo. Każdy z tych strumieni powinien również zawierać kopię każdej unikalnej ścieżki dźwiękowej. Na przykład, gdy obsługiwanych jest wiele języków, te strumienie powinny zawierać ścieżki audio dla każdego języka.
1. Użyj oddzielnych wystąpień serwera (kodera), aby zakodować i wysłać nadmiarowe strumienie wymienione w (1) i (2). 

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
