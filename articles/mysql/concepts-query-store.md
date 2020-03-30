---
title: Magazyn zapytań — usługa Azure Database for MySQL
description: Dowiedz się więcej o funkcji Magazynu zapytań w usłudze Azure Database for MySQL, aby ułatwić śledzenie wydajności w czasie.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: d138c2fb8ed667d5b3c961c9f567264fa40edaee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79537044"
---
# <a name="monitor-azure-database-for-mysql-performance-with-query-store"></a>Monitorowanie usługi Azure Database pod kątem wydajności MySQL za pomocą Magazynu zapytań

**Dotyczy:** Usługa Azure Database dla mysql 5.7

Funkcja Magazynu zapytań w usłudze Azure Database for MySQL umożliwia śledzenie wydajności zapytań w czasie. Query Store upraszcza rozwiązywanie problemów z wydajnością, pomagając szybko znaleźć najdłużej działające i najbardziej zasobochłonne zapytania. Query Store automatycznie przechwytuje historię zapytań i statystyki środowiska uruchomieniowego i zachowuje je do przeglądu. Oddziela dane według okien czasu, dzięki czemu można zobaczyć wzorce użycia bazy danych. Dane dla wszystkich użytkowników, baz danych i zapytań są przechowywane w bazie danych **schematu mysql** w usłudze Azure Database for MySQL wystąpienie.

## <a name="common-scenarios-for-using-query-store"></a>Typowe scenariusze korzystania z Magazynu zapytań

Magazyn zapytań może być używany w wielu scenariuszach, w tym w następujących przypadkach:

- Wykrywanie kwerend regressed
- Określanie liczby wykonań kwerendy w danym oknie czasu
- Porównanie średniego czasu wykonywania kwerendy w oknach czasowych w celu wyświetlenia dużych delt

## <a name="enabling-query-store"></a>Włączanie Magazynu zapytań

Query Store jest funkcją opt-in, więc nie jest domyślnie aktywna na serwerze. Magazyn zapytań jest włączony lub wyłączony globalnie dla wszystkich baz danych na danym serwerze i nie można go włączyć ani wyłączyć na bazę danych.

### <a name="enable-query-store-using-the-azure-portal"></a>Włączanie Magazynu zapytań przy użyciu portalu Azure

1. Zaloguj się do witryny Azure portal i wybierz swoją usługę Azure Database dla serwera MySQL.
1. Wybierz **polecenie Parametry serwera** w sekcji **Ustawienia** menu.
1. Wyszukaj parametr query_store_capture_mode.
1. Ustaw wartość na ALL i **Zapisz**.

Aby włączyć statystyki oczekiwania w Magazynie zapytań:

1. Wyszukaj parametr query_store_wait_sampling_capture_mode.
1. Ustaw wartość na ALL i **Zapisz**.

Poczekaj do 20 minut, aby pierwsza partia danych utrwaliła się w bazie danych mysql.

## <a name="information-in-query-store"></a>Informacje w Magazynie zapytań

Query Store ma dwa sklepy:

- Magazyn statystyk środowiska wykonawczego dla utrwalania informacji statystycznych wykonywania kwerendy.
- Magazyn statystyk oczekiwania dla utrwalania informacji o statystykach oczekiwania.

Aby zminimalizować użycie miejsca, statystyki wykonywania środowiska wykonawczego w magazynie statystyk środowiska wykonawczego są agregowane w stałym, konfigurowalnym oknie czasu. Informacje w tych magazynach są widoczne przez wykonywanie zapytań widoków magazynu zapytań.

Następująca kwerenda zwraca informacje o kwerendach w Magazynie kwerend:

```sql
SELECT * FROM mysql.query_store;
```

Lub to zapytanie dla statystyk oczekiwania:

```sql
SELECT * FROM mysql.query_store_wait_stats;
```

## <a name="finding-wait-queries"></a>Znajdowanie zapytań oczekujących

> [!NOTE]
> Statystyki oczekiwania nie powinny być włączane w godzinach szczytu obciążenia lub być włączone przez czas nieokreślony dla poufnych obciążeń. <br>W przypadku obciążeń działających z wysokim wykorzystaniem procesora CPU lub na serwerach skonfigurowanych z niższymi rdzeniami wirtualnymi należy zachować ostrożność podczas włączania statystyk oczekiwania. Nie należy go włączać przez czas nieokreślony. 

Typy zdarzeń oczekiwania łączą różne zdarzenia oczekiwania w zasobnikach według podobieństwa. Query Store zawiera typ zdarzenia oczekiwania, określoną nazwę zdarzenia oczekiwania i kwerendę, o jakiej mowa. Możliwość skorelowania tych informacji oczekiwania ze statystykami środowiska wykonawczego kwerendy oznacza, że można uzyskać głębsze zrozumienie, co przyczynia się do właściwości wydajności kwerendy.

Oto kilka przykładów, jak można uzyskać więcej wglądu w obciążenie przy użyciu statystyk oczekiwania w Magazynie zapytań:

| **Obserwacji** | **Akcja** |
|---|---|
|High Lock czeka | Sprawdź teksty zapytań dla kwerend, których dotyczy problem i identyfikuj jednostki docelowe. Szukaj w Magazynie zapytań dla innych kwerend modyfikujących tę samą jednostkę, która jest wykonywana często i/lub mają wysoki czas trwania. Po zidentyfikowaniu tych zapytań należy rozważyć zmianę logiki aplikacji w celu poprawy współbieżności lub użyć mniej restrykcyjnego poziomu izolacji. |
|Wysoki bufor we/wy czeka | Znajdź zapytania z dużą liczbą odczytów fizycznych w Magazynie zapytań. Jeśli pasują do zapytań z wysokim oczekiwaniem we/wy, należy rozważyć wprowadzenie indeksu do jednostki podstawowej, aby zrobić szuka zamiast skanowania. Pozwoliłoby to zminimalizować obciążenie we/wy kwerend. Sprawdź **zalecenia dotyczące wydajności** serwera w portalu, aby sprawdzić, czy istnieją zalecenia dotyczące indeksu dla tego serwera, które zoptymalizowałyby zapytania. |
|Wysoka pamięć czeka | Znajdź kwerendy zużywające najlepszą pamięć w Magazynie zapytań. Te zapytania prawdopodobnie opóźniają dalszy postęp kwerend, których dotyczy problem. Sprawdź **zalecenia dotyczące wydajności** serwera w portalu, aby sprawdzić, czy istnieją zalecenia dotyczące indeksu, które optymalizują te zapytania.|

## <a name="configuration-options"></a>Opcje konfiguracji

Po włączeniu Magazynu zapytań zapisuje dane w 15-minutowych oknach agregacji, maksymalnie 500 różnych zapytań w oknie.

Następujące opcje są dostępne do konfigurowania parametrów Magazynu zapytań.

| **Parametr** | **Opis** | **Domyślny** | **Zakres** |
|---|---|---|---|
| query_store_capture_mode | Włącz/WYŁĄCZ funkcję magazynu zapytań na podstawie wartości. Uwaga: Jeśli performance_schema jest wyłączona, włączenie query_store_capture_mode zostanie włączone performance_schema i podzbiór instrumentów schematu wydajności wymaganych dla tej funkcji. | ALL | BRAK, WSZYSTKIE |
| query_store_capture_interval | Interwał przechwytywania magazynu kwerend w minutach. Umożliwia określenie interwału, w którym metryki kwerendy są agregowane | 15 | 5 - 60 |
| query_store_capture_utility_queries | Włączanie lub wyłączanie w celu przechwycenia wszystkich zapytań o narzędzia wykonywanych w systemie. | NO | TAK, NIE |
| query_store_retention_period_in_days | Przedział czasu w dniach, aby zachować dane w magazynie zapytań. | 7 | 1 - 30 |

Poniższe opcje dotyczą w szczególności statystyk oczekiwania.

| **Parametr** | **Opis** | **Domyślny** | **Zakres** |
|---|---|---|---|
| query_store_wait_sampling_capture_mode | Umożliwia włączanie / wyłączanie statystyk oczekiwania. | Brak | BRAK, WSZYSTKIE |
| query_store_wait_sampling_frequency | Zmienia częstotliwość pobierania próbek w ciągu kilku sekund. 5 do 300 sekund. | 30 | 5-300 |

> [!NOTE]
> Obecnie **query_store_capture_mode** zastępuje tę konfigurację, co oznacza, że zarówno **query_store_capture_mode,** jak i **query_store_wait_sampling_capture_mode** muszą być włączone do WSZYSTKICH, aby statystyki oczekiwania działały. Jeśli **query_store_capture_mode** jest wyłączona, statystyki oczekiwania są wyłączone, ponieważ statystyki oczekiwania wykorzystują performance_schema włączone, a query_text przechwycone przez magazyn zapytań.

Użyj [witryny Azure portal](howto-server-parameters.md) lub [interfejsu wiersza polecenia](howto-configure-server-parameters-using-cli.md) platformy Azure, aby uzyskać lub ustawić inną wartość dla parametru.

## <a name="views-and-functions"></a>Widoki i funkcje

Wyświetlanie magazynu zapytań i zarządzanie nim przy użyciu następujących widoków i funkcji. Każda osoba w [roli publicznej wybierz uprawnienia](howto-create-users.md#how-to-create-additional-admin-users-in-azure-database-for-mysql) może użyć tych widoków, aby wyświetlić dane w Magazynie zapytań. Widoki te są dostępne tylko w bazie danych **mysql.**

Zapytania są normalizowane, patrząc na ich strukturę po usunięciu literałów i stałych. Jeśli dwa zapytania są identyczne z wyjątkiem wartości literału, będą miały ten sam skrót.

### <a name="mysqlquery_store"></a>mysql.query_store

Ten widok zwraca wszystkie dane w Magazynie zapytań. Istnieje jeden wiersz dla każdego identyfikatora bazy danych, identyfikatora użytkownika i identyfikatora kwerendy.

| **Nazwa** | **Typ danych** | **IS_NULLABLE** | **Opis** |
|---|---|---|---|
| `schema_name`| varchar(64) | NO | Nazwa schematu |
| `query_id`| bigint(20) | NO| Unikatowy identyfikator wygenerowany dla określonej kwerendy, jeśli ta sama kwerenda jest wykonywana w innym schemacie, zostanie wygenerowany nowy identyfikator |
| `timestamp_id` | sygnatura czasowa| NO| Sygnatura czasowa, w której kwerenda jest wykonywana. Jest to oparte na konfiguracji query_store_interval|
| `query_digest_text`| Longtext| NO| Znormalizowany tekst kwerendy po usunięciu wszystkich literałów|
| `query_sample_text` | Longtext| NO| Pierwsze pojawienie się rzeczywistej kwerendy za pomocą literałów|
| `query_digest_truncated` | bit| TAK| Czy tekst kwerendy został obcięty. Wartość będzie tak, jeśli kwerenda jest dłuższa niż 1 KB|
| `execution_count` | bigint(20)| NO| Liczba wykonań kwerendy dla tego identyfikatora sygnatury czasowej / w skonfigurowanym okresie interwału|
| `warning_count` | bigint(20)| NO| Liczba ostrzeżeń generowanych przez tę kwerendę podczas|
| `error_count` | bigint(20)| NO| Liczba błędów wygenerowanych przez tę kwerendę w interwale|
| `sum_timer_wait` | double| TAK| Całkowity czas wykonywania tej kwerendy w interwale|
| `avg_timer_wait` | double| TAK| Średni czas wykonywania tej kwerendy w interwale|
| `min_timer_wait` | double| TAK| Minimalny czas wykonywania dla tej kwerendy|
| `max_timer_wait` | double| TAK| Maksymalny czas wykonania|
| `sum_lock_time` | bigint(20)| NO| Całkowita ilość czasu spędzonego dla wszystkich blokad dla tego wykonywania kwerendy w tym oknie czasu|
| `sum_rows_affected` | bigint(20)| NO| Liczba wierszy, których dotyczy problem|
| `sum_rows_sent` | bigint(20)| NO| Liczba wierszy wysłanych do klienta|
| `sum_rows_examined` | bigint(20)| NO| Liczba badanych wierszy|
| `sum_select_full_join` | bigint(20)| NO| Liczba pełnych sprzężeń|
| `sum_select_scan` | bigint(20)| NO| Liczba wybranych skanów |
| `sum_sort_rows` | bigint(20)| NO| Liczba posortowanych wierszy|
| `sum_no_index_used` | bigint(20)| NO| Liczba razy, gdy kwerenda nie używała żadnych indeksów|
| `sum_no_good_index_used` | bigint(20)| NO| Ile razy aparat wykonywania kwerendy nie używał żadnych dobrych indeksów|
| `sum_created_tmp_tables` | bigint(20)| NO| Całkowita liczba utworzonych tabel tymczasowych|
| `sum_created_tmp_disk_tables` | bigint(20)| NO| Całkowita liczba tabel tymczasowych utworzonych na dysku (generuje we/wy)|
| `first_seen` | sygnatura czasowa| NO| Pierwsze wystąpienie kwerendy podczas okna agregacji|
| `last_seen` | sygnatura czasowa| NO| Ostatnie wystąpienie kwerendy podczas tego okna agregacji|

### <a name="mysqlquery_store_wait_stats"></a>mysql.query_store_wait_stats

Ten widok zwraca dane zdarzeń oczekiwania w Magazynie zapytań. Istnieje jeden wiersz dla każdego identyfikatora bazy danych, identyfikatora użytkownika, identyfikatora kwerendy i zdarzenia.

| **Nazwa**| **Typ danych** | **IS_NULLABLE** | **Opis** |
|---|---|---|---|
| `interval_start` | sygnatura czasowa | NO| Początek interwału (przyrost 15-minutowy)|
| `interval_end` | sygnatura czasowa | NO| Koniec interwału (przyrost 15-minutowy)|
| `query_id` | bigint(20) | NO| Wygenerowano unikatowy identyfikator w znormalizowanej kwerendzie (z magazynu zapytań)|
| `query_digest_id` | varchar(32) | NO| Znormalizowany tekst kwerendy po usunięciu wszystkich literałów (z magazynu zapytań) |
| `query_digest_text` | Longtext | NO| Pierwsze pojawienie się rzeczywistej kwerendy z literałami (z magazynu zapytań) |
| `event_type` | varchar(32) | NO| Kategoria zdarzenia oczekiwania |
| `event_name` | varchar(128) | NO| Nazwa zdarzenia oczekiwania |
| `count_star` | bigint(20) | NO| Liczba zdarzeń oczekiwania próbkowania podczas interwału dla kwerendy |
| `sum_timer_wait_ms` | double | NO| Całkowity czas oczekiwania (w milisekundach) tej kwerendy w interwale |

### <a name="functions"></a>Funkcje

| **Nazwa**| **Opis** |
|---|---|
| `mysql.az_purge_querystore_data(TIMESTAMP)` | Czyści wszystkie dane magazynu zapytań przed daną sygnaturą czasową |
| `mysql.az_procedure_purge_querystore_event(TIMESTAMP)` | Czyści wszystkie dane zdarzenia oczekiwania przed daną sygnaturą czasową |
| `mysql.az_procedure_purge_recommendation(TIMESTAMP)` | Usuwa zalecenia, których wygaśnięcie jest przed daną sygnaturą czasową |

## <a name="limitations-and-known-issues"></a>Ograniczenia i znane problemy

- Jeśli serwer MySQL ma `default_transaction_read_only` parametr włączony, Query Store nie może przechwytywać danych.
- Funkcje magazynu zapytań mogą zostać przerwane, jeśli\>napotkają długie zapytania Unicode ( = 6000 bajtów).
- Okres przechowywania statystyk oczekiwania wynosi 24 godziny.
- Statystyki oczekiwania używa próbki do przechwytywania ułamek zdarzeń. Częstotliwość można modyfikować za `query_store_wait_sampling_frequency`pomocą parametru .

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [statystykach wydajności kwerend](concepts-query-performance-insight.md)
