---
title: Eksploruj i przekształcania danych (klasę zestawu danych)
titleSuffix: Azure Machine Learning service
description: Eksplorowanie danych za pomocą statystyk podsumowujących i przygotować dane za pomocą czyszczenia danych, przekształcania i technicznego opracowywania funkcji
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 05/23/2019
ms.openlocfilehash: 80137c7f1ecebab4d2da0c4b7ba0ca9292dad22e
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67443964"
---
# <a name="explore-and-prepare-data-with-the-dataset-class-preview"></a>Eksploruj i przygotować dane za pomocą klasy zestawu danych (wersja zapoznawcza)

Dowiedz się, jak eksplorować i przygotować dane za pomocą pakietu zestawów danych usługi Azure ml w [zestawu SDK usługi Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py). [Dataset](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py) klasy (wersja zapoznawcza) umożliwia Eksplorowanie i przygotuj dane, udostępniając funkcje takie jak: próbkowania, statystyki podsumowujące i inteligentne przekształcenia. Kroki przekształcania są zapisywane w [definicji zestawu danych](how-to-manage-dataset-definitions.md) z możliwością obsługi wielu dużych plików z różnymi schematami w sposób o wysokim stopniu skalowalności.

> [!Important]
> Niektóre klasy zestawu danych (wersja zapoznawcza) być zależny od [przygotowania danych usługi Azure ml](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py) pakietu (GA). Gdy funkcji transformacji może odbywać się bezpośrednio z GA'ed [funkcji Data Prep](how-to-transform-data.md), firma Microsoft zaleca otoki pakiet zestawu danych, które są opisane w tym artykule, jeśli tworzysz nowe rozwiązanie. Zestawy Azure Machine Learning danych (wersja zapoznawcza) umożliwiają nie tylko przekształcać dane, ale także [migawki danych](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_snapshot.datasetsnapshot?view=azure-ml-py) i przechowywać [definicje wersji zestawów danych](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset?view=azure-ml-py). Zestawy danych jest następnej wersji zestawu SDK przygotowania bazy danych, oferując rozszerzone funkcje do zarządzania zestawami danych w rozwiązań sztucznej Inteligencji.

## <a name="prerequisites"></a>Wymagania wstępne

Aby eksplorować i przygotuj dane, będą potrzebne:

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji Azure, przed rozpoczęciem utwórz bezpłatne konto. Wypróbuj [bezpłatną lub płatną wersję usługi Azure Machine Learning](https://aka.ms/AMLFree) już dziś.

* Obszar roboczy usługi Azure Machine Learning. Zobacz [Utwórz obszar roboczy usługi Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/setup-create-workspace).

* Zestaw Azure Machine Learning SDK for Python (wersji 1.0.21 lub nowszej), który zawiera pakiet zestawów danych usługi Azure ml. Aby zainstalować lub aktualizacji do najnowszej wersji zestawu SDK, zobacz [Zainstaluj lub zaktualizuj zestaw SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).

* Usługi Azure Machine Learning przygotowywanie danych zestawu SDK. Aby zainstalować lub aktualizacji do najnowszej wersji, zobacz [Zainstaluj lub zaktualizuj zestaw SDK usługi Data Prep](https://docs.microsoft.com/python/api/overview/azure/dataprep/intro?view=azure-dataprep-py#install).

* Pobieranie plików przykładowych, aby skorzystać z przykładów: [crime.csv](https://dprepdata.blob.core.windows.net/dataset-sample-files/crime.csv) i [city.json](https://dprepdata.blob.core.windows.net/dataset-sample-files/city.json).

## <a name="sampling"></a>Próbkowanie

Pobrać próbkę danych zawierają opis początkowego architektury danych i zawartości. W tej chwili [ `sample()` ](https://docs.microsoft.com//python/api/azureml-core/azureml.core.dataset(class)?view=azure-ml-py#sample-sample-strategy--arguments-) metody z klasy zestawu danych obsługuje górne N proste losowych i Stratified strategie.

```Python
from azureml.core.dataset import Dataset
import random

# create an in-memory Dataset from a local file
dataset = Dataset.auto_read_files('./data/crime.csv')

# create seed for Simple Random and Stratified sampling
seed = random.randint(0, 4294967295)
```

### <a name="top-n-sample"></a>Przykład pierwszych N

W przypadku próbkowania górne N pierwszych n rekordów zestawu danych są Twojego przykładu. Jest to przydatne, jeśli po prostu chcesz poznać jakie wygląd rekordów danych takich jak lub, aby zobaczyć, jakie pola danych.

```Python
top_n_sample_dataset = dataset.sample('top_n', {'n': 5})
top_n_sample_dataset.to_pandas_dataframe()
```

||id|Liczba przypadków|Date|Blokuj|IUCR|Typ podstawowy|Przyciski ...|
-|--|-----------|----|-----|----|------------|---
0|10498554|HZ239907|4/4/2016 23:56|007XX E 111TH ST|1153|PRAKTYKI OSZUKAŃCZYM|Przyciski ...
1|10516598|HZ258664|4/15/2016 17:00|082XX S MARSHFIELD AVE|890|PRZED KRADZIEŻĄ|Przyciski ...
2|10519196|HZ261252|4/15/2016 10:00|104XX S SACRAMENTO AVE|1154|PRAKTYKI OSZUKAŃCZYM|Przyciski ...
3|10519591|HZ261534|4/15/2016 9:00|113XX APISZ PIESKÓW|1120|PRAKTYKI OSZUKAŃCZYM|Przyciski ...
4|10534446|HZ277630|4/15/2016 10:00|055XX N KEDZIE AVE|890|PRZED KRADZIEŻĄ|Przyciski ...

### <a name="simple-random-sample"></a>Proste losowej próbki

W prostych losowego próbkowanie każdy członek wypełnianie danymi ma równe szanse na jest wybrany jako części przykładu. W `simple_random` strategia próbki są zaznaczone w oparciu o prawdopodobieństwo, że określona rekordów z zestawu danych i zwraca zmodyfikowane zestawu danych. Inicjator parametru jest opcjonalne.

```Python
simple_random_sample_dataset = dataset.sample('simple_random', {'probability':0.3, 'seed': seed})
simple_random_sample_dataset.to_pandas_dataframe()
```

||id|Liczba przypadków|Date|Blokuj|IUCR|Typ podstawowy|Przyciski ...|
-|--|-----------|----|-----|----|------------|---
0|10516598|HZ258664|4/15/2016 17:00|082XX S MARSHFIELD AVE|890|PRZED KRADZIEŻĄ|Przyciski ...
1|10519196|HZ261252|4/15/2016 10:00|104XX S SACRAMENTO AVE|1154|PRAKTYKI OSZUKAŃCZYM|Przyciski ...
2|10534446|HZ277630|4/15/2016 10:00|055XX N KEDZIE AVE|890|PRZED KRADZIEŻĄ|Przyciski ...

### <a name="stratified-sample"></a>Przykładowe stratyfikowana

Przykłady stratyfikowana upewnij się, że pewnych grup populacji są reprezentowane w próbce. W `stratified` strategia próbki populacji jest podzielony na strata lub podgrupy, w oparciu o podobieństw, i rekordy są wybierane losowo spośród każdego strata według wagi strata wskazywanym przez `fractions` parametru.

W poniższym przykładzie mamy grupie Każdy rekord według określonych kolumn i obejmują wspomniane rekord na podstawie informacji wagi strata X w `fractions`. Jeśli rekord nie mogą być grupowane strata nie zostanie określony, domyślna grubość próbki wynosi 0.

```Python
# take 50% of records with `Primary Type` as `THEFT` and 20% of records with `Primary Type` as `DECEPTIVE PRACTICE` into sample Dataset
fractions = {}
fractions[('THEFT',)] = 0.5
fractions[('DECEPTIVE PRACTICE',)] = 0.2

sample_dataset = dataset.sample('stratified', {'columns': ['Primary Type'], 'fractions': fractions, 'seed': seed})

sample_dataset.to_pandas_dataframe()
```

||id|Liczba przypadków|Date|Blokuj|IUCR|Typ podstawowy|Przyciski ...|
-|--|-----------|----|-----|----|------------|---
0|10516598|HZ258664|4/15/2016 17:00|082XX S MARSHFIELD AVE|890|PRZED KRADZIEŻĄ|Przyciski ...
1|10534446|HZ277630|4/15/2016 10:00|055XX N KEDZIE AVE|890|PRZED KRADZIEŻĄ|Przyciski ...
2|10535059|HZ278872|4/15/2016 4:30|004XX APISZ KILBOURN|810|PRZED KRADZIEŻĄ|Przyciski ...

## <a name="explore-with-summary-statistics"></a>Zapoznaj się ze statystykami podsumowania

 Wykrywaj anomalie, brakujące wartości, lub błąd liczniki z [ `get_profile()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#get-profile-arguments-none--generate-if-not-exist-true--workspace-none--compute-target-none-) metody. Ta funkcja pobiera profil i statystyki podsumowujące dane, co z kolei pomaga ustalić operacji przygotowania danych niezbędnych do wykonania.

```Python
dataset.get_profile()
```

||Typ|Min.|Maks.|Liczba|Liczba brakujących|Liczba niebrakujących|Odsetek brakujących|Liczba błędów|Liczba pustych|Kwantyl 0,1%|Kwantyl 1%|Kwantyl 5%|Kwantyl 25%|Kwantyl 50%|Kwantyl 75%|Kwantyl 95%|Kwantyl 99%|Kwantyl 99,9%|Średnia|Odchylenie standardowe|Wariancja|Skośność|Kurtoza
-|----|---|---|-----|-------------|-----------------|---------------|-----------|-----------|-------------|-----------|-----------|------------|------------|------------|------------|------------|--------------|----|------------------|--------|--------|--------
id|FieldType.INTEGER|1.04986e + 07|1.05351e + 07|10.0|0.0|10.0|0.0|0.0|0.0|1.04986e + 07|1.04992e + 07|1.04986e + 07|1.05166e + 07|1.05209e + 07|1.05259e + 07|1.05351e + 07|1.05351e + 07|1.05351e + 07|1.05195e + 07|12302.7|1.51358e + 08|-0.495701|-1.02814
Liczba przypadków|FieldType.STRING|HZ239907|HZ278872|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
Date|FieldType.DATE|2016-04-04 23:56:00+00:00|2016-04-15 17:00:00+00:00|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
Blokuj|FieldType.STRING|004XX APISZ KILBOURN|113XX APISZ PIESKÓW|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
IUCR|FieldType.INTEGER|810|1154|10.0|0.0|10.0|0.0|0.0|0.0|810|850|810|890|1136|1153|1154|1154|1154|1058.5|137.285|18847.2|-0.785501|-1.3543
Typ podstawowy|FieldType.STRING|PRAKTYKI OSZUKAŃCZYM|PRZED KRADZIEŻĄ|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
Opis|FieldType.STRING|SFAŁSZOWANY WYBORU|ZA POŚREDNICTWEM 500 USD|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
Opis lokalizacji|FieldType.STRING||SZKOŁY, PUBLICZNEJ I TWORZENIE|10.0|0.0|10.0|0.0|0.0|1.0||||||||||||||
Aresztowania|FieldType.BOOLEAN|False|False|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
Krajowych|FieldType.BOOLEAN|False|False|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
Sygnał|FieldType.INTEGER|531|2433|10.0|0.0|10.0|0.0|0.0|0.0|531|531|531|614|1318.5|1911|2433|2433|2433|1371.1|692.094|478994|0.105418|-1.60684
Dystrykt|FieldType.INTEGER|5|24|10.0|0.0|10.0|0.0|0.0|0.0|5|5|5|6|13|19|24|24|24|13.5|6.94822|48.2778|0.0930109|-1.62325
Lej|FieldType.INTEGER|1|48|10.0|0.0|10.0|0.0|0.0|0.0|1|5|1|9|22.5|40|48|48|48|24.5|16.2635|264.5|0.173723|-1.51271
Obszar społeczności|FieldType.INTEGER|4|77|10.0|0.0|10.0|0.0|0.0|0.0|4|8.5|4|24|37.5|71|77|77|77|41.2|26.6366|709.511|0.112157|-1.73379
FBI kodu|FieldType.INTEGER|6|11|10.0|0.0|10.0|0.0|0.0|0.0|6|6|6|6|11|11|11|11|11|9.4|2.36643|5.6|-0.702685|-1.59582
Współrzędna x|FieldType.INTEGER|1.16309e + 06|1.18336e + 06|10.0|7.0|3.0|0.7|0.0|0.0|1.16309e + 06|1.16309e + 06|1.16309e + 06|1.16401e + 06|1.16678e + 06|1.17921e+06|1.18336e + 06|1.18336e + 06|1.18336e + 06|1.17108e + 06|10793.5|1.165e + 08|0.335126|-2.33333
Współrzędna Y|FieldType.INTEGER|1.8315e + 06|1.908e + 06|10.0|7.0|3.0|0.7|0.0|0.0|1.8315e + 06|1.8315e + 06|1.8315e + 06|1.83614e + 06|1.85005e + 06|1.89352e + 06|1.908e + 06|1.908e + 06|1.908e + 06|1.86319e + 06|39905.2|1.59243e + 09|0.293465|-2.33333
Rok|FieldType.INTEGER|2016|2016|10.0|0.0|10.0|0.0|0.0|0.0|2016|2016|2016|2016|2016|2016|2016|2016|2016|2016|0|0|NaN|NaN
Aktualizacja:|FieldType.DATE|2016-05-11 15:48:00+00:00|2016-05-27 15:45:00+00:00|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
Szerokość geograficzna|FieldType.DECIMAL|41.6928|41.9032|10.0|7.0|3.0|0.7|0.0|0.0|41.6928|41.6928|41.6928|41.7057|41.7441|41.8634|41.9032|41.9032|41.9032|41.78|0.109695|0.012033|0.292478|-2.33333
Długość geograficzna|FieldType.DECIMAL|-87.6764|-87.6043|10.0|7.0|3.0|0.7|0.0|0.0|-87.6764|-87.6764|-87.6764|-87.6734|-87.6645|-87.6194|-87.6043|-87.6043|-87.6043|-87.6484|0.0386264|0.001492|0.344429|-2.33333
Location|FieldType.STRING||(41.903206037, -87.676361925)|10.0|0.0|10.0|0.0|0.0|7.0||||||||||||||

## <a name="impute-missing-values"></a>Naliczenie brakujące wartości

W zestawach danych wartości null, NaN firmy i wartości, które mają zawartość, nie są traktowane jako brakujące wartości. To ma wpływ na wydajność modeli usługi machine learning lub spowodować nieprawidłowe wnioski. Przypisywanie jest typową metodą adresów brakujące wartości i jest przydatne, gdy użytkownik ma wysoki odsetek brakujących wartości rekordy, które po prostu nie można usunąć.

Z profilu zestawu danych, wygenerowane w poprzedniej sekcji, widzimy, że `Latitude` i `Longitude` kolumny mają wysoki odsetek brakujących wartości. W tym przykładzie możemy obliczyć średnią i przypisują brakujących wartości dla tych dwóch kolumn.

Najpierw pobierz najnowsze definicje zestawu danych za pomocą [ `get_definition()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#get-definition-version-id-none-) i okrojenia danych za pomocą [ `keep_columns()` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow#keep-columns-columns--multicolumnselection--validate-column-exists--bool---false-----azureml-dataprep-api-dataflow-dataflow), dzięki czemu możemy jedynie wyświetlać kolumn chcemy adresu.

```Python
from azureml.core.dataset import Dataset
import azureml.dataprep as dprep

# get the latest definition of Dataset
ds_def = dataset.get_definition()

# keep useful columns for this example
ds_def = ds_def.keep_columns(['ID', 'Arrest', 'Latitude', 'Longitude'])
ds_def.head(3)
```

||id|Aresztowania| Szerokość geograficzna|Długość geograficzna|
-|---------|-----|---------|----------|
|0|10498554|False|41.692834|-87.604319|
|1|10516598|False| 41.744107 |-87.664494|
|2|10519196|False| NaN|NaN|

Następnie sprawdź `MEAN` wartość szerokości kolumn za pomocą [ `summarize()` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#summarize-summary-columns--typing-union-typing-list-azureml-dataprep-api-dataflow-summarycolumnsvalue---nonetype----none--group-by-columns--typing-union-typing-list-str---nonetype----none--join-back--bool---false--join-back-columns-prefix--typing-union-str--nonetype----none-----azureml-dataprep-api-dataflow-dataflow) funkcji. Ta funkcja akceptuje tablica kolumn w `group_by_columns` parametru do określenia poziomu agregacji. `summary_columns` Akceptuje parametr `SummaryColumnsValue` funkcji, która określa bieżący nazwa kolumny, Nowa nazwa pola obliczeniowego i `SummaryFunction` do wykonania.

```Python
lat_mean = ds_def.summarize(group_by_columns = ['Arrest'],
                            summary_columns = [dprep.SummaryColumnsValue(column_id = 'Latitude',
                                                                         summary_column_name = 'Latitude_MEAN',
                                                                         summary_function = dprep.SummaryFunction.MEAN)])
lat_mean = lat_mean.filter(lat_mean['Arrest'] == False)
lat_mean.head(1)
```

||Aresztowania|Latitude_MEAN|
--|-----|--------|
|0|False|41.780049|

Po możemy sprawdzić wartości, które mają przypisują, użyj [ `ImputeMissingValuesBuilder` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.api.builders.imputemissingvaluesbuilder?view=azure-dataprep-py) się wyrażenie stałej, które imputes kolumny albo obliczeniowego `MIN`, `MAX`, `MEAN` wartość lub `CUSTOM` wartość. Gdy `group_by_columns` jest określony, brakujące wartości zostaną kalkulacyjnych przez grupę przy użyciu `MIN`, `MAX`, i `MEAN` obliczane dla każdej grupy.

[ `ImputeColumnArguments` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.api.builders.imputecolumnarguments?view=azure-dataprep-pyfunction) Akceptuje ciąg column_id i `ReplaceValueFunction` do określania typu impute. Brak wartości szerokości geograficznej przypisują go z-87 na podstawie wiedzy zewnętrznych.

```Python
# impute with MEAN
impute_mean = dprep.ImputeColumnArguments(column_id = 'Latitude',
                                          impute_function = dprep.ReplaceValueFunction.MEAN)

# impute with custom value -87
impute_custom = dprep.ImputeColumnArguments(column_id='Longitude',
                                            custom_impute_value=-87)
```

Naliczenie kroki można łączyć w łańcuch do `ImputeMissingValuesBuilder` przy użyciu [ `Builders` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.api.builders.builders?view=azure-dataprep-py) klasy funkcji [ `impute_missing_values()` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.api.builders.builders?view=azure-dataprep-py#impute-missing-values-impute-columns--typing-list-azureml-dataprep-api-builders-imputecolumnarguments----none--group-by-columns--typing-union-typing-list-str---nonetype----none-----azureml-dataprep-api-builders-imputemissingvaluesbuilder). `impute_columns` Parametr przyjmuje tablicę `ImputeColumnArguments` obiektów.

```Python
# get instance of ImputeMissingValuesBuilder
impute_builder = ds_def.builders.impute_missing_values(impute_columns=[impute_mean, impute_custom],
                                                   group_by_columns=['Arrest'])
```

Wywołaj [ `learn()` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.api.builders.imputemissingvaluesbuilder?view=azure-dataprep-py#learn------none) funkcji do przechowywania kroki impute i zastosować je do obiektu przepływu danych przy użyciu [ `to_dataflow()` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.api.builders.imputemissingvaluesbuilder?view=azure-dataprep-py#to-dataflow------azureml-dataprep-api-dataflow-dataflow).

```Python
impute_builder.learn()
ds_def = impute_builder.to_dataflow()
ds_def.head(3)
```

Jak pokazano w poniższej tabeli danych wyjściowych, brak szerokość była kalkulacyjne z `MEAN` wartość `Arrest==False` grupy i Brak długości geograficznej został kalkulacyjne z-87.

||id|Aresztowania|Szerokość geograficzna|Długość geograficzna
-|---------|-----|---------|----------
0|10498554|False|41.692834|-87.604319
1|10516598|False|41.744107|-87.664494
2|10519196|False|41.780049|-87.000000

Aktualizowanie definicji zestawu danych, [ `update_definition()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset(class)?view=azure-ml-py#update-definition-definition--definition-update-message-) zapewnienie kroków wykonywanych transformacji.

```Python
dataset = dataset.update_definition(ds_def, 'Impute Missing')
dataset.head(3)
```

||id|Aresztowania|Szerokość geograficzna|Długość geograficzna
-|---------|-----|---------|----------
0|10498554|False|41.692834|-87.604319
1|10516598|False|41.744107|-87.664494
2|10519196|False|41.780049|-87.000000

## <a name="create-assertion-rules"></a>Tworzenie reguł asercji

Często dane będziemy pracować z podczas czyszczenia i przygotowywania danych jest tylko podzbiór łączna ilość danych, których potrzebujemy w środowisku produkcyjnym. W rezultacie niektóre założenia, którą udostępnimy w ramach naszych czyszczenia może stają się wartość false. Na przykład w zestawie danych, która stale aktualizuje, kolumny, która początkowo zawierała tylko cyfry, w pewnym zakresie może zawierać szerszy zakres wartości nowszego wykonania. Te błędy często skutkuje potoki uszkodzone lub nieprawidłowe dane.

Obsługa zestawów danych, tworzenie potwierdzenia na danych, które są oceniane, jak potok wykonuje. Te potwierdzenia Pozwól nam zweryfikować, że nasze założeń dotyczących danych w dalszym ciągu być dokładne i, jeśli ta wartość nie, aby odpowiednio obsługiwać błędy.

Na przykład, jeśli chcesz ograniczyć `Latitude` i `Longitude` wartości w zestawie danych do określonych zakresów liczbowych, [ `assert_value()` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#assert-value-columns--multicolumnselection--expression--azureml-dataprep-api-expressions-expression--policy--azureml-dataprep-api-engineapi-typedefinitions-assertpolicy----assertpolicy-errorvalue--1---error-code--str----assertionfailed------azureml-dataprep-api-dataflow-dataflow) metoda gwarantuje zawsze jest to wymagane.

```Python
from azureml.dataprep import value
from azureml.core.dataset import Dataset

# get the latest definition of the Dataset
ds_def = dataset.get_definition()

# set assertion rules for `Latitude` and `Longitude` columns
ds_def = ds_def.assert_value('Latitude', (value <= 90) & (value >= -90), error_code='InvalidLatitude')
ds_def = ds_def.assert_value('Longitude', (value <= 180) & (value >= -87), error_code='InvalidLongitude')

ds_def.get_profile()
```

||Typ|Min.|Maks.|Liczba|Liczba brakujących|Liczba niebrakujących|Odsetek brakujących|Liczba błędów|Liczba pustych|Kwantyl 0,1%|Kwantyl 1%|Kwantyl 5%|Kwantyl 25%|Kwantyl 50%|Kwantyl 75%|Kwantyl 95%|Kwantyl 99%|Kwantyl 99,9%|Średnia|Odchylenie standardowe|Wariancja|Skośność|Kurtoza
-|----|---|---|-----|-------------|-----------------|---------------|-----------|-----------|-------------|-----------|-----------|------------|------------|------------|------------|------------|--------------|----|------------------|--------|--------|--------
id|FieldType.INTEGER|1.04986e + 07|1.05351e + 07|10.0|0.0|10.0|0.0|0.0|0.0|1.04986e + 07|1.04992e + 07|1.04986e + 07|1.05166e + 07|1.05209e + 07|1.05259e + 07|1.05351e + 07|1.05351e + 07|1.05351e + 07|1.05195e + 07|12302.7|1.51358e + 08|-0.495701|-1.02814
Aresztowania|FieldType.BOOLEAN|False|False|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
Szerokość geograficzna|FieldType.DECIMAL|41.6928|41.9032|10.0|0.0|10.0|0.0|0.0|0.0|41.6928|41.7185|41.6928|41.78|41.78|41.78|41.9032|41.9032|41.9032|41.78|0.0517107|0.002674|0.837593|1,05
Długość geograficzna|FieldType.INTEGER|-87|-87|10.0|0.0|10.0|0.0|3.0|0.0|-87|-87|-87|-87|-87|-87|-87|-87|-87|-87|0|0|NaN|NaN

Z profilu, zobaczysz, że `Error Count` dla `Longitude` kolumny to 3. Poniższy kod filtruje zestaw danych pobiera błąd i widzi, co powoduje, że asercja nie powiedzie się. W tym miejscu należy dostosować swój kod i odpowiednio czyszczenia danych.

```Python
from azureml.dataprep import col

ds_error = ds_def.filter(col('Longitude').is_error())
error = ds_error.head(10)['Longitude'][0]

print(error.originalValue)
```

    -87.60431945

## <a name="derive-columns-by-example"></a>Pochodzi kolumn według przykładu

Jednym z bardziej zaawansowanych narzędzi dla zestawów danych jest możliwość uzyskiwania kolumn przy użyciu przykładów zakładanych wyników. Dzięki temu można podać przykład, zestawu SDK, więc może generować kod w celu osiągnięcia zamierzonego przekształcenia.

```Python
from azureml.core.dataset import Dataset

# create an in-memory Dataset from a local file
dataset = Dataset.auto_read_files('./data/crime.csv')
dataset.head(3)
```

||id|Liczba przypadków|Date|Blokuj|Przyciski ...|
-|---------|-----|---------|----|---
0|10498554|HZ239907|2016-04-04 23:56:00|007XX E 111TH ST|Przyciski ...
1|10516598|HZ258664|2016-04-15 17:00:00|082XX S MARSHFIELD AVE|Przyciski ...
2|10519196|HZ261252|2016-04-15 10:00:00|104XX S SACRAMENTO AVE|Przyciski ...

Powiedz, przekształcić na format daty i godziny na "2016-04-04 22: 00 - 00: 00". W [ `derive_column_by_example()` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#derive-column-by-example-source-columns--sourcecolumns--new-column-name--str--example-data--exampledata-----azureml-dataprep-api-dataflow-dataflow) argument, zawierają przykłady żądane dane wyjściowe w `example_data` parametr w następującym formacie: *(oryginalne dane wyjściowe, żądany element wyjściowy)* .

Poniższy kod zawiera dwa przykłady żądanego wyniku, ("2016--04 04 23:56:00", "2016--04 04 10 PM-12 AM") i ("2016-04-15 17:00:00", "2016-04-15, 16: 00 - 18: 00")

```Python
ds_def = dataset.get_definition()
ds_def = ds_def.derive_column_by_example(
        source_columns = "Date",
        new_column_name = "Date_Time_Range",
        example_data = [("2016-04-04 23:56:00", "2016-04-04 10PM-12AM"), ("2016-04-15 17:00:00", "2016-04-15 4PM-6PM")]
    )
ds_def.keep_columns(['ID','Date','Date_Time_Range']).head(3)
```

W poniższej tabeli należy zauważyć, że nową kolumnę Date_Time_Range zawiera rekordy w formacie określonym.

||id|Date|Date_Time_Range
-|--------|-----|----
0|10498554|2016-04-04 23:56:00|2016-04-04 10PM-12AM
1|10516598|2016-04-15 17:00:00|2016-04-15 4PM-6PM
2|10519196|2016-04-15 10:00:00|2016-04-15 10AM-12PM

```Python
# update Dataset definition to keep the transformation steps performed.
dataset = dataset.update_definition(ds_def, 'Derive Date_Time_Range')
```

## <a name="fuzzy-groupings"></a>Rozmyte grupowania

Podczas zbierania danych z różnych źródeł, mogą wystąpić różnice w pisowni, wielkość liter lub skróty w tej samej jednostki. Automatycznie standaryzację i uzgodnić tych wariantów przy użyciu zestawu SDK funkcji rozmyte grupowanie lub klastrowanie tekstu.

Na przykład kolumna `inspections.business.city` zawiera kilka postaci nazw city "San Francisco".

```Python
from azureml.core.dataset import Dataset

# create an in-memory Dataset from a local json file
dataset = Dataset.auto_read_files('./data/city.json')
dataset.head(5)
```

||inspections.business.business_id|inspections.business_name|inspections.business.address|inspections.business.city|Przyciski ...|
-|-----|-------------------------|------------|--|---
0|16162|Rupia Foods Ezee-Quick-N|3861 24th St|SF|Przyciski ...
1|67565|King Cafe klusek tajski|1541 TARAVAL St|SAN FRANCISCO|Przyciski ...
2|67565|King Cafe klusek tajski|1541 TARAVAL St|SAN FRANCISCO|Przyciski ...
3|68701|Grindz|832 clement St|SF|Przyciski ...
4|69186|Świadcząc usługi Premier & zdarzenia, Inc.|St 22 1255|S.F.|Przyciski ...

Użyjmy [ `fuzzy_group_column()` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#fuzzy-group-column-source-column--str--new-column-name--str--similarity-threshold--float---0-8--similarity-score-column-name--str---none-----azureml-dataprep-api-dataflow-dataflow) metodę, aby dodać kolumnę z automatycznie wykrytymi forma kanoniczna "San Francisco". Argumenty `source_column` i `new_column_name` są wymagane. Istnieje również możliwość:

* Utwórz nową kolumnę, `similarity_score_column_name`, pokazujący wynik podobieństwa między każdej pary wartości oryginalnego i canonical.

* Podaj `similarity_threshold`, który jest wynikiem minimalne podobieństwo do wartości, które mają być zgrupowane razem.

```Python
# get the latest Dataset definition
ds_def = dataset.get_definition()
ds_def = ds_def.fuzzy_group_column(source_column='inspections.business.city',
                                       new_column_name='city_grouped',
                                       similarity_threshold=0.8,
                                       similarity_score_column_name='similarity_score')
ds_def.head(5)
```

||inspections.business.business_id|inspections.business_name|inspections.business.address|inspections.business.city|city_grouped|similarity_score|Przyciski ...|
-|-----|-------------------------|------------|--|---|---|---
0|16162|Rupia Foods Ezee-Quick-N|3861 24th St|SF|San Francisco|0.814806|Przyciski ...
1|67565|King Cafe klusek tajski|1541 TARAVAL St|SAN FRANCISCO|San Francisco|1.000000|Przyciski ...
2|67565|King Cafe klusek tajski|1541 TARAVAL St|SAN FRANCISCO|San Francisco|1.000000|Przyciski ...
3|68701|Grindz|832 clement St|SF|San Francisco|0.814806|Przyciski ...
4|69186|Świadcząc usługi Premier & zdarzenia, Inc.|St 22 1255|S.F.|San Francisco|0.814806|Przyciski ...

Wynikowy definicji zestawu danych wszystkich możliwych kombinacji reprezentować "San Francisco" w danych był znormalizowany sam ciąg "San Francisco".

Zapisz ten krok rozmyte grupowania w najnowszych definicji zestawu danych za pomocą `update_definition()`.

```Python
dataset = dataset.update_definition(ds_def, 'fuzzy grouping')
```

## <a name="next-steps"></a>Kolejne kroki

* Zobacz uczenia maszynowego automatycznych [samouczek](tutorial-auto-train-models.md) przykład modelu regresji.

* Zobacz zestaw SDK [Przegląd](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) wzorców projektowych i przykłady użycia.

* Przykład przy użyciu zestawów danych, zobacz [przykładowy notesów](https://aka.ms/dataset-tutorial).
