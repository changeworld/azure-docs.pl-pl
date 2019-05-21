---
title: 'Samouczek: Przygotowywanie danych do nauczenia modelu predykcyjnego w języku R'
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: W części tej serii samouczków trzyczęściowej serii będzie przygotować dane z usługi Azure SQL database do uczenia modelu predykcyjnego w języku R z SQL bazy danych usług Azure Machine Learning (wersja zapoznawcza).
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
ms.date: 05/02/2019
ms.openlocfilehash: aa9c41ee34a50ab9b1409357bfe7d123166601bf
ms.sourcegitcommit: 59fd8dc19fab17e846db5b9e262a25e1530e96f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/21/2019
ms.locfileid: "65978730"
---
# <a name="tutorial-prepare-data-to-train-a-predictive-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Samouczek: Przygotowywanie danych do nauczenia modelu predykcyjnego w języku R z SQL bazy danych usług Azure Machine Learning (wersja zapoznawcza)

W części tej serii samouczków trzyczęściowej serii będzie przygotować dane z usługi Azure SQL database do uczenia modelu predykcyjnego w języku R z SQL bazy danych usług Azure Machine Learning (wersja zapoznawcza).

W tej serii samouczków Wyobraź sobie, jesteś właścicielem firmy wypożyczeń narciarskie i chcesz przewidzieć liczbę dzierżawy, które będziesz mieć w przyszłości. Ta informacja pomoże przygotować zasobów, pracowników i urządzeń.

W tym artykule dowiesz się, jak:

> [!div class="checklist"]
> * Importuj przykładową bazę danych do usługi Azure SQL database
> * Ładowanie danych z bazy danych Azure SQL do ramki danych przy użyciu języka R
> * Przygotowywanie danych, określając niektóre kolumny jako podzielonych na kategorie

W [część druga](sql-database-tutorial-predictive-model-build-compare.md), dowiesz się, jak utworzyć i opracowywaniu wielu modeli, a następnie wybierz najdokładniejszych jeden.

W [trzyczęściowej](sql-database-tutorial-predictive-model-deploy.md), dowiesz się, jak przechowywać modelu w bazie danych, a następnie utwórz procedurę składowaną, która może tworzyć prognozy na podstawie nowych danych.

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

Został zapisany Przykładowy zestaw danych używanych w tym samouczku **bacpac** plik kopii zapasowej bazy danych umożliwiające pobranie i użycie.

1. Pobierz plik [TutorialDB.bacpac](https://sqlchoice.blob.core.windows.net/sqlchoice/static/TutorialDB.bacpac).

1. Postępuj zgodnie z instrukcjami [Importowanie pliku BACPAC do utworzenia bazy danych Azure SQL](https://docs.microsoft.com/azure/sql-database/sql-database-import), za pomocą tych szczegółów:

   * Importuj z **TutorialDB.bacpac** pobrany plik
   * W publicznej wersji zapoznawczej, wybierz **/ — rdzeń wirtualny 5. generacji** konfiguracji dla nowej bazy danych
   * Nadaj nazwę nowej bazy danych "Bazę danych TutorialDB"

## <a name="load-the-data-into-a-data-frame"></a>Załaduj dane do ramki danych

Aby użyć danych w języku R, będzie załadować dane z bazy danych Azure SQL, do ramki danych (`rentaldata`).

Utwórz nowy plik RScript w RStudio i uruchom następujący skrypt. Zastąp **serwera**, **UID**, i **PWD** przy użyciu własnych informacji o połączeniu.

```r
#Define the connection string to connect to the TutorialDB database
connStr <- paste("Driver=SQL Server",
               "; Server=", "<Azure SQL Database Server>",
               "; Database=TutorialDB",
               "; UID=", "<user>",
               "; PWD=", "<password>",
                  sep = "");

#Get the data from the table
SQL_rentaldata <- RxSqlServerData(table = "dbo.rental_data", connectionString = connStr, returnDataFrame = TRUE);

#Import the data into a data frame
rentaldata <- rxImport(SQL_rentaldata);

#Take a look at the structure of the data and the top rows
head(rentaldata);
str(rentaldata);
```

Powinieneś zobaczyć wyniki podobne do następujących.

```results
   Year  Month  Day  RentalCount  WeekDay  Holiday  Snow
1  2014    1     20      445         2        1      0
2  2014    2     13       40         5        0      0
3  2013    3     10      456         1        0      0
4  2014    3     31       38         2        0      0
5  2014    4     24       23         5        0      0
6  2015    2     11       42         4        0      0
'data.frame':       453 obs. of  7 variables:
$ Year       : int  2014 2014 2013 2014 2014 2015 2013 2014 2013 2015 ...
$ Month      : num  1 2 3 3 4 2 4 3 4 3 ...
$ Day        : num  20 13 10 31 24 11 28 8 5 29 ...
$ RentalCount: num  445 40 456 38 23 42 310 240 22 360 ...
$ WeekDay    : num  2 5 1 2 5 4 1 7 6 1 ...
$ Holiday    : int  1 0 0 0 0 0 0 0 0 0 ...
$ Snow       : num  0 0 0 0 0 0 0 0 0 0 ...
```

## <a name="prepare-the-data"></a>Przygotowywanie danych

W tej przykładowej bazy danych najbardziej przygotowywanie zostało już wykonane, ale należy to zrobić co tutaj większych przygotowań.
Użyj następującego skryptu języka R do identyfikowania trzy kolumny jako *kategorie* , zmieniając typ danych, który będzie *współczynnik*.

```r
#Changing the three factor columns to factor types
rentaldata$Holiday <- factor(rentaldata$Holiday);
rentaldata$Snow    <- factor(rentaldata$Snow);
rentaldata$WeekDay <- factor(rentaldata$WeekDay);

#Visualize the dataset after the change
str(rentaldata);
```

Powinieneś zobaczyć wyniki podobne do następujących.

```results
data.frame':      453 obs. of  7 variables:
$ Year       : int  2014 2014 2013 2014 2014 2015 2013 2014 2013 2015 ...
$ Month      : num  1 2 3 3 4 2 4 3 4 3 ...
$ Day        : num  20 13 10 31 24 11 28 8 5 29 ...
$ RentalCount: num  445 40 456 38 23 42 310 240 22 360 ...
$ WeekDay    : Factor w/ 7 levels "1","2","3","4",..: 2 5 1 2 5 4 1 7 6 1 ...
$ Holiday    : Factor w/ 2 levels "0","1": 2 1 1 1 1 1 1 1 1 1 ...
$ Snow       : Factor w/ 2 levels "0","1": 1 1 1 1 1 1 1 1 1 1 ...
```

Dane, jest teraz gotowy do szkolenia.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli nie zamierzasz kontynuować z tego samouczka, należy usunąć bazę danych TutorialDB bazy danych z serwera usługi Azure SQL Database.

W witrynie Azure portal wykonaj następujące kroki:

1. Wybierz z menu po lewej stronie w witrynie Azure portal, **wszystkie zasoby** lub **baz danych SQL**.
1. W **Filtruj według nazwy...**  wprowadź **bazę danych TutorialDB**i wybierz swoją subskrypcję.
1. Wybierz bazę danych TutorialDB bazy danych.
1. Na stronie **Przegląd** wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Kolejne kroki

W części tej serii samouczków wykonywane są następujące kroki:

* Importowanie pliku kopii zapasowej bazy danych do usługi Azure SQL database
* Ładowanie danych z bazy danych Azure SQL do ramki danych przy użyciu języka R
* Przygotowywanie danych, określając niektóre kolumny jako kategorii

Aby utworzyć model, który używa danych z bazy danych bazę danych TutorialDB uczenia maszynowego, postępuj zgodnie z dwóch części tej serii samouczków:

> [!div class="nextstepaction"]
> [Samouczek: Tworzenie modelu predykcyjnego w języku R z SQL bazy danych usług Azure Machine Learning (wersja zapoznawcza)](sql-database-tutorial-predictive-model-build-compare.md)
