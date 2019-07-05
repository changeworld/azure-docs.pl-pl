---
title: Wdrażanie modelu na potrzeby wnioskowania przy użyciu procesora GPU
titleSuffix: Azure Machine Learning service
description: Ten artykuł nauczy Cię sposobu wdrożenia obsługujące procesor GPU Tensorflow głębokiego uczenia modelu w postaci service.service sieci web i ocena wnioskowania żądań przy użyciu usługi Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: vaidyas
author: csteegz
ms.reviewer: larryfr
ms.date: 06/01/2019
ms.openlocfilehash: 8086d059913cc61bff0bca31681368bea6d76777
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/03/2019
ms.locfileid: "67543802"
---
# <a name="deploy-a-deep-learning-model-for-inference-with-gpu"></a>Wdrażanie modelu uczenia głębokiego, na potrzeby wnioskowania przy użyciu procesora GPU

Ten artykuł nauczy Cię sposobu wdrażania z włączonymi procesorami GPU Tensorflow głębokiego uczenia modelu w postaci usługi sieci web przy użyciu usługi Azure Machine Learning.

Model jest wdrażany klaster Azure Kubernetes Service (AKS) w celu wnioskowania z włączonymi procesorami GPU. Wnioskowania lub oceniania modelu, jest fazy, gdzie wdrożony model służy do prognozowania. Przy użyciu procesorów GPU zamiast procesorów oferty korzyści związanych z wydajnością na obliczeń o wysokiej równoległego.

Mimo że w tym przykładzie korzysta z modelu TensorFlow, poniższe kroki można zastosować do dowolnej maszyny uczenia framework, który obsługuje procesory GPU wprowadzając niewielkich zmian w pliku oceniania i plikiem środowiska. 

W tym artykule wykonasz następujące kroki:

* Tworzenie klastra AKS z włączonymi procesorami GPU
* Wdrażanie modelu Tensorflow procesora GPU
* Wydawanie przykładowego zapytania do wdrożonego modelu

## <a name="prerequisites"></a>Wymagania wstępne

* Obszar roboczy usługi Azure Machine Learning.
* Dystrybucja języka Python.
* Zarejestrowane Tensorflow zapisać modelu.
    * Aby dowiedzieć się, jak zarejestrować modeli, zobacz [wdrażanie modeli](../service/how-to-deploy-and-where.md#registermodel).

Możesz wykonać część pierwsza to seria instruktażowych [sposobu uczenia modelu TensorFlow](how-to-train-tensorflow.md), aby spełnić wymagania wstępne.

## <a name="provision-an-aks-cluster-with-gpus"></a>Zainicjuj obsługę klastra usługi AKS za pomocą procesorów GPU

Platforma Azure oferuje wiele różnych opcji procesora GPU. Służy którąkolwiek z nich do wnioskowania. Zobacz [listę maszyn wirtualnych serii N](https://azure.microsoft.com/pricing/details/virtual-machines/linux/#n-series) pełną podziale kosztów i możliwości.

Aby uzyskać więcej informacji na temat korzystania z usługi AKS przy użyciu usługi Azure Machine Learning, zobacz [sposób wdrażania i gdzie](../service/how-to-deploy-and-where.md#deploy-aks).

```Python
# Choose a name for your cluster
aks_name = "aks-gpu"

# Check to see if the cluster already exists
try:
    compute_target = ComputeTarget(workspace=ws, name=aks_name)
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
> Azure naliczy tak długo, jak zainicjowano obsługę administracyjną klastra AKS. Upewnij się, można usunąć klastra usługi AKS, po zakończeniu korzystania z niego.

## <a name="write-the-entry-script"></a>Napisać skrypt wpisu

Zapisz poniższy kod do katalogu roboczego jako `score.py`. Ten plik ocenia obrazów, ponieważ są one wysyłane do usługi. Jego ładowania modelu TensorFlow zapisane, przekazuje obrazu wejściowego do sesji TensorFlow na każde żądanie POST, a następnie zwraca wynikowy wyniki. Inne struktury wnioskowania wymagają różnych plików oceniania.

```python
import json
import numpy as np
import os
import tensorflow as tf

from azureml.core.model import Model

def init():
    global X, output, sess
    tf.reset_default_graph()
    model_root = Model.get_model_path('tf-dnn-mnist')
    saver = tf.train.import_meta_graph(os.path.join(model_root, 'mnist-tf.model.meta'))
    X = tf.get_default_graph().get_tensor_by_name("network/X:0")
    output = tf.get_default_graph().get_tensor_by_name("network/output/MatMul:0")
    
    sess = tf.Session()
    saver.restore(sess, os.path.join(model_root, 'mnist-tf.model'))

def run(raw_data):
    data = np.array(json.loads(raw_data)['data'])
    # make prediction
    out = output.eval(session=sess, feed_dict={X: data})
    y_hat = np.argmax(out, axis=1)
    return y_hat.tolist()

```
## <a name="define-the-conda-environment"></a>Zdefiniuj środowiska conda

Utwórz plik środowiska conda, o nazwie `myenv.yml` Aby określić zależności dla Twojej usługi. Ważne jest, aby określić, że używasz `tensorflow-gpu` osiągnąć wydajność jej jako przyspieszonej.

```yaml
name: project_environment
dependencies:
  # The python interpreter version.
  # Currently Azure ML only supports 3.5.2 and later.
- python=3.6.2

- pip:
  - azureml-defaults==1.0.43.*
- numpy
- tensorflow-gpu=1.12
channels:
- conda-forge
```

## <a name="define-the-gpu-inferenceconfig-class"></a>Zdefiniuj klasę InferenceConfig procesora GPU

Utwórz `InferenceConfig` obiekt, który umożliwia procesorów GPU i gwarantuje, że CUDA jest instalowany z obrazu platformy Docker.

```python
from azureml.core.model import Model
from azureml.core.model import InferenceConfig

aks_service_name ='aks-dnn-mnist'
gpu_aks_config = AksWebservice.deploy_configuration(autoscale_enabled = False, 
                                                    num_replicas = 3, 
                                                    cpu_cores=2, 
                                                    memory_gb=4)
model = Model(ws,"tf-dnn-mnist")

inference_config = InferenceConfig(runtime= "python", 
                                   entry_script="score.py",
                                   conda_file="myenv.yml", 
                                   enable_gpu=True)
```

Aby uzyskać więcej informacji, zobacz:

- [Klasa InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py)
- [Klasa AksServiceDeploymentConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aks.aksservicedeploymentconfiguration?view=azure-ml-py)

## <a name="deploy-the-model"></a>Wdrażanie modelu

Wdrażanie modelu w klastrze usługi AKS i poczekaj na utworzenie usługi.

```python
aks_service = Model.deploy(ws,
                           models=[model],
                           inference_config=inference_config, 
                           deployment_config=gpu_aks_config,
                           deployment_target=aks_target,
                           name=aks_service_name)

aks_service.wait_for_deployment(show_output = True)
print(aks_service.state)
```

> [!NOTE]
> Usługa Azure Machine Learning nie zostaną wdrożone model przy użyciu `InferenceConfig` obiektu, który oczekuje, że procesor GPU, można włączyć do klastra, który nie ma procesora GPU.

Aby uzyskać więcej informacji, zobacz [klasa modelu](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py).

## <a name="issue-a-sample-query-to-your-model"></a>Wydawanie przykładowe zapytanie do modelu

Wysłanie zapytania testu do wdrożony model. Obraz jpeg jest wysyłany do modelu, ocenia obrazu. Poniższy przykładowy kod korzysta z funkcji zewnętrznych narzędzi do ładowania obrazów. Możesz znaleźć odpowiedni kod w pir [przykład TensorFlow w witrynie GitHub](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-tensorflow/utils.py). 

```python
# Used to test your webservice
from utils import load_data 

# Load test data from model training
X_test = load_data('./data/mnist/test-images.gz', False) / 255.0
y_test = load_data('./data/mnist/test-labels.gz', True).reshape(-1)

# send a random row from the test set to score
random_index = np.random.randint(0, len(X_test)-1)
input_data = "{\"data\": [" + str(list(X_test[random_index])) + "]}"

api_key = aks_service.get_keys()[0]
headers = {'Content-Type':'application/json', 'Authorization':('Bearer '+ api_key)}
resp = requests.post(aks_service.scoring_uri, input_data, headers=headers)

print("POST to url", aks_service.scoring_uri)
#print("input data:", input_data)
print("label:", y_test[random_index])
print("prediction:", resp.text)
```

> [!IMPORTANT]
> Aby zminimalizować opóźnienie i zoptymalizowania przepływności, upewnij się, że klient znajduje się w tym samym regionie platformy Azure jako punktu końcowego. W tym przykładzie interfejsy API są tworzone w regionie wschodnie stany USA Azure.

## <a name="clean-up-the-resources"></a>Oczyszczanie zasobów

Po zakończeniu korzystania z tego przykładu, należy usunąć swoje zasoby.

> [!IMPORTANT]
> Platformie Azure są naliczane, który jest oparty na jak długo klaster AKS jest wdrażany. Upewnij się wyczyścić, po zakończeniu z nim.

```python
aks_service.delete()
aks_target.delete()
```

## <a name="next-steps"></a>Kolejne kroki

* [Wdrażanie modelu na FPGA](../service/how-to-deploy-fpga-web-service.md)
* [Wdrażanie modelu z ONNX](../service/concept-onnx.md#deploy-onnx-models-in-azure)
* [Szkolenie modeli Tensorflow DNN](../service/how-to-train-tensorflow.md)
