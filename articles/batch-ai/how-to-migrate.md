---
title: Migrowanie z usługi Batch AI do usługi Azure Machine Learning
description: Dowiedz się, jak przeprowadzić migrację do usługi Azure Machine Learning w przypadku AMLcompute oraz sposób mapowania kodu do kodu w usłudze Azure Machine Learning.
ms.service: batch-ai
services: batch-ai
ms.topic: overview
ms.author: jmartens
author: j-martens
ms.date: 2/28/2019
ms.openlocfilehash: e495ed06c640601c0500d14b42070a264fd687a9
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/18/2019
ms.locfileid: "57862118"
---
# <a name="migrating-from-batch-ai-to-azure-machine-learning-service"></a>Migrowanie z usługi Batch AI do usługi Azure Machine Learning

**Usługa Azure Batch AI zostanie wycofana w marcu.** Możliwości usługi Batch AI związane z trenowaniem i ocenianiem na dużą skalę są teraz dostępne w [usłudze Azure Machine Learning](../machine-learning/service/overview-what-is-azure-ml.md), która stanie się ogólnodostępna 4 grudnia 2018 r.

Wśród wielu możliwości dotyczących uczenia maszynowego usługa Azure Machine Learning obejmuje chmurowy, zarządzany docelowy obiekt obliczeniowy do trenowania, wdrażania i oceniania modeli uczenia maszynowego. Ten docelowy obiekt obliczeniowy jest nazywany [środowiskiem obliczeniowym usługi Azure Machine Learning](../machine-learning/service/how-to-set-up-training-targets.md#amlcompute). Rozpocznij migrację i już dziś przy użyciu. Interakcja z usługą Azure Machine Learning jest możliwa za pomocą wbudowanych zestawów [Python SDK](../machine-learning/service/quickstart-create-workspace-with-python.md), interfejsu wiersza polecenia i [witryny Azure portal](../machine-learning/service/quickstart-get-started.md).

Uaktualnianie z usługi Batch AI (wersja zapoznawcza) do usługi GA'ed Azure Machine Learning zapewnia lepsze środowisko za pomocą pojęcia, które są łatwiejsze w obsłudze, takie jak aplikacjom i magazynów danych. Gwarantuje to również podleganie umowom SLA oraz pomoc techniczną na poziomie ogólnie dostępnych usług platformy Azure.

Usługa Azure Machine Learning, którą usługa udostępniono też nowe funkcje takie jak zautomatyzowane uczenia maszynowego do strojenia hiperparametrycznego i potoków uczenia Maszynowego, które są przydatne w najbardziej na dużą skalę obciążeń sztucznej Inteligencji. Możliwość wdrażania uczonego modelu bez przełączania do oddzielnej usługi pomaga Wykonaj pętlę do nauki o danych, od przygotowania danych (za pomocą zestawu SDK usługi Data Prep) aż do operacjonalizacji i monitorowania modelu.

## <a name="start-migrating"></a>Rozpocznij migrację
Aby uniknąć przerw w działaniu aplikacji i móc korzystać z najnowszych funkcji, przed 31 marca 2019 r. wykonaj następujące kroki:

1. Utwórz obszar roboczy usługi Azure Machine Learning i rozpocznij pracę:
    + [Szybki start oparty na języku Python](../machine-learning/service/quickstart-create-workspace-with-python.md)
    + [Szybki start oparty na platformie Azure](../machine-learning/service/quickstart-get-started.md)

1. Zainstaluj [usługi Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) i [SDK przeznaczonego do przygotowania danych](https://docs.microsoft.com/python/api/overview/azure/dataprep/intro?view=azure-dataprep-py#install). 

1. Skonfiguruj [środowisko obliczeniowe usługi Azure Machine Learning](../machine-learning/service/how-to-set-up-training-targets.md#amlcompute) do trenowania modelu.

1. Zaktualizuj swoje skrypty pod kątem korzystania ze środowiska obliczeniowego usługi Azure Machine Learning. W poniższych sekcjach opisano, jak często spotykane kodu używasz dla usługi Batch AI map kodu dla usługi Azure Machine Learning. 


## <a name="create-workspaces"></a>Tworzenie obszarów roboczych
Podobnie koncepcji inicjowanie obszar roboczy w usłudze Azure Batch AI przy użyciu configuration.json mapuje przy użyciu pliku konfiguracji w usłudze Azure Machine Learning.

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

Dowiedz się więcej o klasie obszaru roboczego usługi Azure Machine Learning w [Dokumentacja referencyjna zestawu SDK](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py).


## <a name="create-compute-clusters"></a>Tworzenie klastrów obliczeniowych
Usługa Azure Machine Learning obsługuje wiele docelowych obiektów obliczeniowych, z których część jest zarządzana przez usługę, a inne mogą być dołączone do obszaru roboczego (np. klaster usługi HDInsight lub zdalna maszyna wirtualna). Dowiedz się więcej o różnych [docelowych obiektach obliczeniowych](../machine-learning/service/how-to-set-up-training-targets.md). Pojęcie tworzenia usługi Azure Batch AI obliczeń map klastra do tworzenia klastra usługi AmlCompute w usłudze Azure Machine Learning. Trwa tworzenie Amlcompute w konfiguracji obliczeniowej, podobnie jak parametry są przekazywane w usłudze Azure Batch AI. To jedno, należy pamiętać, że skalowanie automatyczne jest domyślnie włączona w klastrze AmlCompute należy go jest domyślnie wyłączona w usłudze Azure Batch AI.

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

## <a name="monitor-status-of-your-cluster"></a>Monitoruj stan klastra
Jest to bardziej bezpośrednie w usłudze Azure Machine Learning, jak widać poniżej.

W usłudze **Batch AI** wykonywano to w następujący sposób:

```python
cluster = client.clusters.get(cfg.resource_group, cfg.workspace, cluster_name)
utils.cluster.print_cluster_status(cluster)
```

W usłudze **Azure Machine Learning Service** wypróbuj:

```python
gpu_cluster.get_status().serialize()
```

## <a name="get-reference-to-a-storage-account"></a>Pobierz odwołanie do konta magazynu
Pojęcie magazyn danych, takich jak obiekt blob, pobiera uproszczone w usłudze Azure Machine Learning, za pomocą obiektu magazynu danych. Domyślnie obszaru roboczego usługi Azure Machine Learning tworzy konto magazynu, ale można również dołączyć własny magazyn jako część tworzenia obszaru roboczego. 

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

Dowiedz się więcej na temat rejestrowania dodatkowych kont magazynu lub pobieranie odwołania do innego zarejestrowanego magazynu danych w [dokumentacji usługi Azure Machine Learning](../machine-learning/service/how-to-access-data.md#access).


## <a name="download-and-upload-data"></a>Pobieranie i przekazywanie danych 
Przy użyciu którejkolwiek z tych usług możesz łatwo przekazać dane na konto magazynu za pomocą odwołania do magazynu danych przedstawionego powyżej. Dla usługi Azure Batch AI firma Microsoft również wdrożyć skrypt szkoleniowy w ramach udziału plików, mimo że zobaczysz, jak możesz je określić w ramach konfiguracji zadania w przypadku usługi Azure Machine Learning.

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

## <a name="create-experiments"></a>Tworzenie eksperymentów
Jak wspomniano powyżej usługi Azure Machine Learning ma koncepcji eksperymentu podobne do usługi Azure Batch AI. Każdy eksperymentu następnie może mieć poszczególnych przebiegów, podobnie jak mamy zadania usługi Azure Batch AI. Usługa Azure Machine Learning umożliwia również hierarchii pod każdym elementem nadrzędnym uruchamianie uruchomień poszczególnych podrzędnych.

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


## <a name="submit-jobs"></a>Przesyłanie zadań
Po utworzeniu eksperymentu istnieje kilka różnych sposobów na przesłanie przebiegu. W tym przykładzie firma Microsoft próbuje utworzyć model uczenia głębokiego przy użyciu TensorFlow i będzie używać usługi Azure Machine Learning narzędzie do szacowania, aby to zrobić. [Narzędzie do szacowania](../machine-learning/service/how-to-train-ml-models.md) to po prostu funkcja otoki dla podstawowej konfiguracji przebiegu, która ułatwia przesyłanie przebiegów i jest obecnie obsługiwana tylko dla bibliotek PyTorch i TensorFlow. Dzięki pojęciu magazynów danych zobaczysz również, jakie proste jest określanie ścieżek instalacji. 

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

Przesyłanie zadania w usłudze Azure Batch AI jest przy użyciu funkcji tworzenia.

```python
job_name = datetime.utcnow().strftime('tf_%m_%d_%Y_%H%M%S')
job = client.jobs.create(cfg.resource_group, cfg.workspace, experiment_name, job_name, parameters).result()
print('Created Job {0} in Experiment {1}'.format(job.name, experiment.name))
```

Pełne informacje na ten fragment kodu szkolenia (w tym pliku mnist_replica.py, który miał przekazany do udziału plików powyżej) można znaleźć w [repozytorium github notesu przykładowej usługi Azure Batch AI](https://github.com/Azure/BatchAI/tree/2238607d5a028a0c5e037168aefca7d7bb165d5c/recipes/TensorFlow/TensorFlow-GPU-Distributed).

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

Pełne informacje na ten fragment kodu szkolenia (w tym pliku tf_mnist_replica.py) można znaleźć w [repozytorium github notesu przykładowej usługi Azure Machine Learning](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/distributed-tensorflow-with-parameter-server). Sam magazyn danych można instalować na pojedynczych węzłach lub dane treningowe można pobrać w samym węźle. Szczegółowe informacje na temat odwołuje się do magazynu danych w Twojej narzędzie do szacowania znajduje się w [dokumentacji usługi Azure Machine Learning](../machine-learning/service/how-to-access-data.md#access). 

Przesyłanie wykonywania w usłudze Azure Machine Learning service to za pośrednictwem funkcji Prześlij.

```python
run = experiment.submit(estimator)
print(run)
```

Innym sposobem na określanie parametrów przebiegu jest użycie konfiguracji przebiegu — szczególnie przydatnej podczas definiowania niestandardowego środowiska treningowego. Więcej szczegółów można znaleźć w tym [przykładowym notesie AmlCompute](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-on-amlcompute/train-on-amlcompute.ipynb). 

## <a name="monitor-runs"></a>Monitorowanie uruchomień
Po przesłaniu przebiegu można poczekać na jego ukończenie lub je monitorować w usłudze Azure Machine Learning przy użyciu ładnie elementy widget Jupyter, które można wywołać bezpośrednio w kodzie. Można również uzyskać kontekst dowolnego poprzedniego przebiegu, wykonując pętlę względem różnych eksperymentów w obszarze roboczym i pojedynczych przebiegów w ramach każdego eksperymentu.

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



## <a name="edit-clusters"></a>Edytuj klastry
Usuwanie klastra jest bardzo proste. Ponadto usługa Azure Machine Learning umożliwia również aktualizowanie klastra z w notesie, w przypadku, gdy chcesz przeskalujesz ją do większej liczby węzłów lub zwiększyć czas bezczynności przed skalowanie w dół do klastra. Nie można zmienić rozmiaru maszyny wirtualnej samego klastra, ponieważ wymaga to nowego, efektywnego wdrożenia w zapleczu.

W usłudze **Batch AI** wykonywano to w następujący sposób:
```python
_ = client.clusters.delete(cfg.resource_group, cfg.workspace, cluster_name)
```

W usłudze **Azure Machine Learning Service** wypróbuj:

```python
gpu_cluster.delete()

gpu_cluster.update(min_nodes=2, max_nodes=4, idle_seconds_before_scaledown=600)
```

## <a name="get-support"></a>Uzyskiwanie pomocy technicznej

Usługa Batch AI to slated wycofywania na dzień 31 marca i już blokuje nowe subskrypcje zarejestrowanie korzystająca z usługi, chyba że jest na liście dozwolonych przez wywołanie wyjątku przez za pośrednictwem pomocy technicznej.  Jeśli masz jakiekolwiek pytania lub chcesz przesłać opinię na temat migracji do usługi Azure Machine Learning Service, wyślij wiadomość na adres [Azure Batch AI Training Preview](mailto:AzureBatchAITrainingPreview@service.microsoft.com).

Usługa Azure Machine Learning jest usługą ogólnie dostępną. Oznacza to, że towarzyszy jej zatwierdzona umowa SLA i różne plany pomocy technicznej do wyboru.

Cennik usługi przy użyciu infrastruktury platformy Azure za pomocą usługi Azure Batch AI lub za pośrednictwem usługi Azure Machine Learning nie powinno się zmieniać, ponieważ opłaty będą naliczane wyłącznie cena za podstawowe zasoby obliczeniowe w obu przypadkach. Aby uzyskać więcej informacji, zobacz [kalkulator cen](https://azure.microsoft.com/pricing/details/machine-learning-service/).

Wyświetl dostępność regionalną obu usług w witrynie [Azure Portal](https://azure.microsoft.com/global-infrastructure/services/?products=batch-ai,machine-learning-service&regions=all).


## <a name="next-steps"></a>Kolejne kroki

+ Przeczytaj artykuł [Przegląd usługi Azure Machine Learning](../machine-learning/service/overview-what-is-azure-ml.md).

+ [Skonfiguruj docelowy obiekt obliczeniowy do trenowania modelu](../machine-learning/service/how-to-set-up-training-targets.md) za pomocą usługi Azure Machine Learning.

+ Zapoznaj się z [harmonogramem działania dla platformy Azure](https://azure.microsoft.com/updates/), aby dowiedzieć się o innych aktualizacjach usługi Azure.
