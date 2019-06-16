---
title: Planowanie zadań za pomocą usługi Azure IoT Hub (Java) | Dokumentacja firmy Microsoft
description: Jak zaplanować zadanie usługi Azure IoT Hub wywołuje metody bezpośredniej i ustawić żądaną właściwość na wielu urządzeniach. Urządzenia usługi Azure IoT SDK dla języka Java umożliwia wdrożenie aplikacji symulowanego urządzenia i usługi Azure IoT SDK dla języka Java do zaimplementowania app service, aby uruchomić zadanie.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: conceptual
ms.date: 07/10/2017
ms.openlocfilehash: ce7c70eef2d030a956ca5cc1ea85aff008074edb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64572464"
---
# <a name="schedule-and-broadcast-jobs-java"></a>Planowanie i emitowanie zadań (Java)

[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

Za pomocą usługi Azure IoT Hub planować i śledzić zadania, które aktualizują milionów urządzeń. Używanie zadań do:

* Aktualizowanie żądanych właściwości
* Aktualizacji tagów
* Wywoływanie metod bezpośrednich

Zadanie opakowuje jedną z następujących czynności i śledzenie wykonywanie kodu na zestawie urządzeń. Zapytań bliźniaczych reprezentacji urządzeń definiuje zbiór urządzeń, które zadania wykonuje względem. Na przykład do wywołania metody bezpośredniej na 10 000 urządzeń, który wykonuje ponowny rozruch urządzenia zadań można użyć aplikacji zaplecza. Określ zbiór urządzeń przy użyciu zapytań bliźniaczych reprezentacji urządzeń i zaplanować zadania do uruchomienia w przyszłości. Postęp śledzi zadania, ponieważ do każdego urządzenia otrzymują i wykonaj ponowny rozruch metody bezpośredniej.

Aby dowiedzieć się więcej na temat każdego z tych funkcji, zobacz:

* Bliźniacza reprezentacja urządzenia i właściwości: [Wprowadzenie do zarządzania bliźniaczymi reprezentacjami urządzeń](iot-hub-java-java-twin-getstarted.md)

* Metody bezpośrednie: [Usługi IoT Hub developer guide - metod bezpośrednich](iot-hub-devguide-direct-methods.md) i [samouczka: Używanie metod bezpośrednich](quickstart-control-device-java.md)

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Ten samouczek przedstawia sposób wykonania następujących czynności:

* Tworzenie aplikacji urządzenia, która implementuje metodę bezpośrednią wywołaną **lockDoor**. W aplikacji urządzenia funkcję również odbiera zmiany żądanych właściwości z aplikacji zaplecza.

* Tworzenie aplikacji zaplecza, która tworzy zadanie, aby wywołać **lockDoor** metoda bezpośrednia na wielu urządzeniach. Inne zadanie wysyła aktualizacje żądanych właściwości na wielu urządzeniach.

Na końcu tego samouczka masz urządzenie aplikację konsolową java i serwer zaplecza w aplikacji konsolowej java:

**Symulowane urządzenie** łączący się z Centrum IoT hub, implementuje **lockDoor** bezpośrednie metody i uchwytów żądane zmiany właściwości.

**Planowanie zadań** , użyć zadania do wywołania **lockDoor** metoda bezpośrednia i zaktualizuj odpowiednich właściwości bliźniaka urządzenia na wielu urządzeniach.

> [!NOTE]
> Artykuł [Azure IoT SDKs](iot-hub-devguide-sdks.md) informacje dotyczące zestawów SDK usługi Azure IoT, w której można tworzyć aplikacje zarówno w przypadku urządzeń, jak i zaplecza.

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka niezbędne są następujące elementy:

* Najnowszy zestaw [Java SE Development Kit 8](https://aka.ms/azure-jdks)

* [Maven 3](https://maven.apache.org/install.html)

* Aktywne konto platformy Azure. (Jeśli nie masz konta, możesz utworzyć [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/) w zaledwie kilka minut.)

## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

### <a name="retrieve-connection-string-for-iot-hub"></a>Pobieranie parametrów połączenia dla centrum IoT

[!INCLUDE [iot-hub-include-find-connection-string](../../includes/iot-hub-include-find-connection-string.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Rejestrowanie nowego urządzenia w usłudze IoT hub

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

Można również użyć [rozszerzenia IoT dla interfejsu wiersza polecenia platformy Azure](https://github.com/Azure/azure-iot-cli-extension) narzędzie, aby dodać urządzenie do Centrum IoT hub.

## <a name="create-the-service-app"></a>Tworzenie aplikacji usługi

W tej sekcji utworzysz aplikację konsolową Java, używającej zadań:

* Wywołaj **lockDoor** metoda bezpośrednia na wielu urządzeniach.

* Wyślij żądanych właściwości na wielu urządzeniach.

Aby utworzyć aplikację:

1. Na komputerze deweloperskim, Utwórz pusty folder o nazwie `iot-java-schedule-jobs`.

2. W `iot-java-schedule-jobs` folderu, Utwórz projekt narzędzia Maven o nazwie **Planowanie zadań** przy użyciu następującego polecenia w wierszu polecenia. Zwróć uwagę, że jest to jedno długie polecenie:

    `mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=schedule-jobs -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false`

3. W wierszu polecenia przejdź do `schedule-jobs` folderu.

4. Za pomocą edytora tekstów otwórz `pom.xml` w pliku `schedule-jobs` folderze i dodaj następującą zależność do **zależności** węzła. Ta zależność umożliwia użycie **iot-service-client** pakietu w aplikacji do komunikowania się z Centrum IoT:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-service-client</artifactId>
      <version>1.7.23</version>
      <type>jar</type>
    </dependency>
    ```

    > [!NOTE]
    > Możesz sprawdzić dostępność najnowszej wersji **iot-service-client** przy użyciu [funkcji wyszukiwania narzędzia Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-service-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22).

5. Dodaj następujący kod **kompilacji** węzła po **zależności** węzła. Ta konfiguracja powoduje, że narzędzie Maven na potrzeby tworzenia aplikacji Java 1.8:

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

6. Zapisz i Zamknij `pom.xml` pliku.

7. Za pomocą edytora tekstów otwórz `schedule-jobs\src\main\java\com\mycompany\app\App.java` pliku.

8. Dodaj do pliku następujące instrukcje **importowania**:

    ```java
    import com.microsoft.azure.sdk.iot.service.devicetwin.DeviceTwinDevice;
    import com.microsoft.azure.sdk.iot.service.devicetwin.Pair;
    import com.microsoft.azure.sdk.iot.service.devicetwin.Query;
    import com.microsoft.azure.sdk.iot.service.devicetwin.SqlQuery;
    import com.microsoft.azure.sdk.iot.service.jobs.JobClient;
    import com.microsoft.azure.sdk.iot.service.jobs.JobResult;
    import com.microsoft.azure.sdk.iot.service.jobs.JobStatus;

    import java.util.Date;
    import java.time.Instant;
    import java.util.HashSet;
    import java.util.Set;
    import java.util.UUID;
    ```

9. Dodaj następujące zmienne na poziomie klasy do klasy **App**. Zastąp `{youriothubconnectionstring}` parametrami połączenia Centrum IoT zanotowanym w *Tworzenie Centrum IoT* sekcji:

    ```java
    public static final String iotHubConnectionString = "{youriothubconnectionstring}";
    public static final String deviceId = "myDeviceId";

    // How long the job is permitted to run without
    // completing its work on the set of devices
    private static final long maxExecutionTimeInSeconds = 30;
    ```

10. Dodaj następującą metodę do **aplikacji** klasy, aby zaplanować zadanie, aby zaktualizować **budynku** i **Floor** żądane właściwości w bliźniaczej reprezentacji urządzenia:

    ```java
    private static JobResult scheduleJobSetDesiredProperties(JobClient jobClient, String jobId) {
      DeviceTwinDevice twin = new DeviceTwinDevice(deviceId);
      Set<Pair> desiredProperties = new HashSet<Pair>();
      desiredProperties.add(new Pair("Building", 43));
      desiredProperties.add(new Pair("Floor", 3));
      twin.setDesiredProperties(desiredProperties);
      // Optimistic concurrency control
      twin.setETag("*");

      // Schedule the update twin job to run now
      // against a single device
      System.out.println("Schedule job " + jobId + " for device " + deviceId);
      try {
        JobResult jobResult = jobClient.scheduleUpdateTwin(jobId, 
          "deviceId='" + deviceId + "'",
          twin,
          new Date(),
          maxExecutionTimeInSeconds);
        return jobResult;
      } catch (Exception e) {
        System.out.println("Exception scheduling desired properties job: " + jobId);
        System.out.println(e.getMessage());
        return null;
      }
    }
    ```

11. Aby zaplanować zadanie, aby wywołać **lockDoor** metody, dodaj następującą metodę do **aplikacji** klasy:

    ```java
    private static JobResult scheduleJobCallDirectMethod(JobClient jobClient, String jobId) {
      // Schedule a job now to call the lockDoor direct method
      // against a single device. Response and connection
      // timeouts are set to 5 seconds.
      System.out.println("Schedule job " + jobId + " for device " + deviceId);
      try {
        JobResult jobResult = jobClient.scheduleDeviceMethod(jobId,
          "deviceId='" + deviceId + "'",
          "lockDoor",
          5L, 5L, null,
          new Date(),
          maxExecutionTimeInSeconds);
        return jobResult;
      } catch (Exception e) {
        System.out.println("Exception scheduling direct method job: " + jobId);
        System.out.println(e.getMessage());
        return null;
      }
    };
    ```

12. Aby monitorować zadanie, dodaj następującą metodę do **aplikacji** klasy:

    ```java
    private static void monitorJob(JobClient jobClient, String jobId) {
      try {
        JobResult jobResult = jobClient.getJob(jobId);
        if(jobResult == null)
        {
          System.out.println("No JobResult for: " + jobId);
          return;
        }
        // Check the job result until it's completed
        while(jobResult.getJobStatus() != JobStatus.completed)
        {
          Thread.sleep(100);
          jobResult = jobClient.getJob(jobId);
          System.out.println("Status " + jobResult.getJobStatus() + " for job " + jobId);
        }
        System.out.println("Final status " + jobResult.getJobStatus() + " for job " + jobId);
      } catch (Exception e) {
        System.out.println("Exception monitoring job: " + jobId);
        System.out.println(e.getMessage());
        return;
      }
    }
    ```

13. Aby zbadać szczegóły uruchomienia zadania, dodaj następującą metodę:

    ```java
    private static void queryDeviceJobs(JobClient jobClient, String start) throws Exception {
      System.out.println("\nQuery device jobs since " + start);

      // Create a jobs query using the time the jobs started
      Query deviceJobQuery = jobClient
          .queryDeviceJob(SqlQuery.createSqlQuery("*", SqlQuery.FromType.JOBS, "devices.jobs.startTimeUtc > '" + start + "'", null).getQuery());

      // Iterate over the list of jobs and print the details
      while (jobClient.hasNextJob(deviceJobQuery)) {
        System.out.println(jobClient.getNextJob(deviceJobQuery));
      }
    }
    ```

14. Aktualizacja **głównego** podpis metody, aby uwzględnić następujące `throws` klauzuli:

    ```java
    public static void main( String[] args ) throws Exception
    ```

15. Aby uruchomić i monitorować dwa zadania po kolei, Dodaj następujący kod do **głównego** metody:

    ```java
    // Record the start time
    String start = Instant.now().toString();

    // Create JobClient
    JobClient jobClient = JobClient.createFromConnectionString(iotHubConnectionString);
    System.out.println("JobClient created with success");

    // Schedule twin job desired properties
    // Maximum concurrent jobs is 1 for Free and S1 tiers
    String desiredPropertiesJobId = "DPCMD" + UUID.randomUUID();
    scheduleJobSetDesiredProperties(jobClient, desiredPropertiesJobId);
    monitorJob(jobClient, desiredPropertiesJobId);

    // Schedule twin job direct method
    String directMethodJobId = "DMCMD" + UUID.randomUUID();
    scheduleJobCallDirectMethod(jobClient, directMethodJobId);
    monitorJob(jobClient, directMethodJobId);

    // Run a query to show the job detail
    queryDeviceJobs(jobClient, start);

    System.out.println("Shutting down schedule-jobs app");
    ```

16. Zapisz i Zamknij `schedule-jobs\src\main\java\com\mycompany\app\App.java` pliku

17. Tworzenie **Planowanie zadań** aplikacji i poprawić błędy. W wierszu polecenia przejdź do `schedule-jobs` folderu i uruchom następujące polecenie:

    `mvn clean package -DskipTests`

## <a name="create-a-device-app"></a>Tworzenie aplikacji urządzenia

W tej sekcji utworzysz aplikację konsolową Java, obsługujący żądane właściwości, które są wysyłane z usługi IoT Hub i implementuje wywołania metody bezpośredniej.

1. W `iot-java-schedule-jobs` folderu, Utwórz projekt narzędzia Maven o nazwie **symulowane urządzenie** przy użyciu następującego polecenia w wierszu polecenia. Zwróć uwagę, że jest to jedno długie polecenie:

    `mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false`

2. W wierszu polecenia przejdź do `simulated-device` folderu.

3. Za pomocą edytora tekstów otwórz `pom.xml` w pliku `simulated-device` folderze i dodaj następujące zależności do **zależności** węzła. Ta zależność umożliwia użycie **iot-device-client** pakietu w aplikacji do komunikowania się z Centrum IoT:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-device-client</artifactId>
      <version>1.3.32</version>
    </dependency>
    ```

    > [!NOTE]
    > Możesz sprawdzić dostępność najnowszej wersji **iot-device-client** przy użyciu [funkcji wyszukiwania narzędzia Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-device-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22).

4. Dodaj następujący kod **kompilacji** węzła po **zależności** węzła. Ta konfiguracja powoduje, że narzędzie Maven na potrzeby tworzenia aplikacji Java 1.8:

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

5. Zapisz i Zamknij `pom.xml` pliku.

6. Za pomocą edytora tekstów otwórz `simulated-device\src\main\java\com\mycompany\app\App.java` pliku.

7. Dodaj do pliku następujące instrukcje **importowania**:

    ```java
    import com.microsoft.azure.sdk.iot.device.*;
    import com.microsoft.azure.sdk.iot.device.DeviceTwin.*;

    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.util.Scanner;
    ```

8. Dodaj następujące zmienne na poziomie klasy do klasy **App**. Zastępowanie `{youriothubname}` nazwą Centrum IoT hub, a `{yourdevicekey}` urządzenie wartością klucza wygenerowanego w *tworzenie tożsamości urządzenia* sekcji:

    ```java
    private static String connString = "HostName={youriothubname}.azure-devices.net;DeviceId=myDeviceID;SharedAccessKey={yourdevicekey}";
    private static IotHubClientProtocol protocol = IotHubClientProtocol.MQTT;
    private static final int METHOD_SUCCESS = 200;
    private static final int METHOD_NOT_DEFINED = 404;
    ```

    Ta przykładowa aplikacja używa zmiennej **protocol** podczas tworzenia wystąpienia obiektu **DeviceClient**.

9. Aby wydrukować powiadomień bliźniaczej reprezentacji urządzenia w konsoli, należy dodać następujące klasy zagnieżdżonej, aby **aplikacji** klasy:

    ```java
    // Handler for device twin operation notifications from IoT Hub
    protected static class DeviceTwinStatusCallBack implements IotHubEventCallback {
      public void execute(IotHubStatusCode status, Object context) {
        System.out.println("IoT Hub responded to device twin operation with status " + status.name());
      }
    }
    ```

10. Aby wydrukować powiadomienia metody bezpośredniej do konsoli, należy dodać następujące klasy zagnieżdżonej, aby **aplikacji** klasy:

    ```java
    // Handler for direct method notifications from IoT Hub
    protected static class DirectMethodStatusCallback implements IotHubEventCallback {
      public void execute(IotHubStatusCode status, Object context) {
        System.out.println("IoT Hub responded to direct method operation with status " + status.name());
      }
    }
    ```

11. Aby obsłużyć wywołania metody bezpośredniej z usługi IoT Hub, należy dodać następujące klasy zagnieżdżonej, aby **aplikacji** klasy:

    ```java
    // Handler for direct method calls from IoT Hub
    protected static class DirectMethodCallback
        implements DeviceMethodCallback {
      @Override
      public DeviceMethodData call(String methodName, Object methodData, Object context) {
        DeviceMethodData deviceMethodData;
        switch (methodName) {
          case "lockDoor": {
            System.out.println("Executing direct method: " + methodName);
            deviceMethodData = new DeviceMethodData(METHOD_SUCCESS, "Executed direct method " + methodName);
            break;
          }
          default: {
            deviceMethodData = new DeviceMethodData(METHOD_NOT_DEFINED, "Not defined direct method " + methodName);
          }
        }
        // Notify IoT Hub of result
        return deviceMethodData;
      }
    }
    ```

12. Aktualizacja **głównego** podpis metody, aby uwzględnić następujące `throws` klauzuli:

    ```java
    public static void main( String[] args ) throws IOException, URISyntaxException
    ```

13. Dodaj następujący kod do **głównego** metody:
    * Utwórz klienta urządzenia do komunikowania się z usługą IoT Hub.
    * Tworzenie **urządzenia** obiekt, aby zapisać właściwości bliźniaczych reprezentacji urządzeń.

    ```java
    // Create a device client
    DeviceClient client = new DeviceClient(connString, protocol);

    // An object to manage device twin desired and reported properties
    Device dataCollector = new Device() {
      @Override
      public void PropertyCall(String propertyKey, Object propertyValue, Object context)
      {
        System.out.println("Received desired property change: " + propertyKey + " " + propertyValue);
      }
    };
    ```

14. Do uruchamiania usług klienta urządzenia, Dodaj następujący kod do **głównego** metody:

    ```java
    try {
      // Open the DeviceClient
      // Start the device twin services
      // Subscribe to direct method calls
      client.open();
      client.startDeviceTwin(new DeviceTwinStatusCallBack(), null, dataCollector, null);
      client.subscribeToDeviceMethod(new DirectMethodCallback(), null, new DirectMethodStatusCallback(), null);
    } catch (Exception e) {
      System.out.println("Exception, shutting down \n" + " Cause: " + e.getCause() + " \n" + e.getMessage());
      dataCollector.clean();
      client.closeNow();
      System.out.println("Shutting down...");
    }
    ```

15. Oczekiwania użytkownika poprzez naciśnięcie **Enter** klucza przed zamknięciem, Dodaj następujący kod na końcu **głównego** metody:

    ```java
    // Close the app
    System.out.println("Press any key to exit...");
    Scanner scanner = new Scanner(System.in);
    scanner.nextLine();
    dataCollector.clean();
    client.closeNow();
    scanner.close();
    ```

16. Zapisz i Zamknij `simulated-device\src\main\java\com\mycompany\app\App.java` pliku.

17. Tworzenie **symulowane urządzenie** aplikacji i poprawić błędy. W wierszu polecenia przejdź do `simulated-device` folderu i uruchom następujące polecenie:

    `mvn clean package -DskipTests`

## <a name="run-the-apps"></a>Uruchamianie aplikacji

Teraz można przystąpić do uruchomienia aplikacji konsoli.

1. W wierszu polecenia w `simulated-device` folder, uruchom następujące polecenie, aby uruchomić aplikację urządzenia nasłuchiwanie zmiany żądanych właściwości i wywołania metody bezpośredniej:

    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

    ![Uruchamia klienta urządzenia](./media/iot-hub-java-java-schedule-jobs/device-app-1.png)

2. W wierszu polecenia w `schedule-jobs` folder, uruchom następujące polecenie, aby uruchomić **Planowanie zadań** usługi app Service do uruchamiania dwa zadania. Pierwszy ustawia wartości żądanych właściwości, drugi wywołania metody bezpośredniej:

    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

    ![Aplikacja usługi IoT Hub dla języka Java utworzy dwa zadania](./media/iot-hub-java-java-schedule-jobs/service-app-1.png)

3. W aplikacji urządzenia obsługuje zmiany żądanych właściwości i wywołania metody bezpośredniej:

    ![Klient urządzenia reaguje na zmiany](./media/iot-hub-java-java-schedule-jobs/device-app-2.png)

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku opisano konfigurowanie nowego centrum IoT Hub w witrynie Azure Portal, a następnie tworzenie tożsamości urządzenia w rejestrze tożsamości centrum. Utworzono aplikacji zaplecza, aby uruchomić dwa zadania. Pierwsze zadanie ustawiania wartości żądanych właściwości, a drugie zadanie o nazwie metody bezpośredniej.

Użyj następujących zasobów, aby dowiedzieć się, jak:

* Wysyłanie danych telemetrycznych z urządzeń przy użyciu [Rozpoczynanie pracy z usługą IoT Hub](quickstart-send-telemetry-java.md) samouczka.

* Formant urządzenia interakcyjne (takich jak włączenie wentylator z aplikacji kontrolowanej przez użytkownika) [używanie metod bezpośrednich](quickstart-control-device-java.md) tutorial.s