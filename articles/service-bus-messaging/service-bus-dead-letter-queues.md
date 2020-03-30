---
title: Kolejki utraconych wiadomości w usłudze Service Bus | Dokumenty firmy Microsoft
description: W tym artykule opisano kolejki utraconych wiadomości w usłudze Azure Service Bus. Kolejki usługi Service Bus i subskrypcje tematów zapewniają drugą podoka, zwaną kolejką utraconych wiadomości.
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
ms.date: 03/23/2020
ms.author: aschhab
ms.openlocfilehash: 9c1a0cb92fbaf98d25799ffb5a85e666e7c05f8c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80158909"
---
# <a name="overview-of-service-bus-dead-letter-queues"></a>Omówienie kolejek utraconych wiadomości usługi Service Bus

Kolejki usługi Azure Service Bus i subskrypcje tematów zapewniają pomocniczą podoka, zwaną *kolejką utraconych wiadomości* (DLQ). Kolejki utraconych wiadomości nie musi być jawnie tworzone i nie można usunąć lub w inny sposób zarządzane niezależnie od jednostki głównej.

W tym artykule opisano kolejki utraconych wiadomości w usłudze Service Bus. Wiele dyskusji jest zilustrowane przez [dead-letter kolejki próbki](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/DeadletterQueue) na GitHub.
 
## <a name="the-dead-letter-queue"></a>Kolejka utraconych wiadomości

Celem kolejki utraconych wiadomości jest przytrzymanie wiadomości, które nie mogą być dostarczone do dowolnego odbiornika lub wiadomości, które nie mogą być przetwarzane. Wiadomości można następnie usunąć z dlq i sprawdzić. Aplikacja może, za pomocą operatora, rozwiązać problemy i ponownie przesłać wiadomość, zarejestrować fakt, że wystąpił błąd i podjąć działania naprawcze. 

Z punktu widzenia interfejsu API i protokołu DLQ jest w większości podobny do każdej innej kolejki, z tą różnicą, że wiadomości mogą być przesyłane tylko za pośrednictwem operacji utraconej wiadomości jednostki nadrzędnej. Ponadto czas do żywo nie jest przestrzegane, i nie można dead-letter wiadomości z DLQ. Kolejka utraconych wiadomości w pełni obsługuje dostarczanie blokady wglądu i operacje transakcyjne.

Nie ma automatycznego oczyszczania dlq. Wiadomości pozostają w dlq, dopóki jawnie pobrać je z DLQ i wywołać [Complete()](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync) w wiadomości utraconej wiadomości.

## <a name="dlq-message-count"></a>Liczba komunikatów DLQ
Nie jest możliwe uzyskanie liczby wiadomości w kolejce utraconych wiadomości na poziomie tematu. To dlatego, że wiadomości nie siedzą na poziomie tematu, chyba że usługa Service Bus zgłasza błąd wewnętrzny. Zamiast tego gdy nadawca wysyła wiadomość do tematu, wiadomość jest przekazywać dalej do subskrypcji tematu w milisekundach i w związku z tym nie znajduje się już na poziomie tematu. Tak, można zobaczyć wiadomości w DLQ skojarzone z subskrypcji dla tematu. W poniższym przykładzie **Eksplorator usługi Service Bus** pokazuje, że istnieją 62 komunikaty obecnie w DLQ dla subskrypcji "test1". 

![Liczba komunikatów DLQ](./media/service-bus-dead-letter-queues/dead-letter-queue-message-count.png)

Można również uzyskać liczbę wiadomości DLQ za pomocą [`az servicebus topic subscription show`](/cli/azure/servicebus/topic/subscription?view=azure-cli-latest#az-servicebus-topic-subscription-show)polecenia interfejsu wiersza polecenia platformy Azure: . 

## <a name="moving-messages-to-the-dlq"></a>Przenoszenie wiadomości do biblioteki ddlq

Istnieje kilka działań w usłudze Service Bus, które powodują, że wiadomości są wypychane do DLQ z poziomu samego aparatu obsługi wiadomości. Aplikacja może również jawnie przenosić wiadomości do DLQ. 

Gdy wiadomość zostanie przeniesiona przez brokera, dwie właściwości są dodawane do wiadomości, gdy broker wywołuje `DeadLetterReason` `DeadLetterErrorDescription`jego wewnętrzną wersję metody [DeadLetter](/dotnet/api/microsoft.azure.servicebus.queueclient.deadletterasync) w wiadomości: i .

Aplikacje można zdefiniować `DeadLetterReason` własne kody dla właściwości, ale system ustawia następujące wartości.

| Warunek | DeadLetterReason (Nie żyje) | Martwy Tekst w języku: DeadLetterErrorDescription |
| --- | --- | --- |
| Zawsze |HeaderSizeExceed |Przekroczono limit przydziału rozmiaru dla tego strumienia. |
| ! Opis tematu.<br />EnableFilteringMessagesBeforePublishing i SubscriptionDescription.<br />EnableDeadLetteringOnFilterEvaluationWynikwycekcje |Wyjątek. GetType(). Nazwa |Wyjątek. Komunikat |
| WłączUdyederLetteringOnMessageExpiration |TTLExpiredException |Komunikat wygasł i został uznany za utracony. |
| SubskrypcjaDescription.RequiresSession |Identyfikator sesji ma wartość null. |Jednostka z obsługą sesji nie pozwala na komunikat, którego identyfikator sesji ma wartość null. |
| !kolejka utraconych listów | MaxTransferHopCountExceeded | Maksymalna dozwolona przeskoki podczas przesyłania dalej między kolejkami. Wartość jest ustawiona na 4. |
| Wyraźne martwe litery aplikacji |Określone przez aplikację |Określone przez aplikację |

## <a name="exceeding-maxdeliverycount"></a>Przekroczenie maksymalnej liczby

Kolejki i subskrypcje mają [queueDescription.MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount) i [SubscriptionDescription.MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription.maxdeliverycount) właściwości odpowiednio; wartość domyślna to 10. Za każdym razem, gdy wiadomość została dostarczona pod blokadą[(ReceiveMode.PeekLock),](/dotnet/api/microsoft.azure.servicebus.receivemode)ale została jawnie porzucona lub blokada wygasła, wiadomość [BrokeredMessage.DeliveryCount](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) jest zwiększana. Gdy [DeliveryCount](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) przekracza [MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount), wiadomość jest przenoszona do `MaxDeliveryCountExceeded` DLQ, określając kod przyczyny.

Tego zachowania nie można wyłączyć, ale można ustawić [MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount) na dużą liczbę.

## <a name="exceeding-timetolive"></a>Przekroczenie timetolive

Gdy [właściwość QueueDescription.EnableDeadLetteringOnMessageExpiration](/dotnet/api/microsoft.servicebus.messaging.queuedescription) lub [SubscriptionDescription.EnableDeadLetteringOnMessageExpiration](/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription) jest ustawiona na **true** (wartość domyślna jest **false),** `TTLExpiredException` wszystkie wygasające komunikaty są przenoszone do DLQ, określając kod przyczyny.

Wygasłe wiadomości są czyszczone i przenoszone do DLQ tylko wtedy, gdy istnieje co najmniej jeden aktywny odbiornik ściągający z kolejki głównej lub subskrypcji; że zachowanie jest zgodnie z projektem.

## <a name="errors-while-processing-subscription-rules"></a>Błędy podczas przetwarzania reguł subskrypcji

Gdy [SubscriptionDescription.EnableDeadLetteringOnFilterEvaluationExceptions](/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription) właściwość jest włączona dla subskrypcji, wszelkie błędy, które występują podczas wykonywania reguły filtru SQL subskrypcji są przechwytywane w DLQ wraz z komunikatem naruszającym.

## <a name="application-level-dead-lettering"></a>Napisy martwe na poziomie aplikacji

Oprócz funkcji dead-lettering dostarczonych przez system, aplikacje mogą używać DLQ jawnie odrzucić niedopuszczalne wiadomości. Mogą one zawierać wiadomości, które nie mogą być poprawnie przetwarzane z powodu jakiegokolwiek problemu z systemem, wiadomości, które zawierają nieprawidłowo sformułowane ładunki lub wiadomości, które nie uwierzytelniania, gdy używany jest jakiś schemat zabezpieczeń na poziomie wiadomości.

## <a name="dead-lettering-in-forwardto-or-sendvia-scenarios"></a>Napisy martwe w scenariuszach ForwardTo lub SendVia

Wiadomości będą wysyłane do kolejki utraconych wiadomości transferu pod następującymi warunkami:

- Wiadomość przechodzi przez więcej niż cztery kolejki lub tematy, które są [połączone ze sobą](service-bus-auto-forwarding.md).
- Kolejka docelowa lub temat jest wyłączona lub usunięta.
- Kolejka docelowa lub temat przekracza maksymalny rozmiar jednostki.

Aby pobrać te wiadomości z martwymi literami, można utworzyć odbiornik przy użyciu metody narzędzia [FormatTransferDeadletterPath.](/dotnet/api/microsoft.azure.servicebus.entitynamehelper.formattransferdeadletterpath)

## <a name="example"></a>Przykład

Poniższy fragment kodu tworzy odbiornik wiadomości. W pętli odbierania dla kolejki głównej kod pobiera wiadomość z [Receive(TimeSpan.Zero),](/dotnet/api/microsoft.servicebus.messaging.messagereceiver)która prosi brokera o natychmiastowe zwrócenie każdej wiadomości łatwo dostępnej lub zwrócenie bez rezultatu. Jeśli kod otrzyma wiadomość, natychmiast ją porzuca, co `DeliveryCount`zwiększa . Gdy system przeniesie wiadomość do DLQ, kolejka główna jest pusta i pętla kończy się, ponieważ [ReceiveAsync](/dotnet/api/microsoft.servicebus.messaging.messagereceiver) zwraca **wartość null**.

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
Dostęp do kolejki utraconych wiadomości można uzyskać przy użyciu następującej składni:

```
<queue path>/$deadletterqueue
<topic path>/Subscriptions/<subscription path>/$deadletterqueue
```

Jeśli używasz .NET SDK, można uzyskać ścieżkę do kolejki utraconych wiadomości przy użyciu SubscriptionClient.FormatDeadLetterPath(). Ta metoda przyjmuje nazwę tematu/nazwę subskrypcji i sufiksy z **/$DeadLetterQueue**.


## <a name="next-steps"></a>Następne kroki

Więcej informacji na temat kolejek usługi Service Bus można znaleźć w następujących artykułach:

* [Wprowadzenie do kolejek usługi Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Porównywano kolejki kolejek platformy Azure i usługi Service Bus](service-bus-azure-and-service-bus-queues-compared-contrasted.md)

