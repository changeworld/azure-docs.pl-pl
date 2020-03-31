---
title: Dostosowywanie konfiguracji sieci dla maszyny wirtualnej trybu failover | Dokumenty firmy Microsoft
description: Zawiera omówienie dostosowywania konfiguracji sieci dla maszyny wirtualnej trybu failover w replikacji maszyn wirtualnych platformy Azure przy użyciu usługi Azure Site Recovery.
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 10/21/2019
ms.author: rajanaki
ms.openlocfilehash: 96ffa34166797945afc04c66b03fe151d26c65bc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76292862"
---
# <a name="customize-networking-configurations-of-the-target-azure-vm"></a>Dostosowywanie konfiguracji sieci dla docelowej maszyny wirtualnej platformy Azure

Ten artykuł zawiera wskazówki dotyczące dostosowywania konfiguracji sieci na docelowej maszynie wirtualnej platformy Azure podczas replikowania i odzyskiwania maszyn wirtualnych platformy Azure z jednego regionu do drugiego przy użyciu [usługi Azure Site Recovery.](site-recovery-overview.md)

## <a name="before-you-start"></a>Przed rozpoczęciem

Dowiedz się, jak odzyskiwanie witryny zapewnia odzyskiwanie po awarii w [tym scenariuszu.](azure-to-azure-architecture.md)

## <a name="supported-networking-resources"></a>Obsługiwane zasoby sieciowe

Podczas replikowania maszyn wirtualnych platformy Azure można podać następujące kluczowe konfiguracje zasobów dla maszyny wirtualnej trybu failover:

- [Wewnętrzny moduł równoważenia obciążenia](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)
- [Publiczny adres IP](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#public-ip-addresses)
- [Sieć grupy zabezpieczeń](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group) zarówno dla podsieci, jak i dla karty sieciowej

## <a name="prerequisites"></a>Wymagania wstępne

- Upewnij się, że planujesz konfiguracje po stronie odzyskiwania z wyprzedzeniem.
- Utwórz zasoby sieciowe z wyprzedzeniem. Podaj je jako dane wejściowe, aby usługa Azure Site Recovery mogła honorować te ustawienia i upewnić się, że maszyna wirtualna trybu failover jest zgodna z tymi ustawieniami.

## <a name="customize-failover-and-test-failover-networking-configurations"></a>Dostosowywanie konfiguracji sieci trybu failover i testowania trybu failover

1. Przejdź do pozycji **Elementy replikowane**. 
2. Wybierz żądaną maszynę wirtualną platformy Azure.
3. Wybierz **pozycję Oblicz i Sieć** i wybierz pozycję **Edytuj**. Należy zauważyć, że ustawienia konfiguracji karty sieciowej zawierają odpowiednie zasoby u źródła. 

     ![Dostosowywanie konfiguracji sieci trybu failover](media/azure-to-azure-customize-networking/edit-networking-properties.png)

4. Wybierz testową sieć wirtualną trybu failover. Można pozostawić puste i wybrać jeden w czasie pracy awaryjnej testu.
5. Sieć trybu failover to Wybierz **edytuj** w pobliżu karty sieciowej, którą chcesz skonfigurować. W następnym bloku, który zostanie otwarty, wybierz odpowiednie wstępnie utworzone zasoby w testowej lokalizacji trybu failover i trybu failover.

    ![Edytowanie konfiguracji karty sieciowej](media/azure-to-azure-customize-networking/nic-drilldown.png) 

6. Kliknij przycisk **OK**.

Odzysk lokacji będzie teraz honorować te ustawienia i upewnić się, że maszyna wirtualna na uchodę w pracy awaryjnej jest podłączona do wybranego zasobu za pośrednictwem odpowiedniej karty sieciowej.

Po wyzwoleniu pracy awaryjnej testu za pośrednictwem planu odzyskiwania zawsze poprosi o to sieć wirtualną platformy Azure. Ta sieć wirtualna będzie używana do testowania pracy awaryjnej dla maszyn, które nie miały wstępnie skonfigurowanych ustawień trybu failover testowych.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

### <a name="unable-to-view-or-select-a-resource"></a>Nie można wyświetlić lub wybrać zasobu

Jeśli nie możesz wybrać ani wyświetlić zasobu sieciowego, przejdź przez następujące testy i warunki:

- Pole docelowe zasobu sieciowego jest włączone tylko wtedy, gdy źródłowa maszyna wirtualna miała odpowiednie dane wejściowe. Jest to oparte na zasadzie, że w przypadku scenariusza odzyskiwania po awarii, należy dokładnie lub skalowane w dół wersji źródła.
- Dla każdego zasobu sieciowego niektóre filtry są stosowane na liście rozwijanej, aby upewnić się, że maszyna wirtualna trybu failover może dołączyć się do wybranego zasobu i niezawodność pracy awaryjnej jest utrzymywana. Filtry te są oparte na tych samych warunkach sieciowych, które zostałyby zweryfikowane podczas konfigurowania źródłowej maszyny Wirtualnej.

Wewnętrzne sprawdzanie poprawności modułu równoważenia obciążenia:

- Subskrypcja i region modułu równoważenia obciążenia i docelowej maszyny Wirtualnej powinny być takie same.
- Sieć wirtualna skojarzona z wewnętrznym modułem równoważenia obciążenia i siecią docelowej maszyny Wirtualnej powinna być taka sama.
- Docelowa jednostka SKU IP maszyny Wirtualnej i jednostka SKU modułu równoważenia obciążenia wewnętrznego powinny być takie same.
- Jeśli docelowa maszyna wirtualna jest skonfigurowana do umieszczenia w strefie dostępności, sprawdź, czy moduł równoważenia obciążenia jest strefą nadmiarową lub częścią dowolnej strefy dostępności. (Podstawowe moduły równoważenia obciążenia jednostki SKU nie obsługują stref i w tym przypadku nie będą wyświetlane na liście rozwijanej).
- Upewnij się, że wewnętrzny moduł równoważenia obciążenia ma wstępnie utworzoną pulę zaplecza i konfigurację front-end.

Publiczny adres IP:

- Subskrypcja i region publicznego adresu IP i docelowej maszyny Wirtualnej powinny być takie same.
- Docelowa jednostka SKU IP maszyny Wirtualnej i jednostka SKU modułu równoważenia obciążenia wewnętrznego powinny być takie same.

Sieciowa grupa zabezpieczeń:
- Subskrypcja i region sieciowej grupy zabezpieczeń i docelowej maszyny Wirtualnej powinny być takie same.


> [!WARNING]
> Jeśli docelowa maszyna wirtualna jest skojarzona z zestawem dostępności, należy skojarzyć publiczny adres IP i wewnętrzny moduł równoważenia obciążenia tej samej jednostki SKU z publicznym adresem IP drugiej maszyny wirtualnej i wewnętrznym modułem równoważenia obciążenia w zestawie dostępności. Jeśli tego nie zrobisz, przewija się w stan failover, może się nie powieść.
