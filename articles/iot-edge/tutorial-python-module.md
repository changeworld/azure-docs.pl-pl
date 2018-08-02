---
title: Samouczek usługi Azure IoT Edge dla języka Python | Microsoft Docs
description: W tym samouczku pokazano, jak utworzyć moduł usługi IoT Edge za pomocą kodu języka Python i wdrożyć go na urządzeniu brzegowym.
services: iot-edge
author: shizn
manager: timlt
ms.author: xshi
ms.date: 06/26/2018
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: 49fa81d89fb195e1caedc2348a8b0990022b0d0d
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2018
ms.locfileid: "39414270"
---
# <a name="tutorial-develop-and-deploy-a-python-iot-edge-module-to-your-simulated-device"></a>Samouczek: opracowywanie modułu usługi IoT Edge w języku Python i wdrażanie go na urządzeniu symulowanym

Moduły usługi Azure IoT Edge umożliwiają wdrożenie kodu implementującego logikę biznesową bezpośrednio na urządzeniach usługi IoT Edge. W tym samouczku przedstawiono sposób tworzenia i wdrażania modułu usługi IoT Edge, w którym są filtrowane dane czujnika. Użyjesz symulowanego urządzenia usługi IoT Edge utworzonego podczas pracy z przewodnikami Szybki start dotyczącymi wdrażania usługi Azure IoT Edge na urządzeniu symulowanym w systemie [Windows][lnk-quickstart-win] lub [Linux][lnk-quickstart-lin]. Ten samouczek zawiera informacje na temat wykonywania następujących czynności:    

> [!div class="checklist"]
> * Tworzenie modułu usługi IoT Edge w języku Python przy użyciu programu Visual Studio Code.
> * Tworzenie obrazu platformy Docker i publikowanie go w rejestrze przy użyciu programu Visual Studio Code i platformy Docker.
> * Wdrażanie modułu na urządzeniu usługi IoT Edge.
> * Wyświetlanie wygenerowanych danych.


Utworzony w tym samouczku moduł usługi IoT Edge filtruje dane temperatury generowane przez urządzenie. Komunikaty są wysyłane tylko wtedy, gdy temperatura przekroczy określony próg. Ten typ analizy brzegowej pomaga zmniejszyć ilość danych przekazywanych do chmury i w niej przechowywanych. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Wymagania wstępne

* Urządzenie usługi Azure IoT Edge utworzone w ramach przewodnika Szybki start dla urządzeń z systemem [Linux](quickstart-linux.md).

   >[!Note]
   >Moduły języka Python dla usługi Azure IoT Edge nie obsługują urządzeń z systemem Windows lub ARM. 

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
4. Po utworzeniu rejestru kontenerów przejdź do niego i wybierz pozycję **Klucze dostępu**. 
5. Przełącz pozycję **Administrator** na wartość **Włącz**.
6. Skopiuj wartości w polach **Serwer logowania**, **Nazwa użytkownika** i **Hasło**. Te wartości zostaną wykorzystane później w samouczku. 

## <a name="create-an-iot-edge-module-project"></a>Tworzenie projektu modułu usługi IoT Edge
W następujących krokach przedstawiono sposób tworzenia modułu usługi IoT Edge w języku Python przy użyciu programu Visual Studio Code i rozszerzenia usługi Azure IoT Edge.

### <a name="create-a-new-solution"></a>Tworzenie nowego rozwiązania

Użyj pakietu **cookiecutter** języka Python, aby utworzyć szablon rozwiązania języka Python, na podstawie którego można dalej tworzyć rozwiązanie. 

1. W programie Visual Studio Code wybierz kolejno pozycje **Widok** > **Zintegrowany terminal**, aby otworzyć zintegrowany terminal programu VS Code.

2. W zintegrowanym terminalu wprowadź następujące polecenie, aby zainstalować (lub zaktualizować) pakiet **cookiecutter**, używany do tworzenia szablonu rozwiązania usługi IoT Edge w programie VS Code:

    ```cmd/sh
    pip install --upgrade --user cookiecutter
    ```

3. Wybierz kolejno opcje **Widok** > **Paleta poleceń**, aby otworzyć paletę poleceń programu VS Code. 

4. W palecie poleceń wprowadź i uruchom polecenie **Azure: zaloguj się**, a następnie postępuj zgodnie z instrukcjami, aby zalogować się na koncie platformy Azure. Jeśli już się zalogowano, można pominąć ten krok.

5. W palecie poleceń wprowadź i uruchom polecenie **Azure IoT Edge: nowe rozwiązanie usługi IoT Edge**. W palecie poleceń podaj następujące informacje, aby utworzyć rozwiązanie: 

   1. Wybierz folder, w którym chcesz utworzyć rozwiązanie. 
   2. Podaj nazwę rozwiązania lub zaakceptuj nazwę domyślną **EdgeSolution**.
   3. Wybierz szablon modułu **Moduł języka Python**. 
   4. Nazwij moduł **PythonModule**. 
   5. Określ rejestr kontenerów platformy Azure utworzony w poprzedniej sekcji jako repozytorium obrazów dla pierwszego modułu. Zastąp ciąg **localhost:5000** skopiowaną wartością serwera logowania. Ostateczny ciąg jest następujący \<nazwa rejestru\>.azurecr.io/pythonmodule.
 
W oknie programu VS Code zostanie załadowany obszar roboczy rozwiązania usługi IoT Edge: folder modules, plik szablonu manifestu wdrożenia i plik \.env. 

### <a name="add-your-registry-credentials"></a>Dodawanie poświadczeń rejestru

W pliku środowiska przechowywane są poświadczenia repozytorium kontenera, udostępniane środowisku uruchomieniowemu usługi IoT Edge. Środowisko uruchomieniowe wymaga tych poświadczeń do ściągnięcia prywatnych obrazów na urządzenie usługi IoT Edge. 

1. W eksploratorze programu VS Code otwórz plik env. 
2. Zaktualizuj pola, używając **nazwy użytkownika** i **hasła**, które zostały skopiowane z usługi Azure Container Registry. 
3. Zapisz ten plik. 

### <a name="update-the-module-with-custom-code"></a>Aktualizowanie modułu przy użyciu kodu niestandardowego

Każdy szablon zawiera przykładowy kod, który przyjmuje symulowane dane czujnika z modułu **tempSensor** i kieruje je do centrum IoT Hub. W tej sekcji dodasz kod, który rozszerza moduł **pythonModule** w celu analizowania komunikatów przed ich wysłaniem. 

1. W eksploratorze programu VS Code otwórz kolejno pozycje **modules** > **PythonModule** > **main.py**.

2. Na początku pliku **main.py** zaimportuj bibliotekę **json**:

    ```python
    import json
    ```

3. Dodaj zmienne **TEMPERATURE_THRESHOLD** i **TWIN_CALLBACKS** w obszarze liczników globalnych. Zmienna „temperature_threshold” określa wartość zmierzonej temperatury maszyny, której przekroczenie spowoduje wysłanie danych do centrum IoT Hub.

    ```python
    TEMPERATURE_THRESHOLD = 25
    TWIN_CALLBACKS = 0
    ```

4. Zastąp funkcję **receive_message_callback** następującym kodem:

    ```python
    # receive_message_callback is invoked when an incoming message arrives on the specified 
    # input queue (in the case of this sample, "input1").  Because this is a filter module, 
    # we forward this message to the "output1" queue.
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

5. Dodaj nową funkcję o nazwie **module_twin_callback**. Ta funkcja jest wywoływana w przypadku zaktualizowania odpowiednich właściwości.

    ```python
    # module_twin_callback is invoked when the module twin's desired properties are updated.
    def module_twin_callback(update_state, payload, user_context):
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

6. W klasie **HubManager** dodaj nowy wiersz do metody **__init__**, aby zainicjować właśnie dodaną funkcję **module_twin_callback**:

    ```python
    # Sets the callback when a module twin's desired properties are updated.
    self.client.set_module_twin_callback(module_twin_callback, self)
    ```

7. Zapisz ten plik.

## <a name="build-your-iot-edge-solution"></a>Kompilowanie rozwiązania usługi IoT Edge

W poprzedniej sekcji utworzono rozwiązanie usługi IoT Edge i dodano kod do modułu **PythonModule** w celu filtrowania komunikatów, w których zgłoszona temperatura maszyny jest poniżej akceptowalnego poziomu. Teraz należy skompilować to rozwiązanie jako obraz kontenera i wypchnąć go do rejestru kontenerów. 

1. Zaloguj się do platformy Docker, wprowadzając następujące polecenie w zintegrowanym terminalu programu Visual Studio Code. Następnie możesz wypchnąć obraz modułu do rejestru kontenerów platformy Azure: 
     
   ```csh/sh
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```
   Użyj nazwy użytkownika, hasła i serwera logowania skopiowanych z rejestru kontenerów platformy Azure w pierwszej sekcji. Możesz także pobrać te wartości z sekcji **Klucze dostępu** rejestru w witrynie Azure Portal.

2. W eksploratorze programu VS Code otwórz plik deployment.template.json w obszarze roboczym rozwiązania usługi IoT Edge. 

   Ten plik wydaje agentowi **$edgeAgent** polecenie wdrożenia dwóch modułów: **tempSensor**, który symuluje dane urządzenia, i **PythonModule**. Wartość **PythonModule.image** jest ustawiona na wersję obrazu Linux amd64. Aby dowiedzieć się więcej na temat manifestów wdrożenia, zobacz [Jak używać modułów usługi IoT Edge, konfigurować je i używać ich ponownie](module-composition.md).

   Ten plik zawiera także poświadczenia rejestru. W pliku szablonu nazwa użytkownika i hasło są zastąpione symbolami zastępczymi. Podczas generowania manifestu wdrożenia te pola są aktualizowane przy użyciu wartości dodanych do pliku env. 

3. Dodaj bliźniaczą reprezentację modułu **PythonModule** do manifestu wdrożenia. Wstaw następującą zawartość do pliku JSON na końcu sekcji **moduleContent**, po bliźniaczej reprezentacji modułu **$edgeHub**: 
    ```json
        "PythonModule": {
            "properties.desired":{
                "TemperatureThreshold":25
            }
        }
    ```

4. Zapisz ten plik.

5. W eksploratorze programu VS Code kliknij prawym przyciskiem myszy plik deployment.template.json i wybierz polecenie **Skompiluj rozwiązanie usługi IoT Edge**. 

Po wybraniu polecenia kompilowania rozwiązania w programie Visual Studio Code program najpierw pobiera informacje z szablonu wdrożenia i generuje plik deployment.json w nowym folderze **config**. Następnie uruchamia dwa polecenia w zintegrowanym terminalu: `docker build` i `docker push`. Te dwa polecenia kompilują kod, konteneryzują kod Python i wypychają go do rejestru kontenera określonego podczas inicjowania rozwiązania. 

Pełny adres obrazu kontenera możesz uzyskać za pomocą tagu w poleceniu `docker build` uruchamianym w zintegrowanym terminalu programu VS Code. Adres obrazu składa się z informacji z pliku module.json w formacie \<repozytorium\>:\<wersja\>-\<platforma\>. W tym samouczku powinien wyglądać następująco: registryname.azurecr.io/pythonmodule:0.0.1-amd64.

## <a name="deploy-and-run-the-solution"></a>Wdrażanie i uruchamianie rozwiązania

Do wdrożenia modułu Python na urządzeniu usługi IoT Edge możesz użyć witryny Azure Portal, tak jak w przewodnikach Szybki start. Możesz również wdrażać i monitorować moduły z poziomu programu Visual Studio Code. W następujących sekcjach używane jest rozszerzenie usługi Azure IoT Edge dla programu Visual Studio Code, wymienione w wymaganiach wstępnych. Zainstaluj teraz rozszerzenie, jeśli jeszcze tego nie zrobiono. 

1. Otwórz paletę poleceń programu VS Code, wybierając kolejno opcje **Widok** > **Paleta poleceń**.

2. Wyszukaj i uruchom polecenie **Azure: zaloguj**. Postępuj zgodnie z instrukcjami, aby zalogować się na swoim koncie platformy Azure. 

3. W palecie poleceń wyszukaj i uruchom polecenie **Azure IoT Hub: wybierz centrum IoT Hub**. 

4. Wybierz subskrypcję, która zawiera Twoje centrum IoT Hub, a następnie wybierz centrum IoT Hub, do którego chcesz uzyskać dostęp.

5. W eksploratorze programu VS rozwiń sekcję **Urządzenia usługi Azure IoT Hub**. 

6. Kliknij prawym przyciskiem myszy nazwę urządzenia usługi IoT Edge, a następnie wybierz pozycję **Utwórz wdrożenie dla urządzenia usługi IoT Edge**. 

7. Przejdź do folderu rozwiązania, który zawiera moduł **PythonModule**. Otwórz folder config, wybierz plik deployment.json, a następnie wybierz pozycję **Wybierz manifest wdrożenia usługi Edge**.

8. Odśwież sekcję **Urządzenia usługi Azure IoT Hub**. Powinien zostać wyświetlony nowy moduł **PythonModule** uruchomiony wraz z modułami **TempSensor**, **$edgeAgent** i **$edgeHub**. 

## <a name="view-generated-data"></a>Wyświetlanie wygenerowanych danych

1. Aby monitorować dane, które docierają do centrum IoT Hub, wybierz przycisk wielokropka (**...**), a następnie wybierz pozycję **Rozpocznij monitorowanie komunikatów D2C**.
2. Aby monitorować komunikat D2C dla określonego urządzenia, kliknij prawym przyciskiem myszy to urządzenie na liście, a następnie wybierz opcję **Rozpocznij monitorowanie komunikatów D2C**.
3. Aby zatrzymać monitorowanie danych, uruchom polecenie **Azure IoT Hub: zatrzymaj monitorowanie komunikatu D2C** w palecie poleceń. 
4. Aby wyświetlić lub zaktualizować bliźniaczą reprezentację modułu, kliknij prawym przyciskiem myszy moduł na liście, a następnie wybierz opcję **Edytuj bliźniaczą reprezentację modułu**. Aby zaktualizować bliźniaczą reprezentację modułu, zapisz jej plik JSON, kliknij prawym przyciskiem myszy obszar edytora i wybierz pozycję **Zaktualizuj bliźniaczą reprezentację modułu**.
5. Aby wyświetlić dzienniki platformy Docker, zainstaluj rozszerzenie [Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) dla narzędzia VS Code. Uruchomione moduły można znaleźć lokalnie w eksploratorze platformy Docker. W menu kontekstowym kliknij opcję **Pokaż dzienniki**, aby wyświetlić je w zintegrowanym terminalu. 

## <a name="clean-up-resources"></a>Oczyszczanie zasobów 

<!--[!INCLUDE [iot-edge-quickstarts-clean-up-resources](../../includes/iot-edge-quickstarts-clean-up-resources.md)] -->

Jeśli zamierzasz przejść do kolejnego zalecanego artykułu, możesz zachować utworzone zasoby oraz konfiguracje i użyć ich ponownie.

W przeciwnym razie możesz usunąć konfigurację lokalną i zasoby platformy Azure utworzone podczas pracy z tym artykułem, aby uniknąć naliczania opłat. 

> [!IMPORTANT]
> Usunięcie zasobów i grup zasobów platformy Azure jest nieodwracalne. Po usunięciu tych elementów grupa zasobów oraz wszystkie zawarte w niej zasoby zostaną trwale usunięte. Uważaj, aby nie usunąć przypadkowo niewłaściwych zasobów lub niewłaściwej grupy zasobów. Jeśli centrum IoT Hub zostało utworzone w istniejącej grupie zasobów zawierającej zasoby, które chcesz zachować, zamiast usuwać całą grupę zasobów, usuń tylko zasób centrum IoT Hub.
>

Aby usunąć tylko centrum IoT Hub, uruchom następujące polecenie, używając nazwy centrum i nazwy grupy zasobów:

```azurecli-interactive
az iot hub delete --name MyIoTHub --resource-group TestResources
```


Aby usunąć całą grupę zasobów na podstawie nazwy:

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) i wybierz pozycję **Grupy zasobów**.

2. W polu tekstowym **Filtruj według nazwy** wpisz nazwę grupy zasobów zawierającej centrum IoT Hub. 

3. Po prawej stronie grupy zasobów na liście wyników wybierz wielokropek (**...**), a następnie wybierz pozycję **Usuń grupę zasobów**.

4. Zobaczysz prośbę o potwierdzenie usunięcia grupy zasobów. Ponownie wprowadź nazwę grupy zasobów w celu potwierdzenia, a następnie wybierz pozycję **Usuń**. Po krótkim czasie grupa zasobów i wszystkie zawarte w niej zasoby zostaną usunięte.

## <a name="next-steps"></a>Następne kroki

W tym samouczku został utworzony moduł usługi IoT Edge zawierający kod służący do filtrowania nieprzetworzonych danych wygenerowanych przez urządzenie usługi IoT Edge. Możesz teraz kontynuować pracę, korzystając z kolejnych samouczków, aby poznać inne metody, za pomocą których usługa Azure IoT Edge może ułatwiać przekształcanie danych w analizy biznesowe na urządzeniach brzegowych.

> [!div class="nextstepaction"]
> [Wdrażanie funkcji platformy Azure jako modułu](tutorial-deploy-function.md)
> [Wdrażanie usługi Azure Stream Analytics jako modułu](tutorial-deploy-stream-analytics.md)


<!-- Links -->
[lnk-quickstart-win]: quickstart.md
[lnk-quickstart-lin]: quickstart-linux.md

<!-- Images -->
[1]: ./media/tutorial-csharp-module/programcs.png
[2]: ./media/tutorial-csharp-module/build-module.png
[3]: ./media/tutorial-csharp-module/docker-os.png
