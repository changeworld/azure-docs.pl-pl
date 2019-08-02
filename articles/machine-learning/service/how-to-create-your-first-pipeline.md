---
title: Twórz, uruchamiaj, & Śledź potoki ML
titleSuffix: Azure Machine Learning service
description: Tworzenie i uruchamianie usługi machine learning potoku z zestawem Azure Machine Learning SDK dla języka Python. Potoki służą do tworzenia przepływów pracy łączących etapy uczenia maszynowego (ML) i zarządzania nimi. Te fazy obejmują Przygotowywanie danych, szkolenia modeli, wdrażanie modeli i wnioskowanie/ocenianie.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: sgilley
ms.author: sanpil
author: sanpil
ms.date: 05/02/2019
ms.custom: seodec18
ms.openlocfilehash: 3f0b764b16c1b550c9afa4107449c1b02815e8d1
ms.sourcegitcommit: fecb6bae3f29633c222f0b2680475f8f7d7a8885
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/30/2019
ms.locfileid: "68668502"
---
# <a name="create-and-run-a-machine-learning-pipeline-by-using-azure-machine-learning-sdk"></a>Tworzenie i uruchamianie potoku uczenia maszynowego przy użyciu zestawu SDK Azure Machine Learning

Ten artykuł zawiera informacje na temat tworzenia, publikowania, uruchamiania i śledzenia potoku uczenia [maszynowego](concept-ml-pipelines.md) przy użyciu [zestawu SDK Azure Machine Learning](https://aka.ms/aml-sdk).  Te potoki pomagają tworzyć i zarządzać nimi przepływy pracy, które łączyć różnych machine learning faz. Każdej fazy potoku, takich jak przygotowywanie danych i szkoleń modelowych może zawierać jeden lub więcej kroków.

Potoki tworzenia są widoczne dla członków usługi Azure Machine Learning [obszaru roboczego](how-to-manage-workspace.md). 

Potoki Użyj zdalnego obliczeniowych elementów docelowych dla obliczeń i magazynu pośredniego i końcowego dane skojarzone z tego potoku. Potoki mogą odczytywać i zapisywać dane w i z obsługiwanych lokalizacji [usługi Azure Storage](https://docs.microsoft.com/azure/storage/) .

Jeśli nie masz subskrypcji Azure, przed rozpoczęciem utwórz bezpłatne konto. Wypróbuj [bezpłatną lub płatną wersję usługi Azure Machine Learning Service](https://aka.ms/AMLFree).

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

* `DataReference` Skonfiguruj obiekt, aby wskazywał dane, które znajdują się w, lub jest dostępny w magazynie danych.

* Konfigurowanie [celów obliczeń](concept-azure-machine-learning-architecture.md#compute-targets) uruchamiania etapów potoku.

### <a name="set-up-a-datastore"></a>Skonfiguruj Magazyn danych
Magazyn danych przechowuje dane dla potoku w celu uzyskania dostępu do. Każdy obszar roboczy ma domyślny magazyn danych. Możesz zarejestrować dodatkowe magazynów danych. 

Podczas tworzenia obszaru roboczego [Azure Files](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) i [Magazyn obiektów blob platformy Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction) są dołączone do obszaru roboczego. W celu nawiązania połączenia z magazynem obiektów blob platformy Azure jest zarejestrowany domyślny magazyn danych. Aby dowiedzieć się więcej, zobacz [Decydowanie o tym, kiedy używać Azure Files, obiektów blob platformy Azure lub dysków platformy Azure](https://docs.microsoft.com/azure/storage/common/storage-decide-blobs-files-disks). 

```python
# Default datastore (Azure blob storage)
def_data_store = ws.get_default_datastore()

# The above call is equivalent to this
def_data_store = Datastore(ws, "workspaceblobstore")

# Get file storage associated with the workspace
def_file_store = Datastore(ws, "workspacefilestore")
```

Przekaż dane plików lub katalogów do magazynu danych dla nich, które mają być dostępne z potokami. Ten przykład używa wersji magazynu obiektów blob magazynu danych:

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

Pośredni danych (lub danych wyjściowych kroku), jest reprezentowane przez [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) obiektu. `output_data1`jest tworzony jako dane wyjściowe kroku i używany jako dane wejściowe jednego lub kilku przyszłych kroków. `PipelineData`wprowadza zależność danych między krokami i tworzy niejawną kolejność wykonywania w potoku.

```python
output_data1 = PipelineData(
    "output_data1",
    datastore=def_blob_store,
    output_name="output_data1")
```

## <a name="set-up-compute-target"></a>Konfigurowanie obliczeniowego elementu docelowego

W Azure Machine Learning termin obliczeniowy (lub __element docelowy obliczeń__) odnosi się do maszyn lub klastrów wykonujących kroki obliczeniowe w potoku uczenia maszynowego.   Zobacz [cele obliczeń dla szkolenia modelu](how-to-set-up-training-targets.md) , aby uzyskać pełną listę elementów docelowych obliczeń oraz sposób tworzenia i dołączania ich do obszaru roboczego.  Proces tworzenia i dołączania obiektu docelowego obliczeń jest taki sam, niezależnie od tego, czy jest to szkoleniowy model, czy też uruchamiany jest krok potoku. Po utworzeniu i dołączeniu obiektu docelowego obliczeń Użyj `ComputeTarget` obiektu w kroku potoku. [](#steps)

> [!IMPORTANT]
> Wykonywanie operacji zarządzania na obiektach docelowych obliczeń nie jest obsługiwane w ramach zadań zdalnych. Potoki uczenia maszynowego są przesyłane jako zadania zdalne, dlatego nie należy używać operacji zarządzania na obiektach docelowych obliczeń z wnętrza potoku.

Poniżej przedstawiono przykłady tworzenia i dołączania obiektów docelowych obliczeń dla:

* Usługi Azure Machine Learning obliczeń
* Azure Databricks 
* Azure Data Lake Analytics

### <a name="azure-machine-learning-compute"></a>Usługi Azure Machine Learning obliczeń

Można utworzyć Azure Machine Learning obliczeń do uruchamiania kroków.

```python
from azureml.core.compute import ComputeTarget, AmlCompute

compute_name = "aml-compute"
vm_size = "STANDARD_NC6"
if compute_name in ws.compute_targets:
    compute_target = ws.compute_targets[compute_name]
    if compute_target and type(compute_target) is AmlCompute:
        print('Found compute target: ' + compute_name)
else:
    print('Creating a new compute target...')
    provisioning_config = AmlCompute.provisioning_configuration(vm_size=vm_size,  # STANDARD_NC6 is GPU-enabled
                                                                min_nodes=0,
                                                                max_nodes=4)
    # create the compute target
    compute_target = ComputeTarget.create(
        ws, compute_name, provisioning_config)

    # Can poll for a minimum number of nodes and for a specific timeout.
    # If no min node count is provided it will use the scale settings for the cluster
    compute_target.wait_for_completion(
        show_output=True, min_node_count=None, timeout_in_minutes=20)

    # For a more detailed view of current cluster status, use the 'status' property
    print(compute_target.status.serialize())
```

### <a id="databricks"></a>Usługa Azure Databricks

Usługa Azure Databricks to oparta na platformie Apache Spark środowisko w chmurze platformy Azure. Może służyć jako obiekt docelowy obliczeń z potokiem Azure Machine Learning.

Utwórz obszar roboczy Azure Databricks, zanim go użyjesz. Aby utworzyć tych zasobów, zobacz [uruchamianie zadania Spark w usłudze Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal) dokumentu.

Aby dołączyć Azure Databricks jako element docelowy obliczeń, podaj następujące informacje:

* __Nazwa obliczeniowa datakostek__: Nazwa, która ma zostać przypisana do tego zasobu obliczeniowego.
* __Nazwa obszaru roboczego__datakosteks: Nazwa obszaru roboczego Azure Databricks.
* __Token dostępu do datakostki__: Token dostępu używany do uwierzytelniania w Azure Databricks. Aby wygenerować token dostępu, zobacz [uwierzytelniania](https://docs.azuredatabricks.net/api/latest/authentication.html) dokumentu.

Poniższy kod ilustruje sposób dołączania Azure Databricks jako obiektu docelowego obliczeń przy użyciu zestawu SDK Azure Machine Learning:

```python
import os
from azureml.core.compute import ComputeTarget, DatabricksCompute
from azureml.exceptions import ComputeTargetException

databricks_compute_name = os.environ.get(
    "AML_DATABRICKS_COMPUTE_NAME", "<databricks_compute_name>")
databricks_workspace_name = os.environ.get(
    "AML_DATABRICKS_WORKSPACE", "<databricks_workspace_name>")
databricks_resource_group = os.environ.get(
    "AML_DATABRICKS_RESOURCE_GROUP", "<databricks_resource_group>")
databricks_access_token = os.environ.get(
    "AML_DATABRICKS_ACCESS_TOKEN", "<databricks_access_token>")

try:
    databricks_compute = ComputeTarget(
        workspace=ws, name=databricks_compute_name)
    print('Compute target already exists')
except ComputeTargetException:
    print('compute not found')
    print('databricks_compute_name {}'.format(databricks_compute_name))
    print('databricks_workspace_name {}'.format(databricks_workspace_name))
    print('databricks_access_token {}'.format(databricks_access_token))

    # Create attach config
    attach_config = DatabricksCompute.attach_configuration(resource_group=databricks_resource_group,
                                                           workspace_name=databricks_workspace_name,
                                                           access_token=databricks_access_token)
    databricks_compute = ComputeTarget.attach(
        ws,
        databricks_compute_name,
        attach_config
    )

    databricks_compute.wait_for_completion(True)
```

Aby zapoznać się z bardziej szczegółowym przykładem, zobacz [przykładowy Notes](https://aka.ms/pl-databricks) w witrynie GitHub.

### <a id="adla"></a>Azure Data Lake Analytics

Usługa Azure Data Lake Analytics to platforma analiz danych big data w chmurze platformy Azure. Może służyć jako obiekt docelowy obliczeń z potokiem Azure Machine Learning.

Utwórz konto Azure Data Lake Analytics przed użyciem go. Aby utworzyć ten zasób, zobacz [Rozpoczynanie pracy z usługą Azure Data Lake Analytics](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-get-started-portal) dokumentu.

Aby dołączyć usługi Data Lake Analytics, jako cel obliczenia, możesz użyć zestawu SDK usługi Azure Machine Learning i podaj następujące informacje:

* __Nazwa obliczenia__: Nazwa, która ma zostać przypisana do tego zasobu obliczeniowego.
* __Grupa zasobów__: Grupa zasobów zawierająca konto Data Lake Analytics.
* __Nazwa konta__: Nazwa konta Data Lake Analytics.

Poniższy kod przedstawia sposób dołączania usługi Data Lake Analytics, jako cel obliczenia:

```python
import os
from azureml.core.compute import ComputeTarget, AdlaCompute
from azureml.exceptions import ComputeTargetException


adla_compute_name = os.environ.get(
    "AML_ADLA_COMPUTE_NAME", "<adla_compute_name>")
adla_resource_group = os.environ.get(
    "AML_ADLA_RESOURCE_GROUP", "<adla_resource_group>")
adla_account_name = os.environ.get(
    "AML_ADLA_ACCOUNT_NAME", "<adla_account_name>")

try:
    adla_compute = ComputeTarget(workspace=ws, name=adla_compute_name)
    print('Compute target already exists')
except ComputeTargetException:
    print('compute not found')
    print('adla_compute_name {}'.format(adla_compute_name))
    print('adla_resource_id {}'.format(adla_resource_group))
    print('adla_account_name {}'.format(adla_account_name))
    # create attach config
    attach_config = AdlaCompute.attach_configuration(resource_group=adla_resource_group,
                                                     account_name=adla_account_name)
    # Attach ADLA
    adla_compute = ComputeTarget.attach(
        ws,
        adla_compute_name,
        attach_config
    )

    adla_compute.wait_for_completion(True)
```

Aby zapoznać się z bardziej szczegółowym przykładem, zobacz [przykładowy Notes](https://aka.ms/pl-adla) w witrynie GitHub.

> [!TIP]
> Potoki usługi Azure Machine Learning może pracować tylko z danych przechowywanych w magazynie danych domyślnego konta usługi Data Lake Analytics. Jeśli dane potrzebne do pracy z znajduje się w magazynie innych niż domyślne, można użyć [ `DataTransferStep` ](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.data_transfer_step.datatransferstep?view=azure-ml-py) do kopiowania danych przed szkolenia.

## <a id="steps"></a>Konstruowanie kroków potoku

Po utworzeniu i dołączeniu obiektu docelowego obliczeń do obszaru roboczego możesz zdefiniować krok potoku. Wiele wbudowanych kroków są dostępne za pośrednictwem zestawu SDK usługi Azure Machine Learning. Najbardziej podstawową procedurą jest [PythonScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep?view=azure-ml-py), która uruchamia skrypt języka Python w określonym obiekcie docelowym obliczeń:

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

Ponowne użycie poprzednich wyników (`allow_reuse`) jest kluczem w przypadku używania potoków w środowisku współpracy, ponieważ usunięcie niepotrzebnych ponownych uruchomień zapewnia elastyczność. Jest to zachowanie domyślne, gdy script_name, dane wejściowe i parametry kroku pozostają takie same. Gdy dane wyjściowe kroku są ponownie używane, zadanie nie zostanie przesłane do obliczenia, a wyniki z poprzedniego przebiegu są natychmiast dostępne dla uruchomienia następnego kroku. W przypadku wybrania wartości false dla tego kroku będzie zawsze generowany nowy przebieg podczas wykonywania potoku. 

Po zdefiniowaniu kroków można skompilować potok za pomocą niektórych lub wszystkich tych kroków.

> [!NOTE]
> Podczas definiowania kroków lub kompilowania potoku nie są przekazywane żadne pliki ani dane do usługi Azure Machine Learning.

```python
# list of steps to run
compareModels = [trainStep, extractStep, compareStep]

# Build the pipeline
pipeline1 = Pipeline(workspace=ws, steps=[compareModels])
```

Poniższy przykład używa utworzonego wcześniej elementu docelowego obliczeń Azure Databricks: 

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

Aby uzyskać więcej informacji, zobacz [pakiet Azure-Pipeline-etaps](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py) i informacje o [klasie potoku](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline%28class%29?view=azure-ml-py) .

## <a name="submit-the-pipeline"></a>Przesyłanie potoku

Podczas przesyłania potoku usługa Azure Machine Learning sprawdza zależności od poszczególnych kroków i przekazuje migawkę podanego katalogu źródłowego. Jeśli katalog źródłowy nie zostanie określony, bieżący katalog lokalny jest przekazywany. Migawka jest również przechowywana w ramach eksperymentu w obszarze roboczym.

> [!IMPORTANT]
> Aby uniemożliwić Dołączanie plików do migawki, Utwórz plik [. gitignore](https://git-scm.com/docs/gitignore) lub `.amlignore` w katalogu i Dodaj do niego pliki. Plik używa tej samej składni i wzorców co plik [. gitignore.](https://git-scm.com/docs/gitignore) `.amlignore` Jeśli istnieją oba pliki, `.amlignore` ma pierwszeństwo.
>
> Aby uzyskać więcej informacji, zobacz [migawki](concept-azure-machine-learning-architecture.md#snapshots).

```python
# Submit the pipeline to be run
pipeline_run1 = Experiment(ws, 'Compare_Models_Exp').submit(pipeline1)
pipeline_run1.wait_for_completion()
```

Podczas pierwszego uruchomienia potoku Azure Machine Learning:

* Pobiera migawkę projektu do obiektu docelowego obliczeń z magazynu obiektów BLOB skojarzonego z obszarem roboczym.
* Kompiluje obraz platformy Docker odpowiadający każdemu krokowi w potoku.
* Pobiera obraz platformy Docker dla każdego kroku do obiektu docelowego obliczeń z rejestru kontenerów.
* Instaluje magazyn danych, jeśli `DataReference` obiekt jest określony w kroku. Jeśli instalacji nie jest obsługiwany, dane zamiast tego jest kopiowany do obliczeniowego elementu docelowego.
* Uruchamia krok w elemencie docelowym obliczeń określonym w definicji kroku. 
* Tworzy artefakty, takie jak dzienniki, stdout i stderr, metryki i dane wyjściowe określone przez krok. Te artefakty są następnie przekazywane i przechowywane w domyślnym magazynie danych użytkownika.

![Diagram uruchamiania eksperymentu jako potoku](./media/how-to-create-your-first-pipeline/run_an_experiment_as_a_pipeline.png)

Aby uzyskać więcej informacji, zobacz informacje o [klasie eksperymentów](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py) .

## <a name="github-tracking-and-integration"></a>Śledzenie i integracja z usługą GitHub

Po rozpoczęciu szkolenia w przypadku, gdy katalog źródłowy jest lokalnym repozytorium git, informacje o repozytorium są przechowywane w historii uruchamiania. Na przykład bieżący identyfikator zatwierdzenia dla repozytorium jest rejestrowany jako część historii.

## <a name="publish-a-pipeline"></a>Publikowanie potoku

Możesz opublikować potoku, aby uruchomić go później przy użyciu różnych danych wejściowych. W przypadku punktu końcowego REST już opublikowanego potoku w celu zaakceptowania parametrów należy Sparametryzuj potok przed opublikowaniem. 

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

Wszystkie opublikowane potoki mają punkt końcowy REST. Ten punkt końcowy wywołuje przebieg potoku z systemów zewnętrznych, takich jak klienci spoza języka Python. Ten punkt końcowy włącza "zarządzaną powtarzalność" w ramach oceniania wsadowego i scenariuszy ponownego szkolenia.

Aby wywołać poprzedni potok, należy uzyskać token nagłówka uwierzytelniania Azure Active Directory, zgodnie z opisem w [klasie AzureCliAuthentication](https://docs.microsoft.com/python/api/azureml-core/azureml.core.authentication.azurecliauthentication?view=azure-ml-py) lub uzyskać więcej szczegółów na temat [uwierzytelniania w Azure Machine Learning](https://aka.ms/pl-restep-auth) notesie.

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

## <a name="caching--reuse"></a>Buforowanie & ponownie używane  

Aby zoptymalizować i dostosować zachowanie potoków, można wykonać kilka czynności związanych z buforowaniem i wielokrotnym użyciem. Można na przykład wybrać następujące opcje:
+ Wyłącz **domyślne ponowne użycie kroku Uruchom dane wyjściowe** przez ustawienie `allow_reuse=False` podczas [definiowania kroku](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py). Ponowne użycie jest kluczem przy użyciu potoków w środowisku współpracy, ponieważ wyeliminowanie niepotrzebnych uruchomień zapewnia elastyczność. Można jednak zrezygnować z tej opcji.
+ **Rozszerzenie mieszania wykraczające poza skrypt**, aby również zawierać ścieżkę bezwzględną lub względną do pliku katalog_źródłowy do innych plików i katalogów za pomocą`hash_paths=['<file or directory']` 
+ **Wymuś ponowne wygenerowanie danych wyjściowych dla wszystkich kroków w przebiegu** z`pipeline_run = exp.submit(pipeline, regenerate_outputs=False)`

Domyślnie dla kroków `allow-reuse` jest włączone, a tylko główny plik skryptu jest skrótem. Tak więc, jeśli skrypt dla danego kroku pozostaje taki sam (`script_name`, dane wejściowe i parametry), dane wyjściowe wykonywane w poprzednim kroku są ponownie używane, zadanie nie zostanie przesłane do obliczenia, a wyniki z poprzedniego uruchomienia są natychmiast dostępne w następnym kroku zamiast .  

```python
step = PythonScriptStep(name="Hello World",
                        script_name="hello_world.py",
                        compute_target=aml_compute,
                        source_directory=source_directory,
                        allow_reuse=False,
                        hash_paths=['hello_world.ipynb'])
```
 

## <a name="next-steps"></a>Następne kroki
- Użyj [tych aplikacji Jupyter notebooks w usłudze GitHub](https://aka.ms/aml-pipeline-readme) do eksplorowania dalsze potoków uczenia maszynowego.
- Przeczytaj pomocy odwołanie do zestawu SDK dla [usługi Azure ml potoki rdzeni](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py) pakietu i [kroki w przypadku potoków usługi Azure ml](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py) pakietu.

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]
