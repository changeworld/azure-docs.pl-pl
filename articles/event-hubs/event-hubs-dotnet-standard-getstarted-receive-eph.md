---
title: Odbieranie zdarzeń z usługi Azure Event Hubs za pomocą biblioteki .NET Standard | Microsoft Docs
description: Wprowadzenie do odbierania komunikatów za pomocą klasy EventProcessorHost z biblioteki .NET Standard
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
ms.openlocfilehash: 03acd63ff00f0a3017297d1998289c8e68f0f290
ms.sourcegitcommit: 974c478174f14f8e4361a1af6656e9362a30f515
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/20/2018
ms.locfileid: "41918280"
---
# <a name="get-started-receiving-messages-with-the-event-processor-host-in-net-standard"></a>Wprowadzenie do odbierania komunikatów za pomocą hosta procesora zdarzeń z biblioteki .NET Standard

> [!NOTE]
> Ten przykład jest dostępny w witrynie [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/SampleEphReceiver).

W tym samouczku pokazano, jak napisać aplikację konsoli .NET Core, która odbiera komunikaty z centrum zdarzeń za pomocą biblioteki **hosta procesora zdarzeń**. Rozwiązanie z repozytorium [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/SampleEphReceiver) można uruchomić w niezmienionej postaci, zastępując ciągi wartościami własnego centrum zdarzeń i konta magazynu. Można też utworzyć własne rozwiązanie, wykonując kroki opisane w tym samouczku.

## <a name="prerequisites"></a>Wymagania wstępne

* [Program Microsoft Visual Studio 2015 lub 2017](http://www.visualstudio.com). Przykłady przedstawione w tym samouczku korzystają z programu Visual Studio 2017, ale program Visual Studio 2015 jest również obsługiwany.
* [Narzędzia platformy .NET Core dla programu Visual Studio 2015 lub 2017](http://www.microsoft.com/net/core).
* Subskrypcja platformy Azure.
* Przestrzeń nazw usługi Azure Event Hubs i centrum zdarzeń.
* Konto usługi Azure Storage.

## <a name="create-an-event-hubs-namespace-and-an-event-hub"></a>Tworzenie przestrzeni nazw usługi Event Hubs i centrum zdarzeń  

Pierwszym krokiem jest skorzystanie z witryny [Azure Portal](https://portal.azure.com) w celu utworzenia przestrzeni nazw typu Event Hubs i uzyskania poświadczeń zarządzania wymaganych przez aplikację do komunikacji z centrum zdarzeń. Aby utworzyć przestrzeń nazw i centrum zdarzeń, wykonaj procedurę opisaną w [tym artykule](event-hubs-create.md), a następnie kontynuuj czynności z tego samouczka.  

## <a name="create-an-azure-storage-account"></a>Tworzenie konta usługi Azure Storage  

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).  
2. W lewym okienku nawigacji portalu kliknij kolejno pozycje **Utwórz zasób**, **Magazyn**, a następnie **Konto magazynu**.  
3. Wypełnij pola w oknie konta magazynu, a następnie kliknij przycisk **Utwórz**.

    ![Tworzenie konta magazynu][1]

4. Po wyświetleniu komunikatu **Wdrożenia zakończone pomyślnie** kliknij nazwę nowego konta magazynu. W oknie **Podstawowe elementy** kliknij pozycję **Obiekty blob**. W otwartym oknie dialogowym **Blob Service** kliknij pozycję **+ Kontener** u góry. Nadaj kontenerowi nazwę, a następnie zamknij okno **Blob service**.  
5. W oknie po lewej stronie kliknij pozycję **Klucze dostępu** i skopiuj nazwę kontenera magazynu, konta magazynu i wartość pola **Klucz1**. Zapisz te wartości w Notatniku lub innej tymczasowej lokalizacji.  

## <a name="create-a-console-application"></a>Tworzenie aplikacji konsolowej

Uruchom program Visual Studio. W menu **Plik** kliknij pozycję **Nowy**, a następnie kliknij pozycję **Projekt**. Utwórz aplikację konsolową platformy .NET Core.

![Nowy projekt][2]

## <a name="add-the-event-hubs-nuget-package"></a>Dodawanie pakietu NuGet usługi Event Hubs

Dodaj do swojego projektu pakiety NuGet biblioteki .NET Standard [**Microsoft.Azure.EventHubs**](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) i [**Microsoft.Azure.EventHubs.Processor**](https://www.nuget.org/packages/Microsoft.Azure.EventHubs.Processor/), wykonując następujące czynności: 

1. Kliknij prawym przyciskiem myszy nowo utworzony projekt i wybierz pozycję **Zarządzaj pakietami NuGet**.
2. Kliknij kartę **Przeglądaj**, wyszukaj ciąg **Microsoft.Azure.EventHubs**, a następnie wybierz pakiet **Microsoft.Azure.EventHubs**. Kliknij przycisk **Zainstaluj**, aby ukończyć instalację, a następnie zamknij to okno dialogowe.
3. Powtórz kroki 1 i 2 w celu zainstalowania pakietu **Microsoft.Azure.EventHubs.Processor**.

## <a name="implement-the-ieventprocessor-interface"></a>Implementowanie interfejsu IEventProcessor

1. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy projekt, kliknij polecenie **Dodaj**, a następnie kliknij pozycję **Klasa**. Nowej klasie nadaj nazwę **SimpleEventProcessor**.

2. Otwórz plik SimpleEventProcessor.cs i dodaj na jego początku następujące instrukcje `using`.

    ```csharp
    using Microsoft.Azure.EventHubs;
    using Microsoft.Azure.EventHubs.Processor;
    using System.Threading.Tasks;
    ```

3. Zaimplementuj interfejs `IEventProcessor`. Zastąp całą zawartość klasy `SimpleEventProcessor` następującym kodem:

    ```csharp
    public class SimpleEventProcessor : IEventProcessor
    {
        public Task CloseAsync(PartitionContext context, CloseReason reason)
        {
            Console.WriteLine($"Processor Shutting Down. Partition '{context.PartitionId}', Reason: '{reason}'.");
            return Task.CompletedTask;
        }

        public Task OpenAsync(PartitionContext context)
        {
            Console.WriteLine($"SimpleEventProcessor initialized. Partition: '{context.PartitionId}'");
            return Task.CompletedTask;
        }

        public Task ProcessErrorAsync(PartitionContext context, Exception error)
        {
            Console.WriteLine($"Error on Partition: {context.PartitionId}, Error: {error.Message}");
            return Task.CompletedTask;
        }

        public Task ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
        {
            foreach (var eventData in messages)
            {
                var data = Encoding.UTF8.GetString(eventData.Body.Array, eventData.Body.Offset, eventData.Body.Count);
                Console.WriteLine($"Message received. Partition: '{context.PartitionId}', Data: '{data}'");
            }

            return context.CheckpointAsync();
        }
    }
    ```

## <a name="write-a-main-console-method-that-uses-the-simpleeventprocessor-class-to-receive-messages"></a>Napisanie głównej metody konsoli korzystającej z klasy SimpleEventProcessor do odbierania komunikatów

1. Dodaj następujące instrukcje `using` w górnej części pliku Program.cs.

    ```csharp
    using Microsoft.Azure.EventHubs;
    using Microsoft.Azure.EventHubs.Processor;
    using System.Threading.Tasks;
    ```

2. Dodaj stałe do klasy `Program` dla parametrów połączenia centrum zdarzeń, nazwy centrum zdarzeń, nazwy kontenera konta magazynu, nazwy konta magazynu i klucza konta magazynu. Dodaj następujący kod, zastępując tekst zastępczy odpowiednimi wartościami:

    ```csharp
    private const string EventHubConnectionString = "{Event Hubs connection string}";
    private const string EventHubName = "{Event Hub path/name}";
    private const string StorageContainerName = "{Storage account container name}";
    private const string StorageAccountName = "{Storage account name}";
    private const string StorageAccountKey = "{Storage account key}";

    private static readonly string StorageConnectionString = string.Format("DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}", StorageAccountName, StorageAccountKey);
    ```   

3. Dodaj nową metodę o nazwie `MainAsync` do klasy `Program` w następujący sposób:

    ```csharp
    private static async Task MainAsync(string[] args)
    {
        Console.WriteLine("Registering EventProcessor...");

        var eventProcessorHost = new EventProcessorHost(
            EventHubName,
            PartitionReceiver.DefaultConsumerGroupName,
            EventHubConnectionString,
            StorageConnectionString,
            StorageContainerName);

        // Registers the Event Processor Host and starts receiving messages
        await eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>();

        Console.WriteLine("Receiving. Press ENTER to stop worker.");
        Console.ReadLine();

        // Disposes of the Event Processor Host
        await eventProcessorHost.UnregisterEventProcessorAsync();
    }
    ```

3. Dodaj następujący wiersz kodu do metody `Main`:

    ```csharp
    MainAsync(args).GetAwaiter().GetResult();
    ```

    Oto jak powinien wyglądać plik Program.cs:

    ```csharp
    namespace SampleEphReceiver
    {

        public class Program
        {
            private const string EventHubConnectionString = "{Event Hubs connection string}";
            private const string EventHubName = "{Event Hub path/name}";
            private const string StorageContainerName = "{Storage account container name}";
            private const string StorageAccountName = "{Storage account name}";
            private const string StorageAccountKey = "{Storage account key}";

            private static readonly string StorageConnectionString = string.Format("DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}", StorageAccountName, StorageAccountKey);

            public static void Main(string[] args)
            {
                MainAsync(args).GetAwaiter().GetResult();
            }

            private static async Task MainAsync(string[] args)
            {
                Console.WriteLine("Registering EventProcessor...");

                var eventProcessorHost = new EventProcessorHost(
                    EventHubName,
                    PartitionReceiver.DefaultConsumerGroupName,
                    EventHubConnectionString,
                    StorageConnectionString,
                    StorageContainerName);

                // Registers the Event Processor Host and starts receiving messages
                await eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>();

                Console.WriteLine("Receiving. Press ENTER to stop worker.");
                Console.ReadLine();

                // Disposes of the Event Processor Host
                await eventProcessorHost.UnregisterEventProcessorAsync();
            }
        }
    }
    ```

4. Uruchom program i upewnij się, że nie ma w nim żadnych błędów.

Gratulacje! Odebrano komunikaty z centrum zdarzeń za pomocą hosta procesora zdarzeń.

## <a name="next-steps"></a>Następne kroki
Następujące linki pozwalają dowiedzieć się więcej na temat usługi Event Hubs:

* [Omówienie usługi Event Hubs](event-hubs-what-is-event-hubs.md)
* [Tworzenie centrum zdarzeń](event-hubs-create.md)
* [Event Hubs — często zadawane pytania](event-hubs-faq.md)

[1]: ./media/event-hubs-dotnet-standard-getstarted-receive-eph/event-hubs-python1.png
[2]: ./media/event-hubs-dotnet-standard-getstarted-receive-eph/netcorercv.png
