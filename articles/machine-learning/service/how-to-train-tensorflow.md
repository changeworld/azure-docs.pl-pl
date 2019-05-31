---
title: Szkolenie i zarejestruj modele TensorFlow
titleSuffix: Azure Machine Learning service
description: W tym artykule pokazano, jak szkolenie i zarejestrować model TensorFlow przy użyciu usługi Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: minxia
author: mx-iao
ms.date: 05/28/2019
ms.custom: seodec18
ms.openlocfilehash: 314917ce91407206d786b191df118893696ac82c
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/30/2019
ms.locfileid: "66417131"
---
# <a name="train-and-register-tensorflow-models-at-scale-with-azure-machine-learning-service"></a>Szkolenie i zarejestruj TensorFlow modeli na skalę przy użyciu usługi Azure Machine Learning

W tym artykule pokazano, jak szkolenie i zarejestrować model TensorFlow przy użyciu usługi Azure Machine Learning. Będziemy używać popularnej [zestawu danych mnist ręcznie ZAPISANYCH](http://yann.lecun.com/exdb/mnist/) do klasyfikowania cyfr pisma odręcznego za pomocą sieci neuronowej, oparta na TensorFlow.

Za pomocą usługi Azure Machine Learning będziesz mieć możliwość szybkiego skalowania w poziomie przy użyciu zasobów obliczeniowych w elastycznej chmurze zadań szkoleniowych typu open source. Będzie też można śledzić swoje przebiegów szkoleniowych, wersji modeli, wdrażanie modeli i wiele więcej. 

Czy tworzysz od podstaw modelu TensorFlow korzystamy istniejącego modelu w chmurze, możesz tworzyć modele gotowa do użycia w środowisku produkcyjnym za pomocą usługi Azure Machine Learning.

## <a name="prerequisites"></a>Wymagania wstępne

- Zainstaluj aplikację Azure Machine Learning zestawu SDK dla języka Python
- Opcjonalnie: Utwórz plik konfiguracji obszaru roboczego
- Pobierz [przykładowe pliki skryptów](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-tensorflow) `mnist-tf.py` i `utils.py`

Możesz wykonać [Przewodnik instalacji zestawu SDK języka Python](setup-create-workspace.md#sdk) instrukcje krok po kroku dotyczące konfigurowania środowiska. Przykładowe pliki szkolenia można znaleźć na naszej [stronę przykładów usługi GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-tensorflow) wraz z notesu Juypter rozszerzona, wersję tego przewodnika.

## <a name="set-up-the-experiment"></a>Konfigurowanie eksperymentu

### <a name="import-packages"></a>Importowanie pakietów

Najpierw musimy zaimportuj niezbędne biblioteki języka Python.

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

Obiekt obszaru roboczego jest zasób najwyższego poziomu dla usługi. Udostępnia scentralizowanym miejscem do pracy z wszystkich artefaktów, które można utworzyć.

Jeśli wykonano opcjonalny krok w [sekcji wymagania wstępne](#prerequisites), możesz użyć `Workspace.from_config()` szybko utworzyć obiekt workspace dane przechowywane w pliku konfiguracji.

```Python
ws = Workspace.from_config()
```

Można również można utworzyć obszar roboczy jawnie.

```Python
ws = Workspace.create(name='<workspace-name>',
                      subscription_id='<azure-subscription-id>',
                      resource_group='<choose-a-resource-group>',
                      create_resource_group=True,
                      location='<select-location>' # For example: 'eastus2'
                      )
```

### <a name="create-an-experiment"></a>Tworzenie eksperymentu

Tworzenie eksperymentów i folder do przechowywania skryptów szkolenia. W tym przykładzie należy utworzyć eksperyment o nazwie "tf-mnist ręcznie zapisanych"

```Python
script_folder = './tf-mnist'
os.makedirs(script_folder, exist_ok=True)

exp = Experiment(workspace=ws, name='tf-mnist')
```

### <a name="upload-dataset-and-scripts"></a>Przekaż zestaw danych i skryptów

[Datastore](how-to-access-data.md) jest miejscem, gdzie danych może być przechowywane i używane przez zainstalowanie lub skopiowanie danych do obliczeniowego elementu docelowego. Każdy obszar roboczy zawiera domyślny magazyn danych. Zostanie przekazany naszych danych i skryptów szkolenia, dzięki czemu można łatwo do nich podczas szkolenia.

1. Pobierz zestaw mnist ręcznie ZAPISANYCH danych lokalnie

    ```Python
    os.makedirs('./data/mnist', exist_ok=True)

    urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/train-images-idx3-ubyte.gz', filename = './data/mnist/train-images.gz')
    urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/train-labels-idx1-ubyte.gz', filename = './data/mnist/train-labels.gz')
    urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz', filename = './data/mnist/test-images.gz')
    urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/t10k-labels-idx1-ubyte.gz', filename = './data/mnist/test-labels.gz')
    ```

1. Przekaż zestaw danych mnist ręcznie ZAPISANYCH do domyślnego magazynu danych.

    ```Python
    ds = ws.get_default_datastore()
    ds.upload(src_dir='./data/mnist', target_path='mnist', overwrite=True, show_progress=True)
    ```

1. Przekaż skrypt szkolenia TensorFlow, `tf_mnist.py`, a plik pomocnika `utils.py`.

    ```Python
    shutil.copy('./tf_mnist.py', script_folder)
    shutil.copy('./utils.py', script_folder)
    ```

## <a name="create-a-compute-target"></a>Utworzyć cel obliczenia

Utworzyć obliczeniowego elementu docelowego dla zadania TensorFlow systemem. W tym przykładzie utworzymy klastra AmlCompute z włączonymi procesorami GPU. Aby uzyskać listę dostępnych szkoleń celów obliczeń, zobacz [w tym artykule](how-to-set-up-training-targets.md#compute-targets-for-training)

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

## <a name="create-a-tensorflow-estimator"></a>Tworzenie szacowania TensorFlow

[TensorFlow narzędzie do szacowania](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) zapewnia prosty sposób uruchamiania zadania szkolenia TensorFlow na obliczeniowego elementu docelowego. Automatycznie dostarczy obrazu platformy docker, który ma zainstalowany TensorFlow.

Można dołączyć dodatkowe pakiety pip lub conda Wynikowy obraz platformy docker, przekazując ich nazwiska za pomocą `pip_packages` i `conda_packages` argumentów.

```Python
script_params = {
    '--data-folder': ws.get_default_datastore().as_mount(),
    '--batch-size': 50,
    '--first-layer-neurons': 300,
    '--second-layer-neurons': 100,
    '--learning-rate': 0.01
}

est = TensorFlow(source_directory=script_folder,
                 script_params=script_params,
                 compute_target=compute_target,
                 entry_script='tf_mnist.py',
                 use_gpu=True)
```

## <a name="submit-a-run"></a>Prześlij przebiegu

[Przebiegu](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) zapewnia interfejs do historii uruchamiania podczas uruchamiania zadania, a po jego ukończeniu.

```Python
run = exp.submit(est)
run.wait_for_completion(show_output=True)
```

Uruchomienie jest wykonywane, ponieważ będą przekazywane za pośrednictwem następujących etapów:

- **Trwa przygotowywanie**: Obraz platformy docker są tworzone zgodnie ze skonfigurowanym TensorFlow narzędzie do szacowania. Obraz, który jest przekazany do obszaru roboczego container registry i nowszych przebiegów w pamięci podręcznej. Dzienniki również są przesyłane strumieniowo do historii uruchamiania i mogą być wyświetlane w celu monitorowania postępu.

- **Skalowanie**: Klaster będzie podejmować próby skalowania w górę Jeśli klaster usługi Batch AI wymaga więcej węzłów do wykonania przebiegu nie są obecnie dostępne.

- **Uruchamianie**: Wszystkie skrypty w folderze skryptu są przekazywane do obliczeniowego elementu docelowego, magazyny danych są zainstalowane lub kopiowane i entry_script jest wykonywany. Dane wyjściowe stdout i. / folderu dzienników są przesyłane strumieniowo do historii uruchamiania i może służyć do monitorowania przebiegu.

- **Przetwarzanie końcowe**: . / Wyjścia, folder przebiegu jest kopiowana do historii uruchamiania.

## <a name="register-or-download-a-model"></a>Zarejestruj lub pobrać modelu

Gdy już uczony model, można zarejestrować go do obszaru roboczego. Rejestracja modelu pozwala magazynu i wersji modeli w Twoim obszarze roboczym, aby uprościć [zarządzania i wdrażania modelu](concept-model-management-and-deployment.md).

```Python
model = run.register_model(model_name='tf-dnn-mnist', model_path='outputs/model')
```

Można również pobrać kopię lokalną modelu przy użyciu obiektu przebiegu. W skrypcie szkolenia `mnist-tf.py`, obiekt wygaszacz TensorFlow będzie się powtarzał modelu do folderu lokalnego (local do obliczeniowego elementu docelowego). Możemy użyć obiektu Uruchom, aby pobrać kopię.

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

[ `TensorFlow` ](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) Narzędzie do szacowania obsługuje również rozproszonego szkolenia w klastrach GPU i CPU. Łatwo można uruchamiać rozproszone zadania TensorFlow i usługi Azure Machine Learning będą zarządzać aranżacji dla Ciebie.

Usługa Azure Machine Learning obsługuje dwie metody rozproszonego szkolenia w TensorFlow:

* [Na podstawie MPI](https://www.open-mpi.org/) rozproszonego szkolenia przy użyciu [Horovod](https://github.com/uber/horovod) framework
* Natywne [rozproszonych TensorFlow](https://www.tensorflow.org/deploy/distributed) przy użyciu parametru metody serwera

### <a name="horovod"></a>Horovod

[Horovod](https://github.com/uber/horovod) to architektura typu open source dla rozproszonego szkolenia opracowany przez Uber. Oferuje ona ścieżką łatwe rozproszone zadania TensorFlow procesora GPU.

Aby użyć Horovod, określ `mpi` dla `distributed_training` parametr w Konstruktorze narzędzie do szacowania TensorFlow. Horovod zostaną zainstalowane do użycia w skrypcie szkolenia.

```Python
from azureml.train.dnn import TensorFlow

# Tensorflow constructor
estimator= TensorFlow(source_directory=project_folder,
                      compute_target=compute_target,
                      script_params=script_params,
                      entry_script='script.py',
                      node_count=2,
                      process_count_per_node=1,
                      distributed_backend='mpi',
                      use_gpu=True)
```

### <a name="parameter-server"></a>Serwer parametrów

Można również uruchomić [native rozproszonych TensorFlow](https://www.tensorflow.org/deploy/distributed), parametr modelem serwera, który używa. W przypadku tej metody szkolenie w klastrze serwerów parametru i procesów roboczych. Procesy robocze obliczania gradientów podczas szkolenia, gdy serwery parametr agregacji gradientów.

Aby użyć parametru metody serwera, określ `ps` dla `distributed_training` parametr w Konstruktorze narzędzie do szacowania TensorFlow.

```Python
from azureml.train.dnn import TensorFlow

# Tensorflow constructor
estimator= TensorFlow(source_directory=project_folder,
                      compute_target=compute_target,
                      script_params=script_params,
                      entry_script='script.py',
                      node_count=2,
                      process_count_per_node=1,
                      distributed_backend='ps',
                      use_gpu=True)

# submit the TensorFlow job
run = exp.submit(tf_est)
```

#### <a name="note-on-tfconfig"></a>Uwaga dotycząca `TF_CONFIG`

Należy także adresów sieciowych i portów klastra na potrzeby [ `tf.train.ClusterSpec` ](https://www.tensorflow.org/api_docs/python/tf/train/ClusterSpec), dzięki czemu usługa Azure Machine Learning ustawia `TF_CONFIG` zmienną środowiskową dla Ciebie.

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

Na wysokim poziomie firmy TensorFlow [ `tf.estimator` ](https://www.tensorflow.org/api_docs/python/tf/estimator) interfejsu API, TensorFlow będzie analizować to `TF_CONFIG` Specyfikacja zmienną i kompilacji klastra dla Ciebie.

Firmy TensorFlow niższego poziomu podstawowe interfejsy API, szkolenia, można przeanalizować `TF_CONFIG` zmienną i kompilacji `tf.train.ClusterSpec` w kodzie szkolenia. W [w tym przykładzie](https://aka.ms/aml-notebook-tf-ps), dlatego w, należy wykonać **skrypt szkolenia** w następujący sposób:

```Python
import os, json
import tensorflow as tf

tf_config = os.environ.get('TF_CONFIG')
if not tf_config or tf_config == "":
    raise ValueError("TF_CONFIG not found.")
tf_config_json = json.loads(tf_config)
cluster_spec = tf.train.ClusterSpec(cluster)

```

## <a name="next-steps"></a>Kolejne kroki

W tym artykule przeszkolonych i zarejestrowane w usłudze Azure Machine Learning model TensorFlow. Aby dowiedzieć się, jak wdrożyć model, przejdź do naszego artykułu wdrażania modelu.

> [!div class="nextstepaction"]
> [Jak i gdzie umożliwia wdrażanie modeli](how-to-deploy-and-where.md)
