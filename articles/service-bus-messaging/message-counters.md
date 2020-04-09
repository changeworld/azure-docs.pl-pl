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
ms.date: 04/08/2020
ms.author: aschhab
ms.openlocfilehash: 8020b12ca892fbf7dec6fed6259526d958fb110f
ms.sourcegitcommit: df8b2c04ae4fc466b9875c7a2520da14beace222
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80891768"
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

> [!NOTE]
> Wiadomości, które są wysyłane do tematu usługi Service Bus są przekazywane do subskrypcji dla tego tematu. Tak więc liczba aktywnych wiadomości na temat jest 0, ponieważ te wiadomości zostały pomyślnie przekazane do subskrypcji. Pobierz liczbę wiadomości w ramach subskrypcji i sprawdź, czy jest większa niż 0. Mimo że wiadomości są widoczne w subskrypcji, są one faktycznie przechowywane w magazynie należącym do tematu. 

Jeśli spojrzysz na subskrypcje, to będą one miały niezerową liczbę wiadomości (które dodają do 323MB miejsca dla całej tej jednostki).

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o wiadomościach usługi Service Bus, zobacz następujące tematy:

* [Kolejki, tematy i subskrypcje usługi Service Bus](service-bus-queues-topics-subscriptions.md)
* [Wprowadzenie do kolejek usługi Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Jak używać tematów i subskrypcji usługi Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)
