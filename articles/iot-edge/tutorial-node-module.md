---
title: Samouczek dotyczący tworzenia niestandardowego modułu Node.js — Azure IoT Edge | Microsoft Docs
description: W tym samouczku pokazano, jak utworzyć moduł usługi IoT Edge za pomocą kodu Node.js i wdrożyć go na urządzeniu brzegowym
services: iot-edge
author: shizn
manager: philmea
ms.author: xshi
ms.date: 01/04/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc, seodec18
ms.openlocfilehash: 3b79c75b9846a4f8966a113c6e06fabc25bcf011
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59792611"
---
# <a name="tutorial-develop-and-deploy-a-nodejs-iot-edge-module-to-your-simulated-device"></a>Samouczek: Opracowywanie modułu usługi IoT Edge w środowisku Node.js i wdrażanie go na urządzeniu symulowanym

Moduły usługi IoT Edge umożliwiają wdrożenie kodu implementującego logikę biznesową bezpośrednio na urządzeniach usługi IoT Edge. W tym samouczku przedstawiono sposób tworzenia i wdrażania modułu usługi IoT Edge, w którym są filtrowane dane czujnika. Użyjesz symulowanego urządzenia usługi IoT Edge utworzonego w ramach przewodnika Szybki start. Ten samouczek zawiera informacje na temat wykonywania następujących czynności:    

> [!div class="checklist"]
> * Tworzenie modułu usługi IoT Edge w środowisku Node.js przy użyciu programu Visual Studio Code
> * Tworzenie obrazu platformy Docker i publikowanie go w rejestrze przy użyciu programu Visual Studio Code i platformy Docker 
> * Wdrażanie modułu na urządzeniu usługi IoT Edge
> * Wyświetlanie wygenerowanych danych


Utworzony w tym samouczku moduł usługi IoT Edge filtruje dane temperatury generowane przez urządzenie. Komunikaty są wysyłane tylko wtedy, gdy temperatura przekroczy określony próg. Ten typ analizy brzegowej pomaga zmniejszyć ilość danych przekazywanych do chmury i w niej przechowywanych. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Urządzenie usługi Azure IoT Edge:

* Jako urządzenia brzegowego możesz użyć maszyny deweloperskiej albo maszyny wirtualnej, postępując zgodnie z instrukcjami w przewodniku Szybki start dla urządzeń z systemem [Linux](quickstart-linux.md).
* Moduły node.js dla usługi IoT Edge obsługuje kontenery Windows. 

Zasoby w chmurze:

* Usługa [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) w warstwie Bezpłatna lub Standardowa na platformie Azure. 

Zasoby do programowania:

* [Program Visual Studio Code](https://code.visualstudio.com/) 
* [Narzędzia usługi Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) dla programu Visual Studio Code. 
* [Program Docker CE](https://docs.docker.com/engine/installation/). 
   * Jeśli programujesz na urządzeniu z systemem Windows, upewnij się, że platforma Docker jest [skonfigurowana do używania kontenerów systemu Linux](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers). 
* [Node.js i npm](https://nodejs.org). Pakiet npm jest dystrybuowany wraz ze środowiskiem Node.js, co oznacza, że podczas pobierania środowiska Node.js program npm jest automatycznie instalowany na komputerze.

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
W następujących krokach przedstawiono sposób tworzenia modułu usługi IoT Edge w środowisku Node.js przy użyciu programu Visual Studio Code i narzędzi usługi Azure IoT.

### <a name="create-a-new-solution"></a>Tworzenie nowego rozwiązania

Użyj programu **npm**, aby utworzyć szablon rozwiązania Node.js, na podstawie którego można dalej tworzyć rozwiązanie. 

1. W programie Visual Studio Code wybierz kolejno pozycje **Widok** > **Zintegrowany terminal**, aby otworzyć zintegrowany terminal programu VS Code.

2. W zintegrowanym terminalu wprowadź następujące polecenie, aby zainstalować narzędzie **yeoman** oraz generator dla modułu Node.js usługi Azure IoT Edge: 

    ```cmd/sh
    npm install -g yo generator-azure-iot-edge-module
    ```

3. Wybierz kolejno opcje **Widok** > **Paleta poleceń**, aby otworzyć paletę poleceń programu VS Code. 

3. W palecie poleceń wpisz i uruchom polecenie **Azure: Sign in (Azure: zaloguj się)**, a następnie postępuj zgodnie z instrukcjami, aby zalogować się na swoim koncie platformy Azure. Jeśli już się zalogowano, można pominąć ten krok.

4. W palecie poleceń wpisz i uruchom polecenie **Azure IoT Edge: New IoT Edge solution** (Azure IoT Edge: nowe rozwiązanie usługi IoT Edge). Postępuj zgodnie z monitami wyświetlanymi na palecie poleceń, aby utworzyć rozwiązanie.

   | Pole | Wartość |
   | ----- | ----- |
   | Wybierz folder | Wybierz lokalizację na maszynie deweloperskiej dla programu VS Code, aby utworzyć pliki rozwiązania. |
   | Podaj nazwę rozwiązania | Wprowadź opisową nazwę rozwiązania lub zaakceptuj nazwę domyślną **EdgeSolution**. |
   | Wybierz szablon modułu | Wybierz moduł **Node.js Module**. |
   | Podaj nazwę modułu | Nazwij moduł **NodeModule**. |
   | Podaj repozytorium obrazów platformy Docker dla modułu | Repozytorium obrazów zawiera nazwę rejestru kontenerów oraz nazwę obrazu kontenera. Obraz kontenera jest wstępnie wypełniany na podstawie nazwy podanej w ostatnim kroku. Zastąp ciąg **localhost:5000** wartością serwera logowania z rejestru kontenerów platformy Azure. Serwer logowania możesz pobrać ze strony Przegląd rejestru kontenerów w witrynie Azure Portal. <br><br>Ostateczne repozytorium obrazów wygląda następująco: \<nazwa rejestru\>.azurecr.io/nodemodule. |
 
   ![Udostępnianie repozytorium obrazów platformy Docker](./media/tutorial-node-module/repository.png)

W oknie programu VS Code zostanie załadowany obszar roboczy rozwiązania usługi IoT Edge. Obszar roboczy rozwiązania zawiera pięć składników najwyższego poziomu. Folder **modules** zawiera kod w języku Node.js dla modułu, a także pliki Dockerfile na potrzeby tworzenia modułu jako obrazu kontenera. W pliku **\.env** są przechowywane poświadczenia rejestru kontenerów. **Deployment.template.json** plik zawiera informacje, która używa środowiska uruchomieniowego usługi IoT Edge, aby wdrożyć moduły na urządzeniu i **deployment.debug.template.json** plik zawiera wersję debugowania moduły. Folder **\.vscode** i plik **\.gitignore** nie będą edytowane w tym samouczku. 

Jeśli podczas tworzenia własnego rozwiązania nie określisz rejestru kontenerów, ale zaakceptujesz wartość domyślną localhost:5000, nie będziesz mieć pliku \.env. 

<!--
   ![Node.js solution workspace](./media/tutorial-node-module/workspace.png)
-->

### <a name="add-your-registry-credentials"></a>Dodawanie poświadczeń rejestru

W pliku środowiska przechowywane są poświadczenia repozytorium kontenera, udostępniane środowisku uruchomieniowemu usługi IoT Edge. Środowisko uruchomieniowe wymaga tych poświadczeń do ściągnięcia prywatnych obrazów na urządzenie usługi IoT Edge. 

1. W eksploratorze programu VS Code otwórz plik **env**. 
2. Zaktualizuj pola, używając **nazwy użytkownika** i **hasła**, które zostały skopiowane z usługi Azure Container Registry. 
3. Zapisz ten plik. 

### <a name="update-the-module-with-custom-code"></a>Aktualizowanie modułu przy użyciu kodu niestandardowego

Każdy szablon zawiera przykładowy kod, który przyjmuje symulowane dane czujnika z modułu **tempSensor** i kieruje je do centrum IoT Hub. W tej sekcji dodasz kod, aby umożliwić modułowi NodeModule analizowanie komunikatów przed ich wysłaniem. 

1. W eksploratorze programu VS Code otwórz kolejno pozycje **modules** > **NodeModule** > **app.js**.

5. Dodaj zmienną „temperature_threshold” poniżej wymaganych modułów węzła. Zmienna „temperature_threshold” określa wartość zmierzonej temperatury, której przekroczenie spowoduje wysłanie danych do usługi IoT Hub.

    ```javascript
    var temperatureThreshold = 25;
    ```

6. Zastąp całą funkcję `PipeMessage` funkcją `FilterMessage`.
    
    ```javascript
    // This function filters out messages that report temperatures below the temperature threshold.
    // It also adds the MessageType property to the message with the value set to Alert.
    function filterMessage(client, inputName, msg) {
        client.complete(msg, printResultFor('Receiving message'));
        if (inputName === 'input1') {
            var message = msg.getBytes().toString('utf8');
            var messageBody = JSON.parse(message);
            if (messageBody && messageBody.machine && messageBody.machine.temperature && messageBody.machine.temperature > temperatureThreshold) {
                console.log(`Machine temperature ${messageBody.machine.temperature} exceeds threshold ${temperatureThreshold}`);
                var outputMsg = new Message(message);
                outputMsg.properties.add('MessageType', 'Alert');
                client.sendOutputEvent('output1', outputMsg, printResultFor('Sending received message'));
            }
        }
    }

    ```

7. Zastąp nazwę funkcji `pipeMessage` nazwą `filterMessage` w funkcji `client.on()`.

    ```javascript
    client.on('inputMessage', function (inputName, msg) {
        filterMessage(client, inputName, msg);
        });
    ```

8. Skopiuj poniższy fragment kodu do wywołania funkcji `client.open()`, po funkcji `client.on()` wewnątrz instrukcji `else`. Ta funkcja jest wywoływana w przypadku zaktualizowania odpowiednich właściwości.

    ```javascript
    client.getTwin(function (err, twin) {
        if (err) {
            console.error('Error getting twin: ' + err.message);
        } else {
            twin.on('properties.desired', function(delta) {
                if (delta.TemperatureThreshold) {
                    temperatureThreshold = delta.TemperatureThreshold;
                }
            });
        }
    });
    ```

9. Zapisz plik app.js.

10. W eksploratorze programu VS Code otwórz plik **deployment.template.json** w obszarze roboczym rozwiązania usługi IoT Edge. Ten plik informuje agenta usługi IoT Edge, które moduły mają zostać wdrożone (w tym przypadku **tempSensor** i **NodeModule**), i informuje centrum usługi IoT Edge o tym, jak kierować wiadomości między nimi. Rozszerzenie programu Visual Studio Code automatycznie wypełni większość informacji potrzebnych w szablonie wdrożenia, ale sprawdź, czy wszystko jest dokładne dla Twojego rozwiązania: 

   1. Domyślną platformą ustawioną na pasku stanu programu VS Code dla usługi IoT Edge jest platforma **amd64**, co oznacza, że dla modułu **NodeModule** ustawiono wersję obrazu Linux amd64. Zmień domyślną platformę na pasku stanu z **amd64** na **arm32v7**, jeśli taka jest architektura urządzenia usługi IoT Edge. 

      ![Aktualizowanie platformy obrazu modułu](./media/tutorial-node-module/image-platform.png)

   2. Sprawdź, czy szablon ma prawidłową nazwę modułu, a nie domyślną nazwę **SampleModule** zmienioną podczas tworzenia rozwiązania usługi IoT Edge.

   3. W sekcji **registryCredentials** przechowywane są poświadczenia rejestru platformy Docker, aby agent usługi IoT Edge mógł ściągnąć obraz modułu. Rzeczywiste pary nazw użytkownika i haseł są przechowywane w pliku env, który jest ignorowany przez usługę git. Jeśli jeszcze nie zostało to zrobione, dodaj swoje poświadczenia do pliku env.  

   4. Jeśli chcesz dowiedzieć się więcej na temat manifestów wdrożenia, zobacz artykuł [Learn how to deploy modules and establish routes in IoT Edge](module-composition.md) (Dowiedz się, jak wdrażać moduły i ustanawiać trasy w usłudze IoT Edge).

11. Dodaj bliźniaczą reprezentację modułu NodeModule do manifestu wdrożenia. Wstaw następującą zawartość do pliku JSON na końcu sekcji `moduleContent`, po bliźniaczej reprezentacji modułu `$edgeHub`: 

   ```json
     "NodeModule": {
         "properties.desired":{
             "TemperatureThreshold":25
         }
     }
   ```

   ![Dodawanie bliźniaczej reprezentacji modułu do szablonu wdrożenia](./media/tutorial-node-module/module-twin.png)

12. Zapisz plik deployment.template.json.


## <a name="build-your-iot-edge-solution"></a>Kompilowanie rozwiązania usługi IoT Edge

W poprzedniej sekcji utworzono rozwiązanie usługi IoT Edge i dodano kod do modułu NodeModule, filtrującego komunikaty, w których zgłoszona temperatura maszyny jest poniżej akceptowalnego poziomu. Teraz należy skompilować to rozwiązanie jako obraz kontenera i wypchnąć go do rejestru kontenerów. 

1. Zaloguj się w aplikacji Docker, wprowadzając następujące polecenie w zintegrowanym terminalu programu Visual Studio Code, aby móc wypchnąć obraz modułu do usługi ACR: 
     
   ```csh/sh
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```
   Użyj nazwy użytkownika, hasła i serwera logowania skopiowanych z usługi Azure Container Registry w sekcji pierwszej. Możesz też pobrać je ponownie z sekcji **Klucze dostępu** rejestru w witrynie Azure Portal.

2. W eksploratorze programu VS Code kliknij prawym przyciskiem myszy plik **deployment.template.json** i wybierz polecenie **Skompiluj i wypchnij rozwiązanie usługi IoT Edge**. 

Po wybraniu polecenia kompilowania rozwiązania w programie Visual Studio Code program najpierw pobiera informacje z szablonu wdrożenia i generuje plik `deployment.json` w nowym folderze **config**. Następnie uruchamia dwa polecenia w zintegrowanym terminalu: `docker build` i `docker push`. Te dwa polecenia kompilacji kodu, konteneryzowanie kod w języku Node.js i Wypchnij go do rejestru kontenerów, który określiłeś, po zainicjowaniu rozwiązania. 

Pełny adres obrazu kontenera możesz uzyskać za pomocą tagu w poleceniu `docker build` uruchamianym w zintegrowanym terminalu programu VS Code. Adres obrazu składa się z informacji z pliku `module.json` w formacie **\<repozytorium\>:\<wersja\>-\<platforma\>**. W tym samouczku powinien wyglądać następująco: **registryname.azurecr.io/nodemodule:0.0.1-amd64**.

>[!TIP]
>Jeśli podczas próby skompilowania i wypchnięcia modułu pojawia się błąd, sprawdź następujące rzeczy:
>* Czy do platformy Docker w programie Visual Studio Code zalogowano się przy użyciu poświadczeń z rejestru kontenerów? Te poświadczenia są inne niż te, których używasz do logowania się w witrynie Azure Portal.
>* Czy używasz właściwego repozytorium kontenerów? Otwórz **modułów** > **nodemodule** > **module.json** i Znajdź **repozytorium** pola. Repozytorium obrazów powinno wyglądać tak: **\<nazwa_rejestru\>.azurecr.io/nodemodule**. 
>* Czy kompilujesz kontenery tego samego typu, co działające na maszynie deweloperskiej? Domyślnie w programie Visual Studio Code są używane kontenery amd64 systemu Linux. Jeśli na maszynie deweloperskiej działają kontenery arm32v7 systemu Linux, zaktualizuj platformę na niebieskim pasku stanu w dolnej części okna programu VS Code, aby była zgodna z platformą kontenerów.
>* Moduły node.js dla usługi IoT Edge obsługuje kontenery Windows.

## <a name="deploy-and-run-the-solution"></a>Wdrażanie i uruchamianie rozwiązania

W artykule Szybki start, który był używany do skonfigurowania urządzenia usługi IoT Edge, wdrożono moduł za pomocą witryny Azure Portal. Moduły można także wdrażać przy użyciu rozszerzenia Azure IoT Hub Toolkit (dawniej rozszerzenia Azure IoT Toolkit) dla programu Visual Studio Code. Masz już manifest wdrożenia przygotowany dla danego scenariusza — plik **deployment.json**. Teraz wystarczy wybrać urządzenie, które ma otrzymać wdrożenie.

1. W palecie poleceń programu VS Code uruchom polecenie **Azure IoT Hub: Select IoT Hub**  (Azure IoT Hub: wybierz centrum IoT Hub). 

2. Wybierz subskrypcję i centrum IoT Hub zawierające urządzenie usługi IoT Edge, które chcesz skonfigurować. 

3. W eksploratorze programu VS rozwiń sekcję **Urządzenia usługi Azure IoT Hub**. 

4. Kliknij prawym przyciskiem myszy nazwę urządzenia usługi IoT Edge, a następnie wybierz pozycję **Utwórz wdrożenie dla pojedynczego urządzenia**. 

   ![Tworzenie wdrożenia dla pojedynczego urządzenia](./media/tutorial-node-module/create-deployment.png)

5. Wybierz plik **deployment.json** w folderze **config**, a następnie kliknij pozycję **Wybierz manifest wdrożenia usługi Edge**. Nie używaj pliku deployment.template.json. 

6. Kliknij przycisk Odśwież. Powinien zostać wyświetlony nowy moduł **NodeModule** uruchomiony wraz z modułami **TempSensor**, **$edgeAgent** i **$edgeHub**. 


## <a name="view-generated-data"></a>Wyświetlanie wygenerowanych danych

Gdy zastosujesz manifest wdrożenia na urządzeniu usługi IoT Edge, środowisko uruchomieniowe usługi IoT Edge na tym urządzeniu zbierze nowe informacje na temat wdrożenia i zacznie wykonywanie. Wszelkie moduły uruchomione na urządzeniu, których nie uwzględniono w manifeście wdrożenia, zostaną zatrzymane. Wszelkie moduły, których brakuje na urządzeniu, zostaną uruchomione. 

Możesz wyświetlić stan urządzenia usługi IoT Edge w sekcji **Azure IoT Hub Devices** (Urządzenia usługi Azure IoT Hub) w eksploratorze programu Visual Studio Code. Rozwiń szczegóły urządzenia, aby wyświetlić listę wdrożonych i uruchomionych modułów. 

Możesz również wyświetlić stan modułów wdrożenia na samym urządzeniu usługi IoT Edge, używając polecenia `iotedge list`. Powinny zostać wyświetlone cztery moduły: dwa moduły środowiska uruchomieniowego usługi IoT Edge, moduł tempSensor i moduł niestandardowy utworzony w ramach tego samouczka. Uruchomienie wszystkich modułów może potrwać kilka minut, dlatego jeśli na początku wszystkie nie będą widoczne, uruchom polecenie ponownie. 

Aby wyświetlić komunikaty generowane przez któryś moduł, użyj polecenia `iotedge logs <module name>`. 

Komunikaty odbierane w centrum IoT można wyświetlać za pomocą programu Visual Studio Code. 

1. Aby monitorować dane przychodzące do centrum IoT Hub, kliknij przycisk **...** i wybierz opcję **Rozpocznij monitorowanie komunikatów D2C**.
2. Aby monitorować komunikat D2C dla określonego urządzenia, kliknij prawym przyciskiem myszy to urządzenie na liście, a następnie wybierz opcję **Rozpocznij monitorowanie komunikatów D2C**.
3. Aby zatrzymać monitorowanie danych, uruchom polecenie **Azure IoT Hub: Stop monitoring D2C message** (Azure IoT Hub: zatrzymaj monitorowanie komunikatu D2C) w palecie poleceń. 
4. Aby wyświetlić lub zaktualizować bliźniaczą reprezentację modułu, kliknij prawym przyciskiem myszy moduł na liście, a następnie wybierz opcję **Edytuj bliźniaczą reprezentację modułu**. Aby zaktualizować bliźniaczą reprezentację modułu, zapisz plik JSON, kliknij prawym przyciskiem myszy obszar edycji i wybierz opcję **Zaktualizuj bliźniaczą reprezentację modułu**.
5. Aby wyświetlić dzienniki platformy Docker, możesz zainstalować rozszerzenie [Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) dla narzędzia VS Code i znaleźć uruchomione moduły lokalnie w eksploratorze platformy Docker. W menu kontekstowym kliknij opcję **Pokaż dzienniki**, aby wyświetlić je w zintegrowanym terminalu. 

## <a name="clean-up-resources"></a>Oczyszczanie zasobów 

Jeśli zamierzasz przejść do kolejnego zalecanego artykułu, możesz zachować utworzone zasoby oraz konfiguracje i użyć ich ponownie. Możesz także nadal używać tego samego urządzenia usługi IoT Edge jako urządzenia testowego. 

W przeciwnym razie możesz usunąć konfigurację lokalną i zasoby platformy Azure utworzone podczas pracy z tym artykułem, aby uniknąć naliczania opłat. 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

[!INCLUDE [iot-edge-clean-up-local-resources](../../includes/iot-edge-clean-up-local-resources.md)]


## <a name="next-steps"></a>Kolejne kroki

W tym samouczku został utworzony moduł usługi IoT Edge zawierający kod służący do filtrowania nieprzetworzonych danych wygenerowanych przez urządzenie usługi IoT Edge. Możesz teraz kontynuować pracę, korzystając z dowolnego z następujących samouczków, aby dowiedzieć się o innych metodach, za pomocą których usługa Azure IoT Edge może ułatwiać przekształcanie danych w analizy biznesowe na urządzeniach brzegowych.

> [!div class="nextstepaction"]
> [Wdrażanie funkcji platformy Azure jako modułu](tutorial-deploy-function.md)
> [Wdrażanie usługi Azure Stream Analytics jako modułu](tutorial-deploy-stream-analytics.md)

