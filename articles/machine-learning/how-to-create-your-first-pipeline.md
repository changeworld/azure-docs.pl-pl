---
title: Tworzenie, uruchamianie, & śledzenie potoków ml
titleSuffix: Azure Machine Learning
description: Tworzenie i uruchamianie potoku uczenia maszynowego za pomocą zestawu SDK usługi Azure Machine Learning dla języka Python. Użyj potoków ml, aby utworzyć przepływy pracy, które łączą ze sobą fazy uczenia maszynowego (ML). Fazy te obejmują przygotowanie danych, szkolenie modelu, wdrażanie modelu i wnioskowanie/ocenianie.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: sgilley
ms.author: sanpil
author: sanpil
ms.date: 12/05/2019
ms.custom: seodec18
ms.openlocfilehash: fa0a5bfe921687ad964e9321e3874de37ccf9b98
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80549312"
---
# <a name="create-and-run-machine-learning-pipelines-with-azure-machine-learning-sdk"></a>Tworzenie i uruchamianie potoków uczenia maszynowego za pomocą zestawu SDK usługi Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

W tym artykule dowiesz się, jak tworzyć, publikować, uruchamiać i śledzić [potok uczenia maszynowego](concept-ml-pipelines.md) przy użyciu [sdk usługi Azure Machine Learning.](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)  Użyj **potoków uczenia maszynowego,** aby utworzyć przepływ pracy, który łączy różne fazy ml, a następnie opublikować ten potok w obszarze roboczym usługi Azure Machine Learning, aby uzyskać dostęp później lub udostępnić innym osobom.  Potoki ml są idealne dla scenariuszy oceniania partii, przy użyciu różnych obliczeń, ponownego użycia kroków zamiast ponownego uruchamiania ich, a także udostępniania przepływów pracy ml z innymi.

Chociaż można użyć innego rodzaju potoku o nazwie [azure potoku](https://docs.microsoft.com/azure/devops/pipelines/targets/azure-machine-learning?context=azure%2Fmachine-learning%2Fservice%2Fcontext%2Fml-context&view=azure-devops&tabs=yaml) dla automatyzacji uczenia/ciągłego wdrażania zadań ml, tego typu potoku nigdy nie jest przechowywany wewnątrz obszaru roboczego. [Porównaj te różne potoki](concept-ml-pipelines.md#which-azure-pipeline-technology-should-i-use).

Każda faza potoku uczenia maszynowego, taka jak przygotowanie danych i szkolenie modelu, może zawierać jeden lub więcej kroków.

Potoki ml, które tworzysz są widoczne dla członków [obszaru roboczego](how-to-manage-workspace.md)usługi Azure Machine Learning. 

Potoki ml używają zdalnych celów obliczeniowych do obliczeń i przechowywania danych pośrednich i końcowych skojarzonych z tym potokiem. Mogą odczytywać i zapisywać dane do i z obsługiwanych lokalizacji [usługi Azure Storage.](https://docs.microsoft.com/azure/storage/)

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz bezpłatne konto. Wypróbuj [bezpłatną lub płatną wersję usługi Azure Machine Learning](https://aka.ms/AMLFree).

## <a name="prerequisites"></a>Wymagania wstępne

* Utwórz [obszar roboczy usługi Azure Machine Learning,](how-to-manage-workspace.md) aby pomieścić wszystkie zasoby potoku.

* [Skonfiguruj środowisko programistyczne,](how-to-configure-environment.md) aby zainstalować zestaw SDK usługi Azure Machine Learning lub użyj [wystąpienia obliczeniowego usługi Azure Machine Learning (w wersji zapoznawczej)](concept-compute-instance.md) z już zainstalowanym zestawem SDK.

Zacznij od podłączenia obszaru roboczego:

```Python
import azureml.core
from azureml.core import Workspace, Datastore

ws = Workspace.from_config()
```


## <a name="set-up-machine-learning-resources"></a>Konfigurowanie zasobów uczenia maszynowego

Utwórz zasoby wymagane do uruchomienia potoku uczenia maszynowego:

* Skonfiguruj magazyn danych używany do uzyskiwania dostępu do danych potrzebnych w krokach potoku.

* Skonfiguruj obiekt, `DataReference` aby wskazywał dane, które są dostępne w magazynie danych lub są dostępne w magazynie danych.

* Skonfiguruj [obiekty docelowe obliczeń,](concept-azure-machine-learning-architecture.md#compute-targets) na których będą uruchamiane kroki potoku.

### <a name="set-up-a-datastore"></a>Konfigurowanie magazynu danych

Magazyn danych przechowuje dane dla potoku, aby uzyskać dostęp. Każdy obszar roboczy ma domyślny magazyn danych. Można zarejestrować dodatkowe magazyny danych. 

Podczas tworzenia obszaru roboczego [usługi Azure Files](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) i usługi Azure [Blob storage](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction) są dołączone do obszaru roboczego. Domyślny magazyn danych jest zarejestrowany, aby połączyć się z magazynem obiektów Blob platformy Azure. Aby dowiedzieć się więcej, zobacz [Podejmowanie decyzji o używaniu plików Azure, obiektów blob platformy Azure lub dysków platformy Azure.](https://docs.microsoft.com/azure/storage/common/storage-decide-blobs-files-disks) 

```python
# Default datastore 
def_data_store = ws.get_default_datastore()

# Get the blob storage associated with the workspace
def_blob_store = Datastore(ws, "workspaceblobstore")

# Get file storage associated with the workspace
def_file_store = Datastore(ws, "workspacefilestore")

```

Przekaż pliki danych lub katalogi do magazynu danych, aby były dostępne z potoków. W tym przykładzie używa magazynu obiektów Blob jako magazynu danych:

```python
def_blob_store.upload_files(
    ["./data/20news.pkl"],
    target_path="20newsgroups",
    overwrite=True)
```

Potok składa się z co najmniej jednego kroki. Krok jest jednostką uruchamianą na podstawie celu obliczeniowego. Kroki mogą korzystać ze źródeł danych i tworzyć "pośrednie" dane. Krok można utworzyć dane, takie jak model, katalog z modelu i plików zależnych lub danych tymczasowych. Te dane są następnie dostępne dla innych kroków w dalszej części potoku.

Aby dowiedzieć się więcej o łączeniu potoku z danymi, zobacz artykuły [Jak uzyskać dostęp do danych](how-to-access-data.md) i jak [zarejestrować zestawy danych](how-to-create-register-datasets.md). 

### <a name="configure-data-reference"></a>Konfigurowanie odwołania do danych

Właśnie utworzono źródło danych, do którego można odwoływać się w potoku jako dane wejściowe do kroku. Źródło danych w potoku jest reprezentowana przez [obiekt DataReference.](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference) Obiekt `DataReference` wskazuje dane, które są dostępne w magazynie danych lub są dostępne z magazynu danych.

```python
from azureml.data.data_reference import DataReference

blob_input_data = DataReference(
    datastore=def_blob_store,
    data_reference_name="test_data",
    path_on_datastore="20newsgroups/20news.pkl")
```

Dane pośrednie (lub dane wyjściowe kroku) są reprezentowane przez obiekt [PipelineData.](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) `output_data1`jest produkowany jako wyjście kroku i używany jako wejście jednego lub więcej przyszłych kroków. `PipelineData`wprowadza zależność danych między krokami i tworzy niejawną kolejność wykonywania w potoku. Ten obiekt będzie używany później podczas tworzenia kroków potoku.

```python
from azureml.pipeline.core import PipelineData

output_data1 = PipelineData(
    "output_data1",
    datastore=def_blob_store,
    output_name="output_data1")
```

### <a name="configure-data-using-datasets"></a>Konfigurowanie danych przy użyciu zestawów danych

Jeśli w pliku lub zestawie plików są przechowywane dane tabelaryczne, zestaw danych `DataReference` [tabelaryczne](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) jest skuteczną alternatywą dla pliku . `TabularDataset`obiekty obsługują przechowywanie wersji, różnice i statystyki podsumowania. `TabularDataset`s są leniwie oceniane (jak generatory Pythona) i jest to skuteczne, aby podzdjąć je przez dzielenie lub filtrowanie. Klasa `FileDataset` zawiera podobne dane lazily oceniane reprezentujące jeden lub więcej plików. 

Tworzenie `TabularDataset` przy użyciu metod, takich jak [from_delimited_files](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?view=azure-ml-py#from-delimited-files-path--validate-true--include-path-false--infer-column-types-true--set-column-types-none--separator------header-true--partition-format-none--support-multi-line-false-).

```python
from azureml.data import TabularDataset

iris_tabular_dataset = Dataset.Tabular.from_delimited_files([(def_blob_store, 'train-dataset/tabular/iris.csv')])
```

 Tworzysz `FileDataset` [from_files](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory?view=azure-ml-py#from-files-path--validate-true-)za pomocą .

 Możesz dowiedzieć się więcej o pracy z zestawami danych z [add & register datasets](how-to-create-register-datasets.md) lub [this sample notebook](https://aka.ms/train-datasets).

## <a name="set-up-compute-target"></a>Konfigurowanie obiektu docelowego obliczeń

W usłudze Azure Machine Learning termin __oblicza__ (lub __obiekt docelowy obliczeń)__ odnosi się do maszyn lub klastrów, które wykonują kroki obliczeniowe w potoku uczenia maszynowego.   Aby uzyskać pełną listę celów obliczeniowych i tworzenie i dołączanie ich do obszaru roboczego, zobacz [obiekty docelowe obliczeń](how-to-set-up-training-targets.md) dla szkoleń dla modelu.  Proces tworzenia i lub dołączania obiektu docelowego obliczeń jest taki sam, niezależnie od tego, czy trenujesz model, czy uruchamiasz krok potoku. Po utworzeniu i dołączeniu obiektu `ComputeTarget` docelowego obliczeń należy użyć obiektu w [kroku potoku](#steps).

> [!IMPORTANT]
> Wykonywanie operacji zarządzania na obiekty docelowe obliczeń nie jest obsługiwane z wewnątrz zadań zdalnych. Ponieważ potoki uczenia maszynowego są przesyłane jako zadanie zdalne, nie należy używać operacji zarządzania na obiekty docelowe obliczeń z wewnątrz potoku.

Poniżej znajdują się przykłady tworzenia i dołączania celów obliczeniowych dla:

* Środowisko obliczeniowe usługi Azure Machine Learning
* Azure Databricks 
* Azure Data Lake Analytics

### <a name="azure-machine-learning-compute"></a>Obliczenia usługi Azure Machine Learning

Można utworzyć obliczenia usługi Azure Machine Learning do uruchamiania kroków.

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

### <a name="azure-databricks"></a><a id="databricks"></a>Azure Databricks

Usługa Azure Databricks to środowisko oparte na platformie Spark firmy Apache w chmurze platformy Azure. Może służyć jako cel obliczeniowy z potoku usługi Azure Machine Learning.

Przed użyciem należy utworzyć obszar roboczy usługi Azure Databricks. Aby utworzyć zasób obszaru roboczego, zobacz [zadanie Uruchom program Spark w dokumencie usługi Azure Databricks.](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal)

Aby dołączyć usługi Azure Databricks jako miejsce docelowe obliczeń, podaj następujące informacje:

* __Databricks nazwa obliczeń:__ Nazwa, którą chcesz przypisać do tego zasobu obliczeniowego.
* __Databricks nazwa obszaru roboczego:__ Nazwa obszaru roboczego usługi Azure Databricks.
* __Databricks token dostępu:__ token dostępu używany do uwierzytelniania do usługi Azure Databricks. Aby wygenerować token dostępu, zobacz dokument [uwierzytelniania.](https://docs.azuredatabricks.net/dev-tools/api/latest/authentication.html)

Poniższy kod pokazuje, jak dołączyć usługi Azure Databricks jako miejsce docelowe obliczeń za pomocą sdk usługi Azure Machine Learning __(obszar roboczy Databricks musi znajdować się w tej samej subskrypcji co obszar roboczy AML):__

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

Aby uzyskać bardziej szczegółowy przykład, zobacz [przykładowy notes](https://aka.ms/pl-databricks) w usłudze GitHub.

### <a name="azure-data-lake-analytics"></a><a id="adla"></a>Azure Data Lake Analytics

Usługa Azure Data Lake Analytics to platforma do analizy dużych zbiorów danych w chmurze platformy Azure. Może służyć jako cel obliczeniowy z potoku usługi Azure Machine Learning.

Przed użyciem należy utworzyć konto usługi Azure Data Lake Analytics. Aby utworzyć ten zasób, zobacz [wprowadzenie do usługi Azure Data Lake Analytics](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-get-started-portal) dokumentu.

Aby dołączyć usługę Data Lake Analytics jako cel obliczeniowy, należy użyć zestawu SDK usługi Azure Machine Learning i podać następujące informacje:

* __Nazwa obliczeń:__ Nazwa, którą chcesz przypisać do tego zasobu obliczeniowego.
* __Grupa zasobów:__ Grupa zasobów zawierająca konto Usługi Data Lake Analytics.
* __Nazwa konta:__ nazwa konta Usługi Data Lake Analytics.

Poniższy kod pokazuje, jak dołączyć usługi Data Lake Analytics jako cel obliczeniowy:

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

Aby uzyskać bardziej szczegółowy przykład, zobacz [przykładowy notes](https://aka.ms/pl-adla) w usłudze GitHub.

> [!TIP]
> Potoki usługi Azure Machine Learning mogą pracować tylko z danymi przechowywanymi w domyślnym magazynie danych konta usługi Data Lake Analytics. Jeśli dane, z którymi musisz pracować, są przechowywane w [`DataTransferStep`](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.data_transfer_step.datatransferstep?view=azure-ml-py) magazynie nieobekonu domyślnym, można użyć a, aby skopiować dane przed szkoleniem.

## <a name="construct-your-pipeline-steps"></a><a id="steps"></a>Konstruuj kroki potoku

Po utworzeniu i dołączeniu obiektu docelowego obliczeń do obszaru roboczego można przystąpić do definiowania kroku potoku. Istnieje wiele wbudowanych kroków dostępnych za pośrednictwem narzędzia Azure Machine Learning SDK. Najbardziej podstawowym z tych kroków jest [PythonScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep?view=azure-ml-py), który uruchamia skrypt Języka Python w określonym miejscu docelowym obliczeń:

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

Ponowne użycie poprzednich`allow_reuse`wyników ( ) jest kluczowe podczas korzystania z potoków w środowisku współpracy, ponieważ wyeliminowanie niepotrzebnych powtórzeń zapewnia elastyczność. Ponowne użycie jest zachowaniem domyślnym, gdy script_name, dane wejściowe i parametry kroku pozostają takie same. Gdy dane wyjściowe kroku jest ponownie, zadanie nie jest przesyłane do obliczeń, zamiast tego wyniki z poprzedniego uruchomienia są natychmiast dostępne do następnego kroku uruchomienia. Jeśli `allow_reuse` jest ustawiona na false, nowy przebieg zawsze zostanie wygenerowany dla tego kroku podczas wykonywania potoku. 

Po zdefiniowaniu kroków, można utworzyć potoku przy użyciu niektórych lub wszystkich tych kroków.

> [!NOTE]
> Żaden plik lub dane nie są przekazywane do usługi Azure Machine Learning podczas definiowania kroków lub tworzenia potoku.

```python
# list of steps to run
compareModels = [trainStep, extractStep, compareStep]

from azureml.pipeline.core import Pipeline

# Build the pipeline
pipeline1 = Pipeline(workspace=ws, steps=[compareModels])
```

W poniższym przykładzie użyto docelowego obliczeń usługi Azure Databricks utworzonego wcześniej: 

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

### <a name="use-a-dataset"></a>Używanie zestawu danych 

Aby użyć pliku `TabularDataset` `FileDataset` z potokiem lub w jego potoku, należy przekształcić go w obiekt [DatasetConsumptionConfig,](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_consumption_config.datasetconsumptionconfig?view=azure-ml-py) wywołując [as_named_input(name)](https://docs.microsoft.com/python/api/azureml-core/azureml.data.abstract_dataset.abstractdataset?view=azure-ml-py#as-named-input-name-). Przekaż ten `DatasetConsumptionConfig` obiekt jako `inputs` jeden z kroku do potoku. 

Zestawy danych utworzone na podstawie magazynu obiektów Blob platformy Azure, plików Azure, usługi Azure Data Lake Storage Gen1, usługi Azure Data Lake Storage Gen2, bazy danych SQL Azure i bazy danych azure dla postgreSQL mogą być używane jako dane wejściowe do dowolnego kroku potoku. Z wyjątkiem zapisywania danych wyjściowych do [DataTransferStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.datatransferstep?view=azure-ml-py) lub [DatabricksStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.databricks_step.databricksstep?view=azure-ml-py), dane wyjściowe[(PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py)) mogą być zapisywane tylko w magazynach danych udostępniania obiektów Blob platformy Azure i azure file.

```python
dataset_consuming_step = PythonScriptStep(
    script_name="iris_train.py",
    inputs=[iris_tabular_dataset.as_named_input("iris_data")],
    compute_target=compute_target,
    source_directory=project_folder
)
```

Następnie należy pobrać zestaw danych w potoku przy użyciu [run.input_datasets](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#input-datasets) słownika.

```python
# iris_train.py
from azureml.core import Run, Dataset

run_context = Run.get_context()
iris_dataset = run_context.input_datasets['iris_data']
dataframe = iris_dataset.to_pandas_dataframe()
```

Aby uzyskać więcej informacji, zobacz [pakiet kroków potoku azure](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py) i odwołanie do [klasy potoku.](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline%28class%29?view=azure-ml-py)

## <a name="submit-the-pipeline"></a>Prześlij potok

Po przesłaniu potoku usługa Azure Machine Learning sprawdza zależności dla każdego kroku i przekazuje migawkę określonego katalogu źródłowego. Jeśli nie określono katalogu źródłowego, zostanie przekazany bieżący katalog lokalny. Migawka jest również przechowywana jako część eksperymentu w obszarze roboczym.

> [!IMPORTANT]
> Aby zapobiec doliczaniu plików do migawki, utwórz `.amlignore` [plik gitignore](https://git-scm.com/docs/gitignore) lub plik w katalogu i dodaj do niego pliki. Plik `.amlignore` używa tej samej składni i wzorców co plik [gitignore.](https://git-scm.com/docs/gitignore) Jeśli oba pliki `.amlignore` istnieją, plik ma pierwszeństwo.
>
> Aby uzyskać więcej informacji, zobacz [Migawki](concept-azure-machine-learning-architecture.md#snapshots).

```python
from azureml.core import Experiment

# Submit the pipeline to be run
pipeline_run1 = Experiment(ws, 'Compare_Models_Exp').submit(pipeline1)
pipeline_run1.wait_for_completion()
```

Po pierwszym uruchomieniu potoku usługi Azure Machine Learning:

* Pobiera migawkę projektu do miejsca docelowego obliczeń z magazynu obiektów Blob skojarzonego z obszarem roboczym.
* Tworzy obraz platformy Docker odpowiadający każdemu krokowi w potoku.
* Pobiera obraz platformy Docker dla każdego kroku do obiektu docelowego obliczeń z rejestru kontenerów.
* Montuje magazyn danych, `DataReference` jeśli obiekt jest określony w kroku. Jeśli instalacja nie jest obsługiwana, dane są kopiowane do obiektu docelowego obliczeń.
* Uruchamia krok w celu obliczeń określonych w definicji kroku. 
* Tworzy artefakty, takie jak dzienniki, stdout i stderr, metryki i dane wyjściowe określone przez krok. Te artefakty są następnie przekazywane i przechowywane w domyślnym magazynie danych użytkownika.

![Diagram uruchamiania eksperymentu jako potoku](./media/how-to-create-your-first-pipeline/run_an_experiment_as_a_pipeline.png)

Aby uzyskać więcej informacji, zobacz odwołanie do [klasy eksperymentu.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py)

### <a name="view-results-of-a-pipeline"></a>Wyświetlanie wyników potoku

Zobacz listę wszystkich potoków i ich szczegóły uruchomienia w studio:

1. Zaloguj się do [studia usługi Azure Machine Learning](https://ml.azure.com).

1. [Wyświetlanie obszaru roboczego](how-to-manage-workspace.md#view).

1. Po lewej stronie wybierz **pipelines,** aby wyświetlić wszystkie uruchomień potoku.
 ![lista potoków uczenia maszynowego](./media/how-to-create-your-first-pipeline/pipelines.png)
 
1. Wybierz określony potok, aby wyświetlić wyniki uruchomienia.

## <a name="git-tracking-and-integration"></a>Śledzenie i integracja Git

Po uruchomieniu przebiegu szkolenia, w którym katalog źródłowy jest lokalnym repozytorium Git, informacje o repozytorium są przechowywane w historii uruchamiania. Aby uzyskać więcej informacji, zobacz [Integracja git dla usługi Azure Machine Learning](concept-train-model-git-integration.md).

## <a name="publish-a-pipeline"></a>Publikowanie potoku

Można opublikować potok, aby uruchomić go z różnych danych wejściowych później. Dla punktu końcowego REST już opublikowanego potoku do akceptowania parametrów, należy sparametryzować potoku przed opublikowaniem.

1. Aby utworzyć parametr potoku, należy użyć [pipelineParameter](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.pipelineparameter?view=azure-ml-py) obiektu z wartością domyślną.

   ```python
   from azureml.pipeline.core.graph import PipelineParameter
   
   pipeline_param = PipelineParameter(
     name="pipeline_arg",
     default_value=10)
   ```

2. Dodaj `PipelineParameter` ten obiekt jako parametr do dowolnego z kroków w potoku w następujący sposób:

   ```python
   compareStep = PythonScriptStep(
     script_name="compare.py",
     arguments=["--comp_data1", comp_data1, "--comp_data2", comp_data2, "--output_data", out_data3, "--param1", pipeline_param],
     inputs=[ comp_data1, comp_data2],
     outputs=[out_data3],
     compute_target=compute_target,
     source_directory=project_folder)
   ```

3. Opublikuj ten potok, który zaakceptuje parametr po wywołaniu.

   ```python
   published_pipeline1 = pipeline_run1.publish_pipeline(
        name="My_Published_Pipeline",
        description="My Published Pipeline Description",
        version="1.0")
   ```

### <a name="run-a-published-pipeline"></a>Uruchamianie opublikowanego potoku

Wszystkie opublikowane potoki mają punkt końcowy REST. Ten punkt końcowy wywołuje uruchomienie potoku z systemów zewnętrznych, takich jak klienci spoza języka Python. Ten punkt końcowy umożliwia "powtarzalność zarządzana" w scenariuszach oceniania i ponownego trenowania partii.

Aby wywołać uruchomienie poprzedniego potoku, potrzebujesz tokenu nagłówka uwierzytelniania usługi Azure Active Directory, zgodnie z opisem w [usłudze AzureCliAuthentication class](https://docs.microsoft.com/python/api/azureml-core/azureml.core.authentication.azurecliauthentication?view=azure-ml-py) reference lub uzyskać więcej szczegółów w uwierzytelniania w notesie [usługi Azure Machine Learning.](https://aka.ms/pl-restep-auth)

```python
from azureml.pipeline.core import PublishedPipeline
import requests

response = requests.post(published_pipeline1.endpoint,
                         headers=aad_token,
                         json={"ExperimentName": "My_Pipeline",
                               "ParameterAssignments": {"pipeline_arg": 20}})
```

## <a name="create-a-versioned-pipeline-endpoint"></a>Tworzenie wersji dla punktu końcowego potoku
Można utworzyć punkt końcowy potoku z wielu opublikowanych potoków za nim. Może to być używane jak opublikowany potok, ale daje stały punkt końcowy REST podczas iteracji i aktualizacji potoków ml.

```python
from azureml.pipeline.core import PipelineEndpoint

published_pipeline = PublishedPipeline.get(workspace="ws", name="My_Published_Pipeline")
pipeline_endpoint = PipelineEndpoint.publish(workspace=ws, name="PipelineEndpointTest",
                                            pipeline=published_pipeline, description="Test description Notebook")
```

### <a name="submit-a-job-to-a-pipeline-endpoint"></a>Przesyłanie zadania do punktu końcowego potoku
Zadanie można przesłać do domyślnej wersji punktu końcowego potoku:
```python
pipeline_endpoint_by_name = PipelineEndpoint.get(workspace=ws, name="PipelineEndpointTest")
run_id = pipeline_endpoint_by_name.submit("PipelineEndpointExperiment")
print(run_id)
```
Zadanie można również przesłać do określonej wersji:
```python
run_id = pipeline_endpoint_by_name.submit("PipelineEndpointExperiment", pipeline_version="0")
print(run_id)
```

To samo można osiągnąć za pomocą interfejsu API REST:
```python
rest_endpoint = pipeline_endpoint_by_name.endpoint
response = requests.post(rest_endpoint, 
                         headers=aad_token, 
                         json={"ExperimentName": "PipelineEndpointExperiment",
                               "RunSource": "API",
                               "ParameterAssignments": {"1": "united", "2":"city"}})
```

### <a name="use-published-pipelines-in-the-studio"></a>Korzystanie z opublikowanych potoków w studio

Można również uruchomić opublikowany potok ze studia:

1. Zaloguj się do [studia usługi Azure Machine Learning](https://ml.azure.com).

1. [Wyświetlanie obszaru roboczego](how-to-manage-workspace.md#view).

1. Po lewej stronie wybierz pozycję **Punkty końcowe**.

1. U góry wybierz pozycję **Punkty końcowe potoku**.
 ![lista opublikowanych potoków uczenia maszynowego](./media/how-to-create-your-first-pipeline/pipeline-endpoints.png)

1. Wybierz określony potok do uruchomienia, zużycia lub przeglądu wyników poprzednich przebiegów punktu końcowego potoku.


### <a name="disable-a-published-pipeline"></a>Wyłączanie opublikowanego potoku

Aby ukryć potok z listy opublikowanych potoków, należy go wyłączyć w studio lub w zestawie SDK:

```
# Get the pipeline by using its ID from Azure Machine Learning studio
p = PublishedPipeline.get(ws, id="068f4885-7088-424b-8ce2-eeb9ba5381a6")
p.disable()
```

Można go włączyć `p.enable()`ponownie za pomocą pliku . Aby uzyskać więcej informacji, zobacz [PublishedPipeline class](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.publishedpipeline?view=azure-ml-py) reference.


## <a name="caching--reuse"></a>Buforowanie & ponowne użycie  

Aby zoptymalizować i dostosować zachowanie potoków, można wykonać kilka czynności wokół buforowania i ponownego użycia. Można na przykład wybrać:
+ **Wyłącz domyślne ponowne użycie danych wyjściowych przebiegu kroku,** ustawiając `allow_reuse=False` podczas [definiowania kroku](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py). Ponowne użycie jest kluczowe podczas korzystania z potoków w środowisku współpracy, ponieważ wyeliminowanie niepotrzebnych przebiegów zapewnia elastyczność. Możesz jednak zrezygnować z ponownego użycia.
+ **Wymusz regenerację wyjściową dla wszystkich kroków w biegu** za pomocą`pipeline_run = exp.submit(pipeline, regenerate_outputs=False)`

Domyślnie `allow_reuse` dla kroków jest `source_directory` włączona i określone w definicji kroku jest mieszany. Tak więc, jeśli skrypt dla danego kroku`script_name`pozostaje taka sama ( , dane` source_directory` wejściowe i parametry), a nic innego w nie uległo zmianie, dane wyjściowe poprzedniego przebiegu kroku jest ponownie, zadanie nie jest przesyłane do obliczeń, a wyniki z poprzedniego uruchomienia są natychmiast dostępne do następnego kroku zamiast tego.

```python
step = PythonScriptStep(name="Hello World",
                        script_name="hello_world.py",
                        compute_target=aml_compute,
                        source_directory=source_directory,
                        allow_reuse=False,
                        hash_paths=['hello_world.ipynb'])
```

## <a name="next-steps"></a>Następne kroki

- Użyj [tych notesów Jupyter w usłudze GitHub,](https://aka.ms/aml-pipeline-readme) aby dalej eksplorować potoki uczenia maszynowego.
- Zobacz pomoc referencyjną zestawu SDK dla pakietu [azureml-pipelines-core](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py) i pakietu [azureml-pipelines-steps.](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py)
- Zapoznaj się z [instrukcjami dotyczącymi](how-to-debug-pipelines.md) debugowania i rozwiązywania problemów z potokami.

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]
