---
title: Wprowadzenie do kolejek usługi Azure Service Bus | Microsoft Docs
description: W tym samouczku utworzysz aplikacje konsoli .NET Core do wysyłania wiadomości do i odbierania wiadomości z kolejki usługi Service Bus.
services: service-bus-messaging
documentationcenter: .net
author: axisc
manager: timlt
editor: spelluru
ms.assetid: 68a34c00-5600-43f6-bbcc-fea599d500da
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: conceptual
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 01/24/2020
ms.author: aschhab
ms.openlocfilehash: 5718106aee0e60d111398efdb839945c2c7a8a06
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77471741"
---
# <a name="get-started-with-service-bus-queues"></a>Wprowadzenie do kolejek usługi Service Bus
[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]
W tym samouczku utworzysz aplikacje konsoli .NET Core do wysyłania wiadomości do i odbierania wiadomości z kolejki usługi Service Bus.

## <a name="prerequisites"></a>Wymagania wstępne

- [Visual Studio 2019](https://www.visualstudio.com/vs).
- [Zestaw NET Core SDK](https://www.microsoft.com/net/download/windows), wersja 2.0 lub nowsza.
- Subskrypcja platformy Azure. Do wykonania kroków tego samouczka potrzebne jest konto platformy Azure. Możesz aktywować [swoje korzyści dla subskrybentów MSDN](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) lub założyć bezpłatne [konto.](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)
- Jeśli nie masz kolejki do pracy, wykonaj kroki w [witrynie Azure portal, aby utworzyć kolejkę usługi Service Bus](service-bus-quickstart-portal.md) artykuł, aby utworzyć kolejkę.

  - Przeczytaj szybkie omówienie kolejek usługi Service Bus.
  - Tworzenie obszaru nazw usługi Service Bus.
  - Pobierz ciąg połączenia.
  - Utwórz kolejkę usługi Service Bus.

## <a name="send-messages-to-the-queue"></a>Wysyłanie komunikatów do kolejki

Aby wysyłać komunikaty do kolejki, napisz aplikację konsolową w języku C# za pomocą programu Visual Studio.

### <a name="create-a-console-application"></a>Tworzenie aplikacji konsolowej

Uruchom program Visual Studio i utwórz nowy projekt **aplikacji konsoli (.NET Core)** dla języka C#. W tym przykładzie nazwa aplikacji *CoreSenderApp*.

### <a name="add-the-service-bus-nuget-package"></a>Dodawanie pakietu NuGet usługi Service Bus

1. Kliknij prawym przyciskiem myszy nowo utworzony projekt i wybierz pozycję **Zarządzaj pakietami NuGet**.
1. Wybierz pozycję **Przeglądaj**. Wyszukaj i wybierz **[pozycję Microsoft.Azure.ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus/)**.
1. Wybierz **pozycję Zainstaluj,** aby zakończyć instalację, a następnie zamknij Menedżera pakietów NuGet.

    ![Wybieranie pakietu NuGet][nuget-pkg]

### <a name="write-code-to-send-messages-to-the-queue"></a>Pisanie kodu w celu wysyłania komunikatów do kolejki

1. W *Program.cs*, dodaj następujące `using` instrukcje u góry definicji obszaru nazw, przed deklaracją klasy:

    ```csharp
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.ServiceBus;
    ```

1. W `Program` klasie zadeklarować następujące zmienne:

    ```csharp
    const string ServiceBusConnectionString = "<your_connection_string>";
    const string QueueName = "<your_queue_name>";
    static IQueueClient queueClient;
    ```

    Wprowadź parametry połączenia dla obszaru `ServiceBusConnectionString` nazw jako zmienną. Wprowadź nazwę kolejki.

1. Zastąp `Main()` metodę następującą metodą **asynchronizową.** `Main` Wywołuje `SendMessagesAsync()` metodę, która zostanie dodana w następnym kroku, aby wysłać wiadomości do kolejki. 

    ```csharp
    public static async Task Main(string[] args)
    {    
        const int numberOfMessages = 10;
        queueClient = new QueueClient(ServiceBusConnectionString, QueueName);

        Console.WriteLine("======================================================");
        Console.WriteLine("Press ENTER key to exit after sending all the messages.");
        Console.WriteLine("======================================================");

        // Send messages.
        await SendMessagesAsync(numberOfMessages);

        Console.ReadKey();

        await queueClient.CloseAsync();
    }
    ```
1. Bezpośrednio po `MainAsync()` tej metodzie `SendMessagesAsync()` dodaj następującą metodę, która wykonuje pracę `numberOfMessagesToSend` wysyłania liczby komunikatów określonych przez (obecnie ustawiona na 10):

    ```csharp
    static async Task SendMessagesAsync(int numberOfMessagesToSend)
    {
        try
        {
            for (var i = 0; i < numberOfMessagesToSend; i++)
            {
                // Create a new message to send to the queue.
                string messageBody = $"Message {i}";
                var message = new Message(Encoding.UTF8.GetBytes(messageBody));

                // Write the body of the message to the console.
                Console.WriteLine($"Sending message: {messageBody}");

                // Send the message to the queue.
                await queueClient.SendAsync(message);
            }
        }
        catch (Exception exception)
        {
            Console.WriteLine($"{DateTime.Now} :: Exception: {exception.Message}");
        }
    }
    ```

Oto, jak powinien wyglądać Twój *plik Program.cs.*

```csharp
namespace CoreSenderApp
{
    using System;
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.ServiceBus;

    class Program
    {
        // Connection String for the namespace can be obtained from the Azure portal under the 
        // 'Shared Access policies' section.
        const string ServiceBusConnectionString = "<your_connection_string>";
        const string QueueName = "<your_queue_name>";
        static IQueueClient queueClient;

        public static async Task Main(string[] args)
        {    
            const int numberOfMessages = 10;
            queueClient = new QueueClient(ServiceBusConnectionString, QueueName);
    
            Console.WriteLine("======================================================");
            Console.WriteLine("Press ENTER key to exit after sending all the messages.");
            Console.WriteLine("======================================================");
    
            // Send messages.
            await SendMessagesAsync(numberOfMessages);
    
            Console.ReadKey();
    
            await queueClient.CloseAsync();
        }

        static async Task SendMessagesAsync(int numberOfMessagesToSend)
        {
            try
            {
                for (var i = 0; i < numberOfMessagesToSend; i++)
                {
                    // Create a new message to send to the queue
                    string messageBody = $"Message {i}";
                    var message = new Message(Encoding.UTF8.GetBytes(messageBody));

                    // Write the body of the message to the console
                    Console.WriteLine($"Sending message: {messageBody}");

                    // Send the message to the queue
                    await queueClient.SendAsync(message);
                }
            }
            catch (Exception exception)
            {
                Console.WriteLine($"{DateTime.Now} :: Exception: {exception.Message}");
            }
        }
    }
}
```

Uruchom program i sprawdź witrynę Azure portal.

Wybierz nazwę kolejki w oknie **Przegląd** obszaru nazw, aby wyświetlić kolejkę **Essentials**.

![Odebrane wiadomości z liczbą i rozmiarem][queue-message]

Wartość **liczby wiadomości aktywnych** dla kolejki wynosi teraz **10**. Za każdym razem, gdy uruchamiasz tę aplikację nadawcy bez pobierania wiadomości, wartość ta zwiększa się o 10.

Bieżący rozmiar kolejki zwiększa wartość **CURRENT** w **programach Essentials** za każdym razem, gdy aplikacja dodaje wiadomości do kolejki.

W następnej sekcji opisano sposób pobierania tych wiadomości.

## <a name="receive-messages-from-the-queue"></a>Odbieranie komunikatów z kolejki

Aby odbierać wysłane wiadomości, utwórz inną aplikację **console app (.NET Core).** Zainstaluj pakiet **Microsoft.Azure.ServiceBus** NuGet, tak jak w przypadku aplikacji nadawcy.

### <a name="write-code-to-receive-messages-from-the-queue"></a>Pisanie kodu w celu odbierania komunikatów z kolejki

1. W *Program.cs*, dodaj następujące `using` instrukcje u góry definicji obszaru nazw, przed deklaracją klasy:

    ```csharp
    using System;
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.ServiceBus;
    ```

1. W `Program` klasie zadeklarować następujące zmienne:

    ```csharp
    const string ServiceBusConnectionString = "<your_connection_string>";
    const string QueueName = "<your_queue_name>";
    static IQueueClient queueClient;
    ```

    Wprowadź parametry połączenia dla obszaru `ServiceBusConnectionString` nazw jako zmienną. Wprowadź nazwę kolejki.

1. Zastąp metodę `Main()` poniższym kodem:

    ```csharp
    static void Main(string[] args)
    {
        MainAsync().GetAwaiter().GetResult();
    }

    static async Task MainAsync()
    {
        queueClient = new QueueClient(ServiceBusConnectionString, QueueName);

        Console.WriteLine("======================================================");
        Console.WriteLine("Press ENTER key to exit after receiving all the messages.");
        Console.WriteLine("======================================================");

        // Register QueueClient's MessageHandler and receive messages in a loop
        RegisterOnMessageHandlerAndReceiveMessages();

        Console.ReadKey();

        await queueClient.CloseAsync();
    }
    ```

1. Bezpośrednio po `MainAsync()` metody dodaj następującą metodę, która rejestruje program obsługi wiadomości i odbiera wiadomości wysyłane przez aplikację nadawcy:

    ```csharp
    static void RegisterOnMessageHandlerAndReceiveMessages()
    {
        // Configure the message handler options in terms of exception handling, number of concurrent messages to deliver, etc.
        var messageHandlerOptions = new MessageHandlerOptions(ExceptionReceivedHandler)
        {
            // Maximum number of concurrent calls to the callback ProcessMessagesAsync(), set to 1 for simplicity.
            // Set it according to how many messages the application wants to process in parallel.
            MaxConcurrentCalls = 1,

            // Indicates whether the message pump should automatically complete the messages after returning from user callback.
            // False below indicates the complete operation is handled by the user callback as in ProcessMessagesAsync().
            AutoComplete = false
        };

        // Register the function that processes messages.
        queueClient.RegisterMessageHandler(ProcessMessagesAsync, messageHandlerOptions);
    }
    ```

1. Bezpośrednio po poprzedniej metodzie dodaj następującą metodę `ProcessMessagesAsync()` w celu przetworzenia odebranych komunikatów:

    ```csharp
    static async Task ProcessMessagesAsync(Message message, CancellationToken token)
    {
        // Process the message.
        Console.WriteLine($"Received message: SequenceNumber:{message.SystemProperties.SequenceNumber} Body:{Encoding.UTF8.GetString(message.Body)}");

        // Complete the message so that it is not received again.
        // This can be done only if the queue Client is created in ReceiveMode.PeekLock mode (which is the default).
        await queueClient.CompleteAsync(message.SystemProperties.LockToken);

        // Note: Use the cancellationToken passed as necessary to determine if the queueClient has already been closed.
        // If queueClient has already been closed, you can choose to not call CompleteAsync() or AbandonAsync() etc.
        // to avoid unnecessary exceptions.
    }
    ```

1. Na koniec dodaj następującą metodę obsługującą ewentualne wyjątki, które mogą wystąpić:

    ```csharp
    // Use this handler to examine the exceptions received on the message pump.
    static Task ExceptionReceivedHandler(ExceptionReceivedEventArgs exceptionReceivedEventArgs)
    {
        Console.WriteLine($"Message handler encountered an exception {exceptionReceivedEventArgs.Exception}.");
        var context = exceptionReceivedEventArgs.ExceptionReceivedContext;
        Console.WriteLine("Exception context for troubleshooting:");
        Console.WriteLine($"- Endpoint: {context.Endpoint}");
        Console.WriteLine($"- Entity Path: {context.EntityPath}");
        Console.WriteLine($"- Executing Action: {context.Action}");
        return Task.CompletedTask;
    }
    ```

Oto, jak powinien wyglądać Twój *plik Program.cs:*

```csharp
namespace CoreReceiverApp
{
    using System;
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.ServiceBus;

    class Program
    {
        // Connection String for the namespace can be obtained from the Azure portal under the 
        // 'Shared Access policies' section.
        const string ServiceBusConnectionString = "<your_connection_string>";
        const string QueueName = "<your_queue_name>";
        static IQueueClient queueClient;

        static void Main(string[] args)
        {
            MainAsync().GetAwaiter().GetResult();
        }

        static async Task MainAsync()
        {
            queueClient = new QueueClient(ServiceBusConnectionString, QueueName);

            Console.WriteLine("======================================================");
            Console.WriteLine("Press ENTER key to exit after receiving all the messages.");
            Console.WriteLine("======================================================");

            // Register QueueClient's MessageHandler and receive messages in a loop
            RegisterOnMessageHandlerAndReceiveMessages();
 
            Console.ReadKey();

            await queueClient.CloseAsync();
        }

        static void RegisterOnMessageHandlerAndReceiveMessages()
        {
            // Configure the MessageHandler Options in terms of exception handling, number of concurrent messages to deliver etc.
            var messageHandlerOptions = new MessageHandlerOptions(ExceptionReceivedHandler)
            {
                // Maximum number of Concurrent calls to the callback `ProcessMessagesAsync`, set to 1 for simplicity.
                // Set it according to how many messages the application wants to process in parallel.
                MaxConcurrentCalls = 1,

                // Indicates whether MessagePump should automatically complete the messages after returning from User Callback.
                // False below indicates the Complete will be handled by the User Callback as in `ProcessMessagesAsync` below.
                AutoComplete = false
            };

            // Register the function that will process messages
            queueClient.RegisterMessageHandler(ProcessMessagesAsync, messageHandlerOptions);
        }

        static async Task ProcessMessagesAsync(Message message, CancellationToken token)
        {
            // Process the message
            Console.WriteLine($"Received message: SequenceNumber:{message.SystemProperties.SequenceNumber} Body:{Encoding.UTF8.GetString(message.Body)}");

            // Complete the message so that it is not received again.
            // This can be done only if the queueClient is created in ReceiveMode.PeekLock mode (which is default).
            await queueClient.CompleteAsync(message.SystemProperties.LockToken);

            // Note: Use the cancellationToken passed as necessary to determine if the queueClient has already been closed.
            // If queueClient has already been Closed, you may chose to not call CompleteAsync() or AbandonAsync() etc. calls 
            // to avoid unnecessary exceptions.
        }

        static Task ExceptionReceivedHandler(ExceptionReceivedEventArgs exceptionReceivedEventArgs)
        {
            Console.WriteLine($"Message handler encountered an exception {exceptionReceivedEventArgs.Exception}.");
            var context = exceptionReceivedEventArgs.ExceptionReceivedContext;
            Console.WriteLine("Exception context for troubleshooting:");
            Console.WriteLine($"- Endpoint: {context.Endpoint}");
            Console.WriteLine($"- Entity Path: {context.EntityPath}");
            Console.WriteLine($"- Executing Action: {context.Action}");
            return Task.CompletedTask;
        }
    }
}
```

Uruchom program i ponownie sprawdź portal. **Liczba wiadomości aktywnych** i wartości **CURRENT** są teraz **0**.

![Kolejka po odebraniu wiadomości][queue-message-receive]

Gratulacje! Teraz utworzono kolejkę, wysłano zestaw wiadomości do tej kolejki i odebrano te wiadomości z tej samej kolejki.

> [!NOTE]
> Zasoby usługi Service Bus można zarządzać za pomocą [Eksploratora magistrali usług](https://github.com/paolosalvatori/ServiceBusExplorer/). Eksplorator usługi Service Bus umożliwia użytkownikom łatwe łączenie się z obszarem nazw usługi Service Bus i administrowanie jednostkami obsługi wiadomości. Narzędzie zapewnia zaawansowane funkcje, takie jak funkcje importu/eksportu lub możliwość testowania tematów, kolejek, subskrypcji, usług przekazywania, centrów powiadomień i centrów zdarzeń.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z naszym [repozytorium GitHub zawierającym przykłady](https://github.com/Azure/azure-service-bus/tree/master/samples), które pokazują niektóre bardziej zaawansowane funkcje obsługi komunikatów usługi Service Bus.

<!--Image references-->

[nuget-pkg]: ./media/service-bus-dotnet-get-started-with-queues/nuget-package.png
[queue-message]: ./media/service-bus-dotnet-get-started-with-queues/messages-sent-to-essentials.png
[queue-message-receive]: ./media/service-bus-dotnet-get-started-with-queues/queue-message-receive-in-essentials.png

