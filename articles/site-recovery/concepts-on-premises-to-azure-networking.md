---
title: Konfigurowanie adresów IP, odnoszący się do łączenia z po odzyskiwanie po awarii i trybu failover na platformie Azure przy użyciu usługi Azure Site Recovery | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób konfigurowania adresu IP, odnoszący się do połączenia z maszynami wirtualnymi platformy Azure po odzyskiwania po awarii i tryb failover ze środowiska lokalnego z usługą Azure Site Recovery
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 4/15/2019
ms.author: mayg
ms.openlocfilehash: 2e1cbb2446501d0afda29eba179e388b5a22e6a8
ms.sourcegitcommit: b8a8d29fdf199158d96736fbbb0c3773502a092d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2019
ms.locfileid: "59565686"
---
# <a name="set-up-ip-addressing-to-connect-to-azure-vms-after-failover"></a>Konfigurowanie adresów IP, odnoszący się nawiązać połączenia z maszynami wirtualnymi platformy Azure po włączeniu trybu failover

W tym artykule opisano wymagania sieciowe dotyczące nawiązywania połączenia w przypadku maszyn wirtualnych platformy Azure po zakończeniu korzystania z [usługi Azure Site Recovery](site-recovery-overview.md) usługi podczas replikacji i trybu failover na platformie Azure.

W tym artykule poznasz:

> [!div class="checklist"]
> * Metody połączenia, których można użyć
> * Jak używać innego adresu IP dla replikowanych maszyn wirtualnych platformy Azure
> * Jak zachować adresy IP maszyn wirtualnych platformy Azure po włączeniu trybu failover

## <a name="connecting-to-replica-vms"></a>Nawiązywanie połączenia z maszyn wirtualnych repliki

Podczas planowania strategii trybu failover i replikacji, na których, jedną z kluczowych pytań jest sposób nawiązywania połączenia z maszyną wirtualną Azure po włączeniu trybu failover. Istnieje kilka opcji podczas projektowania strategii sieci, repliki maszyn wirtualnych platformy Azure:

- **Użyj innego adresu IP**: Możesz wybrać, czy używać różnych zakresów adresów IP dla sieci maszyny Wirtualnej platformy Azure replikowane. W tym scenariuszu maszyna wirtualna pobiera nowego adresu IP po włączeniu trybu failover i aktualizacja DNS jest wymagana.
- **Zachować ten sam adres IP**: Można użyć tego samego zakresu adresów IP co w lokacji głównej w środowisku lokalnym, dla sieci platformy Azure po włączeniu trybu failover. Utrzymywanie w tym samym adresem IP adresów upraszcza odzyskiwanie przez zmniejszenie problemów dotyczących sieci po włączeniu trybu failover. Jednak jeśli przeprowadzasz replikację do platformy Azure, musisz zaktualizować tras do nowej lokalizacji, adresy IP, po włączeniu trybu failover.

## <a name="retaining-ip-addresses"></a>Zachowywanie adresów IP

Usługa Site Recovery zapewnia możliwość zachować stały IP adresów podczas przechodzenia w tryb failover na platformę Azure za pomocą podsieci przejścia w tryb failover.

- Z trybem failover podsieć określonej podsieci jest obecny w lokacji 1 lub 2 lokacji, ale nigdy nie w obu lokacjach jednocześnie.
- W celu utrzymania przestrzeń adresów IP w przypadku pracy awaryjnej, można programowo zorganizować infrastruktury routera przenieść podsieci z jednej lokacji.
- Podczas pracy w trybie failover podsieci przejściem skojarzone chronionych maszyn wirtualnych. Główną wadą jest to, że w przypadku awarii, należy przenieść całej podsieci.


### <a name="failover-example"></a>Przykład trybu failover

Spójrzmy na przykład dla trybu failover na platformie Azure przy użyciu fikcyjnej firmie Woodgrove Bank.

- Banku Woodgrove Bank hostuje swoje aplikacje biznesowe w lokacji lokalnej. Mogą hostować swoje aplikacje mobilne na platformie Azure.
- Istnieje łączność lokacja lokacja sieci VPN między siecią krawędzi ich w środowisku lokalnym i sieci wirtualnej platformy Azure. Ze względu na połączenie sieci VPN sieci wirtualnej na platformie Azure zostanie wyświetlony jako rozszerzenia sieci lokalnej.
- Woodgrove chce, aby replikowania obciążeń lokalnych do platformy Azure z usługą Site Recovery.
  - Woodgrove ma aplikacje, które zależą od ustalonych adresy IP, w związku z czym muszą zachować adresy IP dla aplikacji, po włączeniu trybu failover na platformie Azure.
  - Zasoby działające na platformie Azure Użyj 172.16.1.0/24 zakres adresów IP, 172.16.2.0/24.

![Przed włączeniem trybu failover podsieć](./media/site-recovery-network-design/network-design7.png)

**Infrastruktura przed włączeniem trybu failover**


Dla Woodgrove można było do jej maszyny wirtualne można replikować na platformę Azure, zachowując adresy IP, tutaj tego, co firma musi zrobić:


1. Tworzenie sieci wirtualnej platformy Azure, w której maszyny wirtualne platformy Azure zostanie utworzony po przełączeniu w tryb failover maszyn lokalnych. Rozszerzenie sieci lokalnej, należy tak, aby aplikacje w trybie Failover bezproblemowo.
2. Przed włączeniem trybu failover w usłudze Site Recovery mogą przypisać ten sam adres IP we właściwościach maszyny. Po przejściu w tryb failover Usługa Site Recovery przypisuje ten adres do maszyny Wirtualnej platformy Azure.
3. Po uruchomieniu trybu failover i maszyn wirtualnych platformy Azure są tworzone przy użyciu tego samego adresu IP, łączą się z sieci za pomocą funkcji [połączenie między sieciami wirtualnymi](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md). Ta akcja umożliwia pisanie skryptów.
4. Muszą oni tras, tak że 192.168.1.0/24 przeniósł się teraz na platformie Azure, aby zmodyfikować.


**Infrastruktury po włączeniu trybu failover**

![Po włączeniu trybu failover podsieć](./media/site-recovery-network-design/network-design9.png)

#### <a name="site-to-site-connection"></a>Połączenia lokacja lokacja

Oprócz połączenia sieć wirtualna-sieć wirtualna, po przejściu w tryb failover Woodgrove można skonfigurować połączenie sieci VPN typu lokacja lokacja:
- Podczas konfigurowania połączenia lokacja lokacja w sieci platformy Azure, który może tylko kierować ruch do lokalizacji lokalnej (sieci lokalne) Jeśli zakres adresów IP różni się od zakresu adresów IP w środowisku lokalnym. Jest to spowodowane platforma Azure nie obsługuje rozciągnięta podsieci. Dlatego jeśli masz podsieci 192.168.1.0/24 w środowisku lokalnym, nie można dodać 192.168.1.0/24 sieci lokalnej sieci platformy Azure. Jest to oczekiwane, ponieważ platformy Azure nie wie, że nie istnieją żadne aktywne maszyny wirtualne w podsieci i że podsieć jest tworzona dla wyłącznie podczas odzyskiwania po awarii.
- Aby można było poprawnie kierować ruchem sieciowym poza siecią platformy Azure, nie może powodować konflikt podsieci w sieci i sieci lokalnej.




## <a name="assigning-new-ip-addresses"></a>Przypisanie nowych adresów IP

To [wpis w blogu](https://azure.microsoft.com/blog/2014/09/04/networking-infrastructure-setup-for-microsoft-azure-as-a-disaster-recovery-site/) opisano sposób konfigurowania infrastruktury sieci platformy Azure, jeśli nie potrzebujesz zachować adresy IP po włączeniu trybu failover. Jego rozpoczyna się od opisu aplikacji wygląda jak skonfigurować sieć lokalną na platformie Azure i zawiera informacje na temat uruchamiania trybu failover.

## <a name="next-steps"></a>Kolejne kroki
[Uruchamianie trybu failover](site-recovery-failover.md)
