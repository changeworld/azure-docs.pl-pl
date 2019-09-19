---
title: Dostosowywanie konfiguracji sieci dla maszyny wirtualnej trybu failover | Microsoft Docs
description: Zawiera omówienie dostosowywania konfiguracji sieci dla maszyny wirtualnej trybu failover w replikacji maszyn wirtualnych platformy Azure przy użyciu Azure Site Recovery.
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 08/07/2019
ms.author: rajanaki
ms.openlocfilehash: 1905d6afb023b1bed15f1359fed8477d815acb45
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/18/2019
ms.locfileid: "71087701"
---
# <a name="customize-networking-configurations-of-the-target-azure-vm"></a>Dostosowywanie konfiguracji sieci dla docelowej maszyny wirtualnej platformy Azure

Ten artykuł zawiera wskazówki dotyczące dostosowywania konfiguracji sieci na docelowej maszynie wirtualnej platformy Azure podczas replikowania i odzyskiwania maszyn wirtualnych platformy Azure z jednego regionu do innego przy użyciu [Azure Site Recovery](site-recovery-overview.md).

## <a name="before-you-start"></a>Przed rozpoczęciem

Dowiedz się, jak Site Recovery zapewnia odzyskiwanie po awarii w [tym scenariuszu](azure-to-azure-architecture.md).

## <a name="supported-networking-resources"></a>Obsługiwane zasoby sieciowe

Podczas replikowania maszyn wirtualnych platformy Azure można dostarczyć następujące konfiguracje zasobów kluczy dla maszyny wirtualnej trybu failover.

- [Wewnętrzny moduł równoważenia obciążenia](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview#what-is-standard-load-balancer)
- [Publiczny adres IP](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#public-ip-addresses)
- [Grupa zabezpieczeń sieci](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group) dla podsieci i karty sieciowej

 > [!IMPORTANT]
  > Te ustawienia są obsługiwane tylko w ramach operacji przełączania do trybu failover, a nie do testowania pracy w trybie failover.

## <a name="pre-requisites"></a>Wymagania wstępne

- Upewnij się, że konfiguracje po stronie odzyskiwania są planowane z wyprzedzeniem.
- Należy najpierw utworzyć zasoby sieciowe. Podaj je jako dane wejściowe, aby usługa Azure Site Recovery mogła przestrzegać tych ustawień i upewnić się, że maszyna wirtualna trybu failover będzie zgodna z tymi ustawieniami.

## <a name="steps-to-customize-failover-networking-configurations"></a>Procedura dostosowywania konfiguracji sieci trybu failover

1. Przejdź do **pozycji zreplikowane elementy**. 
2. Kliknij żądaną maszynę wirtualną platformy Azure.
3. Kliknij pozycję **obliczenia i sieć**i **Edytuj**. Zobaczysz, że ustawienia konfiguracji karty sieciowej zawierają odpowiednie zasoby w źródle. 

     ![Dostosuj](media/azure-to-azure-customize-networking/edit-networking-properties.png)

4. Kliknij pozycję **Edytuj** obok karty sieciowej, którą chcesz skonfigurować. W następnym bloku, który zostanie otwarty, wybierz odpowiednie wstępnie utworzone zasoby w miejscu docelowym.

    ![Karta sieciowa — oddrążenie](media/azure-to-azure-customize-networking/nic-drilldown.png) 

5. Kliknij przycisk **OK**.

Site Recovery będzie teraz przestrzegać tych ustawień i upewnić się, że maszyna wirtualna w trybie failover jest połączona z wybranym zasobem za pośrednictwem odpowiedniej karty sieciowej.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

### <a name="unable-to-view-or-select-a-resource"></a>Nie można wyświetlić lub wybrać zasobu

Jeśli nie możesz wybrać lub wyświetlić zasobu sieci, wykonaj następujące czynności sprawdzające, & warunki:

- Pole docelowe dla zasobu sieciowego jest włączone tylko wtedy, gdy źródłowa maszyna wirtualna ma odpowiednie dane wejściowe. Jest to oparte na zasadzie, która w przypadku scenariusza odzyskiwania po awarii powinna mieć być dokładna lub skalowana w dół wersja źródła.
- W przypadku każdego z zasobów sieciowych z pytaniem niektóre filtry są stosowane na liście rozwijanej, aby upewnić się, że maszyna wirtualna trybu failover może dołączyć do wybranego zasobu i utrzymywać niezawodność trybu failover. Te filtry są oparte na tych samych warunkach sieciowych, które zostały zweryfikowane podczas konfigurowania źródłowej maszyny wirtualnej.

Walidacje wewnętrznego modułu równoważenia obciążenia:

1. Subskrypcja i region LB oraz docelowa maszyna wirtualna powinny być takie same.
2. Sieć wirtualna skojarzona z wewnętrzną Load Balancer i VMshould elementu docelowego jest taka sama.
3. Jednostka SKU publicznego adresu IP maszyny wirtualnej i jednostka SKU wewnętrznego modułu równoważenia obciążenia muszą być takie same.
4. Jeśli docelowa maszyna wirtualna jest skonfigurowana do umieszczania w strefie dostępności, sprawdź, czy moduł równoważenia obciążenia jest nadmiarowy strefy lub nie jest częścią żadnej strefy dostępności. (Podstawowe usługi równoważenia obciążenia SKU nie obsługują stref i nie będą wyświetlane na liście rozwijanej w tym przypadku).
5. Upewnij się, że wewnętrzny moduł równoważenia obciążenia ma wstępnie utworzoną pulę zaplecza i konfigurację frontonu.


Publiczny adres IP:
    
1. Subskrypcja i region publicznego adresu IP oraz docelowa maszyna wirtualna powinny być takie same.
2. Jednostka SKU publicznego adresu IP maszyny wirtualnej i jednostka SKU wewnętrznego modułu równoważenia obciążenia muszą być takie same.

Sieciowa Grupa zabezpieczeń:
1. Subskrypcja i region grupy zabezpieczeń sieci i docelowej maszyny wirtualnej powinny być takie same.


> [!WARNING]
> Jeśli docelowa maszyna wirtualna jest skojarzona z zestawem dostępności, należy skojarzyć publiczny adres IP/wewnętrzny moduł równoważenia obciążenia z tą samą jednostką SKU, co w przypadku publicznego adresu IP/wewnętrznego modułu równoważenia obciążenia innej maszyny wirtualnej w zestawie dostępności. Niewykonanie tej czynności może spowodować awarię trybu failover.
