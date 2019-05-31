---
title: Wdrażanie modelu na potrzeby wnioskowania przy użyciu procesora GPU
titleSuffix: Azure Machine Learning service
description: Dowiedz się, jak wdrożyć model uczenia głębokiego jako usługę internetową, która używa procesora GPU na potrzeby wnioskowania. W tym artykule modelu Tensorflow zostanie wdrożony do klastra usługi Azure Kubernetes Service. Klaster używa włączonymi procesorami GPU maszyny Wirtualnej do hosta usługi sieci web i wnioskowania wynik żądania.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: vaidyas
author: csteegz
ms.reviewer: larryfr
ms.date: 05/02/2019
ms.openlocfilehash: ec71165553a1d65ff133d605bf94255100f74e6e
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/30/2019
ms.locfileid: "66388920"
---
# <a name="deploy-a-deep-learning-model-for-inference-with-gpu"></a>Wdrażanie modelu uczenia głębokiego, na potrzeby wnioskowania przy użyciu procesora GPU

Dowiedz się, jak używać wnioskowania procesora GPU dla usługi machine learning model wdrożyć jako usługę sieci web. Wnioskowanie lub oceniania modelu, jest faza użycia wdrożony model do przewidywania najczęściej w danych produkcyjnych.

Ten artykuł nauczy Cię sposobu używania usługi Azure Machine Learning do wdrożenia przykład Tensorflow głębokie uczenie modelu do klastra Azure Kubernetes Service (AKS) na maszynie wirtualnej (VM) włączonymi procesorami GPU. Gdy żądania są wysyłane do usługi, model używa procesora GPU do uruchamiania obciążeń wnioskowania.

Procesory GPU oferują za pośrednictwem procesorów korzyści związanych z wydajnością na obliczeń o wysokiej równoległego. Przypadki użycia doskonałe dla maszyn wirtualnych z włączonymi procesorami GPU obejmują głębokiego uczenia modelu szkoleń i wnioskowania, szczególnie w przypadku dużych partii żądań.

W tym przykładzie przedstawiono sposób wdrażania TensorFlow, zapisane modelu usługi Azure Machine Learning. Należy wykonać następujące czynności:

* Tworzenie klastra AKS z włączonymi procesorami GPU
* Wdrażanie modelu Tensorflow procesora GPU

## <a name="prerequisites"></a>Wymagania wstępne

* Obszar roboczy usługi Azure Machine Learning
* Dystrybucja języka Python
* Zarejestrowane Tensorflow zapisać modelu. Aby dowiedzieć się, jak zarejestrować modeli, zobacz [wdrażanie modeli](../service/how-to-deploy-and-where.md#registermodel).

Ten artykuł jest oparty na notesu programu Jupyter [wdrażanie modeli Tensorflow AKS](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/production-deploy-to-aks-gpu/production-deploy-to-aks-gpu.ipynb). Notes Jupyter używa TensorFlow zapisane modeli i wdraża je do klastra usługi AKS. Notes stosować również do dowolnej maszyny uczenia framework, który obsługuje procesory GPU wprowadzając niewielkich zmian w pliku oceniania i plikiem środowiska.  

## <a name="provision-an-aks-cluster-with-gpus"></a>Zainicjuj obsługę klastra usługi AKS za pomocą procesorów GPU

Platforma Azure oferuje wiele różnych opcji procesora GPU. Służy którąkolwiek z nich do wnioskowania. Zobacz [listę maszyn wirtualnych serii N](https://azure.microsoft.com/pricing/details/virtual-machines/linux/#n-series) pełną podziale kosztów i możliwości.

Aby uzyskać więcej informacji na temat korzystania z usługi AKS przy użyciu usługi Azure Machine Learning, zobacz [sposób wdrażania i gdzie](../service/how-to-deploy-and-where.md#deploy-aks).

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

## <a name="write-the-entry-script"></a>Napisać skrypt wpisu

Zapisz poniższy kod do katalogu roboczego jako `score.py`. Ten plik ocenia obrazów, ponieważ są one wysyłane do usługi. Jego ładowania modelu TensorFlow zapisane, przekazuje obrazu wejściowego do sesji TensorFlow na każde żądanie POST, a następnie zwraca wynikowy wyniki. Inne struktury wnioskowania wymagają różnych plików oceniania.

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

## <a name="define-the-conda-environment"></a>Zdefiniuj środowiska conda

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

## <a name="define-the-gpu-inferenceconfig-class"></a>Zdefiniuj klasę InferenceConfig procesora GPU

Utwórz `InferenceConfig` obiekt, który umożliwia procesorów GPU i gwarantuje, że CUDA jest instalowany z obrazu platformy Docker.

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

## <a name="issue-a-sample-query-to-your-deployed-model"></a>Wydawanie przykładowego zapytania do wdrożonego modelu

Wysłanie zapytania testu do wdrożony model. Obraz jpeg jest wysyłany do modelu, ocenia obrazu.

```python
scoring_url = aks_service.scoring_uri
api_key = aks_service.get_key()(0)
IMAGEURL = "https://upload.wikimedia.org/wikipedia/commons/thumb/6/68/Lynx_lynx_poing.jpg/220px-Lynx_lynx_poing.jpg"

headers = {'Authorization':('Bearer '+ api_key)}
img_data = read_image_from(IMAGEURL).read()
r = requests.post(scoring_url, data = img_data, headers=headers)
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
