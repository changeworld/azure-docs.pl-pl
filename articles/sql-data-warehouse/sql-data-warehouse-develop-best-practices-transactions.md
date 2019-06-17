---
title: Optymalizowanie transakcji dla usługi Azure SQL Data Warehouse | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zoptymalizować wydajność transakcyjnych kodu w usłudze Azure SQL Data Warehouse, jednocześnie minimalizując ryzyko dla długich wycofywanie zmian.
services: sql-data-warehouse
author: XiaoyuL-Preview
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 04/19/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 9ab1da9fce74359448311591986d57abbbcef066
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65873641"
---
# <a name="optimizing-transactions-in-azure-sql-data-warehouse"></a>Optymalizowanie transakcji w usłudze Azure SQL Data Warehouse
Dowiedz się, jak zoptymalizować wydajność transakcyjnych kodu w usłudze Azure SQL Data Warehouse, jednocześnie minimalizując ryzyko dla długich wycofywanie zmian.

## <a name="transactions-and-logging"></a>Rejestrowanie i transakcji
Transakcje są ważnym elementem aparacie relacyjnej bazy danych. Usługa SQL Data Warehouse używa transakcji podczas modyfikacji danych. Te transakcje może być jawne lub niejawne. Instrukcje pojedynczego INSERT, UPDATE i DELETE należą do nich niejawne transakcji. Jawnego transakcji Użyj BEGIN TRAN, TRAN zatwierdzania lub WYCOFYWANIA transakcji. Jawne transakcje są zwykle używane, gdy wiele instrukcji modyfikacji muszą być powiązane ze sobą w jednym pojedynczej Atomowej jednostki. 

Usługa Azure SQL Data Warehouse zatwierdzenia zmian w bazie danych przy użyciu dzienników transakcji. Każda dystrybucja ma swój własny dziennika transakcji. Zapisuje dziennik transakcji są automatyczne. Nie jest wymagana żadna konfiguracja. Jednak podczas tego procesu gwarantuje zapisu go wprowadzić obciążenie w systemie. Można zminimalizować wpływ, przez napisanie kodu transakcyjnie wydajne. Kod transakcyjnie wydajne szeroko znajduje się na dwie kategorie.

* Rejestrowanie minimalne użycie konstrukcji zawsze, gdy jest to możliwe
* Przetwarzanie danych przy użyciu partie o określonym zakresie, aby uniknąć pojedynczej długotrwałej transakcji
* Przyjęcie partycji przełączania wzorca dla dużych zmian w danej partycji

## <a name="minimal-vs-full-logging"></a>Minimalny a pełne rejestrowanie
W przeciwieństwie do w pełni zarejestrowane operacje, które umożliwia śledzenie każda zmiana wiersza, należy użyć dziennika transakcji, co najmniej rejestrowanych operacji zachować informacje o zakresie alokacji i tylko zmiany metadanych. W związku z tym minimalnym rejestrowaniem polega na rejestrowanie wyłącznie informacje wymagane można wycofać transakcji po awarii lub dla jawnego żądania (TRAN WYCOFYWANIA). Ponieważ znacznie mniej informacja jest śledzona w dzienniku transakcji, minimalny zestaw zarejestrowanych operacji działa lepiej niż o podobnym rozmiarze pełni zarejestrowanych operacji. Ponadto ponieważ mniejszą liczbę zapisów przechodzi dziennika transakcji, znacznie mniejszą ilość danych dziennika jest generowany i dlatego więcej operacji We/Wy wydajne.

Limity bezpieczeństwa transakcji mają zastosowanie tylko do w pełni rejestrowanych operacji.

> [!NOTE]
> Co najmniej rejestrowanych operacji mogą uczestniczyć w transakcji jawnej. Ponieważ wszystkie zmiany w alokacji struktur są śledzone, istnieje możliwość wycofać minimalny zestaw rejestrowanych operacji. 
> 
> 

## <a name="minimally-logged-operations"></a>Co najmniej rejestrowanych operacji
Następujące operacje są w stanie są rejestrowane:

* UTWÓRZ TABLE AS SELECT ([CTAS](sql-data-warehouse-develop-ctas.md))
* WSTAW... WYBIERZ POZYCJĘ
* CREATE INDEX
* ALTER INDEX REBUILD
* DROP INDEX
* OBCINANIE TABELI
* USUNIĘCIE TABELI
* INSTRUKCJA ALTER TABLE SWITCH PARTITION

<!--
- MERGE
- UPDATE on LOB Types .WRITE
- SELECT..INTO
-->

> [!NOTE]
> Operacje przenoszenia danych wewnętrznych (np. emisji i SHUFFLE) nie dotyczy limitu bezpieczeństwa transakcji.
> 
> 

## <a name="minimal-logging-with-bulk-load"></a>Rejestrowanie przy użyciu ładowania zbiorczego minimalne
CTAS i INSERT... Wybierz są operacjami ładowania zbiorczego. Jednak zarówno wpływało definicję tabeli docelowej i zależą od scenariusza obciążenia. W poniższej tabeli opisano, gdy operacje zbiorcze są całkowicie lub co najmniej rejestrowane:  

| Podstawowy indeks | Scenariusz obciążenia | Tryb rejestrowania |
| --- | --- | --- |
| Sterta |Dowolne |**Minimalny** |
| Indeks klastrowany |Tabela docelowa pusty |**Minimalny** |
| Indeks klastrowany |Załadowano wierszy nie pokrywają się przy użyciu istniejących stron w lokalizacji docelowej |**Minimalny** |
| Indeks klastrowany |Załadowano wierszy pokrywać się z istniejących stron w lokalizacji docelowej |Pełne |
| Klastrowany indeks magazynu kolumn |Wielkość partii > = 102400 na dystrybucję partycji wyrównane |**Minimalny** |
| Klastrowany indeks magazynu kolumn |Rozmiar < 102400 na dystrybucję partycji wyrównane do partii |Pełne |

Warto zauważyć, że wszelkie operacje zapisu, aby zaktualizować dodatkowej lub nieklastrowanych indeksów będzie zawsze w pełni rejestrowanych operacji.

> [!IMPORTANT]
> Usługa SQL Data Warehouse ma 60 dystrybucji. W związku z tym, zakładając, że wszystkie wiersze są dystrybuowane równomiernie i kierowanych do jednej partycji, partii musi zawierać 6,144,000 wierszy lub większej w celu zarejestrowania minimalny zestaw podczas zapisywania do klastrowanego indeksu magazynu kolumn. Jeśli tabela jest podzielona na partycje, a wiersze, jest wstawiany span granice partycji, będzie konieczne 6,144,000 wiersze na granicy partycji, zakładając, że nawet dystrybucję danych. Każdej partycji w każdej dystrybucji niezależnie może przekroczyć próg 102 400 wierszy insert minimalny zestaw zalogowania się do dystrybucji.
> 
> 

Trwa ładowanie danych do niepustej tabeli z indeksem klastrowanym często może zawierać mieszaninę pełni rejestrowane i rejestrowane minimalny zestaw wierszy. Indeks klastrowany jest drzewo o zrównoważonym obciążeniu (b drzewa) stron. Jeśli na stronie zapisywana już zawiera wiersze z innej transakcji, następnie zapisuje te zostanie w pełni zarejestrowane. Jednak jeśli strona jest pusta następnie zapisu na tej stronie będą co najmniej rejestrowane.

## <a name="optimizing-deletes"></a>Optymalizacja usuwa
Usuwanie jest w pełni zarejestrowanych operacji.  Jeśli musisz usunąć duże ilości danych w tabeli lub partycję, często warto więcej `SELECT` dane chcesz zachować, które mogą być uruchamiane jako minimalny zestaw zarejestrowanych operacji.  Aby wybrać dane, należy utworzyć nową tabelę z [CTAS](sql-data-warehouse-develop-ctas.md).  Po utworzeniu użyj [Zmień nazwę](/sql/t-sql/statements/rename-transact-sql) wymienić stare tabeli z nowo utworzoną tabelę.

```sql
-- Delete all sales transactions for Promotions except PromotionKey 2.

--Step 01. Create a new table select only the records we want to kep (PromotionKey 2)
CREATE TABLE [dbo].[FactInternetSales_d]
WITH
(    CLUSTERED COLUMNSTORE INDEX
,    DISTRIBUTION = HASH([ProductKey])
,     PARTITION     (    [OrderDateKey] RANGE RIGHT 
                                    FOR VALUES    (    20000101, 20010101, 20020101, 20030101, 20040101, 20050101
                                                ,    20060101, 20070101, 20080101, 20090101, 20100101, 20110101
                                                ,    20120101, 20130101, 20140101, 20150101, 20160101, 20170101
                                                ,    20180101, 20190101, 20200101, 20210101, 20220101, 20230101
                                                ,    20240101, 20250101, 20260101, 20270101, 20280101, 20290101
                                                )
)
AS
SELECT     *
FROM     [dbo].[FactInternetSales]
WHERE    [PromotionKey] = 2
OPTION (LABEL = 'CTAS : Delete')
;

--Step 02. Rename the Tables to replace the 
RENAME OBJECT [dbo].[FactInternetSales]   TO [FactInternetSales_old];
RENAME OBJECT [dbo].[FactInternetSales_d] TO [FactInternetSales];
```

## <a name="optimizing-updates"></a>Optymalizacja aktualizacji
Aktualizacja jest w pełni zarejestrowanych operacji.  Jeśli musisz zaktualizować dużą liczbę wierszy w tabeli lub partycję, często może być znacznie bardziej efektywne używać minimalny zestaw zarejestrowanych operacji, takich jak [CTAS](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) Aby to zrobić.

W przykładzie poniżej pełną tabelę został przekonwertowany aktualizacji do instrukcji CTAS tak, aby możliwe jest minimalnym rejestrowaniem.

W tym przypadku wsteczną dodajemy kwota rabat w wysokości do sprzedaży w tabeli:

```sql
--Step 01. Create a new table containing the "Update". 
CREATE TABLE [dbo].[FactInternetSales_u]
WITH
(    CLUSTERED INDEX
,    DISTRIBUTION = HASH([ProductKey])
,     PARTITION     (    [OrderDateKey] RANGE RIGHT 
                                    FOR VALUES    (    20000101, 20010101, 20020101, 20030101, 20040101, 20050101
                                                ,    20060101, 20070101, 20080101, 20090101, 20100101, 20110101
                                                ,    20120101, 20130101, 20140101, 20150101, 20160101, 20170101
                                                ,    20180101, 20190101, 20200101, 20210101, 20220101, 20230101
                                                ,    20240101, 20250101, 20260101, 20270101, 20280101, 20290101
                                                )
                )
)
AS 
SELECT
    [ProductKey]  
,    [OrderDateKey] 
,    [DueDateKey]  
,    [ShipDateKey] 
,    [CustomerKey] 
,    [PromotionKey] 
,    [CurrencyKey] 
,    [SalesTerritoryKey]
,    [SalesOrderNumber]
,    [SalesOrderLineNumber]
,    [RevisionNumber]
,    [OrderQuantity]
,    [UnitPrice]
,    [ExtendedAmount]
,    [UnitPriceDiscountPct]
,    ISNULL(CAST(5 as float),0) AS [DiscountAmount]
,    [ProductStandardCost]
,    [TotalProductCost]
,    ISNULL(CAST(CASE WHEN [SalesAmount] <=5 THEN 0
         ELSE [SalesAmount] - 5
         END AS MONEY),0) AS [SalesAmount]
,    [TaxAmt]
,    [Freight]
,    [CarrierTrackingNumber] 
,    [CustomerPONumber]
FROM    [dbo].[FactInternetSales]
OPTION (LABEL = 'CTAS : Update')
;

--Step 02. Rename the tables
RENAME OBJECT [dbo].[FactInternetSales]   TO [FactInternetSales_old];
RENAME OBJECT [dbo].[FactInternetSales_u] TO [FactInternetSales];

--Step 03. Drop the old table
DROP TABLE [dbo].[FactInternetSales_old]
```

> [!NOTE]
> Ponowne tworzenie dużych tabel mogą korzystać z funkcji zarządzania usługi SQL Data Warehouse obciążenia. Aby uzyskać więcej informacji, zobacz [klasy zasobów do zarządzania obciążeniem](resource-classes-for-workload-management.md).
> 
> 

## <a name="optimizing-with-partition-switching"></a>Optymalizacja z użyciem przełączanie partycji
Jeśli w obliczu modyfikacji na dużą skalę w [tabeli partycji](sql-data-warehouse-tables-partition.md), partycji, przełączanie wzorzec ma sens, a następnie. Jeśli modyfikacji danych jest znaczące i obejmuje wiele partycji, następnie Iterowanie partycje osiąga ten sam wynik.

Czynności do wykonania przełączenia partycji, są następujące:

1. Utwórz pustą z partycji
2. Wykonaj tę aktualizację jako instrukcji CTAS
3. Przełącz się istniejących danych do tabeli poza
4. Przełącz w nowych danych
5. Czyszczenie danych

Jednak aby ułatwić identyfikację partycji, aby przełączyć się, należy utworzyć procedurę pomocnika.  

```sql
CREATE PROCEDURE dbo.partition_data_get
    @schema_name           NVARCHAR(128)
,    @table_name               NVARCHAR(128)
,    @boundary_value           INT
AS
IF OBJECT_ID('tempdb..#ptn_data') IS NOT NULL
BEGIN
    DROP TABLE #ptn_data
END
CREATE TABLE #ptn_data
WITH    (    DISTRIBUTION = ROUND_ROBIN
        ,    HEAP
        )
AS
WITH CTE
AS
(
SELECT     s.name                            AS [schema_name]
,        t.name                            AS [table_name]
,         p.partition_number                AS [ptn_nmbr]
,        p.[rows]                        AS [ptn_rows]
,        CAST(r.[value] AS INT)            AS [boundary_value]
FROM        sys.schemas                    AS s
JOIN        sys.tables                    AS t    ON  s.[schema_id]        = t.[schema_id]
JOIN        sys.indexes                    AS i    ON     t.[object_id]        = i.[object_id]
JOIN        sys.partitions                AS p    ON     i.[object_id]        = p.[object_id] 
                                                AND i.[index_id]        = p.[index_id] 
JOIN        sys.partition_schemes        AS h    ON     i.[data_space_id]    = h.[data_space_id]
JOIN        sys.partition_functions        AS f    ON     h.[function_id]        = f.[function_id]
LEFT JOIN    sys.partition_range_values    AS r     ON     f.[function_id]        = r.[function_id] 
                                                AND r.[boundary_id]        = p.[partition_number]
WHERE i.[index_id] <= 1
)
SELECT    *
FROM    CTE
WHERE    [schema_name]        = @schema_name
AND        [table_name]        = @table_name
AND        [boundary_value]    = @boundary_value
OPTION (LABEL = 'dbo.partition_data_get : CTAS : #ptn_data')
;
GO
```

Ta procedura zmaksymalizować ponowne wykorzystanie kodu i utrzymuje przełączania przykład bardziej zwarty partycji.

Poniższy kod demonstruje kroków wymienionych wcześniej, aby osiągnąć pełną partycji przełączanie procedury.

```sql
--Create a partitioned aligned empty table to switch out the data 
IF OBJECT_ID('[dbo].[FactInternetSales_out]') IS NOT NULL
BEGIN
    DROP TABLE [dbo].[FactInternetSales_out]
END

CREATE TABLE [dbo].[FactInternetSales_out]
WITH
(    DISTRIBUTION = HASH([ProductKey])
,    CLUSTERED COLUMNSTORE INDEX
,     PARTITION     (    [OrderDateKey] RANGE RIGHT 
                                    FOR VALUES    (    20020101, 20030101
                                                )
                )
)
AS
SELECT *
FROM    [dbo].[FactInternetSales]
WHERE 1=2
OPTION (LABEL = 'CTAS : Partition Switch IN : UPDATE')
;

--Create a partitioned aligned table and update the data in the select portion of the CTAS
IF OBJECT_ID('[dbo].[FactInternetSales_in]') IS NOT NULL
BEGIN
    DROP TABLE [dbo].[FactInternetSales_in]
END

CREATE TABLE [dbo].[FactInternetSales_in]
WITH
(    DISTRIBUTION = HASH([ProductKey])
,    CLUSTERED COLUMNSTORE INDEX
,     PARTITION     (    [OrderDateKey] RANGE RIGHT 
                                    FOR VALUES    (    20020101, 20030101
                                                )
                )
)
AS 
SELECT
    [ProductKey]  
,    [OrderDateKey] 
,    [DueDateKey]  
,    [ShipDateKey] 
,    [CustomerKey] 
,    [PromotionKey] 
,    [CurrencyKey] 
,    [SalesTerritoryKey]
,    [SalesOrderNumber]
,    [SalesOrderLineNumber]
,    [RevisionNumber]
,    [OrderQuantity]
,    [UnitPrice]
,    [ExtendedAmount]
,    [UnitPriceDiscountPct]
,    ISNULL(CAST(5 as float),0) AS [DiscountAmount]
,    [ProductStandardCost]
,    [TotalProductCost]
,    ISNULL(CAST(CASE WHEN [SalesAmount] <=5 THEN 0
         ELSE [SalesAmount] - 5
         END AS MONEY),0) AS [SalesAmount]
,    [TaxAmt]
,    [Freight]
,    [CarrierTrackingNumber] 
,    [CustomerPONumber]
FROM    [dbo].[FactInternetSales]
WHERE    OrderDateKey BETWEEN 20020101 AND 20021231
OPTION (LABEL = 'CTAS : Partition Switch IN : UPDATE')
;

--Use the helper procedure to identify the partitions
--The source table
EXEC dbo.partition_data_get 'dbo','FactInternetSales',20030101
DECLARE @ptn_nmbr_src INT = (SELECT ptn_nmbr FROM #ptn_data)
SELECT @ptn_nmbr_src

--The "in" table
EXEC dbo.partition_data_get 'dbo','FactInternetSales_in',20030101
DECLARE @ptn_nmbr_in INT = (SELECT ptn_nmbr FROM #ptn_data)
SELECT @ptn_nmbr_in

--The "out" table
EXEC dbo.partition_data_get 'dbo','FactInternetSales_out',20030101
DECLARE @ptn_nmbr_out INT = (SELECT ptn_nmbr FROM #ptn_data)
SELECT @ptn_nmbr_out

--Switch the partitions over
DECLARE @SQL NVARCHAR(4000) = '
ALTER TABLE [dbo].[FactInternetSales]    SWITCH PARTITION '+CAST(@ptn_nmbr_src AS VARCHAR(20))    +' TO [dbo].[FactInternetSales_out] PARTITION '    +CAST(@ptn_nmbr_out AS VARCHAR(20))+';
ALTER TABLE [dbo].[FactInternetSales_in] SWITCH PARTITION '+CAST(@ptn_nmbr_in AS VARCHAR(20))    +' TO [dbo].[FactInternetSales] PARTITION '        +CAST(@ptn_nmbr_src AS VARCHAR(20))+';'
EXEC sp_executesql @SQL

--Perform the clean-up
TRUNCATE TABLE dbo.FactInternetSales_out;
TRUNCATE TABLE dbo.FactInternetSales_in;

DROP TABLE dbo.FactInternetSales_out
DROP TABLE dbo.FactInternetSales_in
DROP TABLE #ptn_data
```

## <a name="minimize-logging-with-small-batches"></a>Minimalizuj rejestrowanie za pomocą małych partiach
Dla operacji modyfikujących dużych ilości danych rozsądne może okazać się podziału operacji na fragmenty lub partie zakresu jednostka pracy.

Poniższy kod jest praktyczny przykład. Rozmiar partii ustawiono numerowi trivial wyróżnić techniki. W rzeczywistości rozmiar partii będzie znacznie większe. 

```sql
SET NO_COUNT ON;
IF OBJECT_ID('tempdb..#t') IS NOT NULL
BEGIN
    DROP TABLE #t;
    PRINT '#t dropped';
END

CREATE TABLE #t
WITH    (    DISTRIBUTION = ROUND_ROBIN
        ,    HEAP
        )
AS
SELECT    ROW_NUMBER() OVER(ORDER BY (SELECT NULL)) AS seq_nmbr
,        SalesOrderNumber
,        SalesOrderLineNumber
FROM    dbo.FactInternetSales
WHERE    [OrderDateKey] BETWEEN 20010101 and 20011231
;

DECLARE    @seq_start        INT = 1
,        @batch_iterator    INT = 1
,        @batch_size        INT = 50
,        @max_seq_nmbr    INT = (SELECT MAX(seq_nmbr) FROM dbo.#t)
;

DECLARE    @batch_count    INT = (SELECT CEILING((@max_seq_nmbr*1.0)/@batch_size))
,        @seq_end        INT = @batch_size
;

SELECT COUNT(*)
FROM    dbo.FactInternetSales f

PRINT 'MAX_seq_nmbr '+CAST(@max_seq_nmbr AS VARCHAR(20))
PRINT 'MAX_Batch_count '+CAST(@batch_count AS VARCHAR(20))

WHILE    @batch_iterator <= @batch_count
BEGIN
    DELETE
    FROM    dbo.FactInternetSales
    WHERE EXISTS
    (
            SELECT    1
            FROM    #t t
            WHERE    seq_nmbr BETWEEN  @seq_start AND @seq_end
            AND        FactInternetSales.SalesOrderNumber        = t.SalesOrderNumber
            AND        FactInternetSales.SalesOrderLineNumber    = t.SalesOrderLineNumber
    )
    ;

    SET @seq_start = @seq_end
    SET @seq_end = (@seq_start+@batch_size);
    SET @batch_iterator +=1;
END
```

## <a name="pause-and-scaling-guidance"></a>Wstrzymywanie i skalowanie wskazówki
Usługa Azure SQL Data Warehouse umożliwia [wstrzymać, wznowić i skalowanie](sql-data-warehouse-manage-compute-overview.md) magazynu danych na żądanie. W przypadku wstrzymania lub skalowania usługi SQL Data Warehouse, ważne jest, aby zrozumieć, od razu; zakończenia wszystkie aktywne transakcje powoduje cofnięcie transakcji otwartych. Jeśli obciążenie wystawił długo działa i jest niekompletna modyfikacji danych przed operacji wstrzymywania lub skalowania, następnie tej pracy należy można cofnąć. Cofanie tej zmiany mogą mieć wpływ na czas, jaki zajmuje wstrzymywania lub skalowania bazy danych Azure SQL Data Warehouse. 

> [!IMPORTANT]
> Zarówno `UPDATE` i `DELETE` są w pełni rejestrowanych operacji, a więc te Cofnij/ponów operacji może potrwać znacznie dłuższe niż odpowiednik rejestrowane w operacji. 
> 
> 

Jest najlepszym scenariuszu umożliwiające w locie modyfikacji transakcje ukończone przed wstrzymywaniem i skalowaniem SQL Data Warehouse. Jednak ten scenariusz może nie zawsze jest praktyczne. Aby zminimalizować ryzyko długiego czasu wycofywania, należy wziąć pod uwagę jedną z następujących opcji:

* Ponowne zapisywanie adresów długotrwałe operacje przy użyciu [CTAS](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse)
* Podział operacji na fragmenty; wykonywanie operacji na podzestaw wierszy

## <a name="next-steps"></a>Kolejne kroki
Zobacz [transakcje w usłudze SQL Data Warehouse](sql-data-warehouse-develop-transactions.md) dowiedzieć się więcej o poziomy izolacji i limity transakcyjnych.  Aby uzyskać ogólne najlepsze rozwiązania, zobacz [najlepsze rozwiązania dotyczące SQL Data Warehouse](sql-data-warehouse-best-practices.md).

