---
title: Tworzenie zautomatyzowanych eksperymentów ML
titleSuffix: Azure Machine Learning
description: Automatyczne Uczenie maszynowe wybiera algorytm dla Ciebie i generuje model gotowy do wdrożenia. Zapoznaj się z opcjami, których można użyć, aby skonfigurować zautomatyzowane eksperymenty uczenia maszynowego.
author: nacharya1
ms.author: nilesha
ms.reviewer: sgilley
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 07/10/2019
ms.custom: seodec18
ms.openlocfilehash: 04753ca4c9b14d7ccc265cfcf971b3fd63c861ae
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/16/2019
ms.locfileid: "72384158"
---
# <a name="configure-automated-ml-experiments-in-python"></a>Konfigurowanie zautomatyzowanych eksperymentów ML w języku Python

W tym przewodniku dowiesz się, jak definiować różne ustawienia konfiguracji zautomatyzowanych eksperymentów uczenia maszynowego za pomocą [zestawu SDK Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py). Automatyczne Uczenie maszynowe wybiera algorytm i parametry i tworzy model gotowy do wdrożenia. Istnieje kilka opcji, których można użyć do skonfigurowania zautomatyzowanych eksperymentów w usłudze Machine Learning.

Aby wyświetlić przykłady zautomatyzowanych eksperymentów dotyczących uczenia maszynowego, zobacz [Samouczek: uczenie modelu klasyfikacji z automatycznym uczeniem maszynowym](tutorial-auto-train-models.md) lub [modelami szkoleń z automatycznym uczeniem maszynowym w chmurze](how-to-auto-train-remote.md).

Opcje konfiguracji dostępne w ramach automatycznego uczenia maszynowego:

* Wybierz typ eksperymentu: Klasyfikacja, regresja lub prognozowanie szeregów czasowych
* Źródło danych, formaty i pobieranie danych
* Wybierz obiekt docelowy obliczeń: lokalny lub zdalny
* Ustawienia zautomatyzowanego eksperymentu w usłudze Machine Learning
* Uruchamianie eksperymentu automatycznego uczenia maszynowego
* Eksplorowanie metryk modelu
* Zarejestruj i Wdróż model

Jeśli wolisz, że nie masz doświadczenia w kodzie, możesz również [utworzyć automatyczne eksperymenty uczenia maszynowego w Azure Portal](how-to-create-portal-experiments.md).

## <a name="select-your-experiment-type"></a>Wybierz typ eksperymentu

Przed rozpoczęciem eksperymentu należy określić rodzaj rozwiązywanego problemu z uczeniem maszynowym. Funkcja automatycznego uczenia maszynowego obsługuje typy zadań klasyfikacji, regresji i prognozowania.

Automatyczne Uczenie maszynowe obsługuje następujące algorytmy w trakcie procesu automatyzacji i dostrajania. Jako użytkownik nie ma potrzeby określania algorytmu.

Klasyfikacja | Ubytk | Prognozowanie szeregów czasowych
|-- |-- |--
[Regresja logistyczna](https://scikit-learn.org/stable/modules/linear_model.html#logistic-regression)| [Sieć elastyczna](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)| [Sieć elastyczna](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)
[Lekki GBM](https://lightgbm.readthedocs.io/en/latest/index.html)|[Lekki GBM](https://lightgbm.readthedocs.io/en/latest/index.html)|[Lekki GBM](https://lightgbm.readthedocs.io/en/latest/index.html)
[Zwiększanie gradientu](https://scikit-learn.org/stable/modules/ensemble.html#classification)|[Zwiększanie gradientu](https://scikit-learn.org/stable/modules/ensemble.html#regression)|[Zwiększanie gradientu](https://scikit-learn.org/stable/modules/ensemble.html#regression)
[Drzewo decyzyjne](https://scikit-learn.org/stable/modules/tree.html#decision-trees)|[Drzewo decyzyjne](https://scikit-learn.org/stable/modules/tree.html#regression)|[Drzewo decyzyjne](https://scikit-learn.org/stable/modules/tree.html#regression)
[Najbliższe sąsiednie sąsiedzi](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)|[Najbliższe sąsiednie sąsiedzi](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)|[Najbliższe sąsiednie sąsiedzi](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)
[Liniowy SVC](https://scikit-learn.org/stable/modules/svm.html#classification)|[LARS Lasso](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)|[LARS Lasso](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)
[C — Klasyfikacja wektorów pomocy technicznej (SVC)](https://scikit-learn.org/stable/modules/svm.html#classification)|[Stochastycznego gradientu (SGD)](https://scikit-learn.org/stable/modules/sgd.html#regression)|[Stochastycznego gradientu (SGD)](https://scikit-learn.org/stable/modules/sgd.html#regression)
[Las losowy](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)|[Las losowy](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)|[Las losowy](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)
[Skrajnie losowe drzewa](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)|[Skrajnie losowe drzewa](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)|[Skrajnie losowe drzewa](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)
[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)|[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)| [Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)
[Klasyfikator DNN](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNClassifier)|[DNN Regresor](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor) | [DNN Regresor](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor)|
[DNN — klasyfikator liniowy](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearClassifier)|[Regresor liniowy](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)|[Regresor liniowy](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)
[Algorytm Bayesa Bayesa](https://scikit-learn.org/stable/modules/naive_bayes.html#bernoulli-naive-bayes)|
[Stochastycznego gradientu (SGD)](https://scikit-learn.org/stable/modules/sgd.html#sgd)|

Użyj parametru `task` w konstruktorze `AutoMLConfig`, aby określić typ eksperymentu.

```python
from azureml.train.automl import AutoMLConfig

# task can be one of classification, regression, forecasting
automl_config = AutoMLConfig(task = "classification")
```

## <a name="data-source-and-format"></a>Źródło i format danych

Automatyczne Uczenie maszynowe obsługuje dane, które znajdują się na pulpicie lokalnym lub w chmurze, takiej jak Azure Blob Storage. Dane można odczytać do Pandas Dataframe lub zestawu danych Azure Machine Learning. Poniższy przykład kodu pokazuje, jak przechowywać dane w tych formatach. [Dowiedz się więcej o datatsets](https://github.com/MicrosoftDocs/azure-docs-pr/pull/how-to-create-register-datasets.md).

* TabularDataset
* Ramka dataPandas

>[!Important]
> Wymagania dotyczące danych szkoleniowych:
>* Dane muszą być w formie tabelarycznej.
>* Wartość, która ma zostać przewidywalna (kolumna docelowa), musi być obecna w danych.

Przykłady:

* TabularDataset
```python
    from azureml.core.dataset import Dataset

    tabular_dataset = Dataset.Tabular.from_delimited_files("https://automldemods.blob.core.windows.net/datasets/PlayaEvents2016,_1.6MB,_3.4k-rows.cleaned.2.tsv")
    train_dataset, test_dataset = tabular_dataset.random_split(percentage = 0.1, seed = 42)
    label = "Label"
```

*   Ramka dataPandas

    ```python
    import pandas as pd
    from sklearn.model_selection import train_test_split

    df = pd.read_csv("https://automldemods.blob.core.windows.net/datasets/PlayaEvents2016,_1.6MB,_3.4k-rows.cleaned.2.tsv", delimiter="\t", quotechar='"')
    train_data, test_data = train_test_split(df, test_size = 0.1, random_state = 42)
    label = "Label"
    ```

## <a name="fetch-data-for-running-experiment-on-remote-compute"></a>Pobieranie danych na potrzeby uruchamiania eksperymentu w przypadku obliczeń zdalnych

W przypadku wykonywania zdalnego dane szkoleniowe muszą być dostępne ze zdalnych obliczeń. Klasa [`Datasets`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py) w zestawie SDK udostępnia funkcje, aby:

* łatwo Transferuj dane ze źródeł plików statycznych lub adresów URL do obszaru roboczego
* Udostępnij swoje dane dla skryptów szkoleniowych w przypadku korzystania z zasobów obliczeniowych w chmurze

Zobacz [instrukcje](how-to-train-with-datasets.md#option-2--mount-files-to-a-remote-compute-target) dotyczące przykładu użycia klasy `Dataset` do instalowania danych w celu obliczenia.

## <a name="train-and-validation-data"></a>Dane dotyczące uczenia i walidacji

Można określić oddzielne zestawy pouczenia i walidacji bezpośrednio w konstruktorze `AutoMLConfig`.

### <a name="k-folds-cross-validation"></a>K — zgięcie krzyżowe

Aby określić liczbę operacji sprawdzania krzyżowego, użyj ustawienia `n_cross_validations`. Zestaw danych szkoleniowych zostanie losowo podzielony na `n_cross_validations` założenia o równym rozmiarze. Podczas każdego zaokrąglania krzyżowego, jeden ze zgięciów będzie używany do walidacji modelu przeszkolonego na pozostałych zgięciach. Ten proces powtarza się dla `n_cross_validations` zaokrągla do momentu, gdy każde zgięcie zostanie użyte raz jako zestaw walidacji. Zostaną zgłoszone średnie wyniki dla wszystkich `n_cross_validations` zaokrąglania, a odpowiedni model zostanie ponownie przeszkolony w całym zestawie danych szkoleniowych.

### <a name="monte-carlo-cross-validation-repeated-random-sub-sampling"></a>Monte Carlo, krzyżowe sprawdzanie poprawności (powtarza losowe Podpróbkowanie)

Użyj `validation_size`, aby określić wartość procentową zestawu danych szkoleniowych, która ma być używana do walidacji, a następnie użyj `n_cross_validations`, aby określić liczbę operacji krzyżowych. Podczas każdego zaokrąglania krzyżowego, podzbiór rozmiaru `validation_size` zostanie losowo wybrany do walidacji modelu przeszkolonego na pozostałych danych. Na koniec zostaną zgłoszone średnie wyniki dla wszystkich `n_cross_validations` zaokrąglania, a odpowiedni model zostanie przeszukany w całym zestawie danych szkoleniowych. Monte Carlo nie jest obsługiwana w przypadku prognozowania szeregów czasowych.

### <a name="custom-validation-dataset"></a>Niestandardowy zestaw danych walidacji

Użyj niestandardowego zestawu danych walidacji, jeśli podział losowy nie jest akceptowalny, zazwyczaj dane szeregów czasowych lub niezrównoważone dane. Możesz określić własny zestaw danych walidacji. Model zostanie oceniony względem określonego zestawu danych sprawdzania poprawności zamiast losowego zestawu danych.

## <a name="compute-to-run-experiment"></a>Oblicz, aby uruchomić eksperyment

Następnie określ, gdzie będzie szkolony model. Zautomatyzowany eksperyment szkoleniowy do uczenia maszynowego można uruchomić na następujących opcjach obliczeniowych:
*   Komputer lokalny, taki jak pulpit lokalny lub laptop, zazwyczaj w przypadku małego zestawu danych i nadal w fazie eksploracji.
*   Maszyna zdalna w chmurze — [Azure Machine Learning zarządzane obliczenia](concept-compute-target.md#amlcompute) to usługa zarządzana, która umożliwia uczenie modeli uczenia maszynowego w klastrach maszyn wirtualnych platformy Azure.

Zapoznaj się z [witryną GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning) na przykład notesów z lokalnymi i zdalnymi obiektami docelowymi obliczeniowymi.

*   Klaster Azure Databricks w ramach subskrypcji platformy Azure. Więcej szczegółów można znaleźć tutaj — [instalator Azure Databricks klaster dla zautomatyzowanej ml](how-to-configure-environment.md#azure-databricks)

Odwiedź [witrynę usługi GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/azure-databricks/automl) , aby zapoznać się z przykładami notesów z Azure Databricks.

<a name='configure-experiment'></a>

## <a name="configure-your-experiment-settings"></a>Skonfiguruj ustawienia eksperymentu

Istnieje kilka opcji, których można użyć do skonfigurowania automatycznego eksperymentu uczenia maszynowego. Te parametry są ustawiane przez utworzenie wystąpienia obiektu `AutoMLConfig`. Zapoznaj się z pełną listą parametrów w [klasie AutoMLConfig](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlconfig?view=azure-ml-py) .

Oto niektóre przykłady:

1.  Eksperyment klasyfikacji korzystający z AUC ważone jako podstawowa Metryka z maksymalnym czasem 12 000 sekund na iterację, z eksperymentem kończącym się po 50 iteracjach i 2 zgięciami z różnymi walidacjami.

    ```python
    automl_classifier=AutoMLConfig(
        task='classification',
        primary_metric='AUC_weighted',
        max_time_sec=12000,
        iterations=50,
        blacklist_models='XGBoostClassifier',
        training_data=train_data,
        label_column_name=label,
        n_cross_validations=2)
    ```
2.  Poniżej znajduje się przykład zestawu eksperymentów regresji, który ma kończyć się po 100 iteracjach, z każdą iteracją trwającą do 600 sekund z 5 walidacji krzyżowych.

    ```python
    automl_regressor = AutoMLConfig(
        task='regression',
        max_time_sec=600,
        iterations=100,
        whitelist_models='kNN regressor'
        primary_metric='r2_score',
        training_data=train_data,
        label_column_name=label,
        n_cross_validations=5)
    ```

Trzy różne wartości parametrów `task` (trzeci typ zadania to `forecasting` i używa tej samej puli algorytmów co `regression` zadania) określają listę modeli do zastosowania. Użyj parametrów `whitelist` lub `blacklist`, aby kontynuować modyfikowanie iteracji z dostępnymi modelami do dołączania lub wykluczania. Listę obsługiwanych modeli można znaleźć w [klasie SupportedModels](https://docs.microsoft.com/en-us/python/api/azureml-train-automl/azureml.train.automl.constants.supportedmodels?view=azure-ml-py).

### <a name="primary-metric"></a>Metryka podstawowa
Metryka podstawowa określa metrykę, która ma być używana podczas uczenia modelu na potrzeby optymalizacji. Dostępne metryki, które można wybrać, są określane przez wybrany typ zadania, a w poniższej tabeli przedstawiono prawidłowe Podstawowe metryki dla każdego typu zadania.

|Klasyfikacja | Ubytk | Prognozowanie szeregów czasowych
|-- |-- |--
|accuracy| spearman_correlation | spearman_correlation
|AUC_weighted | normalized_root_mean_squared_error | normalized_root_mean_squared_error
|average_precision_score_weighted | r2_score | r2_score
|norm_macro_recall | normalized_mean_absolute_error | normalized_mean_absolute_error
|precision_score_weighted |

Zapoznaj się z określonymi definicjami tych elementów, aby [poznać automatyczne wyniki uczenia maszynowego](how-to-understand-automated-ml.md).

### <a name="data-preprocessing--featurization"></a>Przetwarzanie wstępne danych & cechowania

W każdym automatycznym doświadczeniu uczenia maszynowego Twoje dane są [automatycznie skalowane i znormalizowane](concept-automated-ml.md#preprocess) , aby ułatwić *określonym* algorytmom, które są wrażliwe na funkcje różnej skali.  Można jednak włączyć dodatkowe przetwarzanie wstępne/cechowania, na przykład brakujące wartości, które nie przypisywania, kodowania i transformacji. [Dowiedz się więcej na temat tego, co obejmuje cechowania](how-to-create-portal-experiments.md#preprocess).

Aby włączyć tę cechowania, określ `"preprocess": True` dla [klasy `AutoMLConfig`](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlconfig?view=azure-ml-py).

> [!NOTE]
> Zautomatyzowane kroki wstępnego przetwarzania w usłudze Machine Learning (normalizacja funkcji, obsługa brakujących danych, konwertowanie tekstu na liczbowe itp.) staje się częścią modelu źródłowego. Przy użyciu modelu dla prognoz te same kroki przetwarzania wstępnego zastosowane podczas uczenia są automatycznie stosowane do danych wejściowych.

### <a name="time-series-forecasting"></a>Prognozowanie szeregów czasowych
Zadanie @no__t szeregów czasowych-0 wymaga dodatkowych parametrów w obiekcie Configuration:

1. `time_column_name`: wymagany parametr, który definiuje nazwę kolumny w danych szkoleniowych zawierających prawidłową serię czasową.
1. `max_horizon`: określa długość czasu, który ma być przewidywany na podstawie okresowości danych szkoleniowych. Na przykład jeśli masz dane szkoleniowe z codziennymi ziarnami czasowymi, możesz określić, jak daleko w dni ma być nadany model.
1. `grain_column_names`: definiuje nazwę kolumn, które zawierają poszczególne dane szeregów czasowych w danych szkoleniowych. Na przykład w przypadku prognozowania sprzedaży określonej marki według sklepu należy zdefiniować kolumny sklepu i marki jako kolumny ziaren. Dla każdego ziarna/grupowania zostanie utworzona oddzielna seria czasowa i prognozy. 

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
                             iterations=10,
                             training_data=train_data,
                             label_column_name=label,
                             n_cross_validations=5,
                             path=project_folder,
                             verbosity=logging.INFO,
                             **time_series_settings)
```

### <a name="ensemble"></a>Konfiguracja kompletna

Modele kompletów są domyślnie włączone i pojawiają się jako ostateczne iteracje przebiegu w zautomatyzowanym przebiegu uczenia maszynowego. Obecnie obsługiwane metody kompletu są głosune i ułożone na stosie. Głosowanie jest implementowane jako odmowa głosu przy użyciu średniej ważonej, a implementacja stosu korzysta z 2 implementacji warstwy, gdzie pierwsza warstwa ma takie same modele jak zestaw do głosowania, a drugi model warstwy jest używany do znalezienia optymalnej kombinacji modele z pierwszej warstwy. Jeśli używasz modeli ONNX **lub** włączono wyjaśnienie modelu, stos zostanie wyłączony i będzie można używać tylko głosu.

Istnieje wiele argumentów domyślnych, które mogą być podane jako `kwargs` w obiekcie `AutoMLConfig`, aby zmienić domyślne zachowanie kompletności stosu.

* `stack_meta_learner_type`: meta-uczyć jest modelem przeszkolonym w danych wyjściowych poszczególnych modeli heterogenicznych. Domyślnie metaers są `LogisticRegression` w przypadku zadań klasyfikacji (lub `LogisticRegressionCV`, jeśli jest włączona funkcja wzajemnego sprawdzania poprawności) i `ElasticNet` dla zadań regresji/prognozowania (lub `ElasticNetCV`, jeśli włączono weryfikację krzyżową). Ten parametr może być jednym z następujących ciągów: `LogisticRegression`, `LogisticRegressionCV`, `LightGBMClassifier`, `ElasticNet`, `ElasticNetCV`, `LightGBMRegressor` lub `LinearRegression`.
* `stack_meta_learner_train_percentage`: określa proporcję zestawu szkoleniowego (przy wyborze typu uczenia i walidacji), które mają zostać zarezerwowane do uczenia się. Wartość domyślna to `0.2`.
* `stack_meta_learner_kwargs`: parametry opcjonalne do przekazania do inicjatora meta. Te parametry i typy parametrów stanowią duplikaty z odpowiedniego konstruktora modelu i są przekazywane do konstruktora modelu.

Poniższy kod przedstawia przykład określania niestandardowego zachowania w obiekcie `AutoMLConfig`.

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
        iterations=20,
        training_data=train_data,
        label_column_name=label,
        n_cross_validations=5,
        **ensemble_settings
        )
```

Uczenie typu "kompletność" jest domyślnie włączone, ale można je wyłączyć za pomocą parametrów logicznych `enable_voting_ensemble` i `enable_stack_ensemble`.

```python
automl_classifier = AutoMLConfig(
        task='classification',
        primary_metric='AUC_weighted',
        iterations=20,
        training_data=data_train,
        label_column_name=label,
        n_cross_validations=5,
        enable_voting_ensemble=False,
        enable_stack_ensemble=False
        )
```

## <a name="run-experiment"></a>Uruchom eksperyment

W przypadku zautomatyzowanej ML utworzysz obiekt `Experiment`, który jest obiektem nazwanym w `Workspace` używanym do uruchamiania eksperymentów.

```python
from azureml.core.experiment import Experiment

ws = Workspace.from_config()

# Choose a name for the experiment and specify the project folder.
experiment_name = 'automl-classification'
project_folder = './sample_projects/automl-classification'

experiment = Experiment(ws, experiment_name)
```

Prześlij eksperyment, aby uruchomić i wygenerować model. Aby wygenerować model, Przekaż `AutoMLConfig` do metody `submit`.

```python
run = experiment.submit(automl_config, show_output=True)
```

>[!NOTE]
>Zależności są najpierw instalowane na nowym komputerze.  Przed wyświetleniem danych wyjściowych może upłynąć do 10 minut.
>Ustawienie wartości `show_output` spowoduje `True` powoduje, że dane wyjściowe są wyświetlane w konsoli.

### <a name="exit-criteria"></a>Kryteria wyjścia
Istnieje kilka opcji, które można zdefiniować, aby zakończyć eksperyment.
1. Brak kryteriów: Jeśli nie określisz żadnych parametrów zakończenia, eksperyment będzie kontynuowany do momentu, gdy nie zostanie wprowadzony żaden kolejny postęp w głównej metryki.
1. Liczba iteracji: zdefiniujesz liczbę iteracji do uruchomienia eksperymentu. Opcjonalnie można dodać `iteration_timeout_minutes`, aby zdefiniować limit czasu w minutach dla każdej iteracji.
1. Zakończ po upływie dłuższego czasu: użycie `experiment_timeout_minutes` w ustawieniach pozwala określić, jak długo w minutach ma być kontynuowane eksperymenty.
1. Zakończ po osiągnięciu wyniku: użycie `experiment_exit_score` zakończy eksperyment po osiągnięciu podstawowego wyniku metryki.

### <a name="explore-model-metrics"></a>Eksplorowanie metryk modelu

Możesz wyświetlić wyniki szkolenia w widżecie lub inline, jeśli jesteś w notesie. Aby uzyskać więcej informacji [, zobacz Śledzenie i szacowanie modeli](how-to-track-experiments.md#view-run-details) .

## <a name="understand-automated-ml-models"></a>Zrozumienie zautomatyzowanych modeli ML

Każdy model wygenerowany przy użyciu zautomatyzowanej ML obejmuje następujące kroki:
+ Zautomatyzowana funkcja inżynierii
+ Skalowanie/Normalizacja i algorytm przy użyciu wartości parametru

Czynimy to przezroczyste, aby uzyskać te informacje z danych wyjściowych fitted_model z zautomatyzowanej ML.

```python
automl_config = AutoMLConfig(…)
automl_run = experiment.submit(automl_config …)
best_run, fitted_model = automl_run.get_output()
```

### <a name="automated-feature-engineering"></a>Zautomatyzowana funkcja inżynierii

Zapoznaj się z listą procesu wstępnego przetwarzania i [zautomatyzowanej funkcji inżynierii](concept-automated-ml.md#preprocess) , która występuje w przypadku wstępnego przetwarzania = true.

Rozważmy następujący przykład:
+ Istnieją 4 funkcje wejściowe: A (numeryczne), B (numeryczne), C (liczbowe), D (DateTime)
+ Funkcja liczbowa C została porzucona, ponieważ jest kolumną identyfikatora ze wszystkimi unikatowymi wartościami
+ Funkcje liczbowe a i B mają brakujące wartości, a tym samym są przypisane przez średnią
+ Funkcja DateTime D jest featurized do 11 różnych wbudowanych funkcji

Użyj tych 2 interfejsów API w pierwszym kroku dopasowanego modelu, aby poznać więcej.  Zapoznaj się z [tym przykładowym notesem](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand).

+ API 1: `get_engineered_feature_names()` zwraca listę nazw wbudowanych funkcji.

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

Poniżej przedstawiono przykładowe dane wyjściowe dla potoku przy użyciu określonego algorytmu (LogisticRegression z RobustScalar, w tym przypadku).

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

<a name="explain"></a>

## <a name="explain-the-model-interpretability"></a>Wyjaśnij model (interpretowanie)

Automatyczne Uczenie maszynowe pozwala zrozumieć znaczenie funkcji.  Podczas procesu szkolenia można uzyskać globalną ważność funkcji dla modelu.  W przypadku scenariuszy klasyfikacji można także uzyskać ważność funkcji na poziomie klasy.  Musisz dostarczyć zestaw danych sprawdzania poprawności (validation_data), aby uzyskać ważność funkcji.

Istnieją dwa sposoby generowania ważności funkcji.

*   Po zakończeniu eksperymentu można użyć metody `explain_model` dla każdej iteracji.

    ```python
    from azureml.train.automl.automlexplainer import explain_model

    shap_values, expected_values, overall_summary, overall_imp, per_class_summary, per_class_imp = \
        explain_model(fitted_model, train_data, test_data)

    #Overall feature importance
    print(overall_imp)
    print(overall_summary)

    #Class-level feature importance
    print(per_class_imp)
    print(per_class_summary)
    ```

*   Aby wyświetlić ważność funkcji dla wszystkich iteracji, Ustaw flagę `model_explainability` na `True` w AutoMLConfig.

    ```python
    automl_config = AutoMLConfig(task='classification',
                                 debug_log='automl_errors.log',
                                 primary_metric='AUC_weighted',
                                 max_time_sec=12000,
                                 iterations=10,
                                 verbosity=logging.INFO,
                                 training_data=train_data,
                                 label_column_name=y_train,
                                 validation_data=test_data,
                                 model_explainability=True,
                                 path=project_folder)
    ```

    Po wykonaniu tej czynności można użyć metody retrieve_model_explanation, aby pobrać ważność funkcji dla określonej iteracji.

    ```python
    from azureml.train.automl.automlexplainer import retrieve_model_explanation

    shap_values, expected_values, overall_summary, overall_imp, per_class_summary, per_class_imp = \
        retrieve_model_explanation(best_run)

    #Overall feature importance
    print(overall_imp)
    print(overall_summary)

    #Class-level feature importance
    print(per_class_imp)
    print(per_class_summary)
    ```

Wyświetl adres URL, aby wyświetlić ważność funkcji przy użyciu obiektu Run:

```
automl_run.get_portal_url()
```

Możesz wizualizować wykres ważności funkcji w obszarze roboczym w Azure Portal lub ze [strony docelowej obszaru roboczego (wersja zapoznawcza)](https://ml.azure.com). Wykres jest również pokazywany w przypadku @no__t używania [widżetu Jupyter](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py) -0 w notesie. Aby dowiedzieć się więcej na temat wykresów, zobacz [Opis zautomatyzowanych wyników uczenia maszynowego](how-to-understand-automated-ml.md).

```Python
from azureml.widgets import RunDetails
RunDetails(automl_run).show()
```

![Wykres ważności funkcji](./media/how-to-configure-auto-train/feature-importance.png)

Aby uzyskać więcej informacji na temat sposobu, w jaki można włączyć wyjaśnienie modelu i znaczenie funkcji w innych obszarach zestawu SDK poza funkcją automatycznej uczenia maszynowego, zapoznaj się z artykułem [koncepcji](machine-learning-interpretability-explainability.md) w zakresie interpretacji.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o tym [, jak i gdzie wdrożyć model](how-to-deploy-and-where.md).

Dowiedz się więcej o tym, [Jak szkolić model regresji z automatycznym uczeniem maszynowym](tutorial-auto-train-models.md) lub [jak korzystać z funkcji automatycznego uczenia maszynowego w ramach zasobu zdalnego](how-to-auto-train-remote.md).
