---
title: Uruchamianie prognoz partii na dużych zbiorach danych
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak uzyskać wnioski asynchronicznie na duże ilości danych przy użyciu ParallelRunStep w usłudze Azure Machine Learning. ParallelRunStep zapewnia możliwości przetwarzania równoległego po wyjęciu z pudełka i optymalizuje dla wysokiej przepływności, wnioskowania fire-and-forget dla przypadków użycia dużych zbiorów danych.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.reviewer: trbye, jmartens, larryfr, vaidyas
ms.author: vaidyas
author: vaidya-s
ms.date: 01/15/2020
ms.custom: Ignite2019
ms.openlocfilehash: 3d283d1094336b928869aa281b4a640d7a62dd94
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "79477191"
---
# <a name="run-batch-inference-on-large-amounts-of-data-by-using-azure-machine-learning"></a>Uruchamianie wnioskowania wsadowego na dużych ilościach danych przy użyciu usługi Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Dowiedz się, jak przetwarzać duże ilości danych asynchronicznie i równolegle przy użyciu usługi Azure Machine Learning. Funkcja ParallelRunStep opisana w tym miejscu jest w publicznej wersji zapoznawczej. Jest to sposób wysokiej wydajności i wysokiej przepływności do generowania wniosków i przetwarzania danych. Zapewnia możliwości asynchroniczne po wyjęciu z pudełka.

Dzięki ParallelRunStep można łatwo skalować wnioski w trybie offline do dużych klastrów maszyn na terabajtach danych produkcyjnych, co przekłada się na większą produktywność i zoptymalizowane koszty.

W tym artykule dowiesz się o następujących zadaniach:

> * Tworzenie zdalnego zasobu obliczeniowego.
> * Napisz niestandardowy skrypt wnioskowania.
> * Utwórz [potok uczenia maszynowego,](concept-ml-pipelines.md) aby zarejestrować wstępnie przeszkolony model klasyfikacji obrazów na podstawie zestawu danych [MNIST.](https://publicdataset.azurewebsites.net/dataDetail/mnist/) 
> * Użyj modelu do uruchamiania wnioskowania wsadowego na przykładowych obrazów dostępnych na koncie magazynu obiektów Blob platformy Azure. 

## <a name="prerequisites"></a>Wymagania wstępne

* Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz bezpłatne konto. Wypróbuj [bezpłatną lub płatną wersję usługi Azure Machine Learning](https://aka.ms/AMLFree).

* Aby uzyskać przewodnik szybki start, wykonaj [samouczek konfiguracji,](tutorial-1st-experiment-sdk-setup.md) jeśli nie masz jeszcze obszaru roboczego usługi Azure Machine Learning lub maszyny wirtualnej notesu. 

* Aby zarządzać własnym środowiskiem i zależnościami, zobacz [przewodnik infiguracyjnie](how-to-configure-environment.md) własnego środowiska. Uruchom `pip install azureml-sdk[notebooks] azureml-pipeline-core azureml-contrib-pipeline-steps` w środowisku, aby pobrać niezbędne zależności.

## <a name="set-up-machine-learning-resources"></a>Konfigurowanie zasobów uczenia maszynowego

Następujące akcje skonfigurować zasoby, które są potrzebne do uruchomienia potoku wnioskowania partii:

- Utwórz magazyn danych, który wskazuje na kontener obiektu blob, który ma obrazy do wnioskowania.
- Skonfiguruj odwołania do danych jako dane wejściowe i wyjściowe dla kroku potoku wnioskowania partii.
- Skonfiguruj klaster obliczeniowy, aby uruchomić krok wnioskowania partii.

### <a name="create-a-datastore-with-sample-images"></a>Tworzenie magazynu danych z przykładowymi obrazami

Pobierz zestaw oceny MNIST z publicznego `sampledata` kontenera `pipelinedata`obiektów blob na koncie o nazwie . Utwórz magazyn danych `mnist_datastore`o nazwie , który wskazuje ten kontener. W poniższym `register_azure_blob_container`wywołaniu `overwrite` , `True` ustawienie flagi zastąpić każdy magazyn danych, który został utworzony wcześniej o tej nazwie. 

Ten krok można zmienić, aby wskazać kontener obiektów `datastore_name`blob, podając własne wartości dla , `container_name`i `account_name`.

```python
from azureml.core import Datastore
from azureml.core import Workspace

# Load workspace authorization details from config.json
ws = Workspace.from_config()

mnist_blob = Datastore.register_azure_blob_container(ws, 
                      datastore_name="mnist_datastore", 
                      container_name="sampledata", 
                      account_name="pipelinedata",
                      overwrite=True)
```

Następnie należy określić domyślny magazyn danych obszaru roboczego jako wyjściowy magazyn danych. Użyjesz go do wnioskowania danych wyjściowych.

Podczas tworzenia obszaru roboczego [usługi Azure Files](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) i [magazyn](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction) obiektów blob są domyślnie dołączone do obszaru roboczego. Usługa Azure Files jest domyślnym magazynem danych dla obszaru roboczego, ale można również użyć magazynu obiektów Blob jako magazynu danych. Aby uzyskać więcej informacji, zobacz [Opcje magazynu platformy Azure](https://docs.microsoft.com/azure/storage/common/storage-decide-blobs-files-disks).

```python
def_data_store = ws.get_default_datastore()
```

### <a name="configure-data-inputs-and-outputs"></a>Konfigurowanie danych wejściowych i wyjściowych

Teraz musisz skonfigurować dane wejściowe i wyjściowe, w tym:

- Katalog zawierający obrazy wejściowe.
- Katalog, w którym jest przechowywany wstępnie przeszkolony model.
- Katalog zawierający etykiety.
- Katalog danych wyjściowych.

[`Dataset`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py)to klasa do eksplorowania, przekształcania i zarządzania danymi w usłudze Azure Machine Learning. Ta klasa ma [`TabularDataset`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) dwa [`FileDataset`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.filedataset?view=azure-ml-py)typy: i . W tym przykładzie użyjesz `FileDataset` jako dane wejściowe do kroku potoku wnioskowania partii. 

> [!NOTE] 
> `FileDataset`obsługa w przypadku wnioskowania wsadowego jest na razie ograniczona do magazynu obiektów Blob platformy Azure. 

Można również odwoływać się do innych zestawów danych w niestandardowych skrypt wnioskowania. Można go na przykład użyć do uzyskania dostępu do etykiet `Dataset.register` `Dataset.get_by_name`w skrypcie do etykietowania obrazów za pomocą i .

Aby uzyskać więcej informacji na temat zestawów danych usługi Azure Machine Learning, zobacz [Tworzenie i uzyskiwanie dostępu do zestawów danych (wersja zapoznawcza)](https://docs.microsoft.com/azure/machine-learning/how-to-create-register-datasets).

[`PipelineData`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py)obiekty są używane do przesyłania danych pośrednich między etapami potoku. W tym przykładzie można go użyć do wnioskowania wyjść.

```python
from azureml.core.dataset import Dataset

mnist_ds_name = 'mnist_sample_data'

path_on_datastore = mnist_blob.path('mnist/')
input_mnist_ds = Dataset.File.from_files(path=path_on_datastore, validate=False)
registered_mnist_ds = input_mnist_ds.register(ws, mnist_ds_name, create_new_version=True)
named_mnist_ds = registered_mnist_ds.as_named_input(mnist_ds_name)

output_dir = PipelineData(name="inferences", 
                          datastore=def_data_store, 
                          output_path_on_compute="mnist/results")
```

### <a name="set-up-a-compute-target"></a>Konfigurowanie obiektu docelowego obliczeń

W usłudze Azure Machine Learning *obliczanie* (lub *obiekt docelowy obliczeń)* odnosi się do maszyn lub klastrów, które wykonują kroki obliczeniowe w potoku uczenia maszynowego. Uruchom następujący kod, aby utworzyć obiekt docelowy [AmlCompute](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py) oparty na procesorze.

```python
from azureml.core.compute import AmlCompute, ComputeTarget
from azureml.core.compute_target import ComputeTargetException

# choose a name for your cluster
compute_name = os.environ.get("AML_COMPUTE_CLUSTER_NAME", "cpu-cluster")
compute_min_nodes = os.environ.get("AML_COMPUTE_CLUSTER_MIN_NODES", 0)
compute_max_nodes = os.environ.get("AML_COMPUTE_CLUSTER_MAX_NODES", 4)

# This example uses CPU VM. For using GPU VM, set SKU to STANDARD_NC6
vm_size = os.environ.get("AML_COMPUTE_CLUSTER_SKU", "STANDARD_D2_V2")


if compute_name in ws.compute_targets:
    compute_target = ws.compute_targets[compute_name]
    if compute_target and type(compute_target) is AmlCompute:
        print('found compute target. just use it. ' + compute_name)
else:
    print('creating a new compute target...')
    provisioning_config = AmlCompute.provisioning_configuration(vm_size = vm_size,
                                                                min_nodes = compute_min_nodes, 
                                                                max_nodes = compute_max_nodes)

    # create the cluster
    compute_target = ComputeTarget.create(ws, compute_name, provisioning_config)
    
    # can poll for a minimum number of nodes and for a specific timeout. 
    # if no min node count is provided it will use the scale settings for the cluster
    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
    
     # For a more detailed view of current AmlCompute status, use get_status()
    print(compute_target.get_status().serialize())
```

## <a name="prepare-the-model"></a>Przygotowanie modelu

[Pobierz wstępnie przeszkolony model klasyfikacji obrazów,](https://pipelinedata.blob.core.windows.net/mnist-model/mnist-tf.tar.gz)a `models` następnie wyodrębnij go do katalogu.

```python
import os
import tarfile
import urllib.request

model_dir = 'models'
if not os.path.isdir(model_dir):
    os.mkdir(model_dir)

url="https://pipelinedata.blob.core.windows.net/mnist-model/mnist-tf.tar.gz"
response = urllib.request.urlretrieve(url, "model.tar.gz")
tar = tarfile.open("model.tar.gz", "r:gz")
tar.extractall(model_dir)
```

Następnie zarejestruj model w obszarze roboczym, aby był dostępny dla zdalnego zasobu obliczeniowego.

```python
from azureml.core.model import Model

# Register the downloaded model 
model = Model.register(model_path="models/",
                       model_name="mnist",
                       tags={'pretrained': "mnist"},
                       description="Mnist trained tensorflow model",
                       workspace=ws)
```

## <a name="write-your-inference-script"></a>Napisz skrypt wnioskowania

>[!Warning]
>Poniższy kod jest tylko przykładem używanym przez [przykładowy notes.](https://aka.ms/batch-inference-notebooks) Musisz utworzyć własny skrypt dla scenariusza.

Skrypt *musi zawierać* dwie funkcje:
- `init()`: Użyj tej funkcji do wszelkich kosztownych lub wspólnych preparatów do późniejszego wnioskowania. Na przykład użyj go, aby załadować model do obiektu globalnego. Ta funkcja zostanie wywołana tylko raz na początku procesu.
-  `run(mini_batch)`: Funkcja będzie działać `mini_batch` dla każdego wystąpienia.
    -  `mini_batch`: Krok uruchamiania równoległego wywoła metodę uruchamiania i przekaże listę lub Pandas DataFrame jako argument do metody. Każdy wpis w min_batch będzie - ścieżka pliku, jeśli dane wejściowe jest FileDataset, Pandas DataFrame jeśli dane wejściowe jest TabularDataset.
    -  `response`: run() metoda powinna zwrócić Pandas DataFrame lub tablicy. W przypadku append_row output_action te zwrócone elementy są dołączane do wspólnego pliku wyjściowego. W przypadku summary_only zawartość elementów jest ignorowana. Dla wszystkich akcji wyjściowych każdy zwrócony element wyjściowy wskazuje jedno pomyślne uruchomienie elementu wejściowego w wejściowej mini-partii. Należy upewnić się, że wystarczająca ilość danych jest uwzględniona w wyniku uruchomienia do mapowania danych wejściowych, aby uruchomić wynik. Uruchom dane wyjściowe zostaną zapisane w pliku wyjściowym i nie gwarantuje się w porządku, należy użyć pewnego klucza w danych wyjściowych, aby zamapować go na dane wejściowe.

```python
# Snippets from a sample script.
# Refer to the accompanying digit_identification.py
# (https://aka.ms/batch-inference-notebooks)
# for the implementation script.

import os
import numpy as np
import tensorflow as tf
from PIL import Image
from azureml.core import Model


def init():
    global g_tf_sess

    # Pull down the model from the workspace
    model_path = Model.get_model_path("mnist")

    # Construct a graph to execute
    tf.reset_default_graph()
    saver = tf.train.import_meta_graph(os.path.join(model_path, 'mnist-tf.model.meta'))
    g_tf_sess = tf.Session()
    saver.restore(g_tf_sess, os.path.join(model_path, 'mnist-tf.model'))


def run(mini_batch):
    print(f'run method start: {__file__}, run({mini_batch})')
    resultList = []
    in_tensor = g_tf_sess.graph.get_tensor_by_name("network/X:0")
    output = g_tf_sess.graph.get_tensor_by_name("network/output/MatMul:0")

    for image in mini_batch:
        # Prepare each image
        data = Image.open(image)
        np_im = np.array(data).reshape((1, 784))
        # Perform inference
        inference_result = output.eval(feed_dict={in_tensor: np_im}, session=g_tf_sess)
        # Find the best probability, and add it to the result list
        best_result = np.argmax(inference_result)
        resultList.append("{}: {}".format(os.path.basename(image), best_result))

    return resultList
```

### <a name="how-to-access-other-files-in-source-directory-in-entry_script"></a>Jak uzyskać dostęp do innych plików w katalogu źródłowym w entry_script

Jeśli w tym samym katalogu znajduje się inny plik lub folder co skrypt wejścia, możesz odwołać się do niego, znajdując bieżący katalog roboczy.

```python
script_dir = os.path.realpath(os.path.join(__file__, '..',))
file_path = os.path.join(script_dir, "<file_name>")
```

## <a name="build-and-run-the-pipeline-containing-parallelrunstep"></a>Tworzenie i uruchamianie potoku zawierającego ParallelRunStep

Teraz masz wszystko, czego potrzebujesz do zbudowania potoku.

### <a name="prepare-the-run-environment"></a>Przygotowanie środowiska uruchamiania

Najpierw określ zależności dla skryptu. Tego obiektu należy użyć później podczas tworzenia kroku potoku.

```python
from azureml.core.environment import Environment
from azureml.core.conda_dependencies import CondaDependencies
from azureml.core.runconfig import DEFAULT_GPU_IMAGE

batch_conda_deps = CondaDependencies.create(pip_packages=["tensorflow==1.13.1", "pillow"])

batch_env = Environment(name="batch_environment")
batch_env.python.conda_dependencies = batch_conda_deps
batch_env.docker.enabled = True
batch_env.docker.base_image = DEFAULT_GPU_IMAGE
batch_env.spark.precache_packages = False
```

### <a name="specify-the-parameters-for-your-batch-inference-pipeline-step"></a>Określ parametry kroku potoku wnioskowania partii

`ParallelRunConfig`jest główną konfiguracją dla nowo `ParallelRunStep` wprowadzonego wystąpienia wnioskowania wsadowego w potoku usługi Azure Machine Learning. Służy do zawijania skryptu i konfigurowania niezbędnych parametrów, w tym wszystkich następujących parametrów:
- `entry_script`: Skrypt użytkownika jako lokalna ścieżka pliku, która będzie uruchamiana równolegle w wielu węzłach. Jeśli `source_directory` jest obecny, należy użyć ścieżki względnej. W przeciwnym razie użyj dowolnej ścieżki, która jest dostępna na komputerze.
- `mini_batch_size`: Rozmiar mini-partii przeszedł do `run()` pojedynczego wywołania. (opcjonalnie; wartością `10` domyślną są pliki `1MB` dla zestawu danych file i tabulardataset).
    - Dla `FileDataset`, jest to liczba plików o `1`minimalnej wartości . Można połączyć wiele plików w jedną mini-partię.
    - Dla `TabularDataset`, jest to rozmiar danych. Przykładowe `1024`wartości `1024KB` `10MB`to `1GB`, , i . Zalecaną wartością jest `1MB`. Mini-partia z `TabularDataset` nigdy nie przekroczy granic pliku. Na przykład jeśli masz pliki csv o różnych rozmiarach, najmniejszy plik to 100 KB, a największy to 10 MB. Jeśli ustawisz `mini_batch_size = 1MB`, pliki o rozmiarze mniejszym niż 1 MB będą traktowane jako jedna miniparty. Pliki o rozmiarze większym niż 1 MB zostaną podzielone na wiele mini-partii.
- `error_threshold`: Liczba błędów rekordów `TabularDataset` i błędów plików dla `FileDataset` tego powinny być ignorowane podczas przetwarzania. Jeśli liczba błędów dla całego danych wejściowych przekroczy tę wartość, zadanie zostanie przerwane. Próg błędu jest dla całego wejścia, a nie dla `run()` poszczególnych mini-partii wysyłanych do metody. Zakres jest `[-1, int.max]`. Część `-1` wskazuje ignorowanie wszystkich błędów podczas przetwarzania.
- `output_action`: Jedna z następujących wartości wskazuje sposób organizacji danych wyjściowych:
    - `summary_only`: Skrypt użytkownika będzie przechowywać dane wyjściowe. `ParallelRunStep`będzie używać danych wyjściowych tylko do obliczania progu błędu.
    - `append_row`: Dla wszystkich plików wejściowych w folderze wyjściowym zostanie utworzony tylko jeden plik, aby dołączyć wszystkie wyjścia oddzielone wierszem. Nazwa pliku będzie `parallel_run_step.txt`.
- `source_directory`: Ścieżki do folderów, które zawierają wszystkie pliki do wykonania na obiekt obliczeniowy (opcjonalnie).
- `compute_target`: `AmlCompute` Obsługiwane jest tylko.
- `node_count`: Liczba węzłów obliczeniowych, które mają być używane do uruchamiania skryptu użytkownika.
- `process_count_per_node`: Liczba procesów na węzeł.
- `environment`: Definicja środowiska Pythona. Można go skonfigurować tak, aby używał istniejącego środowiska języka Python lub skonfigurował tymczasowe środowisko dla eksperymentu. Definicja jest również odpowiedzialny za ustawienie wymaganych zależności aplikacji (opcjonalnie).
- `logging_level`: Szczegółowość dziennika. Wartości w zwiększaniu szczegółowości `WARNING` `INFO`to: `DEBUG`, , i . (opcjonalnie; wartością `INFO`domyślną jest )
- `run_invocation_timeout`: `run()` Limit czasu wywołania metody w sekundach. (opcjonalnie; domyślna wartość to `60`)

```python
from azureml.contrib.pipeline.steps import ParallelRunConfig

parallel_run_config = ParallelRunConfig(
    source_directory=scripts_folder,
    entry_script="digit_identification.py",
    mini_batch_size="5",
    error_threshold=10,
    output_action="append_row",
    environment=batch_env,
    compute_target=compute_target,
    node_count=4)
```

### <a name="create-the-pipeline-step"></a>Tworzenie kroku potoku

Utwórz krok potoku przy użyciu skryptu, konfiguracji środowiska i parametrów. Określ cel obliczeniowy, który został już dołączony do obszaru roboczego jako cel wykonania dla skryptu. Służy `ParallelRunStep` do tworzenia kroku potoku wnioskowania partii, który przyjmuje wszystkie następujące parametry:
- `name`: Nazwa kroku, z następującymi ograniczeniami nazewnictwa: unikatowy, 3-32 znaki i regex ^\[a-z\]([-a-z0-9]*[a-z0-9])?$.
- `models`: Zero lub więcej nazw modeli już zarejestrowanych w rejestrze modelu usługi Azure Machine Learning.
- `parallel_run_config`: `ParallelRunConfig` Obiekt, zgodnie z definicją wcześniej.
- `inputs`: Co najmniej jeden pojedynczy typicznego zestawu danych usługi Azure Machine Learning.
- `output`: `PipelineData` Obiekt odpowiadający katalogowi wyjściowemu.
- `arguments`: Lista argumentów przekazanych do skryptu użytkownika (opcjonalnie).
- `allow_reuse`: Czy krok powinien ponownie używać poprzednich wyników podczas uruchamiania z tymi samymi ustawieniami/wejściami. Jeśli ten `False`parametr jest, nowy przebieg zawsze będą generowane dla tego kroku podczas wykonywania potoku. (opcjonalnie; wartość `True`domyślna to .)

```python
from azureml.contrib.pipeline.steps import ParallelRunStep

parallelrun_step = ParallelRunStep(
    name="batch-mnist",
    models=[model],
    parallel_run_config=parallel_run_config,
    inputs=[named_mnist_ds],
    output=output_dir,
    arguments=[],
    allow_reuse=True
)
```

>[!Note]
> Powyższy krok `azureml-contrib-pipeline-steps`zależy od , jak opisano w [Wymagania wstępne](#prerequisites). 

### <a name="submit-the-pipeline"></a>Prześlij potok

Teraz uruchom rurociąg. Najpierw utwórz [`Pipeline`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline%28class%29?view=azure-ml-py) obiekt przy użyciu odwołania do obszaru roboczego i kroku potoku, który został utworzony. Parametr `steps` jest tablicą kroków. W takim przypadku istnieje tylko jeden krok do oceniania partii. Aby utworzyć potoki, które mają wiele kroków, należy umieścić kroki w kolejności w tej tablicy.

Następnie użyj `Experiment.submit()` funkcji, aby przesłać potok do wykonania.

```python
from azureml.pipeline.core import Pipeline
from azureml.core.experiment import Experiment

pipeline = Pipeline(workspace=ws, steps=[parallelrun_step])
pipeline_run = Experiment(ws, 'digit_identification').submit(pipeline)
```

## <a name="monitor-the-parallel-run-job"></a>Monitorowanie zadania uruchamiania równoległego

Zadanie wnioskowania partii może zająć dużo czasu, aby zakończyć. W tym przykładzie monitoruje postęp przy użyciu widżetu Jupyter. Postęp zadania można również zarządzać za pomocą:

* Studio usługi Azure Machine Learning. 
* Wyjście konsoli [`PipelineRun`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.run.pipelinerun?view=azure-ml-py) z obiektu.

```python
from azureml.widgets import RunDetails
RunDetails(pipeline_run).show()

pipeline_run.wait_for_completion(show_output=True)
```

## <a name="next-steps"></a>Następne kroki

Aby zobaczyć ten proces pracy od końca do końca, spróbuj [notesu wnioskowania partii](https://aka.ms/batch-inference-notebooks). 

Aby uzyskać wskazówki dotyczące debugowania i rozwiązywania problemów dla ParallelRunStep, zobacz [przewodnik jak to zrobić](how-to-debug-parallel-run-step.md).

Aby uzyskać wskazówki dotyczące debugowania i rozwiązywania problemów z rurociągami, zobacz [przewodnik instrukcje](how-to-debug-pipelines.md).

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

