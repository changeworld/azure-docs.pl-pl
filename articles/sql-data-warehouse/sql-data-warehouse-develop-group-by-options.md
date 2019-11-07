---
title: Używanie opcji Grupuj według
description: Porady dotyczące implementowania opcji Grupuj według w Azure SQL Data Warehouse na potrzeby tworzenia rozwiązań.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: query
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 8d5d0f28fa210a56460ced25323b96a68f245895
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73685839"
---
# <a name="group-by-options-in-sql-data-warehouse"></a>Grupuj według opcji w SQL Data Warehouse
Porady dotyczące implementowania opcji Grupuj według w Azure SQL Data Warehouse na potrzeby tworzenia rozwiązań.

## <a name="what-does-group-by-do"></a>Co robi Grupuj według?

Klauzula [Group by](/sql/t-sql/queries/select-group-by-transact-sql) T-SQL agreguje dane do zestawu podsumowującego wierszy. Grupuj według zawiera kilka opcji, które nie są obsługiwane przez SQL Data Warehouse. Te opcje mają obejścia.

Te opcje są

* Grupuj według z pakietem zbiorczym
* ZESTAWY GRUPUJĄCE
* Grupuj według modułu

## <a name="rollup-and-grouping-sets-options"></a>Opcje zestawu zbiorczego i grupowania
Najprostszą opcją jest użycie UNION ALL zamiast, aby wykonać pakiet zbiorczy, zamiast polegać na jawnej składni. Wynik jest dokładnie taki sam

W poniższym przykładzie użyto instrukcji GROUP BY z opcją ROLLUP:
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

Przy użyciu pakietu zbiorczego poprzedni przykład żąda następujących agregacji:

* Kraj i region
* Kraj
* Suma końcowa

Aby zastąpić pakiet zbiorczy i zwrócić te same wyniki, można użyć klauzuli UNION ALL i jawnie określić wymagane agregacje:

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

Aby zastąpić zestawy grupowania, stosowana jest zasada przykładowa. Wystarczy utworzyć tylko sekcje UNION dla poziomów agregacji, które mają być wyświetlane.

## <a name="cube-options"></a>Opcje modułu
Istnieje możliwość utworzenia grupy za pomocą modułu przy użyciu podejścia UNION ALL. Problem polega na tym, że kod może szybko stać się skomplikowany i nieporęczny. Aby rozwiązać ten problem, można użyć bardziej zaawansowanego podejścia.

Użyjmy powyższego przykładu.

Pierwszym krokiem jest zdefiniowanie elementu "Cube", który definiuje wszystkie poziomy agregacji, które chcemy utworzyć. Ważne jest zanotowanie SPRZĘŻENIa KRZYŻowego dwóch tabel pochodnych. Spowoduje to wygenerowanie wszystkich poziomów dla nas. Pozostała część kodu jest naprawdę dla formatowania.

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

Trzeci krok polega na zapętleniu względem naszego modułu kolumn wykonujących agregację. Zapytanie zostanie uruchomione raz dla każdego wiersza w #Cube tabeli tymczasowej i będzie przechowywane wyniki w tabeli #Results tymczasowych

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

Na koniec można zwrócić wyniki, po prostu odczytując z tabeli tymczasowej #Results

```sql
SELECT *
FROM #Results
ORDER BY 1,2,3
;
```

Dzieląc kod na sekcje i generując konstrukcję zapętlenia, kod jest łatwiejsze do zarządzania i łatwość utrzymania.

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej porad programistycznych, zobacz [Omówienie projektowania](sql-data-warehouse-overview-develop.md).

