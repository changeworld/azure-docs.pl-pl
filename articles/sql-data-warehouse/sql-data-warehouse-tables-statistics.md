---
title: Tworzenie, aktualizowanie statystyk - Azure SQL Data Warehouse | Dokumenty Microsoft
description: Zalecenia i przykłady dotyczące tworzenia i zaktualizowanie statystyk optymalizacji kwerend w tabelach w usłudze Azure SQL Data Warehouse.
services: sql-data-warehouse
author: ckarst
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 05/09/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 2922a859f741c6b6420f49d34b982b7ec4968a8c
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
---
# <a name="creating-updating-statistics-on-tables-in-azure-sql-data-warehouse"></a>Tworzenie, aktualizowanie statystyk dotyczących tabel w magazynie danych SQL Azure
Zalecenia i przykłady dotyczące tworzenia i zaktualizowanie statystyk optymalizacji kwerend w tabelach w usłudze Azure SQL Data Warehouse.

## <a name="why-use-statistics"></a>Dlaczego warto używać statystyki?
Im bardziej zna danych Azure SQL Data Warehouse, tym szybciej jego wykonywanie zapytań względem jego. Zbieranie statystyk na podstawie danych i załadowanie go do usługi SQL Data Warehouse jest jednym z najważniejszych czynności, które pozwalają zoptymalizować zapytania. Jest to spowodowane Optymalizator zapytań SQL Data Warehouse jest Optymalizator opartych na kosztach. Porównuje koszt różne plany zapytań, a następnie wybiera plan o najniższej cenie, który znajduje się w większości przypadków plan, który wykonuje najszybsze. Na przykład jeśli Optymalizator szacuje się, że data filtrowania kwerendy zwróci jeden wiersz, ją wybrać inny plan niż w przypadku szacuje go, że wybrana data zwraca 1 milion wierszy.

## <a name="automatic-creation-of-statistics"></a>Automatyczne tworzenie statystyk
Podczas automatycznego tworzenia statystyk opcja jest włączona, AUTO_CREATE_STATISTICS, SQL Data Warehouse analizuje zapytania przychodzące użytkownika, których statystyki pojedynczej kolumny są tworzone dla kolumny, których brakuje statystyki. Optymalizator zapytań tworzy statystyki dla poszczególnych kolumn w warunek predykatu lub sprzężenia zapytania zwiększające szacowania kardynalności dla planu zapytania. Automatyczne tworzenie statystyk jest obecnie włączona domyślnie.

Możesz sprawdzić, czy magazynu danych ma to skonfigurować, uruchamiając następujące polecenie:

```sql
SELECT name, is_auto_create_stats_on 
FROM sys.databases
```
Jeśli magazyn danych nie ma AUTO_CREATE_STATISTICS skonfigurowane, zalecamy włączenie tej właściwości, uruchamiając następujące polecenie:

```sql
ALTER DATABASE <yourdatawarehousename> 
SET AUTO_CREATE_STATISTICS ON
```
Poniższe instrukcje wyzwoli automatyczne tworzenie statystyk: wykrycia obecności predykat lub wybierz INSERT SELECT, CTAS, UPDATE, DELETE i WYJAŚNIJ po zawierających sprzężenia. 

> [!NOTE]
> Automatyczne tworzenie statystyk nie są tworzone w tabelach tymczasowych lub zewnętrznego.
> 

Automatyczne tworzenie statystyk jest generowany synchronicznie, więc jeśli kolumn nie zostały utworzone dla nich statystyki może zostać obciążony nieznaczne zapytania obniżeniem wydajności. Tworzenie statystyk może zająć kilka sekund na jednej kolumny w zależności od rozmiaru tabeli. Aby uniknąć pomiaru spadek wydajności, szczególnie w testowanie wydajności, powinien zapewnić, że statystyka zostały utworzone najpierw przed rozpoczęciem profilowania system, wykonując testu obciążenia.

> [!NOTE]
> Tworzenie statystyk, również są rejestrowane w [sys.dm_pdw_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?view=aps-pdw-2016) w kontekście innego użytkownika.
> 

Po utworzeniu automatycznego statystyki one będzie mieć postać: _WA_Sys_< identyfikator kolumny 8 cyfr szesnastkowo > _ < identyfikator tabeli 8 cyfr szesnastkowo >. Można wyświetlić statystyki, które zostały już utworzone, uruchamiając następujące polecenie:

```sql
DBCC SHOW_STATISTICS (<tablename>, <targetname>)
```

## <a name="updating-statistics"></a>Zaktualizowanie statystyk

Jeden najlepszym rozwiązaniem jest aktualizację statystyk dotyczących kolumn dat każdego dnia, gdy zostaną dodane nowe daty. Każdy czas nowe wiersze są załadowane do magazynu danych, nowe obciążenia daty lub daty transakcji zostaną dodane. Te zmiany dystrybucji danych i utworzyć statystyki nieaktualny. Z drugiej strony statystyki dotyczące kraju kolumny w tabeli klienta może nigdy nie muszą zostać zaktualizowane, ponieważ rozkład wartości zwykle nie ulega zmianie. Zakładając, że dystrybucja jest stałe między klientami, dodawanie nowych wierszy do zmiany tabeli nie jest będzie zmienić dystrybucji danych. Jednak jeśli magazyn danych zawiera tylko jeden kraj, należy przenieść dane z nowego kraju dane z różnych krajach przechowywane, następnie należy aktualizować statystyki w kolumnie kraju.

Poniżej przedstawiono zalecenia dotyczące zaktualizowanie statystyk:

| **Częstotliwość aktualizacji Statystyka** | Zachowawcze: codziennie <br></br> Po ładowania i przekształcania danych || **Próbkowania** |  Mniej niż 1 miliard wierszy, użyj domyślnej próbkowania (% 20) <br></br> Z więcej niż 1 miliard wierszy, statystyki dotyczące 2-procentowy zakres jest dobrym |

Jedno z pierwszym pytań podczas rozwiązywaniu zapytania jest, **"Są statystyki aktualne?"**

To pytanie nie jest taki, który należy odpowiedzieć przy wiek danych. Obiekt aktualne statystyki może być stare, jeśli nie ma w niej żadnych istotnych zmian w danych źródłowych. Liczba wierszy zmienił znacząco lub istotne zmiany w dystrybucji wartości kolumny, *następnie* nadszedł czas, aby zaktualizować statystyk.

Ponieważ nie ma widoku dynamicznego zarządzania do określenia, czy dane w tabeli zmienił się od czasu ostatniego statystyk czasu zostały zaktualizowane, wiedząc, wieku statystyk może umożliwić z część obrazu.  Następujące zapytanie służy do określenia czasu ostatniego statystyk zostały zaktualizowane w każdej tabeli.  

> [!NOTE]
> Należy pamiętać, że w przypadku istotnych zmian w dystrybucji wartości kolumny, należy zaktualizować statystyk niezależnie od tego czasu, gdy są one zostały zaktualizowane.  
> 
> 

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

**Data kolumny** w magazynie danych, na przykład zwykle wymagają częstego aktualizacji statystyk. Każdy czas nowe wiersze są załadowane do magazynu danych, nowe obciążenia daty lub daty transakcji zostaną dodane. Te zmiany dystrybucji danych i utworzyć statystyki nieaktualny.  Z drugiej strony statystyk dotyczących płci kolumny w tabeli klienta nigdy nie może być konieczne do zaktualizowania. Zakładając, że dystrybucja jest stałe między klientami, dodawanie nowych wierszy do zmiany tabeli nie jest będzie zmienić dystrybucji danych. Jednak jeśli magazyn danych zawiera tylko jeden płci i nowe wyniki wymaganie w wielu płci, następnie należy aktualizować statystyki w kolumnie płci.

Aby uzyskać więcej informacji, zobacz ogólne wskazówki dotyczące [statystyki](/sql/relational-databases/statistics/statistics).

## <a name="implementing-statistics-management"></a>Implementowanie zarządzania statystyki
Często jest dobrym pomysłem jest rozszerzenie procesie ładowania danych, aby upewnić się, że statystyki są aktualizowane na końcu obciążenia. Ładowanie danych jest tabel najczęściej zmiany ich rozmiaru i/lub ich rozkład wartości. W związku z tym jest to logiczne miejsca do wykonania niektórych procesów zarządzania.

Dostępne są następujące wytyczne aktualizowania statystyk podczas procesu obciążenia:

* Sprawdź, czy każdy załadowanej tabeli ma co najmniej jeden obiekt statystyki aktualizacji. Spowoduje to zaktualizowanie informacje o rozmiarze (liczba wierszy i liczba stron) tabeli w ramach aktualizacji statystyk.
* Skoncentruj się na kolumn uczestniczących w klauzuli sprzężenia, GROUP BY, ORDER BY i DISTINCT.
* Należy rozważyć aktualizowanie kolumn "rosnącej klucza" takie jak transakcji daty częściej, ponieważ te wartości nie zostaną uwzględnione w postaci histogramu statystyk.
* Należy rozważyć aktualizowanie kolumn dystrybucji statycznych często.
* Należy pamiętać, że każdy obiekt Statystyka jest aktualizowany w sekwencji. Po prostu implementacja `UPDATE STATISTICS <TABLE_NAME>` nie zawsze jest idealnym rozwiązaniem, zwłaszcza szerokie tabele z dużą liczbą obiektów statystyk.

Aby uzyskać więcej informacji, zobacz [szacowania kardynalności](/sql/relational-databases/performance/cardinality-estimation-sql-server).

## <a name="examples-create-statistics"></a>Przykłady: Tworzenie statystyk
Poniższe przykłady pokazują, jak tworzenie statystyk na użytek różnych opcji. Opcje używane dla każdej kolumny są zależne od właściwości danych i jak kolumna będzie używane w zapytaniach.

### <a name="create-single-column-statistics-with-default-options"></a>Tworzenie statystyk pojedynczej kolumny z opcji domyślnych
Aby utworzyć statystyki dla kolumny, wystarczy podać nazwę obiektu statystyk i nazwę kolumny.

Ta składnia wykorzystuje wszystkie domyślne opcje. Domyślnie program SQL Data Warehouse przykłady **20 procent** podczas tworzenia statystyk tabeli.

```sql
CREATE STATISTICS [statistics_name] ON [schema_name].[table_name]([column_name]);
```

Na przykład:

```sql
CREATE STATISTICS col1_stats ON dbo.table1 (col1);
```

### <a name="create-single-column-statistics-by-examining-every-row"></a>Tworzenie statystyk pojedynczej kolumny, sprawdzając każdego wiersza
Domyślna częstotliwość próbkowania równy 20% jest wystarczające w większości sytuacji. Można jednak dostosować częstotliwość próbkowania.

Do próbkowania pełne tabeli, należy użyć następującej składni:

```sql
CREATE STATISTICS [statistics_name] ON [schema_name].[table_name]([column_name]) WITH FULLSCAN;
```

Na przykład:

```sql
CREATE STATISTICS col1_stats ON dbo.table1 (col1) WITH FULLSCAN;
```

### <a name="create-single-column-statistics-by-specifying-the-sample-size"></a>Tworzenie statystyk pojedynczej kolumny, określając rozmiar próbki
Alternatywnie można określić rozmiar próbki w procentach:

```sql
CREATE STATISTICS col1_stats ON dbo.table1 (col1) WITH SAMPLE = 50 PERCENT;
```

### <a name="create-single-column-statistics-on-only-some-of-the-rows"></a>Tworzenie statystyk pojedynczej kolumny dla niektórych wierszy
Można też utworzyć statystyki w części wiersze w tabeli. Jest to filtrowane statystyki.

Na przykład można użyć statystykę filtrowaną, planując zapytania określonej partycji tabeli partycjonowanej duże. Tworzenie statystyk na tylko wartości partycji, prawidłowość statystyki będzie poprawić, a w związku z tym poprawiać wydajność zapytań.

Ten przykład tworzy statystyki zakresu wartości. Łatwo można zdefiniować wartości odpowiadające zakres wartości w partycji.

```sql
CREATE STATISTICS stats_col1 ON table1(col1) WHERE col1 > '2000101' AND col1 < '20001231';
```

> [!NOTE]
> Optymalizator zapytań należy rozważyć użycie statystykę filtrowaną, gdy wybiera plan zapytania rozproszonego zapytanie musi mieścić się w definicji obiektu statystyk. W poprzednim przykładzie, kwerendy gdzie należy określić wartości col1 między 2000101 i 20001231 klauzuli.
> 
> 

### <a name="create-single-column-statistics-with-all-the-options"></a>Tworzenie statystyk pojedynczej kolumny przy użyciu opcji
Opcje można także połączyć ze sobą. Poniższy przykład tworzy obiekt statystykę filtrowaną z niestandardowych próbkowania:

```sql
CREATE STATISTICS stats_col1 ON table1 (col1) WHERE col1 > '2000101' AND col1 < '20001231' WITH SAMPLE = 50 PERCENT;
```

Aby uzyskać pełną dokumentację, zobacz [instrukcji CREATE STATISTICS](/sql/t-sql/statements/create-statistics-transact-sql).

### <a name="create-multi-column-statistics"></a>Tworzenie statystyk wielokolumnowego
Utwórz obiekt wielokolumnowego statystyk, po prostu użyć poprzednich przykładach, lecz określ większą liczbę kolumn.

> [!NOTE]
> Histogram, służąca do oszacować liczbę wierszy w wyniku zapytania, jest dostępna tylko dla pierwszej kolumny na liście definicji obiektu statystyk.
> 
> 

W tym przykładzie histogram znajduje się na *produktu\_kategorii*. Statystyki między kolumny są obliczane na *produktu\_kategorii* i *produktu\_sub_category*:

```sql
CREATE STATISTICS stats_2cols ON table1 (product_category, product_sub_category) WHERE product_category > '2000101' AND product_category < '20001231' WITH SAMPLE = 50 PERCENT;
```

Ponieważ korelacji między *produktu\_kategorii* i *produktu\_sub\_kategorii*, obiekt statystyki wielokolumnowego może być przydatne, jeśli te kolumny są dostępne w tym samym czasie.

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
Magazyn danych SQL nie ma systemowej procedury składowanej odpowiednikiem sp_create_stats w programie SQL Server. Tę procedurę składowaną tworzy obiekt statystyki jednej kolumny w każdej kolumnie bazy danych, który nie ma jeszcze statystyk.

Poniższy przykład ułatwiają rozpoczęcie pracy z projektu bazy danych. Możesz dostosować go do własnych potrzeb:

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

Aby utworzyć statystyki dla wszystkich kolumn w tabeli, używając wartości domyślne, po prostu wywołania tej procedury.

```sql
EXEC [dbo].[prc_sqldw_create_stats] 1, NULL;
```
Aby utworzyć statystyki dla wszystkich kolumn w tabeli, używając fullscan, wywołania tej procedury:

```sql
EXEC [dbo].[prc_sqldw_create_stats] 2, NULL;
```
Aby utworzyć próbkowanych statystyki dla wszystkich kolumn w tabeli, wprowadź 3 i procent próbki.  Częstotliwość próbkowania 20 procent korzysta z tej procedury.

```sql
EXEC [dbo].[prc_sqldw_create_stats] 3, 20;
```


Aby utworzyć próbkowany statystyki dla wszystkich kolumn 

## <a name="examples-update-statistics"></a>Przykłady: Aktualizuj statystyki
Aby zaktualizować statystyk, można:

- Zaktualizuj jeden obiekt statystyk. Określ nazwę obiektu statystyk, które chcesz zaktualizować.
- Zaktualizuj wszystkie obiekty statystyki dla tabeli. Określ nazwę tabeli zamiast jeden obiekt poszczególnych statystyk.

### <a name="update-one-specific-statistics-object"></a>Zaktualizuj jeden obiekt poszczególnych statystyk
Aby zaktualizować obiekt poszczególnych statystyk, należy użyć następującej składni:

```sql
UPDATE STATISTICS [schema_name].[table_name]([stat_name]);
```

Na przykład:

```sql
UPDATE STATISTICS [dbo].[table1] ([stats_col1]);
```

Aktualizacja poszczególnych statystyk obiektów, można zminimalizować czas i zasoby wymagane do zarządzania statystyk. Wymaga to niektóre traktować wybrać najlepsze obiekty statystyki do aktualizacji.

### <a name="update-all-statistics-on-a-table"></a>Aktualizuj wszystkie statystyki dla tabeli
Oznacza to prosta metoda aktualizowania wszystkich obiektów statystyki dla tabeli:

```sql
UPDATE STATISTICS [schema_name].[table_name];
```

Na przykład:

```sql
UPDATE STATISTICS dbo.table1;
```

Ta instrukcja jest łatwy w użyciu. Należy jednak pamiętać, że aktualizacje *wszystkie* statystyk dotyczących tabeli i dlatego może wykonywać więcej pracy, niż jest to konieczne. Jeśli wydajność nie stanowi to problemu, to najprostszy i najbardziej kompleksowe sposobem zagwarantowania, że statystyki są aktualne.

> [!NOTE]
> Podczas aktualizowania wszystkich statystyk dotyczących tabeli, SQL Data Warehouse nie skanowanie w celu przykładowej tabeli dla każdego obiektu statystyk. Jeśli tabela jest duża i ma wiele kolumn i ilość danych statystycznych, może być bardziej wydajne, można zaktualizować poszczególnych statystyki, w zależności od potrzeb.
> 
> 

Dla implementacji `UPDATE STATISTICS` procedury, zobacz [tabel tymczasowych](sql-data-warehouse-tables-temporary.md). Metoda wdrażania jest nieco inne niż w poprzednim `CREATE STATISTICS` procedura, ale wynik jest taka sama.

Dla pełnej składni, zobacz [Update Statistics](/sql/t-sql/statements/update-statistics-transact-sql).

## <a name="statistics-metadata"></a>Statystyki metadanych
Istnieje kilka widoków systemowych i funkcje, które umożliwia znalezienie informacji na temat statystyk. Na przykład widać, jeśli obiekt statystyki może być nieaktualna za pomocą funkcji daty Statystyka wyświetlać podczas statystyki ostatnio zostały utworzone lub zaktualizowane.

### <a name="catalog-views-for-statistics"></a>Widoków wykazu statystyk
Widoki te systemu zawierają informacje o statystyki:

| Przeglądanie katalogu | Opis |
|:--- |:--- |
| [sys.columns](/sql/relational-databases/system-catalog-views/sys-columns-transact-sql) |Jeden wiersz dla każdej kolumny. |
| [sys.objects](/sql/relational-databases/system-catalog-views/sys-objects-transact-sql) |Jeden wiersz dla każdego obiektu w bazie danych. |
| [sys.schemas](/sql/relational-databases/system-catalog-views/sys-objects-transact-sql) |Jeden wiersz dla każdego schematu w bazie danych. |
| [sys.stats](/sql/relational-databases/system-catalog-views/sys-stats-transact-sql) |Jeden wiersz dla każdego obiektu statystyk. |
| [sys.stats_columns](/sql/relational-databases/system-catalog-views/sys-stats-columns-transact-sql) |Jeden wiersz dla każdej kolumny w obiekcie statystyk. Łącza do sys.columns. |
| [sys.tables](/sql/relational-databases/system-catalog-views/sys-tables-transact-sql) |Jeden wiersz dla każdej tabeli (w tym tabel zewnętrznych). |
| [sys.table_types](/sql/relational-databases/system-catalog-views/sys-table-types-transact-sql) |Jeden wiersz dla każdego typu danych. |

### <a name="system-functions-for-statistics"></a>Funkcje systemu statystyk
Funkcje systemu są przydatne w przypadku pracy z statystyki:

| System — funkcja | Opis |
|:--- |:--- |
| [STATS_DATE](/sql/t-sql/functions/stats-date-transact-sql) |Data ostatniej aktualizacji obiektu statystyk. |
| [DBCC SHOW_STATISTICS](/sql/t-sql/database-console-commands/dbcc-show-statistics-transact-sql) |Podsumowanie poziomu i szczegółowe informacje o rozkład wartości rozumieniu obiektu statystyk. |

### <a name="combine-statistics-columns-and-functions-into-one-view"></a>Łączenie statystyk kolumny i funkcji w jednym widoku
Ten widok wywołuje kolumn, które dotyczą statystyk i wyniki z funkcji STATS_DATE() ze sobą.

```sql
CREATE VIEW dbo.vstats_columns
AS
SELECT
        sm.[name]                           AS [schema_name]
,       tb.[name]                           AS [table_name]
,       st.[name]                           AS [stats_name]
,       st.[filter_definition]              AS [stats_filter_defiinition]
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
Polecenie DBCC SHOW_STATISTICS() zawiera dane przechowywane w obiekcie statystyk. Te dane składa się z trzech części:

- Nagłówek
- Wektor gęstość
- Histogram

Metadane nagłówka o statystyki. Histogram przedstawia rozkład wartości z pierwszej kolumny klucza obiektu statystyk. Wektor gęstość mierzy korelacji między kolumny. Usługa SQL Data Warehouse oblicza szacowania kardynalności o dane w obiekcie statystyk.

### <a name="show-header-density-and-histogram"></a>Pokaż nagłówka, gęstości i histogramu
Ten prosty przykład przedstawia trzy części obiektu statystyki:

```sql
DBCC SHOW_STATISTICS([<schema_name>.<table_name>],<stats_name>)
```

Na przykład:

```sql
DBCC SHOW_STATISTICS (dbo.table1, stats_col1);
```

### <a name="show-one-or-more-parts-of-dbcc-showstatistics"></a>Pokaż co najmniej jeden części DBCC SHOW_STATISTICS()
Jeśli interesuje Cię tylko wyświetlanie określonych części, użyj `WITH` klauzuli i określ części, które chcesz wyświetlić:

```sql
DBCC SHOW_STATISTICS([<schema_name>.<table_name>],<stats_name>) WITH stat_header, histogram, density_vector
```

Na przykład:

```sql
DBCC SHOW_STATISTICS (dbo.table1, stats_col1) WITH histogram, density_vector
```

## <a name="dbcc-showstatistics-differences"></a>Polecenie DBCC SHOW_STATISTICS() różnic
DBCC SHOW_STATISTICS() bardziej ściśle jest zaimplementowana w usłudze SQL Data Warehouse w porównaniu z programem SQL Server:

- Nieudokumentowanej funkcje nie są obsługiwane.
- Nie można użyć Stats_stream.
- Nie można dołączyć wyniki konkretne podzestawy danych statystyk. Na przykład (STAT_HEADER JOIN DENSITY_VECTOR).
- Nie można ustawić NO_INFOMSGS dla pomijania wiadomości.
- Nie można użyć nazwy statystyki nawiasy kwadratowe.
- Nie można użyć nazwy kolumn, aby zidentyfikować obiekty statystyki.
- Błąd niestandardowy 2767 nie jest obsługiwane.

## <a name="next-steps"></a>Kolejne kroki
Dla dalszego zwiększenia wydajności zapytań, zobacz [monitorować obciążenie](sql-data-warehouse-manage-monitor.md)

