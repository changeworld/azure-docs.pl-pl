---
title: Omówienie transakcji przetwarzania w usłudze Azure Service Bus | Dokumentacja firmy Microsoft
description: Omówienie usługi Azure Service Bus transakcje niepodzielne i wysyłania za pomocą
services: service-bus-messaging
documentationcenter: .net
author: spelluru
manager: timlt
editor: ''
ms.assetid: 64449247-1026-44ba-b15a-9610f9385ed8
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/22/2018
ms.author: spelluru
ms.openlocfilehash: bc0e0bfb8d4dc8637c191785a98f5d15e086cbf5
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/05/2018
ms.locfileid: "43696435"
---
# <a name="overview-of-service-bus-transaction-processing"></a>Omówienie usługi Service Bus przetwarzanie transakcji

W tym artykule omówiono możliwości transakcji usługi Microsoft Azure Service Bus. Większość dyskusji zilustrowane [transakcje niepodzielne za pomocą usługi Service Bus przykładowe](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/AtomicTransactions). W tym artykule jest ograniczona do przeglądu przetwarzania transakcji i *wyśle* funkcji w usłudze Service Bus, przykładowe transakcje niepodzielne szersze i bardziej złożone w trakcie zakresu.

## <a name="transactions-in-service-bus"></a>Transakcje w usłudze Service Bus

A [ *transakcji* ](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/AtomicTransactions#what-are-transactions) grupowanie dwóch lub więcej operacji w *zakresu wykonywania*. Ze względu na charakter takich transakcji musi zapewnić, że wszystkie operacje należących do danej grupy operacji powodzenie lub niepowodzenie wspólnie. W związku z tym transakcji pełnić jedną jednostkę, która jest często nazywany *niepodzielność*. 

Usługa Service Bus jest broker wiadomości transakcyjnych i zapewnia integralność transakcji dla wszystkich operacji wewnętrznej względem jego magazynami komunikatów. Wszystkie operacje transferu wiadomości wewnątrz usługi Service Bus, takiego jak przenoszenie wiadomości do [kolejki utraconych wiadomości](service-bus-dead-letter-queues.md) lub [automatyczne przekazywanie](service-bus-auto-forwarding.md) komunikatów między jednostkami, są transakcyjne. Jako takie Jeśli Usługa Service Bus zaakceptuje wiadomość, już został przechowywane i etykietą numer sekwencyjny. Od tego momentu wszelkich transferów komunikatów w ramach usługi Service Bus to operacje skoordynowanego między jednostkami i nie doprowadzi do utraty (źródło zakończy się pomyślnie i docelowym nie powiedzie się) lub dublowania (niepowodzenie dotyczące źródła i docelowych zakończy się powodzeniem) wiadomości.

Usługa Service Bus obsługuje operacje względem pojedynczej jednostki obsługi komunikatów (kolejki, tematu, subskrypcji) w zakresie transakcji. Na przykład kilka wiadomości można wysyłać do jednej kolejki z zakresu transakcji, a komunikaty tylko zostaną przekazane do kolejki dziennika, po pomyślnym zakończeniu transakcji.

## <a name="operations-within-a-transaction-scope"></a>Operacje w zakresie transakcji

Operacje, które mogą być wykonywane w zakresie transakcji są następujące:

* **[QueueClient](/dotnet/api/microsoft.azure.servicebus.queueclient), [MessageSender](/dotnet/api/microsoft.azure.servicebus.core.messagesender), [TopicClient](/dotnet/api/microsoft.azure.servicebus.topicclient)**: wysyłanie SendAsync, SendBatch, SendBatchAsync 
* **[BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage)**: zakończeniu CompleteAsync Abandon, AbandonAsync, utraconych wiadomości, DeadletterAsync, odroczone, DeferAsync, RenewLock, RenewLockAsync 

Odbieranie operacje nie są dołączane, ponieważ zakłada się, że aplikacja uzyskuje komunikatów za pomocą [ReceiveMode.PeekLock](/dotnet/api/microsoft.azure.servicebus.receivemode) tryb wewnątrz niektórych otrzymywać pętli lub za pomocą [OnMessage](/dotnet/api/microsoft.servicebus.messaging.queueclient.onmessage) wywołanie zwrotne, i dopiero wtedy otwiera zakresu transakcji do przetwarzania wiadomości.

Dyspozycja komunikatu (zakończeniu Odrocz abandon, wiadomości utraconych) to występuje w zakresie i zależne od ogólnej wyniku transakcji.

## <a name="transfers-and-send-via"></a>Opłaty za transfery i "Wyślij za pośrednictwem"

Aby włączyć transakcyjnych przekazania danych z kolejki procesora, a następnie do innej kolejki, Usługa Service Bus obsługuje *transfery*. W operacji przeniesienia, nadawca wysyła najpierw wiadomości do *kolejki transferu*, i natychmiast przenosi wiadomość do kolejki miejsca docelowego przy użyciu tej samej implementacji niezawodne przesyłanie w kolejce transferu, automatycznego przekazywania dalej Funkcja opiera się na. Komunikat nigdy nie jest zaangażowana w kolejce transferu dziennika w taki sposób, że stają się one widoczne dla klientów w kolejce transferu.

Możliwości tej transakcji funkcji staje się jasne po kolejce transferu, sama jest źródłem nadawcy komunikatów wejściowych. Innymi słowy, usługi Service Bus mogą przesyłać wiadomości do kolejki docelowej "przez" kolejki transferu podczas wykonywania kompletna (lub Odrocz, lub utraconych) operacja komunikat wejściowy, wszystko to w jednej niepodzielnej operacji. 

### <a name="see-it-in-code"></a>Zobacz go w kodzie

Aby skonfigurować takie przeniesienia, należy utworzyć nadawcy wiadomości, który jest przeznaczony dla kolejki docelowej, za pośrednictwem kolejki transferu. Masz również odbiornik, który pobiera komunikaty z tej samej kolejki. Na przykład:

```csharp
var sender = this.messagingFactory.CreateMessageSender(destinationQueue, myQueueName);
var receiver = this.messagingFactory.CreateMessageReceiver(myQueueName);
```

Następnie proste transakcji korzysta z tych elementów, jak w poniższym przykładzie:

```csharp
var msg = receiver.Receive();

using (scope = new TransactionScope())
{
    // Do whatever work is required 

    var newmsg = ... // package the result 

    msg.Complete(); // mark the message as done
    sender.Send(newmsg); // forward the result

    scope.Complete(); // declare the transaction done
} 
```

## <a name="next-steps"></a>Kolejne kroki

Zobacz następujące artykuły, aby uzyskać więcej informacji na temat usługi Service Bus:

* [Jak używać kolejek usługi Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Tworzenie łańcuchów jednostek usługi Service Bus z automatycznym przekazywaniem](service-bus-auto-forwarding.md)
* [Przykładowe automatycznego przekazywania dalej](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/AutoForward)
* [Transakcje niepodzielne za pomocą przykładowej usługi Service Bus](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/AtomicTransactions)
* [Kolejek systemu Azure i usługi Service Bus porównaniu kolejek](service-bus-azure-and-service-bus-queues-compared-contrasted.md)


