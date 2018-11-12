---
title: Rozpoczynanie pracy z tematami i subskrypcjami usługi Azure Service Bus | Microsoft Docs
description: Napisz aplikację konsolową .NET Core w języku C#, która korzysta z tematów i subskrypcji komunikatów usługi Service Bus.
services: service-bus-messaging
documentationcenter: .net
author: spelluru
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: hero-article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 09/03/2018
ms.author: spelluru
ms.openlocfilehash: d48d658883324637e1026ac00312ade86ccc1400
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2018
ms.locfileid: "51230584"
---
# <a name="get-started-with-service-bus-topics"></a>Wprowadzenie do tematów usługi Service Bus

[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

Ten samouczek obejmuje następujące kroki:

1. Utworzenie przestrzeni nazw usługi Service Bus za pomocą usługi Azure Portal.
2. Utworzenie tematu usługi Service Bus przy użyciu witryny Azure Portal.
3. Utworzenie subskrypcji tego tematu usługi Service Bus przy użyciu witryny Azure Portal.
4. Napisanie aplikacji konsolowej .NET Core w celu wysłania zestawu komunikatów do tematu.
5. Napisanie aplikacji konsolowej .NET Core w celu odebrania tych komunikatów z subskrypcji.

## <a name="prerequisites"></a>Wymagania wstępne

1. [Program Visual Studio 2017 Update 3 (wersja 15.3, 26730.01)](https://www.visualstudio.com/vs) lub nowszy.
2. [Zestaw NET Core SDK](https://www.microsoft.com/net/download/windows), wersja 2.0 lub nowsza.
2. Subskrypcja platformy Azure.

[!INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="1-create-a-namespace-using-the-azure-portal"></a>1. Tworzenie przestrzeni nazw za pomocą usługi Azure Portal

> [!NOTE] 
> Przestrzeń nazw usługi Service Bus i jednostki obsługi komunikatów można tworzyć przy użyciu programu [PowerShell](/powershell/azure/get-started-azureps). Aby uzyskać więcej informacji, zobacz [Use PowerShell to manage Service Bus resources (Używanie programu PowerShell do zarządzania zasobami usługi Service Bus)](service-bus-manage-with-ps.md).

Jeśli przestrzeń nazw obsługi komunikatów usługi Service Bus została już utworzona, przejdź do sekcji [Tworzenie tematu przy użyciu witryny Azure Portal](#2-create-a-topic-using-the-azure-portal).

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## <a name="2-create-a-topic-using-the-azure-portal"></a>2. Tworzenie tematu przy użyciu witryny Azure Portal

1. Zaloguj się w witrynie [Azure Portal][azure-portal].
2. W lewym okienku nawigacji portalu kliknij pozycję **Service Bus** (jeśli pozycja **Service Bus** nie jest widoczna, kliknij pozycję **Wszystkie usługi** lub pozycję **Wszystkie zasoby**). Kliknij przestrzeń nazw, w której chcesz utworzyć temat. 
3. Zostanie otwarte okno przeglądu przestrzeni nazw. Kliknij pozycję **Tematy**:
   
    ![Tworzenie tematu][createtopic1]
4. Kliknij pozycję **+ Temat**.
   
    ![Wybieranie tematów][createtopic2]
5. Wprowadź nazwę tematu. Pozostaw inne opcje z wartościami domyślnymi.
   
    ![Wybieranie nowych kolejek][createtopic3]
6. W dolnej części okna dialogowego kliknij pozycję **Utwórz**.

## <a name="3-create-a-subscription-to-the-topic"></a>3. Tworzenie subskrypcji tematu

1. W okienku zasobów portalu kliknij przestrzeń nazw utworzoną w kroku 1, a następnie kliknij pozycję **Tematy** i nazwę tematu utworzonego w kroku 2.
2. W górnej części okienka przeglądu kliknij pozycję **+ Subskrypcja**, aby dodać subskrypcję do tego tematu.

    ![Tworzenie subskrypcji][createtopic4]

3. Wprowadź nazwę subskrypcji. Pozostaw inne opcje z wartościami domyślnymi.

## <a name="4-send-messages-to-the-topic"></a>4. Wysyłanie komunikatów do tematu

Aby wysyłać komunikaty do tematu, napisz aplikację konsolową w języku C# za pomocą programu Visual Studio.

### <a name="create-a-console-application"></a>Tworzenie aplikacji konsolowej

Uruchom program Visual Studio i utwórz nowy projekt **Aplikacja konsoli (.NET Core)**.

### <a name="add-the-service-bus-nuget-package"></a>Dodawanie pakietu NuGet usługi Service Bus

1. Kliknij prawym przyciskiem myszy nowo utworzony projekt i wybierz pozycję **Zarządzaj pakietami NuGet**.
2. Kliknij kartę **Przeglądaj**, wyszukaj ciąg **[Microsoft.Azure.ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus/)**, a następnie wybierz element **Microsoft.Azure.ServiceBus**. Kliknij przycisk **Zainstaluj**, aby ukończyć instalację, a następnie zamknij to okno dialogowe.
   
    ![Wybieranie pakietu NuGet][nuget-pkg]

### <a name="write-code-to-send-messages-to-the-topic"></a>Pisanie kodu w celu wysyłania komunikatów do tematu

1. W pliku Program.cs dodaj następujące instrukcje `using` na początku definicji przestrzeni nazw przed deklaracją klasy:
   
    ```csharp
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.ServiceBus;
    ```

2. W ramach klasy `Program` zadeklaruj następujące zmienne. Ustaw jako zmienną `ServiceBusConnectionString` parametry połączenia, które zostały uzyskane podczas tworzenia przestrzeni nazw, a następnie ustaw jako zmienną `TopicName` nazwę użytą podczas tworzenia tematu:
   
    ```csharp
    const string ServiceBusConnectionString = "<your_connection_string>";
    const string TopicName = "<your_topic_name>";
    static ITopicClient topicClient;
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
        topicClient = new TopicClient(ServiceBusConnectionString, TopicName);

        Console.WriteLine("======================================================");
        Console.WriteLine("Press ENTER key to exit after sending all the messages.");
        Console.WriteLine("======================================================");

        // Send messages.
        await SendMessagesAsync(numberOfMessages);

        Console.ReadKey();

        await topicClient.CloseAsync();
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
                // Create a new message to send to the topic.
                string messageBody = $"Message {i}";
                var message = new Message(Encoding.UTF8.GetBytes(messageBody));

                // Write the body of the message to the console.
                Console.WriteLine($"Sending message: {messageBody}");

                // Send the message to the topic.
                await topicClient.SendAsync(message);
            }
        }
        catch (Exception exception)
        {
            Console.WriteLine($"{DateTime.Now} :: Exception: {exception.Message}");
        }
    }
    ```
  
6. Oto jak powinien wyglądać plik Program.cs nadawcy.
   
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
            const string ServiceBusConnectionString = "<your_connection_string>";
            const string TopicName = "<your_topic_name>";
            static ITopicClient topicClient;

            static void Main(string[] args)
            {
                MainAsync().GetAwaiter().GetResult();
            }

            static async Task MainAsync()
            {
                const int numberOfMessages = 10;
                topicClient = new TopicClient(ServiceBusConnectionString, TopicName);

                Console.WriteLine("======================================================");
                Console.WriteLine("Press ENTER key to exit after sending all the messages.");
                Console.WriteLine("======================================================");

                // Send messages.
                await SendMessagesAsync(numberOfMessages);

                Console.ReadKey();

                await topicClient.CloseAsync();
            }

            static async Task SendMessagesAsync(int numberOfMessagesToSend)
            {
                try
                {
                    for (var i = 0; i < numberOfMessagesToSend; i++)
                    {
                        // Create a new message to send to the topic
                        string messageBody = $"Message {i}";
                        var message = new Message(Encoding.UTF8.GetBytes(messageBody));

                        // Write the body of the message to the console
                        Console.WriteLine($"Sending message: {messageBody}");

                        // Send the message to the topic
                        await topicClient.SendAsync(message);
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

3. Uruchom program i sprawdź witrynę Azure Portal: kliknij nazwę swojego tematu w oknie **Przegląd** przestrzeni nazw. Zostanie wyświetlony ekran **Podstawowe elementy** tematu. Zwróć uwagę, że w subskrypcji wyświetlonej w dolnej części okna wartość pola **Liczba komunikatów** dla subskrypcji wynosi teraz **10**. Przy każdym uruchomieniu aplikacji nadawcy bez pobierania komunikatów (zgodnie z opisem w następnej sekcji) ta wartość zwiększa się o 10. Należy również zauważyć, że bieżący rozmiar tematu zwiększa wartość **Bieżące** w oknie **Podstawowe elementy** za każdym razem, kiedy aplikacja dodaje komunikaty do tematu.
   
      ![Rozmiar komunikatu][topic-message]

## <a name="5-receive-messages-from-the-subscription"></a>5. Odbieranie komunikatów z subskrypcji

Aby odbierać komunikaty wysłane w poprzednim kroku, utwórz nową aplikację konsolową .NET Core i zainstaluj pakiet NuGet **Microsoft.Azure.ServiceBus**, podobnie jak w przypadku poprzedniej aplikacji nadawcy.

### <a name="write-code-to-receive-messages-from-the-subscription"></a>Pisanie kodu w celu odbierania komunikatów z subskrypcji

1. W pliku Program.cs dodaj następujące instrukcje `using` na początku definicji przestrzeni nazw przed deklaracją klasy:
   
    ```csharp
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.ServiceBus;
    ```

2. W ramach klasy `Program` zadeklaruj następujące zmienne. Ustaw jako zmienną `ServiceBusConnectionString` parametry połączenia, które zostały uzyskane podczas tworzenia przestrzeni nazw, ustaw jako zmienną `TopicName` nazwę użytą podczas tworzenia tematu, a następnie ustaw jako zmienną `SubscriptionName` nazwę użytą podczas tworzenia subskrypcji do tematu:
   
    ```csharp
    const string ServiceBusConnectionString = "<your_connection_string>";
    const string TopicName = "<your_topic_name>";
    const string SubscriptionName = "<your_subscription_name>";
    static ISubscriptionClient subscriptionClient;
    ```

3. Zastąp domyślną zawartość elementu `Main()` następującym wierszem kodu:

    ```csharp
    MainAsync().GetAwaiter().GetResult();
    ```

4. Bezpośrednio po elemencie `Main()` dodaj następującą metodę asynchroniczną `MainAsync()`, która wywołuje metodę `RegisterOnMessageHandlerAndReceiveMessages()`:

    ```csharp
    static async Task MainAsync()
    {
        subscriptionClient = new SubscriptionClient(ServiceBusConnectionString, TopicName, SubscriptionName);

        Console.WriteLine("======================================================");
        Console.WriteLine("Press ENTER key to exit after receiving all the messages.");
        Console.WriteLine("======================================================");

        // Register subscription message handler and receive messages in a loop
        RegisterOnMessageHandlerAndReceiveMessages();

        Console.ReadKey();

        await subscriptionClient.CloseAsync();
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
        subscriptionClient.RegisterMessageHandler(ProcessMessagesAsync, messageHandlerOptions);
    }
    ```    

6. Bezpośrednio po poprzedniej metodzie dodaj następującą metodę `ProcessMessagesAsync()` w celu przetworzenia odebranych komunikatów:
 
    ```csharp
    static async Task ProcessMessagesAsync(Message message, CancellationToken token)
    {
        // Process the message.
        Console.WriteLine($"Received message: SequenceNumber:{message.SystemProperties.SequenceNumber} Body:{Encoding.UTF8.GetString(message.Body)}");

        // Complete the message so that it is not received again.
        // This can be done only if the subscriptionClient is created in ReceiveMode.PeekLock mode (which is the default).
        await subscriptionClient.CompleteAsync(message.SystemProperties.LockToken);

        // Note: Use the cancellationToken passed as necessary to determine if the subscriptionClient has already been closed.
        // If subscriptionClient has already been closed, you can choose to not call CompleteAsync() or AbandonAsync() etc.
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

8. Oto jak powinien wyglądać plik Program.cs odbiorcy:
   
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
            const string ServiceBusConnectionString = "<your_connection_string>";
            const string TopicName = "<your_topic_name>";
            const string SubscriptionName = "<your_subscription_name>";
            static ISubscriptionClient subscriptionClient;

            static void Main(string[] args)
            {
                MainAsync().GetAwaiter().GetResult();
            }

            static async Task MainAsync()
            {
                subscriptionClient = new SubscriptionClient(ServiceBusConnectionString, TopicName, SubscriptionName);

                Console.WriteLine("======================================================");
                Console.WriteLine("Press ENTER key to exit after receiving all the messages.");
                Console.WriteLine("======================================================");

                // Register subscription message handler and receive messages in a loop.
                RegisterOnMessageHandlerAndReceiveMessages();

                Console.ReadKey();

                await subscriptionClient.CloseAsync();
            }

            static void RegisterOnMessageHandlerAndReceiveMessages()
            {
                // Configure the message handler options in terms of exception handling, number of concurrent messages to deliver, etc.
                var messageHandlerOptions = new MessageHandlerOptions(ExceptionReceivedHandler)
                {
                    // Maximum number of concurrent calls to the callback ProcessMessagesAsync(), set to 1 for simplicity.
                    // Set it according to how many messages the application wants to process in parallel.
                    MaxConcurrentCalls = 1,

                    // Indicates whether MessagePump should automatically complete the messages after returning from User Callback.
                    // False below indicates the Complete will be handled by the User Callback as in `ProcessMessagesAsync` below.
                    AutoComplete = false
                };

                // Register the function that processes messages.
                subscriptionClient.RegisterMessageHandler(ProcessMessagesAsync, messageHandlerOptions);
            }

            static async Task ProcessMessagesAsync(Message message, CancellationToken token)
            {
                // Process the message.
                Console.WriteLine($"Received message: SequenceNumber:{message.SystemProperties.SequenceNumber} Body:{Encoding.UTF8.GetString(message.Body)}");

                // Complete the message so that it is not received again.
                // This can be done only if the subscriptionClient is created in ReceiveMode.PeekLock mode (which is the default).
                await subscriptionClient.CompleteAsync(message.SystemProperties.LockToken);

                // Note: Use the cancellationToken passed as necessary to determine if the subscriptionClient has already been closed.
                // If subscriptionClient has already been closed, you can choose to not call CompleteAsync() or AbandonAsync() etc.
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
9. Uruchom program i ponownie sprawdź portal. Zauważ, że wartości **Liczba komunikatów** i **Bieżące** wynoszą teraz **0**.
   
    ![Długość tematu][topic-message-receive]

Gratulacje! Za pomocą biblioteki .NET Standard utworzono teraz temat i subskrypcję, wysłano 10 komunikatów i odebrano te komunikaty.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z naszym [repozytorium GitHub zawierającym przykłady](https://github.com/Azure/azure-service-bus/tree/master/samples) dotyczące usługi Service Bus, które pokazują niektóre bardziej zaawansowane funkcje obsługi komunikatów usługi Service Bus.

<!--Image references-->

[nuget-pkg]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/nuget-package.png
[topic-message]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/topic-message.png
[topic-message-receive]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/topic-message-receive.png
[createtopic1]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/create-topic1.png
[createtopic2]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/create-topic2.png
[createtopic3]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/create-topic3.png
[createtopic4]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/create-topic4.png
[github-samples]: https://github.com/Azure-Samples/azure-servicebus-messaging-samples
[azure-portal]: https://portal.azure.com
