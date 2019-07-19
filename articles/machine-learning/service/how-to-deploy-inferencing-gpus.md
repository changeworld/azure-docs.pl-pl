---
title: Wdrażanie modelu na potrzeby wnioskowania z procesorem GPU
titleSuffix: Azure Machine Learning service
description: W tym artykule przedstawiono sposób użycia usługi Azure Machine Learning do wdrożenia modelu uczenia głębokiego z obsługą procesora GPU jako usługi sieci Web. żądania wnioskowania dotyczące usługi i oceny.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: vaidyas
author: csteegz
ms.reviewer: larryfr
ms.date: 06/01/2019
ms.openlocfilehash: eeb1bc35e0438a7e99ea5ed8284f0c8611108da0
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/18/2019
ms.locfileid: "68326986"
---
# <a name="deploy-a-deep-learning-model-for-inference-with-gpu"></a>Wdrażanie modelu uczenia głębokiego na potrzeby wnioskowania z procesorem GPU

W tym artykule przedstawiono sposób użycia usługi Azure Machine Learning do wdrożenia modelu uczenia głębokiego z obsługą procesora GPU jako usługi sieci Web.

Wdróż model w klastrze usługi Azure Kubernetes Service (AKS) w celu wykonania inferencing z włączonymi procesorami GPU. Inferencing, lub ocenianie modelu, to faza, w której wdrożony model jest używany do prognozowania. Korzystanie z procesorów GPU zamiast procesorów CPU oferuje zalety wydajności na wysoce działania równoległego obliczeń.

Mimo że ten przykład korzysta z modelu TensorFlow, można zastosować następujące kroki do dowolnej platformy uczenia maszynowego, która obsługuje procesory GPU, wprowadzając małe zmiany w pliku oceniania i pliku środowiska. 

W tym artykule wykonasz następujące kroki:

* Tworzenie klastra AKS z obsługą procesora GPU
* Wdrażanie modelu GPU Tensorflow
* Wydaj przykładowe zapytanie do wdrożonego modelu

## <a name="prerequisites"></a>Wymagania wstępne

* Obszar roboczy usług Azure Machine Learning Services.
* Dystrybucji języka Python.
* Zarejestrowany model Tensorflow zapisany.
    * Aby dowiedzieć się, jak zarejestrować modele, zobacz [Wdrażanie modeli](../service/how-to-deploy-and-where.md#registermodel).

Możesz wykonać jedną z części tej serii, [Jak szkolić model TensorFlow](how-to-train-tensorflow.md), aby spełnić niezbędne wymagania wstępne.

## <a name="provision-an-aks-cluster-with-gpus"></a>Inicjowanie obsługi administracyjnej klastra AKS przy użyciu procesorów GPU

Platforma Azure ma wiele różnych opcji procesora GPU. Można użyć dowolnego z nich dla inferencing. Zapoznaj [się z listą maszyn wirtualnych serii N](https://azure.microsoft.com/pricing/details/virtual-machines/linux/#n-series) , aby zapoznać się z pełnym podziałem możliwości i kosztów.

Aby uzyskać więcej informacji na temat korzystania z usługi AKS z usługą Azure Machine Learning, zobacz artykuł [jak wdrożyć i gdzie](../service/how-to-deploy-and-where.md#deploy-aks).

```Python
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
> Na platformie Azure naliczane są opłaty, o ile jest inicjowany klaster AKS. Upewnij się, że klaster AKS został usunięty po zakończeniu pracy z nim.

## <a name="write-the-entry-script"></a>Napisz skrypt wpisu

Zapisz następujący kod w katalogu roboczym jako `score.py`. Ten plik przedstawia obrazy, które są wysyłane do usługi. Ładuje zapisany model TensorFlow, przekazuje obraz wejściowy do sesji TensorFlow na każdym żądaniu POST, a następnie zwraca wynikowe wyniki. Inne struktury inferencing wymagają różnych plików oceniania.

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
## <a name="define-the-conda-environment"></a>Zdefiniuj środowisko Conda

Utwórz plik środowiska Conda o nazwie `myenv.yml` , aby określić zależności dla usługi. Ważne jest, aby określić, że `tensorflow-gpu` używasz do osiągnięcia przyspieszonej wydajności.

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

## <a name="define-the-gpu-inferenceconfig-class"></a>Definiowanie klasy InferenceConfig procesora GPU

`InferenceConfig` Utwórz obiekt, który umożliwia procesory GPU i gwarantuje, że cuda zostanie zainstalowany przy użyciu obrazu platformy Docker.

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

Wdróż model w klastrze AKS i poczekaj na utworzenie usługi.

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
> Usługa Azure Machine Learning nie będzie wdrażać modelu z `InferenceConfig` obiektem, który oczekuje, że procesor GPU zostanie włączony w klastrze, który nie ma procesora GPU.

Aby uzyskać więcej informacji, zobacz [model Class](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py).

## <a name="issue-a-sample-query-to-your-model"></a>Wydaj przykładowe zapytanie do modelu

Wyślij zapytanie testowe do wdrożonego modelu. Po wysłaniu obrazu JPEG do modelu ocenia obraz. Poniższy przykład kodu używa zewnętrznej funkcji narzędziowej do ładowania obrazów. Odpowiedni kod można znaleźć w przykładowej PIR [TensorFlow w witrynie GitHub](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-tensorflow/utils.py). 

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
> Aby zminimalizować opóźnienia i zoptymalizować przepływność, upewnij się, że klient znajduje się w tym samym regionie świadczenia usługi Azure co punkt końcowy. W tym przykładzie interfejsy API są tworzone w regionie usługi Azure Wschodnie stany USA.

## <a name="clean-up-the-resources"></a>Czyszczenie zasobów

Jeśli klaster AKS został utworzony w ramach tego przykładu, Usuń zasoby po zakończeniu.

> [!IMPORTANT]
> Platforma Azure jest rozliczana na podstawie tego, jak długo wdrożono klaster AKS. Pamiętaj, aby wyczyścić go po zakończeniu pracy z nim.

```python
aks_service.delete()
aks_target.delete()
```

## <a name="next-steps"></a>Kolejne kroki

* [Wdróż model na FPGA](../service/how-to-deploy-fpga-web-service.md)
* [Wdróż model z ONNX](../service/concept-onnx.md#deploy-onnx-models-in-azure)
* [Uczenie modeli DNN Tensorflow](../service/how-to-train-tensorflow.md)
