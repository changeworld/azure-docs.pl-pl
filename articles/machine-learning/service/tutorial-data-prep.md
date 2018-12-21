---
title: 'Samouczek dotyczący modelu regresji: Przygotowywanie danych'
titleSuffix: Azure Machine Learning service
description: Z pierwszej części tego samouczka dowiesz się, jak przygotować dane w języku Python do modelowania regresji z użyciem zestawu Azure ML SDK.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: tutorial
author: cforbe
ms.author: cforbe
ms.reviewer: trbye
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: d20ff1fabfb73c899153cf42bb6f2d7a8f233e21
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/12/2018
ms.locfileid: "53314690"
---
# <a name="tutorial-prepare-data-for-regression-modeling"></a>Samouczek: Przygotowywanie danych do modelowania regresji

Z tego samouczka dowiesz się, jak przygotować dane do modelowania regresji z użyciem zestawu Azure Machine Learning Data Prep SDK. Wykonaj różne przekształcenia, aby przefiltrować i połączyć dwa różne zestawy danych dotyczących nowojorskich taksówek. Końcowym celem tego zestawu samouczków jest przewidywanie kosztów podróży taksówką przez wytrenowanie modelu na cechach danych, takich jak godzina rozpoczęcia podróży, dzień tygodnia, liczba pasażerów i współrzędne. Ten samouczek jest pierwszą częścią dwuczęściowej serii.

W tym samouczku zostaną wykonane następujące czynności:

> [!div class="checklist"]
> * Konfigurowanie środowiska Python i importowania pakietów
> * Ładowanie dwóch zestawów danych z różnymi nazwami pól
> * Oczyszczanie danych w celu usunięcia anomalii
> * Przekształcanie danych za pomocą inteligentnych przekształceń w celu utworzenia nowych cech
> * Zapisywanie obiektu przepływu danych do użycia w modelu regresji

Swoje dane możesz przygotować w języku Python, używając zestawu [Azure Machine Learning Data Prep SDK](https://aka.ms/data-prep-sdk).

## <a name="get-the-notebook"></a>Pobieranie notesu

Dla Twojej wygody ten samouczek jest dostępny jako [notes Jupyter](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/regression-part1-data-prep.ipynb). Uruchom notes `regression-part1-data-prep.ipynb` w usłudze Azure Notebooks lub na własnym serwerze notesów Jupyter.

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-in-azure-notebook.md)]

## <a name="import-packages"></a>Importowanie pakietów

Zacznij od zaimportowania zestawu SDK.


```python
import azureml.dataprep as dprep
```

## <a name="load-data"></a>Ładowanie danych

Pobierz dwa różne zestawy danych dotyczących nowojorskich taksówek do obiektów przepływu danych.  Te zestawy danych zawierają nieco inne pola. Metoda `auto_read_file()` automatycznie rozpoznaje typ pliku wejściowego.


```python
dataset_root = "https://dprepdata.blob.core.windows.net/demo"

green_path = "/".join([dataset_root, "green-small/*"])
yellow_path = "/".join([dataset_root, "yellow-small/*"])

green_df = dprep.read_csv(path=green_path, header=dprep.PromoteHeadersMode.GROUPED)
# auto_read_file will automatically identify and parse the file type, and is useful if you don't know the file type
yellow_df = dprep.auto_read_file(path=yellow_path)

display(green_df.head(5))
display(yellow_df.head(5))
```

## <a name="cleanse-data"></a>Oczyszczanie danych

Teraz możesz wypełnić niektóre zmienne przy użyciu skrótowych przekształceń, które będą stosowane do wszystkich przepływów danych. Zmienna `drop_if_all_null` będzie służyć do usuwania rekordów, w których wszystkie pola mają wartość null. Zmienna `useful_columns` przechowuje tablicę z opisami kolumn, które są zachowywane w każdym przepływie danych.

```python
all_columns = dprep.ColumnSelector(term=".*", use_regex=True)
drop_if_all_null = [all_columns, dprep.ColumnRelationship(dprep.ColumnRelationship.ALL)]
useful_columns = [
    "cost", "distance", "dropoff_datetime", "dropoff_latitude", "dropoff_longitude",
    "passengers", "pickup_datetime", "pickup_latitude", "pickup_longitude", "store_forward", "vendor"
]
```

Najpierw będziesz pracować z danymi zielonych taksówek i nadasz im kształt, który pozwoli na połączenie ich z danymi żółtych taksówek. Utwórz tymczasowy przepływ danych `tmp_df`. Wywołaj funkcje `replace_na()`, `drop_nulls()`, i `keep_columns()`, używając utworzonych przez siebie zmiennych skrótowego przekształcenia. Dodatkowo zmień nazwy wszystkich kolumn w ramce danych, aby pasowały do nazw w zmiennej `useful_columns`.


```python
tmp_df = (green_df
    .replace_na(columns=all_columns)
    .drop_nulls(*drop_if_all_null)
    .rename_columns(column_pairs={
        "VendorID": "vendor",
        "lpep_pickup_datetime": "pickup_datetime",
        "Lpep_dropoff_datetime": "dropoff_datetime",
        "lpep_dropoff_datetime": "dropoff_datetime",
        "Store_and_fwd_flag": "store_forward",
        "store_and_fwd_flag": "store_forward",
        "Pickup_longitude": "pickup_longitude",
        "Pickup_latitude": "pickup_latitude",
        "Dropoff_longitude": "dropoff_longitude",
        "Dropoff_latitude": "dropoff_latitude",
        "Passenger_count": "passengers",
        "Fare_amount": "cost",
        "Trip_distance": "distance"
     })
    .keep_columns(columns=useful_columns))
tmp_df.head(5)
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
      <th>vendor</th>
      <th>pickup_datetime</th>
      <th>dropoff_datetime</th>
      <th>store_forward</th>
      <th>pickup_longitude</th>
      <th>pickup_latitude</th>
      <th>dropoff_longitude</th>
      <th>dropoff_latitude</th>
      <th>passengers</th>
      <th>odległość</th>
      <th>cost</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2</td>
      <td>2013-08-01 08:14:37</td>
      <td>2013-08-01 09:09:06</td>
      <td>Nie</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>,00</td>
      <td>21.25</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>2013-08-01 09:13:00</td>
      <td>2013-08-01 11:38:00</td>
      <td>Nie</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>2</td>
      <td>,00</td>
      <td>74.5</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2</td>
      <td>2013-08-01 09:48:00</td>
      <td>2013-08-01 09:49:00</td>
      <td>Nie</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>,00</td>
      <td>1</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2</td>
      <td>2013-08-01 10:38:35</td>
      <td>2013-08-01 10:38:51</td>
      <td>Nie</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>,00</td>
      <td>3.25</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2</td>
      <td>2013-08-01 11:51:45</td>
      <td>2013-08-01 12:03:52</td>
      <td>Nie</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>,00</td>
      <td>8.5</td>
    </tr>
  </tbody>
</table>
</div>

Zastąp zmienną `green_df` przekształceniem wykonanym na elemencie `tmp_df` w poprzednim kroku.

```python
green_df = tmp_df
```

Wykonaj te same kroki przekształcania danych względem danych żółtych taksówek.


```python
tmp_df = (yellow_df
    .replace_na(columns=all_columns)
    .drop_nulls(*drop_if_all_null)
    .rename_columns(column_pairs={
        "vendor_name": "vendor",
        "VendorID": "vendor",
        "vendor_id": "vendor",
        "Trip_Pickup_DateTime": "pickup_datetime",
        "tpep_pickup_datetime": "pickup_datetime",
        "Trip_Dropoff_DateTime": "dropoff_datetime",
        "tpep_dropoff_datetime": "dropoff_datetime",
        "store_and_forward": "store_forward",
        "store_and_fwd_flag": "store_forward",
        "Start_Lon": "pickup_longitude",
        "Start_Lat": "pickup_latitude",
        "End_Lon": "dropoff_longitude",
        "End_Lat": "dropoff_latitude",
        "Passenger_Count": "passengers",
        "passenger_count": "passengers",
        "Fare_Amt": "cost",
        "fare_amount": "cost",
        "Trip_Distance": "distance",
        "trip_distance": "distance"
    })
    .keep_columns(columns=useful_columns))
tmp_df.head(5)
```

Ponownie zastąp zmienną `yellow_df` za pomocą zmiennej `tmp_df`, a następnie wywołaj funkcję `append_rows()` dla danych zielonych taksówek, aby dołączyć dane żółtych taksówek i utworzyć w ten sposób nową, połączoną ramkę danych.


```python
yellow_df = tmp_df
combined_df = green_df.append_rows([yellow_df])
```

### <a name="convert-types-and-filter"></a>Konwersja typów i filtrowanie 

Przyjrzyj się zbiorczym statystykom współrzędnych rozpoczęcia i zakończenia podróży, aby zobaczyć rozkład danych. Najpierw zdefiniuj obiekt `TypeConverter`, aby zmienić pola długości i szerokości geograficznej na typ dziesiętny. Następnie wywołaj funkcję `keep_columns()`, aby ograniczyć dane wyjściowe tylko do pól długości i szerokości geograficznej, po czym wywołaj funkcję `get_profile()`.


```python
decimal_type = dprep.TypeConverter(data_type=dprep.FieldType.DECIMAL)
combined_df = combined_df.set_column_types(type_conversions={
    "pickup_longitude": decimal_type,
    "pickup_latitude": decimal_type,
    "dropoff_longitude": decimal_type,
    "dropoff_latitude": decimal_type
})
combined_df.keep_columns(columns=[
    "pickup_longitude", "pickup_latitude", 
    "dropoff_longitude", "dropoff_latitude"
]).get_profile()
```




<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Typ</th>
      <th>Min.</th>
      <th>Maks.</th>
      <th>Liczba</th>
      <th>Liczba brakujących</th>
      <th>Liczba niebrakujących</th>
      <th>Odsetek brakujących</th>
      <th>Liczba błędów</th>
      <th>Liczba pustych</th>
      <th>Kwantyl 0,1%</th>
      <th>Kwantyl 1%</th>
      <th>Kwantyl 5%</th>
      <th>Kwantyl 25%</th>
      <th>Kwantyl 50%</th>
      <th>Kwantyl 75%</th>
      <th>Kwantyl 95%</th>
      <th>Kwantyl 99%</th>
      <th>Kwantyl 99,9%</th>
      <th>Odchylenie standardowe</th>
      <th>Średnia</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>pickup_longitude</th>
      <td>FieldType.DECIMAL</td>
      <td>-115.179337</td>
      <td>0.000000</td>
      <td>7722.0</td>
      <td>0.0</td>
      <td>7722.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>-88.114046</td>
      <td>-73.961840</td>
      <td>-73.961964</td>
      <td>-73.947693</td>
      <td>-73.922097</td>
      <td>-73.846670</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>18.792672</td>
      <td>-68.833579</td>
    </tr>
    <tr>
      <th>pickup_latitude</th>
      <td>FieldType.DECIMAL</td>
      <td>0.000000</td>
      <td>40.919121</td>
      <td>7722.0</td>
      <td>0.0</td>
      <td>7722.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>40.682889</td>
      <td>40.675541</td>
      <td>40.721075</td>
      <td>40.756159</td>
      <td>40.803909</td>
      <td>40.849406</td>
      <td>40.870681</td>
      <td>40.891244</td>
      <td>10.345967</td>
      <td>37.936742</td>
    </tr>
    <tr>
      <th>dropoff_longitude</th>
      <td>FieldType.DECIMAL</td>
      <td>-115.179337</td>
      <td>0.000000</td>
      <td>7722.0</td>
      <td>0.0</td>
      <td>7722.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>-87.699611</td>
      <td>-73.984734</td>
      <td>-73.985777</td>
      <td>-73.956250</td>
      <td>-73.928948</td>
      <td>-73.866208</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>18.696526</td>
      <td>-68.896978</td>
    </tr>
    <tr>
      <th>dropoff_latitude</th>
      <td>FieldType.DECIMAL</td>
      <td>0.000000</td>
      <td>41.008934</td>
      <td>7722.0</td>
      <td>0.0</td>
      <td>7722.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>40.662763</td>
      <td>40.654851</td>
      <td>40.717821</td>
      <td>40.756534</td>
      <td>40.784688</td>
      <td>40.852437</td>
      <td>40.879289</td>
      <td>40.937291</td>
      <td>10.290780</td>
      <td>37.963774</td>
    </tr>
  </tbody>
</table>



Z danych wyjściowych zbiorczych statystyk wynika, że istnieją współrzędne, których brakuje, oraz współrzędne, które są poza Nowym Jorkiem. Odfiltruj współrzędne znajdujące się poza granicami miasta, łącząc polecenia filtrowania kolumn w funkcji `filter()` oraz definiując minimalną i maksymalną granicę dla każdego pola. Następnie ponownie wywołaj funkcję `get_profile()` w celu zweryfikowania przekształcenia.


```python
tmp_df = (combined_df
    .drop_nulls(
        columns=["pickup_longitude", "pickup_latitude", "dropoff_longitude", "dropoff_latitude"],
        column_relationship=dprep.ColumnRelationship(dprep.ColumnRelationship.ANY)
    ) 
    .filter(dprep.f_and(
        dprep.col("pickup_longitude") <= -73.72,
        dprep.col("pickup_longitude") >= -74.09,
        dprep.col("pickup_latitude") <= 40.88,
        dprep.col("pickup_latitude") >= 40.53,
        dprep.col("dropoff_longitude") <= -73.72,
        dprep.col("dropoff_longitude") >= -74.09,
        dprep.col("dropoff_latitude") <= 40.88,
        dprep.col("dropoff_latitude") >= 40.53
    )))
tmp_df.keep_columns(columns=[
    "pickup_longitude", "pickup_latitude", 
    "dropoff_longitude", "dropoff_latitude"
]).get_profile()
```




<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Typ</th>
      <th>Min.</th>
      <th>Maks.</th>
      <th>Liczba</th>
      <th>Liczba brakujących</th>
      <th>Liczba niebrakujących</th>
      <th>Odsetek brakujących</th>
      <th>Liczba błędów</th>
      <th>Liczba pustych</th>
      <th>Kwantyl 0,1%</th>
      <th>Kwantyl 1%</th>
      <th>Kwantyl 5%</th>
      <th>Kwantyl 25%</th>
      <th>Kwantyl 50%</th>
      <th>Kwantyl 75%</th>
      <th>Kwantyl 95%</th>
      <th>Kwantyl 99%</th>
      <th>Kwantyl 99,9%</th>
      <th>Odchylenie standardowe</th>
      <th>Średnia</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>pickup_longitude</th>
      <td>FieldType.DECIMAL</td>
      <td>-74.078156</td>
      <td>-73.736481</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>-74.076314</td>
      <td>-73.962542</td>
      <td>-73.962893</td>
      <td>-73.948975</td>
      <td>-73.927856</td>
      <td>-73.866662</td>
      <td>-73.830438</td>
      <td>-73.823160</td>
      <td>-73.769750</td>
      <td>0.048711</td>
      <td>-73.913865</td>
    </tr>
    <tr>
      <th>pickup_latitude</th>
      <td>FieldType.DECIMAL</td>
      <td>40.575485</td>
      <td>40.879852</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>40.632884</td>
      <td>40.713105</td>
      <td>40.711600</td>
      <td>40.721403</td>
      <td>40.758142</td>
      <td>40.805145</td>
      <td>40.848855</td>
      <td>40.867567</td>
      <td>40.877690</td>
      <td>0.048348</td>
      <td>40.765226</td>
    </tr>
    <tr>
      <th>dropoff_longitude</th>
      <td>FieldType.DECIMAL</td>
      <td>-74.085747</td>
      <td>-73.720871</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>-74.078828</td>
      <td>-73.985650</td>
      <td>-73.985813</td>
      <td>-73.959041</td>
      <td>-73.936681</td>
      <td>-73.884846</td>
      <td>-73.815507</td>
      <td>-73.776697</td>
      <td>-73.733471</td>
      <td>0.055961</td>
      <td>-73.920718</td>
    </tr>
    <tr>
      <th>dropoff_latitude</th>
      <td>FieldType.DECIMAL</td>
      <td>40.583530</td>
      <td>40.879734</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>40.597741</td>
      <td>40.695376</td>
      <td>40.695115</td>
      <td>40.727549</td>
      <td>40.758160</td>
      <td>40.788378</td>
      <td>40.850372</td>
      <td>40.867968</td>
      <td>40.878586</td>
      <td>0.050462</td>
      <td>40.759487</td>
    </tr>
  </tbody>
</table>



Zastąp element `combined_df` przekształceniem wykonanym względem elementu `tmp_df`.


```python
combined_df = tmp_df
```

### <a name="split-and-rename-columns"></a>Dzielenie kolumn i zmienianie ich nazw

Spójrz na profil danych dla kolumny `store_forward`.


```python
combined_df.keep_columns(columns='store_forward').get_profile()
```




<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Typ</th>
      <th>Min.</th>
      <th>Maks.</th>
      <th>Liczba</th>
      <th>Liczba brakujących</th>
      <th>Liczba niebrakujących</th>
      <th>Odsetek brakujących</th>
      <th>Liczba błędów</th>
      <th>Liczba pustych</th>
      <th>Kwantyl 0,1%</th>
      <th>Kwantyl 1%</th>
      <th>Kwantyl 5%</th>
      <th>Kwantyl 25%</th>
      <th>Kwantyl 50%</th>
      <th>Kwantyl 75%</th>
      <th>Kwantyl 95%</th>
      <th>Kwantyl 99%</th>
      <th>Kwantyl 99,9%</th>
      <th>Odchylenie standardowe</th>
      <th>Średnia</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>store_forward</th>
      <td>FieldType.STRING</td>
      <td>Nie</td>
      <td>Tak</td>
      <td>7059.0</td>
      <td>99.0</td>
      <td>6960.0</td>
      <td>0.014025</td>
      <td>0.0</td>
      <td>0.0</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
  </tbody>
</table>



W danych wyjściowych profilu danych elementu `store_forward` widać, że dane są niespójne, niektórych wartości brakuje, a niektóre są równe null. Zastąp te wartości przy użyciu funkcji `replace()` i `fill_nulls()`, w obu przypadkach zmieniając je na ciąg „N”.


```python
combined_df = combined_df.replace(columns="store_forward", find="0", replace_with="N").fill_nulls("store_forward", "N")
```

Wykonaj inną funkcję `replace`, tym razem względem pola `distance`. Spowoduje to ponowne sformatowanie wartości odległości, które zostały niepoprawnie oznaczone jako `.00`, oraz wypełnienie wszystkich wartości null zerami. Przekonwertuj pole `distance` na format numeryczny.


```python
combined_df = combined_df.replace(columns="distance", find=".00", replace_with=0).fill_nulls("distance", 0)
combined_df = combined_df.to_number(["distance"])
```

Podziel czasy rozpoczęcia i zakończenia podróży na odpowiednie kolumny daty i godziny. Użyj funkcji `split_column_by_example()`, aby przeprowadzić podział. W tym przypadku opcjonalny parametr `example` funkcji `split_column_by_example()` jest pomijany. W związku z tym funkcja automatycznie określi, gdzie dokonać podziału, bazując na danych.


```python
tmp_df = (combined_df
    .split_column_by_example(source_column="pickup_datetime")
    .split_column_by_example(source_column="dropoff_datetime"))
tmp_df.head(5)
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
      <th>vendor</th>
      <th>pickup_datetime</th>
      <th>pickup_datetime_1</th>
      <th>pickup_datetime_2</th>
      <th>dropoff_datetime</th>
      <th>dropoff_datetime_1</th>
      <th>dropoff_datetime_2</th>
      <th>store_forward</th>
      <th>pickup_longitude</th>
      <th>pickup_latitude</th>
      <th>dropoff_longitude</th>
      <th>dropoff_latitude</th>
      <th>passengers</th>
      <th>odległość</th>
      <th>cost</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2</td>
      <td>2013-08-01 17:22:00</td>
      <td>2013-08-01</td>
      <td>17:22:00</td>
      <td>2013-08-01 17:22:00</td>
      <td>2013-08-01</td>
      <td>17:22:00</td>
      <td>Nie</td>
      <td>-73.937767</td>
      <td>40.758480</td>
      <td>-73.937767</td>
      <td>40.758480</td>
      <td>1</td>
      <td>0.0</td>
      <td>2.5</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>2013-08-01 17:24:00</td>
      <td>2013-08-01</td>
      <td>17:24:00</td>
      <td>2013-08-01 17:25:00</td>
      <td>2013-08-01</td>
      <td>17:25:00</td>
      <td>Nie</td>
      <td>-73.937927</td>
      <td>40.757843</td>
      <td>-73.937927</td>
      <td>40.757843</td>
      <td>1</td>
      <td>0.0</td>
      <td>2.5</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2</td>
      <td>2013-08-06 06:51:19</td>
      <td>2013-08-06</td>
      <td>06:51:19</td>
      <td>2013-08-06 06:51:36</td>
      <td>2013-08-06</td>
      <td>06:51:36</td>
      <td>Nie</td>
      <td>-73.937721</td>
      <td>40.758404</td>
      <td>-73.937721</td>
      <td>40.758369</td>
      <td>1</td>
      <td>0.0</td>
      <td>3.3</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2</td>
      <td>2013-08-06 13:26:34</td>
      <td>2013-08-06</td>
      <td>13:26:34</td>
      <td>2013-08-06 13:26:57</td>
      <td>2013-08-06</td>
      <td>13:26:57</td>
      <td>Nie</td>
      <td>-73.937691</td>
      <td>40.758419</td>
      <td>-73.937790</td>
      <td>40.758358</td>
      <td>1</td>
      <td>0.0</td>
      <td>3.3</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2</td>
      <td>2013-08-06 13:27:53</td>
      <td>2013-08-06</td>
      <td>13:27:53</td>
      <td>2013-08-06 13:28:08</td>
      <td>2013-08-06</td>
      <td>13:28:08</td>
      <td>Nie</td>
      <td>-73.937805</td>
      <td>40.758396</td>
      <td>-73.937775</td>
      <td>40.758450</td>
      <td>1</td>
      <td>0.0</td>
      <td>3.3</td>
    </tr>
  </tbody>
</table>
</div>


Zmień nazwy kolumn wygenerowanych przez funkcję `split_column_by_example()` na opisowe.


```python
tmp_df_renamed = (tmp_df
    .rename_columns(column_pairs={
        "pickup_datetime_1": "pickup_date",
        "pickup_datetime_2": "pickup_time",
        "dropoff_datetime_1": "dropoff_date",
        "dropoff_datetime_2": "dropoff_time"
    }))
tmp_df_renamed.head(5)
```

Zastąp element `combined_df` wykonanym przekształceniem, a następnie wywołaj funkcję `get_profile()`, aby zobaczyć pełną zbiorczą statystykę po wszystkich przekształceniach.


```python
combined_df = tmp_df_renamed
combined_df.get_profile()
```

## <a name="transform-data"></a>Przekształcanie danych

Jeszcze bardziej podziel datę rozpoczęcia i zakończenia podróży na dzień tygodnia, dzień miesiąca i miesiąc. Aby uzyskać dzień tygodnia, użyj funkcji `derive_column_by_example()`. Ta funkcja przyjmuje jako parametr tablicę przykładowych obiektów definiujących dane wejściowe i żądane dane wyjściowe. Następnie automatycznie określa żądane przekształcenie. Kolumny z godzinami rozpoczęcia i zakończenia podróży podziel na godzinę, minuty i sekundy, używając funkcji `split_column_by_example()` bez parametru przykładu.

Po wygenerowaniu tych nowych cech usuń pierwotne pola na rzecz nowo wygenerowanych cech, używając funkcji `drop_columns()`. Zmień nazwy wszystkich pozostałych pól na właściwe opisy.


```python
tmp_df = (combined_df
    .derive_column_by_example(
        source_columns="pickup_date", 
        new_column_name="pickup_weekday", 
        example_data=[("2009-01-04", "Sunday"), ("2013-08-22", "Thursday")]
    )
    .derive_column_by_example(
        source_columns="dropoff_date",
        new_column_name="dropoff_weekday",
        example_data=[("2013-08-22", "Thursday"), ("2013-11-03", "Sunday")]
    )
          
    .split_column_by_example(source_column="pickup_time")
    .split_column_by_example(source_column="dropoff_time")
    # the following two split_column_by_example calls reference the generated column names from the above two calls
    .split_column_by_example(source_column="pickup_time_1")
    .split_column_by_example(source_column="dropoff_time_1")
    .drop_columns(columns=[
        "pickup_date", "pickup_time", "dropoff_date", "dropoff_time", 
        "pickup_date_1", "dropoff_date_1", "pickup_time_1", "dropoff_time_1"
    ])
          
    .rename_columns(column_pairs={
        "pickup_date_2": "pickup_month",
        "pickup_date_3": "pickup_monthday",
        "pickup_time_1_1": "pickup_hour",
        "pickup_time_1_2": "pickup_minute",
        "pickup_time_2": "pickup_second",
        "dropoff_date_2": "dropoff_month",
        "dropoff_date_3": "dropoff_monthday",
        "dropoff_time_1_1": "dropoff_hour",
        "dropoff_time_1_2": "dropoff_minute",
        "dropoff_time_2": "dropoff_second"
    }))

tmp_df.head(5)
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
      <th>vendor</th>
      <th>pickup_datetime</th>
      <th>pickup_weekday</th>
      <th>pickup_hour</th>
      <th>pickup_minute</th>
      <th>pickup_second</th>
      <th>dropoff_datetime</th>
      <th>dropoff_weekday</th>
      <th>dropoff_hour</th>
      <th>dropoff_minute</th>
      <th>dropoff_second</th>
      <th>store_forward</th>
      <th>pickup_longitude</th>
      <th>pickup_latitude</th>
      <th>dropoff_longitude</th>
      <th>dropoff_latitude</th>
      <th>passengers</th>
      <th>odległość</th>
      <th>cost</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2</td>
      <td>2013-08-01 17:22:00</td>
      <td>Thursday</td>
      <td>17</td>
      <td>22</td>
      <td>00</td>
      <td>2013-08-01 17:22:00</td>
      <td>Thursday</td>
      <td>17</td>
      <td>22</td>
      <td>00</td>
      <td>Nie</td>
      <td>-73.937767</td>
      <td>40.758480</td>
      <td>-73.937767</td>
      <td>40.758480</td>
      <td>1</td>
      <td>0.0</td>
      <td>2.5</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>2013-08-01 17:24:00</td>
      <td>Thursday</td>
      <td>17</td>
      <td>24</td>
      <td>00</td>
      <td>2013-08-01 17:25:00</td>
      <td>Thursday</td>
      <td>17</td>
      <td>25</td>
      <td>00</td>
      <td>Nie</td>
      <td>-73.937927</td>
      <td>40.757843</td>
      <td>-73.937927</td>
      <td>40.757843</td>
      <td>1</td>
      <td>0.0</td>
      <td>2.5</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2</td>
      <td>2013-08-06 06:51:19</td>
      <td>Tuesday</td>
      <td>06</td>
      <td>51</td>
      <td>19</td>
      <td>2013-08-06 06:51:36</td>
      <td>Tuesday</td>
      <td>06</td>
      <td>51</td>
      <td>36</td>
      <td>Nie</td>
      <td>-73.937721</td>
      <td>40.758404</td>
      <td>-73.937721</td>
      <td>40.758369</td>
      <td>1</td>
      <td>0.0</td>
      <td>3.3</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2</td>
      <td>2013-08-06 13:26:34</td>
      <td>Tuesday</td>
      <td>13</td>
      <td>26</td>
      <td>34</td>
      <td>2013-08-06 13:26:57</td>
      <td>Tuesday</td>
      <td>13</td>
      <td>26</td>
      <td>57</td>
      <td>Nie</td>
      <td>-73.937691</td>
      <td>40.758419</td>
      <td>-73.937790</td>
      <td>40.758358</td>
      <td>1</td>
      <td>0.0</td>
      <td>3.3</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2</td>
      <td>2013-08-06 13:27:53</td>
      <td>Tuesday</td>
      <td>13</td>
      <td>27</td>
      <td>53</td>
      <td>2013-08-06 13:28:08</td>
      <td>Tuesday</td>
      <td>13</td>
      <td>28</td>
      <td>08</td>
      <td>Nie</td>
      <td>-73.937805</td>
      <td>40.758396</td>
      <td>-73.937775</td>
      <td>40.758450</td>
      <td>1</td>
      <td>0.0</td>
      <td>3.3</td>
    </tr>
  </tbody>
</table>
</div>

W powyższych danych widać, że składniki dat i godzin rozpoczęcia oraz zakończenia podróży wygenerowane przez wyprowadzone przekształcenia są prawidłowe. Usuń kolumny `pickup_datetime` i `dropoff_datetime`, ponieważ nie są już potrzebne.


```python
tmp_df = tmp_df.drop_columns(columns=["pickup_datetime", "dropoff_datetime"])
```

Użyj funkcji wnioskowania o typie, aby automatycznie sprawdzić typ danych w każdym z pól i wyświetlić wyniki wnioskowania.


```python
type_infer = tmp_df.builders.set_column_types()
type_infer.learn()
type_infer
```

    Column types conversion candidates:
    'pickup_weekday': [FieldType.STRING],
    'pickup_hour': [FieldType.DECIMAL],
    'pickup_minute': [FieldType.DECIMAL],
    'pickup_second': [FieldType.DECIMAL],
    'dropoff_hour': [FieldType.DECIMAL],
    'dropoff_minute': [FieldType.DECIMAL],
    'dropoff_second': [FieldType.DECIMAL],
    'store_forward': [FieldType.STRING],
    'pickup_longitude': [FieldType.DECIMAL],
    'dropoff_longitude': [FieldType.DECIMAL],
    'passengers': [FieldType.DECIMAL],
    'distance': [FieldType.DECIMAL],
    'vendor': [FieldType.STRING],
    'dropoff_weekday': [FieldType.STRING],
    'pickup_latitude': [FieldType.DECIMAL],
    'dropoff_latitude': [FieldType.DECIMAL],
    'cost': [FieldType.DECIMAL]

Bazując na danych, wyniki wnioskowania wyglądają poprawnie. Zastosuj teraz konwersje typów do przepływu danych.


```python
tmp_df = type_infer.to_dataflow()
tmp_df.get_profile()
```

Przed spakowaniem przepływu danych użyj dwóch ostatnich filtrów w zestawie danych. Aby wyeliminować nieprawidłowe punkty danych, odfiltruj przepływ danych w rekordach, w których obydwie wartości, `cost` i `distance`, są większe od zera.

```python
tmp_df = tmp_df.filter(dprep.col("distance") > 0)
tmp_df = tmp_df.filter(dprep.col("cost") > 0)
```

W tym momencie masz w pełni przekształcony i przygotowany obiekt przepływu danych, który można zastosować w modelu uczenia maszynowego. Zestaw SDK zawiera funkcję serializacji obiektów, która jest używana w następujący sposób.

```python
import os
file_path = os.path.join(os.getcwd(), "dflows.dprep")

dflow_prepared = tmp_df
package = dprep.Package([dflow_prepared])
package.save(file_path)
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Usuń plik `dflows.dprep` (bez względu na to, czy pracujesz lokalnie, czy w usłudze Azure Notebooks) ze swojego bieżącego katalogu, jeśli nie zamierzasz przechodzić drugiej części samouczka. Jeśli masz zamiar przejść drugą część, będziesz potrzebować pliku `dflows.dprep` w bieżącym katalogu.

## <a name="next-steps"></a>Następne kroki

W części pierwszej części tego samouczka były wykonywane następujące czynności:

> [!div class="checklist"]
> * Konfigurowanie środowiska projektowego
> * Ładowanie i czyszczenie zestawów danych
> * Używanie inteligentnych przekształceń do przewidywania logiki na podstawie przykładu
> * Scalanie zestawów danych i tworzenie z nich pakietów na potrzeby trenowania w uczeniu maszynowym

Możesz już użyć tych danych treningowych w następnej części serii samouczków:

> [!div class="nextstepaction"]
> [Samouczek 2: trenowanie modelu regresji](tutorial-auto-train-models.md)
