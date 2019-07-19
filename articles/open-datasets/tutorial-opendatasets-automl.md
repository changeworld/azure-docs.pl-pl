---
title: 'Samouczek: Wzbogacanie zautomatyzowanego modelu uczenia maszynowego'
titleSuffix: Azure Open Datasets
description: Dowiedz się, jak korzystać z wygody otwartych zestawów danych platformy Azure wraz z możliwością usługi Azure Machine Learning, aby utworzyć model regresji do przewidywania cen opłat za taksówkę NYC.
services: open-datasets
ms.service: open-datasets
ms.topic: tutorial
author: trevorbye
ms.author: trbye
ms.reviewer: trbye
ms.date: 05/02/2019
ms.openlocfilehash: aafbef2c9a9328266a937d4c52c154a8b826c342
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/18/2019
ms.locfileid: "68312160"
---
# <a name="tutorial-build-a-regression-model-with-automated-machine-learning-and-open-datasets"></a>Samouczek: Tworzenie modelu regresji z automatycznym uczeniem maszynowym i otwartymi zestawami danych

W tym samouczku przedstawiono wygodę korzystania z otwartych zestawów danych platformy Azure wraz z możliwością usługi Azure Machine Learning, aby utworzyć model regresji do przewidywania cen opłat za taksówkę NYC. Łatwo Pobieraj publicznie dostępne dane dotyczące świątecznych, wolnych i pogodzych, a następnie skonfiguruj zautomatyzowany eksperyment uczenia maszynowego przy użyciu usługi Azure Machine Learning. Ten proces akceptuje dane szkoleniowe i ustawienia konfiguracji, a następnie automatycznie wykonuje iterację przez kombinacje różnych metod normalizacji/normalizacji funkcji, modeli i parametrów z parametrami, aby dotrzeć do najlepszego modelu.

W tym samouczku przedstawiono następujące zadania:

- Konfigurowanie obszaru roboczego usługi Azure Machine Learning
- Konfigurowanie lokalnego środowiska języka Python
- Uzyskiwanie dostępu do danych, przekształcanie ich i dołączanie przy użyciu usługi Azure Open DataSets
- Uczenie zautomatyzowanego modelu regresji uczenia maszynowego
- Oblicz dokładność modelu

## <a name="prerequisites"></a>Wymagania wstępne

W tym samouczku wymagane są następujące wymagania wstępne.

* Obszar roboczy usługi Azure Machine Learning
* Środowisko Python 3,6

### <a name="create-a-workspace"></a>Tworzenie obszaru roboczego

Postępuj zgodnie z [instrukcjami](https://docs.microsoft.com/azure/machine-learning/service/setup-create-workspace#portal) , aby utworzyć obszar roboczy za pomocą Azure Portal, jeśli jeszcze go nie masz. Po utworzeniu Zanotuj nazwę obszaru roboczego, nazwę grupy zasobów i Identyfikator subskrypcji.

### <a name="create-a-python-environment"></a>Tworzenie środowiska języka Python

W tym przykładzie jest stosowane środowisko Anaconda z notesami Jupyter, ale można uruchomić ten kod w dowolnym środowisku 3.6. x i z dowolnym edytorem tekstu lub środowiskiem IDE. Wykonaj następujące kroki, aby utworzyć nowe środowisko programistyczne.

1. Jeśli jeszcze tego nie zrobiono, [Pobierz](https://www.anaconda.com/distribution/) i zainstaluj Anaconda i wybierz **wersję Python 3,7**.
1. Otwórz monit Anaconda i Utwórz nowe środowisko. Utworzenie środowiska, podczas gdy składniki i pakiety będą pobierane, potrwa kilka minut.
    ```
    conda create -n tutorialenv python=3.6.5
    ```
1. Aktywuj środowisko.
    ```
    conda activate tutorialenv
    ```
1. Włącz jądra IPython specyficzne dla środowiska.
    ```
    conda install notebook ipykernel
    ```
1. Utwórz jądro.
    ```
    ipython kernel install --user
    ```
1. Zainstaluj pakiety, które są potrzebne w tym samouczku. Te pakiety są duże i trwają od 5-10 minut do zainstalowania.
    ```
    pip install azureml-sdk[automl] azureml-opendatasets
    ```
1. Uruchom jądro notesu ze środowiska.
    ```
    jupyter notebook
    ```

Po wykonaniu tych kroków Sklonuj [repozytorium otwartych zestawów danych](https://github.com/Azure/OpenDatasetsNotebooks) i Otwórz Notes **/Taxi-automl/01-tutorial-opendatasets-automl. ipynb** , aby go uruchomić.

## <a name="download-and-prepare-data"></a>Pobieranie i przygotowywanie danych

Zaimportuj niezbędne pakiety. Pakiet Open DataSets zawiera klasę reprezentującą każde źródło danych (`NycTlcGreen` na przykład) do łatwego filtrowania parametrów daty przed pobraniem.


```python
from azureml.opendatasets import NycTlcGreen
import pandas as pd
from datetime import datetime
from dateutil.relativedelta import relativedelta
```

Zacznij od utworzenia ramki Dataframe do przechowywania danych z taksówki. W przypadku pracy w środowisku innym niż platforma Spark otwieranie zestawów danych umożliwia tylko pobieranie z określonych klas tylko jednego miesiąca z niektórymi klasami `MemoryError` . Aby pobrać rok danych z taksówki, można iteracyjnie pobrać jeden miesiąc i przed dołączeniem go do `green_taxi_df` losowo próbkowanych rekordów 2000 z każdego miesiąca, aby uniknąć przeładowania ramki danych. Następnie Wyświetl podgląd danych.

>[!NOTE]
> Otwarte zestawy danych mają klasy dublowania do pracy w środowiskach Spark, w których rozmiar danych i pamięć nie są problemem.

```python
green_taxi_df = pd.DataFrame([])
start = datetime.strptime("1/1/2016","%m/%d/%Y")
end = datetime.strptime("1/31/2016","%m/%d/%Y")

for sample_month in range(12):
    temp_df_green = NycTlcGreen(start + relativedelta(months=sample_month), end + relativedelta(months=sample_month)) \
        .to_pandas_dataframe()
    green_taxi_df = green_taxi_df.append(temp_df_green.sample(2000))

green_taxi_df.head(10)
```

<div>
<style scoped> .dataframe tbody tr th:only-of-type { vertical-align: middle; }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>vendorID</th>
      <th>lpepPickupDatetime</th>
      <th>lpepDropoffDatetime</th>
      <th>passengerCount</th>
      <th>tripDistance</th>
      <th>puLocationId</th>
      <th>doLocationId</th>
      <th>pickupLongitude</th>
      <th>pickupLatitude</th>
      <th>dropoffLongitude</th>
      <th>Przyciski ...</th>
      <th>płatnośćtype</th>
      <th>fareAmount</th>
      <th>Ultimate</th>
      <th>mtaTax</th>
      <th>improvementSurcharge</th>
      <th>tipAmount</th>
      <th>tollsAmount</th>
      <th>ehailFee</th>
      <th>totalAmount</th>
      <th>czas trwania</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>117695</th>
      <td>2</td>
      <td>2016-01-20 17:38:28</td>
      <td>2016-01-20 17:46:33</td>
      <td>1</td>
      <td>0,98</td>
      <td>Brak</td>
      <td>Brak</td>
      <td>-73,921715</td>
      <td>40,766682</td>
      <td>-73,916908</td>
      <td>Przyciski ...</td>
      <td>2.0</td>
      <td>7.0</td>
      <td>1.0</td>
      <td>0,5</td>
      <td>0,3</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>8.8</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>1221794</th>
      <td>2</td>
      <td>2016-01-01 21:53:28</td>
      <td>2016-01-02 00:00:00</td>
      <td>1</td>
      <td>3,08</td>
      <td>Brak</td>
      <td>Brak</td>
      <td>-73,979973</td>
      <td>40,677071</td>
      <td>-73,934349</td>
      <td>Przyciski ...</td>
      <td>2.0</td>
      <td>11,5</td>
      <td>0,5</td>
      <td>0,5</td>
      <td>0,3</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>12,8</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>1165078</th>
      <td>2</td>
      <td>2016-01-01 00:50:23</td>
      <td>2016-01-01 01:05:37</td>
      <td>1</td>
      <td>2.44</td>
      <td>Brak</td>
      <td>Brak</td>
      <td>-73,863045</td>
      <td>40,882923</td>
      <td>-73,839836</td>
      <td>Przyciski ...</td>
      <td>2.0</td>
      <td>12.5</td>
      <td>0,5</td>
      <td>0,5</td>
      <td>0,3</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>13,8</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>1345223</th>
      <td>2</td>
      <td>2016-01-04 17:50:03</td>
      <td>2016-01-04 18:03:43</td>
      <td>1</td>
      <td>2,87</td>
      <td>Brak</td>
      <td>Brak</td>
      <td>-73,977730</td>
      <td>40,684647</td>
      <td>-73,931259</td>
      <td>Przyciski ...</td>
      <td>1.0</td>
      <td>12.0</td>
      <td>1.0</td>
      <td>0,5</td>
      <td>0,3</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>13,8</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>608125</th>
      <td>1</td>
      <td>2016-01-13 08:48:20</td>
      <td>2016-01-13 08:52:16</td>
      <td>1</td>
      <td>0.50</td>
      <td>Brak</td>
      <td>Brak</td>
      <td>-73,942589</td>
      <td>40,841423</td>
      <td>-73,943672</td>
      <td>Przyciski ...</td>
      <td>2.0</td>
      <td>4.5</td>
      <td>0.0</td>
      <td>0,5</td>
      <td>0,3</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>5.3</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>1049431</th>
      <td>2</td>
      <td>2016-01-29 17:16:18</td>
      <td>2016-01-29 17:27:52</td>
      <td>1</td>
      <td>2.25</td>
      <td>Brak</td>
      <td>Brak</td>
      <td>-73.830894</td>
      <td>40,759434</td>
      <td>-73,842422</td>
      <td>Przyciski ...</td>
      <td>2.0</td>
      <td>10.5</td>
      <td>1.0</td>
      <td>0,5</td>
      <td>0,3</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>12,3</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>646563</th>
      <td>2</td>
      <td>2016-01-14 00:45:30</td>
      <td>2016-01-14 00:54:16</td>
      <td>1</td>
      <td>1.93</td>
      <td>Brak</td>
      <td>Brak</td>
      <td>-73,927109</td>
      <td>40,762848</td>
      <td>-73,909302</td>
      <td>Przyciski ...</td>
      <td>1.0</td>
      <td>8.5</td>
      <td>0,5</td>
      <td>0,5</td>
      <td>0,3</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>9,8</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>438204</th>
      <td>1</td>
      <td>2016-01-09 14:25:02</td>
      <td>2016-01-09 14:32:48</td>
      <td>2</td>
      <td>0,80</td>
      <td>Brak</td>
      <td>Brak</td>
      <td>-73.881195</td>
      <td>40,741779</td>
      <td>-73,872086</td>
      <td>Przyciski ...</td>
      <td>2.0</td>
      <td>6.5</td>
      <td>0.0</td>
      <td>0,5</td>
      <td>0,3</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>7.3</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>303784</th>
      <td>2</td>
      <td>2016-01-25 18:13:47</td>
      <td>2016-01-25 18:23:50</td>
      <td>1</td>
      <td>1,04</td>
      <td>Brak</td>
      <td>Brak</td>
      <td>-73,954376</td>
      <td>40,805729</td>
      <td>-73,939117</td>
      <td>Przyciski ...</td>
      <td>1.0</td>
      <td>8.0</td>
      <td>1.0</td>
      <td>0,5</td>
      <td>0,3</td>
      <td>1,5</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>11,3</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>269105</th>
      <td>2</td>
      <td>2016-01-24 20:46:50</td>
      <td>2016-01-24 21:04:03</td>
      <td>6</td>
      <td>2,82</td>
      <td>Brak</td>
      <td>Brak</td>
      <td>-73.845200</td>
      <td>40,722134</td>
      <td>-73,810638</td>
      <td>Przyciski ...</td>
      <td>1.0</td>
      <td>13.0</td>
      <td>0,5</td>
      <td>0,5</td>
      <td>0,3</td>
      <td>2.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>16,3</td>
      <td>1.0</td>
    </tr>
  </tbody>
</table>
<p>10 wierszy × 23 kolumny</p>
</div>



Teraz, gdy początkowe dane są ładowane, zdefiniuj funkcję do tworzenia różnych funkcji opartych na czasie w polu Data/godzina pobrania. Spowoduje to utworzenie nowych pól o numerze miesiąca, dzień miesiąca, dzień tygodnia i godzinie dnia i umożliwi modelowi współczynniki w sezonowości opartym na czasie. Funkcja dodaje również funkcję statyczną dla kodu kraju, aby dołączać dane dni wolnych. Użyj funkcji w ramce Dataframe, aby iteracyjnie `build_time_features()` zastosować funkcję do każdego wiersza w danych z taksówką. `apply()`


```python
def build_time_features(vector):
    pickup_datetime = vector[0]
    month_num = pickup_datetime.month
    day_of_month = pickup_datetime.day
    day_of_week = pickup_datetime.weekday()
    hour_of_day = pickup_datetime.hour
    country_code = "US"

    return pd.Series((month_num, day_of_month, day_of_week, hour_of_day, country_code))

green_taxi_df[["month_num", "day_of_month","day_of_week", "hour_of_day", "country_code"]] = green_taxi_df[["lpepPickupDatetime"]].apply(build_time_features, axis=1)
green_taxi_df.head(10)
```

<div>
<style scoped> .dataframe tbody tr th:only-of-type { vertical-align: middle; }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>vendorID</th>
      <th>lpepPickupDatetime</th>
      <th>lpepDropoffDatetime</th>
      <th>passengerCount</th>
      <th>tripDistance</th>
      <th>puLocationId</th>
      <th>doLocationId</th>
      <th>pickupLongitude</th>
      <th>pickupLatitude</th>
      <th>dropoffLongitude</th>
      <th>Przyciski ...</th>
      <th>tipAmount</th>
      <th>tollsAmount</th>
      <th>ehailFee</th>
      <th>totalAmount</th>
      <th>czas trwania</th>
      <th>month_num</th>
      <th>day_of_month</th>
      <th>day_of_week</th>
      <th>hour_of_day</th>
      <th>country_code</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>117695</th>
      <td>2</td>
      <td>2016-01-20 17:38:28</td>
      <td>2016-01-20 17:46:33</td>
      <td>1</td>
      <td>0,98</td>
      <td>Brak</td>
      <td>Brak</td>
      <td>-73,921715</td>
      <td>40,766682</td>
      <td>-73,916908</td>
      <td>Przyciski ...</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>8.8</td>
      <td>1.0</td>
      <td>1</td>
      <td>20</td>
      <td>2</td>
      <td>17</td>
      <td>USA</td>
    </tr>
    <tr>
      <th>1221794</th>
      <td>2</td>
      <td>2016-01-01 21:53:28</td>
      <td>2016-01-02 00:00:00</td>
      <td>1</td>
      <td>3,08</td>
      <td>Brak</td>
      <td>Brak</td>
      <td>-73,979973</td>
      <td>40,677071</td>
      <td>-73,934349</td>
      <td>Przyciski ...</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>12,8</td>
      <td>1.0</td>
      <td>1</td>
      <td>1</td>
      <td>4</td>
      <td>21</td>
      <td>USA</td>
    </tr>
    <tr>
      <th>1165078</th>
      <td>2</td>
      <td>2016-01-01 00:50:23</td>
      <td>2016-01-01 01:05:37</td>
      <td>1</td>
      <td>2.44</td>
      <td>Brak</td>
      <td>Brak</td>
      <td>-73,863045</td>
      <td>40,882923</td>
      <td>-73,839836</td>
      <td>Przyciski ...</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>13,8</td>
      <td>1.0</td>
      <td>1</td>
      <td>1</td>
      <td>4</td>
      <td>0</td>
      <td>USA</td>
    </tr>
    <tr>
      <th>1345223</th>
      <td>2</td>
      <td>2016-01-04 17:50:03</td>
      <td>2016-01-04 18:03:43</td>
      <td>1</td>
      <td>2,87</td>
      <td>Brak</td>
      <td>Brak</td>
      <td>-73,977730</td>
      <td>40,684647</td>
      <td>-73,931259</td>
      <td>Przyciski ...</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>13,8</td>
      <td>1.0</td>
      <td>1</td>
      <td>4</td>
      <td>0</td>
      <td>17</td>
      <td>USA</td>
    </tr>
    <tr>
      <th>608125</th>
      <td>1</td>
      <td>2016-01-13 08:48:20</td>
      <td>2016-01-13 08:52:16</td>
      <td>1</td>
      <td>0.50</td>
      <td>Brak</td>
      <td>Brak</td>
      <td>-73,942589</td>
      <td>40,841423</td>
      <td>-73,943672</td>
      <td>Przyciski ...</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>5.3</td>
      <td>1.0</td>
      <td>1</td>
      <td>13</td>
      <td>2</td>
      <td>8</td>
      <td>USA</td>
    </tr>
    <tr>
      <th>1049431</th>
      <td>2</td>
      <td>2016-01-29 17:16:18</td>
      <td>2016-01-29 17:27:52</td>
      <td>1</td>
      <td>2.25</td>
      <td>Brak</td>
      <td>Brak</td>
      <td>-73.830894</td>
      <td>40,759434</td>
      <td>-73,842422</td>
      <td>Przyciski ...</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>12,3</td>
      <td>1.0</td>
      <td>1</td>
      <td>29</td>
      <td>4</td>
      <td>17</td>
      <td>USA</td>
    </tr>
    <tr>
      <th>646563</th>
      <td>2</td>
      <td>2016-01-14 00:45:30</td>
      <td>2016-01-14 00:54:16</td>
      <td>1</td>
      <td>1.93</td>
      <td>Brak</td>
      <td>Brak</td>
      <td>-73,927109</td>
      <td>40,762848</td>
      <td>-73,909302</td>
      <td>Przyciski ...</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>9,8</td>
      <td>1.0</td>
      <td>1</td>
      <td>14</td>
      <td>3</td>
      <td>0</td>
      <td>USA</td>
    </tr>
    <tr>
      <th>438204</th>
      <td>1</td>
      <td>2016-01-09 14:25:02</td>
      <td>2016-01-09 14:32:48</td>
      <td>2</td>
      <td>0,80</td>
      <td>Brak</td>
      <td>Brak</td>
      <td>-73.881195</td>
      <td>40,741779</td>
      <td>-73,872086</td>
      <td>Przyciski ...</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>7.3</td>
      <td>1.0</td>
      <td>1</td>
      <td>9</td>
      <td>5</td>
      <td>14</td>
      <td>USA</td>
    </tr>
    <tr>
      <th>303784</th>
      <td>2</td>
      <td>2016-01-25 18:13:47</td>
      <td>2016-01-25 18:23:50</td>
      <td>1</td>
      <td>1,04</td>
      <td>Brak</td>
      <td>Brak</td>
      <td>-73,954376</td>
      <td>40,805729</td>
      <td>-73,939117</td>
      <td>Przyciski ...</td>
      <td>1,5</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>11,3</td>
      <td>1.0</td>
      <td>1</td>
      <td>25</td>
      <td>0</td>
      <td>18</td>
      <td>USA</td>
    </tr>
    <tr>
      <th>269105</th>
      <td>2</td>
      <td>2016-01-24 20:46:50</td>
      <td>2016-01-24 21:04:03</td>
      <td>6</td>
      <td>2,82</td>
      <td>Brak</td>
      <td>Brak</td>
      <td>-73.845200</td>
      <td>40,722134</td>
      <td>-73,810638</td>
      <td>Przyciski ...</td>
      <td>2.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>16,3</td>
      <td>1.0</td>
      <td>1</td>
      <td>24</td>
      <td>6</td>
      <td>20</td>
      <td>USA</td>
    </tr>
  </tbody>
</table>
<p>10 wierszy × 28 kolumn</p>
</div>

Usuń niektóre kolumny, które nie są potrzebne do modelowania lub tworzenia dodatkowych funkcji. Zmień nazwę pola czas na czas odbioru, a dodatkowo Przekształć czas na północ przy `pandas.Series.dt.normalize`użyciu. Należy to zrobić do wszystkich funkcji czasu, dzięki czemu składnik DateTime może być później używany jako klucz podczas dołączania zestawów danych na dziennym poziomie szczegółowości.

```python
columns_to_remove = ["lpepDropoffDatetime", "puLocationId", "doLocationId", "extra", "mtaTax",
                     "improvementSurcharge", "tollsAmount", "ehailFee", "tripType", "rateCodeID",
                     "storeAndFwdFlag", "paymentType", "fareAmount", "tipAmount"
                    ]
for col in columns_to_remove:
    green_taxi_df.pop(col)

green_taxi_df = green_taxi_df.rename(columns={"lpepPickupDatetime": "datetime"})
green_taxi_df["datetime"] = green_taxi_df["datetime"].dt.normalize()
green_taxi_df.head(5)
```

<div>
<style scoped> .dataframe tbody tr th:only-of-type { vertical-align: middle; }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>vendorID</th>
      <th>datetime</th>
      <th>passengerCount</th>
      <th>tripDistance</th>
      <th>pickupLongitude</th>
      <th>pickupLatitude</th>
      <th>dropoffLongitude</th>
      <th>dropoffLatitude</th>
      <th>totalAmount</th>
      <th>month_num</th>
      <th>day_of_month</th>
      <th>day_of_week</th>
      <th>hour_of_day</th>
      <th>country_code</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>117695</th>
      <td>2</td>
      <td>2016-01-20</td>
      <td>1</td>
      <td>0,98</td>
      <td>-73,921715</td>
      <td>40,766682</td>
      <td>-73,916908</td>
      <td>40,761257</td>
      <td>8.8</td>
      <td>1</td>
      <td>20</td>
      <td>2</td>
      <td>17</td>
      <td>USA</td>
    </tr>
    <tr>
      <th>1221794</th>
      <td>2</td>
      <td>2016-01-01</td>
      <td>1</td>
      <td>3,08</td>
      <td>-73,979973</td>
      <td>40,677071</td>
      <td>-73,934349</td>
      <td>40,671654</td>
      <td>12,8</td>
      <td>1</td>
      <td>1</td>
      <td>4</td>
      <td>21</td>
      <td>USA</td>
    </tr>
    <tr>
      <th>1165078</th>
      <td>2</td>
      <td>2016-01-01</td>
      <td>1</td>
      <td>2.44</td>
      <td>-73,863045</td>
      <td>40,882923</td>
      <td>-73,839836</td>
      <td>40,868336</td>
      <td>13,8</td>
      <td>1</td>
      <td>1</td>
      <td>4</td>
      <td>0</td>
      <td>USA</td>
    </tr>
    <tr>
      <th>1345223</th>
      <td>2</td>
      <td>2016-01-04</td>
      <td>1</td>
      <td>2,87</td>
      <td>-73,977730</td>
      <td>40,684647</td>
      <td>-73,931259</td>
      <td>40,694248</td>
      <td>13,8</td>
      <td>1</td>
      <td>4</td>
      <td>0</td>
      <td>17</td>
      <td>USA</td>
    </tr>
    <tr>
      <th>608125</th>
      <td>1</td>
      <td>2016-01-13</td>
      <td>1</td>
      <td>0.50</td>
      <td>-73,942589</td>
      <td>40,841423</td>
      <td>-73,943672</td>
      <td>40,834396</td>
      <td>5.3</td>
      <td>1</td>
      <td>13</td>
      <td>2</td>
      <td>8</td>
      <td>USA</td>
    </tr>
  </tbody>
</table>
</div>

### <a name="enrich-with-holiday-data"></a>Wzbogacanie z danymi o dniach wolnych

Teraz, gdy dane o taksówkach są pobierane i przetworzone w sposób bezstanowy, Dodaj je w postaci danych dni wolnych jako dodatkowych funkcji. Funkcje specyficzne dla wolnych firm będą pomocne w zakresie dokładności modeli, w miarę jak duże święta są coraz większe, a dostawy są ograniczone. Zestaw danych wolnych jest stosunkowo mały, więc Pobierz pełny zestaw przy użyciu `PublicHolidays` konstruktora klasy bez parametrów do filtrowania. Wyświetl podgląd danych, aby sprawdzić format.

```python
from azureml.opendatasets import PublicHolidays
# call default constructor to download full dataset
holidays_df = PublicHolidays().to_pandas_dataframe()
holidays_df.head(5)
```

    ActivityStarted, to_pandas_dataframe
    Looking for parquet files...
    Reading them into Pandas dataframe...
    Reading Processed/part-00000-tid-1353805596865908763-9ee4e95b-0d55-4292-addd-a0e19d7c32cb-3559-c000.snappy.parquet under container holidaydatacontainer
    Done.
    ActivityCompleted: Activity=to_pandas_dataframe, HowEnded=Success, Duration=1799.89 [ms]

<div>
<style scoped> .dataframe tbody tr th:only-of-type { vertical-align: middle; }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>countryOrRegion</th>
      <th>dzień wolny</th>
      <th>isPaidTimeOff</th>
      <th>countryRegionCode</th>
      <th>normalizeHolidayName</th>
      <th>date</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>40688</th>
      <td>Albania</td>
      <td>Nowy rok dnia</td>
      <td>Brak</td>
      <td>AL</td>
      <td>Nowy rok dnia</td>
      <td>2008-01-01</td>
    </tr>
    <tr>
      <th>40689</th>
      <td>Algeria</td>
      <td>Nowy rok dnia</td>
      <td>Brak</td>
      <td>DZ</td>
      <td>Nowy rok dnia</td>
      <td>2008-01-01</td>
    </tr>
    <tr>
      <th>40690</th>
      <td>Andora</td>
      <td>Nowy rok dnia</td>
      <td>Brak</td>
      <td>AD</td>
      <td>Nowy rok dnia</td>
      <td>2008-01-01</td>
    </tr>
    <tr>
      <th>40691</th>
      <td>Angola</td>
      <td>Nowy rok dnia</td>
      <td>Brak</td>
      <td>AO</td>
      <td>Nowy rok dnia</td>
      <td>2008-01-01</td>
    </tr>
    <tr>
      <th>40692</th>
      <td>Argentyna</td>
      <td>Nowy rok dnia</td>
      <td>Brak</td>
      <td>AR</td>
      <td>Nowy rok dnia</td>
      <td>2008-01-01</td>
    </tr>
  </tbody>
</table>
</div>



Zmień nazwy `date` i kolumny, aby odpowiadały nazwom pól z danych dotyczących taksówki, a także znormalizować czas, aby można było go użyć jako klucza. `countryRegionCode` Następnie dołączymy dane urlopowe za pomocą funkcji Pandas `merge()` . Spowoduje to zachowanie wszystkich rekordów z `green_taxi_df`, ale dodanie danych dni wolnych, gdy istnieje dla odpowiednich `datetime` i `country_code`, które w tym przypadku jest zawsze `"US"`. Wyświetl podgląd danych, aby sprawdzić, czy zostały one prawidłowo scalone.

```python
holidays_df = holidays_df.rename(columns={"countryRegionCode": "country_code", "date": "datetime"})
holidays_df["datetime"] = holidays_df["datetime"].dt.normalize()
holidays_df.pop("countryOrRegion")
holidays_df.pop("holidayName")

taxi_holidays_df = pd.merge(green_taxi_df, holidays_df, how="left", on=["datetime", "country_code"])
taxi_holidays_df.head(5)
```

<div>
<style scoped> .dataframe tbody tr th:only-of-type { vertical-align: middle; }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>vendorID</th>
      <th>datetime</th>
      <th>passengerCount</th>
      <th>tripDistance</th>
      <th>pickupLongitude</th>
      <th>pickupLatitude</th>
      <th>dropoffLongitude</th>
      <th>dropoffLatitude</th>
      <th>totalAmount</th>
      <th>month_num</th>
      <th>day_of_month</th>
      <th>day_of_week</th>
      <th>hour_of_day</th>
      <th>country_code</th>
      <th>isPaidTimeOff</th>
      <th>normalizeHolidayName</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2</td>
      <td>2016-01-20</td>
      <td>1</td>
      <td>0,98</td>
      <td>-73,921715</td>
      <td>40,766682</td>
      <td>-73,916908</td>
      <td>40,761257</td>
      <td>8.8</td>
      <td>1</td>
      <td>20</td>
      <td>2</td>
      <td>17</td>
      <td>USA</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>2016-01-01</td>
      <td>1</td>
      <td>3,08</td>
      <td>-73,979973</td>
      <td>40,677071</td>
      <td>-73,934349</td>
      <td>40,671654</td>
      <td>12,8</td>
      <td>1</td>
      <td>1</td>
      <td>4</td>
      <td>21</td>
      <td>USA</td>
      <td>Prawda</td>
      <td>Nowy rok dnia</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2</td>
      <td>2016-01-01</td>
      <td>1</td>
      <td>2.44</td>
      <td>-73,863045</td>
      <td>40,882923</td>
      <td>-73,839836</td>
      <td>40,868336</td>
      <td>13,8</td>
      <td>1</td>
      <td>1</td>
      <td>4</td>
      <td>0</td>
      <td>USA</td>
      <td>Prawda</td>
      <td>Nowy rok dnia</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2</td>
      <td>2016-01-04</td>
      <td>1</td>
      <td>2,87</td>
      <td>-73,977730</td>
      <td>40,684647</td>
      <td>-73,931259</td>
      <td>40,694248</td>
      <td>13,8</td>
      <td>1</td>
      <td>4</td>
      <td>0</td>
      <td>17</td>
      <td>USA</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>4</th>
      <td>1</td>
      <td>2016-01-13</td>
      <td>1</td>
      <td>0.50</td>
      <td>-73,942589</td>
      <td>40,841423</td>
      <td>-73,943672</td>
      <td>40,834396</td>
      <td>5.3</td>
      <td>1</td>
      <td>13</td>
      <td>2</td>
      <td>8</td>
      <td>USA</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
  </tbody>
</table>
</div>

### <a name="enrich-with-weather-data"></a>Wzbogacanie z danymi pogodowymi

Teraz dołączamy dane o pogodzie NOAA powierzchni do danych o taksówkach i świętach. Użyj podobnego podejścia do pobierania danych pogody, pobierając jeden miesiąc w czasie iteracyjnie. Ponadto Określ `cols` parametr z tablicą ciągów, aby odfiltrować kolumny, które chcesz pobrać. Jest to bardzo duży zestaw danych zawierający dane o powierzchni Pogoda ze wszystkich elementów na świecie, dlatego przed dołączeniem każdego miesiąca należy przefiltrować pola lat/Long na blisko `query()` NYC przy użyciu funkcji w ramce Dataframe. Zapewni to, że `weather_df` nie jest zbyt duży.

```python
from azureml.opendatasets import NoaaIsdWeather

weather_df = pd.DataFrame([])
start = datetime.strptime("1/1/2016","%m/%d/%Y")
end = datetime.strptime("1/31/2016","%m/%d/%Y")

for sample_month in range(12):
    tmp_df = NoaaIsdWeather(cols=["temperature", "precipTime", "precipDepth", "snowDepth"], start_date=start + relativedelta(months=sample_month), end_date=end + relativedelta(months=sample_month))\
        .to_pandas_dataframe()
    print("--weather downloaded--")

    # filter out coordinates not in NYC to conserve memory
    tmp_df = tmp_df.query("latitude>=40.53 and latitude<=40.88")
    tmp_df = tmp_df.query("longitude>=-74.09 and longitude<=-73.72")
    print("--filtered coordinates--")
    weather_df = weather_df.append(tmp_df)

weather_df.head(10)
```

<div>
<style scoped> .dataframe tbody tr th:only-of-type { vertical-align: middle; }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>wban</th>
      <th>precipTime</th>
      <th>snowDepth</th>
      <th>temperature</th>
      <th>Szerokość geograficzna</th>
      <th>precipDepth</th>
      <th>Długość geograficzna</th>
      <th>datetime</th>
      <th>usaf</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1754979</th>
      <td>94741</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>7.2</td>
      <td>40,85</td>
      <td>0.0</td>
      <td>-74,061</td>
      <td>2016-01-01 00:51:00</td>
      <td>725025</td>
    </tr>
    <tr>
      <th>1754980</th>
      <td>94741</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>6.7</td>
      <td>40,85</td>
      <td>0.0</td>
      <td>-74,061</td>
      <td>2016-01-01 01:51:00</td>
      <td>725025</td>
    </tr>
    <tr>
      <th>1754981</th>
      <td>94741</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>6.7</td>
      <td>40,85</td>
      <td>0.0</td>
      <td>-74,061</td>
      <td>2016-01-01 02:51:00</td>
      <td>725025</td>
    </tr>
    <tr>
      <th>1754982</th>
      <td>94741</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>6.1</td>
      <td>40,85</td>
      <td>0.0</td>
      <td>-74,061</td>
      <td>2016-01-01 03:51:00</td>
      <td>725025</td>
    </tr>
    <tr>
      <th>1754983</th>
      <td>94741</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>5.6</td>
      <td>40,85</td>
      <td>0.0</td>
      <td>-74,061</td>
      <td>2016-01-01 04:51:00</td>
      <td>725025</td>
    </tr>
    <tr>
      <th>1754984</th>
      <td>94741</td>
      <td>24,0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>40,85</td>
      <td>5.0</td>
      <td>-74,061</td>
      <td>2016-01-01 04:59:00</td>
      <td>725025</td>
    </tr>
    <tr>
      <th>1754985</th>
      <td>94741</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>40,85</td>
      <td>NaN</td>
      <td>-74,061</td>
      <td>2016-01-01 04:59:00</td>
      <td>725025</td>
    </tr>
    <tr>
      <th>1754986</th>
      <td>94741</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>5.6</td>
      <td>40,85</td>
      <td>0.0</td>
      <td>-74,061</td>
      <td>2016-01-01 05:51:00</td>
      <td>725025</td>
    </tr>
    <tr>
      <th>1754987</th>
      <td>94741</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>5.0</td>
      <td>40,85</td>
      <td>0.0</td>
      <td>-74,061</td>
      <td>2016-01-01 06:51:00</td>
      <td>725025</td>
    </tr>
    <tr>
      <th>1754988</th>
      <td>94741</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>5.0</td>
      <td>40,85</td>
      <td>0.0</td>
      <td>-74,061</td>
      <td>2016-01-01 07:51:00</td>
      <td>725025</td>
    </tr>
  </tbody>
</table>
</div>

Ponownie wywołaj `pandas.Series.dt.normalize` `datetime` dla pola w danych pogodowych, aby pasował do klucza czasu w `taxi_holidays_df`. Usuń niepotrzebne kolumny i odfiltruj rekordy, w których znajduje się `NaN`temperatura.

Następnie Grupuj dane pogodowe, aby codziennie agregowane wartości pogodowe. Zdefiniuj DICT `aggregations` , aby zdefiniować sposób agregowania poszczególnych pól na poziomie dziennym. Dla `snowDepth` `precipTime` `precipDepth` i `temperature` podejmij znaczenie i dla i zajmiemy się dzienną największą wartością. `groupby()` Użyj funkcji wraz z agregacjami, aby zgrupować dane. Wyświetl podgląd danych, aby upewnić się, że istnieje jeden rekord dziennie.

```python
weather_df["datetime"] = weather_df["datetime"].dt.normalize()
weather_df.pop("usaf")
weather_df.pop("wban")
weather_df.pop("longitude")
weather_df.pop("latitude")

# filter out NaN
weather_df = weather_df.query("temperature==temperature")

# group by datetime
aggregations = {"snowDepth": "mean", "precipTime": "max", "temperature": "mean", "precipDepth": "max"}
weather_df_grouped = weather_df.groupby("datetime").agg(aggregations)
weather_df_grouped.head(10)
```

<div>
<style scoped> .dataframe tbody tr th:only-of-type { vertical-align: middle; }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>snowDepth</th>
      <th>precipTime</th>
      <th>temperature</th>
      <th>precipDepth</th>
    </tr>
    <tr>
      <th>datetime</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2016-01-01</th>
      <td>NaN</td>
      <td>1.0</td>
      <td>5,197345</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>2016-01-02</th>
      <td>NaN</td>
      <td>1.0</td>
      <td>2,567857</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>2016-01-03</th>
      <td>NaN</td>
      <td>1.0</td>
      <td>3,846429</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>2016-01-04</th>
      <td>NaN</td>
      <td>1.0</td>
      <td>0,123894</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>2016-01-05</th>
      <td>NaN</td>
      <td>6.0</td>
      <td>-7,206250</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>2016-01-06</th>
      <td>NaN</td>
      <td>6.0</td>
      <td>-0,896396</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>2016-01-07</th>
      <td>NaN</td>
      <td>6.0</td>
      <td>3.180645</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>2016-01-08</th>
      <td>NaN</td>
      <td>1.0</td>
      <td>4,384091</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>2016-01-09</th>
      <td>NaN</td>
      <td>6.0</td>
      <td>6,710274</td>
      <td>3.0</td>
    </tr>
    <tr>
      <th>2016-01-10</th>
      <td>NaN</td>
      <td>24,0</td>
      <td>10,943655</td>
      <td>254,0</td>
    </tr>
  </tbody>
</table>
</div>

> [!NOTE]
> Przykłady w tym samouczku scalają dane przy użyciu funkcji Pandas i agregacji niestandardowych, ale zestaw SDK otwartych zestawów danych ma klasy, które ułatwiają scalanie i wzbogacanie zestawów. Zobacz [Notes](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-join/04-nyc-taxi-join-weather-in-pandas.ipynb) , aby zapoznać się z przykładami kodu tych wzorców projektowych.

### <a name="cleanse-data"></a>Oczyszczanie danych

Scalanie danych o taksówkach i dniach wolnych, które zostały przygotowane przy użyciu nowych danych pogody. Tym razem wystarczy `datetime` tylko klucz i ponownie wykonać lewe sprzężenie danych. `describe()` Uruchom funkcję w nowej ramce Dataframe, aby wyświetlić statystyki podsumowania dla każdego pola.

```python
taxi_holidays_weather_df = pd.merge(taxi_holidays_df, weather_df_grouped, how="left", on=["datetime"])
taxi_holidays_weather_df.describe()
```

<div>
<style scoped> .dataframe tbody tr th:only-of-type { vertical-align: middle; }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>vendorID</th>
      <th>passengerCount</th>
      <th>tripDistance</th>
      <th>pickupLongitude</th>
      <th>pickupLatitude</th>
      <th>dropoffLongitude</th>
      <th>dropoffLatitude</th>
      <th>totalAmount</th>
      <th>month_num</th>
      <th>day_of_month</th>
      <th>day_of_week</th>
      <th>hour_of_day</th>
      <th>snowDepth</th>
      <th>precipTime</th>
      <th>temperature</th>
      <th>precipDepth</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>count</th>
      <td>24000,000000</td>
      <td>24000,000000</td>
      <td>24000,000000</td>
      <td>24000,000000</td>
      <td>24000,000000</td>
      <td>24000,000000</td>
      <td>24000,000000</td>
      <td>24000,000000</td>
      <td>24000,000000</td>
      <td>24000,000000</td>
      <td>24000,000000</td>
      <td>24000,000000</td>
      <td>1671.000000</td>
      <td>24000,000000</td>
      <td>24000,000000</td>
      <td>24000,000000</td>
    </tr>
    <tr>
      <th>Średnia</th>
      <td>1,786583</td>
      <td>6,576208</td>
      <td>1,582588</td>
      <td>20,505491</td>
      <td>84,936413</td>
      <td>-36,232825</td>
      <td>21,723144</td>
      <td>7,863018</td>
      <td>6,500000</td>
      <td>15.113708</td>
      <td>3,240250</td>
      <td>13,664125</td>
      <td>11,764141</td>
      <td>13,258875</td>
      <td>13,903524</td>
      <td>1056,644458</td>
    </tr>
    <tr>
      <th>wartość</th>
      <td>0,409728</td>
      <td>9,086857</td>
      <td>2,418177</td>
      <td>108,847821</td>
      <td>70,678506</td>
      <td>37,650276</td>
      <td>19.104384</td>
      <td>10,648766</td>
      <td>3,452124</td>
      <td>8,485155</td>
      <td>1,956895</td>
      <td>6,650676</td>
      <td>15,651884</td>
      <td>10,339720</td>
      <td>9,474396</td>
      <td>2815,592754</td>
    </tr>
    <tr>
      <th>min.</th>
      <td>1,000000</td>
      <td>-60,000000</td>
      <td>-1,000000</td>
      <td>-74.179482</td>
      <td>0.000000</td>
      <td>-74.190704</td>
      <td>0.000000</td>
      <td>-52.800000</td>
      <td>1,000000</td>
      <td>1,000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>3,000000</td>
      <td>1,000000</td>
      <td>-13,379464</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>25%</th>
      <td>2,000000</td>
      <td>1,000000</td>
      <td>0,330000</td>
      <td>-73,946680</td>
      <td>40,717712</td>
      <td>-73,945429</td>
      <td>1,770000</td>
      <td>1,000000</td>
      <td>3,750000</td>
      <td>8,000000</td>
      <td>2,000000</td>
      <td>9,000000</td>
      <td>3,000000</td>
      <td>1,000000</td>
      <td>6,620773</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>50%</th>
      <td>2,000000</td>
      <td>4,000000</td>
      <td>0,830000</td>
      <td>1.500000</td>
      <td>40,814129</td>
      <td>0,500000</td>
      <td>21.495000</td>
      <td>2,000000</td>
      <td>6,500000</td>
      <td>15,000000</td>
      <td>3,000000</td>
      <td>15,000000</td>
      <td>4,428571</td>
      <td>6,000000</td>
      <td>13,090753</td>
      <td>10,000000</td>
    </tr>
    <tr>
      <th>75%</th>
      <td>2,000000</td>
      <td>9,000000</td>
      <td>1,870000</td>
      <td>89,000000</td>
      <td>129,000000</td>
      <td>1,000000</td>
      <td>40,746146</td>
      <td>11,300000</td>
      <td>9,250000</td>
      <td>22,000000</td>
      <td>5,000000</td>
      <td>19,000000</td>
      <td>12,722222</td>
      <td>24,000000</td>
      <td>22,944737</td>
      <td>132,000000</td>
    </tr>
    <tr>
      <th>Maksymalny</th>
      <td>2,000000</td>
      <td>460,000000</td>
      <td>51,950000</td>
      <td>265,000000</td>
      <td>265,000000</td>
      <td>6,000000</td>
      <td>58,600000</td>
      <td>498.000000</td>
      <td>12,000000</td>
      <td>30,000000</td>
      <td>6,000000</td>
      <td>23,000000</td>
      <td>67,090909</td>
      <td>24,000000</td>
      <td>31,303665</td>
      <td>9999,000000</td>
    </tr>
  </tbody>
</table>
</div>

Ze statystyk podsumowujących widać, że istnieje kilka pól, które mają wartości odstających lub wartość, które spowodują zmniejszenie dokładności modelu. Najpierw odfiltruj pola lat/Long, aby znajdować się w tych samych granicach, które były używane do filtrowania danych pogody. `tripDistance` Pole zawiera nieprawidłowe dane, ponieważ minimalna wartość jest ujemna. `passengerCount` Pole ma również złe dane, a maksymalna wartość to 210 pasażerów. Na `totalAmount` koniec pole ma wartości ujemne, które nie mają sensu w kontekście naszego modelu.

Odfiltruj te anomalie przy użyciu funkcji zapytania, a następnie usuń kilka ostatnich kolumn niezbędnych do szkolenia.

```python
final_df = taxi_holidays_weather_df.query("pickupLatitude>=40.53 and pickupLatitude<=40.88")
final_df = final_df.query("pickupLongitude>=-74.09 and pickupLongitude<=-73.72")
final_df = final_df.query("tripDistance>0 and tripDistance<75")
final_df = final_df.query("passengerCount>0 and passengerCount<100")
final_df = final_df.query("totalAmount>0")

columns_to_remove_for_training = ["datetime", "pickupLongitude", "pickupLatitude", "dropoffLongitude", "dropoffLatitude", "country_code"]
for col in columns_to_remove_for_training:
    final_df.pop(col)
```

Ponownie `describe()` Wywołaj dane, aby upewnić się, że czyszczenie działało zgodnie z oczekiwaniami. Masz teraz przygotowany i wyczyszczony zestaw danych o taksówkach, dniach wolnych i pogoda, które mają być używane do szkolenia modelu uczenia maszynowego.

```python
final_df.describe()
```

<div>
<style scoped> .dataframe tbody tr th:only-of-type { vertical-align: middle; }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>vendorID</th>
      <th>passengerCount</th>
      <th>tripDistance</th>
      <th>totalAmount</th>
      <th>month_num</th>
      <th>day_of_month</th>
      <th>day_of_week</th>
      <th>hour_of_day</th>
      <th>snowDepth</th>
      <th>precipTime</th>
      <th>temperature</th>
      <th>precipDepth</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>count</th>
      <td>11765,000000</td>
      <td>11765,000000</td>
      <td>11765,000000</td>
      <td>11765,000000</td>
      <td>11765,000000</td>
      <td>11765,000000</td>
      <td>11765,000000</td>
      <td>11765,000000</td>
      <td>1490.000000</td>
      <td>11765,000000</td>
      <td>11765,000000</td>
      <td>11765,000000</td>
    </tr>
    <tr>
      <th>Średnia</th>
      <td>1,786910</td>
      <td>1,343476</td>
      <td>2,848488</td>
      <td>14,689039</td>
      <td>3,499788</td>
      <td>14,948916</td>
      <td>3,234254</td>
      <td>13,647344</td>
      <td>12,508581</td>
      <td>11,855929</td>
      <td>10,301433</td>
      <td>208.432384</td>
    </tr>
    <tr>
      <th>wartość</th>
      <td>0,409508</td>
      <td>1,001232</td>
      <td>2,895960</td>
      <td>10,289832</td>
      <td>1,707865</td>
      <td>8,442438</td>
      <td>1,958477</td>
      <td>6,640280</td>
      <td>16,203195</td>
      <td>10,125701</td>
      <td>8,553512</td>
      <td>1284,892832</td>
    </tr>
    <tr>
      <th>min.</th>
      <td>1,000000</td>
      <td>1,000000</td>
      <td>0,010000</td>
      <td>3,300000</td>
      <td>1,000000</td>
      <td>1,000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>3,000000</td>
      <td>1,000000</td>
      <td>-13,379464</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>25%</th>
      <td>2,000000</td>
      <td>1,000000</td>
      <td>1,070000</td>
      <td>8,160000</td>
      <td>2,000000</td>
      <td>8,000000</td>
      <td>2,000000</td>
      <td>9,000000</td>
      <td>3,000000</td>
      <td>1,000000</td>
      <td>3,504580</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>50%</th>
      <td>2,000000</td>
      <td>1,000000</td>
      <td>1,900000</td>
      <td>11,300000</td>
      <td>3,000000</td>
      <td>15,000000</td>
      <td>3,000000</td>
      <td>15,000000</td>
      <td>4,250000</td>
      <td>6,000000</td>
      <td>10,168182</td>
      <td>3,000000</td>
    </tr>
    <tr>
      <th>75%</th>
      <td>2,000000</td>
      <td>1,000000</td>
      <td>3,550000</td>
      <td>17.800000</td>
      <td>5,000000</td>
      <td>22,000000</td>
      <td>5,000000</td>
      <td>19,000000</td>
      <td>15,647059</td>
      <td>24,000000</td>
      <td>16,966923</td>
      <td>41,000000</td>
    </tr>
    <tr>
      <th>Maksymalny</th>
      <td>2,000000</td>
      <td>6,000000</td>
      <td>51,950000</td>
      <td>150,300000</td>
      <td>6,000000</td>
      <td>30,000000</td>
      <td>6,000000</td>
      <td>23,000000</td>
      <td>67,090909</td>
      <td>24,000000</td>
      <td>26,524107</td>
      <td>9999,000000</td>
    </tr>
  </tbody>
</table>
</div>

## <a name="train-a-model"></a>Szkolenie modelu

Teraz korzystasz z przygotowanych danych do uczenia zautomatyzowanego modelu uczenia maszynowego. Zacznij od dzielenia `final_df` na funkcje (wartości X) i etykiety (wartość y), które dla tego modelu są kosztami opłat za taksówkę.

```python
y_df = final_df.pop("totalAmount")
x_df = final_df
```

Teraz podzielisz dane na zestawy treningowe i testowe za pomocą funkcji `train_test_split()` z biblioteki `scikit-learn`. Parametr `test_size` określa procent danych przydzielanych do testowania. `random_state` Parametr ustawia inicjator do generatora liczb losowych, dzięki czemu podziały testu pociąga są deterministyczne.


```python
from sklearn.model_selection import train_test_split

X_train, X_test, y_train, y_test = train_test_split(x_df, y_df, test_size=0.2, random_state=222)
```

### <a name="load-workspace-and-configure-experiment"></a>Załaduj obszar roboczy i skonfiguruj eksperyment

Załaduj obszar roboczy usługi Azure Machine Learning przy `get()` użyciu funkcji z informacjami o subskrypcji i obszarze roboczym. Utwórz eksperyment w obszarze roboczym, aby przechowywać i monitorować uruchomienia modelu.


```python
from azureml.core.workspace import Workspace
from azureml.core.experiment import Experiment

workspace = Workspace.get(subscription_id="<your-subscription-id>", name="<your-workspace-name>", resource_group="<your-resource-group>")
experiment = Experiment(workspace, "opendatasets-ml")
```

Utwórz obiekt konfiguracji dla eksperymentu przy użyciu `AutoMLConfig` klasy. Dołączysz dane szkoleniowe, a ponadto określisz ustawienia i parametry, które kontrolują proces szkolenia. Parametry mają następujące cele:

* `task`: typ eksperymentu do uruchomienia.
* `X`: funkcje szkoleniowe.
* `y`: etykiety szkoleniowe.
* `iterations`: liczba iteracji do uruchomienia. Każda iteracja próbuje kombinację różnych metod normalizacji/standaryzacji funkcji i różne modele używające wielu parametrów.
* `primary_metric`: Podstawowa metryka do optymalizacji podczas uczenia modelu. Model najlepiej pasujący zostanie wybrany na podstawie tej metryki.
* `preprocess`: określa, czy eksperyment może wstępnie przetwarzać dane wejściowe (obsługa brakujących danych, konwertowanie tekstu na liczbowe itd.)
* `n_cross_validations`: Liczba podziałów krzyżowego sprawdzania poprawności w przypadku nieokreślenia danych weryfikacji.


```python
from azureml.train.automl import AutoMLConfig

automl_config = AutoMLConfig(task="regression",
                             X=X_train.values,
                             y=y_train.values.flatten(),
                             iterations=20,
                             primary_metric="spearman_correlation",
                             preprocess=True,
                             n_cross_validations=5
                            )
```

### <a name="submit-experiment"></a>Przesyłanie eksperymentu

Prześlij eksperyment do szkoleń. Po przesłaniu eksperymentu proces wykonuje iterację z użyciem różnych algorytmów uczenia maszynowego i ustawień hiperparametrycznych z uwzględnieniem zdefiniowanych ograniczeń. Wybiera model najlepiej pasujący przez optymalizację zdefiniowanej metryki dokładności. `automl_config` Przekaż obiekt do eksperymentu. Ustaw dane wyjściowe, `True` aby wyświetlić postęp podczas eksperymentu.

Po przesłaniu eksperymentu zobaczysz dane wyjściowe na żywo dla procesu szkolenia. Dla każdej iteracji zobaczysz Typ modelu i metodę normalizacji/standaryzacji funkcji, czas trwania przebiegu i dokładność szkolenia. Pole `BEST` umożliwia śledzenie najlepszego wyniku w uruchomionym treningu na podstawie typu metryki.

```python
training_run = experiment.submit(automl_config, show_output=True)
```

    Running on local machine
    Parent Run ID: AutoML_5c35f2a7-e479-4e7f-a131-ed4cb51e29d1
    Current status: DatasetEvaluation. Gathering dataset statistics.
    Current status: FeaturesGeneration. Generating features for the dataset.
    Current status: DatasetFeaturization. Featurizing the dataset.
    Current status: DatasetCrossValidationSplit. Generating individually featurized CV splits.
    Current status: DatasetFeaturization. Featurizing the dataset.
    Current status: DatasetFeaturization. Featurizing the dataset.
    Current status: DatasetFeaturization. Featurizing the dataset.
    Current status: DatasetFeaturization. Featurizing the dataset.
    Current status: DatasetFeaturization. Featurizing the dataset.
    Current status: DatasetFeaturization. Featurizing the dataset.
    Current status: DatasetFeaturization. Featurizing the dataset.
    Current status: DatasetFeaturization. Featurizing the dataset.
    Current status: DatasetFeaturization. Featurizing the dataset.
    Current status: DatasetFeaturization. Featurizing the dataset.
    Current status: ModelSelection. Beginning model selection.

    ****************************************************************************************************
    ITERATION: The iteration being evaluated.
    PIPELINE: A summary description of the pipeline being evaluated.
    DURATION: Time taken for the current iteration.
    METRIC: The result of computing score on the fitted pipeline.
    BEST: The best observed score thus far.
    ****************************************************************************************************

     ITERATION   PIPELINE                                       DURATION      METRIC      BEST
             0   MaxAbsScaler RandomForest                      0:00:07       0.9081    0.9081
             1   StandardScalerWrapper DecisionTree             0:00:05       0.9121    0.9121
             2   StandardScalerWrapper LightGBM                 0:00:04       0.9318    0.9318
             3   StandardScalerWrapper LightGBM                 0:00:07       0.9286    0.9318
             4   MaxAbsScaler LightGBM                          0:00:05       0.9246    0.9318
             5   MaxAbsScaler LightGBM                          0:00:05       0.9199    0.9318
             6   MaxAbsScaler RandomForest                      0:00:07       0.9327    0.9327
             7   StandardScalerWrapper ElasticNet               0:00:04       0.9371    0.9371
             8   MaxAbsScaler LightGBM                          0:00:05       0.9327    0.9371
             9   MaxAbsScaler SGD                               0:00:04       0.9077    0.9371
            10   MaxAbsScaler LightGBM                          0:00:04       0.9340    0.9371
            11   StandardScalerWrapper LightGBM                 0:00:04       0.8301    0.9371
            12   MaxAbsScaler DecisionTree                      0:00:05       0.9214    0.9371
            13   StandardScalerWrapper DecisionTree             0:00:04       0.9201    0.9371
            14   MaxAbsScaler DecisionTree                      0:00:05       0.9179    0.9371
            15   MaxAbsScaler ExtremeRandomTrees                0:00:05       0.9052    0.9371
            16   StandardScalerWrapper DecisionTree             0:00:04       0.9282    0.9371
            17   StandardScalerWrapper ElasticNet               0:00:04       0.9319    0.9371
            18   VotingEnsemble                                 0:00:16       0.9380    0.9380
            19   StackEnsemble                                  0:00:17       0.9376    0.9380

### <a name="retrieve-the-fitted-model"></a>Pobierz montowany model

Po zakończeniu wszystkich iteracji szkoleniowych zautomatyzowany proces uczenia maszynowego tworzy algorytm zestawu integralnego ze wszystkich poszczególnych uruchomień, z użyciem obsługi lub układania w stosie. Pobierz zamocowaną kompletną wartość `fitted_model`do zmiennej i najlepsze indywidualne uruchomienia w zmiennej. `best_run`

```python
best_run, fitted_model = training_run.get_output()
print(best_run)
print(fitted_model)
```

## <a name="test-model-accuracy"></a>Dokładność modelu testu

Użyj wbudowanego modelu zestawu, aby uruchomić przewidywania na testowym zestawie danych w celu przewidywania opłat za taksówkę. Funkcja `predict()` używa zainstalowanego modelu i przewidywalna wartości y, koszt opłaty `X_test` za taksówkę, dla zestawu danych.


```python
y_predict = fitted_model.predict(X_test.values)
```

Oblicz średnią kwadratową wartość błędu. Użyj ramki danych i przekonwertuj ją na listę `y_actual` , aby porównać do wartości prognozowanych. `y_test` Funkcja `mean_squared_error` pobiera dwie tablice wartości i oblicza średnią wartość błędu kwadratowego między nimi. Pobieranie pierwiastka kwadratowego z wyniku spowoduje wystąpienie błędu w tej samej jednostce co zmienna y, koszt. Wskazuje na to, jak daleko opłaty za przejazd z oddziałami są z rzeczywistej taryfy, podczas gdy silnie ważone są duże błędy.


```python
from sklearn.metrics import mean_squared_error
from math import sqrt

y_actual = y_test.values.flatten().tolist()
rmse = sqrt(mean_squared_error(y_actual, y_predict))
rmse
```




    4.178568987067901



Uruchom poniższy kod, aby obliczyć średnią bezwzględną wartość procentową błędu (MAPE) z użyciem pełnych zestawów danych `y_actual` i `y_predict`. Ta metryka oblicza wartości bezwzględne różnic między poszczególnymi wartościami przewidywanymi i rzeczywistymi oraz sumuje wszystkie różnice. Następnie wyrażenie to sumuje jako procent sumy wartości rzeczywistych.


```python
sum_actuals = sum_errors = 0

for actual_val, predict_val in zip(y_actual, y_predict):
    abs_error = actual_val - predict_val
    if abs_error < 0:
        abs_error = abs_error * -1

    sum_errors = sum_errors + abs_error
    sum_actuals = sum_actuals + actual_val

mean_abs_percent_error = sum_errors / sum_actuals
print("Model MAPE:")
print(mean_abs_percent_error)
print()
print("Model Accuracy:")
print(1 - mean_abs_percent_error)
```

    Model MAPE:
    0.14923619644924357

    Model Accuracy:
    0.8507638035507564

Uwzględniając, że użyto dość małego przykładu danych w odniesieniu do pełnego zestawu danych (n = 11748), dokładność modelu jest stosunkowo wysoka w 85%, z RMSE na około + – $4,00 w przypadku prognozowania opłat za taksówkę. W następnym kroku, aby poprawić dokładność, Wróć do drugiej komórki tego notesu i Zwiększ rozmiar próbki z 2 000 rekordów miesięcznie i ponownie uruchom cały eksperyment, aby ponownie szkolić model z większą ilością danych.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli nie planujesz korzystać z utworzonych zasobów, usuń je, aby nie nawiązać żadnych opłat.

1. W witrynie Azure Portal na końcu z lewej strony wybierz pozycję **Grupy zasobów**.
1. Wybierz utworzoną grupę zasobów z listy.
1. Wybierz pozycję **Usuń grupę zasobów**.
1. Wpisz nazwę grupy zasobów. Następnie wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

* Więcej przykładów kodu można znaleźć w [notesach](https://github.com/Azure/OpenDatasetsNotebooks) otwartych zestawów danych platformy Azure.
* Postępuj zgodnie z instrukcjami, [Aby](https://docs.microsoft.com/azure/machine-learning/service/how-to-configure-auto-train) uzyskać więcej informacji na temat automatycznego uczenia maszynowego w usłudze Azure Machine Learning.
