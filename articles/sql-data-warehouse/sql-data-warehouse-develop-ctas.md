---
title: Utwórz TABLE AS SELECT (CTAS) w usłudze Azure SQL Data Warehouse | Dokumentacja firmy Microsoft
description: Objaśnienie i przykłady instrukcji CREATE TABLE AS SELECT (CTAS) w usłudze Azure SQL Data Warehouse do opracowywania rozwiązań.
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: implement
ms.date: 03/26/2019
ms.author: mlee3gsd
ms.reviewer: jrasnick
ms.custom: seoapril2019
ms.openlocfilehash: ea95a13277927b485bb9da3b75b84cce4337bf88
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2019
ms.locfileid: "59280438"
---
# <a name="create-table-as-select-ctas-in-azure-sql-data-warehouse"></a>Utwórz TABLE AS SELECT (CTAS) w usłudze Azure SQL Data Warehouse

Objaśnienie i przykłady kodowania za pomocą instrukcji CREATE TABLE AS SELECT (CTAS) T-SQL w usłudze Azure SQL Data Warehouse do opracowywania rozwiązań.

## <a name="create-table-as-select-ctas"></a>CREATE TABLE AS SELECT (CTAS)

[CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) lub instrukcji CTAS jest jednym z najważniejszych dostępne funkcje języka T-SQL. CTAS jest równolegle operacja, która tworzy nową tabelę, w oparciu o dane wyjściowe w instrukcji SELECT. CTAS to najprostszy i najszybszy sposób tworzenia i wstawiania danych do tabeli za pomocą jednego polecenia.

## <a name="selectinto-vs-ctas"></a>WYBIERZ... DO programu vs. CTAS

CTAS jest bardzo płatne wersją [wybierz... DO](/sql/t-sql/queries/select-into-clause-transact-sql) instrukcji.

Poniżej znajduje się przykład proste wybierz... DO:

```sql
SELECT *
INTO    [dbo].[FactInternetSales_new]
FROM    [dbo].[FactInternetSales]
```

WYBIERZ... DO nie umożliwiają zmianę metody dystrybucji lub typ indeksu w ramach operacji. `[dbo].[FactInternetSales_new]` jest tworzona przy użyciu domyślnego typu dystrybucji ROUND_ROBIN i domyślnej struktury tabeli jako KLASTROWANY indeks magazynu kolumn.

Rozkład danych tabeli, a także strukturę tabeli za pomocą instrukcji CTAS będą mogli podać obydwie wartości typu. Aby skonwertować z poprzedniego przykładu, aby CTAS:

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
> Jeśli tylko chcesz zmienić indeksu w swojej `CTAS` operacji i tabeli źródłowej jest skrót rozproszonych wówczas `CTAS` operacji działają najlepiej, jeśli zachować ten sam typ kolumny i danych dystrybucji. Pozwoli to uniknąć krzyżowe przenoszenia danych dystrybucji podczas operacji, która jest bardziej wydajne.

## <a name="using-ctas-to-copy-a-table"></a>Kopiowanie tabeli przy użyciu instrukcji CTAS

Może być jedną z najbardziej typowych zastosowań `CTAS` jest utworzenie kopii tabeli. Aby zmienić DDL. Jeśli na przykład pierwotnie utworzono tabeli jako `ROUND_ROBIN` , a teraz chcą ją zmienić na tabeli dystrybucji według kolumny, `CTAS` się, jak należy zmienić kolumny dystrybucji. `CTAS` można również zmienić typy partycjonowania, indeksowania lub kolumny.

Załóżmy, że utworzone w tej tabeli przy użyciu domyślnego typu dystrybucji `ROUND_ROBIN` , nie określając kolumny dystrybucji `CREATE TABLE`.

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

Teraz chcesz utworzyć nową kopię tej tabeli na klastrowany indeks magazynu kolumn, tak aby można było korzystać z wydajności tabel klastrowanego magazynu kolumn. Ponadto chcesz dystrybuować tej tabeli na klucz produktu, ponieważ są przewidywania sprzężenia w tej kolumnie i aby uniknąć przenoszenia danych podczas sprzężenia w ProductKey. Na koniec chcesz także dodać partycjonowania na OrderDateKey, dzięki czemu można szybko usunąć stare dane przez usunięcie starych partycji. Oto instrukcji CTAS, co może spowodować skopiowanie tabeli starej do nowej tabeli.

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

Na koniec możesz zmienić nazwy tabel do wymiany w nowej tabeli, a następnie upuść stare tabeli.

```sql
RENAME OBJECT FactInternetSales TO FactInternetSales_old;
RENAME OBJECT FactInternetSales_new TO FactInternetSales;

DROP TABLE FactInternetSales_old;
```

## <a name="using-ctas-to-work-around-unsupported-features"></a>Użycie instrukcji CTAS w celu obejścia nieobsługiwane funkcje

CTAS można również obejść liczba nieobsługiwanych funkcji wymienionych poniżej. Ta metoda może często udowodnić, że staje się sytuacji win/win ponieważ nie tylko Twój kod będzie zgodne, ale będą często wykonywane szybciej w usłudze SQL Data Warehouse. To wydajność jest wynikiem jego w pełni równoległego projekt. Scenariusze, które mogą wykonywać wokół za pomocą instrukcji CTAS obejmują:

* SPRZĘŻENIA ANSI w aktualizacji
* Sprzężenia ANSI w usuwaniu
* SCALANIE — instrukcja

> [!NOTE]
> Pomyśl "CTAS pierwszy". Rozwiązywanie problemów przy użyciu `CTAS` ogólnie to dobra metoda, nawet wtedy, gdy w wyniku pisania większej ilości danych.

## <a name="ansi-join-replacement-for-update-statements"></a>Zastąpienie sprzężenia ANSI dla instrukcji update

Może okazać się, że masz złożone aktualizację, która dołącza więcej niż dwie tabele ze sobą przy użyciu składni klauzuli join ANSI do wykonania, UPDATE lub DELETE.

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

Można użyć kombinacji `CTAS` i niejawne sprzężenia w celu zastąpienia poprzedniego przykładu:

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

Czasami najlepszym podejściem do usuwania danych jest użycie `CTAS`, szczególnie w przypadku `DELETE` instrukcji używających ANSI Dołącz składni. Jest to spowodowane SQL Data Warehouse nie obsługuje sprzężeń ANSI w `FROM` klauzuli `DELETE` instrukcji. Zamiast usuwania danych, należy wybrać dane, które chcesz zachować.

Przykładem przekonwertowanego instrukcji DELETE jest dostępne poniżej:

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

## <a name="replace-merge-statements"></a>Zastąp instrukcje merge

Instrukcje merge można zastąpić, co najmniej w części, za pomocą instrukcji CTAS. INSERT i UPDATE można łączyć w pojedynczej instrukcji. Powinny być ograniczone rekordów usuniętych z `SELECT` instrukcję, aby pominąć w wynikach.

Poniższy przykład jest UPSERT:

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

## <a name="ctas-recommendation-explicitly-state-data-type-and-nullability-of-output"></a>Zalecenie CTAS: Jawnie określać typ danych i dopuszczanie wartości null dla danych wyjściowych

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

Instynktownie może się wydawać, ten kod należy zmigrować do instrukcji CTAS i może być poprawne. Jednak jest to ukryty problem.

Poniższy kod nie daje ten sam wynik:

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

![Wyniki instrukcji CTAS](media/sql-data-warehouse-develop-ctas/ctas-results.png)

Jest to ważne w przypadku migracji danych. Mimo że drugiego zapytania jest prawdopodobnie bardziej precyzyjne występuje problem. Dane będą różnić się w porównaniu z systemem źródłowym i która prowadzi do pytania integralności w procesie migracji. Jest to jedna z tych rzadkich przypadkach, gdzie "nieprawidłowy" odpowiedź jest faktycznie właściwy!

Z powodu widzimy różnic między dwoma wynikami wynika z niejawnego typu rzutowania. W pierwszym przykładzie tabela definiuje definicji kolumny. Gdy zostanie wstawiona występuje niejawna konwersja typu. W drugim przykładzie istnieje nie niejawna konwersja typu wyrażenie definiuje typ danych kolumny. Należy zauważyć, że kolumny w drugim przykładzie został zdefiniowany jako kolumnę dopuszczającą wartości null w pierwszym przykładzie ma ją nie. Podczas tworzenia tabeli w pierwszym przykładzie dopuszczania wartości null w kolumnie został jawnie zdefiniowany. W drugim przykładzie pozostawało na wyrażenie i domyślnie, mogłoby spowodować definicję o wartości NULL.

Aby uniknąć tych problemów, należy jawnie ustawione w części wybierz instrukcji CTAS konwersji typu i dopuszczanie wartości null. Utwórz część tabeli nie można ustawić te właściwości.

W poniższym przykładzie pokazano, jak naprawić kod:

```sql
DECLARE @d decimal(7,2) = 85.455
,       @f float(24)    = 85.455

CREATE TABLE ctas_r
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT ISNULL(CAST(@d*@f AS DECIMAL(7,2)),0) as result
```

Pamiętaj o następujących kwestiach:

* Polecenie CAST lub CONVERT może zostały użyte
* ISNULL, można wymusić dopuszczania wartości null nie ŁĄCZONEJ.
* ISNULL jest najbardziej zewnętrzną funkcję
* Druga część ISNULL jest stałą, 0

> [!NOTE]
> Dla dopuszczania wartości null poprawnie można ustawić jest niezbędna do użycia, ISNULL i nie COALESCE. COALESCE nie jest funkcją deterministyczna, a więc wynikiem wyrażenia zawsze będzie NULLable. ISNULL różni się. Jest ona deterministyczna. W związku z tym, gdy druga część funkcji ISNULL jest stałą lub literału, a następnie wartość wynikowa będzie NOT NULL.

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

Jednak pola jest wyrażeniem obliczeniowe; nie jest częścią źródła danych.

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

Zapytanie może działać doskonale w dobrym stanie. Problem jest dostarczany, gdy spróbujesz przeprowadzić przełączenia partycji. Definicje tabel są niezgodne. Aby dopasować definicje tabel, należy zmodyfikować, aby dodać CTAS `ISNULL` funkcję, aby zachować atrybutu dopuszczania wartości null w kolumnie.

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

Aby zobaczyć, czy typ spójności i utrzymywanie właściwości dopuszczania wartości null w instrukcji CTAS jest dobrą praktyką najlepszych inżynierów. Ona pomóc w utrzymaniu spójności w obliczeniach i gwarantuje również, że możliwe jest przełączanie partycji.

Zapoznaj się [CTAS](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) dokumentacji. CTAS jest jednym z najważniejszych instrukcji w usłudze Azure SQL Data Warehouse. Upewnij się, że dokładnie zrozumieć.

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej porad programistycznych, zobacz [omówienie programowania w usłudze](sql-data-warehouse-overview-develop.md).

