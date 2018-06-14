---
title: Azure sekwencjonowania komunikatów usługi Service Bus i sygnatury czasowe | Dokumentacja firmy Microsoft
description: Zachowaj sekwencji komunikatów usługi Service Bus i kolejność z sygnaturami czasowymi
services: service-bus-messaging
documentationcenter: ''
author: clemensv
manager: timlt
editor: ''
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2018
ms.author: sethm
ms.openlocfilehash: a67a9d01f686c6aa8a569239667ae14d7bf087a9
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2018
ms.locfileid: "28200738"
---
# <a name="message-sequencing-and-timestamps"></a>Komunikat sekwencjonowania i sygnatury czasowe

Sekwencjonowanie i sygnatura czasowa są dwie funkcje, które są zawsze włączona na wszystkich jednostek usługi Service Bus i powierzchni za pośrednictwem [SequenceNumber](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sequencenumber) i [EnqueuedTimeUtc](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.enqueuedtimeutc) właściwości odebranych lub przeglądanych komunikaty.

Dla tych przypadków, w których wiadomości bezwzględną kolejność jest znacząca i/lub w którym konsument wymagań zaufanego Unikatowy identyfikator komunikaty, komunikaty sygnatury broker z przerwę wolne, zwiększenie liczby sekwencji względem kolejka lub temat. Dla partycjonowane jednostki numer sekwencji jest wystawiony względem partycji.

**SequenceNumber** wartość jest unikatowy 64-bitową liczbę całkowitą przypisany do komunikatu jako zaakceptowaniu i przechowywane przez broker i funkcji jako swój identyfikator wewnętrzny. W przypadku partycjonowane jednostki najwyższego poziomu 16 bitów odzwierciedlają identyfikator partycji. Numery sekwencji przerzucane do zera po wyczerpaniu zakresu 48/x 64.

Numer sekwencji można ufać jako unikatowy identyfikator, ponieważ jest przypisany przez urząd centralnej i neutralnego, a nie przez klientów. On również reprezentuje kolejność true odbioru i jest bardziej dokładne niż sygnatura czasowa jako kryterium porządkowania, ponieważ sygnatury czasowe nie może mieć wystarczająco wysokiej rozdzielczości stawkami extreme wiadomości i mogą być regulowane (jednak minimalnego) zegara w sytuacjach, w którym brokera własność przejścia między węzłami.

Kolejność bezwzględną przyjęcia ma znaczenie, na przykład w firmie scenariusze, w których ograniczona liczba oferowanych towarów są udostępniane na podstawie pierwszego zgłoszeń podczas dostaw ostatnio; sprzedaży biletów porozumieniu są przykładem.

Możliwość stosowania sygnatur czasowych działa jako urząd neutralnych i godne zaufania, który przechwytuje dokładnie na czas UTC odbioru wiadomości, zostaną uwzględnione w **EnqueuedTimeUtc** właściwości. Wartość jest przydatne w przypadku scenariusza biznesowego zależy od terminy, takie jak czy elementu roboczego zostało przesłane w określonym dniu przed północy, ale przetwarzanie znajduje się do tej pory za zaległości kolejki.

## <a name="scheduled-messages"></a>Zaplanowane komunikaty

Można przesłać wiadomości do kolejki lub tematu do przetwarzania opóźnione; na przykład można zaplanować zadanie na udostępnienie do przetworzenia przez system w określonym czasie. Ta możliwość realizacji niezawodnej rozproszone na podstawie czasu harmonogramu.

Zaplanowane wiadomości nie zmaterializowania w kolejce do czasu zdefiniowanego umieścić w kolejce. Przed upływem tego czasu można anulować zaplanowanego wiadomości. Anulowanie usuwa komunikat.

Można zaplanować wiadomości, albo ustawiając [ScheduledEnqueueTimeUtc](/dotnet/api/microsoft.azure.servicebus.message.scheduledenqueuetimeutc) właściwości podczas wysyłania wiadomości na ścieżce wysyłania regularnych lub jawnie z [ScheduleMessageAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.schedulemessageasync#Microsoft_Azure_ServiceBus_QueueClient_ScheduleMessageAsync_Microsoft_Azure_ServiceBus_Message_System_DateTimeOffset_) interfejsu API. Drugie natychmiast zwraca komunikat zaplanowane **SequenceNumber**, którym później można anulować zaplanowanego komunikatu w razie potrzeby. Zaplanowane wiadomości i ich numerów sekwencji. można również odnaleźć za pomocą [przeglądania wiadomości](message-browsing.md).

**SequenceNumber** dla zaplanowanych wiadomości jest prawidłowe podczas tylko wiadomość jest w tym stanie. Jako wiadomości przejścia do stanu aktywnego, wiadomość jest dołączany do kolejki tak, jakby był umieszczonych w kolejce w bieżącej moment, w tym przypisywanie nowy **SequenceNumber**.

Ponieważ funkcja jest zakotwiczona na poszczególne wiadomości i komunikaty mogą być tylko umieszczonych w kolejce raz, usługi Service Bus nie obsługuje cyklicznych harmonogramów dla wiadomości.

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej o komunikatów usługi Service Bus, zobacz następujące tematy:

* [Podstawy usługi Service Bus](service-bus-fundamentals-hybrid-solutions.md)
* [Kolejki, tematy i subskrypcje usługi Service Bus](service-bus-queues-topics-subscriptions.md)
* [Wprowadzenie do kolejek usługi Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Jak używać tematów i subskrypcji usługi Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)