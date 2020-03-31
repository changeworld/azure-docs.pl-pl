---
title: Szkolenie i wdrażanie modelu TensorFlow
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak uruchamiać skrypty szkoleniowe TensorFlow na dużą skalę przy użyciu usługi Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: maxluk
author: maxluk
ms.date: 08/20/2019
ms.custom: seodec18
ms.openlocfilehash: 2bbd81f3858aa78b9e0e2d610c0fdb0a67816c8e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78228313"
---
# <a name="build-a-tensorflow-deep-learning-model-at-scale-with-azure-machine-learning"></a>Tworzenie modelu uczenia głębokiego TensorFlow na dużą skalę dzięki usłudze Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

W tym artykule pokazano, jak uruchomić skrypty szkoleniowe [TensorFlow](https://www.tensorflow.org/overview) na dużą skalę przy użyciu klasy [estymatora TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) usługi Azure Machine Learning. W tym przykładzie trenuje i rejestruje model TensorFlow do klasyfikowania cyfr odręcznych przy użyciu głębokiej sieci neuronowej (DNN).

Niezależnie od tego, czy tworzysz model TensorFlow od podstaw, czy wprowadzasz [istniejący model](how-to-deploy-existing-model.md) do chmury, możesz użyć usługi Azure Machine Learning do skalowania w poziomie zadań szkoleniowych typu open source do tworzenia, wdrażania, wersji i monitorowania modeli klasy produkcyjnej.

Dowiedz się więcej o [uczeniu głębokim i uczeniu maszynowym.](concept-deep-learning-vs-machine-learning.md)

## <a name="prerequisites"></a>Wymagania wstępne

Uruchom ten kod w jednym z tych środowisk:

 - Wystąpienie obliczeń usługi Azure Machine Learning — nie jest wymagane pobieranie ani instalacja

     - Ukończ [samouczek: Środowisko instalacji i obszar roboczy,](tutorial-1st-experiment-sdk-setup.md) aby utworzyć serwer dedykowany notebooka wstępnie załadowany za pomocą zestawu SDK i przykładowego repozytorium.
    - W przykładowym folderze uczenia głębokiego na serwerze notesu znajdź ukończony i rozwinięty notes, przechodząc do tego katalogu: **how-to-use-azureml > ml-frameworks > tensorflow > deployment > train-hyperparameter-tune-deploy-with-tensorflow** folder. 
 
 - Twój własny serwer notebooka Jupyter

    - [Zainstaluj zestaw SDK usługi Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).
    - [Tworzenie pliku konfiguracji obszaru roboczego](how-to-configure-environment.md#workspace).
    - [Pobierz przykładowe pliki](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/tensorflow/deployment/train-hyperparameter-tune-deploy-with-tensorflow) `mnist-tf.py` skryptów i`utils.py`
     
    Ukończoną wersję tego przewodnika można również znaleźć ukończoną wersję tego przewodnika [w usłudze](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/tensorflow/deployment/train-hyperparameter-tune-deploy-with-tensorflow/train-hyperparameter-tune-deploy-with-tensorflow.ipynb) GitHub. Notes zawiera rozszerzone sekcje obejmujące inteligentne dostrajanie hiperparametryczne, wdrażanie modelu i widżety notesu.

## <a name="set-up-the-experiment"></a>Konfigurowanie eksperymentu

W tej sekcji konfiguruje eksperyment szkoleniowy, ładując wymagane pakiety języka Python, inicjując obszar roboczy, tworząc eksperyment i przekazując dane szkoleniowe i skrypty szkoleniowe.

### <a name="import-packages"></a>Importowanie pakietów

Najpierw zaimportuj niezbędne biblioteki Języka Python.

```Python
import os
import urllib
import shutil
import azureml

from azureml.core import Experiment
from azureml.core import Workspace, Run

from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException
from azureml.train.dnn import TensorFlow
```

### <a name="initialize-a-workspace"></a>Inicjowanie obszaru roboczego

[Obszar roboczy usługi Azure Machine Learning](concept-workspace.md) jest zasobem najwyższego poziomu dla usługi. Zapewnia scentralizowane miejsce do pracy ze wszystkimi artefaktami, które tworzysz. W zestaw SDK języka Python można uzyskać dostęp do [`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) artefaktów obszaru roboczego, tworząc obiekt.

Utwórz obiekt obszaru `config.json` roboczego z pliku utworzonego w [sekcji wymagania wstępne](#prerequisites).

```Python
ws = Workspace.from_config()
```

### <a name="create-a-deep-learning-experiment"></a>Tworzenie eksperymentu głębokiego uczenia się

Utwórz eksperyment i folder do przechowywania skryptów szkoleniowych. W tym przykładzie utwórz eksperyment o nazwie "tf-mnist".

```Python
script_folder = './tf-mnist'
os.makedirs(script_folder, exist_ok=True)

exp = Experiment(workspace=ws, name='tf-mnist')
```

### <a name="create-a-file-dataset"></a>Tworzenie zestawu danych plików

Obiekt `FileDataset` odwołuje się do jednego lub wielu plików w magazynie danych obszaru roboczego lub publicznych adresach URL. Pliki mogą być w dowolnym formacie, a klasa zapewnia możliwość pobierania lub instalowania plików do obliczeń. Tworząc program `FileDataset`, można utworzyć odwołanie do lokalizacji źródła danych. Jeśli zastosowano żadnych przekształceń do zestawu danych, będą one przechowywane w zestawie danych, jak również. Dane pozostają w istniejącej lokalizacji, więc nie ponosi żadnych dodatkowych kosztów magazynowania. Aby uzyskać więcej informacji, `Dataset` zapoznaj się z [instrukcjami](https://docs.microsoft.com/azure/machine-learning/how-to-create-register-datasets) na opakowaniu.

```python
from azureml.core.dataset import Dataset

web_paths = [
            'http://yann.lecun.com/exdb/mnist/train-images-idx3-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/train-labels-idx1-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/t10k-labels-idx1-ubyte.gz'
            ]
dataset = Dataset.File.from_files(path=web_paths)
```

Użyj `register()` tej metody, aby zarejestrować zestaw danych w obszarze roboczym, aby mogły być udostępniane innym, ponownie używane w różnych eksperymentach i określane według nazwy w skrypcie szkoleniowym.

```python
dataset = dataset.register(workspace=ws,
                           name='mnist dataset',
                           description='training and test dataset',
                           create_new_version=True)

# list the files referenced by dataset
dataset.to_path()
```

## <a name="create-a-compute-target"></a>Tworzenie celu obliczeniowego

Utwórz cel obliczeniowy dla zadania TensorFlow do uruchomienia. W tym przykładzie utwórz klaster obliczeniowy usługi Azure Machine Learning z obsługą procesora GPU.

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

Aby uzyskać więcej informacji na temat obiektów docelowych obliczeń, zobacz, co jest artykuł [docelowy obliczeń.](concept-compute-target.md)

## <a name="create-a-tensorflow-estimator"></a>Tworzenie estymatora TensorFlow

[Estymator TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) zapewnia prosty sposób uruchamiania zadania szkoleniowego TensorFlow na celu obliczeniowym.

Estymator TensorFlow jest implementowany [`estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) za pośrednictwem klasy ogólnej, która może służyć do obsługi dowolnej struktury. Aby uzyskać więcej informacji na temat modeli szkoleniowych przy użyciu ogólnego estymatora, zobacz [modele pociągów z usługą Azure Machine Learning przy użyciu estymatora](how-to-train-ml-models.md)

Jeśli skrypt szkolenia wymaga dodatkowych pakietów pip lub conda do uruchomienia, można mieć pakiety zainstalowane `pip_packages` `conda_packages` na wynikowy obraz platformy Docker, przekazując ich nazwy przez argumenty i.

```python
script_params = {
    '--data-folder': dataset.as_named_input('mnist').as_mount(),
    '--batch-size': 50,
    '--first-layer-neurons': 300,
    '--second-layer-neurons': 100,
    '--learning-rate': 0.01
}

est = TensorFlow(source_directory=script_folder,
                 entry_script='tf_mnist.py',
                 script_params=script_params,
                 compute_target=compute_target,
                 use_gpu=True,
                 pip_packages=['azureml-dataprep[pandas,fuse]'])
```

> [!TIP]
> Obsługa **Tensorflow 2.0** została dodana do klasy estymatora Tensorflow. Zobacz [wpis w blogu](https://azure.microsoft.com/blog/tensorflow-2-0-on-azure-fine-tuning-bert-for-question-tagging/), aby uzyskać więcej informacji.

Aby uzyskać więcej informacji na temat dostosowywania środowiska języka Python, zobacz [Tworzenie środowisk szkoleniowych i wdrożeniowych oraz zarządzanie nimi.](how-to-use-environments.md) 

## <a name="submit-a-run"></a>Prześlij bieg

[Run Obiekt](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) udostępnia interfejs do historii uruchamiania, gdy zadanie jest uruchomiony i po jego zakończeniu.

```Python
run = exp.submit(est)
run.wait_for_completion(show_output=True)
```

W miarę wykonywania przebiegu przechodzi przez następujące etapy:

- **Przygotowanie:** Obraz platformy Docker jest tworzony zgodnie z estymatorem TensorFlow. Obraz jest przekazyany do rejestru kontenerów obszaru roboczego i buforowany dla późniejszych uruchomień. Dzienniki są również przesyłane strumieniowo do historii uruchamiania i można je przeglądać w celu monitorowania postępu.

- **Skalowanie:** Klaster próbuje skalować w górę, jeśli klaster AI partii wymaga więcej węzłów do wykonania uruchomienia niż są obecnie dostępne.

- **Uruchomione**: Wszystkie skrypty w folderze skryptów są przekazywane do obiektu docelowego obliczeń, magazyny danych są montowane lub kopiowane, a entry_script jest wykonywana. Wyjścia z folderu stdout i ./logs są przesyłane strumieniowo do historii uruchamiania i mogą być używane do monitorowania przebiegu.

- **Post-Processing:** Folder ./outputs przebiegu jest kopiowany do historii uruchamiania.

## <a name="register-or-download-a-model"></a>Zarejestruj lub pobierz model

Po przeszkoleniu modelu można go zarejestrować w obszarze roboczym. Rejestracja modelu umożliwia przechowywanie i przechowywanie modeli w obszarze roboczym w celu uproszczenia [zarządzania modelami i wdrażania.](concept-model-management-and-deployment.md) Poprzez określenie `model_framework`parametrów `model_framework_version`, `resource_configuration`i , bez kodu wdrożenia modelu staje się dostępna. Dzięki temu można bezpośrednio wdrożyć model jako usługę sieci web `ResourceConfiguration` z zarejestrowanego modelu, a obiekt definiuje zasób obliczeniowy dla usługi sieci web.

```Python
from azureml.core import Model
from azureml.core.resource_configuration import ResourceConfiguration

model = run.register_model(model_name='tf-dnn-mnist', 
                           model_path='outputs/model',
                           model_framework=Model.Framework.TENSORFLOW,
                           model_framework_version='1.13.0',
                           resource_configuration=ResourceConfiguration(cpu=1, memory_in_gb=0.5))
```

Można również pobrać lokalną kopię modelu przy użyciu Run obiektu. W skrypcie `mnist-tf.py`szkolenia obiekt wygaszacz TensorFlow utrzymuje model do folderu lokalnego (lokalnego do obiektu docelowego obliczeń). Można użyć Uruchom obiektu, aby pobrać kopię.

```Python
# Create a model folder in the current directory
os.makedirs('./model', exist_ok=True)

for f in run.get_file_names():
    if f.startswith('outputs/model'):
        output_file_path = os.path.join('./model', f.split('/')[-1])
        print('Downloading from {} to {} ...'.format(f, output_file_path))
        run.download_file(name=f, output_file_path=output_file_path)
```

## <a name="distributed-training"></a>Trenowanie rozproszone

Estymator [`TensorFlow`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) obsługuje również szkolenia rozproszone w klastrach procesorów CPU i GPU. Można łatwo uruchomić rozproszonych zadań TensorFlow i usługi Azure Machine Learning będzie zarządzać aranżacji dla Ciebie.

Usługa Azure Machine Learning obsługuje dwie metody szkolenia rozproszonego w tensorFlow:

- Szkolenia [rozproszone oparte na MPI](https://www.open-mpi.org/) z wykorzystaniem struktury [Horovod](https://github.com/uber/horovod)
- Natywna [rozproszona tensorflow](https://www.tensorflow.org/deploy/distributed) przy użyciu metody serwera parametrów

### <a name="horovod"></a>Horovod

[Horovod](https://github.com/uber/horovod) to struktura open-source dla szkoleń rozproszonych opracowanych przez Ubera. Oferuje łatwą ścieżkę do rozproszonych zadań GPU TensorFlow.

Aby użyć Horovod, [`MpiConfiguration`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration?view=azure-ml-py) należy `distributed_training` określić obiekt dla parametru w konstruktorze TensorFlow. Ten parametr gwarantuje, że biblioteka Horovod jest zainstalowana do użycia w skrypcie treningowym.

```Python
from azureml.core.runconfig import MpiConfiguration
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
                      use_gpu=True,
                      pip_packages=['azureml-dataprep[pandas,fuse]'])
```

### <a name="parameter-server"></a>Serwer parametrów

Można również uruchomić [macierzysty rozproszony TensorFlow](https://www.tensorflow.org/deploy/distributed), który używa modelu serwera parametrów. W tej metodzie można trenować w klastrze serwerów parametrów i pracowników. Pracownicy obliczają gradienty podczas szkolenia, podczas gdy serwery parametrów agregują gradienty.

Aby użyć metody serwera parametrów, należy określić [`TensorflowConfiguration`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.tensorflowconfiguration?view=azure-ml-py) obiekt dla parametru `distributed_training` w konstruktorze TensorFlow.

```Python
from azureml.train.dnn import TensorFlow

distributed_training = TensorflowConfiguration()
distributed_training.worker_count = 2

# Tensorflow constructor
tf_est= TensorFlow(source_directory=project_folder,
                      compute_target=compute_target,
                      script_params=script_params,
                      entry_script='script.py',
                      node_count=2,
                      process_count_per_node=1,
                      distributed_training=distributed_training,
                      use_gpu=True,
                      pip_packages=['azureml-dataprep[pandas,fuse]'])

# submit the TensorFlow job
run = exp.submit(tf_est)
```

#### <a name="define-cluster-specifications-in-tf_config"></a>Definiowanie specyfikacji klastra w "TF_CONFIG"

Potrzebne są również adresy sieciowe i [`tf.train.ClusterSpec`](https://www.tensorflow.org/api_docs/python/tf/train/ClusterSpec)porty klastra `TF_CONFIG` dla , więc usługa Azure Machine Learning ustawia zmienną środowiskową.

Zmienna środowiskowa `TF_CONFIG` jest ciągiem JSON. Oto przykład zmiennej dla serwera parametrów:

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

W przypadku interfejsu API [`tf.estimator`](https://www.tensorflow.org/api_docs/python/tf/estimator) wysokiego poziomu tensorflow `TF_CONFIG` analizuje zmienną i tworzy specyfikację klastra.

W przypadku interfejsów API niższego poziomu podstawowych tensorflow `TF_CONFIG` do szkolenia, przesiemaj zmienną i skompiluj `tf.train.ClusterSpec` w kodzie szkolenia.

```Python
import os, json
import tensorflow as tf

tf_config = os.environ.get('TF_CONFIG')
if not tf_config or tf_config == "":
    raise ValueError("TF_CONFIG not found.")
tf_config_json = json.loads(tf_config)
cluster_spec = tf.train.ClusterSpec(cluster)

```

## <a name="deploy-a-tensorflow-model"></a>Wdrażanie modelu TensorFlow

Model, który właśnie zarejestrowałeś, można wdrożyć dokładnie w taki sam sposób, jak każdy inny zarejestrowany model w usłudze Azure Machine Learning, niezależnie od tego, który estymator został użyty do szkolenia. Instrukcje wdrażania zawiera sekcję dotyczącą rejestrowania modeli, ale można pominąć bezpośrednio do [tworzenia miejsca docelowego obliczeń](how-to-deploy-and-where.md#choose-a-compute-target) dla wdrożenia, ponieważ masz już zarejestrowany model.

## <a name="preview-no-code-model-deployment"></a>(Wersja zapoznawcza) Wdrożenie modelu bez kodu

Zamiast tradycyjnej trasy wdrażania można również użyć funkcji wdrażania bez kodu (wersja zapoznawcza) dla tensorflow. Rejestrując model, jak pokazano `model_framework`powyżej `model_framework_version`z `resource_configuration` , i parametrów, można po prostu użyć funkcji statycznej `deploy()` do wdrożenia modelu.

```python
service = Model.deploy(ws, "tensorflow-web-service", [model])
```

Pełne [instrukcje dotyczące](how-to-deploy-and-where.md) wdrażania w usłudze Azure Machine Learning w większym stopniu.

## <a name="next-steps"></a>Następne kroki

W tym artykule przeszkolono i zarejestrowałeś model TensorFlow i dowiedziałeś się o opcjach wdrażania. Zobacz te inne artykuły, aby dowiedzieć się więcej o usłudze Azure Machine Learning.

* [Śledzenie danych o przebiegu podczas treningu](how-to-track-experiments.md)
* [Dostrajanie hiperparametrów](how-to-tune-hyperparameters.md)
* [Architektura referencyjna dla rozproszonych szkoleń głębokiego uczenia na platformie Azure](/azure/architecture/reference-architectures/ai/training-deep-learning)
