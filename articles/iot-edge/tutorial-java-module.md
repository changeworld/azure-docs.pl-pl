---
title: Samouczek niestandardowego modułu Java — Azure IoT Edge | Dokumentacja firmy Microsoft
description: W tym samouczku pokazano, jak utworzyć moduł usługi IoT Edge za pomocą kodu języka Java i wdrożyć go na urządzeniu brzegowym.
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/04/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc, seodec18
ms.openlocfilehash: 95fab84fb6472339c9d6b847f8c09139b55e1466
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/29/2019
ms.locfileid: "66303844"
---
# <a name="tutorial-develop-a-java-iot-edge-module-for-linux-devices"></a>Samouczek: Tworzenie modułu usługi IoT Edge w języku Java dla urządzeń z systemem Linux

Moduły usługi Azure IoT Edge umożliwiają wdrożenie kodu implementującego logikę biznesową bezpośrednio na urządzeniach usługi IoT Edge. W tym samouczku przedstawiono sposób tworzenia i wdrażania modułu usługi IoT Edge, w którym są filtrowane dane czujnika. Użyjesz symulowane urządzenie usługi IoT Edge, który został utworzony w wdrożenia usługi Azure IoT Edge na symulowanym urządzeniu w [Linux](quickstart-linux.md) Szybki Start. Ten samouczek zawiera informacje na temat wykonywania następujących czynności:    

> [!div class="checklist"]
> * Używanie programu Visual Studio Code do tworzenia modułu usługi IoT Edge w języku Java w oparciu o pakiet szablonów programu Maven usługi Azure IoT Edge oraz zestaw SDK urządzeń Azure IoT dla języka Java.
> * Tworzenie obrazu platformy Docker i publikowanie go w rejestrze przy użyciu programu Visual Studio Code i platformy Docker.
> * Wdrażanie modułu na urządzeniu usługi IoT Edge.
> * Wyświetlanie wygenerowanych danych.


Utworzony w tym samouczku moduł usługi IoT Edge filtruje dane temperatury generowane przez urządzenie. Komunikaty są wysyłane tylko wtedy, gdy temperatura przekroczy określony próg. Ten typ analizy brzegowej pomaga zmniejszyć ilość danych przekazywanych do chmury i w niej przechowywanych. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="solution-scope"></a>Zakres rozwiązania

Ten samouczek przedstawia sposób tworzenia modułu w **Java** przy użyciu **programu Visual Studio Code**oraz jak wdrożyć ją **urządzenia z systemem Linux**. Usługi IoT Edge obsługuje tylko moduły języka Java dla urządzeń Windows. 

Skorzystaj z poniższej tabeli, aby poznać opcje tworzenia i wdrażania modułów języka Java: 

| Java | Visual Studio Code | Visual Studio 2017/2019 | 
| - | ------------------ | ------------------ |
| **AMD64 systemu Linux** | ![Używanie programu VS Code dla modułów języka Java w systemie Linux AMD64](./media/tutorial-c-module/green-check.png) |  |
| **ARM32 systemu Linux** | ![Używanie programu VS Code dla modułów języka Java w systemie Linux ARM32](./media/tutorial-c-module/green-check.png) |  |

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem tego samouczka, powinien wykonano za pomocą poprzedniego samouczka, aby skonfigurować środowisko programowania do tworzenia aplikacji kontenera systemu Linux: [Twórz moduły usługi IoT Edge dla urządzeń z systemem Linux](tutorial-develop-for-linux.md). Wykonując jedną z tych samouczków, musisz mieć następujące wymagania wstępne spełnione: 

* Usługa [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) w warstwie Bezpłatna lub Standardowa na platformie Azure.
* A [Linux urządzenia z usługą Azure IoT Edge](quickstart-linux.md)
* Rejestr kontenera, takiej jak [usługi Azure Container Registry](https://docs.microsoft.com/azure/container-registry/).
* [Visual Studio Code](https://code.visualstudio.com/) skonfigurowano [narzędzia IoT Azure](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
* [Docker CE](https://docs.docker.com/install/) skonfigurowane do uruchamiania kontenerów systemu Linux.

Aby opracować moduł usługi IoT Edge w języku Java, należy zainstalować następujące dodatkowe wymagania wstępne na komputerze deweloperskim: 

* [Pakiet rozszerzenia języka Java](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-pack) dla programu Visual Studio Code.
* [Java SE Development Kit 10](https://aka.ms/azure-jdks) i [ustaw zmienną środowiskową `JAVA_HOME`](https://docs.oracle.com/cd/E19182-01/820-7851/inst_cli_jdk_javahome_t/) tak, aby wskazywała instalację zestawu JDK.
* [Maven](https://maven.apache.org/)

## <a name="create-a-module-project"></a>Utwórz projekt modułu
W poniższych krokach przedstawiono sposób tworzenia projektu modułu usługi IoT Edge w oparciu o pakiet szablonów programu Maven usługi Azure IoT Edge oraz zestaw SDK urządzeń Azure IoT z obsługą języka Java. Utworzysz projekt za pomocą programu Visual Studio Code i narzędzi usługi Azure IoT.

### <a name="create-a-new-project"></a>Tworzenie nowego projektu

Utwórz szablon rozwiązania w języku Java, który można dostosować przy użyciu własnego kodu. 

1. W programie Visual Studio Code wybierz kolejno pozycje **Widok** > **Paleta poleceń**, aby otworzyć paletę poleceń programu VS Code. 

2. W palecie poleceń wprowadź i uruchom polecenie **Azure IoT Edge: New IoT Edge solution** (Azure IoT Edge: nowe rozwiązanie usługi IoT Edge). Postępuj zgodnie z monitami wyświetlanymi na palecie poleceń, aby utworzyć rozwiązanie.

   | Pole | Wartość |
   | ----- | ----- |
   | Wybierz folder | Wybierz lokalizację na maszynie deweloperskiej dla programu VS Code, aby utworzyć pliki rozwiązania. |
   | Podaj nazwę rozwiązania | Wprowadź opisową nazwę rozwiązania lub zaakceptuj nazwę domyślną **EdgeSolution**. |
   | Wybierz szablon modułu | Wybierz moduł **Java Module**. |
   | Podaj wartość identyfikatora grupy groupId | Podaj wartość identyfikatora grupy lub zaakceptuj wartość domyślną **com.edgemodule**. |
   | Podaj nazwę modułu | Nadaj modułowi nazwę **JavaModule**. |
   | Podaj repozytorium obrazów platformy Docker dla modułu | Repozytorium obrazów zawiera nazwę rejestru kontenerów oraz nazwę obrazu kontenera. Obraz kontenera jest wstępnie wypełniany na podstawie nazwy podanej w ostatnim kroku. Zastąp ciąg **localhost:5000** wartością serwera logowania z rejestru kontenerów platformy Azure. Serwer logowania możesz pobrać ze strony Przegląd rejestru kontenerów w witrynie Azure Portal. <br><br>Ostateczne repozytorium obrazów wygląda następująco: \<nazwa rejestru\>.azurecr.io/javamodule. |
 
   ![Udostępnianie repozytorium obrazów platformy Docker](./media/tutorial-java-module/repository.png)
   
Jeśli moduł Java jest tworzony po raz pierwszy, pobieranie pakietów maven może potrwać kilka minut. Następnie w oknie programu VS Code zostanie załadowany obszar roboczy rozwiązania usługi IoT Edge. Obszar roboczy rozwiązania zawiera pięć składników najwyższego poziomu. Folder **modules** zawiera kod w języku Java dla modułu, a także pliki platformy Docker na potrzeby tworzenia modułu jako obrazu kontenera. W pliku **\.env** są przechowywane poświadczenia rejestru kontenerów. Plik **deployment.template.json** zawiera informacje, których środowisko uruchomieniowe usługi IoT Edge używa do wdrażania modułów na urządzeniu. Natomiast plik **deployment.debug.template.json** zawiera wersję modułów służącą do debugowania. Folder **\.vscode** i plik **\.gitignore** nie będą edytowane w tym samouczku. 

Jeśli podczas tworzenia własnego rozwiązania nie określisz rejestru kontenerów, ale zaakceptujesz wartość domyślną localhost:5000, nie będziesz mieć pliku \.env. 

### <a name="add-your-registry-credentials"></a>Dodawanie poświadczeń rejestru

W pliku środowiska przechowywane są poświadczenia rejestru kontenerów udostępniane środowisku uruchomieniowemu usługi IoT Edge. Środowisko uruchomieniowe wymaga tych poświadczeń do ściągnięcia prywatnych obrazów na urządzenie usługi IoT Edge. 

1. W eksploratorze programu VS Code otwórz plik env. 
2. Zaktualizuj pola, używając **nazwy użytkownika** i **hasła**, które zostały skopiowane z usługi Azure Container Registry. 
3. Zapisz ten plik. 

### <a name="select-your-target-architecture"></a>Wybierz swoje Architektura docelowa

Obecnie usługa Visual Studio Code można tworzyć moduły języka Java dla urządzeń z AMD64 systemu Linux i ARM32v7 systemu Linux. Musisz wybrać architektury objęci za pomocą każdego rozwiązania, ponieważ skompilowane i uruchom w różny sposób dla każdego typu architektury kontenera. Wartość domyślna to AMD64 systemu Linux. 

1. Otwórz paletę poleceń i wyszukaj **usługi Azure IoT Edge: Ustaw domyślne platformę docelową dla nowoczesne rozwiązanie**, lub wybierz ikonę skrótu prowadzącą w pasku bocznym, w dolnej części okna. 

2. W palecie poleceń Wybierz architektury docelowej z listy opcji. W tym samouczku używamy maszynę wirtualną Ubuntu jako urządzenie usługi IoT Edge, dzięki czemu będzie Zachowaj ustawienie domyślne **amd64**. 

### <a name="update-the-module-with-custom-code"></a>Aktualizowanie modułu przy użyciu kodu niestandardowego

1. W eksploratorze programu VS Code otwórz kolejno pozycje **modules** > **JavaModule** > **src** > **main** > **java** > **com** > **edgemodule** > **App.java**.

2. Dodaj następujący kod w górnej części pliku, aby zaimportować nowe przywoływane klasy.

    ```java
    import java.io.StringReader;
    import java.util.concurrent.atomic.AtomicLong;
    import java.util.HashMap;
    import java.util.Map;
    
    import javax.json.Json;
    import javax.json.JsonObject;
    import javax.json.JsonReader;
    
    import com.microsoft.azure.sdk.iot.device.DeviceTwin.Pair;
    import com.microsoft.azure.sdk.iot.device.DeviceTwin.Property;
    import com.microsoft.azure.sdk.iot.device.DeviceTwin.TwinPropertyCallBack;
    ```

3. Dodaj następującą definicję w klasie **App**. Ten zestaw zmiennych określa wartość progową temperatury. Zmierzona temperatura maszyny nie będzie zgłaszana w usłudze IoT Hub, dopóki nie przekroczy tej wartości. 

    ```java
    private static final String TEMP_THRESHOLD = "TemperatureThreshold";
    private static AtomicLong tempThreshold = new AtomicLong(25);
    ```

4. Zastąp metodę execute elementu **MessageCallbackMqtt** następującym kodem. Ta metoda jest wywoływana za każdym razem, gdy moduł odbiera komunikat MQTT z centrum usługi IoT Edge. Odfiltrowuje ona komunikaty zgłaszające temperatury nie przekraczające wartości progowej temperatury ustawionej za pomocą bliźniaczej reprezentacji modułu.

    ```java
    protected static class MessageCallbackMqtt implements MessageCallback {
        private int counter = 0;
        @Override
        public IotHubMessageResult execute(Message msg, Object context) {
            this.counter += 1;
 
            String msgString = new String(msg.getBytes(), Message.DEFAULT_IOTHUB_MESSAGE_CHARSET);
            System.out.println(
                   String.format("Received message %d: %s",
                            this.counter, msgString));
            if (context instanceof ModuleClient) {
                try (JsonReader jsonReader = Json.createReader(new StringReader(msgString))) {
                    final JsonObject msgObject = jsonReader.readObject();
                    double temperature = msgObject.getJsonObject("machine").getJsonNumber("temperature").doubleValue();
                    long threshold = App.tempThreshold.get();
                    if (temperature >= threshold) {
                        ModuleClient client = (ModuleClient) context;
                        System.out.println(
                            String.format("Temperature above threshold %d. Sending message: %s",
                            threshold, msgString));
                        client.sendEventAsync(msg, eventCallback, msg, App.OUTPUT_NAME);
                    }
                } catch (Exception e) {
                    e.printStackTrace();
                }
            }
            return IotHubMessageResult.COMPLETE;
        }
    }
    ```

5. Dodaj następujące dwie statyczne klasy wewnętrzne w klasie **App**. Te klasy aktualizują zmienną tempThreshold, gdy zmieni się określona właściwość bliźniaczej reprezentacji modułu. Wszystkie moduły mają swoje bliźniacze reprezentacje, dzięki czemu można skonfigurować kod działający wewnątrz modułu bezpośrednio z poziomu chmury.

    ```java
    protected static class DeviceTwinStatusCallBack implements IotHubEventCallback {
        @Override
        public void execute(IotHubStatusCode status, Object context) {
            System.out.println("IoT Hub responded to device twin operation with status " + status.name());
        }
    }
 
    protected static class OnProperty implements TwinPropertyCallBack {
        @Override
        public void TwinPropertyCallBack(Property property, Object context) {
            if (!property.getIsReported()) {
                if (property.getKey().equals(App.TEMP_THRESHOLD)) {
                    try {
                        long threshold = Math.round((double) property.getValue());
                        App.tempThreshold.set(threshold);
                    } catch (Exception e) {
                        System.out.println("Faile to set TemperatureThread with exception");
                        e.printStackTrace();
                    }
                }
            }
        }
    }
    ```

6. Dodaj następujące wiersze do metody **main** po metodzie **client.open()** , aby subskrybować aktualizacje bliźniaczej reprezentacji modułu.

    ```java
    client.startTwin(new DeviceTwinStatusCallBack(), null, new OnProperty(), null);
    Map<Property, Pair<TwinPropertyCallBack, Object>> onDesiredPropertyChange = new HashMap<Property, Pair<TwinPropertyCallBack, Object>>() {
        {
            put(new Property(App.TEMP_THRESHOLD, null), new Pair<TwinPropertyCallBack, Object>(new OnProperty(), null));
        }
    };
    client.subscribeToTwinDesiredProperties(onDesiredPropertyChange);
    client.getTwin();
    ```

7. Zapisz plik App.java.

8. W eksploratorze programu VS Code otwórz plik **deployment.template.json** w obszarze roboczym rozwiązania usługi IoT Edge.

9. Dodaj bliźniaczą reprezentację modułu **JavaModule** do manifestu wdrożenia. Wstaw następującą zawartość do pliku JSON na końcu sekcji **moduleContent**, po bliźniaczej reprezentacji modułu **$edgeHub**: 

   ```json
     "JavaModule": {
         "properties.desired":{
             "TemperatureThreshold":25
         }
     }
   ```

   ![Dodawanie bliźniaczej reprezentacji modułu do szablonu wdrożenia](./media/tutorial-java-module/module-twin.png)

10. Zapisz plik deployment.template.json.

## <a name="build-and-push-your-module"></a>Zbuduj i Wypchnij modułu

W poprzedniej sekcji utworzono rozwiązanie usługi IoT Edge i dodano kod do modułu **JavaModule**, aby filtrować komunikaty, w których zgłoszona temperatura maszyny jest niższa od akceptowalnego limitu. Teraz należy skompilować to rozwiązanie jako obraz kontenera i wypchnąć go do rejestru kontenerów. 

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

4. Kliknij przycisk Odśwież. Powinien zostać wyświetlony nowy moduł **JavaModule** uruchomiony wraz z modułami **TempSensor**, **$edgeAgent** i **$edgeHub**.  

## <a name="view-generated-data"></a>Wyświetlanie wygenerowanych danych

Gdy zastosujesz manifest wdrożenia na urządzeniu usługi IoT Edge, środowisko uruchomieniowe usługi IoT Edge na tym urządzeniu zbierze nowe informacje na temat wdrożenia i zacznie wykonywanie. Wszelkie moduły uruchomione na urządzeniu, których nie uwzględniono w manifeście wdrożenia, zostaną zatrzymane. Wszelkie moduły, których brakuje na urządzeniu, zostaną uruchomione.

Możesz wyświetlić stan urządzenia usługi IoT Edge w sekcji **Azure IoT Hub Devices** (Urządzenia usługi Azure IoT Hub) w eksploratorze programu Visual Studio Code. Rozwiń szczegóły urządzenia, aby wyświetlić listę wdrożonych i uruchomionych modułów.

1. W Eksploratorze programu Visual Studio Code kliknij prawym przyciskiem myszy nazwę urządzenia usługi IoT Edge, a następnie wybierz **rozpocząć monitorowanie wbudowany zdarzeń punkt końcowy**.

2. Wyświetl wiadomości otrzymywanych z usługi IoT Hub. Może potrwać trochę wiadomości zostanie dostarczona, ponieważ urządzenia usługi IoT Edge do odbierania jego nowego wdrożenia i uruchamiania wszystkich modułów. Następnie zmiany wprowadzone do kodu JavaModule poczekaj, aż temperatury maszyny osiągnie 25 stopni przed wysłaniem wiadomości. Typ komunikatu dodaje także **alertu** na wszystkie komunikaty, w których progu temperatury. 

## <a name="edit-the-module-twin"></a>Edytuj bliźniaczą reprezentację modułu

Użyliśmy JavaModule bliźniaczą reprezentację modułu w pliku manifestu wdrożenia, aby ustawić próg temperaturę w stopniach 25. Bliźniacza reprezentacja modułu można użyć w celu zmiany funkcji bez konieczności aktualizowania kodu modułu.

1. W programie Visual Studio Code rozwiń szczegóły w obszarze urządzenia usługi IoT Edge, aby wyświetlić uruchomione moduły. 

2. Kliknij prawym przyciskiem myszy **JavaModule** i wybierz **bliźniaczą reprezentację modułu edycji**. 

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
