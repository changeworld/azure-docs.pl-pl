---
title: 'Samouczek: Wzbogacanie modelu uczenia maszynowego automatycznych'
titleSuffix: Azure Open Datasets
description: Dowiedz się, jak korzystać z wygody Otwórz zestawy danych platformy Azure wraz z możliwości usługi Azure Machine Learning, aby utworzyć model regresji do prognozowania cen taryfy taksówek NYC.
services: open-datasets
ms.service: open-datasets
ms.topic: tutorial
author: trevorbye
ms.author: trbye
ms.reviewer: trbye
ms.date: 05/02/2019
ms.openlocfilehash: a1df79c59ede8cd9ad72a2ebb2edb4bdb64b802a
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/05/2019
ms.locfileid: "67588967"
---
# <a name="tutorial-build-a-regression-model-with-automated-machine-learning-and-open-datasets"></a>Samouczek: Budowanie modelu regresji przy użyciu uczenia maszynowego zautomatyzowane i otwórz zestawów danych

W ramach tego samouczka możesz korzystać z wygody Otwórz zestawy danych platformy Azure wraz z możliwości usługi Azure Machine Learning, aby utworzyć model regresji do prognozowania cen taryfy taksówek NYC. Łatwe pobieranie taksówek publicznie dostępnych, świąteczne i dane pogodowe i konfigurowanie automatycznych eksperymentu uczenia maszynowego przy użyciu usługi Azure Machine Learning. Ten proces akceptuje szkolenia danych i ustawień konfiguracji i automatycznie wykonuje iterację przez kombinacji metod normalizacji/normalizacji różnych funkcji, modeli i hiperparametrycznego ustawienia, aby pojawić się w najlepszy model.

W tym samouczku dowiesz się następujące zadania:

- Konfigurowanie obszaru roboczego usługi Azure Machine Learning
- Konfigurowanie lokalnego środowiska Python
- Dostęp, przekształcania i łączenia danych przy użyciu platformy Azure otwartych zestawów danych
- Szkolenie automatycznych modelu regresji uczenia maszynowego
- Oblicz dokładności modelu

## <a name="prerequisites"></a>Wymagania wstępne

Ten samouczek wymaga następujących wymagań wstępnych.

* Obszar roboczy usługi Azure Machine Learning
* Środowisko Python 3.6

### <a name="create-a-workspace"></a>Tworzenie obszaru roboczego

Postępuj zgodnie z [instrukcje](https://docs.microsoft.com/azure/machine-learning/service/setup-create-workspace#portal) utworzyć obszar roboczy za pomocą witryny Azure portal, jeśli nie masz jeszcze jeden. Po utworzeniu Zanotuj nazwę obszaru roboczego, nazwa grupy zasobów i identyfikatora subskrypcji.

### <a name="create-a-python-environment"></a>Tworzenie środowiska Python

W tym przykładzie użyto środowisko Anaconda za pomocą notesów programu Jupyter, ale możesz uruchomić ten kod w dowolnym środowisku 3.6.x i za pomocą dowolnego edytora tekstu lub środowiska IDE. Wykonaj następujące kroki, aby utworzyć nowe środowisko opracowywania.

1. Jeśli nie masz jeszcze, [Pobierz](https://www.anaconda.com/distribution/) i zainstalowania pakietu Anaconda, a wybierz **wersji Python 3.7**.
1. Otwórz wiersz Anaconda i Utwórz nowe środowisko. Utworzenie środowiska, podczas gdy składniki i pakiety będą pobierane, potrwa kilka minut.
    ```
    conda create -n tutorialenv python=3.6.5
    ```
1. Aktywuj środowisko.
    ```
    conda activate tutorialenv
    ```
1. Włączenie specyficznych dla środowiska IPython jądra.
    ```
    conda install notebook ipykernel
    ```
1. Utwórz jądra.
    ```
    ipython kernel install --user
    ```
1. Instalowanie pakietów, czego potrzebujesz do tego samouczka. Te pakiety są duże i potrwa 5 – 10 minut, aby zainstalować.
    ```
    pip install azureml-sdk[automl] azureml-contrib-opendatasets
    ```
1. Uruchomić jądra notesu ze środowiska.
    ```
    jupyter notebook
    ```

Po wykonaniu tych kroków, sklonować [repozytorium notesu Otwórz zestawów danych](https://github.com/Azure/OpenDatasetsNotebooks) , a następnie otwórz **tutorials/taxi-automl/01-tutorial-opendatasets-automl.ipynb** Notes, aby go uruchomić.

## <a name="download-and-prepare-data"></a>Pobierz i przygotowywania danych

Zaimportuj niezbędne pakiety. Pakiet Open zestawów danych zawiera klasy reprezentujące każde źródło danych (`NycTlcGreen` na przykład) można łatwo filtrować podawaj parametrów daty przed pobraniem.


```python
from azureml.contrib.opendatasets import NycTlcGreen
import pandas as pd
from datetime import datetime
from dateutil.relativedelta import relativedelta
```

Rozpocznij od utworzenia ramkę danych zawierającą dane taksówek. Podczas pracy w środowisku-Spark, otwórz zestawów danych może składać się danymi przez miesiąc za pomocą niektórych klas, aby uniknąć pobierania `MemoryError` z dużymi zestawami danych. Aby pobrać dane taksówek w roku, iteracyjne pobrania jednego miesiąca naraz i przed dołączeniem do `green_taxi_df` losowo przykładowy 2000 rekordów w każdym miesiącu, aby uniknąć nadmiernego zwiększenia rozmiaru ramki danych. Następnie Wyświetl podgląd danych.

>[!NOTE]
> Otwieranie zestawów danych ma dublowania klasy do pracy w środowiskach platformy Spark, w których rozmiar danych i pamięci nie są problemem.

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
      <th>Identyfikator dostawcy</th>
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
      <th>paymentType</th>
      <th>fareAmount</th>
      <th>extra</th>
      <th>mtaTax</th>
      <th>improvementSurcharge</th>
      <th>tipAmount</th>
      <th>tollsAmount</th>
      <th>ehailFee</th>
      <th>totalAmount</th>
      <th>tripType</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>117695</th>
      <td>2</td>
      <td>2016-01-20 17:38:28</td>
      <td>2016-01-20 17:46:33</td>
      <td>1</td>
      <td>0.98</td>
      <td>Brak</td>
      <td>Brak</td>
      <td>-73.921715</td>
      <td>40.766682</td>
      <td>-73.916908</td>
      <td>Przyciski ...</td>
      <td>2.0</td>
      <td>7.0</td>
      <td>1.0</td>
      <td>0,5</td>
      <td>0.3</td>
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
      <td>3.08</td>
      <td>Brak</td>
      <td>Brak</td>
      <td>-73.979973</td>
      <td>40.677071</td>
      <td>-73.934349</td>
      <td>Przyciski ...</td>
      <td>2.0</td>
      <td>11.5</td>
      <td>0,5</td>
      <td>0,5</td>
      <td>0.3</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>12.8</td>
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
      <td>-73.863045</td>
      <td>40.882923</td>
      <td>-73.839836</td>
      <td>Przyciski ...</td>
      <td>2.0</td>
      <td>12.5</td>
      <td>0,5</td>
      <td>0,5</td>
      <td>0.3</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>13.8</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>1345223</th>
      <td>2</td>
      <td>2016-01-04 17:50:03</td>
      <td>2016-01-04 18:03:43</td>
      <td>1</td>
      <td>2.87</td>
      <td>Brak</td>
      <td>Brak</td>
      <td>-73.977730</td>
      <td>40.684647</td>
      <td>-73.931259</td>
      <td>Przyciski ...</td>
      <td>1.0</td>
      <td>12.0</td>
      <td>1.0</td>
      <td>0,5</td>
      <td>0.3</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>13.8</td>
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
      <td>-73.942589</td>
      <td>40.841423</td>
      <td>-73.943672</td>
      <td>Przyciski ...</td>
      <td>2.0</td>
      <td>4.5</td>
      <td>0.0</td>
      <td>0,5</td>
      <td>0.3</td>
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
      <td>40.759434</td>
      <td>-73.842422</td>
      <td>Przyciski ...</td>
      <td>2.0</td>
      <td>10.5</td>
      <td>1.0</td>
      <td>0,5</td>
      <td>0.3</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>12.3</td>
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
      <td>-73.927109</td>
      <td>40.762848</td>
      <td>-73.909302</td>
      <td>Przyciski ...</td>
      <td>1.0</td>
      <td>8.5</td>
      <td>0,5</td>
      <td>0,5</td>
      <td>0.3</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>9.8</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>438204</th>
      <td>1</td>
      <td>2016-01-09 14:25:02</td>
      <td>2016-01-09 14:32:48</td>
      <td>2</td>
      <td>0.80</td>
      <td>Brak</td>
      <td>Brak</td>
      <td>-73.881195</td>
      <td>40.741779</td>
      <td>-73.872086</td>
      <td>Przyciski ...</td>
      <td>2.0</td>
      <td>6.5</td>
      <td>0.0</td>
      <td>0,5</td>
      <td>0.3</td>
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
      <td>1.04</td>
      <td>Brak</td>
      <td>Brak</td>
      <td>-73.954376</td>
      <td>40.805729</td>
      <td>-73.939117</td>
      <td>Przyciski ...</td>
      <td>1.0</td>
      <td>8.0</td>
      <td>1.0</td>
      <td>0,5</td>
      <td>0.3</td>
      <td>1,5</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>11.3</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>269105</th>
      <td>2</td>
      <td>2016-01-24 20:46:50</td>
      <td>2016-01-24 21:04:03</td>
      <td>6</td>
      <td>2.82</td>
      <td>Brak</td>
      <td>Brak</td>
      <td>-73.845200</td>
      <td>40.722134</td>
      <td>-73.810638</td>
      <td>Przyciski ...</td>
      <td>1.0</td>
      <td>13.0</td>
      <td>0,5</td>
      <td>0,5</td>
      <td>0.3</td>
      <td>2.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>16.3</td>
      <td>1.0</td>
    </tr>
  </tbody>
</table>
<p>wiersze, kolumny 23 x 10</p>
</div>



Teraz, że początkowe dane są ładowane, należy zdefiniować funkcję, aby tworzyć różne funkcje oparte na czasie w polu odbioru daty/godziny. Spowoduje utworzenie nowych pól numer miesiąca, dzień miesiąca, dnia tygodnia i godzinę, dzień i umożliwi modelu, który ma wziąć pod uwagę oparte na czasie sezonowość. Funkcja dodaje również statycznych funkcji dla kodu kraju dołączyć dane dniem wolnym od pracy. Użyj `apply()` funkcji na ramkę danych można wielokrotnie zastosować `build_time_features()` funkcji do każdego wiersza danych taksówek.


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
      <th>Identyfikator dostawcy</th>
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
      <th>tripType</th>
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
      <td>0.98</td>
      <td>Brak</td>
      <td>Brak</td>
      <td>-73.921715</td>
      <td>40.766682</td>
      <td>-73.916908</td>
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
      <td>3.08</td>
      <td>Brak</td>
      <td>Brak</td>
      <td>-73.979973</td>
      <td>40.677071</td>
      <td>-73.934349</td>
      <td>Przyciski ...</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>12.8</td>
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
      <td>-73.863045</td>
      <td>40.882923</td>
      <td>-73.839836</td>
      <td>Przyciski ...</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>13.8</td>
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
      <td>2.87</td>
      <td>Brak</td>
      <td>Brak</td>
      <td>-73.977730</td>
      <td>40.684647</td>
      <td>-73.931259</td>
      <td>Przyciski ...</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>13.8</td>
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
      <td>-73.942589</td>
      <td>40.841423</td>
      <td>-73.943672</td>
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
      <td>40.759434</td>
      <td>-73.842422</td>
      <td>Przyciski ...</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>12.3</td>
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
      <td>-73.927109</td>
      <td>40.762848</td>
      <td>-73.909302</td>
      <td>Przyciski ...</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>9.8</td>
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
      <td>0.80</td>
      <td>Brak</td>
      <td>Brak</td>
      <td>-73.881195</td>
      <td>40.741779</td>
      <td>-73.872086</td>
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
      <td>1.04</td>
      <td>Brak</td>
      <td>Brak</td>
      <td>-73.954376</td>
      <td>40.805729</td>
      <td>-73.939117</td>
      <td>Przyciski ...</td>
      <td>1,5</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>11.3</td>
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
      <td>2.82</td>
      <td>Brak</td>
      <td>Brak</td>
      <td>-73.845200</td>
      <td>40.722134</td>
      <td>-73.810638</td>
      <td>Przyciski ...</td>
      <td>2.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>16.3</td>
      <td>1.0</td>
      <td>1</td>
      <td>24</td>
      <td>6</td>
      <td>20</td>
      <td>USA</td>
    </tr>
  </tbody>
</table>
<p>wiersze, kolumny 28 x 10</p>
</div>

Usuń kilka kolumn, które nie będzie konieczne w przypadku modelowania lub tworzenie dodatkowych funkcji. Zmień nazwę pola czasu do czasu odbioru, a ponadto przekonwertować wartość czasu do północy, za pomocą `pandas.Series.dt.normalize`. W tym celu wszystkie razem, gdy funkcje, aby składnik daty i godziny może przypadać później użyć go jako klucza podczas dołączania plików danych ze sobą na codzienne stopień szczegółowości.

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
      <th>Identyfikator dostawcy</th>
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
      <td>0.98</td>
      <td>-73.921715</td>
      <td>40.766682</td>
      <td>-73.916908</td>
      <td>40.761257</td>
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
      <td>3.08</td>
      <td>-73.979973</td>
      <td>40.677071</td>
      <td>-73.934349</td>
      <td>40.671654</td>
      <td>12.8</td>
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
      <td>-73.863045</td>
      <td>40.882923</td>
      <td>-73.839836</td>
      <td>40.868336</td>
      <td>13.8</td>
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
      <td>2.87</td>
      <td>-73.977730</td>
      <td>40.684647</td>
      <td>-73.931259</td>
      <td>40.694248</td>
      <td>13.8</td>
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
      <td>-73.942589</td>
      <td>40.841423</td>
      <td>-73.943672</td>
      <td>40.834396</td>
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

### <a name="enrich-with-holiday-data"></a>Wzbogacanie danych dzień wolny

Teraz, gdy masz dane taksówek pobrane i około przygotowane, Dodaj dzień wolny dane jako dodatkowe funkcje. Funkcje specyficzne dla święta pomoże dokładności modelu głównych dni wolnych od pracy to godziny gdzie taksówki gwałtowny wzrost popytu i dostaw staje się ograniczone. Święta zestaw danych jest stosunkowo mały, więc pobierania pełnego zestawu przy użyciu `PublicHolidays` klasa konstruktor bez parametrów w celu filtrowania. Wyświetl podgląd danych, aby sprawdzić, w formacie.

```python
from azureml.contrib.opendatasets import PublicHolidays
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
      <th>holidayName</th>
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
      <td>Nowy rok</td>
      <td>Brak</td>
      <td>AL</td>
      <td>Nowy rok</td>
      <td>2008-01-01</td>
    </tr>
    <tr>
      <th>40689</th>
      <td>Algeria</td>
      <td>Nowy rok</td>
      <td>Brak</td>
      <td>DZ</td>
      <td>Nowy rok</td>
      <td>2008-01-01</td>
    </tr>
    <tr>
      <th>40690</th>
      <td>Andora</td>
      <td>Nowy rok</td>
      <td>Brak</td>
      <td>AD</td>
      <td>Nowy rok</td>
      <td>2008-01-01</td>
    </tr>
    <tr>
      <th>40691</th>
      <td>Angola</td>
      <td>Nowy rok</td>
      <td>Brak</td>
      <td>AO</td>
      <td>Nowy rok</td>
      <td>2008-01-01</td>
    </tr>
    <tr>
      <th>40692</th>
      <td>Argentyna</td>
      <td>Nowy rok</td>
      <td>Brak</td>
      <td>AR</td>
      <td>Nowy rok</td>
      <td>2008-01-01</td>
    </tr>
  </tbody>
</table>
</div>



Zmień nazwę `countryRegionCode` i `date` kolumny, aby odpowiadać nazwom pól odpowiednie dane taksówek i również normalizacji czas, dlatego może służyć jako klucz. Następnie łączyć dane dniem wolnym od pracy z danymi taksówek, wykonując przy użyciu Pandas left join `merge()` funkcji. To zachowa wszystkie rekordy z `green_taxi_df`, ale Dodaj dzień wolny danych, gdy istnieje dla odpowiedniego `datetime` i `country_code`, która w tym przypadku jest zawsze `"US"`. Wyświetl podgląd danych, aby sprawdzić, czy zostały one scalane poprawnie.

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
      <th>Identyfikator dostawcy</th>
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
      <td>0.98</td>
      <td>-73.921715</td>
      <td>40.766682</td>
      <td>-73.916908</td>
      <td>40.761257</td>
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
      <td>3.08</td>
      <td>-73.979973</td>
      <td>40.677071</td>
      <td>-73.934349</td>
      <td>40.671654</td>
      <td>12.8</td>
      <td>1</td>
      <td>1</td>
      <td>4</td>
      <td>21</td>
      <td>USA</td>
      <td>True</td>
      <td>Nowy rok</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2</td>
      <td>2016-01-01</td>
      <td>1</td>
      <td>2.44</td>
      <td>-73.863045</td>
      <td>40.882923</td>
      <td>-73.839836</td>
      <td>40.868336</td>
      <td>13.8</td>
      <td>1</td>
      <td>1</td>
      <td>4</td>
      <td>0</td>
      <td>USA</td>
      <td>True</td>
      <td>Nowy rok</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2</td>
      <td>2016-01-04</td>
      <td>1</td>
      <td>2.87</td>
      <td>-73.977730</td>
      <td>40.684647</td>
      <td>-73.931259</td>
      <td>40.694248</td>
      <td>13.8</td>
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
      <td>-73.942589</td>
      <td>40.841423</td>
      <td>-73.943672</td>
      <td>40.834396</td>
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

### <a name="enrich-with-weather-data"></a>Wzbogacanie danych o pogodzie

Teraz Dołącz dane pogodowe powierzchni NOAA dane taksówek i dni wolnych. Użyj podejście podobne do pobrania danych o pogodzie, pobierając iteracyjne jednego miesiąca w danym momencie. Ponadto określ `cols` parametrem tablicę ciągów, aby filtrować kolumny, którą chcesz pobrać. Jest to bardzo duży zestaw danych zawierający powierzchni o pogodzie z całego świata, dlatego przed dołączeniem każdego miesiąca, pola lat/long do umieszczonej blisko NYC przy użyciu filtru `query()` funkcję dla ramki danych. Pozwoli to zagwarantować `weather_df` nie uzyskuje się zbyt duży.

```python
from azureml.contrib.opendatasets import NoaaIsdWeather

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
      <td>40.85</td>
      <td>0.0</td>
      <td>-74.061</td>
      <td>2016-01-01 00:51:00</td>
      <td>725025</td>
    </tr>
    <tr>
      <th>1754980</th>
      <td>94741</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>6.7</td>
      <td>40.85</td>
      <td>0.0</td>
      <td>-74.061</td>
      <td>2016-01-01 01:51:00</td>
      <td>725025</td>
    </tr>
    <tr>
      <th>1754981</th>
      <td>94741</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>6.7</td>
      <td>40.85</td>
      <td>0.0</td>
      <td>-74.061</td>
      <td>2016-01-01 02:51:00</td>
      <td>725025</td>
    </tr>
    <tr>
      <th>1754982</th>
      <td>94741</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>6.1</td>
      <td>40.85</td>
      <td>0.0</td>
      <td>-74.061</td>
      <td>2016-01-01 03:51:00</td>
      <td>725025</td>
    </tr>
    <tr>
      <th>1754983</th>
      <td>94741</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>5.6</td>
      <td>40.85</td>
      <td>0.0</td>
      <td>-74.061</td>
      <td>2016-01-01 04:51:00</td>
      <td>725025</td>
    </tr>
    <tr>
      <th>1754984</th>
      <td>94741</td>
      <td>24.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>40.85</td>
      <td>5.0</td>
      <td>-74.061</td>
      <td>2016-01-01 04:59:00</td>
      <td>725025</td>
    </tr>
    <tr>
      <th>1754985</th>
      <td>94741</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>40.85</td>
      <td>NaN</td>
      <td>-74.061</td>
      <td>2016-01-01 04:59:00</td>
      <td>725025</td>
    </tr>
    <tr>
      <th>1754986</th>
      <td>94741</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>5.6</td>
      <td>40.85</td>
      <td>0.0</td>
      <td>-74.061</td>
      <td>2016-01-01 05:51:00</td>
      <td>725025</td>
    </tr>
    <tr>
      <th>1754987</th>
      <td>94741</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>5.0</td>
      <td>40.85</td>
      <td>0.0</td>
      <td>-74.061</td>
      <td>2016-01-01 06:51:00</td>
      <td>725025</td>
    </tr>
    <tr>
      <th>1754988</th>
      <td>94741</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>5.0</td>
      <td>40.85</td>
      <td>0.0</td>
      <td>-74.061</td>
      <td>2016-01-01 07:51:00</td>
      <td>725025</td>
    </tr>
  </tbody>
</table>
</div>

Ponownie wywołaj `pandas.Series.dt.normalize` na `datetime` pole danych o pogodzie tak, aby odpowiadała klucza w czasie `taxi_holidays_df`. Usuń niepotrzebne kolumny i odfiltrować rekordy gdzie jest temperatura `NaN`.

Obok grupy danych o pogodzie, dzięki czemu mają codziennie zagregowane wartości o pogodzie. Zdefiniuj dict `aggregations` do definiowania sposobu agregacji w każdego pola na poziomie dzienny. Dla `snowDepth` i `temperature` obliczyć średnią i `precipTime` i `precipDepth` zająć codziennie maksymalnej. Użyj `groupby()` funkcji wraz z agregacji do grupowania danych. Wyświetl podgląd danych, aby upewnić się, że istnieje jeden rekord na dzień.

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
      <td>5.197345</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>2016-01-02</th>
      <td>NaN</td>
      <td>1.0</td>
      <td>2.567857</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>2016-01-03</th>
      <td>NaN</td>
      <td>1.0</td>
      <td>3.846429</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>2016-01-04</th>
      <td>NaN</td>
      <td>1.0</td>
      <td>0.123894</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>2016-01-05</th>
      <td>NaN</td>
      <td>6.0</td>
      <td>-7.206250</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>2016-01-06</th>
      <td>NaN</td>
      <td>6.0</td>
      <td>-0.896396</td>
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
      <td>4.384091</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>2016-01-09</th>
      <td>NaN</td>
      <td>6.0</td>
      <td>6.710274</td>
      <td>3.0</td>
    </tr>
    <tr>
      <th>2016-01-10</th>
      <td>NaN</td>
      <td>24.0</td>
      <td>10.943655</td>
      <td>254.0</td>
    </tr>
  </tbody>
</table>
</div>

> [!NOTE]
> W przykładach w tym samouczku scalania danych przy użyciu funkcji Pandas i niestandardowe agregacji, ale Otwórz SDK zestawów danych zawiera klasy przeznaczone do łatwego scalania i wzbogacanie zestawów danych. Zobacz [notesu](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-join/04-nyc-taxi-join-weather-in-pandas.ipynb) przykłady kodu te wzorce projektowe.

### <a name="cleanse-data"></a>Oczyszczanie danych

Scal dane taksówek i dni wolnych, przygotowanym za pomocą nowych danych o pogodzie. Czas ten jest konieczne tylko `datetime` klucza, a następnie ponownie wykonaj left join w danych. Uruchom `describe()` funkcji na nowe ramkę danych, aby wyświetlić statystyki podsumowujące dla każdego pola.

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
      <th>Identyfikator dostawcy</th>
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
      <td>24000.000000</td>
      <td>24000.000000</td>
      <td>24000.000000</td>
      <td>24000.000000</td>
      <td>24000.000000</td>
      <td>24000.000000</td>
      <td>24000.000000</td>
      <td>24000.000000</td>
      <td>24000.000000</td>
      <td>24000.000000</td>
      <td>24000.000000</td>
      <td>24000.000000</td>
      <td>1671.000000</td>
      <td>24000.000000</td>
      <td>24000.000000</td>
      <td>24000.000000</td>
    </tr>
    <tr>
      <th>Średnia</th>
      <td>1.786583</td>
      <td>6.576208</td>
      <td>1.582588</td>
      <td>20.505491</td>
      <td>84.936413</td>
      <td>-36.232825</td>
      <td>21.723144</td>
      <td>7.863018</td>
      <td>6.500000</td>
      <td>15.113708</td>
      <td>3.240250</td>
      <td>13.664125</td>
      <td>11.764141</td>
      <td>13.258875</td>
      <td>13.903524</td>
      <td>1056.644458</td>
    </tr>
    <tr>
      <th>standardowe</th>
      <td>0.409728</td>
      <td>9.086857</td>
      <td>2.418177</td>
      <td>108.847821</td>
      <td>70.678506</td>
      <td>37.650276</td>
      <td>19.104384</td>
      <td>10.648766</td>
      <td>3.452124</td>
      <td>8.485155</td>
      <td>1.956895</td>
      <td>6.650676</td>
      <td>15.651884</td>
      <td>10.339720</td>
      <td>9.474396</td>
      <td>2815.592754</td>
    </tr>
    <tr>
      <th>min.</th>
      <td>1.000000</td>
      <td>-60.000000</td>
      <td>-1.000000</td>
      <td>-74.179482</td>
      <td>0.000000</td>
      <td>-74.190704</td>
      <td>0.000000</td>
      <td>-52.800000</td>
      <td>1.000000</td>
      <td>1.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>3.000000</td>
      <td>1.000000</td>
      <td>-13.379464</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>25%</th>
      <td>2.000000</td>
      <td>1.000000</td>
      <td>0.330000</td>
      <td>-73.946680</td>
      <td>40.717712</td>
      <td>-73.945429</td>
      <td>1.770000</td>
      <td>1.000000</td>
      <td>3.750000</td>
      <td>8.000000</td>
      <td>2.000000</td>
      <td>9.000000</td>
      <td>3.000000</td>
      <td>1.000000</td>
      <td>6.620773</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>50%</th>
      <td>2.000000</td>
      <td>4.000000</td>
      <td>0.830000</td>
      <td>1.500000</td>
      <td>40.814129</td>
      <td>0.500000</td>
      <td>21.495000</td>
      <td>2.000000</td>
      <td>6.500000</td>
      <td>15.000000</td>
      <td>3.000000</td>
      <td>15.000000</td>
      <td>4.428571</td>
      <td>6.000000</td>
      <td>13.090753</td>
      <td>10.000000</td>
    </tr>
    <tr>
      <th>75%</th>
      <td>2.000000</td>
      <td>9.000000</td>
      <td>1.870000</td>
      <td>89.000000</td>
      <td>129.000000</td>
      <td>1.000000</td>
      <td>40.746146</td>
      <td>11.300000</td>
      <td>9.250000</td>
      <td>22.000000</td>
      <td>5.000000</td>
      <td>19.000000</td>
      <td>12.722222</td>
      <td>24.000000</td>
      <td>22.944737</td>
      <td>132.000000</td>
    </tr>
    <tr>
      <th>Maksymalna</th>
      <td>2.000000</td>
      <td>460.000000</td>
      <td>51.950000</td>
      <td>265.000000</td>
      <td>265.000000</td>
      <td>6.000000</td>
      <td>58.600000</td>
      <td>498.000000</td>
      <td>12.000000</td>
      <td>30.000000</td>
      <td>6.000000</td>
      <td>23.000000</td>
      <td>67.090909</td>
      <td>24.000000</td>
      <td>31.303665</td>
      <td>9999.000000</td>
    </tr>
  </tbody>
</table>
</div>

Z statystyki podsumowujące zostanie wyświetlony, istnieje kilka pól, które mają elementy odstające lub wartości, które spowoduje zmniejszenie dokładności modelu. Najpierw filtrowanie pól lat/długo się w granicach tej samej, używane do filtrowania danych o pogodzie. `tripDistance` Pole ma pewne złe dane ponieważ wartość minimalna jest ujemna. `passengerCount` Pole ma również nieprawidłowe dane z maksymalną wartość jest 210 pasażerów. Na koniec `totalAmount` pole ma ujemne wartości, które nie mają sensu w kontekście nasz model.

Filtrowanie tych anomalii przy użyciu funkcji zapytania, a następnie usuń ostatnie kilka kolumn, niepotrzebne szkolenia.

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

Wywołaj `describe()` ponownie na tych danych, aby upewnić się, czyszczenie zadziałała zgodnie z oczekiwaniami. Teraz masz przygotowany i oczyszczone zbiór taksówek, dniem wolnym od pracy i danych o pogodzie na potrzeby szkolenia modelu uczenia maszynowego.

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
      <th>Identyfikator dostawcy</th>
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
      <td>11765.000000</td>
      <td>11765.000000</td>
      <td>11765.000000</td>
      <td>11765.000000</td>
      <td>11765.000000</td>
      <td>11765.000000</td>
      <td>11765.000000</td>
      <td>11765.000000</td>
      <td>1490.000000</td>
      <td>11765.000000</td>
      <td>11765.000000</td>
      <td>11765.000000</td>
    </tr>
    <tr>
      <th>Średnia</th>
      <td>1.786910</td>
      <td>1.343476</td>
      <td>2.848488</td>
      <td>14.689039</td>
      <td>3.499788</td>
      <td>14.948916</td>
      <td>3.234254</td>
      <td>13.647344</td>
      <td>12.508581</td>
      <td>11.855929</td>
      <td>10.301433</td>
      <td>208.432384</td>
    </tr>
    <tr>
      <th>standardowe</th>
      <td>0.409508</td>
      <td>1.001232</td>
      <td>2.895960</td>
      <td>10.289832</td>
      <td>1.707865</td>
      <td>8.442438</td>
      <td>1.958477</td>
      <td>6.640280</td>
      <td>16.203195</td>
      <td>10.125701</td>
      <td>8.553512</td>
      <td>1284.892832</td>
    </tr>
    <tr>
      <th>min.</th>
      <td>1.000000</td>
      <td>1.000000</td>
      <td>0.010000</td>
      <td>3.300000</td>
      <td>1.000000</td>
      <td>1.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>3.000000</td>
      <td>1.000000</td>
      <td>-13.379464</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>25%</th>
      <td>2.000000</td>
      <td>1.000000</td>
      <td>1.070000</td>
      <td>8.160000</td>
      <td>2.000000</td>
      <td>8.000000</td>
      <td>2.000000</td>
      <td>9.000000</td>
      <td>3.000000</td>
      <td>1.000000</td>
      <td>3.504580</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>50%</th>
      <td>2.000000</td>
      <td>1.000000</td>
      <td>1.900000</td>
      <td>11.300000</td>
      <td>3.000000</td>
      <td>15.000000</td>
      <td>3.000000</td>
      <td>15.000000</td>
      <td>4.250000</td>
      <td>6.000000</td>
      <td>10.168182</td>
      <td>3.000000</td>
    </tr>
    <tr>
      <th>75%</th>
      <td>2.000000</td>
      <td>1.000000</td>
      <td>3.550000</td>
      <td>17.800000</td>
      <td>5.000000</td>
      <td>22.000000</td>
      <td>5.000000</td>
      <td>19.000000</td>
      <td>15.647059</td>
      <td>24.000000</td>
      <td>16.966923</td>
      <td>41.000000</td>
    </tr>
    <tr>
      <th>Maksymalna</th>
      <td>2.000000</td>
      <td>6.000000</td>
      <td>51.950000</td>
      <td>150.300000</td>
      <td>6.000000</td>
      <td>30.000000</td>
      <td>6.000000</td>
      <td>23.000000</td>
      <td>67.090909</td>
      <td>24.000000</td>
      <td>26.524107</td>
      <td>9999.000000</td>
    </tr>
  </tbody>
</table>
</div>

## <a name="train-a-model"></a>Szkolenie modelu

Teraz użyjesz przygotowanych danych do nauczenia modelu uczenia maszynowego automatycznych. Rozpocznij od podział `final_df` do funkcji (wartości X) i etykiet (wartość y), czyli dla tego modelu kosztów taryfy taksówek.

```python
y_df = final_df.pop("totalAmount")
x_df = final_df
```

Teraz podzielisz dane na zestawy treningowe i testowe za pomocą funkcji `train_test_split()` z biblioteki `scikit-learn`. Parametr `test_size` określa procent danych przydzielanych do testowania. `random_state` Parametr ustawia inicjator generator liczb losowych, tak aby były dzieli dane Twojego testu train deterministyczne.


```python
from sklearn.model_selection import train_test_split

X_train, X_test, y_train, y_test = train_test_split(x_df, y_df, test_size=0.2, random_state=222)
```

### <a name="load-workspace-and-configure-experiment"></a>Załaduj obszar roboczy i skonfigurować eksperyment

Ładowanie usługi Azure Machine Learning service obszaru roboczego przy użyciu `get()` funkcji z informacjami o subskrypcji i obszarów roboczych. Tworzenie eksperymentu w obrębie obszaru roboczego w celu przechowywania i monitorować uruchomienia modelu.


```python
from azureml.core.workspace import Workspace
from azureml.core.experiment import Experiment

workspace = Workspace.get(subscription_id="<your-subscription-id>", name="<your-workspace-name>", resource_group="<your-resource-group>")
experiment = Experiment(workspace, "opendatasets-ml")
```

Utwórz obiekt konfiguracji dla eksperymentu za pomocą `AutoMLConfig` klasy. Dołączanie danych szkoleniowych i dodatkowo określić ustawienia i parametry, które kontrolują procesu uczenia. Parametry są następujące cele:

* `task`: typ eksperyment do uruchomienia.
* `X`: uczenie funkcji.
* `y`: szkolenia etykiety.
* `iterations`: liczba iteracji do uruchomienia. Każda iteracja próbuje kombinacji metod normalizacji/normalizacji różnych funkcji i różne modele przy użyciu wielu ustawień hiperparametrycznego.
* `primary_metric`: podstawową metrykę, aby zoptymalizować podczas uczenia modelu. Najlepsze dopasowanie modelu zostanie wybrany w oparciu o tę metrykę.
* `preprocess`: Określa, czy eksperymentu wstępnego przetworzenia danych wejściowych (Obsługa brakuje danych, konwertowania tekstu na liczbowe, itp.)
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

Przesyłanie eksperymentu szkolenia. Po przesłaniu eksperymentu proces wykonuje iterację z użyciem różnych algorytmów uczenia maszynowego i ustawień hiperparametrycznych z uwzględnieniem zdefiniowanych ograniczeń. Wybiera model najlepszego dopasowania, optymalizując Metryka zdefiniowana dokładności. Przekaż `automl_config` obiekt do eksperymentu. Wartość danych wyjściowych `True` Aby wyświetlić postęp podczas eksperymentu.

Po przesłaniu eksperymentu można znaleźć w danych wyjściowych na żywo procesu uczenia. Dla każdej iteracji Zobacz model metoda normalizacji/normalizacji typów i funkcji, czasu trwania i dokładność szkolenia. Pole `BEST` umożliwia śledzenie najlepszego wyniku w uruchomionym treningu na podstawie typu metryki.

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

### <a name="retrieve-the-fitted-model"></a>Pobieranie dopasowanego modelu

Na końcu wszystkich iteracji szkolenia zautomatyzowane uczenia procesu maszynowego tworzy algorytm zespołu z wszystkie poszczególne przebiegi z pakowanie lub umieszczanie na stosie. Pobieranie zespołu dopasowanego do zmiennej `fitted_model`, a najlepiej pojedyncze uruchomienie, do zmiennej `best_run`.

```python
best_run, fitted_model = training_run.get_output()
print(best_run)
print(fitted_model)
```

## <a name="test-model-accuracy"></a>Dokładność modelu testu

Przy użyciu modelu zespołu zainstalowane do uruchamiania prognozy na zestawy danych testowych do prognozowania taryf taksówek. Funkcja `predict()` używa dopasowanego modelu i przewiduje wartości y, taksówek taryfy kosztów dla `X_test` zestawu danych.


```python
y_predict = fitted_model.predict(X_test.values)
```

Obliczenia średniej błędu kwadratów głównego wyniki. Użyj `y_test` ramkę danych oraz przekształcać je do listy `y_actual` do porównania z przewidywanych wartości. Funkcja `mean_squared_error` pobiera dwie tablice wartości i oblicza średnią wartość błędu kwadratowego między nimi. Biorąc pierwiastek kwadratowy daje w wyniku błędu w tej samej jednostki jako zmienna y, kosztów. Wskazuje około jak daleko prognozy taryfy taksówek są z taryf rzeczywiste, podczas intensywnie wagi dużych błędy.


```python
from sklearn.metrics import mean_squared_error
from math import sqrt

y_actual = y_test.values.flatten().tolist()
rmse = sqrt(mean_squared_error(y_actual, y_predict))
rmse
```




    4.178568987067901



Uruchom poniższy kod, aby obliczyć średnią bezwzględną wartość procentową błędu (MAPE) z użyciem pełnych zestawów danych `y_actual` i `y_predict`. Ta metryka oblicza wartości bezwzględne różnic między poszczególnymi wartościami przewidywanymi i rzeczywistymi oraz sumuje wszystkie różnice. Następnie wyrażenie Suma jako procent całkowitej wartości rzeczywistych.


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

Biorąc pod uwagę, że użyliśmy stosunkowo niewielką próbkę danych względem pełny zestaw danych (n = 11748), dokładności modelu jest stosunkowo wysoka 85%, za pomocą RMSE w całym błąd + - $4.00 Prognozowanie cen taryfy taksówek. Kolejnym krokiem potencjalne do poprawy dokładności, wróć do drugiej komórce ten notes i zwiększyć rozmiar próbki z 2000 rekordów w miesiącu i uruchamiania całą eksperymentów ponownie do ponownego trenowania większej ilości danych.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli nie planujesz używania zasobów, który został utworzony, usuń je, więc nie powodują naliczania żadnych opłat.

1. W witrynie Azure Portal na końcu z lewej strony wybierz pozycję **Grupy zasobów**.
1. Wybierz utworzoną grupę zasobów z listy.
1. Wybierz pozycję **Usuń grupę zasobów**.
1. Wpisz nazwę grupy zasobów. Następnie wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Kolejne kroki

* Zobacz Otwórz zestawy danych platformy Azure [notesów](https://github.com/Azure/OpenDatasetsNotebooks) więcej przykładów kodu.
* Postępuj zgodnie z [porad](https://docs.microsoft.com/azure/machine-learning/service/how-to-configure-auto-train) więcej informacji na temat automatycznego uczenia maszynowego w usłudze Azure Machine Learning.
