---
title: Wdrażaj usługi sieci web do usługi Azure Container Instances (ACI) — usługa Azure Machine Learning
description: Dowiedz się, jak wdrożyć nauczony model jako usługę sieci web w usłudze Azure Container Instances (ACI) za pomocą usługi Azure Machine Learning. W tym artykule przedstawiono trzy różne sposoby wdrażania modelu w usłudze ACI. Różnią się liczba wierszy kodu i formant, który ma w częściach nazewnictwa wdrożenia.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: raymondl
author: raymondlaghaeian
ms.reviewer: sgilley
ms.date: 09/24/2018
ms.openlocfilehash: 8a736516a598eee051b416834d2b737211e66b96
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/19/2018
ms.locfileid: "49429467"
---
# <a name="deploy-web-services-to-azure-container-instances"></a>Wdrażaj usługi sieci web w usłudze Azure Container Instances 

Uczonego modelu można wdrożyć jako usługę sieci web, na [usługi Azure Container Instances](https://azure.microsoft.com/services/container-instances/) (ACI), [usługi Azure Kubernetes Service](https://azure.microsoft.com/services/kubernetes-service/) (AKS), IoT krawędzi urządzenia, lub [pola Tablice bramek programowane (układów FPGA)](concept-accelerate-with-fpgas.md) 

ACI jest zazwyczaj tańsze niż usługi AKS i można skonfigurować w wierszach 4 – 6 kodu. ACI jest doskonałą opcją do testowania wdrożenia. Później, gdy wszystko będzie gotowe do użycia modeli i usług sieci web w środowisku produkcyjnym o dużej skali, możesz ją [wdrażanie w usłudze AKS](how-to-deploy-to-aks.md).

W tym artykule przedstawiono trzy różne sposoby wdrażania modelu w usłudze ACI. Różnią się liczba wierszy kodu i formant, który ma w częściach nazewnictwa wdrożenia. Z metody z najmniejszą ilością kodu i kontrolę do metody, które większość kodu i kontroli dostępne są następujące opcje ACI:

* Wdrażanie z pliku przy użyciu modelu `Webservice.deploy()` 
* Wdrażaj z przy użyciu zarejestrowanego modelu `Webservice.deploy_from_model()`
* Wdrażanie obrazów przy użyciu zarejestrowanego modelu `Webservice.deploy_from_image()`

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).


## <a name="prerequisites"></a>Wymagania wstępne

- Obszar roboczy usługi Azure Machine Learning i Azure Machine Learning SDK dla język Python jest zainstalowany. Dowiedz się, jak uzyskać te wymagania wstępne przy użyciu [wprowadzenie do przewodnika Szybki Start usługi Azure Machine Learning](quickstart-get-started.md).

- Obiekt obszaru roboczego usługi Azure Machine Learning

    ```python
    from azureml.core import Workspace
    ws = Workspace.from_config()
    ```

- Model wdrażania. W przykładach w tym dokumencie używane model utworzony po wykonaniu "[Wytrenuj model](tutorial-train-models-with-aml.md)" samouczek. Jeśli używasz tego modelu, należy zmodyfikować kroki do odwoływania się do nazwy modelu.  Należy również napisać własny skrypt oceniania, aby uruchomić modelu.


## <a name="configure-an-image"></a>Obraz przedstawiający Konfigurowanie

Skonfiguruj obraz platformy Docker, który jest używany do przechowywania wszystkich plików modelu.
1. Utwórz skrypt oceniania (score.py) [przy użyciu tych instrukcji](tutorial-deploy-models-with-aml.md#create-scoring-script)

1. Utwórz plik środowiska (myenv.yml) [przy użyciu tych instrukcji](tutorial-deploy-models-with-aml.md#create-environment-file) 

1. Użyj tych plików, aby skonfigurować obraz platformy Docker w języku Python za pomocą zestawu SDK w następujący sposób:

    ```python
    from azureml.core.image import ContainerImage

    image_config = ContainerImage.image_configuration(execution_script = "score.py",
                                                      runtime = "python",
                                                      conda_file = "myenv.yml",
                                                      description = "Image with mnist model",
                                                      tags = {"data": "mnist", "type": "classification"}
                                                     )
    ```

## <a name="configure-the-aci-container"></a>Konfigurowanie kontenera usługi ACI

Konfigurowanie kontenera usługi ACI, określ liczbę procesorów i GB pamięci RAM wymaganej dla kontenera usługi ACI. Wartość domyślna jednego rdzenia i 1 GB pamięci RAM jest wystarczająca dla wielu modeli. Jeśli uważasz, że należy kolejnych w przyszłości, ponownie utworzyć obraz i ponownie wdrożyć usługę.  

```python
from azureml.core.webservice import AciWebservice

aciconfig = AciWebservice.deploy_configuration(cpu_cores = 1, 
                                               memory_gb = 1, 
                                               tags = {"data": "mnist", "type": "classification"},
                                               description = 'Handwriting recognition')
```

## <a name="register-a-model"></a>Zarejestruj model

> Pominąć to wymaganie wstępne, jeśli jesteś [wdrażania z pliku modelu](#deploy-from-model-file) (`Webservice.deploy()`).

Zarejestruj model, aby użyć [Webservice.deploy_from_model](#deploy-from-registered-model) lub [Webservice.deploy_from_image](#deploy-from-image). Lub jeśli masz już zarejestrowanego modelu, pobierz ją teraz.

### <a name="retrieve-a-registered-model"></a>Pobrać zarejestrowanego modelu
Jeśli używasz usługi Azure Machine Learning do nauczenia modelu, model może być już zarejestrowany w obszarze roboczym.  Na przykład ostatni krok [uczenie modelu samouczek](tutorial-train-models-with-aml.md) zarejestrowany modelu.  Następnie pobrać zarejestrowanego modelu wdrażania.

```python
from azureml.core.model import Model

model_name = "sklearn_mnist"
model=Model(ws, model_name)
```
  
### <a name="register-a-model-file"></a>Zarejestruj plik modelu

Jeśli model został utworzony w innym miejscu, możesz nadal zarejestrować go do obszaru roboczego.  Aby zarejestrować model pliku modelu (`sklearn_mnist_model.pkl` w tym przykładzie) musi znajdować się w bieżącym katalogu roboczym. Następnie należy zarejestrować tego pliku jako modelu o nazwie `sklearn_mnist` w przestrzeni roboczej z `Model.register()`.
    
```python
from azureml.core.model import Model

model_name = "sklearn_mnist"
model = Model.register(model_path = "sklearn_mnist_model.pkl",
                        model_name = model_name,
                        tags = {"data": "mnist", "type": "classification"},
                        description = "Mnist handwriting recognition",
                        workspace = ws)
```

<a name='deploy-from-model-file'/>
## <a name="option-1-deploy-from-model-file"></a>Opcja 1: Wdrożenia z pliku modelu

Możliwość wdrożenia aplikacji z plikiem modelu wymaga mniejszej ilości kodu do pisania, ale oferuje także najmniejszą ilością kontrolę nad nazw składników. Ta opcja rozpoczyna się od pliku modelu i rejestruje go do obszaru roboczego.  Nie można jednak nazwa modelu lub skojarzyć tagi i opis dla niego.  

Ta opcja używa metody zestaw SDK, Webservice.deploy().  

**Szacowany czas**: Wdrażanie trwa około 6 – 7 minut.

1. Upewnij się, że plik modelu znajduje się w lokalnym katalogu roboczego.

1. Otwórz plik modelu wymagań wstępnych, score.py i zmień `init()` sekcji:

    ```python
    def init():
        global model
        # retreive the local path to the model using the model name
        model_path = Model.get_model_path('sklearn_mnist_model.pkl')
        model = joblib.load(model_path)
    ```

1. Wdrażanie pliku modelu.

    ```python
    from azureml.core.webservice import Webservice
    
    service_name = 'aci-mnist-1'
    service = Webservice.deploy(deployment_config = aciconfig,
                                    image_config = image_config,
                                    model_paths = ['sklearn_mnist_model.pkl'],
                                    name = service_name,
                                    workspace = ws)
    
    service.wait_for_deployment(show_output = True)
    print(service.state)
    ```

1. Możesz teraz [przetestować usługę sieci web](#test-web-service).

<a name='deploy-from-registered-model'/>
## <a name="option-2-deploy-from-registered-model"></a>Opcja 2: Wdrażanie z zarejestrowanego modelu

Możliwość wdrażania pliku zarejestrowanego modelu zajmuje kilka większą liczbę linii kodu i umożliwia kontrolę nad nazewnictwa danych wyjściowych. Ta opcja jest wygodnym sposobem wdrażania zarejestrowanego modelu, którą już posiadasz.  Nie można jednak nazwa obrazu platformy Docker.  

Ta opcja używa metody zestaw SDK, Webservice.deploy_from_model().

**Szacowany czas**: wdrożenia z tą opcją zajmuje około 8 minut.

1. Uruchom kod, aby skonfigurować kontener platformy Docker i kontenerów usługi ACI i określ zarejestrowanego modelu.

    ```python
    from azureml.core.webservice import Webservice

    service_name = 'aci-mnist-2'
    service = Webservice.deploy_from_model(deployment_config = aciconfig,
                                           image_config = image_config,
                                           models = [model], # this is the registered model object
                                           name = service_name,
                                           workspace = ws)
    service.wait_for_deployment(show_output = True)
    print(service.state)
    ```

1. Możesz teraz [przetestować usługę sieci web](#test-web-service).

<a name='deploy-from-image'/>
## <a name="option-3-deploy-from-image"></a>Opcja 3: Wdrażanie za pomocą obrazu

Wdrażanie modelu zarejestrowany (`model`) przy użyciu `Webservice.deploy_from_image()`. Ta metoda umożliwia oddzielnie utworzyć obraz platformy Docker, a następnie wdrożyć ją z tego obrazu.

1. Tworzenie i rejestrowanie obrazu platformy Docker na za pomocą obszaru roboczego `ContainerImage.create()`

    Ta metoda zapewnia większą kontrolę nad obrazu, tworząc go w osobnym kroku.  Zarejestrowanego modelu (`model`) znajduje się na obrazie.
    
    ```python
    from azureml.core.image import ContainerImage
    
    image = ContainerImage.create(name = "myimage1",
                                  models = [model], # this is the registered model object
                                  image_config = image_config,
                                  workspace = ws)
    
    image.wait_for_creation(show_output = True)
    ```
**Szacowany czas**: około 3 minuty.

1. Wdrażanie obrazu platformy Docker jako przy użyciu usługi `Webservice.deploy_from_image()`

    Teraz można wdrożyć obraz do usługi ACI.  
    
    ```python
    from azureml.core.webservice import Webservice
    
    service_name = 'aci-mnist-3'
    service = Webservice.deploy_from_image(deployment_config = aciconfig,
                                                image = image,
                                                name = service_name,
                                                workspace = ws)
    service.wait_for_deployment(show_output = True)
    print(service.state)
    ```   
 
**Szacowany czas**: około 3 minuty.

Ta metoda zapewnia największą kontrolę nad tworzenia i nazw składników we wdrożeniu.

Teraz można przetestować usługę sieci web.

<a name='test-web-service'/>
## <a name="test-the-web-service"></a>Test usługi sieci web

Usługa sieci web jest taka sama niezależnie od tego, którego użyto metody.  Aby uzyskać prognoz, użyj `run` metody usługi.  

```python
# Load Data
import os
import urllib

os.makedirs('./data', exist_ok = True)

urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz', filename = './data/test-images.gz')

from utils import load_data
X_test = load_data('./data/test-images.gz', False) / 255.0

from sklearn import datasets
import numpy as np
import json

# find 5 random samples from test set
n = 5
sample_indices = np.random.permutation(X_test.shape[0])[0:n]

test_samples = json.dumps({"data": X_test[sample_indices].tolist()})
test_samples = bytes(test_samples, encoding = 'utf8')

# predict using the deployed model
prediction = service.run(input_data = test_samples)
print(prediction)
```


## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli nie zamierzasz korzystać z tej usługi sieci web, należy go usunąć, więc nie powodują naliczania żadnych opłat.

```python
service.delete()
```

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, jak [wdrażanie w usłudze Azure Kubernetes Service](how-to-deploy-to-aks.md) dla rozmieszczenia na większą skalę. 
