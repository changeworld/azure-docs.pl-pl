---
title: Sieciowe grupy zabezpieczeń z usługi Azure Site Recovery | Dokumentacja firmy Microsoft
description: Informacje dotyczące używania grup zabezpieczeń sieci z usługą Azure Site Recovery do migrowania i odzyskiwania po awarii
services: site-recovery
documentationcenter: ''
author: mayanknayar
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 05/21/2018
ms.author: manayar
ms.openlocfilehash: 2b35578e2dc49cd47689f62fc47c5341ea8767a4
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34655756"
---
# <a name="network-security-groups-with-azure-site-recovery"></a>Sieciowe grupy zabezpieczeń z usługi Azure Site Recovery

Grupy zabezpieczeń sieci są używane, aby ograniczyć ruch sieciowy do zasobów w sieci wirtualnej. A [grupy zabezpieczeń sieci (NSG)](../virtual-network/security-overview.md#network-security-groups) zawiera listę dozwolonych lub zablokowanych ruchu sieciowego przychodzącego i wychodzącego oparte na źródłowy lub docelowy adres IP, port i protokół reguły zabezpieczeń.

W modelu wdrażania usługi Resource Manager można skojarzyć z podsieci lub do interfejsów sieciowych poszczególnych grup NSG. Jeśli sieciowa grupa zabezpieczeń jest skojarzona z podsiecią, te reguły są stosowane do wszystkich zasobów połączonych z tą podsiecią. Dodatkowo można ograniczyć ruchu przez również kojarzenie grupy NSG do interfejsów sieciowych poszczególnych w podsieci, która jest już skojarzona grupa NSG.

W tym artykule opisano, jak można użyć grup zabezpieczeń sieci z usługą Azure Site Recovery.

## <a name="using-network-security-groups"></a>Przy użyciu grup zabezpieczeń sieci

Poszczególnych podsieci może być zero lub jeden, skojarzone NSG. Pojedynczego interfejsu sieciowego może również zawierać zero lub jeden, skojarzone NSG. Można więc skutecznie z podwójną ruchu ograniczeń dla maszyny wirtualnej przez skojarzenie grupy NSG najpierw do podsieci, a następnie inne grupy NSG do interfejsu sieciowego maszyny Wirtualnej. Stosowanie zasad grupy NSG w takim przypadku zależy od kierunek ruchu i priorytet reguły zabezpieczeń zastosowane.

Należy wziąć pod uwagę prosty przykład z jedną maszyną wirtualną w następujący sposób:
-   Maszyna wirtualna jest umieszczona wewnątrz **podsieci Contoso**.
-   **Podsieci contoso** jest skojarzony z **NSG podsieci**.
-   Interfejs sieciowy maszyn wirtualnych jest również skojarzony z **NSG wirtualna**.

![Grupy NSG z usługą Site Recovery](./media/concepts-network-security-group-with-site-recovery/site-recovery-with-network-security-group.png)

W tym przykładzie dla ruchu przychodzącego NSG podsieci jest obliczana jako pierwsza. Cały ruch dozwolony przez NSG podsieci jest następnie oceniane przez NSG maszyny Wirtualnej. Odwrotnej jest stosowana dla ruchu wychodzącego z oceniane najpierw NSG maszyny Wirtualnej. Cały ruch dozwolony przez NSG maszyna wirtualna jest następnie oceniane przez NSG podsieci.

Dzięki temu aplikacja reguły zabezpieczeń szczegółowego. Na przykład można zezwolić przychodzący dostęp do Internetu dla kilku VMs aplikacji (na przykład frontonu maszyn wirtualnych) w obszarze podsieci, ale ograniczyć dostęp do Internetu dla ruchu przychodzącego do innych maszyn wirtualnych (takie jak bazy danych i innych maszyn wirtualnych wewnętrznej bazy danych). W takim przypadku możesz mieć łagodniejszą reguły na NSG podsieci, dzięki czemu ruch internetowy i ograniczyć dostęp do określonych maszyn wirtualnych przy odmowie dostępu w NSG maszyny Wirtualnej. Taki sam mogą być stosowane dla ruchu wychodzącego.

Podczas konfigurowania takie konfiguracje NSG, upewnij się, że priorytety poprawne są stosowane do [reguły zabezpieczeń](../virtual-network/security-overview.md#security-rules). Reguły są przetwarzane w kolejności priorytetów. Im niższy numer, tym wyższy priorytet, więc te o niższych numerach są przetwarzane przed tymi o wyższych numerach. Kiedy ruch jest zgodny z regułą, przetwarzanie zostaje zatrzymane. W związku z tym żadne istniejące reguły o niższych priorytetach (wyższych numerach), które mają takie same atrybuty jak reguły o wyższych priorytetach, nie będą przetwarzane.

Możesz czasami nie wiedzieć, że grupy zabezpieczeń sieci są stosowane do interfejsu sieciowego i podsieci. Możesz sprawdzić łączny zasady stosowane do interfejsu sieciowego, wyświetlając [reguły efektywnym elementem systemu zabezpieczeń](../virtual-network/virtual-network-network-interface.md#view-effective-security-rules) dla interfejsu sieciowego. Można również użyć [Sprawdź przepływ IP](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md) możliwości w [obserwatora sieciowego Azure](../network-watcher/network-watcher-monitoring-overview.md) ustalenie, czy komunikacja jest dozwolona na lub z karty sieciowej. Narzędzie informuje, czy komunikacja jest dozwolona, oraz która reguła zabezpieczeń sieci zezwala lub nie zezwala na ruch.

## <a name="on-premises-to-azure-replication-with-nsg"></a>Dane lokalne na Azure replikacji z grupy NSG

Usługa Azure Site Recovery umożliwia odzyskiwanie po awarii i migracji na platformie Azure na potrzeby lokalnego [maszyn wirtualnych funkcji Hyper-V](hyper-v-azure-architecture.md), [maszyn wirtualnych VMware](vmware-azure-architecture.md), i [serwerów fizycznych](physical-azure-architecture.md). Dla wszystkich lokalnego do platformy Azure scenariuszy dane replikacji są wysyłane do i przechowywane na koncie magazynu Azure. Podczas replikacji nie należy zwrócić opłaty maszyny wirtualnej. Po uruchomieniu trybu failover na platformie Azure Site Recovery automatycznie tworzy maszyny wirtualne Azure IaaS.

Po utworzeniu maszyn wirtualnych po przejściu w tryb failover Azure grupy NSG można ograniczyć ruch sieciowy do sieci wirtualnej i maszyn wirtualnych. Usługa Site Recovery nie tworzy grupy NSG w ramach operacji trybu failover. Zaleca się utworzenie wymagane NGSs Azure przed rozpoczęciem pracy awaryjnej. Następnie można skojarzyć grup NSG do przejścia w tryb failover maszyny wirtualne automatycznie podczas pracy awaryjnej zaawansowanym skryptów automatyzacji z usługą Site Recovery [planów odzyskiwania](site-recovery-create-recovery-plans.md).

Na przykład, jeśli konfiguracja maszyny Wirtualnej po pracy w trybie failover jest podobny do [przykładowy scenariusz](concepts-network-security-group-with-site-recovery.md#using-network-security-groups) szczegółowe powyżej:
-   Można utworzyć **sieci wirtualnej firmy Contoso** i **podsieci Contoso** jako część DR planowanie w celu region platformy Azure.
-   Można również utworzyć i skonfigurować zarówno **NSG podsieci** oraz **NSG wirtualna** jako część tego samego DR planowania.
-   **NSG podsieci** mogą następnie natychmiast skojarzony **podsieci Contoso**, ponieważ zarówno grupy NSG, jak i podsieć nie jest już dostępna.
-   **Maszyna wirtualna NSG** może być skojarzony z maszyną wirtualną w tryb failover przy użyciu planów odzyskiwania.

Po utworzenia i skonfigurowania grup NSG, zaleca się uruchamiania [testowanie trybu failover](site-recovery-test-failover-to-azure.md) można zweryfikować inicjowanych przez skrypty skojarzenia NSG i łączności maszyn wirtualnych po pracy w trybie failover.

## <a name="azure-to-azure-replication-with-nsg"></a>Replikacja Azure do platformy Azure z grupy NSG

Usługa Azure Site Recovery umożliwia odzyskiwanie po awarii [maszyn wirtualnych platformy Azure](azure-to-azure-architecture.md). Podczas włączania replikacji na maszynach wirtualnych platformy Azure, Site Recovery można utworzyć repliki sieci wirtualnych (w tym podsieci i podsieci bramy) na region docelowy i utworzyć wymagane mapowań między źródłem i celem sieci wirtualnych. Można również wstępnie utworzyć sieci po stronie docelowej i podsieci i używać tego samego podczas włączania replikacji. Usługi Site Recovery nie tworzy żadnych maszyn wirtualnych na przed regionu Azure docelowego [pracy awaryjnej](azure-to-azure-tutorial-failover-failback.md).

W przypadku replikacji maszyny Wirtualnej platformy Azure, upewnij się, że reguły NSG w źródle region platformy Azure pozwala [łączność wychodząca](azure-to-azure-about-networking.md#outbound-connectivity-for-ip-address-ranges) dla ruchu replikacji. Można również testowanie i weryfikowanie tych reguł wymagany przez to [Przykładowa konfiguracja NSG](azure-to-azure-about-networking.md#example-nsg-configuration).

Usługa Site Recovery nie tworzy ani nie replikować grup NSG w ramach operacji trybu failover. Zaleca się utworzenie NGSs wymagane w celu region platformy Azure, przed rozpoczęciem pracy awaryjnej. Następnie można skojarzyć grup NSG do przejścia w tryb failover maszyny wirtualne automatycznie podczas pracy awaryjnej zaawansowanym skryptów automatyzacji z usługą Site Recovery [planów odzyskiwania](site-recovery-create-recovery-plans.md).

Biorąc pod uwagę [przykładowy scenariusz](concepts-network-security-group-with-site-recovery.md#using-network-security-groups) opisanych wcześniej:
-   Odzyskiwanie lokacji można utworzyć repliki **sieci wirtualnej firmy Contoso** i **podsieci Contoso** w docelowym regionie Azure po włączeniu replikacji dla maszyny Wirtualnej.
-   Można utworzyć żądanego replik **NSG podsieci** i **NSG wirtualna** (o nazwie, na przykład **NSG podsieci docelowej** i **docelowej maszyny Wirtualnej NSG**, odpowiednio) w celu regionu Azure, co zapewnia żadnych dodatkowych reguł wymagane na region docelowy.
-   **Docelowa grupa NSG podsieci** można następnie natychmiast skojarzoną z podsiecią region docelowy, ponieważ zarówno grupy NSG, jak i podsieć nie jest już dostępna.
-   **Docelowa grupa NSG wirtualna** może być skojarzony z maszyną wirtualną w tryb failover przy użyciu planów odzyskiwania.

Po utworzenia i skonfigurowania grup NSG, zaleca się uruchamiania [testowanie trybu failover](azure-to-azure-tutorial-dr-drill.md) można zweryfikować inicjowanych przez skrypty skojarzenia NSG i łączności maszyn wirtualnych po pracy w trybie failover.

## <a name="next-steps"></a>Kolejne kroki
-   Dowiedz się więcej o [grup zabezpieczeń sieci](../virtual-network/security-overview.md#network-security-groups).
-   Dowiedz się więcej o NSG [reguły zabezpieczeń](../virtual-network/security-overview.md#security-rules).
-   Dowiedz się więcej o [reguły efektywnym elementem systemu zabezpieczeń](../virtual-network/diagnose-network-traffic-filter-problem.md) dla grupy NSG.
-   Dowiedz się więcej o [planów odzyskiwania](site-recovery-create-recovery-plans.md) Aby zautomatyzować tryb failover aplikacji.
