---
title: Utwórz TABLE AS SELECT (CTAS) w usłudze Azure SQL Data Warehouse | Dokumentacja firmy Microsoft
description: Objaśnienie i przykłady instrukcji CREATE TABLE AS SELECT (CTAS) w usłudze Azure SQL Data Warehouse związane z opracowywaniem rozwiązań.
services: sql-data-warehouse
author: XiaoyuL-Preview
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 03/26/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seoapril2019
ms.openlocfilehash: 91de474cc0610099b4264cc6d0dfbd26e8df0618
ms.sourcegitcommit: 16cb78a0766f9b3efbaf12426519ddab2774b815
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/17/2019
ms.locfileid: "65851448"
---
# <a name="create-table-as-select-ctas-in-azure-sql-data-warehouse"></a>Utwórz TABLE AS SELECT (CTAS) w usłudze Azure SQL Data Warehouse

W tym artykule opisano instrukcji CREATE TABLE AS SELECT (CTAS) T-SQL w usłudze Azure SQL Data Warehouse do opracowywania rozwiązań. Artykuł zawiera także przykłady kodu.

## <a name="create-table-as-select"></a>UTWÓRZ TABLE AS SELECT

[CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) instrukcji (CTAS) jest jednym z najważniejszych dostępne funkcje języka T-SQL. CTAS jest równolegle operacja, która tworzy nową tabelę, w oparciu o dane wyjściowe w instrukcji SELECT. CTAS to najprostszy i najszybszy sposób tworzenia i wstawiania danych do tabeli za pomocą jednego polecenia.

## <a name="selectinto-vs-ctas"></a>WYBIERZ... DO programu vs. CTAS

CTAS to wersja szersze [wybierz... DO](/sql/t-sql/queries/select-into-clause-transact-sql) instrukcji.

Oto przykład prostego wybierz... DO:

```sql
SELECT *
INTO    [dbo].[FactInternetSales_new]
FROM    [dbo].[FactInternetSales]
```

WYBIERZ... DO nie zezwala na zmianę metody dystrybucji lub typ indeksu w ramach operacji. Możesz utworzyć `[dbo].[FactInternetSales_new]` przy użyciu domyślnego typu dystrybucji ROUND_ROBIN i domyślnej struktury tabeli KLASTROWANEGO indeksu magazynu kolumn.

Z drugiej strony, za pomocą instrukcji CTAS, można określić rozkład danych tabeli, a także typ struktury tabeli. Aby skonwertować z poprzedniego przykładu, aby CTAS:

```sql
CREATE TABLE [dbo].[FactInternetSales_new]
WITH
(
    DISTRIBUTION = ROUND_ROBIN
   ,CLUSTERED COLUMNSTORE INDEX
)
AS
SELECT  *
FROM    [dbo].[FactInternetSales]
;
```

> [!NOTE]
> Jeśli próbujesz tylko zmienić indeksu w instrukcji CTAS operacji, a tabela źródłowa jest dystrybuowane wyznaczania wartości skrótu, należy zachować ten sam typ kolumny i danych dystrybucji. Pozwala to uniknąć przenoszenia danych między dystrybucji podczas operacji, która jest bardziej wydajne.

## <a name="use-ctas-to-copy-a-table"></a>Kopiowanie tabeli za pomocą instrukcji CTAS

Może być jednym z najbardziej typowych zastosowań CTAS tworzy kopię tabeli Aby zmienić DDL. Teraz załóżmy, że zostały pierwotnie utworzone tabeli jako `ROUND_ROBIN`, a teraz chcą ją zmienić na tabeli dystrybucji według kolumny. CTAS jest o tym, jak należy zmienić kolumny dystrybucji. CTAS umożliwia również zmienić typy partycjonowania, indeksowania lub kolumny.

Teraz załóżmy, że ta tabela jest tworzone za pomocą domyślny typ dystrybucji `ROUND_ROBIN`, nie określając kolumny dystrybucji `CREATE TABLE`.

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

Teraz chcesz utworzyć nową kopię tej tabeli przy użyciu `Clustered Columnstore Index`, dzięki czemu możesz korzystać z zalet wydajności tabel klastrowanego magazynu kolumn. Użytkownik chce również dystrybucji w tej tabeli na `ProductKey`, ponieważ są one przewidywanie sprzężenia w tej kolumnie i chcesz uniknąć przenoszenia danych podczas sprzężeń na `ProductKey`. Ponadto chcesz także dodać partycjonowania na `OrderDateKey`, dzięki czemu można szybko usunąć stare dane przez usunięcie starych partycji. Oto instrukcji CTAS, która kopiuje tabeli starej do nowej tabeli.

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

Na koniec możesz zmienić nazwy tabel, aby zamienić w nowej tabeli, a następnie upuść stare tabeli.

```sql
RENAME OBJECT FactInternetSales TO FactInternetSales_old;
RENAME OBJECT FactInternetSales_new TO FactInternetSales;

DROP TABLE FactInternetSales_old;
```

## <a name="use-ctas-to-work-around-unsupported-features"></a>Użycie instrukcji CTAS w celu obejścia nieobsługiwane funkcje

Umożliwia także CTAS w celu obejścia liczba nieobsługiwanych funkcji wymienionych poniżej. Ta metoda często może okazać się pomocne, ponieważ nie tylko Twój kod będzie zgodne, ale będzie często działają szybciej w usłudze SQL Data Warehouse. To wydajność jest wynikiem jego w pełni równoległego projekt. Scenariusze obejmują:

* SPRZĘŻENIA ANSI w aktualizacji
* Sprzężenia ANSI w usuwaniu
* SCALANIE — instrukcja

> [!TIP]
> Pomyśl "CTAS pierwszy." Rozwiązywanie problemów za pomocą instrukcji CTAS ogólnie jest dobra metoda, nawet jeśli piszesz większej ilości danych w wyniku.

## <a name="ansi-join-replacement-for-update-statements"></a>Zastąpienie sprzężenia ANSI dla instrukcji update

Może się okazać, że masz złożone aktualizacji. Aktualizacja łączy więcej niż dwie tabele ze sobą przy użyciu składni klauzuli join ANSI do wykonania, UPDATE lub DELETE.

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

Oryginalne zapytanie może być sprawdzono, podobny do tego przykładu:

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

Usługa SQL Data Warehouse nie obsługuje sprzężeń ANSI w `FROM` klauzuli `UPDATE` instrukcji, więc nie możesz użyć poprzedniego przykładu bez modyfikowania go.

Możesz użyć kombinacji instrukcji CTAS sprzężenie niejawne, aby zamienić w poprzednim przykładzie:

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

## <a name="ansi-join-replacement-for-delete-statements"></a>Zastępuje sprzężenia ANSI usuwania instrukcji

Czasami najlepszym podejściem do usuwania danych jest użycie instrukcji CTAS, szczególnie w przypadku `DELETE` instrukcji używających ANSI Dołącz składni. Jest to spowodowane SQL Data Warehouse nie obsługuje sprzężeń ANSI w `FROM` klauzuli `DELETE` instrukcji. Zamiast usuwania danych, należy wybrać dane, które chcesz zachować.

Oto przykład przekonwertowanego `DELETE` instrukcji:

```sql
CREATE TABLE dbo.DimProduct_upsert
WITH
(   Distribution=HASH(ProductKey)
,   CLUSTERED INDEX (ProductKey)
)
AS -- Select Data you want to keep
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

## <a name="replace-merge-statements"></a>Zastąp instrukcje merge

Możesz zastąpić instrukcjach merge przynajmniej część, za pomocą instrukcji CTAS. Można połączyć `INSERT` i `UPDATE` w pojedynczej instrukcji. Powinny być ograniczone rekordów usuniętych z `SELECT` instrukcję, aby pominąć w wynikach.

Poniższy przykład dotyczy programu `UPSERT`:

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
RENAME OBJECT dbo.[DimProduct_upsert]  TO [DimProduct];
```

## <a name="explicitly-state-data-type-and-nullability-of-output"></a>Jawnie określać typ danych i dopuszczanie wartości null dla danych wyjściowych

Gdy migracja kodu, może się okazać działają w tego typu wzorca kodowania:

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

Może się wydawać, należy zainstalować ten kod CTAS i może być poprawne. Jednak jest to ukryty problem.

Poniższy kod nie uzyskanie takiego samego wyniku:

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

Zwróć uwagę, kolumna "wynik" prowadzi do przodu danych typu i dopuszczanie wartości null wartości wyrażenia. Typ do przodu wykonywania danych może prowadzić do subtelne odchyleń wartości, jeśli nie chcesz zachować ostrożność.

Wypróbuj następująco:

```sql
SELECT result,result*@d
from result
;

SELECT result,result*@d
from ctas_r
;
```

Różni się do wartości przechowywanej na wynik. Ponieważ utrwalonej wartości w kolumnie wyników jest używane w innych wyrażeniach, błąd staje się bardziej znaczące.

![Zrzut ekranu CTAS wyników](media/sql-data-warehouse-develop-ctas/ctas-results.png)

Jest to ważne w przypadku migracji danych. Mimo że drugiego zapytania jest prawdopodobnie bardziej precyzyjne, występuje problem. Dane będą różnić się w porównaniu z systemem źródłowym, a która prowadzi do pytania integralności w procesie migracji. Jest to jedna z tych rzadkich przypadkach, gdzie "nieprawidłowy" odpowiedź jest faktycznie właściwy!

Z powodu widzimy różnic między dwoma wynikami wynika z niejawnego typu rzutowania. W pierwszym przykładzie tabela definiuje definicji kolumny. Gdy zostanie wstawiona występuje niejawna konwersja typu. W drugim przykładzie istnieje nie niejawna konwersja typu wyrażenie definiuje typ danych kolumny.

Zauważ również, że kolumny w drugim przykładzie został zdefiniowany jako kolumnę dopuszczającą wartości null, w pierwszym przykładzie ma ją nie. Podczas tworzenia tabeli w pierwszym przykładzie dopuszczania wartości null w kolumnie został jawnie zdefiniowany. W drugim przykładzie go pozostawało na wyrażenie, a domyślnie mogłoby spowodować definicję o wartości NULL.

Aby uniknąć tych problemów, należy jawnie ustawione w części wybierz instrukcji CTAS konwersji typu i dopuszczanie wartości null. Nie można ustawić te właściwości "CREATE TABLE".
Poniższy przykład pokazuje, jak naprawić kod:

```sql
DECLARE @d decimal(7,2) = 85.455
,       @f float(24)    = 85.455

CREATE TABLE ctas_r
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT ISNULL(CAST(@d*@f AS DECIMAL(7,2)),0) as result
```

Pamiętaj o następujących kwestiach:

* Możesz użyć RZUTOWANIA lub PRZEKONWERTOWAĆ.
* ISNULL, COALESCE nie umożliwia wymuszenie dopuszczania wartości null. Zobacz następujące uwagi.
* ISNULL jest najbardziej zewnętrzną funkcję.
* Druga część ISNULL jest stałą, 0.

> [!NOTE]
> Dopuszczanie wartości null należy poprawnie ustawić jest niezbędna do użycia, ISNULL i nie COALESCE. COALESCE nie jest funkcją deterministyczna, a więc wynikiem wyrażenia zawsze będzie NULLable. ISNULL różni się. Jest ona deterministyczna. W związku z tym, gdy druga część funkcji ISNULL jest stałą lub literałem, wartość wynikowa będzie NOT NULL.

Zapewnianie integralności obliczeniach jest również ważne dla przełączanie partycji tabeli. Załóżmy, że ta tabela jest zdefiniowany jako tabela faktów:

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

Jednak pola to wyrażenie obliczeniowe. Nie jest częścią źródła danych.

Aby utworzyć zestaw danych partycjonowanych, warto użyć następującego kodu:

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

Zapytanie może również działać. Problem jest dostarczany, gdy spróbujesz przeprowadzić przełączenia partycji. Definicje tabel nie są zgodne. Zapewnienie definicje tabel dopasowania, zmodyfikuj CTAS, aby dodać `ISNULL` funkcję, aby zachować atrybutu dopuszczania wartości null w kolumnie.

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

Widać, że typ spójności i utrzymywanie właściwości dopuszczania wartości null w instrukcji CTAS jest najlepszą praktykę. Pomaga w utrzymaniu spójności w obliczeniach i gwarantuje również, że możliwe jest przełączanie partycji.

CTAS jest jednym z najważniejszych oświadczeń w usłudze SQL Data Warehouse. Upewnij się, że dokładnie zrozumieć. Zobacz [dokumentacji CTAS](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse).

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej porad programistycznych, zobacz [omówienie programowania w usłudze](sql-data-warehouse-overview-develop.md).

