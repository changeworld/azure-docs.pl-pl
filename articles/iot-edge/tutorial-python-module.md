---
title: Tworzenie niestandardowego modułu Python — Azure IoT Edge | Dokumentacja firmy Microsoft
description: W tym samouczku pokazano, jak utworzyć moduł usługi IoT Edge za pomocą kodu języka Python i wdrożyć go na urządzeniu brzegowym.
services: iot-edge
author: shizn
manager: philmea
ms.reviewer: kgremban
ms.author: xshi
ms.date: 03/24/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc, seodec18
ms.openlocfilehash: 0affd965bbfc587933a9cdbf5b96c470a6e4dd6a
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62096202"
---
# <a name="tutorial-develop-and-deploy-a-python-iot-edge-module-to-your-simulated-device"></a>Samouczek: Opracowywanie i wdrażanie modułu usługi IoT Edge w języku Python na urządzeniu symulowanym

Moduły usługi Azure IoT Edge umożliwiają wdrożenie kodu implementującego logikę biznesową bezpośrednio na urządzeniach usługi IoT Edge. Ten samouczek zawiera instrukcje dotyczące tworzenia i wdrażania moduł usługi IoT Edge, która filtrowania danych czujników na urządzeniu usługi IoT Edge, skonfigurowanej w przewodniku Szybki Start. Ten samouczek zawiera informacje na temat wykonywania następujących czynności:    

> [!div class="checklist"]
> * Tworzenie modułu usługi IoT Edge w języku Python przy użyciu programu Visual Studio Code.
> * Tworzenie obrazu platformy Docker i publikowanie go w rejestrze przy użyciu programu Visual Studio Code i platformy Docker.
> * Wdrażanie modułu na urządzeniu usługi IoT Edge.
> * Wyświetlanie wygenerowanych danych.


Utworzony w tym samouczku moduł usługi IoT Edge filtruje dane temperatury generowane przez urządzenie. Komunikaty są wysyłane tylko wtedy, gdy temperatura przekroczy określony próg. Ten typ analizy brzegowej pomaga zmniejszyć ilość danych przekazywanych do chmury i w niej przechowywanych. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Wymagania wstępne

Urządzenie usługi Azure IoT Edge:

* Maszynę wirtualną platformy Azure można użyć jako urządzenia usługi IoT Edge, wykonując kroki opisane w przewodniku Szybki Start dla [Linux](quickstart-linux.md).
* Moduły języka Python dla usługi IoT Edge nie są obsługiwane kontenerów Windows.

Zasoby w chmurze:

* Usługa [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) w warstwie Bezpłatna lub Standardowa na platformie Azure. 

Zasoby do programowania:

* [Program Visual Studio Code](https://code.visualstudio.com/) 
* [Narzędzia usługi Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) dla programu Visual Studio Code.
* [Rozszerzenie języka Python dla programu Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-python.python). 
* [Python](https://www.python.org/downloads/).
* Narzędzie [pip](https://pip.pypa.io/en/stable/installing/#installation) do instalacji pakietów języka Python (zazwyczaj instalowane wraz ze środowiskiem języka Python).
* [Program Docker CE](https://docs.docker.com/install/). 
  * Jeśli tworzysz na komputerze Windows, upewnij się, Docker jest [skonfigurowany do używania kontenerów systemu Linux](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers). 

>[!Note]
>Upewnij się, że folder `bin` znajduje się w ramach ścieżki dla Twojej platformy. Zazwyczaj jest to `~/.local/` w przypadku systemów UNIX i macOS lub `%APPDATA%\Python` w przypadku systemu Windows.

## <a name="create-a-container-registry"></a>Tworzenie rejestru kontenerów

W tym samouczku narzędzia usługi Azure IoT dla programu Visual Studio Code zostaną użyte do zbudowania modułu i utworzenia **obrazu kontenera** na podstawie plików. Następnie ten obraz zostanie wypchnięty do **rejestru**, w którym obrazy są przechowywane i zarządzane. Na koniec obraz zostanie wdrożony z rejestru w celu uruchomienia na urządzeniu usługi IoT Edge.  

Do przechowywania obrazów kontenerów możesz użyć dowolnego rejestru zgodnego z platformą Docker. Dwie popularne usługi rejestru Docker to [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) i [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags). W tym samouczku używana jest usługa Azure Container Registry. 

Jeśli nie masz jeszcze rejestru kontenerów, wykonaj następujące kroki, aby utworzyć nowy rejestr na platformie Azure:

1. W witrynie [Azure Portal](https://portal.azure.com) wybierz kolejno pozycje **Utwórz zasób** > **Kontenery** > **Container Registry**.

2. Podaj następujące wartości, aby utworzyć rejestr kontenerów:

   | Pole | Wartość | 
   | ----- | ----- |
   | Nazwa rejestru | Podaj unikatową nazwę. |
   | Subskrypcja | Wybierz subskrypcję z listy rozwijanej. |
   | Grupa zasobów | Zalecamy używanie tej samej grupy zasobów dla wszystkich zasobów testowych tworzonych podczas pracy z przewodnikami Szybki Start i samouczkami usługi IoT Edge. Na przykład **IoTEdgeResources**. |
   | Lokalizacja | Wybierz bliską lokalizację. |
   | Administrator | Ustaw na wartość **Włącz**. |
   | SKU | Wybierz pozycję **Podstawowa**. | 

5. Wybierz pozycję **Utwórz**.

6. Po utworzeniu kontenera rejestru przejdź do niego i wybierz pozycję **Klucze dostępu**. 

7. Skopiuj wartości w polach **Serwer logowania**, **Nazwa użytkownika** i **Hasło**. Użyjesz tych wartości w dalszej części samouczka, aby zapewnić dostęp do rejestru kontenerów. 

## <a name="create-an-iot-edge-module-project"></a>Tworzenie projektu modułu usługi IoT Edge
W następujących krokach przedstawiono sposób tworzenia modułu usługi IoT Edge w języku Python przy użyciu programu Visual Studio Code i narzędzi usługi Azure IoT.

### <a name="create-a-new-solution"></a>Tworzenie nowego rozwiązania

Użyj pakietu **cookiecutter** języka Python, aby utworzyć szablon rozwiązania języka Python, na podstawie którego można dalej tworzyć rozwiązanie. 

1. W programie Visual Studio Code wybierz kolejno pozycje **Widok** > **Terminal**, aby otworzyć zintegrowany terminal programu VS Code.

2. W terminalu wpisz następujące polecenie, aby zainstalować (lub zaktualizować) **cookiecutter**, którego używasz, aby utworzyć szablon rozwiązania usługi IoT Edge:

    ```cmd/sh
    pip install --upgrade --user cookiecutter
    ```
   >[!Note]
   >Upewnij się, że katalog, w którym zostanie zainstalowany pakiet cookiecutter, znajduje się w ramach zmiennej PATH w Twoim środowisku, aby możliwe było wywoływanie go z poziomu wiersza polecenia. Katalog ten jest częścią danych wyjściowych skryptu instalacji, na przykład `C:\Users\{user}\AppData\Roaming\Python\Python{version}\Scripts`.
   >
   >Uruchom ponownie program Visual Studio Code, aby zastosować zmiany w zmiennej PATH. 

3. Wybierz kolejno opcje **Widok** > **Paleta poleceń**, aby otworzyć paletę poleceń programu VS Code. 

4. W palecie poleceń wprowadź i uruchom polecenie **Azure: Sign in (Azure: zaloguj się)**, a następnie postępuj zgodnie z instrukcjami, aby zalogować się na swoim koncie platformy Azure. Jeśli już się zalogowano, można pominąć ten krok.

5. W palecie poleceń wprowadź i uruchom polecenie **Azure IoT Edge: New IoT Edge solution** (Azure IoT Edge: nowe rozwiązanie usługi IoT Edge). Postępuj zgodnie z monitami i podaj następujące informacje, aby utworzyć rozwiązanie:

   | Pole | Wartość |
   | ----- | ----- |
   | Wybierz folder | Wybierz lokalizację na maszynie deweloperskiej dla programu VS Code, aby utworzyć pliki rozwiązania. |
   | Podaj nazwę rozwiązania | Wprowadź opisową nazwę rozwiązania lub zaakceptuj nazwę domyślną **EdgeSolution**. |
   | Wybierz szablon modułu | Wybierz pozycję **Moduł Python**. |
   | Podaj nazwę modułu | Nazwij moduł **PythonModule**. |
   | Podaj repozytorium obrazów platformy Docker dla modułu | Repozytorium obrazów zawiera nazwę rejestru kontenerów oraz nazwę obrazu kontenera. Obraz kontenera jest wstępnie wypełniany na podstawie nazwy podanej w ostatnim kroku. Zastąp ciąg **localhost:5000** wartością serwera logowania z rejestru kontenerów platformy Azure. Serwer logowania możesz pobrać ze strony Przegląd rejestru kontenerów w witrynie Azure Portal. <br><br>Ostateczne repozytorium obrazów wygląda następująco: \<nazwa rejestru\>.azurecr.io/pythonmodule. |
 
   ![Udostępnianie repozytorium obrazów platformy Docker](./media/tutorial-python-module/repository.png)

W oknie programu VS Code zostanie załadowany obszar roboczy rozwiązania usługi IoT Edge. Obszar roboczy rozwiązania zawiera pięć składników najwyższego poziomu. Folder **modules** zawiera kod w języku Python dla modułu, a także pliki Dockerfile na potrzeby tworzenia modułu jako obrazu kontenera. W pliku **\.env** są przechowywane poświadczenia rejestru kontenerów. Plik **deployment.template.json** zawiera informacje, których środowisko uruchomieniowe usługi IoT Edge używa do wdrażania modułów na urządzeniu. Natomiast plik **deployment.debug.template.json** zawiera wersję modułów służącą do debugowania. Folder **\.vscode** i plik **\.gitignore** nie będą edytowane w tym samouczku.  

Jeśli podczas tworzenia własnego rozwiązania nie określisz rejestru kontenerów, ale zaakceptujesz wartość domyślną localhost:5000, nie będziesz mieć pliku \.env. 

<!--
   ![Python solution workspace](./media/tutorial-python-module/workspace.png)
-->

### <a name="add-your-registry-credentials"></a>Dodawanie poświadczeń rejestru

W pliku środowiska przechowywane są poświadczenia repozytorium kontenera, udostępniane środowisku uruchomieniowemu usługi IoT Edge. Środowisko uruchomieniowe wymaga tych poświadczeń do ściągnięcia prywatnych obrazów na urządzenie usługi IoT Edge. 

1. W eksploratorze programu VS Code otwórz plik **env**. 
2. Zaktualizuj pola, używając **nazwy użytkownika** i **hasła**, które zostały skopiowane z usługi Azure Container Registry. 
3. Zapisz plik env. 

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

7. Zapisz plik main.py.

8. W eksploratorze programu VS Code otwórz plik **deployment.template.json** w obszarze roboczym rozwiązania usługi IoT Edge. Ten plik informuje agenta usługi IoT Edge, które moduły mają zostać wdrożone (w tym przypadku **tempSensor** i **PythonModule**), i informuje centrum usługi IoT Edge o tym, jak kierować wiadomości między nimi. Rozszerzenie programu Visual Studio Code automatycznie wypełni większość informacji potrzebnych w szablonie wdrożenia, ale sprawdź, czy wszystko jest dokładne dla Twojego rozwiązania: 

   1. Domyślną platformą ustawioną na pasku stanu programu VS Code dla usługi IoT Edge jest platforma **amd64**, co oznacza, że dla modułu **PythonModule** ustawiono wersję obrazu Linux amd64. Zmień domyślną platformę na pasku stanu z **amd64** na **arm32v7**, jeśli taka jest architektura urządzenia usługi IoT Edge. 

      ![Aktualizowanie platformy obrazu modułu](./media/tutorial-python-module/image-platform.png)

   2. Sprawdź, czy szablon ma prawidłową nazwę modułu, a nie domyślną nazwę **SampleModule** zmienioną podczas tworzenia rozwiązania usługi IoT Edge.

   3. W sekcji **registryCredentials** przechowywane są poświadczenia rejestru platformy Docker, aby agent usługi IoT Edge mógł ściągnąć obraz modułu. Rzeczywiste pary nazw użytkownika i haseł są przechowywane w pliku env, który jest ignorowany przez usługę git. Jeśli jeszcze nie zostało to zrobione, dodaj swoje poświadczenia do pliku env.  

   4. Jeśli chcesz dowiedzieć się więcej na temat manifestów wdrożenia, zobacz artykuł [Learn how to deploy modules and establish routes in IoT Edge](module-composition.md) (Dowiedz się, jak wdrażać moduły i ustanawiać trasy w usłudze IoT Edge).

9. Dodaj bliźniaczą reprezentację modułu **PythonModule** do manifestu wdrożenia. Wstaw następującą zawartość do pliku JSON na końcu sekcji **moduleContent**, po bliźniaczej reprezentacji modułu **$edgeHub**: 

   ```json
       "PythonModule": {
           "properties.desired":{
               "TemperatureThreshold":25
           }
       }
   ```

   ![Dodawanie bliźniaczej reprezentacji modułu do szablonu wdrożenia](./media/tutorial-python-module/module-twin.png)

10. Zapisz plik deployment.template.json.

## <a name="build-and-push-your-solution"></a>Kompilowanie i wypychanie rozwiązania

W poprzedniej sekcji utworzono rozwiązanie usługi IoT Edge i dodano kod do modułu **PythonModule** w celu filtrowania komunikatów, w których zgłoszona temperatura maszyny jest poniżej akceptowalnego poziomu. Teraz należy skompilować to rozwiązanie jako obraz kontenera i wypchnąć go do rejestru kontenerów. 

1. Zaloguj się do platformy Docker, wprowadzając następujące polecenie w zintegrowanym terminalu programu Visual Studio Code. Następnie możesz wypchnąć obraz modułu do rejestru kontenerów platformy Azure: 
     
   ```csh/sh
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```
   Użyj nazwy użytkownika, hasła i serwera logowania skopiowanych z rejestru kontenerów platformy Azure w pierwszej sekcji. Możesz także pobrać te wartości z sekcji **Klucze dostępu** rejestru w witrynie Azure Portal.

   Może być wyświetlone ostrzeżenie o zabezpieczeniach zalecające użycie parametru--stdin hasła. Użycie tego parametru wykracza poza zakres tego artykułu, jednak zalecamy zastosowanie tego najlepszego rozwiązania. Aby uzyskać więcej informacji, zobacz [docker login](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) poleceń. 


2. W eksploratorze programu VS Code kliknij prawym przyciskiem myszy plik deployment.template.json i wybierz polecenie **Skompiluj i wypchnij rozwiązanie usługi IoT Edge**. 

Po wybraniu polecenia kompilowania rozwiązania w programie Visual Studio Code program najpierw pobiera informacje z szablonu wdrożenia i generuje plik deployment.json w nowym folderze **config**. Następnie uruchamia dwa polecenia w zintegrowanym terminalu: `docker build` i `docker push`. Te dwa polecenia kompilują kod, konteneryzują kod Python i wypychają go do rejestru kontenera określonego podczas inicjowania rozwiązania. 

Pełny adres obrazu kontenera możesz uzyskać za pomocą tagu w poleceniu `docker build` uruchamianym w zintegrowanym terminalu programu VS Code. Adres obrazu składa się z informacji z pliku module.json w formacie \<repozytorium\>:\<wersja\>-\<platforma\>. W tym samouczku powinien wyglądać następująco: registryname.azurecr.io/pythonmodule:0.0.1-amd64.

>[!TIP]
>Jeśli podczas próby skompilowania i wypchnięcia modułu pojawia się błąd, sprawdź następujące rzeczy:
>* Czy do platformy Docker w programie Visual Studio Code zalogowano się przy użyciu poświadczeń z rejestru kontenerów? Te poświadczenia są inne niż te, których używasz do logowania się w witrynie Azure Portal.
>* Czy używasz właściwego repozytorium kontenerów? Otwórz **modułów** > **PythonModule** > **module.json** i Znajdź **repozytorium** pola. Repozytorium obrazów powinno wyglądać tak: **\<nazwa_rejestru\>.azurecr.io/pythonmodule**. 
>* Czy kompilujesz kontenery tego samego typu, co działające na maszynie deweloperskiej? Domyślnie w programie Visual Studio Code są używane kontenery amd64 systemu Linux. Jeśli na maszynie deweloperskiej działają kontenery arm32v7 systemu Linux, zaktualizuj platformę na niebieskim pasku stanu w dolnej części okna programu VS Code, aby była zgodna z platformą kontenerów. Moduły języka Python nie obsługują kontenerów systemu Windows. 

## <a name="deploy-and-run-the-solution"></a>Wdrażanie i uruchamianie rozwiązania

W artykule Szybki start, który był używany do skonfigurowania urządzenia usługi IoT Edge, wdrożono moduł za pomocą witryny Azure Portal. Moduły można także wdrażać przy użyciu rozszerzenia Azure IoT Hub Toolkit (dawniej rozszerzenia Azure IoT Toolkit) dla programu Visual Studio Code. Masz już manifest wdrożenia przygotowany dla danego scenariusza — plik **deployment.json**. Teraz wystarczy wybrać urządzenie, które ma otrzymać wdrożenie.

1. W palecie poleceń programu VS Code uruchom polecenie **Azure IoT Hub: Select IoT Hub**  (Azure IoT Hub: wybierz centrum IoT Hub). 

2. Wybierz subskrypcję i centrum IoT Hub zawierające urządzenie usługi IoT Edge, które chcesz skonfigurować. 

3. W eksploratorze programu VS rozwiń sekcję **Urządzenia usługi Azure IoT Hub**. 

4. Kliknij prawym przyciskiem myszy nazwę urządzenia usługi IoT Edge, a następnie wybierz pozycję **Utwórz wdrożenie dla pojedynczego urządzenia**. 

   ![Tworzenie wdrożenia dla pojedynczego urządzenia](./media/tutorial-python-module/create-deployment.png)

5. Wybierz **deployment.amd64** lub **deployment.arm32v7** w pliku (w zależności od architektury docelowej) **config** folder, a następnie kliknij przycisk **wybierz Manifest wdrażania krawędzi**. Nie używaj pliku deployment.template.json. 

6. Kliknij przycisk Odśwież. Powinien zostać wyświetlony nowy moduł **PythonModule** uruchomiony wraz z modułami **TempSensor**, **$edgeAgent** i **$edgeHub**. 

## <a name="view-generated-data"></a>Wyświetlanie wygenerowanych danych

Gdy zastosujesz manifest wdrożenia na urządzeniu usługi IoT Edge, środowisko uruchomieniowe usługi IoT Edge na tym urządzeniu zbierze nowe informacje na temat wdrożenia i zacznie wykonywanie. Wszelkie moduły uruchomione na urządzeniu, których nie uwzględniono w manifeście wdrożenia, zostaną zatrzymane. Wszelkie moduły, których brakuje na urządzeniu, zostaną uruchomione. 

Możesz wyświetlić stan urządzenia usługi IoT Edge w sekcji **Azure IoT Hub Devices** (Urządzenia usługi Azure IoT Hub) w eksploratorze programu Visual Studio Code. Rozwiń szczegóły urządzenia, aby wyświetlić listę wdrożonych i uruchomionych modułów. 

Na urządzeniu usługi IoT Edge można zobaczyć stan moduły wdrożenia za pomocą polecenia `iotedge list`. Powinny zostać wyświetlone cztery moduły: dwa moduły środowiska uruchomieniowego usługi IoT Edge, moduł tempSensor i moduł niestandardowy utworzony w ramach tego samouczka. Uruchomienie wszystkich modułów może potrwać kilka minut, dlatego jeśli na początku wszystkie nie będą widoczne, uruchom polecenie ponownie. 

Aby wyświetlić komunikaty generowane przez któryś moduł, użyj polecenia `iotedge logs <module name>`. 

Komunikaty odbierane w centrum IoT można wyświetlać za pomocą programu Visual Studio Code. 

1. Aby monitorować dane, które docierają do centrum IoT Hub, wybierz przycisk wielokropka (**...**), a następnie wybierz pozycję **Rozpocznij monitorowanie komunikatów D2C**.
2. Aby monitorować komunikat D2C dla określonego urządzenia, kliknij prawym przyciskiem myszy to urządzenie na liście, a następnie wybierz opcję **Rozpocznij monitorowanie komunikatów D2C**.
3. Aby zatrzymać monitorowanie danych, uruchom polecenie **Azure IoT Hub: Stop monitoring D2C message** (Azure IoT Hub: zatrzymaj monitorowanie komunikatu D2C) w palecie poleceń. 
4. Aby wyświetlić lub zaktualizować bliźniaczą reprezentację modułu, kliknij prawym przyciskiem myszy moduł na liście, a następnie wybierz opcję **Edytuj bliźniaczą reprezentację modułu**. Aby zaktualizować bliźniaczą reprezentację modułu, zapisz jej plik JSON, kliknij prawym przyciskiem myszy obszar edytora i wybierz pozycję **Zaktualizuj bliźniaczą reprezentację modułu**.
5. Aby wyświetlić dzienniki platformy Docker, zainstaluj [rozszerzenie Docker extension for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker). Uruchomione moduły można znaleźć lokalnie w eksploratorze platformy Docker. W menu kontekstowym kliknij opcję **Pokaż dzienniki**, aby wyświetlić je w zintegrowanym terminalu. 

## <a name="clean-up-resources"></a>Oczyszczanie zasobów 

Jeśli zamierzasz przejść do kolejnego zalecanego artykułu, możesz zachować utworzone zasoby oraz konfiguracje i użyć ich ponownie. Możesz także nadal używać tego samego urządzenia usługi IoT Edge jako urządzenia testowego. 

W przeciwnym razie możesz usunąć konfigurację lokalną i zasoby platformy Azure utworzone podczas pracy z tym artykułem, aby uniknąć naliczania opłat. 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]


## <a name="next-steps"></a>Kolejne kroki

W tym samouczku został utworzony moduł usługi IoT Edge zawierający kod służący do filtrowania nieprzetworzonych danych wygenerowanych przez urządzenie usługi IoT Edge. Możesz teraz kontynuować pracę, korzystając z kolejnych samouczków, aby poznać inne metody, za pomocą których usługa Azure IoT Edge może ułatwiać przekształcanie danych w analizy biznesowe na urządzeniach brzegowych.

> [!div class="nextstepaction"]
> [Wdrażanie funkcji platformy Azure jako modułu](tutorial-deploy-function.md)
> [Wdrażanie usługi Azure Stream Analytics jako modułu](tutorial-deploy-stream-analytics.md)
