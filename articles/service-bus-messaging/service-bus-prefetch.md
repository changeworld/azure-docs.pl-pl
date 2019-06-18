---
title: Azure Service Bus pobieranie z wyprzedzeniem wiadomości | Dokumentacja firmy Microsoft
description: Poprawianie wydajności poprzez pobieranie z wyprzedzeniem komunikatów usługi Azure Service Bus.
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
ms.date: 08/30/2018
ms.author: aschhab
ms.openlocfilehash: c63e6bf66e4832a1a5b0b5e6fc3dfbbf02d1e490
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "62125852"
---
# <a name="prefetch-azure-service-bus-messages"></a>Pobrana z wyprzedzeniem komunikatów usługi Azure Service Bus

Podczas *pobieranie z wyprzedzeniem* jest włączona we wszystkich klientów usługi Service Bus, oficjalne odbiornika ciche uzyskuje więcej wiadomości do [PrefetchCount](/dotnet/api/microsoft.azure.servicebus.queueclient.prefetchcount#Microsoft_Azure_ServiceBus_QueueClient_PrefetchCount) Ogranicz poza co aplikacja początkowo o podanie.

Pojedynczy początkowego [Receive](/dotnet/api/microsoft.servicebus.messaging.queueclient.receive) lub [ReceiveAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.receiveasync) wywołania w związku z tym uzyskuje wiadomość do bezpośredniego użycia, zwracany tak szybko, jako dostępne. Klient otrzymuje dalsze następnie wiadomości w tle, aby wypełnić w buforze pobierania z wyprzedzeniem.

## <a name="enable-prefetch"></a>Włącz pobieranie z wyprzedzeniem

Przy użyciu platformy .NET, Włącz funkcję pobierania z wyprzedzeniem, ustawiając [PrefetchCount](/dotnet/api/microsoft.azure.servicebus.queueclient.prefetchcount#Microsoft_Azure_ServiceBus_QueueClient_PrefetchCount) właściwość **MessageReceiver**, **QueueClient**, lub **SubscriptionClient**  na liczbę większą niż zero. Ustawienie wartości zero spowoduje wyłączenie pobierania z wyprzedzeniem.

Możesz łatwo dodawać to ustawienie na stronie odbierającej z [QueuesGettingStarted](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/QueuesGettingStarted) lub [ReceiveLoop](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/ReceiveLoop) ustawienia próbek, aby zobaczyć efekt w tych kontekstach.

Komunikaty są dostępne w buforze pobierania z wyprzedzeniem, każda kolejna **Receive**/**ReceiveAsync** wywołania spełnione są natychmiast z bufora i buforu są uzupełniane w w tle jako miejsce staje się dostępna. Jeśli dostępnych żadnych komunikatów do dostarczania, operacji odbierania opróżnienie bufora, a następnie czeka lub bloków, zgodnie z oczekiwaniami.

Pobieranie z wyprzedzeniem działa również w ten sam sposób, korzystając z [OnMessage](/dotnet/api/microsoft.servicebus.messaging.queueclient.onmessage) i [OnMessageAsync](/dotnet/api/microsoft.servicebus.messaging.queueclient.onmessageasync) interfejsów API.

## <a name="if-it-is-faster-why-is-prefetch-not-the-default-option"></a>Jeśli jest szybsze, dlaczego pobieranie z wyprzedzeniem nie jest to opcja domyślna?

Pobieranie z wyprzedzeniem przyspiesza przepływ komunikatów przez o wiadomości, które są łatwo dostępne do pobierania lokalnego, podczas i przed aplikacja prosi o jeden. Ta przepustowość jest wynikiem kompromis, który autor aplikacji należy jawnie:

Za pomocą [ReceiveAndDelete](/dotnet/api/microsoft.servicebus.messaging.receivemode) tryb odbierania, wszystkie komunikaty uzyskiwanych drogą kupna w buforze pobierania z wyprzedzeniem nie są już dostępne w kolejce, a tylko znajdują się w buforze pobierania z wyprzedzeniem w pamięci, dopóki nie zostaną odebrane w aplikacji za pomocą **otrzymywać**/**ReceiveAsync** lub **OnMessage**/**OnMessageAsync** interfejsów API. Jeśli aplikacja zakończy się przed komunikaty są odbierane w aplikacji, te komunikaty zostaną bezpowrotnie utracone.

W [PeekLock](/dotnet/api/microsoft.servicebus.messaging.receivemode#Microsoft_ServiceBus_Messaging_ReceiveMode_PeekLock) tryb odbierania, komunikaty pobrane w buforze pobierania z wyprzedzeniem drogą kupna w buforze w stanie zablokowanym, a także mieć zegar limitu czasu odliczania blokady. Jeśli w buforze pobierania z wyprzedzeniem jest duża, a przetwarzanie trwa tak długo ten komunikat, że wygaśnięciem blokad podczas znajdujących się w buforze pobierania z wyprzedzeniem, lub nawet wtedy, gdy aplikacja jest przetwarzania komunikatu, może to być niektóre zdarzenia mylące dla aplikacji do obsługi.

Aplikacja może uzyskać komunikat z blokadą wygasły lub imminently wygasające. Jeśli tak, aplikacja może przetworzyć komunikatu, ale następnie znajdź, że nie można wykonać ją z powodu wygaśnięcia blokady. Aplikacja może sprawdzać, czy [LockedUntilUtc](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.lockeduntilutc) właściwości (która podlega zegara od broker i zegar komputera lokalnego). Po wygaśnięciu blokady komunikat aplikacji należy zignorować ten komunikat; Brak wywołania interfejsu API na lub z komunikatem należy uwzględnić. Jeśli komunikat jest ważny, ale czas wygaśnięcia jest bliska, blokady może być odnawiane i rozszerzone przez inny domyślny okres blokady przez wywołanie metody [wiadomości. RenewLock()](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.renewlockasync#Microsoft_Azure_ServiceBus_Core_MessageReceiver_RenewLockAsync_System_String_)

Jeśli blokada dyskretnie wygaśnie w buforze pobierania z wyprzedzeniem, komunikat jest traktowany jako porzucone i jest ponownie dostępne do pobrania z kolejki. Który może spowodować, że można pobrać w buforze pobierania z wyprzedzeniem; umieszczone na końcu. Jeśli w buforze pobierania z wyprzedzeniem nie zwykle odbywać się w czasie wygaśnięcia komunikatu, powoduje, że komunikaty, aby być wielokrotnie pobieranych z wyprzedzeniem, ale nigdy nie jest gwarantowane skutecznie się użytecznym stanie (identyfikatorom zablokowane) i po pewnym czasie zostaną przeniesione do kolejki utraconych wiadomości na raz Maksymalna liczba prób dostarczenia zostanie przekroczony.

Jeśli potrzebujesz wysokiego stopnia niezawodności do przetwarzania komunikatów, a przetwarzanie zajmuje dużo pracy i godziny, zalecane jest, skorzystać z funkcji pobierania z wyprzedzeniem, konserwatywnie lub wcale.

Jeśli potrzebujesz wysokiej przepływności i przetwarzania komunikatów jest powszechnie tanie, pobieranie z wyprzedzeniem daje przepływności znaczne korzyści.

Liczba maksymalna pobieranie z wyprzedzeniem i czasu trwania blokady skonfigurowanego w obrębie kolejki lub subskrypcji muszą być rozmieszczane w taki sposób, że limit czasu blokady co najmniej przekracza zbiorczą oczekiwanego komunikatu przetwarzania czas maksymalny rozmiar buforu pobierania z wyprzedzeniem, a także jeden komunikat. W tym samym czasie, limit czasu blokady nie powinny być tak długo, że liczba komunikatów może przekroczyć ich maksymalna [TimeToLive](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive) podczas przypadkowo są odrzucane, dlatego wymagającej wygaśnie przed trwa przed przeniesieniem ich blokady.

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat obsługi komunikatów usługi Service Bus, zobacz następujące tematy:

* [Kolejki, tematy i subskrypcje usługi Service Bus](service-bus-queues-topics-subscriptions.md)
* [Wprowadzenie do kolejek usługi Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Jak używać tematów i subskrypcji usługi Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)
