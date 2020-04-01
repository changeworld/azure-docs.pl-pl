---
title: 'Samouczek regresji: Zautomatyzowane ML'
titleSuffix: Azure Machine Learning
description: W tym samouczku dowiesz się, jak wygenerować model uczenia maszynowego przy użyciu automatycznego uczenia maszynowego. Korzystając z usługi Azure Machine Learning, można w sposób zautomatyzowany wykonywać wstępne przetwarzanie danych, wybierać algorytmy i hiperparametry.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: trevorbye
ms.author: trbye
ms.reviewer: trbye
ms.date: 02/10/2020
ms.openlocfilehash: 75e61ea3f4fa6c2b346f912a9effd66ad94e7e93
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77116452"
---
# <a name="tutorial-use-automated-machine-learning-to-predict-taxi-fares"></a>Samouczek: Użyj automatycznego uczenia maszynowego, aby przewidzieć ceny taksówek
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

W tym samouczku używasz automatycznego uczenia maszynowego w usłudze Azure Machine Learning, aby utworzyć model regresji do przewidywania cen biletów taksówkowych w Nowym Jorku. Ten proces akceptuje dane szkoleniowe i ustawienia konfiguracji i automatycznie iteruje za pomocą kombinacji różnych metod normalizacji/standaryzacji operacji, modeli i ustawień hiperparametru, aby uzyskać najlepszy model.

![Diagram przepływu](./media/tutorial-auto-train-models/flow2.png)

W tym samouczku dowiesz się następujących zadań:

> [!div class="checklist"]
> * Pobieranie, przekształcanie i czyszczenie danych przy użyciu zestawów danych Azure Open
> * Szkolenie zautomatyzowanego modelu regresji uczenia maszynowego
> * Obliczanie dokładności modelu

Jeśli nie masz subskrypcji Azure, przed rozpoczęciem utwórz bezpłatne konto. Wypróbuj [bezpłatną lub płatną wersję](https://aka.ms/AMLFree) usługi Azure Machine Learning już dziś.

## <a name="prerequisites"></a>Wymagania wstępne

* Wypełnij [samouczek konfiguracji,](tutorial-1st-experiment-sdk-setup.md) jeśli nie masz jeszcze obszaru roboczego usługi Azure Machine Learning lub maszyny wirtualnej notesu.
* Po zakończeniu samouczka instalacji otwórz *samouczek/regresję-automl-nyc-taxi-data/regresja-automated-ml.ipynb* notebooka przy użyciu tego samego serwera notebooka.

Ten samouczek jest również dostępny na [GitHub,](https://github.com/Azure/MachineLearningNotebooks/tree/master/tutorials) jeśli chcesz go uruchomić we własnym [środowisku lokalnym.](how-to-configure-environment.md#local) Uruchom, `pip install azureml-sdk[automl] azureml-opendatasets azureml-widgets` aby uzyskać wymagane pakiety.

## <a name="download-and-prepare-data"></a>Pobieranie i przygotowywanie danych

Importuj niezbędne pakiety. Pakiet Otwarte zestawy danych zawiera klasę reprezentującą`NycTlcGreen` każde źródło danych (na przykład), aby łatwo filtrować parametry daty przed pobraniem.

```python
from azureml.opendatasets import NycTlcGreen
import pandas as pd
from datetime import datetime
from dateutil.relativedelta import relativedelta
```

Rozpocznij od utworzenia ramki danych do przechowywania danych taksówki. Podczas pracy w środowisku innym niż Spark, Open Datasets umożliwia pobieranie tylko jeden `MemoryError` miesiąc danych w czasie z niektórych klas, aby uniknąć z dużych zestawów danych.

Aby pobrać dane taksówki, iteracyjne pobieranie jeden miesiąc na raz, `green_taxi_df` a przed dołączeniem go do losowo próbkowania 2000 rekordów z każdego miesiąca, aby uniknąć wzdęcia dataframe. Następnie wyświetl podgląd danych.


```python
green_taxi_df = pd.DataFrame([])
start = datetime.strptime("1/1/2015","%m/%d/%Y")
end = datetime.strptime("1/31/2015","%m/%d/%Y")

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
      <th>Vendorid</th>
      <th>lpepPickupCzas</th>
      <th>lpepDropoffCzas</th>
      <th>liczba pasażerów</th>
      <th>podróżdysty</th>
      <th>puLocationId</th>
      <th>doLocationId</th>
      <th>pickupDycjość</th>
      <th>odbiórLatitude</th>
      <th>dropoffDytude</th>
      <th>...</th>
      <th>płatnośćTylię</th>
      <th>opłataMount</th>
      <th>Dodatkowych</th>
      <th>mtaTax (mtaTax)</th>
      <th>ulepszenieSurcharge</th>
      <th>końcówkaMount</th>
      <th>opłata drogowaMount</th>
      <th>ehailFee (odejmuje)</th>
      <th>sumaMount</th>
      <th>tripType (typ tripType)</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>131969</th>
      <td>2</td>
      <td>2015-01-11 05:34:44</td>
      <td>2015-01-11 05:45:03</td>
      <td>3</td>
      <td>4.84</td>
      <td>Brak</td>
      <td>Brak</td>
      <td>-73.88</td>
      <td>40.84</td>
      <td>-73.94</td>
      <td>...</td>
      <td>2</td>
      <td>15.00</td>
      <td>0,50</td>
      <td>0,50</td>
      <td>0.3</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>Nan</td>
      <td>16.30</td>
      <td>1,00</td>
    </tr>
    <tr>
      <th>1129817</th>
      <td>2</td>
      <td>2015-01-20 16:26:29</td>
      <td>2015-01-20 16:30:26</td>
      <td>1</td>
      <td>0.69</td>
      <td>Brak</td>
      <td>Brak</td>
      <td>-73.96</td>
      <td>40.81</td>
      <td>-73.96</td>
      <td>...</td>
      <td>2</td>
      <td>4.50</td>
      <td>1,00</td>
      <td>0,50</td>
      <td>0.3</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>Nan</td>
      <td>6.30</td>
      <td>1,00</td>
    </tr>
    <tr>
      <th>1278620</th>
      <td>2</td>
      <td>2015-01-01 05:58:10</td>
      <td>2015-01-01 06:00:55</td>
      <td>1</td>
      <td>0.45</td>
      <td>Brak</td>
      <td>Brak</td>
      <td>-73.92</td>
      <td>40.76</td>
      <td>-73.91</td>
      <td>...</td>
      <td>2</td>
      <td>4,00</td>
      <td>0,00</td>
      <td>0,50</td>
      <td>0.3</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>Nan</td>
      <td>4,80</td>
      <td>1,00</td>
    </tr>
    <tr>
      <th>348430</th>
      <td>2</td>
      <td>2015-01-17 02:20:50</td>
      <td>2015-01-17 02:41:38</td>
      <td>1</td>
      <td>0,00</td>
      <td>Brak</td>
      <td>Brak</td>
      <td>-73.81</td>
      <td>40.70</td>
      <td>-73.82</td>
      <td>...</td>
      <td>2</td>
      <td>12.50</td>
      <td>0,50</td>
      <td>0,50</td>
      <td>0.3</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>Nan</td>
      <td>13.80</td>
      <td>1,00</td>
    </tr>
    <tr>
      <th>1269627</th>
      <td>1</td>
      <td>2015-01-01 05:04:10</td>
      <td>2015-01-01 05:06:23</td>
      <td>1</td>
      <td>0,50</td>
      <td>Brak</td>
      <td>Brak</td>
      <td>-73.92</td>
      <td>40.76</td>
      <td>-73.92</td>
      <td>...</td>
      <td>2</td>
      <td>4,00</td>
      <td>0,50</td>
      <td>0,50</td>
      <td>0</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>Nan</td>
      <td>5,00</td>
      <td>1,00</td>
    </tr>
    <tr>
      <th>811755</th>
      <td>1</td>
      <td>2015-01-04 19:57:51</td>
      <td>2015-01-04 20:05:45</td>
      <td>2</td>
      <td>1.10</td>
      <td>Brak</td>
      <td>Brak</td>
      <td>-73.96</td>
      <td>40.72</td>
      <td>-73.95</td>
      <td>...</td>
      <td>2</td>
      <td>6.50</td>
      <td>0,50</td>
      <td>0,50</td>
      <td>0.3</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>Nan</td>
      <td>7.80</td>
      <td>1,00</td>
    </tr>
    <tr>
      <th>737281</th>
      <td>1</td>
      <td>2015-01-03 12:27:31</td>
      <td>2015-01-03 12:33:52</td>
      <td>1</td>
      <td>0,90</td>
      <td>Brak</td>
      <td>Brak</td>
      <td>-73.88</td>
      <td>40.76</td>
      <td>-73.87</td>
      <td>...</td>
      <td>2</td>
      <td>6.00</td>
      <td>0,00</td>
      <td>0,50</td>
      <td>0.3</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>Nan</td>
      <td>6.80</td>
      <td>1,00</td>
    </tr>
    <tr>
      <th>113951</th>
      <td>1</td>
      <td>2015-01-09 23:25:51</td>
      <td>2015-01-09 23:39:52</td>
      <td>1</td>
      <td>3.30</td>
      <td>Brak</td>
      <td>Brak</td>
      <td>-73.96</td>
      <td>40.72</td>
      <td>-73.91</td>
      <td>...</td>
      <td>2</td>
      <td>12.50</td>
      <td>0,50</td>
      <td>0,50</td>
      <td>0.3</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>Nan</td>
      <td>13.80</td>
      <td>1,00</td>
    </tr>
    <tr>
      <th>150436</th>
      <td>2</td>
      <td>2015-01-11 17:15:14</td>
      <td>2015-01-11 17:22:57</td>
      <td>1</td>
      <td>1.19</td>
      <td>Brak</td>
      <td>Brak</td>
      <td>-73.94</td>
      <td>40.71</td>
      <td>-73.95</td>
      <td>...</td>
      <td>1</td>
      <td>7.00</td>
      <td>0,00</td>
      <td>0,50</td>
      <td>0.3</td>
      <td>1,75</td>
      <td>0,00</td>
      <td>Nan</td>
      <td>9.55</td>
      <td>1,00</td>
    </tr>
    <tr>
      <th>432136</th>
      <td>2</td>
      <td>2015-01-22 23:16:33</td>
      <td>2015-01-22 23:20:13</td>
      <td>1</td>
      <td>0.65</td>
      <td>Brak</td>
      <td>Brak</td>
      <td>-73.94</td>
      <td>40.71</td>
      <td>-73.94</td>
      <td>...</td>
      <td>2</td>
      <td>5,00</td>
      <td>0,50</td>
      <td>0,50</td>
      <td>0.3</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>Nan</td>
      <td>6.30</td>
      <td>1,00</td>
    </tr>
  </tbody>
</table>
<p>10 wierszy × 23 kolumny</p>
</div>


Po załadowaniu danych początkowych zdefiniuj funkcję, aby utworzyć różne funkcje oparte na czasie z pola datatime odbioru. Spowoduje to utworzenie nowych pól dla numeru miesiąca, dnia miesiąca, dnia tygodnia i godziny dnia i umożliwi modelowi uwzględnienie sezonowości opartej na czasie. Użyj `apply()` funkcji na ramie danych, aby `build_time_features()` iteracyjne zastosowanie funkcji do każdego wiersza w danych taksówki.

```python
def build_time_features(vector):
    pickup_datetime = vector[0]
    month_num = pickup_datetime.month
    day_of_month = pickup_datetime.day
    day_of_week = pickup_datetime.weekday()
    hour_of_day = pickup_datetime.hour

    return pd.Series((month_num, day_of_month, day_of_week, hour_of_day))

green_taxi_df[["month_num", "day_of_month","day_of_week", "hour_of_day"]] = green_taxi_df[["lpepPickupDatetime"]].apply(build_time_features, axis=1)
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
      <th>Vendorid</th>
      <th>lpepPickupCzas</th>
      <th>lpepDropoffCzas</th>
      <th>liczba pasażerów</th>
      <th>podróżdysty</th>
      <th>puLocationId</th>
      <th>doLocationId</th>
      <th>pickupDycjość</th>
      <th>odbiórLatitude</th>
      <th>dropoffDytude</th>
      <th>...</th>
      <th>ulepszenieSurcharge</th>
      <th>końcówkaMount</th>
      <th>opłata drogowaMount</th>
      <th>ehailFee (odejmuje)</th>
      <th>sumaMount</th>
      <th>tripType (typ tripType)</th>
      <th>month_num</th>
      <th>day_of_month</th>
      <th>day_of_week</th>
      <th>hour_of_day</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>131969</th>
      <td>2</td>
      <td>2015-01-11 05:34:44</td>
      <td>2015-01-11 05:45:03</td>
      <td>3</td>
      <td>4.84</td>
      <td>Brak</td>
      <td>Brak</td>
      <td>-73.88</td>
      <td>40.84</td>
      <td>-73.94</td>
      <td>...</td>
      <td>0.3</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>Nan</td>
      <td>16.30</td>
      <td>1,00</td>
      <td>1</td>
      <td>11</td>
      <td>6</td>
      <td>5</td>
    </tr>
    <tr>
      <th>1129817</th>
      <td>2</td>
      <td>2015-01-20 16:26:29</td>
      <td>2015-01-20 16:30:26</td>
      <td>1</td>
      <td>0.69</td>
      <td>Brak</td>
      <td>Brak</td>
      <td>-73.96</td>
      <td>40.81</td>
      <td>-73.96</td>
      <td>...</td>
      <td>0.3</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>Nan</td>
      <td>6.30</td>
      <td>1,00</td>
      <td>1</td>
      <td>20</td>
      <td>1</td>
      <td>16</td>
    </tr>
    <tr>
      <th>1278620</th>
      <td>2</td>
      <td>2015-01-01 05:58:10</td>
      <td>2015-01-01 06:00:55</td>
      <td>1</td>
      <td>0.45</td>
      <td>Brak</td>
      <td>Brak</td>
      <td>-73.92</td>
      <td>40.76</td>
      <td>-73.91</td>
      <td>...</td>
      <td>0.3</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>Nan</td>
      <td>4,80</td>
      <td>1,00</td>
      <td>1</td>
      <td>1</td>
      <td>3</td>
      <td>5</td>
    </tr>
    <tr>
      <th>348430</th>
      <td>2</td>
      <td>2015-01-17 02:20:50</td>
      <td>2015-01-17 02:41:38</td>
      <td>1</td>
      <td>0,00</td>
      <td>Brak</td>
      <td>Brak</td>
      <td>-73.81</td>
      <td>40.70</td>
      <td>-73.82</td>
      <td>...</td>
      <td>0.3</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>Nan</td>
      <td>13.80</td>
      <td>1,00</td>
      <td>1</td>
      <td>17</td>
      <td>5</td>
      <td>2</td>
    </tr>
    <tr>
      <th>1269627</th>
      <td>1</td>
      <td>2015-01-01 05:04:10</td>
      <td>2015-01-01 05:06:23</td>
      <td>1</td>
      <td>0,50</td>
      <td>Brak</td>
      <td>Brak</td>
      <td>-73.92</td>
      <td>40.76</td>
      <td>-73.92</td>
      <td>...</td>
      <td>0</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>Nan</td>
      <td>5,00</td>
      <td>1,00</td>
      <td>1</td>
      <td>1</td>
      <td>3</td>
      <td>5</td>
    </tr>
    <tr>
      <th>811755</th>
      <td>1</td>
      <td>2015-01-04 19:57:51</td>
      <td>2015-01-04 20:05:45</td>
      <td>2</td>
      <td>1.10</td>
      <td>Brak</td>
      <td>Brak</td>
      <td>-73.96</td>
      <td>40.72</td>
      <td>-73.95</td>
      <td>...</td>
      <td>0.3</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>Nan</td>
      <td>7.80</td>
      <td>1,00</td>
      <td>1</td>
      <td>4</td>
      <td>6</td>
      <td>19</td>
    </tr>
    <tr>
      <th>737281</th>
      <td>1</td>
      <td>2015-01-03 12:27:31</td>
      <td>2015-01-03 12:33:52</td>
      <td>1</td>
      <td>0,90</td>
      <td>Brak</td>
      <td>Brak</td>
      <td>-73.88</td>
      <td>40.76</td>
      <td>-73.87</td>
      <td>...</td>
      <td>0.3</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>Nan</td>
      <td>6.80</td>
      <td>1,00</td>
      <td>1</td>
      <td>3</td>
      <td>5</td>
      <td>12</td>
    </tr>
    <tr>
      <th>113951</th>
      <td>1</td>
      <td>2015-01-09 23:25:51</td>
      <td>2015-01-09 23:39:52</td>
      <td>1</td>
      <td>3.30</td>
      <td>Brak</td>
      <td>Brak</td>
      <td>-73.96</td>
      <td>40.72</td>
      <td>-73.91</td>
      <td>...</td>
      <td>0.3</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>Nan</td>
      <td>13.80</td>
      <td>1,00</td>
      <td>1</td>
      <td>9</td>
      <td>4</td>
      <td>23</td>
    </tr>
    <tr>
      <th>150436</th>
      <td>2</td>
      <td>2015-01-11 17:15:14</td>
      <td>2015-01-11 17:22:57</td>
      <td>1</td>
      <td>1.19</td>
      <td>Brak</td>
      <td>Brak</td>
      <td>-73.94</td>
      <td>40.71</td>
      <td>-73.95</td>
      <td>...</td>
      <td>0.3</td>
      <td>1,75</td>
      <td>0,00</td>
      <td>Nan</td>
      <td>9.55</td>
      <td>1,00</td>
      <td>1</td>
      <td>11</td>
      <td>6</td>
      <td>17</td>
    </tr>
    <tr>
      <th>432136</th>
      <td>2</td>
      <td>2015-01-22 23:16:33</td>
      <td>2015-01-22 23:20:13</td>
      <td>1</td>
      <td>0.65</td>
      <td>Brak</td>
      <td>Brak</td>
      <td>-73.94</td>
      <td>40.71</td>
      <td>-73.94</td>
      <td>...</td>
      <td>0.3</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>Nan</td>
      <td>6.30</td>
      <td>1,00</td>
      <td>1</td>
      <td>22</td>
      <td>3</td>
      <td>23</td>
    </tr>
  </tbody>
</table>
<p>10 wierszy × 27 kolumn</p>
</div>

Usuń niektóre kolumny, których nie potrzebujesz do szkolenia lub tworzenia dodatkowych funkcji.

```python
columns_to_remove = ["lpepPickupDatetime", "lpepDropoffDatetime", "puLocationId", "doLocationId", "extra", "mtaTax",
                     "improvementSurcharge", "tollsAmount", "ehailFee", "tripType", "rateCodeID",
                     "storeAndFwdFlag", "paymentType", "fareAmount", "tipAmount"
                    ]
for col in columns_to_remove:
    green_taxi_df.pop(col)

green_taxi_df.head(5)
```

### <a name="cleanse-data"></a>Oczyszczanie danych

Uruchom `describe()` funkcję na nowej ramce danych, aby wyświetlić statystyki podsumowania dla każdego pola.

```python
green_taxi_df.describe()
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
      <th>Vendorid</th>
      <th>liczba pasażerów</th>
      <th>podróżdysty</th>
      <th>pickupDycjość</th>
      <th>odbiórLatitude</th>
      <th>dropoffDytude</th>
      <th>dropoffLatitude (dropoffLatitude)</th>
      <th>sumaMount</th>
      <th>month_num</th>
      <th>day_of_month</th>
      <th>day_of_week</th>
      <th>hour_of_day</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>count</th>
      <td>48000.00</td>
      <td>48000.00</td>
      <td>48000.00</td>
      <td>48000.00</td>
      <td>48000.00</td>
      <td>48000.00</td>
      <td>48000.00</td>
      <td>48000.00</td>
      <td>48000.00</td>
      <td>48000.00</td>
      <td>48000.00</td>
      <td>48000.00</td>
    </tr>
    <tr>
      <th>średnia</th>
      <td>1.78</td>
      <td>1.37</td>
      <td>2.87</td>
      <td>-73.83</td>
      <td>40.69</td>
      <td>-73.84</td>
      <td>40.70</td>
      <td>14.75</td>
      <td>6.50</td>
      <td>15.13</td>
      <td>3.27</td>
      <td>13.52</td>
    </tr>
    <tr>
      <th>Std</th>
      <td>0,41</td>
      <td>1.04</td>
      <td>2,93</td>
      <td>2.76</td>
      <td>1,52</td>
      <td>2.61</td>
      <td>1.44</td>
      <td>12.08</td>
      <td>3.45</td>
      <td>8.45</td>
      <td>1.95</td>
      <td>6.83</td>
    </tr>
    <tr>
      <th>min</th>
      <td>1,00</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>-74.66</td>
      <td>0,00</td>
      <td>-74.66</td>
      <td>0,00</td>
      <td>-300.00</td>
      <td>1,00</td>
      <td>1,00</td>
      <td>0,00</td>
      <td>0,00</td>
    </tr>
    <tr>
      <th>25%</th>
      <td>2,00</td>
      <td>1,00</td>
      <td>1.06</td>
      <td>-73.96</td>
      <td>40.70</td>
      <td>-73.97</td>
      <td>40.70</td>
      <td>7.80</td>
      <td>3,75</td>
      <td>8.00</td>
      <td>2,00</td>
      <td>9.00</td>
    </tr>
    <tr>
      <th>50%</th>
      <td>2,00</td>
      <td>1,00</td>
      <td>1.90</td>
      <td>-73.94</td>
      <td>40.75</td>
      <td>-73.94</td>
      <td>40.75</td>
      <td>11.30</td>
      <td>6.50</td>
      <td>15.00</td>
      <td>3,00</td>
      <td>15.00</td>
    </tr>
    <tr>
      <th>75%</th>
      <td>2,00</td>
      <td>1,00</td>
      <td>3,60</td>
      <td>-73.92</td>
      <td>40.80</td>
      <td>-73.91</td>
      <td>40.79</td>
      <td>17.80</td>
      <td>9.25</td>
      <td>22.00</td>
      <td>5,00</td>
      <td>19.00</td>
    </tr>
    <tr>
      <th>max</th>
      <td>2,00</td>
      <td>9.00</td>
      <td>97.57</td>
      <td>0,00</td>
      <td>41.93</td>
      <td>0,00</td>
      <td>41.94</td>
      <td>450.00</td>
      <td>12.00</td>
      <td>30.00</td>
      <td>6.00</td>
      <td>23.00</td>
    </tr>
  </tbody>
</table>
</div>


Na podstawie statystyk podsumowania widać, że istnieje kilka pól, które mają wartości odstające lub wartości, które zmniejszą dokładność modelu. Najpierw przefiltruj pola łat/długie, aby znajdować się w granicach obszaru Manhattanu. Spowoduje to odfiltrowanie dłuższych podróży taksówką lub wycieczek, które są odstające w odniesieniu do ich relacji z innymi funkcjami.

Dodatkowo filtrować `tripDistance` pole być większe niż zero, ale mniej niż 31 mil (odległość haversine między dwoma lat / długie pary). Eliminuje to długie odstające podróże, które mają niespójne koszty podróży.

Wreszcie `totalAmount` pole ma wartości ujemne dla taryf taksówek, które nie mają sensu `passengerCount` w kontekście naszego modelu, a pole ma złe dane, przy czym wartości minimalne są zerowe.

Odfiltruj te anomalie przy użyciu funkcji kwerendy, a następnie usuń kilka ostatnich kolumn niepotrzebnych do szkolenia.


```python
final_df = green_taxi_df.query("pickupLatitude>=40.53 and pickupLatitude<=40.88")
final_df = final_df.query("pickupLongitude>=-74.09 and pickupLongitude<=-73.72")
final_df = final_df.query("tripDistance>=0.25 and tripDistance<31")
final_df = final_df.query("passengerCount>0 and totalAmount>0")

columns_to_remove_for_training = ["pickupLongitude", "pickupLatitude", "dropoffLongitude", "dropoffLatitude"]
for col in columns_to_remove_for_training:
    final_df.pop(col)
```

Ponownie `describe()` zadzwoń do danych, aby zapewnić czyszczenie pracował zgodnie z oczekiwaniami. Masz teraz przygotowany i oczyszczony zestaw danych taksówek, wakacji i pogody do wykorzystania do szkolenia modelu uczenia maszynowego.

```python
final_df.describe()
```

## <a name="configure-workspace"></a>Konfigurowanie obszaru roboczego

Utwórz obiekt obszaru roboczego na podstawie istniejącego obszaru roboczego. [Obszar roboczy](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) to klasa, która akceptuje informacje o subskrypcji i zasobach platformy Azure. Tworzy ona również zasób w chmurze służący do monitorowania i śledzenia przebiegów modelu. `Workspace.from_config()`odczytuje plik **config.json** i ładuje szczegóły `ws`uwierzytelniania do obiektu o nazwie . Obiekt `ws` jest używany w kodzie w tym samouczku.

```python
from azureml.core.workspace import Workspace
ws = Workspace.from_config()
```

## <a name="split-the-data-into-train-and-test-sets"></a>Podział danych na zestawy treningowe i testowe

Podziel dane na zestawy szkoleniowe `train_test_split` i testowe przy użyciu funkcji w `scikit-learn` bibliotece. Ta funkcja segreguje dane do zestawu danych x **(funkcje)** dla szkolenia modelu i y **(wartości do przewidzenia)** zestaw danych do testowania.

Parametr `test_size` określa procent danych przydzielanych do testowania. Parametr `random_state` ustawia materiał siewny generatora losowego, dzięki czemu podziały testu pociągu są deterministyczne.

```python
from sklearn.model_selection import train_test_split

y_df = final_df.pop("totalAmount")
x_df = final_df

x_train, x_test, y_train, y_test = train_test_split(x_df, y_df, test_size=0.2, random_state=223)
```

Celem tego etapu jest uzyskanie punktów danych do przetestowania ukończono modelu, które nie były używane do jego wytrenowania. Pozwoli to zmierzyć rzeczywistą dokładność.

Innymi słowy, dobrze wytrenowany model powinien umożliwiać dokładne prognozowanie na podstawie danych, które jeszcze się nie pojawiły. Masz teraz dane przygotowane do automatycznego szkolenia modelu uczenia maszynowego.

## <a name="automatically-train-a-model"></a>Automatyczne trenowanie modelu

Aby przeprowadzić automatyczne trenowanie modelu, wykonaj następujące czynności:
1. Zdefiniuj ustawienia przebiegu eksperymentu. Dołącz do konfiguracji dane treningowe i zmodyfikuj ustawienia, które sterują procesem treningu.
1. Prześlij eksperyment do strojenia modelu. Po przesłaniu eksperymentu proces wykonuje iterację z użyciem różnych algorytmów uczenia maszynowego i ustawień hiperparametrycznych z uwzględnieniem zdefiniowanych ograniczeń. Na podstawie zoptymalizowanej metryki dokładności jest wybierany model o najlepszym dopasowaniu.

### <a name="define-training-settings"></a>Definiowanie ustawień treningu

Zdefiniuj parametr eksperymentu i ustawienia modelu dla szkolenia. Wyświetl pełną listę [ustawień](how-to-configure-auto-train.md). Przesłanie eksperymentu z tymi ustawieniami domyślnymi zajmie około 5-20 minut, ale `experiment_timeout_minutes` jeśli chcesz skrócić czas wykonywania, zmniejsz parametr.

|Właściwość| Wartość w ramach tego samouczka |Opis|
|----|----|---|
|**iteration_timeout_minutes**|2|Limit czasu w minutach dla każdej iteracji. Zmniejszenie tej wartości powoduje skrócenie całkowitego czasu wykonywania.|
|**experiment_timeout_minutes**|20|Maksymalny czas w minutach, który wszystkie iteracje połączone może potrwać przed zakończeniem eksperymentu.|
|**enable_early_stopping**|True|Flaga, aby umożliwić wcześniejsze zakończenie, jeśli wynik nie poprawia się w krótkim okresie.|
|**primary_metric**| spearman_correlation | Metryka, który ma być optymalizowana. Na podstawie tej metryki zostanie wybrany model o najlepszym dopasowaniu.|
|**featurization**| auto | Za pomocą **auto**eksperyment może wstępnie przetworzyć dane wejściowe (obsługa brakujących danych, konwertowanie tekstu na numeryczne itp.)|
|**verbosity**| logging.INFO | Steruje poziomem rejestrowania.|
|**n_cross_validations**|5|Liczba podziałów krzyżowego sprawdzania poprawności w przypadku nieokreślenia danych weryfikacji.|

```python
import logging

automl_settings = {
    "iteration_timeout_minutes": 2,
    "experiment_timeout_minutes": 20,
    "enable_early_stopping": True,
    "primary_metric": 'spearman_correlation',
    "featurization": 'auto',
    "verbosity": logging.INFO,
    "n_cross_validations": 5
}
```

Użyj zdefiniowanych ustawień `**kwargs` treningu `AutoMLConfig` jako parametru obiektu. Ponadto określ dane treningowe i typ modelu — w tym przypadku `regression`.

```python
from azureml.train.automl import AutoMLConfig

automl_config = AutoMLConfig(task='regression',
                             debug_log='automated_ml_errors.log',
                             X=x_train.values,
                             y=y_train.values.flatten(),
                             **automl_settings)
```

> [!NOTE]
> Zautomatyzowane kroki wstępnego przetwarzania uczenia maszynowego (normalizacja funkcji, obsługa brakujących danych, konwertowanie tekstu na numeryczne itp.) stają się częścią modelu źródłowego. Podczas korzystania z modelu dla prognoz, te same kroki przetwarzania wstępnego stosowane podczas szkolenia są stosowane do danych wejściowych automatycznie.

### <a name="train-the-automatic-regression-model"></a>Trenowanie automatycznego modelu regresji

Utwórz obiekt eksperymentu w obszarze roboczym. Eksperyment działa jako kontener dla poszczególnych przebiegów. Przekaż zdefiniowany `automl_config` obiekt do eksperymentu i `True` ustaw dane wyjściowe, aby wyświetlić postęp podczas uruchamiania.

Po uruchomieniu eksperymentu dane wyjściowe wyświetlane są aktualizowane na żywo podczas uruchamiania eksperymentu. W każdej iteracji widać typ modelu, czas trwania i dokładność treningu. Pole `BEST` umożliwia śledzenie najlepszego wyniku w uruchomionym treningu na podstawie typu metryki.

```python
from azureml.core.experiment import Experiment
experiment = Experiment(ws, "taxi-experiment")
local_run = experiment.submit(automl_config, show_output=True)
```

    Running on local machine
    Parent Run ID: AutoML_1766cdf7-56cf-4b28-a340-c4aeee15b12b
    Current status: DatasetFeaturization. Beginning to featurize the dataset.
    Current status: DatasetEvaluation. Gathering dataset statistics.
    Current status: FeaturesGeneration. Generating features for the dataset.
    Current status: DatasetFeaturizationCompleted. Completed featurizing the dataset.
    Current status: DatasetCrossValidationSplit. Generating individually featurized CV splits.
    Current status: ModelSelection. Beginning model selection.

    ****************************************************************************************************
    ITERATION: The iteration being evaluated.
    PIPELINE: A summary description of the pipeline being evaluated.
    DURATION: Time taken for the current iteration.
    METRIC: The result of computing score on the fitted pipeline.
    BEST: The best observed score thus far.
    ****************************************************************************************************

     ITERATION   PIPELINE                                       DURATION      METRIC      BEST
             0   StandardScalerWrapper RandomForest             0:00:16       0.8746    0.8746
             1   MinMaxScaler RandomForest                      0:00:15       0.9468    0.9468
             2   StandardScalerWrapper ExtremeRandomTrees       0:00:09       0.9303    0.9468
             3   StandardScalerWrapper LightGBM                 0:00:10       0.9424    0.9468
             4   RobustScaler DecisionTree                      0:00:09       0.9449    0.9468
             5   StandardScalerWrapper LassoLars                0:00:09       0.9440    0.9468
             6   StandardScalerWrapper LightGBM                 0:00:10       0.9282    0.9468
             7   StandardScalerWrapper RandomForest             0:00:12       0.8946    0.9468
             8   StandardScalerWrapper LassoLars                0:00:16       0.9439    0.9468
             9   MinMaxScaler ExtremeRandomTrees                0:00:35       0.9199    0.9468
            10   RobustScaler ExtremeRandomTrees                0:00:19       0.9411    0.9468
            11   StandardScalerWrapper ExtremeRandomTrees       0:00:13       0.9077    0.9468
            12   StandardScalerWrapper LassoLars                0:00:15       0.9433    0.9468
            13   MinMaxScaler ExtremeRandomTrees                0:00:14       0.9186    0.9468
            14   RobustScaler RandomForest                      0:00:10       0.8810    0.9468
            15   StandardScalerWrapper LassoLars                0:00:55       0.9433    0.9468
            16   StandardScalerWrapper ExtremeRandomTrees       0:00:13       0.9026    0.9468
            17   StandardScalerWrapper RandomForest             0:00:13       0.9140    0.9468
            18   VotingEnsemble                                 0:00:23       0.9471    0.9471
            19   StackEnsemble                                  0:00:27       0.9463    0.9471

## <a name="explore-the-results"></a>Eksplorowanie wyników

Poznaj wyniki automatycznego szkolenia za pomocą [widżetu Jupyter](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py). Widżet pozwala zobaczyć wykres i tabelę wszystkich poszczególnych iteracji uruchamiania, wraz z metrykami dokładności treningu i metadanymi. Ponadto można filtrować na różnych metryki dokładności niż metryki podstawowej z selektorem listy rozwijanej.

```python
from azureml.widgets import RunDetails
RunDetails(local_run).show()
```

![Szczegóły przebiegu widżetu Jupyter](./media/tutorial-auto-train-models/automl-dash-output.png)
![Wykres widżetu Jupyter](./media/tutorial-auto-train-models/automl-chart-output.png)

### <a name="retrieve-the-best-model"></a>Pobieranie najlepszego modelu

Wybierz najlepszy model z iteracji. Funkcja `get_output` zwraca najlepszy przebieg i dopasowany model dla wywołania ostatniego dopasowania. Za pomocą przeciążenia `get_output`na , można pobrać najlepszy uruchomić i dopasowany model dla wszystkich zarejestrowanych metryki lub określonej iteracji.

```python
best_run, fitted_model = local_run.get_output()
print(best_run)
print(fitted_model)
```

### <a name="test-the-best-model-accuracy"></a>Testowanie dokładności najlepszego modelu

Użyj najlepszego modelu, aby uruchomić prognozy na zestawie danych testowych, aby przewidzieć ceny taksówek. Funkcja `predict` wykorzystuje najlepszy model i przewiduje wartości y, **koszt podróży**, z zestawu `x_test` danych. Wyświetl pierwsze 10 wartości przewidywanego kosztu z zestawu `y_predict`.

```python
y_predict = fitted_model.predict(x_test.values)
print(y_predict[:10])
```

Oblicz wartość `root mean squared error` dla wyników. Konwertuj ramkę danych na `y_test` listę, aby porównać ją z przewidywanymi wartościami. Funkcja `mean_squared_error` pobiera dwie tablice wartości i oblicza średnią wartość błędu kwadratowego między nimi. Wyciągnięcie pierwiastka kwadratowego z wyniku powoduje błąd w tych samych jednostkach co zmienna y **koszt**. Wskazuje mniej więcej, jak daleko prognozy taryfy taksówek są od rzeczywistych taryf.

```python
from sklearn.metrics import mean_squared_error
from math import sqrt

y_actual = y_test.values.flatten().tolist()
rmse = sqrt(mean_squared_error(y_actual, y_predict))
rmse
```

Uruchom następujący kod, aby obliczyć średni błąd procentu bezwzględnego (MAPE) przy użyciu pełnych `y_actual` i `y_predict` zestawów danych. Ta metryka oblicza wartości bezwzględne różnic między poszczególnymi wartościami przewidywanymi i rzeczywistymi oraz sumuje wszystkie różnice. Następnie wyraża tę sumę jako procent sumy rzeczywistych wartości.

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
    0.14353867606052823

    Model Accuracy:
    0.8564613239394718


Z dwóch wskaźników dokładności przewidywania, widać, że model jest dość dobry w przewidywaniu taryf taksówek z funkcji zestawu danych, zazwyczaj w granicach +- 4,00 dolarów i około 15% błąd.

Tradycyjny proces opracowywania modelu uczenia maszynowego intensywnie korzysta z zasobów. Wymaga dużej wiedzy o danej dziedzinie oraz czasu, który trzeba poświęcić na uruchamianie kilkudziesięciu modeli i porównywanie ich wyników. Użycie automatycznego uczenia maszynowego jest doskonałym sposobem na szybkie przetestowanie wielu różnych modeli w danym scenariuszu.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Nie należy ukończyć tej sekcji, jeśli planujesz uruchomienie innych samouczków usługi Azure Machine Learning.

### <a name="stop-the-compute-instance"></a>Zatrzymywać wystąpienie obliczeń

[!INCLUDE [aml-stop-server](../../includes/aml-stop-server.md)]

### <a name="delete-everything"></a>Usuń wszystko

Jeśli nie zamierzasz używać utworzonych zasobów, usuń je, aby nie ponosić żadnych opłat.

1. W witrynie Azure Portal na końcu z lewej strony wybierz pozycję **Grupy zasobów**.
1. Wybierz utworzoną grupę zasobów z listy.
1. Wybierz pozycję **Usuń grupę zasobów**.
1. Wpisz nazwę grupy zasobów. Następnie wybierz pozycję **Usuń**.

Możesz też zachować grupę zasobów i usunąć jeden obszar roboczy. Wyświetl właściwości obszaru roboczego i wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

W tym samouczku zautomatyzowanego uczenia maszynowego wykonano następujące czynności:

> [!div class="checklist"]
> * Skonfigurowano obszar roboczy i przygotowano dane do eksperymentu.
> * Przeprowadzono trenowanie przy użyciu zautomatyzowanego modelu regresji lokalnie z użyciem niestandardowych parametrów.
> * Zbadano i przejrzano wyniki trenowania.

[Wdróż model](tutorial-deploy-models-with-aml.md) za pomocą usługi Azure Machine Learning.
