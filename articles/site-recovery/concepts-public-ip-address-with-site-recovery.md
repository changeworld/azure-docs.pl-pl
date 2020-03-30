---
title: Przypisywanie publicznych adresów IP po przewidzieli awaryjnej pracy za pomocą usługi Azure Site Recovery
description: W tym artykule opisano sposób konfigurowania publicznych adresów IP za pomocą usługi Azure Site Recovery i usługi Azure Traffic Manager w celu odzyskiwania po awarii i migracji
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: mayg
ms.openlocfilehash: b1f3ffa6fc90fc0cab0217d1b71907342f2dbd0d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79281953"
---
# <a name="set-up-public-ip-addresses-after-failover"></a>Konfigurowanie publicznych adresów IP po przemijaniu awaryjnym

Publiczne adresy IP umożliwiają zasobom internetowym komunikowanie się w ramach ruchu przychodzącego z zasobami platformy Azure. Publiczne adresy IP umożliwiają również zasobom platformy Azure komunikowanie się w ramach ruchu wychodzącego z Internetem i publicznymi usługami platformy Azure za pomocą adresu IP przypisanego do zasobu.
- Komunikacja przychodząca z Internetu do zasobu, takiego jak maszyny wirtualne platformy Azure (VM), bramy aplikacji platformy Azure, moduły równoważenia obciążenia platformy Azure, bramy sieci VPN platformy Azure i inne. Nadal można komunikować się z niektórymi zasobami, takimi jak maszyny wirtualne, z Internetu, jeśli maszyna wirtualna nie ma przypisanego publicznego adresu IP, o ile maszyna wirtualna jest częścią puli zaplecza modułu równoważenia obciążenia, a modułowi równoważenia obciążenia jest przypisywany publiczny adres IP.
- Łączność wychodząca z Internetem przy użyciu przewidywalnego adresu IP. Na przykład maszyna wirtualna może komunikować się wychodzących do Internetu bez publicznego adresu IP przypisane do niego, ale jego adres jest adres sieciowy przetłumaczone przez platformę Azure do nieprzewidywalnego adresu publicznego, domyślnie. Przypisanie publicznego adresu IP do zasobu umożliwia poznanie, który adres IP jest używany dla połączenia wychodzącego. Choć jest to przewidywalne, adres może ulec zmianie, w zależności od wybranej metody przypisania. Aby uzyskać więcej informacji, zobacz [Tworzenie publicznego adresu IP](../virtual-network/virtual-network-public-ip-address.md#create-a-public-ip-address). Aby dowiedzieć się więcej o połączeniach wychodzących z zasobów platformy Azure, zobacz [Opis połączeń wychodzących](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

W usłudze Azure Resource Manager publiczny adres IP jest zasobem, który ma własne właściwości. Niektóre zasoby, z którymi możesz powiązać zasób publicznego adresu IP, to:

* Interfejsy sieciowe maszyny wirtualnej
* Moduły równoważenia obciążenia dostępne z Internetu
* Bramy sieci VPN
* Bramy aplikacji

W tym artykule opisano, jak można używać publicznych adresów IP z odzyskiwaniem witryny.

## <a name="public-ip-address-assignment-using-recovery-plan"></a>Przypisanie publicznego adresu IP przy użyciu planu odzyskiwania

Publicznego adresu IP aplikacji produkcyjnej **nie można zachować w uchodze w pracy awaryjnej**. Obciążenia utworzone w ramach procesu pracy awaryjnej muszą być przypisane do publicznego zasobu IP platformy Azure dostępnego w regionie docelowym. Ten krok można wykonać ręcznie lub jest zautomatyzowany z planami odzyskiwania. Plan odzyskiwania gromadzi maszyny w grupach odzyskiwania. Pomaga zdefiniować systematyczny proces odzyskiwania. Za pomocą planu odzyskiwania można narzucić zamówienie i zautomatyzować akcje wymagane na każdym kroku przy użyciu śreżystek systemu Azure Automation do pracy awaryjnej na platformie Azure lub skryptów.

Konfiguracja jest następująca:
- Utwórz [plan odzyskiwania](../site-recovery/site-recovery-create-recovery-plans.md#create-a-recovery-plan) i zgrupuj obciążenia w razie potrzeby w planie.
- Dostosuj plan, dodając krok, aby dołączyć publiczny adres IP przy użyciu [skryptów śmięty runbook usługi Azure Automation](../site-recovery/site-recovery-runbook-automation.md#customize-the-recovery-plan) do maszyny wirtualnej w sieci failed.

 
## <a name="public-endpoint-switching-with-dns-level-routing"></a>Przełączanie publicznych punktów końcowych z routingiem na poziomie DNS

Usługa Azure Traffic Manager umożliwia routing poziomu DNS między punktami końcowymi i może pomóc [w obniżaniu RTO](../site-recovery/concepts-traffic-manager-with-site-recovery.md#recovery-time-objective-rto-considerations) w scenariuszu odzyskiwania po awarii. 

Dowiedz się więcej o scenariuszach pracy awaryjnej w usłudze Traffic Manager:
1. [Lokalna](../site-recovery/concepts-traffic-manager-with-site-recovery.md#on-premises-to-azure-failover) usługa pracy awaryjnej platformy Azure za pomocą usługi Traffic Manager 
2. [Usługa azure do pracy awaryjnej platformy Azure](../site-recovery/concepts-traffic-manager-with-site-recovery.md#azure-to-azure-failover) za pomocą usługi Traffic Manager 

Konfiguracja jest następująca:
- Utwórz [profil usługi Traffic Manager](../traffic-manager/traffic-manager-create-profile.md).
- Korzystając z metody routingu **priorytetu,** utwórz dwa punkty końcowe — **podstawowy** dla źródła i **Failover** dla platformy Azure. **Podstawowy** jest przypisany priorytet 1 i **pracy awaryjnej** jest przypisany priorytet 2.
- **Podstawowym punktem** końcowym może być [azure](../traffic-manager/traffic-manager-endpoint-types.md#azure-endpoints) lub [zewnętrzne](../traffic-manager/traffic-manager-endpoint-types.md#external-endpoints) w zależności od tego, czy środowisko źródłowe znajduje się wewnątrz lub poza platformą Azure.
- Punkt końcowy **trybu failover** jest tworzony jako punkt końcowy **platformy Azure.** Użyj **statycznego publicznego adresu IP,** ponieważ będzie to zewnętrzny punkt końcowy dla usługi Traffic Manager w przypadku awarii.

## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o [usłudze Traffic Manager za pomocą usługi Azure Site Recovery](../site-recovery/concepts-traffic-manager-with-site-recovery.md)
- Dowiedz się więcej o [metodach routingu](../traffic-manager/traffic-manager-routing-methods.md)usługi Traffic Manager .
- Dowiedz się więcej o [planach odzyskiwania](site-recovery-create-recovery-plans.md) w celu zautomatyzowania pracy awaryjnej aplikacji.
