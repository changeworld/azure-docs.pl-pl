---
title: Podręcznik programowania — usługa Azure Event Hubs | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera informacje na temat sposobu pisanie kodu dla usługi Azure Event Hubs przy użyciu zestawu Azure .NET SDK.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
ms.service: event-hubs
ms.custom: seodec18
ms.topic: article
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 2af076153725dc91caaf07b710acf21ebc143fb0
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/20/2019
ms.locfileid: "67273664"
---
# <a name="programming-guide-for-azure-event-hubs"></a>Przewodnik programowania dotyczący usługi Azure Event Hubs
W tym artykule omówiono kilka typowych scenariuszy, w pisaniu kodu za pomocą usługi Azure Event Hubs. Przyjęto założenie, że wstępnie znasz i rozumiesz usługę Event Hubs. Omówienie koncepcji usługi Event Hubs można znaleźć w temacie [Przegląd usługi Event Hubs](event-hubs-what-is-event-hubs.md).

## <a name="event-publishers"></a>Wydawcy zdarzeń

Wysyła zdarzenia do Centrum zdarzeń za pomocą metody POST protokołu HTTP lub przy użyciu połączenia protokołu AMQP 1.0. Wybór rodzaju używanej i kiedy zależy od konkretnego scenariusza. Połączenia protokołu AMQP 1.0 są mierzone jako połączenia obsługiwane przez brokera w Service Bus i są bardziej odpowiednie w scenariuszach z częstymi większymi ilościami wiadomości oraz wymaganiami dotyczącymi krótszych opóźnień, ponieważ zapewniają trwały kanał obsługi komunikatów.

W przypadku używania zarządzanych interfejsów API platformy .NET głównymi konstrukcjami na potrzeby publikowania danych w usłudze Event Hubs są klasy [EventHubClient][] i [EventData][]. [EventHubClient][] udostępnia kanał komunikacji protokołu AMQP, przez który zdarzenia są wysyłane do Centrum zdarzeń. [EventData][] klasa reprezentuje zdarzenie i jest używana do publikowania komunikatów do Centrum zdarzeń. Ta klasa zawiera treść, niektóre metadane i informacje nagłówka zdarzenia. Inne właściwości są dodawane do [EventData][] przechodzi on przez Centrum zdarzeń.

## <a name="get-started"></a>Rozpoczęcie pracy
.NET klasy, które obsługują usługi Event Hubs znajdują się w [Microsoft.Azure.EventHubs](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) pakietu NuGet. Można to zrobić za pomocą Eksploratora rozwiązania Visual Studio lub [Konsola Menedżera pakietów](https://docs.nuget.org/docs/start-here/using-the-package-manager-console) w programie Visual Studio. Aby to zrobić, należy wydać następujące polecenie w oknie [konsoli menedżera pakietów](https://docs.nuget.org/docs/start-here/using-the-package-manager-console):

```shell
Install-Package Microsoft.Azure.EventHubs
```

## <a name="create-an-event-hub"></a>Tworzenie centrum zdarzeń

Witryna Azure portal, programu Azure PowerShell lub wiersza polecenia platformy Azure służy do tworzenia usług Event Hubs. Aby uzyskać więcej informacji, zobacz [tworzenie przestrzeni nazw usługi Event Hubs i Centrum zdarzeń za pomocą witryny Azure portal](event-hubs-create.md).

## <a name="create-an-event-hubs-client"></a>Tworzenie klienta usługi Event Hubs

Klasa podstawowe do interakcji z usługą Event Hubs jest [Microsoft.Azure.EventHubs.EventHubClient][EventHubClient]. Można utworzyć wystąpienie tej klasy przy użyciu [CreateFromConnectionString](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.createfromconnectionstring) metodzie, jak pokazano w poniższym przykładzie:

```csharp
private const string EventHubConnectionString = "Event Hubs namespace connection string";
private const string EventHubName = "event hub name";

var connectionStringBuilder = new EventHubsConnectionStringBuilder(EventHubConnectionString)
{
    EntityPath = EventHubName

};
eventHubClient = EventHubClient.CreateFromConnectionString(connectionStringBuilder.ToString());
```

## <a name="send-events-to-an-event-hub"></a>Wysyłanie zdarzeń do Centrum zdarzeń

Wysyłanie zdarzeń do Centrum zdarzeń, tworząc [EventHubClient][] wystąpienie i wysłanie go asynchronicznie za pomocą [SendAsync](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.sendasync) metody. Ta metoda przyjmuje jeden [EventData][] parametr wystąpienia, a następnie asynchronicznie wysyła do Centrum zdarzeń.

## <a name="event-serialization"></a>Serializacja zdarzeń

[EventData][] klasa ma [dwie przeciążone konstruktory](/dotnet/api/microsoft.azure.eventhubs.eventdata.-ctor) , które wymagają różnych parametrów, bajtów lub tablicą bajtów reprezentujących ładunku danych zdarzenia. W przypadku używania formatu JSON z klasą [EventData][] można użyć funkcji **Encoding.UTF8.GetBytes()** do pobrania tablicy bajtowej dla ciągu zakodowanego w formacie JSON. Na przykład:

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
> Jeśli nie jesteś zaznajomiony z partycjami, zobacz [w tym artykule](event-hubs-features.md#partitions). 

Podczas wysyłania danych zdarzenia, można określić wartość, która jest przekazywane do utworzenia przypisania partycji. Możesz określić za pomocą partycji [PartitionSender.PartitionID](/dotnet/api/microsoft.azure.eventhubs.partitionsender.partitionid) właściwości. Jednak zdecydować, czy użyć partycje oznacza wybór między dostępnością a spójnością. 

### <a name="availability-considerations"></a>Zagadnienia dotyczące dostępności

Użycie klucza partycji jest opcjonalna i należy rozważyć, czy do korzystania z jednego. Jeśli nie określisz klucza partycji podczas publikowania zdarzenia, używane jest przypisanie działania okrężnego. W wielu przypadkach przy użyciu klucza partycji jest dobrym rozwiązaniem, jeśli zdarzenie kolejność jest ważna. Użycie klucza partycji, partycje te wymagają dostępności w jednym węźle, a awarii wraz z upływem czasu; na przykład, gdy obliczeń ponownego uruchomienia węzłów i patch. W efekcie Jeśli ustawisz identyfikator partycji: partycji stanie się niedostępna z powodu, próba uzyskania dostępu do danych w tej partycji zakończy się niepowodzeniem. Jeśli wysokiej dostępności jest najważniejsza, nie należy określać klucz partycji. w takim przypadku zdarzenia są wysyłane na partycje przy użyciu modelu działania okrężnego opisanych powyżej. W tym scenariuszu w przypadku wprowadzania jawną wyboru między dostępnością (Brak Identyfikatora partycji) i spójności (przypinanie zdarzenia do Identyfikatora partycji).

Inną ważną kwestią jest obsługa opóźnienia w przetwarzaniu zdarzeń. W niektórych przypadkach może być lepszym rozwiązaniem porzucić danych i ponów próbę niż próbować na bieżąco przetwarzania, co może powodować dalsze opóźnienia w przetwarzaniu podrzędnym. Na przykład za pomocą giełdowej lepiej jest oczekiwania kompletne aktualne dane, ale w Czat na żywo lub scenariusza VOIP zamiast trzeba je szybko, nawet jeśli nie zostało zakończone.

Biorąc pod uwagę następujące zagadnienia dotyczące dostępności w tych scenariuszach możesz wybrać jedną z powodu następującego błędu strategii obsługi:

- Stop (Zatrzymaj odczytu z usługi Event Hubs, dopóki elementy są stałe)
- Upuść (komunikaty nie są istotne, upuścić je)
- Ponów próbę wykonania (ponawiania prób komunikaty wedle uznania)

Aby uzyskać więcej informacji i dyskusji na temat wad i zalet między dostępnością a spójnością, zobacz [dostępność i spójność w usłudze Event Hubs](event-hubs-availability-and-consistency.md). 

## <a name="batch-event-send-operations"></a>Operacje wysyłania partii zdarzeń

Wysyłanie zdarzeń w partiach może pomóc zwiększyć przepustowość. Możesz użyć [CreateBatch](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.createbatch) interfejsu API w celu tworzenia partii do danych, które obiekty później można dodać [SendAsync](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.sendasync) wywołania.

Pojedyncza partia nie może przekraczać limit 1 MB zdarzenia. Ponadto każdy komunikat w partii używa tej samej tożsamości wydawcy. Nadawca jest odpowiedzialny za upewnienie się, że partia nie przekracza maksymalnego rozmiaru zdarzenia. Jeśli go przekroczy, zostanie wygenerowany błąd metody **Send** klienta. Można użyć metody pomocniczej [EventHubClient.CreateBatch](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.createbatch) aby upewnić się, że partia nie przekracza 1 MB. Pobierz pusty [EventDataBatch](/dotnet/api/microsoft.azure.eventhubs.eventdatabatch) z [CreateBatch](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.createbatch) interfejsu API, a następnie użycie [TryAdd](/dotnet/api/microsoft.azure.eventhubs.eventdatabatch.tryadd) próbę dodania zdarzeń do utworzenia zadania wsadowego. 

## <a name="send-asynchronously-and-send-at-scale"></a>Wysyłanie asynchroniczne i wysyłanie na dużą skalę

Asynchronicznie wysyła zdarzenia do Centrum zdarzeń. Wysyłanie asynchroniczne zwiększa szybkość jaką klient jest w stanie wysyłać zdarzenia. [SendAsync](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.sendasync) zwraca [zadań](https://msdn.microsoft.com/library/system.threading.tasks.task.aspx) obiektu. Możesz użyć [RetryPolicy](/dotnet/api/microsoft.servicebus.retrypolicy) opcje ponawiania klasy na kliencie do sterowania klienta.

## <a name="event-consumers"></a>Odbiorcy zdarzeń
Klasa [EventProcessorHost][] przetwarza dane z usługi Event Hubs. Podczas tworzenia czytników zdarzeń na platformie .NET należy używać tej implementacji. Klasa [EventProcessorHost][] udostępnia bezpieczne wątkowo, wieloprocesowe, bezpieczne środowisko uruchomieniowe dla implementacji procesora zdarzeń, które umożliwia także tworzenie punktów kontrolnych i zarządzanie dzierżawą partycji.

Aby używać klasy [EventProcessorHost][], można zaimplementować interfejs [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor). Ten interfejs zawiera cztery metody:

* [OpenAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.openasync)
* [CloseAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.closeasync)
* [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync)
* [ProcessErrorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processerrorasync)

Aby rozpocząć przetwarzanie zdarzeń, utworzyć [EventProcessorHost][], podając odpowiednie parametry Centrum zdarzeń. Na przykład:

> [!NOTE]
> EventProcessorHost oraz ich powiązanymi klasami znajdują się w **Microsoft.Azure.EventHubs.Processor** pakietu. Dodaj pakiet do projektu programu Visual Studio, postępując zgodnie z instrukcjami opisanymi w [w tym artykule](event-hubs-dotnet-framework-getstarted-send.md#add-the-event-hubs-nuget-package) lub wysyłając następujące polecenie w [Konsola Menedżera pakietów](https://docs.nuget.org/docs/start-here/using-the-package-manager-console) okna:`Install-Package Microsoft.Azure.EventHubs.Processor`.

```csharp
var eventProcessorHost = new EventProcessorHost(
        EventHubName,
        PartitionReceiver.DefaultConsumerGroupName,
        EventHubConnectionString,
        StorageConnectionString,
        StorageContainerName);
```

Następnie wywołaj [RegisterEventProcessorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.registereventprocessorasync) zarejestrować swoje [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) wdrożenia w środowisku uruchomieniowym:

```csharp
await eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>();
```

W tym momencie host podejmuje próby uzyskania dzierżawy na każdej partycji w Centrum zdarzeń za pomocą "zachłannego" algorytmu. Te dzierżawy trwać przez dany przedział czasu, a następnie muszą być odnowione. W miarę jak nowe węzły, w tym przypadku wystąpienia procesów roboczych, przechodzą w tryb online, umieszczają rezerwacje dzierżawy i z czasem obciążenie przesuwa się między węzłami, ponieważ każdy próbuje uzyskać więcej dzierżaw.

![Host procesora zdarzeń](./media/event-hubs-programming-guide/IC759863.png)

W miarę upływu czasu zostaje ustalona równowaga. Ta dynamiczna funkcja umożliwia zastosowanie skalowania automatycznego na podstawie procesora CPU do odbiorców, zarówno w celu skalowania w górę, jak i w dół. Ponieważ usługa Event Hubs nie obsługuje bezpośredniej koncepcji liczby komunikatów, średnie wykorzystanie procesora CPU jest często najlepszym mechanizmem mierzenia skali zaplecza lub odbiorcy z tyłu. Jeśli wydawcy zaczną publikować więcej zdarzeń, niż odbiorcy mogą przetworzyć, zwiększenie użycia procesora CPU przez odbiorców może służyć do powodowania automatycznego skalowania liczby wystąpień procesu roboczego.

Klasa [EventProcessorHost][] implementuje również mechanizm tworzenia punktów kontrolnych oparty na usłudze Azure Storage. Ten mechanizm przechowuje przesunięcie na podstawie partycji, dzięki czemu każdy odbiorca może określić, jaki był ostatni punkt kontrolny od poprzedniego odbiorcy. Ponieważ partycje przechodzą między węzłami za pośrednictwem dzierżaw, jest to mechanizm synchronizacji, który ułatwia przesunięcie obciążenia.

## <a name="publisher-revocation"></a>Odwołanie wydawcy

Oprócz zaawansowanych funkcji środowiska wykonawczego [EventProcessorHost][], usługa Event Hubs umożliwia odwołanie wydawcy w celu zablokowania określonym wydawcom możliwości wysyłania zdarzeń do Centrum zdarzeń. Funkcje te są przydatne, jeśli naruszono bezpieczeństwo tokenu wydawcy lub aktualizacja oprogramowania powoduje nieuprawnione zachowanie. W takich sytuacjach dla tożsamości wydawcy, która jest częścią jego tokenu sygnatury dostępu współdzielonego, można zablokować dostęp do publikowania zdarzeń.

Aby uzyskać więcej informacji o odwołaniu wydawcy i sposobie wysyłania zdarzeń do usługi Event Hubs jako wydawca, zobacz przykład [Event Hubs Large Scale Secure Publishing](https://code.msdn.microsoft.com/Service-Bus-Event-Hub-99ce67ab) (Bezpieczne publikowanie na dużą skalę w usłudze Event Hubs).

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej o scenariuszach usługi Event Hubs, skorzystaj z następujących linków:

* [Omówienie interfejsu API usługi Event Hubs](event-hubs-api-overview.md)
* [Co to jest usługa Event Hubs](event-hubs-what-is-event-hubs.md)
* [Availability and consistency in Event Hubs](event-hubs-availability-and-consistency.md) (Dostępność i spójność w usłudze Event Hubs)
* [Dokumentacja interfejsu API hosta procesora zdarzeń](/dotnet/api/microsoft.servicebus.messaging.eventprocessorhost)

[NamespaceManager]: /dotnet/api/microsoft.servicebus.namespacemanager
[EventHubClient]: /dotnet/api/microsoft.azure.eventhubs.eventhubclient
[EventData]: /dotnet/api/microsoft.azure.eventhubs.eventdata
[CreateEventHubIfNotExists]: /dotnet/api/microsoft.servicebus.namespacemanager.createeventhubifnotexists
[PartitionKey]: /dotnet/api/microsoft.servicebus.messaging.eventdata#Microsoft_ServiceBus_Messaging_EventData_PartitionKey
[EventProcessorHost]: /dotnet/api/microsoft.azure.eventhubs.processor
