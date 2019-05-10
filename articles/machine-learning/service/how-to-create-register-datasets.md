---
title: Tworzenie i rejestrowanie zestawów danych przy użyciu obszaru roboczego
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
ms.date: 05/02/19
ms.openlocfilehash: 938f13524b22f34f4becc936885d1611cb854df1
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/09/2019
ms.locfileid: "65510508"
---
# <a name="create-and-register-azure-machine-learning-datasets-preview"></a>Tworzenie i rejestrowanie usługi Azure Machine Learning z zestawami danych (wersja zapoznawcza)

W tym artykule dowiesz się, tworzenie i rejestrowanie zestawów danych oraz jak uzyskiwać do nich dostęp do ponownego wykorzystania w lokalnych i zdalnych eksperymentów przepływy pracy usługi Azure Machine Learning.

Zestawy Azure Machine Learning danych (wersja zapoznawcza) ułatwiają dostęp i pracować z danymi. Zestawy danych zarządzania danymi w różnych scenariuszach, takich jak szkolenia i modelu tworzenia potoku. Za pomocą [zestawu SDK usługi Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py), można pracować z danymi w popularnych formatach, dostęp do podstawowego magazynu, zapoznaj się z i przygotowywać dane, zarządzać cyklem życia definicje różnych zestawów danych i porównać między zestawów danych używanych w szkolenie i w środowisku produkcyjnym.

## <a name="prerequisites"></a>Wymagania wstępne

Aby utworzyć i zarejestrować zestawy danych potrzebne są:

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji Azure, przed rozpoczęciem utwórz bezpłatne konto. Wypróbuj [bezpłatną lub płatną wersję usługi Azure Machine Learning](https://aka.ms/AMLFree) już dziś.

* Obszar roboczy usługi Azure Machine Learning. Zobacz [Utwórz obszar roboczy usługi Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/setup-create-workspace).

* Aplikację Azure Machine Learning zestawu SDK dla języka Python. Aby zainstalować lub aktualizacji do najnowszej wersji zestawu SDK, zobacz [Zainstaluj lub zaktualizuj zestaw SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).

## <a name="create-datasets-from-local-files"></a>Tworzenie zestawów danych z plików lokalnych

Ładowanie plików z komputera lokalnego, podając ścieżkę do pliku lub folderu z [ `auto_read_files()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset(class)?view=azure-ml-py#auto-read-files-path--include-path-false-) metody z `Dataset` klasy.  Ta metoda wykonuje następujące czynności, bez konieczności określania typu plików lub analizowania argumentów:

* Wnioskowanie i ustawienie ogranicznik.
* Pomija puste rekordy w górnej części pliku.
* Wnioskowanie i ustawienie wiersz nagłówka.
* Wnioskowanie i konwertowanie typów danych kolumny.

```Python
from azureml.core.dataset import Dataset

dataset = Dataset.auto_read_files('./data/crime.csv')
```

Można również użyć funkcji specyficznych dla pliku, aby jawnie kontrolować podczas analizowania pliku. Obecnie usługa obsługuje zestaw SDK zestawów danych [rozdzielany](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-delimited-files-path--separator------header--promoteheadersbehavior-allfileshavesameheaders--3---encoding--fileencoding-utf8--0---quoting-false--infer-column-types-true--skip-rows-0--skip-mode--skiplinesbehavior-norows--0---comment-none--include-path-false--archive-options-none-), [Excel](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-excel-files-path--sheet-name-none--use-column-headers-false--skip-rows-0--include-path-false--infer-column-types-true-), [Parquet](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-parquet-files-path--include-path-false-), [binarne](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-binary-files-path-), i [json](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-json-files-path--encoding--fileencoding-utf8--0---flatten-nested-arrays-false--include-path-false-) formaty plików.

## <a name="create-datasets-from-azure-datastores"></a>Tworzenie zestawów danych z magazynów danych platformy Azure

Aby utworzyć zestawy danych z magazynu danych platformy Azure, należy koniecznie:

* Sprawdź, czy masz współautora lub właściciela dostęp do zarejestrowanych magazynu danych platformy Azure.

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

||ID|Liczba przypadków|Date|Blokuj|IUCR|Typ podstawowy|Opis|Opis lokalizacji|Aresztowania|Krajowych|Przyciski ...|Lej|Obszar społeczności|FBI kodu|Współrzędna x|Współrzędna Y|Rok|Aktualizacja:|Szerokość geograficzna|Długość geograficzna|Lokalizacja|
|--|--|---|---|---|---|----|------|-------|------|-----|---|----|----|-----|-----|------|----|-----|----|----|-----
|0|10498554|HZ239907|4/4/2016 23:56|007XX E 111TH ST|1153|PRAKTYKI OSZUKAŃCZYM|KRADZIEŻE TOŻSAMOŚCI FINANSOWYCH ZA POŚREDNICTWEM 300 USD|INNE|WARTOŚĆ FALSE|WARTOŚĆ FALSE|...|9|50|11|1183356|1831503|2016|5/11/2016 15:48|41.69283384|-87.60431945|(41.692833841, -87.60431945)|
1|10516598|HZ258664|4/15/2016 17:00|082XX S MARSHFIELD AVE|890|PRZED KRADZIEŻĄ| OD TWORZENIA|W MIEJSCU ZAMIESZKANIA UŻYTKOWNIKA|WARTOŚĆ FALSE|WARTOŚĆ FALSE|...|21|71|6|1166776|1850053|2016|5/12/2016 15:48|41.74410697|-87.66449429|(41.744106973, -87.664494285)
2|10519196|HZ261252|4/15/2016 10:00|104XX S SACRAMENTO AVE|1154|PRAKTYKI OSZUKAŃCZYM|KRADZIEŻE TOŻSAMOŚCI FINANSOWYCH 300 USD I W OBSZARZE|W MIEJSCU ZAMIESZKANIA UŻYTKOWNIKA|WARTOŚĆ FALSE|WARTOŚĆ FALSE|...|19|74|11|||2016|5/12/2016 15:50
3|10519591|HZ261534|4/15/2016 9:00|113XX APISZ PIESKÓW|1120|PRAKTYKI OSZUKAŃCZYM|FAŁSZOWANIE|W MIEJSCU ZAMIESZKANIA UŻYTKOWNIKA|WARTOŚĆ FALSE|WARTOŚĆ FALSE|...|9|49|10|||2016|5/13/2016 15:51
4|10534446|HZ277630|4/15/2016 10:00|055XX N KEDZIE AVE|890|PRZED KRADZIEŻĄ|OD TWORZENIA|SZKOŁY, PUBLICZNEJ I TWORZENIE|WARTOŚĆ FALSE|WARTOŚĆ FALSE|...|40|13|6|||2016|5/25/2016 15:59|

## <a name="register-your-datasets-with-workspace"></a>Rejestrowanie zestawów danych z obszarem roboczym

Użyj [ `register()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#register-workspace--name--description-none--tags-none--visible-true--exist-ok-false--update-if-exist-false-) metody do rejestrowania zestawów danych do obszaru roboczego na potrzeby udostępniania i ponowne użycie w danej organizacji oraz w różnych doświadczeń.

```Python
dataset = dataset.register(workspace = workspace,
                           name = 'dataset_crime',
                           description = 'Training data',
                           exist_ok = False
                           )
```

>[!NOTE]
> Domyślne ustawienie parametru dla `register()` jest `exist_ok = False`. Jeśli spróbujesz zarejestrować zestaw danych o takiej samej nazwie, nie zmieniając to ustawienie powoduje błąd.

`register()` Metoda zwraca już zarejestrowanego zestawu danych z ustawieniem parametru `exist_ok = True`.

```Python
dataset = dataset.register(workspace = workspace,
                           name = 'dataset_crime',
                           description = 'Training data',
                           exist_ok = True
                           )
```

Użyj `list()` do wyświetlenia wszystkich zarejestrowanych zestawów danych w obszarze roboczym.

```Python
Dataset.list(workspace_name)
```

Powyższy kod wyniki będą następujące:

```Python
[Dataset(Name: dataset_crime,
         Workspace: workspace_name)]
```

## <a name="access-datasets-in-workspace"></a>Dostęp do zestawów danych w obszarze roboczym

Zarejestrowane zestawy danych są dostępne i użyciu lokalnie, zdalnie i w klastrach obliczeniowych, takich jak moc obliczeniowa usługi Azure Machine Learning. Aby ponownie użyć zarejestrowanego zestawu danych między eksperymentów i środowiskach obliczeniowych, użyj następującego kodu, można pobrać według nazwy swojego obszaru roboczego i zarejestrowanego zestawu danych.

```Python
workspace = Workspace.from_config()

dataset = workspace.datasets['dataset_crime']
```

## <a name="next-steps"></a>Kolejne kroki

* [Eksploruj i przygotować zestawów danych](how-to-explore-prepare-data.md).
* [Zarządzać cyklem życia definicji zestawu danych](how-to-manage-dataset-definitions.md).
* Przykład przy użyciu zestawów danych, zobacz [przykładowy notesów](https://aka.ms/dataset-tutorial).
