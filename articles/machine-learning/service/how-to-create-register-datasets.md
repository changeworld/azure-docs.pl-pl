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
ms.date: 08/22/2019
ms.openlocfilehash: 7a6a2c35360f59c8c2e3d0a75e646ae76c0c9de2
ms.sourcegitcommit: 3fa4384af35c64f6674f40e0d4128e1274083487
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2019
ms.locfileid: "71218301"
---
# <a name="create-and-access-datasets-preview-in-azure-machine-learning"></a>Tworzenie zestawów danych i uzyskiwanie do nich dostępu (wersja zapoznawcza) w Azure Machine Learning

W tym artykule opisano sposób tworzenia Azure Machine Learning zestawów danych (wersja zapoznawcza) oraz uzyskiwania dostępu do danych z eksperymentów lokalnych lub zdalnych.

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
> Niektóre klasy zestawu danych (wersja zapoznawcza) mają zależności w pakiecie [Azure preprodukcyjnym](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py) . W przypadku użytkowników systemu Linux te klasy są obsługiwane tylko w następujących dystrybucjach:  Red Hat Enterprise Linux, Ubuntu, Fedora i CentOS.

## <a name="dataset-types"></a>Typy zestawów danych

Zestawy danych są podzielone na dwa typy w zależności od tego, jak użytkownicy zużywają je w szkoleniu.

* [TabularDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) reprezentuje dane w formacie tabelarycznym przez analizowanie dostarczonego pliku lub listy plików. Zapewnia to możliwość zmaterializowania danych do Pandas lub Spark Dataframe. `TabularDataset` Obiekt można utworzyć na podstawie plików CSV, TSV, Parquet, wyników zapytania SQL itp. Pełną listę można znaleźć w naszej [dokumentacji](https://aka.ms/tabulardataset-api-reference).

* [FileDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.file_dataset.filedataset?view=azure-ml-py) odwołuje się do jednego lub wielu plików w magazynach danych lub publicznych adresach URL. Zapewnia to możliwość pobierania lub instalowania plików na potrzeby obliczeń. Pliki mogą być w dowolnym formacie, co umożliwia szeroką gamę scenariuszy uczenia maszynowego, w tym głębokie uczenie.

Aby dowiedzieć się więcej o nadchodzących zmianach interfejsu API, zobacz [tutaj](https://aka.ms/tabular-dataset).

## <a name="create-datasets"></a>Tworzenie zestawów danych

Tworząc zestaw danych, utworzysz odwołanie do lokalizacji źródła danych wraz z kopią jej metadanych. Dane pozostają w istniejącej lokalizacji, więc nie są naliczane żadne dodatkowe koszty związane z magazynem.

Aby można było uzyskać dostęp do danych przez Azure Machine Learning, zestawy danych muszą zostać utworzone na podstawie ścieżek w [usłudze Azure datastores](how-to-access-data.md) lub publicznych adresów URL sieci Web.

Aby utworzyć zestawy danych ze [sklepu datastore](how-to-access-data.md):

* Sprawdź, czy `contributor` masz `owner` lub masz dostęp do zarejestrowanego magazynu danych platformy Azure.

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

### <a name="create-tabulardatasets"></a>Utwórz TabularDatasets

TabularDatasets można utworzyć za pomocą zestawu SDK lub strony docelowej obszaru roboczego (wersja zapoznawcza). Sygnaturę czasową można określić z kolumny w danych lub dane wzorca ścieżki są przechowywane w, aby umożliwić szeregów czasowych cechy, które umożliwiają łatwe i wydajne filtrowanie według czasu.

#### <a name="using-the-sdk"></a>Używanie zestawu SDK

[`from_delimited_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?view=azure-ml-py#from-delimited-files-path--validate-true--include-path-false--infer-column-types-true--set-column-types-none--separator------header--promoteheadersbehavior-all-files-have-same-headers--3---partition-format-none-) Użyj`TabularDatasetFactory` metody klasy, aby odczytać pliki w formacie CSV lub TSV i utworzyć niezarejestrowane TabularDataset. W przypadku odczytywania z wielu plików wyniki zostaną zagregowane w jednej reprezentacji tabelarycznej.

```Python
# create a TabularDataset from multiple paths in datastore
datastore_paths = [
                  (datastore, 'weather/2018/11.csv'),
                  (datastore, 'weather/2018/12.csv'),
                  (datastore, 'weather/2019/*.csv')
                 ]
weather_ds = Dataset.Tabular.from_delimited_files(path=datastore_paths)

# create a TabularDataset from a delimited file behind a public web url
web_path ='https://dprepdata.blob.core.windows.net/demo/Titanic.csv'
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_path)

# preview the first 3 rows of titanic_ds
titanic_ds.take(3).to_pandas_dataframe()
```

| |PassengerId|Ocalałe|Pclass|Name|Biciu|Wiek|SibSp|Parch|Równ|Bezprzewodow|Kabin|Zaokrętowanie
-|-----------|--------|------|----|---|---|-----|-----|------|----|-----|--------|
0|1|0|3|Braund, Mr. Owen Harris|mężczyzna|22,0|1|0|A/5 21171|7,2500||N
1|2|1|1|Cumings, Pani. Jan Bradley (Florencji Briggs th...|kobieta|38,0|1|0|KOMPUTER 17599|71,2833|C85|C
2|3|1|3|Heikkinen, chybień. Laina|kobieta|26,0|0|0|STON/O2. 3101282|7,9250||N

[`from_sql_query()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?view=azure-ml-py#from-sql-query-query--validate-true--set-column-types-none-) Użyj`TabularDatasetFactory` metody klasy, aby odczytać z Azure SQL Database.

```Python

from azureml.core import Dataset, Datastore

# create tabular dataset from a SQL database in datastore
sql_datastore = Datastore.get(workspace, 'mssql')
sql_ds = Dataset.Tabular.from_sql_query((sql_datastore, 'SELECT * FROM my_table'))
```
[`with_timestamp_columns()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py#with-timestamp-columns-fine-grain-timestamp--coarse-grain-timestamp-none--validate-false-) Użyj`TabularDataset` metody klasy, aby umożliwić łatwe i wydajne filtrowanie według czasu. Więcej przykładów i szczegółów można znaleźć [tutaj](https://aka.ms/azureml-tsd-notebook).

```Python
# create a TabularDataset with timeseries trait
datastore_paths = [(datastore, 'weather/*/*/*/data.parquet')]

# get a coarse timestamp column from the path pattern
dataset = Dataset.Tabular.from_parquet_files(path=datastore_path, partition_format='weather/{coarse_time:yyy/MM/dd}/data.parquet')

# set coarse timestamp to the virtual column created, and fine grain timestamp from a column in the data
dataset = dataset.with_timestamp_columns(fine_grain_timestamp='datetime', coarse_grain_timestamp='coarse_time')

# filter with timeseries trait specific methods
data_slice = dataset.time_before(datetime(2019, 1, 1))
data_slice = dataset.time_after(datetime(2019, 1, 1))
data_slice = dataset.time_between(datetime(2019, 1, 1), datetime(2019, 2, 1))
data_slice = dataset.time_recent(timedelta(weeks=1, days=1))
```

#### <a name="using-the-workspace-landing-page"></a>Korzystanie ze strony docelowej obszaru roboczego

Zaloguj się do [strony docelowej obszaru roboczego](https://ml.azure.com) , aby utworzyć zestaw danych za pośrednictwem środowiska sieci Web. Obecnie Strona docelowa obszaru roboczego obsługuje tylko tworzenie TabularDatasets.

Poniższe animacje pokazują, jak utworzyć zestaw danych na stronie docelowej obszaru roboczego.

Najpierw wybierz pozycję **zestawy danych** w sekcji **elementy zawartości** okienka po lewej stronie. Następnie wybierz pozycję **+ Utwórz zestaw danych** , aby wybrać źródło zestawu danych. może to być zarówno z plików lokalnych, magazynów danych, jak i publicznych adresów URL sieci Web. **Ustawienia i Podgląd** oraz formularze **schematów** są inteligentnie wypełniane na podstawie typu pliku. Wybierz pozycję **dalej** , aby je przejrzeć lub aby jeszcze bardziej skonfigurować zestaw danych przed utworzeniem. Wybierz pozycję **gotowe** , aby zakończyć tworzenie zestawu danych.

![Tworzenie zestawu danych przy użyciu interfejsu użytkownika](media/how-to-create-register-datasets/create-dataset-ui.gif)

### <a name="create-filedatasets"></a>Utwórz FileDatasets

[`from_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory?view=azure-ml-py#from-files-path--validate-true-) Użyj`FileDatasetFactory` metody klasy, aby załadować pliki w dowolnym formacie i utworzyć niezarejestrowane FileDataset.

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

## <a name="register-datasets"></a>Rejestrowanie zestawów danych

Aby ukończyć proces tworzenia, zarejestruj zestawy danych w obszarze roboczym:

Użyj metody [`register()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#register-workspace--name--description-none--tags-none--visible-true--exist-ok-false--update-if-exist-false-) , aby zarejestrować zestawy danych w obszarze roboczym, aby mogły być współużytkowane z innymi osobami i ponownie używane w różnych eksperymentach.

```Python
titanic_ds = titanic_ds.register(workspace = workspace,
                                 name = 'titanic_ds',
                                 description = 'titanic training data')
```

>[!Note]
> Zestawy danych utworzone za pośrednictwem strony docelowej obszaru roboczego są automatycznie rejestrowane w obszarze roboczym.

## <a name="version-datasets"></a>Zestawy danych wersji

Nowy zestaw danych można zarejestrować pod tą samą nazwą, tworząc nową wersję. Wersja zestawu danych to sposób zakładania stanu danych, dzięki czemu można zastosować określoną wersję zestawu danych na potrzeby eksperymentowania lub kopiowania w przyszłości. Typowe scenariusze, które należy wziąć pod uwagę w przypadku przechowywania wersji:
* Gdy nowe dane są dostępne do ponownego szkolenia.
* W przypadku stosowania różnych metod przygotowywania lub opracowywania funkcji.

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

Zarejestrowane zestawy danych są dostępne lokalnie i zdalnie w klastrach obliczeniowych, takich jak Azure Machine Learning COMPUTE. Aby uzyskać dostęp do zarejestrowanego zestawu danych w ramach eksperymentów, użyj poniższego kodu, aby uzyskać obszar roboczy i zarejestrowany zestaw danych według nazwy. [`get_by_name()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#get-by-name-workspace--name--version--latest--) Metoda`Dataset` w klasie domyślnie zwraca najnowszą wersję zestawu danych zarejestrowanego w obszarze roboczym.

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

* Dowiedz się [, jak uczenie się z zestawami danych](how-to-train-with-datasets.md)
* Używaj automatycznej uczenia maszynowego do [uczenia się z TabularDatasets](https://aka.ms/automl-dataset).
* Aby uzyskać więcej przykładów szkolenia z zestawami danych, zobacz [przykładowe notesy](https://aka.ms/dataset-tutorial).
