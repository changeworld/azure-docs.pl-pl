---
title: Azure Service Bus pobierania z wyprzedzeniem wiadomości | Dokumentacja firmy Microsoft
description: Zwiększanie wydajności dzięki odczyt z wyprzedzeniem komunikatów usługi Azure Service Bus.
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
ms.date: 01/30/2018
ms.author: sethm
ms.openlocfilehash: 0a61918108a48f4a9fa3d1c07cc8d41525f1f2a0
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/01/2018
ms.locfileid: "28928165"
---
# <a name="prefetch-azure-service-bus-messages"></a>Pobrana z wyprzedzeniem komunikatów usługi Azure Service Bus

Gdy *pobierania z wyprzedzeniem* jest włączona w żadnym z oficjalnego klientów usługi Service Bus odbiornika ciche uzyskuje więcej wiadomości do [PrefetchCount](/dotnet/api/microsoft.azure.servicebus.queueclient.prefetchcount#Microsoft_Azure_ServiceBus_QueueClient_PrefetchCount) limit poza co aplikacja początkowo wyświetlony monit o podanie.

Pojedynczy początkowego [Receive](/dotnet/api/microsoft.servicebus.messaging.queueclient.receive) lub [metody ReceiveAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.receiveasync) wywołanie w związku z tym uzyskuje wiadomości w celu natychmiastowego zużycia, która jest zwracana tak szybko, jak dostępne. Klient otrzymuje dalsze następnie komunikaty w tle, aby wypełnić w buforze pobierania z wyprzedzeniem.

## <a name="enable-prefetch"></a>Włącz pobieranie z wyprzedzeniem

Z platformą .NET, Włącz funkcję wyprzedzeniem przez ustawienie [PrefetchCount](/dotnet/api/microsoft.azure.servicebus.queueclient.prefetchcount#Microsoft_Azure_ServiceBus_QueueClient_PrefetchCount) właściwość **MessageReceiver**, **QueueClient**, lub **SubscriptionClient**  na większą niż zero. Ustawienie wartości zero spowoduje wyłączenie pobierania z wyprzedzeniem.

Możesz łatwo dodać to ustawienie po stronie odbioru z [QueuesGettingStarted](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/QueuesGettingStarted) lub [ReceiveLoop](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/ReceiveLoop) ustawienia próbek, aby zobaczyć efekt tych kontekstach.

Wiadomości są dostępne w buforze pobierania z wyprzedzeniem, wszystkie kolejne **Receive**/**metody ReceiveAsync** wywołania spełnione są natychmiast z buforu, a bufor jest uzupełniona w wraz ze wzrostem dostępności miejsca w tle. Jeśli w celu dostarczania nie ma dostępnych żadnych komunikatów, operacji odbierania opróżnia bufor, a następnie czeka lub bloków, zgodnie z oczekiwaniami.

Pobieranie z wyprzedzeniem działa także w taki sam sposób, z [OnMessage](/dotnet/api/microsoft.servicebus.messaging.queueclient.onmessage) i [OnMessageAsync](/dotnet/api/microsoft.servicebus.messaging.queueclient.onmessageasync) interfejsów API.

## <a name="if-it-is-faster-why-is-prefetch-not-the-default-option"></a>Jeśli jest szybsze, dlaczego pobierania z wyprzedzeniem nie jest opcją domyślną?

Pobieranie z wyprzedzeniem przyspiesza przepływ komunikatów przez komunikat łatwo dostępne do pobrania lokalnych po i przed aplikacja prosi o jeden. Przyrost ten przepływności jest wynikiem zależność, które autor aplikacji należy jawnie:

Z [ReceiveAndDelete](/dotnet/api/microsoft.azure.servicebus.receivemode.receiveanddelete) tryb odbierania, wszystkie komunikaty, które zostały nabyte w buforze pobierania z wyprzedzeniem nie będą już dostępne w kolejce, a tylko znajdują się w buforze pobierania z wyprzedzeniem w pamięci, dopóki nie zostaną odebrane do aplikacji za pomocą **odbierania**/**metody ReceiveAsync** lub **OnMessage**/**OnMessageAsync** interfejsów API. Jeśli aplikacja zakończy się przed komunikaty są odbierane w aplikacji, te komunikaty są bezpowrotnie utracona.

W [PeekLock](/dotnet/api/microsoft.azure.servicebus.receivemode.peeklock) tryb odbierania, wiadomości pobierane w buforze wyprzedzeniem drogą kupna w buforze w stanie zablokowanym, a ma limit czasu zegar materacy blokady. Jeśli w buforze pobierania z wyprzedzeniem jest duży, a przetwarzanie ma tak długo ten komunikat, że wygasanie blokad podczas znajdującej się w buforze pobierania z wyprzedzeniem, lub nawet wtedy, gdy aplikacja jest przetwarzanie komunikatu, może być niektóre zdarzenia mylące dla aplikacji do obsługi.

Aplikacja może uzyskać wiadomość z wygasły lub imminently wygasające blokady. Jeśli tak, aplikacja może przetworzyć komunikatu, ale następnie znajdź, że nie można wykonać go z powodu wygaśnięcia blokady. Aplikacja może sprawdzać [LockedUntilUtc](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.lockeduntilutc#Microsoft_Azure_ServiceBus_Core_MessageReceiver_LockedUntilUtc) właściwości (która podlega zegara między brokera i zegar komputera lokalnego). Po wygaśnięciu ważności blokady wiadomości w aplikacji należy zignorować ten komunikat; należy Brak wywołania interfejsu API na lub z komunikatem. Jeśli wiadomość nie utracił ważności, ale jest bliskie wygaśnięcia, blokady można odnowić i rozszerzać przez inny domyślny okres blokady przez wywołanie metody [wiadomości. RenewLock()](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.renewlockasync#Microsoft_Azure_ServiceBus_Core_MessageReceiver_RenewLockAsync_System_String_)

Jeśli blokady dyskretnie wygaśnie w buforze pobierania z wyprzedzeniem, wiadomość jest traktowana jako porzucone i ponownie ma zostać udostępnione do pobrania z kolejki. Który może spowodować, że plik do pobrania w buforze pobierania z wyprzedzeniem; umieszczane na końcu. Jeśli w buforze pobierania z wyprzedzeniem nie zwykle odbywać się w czasie wygaśnięcia komunikatu, powoduje, że wiadomości wielokrotnie prefetched, ale nigdy nie skutecznie dostarczonego można używać (licencjobiorcę) blokadę i po pewnym czasie zostaną przeniesione do kolejki utraconych wiadomości na raz Przekroczono dostarczania maksymalna liczba.

Jeśli potrzebują wysokiego stopnia niezawodności do przetwarzania komunikatów i przetwarzanie ma wiele pracy i godziny, zalecane jest korzystania z funkcji pobierania z wyprzedzeniem, konserwatywnie lub w ogóle.

Potrzebne są wysokiego w całym i przetwarzanie komunikatu jest często tanie, pobieranie z wyprzedzeniem powoduje przepływności znaczących korzyści.

Liczba maksymalna pobierania z wyprzedzeniem i czas trwania blokady skonfigurowany dla kolejki lub subskrypcji musi uwzględniać w taki sposób, że limit czasu blokady co najmniej przekracza zbiorczą oczekiwanego komunikatu przetwarzania czas maksymalny rozmiar buforu pobierania z wyprzedzeniem plus jeden komunikat. W tym samym czasie, przekroczenie limitu czasu blokady nie powinny być tak długo, że komunikaty może być dłuższa niż ich maksymalna [TimeToLive](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive) podczas przypadkowo są usuwane, w związku z tym wymagających ich blokady wygaśnie przed trwa przed przeniesieniem.

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej o komunikatów usługi Service Bus, zobacz następujące tematy:

* [Podstawy usługi Service Bus](service-bus-fundamentals-hybrid-solutions.md)
* [Kolejki, tematy i subskrypcje usługi Service Bus](service-bus-queues-topics-subscriptions.md)
* [Wprowadzenie do kolejek usługi Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Jak używać tematów i subskrypcji usługi Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)
