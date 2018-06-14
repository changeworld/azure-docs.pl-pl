---
title: Omówienie transakcji przetwarzania w Azure Service Bus | Dokumentacja firmy Microsoft
description: Omówienie usługi Azure Service Bus atomic transakcji i za pośrednictwem
services: service-bus-messaging
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: 64449247-1026-44ba-b15a-9610f9385ed8
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/22/2018
ms.author: sethm
ms.openlocfilehash: d2e3fc7c59e0b57e77d2239ff73368f96426ef39
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/24/2018
ms.locfileid: "29558945"
---
# <a name="overview-of-service-bus-transaction-processing"></a>Omówienie przetwarzanie transakcji usługi Service Bus

W tym artykule omówiono możliwości transakcji usługi Microsoft Azure Service Bus. Znacznie dyskusji zilustrowane [Atomic transakcji z usługi Service Bus próbki](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/AtomicTransactions). W tym artykule jest ograniczona do przeglądu przetwarzanie transakcji i *wyśle* funkcji w usłudze Service Bus, gdy przykład Atomic transakcji jest bardziej rozbudowaną i bardziej złożonej w zakresie.

## <a name="transactions-in-service-bus"></a>Transakcje w usłudze Service Bus

A [ *transakcji* ](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/AtomicTransactions#what-are-transactions) grupowanie dwóch lub więcej operacji w *zakresu wykonywania*. Natury takich transakcji musi zapewnić, że wszystkie operacje należących do danej grupy działań albo powodzenie lub Niepowodzenie w wspólnie. W związku z tym transakcji pełnić jedną jednostkę, która jest często określany jako *niepodzielność*. 

Usługa Service Bus jest brokera wiadomości transakcyjnych i zapewnia integralność transakcyjne dla wszystkich operacji wewnętrznych przed jego magazyny wiadomości. Wszystkie operacje transferu komunikatów wewnątrz usługi Service Bus, takich jak przenoszenie wiadomości do [kolejki utraconych wiadomości](service-bus-dead-letter-queues.md) lub [automatyczne przekazywanie](service-bus-auto-forwarding.md) komunikatów między jednostkami, są transakcyjnych. Tak Jeśli usługi Service Bus akceptuje wiadomości, już został przechowywane i z liczbą sekwencji. Następnie wszelkich transferów komunikatów w ramach usługi Service Bus są skoordynowanej operacji w ramach jednostkami, a nie doprowadzi do utraty (źródła zakończy się pomyślnie i docelowy nie powiedzie się) lub do duplikacji (źródłowego nie powiedzie się i docelowego zakończy się powodzeniem) wiadomości.

Usługa Service Bus obsługuje operacje grupowania względem jednej jednostki obsługi komunikatów (kolejki, tematu, subskrypcji) w zakresie transakcji. Na przykład kilka wiadomości można wysyłać do jednej kolejki z w zakresie transakcji, a wiadomości tylko zostaną przekazane do kolejki dziennika, po pomyślnym zakończeniu transakcji.

## <a name="operations-within-a-transaction-scope"></a>Operacje w zakresie transakcji

Oto operacje, które mogą być wykonywane w zakresie transakcji:

* **[QueueClient](/dotnet/api/microsoft.azure.servicebus.queueclient), [MessageSender](/dotnet/api/microsoft.azure.servicebus.core.messagesender), [TopicClient](/dotnet/api/microsoft.azure.servicebus.topicclient)**: wysyłanie SendAsync, SendBatch, SendBatchAsync 
* **[BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage)**: zakończeniu CompleteAsync, porzucenia, AbandonAsync, utraconych wiadomości, DeadletterAsync, wstrzymuje, DeferAsync, RenewLock, RenewLockAsync 

Odbieranie operacje nie są uwzględnione, ponieważ zakłada się, że aplikacja uzyskuje wiadomości przy użyciu [ReceiveMode.PeekLock](/dotnet/api/microsoft.azure.servicebus.receivemode) pętlę odbierania tryb, w niektórych lub [OnMessage](/dotnet/api/microsoft.servicebus.messaging.queueclient.onmessage) wywołania zwrotnego i dopiero wtedy otwiera zakresu transakcji do przetwarzania komunikatu.

Dyspozycji komunikatu (zakończeniu porzucenia, wiadomości utraconych odroczyć) to występuje w zakresie i zależne od ogólnej wyniku transakcji.

## <a name="transfers-and-send-via"></a>Transfery i "Wyślij za pośrednictwem"

Aby włączyć transakcyjne przekazania danych z kolejki procesora, a następnie do innej kolejki, Usługa Service Bus obsługuje *transferów*. W operacji przeniesienia nadawca najpierw wysyła komunikat do *kolejce transferu*, i w kolejce transferu na natychmiast przenosi wiadomości w kolejce przeznaczenia przy użyciu tej samej implementacji transferu niezawodne który automatycznie do przodu zależy od możliwości. Komunikat nigdy nie jest zaangażowana w kolejce transferu dziennika w taki sposób, że stają się one widoczne dla użytkowników w kolejce transferu.

Możliwości tej możliwości transakcyjnych stała się oczywista podczas transferu kolejki jest źródłem komunikaty wejściowe nadawcy. Innymi słowy, usługi Service Bus można transferu wiadomości do kolejki docelowej "przez" kolejce transferu na podczas wykonywania pełnego (lub mają być odroczone, lub utraconych) operacji na komunikat wejściowy, wszystko w jednym niepodzielną operację. 

### <a name="see-it-in-code"></a>Zobacz go w kodzie

Aby skonfigurować przeniesienie, należy utworzyć przeznaczonego do kolejki docelowej za pośrednictwem kolejki transfer nadawcy wiadomości. Masz również odbiornik, który pobiera komunikaty z tej samej kolejki. Na przykład:

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

Zobacz następujące artykuły, aby uzyskać więcej informacji na temat kolejek usługi Service Bus:

* [Jak używać kolejek usługi Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Tworzenie łańcuchów jednostek usługi Service Bus z automatyczne przekazywanie](service-bus-auto-forwarding.md)
* [Przykładowe do przodu automatycznie](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/AutoForward)
* [Niepodzielne transakcji z usługi Service Bus próbki](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/AtomicTransactions)
* [Azure kolejki i usługi Service Bus w porównaniu kolejek](service-bus-azure-and-service-bus-queues-compared-contrasted.md)


