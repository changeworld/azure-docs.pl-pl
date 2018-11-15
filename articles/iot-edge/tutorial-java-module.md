---
title: Samouczek usługi Azure IoT Edge dla języka Java | Microsoft Docs
description: W tym samouczku pokazano, jak utworzyć moduł usługi IoT Edge za pomocą kodu języka Java i wdrożyć go na urządzeniu brzegowym.
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 09/21/2018
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: d5201f3b2a0a3548b1f9eaf4a3f6c6b0fe160d18
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/12/2018
ms.locfileid: "51567710"
---
# <a name="tutorial-develop-a-java-iot-edge-module-and-deploy-to-your-simulated-device"></a>Samouczek: programowanie modułu usługi IoT Edge w języku Java i wdrażanie go na urządzeniu symulowanym

Moduły usługi Azure IoT Edge umożliwiają wdrożenie kodu implementującego logikę biznesową bezpośrednio na urządzeniach usługi IoT Edge. W tym samouczku przedstawiono sposób tworzenia i wdrażania modułu usługi IoT Edge, w którym są filtrowane dane czujnika. Użyjesz symulowanego urządzenia usługi IoT Edge utworzonego podczas pracy z przewodnikami Szybki start dotyczącymi wdrażania usługi Azure IoT Edge na urządzeniu symulowanym w systemie [Windows](quickstart.md) lub [Linux](quickstart-linux.md). Ten samouczek zawiera informacje na temat wykonywania następujących czynności:    

> [!div class="checklist"]
> * Używanie programu Visual Studio Code do tworzenia modułu usługi IoT Edge w języku Java w oparciu o pakiet szablonów programu Maven usługi Azure IoT Edge oraz zestaw SDK urządzeń Azure IoT dla języka Java.
> * Tworzenie obrazu platformy Docker i publikowanie go w rejestrze przy użyciu programu Visual Studio Code i platformy Docker.
> * Wdrażanie modułu na urządzeniu usługi IoT Edge.
> * Wyświetlanie wygenerowanych danych.


Utworzony w tym samouczku moduł usługi IoT Edge filtruje dane temperatury generowane przez urządzenie. Komunikaty są wysyłane tylko wtedy, gdy temperatura przekroczy określony próg. Ten typ analizy brzegowej pomaga zmniejszyć ilość danych przekazywanych do chmury i w niej przechowywanych. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Wymagania wstępne

Urządzenie usługi Azure IoT Edge:

* Jako urządzenia brzegowego możesz użyć maszyny deweloperskiej albo maszyny wirtualnej, postępując zgodnie z instrukcjami w przewodniku Szybki start dla urządzeń z systemem [Linux](quickstart-linux.md).
* Moduły języka Java dla usługi IoT Edge nie obsługują urządzeń z systemem Windows.

Zasoby w chmurze:

* Usługa [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) w warstwie Bezpłatna lub Standardowa na platformie Azure. 

Zasoby do programowania:

* [Program Visual Studio Code](https://code.visualstudio.com/) 
* [Pakiet rozszerzenia języka Java](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-pack) dla programu Visual Studio Code.
* [Rozszerzenie usługi Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) dla programu Visual Studio Code. 
* [Java SE Development Kit 10](https://aka.ms/azure-jdks) i [ustaw zmienną środowiskową `JAVA_HOME`](https://docs.oracle.com/cd/E19182-01/820-7851/inst_cli_jdk_javahome_t/) tak, aby wskazywała instalację zestawu JDK.
* [Maven](https://maven.apache.org/)
* [Program Docker CE](https://docs.docker.com/install/)
   * Jeśli programujesz na urządzeniu z systemem Windows, upewnij się, że platforma Docker jest [skonfigurowana do używania kontenerów systemu Linux](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers). 


## <a name="create-a-container-registry"></a>Tworzenie rejestru kontenerów
W tym samouczku rozszerzenie usługi Azure IoT Edge dla programu VS Code zostanie użyte do zbudowania modułu i utworzenia **obrazu kontenera** na podstawie plików. Następnie ten obraz zostanie wypchnięty do **rejestru**, w którym obrazy są przechowywane i zarządzane. Na koniec obraz zostanie wdrożony z rejestru w celu uruchomienia na urządzeniu usługi IoT Edge.  

Na potrzeby tego samouczka możesz użyć dowolnego rejestru zgodnego z platformą Docker. Dwie popularne usługi rejestru Docker dostępne w chmurze to [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) i [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags). W tym samouczku używana jest usługa Azure Container Registry. 

1. W witrynie [Azure Portal](https://portal.azure.com) wybierz pozycję **Utwórz zasób** > **Kontenery** > **Azure Container Registry**.
2. Nazwij rejestr, wybierz subskrypcję i grupę zasobów oraz ustaw jednostkę SKU na wartość **Podstawowa**. 
3. Wybierz pozycję **Utwórz**.
4. Po utworzeniu rejestru kontenerów przejdź do niego i wybierz pozycję **Klucze dostępu**. 
5. Przełącz pozycję **Administrator** na wartość **Włącz**.
6. Skopiuj wartości w polach **Serwer logowania**, **Nazwa użytkownika** i **Hasło**. Te wartości są używane w dalszej części samouczka w celu publikowania obrazu platformy Docker do rejestru oraz dodawania poświadczeń rejestru do środowiska uruchomieniowego usługi Azure IoT Edge. 

## <a name="create-an-iot-edge-module-project"></a>Tworzenie projektu modułu usługi IoT Edge
W poniższych krokach przedstawiono sposób tworzenia projektu modułu usługi IoT Edge w oparciu o pakiet szablonów programu Maven usługi Azure IoT Edge oraz zestaw SDK urządzeń Azure IoT dla języka Java przy użyciu programu Visual Studio Code i rozszerzenia usługi Azure IoT Edge.

### <a name="create-a-new-solution"></a>Tworzenie nowego rozwiązania

Utwórz szablon rozwiązania w języku Java, który można dostosować przy użyciu własnego kodu. 

1. W programie Visual Studio Code wybierz kolejno pozycje **Widok** > **Paleta poleceń**, aby otworzyć paletę poleceń programu VS Code. 

2. W palecie poleceń wprowadź i uruchom polecenie **Azure IoT Edge: nowe rozwiązanie usługi IoT Edge**. W palecie poleceń podaj następujące informacje, aby utworzyć rozwiązanie: 

   1. Wybierz folder, w którym chcesz utworzyć rozwiązanie. 
   2. Podaj nazwę rozwiązania lub zaakceptuj nazwę domyślną **EdgeSolution**.
   3. Wybierz szablon modułu **Java Module** (Moduł języka Java). 
   4. Podaj wartość dla elementu groupId lub zaakceptuj wartość domyślną **com.edgemodule**.
   5. Zastąp domyślną nazwę modułu ciągiem **JavaModule**. 
   6. Określ rejestr kontenerów platformy Azure utworzony w poprzedniej sekcji jako repozytorium obrazów dla pierwszego modułu. Zastąp ciąg **localhost:5000** skopiowaną wartością serwera logowania. Ostatecznie ciąg będzie wyglądał następująco: \<nazwa rejestru\>.azurecr.io/javamodule.


Jeśli moduł Java jest tworzony po raz pierwszy, pobieranie pakietów maven może potrwać kilka minut. Następnie w oknie programu VS Code zostanie załadowany obszar roboczy rozwiązania usługi IoT Edge. Obszar roboczy rozwiązania zawiera pięć składników najwyższego poziomu. Folder **\.vscode** i plik **\.gitignore** nie będą edytowane w tym samouczku. Folder **modules** zawiera kod w języku Java dla modułu, a także pliki Dockerfile na potrzeby tworzenia modułu jako obrazu kontenera. W pliku **\.env** są przechowywane poświadczenia rejestru kontenerów. Plik **deployment.template.json** zawiera informacje, których środowisko uruchomieniowe usługi IoT Edge używa do wdrażania modułów na urządzeniu. 

Jeśli podczas tworzenia własnego rozwiązania nie określisz rejestru kontenerów, ale zaakceptujesz wartość domyślną localhost:5000, nie będziesz mieć pliku \.env. 

### <a name="add-your-registry-credentials"></a>Dodawanie poświadczeń rejestru

W pliku środowiska przechowywane są poświadczenia rejestru kontenerów udostępniane środowisku uruchomieniowemu usługi IoT Edge. Środowisko uruchomieniowe wymaga tych poświadczeń do ściągnięcia prywatnych obrazów na urządzenie usługi IoT Edge. 

1. W eksploratorze programu VS Code otwórz plik env. 
2. Zaktualizuj pola, używając **nazwy użytkownika** i **hasła**, które zostały skopiowane z usługi Azure Container Registry. 
3. Zapisz ten plik. 

### <a name="update-the-module-with-custom-code"></a>Aktualizowanie modułu przy użyciu kodu niestandardowego

1. W eksploratorze programu VS Code otwórz kolejno pozycje **modules** > **JavaModule** > **src** > **main** > **java** > **com** > **edgemodule** > **App.java**.

5. Dodaj następujący kod w górnej części pliku, aby zaimportować nowe przywoływane klasy.

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

5. Dodaj następującą definicję w klasie **App**. Za pomocą tej zmiennej jest ustawiana wartość zmierzonej temperatury, której przekroczenie spowoduje wysłanie danych do centrum IoT Hub. 

    ```java
    private static final String TEMP_THRESHOLD = "TemperatureThreshold";
    private static AtomicLong tempThreshold = new AtomicLong(25);
    ```

7. Zastąp metodę execute elementu **MessageCallbackMqtt** następującym kodem. Ta metoda jest wywoływana za każdym razem, gdy moduł odbiera komunikat MQTT z centrum usługi IoT Edge. Odfiltrowuje ona komunikaty zgłaszające temperatury nie przekraczające wartości progowej temperatury ustawionej za pomocą bliźniaczej reprezentacji modułu.

    ```java
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
    ```

8. Dodaj następujące dwie statyczne klasy wewnętrzne w klasie **App**. Te klasy odbierają aktualizacje odpowiednich właściwości z bliźniaczej reprezentacji modułu i aktualizują zmienną **tempThreshold**, aby ją dopasować. Wszystkie moduły mają swoje bliźniacze reprezentacje, dzięki czemu można skonfigurować kod działający wewnątrz modułu bezpośrednio z poziomu chmury.

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

9. Dodaj następujące wiersze do metody **main** po metodzie **client.open()**, aby subskrybować aktualizacje bliźniaczej reprezentacji modułu.

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

11. Zapisz ten plik.

## <a name="build-your-iot-edge-solution"></a>Kompilowanie rozwiązania usługi IoT Edge

W poprzedniej sekcji utworzono rozwiązanie usługi IoT Edge i dodano kod do modułu **JavaModule**, aby filtrować komunikaty, w których zgłoszona temperatura maszyny jest niższa od akceptowalnego poziomu. Teraz należy skompilować to rozwiązanie jako obraz kontenera i wypchnąć go do rejestru kontenerów. 

1. Zaloguj się do platformy Docker, wprowadzając następujące polecenie w zintegrowanym terminalu programu Visual Studio Code. Następnie możesz wypchnąć obraz modułu do rejestru kontenerów platformy Azure.
     
   ```csh/sh
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```
   Użyj nazwy użytkownika, hasła i serwera logowania skopiowanych z rejestru kontenerów platformy Azure w pierwszej sekcji. Możesz także pobrać te wartości z sekcji **Klucze dostępu** rejestru w witrynie Azure Portal.

2. W eksploratorze programu VS Code otwórz plik deployment.template.json w obszarze roboczym rozwiązania usługi IoT Edge. Ten plik wydaje agentowi **$edgeAgent** polecenie wdrożenia dwóch modułów: **tempSensor** i **JavaModule**. Wartość **JavaModule.image** jest ustawiona na wersję obrazu Linux amd64. Zmień wersję obrazu na **arm32v7**, jeśli jest to architektura urządzenia usługi IoT Edge. 

   Sprawdź, czy szablon ma prawidłową nazwę modułu, a nie domyślną nazwę **SampleModule** zmienioną podczas tworzenia rozwiązania usługi IoT Edge.

   Aby dowiedzieć się więcej na temat manifestów wdrożenia, zobacz [Jak używać modułów usługi IoT Edge, konfigurować je i używać ich ponownie](module-composition.md).

3. Plik deployment.template.json zawiera sekcję **registryCredentials**, w której są przechowywane poświadczenia rejestru platformy Docker. Rzeczywiste pary nazw użytkownika i haseł są przechowywane w pliku env, który jest ignorowany przez usługę git.  

4. Dodaj bliźniaczą reprezentację modułu **JavaModule** do manifestu wdrożenia. Wstaw następującą zawartość do pliku JSON na końcu sekcji **moduleContent**, po bliźniaczej reprezentacji modułu **$edgeHub**: 
    ```json
        "JavaModule": {
            "properties.desired":{
                "TemperatureThreshold":25
            }
        }
    ```

4. Zapisz ten plik.

5. W eksploratorze programu VS Code kliknij prawym przyciskiem myszy plik deployment.template.json i wybierz polecenie **Skompiluj i wypchnij rozwiązanie usługi IoT Edge**. 

Po wybraniu polecenia kompilowania rozwiązania w programie Visual Studio Code program najpierw pobiera informacje z szablonu wdrożenia i generuje plik deployment.json w nowym folderze **config**. Następnie uruchamia dwa polecenia w zintegrowanym terminalu: `docker build` i `docker push`. Te dwa polecenia kompilują kod, konteneryzują aplikację w języku Java i wypychają kod do rejestru kontenerów określonego podczas inicjowania rozwiązania. 

Pełny adres obrazu kontenera możesz wyświetlić za pomocą tagu w zintegrowanym terminalu programu VS Code. Adres obrazu składa się z informacji z pliku module.json w formacie \<repozytorium\>:\<wersja\>-\<platforma\>. W tym samouczku powinien wyglądać następująco: registryname.azurecr.io/javamodule:0.0.1-amd64.

## <a name="deploy-and-run-the-solution"></a>Wdrażanie i uruchamianie rozwiązania

W artykule Szybki start, który był używany do skonfigurowania urządzenia usługi IoT Edge, wdrożono moduł za pomocą witryny Azure Portal. Moduły można także wdrażać przy użyciu rozszerzenia Azure IoT Toolkit dla programu Visual Studio Code. Masz już manifest wdrożenia przygotowany dla danego scenariusza — plik **deployment.json**. Teraz wystarczy wybrać urządzenie, które ma otrzymać wdrożenie.

1. Na palecie poleceń programu VS Code uruchom polecenie **Azure: Sign in**, a następnie postępuj zgodnie z instrukcjami, aby zalogować się na koncie platformy Azure. Jeśli już się zalogowano, można pominąć ten krok.

2. W palecie poleceń programu VS Code wybierz i uruchom polecenie **Azure IoT Hub: wybierz centrum IoT Hub**. 

3. Wybierz subskrypcję i centrum IoT Hub zawierające urządzenie usługi IoT Edge, które chcesz skonfigurować. 

4. W eksploratorze programu VS rozwiń sekcję **Urządzenia usługi Azure IoT Hub**. 

5. Kliknij prawym przyciskiem myszy nazwę urządzenia usługi IoT Edge, a następnie wybierz pozycję **Utwórz wdrożenie dla pojedynczego urządzenia**. 

   ![Tworzenie wdrożenia dla pojedynczego urządzenia](./media/tutorial-java-module/create-deployment.png)

6. Wybierz plik **deployment.json** w folderze **config**, a następnie kliknij pozycję **Wybierz manifest wdrożenia usługi Edge**. Nie używaj pliku deployment.template.json. 

7. Kliknij przycisk Odśwież. Powinien zostać wyświetlony nowy moduł **JavaModule** uruchomiony wraz z modułami **TempSensor**, **$edgeAgent** i **$edgeHub**.  

## <a name="view-generated-data"></a>Wyświetlanie wygenerowanych danych

Gdy zastosujesz manifest wdrożenia na urządzeniu usługi IoT Edge, środowisko uruchomieniowe usługi IoT Edge na tym urządzeniu zbierze nowe informacje na temat wdrożenia i zacznie wykonywanie. Wszelkie moduły uruchomione na urządzeniu, których nie uwzględniono w manifeście wdrożenia, zostaną zatrzymane. Wszelkie moduły, których brakuje na urządzeniu, zostaną uruchomione. 

Możesz wyświetlić stan urządzenia usługi IoT Edge w sekcji **Azure IoT Hub Devices** (Urządzenia usługi Azure IoT Hub) w eksploratorze programu Visual Studio Code. Rozwiń szczegóły urządzenia, aby wyświetlić listę wdrożonych i uruchomionych modułów. 

Możesz również wyświetlić stan modułów wdrożenia na samym urządzeniu usługi IoT Edge, używając polecenia `iotedge list`. Powinny zostać wyświetlone cztery moduły: dwa moduły środowiska uruchomieniowego usługi IoT Edge, moduł tempSensor i moduł niestandardowy utworzony w ramach tego samouczka. Uruchomienie wszystkich modułów może potrwać kilka minut, dlatego jeśli na początku wszystkie nie będą widoczne, uruchom polecenie ponownie. 

Aby wyświetlić komunikaty generowane przez któryś moduł, użyj polecenia `iotedge logs <module name>`. 

Komunikaty odbierane w centrum IoT można wyświetlać za pomocą programu Visual Studio Code. 

1. Aby monitorować dane, które docierają do centrum IoT Hub, wybierz przycisk wielokropka (**...**), a następnie wybierz pozycję **Rozpocznij monitorowanie komunikatów D2C**.
2. Aby monitorować komunikat D2C dla określonego urządzenia, kliknij prawym przyciskiem myszy to urządzenie na liście, a następnie wybierz opcję **Rozpocznij monitorowanie komunikatów D2C**.
3. Aby zatrzymać monitorowanie danych, uruchom polecenie **Azure IoT Hub: zatrzymaj monitorowanie komunikatu D2C** w palecie poleceń. 
4. Aby wyświetlić lub zaktualizować bliźniaczą reprezentację modułu, kliknij prawym przyciskiem myszy moduł na liście, a następnie wybierz opcję **Edytuj bliźniaczą reprezentację modułu**. Aby zaktualizować bliźniaczą reprezentację modułu, zapisz jej plik JSON, kliknij prawym przyciskiem myszy obszar edytora i wybierz pozycję **Zaktualizuj bliźniaczą reprezentację modułu**.
5. Aby wyświetlić dzienniki platformy Docker, zainstaluj rozszerzenie [Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) dla narzędzia VS Code. Uruchomione moduły można znaleźć lokalnie w eksploratorze platformy Docker. W menu kontekstowym wybierz pozycję **Pokaż dzienniki**, aby wyświetlić je w zintegrowanym terminalu.
 
## <a name="clean-up-resources"></a>Oczyszczanie zasobów 

Jeśli zamierzasz przejść do kolejnego zalecanego artykułu, możesz zachować utworzone zasoby oraz konfiguracje i użyć ich ponownie. Możesz także nadal używać tego samego urządzenia usługi IoT Edge jako urządzenia testowego. 

W przeciwnym razie możesz usunąć konfigurację lokalną i zasoby platformy Azure utworzone podczas pracy z tym artykułem, aby uniknąć naliczania opłat. 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

[!INCLUDE [iot-edge-clean-up-local-resources](../../includes/iot-edge-clean-up-local-resources.md)]


## <a name="next-steps"></a>Następne kroki

W tym samouczku został utworzony moduł usługi IoT Edge zawierający kod służący do filtrowania nieprzetworzonych danych wygenerowanych przez urządzenie usługi IoT Edge. Możesz teraz kontynuować pracę, korzystając z kolejnych samouczków, aby poznać inne metody, za pomocą których usługa Azure IoT Edge może ułatwiać przekształcanie danych w analizy biznesowe na urządzeniach brzegowych.

> [!div class="nextstepaction"]
> [Store data at the edge with SQL Server databases (Przechowywanie danych na brzegu sieci przy użyciu baz danych programu SQL Server)](tutorial-store-data-sql-server.md)

