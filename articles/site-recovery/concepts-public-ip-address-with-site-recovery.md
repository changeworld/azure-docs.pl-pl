---
title: Używania publicznych adresów IP po włączeniu trybu failover za pomocą usługi Azure Site Recovery | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób konfigurowania publicznych adresów IP przy użyciu usługi Azure Site Recovery i Azure Traffic Manager na potrzeby odzyskiwania po awarii i migracji
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: mayg
ms.openlocfilehash: 1f20818f0b899eede9fff05d71e98c8bffb94b0a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "62101957"
---
# <a name="set-up-public-ip-addresses-after-failover"></a>Konfigurowanie publicznych adresów IP, po włączeniu trybu failover

Publiczne adresy IP umożliwiają zasobom internetowym komunikowanie się w ramach ruchu przychodzącego z zasobami platformy Azure. Publiczne adresy IP umożliwiają również zasobom platformy Azure komunikowanie się w ramach ruchu wychodzącego z Internetem i publicznymi usługami platformy Azure za pomocą adresu IP przypisanego do zasobu.
- Komunikacji przychodzącej z Internetu do zasobu, takich jak Azure Virtual Machines (VM), bramy aplikacji platformy Azure, usługi równoważenia obciążenia platformy Azure, bram Azure VPN Gateway i inne. Użytkownik może nadal komunikować się z niektórych zasobów, takich jak maszyny wirtualne z Internetu, jeśli maszyna wirtualna nie ma publiczny adres IP przypisany do niego, tak długo, jak maszyna wirtualna jest częścią puli zaplecza modułu równoważenia obciążenia i równoważenia obciążenia jest przypisany publiczny adres IP.
- Łączność wychodząca z Internetem przy użyciu adresu IP przewidywalne. Na przykład maszyny wirtualnej mogą komunikować się ruch wychodzący do Internetu bez publicznego adresu IP do niej przypisany, lecz jego adres sieci adresu tłumaczone przez usługę Azure nieprzewidywalne adres publiczny, domyślnie. Przypisanie publicznego adresu IP do zasobu umożliwia wiedzieć, których adres IP jest używany dla połączeń wychodzących. Chociaż przewidywalne, można zmienić adres, w zależności od wybranej metody przydziału. Aby uzyskać więcej informacji, zobacz [tworzenie publicznego adresu IP](../virtual-network/virtual-network-public-ip-address.md#create-a-public-ip-address). Aby dowiedzieć się więcej na temat połączeń wychodzących z zasobów platformy Azure, zobacz [informacje o połączeniach wychodzących](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

W usłudze Azure Resource Manager, publiczny adres IP jest zasobem, który ma własne właściwości. Niektóre zasoby, z którymi możesz powiązać zasób publicznego adresu IP, to:

* Interfejsy sieciowe maszyny wirtualnej
* Moduły równoważenia obciążenia dostępne z Internetu
* Bramy sieci VPN
* Bramy aplikacji

W tym artykule opisano, jak użyć publicznych adresów IP, z usługą Site Recovery.

## <a name="public-ip-address-assignment-using-recovery-plan"></a>Publiczne przypisania adresu IP przy użyciu planu odzyskiwania

Publiczny adres IP aplikacji produkcyjnej **nie mogą być przechowywane w trybie failover**. Obciążenia wznowione, jako część procesu pracy awaryjnej musi być przypisany zasób publicznego adresu IP usługi Azure dostępne w regionie docelowym. Ten krok można wykonać albo ręcznie lub jest automatycznie przy użyciu planów odzyskiwania. Plan odzyskiwania zbiera maszyn w grupach odzyskiwania. Pomaga zdefiniować proces systematyczne odzyskiwania. Umożliwia nakłada zamówienie planu odzyskiwania i zautomatyzować wymagane przeprowadzenie kolejnych czynności na każdym etapie, za pomocą elementów runbook usługi Azure Automation do trybu failover na platformie Azure lub skryptów.

Instalator jest w następujący sposób:
- Tworzenie [planu odzyskiwania](../site-recovery/site-recovery-create-recovery-plans.md#create-a-recovery-plan) i grupach obciążenia w razie potrzeby do planu.
- Dostosuj planu, dodając krok do dołączenia, publiczny adres IP, za pomocą [elementów runbook usługi Azure Automation](../site-recovery/site-recovery-runbook-automation.md#customize-the-recovery-plan) skryptów w trybie Failover maszyny Wirtualnej.

 
## <a name="public-endpoint-switching-with-dns-level-routing"></a>Publiczny punkt końcowy przełączenie Routing poziomu DNS

Usługa Azure Traffic Manager umożliwia routing między punktami końcowymi na poziomie DNS i może pomóc [obniżenie swoje RTO](../site-recovery/concepts-traffic-manager-with-site-recovery.md#recovery-time-objective-rto-considerations) dla scenariusza odzyskiwania po awarii. 

Dowiedz się więcej na temat Scenariusze trybu failover za pomocą usługi Traffic Manager:
1. [Środowiska lokalnego do trybu failover systemu Azure](../site-recovery/concepts-traffic-manager-with-site-recovery.md#on-premises-to-azure-failover) z usługą Traffic Manager 
2. [Tryb failover do platformy](../site-recovery/concepts-traffic-manager-with-site-recovery.md#azure-to-azure-failover) z usługą Traffic Manager 

Instalator jest w następujący sposób:
- Tworzenie [profilu usługi Traffic Manager](../traffic-manager/traffic-manager-create-profile.md).
- Przy użyciu **priorytet** routingu metody, Utwórz dwa punkty końcowe — **podstawowego** źródła i **trybu Failover** dla platformy Azure. **Podstawowy** jest przypisany priorytet 1 i **trybu Failover** jest przypisany priorytet 2.
- **Podstawowego** punkt końcowy może być [Azure](../traffic-manager/traffic-manager-endpoint-types.md#azure-endpoints) lub [zewnętrznych](../traffic-manager/traffic-manager-endpoint-types.md#external-endpoints) w zależności od tego, czy Twoje środowisko źródłowe jest wewnątrz lub spoza platformy Azure.
- **Trybu Failover** punkt końcowy został utworzony jako **Azure** punktu końcowego. Użyj **statyczny publiczny adres IP** będzie to zewnątrz punktu końcowego usługi Traffic Manager w przypadku awarii.

## <a name="next-steps"></a>Kolejne kroki
- Dowiedz się więcej o [usługi Traffic Manager z usługą Azure Site Recovery](../site-recovery/concepts-traffic-manager-with-site-recovery.md)
- Dowiedz się więcej o usłudze Traffic Manager [metod routingu](../traffic-manager/traffic-manager-routing-methods.md).
- Dowiedz się więcej o [planów odzyskiwania](site-recovery-create-recovery-plans.md) do automatyzowania trybu failover w aplikacji.
