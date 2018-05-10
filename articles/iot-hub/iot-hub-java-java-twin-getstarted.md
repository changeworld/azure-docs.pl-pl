---
title: Rozpoczynanie pracy z Centrum IoT Azure urządzenia twins (Java) | Dokumentacja firmy Microsoft
description: Jak używać twins urządzenia Azure IoT Hub Dodawanie tagów, a następnie użyć kwerendy Centrum IoT. Przy użyciu urządzenia Azure IoT SDK dla języka Java aplikacją urządzenia i usługi Azure IoT SDK dla języka Java, aby zaimplementować aplikację usługi, która dodaje znaczniki i uruchamia kwerendy Centrum IoT.
services: iot-hub
documentationcenter: java
author: dominicbetts
manager: timlt
editor: ''
ms.service: iot-hub
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/04/2017
ms.author: dobett
ms.openlocfilehash: 8611b55f6e4f33f8946d8c7c481163166d24049c
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="get-started-with-device-twins-java"></a>Rozpoczynanie pracy z urządzenia twins (Java)

[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

W tym samouczku można utworzyć dwie aplikacje konsoli Java:

* **Dodaj tag query**, aplikacji zaplecza Java, która dodaje znaczniki i zapytanie twins urządzenia.
* **Symulowane urządzenie**, aplikacji urządzenia Java, która łączy się z Centrum IoT i raporty łączność warunku, za pomocą właściwości zgłoszony.

> [!NOTE]
> Artykuł [Azure IoT SDK](iot-hub-devguide-sdks.md) informacje na temat zestawów SDK IoT Azure można tworzyć aplikacje zarówno urządzenia, jak i zaplecza.

Do ukończenia tego samouczka niezbędne są następujące elementy:

* Najnowszy zestaw [Java SE Development Kit 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
* [Maven 3](https://maven.apache.org/install.html)
* Aktywne konto platformy Azure. (Jeśli nie masz konta, możesz utworzyć [bezpłatne konto](http://azure.microsoft.com/pricing/free-trial/) w zaledwie kilka minut.)

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity-portal](../../includes/iot-hub-get-started-create-device-identity-portal.md)]

## <a name="create-the-service-app"></a>Tworzenie aplikacji usługi

W tej sekcji Tworzenie aplikacji Java, która dodaje lokalizacji metadanych jako tag, aby dwie urządzenie w Centrum IoT powiązanych z **myDeviceId**. Aplikacja wysyła zapytanie najpierw Centrum IoT dla urządzeń znajdujących się w Stanach Zjednoczonych, a następnie dla urządzeń, które zgłosiły połączenia sieci komórkowej.

1. Na komputerze deweloperskim, należy utworzyć pusty folder o nazwie `iot-java-twin-getstarted`.

1. W `iot-java-twin-getstarted` folderu, Utwórz projekt Maven o nazwie **Dodaj tag query** za pomocą następującego polecenia z wiersza polecenia. Zwróć uwagę, że jest to jedno długie polecenie:

    `mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=add-tags-query -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false`

1. Wierszu polecenia przejdź do `add-tags-query` folderu.

1. Za pomocą edytora tekstu, otwórz `pom.xml` w pliku `add-tags-query` folderu i dodaj następujące zależności do **zależności** węzła. Ta zależność pozwala na użycie **klienta w przypadku usługi iot** pakietu aplikacji w celu komunikowania się z Centrum IoT:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-service-client</artifactId>
      <version>1.7.23</version>
      <type>jar</type>
    </dependency>
    ```

    > [!NOTE]
    > Najnowszą wersję można sprawdzić **klienta w przypadku usługi iot** przy użyciu [wyszukiwania Maven](http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-service-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22).

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

1. Zapisz i Zamknij `pom.xml` pliku.

1. Za pomocą edytora tekstu, otwórz `add-tags-query\src\main\java\com\mycompany\app\App.java` pliku.

1. Dodaj do pliku następujące instrukcje **importowania**:

    ```java
    import com.microsoft.azure.sdk.iot.service.devicetwin.*;
    import com.microsoft.azure.sdk.iot.service.exceptions.IotHubException;

    import java.io.IOException;
    import java.util.HashSet;
    import java.util.Set;
    ```

1. Dodaj następujące zmienne na poziomie klasy do klasy **App**. Zastąp `{youriothubconnectionstring}` z wymienionych w ciągu połączenia Centrum IoT *tworzenia Centrum IoT* sekcji:

    ```java
    public static final String iotHubConnectionString = "{youriothubconnectionstring}";
    public static final String deviceId = "myDeviceId";

    public static final String region = "US";
    public static final String plant = "Redmond43";
    ```

1. Aktualizacja **głównego** podpis metody, aby uwzględnić następujące `throws` klauzuli:

    ```java
    public static void main( String[] args ) throws IOException
    ```

1. Dodaj następujący kod do **głównego** metodę w celu utworzenia **DeviceTwin** i **DeviceTwinDevice** obiektów. **DeviceTwin** obiektu obsługuje komunikację z Centrum IoT. **DeviceTwinDevice** obiekt reprezentuje dwie urządzenia z jego właściwości i tagi:

    ```java
    // Get the DeviceTwin and DeviceTwinDevice objects
    DeviceTwin twinClient = DeviceTwin.createFromConnectionString(iotHubConnectionString);
    DeviceTwinDevice device = new DeviceTwinDevice(deviceId);
    ```

1. Dodaj następujące `try/catch` za pomocą bloku **głównego** metody:

    ```java
    try {
      // Code goes here
    } catch (IotHubException e) {
      System.out.println(e.getMessage());
    } catch (IOException e) {
      System.out.println(e.getMessage());
    }
    ```

1. Aby zaktualizować **region** i **roślin** tagi dwie urządzenia w sieci dwie urządzenia, Dodaj następujący kod w `try` bloku:

    ```java
    // Get the device twin from IoT Hub
    System.out.println("Device twin before update:");
    twinClient.getTwin(device);
    System.out.println(device);

    // Update device twin tags if they are different
    // from the existing values
    String currentTags = device.tagsToString();
    if ((!currentTags.contains("region=" + region) && !currentTags.contains("plant=" + plant))) {
      // Create the tags and attach them to the DeviceTwinDevice object
      Set<Pair> tags = new HashSet<Pair>();
      tags.add(new Pair("region", region));
      tags.add(new Pair("plant", plant));
      device.setTags(tags);

      // Update the device twin in IoT Hub
      System.out.println("Updating device twin");
      twinClient.updateTwin(device);
    }

    // Retrieve the device twin with the tag values from IoT Hub
    System.out.println("Device twin after update:");
    twinClient.getTwin(device);
    System.out.println(device);
    ```

1. Aby odpytać twins urządzenie w Centrum IoT, Dodaj następujący kod do `try` blok po kodzie dodanym w poprzednim kroku. Kod działa na dwa zapytania. Każde zapytanie zwraca maksymalnie 100 urządzeń:

    ```java
    // Query the device twins in IoT Hub
    System.out.println("Devices in Redmond:");

    // Construct the query
    SqlQuery sqlQuery = SqlQuery.createSqlQuery("*", SqlQuery.FromType.DEVICES, "tags.plant='Redmond43'", null);

    // Run the query, returning a maximum of 100 devices
    Query twinQuery = twinClient.queryTwin(sqlQuery.getQuery(), 100);
    while (twinClient.hasNextDeviceTwin(twinQuery)) {
      DeviceTwinDevice d = twinClient.getNextDeviceTwin(twinQuery);
      System.out.println(d.getDeviceId());
    }

    System.out.println("Devices in Redmond using a cellular network:");

    // Construct the query
    sqlQuery = SqlQuery.createSqlQuery("*", SqlQuery.FromType.DEVICES, "tags.plant='Redmond43' AND properties.reported.connectivityType = 'cellular'", null);

    // Run the query, returning a maximum of 100 devices
    twinQuery = twinClient.queryTwin(sqlQuery.getQuery(), 3);
    while (twinClient.hasNextDeviceTwin(twinQuery)) {
      DeviceTwinDevice d = twinClient.getNextDeviceTwin(twinQuery);
      System.out.println(d.getDeviceId());
    }
    ```

1. Zapisz i Zamknij `add-tags-query\src\main\java\com\mycompany\app\App.java` pliku

1. Tworzenie **Dodaj tag query** aplikacji i poprawić błędy. Wierszu polecenia przejdź do `add-tags-query` folderu i uruchom następujące polecenie:

    `mvn clean package -DskipTests`

## <a name="create-a-device-app"></a>Tworzenie aplikacji urządzenia

W tej sekcji utworzysz aplikację konsoli języka Java, która ustawia wartości właściwości zgłoszone wysyłany do Centrum IoT.

1. W `iot-java-twin-getstarted` folderu, Utwórz projekt Maven o nazwie **symulowane urządzenie** przy użyciu następującego polecenia z wiersza polecenia. Zwróć uwagę, że jest to jedno długie polecenie:

    `mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false`

1. Wierszu polecenia przejdź do `simulated-device` folderu.

1. Za pomocą edytora tekstu, otwórz `pom.xml` w pliku `simulated-device` folderu i dodaj następujące zależności, aby **zależności** węzła. Ta zależność pozwala na użycie **klienta w przypadku urządzenia iot** pakietu aplikacji w celu komunikowania się z Centrum IoT:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-device-client</artifactId>
      <version>1.3.32</version>
    </dependency>
    ```

    > [!NOTE]
    > Najnowszą wersję można sprawdzić **klienta w przypadku urządzenia iot** przy użyciu [wyszukiwania Maven](http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-device-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22).

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

1. Zapisz i Zamknij `pom.xml` pliku.

1. Za pomocą edytora tekstu, otwórz `simulated-device\src\main\java\com\mycompany\app\App.java` pliku.

1. Dodaj do pliku następujące instrukcje **importowania**:

    ```java
    import com.microsoft.azure.sdk.iot.device.*;
    import com.microsoft.azure.sdk.iot.device.DeviceTwin.*;

    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.util.Scanner;
    ```

1. Dodaj następujące zmienne na poziomie klasy do klasy **App**. Zastępowanie `{youriothubname}` z nazwę Centrum IoT i `{yourdevicekey}` kluczem urządzenia wartość, zostanie wygenerowany w *tworzenie tożsamości urządzenia* sekcji:

    ```java
    private static String connString = "HostName={youriothubname}.azure-devices.net;DeviceId=myDeviceID;SharedAccessKey={yourdevicekey}";
    private static IotHubClientProtocol protocol = IotHubClientProtocol.MQTT;
    private static String deviceId = "myDeviceId";
    ```

    Ta przykładowa aplikacja używa zmiennej **protocol** podczas tworzenia wystąpienia obiektu **DeviceClient**. 

1. Dodaj następujący kod do **głównego** metodę:
    * Utwórz klienta urządzenia do komunikowania się z Centrum IoT.
    * Utwórz **urządzenia** obiekt, aby zapisać właściwości dwie urządzenia.

    ```java
    DeviceClient client = new DeviceClient(connString, protocol);

    // Create a Device object to store the device twin properties
    Device dataCollector = new Device() {
      // Print details when a property value changes
      @Override
      public void PropertyCall(String propertyKey, Object propertyValue, Object context) {
        System.out.println(propertyKey + " changed to " + propertyValue);
      }
    };
    ```

1. Dodaj następujący kod do **głównego** metodę w celu utworzenia **connectivityType** zgłosił właściwości i wysyłania go do Centrum IoT:

    ```java
    try {
      // Open the DeviceClient and start the device twin services.
      client.open();
      client.startDeviceTwin(new DeviceTwinStatusCallBack(), null, dataCollector, null);

      // Create a reported property and send it to your IoT hub.
      dataCollector.setReportedProp(new Property("connectivityType", "cellular"));
      client.sendReportedProperties(dataCollector.getReportedProp());
    }
    catch (Exception e) {
      System.out.println("On exception, shutting down \n" + " Cause: " + e.getCause() + " \n" + e.getMessage());
      dataCollector.clean();
      client.close();
      System.out.println("Shutting down...");
    }
    ```

1. Dodaj następujący kod na końcu **głównego** metody. Oczekiwanie na **Enter** klucz umożliwia czas Centrum IoT do raportowania stanu operacji dwie urządzenia:

    ```java
    System.out.println("Press any key to exit...");

    Scanner scanner = new Scanner(System.in);
    scanner.nextLine();

    dataCollector.clean();
    client.close();
    ```

1. Zapisz i Zamknij `simulated-device\src\main\java\com\mycompany\app\App.java` pliku.

1. Tworzenie **symulowane urządzenie** aplikacji i poprawić błędy. Wierszu polecenia przejdź do `simulated-device` folderu i uruchom następujące polecenie:

    `mvn clean package -DskipTests`

## <a name="run-the-apps"></a>Uruchamianie aplikacji

Teraz można przystąpić do uruchomienia aplikacji konsoli.

1. W wierszu polecenia `add-tags-query` folderu, uruchom następujące polecenie, aby uruchomić **Dodaj tag query** usługi aplikacji:

    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

    ![Centrum IoT Java usługi aplikacji, aby zaktualizować wartości tagów i uruchamianie zapytań urządzenia](media/iot-hub-java-java-twin-getstarted/service-app-1.png)

    Widać **roślin** i **region** tagi dodane do dwie urządzenia. Pierwsza kwerenda zwraca urządzenia, ale druga nie.

1. W wierszu polecenia `simulated-device` folderu, uruchom następujące polecenie, aby dodać **connectivityType** zgłoszonych właściwości dwie urządzenia:

    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

    ![Dodaje klienta urządzenia ** connectivityType ** zgłosił właściwości](media/iot-hub-java-java-twin-getstarted/device-app-1.png)

1. W wierszu polecenia `add-tags-query` folderu, uruchom następujące polecenie, aby uruchomić **Dodaj tag query** usługi aplikacji po raz drugi:

    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

    ![Centrum IoT Java usługi aplikacji, aby zaktualizować wartości tagów i uruchamianie zapytań urządzenia](media/iot-hub-java-java-twin-getstarted/service-app-2.png)

    Teraz wysłał urządzenia **connectivityType** właściwości Centrum IoT, drugie zapytanie zwraca urządzenie.

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku opisano konfigurowanie nowego centrum IoT Hub w witrynie Azure Portal, a następnie tworzenie tożsamości urządzenia w rejestrze tożsamości centrum. Dodaje metadane urządzenia jako tagi z aplikacji zaplecza i zapisano aplikacji urządzenia informacji w raporcie urządzenia łączności w dwie urządzenia. Przedstawiono również sposób zbadać informacji dwie urządzenia przy użyciu języka przypominającego SQL Centrum IoT zapytania.

Użyj następujących zasobów, aby dowiedzieć się, jak:

* Wysyłanie danych telemetrycznych z urządzenia z [Rozpoczynanie pracy z Centrum IoT](iot-hub-java-java-getstarted.md) samouczka.
* Kontrolowanie urządzenia interakcyjne (takich jak włączanie wentylator z aplikacji kontrolowane przez użytkownika) z [metody bezpośredniego](iot-hub-java-java-direct-methods.md) samouczka.

<!-- Images. -->
[7]: ./media/iot-hub-java-java-twin-getstarted/invoke-method.png
[8]: ./media/iot-hub-java-java-twin-getstarted/device-listen.png
[9]: ./media/iot-hub-java-java-twin-getstarted/device-respond.png

<!-- Links -->
[lnk-hub-sdks]: iot-hub-devguide-sdks.md
