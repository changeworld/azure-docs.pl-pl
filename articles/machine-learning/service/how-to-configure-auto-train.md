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
ms.date: 04/08/2019
ms.custom: seodec18
ms.openlocfilehash: 87e1e57a969fc5e65302dcce44231773f7e74b3a
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2019
ms.locfileid: "59548836"
---
# <a name="configure-automated-machine-learning-experiments"></a>Konfigurowanie automatycznych eksperymentów uczenia maszynowego

Uczenie maszynowe automatycznych wybiera algorytm i hiperparametrów dla Ciebie i generuje gotowe do wdrożenia modelu. Istnieje kilka opcji, które umożliwiają skonfigurowanie automatycznych eksperymentów uczenia maszynowego. W tym przewodniku Dowiedz się, jak zdefiniować różne ustawienia konfiguracji.

Aby wyświetlić przykłady automatycznych eksperymentów uczenia maszynowego, zobacz [samouczka: Wytrenuj model klasyfikacji przy użyciu uczenia maszynowego automatycznych](tutorial-auto-train-models.md) lub [uczenia modeli za pomocą automatycznych machine learning w chmurze](how-to-auto-train-remote.md).

Opcje konfiguracji dostępne w automatycznych machine learning:

* Wybierz typ eksperymentu: Klasyfikacji, regresji lub prognozowania szeregu czasowego
* Źródła danych, formatów i pobierania danych
* Wybierz obliczeniowego elementu docelowego: lokalnych lub zdalnych
* Automatyczne usługi machine learning ustawienia eksperymentu
* Uruchom zautomatyzowane eksperymentu uczenia maszynowego
* Eksplorowanie metryk modelu
* Zarejestruj i Wdróż model

## <a name="select-your-experiment-type"></a>Wybierz typ eksperymentu
Przed przystąpieniem do wykonywania eksperymentu, należy określić rodzaj maszyny nauczanym problemem, które są rozwiązywania. Automatyczne machine learning obsługuje typy zadań klasyfikacji, regresji i prognozowania.

Automatyczne machine learning obsługuje następujące algorytmy podczas automatyzacji i dostosowywania procesu. Użytkownik nie ma potrzeby w celu określenia algorytm. Algorytmy DNN są dostępne podczas szkolenia, zautomatyzowane ML nie są kompilowane DNN modeli.

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
[DNN Classifer](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNClassifier)|[DNN Regressor](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor) | [DNN Regressor](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor)|
[Klasyfikator liniowej DNN](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearClassifier)|[Regresor liniowy](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)|[Regresor liniowy](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)
[Bayesa firmy](https://scikit-learn.org/stable/modules/naive_bayes.html#bernoulli-naive-bayes)|
[Stochastycznego spadku gradientu (wstecznej Propagacji)](https://scikit-learn.org/stable/modules/sgd.html#sgd)|


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
    df = pd.read_csv("https://automldemods.blob.core.windows.net/datasets/PlayaEvents2016,_1.6MB,_3.4k-rows.cleaned.2.tsv", delimiter="\t", quotechar='"')
    # get integer labels
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
X_valid | Tablica Numpy lub pandas Dataframe   | data_train, etykiety | _Opcjonalnie_ wszystkie funkcje umożliwiające weryfikację. Jeśli nie zostanie określony, X jest podzielony między szkolenie i sprawdzanie poprawności
y_valid |   Tablica Numpy lub pandas Dataframe | data_train, etykiety | _Opcjonalnie_ danych etykietę umożliwiające weryfikację. Jeśli nie zostanie określony, y jest podzielony między szkolenie i sprawdzanie poprawności
sample_weight | Tablica Numpy lub pandas Dataframe |   data_train, etykiety kolumn| _Opcjonalnie_ wartość wagi, dla każdego przykładu. Użyj, jeśli chcesz przypisać różne wagi dla punktów danych
sample_weight_valid | Tablica Numpy lub pandas Dataframe | data_train, etykiety kolumn |    _Opcjonalnie_ wartość wagi, dla każdego przykładu sprawdzania poprawności. Jeśli nie zostanie określony, sample_weight są dzielone na szkolenie i sprawdzanie poprawności
data_train |    Pandas Dataframe |  X, y, X_valid, y_valid |    Wszystkie dane (funkcje i etykiety), do jego trenowanie za pomocą
label | ciąg  | X, y, X_valid, y_valid |  Kolumna data_train reprezentuje etykietę
Kolumny | Tablica ciągów  ||  _Opcjonalnie_ listy dozwolonych kolumn na potrzeby funkcji
cv_splits_indices   | Tablica liczb całkowitych ||  _Opcjonalnie_ listy indeksów do dzielenia danych krzyżowego sprawdzania poprawności

### <a name="load-and-prepare-data-using-dataprep-sdk"></a>Załadowania i przygotowania danych przy użyciu zestawu SDK przygotowania danych
Automatyczne eksperymentów uczenia maszynowego obsługuje ładowanie danych i przekształcenia przy użyciu przygotowania danych zestawu SDK. Korzystanie z zestawu SDK zapewnia możliwość

>* Ładowanie z wiele typów plików za pomocą analizy parametru wnioskowania (kodowanie, separator, nagłówki)
>* Konwersja typu użycia wnioskowania podczas ładowania pliku
>* Funkcja obsługi połączeń MS SQL Server i usługi Azure Data Lake Storage
>* Dodawanie kolumny za pomocą wyrażenia
>* Naliczenie brakujące wartości
>* Tworzenie kolumn pochodnych według przykładu
>* Filtrowanie
>* Niestandardowe przekształcenia języka Python

Aby dowiedzieć się więcej o danych można znaleźć zestawu sdk przygotowywania [sposobu przygotowania danych do modelowania artykułu](how-to-load-data.md).
Poniżej przedstawiono przykład ładowania danych przy użyciu zestawu sdk przygotowywania danych.
```python
# The data referenced here was pulled from `sklearn.datasets.load_digits()`.
simple_example_data_root = 'https://dprepdata.blob.core.windows.net/automl-notebook-data/'
X = dprep.auto_read_file(simple_example_data_root + 'X.csv').skip(1)  # Remove the header row.
# You can use `auto_read_file` which intelligently figures out delimiters and datatypes of a file.

# Here we read a comma delimited file and convert all columns to integers.
y = dprep.read_csv(simple_example_data_root + 'y.csv').to_long(dprep.ColumnSelector(term='.*', use_regex = True))
```

## <a name="train-and-validation-data"></a>Szkolenie i sprawdzanie poprawności danych

Można określić oddzielne train i sprawdzanie poprawności zestawu za pomocą get_data() lub bezpośrednio w `AutoMLConfig` metody.

## <a name="cross-validation-split-options"></a>Krzyżowego sprawdzania poprawności opcji podziału

### <a name="k-folds-cross-validation"></a>K złożeń krzyżowe sprawdzanie poprawności

Użyj `n_cross_validations` ustawienie, aby określić liczbę krzyżowego sprawdzania poprawności. Szkoleniowy zestaw danych zostanie ona podzielona losowo na `n_cross_validations` złożeń taki sam rozmiar. Podczas każdego krzyżowego sprawdzania poprawności round jednym z złożeń będzie służyć do sprawdzania poprawności modelu skonfigurowanych pod kątem w pozostałych złożeń. Ten proces powtarza się dla `n_cross_validations` Zaokrągla do momentu poszczególnych etapów jest używany jeden raz w postanowieniach sprawdzania poprawności. Średnie wyniki dla wszystkich `n_cross_validations` zaokrągla zostaną zgłoszone, a odpowiedni model zostanie retrained, szkolenie dla całego zestawu danych. 

### <a name="monte-carlo-cross-validation-aka-repeated-random-sub-sampling"></a>Krzyżowe sprawdzanie poprawności Monte Carlo (zwane) Powtórzona losowego próbkowanie podrzędnych)

Użyj `validation_size` Aby określić procent zestaw danych szkoleniowych, które mają być używane do sprawdzania poprawności i użyj `n_cross_validations` Aby określić liczbę krzyżowego sprawdzania poprawności. Podczas każdego cross weryfikacji round podzestaw rozmiar `validation_size` będą wybierane losowo do sprawdzania poprawności modelu skonfigurowanych pod kątem w pozostałych danych. Ponadto ocenia średnią dla wszystkich `n_cross_validations` zaokrągla zostaną zgłoszone, a odpowiedni model zostanie retrained, szkolenie dla całego zestawu danych. Monte Carlo nie jest obsługiwana w przypadku prognozowania serii czasu.

### <a name="custom-validation-dataset"></a>Zestaw niestandardowego sprawdzania poprawności danych

Użyj niestandardowego sprawdzania poprawności zestawu danych, jeśli losowe podziału nie jest dopuszczalne, zwykle danych szeregów czasowych lub imbalanced danych. Można określić własnego zestawu danych walidacji. Model zostaną ocenione względem zestawu danych walidacji, zamiast losowego zestawu danych.

## <a name="compute-to-run-experiment"></a>Zasoby obliczeniowe, uruchamianie eksperymentu

Następnie określić, gdzie będzie uczony model. Automatyczne eksperymentu uczenia maszynowego, szkolenia można uruchomić na następujących opcji obliczeniowych:
*   Komputer lokalny, takich jak lokalne stacjonarnym lub przenośnym — zazwyczaj, gdy masz mały zestaw danych i są nadal w fazie eksploracji.
*   Komputer zdalny w chmurze — [Machine Learning zarządzane obliczeń Azure](concept-azure-machine-learning-architecture.md#managed-and-unmanaged-compute-targets) to zarządzana usługa, która umożliwia szkolenie modeli uczenia maszynowego w klastrach maszyn wirtualnych platformy Azure.

Zobacz [witryny GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning) na przykład celów obliczeń notesów z lokalną i zdalną.

<a name='configure-experiment'></a>

## <a name="configure-your-experiment-settings"></a>Skonfiguruj ustawienia eksperymentu

Istnieje kilka opcji, które umożliwiają skonfigurowanie Twoje zautomatyzowane eksperymentu uczenia maszynowego. Te parametry są ustawiane przez utworzenie wystąpienia `AutoMLConfig` obiektu. Zobacz [klasy AutoMLConfig](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlconfig.automlconfig?view=azure-ml-py) pełną listę parametrów.  

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

Trzy różne `task` wartości parametru określają listy algorytmów do zastosowania.  Użyj `whitelist` lub `blacklist` parametry dodatkowo zmodyfikować iteracji, używając dostępnych algorytmów do dołączania lub wykluczania. Na liście obsługiwanych modeli można znaleźć na [SupportedAlgorithms, klasa](https://docs.microsoft.com/en-us/python/api/azureml-train-automl/azureml.train.automl.constants.supportedalgorithms?view=azure-ml-py)

## <a name="primary-metric"></a>Podstawowe metryki
Podstawowy metric; jak pokazano w przykładach Określa metrykę do użycia podczas uczenia modelu do optymalizacji. Podstawową metrykę, którą można wybrać, jest określana przez typ zadania, które wybierzesz. Poniżej znajduje się lista dostępnych metryk.

|Klasyfikacja | Regresji | Prognozowanie szeregów czasowych
|-- |-- |--
|accuracy| spearman_correlation | spearman_correlation
|AUC_weighted | normalized_root_mean_squared_error | normalized_root_mean_squared_error
|average_precision_score_weighted | r2_score | r2_score
|norm_macro_recall | normalized_mean_absolute_error | normalized_mean_absolute_error
|precision_score_weighted |

## <a name="data-pre-processing-and-featurization"></a>Wstępne przetwarzanie danych i cechowania

Jeśli używasz `preprocess=True`, następujące dane przetwarzania wstępnego kroki są wykonywane automatycznie dla Ciebie:
1.  Upuść dużej kardynalności lub żadne funkcje WARIANCJA
    * Usuwanie funkcji żadnych użytecznych informacji z zestawów szkolenia i sprawdzania poprawności. Obejmują one funkcje wszystkich wartości Brak, tę samą wartość we wszystkich wierszach lub bardzo dużej kardynalności (np. skróty, lub identyfikatory GUID).
1.  Brak przypisywania wartości
    *   W przypadku funkcji numerycznych przypisują brakujące wartości przy użyciu średnią z wartości w kolumnie.
    *   W przypadku kategorii funkcji przypisują brakujące wartości przy użyciu wartości najczęściej.
1.  Generuj dodatkowe funkcje
    * Funkcje daty/godziny: Rok, miesiąc, dzień, dzień tygodnia, dnia rok, kwartał, tydzień roku, godzina, minuta, sekunda.
    * Funkcje tekstowe: Częstotliwość termin oparte na słowo unigram, gram Power bi i tri-gram, liczba automatycznej wektoryzacji.
1.  Przekształcenia i kodowania
    * Funkcje liczbowe z bardzo niewielkiej liczbie unikatowych wartości przekształcone w kategorii funkcji.
    * W zależności od kardynalności kategorii funkcji należy wykonać etykiety kodowania lub (wyznaczania wartości skrótu) hot jeden kodowania.

## <a name="ensemble-models"></a>Modele zespołu
Learning zespołu zwiększa machine learning wyniki i wydajność predykcyjne, łącząc wiele modeli, w przeciwieństwie do używania jednego modeli. Przypadku przy użyciu automatycznego uczenia maszynowego, uczyć modele zespołu przy użyciu [algorytm wybór zespołu Caruana przy użyciu posortowanych inicjowania zespołu](http://www.niculescu-mizil.org/papers/shotgun.icml04.revised.rev2.pdf). Iteracji zespół jest wyświetlana jako ostatniej iteracji przebieg.

## <a name="time-series-forecasting"></a>Prognozowanie szeregów czasowych
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

Przesyłanie eksperymentu do uruchamiania i wygenerować model. Przekaż `AutoMLConfig` do `submit` metodę w celu wygenerowania modelu.

```python
run = experiment.submit(automl_config, show_output=True)
```

>[!NOTE]
>Zależności są najpierw instalowane na nowej maszynie.  Może upłynąć do 10 minut, zanim są wyświetlane dane wyjściowe.
>Ustawienie `show_output` do `True` skutkuje dane wyjściowe są wyświetlane w konsoli.

## <a name="exit-criteria"></a>Kryteria wyjścia 
Istnieje kilka opcji można zdefiniować ukończenie eksperymentu.
1. Kryteriów — Jeżeli nie zdefiniujesz dowolne Zakończ parametry, których eksperymentu będzie kontynuowane, dopóki nie dalsze postęp na swoje podstawowe metryki. 
1. Liczba iteracji — należy zdefiniować liczbę iteracji eksperymentu do uruchomienia. Możesz opcjonalnie dodać iteration_timeout_minutes do definiowania limitu czasu w ciągu kilku minut dla każdej iteracji.
1. Zakończ po przez długi czas — przy użyciu experiment_timeout_minutes w ustawieniach, które można zdefiniować, jak długo w ciągu kilku minut, na powinno być kontynuowane eksperymentu w przebiegu.
1. Zakończ po osiągnięciu wyniku — przy użyciu experiment_exit_score można ukończyć experiement po osiągnięciu wynik, w oparciu o swoje podstawowe metryki.

## <a name="explore-model-metrics"></a>Eksplorowanie metryk modelu
Jeśli jesteś w notesie, można wyświetlić wyniki w element widget lub wewnętrznej. Zobacz [śledzenia i oceniać modele](how-to-track-experiments.md#view-run-details) Aby uzyskać więcej informacji.


### <a name="classification-metrics"></a>Metryki klasyfikacji
Następujące metryki są zapisywane w każdej iteracji dla zadania klasyfikacji.

|Metryka|Opis|Obliczenia|Dodatkowe parametry
--|--|--|--|
AUC_Macro| AUC jest obszarem pod krzywą cechy operacyjne odbiorcy. Makro jest średnią arytmetyczną AUC dla każdej klasy.  | [Obliczanie](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | Średni = "makro"|
AUC_Micro| AUC jest obszarem pod krzywą cechy operacyjne odbiorcy. Micro jest kolumną obliczaną globalnie, łącząc prawdziwie dodatnie i fałszywych alarmów od każdej klasy| [Obliczanie](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | Średni = "micro"|
AUC_Weighted  | AUC jest obszarem pod krzywą cechy operacyjne odbiorcy. Ważona jest średnią arytmetyczną oceny dla każdej klasy, ważona według liczby wystąpień wartość true w każdej klasie| [Obliczanie](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html)|Średni = "ważona"
accuracy|Dokładność jest procent przewidywane etykiety, które dokładnie pasują do etykiety wartość true. |[Obliczanie](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html) |Brak|
average_precision_score_macro|Średnia dokładności znajduje się podsumowanie krzywej precision-recall jako średnią ważoną szczegółowości w opisie w każdej wartości progowej ze wzrostem wycofaniu z poprzedniej wartości progowej, używane jako wagę. Makro jest średnią arytmetyczną wyniku średnią precyzję każdej klasy|[Obliczanie](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|Średni = "makro"|
average_precision_score_micro|Średnia dokładności znajduje się podsumowanie krzywej precision-recall jako średnią ważoną szczegółowości w opisie w każdej wartości progowej ze wzrostem wycofaniu z poprzedniej wartości progowej, używane jako wagę. Micro jest kolumną obliczaną globalnie, łącząc prawdziwie dodatnie i fałszywych alarmów przy każdym odcięcia|[Obliczanie](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|Średni = "micro"|
average_precision_score_weighted|Średnia dokładności znajduje się podsumowanie krzywej precision-recall jako średnią ważoną szczegółowości w opisie w każdej wartości progowej ze wzrostem wycofaniu z poprzedniej wartości progowej, używane jako wagę. Ważona jest średnią arytmetyczną wyniku średnią precyzję dla każdej klasy, ważona według liczby wystąpień wartość true w każdej klasie|[Obliczanie](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|Średni = "ważona"|
balanced_accuracy|Dokładność o zrównoważonym obciążeniu jest średnią arytmetyczną odwołania dla każdej klasy.|[Obliczanie](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|Średni = "makro"|
f1_score_macro|F1 wynik jest harmoniczna dokładności i odwołania. Makro jest średnią arytmetyczną wynik F1 dla każdej klasy|[Obliczanie](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|Średni = "makro"|
f1_score_micro|F1 wynik jest harmoniczna dokładności i odwołania. Micro jest kolumną obliczaną globalnie przez liczenie całkowita prawdziwie dodatnie, fałszywych wyników negatywnych i fałszywych alarmów|[Obliczanie](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|Średni = "micro"|
f1_score_weighted|F1 wynik jest harmoniczna dokładności i odwołania. Średnia ważona przez klasy częstotliwość oceny F1 dla każdej klasy|[Obliczanie](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|Średni = "ważona"|
log_loss|To jest funkcja utraty używanych w regresji logistycznej (wielomian) i rozszerzenia takich jak sieci neuronowych, zdefiniowane jako ujemny dziennika prawdopodobieństwo true etykiet podane przewidywania prawdopodobieństwa klasyfikatora. Dla jednego przykładu z true etykiety yt w {0,1} oraz szacowane prawdopodobieństwa yp tego yt = 1, utraty dziennika jest - dziennika P (yt&#124;yp) =-(yt log(yp) + (1 - yt) dziennika (1 - yp))|[Obliczanie](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.log_loss.html)|Brak|
norm_macro_recall|Odwołaj znormalizowane — makro jest makro odwołania znormalizowane, dzięki czemu losowe wydajności ma wynik 0 i doskonałe wydajności ma wynik 1. Jest to osiągane przez norm_macro_recall: = (recall_score_macro - R) /(1-R), gdzie R jest oczekiwana wartość recall_score_macro dla prognoz losowy (czyli R = 0,5 dla klasyfikacji binarnej) i R=(1/C) klasy C klasyfikacji problemów|[Obliczanie](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|Średni = "makro" a następnie (recall_score_macro - R) /(1-R), gdzie R jest oczekiwana wartość recall_score_macro dla prognoz losowy (czyli R = 0,5 dla klasyfikacji binarnej) i R=(1/C) klasy C klasyfikacji problemów|
precision_score_macro|Dokładność jest procent elementy oznaczone jako niektórych klas, które faktycznie są w tej klasie. Makro jest średnią arytmetyczną dokładności dla każdej klasy|[Obliczanie](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|Średni = "makro"|
precision_score_micro|Dokładność jest procent elementy oznaczone jako niektórych klas, które faktycznie są w tej klasie. Micro jest kolumną obliczaną globalnie przez liczenie całkowita prawdziwie dodatnie i fałszywych alarmów|[Obliczanie](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|Średni = "micro"|
precision_score_weighted|Dokładność jest procent elementy oznaczone jako niektórych klas, które faktycznie są w tej klasie. Ważona jest średnią arytmetyczną dokładności dla każdej klasy, ważona według liczby wystąpień wartość true w każdej klasie|[Obliczanie](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|Średni = "ważona"|
recall_score_macro|Odwołanie jest procent elementy rzeczywiście w niektórych klas, które są oznaczone etykietami poprawnie. Makro jest średnią arytmetyczną odwołania dla każdej klasy|[Obliczanie](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|Średni = "makro"|
recall_score_micro|Odwołanie jest procent elementy rzeczywiście w niektórych klas, które są oznaczone etykietami poprawnie. Micro jest kolumną obliczaną globalnie przez liczenie całkowita prawdziwie dodatnie, fałszywych wyników negatywnych|[Obliczanie](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|Średni = "micro"|
recall_score_weighted|Odwołanie jest procent elementy rzeczywiście w niektórych klas, które są oznaczone etykietami poprawnie. Ważona jest średnią arytmetyczną odwołania dla każdej klasy, ważona według liczby wystąpień wartość true w każdej klasie|[Obliczanie](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|Średni = "ważona"|
weighted_accuracy|Ważona dokładności jest dokładności, gdzie wagę każdy przykład jest równe część wystąpień wartość true, w tym przykładzie wartość true, klasa|[Obliczanie](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html)|sample_weight jest wektorem równą część tej klasy, dla każdego elementu w elemencie docelowym|

### <a name="regression-and-time-series-forecasting-metrics"></a>Regresji i czas serii Prognozowanie metryki
Następujące metryki są zapisywane w każdej iteracji regresji lub zadanie prognozowania.

|Metryka|Opis|Obliczenia|Dodatkowe parametry
--|--|--|--|
explained_variance|Wariancja wyjaśniono to modelu matematycznego kont odmiany danego zestawu danych. Jest to procent spadek wariancji oryginalne dane do wariancję błędy. Średnia błędów wynosi 0, jest równa wyjaśniono wariancji.|[Obliczanie](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.explained_variance_score.html)|Brak|
r2_score|R2 to określenie lub procent redukcji kwadratów błędów w porównaniu z modelem linii bazowej, który wyprowadza średniej. Średnia błędów wynosi 0, jest równa wyjaśniono wariancji.|[Obliczanie](https://scikit-learn.org/0.16/modules/generated/sklearn.metrics.r2_score.html)|Brak|
spearman_correlation|Korelacja metodą Spearman jest miarą nonparametric monotonicity relacji między dwoma zestawami danych. W przeciwieństwie do korelacji Pearsona korelacji metodą Spearman nie zakłada, że oba zestawy danych zwykle są rozpowszechniane. Podobnie jak inne współczynniki korelacji ta różni się od -1 do + 1 od 0, co oznacza brak korelacji. Korelacji -1 lub + 1 oznacza dokładne monotoniczny relacji. Dodatnia korelacji oznacza, że x rosną, więc nie y. Korelacji ujemna oznacza, że x rosną, y zmniejsza.|[Obliczanie](https://docs.scipy.org/doc/scipy-0.16.1/reference/generated/scipy.stats.spearmanr.html)|Brak|
mean_absolute_error|Oznacza to, że błąd absolutny jest oczekiwanej wartości bezwzględnej wartości różnicy między obiektu docelowego i prognozowania|[Obliczanie](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_absolute_error.html)|Brak|
normalized_mean_absolute_error|Znormalizowany średni bezwzględny błąd to średni bezwzględny błąd podzielona przez zakres danych|[Obliczanie](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_absolute_error.html)|Podziel według zakresu danych|
median_absolute_error|Mediana bezwzględny błąd jest medianę wszystkich bezwzględnych różnic między obiektu docelowego i prognozowania. To jest odporny elementy odstające.|[Obliczanie](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.median_absolute_error.html)|Brak|
normalized_median_absolute_error|Znormalizowana mediany bezwzględny błąd jest mediany błąd absolutny podzielona przez zakres danych|[Obliczanie](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.median_absolute_error.html)|Podziel według zakresu danych|
root_mean_squared_error|Główny oznacza to, że błąd kwadratów jest pierwiastek kwadratowy z oczekiwanym kwadratu różnicy między obiektu docelowego i prognozowania|[Obliczanie](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_error.html)|Brak|
normalized_root_mean_squared_error|Znormalizowana głównego oznacza to, że błąd kwadratów błędu głównego średniej kwadratów podzielona przez zakres danych|[Obliczanie](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_error.html)|Podziel według zakresu danych|
root_mean_squared_log_error|Główny oznaczać błędów w dzienniku kwadratów pierwiastek kwadratowy z oczekiwanym błędem logarytmicznej kwadrat|[Obliczanie](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_log_error.html)|Brak|
normalized_root_mean_squared_log_error|Znormalizowane błąd średniej kwadratów dziennika głównego jest błąd średniej kwadratów dziennika głównego podzielona przez zakres danych|[Obliczanie](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_log_error.html)|Podziel według zakresu danych|

## <a name="explain-the-model"></a>Wyjaśniono modelu

Chociaż funkcje uczenia maszynowego automatycznych są ogólnie dostępne, **funkcja explainability modelu jest nadal w publicznej wersji zapoznawczej.**

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

```python
from azureml.widgets import RunDetails
RunDetails(local_run).show()
```
![Funkcja znaczenie wykresu](./media/how-to-configure-auto-train/feature-importance.png)

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o [jak i gdzie można wdrożyć model](how-to-deploy-and-where.md).

Dowiedz się więcej o [sposobu uczenia modelu regresji przy użyciu uczenia maszynowego automatyczne](tutorial-auto-train-models.md) lub [sposób trenowania przy użyciu zautomatyzowanego machine learning na zasób zdalny](how-to-auto-train-remote.md).
