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
ms.openlocfilehash: b19dc7a85fafa1a4d875c84db9bbefabb3cd5a7d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77651506"
---
W poniższej tabeli wymieniono informacje o przydziałach specyficzne dla obsługi wiadomości usługi Azure Service Bus. Aby uzyskać informacje na temat cen i innych przydziałów dla usługi Service Bus, zobacz [Ceny usługi Service Bus](https://azure.microsoft.com/pricing/details/service-bus/).

| Nazwa przydziału | Zakres | Uwagi | Wartość |
| --- | --- | --- | --- |
| Maksymalna liczba podstawowych lub standardowych obszarów nazw na subskrypcję platformy Azure |Przestrzeń nazw |Kolejne żądania dodatkowych podstawowych lub standardowych obszarów nazw są odrzucane przez witrynę Azure portal. |100|
| Maksymalna liczba obszarów nazw premium na subskrypcję platformy Azure |Przestrzeń nazw |Kolejne żądania dodatkowych obszarów nazw premium są odrzucane przez portal. |100 |
| Rozmiar kolejki lub tematu |Jednostka |Zdefiniowane po utworzeniu kolejki lub tematu. <br/><br/> Kolejne przychodzące wiadomości są odrzucane, a wyjątek jest odbierany przez kod wywołujący. |1, 2, 3, 4 GB lub 5 GB.<br /><br />W jednostce SKU premium i standardowej jednostce SKU z [włączoną partycjonowania](/azure/service-bus-messaging/service-bus-partitioning) maksymalny rozmiar kolejki lub tematu wynosi 80 GB. |
| Liczba równoczesnych połączeń w obszarze nazw |Przestrzeń nazw |Kolejne żądania dotyczące dodatkowych połączeń są odrzucane, a wyjątek jest odbierany przez kod wywołujący. Operacje REST nie są wliczane do równoczesnych połączeń TCP. |NetMessaging: 1000.<br /><br />AMQP: 5000. |
| Liczba równoczesnych żądań odbierania w kolejce, temacie lub encji subskrypcji |Jednostka |Kolejne żądania odbierania są odrzucane, a wyjątek jest odbierany przez kod wywołujący. Ten przydział ma zastosowanie do łącznej liczby równoczesnych operacji odbierania we wszystkich subskrypcjach na temat. |5000 |
| Liczba tematów lub kolejek na obszar nazw |Przestrzeń nazw |Kolejne żądania utworzenia nowego tematu lub kolejki w obszarze nazw są odrzucane. W rezultacie, jeśli skonfigurowano za pośrednictwem [witryny Azure portal,][Azure portal]generowany jest komunikat o błędzie. Jeśli wywoływane z interfejsu API zarządzania, wyjątek jest odbierany przez kod wywołujący. |10 000 dla warstwy Podstawowa lub Standardowa. Całkowita liczba tematów i kolejek w obszarze nazw musi być mniejsza lub równa 10 000. <br/><br/>Dla warstwy Premium 1000 na jednostkę obsługi wiadomości (MU). Maksymalny limit wynosi 4000. |
| Liczba [podzielonych na partycje tematów lub kolejek](/azure/service-bus-messaging/service-bus-partitioning) na obszar nazw |Przestrzeń nazw |Kolejne żądania utworzenia nowego tematu partycjonowanego lub kolejki w obszarze nazw są odrzucane. W rezultacie, jeśli skonfigurowano za pośrednictwem [witryny Azure portal,][Azure portal]generowany jest komunikat o błędzie. Jeśli wywoływane z interfejsu API zarządzania, wyjątek **QuotaExceededException** jest odbierany przez kod wywołujący. |Poziomy podstawowe i standardowe: 100.<br/><br/>Jednostki podzielone na partycje nie są obsługiwane w warstwie [Premium.](../articles/service-bus-messaging/service-bus-premium-messaging.md)<br/><br />Każda kolejka lub temat podzielony na partycje jest wliczona do przydziału 1000 jednostek na obszar nazw. |
| Maksymalny rozmiar dowolnej ścieżki jednostki obsługi wiadomości: kolejka lub temat |Jednostka |- |260 znaków. |
| Maksymalny rozmiar dowolnej nazwy jednostki obsługi wiadomości: obszar nazw, subskrypcja lub reguła subskrypcji |Jednostka |- |50 znaków. |
| Maksymalny rozmiar identyfikatora [wiadomości](/dotnet/api/microsoft.azure.servicebus.message.messageid) | Jednostka |- | 128 |
| Maksymalny rozmiar identyfikatora [sesji](/dotnet/api/microsoft.azure.servicebus.message.sessionid) wiadomości | Jednostka |- | 128 |
| Rozmiar wiadomości dla kolejki, tematu lub encji subskrypcji |Jednostka |Przychodzące wiadomości, które przekraczają te przydziały są odrzucane, a wyjątek jest odbierany przez kod wywołujący. |Maksymalny rozmiar wiadomości: 256 KB dla [warstwy standardowej,](../articles/service-bus-messaging/service-bus-premium-messaging.md)1 MB dla [warstwy Premium.](../articles/service-bus-messaging/service-bus-premium-messaging.md) <br /><br />Ze względu na obciążenie systemu ten limit jest mniejszy niż te wartości.<br /><br />Maksymalny rozmiar nagłówka: 64 KB.<br /><br />Maksymalna liczba właściwości nagłówka w torbie właściwości: **bajt/int. MaxValue**.<br /><br />Maksymalny rozmiar właściwości w torbie właściwości: Brak wyraźnego limitu. Ograniczona przez maksymalny rozmiar nagłówka. |
| Rozmiar właściwości wiadomości dla kolejki, tematu lub encji subskrypcji |Jednostka | Wyjątek **SerializationException** jest generowany. |Maksymalny rozmiar właściwości message dla każdej właściwości wynosi 32 000. Skumulowany rozmiar wszystkich właściwości nie może przekraczać 64 000. Ten limit ma zastosowanie do całego nagłówka [BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage), który ma zarówno właściwości użytkownika, jak i właściwości systemu, takie jak [SequenceNumber](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sequencenumber), [Label](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.label)i [MessageId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.messageid). |
| Liczba subskrypcji na temat |Jednostka |Kolejne żądania tworzenia dodatkowych subskrypcji dla tematu są odrzucane. W rezultacie, jeśli jest skonfigurowany za pośrednictwem portalu, wyświetlany jest komunikat o błędzie. Jeśli wywoływane z interfejsu API zarządzania, wyjątek jest odbierany przez kod wywołujący. |2000 na temat dla warstwy Standardowa. |
| Liczba filtrów SQL na temat |Jednostka |Kolejne żądania utworzenia dodatkowych filtrów na ten temat są odrzucane, a wyjątek jest odbierany przez kod wywołujący. |2000 |
| Liczba filtrów korelacji na temat |Jednostka |Kolejne żądania utworzenia dodatkowych filtrów na ten temat są odrzucane, a wyjątek jest odbierany przez kod wywołujący. |100 000 |
| Rozmiar filtrów lub akcji SQL |Przestrzeń nazw |Kolejne żądania utworzenia dodatkowych filtrów są odrzucane, a wyjątek jest odbierany przez kod wywołujący. |Maksymalna długość ciągu warunku filtru: 1024 (1 K).<br /><br />Maksymalna długość ciągu akcji reguły: 1024 (1 K).<br /><br />Maksymalna liczba wyrażeń na akcję reguły: 32. |
| Liczba reguł [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) na obszar nazw, kolejkę lub temat |Encja, obszar nazw |Kolejne żądania utworzenia dodatkowych reguł są odrzucane, a wyjątek jest odbierany przez kod wywołujący. |Maksymalna liczba reguł dla typu encji: 12. <br /><br /> Reguły skonfigurowane w obszarze nazw usługi Service Bus dotyczą wszystkich typów: kolejek, tematów. |
| Liczba wiadomości na transakcję | Transakcja | Dodatkowe wiadomości przychodzące są odrzucane, a kod wywołujący otrzymuje wyjątek z napisem "Nie można wysłać więcej niż 100 wiadomości w jednej transakcji". | 100 <br /><br /> Dla operacji **Send()** i **SendAsync().** |
| Liczba reguł filtrowania sieci wirtualnej i adresów IP | Przestrzeń nazw | &nbsp; | 128 | 

[Azure portal]: https://portal.azure.com
