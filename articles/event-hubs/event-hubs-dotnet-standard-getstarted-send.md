---
title: Wysyłanie zdarzeń do usługi Azure Event Hubs za pomocą biblioteki .NET Standard | Microsoft Docs
description: Wprowadzenie do wysyłania zdarzeń do usługi Event Hubs przy użyciu biblioteki .NET Standard
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/16/2018
ms.author: shvija
ms.openlocfilehash: 6f95d8dc291911ac7506e33b80c2d71c8f50dfdc
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/27/2018
ms.locfileid: "47405635"
---
# <a name="get-started-sending-messages-to-azure-event-hubs-in-net-standard"></a>Wprowadzenie do wysyłania komunikatów do usługi Azure Event Hubs przy użyciu biblioteki .NET Standard

> [!NOTE]
> Ten przykład jest dostępny w witrynie [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/SampleSender).

W tym samouczku przedstawiono sposób tworzenia aplikacji konsoli .NET Core wysyłającej zestaw komunikatów do centrum zdarzeń. Rozwiązanie z repozytorium [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/SampleSender) można uruchomić w niezmienionej postaci, zastępując ciągi `EventHubConnectionString` i `EventHubName` wartościami własnego centrum zdarzeń. Można też utworzyć własne rozwiązanie, wykonując kroki opisane w tym samouczku.

## <a name="prerequisites"></a>Wymagania wstępne

* [Program Microsoft Visual Studio 2015 lub 2017](http://www.visualstudio.com). Przykłady przedstawione w tym samouczku korzystają z programu Visual Studio 2017, ale program Visual Studio 2015 jest również obsługiwany.
* [Narzędzia platformy .NET Core dla programu Visual Studio 2015 lub 2017](http://www.microsoft.com/net/core).
* Subskrypcja platformy Azure.
* [Przestrzeń nazw centrum zdarzeń i centrum zdarzeń](event-hubs-quickstart-portal.md).

Do wysyłania komunikatów do centrum zdarzeń w tym samouczku użyto aplikacji konsoli napisanej w języku C# w programie Visual Studio.

## <a name="create-an-event-hubs-namespace-and-an-event-hub"></a>Tworzenie przestrzeni nazw usługi Event Hubs i centrum zdarzeń

Aby utworzyć przestrzeń nazw i centrum zdarzeń, wykonaj procedurę opisaną w [tym artykule](event-hubs-quickstart-portal.md), a następnie kontynuuj czynności z tego samouczka.

## <a name="create-a-console-application"></a>Tworzenie aplikacji konsolowej

Uruchom program Visual Studio. W menu **Plik** kliknij pozycję **Nowy**, a następnie kliknij pozycję **Projekt**. Utwórz aplikację konsolową platformy .NET Core.

![Nowy projekt][1]

## <a name="add-the-event-hubs-nuget-package"></a>Dodawanie pakietu NuGet usługi Event Hubs

Dodaj pakiet NuGet [`Microsoft.Azure.EventHubs`](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) biblioteki .NET Standard do projektu, wykonując następujące czynności: 

1. Kliknij prawym przyciskiem myszy nowo utworzony projekt i wybierz pozycję **Zarządzaj pakietami NuGet**.
2. Kliknij kartę **Przeglądaj**, a następnie wyszukaj ciąg „Microsoft.Azure.EventHubs” i wybierz pakiet **Microsoft.Azure.EventHubs**. Kliknij przycisk **Zainstaluj**, aby ukończyć instalację, a następnie zamknij to okno dialogowe.

## <a name="write-some-code-to-send-messages-to-the-event-hub"></a>Pisanie kodu w celu wysyłania komunikatów do centrum zdarzeń

1. Dodaj następujące instrukcje `using` na początku pliku Program.cs:

    ```csharp
    using Microsoft.Azure.EventHubs;
    using System.Text;
    using System.Threading.Tasks;
    ```

2. Dodaj stałe do klasy `Program` na potrzeby parametrów połączenia usługi Event Hubs oraz ścieżki jednostki (indywidualna nazwa centrum zdarzeń). Zastąp symbole zastępcze w nawiasach odpowiednimi wartościami uzyskanymi podczas tworzenia centrum zdarzeń. Upewnij się, że `{Event Hubs connection string}` jest parametrem połączenia na poziomie przestrzeni nazw, a nie parametrem centrum zdarzeń. 

    ```csharp
    private static EventHubClient eventHubClient;
    private const string EventHubConnectionString = "{Event Hubs connection string}";
    private const string EventHubName = "{Event Hub path/name}";
    ```

3. Dodaj nową metodę o nazwie `MainAsync` do klasy `Program` w następujący sposób:

    ```csharp
    private static async Task MainAsync(string[] args)
    {
        // Creates an EventHubsConnectionStringBuilder object from the connection string, and sets the EntityPath.
        // Typically, the connection string should have the entity path in it, but this simple scenario
        // uses the connection string from the namespace.
        var connectionStringBuilder = new EventHubsConnectionStringBuilder(EventHubConnectionString)
        {
            EntityPath = EventHubName
        };

        eventHubClient = EventHubClient.CreateFromConnectionString(connectionStringBuilder.ToString());

        await SendMessagesToEventHub(100);

        await eventHubClient.CloseAsync();

        Console.WriteLine("Press ENTER to exit.");
        Console.ReadLine();
    }
    ```

4. Dodaj nową metodę o nazwie `SendMessagesToEventHub` do klasy `Program` w następujący sposób:

    ```csharp
    // Creates an event hub client and sends 100 messages to the event hub.
    private static async Task SendMessagesToEventHub(int numMessagesToSend)
    {
        for (var i = 0; i < numMessagesToSend; i++)
        {
            try
            {
                var message = $"Message {i}";
                Console.WriteLine($"Sending message: {message}");
                await eventHubClient.SendAsync(new EventData(Encoding.UTF8.GetBytes(message)));
            }
            catch (Exception exception)
            {
                Console.WriteLine($"{DateTime.Now} > Exception: {exception.Message}");
            }

            await Task.Delay(10);
        }

        Console.WriteLine($"{numMessagesToSend} messages sent.");
    }
    ```

5. Dodaj następujący kod do metody `Main` w klasie `Program`:

    ```csharp
    MainAsync(args).GetAwaiter().GetResult();
    ```

   Oto jak powinien wyglądać plik Program.cs.

    ```csharp
    namespace SampleSender
    {
        using System;
        using System.Text;
        using System.Threading.Tasks;
        using Microsoft.Azure.EventHubs;

        public class Program
        {
            private static EventHubClient eventHubClient;
            private const string EventHubConnectionString = "{Event Hubs connection string}";
            private const string EventHubName = "{Event Hub path/name}";

            public static void Main(string[] args)
            {
                MainAsync(args).GetAwaiter().GetResult();
            }

            private static async Task MainAsync(string[] args)
            {
                // Creates an EventHubsConnectionStringBuilder object from the connection string, and sets the EntityPath.
                // Typically, the connection string should have the entity path in it, but for the sake of this simple scenario
                // we are using the connection string from the namespace.
                var connectionStringBuilder = new EventHubsConnectionStringBuilder(EventHubConnectionString)
                {
                    EntityPath = EventHubName
                };

                eventHubClient = EventHubClient.CreateFromConnectionString(connectionStringBuilder.ToString());

                await SendMessagesToEventHub(100);

                await eventHubClient.CloseAsync();

                Console.WriteLine("Press ENTER to exit.");
                Console.ReadLine();
            }

            // Creates an event hub client and sends 100 messages to the event hub.
            private static async Task SendMessagesToEventHub(int numMessagesToSend)
            {
                for (var i = 0; i < numMessagesToSend; i++)
                {
                    try
                    {
                        var message = $"Message {i}";
                        Console.WriteLine($"Sending message: {message}");
                        await eventHubClient.SendAsync(new EventData(Encoding.UTF8.GetBytes(message)));
                    }
                    catch (Exception exception)
                    {
                        Console.WriteLine($"{DateTime.Now} > Exception: {exception.Message}");
                    }

                    await Task.Delay(10);
                }

                Console.WriteLine($"{numMessagesToSend} messages sent.");
            }
        }
    }
    ```

6. Uruchom program i upewnij się, że nie ma w nim żadnych błędów.

Gratulacje! Wysłano komunikaty do centrum zdarzeń.

## <a name="next-steps"></a>Następne kroki
Następujące linki pozwalają dowiedzieć się więcej na temat usługi Event Hubs:

* [Odbieranie zdarzeń z usługi Event Hubs](event-hubs-dotnet-standard-getstarted-receive-eph.md)
* [Omówienie usługi Event Hubs](event-hubs-what-is-event-hubs.md)
* [Tworzenie centrum zdarzeń](event-hubs-create.md)
* [Event Hubs — często zadawane pytania](event-hubs-faq.md)

[1]: ./media/event-hubs-dotnet-standard-getstarted-send/netcoresnd.png
