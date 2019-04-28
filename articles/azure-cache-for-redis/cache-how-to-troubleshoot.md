---
title: Jak rozwiązywać problemy z pamięć podręczna systemu Azure dla usługi Redis | Dokumentacja firmy Microsoft
description: Dowiedz się, jak rozwiązać typowe problemy z pamięcią podręczną Azure dla usługi Redis.
services: cache
documentationcenter: ''
author: yegu-ms
manager: jhubbard
editor: ''
ms.assetid: 928b9b9c-d64f-4252-884f-af7ba8309af6
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache
ms.devlang: na
ms.topic: article
ms.date: 03/15/2019
ms.author: yegu
ms.openlocfilehash: 66361871d365068a90a2eeab70d92adb6b246a83
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60830011"
---
# <a name="how-to-troubleshoot-azure-cache-for-redis"></a>Jak rozwiązywać problemy z pamięć podręczna systemu Azure dla usługi Redis

Ten artykuł pomaga w rozwiązywaniu różne kategorie problemów, które mogą wystąpić podczas nawiązywania połączenia z usługą Azure Cache dla wystąpień usługi Redis.

- [Rozwiązywanie problemów po stronie klienta](#client-side-troubleshooting) ułatwia identyfikowanie i rozwiązywanie problemów w aplikacji, na nawiązywanie połączeń z pamięci podręcznej.
- [Rozwiązywanie problemów po stronie serwera](#server-side-troubleshooting) ułatwia identyfikowanie i rozwiązywanie problemów występujących na usługi Azure Cache dla strony pamięci podręcznej Redis.
- [Rozwiązywanie problemów z utratą danych](#data-loss-troubleshooting) ułatwia identyfikowanie i rozwiązywanie zdarzeń, gdzie klucze są nie znaleziono oczekiwanej w pamięci podręcznej.
- [Wyjątków przekroczenia limitu czasu StackExchange.Redis](#stackexchangeredis-timeout-exceptions) zawiera dokładne wskazówki dotyczące rozwiązywania problemów z biblioteki StackExchange.Redis.

> [!NOTE]
> Niektóre kroki rozwiązywania problemów, w tym przewodniku obejmują instrukcji, aby uruchamiać polecenia Redis i monitorować różne metryki wydajności. Aby uzyskać więcej informacji oraz instrukcje, zobacz artykuły w [dodatkowe informacje](#additional-information) sekcji.
>
>

## <a name="client-side-troubleshooting"></a>Rozwiązywanie problemów po stronie klienta

W tej sekcji omówiono rozwiązywanie problemów, które występują ze względu na stan aplikacji klienta.

- [Wykorzystanie pamięci na komputerze klienckim](#memory-pressure-on-the-client)
- [Serii ruchu](#burst-of-traffic)
- [Klient wysokie użycie procesora CPU](#high-client-cpu-usage)
- [Przekroczona przepustowość po stronie klienta](#client-side-bandwidth-exceeded)
- [Rozmiar dużych żądań/odpowiedzi](#large-requestresponse-size)

### <a name="memory-pressure-on-the-client"></a>Wykorzystanie pamięci na komputerze klienckim

Wykorzystanie pamięci na komputerze klienckim prowadzi do wszelkiego rodzaju problemów z wydajnością, które mogą opóźnić proces przetwarzania odpowiedzi z pamięci podręcznej. Gdy liczba trafień wykorzystanie pamięci, system może strony danych na dysku. To _powodujący błąd strony_ sprawia, że znacznie spowolnić system.

Aby wykryć wykorzystanie pamięci na komputerze klienckim:

- Monitoruj użycie pamięci na komputerze, aby upewnić się, że nie może przekraczać dostępnej pamięci.
- Monitorowanie klienta `Page Faults/Sec` licznika wydajności. Podczas normalnego działania większość systemów ma kilka błędów stron. Skoków błędów stron odpowiedni dla żądania przekroczeń limitu czasu może wskazywać dużego wykorzystania pamięci.

Wykorzystanie dużą ilość pamięci na komputerze klienckim, można zminimalizować na kilka sposobów:

- Analizując swoje wzorców użycia pamięci, aby zmniejszyć zużycie pamięci na komputerze klienckim.
- Uaktualnić klienta maszyny Wirtualnej na większy rozmiar przy użyciu większej ilości pamięci.

### <a name="burst-of-traffic"></a>Serii ruchu

Wzrosty ruchu w połączeniu z niska `ThreadPool` ustawień może spowodować opóźnienia w przetwarzaniu danych już wysłanych przez serwer Redis, ale nie są jeszcze używane po stronie klienta.

Monitor jak Twoje `ThreadPool` statystyki zmieniać wraz z upływem czasu przy użyciu [przykład `ThreadPoolLogger` ](https://github.com/JonCole/SampleCode/blob/master/ThreadPoolMonitor/ThreadPoolLogger.cs). Możesz użyć `TimeoutException` wiadomości z StackExchange.Redis, takich jak poniżej w celu dalszego zbadania problemu:

    System.TimeoutException: Timeout performing EVAL, inst: 8, mgr: Inactive, queue: 0, qu: 0, qs: 0, qc: 0, wr: 0, wq: 0, in: 64221, ar: 0,
    IOCP: (Busy=6,Free=999,Min=2,Max=1000), WORKER: (Busy=7,Free=8184,Min=2,Max=8191)

W poprzednim wyjątku istnieje kilka problemów, które mogą być interesujące:

- Należy zauważyć, że w `IOCP` sekcji i `WORKER` sekcji masz `Busy` wartość, która jest większa niż `Min` wartości. Różnica ta oznacza, że Twoje `ThreadPool` ustawienia wymagać dostosowania.
- Można również wyświetlić `in: 64221`. Ta wartość wskazuje, że 64,211 Bajty otrzymane w warstwie klienta jądra gniazda, ale jeszcze nie został odczytany przez aplikację. Różnica ta zazwyczaj oznacza, że aplikacji (na przykład StackExchange.Redis) będą nie jest odczyt danych z sieci tak szybko, jak serwer wysyła go do Ciebie.

Możesz [skonfigurować swoje `ThreadPool` ustawienia](https://gist.github.com/JonCole/e65411214030f0d823cb) aby upewnić się, że z puli wątków jest skalowany w górę szybko w obszarze serii scenariuszy.

### <a name="high-client-cpu-usage"></a>Klient wysokie użycie procesora CPU

Klient wysokie użycie procesora CPU wskazuje, że system nie może nadążyć z pracy, który jest prośby o ich czy. Mimo, że pamięć podręczna wysyłane odpowiedzi szybko, klient może zakończyć się niepowodzeniem do przetwarzania odpowiedzi w odpowiednim czasie.

Monitoruj użycie procesora CPU całego systemu klienta przy użyciu metryk dostępnych w witrynie Azure portal lub za pomocą liczników wydajności na maszynie. Uważaj nie monitorował *procesu* procesora CPU, ponieważ jeden proces może mieć niskie użycie procesora CPU, ale Procesora systemowe mogą być wysokie. Obserwuj wzrostów użycia procesora CPU, które odnoszą się do przekroczenia limitu czasu. Wysokie użycie procesora CPU mogą również spowodować wysokiej `in: XXX` wartości w `TimeoutException` komunikaty o błędach, zgodnie z opisem w [serii ruchu](#burst-of-traffic) sekcji.

> [!NOTE]
> StackExchange.Redis 1.1.603 i nowszych obejmują `local-cpu` metryki w `TimeoutException` komunikaty o błędach. Upewnij się, że za pomocą najnowszej wersji [pakietu StackExchange.Redis NuGet](https://www.nuget.org/packages/StackExchange.Redis/). Brak błędów stale zostanie naprawiony w kodzie, aby był bardziej niezawodny dla limitów czasu tak ważne jest posiadanie najnowszej wersji.
>
>

Aby zmniejszyć wysokie użycie procesora CPU do klienta:

- Sprawdź, co jest przyczyną wzrostów użycia Procesora.
- Uaktualnij klienta do większego rozmiaru maszyny Wirtualnej o większej pojemności procesora CPU.

### <a name="client-side-bandwidth-exceeded"></a>Przekroczona przepustowość po stronie klienta

W zależności od architektury komputery klienckie, mogą mieć ograniczenia na przepustowość sieci, ile mają one dostępne. Jeżeli klient przekracza dostępną przepustowość przeciążając pojemności sieci, danych nie jest przetwarzany po stronie klienta tak szybko, jak serwer wysyła go. Taka sytuacja może prowadzić do przekroczenia limitu czasu.

Monitorowanie, jak wykorzystanie przepustowości sieci, zmieniać wraz z upływem czasu przy użyciu [przykład `BandwidthLogger` ](https://github.com/JonCole/SampleCode/blob/master/BandWidthMonitor/BandwidthLogger.cs). Ten kod może nie działać prawidłowo w niektórych środowiskach z ograniczonymi uprawnieniami (np. witryny sieci web systemu Azure).

Aby rozwiązać problem, ograniczyć zużycie przepustowości sieci, lub zwiększ rozmiar maszyny Wirtualnej do jednego o większej pojemności sieci klienta.

### <a name="large-requestresponse-size"></a>Rozmiar dużych żądań/odpowiedzi

Dużych żądań/odpowiedzi może być przyczyną przekroczenia limitu czasu. Na przykład załóżmy, że wartość limitu czasu skonfigurowany na komputerze klienckim wynosi 1 s. Aplikacja żąda dwa klucze (na przykład, "A" i "B") w tym samym czasie (przy użyciu tego samego połączenia sieci fizycznej). Większość klientów obsługuje żądania "przetwarzanie potokowe", gdzie oba "A" i "B" są wysyłane żądania jedna po drugiej bez oczekiwania na odpowiedzi. Serwer wysyła odpowiedzi w tej samej kolejności. W przypadku dużych odpowiedzi "A", może to jeść większość limitu czasu dla nowszych żądań.

W poniższym przykładzie żądania "A" i "B" są szybko wysyłane do serwera. Serwer jest uruchamiany szybkie wysyłanie odpowiedzi "A" i "B". Ze względu na czas transferu danych odpowiedzi, 'B' musi czekać za odpowiedź "A" upłynie limit czasu, mimo że szybko odpowiedź serwera.

    |-------- 1 Second Timeout (A)----------|
    |-Request A-|
         |-------- 1 Second Timeout (B) ----------|
         |-Request B-|
                |- Read Response A --------|
                                           |- Read Response B-| (**TIMEOUT**)

Żądanie/odpowiedź jest trudne do mierzenia. Można instrumentować swój kod klienta, aby śledzić dużych żądań i odpowiedzi.

Rozwiązania dla dużych odpowiedzi rozmiarów są różne, ale obejmuje:

1. Optymalizowanie aplikacji dla dużej liczby małych wartości, a nie kilka dużych wartościach.
    - Preferowanym rozwiązaniem jest podział danych do powiązanych wartości mniejsze.
    - Zobacz wpis [co to jest idealna wartość zakresem rozmiar pamięci podręcznej redis? 100 KB jest za duży? ](https://groups.google.com/forum/#!searchin/redis-db/size/redis-db/n7aa2A4DZDs/3OeEPHSQBAAJ) Aby uzyskać szczegółowe informacje o tym, dlaczego są zalecane, mniejsze wartości.
1. Zwiększ rozmiar maszyny Wirtualnej, aby uzyskać wyższy możliwości przepustowości
    - Większej przepustowości w maszynie Wirtualnej serwera lub klienta, na których może zmniejszyć czas transferu danych dla większych odpowiedzi.
    - Porównaj bieżące użycie sieci na obu komputerach, na granicach bieżącego rozmiaru maszyn wirtualnych. Większa przepustowość tylko serwer lub tylko na komputerze klienckim może nie być wystarczająca.
1. Zwiększ liczbę obiektów połączeń, używanych przez aplikację.
    - Użyj sposób okrężny na wysyłanie żądań za pośrednictwem połączenia różnych obiektów.

## <a name="server-side-troubleshooting"></a>Rozwiązywanie problemów po stronie serwera

W tej sekcji omówiono rozwiązywanie problemów, które występują ze względu na stan na serwerze pamięci podręcznej.

- [Wykorzystanie pamięci na serwerze](#memory-pressure-on-the-server)
- [Wysokie użycie procesora CPU / obciążenie serwera](#high-cpu-usage--server-load)
- [Przekroczona przepustowość po stronie serwera](#server-side-bandwidth-exceeded)

### <a name="memory-pressure-on-the-server"></a>Wykorzystanie pamięci na serwerze

Wykorzystanie pamięci po stronie serwera prowadzi do wszelkiego rodzaju problemów z wydajnością, które mogą opóźnić proces przetwarzania żądania. Gdy liczba trafień wykorzystanie pamięci, system może strony danych na dysku. To _powodujący błąd strony_ sprawia, że znacznie spowolnić system. Istnieje kilka możliwych przyczyn to wykorzystania pamięci:

- Pamięć podręczna jest wypełniony niemal pojemności maksymalnej.
- Duża ilość pamięci fragmentacji ma do czynienia z pamięci podręcznej redis. Fragmentacja to najczęściej spowodowane przechowywanie dużych obiektów, ponieważ w pamięci podręcznej Redis zoptymalizowana pod kątem małych obiektów.

Redis Cache udostępnia dwa statystyki za pośrednictwem [informacje](https://redis.io/commands/info) polecenia, które pomaga zidentyfikować ten problem: "used_memory" i "used_memory_rss". Możesz [wyświetlić te metryki](cache-how-to-monitor.md#view-metrics-with-azure-monitor) przy użyciu portalu.

Istnieje kilka możliwych zmian, wprowadzane w celu zapewnienia dobrej kondycji zużycie pamięci:

- [Konfigurowanie zasad pamięci](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) i ustaw czas wygaśnięcia na klucze. Ta zasada może nie być wystarczające, jeśli masz fragmentacji.
- [Skonfiguruj wartość maxmemory zastrzeżone](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) jest wystarczająco duży, aby zrekompensować fragmentacji pamięci. Aby uzyskać więcej informacji, zobacz dodatkowe [zagadnienia dotyczące rezerwacji pamięci](#considerations-for-memory-reservations) poniżej.
- Podziel duże obiekty pamięci podręcznej na mniejsze powiązane obiekty.
- [Tworzenie alertów](cache-how-to-monitor.md#alerts) na metryki, takie jak użycie pamięci powiadamiany wcześnie o potencjalny wpływ na środowisko.
- [Skala](cache-how-to-scale.md) na większy rozmiar pamięci podręcznej o większej pojemności pamięci.

#### <a name="considerations-for-memory-reservations"></a>Zagadnienia dotyczące rezerwacji pamięci

Trwa aktualizowanie wartości rezerwacji pamięci, takich jak maxmemory zarezerwowane, może wpływać na wydajność pamięci podręcznej. Załóżmy, że 53GB pamięci podręcznej, który zostanie wypełniony kolorem 49 GB danych. Zmiana wartości rezerwacji do 8 GB porzuca systemu maksymalną dostępną pamięć, aby 45 GB. Jeśli _used_memory_ lub _used_memory_rss_ wartości jest wyższa niż 45 GB, system może eksmitują dane przed zakończeniem _used_memory_ i _used_memory_rss_ znajdują się poniżej 45 GB. Wykluczenia może zwiększyć fragmentacji obciążenia i pamięci serwera.

### <a name="high-cpu-usage--server-load"></a>Wysokie użycie procesora CPU / obciążenie serwera

Obciążenie serwera wysokiej albo użycia procesorów CPU oznacza, że serwer nie może przetworzyć żądania w odpowiednim czasie. Serwer może być powolne odpowiedzi i nie może nadążyć z kursów żądania.

[Monitoruj metryki](cache-how-to-monitor.md#view-metrics-with-azure-monitor) takich jak obciążenie procesora CPU lub serwera. Obserwuj wzrostów użycia procesora CPU, które odnoszą się do przekroczenia limitu czasu.

Istnieje kilka zmian, możesz wprowadzić, aby zmniejszyć obciążenie serwera wysokiej:

- Zbadaj, co jest przyczyną wzrostów użycia Procesora, takie jak uruchamianie [kosztownych poleceń](#expensive-commands) lub strony powodujący błąd z powodu dużego wykorzystania dużą ilość pamięci.
- [Tworzenie alertów](cache-how-to-monitor.md#alerts) na metryki, takie jak obciążenie procesora CPU lub serwer powiadamiany wcześnie o potencjalny wpływ na środowisko.
- [Skala](cache-how-to-scale.md) na większy rozmiar pamięci podręcznej o większej pojemności procesora CPU.

#### <a name="expensive-commands"></a>Kosztownych poleceń

Nie wszystkie polecenia usługi Redis są tworzone w równym stopniu — niektóre są droższe niż inne. [Redis polecenia dokumentacji](https://redis.io/commands) pokazano stopnia złożoności czasu każdego polecenia. Zalecane jest, że zostały spełnione poleceń, że używasz w pamięci podręcznej w celu zrozumienia wpływu tych poleceń. Na przykład [KLUCZE](https://redis.io/commands/keys) polecenia jest często używana, nie wiedząc o tym, że jest operacją O(N). KLUCZE można uniknąć, używając [skanowania](https://redis.io/commands/scan) zmniejszyć Procesora nagłego zapotrzebowania.

Za pomocą [dziennik wolno wykonywanych zapytań](https://redis.io/commands/slowlog) polecenia, można zmierzyć, kosztownych poleceń, wykonywane na serwerze.

### <a name="server-side-bandwidth-exceeded"></a>Przekroczona przepustowość po stronie serwera

Pamięć podręczna w różnych rozmiarach charakteryzują się pojemności przepustowości inną sieć. Jeśli serwer przekracza dostępną przepustowość, dane nie zostaną wysłane do klienta tak szybko. Żądania klientów może przekroczyć limit czasu, ponieważ serwer nie można wypchnąć dane do klienta wystarczająco szybko.

Metryki "Pamięć podręczna Odczyt" i "Pamięć podręczna zapisu" może służyć do przepustowości, jaką po stronie serwera jest używany. Możesz [wyświetlić te metryki](cache-how-to-monitor.md#view-metrics-with-azure-monitor) w portalu.

Aby uniknąć sytuacji, gdy wykorzystanie przepustowości sieci znajduje się w pobliżu maksymalną pojemność:

- Zmiany zachowania wywołania klienta, aby ograniczyć żądanie sieci.
- [Tworzenie alertów](cache-how-to-monitor.md#alerts) dotyczące metryk, takich jak pamięci podręcznej odczytu lub zapisu w pamięci podręcznej powiadamiany wcześnie o potencjalny wpływ na środowisko.
- [Skala](cache-how-to-scale.md) na większy rozmiar pamięci podręcznej o większej pojemności przepustowości sieci.

## <a name="data-loss-troubleshooting"></a>Rozwiązywanie problemów z utratą danych

Dla niektórych oczekiwanych danych w pamięci podręcznej Azure Moje wystąpienia pamięci podręcznej Redis, ale go nie wydaje się, że ma być.

Zobacz [co się stało z moimi danymi w usłudze Redis?](https://gist.github.com/JonCole/b6354d92a2d51c141490f10142884ea4#file-whathappenedtomydatainredis-md) możliwe przyczyny i rozwiązania.

## <a name="stackexchangeredis-timeout-exceptions"></a>Wyjątków przekroczenia limitu czasu StackExchange.Redis

StackExchange.Redis korzysta z konfiguracji, ustawienie o nazwie `synctimeout` na operacje synchroniczne z wartością domyślną 1000 ms. Jeśli wywołanie synchroniczne nie zakończy się w tym okresie, klient StackExchange.Redis zgłasza błąd upływu limitu czasu, podobny do poniższego przykładu:

    System.TimeoutException: Timeout performing MGET 2728cc84-58ae-406b-8ec8-3f962419f641, inst: 1,mgr: Inactive, queue: 73, qu=6, qs=67, qc=0, wr=1/1, in=0/0 IOCP: (Busy=6, Free=999, Min=2,Max=1000), WORKER (Busy=7,Free=8184,Min=2,Max=8191)

Ten komunikat o błędzie zawiera metryki, które mogą ułatwić wskaż przyczyny oraz możliwe rozwiązania tego problemu. Poniższa tabela zawiera szczegółowe informacje o metrykach komunikat o błędzie.

| Metryka komunikat o błędzie | Szczegóły |
| --- | --- |
| instrukcje powrotu |W ostatnim przedziału czasu: 0 polecenia zostały wydane. |
| mgr |Działania Menedżera gniazda `socket.select`, co oznacza, że jest zapytaniem systemu operacyjnego w celu wskazania gniazda, które ma coś zrobić. Czytnik nie jest aktywnie odczytu z sieci, ponieważ on nie wydaje się, że jest coś zrobić |
| kolejka |Istnieją 73 łączna liczba operacji w toku |
| qu |6 operacji w toku znajdują się w kolejce niewysłane i jeszcze nie zostały zapisane do sieciowego ruchu wychodzącego |
| qs |67 operacji w toku zostały wysłane do serwera, ale odpowiedź nie jest jeszcze dostępna. Odpowiedzi mogą być `Not yet sent by the server` lub `sent by the server but not yet processed by the client.` |
| qc |Zauważyliśmy już odpowiedzi na 0 lub operacji w toku, ale jeszcze nie zostało oznaczone jako ukończone, ponieważ czeka na zakończenie pętli |
| wr |Ma aktywnego modułu zapisującego (co oznacza, że 6 niewysłane żądania nie są ignorowane) bajty/activewriters |
| wejście |Nie ma żadnych aktywnych czytelników i zero bajtów dostępnych do odczytu na NIC bajty/activereaders |

### <a name="steps-to-investigate"></a>Kroki, aby zbadać

1. Najlepszym rozwiązaniem upewnij się, że używasz następujący wzorzec łączenia podczas korzystania z klienta StackExchange.Redis.

    ```csharp
    private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
    {
        return ConnectionMultiplexer.Connect("cachename.redis.cache.windows.net,abortConnect=false,ssl=true,password=...");

    });

    public static ConnectionMultiplexer Connection
    {
        get
        {
            return lazyConnection.Value;
        }
    }
    ```

    Aby uzyskać więcej informacji, zobacz [nawiązywanie połączenia z pamięci podręcznej, za pomocą StackExchange.Redis](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-the-cache).

1. Upewnij się, że serwer i aplikacja kliencka znajdują się w tym samym regionie platformy Azure. Na przykład użytkownik może się pojawiać przekroczeń limitu czasu gdy pamięć podręczna znajduje się we wschodnim regionie USA, ale klient znajduje się w regionie zachodnie stany USA i żądania nie zakończył się w `synctimeout` interwał lub mogą się pojawiać przekroczeń limitu czasu podczas debugowania z lokalnej maszynie do programowania. 

    Zdecydowanie zaleca się mieć pamięci podręcznej i w kliencie, w tym samym regionie platformy Azure. W przypadku scenariusza, który zawiera wywołań między regionami, należy ustawić `synctimeout` interwał na wartość większą niż domyślny interwał 1000 ms, umieszczając `synctimeout` właściwość w parametrach połączenia. W poniższym przykładzie przedstawiono fragment ciągu połączenia dla StackExchange.Redis udostępnianej przez pamięć podręczna Azure redis cache za pomocą `synctimeout` 2000 MS.

        synctimeout=2000,cachename.redis.cache.windows.net,abortConnect=false,ssl=true,password=...
1. Upewnij się, że za pomocą najnowszej wersji [pakietu StackExchange.Redis NuGet](https://www.nuget.org/packages/StackExchange.Redis/). Brak błędów stale zostanie naprawiony w kodzie, aby był bardziej niezawodny dla limitów czasu tak ważne jest posiadanie najnowszej wersji.
1. Jeśli żądania są powiązane przez ograniczenia przepustowości na serwerze lub kliencie, go dłużej na ich ukończenie i może spowodować, że limity czasu. Aby zobaczyć, czy Twoje limitu czasu, ze względu na przepustowość sieci na serwerze, zobacz [Przekroczona przepustowość po stronie serwera](#server-side-bandwidth-exceeded). Aby zobaczyć, czy Twoje limitu czasu, ze względu na przepustowość sieci klienta, zobacz [Przekroczona przepustowość po stronie klienta](#client-side-bandwidth-exceeded).
1. Są możesz wprowadzenie procesora CPU związane na serwerze lub na komputerze klienckim?

   - Sprawdź, jeśli użytkownik jest wprowadzenie powiązany najbardziej obciążających procesor CPU na komputerze klienckim. Wysokie użycie procesora CPU może spowodować, że żądania nie został przetworzony w ramach `synctimeout` interwał i przyczynę żądania przekroczy limit czasu. Przejście do klienta o większym rozmiarze lub rozłożeniu obciążenia może pomóc kontrolować ten problem.
   - Sprawdź, czy uzyskujesz procesora CPU związane na serwerze monitorowania `CPU` [Metryka wydajności w pamięci podręcznej](cache-how-to-monitor.md#available-metrics-and-reporting-intervals). Żądań przychodzących trakcie Redis procesora CPU związane może spowodować te żądania, które przekraczają limit czasu. Aby rozwiązać ten problem, rozkładają obciążenie na wiele fragmentów w pamięci podręcznej — wersja premium lub uaktualnienia do wersji o większym rozmiarze lub warstwie cenowej. Aby uzyskać więcej informacji, zobacz [Przekroczona przepustowość po stronie serwera](#server-side-bandwidth-exceeded).
1. Czy istnieją polecenia dłużej przetwarzania na serwerze? Długotrwałych poleceń, które mają długi czas przetwarzania na serwerze usługi redis może spowodować przekroczeń limitu czasu. Aby uzyskać więcej informacji na temat długotrwałych poleceń, zobacz [kosztownych poleceń](#expensive-commands). Można połączyć się z pamięci podręcznej dla wystąpienia pamięci podręcznej Redis przy użyciu klienta pamięci podręcznej redis — interfejs wiersza polecenia platformy Azure lub [konsolę pamięci podręcznej Redis](cache-configure.md#redis-console). Następnie uruchom [dziennik wolno wykonywanych zapytań](https://redis.io/commands/slowlog) polecenie, aby sprawdzić, czy żądania wolniej niż oczekiwano. Serwer redis i StackExchange.Redis są zoptymalizowane pod kątem wielu małych żądań, a nie mniej dużych żądań. Podział danych na mniejsze części może poprawić rzeczy w tym miejscu.

    Instrukcje dotyczące nawiązywania połączenia z punktu końcowego protokołu SSL pamięć podręczną przy użyciu usługi redis — interfejs wiersza polecenia i programu stunnel, zobacz wpis w blogu [ogłoszenie dostawca stanu sesji ASP.NET dla wersji zapoznawczej Redis](https://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx).
1. Wysokie obciążenie serwera Redis może spowodować przekroczeń limitu czasu. Można monitorować, obciążenie serwera jest monitorowanie `Redis Server Load` [Metryka wydajności w pamięci podręcznej](cache-how-to-monitor.md#available-metrics-and-reporting-intervals). Obciążenie serwera, 100 (wartość maksymalna) oznacza, że serwer redis był zajęty, bez czasu bezczynności, przetwarzanie żądań. Aby zobaczyć, jeśli niektórych żądań zajmują wszystkich funkcji serwera, uruchom polecenie Dziennik wolno wykonywanych zapytań, zgodnie z opisem w poprzednim akapicie. Aby uzyskać więcej informacji, zobacz wysokie użycie procesora CPU / obciążenie serwera.
1. Po stronie klienta, który może to być spowodowane blip sieci była dowolne inne zdarzenie? Typowe zdarzenia obejmują: skalowanie liczby wystąpień klienta w górę lub w dół, wdrażania nowej wersji klienta lub włączyć Skalowanie automatyczne. W naszych testach, znaleźliśmy, że automatyczne skalowanie lub skalowania w górę/w dół może spowodować połączenia sieciowego ruchu wychodzącego, zostanie utracone przez kilka sekund. Kod StackExchange.Redis jest odporna na takie zdarzenia i ponownie nawiąże połączenie. Podczas ponownego podłączania, wszystkie żądania w kolejce może przekroczyło limit czasu.
1. Czy były dużych poprzedzających kilka żądań o małym rozmiarze pamięci podręcznej, który upłynął limit czasu żądania? Parametr `qs` w błędzie komunikat informujący o tym ile żądań wysłanych z klienta do serwera, ale nie zostały przetworzone odpowiedzi. Tę wartość można zachować rośnie, ponieważ StackExchange.Redis korzysta z jednego połączenia TCP i jedną odpowiedź mogą być odczytane tylko w danym momencie. Mimo że upłynął limit czasu operacji pierwszy, go nie zatrzymuje większej ilości danych wysyłanych do lub z serwera. Inne żądania będą blokowane, aż do dużych żądanie zostało zakończone i może spowodować, że limity czasu. Jednym rozwiązaniem jest zminimalizować prawdopodobieństwo przekroczenia limitu czasu, zapewnia, że pamięć podręczna jest wystarczająco duży dla obciążenia i dzielenia dużych wartości na mniejsze części. Inne rozwiązanie możliwe jest użycie puli `ConnectionMultiplexer` obiektów w swoim kliencie, a następnie wybierz co najmniej załadować `ConnectionMultiplexer` wysyłając żądanie nowego. Podczas ładowania wielu obiektów połączenia uniemożliwiać pojedynczego limitu czasu będą powodować innych żądaniach również limit czasu.
1. Jeśli używasz `RedisSessionStateProvider`, upewnij się, limit czasu ponawiania zostały ustawione prawidłowo. `retryTimeoutInMilliseconds` powinien być większy niż `operationTimeoutInMilliseconds`, w przeciwnym razie wystąpić brak ponownych prób. W poniższym przykładzie `retryTimeoutInMilliseconds` jest równa 3000. Aby uzyskać więcej informacji, zobacz [dostawca stanu sesji ASP.NET dla usługi Azure Cache dla usługi Redis](cache-aspnet-session-state-provider.md) i [sposobu korzystania z parametrów konfiguracji dostawcy stanu sesji i dostawca wyjściowej pamięci podręcznej](https://github.com/Azure/aspnet-redis-providers/wiki/Configuration).

    ```xml
    <add
      name="AFRedisCacheSessionStateProvider"
      type="Microsoft.Web.Redis.RedisSessionStateProvider"
      host="enbwcache.redis.cache.windows.net"
      port="6380"
      accessKey="…"
      ssl="true"
      databaseId="0"
      applicationName="AFRedisCacheSessionState"
      connectionTimeoutInMilliseconds = "5000"
      operationTimeoutInMilliseconds = "1000"
      retryTimeoutInMilliseconds="3000" />
    ```

1. Sprawdź użycie pamięci w usłudze Azure Cache serwera Redis przez [monitorowania](cache-how-to-monitor.md#available-metrics-and-reporting-intervals) `Used Memory RSS` i `Used Memory`. Jeśli zasady eksmisji znajduje się w miejscu, rozpoczyna się Redis wykluczania klucze kiedy `Used_Memory` osiągnie rozmiar pamięci podręcznej. W idealnym przypadku `Used Memory RSS` powinny być tylko nieznacznie wyższe niż `Used memory`. Duża różnica oznacza, że istnieje fragmentacji pamięci (wewnętrznych lub zewnętrznych). Gdy `Used Memory RSS` jest mniejsza niż `Used Memory`, oznacza to część pamięci podręcznej została zapisana przez system operacyjny. Jeśli zamianę ten problem wystąpi, można oczekiwać, że niektóre znaczne opóźnienia. Ponieważ usługa Redis nie ma kontrolę nad jak jego alokacje są mapowane do stron pamięci wysokiej `Used Memory RSS` często jest wynikiem wzrost użycia pamięci. Gdy serwer Redis zwalnia pamięć, alokator zajmuje pamięć, ale może być lub może nie być pamięć powrót do systemu. Może być niezgodność między `Used Memory` zużycie wartości i pamięci podaną przez system operacyjny. Może być używana pamięć i wydane przez usługi Redis, ale nie udzieliła wstecz do systemu. Aby pomóc rozwiązać problemy z pamięcią, można wykonać następujące czynności:

   - Uaktualnij do większego rozmiaru pamięci podręcznej, tak, aby nie są uruchomione względem ograniczeń pamięci, w systemie.
   - Ustaw czas wygaśnięcia na kluczach, tak, aby aktywnie obrazuje starsze wartości.
   - Monitor `used_memory_rss` metryki w pamięci podręcznej. Ta wartość koloru zbliża się rozmiaru pamięci podręcznej, prawdopodobnie wykonywanych problemy z wydajnością. Rozmieść dane w wielu fragmentów, jeśli jesteś przy użyciu cache w wersji premium lub uaktualnienie do większego rozmiaru pamięci podręcznej.

   Aby uzyskać więcej informacji, zobacz [wykorzystanie pamięci na serwerze](#memory-pressure-on-the-server).

## <a name="additional-information"></a>Dodatkowe informacje

- [Jakie usługi Azure Cache oferty pamięci podręcznej Redis i rozmiaru należy używać?](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use)
- [Jak testu wydajności i przetestować wydajność przepełnieniu pamięci podręcznej?](cache-faq.md#how-can-i-benchmark-and-test-the-performance-of-my-cache)
- [Jak uruchomić polecenia Redis](cache-faq.md#how-can-i-run-redis-commands)
- [Jak monitorować pamięć podręczna systemu Azure dla usługi Redis](cache-how-to-monitor.md)
