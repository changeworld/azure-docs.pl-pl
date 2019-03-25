---
title: Wdrażanie modeli jako usług sieci web
titleSuffix: Azure Machine Learning service
description: 'Dowiedz się, jak i gdzie wdrożyć swoje modele usługi Azure Machine Learning, w tym: Usługa Azure Container Instances, Azure Kubernetes Service, Azure IoT Edge i programowalny bramy tablic.'
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: b9dbd644aff3a41bcf38b982ebd46396ad30edca
ms.sourcegitcommit: 223604d8b6ef20a8c115ff877981ce22ada6155a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/22/2019
ms.locfileid: "58361969"
---
# <a name="deploy-models-with-the-azure-machine-learning-service"></a>Wdrażaj modele za pomocą usługi Azure Machine Learning

Zestaw SDK usługi Azure Machine Learning udostępnia uczony model jest wdrażany na kilka sposobów. W tym dokumencie informacje o sposobie wdrażania swojego modelu jako usługi sieci web w chmurze platformy Azure lub na urządzeniach usługi IoT Edge.

Można wdrażać modele do następujących celów obliczeń:

| Obliczeniowego elementu docelowego | Typ wdrożenia | Opis |
| ----- | ----- | ----- |
| [Usługa Azure Kubernetes Service (AKS)](#aks) | Wnioskowanie w czasie rzeczywistym | Dobre dla wdrożeń produkcyjnych w dużej skali. Oferuje automatyczne skalowanie i krótszych czasów reakcji. |
| [Usługa Azure obliczeniowego usługi Machine Learning (amlcompute)](#azuremlcompute) | Wnioskowanie usługi Batch | Uruchom prognoz usługi batch na bezserwerowe środowisko obliczeniowe. Obsługuje maszyny wirtualne normalnych i o niskim priorytecie. |
| [Usługa Azure Container Instances (ACI)](#aci) | Testowanie | Dobre do tworzenia i testowania. **Nie jest ona odpowiednia dla obciążeń produkcyjnych.** |
| [Azure IoT Edge](#iotedge) | (Wersja zapoznawcza) Moduł IoT | Wdrażaj modele na urządzeniach IoT. Wnioskowania odbywa się na urządzeniu. |
| [Tablica programowalny bramy (FPGA)](#fpga) | (Wersja zapoznawcza) Usługa sieci Web | Bardzo niskimi opóźnieniami dla wnioskowania w czasie rzeczywistym. |

Proces wdrażania modelu jest podobnych dla wszystkich celów obliczeń:

1. Szkolenie i zarejestruj modelu.
1. Konfigurowanie i rejestrowanie obrazu, który korzysta z modelu.
1. Wdrażania obrazu obliczeniowego elementu docelowego.
1. Testowanie wdrożenia

Poniższy klip wideo pokazuje, wdrażanie w usłudze Azure Container Instances:

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2Kwk3]


Aby uzyskać więcej informacji na temat pojęć, które są zaangażowane w przepływ pracy wdrażania, zobacz [zarządzanie, wdrażanie i monitorowanie modeli przy użyciu usługi Azure Machine Learning](concept-model-management-and-deployment.md).

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure. Jeśli nie masz subskrypcji Azure, przed rozpoczęciem utwórz bezpłatne konto. Wypróbuj [bezpłatną lub płatną wersję usługi Azure Machine Learning](https://aka.ms/AMLFree) już dziś.

- Obszar roboczy usługi Azure Machine Learning i Azure Machine Learning SDK dla język Python jest zainstalowany. Dowiedz się, jak uzyskać te wymagania wstępne przy użyciu [Utwórz obszar roboczy usługi Azure Machine Learning](setup-create-workspace.md).

- Uczonego modelu. Jeśli nie masz trenowanego modelu, wykonaj kroki w [uczyć modele](tutorial-train-models-with-aml.md) samouczka, aby uczyć i zarejestrować jeden z usługą Azure Machine Learning.

    > [!NOTE]
    > Gdy usługa Azure Machine Learning można pracować z model ogólny, który może zostać załadowany w wersji 3 języka Python, przykłady w niniejszym dokumencie pokazują, przy użyciu modelu są przechowywane w formacie pickle języka Python.
    >
    > Aby uzyskać więcej informacji na temat korzystania z modelami ONNX, zobacz [ONNX i Azure Machine Learning](how-to-build-deploy-onnx.md) dokumentu.

## <a id="registermodel"></a> Rejestrowanie uczonego modelu

Rejestr modelu jest sposób przechowywać i organizować swoje wytrenowane modele w chmurze platformy Azure. Modele są rejestrowane w obszarze roboczym usługi Azure Machine Learning. Model może być uczony przy użyciu usługi Azure Machine Learning lub innej usługi. Poniższy kod przedstawia sposób zarejestrować modelu z pliku, należy ustawić nazwę, tagi i opis:

```python
from azureml.core.model import Model

model = Model.register(model_path = "outputs/sklearn_mnist_model.pkl",
                       model_name = "sklearn_mnist",
                       tags = {"key": "0.1"},
                       description = "test",
                       workspace = ws)
```

**Szacowany czas**: Około 10 sekund.

Na przykład zarejestrować model, zobacz [szkolenie klasyfikatora obraz](tutorial-train-models-with-aml.md).

Aby uzyskać więcej informacji, zobacz dokumentację referencyjną [klasa modelu](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py).

## <a id="configureimage"></a> Tworzenie i rejestrowanie obrazu

Wdrożonych modelach są spakowane w formie obrazu. Obraz zawiera zależności niezbędne do uruchomienia modelu.

Aby uzyskać **wystąpienia kontenera platformy Azure**, **usługi Azure Kubernetes Service**, i **usługi Azure IoT Edge** wdrożeń [azureml.core.image.ContainerImage](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image.containerimage?view=azure-ml-py) klasa jest używana do tworzenia konfiguracji obrazu. Konfiguracja obrazu jest następnie używany do utworzenia nowego obrazu platformy Docker.

Poniższy kod przedstawia sposób tworzenia nowej konfiguracji obrazu:

```python
from azureml.core.image import ContainerImage

# Image configuration
image_config = ContainerImage.image_configuration(execution_script = "score.py",
                                                 runtime = "python",
                                                 conda_file = "myenv.yml"}
                                                 )
```

**Szacowany czas**: Około 10 sekund.

Ważne parametry w tym przykładzie opisano w poniższej tabeli:

| Parametr | Opis |
| ----- | ----- |
| `execution_script` | Określa skrypt w języku Python, który jest używany do odbierania żądań przesłanych do usługi. W tym przykładzie skrypt znajduje się w `score.py` pliku. Aby uzyskać więcej informacji, zobacz [wykonywania skryptu](#script) sekcji. |
| `runtime` | Wskazuje, że obraz używany jest język Python. Druga opcja to `spark-py`, która używa języka Python z platformą Apache Spark. |
| `conda_file` | Używane do udostępniania plików środowiska conda. Ten plik definiuje środowiska conda wdrożony model. Aby uzyskać więcej informacji na temat tworzenia tego pliku, zobacz [Utwórz plik środowiska (myenv.yml)](tutorial-deploy-models-with-aml.md#create-environment-file). |

Aby uzyskać przykład tworzenia konfiguracji obrazu, zobacz [wdrażanie klasyfikatora obraz](tutorial-deploy-models-with-aml.md).

Aby uzyskać więcej informacji, zobacz dokumentację referencyjną [ContainerImage, klasa](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image.containerimage?view=azure-ml-py)

### <a id="script"></a> Wykonanie skryptu

Wykonanie skryptu odbiera dane przesłane do wdrożonego obrazu i przekazuje je do modelu. Następnie pobiera odpowiedź zwrócona przez model i zwraca go do klienta. **Skrypt jest specyficzny dla modelu**; należy zrozumieć, dane, które oczekuje modelu i zwraca. Aby uzyskać przykładowy skrypt, która współdziała z model klasyfikacji obrazów, zobacz [wdrażanie klasyfikatora obraz](tutorial-deploy-models-with-aml.md).

Skrypt zawiera dwie funkcje, które ładują i uruchomić model:

* `init()`: Zazwyczaj ta funkcja ładuje modelu do obiektów globalnych. Ta funkcja jest uruchamiana tylko raz, po uruchomieniu kontenera platformy Docker.

* `run(input_data)`: Ta funkcja wykorzystuje model do przewidywania wartości w oparciu o dane wejściowe. Dane wejściowe i wyjściowe, uruchom zazwyczaj korzystają w ramach serializacji i deserializacji JSON. Może również współdziałać z nieprzetworzone dane binarne. Można przekształcać dane przed wysłaniem do modelu lub przed zwróceniem do klienta.

#### <a name="working-with-json-data"></a>Praca z danymi w formacie JSON

Poniższy przykładowy skrypt akceptuje i zwraca dane JSON. `run` Funkcja przekształca dane z formatu JSON do formatu oczekuje modelu, a następnie przekształca odpowiedź JSON przed zwróceniem:

```python
%%writefile score.py
import json
import numpy as np
import os
import pickle
from sklearn.externals import joblib
from sklearn.linear_model import LogisticRegression
from azureml.core.model import Model

# load the model
def init():
    global model
    # retrieve the path to the model file using the model name
    model_path = Model.get_model_path('sklearn_mnist')
    model = joblib.load(model_path)

# Passes data to the model and returns the prediction
def run(raw_data):
    data = np.array(json.loads(raw_data)['data'])
    # make prediction
    y_hat = model.predict(data)
    return json.dumps(y_hat.tolist())
```

#### <a name="working-with-binary-data"></a>Praca z danymi binarnymi

Jeśli model akceptuje __dane binarne__, użyj `AMLRequest`, `AMLResponse`, i `rawhttp`. Poniższy przykładowy skrypt akceptuje dane binarne i zwraca odwróconej bajtów dla żądania POST. Dla żądań GET wysyłanych zwraca pełny adres URL w treści odpowiedzi:

```python
from azureml.contrib.services.aml_request  import AMLRequest, rawhttp
from azureml.contrib.services.aml_response import AMLResponse

def init():
    print("This is init()")

# Accept and return binary data
@rawhttp
def run(request):
    print("This is run()")
    print("Request: [{0}]".format(request))
    # handle GET requests
    if request.method == 'GET':
        respBody = str.encode(request.full_path)
        return AMLResponse(respBody, 200)
    # handle POST requests
    elif request.method == 'POST':
        reqBody = request.get_data(False)
        respBody = bytearray(reqBody)
        respBody.reverse()
        respBody = bytes(respBody)
        return AMLResponse(respBody, 200)
    else:
        return AMLResponse("bad request", 500)
```

> [!IMPORTANT]
> `azureml.contrib` Przestrzeni nazw zmienia się często, gdy będziemy pracować nad usprawnienia świadczonej usługi. Jako takie wszystko w tej przestrzeni nazw powinny być uważane za wersję zapoznawczą i nie są w pełni obsługiwane przez firmę Microsoft.
>
> Jeśli zachodzi potrzeba testować jej względem swojego lokalnego środowiska deweloperskiego, można zainstalować składników w `contrib` przestrzeni nazw za pomocą następującego polecenia:
> ```shell
> pip install azureml-contrib-services
> ```

### <a id="createimage"></a> Zarejestruj obrazu

Po utworzeniu obrazu konfiguracji służy do rejestrowania obrazu. Ten obraz jest przechowywany w rejestrze kontenerów do obszaru roboczego. Po utworzeniu można wdrożyć ten sam obraz do wielu usług.

```python
# Register the image from the image configuration
image = ContainerImage.create(name = "myimage",
                              models = [model], #this is the model object
                              image_config = image_config,
                              workspace = ws
                              )
```

**Szacowany czas**: Około 3 minuty.

Obrazy są wersjonowane automatycznie podczas rejestrowania wielu obrazów o takiej samej nazwie. Na przykład pierwszy obraz zarejestrowany jako `myimage` jest przypisany identyfikator `myimage:1`. Przy następnym zarejestrowaniu obrazu jako `myimage`, identyfikator nowego obrazu jest `myimage:2`.

Aby uzyskać więcej informacji, zobacz dokumentację referencyjną [klasy ContainerImage](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image.containerimage?view=azure-ml-py).

## <a id="deploy"></a> Wdrożyć jako usługę sieci web

Gdy pojawi się do wdrożenia, proces jest nieco inne w zależności od wdrażanego na docelowym obliczeń. Użyj informacji w poniższych sekcjach, aby dowiedzieć się, jak wdrożyć:

| Obliczeniowego elementu docelowego | Typ wdrożenia | Opis |
| ----- | ----- | ----- |
| [Usługa Azure Kubernetes Service (AKS)](#aks) | Usługa sieci Web (wnioskowania w czasie rzeczywistym)| Dobre dla wdrożeń produkcyjnych w dużej skali. Oferuje automatyczne skalowanie i krótszych czasów reakcji. |
| [Usługa Azure środowiska obliczeniowego usługi ML](#azuremlcompute) | Usługa sieci Web (wnioskowanie usługi Batch)| Uruchom prognoz usługi batch na bezserwerowe środowisko obliczeniowe. Obsługuje maszyny wirtualne normalnych i o niskim priorytecie. |
| [Usługa Azure Container Instances (ACI)](#aci) | Usługa sieci Web (Tworzenie i testowanie)| Dobre do tworzenia i testowania. **Nie jest ona odpowiednia dla obciążeń produkcyjnych.** |
| [Azure IoT Edge](#iotedge) | (Wersja zapoznawcza) Moduł IoT | Wdrażaj modele na urządzeniach IoT. Wnioskowania odbywa się na urządzeniu. |
| [Tablica programowalny bramy (FPGA)](#fpga) | (Wersja zapoznawcza) Usługa sieci Web | Bardzo niskimi opóźnieniami dla wnioskowania w czasie rzeczywistym. |

> [!IMPORTANT]
> Współużytkowanie zasobów między źródłami (cors) nie jest obecnie obsługiwane w przypadku wdrażania modelu w postaci usługi sieci web.

Przykłady w tej sekcji użyto [deploy_from_image](https://docs.microsoft.com/en-us/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#deploy-from-model-workspace--name--models--image-config--deployment-config-none--deployment-target-none-), który wymaga zarejestrowania modelu i obrazów przed wykonaniem wdrożenia. Aby uzyskać więcej informacji na temat innych metod wdrażania, zobacz [wdrażanie](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#deploy-workspace--name--model-paths--image-config--deployment-config-none--deployment-target-none-) i [deploy_from_model](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#deploy-from-model-workspace--name--models--image-config--deployment-config-none--deployment-target-none-).

### <a id="aci"></a> Wdrażanie w usłudze Azure Container Instances (DEVTEST)

Użyj usługi Azure Container Instances dla wdrażając swoje modele jako usługi sieci web, jeśli jeden lub więcej z następujących warunków jest spełniony:

- Musisz szybko wdrażać i weryfikacja modelu. Wdrożenie usługi ACI zostało zakończone w ciągu mniej niż 5 minut.
- W przypadku testowania modelu, który jest w fazie projektowania. Aby wyświetlić przydziału i regionu dostępność usługi ACI, zobacz [limity przydziałów i dostępność regionów dla usługi Azure Container Instances](https://docs.microsoft.com/azure/container-instances/container-instances-quotas) dokumentu.

Do wdrożenia usługi Azure Container Instances, wykonaj następujące kroki:

1. Definiowanie konfiguracji wdrożenia. Ta konfiguracja zależy od wymagań modelu. W poniższym przykładzie zdefiniowano konfiguracji, który używa jednego rdzenia procesora CPU i 1 GB pamięci:

    [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-deploy-to-aci/how-to-deploy-to-aci.py?name=configAci)]

2. Do wdrożenia obrazu utworzonego w [utworzyć obraz](#createimage) sekcji tego dokumentu, użyj następującego kodu:

    [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-deploy-to-aci/how-to-deploy-to-aci.py?name=option3Deploy)]

    **Szacowany czas**: Mniej więcej 5 minut.

Aby uzyskać więcej informacji, zobacz dokumentację referencyjną [AciWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py) i [Webservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice?view=azure-ml-py) klasy.

### <a id="aks"></a> Wdrażanie w usłudze Azure Kubernetes Service (produkcja)

Aby wdrożyć modelu w postaci usługi sieci web o dużej skali w środowisku produkcyjnym, należy użyć usługi Azure Kubernetes Service (AKS). Można użyć istniejącego klastra AKS lub utworzyć nowe konto, przy użyciu zestawu SDK usługi Azure Machine Learning, interfejsu wiersza polecenia lub witryny Azure portal.

Tworzenie klastra AKS jest czas procesu dla obszaru roboczego. Można ponownie użyć klastra dla wielu wdrożeń.

> [!IMPORTANT]
> Po usunięciu klastra, następnie musisz utworzyć nowe klaster przy następnym zajdzie potrzeba wdrożenia.

Usługa Azure Kubernetes Service zapewnia następujące możliwości:

* Skalowanie automatyczne
* Rejestrowanie
* Zbieranie danych modelu
* Krótszych czasów reakcji dla usług sieci web
* Kończenie żądań protokołu TLS
* Authentication

#### <a name="autoscaling"></a>Skalowanie automatyczne

Skalowanie automatyczne można kontrolować przez ustawienie `autoscale_target_utilization`, `autoscale_min_replicas`, i `autoscale_max_replicas` dla usługi AKS usługi sieci web. Poniższy przykład pokazuje sposób włączania skalowania automatycznego:

```python
aks_config = AksWebservice.deploy_configuration(autoscale_enabled=True,
                                                autoscale_target_utilization=30,
                                                autoscale_min_replicas=1,
                                                autoscale_max_replicas=4)
```

Zniżki w stosunku do wykorzystania bieżącej repliki kontenera opiera się decyzji dotyczących skalowania w górę/w dół. Liczba replik, które są zajęte, (przetwarzania żądania) podzielony przez łączną liczba replik w bieżącym jest bieżące użycie. Jeśli ta liczba przekracza wykorzystanie docelowe, większa liczba replik są tworzone. Jeśli jest ona niższa, repliki są mniejsze. Domyślnie wykorzystanie docelowe wynosi 70%.

Decyzje, aby dodać repliki są wykonywane i zaimplementowane szybko (około 1 sekunda). Decyzje, aby usunąć repliki trwa dłużej (około 1 minuta). To zachowanie przechowuje bezczynności replik na minutę w przypadku, gdy dotrze nowe żądanie, że może obsłużyć.

Wymagane repliki można obliczyć przy użyciu następującego kodu:

```python
from math import ceil
# target requests per second
targetRps = 20
# time to process the request (in seconds)
reqTime = 10
# Maximum requests per container
maxReqPerContainer = 1
# target_utilization. 70% in this example
targetUtilization = .7

concurrentRequests = targetRps * reqTime / targetUtilization

# Number of container replicas
replicas = ceil(concurrentRequests / maxReqPerContainer)
```

Aby uzyskać więcej informacji na temat ustawień dotyczących `autoscale_target_utilization`, `autoscale_max_replicas`, i `autoscale_min_replicas`, zobacz [AksWebservice.deploy_configuration](https://docs.microsoft.com/en-us/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py#deploy-configuration-autoscale-enabled-none--autoscale-min-replicas-none--autoscale-max-replicas-none--autoscale-refresh-seconds-none--autoscale-target-utilization-none--collect-model-data-none--auth-enabled-none--cpu-cores-none--memory-gb-none--enable-app-insights-none--scoring-timeout-ms-none--replica-max-concurrent-requests-none--max-request-wait-time-none--num-replicas-none--primary-key-none--secondary-key-none--tags-none--properties-none--description-none-) odwołania.

#### <a name="create-a-new-cluster"></a>Tworzenie nowego klastra

Aby utworzyć nowy klaster Azure Kubernetes Service, użyj następującego kodu:

> [!IMPORTANT]
> Tworzenie klastra AKS jest czas procesu dla obszaru roboczego. Po utworzeniu można ponownie użyć klastra dla wielu wdrożeń. Jeśli możesz usunąć klaster lub grupę zasobów, która ją zawiera, następnie musi utworzysz nowy klaster przy następnym zajdzie potrzeba wdrożenia.
> Aby uzyskać [ `provisioning_configuration()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py), w przypadku wybrania wartości niestandardowe agent_count i vm_size, a następnie należy upewnić się, że agent_count pomnożona przez vm_size jest większa lub równa 12 procesorów wirtualnych. Na przykład jeśli używasz vm_size "Maszyna wirtualna Standard_D3_v2", która ma 4 procesory wirtualne, następnie należy wybrać agent_count 3 lub większą.

```python
from azureml.core.compute import AksCompute, ComputeTarget

# Use the default configuration (you can also provide parameters to customize this)
prov_config = AksCompute.provisioning_configuration()

aks_name = 'aml-aks-1'
# Create the cluster
aks_target = ComputeTarget.create(workspace = ws,
                                    name = aks_name,
                                    provisioning_configuration = prov_config)

# Wait for the create process to complete
aks_target.wait_for_completion(show_output = True)
print(aks_target.provisioning_state)
print(aks_target.provisioning_errors)
```

**Szacowany czas**: Około 20 minut.

#### <a name="use-an-existing-cluster"></a>Użyj istniejącego klastra

Jeśli masz już klaster AKS w subskrypcji platformy Azure i jest wersja 1.11. ## i ma co najmniej 12 procesorów wirtualnych, służy do wdrażania obrazu systemu. Poniższy kod przedstawia sposób dołączania istniejącej 1.11 AKS. ## klastra do swojego obszaru roboczego:

```python
from azureml.core.compute import AksCompute, ComputeTarget
# Set the resource group that contains the AKS cluster and the cluster name
resource_group = 'myresourcegroup'
cluster_name = 'mycluster'

# Attach the cluster to your workgroup
attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                         cluster_name = cluster_name)
aks_target = ComputeTarget.attach(ws, 'mycompute', attach_config)

# Wait for the operation to complete
aks_target.wait_for_completion(True)
```

**Szacowany czas**: Około 3 minuty.

Aby uzyskać więcej informacji na temat tworzenia klastra usługi AKS w taki sposób, poza zestawem SDK usługi Azure Machine Learning zobacz następujące artykuły:

* [Tworzenie klastra AKS](https://docs.microsoft.com/cli/azure/aks?toc=%2Fen-us%2Fazure%2Faks%2FTOC.json&bc=%2Fen-us%2Fazure%2Fbread%2Ftoc.json&view=azure-cli-latest#az-aks-create)
* [Tworzenie klastra AKS (portal)](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough-portal?view=azure-cli-latest)

#### <a name="deploy-the-image"></a>Wdrażanie obrazu

Do wdrożenia obrazu utworzonego w [utworzyć obraz](#createimage) sekcji tego dokumentu do klastra Azure Kubernetes serwera, użyj następującego kodu:

```python
from azureml.core.webservice import Webservice, AksWebservice

# Set configuration and service name
aks_config = AksWebservice.deploy_configuration()
aks_service_name ='aks-service-1'
# Deploy from image
service = Webservice.deploy_from_image(workspace = ws,
                                            name = aks_service_name,
                                            image = image,
                                            deployment_config = aks_config,
                                            deployment_target = aks_target)
# Wait for the deployment to complete
service.wait_for_deployment(show_output = True)
print(service.state)
```

**Szacowany czas**: Około 3 minuty.

Aby uzyskać więcej informacji, zobacz dokumentację referencyjną [AksWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py) i [Webservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice.webservice?view=azure-ml-py) klasy.

### <a id="azuremlcompute"></a> Wnioskowanie za pomocą platformy Azure środowiska obliczeniowego usługi ML

Usługa Azure ML obliczeniowych elementów docelowych są tworzone i zarządzane przez usługę Azure Machine Learning. Służy do prognozowania usługi batch z usługi Azure ML potoki.

Aby zapoznać się z przewodnikiem wnioskowania usługi batch za pomocą usługi Azure ML Compute, przeczytaj [sposobu uruchamiania prognoz usługi Batch](how-to-run-batch-predictions.md) dokumentu.


### <a id="fpga"></a> Wdrażanie bramy programowalny tablic (FPGA)

Project Brainwave sprawia, że można osiągnąć bardzo niskimi opóźnieniami dla żądań wnioskowania w czasie rzeczywistym. Project Brainwave przyspiesza głębokich sieciach neuronowych (DNN) wdrożone w tablicach programowalny bramy w chmurze platformy Azure. Najczęściej używane sieci są dostępne jako featurizers transferu uczenia, lub można dostosować za pomocą wag uczony z własnych danych.

Aby zapoznać się z przewodnikiem wdrażania modelu za pomocą Project Brainwave, zobacz [Wdróż na FPGA](how-to-deploy-fpga-web-service.md) dokumentu.

## <a name="define-schema"></a>Definiowanie schematu

Dekoratory niestandardowe mogą służyć do [OpenAPI](https://swagger.io/docs/specification/about/) specyfikacji generacji i danych wejściowych typu manipulacji podczas wdrażania usługi sieci web. W `score.py` pliku podawanej przykładowe dane wejściowe i/lub dane wyjściowe w Konstruktorze w jednym z obiektów zdefiniowanego typu, a przykład i typ są używane do automatycznego generowania schematu. Obecnie obsługiwane są następujące typy:

* `pandas`
* `numpy`
* `pyspark`
* Standard języka Python

Najpierw upewnij się, wymagane zależności dla `inference-schema` pakietu znajdują się w Twojej `env.yml` plikiem środowiska conda. W tym przykładzie użyto `numpy` typu parametru dla schematu, więc pip dodatkowe `[numpy-support]` jest również instalowany.

```python
%%writefile myenv.yml
name: project_environment
dependencies:
  - python=3.6.2
  - pip:
    - azureml-defaults
    - scikit-learn
    - inference-schema[numpy-support]
```

Następnie zmodyfikuj `score.py` plik do zaimportowania `inference-schema` pakietów. Zdefiniuj wejściowe i wyjściowe przykładowe formaty w `input_sample` i `output_sample` zmiennych, które reprezentują formatów żądania i odpowiedzi usługi sieci web. Za pomocą tych przykładów w danych wejściowych i wyjściowych dekoratory funkcji na `run()` funkcji.

```python
%%writefile score.py
import json
import numpy as np
import os
import pickle
from sklearn.externals import joblib
from sklearn.linear_model import LogisticRegression
from azureml.core.model import Model

from inference_schema.schema_decorators import input_schema, output_schema
from inference_schema.parameter_types.numpy_parameter_type import NumpyParameterType


def init():
    global model
    model_path = Model.get_model_path('sklearn_mnist')
    model = joblib.load(model_path)


input_sample = np.array([[1.8]])
output_sample = np.array([43638.88])

@input_schema('data', NumpyParameterType(input_sample))
@output_schema(NumpyParameterType(output_sample))
def run(raw_data):
    data = np.array(json.loads(raw_data)['data'])
    y_hat = model.predict(data)
    return json.dumps(y_hat.tolist())
```

Po wykonaniu normalnej rejestracji i sieci web usługi proces wdrażania obrazu za pomocą zaktualizowanego `score.py` plik, pobrać identyfikator uri struktury Swagger z usługi. Ten identyfikator uri żądania zwróci `swagger.json` pliku.

```python
service.wait_for_deployment(show_output=True)
print(service.swagger_uri)
```



Podczas tworzenia nowego obrazu, należy ręcznie zaktualizować wszystkich usług, które chcesz użyć nowego obrazu. Aby zaktualizować usługę sieci web, użyj `update` metody. Poniższy kod ilustruje sposób aktualizacji usługi sieci web w celu użycia nowego obrazu:

```python
from azureml.core.webservice import Webservice
from azureml.core.image import Image

service_name = 'aci-mnist-3'
# Retrieve existing service
service = Webservice(name = service_name, workspace = ws)

# point to a different image
new_image = Image(workspace = ws, id="myimage2:1")

# Update the image used by the service
service.update(image = new_image)
print(service.state)
```

Aby uzyskać więcej informacji, zobacz dokumentację referencyjną [Webservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py) klasy.

## <a name="test-web-service-deployments"></a>Testowanie wdrożeń usług internetowych

Aby przetestować wdrożenie usługi sieci web, można użyć `run` metody obiektu usługi sieci Web. W poniższym przykładzie ustawiono dokument JSON usługi sieci web, a wynik jest wyświetlany. Dane wysyłane musi odpowiadać oczekiwaniom modelu. W tym przykładzie format danych jest zgodny z oczekiwaną przez model choroby danych wejściowych.

```python
import json

test_sample = json.dumps({'data': [
    [1,2,3,4,5,6,7,8,9,10],
    [10,9,8,7,6,5,4,3,2,1]
]})
test_sample = bytes(test_sample,encoding = 'utf8')

prediction = service.run(input_data = test_sample)
print(prediction)
```

Usługi sieci Web jest interfejs API REST, aby można było utworzyć aplikacje klienta w różnych językach programowania. Aby uzyskać więcej informacji, zobacz [tworzenie aplikacji do używania usług sieci Web klienta](how-to-consume-web-service.md).

## <a id="update"></a> Aktualizacja usługi sieci web

Podczas tworzenia nowego obrazu, należy ręcznie zaktualizować wszystkich usług, które chcesz użyć nowego obrazu. Aby zaktualizować usługę sieci web, użyj `update` metody. Poniższy kod ilustruje sposób aktualizacji usługi sieci web w celu użycia nowego obrazu:

```python
from azureml.core.webservice import Webservice
from azureml.core.image import Image

service_name = 'aci-mnist-3'
# Retrieve existing service
service = Webservice(name = service_name, workspace = ws)

# point to a different image
new_image = Image(workspace = ws, id="myimage2:1")

# Update the image used by the service
service.update(image = new_image)
print(service.state)
```

Aby uzyskać więcej informacji, zobacz dokumentację referencyjną [Webservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py) klasy.

## <a id="iotedge"></a> Wdrażanie usługi Azure IoT Edge

Urządzenia usługi Azure IoT Edge to Linux lub urządzeń z systemem Windows, uruchamiana, środowisko uruchomieniowe usługi Azure IoT Edge. Za pomocą usługi Azure IoT Hub, można wdrożyć modele uczenia maszynowego na tych urządzeniach jako moduły usługi IoT Edge. Wdrażanie modelu na urządzeniu usługi IoT Edge umożliwia urządzeniu do korzystania z modelu bezpośrednio, zamiast wysyłać dane do chmury na potrzeby przetwarzania. Otrzymujesz krótszy czas reakcji i mniej transferu danych.

Moduły usługi IoT Edge platformy Azure są wdrażane na urządzeniu z rejestru kontenerów. Po utworzeniu obrazu z modelu znajduje się w rejestrze kontenerów do obszaru roboczego.

> [!IMPORTANT]
> Informacje przedstawione w tej sekcji założono, że czytelnik zna już modułami usługi Azure IoT Hub i Azure IoT Edge. Niektóre informacje w tej sekcji jest specyficzne dla usługi Azure Machine Learning, większość procesu wdrażania na urządzeniu usługi edge odbywa się w usłudze Azure IoT.
>
> Jeśli nie jesteś zaznajomiony z usługi Azure IoT, zobacz [Azure IoT Edge — podstawy](https://docs.microsoft.com/azure/iot-fundamentals/) i [usługi Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/) podstawowe informacje. Następnie należy użyć innych linków w tej sekcji, aby dowiedzieć się więcej na temat określonych operacji.

### <a name="set-up-your-environment"></a>Konfigurowanie środowiska

* Środowisko deweloperskie. Aby uzyskać więcej informacji, zobacz [sposób konfigurowania środowiska deweloperskiego](how-to-configure-environment.md) dokumentu.

* [Usługi Azure IoT Hub](../../iot-hub/iot-hub-create-through-portal.md) w subskrypcji platformy Azure.

* Uczonego modelu. Na przykład sposobu uczenia modelu zobacz [Wytrenuj model klasyfikacji obrazów za pomocą usługi Azure Machine Learning](tutorial-train-models-with-aml.md) dokumentu. Wstępnie przeszkolonych modelu jest dostępna w [zestaw narzędzi SI dla repozytorium Azure IoT Edge w witrynie GitHub](https://github.com/Azure/ai-toolkit-iot-edge/tree/master/IoT%20Edge%20anomaly%20detection%20tutorial).

### <a id="getcontainer"></a> Pobieranie poświadczeń rejestru kontenera

Aby wdrożyć moduł usługi IoT Edge na urządzeniu, usługi Azure IoT potrzebuje poświadczeń dla rejestru kontenerów, w którym usługi Azure Machine Learning, są przechowywane obrazy platformy docker w.

Poświadczenia można uzyskać na dwa sposoby:

+ **W witrynie Azure portal**:

  1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/signin/index).

  1. Przejdź do obszaru roboczego usługi Azure Machine Learning i wybierz __Przegląd__. Aby przejść do ustawienia usługi container registry, wybierz __rejestru__ łącza.

     ![Obraz wpisu rejestru kontenerów](./media/how-to-deploy-and-where/findregisteredcontainer.png)

  1. Jeden raz w rejestrze kontenerów należy wybrać **klucze dostępu** , a następnie włącz użytkownika administratora.

     ![Obraz przedstawiający ekran klucze dostępu](./media/how-to-deploy-and-where/findaccesskey.png)

  1. Zapisz wartości **serwer logowania**, **username**, i **hasło**.

+ **Za pomocą skryptu języka Python**:

  1. Użyj następującego skryptu języka Python po kodzie, na których uruchomiono powyżej, aby utworzyć kontener:

     ```python
     # Getting your container details
     container_reg = ws.get_details()["containerRegistry"]
     reg_name=container_reg.split("/")[-1]
     container_url = "\"" + image.image_location + "\","
     subscription_id = ws.subscription_id
     from azure.mgmt.containerregistry import ContainerRegistryManagementClient
     from azure.mgmt import containerregistry
     client = ContainerRegistryManagementClient(ws._auth,subscription_id)
     result= client.registries.list_credentials(resource_group_name, reg_name, custom_headers=None, raw=False)
     username = result.username
     password = result.passwords[0].value
     print('ContainerURL{}'.format(image.image_location))
     print('Servername: {}'.format(reg_name))
     print('Username: {}'.format(username))
     print('Password: {}'.format(password))
     ```
  1. Zapisz te wartości do obiektu ContainerURL, servername, username i password.

     Te poświadczenia są niezbędne do zapewnienia usługi IoT Edge dostęp urządzenia do obrazów w prywatnego rejestru kontenera.

### <a name="prepare-the-iot-device"></a>Przygotuj urządzenie IoT

Zarejestruj urządzenie w usłudze Azure IoT Hub, a następnie zainstalować środowisko uruchomieniowe usługi IoT Edge na urządzeniu. Jeśli nie znasz tego procesu, zobacz [Szybki Start: Wdrożenia pierwszego modułu usługi IoT Edge na urządzeniu Linux x64](../../iot-edge/quickstart-linux.md).

Inne sposoby rejestrowania urządzenia są:

* [Azure Portal](https://docs.microsoft.com/azure/iot-edge/how-to-register-device-portal)
* [Interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/azure/iot-edge/how-to-register-device-cli)
* [Visual Studio Code](https://docs.microsoft.com/azure/iot-edge/how-to-register-device-vscode)

### <a name="deploy-the-model-to-the-device"></a>Wdrażanie modelu na urządzeniu

Aby wdrożyć model do urządzenia, należy użyć rejestru informacje zebrane w [pobieranie poświadczeń rejestru kontenera](#getcontainer) sekcję wdrażanie modułu kroków dla modułów usługi IoT Edge. Na przykład, gdy [modułów wdrażania usługi Azure IoT Edge w witrynie Azure portal](../../iot-edge/how-to-deploy-modules-portal.md), należy skonfigurować __ustawień rejestru__ dla tego urządzenia. Użyj __serwer logowania__, __username__, i __hasło__ dla rejestru kontenera obszaru roboczego.

Można także wdrożyć za pomocą [wiersza polecenia platformy Azure](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-modules-cli) i [programu Visual Studio Code](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-modules-vscode).

## <a name="clean-up"></a>Czyszczenie

Aby usunąć wdrożonej usługi sieci web, użyj `service.delete()`.

Aby usunąć obrazu, użyj `image.delete()`.

Aby usunąć zarejestrowanego modelu, użyj `model.delete()`.

Aby uzyskać więcej informacji, zobacz dokumentację referencyjną [WebService.delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#delete--), [Image.delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image.image(class)?view=azure-ml-py#delete--), i [Model.delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#delete--).

## <a name="next-steps"></a>Kolejne kroki

* [Rozwiązywanie problemów z wdrożenia](how-to-troubleshoot-deployment.md)
* [Zabezpieczania usług sieci web Azure Machine Learning przy użyciu protokołu SSL](how-to-secure-web-service.md)
* [Korzystanie z modelu uczenia Maszynowego, wdrożyć jako usługę sieci web](how-to-consume-web-service.md)
* [Jak uruchomić prognoz usługi batch](how-to-run-batch-predictions.md)
* [Monitoruj swoje modele usługi Azure Machine Learning z usługą Application Insights](how-to-enable-app-insights.md)
* [Zbieranie danych dla modeli w środowisku produkcyjnym](how-to-enable-data-collection.md)
* [Usługa Azure Machine Learning zestawu SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)
* [Usługa Azure Machine Learning za pomocą usługi Azure Virtual Networks](how-to-enable-virtual-network.md)
* [Najlepsze rozwiązania dotyczące tworzenia systemów rekomendacji](https://github.com/Microsoft/Recommenders)
* [Tworzenie interfejsu API zaleceń w czasie rzeczywistym na platformie Azure](https://docs.microsoft.com/azure/architecture/reference-architectures/ai/real-time-recommendation)
