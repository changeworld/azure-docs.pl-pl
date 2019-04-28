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
ms.date: 01/23/2019
ms.author: pepogors
ms.openlocfilehash: a0174a5442de7a10e45e9dc2a2f43f5f401d1a69
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60803174"
---
# <a name="azure-service-fabric-application-and-cluster-best-practices"></a>Najważniejsze wskazówki dotyczące platformy Azure aplikacji usługi Service Fabric i klastra

Aby zarządzać aplikacjami usługi Azure Service Fabric i klastrami pomyślnie, pewne operacje, zdecydowanie zalecamy wykonać, aby zoptymalizować niezawodności w środowisku produkcyjnym; można wykonywać operacje zdefiniowane w tym dokumencie i wybierz jedną z naszych [klastra Azure przykłady usługi Service Fabric szablony](https://github.com/Azure-Samples/service-fabric-cluster-templates) rozpocząć projektowanie rozwiązania do produkcji lub zmodyfikować istniejący szablon, aby zawierały te praktyki.

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

## <a name="checklist"></a>Lista kontrolna

Po wykonaniu wszystkich powyższych sekcji, upewnij się, że zintegrowano wszystkie najlepsze rozwiązania na liście kontrolnej gotowości produkcyjnym:
* [Lista kontrolna dotycząca usługi Azure Service Fabric produkcji gotowości](https://docs.microsoft.com/azure/service-fabric/service-fabric-production-readiness-checklist)

## <a name="next-steps"></a>Kolejne kroki

* Tworzenie klastra na maszynach wirtualnych lub komputerach z systemem Windows Server: [Tworzenie klastra usługi Service Fabric dla systemu Windows Server](service-fabric-cluster-creation-for-windows-server.md)
* Tworzenie klastra na maszynach wirtualnych lub komputerach z systemem Linux: [Tworzenie klastra systemu Linux](service-fabric-cluster-creation-via-portal.md)
* Rozwiązywanie problemów: [Przewodnik rozwiązywania problemów w usłudze Service Fabric](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides)