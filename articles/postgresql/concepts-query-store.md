---
title: Query Store w usłudze Azure Database for PostgreSQL — pojedynczy serwer
description: W tym artykule opisano funkcję Store zapytania w usłudze Azure Database for PostgreSQL — pojedynczy serwer.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: b622de3e21d26676bb11d81a6facf8fea18cabc1
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65067198"
---
# <a name="monitor-performance-with-the-query-store"></a>Monitorowanie wydajności za pomocą Store zapytania

**Dotyczy:** Azure Database for PostgreSQL — pojedynczy serwer 9.6 i 10

Funkcja Query Store, w usłudze Azure Database for PostgreSQL zapewnia sposób śledzenia wydajności zapytań, wraz z upływem czasu. Zapytanie Store upraszcza wydajności rozwiązywania problemów, ułatwiając szybkie znajdowanie zapytań najdłużej działających i najbardziej dużej ilości zasobów. Query Store automatycznie przechwytuje historię zapytań i statystyki środowiska uruchomieniowego i przechowuje je do przejrzenia. Tak, aby zobaczyć wzorców użycia baz danych, dzieli dane według czasu systemu windows. Dane dla wszystkich użytkowników, bazy danych i zapytania są przechowywane w bazie danych o nazwie **azure_sys** w usłudze Azure Database for postgresql w warstwie wystąpienia.

> [!IMPORTANT]
> Nie należy modyfikować **azure_sys** bazy danych lub jego schematów. Ten sposób zapobiegnie Query Store i funkcje dotyczące wydajności działa prawidłowo.

## <a name="enabling-query-store"></a>Włączanie kwerenda Store
Query Store jest funkcji opcjonalnych, więc nie jest aktywne, domyślnie na serwerze. Magazyn jest włączone lub wyłączone globalnie, dla wszystkich baz danych na danym serwerze i nie można włączyć lub wyłączyć dla bazy danych.

### <a name="enable-query-store-using-the-azure-portal"></a>Włączanie kwerenda Store przy użyciu witryny Azure portal
1. Zaloguj się do witryny Azure portal i wybierz usługi Azure Database for postgresql w warstwie serwera.
2. Wybierz **parametrów serwera** w **ustawienia** części menu.
3. Wyszukaj `pg_qs.query_capture_mode` parametru.
4. Ustaw wartość `TOP` i **Zapisz**.

Aby włączyć statystyki oczekiwania w swojej Query Store: 
1. Wyszukaj `pgms_wait_sampling.query_capture_mode` parametru.
1. Ustaw wartość `ALL` i **Zapisz**.


Alternatywnie można ustawić te parametry przy użyciu wiersza polecenia platformy Azure.
```azurecli-interactive
az postgres server configuration set --name pg_qs.query_capture_mode --resource-group myresourcegroup --server mydemoserver --value TOP
az postgres server configuration set --name pgms_wait_sampling.query_capture_mode --resource-group myresourcegroup --server mydemoserver --value ALL
```

Zezwalaj na pierwszej partii danych do utrwalenia w bazie danych azure_sys do 20 minut.

## <a name="information-in-query-store"></a>Informacje przedstawione w Query Store
Query Store składa się z dwóch magazynów:
- Magazyn statystyki środowiska uruchomieniowego potrzeby utrwalania informacji statystyk wykonywania zapytań.
- Magazyn statystyki oczekiwania utrwalanie informacji statystyki oczekiwania.

Typowe scenariusze dotyczące korzystania z zapytania Store obejmują:
- Określenie, ile razy zapytanie zostało wykonane w danym przedziale czasowym
- Porównywanie Średni czas wykonania zapytania na okna czasowe, aby zobaczyć dużych różnic
- Identyfikowanie najdłuższy uruchamianie zapytań w przeszłości X godzin
- Identyfikowanie pierwszych N zapytań, które oczekują na zasoby
- Zrozumienie natury oczekiwania dla określonego zapytania

Aby zminimalizować użycie miejsca na, statystyk wykonywania środowiska uruchomieniowego w magazynie statystyki środowiska uruchomieniowego są agregowane w przedziale czasu stałych, które można konfigurować. Informacje przedstawione w tych magazynów jest widoczna, badając widoków magazynu zapytań.

Następujące zapytanie zwraca informacje o zapytaniach w Query Store:
```sql
SELECT * FROM query_store.qs_view; 
``` 

Lub to zapytanie, aby uzyskać statystyki oczekiwania:
```sql
SELECT * FROM query_store.pgms_wait_sampling_view;
```

## <a name="finding-wait-queries"></a>Znajdowanie oczekiwania zapytań
Typy zdarzeń oczekiwania łączyć różne oczekiwania zdarzenia w zasobników wg podobieństwa. Query Store zawiera typ zdarzenia oczekiwania, Nazwa zdarzenia określonego oczekiwania i zapytania w danym. Możliwość skorelować te informacje oczekiwania ze środowiskiem uruchomieniowym zapytania, statystyki oznacza, że można uzyskać, aby lepiej zrozumieć, co przyczynia się do charakterystyki wydajności zapytania.

Poniżej przedstawiono kilka przykładów, jak można uzyskać lepszy wgląd w obciążenia przy użyciu statystyki oczekiwania w Query Store:

| **Obserwowanie** | **Akcja** |
|---|---|
|Wysoka czeka blokady | Sprawdź teksty zapytania dla zapytań, których to dotyczy i zidentyfikować jednostki docelowej. Szukaj w Query Store na inne zapytania modyfikowanie tej samej jednostki, która jest często wykonywane i/lub mają wysoki czas trwania. Po określeniu tych zapytań, należy rozważyć zmianę logiki aplikacji w celu zwiększenia współbieżności, lub użyj mniej restrykcyjny poziom izolacji.|
| Wysoka czeka buforu operacji We/Wy | Znajdź zapytania z dużą liczbą fizyczne odczyty Query Store. Jeśli zapytania z wysoką czeka We/Wy są zgodne, należy rozważyć wprowadzenie do indeksu w podstawowej jednostki, w tym szuka zamiast skanowania. Spowoduje to zminimalizować obciążenie We/Wy zapytań. Sprawdź **zalecenia dotyczące wydajności** serwera w portalu, aby sprawdzić, czy są zalecenia dotyczące indeksu dla tego serwera, który będzie optymalizacji zapytań.|
| Oczekuje na dużą ilość pamięci | Znajdź najważniejsze pamięć zapytań w Store zapytań zużywających najwięcej zasobów. Te zapytania są prawdopodobnie opóźniania dalszych postępów objęte zapytania. Sprawdź **zalecenia dotyczące wydajności** serwera w portalu, aby sprawdzić, czy są zalecenia dotyczące indeksu, które będzie zoptymalizowania tych zapytań.|

## <a name="configuration-options"></a>Opcje konfiguracji
Po włączeniu Query Store zapisuje dane w systemie windows agregacji 15 minut, maksymalnie 500 różne zapytania na okna. 

Następujące opcje są dostępne w celu konfigurowania parametrów Query Store.

| **Parametr** | **Opis** | **Domyślne** | **Range**|
|---|---|---|---|
| pg_qs.query_capture_mode | Zestawy instrukcji, które są śledzone. | brak | Brak, top, wszystkie |
| pg_qs.max_query_text_length | Ustawia długość maksymalna zapytania, który może zostać zapisany. Dłużej zapytań zostanie obcięta. | 6000 | 100 - 10 TYS. |
| pg_qs.retention_period_in_days | Ustawia okres przechowywania. | 7 | 1 - 30 |
| pg_qs.track_utility | Określa, czy polecenia narzędzia są śledzone. | włączone | włączony wyłączony |

Poniższe opcje są stosowane specjalnie w celu oczekiwania statystyk.

| **Parametr** | **Opis** | **Domyślne** | **Range**|
|---|---|---|---|
| pgms_wait_sampling.query_capture_mode | Zestawy, które są śledzone instrukcji poczekaj statystyki. | brak | Brak, wszystkie|
| Pgms_wait_sampling.history_period | Ustaw częstotliwość (w milisekundach), w których oczekiwania są próbkowane zdarzenia. | 100 | 1-600000 |

> [!NOTE] 
> **pg_qs.query_capture_mode** supersedes **pgms_wait_sampling.query_capture_mode**. W przypadku pg_qs.query_capture_mode NONE, ustawienie pgms_wait_sampling.query_capture_mode nie ma wpływu.


Użyj [witryny Azure portal](howto-configure-server-parameters-using-portal.md) lub [wiersza polecenia platformy Azure](howto-configure-server-parameters-using-cli.md) można pobrać lub ustawić inną wartość dla parametru.

## <a name="views-and-functions"></a>Widoki i funkcje
Wyświetlanie i zarządzanie nimi Query Store przy użyciu następujących widoki i funkcje. Każda osoba w roli publicznej postgresql w warstwie umożliwia tych widoków wyświetlić dane w Query Store. Widoki te są dostępne tylko w **azure_sys** bazy danych.

Zapytania są znormalizowane, analizując ich struktury, po usunięciu literałów i stałych. Jeśli dwa zapytania są identyczne, z wyjątkiem wartości literału, będzie miał ten sam skrót.

### <a name="querystoreqsview"></a>query_store.qs_view
Ten widok zwraca wszystkie dane w Query Store. Istnieje jeden wiersz dla każdej różne bazy danych, identyfikator, nazwę użytkownika i identyfikator zapytania. 

|**Nazwa**   |**Typ** | **Odwołania**  | **Opis**|
|---|---|---|---|
|runtime_stats_entry_id |bigint | | Identyfikator z tabeli runtime_stats_entries|
|user_id    |Identyfikator OID    |pg_authid.oid  |Identyfikator OID użytkownika, który wykonał instrukcji|
|db_id  |Identyfikator OID    |pg_database.oid    |Identyfikator OID bazy danych, w którym została wykonana instrukcja|
|query_id   |bigint  || Kod skrótu wewnętrznego obliczane na podstawie instrukcji drzewo analizy|
|query_sql_text |Varchar(10000)  || Tekst instrukcji językiem. Różne zapytania przy użyciu tej samej struktury są zgrupowane razem; Ten tekst jest tekstem, który w pierwszym zapytań w klastrze.|
|plan_id    |bigint |   |Identyfikator planu jeszcze odpowiadający to zapytanie nie jest dostępna|
|godzina_rozpoczęcia |timestamp  ||  Zapytania są agregowane według przedziałów czasu - okres przedziału wynosi 15 minut, domyślnie. Jest to czas rozpoczęcia odpowiadający przedział czasu dla tego wpisu.|
|end_time   |timestamp  ||  Godzina zakończenia odpowiadający przedział czasu dla tego wpisu.|
|wywołania  |bigint  || Liczba przypadków, wykonać zapytania|
|TOTAL_TIME |podwójnej precyzji   ||  Czas wykonywania kwerendy w milisekundach|
|min_time   |podwójnej precyzji   ||  Czas wykonywania zapytania minimalne, w milisekundach|
|max_time   |podwójnej precyzji   ||  Czas wykonywania zapytania maksymalna (w milisekundach)|
|mean_time  |podwójnej precyzji   ||  Oznacza czas wykonywania zapytania, w milisekundach|
|stddev_time|   podwójnej precyzji    ||  Odchylenie standardowe czasu wykonywania kwerendy w milisekundach |
|wiersze   |bigint ||  Całkowita liczba wierszy, pobrać lub wpływ instrukcja|
|shared_blks_hit|   bigint  ||  Łączna liczba trafień w pamięci podręcznej bloku udostępnionego przez instrukcję|
|shared_blks_read|  bigint  ||  Całkowita liczba bloków udostępnionego odczytywane przez instrukcję|
|shared_blks_dirtied|   bigint   || Całkowita liczba bloków udostępnionego dirtied przez instrukcję |
|shared_blks_written|   bigint  ||  Całkowita liczba bloków udostępnionego napisane przez instrukcję|
|local_blks_hit|    bigint ||   Łączna liczba trafień w pamięci podręcznej bloku lokalnych przez instrukcję|
|local_blks_read|   bigint   || Całkowita liczba bloków lokalnego odczytywane przez instrukcję|
|local_blks_dirtied|    bigint  ||  Całkowita liczba bloków lokalnego dirtied przez instrukcję|
|local_blks_written|    bigint  ||  Całkowita liczba bloków lokalne zapisane przez instrukcję|
|temp_blks_read |bigint  || Całkowita liczba bloków temp odczytywane przez instrukcję|
|temp_blks_written| bigint   || Całkowita liczba bloków temp napisane przez instrukcję|
|blk_read_time  |podwójnej precyzji    || Łączny czas czytania bloki, w milisekundach w instrukcji (Jeśli włączono track_io_timing, w przeciwnym razie od zera)|
|blk_write_time |podwójnej precyzji    || Łączny czas instrukcji pisania bloków, w milisekundach (Jeśli włączono track_io_timing, w przeciwnym razie od zera)|
    
### <a name="querystorequerytextsview"></a>query_store.query_texts_view
Ten widok zwraca tekst zapytania o dane w Query Store. Istnieje jeden wiersz dla każdego zapytanie_tekst distinct.

|**Nazwa**|  **Typ**|   **Opis**|
|---|---|---|
|query_text_id  |bigint     |Identyfikator tabeli query_texts|
|query_sql_text |Varchar(10000)     |Tekst instrukcji językiem. Różne zapytania przy użyciu tej samej struktury są zgrupowane razem; Ten tekst jest tekstem, który w pierwszym zapytań w klastrze.|

### <a name="querystorepgmswaitsamplingview"></a>query_store.pgms_wait_sampling_view
Ten widok zwraca oczekiwać dane zdarzeń w Query Store. Istnieje jeden wiersz dla każdego identyfikator różne bazy danych, identyfikator użytkownika, identyfikator zapytania i zdarzeń.

|**Nazwa**|  **Typ**|   **Odwołania**| **Opis**|
|---|---|---|---|
|user_id    |Identyfikator OID    |pg_authid.oid  |Identyfikator OID użytkownika, który wykonał instrukcji|
|db_id  |Identyfikator OID    |pg_database.oid    |Identyfikator OID bazy danych, w którym została wykonana instrukcja|
|query_id   |bigint     ||Kod skrótu wewnętrznego obliczane na podstawie instrukcji drzewo analizy|
|event_type |tekst       ||Typ zdarzenia, dla którego oczekuje wewnętrznej bazy danych|
|event  |tekst       ||Nazwa zdarzenia oczekiwania, jeśli oczekiwaniem wewnętrznej bazy danych|
|wywołania  |Liczba całkowita        ||Liczba te same zdarzenia, które są przechwytywane|


### <a name="functions"></a>Funkcje
Query_store.qs_reset() returns void

`qs_reset` odrzuca wszystkie dane statystyczne zebrane do tej pory przez Query Store. Tę funkcję można wykonać tylko przez rolę administratora serwera.

Query_store.staging_data_reset() zwraca wartość void

`staging_data_reset` odrzuca wszystkie statystyki zebrane w pamięci przez Query Store (czyli dane w pamięci, który nie został opróżniony jeszcze do bazy danych). Tę funkcję można wykonać tylko przez rolę administratora serwera.

## <a name="limitations-and-known-issues"></a>Ograniczenia i znane problemy
- Jeśli dany serwer PostgreSQL default_transaction_read_only parametru, Query Store nie mogą przechwytywać dane.
- Funkcje Query Store można przerwana w przypadku napotkania długie zapytania Unicode (> = 6000 bajtów).


## <a name="next-steps"></a>Kolejne kroki
- Dowiedz się więcej o [scenariusze, w których Query Store może być szczególnie przydatne](concepts-query-store-scenarios.md).
- Dowiedz się więcej o [najlepsze rozwiązania dotyczące korzystania z zapytania Store](concepts-query-store-best-practices.md).
