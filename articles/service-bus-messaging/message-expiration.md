---
title: Wygaśnięciu komunikatów w usłudze Azure Service Bus | Dokumentacja firmy Microsoft
description: Wygaśnięcia i godzina wygaśnięcia komunikatów usługi Azure Service Bus
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
ms.openlocfilehash: fdfd7794961b0254526b124525c6e978d13b0114
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65800274"
---
# <a name="message-expiration-time-to-live"></a>Wygaśnięcie komunikatu (czas wygaśnięcia)

Ładunek w wiadomości, lub polecenie lub zapytanie komunikat przekazywanych do odbiorcy przez prawie zawsze podlega pewnego rodzaju termin wygaśnięcia na poziomie aplikacji. Po takich terminu już dostarczania zawartości lub Żądana operacja nie jest wykonywany.

Środowiska projektowania i testowania w których kolejki i tematy są często używane w kontekście częściowe przebiegach aktualizacji aplikacji lub części aplikacji jest również pożądane dla wiadomości testowe opuszczone będą automatycznie bezużyteczne tak, aby uruchomić Następny test może rozpoczynanie czyszczenia.

Wygaśnięcie dla wszystkie poszczególne komunikaty mogą być kontrolowane przez ustawienie [TimeToLive](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive) właściwości systemu, który określa względne czasu trwania. Czas wygaśnięcia staje się natychmiastową bezwzględny, gdy komunikat jest umieszczonych w kolejce do jednostki. W tym czasie [ExpiresAtUtc](/dotnet/api/microsoft.azure.servicebus.message.expiresatutc) właściwość przyjmuje wartość [(**EnqueuedTimeUtc**](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.enqueuedtimeutc#Microsoft_ServiceBus_Messaging_BrokeredMessage_EnqueuedTimeUtc) + [**TimeToLive**)](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive). Ustawienie czasu wygaśnięcia (TTL), na komunikatu obsługiwanego przez brokera nie są wymuszane, gdy żaden klient nie jest aktywnie nasłuchuje.

Ostatnie **ExpiresAtUtc** błyskawiczne, wiadomości kwalifikować się do pobrania. Czas wygaśnięcia nie ma wpływu na wiadomości, które są aktualnie zablokowane w celu dostarczenia; te komunikaty są nadal obsługiwane normalnie. Czy wygaśnięciu blokady komunikat zostanie porzucony, po upływie ma natychmiastowy wpływ.

Natomiast wiadomość blokady, aplikacja może być w posiadaniu komunikat, który wygasł. Czy chce hypervreplicavolumesize przetwarzania lub wybiera można porzucić wiadomości w aplikacji zależy od implementujący.

## <a name="entity-level-expiration"></a>Jednostki poziomu ważności

Wszystkie komunikaty wysyłane do kolejki lub tematu jest zależna od wygaśnięcia domyślny, który jest ustawiony w jednostce poziom dzięki [defaultMessageTimeToLive](/azure/templates/microsoft.servicebus/namespaces/queues) właściwości, które również można ustawić w portalu podczas tworzenia i zmienić później. Domyślny czas wygaśnięcia jest używany dla wszystkich komunikatów wysłanych do jednostki gdzie [TimeToLive](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive) nie jest jawnie określona. Domyślny czas wygaśnięcia działa również jako pułap **TimeToLive** wartość. Wiadomości, które mają dłuższego **TimeToLive** wygaśnięcia niż domyślna wartość dyskretnie są dostosowywane do **defaultMessageTimeToLive** wartość przed umieszczonych w kolejce.

> [!NOTE]
> Wartość domyślna [TimeToLive](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive) wartość dla komunikatu obsługiwanego przez brokera jest [TimeSpan.Max](https://docs.microsoft.com/dotnet/api/system.timespan.maxvalue) , jeśli nie określono inaczej.
>
> Dla jednostki (kolejek i tematów) do obsługi komunikatów, domyślny czas wygaśnięcia jest również [TimeSpan.Max](https://docs.microsoft.com/dotnet/api/system.timespan.maxvalue) usługi Service Bus w warstwach standardowa i premium.  W przypadku warstwy basic domyślny czas wygaśnięcia to 14 dni.

Opcjonalnie można przenieść wygasłe wiadomości do [kolejki utraconych wiadomości](service-bus-dead-letter-queues.md) , ustawiając [EnableDeadLetteringOnMessageExpiration](/dotnet/api/microsoft.servicebus.messaging.queuedescription.enabledeadletteringonmessageexpiration#Microsoft_ServiceBus_Messaging_QueueDescription_EnableDeadLetteringOnMessageExpiration) właściwości lub zaznaczając odpowiednie pole wyboru w portalu. Jeśli opcja zostanie wyłączona, wygasłe wiadomości są porzucane. Wygasłe wiadomości przeniesiona do kolejki utraconych wiadomości można odróżnić od innych lettered Obsługa utraconych komunikatów poprzez ocenę [DeadletterReason](service-bus-dead-letter-queues.md#moving-messages-to-the-dlq) właściwość, która przez brokera są przechowywane w sekcji właściwości użytkownika; wartość [TTLExpiredException](service-bus-dead-letter-queues.md#moving-messages-to-the-dlq) w tym przypadku.

W przypadku wyżej, w którym komunikat jest chroniony przed wygaśnięcia podczas blokady i jeśli flaga jest ustawiona na jednostce wiadomość zostanie przeniesiona do kolejki utraconych wiadomości, jak blokadę zostaje porzucona lub wygaśnie. Jednak nie jest przenoszony Jeśli komunikat zostanie pomyślnie rozliczona, która zakłada się, czy aplikacja ma pomyślnie obsłużony, pomimo nominalna wygaśnięcia.

Kombinacja [TimeToLive](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive) i automatyczne (i transakcyjnych) Obsługa utraconych komunikatów po upływie są przydatnym narzędziem do ustanawiania zaufania do tego, czy zadanie do obsługi lub grupę programów obsługi w ramach terminu będą pobierane dla Przetwarzanie jako termin zostanie osiągnięty.

Na przykład należy wziąć pod uwagę witryny sieci web, który musi niezawodne wykonywanie zadań w wewnętrznej bazie danych ograniczonego skali, a od czasu do czasu gwałtowne wzrosty ruchu środowiska, lub chce być odizolowane od dostępności odcinki tego wewnętrznej bazy danych. W przypadku regularnego obsługi po stronie serwera dla danych użytkownika przesłane wypycha dane do kolejki i następnie otrzyma odpowiedź potwierdzenie pomyślnego obsługi transakcji do kolejki odpowiedzi. W przypadku nagły wzrost ruchu obsługi wewnętrznej bazy danych nie może przetworzyć jego elementów zaległości w czasie, wygasłych zadania są zwracane do kolejki utraconych wiadomości. Użytkownik interakcyjny, może zostać poinformowany, czy Żądana operacja zajmie trochę dłużej niż zwykle, a żądanie następnie można umieścić w kolejce różne ścieżki przetwarzania wysyłania wyniku przetwarzania ostatecznej użytkownikowi za pośrednictwem poczty e-mail. 


## <a name="temporary-entities"></a>Tymczasowe jednostek

Kolejki usługi Service Bus, tematy i subskrypcje, mogą być tworzone jako tymczasowy jednostki, które są automatycznie usuwane, gdy nie były używane przez określony czas.
 
Automatyczne oczyszczanie jest przydatne w scenariuszach tworzenia i testowania, w których podmioty są tworzone dynamicznie i nie są czyszczone po użyciu ze względu na pewne zakłóceń w działaniu testów lub debugowanie Uruchom. Jest również przydatna, jeśli aplikacja tworzy dynamiczne jednostek, takich jak kolejki odpowiedzi, do odbierania odpowiedzi z powrotem do procesu serwera sieci web lub do innego obiektu stosunkowo krótkotrwałe, gdzie jest trudny do niezawodnego wyczyścić te jednostki po obiektu wystąpienie zostanie usunięte.

Ta funkcja jest włączona, przy użyciu [autoDeleteOnIdle](/azure/templates/microsoft.servicebus/namespaces/queues) właściwości. Ta właściwość jest równa czas trwania, dla którego jednostki musi być bezczynne (nieużywane) zanim zostanie on automatycznie usunięty. Minimalna wartość tej właściwości to 5.
 
**AutoDeleteOnIdle** za pośrednictwem operacji usługi Azure Resource Manager lub przy użyciu klienta programu .NET Framework, należy ustawić właściwość [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) interfejsów API. Nie możesz ustawić w portalu.

## <a name="idleness"></a>Bezczynność

Oto, co uznawany za Bezczynność jednostek (kolejki, tematy i subskrypcje):

- Kolejki
    - Nie wysyła  
    - Nie odbiera  
    - Brak aktualizacji do kolejki  
    - Nie zaplanowanych wiadomości  
    - Przeglądaj/Odbierz 
- Tematy  
    - Nie wysyła  
    - Brak aktualizacji do tematu  
    - Nie zaplanowanych wiadomości 
- Subscriptions
    - Nie odbiera  
    - Brak aktualizacji do subskrypcji  
    - Nie nowych zasad dodane do subskrypcji  
    - Przeglądaj/Odbierz  
 


## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat obsługi komunikatów usługi Service Bus, zobacz następujące tematy:

* [Kolejki, tematy i subskrypcje usługi Service Bus](service-bus-queues-topics-subscriptions.md)
* [Wprowadzenie do kolejek usługi Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Jak używać tematów i subskrypcji usługi Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)