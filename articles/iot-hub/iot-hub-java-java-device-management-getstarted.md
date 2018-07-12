---
title: Wprowadzenie do zarządzania urządzeniami Azure IoT Hub (Java) | Dokumentacja firmy Microsoft
description: Jak zainicjować ponownego uruchomienia urządzenia zdalnego za pomocą zarządzania urządzeniami Azure IoT Hub. Urządzenia usługi Azure IoT SDK dla języka Java umożliwia implementowanie aplikacji symulowanego urządzenia, która obejmuje metody bezpośredniej i usługi Azure IoT SDK dla języka Java w celu zaimplementowania app service, która wywołuje metody bezpośredniej.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: conceptual
ms.date: 08/08/2017
ms.author: dobett
ms.openlocfilehash: 6a4ba8c2b88520dff028610cf64aa9b3a6e3fefd
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2018
ms.locfileid: "38481977"
---
# <a name="get-started-with-device-management-java"></a>Wprowadzenie do zarządzania urządzeniami (Java)

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

Ten samouczek przedstawia sposób wykonania następujących czynności:

* Użyj witryny Azure portal do utworzenia Centrum IoT, a następnie tworzenie tożsamości urządzenia w usłudze IoT hub.
* Tworzenie aplikacji symulowanego urządzenia, która implementuje metody bezpośredniej o ponownym uruchomieniu urządzenia. Metody bezpośrednie są wywoływane z poziomu chmury.
* Utwórz aplikację, która wywołuje metody bezpośredniej ponowny rozruch w aplikacji symulowanego urządzenia za pośrednictwem usługi IoT hub. Tej aplikacji, a następnie monitoruje zgłaszane właściwości z urządzenia, aby zobaczyć, po zakończeniu operacji ponownego uruchomienia.

Na końcu tego samouczka będziesz mieć dwie aplikacje konsolowe Java:

**Symulowane urządzenie**. Ta aplikacja:

* Nawiązuje połączenie z Centrum IoT przy użyciu utworzonej wcześniej tożsamości urządzenia.
* Odbiera wywołania metody bezpośredniej ponowny rozruch.
* Symuluje ponowne uruchomienie komputera fizycznego.
* Raporty czas ostatniego ponownego uruchomienia za pomocą zgłoszonych właściwości.

**ponowne uruchomienie wyzwalacza**. Ta aplikacja:

* Wywołuje metody bezpośredniej w symulowanej aplikacji urządzenia.
* Wyświetla odpowiedź na wywołania metody bezpośredniej wysyłane przez urządzenie symulowane
* Wyświetla zaktualizowany zgłoszonych właściwości.

> [!NOTE]
> Aby uzyskać informacje dotyczące zestawów SDK, których można tworzyć aplikacje do uruchamiania na urządzeniach i w zapleczu rozwiązania, zobacz [Azure IoT SDKs][lnk-hub-sdks].

Do ukończenia tego samouczka niezbędne są następujące elementy:

* Java SE 8. <br/> W artykule [Prepare your development environment][lnk-dev-setup] (Przygotowanie środowiska projektowego) opisano, jak zainstalować środowisko Java na potrzeby tego samouczka w systemie Windows lub Linux.
* Maven 3.  <br/> W artykule [Prepare your development environment][lnk-dev-setup] (Przygotowanie środowiska projektowego) opisano, jak zainstalować środowisko [Maven][lnk-maven] na potrzeby tego samouczka w systemie Windows lub Linux.
* [Środowisko node.js w wersji 0.10.0 lub nowszej](http://nodejs.org).

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>Zdalne ponowne uruchamianie systemu na urządzeniu, korzystając z metody bezpośredniej wyzwalacza

W tej sekcji utworzysz aplikację konsolową Java który:

1. Wywołuje metody bezpośredniej ponowny rozruch w symulowanej aplikacji urządzenia.
1. Wyświetla odpowiedzi.
1. Ankiety zgłaszanych właściwości wysyłanych z urządzenia w celu ustalenia, po zakończeniu ponownego uruchamiania.

Ta aplikacja konsolowa łączy do usługi IoT Hub wywołuje metody bezpośredniej i zgłaszanych właściwości do odczytu.

1. Utwórz pusty folder o nazwie dm-get-started.

1. W folderze dm-get-started Utwórz projekt narzędzia Maven o nazwie **ponownego uruchomienia wyzwalacza** przy użyciu następującego polecenia w wierszu polecenia. Na poniższym obrazie przedstawiono jedno długie polecenie:

    `mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=trigger-reboot -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false`

1. W wierszu polecenia przejdź do folderu ponownego uruchomienia wyzwalacza.

1. Za pomocą edytora tekstu Otwórz plik pom.xml w folderze ponownego uruchomienia wyzwalacza i dodaj następującą zależność do **zależności** węzła. Ta zależność umożliwia użycie pakietu iot-service-client w aplikacji do komunikowania się z Centrum IoT:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-service-client</artifactId>
      <version>1.7.23</version>
      <type>jar</type>
    </dependency>
    ```

    > [!NOTE]
    > Możesz sprawdzić dostępność najnowszej wersji pakietu **iot-service-client** za pomocą [funkcji wyszukiwania narzędzia Maven][lnk-maven-service-search].

1. Dodaj następujący kod **kompilacji** węzła po **zależności** węzła. Ta konfiguracja powoduje, że narzędzie Maven na potrzeby tworzenia aplikacji Java 1.8:

    ```xml
    <build>
      <plugins>
        <plugin>
          <groupId>org.apache.maven.plugins</groupId>
          <artifactId>maven-compiler-plugin</artifactId>
          <version>3.3</version>
          <configuration>
            <source>1.8</source>
            <target>1.8</target>
          </configuration>
        </plugin>
      </plugins>
    </build>
    ```

1. Zapisz i zamknij plik pom.xml.

1. Za pomocą edytora tekstu Otwórz plik źródłowy trigger-reboot\src\main\java\com\mycompany\app\App.java.

1. Dodaj do pliku następujące instrukcje **importowania**:

    ```java
    import com.microsoft.azure.sdk.iot.service.devicetwin.DeviceMethod;
    import com.microsoft.azure.sdk.iot.service.devicetwin.MethodResult;
    import com.microsoft.azure.sdk.iot.service.exceptions.IotHubException;
    import com.microsoft.azure.sdk.iot.service.devicetwin.DeviceTwin;
    import com.microsoft.azure.sdk.iot.service.devicetwin.DeviceTwinDevice;

    import java.io.IOException;
    import java.util.concurrent.TimeUnit;
    import java.util.concurrent.Executors;
    import java.util.concurrent.ExecutorService;
    ```

1. Dodaj następujące zmienne na poziomie klasy do klasy **App**. Zastąp `{youriothubconnectionstring}` parametrami połączenia Centrum IoT zanotowanym w *Tworzenie Centrum IoT* sekcji:

    ```java
    public static final String iotHubConnectionString = "{youriothubconnectionstring}";
    public static final String deviceId = "myDeviceId";

    private static final String methodName = "reboot";
    private static final Long responseTimeout = TimeUnit.SECONDS.toSeconds(30);
    private static final Long connectTimeout = TimeUnit.SECONDS.toSeconds(5);
    ```

1. Aby zaimplementować wątek, który odczytuje zgłaszane właściwości z bliźniaczej reprezentacji urządzenia, co 10 sekund, Dodaj następujące klasy zagnieżdżonej, aby **aplikacji** klasy:

    ```java
    private static class ShowReportedProperties implements Runnable {
      public void run() {
        try {
          DeviceTwin deviceTwins = DeviceTwin.createFromConnectionString(iotHubConnectionString);
          DeviceTwinDevice twinDevice = new DeviceTwinDevice(deviceId);
          while (true) {
            System.out.println("Get reported properties from device twin");
            deviceTwins.getTwin(twinDevice);
            System.out.println(twinDevice.reportedPropertiesToString());
            Thread.sleep(10000);
          }
        } catch (Exception ex) {
          System.out.println("Exception reading reported properties: " + ex.getMessage());
        }
      }
    }
    ```

1. Zmodyfikuj podpis metody **głównego** metodę, aby zgłosić następujący wyjątek:

    ```java
    public static void main(String[] args) throws IOException
    ```

1. Aby wywołać metody bezpośredniej ponowne uruchomienie symulowanego urządzenia, Dodaj następujący kod do **głównego** metody:

    ```java
    System.out.println("Starting sample...");
    DeviceMethod methodClient = DeviceMethod.createFromConnectionString(iotHubConnectionString);

    try
    {
      System.out.println("Invoke reboot direct method");
      MethodResult result = methodClient.invoke(deviceId, methodName, responseTimeout, connectTimeout, null);

      if(result == null)
      {
        throw new IOException("Invoke direct method reboot returns null");
      }
      System.out.println("Invoked reboot on device");
      System.out.println("Status for device:   " + result.getStatus());
      System.out.println("Message from device: " + result.getPayload());
    }
    catch (IotHubException e)
    {
        System.out.println(e.getMessage());
    }
    ```

1. Aby rozpocząć wątku sondowania zgłaszane właściwości symulowane urządzenia, Dodaj następujący kod do **głównego** metody:

    ```java
    ShowReportedProperties showReportedProperties = new ShowReportedProperties();
    ExecutorService executor = Executors.newFixedThreadPool(1);
    executor.execute(showReportedProperties);
    ```

1. Umożliwienie Zatrzymaj aplikację, Dodaj następujący kod do **głównego** metody:

    ```java
    System.out.println("Press ENTER to exit.");
    System.in.read();
    executor.shutdownNow();
    System.out.println("Shutting down sample...");
    ```

1. Zapisz i zamknij plik trigger-reboot\src\main\java\com\mycompany\app\App.java.

1. Tworzenie **ponownego uruchomienia wyzwalacza** aplikacji zaplecza i poprawić błędy. W wierszu polecenia przejdź do folderu, ponowne uruchomienie wyzwalacza, a następnie uruchom następujące polecenie:

    `mvn clean package -DskipTests`

## <a name="create-a-simulated-device-app"></a>Tworzenie aplikacji symulowanego urządzenia

W tej sekcji utworzysz aplikację konsolową Java, która symuluje urządzenie. Aplikacja nasłuchuje ponownego uruchamiania bezpośrednie wywołanie metody z usługi IoT hub i natychmiast reaguje na to wywołanie. Aplikacja, a następnie sen od pewnego czasu zasymulować ponowne uruchomienie procesu używa zgłaszanych właściwości do powiadamiania **ponownego uruchomienia wyzwalacza** aplikacji zaplecza, że ponowne uruchomienie zostało zakończone.

1. W folderze dm-get-started Utwórz projekt narzędzia Maven o nazwie **symulowane urządzenie** przy użyciu następującego polecenia w wierszu polecenia. Poniżej przedstawiono jedno długie polecenie:

    `mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false`

1. W wierszu polecenia przejdź do folderu simulated-device.

1. Za pomocą edytora tekstu Otwórz plik pom.xml w folderze simulated-device i dodaj następującą zależność do **zależności** węzła. Ta zależność umożliwia użycie pakietu iot-service-client w aplikacji do komunikowania się z Centrum IoT:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-device-client</artifactId>
      <version>1.3.32</version>
    </dependency>
    ```

    > [!NOTE]
    > Możesz sprawdzić dostępność najnowszej wersji pakietu **iot-device-client** za pomocą [funkcji wyszukiwania narzędzia Maven][lnk-maven-device-search].

1. Dodaj następujący kod **kompilacji** węzła po **zależności** węzła. Ta konfiguracja powoduje, że narzędzie Maven na potrzeby tworzenia aplikacji Java 1.8:

    ```xml
    <build>
      <plugins>
        <plugin>
          <groupId>org.apache.maven.plugins</groupId>
          <artifactId>maven-compiler-plugin</artifactId>
          <version>3.3</version>
          <configuration>
            <source>1.8</source>
            <target>1.8</target>
          </configuration>
        </plugin>
      </plugins>
    </build>
    ```

1. Zapisz i zamknij plik pom.xml.

1. Za pomocą edytora tekstu Otwórz plik simulated-device\src\main\java\com\mycompany\app\App.java źródła.

1. Dodaj do pliku następujące instrukcje **importowania**:

    ```java
    import com.microsoft.azure.sdk.iot.device.*;
    import com.microsoft.azure.sdk.iot.device.DeviceTwin.*;

    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.time.LocalDateTime;
    import java.util.Scanner;
    import java.util.Set;
    import java.util.HashSet;
    ```

1. Dodaj następujące zmienne na poziomie klasy do klasy **App**. Zastąp `{yourdeviceconnectionstring}` przy użyciu parametrów połączenia urządzeń wymienionych w *tworzenie tożsamości urządzenia* sekcji:

    ```java
    private static final int METHOD_SUCCESS = 200;
    private static final int METHOD_NOT_DEFINED = 404;

    private static IotHubClientProtocol protocol = IotHubClientProtocol.MQTT;
    private static String connString = "{yourdeviceconnectionstring}";
    private static DeviceClient client;
    ```

1. Aby zaimplementować Obsługa wywołania metody bezpośredniej zdarzenia stanu, Dodaj następujące klasy zagnieżdżonej, aby **aplikacji** klasy:

    ```java
    protected static class DirectMethodStatusCallback implements IotHubEventCallback
    {
      public void execute(IotHubStatusCode status, Object context)
      {
        System.out.println("IoT Hub responded to device method operation with status " + status.name());
      }
    }
    ```

1. Aby zaimplementować program obsługi wywołania zwrotnego dla zdarzeń stanu bliźniaczej reprezentacji urządzenia, należy dodać następujące klasy zagnieżdżonej, aby **aplikacji** klasy:

    ```java
    protected static class DeviceTwinStatusCallback implements IotHubEventCallback
    {
        public void execute(IotHubStatusCode status, Object context)
        {
            System.out.println("IoT Hub responded to device twin operation with status " + status.name());
        }
    }
    ```

1. Aby zaimplementować program obsługi wywołania zwrotnego dla właściwości zdarzenia, należy dodać następujące klasy zagnieżdżonej, aby **aplikacji** klasy:

    ```java
    protected static class PropertyCallback implements PropertyCallBack<String, String>
    {
      public void PropertyCall(String propertyKey, String propertyValue, Object context)
      {
        System.out.println("PropertyKey:     " + propertyKey);
        System.out.println("PropertyKvalue:  " + propertyKey);
      }
    }
    ```

1. Aby zaimplementować wątku, aby zasymulować ponowne uruchomienie urządzenia, należy dodać następujące klasy zagnieżdżonej, aby **aplikacji** klasy. Wątek jest w stanie uśpienia na pięć sekund, a następnie ustawia **lastReboot** zgłoszonych właściwości:

    ```java
    protected static class RebootDeviceThread implements Runnable {
      public void run() {
        try {
          System.out.println("Rebooting...");
          Thread.sleep(5000);
          Property property = new Property("lastReboot", LocalDateTime.now());
          Set<Property> properties = new HashSet<Property>();
          properties.add(property);
          client.sendReportedProperties(properties);
          System.out.println("Rebooted");
        }
        catch (Exception ex) {
          System.out.println("Exception in reboot thread: " + ex.getMessage());
        }
      }
    }
    ```

1. Aby zaimplementować metody bezpośredniej na urządzeniu, należy dodać następujące klasy zagnieżdżonej, aby **aplikacji** klasy. Gdy symulowane aplikacja otrzyma po wywołaniu **ponowny rozruch** metody bezpośredniej go zwraca potwierdzenia do obiektu wywołującego, a następnie uruchamia wątku w celu przetworzenia ponownego uruchamiania:

    ```java
    protected static class DirectMethodCallback implements com.microsoft.azure.sdk.iot.device.DeviceTwin.DeviceMethodCallback
    {
      @Override
      public DeviceMethodData call(String methodName, Object methodData, Object context)
      {
        DeviceMethodData deviceMethodData;
        switch (methodName)
        {
          case "reboot" :
          {
            int status = METHOD_SUCCESS;
            System.out.println("Received reboot request");
            deviceMethodData = new DeviceMethodData(status, "Started reboot");
            RebootDeviceThread rebootThread = new RebootDeviceThread();
            Thread t = new Thread(rebootThread);
            t.start();
            break;
          }
          default:
          {
            int status = METHOD_NOT_DEFINED;
            deviceMethodData = new DeviceMethodData(status, "Not defined direct method " + methodName);
          }
        }
        return deviceMethodData;
      }
    }
    ```

1. Zmodyfikuj podpis metody **głównego** metodę, aby zgłosić następujące wyjątki:

    ```java
    public static void main(String[] args) throws IOException, URISyntaxException
    ```

1. Do utworzenia wystąpienia **DeviceClient**, Dodaj następujący kod do **głównego** metody:

    ```java
    System.out.println("Starting device client sample...");
    client = new DeviceClient(connString, protocol);
    ```

1. Aby rozpocząć nasłuchiwania na wywołania metody bezpośredniej, Dodaj następujący kod do **głównego** metody:

    ```java
    try
    {
      client.open();
      client.subscribeToDeviceMethod(new DirectMethodCallback(), null, new DirectMethodStatusCallback(), null);
      client.startDeviceTwin(new DeviceTwinStatusCallback(), null, new PropertyCallback(), null);
      System.out.println("Subscribed to direct methods and polling for reported properties. Waiting...");
    }
    catch (Exception e)
    {
      System.out.println("On exception, shutting down \n" + " Cause: " + e.getCause() + " \n" +  e.getMessage());
      client.close();
      System.out.println("Shutting down...");
    }
    ```

1. Aby zamknąć symulator urządzeń, Dodaj następujący kod do **głównego** metody:

    ```java
    System.out.println("Press any key to exit...");
    Scanner scanner = new Scanner(System.in);
    scanner.nextLine();
    scanner.close();
    client.close();
    System.out.println("Shutting down...");
    ```

1. Zapisz i zamknij plik simulated-device\src\main\java\com\mycompany\app\App.java.

1. Tworzenie **symulowane urządzenie** aplikacji zaplecza i poprawić błędy. W wierszu polecenia przejdź do folderu simulated-device i uruchom następujące polecenie:

    `mvn clean package -DskipTests`

## <a name="run-the-apps"></a>Uruchamianie aplikacji

Teraz można przystąpić do uruchomienia aplikacji.

1. W wierszu polecenia w folderze simulated-device uruchom następujące polecenie, aby rozpocząć nasłuchiwania dla wywołań metod ponowny rozruch z usługi IoT hub:

    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

    ![Aplikacji symulowanego urządzenia IoT Hub dla środowiska Java do nasłuchiwania pod kątem ponowny rozruch wywołania metody bezpośredniej][1]

1. W wierszu polecenia w folderze ponownego uruchomienia wyzwalacza uruchom następujące polecenie, aby wywołać metodę ponownego uruchomienia dla symulowanego urządzenia z usługi IoT hub:

    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

    ![Aplikacja usługi IoT Hub dla środowiska Java do wywołania metody bezpośredniej ponowny rozruch][2]

1. Symulowane urządzenie odpowiada na wywołanie metody bezpośredniej ponowne uruchomienie komputera:

    ![IoT Hub dla środowiska Java symulowanej aplikacji urządzenia odpowiada na wywołanie metody bezpośredniej][3]

[!INCLUDE [iot-hub-dm-followup](../../includes/iot-hub-dm-followup.md)]

<!-- images and links -->
[1]: ./media/iot-hub-java-java-device-management-getstarted/launchsimulator.png
[2]: ./media/iot-hub-java-java-device-management-getstarted/triggerreboot.png
[3]: ./media/iot-hub-java-java-device-management-getstarted/respondtoreboot.png
<!-- Links -->

[lnk-maven]: https://maven.apache.org/what-is-maven.html

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md

[lnk-maven-service-search]: http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-service-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22
[lnk-maven-device-search]: http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-device-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22