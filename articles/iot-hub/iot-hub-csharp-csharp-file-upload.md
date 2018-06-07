---
title: Przekazywanie plików z urządzenia do Centrum IoT Azure z platformą .NET | Dokumentacja firmy Microsoft
description: Sposób przekazywania plików z urządzenia do chmury przy użyciu urządzenia Azure IoT SDK dla platformy .NET. Przekazano pliki są przechowywane w kontenerze obiektu blob magazynu Azure.
author: fsautomata
manager: ''
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 07/04/2017
ms.author: elioda
ms.openlocfilehash: 8c57f93a755d01dc17b369e712285c2ac8f0ef37
ms.sourcegitcommit: 6cf20e87414dedd0d4f0ae644696151e728633b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/06/2018
ms.locfileid: "34807496"
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub-using-net"></a>Przekazywanie plików z urządzenia do chmury z Centrum IoT przy użyciu platformy .NET

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

W tym samouczku opiera się na kodzie w [wysyłać chmury do urządzenia z Centrum IoT](iot-hub-csharp-csharp-c2d.md) samouczkiem, aby opisano, jak korzystać z funkcji przekazywania plików z Centrum IoT. Jak przedstawiono na:

- Bezpieczne udostępnianie urządzenia platformy Azure blob identyfikatora URI pobierania pliku.
- Powiadomienia o przekazywania plików Centrum IoT umożliwia wyzwalanie przetwarzania plików w sieci wewnętrznej aplikacji.

[Rozpoczynanie pracy z Centrum IoT](iot-hub-csharp-csharp-getstarted.md) i [wysyłać chmury do urządzenia z Centrum IoT](iot-hub-csharp-csharp-c2d.md) samouczki przedstawiają podstawowych funkcji obsługi komunikatów z Centrum IoT urządzenia do chmury i chmury do urządzenia. [Wiadomości procesu urządzenia do chmury](tutorial-routing.md) samouczek przedstawia sposób niezawodny sposób przechowywania wiadomości urządzenia do chmury w magazynie obiektów blob platformy Azure. Jednak w niektórych scenariuszach nie można łatwo mapować dane, które Twojego urządzenia wysyłają do stosunkowo mały wiadomości urządzenia do chmury, które akceptuje Centrum IoT. Na przykład:

* Duże pliki, które zawierają obrazów
* Filmy wideo
* Dane wibrację próbkowanych zgodnie o dużej częstotliwości
* Niektóre formularz wstępnie przetworzonych danych

Pliki te są zwykle partii przetwarzania w chmurze za pomocą narzędzi takich jak [fabryki danych Azure](../data-factory/introduction.md) lub [Hadoop](../hdinsight/index.yml) stosu. Gdy chcesz przekazać pliki z urządzenia, można nadal używać bezpieczeństwa i niezawodności Centrum IoT.

Na końcu tego samouczka możesz uruchomić dwóch aplikacji konsoli .NET:

* **SimulatedDevice**, zmodyfikowanej wersji aplikacji utworzonych w [wysyłać chmury do urządzenia z Centrum IoT](iot-hub-csharp-csharp-c2d.md) samouczka. Ta aplikacja przekazuje plik do magazynu przy użyciu identyfikatora URI połączenia SAS udostępniane przez Centrum IoT.
* **ReadFileUploadNotification**, która odbiera powiadomienia o przekazywania plików z Centrum IoT.

> [!NOTE]
> Centrum IoT obsługuje wiele platform urządzeń i języków (w tym C, Java i Javascript) za pomocą urządzenia Azure IoT zestawów SDK. Zapoznaj się [Centrum deweloperów Azure IoT] instrukcje krok po kroku dotyczące sposobu Podłącz urządzenie do Centrum IoT Azure.

Do wykonania kroków tego samouczka niezbędne są następujące elementy:

* Program Visual Studio 2015 lub Visual Studio 2017
* Aktywne konto platformy Azure. (Jeśli go nie masz, możesz utworzyć [bezpłatne konto próbne][lnk-free-trial] w zaledwie kilka minut).

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]

## <a name="upload-a-file-from-a-device-app"></a>Przekaż plik z aplikacji przez urządzenia

W tej sekcji możesz zmodyfikować aplikację urządzenia, utworzony w [wysyłać chmury do urządzenia z Centrum IoT](iot-hub-csharp-csharp-c2d.md) odbierać komunikaty z chmury do urządzenia z Centrum IoT.

1. W programie Visual Studio, kliknij prawym przyciskiem myszy **SimulatedDevice** projektu, kliknij przycisk **Dodaj**, a następnie kliknij przycisk **istniejący element**. Przejdź do pliku obrazu i dołączyć go w projekcie. Ten samouczek zakłada nosi nazwę obrazu `image.jpg`.

1. Kliknij prawym przyciskiem myszy na obrazie, a następnie kliknij przycisk **właściwości**. Upewnij się, że **Kopiuj do katalogu wyjściowego** ustawiono **skopiuj zawsze**.

    ![][1]

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

    `UploadToBlobAsync` Metoda przyjmuje źródła nazwy i strumienia pliku, plik do przekazania i obsługuje przekazywanie do magazynu. Aplikacji konsoli przedstawia czas, jaki zajmuje można przekazać pliku.

1. Dodaj następującą metodę w **Main** metoda, przed `Console.ReadLine()` wiersza:

    ```csharp
    SendToBlobAsync();
    ```

> [!NOTE]
> Sake na prostotę w tym samouczku nie implementuje wszystkie zasady ponawiania. W kodzie produkcyjnym, należy zaimplementować zasady ponawiania (na przykład wykładniczego wycofywania), zgodnie z sugestią podaną w artykuł w witrynie MSDN [Obsługa błędu przejściowego].

## <a name="receive-a-file-upload-notification"></a>Otrzymasz powiadomienie przekazywania pliku

W tej sekcji służy do pisania aplikacji konsoli .NET, który odbiera komunikaty powiadomień dotyczących przekazywania plików z Centrum IoT.

1. W bieżącym rozwiązaniu programu Visual Studio, należy utworzyć projekt Visual C# systemu Windows za pomocą **aplikacji konsoli** szablonu projektu. Nazwij projekt **ReadFileUploadNotification**.

    ![Nowy projekt w programie Visual Studio][2]

1. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy **ReadFileUploadNotification** projektu, a następnie kliknij przycisk **Zarządzaj pakietami NuGet...** .

1. W **Menedżera pakietów NuGet** okna, wyszukiwanie **Microsoft.Azure.Devices**, kliknij przycisk **zainstalować**i zaakceptuj warunki użytkowania.

    Ta akcja spowoduje pobranie, instaluje i dodaje odwołanie do [Pakiet NuGet zestawu SDK usługi IoT Azure] w **ReadFileUploadNotification** projektu.

1. W **Program.cs** plików, Dodaj następujące instrukcje w górnej części pliku:

    ```csharp
    using Microsoft.Azure.Devices;
    ```

1. Dodaj następujące pola do klasy **Program**: Zastąp symbol zastępczy wartości z parametrów połączenia Centrum IoT z [Rozpoczynanie pracy z Centrum IoT]:

    ```csharp
    static ServiceClient serviceClient;
    static string connectionString = "{iot hub connection string}";
    ```

1. Dodaj następującą metodę do klasy **Program**:

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
            Console.WriteLine("Received file upload noticiation: {0}", string.Join(", ", fileUploadNotification.BlobName));
            Console.ResetColor();

            await notificationReceiver.CompleteAsync(fileUploadNotification);
        }
    }
    ```

    Należy zauważyć, że ten wzorzec receive jest tym samym kluczem, używany do odbierania wiadomości chmury do urządzenia z aplikacjami urządzenia.

1. Na koniec dodaj następujące wiersze do metody **Główne**:

    ```csharp
    Console.WriteLine("Receive file upload notifications\n");
    serviceClient = ServiceClient.CreateFromConnectionString(connectionString);
    ReceiveFileUploadNotificationAsync();
    Console.WriteLine("Press Enter to exit\n");
    Console.ReadLine();
    ```

## <a name="run-the-applications"></a>Uruchamianie aplikacji

Teraz wszystko jest gotowe do uruchomienia aplikacji.

1. W programie Visual Studio, kliknij prawym przyciskiem myszy rozwiązanie, a następnie wybierz **projektów startowych ustawić**. Wybierz **wiele projektów startowych**, a następnie wybierz pozycję **Start** akcji **ReadFileUploadNotification** i **SimulatedDevice**.

1. Naciśnij klawisz **F5**. Należy zacząć obydwu aplikacji. Powinna zostać wyświetlona przekazywania zostało ukończone w jednej aplikacji konsoli i wysyłania komunikatu powiadomienia odebrana w aplikacji konsoli. Można użyć [Azure Portal] lub Visual Studio Eksploratora serwera do sprawdzenia obecności przekazanego pliku na koncie magazynu Azure.

    ![][50]

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku przedstawiono sposób korzystania z funkcji przekazywania pliku Centrum IoT uprościć przekazywania plików z urządzeń. Można kontynuować poznać funkcje Centrum IoT i scenariusze z następujących artykułów:

* [Programowo tworzenia Centrum IoT][lnk-create-hub]
* [Wprowadzenie do zestawu SDK C][lnk-c-sdk]
* [Zestawy SDK Azure IoT][lnk-sdks]

Aby dokładniej analizować możliwości Centrum IoT, zobacz:

* [Wdrażanie rozwiązań SI na urządzeniach brzegowych przy użyciu usługi Azure IoT Edge][lnk-iotedge]

<!-- Images. -->

[50]: ./media/iot-hub-csharp-csharp-file-upload/run-apps1.png
[1]: ./media/iot-hub-csharp-csharp-file-upload/image-properties.png
[2]: ./media/iot-hub-csharp-csharp-file-upload/file-upload-project-csharp1.png

<!-- Links -->

[Azure Portal]: https://portal.azure.com/

[Centrum deweloperów Azure IoT]: http://azure.microsoft.com/develop/iot

[Obsługa błędu przejściowego]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[Pakiet NuGet zestawu SDK usługi IoT Azure]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[lnk-create-hub]: iot-hub-rm-template-powershell.md
[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
