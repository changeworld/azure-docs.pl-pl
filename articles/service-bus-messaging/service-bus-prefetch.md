---
title: Komunikaty dotyczące przedfetchowania usługi Azure Service Bus | Dokumenty firmy Microsoft
description: Zwiększ wydajność, pobierając z pocztą wstępną komunikaty usługi Azure Service Bus. Wiadomości są łatwo dostępne do pobierania lokalnego, zanim aplikacja zażąda ich.
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
ms.openlocfilehash: 80717ab940d27e9bf108b3740309bcd7d71668fd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76760661"
---
# <a name="prefetch-azure-service-bus-messages"></a>Wiadomości usługi Azure Service Bus z prefetchem

Gdy *funkcja Prefetch* jest włączona w dowolnym oficjalnym kliencich usługi Service Bus, odbiorca po cichu uzyskuje więcej komunikatów, aż do limitu [PrefetchCount,](/dotnet/api/microsoft.azure.servicebus.queueclient.prefetchcount#Microsoft_Azure_ServiceBus_QueueClient_PrefetchCount) poza to, o co aplikacja początkowo poprosiła.

Pojedyncze początkowe [wywołanie Receive](/dotnet/api/microsoft.servicebus.messaging.queueclient.receive) lub [ReceiveAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.receiveasync) w związku z tym uzyskuje komunikat do natychmiastowego użycia, który jest zwracany tak szybko, jak to możliwe. Klient następnie uzyskuje dalsze komunikaty w tle, aby wypełnić buforu wstępnego.

## <a name="enable-prefetch"></a>Włącz pobieranie z fragmentu wstępnego

Za pomocą platformy .NET można włączyć funkcję Pobierania wstępnego, ustawiając właściwość [PrefetchCount](/dotnet/api/microsoft.azure.servicebus.queueclient.prefetchcount#Microsoft_Azure_ServiceBus_QueueClient_PrefetchCount) **messageReceiver**, **QueueClient**lub **SubscriptionClient** na liczbę większą niż zero. Ustawienie wartości na zero powoduje wyłączenie zwężania z fragmentu przedpremierowego.

Można łatwo dodać to ustawienie po stronie odbierania [queuesGettingStarted](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/QueuesGettingStarted) lub [ReceiveLoop](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/ReceiveLoop) przykłady ustawienia, aby zobaczyć efekt w tych kontekstach.

Gdy wiadomości są dostępne w buforze przedfetchem, wszystkie kolejne **wywołania Receive**/**ReceiveAsync** są natychmiast wypełniane z buforu, a bufor jest uzupełniany w tle, gdy miejsce staje się dostępne. Jeśli nie ma żadnych komunikatów dostępnych do dostarczenia, operacja odbierania opróżnia bufor, a następnie czeka lub blokuje, zgodnie z oczekiwaniami.

Prefetch działa również w ten sam sposób z interfejsami API [OnMessage](/dotnet/api/microsoft.servicebus.messaging.queueclient.onmessage) i [OnMessageAsync.](/dotnet/api/microsoft.servicebus.messaging.queueclient.onmessageasync)

## <a name="if-it-is-faster-why-is-prefetch-not-the-default-option"></a>Jeśli jest szybszy, dlaczego prefetch nie jest opcją domyślną?

Pobieranie wstępne przyspiesza przepływ wiadomości, dzięki że komunikat jest łatwo dostępny do pobierania lokalnego, gdy i przed aplikacją poprosi o jeden. Ten przyrost przepływności jest wynikiem kompromisu, który autor aplikacji musi wyraźnie:

W trybie odbierania [ReceiveAndDelete](/dotnet/api/microsoft.servicebus.messaging.receivemode) wszystkie komunikaty, które są pobierane do buforu pobierania wstępnego nie są już dostępne w kolejce i znajdują się tylko w buforze przedfetch w pamięci, dopóki nie zostaną odebrane do aplikacji za pośrednictwem **receive**/**receiveasync** lub**OnMessageAsync** **OnMessage**/interfejsów API. Jeśli aplikacja zostanie zakończona przed odebraniem wiadomości do aplikacji, te komunikaty zostaną nieodwracalnie utracone.

W trybie odbierania [PeekLock](/dotnet/api/microsoft.servicebus.messaging.receivemode#Microsoft_ServiceBus_Messaging_ReceiveMode_PeekLock) wiadomości pobrane do buforu pobierania wstępnego są pobierane do buforu w stanie zablokowanym i mają zegar limitu czasu dla tykania blokady. Jeśli bufor z wyprzedzeniem jest duży, a przetwarzanie trwa tak długo, że blokady wiadomości wygasają podczas przebywania w buforze przedbrań, a nawet podczas przetwarzania wiadomości przez aplikację, mogą wystąpić pewne mylące zdarzenia dla aplikacji do obsługi.

Aplikacja może uzyskać komunikat z wygasłą lub wkrótce wygasającą blokadą. Jeśli tak, aplikacja może przetworzyć komunikat, ale następnie okaże się, że nie może go ukończyć z powodu wygaśnięcia blokady. Aplikacja może sprawdzić [LockedUntilUtc](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.lockeduntilutc) właściwość (która podlega pochylenia zegara między brokera i lokalnego zegara maszyny). Jeśli blokada wiadomości wygasła, aplikacja musi zignorować komunikat; nie należy nawoływania do interfejsu API ani z komunikatem. Jeśli wiadomość nie wygasła, ale wygaśnięcie jest nieuchronne, blokada może zostać odnowiona i rozszerzona o inny domyślny okres blokady przez [wywołanie wiadomości. RenewLock()](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.renewlockasync#Microsoft_Azure_ServiceBus_Core_MessageReceiver_RenewLockAsync_System_String_)

Jeśli blokada dyskretnie wygaśnie w buforze pobierania wstępnego, wiadomość jest traktowana jako porzucona i jest ponownie udostępniana do pobrania z kolejki. To może spowodować, że do pobrania do buforu pobierania wstępnego; umieszczone na końcu. Jeśli bufor pobierania wstępnego zwykle nie można przepracować podczas wygasania wiadomości, powoduje to, że wiadomości są wielokrotnie z wyprzedzeniem, ale nigdy skutecznie dostarczane w stanie użytecznym (prawidłowo zablokowanym) i są ostatecznie przenoszone do kolejki utraconych wiadomości po maksymalna liczba dostaw zostanie przekroczona.

Jeśli potrzebujesz wysoki stopień niezawodności do przetwarzania wiadomości, a przetwarzanie zajmuje znaczną pracę i czas, zaleca się użycie funkcji wstępnego wprowadzania zachowawczo lub wcale.

Jeśli potrzebujesz wysokiej przepływności i przetwarzania wiadomości jest powszechnie tanie, prefetch daje znaczne korzyści przepływności.

Maksymalna liczba pobierania z wyprzedzeniem i czas trwania blokady skonfigurowany w kolejce lub subskrypcji muszą być zrównoważone w taki sposób, że limit czasu blokady co najmniej przekracza skumulowany oczekiwany czas przetwarzania wiadomości dla maksymalnego rozmiaru buforu pobierania zaliczki, plus jeden komunikat. W tym samym czasie limit czasu blokady nie powinien być tak długi, że wiadomości mogą przekraczać ich maksymalny [TimeToLive,](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive) gdy są przypadkowo upuszczone, co wymaga ich blokady wygaśnie przed ponownej realizacji.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o wiadomościach usługi Service Bus, zobacz następujące tematy:

* [Kolejki, tematy i subskrypcje usługi Service Bus](service-bus-queues-topics-subscriptions.md)
* [Wprowadzenie do kolejek usługi Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Jak używać tematów i subskrypcji usługi Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)
