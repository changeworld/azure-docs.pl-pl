---
title: Tworzenie zestawów danych, aby uzyskać dostęp do danych z zestawami danych usługi Azure ml
titleSuffix: Azure Machine Learning service
description: Dowiedz się, jak utworzyć zestawy danych z różnych źródeł i zarejestrować zestawy danych z obszarem roboczym
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 05/21/2019
ms.openlocfilehash: 021b9035b10ad5a08bca1842e8d0714b33c84271
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2019
ms.locfileid: "66492438"
---
# <a name="create-and-access-datasets-preview-in-azure-machine-learning"></a>Tworzenie i dostęp do zestawów danych (wersja zapoznawcza) w usłudze Azure Machine Learning

W tym artykule dowiesz się, jak utworzyć zestawy danych usługi Azure Machine Learning (wersja zapoznawcza) i jak uzyskać dostęp do danych lokalnych i zdalnych eksperymentów.

Z zarządzanych zestawów danych możesz wykonywać następujące czynności: 
* **Łatwo uzyskiwać dostęp do danych podczas uczenia modelu** bez ponownego łączenia się z podstawowym sklepów

* **Upewnij się, spójności danych i odtwarzaniem** przy użyciu tego samego wskaźnika dla eksperymentów: notesów, ml automatycznych, potoki, interfejs graficzny

* **Udostępnianie danych i współpraca** z innymi użytkownikami

* **Eksplorowanie danych** & zarządzania cyklem życia migawki danych i wersje

* **Porównywanie danych** szkolenia i produkcji


## <a name="prerequisites"></a>Wymagania wstępne

Aby utworzyć i Praca z zestawami danych, potrzebne są:

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji Azure, przed rozpoczęciem utwórz bezpłatne konto. Wypróbuj [bezpłatną lub płatną wersję usługi Azure Machine Learning](https://aka.ms/AMLFree) już dziś.

* [Obszaru roboczego usługi Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/setup-create-workspace)

* [Azure Machine Learning SDK for język Python jest zainstalowany](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py), który zawiera pakiet zestawów danych usługi Azure ml.

> [!Note]
> Niektóre klasy zestawu danych (wersja zapoznawcza) być zależny od [przygotowania danych usługi Azure ml](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py) pakietu (GA). Użytkownicy systemu Linux w ramach tych zajęć są obsługiwane tylko na poniższe dystrybucje:  Red Hat Enterprise Linux, Ubuntu, Fedora i CentOS.

## <a name="data-formats"></a>Formaty danych

Możesz utworzyć zestaw danych usługi Azure Machine uczenia z następujące dane:
+ [delimited](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-delimited-files-path--separator------header--promoteheadersbehavior-allfileshavesameheaders--3---encoding--fileencoding-utf8--0---quoting-false--infer-column-types-true--skip-rows-0--skip-mode--skiplinesbehavior-norows--0---comment-none--include-path-false--archive-options-none-)
+ [dane binarne](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-binary-files-path-)
+ [json](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-json-files-path--encoding--fileencoding-utf8--0---flatten-nested-arrays-false--include-path-false-)
+ [Excel](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-excel-files-path--sheet-name-none--use-column-headers-false--skip-rows-0--include-path-false--infer-column-types-true-)
+ [Parquet](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-parquet-files-path--include-path-false-)
+ [Azure SQL Database](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-sql-query-data-source--query-)
+ [Usługa Azure Data Lake ogólnego. 1](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-sql-query-data-source--query-)

## <a name="create-datasets"></a>Tworzenie zestawów danych 

Możesz wchodzić w interakcje z zestawów danych przy użyciu pakietu zestawów danych usługi Azure ml w [zestawu SDK usługi Azure Machine Learning Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) , zwłaszcza [ `Dataset` klasy](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset(class)?view=azure-ml-py).

### <a name="create-from-local-files"></a>Tworzenie na podstawie plików lokalnych

Ładowanie plików z komputera lokalnego, podając ścieżkę do pliku lub folderu z [ `auto_read_files()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset(class)?view=azure-ml-py#auto-read-files-path--include-path-false-) metody z `Dataset` klasy.  Ta metoda wykonuje następujące czynności, bez konieczności określania typu plików lub analizowania argumentów:

* Wnioskowanie i ustawienie ogranicznik.
* Pomija puste rekordy w górnej części pliku.
* Wnioskowanie i ustawienie wiersz nagłówka.
* Wnioskowanie i konwertowanie typów danych kolumny.

```Python
from azureml.core.dataset import Dataset

dataset = Dataset.auto_read_files('./data/crime.csv')
```

Można również użyć funkcji specyficznych dla pliku, aby jawnie kontrolować podczas analizowania pliku. 


### <a name="create-from-azure-datastores"></a>Tworzenie na podstawie magazynów danych platformy Azure

Aby utworzyć zestawy danych z magazynu danych Azure:

* Sprawdź `contributor` lub `owner` dostęp do zarejestrowanych magazynu danych platformy Azure.

* Importuj [ `Workspace` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) i [ `Datastore` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#definition) i `Dataset` pakiety z zestawu SDK.

```Python
from azureml.core.workspace import Workspace
from azureml.core.datastore import Datastore
from azureml.core.dataset import Dataset

datastore_name = 'your datastore name'

# get existing workspace
workspace = Workspace.from_config()
```

 `get()` Metoda pobiera istniejący magazyn danych w obszarze roboczym.

```
dstore = Datastore.get(workspace, datastore_name)
```

Użyj `from_delimited_files()` metodę, aby przeczytać pliki rozdzielane i tworzenie wyrejestrować zestawu danych.

```Python
# create an in-memory Dataset on your local machine
datapath = dstore.path('data/src/crime.csv')
dataset = Dataset.from_delimited_files(datapath)

# returns the first 5 rows of the Dataset as a pandas Dataframe.
dataset.head(5)
```

## <a name="register-datasets"></a>Rejestrowanie zestawów danych

Aby ukończyć proces tworzenia, zarejestruj zestawów danych przy użyciu obszaru roboczego:

Użyj [ `register()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#register-workspace--name--description-none--tags-none--visible-true--exist-ok-false--update-if-exist-false-) metody do rejestrowania zestawów danych do obszaru roboczego, dzięki czemu mogą być udostępniane innym osobom i ponownie wykorzystać w odniesieniu do różnych doświadczeń.

```Python
dataset = dataset.register(workspace = workspace,
                           name = 'dataset_crime',
                           description = 'Training data',
                           exist_ok = False
                           )
```

>[!NOTE]
> Jeśli `exist_ok = False` (ustawienie domyślne), i spróbujesz zarejestrować zestaw danych o takiej samej nazwie jak inny, wystąpi błąd. Ustaw `True` zastąpić istniejące.

## <a name="access-data-in-datasets"></a>Dostęp do danych w zestawach danych

Zarejestrowane zestawy danych są dostępne i użyciu lokalnie, zdalnie i w klastrach obliczeniowych, takich jak moc obliczeniowa usługi Azure Machine Learning. Aby ponownie użyć zarejestrowanego zestawu danych między eksperymentów i środowiskach obliczeniowych, użyj następującego kodu, można pobrać według nazwy swojego obszaru roboczego i zarejestrowanego zestawu danych.

```Python
workspace = Workspace.from_config()

# See list of datasets registered in workspace.
Dataset.list(workspace)

# Get dataset by name
dataset = workspace.datasets['dataset_crime']
```

## <a name="next-steps"></a>Kolejne kroki

* [Eksploruj i przygotować zestawów danych](how-to-explore-prepare-data.md).
* [Zarządzanie cyklem życia definicji zestawu danych](how-to-manage-dataset-definitions.md).
* Przykład przy użyciu zestawów danych, zobacz [przykładowy notesów](https://aka.ms/dataset-tutorial).
