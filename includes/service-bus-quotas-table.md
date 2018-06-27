---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: service-bus-messaging
author: sethmanheim
ms.service: service-bus-messaging
ms.topic: include
ms.date: 06/04/2018
ms.author: sethm
ms.custom: include file
ms.openlocfilehash: 0ff1e31e52c7db5c41f92cb9e4cb1a17f28dea6f
ms.sourcegitcommit: b7290b2cede85db346bb88fe3a5b3b316620808d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/05/2018
ms.locfileid: "34806404"
---
W poniższej tabeli przedstawiono informacje o limicie przydziału specyficzne dla komunikatów usługi Service Bus. Aby uzyskać informacje o cenach i innych przydziały dla usługi Service Bus, zobacz [cennik usługi magistrali](https://azure.microsoft.com/pricing/details/service-bus/) omówienie.

| Nazwa przydziału | Zakres | Uwagi | Wartość |
| --- | --- | --- | --- | --- |
| Maksymalna liczba podstawowych / standardowe przestrzeni nazw dla subskrypcji platformy Azure |Przestrzeń nazw |Kolejne żądania dodatkowe przestrzenie nazw podstawowe / standardowe są odrzucane przez portal. |100|
| Maksymalna liczba przestrzeniach nazw warstwy premium dla subskrypcji platformy Azure |Przestrzeń nazw |Kolejne żądania dodatkowe premium usługi przestrzenie nazw są odrzucane przez portal. |10 |
| Rozmiar kolejki/tematu |Jednostka |Określone podczas tworzenia kolejki/tematu. <br/><br/> Kolejne komunikaty przychodzące są odrzucane i wyjątek jest odbierany przez kod wywołujący. |1, 2, 3, 4 GB lub 5 GB.<br /><br />W wersji Premium, a także Standard z [partycjonowania](/azure/service-bus-messaging/service-bus-partitioning) włączone, rozmiar maksymalny kolejki/tematu jest 80 GB. |
| Liczba jednoczesnych połączeń w przestrzeni nazw |Przestrzeń nazw |Kolejne żądania dla dodatkowych połączeń są odrzucane i wyjątek jest odbierany przez kod wywołujący. Operacje REST nie wchodzą w skład równoczesnych połączeń TCP. |NetMessaging: 1000<br /><br />PROTOKÓŁ AMQP: 5000 |
| Liczba równoczesnych odbierania żądań w jednostce kolejki/tematu/subskrypcji |Jednostka |Odbieranie kolejne żądania są odrzucane i wyjątek zostanie odebrane przez kod wywołujący. Ten przydział dotyczy łączna liczba równoczesnych operacji odbioru przez wszystkie subskrypcje tematu. |5000 |
| Liczba tematów/kolejek na przestrzeń nazw |Przestrzeń nazw |Kolejne żądania w celu utworzenia nowego tematu lub kolejki w przestrzeni nazw są odrzucane. Dzięki temu, jeśli skonfigurowana za pośrednictwem [portalu Azure][Azure portal], zostanie wygenerowany komunikat o błędzie. Jeśli wywoływane z interfejsem API zarządzania, wyjątek jest odbierany przez kod wywołujący. |10 000<br /><br />Całkowita liczba tematów i kolejek w przestrzeni nazw musi być mniejsza lub równa 10 000. |
| Liczba [podzielona na partycje tematy/kolejek](/azure/service-bus-messaging/service-bus-partitioning) na przestrzeń nazw |Przestrzeń nazw |Kolejne żądania w celu utworzenia nowego tematu podzielonym na partycje lub kolejki w przestrzeni nazw są odrzucane. Dzięki temu, jeśli skonfigurowana za pośrednictwem [portalu Azure][Azure portal], zostanie wygenerowany komunikat o błędzie. Jeśli wywołany z zarządzania interfejsu API, **quotaexceededexception —** wyjątku jest odbierany przez kod wywołujący. |Warstwy Basic i Standard - 100<br/><br/>Partycjonowane jednostki są nieobsługiwane w [Premium](../articles/service-bus-messaging/service-bus-premium-messaging.md) warstwy.<br/><br />Każdy partycjonowanej kolejka lub temat, liczy się przydziału 10 000 jednostek na przestrzeni nazw. |
| Maksymalny rozmiar dowolnego komunikatów ścieżki jednostki: kolejka lub temat |Jednostka |- |260 znaków |
| Maksymalny rozmiar dowolnego wiadomości nazwa jednostki: reguła przestrzeni nazw, subskrypcji lub subskrypcji |Jednostka |- |50 znaków |
| Maksymalny rozmiar wiadomości [SessionID](/dotnet/api/microsoft.azure.servicebus.message.sessionid) | Jednostka |- | 128 |
| Rozmiar komunikatu dla jednostki kolejki/tematu/subskrypcji |Jednostka |Wiadomości przychodzących, które przekraczają te przydziały są odrzucane i wyjątek jest odbierany przez kod wywołujący. |Maksymalny rozmiar wiadomości: 256 KB ([warstwy standardowa](../articles/service-bus-messaging/service-bus-premium-messaging.md)) / 1 MB ([warstwy Premium](../articles/service-bus-messaging/service-bus-premium-messaging.md)). <br /><br />Ze względu na obciążenie systemu ten limit jest mniejsza niż te wartości.<br /><br />Rozmiar maksymalny nagłówka: 64 KB<br /><br />Maksymalna liczba właściwości nagłówka w zbiorze właściwości: **bajtów/int. MaxValue**<br /><br />Maksymalna liczba właściwości w zbiorze właściwości: Brak jawnych limitu. Ograniczona przez rozmiar maksymalny nagłówka. |
| Rozmiar komunikatu właściwości dla obiektu kolejki/tematu/subskrypcji |Jednostka |A **SerializationException** wygenerowany wyjątek. |Komunikat maksymalny rozmiar właściwości dla każdej właściwości jest 32 K. całkowity rozmiar wszystkich właściwości nie może przekraczać 64 K. Ten limit dotyczy całego nagłówek [BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage), który zawiera zarówno właściwości użytkownika, jak również właściwości systemu (takich jak [SequenceNumber](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sequencenumber), [etykiety](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.label), [ Identyfikator komunikatu](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.messageid)i tak dalej). |
| Liczba subskrypcji na temat |Jednostka |Kolejne żądania tworzenia dodatkowych subskrypcji dla tematu są odrzucane. W związku z tym Jeśli skonfigurowana za pośrednictwem portalu, wyświetlany jest komunikat o błędzie. Jeśli wywołany z interfejsu API zarządzania wyjątek jest odbierany przez kod wywołujący. |2,000 |
| Liczba filtrów SQL na temat |Jednostka |Kolejne żądania w celu utworzenia dodatkowe filtry tematu są odrzucane i wyjątek jest odbierany przez kod wywołujący. |2,000 |
| Liczba filtrów korelacji na temat |Jednostka |Kolejne żądania w celu utworzenia dodatkowe filtry tematu są odrzucane i wyjątek jest odbierany przez kod wywołujący. |100,000 |
| Rozmiar SQL filtry/akcji |Przestrzeń nazw |Kolejne żądania w celu utworzenia dodatkowe filtry są odrzucane i wyjątek jest odbierany przez kod wywołujący. |Maksymalna długość ciągu warunek filtru: 1024 (1 K).<br /><br />Maksymalna długość ciągu działanie reguły: 1024 (1 K).<br /><br />Maksymalna liczba wyrażeń na Akcja reguły: 32. |
| Liczba [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) zasad na przestrzeń nazw, kolejka lub temat |Jednostki, przestrzeń nazw |Kolejne żądania w celu utworzenia dodatkowych reguł są odrzucane i wyjątek jest odbierany przez kod wywołujący. |Maksymalna liczba reguł: 12. <br /><br /> Zasady, które są skonfigurowane w przestrzeni nazw usługi Service Bus dotyczą wszystkich kolejek i tematów w tej przestrzeni nazw. |
| Liczba komunikatów na transakcję | Transakcji | Dodatkowe komunikaty przychodzące są odrzucane i wyjątków z informacją "nie można wysłać wiadomości więcej niż 100 w ramach jednej transakcji" nieodebrania przez kod wywołujący. | 100 <br /><br /> Dla obu **Send()** i **SendAsync()** operacji. |

[Azure portal]: https://portal.azure.com