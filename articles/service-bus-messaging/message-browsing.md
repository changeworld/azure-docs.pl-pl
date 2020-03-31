---
title: Usługa Azure Service Bus — przeglądanie wiadomości
description: Przeglądaj i wgląduj do komunikatów usługi Service Bus umożliwia klientowi usługi Azure Service Bus wyliczenie wszystkich wiadomości, które znajdują się w kolejce lub subskrypcji.
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
ms.openlocfilehash: 6156557d10210535b287aa516070c0b5da416512
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77539369"
---
# <a name="message-browsing"></a>Przeglądanie komunikatów

Przeglądanie wiadomości lub wgląd umożliwia klientowi usługi Service Bus wyliczyć wszystkie wiadomości, które znajdują się w kolejce lub subskrypcji, zazwyczaj do celów diagnostycznych i debugowania.

Operacje wglądu zwracają wszystkie wiadomości, które istnieją w dzienniku komunikatów kolejki `Receive()` lub `OnMessage()` subskrypcji, nie tylko te dostępne do natychmiastowego nabycia z lub pętli. Właściwość `State` każdej wiadomości informuje, czy wiadomość jest aktywna (dostępna do odebrania), [odroczona](message-deferral.md)lub [zaplanowana.](message-sequencing.md)

Zużyte i wygasłe wiadomości są czyszczone przez asynchronisty "wyrzucania elementów bezużytecznych" uruchomić i niekoniecznie dokładnie po wygaśnięciu wiadomości i dlatego `Peek` może rzeczywiście zwrócić wiadomości, które już wygasły i zostaną usunięte lub utraconych liter, gdy operacja odbierania jest następnie wywoływane w kolejce lub subskrypcji.

Jest to szczególnie ważne, aby pamiętać podczas próby odzyskania odroczonych wiadomości z kolejki. Wiadomość, dla której [expiresatutc](/dotnet/api/microsoft.azure.servicebus.message.expiresatutc#Microsoft_Azure_ServiceBus_Message_ExpiresAtUtc) instant przeszedł nie jest już uprawniony do regularnego pobierania w jakikolwiek inny sposób, nawet wtedy, gdy jest zwracany przez Peek. Zwracanie tych komunikatów jest celowe, ponieważ Peek jest narzędziem diagnostycznym odzwierciedlającym bieżący stan dziennika.

Zaglądaj również zwraca wiadomości, które zostały zablokowane i są obecnie przetwarzane przez innych odbiorców, ale nie zostały jeszcze zakończone. Jednak ponieważ Peek zwraca rozłączony migawki, stan blokady wiadomości nie można obserwować na zaglądając wiadomości i [LockedUntilUtc](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.lockeduntilutc) i [LockToken](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.locktoken#Microsoft_Azure_ServiceBus_Message_SystemPropertiesCollection_LockToken) właściwości zgłosić [InvalidOperationException,](/dotnet/api/system.invalidoperationexception) gdy aplikacja próbuje je odczytać.

## <a name="peek-apis"></a>Interfejsy API wglądu

Metody [Peek/PeekAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.peekasync#Microsoft_Azure_ServiceBus_Core_MessageReceiver_PeekAsync) i [PeekBatch/PeekBatchAsync](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatchasync#Microsoft_ServiceBus_Messaging_QueueClient_PeekBatchAsync_System_Int64_System_Int32_) istnieją we wszystkich bibliotekach klienckich .NET i Java oraz we wszystkich obiektach odbiornika: **MessageReceiver**, **MessageSession**. Peek działa na wszystkich kolejkach i subskrypcjach oraz ich odpowiednich kolejkach utraconych wiadomości.

Po wywołaniu wielokrotnie, Peek metoda wylicza wszystkie komunikaty, które istnieją w kolejce lub dziennika subskrypcji, w kolejności numerów sekwencyjnych, od najniższego dostępnego numeru sekwencyjnego do najwyższego. Jest to kolejność, w której wiadomości były w kolejce i nie jest kolejność, w której wiadomości mogą być ostatecznie pobierane.

[PeekBatch](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatch#Microsoft_ServiceBus_Messaging_QueueClient_PeekBatch_System_Int32_) pobiera wiele wiadomości i zwraca je jako wyliczenie. Jeśli żadne komunikaty nie są dostępne, obiekt wyliczenia jest pusty, a nie null.

Można również seed przeciążenie metody z [SequenceNumber,](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.sequencenumber#Microsoft_Azure_ServiceBus_Message_SystemPropertiesCollection_SequenceNumber) w którym można uruchomić, a następnie wywołać przeciążenie metody bez parametrów, aby wyliczyć dalej. **PeekBatch** działa równoważnie, ale pobiera zestaw komunikatów na raz.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o wiadomościach usługi Service Bus, zobacz następujące tematy:

* [Kolejki, tematy i subskrypcje usługi Service Bus](service-bus-queues-topics-subscriptions.md)
* [Wprowadzenie do kolejek usługi Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Jak używać tematów i subskrypcji usługi Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)
