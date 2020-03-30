---
title: Tworzenie zautomatyzowanych eksperymentów ml
titleSuffix: Azure Machine Learning
description: Automatyczne uczenie maszynowe wybiera algorytm dla Ciebie i generuje model gotowy do wdrożenia. Poznaj opcje, za pomocą których można skonfigurować eksperymenty automatycznego uczenia maszynowego.
author: cartacioS
ms.author: sacartac
ms.reviewer: sgilley
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 03/09/2020
ms.custom: seodec18
ms.openlocfilehash: 4cf940e38a84ea2eeb1896c8f7c628c8d5734374
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80247133"
---
# <a name="configure-automated-ml-experiments-in-python"></a>Konfigurowanie eksperymentów zautomatyzowanego uczenia maszynowego w języku Python
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

W tym przewodniku dowiesz się, jak zdefiniować różne ustawienia konfiguracji eksperymentów automatycznego uczenia maszynowego za pomocą [sdk usługi Azure Machine Learning.](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) Automatyczne uczenie maszynowe wybiera algorytm i hiperparametry dla Ciebie i generuje model gotowy do wdrożenia. Istnieje kilka opcji, których można użyć do skonfigurowania eksperymentów automatycznego uczenia maszynowego.

Aby wyświetlić przykłady eksperymentów automatycznego uczenia maszynowego, zobacz [Samouczek: Trenuj model klasyfikacji z automatycznym uczeniem maszynowym](tutorial-auto-train-models.md) lub [Modele pociągu z automatycznym uczeniem maszynowym w chmurze.](how-to-auto-train-remote.md)

Opcje konfiguracji dostępne w zautomatyzowanym uczeniu maszynowym:

* Wybierz typ eksperymentu: Klasyfikacja, Regresja lub Prognozowanie szeregów czasowych
* Źródło danych, formaty i dane pobierania
* Wybierz cel obliczeniowy: lokalny lub zdalny
* Zautomatyzowane ustawienia eksperymentu uczenia maszynowego
* Uruchamianie eksperymentu automatycznego uczenia maszynowego
* Eksploruj metryki modelu
* Rejestrowanie i wdrażanie modelu

Jeśli wolisz brak kodu, możesz również [utworzyć eksperymenty automatycznego uczenia maszynowego w studiu usługi Azure Machine Learning.](how-to-use-automated-ml-for-ml-models.md)

## <a name="select-your-experiment-type"></a>Wybieranie typu eksperymentu

Przed rozpoczęciem eksperymentu należy określić rodzaj rozwiązania problemu uczenia maszynowego. Automatyczne uczenie maszynowe obsługuje typy zadań klasyfikacji, regresji i prognozowania. Dowiedz się więcej o [typach zadań](how-to-define-task-type.md).

Zautomatyzowane uczenie maszynowe obsługuje następujące algorytmy podczas procesu automatyzacji i dostrajania. Jako użytkownik nie ma potrzeby określania algorytmu.

Klasyfikacja | Regresja | Prognozowanie szeregów czasowych
|-- |-- |--
[Regresja logistyczna](https://scikit-learn.org/stable/modules/linear_model.html#logistic-regression)| [Sieć elastyczna](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)| [Sieć elastyczna](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)
[Lekki GBM](https://lightgbm.readthedocs.io/en/latest/index.html)|[Lekki GBM](https://lightgbm.readthedocs.io/en/latest/index.html)|[Lekki GBM](https://lightgbm.readthedocs.io/en/latest/index.html)
[Zwiększanie gradientu](https://scikit-learn.org/stable/modules/ensemble.html#classification)|[Zwiększanie gradientu](https://scikit-learn.org/stable/modules/ensemble.html#regression)|[Zwiększanie gradientu](https://scikit-learn.org/stable/modules/ensemble.html#regression)
[Drzewo decyzyjne](https://scikit-learn.org/stable/modules/tree.html#decision-trees)|[Drzewo decyzyjne](https://scikit-learn.org/stable/modules/tree.html#regression)|[Drzewo decyzyjne](https://scikit-learn.org/stable/modules/tree.html#regression)
[K Sąsiedzi](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)|[K Sąsiedzi](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)|[K Sąsiedzi](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)
[Liniowy SVC](https://scikit-learn.org/stable/modules/svm.html#classification)|[LARS/Lasso](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)|[LARS/Lasso](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)
[Obsługuje klasyfikację wektorową (SVC)](https://scikit-learn.org/stable/modules/svm.html#classification)|[Stochastyczne zejście gradientu (SGD)](https://scikit-learn.org/stable/modules/sgd.html#regression)|[Stochastyczne zejście gradientu (SGD)](https://scikit-learn.org/stable/modules/sgd.html#regression)
[Las losowy](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)|[Las losowy](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)|[Las losowy](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)
[Bardzo losowo drzewa](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)|[Bardzo losowo drzewa](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)|[Bardzo losowo drzewa](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)
[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)|[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)| [Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)
[Klasyfikator DNN](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNClassifier)|[DNN Regressor](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor) | [DNN Regressor](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor)|
[Klasyfikator liniowy DNN](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearClassifier)|[Regresor liniowy](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)|[Regresor liniowy](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)
[Prosty algorytm Bayesa](https://scikit-learn.org/stable/modules/naive_bayes.html#bernoulli-naive-bayes)|[Szybki regresor liniowy](https://docs.microsoft.com/python/api/nimbusml/nimbusml.linear_model.fastlinearregressor?view=nimbusml-py-latest)|[Auto-ARIMA](https://www.alkaline-ml.com/pmdarima/modules/generated/pmdarima.arima.auto_arima.html#pmdarima.arima.auto_arima)
[Stochastyczne zejście gradientu (SGD)](https://scikit-learn.org/stable/modules/sgd.html#sgd)|[Regressor gradientu online](https://docs.microsoft.com/python/api/nimbusml/nimbusml.linear_model.onlinegradientdescentregressor?view=nimbusml-py-latest)|[Proroka](https://facebook.github.io/prophet/docs/quick_start.html)
|[Średni klasyfikator perceptronu](https://docs.microsoft.com/python/api/nimbusml/nimbusml.linear_model.averagedperceptronbinaryclassifier?view=nimbusml-py-latest)||ForecastTCN (ForecastTCN)
|[Klasyfikator liniowy SVM](https://docs.microsoft.com/python/api/nimbusml/nimbusml.linear_model.linearsvmbinaryclassifier?view=nimbusml-py-latest)||

Użyj `task` parametru `AutoMLConfig` w konstruktorze, aby określić typ eksperymentu.

```python
from azureml.train.automl import AutoMLConfig

# task can be one of classification, regression, forecasting
automl_config = AutoMLConfig(task = "classification")
```

## <a name="data-source-and-format"></a>Źródło i format danych

Automatyczne uczenie maszynowe obsługuje dane, które znajdują się na pulpicie lokalnym lub w chmurze, takich jak usługa Azure Blob Storage. Dane można odczytać w **pandas DataFrame** lub **Azure Machine Learning TabularDatset**.  [Dowiedz się więcej o zestawach danych](how-to-create-register-datasets.md).

Wymagania dotyczące danych szkoleniowych:
- Dane muszą być w formie tabelarycznej.
- Wartość do przewidzenia, kolumna docelowa, musi znajdować się w danych.

Poniższe przykłady kodu pokazują, jak przechowywać dane w tych formatach.

* Zestaw danych tabelarykowanych

  ```python
  from azureml.core.dataset import Dataset
  from azureml.opendatasets import Diabetes
  
  tabular_dataset = Diabetes.get_tabular_dataset()
  train_dataset, test_dataset = tabular_dataset.random_split(percentage=0.1, seed=42)
  label = "Y"
  ```

* Pandas dataframe

  ```python
  import pandas as pd
  from sklearn.model_selection import train_test_split

  df = pd.read_csv("your-local-file.csv")
  train_data, test_data = train_test_split(df, test_size=0.1, random_state=42)
  label = "label-col-name"
  ```

## <a name="fetch-data-for-running-experiment-on-remote-compute"></a>Pobieranie danych do uruchamiania eksperymentu na zdalnych obliczeniach

W przypadku wykonywania zdalnych dane szkoleniowe muszą być dostępne z obliczeń zdalnych. Klasa [`Datasets`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py) w SDK udostępnia funkcje do:

* łatwe przesyłanie danych z plików statycznych lub źródeł adresów URL do obszaru roboczego
* udostępnianie danych skryptom szkoleniowym podczas uruchamiania zasobów obliczeniowych w chmurze

Zobacz [instrukcje na](how-to-train-with-datasets.md#option-2--mount-files-to-a-remote-compute-target) przykład przy `Dataset` użyciu klasy do instalowania danych do celu obliczeniowego.

## <a name="train-and-validation-data"></a>Szkolenie i sprawdzanie poprawności danych

Można określić oddzielne zestawy pociągów i `AutoMLConfig` sprawdzania poprawności bezpośrednio w konstruktorze.

### <a name="k-folds-cross-validation"></a>K-Folds Cross Walidacja krzyżowa

Użyj `n_cross_validations` ustawienia, aby określić liczbę krzyżowych weryfikacji. Zestaw danych szkoleniowych zostanie losowo podzielony na `n_cross_validations` fałdy o równej wielkości. Podczas każdej rundy sprawdzania krzyżowej, jeden z fałd będzie używany do sprawdzania poprawności modelu przeszkolonego na pozostałych fałdach. Ten proces powtarza `n_cross_validations` się dla rund, aż każde krotnie jest używany raz jako zestaw sprawdzania poprawności. Średnie wyniki we `n_cross_validations` wszystkich rundach zostaną zgłoszone, a odpowiedni model zostanie przeszkolony na całym zestawie danych treningowych.

### <a name="monte-carlo-cross-validation-repeated-random-sub-sampling"></a>Walidacja krzyżowa Monte Carlo (powtarzające się losowe próbkowanie podrzędne)

Służy `validation_size` do określania procentu zestawu danych szkolenia, który `n_cross_validations` powinien być używany do sprawdzania poprawności i służy do określania liczby krzyżowych sprawdzania poprawności. Podczas każdej rundy sprawdzania poprawności krzyżowej podzbiór rozmiaru `validation_size` zostanie losowo wybrany do sprawdzania poprawności modelu przeszkolonego na pozostałych danych. Na koniec zostaną zgłoszone `n_cross_validations` średnie wyniki we wszystkich rundach, a odpowiedni model zostanie przeszkolony w całym zestawie danych treningowych. Monte Carlo nie jest obsługiwany w przypadku prognozowania szeregów czasowych.

### <a name="custom-validation-dataset"></a>Niestandardowy zestaw danych sprawdzania poprawności

Użyj niestandardowego zestawu danych sprawdzania poprawności, jeśli losowy podział jest nie do przyjęcia, zwykle dane szeregów czasowych lub dane niezrównoważone. Można określić własny zestaw danych sprawdzania poprawności. Model zostanie oceniony względem zestawu danych sprawdzania poprawności określonego zamiast losowego zestawu danych.

## <a name="compute-to-run-experiment"></a>Obliczenia w celu uruchomienia eksperymentu

Następnie określ, gdzie model będzie przeszkolony. Eksperyment szkolenia automatycznego uczenia maszynowego można uruchomić na następujących opcji obliczeniowych:
* Komputer lokalny, taki jak lokalny komputer stacjonarny lub laptop — zazwyczaj, gdy masz mały zestaw danych i nadal jesteś na etapie eksploracji.
* Maszyna zdalna w chmurze — [usługa Azure Machine Learning Managed Compute](concept-compute-target.md#amlcompute) to usługa zarządzana, która umożliwia uczenie modeli uczenia maszynowego w klastrach maszyn wirtualnych platformy Azure.

  Zobacz tę [witrynę GitHub,](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning) aby zapoznać się z przykładami notesów z lokalnymi i zdalnymi obiektami docelowymi obliczeniowymi.

* Klaster usługi Azure Databricks w ramach subskrypcji platformy Azure. Więcej informacji można znaleźć tutaj — [konfigurowanie klastra azure databricks dla zautomatyzowanego uczenia maszynowego](how-to-configure-environment.md#azure-databricks)

  Zobacz tę [witrynę GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/azure-databricks/automl) przykłady notesów z usługi Azure Databricks.

<a name='configure-experiment'></a>

## <a name="configure-your-experiment-settings"></a>Konfigurowanie ustawień eksperymentu

Istnieje kilka opcji, których można użyć do skonfigurowania eksperymentu automatycznego uczenia maszynowego. Te parametry są ustawiane `AutoMLConfig` przez utworzenie wystąpienia obiektu. Zobacz [AutoMLConfig klasy](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) pełnej listy parametrów.

Oto niektóre przykłady:

1. Eksperyment klasyfikacji przy użyciu AUC ważone jako metryka podstawowa z minut limitu czasu eksperymentu ustawiony na 30 minut i 2 przekrojowe sprawdzanie poprawności.

   ```python
       automl_classifier=AutoMLConfig(
       task='classification',
       primary_metric='AUC_weighted',
       experiment_timeout_minutes=30,
       blacklist_models=['XGBoostClassifier'],
       training_data=train_data,
       label_column_name=label,
       n_cross_validations=2)
   ```
2. Poniżej znajduje się przykład eksperymentu regresji zestaw do końca po 60 minutach z pięciu przeg upływu sprawdzania poprawności.

   ```python
      automl_regressor = AutoMLConfig(
      task='regression',
      experiment_timeout_minutes=60,
      whitelist_models=['kNN regressor'],
      primary_metric='r2_score',
      training_data=train_data,
      label_column_name=label,
      n_cross_validations=5)
   ```

Trzy różne `task` wartości parametrów (trzeci `forecasting`typ zadania jest i używa `regression` podobnej puli algorytmów jako zadania) określają listę modeli do zastosowania. Użyj `whitelist` lub `blacklist` parametrów do dalszej modyfikacji iteracji z dostępnych modeli do uwzględnienia lub wykluczenia. Lista obsługiwanych modeli znajduje się na [stronie Obsługiwanedkody klasy](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.constants.supportedmodels) dla ([Klasyfikacja](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.constants.supportedmodels.classification), [Prognozowanie](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.constants.supportedmodels.forecasting)i [Regresja](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.constants.supportedmodels.regression)).

Aby uniknąć błędów limitu czasu eksperymentu, usługa sprawdzania `experiment_timeout_minutes` poprawności automatycznego uczenia maszynowego będzie wymagać, aby ustawić co najmniej 15 minut lub 60 minut, jeśli rozmiar wiersza według kolumny przekracza 10 milionów.

### <a name="primary-metric"></a>Metryka podstawowa
Metryka podstawowa określa metrykę, która ma być używana podczas szkolenia modelu w celu optymalizacji. Dostępne metryki, które można wybrać, zależą od wybranego typu zadania, a w poniższej tabeli przedstawiono prawidłowe metryki podstawowe dla każdego typu zadania.

|Klasyfikacja | Regresja | Prognozowanie szeregów czasowych
|-- |-- |--
|accuracy| spearman_correlation | spearman_correlation
|AUC_weighted | normalized_root_mean_squared_error | normalized_root_mean_squared_error
|average_precision_score_weighted | r2_score | r2_score
|norm_macro_recall | normalized_mean_absolute_error | normalized_mean_absolute_error
|precision_score_weighted |

Dowiedz się więcej o konkretnych definicjach tych wskaźników w [opisie wyników automatycznego uczenia maszynowego.](how-to-understand-automated-ml.md)

### <a name="data-featurization"></a>Featurization danych

W każdym zautomatyzowanym eksperymencie uczenia maszynowego dane są [automatycznie skalowane i normalizowane,](concept-automated-ml.md#preprocess) aby pomóc *niektórym* algorytmom, które są wrażliwe na funkcje, które są w różnych skalach.  Można jednak również włączyć dodatkowe featurization, takie jak brakujące wartości przypisywania, kodowania i przekształceń. [Dowiedz się więcej o tym, co featurization jest wliczone](how-to-use-automated-ml-for-ml-models.md#featurization)w cenę .

Podczas konfigurowania eksperymentów można włączyć ustawienie `featurization`zaawansowane . W poniższej tabeli przedstawiono zaakceptowane ustawienia featurization w [ `AutoMLConfig` klasie](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlconfig?view=azure-ml-py).

|Konfiguracja featurization | Opis |
| ------------- | ------------- |
|`"featurization":`&nbsp;`'FeaturizationConfig'`| Wskazuje, że należy użyć niestandardowego kroku featurization. [Dowiedz się, jak dostosować featurization](how-to-configure-auto-train.md#customize-feature-engineering).|
|`"featurization": 'off'`| Wskazuje, że krok featurization nie powinien być wykonywany automatycznie.|
|`"featurization": 'auto'`| Wskazuje, że w ramach wstępnego [przetwarzania, barierki danych i kroki featurization](how-to-use-automated-ml-for-ml-models.md#advanced-featurization-options) są wykonywane automatycznie.|

> [!NOTE]
> Zautomatyzowane kroki featurization uczenia maszynowego (normalizacja funkcji, obsługa brakujących danych, konwersja tekstu na numeryczne, itp.) stają się częścią modelu źródłowego. Podczas korzystania z modelu dla prognoz, te same kroki featurization stosowane podczas szkolenia są stosowane do danych wejściowych automatycznie.

### <a name="time-series-forecasting"></a>Prognozowanie szeregów czasowych
Zadanie szeregów `forecasting` czasowych wymaga dodatkowych parametrów w obiekcie konfiguracji:

1. `time_column_name`: Wymagany parametr definiujący nazwę kolumny w danych treningowych zawierającej prawidłową serię czasową.
1. `max_horizon`: Określa czas, który ma być przewidywany na podstawie czasu danych szkoleniowych. Na przykład jeśli masz dane szkoleniowe z dziennych ziaren czasu, można zdefiniować, jak daleko w dniach, które mają trenować dla modelu.
1. `grain_column_names`: Definiuje nazwę kolumn zawierających dane poszczególnych szeregów czasowych w danych szkoleniowych. Na przykład jeśli prognozujesz sprzedaż określonej marki według sklepu, należy zdefiniować kolumny sklepu i marki jako kolumny ziarna. Dla każdego ziarna/grupowania zostaną utworzone oddzielne serie czasowe i prognozy. 

Przykłady ustawień użytych poniżej można znaleźć w [przykładowym notesie](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-orange-juice-sales/auto-ml-forecasting-orange-juice-sales.ipynb).

```python
# Setting Store and Brand as grains for training.
grain_column_names = ['Store', 'Brand']
nseries = data.groupby(grain_column_names).ngroups

# View the number of time series data with defined grains
print('Data contains {0} individual time-series.'.format(nseries))
```

```python
time_series_settings = {
    'time_column_name': time_column_name,
    'grain_column_names': grain_column_names,
    'drop_column_names': ['logQuantity'],
    'max_horizon': n_test_periods
}

automl_config = AutoMLConfig(task = 'forecasting',
                             debug_log='automl_oj_sales_errors.log',
                             primary_metric='normalized_root_mean_squared_error',
                             experiment_timeout_minutes=20,
                             training_data=train_data,
                             label_column_name=label,
                             n_cross_validations=5,
                             path=project_folder,
                             verbosity=logging.INFO,
                             **time_series_settings)
```

### <a name="ensemble-configuration"></a><a name="ensemble"></a>Konfiguracja zespołu

Modele zespołu są domyślnie włączone i są wyświetlane jako iteracje końcowego uruchomienia w automatycznym uruchomieniu uczenia maszynowego. Obecnie obsługiwane metody zespołu są głosowania i układania. Głosowanie jest realizowane jako głosowanie miękkie przy użyciu średnich ważonych, a implementacja układania jest przy użyciu dwuwarstwowej implementacji, gdzie pierwsza warstwa ma te same modele co zespół głosowania, a drugi model warstwy służy do znalezienia optymalnej kombinacji modeli z pierwszej warstwy. Jeśli używasz modeli ONNX **lub** masz włączoną funkcję wytłumaczenia modelu, układanie zostanie wyłączone i zostanie wykorzystane tylko głosowanie.

Istnieje wiele domyślnych argumentów, `kwargs` które `AutoMLConfig` mogą być dostarczone jako w obiekcie, aby zmienić domyślne zachowanie zespołu stosu.

* `stack_meta_learner_type`: meta-uczeń jest modelem przeszkolonym na podstawie wyników poszczególnych modeli heterogenicznych. Domyślne `LogisticRegression` osoby uczące się meta-learners są dla zadań klasyfikacji (lub `LogisticRegressionCV` jeśli krzyżowa walidacja jest włączona) i `ElasticNet` regresji/prognozowania zadań (lub `ElasticNetCV` jeśli krzyżowa walidacja jest włączona). Ten parametr może być jednym z `LogisticRegression` `LogisticRegressionCV`następujących `LightGBMClassifier` `ElasticNet`ciągów: , , , `ElasticNetCV`, , `LightGBMRegressor`, lub `LinearRegression`.
* `stack_meta_learner_train_percentage`: określa proporcję zestawu szkoleniowego (przy wyborze pociągu i rodzaju szkolenia walidacyjnego) do szkolenia osoby w kształceniu. Wartością `0.2`domyślną jest .
* `stack_meta_learner_kwargs`: parametry opcjonalne, aby przejść do inicjatora meta-learner. Te parametry i typy parametrów odzwierciedlają parametry i typy parametrów z odpowiedniego konstruktora modelu i są przekazywane do konstruktora modelu.

Poniższy kod przedstawia przykład określania niestandardowego `AutoMLConfig` zachowania zespołu w obiekcie.

```python
ensemble_settings = {
    "stack_meta_learner_type": "LogisticRegressionCV",
    "stack_meta_learner_train_percentage": 0.3,
    "stack_meta_learner_kwargs": {
        "refit": True,
        "fit_intercept": False,
        "class_weight": "balanced",
        "multi_class": "auto",
        "n_jobs": -1
    }
}

automl_classifier = AutoMLConfig(
        task='classification',
        primary_metric='AUC_weighted',
        experiment_timeout_minutes=30,
        training_data=train_data,
        label_column_name=label,
        n_cross_validations=5,
        **ensemble_settings
        )
```

Szkolenie zespołu jest domyślnie włączone, ale można `enable_voting_ensemble` je `enable_stack_ensemble` wyłączyć przy użyciu parametrów logicznych i logicznych.

```python
automl_classifier = AutoMLConfig(
        task='classification',
        primary_metric='AUC_weighted',
        experiment_timeout_minutes=30,
        training_data=data_train,
        label_column_name=label,
        n_cross_validations=5,
        enable_voting_ensemble=False,
        enable_stack_ensemble=False
        )
```

## <a name="run-experiment"></a>Uruchom eksperyment

W przypadku zautomatyzowanego `Experiment` uczenia maszynowego należy utworzyć `Workspace` obiekt, który jest obiektem nazwanym w ekspercie używanym do uruchamiania eksperymentów.

```python
from azureml.core.experiment import Experiment

ws = Workspace.from_config()

# Choose a name for the experiment and specify the project folder.
experiment_name = 'automl-classification'
project_folder = './sample_projects/automl-classification'

experiment = Experiment(ws, experiment_name)
```

Prześlij eksperyment, aby uruchomić i wygenerować model. Przekaż `AutoMLConfig` do `submit` metody, aby wygenerować model.

```python
run = experiment.submit(automl_config, show_output=True)
```

>[!NOTE]
>Zależności są najpierw instalowane na nowym komputerze.  Może upłynąć do 10 minut, zanim zostanie wyświetlone dane wyjściowe.
>Ustawienie `show_output` `True` wyników w danych wyjściowych wyświetlanych na konsoli.

### <a name="exit-criteria"></a>Kryteria wyjścia
Istnieje kilka opcji, które można zdefiniować, aby zakończyć eksperyment.
1. Brak kryteriów: Jeśli nie zdefiniujesz żadnych parametrów zakończenia, eksperyment będzie kontynuowany, dopóki nie zostanie osiągnięty dalszy postęp w metryki podstawowej.
1. Wyjdź po dłuższym czasie: użycie `experiment_timeout_minutes` w ustawieniach pozwala określić, jak długo w minutach eksperyment powinien być kontynuowany.
1. Zakończ po osiągnięciu wyniku: `experiment_exit_score` użycie zakończy eksperyment po osiągnięciu podstawowego wyniku metryki.

### <a name="explore-model-metrics"></a>Eksploruj metryki modelu

Wyniki treningu można wyświetlić w widżecie lub w trybie inline, jeśli znajdujesz się w notesie. Zobacz [Śledzenie i ocenianie modeli, aby](how-to-track-experiments.md#view-run-details) uzyskać więcej informacji.

## <a name="understand-automated-ml-models"></a>Poznaj zautomatyzowane modele ML

Każdy model wyprodukowany przy użyciu zautomatyzowanego uczenia maszynowego obejmuje następujące kroki:
+ Zautomatyzowana inżynieria `"featurization": 'auto'`funkcji (jeśli)
+ Skalowanie/normalizacja i algorytm z wartościami hiperparametrycznymi

Zapewniamy przejrzystość, aby uzyskać te informacje z fitted_model danych wyjściowych z automatycznego uczenia maszynowego.

```python
automl_config = AutoMLConfig(…)
automl_run = experiment.submit(automl_config …)
best_run, fitted_model = automl_run.get_output()
```

### <a name="automated-feature-engineering"></a>Zautomatyzowana inżynieria funkcji

Zobacz listę wstępnego przetwarzania i [zautomatyzowanej inżynierii funkcji,](concept-automated-ml.md#preprocess) która ma miejsce, gdy `"featurization": 'auto'`.

Rozważmy następujący przykład:
+ Istnieją cztery funkcje wejściowe: A (numeryczne), B (numeryczne), C (numeryczne), D (DateTime)
+ Funkcja numeryczna C jest odrzucana, ponieważ jest to kolumna identyfikatora ze wszystkimi unikatowymi wartościami
+ Cechy numeryczne A i B mają brakujące wartości, a zatem są przypisywane przez średnią
+ Funkcja DateTime D jest podzielona na 11 różnych funkcji inżynieryjnych

Użyj tych 2 interfejsów API w pierwszym kroku dopasowanego modelu, aby dowiedzieć się więcej.  Zobacz [ten przykładowy notes](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand).

+ Interfejs API `get_engineered_feature_names()` 1: zwraca listę nazw obiektów inżynierii.

  Użycie:
  ```python
  fitted_model.named_steps['timeseriestransformer']. get_engineered_feature_names ()
  ```

  ```
  Output: ['A', 'B', 'A_WASNULL', 'B_WASNULL', 'year', 'half', 'quarter', 'month', 'day', 'hour', 'am_pm', 'hour12', 'wday', 'qday', 'week']
  ```

  Ta lista zawiera wszystkie zaprojektowane nazwy funkcji.

  >[!Note]
  >Użyj 'timeseriestransformer' dla task='forecasting', w przeciwnym razie użyj 'datatransformator' dla zadania "regresja" lub "klasyfikacja".

+ Interfejs API `get_featurization_summary()` 2: zwraca podsumowanie featurization dla wszystkich funkcji wejściowych.

  Użycie:
  ```python
  fitted_model.named_steps['timeseriestransformer'].get_featurization_summary()
  ```

  >[!Note]
  >Użyj 'timeseriestransformer' dla task='forecasting', w przeciwnym razie użyj 'datatransformator' dla zadania "regresja" lub "klasyfikacja".

  Dane wyjściowe:
  ```
  [{'RawFeatureName': 'A',
    'TypeDetected': 'Numeric',
    'Dropped': 'No',
    'EngineeredFeatureCount': 2,
    'Tranformations': ['MeanImputer', 'ImputationMarker']},
   {'RawFeatureName': 'B',
    'TypeDetected': 'Numeric',
    'Dropped': 'No',
    'EngineeredFeatureCount': 2,
    'Tranformations': ['MeanImputer', 'ImputationMarker']},
   {'RawFeatureName': 'C',
    'TypeDetected': 'Numeric',
    'Dropped': 'Yes',
    'EngineeredFeatureCount': 0,
    'Tranformations': []},
   {'RawFeatureName': 'D',
    'TypeDetected': 'DateTime',
    'Dropped': 'No',
    'EngineeredFeatureCount': 11,
    'Tranformations': ['DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime']}]
  ```

   Gdzie:

   |Dane wyjściowe|Definicja|
   |----|--------|
   |Nazwa rawfeature|Nazwa funkcji/kolumny wprowadzania z dostarczonego zestawu danych.|
   |TypDetected|Wykryty typ danych funkcji wprowadzania.|
   |Spadła|Wskazuje, czy funkcja wprowadzania została upuszczona lub użyta.|
   |EngineeringFeatureCount|Liczba funkcji generowanych za pomocą zautomatyzowanych przekształceń inżynierii funkcji.|
   |Przekształcenia|Lista przekształceń zastosowanych do operacji wejściowych w celu wygenerowania obiektów inżynieryjnych.|
   
### <a name="customize-feature-engineering"></a>Dostosowywanie inżynierii funkcji
Aby dostosować inżynierię `"featurization": FeaturizationConfig`operacji, należy określić .

Obsługiwane dostosowywanie obejmuje:

|Dostosowywanie|Definicja|
|--|--|
|Aktualizacja celu kolumny|Zastąpuj typ operacji dla określonej kolumny.|
|Aktualizacja parametru transformatora |Aktualizuj parametry dla określonego transformatora. Obecnie obsługuje Imputer (średnia, najczęściej & mediana) i HashOneHotEncoder.|
|Kolumny upuszczania |Kolumny do spadku z featurized.|
|Transformatory blokowe| Transformatory blokowe do użycia w procesie featurization.|

Utwórz obiekt FeaturizationConfig przy użyciu wywołań interfejsu API:
```python
featurization_config = FeaturizationConfig()
featurization_config.blocked_transformers = ['LabelEncoder']
featurization_config.drop_columns = ['aspiration', 'stroke']
featurization_config.add_column_purpose('engine-size', 'Numeric')
featurization_config.add_column_purpose('body-style', 'CategoricalHash')
#default strategy mean, add transformer param for for 3 columns
featurization_config.add_transformer_params('Imputer', ['engine-size'], {"strategy": "median"})
featurization_config.add_transformer_params('Imputer', ['city-mpg'], {"strategy": "median"})
featurization_config.add_transformer_params('Imputer', ['bore'], {"strategy": "most_frequent"})
featurization_config.add_transformer_params('HashOneHotEncoder', [], {"number_of_bits": 3})
```

### <a name="scalingnormalization-and-algorithm-with-hyperparameter-values"></a>Skalowanie/normalizacja i algorytm z wartościami hiperparametrycznymi:

Aby zrozumieć skalowanie/normalizacja i algorytm/hiperparametr wartości dla potoku, należy użyć fitted_model.steps. [Dowiedz się więcej o skalowaniu/normalizacji](concept-automated-ml.md#preprocess). Oto przykładowe dane wyjściowe:

```
[('RobustScaler', RobustScaler(copy=True, quantile_range=[10, 90], with_centering=True, with_scaling=True)), ('LogisticRegression', LogisticRegression(C=0.18420699693267145, class_weight='balanced', dual=False, fit_intercept=True, intercept_scaling=1, max_iter=100, multi_class='multinomial', n_jobs=1, penalty='l2', random_state=None, solver='newton-cg', tol=0.0001, verbose=0, warm_start=False))
```

Aby uzyskać więcej szczegółów, użyj tej funkcji pomocnika pokazanej w [tym przykładowym notesie](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification/auto-ml-classification.ipynb).

```python
from pprint import pprint


def print_model(model, prefix=""):
    for step in model.steps:
        print(prefix + step[0])
        if hasattr(step[1], 'estimators') and hasattr(step[1], 'weights'):
            pprint({'estimators': list(
                e[0] for e in step[1].estimators), 'weights': step[1].weights})
            print()
            for estimator in step[1].estimators:
                print_model(estimator[1], estimator[0] + ' - ')
        else:
            pprint(step[1].get_params())
            print()


print_model(fitted_model)
```

Następujące dane wyjściowe próbki jest dla potoku przy użyciu określonego algorytmu (LogisticRegression z RobustScalar, w tym przypadku).

```
RobustScaler
{'copy': True,
'quantile_range': [10, 90],
'with_centering': True,
'with_scaling': True}

LogisticRegression
{'C': 0.18420699693267145,
'class_weight': 'balanced',
'dual': False,
'fit_intercept': True,
'intercept_scaling': 1,
'max_iter': 100,
'multi_class': 'multinomial',
'n_jobs': 1,
'penalty': 'l2',
'random_state': None,
'solver': 'newton-cg',
'tol': 0.0001,
'verbose': 0,
'warm_start': False}
```

### <a name="predict-class-probability"></a>Przewidywanie prawdopodobieństwa klasy

Modele produkowane przy użyciu zautomatyzowanego uczenia maszynowego mają obiekty otoki, które odzwierciedlają funkcjonalność z ich klasy pochodzenia typu open source. Większość obiektów otoki modelu klasyfikacji zwracanych przez zautomatyzowane ML implementuje `predict_proba()` funkcję, która akceptuje próbki danych macierzy podobnej lub rozrzedzone obiektów (wartości X) i zwraca tablicę n-wymiarową każdej próbki i jego prawdopodobieństwo odpowiedniej klasy.

Zakładając, że pobrano najlepszy model uruchamiania i montażu przy użyciu `predict_proba()` tych samych wywołań z `X_test` góry, można wywołać bezpośrednio z zainstalowanego modelu, podając próbkę w odpowiednim formacie w zależności od typu modelu.

```python
best_run, fitted_model = automl_run.get_output()
class_prob = fitted_model.predict_proba(X_test)
```

Jeśli model podstawowy nie `predict_proba()` obsługuje funkcji lub format jest niepoprawny, zostanie zgłoszony wyjątek specyficzny dla klasy modelu. Zobacz [RandomForestClassifier](https://scikit-learn.org/stable/modules/generated/sklearn.ensemble.RandomForestClassifier.html#sklearn.ensemble.RandomForestClassifier.predict_proba) i [XGBoost](https://xgboost.readthedocs.io/en/latest/python/python_api.html) dokumentów odwołania przykłady, jak ta funkcja jest implementowana dla różnych typów modeli.

<a name="explain"></a>

## <a name="model-interpretability"></a>Możliwość interpretowania modelu

Interpretability modelu pozwala zrozumieć, dlaczego modele wykonane prognoz i podstawowych wartości ważności funkcji. Zestaw SDK zawiera różne pakiety umożliwiające obsługę funkcji interpretowania modelu, zarówno w czasie szkolenia, jak i wnioskowania dla modeli lokalnych i wdrożonych.

Zobacz [przykłady](how-to-machine-learning-interpretability-automl.md) kodu dotyczące sposobu włączania funkcji interpretalności w szczególności w eksperymentach automatycznego uczenia maszynowego.

Aby uzyskać ogólne informacje na temat sposobu, w jaki można włączyć objaśnienia modelu i ważność funkcji w innych obszarach sdk poza zautomatyzowanym uczeniem maszynowym, zobacz artykuł [koncepcyjny](how-to-machine-learning-interpretability.md) dotyczący interpretowania.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [tym, jak i gdzie wdrożyć model](how-to-deploy-and-where.md).

Dowiedz się więcej o [tym, jak trenować model regresji za pomocą zautomatyzowanego uczenia maszynowego](tutorial-auto-train-models.md) lub [jak trenować przy użyciu automatycznego uczenia maszynowego na zdalnym zasobie.](how-to-auto-train-remote.md)
