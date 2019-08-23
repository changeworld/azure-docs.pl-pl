---
title: Magazyn zapytań w Azure Database for PostgreSQL — pojedynczy serwer
description: W tym artykule opisano funkcję magazynu zapytań w Azure Database for PostgreSQL-pojedynczym serwerze.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 08/21/2019
ms.openlocfilehash: 5ddbff62421d97b1105a997bd084e1fe5b44cf12
ms.sourcegitcommit: beb34addde46583b6d30c2872478872552af30a1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/22/2019
ms.locfileid: "69907415"
---
# <a name="monitor-performance-with-the-query-store"></a>Monitorowanie wydajności za pomocą magazynu zapytań

**Dotyczy:** Azure Database for PostgreSQL — jeden serwer 9,6 i 10

Funkcja magazynu zapytań w Azure Database for PostgreSQL zapewnia sposób śledzenia wydajności zapytań w miarę upływu czasu. Magazyn zapytań upraszcza Rozwiązywanie problemów z wydajnością, pomagając szybko znaleźć najdłuższych uruchomionych i większości zapytań intensywnie korzystających z zasobów. Magazyn zapytań automatycznie przechwytuje historię zapytań i statystyk środowiska uruchomieniowego i zachowuje je do przeglądu. Oddziela ona dane według czasu, dzięki czemu można zobaczyć wzorce użycia bazy danych. Dane dla wszystkich użytkowników, baz danych i zapytań są przechowywane w bazie danych o nazwie **azure_sys** w wystąpieniu Azure Database for PostgreSQL.

> [!IMPORTANT]
> Nie należy modyfikować bazy danych **azure_sys** ani jej schematów. Wykonanie tej czynności uniemożliwi poprawne działanie magazynu zapytań i pokrewnych funkcji wydajności.

## <a name="enabling-query-store"></a>Włączanie magazynu zapytań
Magazyn zapytań jest funkcją wyboru, dlatego nie jest domyślnie aktywna na serwerze. Magazyn jest włączony lub wyłączony globalnie dla wszystkich baz danych na danym serwerze i nie można go włączać ani wyłączać na bazę danych.

### <a name="enable-query-store-using-the-azure-portal"></a>Włącz magazyn zapytań przy użyciu Azure Portal
1. Zaloguj się do Azure Portal i wybierz swój serwer Azure Database for PostgreSQL.
2. Wybierz opcję **parametry serwera** w sekcji **Ustawienia** w menu.
3. `pg_qs.query_capture_mode` Wyszukaj parametr.
4. Ustaw wartość na `TOP` i **Zapisz**.

Aby włączyć statystykę oczekiwania w magazynie zapytań: 
1. `pgms_wait_sampling.query_capture_mode` Wyszukaj parametr.
1. Ustaw wartość na `ALL` i **Zapisz**.


Alternatywnie możesz ustawić te parametry za pomocą interfejsu wiersza polecenia platformy Azure.
```azurecli-interactive
az postgres server configuration set --name pg_qs.query_capture_mode --resource-group myresourcegroup --server mydemoserver --value TOP
az postgres server configuration set --name pgms_wait_sampling.query_capture_mode --resource-group myresourcegroup --server mydemoserver --value ALL
```

Zaczekaj do 20 minut, aż pierwsza partia danych ma pozostać w bazie danych azure_sys.

## <a name="information-in-query-store"></a>Informacje w magazynie zapytań
Magazyn zapytań ma dwa magazyny:
- Magazyn statystyk środowiska uruchomieniowego na potrzeby utrwalania informacji statystycznych wykonywania zapytania.
- Magazyn statystyk oczekiwania na utrwalanie informacji statystycznych oczekiwania.

Typowe scenariusze korzystania z magazynu zapytań obejmują:
- Określanie liczby przypadków wykonania zapytania w danym przedziale czasu
- Porównanie średniego czasu wykonywania zapytania w oknach czasu, aby zobaczyć duże różnice
- Identyfikowanie najdłuższych uruchomionych zapytań w ciągu ostatnich X godzin
- Identyfikowanie pierwszych N zapytań, które oczekują na zasoby
- Zrozumienie charakteru oczekiwania dla konkretnego zapytania

Aby zminimalizować użycie miejsca, statystyki wykonywania środowiska uruchomieniowego w magazynie statystyk środowiska uruchomieniowego są agregowane w ustalonym, konfigurowalnym przedziale czasu. Informacje w tych sklepach są widoczne przez zapytanie dotyczące widoków magazynu zapytań.

Następujące zapytanie zwraca informacje o zapytaniach w magazynie zapytań:
```sql
SELECT * FROM query_store.qs_view; 
``` 

Lub to zapytanie dotyczące statystyk oczekiwania:
```sql
SELECT * FROM query_store.pgms_wait_sampling_view;
```

## <a name="finding-wait-queries"></a>Znajdowanie zapytań oczekujących
Typy zdarzeń oczekiwania łączą różne zdarzenia oczekiwania do zasobników według podobieństwa. Magazyn zapytań zawiera typ zdarzenia oczekiwania, konkretną nazwę zdarzenia oczekiwania i zapytanie, którego dotyczy. Aby skorelować te informacje o poczekaniu z statystykami środowiska uruchomieniowego zapytań, można lepiej zrozumieć, co przyczynia się do charakterystyki wydajności zapytań.

Poniżej przedstawiono kilka przykładów, w których można uzyskać więcej szczegółowych informacji na temat obciążenia przy użyciu statystyk oczekiwania w magazynie zapytań:

| **Uchwyceni** | **Akcja** |
|---|---|
|Duża blokada | Sprawdź teksty zapytania dla zaatakowanych zapytań i zidentyfikuj jednostki docelowe. Wyszukaj inne zapytania w magazynie zapytań, modyfikując tę samą jednostkę, która jest wykonywana często i/lub o dużym czasie trwania. Po zidentyfikowaniu tych zapytań Rozważ zmianę logiki aplikacji, aby zwiększyć współbieżność, lub użyj mniej restrykcyjnego poziomu izolacji.|
| Duże oczekiwania we/wy | Znajdź zapytania z dużą liczbą odczytów fizycznych w magazynie zapytań. Jeśli są one zgodne z zapytaniami o wysokim poziomie operacji we/wy, rozważ wprowadzenie indeksu w jednostce źródłowej w celu przeszukania zamiast skanów. Zmniejsza to obciążenie operacji we/wy dla zapytań. Sprawdź **zalecenia dotyczące wydajności** serwera w portalu, aby sprawdzić, czy istnieją zalecenia dotyczące indeksów dla tego serwera, które optymalizują zapytania.|
| Duża ilość pamięci | Znajdź zapytania zużywające najwięcej pamięci w magazynie zapytań. Te zapytania prawdopodobnie opóźnią dalsze postępy zapytań, których to dotyczy. Sprawdź **zalecenia dotyczące wydajności** serwera w portalu, aby sprawdzić, czy istnieją zalecenia dotyczące indeksów, które optymalizują te zapytania.|

## <a name="configuration-options"></a>Opcje konfiguracji
Po włączeniu magazynu zapytań dane są zapisywane w 15-minutowych oknach agregacji, do 500 oddzielnych zapytań dla każdego okna. 

Następujące opcje są dostępne na potrzeby konfigurowania parametrów magazynu zapytań.

| **Parametr** | **Opis** | **Domyślne** | **Range**|
|---|---|---|---|
| pg_qs.query_capture_mode | Ustawia, które instrukcje są śledzone. | brak | Brak, Góra, wszystkie |
| pg_qs.max_query_text_length | Ustawia maksymalną długość zapytania, którą można zapisać. Dłuższe zapytania zostaną obcięte. | 6000 | 100 – 10 TYS. |
| pg_qs.retention_period_in_days | Ustawia okres przechowywania. | 7 | 1 - 30 |
| pg_qs.track_utility | Określa, czy polecenia narzędzi są śledzone | włączone | włączone, wyłączone |

Poniższe opcje są stosowane w odniesieniu do statystyk oczekiwania.

| **Parametr** | **Opis** | **Domyślne** | **Range**|
|---|---|---|---|
| pgms_wait_sampling.query_capture_mode | Ustawia, które instrukcje są śledzone pod kątem statystyk oczekiwania. | brak | Brak, wszystkie|
| Pgms_wait_sampling.history_period | Ustaw częstotliwość próbkowania zdarzeń oczekiwania (w milisekundach). | 100 | 1-600000 |

> [!NOTE] 
> **pg_qs. query_capture_mode** zastępuje **pgms_wait_sampling. query_capture_mode**. Jeśli pg_qs. query_capture_mode ma wartość NONE, ustawienie pgms_wait_sampling. query_capture_mode nie ma żadnego efektu.


Użyj [Azure Portal](howto-configure-server-parameters-using-portal.md) lub [interfejsu wiersza polecenia platformy Azure](howto-configure-server-parameters-using-cli.md) , aby uzyskać lub ustawić inną wartość dla parametru.

## <a name="views-and-functions"></a>Widoki i funkcje
Wyświetlanie magazynu zapytań i zarządzanie nim przy użyciu następujących widoków i funkcji. Każda osoba w publicznej roli PostgreSQL może używać tych widoków do wyświetlania danych w magazynie zapytań. Te widoki są dostępne tylko w bazie danych **azure_sys** .

Zapytania są znormalizowane przez przejrzenie ich struktury po usunięciu literałów i stałych. Jeśli dwa zapytania są identyczne z wyjątkiem wartości literału, będą miały ten sam skrót.

### <a name="query_storeqs_view"></a>query_store.qs_view
Ten widok zwraca wszystkie dane w magazynie zapytań. Dla każdego unikatowego identyfikatora bazy danych, identyfikatora użytkownika i identyfikatora zapytania istnieje jeden wiersz. 

|**Nazwa**   |**Typ** | **Wołują**  | **Opis**|
|---|---|---|---|
|runtime_stats_entry_id |bigint | | Identyfikator z tabeli runtime_stats_entries|
|user_id    |oid    |pg_authid.oid  |Identyfikator OID użytkownika, który wykonał instrukcję|
|db_id  |oid    |pg_database.oid    |Identyfikator OID bazy danych, w której zostało wykonane wykonywanie instrukcji|
|query_id   |bigint  || Wewnętrzny kod skrótu obliczony na podstawie drzewa analizy instrukcji|
|query_sql_text |Varchar (10000)  || Tekst deklaracji reprezentatywnej. Różne zapytania o tej samej strukturze są klastrowane ze sobą. Ten tekst jest tekstem dla pierwszych zapytań w klastrze.|
|plan_id    |bigint |   |Identyfikator planu odpowiadającego temu zapytaniem, nie jest jeszcze dostępny|
|start_time |timestamp  ||  Zapytania są agregowane według przedziałów czasu — domyślnie jest to 15 minut. Jest to godzina rozpoczęcia odpowiadająca przedziale czasu dla tego wpisu.|
|end_time   |timestamp  ||  Godzina zakończenia odpowiadająca przedziale czasu dla tego wpisu.|
|wywołania  |bigint  || Liczba wykonanych zapytań|
|total_time |Podwójna precyzja   ||  Łączny czas wykonywania zapytania (w milisekundach)|
|min_time   |Podwójna precyzja   ||  Minimalny czas wykonywania zapytania (w milisekundach)|
|max_time   |Podwójna precyzja   ||  Maksymalny czas wykonywania zapytania (w milisekundach)|
|mean_time  |Podwójna precyzja   ||  Średni czas wykonywania zapytania (w milisekundach)|
|stddev_time|   Podwójna precyzja    ||  Odchylenie standardowe czasu wykonywania zapytania (w milisekundach) |
|wiersze   |bigint ||  Łączna liczba wierszy pobranych lub dotkniętych przez instrukcję|
|shared_blks_hit|   bigint  ||  Łączna liczba trafień w pamięci podręcznej bloków udostępnionych przez instrukcję|
|shared_blks_read|  bigint  ||  Łączna liczba bloków udostępnionych odczytywanych przez instrukcję|
|shared_blks_dirtied|   bigint   || Łączna liczba udostępnionych bloków 'dirtied przez instrukcję |
|shared_blks_written|   bigint  ||  Łączna liczba bloków udostępnionych pisanych przez instrukcję|
|local_blks_hit|    bigint ||   Łączna liczba trafień w pamięci podręcznej bloków lokalnych przez instrukcję|
|local_blks_read|   bigint   || Łączna liczba lokalnych bloków odczytywanych przez instrukcję|
|local_blks_dirtied|    bigint  ||  Łączna liczba lokalnych bloków 'dirtied przez instrukcję|
|local_blks_written|    bigint  ||  Łączna liczba bloków lokalnych pisanych przez instrukcję|
|temp_blks_read |bigint  || Łączna liczba bloków tymczasowych odczytywanych przez instrukcję|
|temp_blks_written| bigint   || Łączna liczba bloków tymczasowych pisanych przez instrukcję|
|blk_read_time  |Podwójna precyzja    || Łączny czas trwania instrukcji odczytywania bloków w milisekundach (jeśli track_io_timing jest włączona, w przeciwnym razie zero)|
|blk_write_time |Podwójna precyzja    || Łączny czas, przez jaki instrukcja zapisuje bloki zapisu w milisekundach (jeśli track_io_timing jest włączona, w przeciwnym razie zero)|
    
### <a name="query_storequery_texts_view"></a>query_store.query_texts_view
Ten widok zwraca dane tekstu zapytania w magazynie zapytań. Dla każdego oddzielnego argumentu zapytanie_tekst istnieje jeden wiersz.

|**Nazwa**|  **Typ**|   **Opis**|
|---|---|---|
|query_text_id  |bigint     |Identyfikator tabeli query_texts|
|query_sql_text |Varchar (10000)     |Tekst deklaracji reprezentatywnej. Różne zapytania o tej samej strukturze są klastrowane ze sobą. Ten tekst jest tekstem dla pierwszych zapytań w klastrze.|

### <a name="query_storepgms_wait_sampling_view"></a>query_store.pgms_wait_sampling_view
Ten widok zwraca dane zdarzeń oczekiwania w magazynie zapytań. Istnieje jeden wiersz dla każdego identyfikatora bazy danych, identyfikatora użytkownika, identyfikatora zapytania i zdarzenia.

|**Nazwa**|  **Typ**|   **Wołują**| **Opis**|
|---|---|---|---|
|user_id    |oid    |pg_authid.oid  |Identyfikator OID użytkownika, który wykonał instrukcję|
|db_id  |oid    |pg_database.oid    |Identyfikator OID bazy danych, w której zostało wykonane wykonywanie instrukcji|
|query_id   |bigint     ||Wewnętrzny kod skrótu obliczony na podstawie drzewa analizy instrukcji|
|event_type |text       ||Typ zdarzenia, dla którego zaplecze oczekuje|
|zdarzenie  |text       ||Nazwa zdarzenia oczekiwania, jeśli obecnie trwa oczekiwanie na zaplecze|
|wywołania  |Integer        ||Liczba przechwyconych zdarzeń|


### <a name="functions"></a>Funkcje
Query_store. qs_reset () zwraca wartość void

`qs_reset` odrzuca wszystkie dane statystyczne zebrane do tej pory przez magazyn zapytań. Tę funkcję można wykonać tylko przez rolę administratora serwera.

Query_store. staging_data_reset () zwraca wartość void

`staging_data_reset` odrzuca wszystkie dane statystyczne zebrane w pamięci przez magazyn zapytań (czyli dane w pamięci, które nie zostały jeszcze opróżnione do bazy danych). Tę funkcję można wykonać tylko przez rolę administratora serwera.

## <a name="limitations-and-known-issues"></a>Ograniczenia i znane problemy
- Jeśli serwer PostgreSQL ma parametr default_transaction_read_only on, magazyn zapytań nie może przechwycić danych.
- Funkcja magazynu zapytań może zostać przerwana, jeśli napotka długie zapytania Unicode (> = 6000 bajtów).
- [Odczyt replik](concepts-read-replicas.md) replikuje dane z magazynu zapytań z serwera głównego. Oznacza to, że magazyn zapytań odczytu repliki nie dostarcza statystyk dotyczących zapytań uruchomionych w replice odczytu.


## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej na temat scenariuszy, w [których magazyn zapytań może być szczególnie przydatny](concepts-query-store-scenarios.md).
- Dowiedz się więcej o [najlepszych rozwiązaniach dotyczących korzystania z magazynu zapytań](concepts-query-store-best-practices.md).
