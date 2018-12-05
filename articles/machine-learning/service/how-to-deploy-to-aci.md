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
ms.date: 11/06/2018
ms.openlocfilehash: 4d525fdcbaa85eecee903ed83ee903d55810cbce
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2018
ms.locfileid: "52876601"
---
# <a name="deploy-web-services-to-azure-container-instances"></a>Wdrażaj usługi sieci web w usłudze Azure Container Instances 

Uczonego modelu można wdrożyć jako usługę sieci web, na [usługi Azure Container Instances](https://azure.microsoft.com/services/container-instances/) (ACI), [usługi Azure Kubernetes Service](https://azure.microsoft.com/services/kubernetes-service/) (AKS), IoT krawędzi urządzenia, lub [pola Tablice bramek programowane (układów FPGA)](concept-accelerate-with-fpgas.md) 

ACI jest zazwyczaj tańsze niż usługi AKS i można skonfigurować w wierszach 4 – 6 kodu. ACI jest doskonałą opcją do testowania wdrożenia. Później, gdy wszystko będzie gotowe do użycia modeli i usług sieci web w środowisku produkcyjnym o dużej skali, możesz ją [wdrażanie w usłudze AKS](how-to-deploy-to-aks.md).

W tym artykule przedstawiono trzy różne sposoby wdrażania modelu w usłudze ACI. Różnią się liczba wierszy kodu i formant, który ma w częściach nazewnictwa wdrożenia. Z metody z najmniejszą ilością kodu i kontrolę do metody, które większość kodu i kontroli dostępne są następujące opcje ACI:

* Wdrażanie z pliku przy użyciu modelu `Webservice.deploy()` 
* Wdrażaj z przy użyciu zarejestrowanego modelu `Webservice.deploy_from_model()`
* Wdrażanie obrazów przy użyciu zarejestrowanego modelu `Webservice.deploy_from_image()`

>[!NOTE]
> Kod w tym artykule został przetestowany przy użyciu zestawu SDK usługi Azure Machine Learning w wersji 1.0.2

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://aka.ms/AMLfree).


## <a name="prerequisites"></a>Wymagania wstępne

- Obszar roboczy usługi Azure Machine Learning i Azure Machine Learning SDK dla język Python jest zainstalowany. Dowiedz się, jak uzyskać te wymagania wstępne przy użyciu [wprowadzenie do przewodnika Szybki Start usługi Azure Machine Learning](quickstart-get-started.md).

- Obiekt obszaru roboczego usługi Azure Machine Learning

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-deploy-to-aci/how-to-deploy-to-aci.py?name=loadWorkspace)]

- Model wdrażania. W przykładach w tym dokumencie używane model utworzony po wykonaniu "[Wytrenuj model](tutorial-train-models-with-aml.md)" samouczek. Jeśli używasz tego modelu, należy zmodyfikować kroki do odwoływania się do nazwy modelu.  Należy również napisać własny skrypt oceniania, aby uruchomić modelu.


## <a name="configure-an-image"></a>Obraz przedstawiający Konfigurowanie

Skonfiguruj obraz platformy Docker, który jest używany do przechowywania wszystkich plików modelu.
1. Utwórz skrypt oceniania (score.py) [przy użyciu tych instrukcji](tutorial-deploy-models-with-aml.md#create-scoring-script).

    > [!IMPORTANT]
    > Skrypt oceniania odbiera dane przesłane przez klientów i przekazuje je do modelu w celu oceniania. Dokumentowanie struktury danych, skrypt i model oczekujesz. Posiadanie tej dokumentacji wiele ułatwia podczas tworzenia klienta do korzystania z usługi sieci web.

1. Utwórz plik środowiska (myenv.yml) [przy użyciu tych instrukcji](tutorial-deploy-models-with-aml.md#create-environment-file).

1. Użyj tych plików, aby skonfigurować obraz platformy Docker w języku Python za pomocą zestawu SDK w następujący sposób:

    [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-deploy-to-aci/how-to-deploy-to-aci.py?name=configImage)]

> [!NOTE]
> Podczas tworzenia obrazu, który używa przyspieszenie procesora GPU, obrazu podstawowego procesora GPU musi być używany w usług platformy Microsoft Azure tylko.

## <a name="configure-the-aci-container"></a>Konfigurowanie kontenera usługi ACI

Konfigurowanie kontenera usługi ACI, określ liczbę procesorów i GB pamięci RAM wymaganej dla kontenera usługi ACI. Wartość domyślna jednego rdzenia i 1 GB pamięci RAM jest wystarczająca dla wielu modeli. Jeśli uważasz, że należy kolejnych w przyszłości, ponownie utworzyć obraz i ponownie wdrożyć usługę.  

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-deploy-to-aci/how-to-deploy-to-aci.py?name=configAci)]

## <a name="register-a-model"></a>Zarejestruj model

> Pominąć to wymaganie wstępne, jeśli jesteś [wdrażania z pliku modelu](#deploy-from-model-file) (`Webservice.deploy()`).

Zarejestruj model, aby użyć [Webservice.deploy_from_model](#deploy-from-registered-model) lub [Webservice.deploy_from_image](#deploy-from-image). Lub jeśli masz już zarejestrowanego modelu, pobierz ją teraz.


### <a name="retrieve-a-registered-model"></a>Pobrać zarejestrowanego modelu
Jeśli używasz usługi Azure Machine Learning do nauczenia modelu, model może być już zarejestrowany w obszarze roboczym.  Na przykład ostatni krok [uczenie modelu samouczek](tutorial-train-models-with-aml.md) zarejestrowany modelu.  Następnie pobrać zarejestrowanego modelu wdrażania.

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-deploy-to-aci/how-to-deploy-to-aci.py?name=retrieveModel)]
  
### <a name="register-a-model-file"></a>Zarejestruj plik modelu

Jeśli model został utworzony w innym miejscu, możesz nadal zarejestrować go do obszaru roboczego.  Aby zarejestrować model pliku modelu (`sklearn_mnist_model.pkl` w tym przykładzie) musi znajdować się w bieżącym katalogu roboczym. Następnie należy zarejestrować tego pliku jako modelu o nazwie `sklearn_mnist` w przestrzeni roboczej z `Model.register()`.

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-deploy-to-aci/how-to-deploy-to-aci.py?name=registerModel)]

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

    [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-deploy-to-aci/how-to-deploy-to-aci.py?name=option1Deploy)]

1. Możesz teraz [przetestować usługę sieci web](#test-web-service).

<a name='deploy-from-registered-model'/>
## <a name="option-2-deploy-from-registered-model"></a>Opcja 2: Wdrażanie z zarejestrowanego modelu

Możliwość wdrażania pliku zarejestrowanego modelu zajmuje kilka większą liczbę linii kodu i umożliwia kontrolę nad nazewnictwa danych wyjściowych. Ta opcja jest wygodnym sposobem wdrażania zarejestrowanego modelu, którą już posiadasz.  Nie można jednak nazwa obrazu platformy Docker.  

Ta opcja używa metody zestaw SDK, Webservice.deploy_from_model().

**Szacowany czas**: wdrożenia z tą opcją zajmuje około 8 minut.

1. Uruchom kod, aby skonfigurować kontener platformy Docker i kontenerów usługi ACI i określ zarejestrowanego modelu.

    [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-deploy-to-aci/how-to-deploy-to-aci.py?name=option2Deploy)]

1. Możesz teraz [przetestować usługę sieci web](#test-web-service).

<a name='deploy-from-image'/>
## <a name="option-3-deploy-from-image"></a>Opcja 3: Wdrażanie za pomocą obrazu

Wdrażanie modelu zarejestrowany (`model`) przy użyciu `Webservice.deploy_from_image()`. Ta metoda umożliwia oddzielnie utworzyć obraz platformy Docker, a następnie wdrożyć ją z tego obrazu.

1. Tworzenie i rejestrowanie obrazu platformy Docker na za pomocą obszaru roboczego `ContainerImage.create()`

    Ta metoda zapewnia większą kontrolę nad obrazu, tworząc go w osobnym kroku.  Zarejestrowanego modelu (`model`) znajduje się na obrazie.

    [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-deploy-to-aci/how-to-deploy-to-aci.py?name=option3CreateImage)]

    **Szacowany czas**: około 3 minuty.

1. Wdrażanie obrazu platformy Docker jako przy użyciu usługi `Webservice.deploy_from_image()`

    Teraz można wdrożyć obraz do usługi ACI.  
        
    [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-deploy-to-aci/how-to-deploy-to-aci.py?name=option3Deploy)]
 
    **Szacowany czas**: około 3 minuty.

Ta metoda zapewnia największą kontrolę nad tworzenia i nazw składników we wdrożeniu.

Teraz można przetestować usługę sieci web.

## <a name="a-nametest-web-servicetest-the-web-service"></a><a name='test-web-service'/>Test usługi sieci web

Usługa sieci web jest taka sama niezależnie od tego, którego użyto metody.  Aby uzyskać prognoz, użyj `run` metody usługi.  

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-deploy-to-aci/how-to-deploy-to-aci.py?name=testService)]


## <a name="update-the-web-service"></a>Aktualizacja usługi sieci web

Aby zaktualizować usługę sieci web, użyj `update` metody. Poniższy kod ilustruje sposób aktualizacji usługi sieci web w celu użycia nowego obrazu:

```python
from azureml.core.webservice import Webservice

service_name = 'aci-mnist-3'
# Retrieve existing service
service = Webservice(name = service_name, workspace = ws)
# Update the image used by the service
service.update(image = new-image)
print(service.state)
```

> [!NOTE]
> Po zaktualizowaniu obrazu usługi sieci web nie jest automatycznie aktualizowany. Należy ręcznie zaktualizować wszystkich usług, które chcesz użyć nowego obrazu.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli nie zamierzasz korzystać z tej usługi sieci web, należy go usunąć, więc nie powodują naliczania żadnych opłat.

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-deploy-to-aci/how-to-deploy-to-aci.py?name=deleteService)]


## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się, jak [zużywania modelu uczenia Maszynowego wdrożyć jako usługę sieci web](how-to-consume-web-service.md).
* Dowiedz się, jak [wdrażanie w usłudze Azure Kubernetes Service](how-to-deploy-to-aks.md) dla rozmieszczenia na większą skalę. 
