---
title: Przewodnik rozwiązywania problemów dla Azure Service Bus | Microsoft Docs
description: Lista wyjątków Service Bus komunikatów i sugerowanych akcji.
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
ms.openlocfilehash: eebbef25f2cd4539a5092f271c3944c24503f287
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/16/2020
ms.locfileid: "76156815"
---
# <a name="troubleshooting-guide-for-azure-service-bus"></a>Przewodnik rozwiązywania problemów Azure Service Bus
W tym artykule przedstawiono niektóre wyjątki platformy .NET wygenerowane przez Service Bus .NET Framework interfejsy API, a także inne wskazówki dotyczące rozwiązywania problemów. 

## <a name="service-bus-messaging-exceptions"></a>Wyjątki obsługi komunikatów w usłudze Service Bus
Ta sekcja zawiera listę wyjątków platformy .NET wygenerowanych przez interfejsy API .NET Framework. 

### <a name="exception-categories"></a>Kategorie wyjątków
Interfejsy API obsługi komunikatów generują wyjątki, które mogą należeć do następujących kategorii oraz skojarzoną akcję, którą można wykonać, aby spróbować rozwiązać te problemy. Znaczenie i przyczyny wyjątku mogą się różnić w zależności od typu jednostki obsługi komunikatów:

1. Błąd kodowania użytkownika ([System. ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx), [System. InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx), [System. OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx), [System. Runtime. Serialization. SerializationException](https://msdn.microsoft.com/library/system.runtime.serialization.serializationexception.aspx)). Ogólne działanie: Spróbuj naprawić kod zanim przejdziesz dalej.
2. Błąd instalacji/konfiguracji ([Microsoft. ServiceBus. Messaging. MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.servicebus.messagingentitynotfoundexception), [System. UnauthorizedAccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx). Ogólne Akcja: Przejrzyj konfigurację i w razie potrzeby zmienić.
3. Wyjątki przejściowe ([Microsoft. ServiceBus. Messaging. messagingexception](/dotnet/api/microsoft.servicebus.messaging.messagingexception), [Microsoft. ServiceBus. Messaging. wyjątek serverbusyexception](/dotnet/api/microsoft.azure.servicebus.serverbusyexception), [Microsoft. ServiceBus. Messaging. MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception)). Ogólne działanie: spróbuj ponownie wykonać operację lub powiadomić użytkowników. `RetryPolicy` klasy w zestawie SDK klienta można skonfigurować do automatycznego obsłużenia ponownych prób. Aby uzyskać więcej informacji, zobacz [wskazówki dotyczące ponawiania prób](/azure/architecture/best-practices/retry-service-specific#service-bus).
4. Inne wyjątki ([System. Actions. TransactionException](https://msdn.microsoft.com/library/system.transactions.transactionexception.aspx), [System. TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx), [Microsoft. ServiceBus. Messaging. MessageLockLostException](/dotnet/api/microsoft.azure.servicebus.messagelocklostexception), [Microsoft. ServiceBus. Messaging. SessionLockLostException](/dotnet/api/microsoft.azure.servicebus.sessionlocklostexception)). Akcja ogólna: określona dla typu wyjątku; Zapoznaj się z tabelą w następującej sekcji: 

### <a name="exception-types"></a>Typy wyjątków
W poniższej tabeli wymieniono komunikatów typów wyjątków i powoduje, że oraz informacje o sugerowanej akcji, które można wykonać.

| **Typ wyjątku** | **Opis/przyczyna/przykłady** | **Sugerowana akcja** | **Uwaga dotycząca automatycznego/natychmiastowego ponawiania próby** |
| --- | --- | --- | --- |
| [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) |Serwer nie odpowiedział na żądaną operację w określonym czasie, który jest kontrolowany przez [OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings). Serwer ukończyć żądanej operacji. Może się tak zdarzyć z powodu opóźnień sieci lub innych. |Stan systemu w celu zachowania spójności i spróbuj ponownie, jeśli to konieczne. Zobacz [wyjątki limitów czasu](#timeoutexception). |Ponów próbę, może pomóc w niektórych przypadkach; Dodaj logikę ponawiania próby do kodu. |
| [InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx) |Żądana operacja użytkownika nie jest dozwolona w ramach serwera lub usługi. Wyświetlony komunikat o wyjątku, aby uzyskać szczegółowe informacje. Na przykład [pełna ()](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync) generuje ten wyjątek, jeśli wiadomość została odebrana w trybie [ReceiveAndDelete](/dotnet/api/microsoft.azure.servicebus.receivemode) . |Sprawdź kod i dokumentację. Upewnij się, że żądana operacja jest nieprawidłowa. |Ponowienie nie pomaga. |
| [OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx) |Zostanie podjęta próba wywołania operacji na obiekcie, który już został zamknięty, przerwane lub usunięte. W rzadkich przypadkach otoczenia transakcji został już usunięty. |Sprawdź kod i upewnij się, że nie wywoła operacji na usuniętym obiekcie. |Ponowienie nie pomaga. |
| [Unauthorizedaccessexception —](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx) |Obiekt [TokenProvider](/dotnet/api/microsoft.servicebus.tokenprovider) nie może uzyskać tokenu, token jest nieprawidłowy lub token nie zawiera oświadczeń wymaganych do wykonania tej operacji. |Upewnij się, że dostawcy tokenu, który jest tworzony przy użyciu prawidłowych wartości. Sprawdź konfigurację Access Control Service. |Ponów próbę, może pomóc w niektórych przypadkach; Dodaj logikę ponawiania próby do kodu. |
| [ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx)<br /> [ArgumentNullException](https://msdn.microsoft.com/library/system.argumentnullexception.aspx)<br />[Trwa wyjątku ArgumentOutOfRangeException](https://msdn.microsoft.com/library/system.argumentoutofrangeexception.aspx) |Jeden lub więcej argumentów przekazana do metody są nieprawidłowe.<br /> Identyfikator URI dostarczane do [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) lub [Utwórz](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) zawiera ścieżkę i jej ewentualny związek.<br /> Dostarczony schemat identyfikatora URI, do [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) lub [Utwórz](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) jest nieprawidłowy. <br />Wartość właściwości jest większa niż 32 KB. |Sprawdź kod wywołujący i upewnij się, że argumenty są poprawne. |Ponowienie nie pomaga. |
| [MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.servicebus.messagingentitynotfoundexception) |Jednostka skojarzona z operacją nie istnieje lub została usunięta. |Upewnij się, że jednostka istnieje. |Ponowienie nie pomaga. |
| [MessageNotFoundException](/dotnet/api/microsoft.servicebus.messaging.messagenotfoundexception) |Spróbuj odebrać komunikat z określonym numerem sekwencyjnym. Nie znaleziono tego komunikatu. |Upewnij się, że wiadomość nie została już odebrana. Sprawdź kolejkę utraconych wiadomości, aby sprawdzić, czy wiadomość została deadlettered. |Ponowienie nie pomaga. |
| [MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception) |Klient nie może nawiązać połączenia z Service Bus. |Upewnij się, że podana nazwa hosta jest poprawna, a host jest nieosiągalny. |Ponów próbę, może pomóc w przypadku sporadycznych problemów z łącznością. |
| [Wyjątek serverbusyexception](/dotnet/api/microsoft.azure.servicebus.serverbusyexception) |Usługa nie może w tej chwili przetworzyć żądania. |Klient może poczekaj przez pewien czas, a następnie spróbuj ponownie wykonać operację. |Klient może podjąć kolejną próbę po upływie niektórych interwału. Jeśli ponowienie próby w wyniku inny wyjątek, sprawdź, ponów próbę zachowania tego wyjątku. |
| [MessageLockLostException](/dotnet/api/microsoft.azure.servicebus.messagelocklostexception) |Token blokady skojarzony z komunikatem wygasł lub nie znaleziono tokenu blokady. |Usuń wiadomość. |Ponowienie nie pomaga. |
| [SessionLockLostException](/dotnet/api/microsoft.azure.servicebus.sessionlocklostexception) |Blokada skojarzona z tą sesją zostanie utracona. |Przerwij obiekt [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) . |Ponowienie nie pomaga. |
| [MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception) |Ogólny wyjątek komunikatów, który może być zgłaszany w następujących przypadkach:<br /> Podjęto próbę utworzenia [QueueClient](/dotnet/api/microsoft.azure.servicebus.queueclient) przy użyciu nazwy lub ścieżki, która należy do innego typu jednostki (na przykład tematu).<br />  Aby wysłać wiadomość jest większy niż 256 KB zostanie podjęta próba. Serwer lub Usługa napotkała błąd podczas przetwarzania żądania. Wyświetlony komunikat o wyjątku, aby uzyskać szczegółowe informacje. Zwykle jest to wyjątek przejściowy. |Sprawdź kod i upewnij się, że są używane tylko obiekty możliwe do serializacji treści komunikatu lub użyć serializatora niestandardowego. Sprawdź w dokumentacji obsługiwane typy wartości właściwości i tylko użycie obsługiwane. Sprawdź [IsTransient](/dotnet/api/microsoft.servicebus.messaging.messagingexception) właściwości. Jeśli jest to **prawdziwe**, można ponowić próbę wykonania operacji. |Sposób ponawiania jest niezdefiniowana i nie może pomóc. |
| [MessagingEntityAlreadyExistsException](/dotnet/api/microsoft.servicebus.messaging.messagingentityalreadyexistsexception) |Podjęto próbę utworzenia jednostki o nazwie, która jest już używana przez inną jednostkę w tej przestrzeni nazw usługi. |Usuwanie istniejącej jednostki lub wybierz inną nazwę dla obiektu, który ma zostać utworzony. |Ponowienie nie pomaga. |
| [QuotaExceededException](/dotnet/api/microsoft.azure.servicebus.quotaexceededexception) |Jednostka obsługi komunikatów osiągnęła maksymalny dozwolony rozmiar lub Przekroczono maksymalną liczbę połączeń z przestrzenią nazw. |Utworzyć miejsca w jednostce otrzymywać wiadomości od jednostki lub jego podkolejek. Zobacz [QuotaExceededException](#quotaexceededexception). |Ponownych prób może pomóc, jeśli komunikaty zostały usunięte w tym samym czasie. |
| [RuleActionException](/dotnet/api/microsoft.servicebus.messaging.ruleactionexception) |Service Bus zwraca ten wyjątek, Jeśli podjęto próbę utworzenia nieprawidłowej akcji reguły. Service Bus dołącza ten wyjątek do komunikatu deadlettered, jeśli wystąpi błąd podczas przetwarzania akcji reguły dla tego komunikatu. |Sprawdź akcję reguły, aby sprawdzić poprawność. |Ponowienie nie pomaga. |
| [Filtrexception](/dotnet/api/microsoft.servicebus.messaging.filterexception) |Service Bus zwraca ten wyjątek, jeśli próbujesz utworzyć nieprawidłowy filtr. Service Bus dołącza ten wyjątek do komunikatu deadlettered, jeśli wystąpił błąd podczas przetwarzania filtru dla tej wiadomości. |Sprawdź, czy filtr jest prawidłowy. |Ponowienie nie pomaga. |
| [SessionCannotBeLockedException](/dotnet/api/microsoft.servicebus.messaging.sessioncannotbelockedexception) |Podjęto próbę zaakceptowania sesji z określonym IDENTYFIKATORem sesji, ale sesja jest obecnie zablokowana przez innego klienta. |Upewnij się, że sesja jest odblokowana przez innych klientów. |Ponowienie próby może pomóc, jeśli sesja została wydana w tymczasowym. |
| [TransactionSizeExceededException](/dotnet/api/microsoft.servicebus.messaging.transactionsizeexceededexception) |Zbyt wiele operacji jest częścią transakcji. |Zmniejsz liczbę operacji, które są częścią tej transakcji. |Ponowienie nie pomaga. |
| [MessagingEntityDisabledException](/dotnet/api/microsoft.azure.servicebus.messagingentitydisabledexception) |Żądania dla operacji środowiska uruchomieniowego w jednostce wyłączone. |Aktywuj jednostki. |Ponownych prób może pomóc, jeśli jednostki został aktywowany w międzyczasie. |
| [NoMatchingSubscriptionException](/dotnet/api/microsoft.servicebus.messaging.nomatchingsubscriptionexception) |Service Bus zwraca ten wyjątek, jeśli wysyła komunikat do tematu, który ma włączoną funkcję wstępnego filtrowania, i żaden z filtrów nie jest zgodny. |Upewnij się, że co najmniej jeden filtr jest zgodny. |Ponowienie nie pomaga. |
| [MessageSizeExceededException](/dotnet/api/microsoft.servicebus.messaging.messagesizeexceededexception) |Ładunek komunikatu przekracza limit 256 KB. Limit 256 KB to całkowity rozmiar komunikatu, który może obejmować właściwości systemu i dowolnych obciążeń związanych z platformą .NET. |Zmniejsz rozmiar ładunku komunikatu, a następnie spróbuj ponownie wykonać operację. |Ponowienie nie pomaga. |
| [TransactionException](https://msdn.microsoft.com/library/system.transactions.transactionexception.aspx) |Transakcja otoczenia (*Transaction. Current*) jest nieprawidłowa. Mógł zostać ukończony lub przerwany. Wewnętrzny wyjątek może dostarczyć dodatkowych informacji. | |Ponowienie nie pomaga. |
| [TransactionInDoubtException —](https://msdn.microsoft.com/library/system.transactions.transactionindoubtexception.aspx) |Podjęto próbę wykonania operacji w transakcji, która jest wątpliwa lub podjęto próbę zatwierdzenia transakcji, a transakcja stanie się wątpliwa. |Aplikacja musi obsłużyć ten wyjątek (w specjalnym przypadku), ponieważ transakcja mogła już zostać zatwierdzona. |- |

### <a name="quotaexceededexception"></a>QuotaExceededException
Wyjątek [QuotaExceededException](/dotnet/api/microsoft.azure.servicebus.quotaexceededexception) wskazuje, że przekroczono limit przydziału dla określonej jednostki.

#### <a name="queues-and-topics"></a>Kolejki i tematy
W przypadku kolejek i tematów często jest to rozmiar kolejki. Właściwość komunikat o błędzie zawiera dalsze szczegóły, jak w poniższym przykładzie:

```Output
Microsoft.ServiceBus.Messaging.QuotaExceededException
Message: The maximum entity size has been reached or exceeded for Topic: ‘xxx-xxx-xxx’. 
    Size of entity in bytes:1073742326, Max entity size in bytes:
1073741824..TrackingId:xxxxxxxxxxxxxxxxxxxxxxxxxx, TimeStamp:3/15/2013 7:50:18 AM
```

Komunikat informuje o tym, że temat przekroczył swój limit rozmiaru, w tym przypadku 1 GB (domyślny limit rozmiaru). 

#### <a name="namespaces"></a>Przestrzenie nazw

W przypadku przestrzeni nazw [QuotaExceededException](/dotnet/api/microsoft.azure.servicebus.quotaexceededexception) może wskazywać, że aplikacja przekroczyła maksymalną liczbę połączeń z przestrzenią nazw. Przykład:

```Output
Microsoft.ServiceBus.Messaging.QuotaExceededException: ConnectionsQuotaExceeded for namespace xxx.
<tracking-id-guid>_G12 ---> 
System.ServiceModel.FaultException`1[System.ServiceModel.ExceptionDetail]: 
ConnectionsQuotaExceeded for namespace xxx.
```

#### <a name="common-causes"></a>Typowe przyczyny
Istnieją dwie typowe przyczyny tego błędu: Kolejka utraconych wiadomości i niedziałające odbiorcy komunikatów.

1. **[Kolejka utraconych wiadomości](service-bus-dead-letter-queues.md)** Czytnik kończy się niepowodzeniem, a komunikaty są zwracane do kolejki/tematu, gdy blokada wygaśnie. Może się tak zdarzyć, jeśli czytnik napotka wyjątek, który uniemożliwia jego wywołanie [BrokeredMessage. Complete](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.complete). Gdy wiadomość zostanie odczytana 10 razy, domyślnie przechodzi do kolejki utraconych wiadomości. Takie zachowanie jest kontrolowane przez właściwość [QueueDescription. MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount) i ma wartość domyślną 10. Jako że komunikaty są ustawiane w kolejce utraconych wiadomości, zajmują miejsce.
   
    Aby rozwiązać ten problem, należy odczytać i zakończyć komunikaty z kolejki utraconych wiadomości, tak jak w przypadku każdej innej kolejki. Można użyć metody [FormatDeadLetterPath](/dotnet/api/microsoft.azure.servicebus.entitynamehelper.formatdeadletterpath) , aby ułatwić formatowanie ścieżki kolejki utraconych wiadomości.
2. **Odbiornik został zatrzymany**. Odbiornik przestał odbierać komunikaty z kolejki lub subskrypcji. Aby zidentyfikować ten sposób, należy sprawdzić Właściwość [QueueDescription. MessageCountDetails](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails) , która pokazuje pełny podział komunikatów. Jeśli właściwość [ActiveMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.activemessagecount) jest wysoka lub rosnąca, wiadomości nie są odczytywane tak szybko, jak są zapisywane.

### <a name="timeoutexception"></a>TimeoutException
A [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) wskazuje, że operacja zainicjowana przez użytkownika zajmuje więcej czasu niż limit czasu operacji. 

Należy sprawdzić wartość właściwości [ServicePointManager. DefaultConnectionLimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit) , ponieważ może to spowodować również [przekroczenie limitu czasu](https://msdn.microsoft.com/library/system.timeoutexception.aspx).

#### <a name="queues-and-topics"></a>Kolejki i tematy
W przypadku kolejek i tematów limit czasu jest określony we właściwości [MessagingFactorySettings. OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings) w ramach parametrów połączenia lub przez [ServiceBusConnectionStringBuilder](/dotnet/api/microsoft.azure.servicebus.servicebusconnectionstringbuilder). Komunikat o błędzie, sama może się różnić, ale zawsze zawiera wartość limitu czasu określony dla bieżącej operacji. 

## <a name="connectivity-certificate-or-timeout-issues"></a>Problemy z łącznością, certyfikatem lub limitem czasu
Poniższe kroki mogą pomóc w rozwiązywaniu problemów z łącznością/certyfikatem/przekroczeniem limitu czasu dla wszystkich usług w programie *. servicebus.windows.net. 

- Przejdź do programu lub [wget](https://www.gnu.org/software/wget/) `https://sbwagn2.servicebus.windows.net/`. Pomaga sprawdzić, czy masz problemy z filtrowaniem adresów IP lub siecią wirtualną lub łańcuchem certyfikatów (najbardziej typowe w przypadku korzystania z zestawu Java SDK).
- Uruchom następujące polecenie, aby sprawdzić, czy dowolny port jest zablokowany przez zaporę. W zależności od używanej biblioteki używane są również inne porty. Na przykład: 443, 5672, 9354.

    ```powershell
    tnc sbwagn2.servicebus.windows.net -port 5671
    ```

    W systemie Linux:

    ```shell
    telnet sbwagn2.servicebus.windows.net 5671
    ```
- Gdy występują sporadyczne problemy z łącznością, uruchom następujące polecenie, aby sprawdzić, czy pakiety pozostaną usunięte. To polecenie podejmie próbę nawiązania 25 różnych połączeń TCP co 1 sekundę z usługą, a następnie sprawdzić liczbę zakończonych powodzeniem/niepowodzeniem, a także zobaczyć opóźnienie połączenia TCP. Narzędzie `psping` można pobrać z tego [miejsca](/sysinternals/downloads/psping).

    ```shell
    .\psping.exe -n 25 -i 1 -q yournamespace.servicebus.windows.net:5671 -nobanner     
    ```
    Możesz użyć równoważnych poleceń, jeśli używasz innych narzędzi, takich jak `tnc`, `ping`i tak dalej. 
- Uzyskaj ślad sieci, jeśli poprzednie kroki nie pomagają i nie analizują go ani nie mogą kontaktować się z [Pomoc techniczna firmy Microsoft](https://support.microsoft.com/).


## <a name="next-steps"></a>Następne kroki

Aby uzyskać pełne informacje dotyczące interfejsów API platformy .NET Service Bus, zobacz [Dokumentacja interfejsu API platformy Azure](/dotnet/api/overview/azure/service-bus).

Aby dowiedzieć się więcej na temat [Service Bus](https://azure.microsoft.com/services/service-bus/), zobacz następujące artykuły:

* [Omówienie obsługi komunikatów w usłudze Service Bus](service-bus-messaging-overview.md)
* [Architektura usługi Service Bus](service-bus-architecture.md)

