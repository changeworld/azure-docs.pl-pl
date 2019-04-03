---
title: Jak monitorować pamięć podręczna systemu Azure dla usługi Redis | Dokumentacja firmy Microsoft
description: Dowiedz się, jak monitorować kondycję i wydajność pamięci podręcznej Azure dla wystąpienia usługi Redis
services: cache
documentationcenter: ''
author: yegu-ms
manager: jhubbard
editor: ''
ms.assetid: 7e70b153-9c87-4290-85af-2228f31df118
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: yegu
ms.openlocfilehash: 32d0fb2ba17d322c0a273ebaf0a21d2b3ca0668f
ms.sourcegitcommit: 04716e13cc2ab69da57d61819da6cd5508f8c422
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2019
ms.locfileid: "58849580"
---
# <a name="how-to-monitor-azure-cache-for-redis"></a>Jak monitorować pamięć podręczna systemu Azure dla usługi Redis
Pamięć podręczna systemu Azure do celów Redis [usługi Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) zapewnienie kilka opcji do monitorowania wystąpień pamięci podręcznej. Można wyświetlić metryki, przypinać wykresy metryk do tablicy startowej, dostosować zakres dat i godzin, wykresy monitorowania, Dodaj i Usuń metryki z wykresów i ustawić alerty, gdy są spełnione określone warunki. Te narzędzia umożliwiają monitorowanie kondycji pamięci podręcznej Azure dla wystąpienia usługi Redis i ułatwia zarządzanie aplikacjami buforowania.

Metryki dla usługi Azure Cache dla wystąpienia usługi Redis są zbierane przy użyciu usługi Redis [informacje](https://redis.io/commands/info) polecenia około dwa razy na minutę i automatycznie przechowywane przez 30 dni (zobacz [eksportowanie metryk pamięci podręcznej](#export-cache-metrics) do skonfigurowania zasady przechowywania różnych), może być wyświetlany w przypadku wykresów metryki i sprawdzane przez reguły alertów. Aby uzyskać więcej informacji o różnych wartościach informacje używane dla każdego metryki pamięci podręcznej, zobacz [dostępne metryki i raportowanie interwałów](#available-metrics-and-reporting-intervals).

<a name="view-cache-metrics"></a>

Aby wyświetlić metryki pamięci podręcznej [Przeglądaj](cache-configure.md#configure-azure-cache-for-redis-settings) do wystąpienia pamięci podręcznej w [witryny Azure portal](https://portal.azure.com).  Pamięć podręczna systemu Azure dla usługi Redis zawiera kilka wbudowanych wykresy dotyczące **Przegląd** bloku i **metryki pamięci podręcznej Redis** bloku. Każdego wykresu można dostosować, dodając lub usuwając metryki i zmieniając interwał raportowania.

![Metryki pamięci podręcznej Redis](./media/cache-how-to-monitor/redis-cache-redis-metrics-blade.png)

## <a name="view-pre-configured-metrics-charts"></a>Wyświetlanie wykresów wstępnie skonfigurowanych metryk

**Przegląd** bloku dostępne są wstępnie skonfigurowane, wykresy monitorowania.

* [Wykresy monitorowania](#monitoring-charts)
* [Wykresy użycia](#usage-charts)

### <a name="monitoring-charts"></a>Wykresy monitorowania
**Monitorowanie** sekcji **Przegląd** blok zawiera **trafienia i Chybienia pamięci podręcznej podczas**, **pobiera i ustawia**, **połączeń**, i **łączna liczba poleceń** wykresów.

![Wykresy monitorowania](./media/cache-how-to-monitor/redis-cache-monitoring-part.png)

### <a name="usage-charts"></a>Wykresy użycia
**Użycia** sekcji **Przegląd** blok zawiera **obciążenie serwera Redis**, **użycie pamięci**, **przepustowość sieci**, i **użycie procesora CPU** wykresów, a także zostaną wyświetleni **warstwa cenowa** dla wystąpienia pamięci podręcznej.

![Wykresy użycia](./media/cache-how-to-monitor/redis-cache-usage-part.png)

**Warstwa cenowa** Wyświetla pamięci podręcznej ceny warstwy i może być używany do [skalowania](cache-how-to-scale.md) pamięci podręcznej do innej warstwy cenowej.

## <a name="view-metrics-with-azure-monitor"></a>Wyświetlanie metryk z usługą Azure monitor
Kliknij, aby wyświetlić metryki pamięci podręcznej Redis i tworzenie wykresów niestandardowych, przy użyciu usługi Azure Monitor **metryki** z **menu zasobów**i dostosowywanie wykresu za pomocą żądanego metryk, raportowanie, interwału i typ wykresu.

![Metryki pamięci podręcznej Redis](./media/cache-how-to-monitor/redis-cache-monitor.png)

Aby uzyskać więcej informacji na temat pracy z metrykami przy użyciu usługi Azure Monitor, zobacz [Przegląd metryk w systemie Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md).

<a name="how-to-view-metrics-and-customize-chart"></a>
<a name="enable-cache-diagnostics"></a>
## <a name="export-cache-metrics"></a>Eksportowanie metryk pamięci podręcznej
Domyślnie są metryki pamięci podręcznej w usłudze Azure Monitor [przechowywane przez 30 dni](../azure-monitor/platform/data-platform-metrics.md) a następnie usuwane. Aby zachować swoje metryki pamięci podręcznej przez czas dłuższy niż 30 dni, możesz [wyznaczyć konta magazynu](../azure-monitor/platform/archive-diagnostic-logs.md) i określ **przechowywania (dni)** zasady dla metryk pamięci podręcznej. 

Aby skonfigurować konto magazynu dla metryk pamięci podręcznej:

1. Kliknij przycisk **diagnostyki** z **menu zasobów** w **pamięci podręcznej Redis Azure** bloku.
2. Kliknij przycisk **na**.
3. Zaznacz pozycję **Zarchiwizuj na koncie magazynu**.
4. Wybierz konto magazynu, w którym będzie przechowywany metryki pamięci podręcznej.
5. Sprawdź **1 minutę** pole wyboru i określ **przechowywania (dni)** zasad. Jeśli nie chcesz stosować żadnych zasad przechowywania i przechowywać dane w nieskończoność, ustaw **przechowywania (dni)** do **0**.
6. Kliknij pozycję **Zapisz**.

![Redis diagnostyki](./media/cache-how-to-monitor/redis-cache-diagnostics.png)

>[!NOTE]
>Oprócz archiwizacji metryk pamięci podręcznej do magazynu, możesz również [przekazywać je strumieniowo do Centrum zdarzeń lub wysłać je do usługi Azure Monitor dzienniki](../azure-monitor/platform/rest-api-walkthrough.md#retrieve-metric-values).
>
>

Aby uzyskać dostęp do metryk, można je wyświetlić w witrynie Azure portal, jak opisano wcześniej w tym artykule, a użytkownik może także uzyskiwać do nich dostęp za pomocą [API REST usługi Azure Monitor metryki](../azure-monitor/platform/stream-monitoring-data-event-hubs.md).

> [!NOTE]
> W przypadku zmiany konta magazynu danych na koncie magazynu skonfigurowanych wcześniej pozostanie dostępna do pobrania, ale nie jest wyświetlana w witrynie Azure portal.  
> 
> 

## <a name="available-metrics-and-reporting-intervals"></a>Dostępne metryki i raportowanie odstępach czasu
Metryki pamięci podręcznej są zgłaszane przy użyciu kilku raportowania odstępach czasu, w tym **Ostatnia godzina**, **już dziś**, **ostatni tydzień**, i **niestandardowe**. **Metryki** bloku dla poszczególnych wykresów metryki Wyświetla wartość średnią, minimalną i maksymalną dla każdego metryki na wykresie i niektóre metryki wyświetlania łącznie Interwał raportowania. 

Wszystkie metryki. zawiera dwie wersje. Jedna Metryka mierzy wydajność całą pamięć podręczną i pamięci podręczne, które używają [klastrowania](cache-how-to-premium-clustering.md), druga wersja metrykę, która obejmuje `(Shard 0-9)` wydajność nazwę miary dla jednego fragmentu w pamięci podręcznej. Na przykład, jeśli pamięć podręczna ma 4 fragmentów `Cache Hits` jest całkowitej liczby trafień dla całą pamięć podręczną i `Cache Hits (Shard 3)` jest po prostu trafień dla tego fragmentu pamięci podręcznej.

> [!NOTE]
> Nawet gdy pamięci podręcznej jest w stanie bezczynności bez żadnych aplikacji podłączonych aktywnych klientów, może zostać wyświetlony niektóre działania pamięci podręcznej, na przykład połączeni klienci, użycia pamięci i operacji. To działanie jest normalne podczas operacji pamięć podręczna systemu Azure dla wystąpienia usługi Redis.
> 
> 

| Metryka | Opis |
| --- | --- |
| Trafienia w pamięci podręcznej |Liczba pomyślnych wyszukiwań kluczy w określonym interwale raportowania. To jest mapowany `keyspace_hits` z pamięci podręcznej Redis [informacje](https://redis.io/commands/info) polecenia. |
| Opóźnienie pamięci podręcznej (wersja zapoznawcza) | Czas oczekiwania w pamięci podręcznej obliczana w oparciu off opóźnień między węzłami pamięci podręcznej. Ta metryka jest mierzony w mikrosekundach i ma trzy wymiary: "Średnia", "Min" i "Max", którą reprezentują średnią, minimalną i maksymalną opóźnienie pamięci podręcznej odpowiednio w określonym interwale raportowania. |
| Chybienia w pamięci podręcznej |Liczba zakończonych niepowodzeniem klucza wyszukiwania w określonym interwale raportowania. To jest mapowany `keyspace_misses` polecenia Redis informacje. Chybienia w pamięci podręcznej nie musi oznaczać, że występuje problem z pamięcią podręczną. Na przykład gdy użycie wzorca z odkładaniem do pamięci podręcznej programowania, aplikacja wygląda pierwszy w pamięci podręcznej dla elementu. Jeśli element nie ma (Chybienie pamięci podręcznej), elementu jest pobierana z bazy danych i dodane do pamięci podręcznej użytku następnym razem. Chybienia w pamięci podręcznej są normalne zachowanie dla wzorca odkładania do pamięci podręcznej programowania. Jeśli liczba chybień pamięci podręcznej jest większa niż oczekiwano, sprawdź logikę aplikacji, która wypełnia i odczytu z pamięci podręcznej. Jeśli elementów jest wykluczana z pamięci podręcznej z powodu dużego wykorzystania pamięci, a następnie może istnieć kilka Chybienia pamięci podręcznej, ale byłoby lepsze metrykę, aby monitorować wykorzystanie pamięci `Used Memory` lub `Evicted Keys`. |
| Odczyt pamięci podręcznej |Ilość danych odczytanych z pamięci podręcznej w MB na sekundę (MB/s) w określonym interwale raportowania. Ta wartość jest tworzony na podstawie kart interfejsu sieciowego, które obsługują maszyny wirtualnej, który hostuje pamięci podręcznej i nie jest określone usługi Redis. **Ta wartość odpowiada przepustowości sieci używanej przez tę pamięć podręczną. Jeśli chcesz skonfigurować alerty dotyczące limity przepustowości sieci po stronie serwera, utwórz ją za pomocą tego `Cache Read` licznika. Zobacz [tej tabeli](cache-faq.md#cache-performance) limitów obserwowanych przepustowości dla różnych pamięci podręcznej ceny warstwy i rozmiarów.** |
| Zapis w pamięci podręcznej |Ilość danych zapisanych w pamięci podręcznej wyrażony w megabajtach, na sekundę (MB/s) podczas określonego raportowania interwału. Ta wartość jest tworzony na podstawie kart interfejsu sieciowego, które obsługują maszyny wirtualnej, który hostuje pamięci podręcznej i nie jest określone usługi Redis. Ta wartość odpowiada przepustowości sieci, danych wysyłanych do pamięci podręcznej klienta. |
| Podłączeni klienci |Liczba połączeń klienta pamięci podręcznej w określonym interwale raportowania. To jest mapowany `connected_clients` polecenia Redis informacje. Gdy [limit połączeń](cache-configure.md#default-redis-server-configuration) jest niepowodzenie osiągnął kolejnych próbach połączenia do zostanie pamięci podręcznej. Należy pamiętać, że nawet jeśli nie istnieją żadne aplikacje aktywnego klienta, nadal może występować kilka wystąpień podłączonych klientów z powodu połączenia i procesów wewnętrznych. |
| Procesor CPU |Użycie procesora CPU serwera Redis w pamięci podręcznej Azure jako wartość procentowa w określonym interwale raportowania. Ta wartość jest mapowany na system operacyjny `\Processor(_Total)\% Processor Time` licznika wydajności. |
| Błędy | Konkretnych błędów i problemów z wydajnością, które pamięć podręczna może występować w określonym interwale raportowania. Ta metryka ma osiem wymiarów reprezentujący błąd różnych typów, ale można mieć więcej dodane w przyszłości. Typy błędów, teraz reprezentowane są następujące: <br/><ul><li>**Tryb failover** — w przypadku pamięci podręcznej w trybie Failover (element podrzędny promuje do gałęzi głównej)</li><li>**Awarii** — w przypadku pamięci podręcznej nieoczekiwanie ulega awarii na jednym z węzłów</li><li>**Dataloss** — w przypadku dataloss w pamięci podręcznej</li><li>**UnresponsiveClients** — w przypadku klientów nie odczytują dane z serwera wystarczająco szybko</li><li>**AOF** — w przypadku problemów związanych z trwałości AOF</li><li>**RDB** — w przypadku problemów związanych z pliku RDB trwałości</li><li>**Importuj** — w przypadku problemów związanych z pliku RDB importu</li><li>**Eksportuj** — gdy ma obecnie problemów związanych z eksportu pliku RDB</li></ul> |
| Wykluczone klucze |Liczba elementów wykluczona z pamięci podręcznej w określonym interwale raportowania ze względu na `maxmemory` limit. To jest mapowany `evicted_keys` polecenia Redis informacje. |
| Wygasłe klucze |Liczba elementów wygasł z pamięci podręcznej w określonym interwale raportowania. Ta wartość jest mapowany na `expired_keys` polecenia Redis informacje.|
| Pobrania |Liczba operacji get z pamięci podręcznej w określonym interwale raportowania. Ta wartość jest sumą następujące wartości z kolekcji informacji Redis wszystkie polecenia: `cmdstat_get`, `cmdstat_hget`, `cmdstat_hgetall`, `cmdstat_hmget`, `cmdstat_mget`, `cmdstat_getbit`, i `cmdstat_getrange`i jest odpowiednikiem suma pamięci podręcznej trafienia i Chybienia w interwale raportowania. |
| Operacje na sekundę | Całkowita liczba przetworzonych na sekundę przez serwer pamięci podręcznej w określonym interwale raportowania poleceń.  Ta wartość jest mapowany na "instantaneous_ops_per_sec" polecenia Redis informacje. |
| Obciążenie serwera Redis |Procent cykle, w których jest zajęty przetwarzaniem i nie oczekuje bezczynności dla wiadomości z serwerem Redis. Jeśli licznik osiągnie wartość 100 oznacza, że serwer Redis osiągnie limit wydajności i procesora CPU nie może przetworzyć działają dowolne szybciej. Jeśli widzisz wysokie obciążenie serwera Redis, a następnie zostanie wyświetlony wyjątków przekroczenia limitu czasu w obiekcie klienta. W takim przypadku należy rozważyć skalowanie w górę lub partycjonowania danych w wielu pamięciach podręcznych. |
| Zestawy |Liczba operacji zestawu w pamięci podręcznej w określonym interwale raportowania. Ta wartość jest sumą następujące wartości z kolekcji informacji Redis wszystkie polecenia: `cmdstat_set`, `cmdstat_hset`, `cmdstat_hmset`, `cmdstat_hsetnx`, `cmdstat_lset`, `cmdstat_mset`, `cmdstat_msetnx`, `cmdstat_setbit`, `cmdstat_setex`, `cmdstat_setrange` , a `cmdstat_setnx`. |
| Całkowita liczba kluczy  | Maksymalna liczba kluczy w pamięci podręcznej w ostatnim okresie raportowania. To jest mapowany `keyspace` polecenia Redis informacje. Ze względu na ograniczenie bazowego systemu metryki, pod kątem pamięci podręcznych z klastrowaniem włączone wszystkie klucze zwraca maksymalną liczbę kluczy fragmentu, który ma maksymalną liczbę kluczy w interwale raportowania.  |
| Łączna liczba operacji |Całkowita liczba przetworzonych przez serwer pamięci podręcznej w określonym interwale raportowania poleceń. Ta wartość jest mapowany na `total_commands_processed` polecenia Redis informacje. Należy pamiętać, gdy pamięci podręcznej Azure redis Cache jest używana wyłącznie w celu publikowania/subskrybowania będzie brak metryk dla `Cache Hits`, `Cache Misses`, `Gets`, lub `Sets`, ale będzie `Total Operations` metryki odzwierciedlają wykorzystanie pamięci podręcznej dla operacji publikowania/subskrybowania. |
| Użyta pamięć |Ilość pamięci podręcznej używane dla pary klucz/wartość w pamięci podręcznej w MB w określonym interwale raportowania. Ta wartość jest mapowany na `used_memory` polecenia Redis informacje. Nie zawiera metadanych lub fragmentacji. |
| Procent używanej pamięci | Procent całkowitej ilości pamięci, który jest używany w określonym interwale raportowania.  Wartość "used_memory" odwołuje się polecenie Redis informacje do obliczania wartości procentowej. |
| Używana pamięć RSS |Ilość pamięci podręcznej używane w MB w określonym interwale raportowania, łącznie z fragmentacją i metadanych. Ta wartość jest mapowany na `used_memory_rss` polecenia Redis informacje. |

<a name="operations-and-alerts"></a>
## <a name="alerts"></a>Alerty
Można skonfigurować odbieranie alertów w oparciu o metryki i dzienniki aktywności. Usługa Azure Monitor umożliwia skonfigurowanie alertu powodującego wykonywanie następujących czynności po jego wyzwoleniu:

* Wysłanie powiadomienia e-mail
* Wywołanie elementu webhook
* Wywołanie aplikacji logiki platformy Azure

Aby skonfigurować reguły alertów dla pamięci podręcznej, kliknij **reguły alertów** z **menu zasobów**.

![Monitorowanie](./media/cache-how-to-monitor/redis-cache-monitoring.png)

Aby uzyskać więcej informacji o konfigurowaniu i korzystanie z alertów, zobacz [Przegląd alertów](../monitoring-and-diagnostics/insights-alerts-portal.md).

## <a name="activity-logs"></a>Dzienniki aktywności
Dzienniki aktywności udostępniają szczegółowe dane operacji wykonywanych w pamięci podręcznej Azure dla wystąpienia usługi Redis. Jego była wcześniej znana jako "dzienniki inspekcji" lub "operacyjne dzienniki". Przy użyciu dzienników aktywności, można określić "co, kto i kiedy" każdej operacji (PUT, POST, DELETE) wykonywanych na pamięć podręczną Azure Redis wystąpień zapisu. 

> [!NOTE]
> Dzienniki aktywności nie obejmują operacji odczytu (GET).
>
>

Aby wyświetlić dzienniki aktywności dla pamięci podręcznej, kliknij **dzienników aktywności** z **menu zasobów**.

Aby uzyskać więcej informacji na temat dzienników aktywności, zobacz [Przegląd dziennika aktywności platformy Azure](../azure-monitor/platform/activity-logs-overview.md).











