---
title: Partycjonowanie tabel w usłudze Azure SQL Data Warehouse | Dokumentacja firmy Microsoft
description: Zalecenia i przykłady dotyczące używania partycji tabeli w usłudze Azure SQL Data Warehouse.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: implement
ms.date: 03/18/2019
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: d3557be2fd8fdb459571d2c792302963e17e4471
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60935884"
---
# <a name="partitioning-tables-in-sql-data-warehouse"></a>Partycjonowanie tabel w usłudze SQL Data Warehouse
Zalecenia i przykłady dotyczące używania partycji tabeli w usłudze Azure SQL Data Warehouse.

## <a name="what-are-table-partitions"></a>Co to są partycje tabeli?
Partycje tabel umożliwiają podzielić dane na mniejsze grupy danych. W większości przypadków partycji tabeli są tworzone na kolumnę daty. Partycjonowanie jest obsługiwane na wszystkich typach tabeli SQL Data Warehouse; w tym klastrowanego magazynu kolumn, indeks klastrowany i sterty. Partycjonowanie jest również obsługiwana dla wszystkich typów dystrybucji, w tym zarówno hash "lub" działanie okrężne rozproszonych.  

Partycjonowanie mogą zyskać danych konserwację i zwiększa wydajność zapytań. Czy korzyści zarówno lub jedna z tych metod jest zależna od jak dane są ładowane i czy tej samej kolumnie mogą być używane w celach zarówno od partycjonowania jest możliwe tylko w jednej kolumnie.

### <a name="benefits-to-loads"></a>Korzyści dla obciążeń
Podstawową zaletą partycjonowanie w usłudze SQL Data Warehouse jest poprawy efektywności i wydajności podczas ładowania danych przy użyciu usunięcie partycji, przełączanie i scalanie. W większości przypadków dane są partycjonowane na kolumnę daty, który jest ściśle powiązany kolejność, w którym dane są ładowane do bazy danych. Jedną z największych korzyści z używania partycji do przechowywania danych o jej unikania rejestrowanie transakcji. Po prostu wstawiania, aktualizowania lub usuwania danych mogą być Najprostszym rozwiązaniem, za pomocą niewielkiej myślenia i nakład pracy, za pomocą partycjonowania podczas procesu ładowania znacznie poprawić wydajność.

Przełączanie partycji można szybko usunąć lub zamienić część tabeli.  Na przykład tabela faktów sprzedaży może zawierać tylko dane dla ostatnich 36 miesięcy. Na koniec każdego miesiąca miesiąc najstarsze dane sprzedaży są usuwane z tabeli.  Te dane można usunąć za pomocą instrukcji delete umożliwia usunięcie danych najstarsze miesiąc. Jednak usunięcie dużą ilość danych wiersz po wierszu za pomocą instrukcji delete mogą zajęło zbyt dużo czasu, a także spowodować ryzyko duże transakcje, które można wycofać zająć dużo czasu, jeśli coś pójdzie nie tak. Jest bardziej optymalne podejście można usunąć najstarsze partycji danych. Gdzie usuwanie poszczególnych wierszy może potrwać godzin, usunięcie całego partycji może potrwać sekund.

### <a name="benefits-to-queries"></a>Korzyści dla zapytania
Partycjonowanie może również poprawić wydajność zapytań. Zapytanie, które odnosi się do filtru do danych podzielonych na partycje, można ograniczyć skanowanie, aby tylko uprawnionym partycje. Ta metoda filtrowania można uniknąć pełne skanowanie tabeli i skanować tylko mniejszy podzbiór danych. Wraz z wprowadzeniem klastrowane indeksy magazynu kolumn korzyści w zakresie wydajności eliminacji predykatu są mniej odsetek, ale w niektórych przypadkach może być to korzystne dla zapytania. Na przykład jeśli tabela faktów sprzedaży zostanie poddana partycjonowaniu na 36 miesięcy za pomocą pola daty sprzedaży, a następnie wysyła zapytanie z filtrem w dniu sprzedaży pominąć wyszukiwanie w partycji, które nie są zgodne z filtrem.

## <a name="sizing-partitions"></a>Partycje zmiany rozmiaru
Podczas partycjonowania może służyć do zwiększenia wydajności w niektórych scenariuszach, tworzenie tabeli przy użyciu **zbyt wiele** partycji może obniżyć wydajność w pewnych okolicznościach.  Te rozważania są szczególnie istotne w przypadku tabel klastrowanego magazynu kolumn. Partycjonowanie jako pomocny, ważne jest zrozumienie, kiedy należy używać partycji i liczby partycji, aby utworzyć. Nie ma twardych szybkie reguły, jak wiele partycji są zbyt wiele, to zależy od danych i jak wiele partycji można ładowania jednocześnie. Pomyślne schemat partycjonowania ma zwykle dziesiątki, setki partycji, nie tysiące.

Podczas tworzenia partycji na **klastrowanego magazynu kolumn** tabel, warto wziąć pod uwagę liczbę wierszy należą do każdej partycji. Optymalnej kompresji i wydajność tabel klastrowanego magazynu kolumn co najmniej 1 milion wierszy na dystrybucji i partycja na potrzeby. Przed utworzeniem partycji SQL Data Warehouse już dzieli każdej tabeli na 60 rozproszonych baz danych. Wszystkie partycje dodawane do tabeli jest oprócz dystrybucje tworzone w tle. Za pomocą tego przykładu, jeśli tabela faktów sprzedaży zawarte 36 miesięczny partycji i biorąc pod uwagę, że usługa SQL Data Warehouse ma 60 dystrybucji, następnie tabeli faktów sprzedaży powinien zawierać 60 milionów wierszy na miesiąc lub 2.1 miliard wierszy podczas wszystkich miesięcy zostaną wypełnione. Jeśli tabela zawiera mniej niż minimalna zalecana liczba wierszy na partycję, rozważ użycie mniejszej liczby partycji w celu zwiększenia liczby wierszy dla każdej partycji. Aby uzyskać więcej informacji, zobacz [indeksowanie](sql-data-warehouse-tables-index.md) artykułu, który zawiera zapytania, które można ocenić jakość indeksów magazynu kolumn klastra.

## <a name="syntax-differences-from-sql-server"></a>Różnic w składni z programu SQL Server
Usługa SQL Data Warehouse wprowadza sposób zdefiniować partycji, który jest prostsze niż SQL Server. Partycjonowania funkcje i schematy nie są używane w usłudze SQL Data Warehouse, są one w programie SQL Server. Zamiast tego jest wszystko, co należy zrobić, identyfikowanie kolumny podzielone na partycje i punkty granic. Podstawowe pojęcia dotyczące składni partycjonowania może być nieco inne niż SQL Server, są takie same. Program SQL Server i SQL Data Warehouse obsługuje jedna kolumna partycji w tabeli, która może być ranged partycji. Aby dowiedzieć się więcej na temat partycjonowania, zobacz [partycjonowane tabele i indeksy](/sql/relational-databases/partitions/partitioned-tables-and-indexes).

W poniższym przykładzie użyto [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse) instrukcji do partycjonowania tabeli FactInternetSales, w kolumnie OrderDateKey:

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
Aby przeprowadzić migrację definicji partycji programu SQL Server do usługi SQL Data Warehouse po prostu:

- Eliminowanie programu SQL Server [schemat partycji](/sql/t-sql/statements/create-partition-scheme-transact-sql).
- Dodaj [funkcja partycji](/sql/t-sql/statements/create-partition-function-transact-sql) definicji CREATE TABLE.

Jeśli migrujesz tabeli partycjonowanej z wystąpienia programu SQL Server, następujące instrukcje SQL może pomóc ustalić liczbę wierszy w każdej partycji. Należy pamiętać, że jeśli ten sam stopień szczegółowości partycjonowania jest używany w usłudze SQL Data Warehouse, liczba wierszy na partycję zmniejszona współczynnik 60.  

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

## <a name="partition-switching"></a>Przełączanie partycji
Usługa SQL Data Warehouse obsługuje partycji, dzielenie, scalanie i przełączania. Każda z tych funkcji jest wykonywane przy użyciu [instrukcji ALTER TABLE](/sql/t-sql/statements/alter-table-transact-sql) instrukcji.

Aby przełączyć partycji między dwiema tabelami, musi upewnij się, że partycji odpowiednio dostosować ich odpowiednich granice i że definicje tabel są takie same. Jak sprawdzić, czy ograniczenia nie są dostępne do wymuszania zakres wartości w tabeli, tabeli źródłowej musi zawierać tej samej granice partycji w tabeli docelowej. Granice partycji nie są następnie takie same, następnie przełączenia partycji zakończy się niepowodzeniem metadanych partycji nie będą synchronizowane.

### <a name="how-to-split-a-partition-that-contains-data"></a>Jak podzielić partycji, która zawiera dane
To najwydajniejsza metoda podziału partycji, która zawiera już dane, jest użycie `CTAS` instrukcji. Jeśli partycjonowanej tabeli klastrowanego magazynu kolumn, następnie partycji tabeli może być pusty, zanim może zostać podzielony.

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
```

Następujące zapytanie znajduje się liczba wierszy przy użyciu `sys.partitions` widok katalogu:

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

Msg 35346 15 poziom, stan 1 wiersz 44 PODZIELONY klauzula instrukcji ALTER PARTITION nie powiodło się, ponieważ partycja nie jest pusty. Tylko puste partycje można rozdzielić w przypadku w tabeli istnieje indeks magazynu kolumn. Rozważ wyłączenie indeksu magazynu kolumn przed wykonaniem instrukcji ALTER PARTITION, a następnie odbudowanie indeksu magazynu kolumn po ukończeniu operacji ALTER PARTITION.

Można jednak użyć `CTAS` do utworzenia nowej tabeli do przechowywania danych.

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

Jako granice partycji są wyrównane, przełącznik jest dozwolone. Spowoduje to pozostawienie tabeli źródłowej z pustą partycję, który można następnie podzielić.

```sql
ALTER TABLE FactInternetSales SWITCH PARTITION 2 TO  FactInternetSales_20000101 PARTITION 2;

ALTER TABLE FactInternetSales SPLIT RANGE (20010101);
```

Wszystko, co zostało jest wyrównanie danych do nowych granic partycji przy użyciu `CTAS`, a następnie przełącz dane do tabeli głównej.

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

Po zakończeniu przenoszenia danych jest dobry pomysł, aby odświeżyć statystyk dotyczących tabeli docelowej. Zaktualizowanie statystyk gwarantuje, że statystyki dokładnie odzwierciedlają nowej dystrybucji dane w ich odpowiednich partycji.

```sql
UPDATE STATISTICS [dbo].[FactInternetSales];
```

### <a name="load-new-data-into-partitions-that-contain-data-in-one-step"></a>Ładowanie nowych danych na partycje, które zawierają dane w jednym kroku
Ładowanie danych na partycje za pomocą przełączanie partycji jest wygodny sposób Przygotuj nowe dane w tabeli, która nie jest widoczna dla użytkowników przełącznika w nowych danych.  Może być trudne w systemach zajęty, aby poradzić sobie z blokowaniem rywalizacji o zasoby skojarzone z przełączanie partycji.  Aby umożliwić wyczyszczenie istniejących danych w jednej partycji, `ALTER TABLE` umożliwia być wymagane, aby przełączyć się dane.  Następnie inne `ALTER TABLE` było wymagane, aby przełączyć się w nowych danych.  W usłudze SQL Data Warehouse `TRUNCATE_TARGET` opcja jest obsługiwana w `ALTER TABLE` polecenia.  Za pomocą `TRUNCATE_TARGET` `ALTER TABLE` polecenia zastępuje istniejące dane w partycji za pomocą nowych danych.  Poniżej znajduje się przykład, który używa `CTAS` Aby utworzyć nową tabelę z istniejącymi danymi wstawia nowe dane, a następnie przełącza wszystkie dane z powrotem do tabeli docelowej, zastępując istniejące dane.

```sql
CREATE TABLE [dbo].[FactInternetSales_NewSales]
    WITH    (   DISTRIBUTION = HASH([ProductKey])
            ,   CLUSTERED COLUMNSTORE INDEX
            ,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                                (20000101,20010101
                                )
                            )
            )
AS
SELECT  *
FROM    [dbo].[FactInternetSales]
WHERE   [OrderDateKey] >= 20000101
AND     [OrderDateKey] <  20010101
;

INSERT INTO dbo.FactInternetSales_NewSales
VALUES (1,20000101,2,2,2,2,2,2);

ALTER TABLE dbo.FactInternetSales_NewSales SWITCH PARTITION 2 TO dbo.FactInternetSales PARTITION 2 WITH (TRUNCATE_TARGET = ON);  
```

### <a name="table-partitioning-source-control"></a>Tabela partycjonowanie kontroli źródła
Aby uniknąć swojej definicji tabeli z **uszkodzona** w systemie kontroli źródła, warto wziąć pod uwagę następujące podejście:

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

1. `SPLIT` Tabela, w ramach procesu wdrażania:

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

W przypadku tej metody statyczne pozostaje kodu w kontroli źródła, a partycjonowania wartości graniczne mogą być dynamiczny; ewoluują wraz z magazynem, wraz z upływem czasu.

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej informacji na temat tworzenia tabel, zobacz artykuły w [Omówienie tabel](sql-data-warehouse-tables-overview.md).

