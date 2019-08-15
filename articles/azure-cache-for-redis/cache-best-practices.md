---
title: Najlepsze rozwiązania dotyczące usługi Azure cache for Redis
description: Dowiedz się, jak efektywnie korzystać z pamięci podręcznej platformy Azure, postępując zgodnie z najlepszymi rozwiązaniami.
services: cache
documentationcenter: na
author: joncole
manager: jhubbard
editor: tysonn
ms.assetid: 3e4905e3-89e3-47f7-8cfb-12caf1c6e50e
ms.service: cache
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: cache
ms.workload: tbd
ms.date: 06/21/2019
ms.author: joncole
ms.openlocfilehash: 6ac4722c1253f97bfb8c232202e24a923c027edf
ms.sourcegitcommit: b12a25fc93559820cd9c925f9d0766d6a8963703
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/14/2019
ms.locfileid: "69018839"
---
# <a name="best-practices-for-azure-cache-for-redis"></a>Najlepsze rozwiązania dotyczące usługi Azure cache for Redis 
Postępując zgodnie z najlepszymi rozwiązaniami, możesz pomóc zmaksymalizować wydajność i ekonomiczne użycie wystąpienia usługi Azure cache for Redis.

## <a name="configuration-and-concepts"></a>Konfiguracja i koncepcje
 * **Użyj warstwy Standardowa lub Premium dla systemów produkcyjnych.**  Warstwa Podstawowa to jednowęzłowy system bez replikacji danych i Umowa SLA. Ponadto należy użyć co najmniej pamięci podręcznej C1.  Pamięć podręczna C0 jest przeznaczona dla prostych scenariuszy tworzenia i testowania, ponieważ mają współużytkowaną rdzeń procesora CPU, małą ilość pamięci i są podatne na problemy "zakłócenia".

 * **Należy pamiętać, że Redis jest magazynem danych w pamięci.**  W [tym artykule](https://gist.github.com/JonCole/b6354d92a2d51c141490f10142884ea4#file-whathappenedtomydatainredis-md) opisano niektóre scenariusze, w których może wystąpić utrata danych.

 * **Opracowywanie systemu w taki sposób, aby mógł obsługiwać Blips połączeń** [ze względu na stosowanie poprawek i trybu failover](https://gist.github.com/JonCole/317fe03805d5802e31cfa37e646e419d#file-azureredis-patchingexplained-md).

 * **Skonfiguruj [ustawienie zarezerwowane maxmemory](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) , aby zwiększyć czas odpowiedzi systemu** w warunkach ciśnienia pamięci.  To ustawienie jest szczególnie ważne w przypadku obciążeń z dużym obciążeniem lub w przypadku przechowywania większych wartości (100 KB lub więcej) w Redis.  Zalecamy rozpoczęcie od 10% rozmiaru pamięci podręcznej, a następnie zwiększenie w przypadku obciążeń z dużą ilością zapisu. Podczas [](cache-how-to-troubleshoot.md#considerations-for-memory-reservations) wybierania wartości zapoznaj się z tematem.

 * **Redis najlepiej sprawdza się w przypadku mniejszych wartości**, więc Rozważ powiększanie większych danych do wielu kluczy.  W [tej dyskusji Redis](https://stackoverflow.com/questions/55517224/what-is-the-ideal-value-size-range-for-redis-is-100kb-too-large/)należy wziąć pod uwagę pewne zagadnienia, które należy uwzględnić uważnie.  Przeczytaj [ten artykuł](cache-how-to-troubleshoot.md#large-requestresponse-size) , aby zapoznać się z przykładowym problemem, który może być spowodowany przez duże wartości.

 * **Znajdź wystąpienie pamięci podręcznej i swoją aplikację w tym samym regionie.**  Połączenie z pamięcią podręczną w innym regionie może znacząco zwiększyć opóźnienia i zmniejszyć niezawodność.  Istnieje możliwość nawiązania połączenia poza platformą Azure, ale nie jest to zalecane, *szczególnie w przypadku korzystania z Redis jako pamięci*podręcznej.  Jeśli używasz Redis jako samego magazynu klucz/wartość, opóźnienie może nie być zasadniczym problemem. 

 * **Ponowne użycie połączeń** — tworzenie nowych połączeń jest kosztowne i zwiększa opóźnienia, tak jak to możliwe. Jeśli zdecydujesz się na utworzenie nowych połączeń, przed ich wydaniem upewnij się, że zostały zamknięte stare połączenia (nawet w językach zarządzanych, takich jak .NET lub Java).

 * **Skonfiguruj bibliotekę klienta tak, aby korzystała z *limitu czasu połączenia* wynoszącego co najmniej 15 sekund**, co zapewnia czas systemowy do połączenia nawet w wyższych warunkach CPU.  Niewielka wartość limitu czasu połączenia nie gwarantuje, że połączenie jest ustanowione w tym czasie.  Jeśli coś się nie powiedzie (wysoki procesor CPU klienta, wysoki procesor CPU i tak dalej), wówczas wartość limitu czasu połączenia zostanie spowodowana błędem połączenia. Takie zachowanie często sprawia, że zła sytuacja jest gorsza.  W przeciwieństwie do krótszych limitów czasu pogłębić problem, wymuszając system, aby ponownie uruchomić proces próby ponownego nawiązania połączenia, co może prowadzić do niepowodzenia > pętli ponawiania próby *połączenia >* . Zwykle zaleca się pozostawienie limitu czasu połączenia co 15 sekund. Lepszym rozwiązaniem jest umożliwienie pomyślnej próby połączenia po 15 lub 20 sekundach od momentu jego szybkiego uruchomienia. Taka pętla ponawiania może spowodować wydłużenie czasu przestoju, niż w przypadku dłuższego wydłużenia systemu.  
     > [!NOTE]
     > Wskazówki te są specyficzne dla *próby połączenia* i nie są związane z czasem oczekiwania na wykonanie *operacji* , takich jak pobieranie lub Ustawianie.
 

 * **Unikaj kosztownych poleceń** — niektóre operacje Redis, takie jak [klucze](https://redis.io/commands/keys), są *bardzo* kosztowne i powinny być nieuniknione.  Aby uzyskać więcej informacji, zobacz [niektóre zagadnienia dotyczące kosztownych poleceń](cache-how-to-troubleshoot.md#expensive-commands)


 
## <a name="memory-management"></a>Zarządzanie pamięcią
Istnieje kilka rzeczy związanych z użyciem pamięci w wystąpieniu serwera Redis, które warto wziąć pod uwagę.  Oto kilka z nich:

 * **Wybierz [zasady](https://redis.io/topics/lru-cache) wykluczania, które działają dla aplikacji.**  Zasady domyślne dla usługi Azure Redis to *volatile-LRU*, co oznacza, że tylko klucze mające ustawioną wartość TTL będą kwalifikować się do wykluczenia.  Jeśli żadne klucze nie mają wartości TTL, system nie wykluczają żadnych kluczy.  Jeśli chcesz, aby system zezwalał na każdy klucz, który ma zostać wykluczony w przypadku wykorzystania pamięci, warto rozważyć zasady *AllKeys-LRU* .

 * **Ustaw wartość wygaśnięcia kluczy.**  Spowoduje to usunięcie kluczy w sposób aktywny zamiast oczekiwania na wykorzystanie pamięci.  Gdy wykluczenie zostanie rozpoczęte ze względu na wykorzystanie pamięci, może to spowodować dodatkowe obciążenie serwera.  Aby uzyskać więcej informacji, zobacz dokumentację poleceń [wygaśnięcia](https://redis.io/commands/expire) i [ExpireAt](https://redis.io/commands/expireat) .
 
## <a name="client-library-specific-guidance"></a>Wskazówki dotyczące biblioteki klienta
 * [StackExchange. Redis (.NET)](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-stackexchange-redis-md)
 * [Java — którego klienta należy użyć?](https://gist.github.com/warrenzhu25/1beb02a09b6afd41dff2c27c53918ce7#file-azure-redis-java-best-practices-md)
 * [Sałata (Java)](https://gist.github.com/warrenzhu25/181ccac7fa70411f7eb72aff23aa8a6a#file-azure-redis-lettuce-best-practices-md)
 * [Jedis (Java)](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-java-jedis-md)
 * [Node.js](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-node-js-md)
 * [PHP](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-php-md)
 * [Dostawca stanu sesji Asp.Net](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-session-state-provider-md)


## <a name="when-is-it-safe-to-retry"></a>Kiedy można bezpiecznie ponowić próbę?
Niestety, nie ma żadnej prostej odpowiedzi.  Każda aplikacja musi zdecydować, jakie operacje mogą być ponawiane, a które nie.  Każda operacja ma inne wymagania i zależności między kluczami.  Oto kilka rzeczy, które można wziąć pod uwagę:

 * Można pobrać błędy po stronie klienta, nawet jeśli Redis pomyślnie uruchomił polecenie, które prosi o jego uruchomienie.  Na przykład:
     - Limity czasu to koncepcje po stronie klienta.  Jeśli operacja osiągnęła serwer, na serwerze zostanie uruchomione polecenie, nawet jeśli klient nawiąże oczekiwanie.  
     - Gdy w połączeniu z gniazdem wystąpi błąd, nie można sprawdzić, czy operacja rzeczywiście została uruchomiona na serwerze.  Na przykład błąd połączenia może wystąpić po przetworzeniu żądania przez serwer, ale przed odebraniem odpowiedzi przez klienta.
 *  Jak reaguje moja aplikacja, jeśli przypadkowo uruchamiasz tę samą operację dwukrotnie?  Na przykład, co zrobić, jeśli zwiększę liczbę całkowitą dwa razy, a nie tylko raz?  Czy moja aplikacja zapisuje się w tym samym kluczu z wielu miejsc?  Co zrobić, jeśli moja logika ponawiania zastępuje wartość ustawioną przez inną część mojej aplikacji?

Jeśli chcesz przetestować działanie kodu w warunkach błędów, rozważ użycie [funkcji ponownego uruchamiania](cache-administration.md#reboot). Pozwala to zobaczyć, jak Blips połączeń wpływa na aplikację.

## <a name="performance-testing"></a>Testowanie wydajności
 * **Zacznij od korzystania `redis-benchmark.exe`**  z programu, aby uzyskać dostęp do możliwej przepływności/opóźnienia przed napisaniem własnych testów wydajności.  Dokumentację Redis-testową można [znaleźć tutaj](https://redis.io/topics/benchmarks).  Należy pamiętać, że Redis-testowe nie obsługuje protokołu SSL, dlatego przed uruchomieniem testu należy [włączyć port bez protokołu SSL za pośrednictwem portalu](cache-configure.md#access-ports) .  [Zgodną z systemem Windows wersją programu Redis-Benchmark. exe można znaleźć tutaj](https://github.com/MSOpenTech/redis/releases)
 * Maszyna wirtualna klienta użyta do testowania powinna znajdować się **w tym samym regionie** co wystąpienie pamięci podręcznej Redis.
 * **Zalecamy używanie serii maszyn wirtualnych Dv2** dla klienta, ponieważ mają one lepszy sprzęt i dają najlepsze wyniki.
 * Upewnij się, że używana maszyna wirtualna klienta ma **co najmniej tyle obliczeń i przepustowości* jako przetestowanej pamięci podręcznej. 
 * Jeśli korzystasz z systemu Windows, **Włącz opcję wirtualnego skalowania** na komputerze klienckim.  [Zobacz tutaj, aby uzyskać szczegółowe informacje](https://technet.microsoft.com/library/dn383582(v=ws.11).aspx).  Przykładowy skrypt programu PowerShell:
     >PowerShell-ExecutionPolicy bez ograniczeń Enable-NetAdapterRSS-Name (Get-ServiceAdapter). Nazwij 
     
 * **Rozważ użycie wystąpień usługi Redis w warstwie Premium**.  Te rozmiary pamięci podręcznej będą mieć większe opóźnienia sieci i przepływność, ponieważ działają na lepszym sprzęcie dla obu procesorów i sieci.
 
     > [!NOTE]
     > Nasze obserwowane wyniki wydajności są [publikowane w tym miejscu](cache-faq.md#azure-cache-for-redis-performance) dla odwołania.   Należy również pamiętać, że protokół SSL/TLS dodaje pewne obciążenie, dzięki czemu możesz uzyskać różne opóźnienia i/lub przepływność, jeśli używasz szyfrowania transportowego.
 
### <a name="redis-benchmark-examples"></a>Redis — przykłady testów porównawczych
**Konfiguracja przed testami**: Spowoduje to przygotowanie wystąpienia pamięci podręcznej z danymi wymaganymi dla poleceń testowania opóźnienia i przepływności wymienionych poniżej.
> Redis-Benchmark. exe-h yourcache.redis.cache.windows.net-a yourAccesskey-t SET-n 10-d 1024 

**Aby przetestować opóźnienie**: Spowoduje to przetestowanie żądań GET przy użyciu ładunku o mocy 1K.
> Redis-Benchmark. exe-h yourcache.redis.cache.windows.net-a yourAccesskey-t GET-d 1024-P 50-c 4

**Aby przetestować przepływność:** Powoduje to użycie potokowych żądań GET z obciążeniem 1K.
> Redis-Benchmark. exe-h yourcache.redis.cache.windows.net-a yourAccesskey-t GET-n 1000000-d 1024-P 50-c 50
