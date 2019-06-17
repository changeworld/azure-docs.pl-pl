---
title: Komunikaty wyjątków — usługa Azure Event Hubs | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera listę sugerowanych akcji i wyjątki obsługi komunikatów usługi Azure Event Hubs.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 7b6323e02225be3d954e4ee91ea06952bb3ce396
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66001764"
---
# <a name="event-hubs-messaging-exceptions"></a>Wyjątki obsługi komunikatów w usłudze Event Hubs

W tym artykule wymieniono niektóre wyjątki generowane przez usługi Azure Service Bus komunikatów interfejsu API biblioteki, w tym interfejsów API programu .NET Framework Event Hubs. Ta dokumentacja mogą ulec zmianie, więc sprawdzaj aktualizacje.

## <a name="exception-categories"></a>Kategorie wyjątków

Interfejsy API z centrów zdarzeń generować wyjątki, które może można podzielić na następujące kategorie, wraz ze skojarzonej akcji, które można wykonać, aby spróbować rozwiązać je.

1. Błąd kodowania użytkownika: [System.ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx), [System.InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx), [System.OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx), [System.Runtime.Serialization.SerializationException](https://msdn.microsoft.com/library/system.runtime.serialization.serializationexception.aspx). Ogólne działanie: Spróbuj naprawić kod zanim przejdziesz dalej.
2. Błąd instalacji/konfiguracji: [Microsoft.ServiceBus.Messaging.MessagingEntityNotFoundException](/dotnet/api/microsoft.servicebus.messaging.messagingentitynotfoundexception), [Microsoft.Azure.EventHubs.MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.eventhubs.messagingentitynotfoundexception), [System.UnauthorizedAccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx). Ogólne Akcja: Przejrzyj konfigurację i w razie potrzeby zmienić.
3. Przejściowych wyjątków: [Microsoft.ServiceBus.Messaging.MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception), [Microsoft.ServiceBus.Messaging.ServerBusyException](#serverbusyexception), [Microsoft.Azure.EventHubs.ServerBusyException](#serverbusyexception), [ Microsoft.ServiceBus.Messaging.MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception). Ogólne działanie: spróbuj ponownie wykonać operację lub powiadomić użytkowników.
4. Inne wyjątki: [System.Transactions.TransactionException](https://msdn.microsoft.com/library/system.transactions.transactionexception.aspx), [System.TimeoutException](#timeoutexception), [Microsoft.ServiceBus.Messaging.MessageLockLostException](/dotnet/api/microsoft.servicebus.messaging.messagelocklostexception), [ Microsoft.ServiceBus.Messaging.SessionLockLostException](/dotnet/api/microsoft.servicebus.messaging.sessionlocklostexception). Ogólne działanie: specyficzne dla typu wyjątku; Zapoznaj się z tabelą w sekcji poniżej. 

## <a name="exception-types"></a>Typy wyjątków
W poniższej tabeli wymieniono komunikatów typów wyjątków i powoduje, że oraz informacje o sugerowanej akcji, które można wykonać.

| Typ wyjątku | Opis elementu/Przyczyna/przykłady | Sugerowanej akcji | Należy zwrócić uwagę na automatyczne/natychmiastowe ponowienie próby |
| -------------- | -------------------------- | ---------------- | --------------------------------- |
| [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) |Serwer nie odpowiedział na żądanej operacji w określonym czasie, które są kontrolowane przez [OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings). Serwer ukończyć żądanej operacji. Ten wyjątek może się zdarzyć z powodu sieci lub innych infrastruktury opóźnienia. |Stan systemu w celu zachowania spójności i spróbuj ponownie, jeśli to konieczne.<br /> Zobacz [TimeoutException](#timeoutexception). | Ponów próbę, może pomóc w niektórych przypadkach; Dodaj logikę ponawiania próby do kodu. |
| [InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx) |Operacja żądanego użytkownika nie jest dozwolona w ramach serwera lub usługi. Wyświetlony komunikat o wyjątku, aby uzyskać szczegółowe informacje. Na przykład [Complete](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) generuje ten wyjątek, jeśli wiadomość została odebrana w [ReceiveAndDelete](/dotnet/api/microsoft.servicebus.messaging.receivemode) trybu. | Sprawdź kod i dokumentację. Upewnij się, że żądana operacja jest nieprawidłowa. | Nie pomoże ponownych prób. |
| [OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx) | Zostanie podjęta próba wywołania operacji na obiekcie, który już został zamknięty, przerwane lub usunięte. W rzadkich przypadkach otoczenia transakcji został już usunięty. | Sprawdź kod i upewnij się, że nie wywołuje operacje na zlikwidowanego obiektu. | Nie pomoże ponownych prób. |
| [Unauthorizedaccessexception —](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx) | [TokenProvider](/dotnet/api/microsoft.servicebus.tokenprovider) obiektu nie można uzyskać tokenu, token jest nieprawidłowy lub token zawiera oświadczenia, wymagane do wykonania tej operacji. | Upewnij się, że dostawcy tokenu, który jest tworzony przy użyciu prawidłowych wartości. Sprawdź konfigurację usługi kontroli dostępu. | Ponów próbę, może pomóc w niektórych przypadkach; Dodaj logikę ponawiania próby do kodu. |
| [ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx)<br /> [ArgumentNullException](https://msdn.microsoft.com/library/system.argumentnullexception.aspx)<br />[Trwa wyjątku ArgumentOutOfRangeException](https://msdn.microsoft.com/library/system.argumentoutofrangeexception.aspx) | Jeden lub więcej argumentów przekazana do metody są nieprawidłowe. Identyfikator URI dostarczane do [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) lub [Utwórz](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) zawiera ścieżkę i jej ewentualny związek. Dostarczony schemat identyfikatora URI, do [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) lub [Utwórz](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) jest nieprawidłowy. Wartość właściwości jest większa niż 32 KB. | Sprawdź kod wywołujący i upewnij się, że argumenty są poprawne. | Nie pomoże ponownych prób. |
| [Microsoft.ServiceBus.Messaging MessagingEntityNotFoundException](/dotnet/api/microsoft.servicebus.messaging.messagingentitynotfoundexception) <br /><br/> [Microsoft.Azure.EventHubs MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.eventhubs.messagingentitynotfoundexception) | Jednostka skojarzone z operacją nie istnieje lub został usunięty. | Upewnij się, że jednostka istnieje. | Nie pomoże ponownych prób. |
| [MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception) | Klient nie jest w stanie nawiązać połączenie z Centrum zdarzeń. |Upewnij się, że podana nazwa hosta jest poprawna, a host jest nieosiągalny. | Ponów próbę, może pomóc w przypadku sporadycznych problemów z łącznością. |
| [Microsoft.ServiceBus.Messaging ServerBusyException](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception) <br /> <br/>[Microsoft.Azure.EventHubs ServerBusyException](/dotnet/api/microsoft.azure.eventhubs.serverbusyexception) | Usługa nie jest w stanie przetworzyć żądania w tej chwili. | Klient może poczekaj przez pewien czas, a następnie spróbuj ponownie wykonać operację. <br /> Zobacz [ServerBusyException](#serverbusyexception). | Klient może podjąć kolejną próbę po upływie niektórych interwału. Jeśli ponowienie próby w wyniku inny wyjątek, sprawdź, ponów próbę zachowania tego wyjątku. |
| [MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception) | Ogólne komunikaty wyjątku, który może zostać wygenerowany w następujących przypadkach: Do utworzenia zostanie podjęta próba [QueueClient](/dotnet/api/microsoft.servicebus.messaging.queueclient) za pomocą nazwy lub ścieżki, który należy do innej jednostki typu (na przykład temat). Zostanie podjęta próba do wysyłania komunikatów jest większa niż 1 MB. Serwer lub Usługa napotkała błąd podczas przetwarzania żądania. Wyświetlony komunikat o wyjątku, aby uzyskać szczegółowe informacje. Wyjątek ten jest zazwyczaj wyjątku przejściowego. | Sprawdź kod i upewnij się, że są używane tylko obiekty możliwe do serializacji treści komunikatu lub użyć serializatora niestandardowego. Sprawdź w dokumentacji obsługiwane typy wartości właściwości i tylko użycie obsługiwane. Sprawdź [IsTransient](/dotnet/api/microsoft.servicebus.messaging.messagingexception) właściwości. Jeśli jest **true**, ponowić operację. | Sposób ponawiania jest niezdefiniowana i nie może pomóc. |
| [MessagingEntityAlreadyExistsException](/dotnet/api/microsoft.servicebus.messaging.messagingentityalreadyexistsexception) | Podjęto próbę utworzenia jednostki o nazwie, która jest już używana przez inną jednostkę w tej przestrzeni nazw usługi. | Usuwanie istniejącej jednostki lub wybierz inną nazwę dla obiektu, który ma zostać utworzony. | Nie pomoże ponownych prób. |
| [QuotaExceededException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) | Jednostki obsługi komunikatów osiągnęła maksymalny dozwolony rozmiar. Ten wyjątek może się zdarzyć, jeśli została już otwarta.%12%0 maksymalną liczbę odbiorców (czyli 5) na poziomie grupy-klient. | Utworzyć miejsca w jednostce otrzymywać wiadomości od jednostki lub jego podkolejek. <br /> Zobacz [QuotaExceededException](#quotaexceededexception) | Ponownych prób może pomóc, jeśli komunikaty zostały usunięte w tym samym czasie. |
| [MessagingEntityDisabledException](/dotnet/api/microsoft.servicebus.messaging.messagingentitydisabledexception) | Żądania dla operacji środowiska uruchomieniowego w jednostce wyłączone. |Aktywuj jednostki. | Ponownych prób może pomóc, jeśli jednostki został aktywowany w międzyczasie. |
| [Microsoft.ServiceBus.Messaging MessageSizeExceededException](/dotnet/api/microsoft.servicebus.messaging.messagesizeexceededexception) <br /><br/> [Microsoft.Azure.EventHubs MessageSizeExceededException](/dotnet/api/microsoft.azure.eventhubs.messagesizeexceededexception) | Ładunek komunikatu przekracza limit 1 MB. Ten limit 1 MB to łączna liczba komunikatu, który może zawierać właściwości systemu i wszystkimi narzutami .NET. | Zmniejsz rozmiar ładunku komunikatu, a następnie spróbuj ponownie wykonać operację. |Nie pomoże ponownych prób. |

## <a name="quotaexceededexception"></a>QuotaExceededException
[QuotaExceededException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) wskazuje, że przekroczony został limit przydziału dla określonej jednostki.

Ten wyjątek może się zdarzyć, jeśli została już otwarta.%12%0 maksymalną liczbę odbiorców (5) na poziomie grupy-klient.

### <a name="event-hubs"></a>Event Hubs
Usługa Event Hubs ma limit 20 grup odbiorców dla Centrum zdarzeń. Podczas próby utworzenia więcej, pojawia się [QuotaExceededException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception). 

## <a name="timeoutexception"></a>TimeoutException
A [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) wskazuje, że operacja zainicjowana przez użytkownika zajmuje więcej czasu niż limit czasu operacji. 

Dla usługi Event Hubs może zostać określony jako część ciągu połączenia lub za pośrednictwem [ServiceBusConnectionStringBuilder](/dotnet/api/microsoft.servicebus.servicebusconnectionstringbuilder). Komunikat o błędzie, sama może się różnić, ale zawsze zawiera wartość limitu czasu określony dla bieżącej operacji. 

### <a name="common-causes"></a>Typowe przyczyny
Istnieją dwie typowe przyczyny tego błędu: niepoprawnej konfiguracji lub Błąd przejściowy usługi.

1. **Nieprawidłowa konfiguracja** limit czasu operacji może być za mały dla warunki operacyjne. Domyślna wartość limitu czasu operacji w zestaw SDK klienta wynosi 60 sekund. Sprawdź, czy kod zawiera wartość równa coś, co jest za mały. Należy pamiętać, że stan sieci i użycie Procesora może mieć wpływ na czasu potrzebnego dla określonej operacji do wykonania, więc limit czasu operacji nie powinna być ustawiona małej wartości.
2. **Błąd przejściowy usługi** napotykać opóźnienia podczas przetwarzania żądań, na przykład w okresach dużego ruchu usługi Event Hubs. W takich przypadkach możesz ponowić próbę operacji po chwili, aż operacja się powiedzie. Jeśli w tej samej operacji nadal kończy się niepowodzeniem po wielu próbach, odwiedź stronę [witrynę ze stanem usługi Azure](https://azure.microsoft.com/status/) aby zobaczyć, czy istnieją jakiekolwiek znane awarie usług.

## <a name="serverbusyexception"></a>ServerBusyException

A [Microsoft.ServiceBus.Messaging.ServerBusyException](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception) lub [Microsoft.Azure.EventHubs.ServerBusyException](/dotnet/api/microsoft.azure.eventhubs.serverbusyexception) wskazuje, że serwer jest przeciążony. Istnieją dwa kodów błędów dla tego wyjątku.

### <a name="error-code-50002"></a>Kod błędu: 50002

Ten błąd może wystąpić jeden z dwóch powodów:

1. Obciążenie nie jest równomiernie rozłożona na wszystkie partycje w tym Centrum zdarzeń i ograniczenia dotyczące jednostek przepływności lokalnego uderza w jednej partycji.
    
    Rozwiązanie: Poprawianie strategią dystrybucji partycji lub podjęcie próby [EventHubClient.Send(eventDataWithOutPartitionKey)](/dotnet/api/microsoft.servicebus.messaging.eventhubclient) może pomóc.

2. Przestrzeń nazw usługi Event Hubs nie ma wystarczających jednostek przepływności (można sprawdzić **metryki** ekranu w zdarzeniu koncentratory okno przestrzeni nazw w [witryny Azure portal](https://portal.azure.com) o potwierdzenie). Portal zawiera informacje zagregowanych (1 minuta), ale mierzymy przepływność w czasie rzeczywistym —, więc tylko szacowania.

    Rozwiązanie: Zwiększenie jednostek przepływności w przestrzeni nazw może pomóc. Użytkownik może wykonać tę operację w portalu, w **skalowania** okna ekranu przestrzeni nazw usługi Event Hubs. Alternatywnie można użyć [automatyczne rozszerzanie](event-hubs-auto-inflate.md).

### <a name="error-code-50001"></a>Kod błędu: 50001

Ten błąd rzadko powinien wystąpić. Występuje, jeśli działanie kodu dla swojego obszaru nazw kontenera ma za mało procesora CPU — rozpoczyna się nie więcej niż kilka sekund, zanim moduł równoważenia obciążenia usługi Event Hubs.

### <a name="limit-on-calls-to-the-getruntimeinformation-method"></a>Limit wywołania getruntimeinformation — metoda
Usługa Azure Event Hubs obsługuje maksymalnie 50 wywołania na sekundę GetRuntimeInfo na sekundę. Po osiągnięciu tego limitu, może zostać wyświetlony podobny do następującego wyjątku:

```
ExceptionId: 00000000000-00000-0000-a48a-9c908fbe84f6-ServerBusyException: The request was terminated because the namespace 75248:aaa-default-eventhub-ns-prodb2b is being throttled. Error code : 50001. Please wait 10 seconds and try again.
```

## <a name="next-steps"></a>Kolejne kroki

Następujące linki pozwalają dowiedzieć się więcej na temat usługi Event Hubs:

* [Omówienie usługi Event Hubs](event-hubs-what-is-event-hubs.md)
* [Tworzenie centrum zdarzeń](event-hubs-create.md)
* [Event Hubs — często zadawane pytania](event-hubs-faq.md)
