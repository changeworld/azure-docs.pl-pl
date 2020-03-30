---
title: Rozwiązywanie problemów z limitami czasu usługi Azure Cache for Redis
description: Dowiedz się, jak rozwiązać typowe problemy z limitem czasu z pamięcią podręczną Azure dla programu Redis, takie jak poprawki serwera redis i wyjątki limitu czasu StackExchange.Redis.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 10/18/2019
ms.openlocfilehash: 4b8cfed883ffef780de2e82e3f309e97bcb5515c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79278248"
---
# <a name="troubleshoot-azure-cache-for-redis-timeouts"></a>Rozwiązywanie problemów z limitami czasu usługi Azure Cache for Redis

W tej sekcji omówiono rozwiązywanie problemów z limitem czasu, które występują podczas łączenia się z usługą Azure Cache for Redis.

- [Poprawki serwera Redis](#redis-server-patching)
- [Wyjątki limitu czasu StackExchange.Redis](#stackexchangeredis-timeout-exceptions)

> [!NOTE]
> Kilka kroków rozwiązywania problemów w tym przewodniku zawiera instrukcje uruchamiania poleceń Redis i monitorowania różnych metryk wydajności. Aby uzyskać więcej informacji i instrukcji, zobacz artykuły w sekcji [Dodatkowe informacje.](#additional-information)
>

## <a name="redis-server-patching"></a>Poprawki serwera Redis

Usługa Azure Cache for Redis regularnie aktualizuje oprogramowanie serwera w ramach funkcji usługi zarządzanej, które zapewnia. Ta [łatanie](cache-failover.md) odbywa się w dużej mierze za sceną. Podczas pracy awaryjnej, gdy węzły serwera Redis są łatane, redis klientów podłączonych do tych węzłów mogą wystąpić tymczasowe limity czasu, jak połączenia są przełączane między tymi węzłami. Zobacz [Jak przewijanie awaryjne wpływa na moją aplikację kliencką,](cache-failover.md#how-does-a-failover-affect-my-client-application) aby uzyskać więcej informacji na temat efektów ubocznych, które mogą mieć na aplikacji i jak można poprawić obsługę zdarzeń poprawek.

## <a name="stackexchangeredis-timeout-exceptions"></a>Wyjątki limitu czasu StackExchange.Redis

StackExchange.Redis używa ustawienia konfiguracji `synctimeout` nazwanego dla operacji synchronicznych o domyślnej wartości 1000 ms. Jeśli wywołanie synchroniczne nie zostanie ukończone w tym czasie, stackExchange.Redis klient zgłasza błąd limitu czasu podobny do następującego przykładu:

    System.TimeoutException: Timeout performing MGET 2728cc84-58ae-406b-8ec8-3f962419f641, inst: 1,mgr: Inactive, queue: 73, qu=6, qs=67, qc=0, wr=1/1, in=0/0 IOCP: (Busy=6, Free=999, Min=2,Max=1000), WORKER (Busy=7,Free=8184,Min=2,Max=8191)

Ten komunikat o błędzie zawiera metryki, które mogą pomóc wskazać przyczynę i możliwe rozwiązanie problemu. Poniższa tabela zawiera szczegółowe informacje na temat metryk komunikatu o błędzie.

| Metryka komunikatu o błędzie | Szczegóły |
| --- | --- |
| Inst |W ostatnim czasie wycinek: wydano 0 poleceń |
| Mgr |Menedżer gniazd robi `socket.select`, co oznacza, że prosi system operacyjny o wskazanie gniazda, które ma coś do zrobienia. Czytelnik nie czyta aktywnie z sieci, ponieważ nie myśli, że jest coś do zrobienia |
| kolejka |W sumie w toku działa 73 operacje |
| Qu |6 operacji w toku znajduje się w kolejce niewysłane i nie zostały jeszcze zapisane w sieci wychodzącej |
| Qs |67 operacji w toku zostały wysłane do serwera, ale odpowiedź nie jest jeszcze dostępna. Odpowiedź może `Not yet sent by the server` być lub`sent by the server but not yet processed by the client.` |
| Qc |0 operacji w toku widziałem odpowiedzi, ale nie zostały jeszcze oznaczone jako kompletne, ponieważ czekają na pętlę zakończenia |
| Wr |Istnieje aktywny pisarz (co oznacza, że 6 niewysłanych żądań nie jest ignorowanych) bajtów / activewriters |
| in |Nie ma aktywnych czytników i zero bajtów są dostępne do odczytu na bajtów karty sieciowej / activereaders |

Aby zbadać możliwe przyczyny, można użyć następujących kroków.

1. Najlepszym rozwiązaniem, upewnij się, że używasz następującego wzorca do łączenia się podczas korzystania z klienta StackExchange.Redis.

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

    Aby uzyskać więcej informacji, zobacz [Łączenie się z pamięcią podręczną przy użyciu stackexchange.Redis](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-the-cache).

1. Upewnij się, że serwer i aplikacja kliencka znajdują się w tym samym regionie na platformie Azure. Na przykład może być coraz limity czasu, gdy pamięć podręczna znajduje się we wschodnich stanach zjednoczonych, ale klient jest w zachodnie stany USA i żądanie nie kończy się w `synctimeout` przedziale czasu lub może być uzyskanie limitów czasu podczas debugowania z komputera lokalnego rozwoju. 

    Zdecydowanie zaleca się mieć pamięć podręczną i klienta w tym samym regionie platformy Azure. Jeśli masz scenariusz, który zawiera wywołania między `synctimeout` regionami, należy ustawić interwał na wartość wyższą `synctimeout` niż domyślny interwał 1000 ms, dołączając właściwość w ciągu połączenia. W poniższym przykładzie pokazano fragment ciągu połączenia dla StackExchange.Redis dostarczone przez `synctimeout` usługę Azure Cache dla redis z 2000 ms.

        synctimeout=2000,cachename.redis.cache.windows.net,abortConnect=false,ssl=true,password=...
1. Upewnij się, że używasz najnowszej wersji [pakietu StackExchange.Redis NuGet](https://www.nuget.org/packages/StackExchange.Redis/). Istnieją błędy stale stałe są naprawiane w kodzie, aby uczynić go bardziej niezawodnym dla limitów czasu, więc posiadanie najnowszej wersji jest ważne.
1. Jeśli żądania są związane ograniczeniami przepustowości na serwerze lub kliencie, trwa dłużej, aby zakończyć i może spowodować przesuw czasu. Aby sprawdzić, czy limit czasu jest z powodu przepustowości sieci na serwerze, zobacz [Ograniczenie przepustowości po stronie serwera](cache-troubleshoot-server.md#server-side-bandwidth-limitation). Aby sprawdzić, czy limit czasu jest z powodu przepustowości sieci klienta, zobacz [Ograniczenie przepustowości po stronie klienta](cache-troubleshoot-client.md#client-side-bandwidth-limitation).
1. Czy procesor jest powiązany z procesorem na serwerze lub na kliencie?

   - Sprawdź, czy jesteś związany przez procesor cpu na kliencie. Wysoki procesor CPU może spowodować, że `synctimeout` żądanie nie zostanie przetworzone w interwale i spowodować przesunie czasu żądania. Przejście do większego rozmiaru klienta lub rozłożenie obciążenia może pomóc w kontrolowaniu tego problemu.
   - Sprawdź, czy procesor jest powiązany z procesorem na serwerze, monitorując [metrykę wydajności pamięci podręcznej](cache-how-to-monitor.md#available-metrics-and-reporting-intervals)procesora . Żądania przychodzące, gdy redis jest związany z procesorem CPU może spowodować, że te żądania do przeciągnięcia czasu. Aby rozwiązać ten warunek, można rozdzielić obciążenie na wiele fragmentów w pamięci podręcznej w warstwie premium lub uaktualnić do większego rozmiaru lub warstwy cenowej. Aby uzyskać więcej informacji, zobacz [Ograniczenie przepustowości po stronie serwera](cache-troubleshoot-server.md#server-side-bandwidth-limitation).
1. Czy na serwerze trwa dużo czasu? Długotrwałe polecenia, które zajmują dużo czasu, aby przetworzyć na serwerze redis może spowodować przesuw czasu. Aby uzyskać więcej informacji na temat długotrwałych poleceń, zobacz [Długotrwałe polecenia](cache-troubleshoot-server.md#long-running-commands). Możesz połączyć się z pamięcią podręczną platformy Azure dla wystąpienia Redis przy użyciu klienta redis-cli lub [konsoli Redis](cache-configure.md#redis-console). Następnie uruchom polecenie [SLOWLOG,](https://redis.io/commands/slowlog) aby sprawdzić, czy istnieją żądania wolniejniż oczekiwano. Redis Server i StackExchange.Redis są zoptymalizowane pod kątem wielu małych żądań, a nie mniej dużych żądań. Dzielenie danych na mniejsze fragmenty może poprawić rzeczy tutaj.

    Aby uzyskać informacje na temat łączenia się z punktem końcowym SSL pamięci podręcznej przy użyciu redis-cli i stunnel, zobacz wpis w blogu [Ogłaszając ASP.NET dostawcy stanu sesji dla wersji Redis Preview](https://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx).
1. Wysokie obciążenie serwera Redis może spowodować przesuw czasu. Obciążenie serwera można monitorować, `Redis Server Load` monitorując [metrykę wydajności pamięci podręcznej](cache-how-to-monitor.md#available-metrics-and-reporting-intervals). Obciążenie serwera 100 (wartość maksymalna) oznacza, że serwer redis był zajęty, bez czasu bezczynności, przetwarzania żądań. Aby sprawdzić, czy niektóre żądania zajmują wszystkie możliwości serwera, uruchom polecenie SlowLog, zgodnie z opisem w poprzednim akapicie. Aby uzyskać więcej informacji, zobacz Wysokie użycie procesora CPU / Obciążenie serwera.
1. Czy po stronie klienta było jakieś inne zdarzenie, które mogło spowodować blip sieci? Typowe zdarzenia obejmują: skalowanie liczby wystąpień klienta w górę lub w dół, wdrażanie nowej wersji klienta lub skalowanie automatyczne włączone. W naszych testach odkryliśmy, że skalowanie automatyczne lub skalowanie w górę/w dół może spowodować utratę wychodzącej łączności sieciowej na kilka sekund. StackExchange.Redis kod jest odporny na takie zdarzenia i łączy się ponownie. Podczas ponownego łączenia, wszystkie żądania w kolejce może limit czasu.
1. Czy istnieje duże żądanie poprzedzające kilka małych żądań do pamięci podręcznej, która się skończyła? Parametr `qs` w komunikacie o błędzie informuje, ile żądań zostało wysłanych z klienta do serwera, ale nie przetworzyli odpowiedzi. Ta wartość może rosnąć, ponieważ StackExchange.Redis używa jednego połączenia TCP i może odczytywać tylko jedną odpowiedź naraz. Mimo że przesunął się limit czasu pierwszej operacji, nie zatrzymuje więcej danych przed wysłaniem do lub z serwera. Inne żądania zostaną zablokowane, dopóki duże żądanie nie zostanie zakończone i może spowodować przesuw czasu. Jednym z rozwiązań jest zminimalizowanie szansy na przesuw czasu pracy, zapewniając, że pamięć podręczna jest wystarczająco duża dla obciążenia i dzielenie dużych wartości na mniejsze fragmenty. Innym możliwym rozwiązaniem jest użycie `ConnectionMultiplexer` puli obiektów w kliencie `ConnectionMultiplexer` i wybierz najmniej załadowany podczas wysyłania nowego żądania. Ładowanie przez wiele obiektów połączenia powinno uniemożliwić pojedynczy limit czasu powodując inne żądania również limit czasu.
1. Jeśli używasz `RedisSessionStateProvider`, upewnij się, że ustawiłeś limit czasu ponawiania poprawnie. `retryTimeoutInMilliseconds`powinny być `operationTimeoutInMilliseconds`wyższe niż , w przeciwnym razie nie wystąpią żadne ponowne ponawianie. W poniższym `retryTimeoutInMilliseconds` przykładzie jest ustawiona na 3000. Aby uzyskać więcej informacji, zobacz [ASP.NET dostawcy stanu sesji dla pamięci podręcznej azure dla programu Redis](cache-aspnet-session-state-provider.md) i jak używać [parametrów konfiguracji dostawcy stanu sesji i dostawcy pamięci podręcznej wyjściowej](https://github.com/Azure/aspnet-redis-providers/wiki/Configuration).

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

1. Sprawdź użycie pamięci na serwerze Usługi Azure `Used Memory`Cache dla serwera Redis, [monitorując](cache-how-to-monitor.md#available-metrics-and-reporting-intervals) `Used Memory RSS` i . Jeśli zasady eksmisji jest w miejscu, Redis rozpoczyna eksmitowanie kluczy po `Used_Memory` osiągnięciu rozmiaru pamięci podręcznej. Idealnie, `Used Memory RSS` powinno być tylko `Used memory`nieznacznie wyższe niż . Duża różnica oznacza, że istnieje fragmentacja pamięci (wewnętrzne lub zewnętrzne). Gdy `Used Memory RSS` jest `Used Memory`mniejsza niż , oznacza to, że część pamięci podręcznej została zamieniona przez system operacyjny. Jeśli ta zamiana występuje, można oczekiwać pewnych istotnych opóźnień. Ponieważ Redis nie ma kontroli nad tym, jak jego alokacje są mapowane na strony pamięci, wysoki `Used Memory RSS` jest często wynikiem skok użycia pamięci. Gdy serwer Redis zwalnia pamięć, alokator pobiera pamięć, ale może lub nie może oddać pamięć z powrotem do systemu. Może występować rozbieżność `Used Memory` między wartością a zużyciem pamięci zgłoszoną przez system operacyjny. Pamięć mogła być używana i wydana przez Redis, ale nie została przekazana systemowi. Aby ograniczyć problemy z pamięcią, można wykonać następujące kroki:

   - Uaktualnić pamięć podręczną do większego rozmiaru, tak aby nie są uruchomione z ograniczeniami pamięci w systemie.
   - Ustaw czas wygaśnięcia kluczy, tak aby starsze wartości były proaktywnie eksmitowane.
   - Monitorowanie `used_memory_rss` metryki pamięci podręcznej. Gdy ta wartość zbliża się do rozmiaru ich pamięci podręcznej, prawdopodobnie zaczniesz widzieć problemy z wydajnością. Rozesłaj dane na wielu fragmentach, jeśli używasz pamięci podręcznej premium lub uaktualnij do większego rozmiaru pamięci podręcznej.

   Aby uzyskać więcej informacji, zobacz [Nacisk pamięci na serwerze Redis](cache-troubleshoot-server.md#memory-pressure-on-redis-server).

## <a name="additional-information"></a>Dodatkowe informacje

- [Rozwiązywanie problemów z usługą Azure Cache for Redis po stronie klienta](cache-troubleshoot-client.md)
- [Rozwiązywanie problemów z usługą Azure Cache for Redis po stronie serwera](cache-troubleshoot-server.md)
- [Jak mogę sprawdzić i przetestować wydajność mojej pamięci podręcznej?](cache-faq.md#how-can-i-benchmark-and-test-the-performance-of-my-cache)
- [Jak monitorować pamięć podręczną platformy Azure dla redis](cache-how-to-monitor.md)
