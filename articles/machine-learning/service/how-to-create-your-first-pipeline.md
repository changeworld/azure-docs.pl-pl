---
title: Tworzenie i uruchamianie potoków uczenia Maszynowego — usługa Azure Machine Learning
description: Tworzenie i uruchamianie usługi machine learning potoku z zestawem Azure Machine Learning SDK dla języka Python.  Potoki są używane do tworzenia i zarządzania nimi przepływy pracy tej fazy uczenia maszynowego razem Połącz (ML), takich jak przygotowywanie danych, szkolenie modelu, wdrożenie modelu i wnioskowania.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: sgilley
ms.author: sanpil
author: sanpil
ms.date: 12/04/2018
ms.custom: seodec12
ms.openlocfilehash: 0119e60a6493ed13b04b36fc5fccc7227503fe0a
ms.sourcegitcommit: 698ba3e88adc357b8bd6178a7b2b1121cb8da797
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/07/2018
ms.locfileid: "53015079"
---
# <a name="create-and-run-a-machine-learning-pipeline-using-azure-machine-learning-sdk"></a>Tworzenie i uruchamianie potoku uczenia maszynowego, przy użyciu zestawu SDK usługi Azure Machine Learning

W tym artykule dowiesz się, jak tworzenie, publikowanie, uruchom i Śledź [potoku uczenia maszynowego](concept-ml-pipelines.md) przy użyciu [zestawu SDK usługi Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).  Te potoki pomagają tworzyć i zarządzać nimi przepływy pracy, które łączyć różnych machine learning faz. Każdej fazy potoku, takich jak przygotowywanie danych i szkoleń modelowych może zawierać jeden lub więcej kroków.

Potoki tworzenia są widoczne dla członków usługi Azure Machine Learning [obszaru roboczego](how-to-manage-workspace.md). 

Potoki Użyj zdalnego obliczeniowych elementów docelowych dla obliczeń i magazynu pośredniego i końcowego dane skojarzone z tego potoku.  Potoki można odczytu i zapisu danych do i z obsługiwanych [usługi Azure storage](https://docs.microsoft.com/azure/storage/) lokalizacji.


## <a name="prerequisites"></a>Wymagania wstępne

* Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://aka.ms/AMLfree).

* [Konfigurowanie środowiska projektowego](how-to-configure-environment.md) instalacji zestawu SDK usługi Azure Machine Learning.

* Tworzenie [obszaru roboczego usługi Azure Machine Learning](how-to-configure-environment.md#workspace) do przechowywania wszystkich zasobów potoku. 

 ```python
 ws = Workspace.create(
     name = '<workspace-name>',
     subscription_id = '<subscription-id>',
     resource_group = '<resource-group>',
     location = '<workspace_region>',
     exist_ok = True)
 ```

## <a name="set-up-machine-learning-resources"></a>Konfigurowanie zasobów w machine learning

Utwórz zasoby wymagane do uruchomienia potoku:

* Skonfiguruj Magazyn danych umożliwia dostęp do danych, potrzebne w etapach potoku.

* Konfigurowanie `DataReference` obiektu, aby wskazywał dane są przechowywane jest dostępny w magazynie danych.

* Konfigurowanie [celów obliczeń](concept-azure-machine-learning-architecture.md#compute-target) uruchamiania etapów potoku.

### <a name="set-up-a-datastore"></a>Skonfiguruj Magazyn danych
Magazyn danych przechowuje dane dla potoku w celu uzyskania dostępu do.  Każdy obszar roboczy ma domyślny magazyn danych. Możesz zarejestrować dodatkowe magazynów danych. 

Po utworzeniu obszaru roboczego, [usługi Azure file storage](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) i [magazynu obiektów blob](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction) są dołączone do obszaru roboczego domyślnie.  Azure file storage is została "datastore domyślna" dla obszaru roboczego, ale można również użyć magazynu obiektów blob jako magazyn danych.  Dowiedz się więcej o [opcji usługi Azure storage](https://docs.microsoft.com/azure/storage/common/storage-decide-blobs-files-disks). 

```python
# Default datastore (Azure file storage)
def_data_store = ws.get_default_datastore() 

# The above call is equivalent to this 
def_data_store = Datastore(ws, "workspacefilestore")

# Get blob storage associated with the workspace
def_blob_store = Datastore(ws, "workspaceblobstore")
```

Przekaż dane plików lub katalogów do magazynu danych dla nich, które mają być dostępne z potokami.  W tym przykładzie korzysta z wersji magazynu obiektów blob magazynu danych:

```python
def_blob_store.upload_files(
    ["./data/20news.pkl"],
    target_path="20newsgroups", 
    overwrite=True)
```

Potok składa się z co najmniej jednego kroku.  Krok to jednostka systemem obliczeniowego elementu docelowego.  Kroki może być używanie źródeł danych i generować dane "pośredni". Krok można utworzyć, dane, takie jak model, katalog o modelu i pliki zależne lub dane tymczasowe.  Te dane są następnie dostępne dla innych kroków później w potoku.

### <a name="configure-data-reference"></a>Konfiguruj odwołanie do danych

Utworzono źródła danych, które mogą być przywoływane w potoku jako dane wejściowe do etapu. Źródło danych w potoku jest reprezentowany przez [element DataReference](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference) obiektu. `DataReference` Obiektu punktów danych, który znajduje się w lub jest dostępny z magazynu danych.

```python
blob_input_data = DataReference(
    datastore=def_blob_store,
    data_reference_name="test_data",
    path_on_datastore="20newsgroups/20news.pkl")
```

Pośredni danych (lub danych wyjściowych kroku), jest reprezentowane przez [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) obiektu. `output_data1` jest generowany jako dane wyjściowe kroku i używane jako dane wejściowe z co najmniej jeden toku realizacji kolejnych kroków.  `PipelineData` wprowadza zależność danych między krokami i tworzy kolejność wykonywania niejawne w potoku.

```python
output_data1 = PipelineData(
    "output_data1",
    datastore=def_blob_store,
    output_name="output_data1")
```

### <a name="set-up-compute"></a>Konfigurowanie obliczeń

W usłudze Azure Machine Learning compute (lub obliczeniowego elementu docelowego) odwołuje się do maszyny i klastry, które wykona etapów obliczeniowych w potoku, machine learning. Można na przykład utworzyć Azure obliczeniowego usługi Machine Learning do uruchamiania etapami.

```python
compute_name = "aml-compute"
 if compute_name in ws.compute_targets:
    compute_target = ws.compute_targets[compute_name]
    if compute_target and type(compute_target) is AmlCompute:
        print('Found compute target: ' + compute_name)
else:
    print('Creating a new compute target...')
    provisioning_config = AmlCompute.provisioning_configuration(vm_size = vm_size, # NC6 is GPU-enabled
                                                                min_nodes = 1, 
                                                                max_nodes = 4)
     # create the compute target
    compute_target = ComputeTarget.create(ws, compute_name, provisioning_config)
    
    # Can poll for a minimum number of nodes and for a specific timeout. 
    # If no min node count is provided it will use the scale settings for the cluster
    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
    
     # For a more detailed view of current cluster status, use the 'status' property    
    print(compute_target.status.serialize())
```

## <a name="construct-your-pipeline-steps"></a>Konstruowania etapów potoku

Teraz można przystąpić do definiowania etap potoku. Wiele wbudowanych kroków są dostępne za pośrednictwem zestawu SDK usługi Azure Machine Learning. Jest najbardziej podstawowym te kroki `PythonScriptStep` , który jest wykonywany skrypt w języku Python w określonym obliczeniowego elementu docelowego.

```python
trainStep = PythonScriptStep(
    script_name="train.py",
    arguments=["--input", blob_input_data, "--output", processed_data1],
    inputs=[blob_input_data],
    outputs=[processed_data1],
    compute_target=compute_target,
    source_directory=project_folder
)
```

Po zdefiniowaniu kroki, utworzysz potok za pomocą niektóre lub wszystkie z tych kroków.

>[!NOTE]
>Nie pliku lub danych jest przekazywany do usługi Azure Machine Learning, kiedy zdefiniujesz kroki lub tworzenie potoku.

```python
# list of steps to run
compareModels = [trainStep, extractStep, compareStep]

# Build the pipeline
pipeline1 = Pipeline(workspace=ws, steps=[compareModels])
```

## <a name="submit-the-pipeline"></a>Przesyłanie potoku

Gdy prześlesz planowanej sprzedaży, zależności są sprawdzane pod kątem każdy krok i migawkę folderu określonego jako katalog źródłowy jest przekazywany do usługi Azure Machine Learning.  Jeśli katalog źródłowy nie zostanie określony, bieżący katalog lokalny jest przekazywany.

```python
# Submit the pipeline to be run
pipeline_run1 = Experiment(ws, 'Compare_Models_Exp').submit(pipeline1)
```

Podczas pierwszego uruchomienia potoku:

* Migawka projektu zostanie pobrana do obliczeniowego elementu docelowego z magazynu obiektów blob, skojarzone z obszarem roboczym.
* Obraz platformy docker jest wbudowany, odpowiadający każdego kroku w potoku.
* Obraz platformy docker dla każdego kroku zostanie pobrana do obliczeniowego elementu docelowego z rejestru kontenerów.
* Jeśli `DataReference` obiektu jest określona w kroku jest zainstalowany w magazynie danych. Jeśli instalacji nie jest obsługiwany, dane zamiast tego jest kopiowany do obliczeniowego elementu docelowego.
* Ten krok działa w określonych w definicji kroku obliczeniowego elementu docelowego. 
* Artefakty, takie jak dzienniki, stdout i stderr, metryki i dane wyjściowe, określone w kroku są tworzone. Te artefakty są następnie przekazywane i przechowywane w magazynie danych domyślne użytkownika.

![Uruchamianie eksperymentu jako potok](./media/how-to-create-your-first-pipeline/run_an_experiment_as_a_pipeline.png)

## <a name="publish-a-pipeline"></a>Publikowanie potoku

Możesz opublikować potoku, aby uruchomić go później przy użyciu różnych danych wejściowych. Dla punktu końcowego REST z już opublikowanych potoku, aby akceptować parametry muszą być sparametryzowane potoku, przed opublikowaniem. 

1. Aby utworzyć parametr potoku, użyj [PipelineParameter](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.pipelineparameter?view=azure-ml-py) obiekt z wartością domyślną.

 ```python
 pipeline_param = PipelineParameter(
     name="pipeline_arg", 
     default_value=10)
 ```

2. Dodaj tę `PipelineParameter` obiekt jako parametr do dowolnego z kroków w potoku w następujący sposób:

 ```python
 compareStep = PythonScriptStep(
     script_name="compare.py",
     arguments=["--comp_data1", comp_data1, "--comp_data2", comp_data2, "--output_data", out_data3, "--param1", pipeline_param],
     inputs=[ comp_data1, comp_data2],
     outputs=[out_data3],    
     target=compute_target, 
     source_directory=project_folder)
 ```

3. Opublikuj ten potok, który akceptuje parametr po wywołaniu.

```python
published_pipeline1 = pipeline1.publish(
    name="My_Published_Pipeline", 
    description="My Published Pipeline Description")
```

## <a name="run-a-published-pipeline"></a>Uruchamianie potoku opublikowane

Wszystkie opublikowane potoki zawierają punktu końcowego REST, aby wywołać przebieg potoku z systemów zewnętrznych, takich jak klienci-Python. Ten punkt końcowy, umożliwia "zarządzanych powtarzalności" w zadaniu wsadowym, oceniania i ponownego trenowania scenariuszy.

Aby wywołać przebieg potoku poprzedniego, potrzebujesz tokenu nagłówka uwierzytelniania usługi Azure Active Directory zgodnie z opisem w [AzureCliAuthentication, klasa](https://docs.microsoft.com/python/api/azureml-core/azureml.core.authentication.azurecliauthentication?view=azure-ml-py)

```python
response = requests.post(published_pipeline1.endpoint, 
    headers=aad_token, 
    json={"ExperimentName": "My_Pipeline",
        "ParameterAssignments": {"pipeline_arg": 20}})
```
## <a name="view-results"></a>Wyświetlanie wyników

Zobacz listę wszystkich potoków i ich szczegóły przebiegu:
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).  

1. [Wyświetlanie obszaru roboczego](how-to-manage-workspace.md#view-a-workspace) listy potoków.
 ![listy potoków uczenia maszynowego](./media/how-to-create-your-first-pipeline/list_of_pipelines.png)
 
1. Wybierz określone potoku, aby zobaczyć wyniki jego działania.

## <a name="next-steps"></a>Kolejne kroki
- Użyj [tych aplikacji Jupyter notebooks w usłudze GitHub](https://aka.ms/aml-pipeline-readme) do eksplorowania dalsze potoków uczenia maszynowego.
- Przeczytaj pomocy odwołanie do zestawu SDK dla [usługi Azure ml potoki rdzeni](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py) pakietu i [kroki w przypadku potoków usługi Azure ml](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py) pakietu.

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]
