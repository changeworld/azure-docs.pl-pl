---
title: Jak rozwiązywać problemy z pamięcią podręczną Redis Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak rozwiązać typowe problemy z pamięcią podręczną Redis Azure.
services: redis-cache
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
ms.assetid: 928b9b9c-d64f-4252-884f-af7ba8309af6
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: na
ms.topic: article
ms.date: 01/06/2017
ms.author: wesmc
ms.openlocfilehash: e5f6f423697d90e889ebde2cd203891e34278b3c
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/03/2018
ms.locfileid: "28984575"
---
# <a name="how-to-troubleshoot-azure-redis-cache"></a>Jak rozwiązywać problemy z pamięcią podręczną Redis Azure
Ten artykuł zawiera wskazówki dotyczące rozwiązywania problemów z następujących kategorii problemy z pamięcią podręczną Redis Azure.

* [Rozwiązywanie problemów po stronie klienta](#client-side-troubleshooting) — ta sekcja zawiera wskazówki dotyczące identyfikowania i rozwiązywania problemów spowodowanych aplikacji nawiązywania połączenia z pamięcią podręczną Redis Azure.
* [Rozwiązywanie problemów po stronie serwera](#server-side-troubleshooting) — ta sekcja zawiera wskazówki dotyczące identyfikowania i rozwiązywania problemów spowodował po stronie serwera pamięci podręcznej Redis Azure.
* [Wyjątków przekroczenia limitu czasu w programie StackExchange.Redis](#stackexchangeredis-timeout-exceptions) — ta sekcja zawiera informacje na temat rozwiązywania problemów podczas korzystania z klienta w programie StackExchange.Redis.

> [!NOTE]
> Kilka kroków w tym przewodniku obejmują instrukcji, aby uruchomić polecenia Redis i monitorować różne metryki wydajności. Aby uzyskać więcej informacji oraz instrukcje, zobacz artykuły w [dodatkowe informacje](#additional-information) sekcji.
> 
> 

## <a name="client-side-troubleshooting"></a>Rozwiązywanie problemów po stronie klienta
W tej sekcji omówiono rozwiązywanie problemów występujących ze względu na warunek aplikacji klienta.

* [Wykorzystania pamięci na komputerze klienckim](#memory-pressure-on-the-client)
* [Serii ruchu](#burst-of-traffic)
* [Klient wysokie użycie procesora CPU](#high-client-cpu-usage)
* [Przekroczona przepustowość po stronie klienta](#client-side-bandwidth-exceeded)
* [Rozmiar dużych żądania/odpowiedzi](#large-requestresponse-size)
* [Co się stało z danych w pamięci podręcznej Redis?](#what-happened-to-my-data-in-redis)

### <a name="memory-pressure-on-the-client"></a>Wykorzystania pamięci na komputerze klienckim
#### <a name="problem"></a>Problem
Wykorzystania pamięci na komputerze klienckim prowadzi do wszystkie rodzaje problemów z wydajnością, które mogą opóźnić proces przetwarzania danych, który został wysłany przez wystąpienie Redis bez opóźnień. Gdy liczba trafień wykorzystania pamięci, system zwykle ma do danych strony od ilości pamięci fizycznej w pamięci wirtualnej, która znajduje się na dysku. To *spowodowaniem błędu strony* spowoduje znaczne spowolnienie jego działania systemu.

#### <a name="measurement"></a>Miary
1. Monitorowanie użycia pamięci na komputerze, aby upewnić się, że nie przekracza ilość dostępnej pamięci. 
2. Monitor `Page Faults/Sec` licznika wydajności. Większość systemów ma kilka błędów stron nawet podczas normalnego działania, tak obserwowania maksymalnej ten licznik wydajności strony błędów, które odpowiadają przekroczeń limitu czasu.

#### <a name="resolution"></a>Rozwiązanie
Uaktualnić klienta na kliencie większy rozmiar maszyny Wirtualnej więcej pamięci lub dig do Twoich wzorców użycia pamięci zmniejszenie zużycia pamięci.

### <a name="burst-of-traffic"></a>Serii ruchu
#### <a name="problem"></a>Problem
Seria ruchu w połączeniu z słaby `ThreadPool` ustawień może spowodować opóźnienia w przetwarzaniu danych już wysłanych przez serwer Redis, ale nie została jeszcze używane po stronie klienta.

#### <a name="measurement"></a>Miary
Monitor jak Twoje `ThreadPool` statystyki zmieniać przy użyciu kodu [podobny](https://github.com/JonCole/SampleCode/blob/master/ThreadPoolMonitor/ThreadPoolLogger.cs). Można również sprawdzić `TimeoutException` wiadomości w programie StackExchange.Redis. Oto przykład:

    System.TimeoutException: Timeout performing EVAL, inst: 8, mgr: Inactive, queue: 0, qu: 0, qs: 0, qc: 0, wr: 0, wq: 0, in: 64221, ar: 0, 
    IOCP: (Busy=6,Free=999,Min=2,Max=1000), WORKER: (Busy=7,Free=8184,Min=2,Max=8191)

W komunikacie poprzedniego istnieją pewne problemy, które mogą być interesujące:

1. Zwróć uwagę, że w `IOCP` sekcji i `WORKER` sekcji masz `Busy` wartość, która jest większa niż `Min` wartości. Ta różnica oznacza, że Twoje `ThreadPool` ustawienia należy dostosowywania.
2. Możesz również sprawdzić `in: 64221`. Ta wartość wskazuje, że 64,211 bajtów odbieranych w warstwie gniazda jądra, ale jeszcze nie zostały przeczytane przez aplikację (na przykład programie StackExchange.Redis). Ta różnica zazwyczaj oznacza to, że aplikacja będą nie jest odczyt danych z sieci tak szybko, jak serwer wysyła go do użytkownika.

#### <a name="resolution"></a>Rozwiązanie
Konfigurowanie sieci [ustawienia puli wątków](https://gist.github.com/JonCole/e65411214030f0d823cb) aby upewnić się, że w puli wątków skaluje szybko w obszarze serii scenariuszy.

### <a name="high-client-cpu-usage"></a>Klient wysokie użycie procesora CPU
#### <a name="problem"></a>Problem
Wysokie użycie procesora CPU na kliencie jest wskazanie, że system nie może nadążyć z pracy, który został poproszony do wykonania. Taka sytuacja oznacza, że klient może się nie powieść przetworzyć odpowiedzi z pamięci podręcznej Redis w odpowiednim czasie, mimo że Redis wysyłane odpowiedzi szybko.

#### <a name="measurement"></a>Miary
Monitorowanie użycia Procesora szeroki systemu za pośrednictwem portalu Azure lub licznika wydajności skojarzonego. Nie można więc monitorować *procesu* Procesora, ponieważ jeden proces może mieć niskie użycie procesora CPU w tym samym czasu ogólną systemu Procesora może być wysokie. Obserwowania maksymalnej wykorzystania Procesora, które odpowiadają przekroczeń limitu czasu. Wyniku wysokiej Procesora może również zostać wyświetlony wysokiego `in: XXX` wartości w `TimeoutException` komunikaty o błędach zgodnie z opisem w [serii ruchu](#burst-of-traffic) sekcji.

> [!NOTE]
> Programie StackExchange.Redis 1.1.603 i nowszych obejmują `local-cpu` metryki w `TimeoutException` komunikaty o błędach. Upewnij się, używając najnowszej wersji [pakietu NuGet w programie StackExchange.Redis](https://www.nuget.org/packages/StackExchange.Redis/). Istnieją błędy stale usunięte w kodzie, aby był bardziej niezawodne dla limitów czasu tak ważne jest posiadanie najnowszej wersji.
> 
> 

#### <a name="resolution"></a>Rozwiązanie
Uaktualnij do większego rozmiaru maszyny Wirtualnej o większej pojemności procesora CPU lub zbadać przyczynę nagłego procesora CPU. 

### <a name="client-side-bandwidth-exceeded"></a>Przekroczona przepustowość po stronie klienta
#### <a name="problem"></a>Problem
W zależności od architektury komputerów klienckich mogą mieć ograniczenia na przepustowość sieci, ile mają one dostępne. Jeśli klient przekracza dostępną przepustowość przez przeładowanie pojemności sieci, następnie danych nie jest wykonywane po stronie klienta tak szybko, jak serwer wysyła go. Taka sytuacja może prowadzić do przekroczenia limitu czasu.

#### <a name="measurement"></a>Miary
Monitorowanie, w jaki sposób zmienić wykorzystanie przepustowości sieci w czasie przy użyciu kodu [podobny](https://github.com/JonCole/SampleCode/blob/master/BandWidthMonitor/BandwidthLogger.cs). Ten kod może nie działać prawidłowo w niektórych środowiskach z ograniczonymi uprawnieniami (np. witryny sieci web platformy Azure).

#### <a name="resolution"></a>Rozwiązanie
Zwiększ rozmiar maszyny Wirtualnej klienta lub zmniejszyć wykorzystanie przepustowości sieci.

### <a name="large-requestresponse-size"></a>Rozmiar dużych żądania/odpowiedzi
#### <a name="problem"></a>Problem
Duże żądanie/odpowiedź może spowodować przekroczeń limitu czasu. Na przykład załóżmy, że skonfigurowany na komputerze klienckim wartość limitu czasu wynosi 1 s. Aplikacja żądań dwa klucze (na przykład "A" i "B"), w tym samym czasie (przy użyciu tego samego połączenia sieci fizycznej). Większość klientów obsługuje "Pipelining" żądań, w taki sposób, że zarówno żądania "A" i "B" są wysyłane umieszczonego na serwerze, jeden po drugim bez oczekiwania na odpowiedzi. Serwer wysyła odpowiedzi w tej samej kolejności. Jeżeli jest wystarczająco duży odpowiedź "A", jego pochłaniają większość limitu czasu dla kolejnych żądań. 

W poniższym przykładzie pokazano, w tym scenariuszu. W tym scenariuszu szybko wysłaniem żądania "A" i "B", serwer rozpocznie szybkie wysyłanie odpowiedzi na "A" i "B", ale z powodu czasu przesyłania danych, 'B' zatrzymywane za żądania i limit mimo że szybko odpowiedź serwera.

    |-------- 1 Second Timeout (A)----------|
    |-Request A-|
         |-------- 1 Second Timeout (B) ----------|
         |-Request B-|
                |- Read Response A --------|
                                           |- Read Response B-| (**TIMEOUT**)



#### <a name="measurement"></a>Miary
Żądanie/odpowiedź jest trudne do pomiaru. Zasadniczo ma Instrumentacja kod klienta do śledzenia dużych żądań i odpowiedzi. 

#### <a name="resolution"></a>Rozwiązanie
1. Redis jest zoptymalizowana pod kątem dużą liczbę małych wartości, zamiast kilku dużych wartości. Preferowanym rozwiązaniem jest podzielić dane powiązane mniejsze wartości. Zobacz [co to jest zakres rozmiar wartość idealna dla pamięci podręcznej redis? 100 KB jest za duży? ](https://groups.google.com/forum/#!searchin/redis-db/size/redis-db/n7aa2A4DZDs/3OeEPHSQBAAJ) post szczegółowe wokół Dlaczego zalecane są mniejsze wartości.
2. Zwiększ rozmiar maszyny wirtualnej (w przypadku klienta i serwera pamięci podręcznej Redis), można pobrać wyższej przepustowości możliwości, zmniejszenie czasu przesyłania danych na większy odpowiedzi. Uzyskiwanie większej przepustowości, tylko z serwera lub tylko na kliencie może nie być wystarczająca. Zmierz wykorzystanie przepustowości sieci i porównaj je z możliwości rozmiaru aktualnie maszyny Wirtualnej.
3. Zwiększ liczbę `ConnectionMultiplexer` obiekty są użycia i okrężnego żądań za pośrednictwem różnych połączeń.

### <a name="what-happened-to-my-data-in-redis"></a>Co się stało z danych w pamięci podręcznej Redis?
#### <a name="problem"></a>Problem
Dla niektórych oczekiwanych danych w mojej wystąpienia pamięci podręcznej Redis Azure, ale nie wydaje się być istnieje.

#### <a name="resolution"></a>Rozwiązanie
Zobacz [co się stało z danych w pamięci podręcznej Redis?](https://gist.github.com/JonCole/b6354d92a2d51c141490f10142884ea4#file-whathappenedtomydatainredis-md) możliwe przyczyny i rozwiązania.

## <a name="server-side-troubleshooting"></a>Rozwiązywanie problemów po stronie serwera
W tej sekcji omówiono rozwiązywanie problemów występujących ze względu na warunek na serwerze pamięci podręcznej.

* [Wykorzystania pamięci na serwerze](#memory-pressure-on-the-server)
* [Wysokie użycie procesora CPU / ładowania serwera](#high-cpu-usage-server-load)
* [Przekroczona przepustowość po stronie serwera](#server-side-bandwidth-exceeded)

### <a name="memory-pressure-on-the-server"></a>Wykorzystania pamięci na serwerze
#### <a name="problem"></a>Problem
Wykorzystania pamięci po stronie serwera prowadzi do wszystkie rodzaje problemów z wydajnością, które mogą opóźnić proces przetwarzania żądania. Gdy liczba trafień wykorzystania pamięci, system zwykle ma do danych strony od ilości pamięci fizycznej w pamięci wirtualnej, która znajduje się na dysku. To *spowodowaniem błędu strony* spowoduje znaczne spowolnienie jego działania systemu. Istnieje kilka możliwych przyczyn tego wykorzystania pamięci: 

1. Wypełnienie pamięci podręcznej, aby pełną pojemność z danymi. 
2. Redis ma do czynienia fragmentacji pamięci wysokiej - Najczęstszą przyczyną przechowywania dużych obiektów (Redis jest zoptymalizowana pod kątem małych obiektów — zobacz [co to jest zakres rozmiar wartość idealna dla pamięci podręcznej redis? 100 KB jest za duży? ](https://groups.google.com/forum/#!searchin/redis-db/size/redis-db/n7aa2A4DZDs/3OeEPHSQBAAJ) post, aby uzyskać szczegółowe informacje). 

#### <a name="measurement"></a>Miary
Redis przedstawia dwa metryk, które mogą ułatwić identyfikację ten problem. Pierwsza to `used_memory` , a druga `used_memory_rss`. [Te metryki](cache-how-to-monitor.md#available-metrics-and-reporting-intervals) są dostępne w portalu Azure lub za pośrednictwem [Redis informacji](http://redis.io/commands/info) polecenia.

#### <a name="resolution"></a>Rozwiązanie
Istnieje kilka możliwych zmian, które pozwala zapewnić użycie pamięci dobrej kondycji:

1. [Skonfiguruj zasady pamięci](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) i ustaw czas wygaśnięcia w Twoje klucze. Ta konfiguracja może nie być wystarczające, jeśli masz fragmentacji.
2. [Skonfiguruj wartość maxmemory zastrzeżone](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) jest wystarczająco duży, aby rozliczyć fragmentacji pamięci.
3. Rozdziel dużych obiektów pamięci podręcznej w mniejszych powiązanych obiektów.
4. [Skala](cache-how-to-scale.md) na większy rozmiar pamięci podręcznej.
5. Jeśli używasz [premium pamięci podręcznej Redis klastra włączone](cache-how-to-premium-clustering.md), możesz [zwiększyć liczbę fragmentów](cache-how-to-premium-clustering.md#change-the-cluster-size-on-a-running-premium-cache).

### <a name="high-cpu-usage--server-load"></a>Wysokie użycie procesora CPU / ładowania serwera
#### <a name="problem"></a>Problem
Wysokie użycie procesora CPU może oznaczać, że może zakończyć się niepowodzeniem po stronie klienta do przetworzenia odpowiedzi z pamięci podręcznej Redis w odpowiednim czasie, mimo że Redis szybko wysyłane odpowiedzi.

#### <a name="measurement"></a>Miary
Monitorowanie użycia Procesora szeroki systemu za pośrednictwem portalu Azure lub licznika wydajności skojarzonego. Nie można więc monitorować *procesu* Procesora, ponieważ jeden proces może mieć niskie użycie procesora CPU w tym samym czasu ogólną systemu Procesora może być wysokie. Obserwowania maksymalnej wykorzystania Procesora, które odpowiadają przekroczeń limitu czasu.

#### <a name="resolution"></a>Rozwiązanie
* Przejrzyj wszelkie wskazówki i alerty wspomnianego [Advisor pamięci podręcznej Redis](cache-configure.md#redis-cache-advisor).
* Sprawdź również inne zalecenia w tym temacie i [najlepsze rozwiązania dotyczące usługi Azure Redis](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f) aby zobaczyć, czy zostały użyte wszystkie opcje, aby jeszcze bardziej zoptymalizować pamięć podręczną i klienta. 
* Przegląd [wydajności pamięci podręcznej Redis Azure](cache-faq.md#azure-redis-cache-performance) wykresów i sprawdź, czy może być pobliżu progów górną warstwę bieżącego. W razie potrzeby [skali](cache-how-to-scale.md) do większych warstwy pamięci podręcznej o większej pojemności procesora CPU. Jeśli korzystasz już z warstwy Premium, możesz chcieć [skalowanie w poziomie z klastra](cache-how-to-premium-clustering.md)


### <a name="server-side-bandwidth-exceeded"></a>Przekroczona przepustowość po stronie serwera
#### <a name="problem"></a>Problem
W zależności od rozmiaru wystąpienia pamięci podręcznej, może mieć ograniczenia na przepustowość sieci, ile mają one dostępne. Jeśli serwer przekracza dostępną przepustowość, dane nie są wysyłane do klienta tak szybko. Taka sytuacja może prowadzić do przekroczenia limitu czasu.

#### <a name="measurement"></a>Miary
Można monitorować `Cache Read` metryki, czyli ilość danych do odczytu z pamięci podręcznej w MB na sekundę (MB/s) w określonym interwale raportowania. Ta wartość odpowiada przepustowości sieci używanej przez tę pamięć podręczną. Jeśli chcesz skonfigurować alerty dla limity przepustowości sieci po stronie serwera, możesz utworzyć je za pomocą tej `Cache Read` licznika. Porównaj z odczyty z wartościami w [tej tabeli](cache-faq.md#cache-performance) limitów obserwowanych przepustowości dla różnych pamięci podręcznej ceny warstw i rozmiary.

#### <a name="resolution"></a>Rozwiązanie
Jeśli użytkownik zawsze w pobliżu obserwowanych maksymalnej przepustowości cenową rozmiar warstwy i pamięci podręcznej, należy wziąć pod uwagę [skalowanie](cache-how-to-scale.md) cenową warstwy lub rozmiar, która ma większą przepustowość sieci, używając wartości w [tej tabeli](cache-faq.md#cache-performance) jako przewodnika.

## <a name="stackexchangeredis-timeout-exceptions"></a>Wyjątków przekroczenia limitu czasu w programie StackExchange.Redis
Programie StackExchange.Redis używa konfiguracji ustawienie o nazwie `synctimeout` synchronicznych operacji, które mają wartość domyślną równą 1000 ms. Jeśli wywołania synchronicznego nie zakończy się w określonym czasie, przez klienta w programie StackExchange.Redis zgłasza błąd limitu czasu podobny do poniższego przykładu:

    System.TimeoutException: Timeout performing MGET 2728cc84-58ae-406b-8ec8-3f962419f641, inst: 1,mgr: Inactive, queue: 73, qu=6, qs=67, qc=0, wr=1/1, in=0/0 IOCP: (Busy=6, Free=999, Min=2,Max=1000), WORKER (Busy=7,Free=8184,Min=2,Max=8191)


Ten komunikat o błędzie zawiera metryki, które pozwalają wskazać przyczynę i możliwe rozwiązania problemu. Poniższa tabela zawiera szczegółowe informacje o metryki komunikat błędu.

| Metryka komunikat błędu | Szczegóły |
| --- | --- |
| inst |W ostatnim przedziału czasu: 0 polecenia zostały wydane |
| mgr |Menedżer gniazda wykonuje `socket.select`, co oznacza, że jest zapytaniem systemu operacyjnego, aby wskazać gniazda, który ma coś zrobić; zasadniczo: czytnik nie aktywnie odczytuje z sieci, ponieważ nie reakcji, coś zrobić |
| Kolejki |73 całkowita operacji w toku |
| qu |6 w trakcie wykonywania operacji w kolejce niewysłane i nie zostały zapisane z siecią ruchu wychodzącego |
| qs |67 operacji w toku zostały wysłane do serwera, ale odpowiedź nie jest jeszcze dostępna. Odpowiedzi mogą być `Not yet sent by the server` lub`sent by the server but not yet processed by the client.` |
| qc |jak już wspomniano odpowiedzi 0 operacji w toku, ale nie została oznaczona jako zakończona z powodu oczekiwania na zakończenie pętli |
| wR |Brak bajty/activewriters active składnika zapisywania (co oznacza, że 6 niewysłane żądania nie są ignorowane) |
| w |Nie ma żadnych aktywnych czytników i zero bajtów są dostępne do odczytu na NIC bajty/activereaders |

### <a name="steps-to-investigate"></a>Kroki w celu zbadania
1. Jako najlepsze rozwiązanie upewnij się, nawiązać podczas korzystania z klienta w programie StackExchange.Redis są przy użyciu następującego wzorca.

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
    ````

    Aby uzyskać więcej informacji, zobacz [Połącz z pamięci podręcznej, za pomocą programie StackExchange.Redis](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-the-cache).

1. Upewnij się, że pamięć podręczna Redis Azure i aplikacja kliencka są w tym samym regionie platformy Azure. Na przykład możesz może występować limity czasu gdy pamięć podręczna jest wschodnie stany USA, ale klient znajduje się w zachodnie stany USA i żądania nie zakończyła się w ciągu `synctimeout` interwał lub użytkownik może uzyskać przekroczeń limitu czasu podczas debugowania z komputera lokalnego rozwoju. 
   
    Zdecydowanie zaleca się ma pamięci podręcznej i na komputerze klienckim, w tym samym regionie Azure. Jeśli scenariusz, który zawiera wywołań między regionu, należy ustawić `synctimeout` interwał na wartość większą niż domyślny interwał 1000 ms, umieszczając w niej `synctimeout` właściwości w parametrach połączenia. W poniższym przykładzie przedstawiono fragment ciągu połączenia programie StackExchange.Redis pamięci podręcznej z `synctimeout` z 2000 ms.
   
        synctimeout=2000,cachename.redis.cache.windows.net,abortConnect=false,ssl=true,password=...
2. Upewnij się, używając najnowszej wersji [pakietu NuGet w programie StackExchange.Redis](https://www.nuget.org/packages/StackExchange.Redis/). Istnieją błędy stale usunięte w kodzie, aby był bardziej niezawodne dla limitów czasu tak ważne jest posiadanie najnowszej wersji.
3. W przypadku żądań pobierania powiązanych przez ograniczenia przepustowości serwera lub klienta, trwa dłużej na ich zakończenie i spowodować przekroczeń limitu czasu. Aby sprawdzić, czy Twoje limitu czasu jest ze względu na przepustowość sieci na serwerze, zobacz [Przekroczona przepustowość po stronie serwera](#server-side-bandwidth-exceeded). Aby sprawdzić, czy Twoje limitu czasu jest ze względu na przepustowość sieci klienta, zobacz [Przekroczona przepustowość po stronie klienta](#client-side-bandwidth-exceeded).
4. Na serwerze lub na komputerze klienckim należy pobierania Procesora powiązanych?
   
   * Sprawdź, jeśli użytkownik pobierania powiązanych przez procesor CPU na komputerze klienckim, który może spowodować, że żądania nie można przetworzyć w ramach `synctimeout` interwał, powodując przekroczenie limitu czasu. Przejście do większego rozmiaru klienta lub dystrybucji obciążenia ułatwiają kontrolowanie ten problem. 
   * Sprawdź, czy występują procesora CPU związane na serwerze monitorowania `CPU` [pamięci podręcznej metryki wydajności](cache-how-to-monitor.md#available-metrics-and-reporting-intervals). Żądań przychodzących podczas Redis jest powiązany Procesora może powodować tych żądań w celu limitu czasu. Aby rozwiązać ten stan, rozpowszechniają obciążenia wielu fragmentów w pamięci podręcznej premium lub uaktualnienie do warstwy cenowej lub większy rozmiar. Aby uzyskać więcej informacji, zobacz [Przekroczona przepustowość po stronie serwera](#server-side-bandwidth-exceeded).
5. Istnieją polecenia biorąc długi czas przetwarzania na serwerze? Czas uruchamiania poleceń, które są tworzone długi czas przetwarzania na serwerze redis może spowodować przekroczeń limitu czasu. Oto kilka przykładów poleceń długotrwała `mget` z dużą liczbę kluczy, `keys *` lub niepoprawnie napisane skrypty lua. Można nawiązać połączenia z wystąpienia pamięci podręcznej Redis Azure za pomocą klienta pamięci podręcznej redis-cli lub użyć [Redis konsoli](cache-configure.md#redis-console) i uruchom [SlowLog](http://redis.io/commands/slowlog) polecenie, aby sprawdzić, czy są żądania trwa dłużej niż oczekiwano. Serwer redis i programie StackExchange.Redis są zoptymalizowane pod kątem wiele żądań małych zamiast mniej dużych żądania. Podział na mniejsze fragmenty danych może zwiększyć rzeczy w tym miejscu. 
   
    Aby uzyskać informacje dotyczące nawiązywania połączenia przy użyciu interfejsu wiersza polecenia redis i stunnel punkt końcowy SSL pamięci podręcznej Redis Azure, zobacz [Announcing ASP.NET dostawcę stanu sesji dla wersji zapoznawczej Redis](http://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx) wpis w blogu. Aby uzyskać więcej informacji, zobacz [SlowLog](http://redis.io/commands/slowlog).
6. Wysokie obciążenie serwera pamięci podręcznej Redis może spowodować przekroczeń limitu czasu. Można monitorować obciążenie serwera monitorowania `Redis Server Load` [pamięci podręcznej metryki wydajności](cache-how-to-monitor.md#available-metrics-and-reporting-intervals). Obciążenie serwera, 100 (wartość maksymalna) oznacza, że serwer redis zostało zajęte, bez czasu bezczynności, przetwarzanie żądań. Aby zobaczyć, jeśli niektórych żądań zajmują wszystkich funkcji serwera, uruchom polecenie SlowLog, zgodnie z opisem w poprzednim akapicie. Aby uzyskać więcej informacji, zobacz [użycie procesora CPU wysoka / Server załadować](#high-cpu-usage-server-load).
7. Po stronie klienta, który może to być spowodowane blip sieci były inne zdarzenia? Sprawdź, czy na kliencie (sieć web, rolę procesu roboczego lub maszyn wirtualnych Iaas) Jeśli było zdarzeń, takich jak skalowanie liczby wystąpień klienta w górę lub w dół lub wdrażania nowej wersji klienta lub automatyczne skalowanie jest włączone? Podczas testów znaleźliśmy tego skalowania automatycznego lub skalowanie w / dół można łączności sieciowej wychodzącego Przyczyna mogą zostać utracone przez kilka sekund. Kod w programie StackExchange.Redis jest odporność na takich zdarzeń i ponownie nawiąże połączenie. W tym czasie ponownego połączenia żądań w kolejce może upłynął limit czasu.
8. Były big poprzedzających kilka małych żądań do pamięci podręcznej Redis, który upłynął limit czasu żądania? Parametr `qs` w dzienniku błędów komunikat informuje, ile żądań zostały wysłane z klienta do serwera, ale odpowiedź nie jest jeszcze przetworzone. Ta wartość może rosnące, ponieważ w programie StackExchange.Redis używa pojedynczego połączenia TCP i może odczytać tylko jedna odpowiedź w czasie. Mimo że przekroczono limit czasu operacji pierwszy, nie zatrzymuje danych wysłanych z serwera, a inne żądania będą zablokowane dopiero po zakończeniu dużego żądania, co powoduje limity czasu. Rozwiązanie polega na zminimalizować ryzyko przekroczeń limitu czasu przez zapewnienie, że pamięć podręczna jest wystarczająco duży dla obciążenia i dzielenie dużych wartości na mniejsze fragmenty. Innym rozwiązaniem możliwe jest korzystanie z puli z `ConnectionMultiplexer` obiektów na kliencie, a następnie wybierz pozycję najmniej załadować `ConnectionMultiplexer` wysyłając żądanie nowego. To powinno zapobiec pojedynczego limitu czasu powoduje innych żądaniach dotyczących również limit czasu.
9. Jeśli używasz `RedisSessionStateprovider`, upewnij się, że limit ponownych prób została poprawnie ustawiona. `retrytimeoutInMilliseconds`powinien być większy niż `operationTimeoutinMilliseonds`, w przeciwnym razie wystąpić brak ponownych prób. W poniższym przykładzie `retrytimeoutInMilliseconds` ustawiono 3000. Aby uzyskać więcej informacji, zobacz [dostawcę stanu sesji ASP.NET dla pamięci podręcznej Redis Azure](cache-aspnet-session-state-provider.md) i [jak używać parametrów konfiguracji dostawcy stanu sesji i dostawcy wyjściowej pamięci podręcznej](https://github.com/Azure/aspnet-redis-providers/wiki/Configuration).

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


1. Sprawdź użycie pamięci na serwerze usługi pamięć podręczna Redis Azure przez [monitorowania](cache-how-to-monitor.md#available-metrics-and-reporting-intervals) `Used Memory RSS` i `Used Memory`. Czy zasady wykluczania znajduje się w miejscu, rozpoczyna się Redis wykluczania kluczy podczas `Used_Memory` osiągnie rozmiar pamięci podręcznej. W idealnym przypadku `Used Memory RSS` powinny być tylko nieznacznie wyższe niż `Used memory`. Duża różnica oznacza brak fragmentacji pamięci (wewnętrzne lub zewnętrzne. Gdy `Used Memory RSS` jest mniejsza niż `Used Memory`, oznacza to część pamięci podręcznej została zapisana przez system operacyjny. Jeśli wystąpi ten wymiany, można oczekiwać, że niektóre znaczne opóźnienia. Ponieważ Redis nie mają kontrolę nad jak alokacje są mapowane do stron pamięci wysokiej `Used Memory RSS` często jest wynikiem kolekcji użycia pamięci. Gdy Redis zwalnia pamięć, pamięć znajduje się wstecz do przydzielania i programu przydzielania może lub może nie być pamięć wstecz do systemu. Może być niezgodność między `Used Memory` zużycie wartość i pamięci podaną przez system operacyjny. Może być ze względu na fakt pamięci został użyty i wydane przez Redis, ale nie podany wstecz do systemu. Aby rozwiązać problemy z pamięcią, można wykonać następujące czynności:
   
   * Uaktualnij do większy rozmiar pamięci podręcznej, tak, aby nie zostały uruchomione sprawdzania ograniczenia ilości pamięci w systemie.
   * Ustaw czas wygaśnięcia na klucze starsze wartości są aktywne wykluczony.
   * Monitor `used_memory_rss` pamięci podręcznej metryki. Gdy ta wartość koloru zbliża się rozmiar pamięci podręcznej, jest prawdopodobne zacząć wyświetlać problemy z wydajnością. Rozpowszechniają danych wielu odłamków przy użyciu usługi pamięć podręczna premium, lub Uaktualnij do większy rozmiar pamięci podręcznej.
   
   Aby uzyskać więcej informacji, zobacz [wykorzystania pamięci na serwerze](#memory-pressure-on-the-server).

## <a name="additional-information"></a>Dodatkowe informacje
* [Której oferty i jakiego rozmiaru usługi Redis Cache mam użyć?](cache-faq.md#what-redis-cache-offering-and-size-should-i-use)
* [Jak testu wydajności i testowania wydajności Moje pamięci podręcznej?](cache-faq.md#how-can-i-benchmark-and-test-the-performance-of-my-cache)
* [Jak uruchomić polecenia Redis?](cache-faq.md#how-can-i-run-redis-commands)
* [Jak monitorować pamięć podręczna Redis Azure](cache-how-to-monitor.md)

