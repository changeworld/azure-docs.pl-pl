---
title: Sesje komunikatów Azure Service Bus | Microsoft Docs
description: W tym artykule wyjaśniono, jak używać sesji, aby umożliwić wspólną i uporządkowaną obsługę niepowiązanych sekwencji powiązanych komunikatów.
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
ms.openlocfilehash: 0338663046c21adad358e8fddec12a3cc8151c79
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/26/2020
ms.locfileid: "76759333"
---
# <a name="message-sessions-first-in-first-out-fifo"></a>Sesje komunikatów: pierwszy w, pierwszy na zewnątrz (FIFO) 

Sesje Microsoft Azure Service Bus umożliwiają wspólną i uporządkowaną obsługę niepowiązanych sekwencji powiązanych komunikatów. Aby zrealizować gwarancję FIFO w Service Bus, użyj sesji. Service Bus nie jest zamieszczona w opisie natury relacji między komunikatami, a także nie definiuje określonego modelu do określania, gdzie przebiega lub kończąca się sekwencja komunikatów.

> [!NOTE]
> Warstwa Podstawowa Service Bus nie obsługuje sesji. W warstwach Standardowa i Premium obsługiwane są sesje. Aby uzyskać więcej informacji, zobacz [Cennik usługi Service Bus](https://azure.microsoft.com/pricing/details/service-bus/).

Każdy nadawca może utworzyć sesję podczas przesyłania komunikatów do tematu lub kolejki przez ustawienie właściwości [SessionID](/dotnet/api/microsoft.azure.servicebus.message.sessionid#Microsoft_Azure_ServiceBus_Message_SessionId) na określony identyfikator zdefiniowany przez aplikację, który jest unikatowy dla sesji. Na poziomie protokołu AMQP 1,0 ta wartość jest mapowana na Właściwość *Group-ID* .

W przypadku kolejek lub subskrypcji z obsługą sesji sesje są dostępne, gdy istnieje co najmniej jeden komunikat z sesją [sesji.](/dotnet/api/microsoft.azure.servicebus.message.sessionid#Microsoft_Azure_ServiceBus_Message_SessionId) Gdy sesja już istnieje, nie ma zdefiniowanego czasu ani interfejsu API dla momentu wygaśnięcia lub znikania sesji. Teoretycznie można odebrać komunikat dla sesji dzisiaj, następnej wiadomości w czasie roku i jeśli **Identyfikator sesji** jest zgodny, sesja jest taka sama w perspektywie Service Bus.

Zwykle jednak aplikacja ma jasne koncepcje, w której zestaw powiązanych komunikatów zaczyna się i kończy. Service Bus nie ustawi określonych reguł.

Przykładem sposobu odróżnić sekwencji transferu pliku jest ustawienie właściwości **etykieta** pierwszego komunikatu do **uruchomienia** **, w**przypadku komunikatów pośrednich i dla ostatniego komunikatu do **końca**. Względne położenie komunikatów zawartości można obliczyć jako bieżące *SequenceNumber* różnice **między komunikatami** z *SequenceNumber*.

Funkcja sesji w Service Bus włącza określoną operację odbierania w postaci [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) w interfejsach API C# i Java. Aby włączyć tę funkcję, należy ustawić właściwość [requiresSession](/azure/templates/microsoft.servicebus/namespaces/queues#property-values) w kolejce lub subskrypcji za pośrednictwem Azure Resource Manager lub ustawić flagę w portalu. Jest to wymagane przed podjęciem próby użycia powiązanych operacji interfejsu API.

W portalu Ustaw flagę przy użyciu następującego pola wyboru:

![][2]

> [!NOTE]
> Po włączeniu sesji dla kolejki lub subskrypcji aplikacje klienckie ***nie mogą już*** wysyłać/odbierać zwykłych komunikatów. Wszystkie komunikaty muszą być wysyłane w ramach sesji (przez ustawienie identyfikatora sesji) i odebrane przez odebranie sesji.

Interfejsy API dla sesji znajdują się na klientach w kolejkach i subskrypcjach. Istnieje model, który kontroluje czas odbierania sesji i komunikatów oraz model oparty na programie obsługi, podobny do *OnMessage*, który ukrywa złożoność zarządzania pętlą odbierania.

## <a name="session-features"></a>Funkcje sesji

Sesje umożliwiają jednoczesne usuwanie z przeplotu strumieni komunikatów podczas zachowywania i zagwarantowania uporządkowanego dostarczania.

![][1]

Odbiorca [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) jest tworzony przez klienta akceptującego sesję. Klient wywołuje [QueueClient. AcceptMessageSession](/dotnet/api/microsoft.servicebus.messaging.queueclient.acceptmessagesession#Microsoft_ServiceBus_Messaging_QueueClient_AcceptMessageSession) lub [QueueClient. AcceptMessageSessionAsync](/dotnet/api/microsoft.servicebus.messaging.queueclient.acceptmessagesessionasync#Microsoft_ServiceBus_Messaging_QueueClient_AcceptMessageSessionAsync) w C#. W reaktywnym modelu wywołania zwrotnego rejestruje procedurę obsługi sesji.

Gdy obiekt [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) zostanie zaakceptowany i gdy jest przechowywany przez klienta, klient ma zablokowaną blokadę dla wszystkich komunikatów z sesją [sesji, która istnieje](/dotnet/api/microsoft.servicebus.messaging.messagesession.sessionid#Microsoft_ServiceBus_Messaging_MessageSession_SessionId) w kolejce lub subskrypcji, a także na wszystkich komunikatach z tym **identyfikatorem SessionID** , który nadal dociera podczas sesji.

Blokada jest uwalniana, gdy wywoływana jest wartość **Close** lub **CloseAsync** lub gdy blokada wygaśnie w przypadkach, w których aplikacja nie może wykonać operacji zamknięcia. Blokada sesji powinna być traktowana jak blokada wyłączna dla pliku, co oznacza, że aplikacja powinna zamykać sesję, gdy tylko nie będą potrzebne, i/lub nie oczekuje żadnych dalszych komunikatów.

Gdy wiele współbieżnych odbiorników pobiera z kolejki, komunikaty należące do określonej sesji są wysyłane do określonego odbiornika, który aktualnie przechowuje blokadę dla danej sesji. W przypadku tej operacji, przechodzący strumień komunikatów znajdujący się w jednej kolejce lub subskrypcji jest usuwany z różnych odbiorników, a odbiorcy mogą również być aktywni na różnych komputerach klienckich, ponieważ zarządzanie blokadą odbywa się po stronie usługi, wewnątrz Service Bus.

Poprzednia ilustracja przedstawia trzy współbieżne odbiorniki sesji. Jedna sesja z `SessionId` = 4 nie ma aktywnego klienta będącego właścicielem, co oznacza, że żadne komunikaty nie są dostarczane z tej konkretnej sesji. Sesja działa na wiele sposobów, takich jak Kolejka podrzędna.

Blokada sesji zatrzymywana przez odbiorcę sesji to parasol dla blokad komunikatów używanych przez tryb rozliczania *blokady wglądu* . Odbiornik nie może jednocześnie korzystać z dwóch komunikatów "w locie", ale komunikaty muszą być przetwarzane w kolejności. Nowy komunikat można uzyskać tylko wtedy, gdy poprzedni komunikat został ukończony lub utracony. Porzucanie komunikatu powoduje ponowne obsłużynie tego samego komunikatu z następną operacją Receive.

## <a name="message-session-state"></a>Stan sesji komunikatu

Gdy przepływy pracy są przetwarzane w systemach w chmurze o wysokiej dostępności, obsługa przepływu pracy skojarzona z konkretną sesją musi mieć możliwość odzyskania po nieoczekiwanych awariach, a także umożliwia wznowienie częściowo ukończonej pracy w innym proces lub maszyna, z której rozpoczęto pracę.

Funkcja stanu sesji umożliwia zdefiniowaną przez aplikację adnotację sesji wiadomości wewnątrz brokera, dzięki czemu stan zapisanego przetwarzania względem tej sesji stanie się natychmiast dostępny po uzyskaniu sesji przez nowy procesor.

Z perspektywy Service Bus stan sesji jest nieprzezroczystym obiektem binarnym, który może przechowywać dane rozmiaru jednego komunikatu, czyli 256 KB dla Service Bus Standard i 1 MB dla Service Bus Premium. Stan przetwarzania odnoszący się do sesji może być przechowywany w stanie sesji lub stan sesji może wskazywać na część lokalizacji magazynu lub rekordu bazy danych, która zawiera takie informacje.

Interfejsy API do zarządzania stanem sesji, [setstate](/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate#Microsoft_ServiceBus_Messaging_MessageSession_SetState_System_IO_Stream_) i [GetState](/dotnet/api/microsoft.servicebus.messaging.messagesession.getstate#Microsoft_ServiceBus_Messaging_MessageSession_GetState)można znaleźć w obiekcie [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) w interfejsach API C# i Java. Sesja, która nie ma wcześniej zestawu stanu sesji, zwraca odwołanie o **wartości null** dla elementu **GetState**. Czyszczenie poprzednio ustawionego stanu sesji odbywa się z [ustawieniem setstate (null)](/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate#Microsoft_ServiceBus_Messaging_MessageSession_SetState_System_IO_Stream_).

Należy pamiętać, że stan sesji pozostaje, dopóki nie zostanie wyczyszczony (zwracając **wartość null**), nawet jeśli wszystkie komunikaty w sesji są używane.

Wszystkie istniejące sesje w kolejce lub subskrypcji można wyliczyć przy użyciu metody **SessionBrowser** w interfejsie API Java oraz z [GetMessageSessions](/dotnet/api/microsoft.servicebus.messaging.queueclient.getmessagesessions#Microsoft_ServiceBus_Messaging_QueueClient_GetMessageSessions) na [QueueClient](/dotnet/api/microsoft.azure.servicebus.queueclient) i [SubscriptionClient](/dotnet/api/microsoft.azure.servicebus.subscriptionclient) w kliencie .NET.

Stan sesji w kolejce lub w subskrypcji liczy się na przydział magazynu tego obiektu. Gdy aplikacja zostanie zakończona z sesją, zalecane jest, aby aplikacja mogła oczyścić stan zachowanych, aby uniknąć zewnętrznego kosztu zarządzania.

## <a name="impact-of-delivery-count"></a>Wpływ liczby dostaw

Definicja liczby dostaw na komunikat w kontekście sesji różni się nieco od definicji w przypadku braku sesji. Poniżej przedstawiono tabelę podsumowującą, kiedy licznik dostarczania jest zwiększany.

| Scenariusz | Czy liczba dostaw komunikatów jest zwiększana |
|----------|---------------------------------------------|
| Sesja została zaakceptowana, ale blokada sesji wygasa (z powodu przekroczenia limitu czasu) | Tak |
| Sesja zostanie zaakceptowana, komunikaty w sesji nie są kończone (nawet jeśli są zablokowane), a sesja jest ZAMKNIĘTA | Nie |
| Sesja została zaakceptowana, komunikaty są uzupełniane, a sesja jest jawnie ZAMKNIĘTA | Nie dotyczy (jest to standardowy przepływ. Komunikaty w tym miejscu są usuwane z sesji) |

## <a name="next-steps"></a>Następne kroki

- Zobacz przykłady [Microsoft. Azure. ServiceBus](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/Sessions) lub [Microsoft. ServiceBus. Messaging](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/Sessions) , aby zapoznać się z przykładem, który używa klienta .NET Framework do obsługi komunikatów obsługujących sesje. 

Aby dowiedzieć się więcej na temat Service Bus Messaging, zobacz następujące tematy:

* [Kolejki, tematy i subskrypcje usługi Service Bus](service-bus-queues-topics-subscriptions.md)
* [Wprowadzenie do kolejek usługi Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Jak używać tematów i subskrypcji usługi Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[1]: ./media/message-sessions/sessions.png
[2]: ./media/message-sessions/queue-sessions.png
