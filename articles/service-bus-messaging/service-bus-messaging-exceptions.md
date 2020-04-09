---
title: Usługa Azure Service Bus — wyjątki obsługi wiadomości | Dokumenty firmy Microsoft
description: Ten artykuł zawiera listę wyjątków obsługi wiadomości usługi Azure Service Bus i sugerowane akcje do wykonania w przypadku wystąpienia wyjątku.
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
ms.date: 03/23/2020
ms.author: aschhab
ms.openlocfilehash: d04902a8d53397b7e7d9712a1c75ce44cc7aa7ad
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80880792"
---
# <a name="service-bus-messaging-exceptions"></a>Wyjątki obsługi wiadomości usługi Service Bus
W tym artykule wymieniono wyjątki platformy .NET generowane przez interfejsy API programu .NET Framework. 

## <a name="exception-categories"></a>Kategorie wyjątków
Interfejsy API obsługi wiadomości generują wyjątki, które mogą należeć do następujących kategorii, wraz ze skojarzoną akcją, którą można wykonać, aby spróbować je naprawić. Znaczenie i przyczyny wyjątku mogą się różnić w zależności od typu jednostki obsługi wiadomości:

1. Błąd kodowania użytkownika ([System.ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx), [System.InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx), [System.OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx), [System.Runtime.Serialization.SerializationException](https://msdn.microsoft.com/library/system.runtime.serialization.serializationexception.aspx)). Ogólne działanie: spróbuj naprawić kod przed kontynuowaniem.
2. Błąd instalatora/konfiguracji ([Microsoft.ServiceBus.Messaging.MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.servicebus.messagingentitynotfoundexception), [System.UnauthorizedAccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx). Ogólne działanie: przejrzyj konfigurację i w razie potrzeby zmień.
3. Wyjątki przejściowe ([Microsoft.ServiceBus.Messaging.MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception), [Microsoft.ServiceBus.Messaging.ServerBusyException](/dotnet/api/microsoft.azure.servicebus.serverbusyexception), [Microsoft.ServiceBus.MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception)). Ogólne działanie: ponów próbę wykonania operacji lub powiadomienie użytkowników. Klasa `RetryPolicy` w sdk klienta można skonfigurować do obsługi ponownych prób automatycznie. Aby uzyskać więcej informacji, zobacz [Wskazówki dotyczące ponawiania prób](/azure/architecture/best-practices/retry-service-specific#service-bus).
4. Inne wyjątki ([System.Transactions.TransactionException](https://msdn.microsoft.com/library/system.transactions.transactionexception.aspx), [System.TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx), [Microsoft.ServiceBus.Messaging.MessageLockLostException](/dotnet/api/microsoft.azure.servicebus.messagelocklostexception), [Microsoft.ServiceBus.Messaging.SessionLockLostException](/dotnet/api/microsoft.azure.servicebus.sessionlocklostexception)). Działanie ogólne: specyficzne dla typu wyjątku; patrz tabela w poniższej sekcji: 

## <a name="exception-types"></a>Typy wyjątków
W poniższej tabeli wymieniono typy wyjątków obsługi wiadomości i ich przyczyny oraz uwagi sugerowane działania, które można podjąć.

| **Typ wyjątku** | **Opis/Przyczyna/Przykłady** | **Sugerowana akcja** | **Uwaga dotycząca automatycznego/natychmiastowego ponowiania prób** |
| --- | --- | --- | --- |
| [Timeoutexception](https://msdn.microsoft.com/library/system.timeoutexception.aspx) |Serwer nie odpowiedział na żądaną operację w określonym czasie, który jest kontrolowany przez [OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings). Serwer mógł ukończyć żądaną operację. Może się to zdarzyć z powodu opóźnień w sieci lub innej infrastrukturze. |Sprawdź stan systemu pod kątem spójności i w razie potrzeby ponów próbę. Zobacz [Wyjątki limitu czasu](#timeoutexception). |W niektórych przypadkach ponowna spróbuj może pomóc; dodaj logikę ponawiania do kodu. |
| [InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx) |Żądana operacja użytkownika jest niedozwolona na serwerze lub usłudze. Zobacz komunikat o wyjątku, aby uzyskać szczegółowe informacje. Na przykład [Complete()](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync) generuje ten wyjątek, jeśli wiadomość została odebrana w trybie [ReceiveAndDelete.](/dotnet/api/microsoft.azure.servicebus.receivemode) |Sprawdź kod i dokumentację. Upewnij się, że żądana operacja jest prawidłowa. |Ponów próbę nie pomoże. |
| [Operationcanceledexception](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx) |Podejmowana jest próba wywołania operacji na obiekcie, który został już zamknięty, przerwany lub usunięty. W rzadkich przypadkach transakcja otoczenia jest już usuwana. |Sprawdź kod i upewnij się, że nie wywołuje operacji na disposed obiektu. |Ponów próbę nie pomoże. |
| [Unauthorizedaccessexception](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx) |[Obiekt TokenProvider](/dotnet/api/microsoft.servicebus.tokenprovider) nie może uzyskać tokenu, token jest nieprawidłowy lub token nie zawiera oświadczeń wymaganych do wykonania operacji. |Upewnij się, że dostawca tokenu jest tworzony z poprawnymi wartościami. Sprawdź konfigurację usługi kontroli dostępu. |W niektórych przypadkach ponowna spróbuj może pomóc; dodaj logikę ponawiania do kodu. |
| [Zeksuw argumentów](https://msdn.microsoft.com/library/system.argumentexception.aspx)<br /> [Argumentnullexception](https://msdn.microsoft.com/library/system.argumentnullexception.aspx)<br />[Kreślenie argumentów](https://msdn.microsoft.com/library/system.argumentoutofrangeexception.aspx) |Jeden lub więcej argumentów dostarczonych do metody są nieprawidłowe.<br /> Identyfikator URI dostarczony do [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) lub [Create](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) zawiera segmenty ścieżek.<br /> Schemat identyfikatorów URI dostarczonych do [namespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) lub [Create](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) jest nieprawidłowy. <br />Wartość właściwości jest większa niż 32 KB. |Sprawdź kod wywołujący i upewnij się, że argumenty są poprawne. |Ponów próbę nie pomoże. |
| [MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.servicebus.messagingentitynotfoundexception) |Jednostka skojarzona z operacją nie istnieje lub została usunięta. |Upewnij się, że jednostka istnieje. |Ponów próbę nie pomoże. |
| [MessageNotFoundException](/dotnet/api/microsoft.servicebus.messaging.messagenotfoundexception) |Spróbuj odebrać wiadomość o określonym numerze sekwencyjnym. Ten komunikat nie został znaleziony. |Upewnij się, że wiadomość nie została jeszcze odebrana. Sprawdź kolejkę deadletter, aby sprawdzić, czy wiadomość została deadlettered. |Ponów próbę nie pomoże. |
| [MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception) |Klient nie może ustanowić połączenia z usługą Service Bus. |Upewnij się, że podana nazwa hosta jest poprawna i host jest osiągalny. |Ponów próbę może pomóc, jeśli występują sporadyczne problemy z łącznością. |
| [ServerBusyException (Nieekceptycja serwera)](/dotnet/api/microsoft.azure.servicebus.serverbusyexception) |Usługa nie może przetworzyć żądania w tej chwili. |Klient może czekać przez pewien czas, a następnie ponowić próbę wykonania operacji. |Klient może ponowić próbę po pewnym przedziale czasu. Jeśli ponowna próba powoduje inny wyjątek, sprawdź ponowić próbę zachowania tego wyjątku. |
| [MessageLockLostException](/dotnet/api/microsoft.azure.servicebus.messagelocklostexception) |Token blokady skojarzony z wiadomością wygasł lub nie znaleziono tokenu blokady. |Zutylizuj wiadomość. |Ponów próbę nie pomoże. |
| [SessionLockLostException](/dotnet/api/microsoft.azure.servicebus.sessionlocklostexception) |Blokada skojarzona z tą sesją zostanie utracona. |Przerwij [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) obiektu. |Ponów próbę nie pomoże. |
| [MessagingException (Nieekceptor wiadomości)](/dotnet/api/microsoft.servicebus.messaging.messagingexception) |Wyjątek obsługi wiadomości ogólne, który może być zgłaszany w następujących przypadkach:<p>Podejmowana jest próba utworzenia [QueueClient](/dotnet/api/microsoft.azure.servicebus.queueclient) przy użyciu nazwy lub ścieżki, która należy do innego typu jednostki (na przykład tematu).</p><p>Podejmowana jest próba wysłania wiadomości większej niż 256 KB. </p>Serwer lub usługa napotkał błąd podczas przetwarzania żądania. Zobacz komunikat o wyjątku, aby uzyskać szczegółowe informacje. Zazwyczaj jest to przejściowy wyjątek.</p><p>Żądanie zostało zakończone, ponieważ jednostka jest ograniczana. Kod błędu: 50001, 50002, 50008. </p> | Sprawdź kod i upewnij się, że tylko serializable obiekty są używane dla treści wiadomości (lub użyć niestandardowego serializatora). <p>Sprawdź w dokumentacji obsługiwane typy wartości właściwości i używać tylko obsługiwane typy.</p><p> Sprawdź [IsTransient](/dotnet/api/microsoft.servicebus.messaging.messagingexception) właściwości. Jeśli to **prawda,** można ponowić próbę wykonania operacji. </p>| Jeśli wyjątek jest spowodowany ograniczaniem przepustowości, poczekaj kilka sekund i ponów próbę wykonania operacji ponownie. Ponów próbę zachowanie jest niezdefiniowana i może nie pomóc w innych scenariuszach.|
| [MessagingEntityNajednajwiększośćexception](/dotnet/api/microsoft.servicebus.messaging.messagingentityalreadyexistsexception) |Spróbuj utworzyć encję o nazwie, która jest już używana przez inną jednostkę w tym obszarze nazw usługi. |Usuń istniejącą encję lub wybierz inną nazwę dla tworzonej encji. |Ponów próbę nie pomoże. |
| [Quotaexceededexception](/dotnet/api/microsoft.azure.servicebus.quotaexceededexception) |Jednostka obsługi wiadomości osiągnęła maksymalny dopuszczalny rozmiar lub przekroczono maksymalną liczbę połączeń z obszarem nazw. |Utwórz miejsce w encji, odbierając wiadomości z encji lub jej podoka. Zobacz [QuotaExceededException](#quotaexceededexception). |Ponów próbę może pomóc, jeśli wiadomości zostały usunięte w międzyczasie. |
| [RuleActionException (Niestawięcie na reguły)](/dotnet/api/microsoft.servicebus.messaging.ruleactionexception) |Usługa Service Bus zwraca ten wyjątek, jeśli spróbujesz utworzyć nieprawidłową akcję reguły. Usługa Service Bus dołącza ten wyjątek do komunikatu z informacją o martwym wysłonu, jeśli wystąpi błąd podczas przetwarzania akcji reguły dla tego komunikatu. |Sprawdź poprawność akcji reguły. |Ponów próbę nie pomoże. |
| [FiltrException](/dotnet/api/microsoft.servicebus.messaging.filterexception) |Usługa Service Bus zwraca ten wyjątek, jeśli spróbujesz utworzyć nieprawidłowy filtr. Usługa Service Bus dołącza ten wyjątek do komunikatu z informacją o martwym wysłonu, jeśli wystąpił błąd podczas przetwarzania filtru dla tej wiadomości. |Sprawdź poprawność filtra. |Ponów próbę nie pomoże. |
| [SessionCannotBeLockedException (Nie może być zamknięta ekspercja)](/dotnet/api/microsoft.servicebus.messaging.sessioncannotbelockedexception) |Spróbuj zaakceptować sesję o określonym identyfikatorze sesji, ale sesja jest obecnie zablokowana przez innego klienta. |Upewnij się, że sesja jest odblokowana przez innych klientów. |Ponów próbę może pomóc, jeśli sesja została zwolniona w międzyczasie. |
| [TransactionSizeExceededException](/dotnet/api/microsoft.servicebus.messaging.transactionsizeexceededexception) |Zbyt wiele operacji są częścią transakcji. |Zmniejsz liczbę operacji, które są częścią tej transakcji. |Ponów próbę nie pomoże. |
| [MessagingEntityDisabledException](/dotnet/api/microsoft.azure.servicebus.messagingentitydisabledexception) |Żądanie operacji środowiska uruchomieniowego w wyłączonej jednostce. |Aktywuj encję. |Ponów próbę może pomóc, jeśli jednostka została aktywowana w międzyczasie. |
| [NoMatchingSubscriptionException](/dotnet/api/microsoft.servicebus.messaging.nomatchingsubscriptionexception) |Usługa Service Bus zwraca ten wyjątek, jeśli wiadomość zostanie wysłana do tematu, który ma włączone filtrowanie wstępne i żaden z filtrów nie jest zgodny. |Upewnij się, że co najmniej jeden filtr pasuje. |Ponów próbę nie pomoże. |
| [MessageSizeExceedException](/dotnet/api/microsoft.servicebus.messaging.messagesizeexceededexception) |Ładunek wiadomości przekracza limit 256 KB. Limit 256 KB to całkowity rozmiar wiadomości, który może zawierać właściwości systemu i wszelkie obciążenie .NET. |Zmniejsz rozmiar ładunku wiadomości, a następnie ponów próbę wykonania operacji. |Ponów próbę nie pomoże. |
| [TransactionException](https://msdn.microsoft.com/library/system.transactions.transactionexception.aspx) |Transakcja otoczenia (*Transaction.Current*) jest nieprawidłowa. Być może został on ukończony lub przerwany. Wyjątek wewnętrzny może dostarczyć dodatkowych informacji. | |Ponów próbę nie pomoże. |
| [Transactionindoubtexception](https://msdn.microsoft.com/library/system.transactions.transactionindoubtexception.aspx) |Operacja jest podejmowana na transakcji, która jest wątpliwa, lub podejmowana jest próba zatwierdzenia transakcji i transakcja staje się wątpliwa. |Aplikacja musi obsługiwać ten wyjątek (jako przypadek szczególny), ponieważ transakcja mogła już zostać zatwierdzona. |- |

## <a name="quotaexceededexception"></a>Quotaexceededexception
Wyjątek [QuotaExceededException](/dotnet/api/microsoft.azure.servicebus.quotaexceededexception) wskazuje, że przekroczono limit przydziału dla określonej jednostki.

### <a name="queues-and-topics"></a>Kolejki i tematy
W przypadku kolejek i tematów często jest to rozmiar kolejki. Właściwość komunikatu o błędzie zawiera dalsze szczegóły, jak w poniższym przykładzie:

```Output
Microsoft.ServiceBus.Messaging.QuotaExceededException
Message: The maximum entity size has been reached or exceeded for Topic: 'xxx-xxx-xxx'. 
    Size of entity in bytes:1073742326, Max entity size in bytes:
1073741824..TrackingId:xxxxxxxxxxxxxxxxxxxxxxxxxx, TimeStamp:3/15/2013 7:50:18 AM
```

Komunikat stwierdza, że temat przekroczył limit rozmiaru, w tym przypadku 1 GB (domyślny limit rozmiaru). 

### <a name="namespaces"></a>Namespaces

W przypadku obszarów nazw [QuotaExceededException](/dotnet/api/microsoft.azure.servicebus.quotaexceededexception) może wskazywać, że aplikacja przekroczyła maksymalną liczbę połączeń z obszarem nazw. Przykład:

```Output
Microsoft.ServiceBus.Messaging.QuotaExceededException: ConnectionsQuotaExceeded for namespace xxx.
<tracking-id-guid>_G12 ---> 
System.ServiceModel.FaultException`1[System.ServiceModel.ExceptionDetail]: 
ConnectionsQuotaExceeded for namespace xxx.
```

### <a name="common-causes"></a>Najczęstsze przyczyny
Istnieją dwie typowe przyczyny tego błędu: kolejki utraconych wiadomości i niedziałających odbiorników komunikatów.

1. **[Kolejka utraconych wiadomości](service-bus-dead-letter-queues.md)** Czytnik nie może ukończyć wiadomości i wiadomości są zwracane do kolejki/tematu po wygaśnięciu blokady. Może się zdarzyć, jeśli czytelnik napotka wyjątek, który uniemożliwia mu [wywołanie BrokeredMessage.Complete](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.complete). Po 10-krotnym odczytie wiadomości jest on domyślnie przenoszony do kolejki utraconych wiadomości. To zachowanie jest kontrolowane przez [właściwość QueueDescription.MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount) i ma wartość domyślną 10. Gdy wiadomości piętrzą się w kolejce martwych liter, zajmują miejsce.
   
    Aby rozwiązać ten problem, przeczytaj i uzupełnij wiadomości z kolejki utraconych wiadomości, tak jak z każdej innej kolejki. Można użyć [FormatDeadLetterPath](/dotnet/api/microsoft.azure.servicebus.entitynamehelper.formatdeadletterpath) metody, aby pomóc sformatować ścieżkę kolejki utraconych wiadomości.
2. **Odbiornik zatrzymany**. Odbiorca przestał odbierać wiadomości z kolejki lub subskrypcji. Sposób zidentyfikowania tego jest spojrzenie na [QueueDescription.MessageCountDetails](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails) właściwości, która pokazuje pełny podział wiadomości. Jeśli [ActiveMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.activemessagecount) właściwość jest wysoka lub rośnie, a następnie wiadomości nie są odczytywane tak szybko, jak są one zapisywane.

## <a name="timeoutexception"></a>Timeoutexception
[TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) wskazuje, że operacja inicjowana przez użytkownika trwa dłużej niż limit czasu operacji. 

Należy sprawdzić wartość [ServicePointManager.DefaultConnectionLimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit) właściwości, jak naciśnięcie tego limitu może również spowodować [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx).

### <a name="queues-and-topics"></a>Kolejki i tematy
W przypadku kolejek i tematów limit czasu jest określony we właściwości [MessagingFactorySettings.OperationTimeout,](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings) jako część ciągu połączenia lub za pośrednictwem [usługi ServiceBusConnectionStringBuilder](/dotnet/api/microsoft.azure.servicebus.servicebusconnectionstringbuilder). Sam komunikat o błędzie może się różnić, ale zawsze zawiera wartość limitu czasu określoną dla bieżącej operacji. 

## <a name="next-steps"></a>Następne kroki
Aby uzyskać pełne odwołanie do interfejsu API usługi Service Bus .NET, zobacz [odwołanie do interfejsu API platformy Azure .NET](/dotnet/api/overview/azure/service-bus).
Aby uzyskać wskazówki dotyczące [rozwiązywania problemów,](service-bus-troubleshooting-guide.md) zobacz przewodnik rozwiązywania problemów
