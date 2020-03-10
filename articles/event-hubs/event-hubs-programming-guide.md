---
title: Przewodnik programowania .NET — Azure Event Hubs (starsza wersja) | Microsoft Docs
description: Ten artykuł zawiera informacje na temat sposobu pisanie kodu dla usługi Azure Event Hubs przy użyciu zestawu Azure .NET SDK.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
ms.service: event-hubs
ms.custom: seodec18
ms.topic: article
ms.date: 01/15/2020
ms.author: shvija
ms.openlocfilehash: d958c2d32c16874676f46bb216067fe2d7bbe784
ms.sourcegitcommit: e6bce4b30486cb19a6b415e8b8442dd688ad4f92
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/09/2020
ms.locfileid: "78945538"
---
# <a name="net-programming-guide-for-azure-event-hubs-legacy-microsoftazureeventhubs-package"></a>Przewodnik programowania .NET dla platformy Azure Event Hubs (starszy pakiet Microsoft. Azure. EventHubs)
W tym artykule omówiono kilka typowych scenariuszy, w pisaniu kodu za pomocą usługi Azure Event Hubs. Przyjęto założenie, że wstępnie znasz i rozumiesz usługę Event Hubs. Omówienie koncepcji usługi Event Hubs można znaleźć w temacie [Przegląd usługi Event Hubs](event-hubs-what-is-event-hubs.md).

> [!WARNING]
> Ten przewodnik jest przeznaczony dla starego pakietu **Microsoft. Azure. EventHubs** . Zalecamy [Migrowanie](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/MigrationGuide.md) kodu do korzystania z najnowszego pakietu [Azure. Messaging. EventHubs](get-started-dotnet-standard-send-v2.md) .  


## <a name="event-publishers"></a>Wydawcy zdarzeń

Wysyła zdarzenia do Centrum zdarzeń za pomocą metody POST protokołu HTTP lub przy użyciu połączenia protokołu AMQP 1.0. Wybór rodzaju używanej i kiedy zależy od konkretnego scenariusza. Połączenia protokołu AMQP 1.0 są mierzone jako połączenia obsługiwane przez brokera w Service Bus i są bardziej odpowiednie w scenariuszach z częstymi większymi ilościami wiadomości oraz wymaganiami dotyczącymi krótszych opóźnień, ponieważ zapewniają trwały kanał obsługi komunikatów.

W przypadku używania zarządzanych interfejsów API platformy .NET głównymi konstrukcjami na potrzeby publikowania danych w usłudze Event Hubs są klasy [EventHubClient][] i [EventData][]. [EventHubClient][] udostępnia kanał komunikacji AMQP, przez który zdarzenia są wysyłane do centrum zdarzeń. Klasa [EVENTDATA][] reprezentuje zdarzenie i jest używana do publikowania komunikatów w centrum zdarzeń. Ta klasa obejmuje treść, niektóre metadane (właściwości) i informacje nagłówka (SystemProperties) dotyczące zdarzenia. Inne właściwości są dodawane do obiektu [EVENTDATA][] , gdy przechodzi on przez centrum zdarzeń.

## <a name="get-started"></a>Rozpoczynanie pracy
Klasy .NET obsługujące Event Hubs są dostępne w pakiecie NuGet [Microsoft. Azure. EventHubs](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) . Program można zainstalować za pomocą Eksploratora rozwiązań programu Visual Studio lub [konsoli Menedżera pakietów](https://docs.nuget.org/docs/start-here/using-the-package-manager-console) w programie Visual Studio. Aby to zrobić, należy wydać następujące polecenie w oknie [konsoli menedżera pakietów](https://docs.nuget.org/docs/start-here/using-the-package-manager-console):

```shell
Install-Package Microsoft.Azure.EventHubs
```

## <a name="create-an-event-hub"></a>Tworzenie centrum zdarzeń

Witryna Azure portal, programu Azure PowerShell lub wiersza polecenia platformy Azure służy do tworzenia usług Event Hubs. Aby uzyskać szczegółowe informacje, zobacz [Tworzenie przestrzeni nazw Event Hubs i centrum zdarzeń przy użyciu Azure Portal](event-hubs-create.md).

## <a name="create-an-event-hubs-client"></a>Tworzenie klienta usługi Event Hubs

Podstawową klasą służącą do współdziałania z Event Hubs jest [Microsoft. Azure. EventHubs. EventHubClient][EventHubClient]. Można utworzyć wystąpienie tej klasy przy użyciu metody [CreateFromConnectionString](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.createfromconnectionstring) , jak pokazano w następującym przykładzie:

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

Zdarzenia są wysyłane do centrum zdarzeń przez utworzenie wystąpienia [EventHubClient][] i wysłanie go asynchronicznie za pomocą metody [SendAsync](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.sendasync) . Ta metoda przyjmuje jeden parametr wystąpienia [EVENTDATA][] i asynchronicznie wysyła go do centrum zdarzeń.

## <a name="event-serialization"></a>Serializacja zdarzeń

Klasa [EVENTDATA][] ma [dwa przeciążone konstruktory](/dotnet/api/microsoft.azure.eventhubs.eventdata.-ctor) , które przyjmują wiele parametrów, bajtów lub tablicę bajtową, która reprezentuje ładunek danych zdarzenia. W przypadku używania formatu JSON z klasą [EventData][] można użyć funkcji **Encoding.UTF8.GetBytes()** do pobrania tablicy bajtowej dla ciągu zakodowanego w formacie JSON. Na przykład:

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
> Jeśli nie znasz już partycji, zapoznaj się z [tym artykułem](event-hubs-features.md#partitions). 

Podczas wysyłania danych zdarzenia, można określić wartość, która jest przekazywane do utworzenia przypisania partycji. Partycję należy określić przy użyciu właściwości [PartitionSender. PartitionID](/dotnet/api/microsoft.azure.eventhubs.partitionsender.partitionid) . Jednak zdecydować, czy użyć partycje oznacza wybór między dostępnością a spójnością. 

### <a name="availability-considerations"></a>Zagadnienia dotyczące dostępności

Użycie klucza partycji jest opcjonalna i należy rozważyć, czy do korzystania z jednego. Jeśli nie określisz klucza partycji podczas publikowania zdarzenia, używane jest przypisanie działania okrężnego. W wielu przypadkach przy użyciu klucza partycji jest dobrym rozwiązaniem, jeśli zdarzenie kolejność jest ważna. Użycie klucza partycji, partycje te wymagają dostępności w jednym węźle, a awarii wraz z upływem czasu; na przykład, gdy obliczeń ponownego uruchomienia węzłów i patch. W efekcie Jeśli ustawisz identyfikator partycji: partycji stanie się niedostępna z powodu, próba uzyskania dostępu do danych w tej partycji zakończy się niepowodzeniem. Jeśli wysokiej dostępności jest najważniejsza, nie należy określać klucz partycji. w takim przypadku zdarzenia są wysyłane na partycje przy użyciu modelu działania okrężnego opisanych powyżej. W tym scenariuszu w przypadku wprowadzania jawną wyboru między dostępnością (Brak Identyfikatora partycji) i spójności (przypinanie zdarzenia do Identyfikatora partycji).

Inną ważną kwestią jest obsługa opóźnienia w przetwarzaniu zdarzeń. W niektórych przypadkach może być lepszym rozwiązaniem porzucić danych i ponów próbę niż próbować na bieżąco przetwarzania, co może powodować dalsze opóźnienia w przetwarzaniu podrzędnym. Na przykład za pomocą giełdowej lepiej jest oczekiwania kompletne aktualne dane, ale w Czat na żywo lub scenariusza VOIP zamiast trzeba je szybko, nawet jeśli nie zostało zakończone.

Biorąc pod uwagę następujące zagadnienia dotyczące dostępności w tych scenariuszach możesz wybrać jedną z powodu następującego błędu strategii obsługi:

- Stop (Zatrzymaj odczytu z usługi Event Hubs, dopóki elementy są stałe)
- Upuść (komunikaty nie są istotne, upuścić je)
- Ponów próbę wykonania (ponawiania prób komunikaty wedle uznania)

Aby uzyskać więcej informacji i zapoznać się z informacjami o różnicach w zakresie dostępności i spójności, zobacz [dostępność i spójność w Event Hubs](event-hubs-availability-and-consistency.md). 

## <a name="batch-event-send-operations"></a>Operacje wysyłania partii zdarzeń

Wysyłanie zdarzeń w partiach może pomóc zwiększyć przepustowość. Za pomocą interfejsu API tworzenia [wsadowego](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.createbatch) można utworzyć partię, do której obiekty danych można później dodać do wywołania [SendAsync](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.sendasync) .

Pojedyncza partia nie może przekroczyć limitu 1 MB zdarzenia. Ponadto każdy komunikat w partii używa tej samej tożsamości wydawcy. Nadawca jest odpowiedzialny za upewnienie się, że partia nie przekracza maksymalnego rozmiaru zdarzenia. Jeśli go przekroczy, zostanie wygenerowany błąd metody **Send** klienta. Możesz użyć metody pomocnika [EventHubClient. Getbatch](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.createbatch) , aby upewnić się, że partia nie przekracza 1 MB. Otrzymujesz pustą [EventDataBatch](/dotnet/api/microsoft.azure.eventhubs.eventdatabatch) z interfejsu API tworzenia [wsadowego](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.createbatch) , a następnie użyjemy [TryAdd](/dotnet/api/microsoft.azure.eventhubs.eventdatabatch.tryadd) , aby dodać zdarzenia w celu skonstruowania partii. 

## <a name="send-asynchronously-and-send-at-scale"></a>Wysyłanie asynchroniczne i wysyłanie na dużą skalę

Asynchronicznie wysyła zdarzenia do Centrum zdarzeń. Wysyłanie asynchroniczne zwiększa szybkość jaką klient jest w stanie wysyłać zdarzenia. [SendAsync](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.sendasync) zwraca obiekt [zadania](https://msdn.microsoft.com/library/system.threading.tasks.task.aspx) . Aby sterować opcjami ponowień klienta, można użyć klasy [RetryPolicy](/dotnet/api/microsoft.servicebus.retrypolicy) na kliencie.

## <a name="event-consumers"></a>Odbiorcy zdarzeń
Klasa [EventProcessorHost][] przetwarza dane z usługi Event Hubs. Podczas tworzenia czytników zdarzeń na platformie .NET należy używać tej implementacji. Klasa [EventProcessorHost][] udostępnia bezpieczne wątkowo, wieloprocesowe, bezpieczne środowisko uruchomieniowe dla implementacji procesora zdarzeń, które umożliwia także tworzenie punktów kontrolnych i zarządzanie dzierżawą partycji.

Aby używać klasy [EventProcessorHost][], można zaimplementować interfejs [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor). Ten interfejs zawiera cztery metody:

* [OpenAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.openasync)
* [CloseAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.closeasync)
* [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync)
* [ProcessErrorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processerrorasync)

Aby rozpocząć przetwarzanie zdarzeń, Utwórz wystąpienie [EventProcessorHost][], dostarczając odpowiednie parametry dla centrum zdarzeń. Na przykład:

> [!NOTE]
> Klasy eventprocessorhost i powiązane klasy są dostępne w pakiecie **Microsoft. Azure. EventHubs. Processor** . Dodaj pakiet do projektu programu Visual Studio, wykonując instrukcje podane w [tym artykule](event-hubs-dotnet-framework-getstarted-send.md#add-the-event-hubs-nuget-package) lub wykonując następujące polecenie w oknie [konsola menedżera pakietów](https://docs.nuget.org/docs/start-here/using-the-package-manager-console) :`Install-Package Microsoft.Azure.EventHubs.Processor`.

```csharp
var eventProcessorHost = new EventProcessorHost(
        EventHubName,
        PartitionReceiver.DefaultConsumerGroupName,
        EventHubConnectionString,
        StorageConnectionString,
        StorageContainerName);
```

Następnie zadzwoń do [RegisterEventProcessorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.registereventprocessorasync) w celu zarejestrowania implementacji [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) przy użyciu środowiska uruchomieniowego:

```csharp
await eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>();
```

W tym momencie host podejmuje próby uzyskania dzierżawy na każdej partycji w Centrum zdarzeń za pomocą "zachłannego" algorytmu. Te dzierżawy trwać przez dany przedział czasu, a następnie muszą być odnowione. W miarę jak nowe węzły, w tym przypadku wystąpienia procesów roboczych, przechodzą w tryb online, umieszczają rezerwacje dzierżawy i z czasem obciążenie przesuwa się między węzłami, ponieważ każdy próbuje uzyskać więcej dzierżaw.

![hosta procesora zdarzeń](./media/event-hubs-programming-guide/IC759863.png)

W miarę upływu czasu zostaje ustalona równowaga. Ta dynamiczna funkcja umożliwia zastosowanie skalowania automatycznego na podstawie procesora CPU do odbiorców, zarówno w celu skalowania w górę, jak i w dół. Ponieważ usługa Event Hubs nie obsługuje bezpośredniej koncepcji liczby komunikatów, średnie wykorzystanie procesora CPU jest często najlepszym mechanizmem mierzenia skali zaplecza lub odbiorcy z tyłu. Jeśli wydawcy zaczną publikować więcej zdarzeń, niż odbiorcy mogą przetworzyć, zwiększenie użycia procesora CPU przez odbiorców może służyć do powodowania automatycznego skalowania liczby wystąpień procesu roboczego.

Klasa [EventProcessorHost][] implementuje również mechanizm tworzenia punktów kontrolnych oparty na usłudze Azure Storage. Ten mechanizm przechowuje przesunięcie na podstawie partycji, dzięki czemu każdy odbiorca może określić, jaki był ostatni punkt kontrolny od poprzedniego odbiorcy. Ponieważ partycje przechodzą między węzłami za pośrednictwem dzierżaw, jest to mechanizm synchronizacji, który ułatwia przesunięcie obciążenia.

## <a name="publisher-revocation"></a>Odwołanie wydawcy

Oprócz zaawansowanych funkcji w czasie wykonywania hosta procesora zdarzeń usługa Event Hubs umożliwia [odwoływanie wydawcy](/rest/api/eventhub/revoke-publisher) w celu zablokowania wysyłania zdarzeń przez określonych wydawców do centrum zdarzeń. Funkcje te są przydatne, jeśli naruszono bezpieczeństwo tokenu wydawcy lub aktualizacja oprogramowania powoduje nieuprawnione zachowanie. W takich sytuacjach dla tożsamości wydawcy, która jest częścią jego tokenu sygnatury dostępu współdzielonego, można zablokować dostęp do publikowania zdarzeń.

> [!NOTE]
> Obecnie tylko interfejs API REST obsługuje tę funkcję ([odwołanie wydawcy](/rest/api/eventhub/revoke-publisher)).

Aby uzyskać więcej informacji o odwołaniu wydawcy i sposobie wysyłania zdarzeń do usługi Event Hubs jako wydawca, zobacz przykład [Event Hubs Large Scale Secure Publishing](https://code.msdn.microsoft.com/Service-Bus-Event-Hub-99ce67ab) (Bezpieczne publikowanie na dużą skalę w usłudze Event Hubs).

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o scenariuszach usługi Event Hubs, skorzystaj z następujących linków:

* [Przegląd interfejsu API usługi Event Hubs](event-hubs-api-overview.md)
* [Co to jest Event Hubs](event-hubs-what-is-event-hubs.md)
* [Availability and consistency in Event Hubs](event-hubs-availability-and-consistency.md) (Dostępność i spójność w usłudze Event Hubs)
* [Dokumentacja interfejsu API hosta procesora zdarzeń](/dotnet/api/microsoft.servicebus.messaging.eventprocessorhost)

[NamespaceManager]: /dotnet/api/microsoft.servicebus.namespacemanager
[EventHubClient]: /dotnet/api/microsoft.azure.eventhubs.eventhubclient
[EventData]: /dotnet/api/microsoft.azure.eventhubs.eventdata
[CreateEventHubIfNotExists]: /dotnet/api/microsoft.servicebus.namespacemanager.createeventhubifnotexists
[PartitionKey]: /dotnet/api/microsoft.servicebus.messaging.eventdata#Microsoft_ServiceBus_Messaging_EventData_PartitionKey
[EventProcessorHost]: /dotnet/api/microsoft.azure.eventhubs.processor
