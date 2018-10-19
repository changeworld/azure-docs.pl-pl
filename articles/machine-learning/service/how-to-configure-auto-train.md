---
title: Skonfiguruj Twoje zautomatyzowane eksperymentu uczenia maszynowego — Azure Machine Learning
description: Uczenie maszynowe automatycznych wybiera algorytm dla Ciebie i generuje gotowe do wdrożenia modelu. Dowiedz się, opcje, które umożliwiają skonfigurowanie automatycznych eksperymentów uczenia maszynowego.
author: nacharya1
ms.author: nilesha
ms.reviewer: sgilley
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 1aeb1315cfafbcdf3507a6e49d71e1f1e69b537c
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/19/2018
ms.locfileid: "49430191"
---
# <a name="configure-your-automated-machine-learning-experiment"></a>Skonfiguruj Twoje zautomatyzowane eksperymentu uczenia maszynowego

Automatyczne usługi machine learning (zautomatyzowane ML) wybiera algorytm i hiperparametrów dla Ciebie i generuje gotowe do wdrożenia modelu. Model może być pobierany na dalsze można również dostosować. Istnieje kilka opcji, które umożliwiają skonfigurowanie automatycznego ML eksperymentów. W tym przewodniku dowiesz się, jak zdefiniować różne ustawienia konfiguracji.

Aby wyświetlić przykłady automatycznych uczenia Maszynowego, zobacz [samouczek: Wytrenuj model klasyfikacji przy użyciu uczenia maszynowego automatycznych](tutorial-auto-train-models.md) lub [uczenia modeli za pomocą automatycznych machine learning w chmurze](how-to-auto-train-remote.md).

Opcje konfiguracji dostępne w automatycznych machine learning:

* Wybierz typ eksperymentu, np. klasyfikacji, regresji 
* Źródła danych, formatów i pobierania danych
* Wybierz urządzenie docelowe obliczeń (lokalne lub zdalne)
* Automatyczne ustawienia eksperymentu uczenia Maszynowego
* Uruchamianie zautomatyzowanych eksperymentu uczenia Maszynowego
* Eksplorowanie metryk modelu
* Zarejestruj i Wdróż model

## <a name="select-your-experiment-type"></a>Wybierz typ eksperymentu
Przed przystąpieniem do wykonywania eksperymentu, należy określić rodzaj maszyny nauczanym problemem, które są rozwiązywania. Automatyczne uczenie Maszynowe obsługuje dwie kategorie uczenia nadzorowanego: klasyfikacji i regresji. Zautomatyzowane ML obsługuje następujące algorytmy podczas procesu dostosowywania i automatyzacji. Użytkownik nie ma potrzeby w celu określenia algorytm.
Klasyfikacja | Regresji
--|--
sklearn.linear_model. LogisticRegression | sklearn.linear_model. ElasticNet
sklearn.linear_model. SGDClassifier  | sklearn.ensemble.GradientBoostingRegressor
sklearn.naive_bayes. BernoulliNB | sklearn.tree.DecisionTreeRegressor
sklearn.naive_bayes. MultinomialNB | sklearn.neighbors.KNeighborsRegressor
sklearn.svm.SVC | sklearn.linear_model. LassoLars
sklearn.svm.LinearSVC | sklearn.linear_model. SGDRegressor
sklearn.calibration.CalibratedClassifierCV |    sklearn.ensemble.RandomForestRegressor
sklearn.neighbors.KNeighborsClassifier |    sklearn.ensemble.ExtraTreesRegressor
sklearn.tree.DecisionTreeClassifier |   lightgbm. LGBMRegressor
sklearn.ensemble.RandomForestClassifier |
sklearn.ensemble.ExtraTreesClassifier   |
sklearn.ensemble.GradientBoostingClassifier |
lightgbm. LGBMClassifier |


## <a name="data-source-and-format"></a>Źródło danych i format
Automatyczne ML obsługuje dane, które znajdują się na komputerze lokalnym lub w chmurze w usłudze Azure Blob Storage. Dane mogą być odczytywane w scikit-informacje formatów obsługiwanych danych. Może odczytywać dane do tablic Numpy (1) X (cech) i y (Zmienna docelowa lub znany także jako etykieta) lub 2) Pandas dataframe. 

Przykłady:

1.  Tablice Numpy

    ```python
    digits = datasets.load_digits()
    X_digits = digits.data 
    y_digits = digits.target
    ```

2.  Pandas dataframe

    ```python
    Import pandas as pd
    df = pd.read_csv("https://automldemods.blob.core.windows.net/datasets/PlayaEvents2016,_1.6MB,_3.4k-rows.cleaned.2.tsv", delimiter="\t", quotechar='"') 
    # get integer labels 
    le = LabelEncoder() 
    le.fit(df["Label"].values) 
    y = le.transform(df["Label"].values) 
    df = df.drop(["Label"], axis=1) 
    df_train, _, y_train, _ = train_test_split(df, y, test_size=0.1, random_state=42)
    ```

## <a name="fetch-data-for-running-experiment-on-remote-compute"></a>Pobieranie danych do uruchamiania eksperymentów na zdalnego obliczeń

Jeśli używasz zdalnego obliczeń do uruchamiania eksperymentu, pobierania danych muszą być zapakowane w skrypcie języka python w oddzielnych `get_data()`. Ten skrypt jest uruchamiany na zdalne zasoby obliczeniowe, gdzie jest wykonywane automatyczne eksperymentu uczenia Maszynowego. `get_data` eliminuje konieczność pobierania danych przez sieć dla każdej iteracji. Bez `get_data`, eksperymentu zakończy się niepowodzeniem po uruchomieniu na zdalnym obliczeń.

Oto przykład `get_data`:

```python
%%writefile $project_folder/get_data.py 
import pandas as pd 
from sklearn.model_selection 
import train_test_split 
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

`get_data` Skrypt może zwracać następujące czynności:
Klucz | Typ |    Wzajemnie wykluczających się z | Opis
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

## <a name="train-and-validation-data"></a>Szkolenie i sprawdzanie poprawności danych

Można określić oddzielne train i sprawdzanie poprawności zestawu za pomocą get_data() lub bezpośrednio w `AutoMLConfig` metody.

## <a name="cross-validation-split-options"></a>Krzyżowego sprawdzania poprawności opcji podziału

### <a name="k-folds-cross-validation"></a>K złożeń krzyżowe sprawdzanie poprawności

Użyj `n_cross_validations` ustawienie, aby określić liczbę krzyżowego sprawdzania poprawności. Szkoleniowy zestaw danych zostanie ona podzielona losowo na `n_cross_validations` złożeń taki sam rozmiar. Podczas każdego krzyżowego sprawdzania poprawności round jednym z złożeń będzie służyć do sprawdzania poprawności modelu skonfigurowanych pod kątem w pozostałych złożeń. Ten proces powtarza się dla `n_cross_validations` Zaokrągla do momentu poszczególnych etapów jest używany jeden raz w postanowieniach sprawdzania poprawności. Ponadto ocenia średnią dla wszystkich `n_cross_validations` zaokrągla zostaną zgłoszone, a odpowiedni model zostanie retrained, szkolenie dla całego zestawu danych.

### <a name="monte-carlo-cross-validation-aka-repeated-random-sub-sampling"></a>Krzyżowe sprawdzanie poprawności Monte Carlo (zwane) Powtórzona losowego próbkowanie podrzędnych)

Użyj `validation_size` Aby określić procent zestaw danych szkoleniowych, które mają być używane do sprawdzania poprawności i użyj `n_cross_validations` Aby określić liczbę krzyżowego sprawdzania poprawności. Podczas każdego cross weryfikacji round podzestaw rozmiar `validation_size` będą wybierane losowo do sprawdzania poprawności modelu skonfigurowanych pod kątem w pozostałych danych. Ponadto ocenia średnią dla wszystkich `n_cross_validations` zaokrągla zostaną zgłoszone, a odpowiedni model zostanie retrained, szkolenie dla całego zestawu danych.

### <a name="custom-validation-dataset"></a>Zestaw niestandardowego sprawdzania poprawności danych

Użyj zestawu niestandardowego sprawdzania poprawności danych, jeśli losowe podziału nie jest dopuszczalne (zwykle czas serii danych lub imbalanced). Dzięki temu można określić własnego zestawu danych walidacji. Model zostaną ocenione względem zestawu danych walidacji, zamiast losowego zestawu danych.

## <a name="compute-to-run-experiment"></a>Zasoby obliczeniowe, uruchamianie eksperymentu

Następnie określić, gdzie będzie uczony model. Automatyczne eksperymentu szkolenia ML działa na obliczeniowego elementu docelowego, który należy do użytkownika i zarządzanie nimi. 

Opcje obliczeniowe obsługiwane są następujące:
1.  Komputer lokalny, takich jak lokalne stacjonarnym lub przenośnym — zazwyczaj, gdy masz mały zestaw danych i są nadal w fazie eksploracji.
2.  Komputer zdalny w chmurze — [maszyny wirtualnej do nauki o danych platformy Azure](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/) z systemem Linux — masz duży zestaw danych i chcesz skalować na dużej maszynie, która jest dostępna w chmurze platformy Azure. 
3.  Klaster usługi Azure Batch AI — A zarządzany klaster, który można skonfigurować do skalowania w poziomie i równoległego uruchamiania zautomatyzowanej ML iteracji. 

<a name='configure-experiment'/>
## <a name="configure-your-experiment-settings"></a>Skonfiguruj ustawienia eksperymentu

Istnieje kilka pokrętła, które umożliwiają skonfigurowanie automatycznych eksperymentu uczenia Maszynowego. Te parametry są ustawiane przez utworzenie wystąpienia `AutoMLConfig` obiektu.

Oto niektóre przykłady:

1.  Eksperymentu klasyfikacji, przy użyciu AUC ważona jako podstawowe metryki i maksymalny czas z 12 000 sekund na iterację eksperymentu, aby zakończyć po 50 iteracji i 2 złożeń krzyżowego sprawdzania poprawności.

    ```python
    automl_classifier = AutoMLConfig(
        task='classification',
        primary_metric='AUC_weighted',
        max_time_sec=12000,
        iterations=50,
        X=X, 
        y=y,
        n_cross_validations=2)
    ```
2.  Poniżej znajduje się przykład regresji zestaw eksperymentu do końca po 100 iteracji, z każdą iteracją, trwających maksymalnie 600 sekund z 5 weryfikacji wielu złożeń.

    ````python
    automl_regressor = AutoMLConfig(
        task='regression',
        max_time_sec=600,
        iterations=100,
        primary_metric='r2_score',
        X=X, 
        y=y,
        n_cross_validations=5)
    ````

Poniższa tabela zawiera listę dostępnych eksperymentu i ich wartości domyślne ustawień parametrów.

Właściwość |  Opis | Wartość domyślna
--|--|--
`task`  |Określ typ maszyny nauczanym problemem. Dozwolone wartości to <li>klasyfikacja</li><li>Regresji</li>    | Brak |
`primary_metric` |Metryki, którą chcesz zoptymalizować w tworzeniu modelu. Na przykład jeśli określisz dokładność jako primary_metric zautomatyzowane ML wygląda odnaleźć modelu z maksymalną dokładnością. Można określić tylko jeden primary_metric na eksperyment. Dozwolone wartości to <br/>**Klasyfikacja**:<br/><li> accuracy  </li><li> AUC_weighted</li><li> precision_score_weighted </li><li> balanced_accuracy </li><li> average_precision_score_weighted </li><br/>**Regresja**: <br/><li> normalized_mean_absolute_error </li><li> spearman_correlation </li><li> normalized_root_mean_squared_error </li><li> normalized_root_mean_squared_log_error</li><li> R2_score    </li> | Klasyfikacji: dokładności <br/>Aby uzyskać regresji: spearman_correlation <br/> |
`exit_score` |  Można ustawić wartość docelowa dla Twojej primary_metric. Po znalezieniu modelu spełniających docelowego primary_metric ML automatycznego zostanie zatrzymane, iteracja, a kończy eksperymentu. Ta wartość nie jest ustawiona (ustawienie domyślne), będą nadal uruchamiać liczbę iteracji, określony w iteracji eksperymentu uczenia Maszynowego z automatycznego. Przyjmuje wartość typu double. Jeśli element docelowy nigdy nie osiągnie, zautomatyzowane ML będzie kontynuowane, aż do napotkania liczba iteracji, określony w iteracji.|   Brak
`iterations` |Maksymalna liczba iteracji. Każda iteracja jest równy zadania szkolenia, powstałego w potoku. Potok to wstępne przetwarzanie danych i modelu. Aby uzyskać użycia modeli wysokiej jakości, co najmniej 250 | 100
`Concurrent_iterations`|    Maksymalna liczba iteracji do równoległego uruchamiania. To ustawienie działa tylko w przypadku zdalnego obliczeń.|    1
`max_cores_per_iteration`   | Wskazuje, jak wiele rdzeni na obliczeniowego elementu docelowego będzie używana do nauczenia jeden potok. Jeśli algorytm można wykorzystać wiele rdzeni, to zwiększa wydajność na maszynie wielordzeniowych. Jest on ustawiany na wartość -1 do użycia rdzeni dostępnych na komputerze.|  1
`max_time_sec` |    Ogranicza czas (w sekundach), jaki zajmuje konkretnej iteracji. Jeśli iteracji przekroczy określony, pobiera anulowane tej iteracji. W przeciwnym razie zestaw, a następnie iteracji będzie nadal działać, dopóki zostanie zakończone. |   Brak
`n_cross_validations`   |Liczba podziałów krzyżowego sprawdzania poprawności| Brak
`validation_size`   |Rozmiar sprawdzania poprawności, Ustaw jako wartość procentowa próbki szkolenia.|  Brak
`preprocess` | PRAWDA/FAŁSZ <br/>Wartość true włącza eksperymentów do wykonywania przetwarzania wstępnego w danych wejściowych. Poniżej przedstawiono część przetwarzania wstępnego<li>Brak danych: Imputes brakujące dane numeryczne ze średnią, tekst z większości wystąpienia </li><li>Wartości podzielonych na kategorie: Jeśli typ danych liczbowych i liczba unikatowych wartości jest mniejsza niż 5 procent, konwertuje do hot jednego procesu kodowania </li><li>Itp. Aby uzyskać pełną listę wyboru [repozytorium GitHub](https://aka.ms/aml-notebooks)</li><br/>Uwaga: Jeśli dane są rozrzedzonej nie można użyć wstępnie Przetwórz = true |  False | 
`blacklist_algos`   | Automatyczne eksperymentu uczenia Maszynowego ma wiele różnych algorytmów, które podejmuje próby. Skonfiguruj automatyczne uczenia Maszynowego, aby wykluczyć niektóre algorytmy z eksperymentu. Parametr jest przydatne, jeśli masz świadomość, że algorytmy nie działają dobrze sprawdza się w zestawie danych. Z wyjątkiem algorytmów można skrócić zasobów obliczeniowych i czasu szkoleń.<br/>Dozwolone wartości klasyfikacji<br/><li>Regresja logistyczna</li><li>Klasyfikator SGD</li><li>MultinomialNB</li><li>BernoulliNB</li><li>SVM</li><li>LinearSVM</li><li>kNN</li><li>DT</li><li>RF</li><li>dodatkowe drzew</li><li>ulepszanie gradientu</li><li>lgbm_classifier</li><br/>Dozwolone wartości regresji<br/><li>Elastyczne netto</li><li>Regresor zwiększenie gradientu</li><li>Regresor DT</li><li>regresor kNN</li><li>Hurtowi Lasso</li><li>Regresor SGD</li><li>Regresor RF</li><li>regresor dodatkowe drzew</li>|   Brak
`verbosity` |Określa poziom rejestrowania, z użyciem informacji są pełne i najbardziej krytyczne jest najmniej.<br/>Dozwolone wartości to:<br/><li>logging.INFO</li><li>rejestrowanie. OSTRZEŻENIE</li><li>rejestrowanie. BŁĄD</li><li>rejestrowanie. KRYTYCZNE</li>  | logging.INFO</li> 
`X` | Wszystkie funkcje do jego trenowanie za pomocą |  Brak
`y` |   Etykiety danych w celu jego trenowanie za pomocą. Klasyfikacja powinna być tablicy liczb całkowitych.|  Brak
`X_valid`|_Opcjonalnie_ wszystkie funkcje umożliwiające weryfikację. Jeśli nie zostanie określony, X jest podzielony między szkolenie i sprawdzanie poprawności |   Brak
`y_valid`   |_Opcjonalnie_ danych etykietę umożliwiające weryfikację. Jeśli nie zostanie określony, y jest podzielony między szkolenie i sprawdzanie poprawności    | Brak
`sample_weight` |   _Opcjonalnie_ wartość wagi, dla każdego przykładu. Użyj, jeśli chcesz przypisać różne wagi dla punktów danych |   Brak
`sample_weight_valid`   |   _Opcjonalnie_ wartość wagi, dla każdego przykładu sprawdzania poprawności. Jeśli nie zostanie określony, sample_weight są dzielone na szkolenie i sprawdzanie poprawności   | Brak
`run_configuration` |   Obiekt RunConfiguration.  Używane dla przebiegów zdalnych. |Brak
`data_script`  |    Ścieżka do pliku zawierającego metodę get_data.  Wymagane dla zdalnych przebiegów.   |Brak


## <a name="run-experiment"></a>Uruchamianie eksperymentu

Następnie możemy zainicjować eksperyment do uruchomienia i wygenerować model dla nas. Przekaż `AutoMLConfig` do `submit` metodę w celu wygenerowania modelu.

```python
run = experiment.submit(automl_config, show_output=True)
```

>[!NOTE]
>Zależności są najpierw instalowane na nowej maszyny wirtualnej DSVM.  Może upłynąć do 10 minut, zanim są wyświetlane dane wyjściowe.
>Ustawienie `show_output` True powoduje dane wyjściowe są wyświetlane w konsoli.


## <a name="explore-model-metrics"></a>Eksplorowanie metryk modelu
Jeśli jesteś w notesie, można wyświetlić wyniki w element widget lub wewnętrznej. Zobacz szczegóły "Śledzenie i oceniać modele". (Upewnij się, że zawartość AML zawiera istotne informacje w celu automatycznego ML)

Następujące metryki są zapisywane w każdej iteracji
* AUC_macro
* AUC_micro
* AUC_weighted
* accuracy
* average_precision_score_macro
* average_precision_score_micro
* average_precision_score_weighted
* balanced_accuracy
* f1_score_macro
* f1_score_micro
* f1_score_weighted
* log_loss
* norm_macro_recall
* precision_score_macro
* precision_score_micro
* precision_score_weighted
* recall_score_macro
* recall_score_micro
* recall_score_weighted
* weighted_accuracy

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o [jak i gdzie można wdrożyć model](how-to-deploy-and-where.md).

Dowiedz się więcej o [jak wytrenuj model klasyfikacji z automatycznego ML](tutorial-auto-train-models.md) lub [sposób trenowania przy użyciu zautomatyzowanego uczenia Maszynowego na zasób zdalny](how-to-auto-train-remote.md). 