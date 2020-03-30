---
title: Przechowywanie wersji zestawu danych
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak najlepiej wersją zestawów danych i jak działa przechowywanie wersji z potokami uczenia maszynowego.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
ms.reviewer: nibaccam
ms.date: 03/09/2020
ms.custom: ''
ms.openlocfilehash: acbd2e3ba756255cbc69ae8a7b7ad62d7a1c1c5a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79528476"
---
# <a name="version-and-track-datasets-in-experiments"></a>Wersje i śledzenie zestawów danych w eksperymentach
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

W tym artykule dowiesz się, jak wersji i śledzić zestawy danych usługi Azure Machine Learning dla odtwarzalności. Przechowywanie wersji zestawu danych to sposób do zakładki stanu danych, dzięki czemu można zastosować określoną wersję zestawu danych dla przyszłych eksperymentów.

Typowe scenariusze przechowywania wersji:

* Gdy dostępne są nowe dane dotyczące przekwalifikowania
* Gdy stosujesz różne metody przygotowania danych lub inżynierii funkcji

## <a name="prerequisites"></a>Wymagania wstępne

W celu skorzystania z tego samouczka potrzebne są następujące elementy:

- [Zainstalowano zestaw SDK usługi Azure Machine Learning dla języka Python](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py). Ten zestaw SDK zawiera pakiet [zestawów danych azureml.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset?view=azure-ml-py)
    
- [Obszar roboczy usługi Azure Machine Learning](concept-workspace.md). Pobierz istniejący, uruchamiając następujący kod lub [utwórz nowy obszar roboczy](how-to-manage-workspace.md).

    ```Python
    import azureml.core
    from azureml.core import Workspace
    
    ws = Workspace.from_config()
    ```
- [Zestaw danych usługi Azure Machine Learning](how-to-create-register-datasets.md).

<a name="register"></a>

## <a name="register-and-retrieve-dataset-versions"></a>Rejestrowanie i pobieranie wersji zestawów danych

Rejestrując zestaw danych, można wersji, ponownego użycia i udostępnić go w eksperymentach i współpracowników. Można zarejestrować wiele zestawów danych pod tą samą nazwą i pobrać określoną wersję według nazwy i numeru wersji.

### <a name="register-a-dataset-version"></a>Rejestrowanie wersji zestawu danych

Poniższy kod rejestruje nową wersję `titanic_ds` zestawu danych, `create_new_version` ustawiając parametr na `True`. Jeśli nie ma `titanic_ds` istniejącego zestawu danych zarejestrowanego w obszarze roboczym, `titanic_ds` kod tworzy nowy zestaw danych o nazwie i ustawia jego wersję na 1.

```Python
titanic_ds = titanic_ds.register(workspace = workspace,
                                 name = 'titanic_ds',
                                 description = 'titanic training data',
                                 create_new_version = True)
```
Można również zarejestrować nową wersję zestawu danych na 

### <a name="retrieve-a-dataset-by-name"></a>Pobieranie zestawu danych według nazwy

Domyślnie metoda [get_by_name()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#get-by-name-workspace--name--version--latest--) w `Dataset` klasie zwraca najnowszą wersję zestawu danych zarejestrowanego w obszarze roboczym. 

Poniższy kod pobiera wersję `titanic_ds` 1 zestawu danych.

```Python
from azureml.core import Dataset
# Get a dataset by name and version number
titanic_ds = Dataset.get_by_name(workspace = workspace,
                                 name = 'titanic_ds', 
                                 version = 1)
```

<a name="best-practice"></a>

## <a name="versioning-best-practice"></a>Najlepsze praktyki dotyczące przechowywania wersji

Podczas tworzenia wersji zestawu danych *nie* tworzysz dodatkowej kopii danych z obszarem roboczym. Ponieważ zestawy danych są odwołania do danych w usłudze magazynu, masz jedno źródło prawdy, zarządzane przez usługę magazynu.

>[!IMPORTANT]
> Jeśli dane, do których odwołuje się zestaw danych, zostaną zastąpione lub usunięte, wywołanie określonej wersji zestawu danych *nie* powoduje ponownego przywrócenia zmiany.

Podczas ładowania danych z zestawu danych bieżąca zawartość danych, do której odwołuje się zestaw danych, jest zawsze ładowana. Jeśli chcesz upewnić się, że każda wersja zestawu danych jest odtwarzalna, zaleca się, aby nie modyfikować zawartości danych, do której odwołuje się wersja zestawu danych. Po wprowadzeniu nowych danych zapisz nowe pliki danych w oddzielnym folderze danych, a następnie utwórz nową wersję zestawu danych, aby uwzględnić dane z tego nowego folderu.

Na poniższej ilustracji i przykładowym kodzie pokazano zalecany sposób struktury folderów danych i tworzenia wersji zestawów danych, które odwołują się do tych folderów:

![Struktura folderów](./media/how-to-version-track-datasets/folder-image.png)

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

Można użyć zestawu danych jako danych wejściowych i wyjściowych każdego kroku potoku uczenia maszynowego. Po ponownym uruchomieniu potoków, dane wyjściowe każdego kroku potoku jest rejestrowany jako nowa wersja zestawu danych.

Ponieważ potoki uczenia maszynowego wypełniają dane wyjściowe każdego kroku do nowego folderu za każdym razem, gdy potok jest ponownie wprowadzany, wersjonowane wyjściowe zestawy danych są powtarzalne. Dowiedz się więcej o [zestawach danych w potokach](how-to-create-your-first-pipeline.md#steps).

```Python
from azureml.core import Dataset
from azureml.pipeline.steps import PythonScriptStep
from azureml.pipeline.core import Pipeline, PipelineData
from azureml.core. runconfig import CondaDependencies, RunConfiguration

# get input dataset 
input_ds = Dataset.get_by_name(workspace, 'weather_ds')

# register pipeline output as dataset
output_ds = PipelineData('prepared_weather_ds', datastore=datastore).as_dataset()
output_ds = output_ds.register(name='prepared_weather_ds', create_new_version=True)

conda = CondaDependencies.create(
    pip_packages=['azureml-defaults', 'azureml-dataprep[fuse,pandas]'], 
    pin_sdk_version=False)

run_config = RunConfiguration()
run_config.environment.docker.enabled = True
run_config.environment.python.conda_dependencies = conda

# configure pipeline step to use dataset as the input and output
prep_step = PythonScriptStep(script_name="prepare.py",
                             inputs=[input_ds.as_named_input('weather_ds')],
                             outputs=[output_ds],
                             runconfig=run_config,
                             compute_target=compute_target,
                             source_directory=project_folder)
```

<a name="track"></a>

## <a name="track-datasets-in-experiments"></a>Śledzenie zestawów danych w eksperymentach

Dla każdego eksperymentu uczenia maszynowego można łatwo śledzić zestawy `Run` danych używane jako dane wejściowe za pośrednictwem obiektu eksperymentu.

Poniższy kod używa [`get_details()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#get-details--) metody do śledzenia, które wejściowe zestawy danych zostały użyte z przebiegiem eksperymentu:

```Python
# get input datasets
inputs = run.get_details()['inputDatasets']
input_dataset = inputs[0]['dataset']

# list the files referenced by input_dataset
input_dataset.to_path()
```

Można również znaleźć `input_datasets` z eksperymentów https://ml.azure.com/za pomocą . 

Na poniższej ilustracji pokazano, gdzie można znaleźć wejściowy zestaw danych eksperymentu w studio usługi Azure Machine Learning. W tym przykładzie przejdź do okienka **Eksperymenty** i otwórz kartę **Właściwości** `keras-mnist`dla określonego przebiegu eksperymentu.

![Wejściowe zestawy danych](./media/how-to-version-track-datasets/input-datasets.png)

Użyj następującego kodu, aby zarejestrować modele z zestawami danych:

```Python
model = run.register_model(model_name='keras-mlp-mnist',
                           model_path=model_path,
                           datasets =[('training data',train_dataset)])
```

Po rejestracji możesz zobaczyć listę modeli zarejestrowanych w zestawie danych https://ml.azure.com/za pomocą języka Python lub przejść do .

Poniższy widok pochodzi z okienka **Zestawy danych** w obszarze **Zasoby**. Zaznacz zestaw danych, a następnie wybierz kartę **Modele** dla listy modeli zarejestrowanych w zestawie danych. 

![Modele wejściowych zestawów danych](./media/how-to-version-track-datasets/dataset-models.png)

## <a name="next-steps"></a>Następne kroki

* [Szkolenie przy użyciu zestawów danych](how-to-train-with-datasets.md)
* [Więcej przykładowych notesów zestawu danych](https://aka.ms/dataset-tutorial)
