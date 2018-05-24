---
title: Omówienie warstw cenowych Premium i Standardowa komunikatów usługi Azure Service Bus | Microsoft Docs
description: Warstwy Premium i Standardowa komunikatów usługi Service Bus
services: service-bus-messaging
documentationcenter: .net
author: djrosanova
manager: timlt
editor: ''
ms.assetid: e211774d-821c-4d79-8563-57472d746c58
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 04/30/2018
ms.author: sethm
ms.openlocfilehash: 1b2c9b3a0b7565e7de40d4dc0e623a62313b79db
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/03/2018
---
# <a name="service-bus-premium-and-standard-messaging-tiers"></a>Warstwy Premium i Standardowa komunikatów usługi Service Bus

Komunikaty usługi Service Bus, w tym jednostki, takie jak kolejki i tematy, stanowią połączenie możliwości obsługi komunikatów dla przedsiębiorstw oraz zaawansowanej semantyki publikowania/subskrybowania w skali chmury. Obsługa komunikatów usługi Service Bus pełni rolę szkieletu komunikacyjnego dla wielu zaawansowanych rozwiązań w chmurze.

Warstwa *Premium* komunikatów usługi Service Bus stanowi odpowiedź na częste żądania klientów dotyczące skali, wydajności i dostępności dla aplikacji o krytycznym znaczeniu. Warstwa Premium jest zalecana do użytku w scenariuszach produkcyjnych. Mimo że zestawy funkcji są niemal identyczne, te dwie warstwy komunikatów usługi Service Bus są przeznaczone do różnych zastosowań.

W poniższej tabeli wyróżniono pewne ogólne różnice.

| Premium | Standardowa (Standard) |
| --- | --- |
| Wysoka przepływność |Zmienna przepływność |
| Przewidywalna wydajność |Zmienne opóźnienie |
| Stałe ceny |Zmienne ceny i płatność zgodnie z rzeczywistym użyciem |
| Możliwość skalowania obciążenia |Nie dotyczy |
| Rozmiar komunikatu do 1 MB |Rozmiar komunikatu do 256 KB |

**Warstwa Premium komunikatów usługi Service Bus** zapewnia izolację zasobów na poziomie procesora CPU i pamięci, dlatego obciążenia poszczególnych klientów są od siebie odizolowane. Ten kontener zasobów jest nazywany *jednostką obsługi komunikatów*. Każda przestrzeń nazw w warstwie Premium ma przydzieloną co najmniej jedną jednostkę obsługi komunikatów. Możesz kupić 1, 2 lub 4 jednostki obsługi komunikatów dla każdej przestrzeni nazw usługi Service Bus w warstwie Premium. Pojedyncze obciążenie lub jednostka może obejmować wiele jednostek obsługi komunikatów, a ich liczbę można dowolnie zmieniać, chociaż opłaty są naliczane w cyklu 24-godzinnym lub codziennie. Pozwala to uzyskać przewidywalną i powtarzalną wydajność dla rozwiązania opartego na usłudze Service Bus.

Poprawa dotyczy nie tylko przewidywalności i dostępności, ale również szybkości działania. Komunikaty usługi Service Bus w warstwie Premium są oparte na aparacie magazynu wprowadzonym w usłudze [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/). Dzięki komunikatom w warstwie Premium maksymalna szybkość działania jest znacznie wyższa niż w przypadku warstwy Standardowa.

## <a name="premium-messaging-technical-differences"></a>Różnice techniczne dotyczące komunikatów w warstwie Premium

W poniższych sekcjach omówiono niektóre różnice między komunikatami w warstwie Premium i Standardowa.

### <a name="partitioned-queues-and-topics"></a>Partycjonowane kolejki i tematy

Partycjonowane kolejki i tematy nie są obsługiwane w przypadku komunikatów w warstwie Premium. Aby uzyskać więcej informacji na temat partycjonowania, zobacz [Partitioned queues and topics](service-bus-partitioning.md) (Partycjonowane kolejki i tematy).

### <a name="express-entities"></a>Jednostki ekspresowe

Ze względu na fakt, że obsługa komunikatów Premium działa w całkowicie odizolowanym środowisku uruchomieniowym, jednostki ekspresowe nie są obsługiwane w przestrzeniach nazw w warstwie Premium. Aby uzyskać więcej informacji o funkcji jednostek ekspresowych, zobacz opis właściwości [QueueDescription.EnableExpress](/dotnet/api/microsoft.servicebus.messaging.queuedescription.enableexpress#Microsoft_ServiceBus_Messaging_QueueDescription_EnableExpress).

Jeśli masz kod uruchomiony w ramach obsługi komunikatów w warstwie Standardowa i chcesz przenieść go do warstwy Premium, upewnij się, że właściwość [EnableExpress](/dotnet/api/microsoft.servicebus.messaging.queuedescription.enableexpress#Microsoft_ServiceBus_Messaging_QueueDescription_EnableExpress) jest ustawiona na wartość **false** (wartość domyślna).

## <a name="get-started-with-premium-messaging"></a>Wprowadzenie do obsługi komunikatów Premium

Rozpoczęcie pracy z obsługą komunikatów Premium jest proste, a proces jest podobny, jak w przypadku standardowej obsługi komunikatów. Rozpocznij od [utworzenia przestrzeni nazw](service-bus-create-namespace-portal.md) w witrynie [Azure Portal](https://portal.azure.com). W obszarze **Wybieranie warstwy cenowej** wybierz pozycję **Premium**.

![create-premium-namespace][create-premium-namespace]

Możesz również tworzyć [przestrzenie nazw Premium za pomocą szablonów usługi Azure Resource Manager](https://azure.microsoft.com/resources/templates/101-servicebus-pn-ar/).

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat obsługi komunikatów usługi Service Bus, zobacz następujące tematy.

* [Introducing Azure Service Bus Premium messaging](http://azure.microsoft.com/blog/introducing-azure-service-bus-premium-messaging/) (Wprowadzenie do komunikatów usługi Service Bus w warstwie Premium) — wpis w blogu
* [Introducing Azure Service Bus Premium messaging](https://channel9.msdn.com/Blogs/Subscribe/Introducing-Azure-Service-Bus-Premium-Messaging) (Wprowadzenie do komunikatów usługi Service Bus w warstwie Premium) — Channel9
* [Omówienie obsługi komunikatów w usłudze Service Bus](service-bus-messaging-overview.md)
* [Wprowadzenie do kolejek usługi Service Bus](service-bus-dotnet-get-started-with-queues.md)

<!--Image references-->

[create-premium-namespace]: ./media/service-bus-premium-messaging/select-premium-tier.png
