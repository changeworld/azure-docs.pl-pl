---
title: Usługa Azure Service Bus — wygasanie komunikatów
description: W tym artykule wyjaśniono wygaśnięcia i czas do życia wiadomości usługi Azure Service Bus. Po upływie takiego terminu wiadomość nie jest już dostarczana.
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
ms.openlocfilehash: e86c92fa1cfb13929d5617502224f479709efdd3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76756338"
---
# <a name="message-expiration-time-to-live"></a>Wygaśnięcie komunikatu (czas wygaśnięcia)

Ładunek w wiadomości lub polecenia lub zapytania, które wiadomość przekazuje do odbiornika, prawie zawsze podlega jakiejś formie terminu wygaśnięcia na poziomie aplikacji. Po upływie takiego terminu zawartość nie jest już dostarczana lub żądana operacja nie jest już wykonywana.

W środowiskach deweloperskich i testowych, w których kolejki i tematy są często używane w kontekście częściowych przebiegów aplikacji lub części aplikacji, pożądane jest również automatyczne zbierania osieroconych komunikatów testowych, aby następne uruchomienie testowe było możliwe. rozpocząć czyszczenie.

Wygaśnięcia dla każdej pojedynczej wiadomości można kontrolować przez ustawienie [timetolive](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive) właściwości systemu, który określa względny czas trwania. Wygaśnięcie staje się bezwzględnym momentem, gdy wiadomość jest w kolejce do jednostki. W tym czasie [ExpiresAtUtc](/dotnet/api/microsoft.azure.servicebus.message.expiresatutc) właściwość przyjmuje na wartość [(**EnqueuedTimeUtc**](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.enqueuedtimeutc#Microsoft_ServiceBus_Messaging_BrokeredMessage_EnqueuedTimeUtc) + [**TimeToLive**).](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive) Ustawienie czasu wygaśnięcia (TTL) w wiadomości brokerskiej nie jest wymuszane, gdy nie ma żadnych klientów aktywnie nasłuchiwanie.

Po upływie funkcji instant **ExpiresAtUtc** wiadomości stają się niekwalifikowalne do pobierania. Wygaśnięcie nie ma wpływu na wiadomości, które są obecnie zablokowane do dostarczenia; te wiadomości są nadal obsługiwane normalnie. Jeśli blokada wygaśnie lub wiadomość zostanie porzucona, wygaśnięcie staje się natychmiastowe.

Gdy wiadomość jest w zamknięciu, aplikacja może być w posiadaniu wiadomości, która wygasła. Czy aplikacja jest gotowa do kontynuowania przetwarzania lub zdecyduje się porzucić wiadomość jest do implementera.

## <a name="entity-level-expiration"></a>Wygaśnięcie na poziomie jednostki

Wszystkie wiadomości wysyłane do kolejki lub tematu podlegają domyślnemu wygaśnięciu, który jest ustawiony na poziomie jednostki z [defaultMessageTimeToLive](/azure/templates/microsoft.servicebus/namespaces/queues) właściwości i które mogą być również ustawione w portalu podczas tworzenia i dostosowane później. Domyślny wygaśnięcia jest używany dla wszystkich wiadomości wysyłanych do jednostki, gdzie [TimeToLive](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive) nie jest jawnie ustawiona. Domyślny wygaśnięcia działa również jako pułap dla **timetolive** wartości. Wiadomości, które mają dłuższy **timetolive** wygaśnięcia niż wartość domyślna są dyskretnie dostosowane do **defaultMessageTimeToLive** wartość przed wejściem w kolejce.

> [!NOTE]
> Domyślną wartością [TimeToLive](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive) dla wiadomości brokerskiej jest [TimeSpan.Max,](https://docs.microsoft.com/dotnet/api/system.timespan.maxvalue) jeśli nie określono inaczej.
>
> W przypadku jednostek obsługi wiadomości (kolejek i tematów) domyślnym czasem wygaśnięcia jest również [timespan.max](https://docs.microsoft.com/dotnet/api/system.timespan.maxvalue) dla warstw standardowych usługi Service Bus i premium.  W przypadku warstwy podstawowej domyślny czas wygaśnięcia wynosi 14 dni.

Wygasłe wiadomości można opcjonalnie przenieść do [kolejki utraconych wiadomości,](service-bus-dead-letter-queues.md) ustawiając [właściwość EnableDeadLetteringOnMessageExpiration](/dotnet/api/microsoft.servicebus.messaging.queuedescription.enabledeadletteringonmessageexpiration#Microsoft_ServiceBus_Messaging_QueueDescription_EnableDeadLetteringOnMessageExpiration) lub zaznaczając odpowiednie pole w portalu. Jeśli opcja jest wyłączona, wygasłe wiadomości są odrzucane. Wygasłe wiadomości przeniesione do kolejki utraconych wiadomości można odróżnić od innych wiadomości utraconych liter, oceniając [DeadletterReason](service-bus-dead-letter-queues.md#moving-messages-to-the-dlq) właściwości, że broker magazyny w sekcji właściwości użytkownika; wartość jest [TTLExpiredException](service-bus-dead-letter-queues.md#moving-messages-to-the-dlq) w tym przypadku.

W wyżej wymienionym przypadku, w którym wiadomość jest chroniona przed wygaśnięciem, gdy znajduje się w blokadzie i jeśli flaga jest ustawiona na jednostce, wiadomość jest przenoszona do kolejki utraconych wiadomości, gdy blokada jest porzucona lub wygasa. Jednak nie jest przenoszony, jeśli wiadomość została pomyślnie rozliczona, która następnie zakłada, że aplikacja pomyślnie obsłużyła go, pomimo nominalnego wygaśnięcia.

Połączenie [TimeToLive](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive) i automatyczne (i transakcyjne) dead-lettering po wygaśnięciu są cennym narzędziem do ustanowienia zaufania, czy zadanie podane do obsługi lub grupy programów obsługi w terminie jest pobierana do przetwarzania po osiągnięciu terminu.

Rozważmy na przykład witryny sieci web, która musi niezawodnie wykonywać zadania na podstawie wewnętrznej bazy danych o ograniczonej skali i która od czasu do czasu doświadcza skoków ruchu lub chce być izolowana od odcinków dostępności tego zaplecza. W zwykłym przypadku program obsługi po stronie serwera dla przesłanych danych użytkownika wypycha informacje do kolejki, a następnie otrzymuje odpowiedź potwierdzającą pomyślną obsługę transakcji do kolejki odpowiedzi. Jeśli istnieje skok ruchu i obsługi wewnętrznej bazy danych nie można przetworzyć jego zapasów zaległości w czasie, wygasłe zadania są zwracane w kolejce utraconych wiadomości. Interaktywny użytkownik może zostać powiadomiony, że żądana operacja potrwa nieco dłużej niż zwykle, a żądanie można następnie umieścić w innej kolejce dla ścieżki przetwarzania, gdzie ostateczny wynik przetwarzania jest wysyłany do użytkownika pocztą e-mail. 


## <a name="temporary-entities"></a>Jednostki tymczasowe

Kolejki, tematy i subskrypcje usługi Service Bus można tworzyć jako jednostki tymczasowe, które są automatycznie usuwane, gdy nie były używane przez określony czas.
 
Automatyczne oczyszczanie jest przydatne w scenariuszach rozwoju i testowania, w których jednostki są tworzone dynamicznie i nie są czyszczone po użyciu, ze względu na pewne przerwy w przebiegu testu lub debugowania. Jest to również przydatne, gdy aplikacja tworzy jednostki dynamiczne, takie jak kolejka odpowiedzi, do odbierania odpowiedzi z powrotem do procesu serwera sieci web lub do innego obiektu o stosunkowo krótkotrwałym, gdzie trudno jest niezawodnie oczyścić te jednostki, gdy obiekt znika.

Funkcja jest włączona przy użyciu [właściwości autoDeleteOnIdle.](/azure/templates/microsoft.servicebus/namespaces/queues) Ta właściwość jest ustawiona na czas trwania, dla którego jednostka musi być bezczynny (nieużywane) przed jej automatyczne usunięcie. Minimalna wartość dla tej właściwości wynosi 5.
 
Właściwość **autoDeleteOnIdle** musi być ustawiona za pomocą operacji usługi Azure Resource Manager lub za pośrednictwem interfejsów API serwera nazw serwera .NET Framework [clientManager.](/dotnet/api/microsoft.servicebus.namespacemanager) Nie można go ustawić w portalu.

## <a name="idleness"></a>Lenistwa

Oto, co uważa za bezczynność jednostek (kolejki, tematy i subskrypcje):

- Kolejki
    - Brak wysyła  
    - Nie otrzymuje  
    - Brak aktualizacji kolejki  
    - Brak zaplanowanych wiadomości  
    - Brak przeglądania/wglądu 
- Tematy  
    - Brak wysyła  
    - Brak aktualizacji tematu  
    - Brak zaplanowanych wiadomości 
- Subskrypcje
    - Nie otrzymuje  
    - Brak aktualizacji subskrypcji  
    - Brak nowych reguł dodanych do subskrypcji  
    - Brak przeglądania/wglądu  
 


## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o wiadomościach usługi Service Bus, zobacz następujące tematy:

* [Kolejki, tematy i subskrypcje usługi Service Bus](service-bus-queues-topics-subscriptions.md)
* [Wprowadzenie do kolejek usługi Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Jak używać tematów i subskrypcji usługi Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)
