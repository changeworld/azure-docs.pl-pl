---
title: Jak rozwiązywać problemy z pamięć podręczna systemu Azure dla usługi Redis | Dokumentacja firmy Microsoft
description: Dowiedz się, jak rozwiązać typowe problemy z pamięcią podręczną Azure dla usługi Redis.
services: azure-cache-for-redis
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
ms.assetid: 928b9b9c-d64f-4252-884f-af7ba8309af6
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: azure-cache-for-redis
ms.devlang: na
ms.topic: article
ms.date: 01/06/2017
ms.author: wesmc
ms.openlocfilehash: 58c1af860c5ccc87f4396c698b432f47f0ea7c65
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/28/2019
ms.locfileid: "55096963"
---
# <a name="how-to-troubleshoot-azure-cache-for-redis"></a>Jak rozwiązywać problemy z pamięć podręczna systemu Azure dla usługi Redis
Ten artykuł zawiera wskazówki dotyczące rozwiązywania problemów z następujących kategorii pamięć podręczna systemu Azure w przypadku problemów z pamięci podręcznej Redis.

* [Rozwiązywanie problemów po stronie klienta](#client-side-troubleshooting) — ta sekcja zawiera wskazówki dotyczące identyfikowania i rozwiązywania problemów spowodowanych aplikacji nawiązaniem połączenia z usługą pamięć podręczna Azure redis Cache.
* [Rozwiązywanie problemów po stronie serwera](#server-side-troubleshooting) — ta sekcja zawiera wskazówki dotyczące identyfikowania i rozwiązywania problemów spowodowane na platformie Azure pamięci podręcznej po stronie serwera Redis.
* [Wyjątków przekroczenia limitu czasu StackExchange.Redis](#stackexchangeredis-timeout-exceptions) — ta sekcja zawiera informacje na temat rozwiązywania problemów w przypadku korzystania z klienta StackExchange.Redis.

> [!NOTE]
> Niektóre kroki rozwiązywania problemów, w tym przewodniku obejmują instrukcji, aby uruchamiać polecenia Redis i monitorować różne metryki wydajności. Aby uzyskać więcej informacji oraz instrukcje, zobacz artykuły w [dodatkowe informacje](#additional-information) sekcji.
> 
> 

## <a name="client-side-troubleshooting"></a>Rozwiązywanie problemów po stronie klienta
W tej sekcji omówiono rozwiązywanie problemów, które występują ze względu na stan aplikacji klienta.

* [Wykorzystanie pamięci na komputerze klienckim](#memory-pressure-on-the-client)
* [Serii ruchu](#burst-of-traffic)
* [Klient wysokie użycie procesora CPU](#high-client-cpu-usage)
* [Przekroczona przepustowość po stronie klienta](#client-side-bandwidth-exceeded)
* [Rozmiar dużych żądań/odpowiedzi](#large-requestresponse-size)
* [Co się stało z moimi danymi w usłudze Redis?](#what-happened-to-my-data-in-redis)

### <a name="memory-pressure-on-the-client"></a>Wykorzystanie pamięci na komputerze klienckim
#### <a name="problem"></a>Problem
Wykorzystanie pamięci na komputerze klienckim prowadzi do wszelkiego rodzaju problemów z wydajnością, które mogą opóźnić proces przetwarzania danych, który został wysłany przez wystąpienia pamięci podręcznej Redis, bez żadnego opóźnienia. Gdy liczba trafień wykorzystanie pamięci, system zwykle ma do danych strony od ilości pamięci fizycznej w pamięci wirtualnej, która znajduje się na dysku. To *powodujący błąd strony* sprawia, że znacznie spowolnić system.

#### <a name="measurement"></a>Miara
1. Monitoruj użycie pamięci na komputerze, aby upewnić się, że nie przekracza ilość dostępnej pamięci. 
2. Monitor `Page Faults/Sec` licznika wydajności. Większość systemów mają pewne błędy stron nawet podczas normalnego działania, więc poszukaj skoków ten licznik wydajności strony błędów, które odnoszą się do przekroczenia limitu czasu.

#### <a name="resolution"></a>Rozwiązanie
Uaktualnić klienta na kliencie większy rozmiar maszyny Wirtualnej przy użyciu większej ilości pamięci lub zagłębiania się w Twojej wzorców użycia pamięci, aby zmniejszyć zużycie pamięci.

### <a name="burst-of-traffic"></a>Serii ruchu
#### <a name="problem"></a>Problem
Wzrosty ruchu w połączeniu z niska `ThreadPool` ustawień może spowodować opóźnienia w przetwarzaniu danych już wysłanych przez serwer Redis, ale nie są jeszcze używane po stronie klienta.

#### <a name="measurement"></a>Miara
Monitor jak Twoje `ThreadPool` statystyki zmieniać wraz z upływem czasu przy użyciu kodu [następująco](https://github.com/JonCole/SampleCode/blob/master/ThreadPoolMonitor/ThreadPoolLogger.cs). Można również przeglądać `TimeoutException` komunikat z StackExchange.Redis. Oto przykład:

    System.TimeoutException: Timeout performing EVAL, inst: 8, mgr: Inactive, queue: 0, qu: 0, qs: 0, qc: 0, wr: 0, wq: 0, in: 64221, ar: 0, 
    IOCP: (Busy=6,Free=999,Min=2,Max=1000), WORKER: (Busy=7,Free=8184,Min=2,Max=8191)

W poprzednim komunikacie istnieje kilka problemów, które mogą być interesujące:

1. Należy zauważyć, że w `IOCP` sekcji i `WORKER` sekcji masz `Busy` wartość, która jest większa niż `Min` wartości. Różnica ta oznacza, że Twoje `ThreadPool` ustawienia wymagać dostosowania.
2. Można również wyświetlić `in: 64221`. Ta wartość wskazuje, że 64,211 bajty zostały odebrane w warstwie jądra gniazda, ale jeszcze nie został odczytany przez aplikację (na przykład StackExchange.Redis). Różnica ta zazwyczaj oznacza, że aplikacji będą nie jest odczyt danych z sieci tak szybko, jak serwer wysyła go do Ciebie.

#### <a name="resolution"></a>Rozwiązanie
Konfigurowanie usługi [ustawienia puli wątków](https://gist.github.com/JonCole/e65411214030f0d823cb) aby upewnić się, że z puli wątków jest skalowany w górę szybko w obszarze serii scenariuszy.

### <a name="high-client-cpu-usage"></a>Klient wysokie użycie procesora CPU
#### <a name="problem"></a>Problem
Wysokie użycie procesora CPU na kliencie jest wskazanie, że system nie może nadążyć z pracy, która ma prośby o ich wykonania. Tę sytuację oznacza, że klient może się nie powieść przetwarzania odpowiedzi z pamięci podręcznej Redis w odpowiednim czasie, nawet jeśli Redis wysyłane odpowiedzi szybko.

#### <a name="measurement"></a>Miara
Monitorowanie użycia szerokiego procesor CPU systemu za pośrednictwem witryny Azure Portal lub za pomocą licznika wydajności skojarzonego. Uważaj nie monitorował *procesu* procesora CPU, ponieważ jeden proces może mieć niskie użycie procesora CPU w tym samym czasu tego całego systemu Procesora może być wysokie. Obserwuj wzrostów użycia procesora CPU, które odnoszą się do przekroczenia limitu czasu. W wyniku wysokie użycie procesora CPU, może również zostać wyświetlony wysokiego `in: XXX` wartości w `TimeoutException` komunikaty o błędach, zgodnie z opisem w [serii ruchu](#burst-of-traffic) sekcji.

> [!NOTE]
> StackExchange.Redis 1.1.603 i nowszych obejmują `local-cpu` metryki w `TimeoutException` komunikaty o błędach. Upewnij się, że za pomocą najnowszej wersji [pakietu StackExchange.Redis NuGet](https://www.nuget.org/packages/StackExchange.Redis/). Brak błędów stale zostanie naprawiony w kodzie, aby był bardziej niezawodny dla limitów czasu tak ważne jest posiadanie najnowszej wersji.
> 
> 

#### <a name="resolution"></a>Rozwiązanie
Uaktualnij do większego rozmiaru maszyny Wirtualnej o większej pojemności procesora CPU lub badania, co jest przyczyną wzrostów użycia Procesora. 

### <a name="client-side-bandwidth-exceeded"></a>Przekroczona przepustowość po stronie klienta
#### <a name="problem"></a>Problem
W zależności od architektury komputery klienckie, mogą mieć ograniczenia na przepustowość sieci, ile mają one dostępne. Jeśli klient przekracza dostępną przepustowość przeciążając pojemności sieci, następnie danych nie jest wykonywane po stronie klienta tak szybko, jak serwer wysyła go. Taka sytuacja może prowadzić do przekroczenia limitu czasu.

#### <a name="measurement"></a>Miara
Monitorowanie, jak zmienić wykorzystanie przepustowości sieci wraz z upływem czasu przy użyciu kodu [następująco](https://github.com/JonCole/SampleCode/blob/master/BandWidthMonitor/BandwidthLogger.cs). Ten kod może nie działać prawidłowo w niektórych środowiskach z ograniczonymi uprawnieniami (np. witryny sieci web systemu Azure).

#### <a name="resolution"></a>Rozwiązanie
Zwiększ rozmiar maszyny Wirtualnej klienta lub ograniczyć zużycie przepustowości sieci.

### <a name="large-requestresponse-size"></a>Rozmiar dużych żądań/odpowiedzi
#### <a name="problem"></a>Problem
Dużych żądań/odpowiedzi może być przyczyną przekroczenia limitu czasu. Na przykład załóżmy, że wartość limitu czasu skonfigurowany na komputerze klienckim wynosi 1 s. Aplikacja żąda dwa klucze (na przykład, "A" i "B") w tym samym czasie (przy użyciu tego samego połączenia sieci fizycznej). Większość klientów pomocy technicznej "Pipelining" żądań w taki sposób, że zarówno w przypadku żądań "A" i "B" są wysyłane na o komunikacji sieciowej na serwerze, jeden po drugim bez oczekiwania na odpowiedzi. Serwer wysyła odpowiedzi w tej samej kolejności. Jeśli odpowiedź "A" jest wystarczająco duży, może to jeść większość limitu czasu dla kolejnych żądań. 

Poniższy przykład pokazuje, w tym scenariuszu. W tym scenariuszu szybko wysłaniem żądania "A" i "B", serwer jest uruchamiany szybkie wysyłanie odpowiedzi "A" i "B", ale ze względu na czas transferu danych, 'B' utknięcie żądania i limit czasu, mimo że szybko odpowiedź serwera.

    |-------- 1 Second Timeout (A)----------|
    |-Request A-|
         |-------- 1 Second Timeout (B) ----------|
         |-Request B-|
                |- Read Response A --------|
                                           |- Read Response B-| (**TIMEOUT**)



#### <a name="measurement"></a>Miara
Żądanie/odpowiedź jest trudne do mierzenia. Zasadniczo muszą instrumentować swój kod klienta, aby śledzić dużych żądań i odpowiedzi. 

#### <a name="resolution"></a>Rozwiązanie
1. Redis jest zoptymalizowany pod kątem dużej liczby małych wartości, a nie kilka dużych wartościach. Preferowanym rozwiązaniem jest podział danych do powiązanych wartości mniejsze. Zobacz [co to jest idealna wartość zakresem rozmiar pamięci podręcznej redis? 100 KB jest za duży? ](https://groups.google.com/forum/#!searchin/redis-db/size/redis-db/n7aa2A4DZDs/3OeEPHSQBAAJ) uzyskać szczegółowe informacje w całym dlaczego są zalecane, mniejsze wartości.
2. Zwiększ rozmiar maszyny wirtualnej (klient i serwer Redis w pamięci podręcznej Azure), aby uzyskać wyższy możliwości przepustowość, zmniejszyć czas transferu danych dla większych odpowiedzi. Pobieranie większej przepustowości, po prostu serwera lub tylko na kliencie może nie być wystarczająca. Pomiar użycia przepustowości i porównać go z możliwości rozmiaru maszyny Wirtualnej aktualnie zainstalowana.
3. Zwiększenie liczby `ConnectionMultiplexer` obiektów użycia i działanie okrężne żądania za pośrednictwem różnych połączeń.

### <a name="what-happened-to-my-data-in-redis"></a>Co się stało z moimi danymi w usłudze Redis?
#### <a name="problem"></a>Problem
Dla niektórych oczekiwanych danych w pamięci podręcznej Azure Moje wystąpienia pamięci podręcznej Redis, ale go nie wydaje się, że ma być.

#### <a name="resolution"></a>Rozwiązanie
Zobacz [co się stało z moimi danymi w usłudze Redis?](https://gist.github.com/JonCole/b6354d92a2d51c141490f10142884ea4#file-whathappenedtomydatainredis-md) możliwe przyczyny i rozwiązania.

## <a name="server-side-troubleshooting"></a>Rozwiązywanie problemów po stronie serwera
W tej sekcji omówiono rozwiązywanie problemów, które występują ze względu na stan na serwerze pamięci podręcznej.

* [Wykorzystanie pamięci na serwerze](#memory-pressure-on-the-server)
* [Wysokie użycie procesora CPU / obciążenie serwera](#high-cpu-usage-server-load)
* [Przekroczona przepustowość po stronie serwera](#server-side-bandwidth-exceeded)

### <a name="memory-pressure-on-the-server"></a>Wykorzystanie pamięci na serwerze
#### <a name="problem"></a>Problem
Wykorzystanie pamięci po stronie serwera prowadzi do wszelkiego rodzaju problemów z wydajnością, które mogą opóźnić proces przetwarzania żądania. Gdy liczba trafień wykorzystanie pamięci, system zwykle ma do danych strony od ilości pamięci fizycznej w pamięci wirtualnej, która znajduje się na dysku. To *powodujący błąd strony* sprawia, że znacznie spowolnić system. Istnieje kilka możliwych przyczyn to wykorzystania pamięci: 

1. Wypełnienie pamięci podręcznej w celu pełnego wykorzystania możliwości danych. 
2. Redis Cache ma do czynienia z fragmentacji pamięci wysokiej — najczęściej powodowane przez przechowywanie dużych obiektów (Redis zoptymalizowana pod kątem małych obiektów — zobacz [co to jest idealna wartość zakresem rozmiar pamięci podręcznej redis? 100 KB jest za duży? ](https://groups.google.com/forum/#!searchin/redis-db/size/redis-db/n7aa2A4DZDs/3OeEPHSQBAAJ) uzyskać szczegółowe informacje). 

#### <a name="measurement"></a>Miara
Redis Cache udostępnia dwie metryki, które mogą pomóc zidentyfikować ten problem. Pierwsza to `used_memory` , a drugi to `used_memory_rss`. [Te metryki](cache-how-to-monitor.md#available-metrics-and-reporting-intervals) są dostępne w witrynie Azure Portal lub za pośrednictwem [Redis informacje](https://redis.io/commands/info) polecenia.

#### <a name="resolution"></a>Rozwiązanie
Istnieje kilka możliwych zmian, wchodzące w celu zapewnienia dobrej kondycji zużycie pamięci:

1. [Konfigurowanie zasad pamięci](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) i ustaw czas wygaśnięcia na klucze. Ta konfiguracja może nie być wystarczające, jeśli masz fragmentacji.
2. [Skonfiguruj wartość maxmemory zastrzeżone](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) jest wystarczająco duży, aby zrekompensować fragmentacji pamięci.
3. Podziel duże obiekty pamięci podręcznej na mniejsze powiązane obiekty.
4. [Skala](cache-how-to-scale.md) na większy rozmiar pamięci podręcznej.
5. Jeśli używasz [cache w warstwie premium przy użyciu klastra pamięci podręcznej Redis włączone](cache-how-to-premium-clustering.md), możesz [zwiększyć liczbę fragmentów](cache-how-to-premium-clustering.md#change-the-cluster-size-on-a-running-premium-cache).

### <a name="high-cpu-usage--server-load"></a>Wysokie użycie procesora CPU / obciążenie serwera
#### <a name="problem"></a>Problem
Wysokie użycie procesora CPU może oznaczać, że może zakończyć się niepowodzeniem po stronie klienta do przetwarzania odpowiedzi z pamięci podręcznej Redis w odpowiednim czasie, nawet jeśli Redis wysyłane odpowiedzi szybko.

#### <a name="measurement"></a>Miara
Monitorowanie użycia szerokiego procesor CPU systemu za pośrednictwem witryny Azure Portal lub za pomocą licznika wydajności skojarzonego. Uważaj nie monitorował *procesu* procesora CPU, ponieważ jeden proces może mieć niskie użycie procesora CPU w tym samym czasu tego całego systemu Procesora może być wysokie. Obserwuj wzrostów użycia procesora CPU, które odnoszą się do przekroczenia limitu czasu.

#### <a name="resolution"></a>Rozwiązanie
* Przejrzyj wszelkie opinie i alerty wymienione w [pamięci podręcznej Azure redis Cache Advisor](cache-configure.md#azure-cache-for-redis-advisor).
* Sprawdź również inne zalecenia przedstawione w tym temacie i [najlepsze rozwiązania dotyczące usługi Azure Redis](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f) aby zobaczyć, jeśli zostały użyte wszystkie opcje do dalszej optymalizacji pamięci podręcznej i klienta. 
* Przegląd [usługi Azure Cache wydajność pamięci podręcznej Redis](cache-faq.md#azure-cache-for-redis-performance) wykresy i zobaczyć, czy użytkownik może w pobliżu górnej wartości progowe Twojej bieżącej warstwy. Jeśli to konieczne, [skalowania](cache-how-to-scale.md) do większych warstwy pamięci podręcznej o większej pojemności procesora CPU. Jeśli już używasz w warstwie Premium, możesz chcieć [skalowanie w poziomie z klastrowaniem](cache-how-to-premium-clustering.md)


### <a name="server-side-bandwidth-exceeded"></a>Przekroczona przepustowość po stronie serwera
#### <a name="problem"></a>Problem
W zależności od rozmiaru wystąpienia pamięci podręcznej mogą mieć ograniczenia na przepustowość sieci, ile mają one dostępne. Jeśli serwer przekracza dostępną przepustowość, dane nie są wysyłane do klienta tak szybko. Taka sytuacja może prowadzić do przekroczenia limitu czasu.

#### <a name="measurement"></a>Miara
Możesz monitorować `Cache Read` metryki, które jest ilość danych odczytu z pamięci podręcznej w MB na sekundę (MB/s) w określonym interwale raportowania. Ta wartość odpowiada przepustowości sieci używanej przez tę pamięć podręczną. Jeśli chcesz skonfigurować alerty dla limity przepustowości sieci po stronie serwera, możesz utworzyć je za pomocą tego `Cache Read` licznika. Porównaj swoje odczyty z wartościami w [tej tabeli](cache-faq.md#cache-performance) limitów obserwowanych przepustowości dla różnych pamięci podręcznej ceny warstwy i rozmiarów.

#### <a name="resolution"></a>Rozwiązanie
Jeśli jesteś spójnie niemal obserwowanych maksymalnej przepustowości dla cenową rozmiar warstwy i pamięci podręcznej, należy wziąć pod uwagę [skalowanie](cache-how-to-scale.md) cen warstwę lub rozmiar, który ma większą przepustowość sieci, przy użyciu wartości w [tej tabeli](cache-faq.md#cache-performance)jako wskazówki.

## <a name="stackexchangeredis-timeout-exceptions"></a>Wyjątków przekroczenia limitu czasu StackExchange.Redis
StackExchange.Redis korzysta z konfiguracji, ustawienie o nazwie `synctimeout` synchronicznej operacji, które mają wartość domyślną 1000 MS. Jeśli wywołanie synchroniczne nie zakończy się w określonym czasie, klient StackExchange.Redis zgłasza błąd upływu limitu czasu, podobny do poniższego przykładu:

    System.TimeoutException: Timeout performing MGET 2728cc84-58ae-406b-8ec8-3f962419f641, inst: 1,mgr: Inactive, queue: 73, qu=6, qs=67, qc=0, wr=1/1, in=0/0 IOCP: (Busy=6, Free=999, Min=2,Max=1000), WORKER (Busy=7,Free=8184,Min=2,Max=8191)


Ten komunikat o błędzie zawiera metryki, które mogą ułatwić wskaż przyczyny oraz możliwe rozwiązania tego problemu. Poniższa tabela zawiera szczegółowe informacje o metrykach komunikat o błędzie.

| Metryka komunikat o błędzie | Szczegóły |
| --- | --- |
| instrukcje powrotu |W ostatnim przedziału czasu: 0 polecenia zostały wydane. |
| mgr |Menedżer gniazda wykonuje `socket.select`, co oznacza, że jest zapytaniem systemu operacyjnego w celu wskazania gniazda, które ma związek; po prostu: czytnik nie aktywnie odczytuje z sieci ponieważ on nie rozmawiamy tu nic wspólnego |
| kolejka |Istnieją 73 łączna liczba operacji w toku |
| qu |6 operacji w toku znajdują się w kolejce niewysłane i nie zostały jeszcze zapisane sieciowego ruchu wychodzącego |
| qs |67 operacji w toku zostały wysłane do serwera, ale odpowiedź nie jest jeszcze dostępna. Odpowiedzi mogą być `Not yet sent by the server` lub `sent by the server but not yet processed by the client.` |
| qc |Zauważyliśmy już odpowiedzi na 0 lub operacji w toku, ale nie zostało oznaczone jako zakończone z powodu oczekiwania na zakończenie pętli |
| wr |Ma aktywnego modułu zapisującego (co oznacza, że 6 niewysłane żądania nie są ignorowane) bajty/activewriters |
| wejście |Nie ma żadnych aktywnych czytelników i zero bajtów dostępnych do odczytu na NIC bajty/activereaders |

### <a name="steps-to-investigate"></a>Kroki, aby zbadać
1. Zgodnie z najlepszym rozwiązaniem jest upewnij się, używają następującego wzorca łączenia podczas korzystania z klienta StackExchange.Redis.

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

1. Upewnij się, że pamięć podręczna Azure dla usługi Redis i aplikację kliencką znajdują się w tym samym regionie platformy Azure. Na przykład użytkownik może się pojawiać przekroczeń limitu czasu gdy pamięć podręczna znajduje się we wschodnim regionie USA, ale klient znajduje się w regionie zachodnie stany USA i żądania nie zakończył się w `synctimeout` interwał lub mogą się pojawiać przekroczeń limitu czasu podczas debugowania z lokalnej maszynie do programowania. 
   
    Zdecydowanie zaleca się mieć pamięci podręcznej i w kliencie, w tym samym regionie platformy Azure. W przypadku scenariusza, który zawiera wywołań między regionami, należy ustawić `synctimeout` interwał na wartość większą niż domyślny interwał 1000 ms, umieszczając `synctimeout` właściwość w parametrach połączenia. W poniższym przykładzie pokazano StackExchange.Azure pamięci podręcznej dla pamięci podręcznej Redis fragment kodu parametrów połączenia z `synctimeout` 2000 MS.
   
        synctimeout=2000,cachename.redis.cache.windows.net,abortConnect=false,ssl=true,password=...
2. Upewnij się, że za pomocą najnowszej wersji [pakietu StackExchange.Redis NuGet](https://www.nuget.org/packages/StackExchange.Redis/). Brak błędów stale zostanie naprawiony w kodzie, aby był bardziej niezawodny dla limitów czasu tak ważne jest posiadanie najnowszej wersji.
3. W przypadku żądań, które są związane wprowadzenie ograniczenia przepustowości na serwerze lub kliencie zajmuje więcej czasu na ich ukończenie i spowodować przekroczeń limitu czasu. Aby zobaczyć, czy Twoje limitu czasu, ze względu na przepustowość sieci na serwerze, zobacz [Przekroczona przepustowość po stronie serwera](#server-side-bandwidth-exceeded). Aby zobaczyć, czy Twoje limitu czasu, ze względu na przepustowość sieci klienta, zobacz [Przekroczona przepustowość po stronie klienta](#client-side-bandwidth-exceeded).
4. Są możesz wprowadzenie procesora CPU związane na serwerze lub na komputerze klienckim?
   
   * Sprawdź, jeśli jesteś wprowadzenie zobowiązany najbardziej obciążających procesor CPU na komputerze klienckim, który może spowodować, że żądania nie został przetworzony w ramach `synctimeout` interwał, w związku z tym, co powoduje przekroczenie limitu czasu. Przejście do klienta o większym rozmiarze lub rozłożeniu obciążenia może pomóc kontrolować ten problem. 
   * Sprawdź, czy pojawiają się procesora CPU związane na serwerze monitorowania `CPU` [Metryka wydajności w pamięci podręcznej](cache-how-to-monitor.md#available-metrics-and-reporting-intervals). Żądań przychodzących trakcie Redis procesora CPU związane może spowodować te żądania, przekroczenie limitu czasu. Aby rozwiązać ten problem, rozkładają obciążenie na wiele fragmentów w pamięci podręcznej — wersja premium lub uaktualnienia do wersji o większym rozmiarze lub warstwie cenowej. Aby uzyskać więcej informacji, zobacz [Przekroczona przepustowość po stronie serwera](#server-side-bandwidth-exceeded).
5. Czy istnieją polecenia dłużej przetwarzania na serwerze? Długie wykonywanie poleceń, które mają długi czas przetwarzania na serwerze usługi redis może spowodować przekroczeń limitu czasu. Oto kilka przykładów poleceń długotrwałe `mget` z dużą liczbą kluczy `keys *` lub źle napisane skryptów lua. Możesz nawiązać połączenie z pamięci podręcznej dla wystąpienia pamięci podręcznej Redis przy użyciu klienta pamięci podręcznej redis — interfejs wiersza polecenia platformy Azure lub użyć [konsolę pamięci podręcznej Redis](cache-configure.md#redis-console) i uruchom [dziennik wolno wykonywanych zapytań](https://redis.io/commands/slowlog) polecenie, aby sprawdzić, czy żądania trwa dłużej niż oczekiwano. Serwer redis i StackExchange.Redis są zoptymalizowane pod kątem wielu małych żądań, a nie mniej dużych żądań. Podział danych na mniejsze części może poprawić rzeczy w tym miejscu. 
   
    Aby uzyskać informacji na temat nawiązywania połączenia z pamięci podręcznej Azure redis Cache punktu końcowego protokołu SSL przy użyciu usługi redis — interfejs wiersza polecenia i programu stunnel, zobacz [ogłoszenie dostawca stanu sesji ASP.NET dla wersji zapoznawczej Redis](https://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx) wpis w blogu. Aby uzyskać więcej informacji, zobacz [dziennik wolno wykonywanych zapytań](https://redis.io/commands/slowlog).
6. Wysokie obciążenie serwera Redis może spowodować przekroczeń limitu czasu. Można monitorować, obciążenie serwera jest monitorowanie `Redis Server Load` [Metryka wydajności w pamięci podręcznej](cache-how-to-monitor.md#available-metrics-and-reporting-intervals). Obciążenie serwera, 100 (wartość maksymalna) oznacza, że serwer redis był zajęty, bez czasu bezczynności, przetwarzanie żądań. Aby zobaczyć, jeśli niektórych żądań zajmują wszystkich funkcji serwera, uruchom polecenie Dziennik wolno wykonywanych zapytań, zgodnie z opisem w poprzednim akapicie. Aby uzyskać więcej informacji, zobacz [wysokie użycie procesora CPU / obciążenie serwera](#high-cpu-usage-server-load).
7. Po stronie klienta, który może to być spowodowane blip sieci była dowolne inne zdarzenie? Sprawdź na kliencie (web, rola procesu roboczego lub Maszynie wirtualnej IaaS), jeśli wystąpiło zdarzenie, takie jak skalowanie liczby wystąpień klienta w górę lub w dół lub wdrażania nowej wersji klienta lub automatyczne skalowanie jest włączone? W naszych testach, znaleźliśmy tego skalowania automatycznego lub skalowania w górę/dół można przyczyny połączenia sieciowego ruchu wychodzącego mogą zostać utracone przez kilka sekund. Kod StackExchange.Redis jest odporna na takie zdarzenia i ponownie nawiąże połączenie. W tym czasie ponowne łączenie wszystkich żądań w kolejce można limit czasu.
8. Czy były poprzedzających kilka małych żądań do usługi Azure Cache dla pamięci podręcznej Redis, który upłynął limit czasu żądania big? Parametr `qs` w błędzie komunikat informujący o tym ile żądań wysłanych z klienta do serwera, ale nie zostały jeszcze przetworzone odpowiedzi. Tę wartość można zachować rośnie, ponieważ StackExchange.Redis korzysta z jednego połączenia TCP i jedną odpowiedź mogą być odczytane tylko w danym momencie. Mimo że upłynął limit czasu operacji pierwsze, nie zatrzymuje danych wysyłanych z serwera, a inne żądania są blokowane, aż do ukończenia żądania dużych przyczyną przekroczenia limitów czasu. Jednym rozwiązaniem jest zminimalizować prawdopodobieństwo przekroczenia limitu czasu, zapewnia, że pamięć podręczna jest wystarczająco duży dla obciążenia i dzielenia dużych wartości na mniejsze części. Inne rozwiązanie możliwe jest użycie puli `ConnectionMultiplexer` obiektów w swoim kliencie, a następnie wybierz co najmniej załadować `ConnectionMultiplexer` wysyłając żądanie nowego. Powinno to zapobiec pojedynczego limitu czasu powoduje innych żądaniach również limit czasu.
9. Jeśli używasz `RedisSessionStateProvider`, upewnij się, limit czasu ponawiania zostały ustawione prawidłowo. `retryTimeoutInMilliseconds` powinien być większy niż `operationTimeoutInMilliseconds`, w przeciwnym razie wystąpić brak ponownych prób. W poniższym przykładzie `retryTimeoutInMilliseconds` jest równa 3000. Aby uzyskać więcej informacji, zobacz [dostawca stanu sesji ASP.NET dla usługi Azure Cache dla usługi Redis](cache-aspnet-session-state-provider.md) i [sposobu korzystania z parametrów konfiguracji dostawcy stanu sesji i dostawca wyjściowej pamięci podręcznej](https://github.com/Azure/aspnet-redis-providers/wiki/Configuration).

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


1. Sprawdź użycie pamięci w usłudze Azure Cache serwera Redis przez [monitorowania](cache-how-to-monitor.md#available-metrics-and-reporting-intervals) `Used Memory RSS` i `Used Memory`. Jeśli zasady eksmisji znajduje się w miejscu, rozpoczyna się Redis wykluczania klucze kiedy `Used_Memory` osiągnie rozmiar pamięci podręcznej. W idealnym przypadku `Used Memory RSS` powinny być tylko nieznacznie wyższe niż `Used memory`. Duża różnica oznacza, że istnieje fragmentacji pamięci (wewnętrznych lub zewnętrznych). Gdy `Used Memory RSS` jest mniejsza niż `Used Memory`, oznacza to część pamięci podręcznej została zapisana przez system operacyjny. Jeśli zamianę ten problem wystąpi, można oczekiwać, że niektóre znaczne opóźnienia. Ponieważ usługi Redis nie ma kontrolę nad jak jego alokacje są mapowane do stron pamięci wysokiej `Used Memory RSS` często jest wynikiem wzrost użycia pamięci. Gdy Redis zwalnia pamięć, pamięć umożliwiającej powrót alokator i alokator może być lub może nie być pamięć powrót do systemu. Może być niezgodność między `Used Memory` zużycie wartości i pamięci podaną przez system operacyjny. Może być ze względu na fakt pamięci został używane i opublikowany w pamięci podręcznej Redis, ale nie udzieliła wstecz do systemu. Aby ułatwić uniknięcie problemów z pamięcią, należy wykonać następujące czynności:
   
   * Uaktualnij do większego rozmiaru pamięci podręcznej, tak, aby nie zostały uruchomione w odniesieniu do ograniczenia ilości pamięci w systemie.
   * Ustaw czas wygaśnięcia na kluczach, tak, aby aktywnie obrazuje starsze wartości.
   * Monitor `used_memory_rss` metryki w pamięci podręcznej. Ta wartość koloru zbliża się rozmiaru pamięci podręcznej, prawdopodobnie wykonywanych problemy z wydajnością. Rozmieść dane w wielu fragmentów, jeśli są przy użyciu cache w wersji premium lub uaktualnienie do większego rozmiaru pamięci podręcznej.
   
   Aby uzyskać więcej informacji, zobacz [wykorzystanie pamięci na serwerze](#memory-pressure-on-the-server).

## <a name="additional-information"></a>Dodatkowe informacje
* [Jakie usługi Azure Cache oferty pamięci podręcznej Redis i rozmiaru należy używać?](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use)
* [Jak testu wydajności i przetestować wydajność przepełnieniu pamięci podręcznej?](cache-faq.md#how-can-i-benchmark-and-test-the-performance-of-my-cache)
* [Jak uruchomić polecenia Redis](cache-faq.md#how-can-i-run-redis-commands)
* [Jak monitorować pamięć podręczna systemu Azure dla usługi Redis](cache-how-to-monitor.md)

