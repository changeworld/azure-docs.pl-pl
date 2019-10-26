---
title: Przechowywanie wersji zestawu danych
titleSuffix: Azure Machine Learning service
description: Zapoznaj się z najlepszymi rozwiązaniami dotyczącymi wersji zestawów danych i sposobem działania wersji z potokami uczenia maszynowego
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: sihhu
ms.reviewer: nibaccam
ms.date: 10/25/2019
ms.custom: ''
ms.openlocfilehash: a361800623796cb3bc26a47c4f8f532503836124
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/24/2019
ms.locfileid: "72902004"
---
# <a name="version-and-track-datasets-in-experiments"></a>Wersje i śledzenie zestawów danych w eksperymentach

W tym instruktażu dowiesz się, jak uzyskać informacje o wersji i śledzeniu Azure Machine Learning zestawów danych w celu uzyskania odtwarzalności. Wersja zestawu danych to sposób zakładania stanu danych, dzięki czemu można zastosować określoną wersję zestawu danych do przyszłych eksperymentów.

Typowe scenariusze, które należy wziąć pod uwagę w przypadku przechowywania wersji:

* Gdy nowe dane są dostępne do ponownego szkolenia.
* W przypadku stosowania różnych metod przygotowywania lub opracowywania funkcji.

## <a name="prerequisites"></a>Wymagania wstępne

W tym celu należy:

- [Zestaw Azure Machine Learning SDK dla języka Python](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py), który obejmuje pakiet usługi [Azure DataSets](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset?view=azure-ml-py) .
    
- [Obszar roboczy Azure Machine Learning](concept-workspace.md). Pobierz istniejący z poniższym kodem lub [Utwórz nowy obszar roboczy](how-to-manage-workspace.md).

    ```Python
    import azureml.core
    from azureml.core import Workspace
    
    ws = Workspace.from_config()
    ```
- [Zestaw danych Azure Machine Learning](how-to-create-register-datasets.md).

<a name="register"></a>

## <a name="register-and-retrieve-dataset-versions"></a>Rejestrowanie i pobieranie wersji zestawu danych

Zarejestrowanie zestawu danych umożliwia jego wersję, ponowne użycie i udostępnienie w eksperymentach i współpracownikom. Można zarejestrować wiele zestawów danych w tej samej nazwie i pobrać określoną wersję według nazwy i numeru wersji.

### <a name="register-a-dataset-version"></a>Rejestrowanie wersji zestawu danych

Poniższy kod rejestruje nową wersję zestawu danych, `titanic_ds`, ustawiając parametr `create_new_version` na `True`. Jeśli nie ma żadnych istniejących `titanic_ds` zarejestrowanych w obszarze roboczym, utworzy nowy zestaw danych o nazwie `titanic_ds` i ustawi jego wersję na 1.

```Python
titanic_ds = titanic_ds.register(workspace = workspace,
                                 name = 'titanic_ds',
                                 description = 'titanic training data',
                                 create_new_version = True)
```

### <a name="retrieve-a-dataset-by-name"></a>Pobieranie zestawu danych według nazwy

Domyślnie metoda [get_by_name ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#get-by-name-workspace--name--version--latest--) w klasie `Dataset` zwraca najnowszą wersję zestawu danych zarejestrowanego w obszarze roboczym. 

Poniższy kod pobiera wersję 1 `titanic_ds` zestawu danych.

```Python
from azureml.core import Dataset
# Get a dataset by name and version number
titanic_ds = Dataset.get_by_name(workspace = workspace,
                                 name = 'titanic_ds', 
                                 version = 1)
```

<a name="best-practice"></a>

## <a name="versioning-best-practice"></a>Najlepsze rozwiązanie w zakresie wersji

Podczas tworzenia wersji zestawu danych **nie** jest tworzona dodatkowa kopia danych z obszarem roboczym. Zestawy danych są odwołaniami do dane w usłudze magazynu, więc masz tylko jedno źródło prawdziwie zarządzane przez usługę magazynu. 

>[!IMPORTANT]
> Jeśli dane, do których odwołuje się zestaw danych, zostaną zastąpione lub usunięte, wywołanie określonej wersji zestawu danych nie może przywrócić zmiany. 

Podczas ładowania danych z zestawu danych zawsze zostanie załadowana bieżąca zawartość danych, do której odwołuje się zestaw danych. Jeśli chcesz zapewnić odtwarzalność poszczególnych wersji zestawu danych, zalecamy, aby nie modyfikować zawartości danych, do której odwołuje się wersja zestawu danych. Gdy nowe dane są dostępne, Zapisz nowe pliki danych w osobnym folderze danych i Utwórz nową wersję zestawu danych, aby dołączyć dane z tego nowego folderu danych.

Na poniższym obrazie i przykładowym kodzie przedstawiono zalecany sposób struktury folderów danych i Tworzenie wersji zestawu danych odwołujących się do tych folderów.

![Struktura folderów](media/how-to-version-datasets/folder-image.png)

```Python
from azureml.core import Dataset

# get the default datastore of the workspace
datastore = workspace.get_default_datastore()

# create & register weather_ds version 1 pointing to all files in the folder of week 27
datastore_path1 = [(datastore, 'Weather/week 27')]
dataset1 = Dataset.File.from_files(path=datastore_path1)
dataset1.register(workspace = workspace,
                  name = 'weather_ds',
                  description = 'weather data in week 27',
                  create_new_version = True)

# create & register weather_ds version 2 pointing to all files in the folder of week 27 and 28
datastore_path2 = [(datastore, 'Weather/week 27'), (datastore, 'Weather/week 28')]
dataset2 = Dataset.File.from_files(path = datastore_path2)
dataset2.register(workspace = workspace,
                  name = 'weather_ds',
                  description = 'weather data in week 27, 28',
                  create_new_version = True)

```

<a name="pipeline"></a>

## <a name="version-a-pipeline-output-dataset"></a>Wersja zestawu danych wyjściowych potoku

Zestawu danych można użyć jako danych wejściowych i wyjściowych poszczególnych etapów potoku uczenia maszynowego (ML). Po ponownym uruchomieniu potoków dane wyjściowe poszczególnych etapów potoku zostaną zarejestrowane jako nowa wersja zestawu danych. 

Ponieważ potoki ML wypełniają dane wyjściowe każdego kroku do nowego folderu za każdym razem, gdy potok jest ponownie uruchamiany, zestawy danych wyjściowych z wersjami są odtwarzalne.

```Python
from azureml.core import Dataset
from azureml.pipeline.steps import PythonScriptStep
from azureml.pipeline.core import Pipeline, PipelineData

# get input dataset 
input_ds = Dataset.get_by_name(workspace, 'weather_ds')

# register pipeline output as dataset
output_ds = PipelineData('prepared_weather_ds', datastore=datastore).as_dataset()
output_ds = output_ds.register(name='prepared_weather_ds', create_new_version=True)

# configure pipeline step to use dataset as the input and output
prep_step = PythonScriptStep(script_name="prepare.py",
                             inputs=[input_ds.as_named_input('weather_ds')],
                             outputs=[output_ds],
                             compute_target=compute_target,
                             source_directory=project_folder)
```

<a name="track"></a>

## <a name="track-datasets-in-experiments"></a>Śledzenie zestawów danych w eksperymentach

Dla każdego eksperymentu uczenia maszynowego można łatwo śledzić zestawy danych używane jako dane wejściowe za pomocą obiektu `Run` zarejestrowanego modelu.

Użyj poniższego kodu, aby zarejestrować modele z zestawami danych.

```Python
model = run.register_model(model_name='keras-mlp-mnist',
                           model_path=model_path,
                           datasets =[('training data',train_dataset)])
```

Po zarejestrowaniu można zobaczyć listę modeli zarejestrowanych w zestawie danych przy użyciu języka Python lub [strony docelowej obszaru roboczego](https://ml.azure.com/).

Poniższy kod używa metody [`get_details()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#get-details--) do śledzenia, które wejściowe zestawy danych zostały użyte w tym przebiegu eksperymentu.

```Python
# get input datasets
inputs = run.get_details()['inputDatasets']
train_dataset = inputs[0]['dataset']

# list the files referenced by train_dataset
train_dataset.to_path()
```

`input_datasets` z eksperymentów można również znaleźć za pomocą [strony docelowej obszaru roboczego (wersja zapoznawcza)](https://ml.azure.com/). 

Na poniższej ilustracji przedstawiono, gdzie znaleźć zestaw danych wejściowych eksperymentu na stronie docelowej obszaru roboczego. Na potrzeby tego przykładu przejdź do okienka **eksperymenty** , a następnie otwórz kartę **Właściwości** dla określonego uruchomienia eksperymentu `keras-mnist`. 

![Wejściowe zestawy danych](media/how-to-version-datasets/input-datasets.png)

Modele, które używały zestawu danych, można również znaleźć na stronie docelowej obszaru roboczego. Poniższy widok pochodzi z bloku zestawy danych w obszarze zasoby. Wybierz zestaw danych i przejdź do karty modele, aby wyświetlić listę modeli korzystających z tego zestawu danych. 

![Modele wejściowych zestawów danych](media/how-to-version-datasets/dataset-models.png)

## <a name="next-steps"></a>Następne kroki

* [Uczenie się z zestawami danych](how-to-train-with-datasets.md).
* [Więcej przykładowych notesów zestawów danych](https://aka.ms/dataset-tutorial).
