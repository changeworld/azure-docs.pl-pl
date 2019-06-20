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
ms.date: 06/10/2019
ms.custom: seodec18
ms.openlocfilehash: e1f587785b06f10bab42a425b0910d4f25fddf9f
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/17/2019
ms.locfileid: "67165537"
---
# <a name="train-and-register-tensorflow-models-at-scale-with-azure-machine-learning-service"></a>Szkolenie i zarejestruj TensorFlow modeli na skalę przy użyciu usługi Azure Machine Learning

W tym artykule pokazano, jak szkolenie i zarejestrować model TensorFlow przy użyciu usługi Azure Machine Learning. Używa ona popularnej [zestawu danych mnist ręcznie ZAPISANYCH](http://yann.lecun.com/exdb/mnist/) do klasyfikowania cyfr pisma odręcznego za pomocą sieci neuronowej, utworzonych przy użyciu [biblioteki TensorFlow Python](https://www.tensorflow.org/overview).

TensorFlow to platforma obliczeniowa typu open-source najczęściej używany do tworzenia głębokich sieciach neuronowych (DNN). Za pomocą usługi Azure Machine Learning można szybko skalować w poziomie zadania szkoleń o całkowicie otwartym kodzie źródłowym, przy użyciu zasobów obliczeniowych w elastycznej chmurze. Można także śledzić swoje przebiegów szkoleniowych, modele wersji wdrażanie modeli i wiele więcej.

Czy tworzysz od podstaw modelu TensorFlow korzystamy istniejącego modelu w chmurze, usługi Azure Machine Learning mogą pomóc tworzyć modele gotowe do produkcji.

## <a name="prerequisites"></a>Wymagania wstępne

Uruchom ten kod w dowolnym z tych środowisk:

 - Notesu platformy Azure Machine Learning maszyny Wirtualnej — bez plików do pobrania i instalacji konieczne

     - Wykonaj [Szybki Start oparte na chmurze notesu](quickstart-run-cloud-notebook.md) utworzyć dedykowany serwer wstępnie załadowane z zestawu SDK i przykładowe repozytorium.
    - W folderze samples na serwerze Notes, należy znaleźć notesu ukończonych oraz rozszerzone, przechodząc do tego katalogu: **jak-to-użyj-usługi Azure ml > szkolenia z głębokiego uczenia > train-hyperparameter-tune-deploy-with-tensorflow**folderu. 
 
 - Serwer notesu programu Jupyter

     - [Zainstaluj aplikację Azure Machine Learning zestawu SDK dla języka Python](setup-create-workspace.md#sdk)
    - [Utwórz plik konfiguracji obszaru roboczego](setup-create-workspace.md#write-a-configuration-file)
    - [Pobieranie plików przykładowych skryptów](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-tensorflow) `mnist-tf.py` i `utils.py`
     
    Możesz również znaleźć ukończone [wersji notesu programu Jupyter](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-tensorflow/train-hyperparameter-tune-deploy-with-tensorflow.ipynb) tego przewodnika stronę przykładów usługi GitHub. Notes zawiera rozwinięte sekcje, obejmujące strojenia hiperparametrycznego inteligentnego, wdrażanie modelu i widżetów notesu.

## <a name="set-up-the-experiment"></a>Konfigurowanie eksperymentu

W tej sekcji konfiguruje eksperymentu szkolenia ładowania pakietów wymaganych języka python, inicjowanie obszar roboczy, tworzenia eksperymentu i przekazywanie danych szkoleniowych i skryptów szkolenia.

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

[Obszaru roboczego usługi Azure Machine Learning](concept-workspace.md) jest zasobem najwyższego poziomu dla usługi. Udostępnia scentralizowanym miejscem do pracy z wszystkich artefaktów, które można utworzyć. W zestawu SDK języka Python, uzyskujesz dostęp artefaktów obszaru roboczego, tworząc [ `workspace` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) obiektu.

Utwórz obiekt obszaru roboczego z `config.json` pliku utworzonego w [sekcji wymagania wstępne](#prerequisites).

```Python
ws = Workspace.from_config()
```

### <a name="create-an-experiment"></a>Tworzenie eksperymentu

Tworzenie eksperymentów i folder do przechowywania skryptów szkolenia. W tym przykładzie należy utworzyć eksperyment, o nazwie "tf-mnist ręcznie zapisanych".

```Python
script_folder = './tf-mnist'
os.makedirs(script_folder, exist_ok=True)

exp = Experiment(workspace=ws, name='tf-mnist')
```

### <a name="upload-dataset-and-scripts"></a>Przekaż zestaw danych i skryptów

[Datastore](how-to-access-data.md) jest miejscem, gdzie danych może być przechowywane i używane przez zainstalowanie lub skopiowanie danych do obliczeniowego elementu docelowego. Każdy obszar roboczy zawiera domyślny magazyn danych. Przekazywanie danych i skryptów szkolenia z magazynem danych, dzięki czemu można łatwo do nich podczas szkolenia.

1. Pobierz zestaw mnist ręcznie ZAPISANYCH danych lokalnie.

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

Utworzyć obliczeniowego elementu docelowego dla zadania TensorFlow systemem. W tym przykładzie należy utworzyć klaster obliczeniowy włączonymi procesorami GPU z usługi Azure Machine Learning.

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

Aby uzyskać więcej informacji na temat obliczeniowych elementów docelowych, zobacz [co to jest cel obliczenia](concept-compute-target.md) artykułu.

## <a name="create-a-tensorflow-estimator"></a>Tworzenie szacowania TensorFlow

[TensorFlow narzędzie do szacowania](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) zapewnia prosty sposób uruchamiania zadania szkolenia TensorFlow na obliczeniowego elementu docelowego.

Narzędzie do szacowania TensorFlow jest implementowane za pomocą ogólnego [ `estimator` ](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) klasy, która może służyć do obsługi dowolnej platformy. Aby uzyskać więcej informacji na temat szkolenie modeli za pomocą ogólnego narzędzie do szacowania zobacz [uczenia modeli za pomocą usługi Azure Machine Learning przy użyciu narzędzie do szacowania](how-to-train-ml-models.md)

Jeśli dodatkowe narzędzia pip lub conda pakietów, aby uruchomić skrypt szkolenia, może mieć zainstalowanego na Wynikowy obraz platformy docker, przekazując ich nazwiska za pomocą pakietu `pip_packages` i `conda_packages` argumentów.

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

## <a name="submit-a-run"></a>Prześlij przebiegu

[Przebiegu](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) zapewnia interfejs do historii uruchamiania podczas uruchamiania zadania, a po jego ukończeniu.

```Python
run = exp.submit(est)
run.wait_for_completion(show_output=True)
```

Uruchomienie jest wykonywane, przechodzi on przez następujące etapy:

- **Trwa przygotowywanie**: Obraz platformy docker są tworzone zgodnie ze skonfigurowanym TensorFlow narzędzie do szacowania. Obraz, który jest przekazany do obszaru roboczego container registry i nowszych przebiegów w pamięci podręcznej. Dzienniki również są przesyłane strumieniowo do historii uruchamiania i mogą być wyświetlane w celu monitorowania postępu.

- **Skalowanie**: Klaster polegające na skalowanie w górę, jeśli klaster usługi Batch AI wymaga więcej węzłów do wykonania przebiegu nie są obecnie dostępne.

- **Uruchamianie**: Wszystkie skrypty w folderze skryptu są przekazywane do obliczeniowego elementu docelowego, magazyny danych są zainstalowane lub kopiowane i entry_script jest wykonywany. Dane wyjściowe ze strumienia wyjściowego stdout i. / folderu dzienników są przesyłane strumieniowo do historii uruchamiania i może służyć do monitorowania przebiegu.

- **Przetwarzanie końcowe**: . / Wyjścia, folder przebiegu jest kopiowana do historii uruchamiania.

## <a name="register-or-download-a-model"></a>Zarejestruj lub pobrać modelu

Gdy już uczony model, można zarejestrować go do obszaru roboczego. Rejestracja modelu pozwala magazynu i wersji modeli w Twoim obszarze roboczym, aby uprościć [zarządzania i wdrażania modelu](concept-model-management-and-deployment.md).

```Python
model = run.register_model(model_name='tf-dnn-mnist', model_path='outputs/model')
```

Można również pobrać kopię lokalną modelu przy użyciu obiektu przebiegu. W skrypcie szkolenia `mnist-tf.py`, obiekt wygaszacz TensorFlow będzie się powtarzał modelu do folderu lokalnego (local do obliczeniowego elementu docelowego). Aby pobrać kopię, można użyć obiektu przebiegu.

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

- [Na podstawie MPI](https://www.open-mpi.org/) rozproszonego szkolenia przy użyciu [Horovod](https://github.com/uber/horovod) framework
- Natywne [rozproszonych TensorFlow](https://www.tensorflow.org/deploy/distributed) przy użyciu parametru metody serwera

### <a name="horovod"></a>Horovod

[Horovod](https://github.com/uber/horovod) to architektura typu open source dla rozproszonego szkolenia opracowany przez Uber. Oferuje ona ścieżką łatwe rozproszone zadania TensorFlow procesora GPU.

Aby użyć Horovod, określ [ `MpiConfiguration` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration?view=azure-ml-py) dla obiektu `distributed_training` parametr w Konstruktorze TensorFlow. Tego parametru gwarantuje zainstalowanie biblioteki Horovod do użycia w skrypcie szkolenia.

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

Aby użyć parametru metody serwera, określ [ `TensorflowConfiguration` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.tensorflowconfiguration?view=azure-ml-py) dla obiektu `distributed_training` parametr w Konstruktorze TensorFlow.

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

#### <a name="define-cluster-specifications-in-tfconfig"></a>Definiowanie specyfikacji klastra w "TF_CONFIG"

Należy również adresów sieciowych i portów klastra na potrzeby [ `tf.train.ClusterSpec` ](https://www.tensorflow.org/api_docs/python/tf/train/ClusterSpec), dzięki czemu usługa Azure Machine Learning ustawia `TF_CONFIG` zmienną środowiskową dla Ciebie.

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

Na wysokim poziomie firmy TensorFlow [ `tf.estimator` ](https://www.tensorflow.org/api_docs/python/tf/estimator) analizuje TensorFlow interfejsu API, `TF_CONFIG` zmienną i kompilacje specyfikacja klastra dla Ciebie.

Firmy TensorFlow niższego poziomu podstawowe interfejsy API, szkolenia, można przeanalizować `TF_CONFIG` zmienną i kompilacji `tf.train.ClusterSpec` w kodzie szkolenia.

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
