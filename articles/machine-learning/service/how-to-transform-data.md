---
title: Przekształcanie danych za pomocą Azure Machine Learning danych Prep SDK — języka Python
description: Więcej informacji na temat przekształcania i czyszczenie danych przy użyciu zestawu SDK usługi Azure Machine Learning danych przedprodukcyjnym. Dodawanie kolumn odfiltrować zbędne wiersze lub kolumny i przypisują brakujące wartości, należy użyć metod transformacji.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: cforbe
author: cforbe
manager: cgronlun
ms.reviewer: jmartens
ms.date: 09/24/2018
ms.openlocfilehash: 388957fc5dac5cdab5bee34a4431eaa524e76a48
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46999906"
---
# <a name="transform-data-with-the-azure-machine-learning-data-prep-sdk"></a>Przekształcanie danych za pomocą usługi Azure Machine Learning Prep zestawu SDK usługi Data

[Zestawu SDK usługi Azure Machine Learning danych Prep](https://docs.microsoft.com/python/api/overview/azure/dataprep?view=azure-dataprep-py) oferuje różne przekształcenia metody, aby wyczyścić dane. Metody te ułatwiają dodawanie kolumn odfiltrować zbędne wiersze lub kolumny i przypisują brakujące wartości.

Obecnie dostępne są metody w celu uwzględnienia poniższych zadań:
- [Dodawanie kolumny za pomocą wyrażenia](#add-column-using-expression)
- [Naliczenie brakujące wartości](#impute-missing-values)
- [Utwórz kolumnę pochodną według przykładu](#derive-column-by-example)
- [Filtrowanie](#filtering)
- [Niestandardowe przekształcenia języka Python](#custom-python-transforms)

## <a name="add-column-using-an-expression"></a>Dodawanie kolumny za pomocą wyrażenia

Zestaw SDK Azure Machine Learning danych Prep zawiera `substring` wyrażeń, można użyć do obliczenia wartości z istniejących kolumn, a następnie umieść tej wartości w nowej kolumnie. W tym przykładzie firma Microsoft ładowanie danych i spróbuj dodać kolumny do tych danych wejściowych.

```
import azureml.dataprep as dprep

# loading data
dataflow = dprep.read_csv(path=r'data\crime0-10.csv')
dataflow.head(3)
```

||ID|Liczba przypadków|Date|Blokuj|IUCR|Typ podstawowy|Opis|Opis lokalizacji|Aresztowania|Krajowych|Przyciski ...|Lej|Obszar społeczności|FBI kodu|Współrzędna x|Współrzędna Y|Rok|Aktualizacja:|Szerokość geograficzna|Długość geograficzna|Lokalizacja|
|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|
|0|10140490|HY329907|2015-07-05 11:50:00 PM|ZAPISZ NEWLAND N 050XX|0820|PRZED KRADZIEŻĄ|500 USD I W OBSZARZE|ULICA|false|false|Przyciski ...|41|10|06|1129230|1933315|2015|2015-07-12 |GODZINA 12:42:46|41.973309466|-87.800174996|(41.973309466,-87.800174996)|
|1|10139776|HY329265|2015-07-05 11:30:00 PM|ZAPISZ MORSE'A W 011XX|0460|BATERIA|PROSTE|ULICA|false|true|Przyciski ...|49|1|08B|1167370|1946271|2015|2015-07-12 12:42:46: 00|42.008124017|-87.65955018|(42.008124017,-87.65955018)|
|2|10140270|HY329253|2015-07-05 11:20:00 PM|Z PRZODU APISZ 121XX|0486|BATERIA|PROSTE ŚWIATOWEGO BATERII|ULICA|false|true|Przyciski ...|9|53|08B|||2015|2015-07-12 12:42:46: 00|



Użyj `substring(start, length)` wyrażenie, aby wyodrębnić prefiks z kolumny liczba przypadków i umieść je w nowej kolumnie: Kategoria przypadku.

```
substring_expression = dprep.col('Case Number').substring(0, 2)
case_category = dataflow.add_column(new_column_name='Case Category',
                                    prior_column='Case Number',
                                    expression=substring_expression)
case_category.head(3)
```

||ID|Liczba przypadków|Kategoria przypadku|Date|Blokuj|IUCR|Typ podstawowy|Opis|Opis lokalizacji|Aresztowania|Przyciski ...|Lej|Obszar społeczności|FBI kodu|Współrzędna x|Współrzędna Y|Rok|Aktualizacja:|Szerokość geograficzna|Długość geograficzna|Lokalizacja|
|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|------|
|0|10140490|HY329907|AZJATYCKA|2015-07-05 11:50:00 PM|ZAPISZ NEWLAND N 050XX|0820|PRZED KRADZIEŻĄ|500 USD I W OBSZARZE|ULICA|false|false|Przyciski ...|41|10|06|1129230|1933315|2015|2015-07-12 |GODZINA 12:42:46|41.973309466|-87.800174996|(41.973309466,-87.800174996)|
|1|10139776|HY329265|AZJATYCKA|2015-07-05 11:30:00 PM|ZAPISZ MORSE'A W 011XX|0460|BATERIA|PROSTE|ULICA|false|true|Przyciski ...|49|1|08B|1167370|1946271|2015|2015-07-12 12:42:46: 00|42.008124017|-87.65955018|(42.008124017,-87.65955018)|
|2|10140270|HY329253|AZJATYCKA|2015-07-05 11:20:00 PM|Z PRZODU APISZ 121XX|0486|BATERIA|PROSTE ŚWIATOWEGO BATERII|ULICA|false|true|Przyciski ...|9|53|08B|||2015|2015-07-12 12:42:46: 00|



Użyj `substring(start)` wyrażenie prowadzenie tylko numer kolumny liczba przypadków, a następnie przekonwertować go na typ danych numerycznych i umieść je w nowej kolumnie: Identyfikator przypadku.
```
substring_expression2 = dprep.col('Case Number').substring(2)
case_id = dataflow.add_column(new_column_name='Case Id',
                              prior_column='Case Number',
                              expression=substring_expression2)
case_id = case_id.to_number('Case Id')
case_id.head(3)
```

||ID|Liczba przypadków|Identyfikator przypadku|Date|Blokuj|IUCR|Typ podstawowy|Opis|Opis lokalizacji|Aresztowania|Przyciski ...|Lej|Obszar społeczności|FBI kodu|Współrzędna x|Współrzędna Y|Rok|Aktualizacja:|Szerokość geograficzna|Długość geograficzna|Lokalizacja|
|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|------|
|0|10140490|HY329907|329907.0|2015-07-05 11:50:00 PM|ZAPISZ NEWLAND N 050XX|0820|PRZED KRADZIEŻĄ|500 USD I W OBSZARZE|ULICA|false|false|Przyciski ...|41|10|06|1129230|1933315|2015|2015-07-12 |GODZINA 12:42:46|41.973309466|-87.800174996|(41.973309466,-87.800174996)|
|1|10139776|HY329265|329265.0|2015-07-05 11:30:00 PM|ZAPISZ MORSE'A W 011XX|0460|BATERIA|PROSTE|ULICA|false|true|Przyciski ...|49|1|08B|1167370|1946271|2015|2015-07-12 12:42:46: 00|42.008124017|-87.65955018|(42.008124017,-87.65955018)|
|2|10140270|HY329253|329253.0|2015-07-05 11:20:00 PM|Z PRZODU APISZ 121XX|0486|BATERIA|PROSTE ŚWIATOWEGO BATERII|ULICA|false|true|Przyciski ...|9|53|08B|||2015|2015-07-12 12:42:46: 00|

## <a name="impute-missing-values"></a>Naliczenie brakujące wartości

Azure Machine Learning Prep zestawu SDK usługi Data można przypisują brakujących wartości w określonych kolumnach. W tym przykładzie będzie załadować wartości długości i szerokości geograficznej, a następnie spróbuj przypisują brakujących wartości w danych wejściowych.

```
import azureml.dataprep as dprep

# loading input data
df = dprep.read_csv(r'data\crime0-10.csv')
df = df.keep_columns(['ID', 'Arrest', 'Latitude', 'Longitude'])
df = df.to_number(['Latitude', 'Longitude'])
df.head(5)
```

||ID|Aresztowania|Szerokość geograficzna|Długość geograficzna|
|-----|------|-----|------|-----|
|0|10140490|false|41.973309|-87.800175|
|1|10139776|false|42.008124|-87.659550|
|2|10140270|false|NaN|NaN|
|3|10139885|false|41.902152|-87.754883|
|4|10140379|false|41.885610|-87.657009|

Trzeci rekordzie brakuje wartości długości i szerokości geograficznej. Aby przypisują te brakujące wartości, można użyć `ImputeMissingValuesBuilder` się program stały. Jego przypisują kolumny albo obliczeniowego `MIN`, `MAX`, lub `MEAN` wartość lub `CUSTOM` wartość. Gdy `group_by_columns` jest określony, brakujące wartości zostaną kalkulacyjnych przez grupę przy użyciu `MIN`, `MAX`, i `MEAN` obliczane dla każdej grupy.

Po pierwsze, szybko sprawdzić `MEAN` wartości w kolumnie szerokości geograficznej.
```
df_mean = df.summarize(group_by_columns=['Arrest'],
                       summary_columns=[dprep.SummaryColumnsValue(column_id='Latitude',
                                                                 summary_column_name='Latitude_MEAN',
                                                                 summary_function=dprep.SummaryFunction.MEAN)])
df_mean = df_mean.filter(dprep.col('Arrest') == 'false')
df_mean.head(1)
```

||Aresztowania|Latitude_MEAN|
|-----|-----|----|
|0|false|41.878961|

`MEAN` Wartość geograficznej wygląda OK, aby można było używać do przypisują szerokości geograficznej. Brakuje wartości szerokości geograficznej firma Microsoft będzie przypisują z 42 na podstawie wiedzy zewnętrznych.


```
# impute with MEAN
impute_mean = dprep.ImputeColumnArguments(column_id='Latitude',
                                          impute_function=dprep.ReplaceValueFunction.MEAN)
# impute with custom value 42
impute_custom = dprep.ImputeColumnArguments(column_id='Longitude',
                                            custom_impute_value=42)
# get instance of ImputeMissingValuesBuilder
impute_builder = df.builders.impute_missing_values(impute_columns=[impute_mean, impute_custom],
                                                   group_by_columns=['Arrest'])
# call learn() to learn a fixed program to impute missing values
impute_builder.learn()
# call to_dataflow() to get a data flow with impute step added
df_imputed = impute_builder.to_dataflow()

# check impute result
df_imputed.head(5)
```

||ID|Aresztowania|Szerokość geograficzna|Długość geograficzna|
|-----|------|-----|------|-----|
|0|10140490|false|41.973309|-87.800175|
|1|10139776|false|42.008124|-87.659550|
|2|10140270|false|41.878961|42.000000|
|3|10139885|false|41.902152|-87.754883|
|4|10140379|false|41.885610|-87.657009|

Jak pokazano w wyniku powyżej, brak szerokości geograficznej został kalkulacyjne z `MEAN` wartość `Arrest=='false'` grupy. Brak długości geograficznej został kalkulacyjne z 42.
```
imputed_longitude = df_imputed.to_pandas_dataframe()['Longitude'][2]
assert imputed_longitude == 42
```

## <a name="derive-column-by-example"></a>Tworzenie kolumn pochodnych według przykładu
Jednym z bardziej zaawansowanych narzędzi w usługi Azure Machine Learning Prep zestawu SDK usługi Data jest możliwość uzyskiwania kolumn przy użyciu przykładów z odpowiednich wyników. Dzięki temu można podać przykład zestawu SDK, dzięki czemu może generować kod w celu osiągnięcia zamierzonego pochodnym.

```
import azureml.dataprep as dprep
dataflow = dprep.read_csv(path='https://dpreptestfiles.blob.core.windows.net/testfiles/BostonWeather.csv')
df = dataflow.head(10)
df
```
||DATE|REPORTTPYE|HOURLYDRYBULBTEMPF|HOURLYRelativeHumidity|HOURLYWindSpeed|
|----|----|----|----|----|----|
|0|1/1/2015 0:54|FM-15|22|50|10|
|1|1/1/2015 1:00|FM-12|22|50|10|
|2|1/1/2015 1:54|FM-15|22|50|10|
|3|1/1/2015 2:54|FM-15|22|50|11|
|4|1/1/2015 3:54|FM-15|24|46|13|
|5|1/1/2015 4:00|FM-12|24|46|13|
|6|1/1/2015 4:54|FM-15|22|52|15|
|7|1/1/2015 5:54|FM-15|23|48|17|
|8|1/1/2015 6:54|FM-15|23|50|14|
|9|1/1/2015 7:00|FM-12|23|50|14|

Jak widać, ten plik jest dość prosta. Jednak przyjęto założenie, że należy dołączyć ten plik przy użyciu zestawu danych, w których data i godzina w formacie "10 marca 2018 r. | 2: 00 – 4: 00 ".

Można przekształcać dane, do kształtu, których potrzebujesz.

```
builder = dataflow.builders.derive_column_by_example(source_columns=['DATE'], new_column_name='date_timerange')
builder.add_example(source_data=df.iloc[1], example_value='Jan 1, 2015 12AM-2AM')
builder.preview() 
```

||DATE|date_timerange|
|----|----|----|
|0|1/1/2015 0:54|1 stycznia 2015 0: 00 – 2: 00|
|1|1/1/2015 1:00|1 stycznia 2015 0: 00 – 2: 00|
|2|1/1/2015 1:54|1 stycznia 2015 0: 00 – 2: 00|
|3|1/1/2015 2:54|1 stycznia 2015 2: 00 – 4: 00|
|4|1/1/2015 3:54|1 stycznia 2015 2: 00 – 4: 00|
|5|1/1/2015 4:00|Od 1 stycznia 2015 r. 4: 00 - 6: 00|
|6|1/1/2015 4:54|Od 1 stycznia 2015 r. 4: 00 - 6: 00|
|7|1/1/2015 5:54|Od 1 stycznia 2015 r. 4: 00 - 6: 00|
|8|1/1/2015 6:54|Od 1 stycznia 2015 r. 6: 00 - 8: 00|
|9|1/1/2015 7:00|Od 1 stycznia 2015 r. 6: 00 - 8: 00|

Powyższy kod najpierw tworzy konstruktora dla kolumny pochodnej. Podana tablica kolumn źródłowych wziąć pod uwagę (`DATE`) i nazwę dla nowej kolumny, która ma zostać dodana.

Następnie pierwszy przykład przekazany w drugim wierszu (indeks 1) i udostępniła oczekiwaną wartością dla kolumny pochodnej.

Na koniec, o nazwie `builder.preview()` i sprawdź, czy kolumny pochodnej obok kolumny źródłowej. Format jest dobrym rozwiązaniem, ale tylko zostaną wyświetlone wartości dla tej samej daty "1 stycznia 2015".

Liczba wierszy, które chcesz teraz przekazać `skip` z góry, aby wyświetlić wiersze w dół.

```
preview_df = builder.preview(skip=30)
preview_df
```

||DATE|date_timerange|
|-----|-----|-----|
|30|11/1/2015 22:54|Od 1 stycznia 2015 r. 22: 00 - 00: 00|
|31|11/1/2015 23:54|Od 1 stycznia 2015 r. 22: 00 - 00: 00|
|32|11/1/2015 23:59|Od 1 stycznia 2015 r. 22: 00 - 00: 00|
|33|2-11-2015 0:54|1 lutego 2015 0: 00 – 2: 00|
|34|2015-11-2 1:00|1 lutego 2015 0: 00 – 2: 00|
|35|2015-11-2 1:54|1 lutego 2015 0: 00 – 2: 00|
|36|2015-11-2 2:54|Od 1 lutego 2015 r. 2: 00 – 4: 00|
|37|2015-11-2-3:54|Od 1 lutego 2015 r. 2: 00 – 4: 00|
|38|2015-11-2-4:00|Od 1 lutego 2015 r. 4: 00 - 6: 00|
|39|2015-11-2-4:54|Od 1 lutego 2015 r. 4: 00 - 6: 00|

Tutaj można zobaczyć wystąpił problem z wygenerowanego programu: wyłącznie na podstawie jednego przykładu podanych powyżej, program pochodnych wybrała można przeanalizować daty jako "Dzień/miesiąc/rok", czyli nie chcesz w tym przypadku.

Aby rozwiązać ten problem, należy podać inny przykład.

```
builder.add_example(source_data=preview_df.iloc[3], example_value='Jan 2, 2015 12AM-2AM')
preview_df = builder.preview(skip=30, count=10)
preview_df
```

||DATE|date_timerange|
|-----|-----|-----|
|30|1/1/2015 22:54|Od 1 stycznia 2015 r. 22: 00 - 00: 00|
|31|1/1/2015 23:54|Od 1 stycznia 2015 r. 22: 00 - 00: 00|
|32|1/1/2015 23:59|Od 1 stycznia 2015 r. 22: 00 - 00: 00|
|33|1/2/2015 0:54|2 stycznia 2015 0: 00 – 2: 00|
|34|1/2/2015 1:00|2 stycznia 2015 0: 00 – 2: 00|
|35|1/2/2015 1:54|2 stycznia 2015 0: 00 – 2: 00|
|36|1/2/2015 2:54|2 stycznia 2015 2: 00 – 4: 00|
|37|2015-1-2-3:54|2 stycznia 2015 2: 00 – 4: 00|
|38|1/2/2015 4:00|2 stycznia 2015 4: 00 - 6: 00|
|39|1/2/2015 4:54|2 stycznia 2015 4: 00 - 6: 00|


Teraz poprawnie obsługiwać wiersze "2015-1-2' jako"Sty 2 2015", ale jeśli możesz, spójrz dalej na dół kolumny pochodnej widać, że wartości na końcu ma nic w kolumny pochodnej. Aby rozwiązać ten problem, należy podać inny przykład dla wiersza 66.

```
builder.add_example(source_data=preview_df.iloc[66], example_value='Jan 29, 2015 8PM-10PM')
builder.preview(count=10)
```

||DATE|date_timerange|
|-----|-----|-----|
|0|1/1/2015 22:54|Od 1 stycznia 2015 r. 22: 00 - 00: 00|
|1|1/1/2015 23:54|Od 1 stycznia 2015 r. 22: 00 - 00: 00|
|2|1/1/2015 23:59|Od 1 stycznia 2015 r. 22: 00 - 00: 00|
|3|1/2/2015 0:54|2 stycznia 2015 0: 00 – 2: 00|
|4|1/2/2015 1:00|2 stycznia 2015 0: 00 – 2: 00|
|5|1/2/2015 1:54|2 stycznia 2015 0: 00 – 2: 00|
|6|1/2/2015 2:54|2 stycznia 2015 2: 00 – 4: 00|
|7|2015-1-2-3:54|2 stycznia 2015 2: 00 – 4: 00|
|8|1/2/2015 4:00|2 stycznia 2015 4: 00 - 6: 00|
|9|1/2/2015 4:54|2 stycznia 2015 4: 00 - 6: 00|

Wszystko wygląda dobrze, ale można zauważyć, że jest dokładnie Chcieliśmy. Należy oddzielić daty i godziny przy użyciu ' |' do generowania poprawny format.

Aby rozwiązać ten problem, można dodać inny przykład. Tym razem, zamiast w wierszu w wersji zapoznawczej konstruowania słownika zawierającego nazwę kolumny na wartość `source_data` parametru.

```
builder.add_example(source_data={'DATE': '11/11/2015 0:54'}, example_value='Nov 11, 2015 | 12AM-2AM')
builder.preview(count=10)
```
||DATE|date_timerange|
|-----|-----|-----|
|0|1/1/2015 22:54|Brak|
|1|1/1/2015 23:54|Brak|
|2|1/1/2015 23:59|Brak|
|3|1/2/2015 0:54|Brak|
|4|1/2/2015 1:00|Brak|
|5|1/2/2015 1:54|Brak|
|6|1/2/2015 2:54|Brak|
|7|2015-1-2-3:54|Brak|
|8|1/2/2015 4:00|Brak|
|9|1/2/2015 4:54|Brak|

Negatywnych skutków tego wyraźnie był jak teraz tylko wiersze, które mają wszystkie wartości w kolumnie pochodnej są tymi, które pasują dokładnie do przykładów, które zamieszczone.

Oto przykłady:
```
examples = builder.list_examples()
examples
```

| |DATE|Przykład|example_id|
| -------- | -------- | -------- | -------- |
|0|1/1/2015 1:00|1 stycznia 2015 0: 00 – 2: 00|-1|
|1|1/2/2015 0:54|2 stycznia 2015 0: 00 – 2: 00|-2|
|2|2015-1-29 20:54|29 stycznia 2015 20: 00 - 22: 00|-3|
|3|11-11-2015 0:54|11 listopada 2015 r. \| 12: 00 – 2: 00|-4|

Aby zobaczyć, udostępniliśmy niespójne przykłady. Aby rozwiązać ten problem, należy zastąpić pierwsze trzy przykłady poprawne (w tym ' |' między daty i godziny).

Firma Microsoft może osiągnąć, usuwając przykładów, które są nieprawidłowe (przez przekazanie dowolnego `example_row` z biblioteki pandas DataFrame lub przez przekazanie `example_id` wartość) i następnie dodawania nowych modyfikować przykłady z powrotem.

```
builder.delete_example(example_id=-1)
builder.delete_example(example_row=examples.iloc[1])
builder.delete_example(example_row=examples.iloc[2])
builder.add_example(examples.iloc[0], 'Jan 1, 2015 | 12AM-2AM')
builder.add_example(examples.iloc[1], 'Jan 2, 2015 | 12AM-2AM')
builder.add_example(examples.iloc[2], 'Jan 29, 2015 | 8PM-10PM')
builder.preview()
```

| | DATE | date_timerange |
| -------- | -------- | -------- |
| 0 | 1/1/2015 0:54 | Od 1 stycznia 2015 r. \| 12: 00 – 2: 00 |
| 1 | 1/1/2015 1:00 | Od 1 stycznia 2015 r. \| 12: 00 – 2: 00 |
| 2 | 1/1/2015 1:54 | Od 1 stycznia 2015 r. \| 12: 00 – 2: 00 |
| 3 | 1/1/2015 2:54 | Od 1 stycznia 2015 r. \| 2: 00 – 4: 00 |
| 4 | 1/1/2015 3:54 | Od 1 stycznia 2015 r. \| 2: 00 – 4: 00 |
| 5 | 1/1/2015 4:00 | Od 1 stycznia 2015 r. \| 4: 00 - 6: 00|
| 6 | 1/1/2015 4:54 | Od 1 stycznia 2015 r. \| 4: 00 - 6: 00|
| 7 | 1/1/2015 5:54 | Od 1 stycznia 2015 r. \| 4: 00 - 6: 00|
| 8 | 1/1/2015 6:54 | Od 1 stycznia 2015 r. \| 6: 00 - 8: 00|
| 9 | 1/1/2015 7:00 | Od 1 stycznia 2015 r. \| 6: 00 - 8: 00|

Teraz dane są poprawne, a na koniec możemy wywołać `to_dataflow()` w konstruktorze, co spowoduje zwrócenie przepływu danych z wymaganych kolumn pochodnych dodane.

```
dataflow = builder.to_dataflow()
df = dataflow.to_pandas_dataframe()
df
```

## <a name="filtering"></a>Filtrowanie

Zestaw SDK zawiera metody `Dataflow.drop_columns` i `Dataflow.filter` umożliwia filtrowanie wierszy lub kolumn.

### <a name="initial-setup"></a>Początkowej konfiguracji
```
import azureml.dataprep as dprep
from datetime import datetime
dataflow = dprep.read_csv(path='https://dprepdata.blob.core.windows.net/demo/green-small/*')
dataflow.head(5)
```
||lpep_pickup_datetime|Lpep_dropoff_datetime|Store_and_fwd_flag|RateCodeID|Pickup_longitude|Pickup_latitude|Dropoff_longitude|Dropoff_latitude|Passenger_count|Trip_distance|Tip_amount|Tolls_amount|Total_amount|
|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|
|0|Brak|Brak|Brak|Brak|Brak|Brak|Brak|Brak|Brak|Brak|Brak|Brak|Brak|
|1|2013-08-01-08:14:37|2013-08-01-09:09:06|Nie|1|0|0|0|0|1|.00|0|0|21,25|
|2|2013-08-01-09:13:00|2013-08-01 11:38:00|Nie|1|0|0|0|0|2|.00|0|0|75|
|3|2013-08-01-09:48:00|2013-08-01-09:49:00|Nie|5|0|0|0|0|1|.00|0|1|2.1|
|4|2013-08-01-10:38:35|2013-08-01-10:38:51|Nie|1|0|0|0|0|1|.00|0|0|3.25|

### <a name="filtering-columns"></a>Filtrowanie kolumn

Aby filtrować kolumny, użyj `Dataflow.drop_columns`. Ta metoda przyjmuje listę kolumn, aby porzucić lub bardziej złożone argument o nazwie `ColumnSelector`.

#### <a name="filtering-columns-with-list-of-strings"></a>Filtrowanie kolumn zawierających listę ciągów

W tym przykładzie `drop_columns` przyjmuje listę ciągów. Każdy ciąg powinny być identyczne z odpowiednią kolumnę w celu usunięcia.

``` 
dataflow = dataflow.drop_columns(['Store_and_fwd_flag', 'RateCodeID'])
dataflow.head(5)
```
||lpep_pickup_datetime|Lpep_dropoff_datetime|Pickup_longitude|Pickup_latitude|Dropoff_longitude|Dropoff_latitude|Passenger_count|Trip_distance|Tip_amount|Tolls_amount|Total_amount|
|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|
|0|Brak|Brak|Brak|Brak|Brak|Brak|Brak|Brak|Brak|Brak|Brak|
|1|2013-08-01-08:14:37|2013-08-01-09:09:06|0|0|0|0|1|.00|0|0|21,25|
|2|2013-08-01-09:13:00|2013-08-01 11:38:00|0|0|0|0|2|.00|0|0|75|
|3|2013-08-01-09:48:00|2013-08-01-09:49:00|0|0|0|0|1|.00|0|1|2.1|
|4|2013-08-01-10:38:35|2013-08-01-10:38:51|0|0|0|0|1|.00|0|0|3.25|

#### <a name="filtering-columns-with-regex"></a>Filtrowanie kolumn z wyrażeniem regularnym
Alternatywnie, można użyć `ColumnSelector` wyrażenia można usunąć kolumny, które są zgodne z danym wyrażeniem regularnym. W tym przykładzie firma Microsoft porzucić wszystkie kolumny, które pasują do wyrażenia `Column*|.*longitude|.*latitude`.

```
dataflow = dataflow.drop_columns(dprep.ColumnSelector('Column*|.*longitud|.*latitude', True, True))
dataflow.head(5)
```
||lpep_pickup_datetime|Lpep_dropoff_datetime|Passenger_count|Trip_distance|Tip_amount|Tolls_amount|Total_amount|
|-----|-----|-----|-----|-----|-----|-----|-----|
|0|Brak|Brak|Brak|Brak|Brak|Brak|Brak|
|1|2013-08-01-08:14:37|2013-08-01-09:09:06|1|.00|0|0|21,25|
|2|2013-08-01-09:13:00|2013-08-01 11:38:00|2|.00|0|0|75|
|3|2013-08-01-09:48:00|2013-08-01-09:49:00|1|.00|0|1|2.1|
|4|2013-08-01-10:38:35|2013-08-01-10:38:51|1|.00|0|0|3.25|

## <a name="filtering-rows"></a>Filtrowanie wierszy

Filtruj wiersze, należy użyć `DataFlow.filter`. Ta metoda przyjmuje zestawu SDK usługi Azure Machine Learning danych Prep wyrażenie jako argument i zwraca nowy przepływ danych z wierszami, która wyrażenie ma wartość true. Wyrażenia są tworzone za pomocą konstruktorów wyrażeń (`col`, `f_not`, `f_and`, `f_or`) i regularnego operatorów (>, <>, =, < =, ==,! =).

### <a name="filtering-rows-with-simple-expressions"></a>Filtrowanie wierszy za pomocą prostych wyrażeń

Za pomocą Konstruktora wyrażeń `col`, określ nazwę kolumny jako argumentu ciągu `col('column_name')` i w połączeniu z jedną z następujących standardowych operatorów >, <>, =, < =, ==,! =, takie jak utworzyć wyrażenie `col('Tip_amount') > 0`. Na koniec Przekaż skompilowane wyrażenie `Dataflow.filter` funkcji.

W tym przykładzie `dataflow.filter(col('Tip_amount') > 0)` zwraca nowego przepływu danych z wierszami, w którym wartość `Tip_amount` jest większa niż 0.

> [!NOTE] 
> `Tip_amount` jest najpierw konwertowany na liczbowy, co pozwala utworzyć wyrażenie Porównanie innych wartości liczbowych.

```
dataflow = dataflow.to_number(['Tip_amount'])
dataflow = dataflow.filter(dprep.col('Tip_amount') > 0)
dataflow.head(5)
```
||lpep_pickup_datetime|Lpep_dropoff_datetime|Passenger_count|Trip_distance|Tip_amount|Tolls_amount|Total_amount|
|-----|-----|-----|-----|-----|-----|-----|-----|
|0|2013-08-01-19:33:28|2013-08-01-19:35:21|5|.00|0,08|0|4.58|
|1|2013-08-05 13:16:38|2013-08-05 13:18:24|1|.00|0.30|0|3.8|
|2|2013-08-05 14:11:42|2013-08-05 14:12:47|1|.00|1,05|0|4.55|
|3|2013-08-05 14:15:56|2013-08-05 14:18:04|5|.00|2.22|0|5,72|
|4|2013-08-05 14:42:14|2013-08-05 14:42:38|1|.00|0.88|0|4.38|

### <a name="filtering-rows-with-complex-expressions"></a>Filtrowanie wierszy za pomocą wyrażenia złożone

Aby filtrować przy użyciu wyrażeń złożonych, wyrażeń co najmniej jeden prostego z konstruktorów wyrażeń `f_not`, `f_and`, lub `f_or`.

W tym przykładzie `Dataflow.filter` zwraca nowego przepływu danych z wierszami gdzie `'Passenger_count'` jest mniejsza niż 5 i `'Tolls_amount'` jest większa niż 0.

```
dataflow = dataflow.to_number(['Passenger_count', 'Tolls_amount'])
dataflow = dataflow.filter(dprep.f_and(dprep.col('Passenger_count') < 5, dprep.col('Tolls_amount') > 0))
dataflow.head(5)
```
||lpep_pickup_datetime|Lpep_dropoff_datetime|Passenger_count|Trip_distance|Tip_amount|Tolls_amount|Total_amount|
|-----|-----|-----|-----|-----|-----|-----|-----|
|0|2013-08-08 12:16:00|2013-08-08 12:16:00|1.0|.00|2.25|5.00|19.75|
|1|2013-08-12 14:43:53|2013-08-12: 15:04:50|1.0|5.28|6.46|5.33|32.29|
|2|2013-08-12 19:48:12|2013-08-12 20:03:42|1.0|5.50|1.00|10.66|30.66|
|3|2013-08-13 06:11:06|2013-08-13 06:30:28|1.0|9,57|7.47|5.33|44,8|
|4|2013-08-16 20:33:50|2013-08-16 20:48:50|1.0|5.63|3.00|5.33|27.83|

Istnieje również możliwość filtrowanie wierszy, łącząc więcej niż jeden Konstruktor wyrażeń, aby utworzyć wyrażenie zagnieżdżonych.

> [!NOTE]
> `lpep_pickup_datetime` i `Lpep_dropoff_datetime` są najpierw konwertowany na daty i godziny, który pozwala utworzyć wyrażenie porównanie inne wartości daty/godziny.

```
dataflow = dataflow.to_datetime(['lpep_pickup_datetime', 'Lpep_dropoff_datetime'], ['%Y-%m-%d %H:%M:%S'])
dataflow = dataflow.to_number(['Total_amount', 'Trip_distance'])
mid_2013 = datetime(2013,7,1)
dataflow = dataflow.filter(
    dprep.f_and(
        dprep.f_or(
            dprep.col('lpep_pickup_datetime') > mid_2013,
            dprep.col('Lpep_dropoff_datetime') > mid_2013),
        dprep.f_and(
            dprep.col('Total_amount') > 40,
            dprep.col('Trip_distance') < 10)))
dataflow.head(5)
```

||lpep_pickup_datetime|Lpep_dropoff_datetime|Passenger_count|Trip_distance|Tip_amount|Tolls_amount|Total_amount|
|-----|-----|-----|-----|-----|-----|-----|-----|
|0|2013-08-13 06:11:06 + 00:00|2013-08-13 06:30:28 + 00:00|1.0|9,57|7.47|5.33|44.80|
|1|2013-08-23 12:28:20 + 00:00|2013-08-23 12:50:28 + 00:00|2.0|8.22|8.08|5.33|40.41|
|2|25-08-2013 r. 09:12:52 + 00:00|25-08-2013 r. 09:34:34 + 00:00|1.0|8.80|8.33|5.33|41.66|
|3|2013-08-25 16:46:51 + 00:00|2013-08-25 17:13:55 + 00:00|2.0|9.66|7.37|5.33|44.20|
|4|2013-08-25 17:42:11 + 00:00|2013-08-25 18:02:57 + 00:00|1.0|9.60|6.87|5.33|41.20|

## <a name="custom-python-transforms"></a>Niestandardowe przekształcenia języka Python 

Scenariusze będzie istnieć, gdy najłatwiejszym, należy wykonać, jest napisanie kodu języka Python. Zestaw SDK udostępnia trzy punkty rozszerzenia, których można użyć.

- Nowa kolumna skryptu
- Nowy filtr skryptu
- Przekształcanie partycji

Każdego rozszerzenia jest obsługiwana w runtime skalowanie w górę i skalowania w poziomie. Zaletą używania tych punktów rozszerzenia jest, nie należy ściągnąć wszystkie dane, aby można było utworzyć ramkę danych. Twoje niestandardowe języka Python, którego kod będzie działał tylko, takich jak innych transformacji, w dużej skali, przez partycję i zazwyczaj równolegle.

### <a name="initial-data-preparation"></a>Przygotowanie danych początkowych

Rozpocznij od ładowania niektórych danych z obiektów Blob platformy Azure.

```
import azureml.dataprep as dprep
col = dprep.col

df = dprep.read_csv(path='https://dpreptestfiles.blob.core.windows.net/testfiles/read_csv_duplicate_headers.csv', skip_rows=1)
df.head(5)
```
| |stnam|fipst|leaid|leanm10|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|-----|------|-----|
|0|ALABAMA|1|101710|Hale hrabstwa|10171002158| |
|1|ALABAMA|1|101710|Hale hrabstwa|10171002162| |
|2|ALABAMA|1|101710|Hale hrabstwa|10171002156| |
|3|ALABAMA|1|101710|Hale hrabstwa|10171000588|2|
|4|ALABAMA|1|101710|Hale hrabstwa|10171000589| |

Ogranicz szczegółów zestawu danych, a następnie wykonaj niektóre podstawowe przekształcenia.

```
df = df.keep_columns(['stnam', 'leanm10', 'ncessch', 'MAM_MTH00numvalid_1011'])
df = df.replace_na(columns=['leanm10', 'MAM_MTH00numvalid_1011'], custom_na_list='.')
df = df.to_number(['ncessch', 'MAM_MTH00numvalid_1011'])
df.head(5)
```
| |stnam|leanm10|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|-----|
|0|ALABAMA|Hale hrabstwa|1.017100e + 10|Brak|
|1|ALABAMA|Hale hrabstwa|1.017100e + 10|Brak|
|2|ALABAMA|Hale hrabstwa|1.017100e + 10|Brak|
|3|ALABAMA|Hale hrabstwa|1.017100e + 10|2|
|4|ALABAMA|Hale hrabstwa|1.017100e + 10|Brak|

Zwróć uwagę na wartości null przy użyciu filtru. Będzie znajdę coś, więc teraz wypełnić te brakujące wartości.

```
df.filter(col('MAM_MTH00numvalid_1011').is_null()).head(5)
```

| |stnam|leanm10|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|-----|
|0|ALABAMA|Hale hrabstwa|1.017100e + 10|Brak|
|1|ALABAMA|Hale hrabstwa|1.017100e + 10|Brak|
|2|ALABAMA|Hale hrabstwa|1.017100e + 10|Brak|
|3|ALABAMA|Hale hrabstwa|1.017100e + 10|Brak|
|4|ALABAMA|Hale hrabstwa|1.017100e + 10|Brak|

### <a name="transform-partition"></a>Przekształcanie partycji

Funkcja przydatna pandas umożliwia Zamień wszystkie wartości null na wartość 0. Ten kod jest uruchamiane przez partycję, nie wszystkie zestawu danych w danym momencie. Oznacza to, że na duży zestaw danych, ten kod mogą być wykonywane równolegle jako środowisko wykonawcze przetwarza dane, partycji według partycji.

```
df = df.transform_partition("""
def transform(df, index):
    df['MAM_MTH00numvalid_1011'].fillna(0,inplace=True)
    return df
""")
h = df.head(5)
h
```
||stnam|leanm10|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|-----|
|0|ALABAMA|Hale hrabstwa|1.017100e + 10|0.0|
|1|ALABAMA|Hale hrabstwa|1.017100e + 10|0.0|
|2|ALABAMA|Hale hrabstwa|1.017100e + 10|0.0|
|3|ALABAMA|Hale hrabstwa|1.017100e + 10|2.0|
|4|ALABAMA|Hale hrabstwa|1.017100e + 10|0.0|

### <a name="new-script-column"></a>Nowa kolumna skryptu

Utwórz nową kolumnę, która ma tę nazwę i nazwę stanu, a także korzystaj Nazwa stanu, można użyć kodu w języku Python. Aby to zrobić, należy użyć `new_script_column()` metody na przepływ danych.

```
df = df.new_script_column(new_column_name='county_state', insert_after='leanm10', script="""
def newvalue(row):
    return row['leanm10'] + ', ' + row['stnam'].title()
""")
h = df.head(5)
h
```
||stnam|leanm10|county_state|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|-----|
|0|ALABAMA|Hale hrabstwa|Hale hrabstwa, Alabama|1.017100e + 10|0.0|
|1|ALABAMA|Hale hrabstwa|Hale hrabstwa, Alabama|1.017100e + 10|0.0|
|2|ALABAMA|Hale hrabstwa|Hale hrabstwa, Alabama|1.017100e + 10|0.0|
|3|ALABAMA|Hale hrabstwa|Hale hrabstwa, Alabama|1.017100e + 10|2.0|
|4|ALABAMA|Hale hrabstwa|Hale hrabstwa, Alabama|1.017100e + 10|0.0|
### <a name="new-script-filter"></a>Nowy filtr skryptu

Teraz, tworzyć wyrażenia języka Python do filtrowania zestawu danych, aby tylko wiersze, w którym "Hale", nie znajduje się w nowym `county_state` kolumny. Wyrażenie zwraca `True` Jeśli chcemy zachować wiersza, i `False` można usunąć wiersza.

```
df = df.new_script_filter("""
def includerow(row):
    val = row['county_state']
    return 'Hale' not in val
""")
h = df.head(5)
h
```

||stnam|leanm10|county_state|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|-----|
|0|ALABAMA|Jefferson hrabstwa|Jefferson hrabstwa, Alabama|1.019200e + 10|1.0|
|1|ALABAMA|Jefferson hrabstwa|Jefferson hrabstwa, Alabama|1.019200e + 10|0.0|
|2|ALABAMA|Jefferson hrabstwa|Jefferson hrabstwa, Alabama|1.019200e + 10|0.0|
|3|ALABAMA|Jefferson hrabstwa|Jefferson hrabstwa, Alabama|1.019200e + 10|0.0|
|4|ALABAMA|Jefferson hrabstwa|Jefferson hrabstwa, Alabama|1.019200e + 10|0.0|
