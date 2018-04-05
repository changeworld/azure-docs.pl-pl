---
title: Komunikaty chmury do urządzenia z Centrum IoT Azure (Java) | Dokumentacja firmy Microsoft
description: Jak wysyłać wiadomości chmury do urządzenia na urządzeniu z Centrum Azure IoT przy użyciu zestawów SDK IoT Azure dla języka Java. Możesz zmodyfikować aplikację symulowane urządzenie, aby odbierać komunikaty z chmury do urządzenia i modyfikowania aplikacji zaplecza, do wysyłania wiadomości chmury do urządzenia.
services: iot-hub
documentationcenter: java
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: 7f785ea8-e7c2-40c5-87ef-96525e9b9e1e
ms.service: iot-hub
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2017
ms.author: dobett
ms.openlocfilehash: 83fe0e2e39d414120bb09ad58d17b5081caef745
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2018
---
# <a name="send-cloud-to-device-messages-with-iot-hub-java"></a>Wysyłanie komunikatów chmury do urządzenia z Centrum IoT (Java)
[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

Centrum IoT Azure jest w pełni zarządzaną usługę, która umożliwia włączanie i niezawodności komunikację dwukierunkową między milionów urządzeń i zaplecze rozwiązania. [Rozpoczynanie pracy z Centrum IoT] samouczek przedstawia sposób tworzenia Centrum IoT, udostępnić tożsamość urządzenia w nim i kodem aplikacji symulowane urządzenie, który wysyła wiadomości urządzenia do chmury.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

W tym samouczku opiera się na [Rozpoczynanie pracy z Centrum IoT]. Jak przedstawiono na:

* Z sieci zaplecza rozwiązania wysyłać chmury do urządzenia do jednego urządzenia za pośrednictwem Centrum IoT.
* Komunikaty chmury do urządzenia na urządzeniu.
* Z sieci zaplecza rozwiązania, żądania potwierdzenia dostarczenia (*opinii*) dla wiadomości wysyłanych do urządzenia z Centrum IoT.

Można znaleźć więcej informacji na temat wiadomości chmury do urządzenia w [Centrum IoT — przewodnik dewelopera][IoT Hub developer guide - C2D].

Na końcu tego samouczka możesz uruchomić dwie aplikacje konsoli Java:

* **Symulowane urządzenie**, zmodyfikowanej wersji aplikacji utworzonych w [Rozpoczynanie pracy z Centrum IoT], która łączy się z Centrum IoT i odbiera komunikaty chmury do urządzenia.
* **send — c2d — liczba komunikatów**, który wysyła komunikat chmury do urządzenia do aplikacji symulowane urządzenie za pomocą Centrum IoT i następnie odbiera jego potwierdzenie dostarczenia.

> [!NOTE]
> Centrum IoT obsługuje zestawu SDK dla wielu platform urządzeń i języków (w tym C, Java i Javascript) za pośrednictwem zestawy SDK urządzenia Azure IoT. Aby uzyskać instrukcje krok po kroku dotyczące sposobu Podłącz urządzenie do kodu w tym samouczku i zwykle z Centrum IoT Azure, zobacz [Azure IoT Developer Center].

Do wykonania kroków tego samouczka niezbędne są następujące elementy:

* Pełną wersję pracy [Rozpoczynanie pracy z Centrum IoT](iot-hub-java-java-getstarted.md) lub [wiadomości urządzenia do chmury Centrum IoT procesu](iot-hub-java-java-process-d2c.md) samouczka.
* Najnowszy zestaw [Java SE Development Kit 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
* [Maven 3](https://maven.apache.org/install.html)
* Aktywne konto platformy Azure. (Jeśli go nie masz, możesz utworzyć [bezpłatne konto próbne][lnk-free-trial] w zaledwie kilka minut).

## <a name="receive-messages-in-the-simulated-device-app"></a>Odbieranie komunikatów w aplikacji symulowane urządzenie

W tej sekcji możesz zmodyfikować aplikację symulowane urządzenie, utworzony w [Rozpoczynanie pracy z Centrum IoT] odbierać komunikaty z chmury do urządzenia z Centrum IoT.

1. Za pomocą edytora tekstów otwórz plik simulated-device\src\main\java\com\mycompany\app\App.java.

2. Dodaj następujące **MessageCallback** jak zagnieżdżone klasa wewnątrz **aplikacji** klasy. **Wykonania** metoda jest wywoływana, gdy urządzenie otrzyma wiadomość z Centrum IoT. W tym przykładzie urządzenie zawsze powiadamia o Centrum IoT o zakończeniu komunikat:

    ```java
    private static class AppMessageCallback implements MessageCallback {
      public IotHubMessageResult execute(Message msg, Object context) {
        System.out.println("Received message from hub: "
          + new String(msg.getBytes(), Message.DEFAULT_IOTHUB_MESSAGE_CHARSET));
    
        return IotHubMessageResult.COMPLETE;
      }
    }
    ```
3. Modyfikowanie **głównego** metodę w celu utworzenia **AppMessageCallback** wystąpienia i wywołanie **setMessageCallback** metoda przed jego otwarciem klienta w następujący sposób:

    ```java
    client = new DeviceClient(connString, protocol);
   
    MessageCallback callback = new AppMessageCallback();
    client.setMessageCallback(callback, null);
    client.open();
    ```

    > [!NOTE]
    > Jeśli jest używany protokół HTTPS zamiast MQTT lub AMQP do transportu **DeviceClient** wystąpienia sprawdza, czy komunikaty z Centrum IoT rzadko (mniej niż co 25 minut). Aby uzyskać więcej informacji na temat różnic między MQTT, protokołu AMQP lub HTTPS obsługi i ograniczania Centrum IoT, zobacz [Centrum IoT — przewodnik dewelopera][IoT Hub developer guide - C2D].

4. Aby utworzyć aplikację **simulated-device** przy użyciu narzędzia Maven, wykonaj następujące polecenie w wierszu polecenia w folderze simulated-device:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="send-a-cloud-to-device-message"></a>Wyślij wiadomość chmury do urządzenia

W tej sekcji utworzysz aplikację konsoli języka Java, która wysyła komunikaty chmury do urządzenia do aplikacji symulowane urządzenie. Potrzebny jest identyfikator urządzenia urządzenia dodane w [Rozpoczynanie pracy z Centrum IoT] samouczka. Należy również parametry połączenia Centrum IoT, który znajduje się w Centrum [portalu Azure].

1. Utwórz projekt Maven o nazwie **wysyłania — c2d — liczba komunikatów** za pomocą następującego polecenia z wiersza polecenia. Należy zauważyć, że to polecenie jest pojedynczą, długie polecenie:

    ```cmd/sh
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=send-c2d-messages -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. Wierszu polecenia przejdź do nowego folderu wysyłania — c2d — liczba komunikatów.

3. Za pomocą edytora tekstu, otwórz plik pom.xml w folderze wysyłania — c2d — liczba komunikatów i dodaj następujące zależności do **zależności** węzła. Dodawanie zależności pozwala na użycie **Centrum iothub-java--klient usługi** pakietu aplikacji do komunikowania się z usługą Centrum IoT:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-service-client</artifactId>
      <version>1.7.23</version>
    </dependency>
    ```

    > [!NOTE]
    > Możesz sprawdzić dostępność najnowszej wersji pakietu **iot-service-client** za pomocą [funkcji wyszukiwania narzędzia Maven][lnk-maven-service-search].

4. Zapisz i zamknij plik pom.xml.

5. Za pomocą edytora tekstu, otwórz plik send-c2d-messages\src\main\java\com\mycompany\app\App.java.

6. Dodaj do pliku następujące instrukcje **importowania**:

    ```java
    import com.microsoft.azure.sdk.iot.service.*;
    import java.io.IOException;
    import java.net.URISyntaxException;
    ```

7. Dodaj następujące zmienne poziomie klasy do **aplikacji** klasy, zastępując **{yourhubconnectionstring}** i **{yourdeviceid}** z wartościami z dostrzeżone wcześniej:

    ```java
    private static final String connectionString = "{yourhubconnectionstring}";
    private static final String deviceId = "{yourdeviceid}";
    private static final IotHubServiceClientProtocol protocol = IotHubServiceClientProtocol.AMQPS;
    ```

8. Zastąp **głównego** metodę z następującym kodem. Ten kod łączy się z Centrum IoT, wysyła komunikat do Twojego urządzenia i czeka na potwierdzenie, że urządzenie odbierany i przetwarzany komunikat:
   
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
    > Sake na prostotę w tym samouczku nie implementuje wszystkie zasady ponawiania. W kodzie produkcyjnym, należy zaimplementować zasady ponawiania (na przykład wykładniczego wycofywania), zgodnie z sugestią podaną w artykuł w witrynie MSDN [obsługi błędów przejściowych].


9. Aby utworzyć aplikację **simulated-device** przy użyciu narzędzia Maven, wykonaj następujące polecenie w wierszu polecenia w folderze simulated-device:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="run-the-applications"></a>Uruchamianie aplikacji

Teraz można uruchomić aplikacje.

1. W wierszu polecenia w folderze symulowane urządzenie uruchom następujące polecenie, aby rozpocząć wysyłanie danych telemetrycznych do Centrum IoT i nasłuchiwać komunikatów chmury do urządzenia wysyłane z Centrum:

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App" 
    ```

    ![Uruchamianie aplikacji symulowane urządzenie][img-simulated-device]

2. W wierszu polecenia w folderze wysyłania — c2d — liczba komunikatów uruchom następujące polecenie, aby wysłać wiadomość chmury do urządzenia i czeka na potwierdzenie opinii:

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Uruchom polecenie, aby wysłać wiadomość chmury do urządzenia][img-send-command]

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób wysyłania i odbierania wiadomości chmury do urządzenia. 

Aby zapoznać się przykładem kompletnych rozwiązań end-to-end korzystających z Centrum IoT, zobacz [pakiet IoT Azure].

Aby dowiedzieć się więcej na temat tworzenia rozwiązań z Centrum IoT, zobacz [Centrum IoT — przewodnik dewelopera].

<!-- Images -->
[img-simulated-device]: media/iot-hub-java-java-c2d/receivec2d.png
[img-send-command]:  media/iot-hub-java-java-c2d/sendc2d.png
<!-- Links -->

[Rozpoczynanie pracy z Centrum IoT]: iot-hub-java-java-getstarted.md
[IoT Hub developer guide - C2D]: iot-hub-devguide-messaging.md
[Centrum IoT — przewodnik dewelopera]: iot-hub-devguide.md
[Azure IoT Developer Center]: http://azure.microsoft.com/develop/iot
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-java
[obsługi błędów przejściowych]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[portalu Azure]: https://portal.azure.com
[pakiet IoT Azure]: https://azure.microsoft.com/documentation/suites/iot-suite/
[lnk-maven-service-search]: http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-service-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22