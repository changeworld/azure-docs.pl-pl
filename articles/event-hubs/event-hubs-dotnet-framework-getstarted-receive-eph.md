---
title: Odbieranie zdarzeń z usługi Azure Event Hubs za pomocą programu .NET Framework | Microsoft Docs
description: Postępuj zgodnie z tym samouczkiem, aby odbierać zdarzenia z usługi Azure Event Hubs za pomocą programu .NET Framework.
services: event-hubs
documentationcenter: ''
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: c4974bd3-2a79-48a1-aa3b-8ee2d6655b28
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/01/2018
ms.author: sethm
ms.openlocfilehash: 8fd70380dbb88f379789e1a4730934dcd38cac5a
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/21/2018
ms.locfileid: "29393222"
---
# <a name="receive-events-from-azure-event-hubs-using-the-net-framework"></a>Odbieranie zdarzeń z usługi Azure Event Hubs za pomocą programu .NET Framework

## <a name="introduction"></a>Wprowadzenie

Event Hubs to usługa, która przetwarza duże ilości danych zdarzeń (danych telemetrycznych) z podłączonych urządzeń i aplikacji. Po zebraniu danych w usłudze Event Hubs można przechowywać dane przy użyciu klastra magazynu lub przekształcać je za pomocą dostawcy analiz w czasie rzeczywistym. Ta możliwość zbierania i przetwarzania zdarzeń na wielką skalę jest kluczowym składnikiem architektur nowoczesnych aplikacji, w tym Internetu rzeczy (IoT).

W tym samouczku pokazano, jak napisać aplikację konsoli .NET Framework, która odbiera komunikaty z centrum zdarzeń za pomocą **[hosta procesora zdarzeń][EventProcessorHost]**. Aby wysyłać zdarzenia przy użyciu programu .NET Framework, zobacz artykuł [Wysyłanie zdarzeń do usługi Azure Event Hubs za pomocą programu .NET Framework](event-hubs-dotnet-framework-getstarted-send.md) lub kliknij odpowiedni język wysyłający w spisie treści po lewej stronie.

[Host procesora zdarzeń][EventProcessorHost] jest klasą .NET, która upraszcza odbieranie zdarzeń z centrów zdarzeń przez zarządzanie trwałymi punktami kontrolnymi i równoległymi odbiorami z tej usługi. Za pomocą [hosta procesora zdarzeń][Event Processor Host] można podzielić zdarzenia między wieloma odbiornikami, nawet w przypadku hostowania w różnych węzłach. W tym przykładzie przedstawiono, jak używać [hosta procesora zdarzeń][EventProcessorHost] dla jednego odbiornika. W przykładzie [Skalowanie przetwarzania zdarzeń][Scale out Event Processing with Event Hubs] przedstawiono instrukcje korzystania z [hosta procesora zdarzeń][EventProcessorHost] z wieloma odbiornikami.

## <a name="prerequisites"></a>Wymagania wstępne

Do wykonania kroków tego samouczka niezbędne jest spełnienie następujących wymagań wstępnych:

* [Program Microsoft Visual Studio w wersji 2015 lub nowszej](http://visualstudio.com). Na zrzutach ekranów przedstawionych w tym samouczku używany jest program Visual Studio 2017.
* Aktywne konto platformy Azure. Jeśli go nie masz, możesz utworzyć bezpłatne konto w zaledwie kilka minut. Aby uzyskać szczegółowe informacje, zobacz artykuł [Bezpłatna wersja próbna platformy Azure](https://azure.microsoft.com/free/).

## <a name="create-an-event-hubs-namespace-and-an-event-hub"></a>Tworzenie przestrzeni nazw usługi Event Hubs i centrum zdarzeń

Pierwszym krokiem jest skorzystanie z witryny [Azure Portal](https://portal.azure.com) w celu utworzenia przestrzeni nazw typu Event Hubs i uzyskania poświadczeń zarządzania wymaganych przez aplikację do komunikacji z centrum zdarzeń. Aby utworzyć obszar nazw i centrum zdarzeń, wykonaj procedurę opisaną w [tym artykule](event-hubs-create.md), a następnie wykonaj następujące czynności z tego samouczka.

## <a name="create-an-azure-storage-account"></a>Tworzenie konta usługi Azure Storage

Aby móc korzystać z [hosta procesora zdarzeń][EventProcessorHost], trzeba mieć [konto usługi Azure Storage][Azure Storage account]:

1. Zaloguj się do [portalu Azure][Azure portal]i kliknij przycisk **Utwórz zasób** w lewym górnym rogu ekranu.
2. Kliknij pozycję **Magazyn**, a następnie pozycję **Konto magazynu**.
   
    ![](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-storage1.png)
3. W **utworzyć konto magazynu** okienku, wpisz nazwę konta magazynu. Wybierz subskrypcję platformy Azure, grupę zasobów i lokalizację, w której chcesz utworzyć zasób. Następnie kliknij pozycję **Utwórz**.
   
    ![](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-storage2.png)
4. Na liście kont magazynu kliknij nowo utworzone konto.
5. W okienku konta magazynu, kliknij **klucze dostępu**. Skopiuj wartość **klucz1**, aby użyć jej w dalszej części tego samouczka.
   
    ![](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-storage3.png)

## <a name="create-a-receiver-console-application"></a>Tworzenie aplikacji konsolowej odbiorcy

1. W programie Visual Studio utwórz nowy projekt aplikacji klasycznej Visual C# za pomocą szablonu projektu **Aplikacja konsoli**. Nazwij projekt **Odbiornik**.
   
    ![](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-receiver-csharp1.png)
2. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy projekt **Odbiornik**, a następnie kliknij pozycję **Zarządzaj pakietami NuGet rozwiązania**.
3. Kliknij kartę **Przeglądanie**, a następnie wyszukaj ciąg `Microsoft Azure Service Bus Event Hub - EventProcessorHost`. Kliknij pozycję **Zainstaluj** i zaakceptuj warunki użytkowania.
   
    ![](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-eph-csharp1.png)
   
    Program Visual Studio pobierze, zainstaluje i doda odniesienie do [centrum zdarzeń usługi Azure Service Bus — pakiet NuGet EventProcessorHost](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost) wraz ze wszystkimi jego zależnościami.
4. Kliknij prawym przyciskiem myszy projekt **Odbiornik**, kliknij przycisk **Dodaj**, a następnie kliknij opcję **Klasa**. Nadaj nowej klasie nazwę **SimpleEventProcessor**, a następnie kliknij przycisk **Dodaj**, aby utworzyć klasę.
   
    ![](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-receiver-csharp2.png)
5. W górnej części pliku SimpleEventProcessor.cs dodaj następujące instrukcje:
    
  ```csharp
  using Microsoft.ServiceBus.Messaging;
  using System.Diagnostics;
  ```
    
  Następnie zastąp następujący kod treścią klasy:
    
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
6. W klasie **Program** dodaj następującą instrukcję `using` w górnej części pliku:
    
  ```csharp
  using Microsoft.ServiceBus.Messaging;
  ```
    
  Następnie zastąp metodę `Main` w klasie `Program` następującym kodem, zastępując nazwę centrum zdarzeń i parametry połączenia na poziomie przestrzeni nazw, które zostały zapisane wcześniej, oraz konto magazynu i klucz skopiowane we wcześniejszych sekcjach. 
    
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

7. Uruchom program i upewnij się, że nie ma w nim żadnych błędów.
  
Gratulacje! Odebrano komunikaty z centrum zdarzeń za pomocą hosta procesora zdarzeń.


> [!NOTE]
> Instrukcje w tym samouczku obejmują użycie pojedynczego wystąpienia klasy [EventProcessorHost][EventProcessorHost]. W celu zwiększenia przepływności zaleca się uruchomienie wielu wystąpień klasy [EventProcessorHost][EventProcessorHost], jak przedstawiono w przykładzie [Skalowanie przetwarzania zdarzeń][Skalowanie przetwarzania zdarzeń]. W tych przypadkach różne wystąpienia automatycznie koordynują się ze sobą w celu równoważenia obciążenia odebranych zdarzeń. Jeśli chcesz, aby wiele odbiorników przetwarzało *wszystkie* zdarzenia, musisz użyć koncepcji **ConsumerGroup**. W przypadku odbierania zdarzeń z różnych maszyn dobrym rozwiązaniem może być określenie nazw wystąpień klasy [EventProcessorHost][EventProcessorHost] w oparciu o maszyny (lub role), w których są one wdrażane. Więcej informacji można znaleźć w artykułach [Omówienie usługi Azure Event Hubs][Event Hubs overview] i [Event Hubs programming guide][Event Hubs Programming Guide] (Przewodnik programowania w usłudze Event Hubs).
> 
> 

## <a name="next-steps"></a>Kolejne kroki

Teraz, gdy masz utworzoną działającą aplikację, która tworzy centrum zdarzeń oraz wysyła i odbiera dane, możesz dowiedzieć się więcej, odwiedzając jeden z następujących linków:

* [Host procesora zdarzeń][Event Processor Host]
* [Przegląd usługi Event Hubs][Event Hubs overview]
* [Event Hubs — często zadawane pytania](event-hubs-faq.md)

<!-- Images. -->
[19]: ./media/event-hubs-csharp-ephcs-getstarted/create-eh-proj1.png
[20]: ./media/event-hubs-csharp-ephcs-getstarted/create-eh-proj2.png
[21]: ./media/event-hubs-csharp-ephcs-getstarted/run-csharp-ephcs1.png
[22]: ./media/event-hubs-csharp-ephcs-getstarted/run-csharp-ephcs2.png

<!-- Links -->
[EventProcessorHost]: https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost
[Event Hubs overview]: event-hubs-what-is-event-hubs.md
[Scale out Event Processing with Event Hubs]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3
[Event Hubs Programming Guide]: event-hubs-programming-guide.md
[Azure Storage account]:../storage/common/storage-create-storage-account.md
[Event Processor Host]: /dotnet/api/microsoft.servicebus.messaging.eventprocessorhost
[Azure portal]: https://portal.azure.com
