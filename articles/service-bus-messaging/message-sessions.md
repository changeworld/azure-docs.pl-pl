---
title: Sesje komunikatów usługi Azure Service Bus | Dokumenty firmy Microsoft
description: W tym artykule wyjaśniono, jak używać sesji, aby włączyć wspólne i uporządkowane obsługi niepowiązanych sekwencji powiązanych komunikatów.
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
ms.openlocfilehash: 1e22641e9d4f9959c26cd2043ea2acd7e260e0f0
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81314057"
---
# <a name="message-sessions"></a>Sesje komunikatów
Sesje usługi Microsoft Azure Service Bus umożliwiają wspólne i uporządkowane obsługa niepowiązanych sekwencji powiązanych wiadomości. Sesje mogą być używane w pierwszym w, pierwszy na zewnątrz (FIFO) i wzorce żądania odpowiedzi. W tym artykule pokazano, jak używać sesji do implementowania tych wzorców podczas korzystania z usługi Service Bus. 

## <a name="first-in-first-out-fifo-pattern"></a>Wzór pierwszego w, pierwszego wyjścia (FIFO)
Aby zrealizować gwarancję FIFO w usłudze Service Bus, użyj sesji. Usługa Service Bus nie jest nakazowa co do charakteru relacji między wiadomościami, a także nie definiuje określonego modelu do określania, gdzie rozpoczyna się lub kończy sekwencję wiadomości.

> [!NOTE]
> Podstawowa warstwa usługi Service Bus nie obsługuje sesji. Sesje pomocy technicznej warstwy standardowej i premium. Aby uzyskać różnice między tymi warstwami, zobacz [Wycena usługi Service Bus](https://azure.microsoft.com/pricing/details/service-bus/).

Każdy nadawca może utworzyć sesję podczas przesyłania wiadomości do tematu lub kolejki, ustawiając [sessionid](/dotnet/api/microsoft.azure.servicebus.message.sessionid#Microsoft_Azure_ServiceBus_Message_SessionId) właściwość do niektórych identyfikatorów zdefiniowanych przez aplikację, który jest unikatowy dla sesji. Na poziomie protokołu AMQP 1.0 ta wartość jest mapowana na właściwość *identyfikatora grupy.*

W przypadku kolejek lub subskrypcji obsługujących sesję sesje powstają, gdy istnieje co najmniej jedna wiadomość z [identyfikatorem SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid#Microsoft_Azure_ServiceBus_Message_SessionId)sesji. Gdy sesja istnieje, nie ma zdefiniowanego czasu lub interfejsu API, gdy sesja wygasa lub znika. Teoretycznie wiadomość może być odebrana dla sesji dzisiaj, następna wiadomość w ciągu roku, a jeśli **SessionId** pasuje, sesja jest taka sama z punktu widzenia usługi Service Bus.

Zazwyczaj jednak aplikacja ma jasne pojęcie, gdzie zestaw powiązanych wiadomości rozpoczyna się i kończy. Usługa Service Bus nie ustawia żadnych określonych reguł.

Przykładem sposobu nakreślenia sekwencji przesyłania pliku jest ustawienie właściwości **Label** dla pierwszej wiadomości, która **ma się rozpocząć,** dla wiadomości pośrednich do **zawartości**i dla **zakończenia**ostatniej wiadomości. Względne położenie komunikatów zawartości można obliczyć jako bieżącą wiadomość *SequenceNumber* delta z komunikatu **początkowego** *SequenceNumber*.

Funkcja sesji w usłudze Service Bus umożliwia określoną operację odbierania w postaci [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) w interfejsach API języka C# i Java. Włącz tę funkcję, ustawiając właściwość [requiresSession](/azure/templates/microsoft.servicebus/namespaces/queues#property-values) w kolejce lub subskrypcji za pośrednictwem usługi Azure Resource Manager lub ustawiając flagę w portalu. Jest to wymagane przed podjęciem próby użycia powiązanych operacji interfejsu API.

W portalu ustaw flagę z następującym polem wyboru:

![][2]

> [!NOTE]
> Gdy sesje są włączone w kolejce lub subskrypcji, aplikacje klienckie nie mogą ***już*** wysyłać/odbierać zwykłych wiadomości. Wszystkie wiadomości muszą być wysyłane w ramach sesji (przez ustawienie identyfikatora sesji) i odbierane przez odbieranie sesji.

Interfejsy API dla sesji istnieją na klientach kolejkowych i subskrypcji. Istnieje model imperatywny, który kontroluje, gdy sesje i wiadomości są odbierane, a model oparty na programie obsługi, podobny do *OnMessage*, który ukrywa złożoność zarządzania pętli odbierania.

### <a name="session-features"></a>Funkcje sesji

Sesje zapewniają równoczesne odłączanie strumieni komunikatów z przeplotem przy jednoczesnym zachowaniu i zagwarantowaniu zamówionego dostarczania.

![][1]

Odbiornik [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) jest tworzony przez klienta akceptującego sesję. Klient wywołuje [QueueClient.AcceptMessageSession](/dotnet/api/microsoft.servicebus.messaging.queueclient.acceptmessagesession#Microsoft_ServiceBus_Messaging_QueueClient_AcceptMessageSession) lub [QueueClient.AcceptMessageSessionAsync](/dotnet/api/microsoft.servicebus.messaging.queueclient.acceptmessagesessionasync#Microsoft_ServiceBus_Messaging_QueueClient_AcceptMessageSessionAsync) w języku C#. W modelu wywołania zwrotnego reaktywnego rejestruje obsługi sesji.

Gdy [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) obiekt jest akceptowany i gdy jest w posiadaniu klienta, że klient posiada wyłączną blokadę na wszystkie wiadomości z [sessionid](/dotnet/api/microsoft.servicebus.messaging.messagesession.sessionid#Microsoft_ServiceBus_Messaging_MessageSession_SessionId) tej sesji, które istnieją w kolejce lub subskrypcji, a także na wszystkie wiadomości z tym **SessionId,** które nadal przychodzą podczas sesji jest utrzymywana.

Blokada jest zwalniana, gdy **close** lub **CloseAsync** są wywoływane lub gdy blokada wygasa w przypadkach, w których aplikacja nie jest w stanie wykonać operację zamknięcia. Blokada sesji powinna być traktowana jak blokada wyłączności w pliku, co oznacza, że aplikacja powinna zamknąć sesję, gdy tylko nie będzie już jej potrzebna i/lub nie oczekuje żadnych dalszych komunikatów.

Gdy wiele równoczesnych odbiorników ściągać z kolejki, wiadomości należące do określonej sesji są wywoływane do określonego odbiorcy, który obecnie posiada blokadę dla tej sesji. Dzięki tej operacji przeplot strumienia komunikatów w jednej kolejce lub subskrypcji jest czysto od multipleksowane do różnych odbiorników i tych odbiorników może również żyć na różnych komputerach klienckich, ponieważ zarządzanie blokadą odbywa się po stronie usługi, wewnątrz usługi Service Bus.

Na poprzedniej ilustracji przedstawiono trzy równoczesne odbiorniki sesji. Jedna sesja `SessionId` z = 4 nie ma aktywnego klienta, posiadającego, co oznacza, że żadne wiadomości nie są dostarczane z tej konkretnej sesji. Sesja działa na wiele sposobów, jak kolejka podrzędna.

Blokada sesji utrzymywana przez odbiornik sesji jest parasolem dla blokad komunikatów używanych w trybie rozliczania *zaglądania* do blokady. Odbiornik nie może mieć dwóch wiadomości jednocześnie "w locie", ale wiadomości muszą być przetwarzane w kolejności. Nową wiadomość można uzyskać tylko wtedy, gdy poprzednia wiadomość została ukończona lub martwa litera. Porzucenie wiadomości powoduje, że ten sam komunikat ma być ponownie obsługiwany przy następnej operacji odbierania.

### <a name="message-session-state"></a>Stan sesji wiadomości

Gdy przepływy pracy są przetwarzane w systemach chmury o wysokiej skali i wysokiej dostępności, program obsługi przepływu pracy skojarzony z określoną sesją musi być w stanie odzyskać po nieoczekiwanych błędach i może wznowić częściowo zakończoną pracę nad innym procesem lub komputerem, z którego rozpoczęto pracę.

Funkcja stanu sesji umożliwia zdefiniowaną przez aplikację adnotację sesji wiadomości wewnątrz brokera, dzięki czemu zarejestrowany stan przetwarzania względem tej sesji staje się natychmiast dostępny, gdy sesja zostanie przejęta przez nowy procesor.

Z punktu widzenia usługi Service Bus stan sesji wiadomości jest nieprzezroczystym obiektem binarnym, który może przechowywać dane o rozmiarze jednego komunikatu, który jest 256 KB dla standardu magistrali usług i 1 MB dla usługi Service Bus Premium. Stan przetwarzania względem sesji może być przechowywany wewnątrz stanu sesji lub stan sesji może wskazywać niektóre lokalizacji magazynu lub rekord bazy danych, który przechowuje takie informacje.

Interfejsy API do zarządzania stanem sesji, [SetState](/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate#Microsoft_ServiceBus_Messaging_MessageSession_SetState_System_IO_Stream_) i [GetState](/dotnet/api/microsoft.servicebus.messaging.messagesession.getstate#Microsoft_ServiceBus_Messaging_MessageSession_GetState), można znaleźć w [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) obiektu w interfejsach API języka C# i Java. Sesja, która wcześniej nie miała ustawionego stanu sesji zwraca **odwołanie null** dla **GetState**. Wyczyszczenie wcześniej ustawionego stanu sesji odbywa się za pomocą [SetState(null)](/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate#Microsoft_ServiceBus_Messaging_MessageSession_SetState_System_IO_Stream_).

Stan sesji pozostaje tak długo, jak nie jest wyczyszczone **(zwraca**null), nawet jeśli wszystkie wiadomości w sesji są używane.

Wszystkie istniejące sesje w kolejce lub subskrypcji można wyliczyć za pomocą **sessionbrowser** metody w interfejsie API Java i [GetMessageSessions](/dotnet/api/microsoft.servicebus.messaging.queueclient.getmessagesessions#Microsoft_ServiceBus_Messaging_QueueClient_GetMessageSessions) na [QueueClient](/dotnet/api/microsoft.servicebus.messaging.queueclient) i [SubscriptionClient](/dotnet/api/microsoft.servicebus.messaging.subscriptionclient) w kliencie .NET Framework.

Stan sesji przechowywany w kolejce lub w subskrypcji liczy się do przydziału magazynu tej jednostki. Po zakończeniu aplikacji z sesji, dlatego zaleca się dla aplikacji, aby oczyścić swój stan zatrzymany, aby uniknąć kosztów zarządzania zewnętrznego.

### <a name="impact-of-delivery-count"></a>Wpływ liczby dostaw

Definicja liczby dostarczania na wiadomość w kontekście sesji różni się nieznacznie od definicji w przypadku braku sesji. Oto tabela podsumowująca, kiedy liczba dostaw jest zwiększana.

| Scenariusz | Zwiększa liczbę dostarczenia wiadomości |
|----------|---------------------------------------------|
| Sesja jest akceptowana, ale blokada sesji wygasa (z powodu przeterminu) | Tak |
| Sesja jest akceptowana, wiadomości w ramach sesji nie są zakończone (nawet jeśli są zablokowane), a sesja jest zamknięta | Nie |
| Sesja jest akceptowana, wiadomości są wypełniane, a następnie sesja jest jawnie zamknięta | Nie dotyczy (Jest to standardowy przepływ. Tutaj wiadomości są usuwane z sesji) |

## <a name="request-response-pattern"></a>Wzorzec żądania odpowiedzi
[Wzorzec żądanie odpowiedź](https://www.enterpriseintegrationpatterns.com/patterns/messaging/RequestReply.html) jest dobrze ugruntowany wzorzec integracji, który umożliwia aplikacji nadawcy do wysyłania żądania i zapewnia sposób dla odbiorcy poprawnie wysłać odpowiedź z powrotem do aplikacji nadawcy. Ten wzorzec zazwyczaj potrzebuje kolejki krótkotrwałej lub tematu dla aplikacji do wysyłania odpowiedzi do. W tym scenariuszu sesje zapewniają proste rozwiązanie alternatywne z porównywalną semantyką. 

Wiele aplikacji może wysyłać swoje żądania do jednej kolejki żądań, z określonym parametrem nagłówka ustawionym na jednoznaczną identyfikację aplikacji nadawcy. Aplikacja odbiorcy może przetwarzać żądania przychodzące w kolejce i wysyłać odpowiedzi w kolejce z włączoną sesją, ustawiając identyfikator sesji na unikatowy identyfikator, który nadawca wysłał w wiadomości żądania. Aplikacja, która wysłała żądanie, może następnie odbierać wiadomości o określonym identyfikatorze sesji i poprawnie przetwarzać odpowiedzi.

> [!NOTE]
> Aplikacja, która wysyła początkowe żądania powinny wiedzieć `SessionClient.AcceptMessageSession(SessionID)` o identyfikatorze sesji i używać do blokowania sesji, na której oczekuje odpowiedzi. Dobrym pomysłem jest użycie identyfikatora GUID, który jednoznacznie identyfikuje wystąpienie aplikacji jako identyfikator sesji. Nie powinno być żadnego programu obsługi sesji lub `AcceptMessageSession(timeout)` w kolejce, aby upewnić się, że odpowiedzi są dostępne do zablokowania i przetworzenia przez określone odbiorniki.

## <a name="next-steps"></a>Następne kroki

- Zobacz [przykłady microsoft.Azure.ServiceBus](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/Sessions) lub [Microsoft.ServiceBus.Messaging przykłady,](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/Sessions) który używa klienta .NET Framework do obsługi wiadomości obsługujących sesję. 

Aby dowiedzieć się więcej o wiadomościach usługi Service Bus, zobacz następujące tematy:

* [Kolejki, tematy i subskrypcje usługi Service Bus](service-bus-queues-topics-subscriptions.md)
* [Wprowadzenie do kolejek usługi Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Jak używać tematów i subskrypcji usługi Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[1]: ./media/message-sessions/sessions.png
[2]: ./media/message-sessions/queue-sessions.png
