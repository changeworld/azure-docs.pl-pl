---
title: Przechowywanie wersji zestawu danych
titleSuffix: Azure Machine Learning service
description: Dowiedz się, jak najlepiej określić wersję zestawów danych i jak działa obsługa wersji przy użyciu potoków uczenia maszynowego.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: sihhu
ms.reviewer: nibaccam
ms.date: 11/04/2019
ms.custom: ''
ms.openlocfilehash: 426a93473b969c166a847374d1b4c039055e92d5
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2019
ms.locfileid: "73716093"
---
# <a name="version-and-track-datasets-in-experiments"></a>Wersje i śledzenie zestawów danych w eksperymentach
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Ten artykuł zawiera informacje na temat wersji i śledzenia Azure Machine Learning zestawów danych w celu uzyskania odtwarzalności. Wersja zestawu danych to sposób zakładania stanu danych, aby można było zastosować określoną wersję zestawu danych do przyszłych eksperymentów.

Typowe scenariusze obsługi wersji:

* Gdy nowe dane są dostępne do ponownego szkolenia
* W przypadku stosowania różnych metod przygotowywania lub opracowywania funkcji

## <a name="prerequisites"></a>Wymagania wstępne

W celu skorzystania z tego samouczka potrzebne są następujące elementy:

- [Azure Machine Learning zestawu SDK dla języka Python](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py). Ten zestaw SDK zawiera pakiet [Azure-DataSets](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset?view=azure-ml-py) .
    
- [Obszar roboczy Azure Machine Learning](concept-workspace.md). Pobierz istniejący kod lub [Utwórz nowy obszar roboczy](how-to-manage-workspace.md).

    ```Python
    import azureml.core
    from azureml.core import Workspace
    
    ws = Workspace.from_config()
    ```
- [Zestaw danych Azure Machine Learning](how-to-create-register-datasets.md).

<a name="register"></a>

## <a name="register-and-retrieve-dataset-versions"></a>Rejestrowanie i pobieranie wersji zestawu danych

Rejestrując zestaw danych, można w wersji, ponownie użyć i udostępnić go w eksperymentach i współpracownikom. Można zarejestrować wiele zestawów danych w tej samej nazwie i pobrać określoną wersję według nazwy i numeru wersji.

### <a name="register-a-dataset-version"></a>Rejestrowanie wersji zestawu danych

Poniższy kod rejestruje nową wersję zestawu danych `titanic_ds` przez ustawienie parametru `create_new_version` na `True`. Jeśli nie ma żadnych istniejących `titanic_ds` DataSet zarejestrowanych w obszarze roboczym, kod utworzy nowy zestaw danych o nazwie `titanic_ds` i ustawi jego wersję na 1.

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

Podczas tworzenia wersji zestawu danych *nie* jest tworzona dodatkowa kopia danych z obszarem roboczym. Ze względu na to, że zestawy danych są odwołaniami do dane w usłudze Storage, masz pojedyncze Źródło prawdy, zarządzane przez usługę magazynu.

>[!IMPORTANT]
> Jeśli dane, do których odwołuje się zestaw danych, zostaną zastąpione lub usunięte, wywołanie określonej wersji zestawu *danych nie powoduje przywrócenia zmiany* .

Podczas ładowania danych z zestawu danych bieżąca zawartość danych, do której odwołuje się zestaw danych, jest zawsze ładowana. Jeśli chcesz upewnić się, że każda wersja zestawu danych jest powtarzalna, zalecamy, aby nie modyfikować zawartości danych, do której odwołuje się wersja zestawu danych. Po dojściu do nowych danych Zapisz nowe pliki danych w osobnym folderze danych, a następnie utwórz nową wersję zestawu danych, aby dołączyć dane z tego nowego folderu.

Na poniższym obrazie i przykładowym kodzie przedstawiono zalecany sposób struktury folderów danych i tworzenia wersji zestawu danych, które odwołują się do tych folderów:

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

Zestawu danych można użyć jako danych wejściowych i wyjściowych każdego etapu potoku Machine Learning. Po ponownym uruchomieniu potoków dane wyjściowe poszczególnych etapów potoku są rejestrowane jako nowa wersja zestawu danych.

Ponieważ Machine Learning potoki wypełniają dane wyjściowe każdego kroku do nowego folderu za każdym razem, gdy potok zostanie ponownie skonfigurowany, przywracane wersje zestawów danych są odtwarzalne.

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

Dla każdego Machine Learning eksperymentu można łatwo śledzić zestawy danych używane jako dane wejściowe za pomocą obiektu `Run` zarejestrowanego modelu.

Użyj następującego kodu, aby zarejestrować modele z zestawami danych:

```Python
model = run.register_model(model_name='keras-mlp-mnist',
                           model_path=model_path,
                           datasets =[('training data',train_dataset)])
```

Po zarejestrowaniu można zobaczyć listę modeli zarejestrowanych w zestawie danych przy użyciu języka Python lub [Azure Machine Learning Studio](https://ml.azure.com/).

Poniższy kod używa metody [`get_details()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#get-details--) do śledzenia, które wejściowe zestawy danych były używane z przebiegiem eksperymentu:

```Python
# get input datasets
inputs = run.get_details()['inputDatasets']
train_dataset = inputs[0]['dataset']

# list the files referenced by train_dataset
train_dataset.to_path()
```

`input_datasets` z eksperymentów można również znaleźć przy użyciu [Azure Machine Learning Studio](https://ml.azure.com/). 

Na poniższej ilustracji przedstawiono, gdzie znaleźć zestaw danych wejściowych eksperymentu na Azure Machine Learning Studio. Na potrzeby tego przykładu przejdź do okienka **eksperymenty** i Otwórz kartę **Właściwości** dla określonego uruchomienia eksperymentu, `keras-mnist`.

![Wejściowe zestawy danych](media/how-to-version-datasets/input-datasets.png)

Możesz również znaleźć modele, które używały zestawu danych. Poniższy widok pochodzi z okienka **zestawy danych** w obszarze **zasoby**. Wybierz zestaw danych, a następnie wybierz kartę **modele** , aby wyświetlić listę modeli, które korzystają z tego zestawu danych. 

![Modele wejściowych zestawów danych](media/how-to-version-datasets/dataset-models.png)

## <a name="next-steps"></a>Następne kroki

* [Uczenie z zestawami danych](how-to-train-with-datasets.md)
* [Więcej przykładowych notesów zestawu danych](https://aka.ms/dataset-tutorial)
