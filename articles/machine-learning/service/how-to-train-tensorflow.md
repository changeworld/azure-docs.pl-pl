---
title: Uczenie i rejestrowanie modeli TensorFlow
titleSuffix: Azure Machine Learning service
description: W tym artykule przedstawiono sposób uczenia i rejestrowania modelu TensorFlow przy użyciu usługi Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: maxluk
author: maxluk
ms.date: 06/10/2019
ms.custom: seodec18
ms.openlocfilehash: a5d281598bc905914b71f40d556cfa0b16a46485
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2019
ms.locfileid: "68847649"
---
# <a name="train-and-register-tensorflow-models-at-scale-with-azure-machine-learning-service"></a>Uczenie i rejestrowanie modeli TensorFlow na dużą skalę za pomocą usługi Azure Machine Learning

W tym artykule przedstawiono sposób uczenia i rejestrowania modelu TensorFlow przy użyciu usługi Azure Machine Learning. Używa popularnego [zestawu danych mnist ręcznie](http://yann.lecun.com/exdb/mnist/) do klasyfikowania ręcznych cyfr przy użyciu głębokiej sieci neuronowych utworzonej przy użyciu [biblioteki języka Python TensorFlow](https://www.tensorflow.org/overview).

TensorFlow to platforma obliczeniowa Open Source często używana do tworzenia sieci głębokiej neuronowych (DNN). Usługa Azure Machine Learning umożliwia szybkie skalowanie zadań szkoleniowych "open source" przy użyciu elastycznych zasobów obliczeniowych w chmurze. Możesz również śledzić przebiegi szkoleniowe, modele wersji, wdrażać modele i wiele innych.

Bez względu na to, czy tworzysz model TensorFlow z podstaw, czy przenosisz [istniejący model](how-to-deploy-existing-model.md) do chmury, usługa Azure Machine Learning może pomóc w tworzeniu modeli gotowych do produkcji.

## <a name="prerequisites"></a>Wymagania wstępne

Uruchom ten kod w dowolnym z następujących środowisk:

 - Maszyna wirtualna w Azure Machine Learning Notes — nie jest wymagane pobieranie ani instalacja

     - Wykonaj kroki z artykułu [Samouczek: Zainstaluj środowisko i obszar](tutorial-1st-experiment-sdk-setup.md) roboczy, aby utworzyć dedykowany serwer notesu wstępnie załadowany z zestawem SDK i przykładowym repozytorium.
    - W folderze Samples na serwerze notesu Znajdź ukończony i rozwinięty Notes, przechodząc do tego katalogu: How to- **use-azure > Training-with-Learning-uczenie > uczenie** ----------tensorflow. 
 
 - Własny serwer Jupyter Notebook

    - [Zainstaluj zestaw SDK Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).
    - [Utwórz plik konfiguracji obszaru roboczego](how-to-configure-environment.md#workspace).
    - [Pobierz pliki przykładowego skryptu](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-tensorflow) `mnist-tf.py` i`utils.py`
     
    Ukończoną [wersję Jupyter Notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-tensorflow/train-hyperparameter-tune-deploy-with-tensorflow.ipynb) tego przewodnika można również znaleźć na stronie przykładów usługi GitHub. Notes obejmuje rozwinięte sekcje obejmujące dostrajanie inteligentnego parametru, wdrożenie modelu i widżety notesu.

## <a name="set-up-the-experiment"></a>Konfigurowanie eksperymentu

Ta sekcja umożliwia skonfigurowanie eksperymentu szkoleniowego przez załadowanie wymaganych pakietów języka Python, zainicjowanie obszaru roboczego, utworzenie eksperymentu i przekazanie danych szkoleniowych i skryptów szkoleniowych.

### <a name="import-packages"></a>Importowanie pakietów

Najpierw zaimportuj niezbędne biblioteki języka Python.

```Python
import os
import urllib
import shutil
import azureml

from azureml.core import Experiment
from azureml.core import Workspace, Run

from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException
```

### <a name="initialize-a-workspace"></a>Inicjowanie obszaru roboczego

[Obszar roboczy usługi Azure Machine Learning](concept-workspace.md) jest zasobem najwyższego poziomu dla usługi. Zapewnia ono scentralizowane miejsce do pracy ze wszystkimi tworzonymi artefaktami. W zestawie SDK języka Python można uzyskać dostęp do artefaktów obszaru roboczego przez [`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) utworzenie obiektu.

Utwórz obiekt obszaru roboczego z `config.json` pliku utworzonego w [sekcji wymagania wstępne](#prerequisites).

```Python
ws = Workspace.from_config()
```

### <a name="create-an-experiment"></a>Tworzenie eksperymentu

Utwórz eksperyment i folder do przechowywania skryptów szkoleniowych. W tym przykładzie Utwórz eksperyment o nazwie "TF-mnist ręcznie".

```Python
script_folder = './tf-mnist'
os.makedirs(script_folder, exist_ok=True)

exp = Experiment(workspace=ws, name='tf-mnist')
```

### <a name="upload-dataset-and-scripts"></a>Przekazywanie zestawu danych i skryptów

[Magazyn](how-to-access-data.md) danych to miejsce, w którym dane mogą być przechowywane i dostępne przez zainstalowanie lub skopiowanie danych do elementu docelowego obliczeń. Każdy obszar roboczy zawiera domyślny magazyn danych. Przekaż skrypty danych i szkoleniowe do magazynu datastore, aby można było łatwo uzyskać do nich dostęp podczas szkoleń.

1. Pobierz zestaw danych MNIST ręcznie lokalnie.

    ```Python
    os.makedirs('./data/mnist', exist_ok=True)

    urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/train-images-idx3-ubyte.gz', filename = './data/mnist/train-images.gz')
    urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/train-labels-idx1-ubyte.gz', filename = './data/mnist/train-labels.gz')
    urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz', filename = './data/mnist/test-images.gz')
    urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/t10k-labels-idx1-ubyte.gz', filename = './data/mnist/test-labels.gz')
    ```

1. Przekaż MNIST ręcznie DataSet do domyślnego magazynu danych.

    ```Python
    ds = ws.get_default_datastore()
    ds.upload(src_dir='./data/mnist', target_path='mnist', overwrite=True, show_progress=True)
    ```

1. Przekaż skrypt `tf_mnist.py`szkolenia TensorFlow oraz `utils.py`plik pomocnika.

    ```Python
    shutil.copy('./tf_mnist.py', script_folder)
    shutil.copy('./utils.py', script_folder)
    ```

## <a name="create-a-compute-target"></a>Utworzyć cel obliczenia

Utwórz obiekt docelowy obliczeń dla zadania TensorFlow. W tym przykładzie należy utworzyć klaster obliczeniowy z obsługą procesora GPU Azure Machine Learning.

```Python
cluster_name = "gpucluster"

try:
    compute_target = ComputeTarget(workspace=ws, name=cluster_name)
    print('Found existing compute target')
except ComputeTargetException:
    print('Creating a new compute target...')
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_NC6', 
                                                           max_nodes=4)

    compute_target = ComputeTarget.create(ws, cluster_name, compute_config)

    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
```

Aby uzyskać więcej informacji na temat obiektów docelowych obliczeń, zobacz artykuł [co to jest Target COMPUTE](concept-compute-target.md) .

## <a name="create-a-tensorflow-estimator"></a>Tworzenie TensorFlow szacowania

[TensorFlow szacowania](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) zapewnia prosty sposób uruchamiania zadania szkolenia TensorFlow na obiekcie docelowym obliczeń.

TensorFlow szacowania jest implementowane za pomocą klasy [`estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) generycznej, która może służyć do obsługi dowolnej struktury. Aby uzyskać więcej informacji o modelach szkoleniowych przy użyciu generycznej szacowania, zobacz [uczenie modeli Azure Machine Learning przy](how-to-train-ml-models.md) użyciu usługi szacowania

Jeśli do uruchomienia skryptu szkoleniowego wymagane są dodatkowe pakiety PIP lub Conda, można je zainstalować na powstającym obrazie Docker, przekazując ich nazwy za pomocą `pip_packages` argumentów i. `conda_packages`

```Python
script_params = {
    '--data-folder': ws.get_default_datastore().as_mount(),
    '--batch-size': 50,
    '--first-layer-neurons': 300,
    '--second-layer-neurons': 100,
    '--learning-rate': 0.01
}

est = TensorFlow(source_directory=script_folder,
                 entry_script='tf_mnist.py',
                 script_params=script_params,
                 compute_target=compute_target,
                 use_gpu=True)
```

## <a name="submit-a-run"></a>Prześlij przebieg

[Obiekt Run](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) udostępnia interfejs do historii uruchamiania, gdy zadanie jest uruchomione i po jego zakończeniu.

```Python
run = exp.submit(est)
run.wait_for_completion(show_output=True)
```

Gdy przebieg jest wykonywany, przechodzi przez następujące etapy:

- **Przygotowywanie**: Obraz platformy Docker jest tworzony zgodnie z TensorFlow szacowania. Obraz zostanie przekazany do rejestru kontenerów obszaru roboczego i zapisany w pamięci podręcznej do późniejszego uruchomienia. Dzienniki są również przesyłane strumieniowo do historii uruchamiania i mogą być przeglądane w celu monitorowania postępu.

- **Skalowanie**: Klaster próbuje skalować w górę, Jeśli klaster Batch AI wymaga więcej węzłów do uruchomienia przebiegu niż jest to obecnie dostępne.

- **Uruchamianie**: Wszystkie skrypty w folderze skryptów są przekazywane do obiektu docelowego obliczeń, magazyny danych są instalowane lub kopiowane, a entry_script jest wykonywane. Dane wyjściowe z stdout i folder./Logs są przesyłane strumieniowo do historii uruchamiania i mogą być używane do monitorowania przebiegu.

- **Przetwarzanie końcowe**: Folder./Outputs przebiegu jest kopiowany do historii uruchamiania.

## <a name="register-or-download-a-model"></a>Rejestrowanie lub pobieranie modelu

Po przeszkoleniu modelu możesz zarejestrować go w obszarze roboczym. Rejestracja modelu umożliwia przechowywanie modeli i ich wersji w obszarze roboczym w celu uproszczenia [zarządzania modelami i ich wdrażania](concept-model-management-and-deployment.md).

```Python
model = run.register_model(model_name='tf-dnn-mnist', model_path='outputs/model')
```

Możesz również pobrać lokalną kopię modelu przy użyciu obiektu Run. W skrypcie `mnist-tf.py`szkoleniowym obiekt wygaszacza TensorFlow zachowuje model do folderu lokalnego (lokalnie dla elementu docelowego obliczeń). Aby pobrać kopię, można użyć obiektu Run.

```Python
# Create a model folder in the current directory
os.makedirs('./model', exist_ok=True)

for f in run.get_file_names():
    if f.startswith('outputs/model'):
        output_file_path = os.path.join('./model', f.split('/')[-1])
        print('Downloading from {} to {} ...'.format(f, output_file_path))
        run.download_file(name=f, output_file_path=output_file_path)
```

## <a name="distributed-training"></a>Rozproszonego szkolenia

[`TensorFlow`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) Szacowania obsługuje również szkolenia rozproszone między procesorami i klastrami GPU. Można łatwo uruchomić rozproszone zadania TensorFlow, a usługa Azure Machine Learning będzie zarządzać aranżacją.

Usługa Azure Machine Learning obsługuje dwie metody rozłożonego szkolenia w TensorFlow:

- Szkolenie rozproszone [oparte na MPI](https://www.open-mpi.org/) za pomocą platformy [Horovod](https://github.com/uber/horovod)
- Natywne [rozproszone TensorFlow](https://www.tensorflow.org/deploy/distributed) przy użyciu metody serwera parametrów

### <a name="horovod"></a>Horovod

[Horovod](https://github.com/uber/horovod) to struktura "open source" dla szkolenia rozproszonego opracowanego przez Uber. Oferuje łatwą ścieżkę do dystrybuowanych zadań TensorFlow procesora GPU.

Aby użyć Horovod, określ [`MpiConfiguration`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration?view=azure-ml-py) obiekt `distributed_training` dla parametru w konstruktorze TensorFlow. Ten parametr zapewnia, że biblioteka Horovod jest zainstalowana do użycia w skrypcie szkoleniowym.

```Python
from azureml.train.dnn import TensorFlow

# Tensorflow constructor
estimator= TensorFlow(source_directory=project_folder,
                      compute_target=compute_target,
                      script_params=script_params,
                      entry_script='script.py',
                      node_count=2,
                      process_count_per_node=1,
                      distributed_training=MpiConfiguration(),
                      framework_version='1.13',
                      use_gpu=True)
```

### <a name="parameter-server"></a>Serwer parametrów

Można również uruchomić [native rozproszonych TensorFlow](https://www.tensorflow.org/deploy/distributed), parametr modelem serwera, który używa. W przypadku tej metody szkolenie w klastrze serwerów parametru i procesów roboczych. Procesy robocze obliczania gradientów podczas szkolenia, gdy serwery parametr agregacji gradientów.

Aby użyć metody serwera parametrów, określ [`TensorflowConfiguration`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.tensorflowconfiguration?view=azure-ml-py) obiekt `distributed_training` dla parametru w konstruktorze TensorFlow.

```Python
from azureml.train.dnn import TensorFlow

distributed_training = TensorflowConfiguration()
distributed_training.worker_count = 2

# Tensorflow constructor
estimator= TensorFlow(source_directory=project_folder,
                      compute_target=compute_target,
                      script_params=script_params,
                      entry_script='script.py',
                      node_count=2,
                      process_count_per_node=1,
                      distributed_training=distributed_training,
                      use_gpu=True)

# submit the TensorFlow job
run = exp.submit(tf_est)
```

#### <a name="define-cluster-specifications-in-tf_config"></a>Zdefiniuj specyfikacje klastra w "TF_CONFIG"

Potrzebne są również adresy sieciowe i porty klastra dla programu [`tf.train.ClusterSpec`](https://www.tensorflow.org/api_docs/python/tf/train/ClusterSpec), dlatego Azure Machine Learning `TF_CONFIG` ustawia zmienną środowiskową dla użytkownika.

`TF_CONFIG` Zmienna środowiskowa jest ciąg JSON. Oto przykład zmiennej serwera parametru:

```JSON
TF_CONFIG='{
    "cluster": {
        "ps": ["host0:2222", "host1:2222"],
        "worker": ["host2:2222", "host3:2222", "host4:2222"],
    },
    "task": {"type": "ps", "index": 0},
    "environment": "cloud"
}'
```

W przypadku interfejsu API wysokiego [`tf.estimator`](https://www.tensorflow.org/api_docs/python/tf/estimator) poziomu TensorFlow TensorFlow analizuje `TF_CONFIG` zmienną i kompiluje specyfikację klastra.

W przypadku podstawowych interfejsów API TensorFlow na niższym poziomie na potrzeby szkoleń `TF_CONFIG` należy analizować zmienną i `tf.train.ClusterSpec` skompilować ją w kodzie szkoleniowym.

```Python
import os, json
import tensorflow as tf

tf_config = os.environ.get('TF_CONFIG')
if not tf_config or tf_config == "":
    raise ValueError("TF_CONFIG not found.")
tf_config_json = json.loads(tf_config)
cluster_spec = tf.train.ClusterSpec(cluster)

```

## <a name="next-steps"></a>Następne kroki

W tym artykule został przeszkolony i zarejestrowany model TensorFlow. Aby dowiedzieć się, jak wdrożyć model w klastrze z obsługą procesora GPU, przejdź do artykułu Wdrażanie modelu procesora GPU.

[Jak wdrożyć program inferencing z procesorami GPU](how-to-deploy-inferencing-gpus.md)
[, jak monitorować za pomocą Tensorboard](how-to-monitor-tensorboard.md)
