---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: service-bus-messaging
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 12/13/2018
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: d6c5d881c5ca6eee14835ab0ec10bff2749299dc
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/10/2019
ms.locfileid: "68502315"
---
W poniższej tabeli wymieniono informacje o limicie przydziału dotyczące Azure Service Bus komunikatów. Aby uzyskać informacje na temat cen i innych przydziałów Service Bus, zobacz [Cennik usługi Service Bus](https://azure.microsoft.com/pricing/details/service-bus/).

| Nazwa limitu przydziału | Scope | Uwagi | Value |
| --- | --- | --- | --- |
| Maksymalna liczba przestrzeni nazw podstawowa lub standardowa na subskrypcję platformy Azure |Przestrzeń nazw |Kolejne żądania dotyczące dodatkowych podstawowych lub standardowych przestrzeni nazw są odrzucane przez Azure Portal. |100|
| Maksymalna liczba przestrzeni nazw w warstwie Premium na subskrypcję platformy Azure |Przestrzeń nazw |Kolejne żądania dla dodatkowych przestrzeni nazw w warstwie Premium są odrzucane przez portal. |50 |
| Rozmiar kolejki lub tematu |Jednostka |Zdefiniowane podczas tworzenia kolejki lub tematu. <br/><br/> Kolejne komunikaty przychodzące są odrzucane i występuje wyjątek przez wywoływany kod. |1, 2, 3, 4 GB lub 5 GB.<br /><br />W jednostkach SKU Premium i standardowej jednostce SKU z [](/azure/service-bus-messaging/service-bus-partitioning) włączoną funkcją partycjonowania maksymalny rozmiar kolejki lub tematu wynosi 80 GB. |
| Liczba równoczesnych połączeń w przestrzeni nazw |Przestrzeń nazw |Kolejne żądania dla dodatkowych połączeń są odrzucane i występuje wyjątek przez wywoływany kod. Operacje REST nie są wliczane do współbieżnych połączeń TCP. |NetMessaging 1 000.<br /><br />AMQP: 5 000. |
| Liczba współbieżnych żądań odebrania dla kolejki, tematu lub jednostki subskrypcji |Jednostka |Kolejne żądania odbioru są odrzucane, a wywoływany kod wywołuje wyjątek. Ten limit przydziału ma zastosowanie do łącznej liczby równoczesnych operacji odbierania we wszystkich subskrypcjach w temacie. |5,000 |
| Liczba tematów lub kolejek na przestrzeń nazw |Przestrzeń nazw |Kolejne żądania utworzenia nowego tematu lub kolejki w przestrzeni nazw są odrzucane. W związku z tym, jeśli zostanie on skonfigurowany za pomocą [Azure Portal][Azure portal], zostanie wygenerowany komunikat o błędzie. Jeśli wywoływana z interfejsu API zarządzania, kod wywołujący otrzymuje wyjątek. |10 000 dla warstwy Podstawowa lub standardowa. Łączna liczba tematów i kolejek w przestrzeni nazw musi być mniejsza lub równa 10 000. <br/><br/>W przypadku warstwy Premium 1 000 na jednostkę obsługi wiadomości (MU). Maksymalny limit to 4 000. |
| Liczba [podzielonych tematów lub kolejek](/azure/service-bus-messaging/service-bus-partitioning) na przestrzeń nazw |Przestrzeń nazw |Kolejne żądania utworzenia nowego podzielonego tematu lub kolejki w przestrzeni nazw są odrzucane. W związku z tym, jeśli zostanie on skonfigurowany za pomocą [Azure Portal][Azure portal], zostanie wygenerowany komunikat o błędzie. W przypadku wywołania z interfejsu API zarządzania wyjątek **QuotaExceededException** jest odbierany przez wywoływany kod. |Warstwy Podstawowa i standardowa: 100.<br/><br/>Partycjonowane jednostki nie są obsługiwane w warstwie [Premium](../articles/service-bus-messaging/service-bus-premium-messaging.md) .<br/><br />Każda podzielona Kolejka lub temat ma do przydziału liczby jednostek 1 000 na przestrzeń nazw. |
| Maksymalny rozmiar dowolnej ścieżki jednostki obsługi komunikatów: kolejki lub tematu |Jednostka |- |260 znaków. |
| Maksymalny rozmiar każdej nazwy jednostki obsługi komunikatów: przestrzeń nazw, subskrypcja lub reguła subskrypcji |Jednostka |- |50 znaków. |
| Maksymalny rozmiar [identyfikatora komunikatu](/dotnet/api/microsoft.azure.servicebus.message.messageid) | Jednostka |- | 128 |
| Maksymalny rozmiar [identyfikatora sesji](/dotnet/api/microsoft.azure.servicebus.message.sessionid) komunikatu | Jednostka |- | 128 |
| Rozmiar komunikatu dla jednostki kolejki, tematu lub subskrypcji |Jednostka |Komunikaty przychodzące, które przekraczają te przydziały, są odrzucane i występuje wyjątek przez wywoływany kod. |Maksymalny rozmiar komunikatu: 256 KB dla [warstwy Standard](../articles/service-bus-messaging/service-bus-premium-messaging.md), 1 MB dla [warstwy Premium](../articles/service-bus-messaging/service-bus-premium-messaging.md). <br /><br />Ze względu na obciążenie systemu ten limit jest mniejszy niż te wartości.<br /><br />Maksymalny rozmiar nagłówka: 64 KB.<br /><br />Maksymalna liczba właściwości nagłówka w zbiorze właściwości: **Byte/int. MaxValue**.<br /><br />Maksymalny rozmiar właściwości w zbiorze właściwości: Brak jawnego limitu. Ograniczone przez maksymalny rozmiar nagłówka. |
| Rozmiar właściwości komunikatu dla jednostki kolejki, tematu lub subskrypcji |Jednostka | Wyjątek **SerializationException** jest generowany. |Maksymalny rozmiar właściwości komunikatu dla każdej właściwości to 32 000. Łączny rozmiar wszystkich właściwości nie może przekroczyć 64 000. Ten limit dotyczy całego nagłówka [BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage), który ma zarówno właściwości użytkownika, jak i właściwości systemu, takie jak [SequenceNumber](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sequencenumber), [Label](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.label)i [MessageID](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.messageid). |
| Liczba subskrypcji na temat |Jednostka |Kolejne żądania utworzenia dodatkowych subskrypcji dla tematu są odrzucane. W związku z tym, jeśli zostanie skonfigurowany za pomocą portalu, zostanie wyświetlony komunikat o błędzie. Jeśli wywoływana z interfejsu API zarządzania, kod wywołujący otrzymuje wyjątek. |2 000 na temat dla warstwy Podstawowa lub standardowa. |
| Liczba filtrów SQL na temat |Jednostka |Kolejne żądania utworzenia dodatkowych filtrów w temacie są odrzucane i występuje wyjątek przez wywoływany kod. |2000 |
| Liczba filtrów korelacji na temat |Jednostka |Kolejne żądania utworzenia dodatkowych filtrów w temacie są odrzucane i występuje wyjątek przez wywoływany kod. |100,000 |
| Rozmiar filtrów SQL lub akcji |Przestrzeń nazw |Kolejne żądania utworzenia dodatkowych filtrów są odrzucane i występuje wyjątek w wywołaniu kodu. |Maksymalna długość ciągu warunku filtru: 1 024 (1 K).<br /><br />Maksymalna długość ciągu akcji reguły: 1 024 (1 K).<br /><br />Maksymalna liczba wyrażeń na akcję reguły: 32. |
| Liczba reguł [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) na przestrzeń nazw, kolejkę lub temat |Jednostka, przestrzeń nazw |Kolejne żądania utworzenia dodatkowych reguł są odrzucane i występuje wyjątek przez wywoływany kod. |Maksymalna liczba reguł: 12. <br /><br /> Reguły, które są skonfigurowane w przestrzeni nazw Service Bus, mają zastosowanie do wszystkich kolejek i tematów w tej przestrzeni nazw. |
| Liczba komunikatów na transakcję | Transakcji | Dodatkowe komunikaty przychodzące są odrzucane, a wyjątek "nie może wysyłać więcej niż 100 komunikatów w jednej transakcji" jest odbierany przez wywoływany kod. | 100 <br /><br /> Dla operacji **Send ()** i **SendAsync ()** . |

[Azure portal]: https://portal.azure.com
