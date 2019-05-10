---
title: 'Przekształcenia: przeznaczonego do przygotowania danych zestawu SDK języka Python'
titleSuffix: Azure Machine Learning service
description: Więcej informacji na temat przekształcania i czyszczenie danych przy użyciu zestawu SDK usługi Azure Machine Learning danych przedprodukcyjnym. Dodawanie kolumn odfiltrować zbędne wiersze lub kolumny i przypisują brakujące wartości, należy użyć metod transformacji.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: jmartens
ms.date: 05/02/2019
ms.custom: seodec18
ms.openlocfilehash: db23c8af7eaa4a86691ccb0bb831ce2cc28d635c
ms.sourcegitcommit: 399db0671f58c879c1a729230254f12bc4ebff59
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/09/2019
ms.locfileid: "65471831"
---
# <a name="transform-data-with-the-azure-machine-learning-data-prep-sdk"></a>Przekształcanie danych za pomocą usługi Azure Machine Learning Prep zestawu SDK usługi Data

W tym artykule dowiesz się, przekształcanie danych przy użyciu różnych metod `azureml-dataprep` pakietu. Pakiet oferuje funkcje, które ułatwiają dodawanie kolumn odfiltrować zbędne wiersze lub kolumny i przypisują brakujące wartości. Zobacz pełna dokumentacja dotycząca [pakiet przygotowania danych usługi Azure ml](https://aka.ms/data-prep-sdk).

> [!Important]
> Jeśli tworzysz nowe rozwiązanie, spróbuj [zestawów danych Learning maszyny Azure](how-to-explore-prepare-data.md) (wersja zapoznawcza), Przekształć swoje dane, dane migawki w celu przechowywania definicji określonej wersji zestawu danych. Zestawy danych to następna wersja przygotowywanie danych zestawu SDK, oferując rozszerzone funkcje do zarządzania zestawami danych w rozwiązań sztucznej Inteligencji. Jeśli używasz `azureml-dataprep` pakietu do utworzenia przepływu danych przy użyciu przekształceń zamiast `azureml-datasets` pakietu, aby utworzyć zestaw danych, nie będzie można później użyć migawki lub numerów wersji zestawów danych.

Niniejszy instruktaż zawiera przykłady dla następujących zadań:

- Dodawanie kolumny za pomocą wyrażenia
- [Naliczenie brakujące wartości](#impute-missing-values)
- [Utwórz kolumnę pochodną według przykładu](#derive-column-by-example)
- [Filtrowanie](#filtering)
- [Niestandardowe przekształcenia języka Python](#custom-python-transforms)

## <a name="add-column-using-an-expression"></a>Dodawanie kolumny za pomocą wyrażenia

Zestaw SDK Azure Machine Learning danych Prep zawiera `substring` wyrażeń, można użyć do obliczenia wartości z istniejących kolumn, a następnie umieść tej wartości w nowej kolumnie. W tym przykładzie, Załaduj dane i spróbuj dodać kolumny do tych danych wejściowych.

```Python
import azureml.dataprep as dprep

# loading data
dflow = dprep.read_csv(path=r'data\crime0-10.csv')
dflow.head(3)
```

||ID|Liczba przypadków|Date|Blokuj|IUCR|Typ podstawowy|Opis|Opis lokalizacji|Aresztowania|Krajowych|Przyciski ...|Lej|Obszar społeczności|FBI kodu|Współrzędna x|Współrzędna Y|Rok|Aktualizacja:|Szerokość geograficzna|Długość geograficzna|Lokalizacja|
|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|
|0|10140490|HY329907|2015-07-05 11:50:00 PM|ZAPISZ NEWLAND N 050XX|0820|PRZED KRADZIEŻĄ|500 USD I W OBSZARZE|ULICA|false|false|Przyciski ...|41|10|06|1129230|1933315|2015|2015-07-12 12:42:46: 00|41.973309466|-87.800174996|(41.973309466,-87.800174996)|
|1|10139776|HY329265|2015-07-05 11:30:00 PM|ZAPISZ MORSE'A W 011XX|0460|BATERIA|PROSTE|ULICA|false|true|Przyciski ...|49|1|08B|1167370|1946271|2015|2015-07-12 12:42:46: 00|42.008124017|-87.65955018|(42.008124017,-87.65955018)|
|2|10140270|HY329253|2015-07-05 11:20:00 PM|Z PRZODU APISZ 121XX|0486|BATERIA|PROSTE ŚWIATOWEGO BATERII|ULICA|false|true|Przyciski ...|9|53|08B|||2015|2015-07-12 12:42:46: 00|


Użyj `substring(start, length)` wyrażenie, aby wyodrębnić prefiks z kolumny liczba przypadków i umieścić ciąg w nowej kolumnie `Case Category`. Przekazywanie `substring_expression` zmienną `expression` parametr tworzy nową kolumnę obliczeniową, która wykonuje wyrażenia dla każdego rekordu.

```Python
substring_expression = dprep.col('Case Number').substring(0, 2)
case_category = dflow.add_column(new_column_name='Case Category',
                                    prior_column='Case Number',
                                    expression=substring_expression)
case_category.head(3)
```

||ID|Liczba przypadków|Kategoria przypadku|Date|Blokuj|IUCR|Typ podstawowy|Opis|Opis lokalizacji|Aresztowania|Krajowych|Przyciski ...|Lej|Obszar społeczności|FBI kodu|Współrzędna x|Współrzędna Y|Rok|Aktualizacja:|Szerokość geograficzna|Długość geograficzna|Lokalizacja|
|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|------|
|0|10140490|HY329907|AZJATYCKA|2015-07-05 11:50:00 PM|ZAPISZ NEWLAND N 050XX|0820|PRZED KRADZIEŻĄ|500 USD I W OBSZARZE|ULICA|false|false|Przyciski ...|41|10|06|1129230|1933315|2015|2015-07-12 12:42:46: 00|41.973309466|-87.800174996|(41.973309466,-87.800174996)|
|1|10139776|HY329265|AZJATYCKA|2015-07-05 11:30:00 PM|ZAPISZ MORSE'A W 011XX|0460|BATERIA|PROSTE|ULICA|false|true|Przyciski ...|49|1|08B|1167370|1946271|2015|2015-07-12 12:42:46: 00|42.008124017|-87.65955018|(42.008124017,-87.65955018)|
|2|10140270|HY329253|AZJATYCKA|2015-07-05 11:20:00 PM|Z PRZODU APISZ 121XX|0486|BATERIA|PROSTE ŚWIATOWEGO BATERII|ULICA|false|true|Przyciski ...|9|53|08B|||2015|2015-07-12 12:42:46: 00|


Użyj `substring(start)` wyrażenia do wyodrębniania tylko z kolumny liczba przypadków i Utwórz nową kolumnę. Przekonwertuj go na typ danych liczbowych przy użyciu `to_number()` działać, a następnie przekaż nazwę kolumny ciągu jako parametr.

```Python
substring_expression2 = dprep.col('Case Number').substring(2)
case_id = dflow.add_column(new_column_name='Case Id',
                              prior_column='Case Number',
                              expression=substring_expression2)
case_id = case_id.to_number('Case Id')
```

## <a name="impute-missing-values"></a>Naliczenie brakujące wartości

Zestaw SDK może przypisują brakujących wartości w określonych kolumnach. W tym przykładzie obciążenia wartości długości i szerokości geograficznej, a następnie spróbuj przypisują brakujących wartości w danych wejściowych.

```python
import azureml.dataprep as dprep

# loading input data
dflow = dprep.read_csv(r'data\crime0-10.csv')
dflow = dflow.keep_columns(['ID', 'Arrest', 'Latitude', 'Longitude'])
dflow = dflow.to_number(['Latitude', 'Longitude'])
dflow.head(3)
```

||ID|Aresztowania|Szerokość geograficzna|Długość geograficzna|
|-----|------|-----|------|-----|
|0|10140490|false|41.973309|-87.800175|
|1|10139776|false|42.008124|-87.659550|
|2|10140270|false|NaN|NaN|

Trzeci rekordzie brakuje wartości długości i szerokości geograficznej. Naliczenie te brakujące wartości, należy użyć [ `ImputeMissingValuesBuilder` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.api.builders.imputemissingvaluesbuilder?view=azure-dataprep-py) się wyrażeniem stałym. Jego przypisują kolumny albo obliczeniowego `MIN`, `MAX`, `MEAN` wartość lub `CUSTOM` wartość. Gdy `group_by_columns` jest określony, brakujące wartości zostaną kalkulacyjnych przez grupę przy użyciu `MIN`, `MAX`, i `MEAN` obliczane dla każdej grupy.

Sprawdź `MEAN` wartość szerokości kolumn za pomocą [ `summarize()` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#summarize-summary-columns--typing-union-typing-list-azureml-dataprep-api-dataflow-summarycolumnsvalue---nonetype----none--group-by-columns--typing-union-typing-list-str---nonetype----none--join-back--bool---false--join-back-columns-prefix--typing-union-str--nonetype----none-----azureml-dataprep-api-dataflow-dataflow) funkcji. Ta funkcja akceptuje tablica kolumn w `group_by_columns` parametru do określenia poziomu agregacji. `summary_columns` Akceptuje parametr `SummaryColumnsValue` wywołania. Wywołanie tej funkcji określa bieżąca nazwa kolumny, Nowa nazwa pola obliczeniowego i `SummaryFunction` do wykonania.

```python
dflow_mean = dflow.summarize(group_by_columns=['Arrest'],
                       summary_columns=[dprep.SummaryColumnsValue(column_id='Latitude',
                                                                 summary_column_name='Latitude_MEAN',
                                                                 summary_function=dprep.SummaryFunction.MEAN)])
dflow_mean = dflow_mean.filter(dprep.col('Arrest') == 'false')
dflow_mean.head(1)
```

||Aresztowania|Latitude_MEAN|
|-----|-----|----|
|0|false|41.878961|

`MEAN` Wartość geograficznej wygląda dokładne, należy użyć `ImputeColumnArguments` funkcji przypisują je. Ta funkcja akceptuje `column_id` ciąg, a `ReplaceValueFunction` do określania typu impute. Brakuje wartości szerokości geograficznej przypisują go za pomocą 42 na podstawie wiedzy zewnętrznych.

Naliczenie kroki można łączyć w łańcuch do `ImputeMissingValuesBuilder` obiektu przy użyciu funkcji konstruktora `impute_missing_values()`. `impute_columns` Parametr przyjmuje tablicę `ImputeColumnArguments` obiektów. Wywołaj `learn()` funkcji do przechowywania kroki impute, a następnie zastosować obiekt przepływu danych przy użyciu `to_dataflow()`.

```python
# impute with MEAN
impute_mean = dprep.ImputeColumnArguments(column_id='Latitude',
                                          impute_function=dprep.ReplaceValueFunction.MEAN)
# impute with custom value 42
impute_custom = dprep.ImputeColumnArguments(column_id='Longitude',
                                            custom_impute_value=42)
# get instance of ImputeMissingValuesBuilder
impute_builder = dflow.builders.impute_missing_values(impute_columns=[impute_mean, impute_custom],
                                                   group_by_columns=['Arrest'])

impute_builder.learn()
dflow_imputed = impute_builder.to_dataflow()
dflow_imputed.head(3)
```

||ID|Aresztowania|Szerokość geograficzna|Długość geograficzna|
|-----|------|-----|------|-----|
|0|10140490|false|41.973309|-87.800175|
|1|10139776|false|42.008124|-87.659550|
|2|10140270|false|41.878961|42.000000|

Jak pokazano w wyniku powyżej, brak szerokości geograficznej został kalkulacyjne z `MEAN` wartość `Arrest=='false'` grupy. Brak długości geograficznej został kalkulacyjne z 42.

```python
imputed_longitude = dflow_imputed.to_pandas_dataframe()['Longitude'][2]
assert imputed_longitude == 42
```

## <a name="derive-column-by-example"></a>Tworzenie kolumn pochodnych według przykładu

Jednym z bardziej zaawansowanych narzędzi w usługi Azure Machine Learning Prep zestawu SDK usługi Data jest możliwość uzyskiwania kolumn przy użyciu przykładów zakładanych wyników. Dzięki temu można podać przykład zestawu SDK, dzięki czemu może generować kod w celu osiągnięcia zamierzonego przekształcania.

```python
import azureml.dataprep as dprep
dflow = dprep.read_csv(path='https://dpreptestfiles.blob.core.windows.net/testfiles/BostonWeather.csv')
dflow.head(4)
```

||DATE|REPORTTPYE|HOURLYDRYBULBTEMPF|HOURLYRelativeHumidity|HOURLYWindSpeed|
|----|----|----|----|----|----|
|0|1/1/2015 0:54|FM-15|22|50|10|
|1|1/1/2015 1:00|FM-12|22|50|10|
|2|1/1/2015 1:54|FM-15|22|50|10|
|3|1/1/2015 2:54|FM-15|22|50|11|

Przyjęto założenie, że należy dołączyć ten plik przy użyciu zestawu danych, w których data i godzina w formacie "10 marca 2018 r. | 2: 00 – 4: 00 ".

```python
builder = dflow.builders.derive_column_by_example(source_columns=['DATE'], new_column_name='date_timerange')
builder.add_example(source_data=dflow.iloc[1], example_value='Jan 1, 2015 12AM-2AM')
builder.preview(count=5) 
```

||DATE|date_timerange|
|----|----|----|
|0|1/1/2015 0:54|1 stycznia 2015 0: 00 – 2: 00|
|1|1/1/2015 1:00|1 stycznia 2015 0: 00 – 2: 00|
|2|1/1/2015 1:54|1 stycznia 2015 0: 00 – 2: 00|
|3|1/1/2015 2:54|1 stycznia 2015 2: 00 – 4: 00|
|4|1/1/2015 3:54|1 stycznia 2015 2: 00 – 4: 00|

Powyższy kod najpierw tworzy konstruktora dla kolumny pochodnej. Należy podać tablicę kolumny źródłowe wziąć pod uwagę (`DATE`) i nazwę dla nowej kolumny, która ma zostać dodana. Co w pierwszym przykładzie przekazywania w drugim wierszu (indeks 1) i nadaj oczekiwaną wartością dla kolumny pochodnej.

Na koniec Wywołaj `builder.preview(skip=30, count=5)` i sprawdź, czy kolumny pochodnej obok kolumny źródłowej. Format jest prawidłowy, ale tylko zostaną wyświetlone wartości dla tej samej daty "1 stycznia 2015".

Liczba wierszy, które chcesz teraz przekazać `skip` z góry, aby wyświetlić wiersze w dół.

> [!NOTE]
> Funkcja preview() pomija wierszy, ale nie ponownie numer indeksu danych wyjściowych. W poniższym przykładzie indeks 0 w tabeli odnosi się do indeksu 30 w przepływie danych.

```python
builder.preview(skip=30, count=5)
```

||DATE|date_timerange|
|-----|-----|-----|
|0|1/1/2015 22:54|Od 1 stycznia 2015 r. 22: 00 - 00: 00|
|1|1/1/2015 23:54|Od 1 stycznia 2015 r. 22: 00 - 00: 00|
|2|1/1/2015 23:59|Od 1 stycznia 2015 r. 22: 00 - 00: 00|
|3|1/2/2015 0:54|1 lutego 2015 0: 00 – 2: 00|
|4|1/2/2015 1:00|1 lutego 2015 0: 00 – 2: 00|

W tym miejscu zobaczysz wystąpił problem z wygenerowanego programu. Wyłącznie na podstawie jednego przykładu podane powyżej, program pochodnych wybrała można przeanalizować daty jako "Dzień/miesiąc/rok", czyli nie chcesz w tym przypadku. Aby rozwiązać ten problem, docelowy określonego indeksu rekordu, a następnie podaj inny przykład przy użyciu `add_example()` działać na `builder` zmiennej.

```python
builder.add_example(source_data=dflow.iloc[3], example_value='Jan 2, 2015 12AM-2AM')
builder.preview(skip=30, count=5)
```

||DATE|date_timerange|
|-----|-----|-----|
|0|1/1/2015 22:54|Od 1 stycznia 2015 r. 22: 00 - 00: 00|
|1|1/1/2015 23:54|Od 1 stycznia 2015 r. 22: 00 - 00: 00|
|2|1/1/2015 23:59|Od 1 stycznia 2015 r. 22: 00 - 00: 00|
|3|1/2/2015 0:54|2 stycznia 2015 0: 00 – 2: 00|
|4|1/2/2015 1:00|2 stycznia 2015 0: 00 – 2: 00|

Teraz poprawnie obsługiwać wiersze "1/2/2015' jako"2 stycznia 2015", ale jeśli wygląda poza index 76 kolumny pochodnej, zobaczysz, że wartości na końcu ma nic w kolumny pochodnej.

```python
builder.preview(skip=75, count=5)
```


||DATE|date_timerange|
|-----|-----|-----|
|0|1/3/2015 7:00|3 stycznia 2015 6: 00 - 8: 00|
|1|1/3/2015 7:54|3 stycznia 2015 6: 00 - 8: 00|
|2|1/29/2015 6:54|Brak|
|3|1/29/2015 7:00|Brak|
|4|1/29/2015 7:54|Brak|

```python
builder.add_example(source_data=dflow.iloc[77], example_value='Jan 29, 2015 6AM-8AM')
builder.preview(skip=75, count=5)
```

||DATE|date_timerange|
|-----|-----|-----|
|0|1/3/2015 7:00|3 stycznia 2015 6: 00 - 8: 00|
|1|1/3/2015 7:54|3 stycznia 2015 6: 00 - 8: 00|
|2|1/29/2015 6:54|29 stycznia 2015 6: 00 - 8: 00|
|3|1/29/2015 7:00|29 stycznia 2015 6: 00 - 8: 00|
|4|1/29/2015 7:54|29 stycznia 2015 6: 00 - 8: 00|

 Aby wyświetlić listę bieżącego pochodnymi przykład wywołania `list_examples()` do konstruktora obiektu.

```python
examples = builder.list_examples()
```

| |DATE|Przykład|example_id|
| -------- | -------- | -------- | -------- |
|0|1/1/2015 1:00|1 stycznia 2015 0: 00 – 2: 00|-1|
|1|1/2/2015 0:54|2 stycznia 2015 0: 00 – 2: 00|-2|
|2|2015-1-29 20:54|29 stycznia 2015 20: 00 - 22: 00|-3|


W niektórych przypadkach, jeśli chcesz usunąć przykładów, które są niepoprawne, możesz przekazać albo `example_row` z biblioteki pandas DataFrame, lub `example_id` wartość. Na przykład jeśli uruchomisz `builder.delete_example(example_id=-1)`, usuwa w pierwszym przykładzie transformacji.


Wywołaj `to_dataflow()` w konstruktorze, która zwraca przepływu danych z wymaganych kolumn pochodnych dodane.

```python
dflow = builder.to_dataflow()
df = dflow.to_pandas_dataframe()
```

## <a name="filtering"></a>Filtering

Zestaw SDK zawiera metody [ `drop_columns()` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#drop-columns-columns--multicolumnselection-----azureml-dataprep-api-dataflow-dataflow) i [ `filter()` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py) umożliwia filtrowanie wierszy lub kolumn.

### <a name="initial-setup"></a>Konfiguracja początkowa

```python
import azureml.dataprep as dprep
from datetime import datetime
dflow = dprep.read_csv(path='https://dprepdata.blob.core.windows.net/demo/green-small/*')
dflow.head(5)
```

||lpep_pickup_datetime|Lpep_dropoff_datetime|Store_and_fwd_flag|RateCodeID|Pickup_longitude|Pickup_latitude|Dropoff_longitude|Dropoff_latitude|Passenger_count|Trip_distance|Tip_amount|Tolls_amount|Total_amount|
|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|
|0|Brak|Brak|Brak|Brak|Brak|Brak|Brak|Brak|Brak|Brak|Brak|Brak|Brak|
|1|2013-08-01-08:14:37|2013-08-01-09:09:06|Nie|1|0|0|0|0|1|.00|0|0|21,25|
|2|2013-08-01-09:13:00|2013-08-01 11:38:00|Nie|1|0|0|0|0|2|.00|0|0|75|
|3|2013-08-01-09:48:00|2013-08-01-09:49:00|Nie|5|0|0|0|0|1|.00|0|1|2.1|
|4|2013-08-01-10:38:35|2013-08-01-10:38:51|Nie|1|0|0|0|0|1|.00|0|0|3.25|

### <a name="filtering-columns"></a>Filtrowanie kolumn

Aby filtrować kolumny, użyj `drop_columns()`. Ta metoda przyjmuje listę kolumn, aby porzucić lub bardziej złożone argument o nazwie [ `ColumnSelector` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.columnselector?view=azure-dataprep-py).

#### <a name="filtering-columns-with-list-of-strings"></a>Filtrowanie kolumn zawierających listę ciągów

W tym przykładzie `drop_columns()` przyjmuje listę ciągów. Każdy ciąg powinny być identyczne z odpowiednią kolumnę w celu usunięcia.

```python
dflow = dflow.drop_columns(['Store_and_fwd_flag', 'RateCodeID'])
dflow.head(2)
```

||lpep_pickup_datetime|Lpep_dropoff_datetime|Pickup_longitude|Pickup_latitude|Dropoff_longitude|Dropoff_latitude|Passenger_count|Trip_distance|Tip_amount|Tolls_amount|Total_amount|
|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|
|0|Brak|Brak|Brak|Brak|Brak|Brak|Brak|Brak|Brak|Brak|Brak|
|1|2013-08-01-08:14:37|2013-08-01-09:09:06|0|0|0|0|1|.00|0|0|21,25|

#### <a name="filtering-columns-with-regex"></a>Filtrowanie kolumn z wyrażeniem regularnym

Można również użyć `ColumnSelector` wyrażenia można usunąć kolumny, które są zgodne z danym wyrażeniem regularnym. W tym przykładzie, usuniesz wszystkie kolumny, które pasują do wyrażenia `Column*|.*longitude|.*latitude`.

```python
dflow = dflow.drop_columns(dprep.ColumnSelector('Column*|.*longitud|.*latitude', True, True))
dflow.head(2)
```

||lpep_pickup_datetime|Lpep_dropoff_datetime|Passenger_count|Trip_distance|Tip_amount|Tolls_amount|Total_amount|
|-----|-----|-----|-----|-----|-----|-----|-----|
|0|Brak|Brak|Brak|Brak|Brak|Brak|Brak|
|1|2013-08-01-08:14:37|2013-08-01-09:09:06|1|.00|0|0|21,25|

## <a name="filtering-rows"></a>Filtrowanie wierszy

Filtruj wiersze, należy użyć `filter()`. Ta metoda przyjmuje zestawu SDK usługi Azure Machine Learning danych Prep wyrażenie jako argument i zwraca nowy przepływ danych z wierszami, która wyrażenie ma wartość true. Wyrażenia są tworzone za pomocą konstruktorów wyrażeń (`col`, `f_not`, `f_and`, `f_or`) i regularnego operatorów (>, <>, =, < =, ==,! =).

### <a name="filtering-rows-with-simple-expressions"></a>Filtrowanie wierszy za pomocą prostych wyrażeń

Za pomocą Konstruktora wyrażeń `col`, określ nazwę kolumny jako argumentu ciągu `col('column_name')`. Użyj tego wyrażenia w połączeniu z jedną z następujących standardowych operatorów >, <>, =, < =, ==,! =, aby utworzyć wyrażenie, takie jak `col('Tip_amount') > 0`. Na koniec Przekaż skompilowane wyrażenie `filter()` funkcji.

W tym przykładzie `dflow.filter(col('Tip_amount') > 0)` zwraca nowego przepływu danych z wierszami, w którym wartość `Tip_amount` jest większa niż 0.

> [!NOTE] 
> `Tip_amount` jest najpierw konwertowany na liczbowy, co pozwala utworzyć wyrażenie Porównanie innych wartości liczbowych.

```python
dflow = dflow.to_number(['Tip_amount'])
dflow = dflow.filter(dprep.col('Tip_amount') > 0)
dflow.head(2)
```

||lpep_pickup_datetime|Lpep_dropoff_datetime|Passenger_count|Trip_distance|Tip_amount|Tolls_amount|Total_amount|
|-----|-----|-----|-----|-----|-----|-----|-----|
|0|2013-08-01-19:33:28|2013-08-01-19:35:21|5|.00|0,08|0|4.58|
|1|2013-08-05 13:16:38|2013-08-05 13:18:24|1|.00|0.30|0|3.8|

### <a name="filtering-rows-with-complex-expressions"></a>Filtrowanie wierszy za pomocą wyrażenia złożone

Aby filtrować przy użyciu wyrażeń złożonych, wyrażeń co najmniej jeden prostego z konstruktorów wyrażeń `f_not`, `f_and`, lub `f_or`.

W tym przykładzie `dflow.filter()` zwraca nowego przepływu danych z wierszami gdzie `'Passenger_count'` jest mniejsza niż 5 i `'Tolls_amount'` jest większa niż 0.

```python
dflow = dflow.to_number(['Passenger_count', 'Tolls_amount'])
dflow = dflow.filter(dprep.f_and(dprep.col('Passenger_count') < 5, dprep.col('Tolls_amount') > 0))
dflow.head(2)
```

||lpep_pickup_datetime|Lpep_dropoff_datetime|Passenger_count|Trip_distance|Tip_amount|Tolls_amount|Total_amount|
|-----|-----|-----|-----|-----|-----|-----|-----|
|0|2013-08-08 12:16:00|2013-08-08 12:16:00|1.0|.00|2.25|5.00|19.75|
|1|2013-08-12 14:43:53|2013-08-12: 15:04:50|1.0|5.28|6.46|5.33|32.29|

Istnieje również możliwość filtrowanie wierszy, łącząc więcej niż jeden Konstruktor wyrażeń, aby utworzyć wyrażenie zagnieżdżonych.

> [!NOTE]
> `lpep_pickup_datetime` i `Lpep_dropoff_datetime` są najpierw konwertowany na daty i godziny, który pozwala utworzyć wyrażenie porównanie inne wartości daty/godziny.

```python
dflow = dflow.to_datetime(['lpep_pickup_datetime', 'Lpep_dropoff_datetime'], ['%Y-%m-%d %H:%M:%S'])
dflow = dflow.to_number(['Total_amount', 'Trip_distance'])
mid_2013 = datetime(2013,7,1)
dflow = dflow.filter(
    dprep.f_and(
        dprep.f_or(
            dprep.col('lpep_pickup_datetime') > mid_2013,
            dprep.col('Lpep_dropoff_datetime') > mid_2013),
        dprep.f_and(
            dprep.col('Total_amount') > 40,
            dprep.col('Trip_distance') < 10)))
dflow.head(2)
```

||lpep_pickup_datetime|Lpep_dropoff_datetime|Passenger_count|Trip_distance|Tip_amount|Tolls_amount|Total_amount|
|-----|-----|-----|-----|-----|-----|-----|-----|
|0|2013-08-13 06:11:06 + 00:00|2013-08-13 06:30:28 + 00:00|1.0|9,57|7.47|5.33|44.80|
|1|2013-08-23 12:28:20 + 00:00|2013-08-23 12:50:28 + 00:00|2.0|8.22|8.08|5.33|40.41|

## <a name="custom-python-transforms"></a>Niestandardowe przekształcenia języka Python

Zawsze będzie scenariuszy podczas pisania własnego skryptu Najłatwiejszą opcją składania transformacji. Zestaw SDK udostępnia trzy punkty rozszerzenia, które można użyć niestandardowych skryptów języka Python.

- Nowa kolumna skryptu
- Nowy filtr skryptu
- Przekształcanie partycji

Każdego rozszerzenia jest obsługiwana w runtime skalowanie w górę i skalowania w poziomie. Zaletą używania tych punktów rozszerzenia jest, nie należy ściągnąć wszystkie dane, aby można było utworzyć ramkę danych. Twoje niestandardowe języka Python, którego kod będzie działał tylko, takich jak innych transformacji, w dużej skali, przez partycję i zazwyczaj równolegle.

### <a name="initial-data-preparation"></a>Przygotowanie danych początkowych

Rozpocznij od ładowania niektórych danych z obiektów Blob platformy Azure.

```python
import azureml.dataprep as dprep
col = dprep.col

dflow = dprep.read_csv(path='https://dpreptestfiles.blob.core.windows.net/testfiles/read_csv_duplicate_headers.csv', skip_rows=1)
dflow.head(2)
```

| |stnam|fipst|leaid|leanm10|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|-----|------|
|0|ALABAMA|1|101710|Hale hrabstwa|10171002158| |
|1|ALABAMA|1|101710|Hale hrabstwa|10171002162| |

Ogranicz szczegółów zestawu danych, a następnie wykonaj niektóre podstawowe przekształceń, w tym usunięcie kolumn, zastępując wartości oraz konwertowanie typów.

```python
dflow = dflow.keep_columns(['stnam', 'leanm10', 'ncessch', 'MAM_MTH00numvalid_1011'])
dflow = dflow.replace_na(columns=['leanm10', 'MAM_MTH00numvalid_1011'], custom_na_list='.')
dflow = dflow.to_number(['ncessch', 'MAM_MTH00numvalid_1011'])
dflow.head(2)
```

| |stnam|leanm10|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|
|0|ALABAMA|Hale hrabstwa|1.017100e + 10|Brak|
|1|ALABAMA|Hale hrabstwa|1.017100e + 10|Brak|

Zwróć uwagę na wartości null, korzystając z następujących filtrów.

```python
dflow.filter(col('MAM_MTH00numvalid_1011').is_null()).head(2)
```

| |stnam|leanm10|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|
|0|ALABAMA|Hale hrabstwa|1.017100e + 10|Brak|
|1|ALABAMA|Hale hrabstwa|1.017100e + 10|Brak|

### <a name="transform-partition"></a>Przekształcanie partycji

Użyj [ `transform_partition()` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#transform-partition-script--str-----azureml-dataprep-api-dataflow-dataflow) Aby zamienić wszystkie wartości null z 0. Ten kod będzie działał przez partycję, a nie na cały zestaw danych w tym samym czasie. Oznacza to, że dotyczące dużych zestawów danych, ten kod mogą być wykonywane równolegle jako środowisko wykonawcze przetwarza dane, partycji, partycji.

Skrypt w języku Python, musisz zdefiniować funkcję o nazwie `transform()` która przyjmuje dwa argumenty `df` i `index`. `df` Argument będzie pandas ramkę danych, która zawiera dane dla partycji i `index` argument jest unikatowym identyfikatorem partycji. Funkcja transformacji pełni edytować przekazaną ramkę danych, ale musi zwracać ramkę danych. Wszystkie biblioteki, które importuje skrypt w języku Python musi istnieć w środowisku, w którym uruchomiono przepływu danych.

```python
df = df.transform_partition("""
def transform(df, index):
    df['MAM_MTH00numvalid_1011'].fillna(0,inplace=True)
    return df
""")
df.head(2)
```

||stnam|leanm10|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|
|0|ALABAMA|Hale hrabstwa|1.017100e + 10|0.0|
|1|ALABAMA|Hale hrabstwa|1.017100e + 10|0.0|

### <a name="new-script-column"></a>Nowa kolumna skryptu

Utwórz nową kolumnę, która ma tę nazwę i nazwę stanu, a także korzystaj Nazwa stanu, można użyć skryptu w języku Python. Aby to zrobić, należy użyć [ `new_script_column()` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#new-script-column-new-column-name--str--insert-after--str--script--str-----azureml-dataprep-api-dataflow-dataflow) metody na przepływ danych.

Skrypt w języku Python, musisz zdefiniować funkcję o nazwie `newvalue()` , przyjmuje jeden argument `row`. `row` Argument jest dict (`key`: Nazwa kolumny `val`: Bieżąca wartość) i zostaną przekazane do tej funkcji dla każdego wiersza w zestawie danych. Ta funkcja musi zwracać wartość ma być używany w nowej kolumnie. Wszystkie biblioteki, które importuje skrypt w języku Python musi istnieć w środowisku, w którym uruchomiono przepływu danych.

```python
dflow = dflow.new_script_column(new_column_name='county_state', insert_after='leanm10', script="""
def newvalue(row):
    return row['leanm10'] + ', ' + row['stnam'].title()
""")
dflow.head(2)
```

||stnam|leanm10|county_state|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|-----|
|0|ALABAMA|Hale hrabstwa|Hale hrabstwa, Alabama|1.017100e + 10|0.0|
|1|ALABAMA|Hale hrabstwa|Hale hrabstwa, Alabama|1.017100e + 10|0.0|

### <a name="new-script-filter"></a>Nowy filtr skryptu

Kompiluj wyrażenie języka Python przy użyciu [ `new_script_filter()` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#new-script-filter-script--str-----azureml-dataprep-api-dataflow-dataflow) do filtrowania zestawu danych, aby tylko wiersze, w którym "Hale", nie znajduje się w nowym `county_state` kolumny. Wyrażenie zwraca `True` Jeśli chcemy zachować wiersza, i `False` można usunąć wiersza.

```python
dflow = dflow.new_script_filter("""
def includerow(row):
    val = row['county_state']
    return 'Hale' not in val
""")
dflow.head(2)
```

||stnam|leanm10|county_state|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|-----|
|0|ALABAMA|Jefferson hrabstwa|Jefferson hrabstwa, Alabama|1.019200e + 10|1.0|
|1|ALABAMA|Jefferson hrabstwa|Jefferson hrabstwa, Alabama|1.019200e + 10|0.0|

## <a name="next-steps"></a>Kolejne kroki

* Zobacz zestaw SDK Azure Machine Learning danych Prep [samouczek](tutorial-data-prep.md) przykładem rozwiązania konkretnego scenariusza
