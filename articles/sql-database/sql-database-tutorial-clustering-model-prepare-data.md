---
title: 'Samouczek: Przygotowywanie danych do wykonania, klastrowania w języku R'
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: W części tej serii samouczków trzyczęściowej serii będzie przygotować dane z usługi Azure SQL database do wykonania, klastrowania w języku R z SQL bazy danych usług Azure Machine Learning (wersja zapoznawcza).
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
ms.openlocfilehash: 83ef25f04012933c2665e63e4617d480eb336f7b
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/30/2019
ms.locfileid: "66419461"
---
# <a name="tutorial-prepare-data-to-perform-clustering-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Samouczek: Przygotowywanie danych do wykonania, klastrowania w języku R z SQL bazy danych usług Azure Machine Learning (wersja zapoznawcza)

W części tej serii samouczków trzyczęściowej serii będzie przygotować dane z usługi Azure SQL database do wykonania, klastrowania w języku R z SQL bazy danych usług Azure Machine Learning (wersja zapoznawcza).

*Klastrowanie* można wyjaśnić jako organizowania danych w grupy, której członkowie danej grupy są podobne w jakiś sposób.
Użyjesz **K-średnich** algorytm klastrowanie klientów w zestawie danych produktu zakupów i zwraca. Przez klaster klientów, możesz skoncentrować się działań marketingowych efektywniej przez przeznaczonych dla określonych grup.
Klastrowanie K-średnich jest *uczenie nienadzorowane* oparciu algorytmu, który wyszukuje wzorce w danych.

W tym artykule dowiesz się, jak:

> [!div class="checklist"]
> * Importuj przykładową bazę danych do usługi Azure SQL database
> * Oddzielne klientów na różnych wymiarach
> * Ładowanie danych z bazy danych Azure SQL do ramki danych przy użyciu języka R

W [część druga](sql-database-tutorial-clustering-model-build.md), dowiesz się, jak utworzyć i wytrenuj model klastrowania K-średnich.

W [trzyczęściowej](sql-database-tutorial-clustering-model-deploy.md), dowiesz się, jak utworzyć procedurę przechowywaną w usługi Azure SQL database, które może wykonywać, klastrowanie, na podstawie nowych danych.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja Azure — Jeśli nie masz subskrypcji platformy Azure, [Tworzenie konta usługi](https://azure.microsoft.com/free/) przed przystąpieniem do wykonywania.

* Serwer bazy danych Azure SQL przy użyciu Machine Learning Services włączone - okresie publicznej wersji zapoznawczej będzie Microsoft dołączyć możesz i Włącz machine learning dla istniejących lub nowych baz danych. Postępuj zgodnie z instrukcjami w części [Tworzenie konta na potrzeby korzystania z wersji zapoznawczej](sql-database-machine-learning-services-overview.md#signup).

* Pakiet kolekcję funkcji RevoScaleR — zobacz [kolekcję funkcji RevoScaleR](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-revoscaler?view=sql-server-2017#versions-and-platforms) opcji do zainstalowania lokalnie tego pakietu.

* Środowisko IDE języka R — w tym samouczku używana [RStudio Desktop](https://www.rstudio.com/products/rstudio/download/).

* Narzędzie zapytania SQL — w tym samouczku założono, używasz [usługi Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is) lub [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS).

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

## <a name="import-the-sample-database"></a>Importuj przykładową bazę danych

Został zapisany Przykładowy zestaw danych używanych w tym samouczku **bacpac** plik kopii zapasowej bazy danych umożliwiające pobranie i użycie. Ten zestaw danych jest tworzony na podstawie [tpcx bb](http://www.tpc.org/tpcx-bb/default.asp) zestawu danych przedstawionego przez [Rady wydajność przetwarzania transakcji (TPC)](http://www.tpc.org/default.asp).

1. Pobierz plik [tpcxbb_1gb.bacpac](https://sqlchoice.blob.core.windows.net/sqlchoice/static/tpcxbb_1gb.bacpac).

1. Postępuj zgodnie z instrukcjami [Importowanie pliku BACPAC do utworzenia bazy danych Azure SQL](https://docs.microsoft.com/azure/sql-database/sql-database-import), za pomocą tych szczegółów:

   * Importuj z **tpcxbb_1gb.bacpac** pobrany plik
   * W publicznej wersji zapoznawczej, wybierz **/ — rdzeń wirtualny 5. generacji** konfiguracji dla nowej bazy danych
   * Nadaj nazwę nowej bazy danych "tpcxbb_1gb"

## <a name="separate-customers"></a>Oddzielne klientów

Utwórz nowy plik RScript w RStudio i uruchom następujący skrypt.
W zapytaniu SQL dzielisz klientów wzdłuż następujące wymiary:

* **orderRatio** = stosunek zwrotu zamówienia (całkowita liczba zamówień, częściowo lub całkowicie zwrócony, a łączna liczba zamówień)
* **itemsRatio** = stosunek zwracanego elementu (całkowita liczba elementów zwróconych w porównaniu z liczbą elementów zakupu)
* **monetaryRatio** = stosunek kwota zwrotu (łączną kwotę pieniężną elementów zwróconych w porównaniu z kwota zakupu)
* **częstotliwość** = zwracany częstotliwości

W **Wklej** funkcji, Zastąp **serwera**, **UID**, i **PWD** przy użyciu własnych informacji o połączeniu.

```r
# Define the connection string to connect to the tpcxbb_1gb database
connStr <- paste("Driver=SQL Server",
               "; Server=", "<Azure SQL Database Server>",
               "; Database=tpcxbb_1gb",
               "; UID=", "<user>",
               "; PWD=", "<password>",
                  sep = "");


#Define the query to select data from SQL Server
input_query <- "
SELECT ss_customer_sk AS customer
    ,round(CASE 
            WHEN (
                       (orders_count = 0)
                    OR (returns_count IS NULL)
                    OR (orders_count IS NULL)
                    OR ((returns_count / orders_count) IS NULL)
                    )
                THEN 0.0
            ELSE (cast(returns_count AS NCHAR(10)) / orders_count)
            END, 7) AS orderRatio
    ,round(CASE 
            WHEN (
                     (orders_items = 0)
                  OR (returns_items IS NULL)
                  OR (orders_items IS NULL)
                  OR ((returns_items / orders_items) IS NULL)
                 )
            THEN 0.0
            ELSE (cast(returns_items AS NCHAR(10)) / orders_items)
            END, 7) AS itemsRatio
    ,round(CASE 
            WHEN (
                     (orders_money = 0)
                  OR (returns_money IS NULL)
                  OR (orders_money IS NULL)
                  OR ((returns_money / orders_money) IS NULL)
                 )
            THEN 0.0
            ELSE (cast(returns_money AS NCHAR(10)) / orders_money)
            END, 7) AS monetaryRatio
    ,round(CASE 
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
"
```

## <a name="load-the-data-into-a-data-frame"></a>Załaduj dane do ramki danych

Teraz Użyj następującego skryptu w celu zwracania wyników z zapytania do usługi języka R danych ramki za pomocą **rxSqlServerData** funkcji.
W ramach procesu należy zdefiniować typ dla wybranych kolumn (z wykorzystaniem colClasses) aby upewnić się, że typy są prawidłowo przeniesione do języka R.

```r
# Query SQL Server using input_query and get the results back
# to data frame customer_returns
# Define the types for selected columns (using colClasses),
# to make sure that the types are correctly transferred to R
customer_returns <- rxSqlServerData(
                     sqlQuery=input_query,
                     colClasses=c(customer ="numeric",
                                  orderRatio="numeric",
                                  itemsRatio="numeric",
                                  monetaryRatio="numeric",
                                  frequency="numeric" ),
                     connectionString=connStr);

# Transform the data from an input dataset to an output dataset
customer_data <- rxDataStep(customer_returns);

# Take a look at the data just loaded from SQL Server
head(customer_data, n = 5);
```

Powinieneś zobaczyć wyniki podobne do następujących.

```results
  customer orderRatio itemsRatio monetaryRatio frequency
1    29727          0          0      0.000000         0
2    26429          0          0      0.041979         1
3    60053          0          0      0.065762         3
4    97643          0          0      0.037034         3
5    32549          0          0      0.031281         4
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

***Jeśli nie zamierzasz kontynuować z tego samouczka***, Usuń tpcxbb_1gb bazę danych z serwera usługi Azure SQL Database.

W witrynie Azure portal wykonaj następujące kroki:

1. Wybierz z menu po lewej stronie w witrynie Azure portal, **wszystkie zasoby** lub **baz danych SQL**.
1. W **Filtruj według nazwy...**  wprowadź **tpcxbb_1gb**i wybierz swoją subskrypcję.
1. Wybierz swoje **tpcxbb_1gb** bazy danych.
1. Na stronie **Przegląd** wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Kolejne kroki

W części tej serii samouczków wykonywane są następujące kroki:

* Importuj przykładową bazę danych do usługi Azure SQL database
* Oddzielne klientów na różnych wymiarach
* Ładowanie danych z bazy danych Azure SQL do ramki danych przy użyciu języka R

Aby utworzyć model, który używa tych danych uczenia maszynowego, postępuj zgodnie z dwóch części tej serii samouczków:

> [!div class="nextstepaction"]
> [Samouczek: Tworzenie modelu predykcyjnego w języku R z SQL bazy danych usług Azure Machine Learning (wersja zapoznawcza)](sql-database-tutorial-clustering-model-build.md)
