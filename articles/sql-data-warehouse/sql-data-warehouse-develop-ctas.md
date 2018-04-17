---
title: Utwórz TABLE AS SELECT (CTAS) w usłudze Azure SQL Data Warehouse | Dokumentacja firmy Microsoft
description: Porady dotyczące programowania z instrukcji tworzenia tabeli jako wybierz (CTAS) w usłudze Azure SQL Data Warehouse związane z opracowywaniem rozwiązań.
services: sql-data-warehouse
author: ronortloff
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/12/2018
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: cb36cb7d3dd652608620283fe2ea2e6649406be5
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="using-create-table-as-select-ctas-in-azure-sql-data-warehouse"></a>Przy użyciu CREATE TABLE AS SELECT (CTAS) w magazynie danych Azure SQL
Porady dotyczące programowania z instrukcji tworzenia tabeli jako wybierz (CTAS) T-SQL w usłudze Azure SQL Data Warehouse związane z opracowywaniem rozwiązań.

## <a name="what-is-create-table-as-select-ctas"></a>Co to jest tworzenie tabeli jako wybierz (CTAS)?

[CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) lub instrukcji CTAS jest jednym z najważniejszych funkcji T-SQL. Jest operacji równoległej, która tworzy nową tabelę oparte na danych wyjściowych instrukcji SELECT. CTASD jest najprostszym i najszybszym sposobem tworzenia kopii tabeli. 

## <a name="selectinto-vs-ctas"></a>WYBIERZ... W wersji programu vs. CTAS
Należy rozważyć CTAS nadrzędne obciążona wersja [wybierz... DO](/sql/t-sql/queries/select-into-clause-transact-sql) instrukcji.

Poniżej znajduje się przykład prostego SELECT... DO:

```sql
SELECT *
INTO    [dbo].[FactInternetSales_new]
FROM    [dbo].[FactInternetSales]
```

W powyższym przykładzie `[dbo].[FactInternetSales_new]` zostanie utworzona jako ROUND_ROBIN rozproszonej tabeli z INDEKSEM magazynu kolumn w KLASTRZE, ponieważ są to domyślne tabeli w magazynie danych SQL Azure.

WYBIERZ... DO jednak nie umożliwiają zmianę metody dystrybucji lub typ indeksu jako część operacji. Jest to, gdzie CTAS polega na.

Aby przekonwertować w poprzednim przykładzie, aby CTAS jest dość proste:

```sql
CREATE TABLE [dbo].[FactInternetSales_new]
WITH
(
    DISTRIBUTION = ROUND_ROBIN
,   CLUSTERED COLUMNSTORE INDEX
)
AS
SELECT  *
FROM    [dbo].[FactInternetSales]
;
```

Z CTAS będą mogli zmienić dystrybucji dane w tabeli, a także z typem tabeli. 

> [!NOTE]
> Jeśli tylko chcesz zmienić indeks w Twojej `CTAS` operacji i tabeli źródłowej są rozpowszechniane skrót, a następnie z `CTAS` operacji będzie wykonywać najlepiej, jeśli obsługa tego samego typu kolumny i danych dystrybucji. Pozwoli to uniknąć cross przenoszenia danych dystrybucji podczas operacji, które jest bardziej wydajny.
> 
> 

## <a name="using-ctas-to-copy-a-table"></a>Aby skopiować tabelę przy użyciu CTAS
Możliwe, że jedną z najbardziej typowych zastosowań `CTAS` jest tworzenie kopii tabeli, dzięki czemu można zmienić kod DDL. Jeśli na przykład pierwotnie utworzona jako tabela `ROUND_ROBIN` , a teraz chcesz zmienić rozproszonych od kolumny, tabeli `CTAS` jest sposób należy zmienić kolumny dystrybucji. `CTAS` można również zmienić typy partycjonowania, indeksowania lub kolumny.

Załóżmy, że utworzono tej tabeli przy użyciu domyślnego typu dystrybucji `ROUND_ROBIN` rozproszonych, ponieważ kolumna nie dystrybucji została określona w `CREATE TABLE`.

```sql
CREATE TABLE FactInternetSales
(
    ProductKey int NOT NULL,
    OrderDateKey int NOT NULL,
    DueDateKey int NOT NULL,
    ShipDateKey int NOT NULL,
    CustomerKey int NOT NULL,
    PromotionKey int NOT NULL,
    CurrencyKey int NOT NULL,
    SalesTerritoryKey int NOT NULL,
    SalesOrderNumber nvarchar(20) NOT NULL,
    SalesOrderLineNumber tinyint NOT NULL,
    RevisionNumber tinyint NOT NULL,
    OrderQuantity smallint NOT NULL,
    UnitPrice money NOT NULL,
    ExtendedAmount money NOT NULL,
    UnitPriceDiscountPct float NOT NULL,
    DiscountAmount float NOT NULL,
    ProductStandardCost money NOT NULL,
    TotalProductCost money NOT NULL,
    SalesAmount money NOT NULL,
    TaxAmt money NOT NULL,
    Freight money NOT NULL,
    CarrierTrackingNumber nvarchar(25),
    CustomerPONumber nvarchar(25)
);
```

Teraz chcesz utworzyć nową kopię tej tabeli z indeksem magazynu kolumn w klastrze, tak, aby można było korzystać z wydajności tabel klastrowanego magazynu kolumn. Również dystrybuowania tej tabeli po klucz produktu, ponieważ są przewidywanie sprzężenia od tej kolumny i aby uniknąć przenoszenia danych podczas sprzężenia na klucz produktu. Na koniec również chcesz dodać partycjonowania na OrderDateKey, dzięki czemu można szybko usunąć stare dane przez usunięcie starego partycji. Oto instrukcji CTAS, które będzie skopiować starego tabeli do nowej tabeli.

```sql
CREATE TABLE FactInternetSales_new
WITH
(
    CLUSTERED COLUMNSTORE INDEX,
    DISTRIBUTION = HASH(ProductKey),
    PARTITION
    (
        OrderDateKey RANGE RIGHT FOR VALUES
        (
        20000101,20010101,20020101,20030101,20040101,20050101,20060101,20070101,20080101,20090101,
        20100101,20110101,20120101,20130101,20140101,20150101,20160101,20170101,20180101,20190101,
        20200101,20210101,20220101,20230101,20240101,20250101,20260101,20270101,20280101,20290101
        )
    )
)
AS SELECT * FROM FactInternetSales;
```

Na koniec można zmienić nazwy tabel do wymiany w nowej tabeli, a następnie upuść starego tabeli.

```sql
RENAME OBJECT FactInternetSales TO FactInternetSales_old;
RENAME OBJECT FactInternetSales_new TO FactInternetSales;

DROP TABLE FactInternetSales_old;
```

> [!NOTE]
> Usługa Azure SQL Data Warehouse nie obsługuje jeszcze automatycznego tworzenia ani aktualizowania statystyk.  W celu uzyskania najlepszej wydajności zapytań należy utworzyć statystyki dla wszystkich kolumn wszystkich tabel po pierwszym załadowaniu danych, a następnie po każdej istotnej zmianie.  Aby uzyskać szczegółowy opis statystyk zobacz temat [statystyki] [statystyki] w grupie artykułów dla programistów.
> 
> 

## <a name="using-ctas-to-work-around-unsupported-features"></a>Aby obejść nieobsługiwanych funkcji przy użyciu CTAS
CTAS można również obejść liczba nieobsługiwanych funkcji wymienionych poniżej. Można to często okazać się sytuacji win/win nie tylko kodzie będą zgodne, ale będą często wykonywane szybciej na magazyn danych SQL. Jest to wyniku pełni zrównoleglone projekt. Scenariusze, które można pracować z CTAS wokół obejmują:

* SPRZĘŻENIA ANSI na aktualizacje
* Sprzężenia ANSI na usuwaniu
* MERGE — instrukcja

> [!NOTE]
> Pomyśl "CTAS pierwszy". Jeśli uważasz, że uda się rozwiązać problem przy użyciu `CTAS` następnie jest zwykle najlepszym sposobem zbliżających się — nawet wtedy, gdy w związku z tym pisania większej ilości danych.
> 
> 

## <a name="ansi-join-replacement-for-update-statements"></a>ANSI zamienny sprzężenia dla instrukcji update
Może się okazać się, że masz złożonych aktualizacji, w której jest przyłączany więcej niż dwie tabele ze sobą przy użyciu ANSI dołączenie składni przeprowadzić UPDATE lub DELETE.

Załóżmy, że wymagał zaktualizowania tej tabeli:

```sql
CREATE TABLE [dbo].[AnnualCategorySales]
(    [EnglishProductCategoryName]    NVARCHAR(50)    NOT NULL
,    [CalendarYear]                    SMALLINT        NOT NULL
,    [TotalSalesAmount]                MONEY            NOT NULL
)
WITH
(
    DISTRIBUTION = ROUND_ROBIN
)
;
```

Oryginalne zapytanie może sprawdzono podobny do następującego:

```sql
UPDATE    acs
SET        [TotalSalesAmount] = [fis].[TotalSalesAmount]
FROM    [dbo].[AnnualCategorySales]     AS acs
JOIN    (
        SELECT    [EnglishProductCategoryName]
        ,        [CalendarYear]
        ,        SUM([SalesAmount])                AS [TotalSalesAmount]
        FROM    [dbo].[FactInternetSales]        AS s
        JOIN    [dbo].[DimDate]                    AS d    ON s.[OrderDateKey]                = d.[DateKey]
        JOIN    [dbo].[DimProduct]                AS p    ON s.[ProductKey]                = p.[ProductKey]
        JOIN    [dbo].[DimProductSubCategory]    AS u    ON p.[ProductSubcategoryKey]    = u.[ProductSubcategoryKey]
        JOIN    [dbo].[DimProductCategory]        AS c    ON u.[ProductCategoryKey]        = c.[ProductCategoryKey]
        WHERE     [CalendarYear] = 2004
        GROUP BY
                [EnglishProductCategoryName]
        ,        [CalendarYear]
        ) AS fis
ON    [acs].[EnglishProductCategoryName]    = [fis].[EnglishProductCategoryName]
AND    [acs].[CalendarYear]                = [fis].[CalendarYear]
;
```

Ponieważ Magazyn danych SQL nie obsługuje ANSI sprzężenia w `FROM` klauzuli `UPDATE` instrukcji, nie można skopiować ten kod nad zmieniając nieco.

Można użyć kombinacji `CTAS` i niejawne sprzężenia, aby zastąpić ten kod:

```sql
-- Create an interim table
CREATE TABLE CTAS_acs
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT    ISNULL(CAST([EnglishProductCategoryName] AS NVARCHAR(50)),0)    AS [EnglishProductCategoryName]
,        ISNULL(CAST([CalendarYear] AS SMALLINT),0)                         AS [CalendarYear]
,        ISNULL(CAST(SUM([SalesAmount]) AS MONEY),0)                        AS [TotalSalesAmount]
FROM    [dbo].[FactInternetSales]        AS s
JOIN    [dbo].[DimDate]                    AS d    ON s.[OrderDateKey]                = d.[DateKey]
JOIN    [dbo].[DimProduct]                AS p    ON s.[ProductKey]                = p.[ProductKey]
JOIN    [dbo].[DimProductSubCategory]    AS u    ON p.[ProductSubcategoryKey]    = u.[ProductSubcategoryKey]
JOIN    [dbo].[DimProductCategory]        AS c    ON u.[ProductCategoryKey]        = c.[ProductCategoryKey]
WHERE     [CalendarYear] = 2004
GROUP BY
        [EnglishProductCategoryName]
,        [CalendarYear]
;

-- Use an implicit join to perform the update
UPDATE  AnnualCategorySales
SET     AnnualCategorySales.TotalSalesAmount = CTAS_ACS.TotalSalesAmount
FROM    CTAS_acs
WHERE   CTAS_acs.[EnglishProductCategoryName] = AnnualCategorySales.[EnglishProductCategoryName]
AND     CTAS_acs.[CalendarYear]               = AnnualCategorySales.[CalendarYear]
;

--Drop the interim table
DROP TABLE CTAS_acs
;
```

## <a name="ansi-join-replacement-for-delete-statements"></a>Zastępuje sprzężenia ANSI usunąć — instrukcje
Czasami najlepszym rozwiązaniem związanych z usuwaniem danych jest użycie `CTAS`. Zamiast usuwania danych, po prostu zaznacz dane, które chcesz zachować. To szczególnie istotne dla `DELETE` instrukcji używających ansi łącząca składni, ponieważ usługa SQL Data Warehouse nie obsługuje ANSI sprzężenia w `FROM` klauzuli `DELETE` instrukcji.

Przykład przekonwertowanego instrukcji DELETE jest dostępny poniżej:

```sql
CREATE TABLE dbo.DimProduct_upsert
WITH
(   Distribution=HASH(ProductKey)
,   CLUSTERED INDEX (ProductKey)
)
AS -- Select Data you wish to keep
SELECT     p.ProductKey
,          p.EnglishProductName
,          p.Color
FROM       dbo.DimProduct p
RIGHT JOIN dbo.stg_DimProduct s
ON         p.ProductKey = s.ProductKey
;

RENAME OBJECT dbo.DimProduct        TO DimProduct_old;
RENAME OBJECT dbo.DimProduct_upsert TO DimProduct;
```

## <a name="replace-merge-statements"></a>Zastąp instrukcjach merge
Instrukcje scalania można zastąpić, co najmniej w części, za pomocą CTAS. Można skonsolidować INSERT i UPDATE w jednej instrukcji. Rekordy usunięte musi być zamknięte w drugim instrukcji.

Poniżej przedstawiono przykład UPSERT:

```sql
CREATE TABLE dbo.[DimProduct_upsert]
WITH
(   DISTRIBUTION = HASH([ProductKey])
,   CLUSTERED INDEX ([ProductKey])
)
AS
-- New rows and new versions of rows
SELECT      s.[ProductKey]
,           s.[EnglishProductName]
,           s.[Color]
FROM      dbo.[stg_DimProduct] AS s
UNION ALL  
-- Keep rows that are not being touched
SELECT      p.[ProductKey]
,           p.[EnglishProductName]
,           p.[Color]
FROM      dbo.[DimProduct] AS p
WHERE NOT EXISTS
(   SELECT  *
    FROM    [dbo].[stg_DimProduct] s
    WHERE   s.[ProductKey] = p.[ProductKey]
)
;

RENAME OBJECT dbo.[DimProduct]          TO [DimProduct_old];
RENAME OBJECT dbo.[DimpProduct_upsert]  TO [DimProduct];

```

## <a name="ctas-recommendation-explicitly-state-data-type-and-nullability-of-output"></a>Zalecenie CTAS: jawnie określać typ danych i dopuszczanie wartości null dla danych wyjściowych
Podczas migrowania kod może się okazać, czy uruchomione przez ten typ kodowania wzorca:

```sql
DECLARE @d decimal(7,2) = 85.455
,       @f float(24)    = 85.455

CREATE TABLE result
(result DECIMAL(7,2) NOT NULL
)
WITH (DISTRIBUTION = ROUND_ROBIN)

INSERT INTO result
SELECT @d*@f
;
```

Instynktownie może traktować, należy zainstalować ten kod CTAS, a użytkownik będzie poprawna. Istnieje jednak jest ukrytym problem.

Następujący kod nie uzyskanie takiego samego wyniku:

```sql
DECLARE @d decimal(7,2) = 85.455
,       @f float(24)    = 85.455
;

CREATE TABLE ctas_r
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT @d*@f as result
;
```

Zwróć uwagę, kolumna "wynik" przenosi do przodu danych typu i dopuszczanie wartości null wartości wyrażenia. Może to prowadzić do niewielkie różnice w wartości, jeśli nie są dokładne.

Spróbuj wykonać następujące czynności, na przykład:

```sql
SELECT result,result*@d
from result
;

SELECT result,result*@d
from ctas_r
;
```

Wartość przechowywana dla wyniku jest inny. Zgodnie z utrwalonego wartości w kolumnie wyników jest używana w innych wyrażeniach błąd staje się jeszcze bardziej znaczące.

![Wyniki CTAS](media/sql-data-warehouse-develop-ctas/ctas-results.png)

Jest to szczególnie ważne w przypadku migracji danych. Mimo że drugiego zapytania jest raczej dokładniejsze występuje problem. Dane będą różne w porównaniu z systemem źródłowym i powodująca pytania integralności w procesie migracji. Jest to jeden z tych rzadkich przypadkach, gdy "nieprawidłowe" odpowiedzi jest rzeczywiście właściwy!

Z powodu widzimy tej różnicy między dwoma wynikami jest do rzutowania typu niejawnego. W pierwszym przykładzie tabela definiuje definicji kolumny. Gdy zostanie wstawiona występuje niejawna konwersja typu. W drugim przykładzie nie jest typu niejawna konwersja jako wyrażenie definiuje typ danych kolumny. Należy zauważyć, że kolumny w drugim przykładzie została zdefiniowana jako kolumny wartości null należy w pierwszym przykładzie nie. Podczas tworzenia tabeli w pierwszym dopuszczania wartości Null kolumny przykład został jawnie zdefiniowany. W drugim przykładzie, który został właśnie pozostawiany wyrażeniu i domyślnie to spowoduje definicji wartości NULL.  

Aby rozwiązać te problemy musi jawnie ustawione w części SELECT instrukcji CTAS konwersji typu i dopuszczanie wartości null. Te właściwości nie można ustawić w części Tworzenie tabeli.

W poniższym przykładzie pokazano, jak poprawić kod:

```sql
DECLARE @d decimal(7,2) = 85.455
,       @f float(24)    = 85.455

CREATE TABLE ctas_r
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT ISNULL(CAST(@d*@f AS DECIMAL(7,2)),0) as result
```

Pamiętaj o następujących kwestiach:

* CAST lub CONVERT może zostały już użyte
* Można wymusić dopuszczania wartości null nie ŁĄCZONEJ ISNULL
* ISNULL jest najbardziej zewnętrzną funkcję
* Druga część ISNULL jest stałą tj. 0

> [!NOTE]
> Dla dopuszczania wartości null poprawnie można ustawić jest niezbędne do użycia, ISNULL i COALESCE nie. ŁĄCZONEJ nie jest deterministyczna funkcji i dlatego wynikiem wyrażenia zawsze będzie NULLable. ISNULL różni się. Jest ona deterministyczna. W związku z tym, gdy druga część funkcji ISNULL jest stałą lub literału, a następnie wartość wynikową będzie NOT NULL.
> 
> 

Tej porady nie jest przydatne tylko w celu zapewnienia integralności obliczeń. Należy również do przełączenia partycji tabeli. Załóżmy, że w tej tabeli zdefiniowany jako Twoje fakt:

```sql
CREATE TABLE [dbo].[Sales]
(
    [date]      INT     NOT NULL
,   [product]   INT     NOT NULL
,   [store]     INT     NOT NULL
,   [quantity]  INT     NOT NULL
,   [price]     MONEY   NOT NULL
,   [amount]    MONEY   NOT NULL
)
WITH
(   DISTRIBUTION = HASH([product])
,   PARTITION   (   [date] RANGE RIGHT FOR VALUES
                    (20000101,20010101,20020101
                    ,20030101,20040101,20050101
                    )
                )
)
;
```

Jednak w polu wartość jest wyrażenie obliczeniowej nie jest on częścią źródła danych.

Aby utworzyć partycjonowanej zestawu danych może być w tym celu:

```sql
CREATE TABLE [dbo].[Sales_in]
WITH    
(   DISTRIBUTION = HASH([product])
,   PARTITION   (   [date] RANGE RIGHT FOR VALUES
                    (20000101,20010101
                    )
                )
)
AS
SELECT
    [date]    
,   [product]
,   [store]
,   [quantity]
,   [price]   
,   [quantity]*[price]  AS [amount]
FROM [stg].[source]
OPTION (LABEL = 'CTAS : Partition IN table : Create')
;
```

Zapytanie może działać dokładnie poprawnie. Problem jest dostarczany podczas próby wykonania przełącznik partycji. Definicje tabel są zgodne. Aby definicji tabeli, zgodna CTAS ma zostać zmodyfikowana.

```sql
CREATE TABLE [dbo].[Sales_in]
WITH    
(   DISTRIBUTION = HASH([product])
,   PARTITION   (   [date] RANGE RIGHT FOR VALUES
                    (20000101,20010101
                    )
                )
)
AS
SELECT
    [date]    
,   [product]
,   [store]
,   [quantity]
,   [price]   
,   ISNULL(CAST([quantity]*[price] AS MONEY),0) AS [amount]
FROM [stg].[source]
OPTION (LABEL = 'CTAS : Partition IN table : Create');
```

Widać w związku z tym, że typ spójności i utrzymywanie właściwości dopuszczania wartości null na CTAS dobrej engineering najlepszym rozwiązaniem jest. Umożliwia zachowanie spójności w obliczeniach, a także zapewnia, że możliwe jest przełączanie partycji.

Zapoznaj się z [CTAS](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) dokumentacji. Jest jednym z najważniejszych instrukcje w usłudze Azure SQL Data Warehouse. Upewnij się, że rozumiesz dokładnie.

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej porad programistycznych, zobacz [omówienie tworzenia](sql-data-warehouse-overview-develop.md).

