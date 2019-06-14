---
title: Usługa Azure Service Bus komunikat sekwencjonowanie i sygnatury czasowe | Dokumentacja firmy Microsoft
description: Zachowanie sekwencji komunikatów usługi Service Bus i kolejności z sygnaturami czasowymi
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
ms.openlocfilehash: 8665d0a1fccecf5521a553a894e2a55e52384ec3
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60402722"
---
# <a name="message-sequencing-and-timestamps"></a>Sekwencjonowanie i sygnatury czasowe komunikatów

Sekwencjonowanie i sygnatura czasowa to dwie funkcje, które są zawsze włączone dla wszystkich jednostek usługi Service Bus, a następnie publikować za pośrednictwem [SequenceNumber](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sequencenumber) i [EnqueuedTimeUtc](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.enqueuedtimeutc) właściwości odebranych lub przeglądanych komunikaty.

Dla tych przypadków, w których bezwzględne kolejność komunikatów jest ważna i/lub w którym użytkownik potrzebuje zaufanego Unikatowy identyfikator komunikaty, komunikaty sygnatury brokera z odstępu bez, zwiększenie numeru sekwencyjnego względem kolejki lub tematu. Partycjonowane jednostki, aby uzyskać numer sekwencyjny wystawiono względem partycji.

**SequenceNumber** wartość jest unikatowy 64-bitową liczbę całkowitą przypisany do wiadomości, jak jest akceptowane i przechowywane przez brokera i funkcje jako wewnętrzny identyfikator. W przypadku partycjonowane jednostki znajdujące się najwyżej 16 bitów odzwierciedlają identyfikatora partycji. Numery sekwencyjne przechodzą do zera po wyczerpaniu zakresu 48/64-bitowy.

Numer sekwencyjny może być zaufane jako unikatowy identyfikator, ponieważ jest przypisany przez urząd Środkowa i neutralne a nie przez klientów. Ponadto reprezentuje wartość true, kolejności przybycia i jest bardziej precyzyjne niż sygnatura czasowa, jako kryterium zamówienia, ponieważ sygnatury czasowe nie może mieć wystarczająco wysoka rozdzielczość stawek extreme wiadomości i mogą paść ofiarą (ale minimalne) zegara w sytuacjach, w którym brokera własność przejścia między węzłami.

Kolejności przyjazdu bezwzględna jest ważna, na przykład w firmie scenariusze, w których ograniczoną liczbę oferowanego segmentu żywności pakowanej są udostępniane na podstawie pierwszego zgłoszeń czasu wyczerpania ostatnio; Sprzedaż biletów koncert służą jako przykład.

Oznaczanie sygnaturą czasową możliwości działa jako urząd neutralne i godna zaufania, który przechwytuje dokładnego czasu UTC nadejścia wiadomości zostaną uwzględnione w **EnqueuedTimeUtc** właściwości. Wartość jest przydatne w przypadku scenariusza biznesowego, który jest zależny od terminy, takie jak tego, czy element roboczy został przesłany w określonym dniu wcześniejszą niż północ, ale przetwarzanie jest daleko w tyle zaległości kolejki.

## <a name="scheduled-messages"></a>Zaplanowane komunikaty

Możesz przesłać wiadomości do kolejki lub tematu do opóźnionego przetwarzania; na przykład, aby zaplanować zadanie, stanie się dostępny do przetworzenia przez system w określonym czasie. Ta funkcja zdaje sobie sprawę niezawodne rozproszonych harmonogramu opartego na czasie.

Zaplanowane komunikaty nie zmaterializowania w kolejce do czasu zdefiniowanego umieścić w kolejce. Przed upływem tego czasu można anulować zaplanowane wiadomości. Unieważnieniu usuwa komunikat.

Można zaplanować komunikaty, albo ustawiając [ScheduledEnqueueTimeUtc](/dotnet/api/microsoft.azure.servicebus.message.scheduledenqueuetimeutc) właściwości podczas wysyłania komunikatu za pośrednictwem ścieżki regularnego wysyłania lub jawnie przy użyciu [ScheduleMessageAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.schedulemessageasync#Microsoft_Azure_ServiceBus_QueueClient_ScheduleMessageAsync_Microsoft_Azure_ServiceBus_Message_System_DateTimeOffset_) interfejsu API. Te ostatnie natychmiast zwraca zaplanowanych wiadomości **SequenceNumber**, który mógł później użyć do anulowania zaplanowanych wiadomości, jeśli to konieczne. Zaplanowane wiadomości i ich numerów sekwencji można również znaleźć przy użyciu [Przeglądanie komunikatów](message-browsing.md).

**SequenceNumber** dla zaplanowanych wiadomości obowiązuje tylko podczas wiadomość znajduje się w tym stanie. Jako przejścia do wiadomości na stan aktywne wiadomości jest dołączany do kolejki tak, jakby była umieszczonych w kolejce u bieżącej moment, w tym Przypisywanie nowego **SequenceNumber**.

Ta funkcja jest zakotwiczona na poszczególne wiadomości, a komunikaty mogą być tylko umieszczonych w kolejce raz, dlatego dla komunikatów usługi Service Bus nie obsługuje harmonogramów cyklicznych.

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat obsługi komunikatów usługi Service Bus, zobacz następujące tematy:

* [Kolejki, tematy i subskrypcje usługi Service Bus](service-bus-queues-topics-subscriptions.md)
* [Wprowadzenie do kolejek usługi Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Jak używać tematów i subskrypcji usługi Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)