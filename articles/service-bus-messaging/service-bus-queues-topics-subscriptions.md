---
title: Omówienie obsługi komunikatów kolejek, tematów i subskrypcji usługi Azure Service Bus | Dokumentacja firmy Microsoft
description: Przegląd jednostki do obsługi komunikatów usługi Service Bus.
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: a306ced4-74e9-47c6-990a-d9c47efa31d5
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/07/2017
ms.author: sethm
ms.openlocfilehash: 5bea3b56cea81362b25e696a672bf2a00e26d3ef
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2017
ms.locfileid: "24029511"
---
# <a name="service-bus-queues-topics-and-subscriptions"></a>Kolejki, tematy i subskrypcje usługi Service Bus

Microsoft Azure Service Bus obsługuje zestaw technologii chmurowych, zorientowany oprogramowanie pośredniczące, takich jak usługi kolejkowania komunikatów niezawodnej i trwałe publikowania/subskrybowania komunikatów. Te możliwości obsługi komunikatów "obsługiwanych przez brokera" można traktować jako całkowicie niezależna funkcji obsługi komunikatów, które publikacji / subskrypcji, oddzielenia czasowego i scenariuszy przy użyciu obciążenie obsługi komunikatów usługi Service Bus równoważenia obciążenia. Komunikacja oddzielona ma wiele zalet, na przykład klienci i serwery mogą nawiązywać połączenie zgodnie z potrzebami i wykonywać ich operacje w sposób asynchroniczny.

Jednostek obsługi komunikatów, które tworzą podstawowe możliwości obsługi komunikatów w usłudze Service Bus są kolejki, tematy i subskrypcje i reguły/akcji.

## <a name="queues"></a>Kolejki

Kolejki oferują *pierwszy na wejściu — pierwszy limit* dostarczanie komunikatów (FIFO) do co najmniej jeden konkurujących konsumentów. Oznacza to, że komunikaty zwykle powinny były odbierane i przetwarzane przez odbiorców w kolejności, w którym zostały one dodane do kolejki, a każdy komunikat jest odbierany i przetwarzany przez tylko jednego odbiorcę komunikatów. Najważniejszą korzyścią z używania kolejek jest osiągnięcie "oddzielenia czasowego" składników aplikacji. Innymi słowy producenci (nadawcy) i konsumenci (odbiorcy) nie ma wysyłać i odbierać komunikatów w tym samym czasie, ponieważ komunikaty są trwale przechowywane w kolejce. Ponadto producent nie ma czekać na odpowiedź od konsumenta, aby kontynuować przetwarzanie i wysyłanie komunikatów.

Pokrewną korzyścią jest "Wyrównywanie obciążenia," które umożliwia producentom i odbiorcom wysyłanie i odbieranie wiadomości w różnym tempie. W wielu aplikacjach obciążenie systemu różni się wraz z upływem czasu; Jednak gdy czas przetwarzania wymagany dla każdej jednostki pracy jest zwykle stały. Pośredniczenie producentami a konsumentami komunikatów z kolejki oznacza, że aplikacja odbierająca komunikaty tylko ma być przygotowana do być w stanie obsłużyć obciążenia średni zamiast obciążenia szczytowego. Głębokość kolejki rośnie i zmniejsza się w zależności od zmian obciążenia przychodzącego. To jest zapisywany bezpośrednio oszczędność pieniędzy w odniesieniu do kwoty infrastruktury wymaganej do obsługi obciążenia aplikacji. Miarę wzrostu obciążenia, można dodać więcej procesów roboczych do odczytu z kolejki. Każdy komunikat jest przetwarzany tylko przez jeden z procesów roboczych. Ponadto ta Równoważenie obciążenia oparte na ściąganiu umożliwia optymalne wykorzystanie komputerów roboczych nawet wtedy, gdy komputery procesu roboczego różnią się mocy obliczeniowej, zgodnie z ich ściągać komunikaty z ich maksymalna szybkość. Ten wzorzec jest często nazywany wzorcem "konkurujących konsumentów".

Kolejki, aby między producentami a konsumentami komunikatów zapewnia możliwość dostępu do właściwych luźne powiązanie między składnikami. Ponieważ producenci i konsumenci nie są wzajemnie świadomi, można uaktualnić konsumenta bez wpływu na producenta.

Tworzenie kolejki jest procesem wieloetapowych. Operacje zarządzania jednostki (kolejki i tematy) do obsługi komunikatów usługi Service Bus można wykonywać za pomocą [Microsoft.ServiceBus.NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager#microsoft_servicebus_namespacemanager) klasy, która jest tworzony przez podanie adresu podstawowego przestrzeni nazw usługi Service Bus oraz poświadczenia użytkownika. [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager#microsoft_servicebus_namespacemanager) udostępnia metody służące do tworzenia, wyliczania i usuwania jednostek obsługi komunikatów. Po utworzeniu [Microsoft.ServiceBus.TokenProvider](/dotnet/api/microsoft.servicebus.tokenprovider#microsoft_servicebus_tokenprovider) obiektu obiektu z nazwą SAS i kluczy i zarządzania przestrzeni nazw usługi, można użyć [Microsoft.ServiceBus.NamespaceManager.CreateQueue](/dotnet/api/microsoft.servicebus.namespacemanager#Microsoft_ServiceBus_NamespaceManager_CreateQueue_System_String_) metodę w celu utworzenia kolejki. Na przykład:

```csharp
// Create management credentials
TokenProvider credentials = TokenProvider.CreateSharedAccessSignatureTokenProvider(sasKeyName,sasKeyValue);
// Create namespace client
NamespaceManager namespaceClient = new NamespaceManager(ServiceBusEnvironment.CreateServiceUri("sb", ServiceNamespace, string.Empty), credentials);
```

Następnie można utworzyć obiektu typu kolejka i fabryki obsługi komunikatów o identyfikatorze URI magistrali usługi jako argument. Na przykład:

```csharp
QueueDescription myQueue;
myQueue = namespaceClient.CreateQueue("TestQueue");
MessagingFactory factory = MessagingFactory.Create(ServiceBusEnvironment.CreateServiceUri("sb", ServiceNamespace, string.Empty), credentials); 
QueueClient myQueueClient = factory.CreateQueueClient("TestQueue");
```

Następnie możesz wysłać wiadomości do kolejki. Na przykład, jeśli masz listę obsługiwanych przez brokera komunikatów o nazwie `MessageList`, kod jest podobny do poniższego przykładu:

```csharp
for (int count = 0; count < 6; count++)
{
    var issue = MessageList[count];
    issue.Label = issue.Properties["IssueTitle"].ToString();
    myQueueClient.Send(issue);
}
```

Następnie komunikaty z kolejki w następujący sposób:

```csharp
while ((message = myQueueClient.Receive(new TimeSpan(hours: 0, minutes: 0, seconds: 5))) != null)
    {
        Console.WriteLine(string.Format("Message received: {0}, {1}, {2}", message.SequenceNumber, message.Label, message.MessageId));
        message.Complete();

        Console.WriteLine("Processing message (sleeping...)");
        Thread.Sleep(1000);
    }
```

W [ReceiveAndDelete](/dotnet/api/microsoft.servicebus.messaging.receivemode) trybie operacji odbierania pojedynczego zrzutu; oznacza to, kiedy usługa Service Bus odbiera żądanie, oznacza komunikat jako wykorzystany i zwraca go do aplikacji. **ReceiveAndDelete** tryb jest najprostszym modelem i działa najlepiej w scenariuszach, w których aplikacja może tolerować nieprzetworzenie komunikatu w razie awarii. Aby to zrozumieć, rozważmy scenariusz, w którym konsument wystawia żądanie odbioru, a następnie ulega awarii przed jego przetworzeniem. Ponieważ Usługa Service Bus oznacza komunikat jako wykorzystany, gdy aplikacja ponownego uruchomienia i rozpocznie korzystanie z komunikatów ponownie, pominie utracony komunikat, który został wykorzystany przed awarią.

W [PeekLock](/dotnet/api/microsoft.servicebus.messaging.receivemode) trybie operacji odbierania staje się dwuetapowa, który umożliwia obsługę aplikacji, które nie tolerują brakujących komunikatów. Gdy Usługa Service Bus odbiera żądanie, znajduje następny komunikat do wykorzystania, blokuje go, aby uniemożliwić innym klientom odebrania go i zwraca go do aplikacji. Kiedy aplikacja zakończy przetwarzanie komunikatu (lub niezawodnie zapisze go w celu przyszłego przetwarzania), wykonuje drugi etap procesu odbierania przez wywołanie metody [Complete](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Complete) dla odebranego komunikatu. Kiedy Usługa Service Bus widzi **Complete** wywołanie, oznacza komunikat jako wykorzystany.

Jeśli aplikacja nie może przetworzyć komunikatu z jakiegoś powodu, może wywołać [Abandon](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Abandon) metody dla odebranego komunikatu (zamiast [Complete](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Complete)). Dzięki temu usługa Service Bus odblokować wiadomości i udostępnienie go do ponownego odbioru, przez tego samego użytkownika lub innego konkurujących konsumentów. Po drugie, istnieje limit czasu skojarzony z blokadą i jeśli aplikacja nie może przetworzyć komunikatu przed blokady upłynięciem limitu czasu (na przykład jeśli wystąpiła awaria aplikacji), a następnie usługi Service Bus odblokowuje komunikat i udostępnia go do ponownego odbioru (zasadniczo wykonywania [Abandon](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Abandon) operacji domyślnie).

Należy pamiętać, że w przypadku, gdy aplikacja przestaje działać po przetworzeniu komunikatu, ale przed wysłaniem **Complete** żądania, wiadomość jest dostarczony do aplikacji po jej ponownym uruchomieniu. Jest to często nazywane *co najmniej raz* przetwarzania; oznacza to, że każdy komunikat jest przetwarzany co najmniej raz. Jednak w pewnych sytuacjach ten sam komunikat może być dostarczony ponownie. Jeśli scenariusz nie Toleruje zduplikowane przetwarzania, a następnie dodatkową logikę jest wymagany w aplikacji na wykrywanie duplikatów, który można uzyskać na podstawie **MessageId** właściwości wiadomości, która pozostaje stała między kolejnymi próbami dostarczenia. Jest to nazywane *dokładnie raz* przetwarzania.

## <a name="topics-and-subscriptions"></a>Tematy i subskrypcje
W przeciwieństwie do kolejek, w których każdy komunikat jest przetwarzany przez jednego konsumenta, *tematy* i *subskrypcje* Podaj jeden do wielu formę komunikacji w *publikowania/subskrypcji* wzorca. Przydatne w przypadku skalowania do bardzo dużej liczby adresatów, każdy opublikowany komunikat jest udostępniana do każdej subskrypcji zarejestrowanej w temacie. Komunikaty są wysyłane do tematu i dostarczane do przynajmniej jednej subskrypcji skojarzone, w zależności od reguły filtrowania, które można ustawić na podstawie na subskrypcję. Subskrypcje użyć dodatkowych filtrów do ograniczenia wiadomości, które mają otrzymywać. Komunikaty są wysyłane do tematu w taki sam sposób jak są wysyłane do kolejki, ale nie odbiera wiadomości z tematu bezpośrednio. Zamiast tego są odbierane z subskrypcji. Subskrypcja tematu przypomina wirtualną kolejkę, która odbiera kopie komunikatów, które są wysyłane do tematu. Komunikaty są odbierane z subskrypcji, tak samo jak, które zostały odebrane z kolejki.

Porównania funkcji wysyłanie komunikatu z kolejki mapuje bezpośrednio na temat i jego działanie odbieranie komunikatu mapy do subskrypcji. Między innymi, oznacza to, że subskrypcje obsługuje te same wzory opisany wcześniej w tej sekcji, w odniesieniu do kolejki: konkurujących konsumentów, oddzielenia czasowego wyrównywanie obciążenia i równoważenia obciążenia.

Tworzenie tematu przypomina tworzenia kolejki, jak pokazano w przykładzie w poprzedniej sekcji. Utwórz identyfikator URI usługi, a następnie użyj [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) klasy w celu utworzenia klienta przestrzeni nazw. Następnie można utworzyć przy użyciu tematu [CreateTopic](/dotnet/api/microsoft.servicebus.namespacemanager#Microsoft_ServiceBus_NamespaceManager_CreateTopic_System_String_) metody. Na przykład:

```csharp
TopicDescription dataCollectionTopic = namespaceClient.CreateTopic("DataCollectionTopic");
```

Następnie dodaj subskrypcji, zgodnie z wymaganiami:

```csharp
SubscriptionDescription myAgentSubscription = namespaceClient.CreateSubscription(myTopic.Path, "Inventory");
SubscriptionDescription myAuditSubscription = namespaceClient.CreateSubscription(myTopic.Path, "Dashboard");
```

Następnie można utworzyć klienta tematu. Na przykład:

```csharp
MessagingFactory factory = MessagingFactory.Create(serviceUri, tokenProvider);
TopicClient myTopicClient = factory.CreateTopicClient(myTopic.Path)
```

Przy użyciu nadawcy wiadomości, można wysyłać i odbierać komunikaty z tematu, jak pokazano w poprzedniej sekcji. Na przykład:

```csharp
foreach (BrokeredMessage message in messageList)
{
    myTopicClient.Send(message);
    Console.WriteLine(
    string.Format("Message sent: Id = {0}, Body = {1}", message.MessageId, message.GetBody<string>()));
}
```

Podobnie jak kolejki, komunikaty są odbierane z subskrypcją za pomocą [SubscriptionClient](/dotnet/api/microsoft.servicebus.messaging.subscriptionclient) obiekt zamiast [QueueClient](/dotnet/api/microsoft.servicebus.messaging.queueclient) obiektu. Tworzenie klienta subskrypcji, przekazywanie nazwę tematu, nazwę subskrypcji i (opcjonalnie) tryb odbierania jako parametrów. Na przykład z **spisu** subskrypcji:

```csharp
// Create the subscription client
MessagingFactory factory = MessagingFactory.Create(serviceUri, tokenProvider); 

SubscriptionClient agentSubscriptionClient = factory.CreateSubscriptionClient("IssueTrackingTopic", "Inventory", ReceiveMode.PeekLock);
SubscriptionClient auditSubscriptionClient = factory.CreateSubscriptionClient("IssueTrackingTopic", "Dashboard", ReceiveMode.ReceiveAndDelete); 

while ((message = agentSubscriptionClient.Receive(TimeSpan.FromSeconds(5))) != null)
{
    Console.WriteLine("\nReceiving message from Inventory...");
    Console.WriteLine(string.Format("Message received: Id = {0}, Body = {1}", message.MessageId, message.GetBody<string>()));
    message.Complete();
}          

// Create a receiver using ReceiveAndDelete mode
while ((message = auditSubscriptionClient.Receive(TimeSpan.FromSeconds(5))) != null)
{
    Console.WriteLine("\nReceiving message from Dashboard...");
    Console.WriteLine(string.Format("Message received: Id = {0}, Body = {1}", message.MessageId, message.GetBody<string>()));
}
```

### <a name="rules-and-actions"></a>Reguły i akcje
W wielu scenariuszach wiadomości, które mają określone parametry muszą być przetwarzane na różne sposoby. Aby włączyć tę opcję, można skonfigurować subskrypcje, aby znaleźć komunikaty, które ma potrzebne właściwości, a następnie wykonaj pewne zmiany do tych właściwości. Natomiast subskrypcje usługi Service Bus, zobacz wszystkich wiadomości wysłanych do tematu, można kopiować tylko podzbiór tych wiadomości do kolejki subskrypcji wirtualnego. Jest to realizowane przy użyciu filtrów subskrypcji. Takie zmiany są nazywane *akcje filtru*. Po utworzeniu subskrypcji można podać wyrażenie filtru, który działa we właściwościach komunikatu właściwości systemu (na przykład **etykiety**) i właściwości niestandardowych aplikacji (na przykład  **StoreName**.) Wyrażenie filtru SQL jest opcjonalne w takim przypadku; bez wyrażenie filtru SQL żadnych działań filtru zdefiniowane w przypadku subskrypcji zostaną wykonane dla wszystkich wiadomości dla tej subskrypcji.

W poprzednim przykładzie, by przefiltrować komunikaty pochodzące tylko z **Store1**, należy utworzyć subskrypcję pulpitu nawigacyjnego w następujący sposób:

```csharp
namespaceManager.CreateSubscription("IssueTrackingTopic", "Dashboard", new SqlFilter("StoreName = 'Store1'"));
```

Ten filtr subskrypcji w miejscu, tylko komunikaty, które mają `StoreName` ustawioną właściwość `Store1` są kopiowane do wirtualnej kolejki w celu `Dashboard` subskrypcji.

Aby uzyskać więcej informacji o wartości filtru możliwości, zobacz dokumentację dla [SqlFilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter) i [SqlRuleAction](/dotnet/api/microsoft.servicebus.messaging.sqlruleaction) klasy. Zobacz też [obsługiwanych przez brokera obsługi komunikatów: filtry zaawansowane](http://code.msdn.microsoft.com/Brokered-Messaging-6b0d2749) i [filtry tematu](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/TopicFilters) próbek.

## <a name="next-steps"></a>Następne kroki
Zobacz poniższe tematy, aby uzyskać więcej informacji i przykłady użycia komunikatów usługi Service Bus zaawansowane.

* [Omówienie obsługi komunikatów w usłudze Service Bus](service-bus-messaging-overview.md)
* [samouczek dotyczący komunikatów obsługiwanych przez brokera w usłudze Service Bus dla platformy .NET](service-bus-brokered-tutorial-dotnet.md)
* [Samouczek dotyczący REST komunikatów obsługiwanych przez brokera usługi Service Bus](service-bus-brokered-tutorial-rest.md)
* [Obsługiwanych przez brokera komunikatów: Przykładowe filtry zaawansowane](http://code.msdn.microsoft.com/Brokered-Messaging-6b0d2749)

