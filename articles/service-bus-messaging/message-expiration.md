---
title: Wygaśnięcia wiadomości usługi Azure Service Bus | Dokumentacja firmy Microsoft
description: Wygaśnięcia i czas wygaśnięcia wiadomości usługi Azure Service Bus
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
ms.date: 01/26/2018
ms.author: sethm
ms.openlocfilehash: 6e1f6177ccacf24955763982189bcdb1ef69c788
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2018
ms.locfileid: "28196772"
---
# <a name="message-expiration-time-to-live"></a>Wygaśnięcia wiadomości (czas wygaśnięcia)

Ładunek wewnątrz wiadomości, lub polecenie lub zapytanie przekazujący komunikat do odbiornika, prawie zawsze podlega jakiegoś termin wygaśnięcia na poziomie aplikacji. Po takich terminu już dostarczania zawartości lub jest już wykonać żądanej operacji.

Programowanie i środowisk testowych, w których kolejek i tematów są często używane w kontekście częściowe uruchomień aplikacji lub części aplikacji jest również pożądane dla wiadomości testowych Opuszczony standardowy będą automatycznie bezużytecznych tak, aby uruchomić Następny test może rozpoczynanie czyszczenia.

Wygaśnięcia wszelkie poszczególne wiadomości mogą kontrolowane przez ustawienie [TimeToLive](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive) właściwości systemu, który określa względny czas trwania. Po upływie staje się bezwzględne natychmiastowe, gdy komunikat jest dodawanych do kolejki w jednostce. W tym czasie [ExpiresAtUtc](/dotnet/api/microsoft.azure.servicebus.message.expiresatutc) właściwość przyjmuje wartość [(**EnqueuedTimeUtc**](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.enqueuedtimeutc#Microsoft_ServiceBus_Messaging_BrokeredMessage_EnqueuedTimeUtc) + [**TimeToLive**)](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive).

Ostatnie **ExpiresAtUtc** błyskawicznych, wiadomości kwalifikować się do pobrania. Czas wygaśnięcia nie wpływa na wiadomości, które są aktualnie zablokowane w celu dostarczania; te komunikaty nadal są zwykle obsługiwane. Jeśli blokady wygaśnie lub komunikat zostanie porzucony, po upływie obowiązuje bezpośrednim.

Gdy komunikat jest pod blokady, aplikacja może być w posiadaniu komunikat, który wygasł. Określa, czy chce hypervreplicavolumesize przetwarzania lub wybierze został porzucony komunikat aplikacji jest implementujący.

## <a name="entity-level-expiration"></a>Jednostki poziom ważności

Wszystkich wiadomości wysłanych do kolejki lub temat są poddawane domyślnie wygasają na jednostkę z poziomu [defaultMessageTimeToLive](/azure/templates/microsoft.servicebus/namespaces/queues) właściwości, które również należy ustawić w portalu podczas tworzenia i dostosowane później. Domyślny czas wygaśnięcia jest używany dla wszystkich wiadomości wysłanych do jednostki gdzie [TimeToLive](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive) nie jest jawnie ustawiona. Po upływie domyślnego działa również jako limitu dla **TimeToLive** wartość. Wiadomości, które mają dłuższe **TimeToLive** wygaśnięcia niż wartość domyślna dyskretnie są dostosowane do **defaultMessageTimeToLive** wartość przed umieszczonych w kolejce.

Opcjonalnie można przenieść wygasłych komunikatów do [kolejki utraconych wiadomości](service-bus-dead-letter-queues.md) przez ustawienie [EnableDeadLetteringOnMessageExpiration](/dotnet/api/microsoft.servicebus.messaging.queuedescription.enabledeadletteringonmessageexpiration#Microsoft_ServiceBus_Messaging_QueueDescription_EnableDeadLetteringOnMessageExpiration) właściwości lub zaznaczając pole wyboru odpowiednich w portalu. Jeśli opcja jest wyłączona, wygasłych komunikatów są usuwane. Komunikaty wygasłe przeniesiona do kolejki utraconych wiadomości można odróżnić od inne komunikaty lettered wiadomości w wyniku obliczenia [DeadletterReason](service-bus-dead-letter-queues.md#moving-messages-to-the-dlq) właściwość, która przechowuje brokera w sekcji właściwości użytkownika; wartość jest [TTLExpiredException](service-bus-dead-letter-queues.md#moving-messages-to-the-dlq) w takim przypadku.

W przypadku wyżej wymienione, w którym komunikat jest chronione przed wygaśnięcia podczas blokady i jeśli flaga jest ustawiona na jednostce wiadomość zostanie przeniesiona do kolejki utraconych wiadomości blokady zostanie porzucony lub wygasa. Jednak nie jest przenoszony Jeśli komunikat jest pomyślnie rozliczany, co przyjęto założenie, że aplikacja ma pomyślnie obsługiwane, pomimo nominalnego wygaśnięcia.

Kombinacja [TimeToLive](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive) i automatyczne (i transakcyjnych) Obsługa utraconych komunikatów po upływie są przydatnym narzędziem do ustanowienia zaufania do tego, czy zadanie do obsługi lub grupę programów obsługi, w obszarze terminu są pobierane dla Przetwarzanie jako termin zostanie osiągnięty.

Rozważmy na przykład witryny sieci web, który musi być niezawodnie wykonać zadania w wewnętrznej bazie danych ograniczonego skali i które czasami ruchu środowiska wzrósł lub chce być odizolowane od dostępności odcinki tego wewnętrznej bazy danych. W przypadku regularne obsługę po stronie serwera dane przesłane użytkownika wypycha dane do kolejki, a następnie otrzyma odpowiedź potwierdzenie pomyślnego obsługi transakcji do kolejki odpowiedzi. Brak kolekcji ruchu i obsługi w wewnętrznej bazie danych nie można przetworzyć jego elementów listy prac w czasie, wygasłych zadania są zwracane do kolejki utraconych wiadomości. Użytkownik interakcyjny można powiadamianych żądanej operacji potrwa nieco dłużej niż zwykle, czy żądanie można następnie znajdować się na innej kolejki dla ścieżki przetwarzania wysyłania wynik ostatecznego przetwarzania dla użytkownika za pośrednictwem poczty e-mail. 

## <a name="temporary-entities"></a>Tymczasowe jednostek

Kolejki usługi Service Bus, tematy i subskrypcje mogą być tworzone jako tymczasowy jednostek, które są automatycznie usuwane, gdy nie były używane w określonym okresie czasu.
 
Automatycznego oczyszczania jest przydatny do projektowania i testowania scenariuszy, w których podmioty są tworzone dynamicznie i nie są czyszczone po użyciu z powodu niektóre przerwom w wykonywaniu test lub uruchom debugowanie. Jest również przydatne w przypadku aplikacji tworzy dynamiczne jednostki, na przykład kolejki odpowiedzi, wysyłania odpowiedzi do procesu serwera sieci web lub do innego obiektu względnie krótkim okresie, w których trudno niezawodnie wyczyścić tych jednostek dla podczas obiektu wystąpienie zniknie.

Ta funkcja jest włączona, za pomocą [autoDeleteOnIdle](/azure/templates/microsoft.servicebus/namespaces/queues) właściwość, która jest ustawiona na czas trwania, dla którego jednostka musi być w stanie bezczynności (i nieużywanych) zanim zostanie automatycznie usunięty. Minimalny czas trwania wynosi 5 minut.
 
**AutoDeleteOnIdle** za pośrednictwem operacji usługi Azure Resource Manager lub za pomocą klienta programu .NET Framework, należy ustawić właściwość [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) interfejsów API. Nie można ustawić za pośrednictwem portalu.


## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej o komunikatów usługi Service Bus, zobacz następujące tematy:

* [Podstawy usługi Service Bus](service-bus-fundamentals-hybrid-solutions.md)
* [Kolejki, tematy i subskrypcje usługi Service Bus](service-bus-queues-topics-subscriptions.md)
* [Wprowadzenie do kolejek usługi Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Jak używać tematów i subskrypcji usługi Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)