---
title: Aktualizacja oprogramowania układowego urządzenia za pomocą usługi Azure IoT Hub (Java/Java) | Dokumentacja firmy Microsoft
description: Jak zainicjować aktualizację oprogramowania układowego urządzenia, za pomocą zarządzania urządzeniami w usłudze Azure IoT Hub. Implementowanie aplikacji symulowanego urządzenia i zaimplementować app service, która powoduje aktualizację oprogramowania układowego za pomocą urządzeń Azure IoT SDK dla języka Java.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: conceptual
ms.date: 09/11/2017
ms.author: dobett
ms.openlocfilehash: 5991615bca26749e1f138b561260108f8bcf2646
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2018
ms.locfileid: "38611331"
---
# <a name="use-device-management-to-initiate-a-device-firmware-update-javajava"></a>Umożliwia zarządzanie urządzeniami zainicjować aktualizację oprogramowania układowego urządzenia (Java/Java)
[!INCLUDE [iot-hub-selector-firmware-update](../../includes/iot-hub-selector-firmware-update.md)]

W [wprowadzenie do zarządzania urządzeniami] [ lnk-dm-getstarted] samouczków pokazano, jak używać [bliźniaczej reprezentacji urządzenia] [ lnk-devtwin] i [metody bezpośrednie ] [ lnk-c2dmethod] podstawowych, aby zdalnie ponownie uruchomić urządzenie. Ten samouczek używa tych samych podstawowych usługi IoT Hub oraz dowiesz się, jak przeprowadzić aktualizację oprogramowania układowego symulowane end-to-end.  Ten wzorzec jest używany we wdrażaniu aktualizacji oprogramowania układowego dla [przykładu implementacji urządzenia Raspberry Pi][lnk-rpi-implementation].

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Ten samouczek przedstawia sposób wykonania następujących czynności:

* Tworzenie aplikacji konsolowej Java, która wywołuje **firmwareUpdate** bezpośrednie metody w aplikacji symulowanego urządzenia za pośrednictwem usługi IoT hub.
* Utwórz aplikację konsolową Java, która symuluje urządzenie i implementuje **firmwareUpdate** bezpośrednie metody. Ta metoda inicjuje proces wieloetapowych czeka do pobierania obrazu oprogramowania układowego, który pobierze obraz oprogramowania układowego i na koniec ma zastosowanie obrazu oprogramowania układowego. Na etapie każdej aktualizacji urządzenie używa zgłaszanych właściwości do raportowania postępu.

Na końcu tego samouczka będziesz mieć dwie aplikacje konsolowe Java:

**Aktualizacja oprogramowania układowego**, wywołuje metody bezpośredniej na symulowanym urządzeniu, wyświetla odpowiedzi oraz okresowo zgłaszanych właściwości aktualizacji

**Symulowane urządzenie**, nawiązanie połączenia z Centrum IoT hub przy użyciu tożsamości urządzenia utworzonej wcześniej, wywołanie metody bezpośredniej firmwareUpdate odbiera i przechodzi przez symulację aktualizacji oprogramowania układowego

Do wykonania kroków tego samouczka niezbędne są następujące elementy:

* Najnowszy zestaw [Java SE Development Kit 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) 
* [Maven 3](https://maven.apache.org/install.html) 
* Aktywne konto platformy Azure. (Jeśli go nie masz, możesz utworzyć [bezpłatne konto próbne][lnk-free-trial] w zaledwie kilka minut).

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity-portal](../../includes/iot-hub-get-started-create-device-identity-portal.md)]

## <a name="trigger-a-remote-firmware-update-on-the-device-using-a-direct-method"></a>Wyzwalanie aktualizacji oprogramowania układowego zdalnego na urządzeniu, korzystając z metody bezpośredniej
W tej sekcji utworzysz aplikację konsolową Java, która inicjuje aktualizacji oprogramowania układowego zdalnego na urządzeniu. Aplikacja używa metody bezpośredniej do inicjowania aktualizacji i używa zapytań bliźniaczych reprezentacji urządzeń, aby okresowo pobieranie stanu aktualizacji oprogramowania układowego active.

1. Utwórz pusty folder o nazwie PD get-started.

1. W folderze PD get-started Utwórz projekt narzędzia Maven o nazwie **aktualizacji oprogramowania układowego** przy użyciu następującego polecenia w wierszu polecenia. Zwróć uwagę, że jest to jedno długie polecenie:

    `mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=firmware-update -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false`

1. W wierszu polecenia przejdź do folderu aktualizacji oprogramowania układowego.

1. Za pomocą edytora tekstu Otwórz plik pom.xml w folderze aktualizacji oprogramowania układowego i dodaj następującą zależność do **zależności** węzła. Ta zależność umożliwia użycie pakietu iot-service-client w aplikacji do komunikowania się z Centrum IoT:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-service-client</artifactId>
      <version>1.5.22</version>
      <type>jar</type>
    </dependency>
    ```

    > [!NOTE]
    > Możesz sprawdzić dostępność najnowszej wersji **iot-service-client** przy użyciu [funkcji wyszukiwania narzędzia Maven](http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-service-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22).

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

1. Za pomocą edytora tekstu Otwórz plik źródłowy firmware-update\src\main\java\com\mycompany\app\App.java.

1. Dodaj do pliku następujące instrukcje **importowania**:

    ```java
    import com.microsoft.azure.sdk.iot.service.devicetwin.DeviceMethod;
    import com.microsoft.azure.sdk.iot.service.devicetwin.MethodResult;
    import com.microsoft.azure.sdk.iot.service.exceptions.IotHubException;
    import com.microsoft.azure.sdk.iot.service.devicetwin.DeviceTwin;
    import com.microsoft.azure.sdk.iot.service.devicetwin.DeviceTwinDevice;

    import java.io.IOException;
    import java.util.concurrent.TimeUnit;
    ```

1. Dodaj następujące zmienne na poziomie klasy do klasy **App**. Zastąp **{youriothubconnectionstring}** parametrami połączenia Centrum IoT zanotowanym w *Tworzenie Centrum IoT* sekcji:

    ```java
    public static final String iotHubConnectionString = "{youriothubconnectionstring}";
    public static final String deviceId = "myDeviceId";

    private static final String methodName = "firmwareUpdate";
    private static final Long responseTimeout = TimeUnit.SECONDS.toSeconds(30);
    private static final Long connectTimeout = TimeUnit.SECONDS.toSeconds(5);
    ```

1. Aby zaimplementować metodę, która odczytuje zgłaszane właściwości z bliźniaczej reprezentacji urządzenia, należy dodać następujące polecenie, aby **aplikacji** klasy:

    ```java
    public static void ShowReportedProperties() 
    {
        try 
        {
          DeviceTwin deviceTwins = DeviceTwin.createFromConnectionString(iotHubConnectionString);
          DeviceTwinDevice twinDevice = new DeviceTwinDevice(deviceId);
          
          Boolean firmwareUpdated = false;
          Integer timeoutCycle = 0;
    
          while (!firmwareUpdated)
          {
            if (timeoutCycle > 5)
            {
              System.out.println("Operation timed out");
              break;
            }
    
            Thread.sleep(1000);
              
            deviceTwins.getTwin(twinDevice);
    
            String reportedProperties = twinDevice.reportedPropertiesToString();
              
            if(reportedProperties.contains("status=waiting"))
            {
              System.out.println("Waiting on device...");
            }
            else if(reportedProperties.contains("status=downloadComplete"))
            {
              System.out.println("Download complete, applying firmware...");
            }
            else if (reportedProperties.contains("status=applyComplete"))
            {
              System.out.println("Firmware applied");
              System.out.println("Get reported properties from device twin");
              System.out.println(twinDevice.reportedPropertiesToString());
              firmwareUpdated = true;
            }
            else
            {
              timeoutCycle++;
            }
          }
        } catch (Exception ex) {
            System.out.println("Exception reading reported properties: " + ex.getMessage());
        }
    }
    ```

1. Zmodyfikuj podpis metody **głównego** metodę, aby zgłosić następujące wyjątki:

    ```java
    public static void main( String[] args ) throws IOException
    ```

1. Aby wywołać metody bezpośredniej firmwareUpdate na symulowanym urządzeniu, Dodaj następujący kod do **głównego** metody:

    ```java
    DeviceMethod methodClient = DeviceMethod.createFromConnectionString(iotHubConnectionString);

    try
    {
      String payload = "https://someurl";
      
      System.out.println("Invoked firmware update on device.");
      System.out.println("Sent URL: " + payload);
      
      MethodResult result = methodClient.invoke(deviceId, methodName, responseTimeout, connectTimeout, payload);

      if(result == null)
      {
        throw new IOException("Invoke direct method reboot returns null");
      }

      System.out.println("Status for device:   " + result.getStatus());
      System.out.println("Message from device: " + result.getPayload());
    }
    catch (IotHubException e)
    {
      System.out.println(e.getMessage());
    }
    ```

1. Sondowanie zgłaszane właściwości symulowane urządzenia, Dodaj następujący kod do **głównego** metody:

    ```java
    ShowReportedProperties();
    ```

1. Umożliwienie Zatrzymaj aplikację, Dodaj następujący kod do **głównego** metody:

    ```java
    System.out.println("Press ENTER to exit.");
    System.in.read();
    System.out.println("Shutting down sample...");
    ```

1. Zapisz i zamknij plik firmware-update\src\main\java\com\mycompany\app\App.java.

1. Tworzenie **aktualizacji oprogramowania układowego** aplikacji zaplecza i poprawić błędy. W wierszu polecenia przejdź do folderu, aktualizacja oprogramowania układowego i uruchom następujące polecenie:

    `mvn clean package -DskipTests`

## <a name="simulate-a-device-to-handle-direct-method-calls"></a>Symulowanie urządzenia, aby obsłużyć wywołania metody bezpośredniej
W tej sekcji służy do tworzenia aplikacji symulowanego urządzenia konsoli Java, która może odbierać firmwareUpdate metody bezpośredniej. Aplikacja, która jest następnie uruchamia proces wielostanowy do symulacji za pomocą reportedProperties informują o stanie aktualizacji oprogramowania układowego.

1. W folderze PD get-started Utwórz projekt narzędzia Maven o nazwie **symulowane urządzenie** przy użyciu następującego polecenia w wierszu polecenia. Zwróć uwagę, że jest to jedno długie polecenie:

    `mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false`

1. W wierszu polecenia przejdź do folderu simulated-device.

1. Za pomocą edytora tekstu Otwórz plik pom.xml w folderze aktualizacji oprogramowania układowego i dodaj następującą zależność do **zależności** węzła. Ta zależność umożliwia użycie pakietu iot-service-client w aplikacji do komunikowania się z Centrum IoT:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-device-client</artifactId>
      <version>1.3.32</version>
      <type>jar</type>
    </dependency>
    ```

    > [!NOTE]
    > Możesz sprawdzić dostępność najnowszej wersji **iot-device-client** przy użyciu [funkcji wyszukiwania narzędzia Maven](http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-device-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22).

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
    import java.util.HashMap;
    ```

1. Dodaj następujące zmienne na poziomie klasy do klasy **App**. Zastąp **{yourdeviceconnectionstring}** parametrami połączenia urządzeń wymienionych w *tworzenie tożsamości urządzenia* sekcji:

    ```java
    private static final int METHOD_SUCCESS = 200;
    private static final int METHOD_NOT_DEFINED = 404;

    private static IotHubClientProtocol protocol = IotHubClientProtocol.MQTT;
    private static String connString = "{yourdeviceconnectionstring";
    private static DeviceClient client;

    private static String downloadURL = "unknown";
    ```

1. Aby zaimplementować metodę bezpośrednią funkcji, należy podać wywołań zwrotnych przez dodanie następujących klas zagnieżdżonych w celu **aplikacji** klasy:

    ```java
    protected static class DirectMethodStatusCallback implements IotHubEventCallback
    {
      public void execute(IotHubStatusCode status, Object context)
      {
        System.out.println("IoT Hub responded to device method operation with status " + status.name());
      }
    }
    protected static class DirectMethodCallback implements com.microsoft.azure.sdk.iot.device.DeviceTwin.DeviceMethodCallback
    {
      @Override
      public DeviceMethodData call(String methodName, Object methodData, Object context)
      {
        DeviceMethodData deviceMethodData;
        switch (methodName)
        {
          case "firmwareUpdate" :
          {
            System.out.println("Response to method '" + methodName + "' sent successfully");
    
            downloadURL = new String((byte[])methodData);
    
            int status = METHOD_SUCCESS;
            System.out.println("Starting firmware update");
            deviceMethodData = new DeviceMethodData(status, "Started firmware update");
            FirmwareUpdateThread firmwareUpdateThread = new FirmwareUpdateThread();
            Thread t = new Thread(firmwareUpdateThread);
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

1. Aby zaimplementować funkcje bliźniaczej reprezentacji urządzenia, należy podać wywołań zwrotnych przez dodanie następujących klas zagnieżdżonych w celu **aplikacji** klasy:

    ```java
    protected static class DeviceTwinStatusCallback implements IotHubEventCallback
    {
      public void execute(IotHubStatusCode status, Object context)
      {
        System.out.println("IoT Hub responded to device twin operation with status " + status.name());
      }
    }
    protected static class PropertyCallback implements PropertyCallBack<String, String>
    {
      public void PropertyCall(String propertyKey, String propertyValue, Object context)
      {
        System.out.println("PropertyKey:     " + propertyKey);
        System.out.println("PropertyKvalue:  " + propertyKey);
      }
    }
    ```

1. Aby wdrożyć aktualizację oprogramowania układowego, Dodaj następujące klasy zagnieżdżonej, aby **aplikacji** klasy:

    ```java
    protected static class FirmwareUpdateThread implements Runnable {
      public void run() {
        try {      
          HashMap initialUpdate = new HashMap();
          Property sentProperty = new Property("firmwareUpdate", initialUpdate);
          Set<Property> sentPackage = new HashSet<Property>();
          
          System.out.println("Downloading from " + downloadURL);
    
          initialUpdate.put("status", "waiting");
          initialUpdate.put("fwPackageUri", downloadURL);
          initialUpdate.put("startedWaitingTime", LocalDateTime.now().toString());   
          sentPackage.add(sentProperty);
    
          client.sendReportedProperties(sentPackage);
    
          Thread.sleep(5000);
    
          System.out.println("Download complete");
    
          HashMap downloadUpdate = new HashMap();
          downloadUpdate.put("status","downloadComplete");
          downloadUpdate.put("downloadCompleteTime", LocalDateTime.now().toString());
          downloadUpdate.put("startedApplyingImage", LocalDateTime.now().toString());
          sentProperty.setValue(downloadUpdate);
    
          client.sendReportedProperties(sentPackage);
    
          Thread.sleep(5000);
    
          System.out.println("Apply complete");
    
          HashMap applyUpdate = new HashMap();
          applyUpdate.put("status","applyComplete");
          applyUpdate.put("lastFirmwareUpdate", LocalDateTime.now().toString());
          sentProperty.setValue(applyUpdate);
    
          client.sendReportedProperties(sentPackage);
    
          Thread.sleep(5000);
    
          HashMap resetUpdate = new HashMap();
          applyUpdate.put("status","reset");
          sentProperty.setValue(resetUpdate);
    
          client.sendReportedProperties(sentPackage);
        }
        catch (Exception ex) {
          System.out.println("Exception in reboot thread: " + ex.getMessage());
        }
      }
    }
    ```

1. Zmodyfikuj podpis metody **głównego** metodę, aby zgłosić następujące wyjątki:

    ```java
    public static void main(String[] args) throws IOException, URISyntaxException
    ```

1. Aby zainicjować bezpośrednich metod i procedury bliźniaczych reprezentacji urządzeń, Dodaj następujący kod do **głównego** metody:

    ```java
    client = new DeviceClient(connString, protocol);

    try
    {
      client.open();
      client.subscribeToDeviceMethod(new DirectMethodCallback(), null, new DirectMethodStatusCallback(), null);
      client.startDeviceTwin(new DeviceTwinStatusCallback(), null, new PropertyCallback(), null);
      System.out.println("Client connected to IoT Hub.  Waiting for firmwareUpdate direct method.");
    }
    catch (Exception e)
    {
      System.out.println("On exception, shutting down \n" + " Cause: " + e.getCause() + " \n" +  e.getMessage());
      client.close();
      System.out.println("Shutting down...");
    }
    ```

1. Aby umożliwić Zatrzymaj aplikację, Dodaj następujący kod na końcu **głównego** metody:

    ```java
    System.out.println("Press any key to exit...");
    Scanner scanner = new Scanner(System.in);
    scanner.nextLine();
    scanner.close();
    client.close();
    System.out.println("Shutting down...");
    ```

1. Zapisz i zamknij plik simulated-device\src\main\java\com\mycompany\app\App.java.

1. Tworzenie **symulowane urządzenie** aplikacji i poprawić błędy. W wierszu polecenia przejdź do folderu simulated-device i uruchom następujące polecenie:

    `mvn clean package -DskipTests`

## <a name="run-the-apps"></a>Uruchamianie aplikacji
Teraz można przystąpić do uruchomienia aplikacji.

1. W wierszu polecenia w **symulowane urządzenie** folder, uruchom następujące polecenie, aby rozpocząć nasłuchiwanie metody bezpośredniej aktualizacji oprogramowania układowego.
   
    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

1. W wierszu polecenia w **aktualizacji oprogramowania układowego** folder, uruchom następujące polecenie, aby wywołać aktualizacji oprogramowania układowego i zapytań bliźniaczych reprezentacji urządzeń na symulowanego urządzenia z usługi IoT hub:

    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

3. Możesz zobaczyć symulowane urządzenie odpowiada na metody bezpośredniej w konsoli.

    ![Pomyślnie zaktualizowano oprogramowania układowego][img-fwupdate]

## <a name="next-steps"></a>Kolejne kroki
W tym samouczku używane metody bezpośredniej do wyzwalania aktualizacji oprogramowania układowego zdalnego na urządzeniu i można śledzić postęp aktualizacji oprogramowania układowego w zgłaszanych właściwości.

Aby dowiedzieć się, jak rozszerzyć rozwiązanie i harmonogram metoda wywołuje na wielu urządzeniach IoT, zobacz [harmonogramu i zadań emisji] [ lnk-tutorial-jobs] samouczka.

<!-- images -->
[img-fwupdate]: media/iot-hub-java-java-firmware-update/firmwareUpdated.png

[lnk-devtwin]: iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: iot-hub-devguide-direct-methods.md
[lnk-dm-getstarted]: iot-hub-java-java-device-management-getstarted.md
[lnk-tutorial-jobs]: iot-hub-java-java-schedule-jobs.md

[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[lnk-rpi-implementation]: https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iothub_client_sample_mqtt_dm/pi_device