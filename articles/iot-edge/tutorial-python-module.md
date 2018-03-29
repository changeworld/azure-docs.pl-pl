---
title: Moduł Python krawędzi IoT Azure | Dokumentacja firmy Microsoft
description: Utwórz moduł krawędzi IoT z kodem języka Python i wdróż je do urządzenia
services: iot-edge
keywords: ''
author: shizn
manager: timlt
ms.author: xshi
ms.date: 03/18/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: d5bad277e6a54b23f0e3ef7321e82d212ae885d3
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2018
---
# <a name="develop-and-deploy-a-python-iot-edge-module-to-your-simulated-device---preview"></a>Tworzenie i wdrażanie modułu Python IoT krawędzi w symulowane urządzenie — w wersji preview

Moduły krawędzi IoT umożliwia wdrażanie kodu, który implementuje logiki biznesowej bezpośrednio do urządzenia IoT krawędzi. W tym samouczku przedstawiono sposób tworzenia i wdrażania moduł krawędzi IoT filtrujące danych czujnika. Użyjesz symulowane urządzenie brzegowe IoT utworzoną w programie IoT Edge Azure wdrożenia symulowanego urządzenia w [Windows] [ lnk-tutorial1-win] lub [Linux] [ lnk-tutorial1-lin]samouczki. Ten samouczek zawiera informacje na temat wykonywania następujących czynności:    

> [!div class="checklist"]
> * Użyj programu Visual Studio Code, aby utworzyć moduł Python krawędzi IoT
> * Użyj programu Visual Studio Code i Docker, aby utworzyć obraz docker i opublikować ją w rejestrze 
> * Wdrażanie modułu do Twojego urządzenia IoT krawędzi
> * Widok wygenerowany danych


Moduł IoT krawędzi, który możesz utworzyć w tym samouczku filtruje temperatury dane generowane przez urządzenie. Tylko wysyła komunikaty powyżej jeśli temperatura przekracza określoną wartość progową. Ten typ analizy na krawędzi jest przydatne w przypadku zmniejszenie ilości danych przekazane i przechowywane w chmurze. 

> [!IMPORTANT]
> Obecnie modułu Python mogą być uruchomione w kontenerach Linux amd64. Nie działa w kontenerach systemu Windows lub kontenery opartego na architekturze ARM. 

## <a name="prerequisites"></a>Wymagania wstępne

* Urządzenie brzegowe IoT Azure utworzoną w pierwszym samouczku lub Szybki Start.
* Parametry połączenia klucza podstawowego dla urządzenia usługi IoT Edge.  
* [Visual Studio Code](https://code.visualstudio.com/). 
* [Rozszerzenie krawędzi IoT Azure dla programu Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge). 
* [Rozszerzenia języka Python dla programu Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-python.python). 
* [Docker](https://docs.docker.com/engine/installation/) na tym samym komputerze, na którym Visual Studio Code. Community Edition (CE) jest wystarczająca dla tego samouczka. 
* [Python](https://www.python.org/downloads/).
* [PIP](https://pip.pypa.io/en/stable/installing/#installation) instalowania pakietów języka Python.

## <a name="create-a-container-registry"></a>Tworzenie rejestru kontenerów
W tym samouczku umożliwia rozszerzenie Azure IoT krawędzi kodzie VS utworzenie modułu i Utwórz **obrazu kontenera** z plików. A następnie Wypchnij tego obrazu, aby **rejestru** który przechowywanych i zarządzanych obrazów. Ponadto można wdrożyć obraz z rejestru do uruchomienia na urządzeniu IoT krawędzi.  

W tym samouczku, można użyć dowolnego rejestru zgodnego Docker. Są dwa popularne Docker rejestru usług dostępnych w chmurze [rejestru kontenera Azure](https://docs.microsoft.com/azure/container-registry/) i [Centrum Docker](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags). W tym samouczku korzysta z rejestru kontenera platformy Azure. 

1. W [portalu Azure](https://portal.azure.com), wybierz pozycję **Utwórz zasób** > **kontenery** > **rejestru kontenera Azure** .
2. Nadaj nazwę rejestru, wybierz subskrypcję, wybierz grupę zasobów i ustawić jednostki SKU **podstawowe**. 
3. Wybierz pozycję **Utwórz**.
4. Po utworzeniu kontenera rejestru przejdź do niego i wybierz **klucze dostępu**. 
5. Przełącz **administrator** do **włączyć**.
6. Skopiuj wartości **logowania serwera**, **Username**, i **hasło**. Te wartości będą używane w dalszej części tego samouczka. 

## <a name="create-an-iot-edge-module-project"></a>Tworzenie projektu modułu krawędzi IoT
Poniższe kroki pokazują, jak utworzyć moduł Python krawędzi IoT przy użyciu programu Visual Studio Code i rozszerzenie Azure IoT krawędzi.
1. W programie Visual Studio Code, wybierz **widoku** > **zintegrowane terminali** otworzyć terminal zintegrowane kodzie VS.
2. W terminalu zintegrowanego, wprowadź następujące polecenie, aby zainstalować (lub zaktualizować) **cookiecutter**:

    ```cmd/sh
    pip install -U cookiecutter
    ```

3. Tworzenie projektu dla nowego modułu. Poniższe polecenie tworzy folder projektu **FilterModule**, z Twoim repozytorium kontenera. Parametr `image_repository` powinien być w formie `<your container registry name>.azurecr.io/filtermodule` Jeśli używasz rejestru kontenera platformy Azure. W bieżącym folderze pracy, wprowadź następujące polecenie:

    ```cmd/sh
    cookiecutter --no-input https://github.com/Azure/cookiecutter-azure-iot-edge-module module_name=FilterModule image_repository=<your container registry address>/filtermodule
    ```
 
4. Wybierz **pliku** > **Otwórz Folder**.
5. Przejdź do **FilterModule** folder i kliknij przycisk **wybierz Folder** otworzyć projektu w kodzie VS.
6. W kodzie VS explorer, kliknij przycisk **main.py** go otworzyć.
7. W górnej części **FilterModule** przestrzeni nazw, importu `json` biblioteki:

    ```python
    import json
    ```

8. Dodaj `TEMPERATURE_THRESHOLD` i `TWIN_CALLBACKS` w obszarze globalne liczniki. Próg temperatury ustawia wartość, która może przekraczać zmierzone temperatury dane do wysłania do Centrum IoT.

    ```python
    TEMPERATURE_THRESHOLD = 25
    TWIN_CALLBACKS = 0
    ```

9. Aktualizacja funkcji `receive_message_callback` z poniżej zawartości.

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
        print ( "    Data: <<<%s>>> & Size=%d" % (message_text, size) )
        map_properties = message.properties()
        key_value_pair = map_properties.get_internals()
        print ( "    Properties: %s" % key_value_pair )
        RECEIVE_CALLBACKS += 1
        print ( "    Total calls received: %d" % RECEIVE_CALLBACKS )
        data = json.loads(message_text)
        if "machine" in data and "temperature" in data["machine"] and data["machine"]["temperature"] > TEMPERATURE_THRESHOLD:
            map_properties.add("MessageType", "Alert")
            print("Machine temperature %s exceeds threshold %s" % (data["machine"]["temperature"], TEMPERATURE_THRESHOLD))
        hubManager.forward_event_to_output("output1", message, 0)
        return IoTHubMessageDispositionResult.ACCEPTED
    ```

10. Dodaj nową funkcję `device_twin_callback`. Ta funkcja zostanie wywołany, gdy żądany właściwości są aktualizowane.

    ```python
    # device_twin_callback is invoked when twin's desired properties are updated.
    def device_twin_callback(update_state, payload, user_context):
        global TWIN_CALLBACKS
        global TEMPERATURE_THRESHOLD
        print ( "\nTwin callback called with:\nupdateStatus = %s\npayload = %s\ncontext = %s" % (update_state, payload, user_context) )
        data = json.loads(payload)
        if "desired" in data and "TemperatureThreshold" in data["desired"]:
            TEMPERATURE_THRESHOLD = data["desired"]["TemperatureThreshold"]
        if "TemperatureThreshold" in data:
            TEMPERATURE_THRESHOLD = data["TemperatureThreshold"]
        TWIN_CALLBACKS += 1
        print ( "Total calls confirmed: %d\n" % TWIN_CALLBACKS )
    ```

11. W klasie `HubManager`, Dodaj nowy wiersz do `__init__` metodę, aby zainicjować `device_twin_callback` funkcja właśnie został dodany.

    ```python
    # sets the callback when a twin's desired properties are updated.
    self.client.set_device_twin_callback(device_twin_callback, self)
    ```


12. Zapisz ten plik.

## <a name="create-a-docker-image-and-publish-it-to-your-registry"></a>Tworzenie obrazu Docker i publikowanie go do rejestru

1. Zaloguj się do Docker, wprowadzając następujące polecenie w kodzie VS terminal zintegrowane: 
     
   ```csh/sh
   docker login -u <username> -p <password> <Login server>
   ```
        
   Użyj nazwy użytkownika, hasło i serwer logowania, skopiowany z rejestru kontenera platformy Azure podczas jego tworzenia.

2. W kodzie VS explorer, kliknij prawym przyciskiem myszy **module.json** plik i kliknij przycisk **kompilacji i Push IoT krawędzi obrazu Docker modułu**. W polu listy rozwijanej wyskakujących w górnej części okna kodu VS Wybierz kontener platformy, na przykład **amd64** kontenera systemu Linux. Kod VS containerize `main.py` i wymaganych zależności, następnie wypchnąć go do określonego rejestru kontenera. Może upłynąć kilka minut sieci po raz pierwszy utworzyć obraz.

3. Możesz uzyskać adres pełne kontenera obrazu znacznika w kodzie VS zintegrowane terminala. Aby uzyskać więcej informacji na temat definicji kompilacji i wypychania mogą odwoływać się do `module.json` pliku.

## <a name="add-registry-credentials-to-edge-runtime"></a>Dodawanie poświadczeń rejestru do środowiska wykonawczego krawędzi
Dodaj poświadczenia do rejestru do środowiska wykonawczego Edge na komputerze, na którym są uruchomione Twoje urządzenie brzegowe. Te poświadczenia zapewniają dostęp środowiska uruchomieniowego do ściągnięcia kontenera. 

- W systemie Windows uruchom następujące polecenie:
    
    ```cmd/sh
    iotedgectl login --address <your container registry address> --username <username> --password <password> 
    ```

- Dla systemu Linux uruchom następujące polecenie:
    
    ```cmd/sh
    sudo iotedgectl login --address <your container registry address> --username <username> --password <password> 
    ```

## <a name="run-the-solution"></a>Uruchamianie rozwiązania

1. W [portalu Azure](https://portal.azure.com), przejdź do Centrum IoT.
2. Przejdź do pozycji **IoT Edge (wersja zapoznawcza)** i wybierz urządzenie usługi IoT Edge.
3. Wybierz **ustawić modułów**. 
2. Sprawdź, czy **tempSensor** modułu jest wypełniane automatycznie. Jeśli nie, należy dodać ją za pomocą następujących czynności:
    1. Wybierz **Dodaj moduł krawędzi IoT**.
    2. W **nazwa** wprowadź `tempSensor`.
    3. W **identyfikatora URI obrazu** wprowadź `microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview`.
    4. Pozostaw innych ustawień bez zmian i kliknij przycisk **zapisać**.
9. Dodaj **filterModule** moduł, który został utworzony w poprzednich sekcjach. 
    1. Wybierz **Dodaj moduł krawędzi IoT**.
    2. W **nazwa** wprowadź `filterModule`.
    3. W **identyfikatora URI obrazu** wprowadź swój adres obrazu, na przykład `<your container registry address>/filtermodule:0.0.1-amd64`. Adres pełnego obrazu można znaleźć w poprzedniej sekcji.
    4. Sprawdź **włączyć** , dzięki czemu można edytować dwie modułu. 
    5. Zastąp dane JSON w polu tekstowym dla modułu dwie następujące JSON: 

        ```json
        {
           "properties.desired":{
              "TemperatureThreshold":25
           }
        }
        ```
 
    6. Kliknij pozycję **Zapisz**.
10. Kliknij przycisk **Dalej**.
11. W **Określ tras** kroku, skopiuj kod JSON poniżej w polu tekstowym. Moduły opublikować wszystkie komunikaty do środowiska wykonawczego krawędzi. Deklaracyjne reguły w środowisku uruchomieniowym definiują, do których przepływu wiadomości. W tym samouczku należy na dwa sposoby. Pierwszy trasy transportu wiadomości z czujnika temperatury w module filtru za pośrednictwem punktu końcowego "input1", który jest punkt końcowy, który został skonfigurowany z **FilterMessages** programu obsługi. Drugi trasy transportu wiadomości z modułem filtru do Centrum IoT. W tej trasy `upstream` jest specjalne lokalizacji docelowej, która informuje krawędzi koncentratora do wysyłania komunikatów do Centrum IoT. 

    ```json
    {
       "routes":{
          "sensorToFilter":"FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/filterModule/inputs/input1\")",
          "filterToIoTHub":"FROM /messages/modules/filterModule/outputs/output1 INTO $upstream"
       }
    }
    ```

4. Kliknij przycisk **Dalej**.
5. W **szablon przeglądu** kroku, kliknij przycisk **przesyłania**. 
6. Wróć do strony szczegółów urządzenia IoT krawędzi, a następnie kliknij przycisk **Odśwież**. Powinien zostać wyświetlony nowy **filtermodule** uruchomiona wraz z **tempSensor** modułu i **środowiska uruchomieniowego krawędzi IoT**. 

## <a name="view-generated-data"></a>Widok wygenerowany danych

Aby monitorować urządzenia do chmury wiadomości wysyłane z urządzenia IoT krawędzi do Centrum IoT:
1. Rozszerzenia Azure IoT Toolkit należy skonfigurować parametry połączenia Centrum IoT: 
    1. Otwórz Eksploratora kodzie VS wybierając **widoku** > **Explorer**. 
    3. W Eksploratorze kliknij **urządzenia IOT HUB** , a następnie kliknij przycisk **...** . Kliknij przycisk **ustawić parametry połączenia Centrum IoT** , a następnie wprowadź parametry połączenia dla Centrum IoT, które urządzenia IoT krawędzi łączy w wyskakującym oknie. 

        Aby znaleźć ciąg połączenia, kliknij Kafelek Centrum IoT w portalu Azure, a następnie kliknij przycisk **zasady dostępu współużytkowanego**. W **zasady dostępu współużytkowanego**, kliknij przycisk **iothubowner** zasad i skopiuj połączenia Centrum IoT ciąg w **iothubowner** okna.   

1. Do monitorowania danych otrzymywanych przez Centrum IoT, wybierz **widoku** > **palety polecenia** i wyszukaj **IoT: rozpocząć monitorowanie komunikat D2C** polecenia menu. 
2. Aby zatrzymać monitorowanie danych, należy użyć **IoT: zatrzymać monitorowanie komunikat D2C** polecenia menu. 

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku utworzony moduł IoT krawędzi, który zawiera kod, aby filtrować nieprzetworzone dane generowane przez urządzenia IoT krawędzi. Można kontynuować do jednej z następujących samouczków, aby uzyskać informacje o innych metod, które ułatwiają krawędź IoT Azure możesz przekształcić w danych biznesowych na krawędzi.

> [!div class="nextstepaction"]
> [Wdrażanie funkcji platformy Azure jako moduł](tutorial-deploy-function.md)
> [wdrożenia usługi Azure Stream Analytics jako moduł](tutorial-deploy-stream-analytics.md)


<!-- Links -->
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md

<!-- Images -->
[1]: ./media/tutorial-csharp-module/programcs.png
[2]: ./media/tutorial-csharp-module/build-module.png
[3]: ./media/tutorial-csharp-module/docker-os.png
