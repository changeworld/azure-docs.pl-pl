---
title: Azure Usługa sieci szkieletowej zdarzenia magazynu | Dokumentacja firmy Microsoft
description: Więcej informacji na temat usługi Azure Service Fabric EventStore
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/25/2018
ms.author: dekapur
ms.openlocfilehash: 1d235d5a75975c8d58cad1bbde0f16df2b1b3e7a
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
---
# <a name="eventstore-service-overview"></a>Omówienie usługi EventStore

>[!NOTE]
>Począwszy od platformy Service Fabric wersję 6.2. interfejsy API EventStore są obecnie w przypadku klastrów systemu Windows tylko działających na platformie Azure w wersji zapoznawczej. Pracujemy nad eksportowanie tej funkcji do systemu Linux, a także naszych klastry autonomicznego.

## <a name="overview"></a>Przegląd

Wprowadzone w wersji 6.2, usługa EventStore jest opcji monitorowania w sieci szkieletowej usług, które umożliwia poznanie stanu klastra lub obciążeń w danym punkcie w czasie. Usługa EventStore opisuje zdarzenia sieci szkieletowej usług za pośrednictwem interfejsów API umożliwia wywołania. Te interfejsy API EventStore umożliwia badanie klastra bezpośrednio, aby uzyskać dane diagnostyczne w dowolnej jednostki w klastrze i powinien służyć do:
* Diagnozowanie problemów dotyczących projektowania lub testowania lub których mogą używać monitorowania procesu
* Upewnij się, że akcje zarządzania, które są tworzone w klastrze są przetwarzane prawidłowo przez klaster

Aby wyświetlić pełną listę dostępnych w EventStore zdarzeń, zobacz [zdarzeń usługi sieć szkieletowa](service-fabric-diagnostics-event-generation-operational.md).

Można tworzyć zapytania usługi EventStore dla zdarzeń, które są dostępne dla każdej jednostki i typu jednostki w klastrze. Oznacza to, że można wyszukać zdarzenia na następujących poziomach;
* Klaster: wszystkie zdarzenia na poziomie klastra
* Węzłów: wszystkie zdarzenia poziomu węzeł
* Węzła: specyficzne dla jednego węzła, na podstawie zdarzeń `nodeName`
* Aplikacji: wszystkie zdarzenia poziomu aplikacji
* Aplikacji: zdarzenia specyficzne dla jednej aplikacji
* Usługi: zdarzenia ze wszystkich usług w klastrach
* Usługa: zdarzenia z określoną usługą
* Partycje: zdarzenia ze wszystkich partycji
* Partycja: zdarzenia z określonej partycji
* Replik: zdarzenia ze wszystkich replik / wystąpienia
* Replika: zdarzenia z replik określonych / wystąpienia


Usługa EventStore ma również umożliwia korelowanie zdarzeń w klastrze. Sprawdzając zdarzenia, które zostały zapisane w tym samym czasie z różnych obiektów, które mogą mieć wpływ na siebie nawzajem, usługa EventStore jest w stanie połączyć tych zdarzeń, aby ułatwić zidentyfikowanie przyczyny działań w klastrze. Na przykład, jeśli jedna z aplikacji stają się zła bez wprowadzania żadnych zmian wywołane, EventStore będzie też przyjrzeć się inne zdarzenia udostępniany przez platformę i może skorelować to z `NodeDown` zdarzeń. Ułatwia to szybsze wykrywanie błędów i głównych przyczyn analizy.

Aby rozpocząć korzystanie z usługi EventStore, zobacz [interfejsy API EventStore zapytania do zdarzenia klastra](service-fabric-diagnostics-eventstore-query.md).

## <a name="next-steps"></a>Kolejne kroki
* Omówienie monitorowania i diagnostyki w sieci szkieletowej usług - [monitorowania i diagnostyki dla sieci szkieletowej usług](service-fabric-diagnostics-overview.md)
* Dowiedz się więcej na temat monitorowania klastra- [monitorowania klastra i platforma](service-fabric-diagnostics-event-generation-infra.md).