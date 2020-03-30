---
title: Sekwencjonowanie komunikatów usługi Azure Service Bus i sygnatury czasowe | Dokumenty firmy Microsoft
description: W tym artykule wyjaśniono, jak zachować sekwencjonowanie i zamawianie (z sygnaturami czasowymi) komunikatów usługi Azure Service Bus.
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
ms.openlocfilehash: 54d774c00fa650cb9608f46cc07b9d899709eaa5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79261660"
---
# <a name="message-sequencing-and-timestamps"></a>Sekwencjonowanie i sygnatury czasowe komunikatów

Sekwencjonowanie i sygnatura czasowa to dwie funkcje, które są zawsze włączone we wszystkich jednostkach usługi Service Bus i powierzchni za pomocą [sequenceNumber](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sequencenumber) i [EnqueuedTimeUtc](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.enqueuedtimeutc) właściwości odebranych lub przeglądanych wiadomości.

W przypadkach, w których bezwzględna kolejność wiadomości jest znacząca i/lub w których konsument potrzebuje wiarygodnego unikatowego identyfikatora wiadomości, broker stempluje wiadomości z numerem sekwencyjnym bez przerw, zwiększając numer sekwencyjny względem kolejki lub tematu. W przypadku jednostek podzielonych na partycje numer sekwencyjny jest wydawany względem partycji.

**SequenceNumber** Wartość jest unikatowy 64-bitowy liczba całkowita przypisana do wiadomości, ponieważ jest akceptowana i przechowywana przez brokera i działa jako jego identyfikator wewnętrzny. W przypadku jednostek podzielonych na partycje najwyższej liczby 16 bitów odzwierciedla identyfikator partycji. Numery sekwencji są przerzucone na zero po wyczerpaniu zakresu 48/64-bitowego.

Numer sekwencyjny można ufać jako unikatowy identyfikator, ponieważ jest on przypisany przez centralny i neutralny urząd, a nie przez klientów. Reprezentuje również prawdziwą kolejność przyjazdu i jest bardziej precyzyjny niż sygnatura czasowa jako kryterium zamówienia, ponieważ znaczniki czasu mogą nie mieć wystarczająco wysokiej rozdzielczości przy ekstremalnych szybkościach wiadomości i mogą podlegać (choćkolwiek minimalnym) pochyleniu zegara w sytuacjach, gdy broker przejścia własności między węzłami.

Bezwzględne zamówienie przyjazdu ma znaczenie, na przykład w scenariuszach biznesowych, w których ograniczona liczba oferowanych towarów jest obsługiwana na zasadzie "kto pierwszy, ten lepszy" do wyczerpania zapasów; sprzedaży biletów na koncerty.

Funkcja sygnatury czasowej działa jako neutralny i godny zaufania urząd, który dokładnie przechwytuje czas UTC nadejścia wiadomości, odzwierciedlone w **EnqueuedTimeUtc** właściwości. Wartość jest przydatna, jeśli scenariusz biznesowy zależy od terminów, takich jak czy element pracy został przesłany w określonym dniu przed północą, ale przetwarzanie jest daleko za zaległości kolejki.

## <a name="scheduled-messages"></a>Zaplanowane wiadomości

Komunikaty można przesyłać do kolejki lub tematu w celu wykonania opóźnionego przetwarzania, na przykład aby zaplanować udostępnienie zadania do przetwarzania przez system w określonym czasie. Ta funkcja realizuje niezawodny harmonogram rozproszony oparty na czasie.

Zaplanowane wiadomości nie zmaterializują się w kolejce, dopóki nie zostanie zdefiniowany czas w kolejce. Przed tym czasem zaplanowane wiadomości mogą zostać anulowane. Anulowanie usuwa wiadomość.

Można zaplanować wiadomości albo przez ustawienie [ScheduledEnqueueTimeUtc](/dotnet/api/microsoft.azure.servicebus.message.scheduledenqueuetimeutc) właściwość podczas wysyłania wiadomości za pośrednictwem zwykłej ścieżki wysyłania lub jawnie z [ScheduleMessageAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.schedulemessageasync#Microsoft_Azure_ServiceBus_QueueClient_ScheduleMessageAsync_Microsoft_Azure_ServiceBus_Message_System_DateTimeOffset_) INTERFEJSU API. Ten ostatni natychmiast zwraca numer **sekwencji**zaplanowanej wiadomości , który można później użyć do anulowania zaplanowanej wiadomości w razie potrzeby. Zaplanowane wiadomości i ich numery sekwencji można również odnajdyć za pomocą [przeglądania wiadomości](message-browsing.md).

**Numer SequenceNumber** dla zaplanowanej wiadomości jest prawidłowy tylko wtedy, gdy wiadomość jest w tym stanie. Gdy wiadomość przechodzi do stanu aktywnego, wiadomość jest dołączana do kolejki tak, jakby była umieszczana w kolejce w bieżącym momencie, co obejmuje przypisanie nowego **numeru sekwencji**.

Ponieważ funkcja jest zakotwiczona w poszczególnych wiadomości i wiadomości mogą być ujmowane w kolejce tylko raz, usługa Service Bus nie obsługuje cykliczne harmonogramy dla wiadomości.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o wiadomościach usługi Service Bus, zobacz następujące tematy:

* [Kolejki, tematy i subskrypcje usługi Service Bus](service-bus-queues-topics-subscriptions.md)
* [Wprowadzenie do kolejek usługi Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Jak używać tematów i subskrypcji usługi Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)