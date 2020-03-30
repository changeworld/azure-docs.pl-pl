---
title: Co to są FPGA - jak wdrożyć
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak wdrożyć usługę sieci web z modelem uruchomionym na fpga z usługą Azure Machine Learning dla wnioskowania o bardzo małych opóźnieniach.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: larryfr
ms.author: jordane
author: jpe316
ms.date: 03/05/2020
ms.custom: seodec18
ms.openlocfilehash: 8cb6cf49e302122849dc2402bcff008e72e15608
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79472362"
---
# <a name="what-are-field-programmable-gate-arrays-fpga-and-how-to-deploy"></a>Czym są programowalne w terenie tablice bramek (FPGA) i jak wdrożyć
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

W tym artykule przedstawiono wprowadzenie do tablic bramy programowalne w terenie (FPGA) i pokazano, jak wdrożyć modele przy użyciu usługi Azure Machine Learning do usługi Azure FPGA.

Układy FPGA zawierają tablicę programowalnych bloków logicznych i hierarchię możliwych do ponownego skonfigurowania wzajemnych połączeń. Połączenia te umożliwiają konfigurację tych bloków na różne sposoby po zakończeniu produkcji. W porównaniu z innymi chipami, układy FPGA zapewniają połączenie programowalności i wydajności.

## <a name="fpgas-vs-cpu-gpu-and-asic"></a>Układy FPGA a cpu, GPU i ASIC

Na poniższym diagramie i tabeli pokazano, jak układy FPGA porównują się do innych procesorów.

![Diagram porównania fpga usługi Azure Machine Learning](./media/how-to-deploy-fpga-web-service/azure-machine-learning-fpga-comparison.png)

|Procesor||Opis|
|---|:-------:|------|
|Układy scalonowe specyficzne dla danego zastosowania|Asics|Obwody niestandardowe, takie jak jednostki procesora TensorFlow (TPU) firmy Google, zapewniają najwyższą wydajność. Nie można ich ponownie skonfigurować w miarę zmiany potrzeb.|
|Tablice bramek programowalnych w terenie|Układy FPGA|Układy FPGA, takie jak te dostępne na platformie Azure, zapewniają wydajność zbliżoną do asic. Są one również elastyczne i reconfigurable w czasie, aby zaimplementować nową logikę.|
|Jednostki przetwarzania grafiki|Procesory GPU|Popularny wybór dla obliczeń SI. Procesory gpu oferują możliwości przetwarzania równoległego, dzięki czemu są szybsze w renderowaniu obrazów niż procesory.|
|Jednostki centralne|Procesory CPU|Procesory ogólnego przeznaczenia, których wydajność nie jest idealna do przetwarzania grafiki i wideo.|

Układy FPGA na platformie Azure są oparte na urządzeniach FPGA firmy Intel, których analitycy danych i deweloperzy używają do przyspieszania obliczeń sztucznej inteligencji w czasie rzeczywistym. Ta architektura obsługująca FPGA oferuje wydajność, elastyczność i skalę i jest dostępna na platformie Azure.

Układy FPGA umożliwiają osiągnięcie małych opóźnień w przypadku żądań wnioskowania (lub oceniania modelu) w czasie rzeczywistym. Asynchroniczne żądania (przetwarzanie wsadowe) nie są potrzebne. Przetwarzanie wsadowe może spowodować opóźnienie, ponieważ więcej danych musi być przetwarzanych. Implementacje jednostek przetwarzania neuronowego nie wymagają przetwarzania wsadowego; w związku z tym opóźnienie może być wiele razy niższe, w porównaniu do procesorów CPU i procesorów GPU.

### <a name="reconfigurable-power"></a>Moc rekonfigurowalna
Można ponownie skonfigurować układy FPGA dla różnych typów modeli uczenia maszynowego. Ta elastyczność ułatwia przyspieszenie aplikacji w oparciu o najbardziej optymalną precyzję numeryczną i używany model pamięci. Ponieważ układy FPGA można rekonfigurować, można zachować aktualną wartość z wymaganiami szybko zmieniających się algorytmów sztucznej inteligencji.

## <a name="whats-supported-on-azure"></a>Co jest obsługiwane na platformie Azure
Microsoft Azure to największa na świecie inwestycja w chmurę w układy FPGA. Korzystając z tej architektury sprzętowej obsługującej technologię FPGA, przeszkolone sieci neuronowe działają szybko i z mniejszym opóźnieniem. Platforma Azure może zrównować wstępnie przeszkolonych sieci głębokich sieci neuronowych (DNN) w układach FPGA w celu skalowania w poziomie usługi. Numery DN można wstępnie wyszkolić, jako głęboki featurizer do nauki transferu lub dostrojony do zaktualizowanych wag.

Układy FPGA na platformie Azure obsługuje:

+ Scenariusze klasyfikacji i rozpoznawania obrazów
+ Wdrożenie TensorFlow
+ Sprzęt Intel FPGA

Te modele DNN są obecnie dostępne:
  - ResNet 50
  - ResNet 152
  - GęstyNet-121
  - VGG-16
  - SSD-VGG

Układy FPGA są dostępne w następujących regionach platformy Azure:
  - Wschodnie stany USA
  - Azja Południowo-Wschodnia
  - Europa Zachodnia
  - Zachodnie stany USA 2

> [!IMPORTANT]
> Aby zoptymalizować opóźnienie i przepływność, klient wysyłający dane do modelu FPGA powinien znajdować się w jednym z powyższych regionów (w tym, w których wdrożono model).

**Rodzina maszyn wirtualnych platformy Azure usługi PbS** zawiera układy FPGA Intel Arria 10. Będzie ono wyświetlane jako "Standardowe procesory wirtualne rodziny książek telefonicznych" podczas sprawdzania alokacji przydziału platformy Azure. Maszyna wirtualna PB6 ma sześć procesorów wirtualnych i jedną fpga i zostanie automatycznie zainicjowana przez usługę Azure ML w ramach wdrażania modelu w fpga. Jest on używany tylko z usługą Azure ML i nie można uruchomić dowolnego strumieni bitów. Na przykład nie będzie można flashować FPGA z strumieniami bitów do szyfrowania, kodowania itp.

### <a name="scenarios-and-applications"></a>Scenariusze i aplikacje

Układy FPGA platformy Azure są zintegrowane z usługą Azure Machine Learning. Firma Microsoft używa układów FPGA do oceny DNN, rankingu wyszukiwania Bing i przyspieszania sieci zdefiniowanej programowo (SDN) w celu zmniejszenia opóźnień przy jednoczesnym zwalnianiu procesorów do innych zadań.

W następujących scenariuszach użyto układów FPGA:
+ [Zautomatyzowany system kontroli optycznej](https://blogs.microsoft.com/ai/build-2018-project-brainwave/)

+ [Mapowanie pokrycia terenu](https://blogs.technet.microsoft.com/machinelearning/2018/05/29/how-to-use-fpgas-for-deep-learning-inference-to-perform-land-cover-mapping-on-terabytes-of-aerial-images/)

## <a name="example-deploy-models-on-fpgas"></a>Przykład: Wdrażanie modeli na układach FPGA

Model można wdrożyć jako usługę sieci web w układach FPGA za pomocą modeli przyspieszanych sprzętowo usługi Azure Machine Learning. Za pomocą układów FPGA zapewnia wnioskowanie o bardzo niskim opóźnieniu, nawet przy jednym rozmiarze partii. Wnioskowanie lub ocenianie modelu jest fazą, w której wdrożony model jest używany do przewidywania, najczęściej na danych produkcyjnych.

### <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure.  Jeśli nie masz subskrypcji, przed rozpoczęciem utwórz bezpłatne konto. Wypróbuj [bezpłatną lub płatną wersję usługi Azure Machine Learning](https://aka.ms/AMLFree) już dziś.

- fpga. Użyj interfejsu wiersza polecenia platformy Azure, aby sprawdzić, czy masz przydział:

    ```azurecli-interactive
    az vm list-usage --location "eastus" -o table --query "[?localName=='Standard PBS Family vCPUs']"
    ```

    > [!TIP]
    > Inne możliwe lokalizacje ``southeastasia`` ``westeurope``to ``westus2``, i .

    Polecenie zwraca tekst podobny do następującego:

    ```text
    CurrentValue    Limit    LocalName
    --------------  -------  -------------------------
    0               6        Standard PBS Family vCPUs
    ```

    Upewnij się, że masz co najmniej 6 procesorów wirtualnych w obszarze __CurrentValue__.

    Jeśli nie masz przydziału, prześlij żądanie w pliku [https://aka.ms/accelerateAI](https://aka.ms/accelerateAI).

- Zainstalowany obszar roboczy usługi Azure Machine Learning i zestaw SDK usługi Azure Machine Learning dla języka Python. Aby uzyskać więcej informacji, zobacz [Tworzenie obszaru roboczego](how-to-manage-workspace.md).
 
- Moduł SDK języka Python dla modeli przyspieszanych sprzętowo:

    ```bash
    pip install --upgrade azureml-accel-models
    ```


## <a name="1-create-and-containerize-models"></a>1. Tworzenie i konteneryzowanie modeli

W tym dokumencie opisano, jak utworzyć wykres TensorFlow do wstępnego przetwarzania obrazu wejściowego, uczynić go featurizer przy użyciu ResNet 50 na FPGA, a następnie uruchomić funkcje za pośrednictwem klasyfikatora przeszkolonych w zestawie danych ImageNet.

Postępuj zgodnie z instrukcjami, aby:

* Definiowanie modelu TensorFlow
* Konwertowanie modelu
* Wdrażanie modelu
* Korzystanie z wdrożonego modelu
* Usuwanie wdrożonych usług

Użyj zestawu [SDK usługi Azure Machine Learning dla języka Python,](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) aby utworzyć definicję usługi. Definicja usługi to plik opisujący potok wykresów (dane wejściowe, featurizer i klasyfikator) oparty na TensorFlow. Polecenie wdrażania automatycznie kompresuje definicję i wykresy do pliku ZIP i przekazuje zip do magazynu obiektów Blob platformy Azure. DNN jest już wdrożony do uruchomienia na FPGA.

### <a name="load-azure-machine-learning-workspace"></a>Załaduj obszar roboczy usługi Azure Machine Learning

Załaduj swój obszar roboczy usługi Azure Machine Learning.

```python
import os
import tensorflow as tf

from azureml.core import Workspace

ws = Workspace.from_config()
print(ws.name, ws.resource_group, ws.location, ws.subscription_id, sep='\n')
```

### <a name="preprocess-image"></a>Obraz przetwarzania wstępnego

Dane wejściowe do usługi sieci web to obraz JPEG.  Pierwszym krokiem jest dekodowanie obrazu JPEG i wstępne przetwarzanie go.  Obrazy JPEG są traktowane jako ciągi, a wynik są tensors, które będą dane wejściowe do modelu ResNet 50.

```python
# Input images as a two-dimensional tensor containing an arbitrary number of images represented a strings
import azureml.accel.models.utils as utils
tf.reset_default_graph()

in_images = tf.placeholder(tf.string)
image_tensors = utils.preprocess_array(in_images)
print(image_tensors.shape)
```

### <a name="load-featurizer"></a>Załaduj featurizer

Zainicjować model i pobrać punkt kontrolny TensorFlow ilościowej wersji ResNet50 do użycia jako featurizer.  Możesz zastąpić "QuantizedResnet50" w poniższym fragmentie kodu, importując inne głębokie sieci neuronowe:

- QuantizedResnet152
- QuantizedVgg16
- Gęsty 121

```python
from azureml.accel.models import QuantizedResnet50
save_path = os.path.expanduser('~/models')
model_graph = QuantizedResnet50(save_path, is_frozen=True)
feature_tensor = model_graph.import_graph_def(image_tensors)
print(model_graph.version)
print(feature_tensor.name)
print(feature_tensor.shape)
```

### <a name="add-classifier"></a>Dodaj klasyfikatora

Ten klasyfikator został przeszkolony w zestawie danych ImageNet.  Przykłady przenoszenia uczenia się i szkolenia dostosowanych wag są dostępne w zestawie [przykładowych notesów.](https://aka.ms/aml-notebooks)

```python
classifier_output = model_graph.get_default_classifier(feature_tensor)
print(classifier_output)
```

### <a name="save-the-model"></a>Zapisywanie modelu

Teraz, gdy preprocesor, ResNet 50 featurizer i klasyfikator zostały załadowane, zapisz wykres i skojarzone zmienne jako model.

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

### <a name="save-input-and-output-tensors"></a>Zapisywanie tensorów wejściowych i wyjściowych
Tensory wejściowe i wyjściowe, które zostały utworzone podczas kroków przetwarzania wstępnego i klasyfikatora, będą potrzebne do konwersji i wnioskowania modelu.

```python
input_tensors = in_images.name
output_tensors = classifier_output.name

print(input_tensors)
print(output_tensors)
```

> [!IMPORTANT]
> Zapisz tensory wejściowe i wyjściowe, ponieważ będą one potrzebne do konwersji modelu i wnioskowania żądań.

Dostępne modele i odpowiadające im domyślne tensory wyjściowe klasyfikatora znajdują się poniżej, czyli co służy do wnioskowania, jeśli użyto domyślnego klasyfikatora.

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

[Zarejestruj](concept-model-management-and-deployment.md) model przy użyciu zestawu SDK z plikiem ZIP w magazynie obiektów Blob platformy Azure. Dodawanie tagów i innych metadanych dotyczących modelu ułatwia śledzenie przeszkolonych modeli.

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

### <a name="convert-model"></a>Konwertowanie modelu

Konwertuj wykres TensorFlow na format Otwartej wymiany sieci neuronowej[(ONNX).](https://onnx.ai/)  Należy podać nazwy tensorów wejściowych i wyjściowych, a te nazwy będą używane przez klienta podczas korzystania z usługi sieci web.

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

Przekonwertowany model i wszystkie zależności są dodawane do obrazu platformy Docker.  Ten obraz platformy Docker można następnie wdrożyć i utworzyć wystąpienia.  Obsługiwane obiekty docelowe wdrażania obejmują usługę AKS w chmurze lub urządzenie brzegowe, takie jak [usługa Azure Data Box Edge.](https://docs.microsoft.com/azure/databox-online/data-box-edge-overview)  Możesz również dodać tagi i opisy zarejestrowanego obrazu platformy Docker.

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

Lista obrazów według tagu i uzyskać szczegółowe dzienniki dla debugowania.

```python
for i in Image.list(workspace=ws):
    print('{}(v.{} [{}]) stored at {} with build log {}'.format(
        i.name, i.version, i.creation_state, i.image_location, i.image_build_log_uri))
```

## <a name="2-deploy-to-cloud-or-edge"></a>2. Wdrażanie w chmurze lub na krawędzi

### <a name="deploy-to-the-cloud"></a>Wdrażanie w chmurze

Aby wdrożyć model jako produkcyjną usługę sieci web na dużą skalę, użyj usługi Azure Kubernetes Service (AKS). Możesz utworzyć nowy przy użyciu usługi Azure Machine Learning SDK, CLI lub [Azure Machine Learning studio.](https://ml.azure.com)

```python
from azureml.core.compute import AksCompute, ComputeTarget

# Specify the Standard_PB6s Azure VM and location. Values for location may be "eastus", "southeastasia", "westeurope", or "westus2". If no value is specified, the default is "eastus".
prov_config = AksCompute.provisioning_configuration(vm_size = "Standard_PB6s",
                                                    agent_count = 1,
                                                    location = "eastus")

aks_name = 'my-aks-cluster'
# Create the cluster
aks_target = ComputeTarget.create(workspace=ws,
                                  name=aks_name,
                                  provisioning_configuration=prov_config)
```

Wdrożenie usługi AKS może potrwać około 15 minut.  Sprawdź, czy wdrożenie zakończyło się pomyślnie.

```python
aks_target.wait_for_completion(show_output=True)
print(aks_target.provisioning_state)
print(aks_target.provisioning_errors)
```

Wdrażanie kontenera w klastrze AKS.
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
Obraz platformy Docker obsługuje gRPC i TensorFlow obsługujących "przewidzieć" API.  Użyj przykładowego klienta do wywołania obrazu platformy Docker, aby uzyskać prognoz z modelu.  Przykładowy kod klienta jest dostępny:
- [Python](https://github.com/Azure/aml-real-time-ai/blob/master/pythonlib/amlrealtimeai/client.py)
- [C #](https://github.com/Azure/aml-real-time-ai/blob/master/sample-clients/csharp)

Jeśli chcesz użyć TensorFlow Serving, możesz [pobrać przykładowego klienta](https://www.tensorflow.org/serving/setup).

```python
# Using the grpc client in Azure ML Accelerated Models SDK package
from azureml.accel import PredictionClient

address = aks_service.scoring_uri
ssl_enabled = address.startswith("https")
address = address[address.find('/')+2:].strip('/')
port = 443 if ssl_enabled else 80

# Initialize Azure ML Accelerated Models client
client = PredictionClient(address=address,
                          port=port,
                          use_ssl=ssl_enabled,
                          service_name=aks_service.name)
```

Ponieważ ten klasyfikator został przeszkolony w zestawie danych [ImageNet,](http://www.image-net.org/) mapuj klasy na etykiety czytelne dla człowieka.

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

### <a name="clean-up-the-service"></a>Oczyszczanie usługi
Usuń usługę sieci web, obraz i model (należy wykonać w tej kolejności, ponieważ istnieją zależności).

```python
aks_service.delete()
aks_target.delete()
image.delete()
registered_model.delete()
converted_model.delete()
```

### <a name="deploy-to-a-local-edge-server"></a>Wdrażanie na lokalnym serwerze brzegowym

Wszystkie [urządzenia usługi Azure Data Box Edge](https://docs.microsoft.com/azure/databox-online/data-box-edge-overview
) zawierają fpga do uruchamiania modelu.  Tylko jeden model może być uruchomiony na FPGA w tym samym czasie.  Aby uruchomić inny model, wystarczy wdrożyć nowy kontener. Instrukcje i przykładowy kod można znaleźć w [tym przykładzie platformy Azure.](https://github.com/Azure-Samples/aml-hardware-accelerated-models)

## <a name="secure-fpga-web-services"></a>Bezpieczne usługi internetowe FPGA

Aby zabezpieczyć usługi sieci web FPGA, zobacz dokument [Bezpieczne usługi sieci Web.](how-to-secure-web-service.md)

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z tymi notesami, filmami i blogami:

+ Kilka [przykładowych notesów](https://aka.ms/aml-accel-models-notebooks)

+ [Sprzęt hiperskali: ml w skali na górze platformy Azure + FPGA: Kompilacja 2018 (wideo)](https://channel9.msdn.com/events/Build/2018/BRK3202)

+ [Wewnątrz konfigurowalnej chmury opartej na FPGA firmy Microsoft (wideo)](https://channel9.msdn.com/Events/Build/2017/B8063)

+ [Projekt Brainwave dla AI w czasie rzeczywistym: strona główna projektu](https://www.microsoft.com/research/project/project-brainwave/)
