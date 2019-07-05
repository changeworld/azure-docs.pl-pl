---
title: Query Store w usłudze Azure Database for MySQL
description: W tym artykule opisano funkcję Store zapytania w usłudze Azure Database for MySQL
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 06/27/2019
ms.openlocfilehash: a24bba0786201f4ea1d1be431107f7bfe26a2a8f
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67461720"
---
# <a name="monitor-azure-database-for-mysql-performance-with-query-store"></a>Monitorowanie — Azure Database dla wydajności programu MySQL przy użyciu Query Store

**Dotyczy:**  Azure Database for MySQL 5.7

> [!NOTE]
> Query Store jest dostępna w wersji zapoznawczej.

Funkcja Query Store, w usłudze Azure Database for MySQL zapewnia sposób śledzenia wydajności zapytań, wraz z upływem czasu. Zapytanie Store upraszcza wydajności rozwiązywania problemów, ułatwiając szybkie znajdowanie zapytań najdłużej działających i najbardziej dużej ilości zasobów. Query Store automatycznie przechwytuje historię zapytań i statystyki środowiska uruchomieniowego i przechowuje je do przejrzenia. Tak, aby zobaczyć wzorców użycia baz danych, dzieli dane według czasu systemu windows. Dane dla wszystkich użytkowników, bazy danych i zapytania są przechowywane w **mysql** schematu bazy danych, w usłudze Azure Database for MySQL — wystąpienia.

## <a name="common-scenarios-for-using-query-store"></a>Typowe scenariusze za pomocą Query Store

Magazyn zapytań może służyć w wielu scenariuszach, w tym następujące:

- Wykrywanie pogorszonej zapytań
- Określenie, ile razy zapytanie zostało wykonane w danym przedziale czasowym
- Porównywanie Średni czas wykonania zapytania na okna czasowe, aby zobaczyć dużych różnic

## <a name="enabling-query-store"></a>Włączanie kwerenda Store

Query Store jest funkcji opcjonalnych, więc nie jest aktywne, domyślnie na serwerze. Magazyn zapytań jest włączone lub wyłączone globalnie, dla wszystkich baz danych na danym serwerze i nie można włączyć lub wyłączyć dla bazy danych.

### <a name="enable-query-store-using-the-azure-portal"></a>Włączanie kwerenda Store przy użyciu witryny Azure portal

1. Zaloguj się do witryny Azure portal i wybierz usługi Azure Database for MySQL server.
1. Wybierz **parametrów serwera** w **ustawienia** części menu.
1. Wyszukaj parametr query_store_capture_mode.
1. Ustaw wartość do wszystkich i **Zapisz**.

Aby włączyć statystyki oczekiwania w swojej Query Store:

1. Wyszukaj parametr query_store_wait_sampling_capture_mode.
1. Ustaw wartość do wszystkich i **Zapisz**.

Zezwala na maksymalnie 20 minut pierwszej partii danych do utrwalenia w bazie danych mysql.

## <a name="information-in-query-store"></a>Informacje przedstawione w Query Store

Query Store składa się z dwóch magazynów:

- Statystyki środowiska uruchomieniowego przechowywania na potrzeby utrwalania informacji statystyk wykonywania zapytań.
- Magazyn statystyki oczekiwania utrwalanie informacji statystyki oczekiwania.

Aby zminimalizować użycie miejsca na, statystyk wykonywania środowiska uruchomieniowego w magazynie statystyki środowiska uruchomieniowego są agregowane w przedziale czasu stałych, które można konfigurować. Informacje przedstawione w tych magazynów jest widoczna, badając widoków magazynu zapytań.

Następujące zapytanie zwraca informacje o zapytaniach w Query Store:

```sql
SELECT * FROM mysql.query_store;
```

Lub to zapytanie statystyki oczekiwania:

```sql
SELECT * FROM mysql.query_store_wait_stats;
```

## <a name="finding-wait-queries"></a>Znajdowanie oczekiwania zapytań

Typy zdarzeń oczekiwania łączyć różne oczekiwania zdarzenia w zasobników wg podobieństwa. Query Store zawiera typ zdarzenia oczekiwania, Nazwa zdarzenia określonego oczekiwania i zapytania w danym. Możliwość skorelować te informacje oczekiwania ze środowiskiem uruchomieniowym zapytania, statystyki oznacza, że można uzyskać, aby lepiej zrozumieć, co przyczynia się do charakterystyki wydajności zapytania.

Poniżej przedstawiono kilka przykładów, jak można uzyskać lepszy wgląd w obciążenia przy użyciu statystyki oczekiwania w Query Store:

| **Obserwowanie** | **Akcja** |
|---|---|
|Wysoka czeka blokady | Sprawdź teksty zapytania dla zapytań, których to dotyczy i zidentyfikować jednostki docelowej. Szukaj w Query Store na inne zapytania modyfikowanie tej samej jednostki, która jest często wykonywane i/lub mają wysoki czas trwania. Po określeniu tych zapytań, należy rozważyć zmianę logiki aplikacji w celu zwiększenia współbieżności, lub użyj mniej restrykcyjny poziom izolacji. |
|Wysoka czeka buforu operacji We/Wy | Znajdź zapytania z dużą liczbą fizyczne odczyty Query Store. Jeśli zapytania z wysoką czeka We/Wy są zgodne, należy rozważyć wprowadzenie do indeksu w podstawowej jednostki w celu szuka zamiast skanowania. Spowoduje to zminimalizować obciążenie We/Wy zapytań. Sprawdź **zalecenia dotyczące wydajności** serwera w portalu, aby sprawdzić, czy są zalecenia dotyczące indeksu dla tego serwera, który będzie optymalizacji zapytań. |
|Oczekuje na dużą ilość pamięci | Znajdź najważniejsze pamięć zapytań w Store zapytań zużywających najwięcej zasobów. Te zapytania są prawdopodobnie opóźniania dalszych postępów objęte zapytania. Sprawdź **zalecenia dotyczące wydajności** serwera w portalu, aby sprawdzić, czy są zalecenia dotyczące indeksu, które będzie zoptymalizowania tych zapytań.|

## <a name="configuration-options"></a>Opcje konfiguracji

Po włączeniu Query Store zapisuje dane w systemie windows agregacji 15 minut, maksymalnie 500 różne zapytania na okna.

Następujące opcje są dostępne w celu konfigurowania parametrów Query Store.

| **Parametr** | **Opis** | **Domyślne** | **Range** |
|---|---|---|---|
| query_store_capture_mode | Włącz funkcję magazynu zapytań, włączyć/wyłączyć na podstawie wartości. Uwaga: Jeśli performance_schema jest WYŁĄCZONY, włączając query_store_capture_mode spowoduje włączenie performance_schema oraz podzbiór dokumentów schematu wydajności wymagana dla tej funkcji. | ALL | BRAK, WSZYSTKIE |
| query_store_capture_interval | Magazyn zapytań przechwytywania interwału w ciągu kilku minut. Umożliwia określenie interwału, w którym są agregowane metryki zapytania | 15 | 5 - 60 |
| query_store_capture_utility_queries | Włączanie wł. lub Wył do przechwytywania wszystkich zapytań narzędzie, które jest wykonywane w systemie. | NO | TAK, NO |
| query_store_retention_period_in_days | Przedział czasu w dni przechowywania danych w magazynie zapytań. | 7 | 1 - 30 |

Poniższe opcje są stosowane specjalnie w celu oczekiwania statystyk.

| **Parametr** | **Opis** | **Domyślne** | **Range** |
|---|---|---|---|
| query_store_wait_sampling_capture_mode | Umożliwia włączenie ON / OFF statystyki oczekiwania. | BRAK | BRAK, WSZYSTKIE |
| query_store_wait_sampling_frequency | Alters częstotliwość pobierania próbek oczekiwania w sekundach. 5-300 sekund. | 30 | 5-300 |

> [!NOTE]
> Obecnie **query_store_capture_mode** zastępuje tę konfigurację, co oznacza zarówno **query_store_capture_mode** i **query_store_wait_sampling_capture_mode** musi być włączone dla wszystkich statystyki oczekiwania do pracy. Jeśli **query_store_capture_mode** jest wyłączony, a następnie statystyki oczekiwania jest wyłączone także ponieważ statystyki oczekiwania wykorzystuje performance_schema włączone i zapytanie_tekst przechwycone przez Magazyn zapytań.

Użyj [witryny Azure portal](howto-server-parameters.md) lub [wiersza polecenia platformy Azure](howto-configure-server-parameters-using-cli.md) można pobrać lub ustawić inną wartość dla parametru.

## <a name="views-and-functions"></a>Widoki i funkcje

Wyświetlanie i zarządzanie nimi Query Store przy użyciu następujących widoki i funkcje. Każda osoba w [wybierz uprawnienia roli publicznej](howto-create-users.md#how-to-create-additional-admin-users-in-azure-database-for-mysql) użyć tych widoków, aby wyświetlić dane w Query Store. Widoki te są dostępne tylko w **mysql** bazy danych.

Zapytania są znormalizowane, analizując ich struktury, po usunięciu literałów i stałych. Jeśli dwa zapytania są identyczne, z wyjątkiem wartości literału, będzie miał ten sam skrót.

### <a name="mysqlquerystore"></a>mysql.query_store

Ten widok zwraca wszystkie dane w Query Store. Istnieje jeden wiersz dla każdej różne bazy danych, identyfikator, nazwę użytkownika i identyfikator zapytania.

| **Nazwa** | **Typ danych** | **IS_NULLABLE** | **Opis** |
|---|---|---|---|
| `schema_name`| varchar(64) | NO | Nazwa schematu |
| `query_id`| bigint(20) | NO| Unikatowy identyfikator generowany dla określonego zapytania, jeśli to samo zapytanie wykonuje się w różnych schematów, nowy identyfikator zostanie wygenerowany. |
| `timestamp_id` | timestamp| NO| Znacznik czasu wykonywania zapytania. To zależy od konfiguracji query_store_interval|
| `query_digest_text`| longtext| NO| Tekst zapytania znormalizowane po usunięciu wszystkich literałach|
| `query_sample_text` | longtext| NO| Pierwsze wystąpienie rzeczywiste zapytanie z literałów|
| `query_digest_truncated` | bit| TAK| Czy został obcięty tekst zapytania. Wartością będzie tak, jeśli zapytanie jest dłuższy niż 1 KB|
| `execution_count` | bigint(20)| NO| Liczba przypadków, gdy dla tego Identyfikatora sygnatury czasowej / okresie skonfigurowany interwał stało się wykonać zapytania|
| `warning_count` | bigint(20)| NO| Liczba ostrzeżeń to zapytanie generowane podczas wewnętrzny|
| `error_count` | bigint(20)| NO| Liczba błędów, to zapytanie generowane w interwale|
| `sum_timer_wait` | double| TAK| Łączny czas wykonywania tego zapytania w interwale|
| `avg_timer_wait` | double| TAK| Średni czas wykonywania tego zapytania w interwale|
| `min_timer_wait` | double| TAK| Minimalny czas wykonywania tego zapytania|
| `max_timer_wait` | double| TAK| Maksymalny czas wykonywania|
| `sum_lock_time` | bigint(20)| NO| Łączna ilość czasu przeznaczonego dla wszystkich blokad to wykonywania zapytań w tym przedziale czasu|
| `sum_rows_affected` | bigint(20)| NO| Liczba wierszy, których to dotyczy|
| `sum_rows_sent` | bigint(20)| NO| Liczba wierszy, wysłane do klienta|
| `sum_rows_examined` | bigint(20)| NO| Liczba wierszy, które badania|
| `sum_select_full_join` | bigint(20)| NO| Liczba pełnego sprzężenia|
| `sum_select_scan` | bigint(20)| NO| Numer skanowania, wybierz opcję |
| `sum_sort_rows` | bigint(20)| NO| Liczba posortowanych wierszy|
| `sum_no_index_used` | bigint(20)| NO| Liczba przypadków, gdy zapytanie bez użycia jakichkolwiek indeksów|
| `sum_no_good_index_used` | bigint(20)| NO| Ile razy podczas aparatu wykonywania zapytań bez użycia jakichkolwiek indeksów dobre|
| `sum_created_tmp_tables` | bigint(20)| NO| Łączna liczba utworzone tabele tymczasowe|
| `sum_created_tmp_disk_tables` | bigint(20)| NO| Łączna liczba tabel tymczasowych utworzony na dysku (generuje operacji We/Wy)|
| `first_seen` | timestamp| NO| Pierwsze wystąpienie (UTC) zapytanie przedziale agregacji|
| `last_seen` | timestamp| NO| Ostatnie wystąpienie (UTC) zapytania w tym oknie agregacji|

### <a name="mysqlquerystorewaitstats"></a>mysql.query_store_wait_stats

Ten widok zwraca oczekiwać dane zdarzeń w Query Store. Istnieje jeden wiersz dla każdego identyfikator różne bazy danych, identyfikator użytkownika, identyfikator zapytania i zdarzeń.

| **Nazwa**| **Typ danych** | **IS_NULLABLE** | **Opis** |
|---|---|---|---|
| `interval_start` | timestamp | NO| Początek interwału (15-minutowy inkrementacja)|
| `interval_end` | timestamp | NO| Koniec interwału (15-minutowy inkrementacja)|
| `query_id` | bigint(20) | NO| Wygenerowany unikatowy identyfikator na znormalizowaną zapytania (z magazynu zapytań)|
| `query_digest_id` | varchar(32) | NO| Tekst zapytania znormalizowane po usunięciu wszystkich literałach (z magazynu zapytań) |
| `query_digest_text` | longtext | NO| Pierwsze wystąpienie rzeczywiste zapytanie z literałami (z magazynu zapytań) |
| `event_type` | varchar(32) | NO| Kategoria zdarzenia oczekiwania |
| `event_name` | varchar(128) | NO| Nazwa zdarzenia oczekiwania |
| `count_star` | bigint(20) | NO| Liczba zdarzeń oczekiwanie próbkowany przedział czasu dla zapytania |
| `sum_timer_wait_ms` | double | NO| Całkowity czas oczekiwania (w milisekundach) tego zapytania w interwale |

### <a name="functions"></a>Funkcje

| **Nazwa**| **Opis** |
|---|---|
| `mysql.az_purge_querystore_data(TIMESTAMP)` | Przeczyszcza wszystkie zapytania o dane magazynu przed sygnaturę czasową danej |
| `mysql.az_procedure_purge_querystore_event(TIMESTAMP)` | Powoduje usunięcie wszystkich oczekiwania dane zdarzeń przed sygnaturę czasową danej |
| `mysql.az_procedure_purge_recommendation(TIMESTAMP)` | Przeczyszcza zaleceń, których wygaśnięcia jest wcześniejsza niż sygnaturę czasową danej |

## <a name="limitations-and-known-issues"></a>Ograniczenia i znane problemy

- Jeśli dany serwer MySQL parametru `default_transaction_read_only` , Query Store nie mogą przechwytywać dane.
- Funkcje Query Store można przerwana w przypadku napotkania długie zapytania Unicode (\>= 6000 bajtów).
- Okres przechowywania dla statystyki oczekiwania wynosi 24 godziny.
- Statystyki oczekiwania używa próbki analizy czasowej przechwytywania ułamek zdarzeń. Częstotliwość można modyfikować za pomocą parametru `query_store_wait_sampling_frequency`.

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o [szczegółowe informacje o wydajności zapytań](concepts-query-performance-insight.md)