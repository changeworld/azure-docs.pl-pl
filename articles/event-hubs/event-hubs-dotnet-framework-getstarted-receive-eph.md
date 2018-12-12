---
title: Odbieranie zdarzeń za pomocą programu .NET Framework — usługa Azure Event Hubs | Dokumentacja firmy Microsoft
description: Postępuj zgodnie z tym samouczkiem, aby odbierać zdarzenia z usługi Azure Event Hubs za pomocą programu .NET Framework.
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: c4974bd3-2a79-48a1-aa3b-8ee2d6655b28
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 8052b797707f7913fdd678f4dd51822754623104
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/08/2018
ms.locfileid: "53077260"
---
# <a name="receive-events-from-azure-event-hubs-using-the-net-framework"></a>Odbieranie zdarzeń z usługi Azure Event Hubs za pomocą programu .NET Framework

## <a name="introduction"></a>Wprowadzenie

Event Hubs to usługa, która przetwarza duże ilości danych zdarzeń (danych telemetrycznych) z podłączonych urządzeń i aplikacji. Po zebraniu danych w usłudze Event Hubs można przechowywać dane przy użyciu klastra magazynu lub przekształcać je za pomocą dostawcy analiz w czasie rzeczywistym. Ta możliwość zbierania i przetwarzania zdarzeń na wielką skalę jest kluczowym składnikiem architektur nowoczesnych aplikacji, w tym Internetu rzeczy (IoT). Aby zapoznać się ze szczegółowym omówieniem usługi Event Hubs, zobacz [Omówienie usługi Event Hubs](event-hubs-about.md) i [Funkcje usługi Event Hubs](event-hubs-features.md).

W tym samouczku pokazano, jak napisać aplikację konsoli .NET Framework, która odbiera komunikaty z Centrum zdarzeń za pomocą [hosta procesora zdarzeń](event-hubs-event-processor-host.md). [Host procesora zdarzeń](event-hubs-event-processor-host.md) jest klasą .NET, która upraszcza odbieranie zdarzeń z centrów zdarzeń przez zarządzanie trwałymi punktami kontrolnymi i równoległymi odbiorami z tych centrów zdarzeń. Za pomocą hosta procesora zdarzeń można podzielić zdarzenia między wieloma odbiornikami, nawet w przypadku hostowania w różnych węzłach. W tym przykładzie przedstawiono, jak używać hosta procesora zdarzeń dla jednego odbiornika. [Skalowanie przetwarzania zdarzeń] [ Scale out Event Processing with Event Hubs] przykład pokazuje, jak używać hosta procesora zdarzeń z wieloma odbiornikami.

## <a name="prerequisites"></a>Wymagania wstępne

Do wykonania kroków tego samouczka niezbędne jest spełnienie następujących wymagań wstępnych:

* [Microsoft Visual Studio 2017 lub nowszego](https://visualstudio.com).

## <a name="create-an-event-hubs-namespace-and-an-event-hub"></a>Tworzenie przestrzeni nazw usługi Event Hubs i centrum zdarzeń
Pierwszym krokiem jest skorzystanie z witryny [Azure Portal](https://portal.azure.com) w celu utworzenia przestrzeni nazw typu Event Hubs i uzyskania poświadczeń zarządzania wymaganych przez aplikację do komunikacji z centrum zdarzeń. Aby utworzyć przestrzeń nazw i centrum zdarzeń, wykonaj procedurę opisaną w [tym artykule](event-hubs-create.md), a następnie wykonaj następujące czynności z tego samouczka.

[!INCLUDE [event-hubs-create-storage](../../includes/event-hubs-create-storage.md)]

## <a name="create-a-console-application"></a>Tworzenie aplikacji konsolowej

W programie Visual Studio utwórz nowy projekt aplikacji klasycznej Visual C# za pomocą szablonu projektu **Aplikacja konsoli**. Nazwij projekt **Odbiornik**.
   
![Tworzenie aplikacji konsolowej](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-receiver-csharp1.png)

## <a name="add-the-event-hubs-nuget-package"></a>Dodawanie pakietu NuGet usługi Event Hubs

1. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy projekt **Odbiornik**, a następnie kliknij pozycję **Zarządzaj pakietami NuGet rozwiązania**.
2. Kliknij kartę **Przeglądanie**, a następnie wyszukaj ciąg `Microsoft Azure Service Bus Event Hub - EventProcessorHost`. Kliknij pozycję **Zainstaluj** i zaakceptuj warunki użytkowania.
   
    ![Wyszukaj pakiet NuGet hosta procesora zdarzeń](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-eph-csharp1.png)
   
    Program Visual Studio pobierze, zainstaluje i doda odniesienie do [centrum zdarzeń usługi Azure Service Bus — pakiet NuGet EventProcessorHost](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost) wraz ze wszystkimi jego zależnościami.

## <a name="implement-the-ieventprocessor-interface"></a>Implementowanie interfejsu IEventProcessor

1. Kliknij prawym przyciskiem myszy projekt **Odbiornik**, kliknij przycisk **Dodaj**, a następnie kliknij opcję **Klasa**. Nadaj nowej klasie nazwę **SimpleEventProcessor**, a następnie kliknij przycisk **Dodaj**, aby utworzyć klasę.
   
    ![Dodawanie klasy SimpleEventProcessor](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-receiver-csharp2.png)
2. W górnej części pliku SimpleEventProcessor.cs dodaj następujące instrukcje:
    
      ```csharp
      using Microsoft.ServiceBus.Messaging;
      using System.Diagnostics;
      ```
    
3. Zastąp następujący kod treścią klasy:
    
      ```csharp
      class SimpleEventProcessor : IEventProcessor
      {
        Stopwatch checkpointStopWatch;
        
        async Task IEventProcessor.CloseAsync(PartitionContext context, CloseReason reason)
        {
            Console.WriteLine("Processor Shutting Down. Partition '{0}', Reason: '{1}'.", context.Lease.PartitionId, reason);
            if (reason == CloseReason.Shutdown)
            {
                await context.CheckpointAsync();
            }
        }
        
        Task IEventProcessor.OpenAsync(PartitionContext context)
        {
            Console.WriteLine("SimpleEventProcessor initialized.  Partition: '{0}', Offset: '{1}'", context.Lease.PartitionId, context.Lease.Offset);
            this.checkpointStopWatch = new Stopwatch();
            this.checkpointStopWatch.Start();
            return Task.FromResult<object>(null);
        }
        
        async Task IEventProcessor.ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
        {
            foreach (EventData eventData in messages)
            {
                string data = Encoding.UTF8.GetString(eventData.GetBytes());
        
                Console.WriteLine(string.Format("Message received.  Partition: '{0}', Data: '{1}'",
                    context.Lease.PartitionId, data));
            }
        
            //Call checkpoint every 5 minutes, so that worker can resume processing from 5 minutes back if it restarts.
            if (this.checkpointStopWatch.Elapsed > TimeSpan.FromMinutes(5))
            {
                await context.CheckpointAsync();
                this.checkpointStopWatch.Restart();
            }
        }
      }
      ```
    
      Klasa ta zostanie wywołana przez klasę **EventProcessorHost** do przetwarzania zdarzeń odebranych z centrum zdarzeń. Klasa `SimpleEventProcessor` używa stopera, aby okresowo wywoływać metodę punktu kontrolnego w kontekście klasy **EventProcessorHost**. Takie przetwarzanie daje gwarancję, że jeśli odbiorca zostanie ponownie uruchomiony, nie straci więcej niż pięć minut operacji przetwarzania.

## <a name="update-the-main-method-to-use-simpleeventprocessor"></a>Aktualizowanie metody Main w celu użycia klasy SimpleEventProcessor

1. W klasie **Program** dodaj następującą instrukcję `using` w górnej części pliku:
    
      ```csharp
      using Microsoft.ServiceBus.Messaging;
      ```
    
2. Zastąp `Main` method in Class metoda `Program` klasy następujący kod, zastępując nazwę Centrum zdarzeń i parametry połączenia na poziomie przestrzeni nazw, które zostały zapisane wcześniej oraz konto magazynu i klucz skopiowane we wcześniejszych sekcjach. 
    
      ```csharp
      static void Main(string[] args)
      {
        string eventHubConnectionString = "{Event Hubs namespace connection string}";
        string eventHubName = "{Event Hub name}";
        string storageAccountName = "{storage account name}";
        string storageAccountKey = "{storage account key}";
        string storageConnectionString = string.Format("DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}", storageAccountName, storageAccountKey);
        
        string eventProcessorHostName = Guid.NewGuid().ToString();
        EventProcessorHost eventProcessorHost = new EventProcessorHost(eventProcessorHostName, eventHubName, EventHubConsumerGroup.DefaultGroupName, eventHubConnectionString, storageConnectionString);
        Console.WriteLine("Registering EventProcessor...");
        var options = new EventProcessorOptions();
        options.ExceptionReceived += (sender, e) => { Console.WriteLine(e.Exception); };
        eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>(options).Wait();
        
        Console.WriteLine("Receiving. Press enter key to stop worker.");
        Console.ReadLine();
        eventProcessorHost.UnregisterEventProcessorAsync().Wait();
      }
      ```
    
3. Uruchom program i upewnij się, że nie ma w nim żadnych błędów.
  
Gratulacje! Odebrano komunikaty z centrum zdarzeń za pomocą hosta procesora zdarzeń.


> [!NOTE]
> Instrukcje w tym samouczku obejmują użycie pojedynczego wystąpienia klasy [EventProcessorHost](event-hubs-event-processor-host.md). W celu zwiększenia przepływności zalecamy uruchomienie wielu wystąpień klasy [EventProcessorHost](event-hubs-event-processor-host.md), jak pokazano w przykładzie [Skalowanie przetwarzania zdarzeń](https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3). W tych przypadkach wiele wystąpień automatycznie koordynuje się ze sobą w celu równoważenia obciążenia odebranych zdarzeń. 

## <a name="next-steps"></a>Kolejne kroki
W tym przewodniku Szybki Start utworzono aplikację .NET Framework, które odebrano komunikaty z Centrum zdarzeń. Aby dowiedzieć się, jak do wysyłania zdarzeń do Centrum zdarzeń za pomocą .NET Framework, zobacz [wysyłać zdarzenia z Centrum zdarzeń — .NET Framework](event-hubs-dotnet-framework-getstarted-send.md).

<!-- Images. -->
[19]: ./media/event-hubs-csharp-ephcs-getstarted/create-eh-proj1.png
[20]: ./media/event-hubs-csharp-ephcs-getstarted/create-eh-proj2.png
[21]: ./media/event-hubs-csharp-ephcs-getstarted/run-csharp-ephcs1.png
[22]: ./media/event-hubs-csharp-ephcs-getstarted/run-csharp-ephcs2.png

<!-- Links -->
[EventProcessorHost]: /dotnet/api/microsoft.servicebus.messaging.eventprocessorhost
[Event Hubs overview]: event-hubs-about.md
[Scale out Event Processing with Event Hubs]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3
[Event Hubs Programming Guide]: event-hubs-programming-guide.md
[Azure Storage account]:../storage/common/storage-create-storage-account.md
[Event Processor Host]: event-hubs-event-processor-host.md
[Azure portal]: https://portal.azure.com
