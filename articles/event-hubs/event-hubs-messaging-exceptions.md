---
title: Przewodnik po rozwiązywaniu problemów — Usługi Azure Event Hubs | Dokumenty firmy Microsoft
description: Ten artykuł zawiera listę wyjątków obsługi wiadomości usługi Azure Event Hubs i sugerowanych akcji.
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
ms.date: 01/16/2020
ms.author: shvija
ms.openlocfilehash: de5b95bd10bf72f60ba5d63c4b3a799556fcce33
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76309782"
---
# <a name="troubleshooting-guide-for-azure-event-hubs"></a>Przewodnik rozwiązywania problemów z usługą Azure Event Hubs
Ten artykuł zawiera niektóre wyjątki platformy .NET generowane przez interfejsy API programu Event Hubs .NET Framework, a także inne wskazówki dotyczące rozwiązywania problemów. 

## <a name="event-hubs-messaging-exceptions---net"></a>Wyjątki obsługi wiadomości w centrach zdarzeń — .NET
W tej sekcji wymieniono wyjątki platformy .NET generowane przez interfejsy API programu .NET Framework. 

### <a name="exception-categories"></a>Kategorie wyjątków

Interfejsy API usługi Event Hubs .NET generują wyjątki, które mogą należeć do następujących kategorii, wraz ze skojarzoną akcją, którą można wykonać, aby spróbować je naprawić.

1. Błąd kodowania użytkownika: [System.ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx), [System.InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx), [System.OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx), [System.Runtime.Serialization.SerializationException](https://msdn.microsoft.com/library/system.runtime.serialization.serializationexception.aspx). Ogólne działanie: spróbuj naprawić kod przed kontynuowaniem.
2. Błąd instalacji/konfiguracji: [Microsoft.ServiceBus.Messaging.MessagingEntityNotFoundException](/dotnet/api/microsoft.servicebus.messaging.messagingentitynotfoundexception), [Microsoft.Azure.EventHubs.MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.eventhubs.messagingentitynotfoundexception), [System.UnauthorizedAccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx). Ogólne działanie: przejrzyj konfigurację i w razie potrzeby zmień.
3. Wyjątki przejściowe: [Microsoft.ServiceBus.Messaging.MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception), [Microsoft.ServiceBus.Messaging.ServerBusyException](#serverbusyexception), [Microsoft.Azure.EventHubs.ServerBusyException](#serverbusyexception), [Microsoft.ServiceBus.MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception). Ogólne działanie: ponów próbę wykonania operacji lub powiadomienie użytkowników.
4. Inne wyjątki: [System.Transactions.TransactionException](https://msdn.microsoft.com/library/system.transactions.transactionexception.aspx), [System.TimeoutException](#timeoutexception), [Microsoft.ServiceBus.Messaging.MessageLostException](/dotnet/api/microsoft.servicebus.messaging.messagelocklostexception), [Microsoft.ServiceBus.Messaging.SessionLockLostException](/dotnet/api/microsoft.servicebus.messaging.sessionlocklostexception). Działanie ogólne: specyficzne dla typu wyjątku; patrz tabela w poniższej sekcji. 

### <a name="exception-types"></a>Typy wyjątków
W poniższej tabeli wymieniono typy wyjątków obsługi wiadomości i ich przyczyny oraz uwagi sugerowane działania, które można podjąć.

| Typ wyjątku | Opis/Przyczyna/Przykłady | Sugerowana akcja | Uwaga dotycząca automatycznego/natychmiastowego ponowiania prób |
| -------------- | -------------------------- | ---------------- | --------------------------------- |
| [Timeoutexception](https://msdn.microsoft.com/library/system.timeoutexception.aspx) |Serwer nie odpowiedział na żądaną operację w określonym czasie, który jest kontrolowany przez [OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings). Serwer mógł ukończyć żądaną operację. Ten wyjątek może się zdarzyć z powodu opóźnień sieci lub innej infrastruktury. |Sprawdź stan systemu pod kątem spójności i w razie potrzeby ponów próbę.<br /> Zobacz [TimeoutException](#timeoutexception). | W niektórych przypadkach ponowna spróbuj może pomóc; dodaj logikę ponawiania do kodu. |
| [InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx) |Żądana operacja użytkownika jest niedozwolona na serwerze lub usłudze. Zobacz komunikat o wyjątku, aby uzyskać szczegółowe informacje. Na przykład [Complete](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) generuje ten wyjątek, jeśli wiadomość została odebrana w trybie [ReceiveAndDelete.](/dotnet/api/microsoft.servicebus.messaging.receivemode) | Sprawdź kod i dokumentację. Upewnij się, że żądana operacja jest prawidłowa. | Ponów próbę nie pomoże. |
| [Operationcanceledexception](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx) | Podejmowana jest próba wywołania operacji na obiekcie, który został już zamknięty, przerwany lub usunięty. W rzadkich przypadkach transakcja otoczenia jest już usuwana. | Sprawdź kod i upewnij się, że nie wywołuje operacji na disposed obiektu. | Ponów próbę nie pomoże. |
| [Unauthorizedaccessexception](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx) | [Obiekt TokenProvider](/dotnet/api/microsoft.servicebus.tokenprovider) nie może uzyskać tokenu, token jest nieprawidłowy lub token nie zawiera oświadczeń wymaganych do wykonania operacji. | Upewnij się, że dostawca tokenu jest tworzony z poprawnymi wartościami. Sprawdź konfigurację usługi kontroli dostępu. | W niektórych przypadkach ponowna spróbuj może pomóc; dodaj logikę ponawiania do kodu. |
| [Zeksuw argumentów](https://msdn.microsoft.com/library/system.argumentexception.aspx)<br /> [Argumentnullexception](https://msdn.microsoft.com/library/system.argumentnullexception.aspx)<br />[Kreślenie argumentów](https://msdn.microsoft.com/library/system.argumentoutofrangeexception.aspx) | Jeden lub więcej argumentów dostarczonych do metody są nieprawidłowe. Identyfikator URI dostarczony do [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) lub [Create](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) zawiera segmenty ścieżek. Schemat identyfikatorów URI dostarczonych do [namespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) lub [Create](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) jest nieprawidłowy. Wartość właściwości jest większa niż 32 KB. | Sprawdź kod wywołujący i upewnij się, że argumenty są poprawne. | Ponów próbę nie pomoże. |
| [Microsoft.ServiceBus.Messaging MessagingEntityNotFoundException](/dotnet/api/microsoft.servicebus.messaging.messagingentitynotfoundexception) <br /><br/> [Microsoft.Azure.EventHubs MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.eventhubs.messagingentitynotfoundexception) | Jednostka skojarzona z operacją nie istnieje lub została usunięta. | Upewnij się, że jednostka istnieje. | Ponów próbę nie pomoże. |
| [MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception) | Klient nie może ustanowić połączenia z Centrum zdarzeń. |Upewnij się, że podana nazwa hosta jest poprawna i host jest osiągalny. | Ponów próbę może pomóc, jeśli występują sporadyczne problemy z łącznością. |
| [Microsoft.ServiceBus.Messaging ServerBusyException](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception) <br /> <br/>[Microsoft.Azure.EventHubs ServerBusyException](/dotnet/api/microsoft.azure.eventhubs.serverbusyexception) | Usługa nie może przetworzyć żądania w tej chwili. | Klient może czekać przez pewien czas, a następnie ponowić próbę wykonania operacji. <br /> Zobacz [ServerBusyException](#serverbusyexception). | Klient może ponowić próbę po pewnym przedziale czasu. Jeśli ponowna próba powoduje inny wyjątek, sprawdź ponowić próbę zachowania tego wyjątku. |
| [MessagingException (Nieekceptor wiadomości)](/dotnet/api/microsoft.servicebus.messaging.messagingexception) | Wyjątek obsługi wiadomości ogólne, które mogą być generowane w następujących przypadkach: Podejmowana jest próba [utworzenia QueueClient](/dotnet/api/microsoft.servicebus.messaging.queueclient) przy użyciu nazwy lub ścieżki, która należy do innego typu jednostki (na przykład temat). Podejmowana jest próba wysłania wiadomości większej niż 1 MB. Serwer lub usługa napotkał błąd podczas przetwarzania żądania. Zobacz komunikat o wyjątku, aby uzyskać szczegółowe informacje. Ten wyjątek jest zwykle przejściowy wyjątek. | Sprawdź kod i upewnij się, że tylko serializable obiekty są używane dla treści wiadomości (lub użyć niestandardowego serializatora). Sprawdź w dokumentacji obsługiwane typy wartości właściwości i używać tylko obsługiwane typy. Sprawdź [IsTransient](/dotnet/api/microsoft.servicebus.messaging.messagingexception) właściwości. Jeśli jest to **prawda,** można ponowić próbę wykonania operacji. | Ponów próbę zachowania jest niezdefiniowana i może nie pomóc. |
| [MessagingEntityNajednajwiększośćexception](/dotnet/api/microsoft.servicebus.messaging.messagingentityalreadyexistsexception) | Spróbuj utworzyć encję o nazwie, która jest już używana przez inną jednostkę w tym obszarze nazw usługi. | Usuń istniejącą encję lub wybierz inną nazwę dla tworzonej encji. | Ponów próbę nie pomoże. |
| [Quotaexceededexception](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) | Jednostka obsługi wiadomości osiągnęła maksymalny dopuszczalny rozmiar. Ten wyjątek może się zdarzyć, jeśli maksymalna liczba odbiorników (czyli 5) została już otwarta na poziomie grupy dla odbiorców. | Utwórz miejsce w encji, odbierając wiadomości z encji lub jej podoka. <br /> Zobacz [QuotaExceededException](#quotaexceededexception) | Ponów próbę może pomóc, jeśli wiadomości zostały usunięte w międzyczasie. |
| [MessagingEntityDisabledException](/dotnet/api/microsoft.servicebus.messaging.messagingentitydisabledexception) | Żądanie operacji środowiska uruchomieniowego w wyłączonej jednostce. |Aktywuj encję. | Ponów próbę może pomóc, jeśli jednostka została aktywowana w międzyczasie. |
| [Microsoft.ServiceBus.Messaging MessageSizeExceedException](/dotnet/api/microsoft.servicebus.messaging.messagesizeexceededexception) <br /><br/> [Microsoft.Azure.EventHubs MessageSizeExceedException](/dotnet/api/microsoft.azure.eventhubs.messagesizeexceededexception) | Ładunek wiadomości przekracza limit 1 MB. Ten limit 1 MB dotyczy całkowitej wiadomości, która może zawierać właściwości systemu i wszelkie obciążenie .NET. | Zmniejsz rozmiar ładunku wiadomości, a następnie ponów próbę wykonania operacji. |Ponów próbę nie pomoże. |

### <a name="quotaexceededexception"></a>Quotaexceededexception
Wyjątek [QuotaExceededException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) wskazuje, że przekroczono limit przydziału dla określonej jednostki.

Ten wyjątek może się zdarzyć, jeśli maksymalna liczba odbiorników (5) została już otwarta na poziomie grupy dla odbiorców.

#### <a name="event-hubs"></a>Usługa Event Hubs
Centra zdarzeń ma limit 20 grup odbiorców na Centrum zdarzeń. Podczas próby utworzenia więcej, otrzymasz [QuotaExceededException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception). 

### <a name="timeoutexception"></a>Timeoutexception
[TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) wskazuje, że operacja inicjowana przez użytkownika trwa dłużej niż limit czasu operacji. 

W przypadku centrów zdarzeń limit czasu jest określony jako część ciągu połączenia lub za pośrednictwem [usługi ServiceBusConnectionStringBuilder](/dotnet/api/microsoft.servicebus.servicebusconnectionstringbuilder). Sam komunikat o błędzie może się różnić, ale zawsze zawiera wartość limitu czasu określoną dla bieżącej operacji. 

#### <a name="common-causes"></a>Najczęstsze przyczyny
Istnieją dwie typowe przyczyny tego błędu: niepoprawna konfiguracja lub błąd usługi przejściowej.

1. **Nieprawidłowa konfiguracja** Limit czasu operacji może być zbyt mały dla stanu operacyjnego. Domyślna wartość limitu czasu operacji w sdk klienta wynosi 60 sekund. Sprawdź, czy kod ma ustawioną wartość na zbyt małą. Warunek użycia sieci i procesora CPU może mieć wpływ na czas potrzebny na ukończenie określonej operacji, więc limit czasu operacji nie powinien być ustawiony na małą wartość.
2. **Błąd usługi przejściowej** Czasami usługa Event Hubs może wystąpić opóźnienia w przetwarzaniu żądań; na przykład w okresach wysokiego natężenia ruchu. W takich przypadkach można ponowić próbę wykonania operacji po opóźnieniu, dopóki operacja nie zakończy się pomyślnie. Jeśli ta sama operacja nadal nie powiedzie się po wielu próbach, odwiedź [witrynę stanu usługi platformy Azure,](https://azure.microsoft.com/status/) aby sprawdzić, czy występują znane awarie usługi.

### <a name="serverbusyexception"></a>ServerBusyException (Nieekceptycja serwera)

A [Microsoft.ServiceBus.Messaging.ServerBusyException](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception) lub [Microsoft.Azure.EventHubs.ServerBusyException](/dotnet/api/microsoft.azure.eventhubs.serverbusyexception) wskazuje, że serwer jest przeciążony. Istnieją dwa odpowiednie kody błędów dla tego wyjątku.

#### <a name="error-code-50002"></a>Kod błędu 50002

Ten błąd może wystąpić z jednego z dwóch powodów:

1. Obciążenie nie jest równomiernie rozłożone na wszystkie partycje w centrum zdarzeń, a jedna partycja uderza w ograniczenie jednostki przepływności lokalnej.
    
    Rozwiązanie: Zmiana strategii dystrybucji partycji lub wypróbowanie [eventHubClient.Send(eventDataWithOutPartitionKey)](/dotnet/api/microsoft.servicebus.messaging.eventhubclient) może pomóc.

2. Obszar nazw centrum zdarzeń nie ma wystarczających jednostek przepływności (można sprawdzić ekran **metryki** w oknie obszaru nazw usługi Event Hubs w [witrynie Azure portal,](https://portal.azure.com) aby potwierdzić). Portal pokazuje zagregowane (1 minuta) informacje, ale mierzymy przepływność w czasie rzeczywistym — więc jest to tylko oszacowanie.

    Rozwiązanie: Zwiększenie jednostek przepływności w obszarze nazw może pomóc. Tę operację można wykonać w portalu w oknie **Skalowanie** obszaru nazw centrum zdarzeń. Możesz też użyć [funkcji Auto-nadmuchiwane](event-hubs-auto-inflate.md).

#### <a name="error-code-50001"></a>Kod błędu 50001

Ten błąd powinien rzadko występować. Dzieje się tak, gdy kod kontenera z uruchomionym dla obszaru nazw jest niski na procesorze CPU — nie więcej niż kilka sekund przed rozpoczęciem modułu równoważenia obciążenia centrum zdarzeń.

#### <a name="limit-on-calls-to-the-getruntimeinformation-method"></a>Ogranicz liczbę wywołań metody GetRuntimeInformation
Usługa Azure Event Hubs obsługuje do 50 wywołań na sekundę do GetRuntimeInfo na sekundę. Po osiągnięciu limitu może pojawić się wyjątek podobny do następującego:

```
ExceptionId: 00000000000-00000-0000-a48a-9c908fbe84f6-ServerBusyException: The request was terminated because the namespace 75248:aaa-default-eventhub-ns-prodb2b is being throttled. Error code : 50001. Please wait 10 seconds and try again.
```

## <a name="connectivity-certificate-or-timeout-issues"></a>Problemy z łącznością, certyfikatem lub limitem czasu
Poniższe kroki mogą pomóc w rozwiązywaniu problemów z łącznością/certyfikatem/limitem czasu dla wszystkich usług w obszarze *.servicebus.windows.net. 

- Przejdź do lub [wget](https://www.gnu.org/software/wget/) `https://<yournamespacename>.servicebus.windows.net/`. Pomaga sprawdzić, czy masz filtrowanie IP lub problemy z siecią wirtualną lub łańcuchem certyfikatów (najczęściej podczas korzystania z java SDK).

    Przykład pomyślnej wiadomości:
    
    ```xml
    <feed xmlns="http://www.w3.org/2005/Atom"><title type="text">Publicly Listed Services</title><subtitle type="text">This is the list of publicly-listed services currently available.</subtitle><id>uuid:27fcd1e2-3a99-44b1-8f1e-3e92b52f0171;id=30</id><updated>2019-12-27T13:11:47Z</updated><generator>Service Bus 1.1</generator></feed>
    ```
    
    Przykład komunikatu o błędzie błędu:

    ```json
    <Error>
        <Code>400</Code>
        <Detail>
            Bad Request. To know more visit https://aka.ms/sbResourceMgrExceptions. . TrackingId:b786d4d1-cbaf-47a8-a3d1-be689cda2a98_G22, SystemTracker:NoSystemTracker, Timestamp:2019-12-27T13:12:40
        </Detail>
    </Error>
    ```
- Uruchom następujące polecenie, aby sprawdzić, czy jakikolwiek port jest zablokowany na zaporze. Używane porty to 443 (HTTPS), 5671 (AMQP) i 9093 (Kafka). W zależności od używanej biblioteki używane są również inne porty. Oto przykładowe polecenie, które sprawdza, czy port 5671 jest zablokowany.

    ```powershell
    tnc <yournamespacename>.servicebus.windows.net -port 5671
    ```

    W systemie Linux:

    ```shell
    telnet <yournamespacename>.servicebus.windows.net 5671
    ```
- W przypadku wystąpienia sporadyczne problemy z łącznością, uruchom następujące polecenie, aby sprawdzić, czy istnieją jakieś porzucone pakiety. To polecenie spróbuje ustanowić 25 różnych połączeń TCP co 1 sekundę z usługą. Następnie można sprawdzić, ile z nich zakończyło się pomyślnie/nie powiodło się, a także wyświetlić opóźnienie połączenia TCP. Możesz pobrać `psping` narzędzie [stąd](/sysinternals/downloads/psping).

    ```shell
    .\psping.exe -n 25 -i 1 -q <yournamespacename>.servicebus.windows.net:5671 -nobanner     
    ```
    Można użyć równoważnych poleceń, jeśli używasz `tnc` `ping`innych narzędzi, takich jak , i tak dalej. 
- Uzyskaj śledzenie sieci, jeśli poprzednie kroki nie pomagają i analizować go za pomocą narzędzi, takich jak [Wireshark](https://www.wireshark.org/). W razie potrzeby skontaktuj się z [pomocą techniczną firmy Microsoft.](https://support.microsoft.com/) 

## <a name="next-steps"></a>Następne kroki

Następujące linki pozwalają dowiedzieć się więcej na temat usługi Event Hubs:

* [Przegląd usługi Event Hubs](event-hubs-what-is-event-hubs.md)
* [Tworzenie centrum zdarzeń](event-hubs-create.md)
* [Event Hubs — często zadawane pytania](event-hubs-faq.md)
