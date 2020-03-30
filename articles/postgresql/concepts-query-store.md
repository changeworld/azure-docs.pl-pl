---
title: Query Store — usługa Azure Database for PostgreSQL — pojedynczy serwer
description: W tym artykule opisano funkcję Magazynu zapytań w usłudze Azure Database for PostgreSQL — Single Server.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/14/2019
ms.openlocfilehash: ccc503e6718ee8f516920cfbea3ad86e7ed81d84
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74768269"
---
# <a name="monitor-performance-with-the-query-store"></a>Monitorowanie wydajności w Magazynie zapytań

**Dotyczy:** Usługa Azure Database for PostgreSQL — pojedynczy serwer w wersjach 9.6, 10, 11

Funkcja Magazynu zapytań w usłudze Azure Database for PostgreSQL umożliwia śledzenie wydajności zapytań w czasie. Query Store upraszcza rozwiązywanie problemów z wydajnością, pomagając szybko znaleźć najdłużej działające i najbardziej zasobochłonne zapytania. Query Store automatycznie przechwytuje historię zapytań i statystyki środowiska uruchomieniowego i zachowuje je do przeglądu. Oddziela dane według okien czasu, dzięki czemu można zobaczyć wzorce użycia bazy danych. Dane dla wszystkich użytkowników, baz danych i kwerend są przechowywane w bazie danych o nazwie **azure_sys** w usłudze Azure Database for PostgreSQL.

> [!IMPORTANT]
> Nie należy modyfikować **azure_sys** bazy danych ani jej schematów. Spowoduje to zapobiec programowaniu Query Store i powiązanych funkcji wydajności z poprawnie działać.

## <a name="enabling-query-store"></a>Włączanie Magazynu zapytań
Query Store jest funkcją opt-in, więc nie jest domyślnie aktywna na serwerze. Magazyn jest włączony lub wyłączony globalnie dla wszystkich baz danych na danym serwerze i nie można go włączyć ani wyłączyć na bazę danych.

### <a name="enable-query-store-using-the-azure-portal"></a>Włączanie Magazynu zapytań przy użyciu portalu Azure
1. Zaloguj się do witryny Azure portal i wybierz swoją usługę Azure Database dla serwera PostgreSQL.
2. Wybierz **polecenie Parametry serwera** w sekcji **Ustawienia** menu.
3. Wyszukaj `pg_qs.query_capture_mode` parametr.
4. Ustaw wartość `TOP` i **Zapisz**.

Aby włączyć statystyki oczekiwania w Magazynie zapytań: 
1. Wyszukaj `pgms_wait_sampling.query_capture_mode` parametr.
1. Ustaw wartość `ALL` i **Zapisz**.


Alternatywnie można ustawić te parametry przy użyciu interfejsu wiersza polecenia platformy Azure.
```azurecli-interactive
az postgres server configuration set --name pg_qs.query_capture_mode --resource-group myresourcegroup --server mydemoserver --value TOP
az postgres server configuration set --name pgms_wait_sampling.query_capture_mode --resource-group myresourcegroup --server mydemoserver --value ALL
```

Poczekaj do 20 minut na utrwalenie pierwszej partii danych w bazie danych azure_sys.

## <a name="information-in-query-store"></a>Informacje w Magazynie zapytań
Query Store ma dwa sklepy:
- Magazyn statystyk środowiska wykonawczego dla utrwalania informacji statystycznych wykonywania kwerendy.
- Magazyn statystyk oczekiwania dla utrzymujących się informacji o statystykach oczekiwania.

Typowe scenariusze korzystania z Magazynu zapytań obejmują:
- Określanie liczby wykonań kwerendy w danym oknie czasu
- Porównanie średniego czasu wykonywania kwerendy w oknach czasowych w celu wyświetlenia dużych delt
- Identyfikowanie najdłużej działających zapytań w ciągu ostatnich X godzin
- Identyfikowanie najlepszych kwerend N oczekujących na zasoby
- Zrozumienie charakteru oczekiwania na określoną kwerendę

Aby zminimalizować użycie miejsca, statystyki wykonywania środowiska wykonawczego w magazynie statystyk środowiska wykonawczego są agregowane w stałym, konfigurowalnym oknie czasu. Informacje w tych magazynach są widoczne przez wykonywanie zapytań widoków magazynu zapytań.

## <a name="access-query-store-information"></a>Informacje o Magazynie kwerend programu Access

Dane magazynu query store są przechowywane w azure_sys bazie danych na serwerze Postgres. 

Następująca kwerenda zwraca informacje o kwerendach w Magazynie kwerend:
```sql
SELECT * FROM query_store.qs_view; 
``` 

Lub to zapytanie dotyczące statystyk oczekiwania:
```sql
SELECT * FROM query_store.pgms_wait_sampling_view;
```

Można również emitować dane magazynu zapytań do [dzienników usługi Azure Monitor](../azure-monitor/log-query/log-query-overview.md) w celu analizy i alertów, centrów zdarzeń do przesyłania strumieniowego i usługi Azure Storage do archiwizacji. Kategorie dziennika do skonfigurowania to **QueryStoreRuntimeStatistics** i **QueryStoreWaitStatistics**. Aby dowiedzieć się więcej o konfiguracji, zapoznaj się z artykułem [ustawień diagnostycznych usługi Azure Monitor.](../azure-monitor/platform/diagnostic-settings.md)


## <a name="finding-wait-queries"></a>Znajdowanie zapytań oczekujących
Typy zdarzeń oczekiwania łączą różne zdarzenia oczekiwania w zasobnikach według podobieństwa. Query Store zawiera typ zdarzenia oczekiwania, określoną nazwę zdarzenia oczekiwania i kwerendę, o jakiej mowa. Możliwość skorelowania tych informacji oczekiwania ze statystykami środowiska wykonawczego kwerendy oznacza, że można uzyskać głębsze zrozumienie, co przyczynia się do właściwości wydajności kwerendy.

Oto kilka przykładów, jak można uzyskać więcej wglądu w obciążenie przy użyciu statystyk oczekiwania w Magazynie zapytań:

| **Obserwacji** | **Akcja** |
|---|---|
|High Lock czeka | Sprawdź teksty zapytań dla kwerend, których dotyczy problem i identyfikuj jednostki docelowe. Szukaj w Magazynie zapytań dla innych kwerend modyfikujących tę samą jednostkę, która jest wykonywana często i/lub mają wysoki czas trwania. Po zidentyfikowaniu tych zapytań należy rozważyć zmianę logiki aplikacji w celu poprawy współbieżności lub użyć mniej restrykcyjnego poziomu izolacji.|
| Wysoki bufor we/wy czeka | Znajdź zapytania z dużą liczbą odczytów fizycznych w Magazynie zapytań. Jeśli pasują do zapytań z wysokim oczekiwaniem we/wy, należy rozważyć wprowadzenie indeksu do jednostki podstawowej, aby zrobić szuka zamiast skanowania. Pozwoliłoby to zminimalizować obciążenie we/wy kwerend. Sprawdź **zalecenia dotyczące wydajności** serwera w portalu, aby sprawdzić, czy istnieją zalecenia dotyczące indeksu dla tego serwera, które zoptymalizowałyby zapytania.|
| Wysoka pamięć czeka | Znajdź kwerendy zużywające najlepszą pamięć w Magazynie zapytań. Te zapytania prawdopodobnie opóźniają dalszy postęp kwerend, których dotyczy problem. Sprawdź **zalecenia dotyczące wydajności** serwera w portalu, aby sprawdzić, czy istnieją zalecenia dotyczące indeksu, które optymalizują te zapytania.|

## <a name="configuration-options"></a>Opcje konfiguracji
Po włączeniu Magazynu zapytań zapisuje dane w 15-minutowych oknach agregacji, maksymalnie 500 różnych zapytań w oknie. 

Następujące opcje są dostępne do konfigurowania parametrów Magazynu zapytań.

| **Parametr** | **Opis** | **Domyślny** | **Zakres**|
|---|---|---|---|
| pg_qs.query_capture_mode | Ustawia, które instrukcje są śledzone. | brak | brak, top, wszystkie |
| pg_qs.max_query_text_length | Ustawia maksymalną długość zapytania, która może zostać zapisana. Dłuższe zapytania zostaną obcięty. | 6000 | 100 - 10K |
| pg_qs.retention_period_in_days | Ustawia okres przechowywania. | 7 | 1 - 30 |
| pg_qs.track_utility | Określa, czy polecenia narzędzia są śledzone | on | wł., wył. |

Poniższe opcje dotyczą w szczególności statystyk oczekiwania.

| **Parametr** | **Opis** | **Domyślny** | **Zakres**|
|---|---|---|---|
| pgms_wait_sampling.query_capture_mode | Ustawia, które instrukcje są śledzone dla statystyk oczekiwania. | brak | żaden, wszystkie|
| Pgms_wait_sampling.history_period | Ustaw częstotliwość w milisekundach, w której są próbkowania zdarzeń oczekiwania. | 100 | 1-600000 |

> [!NOTE] 
> **pg_qs.query_capture_mode** zastępuje **pgms_wait_sampling.query_capture_mode**. Jeśli pg_qs.query_capture_mode jest NONE, ustawienie pgms_wait_sampling.query_capture_mode nie ma wpływu.


Użyj [witryny Azure portal](howto-configure-server-parameters-using-portal.md) lub [interfejsu wiersza polecenia platformy Azure,](howto-configure-server-parameters-using-cli.md) aby uzyskać lub ustawić inną wartość dla parametru.

## <a name="views-and-functions"></a>Widoki i funkcje
Wyświetlanie magazynu zapytań i zarządzanie nim przy użyciu następujących widoków i funkcji. Każda osoba w roli publicznej PostgreSQL może użyć tych widoków, aby wyświetlić dane w Magazynie zapytań. Widoki te są dostępne tylko w **azure_sys** bazie danych.

Zapytania są normalizowane, patrząc na ich strukturę po usunięciu literałów i stałych. Jeśli dwa zapytania są identyczne z wyjątkiem wartości literału, będą miały ten sam skrót.

### <a name="query_storeqs_view"></a>query_store.qs_view
Ten widok zwraca wszystkie dane w Magazynie zapytań. Istnieje jeden wiersz dla każdego identyfikatora bazy danych, identyfikatora użytkownika i identyfikatora kwerendy. 

|**Nazwa**   |**Typ** | **Odwołania**  | **Opis**|
|---|---|---|---|
|runtime_stats_entry_id |bigint | | Identyfikator z tabeli runtime_stats_entries|
|user_id    |Oid    |pg_authid.oid  |OID użytkownika, który wykonał instrukcję|
|Db_id  |Oid    |pg_database.oid    |OID bazy danych, w której instrukcja została wykonana|
|query_id   |bigint  || Wewnętrzny kod skrótu, obliczony na podstawie drzewa analizy instrukcji|
|query_sql_text |Varchar(10000)  || Tekst oświadczenia przedstawiciela. Różne zapytania o tej samej strukturze są grupowane razem; ten tekst jest tekstem dla pierwszego z zapytań w klastrze.|
|plan_id    |bigint |   |Identyfikator planu odpowiadającego tej kwerendzie, nieuodnawęziony jeszcze|
|Start_time |sygnatura czasowa  ||  Zapytania są agregowane według przedziałów czasu — przedział czasu zasobnika wynosi domyślnie 15 minut. Jest to czas rozpoczęcia odpowiadający zasobnikowi czasu dla tego wpisu.|
|End_time   |sygnatura czasowa  ||  Czas zakończenia odpowiadający zasobnikowi czasu dla tego wpisu.|
|Wywołania  |bigint  || Ile razy kwerenda jest wykonywana|
|total_time |podwójna precyzja   ||  Całkowity czas wykonywania kwerendy w milisekundach|
|min_time   |podwójna precyzja   ||  Minimalny czas wykonywania kwerendy w milisekundach|
|max_time   |podwójna precyzja   ||  Maksymalny czas wykonywania kwerendy w milisekundach|
|mean_time  |podwójna precyzja   ||  Średni czas wykonywania kwerendy w milisekundach|
|stddev_time|   podwójna precyzja    ||  Odchylenie standardowe czasu wykonywania kwerendy w milisekundach |
|Wierszy   |bigint ||  Całkowita liczba wierszy pobranych lub dotkniętych instrukcją|
|shared_blks_hit|   bigint  ||  Całkowita liczba trafień udostępnionej pamięci podręcznej bloków przez instrukcję|
|shared_blks_read|  bigint  ||  Całkowita liczba udostępnionych bloków odczytanych przez instrukcję|
|shared_blks_dirtied|   bigint   || Całkowita liczba współdzielonych bloków zabrudzonych oświadczeniem |
|shared_blks_written|   bigint  ||  Łączna liczba udostępnionych bloków zapisanych w instrukcji|
|local_blks_hit|    bigint ||   Całkowita liczba trafień pamięci podręcznej bloków lokalnych przez instrukcję|
|local_blks_read|   bigint   || Całkowita liczba bloków lokalnych odczytanych przez instrukcję|
|local_blks_dirtied|    bigint  ||  Całkowita liczba bloków lokalnych zabrudzonych oświadczeniem|
|local_blks_written|    bigint  ||  Całkowita liczba bloków lokalnych zapisanych w instrukcji|
|temp_blks_read |bigint  || Całkowita liczba bloków tymczasowych odczytanych przez instrukcję|
|temp_blks_written| bigint   || Całkowita liczba bloków tymczasowych zapisanych przez instrukcję|
|blk_read_time  |podwójna precyzja    || Całkowity czas spędzony na blokach odczytu instrukcji w milisekundach (jeśli jest włączona track_io_timing, w przeciwnym razie zero)|
|blk_write_time |podwójna precyzja    || Całkowity czas spędzony na zapisywaniu bloków instrukcji w milisekundach (jeśli jest włączona track_io_timing, w przeciwnym razie zero)|
    
### <a name="query_storequery_texts_view"></a>query_store.query_texts_view
Ten widok zwraca dane tekstowe kwerendy w Magazynie zapytań. Istnieje jeden wiersz dla każdego odrębnego query_text.

|**Nazwa**|  **Typ**|   **Opis**|
|---|---|---|
|query_text_id  |bigint     |Identyfikator tabeli query_texts|
|query_sql_text |Varchar(10000)     |Tekst oświadczenia przedstawiciela. Różne zapytania o tej samej strukturze są grupowane razem; ten tekst jest tekstem dla pierwszego z zapytań w klastrze.|

### <a name="query_storepgms_wait_sampling_view"></a>query_store.pgms_wait_sampling_view
Ten widok zwraca dane zdarzeń oczekiwania w Magazynie zapytań. Istnieje jeden wiersz dla każdego identyfikatora bazy danych, identyfikatora użytkownika, identyfikatora kwerendy i zdarzenia.

|**Nazwa**|  **Typ**|   **Odwołania**| **Opis**|
|---|---|---|---|
|user_id    |Oid    |pg_authid.oid  |OID użytkownika, który wykonał instrukcję|
|Db_id  |Oid    |pg_database.oid    |OID bazy danych, w której instrukcja została wykonana|
|query_id   |bigint     ||Wewnętrzny kod skrótu, obliczony na podstawie drzewa analizy instrukcji|
|Event_type |tekst       ||Typ zdarzenia, na które oczekuje zaplecze|
|event  |tekst       ||Nazwa zdarzenia oczekiwania, jeśli backend jest obecnie oczekiwany|
|Wywołania  |Liczba całkowita        ||Liczba przechwyconych zdarzeń|


### <a name="functions"></a>Funkcje
Query_store.qs_reset() zwraca

`qs_reset` odrzuca wszystkie statystyki zebrane do tej pory przez Query Store. Ta funkcja może być wykonywana tylko przez rolę administratora serwera.

Query_store.staging_data_reset() zwraca

`staging_data_reset` odrzuca wszystkie statystyki zebrane w pamięci przez Query Store (czyli dane w pamięci, które nie zostały jeszcze opróżnione do bazy danych). Ta funkcja może być wykonywana tylko przez rolę administratora serwera.

## <a name="limitations-and-known-issues"></a>Ograniczenia i znane problemy
- Jeśli serwer PostgreSQL ma parametr default_transaction_read_only włączony, Query Store nie może przechwytywać danych.
- Funkcje magazynu zapytań mogą zostać przerwane, jeśli napotkają długie zapytania Unicode (>= 6000 bajtów).
- [Odczyt repliki replikują](concepts-read-replicas.md) dane Magazynu zapytań z serwera głównego. Oznacza to, że magazyn zapytań repliki odczytu nie zawiera statystyk dotyczących zapytań uruchamianych w replice odczytu.


## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o [scenariuszach, w których magazyn zapytań może być szczególnie pomocny](concepts-query-store-scenarios.md).
- Dowiedz się więcej o [sprawdzonych praktykach dotyczących korzystania z Magazynu zapytań](concepts-query-store-best-practices.md).
