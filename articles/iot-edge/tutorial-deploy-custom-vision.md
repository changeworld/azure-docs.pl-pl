---
title: Samouczek dotyczący wdrażania klasyfikatora usługi Custom Vision na urządzeniu — Azure IoT Edge | Microsoft Docs
description: W tym samouczku dowiesz się, jak sprawić, aby model przetwarzania obrazów był uruchamiany jako kontener, używając usług Custom Vision i IoT Edge.
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 10/15/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: 3418c57493e19580f0d3dbd9ea979b0322d930b8
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/24/2019
ms.locfileid: "74457294"
---
# <a name="tutorial-perform-image-classification-at-the-edge-with-custom-vision-service"></a>Samouczek: wykonywanie klasyfikacji obrazów na urządzeniach brzegowych za pomocą usługi Custom Vision

Usługa Azure IoT Edge może usprawnić Twoje rozwiązanie IoT przez przeniesienie obciążeń z chmury na urządzenia brzegowe. Ta funkcja jest bardzo przydatna w przypadku usług, które przetwarzają dużą ilość danych, takich jak modele przetwarzania obrazów. Usługa [Custom Vision](../cognitive-services/custom-vision-service/home.md) umożliwia tworzenie niestandardowych klasyfikatorów obrazów i wdrażanie ich na urządzeniach w postaci kontenerów. Te dwie usługi połączone razem pozwalają na uzyskiwanie szczegółowych informacji z obrazów lub strumieni wideo bez konieczności wcześniejszego przeniesienia wszystkich danych poza siedzibę firmy. Usługa Custom Vision udostępnia klasyfikator, który porównuje obraz z wyszkolonym modelem w celu wygenerowania szczegółowych informacji.

Na przykład usługa Custom Vision na urządzeniu usługi IoT Edge może określić, czy ruch na autostradzie jest większy, czy mniejszy niż zwykle, albo czy w hali garażowej są dostępne miejsca parkingowe w jednym rzędzie. Te informacje można udostępnić innej usłudze w celu wykonania akcji.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
>
> * Tworzenie klasyfikatora obrazów za pomocą usługi Custom Vision.
> * Tworzenie modułu usługi IoT Edge wysyłającego zapytania do serwera internetowego usługi Custom Vision na urządzeniu.
> * Wysyłanie wyników klasyfikatora obrazów do usługi IoT Hub.

<center>

![Diagram - Tutorial architecture, stage and deploy classifier](./media/tutorial-deploy-custom-vision/custom-vision-architecture.png)
</center>

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

>[!TIP]
>This tutorial is a simplified version of the [Custom Vision and Azure IoT Edge on a Raspberry Pi 3](https://github.com/Azure-Samples/Custom-vision-service-iot-edge-raspberry-pi) sample project. This tutorial was designed to run on a cloud VM and uses static images to train and test the image classifier, which is useful for someone just starting to evaluate Custom Vision on IoT Edge. The sample project uses physical hardware and sets up a live camera feed to train and test the image classifier, which is useful for someone who wants to try a more detailed, real-life scenario.

Before beginning this tutorial, you should have gone through the previous tutorial to set up your environment for Linux container development: [Develop IoT Edge modules for Linux devices](tutorial-develop-for-linux.md). By completing that tutorial, you should have the following prerequisites in place: 

* Usługa [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) w warstwie Bezpłatna lub Standardowa na platformie Azure.
* A [Linux device running Azure IoT Edge](quickstart-linux.md)
* A container registry, like [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/).
* [Visual Studio Code](https://code.visualstudio.com/) configured with the [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
* [Docker CE](https://docs.docker.com/install/) configured to run Linux containers.

To develop an IoT Edge module with the Custom Vision service, install the following additional prerequisites on your development machine: 

* [Python](https://www.python.org/downloads/)
* [Usługa Git](https://git-scm.com/downloads)
* [Python extension for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-python.python) 

## <a name="build-an-image-classifier-with-custom-vision"></a>Tworzenie klasyfikatora obrazów za pomocą usługi Custom Vision

W celu utworzenia klasyfikatora obrazów musisz utworzyć projekt usługi Custom Vision i dostarczyć obrazy szkoleniowe. Aby uzyskać więcej informacji na temat kroków, które wykonasz w tej sekcji, zobacz [Sposób tworzenia klasyfikatora za pomocą usługi Custom Vision](../cognitive-services/custom-vision-service/getting-started-build-a-classifier.md).

Po utworzeniu i wyszkoleniu klasyfikatora obrazów możesz wyeksportować go w postaci kontenera platformy Docker i wdrożyć na urządzeniu usługi IoT Edge. 

### <a name="create-a-new-project"></a>Tworzenie nowego projektu

1. W przeglądarce internetowej przejdź do [strony internetowej usługi Custom Vision](https://customvision.ai/).

2. Wybierz pozycję **Sign in** (Zaloguj się) i zaloguj się za pomocą tego samego konta, którego używasz, aby uzyskać dostęp do zasobów platformy Azure. 

3. Wybierz pozycję **New project** (Nowy projekt).

4. Utwórz projekt z następującymi wartościami:

   | Pole | Wartość |
   | ----- | ----- |
   | Nazwa | Podaj nazwę dla projektu, na przykład **EdgeTreeClassifier**. |
   | Opis | Opcjonalny opis projektu. |
   | Zasób | Select one of your Azure resource groups that includes a Custom Vision Service resource or **create new** if you haven't yet added one. |
   | Project Types (Typy projektów) | **Classification** (Klasyfikacja) |
   | Classification Types (Typy klasyfikacji) | **Multiclass (single tag per image)** (Multiklasa (pojedynczy tag na obrazie)) |
   | Domeny | **General (compact)** (Ogólne (kompaktowe)) |
   | Export Capabilities | **Basic platforms (Tensorflow, CoreML, ONNX, ...)** |

5. Wybierz pozycję **Create project** (Utwórz projekt).

### <a name="upload-images-and-train-your-classifier"></a>Przekazywanie obrazów i szkolenie klasyfikatora

Do utworzenia klasyfikatora obrazów jest potrzebny zestaw obrazów szkoleniowych, a także obrazy testowe. 

1. Sklonuj lub pobierz przykładowe obrazy z repozytorium [Cognitive-CustomVision-Windows](https://github.com/Microsoft/Cognitive-CustomVision-Windows) na lokalnej maszynie deweloperskiej. 

   ```cmd/sh
   git clone https://github.com/Microsoft/Cognitive-CustomVision-Windows.git
   ```

2. Wróć do projektu usługi Custom Vision i wybierz pozycję **Add images** (Dodaj obrazy). 

3. Przejdź do sklonowanego lokalnie repozytorium git, a następnie przejdź do pierwszego folderu obrazów **Cognitive-CustomVision-Windows / Samples / Images / Hemlock**. Zaznacz wszystkie 10 obrazów w tym folderze, a następnie wybierz pozycję **Open** (Otwórz). 

4. Dodaj do tej grupy obrazów tag **cykuta**, a następnie naciśnij klawisz **Enter**, aby zastosować ten tag. 

5. Wybierz pozycję **Upload 10 files** (Przekaż 10 plików). 

   ![Przekazywanie plików oznaczonych tagiem choina do usługi Custom Vision](./media/tutorial-deploy-custom-vision/upload-hemlock.png)

6. Po pomyślnym przekazaniu obrazów wybierz pozycję **Done** (Gotowe).

7. Wybierz ponownie pozycję **Add images** (Dodaj obrazy).

8. Przejdź do drugiego folderu obrazów **Cognitive-CustomVision-Windows / Samples / Images / Japanese Cherry**. Zaznacz wszystkie 10 obrazów w tym folderze, a następnie wybierz pozycję **Open** (Otwórz). 

9. Dodaj do tej grupy obrazów tag **wiśnia japońska**, a następnie naciśnij klawisz **Enter**, aby zastosować ten tag. 

10. Wybierz pozycję **Upload 10 files** (Przekaż 10 plików). Po pomyślnym przekazaniu obrazów wybierz pozycję **Done** (Gotowe). 

11. Po oznakowaniu i przekazaniu obu zestawów obrazów wybierz pozycję **Train** (Szkolenie), aby wyszkolić klasyfikator. 

### <a name="export-your-classifier"></a>Eksportowanie klasyfikatora

1. Po wyszkoleniu klasyfikatora wybierz pozycję **Eksport** (Eksportuj) na stronie Performance (Wydajność) klasyfikatora. 

   ![Eksportowanie wytrenowanego klasyfikatora obrazów](./media/tutorial-deploy-custom-vision/export.png)

2. Jako platformę wybierz **DockerFile**. 

3. Jako wersję wybierz **Linux**.  

4. Wybierz pozycję **Export** (Eksportuj). 

   ![Eksportowanie w postaci pliku DockerFile z kontenerami systemu Linux](./media/tutorial-deploy-custom-vision/export-2.png)

5. Po zakończeniu eksportowania wybierz pozycję **Download** (Pobierz) i zapisz pakiet ZIP lokalnie na komputerze. Wyodrębnij wszystkie pliki z pakietu. Użyjesz tych plików do utworzenia modułu usługi IoT Edge zawierającego serwer klasyfikacji obrazów. 

W tym miejscu kończy się tworzenie i szkolenie projektu usługi Custom Vision. Wyeksportowane pliki zostaną użyte w następnej sekcji, ale na stronie internetowej usługi Custom Vision to już wszystko. 

## <a name="create-an-iot-edge-solution"></a>Tworzenie rozwiązania usługi IoT Edge

Na maszynie deweloperskiej masz teraz pliki dla wersji kontenera Twojego klasyfikatora obrazów. W tej sekcji skonfigurujesz kontener klasyfikatora obrazów tak, aby był on uruchamiany jako moduł usługi IoT Edge. Utworzysz również drugi moduł, który zostanie wdrożony obok klasyfikatora obrazów. Drugi moduł publikuje żądania do klasyfikatora i wysyła wyniki jako komunikaty do usługi IoT Hub. 

### <a name="create-a-new-solution"></a>Utwórz nowe rozwiązanie

Rozwiązanie jest logicznym sposobem tworzenia i organizowania wielu modułów dla pojedynczego wdrożenia usługi IoT Edge. Zawiera ono kod dla jednego lub więcej modułów, a także manifest wdrożenia deklarujący sposób skonfigurowania ich na urządzeniu usługi IoT Edge. 

1. Wybierz kolejno opcje **Widok** > **Paleta poleceń**, aby otworzyć paletę poleceń programu VS Code. 

1. W palecie poleceń wprowadź i uruchom polecenie **Azure IoT Edge: nowe rozwiązanie usługi IoT Edge**. W palecie poleceń podaj następujące informacje, aby utworzyć rozwiązanie: 

   | Pole | Wartość |
   | ----- | ----- |
   | Wybierz folder | Wybierz lokalizację na maszynie deweloperskiej dla programu VS Code, aby utworzyć pliki rozwiązania. |
   | Podaj nazwę rozwiązania | Wprowadź opisową nazwę rozwiązania, na przykład **CustomVisionSolution**, lub zaakceptuj nazwę domyślną. |
   | Wybierz szablon modułu | Wybierz pozycję **Moduł Python**. |
   | Podaj nazwę modułu | Nadaj modułowi nazwę **classifier**.<br><br>Nazwa modułu musi być pisana małymi literami. Podczas odwoływania się do modułów usługa IoT Edge rozróżnia wielkość liter, a to rozwiązanie korzysta z biblioteki, która formatuje wszystkie żądania małymi literami. |
   | Podaj repozytorium obrazów platformy Docker dla modułu | Repozytorium obrazów zawiera nazwę rejestru kontenerów oraz nazwę obrazu kontenera. Obraz kontenera jest wstępnie wypełniany w ostatnim kroku. Zastąp ciąg **localhost:5000** wartością serwera logowania z rejestru kontenerów platformy Azure. Serwer logowania możesz pobrać ze strony Przegląd rejestru kontenerów w witrynie Azure Portal.<br><br>The final string looks like **\<registry name\>.azurecr.io/classifier**. |
 
   ![Udostępnianie repozytorium obrazów platformy Docker](./media/tutorial-deploy-custom-vision/repository.png)

W oknie programu Visual Studio Code zostanie załadowany obszar roboczy rozwiązania usługi IoT Edge.

### <a name="add-your-registry-credentials"></a>Dodawanie poświadczeń rejestru

W pliku środowiska przechowywane są poświadczenia rejestru kontenerów udostępniane środowisku uruchomieniowemu usługi IoT Edge. Środowisko uruchomieniowe wymaga tych poświadczeń do ściągnięcia prywatnych obrazów na urządzenie usługi IoT Edge.

1. W eksploratorze programu VS Code otwórz plik env.
2. Zaktualizuj pola, używając **nazwy użytkownika** i **hasła**, które zostały skopiowane z usługi Azure Container Registry.
3. Zapisz ten plik.

### <a name="select-your-target-architecture"></a>Select your target architecture

Currently, Visual Studio Code can develop modules for Linux AMD64 and Linux ARM32v7 devices. You need to select which architecture you're targeting with each solution, because the container is built and run differently for each architecture type. The default is Linux AMD64, which is what we'll use for this tutorial. 

1. Open the command palette and search for **Azure IoT Edge: Set Default Target Platform for Edge Solution**, or select the shortcut icon in the side bar at the bottom of the window. 

2. In the command palette, select the target architecture from the list of options. For this tutorial, we're using an Ubuntu virtual machine as the IoT Edge device, so will keep the default **amd64**. 

### <a name="add-your-image-classifier"></a>Dodawanie klasyfikatora obrazów

Szablon modułu Python w programie Visual Studio Code zawiera przykładowy kod, który możesz uruchomić w celu przetestowania usługi IoT Edge. W tym scenariuszu ten kod nie będzie używany. Zamiast tego wykonaj kroki opisane w tej sekcji, aby zastąpić przykładowy kod wyeksportowanym wcześniej kontenerem klasyfikatora obrazów. 

1. W eksploratorze plików przejdź do pobranego i wyodrębnionego pakietu usługi Custom Vision. Skopiuj całą zawartość z wyodrębnionego pakietu. Powinien on zawierać dwa foldery, **app** i **azureml**, oraz dwa pliki, **Dockerfile** i **README**. 

2. W eksploratorze plików przejdź do katalogu, w którym program Visual Studio Code miał utworzyć rozwiązanie IoT Edge. 

3. Otwórz folder modułu klasyfikatora. Jeśli w poprzedniej sekcji zostały użyte sugerowane nazwy, struktura folderów wygląda następująco: **CustomVisionSolution / modules / classifier**. 

4. Wklej pliki do folderu **classifier**. 

5. Wróć do okna programu Visual Studio Code. W folderze modułu w obszarze roboczym rozwiązania powinny teraz być widoczne pliki klasyfikatora obrazów. 

   ![Obszar roboczy rozwiązania z plikami klasyfikatora obrazów](./media/tutorial-deploy-custom-vision/workspace.png)

6. Otwórz plik **module.json** w folderze klasyfikatora. 

7. Update the **platforms** parameter to point to the new Dockerfile that you added, and remove all the options besides AMD64, which is the only architecture we're using for this tutorial. 

   ```json
   "platforms": {
       "amd64": "./Dockerfile"
   }
   ```

8. Zapisz zmiany. 

### <a name="create-a-simulated-camera-module"></a>Tworzenie modułu symulowanej kamery

W rzeczywistym wdrożeniu usługi Custom Vision istniałaby kamera dostarczająca obrazy na żywo lub strumienie wideo. W tym scenariuszu kamera będzie symulowana za pomocą modułu wysyłającego obraz testowy do klasyfikatora obrazów. 

#### <a name="add-and-configure-a-new-module"></a>Dodawanie i konfigurowanie nowego modułu

W tej sekcji dodasz nowy moduł do tego samego rozwiązania CustomVisionSolution i podasz kod umożliwiający utworzenie symulowanej kamery. 

1. W tym samym oknie programu Visual Studio Code użyj palety poleceń, aby uruchomić usługę **Azure IoT Edge: dodawanie modułu usługi IoT Edge**. W palecie poleceń podaj następujące informacje dotyczące nowego modułu: 

   | Monit | Wartość | 
   | ------ | ----- |
   | Wybierz plik szablonu wdrożenia | Wybierz plik deployment.template.json w folderze CustomVisionSolution. |
   | Wybierz szablon modułu | Wybierz pozycję **Moduł Python** |
   | Podaj nazwę modułu | Nadaj modułowi nazwę **cameraCapture** |
   | Podaj repozytorium obrazów platformy Docker dla modułu | Zastąp ciąg **localhost:5000** wartością serwera logowania dla rejestru kontenerów platformy Azure.<br><br>Ostatecznie ciąg będzie wyglądał następująco: **\<nazwa rejestru\>.azurecr.io/cameracapture**. |

   W oknie programu VS Code do obszaru roboczego rozwiązania zostanie załadowany nowy moduł, a następnie zostanie zaktualizowany plik deployment.template.json. Teraz powinny być widoczne dwa foldery modułów: classifier i cameraCapture. 

2. Otwórz plik **main.py** w folderze **modules** / **cameraCapture**. 

3. Zastąp cały plik poniższym kodem. Ten przykładowy kod wysyła żądania POST do usługi przetwarzania obrazów działającej w module klasyfikatora. Ten moduł kontenera udostępniamy z przykładowym obrazem do użycia w żądaniach. Pakuje on następnie odpowiedź jako komunikat usługi IoT Hub i wysyła ją do kolejki wyjściowej.  

    ```python
    # Copyright (c) Microsoft. All rights reserved.
    # Licensed under the MIT license. See LICENSE file in the project root for
    # full license information.

    import time
    import sys
    import os
    import requests
    import json
    from azure.iot.device import IoTHubModuleClient, Message

    # global counters
    SENT_IMAGES = 0

    # global client
    CLIENT = None

    # Send a message to IoT Hub
    # Route output1 to $upstream in deployment.template.json
    def send_to_hub(strMessage):
        message = Message(bytearray(strMessage, 'utf8'))
        CLIENT.send_message_to_output(message, "output1")
        global SENT_IMAGES
        SENT_IMAGES += 1
        print( "Total images sent: {}".format(SENT_IMAGES) )

    # Send an image to the image classifying server
    # Return the JSON response from the server with the prediction result
    def sendFrameForProcessing(imagePath, imageProcessingEndpoint):
        headers = {'Content-Type': 'application/octet-stream'}

        with open(imagePath, mode="rb") as test_image:
            try:
                response = requests.post(imageProcessingEndpoint, headers = headers, data = test_image)
                print("Response from classification service: (" + str(response.status_code) + ") " + json.dumps(response.json()) + "\n")
            except Exception as e:
                print(e)
                print("Response from classification service: (" + str(response.status_code))

        return json.dumps(response.json())

    def main(imagePath, imageProcessingEndpoint):
        try:
            print ( "Simulated camera module for Azure IoT Edge. Press Ctrl-C to exit." )

            try:
                global CLIENT
                CLIENT = IoTHubModuleClient.create_from_edge_environment()
            except Exception as iothub_error:
                print ( "Unexpected error {} from IoTHub".format(iothub_error) )
                return

            print ( "The sample is now sending images for processing and will indefinitely.")

            while True:
                classification = sendFrameForProcessing(imagePath, imageProcessingEndpoint)
                send_to_hub(classification)
                time.sleep(10)

        except KeyboardInterrupt:
            print ( "IoT Edge module sample stopped" )

    if __name__ == '__main__':
        try:
            # Retrieve the image location and image classifying server endpoint from container environment
            IMAGE_PATH = os.getenv('IMAGE_PATH', "")
            IMAGE_PROCESSING_ENDPOINT = os.getenv('IMAGE_PROCESSING_ENDPOINT', "")
        except ValueError as error:
            print ( error )
            sys.exit(1)

        if ((IMAGE_PATH and IMAGE_PROCESSING_ENDPOINT) != ""):
            main(IMAGE_PATH, IMAGE_PROCESSING_ENDPOINT)
        else: 
            print ( "Error: Image path or image-processing endpoint missing" )
    ```

4. Zapisz plik **main.py**. 

5. Otwórz plik **requrements.txt**. 

6. Dodaj nowy wiersz dotyczący biblioteki, która zostanie uwzględniona w kontenerze.

   ```Text
   requests
   ```

7. Zapisz plik **requirements.txt**.


#### <a name="add-a-test-image-to-the-container"></a>Dodawanie obrazu testowego do kontenera

Zamiast dostarczać źródło obrazów dla tego scenariusza przy użyciu prawdziwej kamery, użyjemy pojedynczego obrazu testowego. Obraz testowy znajduje się w repozytorium GitHub, które zostało pobrane na potrzeby obrazów szkoleniowych we wcześniejszej części tego samouczka. 

1. Przejdź do obrazu testowego znajdującego się w folderze **Cognitive-CustomVision-Windows** / **Samples** / **Images** / **Test**. 

2. Skopiuj plik **test_image.jpg** 

3. Przejdź do katalogu rozwiązania usługi IoT Edge i wklej obraz testowy w folderze **modules** / **cameraCapture**. Obraz powinien znajdować się w tym samym folderze co plik main.py, który był edytowany w poprzedniej sekcji. 

3. W programie Visual Studio Code otwórz plik **Dockerfile.amd64** modułu cameraCapture. 

4. Po wierszu określającym katalog roboczy `WORKDIR /app` dodaj następujący wiersz kodu: 

   ```Dockerfile
   ADD ./test_image.jpg .
   ```

5. Zapisz plik Dockerfile. 

### <a name="prepare-a-deployment-manifest"></a>Przygotowanie manifestu wdrożenia

Do tej pory w ramach tego samouczka wyszkoliliśmy model usługi Custom Vision służący do klasyfikowania obrazów drzew oraz spakowaliśmy go jako moduł usługi IoT Edge. Następnie utworzyliśmy drugi moduł, który może wysyłać zapytania do serwera klasyfikacji obrazów i raportować wyniki do usługi IoT Hub. Teraz możesz przystąpić do tworzenia manifestu wdrożenia, który poinformuje urządzenie usługi IoT Edge, jak uruchamiać te dwa moduły razem. 

Rozszerzenie usługi IoT Edge dla programu Visual Studio Code zawiera w każdym rozwiązaniu IoT Edge szablon ułatwiający tworzenie manifestu wdrożenia. 

1. Otwórz plik **deployment.template.json** w folderze rozwiązania. 

2. Find the **modules** section, which should contain three modules: the two that you created, classifier and cameraCapture, and a third that's included by default, SimulatedTemperatureSensor. 

3. Delete the **SimulatedTemperatureSensor** module with all of its parameters. Ten moduł jest dołączany w celu dostarczenia przykładowych danych do obsługi scenariuszy testowych, ale w tym wdrożeniu nie jest potrzebny. 

4. Jeśli moduł klasyfikacji obrazów został nazwany inaczej niż **classifier**, sprawdź teraz tę nazwę i upewnij się, że zawiera tylko małe litery. Moduł cameraCapture wywołuje moduł klasyfikatora, używając biblioteki żądań, która formatuje wszystkie żądania małymi literami, a w usłudze IoT Edge jest uwzględniana wielkość liter. 

5. Zaktualizuj parametr **createOptions** modułu cameraCapture za pomocą poniższego kodu JSON. Te informacje tworzą w kontenerze modułu zmienne środowiskowe, które są pobierane w procesie main.py. Umieszczając te informacje w manifeście wdrożenia, możesz zmienić obraz lub punkt końcowy bez konieczności ponownego tworzenia obrazu modułu. 

    ```json
    "createOptions": "{\"Env\":[\"IMAGE_PATH=test_image.jpg\",\"IMAGE_PROCESSING_ENDPOINT=http://classifier/image\"]}"
    ```

    Jeśli moduł usługi Custom Vision został nazwany inaczej niż *classifier*, zaktualizuj odpowiednio wartość punktu końcowego przetwarzania obrazów. 

5. W dolnej części pliku zaktualizuj parametr **routes** modułu $edgeHub. Chcesz rozesłać wyniki prognozowania z modułu cameraCapture do usługi IoT Hub. 

    ```json
        "routes": {
          "CameraCaptureToIoTHub": "FROM /messages/modules/cameraCapture/outputs/* INTO $upstream"
        },
    ```

    Jeśli drugi moduł został nazwany inaczej niż *cameraCapture*, zaktualizuj odpowiednio wartość trasy. 

7. Zapisz plik **deployment.template.json**.

## <a name="build-and-deploy-your-iot-edge-solution"></a>Tworzenie i wdrażanie rozwiązania usługi IoT Edge

Mając utworzone oba moduły i skonfigurowany szablon manifestu wdrożenia, możesz przystąpić do tworzenia obrazów kontenerów i wypychania ich do rejestru kontenerów. 

Gdy obrazy znajdą się w rejestrze, możesz wdrożyć rozwiązanie na urządzeniu usługi IoT Edge. Moduły można ustawić na urządzeniu za pomocą usługi IoT Hub, ale dostęp do usługi IoT Hub i urządzeń można również uzyskać za pomocą programu Visual Studio Code. W tej sekcji skonfigurujesz dostęp do usługi IoT Hub, a następnie użyjesz programu VS Code do wdrożenia rozwiązania na urządzeniu usługi IoT Edge.

Najpierw skompiluj swoje rozwiązanie i wypchnij je do rejestru kontenerów. 

1. W eksploratorze programu VS Code kliknij prawym przyciskiem myszy plik **deployment.template.json** i wybierz polecenie **Skompiluj i wypchnij rozwiązanie usługi IoT Edge**. Postęp tej operacji możesz obserwować w zintegrowanym terminalu w programie VS Code. 
2. Notice that a new folder was added to your solution, **config**. Expand this folder and open the **deployment.json** file inside.
3. Przejrzyj informacje w pliku deployment.json. Plik deployment.json jest tworzony (lub aktualizowany) automatycznie na podstawie skonfigurowanego pliku szablonu wdrożenia oraz informacji z rozwiązania, w tym pliku ENV i plików module.json. 

Next, select your device and deploy your solution.

1. W eksploratorze programu VS rozwiń sekcję **Urządzenia usługi Azure IoT Hub**. 
2. Kliknij prawym przyciskiem myszy urządzenie, które ma być urządzeniem docelowym wdrożenia, i wybierz polecenie **Create deployment for single device** (Utwórz wdrożenie dla pojedynczego urządzenia). 
3. W Eksploratorze plików przejdź do folderu **config** wewnątrz rozwiązania i wybierz plik **deployment.json**. Kliknij pozycję **Wybierz manifest wdrożenia usługi Edge**. 

Jeśli wdrożenie zakończy się pomyślnie, komunikat potwierdzenia zostanie wydrukowany w danych wyjściowych programu VS Code. W eksploratorze programu VS Code rozwiń szczegółowe informacje o urządzeniu usługi IoT Edge, które zostało użyte na potrzeby tego wdrożenia. Umieść kursor na nagłówku **Urządzenia usługi Azure IoT Hub**, aby włączyć przycisk odświeżania, jeśli moduły nie pojawiają się natychmiast. Uruchomienie modułów i wysłanie raportów do usługi IoT Hub może zająć kilka chwil. 

Możesz również sprawdzić, czy wszystkie moduły są wdrożone i uruchomione na samym urządzeniu. Na urządzeniu usługi IoT Edge uruchom następujące polecenie, aby wyświetlić stan modułów. Uruchomienie modułów może zająć kilka chwil.

   ```bash
   iotedge list
   ```

## <a name="view-classification-results"></a>Wyświetlanie wyników klasyfikacji

Istnieją dwa sposoby wyświetlania wyników modułów: na samym urządzeniu podczas generowania i wysłania komunikatów lub z poziomu programu Visual Studio Code podczas docierania komunikatów do usługi IoT Hub. 

Na urządzeniu możesz wyświetlać dzienniki modułu cameraCapture, aby zobaczyć wysyłane komunikaty i potwierdzenie, że zostały one odebrane przez usługę IoT Hub. 

   ```bash
   iotedge logs cameraCapture
   ```

From Visual Studio Code, right-click on the name of your IoT Edge device and select **Start Monitoring Built-in Event Endpoint**. 

Wyniki z modułu usługi Custom Vision, które są wysyłane jako komunikaty z modułu cameraCapture, uwzględniają prawdopodobieństwo, że obraz przedstawia cykutę lub drzewo wiśni. Ponieważ obraz przedstawia cykutę, powinno być widoczne prawdopodobieństwo 1.0. 


## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli zamierzasz przejść do kolejnego zalecanego artykułu, możesz zachować utworzone zasoby oraz konfiguracje i użyć ich ponownie. Możesz także nadal używać tego samego urządzenia usługi IoT Edge jako urządzenia testowego. 

Otherwise, you can delete the local configurations and the Azure resources that you used in this article to avoid charges. 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]


## <a name="next-steps"></a>Następne kroki

W tym samouczku wyszkoliliśmy model usługi Custom Vision i wdrożyliśmy go jako moduł na urządzeniu usługi IoT Edge. Następnie skompilowaliśmy moduł, który może wysyłać zapytania do usługi klasyfikacji obrazów i raportować wyniki do usługi IoT Hub. 

Jeśli chcesz wypróbować bardziej pogłębioną wersję tego scenariusza z kanałem informacyjnym kamery na żywo, zobacz projekt GitHub [Custom Vision and Azure IoT Edge on a Raspberry Pi 3](https://github.com/Azure-Samples/Custom-vision-service-iot-edge-raspberry-pi) (Usługi Custom Vision i Azure IoT Edge na urządzeniu Raspberry Pi 3). 

Kontynuuj pracę z kolejnymi samouczkami, aby dowiedzieć się o innych metodach, za pomocą których usługa Azure IoT Edge może ułatwiać przekształcanie danych w analizy biznesowe na brzegu sieci.

> [!div class="nextstepaction"]
> [Store data at the edge with SQL Server databases (Przechowywanie danych na brzegu sieci przy użyciu baz danych programu SQL Server)](tutorial-store-data-sql-server.md)
