---
title: Jak monitorować pamięć podręczna Redis Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak monitorować kondycję i wydajność swoich wystąpień w pamięci podręcznej Redis Azure
services: redis-cache
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
ms.assetid: 7e70b153-9c87-4290-85af-2228f31df118
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: wesmc
ms.openlocfilehash: 3a68435866e6fb5bf0210144e53918c35b416449
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/19/2018
ms.locfileid: "27910653"
---
# <a name="how-to-monitor-azure-redis-cache"></a>Jak monitorować usługę Azure Redis Cache
Pamięć podręczna Redis Azure używa [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) zapewnienie kilka opcji monitorowania swoich wystąpień w pamięci podręcznej. Można wyświetlić metryki, Przypnij wykresy metryk do tablicy startowej, Dostosuj zakres dat i godzin monitorowania wykresy, dodać i usunąć metryki z wykresy i ustawić alerty, gdy są spełnione następujące warunki. Te narzędzia umożliwiają monitorowanie kondycji wystąpień z pamięci podręcznej Redis Azure i pomocne w zarządzaniu aplikacjami buforowania.

Metryki dla wystąpień w pamięci podręcznej Redis Azure są zbierane przy użyciu pamięci podręcznej Redis [informacji](http://redis.io/commands/info) polecenia około dwa razy na minutę i automatycznie przechowywane przez 30 dni (zobacz [eksportowania metryki pamięci podręcznej](#export-cache-metrics) do konfigurowania zasad przechowywania różnych), mogą być wyświetlane na wykresach metryki i oceniane przez reguły alertów. Aby uzyskać więcej informacji o różnych wartościach informacje używane dla każdego metryki pamięci podręcznej, zobacz [dostępne metryki i raportowania interwałów](#available-metrics-and-reporting-intervals).

<a name="view-cache-metrics"></a>

Aby wyświetlić metryki pamięci podręcznej [Przeglądaj](cache-configure.md#configure-redis-cache-settings) do wystąpienia pamięci podręcznej w [portalu Azure](https://portal.azure.com).  Pamięć podręczna Redis Azure udostępnia niektórych wbudowanych typów wykresów na **omówienie** bloku i **Redis metryki** bloku. Każdego wykresu można dostosowywać przez dodawanie lub usuwanie metryki i zmienianie Interwał raportowania.

![Metryki pamięci podręcznej Redis](./media/cache-how-to-monitor/redis-cache-redis-metrics-blade.png)

## <a name="view-pre-configured-metrics-charts"></a>Wyświetl wstępnie skonfigurowanej metryki wykresów

**Omówienie** bloku dostępne są wstępnie skonfigurowane monitorowania wykresy.

* [Monitorowanie wykresów](#monitoring-charts)
* [Wykresy użycia](#usage-charts)

### <a name="monitoring-charts"></a>Monitorowanie wykresów
**Monitorowanie** sekcji **omówienie** ma bloku **trafienia i Chybienia**, **pobiera i ustawia**, **połączeń**, i **całkowita liczba poleceń** wykresów.

![Monitorowanie wykresów](./media/cache-how-to-monitor/redis-cache-monitoring-part.png)

### <a name="usage-charts"></a>Wykresy użycia
**Użycia** w sekcji **omówienie** ma bloku **obciążenia serwera Redis**, **użycie pamięci**, **przepustowość sieci**, i **użycie procesora CPU** wykresy, a także wyświetla **warstwa cenowa** dla wystąpienia pamięci podręcznej.

![Wykresy użycia](./media/cache-how-to-monitor/redis-cache-usage-part.png)

**Warstwa cenowa** Wyświetla cennik pamięci podręcznej warstwy i mogą być używane do [skali](cache-how-to-scale.md) pamięci podręcznej na inną warstwę cenową.

## <a name="view-metrics-with-azure-monitor"></a>Wyświetlaj metryki z Monitorem systemu Azure
Kliknij, aby wyświetlić metryki pamięci podręcznej Redis i tworzenie wykresów niestandardowych przy użyciu monitora Azure **metryki** z **zasobów menu**i dostosować wykres przy użyciu żądanego metryki, raportowanie interwał, typ wykresu i inne.

![Metryki pamięci podręcznej Redis](./media/cache-how-to-monitor/redis-cache-monitor.png)

Aby uzyskać więcej informacji na temat pracy z metryki za pomocą monitora Azure, zobacz [omówienie metryk w Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md).

<a name="how-to-view-metrics-and-customize-chart"></a>
<a name="enable-cache-diagnostics"></a>
## <a name="export-cache-metrics"></a>Eksportuj metryki pamięci podręcznej
Domyślnie są metryki pamięci podręcznej w monitorze Azure [przechowywane przez 30 dni](../monitoring-and-diagnostics/monitoring-overview-azure-monitor.md#store-and-archive) , a następnie usuwany. Aby zachować Twoje metryki pamięci podręcznej przez czas dłuższy niż 30 dni, można [wyznaczyć konta magazynu](../monitoring-and-diagnostics/monitoring-archive-diagnostic-logs.md) i określ **przechowywania (dni)** zasad dla użytkownika metryki pamięci podręcznej. 

Aby skonfigurować konto magazynu dla Twojego metryki pamięci podręcznej:

1. Kliknij przycisk **diagnostyki** z **zasobów menu** w **pamięci podręcznej Redis** bloku.
2. Kliknij przycisk **na**.
3. Sprawdź **archiwum na konto magazynu**.
4. Wybierz konto magazynu do przechowywania metryki pamięci podręcznej.
5. Sprawdź **1 minuta** wyboru i określ **przechowywania (dni)** zasad. Jeśli nie chcesz zastosować dowolne zasady przechowywania i Zachowaj dane w nieskończoność, ustaw **przechowywania (dni)** do **0**.
6. Kliknij pozycję **Zapisz**.

![Redis diagnostyki](./media/cache-how-to-monitor/redis-cache-diagnostics.png)

>[!NOTE]
>Oprócz archiwizacji Twoje metryki pamięci podręcznej do magazynu, możesz również [strumienia je do Centrum zdarzeń lub wysłać je do analizy dzienników](../monitoring-and-diagnostics/monitoring-overview-metrics.md#export-metrics).
>
>

Aby uzyskać dostęp do Twojego metryki, można je wyświetlić w portalu Azure, jak opisano wcześniej w tym artykule, a użytkownik może także uzyskiwać do nich dostęp przy użyciu [interfejsu API REST Azure Monitor metryki](../monitoring-and-diagnostics/monitoring-overview-metrics.md#access-metrics-via-the-rest-api).

> [!NOTE]
> W przypadku zmiany konta magazynu, dane na koncie magazynu wcześniej skonfigurowane pozostaną dostępne do pobrania, ale nie jest wyświetlane w portalu Azure.  
> 
> 

## <a name="available-metrics-and-reporting-intervals"></a>Dostępne metryki i raportowania odstępach czasu
Metryki pamięci podręcznej są zgłaszane przy użyciu kilku raportowania odstępach czasu, w tym **Ostatnia godzina**, **dzisiaj**, **ostatni tydzień**, i **niestandardowy**. **Metryka** bloku dla każdego wykresu metryki Wyświetla średnią, minimalną i maksymalną wartość dla każdego metryki na wykresie i niektóre metryki wyświetlania łącznie Interwał raportowania. 

Wszystkie metryki obejmuje dwie wersje. Jedna Metryka mierzy wydajności dla całej pamięci podręcznej i pamięci podręczne, które używają [klastrowanie](cache-how-to-premium-clustering.md), druga wersja metrykę, która obejmuje `(Shard 0-9)` wydajności środki nazwy dla jednego niezależnego fragmentu w pamięci podręcznej. Na przykład, jeśli pamięć podręczna ma 4 odłamków `Cache Hits` jest całkowita liczba trafień dla całą pamięć podręczną i `Cache Hits (Shard 3)` jest po prostu trafień dla tego niezależnych pamięci podręcznej.

> [!NOTE]
> Nawet jeśli pamięć podręczna jest w stanie bezczynności bez żadnych aplikacji podłączonych aktywnych klientów, mogą pojawić się niektóre działania pamięci podręcznej, takich jak połączonych klientów, użycie pamięci i wykonywania operacji. To działanie jest normalne podczas operacji wystąpienia pamięci podręcznej Redis Azure.
> 
> 

| Metryka | Opis |
| --- | --- |
| Trafienia w pamięci podręcznej |Liczba pomyślnych wyszukiwań klucza w określonym interwale raportowania. Mapuje to `keyspace_hits` z pamięci podręcznej Redis [informacji](http://redis.io/commands/info) polecenia. |
| Chybienia w pamięci podręcznej |Liczba nieudanych wyszukiwań klucza w określonym interwale raportowania. Mapuje to `keyspace_misses` polecenia Redis informacji. Chybienia pamięci podręcznej nie musi oznaczać, że występuje problem z pamięci podręcznej. Na przykład podczas korzystania z wzorca Zarezerwuj pamięć podręczna programowania, aplikacja wygląda pierwszy w pamięci podręcznej dla elementu. Jeśli element nie ma (Chybienie pamięci podręcznej), element jest pobierane z bazy danych i dodana do pamięci podręcznej dla następnym. Chybienia pamięci podręcznej to normalne zachowanie dla pamięci podręcznej Zarezerwuj programowania wzorca. Jeśli liczba chybień pamięci podręcznej jest większa niż oczekiwano, sprawdź logiki aplikacji, która wypełnia i odczytuje z pamięci podręcznej. Jeśli elementy są zostanie usunięty z pamięci podręcznej z powodu braku pamięci, a następnie może być niektórych Chybienia pamięci podręcznej, ale będzie metrykę lepsze monitorowanie wykorzystania pamięci `Used Memory` lub `Evicted Keys`. |
| Połączeni klienci |Liczba połączeń klientów z pamięci podręcznej w określonym interwale raportowania. Mapuje to `connected_clients` polecenia Redis informacji. Raz [limit połączeń](cache-configure.md#default-redis-server-configuration) jest osiągnęło kolejne próby połączenia do pamięci podręcznej spowoduje niepowodzenie. Należy pamiętać, że nawet jeśli nie ma żadnych aplikacji aktywnego klienta, mogą zostać kilka wystąpień podłączonych klientów z powodu wewnętrznego procesy i połączeń. |
| Wykluczone klucze |Liczba elementów usunięty z pamięci podręcznej w określonym interwale raportowania z powodu `maxmemory` limit. Mapuje to `evicted_keys` polecenia Redis informacji. |
| Wygasłe klucze |Liczba elementów wygasł z pamięci podręcznej w określonym interwale raportowania. Ta wartość jest mapowany na `expired_keys` polecenia Redis informacji. |
| Całkowita liczba kluczy  | Maksymalna liczba kluczy w pamięci podręcznej w ciągu ostatnich okresie raportowania. Mapuje to `keyspace` polecenia Redis informacji. Ze względu na ograniczenia źródłowy system metryki dla pamięci podręcznej z usługą klastrowania włączone wszystkie klucze zwraca maksymalną liczbę kluczy niezależnego fragmentu, który ma maksymalną liczbę kluczy interwale raportowania.  |
| Pobrania |Liczba operacji get z pamięci podręcznej w określonym interwale raportowania. Ta wartość jest sumą następujące wartości z informacji Redis wszystkie polecenia: `cmdstat_get`, `cmdstat_hget`, `cmdstat_hgetall`, `cmdstat_hmget`, `cmdstat_mget`, `cmdstat_getbit`, i `cmdstat_getrange`i jest odpowiednikiem suma pamięci podręcznej trafienia i Chybienia w interwale raportowania. |
| Obciążenie serwera Redis |Wartość procentowa cykle, w których jest zajęty, przetwarzania i nie jest oczekiwane bezczynności komunikatów serwera Redis. Jeśli licznik osiągnie wartość 100 oznacza serwera Redis osiągnęła limit wydajności i procesora CPU nie może przetworzyć działać wszelkie szybciej. Jeśli widzisz wysokie obciążenie serwera Redis będą widzieć wyjątków przekroczenia limitu czasu na komputerze klienckim. W takim przypadku należy rozważyć skalowanie w lub partycjonowanie danych w wielu pamięciach podręcznych. |
| Zestawy |Liczba operacji zestawu do pamięci podręcznej w określonym interwale raportowania. Ta wartość jest sumą następujące wartości z informacji Redis wszystkie polecenia: `cmdstat_set`, `cmdstat_hset`, `cmdstat_hmset`, `cmdstat_hsetnx`, `cmdstat_lset`, `cmdstat_mset`, `cmdstat_msetnx`, `cmdstat_setbit`, `cmdstat_setex`, `cmdstat_setrange`, i `cmdstat_setnx`. |
| Łączna liczba operacji |Całkowita liczba przetworzonych przez serwer w pamięci podręcznej w określonym interwale raportowania poleceń. Ta wartość jest mapowany na `total_commands_processed` polecenia Redis informacji. Należy pamiętać, że gdy pamięć podręczna Redis Azure jest używany wyłącznie w celu pub/sub będzie braku metryk dla `Cache Hits`, `Cache Misses`, `Gets`, lub `Sets`, ale będzie `Total Operations` metryki, które odzwierciedlać użycia pamięci podręcznej dla operacji pub/sub. |
| Użyta pamięć |Ilość pamięci używana dla par klucz/wartość w pamięci podręcznej w MB w określonym interwale raportowania. Ta wartość jest mapowany na `used_memory` polecenia Redis informacji. Nie zawiera metadanych lub fragmentacji. |
| Używana pamięć RSS |Ilość pamięci podręcznej używane w MB w określonym interwale raportowania, łącznie z fragmentacją i metadanych. Ta wartość jest mapowany na `used_memory_rss` polecenia Redis informacji. |
| Procesor CPU |Użycie procesora CPU serwera pamięci podręcznej Redis Azure jako procent w określonym interwale raportowania. Ta wartość jest mapowany na system operacyjny `\Processor(_Total)\% Processor Time` licznika wydajności. |
| Odczyt pamięci podręcznej |Ilość danych do odczytu z pamięci podręcznej w MB na sekundę (MB/s) w określonym interwale raportowania. Ta wartość pochodzi z kart interfejsu sieciowego obsługujących maszyny wirtualnej, który hostuje pamięci podręcznej i nie jest określone Redis. **Ta wartość odpowiada przepustowości sieci używanej przez tę pamięć podręczną. Jeśli chcesz skonfigurować alerty dla limity przepustowości sieci po stronie serwera, to należy go utworzyć za pomocą tej `Cache Read` licznika. Zobacz [tej tabeli](cache-faq.md#cache-performance) limitów obserwowanych przepustowości dla różnych pamięci podręcznej ceny warstw i rozmiary.** |
| Zapis w pamięci podręcznej |Ilość danych, zapisać w pamięci podręcznej w MB na sekundę (MB/s) w określonym raportowania interwału. Ta wartość pochodzi z kart interfejsu sieciowego obsługujących maszyny wirtualnej, który hostuje pamięci podręcznej i nie jest określone Redis. Ta wartość odpowiada przepustowość sieci danych przesyłanych do pamięci podręcznej klienta. |

<a name="operations-and-alerts"></a>
## <a name="alerts"></a>Alerty
Można skonfigurować do odbierania alertów w oparciu metryki i działania dzienników. Azure Monitor umożliwia skonfigurowanie alert o konieczności wyzwala, wykonaj następujące czynności:

* Wyślij powiadomienie e-mail
* Wywołanie elementu webhook
* Wywołanie aplikacji logiki platformy Azure

Kliknij, aby skonfigurować reguły alertów dla pamięci podręcznej **reguły alertów** z **zasobów menu**.

![Monitorowanie](./media/cache-how-to-monitor/redis-cache-monitoring.png)

Aby uzyskać więcej informacji o konfigurowaniu i korzystanie z alertów, zobacz [Przegląd alertów](../monitoring-and-diagnostics/insights-alerts-portal.md).

## <a name="activity-logs"></a>Dzienniki aktywności
Dzienniki aktywności zapewniają wgląd w operacje wykonywane na swoich wystąpień w pamięci podręcznej Redis Azure. Został on wcześniej znana jako "dzienników inspekcji" lub "operacyjne dzienniki". Przy użyciu dzienników działania, można określić ", co, która i kiedy" dla operacji (PUT, POST, DELETE) podejmowaną w odniesieniu do swoich wystąpień w pamięci podręcznej Redis Azure żadnego zapisu. 

> [!NOTE]
> Dzienniki aktywności nie dołączaj operacje odczytu (GET).
>
>

Aby wyświetlić dzienniki aktywności dla pamięci podręcznej, kliknij przycisk **Dzienniki aktywności** z **zasobów menu**.

Aby uzyskać więcej informacji o Dzienniki aktywności, zobacz [Omówienie dziennika aktywności platformy Azure](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md).











