---
title: Opcje w usłudze Azure SQL Data Warehouse przy użyciu grupy | Dokumentacja firmy Microsoft
description: Wskazówki dotyczące implementowania grupy Opcje w usłudze Azure SQL Data Warehouse związane z opracowywaniem rozwiązań.
services: sql-data-warehouse
author: ronortloff
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: 0548983df23b158385783ac777b23268b5ac7d01
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2018
---
# <a name="group-by-options-in-sql-data-warehouse"></a>Grupuj według opcje w usłudze SQL Data Warehouse
Wskazówki dotyczące implementowania grupy Opcje w usłudze Azure SQL Data Warehouse związane z opracowywaniem rozwiązań.

## <a name="what-does-group-by-do"></a>Do czego GROUP BY?

[GROUP BY](/sql/t-sql/queries/select-group-by-transact-sql) klauzuli T-SQL agreguje dane podsumowania zestawu wierszy. GROUP BY zawiera niektóre opcje, które nie obsługuje usługi SQL Data Warehouse. Tych opcji jest obejścia.

Te opcje są

* GROUP BY z pakietem ZBIORCZYM
* GROUPING SETS
* Klauzula GROUP BY modułu

## <a name="rollup-and-grouping-sets-options"></a>Ustawia opcje ROLLUP i grouping
Jest to najprostsza opcja używania UNION ALL do pakietu zbiorczego zamiast polegania na jawnej składni. Wynik jest dokładnie taka sama

Poniższy przykład przy użyciu instrukcji GROUP BY z opcją ZBIORCZY:
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

Za pomocą pakietu ZBIORCZEGO, poprzednim przykładzie zwraca następujące agregacji:

* Kraj lub Region
* Kraj
* Całkowita suma

Pakiet ZBIORCZY i zwrócić takie same wyniki, możesz użyć UNION ALL i jawnie określić wymagane agregacji:

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

Aby zastąpić GROUPING SETS, zasada próbki dotyczy. Należy utworzyć UNION ALL sekcjach poziomów agregacji, które mają być wyświetlane.

## <a name="cube-options"></a>Opcje modułu
Istnieje możliwość utworzenia grupy przez z modułu przy użyciu podejścia UNION ALL. Problem polega na tym, że kod może szybko stać się skomplikowane i niewygodna. Aby temu zaradzić, możesz użyć tego bardziej zaawansowane metody.

Użyjmy w powyższym przykładzie.

Pierwszym krokiem jest określenie "module" definiujący wszystkie poziomy agregacji, jeśli chcesz utworzyć. Należy pamiętać o CROSS JOIN dwóch tabel pochodnych. Spowoduje to wygenerowanie wszystkie poziomy firmie Microsoft. Pozostała część kodu jest naprawdę formatowania.

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

Poniżej przedstawiono wyniki CTAS:

![Grupuj według modułu](media/sql-data-warehouse-develop-group-by-options/sql-data-warehouse-develop-group-by-cube.png)

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

Trzeci krok ma pętlę naszych modułu wykonywania agregacji kolumn. Zapytanie zostanie uruchomione jeden raz dla każdego wiersza w tabeli tymczasowej #Cube i zapisać wyniki w tabeli tymczasowej #Results

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

Ponadto zwracają wyniki odczytując po prostu z tabeli tymczasowej #Results

```sql
SELECT *
FROM #Results
ORDER BY 1,2,3
;
```

Podzielenie kod na sekcje i generowanie konstrukcji pętli, kod staje się bardziej łatwe w zarządzaniu i łatwy w obsłudze.

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej porad programistycznych, zobacz [omówienie tworzenia](sql-data-warehouse-overview-develop.md).

