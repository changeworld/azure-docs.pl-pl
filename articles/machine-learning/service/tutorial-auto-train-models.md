---
title: 'Regression tutorial: Automated ML'
titleSuffix: Azure Machine Learning
description: In this tutorial, you learn how to generate a machine learning model by using automated machine learning. Korzystając z usługi Azure Machine Learning, można w sposób zautomatyzowany wykonywać wstępne przetwarzanie danych, wybierać algorytmy i hiperparametry.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: trevorbye
ms.author: trbye
ms.reviewer: trbye
ms.date: 11/04/2019
ms.openlocfilehash: 2a47b6851d9c3250913783c8a63b666e8a8474d9
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2019
ms.locfileid: "74483542"
---
# <a name="tutorial-use-automated-machine-learning-to-predict-taxi-fares"></a>Tutorial: Use automated machine learning to predict taxi fares
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

In this tutorial, you use automated machine learning in Azure Machine Learning to create a regression model to predict NYC taxi fare prices. This process accepts training data and configuration settings, and automatically iterates through combinations of different feature normalization/standardization methods, models, and hyperparameter settings to arrive at the best model.

![Diagram przepływu](./media/tutorial-auto-train-models/flow2.png)

In this tutorial you learn the following tasks:

> [!div class="checklist"]
> * Download, transform, and clean data using Azure Open Datasets
> * Train an automated machine learning regression model
> * Calculate model accuracy

Jeśli nie masz subskrypcji Azure, przed rozpoczęciem utwórz bezpłatne konto. Try the [free or paid version](https://aka.ms/AMLFree) of Azure Machine Learning today.

## <a name="prerequisites"></a>Wymagania wstępne

* Complete the [setup tutorial](tutorial-1st-experiment-sdk-setup.md) if you don't already have an Azure Machine Learning workspace or notebook virtual machine.
* After you complete the setup tutorial, open the **tutorials/regression-automated-ml.ipynb** notebook using the same notebook server.

This tutorial is also available on [GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/tutorials) if you wish to run it in your own [local environment](how-to-configure-environment.md#local). Run `pip install azureml-sdk[automl] azureml-opendatasets azureml-widgets` to get the required packages.

## <a name="download-and-prepare-data"></a>Download and prepare data

Import the necessary packages. The Open Datasets package contains a class representing each data source (`NycTlcGreen` for example) to easily filter date parameters before downloading.

```python
from azureml.opendatasets import NycTlcGreen
import pandas as pd
from datetime import datetime
from dateutil.relativedelta import relativedelta
```

Begin by creating a dataframe to hold the taxi data. When working in a non-Spark environment, Open Datasets only allows downloading one month of data at a time with certain classes to avoid `MemoryError` with large datasets.

To download taxi data, iteratively fetch one month at a time, and before appending it to `green_taxi_df` randomly sample 2,000 records from each month to avoid bloating the dataframe. Then preview the data.


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
      <td>Przyciski ...</td>
      <td>2</td>
      <td>15.00</td>
      <td>0.50</td>
      <td>0.50</td>
      <td>0.3</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>nan</td>
      <td>16.30</td>
      <td>1.00</td>
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
      <td>Przyciski ...</td>
      <td>2</td>
      <td>4.50</td>
      <td>1.00</td>
      <td>0.50</td>
      <td>0.3</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>nan</td>
      <td>6.30</td>
      <td>1.00</td>
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
      <td>Przyciski ...</td>
      <td>2</td>
      <td>4.00</td>
      <td>0.00</td>
      <td>0.50</td>
      <td>0.3</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>nan</td>
      <td>4.80</td>
      <td>1.00</td>
    </tr>
    <tr>
      <th>348430</th>
      <td>2</td>
      <td>2015-01-17 02:20:50</td>
      <td>2015-01-17 02:41:38</td>
      <td>1</td>
      <td>0.00</td>
      <td>Brak</td>
      <td>Brak</td>
      <td>-73.81</td>
      <td>40.70</td>
      <td>-73.82</td>
      <td>Przyciski ...</td>
      <td>2</td>
      <td>12.50</td>
      <td>0.50</td>
      <td>0.50</td>
      <td>0.3</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>nan</td>
      <td>13.80</td>
      <td>1.00</td>
    </tr>
    <tr>
      <th>1269627</th>
      <td>1</td>
      <td>2015-01-01 05:04:10</td>
      <td>2015-01-01 05:06:23</td>
      <td>1</td>
      <td>0.50</td>
      <td>Brak</td>
      <td>Brak</td>
      <td>-73.92</td>
      <td>40.76</td>
      <td>-73.92</td>
      <td>Przyciski ...</td>
      <td>2</td>
      <td>4.00</td>
      <td>0.50</td>
      <td>0.50</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>nan</td>
      <td>5.00</td>
      <td>1.00</td>
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
      <td>Przyciski ...</td>
      <td>2</td>
      <td>6.50</td>
      <td>0.50</td>
      <td>0.50</td>
      <td>0.3</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>nan</td>
      <td>7.80</td>
      <td>1.00</td>
    </tr>
    <tr>
      <th>737281</th>
      <td>1</td>
      <td>2015-01-03 12:27:31</td>
      <td>2015-01-03 12:33:52</td>
      <td>1</td>
      <td>0.90</td>
      <td>Brak</td>
      <td>Brak</td>
      <td>-73.88</td>
      <td>40.76</td>
      <td>-73.87</td>
      <td>Przyciski ...</td>
      <td>2</td>
      <td>6,00</td>
      <td>0.00</td>
      <td>0.50</td>
      <td>0.3</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>nan</td>
      <td>6.80</td>
      <td>1.00</td>
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
      <td>Przyciski ...</td>
      <td>2</td>
      <td>12.50</td>
      <td>0.50</td>
      <td>0.50</td>
      <td>0.3</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>nan</td>
      <td>13.80</td>
      <td>1.00</td>
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
      <td>Przyciski ...</td>
      <td>1</td>
      <td>7.00</td>
      <td>0.00</td>
      <td>0.50</td>
      <td>0.3</td>
      <td>1,75</td>
      <td>0.00</td>
      <td>nan</td>
      <td>9.55</td>
      <td>1.00</td>
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
      <td>Przyciski ...</td>
      <td>2</td>
      <td>5.00</td>
      <td>0.50</td>
      <td>0.50</td>
      <td>0.3</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>nan</td>
      <td>6.30</td>
      <td>1.00</td>
    </tr>
  </tbody>
</table>
<p>10 rows × 23 columns</p>
</div>


Now that the initial data is loaded, define a function to create various time-based features from the pickup datetime field. This will create new fields for the month number, day of month, day of week, and hour of day, and will allow the model to factor in time-based seasonality. Use the `apply()` function on the dataframe to iteratively apply the `build_time_features()` function to each row in the taxi data.

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
      <th>improvementSurcharge</th>
      <th>tipAmount</th>
      <th>tollsAmount</th>
      <th>ehailFee</th>
      <th>totalAmount</th>
      <th>tripType</th>
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
      <td>Przyciski ...</td>
      <td>0.3</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>nan</td>
      <td>16.30</td>
      <td>1.00</td>
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
      <td>Przyciski ...</td>
      <td>0.3</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>nan</td>
      <td>6.30</td>
      <td>1.00</td>
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
      <td>Przyciski ...</td>
      <td>0.3</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>nan</td>
      <td>4.80</td>
      <td>1.00</td>
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
      <td>0.00</td>
      <td>Brak</td>
      <td>Brak</td>
      <td>-73.81</td>
      <td>40.70</td>
      <td>-73.82</td>
      <td>Przyciski ...</td>
      <td>0.3</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>nan</td>
      <td>13.80</td>
      <td>1.00</td>
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
      <td>0.50</td>
      <td>Brak</td>
      <td>Brak</td>
      <td>-73.92</td>
      <td>40.76</td>
      <td>-73.92</td>
      <td>Przyciski ...</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>nan</td>
      <td>5.00</td>
      <td>1.00</td>
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
      <td>Przyciski ...</td>
      <td>0.3</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>nan</td>
      <td>7.80</td>
      <td>1.00</td>
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
      <td>0.90</td>
      <td>Brak</td>
      <td>Brak</td>
      <td>-73.88</td>
      <td>40.76</td>
      <td>-73.87</td>
      <td>Przyciski ...</td>
      <td>0.3</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>nan</td>
      <td>6.80</td>
      <td>1.00</td>
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
      <td>Przyciski ...</td>
      <td>0.3</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>nan</td>
      <td>13.80</td>
      <td>1.00</td>
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
      <td>Przyciski ...</td>
      <td>0.3</td>
      <td>1,75</td>
      <td>0.00</td>
      <td>nan</td>
      <td>9.55</td>
      <td>1.00</td>
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
      <td>Przyciski ...</td>
      <td>0.3</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>nan</td>
      <td>6.30</td>
      <td>1.00</td>
      <td>1</td>
      <td>22</td>
      <td>3</td>
      <td>23</td>
    </tr>
  </tbody>
</table>
<p>10 rows × 27 columns</p>
</div>

Remove some of the columns that you won't need for training or additional feature building.

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

Run the `describe()` function on the new dataframe to see summary statistics for each field.

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
      <th>mean</th>
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
      <th>std</th>
      <td>0.41</td>
      <td>1.04</td>
      <td>2.93</td>
      <td>2.76</td>
      <td>1.52</td>
      <td>2.61</td>
      <td>1.44</td>
      <td>12.08</td>
      <td>3.45</td>
      <td>8.45</td>
      <td>1.95</td>
      <td>6.83</td>
    </tr>
    <tr>
      <th>min.</th>
      <td>1.00</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>-74.66</td>
      <td>0.00</td>
      <td>-74.66</td>
      <td>0.00</td>
      <td>-300.00</td>
      <td>1.00</td>
      <td>1.00</td>
      <td>0.00</td>
      <td>0.00</td>
    </tr>
    <tr>
      <th>25%</th>
      <td>2.00</td>
      <td>1.00</td>
      <td>1.06</td>
      <td>-73.96</td>
      <td>40.70</td>
      <td>-73.97</td>
      <td>40.70</td>
      <td>7.80</td>
      <td>3.75</td>
      <td>8,00</td>
      <td>2.00</td>
      <td>9.00</td>
    </tr>
    <tr>
      <th>50%</th>
      <td>2.00</td>
      <td>1.00</td>
      <td>1.90</td>
      <td>-73.94</td>
      <td>40.75</td>
      <td>-73.94</td>
      <td>40.75</td>
      <td>11.30</td>
      <td>6.50</td>
      <td>15.00</td>
      <td>3.00</td>
      <td>15.00</td>
    </tr>
    <tr>
      <th>75%</th>
      <td>2.00</td>
      <td>1.00</td>
      <td>3.60</td>
      <td>-73.92</td>
      <td>40.80</td>
      <td>-73.91</td>
      <td>40.79</td>
      <td>17.80</td>
      <td>9.25</td>
      <td>22.00</td>
      <td>5.00</td>
      <td>19.00</td>
    </tr>
    <tr>
      <th>max</th>
      <td>2.00</td>
      <td>9.00</td>
      <td>97.57</td>
      <td>0.00</td>
      <td>41.93</td>
      <td>0.00</td>
      <td>41.94</td>
      <td>450.00</td>
      <td>12.00</td>
      <td>30.00</td>
      <td>6,00</td>
      <td>23.00</td>
    </tr>
  </tbody>
</table>
</div>


From the summary statistics, you see that there are several fields that have outliers or values that will reduce model accuracy. First filter the lat/long fields to be within the bounds of the Manhattan area. This will filter out longer taxi trips or trips that are outliers in respect to their relationship with other features.

Additionally filter the `tripDistance` field to be greater than zero but less than 31 miles (the haversine distance between the two lat/long pairs). This eliminates long outlier trips that have inconsistent trip cost.

Lastly, the `totalAmount` field has negative values for the taxi fares, which don't make sense in the context of our model, and the `passengerCount` field has bad data with the minimum values being zero.

Filter out these anomalies using query functions, and then remove the last few columns unnecessary for training.


```python
final_df = green_taxi_df.query("pickupLatitude>=40.53 and pickupLatitude<=40.88")
final_df = final_df.query("pickupLongitude>=-74.09 and pickupLongitude<=-73.72")
final_df = final_df.query("tripDistance>=0.25 and tripDistance<31")
final_df = final_df.query("passengerCount>0 and totalAmount>0")

columns_to_remove_for_training = ["pickupLongitude", "pickupLatitude", "dropoffLongitude", "dropoffLatitude"]
for col in columns_to_remove_for_training:
    final_df.pop(col)
```

Call `describe()` again on the data to ensure cleansing worked as expected. You now have a prepared and cleansed set of taxi, holiday, and weather data to use for machine learning model training.

```python
final_df.describe()
```

## <a name="configure-workspace"></a>Konfigurowanie obszaru roboczego

Utwórz obiekt obszaru roboczego na podstawie istniejącego obszaru roboczego. A [Workspace](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) is a class that accepts your Azure subscription and resource information. Tworzy ona również zasób w chmurze służący do monitorowania i śledzenia przebiegów modelu. `Workspace.from_config()` reads the file **config.json** and loads the authentication details into an object named `ws`. Obiekt `ws` jest używany w kodzie w tym samouczku.

```python
from azureml.core.workspace import Workspace
ws = Workspace.from_config()
```

## <a name="split-the-data-into-train-and-test-sets"></a>Podział danych na zestawy treningowe i testowe

Split the data into training and test sets by using the `train_test_split` function in the `scikit-learn` library. This function segregates the data into the x (**features**) data set for model training and the y (**values to predict**) data set for testing.

Parametr `test_size` określa procent danych przydzielanych do testowania. The `random_state` parameter sets a seed to the random generator, so that your train-test splits are deterministic.

```python
from sklearn.model_selection import train_test_split

y_df = final_df.pop("totalAmount")
x_df = final_df

x_train, x_test, y_train, y_test = train_test_split(x_df, y_df, test_size=0.2, random_state=223)
```

Celem tego etapu jest uzyskanie punktów danych do przetestowania ukończono modelu, które nie były używane do jego wytrenowania. Pozwoli to zmierzyć rzeczywistą dokładność.

Innymi słowy, dobrze wytrenowany model powinien umożliwiać dokładne prognozowanie na podstawie danych, które jeszcze się nie pojawiły. You now have data prepared for auto-training a machine learning model.

## <a name="automatically-train-a-model"></a>Automatyczne trenowanie modelu

Aby przeprowadzić automatyczne trenowanie modelu, wykonaj następujące czynności:
1. Zdefiniuj ustawienia przebiegu eksperymentu. Dołącz do konfiguracji dane treningowe i zmodyfikuj ustawienia, które sterują procesem treningu.
1. Prześlij eksperyment do strojenia modelu. Po przesłaniu eksperymentu proces wykonuje iterację z użyciem różnych algorytmów uczenia maszynowego i ustawień hiperparametrycznych z uwzględnieniem zdefiniowanych ograniczeń. Na podstawie zoptymalizowanej metryki dokładności jest wybierany model o najlepszym dopasowaniu.

### <a name="define-training-settings"></a>Define training settings

Define the experiment parameter and model settings for training. Wyświetl pełną listę [ustawień](how-to-configure-auto-train.md). Submitting the experiment with these default settings will take approximately 5-20 min, but if you want a shorter run time, reduce the `experiment_timeout_minutes` parameter.

|Właściwość| Wartość w ramach tego samouczka |Opis|
|----|----|---|
|**iteration_timeout_minutes**|2|Limit czasu w minutach dla każdej iteracji. Zmniejszenie tej wartości powoduje skrócenie całkowitego czasu wykonywania.|
|**experiment_timeout_minutes**|20|Maximum amount of time in minutes that all iterations combined can take before the experiment terminates.|
|**enable_early_stopping**|Prawda|Flag to enble early termination if the score is not improving in the short term.|
|**primary_metric**| spearman_correlation | Metryka, który ma być optymalizowana. Na podstawie tej metryki zostanie wybrany model o najlepszym dopasowaniu.|
|**featurization**| auto | By using **auto**, the experiment can preprocess the input data (handling missing data, converting text to numeric, etc.)|
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

Use your defined training settings as a `**kwargs` parameter to an `AutoMLConfig` object. Ponadto określ dane treningowe i typ modelu — w tym przypadku `regression`.

```python
from azureml.train.automl import AutoMLConfig

automl_config = AutoMLConfig(task='regression',
                             debug_log='automated_ml_errors.log',
                             X=x_train.values,
                             y=y_train.values.flatten(),
                             **automl_settings)
```

> [!NOTE]
> Automated machine learning pre-processing steps (feature normalization, handling missing data, converting text to numeric, etc.) become part of the underlying model. When using the model for predictions, the same pre-processing steps applied during training are applied to your input data automatically.

### <a name="train-the-automatic-regression-model"></a>Trenowanie automatycznego modelu regresji

Create an experiment object in your workspace. An experiment acts as a container for your individual runs. Pass the defined `automl_config` object to the experiment, and set the output to `True` to view progress during the run.

After starting the experiment, the output shown updates live as the experiment runs. W każdej iteracji widać typ modelu, czas trwania i dokładność treningu. Pole `BEST` umożliwia śledzenie najlepszego wyniku w uruchomionym treningu na podstawie typu metryki.

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

Explore the results of automatic training with a [Jupyter widget](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py). The widget allows you to see a graph and table of all individual run iterations, along with training accuracy metrics and metadata. Additionally, you can filter on different accuracy metrics than your primary metric with the dropdown selector.

```python
from azureml.widgets import RunDetails
RunDetails(local_run).show()
```

![Szczegóły przebiegu widżetu Jupyter](./media/tutorial-auto-train-models/automl-dash-output.png)
![Wykres widżetu Jupyter](./media/tutorial-auto-train-models/automl-chart-output.png)

### <a name="retrieve-the-best-model"></a>Pobieranie najlepszego modelu

Select the best model from your iterations. The `get_output` function returns the best run and the fitted model for the last fit invocation. By using the overloads on `get_output`, you can retrieve the best run and fitted model for any logged metric or a particular iteration.

```python
best_run, fitted_model = local_run.get_output()
print(best_run)
print(fitted_model)
```

### <a name="test-the-best-model-accuracy"></a>Testowanie dokładności najlepszego modelu

Use the best model to run predictions on the test data set to predict taxi fares. The function `predict` uses the best model and predicts the values of y, **trip cost**, from the `x_test` data set. Wyświetl pierwsze 10 wartości przewidywanego kosztu z zestawu `y_predict`.

```python
y_predict = fitted_model.predict(x_test.values)
print(y_predict[:10])
```

Oblicz wartość `root mean squared error` dla wyników. Convert the `y_test` dataframe to a list to compare to the predicted values. Funkcja `mean_squared_error` pobiera dwie tablice wartości i oblicza średnią wartość błędu kwadratowego między nimi. Wyciągnięcie pierwiastka kwadratowego z wyniku powoduje błąd w tych samych jednostkach co zmienna y **koszt**. It indicates roughly how far the taxi fare predictions are from the actual fares.

```python
from sklearn.metrics import mean_squared_error
from math import sqrt

y_actual = y_test.values.flatten().tolist()
rmse = sqrt(mean_squared_error(y_actual, y_predict))
rmse
```

Run the following code to calculate mean absolute percent error (MAPE) by using the full `y_actual` and `y_predict` data sets. Ta metryka oblicza wartości bezwzględne różnic między poszczególnymi wartościami przewidywanymi i rzeczywistymi oraz sumuje wszystkie różnice. Then it expresses that sum as a percent of the total of the actual values.

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


From the two prediction accuracy metrics, you see that the model is fairly good at predicting taxi fares from the data set's features, typically within +- $4.00, and approximately 15% error.

Tradycyjny proces opracowywania modelu uczenia maszynowego intensywnie korzysta z zasobów. Wymaga dużej wiedzy o danej dziedzinie oraz czasu, który trzeba poświęcić na uruchamianie kilkudziesięciu modeli i porównywanie ich wyników. Użycie automatycznego uczenia maszynowego jest doskonałym sposobem na szybkie przetestowanie wielu różnych modeli w danym scenariuszu.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Do not complete this section if you plan on running other Azure Machine Learning tutorials.

### <a name="stop-the-notebook-vm"></a>Stop the Notebook VM

[!INCLUDE [aml-stop-server](../../../includes/aml-stop-server.md)]

### <a name="delete-everything"></a>Delete everything

If you don't plan to use the resources you created, delete them, so you don't incur any charges.

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
