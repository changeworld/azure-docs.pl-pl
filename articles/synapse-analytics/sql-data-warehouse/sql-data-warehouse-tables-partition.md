---
title: Partycjonowanie tabel
description: Zalecenia i przykłady dotyczące używania partycji tabeli w puli synapse SQL
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 03/18/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 4e19c20036d74752b75a668d6a37c46ef1b008e6
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80583190"
---
# <a name="partitioning-tables-in-synapse-sql-pool"></a>Partycjonowanie tabel w puli SQL Synapse

Zalecenia i przykłady dotyczące używania partycji tabeli w puli języka SQL Synapse.

## <a name="what-are-table-partitions"></a>Co to są partycje tabeli?

Partycje tabeli umożliwiają dzielenie danych na mniejsze grupy danych. W większości przypadków partycje tabeli są tworzone w kolumnie daty. Partycjonowanie jest obsługiwane we wszystkich typach tabel puli Synapse SQL; w tym klastrowany magazyn kolumn, indeks klastrowany i sterty. Partycjonowanie jest również obsługiwane we wszystkich typach dystrybucji, w tym zarówno mieszania lub okrężnego rozproszone.  

Partycjonowanie może korzystać konserwacji danych i wydajności kwerendy. Czy to korzyści zarówno lub tylko jeden zależy od sposobu ładowania danych i czy ta sama kolumna może służyć do obu celów, ponieważ partycjonowanie można wykonać tylko w jednej kolumnie.

### <a name="benefits-to-loads"></a>Korzyści dla obciążeń

Podstawową zaletą partycjonowania w puli Synapse SQL jest poprawa wydajności i wydajności ładowania danych za pomocą usuwania partycji, przełączania i scalania. W większości przypadków dane są podzielone na partycje w kolumnie daty, która jest ściśle powiązana z kolejnością, w której dane są ładowane do bazy danych. Jedną z największych zalet korzystania z partycji do obsługi danych to unikanie rejestrowania transakcji. Podczas po prostu wstawianie, aktualizowanie lub usuwanie danych może być najprostszym podejściem, przy odrobinie przemyślenia i wysiłku, przy użyciu partycjonowania podczas procesu ładowania może znacznie poprawić wydajność.

Przełączanie partycji może służyć do szybkiego usunięcia lub zastąpienia części tabeli.  Na przykład tabela faktów sprzedaży może zawierać tylko dane z ostatnich 36 miesięcy. Na koniec każdego miesiąca najstarszy miesiąc danych sprzedaży jest usuwany z tabeli.  Te dane można usunąć za pomocą instrukcji delete, aby usunąć dane dla najstarszego miesiąca. Jednak usunięcie dużej ilości danych wiersz po wierszu za pomocą instrukcji delete może zająć zbyt dużo czasu, a także stworzyć ryzyko dużych transakcji, które zajmują dużo czasu, aby wycofać, jeśli coś pójdzie nie tak. Bardziej optymalnym podejściem jest upuszczenie najstarszej partycji danych. Jeśli usunięcie poszczególnych wierszy może potrwać wiele godzin, usunięcie całej partycji może potrwać kilka sekund.

### <a name="benefits-to-queries"></a>Korzyści dla zapytań

Partycjonowanie może również służyć do zwiększenia wydajności kwerendy. Kwerenda, która stosuje filtr do danych podzielonych na partycje, może ograniczyć skanowanie tylko do kwalifikujących się partycji. Ta metoda filtrowania może uniknąć pełnego skanowania tabeli i skanowania tylko mniejszego podzbioru danych. Wraz z wprowadzeniem indeksów magazynu kolumn klastrowanych korzyści wydajności eliminacji predykatu są mniej korzystne, ale w niektórych przypadkach może być korzyści dla zapytań. Na przykład jeśli tabela fakt sprzedaży jest podzielony na 36 miesięcy przy użyciu pola data sprzedaży, a następnie kwerendy, które filtrują w dniu sprzedaży można pominąć wyszukiwanie w partycjach, które nie pasują do filtru.

## <a name="sizing-partitions"></a>Podziały rozmiaru

Podczas partycjonowania może służyć do zwiększenia wydajności niektórych scenariuszy, tworzenie tabeli ze **zbyt wielu** partycji może zaszkodzić wydajności w pewnych okolicznościach.  Te problemy są szczególnie prawdziwe w przypadku tabel magazynu kolumn klastrowanych. Dla partycjonowania być pomocne, ważne jest, aby zrozumieć, kiedy używać partycjonowania i liczbę partycji do utworzenia. Nie ma twardej szybkiej reguły, ile partycji jest zbyt wiele, to zależy od danych i ile partycji ładujesz jednocześnie. Udany schemat partycjonowania zwykle ma dziesiątki do setek partycji, a nie tysiące.

Podczas tworzenia partycji w **tabelach magazynu kolumn klastrowanych,** należy wziąć pod uwagę, ile wierszy należą do każdej partycji. Aby uzyskać optymalną kompresję i wydajność tabel magazynu kolumn klastrowanych, potrzeba co najmniej 1 miliona wierszy na dystrybucję i partycję. Przed utworzeniem partycji pula Sql Synapse dzieli już każdą tabelę na 60 rozproszonych baz danych. Wszelkie partycjonowanie dodane do tabeli jest dodatkiem do dystrybucji utworzonych za kulisami. W tym przykładzie, jeśli tabela fakt sprzedaży zawiera 36 partycji miesięcznych i biorąc pod uwagę, że puli Synapse SQL ma 60 dystrybucji, tabela fakt sprzedaży powinna zawierać 60 milionów wierszy miesięcznie lub 2,1 miliarda wierszy, gdy wszystkie miesiące są wypełniane. Jeśli tabela zawiera mniej niż zalecana minimalna liczba wierszy na partycję, należy rozważyć użycie mniejszej liczby partycji w celu zwiększenia liczby wierszy na partycję. Aby uzyskać więcej informacji, zobacz artykuł [Indeksowanie,](sql-data-warehouse-tables-index.md) który zawiera kwerendy, które mogą ocenić jakość indeksów magazynu kolumn klastra.

## <a name="syntax-differences-from-sql-server"></a>Różnice składniowe z programu SQL Server

Puli SQL Synapse wprowadza sposób definiowania partycji, które jest prostsze niż SQL Server. Funkcje i schematy partycjonowania nie są używane w puli SQL Synapse, ponieważ znajdują się w programie SQL Server. Zamiast tego wszystko, co musisz zrobić, to zidentyfikować kolumnę podzieloną na partycje i punkty graniczne. Chociaż składnia partycjonowania może się nieznacznie różnić od programu SQL Server, podstawowe pojęcia są takie same. SQL Server i Synapse puli SQL obsługuje jedną kolumnę partycji na tabelę, które mogą być podzielone partycji. Aby dowiedzieć się więcej o partycjonowaniu, zobacz [Tabele i indeksy podzielone na partycje](/sql/relational-databases/partitions/partitioned-tables-and-indexes).

W poniższym przykładzie użyto instrukcji [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse) do partycjonowania tabeli FactInternetSales w kolumnie OrderDateKey:

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

Aby przeprowadzić migrację definicji partycji programu SQL Server do puli SQL Synapse po prostu:

- Wyeliminuj [schemat partycji](/sql/t-sql/statements/create-partition-scheme-transact-sql)programu SQL Server .
- Dodaj definicję [funkcji partycji](/sql/t-sql/statements/create-partition-function-transact-sql) do tabeli TWORZENIA.

W przypadku migracji tabeli podzielonej na partycje z wystąpienia programu SQL Server poniższy sql może pomóc w określeniu liczby wierszy na każdej partycji. Należy pamiętać, że jeśli ta sama szczegółowość partycjonowania jest używana w puli Synapse SQL, liczba wierszy na partycję zmniejsza się o współczynnik 60.  

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

Puli SQL Synapse obsługuje dzielenie partycji, scalanie i przełączanie. Każda z tych funkcji jest wykonywana przy użyciu instrukcji [ALTER TABLE.](/sql/t-sql/statements/alter-table-transact-sql)

Aby przełączyć partycje między dwiema tabelami, należy upewnić się, że partycje wyrównać ich odpowiednich granic i że definicje tabeli są zgodne. Ponieważ ograniczenia kontrolne nie są dostępne do wymuszania zakresu wartości w tabeli, tabela źródłowcza musi zawierać te same granice partycji co tabela docelowa. Jeśli granice partycji nie są wtedy takie same, przełącznik partycji zakończy się niepowodzeniem, ponieważ metadane partycji nie zostaną zsynchronizowane.

### <a name="how-to-split-a-partition-that-contains-data"></a>Jak podzielić partycję zawierającą dane

Najbardziej efektywną metodą dzielenia partycji, `CTAS` która już zawiera dane, jest użycie instrukcji. Jeśli tabela podzielona na partycje jest klastrowanym magazynem kolumn, partycja tabeli musi być pusta, zanim będzie można ją podzielić.

Poniższy przykład tworzy tabelę magazynu kolumn podzielonych na partycje. Wstawia jeden wiersz do każdej partycji:

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

Następująca kwerenda znajduje liczbę `sys.partitions` wierszy przy użyciu widoku katalogu:

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

Msg 35346, Poziom 15, Stan 1, Wiersz 44 SPLIT klauzuli ALTER PARTITION instrukcji nie powiodło się, ponieważ partycja nie jest pusta. Tylko puste partycje można podzielić, gdy indeks magazynu kolumn istnieje w tabeli. Należy rozważyć wyłączenie indeksu magazynu kolumn przed wydaniem instrukcji ALTER PARTITION, a następnie odbudowując indeks magazynu kolumn po zakończeniu alter partition.

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

Ponieważ granice partycji są wyrównane, przełącznik jest dozwolony. Spowoduje to pozostawienie tabeli źródłowej z pustą partycją, którą można następnie podzielić.

```sql
ALTER TABLE FactInternetSales SWITCH PARTITION 2 TO  FactInternetSales_20000101 PARTITION 2;

ALTER TABLE FactInternetSales SPLIT RANGE (20010101);
```

Wszystko, co pozostało, to wyrównać dane do `CTAS`nowych granic partycji za pomocą programu , a następnie przełączyć dane z powrotem do tabeli głównej.

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

Po zakończeniu przenoszenia danych warto odświeżyć statystyki w tabeli docelowej. Aktualizacja statystyk zapewnia statystyki dokładnie odzwierciedlają nowy rozkład danych w ich odpowiednich partycjach.

```sql
UPDATE STATISTICS [dbo].[FactInternetSales];
```

### <a name="load-new-data-into-partitions-that-contain-data-in-one-step"></a>Ładowanie nowych danych do partycji zawierających dane w jednym kroku

Ładowanie danych do partycji z przełączaniem partycji jest wygodny sposób etap nowych danych w tabeli, która nie jest widoczna dla użytkowników przełącznika w nowych danych.  Może to być trudne w systemach zajęty do czynienia z rywalizacji blokowania związane z przełączania partycji.  Aby wyczyścić istniejące dane w `ALTER TABLE` partycji, używane do przełączania danych.  Następnie `ALTER TABLE` konieczne było przełączenie się nowych danych.  W puli SQL Synapse `TRUNCATE_TARGET` opcja jest `ALTER TABLE` obsługiwana w poleceniu.  Za `TRUNCATE_TARGET` `ALTER TABLE` pomocą polecenia zastępuje istniejące dane na partycji nowymi danymi.  Poniżej znajduje się `CTAS` przykład, który używa do tworzenia nowej tabeli z istniejącymi danymi, wstawia nowe dane, a następnie przełącza wszystkie dane z powrotem do tabeli docelowej, zastępując istniejące dane.

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

### <a name="table-partitioning-source-control"></a>Sterowanie źródłem partycjonowania tabeli

Aby uniknąć **rdzewienia** definicji tabeli w systemie kontroli źródła, należy rozważyć następujące podejście:

1. Tworzenie tabeli jako tabeli podzielonej na partycje, ale bez wartości partycji

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

1. `SPLIT`tabela w ramach procesu wdrażania:

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

Z tego podejścia kod w kontroli źródła pozostaje statyczne i partycjonowanie wartości graniczne mogą być dynamiczne; ewoluuje z bazą danych w miarę czasu.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat tworzenia tabel, zobacz artykuły [w przeglądzie tabel .](sql-data-warehouse-tables-overview.md)

