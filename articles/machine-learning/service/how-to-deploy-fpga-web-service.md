---
title: Wdrażanie modeli w układów FPGA
titleSuffix: Azure Machine Learning service
description: Dowiedz się, jak wdrożyć usługę sieci web przy użyciu modelu z systemem FPGA za pomocą usługi Azure Machine Learning do wnioskowania bardzo niskimi opóźnieniami.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: tedway
author: tedway
ms.date: 1/29/2019
ms.custom: seodec18
ms.openlocfilehash: 7aa0e11ed47219829830369d17b300270d3fbffb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60819440"
---
# <a name="deploy-a-model-as-a-web-service-on-an-fpga-with-azure-machine-learning-service"></a>Wdrażanie modelu jako usługi sieci web na FPGA za pomocą usługi Azure Machine Learning

Model można wdrożyć jako usługę sieci web, na [pola Tablice bramek programowane (układów FPGA)](concept-accelerate-with-fpgas.md).  Za pomocą układów FPGA zapewnia wnioskowania niezwykle małych opóźnień, nawet w przypadku rozmiar jednej partii.  Obecnie dostępne są następujące modele:
  - ResNet 50
  - ResNet 152
  - DenseNet-121
  - VGG-16   

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli nie masz subskrypcji Azure, przed rozpoczęciem utwórz bezpłatne konto. Wypróbuj [bezpłatną lub płatną wersję usługi Azure Machine Learning](https://aka.ms/AMLFree) już dziś.

- Obszar roboczy usługi Azure Machine Learning i Azure Machine Learning SDK dla język Python jest zainstalowany. Dowiedz się, jak uzyskać te wymagania wstępne przy użyciu [sposób konfigurowania środowiska deweloperskiego](how-to-configure-environment.md) dokumentu.
 
  - Obszar roboczy musi być zapisana w *wschodnie stany USA 2* regionu.

  - Zainstaluj dodatkowe contrib:

    ```shell
    pip install --upgrade azureml-sdk[contrib]
    ```

  - Obecnie tylko tensorflow wersji < = 1.10 jest obsługiwany, więc go zainstalować po zakończeniu wszystkich innych instalacji:

    ```shell
    pip install "tensorflow==1.10"
    ```

## <a name="create-and-deploy-your-model"></a>Tworzenie i wdrażanie modelu
Tworzenie potoku w celu przetwarzania wstępnego obrazu wejściowego, ułatwiają funkcji przy użyciu siecią ResNet-50 na FPGA, a następnie uruchom funkcji, za pomocą skonfigurowanych pod kątem w zestawie danych sieci ImageNet klasyfikatora.

Postępuj zgodnie z instrukcjami, aby:

* Zdefiniuj potoku modelu
* Wdrażanie modelu
* Używanie wdrożonego modelu
* Usuwanie wdrożonych usług

> [!IMPORTANT]
> W celu zoptymalizowania opóźnienia i przepływności, klienta należy w tym samym regionie platformy Azure jako punktu końcowego.  Obecnie interfejsy API są tworzone w regionie wschodnie stany USA Azure.



### <a name="preprocess-image"></a>Wstępne przetwarzanie obrazu
Pierwszy etap potoku jest przetwarzanie wstępne obrazów.

```python
import os
import tensorflow as tf

# Input images as a two-dimensional tensor containing an arbitrary number of images represented a strings
import azureml.contrib.brainwave.models.utils as utils
in_images = tf.placeholder(tf.string)
image_tensors = utils.preprocess_array(in_images)
print(image_tensors.shape)
```

### <a name="add-featurizer"></a>Dodaj Featurized
Inicjowanie modelu i Pobierz punkt kontrolny TensorFlow wykonywanie kwantyzowanych wersji ResNet50 ma być używany jako featurized.

```python
from azureml.contrib.brainwave.models import QuantizedResnet50
model_path = os.path.expanduser('~/models')
model = QuantizedResnet50(model_path, is_frozen = True)
feature_tensor = model.import_graph_def(image_tensors)
print(model.version)
print(feature_tensor.name)
print(feature_tensor.shape)
```

### <a name="add-classifier"></a>Dodaj klasyfikatora
Po zapoznaniu to klasyfikatora na zestawie danych sieci ImageNet.

```python
classifier_output = model.get_default_classifier(feature_tensor)
```

### <a name="create-service-definition"></a>Tworzenie definicji usługi
Skoro zdefiniowano wstępnego przetwarzania obrazu, featurized i klasyfikatora, który jest uruchamiany w usłudze, można utworzyć definicji usługi. Definicja usługi to zbiór plików wygenerowany na podstawie modelu, który jest wdrożony w usłudze FPGA. Definicja usługi składa się z potoku. Potok jest serię etapów, które są uruchamiane w kolejności.  Etapy TensorFlow, Keras etapów i etapy BrainWave są obsługiwane.  Etapy są uruchamiane w kolejności od usługi, z danymi wyjściowymi każdy z etapów staje się dane wejściowe w kolejnym etapie.

Aby utworzyć etapu TensorFlow, określ sesję zawierający wykres (w tym przypadku wykres domyślne są używane) i dane wejściowe i wyjściowe tensors na tym etapie.  Te informacje jest używany, aby zapisać wykres, dzięki czemu mogą być uruchamiane w usłudze.

```python
from azureml.contrib.brainwave.pipeline import ModelDefinition, TensorflowStage, BrainWaveStage

save_path = os.path.expanduser('~/models/save')
model_def_path = os.path.join(save_path, 'model_def.zip')

model_def = ModelDefinition()
with tf.Session() as sess:
    model_def.pipeline.append(TensorflowStage(sess, in_images, image_tensors))
    model_def.pipeline.append(BrainWaveStage(sess, model))
    model_def.pipeline.append(TensorflowStage(sess, feature_tensor, classifier_output))
    model_def.save(model_def_path)
    print(model_def_path)
```

### <a name="deploy-model"></a>Wdrażanie modelu
Tworzenie usługi na podstawie definicji usługi.  Obszar roboczy musi znajdować się w lokalizacji wschodnie stany USA 2.

```python
from azureml.core import Workspace

ws = Workspace.from_config()
print(ws.name, ws.resource_group, ws.location, ws.subscription_id, sep = '\n')

from azureml.core.model import Model
model_name = "resnet-50-rtai"
registered_model = Model.register(ws, model_def_path, model_name)

from azureml.core.webservice import Webservice
from azureml.exceptions import WebserviceException
from azureml.contrib.brainwave import BrainwaveWebservice, BrainwaveImage
service_name = "imagenet-infer"
service = None
try:
    service = Webservice(ws, service_name)
except WebserviceException:
    image_config = BrainwaveImage.image_configuration()
    deployment_config = BrainwaveWebservice.deploy_configuration()
    service = Webservice.deploy_from_model(ws, service_name, [registered_model], image_config, deployment_config)
    service.wait_for_deployment(True)
```

### <a name="test-the-service"></a>Testowanie usługi
Wysyłanie obrazu do interfejsu API i testowanie odpowiedzi, należy dodać mapowanie z Identyfikatorem klasy danych wyjściowych do sieci ImageNet nazwy klasy.

```python
import requests
classes_entries = requests.get("https://raw.githubusercontent.com/Lasagne/Recipes/master/examples/resnet50/imagenet_classes.txt").text.splitlines()
```

Wywołanie usługi i zastąp nazwę pliku "your image.jpg" poniżej obrazu z Twojego komputera. 

```python
with open('your-image.jpg') as f:
    results = service.run(f)
# map results [class_id] => [confidence]
results = enumerate(results)
# sort results by confidence
sorted_results = sorted(results, key=lambda x: x[1], reverse=True)
# print top 5 results
for top in sorted_results[:5]:
    print(classes_entries[top[0]], 'confidence:', top[1])
``` 

### <a name="clean-up-service"></a>Czyszczenie usługi
Usuń usługę.

```python
service.delete()
    
registered_model.delete()
```

## <a name="secure-fpga-web-services"></a>Zabezpieczania usług sieci web FPGA

Aby uzyskać informacje na temat zabezpieczenia usługi sieci web FPGA, zobacz [zabezpieczania usług sieci web](how-to-secure-web-service.md) dokumentu.


## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, jak [zużywania modelu uczenia Maszynowego wdrożyć jako usługę sieci web](how-to-consume-web-service.md).
