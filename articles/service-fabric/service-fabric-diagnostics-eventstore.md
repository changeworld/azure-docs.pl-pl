---
title: Azure Service Fabric zdarzeń Store | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o bazie danych EventStore usługi Azure Service Fabric
services: service-fabric
documentationcenter: .net
author: srrengar
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/21/2018
ms.author: srrengar
ms.openlocfilehash: cd58e24a51b153d6bf217f7d32a82e882183ed73
ms.sourcegitcommit: beb4fa5b36e1529408829603f3844e433bea46fe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/22/2018
ms.locfileid: "52290675"
---
# <a name="eventstore-service-overview"></a>Omówienie usługi bazy danych EventStore

## <a name="overview"></a>Przegląd

Wprowadzona w wersji 6.2, usługa bazy danych EventStore jest opcji monitorowania w usłudze Service Fabric, który umożliwia zrozumienie stanu klastra lub obciążeń w danym punkcie w czasie. Usługa bazy danych EventStore przedstawia zdarzenia usługi Service Fabric za pomocą zestawu interfejsów API (dostępnym za pośrednictwem punkty końcowe REST lub za pomocą biblioteki klienta). Te interfejsy API bazy danych EventStore umożliwia badanie klastra bezpośrednio po to, aby uzyskać dane diagnostyczne w dowolnej jednostce w klastrze i powinny być używane do pomocy:

* Diagnozuj problemy z tworzenia i testowania lub gdy być może używasz potoku monitorowania
* Upewnij się, że akcje zarządzania, które są tworzone w klastrze są przetwarzane prawidłowo przez Twój klaster

Aby wyświetlić pełną listę zdarzeń, które są dostępne w bazie danych EventStore, zobacz [zdarzenia usługi Service Fabric](service-fabric-diagnostics-event-generation-operational.md).

>[!NOTE]
>Począwszy od usługi Service Fabric w wersji 6.2. interfejsy API bazy danych EventStore są obecnie dostępne w klastrach Windows działających na platformie Azure tylko w wersji zapoznawczej. Pracujemy nad przenoszenie tej funkcji do systemu Linux, a także naszych autonomicznych klastrów.

Usługa bazy danych EventStore można wykonywać zapytania, zdarzenia, które są dostępne dla każdej jednostki i typu jednostki w klastrze. Oznacza to, że można wyszukiwać zdarzenia na następujących poziomach;
* Klaster: wszystkie zdarzenia na poziomie klastra
* Węzły: wszystkie zdarzenia poziomu węzeł
* Węzła: specyficzne dla jednego węzła, w oparciu o zdarzenia `nodeName`
* Aplikacje: wszystkich zdarzeń na poziomie aplikacji
* Aplikacji: specyficzne dla aplikacji jednej zdarzenia
* Usługi: zdarzenia z wszystkich usług w klastrach
* Usługa: zdarzenia z określonej usługi
* Partycje: zdarzenia ze wszystkich partycji
* Partycja: zdarzenia z określonej partycji
* Repliki: zdarzenia replik wszystkich / wystąpienia
* Repliki: zdarzenia replik określonych / wystąpienie


Usługa bazy danych EventStore ma również możliwość korelowanie zdarzeń w klastrze. Patrząc na zdarzenia, które zostały napisane w tym samym czasie z różnymi jednostkami, które mogą mieć wpływ na siebie, usługa bazy danych EventStore jest można połączyć te zdarzenia, aby ułatwić zidentyfikowanie przyczyny działań w klastrze. Na przykład, jeśli jedna z aplikacji stają się zła, bez konieczności wprowadzania zmian wywołane, bazy danych EventStore również przyjrzeć się inne zdarzenia udostępnianych przez platformę i będą można skorelować za pomocą `NodeDown` zdarzeń. To ułatwia szybsze wykrywanie awarii i głównych przyczyn analizy.

Firma Microsoft zaleca się korzystanie z bazy danych EventStore do szybkiej analizy i mieć migawki pogląd na sposób działania klastra, a jeśli rzeczy dzieją się jako.

Aby rozpocząć korzystanie z niej bazy danych EventStore, zobacz [interfejsów API bazy danych EventStore zapytań dla zdarzenia klastra](service-fabric-diagnostics-eventstore-query.md).

## <a name="next-steps"></a>Kolejne kroki
* Omówienie monitorowania i diagnostyki w usłudze Service Fabric - [monitorowania i diagnostyki dla usługi Service Fabric](service-fabric-diagnostics-overview.md)
* Dowiedz się więcej o monitorowaniu klastra- [monitorowanie klastra i platformy](service-fabric-diagnostics-event-generation-infra.md).