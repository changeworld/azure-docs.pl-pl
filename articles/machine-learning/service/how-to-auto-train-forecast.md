---
title: Autouczenie modelu prognozowania szeregów czasowych
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak używać Azure Machine Learning do uczenia modelu regresji prognozowania szeregów czasowych przy użyciu funkcji automatycznego uczenia maszynowego.
services: machine-learning
author: trevorbye
ms.author: trbye
ms.service: machine-learning
ms.subservice: core
ms.reviewer: trbye
ms.topic: conceptual
ms.date: 06/20/2019
ms.openlocfilehash: 03c5d46221dc385a390e840381270c01c40bdc6d
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/20/2019
ms.locfileid: "71170410"
---
# <a name="auto-train-a-time-series-forecast-model"></a>Autouczenie modelu prognozowania szeregów czasowych

W tym artykule dowiesz się, jak szkolić model regresji prognozowania szeregów czasowych przy użyciu funkcji automatycznego uczenia maszynowego w Azure Machine Learning. Konfigurowanie modelu prognozowania jest podobne do konfigurowania modelu regresji standardowej przy użyciu automatycznego uczenia maszynowego, ale niektóre opcje konfiguracji i wstępne kroki przetwarzania istnieją do pracy z danymi szeregów czasowych. W poniższych przykładach pokazano, jak:

* Przygotowywanie danych do modelowania szeregów czasowych
* Konfigurowanie określonych parametrów szeregów czasowych w [`AutoMLConfig`](/python/api/azureml-train-automl/azureml.train.automl.automlconfig) obiekcie
* Uruchamianie prognoz z danymi szeregów czasowych

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2X1GW]

Możesz użyć zautomatyzowanej ML do łączenia technik i podejścia i uzyskania zalecanej wysokiej jakości prognozy szeregów czasowych. Zautomatyzowany eksperyment szeregów czasowych jest traktowany jako problem z regresją wieloczynnikowa. Poprzednie wartości serii czasowych są "przestawne", aby stać się dodatkowymi wymiarami regresor wraz z innymi predykcyjnymi.

Takie podejście, w przeciwieństwie do klasycznych metod szeregów czasowych, ma zaletę naturalnie dołączania wielu zmiennych kontekstowych i ich relacji do siebie podczas uczenia się. W rzeczywistych aplikacjach do prognozowania można mieć wpływ na prognozę. Na przykład podczas prognozowania sprzedaży, interakcji z tendencjami historycznymi, kursów wymiany i ceny wszystkie wspólnie łączą wynik sprzedaży. Dodatkową korzyścią jest to, że wszystkie najnowsze innowacje w modelach regresji natychmiast stosują się do prognozowania.

Można [skonfigurować](#config) , jak daleko w przyszłość Prognoza powinna zostać rozszerzona (horyzont prognoz), a także spowolnienia i nie tylko. Zautomatyzowana ML zdobywa pojedynczy, ale często wewnętrznie rozgałęzienie modelu dla wszystkich elementów w zestawie danych i prognozowanie Horizons. W tym celu można uzyskać więcej danych w celu oszacowania parametrów modelu i generalizacji do niewidocznej serii.

Funkcje wyodrębnione z danych szkoleniowych odgrywają rolę krytyczną. I, zautomatyzowana ML wykonuje standardowe kroki przetwarzania wstępnego i generuje dodatkowe funkcje szeregów czasowych do przechwytywania efektów sezonowych i maksymalizuje dokładność predykcyjną.

## <a name="prerequisites"></a>Wymagania wstępne

* Obszar roboczy usługi Azure Machine Learning. Aby utworzyć obszar roboczy, zobacz [Tworzenie obszaru roboczego Azure Machine Learning](how-to-manage-workspace.md).
* W tym artykule założono podstawową wiedzę na temat konfigurowania zautomatyzowanego eksperymentu uczenia maszynowego. Postępuj zgodnie z [samouczkiem](tutorial-auto-train-models.md) lub poradami [, aby zobaczyć](how-to-configure-auto-train.md) podstawowe wzorce projektowe eksperymentu w usłudze Machine Learning.

## <a name="preparing-data"></a>Przygotowywanie danych

Najważniejszym różnicą między typem zadania regresja prognozowania a typem zadania regresji w ramach automatycznego uczenia maszynowego jest dołączenie funkcji w danych, która reprezentuje prawidłową serię czasową. Zwykła seria czasowa ma dobrze zdefiniowaną i spójną częstotliwość i ma wartość w każdym punkcie próbki w ciągłym przedziale czasu. Weź pod uwagę poniższą migawkę pliku `sample.csv`.

    day_datetime,store,sales_quantity,week_of_year
    9/3/2018,A,2000,36
    9/3/2018,B,600,36
    9/4/2018,A,2300,36
    9/4/2018,B,550,36
    9/5/2018,A,2100,36
    9/5/2018,B,650,36
    9/6/2018,A,2400,36
    9/6/2018,B,700,36
    9/7/2018,A,2450,36
    9/7/2018,B,650,36

Ten zestaw danych to prosty przykład codziennych danych sprzedaży dla firmy, która ma dwa różne sklepy, a i B. Ponadto istnieje funkcja `week_of_year` , która umożliwi modelowi wykrywanie tygodniowego sezonowości. Pole `day_datetime` reprezentuje czystą serię czasową z częstotliwością dzienną, `sales_quantity` a pole jest kolumną docelową do uruchamiania prognoz. Odczytaj dane do ramki dataPandas, a następnie użyj `to_datetime` funkcji, aby upewnić `datetime` się, że serie czasowe są typu.

```python
import pandas as pd
data = pd.read_csv("sample.csv")
data["day_datetime"] = pd.to_datetime(data["day_datetime"])
```

W takim przypadku dane są już sortowane rosnąco według pola `day_datetime`Time. Jednak podczas konfigurowania eksperymentu upewnij się, że kolumna żądana godzina jest posortowana w kolejności rosnącej, aby utworzyć prawidłową serię czasową. Załóżmy, że dane zawierają 1 000 rekordów i czynią deterministyczną podział w danych w celu tworzenia zestawów danych szkoleniowych i testowych. Następnie należy oddzielić pole `sales_quantity` docelowe, aby utworzyć szkolenia prognoz i zestawy testów.

```python
X_train = data.iloc[:950]
X_test = data.iloc[-50:]

y_train = X_train.pop("sales_quantity").values
y_test = X_test.pop("sales_quantity").values
```

> [!NOTE]
> Podczas uczenia modelu do prognozowania przyszłych wartości upewnij się, że wszystkie funkcje używane w szkoleniu mogą być używane podczas przewidywania dla zamierzonego horyzontu. Na przykład podczas tworzenia prognozy popytu, w tym funkcji dla bieżącej ceny zapasowej, można w znacznym stopniu zwiększyć dokładność szkolenia. Jeśli jednak planujesz prognozowanie za pomocą długich horyzontów, możesz nie być w stanie dokładnie przewidzieć przyszłe wartości giełdowe odpowiadające przyszłym punktom szeregów czasowych, a dokładność modelu może być niepoprawna.

<a name="config"></a>
## <a name="configure-and-run-experiment"></a>Konfigurowanie i uruchamianie eksperymentu

W przypadku zadań prognozowania automatyczne Uczenie maszynowe korzysta z kroków wstępnego przetwarzania i szacowania, które są specyficzne dla danych szeregów czasowych. Zostaną wykonane następujące kroki przetwarzania wstępnego:

* Wykrywaj częstotliwość próbkowania szeregów czasowych (np. co godzinę, codziennie, co tydzień) i Utwórz nowe rekordy dla nieobecnych punktów czasowych, aby zapewnić ciągłość serii.
* Nie ma wartości w elemencie docelowym (za pośrednictwem przekazywania) i kolumn funkcji (przy użyciu wartości kolumn mediany).
* Tworzenie funkcji opartych na ziarnach w celu włączenia stałych efektów w różnych seriach
* Tworzenie funkcji opartych na czasie, które ułatwiają uczenie wzorców sezonowych
* Koduj zmienne kategorii na liczby liczbowe

`AutoMLConfig` Obiekt definiuje ustawienia i dane niezbędne do automatycznego zadania uczenia maszynowego. Podobnie jak w przypadku problemu z regresją, definiuje się standardowe parametry szkolenia, takie jak typ zadania, liczba iteracji, dane szkoleniowe i liczba operacji krzyżowych. W przypadku zadań prognozowania należy ustawić dodatkowe parametry, które mają wpływ na eksperyment. W poniższej tabeli opisano każdy parametr i jego użycie.

| Param | Opis | Wymagane |
|-------|-------|-------|
|`time_column_name`|Służy do określania kolumny DateTime w danych wejściowych używanych do kompilowania szeregów czasowych i wywnioskowania jej częstotliwości.|✓|
|`grain_column_names`|Nazwy definiujące poszczególne grupy serii w danych wejściowych. Jeśli ziarno nie jest zdefiniowane, zakłada się, że zestaw danych jest jedną serią czasową.||
|`max_horizon`|Definiuje maksymalny żądany zakres prognozy w jednostkach częstotliwości szeregów czasowych. Jednostki są oparte na przedziale czasu na dane szkoleniowe, np. co miesiąc, co tydzień prognozy powinien przewidzieć.|✓|
|`target_lags`|Liczba wierszy do rozłożeniu wartości docelowych na podstawie częstotliwości danych. Jest to reprezentowane jako lista lub jedna liczba całkowita. Zwłoki należy używać, gdy relacja między zmiennymi niezależnymi i zmienną zależną nie są zgodne ani nie są domyślnie skorelowane. Na przykład podczas próby prognozowania zapotrzebowania na produkt zapotrzebowanie w dowolnym miesiącu może zależeć od ceny określonych cen w ciągu 3 miesięcy. W tym przykładzie możesz chcieć zażądać negatywnego opóźnienia (popytu) przez 3 miesiące, aby model był szkoleniowy dla poprawnej relacji.||
|`target_rolling_window_size`|*n* okresy historyczne używane do generowania prognozowanych wartości, < = rozmiar zestawu szkoleniowego. W przypadku pominięcia *n* to pełny rozmiar zestawu szkoleniowego. Określ ten parametr, jeśli chcesz wziąć pod uwagę tylko określoną ilość historii podczas uczenia modelu.||

Aby uzyskać więcej informacji, zobacz [dokumentację referencyjną](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlconfig?view=azure-ml-py) .

Utwórz ustawienia szeregów czasowych jako obiekt słownika. Ustaw wartość `time_column_name` `day_datetime` na pole w zestawie danych. Zdefiniuj parametr, aby upewnić się, że dla danych są tworzone **dwie osobne grupy szeregów czasowych** ; jeden dla sklepu a i B. na `max_horizon` koniec ustaw wartość na 50, aby przewidzieć cały zestaw testów. `grain_column_names` Ustaw okno prognozy na 10 okresów `target_rolling_window_size`, a następnie określ pojedyncze opóźnienie na wartościach docelowych dla 2 okresów `target_lags` wraz z parametrem.

```python
time_series_settings = {
    "time_column_name": "day_datetime",
    "grain_column_names": ["store"],
    "max_horizon": 50,
    "target_lags": 2,
    "target_rolling_window_size": 10,
    "preprocess": True,
}
```



> [!NOTE]
> Zautomatyzowane kroki wstępnego przetwarzania w usłudze Machine Learning (normalizacja funkcji, obsługa brakujących danych, konwertowanie tekstu na liczbowe itp.) staje się częścią modelu źródłowego. Przy użyciu modelu dla prognoz te same kroki przetwarzania wstępnego zastosowane podczas uczenia są automatycznie stosowane do danych wejściowych.

Definiując `grain_column_names` w powyższym fragmencie kodu, AutoML utworzy dwie osobne grupy szeregów czasowych, znane także jako wiele szeregów czasowych. Jeśli nie zdefiniowano żadnego ziarna, AutoML założenie, że zestaw danych jest pojedynczą serią czasową. Aby dowiedzieć się więcej o pojedynczych seriach czasowych, zobacz [energy_demand_notebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand).



Teraz można utworzyć obiekt `AutoMLConfig` standardowy, `forecasting` określić typ zadania i przesłać eksperyment. Po zakończeniu działania modelu Pobierz iterację najlepszego przebiegu.

```python
from azureml.core.workspace import Workspace
from azureml.core.experiment import Experiment
from azureml.train.automl import AutoMLConfig
import logging

automl_config = AutoMLConfig(task='forecasting',
                             primary_metric='normalized_root_mean_squared_error',
                             iterations=10,
                             X=X_train,
                             y=y_train,
                             n_cross_validations=5,
                             enable_ensembling=False,
                             verbosity=logging.INFO,
                             **time_series_settings)

ws = Workspace.from_config()
experiment = Experiment(ws, "forecasting_example")
local_run = experiment.submit(automl_config, show_output=True)
best_run, fitted_model = local_run.get_output()
```

Aby zapoznać się ze szczegółowymi przykładami zaawansowanej konfiguracji prognozowania [, w tym](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb) :

* Wykrywanie świąt i cechowania
* krzyżowe sprawdzanie poprawności źródła
* Konfigurowalne spowolnienia
* funkcje agregujące okna stopniowego

### <a name="view-feature-engineering-summary"></a>Wyświetlanie podsumowania inżynierów funkcji

W przypadku typów zadań szeregów czasowych w ramach automatycznego uczenia maszynowego można wyświetlić szczegóły procesu inżynierii funkcji. Poniższy kod przedstawia każdą nieprzetworzoną funkcję wraz z następującymi atrybutami:

* Pierwotna nazwa funkcji
* Liczba skonstruowanych funkcji utworzonych z tej funkcji nieprzetworzonej
* Wykryto typ
* Czy funkcja została porzucona
* Lista przekształceń funkcji dla funkcji RAW

```python
fitted_model.named_steps['timeseriestransformer'].get_featurization_summary()
```

## <a name="forecasting-with-best-model"></a>Prognozowanie przy użyciu najlepszego modelu

Użyj najlepszej iteracji modelu do prognozowania wartości dla zestawu danych testowych.

```python
y_predict = fitted_model.predict(X_test)
y_actual = y_test.flatten()
```

Alternatywnie, można użyć `forecast()` funkcji `predict()`zamiast, która będzie zezwalać na specyfikacje podczas przewidywania. W poniższym przykładzie należy najpierw zastąpić wszystkie wartości w `y_pred`. `NaN` Podstawą prognozy będzie na końcu danych szkoleniowych w tym przypadku, tak jak zwykle w przypadku korzystania z programu `predict()`. Jeśli jednak zamienisz tylko drugą połowę `y_pred` z `NaN`, funkcja spowodowałaby pozostawienie wartości liczbowych w pierwszej połowie `NaN` niemodyfikowanej, ale prognozowanie wartości w drugiej połowie. Funkcja zwraca zarówno wartości prognozowane, jak i wyrównane funkcje.

Można również użyć `forecast_destination` parametru `forecast()` w funkcji, aby prognozować wartości aż do określonej daty.

```python
y_query = y_test.copy().astype(np.float)
y_query.fill(np.nan)
y_fcst, X_trans = fitted_pipeline.forecast(
    X_test, y_query, forecast_destination=pd.Timestamp(2019, 1, 8))
```

Oblicz RMSE (główny błąd oznaczający pierwiastek) między `y_test` wartościami rzeczywistymi i prognozowanymi wartościami w. `y_pred`

```python
from sklearn.metrics import mean_squared_error
from math import sqrt

rmse = sqrt(mean_squared_error(y_actual, y_predict))
rmse
```

Teraz, gdy ogólna dokładność modelu została określona, najbardziej realistycznym następnym krokiem jest użycie modelu do prognozowania nieznanych przyszłych wartości. Wystarczy podać zestaw danych w tym samym formacie co zestaw `X_test` testów, ale z przyszłymi datetimemi, a wynikający z nich zestaw prognoz to prognozowane wartości dla każdego kroku szeregów czasowych. Załóżmy, że ostatnie rekordy szeregów czasowych w zestawie danych były 12/31/2018. Aby prognozować zapotrzebowanie na następny dzień (lub wiele okresów potrzebnych do prognozowania, < = `max_horizon`), Utwórz pojedynczy rekord szeregu czasowego dla każdego magazynu dla 01/01/2019.

    day_datetime,store,week_of_year
    01/01/2019,A,1
    01/01/2019,A,1

Powtórz kroki niezbędne do załadowania tych przyszłych danych do ramki Dataframe, a następnie `best_run.predict(X_test)` Uruchom polecenie, aby przewidzieć przyszłe wartości.

> [!NOTE]
> Wartości nie mogą być przewidywane dla liczby okresów większej niż `max_horizon`. Model musi być przeszkolony z większym horyzontem, aby przewidzieć przyszłe wartości poza bieżącym horyzontem.

## <a name="next-steps"></a>Następne kroki

* Postępuj zgodnie z [samouczkiem](tutorial-auto-train-models.md) , aby dowiedzieć się, jak tworzyć eksperymenty przy użyciu automatycznej uczenia maszynowego
* Zapoznaj się z dokumentacją [zestawu Azure Machine Learning SDK dla języka Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) .
