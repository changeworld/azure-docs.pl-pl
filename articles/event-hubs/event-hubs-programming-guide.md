---
title: Przewodnik po programowaniu platformy .NET — usługi Azure Event Hubs (starsze) | Dokumenty firmy Microsoft
description: Ten artykuł zawiera informacje dotyczące sposobu pisania kodu dla usługi Azure Event Hubs przy użyciu narzędzia Azure .NET SDK.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
ms.service: event-hubs
ms.custom: seodec18
ms.topic: article
ms.date: 01/15/2020
ms.author: shvija
ms.openlocfilehash: d958c2d32c16874676f46bb216067fe2d7bbe784
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79280978"
---
# <a name="net-programming-guide-for-azure-event-hubs-legacy-microsoftazureeventhubs-package"></a>Przewodnik po programowaniu platformy .NET dla centrów zdarzeń platformy Azure (starszy pakiet Microsoft.Azure.EventHubs)
W tym artykule omówiono niektóre typowe scenariusze podczas pisania kodu przy użyciu usługi Azure Event Hubs. Przyjęto założenie, że wstępnie znasz i rozumiesz usługę Event Hubs. Omówienie koncepcji usługi Event Hubs można znaleźć w temacie [Przegląd usługi Event Hubs](event-hubs-what-is-event-hubs.md).

> [!WARNING]
> Ten przewodnik jest przeznaczony dla starego pakietu **Microsoft.Azure.EventHubs.** Zaleca się [migrację](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/MigrationGuide.md) kodu w celu użycia najnowszego pakietu [Azure.Messaging.EventHubs.](get-started-dotnet-standard-send-v2.md)  


## <a name="event-publishers"></a>Wydawcy zdarzeń

Wysyłasz zdarzenia do centrum zdarzeń za pomocą protokołu HTTP POST lub połączenia AMQP 1.0. Wybór, którego i kiedy należy użyć i kiedy zależy od konkretnego scenariusza, którego dotyczy. Połączenia protokołu AMQP 1.0 są mierzone jako połączenia obsługiwane przez brokera w Service Bus i są bardziej odpowiednie w scenariuszach z częstymi większymi ilościami wiadomości oraz wymaganiami dotyczącymi krótszych opóźnień, ponieważ zapewniają trwały kanał obsługi komunikatów.

W przypadku używania zarządzanych interfejsów API platformy .NET głównymi konstrukcjami na potrzeby publikowania danych w usłudze Event Hubs są klasy [EventHubClient][] i [EventData][]. [EventHubClient][] udostępnia kanał komunikacji USŁUGI AMQP, za który zdarzenia są wysyłane do centrum zdarzeń. Klasa [EventData][] reprezentuje zdarzenie i służy do publikowania wiadomości w centrum zdarzeń. Ta klasa zawiera treść, niektóre metadane(Właściwości) i informacje nagłówka(Właściwości systemu) o zdarzeniu. Inne właściwości są dodawane do [obiektu EventData,][] jak przechodzi przez centrum zdarzeń.

## <a name="get-started"></a>Wprowadzenie
Klasy .NET obsługujące centra zdarzeń znajdują się w pakiecie [Microsoft.Azure.EventHubs](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) NuGet. Można zainstalować przy użyciu Eksploratora rozwiązania programu Visual Studio lub [konsoli Menedżera pakietów](https://docs.nuget.org/docs/start-here/using-the-package-manager-console) w programie Visual Studio. Aby to zrobić, należy wydać następujące polecenie w oknie [konsoli menedżera pakietów](https://docs.nuget.org/docs/start-here/using-the-package-manager-console):

```shell
Install-Package Microsoft.Azure.EventHubs
```

## <a name="create-an-event-hub"></a>Tworzenie centrum zdarzeń

Do tworzenia centrów zdarzeń można użyć witryny Azure Portal, Azure PowerShell lub Azure CLI. Aby uzyskać szczegółowe informacje, zobacz [Tworzenie obszaru nazw centrów zdarzeń i centrum zdarzeń przy użyciu portalu Azure.](event-hubs-create.md)

## <a name="create-an-event-hubs-client"></a>Tworzenie klienta usługi Event Hubs

Podstawową klasą do interakcji z centrami zdarzeń jest [Microsoft.Azure.EventHubs.EventHubClient][EventHubClient]. Tę klasę można utworzyć wystąpienia przy użyciu metody [CreateFromConnectionString,](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.createfromconnectionstring) jak pokazano w poniższym przykładzie:

```csharp
private const string EventHubConnectionString = "Event Hubs namespace connection string";
private const string EventHubName = "event hub name";

var connectionStringBuilder = new EventHubsConnectionStringBuilder(EventHubConnectionString)
{
    EntityPath = EventHubName

};
eventHubClient = EventHubClient.CreateFromConnectionString(connectionStringBuilder.ToString());
```

## <a name="send-events-to-an-event-hub"></a>Wysyłanie zdarzeń do centrum zdarzeń

Wysyłasz zdarzenia do centrum zdarzeń, tworząc wystąpienie [EventHubClient][] i wysyłając je asynchronicznie za pomocą metody [SendAsync.](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.sendasync) Ta metoda przyjmuje pojedynczy parametr wystąpienia [EventData][] i asynchronicznie wysyła go do centrum zdarzeń.

## <a name="event-serialization"></a>Serializacja zdarzeń

Klasa [EventData][] ma [dwa przeciążone konstruktory,](/dotnet/api/microsoft.azure.eventhubs.eventdata.-ctor) które przyjmują różne parametry, bajty lub tablicę bajtów, które reprezentują ładunek danych zdarzenia. W przypadku używania formatu JSON z klasą [EventData][] można użyć funkcji **Encoding.UTF8.GetBytes()** do pobrania tablicy bajtowej dla ciągu zakodowanego w formacie JSON. Przykład:

```csharp
for (var i = 0; i < numMessagesToSend; i++)
{
    var message = $"Message {i}";
    Console.WriteLine($"Sending message: {message}");
    await eventHubClient.SendAsync(new EventData(Encoding.UTF8.GetBytes(message)));
}
```

## <a name="partition-key"></a>Klucz partycji

> [!NOTE]
> Jeśli nie znasz partycji, zobacz [ten artykuł](event-hubs-features.md#partitions). 

Podczas wysyłania danych zdarzeń można określić wartość, która jest mieszana do tworzenia przypisania partycji. Określ partycję przy użyciu właściwości [PartitionSender.PartitionID.](/dotnet/api/microsoft.azure.eventhubs.partitionsender.partitionid) Jednak decyzja o użyciu partycji oznacza wybór między dostępnością i spójności. 

### <a name="availability-considerations"></a>Zagadnienia dotyczące dostępności

Za pomocą klucza partycji jest opcjonalne i należy dokładnie rozważyć, czy go używać. Jeśli nie określisz klucza partycji podczas publikowania zdarzenia, używane jest przypisanie działania okrężnego. W wielu przypadkach przy użyciu klucza partycji jest dobrym wyborem, jeśli kolejność zdarzeń jest ważne. Podczas korzystania z klucza partycji te partycje wymagają dostępności w jednym węźle, a awarie mogą wystąpić w czasie; na przykład, gdy węzły obliczeniowe ponownie uruchomić i poprawki. W związku z tym jeśli ustawisz identyfikator partycji i ta partycja stanie się niedostępna z jakiegoś powodu, próba uzyskania dostępu do danych w tej partycji zakończy się niepowodzeniem. Jeśli wysoka dostępność jest najważniejsza, nie należy określać klucz partycji; w takim przypadku zdarzenia są wysyłane do partycji przy użyciu modelu okrężnego opisanego wcześniej. W tym scenariuszu dokonujesz jawnego wyboru między dostępnością (bez identyfikatora partycji) a spójnością (przypinanie zdarzeń do identyfikatora partycji).

Inną kwestią jest obsługa opóźnień w przetwarzaniu zdarzeń. W niektórych przypadkach może być lepiej, aby usunąć dane i ponowić próbę niż spróbować nadążyć za przetwarzaniem, co może potencjalnie spowodować dalsze opóźnienia przetwarzania. Na przykład w przypadku giełdowego znacznika lepiej poczekać na pełne aktualne dane, ale w przypadku czatu na żywo lub voip wolisz szybko mieć dane, nawet jeśli nie są kompletne.

Biorąc pod uwagę te zagadnienia dotyczące dostępności, w tych scenariuszach można wybrać jedną z następujących strategii obsługi błędów:

- Zatrzymaj (przestań czytać z Centrów zdarzeń, dopóki rzeczy nie zostaną naprawione)
- Upuść (wiadomości nie są ważne, upuść je)
- Ponów próbę (ponów próbę wiadomości według własnego uznania)

Aby uzyskać więcej informacji i dyskusję na temat kompromisów między dostępnością a spójnością, zobacz [Dostępność i spójność w Centrach zdarzeń](event-hubs-availability-and-consistency.md). 

## <a name="batch-event-send-operations"></a>Operacje wysyłania partii zdarzeń

Wysyłanie zdarzeń w partiach może pomóc zwiększyć przepływność. Za pomocą interfejsu API [CreateBatch](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.createbatch) można utworzyć partię, do której można później dodać obiekty danych dla wywołania [SendAsync.](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.sendasync)

Pojedyncza partia nie może przekraczać limitu 1 MB zdarzenia. Ponadto każdy komunikat w partii używa tej samej tożsamości wydawcy. Nadawca jest odpowiedzialny za upewnienie się, że partia nie przekracza maksymalnego rozmiaru zdarzenia. Jeśli go przekroczy, zostanie wygenerowany błąd metody **Send** klienta. Można użyć metody pomocniczej [EventHubClient.CreateBatch,](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.createbatch) aby upewnić się, że partia nie przekracza 1 MB. Otrzymasz pusty [EventDataBatch](/dotnet/api/microsoft.azure.eventhubs.eventdatabatch) z Interfejsu API [CreateBatch,](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.createbatch) a następnie użyj [TryAdd,](/dotnet/api/microsoft.azure.eventhubs.eventdatabatch.tryadd) aby dodać zdarzenia do konstruowania partii. 

## <a name="send-asynchronously-and-send-at-scale"></a>Wysyłanie asynchroniczne i wysyłanie na dużą skalę

Wysyłasz zdarzenia do centrum zdarzeń asynchronicznie. Wysyłanie asynchronicznie zwiększa szybkość, z jaką klient jest w stanie wysyłać zdarzenia. [Polecenie Wyślijsync](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.sendasync) zwraca [obiekt Zadanie.](https://msdn.microsoft.com/library/system.threading.tasks.task.aspx) Można użyć [RetryPolicy](/dotnet/api/microsoft.servicebus.retrypolicy) klasy na kliencie do kontrolowania opcji ponawiania próby klienta.

## <a name="event-consumers"></a>Odbiorcy zdarzeń
Klasa [EventProcessorHost][] przetwarza dane z usługi Event Hubs. Podczas tworzenia czytników zdarzeń na platformie .NET należy używać tej implementacji. Klasa [EventProcessorHost][] udostępnia bezpieczne wątkowo, wieloprocesowe, bezpieczne środowisko uruchomieniowe dla implementacji procesora zdarzeń, które umożliwia także tworzenie punktów kontrolnych i zarządzanie dzierżawą partycji.

Aby używać klasy [EventProcessorHost][], można zaimplementować interfejs [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor). Ten interfejs zawiera cztery metody:

* [OpenAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.openasync)
* [CloseAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.closeasync)
* [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync)
* [ProcessErrorAsync (ProcesErrorAsync)](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processerrorasync)

Aby rozpocząć przetwarzanie zdarzeń, wystąpienia [EventProcessorHost][], podając odpowiednie parametry dla centrum zdarzeń. Przykład:

> [!NOTE]
> EventProcessorHost i jego powiązane klasy są dostępne w pakiecie **Microsoft.Azure.EventHubs.Processor.** Dodaj pakiet do projektu programu Visual Studio, postępując zgodnie z instrukcjami w tym`Install-Package Microsoft.Azure.EventHubs.Processor` [artykule](event-hubs-dotnet-framework-getstarted-send.md#add-the-event-hubs-nuget-package) lub wydając następujące polecenie w oknie Konsoli Menedżera [pakietów:](https://docs.nuget.org/docs/start-here/using-the-package-manager-console) .

```csharp
var eventProcessorHost = new EventProcessorHost(
        EventHubName,
        PartitionReceiver.DefaultConsumerGroupName,
        EventHubConnectionString,
        StorageConnectionString,
        StorageContainerName);
```

Następnie zadzwoń [RegisterEventProcessorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.registereventprocessorasync) zarejestrować implementację [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) w czasie wykonywania:

```csharp
await eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>();
```

W tym momencie host próbuje uzyskać dzierżawy na każdej partycji w centrum zdarzeń przy użyciu algorytmu "chciwy". Dzierżawy te trwają przez dany okres, a następnie muszą zostać odnowione. W miarę jak nowe węzły, w tym przypadku wystąpienia procesów roboczych, przechodzą w tryb online, umieszczają rezerwacje dzierżawy i z czasem obciążenie przesuwa się między węzłami, ponieważ każdy próbuje uzyskać więcej dzierżaw.

![Host procesora zdarzeń](./media/event-hubs-programming-guide/IC759863.png)

W miarę upływu czasu zostaje ustalona równowaga. Ta dynamiczna funkcja umożliwia zastosowanie skalowania automatycznego na podstawie procesora CPU do odbiorców, zarówno w celu skalowania w górę, jak i w dół. Ponieważ centra zdarzeń nie ma bezpośredniej koncepcji liczby komunikatów, średnie wykorzystanie procesora CPU jest często najlepszym mechanizmem do pomiaru wstecznej lub skali konsumenta. Jeśli wydawcy zaczną publikować więcej zdarzeń, niż odbiorcy mogą przetworzyć, zwiększenie użycia procesora CPU przez odbiorców może służyć do powodowania automatycznego skalowania liczby wystąpień procesu roboczego.

Klasa [EventProcessorHost][] implementuje również mechanizm tworzenia punktów kontrolnych oparty na usłudze Azure Storage. Ten mechanizm przechowuje przesunięcie na podstawie partycji, dzięki czemu każdy odbiorca może określić, jaki był ostatni punkt kontrolny od poprzedniego odbiorcy. Ponieważ partycje przechodzą między węzłami za pośrednictwem dzierżaw, jest to mechanizm synchronizacji, który ułatwia przesunięcie obciążenia.

## <a name="publisher-revocation"></a>Odwołanie wydawcy

Oprócz zaawansowanych funkcji w czasie wykonywania hosta procesora zdarzeń usługa Centrum zdarzeń umożliwia [odwoływanie wydawców](/rest/api/eventhub/revoke-publisher) w celu zablokowania określonym wydawcom wysyłania zdarzenia do centrum zdarzeń. Te funkcje są przydatne, jeśli token wydawcy został naruszony lub aktualizacja oprogramowania powoduje, że zachowują się niewłaściwie. W takich sytuacjach dla tożsamości wydawcy, która jest częścią jego tokenu sygnatury dostępu współdzielonego, można zablokować dostęp do publikowania zdarzeń.

> [!NOTE]
> Obecnie tylko interfejs API REST obsługuje tę funkcję[(odwołanie wydawcy).](/rest/api/eventhub/revoke-publisher)

Aby uzyskać więcej informacji o odwołaniu wydawcy i sposobie wysyłania zdarzeń do usługi Event Hubs jako wydawca, zobacz przykład [Event Hubs Large Scale Secure Publishing](https://code.msdn.microsoft.com/Service-Bus-Event-Hub-99ce67ab) (Bezpieczne publikowanie na dużą skalę w usłudze Event Hubs).

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o scenariuszach usługi Event Hubs, skorzystaj z następujących linków:

* [Omówienie interfejsu API usługi Event Hubs](event-hubs-api-overview.md)
* [Co to są Centra zdarzeń](event-hubs-what-is-event-hubs.md)
* [Availability and consistency in Event Hubs](event-hubs-availability-and-consistency.md) (Dostępność i spójność w usłudze Event Hubs)
* [Dokumentacja interfejsu API hosta procesora zdarzeń](/dotnet/api/microsoft.servicebus.messaging.eventprocessorhost)

[NamespaceManager]: /dotnet/api/microsoft.servicebus.namespacemanager
[EventHubClient]: /dotnet/api/microsoft.azure.eventhubs.eventhubclient
[Eventdata]: /dotnet/api/microsoft.azure.eventhubs.eventdata
[CreateEventHubIfNotExists]: /dotnet/api/microsoft.servicebus.namespacemanager.createeventhubifnotexists
[PartitionKey]: /dotnet/api/microsoft.servicebus.messaging.eventdata#Microsoft_ServiceBus_Messaging_EventData_PartitionKey
[EventProcessorHost]: /dotnet/api/microsoft.azure.eventhubs.processor
