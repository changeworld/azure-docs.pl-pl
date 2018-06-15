---
title: Wyjątki przekaźnika usługi Azure i sposobu ich rozwiązania | Dokumentacja firmy Microsoft
description: Lista wyjątki przekazywania Azure i wyświetlić sugerowane akcje, które można wykonać w celu ich rozwiązania.
services: service-bus-relay
documentationcenter: na
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: 5f9dd02c-cce0-43b3-8eb8-744f0c27f38c
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/20/2017
ms.author: sethm
ms.openlocfilehash: 1dbe73dac0d09db96ab902909125869959963e6f
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/21/2017
ms.locfileid: "26855865"
---
# <a name="azure-relay-exceptions"></a>Wyjątki przekaźnika usługi Azure

W tym artykule wymieniono niektóre wyjątki, które mogą być generowane przez interfejsów API przekaźnika usługi Azure. To odwołanie jest może ulec zmianie, dlatego sprawdzanie dostępności aktualizacji.

## <a name="exception-categories"></a>Kategorie wyjątku

Interfejsy API przekazywania generowania wyjątków, które mogą można podzielić na następujące kategorie. Liście są również wyświetlić sugerowane akcje, które należy wykonać, aby pomóc w rozwiązaniu wyjątki.

*   **Błąd kodowania użytkownika**: [System.ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx), [System.InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx), [System.OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx), [ System.Runtime.Serialization.SerializationException](https://msdn.microsoft.com/library/system.runtime.serialization.serializationexception.aspx). 

    **Ogólne działanie**: Spróbuj naprawić kod zanim będziesz kontynuować.
*   **Błąd instalacji/konfiguracji**: [System.UnauthorizedAccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx). 

    **Ogólne działanie**: Przejrzyj konfigurację. W razie potrzeby zmień konfigurację.
*   **Wyjątki przejściowa**: [Microsoft.ServiceBus.Messaging.MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception), [Microsoft.ServiceBus.Messaging.ServerBusyException](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception), [ Microsoft.ServiceBus.Messaging.MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception). 

    **Ogólne działanie**: ponów operację lub powiadomić użytkowników.
*   **Pozostałe wyjątki**: [System.Transactions.TransactionException](https://msdn.microsoft.com/library/system.transactions.transactionexception.aspx), [System.TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx). 

    **Ogólne działanie**: specyficzne dla typu wyjątku. Zobacz tabelę w następnej sekcji. 

## <a name="exception-types"></a>Typy wyjątków

W poniższej tabeli wymieniono typów wyjątków obsługi wiadomości i ich przyczyny. Uwagi dotyczące również wyświetlić sugerowane akcje, które należy wykonać, aby pomóc w rozwiązaniu wyjątki.

| **Typ wyjątku** | **Opis** | **Zalecane działanie** | **Należy zwrócić uwagę na automatyczne lub natychmiastowego ponawiania** |
| --- | --- | --- | --- |
| [Limit czasu](https://msdn.microsoft.com/library/system.timeoutexception.aspx) |Serwer nie odpowiedział na żądanej operacji w określonym czasie, które są kontrolowane przez [OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings.operationtimeout). Serwer może zakończył żądanej operacji. Może to nastąpić z sieci lub innych opóźnienia infrastruktury. |Sprawdź stan systemu w celu zachowania spójności, a następnie spróbuj ponownie, jeśli jest to konieczne. Zobacz [TimeoutException](#timeoutexception). |Ponów próbę, może pomóc w niektórych przypadkach; Dodaj logikę ponawiania do kodu. |
| [Nieprawidłowa operacja](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx) |Operacja żądanego użytkownika nie jest dozwolona na serwerze lub usługi. Zawiera komunikat wyjątku, aby uzyskać szczegółowe informacje. |Sprawdź kod i dokumentację. Upewnij się, że żądana operacja jest nieprawidłowa. |Nie pomoże ponów próbę. |
| [Operacja została anulowana](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx) |Podjęta próba wywołania operacji na obiekcie, który już został zamknięty, zostało przerwane lub usunięty. W rzadkich przypadkach transakcja otoczenia zostało już usunięte. |Sprawdź kod i upewnij się, że nie jest wywoływany operacji na usuniętym obiekcie. |Nie pomoże ponów próbę. |
| [Nieautoryzowany dostęp](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx) |[TokenProvider](/dotnet/api/microsoft.servicebus.tokenprovider) obiektu nie można uzyskać tokenu, token jest nieprawidłowy lub token zawiera oświadczenia wymagane do wykonania tej operacji. |Upewnij się, że dostawca tokenu jest tworzony przy użyciu prawidłowych wartości. Sprawdź konfigurację usługi kontroli dostępu. |Ponów próbę, może pomóc w niektórych przypadkach; Dodaj logikę ponawiania do kodu. |
| [Wyjątek argumentu](https://msdn.microsoft.com/library/system.argumentexception.aspx),<br /> [Argument Null](https://msdn.microsoft.com/library/system.argumentnullexception.aspx),<br />[Argument poza zakresem](https://msdn.microsoft.com/library/system.argumentoutofrangeexception.aspx) |Wystąpił co najmniej jeden z następujących czynności:<br />Co najmniej jeden argument przekazany do metody są nieprawidłowe.<br /> Identyfikator URI dostarczony do [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) lub [Utwórz](/dotnet/api/microsoft.servicebus.messaging.messagingfactory.create) zawiera jeden lub więcej segmentów ścieżki.<br />Dostarczony schemat identyfikatora URI do [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) lub [Utwórz](/dotnet/api/microsoft.servicebus.messaging.messagingfactory.create) jest nieprawidłowy. <br />Wartość właściwości jest większy niż 32 KB. |Sprawdź kod wywołujący i upewnij się, że argumenty są poprawne. |Nie pomoże ponów próbę. |
| [Serwer jest zajęty](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception) |Usługa nie będzie mógł przetworzyć żądania w tej chwili. |Klient może czekać przez czas, a następnie spróbuj ponownie wykonać operację. |Klient może próbę po upływie określonego czasu. Jeśli wyniki ponownych prób w różnych wyjątek, sprawdź zachowanie ponów próbę wykonania tego wyjątku. |
| [Przekroczono przydział](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) |Jednostka obsługi komunikatów osiągnęła maksymalny dozwolony rozmiar. |Utworzyć miejsca w jednostce odbieranie komunikatów z jednostek lub jego podkolejki. Zobacz [quotaexceededexception —](#quotaexceededexception). |Ponów próbę mogą ułatwić czy wiadomości zostały usunięte w tym samym czasie. |
| [Przekroczono rozmiar komunikatu](/dotnet/api/microsoft.servicebus.messaging.messagesizeexceededexception) |Ładunek komunikatu przekracza limit 256 KB. Należy pamiętać, że limit 256 KB rozmiar całkowitą komunikatu. Rozmiar całkowitą komunikatu może zawierać właściwości systemu i wszelkie koszty programu Microsoft .NET. |Zmniejsz rozmiar ładunku komunikatu, a następnie spróbuj ponownie wykonać operację. |Nie pomoże ponów próbę. |

## <a name="quotaexceededexception"></a>Quotaexceededexception —

[Quotaexceededexception —](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) wskazuje, że przekroczono limit przydziału dla określonej jednostki.

Do przekazywania, zawijany ten wyjątek [System.ServiceModel.QuotaExceededException](https://msdn.microsoft.com/library/system.servicemodel.quotaexceededexception.aspx), co oznacza, że przekroczono maksymalną liczbę odbiorników dla tego punktu końcowego. Jest to wskazane **MaximumListenersPerEndpoint** wartość komunikat o wyjątku.

## <a name="timeoutexception"></a>TimeoutException
A [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) wskazuje, że użytkownik zainicjował operację trwa dłużej niż limit czasu operacji. 

Sprawdź wartość [ServicePointManager.DefaultConnectionLimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit) właściwości. Osiągnięciu tego limitu mogą dodatkowo powodować [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx).

Do przekazywania może zostać wyświetlony wyjątków przekroczenia limitu czasu, po pierwszym otwarciu połączenia nadawcy przekazywania. Istnieją dwie typowe przyczyny tego wyjątku:

*   [OpenTimeout](https://msdn.microsoft.com/library/wcf.opentimeout.aspx) wartość może być za mały (jeśli nawet przez ułamek sekund).
* Odbiornik przekazywania lokalnego nie może odpowiadać (lub mogą wystąpić problemy reguły zapory, które zabrania odbiorników przyjmuje nowe połączenia klientów) i [OpenTimeout](https://msdn.microsoft.com/library/wcf.opentimeout.aspx) wartość jest mniejsza niż 20 sekund.

Przykład:

```
'System.TimeoutException’: The operation did not complete within the allotted timeout of 00:00:10.
The time allotted to this operation may have been a portion of a longer timeout.
```

### <a name="common-causes"></a>Typowe przyczyny
Istnieją dwie typowe przyczyny tego błędu:

*   **Nieprawidłowa konfiguracja**
    
    Limit czasu operacji może być za mała dla wartości warunki operacyjne. Domyślna wartość limitu czasu operacji na komputerze klienckim SDK wynosi 60 sekund. Sprawdź, czy w kodzie ma wartość inną za mały. Należy pamiętać, że użycie procesora CPU i stan sieci może wpływać na czas potrzebny na zakończenie operacji. Należy dobrze nie jest ustawiony limit czasu operacji na bardzo mała wartość.
*   **Błąd przejściowy usługi**

    Od czasu do czasu usługa przekaźnika mogą wystąpić opóźnienia podczas przetwarzania żądania. To może się zdarzyć, na przykład w okresach dużego natężenia ruchu sieciowego. W takim przypadku ponów operację z opóźnieniem, aż operacja się powiodła. Sprawdź, czy w tej samej operacji zakończy się niepowodzeniem po wielu próbach, [lokacji stanu usługi Azure](https://azure.microsoft.com/status/) aby zobaczyć, czy istnieją znane awarie usługi.

## <a name="next-steps"></a>Kolejne kroki
* [Przekaźnik Azure — często zadawane pytania](relay-faq.md)
* [Tworzenie przestrzeni nazw przekazywania](relay-create-namespace-portal.md)
* [Rozpoczynanie pracy z przekaźnika usługi Azure i .NET](relay-hybrid-connections-dotnet-get-started.md)
* [Rozpoczynanie pracy z przekaźnika usługi Azure i węzła](relay-hybrid-connections-node-get-started.md)

