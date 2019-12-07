---
title: Twórz, uruchamiaj, & Śledź potoki ML
titleSuffix: Azure Machine Learning
description: Tworzenie i uruchamianie usługi machine learning potoku z zestawem Azure Machine Learning SDK dla języka Python. Przy użyciu potoków ML można tworzyć przepływy pracy, które łączą fazy uczenia maszynowego (ML) i zarządzać nimi. Te fazy obejmują Przygotowywanie danych, szkolenia modeli, wdrażanie modeli i wnioskowanie/ocenianie.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: sgilley
ms.author: sanpil
author: sanpil
ms.date: 12/05/2019
ms.custom: seodec18
ms.openlocfilehash: 85c80a5b9f2c0ac7b73fb51fd8138e3aae0b0221
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/06/2019
ms.locfileid: "74894670"
---
# <a name="create-and-run-machine-learning-pipelines-with-azure-machine-learning-sdk"></a>Tworzenie i uruchamianie potoków uczenia maszynowego za pomocą zestawu SDK Azure Machine Learning
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Ten artykuł zawiera informacje na temat tworzenia, publikowania, uruchamiania i śledzenia [potoku uczenia maszynowego](concept-ml-pipelines.md) przy użyciu [zestawu SDK Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).  Użyj **potoków ml** , aby utworzyć przepływ pracy, który jest połączony z różnymi etapami ml, a następnie opublikuj ten potok w obszarze roboczym Azure Machine Learning, aby uzyskać dostęp do nich później lub udostępniać innym osobom.  Potoki ML doskonale nadają się do scenariuszy wsadowych oceniania, przy użyciu różnych obliczeń, ponownej realizacji czynności zamiast uruchamiania ich, a także udostępniania przepływów pracy ML innym osobom.

Chociaż możesz użyć innego rodzaju potoku o nazwie [potoku platformy Azure](https://docs.microsoft.com/azure/devops/pipelines/targets/azure-machine-learning?context=azure%2Fmachine-learning%2Fservice%2Fcontext%2Fml-context&view=azure-devops&tabs=yaml) do automatyzacji wykonywania zadań w usłudze ml, ten typ potoku nigdy nie jest przechowywany w obszarze roboczym. [Porównaj te różne potoki](concept-ml-pipelines.md#which-azure-pipeline-technology-should-i-use).

Każda faza potoku ML, taka jak przygotowanie danych i szkolenia modeli, może obejmować jeden lub więcej kroków.

Utworzone potoki ML są widoczne dla członków [obszaru roboczego](how-to-manage-workspace.md)Azure Machine Learning. 

Potoki ML wykorzystują zdalne cele obliczeniowe do obliczeń i przechowywania danych pośrednich i końcowych skojarzonych z tym potokiem. Mogą odczytywać i zapisywać dane w i z obsługiwanych lokalizacji [usługi Azure Storage](https://docs.microsoft.com/azure/storage/) .

Jeśli nie masz subskrypcji Azure, przed rozpoczęciem utwórz bezpłatne konto. Wypróbuj [bezpłatną lub płatną wersję Azure Machine Learning](https://aka.ms/AMLFree).

## <a name="prerequisites"></a>Wymagania wstępne

* Tworzenie [obszaru roboczego usługi Azure Machine Learning](how-to-manage-workspace.md) do przechowywania wszystkich zasobów potoku.

* [Skonfiguruj środowisko programistyczne](how-to-configure-environment.md) , aby zainstalować zestaw SDK Azure Machine Learning, lub Użyj [maszyny wirtualnej Azure Machine Learning notesu](concept-azure-machine-learning-architecture.md#compute-instance) z już zainstalowanym zestawem SDK.

Zacznij od dołączenia obszaru roboczego:

```Python
import azureml.core
from azureml.core import Workspace, Datastore

ws = Workspace.from_config()
```


## <a name="set-up-machine-learning-resources"></a>Konfigurowanie zasobów w machine learning

Utwórz zasoby wymagane do uruchomienia potoku ML:

* Skonfiguruj Magazyn danych umożliwia dostęp do danych, potrzebne w etapach potoku.

* Skonfiguruj obiekt `DataReference` w taki sposób, aby wskazywał dane, które znajdują się w, lub jest dostępny w magazynie danych.

* Konfigurowanie [celów obliczeń](concept-azure-machine-learning-architecture.md#compute-targets) uruchamiania etapów potoku.

### <a name="set-up-a-datastore"></a>Skonfiguruj Magazyn danych

Magazyn danych przechowuje dane dla potoku w celu uzyskania dostępu do. Każdy obszar roboczy ma domyślny magazyn danych. Możesz zarejestrować dodatkowe magazynów danych. 

Podczas tworzenia obszaru roboczego [Azure Files](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) i [Magazyn obiektów blob platformy Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction) są dołączone do obszaru roboczego. W celu nawiązania połączenia z magazynem obiektów blob platformy Azure jest zarejestrowany domyślny magazyn danych. Aby dowiedzieć się więcej, zobacz [Decydowanie o tym, kiedy używać Azure Files, obiektów blob platformy Azure lub dysków platformy Azure](https://docs.microsoft.com/azure/storage/common/storage-decide-blobs-files-disks). 

```python
# Default datastore 
def_data_store = ws.get_default_datastore()

# Get the blob storage associated with the workspace
def_blob_store = Datastore(ws, "workspaceblobstore")

# Get file storage associated with the workspace
def_file_store = Datastore(ws, "workspacefilestore")

```

Przekaż dane plików lub katalogów do magazynu danych dla nich, które mają być dostępne z potokami. Ten przykład używa magazynu obiektów BLOB jako magazynu danych:

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
from azureml.data.data_reference import DataReference

blob_input_data = DataReference(
    datastore=def_blob_store,
    data_reference_name="test_data",
    path_on_datastore="20newsgroups/20news.pkl")
```

Pośredni danych (lub danych wyjściowych kroku), jest reprezentowane przez [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) obiektu. `output_data1` jest tworzony jako dane wyjściowe kroku i używany jako dane wejściowe jednego lub kilku przyszłych kroków. `PipelineData` wprowadza zależność danych między krokami i tworzy niejawną kolejność wykonywania w potoku. Ten obiekt będzie później używany podczas tworzenia kroków potoku.

```python
from azureml.pipeline.core import PipelineData

output_data1 = PipelineData(
    "output_data1",
    datastore=def_blob_store,
    output_name="output_data1")
```

### <a name="configure-data-using-datasets"></a>Skonfiguruj dane przy użyciu zestawów danych

Jeśli dane tabelaryczne są przechowywane w pliku lub zbiorze plików, [TabularDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) jest efektywną alternatywą dla `DataReference`. obiekty `TabularDataset` obsługują przechowywanie wersji, diffe i statystyki podsumowujące. `TabularDataset`s są oceniane opóźnieniem (na przykład generatory języka Python) i wydajne, aby poddzielić je przez dzielenie lub filtrowanie. Klasa `FileDataset` oferuje podobne opóźnieniem-oceniane dane reprezentujące jeden lub więcej plików. 

Tworzysz `TabularDataset` przy użyciu metod, takich jak [from_delimited_files](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?view=azure-ml-py#from-delimited-files-path--validate-true--include-path-false--infer-column-types-true--set-column-types-none--separator------header-true--partition-format-none-).

```python
from azureml.data import TabularDataset

iris_tabular_dataset = Dataset.Tabular.from_delimited_files([(def_blob_store, 'train-dataset/tabular/iris.csv')])
```

 Tworzysz `FileDataset` przy użyciu [from_files](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory?view=azure-ml-py#from-files-path--validate-true-).

 Więcej informacji na temat pracy z zestawami danych można znaleźć w tematach [dodawanie & Rejestrowanie zestawów danych](how-to-create-register-datasets.md) lub [tego przykładowego notesu](https://aka.ms/tabulardataset-samplenotebook).

## <a name="set-up-compute-target"></a>Konfigurowanie obliczeniowego elementu docelowego

W Azure Machine Learning termin __obliczenia__ (lub __element docelowy obliczeń__) odnosi się do maszyn lub klastrów wykonujących kroki obliczeniowe w potoku uczenia maszynowego.   Zobacz [cele obliczeń dla szkolenia modelu](how-to-set-up-training-targets.md) , aby uzyskać pełną listę elementów docelowych obliczeń oraz sposób tworzenia i dołączania ich do obszaru roboczego.  Proces tworzenia i dołączania obiektu docelowego obliczeń jest taki sam, niezależnie od tego, czy jest to szkoleniowy model, czy też uruchamiany jest krok potoku. Po utworzeniu i dołączeniu obiektu docelowego obliczeń Użyj obiektu `ComputeTarget` w [kroku potoku](#steps).

> [!IMPORTANT]
> Wykonywanie operacji zarządzania na obiektach docelowych obliczeń nie jest obsługiwane w ramach zadań zdalnych. Potoki uczenia maszynowego są przesyłane jako zadania zdalne, dlatego nie należy używać operacji zarządzania na obiektach docelowych obliczeń z wnętrza potoku.

Poniżej przedstawiono przykłady tworzenia i dołączania obiektów docelowych obliczeń dla:

* Usługi Azure Machine Learning obliczeń
* Azure Databricks 
* Azure Data Lake Analytics

### <a name="azure-machine-learning-compute"></a>Obliczeniowe platformy Azure Machine Learning

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

Utwórz obszar roboczy Azure Databricks, zanim go użyjesz. Aby utworzyć zasób obszaru roboczego, zobacz dokument [Uruchamianie zadania Spark na Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal) .

Aby dołączyć Azure Databricks jako element docelowy obliczeń, podaj następujące informacje:

* __Nazwa obliczeniowa datakostek__: nazwa, która ma zostać przypisana do tego zasobu obliczeniowego.
* __Nazwa obszaru roboczego usługi Databricks__: Nazwa obszaru roboczego usługi Azure Databricks.
* __Token dostępu do datakostki__: token dostępu używany do uwierzytelniania w Azure Databricks. Aby wygenerować token dostępu, zobacz [uwierzytelniania](https://docs.azuredatabricks.net/dev-tools/api/latest/authentication.html) dokumentu.

Poniższy kod ilustruje sposób dołączania Azure Databricks jako obiektu docelowego obliczeń przy użyciu zestawu SDK Azure Machine Learning (__obszar roboczy datakostki musi znajdować się w tej samej subskrypcji co obszar roboczy AML__):

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

* __Nazwa obliczeniowego__: nazwa, którą chcesz przypisać do tego zasobu obliczeniowego.
* __Grupa zasobów__: grupy zasobów zawierającej konto usługi Data Lake Analytics.
* __Nazwa konta__: Nazwa konta usługi Data Lake Analytics.

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
from azureml.pipeline.steps import PythonScriptStep

trainStep = PythonScriptStep(
    script_name="train.py",
    arguments=["--input", blob_input_data, "--output", output_data1],
    inputs=[blob_input_data],
    outputs=[output_data1],
    compute_target=compute_target,
    source_directory=project_folder
)
```

Ponowne użycie poprzednich wyników (`allow_reuse`) jest kluczem w przypadku używania potoków w środowisku współpracy, ponieważ wyeliminowanie niepotrzebnych ponownych prób zapewnia elastyczność. Ponowne użycie jest zachowaniem domyślnym, gdy script_name, dane wejściowe i parametry kroku pozostają takie same. Gdy dane wyjściowe kroku są ponownie używane, zadanie nie zostanie przesłane do obliczenia, a wyniki z poprzedniego przebiegu są natychmiast dostępne dla uruchomienia następnego kroku. Jeśli `allow_reuse` jest ustawiona na false, nowy przebieg będzie zawsze generowany dla tego kroku podczas wykonywania potoku. 

Po zdefiniowaniu kroków można skompilować potok za pomocą niektórych lub wszystkich tych kroków.

> [!NOTE]
> Podczas definiowania kroków lub kompilowania potoku nie są przekazywane żadne pliki ani dane do Azure Machine Learning.

```python
# list of steps to run
compareModels = [trainStep, extractStep, compareStep]

from azureml.pipeline.core import Pipeline

# Build the pipeline
pipeline1 = Pipeline(workspace=ws, steps=[compareModels])
```

Poniższy przykład używa utworzonego wcześniej elementu docelowego obliczeń Azure Databricks: 

```python
from azureml.pipeline.steps import DatabricksStep

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

### <a name="use-a-dataset"></a>Korzystanie z zestawu danych 

Aby użyć eith `TabularDataset` lub `FileDataset` w potoku, należy przekształcić go w obiekt [DatasetConsumptionConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_consumption_config.datasetconsumptionconfig?view=azure-ml-py) , wywołując [as_named_input (Name)](https://docs.microsoft.com/python/api/azureml-core/azureml.data.abstract_dataset.abstractdataset?view=azure-ml-py#as-named-input-name-). Ten obiekt `DatasetConsumptionConfig` można przekazać jako jeden z `inputs` do etapu potoku. 

```python
dataset_consuming_step = PythonScriptStep(
    script_name="iris_train.py",
    inputs=[iris_tabular_dataset.as_named_input("iris_data")],
    compute_target=compute_target,
    source_directory=project_folder
)
```

Następnie można pobrać zestaw danych w potoku przy użyciu słownika [Run. input_datasets](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#input-datasets) .

```python
# iris_train.py
from azureml.core import Run, Dataset

run_context = Run.get_context()
iris_dataset = run_context.input_datasets['iris_data']
dataframe = iris_dataset.to_pandas_dataframe()
```

Aby uzyskać więcej informacji, zobacz [pakiet Azure-Pipeline-etaps](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py) i informacje o [klasie potoku](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline%28class%29?view=azure-ml-py) .

## <a name="submit-the-pipeline"></a>Przesyłanie potoku

Gdy przesyłasz potok, Azure Machine Learning sprawdza zależności dla każdego kroku i przekazuje migawkę podanego katalogu źródłowego. Jeśli katalog źródłowy nie zostanie określony, bieżący katalog lokalny jest przekazywany. Migawka jest również przechowywana w ramach eksperymentu w obszarze roboczym.

> [!IMPORTANT]
> Aby uniemożliwić Dołączanie plików do migawki, Utwórz plik [. gitignore](https://git-scm.com/docs/gitignore) lub `.amlignore` w katalogu i Dodaj do niego pliki. Plik `.amlignore` używa tej samej składni i wzorców co plik [. gitignore](https://git-scm.com/docs/gitignore) . Jeśli istnieją oba pliki, plik `.amlignore` ma pierwszeństwo.
>
> Aby uzyskać więcej informacji, zobacz [migawki](concept-azure-machine-learning-architecture.md#snapshots).

```python
from azureml.core import Experiment

# Submit the pipeline to be run
pipeline_run1 = Experiment(ws, 'Compare_Models_Exp').submit(pipeline1)
pipeline_run1.wait_for_completion()
```

Podczas pierwszego uruchomienia potoku Azure Machine Learning:

* Pobiera migawkę projektu do obiektu docelowego obliczeń z magazynu obiektów BLOB skojarzonego z obszarem roboczym.
* Kompiluje obraz platformy Docker odpowiadający każdemu krokowi w potoku.
* Pobiera obraz platformy Docker dla każdego kroku do obiektu docelowego obliczeń z rejestru kontenerów.
* Instaluje magazyn danych, jeśli w kroku jest określony obiekt `DataReference`. Jeśli instalacji nie jest obsługiwany, dane zamiast tego jest kopiowany do obliczeniowego elementu docelowego.
* Uruchamia krok w elemencie docelowym obliczeń określonym w definicji kroku. 
* Tworzy artefakty, takie jak dzienniki, stdout i stderr, metryki i dane wyjściowe określone przez krok. Te artefakty są następnie przekazywane i przechowywane w domyślnym magazynie danych użytkownika.

![Diagram uruchamiania eksperymentu jako potoku](./media/how-to-create-your-first-pipeline/run_an_experiment_as_a_pipeline.png)

Aby uzyskać więcej informacji, zobacz informacje o [klasie eksperymentów](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py) .

### <a name="view-results-of-a-pipeline"></a>Wyświetlanie wyników potoku

Zobacz listę wszystkich potoków i ich szczegóły uruchamiania w programie Studio:

1. Zaloguj się do [Azure Machine Learning Studio](https://ml.azure.com).

1. [Wyświetlanie obszaru roboczego](how-to-manage-workspace.md#view).

1. Po lewej stronie wybierz pozycję **potoki** , aby wyświetlić wszystkie uruchomienia potoków.
 ![listy potoków uczenia maszynowego](./media/how-to-create-your-first-pipeline/pipelines.png)
 
1. Wybierz określone potoku, aby zobaczyć wyniki jego działania.

## <a name="git-tracking-and-integration"></a>Śledzenie i integracja usługi git

Po rozpoczęciu szkolenia w przypadku, gdy katalog źródłowy jest lokalnym repozytorium git, informacje o repozytorium są przechowywane w historii uruchamiania. Aby uzyskać więcej informacji, zobacz Integracja z usługą [git dla Azure Machine Learning](concept-train-model-git-integration.md).

## <a name="publish-a-pipeline"></a>Publikowanie potoku

Możesz opublikować potoku, aby uruchomić go później przy użyciu różnych danych wejściowych. W przypadku punktu końcowego REST już opublikowanego potoku w celu zaakceptowania parametrów należy Sparametryzuj potok przed opublikowaniem.

1. Aby utworzyć parametr potoku, użyj [PipelineParameter](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.pipelineparameter?view=azure-ml-py) obiekt z wartością domyślną.

   ```python
   from azureml.pipeline.core.graph import PipelineParameter
   
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
   published_pipeline1 = pipeline_run1.publish_pipeline(
        name="My_Published_Pipeline",
        description="My Published Pipeline Description",
        version="1.0")
   ```

### <a name="run-a-published-pipeline"></a>Uruchamianie potoku opublikowane

Wszystkie opublikowane potoki mają punkt końcowy REST. Ten punkt końcowy wywołuje przebieg potoku z systemów zewnętrznych, takich jak klienci spoza języka Python. Ten punkt końcowy włącza "zarządzaną powtarzalność" w ramach oceniania wsadowego i scenariuszy ponownego szkolenia.

Aby wywołać poprzedni potok, należy uzyskać token nagłówka uwierzytelniania Azure Active Directory, zgodnie z opisem w dokumentacji dotyczącej [klasy AzureCliAuthentication](https://docs.microsoft.com/python/api/azureml-core/azureml.core.authentication.azurecliauthentication?view=azure-ml-py) lub uzyskać więcej szczegółów dotyczących [uwierzytelniania w Azure Machine Learning](https://aka.ms/pl-restep-auth) notesie.

```python
from azureml.pipeline.core import PublishedPipeline
import requests

response = requests.post(published_pipeline1.endpoint,
                         headers=aad_token,
                         json={"ExperimentName": "My_Pipeline",
                               "ParameterAssignments": {"pipeline_arg": 20}})
```

## <a name="create-a-versioned-pipeline-endpoint"></a>Tworzenie punktu końcowego potoku z wersją
Można utworzyć punkt końcowy potoku z wieloma opublikowanymi potokami. Ten element może być używany jak opublikowany potok, ale zapewnia stały punkt końcowy REST podczas iteracji i aktualizacji potoków ML.

```python
from azureml.pipeline.core import PipelineEndpoint

published_pipeline = PublishedPipeline.get(workspace="ws", name="My_Published_Pipeline")
pipeline_endpoint = PipelineEndpoint.publish(workspace=ws, name="PipelineEndpointTest",
                                            pipeline=published_pipeline, description="Test description Notebook")
```

### <a name="submit-a-job-to-a-pipeline-endpoint"></a>Przesyłanie zadania do punktu końcowego potoku
Można przesłać zadanie do domyślnej wersji punktu końcowego potoku:
```python
pipeline_endpoint_by_name = PipelineEndpoint.get(workspace=ws, name="PipelineEndpointTest")
run_id = pipeline_endpoint_by_name.submit("PipelineEndpointExperiment")
print(run_id)
```
Możesz również przesłać zadanie do określonej wersji:
```python
run_id = pipeline_endpoint_by_name.submit("PipelineEndpointExperiment", pipeline_version="0")
print(run_id)
```

Tę samą funkcję można wykonać przy użyciu interfejsu API REST:
```python
rest_endpoint = pipeline_endpoint_by_name.endpoint
response = requests.post(rest_endpoint, 
                         headers=aad_token, 
                         json={"ExperimentName": "PipelineEndpointExperiment",
                               "RunSource": "API",
                               "ParameterAssignments": {"1": "united", "2":"city"}})
```

### <a name="use-published-pipelines-in-the-studio"></a>Używanie opublikowanych potoków w Studio

Możesz również uruchomić opublikowany potok z programu Studio:

1. Zaloguj się do [Azure Machine Learning Studio](https://ml.azure.com).

1. [Wyświetlanie obszaru roboczego](how-to-manage-workspace.md#view).

1. Po lewej stronie wybierz pozycję **punkty końcowe**.

1. W górnej części wybierz pozycję **punkty końcowe potoku**.
 ![listę opublikowanych potoków uczenia maszynowego](./media/how-to-create-your-first-pipeline/pipeline-endpoints.png)

1. Wybierz konkretny potok, który ma zostać uruchomiony, zużyty lub przejrzany wyniki poprzednich przebiegów punktu końcowego potoku.


### <a name="disable-a-published-pipeline"></a>Wyłącz opublikowany potok

Aby ukryć potok z listy opublikowanych potoków, należy go wyłączyć w programie Studio lub z zestawu SDK:

```
# Get the pipeline by using its ID from Azure Machine Learning studio
p = PublishedPipeline.get(ws, id="068f4885-7088-424b-8ce2-eeb9ba5381a6")
p.disable()
```

Można ją ponownie włączyć z `p.enable()`. Aby uzyskać więcej informacji, zobacz [PublishedPipeline klasy](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.publishedpipeline?view=azure-ml-py) Reference.


## <a name="caching--reuse"></a>Buforowanie & ponownie używane  

Aby zoptymalizować i dostosować zachowanie potoków, można wykonać kilka czynności związanych z buforowaniem i wielokrotnym użyciem. Można na przykład wybrać następujące opcje:
+ Wyłącz **domyślne ponowne użycie kroku uruchamiania etapu** przez ustawienie `allow_reuse=False` podczas [definiowania kroku](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py). Ponowne użycie jest kluczem przy użyciu potoków w środowisku współpracy, ponieważ wyeliminowanie niepotrzebnych uruchomień zapewnia elastyczność. Można jednak zrezygnować z ponownego użycia.
+ **Wymuś ponowne wygenerowanie danych wyjściowych dla wszystkich kroków w przebiegu** z `pipeline_run = exp.submit(pipeline, regenerate_outputs=False)`

Domyślnie `allow_reuse` dla kroków jest włączona, a `source_directory` określone w definicji kroku są skrótem. Tak więc, jeśli skrypt dla danego kroku pozostaje taki sam (`script_name`, dane wejściowe i parametry), a żadne inne w` source_directory` nie zmieniły się, dane wyjściowe przebiegu poprzedniego kroku są ponownie używane, zadanie nie zostanie przesłane do obliczenia, a wyniki poprzedniego przebiegu są natychmiast dostępne w następnym kroku.

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
- Zapoznaj się z dokumentacją zestawu SDK dla pakietu [Azure-Pipelines-Core](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py) oraz pakietem [kroków potoków usługi Azure](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py) .
- Zobacz Porady [dotyczące debugowania](how-to-debug-pipelines.md) i rozwiązywania problemów z potokami.

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]
