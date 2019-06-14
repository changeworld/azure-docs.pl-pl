---
title: Usługa Azure Przeglądanie komunikatów usługi Service Bus | Dokumentacja firmy Microsoft
description: Przeglądanie i wybieranie komunikatów usługi Service Bus
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
ms.date: 01/23/2019
ms.author: aschhab
ms.openlocfilehash: 425cf262b80e83a4d06074a567a2921eee12f9c2
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60402755"
---
# <a name="message-browsing"></a>Przeglądanie komunikatów

Przeglądanie komunikatów lub wgląd, umożliwia klientowi usługi Service Bus można wyliczyć wszystkie komunikaty, które znajdują się w kolejce lub subskrypcji, zazwyczaj w celach diagnostycznych i debugowania.

Operacje podglądu zwrócić wszystkie komunikaty, które istnieją w dzienniku komunikatów kolejki lub subskrypcji, nie tylko te dostępne do natychmiastowej pozyskiwaniem `Receive()` lub `OnMessage()` pętli. `State` Właściwość każdy komunikat informuje, czy komunikat jest aktywna (dostępne do odebrania) [odroczonego](message-deferral.md), lub [zaplanowane](message-sequencing.md).

Użyte i wygasłe komunikaty są czyszczone, asynchronicznego "wyrzucania elementów bezużytecznych" Uruchom i nie musi być dokładnie po komunikaty wygasają i w związku z tym `Peek` w rzeczywistości może zwrócić wiadomości, które już wygasł i zostanie usunięty lub gdy dead lettered Operacja odbioru obok jest wywoływana w obrębie kolejki lub subskrypcji.

Jest to szczególnie ważne, o których należy pamiętać podczas próby odzyskania odroczonej wiadomości z kolejki. Komunikat, dla którego [ExpiresAtUtc](/dotnet/api/microsoft.azure.servicebus.message.expiresatutc#Microsoft_Azure_ServiceBus_Message_ExpiresAtUtc) przeszła natychmiastowe nie jest już kwalifikuje się do regularnych pobieranie w inny sposób, nawet wtedy, gdy jest on zwracany przez wgląd. Zwracanie te komunikaty jest zamierzone, ponieważ podglądu to narzędzie diagnostyczne odzwierciedlenie aktualnego stanu dziennika.

Zobacz również: zwraca wiadomości, które zostały zablokowane są aktualnie przetwarzane przez inne odbiorniki, ale nie została jeszcze zakończona. Jednak ponieważ podglądu zwraca snapshot rozłączonych, stan blokady komunikatu nebyly nalezeny podejrzeć wiadomości, a [LockedUntilUtc](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.lockeduntilutc) i [LockToken](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.locktoken#Microsoft_Azure_ServiceBus_Message_SystemPropertiesCollection_LockToken) throw właściwości [ InvalidOperationException](/dotnet/api/system.invalidoperationexception) gdy aplikacja próbuje je odczytać.

## <a name="peek-apis"></a>Wgląd do interfejsów API

[Wglądu/PeekAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.peekasync#Microsoft_Azure_ServiceBus_Core_MessageReceiver_PeekAsync) i [PeekBatch/PeekBatchAsync](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatchasync#Microsoft_ServiceBus_Messaging_QueueClient_PeekBatchAsync_System_Int64_System_Int32_) metody istnieją w bibliotekach klienta wszystkich .NET, Java i na wszystkich obiektach odbiorcy: **MessageReceiver**, **MessageSession**, **QueueClient**, i **SubscriptionClient**. Wgląd w działania na wszystkich kolejek i subskrypcji oraz ich odpowiednich kolejki utraconych wiadomości.

Gdy wywołuje się wielokrotnie, metody Peek wylicza wszystkie komunikaty, które istnieją w dzienniku kolejki lub subskrypcji, w kolejności numer sekwencji, od najniższego dostępny numer porządkowy jako najwyższe. Tkwi w kolejności, w jakiej komunikaty zostały umieszczonych w kolejce, a nie kolejności, w której wiadomości po pewnym czasie mogą być pobierane.

[PeekBatch](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatch#Microsoft_ServiceBus_Messaging_QueueClient_PeekBatch_System_Int32_) pobiera wiele wiadomości i zwraca je jako wyliczenie. Jeśli nie ma wiadomości, obiekt wyliczenia jest pusta, nie ma wartości null.

Możesz również umieścić początkowo jest przeciążenie metody z [SequenceNumber](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.sequencenumber#Microsoft_Azure_ServiceBus_Message_SystemPropertiesCollection_SequenceNumber) w którym należy uruchomić, a następnie wywołać przeciążenie metody bez parametrów, można wyliczyć dalszych. **PeekBatch** ekwiwalentnie funkcji, ale jednocześnie pobiera zestaw komunikatów.

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat obsługi komunikatów usługi Service Bus, zobacz następujące tematy:

* [Kolejki, tematy i subskrypcje usługi Service Bus](service-bus-queues-topics-subscriptions.md)
* [Wprowadzenie do kolejek usługi Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Jak używać tematów i subskrypcji usługi Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)
