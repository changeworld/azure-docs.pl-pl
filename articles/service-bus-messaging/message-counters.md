---
title: Usługa Azure Service Bus — liczba wiadomości
description: Pobieranie liczby wiadomości przechowywanych w kolejkach i subskrypcji przy użyciu usługi Azure Resource Manager i interfejsów API menedżera nazw usługi Azure Service BusManager.
services: service-bus-messaging
documentationcenter: ''
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2020
ms.author: aschhab
ms.openlocfilehash: 3a4fca0b3b60fcb76bcdc4f5f2d53df816c5053b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76756384"
---
# <a name="message-counters"></a>Liczniki komunikatów

Liczbę wiadomości przechowywanych w kolejkach i subskrypcjach można pobrać przy użyciu usługi Azure Resource Manager i interfejsów API [Menedżera nazw usługi](/dotnet/api/microsoft.servicebus.namespacemanager) Service Bus w pliku .NET Framework SDK.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Za pomocą programu PowerShell można uzyskać liczbę w następujący sposób:

```powershell
(Get-AzServiceBusQueue -ResourceGroup mygrp -NamespaceName myns -QueueName myqueue).CountDetails
```

## <a name="message-count-details"></a>Szczegóły liczby wiadomości

Znajomość liczby aktywnych wiadomości jest przydatna w określaniu, czy kolejka tworzy zaległości, które wymaga więcej zasobów do przetworzenia niż to, co zostało obecnie wdrożone. Następujące szczegóły licznika są dostępne w [MessageCountDetails](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails) klasy:

-   [ActiveMessageCount:](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.activemessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_ActiveMessageCount)Wiadomości w kolejce lub subskrypcji, które są w stanie aktywnym i gotowe do dostarczenia.
-   [DeadLetterMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.deadlettermessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_DeadLetterMessageCount): Wiadomości w kolejce utraconych wiadomości.
-   [ScheduledMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.scheduledmessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_ScheduledMessageCount): Wiadomości w zaplanowanym stanie.
-   [TransferDeadLetterMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.transferdeadlettermessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_TransferDeadLetterMessageCount): Wiadomości, które nie powiodło się przenieść do innej kolejki lub tematu i zostały przeniesione do kolejki utraconych wiadomości transferu.
-   [TransferMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.transfermessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_TransferMessageCount): Wiadomości oczekujące na przeniesienie do innej kolejki lub tematu.

Jeśli aplikacja chce skalować zasoby na podstawie długości kolejki, należy to zrobić z zmierzonym tempem. Nabycie liczników komunikatów jest kosztowną operacją wewnątrz brokera komunikatów, a wykonywanie jej często bezpośrednio i niekorzystnie wpływa na wydajność jednostki.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o wiadomościach usługi Service Bus, zobacz następujące tematy:

* [Kolejki, tematy i subskrypcje usługi Service Bus](service-bus-queues-topics-subscriptions.md)
* [Wprowadzenie do kolejek usługi Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Jak używać tematów i subskrypcji usługi Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)
