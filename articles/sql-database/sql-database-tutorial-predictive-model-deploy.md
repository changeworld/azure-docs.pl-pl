---
title: 'Samouczek: Wdrażanie modelu predykcyjnego w języku R'
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: W trzeciej części tego samouczka trzyczęściowej serii będzie wdrożeniu modelu predykcyjnego w języku R z SQL bazy danych usług Azure Machine Learning (wersja zapoznawcza).
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
ms.openlocfilehash: 17b68f71f4034e5eb637d40b975cc22d94438fb7
ms.sourcegitcommit: 59fd8dc19fab17e846db5b9e262a25e1530e96f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/21/2019
ms.locfileid: "65978704"
---
# <a name="tutorial-deploy-a-predictive-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Samouczek: Wdrażanie modelu predykcyjnego w języku R z SQL bazy danych usług Azure Machine Learning (wersja zapoznawcza)

W trzeciej części tego samouczka trzyczęściowej serii będzie wdrożeniu modelu predykcyjnego w języku R z SQL bazy danych usług Azure Machine Learning (wersja zapoznawcza).

Utworzysz procedury składowanej z osadzonych skrypt języka R, która wykonuje prognozy przy użyciu modelu. Ponieważ model jest wykonywana w bazie danych Azure SQL, łatwo może być uczony danych przechowywanych w bazie danych.

W tym artykule dowiesz się, jak:

> [!div class="checklist"]
> * Store modelu predykcyjnego w tabeli bazy danych
> * Utwórz procedurę składowaną, która generuje modelu
> * Utwórz procedurę składowaną, która wykonuje prognozy przy użyciu modelu
> * Wykonaj modelu za pomocą nowych danych

W [pierwszej części](sql-database-tutorial-predictive-model-prepare-data.md), wiesz, jak do importować przykładową bazę danych do usługi Azure SQL database, a następnie przygotowywać dane, które posłuży do uczenia modelu predykcyjnego w języku R.

W [część druga](sql-database-tutorial-predictive-model-build-compare.md), przedstawiono sposób tworzenia i opracowywaniu wielu modeli, a następnie wybierz najdokładniejszych jeden.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* Trzeciej części tej serii samouczków przyjęto założenie, zostały wykonane [ **pierwszej części** ](sql-database-tutorial-predictive-model-prepare-data.md) i [ **część druga**](sql-database-tutorial-predictive-model-build-compare.md).

## <a name="create-a-stored-procedure-that-generates-the-model"></a>Utwórz procedurę składowaną, która generuje modelu

W drugiej części tej serii samouczków zdecydujesz, że decyzji modelu drzewa (dtree) jest najbardziej dokładna. Teraz Utwórz procedurę składowaną (`generate_rental_rx_model`), szkolenie modeli i generuje model dtree przy użyciu rxDTree z pakietu RevoScaleR.

Uruchom następujące polecenia w Studio danych platformy Azure lub programu SSMS.

```sql
-- Stored procedure that trains and generates an R model using the rental_data and a decision tree algorithm
DROP PROCEDURE IF EXISTS generate_rental_rx_model;
GO
CREATE PROCEDURE generate_rental_rx_model (@trained_model VARBINARY(max) OUTPUT)
AS
BEGIN
    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N'
require("RevoScaleR");

rental_train_data$Holiday <- factor(rental_train_data$Holiday);
rental_train_data$Snow    <- factor(rental_train_data$Snow);
rental_train_data$WeekDay <- factor(rental_train_data$WeekDay);

#Create a dtree model and train it using the training data set
model_dtree <- rxDTree(RentalCount ~ Month + Day + WeekDay + Snow + Holiday, data = rental_train_data);
#Serialize the model before saving it to the database table
trained_model <- as.raw(serialize(model_dtree, connection=NULL));
'
        , @input_data_1 = N'
            SELECT RentalCount
                 , Year
                 , Month
                 , Day
                 , WeekDay
                 , Snow
                 , Holiday
            FROM dbo.rental_data
            WHERE Year < 2015
            '
        , @input_data_1_name = N'rental_train_data'
        , @params = N'@trained_model varbinary(max) OUTPUT'
        , @trained_model = @trained_model OUTPUT;
END;
GO
```

## <a name="store-the-model-in-a-database-table"></a>Store modelu w tabeli bazy danych

Tworzenie tabeli w bazie danych bazę danych TutorialDB, a następnie Zapisz model do tabeli.

1. Utwórz tabelę (`rental_rx_models`) do przechowywania modelu.

    ```sql
    USE TutorialDB;
    DROP TABLE IF EXISTS rental_rx_models;
    GO
    CREATE TABLE rental_rx_models (
          model_name VARCHAR(30) NOT NULL DEFAULT('default model') PRIMARY KEY
        , model VARBINARY(MAX) NOT NULL
        );
    GO
    ```

1. Zapisz model do tabeli jako obiekt binarne, przy użyciu nazwy modelu "rxDTree".

    ```sql
    -- Save model to table
    TRUNCATE TABLE rental_rx_models;
    
    DECLARE @model VARBINARY(MAX);
    
    EXECUTE generate_rental_rx_model @model OUTPUT;
    
    INSERT INTO rental_rx_models (
          model_name
        , model
        )
    VALUES (
         'rxDTree'
        , @model
        );
    
    SELECT *
    FROM rental_rx_models;
    ```

## <a name="create-a-stored-procedure-that-makes-predictions"></a>Utwórz procedurę składowaną, która wykonuje prognozy

Tworzenie procedur składowanych (`predict_rentalcount_new`) sprawia to, że prognozy przy użyciu uczonego modelu i zestaw danych.

```sql
-- Stored procedure that takes model name and new data as input parameters and predicts the rental count for the new data
DROP PROCEDURE IF EXISTS predict_rentalcount_new;
GO
CREATE PROCEDURE predict_rentalcount_new (
      @model_name VARCHAR(100)
    , @input_query NVARCHAR(MAX)
    )
AS
BEGIN
    DECLARE @rx_model VARBINARY(MAX) = (
            SELECT model
            FROM rental_rx_models
            WHERE model_name = @model_name
            );

    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N'
require("RevoScaleR");

#Convert types to factors
rentals$Holiday <- factor(rentals$Holiday);
rentals$Snow    <- factor(rentals$Snow);
rentals$WeekDay <- factor(rentals$WeekDay);

#Before using the model to predict, we need to unserialize it
rental_model <- unserialize(rx_model);

#Call prediction function
rental_predictions <- rxPredict(rental_model, rentals);
'
        , @input_data_1 = @input_query
        , @input_data_1_name = N'rentals'
        , @output_data_1_name = N'rental_predictions'
        , @params = N'@rx_model varbinary(max)'
        , @rx_model = @rx_model
    WITH RESULT SETS(("RentalCount_Predicted" FLOAT));
END;
GO
```

## <a name="execute-the-model-with-new-data"></a>Wykonaj modelu za pomocą nowych danych

Teraz możesz użyć procedury składowanej `predict_rentalcount_new` przewidzieć liczbę wypożyczeń z nowymi danymi.

```sql
-- Use the predict_rentalcount_new stored procedure with the model name and a set of features to predict the rental count
EXECUTE dbo.predict_rentalcount_new @model_name = 'rxDTree'
    , @input_query = '
        SELECT CONVERT(INT,  3) AS Month
             , CONVERT(INT, 24) AS Day
             , CONVERT(INT,  4) AS WeekDay
             , CONVERT(INT,  1) AS Snow
             , CONVERT(INT,  1) AS Holiday
        ';
GO
```

Powinien zostać wyświetlony wynik podobny do następującego.

```results
RentalCount_Predicted
332.571428571429
```

Pomyślnie utworzona, skonfigurowanych pod kątem i wdrożyć model w usłudze Azure SQL database. Następnie używać tego modelu w procedurze składowanej do prognozowania wartości na podstawie nowych danych.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Po zakończeniu korzystania z bazy danych bazę danych TutorialDB, usuń go z serwerem usługi Azure SQL Database.

W witrynie Azure portal wykonaj następujące kroki:

1. Wybierz z menu po lewej stronie w witrynie Azure portal, **wszystkie zasoby** lub **baz danych SQL**.
1. W **Filtruj według nazwy...**  wprowadź **bazę danych TutorialDB**i wybierz swoją subskrypcję.
1. Wybierz bazę danych TutorialDB bazy danych.
1. Na stronie **Przegląd** wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Kolejne kroki

W trzeciej części tej serii samouczków możesz wykonać następujące czynności:

* Store modelu predykcyjnego w tabeli bazy danych
* Utwórz procedurę składowaną, która generuje modelu
* Utwórz procedurę składowaną, która wykonuje prognozy przy użyciu modelu
* Wykonaj modelu za pomocą nowych danych

Aby dowiedzieć się więcej na temat użycia języka R w usłudze Azure SQL Database usług Machine Learning (wersja zapoznawcza), zobacz:

* [Zapisywanie zaawansowane funkcje języka R w usłudze Azure SQL Database przy użyciu usług Machine Learning (wersja zapoznawcza)](sql-database-machine-learning-services-functions.md)
* [Praca z danymi SQL i języka R w SQL bazy danych usług Azure Machine Learning (wersja zapoznawcza)](sql-database-machine-learning-services-data-issues.md)
* [Dodaj pakiet języka R do SQL bazy danych usług Azure Machine Learning (wersja zapoznawcza)](sql-database-machine-learning-services-add-r-packages.md)
