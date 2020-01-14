---
title: Tworzenie zautomatyzowanych eksperymentów ML
titleSuffix: Azure Machine Learning
description: Uczenie maszynowe automatycznych wybiera algorytm dla Ciebie i generuje gotowe do wdrożenia modelu. Dowiedz się, opcje, które umożliwiają skonfigurowanie automatycznych eksperymentów uczenia maszynowego.
author: cartacioS
ms.author: sacartac
ms.reviewer: sgilley
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 11/04/2019
ms.custom: seodec18
ms.openlocfilehash: d7af05f6d39a60a72df5e951f657a6f9b3761876
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/09/2020
ms.locfileid: "75767623"
---
# <a name="configure-automated-ml-experiments-in-python"></a>Konfigurowanie zautomatyzowanych eksperymentów ML w języku Python
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

W tym przewodniku dowiesz się, jak definiować różne ustawienia konfiguracji zautomatyzowanych eksperymentów uczenia maszynowego za pomocą [zestawu SDK Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py). Uczenie maszynowe automatycznych wybiera algorytm i hiperparametrów dla Ciebie i generuje gotowe do wdrożenia modelu. Istnieje kilka opcji, które umożliwiają skonfigurowanie automatycznych eksperymentów uczenia maszynowego.

Aby wyświetlić przykłady zautomatyzowanych eksperymentów dotyczących uczenia maszynowego, zobacz [Samouczek: uczenie modelu klasyfikacji z automatycznym uczeniem maszynowym](tutorial-auto-train-models.md) lub [modelami szkoleń z automatycznym uczeniem maszynowym w chmurze](how-to-auto-train-remote.md).

Opcje konfiguracji dostępne w automatycznych machine learning:

* Wybierz typ eksperymentu: klasyfikację, regresję lub prognozowanie szeregów czasowych
* Źródła danych, formatów i pobierania danych
* Wybierz obliczeniowego elementu docelowego: lokalnych lub zdalnych
* Automatyczne usługi machine learning ustawienia eksperymentu
* Uruchom zautomatyzowane eksperymentu uczenia maszynowego
* Eksplorowanie metryk modelu
* Zarejestruj i Wdróż model

Jeśli wolisz nie używać kodu, możesz również [utworzyć automatyczne eksperymenty uczenia maszynowego w programie Azure Machine Learning Studio](how-to-create-portal-experiments.md).

## <a name="select-your-experiment-type"></a>Wybierz typ eksperymentu

Przed przystąpieniem do wykonywania eksperymentu, należy określić rodzaj maszyny nauczanym problemem, które są rozwiązywania. Funkcja automatycznego uczenia maszynowego obsługuje typy zadań klasyfikacji, regresji i prognozowania. Dowiedz się więcej o [typach zadań](how-to-define-task-type.md).

Automatyczne machine learning obsługuje następujące algorytmy podczas automatyzacji i dostosowywania procesu. Użytkownik nie ma potrzeby w celu określenia algorytm.

Klasyfikacja | Regresja | Prognozowanie szeregów czasowych
|-- |-- |--
[Regresja logistyczna](https://scikit-learn.org/stable/modules/linear_model.html#logistic-regression)| [Elastyczne Net](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)| [Elastyczne Net](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)
[Jasny GBM](https://lightgbm.readthedocs.io/en/latest/index.html)|[Jasny GBM](https://lightgbm.readthedocs.io/en/latest/index.html)|[Jasny GBM](https://lightgbm.readthedocs.io/en/latest/index.html)
[Ulepszanie gradientu](https://scikit-learn.org/stable/modules/ensemble.html#classification)|[Ulepszanie gradientu](https://scikit-learn.org/stable/modules/ensemble.html#regression)|[Ulepszanie gradientu](https://scikit-learn.org/stable/modules/ensemble.html#regression)
[Drzewo decyzyjne](https://scikit-learn.org/stable/modules/tree.html#decision-trees)|[Drzewo decyzyjne](https://scikit-learn.org/stable/modules/tree.html#regression)|[Drzewo decyzyjne](https://scikit-learn.org/stable/modules/tree.html#regression)
[K najbliższym sąsiadów](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)|[K najbliższym sąsiadów](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)|[K najbliższym sąsiadów](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)
[Liniowy SVC](https://scikit-learn.org/stable/modules/svm.html#classification)|[Lasso HURTOWI](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)|[Lasso HURTOWI](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)
[Klasyfikacja wektorów pomocy technicznej (SVC)](https://scikit-learn.org/stable/modules/svm.html#classification)|[Stochastycznego spadku gradientu (wstecznej Propagacji)](https://scikit-learn.org/stable/modules/sgd.html#regression)|[Stochastycznego spadku gradientu (wstecznej Propagacji)](https://scikit-learn.org/stable/modules/sgd.html#regression)
[Losowe lasu](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)|[Losowe lasu](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)|[Losowe lasu](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)
[Bardzo losowego drzew](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)|[Bardzo losowego drzew](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)|[Bardzo losowego drzew](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)
[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)|[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)| [Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)
[DNN Classifier](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNClassifier)|[DNN Regresor](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor) | [DNN Regresor](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor)|
[DNN — klasyfikator liniowy](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearClassifier)|[Regresor liniowy](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)|[Regresor liniowy](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)
[Bayesa firmy](https://scikit-learn.org/stable/modules/naive_bayes.html#bernoulli-naive-bayes)|[Szybka Regresor liniowa](https://docs.microsoft.com/python/api/nimbusml/nimbusml.linear_model.fastlinearregressor?view=nimbusml-py-latest)|[AutoARIMA](https://www.alkaline-ml.com/pmdarima/modules/generated/pmdarima.arima.auto_arima.html#pmdarima.arima.auto_arima)
[Stochastycznego spadku gradientu (wstecznej Propagacji)](https://scikit-learn.org/stable/modules/sgd.html#sgd)|[Gradient online z Regresorem](https://docs.microsoft.com/python/api/nimbusml/nimbusml.linear_model.onlinegradientdescentregressor?view=nimbusml-py-latest)|[Prophet](https://facebook.github.io/prophet/docs/quick_start.html)
|[Klasyfikator średniej Perceptron](https://docs.microsoft.com/python/api/nimbusml/nimbusml.linear_model.averagedperceptronbinaryclassifier?view=nimbusml-py-latest)||ForecastTCN
|[Klasyfikator liniowy SVM](https://docs.microsoft.com/python/api/nimbusml/nimbusml.linear_model.linearsvmbinaryclassifier?view=nimbusml-py-latest)||

Użyj parametru `task` w konstruktorze `AutoMLConfig`, aby określić typ eksperymentu.

```python
from azureml.train.automl import AutoMLConfig

# task can be one of classification, regression, forecasting
automl_config = AutoMLConfig(task = "classification")
```

## <a name="data-source-and-format"></a>Źródło danych i format

Uczenie maszynowe automatycznych obsługuje dane, które znajdują się na komputerze lokalnym lub w chmurze, takich jak Azure Blob Storage. Dane można odczytać do **Pandas Dataframe** lub **Azure Machine Learning TabularDataset**.  [Dowiedz się więcej o zestawach danych](how-to-create-register-datasets.md).

Wymagania dotyczące danych szkoleniowych:
- Dane muszą być w formie tabelarycznej.
- Wartość do przewidywania, kolumna docelowa, musi znajdować się w danych.

Poniższy przykład kodu pokazuje, jak przechowywać dane w tych formatach.

* TabularDataset
  ```python
  from azureml.core.dataset import Dataset
  
  tabular_dataset = Dataset.Tabular.from_delimited_files("https://automldemods.blob.core.windows.net/datasets/PlayaEvents2016,_1.6MB,_3.4k-rows.cleaned.2.tsv")
  train_dataset, test_dataset = tabular_dataset.random_split(percentage = 0.1, seed = 42)
  label = "Label"
  ```

* Pandas dataframe

    ```python
    import pandas as pd
    from sklearn.model_selection import train_test_split

    df = pd.read_csv("https://automldemods.blob.core.windows.net/datasets/PlayaEvents2016,_1.6MB,_3.4k-rows.cleaned.2.tsv", delimiter="\t", quotechar='"')
    train_data, test_data = train_test_split(df, test_size = 0.1, random_state = 42)
    label = "Label"
    ```

## <a name="fetch-data-for-running-experiment-on-remote-compute"></a>Pobieranie danych do uruchamiania eksperymentów na zdalnego obliczeń

W przypadku wykonywania zdalnego dane szkoleniowe muszą być dostępne ze zdalnych obliczeń. Klasa [`Datasets`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py) w zestawie SDK udostępnia funkcje:

* łatwo Transferuj dane ze źródeł plików statycznych lub adresów URL do obszaru roboczego
* Udostępnij swoje dane dla skryptów szkoleniowych w przypadku korzystania z zasobów obliczeniowych w chmurze

Zobacz [instrukcje](how-to-train-with-datasets.md#option-2--mount-files-to-a-remote-compute-target) dotyczące przykładu użycia klasy `Dataset`, aby zainstalować dane w celu obliczenia.

## <a name="train-and-validation-data"></a>Szkolenie i sprawdzanie poprawności danych

Można określić oddzielne zestawy pouczenia i walidacji bezpośrednio w konstruktorze `AutoMLConfig`.

### <a name="k-folds-cross-validation"></a>K złożeń krzyżowe sprawdzanie poprawności

Użyj `n_cross_validations` ustawienie, aby określić liczbę krzyżowego sprawdzania poprawności. Szkoleniowy zestaw danych zostanie ona podzielona losowo na `n_cross_validations` złożeń taki sam rozmiar. Podczas każdego krzyżowego sprawdzania poprawności round jednym z złożeń będzie służyć do sprawdzania poprawności modelu skonfigurowanych pod kątem w pozostałych złożeń. Ten proces powtarza się dla `n_cross_validations` Zaokrągla do momentu poszczególnych etapów jest używany jeden raz w postanowieniach sprawdzania poprawności. Średnie wyniki dla wszystkich `n_cross_validations` zaokrągla zostaną zgłoszone, a odpowiedni model zostanie retrained, szkolenie dla całego zestawu danych.

### <a name="monte-carlo-cross-validation-repeated-random-sub-sampling"></a>Monte Carlo, krzyżowe sprawdzanie poprawności (powtarza losowe Podpróbkowanie)

Użyj `validation_size` Aby określić procent zestaw danych szkoleniowych, które mają być używane do sprawdzania poprawności i użyj `n_cross_validations` Aby określić liczbę krzyżowego sprawdzania poprawności. Podczas każdego cross weryfikacji round podzestaw rozmiar `validation_size` będą wybierane losowo do sprawdzania poprawności modelu skonfigurowanych pod kątem w pozostałych danych. Ponadto ocenia średnią dla wszystkich `n_cross_validations` zaokrągla zostaną zgłoszone, a odpowiedni model zostanie retrained, szkolenie dla całego zestawu danych. Monte Carlo nie jest obsługiwana w przypadku prognozowania szeregów czasowych.

### <a name="custom-validation-dataset"></a>Zestaw niestandardowego sprawdzania poprawności danych

Użyj niestandardowego zestawu danych walidacji, jeśli podział losowy nie jest akceptowalny, zazwyczaj dane szeregów czasowych lub niezrównoważone dane. Można określić własnego zestawu danych walidacji. Model zostaną ocenione względem zestawu danych walidacji, zamiast losowego zestawu danych.

## <a name="compute-to-run-experiment"></a>Zasoby obliczeniowe, uruchamianie eksperymentu

Następnie określić, gdzie będzie uczony model. Automatyczne eksperymentu uczenia maszynowego, szkolenia można uruchomić na następujących opcji obliczeniowych:
*   Komputer lokalny, takich jak lokalne stacjonarnym lub przenośnym — zazwyczaj, gdy masz mały zestaw danych i są nadal w fazie eksploracji.
*   Komputer zdalny w chmurze — [Machine Learning zarządzane obliczeń Azure](concept-compute-target.md#amlcompute) to zarządzana usługa, która umożliwia szkolenie modeli uczenia maszynowego w klastrach maszyn wirtualnych platformy Azure.

    Zobacz tę [witrynę usługi GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning) , aby poznać przykłady notesów z lokalnymi i zdalnymi obiektami docelowymi obliczeniowymi.

*   Klaster Azure Databricks w ramach subskrypcji platformy Azure. Więcej szczegółów można znaleźć tutaj — [instalator Azure Databricks klaster dla zautomatyzowanej ml](how-to-configure-environment.md#azure-databricks)

    Zobacz tę [witrynę w witrynie GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/azure-databricks/automl) , aby zapoznać się z przykładami notesów z Azure Databricks.

<a name='configure-experiment'></a>

## <a name="configure-your-experiment-settings"></a>Skonfiguruj ustawienia eksperymentu

Istnieje kilka opcji, które umożliwiają skonfigurowanie Twoje zautomatyzowane eksperymentu uczenia maszynowego. Te parametry są ustawiane przez utworzenie wystąpienia `AutoMLConfig` obiektu. Zapoznaj się z pełną listą parametrów w [klasie AutoMLConfig](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) .

Oto niektóre przykłady:

1.  Eksperyment klasyfikacji korzystający z AUC ważone jako Metryka podstawowa z limitem czasu eksperymentu w minutach ustawionym na 30 minut i 2 zgięcia wzajemnej walidacji.

    ```python
    automl_classifier=AutoMLConfig(
        task='classification',
        primary_metric='AUC_weighted',
        experiment_timeout_minutes=30,
        blacklist_models='XGBoostClassifier',
        training_data=train_data,
        label_column_name=label,
        n_cross_validations=2)
    ```
2.  Poniżej znajduje się przykład zestawu eksperymentów regresji, który ma zostać zakończony po 60 minutach z pięcioma zgięciami krzyżowymi walidacji.

    ```python
    automl_regressor = AutoMLConfig(
        task='regression',
        experiment_timeout_minutes=60,
        whitelist_models='kNN regressor'
        primary_metric='r2_score',
        training_data=train_data,
        label_column_name=label,
        n_cross_validations=5)
    ```

Trzy różne wartości parametrów `task` (trzeci typ zadania to `forecasting`i używa podobnej puli algorytmu jako `regression` zadania) określają listę modeli do zastosowania. Użyj parametrów `whitelist` lub `blacklist`, aby kontynuować modyfikowanie iteracji z dostępnymi modelami do dołączania lub wykluczania. Listę obsługiwanych modeli można znaleźć w [klasie SupportedModels](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.constants.supportedmodels).

### <a name="primary-metric"></a>Podstawowe metryki
Metryka podstawowa określa metrykę, która ma być używana podczas uczenia modelu na potrzeby optymalizacji. Dostępne metryki, które można wybrać, są określane przez wybrany typ zadania, a w poniższej tabeli przedstawiono prawidłowe Podstawowe metryki dla każdego typu zadania.

|Klasyfikacja | Regresja | Prognozowanie szeregów czasowych
|-- |-- |--
|accuracy| spearman_correlation | spearman_correlation
|AUC_weighted | normalized_root_mean_squared_error | normalized_root_mean_squared_error
|average_precision_score_weighted | r2_score | r2_score
|norm_macro_recall | normalized_mean_absolute_error | normalized_mean_absolute_error
|precision_score_weighted |

Zapoznaj się z określonymi definicjami tych metryk w temacie [Omówienie zautomatyzowanych wyników uczenia maszynowego](how-to-understand-automated-ml.md).

### <a name="data-preprocessing--featurization"></a>Przetwarzanie wstępne danych & cechowania

W każdym automatycznym doświadczeniu uczenia maszynowego Twoje dane są [automatycznie skalowane i znormalizowane](concept-automated-ml.md#preprocess) , aby ułatwić *określonym* algorytmom, które są wrażliwe na funkcje różnej skali.  Można jednak włączyć dodatkowe przetwarzanie wstępne/cechowania, na przykład brakujące wartości, które nie przypisywania, kodowania i transformacji. [Dowiedz się więcej na temat tego, co obejmuje cechowania](how-to-create-portal-experiments.md#preprocess).

Aby włączyć tę cechowania, określ `"preprocess": True` dla [klasy`AutoMLConfig`](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlconfig?view=azure-ml-py).

> [!NOTE]
> Zautomatyzowane kroki wstępnego przetwarzania w usłudze Machine Learning (normalizacja funkcji, obsługa brakujących danych, konwertowanie tekstu na liczbowe itp.) staje się częścią modelu źródłowego. Przy użyciu modelu dla prognoz te same kroki przetwarzania wstępnego zastosowane podczas uczenia są automatycznie stosowane do danych wejściowych.

### <a name="time-series-forecasting"></a>Prognozowanie szeregów czasowych
Zadanie `forecasting` szeregów czasowych wymaga dodatkowych parametrów w obiekcie Configuration:

1. `time_column_name`: wymagany parametr, który definiuje nazwę kolumny w danych szkoleniowych zawierających prawidłową serię czasową.
1. `max_horizon`: określa długość czasu, który ma być przewidywany na podstawie okresowości danych szkoleniowych. Na przykład jeśli masz dane szkoleniowe z codziennymi ziarnami czasowymi, możesz określić, jak daleko w dni ma być nadany model.
1. `grain_column_names`: Określa nazwę kolumn, które zawierają poszczególne dane szeregów czasowych w danych szkoleniowych. Na przykład w przypadku prognozowania sprzedaży określonej marki według sklepu należy zdefiniować kolumny sklepu i marki jako kolumny ziaren. Dla każdego ziarna/grupowania zostanie utworzona oddzielna seria czasowa i prognozy. 

Przykłady ustawień używanych poniżej można znaleźć w [notesie przykładowym](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-orange-juice-sales/auto-ml-forecasting-orange-juice-sales.ipynb).

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

### <a name="ensemble"></a>Konfiguracja kompletna

Modele kompletów są domyślnie włączone i pojawiają się jako ostateczne iteracje przebiegu w zautomatyzowanym przebiegu uczenia maszynowego. Obecnie obsługiwane metody kompletu są głosune i ułożone na stosie. Głosowanie jest implementowane jako rozdanie głosu przy użyciu średniej ważonej, a implementacja stosu korzysta z dwóch implementacji warstwy, w której pierwsza warstwa ma takie same modele jak zestaw do głosowania, a drugi model warstwy jest używany do znalezienia optymalnej kombinacji modele z pierwszej warstwy. Jeśli używasz modeli ONNX **lub** włączono wyjaśnienie modelu, stos zostanie wyłączony i będzie można używać tylko głosu.

Istnieje wiele argumentów domyślnych, które mogą być podane jako `kwargs` w obiekcie `AutoMLConfig`, aby zmienić domyślne zachowanie wbudowanego stosu.

* `stack_meta_learner_type`: meta-uczyć jest modelem przeszkolonym w danych wyjściowych poszczególnych modeli heterogenicznych. W przypadku zadań klasyfikacji są `LogisticRegression` domyślne, czyli `LogisticRegressionCV`, jeśli włączono funkcję wzajemnego sprawdzania poprawności) i `ElasticNet` do zadań regresji/prognozowania (lub `ElasticNetCV`, jeśli włączono funkcję wzajemnego sprawdzania poprawności). Ten parametr może być jednym z następujących ciągów: `LogisticRegression`, `LogisticRegressionCV`, `LightGBMClassifier`, `ElasticNet`, `ElasticNetCV`, `LightGBMRegressor`lub `LinearRegression`.
* `stack_meta_learner_train_percentage`: określa proporcję zestawu szkoleniowego (podczas wybierania typu uczenia i walidacji), które mają zostać zarezerwowane do uczenia się. Wartość domyślna to `0.2`.
* `stack_meta_learner_kwargs`: parametry opcjonalne do przekazania do inicjatora meta. Te parametry i typy parametrów stanowią duplikaty parametrów i typów parametrów z odpowiedniego konstruktora modelu i są przekazywane do konstruktora modelu.

Poniższy kod przedstawia przykład określania zachowania niestandardowego w obiekcie `AutoMLConfig`.

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

Szkolenie w ramach usługi jest domyślnie włączone, ale można je wyłączyć przy użyciu `enable_voting_ensemble` i `enable_stack_ensemble` parametrów logicznych.

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

## <a name="run-experiment"></a>Uruchamianie eksperymentu

W przypadku zautomatyzowanej ML utworzysz obiekt `Experiment`, który jest obiektem nazwanym w `Workspace` używanym do uruchamiania eksperymentów.

```python
from azureml.core.experiment import Experiment

ws = Workspace.from_config()

# Choose a name for the experiment and specify the project folder.
experiment_name = 'automl-classification'
project_folder = './sample_projects/automl-classification'

experiment = Experiment(ws, experiment_name)
```

Przesyłanie eksperymentu do uruchamiania i wygenerować model. Przekaż `AutoMLConfig` do `submit` metodę w celu wygenerowania modelu.

```python
run = experiment.submit(automl_config, show_output=True)
```

>[!NOTE]
>Zależności są najpierw instalowane na nowej maszynie.  Może upłynąć do 10 minut, zanim są wyświetlane dane wyjściowe.
>Ustawienie `show_output` do `True` skutkuje dane wyjściowe są wyświetlane w konsoli.

### <a name="exit-criteria"></a>Kryteria wyjścia
Istnieje kilka opcji, które można zdefiniować, aby zakończyć eksperyment.
1. Brak kryteriów: Jeśli nie określisz żadnych parametrów zakończenia, eksperyment będzie kontynuowany do momentu, gdy nie zostanie wprowadzony żaden kolejny postęp w głównej metryki.
1. Zakończ po upływie dłuższego czasu: użycie `experiment_timeout_minutes` w ustawieniach pozwala określić, jak długo w minutach ma być kontynuowany eksperyment.
1. Zakończ po osiągnięciu wyniku: użycie `experiment_exit_score` spowoduje zakończenie eksperymentu po osiągnięciu podstawowego wyniku metryki.

### <a name="explore-model-metrics"></a>Eksplorowanie metryk modelu

Możesz wyświetlić wyniki szkolenia w widżecie lub inline, jeśli jesteś w notesie. Zobacz [śledzenia i oceniać modele](how-to-track-experiments.md#view-run-details) Aby uzyskać więcej informacji.

## <a name="understand-automated-ml-models"></a>Zrozumienie zautomatyzowanych modeli ML

Każdy model wygenerowany przy użyciu zautomatyzowanej ML obejmuje następujące kroki:
+ Zautomatyzowana funkcja inżynierii
+ Skalowanie/Normalizacja i algorytm przy użyciu wartości parametru

Czynimy to przezroczyste, aby uzyskać te informacje z fitted_model danych wyjściowych z zautomatyzowanej ML.

```python
automl_config = AutoMLConfig(…)
automl_run = experiment.submit(automl_config …)
best_run, fitted_model = automl_run.get_output()
```

### <a name="automated-feature-engineering"></a>Zautomatyzowana funkcja inżynierii

Zapoznaj się z listą funkcji przetwarzania wstępnego i [zautomatyzowanej automatycznej inżynierii](concept-automated-ml.md#preprocess) , która ma miejsce, gdy feauturization = automatyczne.

Rozważmy następujący przykład:
+ Istnieją cztery funkcje wejściowe: A (numeryczne), B (numeryczne), C (liczbowe), D (DateTime)
+ Funkcja liczbowa C została porzucona, ponieważ jest kolumną identyfikatora ze wszystkimi unikatowymi wartościami
+ Funkcje liczbowe a i B mają brakujące wartości i dlatego są przypisane do średniej
+ Funkcja DateTime D jest featurized do 11 różnych wbudowanych funkcji

Użyj tych 2 interfejsów API w pierwszym kroku dopasowanego modelu, aby poznać więcej.  Zapoznaj się z [tym przykładowym notesem](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand).

+ Interfejs API 1: `get_engineered_feature_names()` zwraca listę nazwanych funkcji.

  Użycie:
  ```python
  fitted_model.named_steps['timeseriestransformer']. get_engineered_feature_names ()
  ```

  ```
  Output: ['A', 'B', 'A_WASNULL', 'B_WASNULL', 'year', 'half', 'quarter', 'month', 'day', 'hour', 'am_pm', 'hour12', 'wday', 'qday', 'week']
  ```

  Ta lista zawiera wszystkie zaprojektowane nazwy funkcji.

  >[!Note]
  >Użyj elementu "timeseriestransformer" dla zadania = "prognozowanie", w przeciwnym razie użyj "datatransformer" dla zadania "regresja" lub "Klasyfikacja".

+ Interfejs API 2: `get_featurization_summary()` zwraca podsumowanie cechowania dla wszystkich funkcji wejściowych.

  Użycie:
  ```python
  fitted_model.named_steps['timeseriestransformer'].get_featurization_summary()
  ```

  >[!Note]
  >Użyj elementu "timeseriestransformer" dla zadania = "prognozowanie", w przeciwnym razie użyj "datatransformer" dla zadania "regresja" lub "Klasyfikacja".

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

   Miejsce:

   |Dane wyjściowe|Definicja|
   |----|--------|
   |RawFeatureName|Podano nazwę funkcji/kolumny wejściowej z zestawu danych.|
   |TypeDetected|Wykryto typ danych funkcji wejściowej.|
   |Porzucony|Wskazuje, czy funkcja wejściowa została porzucona lub użyta.|
   |EngineeringFeatureCount|Liczba funkcji generowanych przez automatyczne transformacje inżynieryjnych funkcji.|
   |Przekształcenia|Lista transformacji zastosowanych do funkcji wejściowych do generowania przetworzonych funkcji.|
   
### <a name="customize-feature-engineering"></a>Dostosuj Inżynieria funkcji
Aby dostosować Inżynieria funkcji, określ `"feauturization":FeaturizationConfig`.

Obsługiwane dostosowania obejmują:

|Dostosowywanie|Definicja|
|--|--|
|Aktualizacja celu kolumny|Przesłoń typ funkcji dla określonej kolumny.|
|Aktualizacja parametru Transformer |Zaktualizuj parametry dla określonej funkcji przekształcania. Obecnie obsługuje program kalkulacyjne i HashOneHotEncoder.|
|Upuszczanie kolumn |Kolumny do usunięcia z featurized.|
|Blokuj Transformatory| Blokuj transformatory, które mają być używane w procesie cechowania.|

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

### <a name="scalingnormalization-and-algorithm-with-hyperparameter-values"></a>Skalowanie/Normalizacja i algorytm za pomocą wartości parametrów:

Aby zrozumieć wartości skalowania/normalizacji oraz algorytm/parametry dla potoku, użyj fitted_model. kroki. [Dowiedz się więcej o skalowaniu/normalizacji](concept-automated-ml.md#preprocess). Oto przykładowe dane wyjściowe:

```
[('RobustScaler', RobustScaler(copy=True, quantile_range=[10, 90], with_centering=True, with_scaling=True)), ('LogisticRegression', LogisticRegression(C=0.18420699693267145, class_weight='balanced', dual=False, fit_intercept=True, intercept_scaling=1, max_iter=100, multi_class='multinomial', n_jobs=1, penalty='l2', random_state=None, solver='newton-cg', tol=0.0001, verbose=0, warm_start=False))
```

Aby uzyskać więcej szczegółów, Użyj tej funkcji pomocnika pokazanej w [tym przykładowym notesie](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification/auto-ml-classification.ipynb).

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

Następujące przykładowe dane wyjściowe dotyczą potoku przy użyciu określonego algorytmu (LogisticRegression z RobustScalar, w tym przypadku).

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

### <a name="predict-class-probability"></a>Prawdopodobieństwo przewidywania klasy

Modele wytwarzane za pomocą zautomatyzowanej ML wszystkie mają obiekty otoki, które są dublowane z klasy pochodzenia Open Source. Większość obiektów otoki modelu klasyfikacji zwróconych przez zautomatyzowaną tablicę implementuje funkcję `predict_proba()`, która akceptuje próbkę danych macierzy podobnej do tablicy lub rozrzedzonej (wartości X) i zwraca tablicę n-wymiarową każdego przykładu i odpowiadającą jej wartość prawdopodobieństwa klasy.

Przy założeniu, że pobrano najlepszy przebieg i zamontowany model przy użyciu tych samych wywołań z powyższych, można wywołać `predict_proba()` bezpośrednio z poziomu dopasowanego modelu, dostarczając `X_test` próbkę w odpowiednim formacie w zależności od typu modelu.

```python
best_run, fitted_model = automl_run.get_output()
class_prob = fitted_model.predict_proba(X_test)
```

Jeśli źródłowy model nie obsługuje funkcji `predict_proba()` lub format jest niepoprawny, zostanie zgłoszony wyjątek specyficzny dla klasy modelu. Zobacz dokumenty referencyjne [RandomForestClassifier](https://scikit-learn.org/stable/modules/generated/sklearn.ensemble.RandomForestClassifier.html#sklearn.ensemble.RandomForestClassifier.predict_proba) i [XGBoost](https://xgboost.readthedocs.io/en/latest/python/python_api.html) , aby zapoznać się z przykładami tego, jak ta funkcja jest zaimplementowana dla różnych typów modeli.

<a name="explain"></a>

## <a name="model-interpretability"></a>Możliwość interpretowania modelu

Funkcja interpretacji modelu umożliwia zrozumienie, dlaczego modele są realizowane, oraz wartości ważności funkcji. Zestaw SDK zawiera różne pakiety umożliwiające włączenie funkcji interpretacji modelu zarówno w przypadku szkolenia, jak i czasu wnioskowania dla modeli lokalnych i wdrożonych.

Zapoznaj [się z](how-to-machine-learning-interpretability-automl.md) przykładami dotyczącymi sposobu włączania funkcji interpretacji w ramach zautomatyzowanych eksperymentów usługi Machine Learning.

Aby uzyskać ogólne informacje na temat sposobu włączenia wyjaśnień modelu i ważności funkcji w innych obszarach zestawu SDK poza funkcją automatycznego uczenia maszynowego, zapoznaj się z artykułem [koncepcja](how-to-machine-learning-interpretability.md) w zakresie interpretacji.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [jak i gdzie można wdrożyć model](how-to-deploy-and-where.md).

Dowiedz się więcej o tym, [Jak szkolić model regresji z automatycznym uczeniem maszynowym](tutorial-auto-train-models.md) lub [jak korzystać z funkcji automatycznego uczenia maszynowego w ramach zasobu zdalnego](how-to-auto-train-remote.md).
