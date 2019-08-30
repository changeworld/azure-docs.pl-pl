---
title: Tworzenie zestawów danych w celu uzyskania dostępu do dane za pomocą platformy Azure
titleSuffix: Azure Machine Learning service
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
ms.openlocfilehash: e5d5d36e82914f1d6d03299db0ed1427ac5a389a
ms.sourcegitcommit: aaa82f3797d548c324f375b5aad5d54cb03c7288
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/29/2019
ms.locfileid: "70147587"
---
# <a name="create-and-access-datasets-preview-in-azure-machine-learning"></a>Tworzenie zestawów danych i uzyskiwanie do nich dostępu (wersja zapoznawcza) w Azure Machine Learning

W tym artykule opisano sposób tworzenia Azure Machine Learning zestawów danych (wersja zapoznawcza) oraz uzyskiwania dostępu do danych z eksperymentów lokalnych lub zdalnych.

Za pomocą Azure Machine Learning zestawów danych można: 

* **Przechowywanie pojedynczej kopii danych w magazynie** , do której odwołują się zestawy danych. 

* **Łatwe uzyskiwanie dostępu do danych podczas uczenia modelowego** bez obaw o parametry połączenia lub ścieżki danych.

* **Udostępnianie danych & współpracy** z innymi użytkownikami.

## <a name="prerequisites"></a>Wymagania wstępne

Aby tworzyć zestawy danych i korzystać z nich, potrzebne są:

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji Azure, przed rozpoczęciem utwórz bezpłatne konto. Wypróbuj [bezpłatną lub płatną wersję usługi Azure Machine Learning](https://aka.ms/AMLFree) już dziś.

* [Obszar roboczy usługi Azure Machine Learning](how-to-manage-workspace.md)

* [Zestaw Azure Machine Learning SDK dla języka Python](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py), który obejmuje pakiet usługi Azure DataSets.

> [!Note]
> Niektóre klasy zestawu danych (wersja zapoznawcza) mają zależności w pakiecie [Azure](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py) preprodukcyjnym. W przypadku użytkowników systemu Linux te klasy są obsługiwane tylko w następujących dystrybucjach:  Red Hat Enterprise Linux, Ubuntu, Fedora i CentOS.

## <a name="dataset-types"></a>Typy zestawów danych

Zestawy danych są podzielone na różne typy w zależności od tego, jak użytkownicy zużywają je w szkoleniu. Lista typów zestawów danych:
* [TabularDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) reprezentuje dane w formacie tabelarycznym przez analizowanie dostarczonego pliku lub listy plików. Zapewnia to możliwość zmaterializowania danych w Pandas Dataframe. `TabularDataset` Obiekt można utworzyć na podstawie plików CSV, TSV, Parquet, wyników zapytania SQL itp. Pełną listę można znaleźć w naszej [dokumentacji](https://aka.ms/tabulardataset-api-reference).
* FileDataset odwołuje się do jednego lub wielu plików w magazynach danych lub publicznych adresach URL. Zapewnia to możliwość pobierania lub instalowania plików na potrzeby obliczeń. Pliki mogą być w dowolnym formacie, co umożliwia szeroką gamę scenariuszy uczenia maszynowego, w tym głębokie uczenie.

Aby dowiedzieć się więcej o nadchodzących zmianach interfejsu API, zobacz [tutaj](https://aka.ms/tabular-dataset).

## <a name="create-datasets"></a>Tworzenie zestawów danych 

Tworząc zestaw danych, utworzysz odwołanie do lokalizacji źródła danych wraz z kopią jej metadanych. Dane pozostają w istniejącej lokalizacji, więc nie są naliczane żadne dodatkowe koszty związane z magazynem.

Aby dane były dostępne dla usługi Azure Machine Learning, zestawy danych muszą zostać utworzone na podstawie ścieżek w [usłudze Azure](how-to-access-data.md) datastores lub publicznych adresów URL sieci Web.

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

`from_delimited_files()` Użyj`TabularDatasetFactory` metody klasy, aby odczytać pliki w formacie CSV lub TSV i utworzyć niezarejestrowane TabularDataset. W przypadku odczytywania z wielu plików wyniki zostaną zagregowane w jednej reprezentacji tabelarycznej.

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

### <a name="create-filedatasets"></a>Utwórz FileDatasets
`from_files()` Użyj`FileDatasetFactory` metody klasy, aby załadować pliki w dowolnym formacie i utworzyć niezarejestrowane FileDataset.

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

Użyj metody `register()` , aby zarejestrować zestawy danych w obszarze roboczym, aby mogły być współużytkowane z innymi osobami i ponownie używane w różnych eksperymentach.

```Python
titanic_ds = titanic_ds.register(workspace = workspace,
                                 name = 'titanic_ds',
                                 description = 'titanic training data')
```

## <a name="version-datasets"></a>Zestawy danych wersji

Nowy zestaw danych można zarejestrować pod tą samą nazwą, tworząc nową wersję. Wersja zestawu danych to sposób zakładania stanu danych, dzięki czemu można zastosować określoną wersję zestawu danych na potrzeby eksperymentowania lub kopiowania w przyszłości. Typowe scenariusze, które należy wziąć pod uwagę w przypadku przechowywania wersji: 
* Gdy nowe dane są dostępne do ponownego szkolenia.
* W przypadku stosowania różnych metod przygotowywania lub opracowywania funkcji.

```Python
# create a TabularDataset from new Titanic training data
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


## <a name="access-your-data-during-training"></a>Uzyskiwanie dostępu do danych podczas szkoleń

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

* Używaj automatycznej uczenia maszynowego do [uczenia się z TabularDatasets](https://aka.ms/automl-dataset).
* Aby uzyskać więcej przykładów szkolenia z zestawami danych, zobacz [przykładowe notesy](https://aka.ms/dataset-tutorial).
