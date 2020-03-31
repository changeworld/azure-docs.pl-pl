---
title: Warstwy premium i warstwy standardowe usługi Azure Service Bus
description: W tym artykule opisano warstwy standardowe i warstwy premium usługi Azure Service Bus. Porównuje te warstwy i zapewnia różnice techniczne.
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
ms.date: 01/27/2020
ms.author: aschhab
ms.openlocfilehash: ef3cc8d4c7354b43389244e72c2dbc5899b8db25
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76774562"
---
# <a name="service-bus-premium-and-standard-messaging-tiers"></a>Warstwy Premium i Standardowa komunikatów usługi Service Bus

Komunikaty usługi Service Bus, w tym jednostki, takie jak kolejki i tematy, stanowią połączenie możliwości obsługi komunikatów dla przedsiębiorstw oraz zaawansowanej semantyki publikowania/subskrybowania w skali chmury. Obsługa komunikatów usługi Service Bus pełni rolę szkieletu komunikacyjnego dla wielu zaawansowanych rozwiązań w chmurze.

Warstwa *Premium* usługi Service Bus Messaging adresuje typowe żądania klientów dotyczące skali, wydajności i dostępności aplikacji o znaczeniu krytycznym. Warstwa Premium jest zalecana do użytku w scenariuszach produkcyjnych. Mimo że zestawy funkcji są niemal identyczne, te dwie warstwy komunikatów usługi Service Bus są przeznaczone do różnych zastosowań.

W poniższej tabeli wyróżniono pewne ogólne różnice.

| Premium | Standardowa |
| --- | --- |
| Wysoka przepływność |Zmienna przepływność |
| Przewidywalna wydajność |Zmienne opóźnienie |
| Stałe ceny |Zmienne ceny i płatność zgodnie z rzeczywistym użyciem |
| Możliwość skalowania obciążenia |Nie dotyczy |
| Rozmiar komunikatu do 1 MB |Rozmiar komunikatu do 256 KB |

**Warstwa Premium komunikatów usługi Service Bus** zapewnia izolację zasobów na poziomie procesora CPU i pamięci, dlatego obciążenia poszczególnych klientów są od siebie odizolowane. Ten kontener zasobów jest nazywany *jednostką obsługi wiadomości*. Każda przestrzeń nazw w warstwie Premium ma przydzieloną co najmniej jedną jednostkę obsługi komunikatów. Możesz kupić 1, 2, 4 lub 8 jednostek obsługi wiadomości dla każdej przestrzeni nazw Usługi Service Bus Premium. Jedno obciążenie lub jednostka może obejmować wiele jednostek obsługi wiadomości i liczba jednostek obsługi wiadomości można zmienić do woli. Pozwala to uzyskać przewidywalną i powtarzalną wydajność dla rozwiązania opartego na usłudze Service Bus.

Poprawa dotyczy nie tylko przewidywalności i dostępności, ale również szybkości działania. Service Bus Premium Messaging builds on the storage engine introduced in [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/). Dzięki komunikatom w warstwie Premium maksymalna szybkość działania jest znacznie wyższa niż w przypadku warstwy Standardowa.

## <a name="premium-messaging-technical-differences"></a>Różnice techniczne dotyczące komunikatów w warstwie Premium

W poniższych sekcjach omówiono niektóre różnice między komunikatami w warstwie Premium i Standardowa.

### <a name="partitioned-queues-and-topics"></a>Partycjonowane kolejki i tematy

Partycjonowane kolejki i tematy nie są obsługiwane w przypadku komunikatów w warstwie Premium. Aby uzyskać więcej informacji na temat partycjonowania, zobacz [Partitioned queues and topics](service-bus-partitioning.md) (Partycjonowane kolejki i tematy).

### <a name="express-entities"></a>Jednostki ekspresowe

Ze względu na fakt, że obsługa komunikatów Premium działa w całkowicie odizolowanym środowisku uruchomieniowym, jednostki ekspresowe nie są obsługiwane w przestrzeniach nazw w warstwie Premium. Aby uzyskać więcej informacji o funkcji jednostek ekspresowych, zobacz opis właściwości [QueueDescription.EnableExpress](/dotnet/api/microsoft.servicebus.messaging.queuedescription.enableexpress#Microsoft_ServiceBus_Messaging_QueueDescription_EnableExpress).

Jeśli masz kod uruchomiony w ramach obsługi komunikatów w warstwie Standardowa i chcesz przenieść go do warstwy Premium, upewnij się, że właściwość [EnableExpress](/dotnet/api/microsoft.servicebus.messaging.queuedescription.enableexpress#Microsoft_ServiceBus_Messaging_QueueDescription_EnableExpress) jest ustawiona na wartość **false** (wartość domyślna).

## <a name="premium-messaging-resource-usage"></a>Użycie zasobów usługi Wiadomości w u. w wersji Premium
Ogólnie rzecz biorąc każda operacja na jednostce może spowodować użycie procesora CPU i pamięci. Oto niektóre z tych operacji: 

- Operacje zarządzania, takie jak operacje CRUD (Tworzenie, Pobieranie, Aktualizowanie i Usuwanie) w kolejkach, tematach i subskrypcjach.
- Operacje środowiska uruchomieniowego (wysyłanie i odbieranie wiadomości)
- Monitorowanie operacji i alertów

Dodatkowe użycie procesora i pamięci nie jest jednak dodatkowo wycenione. W przypadku warstwy Wiadomości Premium istnieje jedna cena za jednostkę wiadomości.

Użycie procesora i pamięci są śledzone i wyświetlane dla użytkownika z następujących powodów: 

- Zapewnienie przejrzystości w wewnętrznych systemach
- Opis zdolności produkcyjnych zakupionych zasobów.
- Planowanie pojemności, które pomaga zdecydować się na skalowanie w górę/w dół.

## <a name="messaging-unit---how-many-are-needed"></a>Jednostka obsługi wiadomości - Ile jest potrzebnych?

Podczas inicjowania obsługi administracyjnej obszaru nazw usługi Azure Service Bus Premium należy określić liczbę przydzielonych jednostek obsługi wiadomości. Te jednostki obsługi wiadomości są dedykowane zasoby, które są przydzielane do obszaru nazw.

Liczba jednostek obsługi wiadomości przydzielonych do obszaru nazw usługi Service Bus Premium może być **dynamicznie dostosowywana** do czynnika zmiany (zwiększenia lub zmniejszenia) obciążeń.

Istnieje wiele czynników, które należy wziąć pod uwagę przy podejmowaniu decyzji o liczbie jednostek obsługi wiadomości dla architektury:

- Zacznij od ***1 lub 2 jednostek obsługi wiadomości przydzielonych*** do przestrzeni nazw.
- Przestudiuj metryki użycia procesora CPU w [metryki użycia zasobów](service-bus-metrics-azure-monitor.md#resource-usage-metrics) dla obszaru nazw.
    - Jeśli użycie procesora CPU jest ***poniżej 20%,*** można ***zmniejszyć*** liczbę jednostek obsługi wiadomości przydzielonych do obszaru nazw.
    - Jeśli użycie procesora CPU jest ***powyżej 70%,*** aplikacja będzie korzystać ze ***skalowania*** liczby jednostek obsługi wiadomości przydzielonych do obszaru nazw.

Proces skalowania zasobów przydzielonych do obszarów nazw usługi Service Bus można zautomatyzować przy użyciu [śmięty uruchomieniu usługi Azure Automation.](../automation/automation-quickstart-create-runbook.md)

> [!NOTE]
> **Skalowanie** zasobów przydzielonych do obszaru nazw może być prewencyjne lub reaktywne.
>
>  * **Preemptive:** Jeśli oczekuje się dodatkowego obciążenia (ze względu na sezonowość lub trendy), można przystąpić do przydzielenia większej liczby jednostek obsługi wiadomości do obszaru nazw przed trafieniem obciążeń.
>
>  * **Reaktywne:** Jeśli dodatkowe obciążenia są identyfikowane przez badanie metryki użycia zasobów, a następnie dodatkowe zasoby mogą być przydzielane do obszaru nazw w celu uwzględnienia rosnącego popytu.
>
> Liczniki rozliczeń dla usługi Service Bus są co godzinę. W przypadku skalowania w górę, płacisz tylko za dodatkowe zasoby za godziny, które zostały wykorzystane.
>

## <a name="get-started-with-premium-messaging"></a>Wprowadzenie do obsługi komunikatów Premium

Rozpoczęcie pracy z obsługą komunikatów Premium jest proste, a proces jest podobny, jak w przypadku standardowej obsługi komunikatów. Rozpocznij od [utworzenia przestrzeni nazw](service-bus-create-namespace-portal.md) w witrynie [Azure Portal](https://portal.azure.com). W obszarze **Warstwa cenowa** wybierz pozycję **Premium**. Kliknij przycisk **Wyświetl pełne szczegóły ceny**, aby uzyskać więcej informacji o poszczególnych warstwach.

![create-premium-namespace][create-premium-namespace]

Możesz również tworzyć [przestrzenie nazw Premium za pomocą szablonów usługi Azure Resource Manager](https://azure.microsoft.com/resources/templates/101-servicebus-pn-ar/).

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat obsługi komunikatów usługi Service Bus, zobacz poniższe linki:

* [Introducing Azure Service Bus Premium messaging](https://azure.microsoft.com/blog/introducing-azure-service-bus-premium-messaging/) (Wprowadzenie do komunikatów usługi Service Bus w warstwie Premium) — wpis w blogu
* [Introducing Azure Service Bus Premium messaging](https://channel9.msdn.com/Blogs/Subscribe/Introducing-Azure-Service-Bus-Premium-Messaging) (Wprowadzenie do komunikatów usługi Service Bus w warstwie Premium) — Channel9
* [Omówienie obsługi komunikatów w usłudze Service Bus](service-bus-messaging-overview.md)
* [Wprowadzenie do kolejek usługi Service Bus](service-bus-dotnet-get-started-with-queues.md)

<!--Image references-->

[create-premium-namespace]: ./media/service-bus-premium-messaging/select-premium-tier.png
