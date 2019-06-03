---
title: 'Samouczek: Wdróż model klastrowania w języku R'
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: W trzeciej części tej serii samouczków trzyczęściowej serii wdrożysz model klastrowania w języku R z SQL bazy danych usług Azure Machine Learning (wersja zapoznawcza).
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: r
ms.topic: tutorial
author: garyericson
ms.author: garye
ms.reviewer: davidph
manager: cgronlun
ms.date: 05/17/2019
ms.openlocfilehash: 1fe9df6378884ba55cb1017da87522ae66edaff0
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/30/2019
ms.locfileid: "66419755"
---
# <a name="tutorial-deploy-a-clustering-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Samouczek: Wdróż model klastrowania w języku R z SQL bazy danych usług Azure Machine Learning (wersja zapoznawcza)

W trzeciej części tej serii samouczków trzyczęściowej serii wdrożysz model klastrowania w języku R z SQL bazy danych usług Azure Machine Learning (wersja zapoznawcza).

Utworzysz procedury składowanej z osadzonych skrypt języka R, który wykonuje klastrowania. Ponieważ model jest wykonywana w bazie danych Azure SQL, łatwo może być uczony danych przechowywanych w bazie danych.

W tym artykule dowiesz się, jak:

> [!div class="checklist"]
> * Utwórz procedurę składowaną, która generuje modelu
> * Wykonaj klastrowania w bazie danych SQL
> * Skorzystaj z informacji klastrowania

W [pierwszej części](sql-database-tutorial-clustering-model-prepare-data.md), wiesz, jak przygotować dane z usługi Azure SQL database do wykonania, klastrowania w języku R.

W [część druga](sql-database-tutorial-clustering-model-build.md), wiesz, jak w celu zbudowania modelu K-średnich do wykonania, klastrowania.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* Trzeciej części tej serii samouczków przyjęto założenie, zostały wykonane [ **pierwszej części** ](sql-database-tutorial-clustering-model-prepare-data.md) i [ **część druga**](sql-database-tutorial-clustering-model-build.md).

## <a name="create-a-stored-procedure-that-generates-the-model"></a>Utwórz procedurę składowaną, która generuje modelu

Uruchom następujący skrypt języka T-SQL, aby utworzyć procedurę składowaną. Procedura odtwarza kroki opisane w części 1 i 2 w tej serii samouczków:

* klasyfikowanie klientów w oparciu o ich zakupu i zwracają historii
* Generowanie cztery klastry klientów przy użyciu algorytmu K-średnich

Procedury przechowywane wynikowy mapowania klastra klienta w tabeli bazy danych **customer_return_clusters**.

```sql
USE [tpcxbb_1gb]
DROP PROC IF EXISTS generate_customer_return_clusters;
GO
CREATE procedure [dbo].[generate_customer_return_clusters]
AS
/*
  This procedure uses R to classify customers into different groups
  based on their purchase & return history.
*/
BEGIN
    DECLARE @duration FLOAT
    , @instance_name NVARCHAR(100) = @@SERVERNAME
    , @database_name NVARCHAR(128) = db_name()
-- Input query to generate the purchase history & return metrics
    , @input_query NVARCHAR(MAX) = N'
SELECT ss_customer_sk AS customer,
    round(CASE 
            WHEN (
                    (orders_count = 0)
                    OR (returns_count IS NULL)
                    OR (orders_count IS NULL)
                    OR ((returns_count / orders_count) IS NULL)
                    )
                THEN 0.0
            ELSE (cast(returns_count AS NCHAR(10)) / orders_count)
            END, 7) AS orderRatio,
    round(CASE 
            WHEN (
                    (orders_items = 0)
                    OR (returns_items IS NULL)
                    OR (orders_items IS NULL)
                    OR ((returns_items / orders_items) IS NULL)
                    )
                THEN 0.0
            ELSE (cast(returns_items AS NCHAR(10)) / orders_items)
            END, 7) AS itemsRatio,
    round(CASE 
            WHEN (
                    (orders_money = 0)
                    OR (returns_money IS NULL)
                    OR (orders_money IS NULL)
                    OR ((returns_money / orders_money) IS NULL)
                    )
                THEN 0.0
            ELSE (cast(returns_money AS NCHAR(10)) / orders_money)
            END, 7) AS monetaryRatio,
    round(CASE 
            WHEN (returns_count IS NULL)
                THEN 0.0
            ELSE returns_count
            END, 0) AS frequency
FROM (
    SELECT ss_customer_sk,
        -- return order ratio
        COUNT(DISTINCT (ss_ticket_number)) AS orders_count,
        -- return ss_item_sk ratio
        COUNT(ss_item_sk) AS orders_items,
        -- return monetary amount ratio
        SUM(ss_net_paid) AS orders_money
    FROM store_sales s
    GROUP BY ss_customer_sk
    ) orders
LEFT OUTER JOIN (
    SELECT sr_customer_sk,
        -- return order ratio
        count(DISTINCT (sr_ticket_number)) AS returns_count,
        -- return ss_item_sk ratio
        COUNT(sr_item_sk) AS returns_items,
        -- return monetary amount ratio
        SUM(sr_return_amt) AS returns_money
    FROM store_returns
    GROUP BY sr_customer_sk
    ) returned ON ss_customer_sk = sr_customer_sk
 '
EXECUTE sp_execute_external_script
      @language = N'R'
    , @script = N'
# Define the connection string
connStr <- paste("Driver=SQL Server; Server=", instance_name,
               "; Database=", database_name,
               "; Trusted_Connection=true; ",
                  sep="" );

# Input customer data that needs to be classified.
# This is the result we get from the query.
customer_returns <- RxSqlServerData(
                     sqlQuery=input_query,
                     colClasses=c(customer ="numeric",
                                  orderRatio="numeric",
                                  itemsRatio="numeric",
                                  monetaryRatio="numeric",
                                  frequency="numeric" ),
                     connectionString=connStr);
# Output table to hold the customer cluster mappings
return_cluster = RxSqlServerData(table = "customer_return_clusters",
                                 connectionString = connStr);

# Set seed for random number generator for predictability
set.seed(10);

# Generate clusters using rxKmeans and output clusters to a table
# called "customer_return_clusters"
clust <- rxKmeans( ~ orderRatio + itemsRatio + monetaryRatio + frequency,
                   customer_returns,
                   numClusters = 4,
                   outFile = return_cluster,
                   outColName = "cluster",
                   writeModelVars = TRUE ,
                   extraVarsToWrite = c("customer"),
                   overwrite = TRUE);
'
    , @input_data_1 = N''
    , @params = N'@instance_name nvarchar(100), @database_name nvarchar(128), @input_query nvarchar(max), @duration float OUTPUT'
    , @instance_name = @instance_name
    , @database_name = @database_name
    , @input_query = @input_query
    , @duration = @duration OUTPUT;
END;

GO
```

## <a name="perform-clustering-in-sql-database"></a>Wykonaj klastrowania w bazie danych SQL

Teraz, po utworzeniu procedury składowanej, uruchom następujący skrypt do wykonania, klastrowania.

```sql
--Empty table of the results before running the stored procedure
TRUNCATE TABLE customer_return_clusters;

--Execute the clustering
--This will load the table customer_return_clusters with cluster mappings
EXECUTE [dbo].[generate_customer_return_clusters];
```

Sprawdź, czy to działa, a w rzeczywistości mamy listę klientów i ich mapowań klastra.

```sql
--Select data from table customer_return_clusters
--to verify that the clustering data was loaded
SELECT TOP (5) *
FROM customer_return_clusters;
```

```result
cluster  customer  orderRatio  itemsRatio  monetaryRatio  frequency
1        29727     0           0           0              0
4        26429     0           0           0.041979       1
2        60053     0           0           0.065762       3
2        97643     0           0           0.037034       3
2        32549     0           0           0.031281       4
```

## <a name="use-the-clustering-information"></a>Skorzystaj z informacji klastrowania

Ponieważ klastrowania procedury są przechowywane w bazie danych, można to wykonać, efektywnie klastrowanie danych klienta przechowywanych w tej samej bazy danych. Procedurę można wykonać zawsze wtedy, gdy Twoje dane klienta są aktualizowane i zaktualizowane informacje klastrowania.

Załóżmy, że użytkownik chce wysyłać promocyjne wiadomości e-mail do klientów w klastrze 3, grupy, która ma bardziej aktywne zachowanie zwracany (możesz zobaczyć, jak opisano cztery klastry w [część druga](sql-database-tutorial-clustering-model-build.md#analyze-the-results)). Poniższy kod wybiera adresy e-mail odbiorców w klastrze 3.

```sql
USE [tpcxbb_1gb]

SELECT customer.[c_email_address],
    customer.c_customer_sk
FROM dbo.customer
JOIN [dbo].[customer_return_clusters] AS r ON r.customer = customer.c_customer_sk
WHERE r.cluster = 3
```

Możesz zmienić **r.cluster** wartość do zwrócenia adresy e-mail dla klientów w innych klastrach.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Po zakończeniu tego samouczka możesz usunąć tpcxbb_1gb bazy danych z serwera usługi Azure SQL Database.

W witrynie Azure portal wykonaj następujące kroki:

1. Wybierz z menu po lewej stronie w witrynie Azure portal, **wszystkie zasoby** lub **baz danych SQL**.
1. W **Filtruj według nazwy...**  wprowadź **tpcxbb_1gb**i wybierz swoją subskrypcję.
1. Wybierz swoje **tpcxbb_1gb** bazy danych.
1. Na stronie **Przegląd** wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Kolejne kroki

W trzeciej części tej serii samouczków możesz wykonać następujące czynności:

* Utwórz procedurę składowaną, która generuje modelu
* Wykonaj klastrowania w bazie danych SQL
* Skorzystaj z informacji klastrowania

Aby dowiedzieć się więcej na temat użycia języka R w usłudze Azure SQL Database usług Machine Learning (wersja zapoznawcza), zobacz:

* [Samouczek: Przygotowywanie danych do nauczenia modelu predykcyjnego w języku R z SQL bazy danych usług Azure Machine Learning (wersja zapoznawcza)](sql-database-tutorial-predictive-model-prepare-data.md)
* [Zapisywanie zaawansowane funkcje języka R w usłudze Azure SQL Database przy użyciu usług Machine Learning (wersja zapoznawcza)](sql-database-machine-learning-services-functions.md)
* [Praca z danymi SQL i języka R w SQL bazy danych usług Azure Machine Learning (wersja zapoznawcza)](sql-database-machine-learning-services-data-issues.md)
* [Dodaj pakiet języka R do SQL bazy danych usług Azure Machine Learning (wersja zapoznawcza)](sql-database-machine-learning-services-add-r-packages.md)
