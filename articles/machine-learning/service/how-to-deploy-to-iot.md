---
title: Wdrażanie modeli do urządzenia usługi IoT Edge z usługi Azure Machine Learning | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wdrożyć model z usługi Azure Machine Learning na urządzeniach z usługą Azure IoT Edge. Wdrażanie modelu na urządzeniu usługi edge pozwala na ocenianie danych na urządzeniu, zamiast wysyłania jej do chmury i Oczekiwanie na odpowiedź.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: shipatel
author: shivanipatel
manager: cgronlun
ms.reviewer: larryfr
ms.date: 09/24/2018
ms.openlocfilehash: 7d706cf71761496fd740c729224ee4331eeb2911
ms.sourcegitcommit: 4047b262cf2a1441a7ae82f8ac7a80ec148c40c4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2018
ms.locfileid: "49091627"
---
# <a name="prepare-to-deploy-models-on-iot-edge"></a>Przygotowywanie do wdrażania modeli na brzegowych urządzeniach IoT

W tym dokumencie Dowiedz się, jak przygotować uczonego modelu do wdrożenia na urządzeniu z systemem Azure IoT Edge za pomocą usługi Azure Machine Learning.

Urządzenia usługi Azure IoT Edge to Linux lub urządzeń z systemem Windows, uruchamiana, środowisko uruchomieniowe usługi Azure IoT Edge. Modele uczenia maszynowego można wdrożyć na tych urządzeniach jako moduły usługi IoT Edge. Wdrażanie modelu na urządzeniu usługi IoT Edge umożliwia urządzeniu do korzystania z modelu bezpośrednio, zamiast wysyłać dane do chmury na potrzeby przetwarzania. Otrzymujesz krótszy czas reakcji i mniej transferu danych.

Przed przystąpieniem do wdrażania modelu na urządzeniu usługi edge, umożliwia przygotowanie modelu oraz urządzenia kroki opisane w tym dokumencie.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Obszar roboczy usługi Azure Machine Learning. Aby go utworzyć, użyj kroków w [wprowadzenie do usługi Azure Machine Learning](quickstart-get-started.md) dokumentu.

* Środowisko deweloperskie. Aby uzyskać więcej informacji, zobacz [sposób konfigurowania środowiska deweloperskiego](how-to-configure-environment.md) dokumentu.

* [Usługi Azure IoT Hub](../../iot-hub/iot-hub-create-through-portal.md) w subskrypcji platformy Azure. 

* Uczonego modelu. Na przykład sposobu uczenia modelu zobacz [Wytrenuj model klasyfikacji obrazów za pomocą usługi Azure Machine Learning](tutorial-train-models-with-aml.md) dokumentu. Wstępnie przeszkolonych modelu jest dostępna w [zestaw narzędzi SI dla repozytorium Azure IoT Edge w witrynie GitHub](https://github.com/Azure/ai-toolkit-iot-edge/tree/master/IoT%20Edge%20anomaly%20detection%20tutorial).

## <a name="prepare-the-iot-device"></a>Przygotuj urządzenie IoT

Aby dowiedzieć się, jak zarejestrować urządzenie i zainstalować środowisko uruchomieniowe IoT, wykonaj kroki opisane w [Szybki Start: Wdrażanie pierwszego modułu usługi IoT Edge urządzeniem z systemem Linux x64](../../iot-edge/quickstart-linux.md) dokumentu.

## <a name="register-the-model"></a>Zarejestruj model

Moduły usługi IoT Edge platformy Azure są oparte na obrazów kontenerów. Aby wdrożyć model do urządzenia usługi IoT Edge, umożliwia następujące czynności zarejestrować modelu w obszarze roboczym usługi Azure Machine Learning i utworzyć obraz platformy Docker. 

1. Inicjowanie obszaru roboczego i załadować pliku config.json:

    ```python
    from azureml.core  import Workspace

    #Load existing workspace from the config file info.
    ws  = Workspace.from_config()
    ```    

1. W obszarze roboczym, należy zarejestrować model. Zastąp tekst domyślna ścieżka modelu, nazwa, tagi i opis:

    > [!IMPORTANT]
    > Jeśli używasz usługi Azure Machine Learning do nauczenia modelu, może on już zarejestrowany w obszarze roboczym. Jeśli tak, Pomiń ten krok. Aby wyświetlić listę modeli zarejestrowana z tym obszarem roboczym, użyj `Model.list(ws)`.

    ```python
    from azureml.core.model import Model
    model = Model.register(model_path = "model.pkl", # this path points to the local file
                        model_name = "best_model", # the model gets registered as this name
                        tags = {'attribute': "myattribute", 'classification': "myclassification"},
                        description = "My awesome model",
                        workspace = ws)
    ```    

1. Pobrać zarejestrowanego modelu: 

    ```python
    from azureml.core.model import Model

    model_name = "best_model"
    model = Model(ws, model_name)                     
    ```    

## <a name="create-a-docker-image"></a>Tworzenie obrazu platformy Docker

1. Tworzenie **skrypt oceniania** o nazwie `score.py`. Ten plik jest używany do uruchamiania model w obrazie. Musi zawierać następujące funkcje:

    * Funkcja `init()`, która zazwyczaj ładuje model do obiektu globalnego. Ta funkcja jest uruchamiana tylko raz, po uruchomieniu kontenera platformy Docker. 

    * Funkcja `run(input_data)` używa modelu do przewidywania wartości w oparciu o dane wejściowe. Dane wejściowe i wyjściowe do uruchomienia zazwyczaj używają formatu JSON na potrzeby serializacji i deserializacji, ale obsługiwane są też inne formaty.

    Aby uzyskać przykład, zobacz [samouczek klasyfikacji obrazów](tutorial-deploy-models-with-aml.md#make-script).

1. Tworzenie **plikiem środowiska** o nazwie `myenv.yml`. Ten plik jest specyfikacja środowiska Conda i wyświetla wszystkie zależności wymagane przez skrypt i modelu. Aby uzyskać przykład, zobacz [samouczek klasyfikacji obrazów](tutorial-deploy-models-with-aml.md#make-myenv).

1. Konfigurowanie przy użyciu obrazu platformy Docker `score.py` i `myenv.yml` plików:
    
    ```python
    from azureml.core.image import Image, ContainerImage
    
    #Image configuration
    image_config = ContainerImage.image_configuration( runtime = "python", 
                           execution_script = "score.py",
                           conda_file = "myenv.yml", 
                           tags = {"attributes", "calssification"},
                           description = "Image that contains my model",
                           
                        )
    ```    

1. Tworzenie obrazu za pomocą modelu i obraz konfiguracji:

    ```python
    image = ContainerImage.create (name = "myimage", 
                           models = [model], #this is the model object
                           image_config = image_config,
                           workspace = ws
                        )
    ```     

    Tworzenie obrazu trwa około 5 minut.

## <a name="get-the-container-registry-credentials"></a>Pobieranie poświadczeń rejestru kontenera

Usługa Azure IoT potrzebuje poświadczeń dla rejestru kontenerów, w którym usługi Azure Machine Learning, są przechowywane obrazy platformy docker w. Aby uzyskać poświadczenia, wykonaj następujące kroki:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/signin/index).

1. Przejdź do obszaru roboczego usługi Azure Machine Learning i wybierz __Przegląd__. Aby przejść do ustawienia usługi container registry, wybierz __rejestru__ łącza.

    ![Obraz wpisu rejestru kontenerów](./media/how-to-deploy-to-iot/findregisteredcontainer.png)

1. Jeden raz w rejestrze kontenerów należy wybrać **klucze dostępu** , a następnie włącz użytkownika administratora.

    ![Obraz przedstawiający ekran klucze dostępu](./media/how-to-deploy-to-iot/findaccesskey.png)

1. Zapisz wartości dla nazwy logowania serwera, username i password. 

   Te poświadczenia są niezbędne do zapewnienia dostępu urządzenia IoT edge do obrazów w prywatnego rejestru kontenera.

## <a name="next-steps"></a>Kolejne kroki

Zostały wykonane przygotowania do wdrożenia. Teraz, użyj kroków w [modułów wdrożenia usługi Azure IoT Edge w witrynie Azure portal](../../iot-edge/how-to-deploy-modules-portal.md) dokumentu do wdrażania na urządzenia brzegowe. Podczas konfigurowania __ustawień rejestru__ dla urządzenia, Użyj poświadczeń, który został wcześniej skonfigurowany.
