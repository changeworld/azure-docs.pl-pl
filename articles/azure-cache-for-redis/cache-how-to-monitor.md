---
title: Jak monitorować pamięć podręczną platformy Azure dla redis
description: Dowiedz się, jak monitorować kondycję i wydajność pamięci podręcznej platformy Azure dla wystąpień Redis
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 07/13/2017
ms.openlocfilehash: 86cbeddba699e89ce1127dbac72dac81dcc41449
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76547493"
---
# <a name="how-to-monitor-azure-cache-for-redis"></a>Jak monitorować pamięć podręczną platformy Azure dla redis

Usługa Azure Cache for Redis używa [usługi Azure Monitor,](../azure-monitor/index.yml) aby zapewnić kilka opcji monitorowania wystąpień pamięci podręcznej. Możesz wyświetlać metryki, przypinać wykresy metryk do tablicy startowej, dostosowywać zakres daty i czasu wykresów monitorowania, dodawać i usuwać dane z wykresów oraz ustawiać alerty, gdy spełnione są określone warunki. Te narzędzia umożliwiają monitorowanie kondycji pamięci podręcznej platformy Azure dla wystąpień Redis i ułatwiają zarządzanie aplikacjami buforowania.

Metryki dla usługi Azure Cache dla wystąpień Redis są zbierane przy użyciu polecenia Redis [INFO](https://redis.io/commands/info) około dwa razy na minutę i automatycznie przechowywane przez 30 dni (zobacz [Eksportuj metryki pamięci podręcznej,](#export-cache-metrics) aby skonfigurować różne zasady przechowywania), dzięki czemu mogą być wyświetlane na wykresach metryk i oceniane przez reguły alertów. Aby uzyskać więcej informacji na temat różnych wartości INFO używanych dla każdej metryki pamięci podręcznej, zobacz [Dostępne metryki i interwały raportowania](#available-metrics-and-reporting-intervals).

<a name="view-cache-metrics"></a>

Aby wyświetlić metryki pamięci podręcznej, [przejdź](cache-configure.md#configure-azure-cache-for-redis-settings) do wystąpienia pamięci podręcznej w [witrynie Azure Portal](https://portal.azure.com).  Usługa Azure Cache for Redis udostępnia niektóre wbudowane wykresy na **bloku Przegląd** i **redis metrics** bloku. Każdy wykres można dostosować, dodając lub usuwając metryki i zmieniając interwał raportowania.

![Metryki Redis](./media/cache-how-to-monitor/redis-cache-redis-metrics-blade.png)

## <a name="view-pre-configured-metrics-charts"></a>Wyświetlanie wstępnie skonfigurowanych wykresów metryk

**Blok Przegląd** ma następujące wstępnie skonfigurowane wykresy monitorowania.

* [Monitorowanie wykresów](#monitoring-charts)
* [Wykresy użycia](#usage-charts)

### <a name="monitoring-charts"></a>Monitorowanie wykresów

Sekcja **Monitorowanie** w **bloku Przegląd** zawiera **wykresy Trafienia i chybienia**, **Liczby i zestawy,** **Połączenia**i **Polecenia razem.**

![Monitorowanie wykresów](./media/cache-how-to-monitor/redis-cache-monitoring-part.png)

### <a name="usage-charts"></a>Wykresy użycia

Sekcja **Użycie** w bloku **Przegląd** zawiera wykresy Obciążenie **serwera Redis,** Użycie **pamięci,** **Przepustowość sieci**i Użycie **procesora CPU,** a także jest **wyświetlana warstwa cenowa** dla wystąpienia pamięci podręcznej.

![Wykresy użycia](./media/cache-how-to-monitor/redis-cache-usage-part.png)

**Warstwa cenowa** wyświetla warstwę cenową pamięci podręcznej i może służyć do [skalowania](cache-how-to-scale.md) pamięci podręcznej do innej warstwy cenowej.

## <a name="view-metrics-with-azure-monitor"></a>Wyświetlanie metryk za pomocą monitora platformy Azure

Aby wyświetlić metryki Redis i utworzyć niestandardowe wykresy za pomocą usługi Azure Monitor, kliknij **polecenie Metryki** z menu **Zasób**i dostosuj wykres przy użyciu żądanych metryk, interwału raportowania, typu wykresu i innych obrażeń.

![Metryki Redis](./media/cache-how-to-monitor/redis-cache-monitor.png)

Aby uzyskać więcej informacji na temat pracy z metrykami przy użyciu usługi Azure Monitor, zobacz [Omówienie metryk na platformie Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md).

<a name="how-to-view-metrics-and-customize-chart"></a>
<a name="enable-cache-diagnostics"></a>
## <a name="export-cache-metrics"></a>Eksportowanie danych pamięci podręcznej

Domyślnie metryki pamięci podręcznej w usłudze Azure Monitor są [przechowywane przez 30 dni,](../azure-monitor/platform/data-platform-metrics.md) a następnie usuwane. Aby utrwalić metryki pamięci podręcznej przez okres dłuższy niż 30 dni, można [wyznaczyć konto magazynu](../azure-monitor/platform/archive-diagnostic-logs.md) i określić zasady **przechowywania (dni)** dla metryk pamięci podręcznej. 

Aby skonfigurować konto magazynu dla metryk pamięci podręcznej:

1. Na stronie **Azure Cache for Redis** w nagłówku **Monitorowanie** wybierz pozycję **Diagnostyka**.
2. Wybierz **+ Dodaj ustawienie diagnostyczne**.
3. Nazwij ustawienia.
4. Zaznacz pozycję **Zarchiwizuj na koncie magazynu**. Podczas wysyłania diagnostyki na konto magazynu zostanie naliczona normalna opłata za dane.
4. Wybierz **pozycję Konfiguruj,** aby wybrać konto magazynu, na którym mają być przechowywane metryki pamięci podręcznej.
5. Pod **metryką**nagłówka tabeli pole wyboru obok elementów zamówienia, które chcesz przechowywać, takich jak **AllMetrics**. Określ zasady **przechowywania (dni).** Maksymalna liczba dni przechowywania, którą można określić, to **365 dni**. Jeśli jednak chcesz zachować dane metryk na zawsze, ustaw **retencję (dni)** na **0**.
6. Kliknij przycisk **Zapisz**.


![Diagnostyka Redis](./media/cache-how-to-monitor/redis-cache-diagnostics.png)

>[!NOTE]
>Oprócz archiwizowania metryk pamięci podręcznej do magazynu można również [przesyłać strumieniowo je do Centrum zdarzeń lub wysyłać je do dzienników usługi Azure Monitor.](../azure-monitor/platform/rest-api-walkthrough.md#retrieve-metric-values)
>

Aby uzyskać dostęp do metryk, można wyświetlić je w witrynie Azure portal, jak opisano wcześniej w tym artykule, a także uzyskać do nich dostęp za pomocą [interfejsu API REST metryki usługi Azure Monitor.](../azure-monitor/platform/stream-monitoring-data-event-hubs.md)

> [!NOTE]
> Jeśli zmienisz konta magazynu, dane na wcześniej skonfigurowanym koncie magazynu pozostają dostępne do pobrania, ale nie są wyświetlane w witrynie Azure portal.  
> 

## <a name="available-metrics-and-reporting-intervals"></a>Dostępne metryki i interwały raportowania

Metryki pamięci podręcznej są zgłaszane przy użyciu kilku interwałów raportowania, w tym **przeszłe godziny**, **Dzisiaj,** **Ostatni tydzień**i **Niestandardowe**. **Bloku metryki** dla każdego wykresu metryki wyświetla średnie, minimalne i maksymalne wartości dla każdej metryki na wykresie, a niektóre metryki wyświetlają sumę dla interwału raportowania. 

Każda metryka zawiera dwie wersje. Jedna metryka mierzy wydajność dla całej pamięci podręcznej i dla pamięci podręcznej, `(Shard 0-9)` które używają [klastrowania](cache-how-to-premium-clustering.md), druga wersja metryki, która zawiera w nazwie mierzy wydajność dla pojedynczego fragmentu w pamięci podręcznej. Na przykład, jeśli pamięć podręczna `Cache Hits` ma cztery fragmenty, jest całkowita `Cache Hits (Shard 3)` liczba trafień dla całej pamięci podręcznej i jest tylko trafienia dla tego fragmentu pamięci podręcznej.

> [!NOTE]
> Nawet wtedy, gdy pamięć podręczna jest bezczynna bez podłączonych aktywnych aplikacji klienckich, mogą być widoczne pewne działania pamięci podręcznej, takie jak połączone klientów, użycie pamięci i operacje wykonywane. To działanie jest normalne podczas działania usługi Azure Cache dla wystąpienia Redis.
> 
> 

| Metryka | Opis |
| --- | --- |
| Trafienia w pamięci podręcznej |Liczba pomyślnych odnośeń kluczy w określonym przedziale raportowania. Ten numer `keyspace_hits` jest mapowana z polecenia Redis [INFO.](https://redis.io/commands/info) |
| Opóźnienie pamięci podręcznej (wersja zapoznawcza) | Opóźnienie pamięci podręcznej obliczone na podstawie opóźnienia między węzłem pamięci podręcznej. Ta metryka jest mierzona w mikrosekundach `Min`i `Max`ma trzy wymiary: `Avg`, i , które reprezentują średnie, minimalne i maksymalne opóźnienie pamięci podręcznej odpowiednio w określonym przedziale raportowania. |
| Chybienia w pamięci podręcznej |Liczba nieudanych odnośeń kluczy w określonym przedziale raportowania. Ten numer `keyspace_misses` jest mapowana z polecenia Redis INFO. Braki w pamięci podręcznej nie muszą oznaczać, że występuje problem z pamięcią podręczną. Na przykład podczas korzystania z wzorca programowania na bok pamięci podręcznej, aplikacja wygląda najpierw w pamięci podręcznej dla elementu. Jeśli element nie istnieje (brak pamięci podręcznej), element jest pobierany z bazy danych i dodawany do pamięci podręcznej następnym razem. Chybienia w pamięci podręcznej są normalne zachowanie dla wzorca programowania na bok pamięci podręcznej. Jeśli liczba braków w pamięci podręcznej jest wyższa niż oczekiwano, sprawdź logikę aplikacji, która wypełnia i odczytuje z pamięci podręcznej. Jeśli elementy są eksmitowane z pamięci podręcznej z powodu ciśnienia pamięci, to może być kilka `Used Memory` `Evicted Keys`chybień w pamięci podręcznej, ale lepszą metryką do monitorowania ciśnienia pamięci byłoby lub . |
| Odczyt pamięci podręcznej |Ilość danych odczytywanych z pamięci podręcznej w megabajtach na sekundę (MB/s) w określonym przedziale raportowania. Ta wartość jest pochodną kart interfejsu sieciowego, które obsługują maszynę wirtualną, która obsługuje pamięć podręczną i nie jest specyficzna dla redis. **Ta wartość odpowiada przepustowości sieci używanej przez tę pamięć podręczną. Jeśli chcesz skonfigurować alerty dla limitów przepustowości sieci po `Cache Read` stronie serwera, utwórz je za pomocą tego licznika. Zobacz [tę tabelę](cache-faq.md#cache-performance) dla obserwowanych limitów przepustowości dla różnych warstw i rozmiarów cen pamięci podręcznej.** |
| Zapis w pamięci podręcznej |Ilość danych zapisanych w pamięci podręcznej w megabajtach na sekundę (MB/s) w określonym przedziale raportowania. Ta wartość jest pochodną kart interfejsu sieciowego, które obsługują maszynę wirtualną, która obsługuje pamięć podręczną i nie jest specyficzna dla redis. Ta wartość odpowiada przepustowości sieci danych wysyłanych do pamięci podręcznej z klienta. |
| Podłączeni klienci |Liczba połączeń klienta z pamięcią podręczną w określonym przedziale raportowania. Ten numer `connected_clients` jest mapowana z polecenia Redis INFO. Po osiągnięciu [limitu połączenia](cache-configure.md#default-redis-server-configuration) kolejne próby połączenia z pamięcią podręczną nie powiodą się. Nawet jeśli nie ma żadnych aktywnych aplikacji klienckich, nadal może istnieć kilka wystąpień połączonych klientów z powodu wewnętrznych procesów i połączeń. |
| Procesor CPU |Wykorzystanie procesora CPU serwera Azure Cache for Redis jako procent w określonym przedziale raportowania. Ta wartość jest mapowana na licznik wydajności systemu operacyjnego. `\Processor(_Total)\% Processor Time` |
| Errors | Określone błędy i problemy z wydajnością, które mogą wystąpić w pamięci podręcznej podczas określonego interwału raportowania. Ta metryka ma osiem wymiarów reprezentujących różne typy błędów, ale może mieć więcej dodane w przyszłości. Typy błędów reprezentowane teraz są następujące: <br/><ul><li>**Praca awaryjna** — gdy pamięć podręczna działa awaryjnie (podrzędny promuje do wzorca)</li><li>**Dataloss** – gdy w pamięci podręcznej występuje utrata danych</li><li>**Nie odpowiadaClients** – gdy klienci nie odczytują danych z serwera wystarczająco szybko</li><li>**AOF** – gdy występuje problem związany z trwałością AOF</li><li>**RDB** – gdy występuje problem związany z trwałością RDB</li><li>**Import –** gdy występuje problem związany z importem RDB</li><li>**Eksport** — gdy występuje problem związany z eksportu RDB</li></ul> |
| Eksmitowane klucze |Liczba elementów eksmitowanych z pamięci podręcznej `maxmemory` podczas określonego interwału raportowania z powodu limitu. Ten numer `evicted_keys` jest mapowana z polecenia Redis INFO. |
| Wygasłe klucze |Liczba elementów wygasła z pamięci podręcznej podczas określonego interwału raportowania. Ta wartość `expired_keys` jest mapowana z polecenia Redis INFO.|
| Pobiera |Liczba operacji get z pamięci podręcznej podczas określonego interwału raportowania. Ta wartość jest sumą następujących wartości z polecenia Redis `cmdstat_hgetall` `cmdstat_hmget`INFO `cmdstat_mget` `cmdstat_getbit`all: `cmdstat_getrange` `cmdstat_get`, `cmdstat_hget`, , , , , , i , i jest odpowiednikiem sumy trafień i chybień w pamięci podręcznej podczas interwału raportowania. |
| Operacje na sekundę | Całkowita liczba poleceń przetwarzanych na sekundę przez serwer pamięci podręcznej w określonym przedziale raportowania.  Ta wartość jest mapowana na "instantaneous_ops_per_sec" z polecenia Redis INFO. |
| Obciążenie serwera Redis |Procent cykli, w których serwer Redis jest zajęty przetwarzaniem i nie oczekuje bezczynności na wiadomości. Jeśli ten licznik osiągnie 100, oznacza to, że serwer Redis osiągnął pułap wydajności, a procesor nie może przetwarzać pracy szybciej. Jeśli widzisz wysokie obciążenie serwera Redis, zobaczysz wyjątki limitu czasu w kliencie. W takim przypadku należy rozważyć skalowanie w górę lub partycjonowanie danych do wielu pamięci podręcznych. |
| Zestawy |Liczba ustawionych operacji w pamięci podręcznej podczas określonego interwału raportowania. Ta wartość jest sumą następujących wartości z polecenia `cmdstat_set`Redis `cmdstat_hmset` `cmdstat_hsetnx`INFO `cmdstat_lset` `cmdstat_mset`all: `cmdstat_setbit` `cmdstat_setex`, `cmdstat_setrange` `cmdstat_hset`, `cmdstat_setnx`, , , , `cmdstat_msetnx`, , , , , , , i . |
| Całkowita liczba kluczy  | Maksymalna liczba kluczy w pamięci podręcznej w poprzednim okresie raportowania. Ten numer `keyspace` jest mapowana z polecenia Redis INFO. Ze względu na ograniczenie podstawowego systemu metryk dla pamięci podręcznych z włączoną klastrowania, Całkowita liczba kluczy zwraca maksymalną liczbę kluczy niezależnego fragmentu, który miał maksymalną liczbę kluczy w interwale raportowania.  |
| Łączna liczba operacji |Całkowita liczba poleceń przetwarzanych przez serwer pamięci podręcznej w określonym przedziale raportowania. Ta wartość `total_commands_processed` jest mapowana z polecenia Redis INFO. Gdy usługa Azure Cache for Redis jest używana wyłącznie dla pub/sub, `Sets`nie będzie żadnych `Total Operations` metryk dla `Cache Hits`, `Cache Misses` `Gets`, lub , ale będą metryki, które odzwierciedlają użycie pamięci podręcznej dla operacji pub/sub. |
| Używana pamięć |Ilość pamięci podręcznej używanej dla par klucz/wartość w pamięci podręcznej w MB podczas określonego interwału raportowania. Ta wartość `used_memory` jest mapowana z polecenia Redis INFO. Ta wartość nie obejmuje metadanych ani fragmentacji. |
| Procent używanej pamięci | % całkowitej pamięci używanej w określonym przedziale raportowania.  Ta wartość odwołuje `used_memory` się do wartości z polecenia Redis INFO, aby obliczyć wartość procentową. |
| Używane pamięci RSS |Ilość pamięci podręcznej używanej w MB podczas określonego interwału raportowania, w tym fragmentacji i metadanych. Ta wartość `used_memory_rss` jest mapowana z polecenia Redis INFO. |

<a name="operations-and-alerts"></a>
## <a name="alerts"></a>Alerty

Można skonfigurować odbieranie alertów w oparciu o metryki i dzienniki aktywności. Usługa Azure Monitor umożliwia skonfigurowanie alertu powodującego wykonywanie następujących czynności po jego wyzwoleniu:

* Wysłanie powiadomienia e-mail
* Wywołanie elementu webhook
* Wywołanie aplikacji logiki platformy Azure

Aby skonfigurować reguły alertów dla pamięci podręcznej, kliknij polecenie **Reguły alertów** z **menu Zasób**.

![Monitorowanie](./media/cache-how-to-monitor/redis-cache-monitoring.png)

Aby uzyskać więcej informacji na temat konfigurowania alertów i używania ich, zobacz [Omówienie alertów](../monitoring-and-diagnostics/insights-alerts-portal.md).

## <a name="activity-logs"></a>Dzienniki aktywności
Dzienniki aktywności zapewniają wgląd w operacje, które zostały wykonane w pamięci podręcznej platformy Azure dla wystąpień Redis. Wcześniej był znany jako "dzienniki inspekcji" lub "dzienniki operacyjne". Za pomocą dzienników aktywności, można określić "co, kto i kiedy" dla wszelkich operacji zapisu (PUT, POST, DELETE) podjęte w pamięci podręcznej platformy Azure dla wystąpień Redis. 

> [!NOTE]
> Dzienniki aktywności nie zawierają operacji odczytu (GET).
>

Aby wyświetlić dzienniki aktywności pamięci podręcznej, kliknij polecenie **Dzienniki aktywności** z menu **Zasób**.

Aby uzyskać więcej informacji na temat dzienników aktywności, zobacz [Omówienie dziennika aktywności platformy Azure](../azure-monitor/platform/platform-logs-overview.md).
