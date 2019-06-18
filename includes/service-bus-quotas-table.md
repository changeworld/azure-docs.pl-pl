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
ms.openlocfilehash: f48ad6ca74e6ce10148d66549fea16bc74015b2a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66171217"
---
W poniższej tabeli przedstawiono informacje o limitach przydziału specyficzne dla usługi Azure Service Bus messaging. Aby uzyskać informacje o cenach i inne limity przydziału dla usługi Service Bus, zobacz [cennika usługi Service Bus](https://azure.microsoft.com/pricing/details/service-bus/).

| Nazwa limitu przydziału | Scope | Uwagi | Wartość |
| --- | --- | --- | --- |
| Maksymalna liczba Basic lub Standard przestrzeni nazw na subskrypcję platformy Azure |Przestrzeń nazw |Każde kolejne wywołanie dodatkowe przestrzenie nazw Basic lub Standard są odrzucane przez witryny Azure portal. |100|
| Maksymalna liczba przestrzeni nazw Premium na subskrypcję platformy Azure |Przestrzeń nazw |Każde kolejne wywołanie dodatkowe przestrzenie nazw w warstwie Premium są odrzucane przez portal. |25 |
| Rozmiar kolejki lub tematu |Jednostka |Zdefiniowane podczas tworzenia kolejki lub tematu. <br/><br/> Kolejne komunikaty przychodzące są odrzucane, a wyjątek jest odbierany przez kod wywołujący. |1, 2, 3, 4 GB lub 5 GB.<br /><br />Jednostki SKU Premium i standardowa jednostka SKU z [partycjonowanie](/azure/service-bus-messaging/service-bus-partitioning) włączone, maksymalny rozmiar kolejki lub tematu jest 80 GB. |
| Liczba równoczesnych połączeń w przestrzeni nazw |Przestrzeń nazw |Kolejne żądania dla dodatkowych połączeń są odrzucane, a wyjątek jest odbierany przez kod wywołujący. Operacje REST nie wliczają się równoczesne połączenia TCP. |NetMessaging: 1,000.<br /><br />AMQP: 5,000. |
| Liczba równoczesnych odbierania żądań w jednostce kolejki, tematu lub subskrypcji |Jednostka |Otrzymywać kolejne żądania są odrzucane, a wyjątek jest odbierany przez kod wywołujący. Ten limit przydziału dotyczy łączna liczba równoczesnych operacji odbioru we wszystkich subskrypcjach na temat. |5,000 |
| Liczba tematów lub kolejek na przestrzeń nazw |Przestrzeń nazw |Kolejne żądania w celu utworzenia nowego tematu lub kolejki w przestrzeni nazw są odrzucane. W rezultacie, jeśli skonfigurowana za pośrednictwem [witryny Azure portal][Azure portal], generowany jest komunikat o błędzie. Jeśli jest wywoływana z interfejsem API zarządzania, wyjątek jest odbierany przez kod wywołujący. |1000 połączeń w warstwie podstawowa lub standardowa. Całkowita liczba tematów i kolejek w przestrzeni nazw musi być mniejsza lub równa 1000. <br/><br/>W warstwie Premium, 1000 na jednostkę obsługi komunikatów (MU). Maksymalny limit wynosi 4000. |
| Liczba [podzielony na partycje, tematów lub kolejek](/azure/service-bus-messaging/service-bus-partitioning) na przestrzeń nazw |Przestrzeń nazw |Kolejne żądania do tworzenia nowego podzielonym na partycje tematu lub kolejki w przestrzeni nazw są odrzucane. W rezultacie, jeśli skonfigurowana za pośrednictwem [witryny Azure portal][Azure portal], generowany jest komunikat o błędzie. Jeśli wywoływane z interfejsu API, wyjątek zarządzania **QuotaExceededException** jest odbierany przez kod wywołujący. |Podstawowe i warstwy standardowa: 100.<br/><br/>Partycjonowane jednostki nie są obsługiwane w [Premium](../articles/service-bus-messaging/service-bus-premium-messaging.md) warstwy.<br/><br />Każdy podzieleniu kolejki lub tematu jest liczony przydziału 1000 jednostek na obszar nazw. |
| Maksymalny rozmiar dowolnego komunikatów ścieżka jednostki: kolejki lub tematu |Jednostka |- |260 znaków. |
| Maksymalny rozmiar dowolnego komunikatów nazwa jednostki: reguły w przestrzeni nazw, subskrypcji lub subskrypcji |Jednostka |- |50 znaków. |
| Maksymalny rozmiar [identyfikator wiadomości](/dotnet/api/microsoft.azure.servicebus.message.messageid) | Jednostka |- | 128 |
| Maksymalny rozmiar komunikatu [identyfikator sesji](/dotnet/api/microsoft.azure.servicebus.message.sessionid) | Jednostka |- | 128 |
| Rozmiar komunikatu dla jednostki kolejki, tematu lub subskrypcji |Jednostka |Wiadomości przychodzących, które wykraczają poza te przydziały są odrzucane, a wyjątek jest odbierany przez kod wywołujący. |Maksymalny rozmiar wiadomości: 256 KB w przypadku [w warstwie standardowa](../articles/service-bus-messaging/service-bus-premium-messaging.md), 1 MB dla [w warstwie Premium](../articles/service-bus-messaging/service-bus-premium-messaging.md). <br /><br />Ze względu na obciążenie systemu ten limit jest mniejsza niż te wartości.<br /><br />Rozmiar maksymalny nagłówka: 64 KB.<br /><br />Maksymalna liczba właściwości nagłówka w zbiorze właściwości: **bajtów/int. MaxValue**.<br /><br />Maksymalny rozmiar właściwości zbioru właściwości: Brak limitu jawnego. Ograniczone przez rozmiar maksymalny nagłówka. |
| Rozmiar właściwości wiadomości dla jednostki kolejki, tematu lub subskrypcji |Jednostka | Wyjątek **SerializationException** jest generowany. |Maksymalny rozmiar właściwości dla każdej właściwości wynosi 32 000. Całkowity rozmiar wszystkich właściwości nie może przekraczać 64 000. Ten limit dotyczy całego nagłówek [BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage), która ma zarówno użytkownika i właściwości systemu, takie jak [SequenceNumber](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sequencenumber), [etykiety](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.label)i [ Identyfikator komunikatu](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.messageid). |
| Liczba subskrypcji na temat |Jednostka |Kolejne żądania, aby utworzyć dodatkowe subskrypcje tematu są odrzucane. W rezultacie Jeśli skonfigurowana za pośrednictwem portalu, jest wyświetlany komunikat o błędzie. Jeśli jest wywoływana z interfejsem API zarządzania, wyjątek jest odbierany przez kod wywołujący. |Standardowa i warstwa Premium: Każda subskrypcja zmniejsza limit przydziału 1000 jednostek, oznacza to, kolejek, tematów i subskrypcji na przestrzeń nazw. |
| Liczba filtrów SQL na temat |Jednostka |Kolejne żądania do tworzenia dodatkowych filtrów tematu są odrzucane, a wyjątek jest odbierany przez kod wywołujący. |2000 |
| Liczba filtry korelacji przypadających na temat |Jednostka |Kolejne żądania do tworzenia dodatkowych filtrów tematu są odrzucane, a wyjątek jest odbierany przez kod wywołujący. |100,000 |
| Rozmiar filtry SQL lub akcje |Przestrzeń nazw |Kolejne żądania do tworzenia dodatkowych filtrów są odrzucane, a wyjątek jest odbierany przez kod wywołujący. |Maksymalna długość ciągu warunek filtru: 1024 (1 KB).<br /><br />Maksymalna długość ciągu działanie reguły: 1024 (1 KB).<br /><br />Maksymalna liczba wyrażeń na działanie reguły: 32. |
| Liczba [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) reguł na przestrzeń nazw, kolejki lub tematu |Jednostki, przestrzeń nazw |Każde kolejne wywołanie tworzenia dodatkowych reguł są odrzucane, a wyjątek jest odbierany przez kod wywołujący. |Maksymalna liczba reguł: 12. <br /><br /> Reguły, które są skonfigurowane w przestrzeni nazw usługi Service Bus dotyczą wszystkie kolejki i tematy w tej przestrzeni nazw. |
| Liczba komunikatów na transakcję | Transakcji | Dodatkowe wiadomości przychodzące są odrzucane, a wyjątek z informacją "nie można wysyłać więcej niż 100 komunikatów w ramach jednej transakcji" jest odbierany przez kod wywołujący. | 100 <br /><br /> Dla obu **Send()** i **SendAsync()** operacji. |

[Azure portal]: https://portal.azure.com
