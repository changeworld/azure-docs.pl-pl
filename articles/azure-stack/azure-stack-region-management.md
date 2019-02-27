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
ms.date: 11/27/2018
ms.author: sethm
ms.reviewer: efemmano
ms.lastreviewed: 11/27/2018
ms.openlocfilehash: 2845bb09fa44c3bd9cee914e2446403b4182b0d5
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/26/2019
ms.locfileid: "56871047"
---
# <a name="region-management-in-azure-stack"></a>Zarządzanie regionami w usłudze Azure Stack

*Dotyczy: Zintegrowane usługi Azure Stack, systemy i usługi Azure Stack Development Kit*

Usługi Azure Stack korzysta z koncepcji *regionów*, które są jednostek logicznych składa się zasoby sprzętowe, które tworzą infrastruktury Azure Stack. Zarządzanie regionami zawiera wszystkie zasoby, które są wymagane do pomyślnie działać infrastruktury Azure Stack.

Jedna zintegrowana wdrożenia systemu (nazywane *chmurą usługi Azure Stack*) tworzy w jednym regionie. Każdy zestaw Azure Stack Development Kit ma jednego regionu, o nazwie **lokalnego**. Jeśli wdrażanie drugiego system zintegrowany z usługi Azure Stack lub skonfigurować inne wystąpienie development kit na oddzielnych sprzętu, ta chmura usługi Azure Stack jest innym regionem.

## <a name="information-available-through-the-region-management-tile"></a>Informacje o dostępnych za pośrednictwem Kafelek Zarządzanie regionu

Usługa Azure Stack zawiera zestaw możliwości zarządzania regionu są dostępne w **zarządzanie regionami** kafelka. Ten Kafelek jest dostępny dla operatorów usługi Azure Stack na domyślny pulpit nawigacyjny w portalu administratora. Za pomocą tego kafelka można monitorować i aktualizować swój region usługi Azure Stack i jego składników, które są specyficzne dla regionu.

![Kafelek Zarządzanie regionu](media/azure-stack-region-management/image1.png)

Jeśli klikniesz regionu w **zarządzanie regionami** kafelka dostęp następujące informacje:

[![Opis okienka, w bloku zarządzania Region](media/azure-stack-region-management/regionssm.png "blok zarządzania regionu")](media/azure-stack-region-management/regions.png#lightbox)

1. **W menu zasobów**. Dostęp do obszarów zarządzania określonej infrastruktury oraz wyświetlanie i zarządzanie zasobów użytkownika, takie jak konta magazynu i sieci wirtualnych.

2. **Alerty**. Zawiera listę całego systemu alertów i szczegółów na temat każdego z tych alertów.

3. **Aktualizacje**. Wyświetl bieżącą wersję infrastruktury Azure Stack, dostępne aktualizacje i historię aktualizacji. Można również zaktualizować zintegrowanego systemu.

4. **Dostawcy zasobów**. Zarządzaj funkcje użytkownika, oferowane przez składniki wymagane do uruchamiania usługi Azure Stack. Każdy dostawca zasobów jest powiązana z środowisko administracyjne. Doświadczenia te mogą obejmować alerty dla określonego dostawcy, metryki i innych funkcji zarządzania specyficzne dla dostawcy zasobów.

5. **Infrastruktura ról**. Składniki niezbędne do uruchomienia usługi Azure Stack. Tylko role infrastruktury, które zgłaszać alerty są wyświetlane. Wybierając rolę, możesz wyświetlić alerty skojarzone z roli i wystąpień roli, w którym ta rola jest uruchomiona.

6. **Właściwości**. Stan rejestracji i szczegółowe cechy danego środowiska, w bloku zarządzania regionu. Możliwe stany **zarejestrowanej** lub **niezarejestrowany**. Jeśli nie zarejestrowano, pokazuje również identyfikator subskrypcji platformy Azure umożliwia rejestrowanie usługi Azure Stack, wraz z rejestracji grupy zasobów i nazwę.

## <a name="next-steps"></a>Kolejne kroki

- [Monitor health and alerts in Azure Stack (Monitorowanie kondycji i alertów w usłudze Azure Stack)](azure-stack-monitor-health.md)
- [Zarządzanie aktualizacjami w usłudze Azure Stack](azure-stack-updates.md)
