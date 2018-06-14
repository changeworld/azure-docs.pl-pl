---
title: Usługa Azure Service Bus wiadomości wyjątki | Dokumentacja firmy Microsoft
description: Lista wyjątki obsługi komunikatów usługi Service Bus i wyświetlić sugerowane akcje.
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: 3d8526fe-6e47-4119-9f3e-c56d916a98f9
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/31/2018
ms.author: sethm
ms.openlocfilehash: efcfad2834c2d6775c6693f5c705a0531b2650d6
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/01/2018
ms.locfileid: "28930154"
---
# <a name="service-bus-messaging-exceptions"></a>Wyjątki obsługi komunikatów w usłudze Service Bus
W tym artykule wymieniono niektóre wyjątki generowane przez interfejsy API do obsługi komunikatów usługi Microsoft Azure Service Bus. To odwołanie jest może ulec zmianie, dlatego sprawdzanie dostępności aktualizacji.

## <a name="exception-categories"></a>Kategorie wyjątku
Interfejsów API obsługi komunikatów generowania wyjątków, które można podzielić na następujące kategorie, wraz ze skojarzonej akcji, które należy wykonać, aby je rozwiązać. Należy pamiętać, że znaczenie i przyczyny wyjątek można się różnić w zależności od typu jednostki obsługi komunikatów:

1. Błąd kodowania użytkownika ([System.ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx), [System.InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx), [System.OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx), [System.Runtime.Serialization.SerializationException](https://msdn.microsoft.com/library/system.runtime.serialization.serializationexception.aspx)). Akcja ogólne: Spróbuj naprawić kod zanim przejdziesz dalej.
2. Błąd instalacji/konfiguracji ([Microsoft.ServiceBus.Messaging.MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.servicebus.messagingentitynotfoundexception), [System.UnauthorizedAccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx). Akcja ogólne: Przejrzyj konfigurację i w razie potrzeby zmień.
3. Transient exceptions ([Microsoft.ServiceBus.Messaging.MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception), [Microsoft.ServiceBus.Messaging.ServerBusyException](/dotnet/api/microsoft.azure.servicebus.serverbusyexception), [Microsoft.ServiceBus.Messaging.MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception)). Akcja ogólne: ponów operację lub powiadomić użytkowników.
4. Pozostałe wyjątki ([System.Transactions.TransactionException](https://msdn.microsoft.com/library/system.transactions.transactionexception.aspx), [System.TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx), [Microsoft.ServiceBus.Messaging.MessageLockLostException](/dotnet/api/microsoft.azure.servicebus.messagelocklostexception), [Microsoft.ServiceBus.Messaging.SessionLockLostException](/dotnet/api/microsoft.azure.servicebus.sessionlocklostexception)). Akcja ogólne: specyficzne dla typu wyjątku; Zapoznaj się z tabelą w następnej sekcji. 

## <a name="exception-types"></a>Typy wyjątków
W poniższej tabeli wymieniono obsługi typów wyjątków i ich przyczyny i zalecane działanie uwagi, które można wykonać.

| **Typ wyjątku** | **Opis elementu/Przyczyna/przykłady** | **Zalecane działanie** | **Należy zwrócić uwagę na automatyczne/natychmiastowego ponawiania** |
| --- | --- | --- | --- |
| [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) |Serwer nie odpowiedział na żądanej operacji w określonym czasie, które są kontrolowane przez [OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings#Microsoft_ServiceBus_Messaging_MessagingFactorySettings_OperationTimeout). Serwer mogło ukończyć żądanej operacji. Może to nastąpić z sieci lub innych opóźnienia infrastruktury. |Sprawdź stan systemu w celu zachowania spójności i ponów próbę, w razie potrzeby. Zobacz [wyjątków przekroczenia limitu czasu](#timeoutexception). |Ponów próbę, może pomóc w niektórych przypadkach; Dodaj logikę ponawiania do kodu. |
| [InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx) |Operacja żądanego użytkownika nie jest dozwolona na serwerze lub usługi. Zawiera komunikat wyjątku, aby uzyskać szczegółowe informacje. Na przykład [Complete()](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync) generuje ten wyjątek, jeśli wiadomość została odebrana w [ReceiveAndDelete](/dotnet/api/microsoft.azure.servicebus.receivemode) tryb. |Sprawdź kod i dokumentację. Upewnij się, że żądana operacja jest nieprawidłowa. |Nie pomoże ponów próbę. |
| [OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx) |Podjęta próba wywołania operacji na obiekcie, który już został zamknięty, przerwana lub usunięty. W rzadkich przypadkach transakcja otoczenia zostało już usunięte. |Sprawdź kod i upewnij się, że nie jest wywoływany operacji na usuniętym obiekcie. |Nie pomoże ponów próbę. |
| [Unauthorizedaccessexception —](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx) |[TokenProvider](/dotnet/api/microsoft.azure.servicebus.tokenprovider) obiektu nie można uzyskać tokenu, token jest nieprawidłowy lub token zawiera oświadczenia wymagane do wykonania tej operacji. |Upewnij się, że dostawca tokenu jest tworzony przy użyciu prawidłowych wartości. Sprawdź konfigurację usługi kontroli dostępu. |Ponów próbę, może pomóc w niektórych przypadkach; Dodaj logikę ponawiania do kodu. |
| [ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx)<br /> [Argumentnullexception —](https://msdn.microsoft.com/library/system.argumentnullexception.aspx)<br />[ArgumentOutOfRangeException](https://msdn.microsoft.com/library/system.argumentoutofrangeexception.aspx) |Co najmniej jeden argument przekazany do metody są nieprawidłowe.<br /> Identyfikator URI dostarczony do [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) lub [Utwórz](/dotnet/api/microsoft.servicebus.messaging.messagingfactory#Microsoft_ServiceBus_Messaging_MessagingFactory_Create_System_Collections_Generic_IEnumerable_System_Uri__) zawiera następującą liczbę segmentów ścieżki.<br /> Dostarczony schemat identyfikatora URI do [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) lub [Utwórz](/dotnet/api/microsoft.servicebus.messaging.messagingfactory#Microsoft_ServiceBus_Messaging_MessagingFactory_Create_System_Collections_Generic_IEnumerable_System_Uri__) jest nieprawidłowy. <br />Wartość właściwości jest większy niż 32KB. |Sprawdź kod wywołujący i upewnij się, że argumenty są poprawne. |Nie pomoże ponów próbę. |
| [MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.servicebus.messagingentitynotfoundexception) |Jednostka skojarzonych z operacją nie istnieje lub została usunięta. |Upewnij się, że istnieje jednostki. |Nie pomoże ponów próbę. |
| [MessageNotFoundException](/dotnet/api/microsoft.servicebus.messaging.messagenotfoundexception) |Próba komunikat o błędzie z określonym numerem. Ta wiadomość nie została znaleziona. |Upewnij się, że komunikat nie zostały jeszcze odebrane. Sprawdź kolejki utraconych wiadomości, aby zobaczyć, czy komunikat został deadlettered. |Nie pomoże ponów próbę. |
| [MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception) |Klient nie będzie mógł nawiązać połączenia z magistralą usług. |Upewnij się, że nazwa podana hosta jest poprawna, a host jest dostępny. |Ponów próbę mogą ułatwić, jeśli występują problemy z łącznością sporadyczne. |
| [ServerBusyException](/dotnet/api/microsoft.azure.servicebus.serverbusyexception) |Usługa nie będzie mógł przetworzyć żądania w tej chwili. |Klient może czekać przez czas, a następnie spróbuj ponownie wykonać operację. |Klient może podjąć kolejną próbę po niektórych interwału. Jeśli ponowna próba powoduje różnych wyjątek, sprawdź zachowanie ponów próbę wykonania tego wyjątku. |
| [MessageLockLostException](/dotnet/api/microsoft.azure.servicebus.messagelocklostexception) |Token blokady skojarzony z komunikatem wygasł lub nie znaleziono token blokady. |Usuwanie komunikatu. |Nie pomoże ponów próbę. |
| [SessionLockLostException](/dotnet/api/microsoft.azure.servicebus.sessionlocklostexception) |Zablokuj skojarzone z tą sesją zostaną utracone. |Przerwij [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) obiektu. |Nie pomoże ponów próbę. |
| [MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception) |Ogólny wiadomości wyjątek, który może zostać zgłoszony w następujących przypadkach:<br /> Aby utworzyć podejmowana jest próba [QueueClient](/dotnet/api/microsoft.azure.servicebus.queueclient) za pomocą nazwy lub ścieżki, który należy do innej jednostki typu (na przykład temat).<br />  Aby wysłać wiadomość jest większy niż 256KB podejmowana jest próba. Serwer lub Usługa napotkała błąd podczas przetwarzania żądania. Sprawdź komunikat wyjątku, aby uzyskać szczegółowe informacje. Zwykle jest to przejściowy wyjątku. |Sprawdź kod i upewnij się, że są używane tylko obiekty możliwe do serializacji treści komunikatu lub użyć serializatora niestandardowego. Sprawdź dokumentację dla typów obsługiwaną wartością właściwości i tylko Użyj obsługiwane typy. Sprawdź [IsTransient](/dotnet/api/microsoft.servicebus.messaging.messagingexception#Microsoft_ServiceBus_Messaging_MessagingException_IsTransient) właściwości. Jeśli jest **true**, spróbuj ponownie wykonać operację. |Ponów próbę zachowanie nie jest zdefiniowana i nie może pomóc. |
| [MessagingEntityAlreadyExistsException](/dotnet/api/microsoft.servicebus.messaging.messagingentityalreadyexistsexception) |Podjęto próbę utworzenia jednostki o nazwie, która jest już używana przez inną jednostkę w tej przestrzeni nazw usługi. |Usuń istniejące jednostki lub wybierz inną nazwę dla obiektu, który ma zostać utworzony. |Nie pomoże ponów próbę. |
| [QuotaExceededException](/dotnet/api/microsoft.azure.servicebus.quotaexceededexception) |Jednostka obsługi komunikatów osiągnęła maksymalny dopuszczalny rozmiar lub przekroczono maksymalną liczbę połączeń do przestrzeni nazw. |Utworzyć miejsca w jednostce odbieranie komunikatów z jednostek lub jego podrzędne kolejek. Zobacz [quotaexceededexception —](#quotaexceededexception). |Ponów próbę mogą ułatwić czy wiadomości zostały usunięte w tym samym czasie. |
| [RuleActionException](/dotnet/api/microsoft.servicebus.messaging.ruleactionexception) |Usługi Service Bus zwraca ten wyjątek, jeśli podjęto próbę utworzenia akcji nieprawidłową zasadę. Usługi Service Bus dołącza do wiadomości deadlettered ten wyjątek, jeśli wystąpi błąd podczas przetwarzania Akcja reguły dla tej wiadomości. |Sprawdź działanie reguły pod kątem poprawności. |Nie pomoże ponów próbę. |
| [FilterException](/dotnet/api/microsoft.servicebus.messaging.filterexception) |Usługa Service Bus zwraca ten wyjątek przy próbie utworzenia nieprawidłowy filtr. Usługi Service Bus dołącza do wiadomości deadlettered ten wyjątek, jeśli wystąpił błąd podczas przetwarzania filtru dla tego komunikatu. |Sprawdź poprawność filtru. |Nie pomoże ponów próbę. |
| [SessionCannotBeLockedException](/dotnet/api/microsoft.servicebus.messaging.sessioncannotbelockedexception) |Próba zaakceptować sesji o identyfikatorze określonej sesji, ale sesja jest zablokowany przez innego klienta. |Upewnij się, że sesja jest odblokowany przez innych klientów. |Ponów próbę mogą ułatwić, jeśli sesja została wydana w tymczasowych. |
| [TransactionSizeExceededException](/dotnet/api/microsoft.servicebus.messaging.transactionsizeexceededexception) |Zbyt wiele operacji są częścią transakcji. |Zmniejsz liczbę operacji, które są częścią tej transakcji. |Nie pomoże ponów próbę. |
| [MessagingEntityDisabledException](/dotnet/api/microsoft.azure.servicebus.messagingentitydisabledexception) |Żądanie operacji środowiska uruchomieniowego w jednostce wyłączone. |Aktywuj jednostki. |Ponów próbę mogą ułatwić, gdy jednostka została uaktywniona w tymczasowych. |
| [NoMatchingSubscriptionException](/dotnet/api/microsoft.servicebus.messaging.nomatchingsubscriptionexception) |Usługa Service Bus zwraca tego wyjątku w przypadku wysłania komunikatu do tematu, który ma wstępnie Filtrowanie włączone i żadna filtrów nie zgadza się. |Upewnij się, że co najmniej jeden filtr jest zgodna. |Nie pomoże ponów próbę. |
| [MessageSizeExceededException](/dotnet/api/microsoft.servicebus.messaging.messagesizeexceededexception) |Ładunek komunikatu przekracza limit 256 KB. Należy pamiętać, że limit 256 KB rozmiaru całkowita liczba wiadomości, który może zawierać żadnych czynności .NET i właściwości systemu. |Zmniejsz rozmiar ładunku komunikatu, a następnie spróbuj ponownie wykonać operację. |Nie pomoże ponów próbę. |
| [TransactionException](https://msdn.microsoft.com/library/system.transactions.transactionexception.aspx) |Transakcja otoczenia (*Transaction.Current*) jest nieprawidłowy. Może została wykonana lub zostało przerwane. Wyjątek wewnętrzny może zawierać dodatkowe informacje. | |Nie pomoże ponów próbę. |
| [Transactionindoubtexception —](https://msdn.microsoft.com/library/system.transactions.transactionindoubtexception.aspx) |Nastąpi próba wykonania operacji transakcji, który znajduje się w razie wątpliwości lub próby zatwierdzić transakcji i wątpliwe staje się transakcji. |Aplikacja musi obsługiwać tego wyjątku (jako szczególnych przypadkach), jako transakcja mogła już zostać zatwierdzone. |- |

## <a name="quotaexceededexception"></a>Quotaexceededexception —
[Quotaexceededexception —](/dotnet/api/microsoft.azure.servicebus.quotaexceededexception) wskazuje, że przekroczono limit przydziału dla określonej jednostki.

### <a name="queues-and-topics"></a>Kolejki i tematy
Dla kolejek i tematów często jest to rozmiar kolejki. Właściwości komunikatu błędu zawiera dalsze szczegółowe informacje, jak w poniższym przykładzie:

```Output
Microsoft.ServiceBus.Messaging.QuotaExceededException
Message: The maximum entity size has been reached or exceeded for Topic: ‘xxx-xxx-xxx’. 
    Size of entity in bytes:1073742326, Max entity size in bytes:
1073741824..TrackingId:xxxxxxxxxxxxxxxxxxxxxxxxxx, TimeStamp:3/15/2013 7:50:18 AM
```

Komunikat, że temat przekracza limit rozmiaru, w tym wielkości 1 GB (domyślnego limitu rozmiaru). 

### <a name="namespaces"></a>Przestrzenie nazw

W przypadku przestrzeni nazw [quotaexceededexception —](/dotnet/api/microsoft.azure.servicebus.quotaexceededexception) może oznaczać, że aplikacja przekroczył maksymalną liczbę połączeń do przestrzeni nazw. Na przykład:

```Output
Microsoft.ServiceBus.Messaging.QuotaExceededException: ConnectionsQuotaExceeded for namespace xxx.
<tracking-id-guid>_G12 ---> 
System.ServiceModel.FaultException`1[System.ServiceModel.ExceptionDetail]: 
ConnectionsQuotaExceeded for namespace xxx.
```

#### <a name="common-causes"></a>Typowe przyczyny
Istnieją dwie typowe przyczyny tego błędu: kolejki utraconych wiadomości i niedziałającej odbiorcy wiadomości.

1. **[Kolejki utraconych wiadomości](service-bus-dead-letter-queues.md)**  czytnik nie może ukończyć wiadomości i są zwracane wiadomości do kolejki/tematu, po wygaśnięciu blokady. Może się to zdarzyć, jeśli czytnik napotkał wyjątek, który uniemożliwia wywołanie [BrokeredMessage.Complete](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.complete). Po komunikat został odczytany 10 razy, powoduje przeniesienie do kolejki utraconych wiadomości domyślnie. To zachowanie jest kontrolowany przez [QueueDescription.MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount) właściwości i ma wartość domyślną równą 10. Jak komunikaty Ustawianie kolejki utraconych wiadomości, zajmują miejsce.
   
    Aby rozwiązać ten problem, przeczytaj i ukończyć wiadomości z kolejki utraconych wiadomości, jak w przypadku z innych kolejki. Można użyć [FormatDeadLetterPath](/dotnet/api/microsoft.azure.servicebus.entitynamehelper.formatdeadletterpath) metodę formatowania na ścieżkę kolejki utraconych wiadomości.
2. **Odbiornik zatrzymana** odbiornik została zatrzymana, odbierania wiadomości z kolejki lub subskrypcji. Sposób identyfikacji to jest aby przyjrzeć się [QueueDescription.MessageCountDetails](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails) właściwość, która przedstawia podział pełne komunikaty. Jeśli [ActiveMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.activemessagecount) właściwości jest duża lub rosnącym, a następnie komunikaty nie są tak szybko, jak są one zapisywane odczytu.

## <a name="timeoutexception"></a>TimeoutException
A [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) wskazuje, że użytkownik zainicjował operację trwa dłużej niż limit czasu operacji. 

Należy sprawdzić wartość [ServicePointManager.DefaultConnectionLimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit) właściwość jako naciśnięcie tego limitu może również spowodować [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx).

### <a name="queues-and-topics"></a>Kolejki i tematy
Dla kolejek i tematów, może zostać określony w [MessagingFactorySettings.OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings#Microsoft_ServiceBus_Messaging_MessagingFactorySettings_OperationTimeout) właściwość, jako część ciągu połączenia lub za pośrednictwem [ServiceBusConnectionStringBuilder](/dotnet/api/microsoft.azure.servicebus.servicebusconnectionstringbuilder). Może się różnić się komunikat o błędzie, ale zawsze zawiera wartość limitu czasu określony w bieżącej operacji. 



## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać pełną dokumentację interfejsu API usługi Service Bus .NET, zobacz [dokumentacja interfejsu API platformy .NET Azure](/dotnet/api/overview/azure/service-bus).

Aby dowiedzieć się więcej o [usługi Service Bus](https://azure.microsoft.com/services/service-bus/), zobacz następujące artykuły:

* [Omówienie obsługi komunikatów w usłudze Service Bus](service-bus-messaging-overview.md)
* [Podstawy usługi Service Bus](service-bus-fundamentals-hybrid-solutions.md)
* [Architektura usługi Service Bus](service-bus-architecture.md)

