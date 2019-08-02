---
title: Wdróż modele na FPGA
titleSuffix: Azure Machine Learning service
description: Dowiedz się, jak wdrożyć usługę sieci Web z modelem działającym na FPGA za pomocą usługi Azure Machine Learning na potrzeby wnioskowania o bardzo małych opóźnieniach.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: larryfr
ms.author: tedway
author: tedway
ms.date: 07/25/2019
ms.custom: seodec18
ms.openlocfilehash: cec1a74938690a4f781ea7850fdd6d649550b3eb
ms.sourcegitcommit: 5604661655840c428045eb837fb8704dca811da0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/25/2019
ms.locfileid: "68494908"
---
# <a name="deploy-a-model-as-a-web-service-on-an-fpga-with-azure-machine-learning-service"></a>Wdrażanie modelu jako usługi sieci Web na FPGA za pomocą usługi Azure Machine Learning

Model można wdrożyć jako usługę sieci Web na przykładowych [tablicach bram (FPGA)](concept-accelerate-with-fpgas.md) z Azure Machine Learning modele z przyspieszaniem sprzętowym. Korzystanie z usługi FPGA zapewnia bardzo małe ilości opóźnienia, nawet w przypadku pojedynczego rozmiaru partii. Wnioskowanie lub ocenianie modelu to faza, w której wdrożony model jest używany do prognozowania, najczęściej dotyczący danych produkcyjnych.

Te modele są obecnie dostępne:
  - ResNet 50
  - ResNet 152
  - DenseNet-121
  - VGG-16
  - SSD-VGG

FPGA są dostępne w tych regionach świadczenia usługi Azure:
  - East US
  - Azja Południowo-Wschodnia
  - Europa Zachodnia
  - Zachodnie stany USA 2

> [!IMPORTANT]
> Aby zoptymalizować opóźnienia i przepływność, klient wysyłający dane do modelu FPGA powinien znajdować się w jednym z powyższych regionów (na którym został wdrożony model).

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure.  Jeśli nie masz takiego konta, przed rozpoczęciem utwórz bezpłatne konto. Wypróbuj [bezpłatną lub płatną wersję usługi Azure Machine Learning](https://aka.ms/AMLFree) już dziś.

- Limit przydziału FPGA. Użyj interfejsu wiersza polecenia platformy Azure, aby sprawdzić, czy masz limit przydziału:

    ```shell
    az vm list-usage --location "eastus" -o table --query "[?localName=='Standard PBS Family vCPUs']"
    ```

    > [!TIP]
    > Inne możliwe lokalizacje to ``southeastasia``, ``westeurope``i ``westus2``.

    Polecenie zwraca tekst podobny do następującego:

    ```text
    CurrentValue    Limit    LocalName
    --------------  -------  -------------------------
    0               6        Standard PBS Family vCPUs
    ```

    Upewnij się, że masz co najmniej 6 procesorów wirtualnych vCPU w obszarze __CurrentValue__.

    Jeśli nie masz przydziału, Prześlij żądanie na [https://aka.ms/accelerateAI](https://aka.ms/accelerateAI).

- Obszar roboczy usługi Azure Machine Learning i Azure Machine Learning SDK dla język Python jest zainstalowany. Aby uzyskać więcej informacji, zobacz [Tworzenie obszaru roboczego](setup-create-workspace.md).
 
- Zestaw SDK języka Python dla modeli przyspieszanych sprzętowo:

    ```shell
    pip install --upgrade azureml-accel-models
    ```

## <a name="sample-notebooks"></a>Przykładowe notesy

Dla wygody można [](https://aka.ms/aml-accel-models-notebooks) zapoznać się z przykładowymi notesami poniżej i innymi przykładami.

## <a name="create-and-containerize-your-model"></a>Utwórz i konteneryzowanie model

W tym dokumencie opisano sposób tworzenia grafu TensorFlow w celu wstępnego przetworzenia obrazu wejściowego, przeprowadzenia featurized przy użyciu ResNet 50 na FPGA, a następnie uruchomienia funkcji za pomocą klasyfikatora przeszkolonego na zestawie danych ImageNet.

Postępuj zgodnie z instrukcjami, aby:

* Zdefiniuj model TensorFlow
* Konwertuj model
* Wdrażanie modelu
* Używanie wdrożonego modelu
* Usuwanie wdrożonych usług

### <a name="load-azure-ml-workspace"></a>Załaduj obszar roboczy usługi Azure ML

Załaduj obszar roboczy usługi Azure ML.

```python
import os
import tensorflow as tf

from azureml.core import Workspace

ws = Workspace.from_config()
print(ws.name, ws.resource_group, ws.location, ws.subscription_id, sep='\n')
```

### <a name="preprocess-image"></a>Wstępne przetwarzanie obrazu

Wejście do usługi sieci Web jest obrazem JPEG.  Pierwszym krokiem jest Dekodowanie obrazu JPEG i jego wstępne przetworzenie.  Obrazy JPEG są traktowane jako ciągi, a wynikiem są dwuplikowe dane wejściowe modelu ResNet 50.

```python
# Input images as a two-dimensional tensor containing an arbitrary number of images represented a strings
import azureml.accel.models.utils as utils
tf.reset_default_graph()

in_images = tf.placeholder(tf.string)
image_tensors = utils.preprocess_array(in_images)
print(image_tensors.shape)
```

### <a name="load-featurizer"></a>Ładuj featurized

Inicjowanie modelu i Pobierz punkt kontrolny TensorFlow wykonywanie kwantyzowanych wersji ResNet50 ma być używany jako featurized.  W poniższym fragmencie kodu można zastąpić "QuantizedResnet50", importując inne sieci głębokiej neuronowych:

- QuantizedResnet152
- QuantizedVgg16
- Densenet121

```python
from azureml.accel.models import QuantizedResnet50
save_path = os.path.expanduser('~/models')
model_graph = QuantizedResnet50(save_path, is_frozen=True)
feature_tensor = model_graph.import_graph_def(image_tensors)
print(model_graph.version)
print(feature_tensor.name)
print(feature_tensor.shape)
```

### <a name="add-classifier"></a>Dodaj klasyfikator

Po zapoznaniu to klasyfikatora na zestawie danych sieci ImageNet.  Przykłady dotyczące uczenia i szkolenia z zakresu przenoszenia są dostępne w zestawie przykładowych [notesów](https://aka.ms/aml-notebooks).

```python
classifier_output = model_graph.get_default_classifier(feature_tensor)
print(classifier_output)
```

### <a name="save-the-model"></a>Zapisz model

Teraz, gdy preprocesor, ResNet 50 featurized i klasyfikator został załadowany, Zapisz wykres i skojarzone zmienne jako model.

```python
model_name = "resnet50"
model_save_path = os.path.join(save_path, model_name)
print("Saving model in {}".format(model_save_path))

with tf.Session() as sess:
    model_graph.restore_weights(sess)
    tf.saved_model.simple_save(sess, model_save_path,
                               inputs={'images': in_images},
                               outputs={'output_alias': classifier_output})
```

### <a name="save-input-and-output-tensors"></a>Zapisz natężenia wejścia i wyjścia
W przypadku konwersji modelu i wnioskowania będą wymagane dwuetapy wejściowe i wyjściowe, które zostały utworzone podczas procesu przetwarzania wstępnego i klasyfikatora.

```python
input_tensors = in_images.name
output_tensors = classifier_output.name

print(input_tensors)
print(output_tensors)
```

> [!IMPORTANT]
> Zapisz natężenia wejścia i wyjścia, ponieważ będą potrzebne do konwersji modeli i żądań wnioskowania.

Dostępne są następujące modele i odpowiednie rozdzielacze domyślne klasyfikatora, które są używane do wnioskowania, jeśli użyto klasyfikatora domyślnego.

+ Resnet50, QuantizedResnet50
  ```python
  output_tensors = "classifier_1/resnet_v1_50/predictions/Softmax:0"
  ```
+ Resnet152, QuantizedResnet152
  ```python
  output_tensors = "classifier/resnet_v1_152/predictions/Softmax:0"
  ```
+ Densenet121, QuantizedDensenet121
  ```python
  output_tensors = "classifier/densenet121/predictions/Softmax:0"
  ```
+ Vgg16, QuantizedVgg16
  ```python
  output_tensors = "classifier/vgg_16/fc8/squeezed:0"
  ```
+ SsdVgg, QuantizedSsdVgg
  ```python
  output_tensors = ['ssd_300_vgg/block4_box/Reshape_1:0', 'ssd_300_vgg/block7_box/Reshape_1:0', 'ssd_300_vgg/block8_box/Reshape_1:0', 'ssd_300_vgg/block9_box/Reshape_1:0', 'ssd_300_vgg/block10_box/Reshape_1:0', 'ssd_300_vgg/block11_box/Reshape_1:0', 'ssd_300_vgg/block4_box/Reshape:0', 'ssd_300_vgg/block7_box/Reshape:0', 'ssd_300_vgg/block8_box/Reshape:0', 'ssd_300_vgg/block9_box/Reshape:0', 'ssd_300_vgg/block10_box/Reshape:0', 'ssd_300_vgg/block11_box/Reshape:0']
  ```

### <a name="register-model"></a>Rejestrowanie modelu

[Zarejestruj](./concept-model-management-and-deployment.md) utworzony model.  Dodawanie tagów i innych metadanych dotyczących modelu ułatwia śledzenie przeszkolonych modeli.

```python
from azureml.core.model import Model

registered_model = Model.register(workspace=ws,
                                  model_path=model_save_path,
                                  model_name=model_name)

print("Successfully registered: ", registered_model.name,
      registered_model.description, registered_model.version, sep='\t')
```

Jeśli model został już zarejestrowany i chcesz go załadować, możesz go pobrać.

```python
from azureml.core.model import Model
model_name = "resnet50"
# By default, the latest version is retrieved. You can specify the version, i.e. version=1
registered_model = Model(ws, name="resnet50")
print(registered_model.name, registered_model.description,
      registered_model.version, sep='\t')
```

### <a name="convert-model"></a>Konwertuj model

Przekonwertuj wykres TensorFlow na format Open neuronowych Network Exchange ([ONNX](https://onnx.ai/)).  Należy podać nazwy natężeń wejściowych i wyjściowych, a te nazwy będą używane przez klienta podczas korzystania z usługi sieci Web.

```python
from azureml.accel import AccelOnnxConverter

convert_request = AccelOnnxConverter.convert_tf_model(
    ws, registered_model, input_tensors, output_tensors)

# If it fails, you can run wait_for_completion again with show_output=True.
convert_request.wait_for_completion(show_output=False)

# If the above call succeeded, get the converted model
converted_model = convert_request.result
print("\nSuccessfully converted: ", converted_model.name, converted_model.url, converted_model.version,
      converted_model.id, converted_model.created_time, '\n')
```

### <a name="create-docker-image"></a>Tworzenie obrazu platformy Docker

Przekonwertowany model i wszystkie zależności są dodawane do obrazu platformy Docker.  Ten obraz platformy Docker można następnie wdrożyć i utworzyć jego wystąpienie.  Obsługiwane cele wdrożenia obejmują AKS w chmurze lub urządzenie brzegowe, takie jak [Azure Data Box Edge](https://docs.microsoft.com/azure/databox-online/data-box-edge-overview).  Możesz również dodać tagi i opisy dla zarejestrowanego obrazu platformy Docker.

```python
from azureml.core.image import Image
from azureml.accel import AccelContainerImage

image_config = AccelContainerImage.image_configuration()
# Image name must be lowercase
image_name = "{}-image".format(model_name)

image = Image.create(name=image_name,
                     models=[converted_model],
                     image_config=image_config,
                     workspace=ws)
image.wait_for_creation(show_output=False)
```

Wyświetl listę obrazów według tagów i uzyskaj szczegółowe dzienniki dla dowolnego debugowania.

```python
for i in Image.list(workspace=ws):
    print('{}(v.{} [{}]) stored at {} with build log {}'.format(
        i.name, i.version, i.creation_state, i.image_location, i.image_build_log_uri))
```

## <a name="model-deployment"></a>Wdrażanie modelu

### <a name="deploy-to-the-cloud"></a>Wdrażanie w chmurze

Aby wdrożyć modelu w postaci usługi sieci web o dużej skali w środowisku produkcyjnym, należy użyć usługi Azure Kubernetes Service (AKS). Nowy element można utworzyć przy użyciu zestawu SDK Azure Machine Learning, interfejsu wiersza polecenia lub Azure Portal.

```python
from azureml.core.compute import AksCompute, ComputeTarget

# Specify the Standard_PB6s Azure VM
prov_config = AksCompute.provisioning_configuration(vm_size="Standard_PB6s",
                                                    agent_count=1)

aks_name = 'my-aks-cluster'
# Create the cluster
aks_target = ComputeTarget.create(workspace=ws,
                                  name=aks_name,
                                  provisioning_configuration=prov_config)
```

Wdrożenie AKS może potrwać około 15 minut.  Sprawdź, czy wdrożenie zakończyło się pomyślnie.

```python
aks_target.wait_for_completion(show_output=True)
print(aks_target.provisioning_state)
print(aks_target.provisioning_errors)
```

Wdróż kontener w klastrze AKS.
```python
from azureml.core.webservice import Webservice, AksWebservice

# For this deployment, set the web service configuration without enabling auto-scaling or authentication for testing
aks_config = AksWebservice.deploy_configuration(autoscale_enabled=False,
                                                num_replicas=1,
                                                auth_enabled=False)

aks_service_name = 'my-aks-service'

aks_service = Webservice.deploy_from_image(workspace=ws,
                                           name=aks_service_name,
                                           image=image,
                                           deployment_config=aks_config,
                                           deployment_target=aks_target)
aks_service.wait_for_deployment(show_output=True)
```

#### <a name="test-the-cloud-service"></a>Testowanie usługi w chmurze
Obraz platformy Docker obsługuje gRPC i TensorFlow obsługujący interfejs API "przewidywania".  Użyj przykładowego klienta do wywołania obrazu platformy Docker w celu uzyskania prognoz z modelu.  Kod przykładowego klienta jest dostępny:
- [Python](https://github.com/Azure/aml-real-time-ai/blob/master/pythonlib/amlrealtimeai/client.py)
- [C#](https://github.com/Azure/aml-real-time-ai/blob/master/sample-clients/csharp)

Jeśli chcesz korzystać z TensorFlow, możesz [pobrać przykładowego klienta](https://www.tensorflow.org/serving/setup).

```python
# Using the grpc client in Azure ML Accelerated Models SDK package
from azureml.accel import PredictionClient

address = aks_service.scoring_uri
ssl_enabled = address.startswith("https")
address = address[address.find('/')+2:].strip('/')
port = 443 if ssl_enabled else 80

# Initialize AzureML Accelerated Models client
client = PredictionClient(address=address,
                          port=port,
                          use_ssl=ssl_enabled,
                          service_name=aks_service.name)
```

Ponieważ ten klasyfikator został przeszkolony w zestawie danych [ImageNet](http://www.image-net.org/) , Mapuj klasy na etykiety czytelne dla człowieka.

```python
import requests
classes_entries = requests.get(
    "https://raw.githubusercontent.com/Lasagne/Recipes/master/examples/resnet50/imagenet_classes.txt").text.splitlines()

# Score image with input and output tensor names
results = client.score_file(path="./snowleopardgaze.jpg",
                            input_name=input_tensors,
                            outputs=output_tensors)

# map results [class_id] => [confidence]
results = enumerate(results)
# sort results by confidence
sorted_results = sorted(results, key=lambda x: x[1], reverse=True)
# print top 5 results
for top in sorted_results[:5]:
    print(classes_entries[top[0]], 'confidence:', top[1])
```

### <a name="clean-up-the-service"></a>Czyszczenie usługi
Usuń usługę sieci Web, obraz i model (należy wykonać w tej kolejności, ponieważ istnieją zależności).

```python
aks_service.delete()
aks_target.delete()
image.delete()
registered_model.delete()
converted_model.delete()
```

## <a name="deploy-to-a-local-edge-server"></a>Wdrażanie na lokalnym serwerze brzegowym

Wszystkie [urządzenia](https://docs.microsoft.com/azure/databox-online/data-box-edge-overview
) Azure Data Box Edge zawierają FPGA do uruchamiania modelu.  Tylko jeden model może być uruchomiony w FPGA jednocześnie.  Aby uruchomić inny model, po prostu wdróż nowy kontener. Instrukcje i przykładowy kod można znaleźć w [tym przykładzie platformy Azure](https://github.com/Azure-Samples/aml-hardware-accelerated-models).

## <a name="secure-fpga-web-services"></a>Zabezpieczania usług sieci web FPGA

Aby uzyskać informacje na temat zabezpieczania usług sieci Web FPGA, zobacz dokument [Secure Web Services](how-to-secure-web-service.md) .

## <a name="pbs-family-vms"></a>Maszyny wirtualne z rodziny PBS

Rodzina PBS maszyn wirtualnych platformy Azure zawiera technologię Intel Arria 10 FPGA.  Zostanie on wyświetlony jako "standardowa rodzina PBS procesorów wirtualnych vCPU" podczas sprawdzania przydziału przydziału platformy Azure.  Maszyna wirtualna w PB6 ma sześć procesorów wirtualnych vCPU i jeden FPGA i zostanie automatycznie zainicjowana przez usługę Azure ML w ramach wdrażania modelu w FPGA.  Jest ona używana tylko z platformą Azure ML i nie może uruchamiać dowolnego bitstreams.  Na przykład nie będzie można wykonać czynności Flash FPGA z bitstreams do szyfrowania, kodowania itp. 
