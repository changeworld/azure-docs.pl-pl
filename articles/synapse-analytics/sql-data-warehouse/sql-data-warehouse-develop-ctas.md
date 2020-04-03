---
title: TWORZENIE TABELI JAKO WYBIERZ (CTAS)
description: Wyjaśnienie i przykłady instrukcji CREATE TABLE AS SELECT (CTAS) w Synapse SQL do tworzenia rozwiązań.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 03/26/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seoapril2019, azure-synapse
ms.openlocfilehash: e5dc8835a6d5f235cf269edd4e9f069c904e1b7e
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80582147"
---
# <a name="create-table-as-select-ctas"></a>TWORZENIE TABELI JAKO WYBIERZ (CTAS)

W tym artykule opisano instrukcję T-SQL CREATE TABLE AS SELECT (CTAS) w języku Synapse SQL dotyczącą opracowywania rozwiązań. Artykuł zawiera również przykłady kodu.

## <a name="create-table-as-select"></a>CREATE TABLE AS SELECT

Instrukcja [CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) (CTAS) jest jedną z najważniejszych dostępnych funkcji T-SQL. CTAS jest operacją równoległą, która tworzy nową tabelę na podstawie danych wyjściowych instrukcji SELECT. CTAS to najprostszy i najszybszy sposób tworzenia i wstawiania danych do tabeli za pomocą jednego polecenia.

## <a name="selectinto-vs-ctas"></a>Wybierz... INTO vs. CTAS

CTAS jest bardziej konfigurowalny wersja [SELECT ... INSTRUKCJA INTO.](/sql/t-sql/queries/select-into-clause-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

Poniżej znajduje się przykład prostego SELECT... Do:

```sql
SELECT *
INTO    [dbo].[FactInternetSales_new]
FROM    [dbo].[FactInternetSales]
```

Wybierz... INTO nie pozwala na zmianę metody dystrybucji lub typu indeksu w ramach operacji. Tworzenie `[dbo].[FactInternetSales_new]` przy użyciu domyślnego typu dystrybucji ROUND_ROBIN i domyślnej struktury tabeli INDEKSU KLASTROWANEGO MAGAZYNU KOLUMN.

Z CTAS, z drugiej strony, można określić zarówno rozkład danych tabeli, jak również typ struktury tabeli. Aby przekonwertować poprzedni przykład na CTAS:

```sql
CREATE TABLE [dbo].[FactInternetSales_new]
WITH
(
 DISTRIBUTION = ROUND_ROBIN
 ,CLUSTERED COLUMNSTORE INDEX
)
AS
SELECT  *
FROM    [dbo].[FactInternetSales];
```

> [!NOTE]
> Jeśli próbujesz tylko zmienić indeks w operacji CTAS, a tabela źródłowa jest rozproszona mieszania, należy zachować tę samą kolumnę dystrybucji i typ danych. Pozwala to uniknąć przenoszenia danych między dystrybucją podczas operacji, która jest bardziej wydajna.

## <a name="use-ctas-to-copy-a-table"></a>Kopiowanie tabeli za pomocą CTAS

Być może jednym z najczęstszych zastosowań CTAS jest tworzenie kopii tabeli w celu zmiany DDL. Załóżmy, że pierwotnie utworzono tabelę jako `ROUND_ROBIN`, a teraz chcesz ją zmienić na tabelę rozmieszczona w kolumnie. CTAS jest jak można zmienić kolumnę dystrybucji. CTAS można również użyć do zmiany partycjonowania, indeksowania lub typów kolumn.

Załóżmy, że ta tabela została utworzona `ROUND_ROBIN`przy użyciu domyślnego typu `CREATE TABLE`dystrybucji , nie określając kolumny dystrybucyjnej w pliku .

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
    CustomerPONumber nvarchar(25));
```

Teraz chcesz utworzyć nową kopię tej tabeli, z programem `Clustered Columnstore Index`, dzięki czemu można korzystać z wydajności tabel magazynu kolumn klastrowanych. Chcesz również rozesłać `ProductKey`tę tabelę na , ponieważ przewidujesz sprzężenia w tej `ProductKey`kolumnie i chcesz uniknąć przenoszenia danych podczas sprzężenia na . Nareszcie, chcesz również dodać partycjonowanie na `OrderDateKey`, dzięki czemu można szybko usunąć stare dane, upuszczając stare partycje. Oto instrukcja CTAS, która kopiuje starą tabelę do nowej tabeli.

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

Na koniec możesz zmienić nazwę tabel, aby zamienić nową tabelę, a następnie upuścić stary stół.

```sql
RENAME OBJECT FactInternetSales TO FactInternetSales_old;
RENAME OBJECT FactInternetSales_new TO FactInternetSales;

DROP TABLE FactInternetSales_old;
```

## <a name="use-ctas-to-work-around-unsupported-features"></a>Praca nad nieobsługiowanymi funkcjami za pomocą ctas

Można również użyć CTAS do obejść szereg nieobsługiwał funkcje wymienione poniżej. Ta metoda często może okazać się przydatne, ponieważ nie tylko kod będzie zgodny, ale często będzie działać szybciej na Synapse SQL. Ta wydajność jest wynikiem jego w pełni równoległej konstrukcji. Scenariusze obejmują:

* ANSI JOINS na updates
* FON ANSI na deletes
* INSTRUKCJA SCALANIE

> [!TIP]
> Spróbuj myśleć "CTAS pierwszy." Rozwiązywanie problemu za pomocą CTAS jest ogólnie dobrym podejściem, nawet jeśli piszesz więcej danych w wyniku.

## <a name="ansi-join-replacement-for-update-statements"></a>Ansi dołączyć zastąpienie instrukcji aktualizacji

Może się okazać, że masz złożoną aktualizację. Aktualizacja łączy więcej niż dwie tabele razem przy użyciu składni sprzężenia ANSI do wykonywania UPDATE lub DELETE.

Wyobraź sobie, że musiałeś zaktualizować tę tabelę:

```sql
CREATE TABLE [dbo].[AnnualCategorySales]
( [EnglishProductCategoryName]    NVARCHAR(50)    NOT NULL
, [CalendarYear]                    SMALLINT        NOT NULL
, [TotalSalesAmount]                MONEY            NOT NULL
)
WITH
(
    DISTRIBUTION = ROUND_ROBIN
);
```

Oryginalna kwerenda mogła wyglądać mniej więcej w tym przykładzie:

```sql
UPDATE    acs
SET        [TotalSalesAmount] = [fis].[TotalSalesAmount]
FROM    [dbo].[AnnualCategorySales]     AS acs
JOIN    (
        SELECT [EnglishProductCategoryName]
        , [CalendarYear]
        , SUM([SalesAmount])                AS [TotalSalesAmount]
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
AND    [acs].[CalendarYear]                = [fis].[CalendarYear];
```

Synapse SQL nie obsługuje ansi sprzężenia w `FROM` klauzuli `UPDATE` instrukcji, więc nie można użyć poprzedniego przykładu bez modyfikowania go.

Można użyć kombinacji CTAS i sprzężenia niejawnego, aby zastąpić poprzedni przykład:

```sql
-- Create an interim table
CREATE TABLE CTAS_acs
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT    ISNULL(CAST([EnglishProductCategoryName] AS NVARCHAR(50)),0) AS [EnglishProductCategoryName]
, ISNULL(CAST([CalendarYear] AS SMALLINT),0)  AS [CalendarYear]
, ISNULL(CAST(SUM([SalesAmount]) AS MONEY),0)  AS [TotalSalesAmount]
FROM    [dbo].[FactInternetSales]        AS s
JOIN    [dbo].[DimDate]                    AS d    ON s.[OrderDateKey]                = d.[DateKey]
JOIN    [dbo].[DimProduct]                AS p    ON s.[ProductKey]                = p.[ProductKey]
JOIN    [dbo].[DimProductSubCategory]    AS u    ON p.[ProductSubcategoryKey]    = u.[ProductSubcategoryKey]
JOIN    [dbo].[DimProductCategory]        AS c    ON u.[ProductCategoryKey]        = c.[ProductCategoryKey]
WHERE     [CalendarYear] = 2004
GROUP BY [EnglishProductCategoryName]
, [CalendarYear];

-- Use an implicit join to perform the update
UPDATE  AnnualCategorySales
SET     AnnualCategorySales.TotalSalesAmount = CTAS_ACS.TotalSalesAmount
FROM    CTAS_acs
WHERE   CTAS_acs.[EnglishProductCategoryName] = AnnualCategorySales.[EnglishProductCategoryName]
AND     CTAS_acs.[CalendarYear]  = AnnualCategorySales.[CalendarYear] ;

--Drop the interim table
DROP TABLE CTAS_acs;
```

## <a name="ansi-join-replacement-for-delete-statements"></a>Ansi dołączyć zastąpienie instrukcji usuwania

Czasami najlepszym rozwiązaniem dla usuwania danych jest użycie `DELETE` CTAS, szczególnie w przypadku instrukcji, które używają składni sprzężenia ANSI. Dzieje się tak, ponieważ Synapse SQL nie obsługuje `FROM` ansi sprzężenia w klauzuli `DELETE` instrukcji. Zamiast usuwać dane, wybierz dane, które chcesz zachować.

Poniżej przedstawiono przykład przekonwertowanej `DELETE` instrukcji:

```sql
CREATE TABLE dbo.DimProduct_upsert
WITH
(   Distribution=HASH(ProductKey)
,   CLUSTERED INDEX (ProductKey)
)
AS -- Select Data you want to keep
SELECT p.ProductKey
, p.EnglishProductName
,  p.Color
FROM  dbo.DimProduct p
RIGHT JOIN dbo.stg_DimProduct s
ON p.ProductKey = s.ProductKey;

RENAME OBJECT dbo.DimProduct TO DimProduct_old;
RENAME OBJECT dbo.DimProduct_upsert TO DimProduct;
```

## <a name="replace-merge-statements"></a>Zastępowanie instrukcji scalania

Instrukcje scalania, przynajmniej częściowo, można zastąpić za pomocą CTAS. Można połączyć `INSERT` i `UPDATE` w jedną instrukcję. Wszelkie usunięte rekordy powinny `SELECT` być ograniczone z instrukcji, aby pominąć wyniki.

Poniższy przykład `UPSERT`dotyczy:

```sql
CREATE TABLE dbo.[DimProduct_upsert]
WITH
(   DISTRIBUTION = HASH([ProductKey])
,   CLUSTERED INDEX ([ProductKey])
)
AS
-- New rows and new versions of rows
SELECT s.[ProductKey]
, s.[EnglishProductName]
, s.[Color]
FROM      dbo.[stg_DimProduct] AS s
UNION ALL  
-- Keep rows that are not being touched
SELECT      p.[ProductKey]
, p.[EnglishProductName]
, p.[Color]
FROM      dbo.[DimProduct] AS p
WHERE NOT EXISTS
(   SELECT  *
    FROM    [dbo].[stg_DimProduct] s
    WHERE   s.[ProductKey] = p.[ProductKey]
);

RENAME OBJECT dbo.[DimProduct]          TO [DimProduct_old];
RENAME OBJECT dbo.[DimProduct_upsert]  TO [DimProduct];
```

## <a name="explicitly-state-data-type-and-nullability-of-output"></a>Jawnie podać typ danych i nieważność danych wyjściowych

Podczas migracji kodu może się okazać, że można uruchomić przez tego typu wzorca kodowania:

```sql
DECLARE @d decimal(7,2) = 85.455
,       @f float(24)    = 85.455

CREATE TABLE result
(result DECIMAL(7,2) NOT NULL
)
WITH (DISTRIBUTION = ROUND_ROBIN)

INSERT INTO result
SELECT @d*@f;
```

Może się wydawać, że należy przeprowadzić migrację tego kodu do CTAS i chcesz poprawić. Jest tu jednak ukryty problem.

Poniższy kod nie daje ten sam wynik:

```sql
DECLARE @d decimal(7,2) = 85.455
, @f float(24)    = 85.455;

CREATE TABLE ctas_r
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT @d*@f as result;
```

Należy zauważyć, że kolumna "wynik" przenosi do przodu typ danych i nullability wartości wyrażenia. Przenoszenie typu danych do przodu może prowadzić do subtelnych wariancji wartości, jeśli nie jesteś ostrożny.

Wypróbuj ten przykład:

```sql
SELECT result,result*@d
from result;

SELECT result,result*@d
from ctas_r;
```

Wartość przechowywana dla wyniku jest inna. Ponieważ wartość utrwalone w kolumnie wynik jest używany w innych wyrażeniach, błąd staje się jeszcze bardziej znaczące.

![Zrzut ekranu przedstawiający wyniki CTAS](./media/sql-data-warehouse-develop-ctas/ctas-results.png)

Jest to ważne w przypadku migracji danych. Mimo że drugie zapytanie jest prawdopodobnie bardziej dokładne, występuje problem. Dane będą różne w porównaniu do systemu źródłowego, co prowadzi do kwestii integralności w migracji. Jest to jeden z tych rzadkich przypadków, w których "zła" odpowiedź jest rzeczywiście właściwa!

Powodem widzimy rozbieżności między tymi dwoma wynikami jest ze względu na rzutowanie typu niejawnego. W pierwszym przykładzie tabela definiuje definicję kolumny. Po wstawieniu wiersza następuje konwersja typu niejawnego. W drugim przykładzie nie ma konwersji typu niejawnego, ponieważ wyrażenie definiuje typ danych kolumny.

Należy również zauważyć, że kolumna w drugim przykładzie została zdefiniowana jako NULLable kolumny, podczas gdy w pierwszym przykładzie nie ma. Podczas tworzenia tabeli w pierwszym przykładzie, nullability kolumny został jawnie zdefiniowany. W drugim przykładzie został pozostawiony do wyrażenia i domyślnie spowodowałoby definicji NULL.

Aby rozwiązać te problemy, należy jawnie ustawić konwersji typu i nullability w select części instrukcji CTAS. Nie można ustawić tych właściwości w "CREATE TABLE".
W poniższym przykładzie pokazano, jak naprawić kod:

```sql
DECLARE @d decimal(7,2) = 85.455
, @f float(24)    = 85.455

CREATE TABLE ctas_r
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT ISNULL(CAST(@d*@f AS DECIMAL(7,2)),0) as result
```

Pamiętaj o następujących kwestiach:

* Można użyć CAST lub CONVERT.
* Użyj ISNULL, nie COALESCE, aby wymusić NULLability. Zobacz następującą uwagę.
* ISNULL jest najbardziej zewnętrzną funkcją.
* Druga część ISNULL jest stała, 0.

> [!NOTE]
> Aby nullability być poprawnie ustawione, ważne jest, aby użyć ISNULL, a nie COALESCE. COALESCE nie jest funkcją deterministyczną, a więc wynikiem wyrażenia zawsze będzie NULLable. ISNULL jest inny. To deterministyczne. W związku z tym gdy druga część funkcji ISNULL jest stała lub literał, wartość wynikowa nie będzie NULL.

Zapewnienie integralności obliczeń jest również ważne dla przełączania partycji tabeli. Wyobraź sobie, że ta tabela jest zdefiniowana jako tabela faktów:

```sql
CREATE TABLE [dbo].[Sales]
(
    [date]      INT     NOT NULL
, [product]   INT     NOT NULL
, [store]     INT     NOT NULL
, [quantity]  INT     NOT NULL
, [price]     MONEY   NOT NULL
, [amount]    MONEY   NOT NULL
)
WITH
(   DISTRIBUTION = HASH([product])
,   PARTITION   (   [date] RANGE RIGHT FOR VALUES
                    (20000101,20010101,20020101
                    ,20030101,20040101,20050101
                    )
                )
);
```

Jednak pole kwoty jest wyrażeniem obliczeniowym. Nie jest częścią danych źródłowych.

Aby utworzyć podzielony na partycje zestaw danych, można użyć następującego kodu:

```sql
CREATE TABLE [dbo].[Sales_in]
WITH
( DISTRIBUTION = HASH([product])
, PARTITION   (   [date] RANGE RIGHT FOR VALUES
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
OPTION (LABEL = 'CTAS : Partition IN table : Create');
```

Zapytanie będzie działać doskonale dobrze. Problem pojawia się, gdy próbujesz zrobić przełącznik partycji. Definicje tabel nie są zgodne. Aby definicje tabeli były zgodne, zmodyfikuj CTAS, aby dodać `ISNULL` funkcję, aby zachować atrybut nullability kolumny.

```sql
CREATE TABLE [dbo].[Sales_in]
WITH
( DISTRIBUTION = HASH([product])
, PARTITION   (   [date] RANGE RIGHT FOR VALUES
                    (20000101,20010101
                    )
                )
)
AS
SELECT
  [date]
, [product]
, [store]
, [quantity]
, [price]   
, ISNULL(CAST([quantity]*[price] AS MONEY),0) AS [amount]
FROM [stg].[source]
OPTION (LABEL = 'CTAS : Partition IN table : Create');
```

Widać, że spójność typu i utrzymanie właściwości nullability na CTAS jest najlepszym rozwiązaniem inżynierii. Pomaga zachować integralność w obliczeniach, a także zapewnia, że przełączanie partycji jest możliwe.

CTAS jest jednym z najważniejszych instrukcji w Synapse SQL. Upewnij się, że dokładnie to rozumiesz. Zobacz [dokumentację CTAS](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej wskazówek dotyczących rozwoju, zobacz [omówienie rozwoju](sql-data-warehouse-overview-develop.md).

