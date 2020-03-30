---
title: Rozwiązywanie problemów z utratą danych w usłudze Azure Cache for Redis
description: Dowiedz się, jak rozwiązać problemy z utratą danych za pomocą usługi Azure Cache for Redis, takie jak częściowa utrata kluczy, wygaśnięcie kluczy lub całkowita utrata kluczy.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 10/17/2019
ms.openlocfilehash: d54506b94f076f0a3d967f88bd4e2960a1ca6396
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75530905"
---
# <a name="troubleshoot-data-loss-in-azure-cache-for-redis"></a>Rozwiązywanie problemów z utratą danych w usłudze Azure Cache for Redis

W tym artykule omówiono sposób diagnozowania rzeczywistych lub postrzeganych strat danych, które mogą wystąpić w pamięci podręcznej platformy Azure dla firmy Redis.

> [!NOTE]
> Kilka kroków rozwiązywania problemów w tym przewodniku zawiera instrukcje uruchamiania poleceń Redis i monitorowania różnych metryk wydajności. Aby uzyskać więcej informacji i instrukcji, zobacz artykuły w sekcji [Dodatkowe informacje.](#additional-information)
>

## <a name="partial-loss-of-keys"></a>Częściowa utrata kluczy

Usługa Azure Cache for Redis nie usuwa losowo kluczy po ich zapisaniu w pamięci. Jednak usuwa klucze w odpowiedzi na zasady wygasania lub eksmisji i jawne polecenia usuwania kluczy. Klucze, które zostały zapisane w węźle głównym w premium lub standardowej pamięci podręcznej azure dla wystąpienia Redis również może nie być dostępne w replice od razu. Dane są replikowane z wzorca do repliki w sposób asynchroniczne i nieblokujące.

Jeśli okaże się, że klucze zniknęły z pamięci podręcznej, sprawdź następujące możliwe przyczyny:

| Przyczyna | Opis |
|---|---|
| [Wygaśnięcie klucza](#key-expiration) | Klucze są usuwane z powodu limitów czasu ustawionych na nich. |
| [Kluczowa eksmisja](#key-eviction) | Klawisze są usuwane pod ciśnieniem pamięci. |
| [Usuwanie kluczy](#key-deletion) | Klucze są usuwane przez jawne polecenia usuwania. |
| [Replikacja asynchroniowa](#async-replication) | Klucze nie są dostępne w replice z powodu opóźnień replikacji danych. |

### <a name="key-expiration"></a>Wygaśnięcie klucza

Usługa Azure Cache for Redis automatycznie usuwa klucz, jeśli klucz jest przypisany limit czasu i ten okres minął. Aby uzyskać więcej informacji na temat wygasania klucza Redis, zobacz dokumentację polecenia [EXPIRE.](https://redis.io/commands/expire) Wartości przesuwu czasu można również ustawić za pomocą poleceń [SET,](https://redis.io/commands/set) [SETEX](https://redis.io/commands/setex), [GETSET](https://redis.io/commands/getset)i innych ** \*** poleceń STORE.

Aby uzyskać statystyki dotyczące liczby kluczy, które wygasły, użyj polecenia [INFO.](https://redis.io/commands/info) Sekcja `Stats` zawiera całkowitą liczbę wygasłych kluczy. Sekcja `Keyspace` zawiera więcej informacji na temat liczby kluczy z limitami czasu i średnią wartością przekroju czasu.

```
# Stats

expired_keys:46583

# Keyspace

db0:keys=3450,expires=2,avg_ttl=91861015336
```

Można również spojrzeć na metryki diagnostyczne dla pamięci podręcznej, aby sprawdzić, czy istnieje korelacja między gdy klucz zaginął i skok wygasłych kluczy. Zobacz dodatek [Debugowania Redis Keyspace Misses](https://gist.github.com/JonCole/4a249477142be839b904f7426ccccf82#appendix) informacji na temat korzystania z powiadomień przestrzeni kluczy lub **MONITOR** do debugowania tego typu problemów.

### <a name="key-eviction"></a>Kluczowa eksmisja

Usługa Azure Cache for Redis wymaga miejsca w pamięci do przechowywania danych. Czyści klucze, aby zwolnić dostępną pamięć, gdy jest to konieczne. Gdy **used_memory** lub **used_memory_rss** wartości w poleceniu [INFO](https://redis.io/commands/info) zbliży się do skonfigurowanego ustawienia **maxmemory,** usługa Azure Cache for Redis rozpoczyna wykluczanie kluczy z pamięci na podstawie [zasad pamięci podręcznej.](https://redis.io/topics/lru-cache)

Liczbę eksmitowanych kluczy można monitorować za pomocą polecenia [INFO:](https://redis.io/commands/info)

```
# Stats

evicted_keys:13224
```

Można również spojrzeć na metryki diagnostyczne dla pamięci podręcznej, aby sprawdzić, czy istnieje korelacja między gdy klucz zaginął i skok w eksmitowanych kluczy. Zobacz dodatek [Debugowania Redis Keyspace Misses](https://gist.github.com/JonCole/4a249477142be839b904f7426ccccf82#appendix) informacji na temat korzystania z powiadomień przestrzeni kluczy lub **MONITOR** do debugowania tego typu problemów.

### <a name="key-deletion"></a>Usuwanie kluczy

Redis klienci mogą wydać polecenie [DEL](https://redis.io/commands/del) lub [HDEL](https://redis.io/commands/hdel) jawnie usunąć klucze z usługi Azure Cache for Redis. Liczbę operacji usuwania można śledzić za pomocą polecenia [INFO.](https://redis.io/commands/info) Jeśli wywołano polecenia **DEL** lub **HDEL,** zostaną one `Commandstats` wyświetlone w sekcji.

```
# Commandstats

cmdstat_del:calls=2,usec=90,usec_per_call=45.00

cmdstat_hdel:calls=1,usec=47,usec_per_call=47.00
```

### <a name="async-replication"></a>Replikacja asynchroniowa

Każda usługa Azure Cache for Redis wystąpienie w warstwie Standardowa lub Premium jest skonfigurowany z węzłem głównym i co najmniej jedną replikę. Dane są kopiowane z wzorca do repliki asynchronicznie przy użyciu procesu w tle. W [witrynie redis.io](https://redis.io/topics/replication) opisano, jak replikacja danych Redis działa ogólnie. W scenariuszach, w których klienci często zapisują do redis, może wystąpić częściowa utrata danych, ponieważ ta replikacja jest gwarantowana jako natychmiastowa. Na przykład jeśli wzorzec ustępuje *po* klient zapisuje klucz do niego, ale *zanim* proces w tle ma szansę wysłać ten klucz do repliki, klucz zostanie utracony, gdy replika przejmuje jako nowy wzorzec.

## <a name="major-or-complete-loss-of-keys"></a>Poważna lub całkowita utrata kluczy

Jeśli większość lub wszystkie klucze zniknęły z pamięci podręcznej, sprawdź następujące możliwe przyczyny:

| Przyczyna | Opis |
|---|---|
| [Opróżnianie kluczy](#key-flushing) | Klawisze zostały usunięte ręcznie. |
| [Nieprawidłowy wybór bazy danych](#incorrect-database-selection) | Usługa Azure Cache for Redis jest skonfigurowana do używania nieniewiążej bazy danych. |
| [Niepowodzenie wystąpienia Redis](#redis-instance-failure) | Serwer Redis jest niedostępny. |

### <a name="key-flushing"></a>Opróżnianie kluczy

Klienci mogą wywołać polecenie [FLUSHDB,](https://redis.io/commands/flushdb) aby usunąć wszystkie klucze w *pojedynczej* bazie danych lub [FLUSHALL,](https://redis.io/commands/flushall) aby usunąć wszystkie klucze ze *wszystkich* baz danych w pamięci podręcznej Redis. Aby dowiedzieć się, czy klucze zostały opróżnione, użyj polecenia [INFO.](https://redis.io/commands/info) Sekcja `Commandstats` pokazuje, czy wywołano polecenie **FLUSH:**

```
# Commandstats

cmdstat_flushall:calls=2,usec=112,usec_per_call=56.00

cmdstat_flushdb:calls=1,usec=110,usec_per_call=52.00
```

### <a name="incorrect-database-selection"></a>Nieprawidłowy wybór bazy danych

Usługa Azure Cache for Redis domyślnie używa bazy danych **db0.** Jeśli przełączysz się do innej bazy danych (na przykład **db1)** i spróbujesz odczytać z niej klucze, usługa Azure Cache for Redis nie znajdzie ich tam. Każda baza danych jest logicznie oddzielną jednostką i zawiera inny zestaw danych. Użyj polecenia [SELECT,](https://redis.io/commands/select) aby użyć innych dostępnych baz danych i poszukaj kluczy w każdym z nich.

### <a name="redis-instance-failure"></a>Niepowodzenie wystąpienia Redis

Redis to magazyn danych w pamięci. Dane są przechowywane na maszynach fizycznych lub wirtualnych, które hostują pamięć podręczną Redis. Usługa Azure Cache for Redis wystąpienie w warstwie Basic działa tylko na jednej maszynie wirtualnej (VM). Jeśli ta maszyna wirtualna jest wyłączona, wszystkie dane, które zostały zapisane w pamięci podręcznej zostanie utracone. 

Pamięci podręczne w warstwach Standardowa i Premium oferują znacznie większą odporność na utratę danych przy użyciu dwóch maszyn wirtualnych w konfiguracji replikowanej. Gdy węzeł główny w takiej pamięci podręcznej ulegnie awarii, węzeł repliki przejmuje do obsługi danych automatycznie. Te maszyny wirtualne znajdują się w oddzielnych domenach dla błędów i aktualizacji, aby zminimalizować ryzyko obu staje się niedostępny jednocześnie. Jeśli jednak nastąpi awaria głównego centrum danych, maszyny wirtualne mogą nadal iść w dół razem. Twoje dane zostaną utracone w tych rzadkich przypadkach.

Należy rozważyć użycie [trwałości danych Redis](https://redis.io/topics/persistence) i [replikacji geograficznej,](https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-geo-replication) aby poprawić ochronę danych przed tymi awariami infrastruktury.

## <a name="additional-information"></a>Dodatkowe informacje

- [Rozwiązywanie problemów z usługą Azure Cache for Redis po stronie serwera](cache-troubleshoot-server.md)
- [Jakiej oferty i rozmiaru usługi Azure Cache for Redis należy używać?](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use)
- [Jak monitorować pamięć podręczną platformy Azure dla redis](cache-how-to-monitor.md)
- [Jak uruchomić polecenia Redis?](cache-faq.md#how-can-i-run-redis-commands)
