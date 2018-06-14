---
title: Kolejki utraconych wiadomości usługi Service Bus | Dokumentacja firmy Microsoft
description: Omówienie usługi Azure Service Bus kolejki utraconych wiadomości
services: service-bus-messaging
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: 68b2aa38-dba7-491a-9c26-0289bc15d397
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/31/2018
ms.author: sethm
ms.openlocfilehash: a82d70e7bf776bf470d14e7f061774ccbb136316
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/01/2018
ms.locfileid: "28925666"
---
# <a name="overview-of-service-bus-dead-letter-queues"></a>Omówienie kolejki utraconych wiadomości usługi Service Bus

Azure kolejek usługi Service Bus i subskrypcje tematu zapewniają podrzędnej kolejki dodatkowej, nazywany *kolejki utraconych wiadomości* (DLQ). Kolejki utraconych wiadomości nie trzeba jawnie można utworzyć i nie może być usunięte ani w przeciwnym razie zarządzane niezależnie od głównego jednostki.

W tym artykule opisano kolejki utraconych wiadomości w usłudze Service Bus. Znacznie dyskusji zilustrowane [próbki kolejki utraconych wiadomości](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/DeadletterQueue) w witrynie GitHub.
 
## <a name="the-dead-letter-queue"></a>Kolejki utraconych wiadomości

Kolejki utraconych wiadomości służy do przechowywania wiadomości, których nie można dostarczyć żadnych odbiorcy lub wiadomości, których nie można przetworzyć. Komunikaty można następnie usuwane z DLQ i inspekcji. Aplikacji może za pomocą operatora, rozwiązanie problemów i ponowne przesłanie wiadomości, fakt, że wystąpił błąd podczas logowania i podjęcia działań naprawczych. 

Z perspektywy interfejsu API i protokół DLQ przede wszystkim jest podobny do każdej kolejki, z tą różnicą, że komunikaty może zostać przesłane tylko za pośrednictwem operacji utraconych jednostki nadrzędnej. Ponadto, time-to-live nie zostaną spełnione, a nie utraconych komunikatów z DLQ. Kolejki utraconych wiadomości w pełni obsługuje operacje transakcyjne i dostarczania peek blokady.

Należy zauważyć, że nie jest Brak automatycznego oczyszczania DLQ. Wiadomości pozostaną w DLQ, dopóki jawnie je odzyskać z DLQ i wywołanie [Complete()](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync) wiadomości utraconych.

## <a name="moving-messages-to-the-dlq"></a>Przenoszenie wiadomości do DLQ

Istnieje kilka działań w usłudze Service Bus, które komunikaty uzyskać wypychana do DLQ z wewnątrz aparat obsługi wiadomości, sam. Aplikację można również jawnie przenoszenie wiadomości do DLQ. 

Jako Broker pobiera przenieść tej wiadomości, dwie właściwości są dodawane do wiadomości, gdy brokera wywołuje swoją wersję wewnętrzny [utraconych wiadomości](/dotnet/api/microsoft.azure.servicebus.queueclient.deadletterasync) metody w komunikacie: `DeadLetterReason` i `DeadLetterErrorDescription`.

Aplikacje mogą definiować własne kody `DeadLetterReason` właściwości, ale system ustawia następujące wartości.

| Warunek | DeadLetterReason | DeadLetterErrorDescription |
| --- | --- | --- |
| Zawsze |HeaderSizeExceeded |Przekroczono przydział rozmiaru dla tego strumienia. |
| ! TopicDescription.<br />EnableFilteringMessagesBeforePublishing i SubscriptionDescription.<br />EnableDeadLetteringOnFilterEvaluationExceptions |exception.GetType().Name |exception.Message |
| EnableDeadLetteringOnMessageExpiration |TTLExpiredException |Komunikat wygasła i została martwych lettered. |
| SubscriptionDescription.RequiresSession |Identyfikator sesji ma wartość null. |Jednostki włączone sesji nie pozwala na komunikat, którego identyfikator sesji ma wartość null. |
| ! kolejki utraconych wiadomości |MaxTransferHopCountExceeded |Null |
| Jawne martwych drukiem aplikacji |Określone przez aplikację |Określone przez aplikację |

## <a name="exceeding-maxdeliverycount"></a>Powyżej MaxDeliveryCount

Kolejki, jak i subskrypcje mają [QueueDescription.MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount) i [SubscriptionDescription.MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription.maxdeliverycount) właściwości wartość domyślna to 10. Zawsze, gdy wiadomość została dostarczona w obszarze blokady ([ReceiveMode.PeekLock](/dotnet/api/microsoft.azure.servicebus.receivemode)), ale został albo jawnie porzucone lub blokady wygasło wiadomości [BrokeredMessage.DeliveryCount](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) jest zwiększany. Gdy [DeliveryCount](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) przekracza [MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount), wiadomość zostanie przeniesiona do DLQ, określając `MaxDeliveryCountExceeded` kod przyczyny.

Nie można wyłączyć to zachowanie, ale możesz ustawić [MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount) do bardzo dużej liczby.

## <a name="exceeding-timetolive"></a>Wartość TimeToLive powyżej

Podczas [QueueDescription.EnableDeadLetteringOnMessageExpiration](/dotnet/api/microsoft.servicebus.messaging.queuedescription#Microsoft_ServiceBus_Messaging_QueueDescription_EnableDeadLetteringOnMessageExpiration) lub [SubscriptionDescription.EnableDeadLetteringOnMessageExpiration](/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription#Microsoft_ServiceBus_Messaging_SubscriptionDescription_EnableDeadLetteringOnMessageExpiration) właściwość jest ustawiona na **wartość true,** (wartość domyślna to **false**), wszystkie komunikaty wygaszenie zostaną przeniesione do DLQ, określając `TTLExpiredException` kod przyczyny.

Zauważ, że komunikaty wygasłe tylko usunięte i przeniesiony do DLQ, gdy istnieje co najmniej jeden odbiornik active ściąganie z kolejki głównej lub subskrypcji; to zachowanie jest celowe.

## <a name="errors-while-processing-subscription-rules"></a>Wystąpiły błędy podczas przetwarzania reguły subskrypcji

Gdy [SubscriptionDescription.EnableDeadLetteringOnFilterEvaluationExceptions](/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription#Microsoft_ServiceBus_Messaging_SubscriptionDescription_EnableDeadLetteringOnFilterEvaluationExceptions) właściwości jest włączony dla subskrypcji, wszystkie błędy, które są wykonywane, gdy wykonuje regułę filtru SQL subskrypcji są przechwytywane DLQ wraz z komunikatu ataku.

## <a name="application-level-dead-lettering"></a>Poziom aplikacji obsługa utraconych komunikatów

Oprócz funkcji Obsługa utraconych komunikatów dostarczane przez system umożliwia aplikacjom DLQ jawnie odrzucić wiadomości nie do przyjęcia. Mogą to być wiadomości, których nie można przetworzyć poprawnie ze względu na dowolny rodzaj problemu z systemem, komunikatów zawierających nieprawidłowo sformułowany ładunek lub wiadomości, które niepowodzenia uwierzytelniania, gdy jest używany schemat niektórych zabezpieczenia na poziomie wiadomości.

## <a name="dead-lettering-in-forwardto-or-sendvia-scenarios"></a>Obsługa utraconych komunikatów w scenariuszach ForwardTo lub SendVia

Komunikaty będą wysyłane do kolejki utraconych wiadomości transfer w następujących warunkach:

- Komunikat przechodzi przez więcej niż 3 kolejki i tematy, które są [połączonych ze sobą](service-bus-auto-forwarding.md).
- Kolejka docelowa lub temat zostało wyłączone lub usunięte.
- Kolejka docelowa lub temat przekracza rozmiar maksymalny jednostki.

Aby pobrać te komunikaty lettered wiadomości, można utworzyć odbiornika za pomocą [FormatTransferDeadletterPath](/dotnet/api/microsoft.azure.servicebus.entitynamehelper.formattransferdeadletterpath) narzędzie metody.

## <a name="example"></a>Przykład

Poniższy fragment kodu tworzy odbiorcy wiadomości. W pętli odbioru dla kolejki głównej, kod pobiera wiadomość z [Receive(TimeSpan.Zero)](/dotnet/api/microsoft.servicebus.messaging.messagereceiver#Microsoft_ServiceBus_Messaging_MessageReceiver_Receive_System_TimeSpan_), z pytaniem brokera, aby natychmiast zwraca wszystkie dostępne wiadomości lub mają być zwracane z żadnego wyniku. Jeśli kod odbiera wiadomości, jego natychmiast odstępuje go, która zwiększa `DeliveryCount`. Po systemie przenosi wiadomość do DLQ, kolejki głównej jest pusty i kończy działanie pętli, jako [metody ReceiveAsync](/dotnet/api/microsoft.servicebus.messaging.messagereceiver#Microsoft_ServiceBus_Messaging_MessageReceiver_ReceiveAsync_System_TimeSpan_) zwraca **null**.

```csharp
var receiver = await receiverFactory.CreateMessageReceiverAsync(queueName, ReceiveMode.PeekLock);
while(true)
{
    var msg = await receiver.ReceiveAsync(TimeSpan.Zero);
    if (msg != null)
    {
        Console.WriteLine("Picked up message; DeliveryCount {0}", msg.DeliveryCount);
        await msg.AbandonAsync();
    }
    else
    {
        break;
    }
}
```

## <a name="next-steps"></a>Kolejne kroki

Zobacz następujące artykuły, aby uzyskać więcej informacji na temat kolejek usługi Service Bus:

* [Wprowadzenie do kolejek usługi Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Azure kolejki i usługi Service Bus w porównaniu kolejek](service-bus-azure-and-service-bus-queues-compared-contrasted.md)

