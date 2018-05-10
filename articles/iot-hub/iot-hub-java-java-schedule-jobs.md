---
title: Planowanie zadań z Centrum IoT Azure (Java) | Dokumentacja firmy Microsoft
description: Sposób tworzenia harmonogramu zadań Centrum IoT Azure do wywołania metody bezpośredniego i ustaw odpowiednie właściwości na wielu urządzeniach. Urządzenia Azure IoT SDK dla języka Java umożliwia wdrożenie symulowane urządzenie aplikacje i usługi Azure IoT SDK dla języka Java, aby zaimplementować aplikację usługi, aby uruchomić to zadanie.
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
ms.date: 07/10/2017
ms.author: dobett
ms.openlocfilehash: b8b0742054b0348ded39b6357d00f6eac3449f99
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="schedule-and-broadcast-jobs-java"></a>Zadania harmonogramu i emisji (Java)

[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

Użyj Centrum IoT Azure do planowania i śledzenia zadań, które aktualizują milionów urządzeń. Należy użyć zadania, aby:

* Aktualizowanie żądanych właściwości
* Tagi aktualizacji
* Wywołanie metody bezpośredniego

Zadanie opakowuje jedną z następujących czynności i śledzi wykonywania z zestawem urządzeń. Zapytanie dwie urządzenia definiuje zbiór urządzeń, które wykonuje zadanie przed. Na przykład aplikacji zaplecza zadanie służy do wywołania metody bezpośrednie na 10 000 urządzeń, które ponownym uruchomieniu urządzenia. Określ zbiór urządzeń za pomocą kwerendy dwie urządzenia i zaplanować zadania do uruchomienia w przyszłości. Śledzi postęp zadania jako każdego urządzenia odbierają i wykonać metoda bezpośrednia ponowny rozruch.

Aby dowiedzieć się więcej na temat każdego z tych funkcji, zobacz:

* Dwie urządzeń i właściwości: [wprowadzenie twins urządzenia](iot-hub-java-java-twin-getstarted.md)
* Bezpośrednie metody: [przewodnik dewelopera Centrum IoT — metody bezpośredniego](iot-hub-devguide-direct-methods.md) i [samouczek: bezpośrednie metody](iot-hub-java-java-direct-methods.md)

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Ten samouczek przedstawia sposób wykonania następujących czynności:

* Tworzenie aplikacji urządzenia implementujący bezpośredniego metodę o nazwie **lockDoor**. Aplikacji urządzenia Ponadto otrzymuje żądanej właściwości zmiany z poziomu aplikacji zaplecza.
* Tworzenie aplikacji zaplecza, która tworzy zadanie, aby wywołać **lockDoor** metoda bezpośrednia na wielu urządzeniach. Inne zadanie po zaakceptowaniu żądanej właściwości na wielu urządzeniach.

Na końcu tego samouczka masz urządzenie aplikację konsoli języka java i zaplecza aplikacji konsoli java:

**Symulowane urządzenie** które nawiązuje połączenie z Centrum IoT, implementuje **lockDoor** bezpośrednie — metoda i uchwytów potrzebne zmiany właściwości.

**Harmonogram zadań** wykorzystujące zadania do wywołania **lockDoor** metoda bezpośrednia i zaktualizuj właściwości dwie żądanego urządzenia na wielu urządzeniach.

> [!NOTE]
> Artykuł [Azure IoT SDK](iot-hub-devguide-sdks.md) informacje na temat zestawów SDK IoT Azure można tworzyć aplikacje zarówno urządzenia, jak i zaplecza.

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka niezbędne są następujące elementy:

* Najnowszy zestaw [Java SE Development Kit 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
* [Maven 3](https://maven.apache.org/install.html)
* Aktywne konto platformy Azure. (Jeśli nie masz konta, możesz utworzyć [bezpłatne konto](http://azure.microsoft.com/pricing/free-trial/) w zaledwie kilka minut.)

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity-portal](../../includes/iot-hub-get-started-create-device-identity-portal.md)]

Można również użyć [rozszerzenia IoT Azure CLI 2.0](https://github.com/Azure/azure-iot-cli-extension) narzędzia do dodania urządzenia do Centrum IoT.

## <a name="create-the-service-app"></a>Tworzenie aplikacji usługi

W tej sekcji służy do tworzenia aplikacji konsoli Java, który używa zadań:

* Wywołanie **lockDoor** metoda bezpośrednia na wielu urządzeniach.
* Wyślij odpowiednie właściwości na wielu urządzeniach.

Aby utworzyć aplikację:

1. Na komputerze deweloperskim, należy utworzyć pusty folder o nazwie `iot-java-schedule-jobs`.

1. W `iot-java-schedule-jobs` folderu, Utwórz projekt Maven o nazwie **harmonogram zadań** za pomocą następującego polecenia z wiersza polecenia. Zwróć uwagę, że jest to jedno długie polecenie:

    `mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=schedule-jobs -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false`

1. Wierszu polecenia przejdź do `schedule-jobs` folderu.

1. Za pomocą edytora tekstu, otwórz `pom.xml` w pliku `schedule-jobs` folderu i dodaj następujące zależności do **zależności** węzła. Ta zależność pozwala na użycie **klienta w przypadku usługi iot** pakietu aplikacji w celu komunikowania się z Centrum IoT:

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

1. Za pomocą edytora tekstu, otwórz `schedule-jobs\src\main\java\com\mycompany\app\App.java` pliku.

1. Dodaj do pliku następujące instrukcje **importowania**:

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

1. Dodaj następujące zmienne na poziomie klasy do klasy **App**. Zastąp `{youriothubconnectionstring}` z wymienionych w ciągu połączenia Centrum IoT *tworzenia Centrum IoT* sekcji:

    ```java
    public static final String iotHubConnectionString = "{youriothubconnectionstring}";
    public static final String deviceId = "myDeviceId";

    // How long the job is permitted to run without
    // completing its work on the set of devices
    private static final long maxExecutionTimeInSeconds = 30;
    ```

1. Dodaj następującą metodę do **aplikacji** klasy zaplanowane zadanie, aby zaktualizować **budynku** i **Floor** żądanego właściwości w dwie urządzenia:

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

1. Aby zaplanować zadanie do wywołania **lockDoor** metody, dodaj następującą metodę do **aplikacji** klasy:

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

1. Aby monitorować zadanie, dodaj następującą metodę do **aplikacji** klasy:

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

1. Aby sprawdzić, czy szczegóły uruchomienia zadania, dodaj następującą metodę:

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

1. Aktualizacja **głównego** podpis metody, aby uwzględnić następujące `throws` klauzuli:

    ```java
    public static void main( String[] args ) throws Exception
    ```

1. Aby uruchomić i monitorować dwa zadania sekwencyjnie, Dodaj następujący kod do **głównego** metody:

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

1. Zapisz i Zamknij `schedule-jobs\src\main\java\com\mycompany\app\App.java` pliku

1. Tworzenie **harmonogram zadań** aplikacji i poprawić błędy. Wierszu polecenia przejdź do `schedule-jobs` folderu i uruchom następujące polecenie:

    `mvn clean package -DskipTests`

## <a name="create-a-device-app"></a>Tworzenie aplikacji urządzenia

W tej sekcji utworzysz aplikację konsoli języka Java, która obsługuje odpowiednie właściwości, które są wysyłane z Centrum IoT i implementuje wywołanie metody bezpośredniego.

1. W `iot-java-schedule-jobs` folderu, Utwórz projekt Maven o nazwie **symulowane urządzenie** przy użyciu następującego polecenia z wiersza polecenia. Zwróć uwagę, że jest to jedno długie polecenie:

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
    private static final int METHOD_SUCCESS = 200;
    private static final int METHOD_NOT_DEFINED = 404;
    ```

    Ta przykładowa aplikacja używa zmiennej **protocol** podczas tworzenia wystąpienia obiektu **DeviceClient**.

1. Aby wydrukować powiadomień dwie urządzenia do konsoli, należy dodać następujące zagnieżdżona klasa na **aplikacji** klasy:

    ```java
    // Handler for device twin operation notifications from IoT Hub
    protected static class DeviceTwinStatusCallBack implements IotHubEventCallback {
      public void execute(IotHubStatusCode status, Object context) {
        System.out.println("IoT Hub responded to device twin operation with status " + status.name());
      }
    }
    ```

1. Aby wydrukować metoda bezpośrednia powiadomienia do konsoli, należy dodać następujące zagnieżdżona klasa na **aplikacji** klasy:

    ```java
    // Handler for direct method notifications from IoT Hub
    protected static class DirectMethodStatusCallback implements IotHubEventCallback {
      public void execute(IotHubStatusCode status, Object context) {
        System.out.println("IoT Hub responded to direct method operation with status " + status.name());
      }
    }
    ```

1. Aby obsłużyć wywołania metody bezpośrednio z Centrum IoT, Dodaj następujące zagnieżdżona klasa na **aplikacji** klasy:

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

1. Aktualizacja **głównego** podpis metody, aby uwzględnić następujące `throws` klauzuli:

    ```java
    public static void main( String[] args ) throws IOException, URISyntaxException
    ```

1. Dodaj następujący kod do **głównego** metodę:
    * Utwórz klienta urządzenia do komunikowania się z Centrum IoT.
    * Utwórz **urządzenia** obiekt, aby zapisać właściwości dwie urządzenia.

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

1. Do uruchamiania usług klienta urządzenia, Dodaj następujący kod do **głównego** metody:

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

1. Oczekiwania użytkownika o naciśnięcie **Enter** klucza przed zamknięciem, Dodaj następujący kod na końcu **głównego** metody:

    ```java
    // Close the app
    System.out.println("Press any key to exit...");
    Scanner scanner = new Scanner(System.in);
    scanner.nextLine();
    dataCollector.clean();
    client.closeNow();
    scanner.close();
    ```

1. Zapisz i Zamknij `simulated-device\src\main\java\com\mycompany\app\App.java` pliku.

1. Tworzenie **symulowane urządzenie** aplikacji i poprawić błędy. Wierszu polecenia przejdź do `simulated-device` folderu i uruchom następujące polecenie:

    `mvn clean package -DskipTests`

## <a name="run-the-apps"></a>Uruchamianie aplikacji

Teraz można przystąpić do uruchomienia aplikacji konsoli.

1. W wierszu polecenia `simulated-device` folderu, uruchom następujące polecenie, aby uruchomić aplikację urządzenia nasłuchiwanie zmian żądanej właściwości i wywołania metody bezpośrednie:

    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

    ![Uruchamia klienta urządzenia](media/iot-hub-java-java-schedule-jobs/device-app-1.png)

1. W wierszu polecenia `schedule-jobs` folderu, uruchom następujące polecenie, aby uruchomić **harmonogram zadań** dwa zadania uruchamiania aplikacji usługi. Pierwszy ustawia wartości żądanej właściwości, drugi wywołuje metodę bezpośrednie:

    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

    ![Centrum IoT Java usługi aplikacji tworzy dwa zadania](media/iot-hub-java-java-schedule-jobs/service-app-1.png)

1. Aplikacji urządzenia obsługującego zmiany żądanej właściwości i wywołanie metody bezpośrednie:

    ![Klient urządzenie odpowiada na zmiany](media/iot-hub-java-java-schedule-jobs/device-app-2.png)

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku opisano konfigurowanie nowego centrum IoT Hub w witrynie Azure Portal, a następnie tworzenie tożsamości urządzenia w rejestrze tożsamości centrum. Utworzono aplikacji zaplecza, aby uruchomić dwa zadania. Pierwszego zadania do ustawiania wartości właściwości odpowiednie, a drugi zadania wywołana metoda bezpośrednia.

Użyj następujących zasobów, aby dowiedzieć się, jak:

* Wysyłanie danych telemetrycznych z urządzenia z [Rozpoczynanie pracy z Centrum IoT](iot-hub-java-java-getstarted.md) samouczka.
* Kontrolowanie urządzenia interakcyjne (takich jak włączanie wentylator z aplikacji kontrolowane przez użytkownika) z [metody bezpośredniego](iot-hub-java-java-direct-methods.md) samouczka.
