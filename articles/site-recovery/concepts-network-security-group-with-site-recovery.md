---
title: Sieciowe grupy zabezpieczeń z funkcją Azure Site Recovery | Dokumenty firmy Microsoft
description: W tym artykule opisano, jak używać sieciowych grup zabezpieczeń z funkcją Azure Site Recovery do odzyskiwania po awarii i migracji
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: mayg
ms.openlocfilehash: eb5ba99133f5726c44164b0ba45b7ab5d94e292f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80292358"
---
# <a name="network-security-groups-with-azure-site-recovery"></a>Sieciowe grupy zabezpieczeń z funkcją Azure Site Recovery

Sieciowe grupy zabezpieczeń służą do ograniczania ruchu sieciowego do zasobów w sieci wirtualnej. [Sieciowa grupa zabezpieczeń (NSG)](../virtual-network/security-overview.md#network-security-groups) zawiera listę reguł zabezpieczeń, które zezwalają lub odmawiają przychodzącego lub wychodzącego ruchu sieciowego na podstawie źródłowego lub docelowego adresu IP, portu i protokołu.

W modelu wdrażania Menedżera zasobów sieciowe sieciowe mogą być skojarzone z podsieciami lub poszczególnymi interfejsami sieciowymi. Jeśli sieciowa grupa zabezpieczeń jest skojarzona z podsiecią, te reguły są stosowane do wszystkich zasobów połączonych z tą podsiecią. Ruch może być dodatkowo ograniczony przez skojarzenie sieciowej grupy sieciowej z poszczególnymi interfejsami sieciowymi w podsieci, która ma już skojarzoną sieć sieciową.

W tym artykule opisano, jak używać sieciowych grup zabezpieczeń z usługą Azure Site Recovery.

## <a name="using-network-security-groups"></a>Korzystanie z grup zabezpieczeń sieci

Pojedyncza podsieć może mieć zero lub jedną skojarzoną siecię szkopułców sieciowych. Pojedynczy interfejs sieciowy może również mieć zero lub jeden skojarzony nsg. Dzięki tej rzeczywistości można skutecznie mieć podwójne ograniczenie ruchu dla maszyny wirtualnej, kojarząc nsg najpierw do podsieci, a następnie innego nsg do interfejsu sieciowego maszyny wirtualnej. Stosowanie reguł sieciowej grupy zabezpieczeń w tym przypadku zależy od kierunku ruchu i priorytetu stosowanych reguł bezpieczeństwa.

Rozważmy prosty przykład z jednej maszyny wirtualnej w następujący sposób:
-    Maszyna wirtualna jest umieszczana wewnątrz podsieci **Contoso**.
-    **Podsieć Contoso** jest skojarzona z **siecią sieciowej podsieci**.
-    Interfejs sieciowy maszyny Wirtualnej jest dodatkowo skojarzony z **siecią NSG maszyny Wirtualnej**.

![NSG z odzyskiwaniem witryny](./media/concepts-network-security-group-with-site-recovery/site-recovery-with-network-security-group.png)

W tym przykładzie dla ruchu przychodzącego podsieci sieciowej jest oceniane jako pierwszy. Każdy ruch dozwolony za pośrednictwem sieciowej sieciowej podsieci jest następnie oceniany przez sieci nsg maszyny wirtualnej. Odwrotna ma zastosowanie do ruchu wychodzącego, z VM NSG są oceniane jako pierwszy. Każdy ruch dozwolony za pośrednictwem sieciowej sieciowej maszyny Wirtualnej jest następnie oceniany przez siecią sieciowej podsieci.

Pozwala to na szczegółowe aplikacji reguły zabezpieczeń. Na przykład można zezwolić przychodzącemu dostępowi do Internetu na kilka maszyn wirtualnych aplikacji (takich jak maszyny wirtualne frontendu) w podsieci, ale ograniczyć przychodzący dostęp do Internetu do innych maszyn wirtualnych (takich jak bazy danych i innych maszyn wirtualnych wewnętrznej bazy danych). W takim przypadku można mieć bardziej łagodną regułę w podsieci sieciowej sieciowej, zezwalając na ruch internetowy i ograniczyć dostęp do określonych maszyn wirtualnych, odmawiając dostępu na maszynie wirtualnej sieciowej. To samo można zastosować do ruchu wychodzącego.

Podczas konfigurowania takich konfiguracji sieciowej grupy zabezpieczeń należy upewnić się, że do [reguł bezpieczeństwa](../virtual-network/security-overview.md#security-rules)stosowane są odpowiednie priorytety. Reguły są przetwarzane w kolejności priorytetów. Im niższy numer, tym wyższy priorytet, więc te o niższych numerach są przetwarzane przed tymi o wyższych numerach. Kiedy ruch jest zgodny z regułą, przetwarzanie zostaje zatrzymane. W związku z tym żadne istniejące reguły o niższych priorytetach (wyższych numerach), które mają takie same atrybuty jak reguły o wyższych priorytetach, nie będą przetwarzane.

Możesz czasami nie wiedzieć, że grupy zabezpieczeń sieci są stosowane do interfejsu sieciowego i podsieci. Reguły zagregowane zastosowane do interfejsu sieciowego można zweryfikować, wyświetlając [skuteczne reguły zabezpieczeń](../virtual-network/virtual-network-network-interface.md#view-effective-security-rules) dla interfejsu sieciowego. Można również użyć możliwości [weryfikacji przepływu IP](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md) w [usłudze Azure Network Watcher,](../network-watcher/network-watcher-monitoring-overview.md) aby określić, czy komunikacja jest dozwolona do lub z interfejsu sieciowego. Narzędzie informuje, czy komunikacja jest dozwolona, oraz która reguła zabezpieczeń sieci zezwala lub nie zezwala na ruch.

## <a name="on-premises-to-azure-replication-with-nsg"></a>Lokalna replikacja do platformy Azure za pomocą sieciowej grupy sieciowej

Usługa Azure Site Recovery umożliwia odzyskiwanie po awarii i migrację na platformę Azure dla lokalnych [maszyn wirtualnych hyper-V,](hyper-v-azure-architecture.md) [maszyn wirtualnych VMware](vmware-azure-architecture.md)i [serwerów fizycznych.](physical-azure-architecture.md) W przypadku wszystkich scenariuszy lokalnych do platformy Azure dane replikacji są wysyłane do konta usługi Azure Storage i przechowywane na nich. Podczas replikacji nie płacisz żadnych opłat za maszynę wirtualną. Po uruchomieniu pracy awaryjnej na platformie Azure usługa Site Recovery automatycznie tworzy maszyny wirtualne usługi Azure IaaS.

Po utworzeniu maszyn wirtualnych po przetwórz pracy awaryjnej na platformie Azure, sieciowych grup zabezpieczeń można użyć do ograniczenia ruchu sieciowego do sieci wirtualnej i maszyn wirtualnych. Odzysk lokacji nie tworzy grup NSG w ramach operacji pracy awaryjnej. Firma Microsoft zaleca utworzenie wymaganych witryn nsgs platformy Azure przed zainicjowaniem pracy awaryjnej. Następnie można skojarzyć sieciowe sieciowe sieciowych z automatycznymi maszynami wirtualnymi po awarii podczas pracy awaryjnej przy użyciu skryptów automatyzacji z potężnymi [planami odzyskiwania](site-recovery-create-recovery-plans.md)usługi Site Recovery.

Na przykład jeśli konfiguracja maszyny wirtualnej po pracy awaryjnej jest podobna do [przykładowego scenariusza](concepts-network-security-group-with-site-recovery.md#using-network-security-groups) opisanego powyżej:
-    Sieć **wirtualną Contoso** i **podsieć Contoso** można utworzyć w ramach planowania odzyskiwania po awarii w docelowym regionie platformy Azure.
-    Można również utworzyć i skonfigurować zarówno **sieciowe sieciowe podsieci,** jak i **sieć sieciowa maszyny Wirtualnej** jako część tego samego planowania odzyskiwania po awarii.
-    **Sieciowe sieciowe podsieci** można następnie natychmiast skojarzyć z **podsiecią Contoso**, ponieważ zarówno sieciowej sieciowej sieciowej, jak i podsieci są już dostępne.
-    **NSG maszyn wirtualnych** może być skojarzony z maszynami wirtualnymi podczas pracy awaryjnej przy użyciu planów odzyskiwania.

Po utworzeniu i skonfigurowaniu grup zabezpieczeń sieciowych zaleca się uruchomienie [testowego trybu failover](site-recovery-test-failover-to-azure.md) w celu zweryfikowania skryptowych skojarzeń sieciowych grup zabezpieczeń i łączności maszyny wirtualnej po pracy awaryjnej.

## <a name="azure-to-azure-replication-with-nsg"></a>Replikacja platformy Azure na platformę Azure z nsg

Usługa Azure Site Recovery umożliwia odzyskiwanie po awarii [maszyn wirtualnych platformy Azure.](azure-to-azure-architecture.md) Podczas włączania replikacji dla maszyn wirtualnych platformy Azure usługa Site Recovery może tworzyć sieci wirtualne replik (w tym podsieci i podsieci bramy) w regionie docelowym i tworzyć wymagane mapowania między źródłowymi i docelowymi sieciami wirtualnymi. Można również wstępnie utworzyć docelowe sieci boczne i podsieci i używać tego samego podczas włączania replikacji. Usługa Site Recovery nie tworzy żadnych maszyn wirtualnych w docelowym regionie platformy Azure przed [przebładaniem w stan failover](azure-to-azure-tutorial-failover-failback.md).

W przypadku replikacji maszyny wirtualnej platformy Azure upewnij się, że reguły sieciowej grupy danych sieciowych w źródłowym regionie platformy Azure zezwalają na [łączność wychodzącą](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags) dla ruchu replikacji. Można również przetestować i zweryfikować te wymagane reguły za pomocą tej [przykładowej konfiguracji sieciowej grupy zabezpieczeń](azure-to-azure-about-networking.md#example-nsg-configuration).

Usługa Site Recovery nie tworzy ani nie replikuje grup NSG w ramach operacji pracy awaryjnej. Firma Microsoft zaleca utworzenie wymaganych grup zabezpieczeń w docelowym regionie platformy Azure przed rozpoczęciem pracy awaryjnej. Następnie można skojarzyć sieciowe sieciowe sieciowych z automatycznymi maszynami wirtualnymi po awarii podczas pracy awaryjnej przy użyciu skryptów automatyzacji z potężnymi [planami odzyskiwania](site-recovery-create-recovery-plans.md)usługi Site Recovery.

Biorąc pod uwagę [przykładowy scenariusz](concepts-network-security-group-with-site-recovery.md#using-network-security-groups) opisany wcześniej:
-    Usługa Site Recovery może tworzyć repliki sieci **wirtualnej Contoso** i **podsieci Contoso** w docelowym regionie platformy Azure, gdy replikacja jest włączona dla maszyny Wirtualnej.
-    Można utworzyć żądane repliki **sieciowej sieciowej podsieci** i sieciowej grupy **wirtualnej maszyny wirtualnej** (o nazwie, na przykład **sieciowej sieciowej podsieci docelowej** i **docelowej sieci owej grupy wirtualnej,** odpowiednio) w docelowym regionie platformy Azure, umożliwiając wszelkie dodatkowe reguły wymagane w regionie docelowym.
-    **Grupa sieciowa podsieci docelowej** może być natychmiast skojarzona z podsiecią regionu docelowego, ponieważ zarówno siecinasku sieciowego, jak i podsieci są już dostępne.
-    **Docelowa grupa NSG maszyny wirtualnej** może być skojarzona z maszynami wirtualnymi podczas pracy awaryjnej przy użyciu planów odzyskiwania.

Po utworzeniu i skonfigurowaniu grup zabezpieczeń sieciowych zaleca się uruchomienie [testowego trybu failover](azure-to-azure-tutorial-dr-drill.md) w celu zweryfikowania skryptowych skojarzeń sieciowych grup zabezpieczeń i łączności maszyny wirtualnej po pracy awaryjnej.

## <a name="next-steps"></a>Następne kroki
-    Dowiedz się więcej o [sieciowych grupach zabezpieczeń](../virtual-network/security-overview.md#network-security-groups).
-    Dowiedz się więcej o [regułach zabezpieczeń](../virtual-network/security-overview.md#security-rules)sieciowych sieciowych .
-    Dowiedz się więcej o [skutecznych regułach zabezpieczeń](../virtual-network/diagnose-network-traffic-filter-problem.md) sieciowych sieci.
-    Dowiedz się więcej o [planach odzyskiwania](site-recovery-create-recovery-plans.md) w celu zautomatyzowania pracy awaryjnej aplikacji.
