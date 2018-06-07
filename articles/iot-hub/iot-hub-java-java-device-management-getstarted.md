---
title: Wprowadzenie do zarządzania urządzeniami Centrum IoT Azure (Java) | Dokumentacja firmy Microsoft
description: Jak używać zarządzania urządzeniami Centrum IoT Azure do zainicjowania ponownego uruchomienia urządzenia zdalnego. Przy użyciu urządzenia Azure IoT SDK dla języka Java aplikacji symulowane urządzenie, która zawiera metoda bezpośrednia i usługę Azure IoT SDK dla języka Java, aby zaimplementować aplikację usługi, która wywołuje metodę bezpośredniego.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: conceptual
ms.date: 08/08/2017
ms.author: dobett
ms.openlocfilehash: 6a4ba8c2b88520dff028610cf64aa9b3a6e3fefd
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34636192"
---
# <a name="get-started-with-device-management-java"></a>Wprowadzenie do zarządzania urządzeniami (Java)

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

Ten samouczek przedstawia sposób wykonania następujących czynności:

* Użyj portalu Azure do tworzenia Centrum IoT i tworzenia tożsamości urządzenia w Centrum IoT.
* Utwórz aplikację symulowane urządzenie, która implementuje bezpośredniego metodę ponownego uruchomienia urządzenia. Bezpośrednie metody są wywoływane z chmury.
* Utwórz aplikację, która wywołuje metodę bezpośredniego ponowny rozruch w aplikacji symulowane urządzenie za pomocą Centrum IoT. Tę aplikację, a następnie monitoruje zgłoszone właściwości z urządzenia, aby zobaczyć po zakończeniu operacji ponownego rozruchu.

Na końcu tego samouczka znajdują się dwie aplikacje konsoli Java:

**Symulowane urządzenie**. Ta aplikacja:

* Nawiązanie połączenia Centrum IoT z tożsamości urządzenia utworzony wcześniej.
* Odbiera wywołanie metody bezpośredniego ponowne uruchomienie komputera.
* Symuluje ponowny rozruch komputera fizycznego.
* Raporty czasu ostatniego ponownego uruchomienia za pośrednictwem właściwości zgłoszony.

**ponowne uruchomienie wyzwalacza**. Ta aplikacja:

* Wywołuje metodę bezpośrednio w aplikacji symulowane urządzenie.
* Wyświetla odpowiedź na wywołania metody bezpośredniego wysyłane przez symulowane urządzenie
* Wyświetla zaktualizowanego zgłosił właściwości.

> [!NOTE]
> Aby uzyskać informacji na temat zestawów SDK, które służy do tworzenia aplikacji do uruchamiania na urządzeniach i z zaplecza rozwiązania, zobacz [Azure IoT SDK][lnk-hub-sdks].

Do ukończenia tego samouczka niezbędne są następujące elementy:

* Java SE 8. <br/> W artykule [Prepare your development environment][lnk-dev-setup] (Przygotowanie środowiska projektowego) opisano, jak zainstalować środowisko Java na potrzeby tego samouczka w systemie Windows lub Linux.
* Maven 3.  <br/> W artykule [Prepare your development environment][lnk-dev-setup] (Przygotowanie środowiska projektowego) opisano, jak zainstalować środowisko [Maven][lnk-maven] na potrzeby tego samouczka w systemie Windows lub Linux.
* [Środowisko node.js w wersji 0.10.0 lub nowszym](http://nodejs.org).

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>Wyzwalacz zdalnego ponownego uruchomienia na urządzeniu, za pomocą bezpośrednich — metoda

W tej sekcji tworzenia aplikacji konsoli Java który:

1. Wywołuje metodę bezpośredniego ponowny rozruch w aplikacji symulowane urządzenie.
1. Wyświetla odpowiedzi.
1. Ankiety zgłoszone właściwości wysyłane z urządzenia, aby określić, po zakończeniu ponownego rozruchu.

Łączy się Centrum IoT do wywołania metody bezpośredniego i odczytu zgłoszone właściwości z tej aplikacji konsoli.

1. Utwórz pusty folder o nazwie dm-get-started.

1. W folderze dm-get-started Utwórz projekt Maven o nazwie **ponowne uruchomienie wyzwalacza** za pomocą następującego polecenia z wiersza polecenia. Poniżej przedstawiono polecenia jednej, długa:

    `mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=trigger-reboot -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false`

1. Wierszu polecenia przejdź do folderu, ponowne uruchomienie wyzwalacza.

1. Za pomocą edytora tekstu, otwórz plik pom.xml w folderze ponowne uruchomienie wyzwalacza i dodaj następujące zależności do **zależności** węzła. Ta zależność umożliwia za pomocą pakietu iot usługi klienta w aplikacji do komunikowania się z Centrum IoT:

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

1. Dodaj następujące **kompilacji** węzła po **zależności** węzła. Ta konfiguracja powoduje, że Maven na potrzeby tworzenia aplikacji Java 1.8:

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

1. Za pomocą edytora tekstu, otwórz plik źródłowy trigger-reboot\src\main\java\com\mycompany\app\App.java.

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

1. Dodaj następujące zmienne na poziomie klasy do klasy **App**. Zastąp `{youriothubconnectionstring}` z wymienionych w ciągu połączenia Centrum IoT *tworzenia Centrum IoT* sekcji:

    ```java
    public static final String iotHubConnectionString = "{youriothubconnectionstring}";
    public static final String deviceId = "myDeviceId";

    private static final String methodName = "reboot";
    private static final Long responseTimeout = TimeUnit.SECONDS.toSeconds(30);
    private static final Long connectTimeout = TimeUnit.SECONDS.toSeconds(5);
    ```

1. Aby zaimplementować wątku, który odczytuje właściwości zgłoszony z dwie urządzenia co 10 sekund, Dodaj następujące zagnieżdżona klasa na **aplikacji** klasy:

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

1. Modyfikowanie podpis **głównego** metody ma zostać zgłoszony następujący wyjątek:

    ```java
    public static void main(String[] args) throws IOException
    ```

1. Aby wywołać metody bezpośredniego ponownego uruchomienia dla symulowane urządzenie, Dodaj następujący kod, aby **głównego** metody:

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

1. Można uruchomić wątku sondowanie zgłoszone właściwości z symulowane urządzenie, Dodaj następujący kod do **głównego** metody:

    ```java
    ShowReportedProperties showReportedProperties = new ShowReportedProperties();
    ExecutorService executor = Executors.newFixedThreadPool(1);
    executor.execute(showReportedProperties);
    ```

1. Aby umożliwić Zatrzymaj aplikację, Dodaj następujący kod do **głównego** metody:

    ```java
    System.out.println("Press ENTER to exit.");
    System.in.read();
    executor.shutdownNow();
    System.out.println("Shutting down sample...");
    ```

1. Zapisz i zamknij plik trigger-reboot\src\main\java\com\mycompany\app\App.java.

1. Tworzenie **ponowne uruchomienie wyzwalacza** aplikacji zaplecza i poprawić błędy. Wierszu polecenia przejdź do folderu, ponowne uruchomienie wyzwalacza i uruchom następujące polecenie:

    `mvn clean package -DskipTests`

## <a name="create-a-simulated-device-app"></a>Tworzenie aplikacji symulowanego urządzenia

W tej sekcji utworzysz aplikację konsoli języka Java, która symuluje urządzenia. Aplikacja nasłuchuje ponownego uruchamiania bezpośrednie wywołanie metody z Centrum IoT i natychmiast odpowiada tego wywołania. Aplikacji, a następnie sen przez pewien czas symulować proces ponownego uruchomienia przed użyciem zgłoszony właściwość do powiadamiania **ponowne uruchomienie wyzwalacza** aplikacji zaplecza zakończeniu ponownego rozruchu.

1. W folderze dm-get-started Utwórz projekt Maven o nazwie **symulowane urządzenie** przy użyciu następującego polecenia z wiersza polecenia. Poniżej przedstawiono polecenia jednej, długa:

    `mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false`

1. W wierszu polecenia przejdź do folderu simulated-device.

1. Za pomocą edytora tekstu, otwórz plik pom.xml w folderze symulowane urządzenie i dodaj następujące zależności do **zależności** węzła. Ta zależność umożliwia za pomocą pakietu iot usługi klienta w aplikacji do komunikowania się z Centrum IoT:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-device-client</artifactId>
      <version>1.3.32</version>
    </dependency>
    ```

    > [!NOTE]
    > Możesz sprawdzić dostępność najnowszej wersji pakietu **iot-device-client** za pomocą [funkcji wyszukiwania narzędzia Maven][lnk-maven-device-search].

1. Dodaj następujące **kompilacji** węzła po **zależności** węzła. Ta konfiguracja powoduje, że Maven na potrzeby tworzenia aplikacji Java 1.8:

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

1. Za pomocą edytora tekstu, otwórz plik źródłowy simulated-device\src\main\java\com\mycompany\app\App.java.

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

1. Dodaj następujące zmienne na poziomie klasy do klasy **App**. Zastąp `{yourdeviceconnectionstring}` zanotowaną w ciągu połączenia urządzenia *tworzenie tożsamości urządzenia* sekcji:

    ```java
    private static final int METHOD_SUCCESS = 200;
    private static final int METHOD_NOT_DEFINED = 404;

    private static IotHubClientProtocol protocol = IotHubClientProtocol.MQTT;
    private static String connString = "{yourdeviceconnectionstring}";
    private static DeviceClient client;
    ```

1. Do implementowania obsługi wywołania zwrotnego, metoda bezpośrednia zdarzeń stanu, Dodaj następujące zagnieżdżona klasa na **aplikacji** klasy:

    ```java
    protected static class DirectMethodStatusCallback implements IotHubEventCallback
    {
      public void execute(IotHubStatusCode status, Object context)
      {
        System.out.println("IoT Hub responded to device method operation with status " + status.name());
      }
    }
    ```

1. Aby zaimplementować program obsługi wywołania zwrotnego dla zdarzeń stanu dwie urządzenia, Dodaj następujące zagnieżdżona klasa na **aplikacji** klasy:

    ```java
    protected static class DeviceTwinStatusCallback implements IotHubEventCallback
    {
        public void execute(IotHubStatusCode status, Object context)
        {
            System.out.println("IoT Hub responded to device twin operation with status " + status.name());
        }
    }
    ```

1. Aby zaimplementować obsługi wywołania zwrotnego dla właściwości zdarzenia, Dodaj następujące zagnieżdżona klasa na **aplikacji** klasy:

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

1. Aby zaimplementować wątek, aby zasymulować ponowne uruchomienie urządzenia, Dodaj następujące zagnieżdżona klasa na **aplikacji** klasy. Wątek jest w stanie uśpienia przez pięć sekund, a następnie ustawia **lastReboot** zgłoszonych właściwości:

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

1. Aby zaimplementować metodę bezpośrednio na urządzeniu, Dodaj następujące zagnieżdżona klasa na **aplikacji** klasy. Gdy symulowane aplikacja odbiera wywołanie **ponowny rozruch** metoda bezpośrednia go zwraca potwierdzenia do obiektu wywołującego, a następnie uruchamia wątku w celu przetworzenia ponownego rozruchu:

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

1. Modyfikowanie podpis **głównego** metodę, aby zgłosić następujące wyjątki:

    ```java
    public static void main(String[] args) throws IOException, URISyntaxException
    ```

1. Można utworzyć wystąpienia **DeviceClient**, Dodaj następujący kod do **głównego** metody:

    ```java
    System.out.println("Starting device client sample...");
    client = new DeviceClient(connString, protocol);
    ```

1. Aby rozpocząć nasłuchiwania dla wywołań metod bezpośredniego, Dodaj następujący kod do **głównego** metody:

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

1. Tworzenie **symulowane urządzenie** aplikacji zaplecza i poprawić błędy. Wierszu polecenia przejdź do folderu symulowane urządzenie, a następnie uruchom następujące polecenie:

    `mvn clean package -DskipTests`

## <a name="run-the-apps"></a>Uruchamianie aplikacji

Teraz można przystąpić do uruchomienia aplikacji.

1. W wierszu polecenia w folderze symulowane urządzenie uruchom następujące polecenie, aby rozpocząć nasłuchiwania dla wywołań metod ponowny rozruch z Centrum IoT:

    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

    ![Centrum IoT Java symulowane urządzenie aplikacji do nasłuchiwania ponowny rozruch połączeń bezpośrednich — metoda][1]

1. W wierszu polecenia w folderze wyzwalacza — ponowne uruchomienie komputera uruchom następujące polecenie, aby wywołać metodę ponownego uruchomienia na symulowane urządzenie z Centrum IoT:

    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

    ![Java Centrum IoT usługi aplikacji, aby wywołać metodę bezpośredniego ponowny rozruch][2]

1. Symulowane urządzenie odpowiada na wywołanie metody bezpośredniego ponownego rozruchu:

    ![Centrum IoT Java symulowane urządzenie aplikacji odpowiada na wywołanie bezpośrednie — metoda][3]

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