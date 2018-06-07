---
title: Przekazywanie plików z urządzenia do Centrum IoT Azure z językiem Java | Dokumentacja firmy Microsoft
description: Sposób przekazywania plików z urządzenia do chmury przy użyciu urządzenia Azure IoT SDK dla języka Java. Przekazano pliki są przechowywane w kontenerze obiektu blob magazynu Azure.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: conceptual
ms.date: 06/28/2017
ms.author: dobett
ms.openlocfilehash: 09580f3bb5d6f6f5ccb15adddf0cf1f9e19c2210
ms.sourcegitcommit: 6cf20e87414dedd0d4f0ae644696151e728633b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/06/2018
ms.locfileid: "34807581"
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub"></a>Przekazywanie plików z urządzenia do chmury z Centrum IoT

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

W tym samouczku opiera się na kodzie w [wysyłać chmury do urządzenia z Centrum IoT](iot-hub-java-java-c2d.md) samouczek pokazanie sposobu używania [pliku możliwości przekazywania Centrum IoT](iot-hub-devguide-file-upload.md) można przekazać pliku do [magazynu obiektów blob Azure](../storage/index.yml). Samouczek pokazuje, jak do:

- Bezpieczne udostępnianie urządzenia platformy Azure blob identyfikatora URI pobierania pliku.
- Powiadomienia o przekazywania plików Centrum IoT umożliwia wyzwalanie przetwarzania plików w sieci wewnętrznej aplikacji.

[Rozpoczynanie pracy z Centrum IoT](iot-hub-java-java-getstarted.md) i [wysyłać chmury do urządzenia z Centrum IoT](iot-hub-java-java-c2d.md) samouczki przedstawiają podstawowych funkcji obsługi komunikatów z Centrum IoT urządzenia do chmury i chmury do urządzenia. [Wiadomości procesu urządzenia do chmury](tutorial-routing.md) samouczek przedstawia sposób niezawodny sposób przechowywania wiadomości urządzenia do chmury w magazynie obiektów blob platformy Azure. Jednak w niektórych scenariuszach nie można łatwo mapować dane, które Twojego urządzenia wysyłają do stosunkowo mały wiadomości urządzenia do chmury, które akceptuje Centrum IoT. Na przykład:

* Duże pliki, które zawierają obrazów
* Filmy wideo
* Dane wibrację próbkowanych zgodnie o dużej częstotliwości
* Niektóre formularz wstępnie przetworzonych danych.

Pliki te są zwykle partii przetwarzania w chmurze za pomocą narzędzi takich jak [fabryki danych Azure](../data-factory/introduction.md) lub [Hadoop](../hdinsight/index.yml) stosu. Gdy trzeba wyżynne pliki z urządzenia, można nadal używać bezpieczeństwa i niezawodności Centrum IoT.

Na końcu tego samouczka możesz uruchomić dwie aplikacje konsoli Java:

* **Symulowane urządzenie**, zmodyfikowanej wersji aplikacji utworzonej w samouczku [chmury urządzenia wysyłania wiadomości z Centrum IoT]. Ta aplikacja przekazuje plik do magazynu przy użyciu identyfikatora URI połączenia SAS udostępniane przez Centrum IoT.
* **Odczyt pliku przekazywania powiadomień**, która odbiera powiadomienia o przekazywania plików z Centrum IoT.

> [!NOTE]
> Centrum IoT obsługuje wiele platform urządzeń i języków (w tym C, .NET i Javascript) za pomocą urządzenia Azure IoT zestawów SDK. Zapoznaj się [Centrum deweloperów Azure IoT] instrukcje krok po kroku dotyczące sposobu Podłącz urządzenie do Centrum IoT Azure.

Do wykonania kroków tego samouczka niezbędne są następujące elementy:

* Najnowszy zestaw [Java SE Development Kit 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
* [Maven 3](https://maven.apache.org/install.html)
* Aktywne konto platformy Azure. (Jeśli nie masz konta, możesz utworzyć [bezpłatne konto](http://azure.microsoft.com/pricing/free-trial/) w zaledwie kilka minut.)

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]

## <a name="upload-a-file-from-a-device-app"></a>Przekaż plik z aplikacji przez urządzenia

W tej sekcji możesz zmodyfikować aplikację urządzenia, utworzony w [wysyłać chmury do urządzenia z Centrum IoT](iot-hub-java-java-c2d.md) można przekazać pliku do Centrum IoT.

1. Skopiuj plik obrazu do `simulated-device` folder i zmień jego nazwę `myimage.png`.

1. Za pomocą edytora tekstu, otwórz `simulated-device\src\main\java\com\mycompany\app\App.java` pliku.

1. Deklaracja zmiennej, aby dodać **aplikacji** klasy:

    ```java
    private static String fileName = "myimage.png";
    ```

1. Można przetworzyć wywołania zwrotnego komunikatach przekazywania pliku, należy dodać następujące zagnieżdżona klasa na **aplikacji** klasy:

    ```java
    // Define a callback method to print status codes from IoT Hub.
    protected static class FileUploadStatusCallBack implements IotHubEventCallback {
      public void execute(IotHubStatusCode status, Object context) {
        System.out.println("IoT Hub responded to file upload for " + fileName
            + " operation with status " + status.name());
      }
    }
    ```

1. Aby przekazać obrazy do Centrum IoT, dodaj następującą metodę do **aplikacji** klasy przekazywać obrazy do Centrum IoT:

    ```java
    // Use IoT Hub to upload a file asynchronously to Azure blob storage.
    private static void uploadFile(String fullFileName) throws FileNotFoundException, IOException
    {
      File file = new File(fullFileName);
      InputStream inputStream = new FileInputStream(file);
      long streamLength = file.length();

      client.uploadToBlobAsync(fileName, inputStream, streamLength, new FileUploadStatusCallBack(), null);
    }
    ```

1. Modyfikowanie **głównego** metodę do wywołania **uploadFile** — metoda, jak pokazano w poniższy fragment kodu:

    ```java
    client.open();

    try
    {
      // Get the filename and start the upload.
      String fullFileName = System.getProperty("user.dir") + File.separator + fileName;
      uploadFile(fullFileName);
      System.out.println("File upload started with success");
    }
    catch (Exception e)
    {
      System.out.println("Exception uploading file: " + e.getCause() + " \nERROR: " + e.getMessage());
    }

    MessageSender sender = new MessageSender();
    ```

1. Użyj następującego polecenia, aby utworzyć **symulowane urządzenie** aplikacji i sprawdź, czy błędy:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="receive-a-file-upload-notification"></a>Otrzymasz powiadomienie przekazywania pliku

W tej sekcji utworzysz aplikację konsoli języka Java, która odbiera komunikaty powiadomień dotyczących przekazywania plików z Centrum IoT.

Należy **iothubowner** parametry połączenia Centrum IoT do ukończenia tej sekcji. Można znaleźć w ciągu połączenia [portalu Azure](https://portal.azure.com/) na **zasady dostępu współużytkowanego** bloku.

1. Utwórz projekt Maven o nazwie **odczytu pliku przekazywania powiadomień** za pomocą następującego polecenia z wiersza polecenia. Należy zauważyć, że to polecenie jest pojedynczą, długie polecenie:

    ```cmd/sh
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=read-file-upload-notification -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

1. Wierszu polecenia przejdź do nowego `read-file-upload-notification` folderu.

1. Za pomocą edytora tekstu, otwórz `pom.xml` w pliku `read-file-upload-notification` folderu i dodaj następujące zależności do **zależności** węzła. Dodawanie zależności pozwala na użycie **Centrum iothub-java--klient usługi** pakietu aplikacji do komunikowania się z usługą Centrum IoT:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-service-client</artifactId>
      <version>1.7.23</version>
    </dependency>
    ```

    > [!NOTE]
    > Najnowszą wersję można sprawdzić **klienta w przypadku usługi iot** przy użyciu [wyszukiwania Maven](http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-service-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22).

1. Zapisz i Zamknij `pom.xml` pliku.

1. Za pomocą edytora tekstu, otwórz `read-file-upload-notification\src\main\java\com\mycompany\app\App.java` pliku.

1. Dodaj do pliku następujące instrukcje **importowania**:

    ```java
    import com.microsoft.azure.sdk.iot.service.*;
    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.util.concurrent.ExecutorService;
    import java.util.concurrent.Executors;
    ```

1. Dodaj następujące zmienne poziomie klasy do **aplikacji** klasy:

    ```java
    private static final String connectionString = "{Your IoT Hub connection string}";
    private static final IotHubServiceClientProtocol protocol = IotHubServiceClientProtocol.AMQPS;
    private static FileUploadNotificationReceiver fileUploadNotificationReceiver = null;
    ```

1. Aby wydrukować informacji na temat przekazywania pliku w konsoli, należy dodać następujące zagnieżdżona klasa na **aplikacji** klasy:

    ```java
    // Create a thread to receive file upload notifications.
    private static class ShowFileUploadNotifications implements Runnable {
      public void run() {
        try {
          while (true) {
            System.out.println("Recieve file upload notifications...");
            FileUploadNotification fileUploadNotification = fileUploadNotificationReceiver.receive();
            if (fileUploadNotification != null) {
              System.out.println("File Upload notification received");
              System.out.println("Device Id : " + fileUploadNotification.getDeviceId());
              System.out.println("Blob Uri: " + fileUploadNotification.getBlobUri());
              System.out.println("Blob Name: " + fileUploadNotification.getBlobName());
              System.out.println("Last Updated : " + fileUploadNotification.getLastUpdatedTimeDate());
              System.out.println("Blob Size (Bytes): " + fileUploadNotification.getBlobSizeInBytes());
              System.out.println("Enqueued Time: " + fileUploadNotification.getEnqueuedTimeUtcDate());
            }
          }
        } catch (Exception ex) {
          System.out.println("Exception reading reported properties: " + ex.getMessage());
        }
      }
    }
    ```

1. Aby rozpocząć wątku, który odbiera powiadomienia o przekazywania plików, Dodaj następujący kod do **głównego** metody:

    ```java
    public static void main(String[] args) throws IOException, URISyntaxException, Exception {
      ServiceClient serviceClient = ServiceClient.createFromConnectionString(connectionString, protocol);

      if (serviceClient != null) {
        serviceClient.open();

        // Get a file upload notification receiver from the ServiceClient.
        fileUploadNotificationReceiver = serviceClient.getFileUploadNotificationReceiver();
        fileUploadNotificationReceiver.open();

        // Start the thread to receive file upload notifications.
        ShowFileUploadNotifications showFileUploadNotifications = new ShowFileUploadNotifications();
        ExecutorService executor = Executors.newFixedThreadPool(1);
        executor.execute(showFileUploadNotifications);

        System.out.println("Press ENTER to exit.");
        System.in.read();
        executor.shutdownNow();
        System.out.println("Shutting down sample...");
        fileUploadNotificationReceiver.close();
        serviceClient.close();
      }
    }
    ```

1. Zapisz i Zamknij `read-file-upload-notification\src\main\java\com\mycompany\app\App.java` pliku.

1. Użyj następującego polecenia, aby utworzyć **odczytu pliku przekazywania powiadomień** aplikacji i sprawdź, czy błędy:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="run-the-applications"></a>Uruchamianie aplikacji

Teraz wszystko jest gotowe do uruchomienia aplikacji.

W wierszu polecenia `read-file-upload-notification` folderu, uruchom następujące polecenie:

```cmd/sh
mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
```

W wierszu polecenia `simulated-device` folderu, uruchom następujące polecenie:

```cmd/sh
mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
```

Poniższy zrzut ekranu przedstawia dane wyjściowe z **symulowane urządzenie** aplikacji:

![Dane wyjściowe z aplikacji symulowane urządzenie](media/iot-hub-java-java-upload/simulated-device.png)

Poniższy zrzut ekranu przedstawia dane wyjściowe z **odczytu pliku przekazywania powiadomień** aplikacji:

![Dane wyjściowe z aplikacji odczytu pliku przekazywania — powiadomienie](media/iot-hub-java-java-upload/read-file-upload-notification.png)

Portal służy do wyświetlania przekazany plik w kontenerze magazynu, które zostały skonfigurowane:

![Przekazany plik](media/iot-hub-java-java-upload/uploaded-file.png)

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku przedstawiono sposób korzystania z funkcji przekazywania pliku Centrum IoT uprościć przekazywania plików z urządzeń. Można kontynuować poznać funkcje Centrum IoT i scenariusze z następujących artykułów:

* [Programowo tworzenia Centrum IoT][lnk-create-hub]
* [Wprowadzenie do zestawu SDK C][lnk-c-sdk]
* [Zestawy SDK Azure IoT][lnk-sdks]

Aby dokładniej analizować możliwości Centrum IoT, zobacz:

* [Symuluje urządzenia IoT krawędzi][lnk-iotedge]

<!-- Images. -->

[50]: ./media/iot-hub-csharp-csharp-file-upload/run-apps1.png
[1]: ./media/iot-hub-csharp-csharp-file-upload/image-properties.png
[2]: ./media/iot-hub-csharp-csharp-file-upload/file-upload-project-csharp1.png
[3]: ./media/iot-hub-csharp-csharp-file-upload/enable-file-notifications.png

<!-- Links -->



[Centrum deweloperów Azure IoT]: http://azure.microsoft.com/develop/iot

[Transient Fault Handling]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[Azure Storage]:../storage/common/storage-create-storage-account.md#create-a-storage-account
[lnk-configure-upload]: iot-hub-configure-file-upload.md
[Azure IoT service SDK NuGet package]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[lnk-create-hub]: iot-hub-rm-template-powershell.md
[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md


