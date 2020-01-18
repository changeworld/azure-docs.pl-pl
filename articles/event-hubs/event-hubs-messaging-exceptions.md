---
title: Przewodnik rozwiązywania problemów — Event Hubs platformy Azure | Microsoft Docs
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
ms.date: 01/16/2020
ms.author: shvija
ms.openlocfilehash: 96eaae81a25e361c0041fb02099b8e0cb9da8c28
ms.sourcegitcommit: 2a2af81e79a47510e7dea2efb9a8efb616da41f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/17/2020
ms.locfileid: "76260461"
---
# <a name="troubleshooting-guide-for-azure-event-hubs"></a>Przewodnik rozwiązywania problemów dotyczących usługi Azure Event Hubs
W tym artykule przedstawiono niektóre wyjątki platformy .NET wygenerowane przez Event Hubs .NET Framework interfejsy API, a także inne wskazówki dotyczące rozwiązywania problemów. 

## <a name="event-hubs-messaging-exceptions---net"></a>Event Hubs wyjątki komunikatów — .NET
Ta sekcja zawiera listę wyjątków platformy .NET wygenerowanych przez interfejsy API .NET Framework. 

### <a name="exception-categories"></a>Kategorie wyjątków

Event Hubs interfejsy API platformy .NET generują wyjątki, które mogą należeć do następujących kategorii oraz skojarzoną akcję, którą można wykonać, aby spróbować rozwiązać te problemy.

1. Błąd kodowania użytkownika: [System.ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx), [System.InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx), [System.OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx), [ System.Runtime.Serialization.SerializationException](https://msdn.microsoft.com/library/system.runtime.serialization.serializationexception.aspx). Ogólne działanie: Spróbuj naprawić kod zanim przejdziesz dalej.
2. Błąd instalacji/konfiguracji: [Microsoft.ServiceBus.Messaging.MessagingEntityNotFoundException](/dotnet/api/microsoft.servicebus.messaging.messagingentitynotfoundexception), [Microsoft.Azure.EventHubs.MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.eventhubs.messagingentitynotfoundexception), [ System.UnauthorizedAccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx). Ogólne Akcja: Przejrzyj konfigurację i w razie potrzeby zmienić.
3. Przejściowych wyjątków: [Microsoft.ServiceBus.Messaging.MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception), [Microsoft.ServiceBus.Messaging.ServerBusyException](#serverbusyexception), [ Microsoft.Azure.EventHubs.ServerBusyException](#serverbusyexception), [Microsoft.ServiceBus.Messaging.MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception). Ogólne działanie: spróbuj ponownie wykonać operację lub powiadomić użytkowników.
4. Inne wyjątki: [System.Transactions.TransactionException](https://msdn.microsoft.com/library/system.transactions.transactionexception.aspx), [System.TimeoutException](#timeoutexception), [Microsoft.ServiceBus.Messaging.MessageLockLostException](/dotnet/api/microsoft.servicebus.messaging.messagelocklostexception), [Microsoft.ServiceBus.Messaging.SessionLockLostException](/dotnet/api/microsoft.servicebus.messaging.sessionlocklostexception). Ogólne działanie: specyficzne dla typu wyjątku; Zapoznaj się z tabelą w sekcji poniżej. 

### <a name="exception-types"></a>Typy wyjątków
W poniższej tabeli wymieniono komunikatów typów wyjątków i powoduje, że oraz informacje o sugerowanej akcji, które można wykonać.

| Typ wyjątku | Opis elementu/Przyczyna/przykłady | Sugerowanej akcji | Należy zwrócić uwagę na automatyczne/natychmiastowe ponowienie próby |
| -------------- | -------------------------- | ---------------- | --------------------------------- |
| [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) |Serwer nie odpowiedział na żądaną operację w określonym czasie, który jest kontrolowany przez [OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings). Serwer ukończyć żądanej operacji. Ten wyjątek może wystąpić z powodu opóźnień między siecią lub innymi. |Stan systemu w celu zachowania spójności i spróbuj ponownie, jeśli to konieczne.<br /> Zobacz [TimeoutException](#timeoutexception). | Ponów próbę, może pomóc w niektórych przypadkach; Dodaj logikę ponawiania próby do kodu. |
| [InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx) |Żądana operacja użytkownika nie jest dozwolona w ramach serwera lub usługi. Wyświetlony komunikat o wyjątku, aby uzyskać szczegółowe informacje. Na przykład [Complete](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) generuje ten wyjątek, jeśli wiadomość została odebrana w [ReceiveAndDelete](/dotnet/api/microsoft.servicebus.messaging.receivemode) trybu. | Sprawdź kod i dokumentację. Upewnij się, że żądana operacja jest nieprawidłowa. | Ponowienie próby nie powiedzie się. |
| [OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx) | Zostanie podjęta próba wywołania operacji na obiekcie, który już został zamknięty, przerwane lub usunięte. W rzadkich przypadkach otoczenia transakcji został już usunięty. | Sprawdź kod i upewnij się, że nie wywoła operacji na usuniętym obiekcie. | Ponowienie próby nie powiedzie się. |
| [Unauthorizedaccessexception —](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx) | Obiekt [TokenProvider](/dotnet/api/microsoft.servicebus.tokenprovider) nie może uzyskać tokenu, token jest nieprawidłowy lub token nie zawiera oświadczeń wymaganych do wykonania tej operacji. | Upewnij się, że dostawcy tokenu, który jest tworzony przy użyciu prawidłowych wartości. Sprawdź konfigurację Access Control Service. | Ponów próbę, może pomóc w niektórych przypadkach; Dodaj logikę ponawiania próby do kodu. |
| [ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx)<br /> [ArgumentNullException](https://msdn.microsoft.com/library/system.argumentnullexception.aspx)<br />[Trwa wyjątku ArgumentOutOfRangeException](https://msdn.microsoft.com/library/system.argumentoutofrangeexception.aspx) | Jeden lub więcej argumentów przekazana do metody są nieprawidłowe. Identyfikator URI dostarczane do [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) lub [Utwórz](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) zawiera ścieżkę i jej ewentualny związek. Dostarczony schemat identyfikatora URI, do [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) lub [Utwórz](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) jest nieprawidłowy. Wartość właściwości jest większa niż 32 KB. | Sprawdź kod wywołujący i upewnij się, że argumenty są poprawne. | Nie pomoże ponownych prób. |
| [Microsoft.ServiceBus.Messaging MessagingEntityNotFoundException](/dotnet/api/microsoft.servicebus.messaging.messagingentitynotfoundexception) <br /><br/> [Microsoft.Azure.EventHubs MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.eventhubs.messagingentitynotfoundexception) | Jednostka skojarzone z operacją nie istnieje lub został usunięty. | Upewnij się, że jednostka istnieje. | Nie pomoże ponownych prób. |
| [MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception) | Klient nie jest w stanie nawiązać połączenie z Centrum zdarzeń. |Upewnij się, że podana nazwa hosta jest poprawna, a host jest nieosiągalny. | Ponów próbę, może pomóc w przypadku sporadycznych problemów z łącznością. |
| [Microsoft. ServiceBus. Messaging wyjątek serverbusyexception](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception) <br /> <br/>[Microsoft.Azure.EventHubs ServerBusyException](/dotnet/api/microsoft.azure.eventhubs.serverbusyexception) | Usługa nie jest w stanie przetworzyć żądania w tej chwili. | Klient może poczekaj przez pewien czas, a następnie spróbuj ponownie wykonać operację. <br /> Zobacz [ServerBusyException](#serverbusyexception). | Klient może podjąć kolejną próbę po upływie niektórych interwału. Jeśli ponowienie próby w wyniku inny wyjątek, sprawdź, ponów próbę zachowania tego wyjątku. |
| [MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception) | Ogólne komunikaty wyjątku, który może zostać wygenerowany w następujących przypadkach: jest podejmowana próba tworzenia [QueueClient](/dotnet/api/microsoft.servicebus.messaging.queueclient) za pomocą nazwy lub ścieżki, który należy do innej jednostki typu (na przykład temat). Podjęto próbę wysłania komunikatu o rozmiarze większym niż 1 MB. Serwer lub Usługa napotkała błąd podczas przetwarzania żądania. Wyświetlony komunikat o wyjątku, aby uzyskać szczegółowe informacje. Wyjątek ten jest zazwyczaj wyjątku przejściowego. | Sprawdź kod i upewnij się, że są używane tylko obiekty możliwe do serializacji treści komunikatu lub użyć serializatora niestandardowego. Sprawdź w dokumentacji obsługiwane typy wartości właściwości i tylko użycie obsługiwane. Sprawdź [IsTransient](/dotnet/api/microsoft.servicebus.messaging.messagingexception) właściwości. Jeśli jest **true**, ponowić operację. | Sposób ponawiania jest niezdefiniowana i nie może pomóc. |
| [MessagingEntityAlreadyExistsException](/dotnet/api/microsoft.servicebus.messaging.messagingentityalreadyexistsexception) | Podjęto próbę utworzenia jednostki o nazwie, która jest już używana przez inną jednostkę w tej przestrzeni nazw usługi. | Usuwanie istniejącej jednostki lub wybierz inną nazwę dla obiektu, który ma zostać utworzony. | Nie pomoże ponownych prób. |
| [QuotaExceededException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) | Jednostki obsługi komunikatów osiągnęła maksymalny dozwolony rozmiar. Ten wyjątek może się zdarzyć, jeśli została już otwarta.%12%0 maksymalną liczbę odbiorców (czyli 5) na poziomie grupy-klient. | Utworzyć miejsca w jednostce otrzymywać wiadomości od jednostki lub jego podkolejek. <br /> Zobacz [QuotaExceededException](#quotaexceededexception) | Ponownych prób może pomóc, jeśli komunikaty zostały usunięte w tym samym czasie. |
| [MessagingEntityDisabledException](/dotnet/api/microsoft.servicebus.messaging.messagingentitydisabledexception) | Żądania dla operacji środowiska uruchomieniowego w jednostce wyłączone. |Aktywuj jednostki. | Ponownych prób może pomóc, jeśli jednostki został aktywowany w międzyczasie. |
| [Microsoft.ServiceBus.Messaging MessageSizeExceededException](/dotnet/api/microsoft.servicebus.messaging.messagesizeexceededexception) <br /><br/> [Microsoft.Azure.EventHubs MessageSizeExceededException](/dotnet/api/microsoft.azure.eventhubs.messagesizeexceededexception) | Ładunek komunikatu przekracza limit 1 MB. Ten limit 1 MB jest przeznaczony dla łącznej wiadomości, która może obejmować właściwości systemu i wszystkie obciążenia platformy .NET. | Zmniejsz rozmiar ładunku komunikatu, a następnie spróbuj ponownie wykonać operację. |Nie pomoże ponownych prób. |

### <a name="quotaexceededexception"></a>QuotaExceededException
Wyjątek [QuotaExceededException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) wskazuje, że przekroczono limit przydziału dla określonej jednostki.

Ten wyjątek może się zdarzyć, jeśli została już otwarta.%12%0 maksymalną liczbę odbiorców (5) na poziomie grupy-klient.

#### <a name="event-hubs"></a>Centra zdarzeń
Usługa Event Hubs ma limit 20 grup odbiorców dla Centrum zdarzeń. Podczas próby utworzenia więcej, pojawia się [QuotaExceededException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception). 

### <a name="timeoutexception"></a>TimeoutException
A [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) wskazuje, że operacja zainicjowana przez użytkownika zajmuje więcej czasu niż limit czasu operacji. 

Dla usługi Event Hubs może zostać określony jako część ciągu połączenia lub za pośrednictwem [ServiceBusConnectionStringBuilder](/dotnet/api/microsoft.servicebus.servicebusconnectionstringbuilder). Komunikat o błędzie, sama może się różnić, ale zawsze zawiera wartość limitu czasu określony dla bieżącej operacji. 

#### <a name="common-causes"></a>Typowe przyczyny
Istnieją dwie typowe przyczyny tego błędu: niepoprawnej konfiguracji lub Błąd przejściowy usługi.

1. **Nieprawidłowa konfiguracja** limit czasu operacji może być za mały dla warunki operacyjne. Domyślna wartość limitu czasu operacji w zestaw SDK klienta wynosi 60 sekund. Sprawdź, czy kod zawiera wartość równa coś, co jest za mały. Warunek użycia sieci i procesora może mieć wpływ na czas potrzebny na wykonanie określonej operacji, więc limit czasu operacji nie powinien być ustawiony na małą wartość.
2. **Błąd przejściowy usługi** napotykać opóźnienia podczas przetwarzania żądań, na przykład w okresach dużego ruchu usługi Event Hubs. W takich przypadkach możesz ponowić próbę operacji po chwili, aż operacja się powiedzie. Jeśli w tej samej operacji nadal kończy się niepowodzeniem po wielu próbach, odwiedź stronę [witrynę ze stanem usługi Azure](https://azure.microsoft.com/status/) aby zobaczyć, czy istnieją jakiekolwiek znane awarie usług.

### <a name="serverbusyexception"></a>ServerBusyException

A [Microsoft.ServiceBus.Messaging.ServerBusyException](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception) lub [Microsoft.Azure.EventHubs.ServerBusyException](/dotnet/api/microsoft.azure.eventhubs.serverbusyexception) wskazuje, że serwer jest przeciążony. Istnieją dwa kodów błędów dla tego wyjątku.

#### <a name="error-code-50002"></a>Kod błędu: 50002

Ten błąd może wystąpić jeden z dwóch powodów:

1. Obciążenie nie jest równomiernie dystrybuowane we wszystkich partycjach w centrum zdarzeń, a jedna z nich trafi na ograniczenie lokalnej jednostki przepływności.
    
    Rozwiązanie: Przeglądu strategii dystrybucji partycji lub spróbować [EventHubClient.Send(eventDataWithOutPartitionKey)](/dotnet/api/microsoft.servicebus.messaging.eventhubclient) może pomóc.

2. Przestrzeń nazw Event Hubs nie ma wystarczającej liczby jednostek przepływności (można sprawdzić ekran **metryk** w oknie Event Hubs przestrzeni nazw w [Azure Portal](https://portal.azure.com) , aby potwierdzić). W portalu są wyświetlane zagregowane informacje (1 minuty), ale mierzy przepływność w czasie rzeczywistym, więc jest to tylko oszacowanie.

    Rozwiązanie: Zwiększenie jednostek przepływności w przestrzeni nazw może pomóc. Użytkownik może wykonać tę operację w portalu, w **skalowania** okna ekranu przestrzeni nazw usługi Event Hubs. Alternatywnie można użyć [automatyczne rozszerzanie](event-hubs-auto-inflate.md).

#### <a name="error-code-50001"></a>Kod błędu: 50001

Ten błąd rzadko powinien wystąpić. Występuje, jeśli działanie kodu dla swojego obszaru nazw kontenera ma za mało procesora CPU — rozpoczyna się nie więcej niż kilka sekund, zanim moduł równoważenia obciążenia usługi Event Hubs.

#### <a name="limit-on-calls-to-the-getruntimeinformation-method"></a>Limit wywołań metody GetRuntimeInformation —
Usługa Azure Event Hubs obsługuje do 50 wywołań na sekundę w ciągu sekundy do GetRuntimeInfo. Po osiągnięciu limitu może zostać wyświetlony wyjątek podobny do następującego:

```
ExceptionId: 00000000000-00000-0000-a48a-9c908fbe84f6-ServerBusyException: The request was terminated because the namespace 75248:aaa-default-eventhub-ns-prodb2b is being throttled. Error code : 50001. Please wait 10 seconds and try again.
```

## <a name="connectivity-certificate-or-timeout-issues"></a>Problemy z łącznością, certyfikatem lub limitem czasu
Poniższe kroki mogą pomóc w rozwiązywaniu problemów z łącznością/certyfikatem/przekroczeniem limitu czasu dla wszystkich usług w programie *. servicebus.windows.net. 

- Przejdź do programu lub [wget](https://www.gnu.org/software/wget/) `https://<yournamespacename>.servicebus.windows.net/`. Pomaga sprawdzić, czy masz problemy z filtrowaniem adresów IP lub siecią wirtualną lub łańcuchem certyfikatów (najbardziej typowe w przypadku korzystania z zestawu Java SDK).
- Uruchom następujące polecenie, aby sprawdzić, czy dowolny port jest zablokowany przez zaporę. Używane porty to 443 (HTTPS), 5671 (AMQP) i 9093 (Kafka). W zależności od używanej biblioteki używane są również inne porty. Oto przykładowe polecenie, które sprawdza, czy port 5671 jest zablokowany.

    ```powershell
    tnc <yournamespacename>.servicebus.windows.net -port 5671
    ```

    W systemie Linux:

    ```shell
    telnet <yournamespacename>.servicebus.windows.net 5671
    ```
    
    Przykład pomyślnego komunikatu:
    
    ```xml
    <feed xmlns="http://www.w3.org/2005/Atom"><title type="text">Publicly Listed Services</title><subtitle type="text">This is the list of publicly-listed services currently available.</subtitle><id>uuid:27fcd1e2-3a99-44b1-8f1e-3e92b52f0171;id=30</id><updated>2019-12-27T13:11:47Z</updated><generator>Service Bus 1.1</generator></feed>
    ```
    
    Przykładowy komunikat o błędzie błędu:

    ```json
    <Error>
        <Code>400</Code>
        <Detail>
            Bad Request. To know more visit https://aka.ms/sbResourceMgrExceptions. . TrackingId:b786d4d1-cbaf-47a8-a3d1-be689cda2a98_G22, SystemTracker:NoSystemTracker, Timestamp:2019-12-27T13:12:40
        </Detail>
    </Error>
    ```
- Gdy występują sporadyczne problemy z łącznością, uruchom następujące polecenie, aby sprawdzić, czy pakiety pozostaną usunięte. To polecenie próbuje nawiązać 25 różnych połączeń TCP co 1 sekundę z usługą. Następnie można sprawdzić, ile z nich zakończyło się powodzeniem/niepowodzeniem, i zobaczyć także opóźnienia połączeń TCP. Narzędzie `psping` można pobrać z tego [miejsca](/sysinternals/downloads/psping).

    ```shell
    .\psping.exe -n 25 -i 1 -q <yournamespacename>.servicebus.windows.net:5671 -nobanner     
    ```
    Możesz użyć równoważnych poleceń, jeśli używasz innych narzędzi, takich jak `tnc`, `ping`i tak dalej. 
- Uzyskaj ślad sieci, jeśli poprzednie kroki nie pomagają i nie analizują go przy użyciu narzędzi, takich jak [Wireshark](https://www.wireshark.org/). W razie konieczności skontaktuj się z [Pomoc techniczna firmy Microsoft](https://support.microsoft.com/) . 

## <a name="next-steps"></a>Następne kroki

Następujące linki pozwalają dowiedzieć się więcej na temat usługi Event Hubs:

* [Przegląd usługi Event Hubs](event-hubs-what-is-event-hubs.md)
* [Tworzenie centrum zdarzeń](event-hubs-create.md)
* [Event Hubs — często zadawane pytania](event-hubs-faq.md)
