---
title: Moduł usługi Azure IoT Edge w języku Python | Microsoft Docs
description: Tworzenie modułu usługi IoT Edge za pomocą kodu Python i wdrażanie go na urządzeniu brzegowym
author: shizn
manager: ''
ms.author: xshi
ms.date: 03/18/2018
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 88d772306cb9e67216b380aa885284ebedc77b5f
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34632112"
---
# <a name="develop-and-deploy-a-python-iot-edge-module-to-your-simulated-device---preview"></a>Opracowywanie i wdrażanie modułu usługi IoT Edge w języku Python na urządzeniu symulowanym — wersja zapoznawcza

Moduły usługi IoT Edge umożliwiają wdrożenie kodu implementującego logikę biznesową bezpośrednio na urządzeniach usługi IoT Edge. W tym samouczku przedstawiono sposób tworzenia i wdrażania modułu usługi IoT Edge, w którym są filtrowane dane czujnika. Użyjesz symulowanego urządzenia usługi IoT Edge utworzonego podczas pracy z samouczkami dotyczącymi wdrażania usługi Azure IoT Edge na urządzeniu symulowanym w systemie [Windows][lnk-tutorial1-win] lub [Linux][lnk-tutorial1-lin]. Ten samouczek zawiera informacje na temat wykonywania następujących czynności:    

> [!div class="checklist"]
> * Tworzenie modułu usługi IoT Edge w języku Python przy użyciu programu Visual Studio Code
> * Tworzenie obrazu platformy Docker i publikowanie go w rejestrze przy użyciu programu Visual Studio Code i platformy Docker 
> * Wdrażanie modułu na urządzeniu usługi IoT Edge
> * Wyświetlanie wygenerowanych danych


Utworzony w tym samouczku moduł usługi IoT Edge filtruje dane temperatury generowane przez urządzenie. Komunikaty są wysyłane tylko wtedy, gdy temperatura przekroczy określony próg. Ten typ analizy brzegowej pomaga zmniejszyć ilość danych przekazywanych do chmury i w niej przechowywanych. 

> [!IMPORTANT]
> Obecnie moduł w języku Python można uruchomić tylko w kontenerach z systemem Linux w wersji amd64 — nie można go uruchamiać w kontenerach z systemem Windows ani w kontenerach opartych na architekturze ARM. 

## <a name="prerequisites"></a>Wymagania wstępne

* Urządzenie usługi Azure IoT Edge utworzone w ramach przewodnika Szybki start lub pierwszego samouczka.
* Parametry połączenia klucza podstawowego dla urządzenia usługi IoT Edge.  
* [Program Visual Studio Code](https://code.visualstudio.com/) 
* [Rozszerzenie usługi Azure IoT Edge dla programu Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) 
* [Rozszerzenie języka Python dla programu Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-python.python). 
* Platforma [Docker](https://docs.docker.com/engine/installation/) na tym samym komputerze, na którym działa program Visual Studio Code. Wersja Community Edition (CE) jest wystarczająca na potrzeby tego samouczka. 
* [Python](https://www.python.org/downloads/).
* Narzędzie [pip](https://pip.pypa.io/en/stable/installing/#installation) do instalacji pakietów języka Python (zazwyczaj instalowane wraz ze środowiskiem języka Python).

## <a name="create-a-container-registry"></a>Tworzenie rejestru kontenerów
W tym samouczku rozszerzenie usługi Azure IoT Edge dla programu VS Code zostanie użyte do zbudowania modułu i utworzenia **obrazu kontenera** na podstawie plików. Następnie ten obraz zostanie wypchnięty do **rejestru**, w którym obrazy są przechowywane i zarządzane. Na koniec obraz zostanie wdrożony z rejestru w celu uruchomienia na urządzeniu usługi IoT Edge.  

Na potrzeby tego samouczka możesz użyć dowolnego rejestru zgodnego z platformą Docker. Dwie popularne usługi rejestru Docker dostępne w chmurze to [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) i [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags). W tym samouczku używana jest usługa Azure Container Registry. 

1. W witrynie [Azure Portal](https://portal.azure.com) wybierz pozycję **Utwórz zasób** > **Kontenery** > **Azure Container Registry**.
2. Nazwij rejestr, wybierz subskrypcję i grupę zasobów oraz ustaw jednostkę SKU na wartość **Podstawowa**. 
3. Wybierz pozycję **Utwórz**.
4. Po utworzeniu kontenera rejestru przejdź do niego i wybierz pozycję **Klucze dostępu**. 
5. Przełącz pozycję **Administrator** na wartość **Włącz**.
6. Skopiuj wartości w polach **Serwer logowania**, **Nazwa użytkownika** i **Hasło**. Te wartości zostaną wykorzystane później w samouczku. 

## <a name="create-an-iot-edge-module-project"></a>Tworzenie projektu modułu usługi IoT Edge
W następujących krokach przedstawiono sposób tworzenia modułu usługi IoT Edge w języku Python przy użyciu programu Visual Studio Code i rozszerzenia usługi Azure IoT Edge.
1. W programie Visual Studio Code wybierz kolejno pozycje **Widok** > **Zintegrowany terminal**, aby otworzyć zintegrowany terminal programu VS Code.
2. W zintegrowanym terminalu wprowadź następujące polecenie, aby zainstalować (lub zaktualizować) narzędzie **cookiecutter** (sugerujemy wykonanie tej czynności w środowisku wirtualnym lub jako instalacji użytkownika, jak pokazano poniżej):

    ```cmd/sh
    pip install --upgrade --user cookiecutter
    ```

3. Utwórz projekt dla nowego modułu. Poniższe polecenie utworzy folder projektu, **FilterModule**, w Twoim repozytorium kontenerów. Parametr `image_repository` powinien mieć postać `<your container registry name>.azurecr.io/filtermodule`, jeśli używasz rejestru kontenerów platformy Azure. Wprowadź następujące polecenie w bieżącym folderze roboczym:

    ```cmd/sh
    cookiecutter --no-input https://github.com/Azure/cookiecutter-azure-iot-edge-module module_name=FilterModule image_repository=<your container registry address>/filtermodule
    ```
 
4. Wybierz pozycję **Plik** > **Otwórz folder**.
5. Przejdź do folderu **FilterModule** i kliknij pozycję **Wybierz folder**, aby otworzyć projekt w programie VS Code.
6. W eksploratorze programu VS Code kliknij plik **main.py**, aby go otworzyć.
7. Na początku przestrzeni nazw **FilterModule** zaimportuj bibliotekę `json`:

    ```python
    import json
    ```

8. Dodaj zmienne `TEMPERATURE_THRESHOLD`, `RECEIVE_CALLBACKS` i `TWIN_CALLBACKS` pod licznikami globalnymi. Zmienna „temperature_threshold” określa wartość zmierzonej temperatury, której przekroczenie spowoduje wysłanie danych do usługi IoT Hub.

    ```python
    TEMPERATURE_THRESHOLD = 25
    TWIN_CALLBACKS = RECEIVE_CALLBACKS = 0
    ```

9. Zaktualizuj funkcję `receive_message_callback` przy użyciu następującej zawartości.

    ```python
    # receive_message_callback is invoked when an incoming message arrives on the specified 
    # input queue (in the case of this sample, "input1").  Because this is a filter module, 
    # we will forward this message onto the "output1" queue.
    def receive_message_callback(message, hubManager):
        global RECEIVE_CALLBACKS
        global TEMPERATURE_THRESHOLD
        message_buffer = message.get_bytearray()
        size = len(message_buffer)
        message_text = message_buffer[:size].decode('utf-8')
        print("    Data: <<<{}>>> & Size={:d}".format(message_text, size))
        map_properties = message.properties()
        key_value_pair = map_properties.get_internals()
        print("    Properties: {}".format(key_value_pair))
        RECEIVE_CALLBACKS += 1
        print("    Total calls received: {:d}".format(RECEIVE_CALLBACKS))
        data = json.loads(message_text)
        if "machine" in data and "temperature" in data["machine"] and data["machine"]["temperature"] > TEMPERATURE_THRESHOLD:
            map_properties.add("MessageType", "Alert")
            print("Machine temperature {} exceeds threshold {}".format(data["machine"]["temperature"], TEMPERATURE_THRESHOLD))
        hubManager.forward_event_to_output("output1", message, 0)
        return IoTHubMessageDispositionResult.ACCEPTED
    ```

10. Dodaj nową funkcję `device_twin_callback`. Ta funkcja będzie wywoływana w przypadku zaktualizowania odpowiednich właściwości.

    ```python
    # device_twin_callback is invoked when twin's desired properties are updated.
    def device_twin_callback(update_state, payload, user_context):
        global TWIN_CALLBACKS
        global TEMPERATURE_THRESHOLD
        print("\nTwin callback called with:\nupdateStatus = {}\npayload = {}\ncontext = {}".format(update_state, payload, user_context))
        data = json.loads(payload)
        if "desired" in data and "TemperatureThreshold" in data["desired"]:
            TEMPERATURE_THRESHOLD = data["desired"]["TemperatureThreshold"]
        if "TemperatureThreshold" in data:
            TEMPERATURE_THRESHOLD = data["TemperatureThreshold"]
        TWIN_CALLBACKS += 1
        print("Total calls confirmed: {:d}\n".format(TWIN_CALLBACKS))
    ```

11. W klasie `HubManager` dodaj nowy wiersz do metody `__init__`, aby zainicjować nowo dodaną funkcję `device_twin_callback`.

    ```python
    # sets the callback when a twin's desired properties are updated.
    self.client.set_device_twin_callback(device_twin_callback, self)
    ```


12. Zapisz ten plik.

## <a name="create-a-docker-image-and-publish-it-to-your-registry"></a>Tworzenie obrazu platformy Docker i publikowanie go w rejestrze

1. Zaloguj się do platformy Docker, wprowadzając następujące polecenie w zintegrowanym terminalu programu VS Code: 
     
   ```csh/sh
   docker login -u <username> -p <password> <Login server>
   ```
        
   Użyj nazwy użytkownika, hasła i serwera logowania skopiowanych z rejestru kontenerów platformy Azure po utworzeniu go.

2. W eksploratorze programu VS Code kliknij prawym przyciskiem myszy plik **module.json** i kliknij polecenie **Skompiluj i wypchnij obraz platformy Docker modułu usługi IoT Edge**. W podręcznym polu listy rozwijanej w górnej części okna programu VS Code wybierz platformę kontenera, na przykład **amd64** dla kontenera systemu Linux. Program VS Code umieszcza plik `main.py` i wymagane zależności w kontenerze, a następnie wypycha go do określonego rejestru kontenerów. Tworzenie obrazu po raz pierwszy może potrwać kilka minut.

3. Pełny adres obrazu kontenera możesz uzyskać za pomocą tagu w zintegrowanym terminalu programu VS Code. Aby uzyskać więcej informacji na temat definicji kompilacji i wypychania, zapoznaj się z plikiem `module.json`.

## <a name="add-registry-credentials-to-edge-runtime"></a>Dodawanie poświadczeń rejestru do środowiska uruchomieniowego usługi Edge
Dodaj poświadczenia dla rejestru do środowiska uruchomieniowego usługi Edge na komputerze, na którym jest uruchomione urządzenie usługi Edge. Dzięki tym poświadczeniom środowisko uruchomieniowe może pobrać kontener. 

- W systemie Windows uruchom następujące polecenie:
    
    ```cmd/sh
    iotedgectl login --address <your container registry address> --username <username> --password <password> 
    ```

- W systemie Linux uruchom następujące polecenie:
    
    ```cmd/sh
    sudo iotedgectl login --address <your container registry address> --username <username> --password <password> 
    ```

## <a name="run-the-solution"></a>Uruchamianie rozwiązania

1. W witrynie [Azure Portal](https://portal.azure.com) przejdź do centrum IoT Hub.
2. Przejdź do pozycji **IoT Edge (wersja zapoznawcza)** i wybierz urządzenie usługi IoT Edge.
3. Wybierz pozycję **Ustaw moduły**. 
2. Sprawdź, czy moduł **tempSensor** jest automatycznie wypełniany. Jeśli nie, wykonaj następujące czynności, aby go dodać:
    1. Wybierz pozycję **Dodaj moduł usługi IoT Edge**.
    2. W polu **Nazwa** wprowadź wartość `tempSensor`.
    3. W polu **Identyfikator URI obrazu** wprowadź wartość `microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview`.
    4. Pozostaw inne ustawienia bez zmian, a następnie kliknij pozycję **Zapisz**.
9. Dodaj moduł **filterModule**, który został utworzony w poprzednich sekcjach. 
    1. Wybierz pozycję **Dodaj moduł usługi IoT Edge**.
    2. W polu **Nazwa** wprowadź wartość `filterModule`.
    3. W polu **Identyfikator URI obrazu** wprowadź adres obrazu, na przykład `<your container registry address>/filtermodule:0.0.1-amd64`. Pełen adres obrazu można znaleźć w poprzedniej sekcji.
    4. Zaznacz pole wyboru **Włącz**, aby móc edytować bliźniaczą reprezentację modułu. 
    5. Zastąp dane JSON w polu tekstowym dla bliźniaczej reprezentacji modułu następującym kodem JSON: 

        ```json
        {
           "properties.desired":{
              "TemperatureThreshold":25
           }
        }
        ```
 
    6. Kliknij pozycję **Zapisz**.
10. Kliknij przycisk **Dalej**.
11. W kroku **Określanie tras** skopiuj poniższe dane JSON do pola tekstowego. Moduły publikują wszystkie komunikaty do środowiska uruchomieniowego usługi Edge. Reguły deklaratywne w środowisku uruchomieniowym definiują trasy przepływu komunikatów. W tym samouczku będą potrzebne dwie trasy. Pierwsza trasa jest przeznaczona do transportowania komunikatów z czujnika temperatury do modułu filtru za pośrednictwem punktu końcowego „input1”, który jest punktem końcowym skonfigurowanym za pomocą procedury obsługi **FilterMessages**. Druga trasa służy do transportu komunikatów z modułu filtru do centrum IoT Hub. W tej trasie element `upstream` jest specjalnym miejscem docelowym, które nakazuje centrum Edge Hub wysyłanie komunikatów do centrum IoT Hub. 

    ```json
    {
       "routes":{
          "sensorToFilter":"FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/filterModule/inputs/input1\")",
          "filterToIoTHub":"FROM /messages/modules/filterModule/outputs/output1 INTO $upstream"
       }
    }
    ```

4. Kliknij przycisk **Dalej**.
5. W kroku **Przegląd szablonu** kliknij pozycję **Prześlij**. 
6. Wróć do strony szczegółów urządzenia usługi IoT Edge, a następnie wybierz pozycję **Odśwież**. Powinien zostać wyświetlony nowy moduł **filtermodule** uruchomiony wraz z modułem **tempSensor** i **środowiskiem uruchomieniowym usługi IoT Edge**. 

## <a name="view-generated-data"></a>Wyświetlanie wygenerowanych danych

Aby monitorować urządzenie pod kątem komunikatów w chmurze wysyłanych z urządzenia usługi IoT Edge do centrum IoT Hub:
1. Skonfiguruj rozszerzenie Azure IoT Toolkit za pomocą parametrów połączenia na potrzeby centrum IoT Hub: 
    1. Otwórz eksploratora programu VS Code, wybierając pozycję **Widok** > **Eksplorator**. 
    3. W eksploratorze kliknij pozycję **IOT HUB DEVICES**, a następnie kliknij pozycję **...**. Kliknij pozycję **Ustaw parametry połączenia centrum IoT Hub** i w oknie podręcznym wprowadź parametry połączenia dla centrum IoT Hub, z którym nawiązuje połączenie urządzenie usługi IoT Edge. 

        Aby znaleźć parametry połączenia, kliknij kafelek centrum IoT Hub w witrynie Azure Portal, a następnie kliknij pozycję **Zasady dostępu współużytkowanego**. W obszarze **Zasady dostępu współużytkowanego** kliknij zasady **iothubowner** i skopiuj parametry połączenia usługi IoT Hub w oknie **iothubowner**.   

1. Aby monitorować dane otrzymywane przez centrum IoT Hub, wybierz pozycję **Widok** > **Paleta poleceń**, a następnie wyszukaj polecenie menu **IoT: rozpocznij monitorowania komunikatu D2C**. 
2. Aby zatrzymać monitorowanie danych, użyj polecenia menu **IoT: zatrzymaj monitorowanie komunikatu D2C**. 

## <a name="next-steps"></a>Następne kroki

W tym samouczku został utworzony moduł usługi IoT Edge zawierający kod służący do filtrowania nieprzetworzonych danych wygenerowanych przez urządzenie usługi IoT Edge. Możesz teraz kontynuować pracę, korzystając z dowolnego z następujących samouczków, aby dowiedzieć się o innych metodach, za pomocą których usługa Azure IoT Edge może ułatwiać przekształcanie danych w analizy biznesowe na urządzeniach brzegowych.

> [!div class="nextstepaction"]
> [Wdrażanie funkcji platformy Azure jako modułu](tutorial-deploy-function.md)
> [Wdrażanie usługi Azure Stream Analytics jako modułu](tutorial-deploy-stream-analytics.md)


<!-- Links -->
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md

<!-- Images -->
[1]: ./media/tutorial-csharp-module/programcs.png
[2]: ./media/tutorial-csharp-module/build-module.png
[3]: ./media/tutorial-csharp-module/docker-os.png
