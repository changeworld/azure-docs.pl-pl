---
title: Wyjątki obsługi komunikatów usługi Azure Service Bus | Dokumentacja firmy Microsoft
description: Lista wyjątki obsługi komunikatów usługi Service Bus i sugerowane rozwiązania.
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.assetid: 3d8526fe-6e47-4119-9f3e-c56d916a98f9
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/21/2018
ms.author: aschhab
ms.openlocfilehash: b90e87310bf6dec505176b7f4d4cb9e15ac57c20
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60307781"
---
# <a name="service-bus-messaging-exceptions"></a>Wyjątki obsługi komunikatów w usłudze Service Bus
W tym artykule wymieniono niektóre wyjątki generowane przez Microsoft Azure Service Bus messaging API. Ta dokumentacja mogą ulec zmianie, więc sprawdzaj aktualizacje.

## <a name="exception-categories"></a>Kategorie wyjątków
Interfejsów API obsługi komunikatów generują wyjątki, które może można podzielić na następujące kategorie, wraz ze skojarzonej akcji, które można wykonać, aby spróbować rozwiązać je. Znaczenie i powoduje, że wyjątek może się różnić w zależności od typu jednostki obsługi komunikatów:

1. Błąd kodowania użytkownika ([System.ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx), [System.InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx), [System.OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx), [ System.Runtime.Serialization.SerializationException](https://msdn.microsoft.com/library/system.runtime.serialization.serializationexception.aspx)). Ogólne działanie: Spróbuj naprawić kod zanim przejdziesz dalej.
2. Błąd instalacji/konfiguracji ([Microsoft.ServiceBus.Messaging.MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.servicebus.messagingentitynotfoundexception), [System.UnauthorizedAccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx). Ogólne Akcja: Przejrzyj konfigurację i w razie potrzeby zmienić.
3. Przejściowych wyjątków ([Microsoft.ServiceBus.Messaging.MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception), [Microsoft.ServiceBus.Messaging.ServerBusyException](/dotnet/api/microsoft.azure.servicebus.serverbusyexception), [ Microsoft.ServiceBus.Messaging.MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception)). Ogólne działanie: spróbuj ponownie wykonać operację lub powiadomić użytkowników. Należy pamiętać, że `RetryPolicy` klasy w zestaw SDK klienta można skonfigurować w celu obsługi ponowień automatycznie. Zobacz [wskazówki dotyczące ponawiania prób](/azure/architecture/best-practices/retry-service-specific#service-bus) Aby uzyskać więcej informacji.
4. Inne wyjątki ([System.Transactions.TransactionException](https://msdn.microsoft.com/library/system.transactions.transactionexception.aspx), [System.TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx), [Microsoft.ServiceBus.Messaging.MessageLockLostException](/dotnet/api/microsoft.azure.servicebus.messagelocklostexception), [Microsoft.ServiceBus.Messaging.SessionLockLostException](/dotnet/api/microsoft.azure.servicebus.sessionlocklostexception)). Ogólne działanie: specyficzne dla typu wyjątku; Zapoznaj się z tabelą w sekcji poniżej: 

## <a name="exception-types"></a>Typy wyjątków
W poniższej tabeli wymieniono komunikatów typów wyjątków i powoduje, że oraz informacje o sugerowanej akcji, które można wykonać.

| **Typ wyjątku** | **Opis elementu/Przyczyna/przykłady** | **Sugerowanej akcji** | **Należy zwrócić uwagę na automatyczne/natychmiastowe ponowienie próby** |
| --- | --- | --- | --- |
| [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) |Serwer nie odpowiedział na żądanej operacji w określonym czasie, które są kontrolowane przez [OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings). Serwer ukończyć żądanej operacji. Może to nastąpić z sieci lub innych infrastruktury opóźnienia. |Stan systemu w celu zachowania spójności i spróbuj ponownie, jeśli to konieczne. Zobacz [wyjątków przekroczenia limitu czasu](#timeoutexception). |Ponów próbę, może pomóc w niektórych przypadkach; Dodaj logikę ponawiania próby do kodu. |
| [InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx) |Operacja żądanego użytkownika nie jest dozwolona w ramach serwera lub usługi. Wyświetlony komunikat o wyjątku, aby uzyskać szczegółowe informacje. Na przykład [Complete()](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync) generuje ten wyjątek, jeśli wiadomość została odebrana w [ReceiveAndDelete](/dotnet/api/microsoft.azure.servicebus.receivemode) trybu. |Sprawdź kod i dokumentację. Upewnij się, że żądana operacja jest nieprawidłowa. |Nie jest pomocne w ponownych prób. |
| [OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx) |Zostanie podjęta próba wywołania operacji na obiekcie, który już został zamknięty, przerwane lub usunięte. W rzadkich przypadkach otoczenia transakcji został już usunięty. |Sprawdź kod i upewnij się, że nie wywołuje operacje na zlikwidowanego obiektu. |Nie jest pomocne w ponownych prób. |
| [Unauthorizedaccessexception —](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx) |[TokenProvider](/dotnet/api/microsoft.servicebus.tokenprovider) obiektu nie można uzyskać tokenu, token jest nieprawidłowy lub token zawiera oświadczenia, wymagane do wykonania tej operacji. |Upewnij się, że dostawcy tokenu, który jest tworzony przy użyciu prawidłowych wartości. Sprawdź konfigurację usługi kontroli dostępu. |Ponów próbę, może pomóc w niektórych przypadkach; Dodaj logikę ponawiania próby do kodu. |
| [ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx)<br /> [ArgumentNullException](https://msdn.microsoft.com/library/system.argumentnullexception.aspx)<br />[Trwa wyjątku ArgumentOutOfRangeException](https://msdn.microsoft.com/library/system.argumentoutofrangeexception.aspx) |Jeden lub więcej argumentów przekazana do metody są nieprawidłowe.<br /> Identyfikator URI dostarczane do [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) lub [Utwórz](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) zawiera ścieżkę i jej ewentualny związek.<br /> Dostarczony schemat identyfikatora URI, do [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) lub [Utwórz](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) jest nieprawidłowy. <br />Wartość właściwości jest większa niż 32 KB. |Sprawdź kod wywołujący i upewnij się, że argumenty są poprawne. |Nie jest pomocne w ponownych prób. |
| [MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.servicebus.messagingentitynotfoundexception) |Jednostka skojarzone z operacją nie istnieje lub został usunięty. |Upewnij się, że jednostka istnieje. |Nie jest pomocne w ponownych prób. |
| [MessageNotFoundException](/dotnet/api/microsoft.servicebus.messaging.messagenotfoundexception) |Próba komunikat o błędzie z liczbą określonej sekwencji. Nie można odnaleźć tego komunikatu. |Upewnij się, że komunikat nie otrzymano jeszcze. Sprawdź, czy kolejka utraconych wiadomości, aby zobaczyć, czy komunikat został deadlettered |Nie jest pomocne w ponownych prób. |
| [MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception) |Klient nie jest w stanie nawiązać połączenia z magistralą usług. |Upewnij się, że podana nazwa hosta jest poprawna, a host jest nieosiągalny. |Ponów próbę, może pomóc w przypadku sporadycznych problemów z łącznością. |
| [ServerBusyException](/dotnet/api/microsoft.azure.servicebus.serverbusyexception) |Usługa nie jest w stanie przetworzyć żądania w tej chwili. |Klient może poczekaj przez pewien czas, a następnie spróbuj ponownie wykonać operację. |Klient może podjąć kolejną próbę po upływie niektórych interwału. Jeśli ponowienie próby w wyniku inny wyjątek, sprawdź, ponów próbę zachowania tego wyjątku. |
| [MessageLockLostException](/dotnet/api/microsoft.azure.servicebus.messagelocklostexception) |Token blokady skojarzonych z wiadomością wygasł lub nie znajduje się token blokady. |Usuń komunikat. |Nie jest pomocne w ponownych prób. |
| [SessionLockLostException](/dotnet/api/microsoft.azure.servicebus.sessionlocklostexception) |Zablokuj skojarzone z tą sesją zostaną utracone. |Przerwij [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) obiektu. |Nie jest pomocne w ponownych prób. |
| [MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception) |Ogólne komunikaty wyjątku, który może zostać wygenerowany w następujących przypadkach:<br /> Do utworzenia zostanie podjęta próba [QueueClient](/dotnet/api/microsoft.azure.servicebus.queueclient) za pomocą nazwy lub ścieżki, który należy do innej jednostki typu (na przykład temat).<br />  Aby wysłać wiadomość jest większy niż 256 KB zostanie podjęta próba. Serwer lub Usługa napotkała błąd podczas przetwarzania żądania. Wyświetlony komunikat o wyjątku, aby uzyskać szczegółowe informacje. Zazwyczaj jest to wyjątek przejściowy. |Sprawdź kod i upewnij się, że są używane tylko obiekty możliwe do serializacji treści komunikatu lub użyć serializatora niestandardowego. Sprawdź w dokumentacji obsługiwane typy wartości właściwości i tylko użycie obsługiwane. Sprawdź [IsTransient](/dotnet/api/microsoft.servicebus.messaging.messagingexception) właściwości. Jeśli jest **true**, ponowić operację. |Sposób ponawiania jest niezdefiniowana i nie może pomóc. |
| [MessagingEntityAlreadyExistsException](/dotnet/api/microsoft.servicebus.messaging.messagingentityalreadyexistsexception) |Podjęto próbę utworzenia jednostki o nazwie, która jest już używana przez inną jednostkę w tej przestrzeni nazw usługi. |Usuwanie istniejącej jednostki lub wybierz inną nazwę dla obiektu, który ma zostać utworzony. |Nie jest pomocne w ponownych prób. |
| [QuotaExceededException](/dotnet/api/microsoft.azure.servicebus.quotaexceededexception) |Jednostki obsługi komunikatów osiągnęła maksymalny dozwolony rozmiar lub przekroczono maksymalną liczbę połączeń do przestrzeni nazw. |Utworzyć miejsca w jednostce otrzymywać wiadomości od jednostki lub jego podkolejek. Zobacz [QuotaExceededException](#quotaexceededexception). |Ponownych prób może pomóc, jeśli komunikaty zostały usunięte w tym samym czasie. |
| [RuleActionException](/dotnet/api/microsoft.servicebus.messaging.ruleactionexception) |Usługa Service Bus zwraca ten wyjątek, Jeśli spróbujesz utworzyć nieprawidłową akcję reguły. Usługi Service Bus dołącza do wiadomości deadlettered ten wyjątek, jeśli wystąpi błąd podczas przetwarzania akcji reguły dla tego komunikatu. |Sprawdź działanie reguły pod kątem poprawności. |Nie jest pomocne w ponownych prób. |
| [FilterException](/dotnet/api/microsoft.servicebus.messaging.filterexception) |Usługa Service Bus zwraca ten wyjątek, Jeśli spróbujesz utworzyć nieprawidłowy filtr. Usługi Service Bus dołącza do wiadomości deadlettered ten wyjątek, jeśli wystąpił błąd podczas przetwarzania filtr dla tego komunikatu. |Sprawdź filtr pod kątem poprawności. |Nie jest pomocne w ponownych prób. |
| [SessionCannotBeLockedException](/dotnet/api/microsoft.servicebus.messaging.sessioncannotbelockedexception) |Próba zaakceptować sesji o identyfikatorze określonej sesji, ale sesja jest zablokowana przez innego klienta. |Upewnij się, że sesja jest odblokowany przez innych klientów. |Ponownych prób może pomóc, jeśli sesja wydano w międzyczasie. |
| [TransactionSizeExceededException](/dotnet/api/microsoft.servicebus.messaging.transactionsizeexceededexception) |Zbyt wiele operacji są częścią transakcji. |Zmniejsz liczbę operacji, które są częścią tej transakcji. |Nie jest pomocne w ponownych prób. |
| [MessagingEntityDisabledException](/dotnet/api/microsoft.azure.servicebus.messagingentitydisabledexception) |Żądania dla operacji środowiska uruchomieniowego w jednostce wyłączone. |Aktywuj jednostki. |Ponownych prób może pomóc, jeśli jednostki został aktywowany w międzyczasie. |
| [NoMatchingSubscriptionException](/dotnet/api/microsoft.servicebus.messaging.nomatchingsubscriptionexception) |Usługi Service Bus zwraca ten wyjątek, jeśli wyślesz wiadomość do tematu, który włączył wstępnie filtrowania i żaden z filtrów nie odpowiada. |Upewnij się, że co najmniej jeden filtr dopasowuje wartość. |Nie jest pomocne w ponownych prób. |
| [MessageSizeExceededException](/dotnet/api/microsoft.servicebus.messaging.messagesizeexceededexception) |Ładunek komunikatu przekracza limit 256 KB. Limit 256 KB jest rozmiaru łączna liczba wiadomości, który może zawierać właściwości systemu i wszystkimi narzutami .NET. |Zmniejsz rozmiar ładunku komunikatu, a następnie spróbuj ponownie wykonać operację. |Nie jest pomocne w ponownych prób. |
| [TransactionException —](https://msdn.microsoft.com/library/system.transactions.transactionexception.aspx) |Otoczenia transakcji (*Transaction.Current*) jest nieprawidłowy. Może zostać ukończone lub zostało przerwane. Wyjątek wewnętrzny może dostarczyć dodatkowych informacji. | |Nie jest pomocne w ponownych prób. |
| [TransactionInDoubtException](https://msdn.microsoft.com/library/system.transactions.transactionindoubtexception.aspx) |Próba wykonania operacji w transakcji, która jest w stanie wątpliwości, lub zostanie podjęta próba można zatwierdzić transakcji i transakcji staje się w stanie wątpliwości. |Aplikacja musi obsługiwać ten wyjątek (jako szczególny przypadek), jako transakcja może zostały już zatwierdzone. |- |

## <a name="quotaexceededexception"></a>QuotaExceededException
[QuotaExceededException](/dotnet/api/microsoft.azure.servicebus.quotaexceededexception) wskazuje, że przekroczony został limit przydziału dla określonej jednostki.

### <a name="queues-and-topics"></a>Kolejki i tematy
W przypadku kolejek i tematów często jest rozmiar kolejki. Właściwości komunikatu błędu zawiera dalsze szczegółowe informacje, jak w poniższym przykładzie:

```Output
Microsoft.ServiceBus.Messaging.QuotaExceededException
Message: The maximum entity size has been reached or exceeded for Topic: ‘xxx-xxx-xxx’. 
    Size of entity in bytes:1073742326, Max entity size in bytes:
1073741824..TrackingId:xxxxxxxxxxxxxxxxxxxxxxxxxx, TimeStamp:3/15/2013 7:50:18 AM
```

Komunikat stwierdza, że temat przekroczony limit rozmiaru, w tym wielkość 1 GB (domyślnego limitu rozmiaru). 

### <a name="namespaces"></a>Przestrzenie nazw

W przypadku przestrzeni nazw [QuotaExceededException](/dotnet/api/microsoft.azure.servicebus.quotaexceededexception) może wskazywać, że aplikacja przekroczył maksymalną liczbę połączeń do przestrzeni nazw. Na przykład:

```Output
Microsoft.ServiceBus.Messaging.QuotaExceededException: ConnectionsQuotaExceeded for namespace xxx.
<tracking-id-guid>_G12 ---> 
System.ServiceModel.FaultException`1[System.ServiceModel.ExceptionDetail]: 
ConnectionsQuotaExceeded for namespace xxx.
```

#### <a name="common-causes"></a>Typowe przyczyny
Istnieją dwie typowe przyczyny tego błędu: kolejki utraconych wiadomości, a niedziałającej odbiornikami komunikat.

1. **[Kolejka utraconych wiadomości](service-bus-dead-letter-queues.md)**  czytnik nie może ukończyć wiadomości i komunikaty są zwracane do kolejki/tematu po wygaśnięciu blokady. Może się to zdarzyć, jeśli czytnik napotka wyjątek, który uniemożliwia wywołanie [BrokeredMessage.Complete](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.complete). Po komunikat został odczytany 10 razy, przenosi do kolejki utraconych wiadomości domyślnie. To zachowanie jest kontrolowany przez [QueueDescription.MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount) właściwości i ma wartość domyślną równą 10. Jak komunikaty Ustawianie kolejki utraconych wiadomości, zajmują miejsce.
   
    Aby rozwiązać ten problem, przeczytaj i wykonaj komunikaty z kolejki utraconych wiadomości, tak jak z innych kolejki. Możesz użyć [FormatDeadLetterPath](/dotnet/api/microsoft.azure.servicebus.entitynamehelper.formatdeadletterpath) metodę, aby ułatwić formatowanie na ścieżkę kolejki utraconych wiadomości.
2. **Odbiornik zatrzymana**. Odbiornik został zatrzymany, otrzymywanie komunikatów z kolejki lub subskrypcji. Sposób identyfikacji, to jest Przyjrzyj się [QueueDescription.MessageCountDetails](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails) właściwość, która pokazuje podział pełne komunikaty. Jeśli [ActiveMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.activemessagecount) właściwość jest duża lub rosnącej, a następnie komunikaty nie są tak szybko, jak są one zapisywane odczytu.

## <a name="timeoutexception"></a>TimeoutException
A [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) wskazuje, że operacja zainicjowana przez użytkownika zajmuje więcej czasu niż limit czasu operacji. 

Należy sprawdzić wartość [ServicePointManager.DefaultConnectionLimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit) właściwości jako osiągnięcia tego limitu, również mogą powodować [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx).

### <a name="queues-and-topics"></a>Kolejki i tematy
W przypadku kolejek i tematów określono limit czasu w [MessagingFactorySettings.OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings) właściwości jako część ciągu połączenia lub za pośrednictwem [ServiceBusConnectionStringBuilder](/dotnet/api/microsoft.azure.servicebus.servicebusconnectionstringbuilder). Komunikat o błędzie, sama może się różnić, ale zawsze zawiera wartość limitu czasu określony dla bieżącej operacji. 



## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać pełną dokumentację API .NET usługi Service Bus, zobacz [dokumentacja interfejsu API .NET platformy Azure](/dotnet/api/overview/azure/service-bus).

Aby dowiedzieć się więcej na temat [usługi Service Bus](https://azure.microsoft.com/services/service-bus/), zobacz następujące artykuły:

* [Omówienie obsługi komunikatów w usłudze Service Bus](service-bus-messaging-overview.md)
* [Architektura usługi Service Bus](service-bus-architecture.md)

