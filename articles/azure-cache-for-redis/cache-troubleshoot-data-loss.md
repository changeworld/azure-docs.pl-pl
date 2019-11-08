---
title: Rozwiązywanie problemów z utratą danych w usłudze Azure cache for Redis | Microsoft Docs
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
ms.openlocfilehash: 8165ce3195c12af52cfee2fff598d9417697f4cb
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73806554"
---
# <a name="troubleshoot-data-loss-in-azure-cache-for-redis"></a>Rozwiązywanie problemów z utratą danych w usłudze Azure cache for Redis

W tym artykule omówiono sposób diagnozowania rzeczywistych lub postrzeganych strat danych, które mogą wystąpić w usłudze Azure cache for Redis.

> [!NOTE]
> Kilka kroków rozwiązywania problemów zawartych w tym przewodniku zawiera instrukcje dotyczące uruchamiania poleceń Redis i monitorowania różnych metryk wydajności. Aby uzyskać więcej informacji i instrukcje, zapoznaj się z artykułami w sekcji [Informacje dodatkowe](#additional-information) .
>

## <a name="partial-loss-of-keys"></a>Częściowa utrata kluczy

Usługa Azure cache for Redis nie usuwa losowo kluczy po ich zapisaniu w pamięci. Jednak usuwa klucze w odpowiedzi na zasady wygasania lub wykluczania oraz do jawnych poleceń usuwania kluczy. Klucze, które zostały zapisana w węźle głównym w Premium lub standardowej pamięci podręcznej platformy Azure dla wystąpienia Redis, również mogą nie być dostępne od razu. Dane są replikowane z serwera głównego do repliki w sposób asynchroniczny i nieblokowany.

Jeśli okaże się, że klucze zniknęły z pamięci podręcznej, należy sprawdzić następujące możliwe przyczyny:

| Przyczyna | Opis |
|---|---|
| [Wygaśnięcie klucza](#key-expiration) | Klucze zostały usunięte ze względu na ustawione limity czasu. |
| [Wykluczenia klucza](#key-eviction) | Klucze są usuwane w obszarze wykorzystanie pamięci. |
| [Usuwanie klucza](#key-deletion) | Klucze są usuwane przez jawne polecenia Delete. |
| [Replikacja asynchroniczna](#async-replication) | Klucze nie są dostępne w replice z powodu opóźnień replikacji danych. |

### <a name="key-expiration"></a>Wygaśnięcie klucza

Usługa Azure cache for Redis usuwa klucz automatycznie, jeśli klucz ma przypisany limit czasu i ten okres został zakończony. Więcej informacji o wygaśnięciu klucza Redis można znaleźć w dokumentacji polecenia [wygasania](http://redis.io/commands/expire) . Wartości limitu czasu można również ustawić za pomocą poleceń [Set](http://redis.io/commands/set), [SETEX](https://redis.io/commands/setex), [GetSet](https://redis.io/commands/getset)i other **\*Store** .

Aby uzyskać statystyki dotyczące liczby wygasłych kluczy, użyj polecenia [info](http://redis.io/commands/info) . Sekcja `Stats` zawiera łączną liczbę wygasłych kluczy. Sekcja `Keyspace` zawiera więcej informacji o liczbie kluczy z limitami czasu i średnim limitem czasu.

```
# Stats

expired_keys:46583

# Keyspace

db0:keys=3450,expires=2,avg_ttl=91861015336
```

Możesz również sprawdzić metryki diagnostyki dla pamięci podręcznej, aby sprawdzić, czy istnieje korelacja między brakującym kluczem a skokiem w kluczach wygasłych. Zapoznaj się z dodatkiem do [debugowania Redis miejsca na dysku](https://gist.github.com/JonCole/4a249477142be839b904f7426ccccf82#appendix) , aby uzyskać informacje na temat korzystania z powiadomień o przestrzeni kluczy lub **monitora** w celu debugowania tych typów problemów.

### <a name="key-eviction"></a>Wykluczenia klucza

Pamięć podręczna systemu Azure dla Redis wymaga miejsca w pamięci do przechowywania danych. Klucze są przeczyszczane, aby zwolnić dostępną pamięć w razie potrzeby. Gdy wartości **used_memory** lub **used_memory_rss** w poleceniu [info](http://redis.io/commands/info) są zgodne ze skonfigurowanym ustawieniem **maxmemory** , usługa Azure cache for Redis uruchamia wykluczenia kluczy z pamięci na podstawie [zasad pamięci podręcznej](http://redis.io/topics/lru-cache).

Liczbę wykluczonych kluczy można monitorować za pomocą polecenia [info](http://redis.io/commands/info) :

```
# Stats

evicted_keys:13224
```

Możesz również sprawdzić metryki diagnostyki dla pamięci podręcznej, aby sprawdzić, czy istnieje korelacja między brakującym kluczem a skokiem w wykluczonych kluczach. Zapoznaj się z dodatkiem do [debugowania Redis miejsca na dysku](https://gist.github.com/JonCole/4a249477142be839b904f7426ccccf82#appendix) , aby uzyskać informacje na temat korzystania z powiadomień o przestrzeni kluczy lub **monitora** w celu debugowania tych typów problemów.

### <a name="key-deletion"></a>Usuwanie klucza

Redis klienci mogą wydać polecenie [del](http://redis.io/commands/del) lub [HDEL](http://redis.io/commands/hdel) , aby jawnie usunąć klucze z usługi Azure cache dla Redis. Liczbę operacji usuwania można śledzić za pomocą polecenia [info](http://redis.io/commands/info) . Jeśli polecenia **del** lub **HDEL** zostały wywołane, zostaną one wyświetlone w sekcji `Commandstats`.

```
# Commandstats

cmdstat_del:calls=2,usec=90,usec_per_call=45.00

cmdstat_hdel:calls=1,usec=47,usec_per_call=47.00
```

### <a name="async-replication"></a>Replikacja asynchroniczna

Wszystkie wystąpienia usługi Azure cache for Redis w warstwie Standardowa lub Premium są skonfigurowane z węzłem głównym i co najmniej jedną repliką. Dane są kopiowane z serwera głównego do repliki asynchronicznie przy użyciu procesu w tle. W witrynie sieci Web [Redis.IO](http://redis.io/topics/replication) opisano, jak działa replikacja danych Redis. W przypadku scenariuszy, w których klienci zapisują w Redis często, częściowe utrata danych może wystąpić, ponieważ ta replikacja gwarantuje chwilę. Jeśli na przykład serwer główny ulegnie awarii *po* zapisaniu przez klienta klucza do niego, ale *zanim* proces w tle będzie miał szansę na wysłanie tego klucza do repliki, klucz zostanie utracony, gdy replika przejmuje jako nowy wzorzec.

## <a name="major-or-complete-loss-of-keys"></a>Główna lub pełna utrata kluczy

Jeśli większość lub wszystkie klucze zniknęły z pamięci podręcznej, należy sprawdzić następujące możliwe przyczyny:

| Przyczyna | Opis |
|---|---|
| [Opróżnianie klucza](#key-flushing) | Klucze zostały przeczyszczone ręcznie. |
| [Nieprawidłowy wybór bazy danych](#incorrect-database-selection) | Usługa Azure cache for Redis jest ustawiona do użycia niedomyślnej bazy danych. |
| [Niepowodzenie wystąpienia Redis](#redis-instance-failure) | Serwer Redis jest niedostępny. |

### <a name="key-flushing"></a>Opróżnianie klucza

Klienci mogą wywołać polecenie [FLUSHDB](http://redis.io/commands/flushdb) , aby usunąć wszystkie klucze w *pojedynczej* bazie danych lub [FLUSHALL](http://redis.io/commands/flushall) , aby usunąć wszystkie klucze ze *wszystkich* baz danych w pamięci podręcznej Redis. Aby dowiedzieć się, czy klucze zostały opróżnione, użyj polecenia [info](http://redis.io/commands/info) . Sekcja `Commandstats` pokazuje, czy zostało wywołane polecenie **Flush** :

```
# Commandstats

cmdstat_flushall:calls=2,usec=112,usec_per_call=56.00

cmdstat_flushdb:calls=1,usec=110,usec_per_call=52.00
```

### <a name="incorrect-database-selection"></a>Nieprawidłowy wybór bazy danych

Usługa Azure cache for Redis domyślnie używa bazy danych **DB0** . Jeśli przełączysz się do innej bazy danych (na przykład **DB1**) i spróbujesz odczytać z niej klucze, usługa Azure cache for Redis nie będzie tam znajdować. Każda baza danych jest logicznie oddzielną jednostką i zawiera inny zestaw danych. Użyj polecenia [SELECT](http://redis.io/commands/select) , aby użyć innych dostępnych baz danych i wyszukać klucze w każdym z nich.

### <a name="redis-instance-failure"></a>Niepowodzenie wystąpienia Redis

Redis to magazyn danych w pamięci. Dane są przechowywane na maszynach fizycznych lub wirtualnych, które obsługują pamięć podręczną Redis. Wystąpienie usługi Azure cache for Redis w warstwie Podstawowa działa tylko na jednej maszynie wirtualnej. Jeśli ta maszyna wirtualna nie działa, wszystkie dane przechowywane w pamięci podręcznej zostaną utracone. 

Pamięć podręczna w warstwach Standardowa i Premium zapewnia znacznie wyższą odporność na utratę danych przy użyciu dwóch maszyn wirtualnych w zreplikowanej konfiguracji. Gdy węzeł główny w takiej pamięci podręcznej nie ulegnie awarii, węzeł repliki przejmuje dane automatycznie. Te maszyny wirtualne znajdują się w oddzielnych domenach dla błędów i aktualizacji, aby zminimalizować prawdopodobieństwo, że oba staną się niedostępne jednocześnie. W przypadku wystąpienia poważnej awarii centrum danych maszyny wirtualne mogą jednak nadal przechodzić do siebie. W tych rzadkich przypadkach Twoje dane zostaną utracone.

Rozważ użycie [trwałości danych Redis](http://redis.io/topics/persistence) i [replikacji geograficznej](https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-geo-replication) , aby zwiększyć ochronę danych przed awarią infrastruktury.

## <a name="additional-information"></a>Dodatkowe informacje

- [Rozwiązywanie problemów z usługą Azure cache pod kątem problemów po stronie serwera Redis](cache-troubleshoot-server.md)
- [Jakiej pamięci podręcznej platformy Azure dla oferty Redis i rozmiaru należy użyć?](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use)
- [Jak monitorować usługę Azure cache for Redis](cache-how-to-monitor.md)
- [Jak można uruchomić polecenia Redis?](cache-faq.md#how-can-i-run-redis-commands)
