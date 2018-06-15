---
title: Skonfigurować adresy IP, aby połączyć po przejściu w tryb failover Azure z usługą Azure Site Recovery | Dokumentacja firmy Microsoft
description: Zawiera opis sposobu konfigurowania adresu IP, adresy połączyć się z maszynami wirtualnymi Azure po pracy awaryjnej z lokalnych z usługą Azure Site Recovery
services: site-recovery
documentationcenter: ''
author: mayanknayar
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 05/11/2018
ms.author: manayar
ms.openlocfilehash: 9d4bf43ea727bbf7df79532499007dbd23eea925
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/11/2018
ms.locfileid: "34071641"
---
# <a name="set-up-ip-addressing-to-connect-after-failover-to-azure"></a>Skonfiguruj adresowanie nawiązania połączenia po przejściu w tryb failover Azure IP

W tym artykule opisano wymagania sieciowe dotyczące połączenia z maszyn wirtualnych platformy Azure, po użyciu [usługi Azure Site Recovery](site-recovery-overview.md) usługi dla replikacji i trybu failover na platformie Azure.

W tym artykule dowiesz się o:

> [!div class="checklist"]
> * Metody połączenia, których można używać
> * Jak używać innego adresu IP dla replikowanych maszyn wirtualnych platformy Azure
> * Jak zachować adresów IP dla maszyn wirtualnych Azure po trybu failover

## <a name="connecting-to-replica-vms"></a>Łączenie z repliki maszyny wirtualne

Podczas planowania strategii trybu failover i replikacji, na których, jedno z pytań klucza jest sposób nawiązywania połączenia z maszyną wirtualną Azure po pracy awaryjnej. Istnieje kilka opcji do wyboru podczas projektowania strategii sieci dla repliki maszyny wirtualne platformy Azure:

- **Użyj innego adresu IP**: można określić, aby użyć różnych zakresów adresów IP dla sieci maszyny Wirtualnej Azure zreplikowane. W tym scenariuszu maszyny Wirtualnej pobiera nowego adresu IP po w tryb failover i aktualizowania DNS jest wymagany.
- **Zachowaj tego samego adresu IP**: może chcesz użyć tego samego zakresu adresów IP jak w witrynie głównej lokalnymi dla sieci platformy Azure po pracy awaryjnej. Utrzymywanie tego samego adresu IP adresów upraszcza odzyskiwanie dzięki zmniejszeniu problemów dotyczących sieci po pracy awaryjnej. Jednak jeśli przeprowadzasz replikację do platformy Azure, należy zaktualizować tras z nowej lokalizacji adresy IP po pracy awaryjnej.

## <a name="retaining-ip-addresses"></a>Zachowanie adresów IP

Usługa Site Recovery zapewnia adresy możliwości, aby zachować stałego adresu IP podczas przechodzenie w tryb failover na platformie Azure, z trybu failover podsieci.

- Tryb failover podsieci określonej podsieci jest obecny w lokacji 1 lub 2 lokacji, ale nigdy nie w obu lokacjach jednocześnie.
- Aby zachować przestrzeń adresów IP w przypadku trybu failover, można programowo Rozmieść infrastruktury router przenieść podsieci z jednej lokacji.
- Podczas pracy w trybie failover podsieci są przenoszone wraz z skojarzone chronionych maszyn wirtualnych. Główną wadą jest, że w przypadku awarii, należy przenieść całej podsieci.


### <a name="failover-example"></a>Przykład trybu failover

Oto przykład dla trybu failover do platformy Azure przy użyciu fikcyjnej firmie Woodgrove Bank.

- Banku Woodgrove obsługuje aplikacje biznesowe w lokacji lokalnej. One hostowanie swoich aplikacji mobilnych na platformie Azure.
- Brak połączenia lokacja lokacja sieci VPN między lokalnymi krawędzi sieci i sieci wirtualnej platformy Azure. Z powodu połączenie sieci VPN sieci wirtualnej platformy Azure jest wyświetlany jako rozszerzenie sieci lokalnej.
- Woodgrove chce, aby replikować obciążenia lokalnego do platformy Azure z usługą Site Recovery.
 - Woodgrove ma aplikacje, które są zależne od stałe adresy IP, dlatego należy zachować adresów IP dla aplikacji, po przejściu w tryb failover Azure.
 - Zasoby, które działają na platformie Azure używają 172.16.1.0/24 zakres adresów IP, 172.16.2.0/24.

![Przed podsieci trybu failover](./media/site-recovery-network-design/network-design7.png)

**Infrastruktura przed trybu failover**


Dla Woodgrove można było do replikowania jego maszyn wirtualnych na platformie Azure przy zachowaniu adresy IP, tutaj firmy, co firma musi wykonać:


1. Tworzenie sieci wirtualnej platformy Azure, w którym zostanie utworzona maszynach wirtualnych platformy Azure, po pracy w trybie failover maszyny lokalnymi. Rozszerzenie sieci lokalnej, należy tak, aby aplikacje działają w trybie Failover bezproblemowo.
2. Przed trybu failover w usłudze Site Recovery ich przypisać ten sam adres IP we właściwościach maszyny. Po pracy w trybie failover Usługa Site Recovery przypisuje ten adres maszyny Wirtualnej platformy Azure.
3. Po uruchomieniu trybu failover i maszyn wirtualnych platformy Azure są tworzone przy użyciu tego samego adresu IP, łączą się z sieci za pomocą funkcji [sieci wirtualnej do sieci wirtualnej połączenia](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md). Ta akcja umożliwia pisanie skryptów.
4. Należy zmodyfikować trasy, aby odzwierciedlał tego 192.168.1.0/24 teraz zostały przeniesione do platformy Azure.


**Infrastruktura po trybu failover**

![Po podsieci w tryb failover](./media/site-recovery-network-design/network-design9.png)

#### <a name="site-to-site-connection"></a>Połączenie lokacja lokacja

Oprócz połączenia do wirtualnymi, po przejściu w tryb failover Woodgrove można skonfigurować połączenie sieci VPN typu lokacja lokacja:
- Podczas konfigurowania połączenia lokacja lokacja w sieci platformy Azure, który może tylko kierować ruch do lokalizacji lokalnego (lokalnego ntwork) Jeśli zakres adresów IP różni się od zakresu adresów IP z lokalnymi. Jest to spowodowane Azure nie obsługuje rozciągnięty podsieci. Tak Jeśli masz podsieci 192.168.1.0/24 lokalnymi, nie można dodać 192.168.1.0/24 sieci lokalnej w sieci platformy Azure. Oczekiwany jest Azure nie może ustalić, czy brak aktywnych maszyn wirtualnych w podsieci i że podsieć jest tworzony tylko odzyskiwania po awarii.
- Aby można było poprawnie kierować ruchem sieciowym spoza sieci platformy Azure, nie może powodować konflikt podsieci w sieci i sieci lokalnej.




## <a name="assigning-new-ip-addresses"></a>Przypisanie nowych adresów IP

To [wpis w blogu](http://azure.microsoft.com/blog/2014/09/04/networking-infrastructure-setup-for-microsoft-azure-as-a-disaster-recovery-site/) wyjaśniono, jak skonfigurować infrastrukturę sieci Azure, gdy nie trzeba zachować adresy IP po pracy awaryjnej. Go rozpoczyna się od opisu aplikacji wygląda jak skonfigurować sieci lokalnej na platformie Azure i zawiera informacje o uruchamianiu przechodzenia w tryb failover.

## <a name="next-steps"></a>Kolejne kroki
[Tryb failover](site-recovery-failover.md)
