---
title: Wdrażanie modeli w układów FPGA
titleSuffix: Azure Machine Learning service
description: Dowiedz się, jak wdrożyć usługę sieci web przy użyciu modelu z systemem FPGA za pomocą usługi Azure Machine Learning do wnioskowania bardzo niskimi opóźnieniami.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: larryfr
ms.author: tedway
author: tedway
ms.date: 05/02/2019
ms.custom: seodec18
ms.openlocfilehash: cfe21d2119b92665c5950d792dec6500257c6316
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/02/2019
ms.locfileid: "65024179"
---
# <a name="deploy-a-model-as-a-web-service-on-an-fpga-with-azure-machine-learning-service"></a>Wdrażanie modelu jako usługi sieci web na FPGA za pomocą usługi Azure Machine Learning

Model można wdrożyć jako usługę sieci web, na [pola Tablice bramek programowane (układów FPGA)](concept-accelerate-with-fpgas.md) przy użyciu modeli Accelerated sprzętu uczenia maszynowego Azure. Za pomocą układów FPGA zapewnia wnioskowania niezwykle małych opóźnień, nawet w przypadku rozmiar jednej partii.

Obecnie dostępne są następujące modele:
  - ResNet 50
  - ResNet 152
  - DenseNet-121
  - VGG-16
  - SSD-VGG

Układów FPGA są dostępne w tych regionach platformy Azure:
  - Wschodnie stany USA
  - Zachodnie stany USA 2
  - Europa Zachodnia
  - Azja Południowo-Wschodnia

> [!IMPORTANT]
> W celu zoptymalizowania opóźnienia i przepływności, klient wysyłania danych do modelu FPGA powinna mieć jeden z regionów powyżej (ten, który został wdrożony model, który ma).

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli nie masz subskrypcji Azure, przed rozpoczęciem utwórz bezpłatne konto. Wypróbuj [bezpłatną lub płatną wersję usługi Azure Machine Learning](https://aka.ms/AMLFree) już dziś.

- Obszar roboczy usługi Azure Machine Learning i Azure Machine Learning SDK dla język Python jest zainstalowany. Dowiedz się, jak uzyskać te wymagania wstępne przy użyciu [sposób konfigurowania środowiska deweloperskiego](how-to-configure-environment.md) dokumentu.
 
  - Instalowanie zestawu SDK języka Python dla modeli przyspieszanych sprzętowo:

    ```shell
    pip install --upgrade azureml-accel-models
    ```

## <a name="sample-notebooks"></a>Przykładowe notesy

Dla Twojej wygody [przykładowy notesów](https://aka.ms/aml-notebooks) są dostępne w przykładzie poniżej, a oprócz inne przykłady.  Poszukaj w folderach, jak-to-użyj-usługi Azure ml i wdrażania modeli jej jako przyspieszonej.

## <a name="create-and-containerize-your-model"></a>Tworzenie i umieścić model w kontenerze

Ten dokument będzie opisują sposób tworzenia grafu TensorFlow przetwarzanie wstępne obrazu wejściowego, spowodować featurized, za pomocą siecią ResNet-50 na FPGA, a następnie uruchom funkcji przy użyciu klasyfikatora skonfigurowanych pod kątem w zestawie danych sieci ImageNet.

Postępuj zgodnie z instrukcjami, aby:

* Zdefiniuj TensorFlow model
* Wdrażanie modelu
* Używanie wdrożonego modelu
* Usuwanie wdrożonych usług

### <a name="load-azure-ml-workspace"></a>Załaduj obszar roboczy usługi uczenie Maszynowe Azure

Załaduj obszar roboczy usługi uczenie Maszynowe Azure.

```python
import os
import tensorflow as tf
 
from azureml.core import Workspace
 
ws = Workspace.from_config()
print(ws.name, ws.resource_group, ws.location, ws.subscription_id, sep = '\n')
```

### <a name="preprocess-image"></a>Wstępne przetwarzanie obrazu

Dane wejściowe do usługi sieci web jest obraz JPEG.  Pierwszym krokiem jest dekodować obraz JPEG i wstępnie Przetwórz ją.  Obrazy JPEG są traktowane jak ciągów i wynik tensors, które będą znajdować się dane wejściowe do modelu siecią ResNet-50.

```python
# Input images as a two-dimensional tensor containing an arbitrary number of images represented a strings
import azureml.accel.models.utils as utils
in_images = tf.placeholder(tf.string)
image_tensors = utils.preprocess_array(in_images)
print(image_tensors.shape)
```

### <a name="load-featurizer"></a>Featurized obciążenia

Inicjowanie modelu i Pobierz punkt kontrolny TensorFlow wykonywanie kwantyzowanych wersji ResNet50 ma być używany jako featurized.  Można zastąpić "QuantizedResnet50" we fragmencie kodu poniżej z importując innych głębokich sieciach neuronowych:

- QuantizedResnet152
- QuantizedVgg16
- Densenet121

```python
from azureml.accel.models import QuantizedResnet50
save_path = os.path.expanduser('~/models')
model_graph = QuantizedResnet50(save_path, is_frozen = True)
feature_tensor = model_graph.import_graph_def(image_tensors)
print(model_graph.version)
print(feature_tensor.name)
print(feature_tensor.shape)
```

### <a name="add-classifier"></a>Dodaj klasyfikatora

Po zapoznaniu to klasyfikatora na zestawie danych sieci ImageNet.  Przykłady transferu uczenia i szkolenia swoje niestandardowe wagi są dostępne w zestawie [przykładowy notesów](https://aka.ms/aml-notebooks).

```python
classifier_output = model_graph.get_default_classifier(feature_tensor)
print(classifier_output)
```

### <a name="save-the-model"></a>Zapisz model

Teraz, gdy załadowano preprocesora, siecią ResNet-50 featurized i klasyfikatora, Zapisz wykres i skojarzone zmienne jako model.

```python
model_name = "resnet50"
model_def_path = os.path.join(save_path, model_name)
print("Saving model in {}".format(model_def_path))

with tf.Session() as sess:
    model_graph.restore_weights(sess)
    tf.saved_model.simple_save(sess, model_def_path,
                                   inputs={'images': in_images},
                                   outputs={'output_alias': classifier_output})
```

### <a name="register-model"></a>Rejestrowanie modelu

[Zarejestruj](./concept-model-management-and-deployment.md) modelu, który został utworzony.  Dodawanie tagów i innych metadanych dotyczących modelu pomaga śledzić wytrenowane modele.

```python
from azureml.core.model import Model

registered_model = Model.register(workspace = ws
                                  model_path = model_def_path,
                                  model_name = model_name)

print("Successfully registered: ", registered_model.name, registered_model.description, registered_model.version, sep = '\t')
```

Jeśli model jest już zarejestrowany, a chcesz go załadować, mogą ją pobrać.

```python
from azureml.core.model import Model
model_name = "resnet50"
# By default, the latest version is retrieved. You can specify the version, i.e. version=1
registered_model = Model(ws, name="resnet50")
print(registered_model.name, registered_model.description, registered_model.version, sep = '\t')
```

### <a name="convert-model"></a>Konwertuj modelu

Wykres TensorFlow musi zostać skonwertowany do formatu Otwórz Exchange sieci Neuronowej ([ONNX](https://onnx.ai/)).  Należy podać nazwy tensors wejściowych i wyjściowych, a te nazwy będą używane przez klienta podczas korzystania z usługi sieci web.

```python
input_tensor = in_images.name
output_tensors = classifier_output.name

print(input_tensor)
print(output_tensors)


from azureml.accel.accel_onnx_converter import AccelOnnxConverter

convert_request = AccelOnnxConverter.convert_tf_model(ws, registered_model, input_tensor, output_tensors)
convert_request.wait_for_completion(show_output=True)

# If the above call succeeded, get the converted model
converted_model = convert_request.result
print(converted_model.name, converted_model.url, converted_model.version, converted_model.id,converted_model.created_time)
```

### <a name="create-docker-image"></a>Tworzenie obrazu platformy Docker

Przekonwertowana modelu i wszystkie zależności są dodawane do obrazu platformy Docker.  Ten obraz platformy Docker można następnie wdrożyć i tworzone w chmurze lub urządzenie na krawędzi obsługiwanych, takich jak [usługi Azure Data Box Edge](https://docs.microsoft.com/azure/databox-online/data-box-edge-overview).  Można również dodać tagi i opisów dla zarejestrowanego obraz platformy Docker.

```python
from azureml.core.image import Image
from azureml.accel.accel_container_image import AccelContainerImage

image_config = AccelContainerImage.image_configuration()
image_name = "{}-image".format(model_name)

image = Image.create(name = image_name,
                     models = [converted_model],
                     image_config = image_config, 
                     workspace = ws)


image.wait_for_creation(show_output = True)
```

Wyświetl listę obrazów według tagów i uzyskać szczegółowe dzienniki wszelkie debugowania.

```python
for i in Image.list(workspace = ws):
    print('{}(v.{} [{}]) stored at {} with build log {}'.format(i.name, i.version, i.creation_state, i.image_location, i.image_build_log_uri))
```

## <a name="model-deployment"></a>Wdrażanie modelu

### <a name="deploy-to-the-cloud"></a>Wdrożenie w chmurze

Aby wdrożyć modelu w postaci usługi sieci web o dużej skali w środowisku produkcyjnym, należy użyć usługi Azure Kubernetes Service (AKS). Można utworzyć nowe konto, przy użyciu zestawu SDK usługi Azure Machine Learning, interfejsu wiersza polecenia lub witryny Azure portal.

```python
# Use the default configuration (can also provide parameters to customize)
prov_config = AksCompute.provisioning_configuration()

aks_name = 'my-aks-9' 
# Create the cluster
aks_target = ComputeTarget.create(workspace = ws, 
                                  name = aks_name, 
                                  provisioning_configuration = prov_config)

%%time
aks_target.wait_for_completion(show_output = True)
print(aks_target.provisioning_state)
print(aks_target.provisioning_errors)

#Set the web service configuration (using default here)
aks_config = AksWebservice.deploy_configuration()

%%time
aks_service_name ='aks-service-1'

aks_service = Webservice.deploy_from_image(workspace = ws, 
                                           name = aks_service_name,
                                           image = image,
                                           deployment_config = aks_config,
                                           deployment_target = aks_target)
aks_service.wait_for_deployment(show_output = True)
print(aks_service.state)
print(aks_service.scoring_uri)
```

#### <a name="test-the-cloud-service"></a>Testowanie usługi w chmurze
Obraz platformy Docker obsługuje gRPC i TensorFlow obsługująca "przewidzieć" interfejsu API.  Za pomocą klienta przykładowe wywołanie obrazu platformy Docker, aby uzyskiwać prognozy z modelu.  Przykładowy kod klienta jest dostępny:
- [Python](https://github.com/Azure/aml-real-time-ai/blob/master/pythonlib/amlrealtimeai/client.py)
- [C#](https://github.com/Azure/aml-real-time-ai/blob/master/sample-clients/csharp)

Jeśli chcesz użyć, obsługująca TensorFlow, możesz to zrobić [Pobierz klienta przykładowe](https://www.tensorflow.org/serving/setup).

```python
import requests
classes_entries = requests.get("https://raw.githubusercontent.com/Lasagne/Recipes/master/examples/resnet50/imagenet_classes.txt").text.splitlines()

# Score image using input and output tensor names
results = client.score_file(path="./snowleopardgaze.jpg", 
                             input_name=input_tensor, 
                             outputs=output_tensors)

# map results [class_id] => [confidence]
results = enumerate(results)
# sort results by confidence
sorted_results = sorted(results, key=lambda x: x[1], reverse=True)
# print top 5 results
for top in sorted_results[:5]:
    print(classes_entries[top[0]], 'confidence:', top[1])
```

### <a name="clean-up-the-service"></a>Oczyszczanie usługi
Usunięcie usługi sieci web, obrazów i modeli (musi odbywać się w tej kolejności, ponieważ istnieją zależności).

```python
aks_service.delete()
image.delete()
registered_model.delete()
converted_model.delete()
```

## <a name="deploy-to-a-local-edge-server"></a>Wdrażanie na serwerze lokalnym krawędzi

Wszystkie [urządzeń usługi Azure Data Box Edge](https://docs.microsoft.com/azure/databox-online/data-box-edge-overview
) zawierają FPGA uruchamiania modelu.  Tylko jeden model mogą działać na FPGA, w tym samym czasie.  Aby uruchomić inny model, wystarczy wdrożyć nowy kontener. Instrukcje i kod przykładowy można znaleźć w [w tym przykładzie Azure](https://github.com/Azure-Samples/aml-hardware-accelerated-models).

## <a name="secure-fpga-web-services"></a>Zabezpieczania usług sieci web FPGA

Aby uzyskać informacje na temat zabezpieczenia usługi sieci web FPGA, zobacz [zabezpieczania usług sieci web](how-to-secure-web-service.md) dokumentu.