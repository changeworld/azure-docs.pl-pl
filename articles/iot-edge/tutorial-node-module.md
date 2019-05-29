---
title: Samouczek tworzenia modułów Node.js dla systemu Linux — usługi Azure IoT Edge | Dokumentacja firmy Microsoft
description: W tym samouczku pokazano, jak utworzyć moduł usługi IoT Edge za pomocą kodu Node.js i wdrożyć go na urządzeniu brzegowym
services: iot-edge
author: shizn
manager: philmea
ms.author: xshi
ms.date: 01/04/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc, seodec18
ms.openlocfilehash: 6c94ca3a82095736ef7d242987d1fbf66a825950
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/29/2019
ms.locfileid: "66306498"
---
# <a name="tutorial-develop-and-deploy-a-nodejs-iot-edge-module-for-linux-devices"></a>Samouczek: Tworzenie i wdrażanie modułu usługi IoT Edge dla środowiska Node.js dla urządzeń z systemem Linux

Użyj programu Visual Studio Code do tworzenia kodu w języku Node.js oraz wdrażania jej urządzeniem z systemem Linux z usługą Azure IoT Edge. 

Moduły usługi IoT Edge umożliwiają wdrożenie kodu implementującego logikę biznesową bezpośrednio na urządzeniach usługi IoT Edge. W tym samouczku przedstawiono sposób tworzenia i wdrażania modułu usługi IoT Edge, w którym są filtrowane dane czujnika. Użyjesz symulowanego urządzenia usługi IoT Edge utworzonego w ramach przewodnika Szybki start. Ten samouczek zawiera informacje na temat wykonywania następujących czynności:    

> [!div class="checklist"]
> * Tworzenie modułu usługi IoT Edge w środowisku Node.js przy użyciu programu Visual Studio Code
> * Tworzenie obrazu platformy Docker i publikowanie go w rejestrze przy użyciu programu Visual Studio Code i platformy Docker 
> * Wdrażanie modułu na urządzeniu usługi IoT Edge
> * Wyświetlanie wygenerowanych danych


Utworzony w tym samouczku moduł usługi IoT Edge filtruje dane temperatury generowane przez urządzenie. Komunikaty są wysyłane tylko wtedy, gdy temperatura przekroczy określony próg. Ten typ analizy brzegowej pomaga zmniejszyć ilość danych przekazywanych do chmury i w niej przechowywanych. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="solution-scope"></a>Zakres rozwiązania

Ten samouczek przedstawia sposób tworzenia modułu w **Node.js** przy użyciu **programu Visual Studio Code**oraz jak wdrożyć ją **urządzenia z systemem Linux**. W przypadku urządzeń Windows IoT Edge nie obsługuje modułów Node.js.

Skorzystaj z poniższej tabeli, aby poznać opcje tworzenia i wdrażania modułów Node.js: 

| Node.js | Visual Studio Code | Visual Studio 2017/2019 | 
| - | ------------------ | ------------------ |
| **AMD64 systemu Linux** | ![Używanie programu VS Code dla modułów Node.js w systemie Linux AMD64](./media/tutorial-c-module/green-check.png) |  |
| **ARM32 systemu Linux** | ![Używanie programu VS Code dla modułów Node.js w systemie Linux ARM32](./media/tutorial-c-module/green-check.png) |  |

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem tego samouczka, powinien wykonano za pomocą poprzedniego samouczka, aby skonfigurować środowisko programowania do tworzenia aplikacji kontenera systemu Linux: [Twórz moduły usługi IoT Edge dla urządzeń z systemem Linux](tutorial-develop-for-linux.md). Wykonując jedną z tych samouczków, musisz mieć następujące wymagania wstępne spełnione: 

* Usługa [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) w warstwie Bezpłatna lub Standardowa na platformie Azure.
* A [Linux urządzenia z usługą Azure IoT Edge](quickstart-linux.md)
* Rejestr kontenera, takiej jak [usługi Azure Container Registry](https://docs.microsoft.com/azure/container-registry/).
* [Visual Studio Code](https://code.visualstudio.com/) skonfigurowano [narzędzia IoT Azure](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
* [Docker CE](https://docs.docker.com/install/) skonfigurowane do uruchamiania kontenerów systemu Linux.

Aby opracować moduł usługi IoT Edge w języku Node.js, należy zainstalować następujące dodatkowe wymagania wstępne na komputerze deweloperskim: 

* [Node.js i npm](https://nodejs.org). Pakiet npm jest dystrybuowany wraz ze środowiskiem Node.js, co oznacza, że podczas pobierania środowiska Node.js program npm jest automatycznie instalowany na komputerze.

## <a name="create-a-module-project"></a>Utwórz projekt modułu
W następujących krokach przedstawiono sposób tworzenia modułu usługi IoT Edge w środowisku Node.js przy użyciu programu Visual Studio Code i narzędzi usługi Azure IoT.

### <a name="create-a-new-project"></a>Tworzenie nowego projektu

Użyj programu **npm**, aby utworzyć szablon rozwiązania Node.js, na podstawie którego można dalej tworzyć rozwiązanie. 

1. W programie Visual Studio Code wybierz kolejno pozycje **Widok** > **Zintegrowany terminal**, aby otworzyć zintegrowany terminal programu VS Code.

2. W zintegrowanym terminalu wprowadź następujące polecenie, aby zainstalować narzędzie **yeoman** oraz generator dla modułu Node.js usługi Azure IoT Edge: 

    ```cmd/sh
    npm install -g yo generator-azure-iot-edge-module
    ```

3. Wybierz kolejno opcje **Widok** > **Paleta poleceń**, aby otworzyć paletę poleceń programu VS Code. 

3. W palecie poleceń wpisz i uruchom polecenie **Azure: Sign in (Azure: zaloguj się)** , a następnie postępuj zgodnie z instrukcjami, aby zalogować się na swoim koncie platformy Azure. Jeśli już się zalogowano, można pominąć ten krok.

4. W palecie poleceń wpisz i uruchom polecenie **Azure IoT Edge: New IoT Edge solution** (Azure IoT Edge: nowe rozwiązanie usługi IoT Edge). Postępuj zgodnie z monitami wyświetlanymi na palecie poleceń, aby utworzyć rozwiązanie.

   | Pole | Wartość |
   | ----- | ----- |
   | Wybierz folder | Wybierz lokalizację na maszynie deweloperskiej dla programu VS Code, aby utworzyć pliki rozwiązania. |
   | Podaj nazwę rozwiązania | Wprowadź opisową nazwę rozwiązania lub zaakceptuj nazwę domyślną **EdgeSolution**. |
   | Wybierz szablon modułu | Wybierz moduł **Node.js Module**. |
   | Podaj nazwę modułu | Nazwij moduł **NodeModule**. |
   | Podaj repozytorium obrazów platformy Docker dla modułu | Repozytorium obrazów zawiera nazwę rejestru kontenerów oraz nazwę obrazu kontenera. Obraz kontenera jest wstępnie wypełniany na podstawie nazwy podanej w ostatnim kroku. Zastąp ciąg **localhost:5000** wartością serwera logowania z rejestru kontenerów platformy Azure. Serwer logowania możesz pobrać ze strony Przegląd rejestru kontenerów w witrynie Azure Portal. <br><br>Ostateczne repozytorium obrazów wygląda następująco: \<nazwa rejestru\>.azurecr.io/nodemodule. |
 
   ![Udostępnianie repozytorium obrazów platformy Docker](./media/tutorial-node-module/repository.png)


### <a name="add-your-registry-credentials"></a>Dodawanie poświadczeń rejestru

W pliku środowiska przechowywane są poświadczenia repozytorium kontenera, udostępniane środowisku uruchomieniowemu usługi IoT Edge. Środowisko uruchomieniowe wymaga tych poświadczeń do ściągnięcia prywatnych obrazów na urządzenie usługi IoT Edge. 

1. W eksploratorze programu VS Code otwórz plik **env**. 
2. Zaktualizuj pola, używając **nazwy użytkownika** i **hasła**, które zostały skopiowane z usługi Azure Container Registry. 
3. Zapisz ten plik. 

### <a name="select-your-target-architecture"></a>Wybierz swoje Architektura docelowa

Obecnie usługa Visual Studio Code można tworzyć modułów Node.js dla urządzeń z AMD64 systemu Linux i ARM32v7 systemu Linux. Musisz wybrać architektury objęci za pomocą każdego rozwiązania, ponieważ skompilowane i uruchom w różny sposób dla każdego typu architektury kontenera. Wartość domyślna to AMD64 systemu Linux. 

1. Otwórz paletę poleceń i wyszukaj **usługi Azure IoT Edge: Ustaw domyślne platformę docelową dla nowoczesne rozwiązanie**, lub wybierz ikonę skrótu prowadzącą w pasku bocznym, w dolnej części okna. 

2. W palecie poleceń Wybierz architektury docelowej z listy opcji. W tym samouczku używamy maszynę wirtualną Ubuntu jako urządzenie usługi IoT Edge, dzięki czemu będzie Zachowaj ustawienie domyślne **amd64**.

### <a name="update-the-module-with-custom-code"></a>Aktualizowanie modułu przy użyciu kodu niestandardowego

Każdy szablon zawiera przykładowy kod, który przyjmuje symulowane dane czujnika z modułu **tempSensor** i kieruje je do centrum IoT Hub. W tej sekcji dodasz kod, aby umożliwić modułowi NodeModule analizowanie komunikatów przed ich wysłaniem. 

1. W eksploratorze programu VS Code otwórz kolejno pozycje **modules** > **NodeModule** > **app.js**.

2. Dodaj zmienną „temperature_threshold” poniżej wymaganych modułów węzła. Zmienna „temperature_threshold” określa wartość zmierzonej temperatury, której przekroczenie spowoduje wysłanie danych do usługi IoT Hub.

    ```javascript
    var temperatureThreshold = 25;
    ```

3. Zastąp całą funkcję `PipeMessage` funkcją `FilterMessage`.
    
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

4. Zastąp nazwę funkcji `pipeMessage` nazwą `filterMessage` w funkcji `client.on()`.

    ```javascript
    client.on('inputMessage', function (inputName, msg) {
        filterMessage(client, inputName, msg);
        });
    ```

5. Skopiuj poniższy fragment kodu do wywołania funkcji `client.open()`, po funkcji `client.on()` wewnątrz instrukcji `else`. Ta funkcja jest wywoływana w przypadku zaktualizowania odpowiednich właściwości.

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

6. Zapisz plik app.js.

7. W eksploratorze programu VS Code otwórz plik **deployment.template.json** w obszarze roboczym rozwiązania usługi IoT Edge.

8. Dodaj bliźniaczą reprezentację modułu NodeModule do manifestu wdrożenia. Wstaw następującą zawartość do pliku JSON na końcu sekcji `moduleContent`, po bliźniaczej reprezentacji modułu `$edgeHub`: 

   ```json
     "NodeModule": {
         "properties.desired":{
             "TemperatureThreshold":25
         }
     }
   ```

   ![Dodawanie bliźniaczej reprezentacji modułu do szablonu wdrożenia](./media/tutorial-node-module/module-twin.png)

9. Zapisz plik deployment.template.json.


## <a name="build-and-push-your-module"></a>Zbuduj i Wypchnij modułu

W poprzedniej sekcji utworzyliśmy rozwiązanie IoT Edge i dodać kod do NodeModule, która będzie filtrować wiadomości, gdzie temperatury zgłoszonych maszyny wykracza poza dopuszczalne limity. Teraz należy skompilować to rozwiązanie jako obraz kontenera i wypchnąć go do rejestru kontenerów.

1. Otwórz zintegrowany terminal programu VS Code, wybierając pozycję **View (Widok)**  > **Terminal**.

1. Zaloguj się do platformy Docker, wprowadzając następujące polecenie w terminalu. Zaloguj się przy użyciu nazwy użytkownika, hasło i serwer logowania z usługi Azure container registry. Możesz pobrać te wartości z **klucze dostępu** sekcji rejestru w witrynie Azure portal.
     
   ```bash
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Może pojawić się ostrzeżenie o zabezpieczeniach zalecające użycie `--password-stdin`. Gdy na tym najlepszym rozwiązaniem jest zalecane na potrzeby scenariuszy produkcyjnych, znajduje się poza zakres tego samouczka. Aby uzyskać więcej informacji, zobacz [docker login](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) odwołania.

2. W eksploratorze programu VS Code kliknij prawym przyciskiem myszy plik **deployment.template.json** i wybierz polecenie **Skompiluj i wypchnij rozwiązanie usługi IoT Edge**.

   Polecenie kompilacji i wypychania uruchamia trzy operacje. Najpierw tworzy nowy folder w rozwiązaniu o nazwie **config** przechowuje pliki manifestu, wbudowanego się z informacjami o szablon wdrożenia i inne rozwiązania pełne wdrożenie. Po drugie, uruchamia `docker build` do utworzenia obrazu kontenera, w oparciu o plik dockerfile odpowiednie dla architektury docelowej. Następnie uruchamia `docker push` wypychanie repozytorium obrazów do rejestru kontenerów.

## <a name="deploy-modules-to-device"></a>Wdrażanie modułów na urządzeniu

Do wdrożenia projektu modułu do urządzenia usługi IoT Edge przy użyciu Eksploratora Visual Studio Code i rozszerzenia narzędzia IoT platformy Azure. Masz już manifest wdrożenia, jest przygotowane do scenariusza **deployment.json** pliku w folderze konfiguracji. Teraz wystarczy wybrać urządzenie, które ma otrzymać wdrożenie.

Upewnij się, że urządzenia usługi IoT Edge jest uruchomiona.

1. W Eksploratorze programu Visual Studio Code rozwiń **Azure IoT Hub Devices** sekcję, aby wyświetlić listę urządzeń IoT.

2. Kliknij prawym przyciskiem myszy nazwę urządzenia usługi IoT Edge, a następnie wybierz pozycję **Utwórz wdrożenie dla pojedynczego urządzenia**.

3. Wybierz plik **deployment.json** w folderze **config**, a następnie kliknij pozycję **Wybierz manifest wdrożenia usługi Edge**. Nie używaj pliku deployment.template.json.

4. Kliknij przycisk Odśwież. Powinien zostać wyświetlony nowy moduł **NodeModule** uruchomiony wraz z modułami **TempSensor**, **$edgeAgent** i **$edgeHub**.

## <a name="view-generated-data"></a>Wyświetlanie wygenerowanych danych

Gdy zastosujesz manifest wdrożenia na urządzeniu usługi IoT Edge, środowisko uruchomieniowe usługi IoT Edge na tym urządzeniu zbierze nowe informacje na temat wdrożenia i zacznie wykonywanie. Wszelkie moduły uruchomione na urządzeniu, których nie uwzględniono w manifeście wdrożenia, zostaną zatrzymane. Wszelkie moduły, których brakuje na urządzeniu, zostaną uruchomione.

Możesz wyświetlić stan urządzenia usługi IoT Edge w sekcji **Azure IoT Hub Devices** (Urządzenia usługi Azure IoT Hub) w eksploratorze programu Visual Studio Code. Rozwiń szczegóły urządzenia, aby wyświetlić listę wdrożonych i uruchomionych modułów.

1. W Eksploratorze programu Visual Studio Code kliknij prawym przyciskiem myszy nazwę urządzenia usługi IoT Edge, a następnie wybierz **rozpocząć monitorowanie wbudowany zdarzeń punkt końcowy**.

2. Wyświetl wiadomości otrzymywanych z usługi IoT Hub. Może potrwać trochę wiadomości zostanie dostarczona, ponieważ urządzenia usługi IoT Edge do odbierania jego nowego wdrożenia i uruchamiania wszystkich modułów. Następnie zmiany wprowadzone do kodu NodeModule poczekaj, aż temperatury maszyny osiągnie 25 stopni przed wysłaniem wiadomości. Typ komunikatu dodaje także **alertu** na wszystkie komunikaty, w których progu temperatury. 

## <a name="edit-the-module-twin"></a>Edytuj bliźniaczą reprezentację modułu

Użyliśmy NodeModule bliźniaczą reprezentację modułu w pliku manifestu wdrożenia, aby ustawić próg temperaturę w stopniach 25. Bliźniacza reprezentacja modułu można użyć w celu zmiany funkcji bez konieczności aktualizowania kodu modułu.

1. W programie Visual Studio Code rozwiń szczegóły w obszarze urządzenia usługi IoT Edge, aby wyświetlić uruchomione moduły. 

2. Kliknij prawym przyciskiem myszy **NodeModule** i wybierz **bliźniaczą reprezentację modułu edycji**. 

3. Znajdź **TemperatureThreshold** w odpowiednich właściwości. Zmień jej wartość temperatury nowe 5 stopni do 10 stopni wyższa niż najnowszy zgłoszonych temperatury. 

4. Zapisz plik bliźniaczej reprezentacji modułu.

5. Kliknij prawym przyciskiem myszy w dowolnym miejscu w bliźniaczej reprezentacji modułu do edycji okienko, a następnie wybierz **bliźniaczą reprezentację modułu aktualizacji**. 

6. Monitorowanie przychodzących komunikatów przesyłanych z chmury do urządzenia. Powinien zostać wyświetlony wiadomości, Zatrzymaj, aż do osiągnięcia progu temperatury nowe. 

## <a name="clean-up-resources"></a>Oczyszczanie zasobów 

Jeśli zamierzasz przejść do kolejnego zalecanego artykułu, możesz zachować utworzone zasoby oraz konfiguracje i użyć ich ponownie. Możesz także nadal używać tego samego urządzenia usługi IoT Edge jako urządzenia testowego. 

W przeciwnym razie możesz usunąć konfigurację lokalną i zasoby platformy Azure utworzone podczas pracy z tym artykułem, aby uniknąć naliczania opłat. 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku został utworzony moduł usługi IoT Edge zawierający kod służący do filtrowania nieprzetworzonych danych wygenerowanych przez urządzenie usługi IoT Edge. Gdy wszystko będzie gotowe do tworzenia własnych modułach, możesz dowiedzieć się więcej [tworzenia własnych modułów usługi IoT Edge](module-development.md) lub jak [opracowanie modułów przy użyciu programu Visual Studio Code](how-to-vs-code-develop-module.md). Można przejść do następnego samouczki, aby dowiedzieć się, jak usługa Azure IoT Edge mogą pomóc Ci wdrażanie usług w chmurze platformy Azure do przetwarzania i analizowania danych na urządzeniach brzegowych.

> [!div class="nextstepaction"]
> [Funkcje](tutorial-deploy-function.md)
> [Stream Analytics](tutorial-deploy-stream-analytics.md)
> [uczenia maszynowego](tutorial-deploy-machine-learning.md)
> [Custom Vision Service](tutorial-deploy-custom-vision.md)
