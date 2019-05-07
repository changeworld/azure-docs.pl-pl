---
title: Jak wdrożyć model uczenia głębokiego dla wnioskowania przy użyciu procesora GPU
titleSuffix: Azure Machine Learning service
description: Dowiedz się, jak wdrożyć model uczenia głębokiego jako usługę sieci web, który używa procesora GPU do wnioskowania. W tym artykule modelu Tensorflow zostanie wdrożony do klastra usługi Azure Kubernetes Service. Klaster używa włączonymi procesorami GPU maszyny Wirtualnej do hosta usługi sieci web i wnioskowania wynik żądania.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: vaidyas
author: csteegz
ms.reviewer: larryfr
ms.date: 05/02/2019
ms.openlocfilehash: 5cc0fe0526937245d3ca913afc477f0259e2afd4
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65160653"
---
# <a name="how-to-do-gpu-inferencing"></a>Jak przeprowadzić wnioskowania procesora GPU

Dowiedz się, jak używać wnioskowania procesora GPU dla usługi machine learning model wdrożyć jako usługę sieci web. W tym artykule dowiesz się, jak wdrożyć przykładowy Tensorflow głębokie uczenie modelu przy użyciu usługi Azure Machine Learning. Model jest wdrożony do klastra Azure Kubernetes Service (AKS), korzystającą z włączonymi procesorami GPU maszyny Wirtualnej w celu obsługi usługi. Gdy żądania są wysyłane do usługi, model używa procesora GPU do wykonania wnioskowania.

Procesory GPU oferują za pośrednictwem procesorów korzyści związanych z wydajnością na obliczeń o wysokiej równoległego. Szkolenia oraz wnioskowania głębokiego uczenia modeli (szczególnie w przypadku dużych partii żądań) sprawdzają się doskonale dla procesorów GPU.  

W tym przykładzie opisano, jak wdrożyć model TensorFlow zapisywane do usługi Azure Machine Learning. 

## <a name="goals-and-prerequisites"></a>Cele i wymagania wstępne

Postępuj zgodnie z instrukcjami, aby:
* Utwórz procesora GPU klastra AKS
* Wdrażanie modelu z procesorem GPU Tensorflow

Wymagania wstępne:
* Obszar roboczy usługi Azure Machine Learning
* Python
* Zarejestrowany Tensorflow SavedModel. Aby dowiedzieć się, jak zarejestrować Zobacz modeli [wdrażanie modeli](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-and-where#registermodel)

Ten artykuł jest oparty na [wdrażanie modeli Tensorflow AKS](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/production-deploy-to-aks-gpu/production-deploy-to-aks-gpu.ipynb), TensorFlow, zapisane, który używa modeli i wdraża klaster AKS. Jednak z niewielkie zmiany w pliku oceniania i plikiem środowiska jest zastosowanie na dowolną platformę nauczania maszyny, które obsługują procesorów GPU.  

## <a name="provision-aks-cluster-with-gpus"></a>Aprowizowanie klastra AKS za pomocą procesorów GPU
Platforma Azure oferuje wiele różnych opcji procesora GPU, które mogą służyć do wnioskowania. Zobacz [listy seria N](https://azure.microsoft.com/pricing/details/virtual-machines/linux/#n-series) pełną podziale kosztów i możliwości. 

Aby uzyskać więcej informacji na temat korzystania z usługi AKS przy użyciu usługi Azure Machine Learning, zobacz [jak wdrażać i którym artykuł.](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-and-where#create-a-new-cluster)

```python
# Provision AKS cluster with GPU machine
prov_config = AksCompute.provisioning_configuration(vm_size="Standard_NC6")

# Create the cluster
aks_target = ComputeTarget.create(
    workspace=ws, name=aks_name, provisioning_configuration=prov_config
)

aks_target.wait_for_deployment()
```

> [!IMPORTANT]
> Azure naliczy tak długo, jak zainicjowano obsługę administracyjną klastra AKS. Upewnij się, można usunąć klastra usługi AKS, po zakończeniu korzystania z niego.


## <a name="write-entry-script"></a>Napisać skrypt wpisu

Zapisz następujące do katalogu roboczego jako `score.py`. Ten plik będzie służyć do oceniania obrazów, ponieważ są one wysyłane do usługi. Ten plik ładuje TensorFlow zapisane modelu, a następnie na każdy WPIS żądania przekazuje obrazu wejściowego do sesji TensorFlow i zwraca wynikowy wyniki.
Inne struktury wnioskowania będzie wymagać różnych plików oceniania.

```python
import tensorflow as tf
import numpy as np
import ujson
from azureml.core.model import Model
from azureml.contrib.services.aml_request import AMLRequest, rawhttp
from azureml.contrib.services.aml_response import AMLResponse

def init():
    global session
    global input_name
    global output_name
    
    session = tf.Session()

    model_path = Model.get_model_path('resnet50')
    model = tf.saved_model.loader.load(session, ['serve'], model_path)
    if len(model.signature_def['serving_default'].inputs) > 1:
        raise ValueError("This score.py only supports one input")
    input_name = [tensor.name for tensor in model.signature_def['serving_default'].inputs.values()][0]
    output_name = [tensor.name for tensor in model.signature_def['serving_default'].outputs.values()]
    

@rawhttp
def run(request):
    if request.method == 'POST':
        reqBody = request.get_data(False)
        resp = score(reqBody)
        return AMLResponse(resp, 200)
    if request.method == 'GET':
        respBody = str.encode("GET is not supported")
        return AMLResponse(respBody, 405)
    return AMLResponse("bad request", 500)

def score(data):
    result = session.run(output_name, {input_name: [data]})
    return ujson.dumps(result[1])

if __name__ == "__main__":
    init()
    with open("lynx.jpg", 'rb') as f: #load file for testing locally
        content = f.read()
        print(score(content))

```

## <a name="define-conda-environment"></a>Zdefiniuj środowiska Conda
Utwórz plik środowiska conda, o nazwie `myenv.yml` Aby określić zależności dla Twojej usługi. Ważne jest, aby określić, że używasz `tensorflow-gpu` osiągnąć wydajność jej jako przyspieszonej.
```yaml
name: aml-accel-perf
channels:
  - defaults
dependencies:
  - tensorflow-gpu = 1.12
  - numpy
  - ujson
  - pip:
    - azureml-core
    - azureml-contrib-services
```

## <a name="define-gpu-inferenceconfig"></a>Zdefiniuj InferenceConfig procesora GPU

Tworzenie [ `InferenceConfig` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) Określa, że włączasz procesora GPU. Pozwoli to zagwarantować, że CUDA jest instalowany z obrazu.

```python
from azureml.core.model import Model
from azureml.core.model import InferenceConfig

aks_service_name ='gpu-rn'
gpu_aks_config = AksWebservice.deploy_configuration(autoscale_enabled = False, 
                                                    num_replicas = 3, 
                                                    cpu_cores=2, 
                                                    memory_gb=4)
model = Model(ws,"resnet50")

inference_config = InferenceConfig(runtime= "python", 
                                   entry_script="score.py",
                                   conda_file="myenv.yml", 
                                   gpu_enabled=True)
```

Aby uzyskać więcej informacji, zobacz [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) i [AksServiceDeploymentConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aks.aksservicedeploymentconfiguration?view=azure-ml-py).
## <a name="deploy-the-model"></a>Wdrażanie modelu

Wdrażanie modelu w klastrze usługi AKS i poczekaj na utworzenie usługi.

```python
aks_service = Model.deploy(ws,
                           models=[model],
                           inference_config=inference_config, 
                           deployment_config=aks_config,
                           deployment_target=aks_target,
                           name=aks_service_name)

aks_service.wait_for_deployment(show_output = True)
print(aks_service.state)
```

> [!NOTE]
> Usługa Azure Machine Learning nie wdroży model przy użyciu `InferenceConfig` który oczekuje, że procesora GPU do klastra bez procesora GPU.

Aby uzyskać więcej informacji, zobacz [modelu](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py).

## <a name="issue-sample-query-to-deployed-model"></a>Problem przykładowe zapytanie, aby wdrożyć model

Przykładowe zapytanie można wydać wdrożonego modelu. W tym modelu będzie wynik dowolnego obrazu jpeg, wysyłanych do niego jako żądania post. 

```python
scoring_url = aks_service.scoring_uri
api_key = aks_service.get_key()(0)
IMAGEURL = "https://upload.wikimedia.org/wikipedia/commons/thumb/6/68/Lynx_lynx_poing.jpg/220px-Lynx_lynx_poing.jpg"

headers = {'Authorization':('Bearer '+ api_key)}
img_data = read_image_from(IMAGEURL).read()
r = requests.post(scoring_url, data = img_data, headers=headers)
```

> [!IMPORTANT]
> W celu zoptymalizowania opóźnienia i przepływności, klienta należy w tym samym regionie platformy Azure jako punktu końcowego.  Obecnie interfejsy API są tworzone w regionie wschodnie stany USA Azure.

## <a name="cleaning-up-the-resources"></a>Oczyszczanie zasobów

Po wykonaniu wersję demonstracyjną, należy usunąć swoje zasoby.

> [!IMPORTANT]
> Azure obciąży oparte na jak długo klaster AKS jest wdrażany. Upewnij się wyczyścić, po zakończeniu z nim.

```python
aks_service.delete()
aks_target.delete()
```

## <a name="next-steps"></a>Kolejne kroki

* [Wdrażanie modelu na FPGA](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-fpga-web-service)
* [Wdrażanie modelu z ONNX](https://docs.microsoft.com/azure/machine-learning/service/how-to-build-deploy-onnx#deploy)
* [Szkolenie modeli Tensorflow DNN](https://docs.microsoft.com/azure/machine-learning/service/how-to-train-tensorflow)
