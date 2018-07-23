---
title: Komunikaty z chmury do urządzenia za pomocą usługi Azure IoT Hub (.NET) | Dokumentacja firmy Microsoft
description: Jak wysyłać komunikaty z chmury do urządzenia na urządzeniu z usługi Azure IoT hub przy użyciu zestawów SDK usługi Azure IoT dla platformy .NET. Możesz zmodyfikować aplikacji urządzenia, aby odbierać komunikaty z chmury do urządzenia i modyfikować aplikacji zaplecza, do wysyłania komunikatów z chmury do urządzeń.
author: fsautomata
manager: ''
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 08/24/2017
ms.author: elioda
ms.openlocfilehash: e2090977813f5335f2170ff543bde19cd8bf123b
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/20/2018
ms.locfileid: "39185798"
---
# <a name="send-messages-from-the-cloud-to-your-device-with-iot-hub-net"></a>Wysyłanie komunikatów z chmury do urządzenia z IoT Hub (.NET)
[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

## <a name="introduction"></a>Wprowadzenie
Usługa Azure IoT Hub to w pełni zarządzana usługa, która ułatwia włączanie bezpieczną i niezawodną komunikację dwukierunkową między milionami urządzeń i zapleczem rozwiązania. [Rozpoczynanie pracy z usługą IoT Hub] samouczek pokazuje sposób tworzenia Centrum IoT hub, aprowizować w nim tożsamości urządzenia i kodu aplikacji urządzenia, która wysyła komunikaty z urządzenia do chmury.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Ten samouczek opiera się [Rozpoczynanie pracy z usługą IoT Hub]. Przedstawia on sposób do:

* Z zapleczem rozwiązania wysyłanie komunikatów z chmury do urządzeń do pojedynczego urządzenia za pomocą usługi IoT Hub.
* Odbieranie komunikatów z chmury do urządzeń na urządzeniu.
* Z zapleczem rozwiązania, żądania potwierdzenia dostarczania (*opinii*) dla wiadomości wysyłanych do urządzenia z usługi IoT Hub.

Można znaleźć więcej informacji na temat komunikatów z chmury do urządzeń w [usługi IoT Hub — przewodnik dewelopera][IoT Hub developer guide - C2D].

Na końcu tego samouczka możesz uruchomić dwie aplikacje konsolowe .NET:

* **SimulatedDevice**, zmodyfikowanej wersji aplikacji utworzonej w [Rozpoczynanie pracy z usługą IoT Hub], który nawiązuje połączenie z Centrum IoT i odbiera komunikaty z chmury do urządzenia.
* **SendCloudToDevice**, która wysyła komunikat chmury do urządzenia do aplikacji urządzenia za pomocą usługi IoT Hub i odbiera jego potwierdzenie dostawy.

> [!NOTE]
> Usługi IoT Hub obsługuje zestaw SDK na wielu platformach i językach (w tym C, Java i Javascript) poprzez [Zestawy SDK platformy Azure w zakresie urządzeń IoT]. Aby uzyskać instrukcje krok po kroku dotyczące łączenia urządzenia do kodu w tym samouczku i ogólnie do usługi Azure IoT Hub, zobacz [Przewodnik dla deweloperów usługi IoT Hub].
> 
> 

Do wykonania kroków tego samouczka niezbędne są następujące elementy:

* Program Visual Studio 2015 lub Visual Studio 2017
* Aktywne konto platformy Azure. (Jeśli go nie masz, możesz utworzyć [bezpłatne konto próbne][lnk-free-trial] w zaledwie kilka minut).

## <a name="receive-messages-in-the-device-app"></a>Odbieranie wiadomości w aplikacji urządzenia
W tej sekcji zmodyfikujesz aplikację urządzenia utworzone w [Rozpoczynanie pracy z usługą IoT Hub] do odbierania komunikatów z chmury do urządzeń z usługi IoT hub.

1. W programie Visual Studio w **SimulatedDevice** projektu, dodaj następującą metodę do **Program** klasy.
   
        private static async void ReceiveC2dAsync()
        {
            Console.WriteLine("\nReceiving cloud to device messages from service");
            while (true)
            {
                Message receivedMessage = await deviceClient.ReceiveAsync();
                if (receivedMessage == null) continue;
   
                Console.ForegroundColor = ConsoleColor.Yellow;
                Console.WriteLine("Received message: {0}", Encoding.ASCII.GetString(receivedMessage.GetBytes()));
                Console.ResetColor();
   
                await deviceClient.CompleteAsync(receivedMessage);
            }
        }
   
    `ReceiveAsync` Metoda asynchronicznie zwraca odebraną wiadomość w momencie odebrania przez urządzenie. Zwraca *null* po upływie limitu czasu specifiable (w tym przypadku używana jest domyślna jedną minutę). Jeśli aplikacja otrzyma *null*, powinno ono nadal czekać na nowe wiadomości. To wymaganie jest przyczyna `if (receivedMessage == null) continue` wiersza.
   
    Wywołanie `CompleteAsync()` powiadamia usługi IoT Hub pomyślnie przetworzyć wiadomości. Komunikat można bezpiecznie usunąć z kolejki urządzenia. Jeśli coś się stało z który uniemożliwił ukończenie przetwarzania komunikatu aplikacji urządzenia usługi IoT Hub dostarczy go ponownie. Ważne jest, następnie wiadomość przetwarzania logiki w aplikacji urządzenia jest *idempotentne*, dzięki czemu odbieranie ten sam komunikat wielokrotnie daje ten sam wynik. Aplikacja może również tymczasowo porzucić wiadomości, co skutkuje zachowaniem komunikatu w kolejce do przyszłego użytku w usłudze IoT hub. Lub aplikacji można odrzucić wiadomości, które trwale usuwa komunikat z kolejki. Aby uzyskać więcej informacji na temat cyklu życia komunikatów z chmury do urządzenia, zobacz [usługi IoT Hub — przewodnik dewelopera][IoT Hub developer guide - C2D].
   
   > [!NOTE]
   > Korzystając z protokołu HTTPS zamiast protokołu MQTT, AMQP jako transportu, `ReceiveAsync` metoda zwraca natychmiast. Obsługiwane wzorzec komunikatów z chmury do urządzeń za pomocą protokołu HTTPS jest sporadycznie podłączone urządzenia Sprawdź komunikaty rzadko (mniej niż co 25 minut). Wystawianie więcej HTTPS otrzymuje wyniki w usłudze IoT Hub ograniczania żądań. Aby uzyskać więcej informacji na temat różnic między Obsługa protokołu MQTT, AMQP i protokołu HTTPS i ograniczania przepustowości usługi IoT Hub, zobacz [usługi IoT Hub — przewodnik dewelopera][IoT Hub developer guide - C2D].
   > 
   > 
2. Dodaj następującą metodę w **Main** metody, po prawej stronie przed `Console.ReadLine()` wiersza:
   
        ReceiveC2dAsync();

> [!NOTE]
> Sake dla uproszczenia w tym samouczku nie implementuje żadnych zasad ponawiania. W kodzie produkcyjnym należy wdrożyć zasady ponawiania (np. wycofywanie wykładnicze) zgodnie z sugestią w artykule z witryny MSDN [Obsługa błędu przejściowego].
> 
> 

## <a name="send-a-cloud-to-device-message"></a>Wysyłanie komunikatów chmura urządzenie
W tej sekcji służy do pisania aplikacji konsolowej .NET, która wysyła komunikaty z chmury do urządzenia do aplikacji urządzenia.

1. W bieżącym rozwiązaniu programu Visual Studio Utwórz projekt aplikacji klasycznej Visual C# za pomocą **aplikację Konsolową** szablonu projektu. Nadaj projektowi nazwę **SendCloudToDevice**.
   
    ![Nowy projekt w programie Visual Studio][20]
2. W Eksploratorze rozwiązań kliknij rozwiązanie prawym przyciskiem myszy, a następnie kliknij przycisk **Zarządzaj pakietami NuGet dla rozwiązania...** . 
   
    Ta akcja powoduje otwarcie **Zarządzaj pakietami NuGet** okna.
3. Wyszukaj **Microsoft.Azure.Devices**, kliknij przycisk **zainstalować**i zaakceptuj warunki użytkowania. 
   
    To spowoduje pobranie, spowoduje zainstalowanie i dodanie odwołania do [Pakiet NuGet zestawu SDK usługi w usłudze Azure IoT].

4. Dodaj następującą instrukcję `using` w górnej części pliku **Program.cs**:
   
        using Microsoft.Azure.Devices;
5. Dodaj następujące pola do klasy **Program**: Zastąp wartość symbolu zastępczego parametrami połączenia Centrum IoT z [Rozpoczynanie pracy z usługą IoT Hub]:
   
        static ServiceClient serviceClient;
        static string connectionString = "{iot hub connection string}";
6. Dodaj następującą metodę do klasy **Program**:
   
        private async static Task SendCloudToDeviceMessageAsync()
        {
            var commandMessage = new Message(Encoding.ASCII.GetBytes("Cloud to device message."));
            await serviceClient.SendAsync("myFirstDevice", commandMessage);
        }
   
    Ta metoda wysyła nowy komunikat z chmury do urządzenia na urządzeniu z Identyfikatorem, `myFirstDevice`. Ten parametr należy zmienić tylko wtedy, gdy został zmodyfikowany z działaniem używanym w [Rozpoczynanie pracy z usługą IoT Hub].
7. Na koniec dodaj następujące wiersze do metody **Główne**:
   
        Console.WriteLine("Send Cloud-to-Device message\n");
        serviceClient = ServiceClient.CreateFromConnectionString(connectionString);
   
        Console.WriteLine("Press any key to send a C2D message.");
        Console.ReadLine();
        SendCloudToDeviceMessageAsync().Wait();
        Console.ReadLine();
8. Z poziomu programu Visual Studio, kliknij prawym przyciskiem myszy rozwiązanie i wybierz **Ustaw projekty startowe...** . Wybierz **wiele projektów startowych**, a następnie wybierz **Start** akcji dla **ReadDeviceToCloudMessages**, **SimulatedDevice**, i **SendCloudToDevice**.
9. Naciśnij klawisz **F5**. Należy uruchomić wszystkie trzy aplikacje. Wybierz **SendCloudToDevice** systemu windows, a następnie naciśnij klawisz **Enter**. Powinien zostać wyświetlony komunikat odbierane przez aplikację urządzenia.
   
   ![Odbieranie komunikatów aplikacji][21]

## <a name="receive-delivery-feedback"></a>Odbieranie opinii dostarczania
Jest możliwe do żądania potwierdzeń dostarczenia (lub data jej wygaśnięcia) z usługi IoT Hub dla każdego komunikatu chmury do urządzenia. Ta opcja umożliwia zapleczu rozwiązania łatwo informować logiki ponawiania próby lub odszkodowania. Aby uzyskać więcej informacji dotyczących opinii z chmury do urządzenia, zobacz [usługi IoT Hub — przewodnik dewelopera][IoT Hub developer guide - C2D].

W tej sekcji zmodyfikujesz **SendCloudToDevice** aplikacji na żądanie opinii i otrzymywać usługi IoT Hub.

1. W programie Visual Studio w **SendCloudToDevice** projektu, dodaj następującą metodę do **Program** klasy.
   
        private async static void ReceiveFeedbackAsync()
        {
            var feedbackReceiver = serviceClient.GetFeedbackReceiver();
   
            Console.WriteLine("\nReceiving c2d feedback from service");
            while (true)
            {
                var feedbackBatch = await feedbackReceiver.ReceiveAsync();
                if (feedbackBatch == null) continue;
   
                Console.ForegroundColor = ConsoleColor.Yellow;
                Console.WriteLine("Received feedback: {0}", string.Join(", ", feedbackBatch.Records.Select(f => f.StatusCode)));
                Console.ResetColor();
   
                await feedbackReceiver.CompleteAsync(feedbackBatch);
            }
        }
   
    Należy zauważyć, że ten wzorzec odbioru jest taka sama, jak używaną do odbierania komunikatów z chmury do urządzeń z aplikacji urządzenia.
2. Dodaj następującą metodę w **Main** metoda zaraz po `serviceClient = ServiceClient.CreateFromConnectionString(connectionString)` wiersza:
   
        ReceiveFeedbackAsync();
3. Aby zażądać opinii w dostarczaniu wiadomości chmury do urządzenia, należy określić właściwość **SendCloudToDeviceMessageAsync** metody. Dodaj następujący wiersz, od razu po `var commandMessage = new Message(...);` wiersza:
   
        commandMessage.Ack = DeliveryAcknowledgement.Full;
4. Uruchamianie aplikacji, naciskając klawisz **F5**. Powinny być widoczne wszystkie trzy aplikacje start. Wybierz **SendCloudToDevice** systemu windows, a następnie naciśnij klawisz **Enter**. Powinien zostać wyświetlony komunikat odbierane przez aplikację urządzenia i po kilku sekundach komunikat opinii odbierane przez użytkownika **SendCloudToDevice** aplikacji.
   
   ![Odbieranie komunikatów aplikacji][22]

> [!NOTE]
> Sake dla uproszczenia w tym samouczku nie implementuje żadnych zasad ponawiania. W kodzie produkcyjnym należy wdrożyć zasady ponawiania (np. wycofywanie wykładnicze) zgodnie z sugestią w artykule z witryny MSDN [Obsługa błędu przejściowego].
> 
> 

## <a name="next-steps"></a>Kolejne kroki
W tym samouczku przedstawiono sposób wysyłania i odbierania komunikatów z chmury do urządzeń. 

Aby wyświetlić przykłady kompletne rozwiązania end-to-end, które używają usługi IoT Hub, zobacz [Akcelerator rozwiązań IoT zdalnego monitorowania platformy Azure].

Aby dowiedzieć się więcej na temat opracowywania rozwiązań usługi IoT Hub, zobacz [Przewodnik dla deweloperów usługi IoT Hub].

<!-- Images -->
[20]: ./media/iot-hub-csharp-csharp-c2d/create-identity-csharp1.png
[21]: ./media/iot-hub-csharp-csharp-c2d/sendc2d1.png
[22]: ./media/iot-hub-csharp-csharp-c2d/sendc2d2.png

<!-- Links -->

[Pakiet NuGet zestawu SDK usługi w usłudze Azure IoT]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[Obsługa błędu przejściowego]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[IoT Hub developer guide - C2D]: iot-hub-devguide-messaging.md

[Przewodnik dla deweloperów usługi IoT Hub]: iot-hub-devguide.md
[Rozpoczynanie pracy z usługą IoT Hub]: quickstart-send-telemetry-dotnet.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[Akcelerator rozwiązań IoT zdalnego monitorowania platformy Azure]: https://docs.microsoft.com/azure/iot-suite/
[Zestawy SDK platformy Azure w zakresie urządzeń IoT]: iot-hub-devguide-sdks.md