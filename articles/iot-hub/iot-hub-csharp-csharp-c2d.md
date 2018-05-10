---
title: Komunikaty chmury do urządzenia z Centrum IoT Azure (.NET) | Dokumentacja firmy Microsoft
description: Jak wysyłać wiadomości chmury do urządzenia na urządzeniu z Centrum Azure IoT przy użyciu zestawów SDK IoT Azure dla platformy .NET. Możesz zmodyfikować aplikacji przez urządzenia do odbierania wiadomości chmury do urządzenia i modyfikowania aplikacji zaplecza, do wysyłania wiadomości chmury do urządzenia.
services: iot-hub
documentationcenter: .net
author: fsautomata
manager: timlt
editor: ''
ms.assetid: a31c05ed-6ec0-40f3-99ab-8fdd28b1a89a
ms.service: iot-hub
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/24/2017
ms.author: elioda
ms.openlocfilehash: b867976c637cdd4dd9b696382103c63f1af2e8b3
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="send-messages-from-the-cloud-to-your-device-with-iot-hub-net"></a>Wysyłanie komunikatów z chmury do urządzenia z Centrum IoT (.NET)
[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

## <a name="introduction"></a>Wprowadzenie
Centrum IoT Azure jest w pełni zarządzaną usługę, która umożliwia włączanie i niezawodności komunikację dwukierunkową między milionów urządzeń i zaplecze rozwiązania. [Rozpoczynanie pracy z Centrum IoT] samouczek przedstawia sposób tworzenia Centrum IoT, udostępnić tożsamość urządzenia w nim i kodem aplikacji urządzenia, który wysyła komunikaty urządzenia do chmury.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

W tym samouczku opiera się na [Rozpoczynanie pracy z Centrum IoT]. Jak przedstawiono na:

* Z sieci zaplecza rozwiązania wysyłać chmury do urządzenia do jednego urządzenia za pośrednictwem Centrum IoT.
* Komunikaty chmury do urządzenia na urządzeniu.
* Z sieci zaplecza rozwiązania, żądania potwierdzenia dostarczenia (*opinii*) dla wiadomości wysyłanych do urządzenia z Centrum IoT.

Można znaleźć więcej informacji na temat wiadomości chmury do urządzenia w [Centrum IoT — przewodnik dewelopera][IoT Hub developer guide - C2D].

Na końcu tego samouczka możesz uruchomić dwóch aplikacji konsoli .NET:

* **SimulatedDevice**, zmodyfikowanej wersji aplikacji utworzonych w [Rozpoczynanie pracy z Centrum IoT], która łączy się z Centrum IoT i odbiera komunikaty chmury do urządzenia.
* **SendCloudToDevice**, który wysyła komunikat chmury do urządzenia do aplikacji urządzenia za pośrednictwem Centrum IoT i następnie odbiera jego potwierdzenie dostarczenia.

> [!NOTE]
> Centrum IoT obsługuje zestawu SDK dla wielu platform urządzeń i języków (w tym C, Java i Javascript) za pośrednictwem [urządzenia Azure IoT SDK]. Aby uzyskać instrukcje krok po kroku dotyczące sposobu Podłącz urządzenie do kodu w tym samouczku i zwykle z Centrum IoT Azure, zobacz [Centrum IoT — przewodnik dewelopera].
> 
> 

Do wykonania kroków tego samouczka niezbędne są następujące elementy:

* Program Visual Studio 2015 lub Visual Studio 2017
* Aktywne konto platformy Azure. (Jeśli go nie masz, możesz utworzyć [bezpłatne konto próbne][lnk-free-trial] w zaledwie kilka minut).

## <a name="receive-messages-in-the-device-app"></a>Odbieranie komunikatów w aplikacji urządzeń
W tej sekcji będziesz modyfikowania aplikacji urządzenia został utworzony w [Rozpoczynanie pracy z Centrum IoT] odbierać komunikaty z chmury do urządzenia z Centrum IoT.

1. W programie Visual Studio w **SimulatedDevice** projekt, dodaj następującą metodę do **Program** klasy.
   
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
   
    `ReceiveAsync` Metoda asynchronicznie zwraca odebranego komunikatu w momencie otrzymania przez urządzenie. Zwraca *null* po upływie limitu czasu specifiable (w tym przypadku używana jest domyślna minutę). Gdy aplikacja odbiera *null*, powinno być kontynuowane oczekiwania nowych komunikatów. To wymaganie jest przyczyna `if (receivedMessage == null) continue` wiersza.
   
    Wywołanie `CompleteAsync()` powiadamia Centrum IoT pomyślnie przetworzyć wiadomości. Komunikat można bezpiecznie usunąć z kolejki urządzenia. Jeśli coś się stało, który uniemożliwił ukończenie przetwarzania komunikatu aplikacji urządzenia, Centrum IoT dostarcza ją ponownie. Ważne jest, następnie wiadomość przetwarzania logiki w aplikacji urządzenia jest *idempotentności*, dzięki czemu odbieranie wiele razy ten sam komunikat tworzy ten sam rezultat. Aplikację można również tymczasowo abandon wiadomości, co prowadzi do Centrum IoT, zachowując wiadomości w kolejce do użytku w przyszłości. Lub aplikacji można odrzucić wiadomości, które trwale usuwa komunikat z kolejki. Aby uzyskać więcej informacji na temat cyklu życia komunikatu chmury do urządzenia, zobacz [Centrum IoT — przewodnik dewelopera][IoT Hub developer guide - C2D].
   
   > [!NOTE]
   > W przypadku używania protokołu HTTPS zamiast MQTT lub AMQP jako transportu, `ReceiveAsync` metoda zwraca natychmiast. Obsługiwane wzorzec komunikatów chmury do urządzenia z protokołu HTTPS jest sporadycznie podłączone urządzenia Sprawdź komunikaty rzadko (mniej niż co 25 minut). Wystawianie więcej HTTPS odbiera wyników w Centrum IoT ograniczanie żądań. Aby uzyskać więcej informacji na temat różnic między MQTT, protokołu AMQP lub HTTPS obsługi i ograniczania Centrum IoT, zobacz [Centrum IoT — przewodnik dewelopera][IoT Hub developer guide - C2D].
   > 
   > 
2. Dodaj następującą metodę w **Main** metoda, przed `Console.ReadLine()` wiersza:
   
        ReceiveC2dAsync();

> [!NOTE]
> Sake na prostotę w tym samouczku nie implementuje wszystkie zasady ponawiania. W kodzie produkcyjnym, należy zaimplementować zasady ponawiania (na przykład wykładniczego wycofywania), zgodnie z sugestią podaną w artykuł w witrynie MSDN [obsługi błędów przejściowych].
> 
> 

## <a name="send-a-cloud-to-device-message"></a>Wyślij wiadomość chmury do urządzenia
W tej sekcji służy do pisania aplikacji konsoli .NET, który wysyła wiadomości chmury do urządzenia do aplikacji urządzenia.

1. W bieżącym rozwiązaniu programu Visual Studio, należy utworzyć projekt Visual C# pulpitu aplikacji przy użyciu **aplikacji konsoli** szablonu projektu. Nazwij projekt **SendCloudToDevice**.
   
    ![Nowy projekt w programie Visual Studio][20]
2. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy rozwiązanie, a następnie kliknij przycisk **Zarządzaj pakietami NuGet rozwiązania...** . 
   
    Ta akcja powoduje otwarcie **Zarządzaj pakietami NuGet** okna.
3. Wyszukaj **Microsoft.Azure.Devices**, kliknij przycisk **zainstalować**i zaakceptuj warunki użytkowania. 
   
    To spowoduje pobranie, instaluje i dodaje odwołanie do [pakiet NuGet zestawu SDK usługi Azure IoT].

4. Dodaj następującą instrukcję `using` w górnej części pliku **Program.cs**:
   
        using Microsoft.Azure.Devices;
5. Dodaj następujące pola do klasy **Program**: Zastąp symbol zastępczy wartości z parametrów połączenia Centrum IoT z [Rozpoczynanie pracy z Centrum IoT]:
   
        static ServiceClient serviceClient;
        static string connectionString = "{iot hub connection string}";
6. Dodaj następującą metodę do klasy **Program**:
   
        private async static Task SendCloudToDeviceMessageAsync()
        {
            var commandMessage = new Message(Encoding.ASCII.GetBytes("Cloud to device message."));
            await serviceClient.SendAsync("myFirstDevice", commandMessage);
        }
   
    Ta metoda wysyła nowego komunikatu chmura urządzenie do urządzenia o identyfikatorze `myFirstDevice`. Ten parametr należy zmienić tylko wtedy, gdy użytkownik zmodyfikował z działaniem używanym w [Rozpoczynanie pracy z Centrum IoT].
7. Na koniec dodaj następujące wiersze do metody **Główne**:
   
        Console.WriteLine("Send Cloud-to-Device message\n");
        serviceClient = ServiceClient.CreateFromConnectionString(connectionString);
   
        Console.WriteLine("Press any key to send a C2D message.");
        Console.ReadLine();
        SendCloudToDeviceMessageAsync().Wait();
        Console.ReadLine();
8. Z poziomu programu Visual Studio kliknij rozwiązanie prawym przyciskiem myszy i wybierz **projektów startowych ustawić...** . Wybierz **wiele projektów startowych**, a następnie wybierz pozycję **Start** akcji **ReadDeviceToCloudMessages**, **SimulatedDevice**, i **SendCloudToDevice**.
9. Naciśnij klawisz **F5**. Należy uruchomić wszystkie trzy aplikacje. Wybierz **SendCloudToDevice** systemu windows, a następnie naciśnij klawisz **Enter**. Powinien zostać wyświetlony komunikat odbierane za pomocą aplikacji do urządzenia.
   
   ![Odbieranie komunikatów aplikacji][21]

## <a name="receive-delivery-feedback"></a>Otrzymasz dostarczania opinię
Możliwe jest do żądania potwierdzeń dostarczenia (lub wygaśnięcia) z Centrum IoT dla każdego komunikatu chmury do urządzenia. Ta opcja umożliwia zaplecza rozwiązania, łatwo informują logiki ponawiania próby lub kompensacji. Aby uzyskać więcej informacji dotyczących opinii chmury do urządzenia, zobacz [Centrum IoT — przewodnik dewelopera][IoT Hub developer guide - C2D].

W tej sekcji możesz zmodyfikować **SendCloudToDevice** aplikacji do żądania opinii i otrzymywać Centrum IoT.

1. W programie Visual Studio w **SendCloudToDevice** projekt, dodaj następującą metodę do **Program** klasy.
   
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
   
    Należy zauważyć, że ten wzorzec receive jest tym samym kluczem, używany do odbierania wiadomości chmury do urządzenia z aplikacjami urządzenia.
2. Dodaj następującą metodę w **Main** metody po prawej `serviceClient = ServiceClient.CreateFromConnectionString(connectionString)` wiersza:
   
        ReceiveFeedbackAsync();
3. Do żądania opinii w celu dostarczenia komunikatu chmura urządzenie, należy określić właściwość **SendCloudToDeviceMessageAsync** metody. Dodaj następujący wiersz, bezpośrednio po `var commandMessage = new Message(...);` wiersza:
   
        commandMessage.Ack = DeliveryAcknowledgement.Full;
4. Uruchamianie aplikacji, naciskając klawisz **F5**. Powinny zostać wyświetlone wszystkie trzy aplikacje start. Wybierz **SendCloudToDevice** systemu windows, a następnie naciśnij klawisz **Enter**. Powinien zostać wyświetlony komunikat z nich odbierane za pomocą aplikacji do urządzenia, a po kilku sekundach wiadomość odbierane przez użytkownika **SendCloudToDevice** aplikacji.
   
   ![Odbieranie komunikatów aplikacji][22]

> [!NOTE]
> Sake na prostotę w tym samouczku nie implementuje wszystkie zasady ponawiania. W kodzie produkcyjnym, należy zaimplementować zasady ponawiania (na przykład wykładniczego wycofywania), zgodnie z sugestią podaną w artykuł w witrynie MSDN [obsługi błędów przejściowych].
> 
> 

## <a name="next-steps"></a>Kolejne kroki
W tym samouczku przedstawiono sposób wysyłania i odbierania wiadomości chmury do urządzenia. 

Aby zapoznać się przykładem kompletnych rozwiązań end-to-end korzystających z Centrum IoT, zobacz [zdalnego monitorowania Azure IoT akcelerator rozwiązań].

Aby dowiedzieć się więcej na temat tworzenia rozwiązań z Centrum IoT, zobacz [Centrum IoT — przewodnik dewelopera].

<!-- Images -->
[20]: ./media/iot-hub-csharp-csharp-c2d/create-identity-csharp1.png
[21]: ./media/iot-hub-csharp-csharp-c2d/sendc2d1.png
[22]: ./media/iot-hub-csharp-csharp-c2d/sendc2d2.png

<!-- Links -->

[pakiet NuGet zestawu SDK usługi Azure IoT]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[obsługi błędów przejściowych]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[IoT Hub developer guide - C2D]: iot-hub-devguide-messaging.md

[Centrum IoT — przewodnik dewelopera]: iot-hub-devguide.md
[Rozpoczynanie pracy z Centrum IoT]: iot-hub-csharp-csharp-getstarted.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[zdalnego monitorowania Azure IoT akcelerator rozwiązań]: https://docs.microsoft.com/azure/iot-suite/
[urządzenia Azure IoT SDK]: iot-hub-devguide-sdks.md