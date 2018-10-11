---
title: Zarządzanie regionami w usłudze Azure Stack | Dokumentacja firmy Microsoft
description: Omówienie zarządzania regionów w usłudze Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: e94775d5-d473-4c03-9f4e-ae2eada67c6c
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/05/2018
ms.author: sethm
ms.reviewer: efemmano
ms.openlocfilehash: 401b81ceb7ab71528a4ad11bc7d8944b4d732933
ms.sourcegitcommit: 4b1083fa9c78cd03633f11abb7a69fdbc740afd1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/10/2018
ms.locfileid: "49078869"
---
# <a name="region-management-in-azure-stack"></a>Zarządzanie regionami w usłudze Azure Stack

*Dotyczy: Usługa Azure Stack zintegrowane systemy i usługi Azure Stack Development Kit*

Usługa Azure Stack korzysta z koncepcji regiony, które są jednostek logicznych składa się zasoby sprzętowe, które tworzą infrastruktury Azure Stack. Wewnątrz zarządzanie regionami znajdują się wszystkie zasoby, które są wymagane do pomyślnie działać infrastruktury Azure Stack.

Jedna zintegrowana wdrożenia systemu (nazywane *chmurą usługi Azure Stack*) tworzy w jednym regionie. Każdy zestaw Azure Stack Development Kit ma jednego regionu, o nazwie **lokalnego**. Jeśli wdrażanie drugiego system zintegrowany z usługi Azure Stack lub skonfigurować inne wystąpienie development kit na oddzielnych sprzętu, ta chmura usługi Azure Stack jest innym regionem.

## <a name="information-available-through-the-region-management-tile"></a>Informacje o dostępnych za pośrednictwem Kafelek Zarządzanie regionu

Usługa Azure Stack zawiera zestaw możliwości zarządzania regionu są dostępne w **zarządzanie regionami** kafelka. Ten Kafelek jest dostępny dla operatorów usługi Azure Stack na domyślny pulpit nawigacyjny w portalu administratora. Za pomocą tego kafelka można monitorować i aktualizować swój region usługi Azure Stack i jego składników, które są specyficzne dla regionu.

 ![Kafelek Zarządzanie regionu](media/azure-stack-manage-region/image1.png)

 Jeśli klikniesz pozycję regionu na kafelku zarządzania regionu, możesz uzyskać dostęp następujące informacje:

  ![Opis okienka, w bloku zarządzania regionu](media/azure-stack-manage-region/image2.png)

1. **W menu zasobów**. W tym miejscu możesz uzyskać dostęp obszarów zarządzania określonej infrastruktury oraz wyświetlanie i zarządzanie nimi użytkowników zasobów, takich jak konta magazynu i sieci wirtualnych.

2. **Alerty**. Zawiera listę całego systemu alertów i zawiera szczegółowe informacje dotyczące każdego z tych alertów.

3. **Aktualizacje**. W tym miejscu można wyświetlić bieżącej wersji infrastruktury Azure Stack, dostępne aktualizacje i historię aktualizacji. Można również zaktualizować zintegrowanego systemu.

4. **Dostawcy zasobów**. Dostawcy zasobów jest miejscem, aby zarządzać funkcji użytkownika, oferowane przez składniki wymagane do uruchamiania usługi Azure Stack. Każdy dostawca zasobów jest powiązana z środowisko administracyjne. Doświadczenia te mogą obejmować alerty dla określonego dostawcy, metryki i innych funkcji zarządzania specyficzne dla dostawcy zasobów.

5. **Infrastruktura ról**. Infrastruktura ról są składniki niezbędne do uruchomienia usługi Azure Stack. Tylko role infrastruktury, które zgłaszać alerty są wyświetlane. Wybierając rolę, możesz wyświetlić alerty skojarzone z roli i wystąpień roli, w którym ta rola jest uruchomiona.

## <a name="next-steps"></a>Kolejne kroki

- [Monitorowanie kondycji i alertów w usłudze Azure Stack](azure-stack-monitor-health.md)
- [Zarządzanie aktualizacjami w usłudze Azure Stack](azure-stack-updates.md)
