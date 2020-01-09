---
title: Zdefiniuj zadanie uczenia maszynowego dla automatycznego przebiegu uczenia maszynowego
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak zdefiniować zadanie uczenia maszynowego dla automatycznego przebiegu uczenia maszynowego
services: machine-learning
author: RachelKellam
ms.author: rakellam
ms.reviewer: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.date: 11/04/2019
ms.openlocfilehash: a58959904559cd3b2cec27762f2df93cfea72abd
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/28/2019
ms.locfileid: "75540531"
---
# <a name="how-to-define-a-machine-learning-task"></a>Jak zdefiniować zadanie uczenia maszynowego 
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Ten artykuł zawiera informacje o obsługiwanych zadaniach uczenia maszynowego i sposobach ich definiowania na potrzeby uruchamiania eksperymentu automatycznego uczenia maszynowego.


## <a name="What is a machine learning task?"></a>Co to jest zadanie uczenia maszynowego?

Zadanie uczenia maszynowego reprezentuje typ problemu, który należy rozwiązać przez utworzenie modelu predykcyjnego. Zautomatyzowana ML obsługuje trzy różne typy zadań, takich jak Klasyfikacja, regresja i prognozowanie szeregów czasowych.

Typ zadania| Opis| Przykład
----|----|----
Klasyfikacja | Zadanie do przewidywania kategorii określonego wiersza w zestawie danych. | Wykrywanie oszustw na karcie kredytowej. W kolumnie Target zostałyby **wykryte oszustwo** z kategoriami *true* lub *false*. W tym przypadku klasyfikujemy każdy wiersz danych jako wartość true lub false.
Regresja | Zadanie do przewidywania ciągłej ilości danych wyjściowych. | Koszt samochodów w oparciu o funkcje, kolumna docelowa to **Cena**.
Prognozowanie |Zadanie do wykonywania świadomych szacunków w zakresie określania kierunku przyszłych trendów.| Prognozowanie zapotrzebowania na energię na kolejne 48 godzin. Kolumna docelowa będzie **na żądanie** , a przewidywane wartości byłyby używane do wyświetlania wzorców zapotrzebowania na energię.

Automatyczna ML obsługuje następujące algorytmy w trakcie procesu automatyzacji i dostrajania. Użytkownik nie ma potrzeby w celu określenia algorytm.

Klasyfikacja | Regresja | Prognozowanie szeregów czasowych
-- |-- |--
[Regresja logistyczna](https://scikit-learn.org/stable/modules/linear_model.html#logistic-regression)| [Elastyczne Net](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)| [Elastyczne Net](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)
[Jasny GBM](https://lightgbm.readthedocs.io/en/latest/index.html)|[Jasny GBM](https://lightgbm.readthedocs.io/en/latest/index.html)|[Jasny GBM](https://lightgbm.readthedocs.io/en/latest/index.html)
[Ulepszanie gradientu](https://scikit-learn.org/stable/modules/ensemble.html#classification)|[Ulepszanie gradientu](https://scikit-learn.org/stable/modules/ensemble.html#regression)|[Ulepszanie gradientu](https://scikit-learn.org/stable/modules/ensemble.html#regression)
[Drzewo decyzyjne](https://scikit-learn.org/stable/modules/tree.html#decision-trees)|[Drzewo decyzyjne](https://scikit-learn.org/stable/modules/tree.html#regression)|[Drzewo decyzyjne](https://scikit-learn.org/stable/modules/tree.html#regression)
[K najbliższym sąsiadów](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)|[K najbliższym sąsiadów](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)|[K najbliższym sąsiadów](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)
[Liniowy SVC](https://scikit-learn.org/stable/modules/svm.html#classification)|[Lasso HURTOWI](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)|[Lasso HURTOWI](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)
[Klasyfikacja wektor obsługę języka C (SVC)](https://scikit-learn.org/stable/modules/svm.html#classification)|[Stochastycznego spadku gradientu (wstecznej Propagacji)](https://scikit-learn.org/stable/modules/sgd.html#regression)|[Stochastycznego spadku gradientu (wstecznej Propagacji)](https://scikit-learn.org/stable/modules/sgd.html#regression)
[Losowe lasu](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)|[Losowe lasu](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)|[Losowe lasu](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)
[Bardzo losowego drzew](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)|[Bardzo losowego drzew](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)|[Bardzo losowego drzew](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)
[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)|[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)| [Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)
[DNN Classifier](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNClassifier)|[DNN Regresor](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor) | [DNN Regresor](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor)|
[DNN — klasyfikator liniowy](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearClassifier)|[Regresor liniowy](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)|[Regresor liniowy](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)
[Bayesa firmy](https://scikit-learn.org/stable/modules/naive_bayes.html#bernoulli-naive-bayes)||
[Stochastycznego spadku gradientu (wstecznej Propagacji)](https://scikit-learn.org/stable/modules/sgd.html#sgd)||


### <a name="set-the-task-type"></a>Ustawianie typu zadania
Możesz ustawić typ zadania dla zautomatyzowanych eksperymentów ML przy użyciu zestawu SDK lub Azure Machine Learning Studio.

Użyj parametru `task` w konstruktorze `AutoMLConfig`, aby określić typ eksperymentu.

```python
from azureml.train.automl import AutoMLConfig

# task can be one of classification, regression, forecasting
automl_config = AutoMLConfig(task="classification")
```

Możesz ustawić zadanie jako część zautomatyzowanego tworzenia przebiegu eksperymentu ML w Azure Machine Learning Studio. 

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku-inline.md)]

![Wybór typu zadania](./media/how-to-define-task-type/task-type.png)


## <a name="next-steps"></a>Następne kroki

+ Dowiedz się więcej o [zautomatyzowanych ml](concept-automated-ml.md) w Azure Machine Learning.
+ Dowiedz się więcej o [autoszkoleniu modelu prognozowania szeregów czasowych](how-to-auto-train-forecast.md) w Azure Machine Learning
+ Wypróbuj samouczek [zautomatyzowanej klasyfikacji Machine Learning](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/model-explanation) .
+ Wypróbuj przykładowy Notes [Machine Learning regresji automatycznej](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/model-explanation) .

