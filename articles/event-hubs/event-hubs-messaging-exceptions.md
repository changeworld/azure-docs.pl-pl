---
title: Usługa Azure Event Hubs wiadomości wyjątki | Dokumentacja firmy Microsoft
description: Lista wyjątków i sugerowane rozwiązania do obsługi komunikatów usługi Azure Event hubs.
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: 2c6273de-0106-47e5-b45d-59040e51f2c5
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/19/2017
ms.author: sethm
ms.openlocfilehash: 964475ba8b42ac41707fa78468bfe551677c595f
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/20/2017
ms.locfileid: "26783132"
---
# <a name="event-hubs-messaging-exceptions"></a>Wyjątki obsługi komunikatów w usłudze Event Hubs

W tym artykule wymieniono niektóre wyjątki generowane przez biblioteki interfejsu API obsługi komunikatów usługi Azure Service Bus, które obejmują interfejsów API centrów zdarzeń. To odwołanie jest może ulec zmianie, dlatego sprawdzanie dostępności aktualizacji.

## <a name="exception-categories"></a>Kategorie wyjątku

Interfejsy API centra zdarzeń generowania wyjątków, które można podzielić na następujące kategorie, wraz ze skojarzonej akcji, które należy wykonać, aby je rozwiązać.

1. Kodowanie błąd użytkownika: [System.ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx), [System.InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx), [System.OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx), [ System.Runtime.Serialization.SerializationException](https://msdn.microsoft.com/library/system.runtime.serialization.serializationexception.aspx). Akcja ogólne: Spróbuj naprawić kod zanim przejdziesz dalej.
2. Błąd instalacji/konfiguracji: [Microsoft.ServiceBus.Messaging.MessagingEntityNotFoundException](/dotnet/api/microsoft.servicebus.messaging.messagingentitynotfoundexception), [Microsoft.Azure.EventHubs.MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.eventhubs.messagingentitynotfoundexception), [ System.UnauthorizedAccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx). Akcja ogólne: Przejrzyj konfigurację i w razie potrzeby zmień.
3. Wyjątki przejściowej: [Microsoft.ServiceBus.Messaging.MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception), [Microsoft.ServiceBus.Messaging.ServerBusyException](#serverbusyexception), [ Microsoft.Azure.EventHubs.ServerBusyException](#serverbusyexception), [Microsoft.ServiceBus.Messaging.MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception). Akcja ogólne: ponów operację lub powiadomić użytkowników.
4. Pozostałe wyjątki: [System.Transactions.TransactionException](https://msdn.microsoft.com/library/system.transactions.transactionexception.aspx), [System.TimeoutException](#timeoutexception), [Microsoft.ServiceBus.Messaging.MessageLockLostException](/dotnet/api/microsoft.servicebus.messaging.messagelocklostexception), [Microsoft.ServiceBus.Messaging.SessionLockLostException](/dotnet/api/microsoft.servicebus.messaging.sessionlocklostexception). Akcja ogólne: specyficzne dla typu wyjątku; Zapoznaj się z tabelą w następnej sekcji. 

## <a name="exception-types"></a>Typy wyjątków
W poniższej tabeli wymieniono obsługi typów wyjątków i ich przyczyny i zalecane działanie uwagi, które można wykonać.

| **Typ wyjątku** | **Opis elementu/Przyczyna/przykłady** | **Zalecane działanie** | **Należy zwrócić uwagę na automatyczne/natychmiastowego ponawiania** |
| --- | --- | --- | --- |
| [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) |Serwer nie odpowiedział na żądanej operacji w określonym czasie, które są kontrolowane przez [OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings#Microsoft_ServiceBus_Messaging_MessagingFactorySettings_OperationTimeout). Serwer mogło ukończyć żądanej operacji. Może to nastąpić z sieci lub innych opóźnienia infrastruktury. |Sprawdź stan systemu w celu zachowania spójności i ponów próbę, w razie potrzeby.<br /> Zobacz [TimeoutException](#timeoutexception). |Ponów próbę, może pomóc w niektórych przypadkach; Dodaj logikę ponawiania do kodu. |
| [InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx) |Operacja żądanego użytkownika nie jest dozwolona na serwerze lub usługi. Zawiera komunikat wyjątku, aby uzyskać szczegółowe informacje. Na przykład [Complete](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Complete) generuje ten wyjątek, jeśli wiadomość została odebrana w [ReceiveAndDelete](/dotnet/api/microsoft.servicebus.messaging.receivemode) tryb. |Sprawdź kod i dokumentację. Upewnij się, że żądana operacja jest nieprawidłowa. |Nie pomoże ponów próbę. |
| [Operationcanceledexception —](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx) |Podjęta próba wywołania operacji na obiekcie, który już został zamknięty, przerwana lub usunięty. W rzadkich przypadkach transakcja otoczenia zostało już usunięte. |Sprawdź kod i upewnij się, że nie jest wywoływany operacji na usuniętym obiekcie. |Nie pomoże ponów próbę. |
| [Unauthorizedaccessexception —](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx) |[TokenProvider](/dotnet/api/microsoft.servicebus.tokenprovider) obiektu nie można uzyskać tokenu, token jest nieprawidłowy lub token zawiera oświadczenia wymagane do wykonania tej operacji. |Upewnij się, że dostawca tokenu jest tworzony przy użyciu prawidłowych wartości. Sprawdź konfigurację usługi kontroli dostępu. |Ponów próbę, może pomóc w niektórych przypadkach; Dodaj logikę ponawiania do kodu. |
| [ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx)<br /> [Argumentnullexception —](https://msdn.microsoft.com/library/system.argumentnullexception.aspx)<br />[ArgumentOutOfRangeException](https://msdn.microsoft.com/library/system.argumentoutofrangeexception.aspx) |Co najmniej jeden argument przekazany do metody są nieprawidłowe. Identyfikator URI dostarczony do [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) lub [Utwórz](/dotnet/api/microsoft.servicebus.messaging.messagingfactory#Microsoft_ServiceBus_Messaging_MessagingFactory_Create_System_Collections_Generic_IEnumerable_System_Uri__) zawiera następującą liczbę segmentów ścieżki. Dostarczony schemat identyfikatora URI do [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) lub [Utwórz](/dotnet/api/microsoft.servicebus.messaging.messagingfactory#Microsoft_ServiceBus_Messaging_MessagingFactory_Create_System_Collections_Generic_IEnumerable_System_Uri__) jest nieprawidłowy. Wartość właściwości jest większy niż 32KB. |Sprawdź kod wywołujący i upewnij się, że argumenty są poprawne. |Nie pomoże ponów próbę. |
| [Microsoft.ServiceBus.Messaging.MessagingEntityNotFoundException](/dotnet/api/microsoft.servicebus.messaging.messagingentitynotfoundexception) <br /> [Microsoft.Azure.EventHubs.MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.eventhubs.messagingentitynotfoundexception) |Jednostka skojarzonych z operacją nie istnieje lub została usunięta. |Upewnij się, że istnieje jednostki. |Nie pomoże ponów próbę. |
| [MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception) |Klient nie będzie mógł nawiązać połączenie z Centrum zdarzeń. |Upewnij się, że nazwa podana hosta jest poprawna, a host jest dostępny. |Ponów próbę mogą ułatwić, jeśli występują problemy z łącznością sporadyczne. |
| [Microsoft.ServiceBus.Messaging.ServerBusyException](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception) <br /> [Microsoft.Azure.EventHubs.ServerBusyException](/dotnet/api/microsoft.azure.eventhubs.serverbusyexception) |Usługa nie będzie mógł przetworzyć żądania w tej chwili. |Klient może czekać przez czas, a następnie spróbuj ponownie wykonać operację. <br /> Zobacz [ServerBusyException](#serverbusyexception). |Klient może podjąć kolejną próbę po niektórych interwału. Jeśli ponowna próba powoduje różnych wyjątek, sprawdź zachowanie ponów próbę wykonania tego wyjątku. |
| [MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception) |Ogólny wiadomości wyjątek, który może zostać zgłoszony w następujących przypadkach: próby utworzenia [QueueClient](/dotnet/api/microsoft.servicebus.messaging.queueclient) za pomocą nazwy lub ścieżki, który należy do innej jednostki typu (na przykład temat). Aby wysłać wiadomość jest większy niż 256KB podejmowana jest próba. Serwer lub Usługa napotkała błąd podczas przetwarzania żądania. Sprawdź komunikat wyjątku, aby uzyskać szczegółowe informacje. Zwykle jest to przejściowy wyjątku. |Sprawdź kod i upewnij się, że są używane tylko obiekty możliwe do serializacji treści komunikatu lub użyć serializatora niestandardowego. Sprawdź dokumentację dla typów obsługiwaną wartością właściwości i tylko Użyj obsługiwane typy. Sprawdź [IsTransient](/dotnet/api/microsoft.servicebus.messaging.messagingexception#Microsoft_ServiceBus_Messaging_MessagingException_IsTransient) właściwości. Jeśli jest **true**, spróbuj ponownie wykonać operację. |Ponów próbę zachowanie nie jest zdefiniowana i nie może pomóc. |
| [MessagingEntityAlreadyExistsException](/dotnet/api/microsoft.servicebus.messaging.messagingentityalreadyexistsexception) |Podjęto próbę utworzenia jednostki o nazwie, która jest już używana przez inną jednostkę w tej przestrzeni nazw usługi. |Usuń istniejące jednostki lub wybierz inną nazwę dla obiektu, który ma zostać utworzony. |Nie pomoże ponów próbę. |
| [Quotaexceededexception —](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) |Jednostka obsługi komunikatów osiągnęła maksymalny dozwolony rozmiar. Może to nastąpić, jeśli maksymalną liczbę odbiorników (czyli 5) został już otwarty na poziomie grupy dla użytkownika. |Utworzyć miejsca w jednostce odbieranie komunikatów z jednostek lub jego podrzędne kolejek. <br /> Zobacz [quotaexceededexception —](#quotaexceededexception) |Ponów próbę mogą ułatwić czy wiadomości zostały usunięte w tym samym czasie. |
|  | | | |
| [MessagingEntityDisabledException](/dotnet/api/microsoft.servicebus.messaging.messagingentitydisabledexception) |Żądanie operacji środowiska uruchomieniowego w jednostce wyłączone. |Aktywuj jednostki. |Ponów próbę mogą ułatwić, gdy jednostka została uaktywniona w tymczasowych. |
| [Microsoft.ServiceBus.Messaging.MessageSizeExceededException](/dotnet/api/microsoft.servicebus.messaging.messagesizeexceededexception) <br /> [Microsoft.Azure.EventHubs.MessageSizeExceededException](/dotnet/api/microsoft.azure.eventhubs.messagesizeexceededexception) | Ładunek komunikatu przekracza limit 256 KB. Należy pamiętać, że limit 256 KB rozmiaru całkowita liczba wiadomości, który może zawierać żadnych czynności .NET i właściwości systemu. |Zmniejsz rozmiar ładunku komunikatu, a następnie spróbuj ponownie wykonać operację. |Nie pomoże ponów próbę. |

## <a name="quotaexceededexception"></a>Quotaexceededexception —
[Quotaexceededexception —](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) wskazuje, że przekroczono limit przydziału dla określonej jednostki.

Może to nastąpić, jeśli maksymalną liczbę odbiorników [5] został już otwarty na poziomie grupy dla użytkownika.

### <a name="event-hubs"></a>Event Hubs
Centra zdarzeń ma limit 20 grup odbiorców, na Centrum zdarzeń. Podczas próby utworzenia więcej, pojawi się [quotaexceededexception —](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception). 

## <a name="timeoutexception"></a>TimeoutException
A [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) wskazuje, że użytkownik zainicjował operację trwa dłużej niż limit czasu operacji. 

Usługi Event hubs, limit czasu jest określony jako część ciągu połączenia lub za pośrednictwem [ServiceBusConnectionStringBuilder](/dotnet/api/microsoft.servicebus.servicebusconnectionstringbuilder). Może się różnić się komunikat o błędzie, ale zawsze zawiera wartość limitu czasu określony w bieżącej operacji. 

### <a name="common-causes"></a>Typowe przyczyny
Istnieją dwie typowe przyczyny tego błędu: nieprawidłowa konfiguracja lub Błąd przejściowy usługi.

1. **Nieprawidłowa konfiguracja** limit czasu operacji może być za mała dla wartości warunki operacyjne. Domyślna wartość limitu czasu operacji na komputerze klienckim SDK wynosi 60 sekund. Sprawdź, czy kod ma wartość ustawioną coś za mały. Należy pamiętać, że stan sieci i użycie Procesora może wpływać na czas trwania dla określonej operacji zakończyć, więc limit czasu operacji nie powinien mieć ustawioną bardzo mała wartość.
2. **Błąd przejściowy usługi** czasami usługę Event Hubs mogą występować opóźnienia podczas przetwarzania żądań, na przykład w okresach dużego natężenia ruchu sieciowego. W takich przypadkach możesz ponowić operację z opóźnieniem, aż operacja się powiodła. Jeśli w tej samej operacji nadal kończy się niepowodzeniem po wielu próbach, odwiedź [lokacji stanu usługi Azure](https://azure.microsoft.com/status/) czy są wszystkie awarie znanej usługi.

## <a name="serverbusyexception"></a>ServerBusyException

A [Microsoft.ServiceBus.Messaging.ServerBusyException](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception) lub [Microsoft.Azure.EventHubs.ServerBusyException](/dotnet/api/microsoft.azure.eventhubs.serverbusyexception) wskazuje, że serwer jest przeciążony. Istnieją dwa kody błędów odpowiednie dla tego wyjątku.

### <a name="error-code-50002"></a>Kod błędu 50002

Ten błąd może wystąpić z jednej z dwóch przyczyn:

1. Obciążenia nie jest równomiernie rozłożona wszystkich partycji w Centrum zdarzeń i ograniczenie jednostki przepływności lokalnego trafienia jedną partycję.
    
    Rozwiązanie: Zmiana strategii dystrybucji partycji lub w trakcie [EventHubClient.Send(eventDataWithOutPartitionKey)](/dotnet/api/microsoft.servicebus.messaging.eventhubclient#Microsoft_ServiceBus_Messaging_EventHubClient_Send_Microsoft_ServiceBus_Messaging_EventData_) może pomóc.

2. Centra zdarzeń w przestrzeni nazw nie ma wystarczających jednostek przepływności (można sprawdzić **metryki** ekranu zdarzeń koncentratory okno przestrzeni nazw w [portalu Azure](https://portal.azure.com) o potwierdzenie). Należy pamiętać, portalu pokazuje zagregowane (1 minuta) informacje, że firma Microsoft mierzenia przepływności w czasie rzeczywistym — tak aby zawierała tylko szacowania.

    Rozwiązanie: Zwiększanie jednostek przepływności w przestrzeni nazw może pomóc. Można to zrobić w portalu, **skali** okna ekranu przestrzeni nazw usługi Event Hubs.

### <a name="error-code-50001"></a>Kod błędu 50001

Ten błąd powinny występować rzadko. Zdarza się, gdy kontener uruchamianie kodu przestrzeni nazw ma za mało procesora CPU — rozpoczyna się nie więcej niż kilka sekund, zanim usługa równoważenia obciążenia usługi Event Hubs.


## <a name="next-steps"></a>Kolejne kroki
Następujące linki pozwalają dowiedzieć się więcej na temat usługi Event Hubs:

* [Omówienie usługi Event Hubs](event-hubs-what-is-event-hubs.md)
* [Tworzenie centrum zdarzeń](event-hubs-create.md)
* [Event Hubs — często zadawane pytania](event-hubs-faq.md)
