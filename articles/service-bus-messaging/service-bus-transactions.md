---
title: Omówienie przetwarzania transakcji w usłudze Azure Service Bus
description: W tym artykule przedstawiono omówienie przetwarzania transakcji i funkcji wysyłania za pośrednictwem usługi Azure Service Bus.
services: service-bus-messaging
documentationcenter: .net
author: axisc
editor: spelluru
ms.assetid: 64449247-1026-44ba-b15a-9610f9385ed8
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/27/2020
ms.author: aschhab
ms.openlocfilehash: 22744ecbced40b3195f4d047227b1e2a37228102
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79260906"
---
# <a name="overview-of-service-bus-transaction-processing"></a>Omówienie przetwarzania transakcji usługi Service Bus

W tym artykule omówiono możliwości transakcji usługi Microsoft Azure Service Bus. Wiele dyskusji jest zilustrowane przez [przykaziewki transakcji AMQP z usługą Service Bus](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/TransactionsAndSendVia/TransactionsAndSendVia/AMQPTransactionsSendVia). Ten artykuł jest ograniczony do przeglądu przetwarzania transakcji i *funkcji wysyłania za pośrednictwem* usługi Service Bus, podczas gdy atomic transactions próbki jest szerszy i bardziej złożony w zakresie.

## <a name="transactions-in-service-bus"></a>Transakcje w usłudze Service Bus

*Transakcja* grupuje dwie lub więcej operacji razem w *zakresie wykonania*. Z natury taka transakcja musi zapewnić, że wszystkie operacje należące do danej grupy operacji albo odnieść sukces, albo zakończyć się niepowodzeniem. W tym względzie transakcje działają jako jedna jednostka, która jest często określana jako *niepodzielność*.

Usługa Service Bus jest brokerem komunikatów transakcyjnych i zapewnia integralność transakcyjną dla wszystkich operacji wewnętrznych względem jego magazynów komunikatów. Wszystkie transfery wiadomości wewnątrz usługi Service Bus, takie jak przenoszenie wiadomości do [kolejki utraconych wiadomości](service-bus-dead-letter-queues.md) lub [automatyczne przekazywanie](service-bus-auto-forwarding.md) wiadomości między jednostkami, są transakcyjne. W związku z tym jeśli usługa Service Bus zaakceptuje wiadomość, została już zapisana i oznaczona numerem sekwencyjnym. Od tego czasu wszelkie transfery komunikatów w ramach usługi Service Bus są koordynowane operacje między jednostkami i nie prowadzi do utraty (źródło kończy się powodzeniem i docelowe nie powiedzie się) lub do duplikacji (źródło kończy się niepowodzeniem i cel zakończy się pomyślnie) wiadomości.

Usługa Service Bus obsługuje grupowanie operacji względem jednej jednostki obsługi komunikatów (kolejki, tematu, subskrypcji) w zakresie transakcji. Na przykład można wysłać kilka wiadomości do jednej kolejki z zakresu transakcji, a wiadomości zostaną zatwierdzone do dziennika kolejki tylko po pomyślnym zakończeniu transakcji.

## <a name="operations-within-a-transaction-scope"></a>Operacje w zakresie transakcji

Operacje, które mogą być wykonywane w zakresie transakcji są następujące:

* ** [QueueClient](/dotnet/api/microsoft.azure.servicebus.queueclient), [MessageSender](/dotnet/api/microsoft.azure.servicebus.core.messagesender), [TopicClient](/dotnet/api/microsoft.azure.servicebus.topicclient)**: Wyślij, SendAsync, SendBatch, SendBatchAsync 
* **[BrokeredMessage:](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage)** Complete, CompleteAsync, AbandonAsync, Deadletter, DeadletterAsync, Defer, DeferAsync, RenewLock, RenewLockAsync 

Operacje odbierania nie są uwzględniane, ponieważ zakłada się, że aplikacja uzyskuje wiadomości przy użyciu trybu [ReceiveMode.PeekLock,](/dotnet/api/microsoft.azure.servicebus.receivemode) wewnątrz niektórych pętli odbierania lub z wywołaniem zwrotnym [OnMessage,](/dotnet/api/microsoft.servicebus.messaging.queueclient.onmessage) a dopiero potem otwiera zakres transakcji do przetwarzania wiadomości.

Dyspozycja wiadomości (complete, abandon, dead-letter, defer), a następnie występuje w zakresie i zależy od ogólnego wyniku transakcji.

## <a name="transfers-and-send-via"></a>Przelewy i "wyślij przez"

Aby włączyć transakcyjne przekazywanie danych z kolejki do procesora, a następnie do innej kolejki, usługa Service Bus obsługuje *transfery*. W operacji transferu nadawca najpierw wysyła wiadomość do *kolejki transferu,* a kolejka transferu natychmiast przenosi wiadomość do zamierzonej kolejki docelowej przy użyciu tej samej implementacji transferu niezawodnego, na której polega funkcja automatycznego przesyłania dalej. Komunikat nigdy nie jest zobowiązana do dziennika kolejki transferu w taki sposób, że staje się widoczny dla konsumentów kolejki transferu.

Moc tej możliwości transakcyjnych staje się widoczna, gdy sama kolejka transferu jest źródłem komunikatów wejściowych nadawcy. Innymi słowy usługa Service Bus może przenieść wiadomość do kolejki docelowej "za pośrednictwem" kolejki transferu, podczas wykonywania pełnej operacji (lub odroczyć lub martwą literę) w komunikacie wejściowym, wszystko w jednej operacji atomowej. 

### <a name="see-it-in-code"></a>Zobacz go w kodzie

Aby skonfigurować takie transfery, należy utworzyć nadawcę wiadomości, który jest przeznaczony dla kolejki docelowej za pośrednictwem kolejki transferu. Masz również odbiornik, który pobiera wiadomości z tej samej kolejki. Przykład:

```csharp
var connection = new ServiceBusConnection(connectionString);

var sender = new MessageSender(connection, QueueName);
var receiver = new MessageReceiver(connection, QueueName);
```

Prosta transakcja następnie używa tych elementów, jak w poniższym przykładzie. Aby odwołać się do pełnego przykładu, zapoznaj się z [kodem źródłowym w usłudze GitHub:](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/TransactionsAndSendVia/TransactionsAndSendVia/AMQPTransactionsSendVia)

```csharp
var receivedMessage = await receiver.ReceiveAsync();

using (var ts = new TransactionScope(TransactionScopeAsyncFlowOption.Enabled))
{
    try
    {
        // do some processing
        if (receivedMessage != null)
            await receiver.CompleteAsync(receivedMessage.SystemProperties.LockToken);

        var myMsgBody = new MyMessage
        {
            Name = "Some name",
            Address = "Some street address",
            ZipCode = "Some zip code"
        };

        // send message
        var message = myMsgBody.AsMessage();
        await sender.SendAsync(message).ConfigureAwait(false);
        Console.WriteLine("Message has been sent");

        // complete the transaction
        ts.Complete();
    }
    catch (Exception ex)
    {
        // This rolls back send and complete in case an exception happens
        ts.Dispose();
        Console.WriteLine(ex.ToString());
    }
}
```

## <a name="next-steps"></a>Następne kroki

Więcej informacji na temat kolejek usługi Service Bus można znaleźć w następujących artykułach:

* [Jak używać kolejek usługi Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Łączenie elementów usługi Service Bus z automatycznym przekazywaniem](service-bus-auto-forwarding.md)
* [Próbka do przodu z automatycznym przekazywaniem](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/AutoForward)
* [Przykład transakcji atomowych za pomocą magistrali usług](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/AtomicTransactions)
* [Porównywano kolejki kolejek platformy Azure i usługi Service Bus](service-bus-azure-and-service-bus-queues-compared-contrasted.md)


