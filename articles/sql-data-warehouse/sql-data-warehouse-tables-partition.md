---
title: Partycjonowanie tabel w usłudze Azure SQL Data Warehouse | Dokumentacja firmy Microsoft
description: Zalecenia i przykłady dotyczące używania partycji tabeli w magazynie danych SQL Azure.
services: sql-data-warehouse
author: ronortloff
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: ada55950ee36222e70809e2ef423c63612cd61ed
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2018
---
# <a name="partitioning-tables-in-sql-data-warehouse"></a>Partycjonowanie tabel w usłudze SQL Data Warehouse
Zalecenia i przykłady dotyczące używania partycji tabeli w magazynie danych SQL Azure.

## <a name="what-are-table-partitions"></a>Co to są partycje tabeli?
Partycje tabeli służą do dzielenia danych na mniejszym grupom danych. W większości przypadków partycje tabeli są tworzone według kolumny daty. Partycjonowanie jest obsługiwana dla wszystkich typów tabeli SQL Data Warehouse; w tym klastrowanego magazynu kolumn, indeks klastrowany i stosu. Partycjonowanie jest również obsługiwany na wszystkich typach dystrybucji, w tym zarówno skrótu lub działanie okrężne rozproszonych.  

Partycjonowanie mogą korzystać wydajność obsługi i zapytań danych. Określa, czy przynosi korzyści zarówno lub jeden z nich jest zależna od jak załadować danych i czy tej samej kolumnie może służyć do obu celów, ponieważ Partycjonowanie jest możliwe tylko w jednej kolumnie.

### <a name="benefits-to-loads"></a>Zalety dla obciążeń
Główną zaletą partycjonowania w usłudze SQL Data Warehouse jest zwiększenie wydajności i wydajności podczas ładowania danych przy użyciu usunięcia partycji, przełączania i scalanie. W większości przypadków danych jest podzielona na partycje w kolumnie dat, która jest ściśle związany kolejność, w którym dane zostaną załadowane do bazy danych. Jedną z największych korzyści wynikające ze stosowania partycje do przechowywania danych o jej unikania rejestrowanie transakcji. Po prostu Wstawianie, aktualizowanie lub usuwanie danych mogą być Najprostszym rozwiązaniem z niewielką ilością myśl i nakładu pracy, przy użyciu podziału na partycje podczas procesu obciążenia znacznie może poprawić wydajność.

Przełączanie partycji umożliwia szybkie należy usunąć lub zamienić sekcji tabeli.  Na przykład tabela faktów sprzedaży może zawierać tylko dane z ostatnich miesięcy 36. Na koniec każdego miesiąca miesiąc najstarsze dane sprzedaży został usunięty z tabeli.  Te dane, można go usunąć za pomocą instrukcji delete umożliwia usunięcie danych najstarsze miesiąc. Jednak usunięcie dużych ilości danych wiersz po wierszu z instrukcji delete może trwać zbyt dużo czasu, a także spowodować ryzyko duże transakcje, które można wycofać zająć dużo czasu, jeśli jakaś nieprawidłowość. Więcej optymalne podejście jest Porzuć partycję najstarsze danych. Gdzie usuwanie poszczególnych wierszy może zająć godziny, usunięcie całego partycji może potrwać sekund.

### <a name="benefits-to-queries"></a>Korzyści dotyczące zapytań
Partycjonowanie można również poprawić wydajność zapytań. Zapytanie, które dotyczą filtr danych podzielonej na partycje można ograniczyć skanowanie w celu kwalifikacji partycji. Ta metoda filtrowania można uniknąć skanowania tabeli pełne i tylko skanowanie mniejszego podzestawu danych. Wraz z wprowadzeniem klastrowane indeksy magazynu kolumn zwiększenia wydajności eliminacji predykatu są mniej korzystne, ale w niektórych przypadkach może być korzyści zapytania. Na przykład jeśli tabela faktów sprzedaży jest podzielona na partycje do 36 miesięcy, korzystając z pola Data sprzedaży, a następnie wysyła zapytanie tego filtru na dacie sprzedaży można pominąć wyszukiwanie w partycji, które nie pasuje do filtru.

## <a name="sizing-partitions"></a>Ustawianie rozmiaru partycji
Podczas partycjonowania może służyć do zwiększenia wydajności w niektórych scenariuszach, tworzenia tabeli z **zbyt wiele** partycji może pogarszać wydajność w pewnych okolicznościach.  Te problemy są szczególnie istotne w przypadku tabel klastrowanego magazynu kolumn. Partycjonowania być pomocne, ważne jest zrozumienie, kiedy należy używać partycjonowania i liczby partycji można utworzyć. Nie twardych szybkiego zasady, jak wiele partycji są zbyt wiele, to zależy od danych i jak wiele partycji można jednocześnie ładowania. Pomyślne schemat partycjonowania ma zazwyczaj dziesiątki setki partycji, nie tysięcy.

Podczas tworzenia partycji na **klastrowanego magazynu kolumn** tabel, ważne jest, aby wziąć pod uwagę liczbę wierszy należą do każdej partycji. Optymalne kompresji i wydajności tabel klastrowanego magazynu kolumn co najmniej 1 milion wierszy na dystrybucji i partycja na potrzeby. Przed utworzeniem partycji usługi SQL Data Warehouse już dzieli każdej tabeli 60 rozproszonej bazy danych. Wszystkie partycje dodane do tabeli jest oprócz dystrybucje utworzone w tle. Jeśli tabela faktów sprzedaży zawiera 36 miesięczne partycji i biorąc pod uwagę, że usługa SQL Data Warehouse ma 60 dystrybucje, następnie tabeli faktów sprzedaży powinien zawierać 60 mln wierszy na miesiąc lub 2.1 miliardy wierszy po wszystkich miesięcy są wypełniane przy użyciu tego przykładu. Jeśli tabela zawiera mniej niż minimalna zalecana liczba wierszy przypadających na partycję, rozważ użycie mniejszej liczby partycji w celu zwiększenia liczby wierszy przypadających na partycję. Aby uzyskać więcej informacji, zobacz [indeksowanie](sql-data-warehouse-tables-index.md) artykułu, w tym zapytań, które można ocenić jakość klastra indeksy magazynu kolumn.

## <a name="syntax-differences-from-sql-server"></a>Różnice składni z programu SQL Server
Usługa SQL Data Warehouse wprowadza sposób definiowania partycji, która jest łatwiejsze niż w przypadku programu SQL Server. Partycjonowania funkcje i schematy nie są używane w usłudze SQL Data Warehouse, ponieważ są one w programie SQL Server. Zamiast tego jest wszystko, co należy zrobić, zidentyfikować kolumny podzielone na partycje i punkty granic. Składnia partycjonowania może być nieco inne niż SQL Server, podstawowe koncepcje są takie same. SQL Server i SQL Data Warehouse obsługuje jedna kolumna partycji w tabeli, które mogą być ranged partycji. Aby dowiedzieć się więcej na temat partycjonowania, zobacz [partycjonowane tabele i indeksy](/sql/relational-databases/partitions/partitioned-tables-and-indexes).

W poniższym przykładzie użyto [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse) instrukcji do partycjonowania tabeli FactInternetSales w kolumnie OrderDateKey:

```sql
CREATE TABLE [dbo].[FactInternetSales]
(
    [ProductKey]            int          NOT NULL
,   [OrderDateKey]          int          NOT NULL
,   [CustomerKey]           int          NOT NULL
,   [PromotionKey]          int          NOT NULL
,   [SalesOrderNumber]      nvarchar(20) NOT NULL
,   [OrderQuantity]         smallint     NOT NULL
,   [UnitPrice]             money        NOT NULL
,   [SalesAmount]           money        NOT NULL
)
WITH
(   CLUSTERED COLUMNSTORE INDEX
,   DISTRIBUTION = HASH([ProductKey])
,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                    (20000101,20010101,20020101
                    ,20030101,20040101,20050101
                    )
                )
)
;
```

## <a name="migrating-partitioning-from-sql-server"></a>Migrowanie partycjonowania z programu SQL Server
Aby przeprowadzić migrację definicje partycji programu SQL Server do usługi SQL Data Warehouse po prostu:

- Eliminowanie programu SQL Server [schemat partycji](/sql/t-sql/statements/create-partition-scheme-transact-sql).
- Dodaj [funkcja partycji](/sql/t-sql/statements/create-partition-function-transact-sql) definicji do tworzenia tabeli.

W przypadku migracji tabeli partycjonowanej z wystąpieniem programu SQL Server, SQL następujące może pomóc ustalić liczbę wierszy w każdej partycji. Należy pamiętać, że użycie takiej samej szczegółowości partycjonowania na SQL Data Warehouse liczba wierszy przypadających na partycję zmniejsza się o 60.  

```sql
-- Partition information for a SQL Server Database
SELECT      s.[name]                        AS      [schema_name]
,           t.[name]                        AS      [table_name]
,           i.[name]                        AS      [index_name]
,           p.[partition_number]            AS      [partition_number]
,           SUM(a.[used_pages]*8.0)         AS      [partition_size_kb]
,           SUM(a.[used_pages]*8.0)/1024    AS      [partition_size_mb]
,           SUM(a.[used_pages]*8.0)/1048576 AS      [partition_size_gb]
,           p.[rows]                        AS      [partition_row_count]
,           rv.[value]                      AS      [partition_boundary_value]
,           p.[data_compression_desc]       AS      [partition_compression_desc]
FROM        sys.schemas s
JOIN        sys.tables t                    ON      t.[schema_id]         = s.[schema_id]
JOIN        sys.partitions p                ON      p.[object_id]         = t.[object_id]
JOIN        sys.allocation_units a          ON      a.[container_id]      = p.[partition_id]
JOIN        sys.indexes i                   ON      i.[object_id]         = p.[object_id]
                                            AND     i.[index_id]          = p.[index_id]
JOIN        sys.data_spaces ds              ON      ds.[data_space_id]    = i.[data_space_id]
LEFT JOIN   sys.partition_schemes ps        ON      ps.[data_space_id]    = ds.[data_space_id]
LEFT JOIN   sys.partition_functions pf      ON      pf.[function_id]      = ps.[function_id]
LEFT JOIN   sys.partition_range_values rv   ON      rv.[function_id]      = pf.[function_id]
                                            AND     rv.[boundary_id]      = p.[partition_number]
WHERE       p.[index_id] <=1
GROUP BY    s.[name]
,           t.[name]
,           i.[name]
,           p.[partition_number]
,           p.[rows]
,           rv.[value]
,           p.[data_compression_desc]
;
```

## <a name="workload-management"></a>Zarządzanie obciążeniami
Pierwsza kwestia końcowego składników można zdecydować, czy tabela partycji jest [zarządzania obciążenia](resource-classes-for-workload-management.md). Zarządzanie obciążenia w usłudze SQL Data Warehouse jest głównie zarządzania pamięci i współbieżności. W usłudze SQL Data Warehouse Maksymalna pamięć przydzielona do każdej dystrybucji podczas wykonywania zapytania podlega klasy zasobów. W idealnym przypadku partycji są o rozmiarze, biorąc pod uwagę innych czynników, takich jak wymagania dotyczące pamięci tworzenia klastrowane indeksy magazynu kolumn. Klastrowane korzyści indeksy magazynu kolumn znacznie, gdy są przydzielone więcej pamięci. W związku z tym chcesz upewnić się, że odbudowywanie indeksu partycji nie jest zagłodzone pamięci. Zwiększenie ilości pamięci do zapytania można osiągnąć przełączyć domyślna rola smallrc, do jednego z innych ról, takich jak largerc.

Informacje dotyczące alokacji pamięci dla dystrybucji są dostępne, badając zarządcy zasobów dynamicznych widoków zarządzania. W rzeczywistości Twojej przydział pamięci jest mniejsza niż wyniki następującej kwerendy. Jednak to zapytanie zawiera poziom wskazówki, które można używać podczas określania rozmiaru partycji dla danych operacji zarządzania. Należy unikać zmiany rozmiaru partycji poza przydziału pamięci o rozmiarze udostępnianym przez klasę bardzo dużych zasobów. Jeśli partycji rosnąć poza tym rysunku, istnieje ryzyko wykorzystania pamięci, co z kolei prowadzi do mniej optymalnej kompresji.

```sql
SELECT  rp.[name]                                AS [pool_name]
,       rp.[max_memory_kb]                        AS [max_memory_kb]
,       rp.[max_memory_kb]/1024                    AS [max_memory_mb]
,       rp.[max_memory_kb]/1048576                AS [mex_memory_gb]
,       rp.[max_memory_percent]                    AS [max_memory_percent]
,       wg.[name]                                AS [group_name]
,       wg.[importance]                            AS [group_importance]
,       wg.[request_max_memory_grant_percent]    AS [request_max_memory_grant_percent]
FROM    sys.dm_pdw_nodes_resource_governor_workload_groups    wg
JOIN    sys.dm_pdw_nodes_resource_governor_resource_pools    rp ON wg.[pool_id] = rp.[pool_id]
WHERE   wg.[name] like 'SloDWGroup%'
AND     rp.[name]    = 'SloDWPool'
;
```

## <a name="partition-switching"></a>Przełączanie partycji
Magazyn danych SQL obsługuje partycji dzielenia, łączenia i przełączania. Każda z tych funkcji jest wykonywana przy użyciu [instrukcji ALTER TABLE](/sql/t-sql/statements/alter-table-transact-sql) instrukcji.

Aby przełączyć partycji między dwiema tabelami, pamiętaj, że partycje są wyrównane na ich odpowiednich granice a definicje tabel są takie same. Jak sprawdzić, czy ograniczenia nie są dostępne do wymuszania zakres wartości w tabeli, tabeli źródłowej musi zawierać tej samej granice partycji w tabeli docelowej. Jeżeli granice partycji nie są następnie tego samego przełącznika partycji wystąpi błąd, ponieważ metadane partycji nie będą synchronizowane.

### <a name="how-to-split-a-partition-that-contains-data"></a>Sposób podziału partycji, która zawiera dane
Najbardziej efektywny sposób podział partycji, która zawiera już dane, jest użycie `CTAS` instrukcji. Tabeli partycjonowanej w przypadku klastrowanego magazynu kolumn, następnie partycji tabeli musi być pusta zanim może zostać podzielony.

Poniższy przykład tworzy tabeli partycjonowanej magazynu kolumn. Wstawia jeden wiersz na każdej partycji:

```sql
CREATE TABLE [dbo].[FactInternetSales]
(
        [ProductKey]            int          NOT NULL
    ,   [OrderDateKey]          int          NOT NULL
    ,   [CustomerKey]           int          NOT NULL
    ,   [PromotionKey]          int          NOT NULL
    ,   [SalesOrderNumber]      nvarchar(20) NOT NULL
    ,   [OrderQuantity]         smallint     NOT NULL
    ,   [UnitPrice]             money        NOT NULL
    ,   [SalesAmount]           money        NOT NULL
)
WITH
(   CLUSTERED COLUMNSTORE INDEX
,   DISTRIBUTION = HASH([ProductKey])
,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                    (20000101
                    )
                )
)
;

INSERT INTO dbo.FactInternetSales
VALUES (1,19990101,1,1,1,1,1,1);
INSERT INTO dbo.FactInternetSales
VALUES (1,20000101,1,1,1,1,1,1);


CREATE STATISTICS Stat_dbo_FactInternetSales_OrderDateKey ON dbo.FactInternetSales(OrderDateKey);
```

> [!NOTE]
> Tworząc obiektu Statystyka metadanych tabeli jest bardziej dokładne. W przypadku pominięcia statystyk, SQL Data Warehouse użyje wartości domyślnych. Aby uzyskać więcej informacji dotyczących statystyk, zapoznaj się z tematem [statystyki](sql-data-warehouse-tables-statistics.md).
> 
> 

Następujące zapytanie znajduje się liczba wierszy przy użyciu `sys.partitions` widoku katalogu:

```sql
SELECT  QUOTENAME(s.[name])+'.'+QUOTENAME(t.[name]) as Table_name
,       i.[name] as Index_name
,       p.partition_number as Partition_nmbr
,       p.[rows] as Row_count
,       p.[data_compression_desc] as Data_Compression_desc
FROM    sys.partitions p
JOIN    sys.tables     t    ON    p.[object_id]   = t.[object_id]
JOIN    sys.schemas    s    ON    t.[schema_id]   = s.[schema_id]
JOIN    sys.indexes    i    ON    p.[object_id]   = i.[object_Id]
                            AND   p.[index_Id]    = i.[index_Id]
WHERE t.[name] = 'FactInternetSales'
;
```

Następujące polecenie podziału odbiera komunikat o błędzie:

```sql
ALTER TABLE FactInternetSales SPLIT RANGE (20010101);
```

Msg 35346, 15 poziom, stan 1, wiersz 44 PODZIELONY klauzula instrukcji ALTER PARTITION nie powiodło się, ponieważ partycja nie jest pusty. Można dzielić tylko puste partycje w gdy w tabeli istnieje indeks magazynu kolumn. Rozważ wyłączenie indeksu magazynu kolumn przed wykonaniem instrukcji ALTER PARTITION, a następnie odbudowanie indeksu magazynu kolumn po ukończeniu operacji ALTER PARTITION.

Można jednak użyć `CTAS` Aby utworzyć nową tabelę do przechowywania danych.

```sql
CREATE TABLE dbo.FactInternetSales_20000101
    WITH    (   DISTRIBUTION = HASH(ProductKey)
            ,   CLUSTERED COLUMNSTORE INDEX
            ,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                                (20000101
                                )
                            )
            )
AS
SELECT *
FROM    FactInternetSales
WHERE   1=2
;
```

Jako granice partycji są wyrównane, przełącznik jest dozwolone. Spowoduje to pozostawienie tabeli źródłowej z pustą partycję, które następnie można podzielić.

```sql
ALTER TABLE FactInternetSales SWITCH PARTITION 2 TO  FactInternetSales_20000101 PARTITION 2;

ALTER TABLE FactInternetSales SPLIT RANGE (20010101);
```

Pozostało jest, aby były wyrównane dane do nowych granic partycji przy użyciu `CTAS`, a następnie przejdź danych z powrotem do tabeli głównej.

```sql
CREATE TABLE [dbo].[FactInternetSales_20000101_20010101]
    WITH    (   DISTRIBUTION = HASH([ProductKey])
            ,   CLUSTERED COLUMNSTORE INDEX
            ,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                                (20000101,20010101
                                )
                            )
            )
AS
SELECT  *
FROM    [dbo].[FactInternetSales_20000101]
WHERE   [OrderDateKey] >= 20000101
AND     [OrderDateKey] <  20010101
;

ALTER TABLE dbo.FactInternetSales_20000101_20010101 SWITCH PARTITION 2 TO dbo.FactInternetSales PARTITION 2;
```

Po zakończeniu przepływu danych jest dobrym pomysłem jest Odśwież statystyk dotyczących tabeli docelowej. Zaktualizowanie statystyk gwarantuje, że dane statystyczne odzwierciedlają dokładnie dystrybucji nowe dane w ich odpowiednich partycji.

```sql
UPDATE STATISTICS [dbo].[FactInternetSales];
```

### <a name="table-partitioning-source-control"></a>Tabela partycjonowania kontroli źródła
Aby uniknąć z definicji tabeli z **uszkodzona** w systemie kontroli źródła, warto wziąć pod uwagę następujące podejście:

1. Tworzenie tabeli partycjonowanej tabeli, ale bez wartości partycji

    ```sql
    CREATE TABLE [dbo].[FactInternetSales]
    (
        [ProductKey]            int          NOT NULL
    ,   [OrderDateKey]          int          NOT NULL
    ,   [CustomerKey]           int          NOT NULL
    ,   [PromotionKey]          int          NOT NULL
    ,   [SalesOrderNumber]      nvarchar(20) NOT NULL
    ,   [OrderQuantity]         smallint     NOT NULL
    ,   [UnitPrice]             money        NOT NULL
    ,   [SalesAmount]           money        NOT NULL
    )
    WITH
    (   CLUSTERED COLUMNSTORE INDEX
    ,   DISTRIBUTION = HASH([ProductKey])
    ,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES () )
    )
    ;
    ```

1. `SPLIT` Tabela w ramach procesu wdrażania:

    ```sql
     -- Create a table containing the partition boundaries

    CREATE TABLE #partitions
    WITH
    (
        LOCATION = USER_DB
    ,   DISTRIBUTION = HASH(ptn_no)
    )
    AS
    SELECT  ptn_no
    ,       ROW_NUMBER() OVER (ORDER BY (ptn_no)) as seq_no
    FROM    (
        SELECT CAST(20000101 AS INT) ptn_no
        UNION ALL
        SELECT CAST(20010101 AS INT)
        UNION ALL
        SELECT CAST(20020101 AS INT)
        UNION ALL
        SELECT CAST(20030101 AS INT)
        UNION ALL
        SELECT CAST(20040101 AS INT)
    ) a
    ;

     -- Iterate over the partition boundaries and split the table

    DECLARE @c INT = (SELECT COUNT(*) FROM #partitions)
    ,       @i INT = 1                                 --iterator for while loop
    ,       @q NVARCHAR(4000)                          --query
    ,       @p NVARCHAR(20)     = N''                  --partition_number
    ,       @s NVARCHAR(128)    = N'dbo'               --schema
    ,       @t NVARCHAR(128)    = N'FactInternetSales' --table
    ;

    WHILE @i <= @c
    BEGIN
        SET @p = (SELECT ptn_no FROM #partitions WHERE seq_no = @i);
        SET @q = (SELECT N'ALTER TABLE '+@s+N'.'+@t+N' SPLIT RANGE ('+@p+N');');

        -- PRINT @q;
        EXECUTE sp_executesql @q;
        SET @i+=1;
    END

     -- Code clean-up

    DROP TABLE #partitions;
    ```

Z tej metody pozostaje statyczna kodu w kontroli źródła i partycjonowania wartości granicznych mogą być dynamiczny; ewolucji w magazynie wraz z upływem czasu.

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej informacji na temat tworzenia tabel, zobacz artykuły w [omówienie tabeli](sql-data-warehouse-tables-overview.md).

