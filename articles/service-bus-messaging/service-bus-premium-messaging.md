---
title: Omówienie warstw cenowych Premium i Standardowa komunikatów usługi Azure Service Bus | Microsoft Docs
description: Warstwy Premium i Standardowa komunikatów usługi Service Bus
services: service-bus-messaging
documentationcenter: .net
author: axisc
manager: timlt
editor: spelluru
ms.assetid: e211774d-821c-4d79-8563-57472d746c58
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/05/2019
ms.author: aschhab
ms.openlocfilehash: 600577ebf05a8bc89dbec35d3b3ee5162aa246e1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64872730"
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
| Możliwość skalowania obciążenia |ND |
| Rozmiar komunikatu do 1 MB |Rozmiar komunikatu do 256 KB |

**Warstwa Premium komunikatów usługi Service Bus** zapewnia izolację zasobów na poziomie procesora CPU i pamięci, dlatego obciążenia poszczególnych klientów są od siebie odizolowane. Ten kontener zasobów jest nazywany *jednostką obsługi komunikatów*. Każda przestrzeń nazw w warstwie Premium ma przydzieloną co najmniej jedną jednostkę obsługi komunikatów. Możesz kupić 1, 2, 4 lub 8 jednostki dla każdej przestrzeni nazw usługi Service Bus w warstwie Premium obsługi komunikatów. Pojedyncze obciążenie lub jednostka może obejmować wiele jednostek obsługi komunikatów i liczby jednostek obsługi komunikatów można dowolnie zmieniać. Pozwala to uzyskać przewidywalną i powtarzalną wydajność dla rozwiązania opartego na usłudze Service Bus.

Poprawa dotyczy nie tylko przewidywalności i dostępności, ale również szybkości działania. Komunikaty usługi Service Bus w warstwie Premium są oparte na aparacie magazynu wprowadzonym w usłudze [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/). Dzięki komunikatom w warstwie Premium maksymalna szybkość działania jest znacznie wyższa niż w przypadku warstwy Standardowa.

## <a name="premium-messaging-technical-differences"></a>Różnice techniczne dotyczące komunikatów w warstwie Premium

W poniższych sekcjach omówiono niektóre różnice między komunikatami w warstwie Premium i Standardowa.

### <a name="partitioned-queues-and-topics"></a>Partycjonowane kolejki i tematy

Partycjonowane kolejki i tematy nie są obsługiwane w przypadku komunikatów w warstwie Premium. Aby uzyskać więcej informacji na temat partycjonowania, zobacz [Partitioned queues and topics](service-bus-partitioning.md) (Partycjonowane kolejki i tematy).

### <a name="express-entities"></a>Jednostki ekspresowe

Ze względu na fakt, że obsługa komunikatów Premium działa w całkowicie odizolowanym środowisku uruchomieniowym, jednostki ekspresowe nie są obsługiwane w przestrzeniach nazw w warstwie Premium. Aby uzyskać więcej informacji o funkcji jednostek ekspresowych, zobacz opis właściwości [QueueDescription.EnableExpress](/dotnet/api/microsoft.servicebus.messaging.queuedescription.enableexpress#Microsoft_ServiceBus_Messaging_QueueDescription_EnableExpress).

Jeśli masz kod uruchomiony w ramach obsługi komunikatów w warstwie Standardowa i chcesz przenieść go do warstwy Premium, upewnij się, że właściwość [EnableExpress](/dotnet/api/microsoft.servicebus.messaging.queuedescription.enableexpress#Microsoft_ServiceBus_Messaging_QueueDescription_EnableExpress) jest ustawiona na wartość **false** (wartość domyślna).

## <a name="premium-messaging-resource-usage"></a>Użycie zasobów Obsługa komunikatów Premium
Ogólnie rzecz biorąc żadnych operacji na jednostce może spowodować wykorzystanie Procesora i pamięci. Oto niektóre z tych operacji: 

- Zarządzanie operacjami CRUD (tworzenia, pobierania, aktualizacji i usuwania) operacje na kolejki, tematy i subskrypcje.
- Operacje w czasie wykonywania (wysyłania i odbierania komunikatów)
- Operacje monitorowania i alertów

Dodatkowe użycie procesora CPU i pamięci nie jest rozliczana Ponadto mimo że. W przypadku warstwy Premium Messaging istnieje pojedynczy cena za jednostkę wiadomości.

Użycie procesora CPU i pamięci są śledzone i wyświetlane użytkownikowi w następujących sytuacjach: 

- Zapewnia przejrzyste elementy wewnętrzne systemu
- Informacje o wydajności zakupu zasobów.
- Planowanie pojemności pomaga określić skalowania w górę/w dół.

## <a name="get-started-with-premium-messaging"></a>Wprowadzenie do obsługi komunikatów Premium

Rozpoczęcie pracy z obsługą komunikatów Premium jest proste, a proces jest podobny, jak w przypadku standardowej obsługi komunikatów. Rozpocznij od [utworzenia przestrzeni nazw](service-bus-create-namespace-portal.md) w witrynie [Azure Portal](https://portal.azure.com). W obszarze **Warstwa cenowa** wybierz pozycję **Premium**. Kliknij przycisk **Wyświetl pełne szczegóły ceny**, aby uzyskać więcej informacji o poszczególnych warstwach.

![create-premium-namespace][create-premium-namespace]

Możesz również tworzyć [przestrzenie nazw Premium za pomocą szablonów usługi Azure Resource Manager](https://azure.microsoft.com/resources/templates/101-servicebus-pn-ar/).

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat obsługi komunikatów usługi Service Bus, zobacz poniższe linki:

* [Introducing Azure Service Bus Premium messaging](https://azure.microsoft.com/blog/introducing-azure-service-bus-premium-messaging/) (Wprowadzenie do komunikatów usługi Service Bus w warstwie Premium) — wpis w blogu
* [Introducing Azure Service Bus Premium messaging](https://channel9.msdn.com/Blogs/Subscribe/Introducing-Azure-Service-Bus-Premium-Messaging) (Wprowadzenie do komunikatów usługi Service Bus w warstwie Premium) — Channel9
* [Omówienie obsługi komunikatów w usłudze Service Bus](service-bus-messaging-overview.md)
* [Wprowadzenie do kolejek usługi Service Bus](service-bus-dotnet-get-started-with-queues.md)

<!--Image references-->

[create-premium-namespace]: ./media/service-bus-premium-messaging/select-premium-tier.png
