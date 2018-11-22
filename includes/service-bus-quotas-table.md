---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: service-bus-messaging
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 08/29/2018
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 1116d6037a149b379bd96d6b208ca306a38c7a03
ms.sourcegitcommit: 022cf0f3f6a227e09ea1120b09a7f4638c78b3e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/21/2018
ms.locfileid: "52288696"
---
W poniższej tabeli przedstawiono informacje o limitach przydziału specyficzne dla komunikatów usługi Service Bus. Aby uzyskać informacje o cenach i inne limity przydziału dla usługi Service Bus, zobacz [cennik usługi Service Bus](https://azure.microsoft.com/pricing/details/service-bus/) Przegląd.

| Nazwa limitu przydziału | Zakres | Uwagi | Wartość |
| --- | --- | --- | --- | --- |
| Maksymalna liczba Podstawowa / standardowa przestrzeni nazw na subskrypcję platformy Azure |Przestrzeń nazw |Każde kolejne wywołanie dodatkowe przestrzenie nazw Podstawowa / standardowa są odrzucane przez portal. |100|
| Maksymalna liczba przestrzeni nazw premium na subskrypcję platformy Azure |Przestrzeń nazw |Kolejne żądania dla przestrzeni nazw premium dodatkowe są odrzucane przez portal. |10 |
| Rozmiar kolejki/tematu |Jednostka |Zdefiniowane podczas tworzenia kolejki lub tematu. <br/><br/> Kolejne komunikaty przychodzące są odrzucane, a wyjątek jest odbierany przez kod wywołujący. |1, 2, 3, 4 GB lub 5 GB.<br /><br />W jednostce SKU Premium, jak również Standard z [partycjonowanie](/azure/service-bus-messaging/service-bus-partitioning) włączone, rozmiar maksymalny kolejki lub tematu jest 80 GB. |
| Liczba równoczesnych połączeń w przestrzeni nazw |Przestrzeń nazw |Kolejne żądania dla dodatkowych połączeń są odrzucane, a wyjątek jest odbierany przez kod wywołujący. Operacje REST są wliczane równoczesne połączenia TCP. |NetMessaging: 1000<br /><br />PROTOKÓŁ AMQP: 5000 |
| Liczba równoczesnych odbierania żądań w jednostce kolejki/tematu/subskrypcji |Jednostka |Otrzymywać kolejne żądania są odrzucane, a wyjątek jest odbierany przez kod wywołujący. Ten limit przydziału dotyczy łączna liczba równoczesnych operacji odbioru we wszystkich subskrypcjach na temat. |5000 |
| Liczba tematów/kolejek na przestrzeń nazw |Przestrzeń nazw |Kolejne żądania w celu utworzenia nowego tematu lub kolejki w przestrzeni nazw są odrzucane. W rezultacie, jeśli skonfigurowana za pośrednictwem [witryny Azure portal][Azure portal], generowany jest komunikat o błędzie. Jeśli jest wywoływana z interfejsem API zarządzania, wyjątek jest odbierany przez kod wywołujący. |1000 (warstwa podstawowa/standardowa). Całkowita liczba tematów i kolejek w przestrzeni nazw musi być mniejsza lub równa 1000. <br/><br/>Dla warstwy premium, 1000 na unit(MU) obsługi komunikatów. Maksymalny limit wynosi 4000. |
| Liczba [podzielona na partycje kolejek/tematów](/azure/service-bus-messaging/service-bus-partitioning) na przestrzeń nazw |Przestrzeń nazw |Kolejne żądania do tworzenia nowego podzielonym na partycje tematu lub kolejki w przestrzeni nazw są odrzucane. W rezultacie, jeśli skonfigurowana za pośrednictwem [witryny Azure portal][Azure portal], generowany jest komunikat o błędzie. Jeśli wywoływane z interfejsu API, zarządzania **QuotaExceededException** wyjątek jest odbierany przez kod wywołujący. |Warstw podstawowa i standardowa — 100<br/><br/>Partycjonowane jednostki nie są obsługiwane w [Premium](../articles/service-bus-messaging/service-bus-premium-messaging.md) warstwy.<br/><br />Każdy podzieleniu kolejki lub tematu, liczy się do limitu przydziału 1000 jednostek na obszar nazw. |
| Maksymalny rozmiar dowolnego komunikatów ścieżka jednostki: kolejki lub tematu |Jednostka |- |260 znaków |
| Maksymalny rozmiar dowolnego komunikatów nazwa jednostki: reguły w przestrzeni nazw, subskrypcji lub subskrypcji |Jednostka |- |50 znaków |
| Maksymalny rozmiar komunikatu [SessionID](/dotnet/api/microsoft.azure.servicebus.message.sessionid) | Jednostka |- | 128 |
| Rozmiar komunikatu dla jednostki kolejki/tematu/subskrypcji |Jednostka |Wiadomości przychodzących, które wykraczają poza te przydziały są odrzucane, a wyjątek jest odbierany przez kod wywołujący. |Maksymalny rozmiar wiadomości: 256 KB ([w warstwie standardowa](../articles/service-bus-messaging/service-bus-premium-messaging.md)) / 1 MB ([w warstwie Premium](../articles/service-bus-messaging/service-bus-premium-messaging.md)). <br /><br />Ze względu na obciążenie systemu ten limit jest mniejsza niż te wartości.<br /><br />Nagłówek maksymalny rozmiar: 64 KB<br /><br />Maksymalna liczba właściwości nagłówka w zbiorze właściwości: **bajtów/int. MaxValue**<br /><br />Maksymalny rozmiar właściwości zbioru właściwości: Brak jawnych limitu. Ograniczone przez rozmiar maksymalny nagłówka. |
| Rozmiar właściwości wiadomości dla jednostki kolejki/tematu/subskrypcji |Jednostka |A **SerializationException** generowany jest wyjątek. |Maksymalny rozmiar właściwości dla każdej właściwości jest 32 K. całkowity rozmiar wszystkich właściwości nie może być dłuższa niż 64 K. Ten limit dotyczy całego nagłówek [BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage), która zawiera obie właściwości użytkownika, a także właściwości systemu (takich jak [SequenceNumber](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sequencenumber), [etykiety](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.label), [ Identyfikator komunikatu](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.messageid)i tak dalej). |
| Liczba subskrypcji na temat |Jednostka |Kolejne żądania, aby utworzyć dodatkowe subskrypcje tematu są odrzucane. W rezultacie Jeśli skonfigurowana za pośrednictwem portalu, jest wyświetlany komunikat o błędzie. Jeśli jest wywoływana z interfejsu API zarządzania wyjątek jest odbierany przez kod wywołujący. |W warstwie standardowa — Każda subskrypcja zmniejsza limit przydziału 1000 jednostek (kolejki, tematy i subskrypcje) na przestrzeń nazw. <br/> <br/> Warstwa Premium - 2000 |
| Liczba filtrów SQL na temat |Jednostka |Kolejne żądania do tworzenia dodatkowych filtrów tematu są odrzucane, a wyjątek jest odbierany przez kod wywołujący. |2,000 |
| Liczba filtry korelacji przypadających na temat |Jednostka |Kolejne żądania do tworzenia dodatkowych filtrów tematu są odrzucane, a wyjątek jest odbierany przez kod wywołujący. |100 000 |
| Rozmiar SQL filtry/akcji |Przestrzeń nazw |Kolejne żądania do tworzenia dodatkowych filtrów są odrzucane, a wyjątek jest odbierany przez kod wywołujący. |Maksymalna długość ciągu warunek filtru: 1024 (1 KB).<br /><br />Maksymalna długość ciągu działanie reguły: 1024 (1 KB).<br /><br />Maksymalna liczba wyrażeń na działanie reguły: 32. |
| Liczba [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) reguł na przestrzeń nazw, kolejki lub tematu |Jednostki, przestrzeń nazw |Każde kolejne wywołanie tworzenia dodatkowych reguł są odrzucane, a wyjątek jest odbierany przez kod wywołujący. |Maksymalna liczba reguł: 12. <br /><br /> Reguły, które są skonfigurowane w przestrzeni nazw usługi Service Bus dotyczą wszystkie kolejki i tematy w tej przestrzeni nazw. |
| Liczba komunikatów na transakcję | Transakcji | Dodatkowe wiadomości przychodzące są odrzucane i podając wyjątek "nie można wysyłać więcej niż 100 komunikatów w ramach jednej transakcji" jest odbierany przez kod wywołujący. | 100 <br /><br /> Dla obu **Send()** i **SendAsync()** operacji. |

[Azure portal]: https://portal.azure.com