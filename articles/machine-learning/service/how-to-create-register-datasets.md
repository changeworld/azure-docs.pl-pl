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
ms.date: 05/21/2019
ms.openlocfilehash: 473bf87e1961c3c7687b0867885adef40c14d71f
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/31/2019
ms.locfileid: "68694326"
---
# <a name="create-and-access-datasets-preview-in-azure-machine-learning"></a>Tworzenie zestawów danych i uzyskiwanie do nich dostępu (wersja zapoznawcza) w Azure Machine Learning

W tym artykule opisano sposób tworzenia Azure Machine Learning zestawów danych (wersja zapoznawcza) oraz uzyskiwania dostępu do danych z eksperymentów lokalnych lub zdalnych.

Za pomocą Azure Machine Learning zestawów danych można: 

* **Przechowywanie pojedynczej kopii danych w magazynie** , do której odwołują się zestawy danych

* **Analizowanie danych** za ich poorednictwem analizy danych 

* **Łatwe uzyskiwanie dostępu do danych podczas uczenia modelu** bez obaw o parametry połączenia lub ścieżki danych

* **Udostępnianie danych & współpracy** z innymi użytkownikami

## <a name="prerequisites"></a>Wymagania wstępne

Aby tworzyć zestawy danych i korzystać z nich, potrzebne są:

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji Azure, przed rozpoczęciem utwórz bezpłatne konto. Wypróbuj [bezpłatną lub płatną wersję usługi Azure Machine Learning](https://aka.ms/AMLFree) już dziś.

* [Obszar roboczy usługi Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/setup-create-workspace)

* [Zestaw Azure Machine Learning SDK dla języka Python](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py), który obejmuje pakiet usługi Azure DataSets.

> [!Note]
> Niektóre klasy zestawu danych (wersja zapoznawcza) mają zależności w pakiecie [Azure-](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py) preprodukcyjnym (ga). W przypadku użytkowników systemu Linux te klasy są obsługiwane tylko w następujących dystrybucjach:  Red Hat Enterprise Linux, Ubuntu, Fedora i CentOS.

## <a name="data-formats"></a>Formaty danych

Zestaw danych Azure Machine Learning można utworzyć przy użyciu następujących formatów:
+ [Lista](/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-delimited-files-path--separator------header--promoteheadersbehavior-all-files-have-same-headers--3---encoding--fileencoding-utf8--0---quoting-false--infer-column-types-true--skip-rows-0--skip-mode--skiplinesbehavior-no-rows--0---comment-none--include-path-false--archive-options-none--partition-format-none-)
+ [kodu](/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-json-files-path--encoding--fileencoding-utf8--0---flatten-nested-arrays-false--include-path-false--partition-format-none-)
+ [Excel](/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-excel-files-path--sheet-name-none--use-column-headers-false--skip-rows-0--include-path-false--infer-column-types-true--partition-format-none-)
+ [Parquet](/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-parquet-files-path--include-path-false--partition-format-none-)
+ [Ramka datapandas](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-pandas-dataframe-dataframe--path-none--in-memory-false-)
+ [Zapytanie SQL](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-sql-query-data-source--query-)
+ [binarny](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-binary-files-path-)

## <a name="create-datasets"></a>Tworzenie zestawów danych 

Tworząc zestaw danych, utworzysz odwołanie do lokalizacji źródła danych wraz z kopią jej metadanych. Dane pozostają w istniejącej lokalizacji, więc nie są naliczane żadne dodatkowe koszty związane z magazynem.

### <a name="create-from-local-files"></a>Utwórz z plików lokalnych

Załaduj pliki z komputera lokalnego, określając ścieżkę pliku lub folderu za pomocą [`auto_read_files()`](/python/api/azureml-core/azureml.core.dataset(class)?view=azure-ml-py#auto-read-files-path--include-path-false--partition-format-none-) metody `Dataset` z klasy.  Ta metoda wykonuje następujące czynności bez konieczności określania typu pliku lub argumentów analizy:

* Wnioskowanie i ustawienie ogranicznika.
* Pomijanie pustych rekordów w górnej części pliku.
* Wnioskowanie i ustawienie wiersza nagłówka.
* Wnioskowanie i konwertowanie typów danych kolumn.

```Python
from azureml.core.dataset import Dataset

dataset = Dataset.auto_read_files('./data/crime.csv')
```

Alternatywnie możesz użyć funkcji specyficznych dla plików, aby jawnie kontrolować analizowanie pliku. 


### <a name="create-from-azure-datastores"></a>Tworzenie z magazynów danych platformy Azure

Aby utworzyć zestawy danych ze [sklepu datastore](how-to-access-data.md):

* Sprawdź, czy `contributor` masz `owner` lub masz dostęp do zarejestrowanego magazynu danych platformy Azure.

* Utwórz zestaw danych, odwołując się do ścieżki w magazynie danych 

```Python
from azureml.core.workspace import Workspace
from azureml.core.datastore import Datastore
from azureml.core.dataset import Dataset

datastore_name = 'your datastore name'

# get existing workspace
workspace = Workspace.from_config()

# retrieve an existing datastore in the workspace by name
dstore = Datastore.get(workspace, datastore_name)
```

Użyj metody `from_delimited_files()` , aby odczytać rozdzielone pliki z [odwołania](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py)do danych i utworzyć niezarejestrowany zestaw danych.

```Python
# create an in-memory Dataset on your local machine
dataset = Dataset.from_delimited_files(dstore.path('data/src/crime.csv'))

# returns the first 5 rows of the Dataset as a pandas Dataframe.
dataset.head(5)
```

## <a name="register-datasets"></a>Rejestrowanie zestawów danych

Aby ukończyć proces tworzenia, zarejestruj zestawy danych w obszarze roboczym:

Użyj metody [`register()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#register-workspace--name--description-none--tags-none--visible-true--exist-ok-false--update-if-exist-false-) , aby zarejestrować zestawy danych w obszarze roboczym, aby mogły być współużytkowane z innymi osobami i ponownie używane w różnych eksperymentach.

```Python
dataset = dataset.register(workspace = workspace,
                           name = 'dataset_crime',
                           description = 'Training data',
                           exist_ok = False
                           )
```

>[!NOTE]
> Jeśli `exist_ok = False` (domyślnie) i podjęto próbę zarejestrowania zestawu danych o takiej samej nazwie jak inny, wystąpi błąd. Ustaw, `True` aby zastąpić istniejące.

## <a name="access-data-in-datasets"></a>Uzyskiwanie dostępu do danych w zestawach DataSet

Zarejestrowane zestawy danych są dostępne lokalnie i zdalnie w klastrach obliczeniowych, takich jak Azure Machine Learning COMPUTE. Aby uzyskać dostęp do zarejestrowanego zestawu danych w ramach eksperymentów, użyj poniższego kodu, aby uzyskać obszar roboczy i zarejestrowany zestaw danych według nazwy.

```Python
workspace = Workspace.from_config()

# See list of datasets registered in workspace.
print(Dataset.list(workspace))

# Get dataset by name
dataset = Dataset.get(workspace, 'dataset_crime')

# Load data into pandas DataFrame
dataset.to_pandas_dataframe()
```

## <a name="next-steps"></a>Kolejne kroki

* [Eksplorowanie i przygotowywanie zestawów danych](how-to-explore-prepare-data.md).
* Przykład korzystania z zestawów danych można znaleźć w przykładowych [notesach](https://aka.ms/dataset-tutorial).
