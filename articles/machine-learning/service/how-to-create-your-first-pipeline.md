---
title: Tworzenie, uruchamianie i śledzić potokach uczenia Maszynowego
titleSuffix: Azure Machine Learning service
description: Tworzenie i uruchamianie usługi machine learning potoku z zestawem Azure Machine Learning SDK dla języka Python. Potoki umożliwia tworzenie i zarządzanie nimi przepływy pracy tej fazy uczenia maszynowego razem Połącz (ML). Te etapy obejmują przygotowywania danych, szkolenie modelu, wdrożenie modelu i wnioskowania.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: sgilley
ms.author: sanpil
author: sanpil
ms.date: 01/08/2019
ms.custom: seodec18
ms.openlocfilehash: cc561bd88c18788be3ed1b9aef8a6a985af8a6f2
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2019
ms.locfileid: "59278551"
---
# <a name="create-and-run-a-machine-learning-pipeline-by-using-azure-machine-learning-sdk"></a>Tworzenie i uruchamianie potoku uczenia maszynowego przy użyciu zestawu SDK usługi Azure Machine Learning

W tym artykule dowiesz się, jak tworzenie, publikowanie, uruchom i Śledź [potoku uczenia maszynowego](concept-ml-pipelines.md) przy użyciu [zestawu SDK usługi Azure Machine Learning](https://aka.ms/aml-sdk).  Te potoki pomagają tworzyć i zarządzać nimi przepływy pracy, które łączyć różnych machine learning faz. Każdej fazy potoku, takich jak przygotowywanie danych i szkoleń modelowych może zawierać jeden lub więcej kroków.

Potoki tworzenia są widoczne dla członków usługi Azure Machine Learning [obszaru roboczego](how-to-manage-workspace.md). 

Potoki Użyj zdalnego obliczeniowych elementów docelowych dla obliczeń i magazynu pośredniego i końcowego dane skojarzone z tego potoku. Potoki można odczytu i zapisu danych do i z obsługiwanych [usługi Azure Storage](https://docs.microsoft.com/azure/storage/) lokalizacji.

Jeśli nie masz subskrypcji Azure, przed rozpoczęciem utwórz bezpłatne konto. Spróbuj [bezpłatnej i płatnej wersji usługi Azure Machine Learning](https://aka.ms/AMLFree).

## <a name="prerequisites"></a>Wymagania wstępne

* [Konfigurowanie środowiska projektowego](how-to-configure-environment.md) instalacji zestawu SDK usługi Azure Machine Learning.

* Tworzenie [obszaru roboczego usługi Azure Machine Learning](how-to-configure-environment.md#workspace) do przechowywania wszystkich zasobów potoku. 

  ```python
  from azureml.core import Workspace
  
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

* Konfigurowanie `DataReference` obiektów do punktów danych, który znajduje się w przeciwnym razie jest dostępny w magazynie danych.

* Konfigurowanie [celów obliczeń](concept-azure-machine-learning-architecture.md#compute-target) uruchamiania etapów potoku.

### <a name="set-up-a-datastore"></a>Skonfiguruj Magazyn danych
Magazyn danych przechowuje dane dla potoku w celu uzyskania dostępu do. Każdy obszar roboczy ma domyślny magazyn danych. Możesz zarejestrować dodatkowe magazynów danych. 

Po utworzeniu obszaru roboczego, [usługi Azure Files](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) i [usługi Azure Blob storage](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction) są dołączone do obszaru roboczego domyślnie. Usługa Azure Files jest domyślny magazyn danych dla obszaru roboczego, ale można również użyć magazynu obiektów Blob jako magazyn danych. Aby dowiedzieć się więcej, zobacz [przy wyborze rozwiązania, kiedy należy używać usługi Azure Files, obiektów blob platformy Azure lub usługi Azure Disks](https://docs.microsoft.com/azure/storage/common/storage-decide-blobs-files-disks). 

```python
# Default datastore (Azure file storage)
def_data_store = ws.get_default_datastore() 

# The above call is equivalent to this 
def_data_store = Datastore(ws, "workspacefilestore")

# Get blob storage associated with the workspace
def_blob_store = Datastore(ws, "workspaceblobstore")
```

Przekaż dane plików lub katalogów do magazynu danych dla nich, które mają być dostępne z potokami. W tym przykładzie korzysta z wersji magazynu obiektów Blob magazynu danych:

```python
def_blob_store.upload_files(
    ["./data/20news.pkl"],
    target_path="20newsgroups", 
    overwrite=True)
```

Potok składa się z co najmniej jednego kroku. Krok to jednostka systemem obliczeniowego elementu docelowego. Kroki może być używanie źródeł danych i generować dane "pośredni". Krok można utworzyć, dane, takie jak model, katalog o modelu i pliki zależne lub dane tymczasowe. Te dane są następnie dostępne dla innych kroków później w potoku.

### <a name="configure-data-reference"></a>Konfiguruj odwołanie do danych

Utworzono źródła danych, które mogą być przywoływane w potoku jako dane wejściowe do etapu. Źródło danych w potoku jest reprezentowany przez [element DataReference](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference) obiektu. `DataReference` Obiektu punktów danych, który znajduje się w lub jest dostępny z magazynu danych.

```python
blob_input_data = DataReference(
    datastore=def_blob_store,
    data_reference_name="test_data",
    path_on_datastore="20newsgroups/20news.pkl")
```

Pośredni danych (lub danych wyjściowych kroku), jest reprezentowane przez [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) obiektu. `output_data1` jest generowany jako dane wyjściowe w kroku, a także używane jako dane wejściowe z co najmniej jeden toku realizacji kolejnych kroków. `PipelineData` wprowadza zależność danych między krokami i tworzy kolejność wykonywania niejawne w potoku.

```python
output_data1 = PipelineData(
    "output_data1",
    datastore=def_blob_store,
    output_name="output_data1")
```

## <a name="set-up-compute-target"></a>Konfigurowanie obliczeniowego elementu docelowego

W usłudze Azure Machine Learning termin __obliczenia__ (lub __obliczeniowego elementu docelowego__) odwołuje się do maszyny i klastry, które wykonują etapów obliczeniowych w potoku, machine learning.   Zobacz [celów obliczeń dla szkoleń modelowych](how-to-set-up-training-targets.md) poznania pełnej listy celów obliczeń oraz jak utworzyć i dołączyć je do obszaru roboczego.  Proces tworzenia i/lub dołączanie obliczeniowego elementu docelowego jest taki sam niezależnie od tego, czy podczas uczenia modelu lub etap potoku. Po utworzeniu i dołączyć obliczeniowego elementu docelowego, użyj `ComputeTarget` obiektu w swojej [etap potoku](#steps).

> [!IMPORTANT]
> Wykonywanie operacji zarządzania na obliczeniowych elementów docelowych nie jest obsługiwane wewnątrz zdalnej obsługi zadań. Przesłaniu potokach uczenia maszynowego jako zdalnego zadania nie używaj operacji zarządzania w celów obliczeń z wewnątrz potoku.

Poniżej przedstawiono przykłady tworzenie i dołączanie obliczeniowych elementów docelowych dla:

* Usługi Azure Machine Learning obliczeń
* Azure Databricks 
* Azure Data Lake Analytics

### <a name="azure-machine-learning-compute"></a>Usługi Azure Machine Learning obliczeń

Można tworzyć zasoby obliczeniowe usługi Azure Machine Learning na potrzeby uruchamiania etapów.

```python
from azureml.core.compute import ComputeTarget, AmlCompute

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

### <a id="databricks"></a>Usługa Azure Databricks

Usługa Azure Databricks to oparta na platformie Apache Spark środowisko w chmurze platformy Azure. Może służyć jako cel obliczenia z potoku usługi Azure Machine Learning.

Utwórz obszar roboczy usługi Azure Databricks, przed jego użyciem. Aby utworzyć tych zasobów, zobacz [uruchamianie zadania Spark w usłudze Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal) dokumentu.

Aby dołączyć usługi Azure Databricks, jako cel obliczenia, podaj następujące informacje:

* __Nazwa obliczeniowego Databricks__: Nazwa, którą chcesz przypisać do tego zasobu obliczeniowego.
* __Nazwa obszaru roboczego usługi Databricks__: Nazwa obszaru roboczego usługi Azure Databricks.
* __Token dostępu usługi Databricks__: Token dostępu używany do uwierzytelniania usługi Azure Databricks. Aby wygenerować token dostępu, zobacz [uwierzytelniania](https://docs.azuredatabricks.net/api/latest/authentication.html) dokumentu.

Poniższy kod przedstawia sposób dołączania usługi Azure Databricks jako cel obliczenia przy użyciu zestawu SDK usługi Azure Machine Learning:

```python
import os
from azureml.core.compute import ComputeTarget, DatabricksCompute
from azureml.exceptions import ComputeTargetException

databricks_compute_name = os.environ.get("AML_DATABRICKS_COMPUTE_NAME", "<databricks_compute_name>")
databricks_workspace_name = os.environ.get("AML_DATABRICKS_WORKSPACE", "<databricks_workspace_name>")
databricks_resource_group = os.environ.get("AML_DATABRICKS_RESOURCE_GROUP", "<databricks_resource_group>")
databricks_access_token = os.environ.get("AML_DATABRICKS_ACCESS_TOKEN", "<databricks_access_token>")

try:
    databricks_compute = ComputeTarget(workspace=ws, name=databricks_compute_name)
    print('Compute target already exists')
except ComputeTargetException:
    print('compute not found')
    print('databricks_compute_name {}'.format(databricks_compute_name))
    print('databricks_workspace_name {}'.format(databricks_workspace_name))
    print('databricks_access_token {}'.format(databricks_access_token))

    # Create attach config
    attach_config = DatabricksCompute.attach_configuration(resource_group = databricks_resource_group,
                                                           workspace_name = databricks_workspace_name,
                                                           access_token = databricks_access_token)
    databricks_compute = ComputeTarget.attach(
             ws,
             databricks_compute_name,
             attach_config
         )
    
    databricks_compute.wait_for_completion(True)
```
### <a id="adla"></a>Azure Data Lake Analytics

Usługa Azure Data Lake Analytics to platforma analiz danych big data w chmurze platformy Azure. Może służyć jako cel obliczenia z potoku usługi Azure Machine Learning.

Utwórz konto usługi Azure Data Lake Analytics, przed jego użyciem. Aby utworzyć ten zasób, zobacz [Rozpoczynanie pracy z usługą Azure Data Lake Analytics](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-get-started-portal) dokumentu.

Aby dołączyć usługi Data Lake Analytics, jako cel obliczenia, możesz użyć zestawu SDK usługi Azure Machine Learning i podaj następujące informacje:

* __Nazwa obliczeniowego__: Nazwa, którą chcesz przypisać do tego zasobu obliczeniowego.
* __Grupa zasobów__: Grupy zasobów zawierającej konto usługi Data Lake Analytics.
* __Nazwa konta__: Nazwa konta usługi Data Lake Analytics.

Poniższy kod przedstawia sposób dołączania usługi Data Lake Analytics, jako cel obliczenia:

```python
import os
from azureml.core.compute import ComputeTarget, AdlaCompute
from azureml.exceptions import ComputeTargetException


adla_compute_name = os.environ.get("AML_ADLA_COMPUTE_NAME", "<adla_compute_name>")
adla_resource_group = os.environ.get("AML_ADLA_RESOURCE_GROUP", "<adla_resource_group>")
adla_account_name = os.environ.get("AML_ADLA_ACCOUNT_NAME", "<adla_account_name>")

try:
    adla_compute = ComputeTarget(workspace=ws, name=adla_compute_name)
    print('Compute target already exists')
except ComputeTargetException:
    print('compute not found')
    print('adla_compute_name {}'.format(adla_compute_name))
    print('adla_resource_id {}'.format(adla_resource_group))
    print('adla_account_name {}'.format(adla_account_name))
    # create attach config
    attach_config = AdlaCompute.attach_configuration(resource_group = adla_resource_group,
                                                     account_name = adla_account_name)
    # Attach ADLA
    adla_compute = ComputeTarget.attach(
             ws,
             adla_compute_name,
             attach_config
         )
    
    adla_compute.wait_for_completion(True)
```

> [!TIP]
> Potoki usługi Azure Machine Learning może pracować tylko z danych przechowywanych w magazynie danych domyślnego konta usługi Data Lake Analytics. Jeśli dane potrzebne do pracy z znajduje się w magazynie innych niż domyślne, można użyć [ `DataTransferStep` ](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.data_transfer_step.datatransferstep?view=azure-ml-py) do kopiowania danych przed szkolenia.

## <a id="steps"></a>Konstruowania etapów potoku

Po utworzeniu i dołączyć obliczeniowego elementu docelowego w Twoim obszarze roboczym, jesteś gotowy do definiowania etap potoku. Wiele wbudowanych kroków są dostępne za pośrednictwem zestawu SDK usługi Azure Machine Learning. Jest najbardziej podstawowym te kroki [PythonScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep?view=azure-ml-py), które uruchamia skrypt w języku Python w określonym obliczeniowego elementu docelowego:

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

W poniższym przykładzie użyto usługi Azure Databricks obliczeniowego elementu docelowego utworzonego wcześniej: 

```python
dbStep = DatabricksStep(
    name="databricksmodule",
    inputs=[step_1_input],
    outputs=[step_1_output],
    num_workers=1,
    notebook_path=notebook_path,
    notebook_params={'myparam': 'testparam'},
    run_name='demo run name',
    compute_target=databricks_compute,
    allow_reuse=False
)
# List of steps to run
steps = [dbStep]

# Build the pipeline
pipeline1 = Pipeline(workspace=ws, steps=steps)
```

Aby uzyskać więcej informacji, zobacz [pakiet azure — potok — instrukcje](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py) i [potoku klasy](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline%28class%29?view=azure-ml-py) odwołania.

## <a name="submit-the-pipeline"></a>Przesyłanie potoku

Po przesłaniu potoku usługi Azure Machine Learning sprawdza zależności dla każdego kroku i przesyła migawkę katalog źródłowy, wskazana. Jeśli katalog źródłowy nie zostanie określony, bieżący katalog lokalny jest przekazywany.


```python
# Submit the pipeline to be run
pipeline_run1 = Experiment(ws, 'Compare_Models_Exp').submit(pipeline1)
pipeline_run1.wait_for_completion()
```

Przy pierwszym uruchomieniu potoku usługi Azure Machine Learning:

* Pobiera migawkę projektu do obliczeniowego elementu docelowego z magazynu obiektów Blob, skojarzone z obszarem roboczym.
* Tworzy obraz platformy Docker, odpowiadający każdego kroku w potoku.
* Pobiera obraz platformy docker dla każdego kroku do obliczeniowego elementu docelowego z rejestru kontenerów.
* Jeśli na komputerze instalująca magazynu danych, `DataReference` obiektu określonego w kroku. Jeśli instalacji nie jest obsługiwany, dane zamiast tego jest kopiowany do obliczeniowego elementu docelowego.
* Uruchamia krok w obliczeniowego elementu docelowego określonego w definicji kroku. 
* Tworzy artefaktów, takich jak dzienniki, stdout i stderr, metryki i dane wyjściowe, określone w kroku. Te artefakty są następnie przekazywane i przechowywane w domyślnej użytkownika w magazynie danych.

![Diagram przedstawiający uruchamianie eksperymentu jako potoku](./media/how-to-create-your-first-pipeline/run_an_experiment_as_a_pipeline.png)

Aby uzyskać więcej informacji, zobacz [eksperymentować klasy](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py) odwołania.

## <a name="publish-a-pipeline"></a>Publikowanie potoku

Możesz opublikować potoku, aby uruchomić go później przy użyciu różnych danych wejściowych. Już opublikowaną potoku, aby akceptować parametry punktu końcowego REST należy zdefiniować parametry potoku przed opublikowaniem. 

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

Wszystkie opublikowane potoki zawierają punktu końcowego REST. Ten punkt końcowy wywołuje uruchomienia potoku z systemów zewnętrznych, takich jak klienci-Python. Ten punkt końcowy umożliwia "zarządzanych powtarzalności" w zadaniu wsadowym, oceniania i ponownego trenowania scenariuszy.

Aby wywołać przebieg potoku poprzedniego, potrzebujesz nagłówka token uwierzytelniania usługi Azure Active Directory zgodnie z opisem w [klasy AzureCliAuthentication](https://docs.microsoft.com/python/api/azureml-core/azureml.core.authentication.azurecliauthentication?view=azure-ml-py) lub Dowiedz się więcej o [uwierzytelniania w usłudze Azure Machine Nauka](https://aka.ms/pl-restep-auth) notesu.

```python
response = requests.post(published_pipeline1.endpoint, 
    headers=aad_token, 
    json={"ExperimentName": "My_Pipeline",
        "ParameterAssignments": {"pipeline_arg": 20}})
```
## <a name="view-results"></a>Wyświetlanie wyników

Zobacz listę wszystkich potoków i ich szczegóły przebiegu:
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).  

1. [Wyświetlanie obszaru roboczego](how-to-manage-workspace.md#view) listy potoków.
 ![listy potoków uczenia maszynowego](./media/how-to-create-your-first-pipeline/list_of_pipelines.png)
 
1. Wybierz określone potoku, aby zobaczyć wyniki jego działania.

## <a name="next-steps"></a>Kolejne kroki
- Użyj [tych aplikacji Jupyter notebooks w usłudze GitHub](https://aka.ms/aml-pipeline-readme) do eksplorowania dalsze potoków uczenia maszynowego.
- Przeczytaj pomocy odwołanie do zestawu SDK dla [usługi Azure ml potoki rdzeni](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py) pakietu i [kroki w przypadku potoków usługi Azure ml](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py) pakietu.

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]
