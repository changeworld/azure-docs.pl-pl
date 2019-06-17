---
title: Wyjątki dotyczące przekaźnika usługi Azure oraz sposobów ich rozwiązywania | Dokumentacja firmy Microsoft
description: Lista wyjątków usługi Azure Relay i sugerowane akcje, które można wykonać w celu ich rozwiązania.
services: service-bus-relay
documentationcenter: na
author: spelluru
manager: timlt
editor: ''
ms.assetid: 5f9dd02c-cce0-43b3-8eb8-744f0c27f38c
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/20/2017
ms.author: spelluru
ms.openlocfilehash: fe8f057443b978e70e7cdd2591affd455fefdca8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60749040"
---
# <a name="azure-relay-exceptions"></a>Usługa Azure wyjątki dotyczące przekaźnika

W tym artykule wymieniono niektóre wyjątki, które mogą być generowane przez interfejsów API usługi Azure Relay. Ta dokumentacja mogą ulec zmianie, więc sprawdzaj aktualizacje.

## <a name="exception-categories"></a>Kategorie wyjątków

Interfejsy API usługi Relay generować wyjątki, które być może można podzielić na następujące kategorie. Na liście również są sugerowane akcje, które można wykonać, aby pomóc w rozwiązaniu wyjątki.

*   **Błąd kodowania użytkownika**: [System.ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx), [System.InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx), [System.OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx), [System.Runtime.Serialization.SerializationException](https://msdn.microsoft.com/library/system.runtime.serialization.serializationexception.aspx). 

    **Akcja ogólna**: Spróbuj naprawić kod, przed kontynuowaniem.
*   **Błąd instalacji/konfiguracji**: [System.UnauthorizedAccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx). 

    **Akcja ogólna**: Sprawdź konfigurację programu. Jeśli to konieczne, należy zmienić konfigurację.
*   **Przejściowych wyjątków**: [Microsoft.ServiceBus.Messaging.MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception), [Microsoft.ServiceBus.Messaging.ServerBusyException](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception), [Microsoft.ServiceBus.Messaging.MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception). 

    **Akcja ogólna**: Spróbuj ponownie wykonać operację lub powiadomić użytkowników.
*   **Inne wyjątki**: [System.Transactions.TransactionException](https://msdn.microsoft.com/library/system.transactions.transactionexception.aspx), [System.TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx). 

    **Akcja ogólna**: Specyficzne dla typu wyjątku. Zobacz tabelę w następnej sekcji. 

## <a name="exception-types"></a>Typy wyjątków

W poniższej tabeli wymieniono typy wyjątków obsługi wiadomości i ich przyczyny. Informacje o również sugerowane akcje, które można wykonać w celu rozwiązania tych wyjątków.

| **Typ wyjątku** | **Opis** | **Sugerowanej akcji** | **Należy zwrócić uwagę na automatyczne lub natychmiastowego ponawiania prób** |
| --- | --- | --- | --- |
| [limit czasu](https://msdn.microsoft.com/library/system.timeoutexception.aspx) |Serwer nie odpowiedział na żądanej operacji w określonym czasie, które są kontrolowane przez [OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings.operationtimeout). Serwer może zakończył żądanej operacji. Może to nastąpić z sieci lub innych infrastruktury opóźnienia. |Sprawdź stan systemu w celu zachowania spójności, a następnie spróbuj ponownie, jeśli to konieczne. Zobacz [TimeoutException](#timeoutexception). |Ponów próbę, może pomóc w niektórych przypadkach; Dodaj logikę ponawiania próby do kodu. |
| [Nieprawidłowa operacja](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx) |Operacja żądanego użytkownika nie jest dozwolona w ramach serwera lub usługi. Wyświetlony komunikat o wyjątku, aby uzyskać szczegółowe informacje. |Sprawdź kod i dokumentację. Upewnij się, że żądana operacja jest nieprawidłowa. |Nie pomoże ponownych prób. |
| [Operacja została anulowana](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx) |Zostanie podjęta próba wywołania operacji na obiekcie, który już został zamknięty, przerwane lub usunięte. W rzadkich przypadkach otoczenia transakcji został już usunięty. |Sprawdź kod i upewnij się, że nie wywołuje operacje na zlikwidowanego obiektu. |Nie pomoże ponownych prób. |
| [Nieautoryzowany dostęp](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx) |[TokenProvider](/dotnet/api/microsoft.servicebus.tokenprovider) obiektu nie można uzyskać tokenu, token jest nieprawidłowy lub token zawiera oświadczenia, wymagane do wykonania tej operacji. |Upewnij się, że dostawcy tokenu, który jest tworzony przy użyciu prawidłowych wartości. Sprawdź konfigurację usługi kontroli dostępu. |Ponów próbę, może pomóc w niektórych przypadkach; Dodaj logikę ponawiania próby do kodu. |
| [Wyjątek argumentu](https://msdn.microsoft.com/library/system.argumentexception.aspx),<br /> [Argument o wartości Null](https://msdn.microsoft.com/library/system.argumentnullexception.aspx),<br />[Argument jest poza zakresem](https://msdn.microsoft.com/library/system.argumentoutofrangeexception.aspx) |Wystąpił co najmniej jeden z następujących czynności:<br />Jeden lub więcej argumentów przekazana do metody są nieprawidłowe.<br /> Identyfikator URI dostarczane do [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) lub [Utwórz](/dotnet/api/microsoft.servicebus.messaging.messagingfactory.create) zawiera jeden lub więcej segmentów ścieżki.<br />Dostarczony schemat identyfikatora URI, do [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) lub [Utwórz](/dotnet/api/microsoft.servicebus.messaging.messagingfactory.create) jest nieprawidłowy. <br />Wartość właściwości jest większa niż 32 KB. |Sprawdź kod wywołujący i upewnij się, że argumenty są poprawne. |Nie pomoże ponownych prób. |
| [Serwer jest zajęty](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception) |Usługa nie jest w stanie przetworzyć żądania w tej chwili. |Klient może poczekaj przez pewien czas, a następnie spróbuj ponownie wykonać operację. |Klient może ponowić próbę po upływie określonego czasu. Jeśli wyniki ponawiania prób w różnych wyjątków, sprawdź zachowanie ponawiania tego wyjątku. |
| [Przekroczono limit przydziału](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) |Jednostki obsługi komunikatów osiągnęła maksymalny dozwolony rozmiar. |Utworzyć miejsca w jednostce otrzymywać wiadomości od jednostki lub jego podkolejek. Zobacz [QuotaExceededException](#quotaexceededexception). |Ponownych prób może pomóc, jeśli komunikaty zostały usunięte w tym samym czasie. |
| [Przekroczono rozmiar komunikatu](/dotnet/api/microsoft.servicebus.messaging.messagesizeexceededexception) |Ładunek komunikatu przekracza limit 256 KB. Należy pamiętać, że limitu 256 KB rozmiaru łączna liczba wiadomości. Rozmiar komunikatu całkowity może zawierać właściwości systemu i wszystkimi narzutami programu Microsoft .NET. |Zmniejsz rozmiar ładunku komunikatu, a następnie spróbuj ponownie wykonać operację. |Nie pomoże ponownych prób. |

## <a name="quotaexceededexception"></a>QuotaExceededException

[QuotaExceededException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) wskazuje, że przekroczony został limit przydziału dla określonej jednostki.

Do przekazywania, jest zawijany ten wyjątek [System.ServiceModel.QuotaExceededException](https://msdn.microsoft.com/library/system.servicemodel.quotaexceededexception.aspx), co oznacza, że przekroczono maksymalną liczbę odbiorników dla tego punktu końcowego. Jest to wskazywane w **MaximumListenersPerEndpoint** wartość komunikat o wyjątku.

## <a name="timeoutexception"></a>TimeoutException
A [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) wskazuje, że operacja zainicjowana przez użytkownika zajmuje więcej czasu niż limit czasu operacji. 

Sprawdź wartość [ServicePointManager.DefaultConnectionLimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit) właściwości. Osiągnięcia tego limitu mogą dodatkowo powodować [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx).

Przy pierwszym otwarciu połączenia nadawcy przekazywania do przekazywania, możesz otrzymać wyjątków przekroczenia limitu czasu. Istnieją dwie typowe przyczyny tego wyjątku:

*   [OpenTimeout](https://msdn.microsoft.com/library/wcf.opentimeout.aspx) wartość może być za mały (jeśli nawet przez część 1 sekunda).
* Odbiornik usługi relay w środowisku lokalnym mogą nie odpowiadać (lub mogą występować w problemów reguły zapory, które uniemożliwiają odbiorników przyjmuje nowe połączenia klientów) oraz [OpenTimeout](https://msdn.microsoft.com/library/wcf.opentimeout.aspx) wartość jest mniejsza niż 20 sekund.

Przykład:

```
'System.TimeoutException’: The operation did not complete within the allotted timeout of 00:00:10.
The time allotted to this operation may have been a portion of a longer timeout.
```

### <a name="common-causes"></a>Typowe przyczyny
Istnieją dwie typowe przyczyny tego błędu:

*   **Nieprawidłowa konfiguracja**
    
    Limit czasu operacji może być za mały dla warunki operacyjne. Domyślna wartość limitu czasu operacji w zestaw SDK klienta wynosi 60 sekund. Sprawdź, czy wartość w kodzie jest równa coś, co jest za mały. Należy pamiętać, że użycie procesora CPU oraz stan sieci może wpływać na czas potrzebny na zakończenie operacji. To dobry pomysł, nie, aby ustawić limit czasu operacji bardzo małych wartości.
*   **Błąd przejściowy usługi**

    Od czasu do czasu usługi przekazywania mogą wystąpić opóźnienia podczas przetwarzania żądania. To może się zdarzyć, na przykład w okresach dużego ruchu. W takim przypadku ponów operację z opóźnieniem, aż operacja się powiedzie. Jeśli w tej samej operacji w dalszym ciągu kończą się niepowodzeniem po wielu prób, sprawdź [witrynę ze stanem usługi Azure](https://azure.microsoft.com/status/) Aby sprawdzić, czy są to znane przerwy w działaniu usługi.

## <a name="next-steps"></a>Kolejne kroki
* [Usługa Azure Relay — często zadawane pytania](relay-faq.md)
* [Tworzenie przestrzeni nazw usługi relay](relay-create-namespace-portal.md)
* [Wprowadzenie do usługi Azure Relay i .NET](relay-hybrid-connections-dotnet-get-started.md)
* [Wprowadzenie do usługi Azure Relay i języka Node](relay-hybrid-connections-node-get-started.md)

