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
ms.openlocfilehash: 03cba90874d0f42e6c404009dc4115fb4f1798ed
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/19/2018
ms.locfileid: "49468079"
---
# <a name="get-started-receiving-messages-with-the-event-processor-host-in-net-standard"></a>Wprowadzenie do odbierania komunikatów za pomocą hosta procesora zdarzeń z biblioteki .NET Standard
Event Hubs to usługa, która przetwarza duże ilości danych zdarzeń (danych telemetrycznych) z podłączonych urządzeń i aplikacji. Po zebraniu danych w usłudze Event Hubs można przechowywać dane przy użyciu klastra magazynu lub przekształcać je za pomocą dostawcy analiz w czasie rzeczywistym. Ta możliwość zbierania i przetwarzania zdarzeń na wielką skalę jest kluczowym składnikiem architektur nowoczesnych aplikacji, w tym Internetu rzeczy (IoT). Aby zapoznać się ze szczegółowym omówieniem usługi Event Hubs, zobacz [Omówienie usługi Event Hubs](event-hubs-about.md) i [Funkcje usługi Event Hubs](event-hubs-features.md).

W tym samouczku pokazano, jak napisać aplikację konsolową platformy .NET Core, która odbiera komunikaty z centrum zdarzeń za pomocą [hosta procesora zdarzeń](event-hubs-event-processor-host.md). [Host procesora zdarzeń](event-hubs-event-processor-host.md) jest klasą .NET, która upraszcza odbieranie zdarzeń z centrów zdarzeń przez zarządzanie trwałymi punktami kontrolnymi i równoległymi odbiorami z tych centrów zdarzeń. Za pomocą hosta procesora zdarzeń można podzielić zdarzenia między wieloma odbiornikami, nawet w przypadku hostowania w różnych węzłach. W tym przykładzie przedstawiono, jak używać hosta procesora zdarzeń dla jednego odbiornika. W przykładzie [Skalowanie przetwarzania zdarzeń][Skalowanie przetwarzania zdarzeń za pomocą usługi Event Hubs] przedstawiono instrukcje korzystania z hosta procesora zdarzeń z wieloma odbiornikami.

> [!NOTE]
> Ten przewodnik Szybki start możesz pobrać jako przykład z witryny [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/SampleEphReceiver), zastąpić ciągi `EventHubConnectionString`, `EventHubName`, `StorageAccountName`, `StorageAccountKey` i `StorageContainerName` wartościami swojego centrum zdarzeń, a następnie uruchomić go. Alternatywnie możesz utworzyć własne rozwiązanie, wykonując kroki opisane w tym samouczku.

## <a name="prerequisites"></a>Wymagania wstępne
* [Program Microsoft Visual Studio 2015 lub 2017](http://www.visualstudio.com). Przykłady przedstawione w tym samouczku korzystają z programu Visual Studio 2017, ale program Visual Studio 2015 jest również obsługiwany.
* [Narzędzia platformy .NET Core dla programu Visual Studio 2015 lub 2017](http://www.microsoft.com/net/core).

## <a name="create-an-event-hubs-namespace-and-an-event-hub"></a>Tworzenie przestrzeni nazw usługi Event Hubs i centrum zdarzeń
Pierwszym krokiem jest skorzystanie z witryny [Azure Portal](https://portal.azure.com) w celu utworzenia przestrzeni nazw typu Event Hubs i uzyskania poświadczeń zarządzania wymaganych przez aplikację do komunikacji z centrum zdarzeń. Aby utworzyć przestrzeń nazw i centrum zdarzeń, wykonaj procedurę opisaną w [tym artykule](event-hubs-create.md), a następnie wykonaj następujące czynności z tego samouczka.

[!INCLUDE [event-hubs-create-storage](../../includes/event-hubs-create-storage.md)]

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

## <a name="update-the-main-method-to-use-simpleeventprocessor"></a>Aktualizowanie metody Main w celu użycia klasy SimpleEventProcessor

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

> [!NOTE]
> Instrukcje w tym samouczku obejmują użycie pojedynczego wystąpienia klasy [EventProcessorHost](event-hubs-event-processor-host.md). W celu zwiększenia przepływności zalecamy uruchomienie wielu wystąpień klasy [EventProcessorHost](event-hubs-event-processor-host.md), jak pokazano w przykładzie [Skalowanie przetwarzania zdarzeń](https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3). W tych przypadkach wiele wystąpień automatycznie koordynuje się ze sobą w celu równoważenia obciążenia odebranych zdarzeń. 

## <a name="next-steps"></a>Następne kroki
W tym przewodniku Szybki start utworzono aplikację platformy .NET Standard, która odebrała komunikaty z centrum zdarzeń. Aby dowiedzieć się, jak wysyłać zdarzenia do centrum zdarzeń przy użyciu platformy .NET Standard, zobacz [Send events from event hub - .NET Standard (Wysyłanie zdarzeń z centrum zdarzeń — .NET Standard)](event-hubs-dotnet-standard-getstarted-send.md).

[1]: ./media/event-hubs-dotnet-standard-getstarted-receive-eph/event-hubs-python1.png
[2]: ./media/event-hubs-dotnet-standard-getstarted-receive-eph/netcorercv.png
