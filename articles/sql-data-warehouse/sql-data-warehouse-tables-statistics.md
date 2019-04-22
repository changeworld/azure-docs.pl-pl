---
title: Tworzenie, aktualizowanie statystyk — Azure SQL Data Warehouse | Dokumentacja firmy Microsoft
description: Zalecenia i przykłady do tworzenia i aktualizowania statystyk optymalizacji zapytań dotyczących tabel w usłudze Azure SQL Data Warehouse.
services: sql-data-warehouse
author: ckarst
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: implement
ms.date: 05/09/2018
ms.author: kevin
ms.reviewer: jrasnick
ms.custom: seoapril2019
ms.openlocfilehash: 62007624bdf2b5f1b9c387bcc51d58c020860913
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59279775"
---
# <a name="table-statistics-in-azure-sql-data-warehouse"></a>Statystyki tabeli w usłudze Azure SQL Data Warehouse

Zalecenia i przykłady do tworzenia i aktualizowania statystyk optymalizacji zapytań dotyczących tabel w usłudze Azure SQL Data Warehouse.

## <a name="why-use-statistics"></a>Dlaczego warto używać statystyki

Im więcej obsługującemu danych Azure SQL Data Warehouse, szybciej można wykonywać zapytania względem jego. Po załadowaniu danych do usługi SQL Data Warehouse jest jednym z najważniejszych czynników, które można zrobić, aby zoptymalizować zapytania zbieranie statystyk na podstawie posiadanych danych. Optymalizator zapytań usługi SQL Data Warehouse to optymalizator oparty na kosztach. Porównuje koszt różnych planów zapytań, a następnie wybiera planu, który generuje najniższy koszt. W większości przypadków go wybierze plan, który będzie wykonywał najszybsza. Na przykład jeśli Optymalizator szacuje się, że daty filtrowanie zapytanie zwróci jeden wiersz go wybierze jeden plan. Szacuje go, że wybraną datą będzie zwracać 1 milion wierszy, zwróci na inny plan.

## <a name="automatic-creation-of-statistic"></a>Automatyczne tworzenie statystyk

Po włączeniu opcji AUTO_CREATE_STATISTICS bazy danych SQL Data Warehouse analizuje przychodzące zapytania użytkowników w celach ma statystyk. Jeśli brakuje statystyki Optymalizator zapytań umożliwia utworzenie statystyk dotyczących poszczególnych kolumn w warunek predykatu lub łączony albo oczekuje kwerendy do poprawy szacowania kardynalności dla planu zapytania. Automatyczne tworzenie statystyk jest obecnie włączona domyślnie.

Możesz sprawdzić, czy magazyn danych ma AUTO_CREATE_STATISTICS konfigurowane przez uruchomienie następującego polecenia:

```sql
SELECT name, is_auto_create_stats_on
FROM sys.databases
```

Jeśli magazyn danych nie ma AUTO_CREATE_STATISTICS skonfigurowane, zalecamy włączenie tej właściwości, uruchamiając następujące polecenie:

```sql
ALTER DATABASE <yourdatawarehousename>
SET AUTO_CREATE_STATISTICS ON
```

Te instrukcje wywoła automatyczne tworzenie statystyk:

- SELECT
- WYBIERZ OPCJĘ WSTAW
- CTAS
- UPDATE
- DELETE
- WYJAŚNIONO po wykryciu zawierające sprzężenia lub obecności predykatu

> [!NOTE]
> Automatyczne tworzenie statystyk nie są tworzone w tabelach tymczasowych lub zewnętrznego.

Automatyczne tworzenie statystyk odbywa się synchronicznie, może spowodować naliczenie zapytania nieco pogorszenie wydajności w przypadku kolumn bez statystyk. Czas, aby utworzyć statystyki dla pojedynczej kolumny zależy od rozmiaru tabeli. Aby uniknąć obniżenia wydajności mierzalne, szczególnie w przypadku testów porównawczych wydajności, należy upewnij się, że statystyki mają najpierw utworzyć, wykonując obciążenia porównawczego przed rozpoczęciem profilowania systemu.

> [!NOTE]
> Tworzenie statystyk, które będą rejestrowane w [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?view=azure-sqldw-latest) w kontekście innego użytkownika.

Po utworzeniu automatyczne statystyk, będą one miały formularza: _WA_Sys_< 8-cyfrowy identyfikator kolumny w zapisie szesnastkowym > _ < 8-cyfrowy identyfikator tabeli w zapisie szesnastkowym >. Możesz wyświetlić statystyki, które zostały już utworzone, uruchamiając [DBCC SHOW_STATISTICS](/sql/t-sql/database-console-commands/dbcc-show-statistics-transact-sql?view=azure-sqldw-latest) polecenia:

```sql
DBCC SHOW_STATISTICS (<table_name>, <target>)
```

Nazwa_tabeli jest nazwą tabeli zawierającej statystykę do wyświetlenia. Nie może to być tabeli zewnętrznej. Element docelowy jest nazwą indeksu docelowego, statystyk lub kolumny, dla którego chcesz wyświetlić informacje statystyczne.

## <a name="updating-statistics"></a>Zaktualizowanie statystyk

Jeden najlepszym rozwiązaniem jest można zaktualizować statystyki dla kolumn daty każdego dnia, ponieważ są dodawane nowe daty. Każdy czas nowe wiersze są ładowane do magazynu danych, nowe obciążenia daty lub daty transakcji są dodawane. Te zmiany dystrybucję danych i upewnij się nieaktualna statystyki. Z drugiej strony, statystyki dotyczące kraju kolumny w tabeli klientów nigdy nie potrzebować do zaktualizowania, ponieważ rozkładu wartości zwykle nie ulega zmianie. Przy założeniu, że dystrybucja jest stałe od klientów, dodawanie nowych wierszy do tabeli zmian nie zamierza zmienić dystrybucję danych. Jednak jeśli magazyn danych zawiera tylko w jednym kraju, możesz wyświetlić dane z nowego kraju wynikające dane z wielu krajów i przechowywane, następnie należy zaktualizować statystyki dotyczące kolumn kraju.

Poniżej przedstawiono zalecenia dotyczące zaktualizowanie statystyk:

|||
|-|-|
| **Częstotliwości aktualizacji statystyk**  | Zachowawcze: Codziennie </br> Po ładowania i przekształcania danych |
| **Próbkowanie** |  Mniej niż 1 miliard wierszy, użyj domyślnej próbkowania (20 procent). </br> Za pomocą więcej niż 1 miliard wierszy należy użyć niektóre spośród dwóch procent. |

Jeden z pierwszych pytań zadać, kiedy rozwiązywaniu zapytania jest, **"Są statystyki na bieżąco?"**

To pytanie nie jest taki, który można uzyskać od wieku danych. Statystyki aktualne obiektu może być stare, jeśli został bez znaczących zmian w danych źródłowych. Gdy liczba wierszy zmienił znacząco lub jest istotna zmiana rozkładu wartości dla kolumny, *następnie* nadszedł czas na aktualizowanie statystyk.

Nie ma żadnych dynamicznego widoku zarządzania można określić, jeśli dane w tabeli został zmieniony od czasu ostatniego statystyk czasu zostały zaktualizowane. Wiek statystyk, wiedząc, może zapewnić Ci część obrazu. Następujące zapytanie służy do określenia czasu, gdy zaktualizowano statystyk dla każdej tabeli.

> [!NOTE]
> W przypadku istotnej zmiany w dystrybucji wartości kolumny należy zaktualizować statystyk, niezależnie od tego czasu, gdy zostały one aktualizowane.

```sql
SELECT
    sm.[name] AS [schema_name],
    tb.[name] AS [table_name],
    co.[name] AS [stats_column_name],
    st.[name] AS [stats_name],
    STATS_DATE(st.[object_id],st.[stats_id]) AS [stats_last_updated_date]
FROM
    sys.objects ob
    JOIN sys.stats st
        ON  ob.[object_id] = st.[object_id]
    JOIN sys.stats_columns sc
        ON  st.[stats_id] = sc.[stats_id]
        AND st.[object_id] = sc.[object_id]
    JOIN sys.columns co
        ON  sc.[column_id] = co.[column_id]
        AND sc.[object_id] = co.[object_id]
    JOIN sys.types  ty
        ON  co.[user_type_id] = ty.[user_type_id]
    JOIN sys.tables tb
        ON  co.[object_id] = tb.[object_id]
    JOIN sys.schemas sm
        ON  tb.[schema_id] = sm.[schema_id]
WHERE
    st.[user_created] = 1;
```

**Data kolumny** w magazynie danych, na przykład, zwykle wymagają częstego aktualizacji statystyk. Każdy czas nowe wiersze są ładowane do magazynu danych, nowe obciążenia daty lub daty transakcji są dodawane. Te zmiany dystrybucję danych i upewnij się nieaktualna statystyki. Z drugiej strony statystyki płeć kolumny w tabeli klientów nigdy nie może być konieczne do zaktualizowania. Przy założeniu, że dystrybucja jest stałe od klientów, dodawanie nowych wierszy do tabeli zmian nie zamierza zmienić dystrybucję danych. Jednak jeśli magazyn danych zawiera tylko jeden płeć i nowe wyniki wymagania w wielu płci, następnie należy zaktualizować statystyk w kolumnie płci.

Aby uzyskać więcej informacji, zobacz ogólne wskazówki dotyczące [statystyki](/sql/relational-databases/statistics/statistics).

## <a name="implementing-statistics-management"></a>Implementowanie zarządzania statystyki

Często jest dobry pomysł, aby rozszerzyć proces ładowania danych, aby upewnić się, że statystyki są aktualizowane na końcu obciążenia. Ładowanie danych to tabele najczęściej zmiany ich rozmiaru i/lub ich dystrybucji wartości. Dlatego jest logiczne miejsce do wykonania niektórych procesów zarządzania.

Do aktualizowania statystyk podczas procesu ładowania, dostępne są następujące wytyczne:

* Upewnij się, że każda tabela załadować zawiera co najmniej jeden obiekt statystyki aktualizacji. Spowoduje to zaktualizowanie informacji o rozmiarze (liczba wierszy i liczbę stron) tabeli w ramach aktualizacji statystyk.
* Skup się na kolumn uczestniczących w klauzuli sprzężenia, GROUP BY, klauzula ORDER BY i DISTINCT.
* Należy rozważyć aktualizowanie kolumny "w kolejności rosnącej klucza", takie jak transakcji daty częściej, ponieważ te wartości nie zostaną uwzględnione na histogramie statystyki.
* Należy rozważyć aktualizowanie kolumn dystrybucji statyczne często.
* Należy pamiętać, że każdy obiekt Statystyka zostanie zaktualizowany w sekwencji. Po prostu Implementowanie `UPDATE STATISTICS <TABLE_NAME>` nie zawsze jest idealnym rozwiązaniem, zwłaszcza w przypadku szerokich tabel z dużą liczbą obiektów statystyk.

Aby uzyskać więcej informacji, zobacz [szacunkowa ocena kardynalności](/sql/relational-databases/performance/cardinality-estimation-sql-server).

## <a name="examples-create-statistics"></a>Przykłady: Tworzenie statystyk

Te przykłady przedstawiają sposób tworzenie statystyk na użytek różnych opcji. Opcje używane dla każdej kolumny są zależne od właściwości danych i używania kolumny w zapytaniach.

### <a name="create-single-column-statistics-with-default-options"></a>Tworzenie statystyk jedna kolumna z użyciem opcji domyślnych

Aby utworzyć statystyki dla kolumny, wystarczy podać nazwę dla obiektu statystyk i nazwę kolumny.

Ta składnia wykorzystuje wszystkie opcje domyślne. Domyślnie usługa SQL Data Warehouse przykłady **20 procent** tabeli podczas tworzenia statystyk.

```sql
CREATE STATISTICS [statistics_name] ON [schema_name].[table_name]([column_name]);
```

Na przykład:

```sql
CREATE STATISTICS col1_stats ON dbo.table1 (col1);
```

### <a name="create-single-column-statistics-by-examining-every-row"></a>Tworzenie statystyk jednokolumnową, sprawdzając każdy wiersz

Domyślna częstotliwość próbkowania równy 20% jest wystarczające w większości sytuacji. Można jednak dostosować częstotliwość próbkowania.

Do próbkowania pełną tabelę, użyj następującej składni:

```sql
CREATE STATISTICS [statistics_name] ON [schema_name].[table_name]([column_name]) WITH FULLSCAN;
```

Na przykład:

```sql
CREATE STATISTICS col1_stats ON dbo.table1 (col1) WITH FULLSCAN;
```

### <a name="create-single-column-statistics-by-specifying-the-sample-size"></a>Tworzenie statystyk jednokolumnową, określając wielkość próbki

Alternatywnie można określić rozmiar próbki jako procent:

```sql
CREATE STATISTICS col1_stats ON dbo.table1 (col1) WITH SAMPLE = 50 PERCENT;
```

### <a name="create-single-column-statistics-on-only-some-of-the-rows"></a>Tworzenie statystyk jednokolumnową tylko na niektórych wierszy

Można również utworzyć statystyki w części wiersze w tabeli. Jest to nazywane filtrowane statystyki.

Na przykład można użyć statystykę filtrowaną, planując zapytania na określoną partycję dużych tabeli partycjonowanej. Tworzenie statystyk na podstawie wartości partycji, dokładność danych statystycznych będzie poprawić, a zatem poprawić wydajność zapytań.

Ten przykład umożliwia utworzenie statystyk na zakres wartości. Łatwo można zdefiniować wartości do dopasowania zakres wartości w partycji.

```sql
CREATE STATISTICS stats_col1 ON table1(col1) WHERE col1 > '2000101' AND col1 < '20001231';
```

> [!NOTE]
> Optymalizator zapytań należy wziąć pod uwagę przy użyciu statystykę filtrowaną, gdy go wybierze planu zapytania rozproszonego zapytanie musi mieścić się w definicji obiektu statystyk. W poprzednim przykładzie, zapytania gdzie klauzula wymaga określenia wartości col1 między 2000101 i 20001231.

### <a name="create-single-column-statistics-with-all-the-options"></a>Tworzenie statystyk jednokolumnową ze wszystkimi opcjami

Opcje można również połączyć ze sobą. Poniższy przykład tworzy obiekt statystykę filtrowaną o rozmiarze próbki niestandardowe:

```sql
CREATE STATISTICS stats_col1 ON table1 (col1) WHERE col1 > '2000101' AND col1 < '20001231' WITH SAMPLE = 50 PERCENT;
```

Aby uzyskać pełną dokumentację, zobacz [CREATE STATISTICS](/sql/t-sql/statements/create-statistics-transact-sql).

### <a name="create-multi-column-statistics"></a>Tworzenie statystyk na wielu kolumnach

Aby utworzyć obiekt wielokolumnowych statystyki, po prostu użyć poprzednich przykładach jednak określić większą liczbę kolumn.

> [!NOTE]
> Histogram, który jest używany, aby oszacować liczbę wierszy w wyniku zapytania, jest dostępna tylko dla pierwszej kolumny, które są wymienione w definicji obiektu statystyk.

W tym przykładzie histogram znajduje się na *produktu\_kategorii*. Statystyki dla wielu kolumny są obliczane na *produktu\_kategorii* i *produktu\_sub_category*:

```sql
CREATE STATISTICS stats_2cols ON table1 (product_category, product_sub_category) WHERE product_category > '2000101' AND product_category < '20001231' WITH SAMPLE = 50 PERCENT;
```

Ponieważ istnieje korelacja między *produktu\_kategorii* i *produktu\_sub\_kategorii*, obiekt statystyki wielokolumnowych może być przydatne, jeśli te kolumny są dostępne w tym samym czasie.

### <a name="create-statistics-on-all-columns-in-a-table"></a>Tworzenie statystyk dla wszystkich kolumn w tabeli

Jednym ze sposobów tworzenia statystyk jest wydawać polecenia CREATE STATISTICS po utworzeniu tabeli:

```sql
CREATE TABLE dbo.table1
(
   col1 int
,  col2 int
,  col3 int
)
WITH
  (
    CLUSTERED COLUMNSTORE INDEX
  )
;

CREATE STATISTICS stats_col1 on dbo.table1 (col1);
CREATE STATISTICS stats_col2 on dbo.table2 (col2);
CREATE STATISTICS stats_col3 on dbo.table3 (col3);
```

### <a name="use-a-stored-procedure-to-create-statistics-on-all-columns-in-a-database"></a>Użyj procedury składowanej utworzyć statystyki dla wszystkich kolumn w bazie danych

Usługa SQL Data Warehouse ma systemowej procedury składowanej odpowiednikiem sp_create_stats w programie SQL Server. Tę procedurę składowaną tworzy obiekt statystyki jedną kolumnę dla każdej z kolumn bazy danych, która nie ma jeszcze statystyk.

Poniższy przykład pomoże Ci rozpocząć pracę z projektu bazy danych. Możesz dostosować ją do własnych potrzeb:

```sql
CREATE PROCEDURE    [dbo].[prc_sqldw_create_stats]
(   @create_type    tinyint -- 1 default 2 Fullscan 3 Sample
,   @sample_pct     tinyint
)
AS

IF @create_type IS NULL
BEGIN
    SET @create_type = 1;
END;

IF @create_type NOT IN (1,2,3)
BEGIN
    THROW 151000,'Invalid value for @stats_type parameter. Valid range 1 (default), 2 (fullscan) or 3 (sample).',1;
END;

IF @sample_pct IS NULL
BEGIN;
    SET @sample_pct = 20;
END;

IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN;
    DROP TABLE #stats_ddl;
END;

CREATE TABLE #stats_ddl
WITH    (   DISTRIBUTION    = HASH([seq_nmbr])
        ,   LOCATION        = USER_DB
        )
AS
WITH T
AS
(
SELECT      t.[name]                        AS [table_name]
,           s.[name]                        AS [table_schema_name]
,           c.[name]                        AS [column_name]
,           c.[column_id]                   AS [column_id]
,           t.[object_id]                   AS [object_id]
,           ROW_NUMBER()
            OVER(ORDER BY (SELECT NULL))    AS [seq_nmbr]
FROM        sys.[tables] t
JOIN        sys.[schemas] s         ON  t.[schema_id]       = s.[schema_id]
JOIN        sys.[columns] c         ON  t.[object_id]       = c.[object_id]
LEFT JOIN   sys.[stats_columns] l   ON  l.[object_id]       = c.[object_id]
                                    AND l.[column_id]       = c.[column_id]
                                    AND l.[stats_column_id] = 1
LEFT JOIN    sys.[external_tables] e    ON    e.[object_id]        = t.[object_id]
WHERE       l.[object_id] IS NULL
AND            e.[object_id] IS NULL -- not an external table
)
SELECT  [table_schema_name]
,       [table_name]
,       [column_name]
,       [column_id]
,       [object_id]
,       [seq_nmbr]
,       CASE @create_type
        WHEN 1
        THEN    CAST('CREATE STATISTICS '+QUOTENAME('stat_'+table_schema_name+ '_' + table_name + '_'+column_name)+' ON '+QUOTENAME(table_schema_name)+'.'+QUOTENAME(table_name)+'('+QUOTENAME(column_name)+')' AS VARCHAR(8000))
        WHEN 2
        THEN    CAST('CREATE STATISTICS '+QUOTENAME('stat_'+table_schema_name+ '_' + table_name + '_'+column_name)+' ON '+QUOTENAME(table_schema_name)+'.'+QUOTENAME(table_name)+'('+QUOTENAME(column_name)+') WITH FULLSCAN' AS VARCHAR(8000))
        WHEN 3
        THEN    CAST('CREATE STATISTICS '+QUOTENAME('stat_'+table_schema_name+ '_' + table_name + '_'+column_name)+' ON '+QUOTENAME(table_schema_name)+'.'+QUOTENAME(table_name)+'('+QUOTENAME(column_name)+') WITH SAMPLE '+CONVERT(varchar(4),@sample_pct)+' PERCENT' AS VARCHAR(8000))
        END AS create_stat_ddl
FROM T
;

DECLARE @i INT              = 1
,       @t INT              = (SELECT COUNT(*) FROM #stats_ddl)
,       @s NVARCHAR(4000)   = N''
;

WHILE @i <= @t
BEGIN
    SET @s=(SELECT create_stat_ddl FROM #stats_ddl WHERE seq_nmbr = @i);

    PRINT @s
    EXEC sp_executesql @s
    SET @i+=1;
END

DROP TABLE #stats_ddl;
```

Aby utworzyć statystyki dla wszystkich kolumn w tabeli z użyciem wartości domyślnych, wykonaj procedurę składowaną.

```sql
EXEC [dbo].[prc_sqldw_create_stats] 1, NULL;
```

Aby utworzyć statystyki dla wszystkich kolumn w tabeli, używając fullscan, wywołania tej procedury:

```sql
EXEC [dbo].[prc_sqldw_create_stats] 2, NULL;
```

Aby utworzyć próbkowanych statystyk dla wszystkich kolumn w tabeli, wprowadź 3 i procentu próbki. Częstotliwość próbkowania 20 procent korzysta z tej procedury.

```sql
EXEC [dbo].[prc_sqldw_create_stats] 3, 20;
```

Aby utworzyć próbkowanych statystyk dla wszystkich kolumn

## <a name="examples-update-statistics"></a>Przykłady: Zaktualizuj statystyki

Aktualizowanie statystyk, możesz wykonywać następujące czynności:

- Aktualizacja statystyk jeden obiekt. Określ nazwę obiektu statystyki, który chcesz zaktualizować.
- Zaktualizuj wszystkie obiekty statystyk na tabelę. Określ nazwę tabeli zamiast jednego obiektu poszczególnych statystyk.

### <a name="update-one-specific-statistics-object"></a>Zaktualizuj jeden obiekt poszczególnych statystyk

Aby zaktualizować obiekt poszczególnych statystyk, użyj następującej składni:

```sql
UPDATE STATISTICS [schema_name].[table_name]([stat_name]);
```

Na przykład:

```sql
UPDATE STATISTICS [dbo].[table1] ([stats_col1]);
```

Aktualizując poszczególnych statystyk obiektów, można zminimalizować czas i zasoby wymagane do zarządzania statystyk. To wymaga pewnego traktować wybrać najlepsze obiektów statystyki do zaktualizowania.

### <a name="update-all-statistics-on-a-table"></a>Zaktualizuj wszystkie statystyki tabeli

Jest to prosta metoda aktualizowania wszystkich obiektów statystyki dla tabeli:

```sql
UPDATE STATISTICS [schema_name].[table_name];
```

Na przykład:

```sql
UPDATE STATISTICS dbo.table1;
```

Instrukcja UPDATE STATISTICS jest łatwa w użyciu. Należy jednak pamiętać, że powoduje zaktualizowanie *wszystkie* statystyk dotyczących tabeli, a w związku z tym może wykonać więcej pracy niż jest to konieczne. Jeśli wydajność nie ma problemu, to najprostszy i najbardziej zaawansowane sposobem zagwarantowania, że statystyki są aktualne.

> [!NOTE]
> Podczas aktualizowania wszystkich statystyk dotyczących tabeli, usługa SQL Data Warehouse wykonuje skanowanie, aby przykładowej tabeli dla każdego obiektu statystyk. Jeśli tabela jest duża i ma wiele kolumn i ilość danych statystycznych, może być bardziej efektywne aktualizowanie statystyk indywidualnych potrzeb.

Na implementację `UPDATE STATISTICS` procedury, zobacz [tabele tymczasowe](sql-data-warehouse-tables-temporary.md). Metoda wdrażania jest nieco różne od poprzedniego `CREATE STATISTICS` procedura, ale wynik jest taki sam.

Aby uzyskać pełną składnię, zobacz [Update Statistics](/sql/t-sql/statements/update-statistics-transact-sql).

## <a name="statistics-metadata"></a>Statystyki metadanych

Istnieje kilka widoków systemowych i funkcje, które można użyć, aby znaleźć informacje dotyczące statystyk. Na przykład możesz zobaczyć, jeśli obiekt statystyki może być nieaktualna przy użyciu funkcji date statystyki pozwalają zobaczyć, kiedy ostatnio utworzono lub zaktualizowano statystyk.

### <a name="catalog-views-for-statistics"></a>Widoki wykazu dla statystyk

Widoki te systemu zawierają informacje o statystyki:

| Widok katalogu | Opis |
|:--- |:--- |
| [sys.columns](/sql/relational-databases/system-catalog-views/sys-columns-transact-sql) |Jeden wiersz dla każdej kolumny. |
| [sys.objects](/sql/relational-databases/system-catalog-views/sys-objects-transact-sql) |Jeden wiersz dla każdego obiektu w bazie danych. |
| [sys.schemas](/sql/relational-databases/system-catalog-views/sys-objects-transact-sql) |Jeden wiersz dla każdego schematu w bazie danych. |
| [sys.stats](/sql/relational-databases/system-catalog-views/sys-stats-transact-sql) |Jeden wiersz dla każdego obiektu statystyk. |
| [sys.stats_columns](/sql/relational-databases/system-catalog-views/sys-stats-columns-transact-sql) |Jeden wiersz dla każdej kolumny w obiekcie statystyk. Zawiera łącza do sys.columns. |
| [sys.tables](/sql/relational-databases/system-catalog-views/sys-tables-transact-sql) |Jeden wiersz dla każdej tabeli (z uwzględnieniem tabel zewnętrznych). |
| [sys.table_types](/sql/relational-databases/system-catalog-views/sys-table-types-transact-sql) |Jeden wiersz dla każdego typu danych. |

### <a name="system-functions-for-statistics"></a>Funkcje systemu dla statystyk

Te funkcje systemu są przydatne do pracy ze statystykami:

| Funkcja systemowa | Opis |
|:--- |:--- |
| [STATS_DATE](/sql/t-sql/functions/stats-date-transact-sql) |Data ostatniej aktualizacji obiektu statystyk. |
| [DBCC SHOW_STATISTICS](/sql/t-sql/database-console-commands/dbcc-show-statistics-transact-sql) |Podsumowanie poziomu i szczegółowe informacje o rozkładu wartości rozumieniu obiektu statystyk. |

### <a name="combine-statistics-columns-and-functions-into-one-view"></a>Łączenie statystyki kolumn i funkcje w jednym widoku

Ten widok udostępnia kolumn, które odnoszą się do statystyk i wynikiem funkcji STATS_DATE() ze sobą.

```sql
CREATE VIEW dbo.vstats_columns
AS
SELECT
        sm.[name]                           AS [schema_name]
,       tb.[name]                           AS [table_name]
,       st.[name]                           AS [stats_name]
,       st.[filter_definition]              AS [stats_filter_definition]
,       st.[has_filter]                     AS [stats_is_filtered]
,       STATS_DATE(st.[object_id],st.[stats_id])
                                            AS [stats_last_updated_date]
,       co.[name]                           AS [stats_column_name]
,       ty.[name]                           AS [column_type]
,       co.[max_length]                     AS [column_max_length]
,       co.[precision]                      AS [column_precision]
,       co.[scale]                          AS [column_scale]
,       co.[is_nullable]                    AS [column_is_nullable]
,       co.[collation_name]                 AS [column_collation_name]
,       QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])
                                            AS two_part_name
,       QUOTENAME(DB_NAME())+'.'+QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])
                                            AS three_part_name
FROM    sys.objects                         AS ob
JOIN    sys.stats           AS st ON    ob.[object_id]      = st.[object_id]
JOIN    sys.stats_columns   AS sc ON    st.[stats_id]       = sc.[stats_id]
                            AND         st.[object_id]      = sc.[object_id]
JOIN    sys.columns         AS co ON    sc.[column_id]      = co.[column_id]
                            AND         sc.[object_id]      = co.[object_id]
JOIN    sys.types           AS ty ON    co.[user_type_id]   = ty.[user_type_id]
JOIN    sys.tables          AS tb ON  co.[object_id]        = tb.[object_id]
JOIN    sys.schemas         AS sm ON  tb.[schema_id]        = sm.[schema_id]
WHERE   1=1
AND     st.[user_created] = 1
;
```

## <a name="dbcc-showstatistics-examples"></a>Przykłady DBCC SHOW_STATISTICS()

DBCC SHOW_STATISTICS() pokazuje dane przechowywane w obiekcie statystyk. Te dane składa się z trzech części:

- Nagłówek
- Wektor gęstość
- Histogram

Nagłówek metadanych dotyczących statystyk. Histogram przedstawia rozkład wartości w pierwszej kolumnie klucza obiektu statystyk. Wektor gęstość środki korelacji między kolumną. Usługa SQL Data Warehouse oblicza szacowania kardynalności o jakichkolwiek danych w obiekcie statystyk.

### <a name="show-header-density-and-histogram"></a>Pokaż nagłówek, gęstość i histogramu

Ten prosty przykład przedstawia trzy części obiektu statystyki:

```sql
DBCC SHOW_STATISTICS([<schema_name>.<table_name>],<stats_name>)
```

Na przykład:

```sql
DBCC SHOW_STATISTICS (dbo.table1, stats_col1);
```

### <a name="show-one-or-more-parts-of-dbcc-showstatistics"></a>Pokaż co najmniej jeden części DBCC SHOW_STATISTICS()

Jeśli interesuje Cię tylko wyświetlanie określonych części, użyj `WITH` klauzuli i określić elementy, które mają być wyświetlane:

```sql
DBCC SHOW_STATISTICS([<schema_name>.<table_name>],<stats_name>) WITH stat_header, histogram, density_vector
```

Na przykład:

```sql
DBCC SHOW_STATISTICS (dbo.table1, stats_col1) WITH histogram, density_vector
```

## <a name="dbcc-showstatistics-differences"></a>Polecenie DBCC SHOW_STATISTICS() różnice

Polecenie DBCC SHOW_STATISTICS() bardziej restrykcyjnie jest implementowane w usłudze SQL Data Warehouse w porównaniu do programu SQL Server:

- Nieudokumentowany funkcje nie są obsługiwane.
- Nie można użyć Stats_stream.
- Nie można dołączyć wyniki dla konkretnych podzestawów danych statystyk. Na przykład STAT_HEADER JOIN DENSITY_VECTOR.
- Nie można ustawić NO_INFOMSGS do pomijania wiadomości.
- Nie można używać nazw statystyk nawiasy kwadratowe.
- Nie można użyć nazwy kolumn do identyfikowania obiektów statystyk.
- Błąd niestandardowy 2767 nie jest obsługiwana.

## <a name="next-steps"></a>Kolejne kroki

Dla dodatkowo poprawić wydajność zapytań, zobacz [monitorowanie obciążenia](sql-data-warehouse-manage-monitor.md)
