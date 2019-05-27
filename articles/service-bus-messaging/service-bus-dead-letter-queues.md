---
title: Kolejki utraconych wiadomości usługi Service Bus | Dokumentacja firmy Microsoft
description: Omówienie usługi Azure Service Bus, kolejki utraconych wiadomości
services: service-bus-messaging
documentationcenter: .net
author: axisc
manager: timlt
editor: spelluru
ms.assetid: 68b2aa38-dba7-491a-9c26-0289bc15d397
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/21/2019
ms.author: aschhab
ms.openlocfilehash: af67b27dacf3bb86c2dd5c878a2751e027a53acb
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/22/2019
ms.locfileid: "66003131"
---
# <a name="overview-of-service-bus-dead-letter-queues"></a>Omówienie usługi Service Bus, kolejki utraconych wiadomości

Tematu subskrypcji i kolejek usługi Azure Service Bus zapewnia dodatkowej kolejki podrzędnej, o nazwie *kolejki utraconych wiadomości* (DLQ). Kolejki utraconych wiadomości nie trzeba jawnie tworzyć i nie może być usunięte ani w przeciwnym razie zarządzane niezależnie od elementu głównego.

W tym artykule opisano kolejki utraconych wiadomości w usłudze Service Bus. Większość dyskusji zilustrowane [przykładowe kolejki utraconych wiadomości](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/DeadletterQueue) w witrynie GitHub.
 
## <a name="the-dead-letter-queue"></a>Kolejki utraconych wiadomości

Kolejki utraconych wiadomości ma na celu przechowywania komunikatów, których nie można dostarczyć do żadnego odbiorcy lub wiadomości, których nie można przetworzyć. Komunikaty można następnie usuwane z DLQ i inspekcji. Aplikacja może za pomocą operatora, rozwiązać problemy dotyczące ponownie przesłać wiadomość, fakt, że wystąpił błąd podczas logowania i akcje naprawcze. 

Z perspektywy interfejsu API i protokół DLQ przede wszystkim jest podobny do innych kolejki, z tą różnicą, że komunikaty zostać przesłane tylko za pośrednictwem operacji utraconych obiektu nadrzędnego. Ponadto czas wygaśnięcia nie zostanie wykryty, i nie jest możliwe utraconych wiadomości z DLQ. Kolejki utraconych wiadomości w pełni obsługuje dostarczanie blokady podglądu i operacje transakcyjne.

Zwróć uwagę, że żadne automatyczne oczyszczanie DLQ. Wiadomości pozostają w DLQ, aż jawnie je odzyskać z DLQ i wywołanie [Complete()](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync) na utraconych wiadomości.

## <a name="moving-messages-to-the-dlq"></a>Przenoszenie wiadomości do DLQ

Istnieje kilka działań w usłudze Service Bus, które powodują komunikaty, aby pobrać wypchnięte do DLQ z w ramach aparatu obsługi komunikatów, sam. Aplikację można także jawnie przenosić wiadomości, do DLQ. 

Jak wiadomość zostanie przeniesiona przez brokera, dwie właściwości są dodawane do wiadomości jako broker wywołuje jego wewnętrzny wersję [utraconych](/dotnet/api/microsoft.azure.servicebus.queueclient.deadletterasync) metody w komunikacie: `DeadLetterReason` i `DeadLetterErrorDescription`.

Aplikacje można zdefiniować własne kody `DeadLetterReason` właściwości, ale system ustawia następujące wartości.

| Warunek | DeadLetterReason | DeadLetterErrorDescription |
| --- | --- | --- |
| Zawsze |HeaderSizeExceeded |Przekroczono limit przydziału rozmiaru tego strumienia. |
| ! TopicDescription.<br />EnableFilteringMessagesBeforePublishing i SubscriptionDescription.<br />EnableDeadLetteringOnFilterEvaluationExceptions |exception.GetType().Name |exception.Message |
| EnableDeadLetteringOnMessageExpiration |TTLExpiredException |Komunikat wygasła i została dead lettered. |
| SubscriptionDescription.RequiresSession |Identyfikator sesji ma wartość null. |Jednostki włączone sesji nie pozwala na komunikat, którego identyfikator sesji ma wartość null. |
| ! kolejki utraconych wiadomości |MaxTransferHopCountExceeded |Null |
| Jawne, dead czcionki w aplikacji |Określone przez aplikację |Określone przez aplikację |

## <a name="exceeding-maxdeliverycount"></a>Exceeding MaxDeliveryCount

Kolejki, jak i subskrypcje mają [QueueDescription.MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount) i [SubscriptionDescription.MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription.maxdeliverycount) właściwość odpowiednio; wartość domyślna to 10. Zawsze, gdy wiadomość została dostarczona w ramach blokady ([ReceiveMode.PeekLock](/dotnet/api/microsoft.azure.servicebus.receivemode)), ale została albo jawnie porzucone lub blokada wygasła komunikat [BrokeredMessage.DeliveryCount](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) jest zwiększona. Gdy [DeliveryCount](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) przekracza [MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount), wiadomość zostanie przeniesiona do DLQ, określając `MaxDeliveryCountExceeded` kod przyczyny.

Nie można wyłączyć tego zachowania, ale możesz ustawić [MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount) na bardzo dużą liczbą.

## <a name="exceeding-timetolive"></a>Przekroczenie TimeToLive

Gdy [QueueDescription.EnableDeadLetteringOnMessageExpiration](/dotnet/api/microsoft.servicebus.messaging.queuedescription) lub [SubscriptionDescription.EnableDeadLetteringOnMessageExpiration](/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription) właściwość jest ustawiona na **true** (wartość domyślna to **false**), wszystkie komunikaty wygasające zostaną przeniesione do DLQ, określając `TTLExpiredException` kod przyczyny.

Pamiętaj, że wygasłe wiadomości tylko przeczyszczania i przeniesione do DLQ, gdy istnieje co najmniej jeden aktywny odbiornik ściąganie z głównym kolejki lub subskrypcji; to zachowanie jest celowe.

## <a name="errors-while-processing-subscription-rules"></a>Błędy podczas przetwarzania reguły subskrypcji

Gdy [SubscriptionDescription.EnableDeadLetteringOnFilterEvaluationExceptions](/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription) właściwość jest włączona dla subskrypcji, wszelkie błędy, które wystąpiły w trakcie wykonuje regułę filtru SQL subskrypcji są przechwytywane DLQ wzdłuż komunikatem powodujący problemy.

## <a name="application-level-dead-lettering"></a>Obsługa utraconych komunikatów dodatku poziomu aplikacji

Oprócz funkcji Obsługa utraconych komunikatów dostarczane przez system umożliwia aplikacjom DLQ jawnie odrzucenia wiadomości nie do przyjęcia. Może to obejmować wiadomości, które nie mogą być przetwarzane prawidłowo ze względu na dowolny rodzaj systemu, wiadomości, w których ładunki źle sformułowane lub wiadomości, które niepowodzenia uwierzytelniania, gdy używany jest schemat pewne zabezpieczenia na poziomie komunikatu.

## <a name="dead-lettering-in-forwardto-or-sendvia-scenarios"></a>Obsługa utraconych komunikatów w scenariuszach Prześlij lub Wyślij za pomocą

Komunikaty będą wysyłane do kolejki utraconych wiadomości transferu w następujących warunkach:

- Komunikat przechodzi przez więcej niż 4 kolejek lub tematów, które są [połączonych ze sobą](service-bus-auto-forwarding.md).
- Miejsce docelowe kolejki lub tematu zostało wyłączone lub usunięte.
- Miejsce docelowe kolejki lub tematu przekracza rozmiar maksymalny jednostki.

Aby pobrać te lettered Obsługa utraconych komunikatów, można utworzyć odbiornika zdarzenia za pomocą [FormatTransferDeadletterPath](/dotnet/api/microsoft.azure.servicebus.entitynamehelper.formattransferdeadletterpath) metoda narzędzia.

## <a name="example"></a>Przykład

Poniższy fragment kodu tworzy odbiorcom wiadomości. W pętli receive kolejka główna kod pobiera wiadomość z [Receive(TimeSpan.Zero)](/dotnet/api/microsoft.servicebus.messaging.messagereceiver), która prosi brokera, aby natychmiast przywrócić wszystkie komunikaty, które są łatwo dostępne lub zostać zwrócone wraz z żadnego wyniku. Jeśli kod otrzymuje komunikat, ją od razu porzuca go, które zwiększa `DeliveryCount`. Po system przenosi wiadomość do DLQ, kolejka główna jest pusta, i kończy działanie pętli, jak [ReceiveAsync](/dotnet/api/microsoft.servicebus.messaging.messagereceiver) zwraca **null**.

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

## <a name="path-to-the-dead-letter-queue"></a>Ścieżka do kolejki utraconych wiadomości
Kolejki utraconych wiadomości można uzyskać dostęp, używając następującej składni:

```
<queue path>/$deadletterqueue
<topic path>/Subscription/<subscription path>/$deadletterqueue
```

Jeśli używasz zestawu SDK .NET można uzyskać ścieżki do kolejki utraconych wiadomości przy użyciu metody SubscriptionClient.FormatDeadLetterPath(). Ta metoda przyjmuje nazwę subskrypcji/nazwę tematu i sufiksy z **/$DeadLetterQueue**.


## <a name="next-steps"></a>Kolejne kroki

Zobacz następujące artykuły, aby uzyskać więcej informacji na temat usługi Service Bus:

* [Wprowadzenie do kolejek usługi Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Kolejek systemu Azure i usługi Service Bus porównaniu kolejek](service-bus-azure-and-service-bus-queues-compared-contrasted.md)

