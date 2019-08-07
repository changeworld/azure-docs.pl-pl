---
title: Eksplorowanie i Przekształcanie danych (klasa DataSet)
titleSuffix: Azure Machine Learning service
description: Eksplorowanie danych przy użyciu statystyk podsumowania oraz przygotowywanie danych przy użyciu funkcji czyszczenia, przekształcania i tworzenia danych
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 05/23/2019
ms.openlocfilehash: 31a367fcde909f393efa4fea65b25716f95c56ee
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/06/2019
ms.locfileid: "68828435"
---
# <a name="explore-and-prepare-data-with-the-dataset-class-preview"></a>Eksplorowanie i przygotowywanie danych za pomocą klasy DataSet (wersja zapoznawcza)

Dowiedz się, jak eksplorować i przygotowywać dane za pomocą pakietu Azure DataSets w [zestawie SDK Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py). Klasa [DataSet](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py) (wersja zapoznawcza) umożliwia eksplorowanie i przygotowywanie danych przez dostarczanie takich funkcji, jak: próbkowanie, podsumowanie statystyk i inteligentne przekształcenia. Kroki transformacji są zapisywane w [definicjach zestawu danych](how-to-manage-dataset-definitions.md) z możliwością obsługi wielu dużych plików różnych schematów w wysoce skalowalny sposób.

> [!Important]
> Niektóre klasy zestawu danych (wersja zapoznawcza) mają zależności w pakiecie [Azure-](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py) preprodukcyjnym (ga). Chociaż funkcje przekształcania można wykonywać bezpośrednio przy użyciu [funkcji przygotowywania danych](how-to-transform-data.md)GA'ed, zalecamy użycie otok pakietów zestawu danych opisanych w tym artykule, jeśli tworzysz nowe rozwiązanie. Azure Machine Learning zestawy danych (wersja zapoznawcza) pozwalają nie tylko przekształcać dane, ale również [migawki danych](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_snapshot.datasetsnapshot?view=azure-ml-py) i magazynować [definicje zestawów danych](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset?view=azure-ml-py). Zbiory danych to następna wersja zestawu SDK przygotowywania, oferująca rozszerzone funkcje zarządzania zestawami elementów w rozwiązaniach AI.

## <a name="prerequisites"></a>Wymagania wstępne

Aby eksplorować i przygotowywać dane, musisz:

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji Azure, przed rozpoczęciem utwórz bezpłatne konto. Wypróbuj [bezpłatną lub płatną wersję usługi Azure Machine Learning](https://aka.ms/AMLFree) już dziś.

* Obszar roboczy usługi Azure Machine Learning. Zobacz [Tworzenie obszaru roboczego usługi Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/setup-create-workspace).

* Zestaw Azure Machine Learning SDK dla języka Python (wersja 1.0.21 lub nowsza), który obejmuje pakiet usługi Azure DataSets. Aby zainstalować lub zaktualizować najnowszą wersję zestawu SDK, zobacz [Instalowanie lub aktualizowanie zestawu SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).

* Zestaw SDK przygotowywania danych Azure Machine Learning. Aby zainstalować lub zaktualizować do najnowszej wersji, zobacz [Instalowanie lub aktualizowanie zestawu SDK przygotowywania danych](https://docs.microsoft.com/python/api/overview/azure/dataprep/intro?view=azure-dataprep-py).

* Pobierz pliki przykładowe, aby wykonać następujące kroki: zbrodnie [. csv](https://dprepdata.blob.core.windows.net/dataset-sample-files/crime.csv) i [miasto. JSON](https://dprepdata.blob.core.windows.net/dataset-sample-files/city.json).

## <a name="sampling"></a>Próbkowanie

Zapoznaj się z przykładowymi danymi, aby uzyskać wstępną wiedzę o architekturze i zawartości danych. W tej chwili [`sample()`](https://docs.microsoft.com//python/api/azureml-core/azureml.core.dataset(class)?view=azure-ml-py#sample-sample-strategy--arguments-) Metoda z klasy DataSet obsługuje strategie Top N, proste i losowe i Stratifiede próbkowanie.

```Python
from azureml.core.dataset import Dataset
import random

# create an in-memory Dataset from a local file
dataset = Dataset.auto_read_files('./data/crime.csv')

# create seed for Simple Random and Stratified sampling
seed = random.randint(0, 4294967295)
```

### <a name="top-n-sample"></a>Pierwsze N próbek

W przypadku pierwszych N próbek próbkowanie pierwsze n rekordów zestawu danych jest przykładem. Jest to przydatne, jeśli właśnie próbujesz uzyskać pomysł dotyczący wyglądu rekordów danych lub zobaczyć, jakie pola znajdują się w danych.

```Python
top_n_sample_dataset = dataset.sample('top_n', {'n': 5})
top_n_sample_dataset.to_pandas_dataframe()
```

||id|Liczba przypadków|Date|Blokuj|IUCR|Typ podstawowy|Przyciski ...|
-|--|-----------|----|-----|----|------------|---
0|10498554|HZ239907|4/4/2016 23:56|007XX E 111TH ST|1153|OSZUKAŃCZE POSTĘPOWANIE|Przyciski ...
1|10516598|HZ258664|4/15/2016 17:00|082XX S MARSHFIELD|890|PRZED KRADZIEŻĄ|Przyciski ...
2|10519196|HZ261252|4/15/2016 10:00|104XX S SACRAMENTO|1154|OSZUKAŃCZE POSTĘPOWANIE|Przyciski ...
3|10519591|HZ261534|4/15/2016 9:00|113XX S PRAIRIE|1120|OSZUKAŃCZE POSTĘPOWANIE|Przyciski ...
4|10534446|HZ277630|4/15/2016 10:00|055XX N KEDZIE AVE|890|PRZED KRADZIEŻĄ|Przyciski ...

### <a name="simple-random-sample"></a>Prosta przykład losowy

W prostym losowym próbkowaniu każdy element członkowski populacji danych ma równą szansę wybrania jako część przykładu. W strategii `simple_random` próbkowania rekordy z zestawu danych są wybierane na podstawie określonego prawdopodobieństwa i zwraca zmodyfikowany zestaw danych. Parametr inicjatora jest opcjonalny.

```Python
simple_random_sample_dataset = dataset.sample('simple_random', {'probability':0.3, 'seed': seed})
simple_random_sample_dataset.to_pandas_dataframe()
```

||id|Liczba przypadków|Date|Blokuj|IUCR|Typ podstawowy|Przyciski ...|
-|--|-----------|----|-----|----|------------|---
0|10516598|HZ258664|4/15/2016 17:00|082XX S MARSHFIELD|890|PRZED KRADZIEŻĄ|Przyciski ...
1|10519196|HZ261252|4/15/2016 10:00|104XX S SACRAMENTO|1154|OSZUKAŃCZE POSTĘPOWANIE|Przyciski ...
2|10534446|HZ277630|4/15/2016 10:00|055XX N KEDZIE AVE|890|PRZED KRADZIEŻĄ|Przyciski ...

### <a name="stratified-sample"></a>Przykład Stratified

Próbki Stratified zapewniają, że niektóre grupy populacji są reprezentowane w próbce. W strategii `fractions` próbkowania populacja jest podzielona na warstwy strata, lub podgrupy, w oparciu o podobieństwa, a rekordy są losowo wybierane z każdej warstwy. `stratified`

W poniższym przykładzie grupujemy każdy rekord według określonych kolumn i Uwzględnij ten rekord na podstawie informacji o wadze strat X w `fractions`. Jeśli nie określono warstwy lub nie można zgrupować rekordu, domyślna waga do próbkowania wynosi 0.

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
0|10516598|HZ258664|4/15/2016 17:00|082XX S MARSHFIELD|890|PRZED KRADZIEŻĄ|Przyciski ...
1|10534446|HZ277630|4/15/2016 10:00|055XX N KEDZIE AVE|890|PRZED KRADZIEŻĄ|Przyciski ...
2|10535059|HZ278872|4/15/2016 4:30|004XX S KILBOURN|810|PRZED KRADZIEŻĄ|Przyciski ...

## <a name="explore-with-summary-statistics"></a>Eksploruj ze statystykami podsumowującymi

 Wykrywaj anomalie, brakujące wartości lub liczby błędów za pomocą [`get_profile()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#get-profile-arguments-none--generate-if-not-exist-true--workspace-none--compute-target-none-) metody. Ta funkcja pobiera dane dotyczące profilu i podsumowania danych, co z kolei ułatwia określenie niezbędnych operacji przygotowywania danych do zastosowania.

```Python
dataset.get_profile()
```

||Type|Min.|Maks.|Liczba|Liczba brakujących|Liczba niebrakujących|Odsetek brakujących|Liczba błędów|Liczba pustych|Kwantyl 0,1%|Kwantyl 1%|Kwantyl 5%|Kwantyl 25%|Kwantyl 50%|Kwantyl 75%|Kwantyl 95%|Kwantyl 99%|Kwantyl 99,9%|Średnia|Odchylenie standardowe|Wariancja|Skośność|Kurtoza
-|----|---|---|-----|-------------|-----------------|---------------|-----------|-----------|-------------|-----------|-----------|------------|------------|------------|------------|------------|--------------|----|------------------|--------|--------|--------
id|FieldType. INTEGER|1.04986 e + 07|1.05351 e + 07|10.0|0.0|10.0|0.0|0.0|0.0|1.04986 e + 07|1.04992 e + 07|1.04986 e + 07|1.05166 e + 07|1.05209 e + 07|1.05259 e + 07|1.05351 e + 07|1.05351 e + 07|1.05351 e + 07|1.05195 e + 07|12302,7|1.51358 e + 08|-0,495701|-1,02814
Liczba przypadków|FieldType.STRING|HZ239907|HZ278872|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
Date|FieldType. DATE|2016-04-04 23:56:00+00:00|2016-04-15 17:00:00 + 00:00|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
Blokuj|FieldType.STRING|004XX S KILBOURN|113XX S PRAIRIE|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
IUCR|FieldType. INTEGER|810|1154|10.0|0.0|10.0|0.0|0.0|0.0|810|850|810|890|1136|1153|1154|1154|1154|1058,5|137,285|18847,2|-0,785501|-1,3543
Typ podstawowy|FieldType.STRING|OSZUKAŃCZE POSTĘPOWANIE|PRZED KRADZIEŻĄ|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
Opis|FieldType.STRING|NIEPRAWDZIWA KONTROLA|PONAD $500|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
Opis lokalizacji|FieldType.STRING||SZKOŁA, PUBLICZNA, KOMPILACJA|10.0|0.0|10.0|0.0|0.0|1.0||||||||||||||
Aresztowania|FieldType. BOOLEAN|False|False|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
Krajowych|FieldType. BOOLEAN|False|False|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
Konkurencyjn|FieldType. INTEGER|531|2433|10.0|0.0|10.0|0.0|0.0|0.0|531|531|531|614|1318,5|1911|2433|2433|2433|1371.1|692,094|478994|0,105418|-1,60684
Okręg|FieldType. INTEGER|5|24|10.0|0.0|10.0|0.0|0.0|0.0|5|5|5|6|13|19|24|24|24|13.5|6,94822|48,2778|0,0930109|-1,62325
Lej|FieldType. INTEGER|1|48|10.0|0.0|10.0|0.0|0.0|0.0|1|5|1|9|22.5|40|48|48|48|24.5|16,2635|264,5|0,173723|-1,51271
Obszar społeczności|FieldType. INTEGER|4|77|10.0|0.0|10.0|0.0|0.0|0.0|4|8.5|4|24|37,5|71|77|77|77|41,2|26,6366|709,511|0,112157|-1,73379
FBI kodu|FieldType. INTEGER|6|11|10.0|0.0|10.0|0.0|0.0|0.0|6|6|6|6|11|11|11|11|11|9.4|2,36643|5.6|-0,702685|-1,59582
Współrzędna x|FieldType. INTEGER|1.16309 e + 06|1.18336 e + 06|10.0|7.0|3.0|0,7|0.0|0.0|1.16309 e + 06|1.16309 e + 06|1.16309 e + 06|1.16401 e + 06|1.16678 e + 06|1.17921 e + 06|1.18336 e + 06|1.18336 e + 06|1.18336 e + 06|1.17108 e + 06|10793,5|1.165 e + 08|0,335126|-2,33333
Współrzędna Y|FieldType. INTEGER|1.8315 e + 06|1.908 e + 06|10.0|7.0|3.0|0,7|0.0|0.0|1.8315 e + 06|1.8315 e + 06|1.8315 e + 06|1.83614 e + 06|1.85005 e + 06|1.89352 e + 06|1.908 e + 06|1.908 e + 06|1.908 e + 06|1.86319 e + 06|39905.2|1.59243 e + 09|0,293465|-2,33333
Rok|FieldType. INTEGER|2016|2016|10.0|0.0|10.0|0.0|0.0|0.0|2016|2016|2016|2016|2016|2016|2016|2016|2016|2016|0|0|NaN|NaN
Aktualizacja:|FieldType. DATE|2016-05-11 15:48:00+00:00|2016-05-27 15:45:00+00:00|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
Szerokość geograficzna|FieldType.DECIMAL|41,6928|41,9032|10.0|7.0|3.0|0,7|0.0|0.0|41,6928|41,6928|41,6928|41,7057|41,7441|41,8634|41,9032|41,9032|41,9032|41,78|0,109695|0,012033|0,292478|-2,33333
Długość geograficzna|FieldType.DECIMAL|-87,6764|-87,6043|10.0|7.0|3.0|0,7|0.0|0.0|-87,6764|-87,6764|-87,6764|-87,6734|-87,6645|-87,6194|-87,6043|-87,6043|-87,6043|-87,6484|0,0386264|0,001492|0,344429|-2,33333
Location|FieldType.STRING||(41,903206037,-87,676361925)|10.0|0.0|10.0|0.0|0.0|7.0||||||||||||||

## <a name="impute-missing-values"></a>Naliczenie brakujące wartości

W zestawach danych wartości null, NaN i wartości, które nie zawierają żadnej zawartości, nie są uznawane za brakujące wartości. Mogą one mieć wpływ na wydajność modeli uczenia maszynowego lub prowadzić do nieprawidłowych wniosków. Przypisywanie jest jednym z typowych metod rozwiązywania brakujących wartości i jest przydatne, gdy masz wysoki procent brakujących rekordów wartości, których nie można po prostu usunąć.

Z poziomu profilu zestawu danych wygenerowanego w poprzedniej sekcji zobaczymy `Latitude` , `Longitude` że i kolumny mają wysoki procent brakujących wartości. W tym przykładzie obliczamy wartość średnia i wartości kalkulacyjne dla tych dwóch kolumn.

Najpierw pobierz najnowszą definicję zestawu danych za pomocą [`get_definition()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#get-definition-version-id-none-) i dostosowanie dane za pomocą [`keep_columns()`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow#keep-columns-columns--multicolumnselection--validate-column-exists--bool---false-----azureml-dataprep-api-dataflow-dataflow)programu, aby wyświetlić tylko te kolumny, które chcemy zająć.

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
|0|10498554|False|41,692834|-87,604319|
|1|10516598|False| 41,744107 |-87,664494|
|2|10519196|False| NaN|NaN|

Następnie sprawdź `MEAN` wartość w kolumnie Latitude [`summarize()`](/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-ml-py#summarize-summary-columns--typing-union-typing-list-azureml-dataprep-api-dataflow--summarycolumnsvalue---nonetype----none--group-by-columns--typing-union-typing-list-str---nonetype----none--join-back--bool---false--join-back-columns-prefix--typing-union-str--nonetype----none-----azureml-dataprep-api-dataflow-dataflow) przy użyciu funkcji. Ta funkcja akceptuje tablica kolumn w `group_by_columns` parametru do określenia poziomu agregacji. Parametr akceptuje funkcję, która określa bieżącą nazwę kolumny, nową nazwę `SummaryFunction` pola obliczeniowego i do wykonania. `SummaryColumnsValue` `summary_columns`

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
|0|False|41,780049|

Po sprawdzeniu wartości w wyrażeniach kalkulacyjnych Użyj [`ImputeMissingValuesBuilder`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.api.builders.imputemissingvaluesbuilder?view=azure-dataprep-py) , aby poznać stałe wyrażenie, które będzie określać kolumny z wartością obliczaną `MIN`, `MAX` `MEAN` wartość lub `CUSTOM` wartość. Gdy `group_by_columns` jest określony, brakujące wartości zostaną kalkulacyjnych przez grupę przy użyciu `MIN`, `MAX`, i `MEAN` obliczane dla każdej grupy.

Akceptuje ciąg column_id i a `ReplaceValueFunction` , aby określić typ. [`ImputeColumnArguments`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.api.builders.imputecolumnarguments?view=azure-dataprep-pyfunction) Dla brakującej wartości długości geograficznej, należy wykonać poleceniem "87" w oparciu o zewnętrzną wiedzę.

```Python
# impute with MEAN
impute_mean = dprep.ImputeColumnArguments(column_id = 'Latitude',
                                          impute_function = dprep.ReplaceValueFunction.MEAN)

# impute with custom value -87
impute_custom = dprep.ImputeColumnArguments(column_id='Longitude',
                                            custom_impute_value=-87)
```

Kroki kalkulacyjne można połączyć z `ImputeMissingValuesBuilder` obiektem [`Builders`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.api.builders.builders?view=azure-dataprep-py) za pomocą funkcji [`impute_missing_values()`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.api.builders.builders?view=azure-dataprep-py#impute-missing-values-impute-columns--typing-list-azureml-dataprep-api-builders-imputecolumnarguments----none--group-by-columns--typing-union-typing-list-str---nonetype----none-----azureml-dataprep-api-builders-imputemissingvaluesbuilder)klasy. `impute_columns` Parametr przyjmuje tablicę `ImputeColumnArguments` obiektów.

```Python
# get instance of ImputeMissingValuesBuilder
impute_builder = ds_def.builders.impute_missing_values(impute_columns=[impute_mean, impute_custom],
                                                   group_by_columns=['Arrest'])
```

Wywołaj [`to_dataflow()`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.api.builders.imputemissingvaluesbuilder?view=azure-dataprep-py#to-dataflow------azureml-dataprep-api-dataflow-dataflow)funkcję, aby przechowywać etapy i zastosować je do obiektu przepływu danych przy użyciu. [`learn()`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.api.builders.imputemissingvaluesbuilder?view=azure-dataprep-py#learn------none)

```Python
impute_builder.learn()
ds_def = impute_builder.to_dataflow()
ds_def.head(3)
```

Zgodnie z poniższą tabelą wyjściową, brakująca `MEAN` `Arrest==False` wartość szerokości geograficznej została wyświetlona z wartością grupy, a brakująca Długość geograficzna to 87.

||id|Aresztowania|Szerokość geograficzna|Długość geograficzna
-|---------|-----|---------|----------
0|10498554|False|41,692834|-87,604319
1|10516598|False|41,744107|-87,664494
2|10519196|False|41,780049|-87,000000

Zaktualizuj definicję zestawu danych za pomocą [`update_definition()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset(class)?view=azure-ml-py#update-definition-definition--definition-update-message-) , aby zachować wykonane kroki transformacji.

```Python
dataset = dataset.update_definition(ds_def, 'Impute Missing')
dataset.head(3)
```

||id|Aresztowania|Szerokość geograficzna|Długość geograficzna
-|---------|-----|---------|----------
0|10498554|False|41,692834|-87,604319
1|10516598|False|41,744107|-87,664494
2|10519196|False|41,780049|-87,000000

## <a name="create-assertion-rules"></a>Tworzenie reguł potwierdzenia

Często dane, z którymi pracujemy, podczas czyszczenia i przygotowywania danych są tylko podzbiorem łącznej ilości danych potrzebnych do produkcji. W związku z tym niektóre z założeń, które my wprowadzimy w ramach czyszczenia, mogą mieć wartość false. Na przykład w przypadku zestawu danych, który stale aktualizuje, kolumna, która pierwotnie zawierała tylko liczby w określonym zakresie, może zawierać szerszy zakres wartości w późniejszych wykonaniach. Te błędy często powodują uszkodzenie potoków lub złe dane.

Zestawy danych obsługują tworzenie potwierdzeń w oparciu o dane, które są oceniane w miarę wykonywania potoku. Te potwierdzenia pozwalają nam sprawdzić, czy nasze założenia dotyczące danych nadal są dokładne, a jeśli nie, aby odpowiednio obsłużyć błędy.

Na przykład jeśli chcesz ograniczyć `Latitude` i `Longitude` wartości w zestawie danych do [`assert_value()`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#assert-value-columns--multicolumnselection--expression--azureml-dataprep-api-expressions-expression--policy--azureml-dataprep-api-engineapi-typedefinitions-assertpolicy----assertpolicy-errorvalue--1---error-code--str----assertionfailed------azureml-dataprep-api-dataflow-dataflow) określonych zakresów liczbowych, Metoda zapewnia, że zawsze jest to przypadek.

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

||Type|Min.|Maks.|Liczba|Liczba brakujących|Liczba niebrakujących|Odsetek brakujących|Liczba błędów|Liczba pustych|Kwantyl 0,1%|Kwantyl 1%|Kwantyl 5%|Kwantyl 25%|Kwantyl 50%|Kwantyl 75%|Kwantyl 95%|Kwantyl 99%|Kwantyl 99,9%|Średnia|Odchylenie standardowe|Wariancja|Skośność|Kurtoza
-|----|---|---|-----|-------------|-----------------|---------------|-----------|-----------|-------------|-----------|-----------|------------|------------|------------|------------|------------|--------------|----|------------------|--------|--------|--------
id|FieldType. INTEGER|1.04986 e + 07|1.05351 e + 07|10.0|0.0|10.0|0.0|0.0|0.0|1.04986 e + 07|1.04992 e + 07|1.04986 e + 07|1.05166 e + 07|1.05209 e + 07|1.05259 e + 07|1.05351 e + 07|1.05351 e + 07|1.05351 e + 07|1.05195 e + 07|12302,7|1.51358 e + 08|-0,495701|-1,02814
Aresztowania|FieldType. BOOLEAN|False|False|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
Szerokość geograficzna|FieldType.DECIMAL|41,6928|41,9032|10.0|0.0|10.0|0.0|0.0|0.0|41,6928|41,7185|41,6928|41,78|41,78|41,78|41,9032|41,9032|41,9032|41,78|0,0517107|0,002674|0,837593|1,05
Długość geograficzna|FieldType. INTEGER|-87|-87|10.0|0.0|10.0|0.0|3.0|0.0|-87|-87|-87|-87|-87|-87|-87|-87|-87|-87|0|0|NaN|NaN

Z poziomu profilu zobaczysz, że `Error Count` `Longitude` dla kolumny jest 3. Poniższy kod filtruje zestaw danych, pobiera błąd i sprawdza, jakie wartości powodują niepowodzenie potwierdzeń. W tym miejscu Dostosuj swój kod i odpowiednio Wyczyść dane.

```Python
from azureml.dataprep import col

ds_error = ds_def.filter(col('Longitude').is_error())
error = ds_error.head(10)['Longitude'][0]

print(error.originalValue)
```

    -87.60431945

## <a name="derive-columns-by-example"></a>Utwórz kolumny pochodne według przykładu

Jednym z bardziej zaawansowanych narzędzi dla zestawów danych jest możliwość wyprowadzania kolumn przy użyciu przykładów żądanych wyników. Dzięki temu można nadać zestawowi SDK przykład, aby można było wygenerować kod w celu osiągnięcia zamierzonych transformacji.

```Python
from azureml.core.dataset import Dataset

# create an in-memory Dataset from a local file
dataset = Dataset.auto_read_files('./data/crime.csv')
dataset.head(3)
```

||id|Liczba przypadków|Date|Blokuj|Przyciski ...|
-|---------|-----|---------|----|---
0|10498554|HZ239907|2016-04-04 23:56:00|007XX E 111TH ST|Przyciski ...
1|10516598|HZ258664|2016-04-15 17:00:00|082XX S MARSHFIELD|Przyciski ...
2|10519196|HZ261252|2016-04-15 10:00:00|104XX S SACRAMENTO|Przyciski ...

Załóżmy, że musisz przekształcić format daty i godziny na "2016-04-04 10PM-0". W argumencie Podaj przykłady żądanych danych wyjściowych `example_data` w parametrze w tym formacie: *(oryginalne dane wyjściowe, żądane dane wyjściowe)* . [`derive_column_by_example()`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#derive-column-by-example-source-columns--sourcecolumns--new-column-name--str--example-data--exampledata-----azureml-dataprep-api-dataflow-dataflow)

Poniższy kod zawiera dwa przykłady żądanych danych wyjściowych ("2016-04-04 23:56:00", "2016-04-04 10PM-0") i ("2016-04-15 17:00:00", "2016-04-15 16:00-18:00")

```Python
ds_def = dataset.get_definition()
ds_def = ds_def.derive_column_by_example(
        source_columns = "Date",
        new_column_name = "Date_Time_Range",
        example_data = [("2016-04-04 23:56:00", "2016-04-04 10PM-12AM"), ("2016-04-15 17:00:00", "2016-04-15 4PM-6PM")]
    )
ds_def.keep_columns(['ID','Date','Date_Time_Range']).head(3)
```

W poniższej tabeli Zwróć uwagę, że nowa kolumna Date_Time_Range zawiera rekordy w określonym formacie.

||id|Date|Date_Time_Range
-|--------|-----|----
0|10498554|2016-04-04 23:56:00|2016-04-04 10PM — 0
1|10516598|2016-04-15 17:00:00|2016-04-15 16:00 — 18:00
2|10519196|2016-04-15 10:00:00|2016-04-15 OD 10:00 — 12PM

```Python
# update Dataset definition to keep the transformation steps performed.
dataset = dataset.update_definition(ds_def, 'Derive Date_Time_Range')
```

## <a name="fuzzy-groupings"></a>Grupowania rozmyte

Podczas zbierania danych z różnych źródeł mogą wystąpić wahania pisowni, wersaliki lub skróty tych samych jednostek. Automatyczne standaryzacja i uzgadnianie tych wariantów przy użyciu grupowania rozmytego zestawu SDK lub klastrowania tekstu.

Na przykład kolumna `inspections.business.city` zawiera kilka form nazwy miasta "San Francisco".

```Python
from azureml.core.dataset import Dataset

# create an in-memory Dataset from a local json file
dataset = Dataset.auto_read_files('./data/city.json')
dataset.head(5)
```

||inspekcje. Business. business_id|inspekcje. business_name|inspekcje. Business. Address|inspekcje. Business. miasto|Przyciski ...|
-|-----|-------------------------|------------|--|---
0|16162|Szybkie-N-Ezee indyjskie produkty|3861 24 St|SF|Przyciski ...
1|67565|Król tajlandzkiej Noodles Cafe|1541 TARAVAL St|FRANCISCO SAN|Przyciski ...
2|67565|Król tajlandzkiej Noodles Cafe|1541 TARAVAL St|FRANCISCO SAN|Przyciski ...
3|68701|Grindz|832 Clement St|SF|Przyciski ...
4|69186|Premier gastronomiczne & Events, Inc.|1255 22 St|S.F.|Przyciski ...

Użyjmy [`fuzzy_group_column()`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#fuzzy-group-column-source-column--str--new-column-name--str--similarity-threshold--float---0-8--similarity-score-column-name--str---none-----azureml-dataprep-api-dataflow-dataflow) metody, aby dodać kolumnę z automatycznie wykrytymi kanonicznymi formą "San Francisco". Argumenty `source_column` i`new_column_name` są wymagane. Dostępna jest również opcja:

* Utwórz nową kolumnę `similarity_score_column_name`, która pokazuje wynik podobieństwa między każdą parą wartości oryginalnych i kanonicznych.

* Podaj wartość `similarity_threshold`, która jest minimalnym wynikiem podobieństwa dla wartości, które mają być zgrupowane razem.

```Python
# get the latest Dataset definition
ds_def = dataset.get_definition()
ds_def = ds_def.fuzzy_group_column(source_column='inspections.business.city',
                                       new_column_name='city_grouped',
                                       similarity_threshold=0.8,
                                       similarity_score_column_name='similarity_score')
ds_def.head(5)
```

||inspekcje. Business. business_id|inspekcje. business_name|inspekcje. Business. Address|inspekcje. Business. miasto|city_grouped|similarity_score|Przyciski ...|
-|-----|-------------------------|------------|--|---|---|---
0|16162|Szybkie-N-Ezee indyjskie produkty|3861 24 St|SF|Francisco San|0,814806|Przyciski ...
1|67565|Król tajlandzkiej Noodles Cafe|1541 TARAVAL St|FRANCISCO SAN|Francisco San|1,000000|Przyciski ...
2|67565|Król tajlandzkiej Noodles Cafe|1541 TARAVAL St|FRANCISCO SAN|Francisco San|1,000000|Przyciski ...
3|68701|Grindz|832 Clement St|SF|Francisco San|0,814806|Przyciski ...
4|69186|Premier gastronomiczne & Events, Inc.|1255 22 St|S.F.|Francisco San|0,814806|Przyciski ...

W wygenerowanej definicji zestawu danych wszystkie różne odmiany reprezentujące "San Francisco" w danych zostały znormalizowane do tego samego ciągu "San Francisco".

Zapisz ten krok grupowania rozmytego do najnowszej definicji zestawu danych za `update_definition()`pomocą.

```Python
dataset = dataset.update_definition(ds_def, 'fuzzy grouping')
```

## <a name="next-steps"></a>Następne kroki

* Zobacz [samouczek](tutorial-auto-train-models.md) zautomatyzowanej uczenia maszynowego, aby zapoznać się z przykładem modelu regresji.

* Zobacz [Omówienie](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) zestawu SDK dotyczące wzorców projektowych i przykładów użycia.

* Przykład korzystania z zestawów danych można znaleźć w przykładowych [notesach](https://aka.ms/dataset-tutorial).
