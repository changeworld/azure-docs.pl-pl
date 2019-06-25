---
title: Najważniejsze wskazówki dotyczące platformy Azure aplikacji usługi Service Fabric i klastra | Dokumentacja firmy Microsoft
description: Najlepsze rozwiązania dotyczące zarządzania klastrami usługi Service Fabric i aplikacji.
services: service-fabric
documentationcenter: .net
author: peterpogorski
manager: chackdan
editor: ''
ms.assetid: 19ca51e8-69b9-4952-b4b5-4bf04cded217
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/18/2019
ms.author: pepogors
ms.openlocfilehash: 5fdbd3f15b11e4c3975ca29627d5984382bcf049
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67206801"
---
# <a name="azure-service-fabric-application-and-cluster-best-practices"></a>Najważniejsze wskazówki dotyczące platformy Azure aplikacji usługi Service Fabric i klastra

Ten artykuł zawiera linki do najlepsze rozwiązania dotyczące zarządzania aplikacjami usługi Azure Service Fabric i klastrami. Firma Microsoft zdecydowanie zaleca się korzystanie z nich, aby zoptymalizować niezawodności w środowisku produkcyjnym. Użyj jednej z [szablony klastra usługi Service Fabric](https://github.com/Azure-Samples/service-fabric-cluster-templates) można rozpocząć projektowanie rozwiązania do produkcji lub zaktualizować istniejący szablon, aby zawierały te praktyki.

## <a name="security"></a>Bezpieczeństwo

* [Najlepsze rozwiązania dotyczące zabezpieczeń](service-fabric-best-practices-security.md)

## <a name="networking"></a>Networking

* [Najlepsze rozwiązania dotyczące sieci](service-fabric-best-practices-networking.md)

## <a name="compute-planning-and-scaling"></a>Planowanie i skalowanie obliczeń

* [Najlepsze praktyki dotyczące skalowania obliczeń](service-fabric-best-practices-capacity-scaling.md)
* [Obliczenia, planowania pojemności](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity)

## <a name="infrastructure-as-code"></a>Infrastruktura jako kod

* [Najlepsze rozwiązania dotyczące wdrażania infrastruktury jako kodu](service-fabric-best-practices-infrastructure-as-code.md)

## <a name="monitoring-and-diagnostics"></a>Monitorowanie i diagnostyka

* [Najlepsze rozwiązania dotyczące klastra, monitorowania i diagnostyki](service-fabric-best-practices-monitoring.md)

## <a name="application-design"></a>Projekt aplikacji

* [Najlepsze rozwiązania dotyczące projektowania aplikacji](service-fabric-best-practices-applications.md)

## <a name="checklist"></a>Lista kontrolna

Po zaimplementowaniu rozwiązania sugerowane w poprzednich sekcjach, upewnij się, zintegrowano najlepsze rozwiązania na liście kontrolnej gotowości produkcyjnym:
* [Lista kontrolna Azure Service Fabric produkcji gotowości](https://docs.microsoft.com/azure/service-fabric/service-fabric-production-readiness-checklist)

## <a name="next-steps"></a>Kolejne kroki

* Tworzenie klastra na maszynach wirtualnych lub komputerach z systemem Windows Server: [Tworzenie klastra usługi Service Fabric dla systemu Windows Server](service-fabric-cluster-creation-for-windows-server.md)
* Tworzenie klastra na maszynach wirtualnych lub komputerach z systemem Linux: [Tworzenie klastra systemu Linux](service-fabric-cluster-creation-via-portal.md)
* Rozwiązywanie problemów z usługi Service Fabric [Wskazówki dotyczące rozwiązywania problemów](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides)