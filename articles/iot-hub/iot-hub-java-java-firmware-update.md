---
title: Aktualizacja oprogramowania układowego urządzenia z Centrum IoT Azure (Java/Java) | Dokumentacja firmy Microsoft
description: Jak używać zarządzania urządzeniami w usłudze Azure IoT Hub zainicjować aktualizację oprogramowania układowego urządzenia. Wdrożenie aplikacji symulowane urządzenie i zaimplementować usługi aplikacji, które wyzwala aktualizacji oprogramowania układowego za pomocą urządzenia Azure IoT SDK dla języka Java.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: conceptual
ms.date: 09/11/2017
ms.author: dobett
ms.openlocfilehash: 5991615bca26749e1f138b561260108f8bcf2646
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34634611"
---
# <a name="use-device-management-to-initiate-a-device-firmware-update-javajava"></a>Umożliwia zarządzanie urządzeniami zainicjować aktualizację oprogramowania układowego urządzenia (Java/Java)
[!INCLUDE [iot-hub-selector-firmware-update](../../includes/iot-hub-selector-firmware-update.md)]

W [wprowadzenie do zarządzania urządzeniami] [ lnk-dm-getstarted] samouczka przedstawiono sposób użycia [dwie urządzenia] [ lnk-devtwin] i [bezpośrednie metody ] [ lnk-c2dmethod] podstawowych zdalnie ponownego uruchomienia urządzenia. Ten samouczek używa tego samego podstawowych Centrum IoT i pokazuje, jak wykonać aktualizację oprogramowania układowego symulowane end-to-end.  Ten wzorzec jest używany w implementacji aktualizacji oprogramowania układowego dla [Pi malina urządzenia implementacji próbki][lnk-rpi-implementation].

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Ten samouczek przedstawia sposób wykonania następujących czynności:

* Utwórz aplikację konsoli języka Java, która wywołuje **firmwareUpdate** metoda bezpośrednia w aplikacji symulowane urządzenie za pomocą Centrum IoT.
* Utwórz aplikację konsoli języka Java, która symuluje urządzenia i implementuje **firmwareUpdate** metoda bezpośrednia. Ta metoda inicjuje procesem wieloetapowym czeka na pobranie obrazu oprogramowania układowego, pobierze obraz oprogramowania układowego i na koniec dotyczy oprogramowania układowego obrazu. Podczas każdego etapu aktualizacji urządzenie korzysta z właściwości zgłoszony do raport dotyczący postępu.

Na końcu tego samouczka znajdują się dwie aplikacje konsoli Java:

**Aktualizacja oprogramowania układowego**, wywołuje metodę bezpośredniego symulowane urządzenie, wyświetla odpowiedzi oraz okresowo aktualizacje zgłoszone właściwości

**Symulowane urządzenie**, łączy się z Centrum IoT z tożsamości utworzonej wcześniej urządzenia otrzymuje wywołanie metody bezpośredniego firmwareUpdate i działa za pośrednictwem symulacji aktualizacji oprogramowania układowego

Do wykonania kroków tego samouczka niezbędne są następujące elementy:

* Najnowszy zestaw [Java SE Development Kit 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) 
* [Maven 3](https://maven.apache.org/install.html) 
* Aktywne konto platformy Azure. (Jeśli go nie masz, możesz utworzyć [bezpłatne konto próbne][lnk-free-trial] w zaledwie kilka minut).

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity-portal](../../includes/iot-hub-get-started-create-device-identity-portal.md)]

## <a name="trigger-a-remote-firmware-update-on-the-device-using-a-direct-method"></a>Wyzwalanie aktualizacji oprogramowania układowego zdalnego na urządzeniu, za pomocą bezpośrednich — metoda
W tej sekcji utworzysz aplikację konsoli języka Java, która inicjuje aktualizacji oprogramowania układowego zdalnego na urządzeniu. Aplikacja korzysta metoda bezpośrednia zainicjować aktualizację, a zapytania dwie urządzenia okresowo pobrać stanu aktualizacji oprogramowania układowego active.

1. Utwórz pusty folder o nazwie PD get-started.

1. W folderze PD get-started Utwórz projekt Maven o nazwie **aktualizacji oprogramowania układowego** za pomocą następującego polecenia z wiersza polecenia. Zwróć uwagę, że jest to jedno długie polecenie:

    `mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=firmware-update -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false`

1. Wierszu polecenia przejdź do folderu, aktualizacji oprogramowania układowego.

1. Za pomocą edytora tekstu, otwórz plik pom.xml w folderze aktualizacji oprogramowania układowego i dodaj następujące zależności do **zależności** węzła. Ta zależność umożliwia za pomocą pakietu iot usługi klienta w aplikacji do komunikowania się z Centrum IoT:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-service-client</artifactId>
      <version>1.5.22</version>
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

1. Zapisz i zamknij plik pom.xml.

1. Za pomocą edytora tekstu, otwórz plik źródłowy firmware-update\src\main\java\com\mycompany\app\App.java.

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

1. Dodaj następujące zmienne na poziomie klasy do klasy **App**. Zastąp **{youriothubconnectionstring}** z wymienionych w ciągu połączenia Centrum IoT *tworzenia Centrum IoT* sekcji:

    ```java
    public static final String iotHubConnectionString = "{youriothubconnectionstring}";
    public static final String deviceId = "myDeviceId";

    private static final String methodName = "firmwareUpdate";
    private static final Long responseTimeout = TimeUnit.SECONDS.toSeconds(30);
    private static final Long connectTimeout = TimeUnit.SECONDS.toSeconds(5);
    ```

1. Aby zaimplementować metodę, która odczytuje właściwości zgłoszony z dwie urządzenia, Dodaj następujący kod do **aplikacji** klasy:

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

1. Modyfikowanie podpis **głównego** metodę, aby zgłosić następujące wyjątki:

    ```java
    public static void main( String[] args ) throws IOException
    ```

1. Aby wywołać metodę bezpośredniego firmwareUpdate symulowane urządzenie, Dodaj następujący kod do **głównego** metody:

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

1. Sondowanie zgłoszone właściwości z symulowane urządzenie, Dodaj następujący kod do **głównego** metody:

    ```java
    ShowReportedProperties();
    ```

1. Aby umożliwić Zatrzymaj aplikację, Dodaj następujący kod do **głównego** metody:

    ```java
    System.out.println("Press ENTER to exit.");
    System.in.read();
    System.out.println("Shutting down sample...");
    ```

1. Zapisz i zamknij plik firmware-update\src\main\java\com\mycompany\app\App.java.

1. Tworzenie **aktualizacji oprogramowania układowego** aplikacji zaplecza i poprawić błędy. Wierszu polecenia przejdź do folderu, aktualizacji oprogramowania układowego i uruchom następujące polecenie:

    `mvn clean package -DskipTests`

## <a name="simulate-a-device-to-handle-direct-method-calls"></a>Symulacji urządzenia do obsługi połączeń bezpośrednich — metoda
W tej sekcji służy do tworzenia aplikacji konsoli symulowane urządzenie Java, która może odbierać metoda bezpośrednia firmwareUpdate. Następnie aplikacja jest uruchamiana za pomocą wielu stanu procesu do symulowania aktualizacji oprogramowania układowego przy użyciu reportedProperties do komunikowania się stan.

1. W folderze PD get-started Utwórz projekt Maven o nazwie **symulowane urządzenie** przy użyciu następującego polecenia z wiersza polecenia. Zwróć uwagę, że jest to jedno długie polecenie:

    `mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false`

1. W wierszu polecenia przejdź do folderu simulated-device.

1. Za pomocą edytora tekstu, otwórz plik pom.xml w folderze aktualizacji oprogramowania układowego i dodaj następujące zależności do **zależności** węzła. Ta zależność umożliwia za pomocą pakietu iot usługi klienta w aplikacji do komunikowania się z Centrum IoT:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-device-client</artifactId>
      <version>1.3.32</version>
      <type>jar</type>
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
    import java.util.HashMap;
    ```

1. Dodaj następujące zmienne na poziomie klasy do klasy **App**. Zastąp **{yourdeviceconnectionstring}** z wymienionych w ciągu połączenia urządzenia *tworzenie tożsamości urządzenia* sekcji:

    ```java
    private static final int METHOD_SUCCESS = 200;
    private static final int METHOD_NOT_DEFINED = 404;

    private static IotHubClientProtocol protocol = IotHubClientProtocol.MQTT;
    private static String connString = "{yourdeviceconnectionstring";
    private static DeviceClient client;

    private static String downloadURL = "unknown";
    ```

1. Do implementowania metoda bezpośrednia, zapewniają wywołań zwrotnych, dodając następujące klasy zagnieżdżonej **aplikacji** klasy:

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

1. Do implementowania urządzenia dwie, zapewniają wywołań zwrotnych, dodając następujące klasy zagnieżdżonej **aplikacji** klasy:

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

1. Aby wdrożyć aktualizację oprogramowania układowego, Dodaj następujące zagnieżdżona klasa na **aplikacji** klasy:

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

1. Modyfikowanie podpis **głównego** metodę, aby zgłosić następujące wyjątki:

    ```java
    public static void main(String[] args) throws IOException, URISyntaxException
    ```

1. Do zainicjowania bezpośredniego metody i procedury twins urządzenia, Dodaj następujący kod do **głównego** metody:

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

1. Tworzenie **symulowane urządzenie** aplikacji i poprawić błędy. Wierszu polecenia przejdź do folderu symulowane urządzenie, a następnie uruchom następujące polecenie:

    `mvn clean package -DskipTests`

## <a name="run-the-apps"></a>Uruchamianie aplikacji
Teraz można przystąpić do uruchomienia aplikacji.

1. W wierszu polecenia **symulowane urządzenie** folderu, uruchom następujące polecenie, aby rozpocząć nasłuchiwanie metoda bezpośrednia aktualizacja oprogramowania układowego.
   
    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

1. W wierszu polecenia **aktualizacji oprogramowania układowego** folderu, uruchom następujące polecenie, aby wywołać aktualizacji oprogramowania układowego i zapytanie twins urządzenia na symulowane urządzenie z Centrum IoT:

    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

3. Widać symulowane urządzenie odpowiada na żądania metoda bezpośrednia w konsoli.

    ![Pomyślnie zaktualizowano oprogramowania układowego][img-fwupdate]

## <a name="next-steps"></a>Kolejne kroki
W tym samouczku używana metoda bezpośrednia do wyzwolenia aktualizacji oprogramowania układowego zdalnego na urządzeniu i można śledzić postępy aktualizacji oprogramowania układowego zgłoszone właściwości.

Aby dowiedzieć się, jak rozszerzyć IoT, Twoje rozwiązanie i harmonogram metoda wywołuje na wielu urządzeniach, zobacz [emisji zadania i harmonogramu] [ lnk-tutorial-jobs] samouczka.

<!-- images -->
[img-fwupdate]: media/iot-hub-java-java-firmware-update/firmwareUpdated.png

[lnk-devtwin]: iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: iot-hub-devguide-direct-methods.md
[lnk-dm-getstarted]: iot-hub-java-java-device-management-getstarted.md
[lnk-tutorial-jobs]: iot-hub-java-java-schedule-jobs.md

[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[lnk-rpi-implementation]: https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iothub_client_sample_mqtt_dm/pi_device