---
title: Najważniejsze wskazówki dotyczące aplikacji i klastrów usługi Azure Service
description: Najważniejsze wskazówki i zagadnienia dotyczące projektowania dotyczące zarządzania klastrami, aplikacjami i usługami przy użyciu sieci szkieletowej usługi Azure.
author: peterpogorski
ms.topic: conceptual
ms.date: 06/18/2019
ms.author: pepogors
ms.openlocfilehash: 46601fd91fccb5bd0866bd999cc7643c37c6d582
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75551781"
---
# <a name="azure-service-fabric-application-and-cluster-best-practices"></a>Najważniejsze wskazówki dotyczące aplikacji i klastrów usługi Azure Service

Ten artykuł zawiera łącza do najlepszych rozwiązań dotyczących zarządzania aplikacjami i klastrami sieci szkieletowej usług Azure. Zdecydowanie zaleca się wdrożenie tych praktyk w celu optymalizacji niezawodności środowiska produkcyjnego. Użyj jednego z [szablonów klastra sieci szkieletowej usług,](https://github.com/Azure-Samples/service-fabric-cluster-templates) aby rozpocząć projektowanie rozwiązania produkcyjnego lub zaktualizować istniejący szablon, aby uwzględnić te praktyki.

## <a name="security"></a>Zabezpieczenia

* [Najważniejsze wskazówki dotyczące bezpieczeństwa](service-fabric-best-practices-security.md)

## <a name="networking"></a>Obsługa sieci

* [Najważniejsze wskazówki dotyczące sieci](service-fabric-best-practices-networking.md)

## <a name="compute-planning-and-scaling"></a>Planowanie i skalowanie obliczeń

* [Najważniejsze wskazówki dotyczące skalowania obliczeń](service-fabric-best-practices-capacity-scaling.md)
* [Planowanie zdolności obliczeniowej](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity)

## <a name="infrastructure-as-code"></a>Infrastruktura jako kod

* [Najważniejsze wskazówki dotyczące wdrażania infrastruktury jako kodu](service-fabric-best-practices-infrastructure-as-code.md)

## <a name="monitoring-and-diagnostics"></a>Monitorowanie i diagnostyka

* [Najważniejsze wskazówki dotyczące monitorowania i diagnostyki klastrów](service-fabric-best-practices-monitoring.md)

## <a name="application-design"></a>Projekt aplikacji

* [Najważniejsze wskazówki dotyczące projektowania aplikacji](service-fabric-best-practices-applications.md)

## <a name="checklist"></a>Lista kontrolna

Po zaimplementowaniu rozwiązań zaproponowanych w poprzednich sekcjach upewnij się, że zintegrowałeś wszystkie najlepsze rozwiązania z listy kontrolnej gotowości produkcyjnej:
* [Lista kontrolna gotowości produkcyjnej sieci szkieletowej usług Azure](https://docs.microsoft.com/azure/service-fabric/service-fabric-production-readiness-checklist)

## <a name="next-steps"></a>Następne kroki

* Tworzenie klastra na maszynach wirtualnych lub komputerach z systemem Windows Server: [Tworzenie klastra sieci szkieletowej usług dla systemu Windows Server](service-fabric-cluster-creation-for-windows-server.md)
* Tworzenie klastra na maszynach wirtualnych lub komputerach z systemem Linux: [tworzenie klastra systemu Linux](service-fabric-cluster-creation-via-portal.md)
* Rozwiązywanie problemów z siecią szkieletową usług: [przewodniki dotyczące rozwiązywania problemów](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides)