---
title: Najlepsze rozwiązania dotyczące usługi Azure Cache for Redis
description: Dowiedz się, jak skutecznie korzystać z pamięci podręcznej Azure dla redis, postępając zgodnie z tymi najlepszymi rozwiązaniami.
author: joncole
ms.service: cache
ms.topic: conceptual
ms.date: 01/06/2020
ms.author: joncole
ms.openlocfilehash: 105a3996753a1d1c2d71846cc8bad574e4498acf
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80478612"
---
# <a name="best-practices-for-azure-cache-for-redis"></a>Najlepsze rozwiązania dotyczące usługi Azure Cache for Redis 
Postępujące zgodnie z tymi najlepszymi rozwiązaniami, można pomóc zmaksymalizować wydajność i opłacalne korzystanie z pamięci podręcznej platformy Azure dla wystąpienia Redis.

## <a name="configuration-and-concepts"></a>Konfiguracja i koncepcje
 * **Użyj warstwy Standardowa lub Premium dla systemów produkcyjnych.**  Warstwa Podstawowa to system z jednym węzłem bez replikacji danych i bez umowy SLA. Ponadto można użyć przynajmniej pamięci podręcznej C1.  Pamięci podręczne C0 są przeznaczone do prostych scenariuszy deweloperskich/testowych, ponieważ mają wspólny rdzeń procesora, mało pamięci i są podatne na problemy "hałaśliwego sąsiada".

 * **Należy pamiętać, że Redis jest magazynem danych w pamięci.**  [W tym artykule](cache-troubleshoot-data-loss.md) opisano niektóre scenariusze, w których może wystąpić utrata danych.

 * **Opracowanie systemu w taki sposób, że może obsługiwać blips połączenia** [z powodu poprawek i pracy awaryjnej](cache-failover.md).

 * **Skonfiguruj [ustawienie zarezerwowane dla maxmemory,](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) aby poprawić responsywność systemu** w warunkach ciśnienia pamięci.  Wystarczające ustawienie rezerwacji jest szczególnie ważne w przypadku obciążeń z dużą ilością zapisu lub w przypadku przechowywania większych wartości (100 KB lub więcej) w redis. Należy rozpocząć od 10% rozmiaru pamięci podręcznej i zwiększyć ten procent, jeśli masz obciążenia dużo zapisu.

 * **Redis działa najlepiej z mniejszymi wartościami,** więc rozważ posiekanie większych danych na wiele klawiszy.  W [tej dyskusji Redis,](https://stackoverflow.com/questions/55517224/what-is-the-ideal-value-size-range-for-redis-is-100kb-too-large/)niektóre uwagi są wymienione, które należy dokładnie rozważyć.  Przeczytaj [ten artykuł,](cache-troubleshoot-client.md#large-request-or-response-size) aby zapoznać się z przykładowym problemem, który może być spowodowany przez duże wartości.

 * **Zlokalizuj wystąpienie pamięci podręcznej i aplikację w tym samym regionie.**  Łączenie się z pamięcią podręczną w innym regionie może znacznie zwiększyć opóźnienie i ograniczyć niezawodność.  Chociaż można połączyć się spoza platformy Azure, nie jest zalecane, *zwłaszcza w przypadku korzystania z Redis jako pamięci podręcznej.*  Jeśli używasz Redis jako tylko klucz/magazyn wartości, opóźnienie może nie być głównym problemem. 

 * **Ponowne użycie połączeń.**  Tworzenie nowych połączeń jest kosztowne i zwiększa opóźnienia, więc ponownie używać połączeń jak najwięcej. Jeśli zdecydujesz się utworzyć nowe połączenia, przed ich zwolnieniem zamknij stare połączenia (nawet w językach pamięci zarządzanej, takich jak .NET lub Java).

 * **Skonfiguruj bibliotekę klienta tak, aby używała *limitu czasu połączenia* co najmniej 15 sekund,** co daje czas systemowy na połączenie nawet w wyższych warunkach procesora.  Mała wartość limitu czasu połączenia nie gwarantuje, że połączenie jest ustanawiane w tym przedziale czasu.  Jeśli coś pójdzie nie tak (wysoki procesor klienta, wysoki procesor serwera i tak dalej), a następnie krótki limit czasu połączenia spowoduje próbę połączenia zakończyć się niepowodzeniem. To zachowanie często pogarsza złą sytuację.  Zamiast pomagać, krótsze limity czasu pogłębiają problem, zmuszając system do ponownego uruchomienia procesu próby ponownego połączenia, co może prowadzić do *połączenia -> nie powiedzie się -> pętli ponawiania.* Ogólnie zaleca się pozostawienie limitu czasu połączenia na 15 sekund lub więcej. Lepiej pozwolić, aby próba połączenia powiodła się po 15 lub 20 sekundach, niż aby szybko się nie powiodła, tylko ponowić próbę. Taka pętla ponawiania może spowodować, że awaria potrwa dłużej niż jeśli system po prostu trwa dłużej.  
     > [!NOTE]
     > Te wskazówki są specyficzne dla *próby połączenia* i nie są związane z czasem, w którego chcesz poczekać na *operację,* taką jak GET lub SET, aby zakończyć.
 
 * **Unikaj kosztownych operacji** — niektóre operacje Redis, takie jak polecenie [KEYS,](https://redis.io/commands/keys) są *bardzo* drogie i należy ich unikać.  Aby uzyskać więcej informacji, zobacz niektóre zagadnienia dotyczące [długotrwałych poleceń](cache-troubleshoot-server.md#long-running-commands)

 * **Użyj szyfrowania TLS** — usługa Azure Cache for Redis domyślnie wymaga szyfrowanej komunikacji TLS.  TLS wersje 1.0, 1.1 i 1.2 są obecnie obsługiwane.  Jednak TLS 1.0 i 1.1 są na ścieżce do deprecation całej branży, więc należy użyć TLS 1.2, jeśli w ogóle możliwe.  Jeśli biblioteka klienta lub narzędzie nie obsługuje protokołu TLS, włączenie połączeń niezaszyfrowanych można wykonać [za pośrednictwem witryny Azure portal](cache-configure.md#access-ports) lub [interfejsów API zarządzania.](https://docs.microsoft.com/rest/api/redis/redis/update)  W takich przypadkach, gdy szyfrowane połączenia nie są możliwe, zaleca się umieszczenie pamięci podręcznej i aplikacji klienckiej w sieci wirtualnej.  Aby uzyskać więcej informacji o portach używanych w scenariuszu pamięci podręcznej sieci wirtualnej, zobacz tę [tabelę](cache-how-to-premium-vnet.md#outbound-port-requirements).
 
## <a name="memory-management"></a>Zarządzanie pamięcią
Istnieje kilka rzeczy związanych z użyciem pamięci w wystąpieniu serwera Redis, które warto rozważyć.  Oto kilka z nich:

 * **Wybierz [zasady eksmisji,](https://redis.io/topics/lru-cache) która działa dla aplikacji.**  Domyślna zasada dla usługi Azure Redis jest *volatile-lru*, co oznacza, że tylko klucze, które mają ustawioną wartość TTL będą kwalifikować się do eksmisji.  Jeśli żadne klucze nie mają wartości TTL, system nie eksmituje żadnych kluczy.  Jeśli chcesz, aby system pozwalał na eksmisję dowolnego klucza, jeśli jest pod presją pamięci, możesz rozważyć politykę *allkeys-lru.*

 * **Ustaw wartość wygaśnięcia na kluczach.**  Wygaśnięcie spowoduje proaktywne usunięcie kluczy zamiast oczekiwania, aż pojawi się ciśnienie pamięci.  Gdy eksmisja nie kopać z powodu nacisku pamięci, może to spowodować dodatkowe obciążenie serwera.  Aby uzyskać więcej informacji, zobacz dokumentację dla poleceń [EXPIRE](https://redis.io/commands/expire) i [EXPIREAT.](https://redis.io/commands/expireat)
 
## <a name="client-library-specific-guidance"></a>Wskazówki dotyczące biblioteki klienta
 * [StackExchange.Redis (.NET)](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-stackexchange-redis-md)
 * [Java — którego klienta należy używać?](https://gist.github.com/warrenzhu25/1beb02a09b6afd41dff2c27c53918ce7#file-azure-redis-java-best-practices-md)
 * [Sałata (Java)](https://gist.github.com/warrenzhu25/181ccac7fa70411f7eb72aff23aa8a6a#file-azure-redis-lettuce-best-practices-md)
 * [Jedis (Java)](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-java-jedis-md)
 * [Node.js](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-node-js-md)
 * [PHP](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-php-md)
 * [Dostawca stanu Asp.Net sesji](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-session-state-provider-md)


## <a name="when-is-it-safe-to-retry"></a>Kiedy można bezpiecznie ponowić próbę?
Niestety, nie ma łatwej odpowiedzi.  Każda aplikacja musi zdecydować, jakie operacje mogą być ponowione, a które nie.  Każda operacja ma różne wymagania i zależności między kluczami.  Oto kilka rzeczy, które możesz rozważyć:

 * Można uzyskać błędy po stronie klienta, mimo że Redis pomyślnie uruchomił polecenie, które poprosiłeś go o uruchomienie.  Przykład:
     - Limity czasu są koncepcją po stronie klienta.  Jeśli operacja dotarła do serwera, serwer uruchomi polecenie, nawet jeśli klient z rezygnuje z oczekiwania.  
     - Gdy wystąpi błąd w połączeniu gniazda, nie jest możliwe, aby wiedzieć, czy operacja faktycznie uruchomiony na serwerze.  Na przykład błąd połączenia może się zdarzyć po serwer przetworzył żądanie, ale przed klient odbiera odpowiedź.
 *  Jak reaguje moja aplikacja, jeśli przypadkowo uruchomię tę samą operację dwa razy?  Na przykład co zrobić, jeśli zwiększać liczbę całkowitą dwa razy zamiast raz?  Czy moja aplikacja pisze do tego samego klucza z wielu miejsc?  Co zrobić, jeśli moja logika ponawiania próby zastąpi wartość ustawioną przez inną część mojej aplikacji?

Jeśli chcesz przetestować, jak kod działa w warunkach błędu, należy rozważyć użycie [funkcji restartu](cache-administration.md#reboot). Ponowne uruchomienie pozwala zobaczyć, jak blips połączenia wpływają na aplikację.

## <a name="performance-testing"></a>Testowanie wydajności
 * **Zacznij od `redis-benchmark.exe` użycia,** aby uzyskać odczucie możliwe przepływności/opóźnienia przed napisaniu własnych testów perf.  Dokumentację redis-benchmark można [znaleźć tutaj](https://redis.io/topics/benchmarks).  Należy zauważyć, że redis-benchmark nie obsługuje protokołu TLS, więc przed uruchomieniem testu należy [włączyć port nietls za pośrednictwem portalu.](cache-configure.md#access-ports)  [Wersja redis-benchmark.exe zgodna z systemem Windows znajduje się tutaj](https://github.com/MSOpenTech/redis/releases)
 * Maszyna wirtualna klienta używana do testowania powinna znajdować się **w tym samym regionie co** wystąpienie pamięci podręcznej Redis.
 * **Zalecamy korzystanie z serii Dv2 VM** dla klienta, ponieważ mają one lepszy sprzęt i dadzą najlepsze wyniki.
 * Upewnij się, że używana maszyna wirtualna klienta ma **co najmniej tyle mocy obliczeniowej i przepustowości,* co testowana pamięć podręczna. 
 * **Włącz usługę VRSS** na komputerze klienckim, jeśli korzystasz z systemu Windows.  [Zobacz tutaj, aby uzyskać szczegółowe informacje](https://technet.microsoft.com/library/dn383582(v=ws.11).aspx).  Przykładowy skrypt programu PowerShell:
     >PowerShell -ExecutionPolicy Nieograniczone Enable-NetAdapterRSS -Name (Get-NetAdapter). Nazwa 
     
 * **Rozważ użycie instancji Redis warstwy Premium**.  Te rozmiary pamięci podręcznej będą miały lepsze opóźnienie sieci i przepływność, ponieważ są uruchomione na lepszym sprzęcie dla procesora CPU i sieci.
 
     > [!NOTE]
     > Nasze zaobserwowane wyniki wydajności są [publikowane tutaj](cache-faq.md#azure-cache-for-redis-performance) dla twojego odniesienia.   Należy również pamiętać, że SSL/TLS dodaje pewne obciążenie, więc może pojawić się różne opóźnienia i/lub przepływności, jeśli używasz szyfrowania transportu.
 
### <a name="redis-benchmark-examples"></a>Przykłady redis-benchmark
**Konfiguracja wstępna:** Przygotuj wystąpienie pamięci podręcznej z danymi wymaganymi dla poleceń testowania opóźnienia i przepływności wymienionych poniżej.
> redis-benchmark.exe -h yourcache.redis.cache.windows.net -a twójAccesskey -t SET -n 10 -d 1024 

**Aby przetestować opóźnienie:** Test get żądań przy użyciu ładunku 1k.
> redis-benchmark.exe -h yourcache.redis.cache.windows.net -a twójAccesskey -t GET -d 1024 -P 50 -c 4

**Aby przetestować przepływność:** Pipelined żądania GET z ładunkiem 1k.
> redis-benchmark.exe -h yourcache.redis.cache.windows.net -a twójAccesskey -t GET -n 1000000 -d 1024 -P 50 -c 50
