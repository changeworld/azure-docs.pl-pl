---
title: Tworzenie zautomatyzowanych eksperymentów ML
titleSuffix: Azure Machine Learning service
description: Uczenie maszynowe automatycznych wybiera algorytm dla Ciebie i generuje gotowe do wdrożenia modelu. Dowiedz się, opcje, które umożliwiają skonfigurowanie automatycznych eksperymentów uczenia maszynowego.
author: nacharya1
ms.author: nilesha
ms.reviewer: sgilley
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 07/10/2019
ms.custom: seodec18
ms.openlocfilehash: 6e29e0f89d9270a143d48cf6e85b479813e19d9d
ms.sourcegitcommit: fe50db9c686d14eec75819f52a8e8d30d8ea725b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/14/2019
ms.locfileid: "69013643"
---
# <a name="configure-automated-ml-experiments-in-python"></a>Konfigurowanie zautomatyzowanych eksperymentów ML w języku Python

W tym przewodniku dowiesz się, jak definiować różne ustawienia konfiguracji zautomatyzowanych eksperymentów uczenia maszynowego za pomocą [zestawu SDK Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py). Uczenie maszynowe automatycznych wybiera algorytm i hiperparametrów dla Ciebie i generuje gotowe do wdrożenia modelu. Istnieje kilka opcji, które umożliwiają skonfigurowanie automatycznych eksperymentów uczenia maszynowego.

Aby wyświetlić przykłady zautomatyzowanych eksperymentów dotyczących uczenia maszynowego, zobacz [samouczek: Zapoznaj się z modelem klasyfikacji przy](tutorial-auto-train-models.md) użyciu zautomatyzowanego uczenia maszynowego lub [modeli szkoleń z automatycznym uczeniem maszynowym w chmurze](how-to-auto-train-remote.md).

Opcje konfiguracji dostępne w automatycznych machine learning:

* Wybierz typ eksperymentu: Prognoza klasyfikacji, regresji lub szeregów czasowych
* Źródła danych, formatów i pobierania danych
* Wybierz obliczeniowego elementu docelowego: lokalnych lub zdalnych
* Automatyczne usługi machine learning ustawienia eksperymentu
* Uruchom zautomatyzowane eksperymentu uczenia maszynowego
* Eksplorowanie metryk modelu
* Zarejestruj i Wdróż model

Jeśli wolisz nie używać kodu, możesz również [utworzyć automatyczne eksperymenty uczenia maszynowego w Azure Portal](how-to-create-portal-experiments.md).

## <a name="select-your-experiment-type"></a>Wybierz typ eksperymentu

Przed przystąpieniem do wykonywania eksperymentu, należy określić rodzaj maszyny nauczanym problemem, które są rozwiązywania. Automatyczne machine learning obsługuje typy zadań klasyfikacji, regresji i prognozowania.

Automatyczne machine learning obsługuje następujące algorytmy podczas automatyzacji i dostosowywania procesu. Użytkownik nie ma potrzeby w celu określenia algorytm.

Klasyfikacja | Regresji | Prognozowanie szeregów czasowych
|-- |-- |--
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
[Bayesa firmy](https://scikit-learn.org/stable/modules/naive_bayes.html#bernoulli-naive-bayes)|
[Stochastycznego spadku gradientu (wstecznej Propagacji)](https://scikit-learn.org/stable/modules/sgd.html#sgd)|

`task` Użyj parametru `AutoMLConfig` w konstruktorze, aby określić typ eksperymentu.

```python
from azureml.train.automl import AutoMLConfig

# task can be one of classification, regression, forecasting
automl_config = AutoMLConfig(task="classification")
```

## <a name="data-source-and-format"></a>Źródło danych i format
Uczenie maszynowe automatycznych obsługuje dane, które znajdują się na komputerze lokalnym lub w chmurze, takich jak Azure Blob Storage. Dane mogą być odczytywane w scikit-informacje formatów obsługiwanych danych. Można odczytywać dane do:
* Tablice Numpy X (cech) i y (Zmienna docelowa lub znany także jako etykieta)
* Pandas dataframe

>[!Important]
> Wymagania dotyczące danych szkoleniowych:
>* Dane muszą być w formie tabelarycznej.
>* Wartość, która ma zostać przewidywalna (kolumna docelowa), musi być obecna w danych.

Przykłady:

*   Tablice Numpy

    ```python
    digits = datasets.load_digits()
    X_digits = digits.data
    y_digits = digits.target
    ```

*   Pandas dataframe

    ```python
    import pandas as pd
    from sklearn.model_selection import train_test_split
    df = pd.read_csv("https://automldemods.blob.core.windows.net/datasets/PlayaEvents2016,_1.6MB,_3.4k-rows.cleaned.2.tsv", delimiter="\t", quotechar='"')
    # get integer labels
    y = df["Label"]
    df = df.drop(["Label"], axis=1)
    df_train, _, y_train, _ = train_test_split(df, y, test_size=0.1, random_state=42)
    ```

## <a name="fetch-data-for-running-experiment-on-remote-compute"></a>Pobieranie danych do uruchamiania eksperymentów na zdalnego obliczeń

W przypadku wykonywania zdalnego należy udostępnić dane ze zdalnego obliczenia. Można to zrobić, przekazując dane do magazynu danych.

Oto przykład użycia `datastore`:

```python
    import pandas as pd
    from sklearn import datasets

    data_train = datasets.load_digits()

    pd.DataFrame(data_train.data[100:,:]).to_csv("data/X_train.csv", index=False)
    pd.DataFrame(data_train.target[100:]).to_csv("data/y_train.csv", index=False)

    ds = ws.get_default_datastore()
    ds.upload(src_dir='./data', target_path='digitsdata', overwrite=True, show_progress=True)
```

### <a name="define-dprep-references"></a>Definiuj odwołania dprep

Zdefiniuj X i y jako odwołanie dprep, które zostaną przesłane do zautomatyzowanego obiektu uczenia `AutoMLConfig` maszynowego podobnego do poniższego:

```python

    X = dprep.auto_read_file(path=ds.path('digitsdata/X_train.csv'))
    y = dprep.auto_read_file(path=ds.path('digitsdata/y_train.csv'))


    automl_config = AutoMLConfig(task = 'classification',
                                 debug_log = 'automl_errors.log',
                                 path = project_folder,
                                 run_configuration=conda_run_config,
                                 X = X,
                                 y = y,
                                 **automl_settings
                                )
```

## <a name="train-and-validation-data"></a>Szkolenie i sprawdzanie poprawności danych

Możesz określić oddzielne pouczenie i zestaw walidacji bezpośrednio `AutoMLConfig` w metodzie.

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

Zapoznaj się z [witryną GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning) na przykład notesów z lokalnymi i zdalnymi obiektami docelowymi obliczeniowymi.

*   Klaster Azure Databricks w ramach subskrypcji platformy Azure. Więcej szczegółów można znaleźć tutaj — [instalator Azure Databricks klaster dla zautomatyzowanej ml](how-to-configure-environment.md#azure-databricks)

Odwiedź [witrynę usługi GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/azure-databricks/automl) , aby zapoznać się z przykładami notesów z Azure Databricks.

<a name='configure-experiment'></a>

## <a name="configure-your-experiment-settings"></a>Skonfiguruj ustawienia eksperymentu

Istnieje kilka opcji, które umożliwiają skonfigurowanie Twoje zautomatyzowane eksperymentu uczenia maszynowego. Te parametry są ustawiane przez utworzenie wystąpienia `AutoMLConfig` obiektu. Zapoznaj się z pełną listą parametrów w [klasie AutoMLConfig](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlconfig?view=azure-ml-py) .

Oto niektóre przykłady:

1.  Eksperymentu klasyfikacji, przy użyciu AUC ważona jako podstawowe metryki i maksymalny czas z 12 000 sekund na iterację eksperymentu, aby zakończyć po 50 iteracji i 2 złożeń krzyżowego sprawdzania poprawności.

    ```python
    automl_classifier = AutoMLConfig(
        task='classification',
        primary_metric='AUC_weighted',
        max_time_sec=12000,
        iterations=50,
        blacklist_models='XGBoostClassifier',
        X=X,
        y=y,
        n_cross_validations=2)
    ```
2.  Poniżej znajduje się przykład regresji zestaw eksperymentu do końca po 100 iteracji, z każdą iteracją, trwających maksymalnie 600 sekund z 5 weryfikacji wielu złożeń.

    ```python
    automl_regressor = AutoMLConfig(
        task='regression',
        max_time_sec=600,
        iterations=100,
        whitelist_models='kNN regressor'
        primary_metric='r2_score',
        X=X,
        y=y,
        n_cross_validations=5)
    ```

Trzy różne `task` wartości parametrów określają listę algorytmów, które mają zostać zastosowane.  Użyj parametrów `blacklist` lub, aby kontynuować modyfikowanie iteracji z dostępnymi algorytmami do dołączania lub wykluczania. `whitelist` Listę obsługiwanych modeli można znaleźć w [klasie SupportedAlgorithms](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.constants.supportedalgorithms?view=azure-ml-py).

### <a name="primary-metric"></a>Podstawowe metryki
Metryka podstawowa; jak pokazano w powyższych przykładach, określa metrykę, która ma być używana podczas szkolenia modelu na potrzeby optymalizacji. Podstawowa metryka, którą można wybrać, zależy od wybranego typu zadania. Poniżej znajduje się lista dostępnych metryk.

|Klasyfikacja | Regresji | Prognozowanie szeregów czasowych
|-- |-- |--
|accuracy| spearman_correlation | spearman_correlation
|AUC_weighted | normalized_root_mean_squared_error | normalized_root_mean_squared_error
|average_precision_score_weighted | r2_score | r2_score
|norm_macro_recall | normalized_mean_absolute_error | normalized_mean_absolute_error
|precision_score_weighted |

### <a name="data-preprocessing--featurization"></a>Przetwarzanie wstępne danych & cechowania

W każdym automatycznym doświadczeniu uczenia maszynowego Twoje dane są [automatycznie skalowane i znormalizowane](concept-automated-ml.md#preprocess) w celu zapewnienia prawidłowego wykonywania algorytmów.  Można jednak włączyć dodatkowe przetwarzanie wstępne/cechowania, na przykład brakujące wartości, które nie przypisywania, kodowania i transformacji. [Dowiedz się więcej na temat tego, co obejmuje cechowania](how-to-create-portal-experiments.md#preprocess).

Aby włączyć tę cechowania, określ `"preprocess": True` [ `AutoMLConfig` dla klasy](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlconfig?view=azure-ml-py).

> [!NOTE]
> Zautomatyzowane kroki wstępnego przetwarzania w usłudze Machine Learning (normalizacja funkcji, obsługa brakujących danych, konwertowanie tekstu na liczbowe itp.) staje się częścią modelu źródłowego. Przy użyciu modelu dla prognoz te same kroki przetwarzania wstępnego zastosowane podczas uczenia są automatycznie stosowane do danych wejściowych.

### <a name="time-series-forecasting"></a>Prognozowanie szeregów czasowych
W przypadku typu zadania prognozowania szeregów czasowych masz dodatkowe parametry do zdefiniowania.
1. time_column_name — jest to parametr wymagany, który definiuje nazwę kolumny w danych szkoleniowych zawierających serie daty/godziny.
1. max_horizon — określa długość czasu, który ma być przewidywany na podstawie okresowości danych szkoleniowych. Na przykład jeśli masz dane szkoleniowe z codziennymi ziarnami czasowymi, możesz określić, jak daleko w dni ma być nadany model.
1. grain_column_names — definiuje nazwę kolumn, które zawierają poszczególne dane szeregów czasowych w danych szkoleniowych. Na przykład w przypadku prognozowania sprzedaży określonej marki według sklepu należy zdefiniować kolumny sklepu i marki jako kolumny ziaren.

Zobacz przykład tych ustawień, które są używane poniżej, przykład notesu jest dostępny [tutaj](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-orange-juice-sales/auto-ml-forecasting-orange-juice-sales.ipynb).

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

automl_config = AutoMLConfig(task='forecasting',
                             debug_log='automl_oj_sales_errors.log',
                             primary_metric='normalized_root_mean_squared_error',
                             iterations=10,
                             X=X_train,
                             y=y_train,
                             n_cross_validations=5,
                             path=project_folder,
                             verbosity=logging.INFO,
                             **time_series_settings)
```

### <a name="ensemble"></a>Konfiguracja kompletna

Modele kompletów są domyślnie włączone i pojawiają się jako ostateczne iteracje przebiegu w zautomatyzowanym przebiegu uczenia maszynowego. Obecnie obsługiwane metody kompletu są głosune i ułożone na stosie. Głosowanie jest implementowane jako odmowa głosu przy użyciu średniej ważonej, a implementacja stosu korzysta z 2 implementacji warstwy, gdzie pierwsza warstwa ma takie same modele jak zestaw do głosowania, a drugi model warstwy jest używany do znalezienia optymalnej kombinacji modele z pierwszej warstwy. Jeśli używasz modeli ONNX **lub** włączono wyjaśnienie modelu, stos zostanie wyłączony i będzie można używać tylko głosu.

Istnieje wiele argumentów domyślnych, które mogą być podane jako `kwargs` `AutoMLConfig` obiekt w celu zmiany domyślnego zachowania kompletności stosu.

* `stack_meta_learner_type`: meta-uczyć się to model przeszkolony w danych wyjściowych poszczególnych modeli heterogenicznych. `LogisticRegression` Domyślne meta uczy są przeznaczone do zadań klasyfikacji (lub `LogisticRegressionCV` w przypadku włączenia wzajemnej walidacji) oraz `ElasticNet` dla zadań regresji/prognozowania ( `ElasticNetCV` lub w przypadku włączenia weryfikacji krzyżowej). Ten parametr może być jednym z `LogisticRegression`następujących ciągów:, `LogisticRegressionCV`, `LightGBMRegressor` `ElasticNetCV` `LightGBMClassifier` `ElasticNet`,,,, lub `LinearRegression`.
* `stack_meta_learner_train_percentage`: określa proporcję zestawu szkoleniowego (podczas wybierania typu uczenia i walidacji), które mają zostać zarezerwowane do uczenia się. Wartość domyślna to `0.2`.
* `stack_meta_learner_kwargs`: parametry opcjonalne do przekazania do inicjatora meta. Te parametry i typy parametrów stanowią duplikaty z odpowiedniego konstruktora modelu i są przekazywane do konstruktora modelu.

Poniższy kod przedstawia przykład określania zachowania niestandardowego w `AutoMLConfig` obiekcie.

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
        X=X_train,
        y=y_train,
        n_cross_validations=5,
        **ensemble_settings
        )
```

Szkolenia w ramach usługi jest domyślnie włączone, ale można je wyłączyć za pomocą `enable_voting_ensemble` parametrów logicznych i. `enable_stack_ensemble`

```python
automl_classifier = AutoMLConfig(
        task='classification',
        primary_metric='AUC_weighted',
        iterations=20,
        X=X_train,
        y=y_train,
        n_cross_validations=5,
        enable_voting_ensemble=False,
        enable_stack_ensemble=False
        )
```

## <a name="run-experiment"></a>Uruchamianie eksperymentu

W przypadku zautomatyzowanej ml utworzysz `Experiment` obiekt, który jest obiektem nazwanym `Workspace` w używanym do uruchamiania eksperymentów.

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
1. Brak kryteriów — Jeśli nie określisz żadnych parametrów zakończenia, eksperyment będzie kontynuowany do momentu, gdy nie zostanie wprowadzony żaden kolejny postęp w głównej metryki.
1. Liczba iteracji — definiujesz liczbę iteracji do uruchomienia eksperymentu. Możesz opcjonalnie dodać iteration_timeout_minutes, aby zdefiniować limit czasu w minutach dla każdej iteracji.
1. Zakończ po upływie czasu korzystania z usługi experiment_timeout_minutes w ustawieniach możesz określić, jak długo w minutach ma być kontynuowane eksperymenty.
1. Zakończ po osiągnięciu wyniku — za pomocą experiment_exit_score można wybrać zakończenie eksperymentu po osiągnięciu oceny na podstawie podstawowej metryki.

### <a name="explore-model-metrics"></a>Eksplorowanie metryk modelu

Możesz wyświetlić wyniki szkolenia w widżecie lub inline, jeśli jesteś w notesie. Zobacz [śledzenia i oceniać modele](how-to-track-experiments.md#view-run-details) Aby uzyskać więcej informacji.

## <a name="understand-automated-ml-models"></a>Zrozumienie zautomatyzowanych modeli ML

Każdy model wygenerowany przy użyciu zautomatyzowanej ML obejmuje następujące kroki:
+ Zautomatyzowana funkcja inżynierii
+ Skalowanie/Normalizacja i algorytm przy użyciu wartości z przelicznikiem

Czynimy to przezroczyste, aby uzyskać te informacje z danych wyjściowych fitted_model z zautomatyzowanej ML.

```python
automl_config = AutoMLConfig(…)
automl_run = experiment.submit(automl_config …)
best_run, fitted_model = automl_run.get_output()
```

### <a name="automated-feature-engineering"></a>Zautomatyzowana funkcja inżynierii

Zapoznaj się z listą procesu wstępnego przetwarzania i [zautomatyzowanej funkcji inżynierii](concept-automated-ml.md#preprocess) , która występuje w przypadku wstępnego przetwarzania = true.

Rozważmy następujący przykład:
+ Dostępne są 4 funkcje wejściowe: A (numerycznie), B (numeryczne), C (numeryczne), D (DateTime)
+ Funkcja liczbowa C została porzucona, ponieważ jest kolumną identyfikatora ze wszystkimi unikatowymi wartościami
+ Funkcje liczbowe a i B mają brakujące wartości, a tym samym są przypisane przez średnią
+ Funkcja DateTime D jest featurized do 11 różnych wbudowanych funkcji

Użyj tych 2 interfejsów API w pierwszym kroku dopasowanego modelu, aby poznać więcej.  Zapoznaj się z [tym przykładowym notesem](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand).

+ Interfejs API 1 `get_engineered_feature_names()` : zwraca listę nazw przytworzonych funkcji.

  Sposób użycia:
  ```python
  fitted_model.named_steps['timeseriestransformer']. get_engineered_feature_names ()
  ```

  ```
  Output: ['A', 'B', 'A_WASNULL', 'B_WASNULL', 'year', 'half', 'quarter', 'month', 'day', 'hour', 'am_pm', 'hour12', 'wday', 'qday', 'week']
  ```

  Ta lista zawiera wszystkie zaprojektowane nazwy funkcji.

  >[!Note]
  >Użyj elementu "timeseriestransformer" dla zadania = "prognozowanie", w przeciwnym razie użyj "datatransformer" dla zadania "regresja" lub "Klasyfikacja".

+ API 2: `get_featurization_summary()` zwraca podsumowanie cechowania dla wszystkich funkcji wejściowych.

  Sposób użycia:
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

   Gdzie:

   |Output|Definicja|
   |----|--------|
   |RawFeatureName|Podano nazwę funkcji/kolumny wejściowej z zestawu danych.|
   |TypeDetected|Wykryto typ danych funkcji wejściowej.|
   |Porzucony|Wskazuje, czy funkcja wejściowa została porzucona lub użyta.|
   |EngineeringFeatureCount|Liczba funkcji generowanych przez automatyczne transformacje inżynieryjnych funkcji.|
   |Przekształcenia|Lista transformacji zastosowanych do funkcji wejściowych do generowania przetworzonych funkcji.|

### <a name="scalingnormalization-and-algorithm-with-hypermeter-values"></a>Skalowanie/Normalizacja i algorytm przy użyciu wartości z przelicznikiem:

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

Uczenie maszynowe automatyczne pozwala zrozumieć znaczenie funkcji.  W trakcie szkolenia można uzyskać funkcji globalnych znaczenie dla modelu.  W przypadku scenariuszy klasyfikacji można również uzyskać funkcję klasy na poziomie ważności.  Należy podać zestaw danych (X_valid), można pobrać znaczenie funkcji sprawdzania poprawności.

Istnieją dwa sposoby generowania funkcji znaczenie.

*   Po zakończeniu eksperymentu można użyć `explain_model` metody na dowolną iterację.

    ```python
    from azureml.train.automl.automlexplainer import explain_model

    shap_values, expected_values, overall_summary, overall_imp, per_class_summary, per_class_imp = \
        explain_model(fitted_model, X_train, X_test)

    #Overall feature importance
    print(overall_imp)
    print(overall_summary)

    #Class-level feature importance
    print(per_class_imp)
    print(per_class_summary)
    ```

*   Aby wyświetlić znaczenie funkcji dla wszystkich iteracji, ustaw `model_explainability` flaga `True` w AutoMLConfig.

    ```python
    automl_config = AutoMLConfig(task = 'classification',
                                 debug_log = 'automl_errors.log',
                                 primary_metric = 'AUC_weighted',
                                 max_time_sec = 12000,
                                 iterations = 10,
                                 verbosity = logging.INFO,
                                 X = X_train,
                                 y = y_train,
                                 X_valid = X_test,
                                 y_valid = y_test,
                                 model_explainability=True,
                                 path=project_folder)
    ```

    Po zakończeniu retrieve_model_explanation metoda służy do pobierania funkcji znaczenie dla określonej iteracji.

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

Można wizualizować wykresu znaczenie funkcji w obszarze roboczym w witrynie Azure portal. Wyświetl adres URL przy użyciu obiektu Run:

```
automl_run.get_portal_url()
```

Można wizualizować wykresu znaczenie funkcji w obszarze roboczym w witrynie Azure portal. Wykres jest również pokazywany przy użyciu `RunDetails` [widżetu Jupyter](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py) w notesie. Aby dowiedzieć się więcej na temat wykresów, zobacz [Opis zautomatyzowanych wyników uczenia maszynowego](how-to-understand-automated-ml.md).

```Python
from azureml.widgets import RunDetails
RunDetails(automl_run).show()
```

![Funkcja znaczenie wykresu](./media/how-to-configure-auto-train/feature-importance.png)

Aby uzyskać więcej informacji na temat sposobu, w jaki można włączyć wyjaśnienie modelu i znaczenie funkcji w innych obszarach zestawu SDK poza funkcją automatycznej uczenia maszynowego, zapoznaj się z artykułem [koncepcji](machine-learning-interpretability-explainability.md) w zakresie interpretacji.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [jak i gdzie można wdrożyć model](how-to-deploy-and-where.md).

Dowiedz się więcej o tym, [Jak szkolić model regresji z automatycznym uczeniem maszynowym](tutorial-auto-train-models.md) lub [jak korzystać z funkcji automatycznego uczenia maszynowego w ramach zasobu zdalnego](how-to-auto-train-remote.md).
