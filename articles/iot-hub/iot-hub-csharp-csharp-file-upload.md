---
title: Przekazywanie plików z urządzeń do usługi Azure IoT Hub przy użyciu platformy .NET | Dokumentacja firmy Microsoft
description: Sposób przekazywania plików z urządzenia do chmury przy użyciu zestawu SDK urządzeń Azure IoT dla platformy .NET. Przekazane pliki są przechowywane w kontenerze obiektów blob usługi Azure storage.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 07/04/2017
ms.author: robinsh
ms.openlocfilehash: afa8a77a1c2eabce11af5730ffc5e7b768983589
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59046773"
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub-using-net"></a>Przekazywanie plików z urządzenia do chmury za pomocą usługi IoT Hub przy użyciu platformy .NET

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

Ten samouczek opiera się na kodzie w [wysyłanie komunikatów z chmury do urządzeń z usługą IoT Hub](iot-hub-csharp-csharp-c2d.md) samouczka, aby pokazują, jak korzystać z funkcji przekazywania plików usługi IoT Hub. Przedstawia on sposób do:

- Bezpiecznie przekazać urządzenia z systemem Azure identyfikator URI obiektu blob przekazywania pliku.

- Powiadomienia przekazywania pliku usługi IoT Hub umożliwia wyzwalanie przetwarzania pliku w aplikacji zaplecza.

[Wysyłanie danych telemetrycznych z urządzenia do usługi IoT hub](quickstart-send-telemetry-dotnet.md) i [wysyłanie komunikatów z chmury do urządzeń z usługą IoT Hub](iot-hub-csharp-csharp-c2d.md) artykuły pokazują podstawowe funkcje obsługi komunikatów usługi IoT Hub urządzenia do chmury i z chmury do urządzeń. [Skonfigurować Routing komunikatów usługi IoT Hub](tutorial-routing.md) samouczku opisano sposób niezawodnego przechowywania komunikatów z urządzenia do chmury w usłudze Azure blob storage. Jednak w niektórych scenariuszach nie pozwala na łatwe mapowanie danych wysyłanych przez urządzenia do stosunkowo mały wiadomości urządzenia do chmury, które akceptuje usługi IoT Hub. Na przykład:

* Duże pliki, które zawierają obrazy
* Filmy wideo
* Wibracje danych próbkowania o wysokiej częstotliwości
* Pewnego rodzaju wstępnie przetworzonych danych

Te pliki to typowo wsadowego przetwarzania w chmurze przy użyciu narzędzi takich jak [usługi Azure Data Factory](../data-factory/introduction.md) lub [Hadoop](../hdinsight/index.yml) stosu. Podczas przekazywania plików z urządzenia, należy nadal używać zabezpieczeń i niezawodności usługi IoT Hub.

Na końcu tego samouczka, możesz uruchomić dwie aplikacje konsolowe .NET:

* **SimulatedDevice**, zmodyfikowanej wersji aplikacji utworzonej w [wysyłanie komunikatów z chmury do urządzeń z usługą IoT Hub](iot-hub-csharp-csharp-c2d.md) samouczka. Ta aplikacja przekazuje plik do magazynu przy użyciu identyfikatora URI sygnatury dostępu Współdzielonego, dostarczone przez Centrum IoT hub.

* **ReadFileUploadNotification**, która odbiera powiadomienia o przekazywania plików z usługi IoT hub.

> [!NOTE]
> Usługa IoT Hub obsługuje wiele platform urządzeń i językach (w tym C, Java i Javascript) za pomocą zestawów SDK urządzeń Azure IoT. Zapoznaj się [Centrum deweloperów Azure IoT](https://azure.microsoft.com/develop/iot) instrukcje krok po kroku dotyczące sposobu Podłącz urządzenie do usługi Azure IoT Hub.

Do wykonania kroków tego samouczka niezbędne są następujące elementy:

* Visual Studio 2017
* Aktywne konto platformy Azure. (Jeśli nie masz konta, możesz utworzyć [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/) w zaledwie kilka minut.)

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]

## <a name="upload-a-file-from-a-device-app"></a>Przekaż plik z aplikacji urządzenia

W tej sekcji zmodyfikujesz aplikację urządzenia utworzone w [wysyłanie komunikatów z chmury do urządzeń z usługą IoT Hub](iot-hub-csharp-csharp-c2d.md) do odbierania komunikatów z chmury do urządzeń z usługi IoT hub.

1. W programie Visual Studio, kliknij prawym przyciskiem myszy **SimulatedDevice** projektu, kliknij przycisk **Dodaj**, a następnie kliknij przycisk **istniejący element**. Przejdź do pliku obrazu, a następnie dołącz ją w projekcie. W tym samouczku założono, nosi nazwę obrazu `image.jpg`.

1. Kliknij prawym przyciskiem myszy na obrazie, a następnie kliknij przycisk **właściwości**. Upewnij się, że **Kopiuj do katalogu wyjściowego** ustawiono **zawsze Kopiuj**.

    ![Pokazano, gdzie można zaktualizować właściwości obrazu do skopiowania do katalogu wyjściowego](./media/iot-hub-csharp-csharp-file-upload/image-properties.png)

1. W **Program.cs** plików, Dodaj następujące instrukcje w górnej części pliku:

    ```csharp
    using System.IO;
    ```

1. Dodaj następującą metodę do klasy **Program**:

    ```csharp
    private static async void SendToBlobAsync()
    {
        string fileName = "image.jpg";
        Console.WriteLine("Uploading file: {0}", fileName);
        var watch = System.Diagnostics.Stopwatch.StartNew();

        using (var sourceData = new FileStream(@"image.jpg", FileMode.Open))
        {
            await deviceClient.UploadToBlobAsync(fileName, sourceData);
        }

        watch.Stop();
        Console.WriteLine("Time to upload file: {0}ms\n", watch.ElapsedMilliseconds);
    }
    ```

    `UploadToBlobAsync` Metoda przyjmuje źródła nazwy i strumienia pliku, plik do przekazania i obsługuje przekazywanie do usługi storage. Aplikacja konsoli przedstawia czas, jaki zajmuje można przekazać pliku.

1. Dodaj następującą metodę w **Main** metody, po prawej stronie przed `Console.ReadLine()` wiersza:

    ```csharp
    SendToBlobAsync();
    ```

> [!NOTE]
> Sake dla uproszczenia w tym samouczku nie implementuje żadnych zasad ponawiania. W kodzie produkcyjnym należy wdrożyć zasady ponawiania (np. wycofywanie wykładnicze) zgodnie z sugestią podaną w artykule [obsługi błędów przejściowych](/azure/architecture/best-practices/transient-faults).

## <a name="receive-a-file-upload-notification"></a>Otrzymywać powiadomienie o przekazywaniu pliku

W tej sekcji służy do pisania aplikacji konsolowej .NET, która odbiera komunikaty powiadomień przekazywania pliku z usługi IoT Hub.

1. W bieżącym rozwiązaniu programu Visual Studio Utwórz projekt Visual C# Windows za pomocą **aplikację Konsolową** szablonu projektu. Nadaj projektowi nazwę **ReadFileUploadNotification**.

    ![Nowy projekt w programie Visual Studio](./media/iot-hub-csharp-csharp-file-upload/file-upload-project-csharp1.png)

2. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy **ReadFileUploadNotification** projektu, a następnie kliknij przycisk **Zarządzaj pakietami NuGet...** .

3. W **Menedżera pakietów NuGet** okna, wyszukaj **Microsoft.Azure.Devices**, kliknij przycisk **zainstalować**i zaakceptuj warunki użytkowania.

    Ta akcja spowoduje pobranie, instaluje i dodanie odwołania do [pakietu NuGet zestawu SDK usługi Azure IoT](https://www.nuget.org/packages/Microsoft.Azure.Devices/) w **ReadFileUploadNotification** projektu.

4. W **Program.cs** plików, Dodaj następujące instrukcje w górnej części pliku:

    ```csharp
    using Microsoft.Azure.Devices;
    ```

5. Dodaj następujące pola do klasy **Program**: Zastąp wartość symbolu zastępczego parametrami połączenia Centrum IoT z [wysyłanie danych telemetrycznych z urządzenia do usługi IoT hub](quickstart-send-telemetry-dotnet.md):

    ```csharp
    static ServiceClient serviceClient;
    static string connectionString = "{iot hub connection string}";
    ```

6. Dodaj następującą metodę do klasy **Program**:

    ```csharp
    private async static void ReceiveFileUploadNotificationAsync()
    {
        var notificationReceiver = serviceClient.GetFileNotificationReceiver();

        Console.WriteLine("\nReceiving file upload notification from service");
        while (true)
        {
            var fileUploadNotification = await notificationReceiver.ReceiveAsync();
            if (fileUploadNotification == null) continue;

            Console.ForegroundColor = ConsoleColor.Yellow;
            Console.WriteLine("Received file upload notification: {0}", 
              string.Join(", ", fileUploadNotification.BlobName));
            Console.ResetColor();

            await notificationReceiver.CompleteAsync(fileUploadNotification);
        }
    }   
    ```

    Należy zauważyć, że ten wzorzec odbioru jest taka sama, jak używaną do odbierania komunikatów z chmury do urządzeń z aplikacji urządzenia.

7. Na koniec dodaj następujące wiersze do metody **Główne**:

    ```csharp
    Console.WriteLine("Receive file upload notifications\n");
    serviceClient = ServiceClient.CreateFromConnectionString(connectionString);
    ReceiveFileUploadNotificationAsync();
    Console.WriteLine("Press Enter to exit\n");
    Console.ReadLine();
    ```

## <a name="run-the-applications"></a>Uruchamianie aplikacji

Teraz wszystko jest gotowe do uruchomienia aplikacji.

1. W programie Visual Studio kliknij rozwiązanie prawym przyciskiem myszy, a następnie wybierz **Ustaw projekty startowe**. Wybierz **wiele projektów startowych**, a następnie wybierz **Start** akcji dla **ReadFileUploadNotification** i **SimulatedDevice**.

2. Naciśnij klawisz **F5**. Należy uruchomić obie aplikacje. Powinien zostać wyświetlony przekazywanie ukończone w jednej aplikacji konsoli i przekazywania komunikatu powiadomienia odbierane przez inną aplikację konsoli. Możesz użyć [witryny Azure portal](https://portal.azure.com/) lub Eksploratorze serwera Visual Studio pod kątem obecności przekazany plik na swoim koncie usługi Azure Storage.

    ![Zrzut ekranu przedstawiający ekran danych wyjściowych](./media/iot-hub-csharp-csharp-file-upload/run-apps1.png)

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku przedstawiono sposób użycia funkcji przekazywania plików usługi IoT Hub można uproszczenie przekazywania plików z urządzeń. Możesz kontynuować poznawanie funkcji Centrum IoT i scenariusze z następujących artykułów:

* [Programistyczne tworzenie Centrum IoT hub](iot-hub-rm-template-powershell.md)
* [Wprowadzenie do zestawu SDK języka C](iot-hub-device-sdk-c-intro.md)
* [Zestawy SDK usługi Azure IoT](iot-hub-devguide-sdks.md)

Aby bliżej zapoznać się z możliwościami usługi IoT Hub, zobacz:

* [Wdrażanie rozwiązań SI na urządzeniach brzegowych za pomocą usługi Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)

