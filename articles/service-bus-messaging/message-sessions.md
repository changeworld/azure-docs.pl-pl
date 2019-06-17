---
title: Sesje komunikatów usługi Azure Service Bus | Dokumentacja firmy Microsoft
description: Obsługa sekwencji komunikatów usługi Azure Service Bus za pomocą sesji.
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
ms.openlocfilehash: 2c206d42e220534225cfef0415a65c1f9494f761
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64569796"
---
# <a name="message-sessions-first-in-first-out-fifo"></a>Sesje wiadomości: pierwszy, najpierw FIFO 

Sesje usługi Microsoft Azure Service Bus umożliwiają wspólnego i uporządkowanych obsługi niepowiązanych sekwencji pokrewnych komunikatów. Do osiągnięcia gwarancji FIFO w usłudze Service Bus, należy użyć sesji. Usługa Service Bus jest przetestowanego rozwiązania ze szczegółami o rodzaju relację między komunikaty i również nie definiuje określonego modelu do określania, gdzie rozpoczyna się lub kończy w sekwencji komunikatów.

> [!NOTE]
> Warstwa podstawowa usługi Service Bus nie obsługuje sesji. W warstwach standardowa i premium obsługuje sesji. Aby uzyskać więcej informacji, zobacz [cennika usługi Service Bus](https://azure.microsoft.com/pricing/details/service-bus/).

Wszystkich nadawców można utworzyć sesji podczas przesyłania komunikatów do tematu lub kolejki, ustawiając [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid#Microsoft_Azure_ServiceBus_Message_SessionId) właściwość niektórych zdefiniowanych przez aplikację Unikatowy identyfikator sesji. Na poziomie protokołu AMQP 1.0, ta wartość jest mapowany na *identyfikator grupy* właściwości.

Na subskrypcje lub kolejki obsługujących sesji, sesje rozpoczęciu istnienie po co najmniej jeden komunikat z sesją [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid#Microsoft_Azure_ServiceBus_Message_SessionId). Po sesji istnieje, nie ma zdefiniowanego czasu lub interfejsu API dla kiedy sesja wygaśnie lub zniknie. Teoretycznie komunikat może zostać odebrany sesji obecnie następnej wiadomości w czasie roku i w razie **SessionId** jest zgodny, sesja jest taka sama jak perspektywy usługi Service Bus.

Zwykle jednak aplikacja ma wyczyść pojęcie rozpoczyna się i kończy się zestaw pokrewne wiadomości. Usługa Service Bus nie ustawia wszystkie określone zasady.

Przykładowy sposób odróżnić sekwencji transferu plików jest można ustawić **etykiety** właściwości do pierwszej wiadomości **start**, pośrednie wiadomości do **zawartości**, i na ostatnich wiadomości **zakończenia**. Względne położenie zawartości komunikatów może zostać obliczony jako bieżący komunikat *SequenceNumber* delta **start** komunikat *SequenceNumber*.

Funkcja sesji usługi Service Bus umożliwia określonej operacji, odbioru w formie [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) w języku C# i interfejsy API języka Java. Włącz tę funkcję, ustawiając [element requiresSession](/azure/templates/microsoft.servicebus/namespaces/queues#property-values) właściwości kolejki lub subskrypcji, za pomocą usługi Azure Resource Manager lub przez ustawienie flagi w portalu. Jest to wymagane, przed podjęciem próby użycia powiązanych operacji interfejsu API.

W portalu Ustaw flagę poniższe pole wyboru:

![][2]

Interfejsy API dla sesji istnieje na klientach kolejki i subskrypcji. Istnieje imperatywne model, który określa, kiedy sesji i komunikaty są odbierane i na podstawie programu obsługi modelu, podobnie jak *OnMessage*, ukrywa złożoność zarządzania odbieranie pętli.

## <a name="session-features"></a>Funkcje sesji

Sesje zapewniają współbieżnych cofnąć Multipleksowanie strumieni komunikatów przeplotem gwarantując gwarantujących uporządkowane dostarczanie.

![][1]

A [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) odbiorcy jest tworzony przez klienta, akceptując sesji. Wywołania klienta [QueueClient.AcceptMessageSession](/dotnet/api/microsoft.servicebus.messaging.queueclient.acceptmessagesession#Microsoft_ServiceBus_Messaging_QueueClient_AcceptMessageSession) lub [QueueClient.AcceptMessageSessionAsync](/dotnet/api/microsoft.servicebus.messaging.queueclient.acceptmessagesessionasync#Microsoft_ServiceBus_Messaging_QueueClient_AcceptMessageSessionAsync) w języku C#. W modelu reaktywne wywołania zwrotnego rejestruje obsługi sesji.

Gdy [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) obiektu jest akceptowane i gdy odbywa się przez klienta, ten klient przechowuje blokady na wyłączność na wszystkie komunikaty z tej sesji [SessionId](/dotnet/api/microsoft.servicebus.messaging.messagesession.sessionid#Microsoft_ServiceBus_Messaging_MessageSession_SessionId) znajdujące się w kolejce lub subskrypcji, i również na wszystkie komunikaty z tym **SessionId** , nadal pojawić się podczas sesji jest przechowywany.

Blokada jest zwalniana podczas **Zamknij** lub **CloseAsync** są nazywane lub wygaśnięcia blokady w przypadkach, w których nie można wykonać operacji zamknięcia aplikacji. Blokady sesji powinien być traktowany jak blokady na wyłączność w pliku, co oznacza, że aplikacja powinna zamykać sesji tak szybko, jak go nie jest już konieczne i/lub nie oczekuje żadnych dalszych komunikatów.

Gdy wiele odbiorników współbieżnych ściąganie z kolejki, komunikaty należących do określonej sesji są wysyłane do określonego odbiornika, który obecnie posiada blokadę dla tej sesji. Przy użyciu tej operacji strumień komunikatu przeplotem znajdujących się w jednej kolejki lub subskrypcji nie pozostawia żadnych śladów jest cofnąć multipleksowany do różnych odbiorników i te odbiorniki można również na żywo na komputerach z innego klienta, ponieważ Zarządzanie blokadą się dzieje po stronie usługi, wewnątrz Usługa Service Bus.

Na poprzedniej ilustracji przedstawiono trzy odbiorniki równoczesnych sesji. Jedna sesja z `SessionId` = 4 nie ma żadnych aktywnych, będąca właścicielem klienta, co oznacza, że żadne komunikaty są dostarczane z tej określonej sesji. Sesja działa na wiele sposobów, takich jak kolejki podrzędnej.

Blokady sesji utrzymywane przez odbiornik sesji jest ogólny blokad komunikatów używane przez *Odbierz* tryb rozliczenia. Odbiorca nie może jednocześnie mieć dwa komunikaty "w locie", ale komunikaty muszą być przetwarzane w kolejności. Nowa wiadomość można uzyskać tylko po wykonaniu lub dead lettered poprzedniego komunikatu. Porzucenie komunikatu powoduje, że taka sama wiadomość ma zostać dostarczony ponownie przy następnej operacji odbioru.

## <a name="message-session-state"></a>Komunikat o stanie sesji

Jeśli przepływy pracy są przetwarzane w dużej skali, systemów w chmurze o wysokiej dostępności, Obsługa przepływu pracy skojarzonego z określonej sesji musi mieć możliwość sprawności po awarii i również mieć możliwość wznawiania pracy częściowo ukończone na inny proces lub komputera, z której rozpoczęła pracę.

Funkcji stan sesji umożliwia adnotacja zdefiniowanego przez aplikację sesji komunikatów wewnątrz brokera, tak aby stan przetwarzania zarejestrowane względem tej sesji, staje się natychmiast dostępna, gdy sesja jest uzyskiwany przez nowy procesor.

Z perspektywy usługi Service Bus komunikat stanu sesji jest nieprzezroczyste obiektu binarnego, który może przechowywać dane o rozmiarze jeden komunikat, czyli 256 KB magistrali usługi w wersji Standard i 1 MB dla usługi Service Bus w warstwie Premium. Stan przetwarzania względem sesji mogą być przechowywane w stan sesji lub stan sesji można wskazać do niektórych lokalizacji magazynu lub rekordu bazy danych, który zawiera takie informacje.

Interfejsy API zarządzania stanem sesji [SetState](/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate#Microsoft_ServiceBus_Messaging_MessageSession_SetState_System_IO_Stream_) i [GetState](/dotnet/api/microsoft.servicebus.messaging.messagesession.getstate#Microsoft_ServiceBus_Messaging_MessageSession_GetState), można znaleźć na [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) obiektu w języku C# i interfejsów API języka Java. Sesji, który miał poprzednio bez stanu sesji Ustaw zwraca **null** odwołanie dla **GetState**. Wyczyszczenie stanu sesji poprzednio ustawione jest przeprowadzane za pomocą [SetState(null)](/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate#Microsoft_ServiceBus_Messaging_MessageSession_SetState_System_IO_Stream_).

Należy pamiętać, że stan sesji pozostanie tak długo, jak nie jest czyszczony w (zwracanie **null**), nawet jeśli są używane wszystkie komunikaty w sesji.

Wszystkie istniejące sesje w kolejce lub subskrypcji mogą być wyliczane z **SessionBrowser** metoda w interfejsie API języka Java i za pomocą [GetMessageSessions](/dotnet/api/microsoft.servicebus.messaging.queueclient.getmessagesessions#Microsoft_ServiceBus_Messaging_QueueClient_GetMessageSessions) na [QueueClient](/dotnet/api/microsoft.azure.servicebus.queueclient) i [SubscriptionClient](/dotnet/api/microsoft.azure.servicebus.subscriptionclient) w kliencie programu .NET.

Stan sesji przechowywane w kolejce lub w, że subskrypcja liczy się do tej jednostki przydziału miejsca do magazynowania. Po zakończeniu działania aplikacji przy użyciu sesji w związku z tym zalecane jest dla aplikacji wyczyścić stanu zachowanego Aby uniknąć kosztów zarządzania zewnętrznego.

## <a name="next-steps"></a>Kolejne kroki

- Można znaleźć [przykłady Microsoft.Azure.ServiceBus](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/Sessions) lub [przykłady Microsoft.ServiceBus.Messaging](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/Sessions) przykład, które używa klienta .NET Framework do obsługi komunikatów obsługujących sesji. 

Aby dowiedzieć się więcej na temat obsługi komunikatów usługi Service Bus, zobacz następujące tematy:

* [Kolejki, tematy i subskrypcje usługi Service Bus](service-bus-queues-topics-subscriptions.md)
* [Wprowadzenie do kolejek usługi Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Jak używać tematów i subskrypcji usługi Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[1]: ./media/message-sessions/sessions.png
[2]: ./media/message-sessions/queue-sessions.png
