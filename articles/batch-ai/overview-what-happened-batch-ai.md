---
title: Co się dzieje w usłudze Apache Batch AI? | Microsoft Docs
description: Dowiedz się więcej o tym, co się dzieje z usługą Azure Batch AI i opcją obliczeniową usługi Azure Machine Learning.
services: batch-ai
author: garyericson
ms.service: batch-ai
ms.topic: overview
ms.date: 2/14/2019
ms.author: garye
ms.openlocfilehash: fb1114e94c227ce6787532c6059186399d0f57f0
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/27/2019
ms.locfileid: "56961363"
---
# <a name="whats-happening-to-azure-batch-ai"></a>Co się dzieje z usługą Apache Batch AI?

**Usługa Azure Batch AI zostanie wycofana w marcu.** Możliwości usługi Batch AI związane z trenowaniem i ocenianiem na dużą skalę są teraz dostępne w [usłudze Azure Machine Learning](../machine-learning/service/overview-what-is-azure-ml.md), która stanie się ogólnodostępna 4 grudnia 2018 r.

Wśród wielu możliwości dotyczących uczenia maszynowego usługa Azure Machine Learning obejmuje chmurowy, zarządzany docelowy obiekt obliczeniowy do trenowania, wdrażania i oceniania modeli uczenia maszynowego. Ten docelowy obiekt obliczeniowy jest nazywany [środowiskiem obliczeniowym usługi Azure Machine Learning](../machine-learning/service/how-to-set-up-training-targets.md#amlcompute). [Migrację i korzystanie z tego obiektu warto zacząć już dziś](#migrate). Interakcja z usługą Azure Machine Learning jest możliwa za pomocą wbudowanych zestawów [Python SDK](../machine-learning/service/quickstart-create-workspace-with-python.md), interfejsu wiersza polecenia i [witryny Azure portal](../machine-learning/service/quickstart-get-started.md).

## <a name="support-timeline"></a>Oś czasu pomocy technicznej

| Date | Szczegółowe informacje o obsłudze usługi Batch AI |
| ---- |-----------------|
| &nbsp;14&nbsp; grudnia 2018| Z istniejących subskrypcji usługi Azure Batch AI można korzystać jak dotychczas. Jednak nie można dodawać **nowych subskrypcji** ani nie są dokonywane nowe inwestycje.|
| &nbsp;31&nbsp; marca 2019 | Po tej dacie istniejące subskrypcje usługi Batch AI przestaną działać. |

## <a name="how-does-azure-machine-learning-service-compare"></a>Jak wygląda porównanie z usługą Azure Machine Learning Service?
Jest to usługa w chmurze, która pozwala trenować, wdrażać i automatyzować modele uczenia maszynowego oraz zarządzać nimi. Wszystko to jest możliwe na dużą skalę zapewnianą przez chmurę. Uzyskaj ogólne informacje na temat [usługi Azure Machine Learning Service w tym omówieniu](../machine-learning/service/overview-what-is-azure-ml.md).
 

Typowy cykl życia projektowania modelu obejmuje fazy przygotowywania danych, trenowania i eksperymentowania oraz wdrażania. Ten kompleksowy cykl można orkiestrować przy użyciu potoków uczenia maszynowego.

![Diagram przepływu](./media/overview-what-happened-batch-ai/lifecycle.png)


[Dowiedz się więcej na temat sposobu działania usługi i jej głównych pojęć](../machine-learning/service/concept-azure-machine-learning-architecture.md). Wiele pojęć w przepływie pracy trenowania modelu jest podobnych do pojęć istniejących w usłudze Batch AI. 

Oto mapowanie pomagające w ich zrozumieniu:
 
|Usługa Batch AI|  Usługa Azure Machine Learning|
|:--:|:---:|
|Obszar roboczy|Obszar roboczy|
|Klaster|   Obliczenia typu `AmlCompute`|
|Serwery plików|Magazyny danych|
|Eksperymenty|Eksperymenty|
|Stanowiska|Przebiegi (dozwolone zagnieżdżone przebiegi)|
 
Oto inny widok tej samej tabeli, który pomoże lepiej zwizualizować te informacje:
 
**Hierarchia w usłudze Batch AI**
![Diagram przepływu](./media/overview-what-happened-batch-ai/batchai-heirarchy.png) 
 
**Hierarchia w usłudze Azure Machine Learning Service**
![Diagram przepływu](./media/overview-what-happened-batch-ai/azure-machine-learning-service-heirarchy.png) 

### <a name="platform-capabilities"></a>Możliwości platformy
Usługa Azure Machine Learning Service zapewnia świetny zestaw nowych funkcji, w tym kompleksowy stos trenowanie->wdrożenie, którego można używać na potrzeby wdrażania sztucznej inteligencji bez konieczności zarządzania zasobami platformy Azure. Ta tabela zawiera porównanie obsługi funkcji do trenowania w obu usługach.

|Cecha|Usługa Batch AI|Usługa Azure Machine Learning|
|-------|:-------:|:-------:|
|Wybór rozmiaru maszyny wirtualnej |CPU/GPU    |CPU/GPU. Obsługuje również układ FPGA na potrzeby wnioskowania|
|Klaster z obsługą AI (sterowniki, Docker itp.)|  Yes |Yes|
|Przygotowanie węzła| Yes|    Nie|
|Wybór rodziny systemów operacyjnych   |Częściowo    |Nie|
|Maszyny wirtualne dedykowane i o niskim priorytecie  |Yes    |Yes|
|Skalowanie automatyczne   |Yes    |Tak (domyślnie)|
|Czas oczekiwania na skalowanie automatyczne  |Nie |Yes|
|Protokół SSH    |Yes|   Yes|
|Instalowanie na poziomie klastra |Tak (udziały plików, obiekty blob, system plików NFS, niestandardowe)   |Tak (instalowanie lub pobieranie magazynu danych)|
|Trenowanie rozproszone|  Yes |Yes|
|Tryb wykonywania zadań|    Maszyna wirtualna lub kontener|    Kontener|
|Niestandardowy obraz kontenera|    Yes |Yes|
|Dowolny zestaw narzędzi    |Yes    |Tak (uruchamianie skryptów języka Python)|
|Przygotowywanie zadań|    Yes |Jeszcze nie|
|Instalowanie poziomu zadania |Tak (udziały plików, obiekty blob, system plików NFS, niestandardowe)   |Tak (udziały plików, obiekty blob)|
|Monitorowanie zadań     |Za pomocą metody GetJob|    Za pomocą historii uruchamiania (bogatsze informacje, niestandardowe środowisko uruchomieniowe umożliwiające wypychanie dodatkowych metryk)|
|Pobieranie dzienników zadań i plików/modeli |   Za pomocą metody ListFiles i interfejsów API magazynu  |Za pomocą usługi artefaktu|
 |Obsługa narzędzia Tensorboard   |Nie|    Yes|
|Limity przydziałów na poziomie rodziny maszyn wirtualnych |Yes    |Tak (wcześniejsza pojemność jest przenoszona)|
 
Oprócz funkcji podanych w powyższej tabeli, w usłudze Azure Machine Learning Service istnieją funkcje, które tradycyjnie nie były obsługiwane w usłudze Batch AI.

|Cecha|Usługa Batch AI|Usługa Azure Machine Learning|
|-------|:-------:|:-------:|
|Przygotowywanie środowiska    |Nie |Tak (przygotowywanie i przekazywanie do usługi ACR za pomocą menedżera Conda)|
|Strojenie hiperparametryczne  |Nie|    Yes|
|Zarządzanie modelami   |Nie |Yes|
|Operacjonalizacja/wdrażanie| Nie  |Za pomocą usług AKS i ACI|
|Przygotowywanie danych   |Nie |Yes|
|Docelowe obiekty obliczeniowe    |Maszyny wirtualne platformy Azure  |Środowisko lokalne, usługa Batch AI (jako AmlCompute), usługa Databricks, usługa HDInsight|
|Zautomatyzowane uczenie maszynowe |Nie|    Yes|
|Potoki  |Nie |Yes|
|Wsadowe generowanie wyników  |Yes    |Yes|
|Obsługa portalu/interfejsu wiersza polecenia|    Yes |Yes|


### <a name="programming-interfaces"></a>Interfejsy programowania

Ta tabela przedstawia różne interfejsy programowania dostępne dla każdej usługi.
    
|Cecha|Usługa Batch AI|Usługa Azure Machine Learning|
|-------|:-------:|:-------:|
|SDK    |Java, C#, Python, Nodejs   |Python (zarówno oparty na konfiguracji uruchamiania, jak i na narzędziu do szacowania dla typowych struktur)|
|Interfejs wiersza polecenia    |Yes    |Jeszcze nie|
|Azure Portal   |Yes    |Tak (z wyjątkiem przesyłania zadań)|
|Interfejs API REST   |Yes    |Tak, ale rozproszony na wiele mikrousług|




## <a name="why-migrate"></a>Dlaczego warto przeprowadzić migrację?

Uaktualnianie usługi Batch AI w wersji zapoznawczej do ogólnie dostępnej usługi Azure Machine Learning Service zapewnia lepsze środowisko dzięki korzystaniu z łatwiejszych w użyciu pojęć, takich jak narzędzia do szacowania i magazyny danych. Gwarantuje to również podleganie umowom SLA oraz pomoc techniczną na poziomie ogólnie dostępnych usług platformy Azure.

W usłudze Azure Machine Learning Service wprowadzono także nowe funkcje, np. zautomatyzowane uczenie maszynowe, strojenie hiperparametryczne i potoki uczenia maszynowego, które są przydatne w większości obciążeń AI o dużej skali. Możliwość operacjonalizowania wytrenowanego modelu bez przełączania na oddzielną usługę pomaga ukończyć pętlę nauki o danych od przygotowywania danych (za pomocą zestawu SDK przygotowywania danych) aż po operacjonalizację i monitorowanie modelu.

<a name="migrate"></a>

## <a name="how-do-i-migrate"></a>Jak przeprowadzić migrację?

Przed wykonaniem kroków podanych w tym przewodniku migracji, które pomagają mapować polecenia między tymi dwiema usługami, zalecamy zapoznanie się z usługą Azure Machine Learning Service, korzystając z jej [dokumentacji](../machine-learning/service/overview-what-is-azure-ml.md), w tym [samouczka w języku Python](../machine-learning/service/tutorial-train-models-with-aml.md).

Aby uniknąć przerw w działaniu aplikacji i móc korzystać z najnowszych funkcji, przed 31 marca 2019 r. wykonaj następujące kroki:

1. Utwórz obszar roboczy usługi Azure Machine Learning i rozpocznij pracę:
    + [Szybki start oparty na języku Python](../machine-learning/service/quickstart-create-workspace-with-python.md)
    + [Szybki start oparty na platformie Azure](../machine-learning/service/quickstart-get-started.md)

1. Skonfiguruj [środowisko obliczeniowe usługi Azure Machine Learning](../machine-learning/service/how-to-set-up-training-targets.md#amlcompute) do trenowania modelu.

1. Zaktualizuj swoje skrypty pod kątem korzystania ze środowiska obliczeniowego usługi Azure Machine Learning.


### <a name="sdk-migration"></a>Migracja zestawu SDK

Obecnie obsługa zestawu SDK w usłudze Azure Machine Learning Service odbywa się za pomocą kilku zestawów SDK języka Python. Główny zestaw SDK jest aktualizowany mniej więcej co dwa tygodnie i można go zainstalować z repozytorium PyPi przy użyciu następującego polecenia:

```python
pip install --upgrade azureml-sdk[notebooks]
```

Skonfiguruj środowisko i zainstaluj zestaw SDK przy użyciu tych [instrukcji przewodnika Szybki start](../machine-learning/service/quickstart-create-workspace-with-python.md#install-the-sdk)

Po otwarciu notesu Jupyter z jądrem wskazującym odpowiednie środowisko Conda polecenia w tych dwóch usługach są mapowane w następujący sposób:


#### <a name="create-a-workspace"></a>Tworzenie obszaru roboczego
Pojęcie inicjowania obszaru roboczego przy użyciu pliku configuration.json w usłudze Batch AI jest podobne do użycia pliku konfiguracji w usłudze Azure ML.

W usłudze **Batch AI** wykonywano to w następujący sposób:

```python
sys.path.append('../../..')
import utilities as utils

cfg = utils.config.Configuration('../../configuration.json')
client = utils.config.create_batchai_client(cfg)

utils.config.create_resource_group(cfg)
_ = client.workspaces.create(cfg.resource_group, cfg.workspace, cfg.location).result()
```

W usłudze**Azure Machine Learning Service** wypróbuj:

```python
from azureml.core.workspace import Workspace

ws = Workspace.from_config()
print('Workspace name: ' + ws.name, 
      'Azure region: ' + ws.location, 
      'Subscription id: ' + ws.subscription_id, 
      'Resource group: ' + ws.resource_group, sep = '\n')
```

Ponadto można również utworzyć obszar roboczy bezpośrednio, określając następujące parametry konfiguracji

```python
from azureml.core import Workspace
# Create the workspace using the specified parameters
ws = Workspace.create(name = workspace_name,
                      subscription_id = subscription_id,
                      resource_group = resource_group, 
                      location = workspace_region,
                      create_resource_group = True,
                      exist_ok = True)
ws.get_details()

# write the details of the workspace to a configuration file to the notebook library
ws.write_config()
```

Więcej informacji na temat klasy obszaru roboczego usługi AML znajduje się w [dokumentacji referencyjnej zestawu SDK](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py).


#### <a name="create-a-compute-cluster"></a>Tworzenie klastra obliczeniowego
Usługa Azure Machine Learning obsługuje wiele docelowych obiektów obliczeniowych, z których część jest zarządzana przez usługę, a inne mogą być dołączone do obszaru roboczego (np. klaster usługi HDInsight lub zdalna maszyna wirtualna). Dowiedz się więcej o różnych [docelowych obiektach obliczeniowych](../machine-learning/service/how-to-set-up-training-targets.md). Pojęcie tworzenia klastra obliczeniowego usługi Batch AI jest mapowane na tworzenie klastra AmlCompute w usłudze Azure ML. Podczas tworzenia klastra AmlCompute jest podawana konfiguracja obliczeń, podobnie do sposobu przekazywania parametrów w usłudze Batch AI. Zwróć uwagę, że skalowanie automatyczne jest domyślnie włączone w klastrze AmlCompute, podczas gdy jest domyślnie wyłączone w usłudze Batch AI.

W usłudze **Batch AI** wykonywano to w następujący sposób:

```python
nodes_count = 2
cluster_name = 'nc6'

parameters = models.ClusterCreateParameters(
    vm_size='STANDARD_NC6',
    scale_settings=models.ScaleSettings(
        manual=models.ManualScaleSettings(target_node_count=nodes_count)
    ),
    user_account_settings=models.UserAccountSettings(
        admin_user_name=cfg.admin,
        admin_user_password=cfg.admin_password or None,
        admin_user_ssh_public_key=cfg.admin_ssh_key or None,
    )
)
_ = client.clusters.create(cfg.resource_group, cfg.workspace, cluster_name, parameters).result()
```

W usłudze **Azure Machine Learning Service** wypróbuj:

```python
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

# Choose a name for your CPU cluster
gpu_cluster_name = "nc6"

# Verify that cluster does not exist already
try:
    gpu_cluster = ComputeTarget(workspace=ws, name=gpu_cluster_name)
    print('Found existing cluster, use it.')
except ComputeTargetException:
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_NC6',
                                                           vm_priority='lowpriority',
                                                           min_nodes=1,
                                                           max_nodes=2,
                                                           idle_seconds_before_scaledown='300',
                                                           vnet_resourcegroup_name='<my-resource-group>',
                                                           vnet_name='<my-vnet-name>',
                                                           subnet_name='<my-subnet-name>')
    gpu_cluster = ComputeTarget.create(ws, gpu_cluster_name, compute_config)

gpu_cluster.wait_for_completion(show_output=True)
```

Więcej informacji na temat klasy AMLCompute znajduje się w [dokumentacji referencyjnej zestawu SDK](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py). Należy pamiętać, że w powyższej konfiguracji tylko właściwości vm_size i max_nodes są obowiązkowe, a pozostałe właściwości, takie jak vnet, są przeznaczone tylko dla konfiguracji zaawansowanej klastra.


#### <a name="monitoring-status-of-your-cluster"></a>Monitorowanie stanu klastra
Jest to łatwiejsze w usłudze Azure ML, jak pokazano poniżej.

W usłudze **Batch AI** wykonywano to w następujący sposób:

```python
cluster = client.clusters.get(cfg.resource_group, cfg.workspace, cluster_name)
utils.cluster.print_cluster_status(cluster)
```

W usłudze **Azure Machine Learning Service** wypróbuj:

```python
gpu_cluster.get_status().serialize()
```

#### <a name="getting-reference-to-a-storage-account"></a>Pobieranie odwołania do konta magazynu
Pojęcie magazynu danych, takiego jak obiekt blob, zostało uproszczone w usłudze Azure ML za pomocą obiektu DataStore. Domyślnie obszar roboczy usługi Azure ML tworzy konto magazynu, ale możesz również dołączyć własny magazyn w ramach tworzenia obszaru roboczego. 

W usłudze **Batch AI** wykonywano to w następujący sposób:

```python
azure_blob_container_name = 'batchaisample'
blob_service = BlockBlobService(cfg.storage_account_name, cfg.storage_account_key)
blob_service.create_container(azure_blob_container_name, fail_on_exist=False)
```

W usłudze **Azure Machine Learning Service** wypróbuj:

```python
ds = ws.get_default_datastore()
print(ds.datastore_type, ds.account_name, ds.container_name)
```

Dowiedz się więcej na temat rejestrowania dodatkowych kont magazynu lub pobierania odwołania do innego zarejestrowanego magazynu danych z [dokumentacji usługi Azure ML](../machine-learning/service/how-to-access-data.md#access).


#### <a name="downloading-and-uploading-data"></a>Pobieranie i przekazywanie danych 
Przy użyciu którejkolwiek z tych usług możesz łatwo przekazać dane na konto magazynu za pomocą odwołania do magazynu danych przedstawionego powyżej. Na potrzeby usługi Batch AI również wdrożymy skrypt treningowy w ramach udziału plików i dodatkowo zobaczysz, jak można go określić w ramach konfiguracji zadania w przypadku usługi Azure ML.

W usłudze **Batch AI** wykonywano to w następujący sposób:

```python
mnist_dataset_directory = 'mnist_dataset'
utils.dataset.download_and_upload_mnist_dataset_to_blob(
    blob_service, azure_blob_container_name, mnist_dataset_directory)

script_directory = 'tensorflow_samples'
script_to_deploy = 'mnist_replica.py'

blob_service.create_blob_from_path(azure_blob_container_name,
                                   script_directory + '/' + script_to_deploy, 
                                   script_to_deploy)
```


W usłudze **Azure Machine Learning Service** wypróbuj:

```python
import os
import urllib
os.makedirs('./data', exist_ok=True)
download_url = 'https://s3.amazonaws.com/img-datasets/mnist.npz'
urllib.request.urlretrieve(download_url, filename='data/mnist.npz')

ds.upload(src_dir='data', target_path='mnist_dataset', overwrite=True, show_progress=True)

path_on_datastore = ' mnist_dataset/mnist.npz' ds_data = ds.path(path_on_datastore) print(ds_data)
```

#### <a name="create-an-experiment"></a>Tworzenie eksperymentu
Jak wspomniano powyżej, w usłudze Azure ML istnieje pojęcie eksperymentu podobnego do tego w usłudze Batch AI. Każdy eksperyment może mieć pojedyncze przebiegi, analogicznie do zadań w usłudze Batch AI. Usługa Azure ML umożliwia również zastosowanie hierarchii w ramach każdego przebiegu nadrzędnego na potrzeby pojedynczych przebiegów podrzędnych.

W usłudze **Batch AI** wykonywano to w następujący sposób:

```python
experiment_name = 'tensorflow_experiment'
experiment = client.experiments.create(cfg.resource_group, cfg.workspace, experiment_name).result()
```

W usłudze **Azure Machine Learning Service** wypróbuj:

```python
from azureml.core import Experiment

experiment_name = 'tensorflow_experiment'
experiment = Experiment(ws, name=experiment_name)
```


#### <a name="submit-a-job"></a>Przesyłanie zadania
Po utworzeniu eksperymentu istnieje kilka różnych sposobów na przesłanie przebiegu. W tym przykładzie spróbujemy utworzyć model uczenia głębokiego przy użyciu biblioteki TensorFlow. W tym celu użyjemy narzędzia do szacowania usługi Azure ML. [Narzędzie do szacowania](../machine-learning/service/how-to-train-ml-models.md) to po prostu funkcja otoki dla podstawowej konfiguracji przebiegu, która ułatwia przesyłanie przebiegów i jest obecnie obsługiwana tylko dla bibliotek PyTorch i TensorFlow. Dzięki pojęciu magazynów danych zobaczysz również, jakie proste jest określanie ścieżek instalacji. 

W usłudze **Batch AI** wykonywano to w następujący sposób:

```python
azure_file_share = 'afs'
azure_blob = 'bfs'
args_fmt = '--job_name={0} --num_gpus=1 --train_steps 10000 --checkpoint_dir=$AZ_BATCHAI_OUTPUT_MODEL --log_dir=$AZ_BATCHAI_OUTPUT_TENSORBOARD --data_dir=$AZ_BATCHAI_INPUT_DATASET --ps_hosts=$AZ_BATCHAI_PS_HOSTS --worker_hosts=$AZ_BATCHAI_WORKER_HOSTS --task_index=$AZ_BATCHAI_TASK_INDEX'

parameters = models.JobCreateParameters(
     cluster=models.ResourceId(id=cluster.id),
     node_count=2,
     input_directories=[
        models.InputDirectory(
            id='SCRIPT',
            path='$AZ_BATCHAI_JOB_MOUNT_ROOT/{0}/{1}'.format(azure_blob, script_directory)),
        models.InputDirectory(
            id='DATASET',
            path='$AZ_BATCHAI_JOB_MOUNT_ROOT/{0}/{1}'.format(azure_blob, mnist_dataset_directory))],
     std_out_err_path_prefix='$AZ_BATCHAI_JOB_MOUNT_ROOT/{0}'.format(azure_file_share),
     output_directories=[
        models.OutputDirectory(
            id='MODEL',
            path_prefix='$AZ_BATCHAI_JOB_MOUNT_ROOT/{0}'.format(azure_file_share),
            path_suffix='Models'),
        models.OutputDirectory(
            id='TENSORBOARD',
            path_prefix='$AZ_BATCHAI_JOB_MOUNT_ROOT/{0}'.format(azure_file_share),
            path_suffix='Logs')
     ],
     mount_volumes=models.MountVolumes(
            azure_file_shares=[
                models.AzureFileShareReference(
                    account_name=cfg.storage_account_name,
                    credentials=models.AzureStorageCredentialsInfo(
                        account_key=cfg.storage_account_key),
                    azure_file_url='https://{0}.file.core.windows.net/{1}'.format(
                        cfg.storage_account_name, azure_file_share_name),
                    relative_mount_path=azure_file_share)
            ],
            azure_blob_file_systems=[
                models.AzureBlobFileSystemReference(
                    account_name=cfg.storage_account_name,
                    credentials=models.AzureStorageCredentialsInfo(
                        account_key=cfg.storage_account_key),
                    container_name=azure_blob_container_name,
                    relative_mount_path=azure_blob)
            ]
        ),
     container_settings=models.ContainerSettings(
         image_source_registry=models.ImageSourceRegistry(image='tensorflow/tensorflow:1.8.0-gpu')),
     tensor_flow_settings=models.TensorFlowSettings(
         parameter_server_count=1,
         worker_count=nodes_count,
         python_script_file_path='$AZ_BATCHAI_INPUT_SCRIPT/'+ script_to_deploy,
         master_command_line_args=args_fmt.format('worker'),
         worker_command_line_args=args_fmt.format('worker'),
         parameter_server_command_line_args=args_fmt.format('ps'),
     )
)
```

Samo przesyłanie zadania w usłudze Batch AI odbywa się za pomocą tworzenia funkcji.

```python
job_name = datetime.utcnow().strftime('tf_%m_%d_%Y_%H%M%S')
job = client.jobs.create(cfg.resource_group, cfg.workspace, experiment_name, job_name, parameters).result()
print('Created Job {0} in Experiment {1}'.format(job.name, experiment.name))
```

Pełne informacje na temat tego fragmentu kodu trenowania (w tym plik mnist_replica.py, który został przekazany do powyższego udziału plików) można znaleźć w [repozytorium GitHub przykładowego notesu usługi Batch AI](https://github.com/Azure/BatchAI/tree/2238607d5a028a0c5e037168aefca7d7bb165d5c/recipes/TensorFlow/TensorFlow-GPU-Distributed).

W usłudze **Azure Machine Learning Service** wypróbuj:

```python
from azureml.train.dnn import TensorFlow

script_params={
    '--num_gpus': 1,
    '--train_steps': 500,
    '--input_data': ds_data.as_mount()

}

estimator = TensorFlow(source_directory=project_folder,
                       compute_target=gpu_cluster,
                       script_params=script_params,
                       entry_script='tf_mnist_replica.py',
                       node_count=2,
                       worker_count=2,
                       parameter_server_count=1,   
                       distributed_backend='ps',
                       use_gpu=True)
```

Pełne informacje na temat tego fragmentu kodu trenowania (w tym plik mnist_replica.py) można znaleźć w [repozytorium GitHub przykładowego notesu usługi Azure ML](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/distributed-tensorflow-with-parameter-server). Sam magazyn danych można instalować na pojedynczych węzłach lub dane treningowe można pobrać w samym węźle. Szczegółowe informacje na temat odwołań do magazynu danych w narzędziu do szacowania znajdują się w [dokumentacji usługi Azure ML](../machine-learning/service/how-to-access-data.md#access). 

Przesyłanie przebiegu w usłudze Azure ML za pośrednictwem funkcji przesyłania.

```python
run = experiment.submit(estimator)
print(run)
```

Innym sposobem na określanie parametrów przebiegu jest użycie konfiguracji przebiegu — szczególnie przydatnej podczas definiowania niestandardowego środowiska treningowego. Więcej szczegółów można znaleźć w tym [przykładowym notesie AmlCompute](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-on-amlcompute/train-on-amlcompute.ipynb). 

#### <a name="monitor-your-run"></a>Monitorowanie przebiegu
Po przesłaniu przebiegu można poczekać na jego ukończenie lub monitorować przebieg w usłudze Azure ML za pomocą świetnych widżetów Jupyter, które można wywoływać bezpośrednio z poziomu kodu. Można również uzyskać kontekst dowolnego poprzedniego przebiegu, wykonując pętlę względem różnych eksperymentów w obszarze roboczym i pojedynczych przebiegów w ramach każdego eksperymentu.

W usłudze **Batch AI** wykonywano to w następujący sposób:

```python
utils.job.wait_for_job_completion(client, cfg.resource_group, cfg.workspace, 
                                  experiment_name, job_name, cluster_name, 'stdouterr', 'stdout-wk-0.txt')

files = client.jobs.list_output_files(cfg.resource_group, cfg.workspace, experiment_name, job_name,
                                      models.JobsListOutputFilesOptions(outputdirectoryid='stdouterr')) 
for f in list(files):
    print(f.name, f.download_url or 'directory')
```


W usłudze **Azure Machine Learning Service** wypróbuj:

```python
run.wait_for_completion(show_output=True)

from azureml.widgets import RunDetails
RunDetails(run).show()
```

Poniżej znajduje się migawka pokazująca sposób ładowania widżetu w notesie na potrzeby wyszukiwania dzienników w czasie rzeczywistym: ![Diagram monitorowania widżetu](./media/overview-what-happened-batch-ai/monitor.png)



#### <a name="editing-a-cluster"></a>Edytowanie klastra
Usuwanie klastra jest bardzo proste. Ponadto usługa Azure ML umożliwia również aktualizowanie klastra z poziomu notesu w przypadku, gdy chcesz przeprowadzić skalowanie do większej liczby węzłów lub zwiększyć czas bezczynności przed skalowaniem klastra w dół. Nie można zmienić rozmiaru maszyny wirtualnej samego klastra, ponieważ wymaga to nowego, efektywnego wdrożenia w zapleczu.

W usłudze **Batch AI** wykonywano to w następujący sposób:
```python
_ = client.clusters.delete(cfg.resource_group, cfg.workspace, cluster_name)
```

W usłudze **Azure Machine Learning Service** wypróbuj:

```python
gpu_cluster.delete()

gpu_cluster.update(min_nodes=2, max_nodes=4, idle_seconds_before_scaledown=600)
```

## <a name="support"></a>Pomoc techniczna

Usługa Batch AI zostanie wycofana 31 marca, a już teraz rejestrowanie nowych subskrypcji jest blokowane, chyba że zostanie ona umieszczona na liście dozwolonych przez zgłoszenie wyjątku za pośrednictwem pomocy technicznej.  Jeśli masz jakiekolwiek pytania lub chcesz przesłać opinię na temat migracji do usługi Azure Machine Learning Service, wyślij wiadomość na adres [Azure Batch AI Training Preview](mailto:AzureBatchAITrainingPreview@service.microsoft.com).

Usługa Azure Machine Learning jest usługą ogólnie dostępną. Oznacza to, że towarzyszy jej zatwierdzona umowa SLA i różne plany pomocy technicznej do wyboru.

Cennik korzystania z infrastruktury platformy Azure zarówno za pośrednictwem usługi Batch AI, jak i za pośrednictwem usługi Azure Machine Learning Service nie powinien się różnić, ponieważ w obu przypadkach opłaty będą naliczane wyłącznie za zasoby obliczeniowe. Aby uzyskać więcej informacji, zobacz [kalkulator cen](https://azure.microsoft.com/pricing/details/machine-learning-service/).

Wyświetl dostępność regionalną obu usług w witrynie [Azure Portal](https://azure.microsoft.com/global-infrastructure/services/?products=batch-ai,machine-learning-service&regions=all).


## <a name="next-steps"></a>Następne kroki

+ Przeczytaj artykuł [Przegląd usługi Azure Machine Learning](../machine-learning/service/overview-what-is-azure-ml.md).

+ [Skonfiguruj docelowy obiekt obliczeniowy do trenowania modelu](../machine-learning/service/how-to-set-up-training-targets.md) za pomocą usługi Azure Machine Learning.

+ Zapoznaj się z [harmonogramem działania dla platformy Azure](https://azure.microsoft.com/updates/), aby dowiedzieć się o innych aktualizacjach usługi Azure.
