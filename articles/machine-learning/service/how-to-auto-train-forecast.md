---
title: Auto — szkolenie modelu prognozowania szeregów czasowych
titleSuffix: Azure Machine Learning service
description: Dowiedz się, jak używać usługi Azure Machine Learning to w opracowywaniu prognozowania modelu regresji przy użyciu zautomatyzowanego uczenia maszynowego szeregów czasowych.
services: machine-learning
author: trevorbye
ms.author: trbye
ms.service: machine-learning
ms.subservice: core
ms.reviewer: trbye
ms.topic: conceptual
ms.date: 05/02/2019
ms.openlocfilehash: c7f4b6d8aa614a460772fb7af11f9b83dc3fc979
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2019
ms.locfileid: "65800814"
---
# <a name="auto-train-a-time-series-forecast-model"></a>Auto — szkolenie modelu prognozowania szeregów czasowych

W tym artykule dowiesz się, jak do uczenia modelu regresji prognozowania szeregów czasowych za pomocą automatycznych usługi machine learning w usłudze Azure Machine Learning. Konfigurowanie modelu prognozowania jest podobny do konfigurowania modelu regresji standard za pomocą automatycznych machine learning, ale niektórych opcjami i wstępne przetwarzanie kroki konfiguracji istnieje do pracy z danymi szeregów czasowych. Poniższe przykłady pokazują sposób do:

* Przygotowuje dane do modelowania serii czasu
* Konfigurowanie określone parametry szeregów czasowych w [ `AutoMLConfig` ](/python/api/azureml-train-automl/azureml.train.automl.automlconfig) obiektu
* Uruchom prognoz z danymi szeregów czasowych

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2X1GW]

## <a name="prerequisites"></a>Wymagania wstępne

* Obszar roboczy usługi Azure Machine Learning. Aby utworzyć obszar roboczy, zobacz [Utwórz obszar roboczy usługi Azure Machine Learning](setup-create-workspace.md).
* W tym artykule przyjęto założenie, podstawowe znajomość konfigurowania automatycznych eksperymentu uczenia maszynowego. Postępuj zgodnie z [samouczek](tutorial-auto-train-models.md) lub [porad](how-to-configure-auto-train.md) Aby wyświetlić podstawowe maszyny automatycznych wzorce projektowe eksperymentu uczenia.

## <a name="preparing-data"></a>Trwa przygotowywanie danych

Najważniejsza różnica między prognozowania typ zadania regresji i regresji typu zadania w ramach uczenia maszynowego automatycznych obejmują funkcję danych, która reprezentuje szeregów czasowych prawidłowe. Serie czasu ma częstotliwość dobrze zdefiniowany i spójny i ma wartość w każdym punkcie przykładowe przedział czasu ciągłe. Należy wziąć pod uwagę następującej migawki pliku `sample.csv`.

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

This data set is a simple example of daily sales data for a company that has two different stores, A and B. Additionally, there is a feature for `week_of_year` that will allow the model to detect weekly seasonality. Pole `day_datetime` reprezentuje szeregów czasowych czystego, z częstotliwością dziennych i pole `sales_quantity` jest kolumna docelowa dla wykonywania prognoz. Odczyt danych do elementów Pandas dataframe, a następnie użyj `to_datetime` funkcję, aby upewnić się, jest szeregów czasowych `datetime` typu.

```python
import pandas as pd
data = pd.read_csv("sample.csv")
data["day_datetime"] = pd.to_datetime(data["day_datetime"])
```

W tym przypadku dane są już sortowane rosnąco przez wartość pola Czas `day_datetime`. Jednak podczas konfigurowania eksperymentu, upewnij się, że kolumna żądany czas jest posortowanej rosnąco tworzenie szeregów czasowych prawidłowe. Przyjęto założenie, że dane zawierają 1000 rekordów i deterministyczna podziału w danych, tworzenie, szkolenie i testowanie zestawów danych. Następnie oddzielić pola docelowego `sales_quantity` w celu tworzenia prognoz szkolenie i testowanie zestawów.

```python
X_train = data.iloc[:950]
X_test = data.iloc[-50:]

y_train = X_train.pop("sales_quantity").values
y_test = X_test.pop("sales_quantity").values
```

> [!NOTE]
> Podczas uczenia modelu do prognozowania przyszłych wartości, upewnij się, wszystkie funkcje używane szkolenia i mogą być używane podczas wykonywania prognoz dla Twojego zamierzony horyzoncie. Na przykład podczas tworzenia prognozę, funkcji bieżąca cena akcji w tym może wysoce zwiększyć dokładność szkolenia. Jednak jeśli zamierzasz Prognozuj z długich horizon, nie może być możliwość dokładnego przewidywania przyszłego podstawowych wartości odpowiadające przyszłych punktów szeregów czasowych i dokładności modelu może to spowodować obniżenie.

## <a name="configure-and-run-experiment"></a>Konfigurowanie i uruchamianie eksperymentu

Do prognozowania zadania, uczenie maszynowe automatyczne wykorzystuje przetwarzania wstępnego i Szacowanie czynności, które są specyficzne dla danych szeregów czasowych. Zostaną wykonane następujące kroki przetwarzania wstępnego:

* Wykrywanie szeregów czasowych przykładowy częstotliwość (np. co godzinę, codziennie, co tydzień) i tworzenia nowych rekordów dla braku punktami czasu zapewnienie ciągłej serii.
* Naliczenie brakuje wartości docelowej (za pośrednictwem wypełnienia do przodu) i kolumnami funkcji (przy użyciu wartości w środkowej kolumnie)
* Utwórz funkcje umożliwiające stały efekty w różnych seriach ziarna
* Tworzenie na podstawie czasu funkcji ułatwiających uczenia sezonowych wzorców
* Kodowanie podzielonych na kategorie Zmienne liczbowe ilości

`AutoMLConfig` Obiektu definiuje ustawienia i dane niezbędne dla zadania uczenia automatycznych maszyny. Podobnie jak problem regresji, należy zdefiniować szkolenia standardowe parametry, takie jak typ zadania, liczba iteracji, szkolenia, danych i liczba ocen wielu. Do prognozowania zadania, istnieją dodatkowe parametry, które musi być ustawiona, które wpływają na eksperyment. W poniższej tabeli opisano każdy parametr i sposób jej użycia.

| Param | Opis | Wymagane |
|-------|-------|-------|
|`time_column_name`|Służy do określania kolumn daty/godziny w danych wejściowych, używane do kompilowania szeregów czasowych i wnioskowanie jej częstotliwość.|✓|
|`grain_column_names`|Nazwy, definiując grupy poszczególnych serii danych wejściowych. Jeśli nie zdefiniowano ziarna, zestaw danych zakłada się, że jeden szeregów czasowych.||
|`max_horizon`|Maksymalną żądaną horizon prognozy w jednostkach częstotliwości szeregów czasowych.|✓|
|`target_lags`|*n* okresów opóźnienie do przodu dotyczą wartości przed szkoleń modelowych.||
|`target_rolling_window_size`|*n* historical periods to use to generate forecasted values, <= training set size. W przypadku pominięcia *n* pełną szkolenia ustawiono rozmiaru.||

Utwórz ustawienia Szeregi czasowe jako obiekt słownika. Ustaw `time_column_name` do `day_datetime` pole w zestawie danych. Zdefiniuj `grain_column_names` parametru, aby upewnić się, że **dwa odrębne grupy szeregów czasowych** są tworzone dla zestawu danych; jeden dla magazynu, A i B. na koniec `max_horizon` 50 w celu przewidywania dla całego testu zestawu. Konfiguruj okno prognozowania okresów 10 za pomocą `target_rolling_window_size`i opóźnienie docelowej wartości 2 okresy o rozpoczęciu `target_lags` parametru.

```python
time_series_settings = {
    "time_column_name": "day_datetime",
    "grain_column_names": ["store"],
    "max_horizon": 50,
    "target_lags": 2,
    "target_rolling_window_size": 10
}
```

Teraz Utwórz standardowy `AutoMLConfig` obiektu, określając `forecasting` typ zadania i przesyłanie eksperymentu. Po zakończeniu modelu, należy pobrać najlepiej wykonywania iteracji.

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

> [!NOTE]
> Procedurę krzyżowego sprawdzania poprawności (CV) dane szeregów czasowych może naruszyć podstawowe założenia statystyczne strategii canonical zwijania K krzyżowa Weryfikacja, więc automatyczne uczeniu implementuje stopniowe procedurze weryfikacji źródła, aby utworzyć krzyżowa Weryfikacja złożeń danych szeregów czasowych. Aby użyć tej procedury, należy określić `n_cross_validations` parametru w `AutoMLConfig` obiektu. Można pominąć sprawdzanie poprawności i użycia własnego weryfikacji zestawów z `X_valid` i `y_valid` parametrów.

### <a name="view-feature-engineering-summary"></a>Podsumowanie Inżynieria funkcji widoku

Dla typów podzadań szeregu czasowego automatycznych machine Learning można wyświetlić szczegółowe informacje z tej funkcji, inżynierów procesów. Poniższy kod pokazuje poszczególne funkcje pierwotne wraz z następującymi atrybutami:

* Nazwa funkcji nieprzetworzone
* Wiele funkcji zaprojektowanych utworzone z tej funkcji nieprzetworzone
* Wykryto typ
* Czy funkcja została porzucona
* Lista funkcji przekształcenia dla funkcji nieprzetworzone

```python
fitted_model.named_steps['timeseriestransformer'].get_featurization_summary()
```

## <a name="forecasting-with-best-model"></a>Prognozowanie za pomocą najlepszy model

Użyj najlepsze iteracji modelu do prognozowania wartości dla zestawu danych testowych.

```python
y_predict = fitted_model.predict(X_test)
y_actual = y_test.flatten()
```

Alternatywnie, można użyć `forecast()` zamiast funkcji `predict()`, co pozwoli specyfikacji rozpoczęcia prognozy. W poniższym przykładzie najpierw Zastąp wszystkie wartości w `y_pred` z `NaN`. Prognozy źródła będzie na końcu danych szkoleniowych w tym przypadku tak jak zwykle korzystając z `predict()`. Jednak jeśli zastąpione tylko w drugiej połowie `y_pred` z `NaN`, funkcja spowoduje, że wartości numeryczne w pierwszej połowie zostały zmodyfikowane, ale prognozy `NaN` wartości w drugiej połowie. Funkcja zwraca wartości prognozowanych i wyrównane funkcji.

Można również użyć `forecast_destination` parametru w `forecast()` funkcji do przewidywania wartości aż do określonej daty.

```python
y_query = y_test.copy().astype(np.float)
y_query.fill(np.nan)
y_fcst, X_trans = fitted_pipeline.forecast(X_test, y_query, forecast_destination=pd.Timestamp(2019, 1, 8))
```

Oblicz RMSE (błąd kwadrat oznacza główny) między `y_test` wartościami rzeczywistymi a przewidywane wartości w `y_pred`.

```python
from sklearn.metrics import mean_squared_error
from math import sqrt

rmse = sqrt(mean_squared_error(y_actual, y_predict))
rmse
```

Teraz, gdy ogólnych dokładności modelu w ustaleniu, najbardziej realistyczne następnym krokiem jest użycie modelu do prognozowania nieznany przyszłych wartości. Po prostu podać wartość zestawu danych, w tym samym formacie co zestaw testów `X_test` , ale przyszłych Data/Godzina i prognozowania Wynikowy zestaw jest przewidywane wartości dla każdego kroku szeregów czasowych. Assume the last time-series records in the data set were for 12/31/2018. Do prognozowania popytu na następny dzień (lub dowolną liczbę okresów muszą prognozować, < = `max_horizon`), utworzyć pojedynczy czas serii rekord dla każdego sklepu dla 01/01/2019 r.

    day_datetime,store,week_of_year
    01/01/2019,A,1
    01/01/2019,A,1

Powtórz czynności niezbędnych do załadowania tych danych przyszłych ramkę danych, a następnie uruchom `best_run.predict(X_test)` do przewidywania przyszłych wartości.

> [!NOTE]
> Wartości nie można przewidzieć liczbę okresów większa `max_horizon`. Model musi być ponownie uczony przy użyciu większych horizon do przewidywania przyszłych wartości poza bieżącym horyzoncie.

## <a name="next-steps"></a>Kolejne kroki

* Postępuj zgodnie z [samouczek](tutorial-auto-train-models.md) dowiesz się, jak tworzenie eksperymentów przy użyciu zautomatyzowanego uczenia maszynowego.
* Widok [Azure Machine Learning SDK dla języka Python](https://aka.ms/aml-sdk) dokumentację referencyjną.
