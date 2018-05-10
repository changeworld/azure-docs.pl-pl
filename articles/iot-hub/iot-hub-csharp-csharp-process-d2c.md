---
title: Routing komunikatów z Centrum IoT Azure (.Net) | Dokumentacja firmy Microsoft
description: Jak komunikaty do przetwarzania komunikatów urządzenia do chmury Azure IoT Hub przy użyciu reguł routingu oraz niestandardowe punkty końcowe wysłania wiadomości do innych usług zaplecza.
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: 5177bac9-722f-47ef-8a14-b201142ba4bc
ms.service: iot-hub
ms.devlang: csharp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/25/2017
ms.author: dobett
ms.openlocfilehash: 84a74a59417d3d1b9ebe0e2ede6c105b6fb3a576
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="routing-messages-with-iot-hub-net"></a>Routing komunikatów z Centrum IoT (.NET)

[!INCLUDE [iot-hub-selector-process-d2c](../../includes/iot-hub-selector-process-d2c.md)]

W tym samouczku opiera się na [Rozpoczynanie pracy z Centrum IoT] samouczka. Samouczek:

* Pokazuje, jak używać reguł routingu do wysyłania wiadomości urządzenia do chmury w sposób łatwy, na podstawie konfiguracji.
* Przedstawiono sposób izolować interakcyjne komunikaty, które wymagają natychmiastowego działania z zaplecza rozwiązania dla dalszego przetwarzania. Na przykład urządzenie może wysłać komunikat alarmu, które wyzwala Wstawianie biletu do systemu CRM. Z kolei wiadomości punktu danych, takich jak telemetrii temperatury źródła danych do aparatu analizy.

Na końcu tego samouczka możesz uruchomić trzech aplikacji konsoli .NET:

* **SimulatedDevice**, zmodyfikowanej wersji aplikacji utworzonych w [Rozpoczynanie pracy z Centrum IoT] samouczek wysyła komunikaty urządzenia do chmury punktu danych co sekundę i interaktywne urządzenia do chmury wiadomości co 10 sekund.
* **ReadDeviceToCloudMessages** wyświetlający niekrytycznych danych telemetrycznych wysłanych przez aplikację urządzenia.
* **ReadCriticalQueue** usuwania kolejki krytycznych wiadomości wysyłane przez aplikację urządzenia z kolejki usługi Service Bus. Kolejka ta jest dołączony do Centrum IoT.

> [!NOTE]
> Centrum IoT obsługuje zestawu SDK dla wielu platform urządzeń i języków, w tym C, Java i JavaScript. Aby dowiedzieć się, jak zastąpić symulowane urządzenie fizyczne urządzenia w tym samouczku, zobacz [Azure IoT Developer Center].

Do wykonania kroków tego samouczka niezbędne są następujące elementy:

* Program Visual Studio 2015 lub Visual Studio 2017.
* Aktywne konto platformy Azure. <br/>Jeśli nie masz konta, możesz utworzyć [bezpłatne konto](https://azure.microsoft.com/free/) w zaledwie kilka minut.

Zalecamy również informacje o [usługi Azure Storage] i [Azure Service Bus].

## <a name="send-interactive-messages"></a>Wysyłanie komunikatów interakcyjne

Modyfikowanie aplikacji urządzenia został utworzony w [Rozpoczynanie pracy z Centrum IoT] samouczek czasami wysyłać interaktywnego.

W programie Visual Studio w **SimulatedDevice** projekt, Zastąp `SendDeviceToCloudMessagesAsync` metodę z następującym kodem:

```csharp
private static async void SendDeviceToCloudMessagesAsync()
{
    double minTemperature = 20;
    double minHumidity = 60;
    Random rand = new Random();

    while (true)
    {
        double currentTemperature = minTemperature + rand.NextDouble() * 15;
        double currentHumidity = minHumidity + rand.NextDouble() * 20;

        var telemetryDataPoint = new
        {
            deviceId = "myFirstDevice",
            temperature = currentTemperature,
            humidity = currentHumidity
        };
        var messageString = JsonConvert.SerializeObject(telemetryDataPoint);
        string levelValue;

        if (rand.NextDouble() > 0.7)
        {
            if (rand.NextDouble() > 0.5)
            {
                messageString = "This is a critical message";
                levelValue = "critical";
            }
            else 
            {
                messageString = "This is a storage message";
                levelValue = "storage";
            }
        }
        else
        {
            levelValue = "normal";
        }
        
        var message = new Message(Encoding.ASCII.GetBytes(messageString));
        message.Properties.Add("level", levelValue);
        
        await deviceClient.SendEventAsync(message);
        Console.WriteLine("{0} > Sent message: {1}", DateTime.Now, messageString);

        await Task.Delay(1000);
    }
}
```

Ta metoda losowo dodaje właściwość `"level": "critical"` i `"level": "storage"` dla komunikatów wysyłanych przez urządzenia, która symuluje komunikat, który wymaga natychmiastowego działania przez zaplecza aplikacji lub jeden, który musi być trwale przechowywane. Aplikacja obsługuje routing wiadomości opartych na treść komunikatu.

> [!NOTE]
> Można użyć właściwości wiadomości do przesyłania wiadomości dla różnych scenariuszy, w tym chłodni ścieżką podczas przetwarzania, oprócz tu przykładzie hot ścieżki.

> [!NOTE]
> Firma Microsoft zaleca, aby zaimplementować zasady ponawiania wykładniczego wycofywania, zgodnie z sugestią podaną w artykuł w witrynie MSDN np. [obsługi błędów przejściowych].

## <a name="route-messages-to-a-queue-in-your-iot-hub"></a>Rozsyłanie wiadomości do kolejki w Centrum IoT

W tej sekcji omówiono następujące zagadnienia:

* Tworzenie kolejki usługi Service Bus.
* Podłącz je do Centrum IoT.
* Konfigurowanie Centrum IoT do wysyłania wiadomości do kolejki na podstawie obecności właściwości wiadomości.

Aby uzyskać więcej informacji o sposobie przetwarzania komunikatów z kolejek usługi Service Bus, zobacz [Rozpoczynanie pracy z kolejkami][Service Bus queue].

1. Tworzenie kolejki usługi Service Bus, zgodnie z opisem w [Rozpoczynanie pracy z kolejkami][Service Bus queue]. Kolejki musi być w tej samej subskrypcji i regionu, Centrum IoT. Zanotuj nazwę przestrzeni nazw i kolejki.

    > [!NOTE]
    > Tematy dotyczące używany jako punkty końcowe Centrum IoT nie może mieć i kolejek usługi Service Bus **sesji** lub **wykrywania duplikatów** włączone. Jeśli dowolna z tych opcji są włączone, punkt końcowy jest wyświetlany jako **informujący** w portalu Azure.

2. W portalu Azure Otwórz Centrum IoT i kliknij przycisk **punkty końcowe**.
    
    ![Punkty końcowe Centrum IoT][30]

3. W **punkty końcowe** bloku, kliknij przycisk **Dodaj** u góry, aby dodać kolejki do Centrum IoT. Nazwa punktu końcowego **CriticalQueue** i umożliwia wybranie listach rozwijanych **kolejki usługi Service Bus**, przestrzeń nazw magistrali usług, w której znajduje się kolejki i nazwy kolejki. Gdy wszystko będzie gotowe, kliknij przycisk **zapisać** u dołu.
    
    ![Dodawanie punktu końcowego][31]
    
4. Teraz kliknij **tras** w Centrum IoT. Kliknij przycisk **Dodaj** w górnej części bloku, aby utworzyć regułę routingu kieruje komunikaty do kolejki właśnie został dodany. Wybierz **DeviceTelemetry** jako źródło danych. Wprowadź `level="critical"` jako warunek i wybierz polecenie kolejki właśnie został dodany jako punkt końcowy niestandardowych jako routingu końcowy reguły. Gdy wszystko będzie gotowe, kliknij przycisk **zapisać** u dołu.
    
    ![Dodawanie trasy][32]
    
    Upewnij się, że rezerwowy trasy ma ustawioną wartość **ON**. Ta wartość jest domyślną konfigurację Centrum IoT.
    
    ![Trasy rezerwowej][33]

## <a name="read-from-the-queue-endpoint"></a>Odczyt z kolejki punktu końcowego

W tej sekcji można odczytywać wiadomości z kolejki punktu końcowego.

1. W programie Visual Studio dodaj projekt Visual C# Windows Classic Desktop do bieżącego rozwiązania, używając szablonu projektu **Aplikacja konsoli (.NET Framework)**. Nazwij projekt **ReadCriticalQueue**.

2. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy **ReadCriticalQueue** projektu, a następnie kliknij przycisk **Zarządzaj pakietami NuGet**. Ta operacja wyświetla **Menedżera pakietów NuGet** okna.

3. Wyszukaj **WindowsAzure.ServiceBus**, kliknij przycisk **zainstalować**i zaakceptuj warunki użytkowania. Ta operacja pliki do pobrania, instaluje i dodaje odwołanie do usługi Azure Service Bus, ze wszystkimi zależnościami.

4. Dodaj następujące **przy użyciu** instrukcje w górnej części **Program.cs** pliku:
   
    ```csharp
    using System.IO;
    using Microsoft.ServiceBus.Messaging;
    ```

5. Na koniec należy dodać następujące wiersze do **Main** metody. Zastąp ciąg połączenia z **nasłuchiwania** dla kolejki:
   
    ```csharp
    Console.WriteLine("Receive critical messages. Ctrl-C to exit.\n");
    var connectionString = "{service bus listen string}";
    var queueName = "{queue name}";
    
    var client = QueueClient.CreateFromConnectionString(connectionString, queueName);

    client.OnMessage(message =>
        {
            Stream stream = message.GetBody<Stream>();
            StreamReader reader = new StreamReader(stream, Encoding.ASCII);
            string s = reader.ReadToEnd();
            Console.WriteLine(String.Format("Message body: {0}", s));
        });
        
    Console.ReadLine();
    ```

## <a name="run-the-applications"></a>Uruchamianie aplikacji
Teraz wszystko jest gotowe do uruchomienia aplikacji.

1. W programie Visual Studio w Eksploratorze rozwiązań kliknij rozwiązanie prawym przyciskiem myszy i wybierz **Ustaw projekty startowe**. Wybierz **wiele projektów startowych**, a następnie wybierz pozycję **Start** jako akcja dla **ReadDeviceToCloudMessages**, **SimulatedDevice**, i **ReadCriticalQueue** projektów.
2. Naciśnij klawisz **F5** można uruchomić konsoli trzy aplikacje. **ReadDeviceToCloudMessages** aplikacja ma tylko niekrytyczne komunikatów wysyłanych z **SimulatedDevice** aplikacji oraz **ReadCriticalQueue** aplikacja ma tylko krytycznych wiadomości.
   
   ![Trzy aplikacje konsoli][50]

## <a name="optional-add-storage-container-to-your-iot-hub-and-route-messages-to-it"></a>(Opcjonalnie) Dodaj kontener magazynu do IoT hub i tras wiadomości do niego

W tej sekcji Utwórz konto magazynu, połącz go z Centrum IoT i skonfigurować do wysyłania komunikatów do konta, na podstawie obecności właściwość w komunikacie Centrum IoT. Aby uzyskać więcej informacji o sposobie zarządzania magazynu, zobacz [Rozpoczynanie pracy z magazynem Azure][usługi Azure Storage].

 > [!NOTE]
   > Jeśli nie masz tylko jeden **punktu końcowego**, może skonfigurować **StorageContainer** oprócz **CriticalQueue** i uruchom oba simulatneously.

1. Utwórz konto magazynu, zgodnie z opisem w [dokumentacji platformy Azure magazynu] [lnk magazynu]. Zanotuj nazwę konta.

2. W portalu Azure Otwórz Centrum IoT i kliknij przycisk **punkty końcowe**.

3. W **punkty końcowe** bloku, wybierz opcję **CriticalQueue** punktu końcowego, a następnie kliknij przycisk **usunąć**. Kliknij przycisk **tak**, a następnie kliknij przycisk **Dodaj**. Nazwa punktu końcowego **StorageContainer** i umożliwia wybranie listach rozwijanych **kontenera magazynu Azure**i Utwórz **konta magazynu** i **magazynu kontener**.  Zanotuj nazwy.  Gdy wszystko będzie gotowe, kliknij przycisk **OK** u dołu. 

 > [!NOTE]
   > Jeśli nie masz tylko jeden **punktu końcowego**, nie trzeba usunąć **CriticalQueue**.

4. Kliknij przycisk **tras** w Centrum IoT. Kliknij przycisk **Dodaj** w górnej części bloku, aby utworzyć regułę routingu kieruje komunikaty do kolejki właśnie został dodany. Wybierz **komunikaty** jako źródło danych. Wprowadź `level="storage"` jako warunek i wybierz polecenie **StorageContainer** jako punktu końcowego niestandardowych jako routingu końcowy reguły. Kliknij przycisk **zapisać** u dołu.  

    Upewnij się, że rezerwowy trasy ma ustawioną wartość **ON**. To ustawienie jest domyślną konfigurację Centrum IoT.

1. Upewnij się, że nadal działają poprzedniej aplikacji. 

1. W portalu Azure, przejdź do konta magazynu, w obszarze **usługa Blob**, kliknij przycisk **Przeglądaj obiekty BLOB...** .  Wybierz kontener użytkownika, przejdź do kliknij plik JSON i kliknij przycisk **Pobierz** do wyświetlania danych.

## <a name="next-steps"></a>Kolejne kroki
W tym samouczku przedstawiono sposób niezawodny sposób wysyłania wiadomości urządzenia do chmury przy użyciu funkcji routing komunikatów z Centrum IoT.

[Sposób wysyłania wiadomości chmury do urządzenia z Centrum IoT] [ lnk-c2d] przedstawiono sposób wysyłania komunikatów do urządzeń z Twojej zaplecza rozwiązania.

Aby zapoznać się przykładem kompletnych rozwiązań end-to-end korzystających z Centrum IoT, zobacz [zdalnego monitorowania Azure IoT akcelerator rozwiązań][lnk-suite].

Aby dowiedzieć się więcej na temat tworzenia rozwiązań z Centrum IoT, zobacz [Centrum IoT — przewodnik dewelopera].

Aby dowiedzieć się więcej na temat w Centrum IoT rozsyłania wiadomości, zobacz [wysyłania i odbierania wiadomości z Centrum IoT][lnk-devguide-messaging].

<!-- Images. -->
[50]: ./media/iot-hub-csharp-csharp-process-d2c/run1.png
[30]: ./media/iot-hub-csharp-csharp-process-d2c/click-endpoints.png
[31]: ./media/iot-hub-csharp-csharp-process-d2c/endpoint-creation.png
[32]: ./media/iot-hub-csharp-csharp-process-d2c/route-creation.png
[33]: ./media/iot-hub-csharp-csharp-process-d2c/fallback-route.png

<!-- Links -->
[Service Bus queue]: ../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md
[usługi Azure Storage]: https://azure.microsoft.com/documentation/services/storage/
[Azure Service Bus]: https://azure.microsoft.com/documentation/services/service-bus/
[Centrum IoT — przewodnik dewelopera]: iot-hub-devguide.md
[Rozpoczynanie pracy z Centrum IoT]: iot-hub-csharp-csharp-getstarted.md
[lnk-devguide-messaging]: iot-hub-devguide-messaging.md
[Azure IoT Developer Center]: https://azure.microsoft.com/develop/iot
[obsługi błędów przejściowych]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[lnk-c2d]: iot-hub-csharp-csharp-c2d.md
[lnk-suite]: https://azure.microsoft.com/documentation/suites/iot-suite/
