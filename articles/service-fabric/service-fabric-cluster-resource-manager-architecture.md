---
title: Architektura usługi Resource Manager | Dokumentacja firmy Microsoft
description: Przegląd architektury programu Service Fabric Menedżer zasobów klastra.
services: service-fabric
documentationcenter: .net
author: masnider
manager: chackdan
editor: ''
ms.assetid: 6c4421f9-834b-450c-939f-1cb4ff456b9b
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: bfbdb05e8d2764d2b878e22d236cae30519da176
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "62113976"
---
# <a name="cluster-resource-manager-architecture-overview"></a>Omówienie architektury Menedżer zasobów klastra
Menedżer zasobów klastra usługi Service Fabric jest centralnym usługa, która działa w klastrze. Zarządza żądanego stanu usług w klastrze, szczególnie w odniesieniu do zużycia zasobów i wszystkie reguły umieszczania. 

Aby zarządzać zasobami w klastrze, Menedżer zasobów klastra usługi Service Fabric musi mieć kilka rodzajów informacji:

- Usługi, które obecnie istnieją.
- Każda usługa bieżący (lub domyślny) użycie zasobów 
- Pozostała pojemność klastra 
- Pojemność węzłów w klastrze 
- Ilość zasobów używanych w każdym węźle

Użycie zasobów w danej usługi może ulegać zmianie, a usług zwykle interesujące więcej niż jednego typu zasobu. W różnych usługach może być zarówno rzeczywiste fizyczne zasoby fizyczne i mierzony. Usługi mogą śledzić fizycznych metryk, takich jak zużycie pamięci i dysku. Zazwyczaj usług może interesujące metryki logiczny — np. "WorkQueueDepth" lub "TotalRequests". Metryki zarówno logicznych i fizycznych, może służyć w tym samym klastrze. Metryki mogą być współużytkowane przez wiele usług lub być specyficzne dla określonej usługi.

## <a name="other-considerations"></a>Inne zagadnienia
Właściciele i Operatorzy klastra może różnić się od autorów usług i aplikacji lub co najmniej są tego samego systemy trwałych różnych osób. Podczas opracowywania aplikacji znasz kilka kwestii, o go wymaga. Masz oszacować zasoby będą korzystać z jej i jak powinny być wdrażane różnych usług. Na przykład warstwa sieci web wymaga do uruchomienia w węzłach połączone z Internetem, podczas gdy usługi bazy danych nie powinien. Inny przykład usług sieci web prawdopodobnie zależy od procesora CPU i sieci podczas obsługi usługi warstwy danych więcej informacji na temat zużycia pamięci i dysku. Jednak osoby obsługi zdarzenia aktywnej witryny, dla tej usługi w środowisku produkcyjnym i który jest zarządzany uaktualnienie do usługi zawiera różne zadania w celu i wymaga różnych narzędzi. 

Zarówno klaster, jak i usługi są dynamiczne:

- Liczba węzłów w klastrze można zwiększyć lub zmniejszyć
- Węzły o różnych rozmiarach i typy mogą pochodzić i przejdź
- Usługi można utworzyć, usunąć i zmień ich alokacji żądanego zasobu i reguły umieszczania
- Uaktualnienia lub innych operacji zarządzania można wdrażać za pośrednictwem klastra na poziomie aplikacji, na poziomie infrastruktury
- Awarii może nastąpić w dowolnej chwili.

## <a name="cluster-resource-manager-components-and-data-flow"></a>Składniki Menedżer zasobów klastra i przepływu danych
Menedżer zasobów klastra musi śledzić z wymaganiami dotyczącymi poszczególnych usług i zużycia zasobów przez każdy obiekt usługi w ramach tych usług. Menedżer zasobów klastra ma dwie części pojęć: agentów, działających w każdym węźle i odpornej na uszkodzenia usługa. W agentach na każdej obciążenia śledzenie węzła Raporty z usług, agregacji je, a następnie okresowo zgłoście je. Usługa Menedżer zasobów klastra agreguje wszystkie informacje z agentów lokalnych i reaguje na podstawie bieżącej konfiguracji.

Spójrzmy na poniższym diagramie:

<center>

![Architektura modułu równoważenia zasobów][Image1]
</center>

Podczas wykonywania istnieje wiele zmian, które może się zdarzyć. Na przykład teraz załóżmy, że ilość zasobów, których używanie niektórych usług zmienia, niektóre usługi zakończy się niepowodzeniem i niektóre węzły sprzężenia i pozostaw klastra. Wszystkie zmiany w węźle są agregowane i okresowo wysyłane do usługi Menedżer zasobów klastra (1,2) gdzie one są ponownie agregowane, analizowane i przechowywane. Co kilka sekund, które usługa sprawdza zmiany i określa, czy wszystkie akcje są niezbędne (3). Na przykład można zauważyć, że niektóre węzły pustych zostały dodane do klastra. W rezultacie zdecyduje się przenieść niektórych usług do tych węzłów. Menedżer zasobów klastra można także zauważyć, że określony węzeł jest przeciążony lub że pewnych usług zawiera nie powiodło się lub zostało usunięte, zwalnianiu zasobów w innym miejscu.

Spróbujmy Spójrz na poniższym diagramie i zobacz, co dzieje się potem. Załóżmy, że Menedżer zasobów klastra Określa, czy konieczne jest wprowadzenie zmian. Współrzędne ją z innymi usługami systemu (w szczególności Menedżer trybu Failover) do wprowadź niezbędne zmiany. Następnie konieczne polecenia są wysyłane do odpowiednich węzłów (4). Załóżmy na przykład, że usługi Resource Manager zauważyć, że Węzeł5 został przeciążone, a więc postanowiła przenieść usługa B z Węzeł5 na Węzeł4. Na koniec ponowna konfiguracja (5) klastra wygląda następująco:

<center>

![Architektura modułu równoważenia zasobów][Image2]
</center>

## <a name="next-steps"></a>Kolejne kroki
- Menedżer zasobów klastra ma wiele opcji do opisywania klastra. Aby dowiedzieć się więcej na ich temat, zapoznaj się z tego artykułu na [opisujące klaster usługi Service Fabric](./service-fabric-cluster-resource-manager-cluster-description.md)
- Menedżer zasobów klastra podstawowego obowiązków ponowne równoważenie klastra i wymuszanie reguły umieszczania. Aby uzyskać więcej informacji na temat konfigurowania tych zachowań, zobacz [równoważenie klastra usługi Service Fabric](./service-fabric-cluster-resource-manager-balancing.md)

[Image1]:./media/service-fabric-cluster-resource-manager-architecture/Service-Fabric-Resource-Manager-Architecture-Activity-1.png
[Image2]:./media/service-fabric-cluster-resource-manager-architecture/Service-Fabric-Resource-Manager-Architecture-Activity-2.png
