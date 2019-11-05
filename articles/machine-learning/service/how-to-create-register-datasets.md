---
title: Tworzenie zestawów danych w celu uzyskania dostępu do dane za pomocą platformy Azure
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak tworzyć zestawy danych z różnych źródeł i rejestrować zestawy danych za pomocą obszaru roboczego
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 11/04/2019
ms.openlocfilehash: aabbac60acc53cfccc29fc3dbd06575e09840d83
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73497139"
---
# <a name="create-and-access-datasets-preview-in-azure-machine-learning"></a>Tworzenie zestawów danych i uzyskiwanie do nich dostępu (wersja zapoznawcza) w Azure Machine Learning

[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

W tym artykule dowiesz się, jak utworzyć Azure Machine Learning zestawy danych (wersja zapoznawcza) i jak uzyskać dostęp do informacji z lokalnych lub zdalnych eksperymentów.

Za pomocą Azure Machine Learning zestawów danych można:

* **Przechowywanie pojedynczej kopii danych w magazynie** , do której odwołują się zestawy danych.

* **Łatwe uzyskiwanie dostępu do danych podczas uczenia modelowego** bez obaw o parametry połączenia lub ścieżki danych.

* **Udostępnianie danych & współpracy** z innymi użytkownikami.

## <a name="prerequisites"></a>Wymagania wstępne

Aby tworzyć zestawy danych i korzystać z nich, potrzebne są:

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji Azure, przed rozpoczęciem utwórz bezpłatne konto. Wypróbuj [bezpłatną lub płatną wersję Azure Machine Learning](https://aka.ms/AMLFree) dzisiaj.

* [Obszar roboczy Azure Machine Learning](how-to-manage-workspace.md)

* [Zestaw Azure Machine Learning SDK dla języka Python](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py), który obejmuje pakiet usługi Azure DataSets.

> [!Note]
> Niektóre klasy zestawu danych (wersja zapoznawcza) mają zależności w pakiecie [Azure preprodukcyjnym](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py) . W przypadku użytkowników systemu Linux te klasy są obsługiwane tylko w następujących dystrybucjach: Red Hat Enterprise Linux, Ubuntu, Fedora i CentOS.

## <a name="dataset-types"></a>Typy zestawów danych

Zestawy danych są podzielone na dwa typy w zależności od tego, jak użytkownicy zużywają je w szkoleniu.

* [TabularDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) reprezentuje dane w formacie tabelarycznym przez analizowanie dostarczonego pliku lub listy plików. Zapewnia to możliwość zmaterializowania danych do Pandas lub Spark Dataframe. Obiekt `TabularDataset` można utworzyć na podstawie plików CSV, TSV, Parquet, wyników zapytania SQL itp. Pełną listę można znaleźć w naszej [dokumentacji](https://aka.ms/tabulardataset-api-reference).

* [FileDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.file_dataset.filedataset?view=azure-ml-py) odwołuje się do jednego lub wielu plików w magazynach danych lub publicznych adresach URL. Zapewnia to możliwość pobierania lub instalowania plików na potrzeby obliczeń. Pliki mogą być w dowolnym formacie, co umożliwia szeroką gamę scenariuszy uczenia maszynowego, w tym głębokie uczenie.

Aby dowiedzieć się więcej o nadchodzących zmianach interfejsu API, zobacz [tutaj](https://aka.ms/tabular-dataset).

## <a name="create-datasets"></a>Utwórz zestawy danych

Tworząc zestaw danych, utworzysz odwołanie do lokalizacji źródła danych wraz z kopią jej metadanych. Dane pozostają w istniejącej lokalizacji, więc nie są naliczane żadne dodatkowe koszty związane z magazynem. Zarówno TabularDatasets, jak i FileDatasets można utworzyć za pomocą zestawu SDK języka Python lub strony docelowej obszaru roboczego (wersja zapoznawcza). 

Aby można było uzyskać dostęp do danych przez Azure Machine Learning, zestawy danych muszą zostać utworzone na podstawie ścieżek w [usłudze Azure datastores](how-to-access-data.md) lub publicznych adresów URL sieci Web.

### <a name="using-the-sdk"></a>Używanie zestawu SDK

Aby utworzyć zestawy danych z [usługi Azure datastore](how-to-access-data.md) przy użyciu zestawu SDK języka Python:

* Sprawdź, czy masz `contributor` lub `owner` dostęp do zarejestrowanego magazynu danych platformy Azure.

* Utwórz zestaw danych, odwołując się do ścieżki w magazynie danych.

```Python
from azureml.core.workspace import Workspace
from azureml.core.datastore import Datastore
from azureml.core.dataset import Dataset

datastore_name = 'your datastore name'

# get existing workspace
workspace = Workspace.from_config()

# retrieve an existing datastore in the workspace by name
datastore = Datastore.get(workspace, datastore_name)
```

#### <a name="create-a-tabulardataset"></a>Utwórz TabularDataset

TabularDatasets można utworzyć za pomocą zestawu SDK lub programu Azure Machine Learning Studio. Sygnaturę czasową można określić z kolumny w danych lub dane wzorca ścieżki są przechowywane w, aby umożliwić cechę szeregów czasowych, która umożliwia łatwe i wydajne filtrowanie według czasu.

Użyj metody [`from_delimited_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?view=azure-ml-py#from-delimited-files-path--validate-true--include-path-false--infer-column-types-true--set-column-types-none--separator------header--promoteheadersbehavior-all-files-have-same-headers--3---partition-format-none-) na `TabularDatasetFactory` klasie, aby odczytać pliki w formacie CSV lub TSV i utworzyć niezarejestrowane TabularDataset. W przypadku odczytywania z wielu plików wyniki zostaną zagregowane w jednej reprezentacji tabelarycznej.

```Python
# create a TabularDataset from multiple paths in datastore
datastore_paths = [
                  (datastore, 'weather/2018/11.csv'),
                  (datastore, 'weather/2018/12.csv'),
                  (datastore, 'weather/2019/*.csv')
                 ]
weather_ds = Dataset.Tabular.from_delimited_files(path=datastore_paths)
```

Domyślnie podczas tworzenia TabularDataset typy danych kolumn są wywnioskowane automatycznie. Jeśli wywnioskowane typy nie są zgodne z oczekiwaniami, można określić typy kolumn przy użyciu następującego kodu. Więcej informacji o obsługiwanych typach danych [znajdziesz tutaj](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.datatype?view=azure-ml-py).

```Python
from azureml.data.dataset_factory import DataType

# create a TabularDataset from a delimited file behind a public web url and convert column "Survived" to boolean
web_path ='https://dprepdata.blob.core.windows.net/demo/Titanic.csv'
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_path, set_column_types={'Survived': DataType.to_bool()})

# preview the first 3 rows of titanic_ds
titanic_ds.take(3).to_pandas_dataframe()
```

| |PassengerId|Ocalałe|Pclass|Nazwa|Biciu|Wiek|SibSp|Parch|Równ|Bezprzewodow|Kabin|Zaokrętowanie
-|-----------|--------|------|----|---|---|-----|-----|------|----|-----|--------|
0|1|False|3|Braund, Mr. Owen Harris|mężczyzna|22,0|1|0|A/5 21171|7,2500||S
1|2|True|1|Cumings, Pani. Jan Bradley (Florencji Briggs th...|kobieta|38,0|1|0|KOMPUTER 17599|71,2833|C85|C
2|3|True|3|Heikkinen, chybień. Laina|kobieta|26,0|0|0|STON/O2. 3101282|7,9250||S

Użyj metody [`from_sql_query()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?view=azure-ml-py#from-sql-query-query--validate-true--set-column-types-none-) w klasie `TabularDatasetFactory`, aby odczytać z Azure SQL Database.

```Python

from azureml.core import Dataset, Datastore

# create tabular dataset from a SQL database in datastore
sql_datastore = Datastore.get(workspace, 'mssql')
sql_ds = Dataset.Tabular.from_sql_query((sql_datastore, 'SELECT * FROM my_table'))
```

W TabularDatasets można określić sygnaturę czasową z kolumny w danych lub dane wzorca ścieżki są przechowywane w, aby włączyć cechę szeregów czasowych, która umożliwia łatwe i wydajne filtrowanie według czasu.

Użyj metody [`with_timestamp_columns()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py#with-timestamp-columns-fine-grain-timestamp--coarse-grain-timestamp-none--validate-false-) na `TabularDataset` klasie, aby określić kolumnę sygnatury czasowej i włączyć filtrowanie według czasu. Więcej przykładów i szczegółów można znaleźć [tutaj](https://aka.ms/azureml-tsd-notebook).

```Python
# create a TabularDataset with time series trait
datastore_paths = [(datastore, 'weather/*/*/*/data.parquet')]

# get a coarse timestamp column from the path pattern
dataset = Dataset.Tabular.from_parquet_files(path=datastore_path, partition_format='weather/{coarse_time:yyy/MM/dd}/data.parquet')

# set coarse timestamp to the virtual column created, and fine grain timestamp from a column in the data
dataset = dataset.with_timestamp_columns(fine_grain_timestamp='datetime', coarse_grain_timestamp='coarse_time')

# filter with time-series-trait-specific methods
data_slice = dataset.time_before(datetime(2019, 1, 1))
data_slice = dataset.time_after(datetime(2019, 1, 1))
data_slice = dataset.time_between(datetime(2019, 1, 1), datetime(2019, 2, 1))
data_slice = dataset.time_recent(timedelta(weeks=1, days=1))
```

#### <a name="create-a-filedataset"></a>Utwórz FileDataset

Użyj metody [`from_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory?view=azure-ml-py#from-files-path--validate-true-) na `FileDatasetFactory` klasie, aby załadować pliki w dowolnym formacie i utworzyć niezarejestrowane FileDataset.

```Python
# create a FileDataset from multiple paths in datastore
datastore_paths = [
                  (datastore, 'animals/dog/1.jpg'),
                  (datastore, 'animals/dog/2.jpg'),
                  (datastore, 'animals/dog/*.jpg')
                 ]
animal_ds = Dataset.File.from_files(path=datastore_paths)

# create a FileDataset from image and label files behind public web urls
web_paths = [
            'https://azureopendatastorage.blob.core.windows.net/mnist/train-images-idx3-ubyte.gz',
            'https://azureopendatastorage.blob.core.windows.net/mnist/train-labels-idx1-ubyte.gz'
           ]
mnist_ds = Dataset.File.from_files(path=web_paths)
```

#### <a name="on-the-web"></a>W sieci Web 
Poniższe kroki i animacje pokazują, jak utworzyć zestaw danych w Azure Machine Learning Studio, https://ml.azure.com.

![Tworzenie zestawu danych przy użyciu interfejsu użytkownika](media/how-to-create-register-datasets/create-dataset-ui.gif)

Aby utworzyć zestaw danych w programie Studio:
1. Zaloguj się w https://ml.azure.com.
1. Wybierz pozycję **zestawy danych** w sekcji **elementy zawartości** okienka po lewej stronie. 
1. Wybierz pozycję **+ Utwórz zestaw danych** , aby wybrać źródło zestawu danych. może to być zarówno z plików lokalnych, magazynów danych, jak i publicznych adresów URL sieci Web.
1. Wybierz **tabelaryczną** lub **plik** dla typu zestawu danych.
1. Wybierz pozycję **dalej** , aby przejrzeć formularze **Ustawienia i Podgląd**, **schemat** i **Potwierdź szczegóły** . są one inteligentnie wypełniane na podstawie typu pliku. Za pomocą tych formularzy Sprawdź wybrane opcje i jeszcze bardziej Skonfiguruj zestaw danych przed utworzeniem.  
1. Wybierz pozycję **Utwórz** , aby zakończyć tworzenie zestawu danych.

## <a name="register-datasets"></a>Rejestrowanie zestawów danych

Aby ukończyć proces tworzenia, zarejestruj zestawy danych w obszarze roboczym.

Użyj metody [`register()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#register-workspace--name--description-none--tags-none--visible-true--exist-ok-false--update-if-exist-false-) , aby zarejestrować zestawy danych w obszarze roboczym, aby mogły być współużytkowane z innymi osobami i ponownie używane w różnych eksperymentach.

```Python
titanic_ds = titanic_ds.register(workspace=workspace,
                                 name='titanic_ds',
                                 description='titanic training data')
```

> [!Note]
> Zestawy danych utworzone za pośrednictwem Azure Machine Learning Studio są automatycznie rejestrowane w obszarze roboczym.

## <a name="create-datasets-with-azure-open-datasets"></a>Tworzenie zestawów danych za pomocą otwartych zestawów danych platformy Azure

[Otwarte zestawy danych platformy Azure](https://azure.microsoft.com/services/open-datasets/) mają nadzorowane zestawy danych, których można użyć do dodawania funkcji specyficznych dla scenariusza do rozwiązań uczenia maszynowego w celu uzyskania dokładniejszych modeli. Zestawy danych obejmują dane z domeny publicznej na potrzeby pogodowych, spisu, świąt, bezpieczeństwa publicznego i lokalizacji, które ułatwiają uczenie modeli uczenia maszynowego i wzbogacanie rozwiązań predykcyjnych. Otwarte zestawy danych znajdują się w chmurze w Microsoft Azure i znajdują się zarówno w zestawie SDK, jak i w interfejsie użytkownika obszaru roboczego.

### <a name="using-the-sdk"></a>Używanie zestawu SDK

Aby utworzyć zestawy danych przy użyciu otwartych zestawów danych platformy Azure z zestawu SDK, upewnij się, że pakiet został zainstalowany z `pip install azureml-opendatasets`. Każdy zestaw danych dyskretnych jest reprezentowany przez własną klasę w zestawie SDK, a niektóre klasy są dostępne jako `TabularDataset`, `FileDataset`lub obie. Pełną listę klas można znaleźć w [dokumentacji referencyjnej](https://docs.microsoft.com/python/api/azureml-opendatasets/azureml.opendatasets?view=azure-ml-py) .

Większość klas dziedziczy z i zwraca wystąpienie `TabularDataset`. Przykłady tych klas to `PublicHolidays`, `BostonSafety`i `UsPopulationZip`. Aby utworzyć `TabularDataset` z tych typów klas, należy użyć konstruktora bez argumentów. Po zarejestrowaniu zestawu danych utworzonego na podstawie otwartych zestawów danych żadne dane nie są natychmiast pobierane, ale dane będą dostępne później na żądanie (na przykład na potrzeby szkolenia) z centralnej lokalizacji magazynu. 

```python
from azureml.opendatasets import UsPopulationZip

tabular_dataset = UsPopulationZip()
tabular_dataset = tabular_dataset.register(workspace=workspace, name="pop data", description="US population data by zip code")
```

Niektóre klasy można pobrać jako `TabularDataset` lub `FileDataset`, co pozwala na manipulowanie i/lub pobranie plików bezpośrednio. Inne klasy mogą uzyskać tylko zestaw danych przy użyciu funkcji `get_tabular_dataset()` **lub** `get_file_dataset()`. Poniższy przykład kodu pokazuje kilka przykładów tych typów klas.

```python
from azureml.opendatasets import MNIST

# MNIST class can return either TabularDataset or FileDataset
tabular_dataset = MNIST.get_tabular_dataset()
file_dataset = MNIST.get_file_dataset()

from azureml.opendatasets import Diabetes

# Diabetes class can return ONLY return TabularDataset and must be called from the static function
diabetes_tabular = Diabetes.get_tabular_dataset()
```

### <a name="using-the-ui"></a>Korzystanie z interfejsu użytkownika

Zestawy danych można także tworzyć z klas otwartych zestawów danych przy użyciu interfejsu użytkownika. W obszarze roboczym przejdź do karty **zestawy danych** w obszarze *zasoby*. Kliknij listę rozwijaną **Utwórz zestaw danych** , a następnie kliknij pozycję **z otwartego**zestawu.

![Otwórz zestaw danych przy użyciu interfejsu użytkownika](media/how-to-create-register-datasets/open-datasets-1.png)

Następnie wybierz zestaw danych, wybierając kafelek, opcjonalnie filtrując przy użyciu paska wyszukiwania. Następnie kliknij przycisk **Next** (Dalej).

![Wybierz zestaw danych](media/how-to-create-register-datasets/open-datasets-2.png)

Następnie wybierz nazwę, aby zarejestrować zestaw danych i opcjonalnie odfiltrować dane przy użyciu dostępnych filtrów. W tym przypadku zestaw danych dni wolnych jest filtrowany okres do jednego roku i kod kraju tylko do USA. Następnie kliknij pozycję **Utwórz**.

![Ustaw parametry zestawu danych i Utwórz zestaw danych](media/how-to-create-register-datasets/open-datasets-3.png)

Zestaw danych jest teraz tworzony i dostępny w obszarze roboczym w obszarze **zestawy danych**i może być używany w taki sam sposób jak w przypadku innych utworzonych zestawów danych.

## <a name="version-datasets"></a>Zestawy danych wersji

Nowy zestaw danych można zarejestrować pod tą samą nazwą, tworząc nową wersję. Wersja zestawu danych to sposób zakładania stanu danych, dzięki czemu można zastosować określoną wersję zestawu danych na potrzeby eksperymentowania lub kopiowania w przyszłości. Dowiedz się więcej o [wersjach zestawu danych](how-to-version-track-datasets.md).
```Python
# create a TabularDataset from Titanic training data
web_paths = [
            'https://dprepdata.blob.core.windows.net/demo/Titanic.csv',
            'https://dprepdata.blob.core.windows.net/demo/Titanic2.csv'
           ]
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_paths)

# create a new version of titanic_ds
titanic_ds = titanic_ds.register(workspace = workspace,
                                 name = 'titanic_ds',
                                 description = 'new titanic training data',
                                 create_new_version = True)
```


## <a name="access-datasets-in-your-script"></a>Dostęp do zestawów danych w skrypcie

Zarejestrowane zestawy danych są dostępne lokalnie i zdalnie w klastrach obliczeniowych, takich jak Azure Machine Learning COMPUTE. Aby uzyskać dostęp do zarejestrowanego zestawu danych w ramach eksperymentów, użyj poniższego kodu, aby uzyskać obszar roboczy i zarejestrowany zestaw danych według nazwy. Metoda [`get_by_name()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#get-by-name-workspace--name--version--latest--) w klasie `Dataset` domyślnie zwraca najnowszą wersję zestawu danych zarejestrowanego w obszarze roboczym.

```Python
%%writefile $script_folder/train.py

from azureml.core import Dataset, Run

run = Run.get_context()
workspace = run.experiment.workspace

dataset_name = 'titanic_ds'

# Get a dataset by name
titanic_ds = Dataset.get_by_name(workspace=workspace, name=dataset_name)

# Load a TabularDataset into pandas DataFrame
df = titanic_ds.to_pandas_dataframe()
```

## <a name="next-steps"></a>Następne kroki

* Dowiedz się [, jak uczenie się z zestawami danych](how-to-train-with-datasets.md).
* Używaj automatycznej uczenia maszynowego do [uczenia się z TabularDatasets](https://aka.ms/automl-dataset).
* Aby uzyskać więcej przykładów szkolenia z zestawami danych, zobacz [przykładowe notesy](https://aka.ms/dataset-tutorial).
