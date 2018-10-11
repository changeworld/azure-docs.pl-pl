---
title: Wprowadzenie do kolejek usługi Azure Service Bus | Microsoft Docs
description: Napisz aplikację konsolową w języku C#, która korzysta z kolejek komunikatów w usłudze Service Bus.
services: service-bus-messaging
documentationcenter: .net
author: spelluru
manager: timlt
editor: ''
ms.assetid: 68a34c00-5600-43f6-bbcc-fea599d500da
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: hero-article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 09/07/2018
ms.author: spelluru
ms.openlocfilehash: 30ff970a1f6243bbba6aa42046cf6e08c808f4e5
ms.sourcegitcommit: 7b0778a1488e8fd70ee57e55bde783a69521c912
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/10/2018
ms.locfileid: "49068864"
---
# <a name="get-started-with-service-bus-queues"></a>Wprowadzenie do kolejek usługi Service Bus

[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

Ten samouczek obejmuje następujące kroki:

1. Utworzenie przestrzeni nazw usługi Service Bus za pomocą usługi Azure Portal.
2. Utworzenie kolejki usługi Service Bus przy użyciu witryny Azure Portal.
3. Napisanie aplikacji konsolowej .NET Core w celu wysłania zestawu komunikatów do kolejki.
4. Napisanie aplikacji konsolowej .NET Core w celu otrzymania tych komunikatów z kolejki.

## <a name="prerequisites"></a>Wymagania wstępne

1. [Program Visual Studio 2017 Update 3 (wersja 15.3, 26730.01)](http://www.visualstudio.com/vs) lub nowszy.
2. [Zestaw NET Core SDK](https://www.microsoft.com/net/download/windows), wersja 2.0 lub nowsza.
2. Subskrypcja platformy Azure.

[!INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="1-create-a-namespace-using-the-azure-portal"></a>1. Tworzenie przestrzeni nazw za pomocą usługi Azure Portal

> [!NOTE] 
> Przestrzeń nazw usługi Service Bus i jednostki obsługi komunikatów można tworzyć przy użyciu programu [PowerShell](/powershell/azure/get-started-azureps). Aby uzyskać więcej informacji, zobacz [Use PowerShell to manage Service Bus resources (Używanie programu PowerShell do zarządzania zasobami usługi Service Bus)](service-bus-manage-with-ps.md).

Jeśli przestrzeń nazw obsługi komunikatów usługi Service Bus została już utworzona, przejdź do sekcji [Tworzenie kolejki za pomocą usługi Azure Portal](#2-create-a-queue-using-the-azure-portal).

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## <a name="2-create-a-queue-using-the-azure-portal"></a>2. Tworzenie kolejki za pomocą usługi Azure Portal

Jeśli kolejka usługi Service Bus została już utworzona, przejdź do sekcji [Wysyłanie komunikatów do kolejki](#3-send-messages-to-the-queue).

[!INCLUDE [service-bus-create-queue-portal](../../includes/service-bus-create-queue-portal.md)]

## <a name="3-send-messages-to-the-queue"></a>3. Wysyłanie komunikatów do kolejki

Aby wysyłać komunikaty do kolejki, napisz aplikację konsolową w języku C# za pomocą programu Visual Studio.

### <a name="create-a-console-application"></a>Tworzenie aplikacji konsolowej

Uruchom program Visual Studio i utwórz nowy projekt **Aplikacja konsoli (.NET Core)**.

### <a name="add-the-service-bus-nuget-package"></a>Dodawanie pakietu NuGet usługi Service Bus

1. Kliknij prawym przyciskiem myszy nowo utworzony projekt i wybierz pozycję **Zarządzaj pakietami NuGet**.
2. Kliknij kartę **Przeglądaj**, wyszukaj ciąg **[Microsoft.Azure.ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus/)**, a następnie wybierz element **Microsoft.Azure.ServiceBus**. Kliknij przycisk **Zainstaluj**, aby ukończyć instalację, a następnie zamknij to okno dialogowe.
   
    ![Wybieranie pakietu NuGet][nuget-pkg]

### <a name="write-code-to-send-messages-to-the-queue"></a>Pisanie kodu w celu wysyłania komunikatów do kolejki

1. W pliku Program.cs dodaj następujące instrukcje `using` na początku definicji przestrzeni nazw przed deklaracją klasy:
   
    ```csharp
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.ServiceBus;
    ```

2. W ramach klasy `Program` zadeklaruj następujące zmienne. Ustaw jako zmienną `ServiceBusConnectionString` parametry połączenia, które zostały uzyskane podczas tworzenia przestrzeni nazw, a następnie ustaw jako zmienną `QueueName` nazwę użytą podczas tworzenia kolejki:
   
    ```csharp
    const string ServiceBusConnectionString = "<your_connection_string>";
    const string QueueName = "<your_queue_name>";
    static IQueueClient queueClient;
    ``` 

3. Zastąp domyślną zawartość elementu `Main()` następującym wierszem kodu:

    ```csharp
    MainAsync().GetAwaiter().GetResult();
    ```

4. Bezpośrednio po elemencie `Main()` dodaj następującą metodę asynchroniczną `MainAsync()`, która wywołuje metodę wysyłania komunikatów:

    ```csharp
    static async Task MainAsync()
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

5. Bezpośrednio po metodzie `MainAsync()` dodaj następującą metodę `SendMessagesAsync()`, która wykonuje zadanie wysyłania liczby komunikatów określonej przy użyciu elementu `numberOfMessagesToSend` (wartość aktualnie ustawiona na 10):

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
   
6. Oto jak powinien wyglądać plik Program.cs:
   
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

            static void Main(string[] args)
            {
                MainAsync().GetAwaiter().GetResult();
            }

            static async Task MainAsync()
            {
                const int numberOfMessages = 10;
                queueClient = new QueueClient(ServiceBusConnectionString, QueueName);

                Console.WriteLine("======================================================");
                Console.WriteLine("Press ENTER key to exit after sending all the messages.");
                Console.WriteLine("======================================================");

                // Send Messages
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

7. Uruchom program i sprawdź witrynę Azure Portal: kliknij nazwę swojej kolejki w oknie **Przegląd** przestrzeni nazw. Zostanie wyświetlony ekran **Podstawowe elementy** kolejki. Zauważ, że wartość **Liczba aktywnych komunikatów** wynosi teraz **10**. Przy każdym uruchomieniu aplikacji nadawcy bez pobierania komunikatów (zgodnie z opisem w następnej sekcji) ta wartość zwiększa się o 10. Należy również zauważyć, że bieżący rozmiar kolejki zwiększa wartość **Bieżące** w oknie **Podstawowe elementy** za każdym razem, kiedy aplikacja dodaje komunikaty do kolejki.
   
      ![Rozmiar komunikatu][queue-message]

## <a name="4-receive-messages-from-the-queue"></a>4. Odbieranie komunikatów z kolejki

Aby odbierać komunikaty wysłane w poprzednim kroku, utwórz nową aplikację konsolową .NET Core i zainstaluj pakiet NuGet **Microsoft.Azure.ServiceBus**, podobnie jak w przypadku poprzedniej aplikacji nadawcy.

### <a name="write-code-to-receive-messages-from-the-queue"></a>Pisanie kodu w celu odbierania komunikatów z kolejki

1. W pliku Program.cs dodaj następujące instrukcje `using` na początku definicji przestrzeni nazw przed deklaracją klasy:
   
    ```csharp
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.ServiceBus;
    ```

2. W ramach klasy `Program` zadeklaruj następujące zmienne. Ustaw jako zmienną `ServiceBusConnectionString` parametry połączenia, które zostały uzyskane podczas tworzenia przestrzeni nazw, a następnie ustaw jako zmienną `QueueName` nazwę użytą podczas tworzenia kolejki:
   
    ```csharp
    const string ServiceBusConnectionString = "<your_connection_string>";
    const string QueueName = "<your_queue_name>";
    static IQueueClient queueClient;
    ```

3. Zastąp domyślną zawartość elementu `Main()` następującym wierszem kodu:

    ```csharp
    MainAsync().GetAwaiter().GetResult();
    ```

4. Bezpośrednio po elemencie `Main()` dodaj następującą metodę asynchroniczną `MainAsync()`, która wywołuje metodę `RegisterOnMessageHandlerAndReceiveMessages()`:

    ```csharp
    static async Task MainAsync()
    {
        queueClient = new QueueClient(ServiceBusConnectionString, QueueName);

        Console.WriteLine("======================================================");
        Console.WriteLine("Press ENTER key to exit after receiving all the messages.");
        Console.WriteLine("======================================================");

        // Register the queue message handler and receive messages in a loop
        RegisterOnMessageHandlerAndReceiveMessages();

        Console.ReadKey();

        await queueClient.CloseAsync();
    }
    ```

5. Bezpośrednio po metodzie `MainAsync()` dodaj następującą metodę, która rejestruje obsługę komunikatów i odbiera komunikaty wysyłane przez aplikację nadawcy:

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

6. Bezpośrednio po poprzedniej metodzie dodaj następującą metodę `ProcessMessagesAsync()` w celu przetworzenia odebranych komunikatów:
 
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

7. Na koniec dodaj następującą metodę obsługującą ewentualne wyjątki, które mogą wystąpić:
 
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
   
8. Oto jak powinien wyglądać plik Program.cs:
   
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
4. Uruchom program i ponownie sprawdź portal. Zauważ, że wartości **Liczba aktywnych komunikatów** i **Bieżące** wynoszą teraz **0**.
   
    ![Długość kolejki][queue-message-receive]

Gratulacje! Utworzono kolejkę, wysłano do niej zestaw komunikatów i odebrano te komunikaty z tej samej kolejki.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z naszym [repozytorium GitHub zawierającym przykłady](https://github.com/Azure/azure-service-bus/tree/master/samples), które pokazują niektóre bardziej zaawansowane funkcje obsługi komunikatów usługi Service Bus.

<!--Image references-->

[nuget-pkg]: ./media/service-bus-dotnet-get-started-with-queues/nuget-package.png
[queue-message]: ./media/service-bus-dotnet-get-started-with-queues/queue-message.png
[queue-message-receive]: ./media/service-bus-dotnet-get-started-with-queues/queue-message-receive.png

