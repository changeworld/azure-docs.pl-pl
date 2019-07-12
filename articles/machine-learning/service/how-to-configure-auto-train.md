---
title: Tworzenie zautomatyzowanych eksperymentów uczenia Maszynowego
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
ms.openlocfilehash: 0d9019a6b4a32066480a70f72562bc5a7a9a1e8b
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2019
ms.locfileid: "67797646"
---
# <a name="configure-automated-ml-experiments-in-python"></a>Konfigurowanie automatycznych eksperymentów uczenia Maszynowego w języku Python

W tym przewodniku, Dowiedz się, jak zdefiniować różne ustawienia konfiguracji komputera automatycznych eksperymentów przy użyciu uczenia [zestawu SDK usługi Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py). Uczenie maszynowe automatycznych wybiera algorytm i hiperparametrów dla Ciebie i generuje gotowe do wdrożenia modelu. Istnieje kilka opcji, które umożliwiają skonfigurowanie automatycznych eksperymentów uczenia maszynowego.

Aby wyświetlić przykłady automatycznych eksperymentów uczenia maszynowego, zobacz [samouczka: Wytrenuj model klasyfikacji przy użyciu uczenia maszynowego automatycznych](tutorial-auto-train-models.md) lub [uczenia modeli za pomocą automatycznych machine learning w chmurze](how-to-auto-train-remote.md).

Opcje konfiguracji dostępne w automatycznych machine learning:

* Wybierz typ eksperymentu: Klasyfikacji, regresji lub prognozowania szeregu czasowego
* Źródła danych, formatów i pobierania danych
* Wybierz obliczeniowego elementu docelowego: lokalnych lub zdalnych
* Automatyczne usługi machine learning ustawienia eksperymentu
* Uruchom zautomatyzowane eksperymentu uczenia maszynowego
* Eksplorowanie metryk modelu
* Zarejestruj i Wdróż model

Jeśli wolisz nie środowisko pracy z kodem, możesz również [tworzenie zautomatyzowanych maszyny eksperymentów w witrynie Azure portal uczenia](how-to-create-portal-experiments.md).

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
[DNN Classifier](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNClassifier)|[DNN Regressor](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor) | [DNN Regressor](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor)|
[Klasyfikator liniowej DNN](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearClassifier)|[Regresor liniowy](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)|[Regresor liniowy](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)
[Bayesa firmy](https://scikit-learn.org/stable/modules/naive_bayes.html#bernoulli-naive-bayes)|
[Stochastycznego spadku gradientu (wstecznej Propagacji)](https://scikit-learn.org/stable/modules/sgd.html#sgd)|

Użyj `task` parametru w `AutoMLConfig` konstruktora, aby określić typ swojego eksperymentu.

```python
from azureml.train.automl import AutoMLConfig

# task can be one of classification, regression, forecasting
automl_config = AutoMLConfig(task="classification")
```

## <a name="data-source-and-format"></a>Źródło danych i format
Uczenie maszynowe automatycznych obsługuje dane, które znajdują się na komputerze lokalnym lub w chmurze, takich jak Azure Blob Storage. Dane mogą być odczytywane w scikit-informacje formatów obsługiwanych danych. Można odczytywać dane do:
* Tablice Numpy X (cech) i y (Zmienna docelowa lub znany także jako etykieta)
* Pandas dataframe

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

Jeśli używasz zdalnego obliczeń do uruchamiania eksperymentu, pobierania danych muszą być zapakowane w skrypcie języka python w oddzielnych `get_data()`. Ten skrypt jest uruchamiany na zdalne zasoby obliczeniowe, gdzie jest wykonywane automatyczne eksperymentu uczenia maszynowego. `get_data` eliminuje konieczność pobierania danych przez sieć dla każdej iteracji. Bez `get_data`, eksperymentu zakończy się niepowodzeniem po uruchomieniu na zdalnym obliczeń.

Oto przykład `get_data`:

```python
%%writefile $project_folder/get_data.py
import pandas as pd
from sklearn.model_selection import train_test_split
from sklearn.preprocessing import LabelEncoder
def get_data(): # Burning man 2016 data
    df = pd.read_csv("https://automldemods.blob.core.windows.net/datasets/PlayaEvents2016,_1.6MB,_3.4k-rows.cleaned.2.tsv", delimiter="\t", quotechar='"')
    # get integer labels
    le = LabelEncoder()
    le.fit(df["Label"].values)
    y = le.transform(df["Label"].values)
    df = df.drop(["Label"], axis=1)
    df_train, _, y_train, _ = train_test_split(df, y, test_size=0.1, random_state=42)
    return { "X" : df, "y" : y }
```

W swojej `AutoMLConfig` obiektu, należy określić `data_script` parametru i podaj ścieżkę do `get_data` pliku skryptu, podobnie jak na poniższej ilustracji:

```python
automl_config = AutoMLConfig(****, data_script=project_folder + "/get_data.py", **** )
```

`get_data` Skrypt może zwrócić:

Klucz | Typ | Wzajemnie wykluczających się z    | Opis
---|---|---|---
X | Tablica Numpy lub pandas Dataframe | data_train, etykiety kolumn |  Wszystkie funkcje do jego trenowanie za pomocą
Y | Tablica Numpy lub pandas Dataframe |   label   | Etykiety danych w celu jego trenowanie za pomocą. Klasyfikacja powinna być tablicy liczb całkowitych.
X_valid | Tablica Numpy lub pandas Dataframe   | data_train, etykiety | _Opcjonalnie_ funkcji danych, który wchodzi w skład zestawu sprawdzania poprawności. Jeśli nie zostanie określony, X jest podzielony między szkolenie i sprawdzanie poprawności
y_valid |   Tablica Numpy lub pandas Dataframe | data_train, etykiety | _Opcjonalnie_ danych etykietę umożliwiające weryfikację. Jeśli nie zostanie określony, y jest podzielony między szkolenie i sprawdzanie poprawności
sample_weight | Tablica Numpy lub pandas Dataframe |   data_train, etykiety kolumn| _Opcjonalnie_ wartość wagi, dla każdego przykładu. Użyj, jeśli chcesz przypisać różne wagi dla punktów danych
sample_weight_valid | Tablica Numpy lub pandas Dataframe | data_train, etykiety kolumn |    _Opcjonalnie_ wartość wagi, dla każdego przykładu sprawdzania poprawności. Jeśli nie zostanie określony, sample_weight są dzielone na szkolenie i sprawdzanie poprawności
data_train |    Pandas Dataframe |  X, y, X_valid, y_valid |    Wszystkie dane (funkcje i etykiety), do jego trenowanie za pomocą
label | ciąg  | X, y, X_valid, y_valid |  Kolumna data_train reprezentuje etykietę
Kolumny | Tablica ciągów  ||  _Opcjonalnie_ listy dozwolonych kolumn na potrzeby funkcji
cv_splits_indices   | Tablica liczb całkowitych ||  _Opcjonalnie_ listy indeksów do dzielenia danych krzyżowego sprawdzania poprawności

## <a name="train-and-validation-data"></a>Szkolenie i sprawdzanie poprawności danych

Można określić oddzielne train i sprawdzanie poprawności zestawu za pomocą get_data() lub bezpośrednio w `AutoMLConfig` metody.

### <a name="k-folds-cross-validation"></a>K złożeń krzyżowe sprawdzanie poprawności

Użyj `n_cross_validations` ustawienie, aby określić liczbę krzyżowego sprawdzania poprawności. Szkoleniowy zestaw danych zostanie ona podzielona losowo na `n_cross_validations` złożeń taki sam rozmiar. Podczas każdego krzyżowego sprawdzania poprawności round jednym z złożeń będzie służyć do sprawdzania poprawności modelu skonfigurowanych pod kątem w pozostałych złożeń. Ten proces powtarza się dla `n_cross_validations` Zaokrągla do momentu poszczególnych etapów jest używany jeden raz w postanowieniach sprawdzania poprawności. Średnie wyniki dla wszystkich `n_cross_validations` zaokrągla zostaną zgłoszone, a odpowiedni model zostanie retrained, szkolenie dla całego zestawu danych.

### <a name="monte-carlo-cross-validation-repeated-random-sub-sampling"></a>Monte Carlo między weryfikacji (powtarzanych losowego próbkowanie podrzędnych)

Użyj `validation_size` Aby określić procent zestaw danych szkoleniowych, które mają być używane do sprawdzania poprawności i użyj `n_cross_validations` Aby określić liczbę krzyżowego sprawdzania poprawności. Podczas każdego cross weryfikacji round podzestaw rozmiar `validation_size` będą wybierane losowo do sprawdzania poprawności modelu skonfigurowanych pod kątem w pozostałych danych. Ponadto ocenia średnią dla wszystkich `n_cross_validations` zaokrągla zostaną zgłoszone, a odpowiedni model zostanie retrained, szkolenie dla całego zestawu danych. Monte Carlo nie jest obsługiwana w przypadku prognozowania serii czasu.

### <a name="custom-validation-dataset"></a>Zestaw niestandardowego sprawdzania poprawności danych

Użyj niestandardowego sprawdzania poprawności zestawu danych, jeśli losowe podziału nie jest dopuszczalne, zwykle danych szeregów czasowych lub imbalanced danych. Można określić własnego zestawu danych walidacji. Model zostaną ocenione względem zestawu danych walidacji, zamiast losowego zestawu danych.

## <a name="compute-to-run-experiment"></a>Zasoby obliczeniowe, uruchamianie eksperymentu

Następnie określić, gdzie będzie uczony model. Automatyczne eksperymentu uczenia maszynowego, szkolenia można uruchomić na następujących opcji obliczeniowych:
*   Komputer lokalny, takich jak lokalne stacjonarnym lub przenośnym — zazwyczaj, gdy masz mały zestaw danych i są nadal w fazie eksploracji.
*   Komputer zdalny w chmurze — [Machine Learning zarządzane obliczeń Azure](concept-compute-target.md#amlcompute) to zarządzana usługa, która umożliwia szkolenie modeli uczenia maszynowego w klastrach maszyn wirtualnych platformy Azure.

Zobacz [witryny GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning) na przykład celów obliczeń notesów z lokalną i zdalną.

*   Klaster usługi Azure Databricks w subskrypcji platformy Azure. W tym miejscu — więcej informacji można znaleźć [usługi Azure Databricks konfiguracji klastra dla automatycznego ML](how-to-configure-environment.md#azure-databricks)

Zobacz [witryny GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/azure-databricks/automl) na przykład notesów z usługą Azure Databricks.

<a name='configure-experiment'></a>

## <a name="configure-your-experiment-settings"></a>Skonfiguruj ustawienia eksperymentu

Istnieje kilka opcji, które umożliwiają skonfigurowanie Twoje zautomatyzowane eksperymentu uczenia maszynowego. Te parametry są ustawiane przez utworzenie wystąpienia `AutoMLConfig` obiektu. Zobacz [klasy AutoMLConfig](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlconfig?view=azure-ml-py) pełną listę parametrów.

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

Trzy różne `task` wartości parametru określają listy algorytmów do zastosowania.  Użyj `whitelist` lub `blacklist` parametry dodatkowo zmodyfikować iteracji, używając dostępnych algorytmów do dołączania lub wykluczania. Na liście obsługiwanych modeli można znaleźć na [klasy SupportedAlgorithms](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.constants.supportedalgorithms?view=azure-ml-py).

### <a name="primary-metric"></a>Podstawowe metryki
Podstawowy metric; jak pokazano w przykładach Określa metrykę do użycia podczas uczenia modelu do optymalizacji. Podstawową metrykę, którą można wybrać, jest określana przez typ zadania, które wybierzesz. Poniżej znajduje się lista dostępnych metryk.

|Klasyfikacja | Regresji | Prognozowanie szeregów czasowych
|-- |-- |--
|accuracy| spearman_correlation | spearman_correlation
|AUC_weighted | normalized_root_mean_squared_error | normalized_root_mean_squared_error
|average_precision_score_weighted | r2_score | r2_score
|norm_macro_recall | normalized_mean_absolute_error | normalized_mean_absolute_error
|precision_score_weighted |

### <a name="data-preprocessing--featurization"></a>Wstępne przetwarzanie danych i cechowania

W każdej automatycznych eksperymentu uczenia maszynowego, Twoje dane są [automatyczne skalowanie i znormalizowane](concept-automated-ml.md#preprocess) ułatwiające algorytmy działać dobrze.  Jednak można również włączyć dodatkowe przetwarzanie wstępne/cechowania, takich jak brakujące wartości, przypisywania, kodowanie i przekształcenia. [Dowiedz się więcej o jakie cechowania jest dołączony](how-to-create-portal-experiments.md#preprocess).

Aby włączyć ten cechowania, określ `"preprocess": True` dla [ `AutoMLConfig` klasy](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlconfig?view=azure-ml-py).

### <a name="time-series-forecasting"></a>Prognozowanie szeregów czasowych
Typ zadania prognozowania serii czasu masz dodatkowe parametry, aby zdefiniować.
1. time_column_name — jest wymaganym parametrem, który definiuje nazwę kolumny w serii szkoleń danych zawierającego daty/godziny.
1. max_horizon — definiuje czas, który chcesz przewidzieć się okresowość dane szkoleniowe w oparciu. Na przykład jeśli masz dane szkoleniowe codzienne ziarna czasu, jak daleko na zewnątrz zdefiniujesz Days modelu do szkolenia dla.
1. grain_column_names — definiuje nazwy kolumn, które zawierają dane serii czasu danych szkoleniowych. Na przykład jeśli są prognozowania sprzedaży konkretnego witryn zbiorczych w sklepie, zdefiniujesz sklepu i marki kolumny jako kolumny ziarna.

Zobacz przykład te ustawienia są używane poniżej, jest dostępny przykładowy notes [tutaj](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-orange-juice-sales/auto-ml-forecasting-orange-juice-sales.ipynb).

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

## <a name="run-experiment"></a>Uruchamianie eksperymentu

Dla zautomatyzowanych ML musisz utworzyć `Experiment` obiektu, który jest nazwany obiekt w `Workspace` używane do uruchamiania eksperymentów.

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
Istnieje kilka opcji można zdefiniować ukończenie eksperymentu.
1. Kryteriów — Jeżeli nie zdefiniujesz dowolne Zakończ parametry, których eksperymentu będzie kontynuowane, dopóki nie dalsze postęp na swoje podstawowe metryki.
1. Liczba iteracji — należy zdefiniować liczbę iteracji eksperymentu do uruchomienia. Możesz opcjonalnie dodać iteration_timeout_minutes do definiowania limitu czasu w ciągu kilku minut dla każdej iteracji.
1. Zakończ po przez długi czas — przy użyciu experiment_timeout_minutes w ustawieniach, które można zdefiniować, jak długo w ciągu kilku minut, na powinno być kontynuowane eksperymentu w przebiegu.
1. Zakończ po osiągnięciu wyniku — przy użyciu experiment_exit_score, które możesz wykonać eksperyment, po osiągnięciu wynik, w oparciu o swoje podstawowe metryki.

### <a name="explore-model-metrics"></a>Eksplorowanie metryk modelu

Jeśli jesteś w notesie, można wyświetlić wyniki szkolenia w element widget lub wewnętrznej. Zobacz [śledzenia i oceniać modele](how-to-track-experiments.md#view-run-details) Aby uzyskać więcej informacji.

## <a name="understand-automated-ml-models"></a>Omówienie automatycznego modeli uczenia Maszynowego

Każdy model utworzony przy użyciu zautomatyzowanych ML obejmuje następujące kroki:
+ Automatyczne technicznego opracowywania funkcji (Jeśli przetwarzanie wstępne = True)
+ Skalowanie normalizacji i algorytm wartościami hypermeter

Firma Microsoft był on przezroczysty uzyskanie tych informacji z danych wyjściowych fitted_model z automatycznych uczenia Maszynowego.

```python
automl_config = AutoMLConfig(…)
automl_run = experiment.submit(automl_config …)
best_run, fitted_model = automl_run.get_output()
```

### <a name="automated-feature-engineering"></a>Inżynieria funkcji automatycznego

Przejrzyj listę rzeczy, przetwarzania wstępnego i [zautomatyzowane technicznego opracowywania funkcji](concept-automated-ml.md#preprocess) tak się stanie podczas przetwarzania wstępnego = True.

Rozważmy następujący przykład:
+ Istnieją 4 funkcje wejściowe: (Liczbowy) B (liczbowe od), C (liczbowe od), D (Data/godzina)
+ Funkcja liczbowe języka C jest porzucony, ponieważ jest to kolumna Identyfikatora z samymi unikatowymi wartościami
+ Funkcje numeryczne, A i B brakującymi wartościami i dlatego są kalkulacyjne średniej
+ Funkcja DateTime D jest neural do 11 różnych funkcji zaprojektowanych

Użyj tych 2 interfejsy API w pierwszym kroku dopasowanego modelu, aby dowiedzieć się więcej.  Zobacz [ten przykładowy notes](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand).

+ Interfejs API 1: `get_engineered_feature_names()` zwraca listę nazw funkcji zaprojektowanych.

  Użycie:
  ```python
  fitted_model.named_steps['timeseriestransformer']. get_engineered_feature_names ()
  ```

  ```
  Output: ['A', 'B', 'A_WASNULL', 'B_WASNULL', 'year', 'half', 'quarter', 'month', 'day', 'hour', 'am_pm', 'hour12', 'wday', 'qday', 'week']
  ```

  Ta lista zawiera wszystkie nazwy funkcji zaprojektowanych.

  >[!Note]
  >Użyj "timeseriestransformer" dla zadania = "Prognozowanie", użyj innego "datatransformer" dla zadania "regresji" lub "Klasyfikacja".

+ Interfejs API 2: `get_featurization_summary()` zwraca cechowania podsumowania dla wszystkich danych wejściowych funkcji.

  Użycie:
  ```python
  fitted_model.named_steps['timeseriestransformer'].get_featurization_summary()
  ```

  >[!Note]
  >Użyj "timeseriestransformer" dla zadania = "Prognozowanie", użyj innego "datatransformer" dla zadania "regresji" lub "Klasyfikacja".

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
   |RawFeatureName|Nazwa funkcji/kolumny wejściowej z zestawu danych przedstawionego.|
   |TypeDetected|Wykryto typ danych wejściowych funkcji.|
   |Porzucone|Wskazuje, jeśli danych wejściowych funkcji zostało porzucone lub używane.|
   |EngineeringFeatureCount|Liczba funkcji wygenerowane przez inżynierów przekształcenia funkcja automatyczna.|
   |Przekształcenia|Lista zastosowane do wprowadzania funkcji do generowania zaprojektowanych funkcji przekształcenia.|

### <a name="scalingnormalization-and-algorithm-with-hypermeter-values"></a>Skalowanie normalizacji i algorytm hypermeter wartościami:

Aby poznać skalowanie normalizacji i algorytm/hiperparametrycznego wartości dla potoku, należy użyć fitted_model.steps. [Dowiedz się więcej na temat skalowania normalizacji](concept-automated-ml.md#preprocess). Oto przykładowe dane wyjściowe:

```
[('RobustScaler', RobustScaler(copy=True, quantile_range=[10, 90], with_centering=True, with_scaling=True)), ('LogisticRegression', LogisticRegression(C=0.18420699693267145, class_weight='balanced', dual=False, fit_intercept=True, intercept_scaling=1, max_iter=100, multi_class='multinomial', n_jobs=1, penalty='l2', random_state=None, solver='newton-cg', tol=0.0001, verbose=0, warm_start=False))
```

Aby uzyskać więcej szczegółów, funkcja ta pomocnika objętego [ten przykładowy notes](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification/auto-ml-classification.ipynb).

```python
from pprint import pprint
def print_model(model, prefix=""):
    for step in model.steps:
        print(prefix + step[0])
        if hasattr(step[1], 'estimators') and hasattr(step[1], 'weights'):
            pprint({'estimators': list(e[0] for e in step[1].estimators), 'weights': step[1].weights})
            print()
            for estimator in step[1].estimators:
                print_model(estimator[1], estimator[0]+ ' - ')
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

## <a name="explain-the-model-interpretability"></a>Wyjaśniono modelu (współdziałania)

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

Można wizualizować wykresu znaczenie funkcji w obszarze roboczym w witrynie Azure portal. Wykres jest również wyświetlany, gdy za pomocą widżetu Jupyter w notesie. Aby dowiedzieć się więcej na temat wykresów się [artykułu notesów usługi przykładowej usługi Azure Machine Learning.](samples-notebooks.md)

```Python
from azureml.widgets import RunDetails
RunDetails(local_run).show()
```
![Funkcja znaczenie wykresu](./media/how-to-configure-auto-train/feature-importance.png)

Aby uzyskać więcej informacji na temat jak wyjaśnienia modelu i znaczenie funkcję można włączyć w innych obszarach SDK poza uczenia maszynowego automatycznych, zobacz [koncepcji](machine-learning-interpretability-explainability.md) artykuł na temat współdziałania.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [jak i gdzie można wdrożyć model](how-to-deploy-and-where.md).

Dowiedz się więcej o [sposobu uczenia modelu regresji przy użyciu uczenia maszynowego automatyczne](tutorial-auto-train-models.md) lub [sposób trenowania przy użyciu zautomatyzowanego machine learning na zasób zdalny](how-to-auto-train-remote.md).
