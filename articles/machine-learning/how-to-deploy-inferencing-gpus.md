---
title: Wdrażanie modelu do wnioskowania z procesorem GPU
titleSuffix: Azure Machine Learning
description: W tym artykule opisano, jak używać usługi Azure Machine Learning do wdrażania modelu uczenia głębokiego tensorflow z obsługą procesora GPU jako usługi sieci web.service i żądania wnioskowania.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: vaidyas
author: csteegz
ms.reviewer: larryfr
ms.date: 03/05/2020
ms.openlocfilehash: b0fd537d1930e7c9d5f7a33f56ec5d00b1556562
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78398335"
---
# <a name="deploy-a-deep-learning-model-for-inference-with-gpu"></a>Wdrażanie modelu uczenia głębokiego w celu wnioskowania z procesorem graficznym
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

W tym artykule dowiesz się, jak używać usługi Azure Machine Learning do wdrażania modelu obsługującego procesor GPU jako usługi sieci web. Informacje zawarte w tym artykule jest oparty na wdrażaniu modelu w usłudze Azure Kubernetes Service (AKS). Klaster AKS udostępnia zasób GPU, który jest używany przez model do wnioskowania.

Wnioskowanie lub ocenianie modelu jest fazą, w której wdrożony model jest używany do przewidywania. Korzystanie z procesorów GPU zamiast procesorów oferuje korzyści wydajności na obliczeniach wysoce równoległości.

> [!IMPORTANT]
> W przypadku wdrożeń usług sieci web wnioskowanie o procesorze GPU jest obsługiwane tylko w usłudze Azure Kubernetes. W przypadku wnioskowania przy użyciu __potoku uczenia maszynowego__procesory GPU są obsługiwane tylko w usłudze Azure Machine Learning Compute. Aby uzyskać więcej informacji na temat korzystania z potoków ml, zobacz [Uruchamianie prognozsady wsadowych](how-to-use-parallel-run-step.md). 

> [!TIP]
> Mimo że fragmenty kodu w tym artykule używają modelu TensorFlow, można zastosować informacje do dowolnej struktury uczenia maszynowego, która obsługuje procesory GPU.

> [!NOTE]
> Informacje zawarte w tym artykule opiera się na informacjach w [artykule Jak wdrożyć w usłudze Azure Kubernetes.](how-to-deploy-azure-kubernetes-service.md) Jeśli ten artykuł zazwyczaj obejmuje wdrożenia do usługi AKS, ten artykuł obejmuje wdrożenia specyficzne dla procesora GPU.

## <a name="prerequisites"></a>Wymagania wstępne

* Obszar roboczy usługi Azure Machine Learning. Aby uzyskać więcej informacji, zobacz [Tworzenie obszaru roboczego usługi Azure Machine Learning](how-to-manage-workspace.md).

* Środowisko programistyczne języka Python z zainstalowanym zestawem SDK usługi Azure Machine Learning. Aby uzyskać więcej informacji, zobacz [Zestaw SDK usługi Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).  

* Zarejestrowany model, który używa procesora GPU.

    * Aby dowiedzieć się, jak zarejestrować modele, zobacz [Wdrażanie modeli](how-to-deploy-and-where.md#registermodel).

    * Aby utworzyć i zarejestrować model Tensorflow użyty do utworzenia tego dokumentu, zobacz [Jak wyszkolić model TensorFlow](how-to-train-tensorflow.md).

* Ogólne zrozumienie [jak i gdzie wdrożyć modele](how-to-deploy-and-where.md).

## <a name="connect-to-your-workspace"></a>Nawiązywanie połączenia z obszarem roboczym

Aby połączyć się z istniejącym obszarem roboczym, użyj następującego kodu:

> [!IMPORTANT]
> Ten fragment kodu oczekuje, że konfiguracja obszaru roboczego zostanie zapisana w bieżącym katalogu lub jego łzowym. Aby uzyskać więcej informacji na temat tworzenia obszaru roboczego, zobacz [Tworzenie obszarów roboczych usługi Azure Machine Learning i zarządzanie nimi](how-to-manage-workspace.md).   Aby uzyskać więcej informacji na temat zapisywania konfiguracji do pliku, zobacz [Tworzenie pliku konfiguracji obszaru roboczego](how-to-configure-environment.md#workspace).

```python
from azureml.core import Workspace

# Connect to the workspace
ws = Workspace.from_config()
```

## <a name="create-a-kubernetes-cluster-with-gpus"></a>Tworzenie klastra Kubernetes z procesorami GPU

Usługa Azure Kubernetes zapewnia wiele różnych opcji procesora GPU. Można użyć dowolnego z nich do wnioskowania modelu. Zobacz [listę maszyn wirtualnych serii N, aby](https://azure.microsoft.com/pricing/details/virtual-machines/linux/#n-series) uzyskać pełny podział możliwości i kosztów.

Poniższy kod pokazuje, jak utworzyć nowy klaster AKS dla obszaru roboczego:

```python
from azureml.core.compute import ComputeTarget, AksCompute
from azureml.exceptions import ComputeTargetException

# Choose a name for your cluster
aks_name = "aks-gpu"

# Check to see if the cluster already exists
try:
    aks_target = ComputeTarget(workspace=ws, name=aks_name)
    print('Found existing compute target')
except ComputeTargetException:
    print('Creating a new compute target...')
    # Provision AKS cluster with GPU machine
    prov_config = AksCompute.provisioning_configuration(vm_size="Standard_NC6")

    # Create the cluster
    aks_target = ComputeTarget.create(
        workspace=ws, name=aks_name, provisioning_configuration=prov_config
    )

    aks_target.wait_for_completion(show_output=True)
```

> [!IMPORTANT]
> Platforma Azure będzie rozliczać cię tak długo, jak istnieje klaster AKS. Upewnij się, że po zakończeniu pracy z nim należy usunąć klaster AKS.

Aby uzyskać więcej informacji na temat korzystania z usługi AKS z usługą Azure Machine Learning, zobacz [Jak wdrożyć w usłudze Azure Kubernetes.](how-to-deploy-azure-kubernetes-service.md)

## <a name="write-the-entry-script"></a>Napisz skrypt wpisu

Skrypt wpisu odbiera dane przesłane do usługi sieci web, przekazuje je do modelu i zwraca wyniki oceniania. Poniższy skrypt ładuje model Tensorflow podczas uruchamiania, a następnie używa modelu do oceniania danych.

> [!TIP]
> Skrypt wejściowy jest specyficzny dla modelu. Na przykład skrypt musi znać strukturę do użycia z modelem, formaty danych itp.

```python
import json
import numpy as np
import os
import tensorflow as tf

from azureml.core.model import Model


def init():
    global X, output, sess
    tf.reset_default_graph()
    model_root = os.getenv('AZUREML_MODEL_DIR')
    # the name of the folder in which to look for tensorflow model files
    tf_model_folder = 'model'
    saver = tf.train.import_meta_graph(
        os.path.join(model_root, tf_model_folder, 'mnist-tf.model.meta'))
    X = tf.get_default_graph().get_tensor_by_name("network/X:0")
    output = tf.get_default_graph().get_tensor_by_name("network/output/MatMul:0")

    sess = tf.Session()
    saver.restore(sess, os.path.join(model_root, tf_model_folder, 'mnist-tf.model'))


def run(raw_data):
    data = np.array(json.loads(raw_data)['data'])
    # make prediction
    out = output.eval(session=sess, feed_dict={X: data})
    y_hat = np.argmax(out, axis=1)
    return y_hat.tolist()
```

Ten plik `score.py`nosi nazwę . Aby uzyskać więcej informacji na temat skryptów wprowadzania, zobacz [Jak i gdzie wdrożyć](how-to-deploy-and-where.md).

## <a name="define-the-conda-environment"></a>Definiowanie środowiska conda

Plik środowiska conda określa zależności dla usługi. Zawiera zależności wymagane przez model i skrypt wpisu. Należy pamiętać, że należy wskazać azureml-defaults z verion >= 1.0.45 jako zależności pip, ponieważ zawiera funkcje potrzebne do obsługi modelu jako usługi sieci web. Następujący YAML definiuje środowisko dla modelu Tensorflow. Określa `tensorflow-gpu`, który będzie korzystać z GPU używane w tym wdrożeniu:

```yaml
name: project_environment
dependencies:
  # The python interpreter version.
  # Currently Azure ML only supports 3.5.2 and later.
- python=3.6.2

- pip:
  # You must list azureml-defaults as a pip dependency
  - azureml-defaults>=1.0.45
- numpy
- tensorflow-gpu=1.12
channels:
- conda-forge
```

W tym przykładzie plik `myenv.yml`jest zapisywany jako .

## <a name="define-the-deployment-configuration"></a>Definiowanie konfiguracji wdrażania

Konfiguracja wdrożenia definiuje środowisko usługi Azure Kubernetes używane do uruchamiania usługi sieci web:

```python
from azureml.core.webservice import AksWebservice

gpu_aks_config = AksWebservice.deploy_configuration(autoscale_enabled=False,
                                                    num_replicas=3,
                                                    cpu_cores=2,
                                                    memory_gb=4)
```

Aby uzyskać więcej informacji, zobacz dokumentację referencyjną dla [pliku AksService.deploy_configuration](/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py#deploy-configuration-autoscale-enabled-none--autoscale-min-replicas-none--autoscale-max-replicas-none--autoscale-refresh-seconds-none--autoscale-target-utilization-none--collect-model-data-none--auth-enabled-none--cpu-cores-none--memory-gb-none--enable-app-insights-none--scoring-timeout-ms-none--replica-max-concurrent-requests-none--max-request-wait-time-none--num-replicas-none--primary-key-none--secondary-key-none--tags-none--properties-none--description-none--gpu-cores-none--period-seconds-none--initial-delay-seconds-none--timeout-seconds-none--success-threshold-none--failure-threshold-none--namespace-none--token-auth-enabled-none--compute-target-name-none-).

## <a name="define-the-inference-configuration"></a>Definiowanie konfiguracji wnioskowania

Konfiguracja wnioskowania wskazuje na skrypt wejścia i obiekt środowiska, który używa obrazu docker z obsługą procesora GPU. Należy pamiętać, że plik YAML używany do definicji środowiska musi zawierać listę azureml-defaults z wersją >= 1.0.45 jako zależność pipsa, ponieważ zawiera funkcje potrzebne do obsługi modelu jako usługi sieci web.

```python
from azureml.core.model import InferenceConfig
from azureml.core.environment import Environment, DEFAULT_GPU_IMAGE

myenv = Environment.from_conda_specification(name="myenv", file_path="myenv.yml")
myenv.docker.base_image = DEFAULT_GPU_IMAGE
inference_config = InferenceConfig(entry_script="score.py", environment=myenv)
```

Aby uzyskać więcej informacji na temat środowisk, zobacz [Tworzenie środowisk szkoleniowych i wdrożeniowych oraz zarządzanie nimi.](how-to-use-environments.md)
Aby uzyskać więcej informacji, zobacz dokumentację referencyjną dla [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py).

## <a name="deploy-the-model"></a>Wdrażanie modelu

Wdrażanie modelu w klastrze usługi AKS i poczekaj na jego utworzenie usługi.

```python
from azureml.core.model import Model

# Name of the web service that is deployed
aks_service_name = 'aks-dnn-mnist'
# Get the registerd model
model = Model(ws, "tf-dnn-mnist")
# Deploy the model
aks_service = Model.deploy(ws,
                           models=[model],
                           inference_config=inference_config,
                           deployment_config=gpu_aks_config,
                           deployment_target=aks_target,
                           name=aks_service_name)

aks_service.wait_for_deployment(show_output=True)
print(aks_service.state)
```

> [!NOTE]
> Jeśli `InferenceConfig` obiekt `enable_gpu=True`ma , `deployment_target` parametr musi odwoływać się do klastra, który zapewnia gpu. W przeciwnym razie wdrożenie zakończy się niepowodzeniem.

Aby uzyskać więcej informacji, zobacz dokumentację referencyjną dla [Modelu](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py).

## <a name="issue-a-sample-query-to-your-service"></a>Wystawianie przykładowej kwerendy do usługi

Wyślij zapytanie testowe do wdrożonego modelu. Podczas wysyłania obrazu jpeg do modelu, to wyniki obrazu. Poniższy przykładowy kod pobiera dane testowe, a następnie wybiera losowy obraz testowy do wysłania do usługi.

```python
# Used to test your webservice
import os
import urllib
import gzip
import numpy as np
import struct
import requests

# load compressed MNIST gz files and return numpy arrays
def load_data(filename, label=False):
    with gzip.open(filename) as gz:
        struct.unpack('I', gz.read(4))
        n_items = struct.unpack('>I', gz.read(4))
        if not label:
            n_rows = struct.unpack('>I', gz.read(4))[0]
            n_cols = struct.unpack('>I', gz.read(4))[0]
            res = np.frombuffer(gz.read(n_items[0] * n_rows * n_cols), dtype=np.uint8)
            res = res.reshape(n_items[0], n_rows * n_cols)
        else:
            res = np.frombuffer(gz.read(n_items[0]), dtype=np.uint8)
            res = res.reshape(n_items[0], 1)
    return res

# one-hot encode a 1-D array
def one_hot_encode(array, num_of_classes):
    return np.eye(num_of_classes)[array.reshape(-1)]

# Download test data
os.makedirs('./data/mnist', exist_ok=True)
urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz', filename='./data/mnist/test-images.gz')
urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/t10k-labels-idx1-ubyte.gz', filename='./data/mnist/test-labels.gz')

# Load test data from model training
X_test = load_data('./data/mnist/test-images.gz', False) / 255.0
y_test = load_data('./data/mnist/test-labels.gz', True).reshape(-1)

# send a random row from the test set to score
random_index = np.random.randint(0, len(X_test)-1)
input_data = "{\"data\": [" + str(list(X_test[random_index])) + "]}"

api_key = aks_service.get_keys()[0]
headers = {'Content-Type': 'application/json',
           'Authorization': ('Bearer ' + api_key)}
resp = requests.post(aks_service.scoring_uri, input_data, headers=headers)

print("POST to url", aks_service.scoring_uri)
print("label:", y_test[random_index])
print("prediction:", resp.text)
```

Aby uzyskać więcej informacji na temat tworzenia aplikacji klienckiej, zobacz [Tworzenie klienta do korzystania z wdrożonej usługi sieci web](how-to-consume-web-service.md).

## <a name="clean-up-the-resources"></a>Oczyszczanie zasobów

Jeśli w tym przykładzie utworzono klaster AKS, usuń zasoby po zakończeniu.

> [!IMPORTANT]
> Platforma Azure rozlicza się z tego, jak długo jest wdrażany klaster AKS. Upewnij się, aby oczyścić go po zakończeniu z nim.

```python
aks_service.delete()
aks_target.delete()
```

## <a name="next-steps"></a>Następne kroki

* [Wdrażanie modelu w fpga](how-to-deploy-fpga-web-service.md)
* [Wdrażanie modelu za pomocą ONNX](concept-onnx.md#deploy-onnx-models-in-azure)
* [Trenuj modele Tensorflow DNN](how-to-train-tensorflow.md)
