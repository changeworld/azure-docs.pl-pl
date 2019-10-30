---
title: Rozwiązywanie problemów z pamięcią podręczną Azure pod kątem Redis utraty danych | Microsoft Docs
description: Dowiedz się, jak rozwiązywać problemy z utratą danych za pomocą usługi Azure cache for Redis
services: cache
documentationcenter: ''
author: yegu-ms
manager: maiye
editor: ''
ms.assetid: ''
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache
ms.devlang: na
ms.topic: article
ms.date: 10/17/2019
ms.author: yegu
ms.openlocfilehash: 4fee7c84b394e84369b28d2a4191d0e581f3beba
ms.sourcegitcommit: 38251963cf3b8c9373929e071b50fd9049942b37
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/29/2019
ms.locfileid: "73044358"
---
# <a name="troubleshoot-azure-cache-for-redis-data-loss"></a>Rozwiązywanie problemów z usługą Azure cache pod kątem utraty danych Redis

W tej sekcji omówiono sposób diagnozowania rzeczywistych lub postrzeganych strat danych, które mogą wystąpić w usłudze Azure cache for Redis.

- [Częściowa utrata kluczy](#partial-loss-of-keys)
- [Główna lub pełna utrata kluczy](#major-or-complete-loss-of-keys)

> [!NOTE]
> Kilka kroków rozwiązywania problemów zawartych w tym przewodniku zawiera instrukcje dotyczące uruchamiania poleceń Redis i monitorowania różnych metryk wydajności. Aby uzyskać więcej informacji i instrukcje, zapoznaj się z artykułami w sekcji [Informacje dodatkowe](#additional-information) .
>

## <a name="partial-loss-of-keys"></a>Częściowa utrata kluczy

Redis nie usuwa losowo kluczy, gdy zostały zapisane w pamięci. Spowoduje to usunięcie kluczy, jednak w odpowiedzi na zasady wygasania lub wykluczania oraz jawne polecenia usuwania kluczy. Ponadto klucze, które zostały zapisana w węźle głównym w Premium lub standardowej pamięci podręcznej platformy Azure dla usługi Redis, mogą nie być dostępne od razu. Dane są replikowane z serwera głównego do repliki w sposób asynchroniczny i nieblokowany.

Jeśli okaże się, że klucze zniknęły z pamięci podręcznej, można sprawdzić poniższe informacje, aby zobaczyć, co może być przyczyną:

| Przyczyna | Opis |
|---|---|
| [Wygaśnięcie klucza](#key-expiration) | Klucze zostały usunięte z powodu przekroczenia limitu czasu |
| [Wykluczenia klucza](#key-eviction) | Klucze są usuwane w obszarze wykorzystanie pamięci |
| [Usuwanie klucza](#key-deletion) | Klucze są usuwane przez jawne polecenia usuwania |
| [Replikacja asynchroniczna](#async-replication) | Klucze nie są dostępne w replice z powodu opóźnień replikacji danych |

### <a name="key-expiration"></a>Wygaśnięcie klucza

Redis automatycznie usunie klucz, jeśli ma przypisany limit czasu i przeszedł okres. Więcej szczegółów dotyczących wygaśnięcia klucza Redis można znaleźć w dokumentacji poleceń [wygasania](http://redis.io/commands/expire) . Wartości limitu czasu można także ustawić za poorednictwem poleceń [Set](http://redis.io/commands/set), [SETEX](https://redis.io/commands/setex), [GetSet](https://redis.io/commands/getset)i innych \*STORE.

Możesz użyć polecenia info, aby uzyskać [informacje](http://redis.io/commands/info) statystyczne o liczbie wygasłych kluczy. Sekcja *statystyki* zawiera łączną liczbę wygasłych kluczy. Sekcja *przestrzeni* kluczy zawiera dodatkowe informacje o liczbie kluczy z limitami czasu i średnią wartość limitu czasu.

```
# Stats

expired_keys:46583

# Keyspace

db0:keys=3450,expires=2,avg_ttl=91861015336
```

Ponadto można przyjrzeć się metrykom diagnostycznym pamięci podręcznej, aby sprawdzić, czy istnieje korelacja między brakującym kluczem a skokiem w kluczach wygasłych. Zapoznaj się z [dodatkiem](https://gist.github.com/JonCole/4a249477142be839b904f7426ccccf82#appendix) , aby uzyskać informacje dotyczące korzystania z powiadomień lub monitora przestrzeni kluczy w celu debugowania tych typów problemów.

### <a name="key-eviction"></a>Wykluczenia klucza

Redis wymaga miejsca w pamięci do przechowywania danych. Klucze zostaną przeczyszczone, aby zwolnić dostępną pamięć w razie potrzeby. Gdy wartości **used_memory** lub **used_memory_rss** w poleceniu [info](http://redis.io/commands/info) są zgodne ze skonfigurowanym ustawieniem **maxmemory** , Redis rozpocznie wykluczanie kluczy z pamięci na podstawie [zasad pamięci podręcznej](http://redis.io/topics/lru-cache).

Liczbę wykluczonych kluczy można monitorować za pomocą polecenia [info](http://redis.io/commands/info) .

```
# Stats

evicted_keys:13224
```

Ponadto można przyjrzeć się metrykom diagnostycznym pamięci podręcznej, aby sprawdzić, czy istnieje korelacja między brakującym kluczem a skokiem w wykluczonych kluczach. Zapoznaj się z [dodatkiem](https://gist.github.com/JonCole/4a249477142be839b904f7426ccccf82#appendix) , aby uzyskać informacje dotyczące korzystania z powiadomień lub monitora przestrzeni kluczy w celu debugowania tych typów problemów.

### <a name="key-deletion"></a>Usuwanie klucza

Klienci Redis mogą wydać polecenie [del](http://redis.io/commands/del) lub [HDEL](http://redis.io/commands/hdel) , aby jawnie usunąć klucze z Redis. Liczbę operacji usuwania można śledzić za pomocą polecenia [info](http://redis.io/commands/info) . Jeśli polecenia DEL lub HDEL zostały wywołane, zostaną one wyświetlone w sekcji *Commandstats* .

```
# Commandstats

cmdstat_del:calls=2,usec=90,usec_per_call=45.00

cmdstat_hdel:calls=1,usec=47,usec_per_call=47.00
```

### <a name="async-replication"></a>Replikacja asynchroniczna

Dowolna pamięć podręczna platformy Azure dla Redis w warstwie Standardowa lub Premium jest konfigurowana z węzłem głównym i co najmniej jedną repliką. Dane są kopiowane z serwera głównego do repliki asynchronicznie przy użyciu procesu w tle. W witrynie sieci Web [Redis.IO](http://redis.io/topics/replication) opisano, jak działa replikacja danych Redis. W przypadku scenariuszy, w których klienci zapisują Redis często, częściowe utrata danych może być spowodowana faktem, że ta replikacja jest gwarantowana na chwilę. Jeśli na przykład serwer główny ulegnie awarii _po_ zapisaniu przez klienta klucza do niego, ale _zanim_ proces w tle ma szansę wysłania tego klucza do repliki, klucz zostanie utracony, gdy replika przejmuje jako nowy wzorzec.

## <a name="major-or-complete-loss-of-keys"></a>Główna lub pełna utrata kluczy

Jeśli okaże się, że większość lub wszystkie klucze zniknęły z pamięci podręcznej, można sprawdzić poniższe informacje, aby zobaczyć, co może być przyczyną:

| Przyczyna | Opis |
|---|---|
| [Opróżnianie klucza](#key-flushing) | Klucze zostały ręcznie przeczyszczone |
| [Nieprawidłowy wybór bazy danych](#incorrect-database-selection) | Redis jest ustawiona do korzystania z bazy danych innej niż domyślna |
| [Niepowodzenie wystąpienia Redis](#redis-instance-failure) | Serwer Redis jest niedostępny |

### <a name="key-flushing"></a>Opróżnianie klucza

Klienci mogą wywołać polecenie [FLUSHDB](http://redis.io/commands/flushdb) , aby usunąć wszystkie klucze w **pojedynczej** bazie danych lub [FLUSHALL](http://redis.io/commands/flushall) , aby usunąć wszystkie klucze ze **wszystkich** baz danych w pamięci podręcznej Redis. Można sprawdzić, czy klucze zostały opróżnione za pomocą polecenia [info](http://redis.io/commands/info) . Zostanie wyświetlona, jeśli w sekcji *Commandstats* zostanie wywołane polecenie Flush.

```
# Commandstats

cmdstat_flushall:calls=2,usec=112,usec_per_call=56.00

cmdstat_flushdb:calls=1,usec=110,usec_per_call=52.00
```

### <a name="incorrect-database-selection"></a>Nieprawidłowy wybór bazy danych

Usługa Azure cache for Redis domyślnie używa bazy danych **DB0** . Jeśli przełączysz się do innej bazy danych (np., DB1), a następnie spróbujesz odczytać z niej klucze, Redis nie znajdzie tam, ponieważ każda baza danych jest jednostką logicznie oddzielną i zawiera inny zestaw danych. Użyj polecenia [SELECT](http://redis.io/commands/select) , aby użyć innych dostępnych baz danych i wyszukać klucze w każdym z nich.

### <a name="redis-instance-failure"></a>Niepowodzenie wystąpienia Redis

Redis to magazyn danych w pamięci. Dane są przechowywane na maszynach fizycznych lub wirtualnych, które obsługują Redis. Wystąpienie usługi Azure cache for Redis w warstwie Podstawowa działa tylko na jednej maszynie wirtualnej. Jeśli ta maszyna wirtualna nie działa, wszystkie dane przechowywane w pamięci podręcznej zostaną utracone. Pamięć podręczna w warstwach Standardowa i Premium zapewnia znacznie wyższą odporność na utratę danych przy użyciu dwóch maszyn wirtualnych w zreplikowanej konfiguracji. Gdy węzeł główny w takiej pamięci podręcznej ulegnie awarii, węzeł repliki przejdzie w celu automatycznego objęcia danych. Te maszyny wirtualne znajdują się w oddzielnych domenach błędów i aktualizacji, aby zminimalizować prawdopodobieństwo, że obie stają się niedostępne jednocześnie. W przypadku wystąpienia poważnych awarii centrum danych maszyny wirtualne mogą jednak nadal przechodzić do siebie. W tych rzadkich przypadkach Twoje dane zostaną utracone.

Należy rozważyć użycie [trwałości danych Redis](http://redis.io/topics/persistence) i [replikacji geograficznej](https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-geo-replication) , aby zwiększyć ochronę danych przed awarią infrastruktury.

## <a name="additional-information"></a>Dodatkowe informacje

- [Rozwiązywanie problemów z usługą Azure cache pod kątem problemów po stronie serwera Redis](cache-troubleshoot-server.md)
- [Jakiej pamięci podręcznej platformy Azure dla oferty Redis i rozmiaru należy użyć?](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use)
- [Jak monitorować usługę Azure cache for Redis](cache-how-to-monitor.md)
- [Jak można uruchomić polecenia Redis?](cache-faq.md#how-can-i-run-redis-commands)
