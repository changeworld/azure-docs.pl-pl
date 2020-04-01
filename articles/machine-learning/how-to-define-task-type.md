---
title: Definiowanie zadania uczenia maszynowego dla zautomatyzowanego uruchomienia uczenia maszynowego
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak zdefiniować zadanie uczenia maszynowego dla zautomatyzowanego uruchomienia uczenia maszynowego
services: machine-learning
author: RachelKellam
ms.author: rakellam
ms.reviewer: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.date: 11/04/2019
ms.openlocfilehash: fef3f0347d963b8a7d5fe2f1ca771293e9c3d2a9
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80475503"
---
# <a name="how-to-define-a-machine-learning-task"></a>Jak zdefiniować zadanie uczenia maszynowego 
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

W tym artykule dowiesz się, jakie są obsługiwane zadania uczenia maszynowego i jak je zdefiniować dla eksperymentu automatycznego uczenia maszynowego (zautomatyzowanego uczenia maszynowego).


## <a name="what-is-a-machine-learning-task"></a><a name="What is a machine learning task?"></a>Co to jest zadanie uczenia maszynowego?

Zadanie uczenia maszynowego reprezentuje typ problemu rozwiązywanego przez utworzenie modelu predykcyjnego. Automatyczne ml obsługuje trzy różne typy zadań, w tym klasyfikacji, regresji i prognozowania szeregów czasowych.

Typ zadania| Opis| Przykład
----|----|----
Klasyfikacja | Zadanie do przewidywania kategorii określonego wiersza w zestawie danych. | Wykrywanie oszustw na karcie kredytowej. Kolumna docelowa będzie **wykryta jako oszustwo** z kategoriami *Prawda* lub *Fałsz*. W takim przypadku klasyfikujemy każdy wiersz w danych jako true lub false.
Regresja | Zadanie do przewidywania ciągłej ilości wyjściowej. | Koszt samochodu w oparciu o jego cechy, kolumna docelowa będzie **cena**.
Prognozowanie |Zadanie dokonywania świadomych szacunków w określaniu kierunku przyszłych trendów.| Prognozowanie zapotrzebowania na energię w ciągu najbliższych 48 godzin. Kolumna docelowa będzie **popytu** i przewidywane wartości będą używane do pokazywalek w zapotrzebowaniu na energię.

Zautomatyzowane ml obsługuje następujące algorytmy podczas procesu automatyzacji i dostrajania. Jako użytkownik nie ma potrzeby określania algorytmu.

Klasyfikacja | Regresja | Prognozowanie szeregów czasowych
-- |-- |--
[Regresja logistyczna](https://scikit-learn.org/stable/modules/linear_model.html#logistic-regression)| [Sieć elastyczna](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)| [Sieć elastyczna](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)
[Lekki GBM](https://lightgbm.readthedocs.io/en/latest/index.html)|[Lekki GBM](https://lightgbm.readthedocs.io/en/latest/index.html)|[Lekki GBM](https://lightgbm.readthedocs.io/en/latest/index.html)
[Zwiększanie gradientu](https://scikit-learn.org/stable/modules/ensemble.html#classification)|[Zwiększanie gradientu](https://scikit-learn.org/stable/modules/ensemble.html#regression)|[Zwiększanie gradientu](https://scikit-learn.org/stable/modules/ensemble.html#regression)
[Drzewo decyzyjne](https://scikit-learn.org/stable/modules/tree.html#decision-trees)|[Drzewo decyzyjne](https://scikit-learn.org/stable/modules/tree.html#regression)|[Drzewo decyzyjne](https://scikit-learn.org/stable/modules/tree.html#regression)
[K Sąsiedzi](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)|[K Sąsiedzi](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)|[K Sąsiedzi](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)
[Liniowy SVC](https://scikit-learn.org/stable/modules/svm.html#classification)|[LARS/Lasso](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)|[LARS/Lasso](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)
[Klasyfikacja wektorowa obsługi języka C (SVC)](https://scikit-learn.org/stable/modules/svm.html#classification)|[Stochastyczne zejście gradientu (SGD)](https://scikit-learn.org/stable/modules/sgd.html#regression)|[Stochastyczne zejście gradientu (SGD)](https://scikit-learn.org/stable/modules/sgd.html#regression)
[Las losowy](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)|[Las losowy](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)|[Las losowy](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)
[Bardzo losowo drzewa](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)|[Bardzo losowo drzewa](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)|[Bardzo losowo drzewa](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)
[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)|[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)| [Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)
[Klasyfikator DNN](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNClassifier)|[DNN Regressor](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor) | [DNN Regressor](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor)|
[Klasyfikator liniowy DNN](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearClassifier)|[Regresor liniowy](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)|[Regresor liniowy](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)
[Prosty algorytm Bayesa](https://scikit-learn.org/stable/modules/naive_bayes.html#bernoulli-naive-bayes)||
[Stochastyczne zejście gradientu (SGD)](https://scikit-learn.org/stable/modules/sgd.html#sgd)||


### <a name="set-the-task-type"></a>Ustawianie typu zadania
Można ustawić typ zadania dla eksperymentów automatycznego uczenia maszynowego za pomocą zestawu SDK lub studia usługi Azure Machine Learning.

Użyj `task` parametru `AutoMLConfig` w konstruktorze, aby określić typ eksperymentu.

```python
from azureml.train.automl import AutoMLConfig

# task can be one of classification, regression, forecasting
automl_config = AutoMLConfig(task="classification")
```

Zadanie można ustawić w ramach automatycznego tworzenia eksperymentu uczenia maszynowego w studiu usługi Azure Machine Learning. 

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku-inline.md)]

![Wybór typu zadania](./media/how-to-define-task-type/task-type.png)


## <a name="next-steps"></a>Następne kroki

+ Dowiedz się więcej o [zautomatyzowanych ml](concept-automated-ml.md) w usłudze Azure Machine Learning.
+ Dowiedz się więcej o [automatycznym szkoleniu modelu prognozy szeregów czasowych](how-to-auto-train-forecast.md) w usłudze Azure Machine Learning
+ Wypróbuj samouczek [klasyfikacji automatycznego uczenia maszynowego.](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-credit-card-fraud/auto-ml-classification-credit-card-fraud.ipynb)
+ Wypróbuj przykładowy notes [automatycznego uczenia maszynowego.](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/regression-hardware-performance-explanation-and-featurization/auto-ml-regression-hardware-performance-explanation-and-featurization.ipynb)

