---
title: Wyjątki usługi Azure Relay i sposób ich rozwiązywania | Dokumenty firmy Microsoft
description: Lista wyjątków usługi Azure Relay i sugerowane akcje, które można podjąć, aby pomóc je rozwiązać.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60749040"
---
# <a name="azure-relay-exceptions"></a>Wyjątki usługi Azure Relay

W tym artykule wymieniono niektóre wyjątki, które mogą być generowane przez interfejsy API usługi Azure Relay. To odwołanie może ulec zmianie, więc sprawdź aktualizacje.

## <a name="exception-categories"></a>Kategorie wyjątków

Interfejsy API przekazywania generują wyjątki, które mogą należeć do następujących kategorii. Na liście znajdują się również sugerowane akcje, które można podjąć, aby pomóc rozwiązać wyjątki.

*   **Błąd kodowania użytkownika:** [System.ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx), [System.InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx), [System.OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx), [System.Runtime.Serialization.SerializationException](https://msdn.microsoft.com/library/system.runtime.serialization.serializationexception.aspx). 

    **Ogólne działanie**: Spróbuj naprawić kod przed kontynuowaniem.
*   **Błąd instalatora/konfiguracji**: [System.UnauthorizedAccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx). 

    **Ogólne działanie:** Przejrzyj konfigurację. W razie potrzeby zmień konfigurację.
*   **Wyjątki przejściowe:** [Microsoft.ServiceBus.Messaging.MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception), [Microsoft.ServiceBus.Messaging.ServerBusyException](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception), [Microsoft.ServiceBus.MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception). 

    **Ogólne działanie**: Ponów próbę wykonania operacji lub powiadom użytkowników.
*   **Inne wyjątki:** [System.Transactions.TransactionException](https://msdn.microsoft.com/library/system.transactions.transactionexception.aspx), [System.TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx). 

    **Akcja ogólna**: Specyficzne dla typu wyjątku. Zobacz tabelę w poniższej sekcji. 

## <a name="exception-types"></a>Typy wyjątków

W poniższej tabeli wymieniono typy wyjątków obsługi wiadomości i ich przyczyny. Notatki również sugerowane działania, które można podjąć, aby pomóc rozwiązać wyjątki.

| **Typ wyjątku** | **Opis** | **Zalecane działanie** | **Uwaga dotycząca automatycznego lub natychmiastowego ponawiania próby** |
| --- | --- | --- | --- |
| [Limit czasu](https://msdn.microsoft.com/library/system.timeoutexception.aspx) |Serwer nie odpowiedział na żądaną operację w określonym czasie, który jest kontrolowany przez [OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings.operationtimeout). Serwer mógł ukończyć żądaną operację. Może się to zdarzyć z powodu opóźnień w sieci lub innej infrastrukturze. |Sprawdź stan systemu pod kątem spójności, a następnie ponów próbę, jeśli to konieczne. Zobacz [TimeoutException](#timeoutexception). |W niektórych przypadkach ponowna spróbuj może pomóc; dodaj logikę ponawiania do kodu. |
| [Nieprawidłowa operacja](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx) |Żądana operacja użytkownika jest niedozwolona na serwerze lub usłudze. Zobacz komunikat o wyjątku, aby uzyskać szczegółowe informacje. |Sprawdź kod i dokumentację. Upewnij się, że żądana operacja jest prawidłowa. |Ponów próbę nie pomoże. |
| [Operacja anulowana](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx) |Podejmowana jest próba wywołania operacji na obiekcie, który został już zamknięty, przerwany lub usunięty. W rzadkich przypadkach transakcja otoczenia jest już usuwana. |Sprawdź kod i upewnij się, że nie wywołuje operacji na disposed obiektu. |Ponów próbę nie pomoże. |
| [Nieautoryzowany dostęp](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx) |[Obiekt TokenProvider](/dotnet/api/microsoft.servicebus.tokenprovider) nie może uzyskać tokenu, token jest nieprawidłowy lub token nie zawiera oświadczeń wymaganych do wykonania operacji. |Upewnij się, że dostawca tokenu jest tworzony z poprawnymi wartościami. Sprawdź konfigurację usługi Kontrola dostępu. |W niektórych przypadkach ponowna spróbuj może pomóc; dodaj logikę ponawiania do kodu. |
| [Wyjątek argumentu](https://msdn.microsoft.com/library/system.argumentexception.aspx),<br /> [Argument Null](https://msdn.microsoft.com/library/system.argumentnullexception.aspx),<br />[Argument poza zasięgiem](https://msdn.microsoft.com/library/system.argumentoutofrangeexception.aspx) |Wystąpiło co najmniej jedna z następujących czynności:<br />Jeden lub więcej argumentów dostarczonych do metody są nieprawidłowe.<br /> Identyfikator URI dostarczony do [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) lub [Create](/dotnet/api/microsoft.servicebus.messaging.messagingfactory.create) zawiera jeden lub więcej segmentów ścieżki.<br />Schemat identyfikatorów URI dostarczonych do [namespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) lub [Create](/dotnet/api/microsoft.servicebus.messaging.messagingfactory.create) jest nieprawidłowy. <br />Wartość właściwości jest większa niż 32 KB. |Sprawdź kod wywołujący i upewnij się, że argumenty są poprawne. |Ponów próbę nie pomoże. |
| [Serwer zajęty](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception) |Usługa nie może przetworzyć żądania w tej chwili. |Klient może czekać przez pewien czas, a następnie ponowić próbę wykonania operacji. |Klient może ponowić próbę po określonym przedziale czasu. Jeśli ponowna próba powoduje inny wyjątek, sprawdź zachowanie ponawiania tego wyjątku. |
| [Przekroczono przydział](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) |Jednostka obsługi wiadomości osiągnęła maksymalny dopuszczalny rozmiar. |Utwórz miejsce w encji, odbierając wiadomości z encji lub jej podoka. Zobacz [QuotaExceededException](#quotaexceededexception). |Ponów próbę może pomóc, jeśli wiadomości zostały usunięte w międzyczasie. |
| [Przekroczono rozmiar wiadomości](/dotnet/api/microsoft.servicebus.messaging.messagesizeexceededexception) |Ładunek wiadomości przekracza limit 256 KB. Należy zauważyć, że limit 256 KB jest całkowity rozmiar wiadomości. Całkowity rozmiar wiadomości może zawierać właściwości systemu i wszelkie obciążenie firmy Microsoft .NET. |Zmniejsz rozmiar ładunku wiadomości, a następnie ponów próbę wykonania operacji. |Ponów próbę nie pomoże. |

## <a name="quotaexceededexception"></a>Quotaexceededexception

Wyjątek [QuotaExceededException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) wskazuje, że przekroczono limit przydziału dla określonej jednostki.

W przypadku przekazywania ten wyjątek [zawija System.ServiceModel.QuotaExceededException](https://msdn.microsoft.com/library/system.servicemodel.quotaexceededexception.aspx), co wskazuje, że maksymalna liczba odbiorników została przekroczona dla tego punktu końcowego. Jest to wskazane w **MaximumListenersPerEndpoint** wartość komunikatu wyjątku.

## <a name="timeoutexception"></a>Timeoutexception
[TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) wskazuje, że operacja inicjowana przez użytkownika trwa dłużej niż limit czasu operacji. 

Sprawdź wartość właściwości [ServicePointManager.DefaultConnectionLimit.](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit) Osiągnięcie tego limitu może również spowodować [timeoutexception](https://msdn.microsoft.com/library/system.timeoutexception.aspx).

W przypadku przekazywania mogą pojawić się wyjątki limitu czasu przy pierwszym otwarciu połączenia nadawcy przekazywania. Istnieją dwie typowe przyczyny tego wyjątku:

*   [Wartość OpenTimeout](https://msdn.microsoft.com/library/wcf.opentimeout.aspx) może być zbyt mała (jeśli nawet o ułamek sekundy).
* Odbiornik przekaźnika lokalnego może nie odpowiadać (lub może wystąpić problemy z regułami zapory, które uniemożliwiają odbiornikom akceptowanie nowych połączeń klientów), a wartość [OpenTimeout](https://msdn.microsoft.com/library/wcf.opentimeout.aspx) jest mniejsza niż około 20 sekund.

Przykład:

```
'System.TimeoutException’: The operation did not complete within the allotted timeout of 00:00:10.
The time allotted to this operation may have been a portion of a longer timeout.
```

### <a name="common-causes"></a>Najczęstsze przyczyny
Istnieją dwie typowe przyczyny tego błędu:

*   **Nieprawidłowa konfiguracja**
    
    Limit czasu operacji może być zbyt mały dla stanu operacyjnego. Domyślna wartość limitu czasu operacji w sdk klienta wynosi 60 sekund. Sprawdź, czy wartość w kodzie jest ustawiona na coś za małe. Należy zauważyć, że użycie procesora CPU i stan sieci może mieć wpływ na czas potrzebny do wykonania operacji. Dobrym pomysłem jest, aby nie ustawić limit czasu operacji na bardzo małą wartość.
*   **Błąd usługi przejściowej**

    Od czasu do czasu usługa przekazywania może wystąpić opóźnienia w przetwarzaniu żądań. Może się tak zdarzyć, na przykład, w okresach o dużym natężeniu ruchu. W takim przypadku ponów próbę wykonania operacji po opóźnieniu, dopóki operacja nie zakończy się pomyślnie. Jeśli ta sama operacja nadal zakończy się niepowodzeniem po wielu próbach, sprawdź [witrynę stanu usługi platformy Azure,](https://azure.microsoft.com/status/) aby sprawdzić, czy istnieją znane awarie usługi.

## <a name="next-steps"></a>Następne kroki
* [Często zadawane pytania dotyczące przekazywania platformy Azure](relay-faq.md)
* [Tworzenie przestrzeni nazw przekaźnika](relay-create-namespace-portal.md)
* [Wprowadzenie do usługi Azure Relay i platformy .NET](relay-hybrid-connections-dotnet-get-started.md)
* [Wprowadzenie do usługi Azure Relay i node](relay-hybrid-connections-node-get-started.md)

