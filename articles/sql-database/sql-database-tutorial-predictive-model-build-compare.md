---
title: 'Samouczek: Trenowania i porównywania modele predykcyjne w języku R'
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: W drugiej części tej serii samouczków trzyczęściowej serii będzie utworzyć dwa modele predykcyjne w języku R z SQL bazy danych usług Azure Machine Learning (wersja zapoznawcza), a następnie wybierz najdokładniejszych modelu.
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
ms.openlocfilehash: 3d336d6a53b6d234048c56d8492d278bef6fed64
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2019
ms.locfileid: "65957610"
---
# <a name="tutorial-create-a-predictive-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Samouczek: Tworzenie modelu predykcyjnego w języku R z SQL bazy danych usług Azure Machine Learning (wersja zapoznawcza)

W drugiej części tej serii samouczków trzyczęściowej serii będzie utworzyć dwa modele predykcyjne w języku R z SQL bazy danych usług Azure Machine Learning (wersja zapoznawcza), a następnie wybierz najdokładniejszych modelu.

W tym artykule dowiesz się, jak:

> [!div class="checklist"]
> * Szkolenie dwa modele uczenia maszynowego
> * Prognozowania z obu modeli
> * Należy porównać wyniki, aby wybrać model najdokładniejszych

W [pierwszej części](sql-database-tutorial-predictive-model-prepare-data.md), wiesz, jak do importować przykładową bazę danych do usługi Azure SQL database, a następnie przygotowywać dane, które posłuży do uczenia modelu predykcyjnego w języku R.

W [trzyczęściowej](sql-database-tutorial-predictive-model-deploy.md), dowiesz się, jak przechowywać modelu w bazie danych, a następnie utwórz procedurę składowaną, która może tworzyć prognozy na podstawie nowych danych.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* Drugiej części tego samouczka przyjęto założenie, zostały wykonane [ **pierwszej części** ](sql-database-tutorial-predictive-model-prepare-data.md) i jego wymagania wstępne.

## <a name="train-two-models"></a>Szkolenie dwa modele

Aby znaleźć najlepszy model danych wypożyczeń narciarskie, należy utworzyć dwa różne modele (regresja liniowa i drzewo decyzyjne) i sprawdzić, który z nich jest bardziej precyzyjne Prognozowanie. Użyjesz ramki danych `rentaldata` utworzony w części tej serii.

```r
#First, split the dataset into two different sets:
# one for training the model and the other for validating it
train_data = rentaldata[rentaldata$Year < 2015,];
test_data  = rentaldata[rentaldata$Year == 2015,];

#Use the RentalCount column to check the quality of the prediction against actual values
actual_counts <- test_data$RentalCount;

#Model 1: Use rxLinMod to create a linear regression model, trained with the training data set
model_linmod <- rxLinMod(RentalCount ~  Month + Day + WeekDay + Snow + Holiday, data = train_data);

#Model 2: Use rxDTree to create a decision tree model, trained with the training data set
model_dtree  <- rxDTree(RentalCount ~ Month + Day + WeekDay + Snow + Holiday, data = train_data);
```

## <a name="make-predictions-from-both-models"></a>Prognozowania z obu modeli

Użyj funkcji predict do predict, których dzierżawa jest liczona za pomocą każdego uczonego modelu.

```r
#Use both models to make predictions using the test data set.
predict_linmod <- rxPredict(model_linmod, test_data, writeModelVars = TRUE, extraVarsToWrite = c("Year"));

predict_dtree  <- rxPredict(model_dtree,  test_data, writeModelVars = TRUE, extraVarsToWrite = c("Year"));

#To verify it worked, look at the top rows of the two prediction data sets.
head(predict_linmod);
head(predict_dtree);
```

```results
    RentalCount_Pred  RentalCount  Month  Day  WeekDay  Snow  Holiday
1         27.45858          42       2     11     4      0       0
2        387.29344         360       3     29     1      0       0
3         16.37349          20       4     22     4      0       0
4         31.07058          42       3      6     6      0       0
5        463.97263         405       2     28     7      1       0
6        102.21695          38       1     12     2      1       0
    RentalCount_Pred  RentalCount  Month  Day  WeekDay  Snow  Holiday
1          40.0000          42       2     11     4      0       0
2         332.5714         360       3     29     1      0       0
3          27.7500          20       4     22     4      0       0
4          34.2500          42       3      6     6      0       0
5         645.7059         405       2     28     7      1       0
6          40.0000          38       1     12     2      1       0
```

## <a name="compare-the-results"></a>Należy porównać wyniki

Teraz chcesz zobaczyć, co modeli daje najlepsze prognozy. Jest szybki i łatwy sposób to zrobić na potrzeby wyświetlenia różnicy między wartościami rzeczywistych danych szkoleniowych i przewidywane wartości podstawowej funkcji wykreślania.

```r
#Use the plotting functionality in R to visualize the results from the predictions
par(mfrow = c(2, 1));
plot(predict_linmod$RentalCount_Pred - predict_linmod$RentalCount, main = "Difference between actual and predicted. rxLinmod");
plot(predict_dtree$RentalCount_Pred  - predict_dtree$RentalCount,  main = "Difference between actual and predicted. rxDTree");
```

![Porównanie dwóch modeli](./media/sql-database-tutorial-predictive-model-build-compare/compare-models.png)

Wygląda na to, modelu drzewa decyzyjnego jest bardziej precyzyjne z dwóch modeli.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli nie zamierzasz kontynuować z tego samouczka, należy usunąć bazę danych TutorialDB bazy danych z serwera usługi Azure SQL Database.

W witrynie Azure portal wykonaj następujące kroki:

1. Wybierz z menu po lewej stronie w witrynie Azure portal, **wszystkie zasoby** lub **baz danych SQL**.
1. W **Filtruj według nazwy...**  wprowadź **bazę danych TutorialDB**i wybierz swoją subskrypcję.
1. Wybierz bazę danych TutorialDB bazy danych.
1. Na stronie **Przegląd** wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Kolejne kroki

W drugiej części tej serii samouczków możesz wykonać następujące czynności:

* Szkolenie dwa modele uczenia maszynowego
* Prognozowania z obu modeli
* Należy porównać wyniki, aby wybrać model najdokładniejszych

Aby wdrożyć model usługi machine learning, który został utworzony, postępuj zgodnie z trzeciej części tej serii samouczków:

> [!div class="nextstepaction"]
> [Samouczek: Wdrażanie modelu predykcyjnego w języku R z SQL bazy danych usług Azure Machine Learning (wersja zapoznawcza)](sql-database-tutorial-predictive-model-deploy.md)
