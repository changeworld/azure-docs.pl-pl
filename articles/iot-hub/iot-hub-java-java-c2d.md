---
title: Komunikaty z chmury do urządzenia z usługą Azure IoT Hub (Java) | Dokumentacja firmy Microsoft
description: Jak wysyłać komunikaty z chmury do urządzenia na urządzeniu z usługi Azure IoT hub przy użyciu zestawów SDK usługi Azure IoT dla języka Java. Możesz zmodyfikować aplikacji symulowanego urządzenia do odbierania komunikatów z chmury do urządzeń i modyfikowania aplikacji zaplecza, do wysyłania komunikatów z chmury do urządzeń.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: conceptual
ms.date: 06/28/2017
ms.openlocfilehash: 9032a6903833ba819e09fd1ca11cd6b43d5485cb
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60399491"
---
# <a name="send-cloud-to-device-messages-with-iot-hub-java"></a>Wysyłanie komunikatów z chmury do urządzeń z usługą IoT Hub (Java)

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

Usługa Azure IoT Hub to w pełni zarządzana usługa, która ułatwia włączanie bezpieczną i niezawodną komunikację dwukierunkową między milionami urządzeń i zapleczem rozwiązania. [Wysyłanie danych telemetrycznych z urządzenia do Centrum (Java)](quickstart-send-telemetry-java.md) samouczek pokazuje sposób tworzenia Centrum IoT hub, aprowizować w nim tożsamości urządzenia i kodu aplikacji symulowanego urządzenia, która wysyła komunikaty z urządzenia do chmury.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Ten samouczek opiera się [wysyłanie danych telemetrycznych z urządzenia do usługi IoT Hub (Java)](quickstart-send-telemetry-java.md). Prezentuje sposób wykonywania następujących czynności:

* Z zapleczem rozwiązania wysyłanie komunikatów z chmury do urządzeń do pojedynczego urządzenia za pomocą usługi IoT Hub.

* Odbieranie komunikatów z chmury do urządzeń na urządzeniu.

* Z zapleczem rozwiązania, żądania potwierdzenia dostarczania (*opinii*) dla wiadomości wysyłanych do urządzenia z usługi IoT Hub.

Więcej informacji można znaleźć na [komunikatów z chmury do urządzeń w przewodniku dla deweloperów usługi IoT Hub](iot-hub-devguide-messaging.md).

Na końcu tego samouczka możesz uruchomić dwie aplikacje konsolowe Java:

* **Symulowane urządzenie**, zmodyfikowanej wersji aplikacji utworzonej w [wysyłanie danych telemetrycznych z urządzenia do Centrum (Java)](quickstart-send-telemetry-java.md), który nawiązuje połączenie z Centrum IoT i odbiera komunikaty z chmury do urządzenia.

* **send-c2d-messages**, która jest wysyłana wiadomość chmury do urządzenia w aplikacji symulowanego urządzenia za pomocą usługi IoT Hub i odbiera jego potwierdzenie dostawy.

> [!NOTE]
> Usługa IoT Hub obsługuje zestaw SDK na wielu platformach i językach (w tym C, Java i Javascript) za pomocą zestawów SDK urządzeń Azure IoT. Aby uzyskać instrukcje krok po kroku dotyczące łączenia urządzenia do kodu w tym samouczku i ogólnie do usługi Azure IoT Hub, zobacz [Centrum deweloperów Azure IoT](https://azure.microsoft.com/develop/iot).

Do wykonania kroków tego samouczka niezbędne są następujące elementy:

* Pełną wersję pracy [wysyłanie danych telemetrycznych z urządzenia do Centrum (Java)](quickstart-send-telemetry-java.md) lub [skonfigurować routing komunikatów usługi IoT Hub](tutorial-routing.md) samouczka.

* Najnowszy zestaw [Java SE Development Kit 8](https://aka.ms/azure-jdks)

* [Maven 3](https://maven.apache.org/install.html)

* Aktywne konto platformy Azure. Jeśli nie masz konta, możesz utworzyć [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/) w zaledwie kilka minut.

## <a name="receive-messages-in-the-simulated-device-app"></a>Odbieranie wiadomości w aplikacji symulowanego urządzenia

W tej sekcji zmodyfikujesz symulowanej aplikacji urządzenia utworzone w [wysyłanie danych telemetrycznych z urządzenia do Centrum (Java)](quickstart-send-telemetry-java.md) do odbierania komunikatów z chmury do urządzeń z usługi IoT hub.

1. Za pomocą edytora tekstów otwórz plik simulated-device\src\main\java\com\mycompany\app\App.java.

2. Dodaj następujący kod **MessageCallback** klasy jako klasy zagnieżdżonej wewnątrz **aplikacji** klasy. **Wykonania** metoda jest wywoływana, gdy urządzenie otrzymuje komunikat z usługi IoT Hub. W tym przykładzie urządzenia zawsze powiadamia o Centrum IoT hub o zakończeniu komunikat:

    ```java
    private static class AppMessageCallback implements MessageCallback {
      public IotHubMessageResult execute(Message msg, Object context) {
        System.out.println("Received message from hub: "
          + new String(msg.getBytes(), Message.DEFAULT_IOTHUB_MESSAGE_CHARSET));
    
        return IotHubMessageResult.COMPLETE;
      }
    }
    ```
3. Modyfikowanie **głównego** metodę w celu utworzenia **AppMessageCallback** wystąpienie i wywołania **setMessageCallback** metoda przed jego otwarciem klienta w następujący sposób:

    ```java
    client = new DeviceClient(connString, protocol);
   
    MessageCallback callback = new AppMessageCallback();
    client.setMessageCallback(callback, null);
    client.open();
    ```

    > [!NOTE]
    > Jeśli używasz protokołu HTTPS zamiast protokołu MQTT, AMQP jako transportu, **DeviceClient** wystąpienia sprawdza, czy komunikaty z usługi IoT Hub rzadko (mniej niż co 25 minut). Aby uzyskać więcej informacji na temat różnic między Obsługa protokołu MQTT, AMQP i protokołu HTTPS i ograniczania przepustowości usługi IoT Hub, zobacz [komunikatów części przewodnika dla deweloperów usługi IoT Hub](iot-hub-devguide-messaging.md).

4. Aby utworzyć aplikację **simulated-device** przy użyciu narzędzia Maven, wykonaj następujące polecenie w wierszu polecenia w folderze simulated-device:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="send-a-cloud-to-device-message"></a>Wysyłanie komunikatów chmura urządzenie

W tej sekcji utworzysz aplikację konsolową Java, która wysyła komunikaty z chmury do urządzenia w aplikacji symulowanego urządzenia. Potrzebny jest identyfikator urządzenia, które urządzenia zostały dodane w [wysyłanie danych telemetrycznych z urządzenia do Centrum (Java)](quickstart-send-telemetry-java.md) Szybki Start. Należy również parametry połączenia Centrum IoT Hub dla Centrum można znaleźć w [witryny Azure portal](https://portal.azure.com).

1. Utwórz projekt narzędzia Maven o nazwie **send-c2d-messages** przy użyciu następującego polecenia w wierszu polecenia. Należy pamiętać, że to polecenie jest jedno długie polecenie:

    ```cmd/sh
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=send-c2d-messages -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. W wierszu polecenia przejdź do nowego folderu send-c2d-messages.

3. Za pomocą edytora tekstu Otwórz plik pom.xml w folderze Wyślij c2d-messages i dodaj następującą zależność do **zależności** węzła. Dodawanie zależności pozwala na użycie **iothub-java-service-client** pakietu w aplikacji do komunikowania się z usługą IoT hub:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-service-client</artifactId>
      <version>1.7.23</version>
    </dependency>
    ```

    > [!NOTE]
    > Możesz sprawdzić dostępność najnowszej wersji **iot-service-client** przy użyciu [funkcji wyszukiwania narzędzia Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-service-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22).

4. Zapisz i zamknij plik pom.xml.

5. Za pomocą edytora tekstu Otwórz plik send-c2d-messages\src\main\java\com\mycompany\app\App.java.

6. Dodaj do pliku następujące instrukcje **importowania**:

    ```java
    import com.microsoft.azure.sdk.iot.service.*;
    import java.io.IOException;
    import java.net.URISyntaxException;
    ```

7. Dodaj następujące zmienne poziomu klasa **aplikacji** klasy, zastępując **{yourhubconnectionstring}** i **{yourdeviceid}** wartościami zanotowanymi wcześniej:

    ```java
    private static final String connectionString = "{yourhubconnectionstring}";
    private static final String deviceId = "{yourdeviceid}";
    private static final IotHubServiceClientProtocol protocol =    
        IotHubServiceClientProtocol.AMQPS;
    ```

8. Zastąp **głównego** metoda następującym kodem. Ten kod nawiązanie połączenia z Centrum IoT hub, wysyła komunikat do Twojego urządzenia i następnie oczekuje na potwierdzenie, że urządzenie odbierane i przetwarzane komunikat:
   
    ```java
    public static void main(String[] args) throws IOException,
        URISyntaxException, Exception {
      ServiceClient serviceClient = ServiceClient.createFromConnectionString(
        connectionString, protocol);
   
      if (serviceClient != null) {
        serviceClient.open();
        FeedbackReceiver feedbackReceiver = serviceClient
          .getFeedbackReceiver();
        if (feedbackReceiver != null) feedbackReceiver.open();
   
        Message messageToSend = new Message("Cloud to device message.");
        messageToSend.setDeliveryAcknowledgement(DeliveryAcknowledgement.Full);
   
        serviceClient.send(deviceId, messageToSend);
        System.out.println("Message sent to device");
   
        FeedbackBatch feedbackBatch = feedbackReceiver.receive(10000);
        if (feedbackBatch != null) {
          System.out.println("Message feedback received, feedback time: "
            + feedbackBatch.getEnqueuedTimeUtc().toString());
        }
   
        if (feedbackReceiver != null) feedbackReceiver.close();
        serviceClient.close();
      }
    }
    ```

    > [!NOTE]
    > Sake dla uproszczenia w tym samouczku nie implementuje żadnych zasad ponawiania. W kodzie produkcyjnym należy wdrożyć zasady ponawiania (np. wycofywanie wykładnicze) zgodnie z sugestią podaną w artykule [obsługi błędów przejściowych](/azure/architecture/best-practices/transient-faults).


9. Aby utworzyć aplikację **simulated-device** przy użyciu narzędzia Maven, wykonaj następujące polecenie w wierszu polecenia w folderze simulated-device:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="run-the-applications"></a>Uruchamianie aplikacji

Teraz można uruchomić aplikacje.

1. W wierszu polecenia w folderze simulated-device uruchom następujące polecenie, aby rozpocząć wysyłanie danych telemetrycznych do Centrum IoT hub oraz nasłuchiwać komunikatów z chmury do urządzeń wysyłane z Centrum:

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App" 
    ```

    ![Uruchamianie aplikacji symulowanego urządzenia](./media/iot-hub-java-java-c2d/receivec2d.png)

2. W wierszu polecenia w folderze Wyślij c2d-messages uruchom następujące polecenie, aby wysłać wiadomość chmury do urządzenia i poczekaj na potwierdzenie opinii:

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Uruchom polecenie, aby wysłać komunikatu chmura urządzenie](media/iot-hub-java-java-c2d/sendc2d.png)

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku przedstawiono sposób wysyłania i odbierania komunikatów z chmury do urządzeń. 

Aby wyświetlić przykłady kompletne rozwiązania end-to-end, które używają usługi IoT Hub, zobacz [akceleratorów rozwiązań IoT Azure](https://azure.microsoft.com/documentation/suites/iot-suite/).

Aby dowiedzieć się więcej na temat opracowywania rozwiązań usługi IoT Hub, zobacz [usługi IoT Hub — przewodnik dewelopera](iot-hub-devguide.md).