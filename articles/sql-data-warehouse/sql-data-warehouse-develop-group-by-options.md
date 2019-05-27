---
title: Opcje w usłudze Azure SQL Data Warehouse przy użyciu grupy | Dokumentacja firmy Microsoft
description: Wskazówki dotyczące implementowania grupy przez opcje w usłudze Azure SQL Data Warehouse związane z opracowywaniem rozwiązań.
services: sql-data-warehouse
author: XiaoyuL-Preview
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: query
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: b158048929d3db8672d76027666331448a91a0a8
ms.sourcegitcommit: 4c2b9bc9cc704652cc77f33a870c4ec2d0579451
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/17/2019
ms.locfileid: "65861801"
---
# <a name="group-by-options-in-sql-data-warehouse"></a>Grupuj według opcji w usłudze SQL Data Warehouse
Wskazówki dotyczące implementowania grupy przez opcje w usłudze Azure SQL Data Warehouse związane z opracowywaniem rozwiązań.

## <a name="what-does-group-by-do"></a>Do czego służy GROUP BY?

[GROUP BY](/sql/t-sql/queries/select-group-by-transact-sql) klauzuli języka T-SQL agreguje dane podsumowania zestawu wierszy. GROUP BY zawiera niektóre opcje, które nie obsługuje usługi SQL Data Warehouse. Te opcje mają rozwiązania problemu.

Te opcje są

* Klauzula GROUP BY ZBIORCZEGO
* GROUPING SETS
* Klauzula GROUP BY modułu

## <a name="rollup-and-grouping-sets-options"></a>Ustawia opcje ROLLUP i grouping
Jest to najprostsza opcja, UNION ALL zamiast tego użyć do wykonania w pakiecie zbiorczym zamiast polegać na jawne składni. Wynikiem jest dokładnie taka sama

Poniższy przykład za pomocą instrukcji GROUP BY z opcją ZBIORCZY:
```sql
SELECT [SalesTerritoryCountry]
,      [SalesTerritoryRegion]
,      SUM(SalesAmount)             AS TotalSalesAmount
FROM  dbo.factInternetSales s
JOIN  dbo.DimSalesTerritory t       ON s.SalesTerritoryKey       = t.SalesTerritoryKey
GROUP BY ROLLUP (
                        [SalesTerritoryCountry]
                ,       [SalesTerritoryRegion]
                )
;
```

Za pomocą ZBIORCZEGO, poprzedni przykład żąda następujących agregacji:

* Krajów i regionów
* Kraj
* Suma końcowa

Aby zastąpić pakiet ZBIORCZY i zwracają takie same wyniki, można użyć UNION ALL i jawnie określić wymaganych agregacji:

```sql
SELECT [SalesTerritoryCountry]
,      [SalesTerritoryRegion]
,      SUM(SalesAmount) AS TotalSalesAmount
FROM  dbo.factInternetSales s
JOIN  dbo.DimSalesTerritory t     ON s.SalesTerritoryKey       = t.SalesTerritoryKey
GROUP BY
       [SalesTerritoryCountry]
,      [SalesTerritoryRegion]
UNION ALL
SELECT [SalesTerritoryCountry]
,      NULL
,      SUM(SalesAmount) AS TotalSalesAmount
FROM  dbo.factInternetSales s
JOIN  dbo.DimSalesTerritory t     ON s.SalesTerritoryKey       = t.SalesTerritoryKey
GROUP BY
       [SalesTerritoryCountry]
UNION ALL
SELECT NULL
,      NULL
,      SUM(SalesAmount) AS TotalSalesAmount
FROM  dbo.factInternetSales s
JOIN  dbo.DimSalesTerritory t     ON s.SalesTerritoryKey       = t.SalesTerritoryKey;
```

Aby zastąpić GROUPING SETS, zasada przykład dotyczy. Musisz utworzyć UNION ALL sekcjach poziomów agregacji, które mają być wyświetlane.

## <a name="cube-options"></a>Opcje modułu
Istnieje możliwość utworzenia grupy przez przy użyciu modułu przy użyciu podejścia UNION ALL. Problem polega na tym, że kod może szybko stać się skomplikowane i niewygodna. Aby rozwiązać ten problem, możesz użyć tego bardziej zaawansowane metody.

Użyjemy w powyższym przykładzie.

Pierwszym krokiem jest określenie "modułu" definiujący na wszystkich poziomach agregacji, które chcemy utworzyć. Należy wziąć pod uwagę CROSS JOIN tych dwóch tabel pochodnych. Spowoduje to wygenerowanie wszystkie poziomy dla nas. Pozostała część kodu jest naprawdę formatowania.

```sql
CREATE TABLE #Cube
WITH
(   DISTRIBUTION = ROUND_ROBIN
,   LOCATION = USER_DB
)
AS
WITH GrpCube AS
(SELECT    CAST(ISNULL(Country,'NULL')+','+ISNULL(Region,'NULL') AS NVARCHAR(50)) as 'Cols'
,          CAST(ISNULL(Country+',','')+ISNULL(Region,'') AS NVARCHAR(50))  as 'GroupBy'
,          ROW_NUMBER() OVER (ORDER BY Country) as 'Seq'
FROM       ( SELECT 'SalesTerritoryCountry' as Country
             UNION ALL
             SELECT NULL
           ) c
CROSS JOIN ( SELECT 'SalesTerritoryRegion' as Region
             UNION ALL
             SELECT NULL
           ) r
)
SELECT Cols
,      CASE WHEN SUBSTRING(GroupBy,LEN(GroupBy),1) = ','
            THEN SUBSTRING(GroupBy,1,LEN(GroupBy)-1)
            ELSE GroupBy
       END AS GroupBy  --Remove Trailing Comma
,Seq
FROM GrpCube;
```

Poniżej przedstawiono wyniki instrukcji CTAS:

![Grupuj według modułów](media/sql-data-warehouse-develop-group-by-options/sql-data-warehouse-develop-group-by-cube.png)

Drugim krokiem jest określenie tabeli docelowej do przechowywania wyników pośrednich:

```sql
DECLARE
 @SQL NVARCHAR(4000)
,@Columns NVARCHAR(4000)
,@GroupBy NVARCHAR(4000)
,@i INT = 1
,@nbr INT = 0
;
CREATE TABLE #Results
(
 [SalesTerritoryCountry] NVARCHAR(50)
,[SalesTerritoryRegion]  NVARCHAR(50)
,[TotalSalesAmount]      MONEY
)
WITH
(   DISTRIBUTION = ROUND_ROBIN
,   LOCATION = USER_DB
)
;
```

Trzeci krok dotyczy pętli naszych modułu kolumn, przeprowadzania agregacji. Zapytania będą uruchamiane raz dla każdego wiersza w tabeli tymczasowej #Cube i zapisać wyniki w tabeli tymczasowej #Results

```sql
SET @nbr =(SELECT MAX(Seq) FROM #Cube);

WHILE @i<=@nbr
BEGIN
    SET @Columns = (SELECT Cols    FROM #Cube where seq = @i);
    SET @GroupBy = (SELECT GroupBy FROM #Cube where seq = @i);

    SET @SQL ='INSERT INTO #Results
              SELECT '+@Columns+'
              ,      SUM(SalesAmount) AS TotalSalesAmount
              FROM  dbo.factInternetSales s
              JOIN  dbo.DimSalesTerritory t  
              ON s.SalesTerritoryKey = t.SalesTerritoryKey
              '+CASE WHEN @GroupBy <>''
                     THEN 'GROUP BY '+@GroupBy ELSE '' END

    EXEC sp_executesql @SQL;
    SET @i +=1;
END
```

Ponadto może zwrócić wyniki, po prostu zapoznając się z tabeli tymczasowej #Results

```sql
SELECT *
FROM #Results
ORDER BY 1,2,3
;
```

Podzielenie kod na sekcje i generowania konstrukcję pętli, kod staje się bardziej w zarządzaniu i łatwego w utrzymaniu.

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej porad programistycznych, zobacz [omówienie programowania w usłudze](sql-data-warehouse-overview-develop.md).

