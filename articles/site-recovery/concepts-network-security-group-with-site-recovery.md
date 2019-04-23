---
title: Sieciowe grupy zabezpieczeń usługi Azure Site Recovery | Dokumentacja firmy Microsoft
description: Opisuje, jak za pomocą sieciowych grup zabezpieczeń usługi Azure Site Recovery na potrzeby odzyskiwania po awarii i migracji
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: mayg
ms.openlocfilehash: 0c06283080a4ee51f863714e4c515672299b420d
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59799290"
---
# <a name="network-security-groups-with-azure-site-recovery"></a>Sieciowe grupy zabezpieczeń usługi Azure Site Recovery

Sieciowe grupy zabezpieczeń służą do ograniczania ruchu sieciowego do zasobów w sieci wirtualnej. A [grupy zabezpieczeń sieci (NSG)](../virtual-network/security-overview.md#network-security-groups) zawiera listę reguł zabezpieczeń, które blokują lub zezwalają na ruch sieciowy przychodzący lub wychodzący, w oparciu o źródłowy lub docelowy adres IP, portu i protokołu.

W modelu wdrażania usługi Resource Manager sieciowe grupy zabezpieczeń można skojarzyć z podsieciami lub poszczególnymi interfejsami sieciowymi. Jeśli sieciowa grupa zabezpieczeń jest skojarzona z podsiecią, te reguły są stosowane do wszystkich zasobów połączonych z tą podsiecią. Ruch można ograniczyć jeszcze bardziej przez również skojarzenie sieciowej grupy zabezpieczeń z poszczególnymi interfejsami sieciowymi w obrębie podsieci, który ma już skojarzonej sieciowej grupy zabezpieczeń.

W tym artykule opisano, jak za pomocą sieciowych grup zabezpieczeń usługi Azure Site Recovery.

## <a name="using-network-security-groups"></a>Za pomocą sieciowych grup zabezpieczeń

Poszczególnych podsieci może mieć zero lub jedną skojarzonej sieciowej grupy zabezpieczeń. Pojedynczego interfejsu sieciowego może także zawierać wartość zero lub jedną skojarzonej sieciowej grupy zabezpieczeń. Tak efektywnie może mieć ograniczeń podwójną ruchu sieciowego dla maszyny wirtualnej przez skojarzenie sieciowej grupy zabezpieczeń najpierw podsieci i następnie inne sieciowej grupy zabezpieczeń do interfejsu sieciowego maszyny Wirtualnej. Stosowania reguły sieciowej grupy zabezpieczeń w tym przypadku zależy od kierunku ruchu i priorytet reguł zastosowano zabezpieczenia.

Należy wziąć pod uwagę prosty przykład z jedną maszyną wirtualną w następujący sposób:
-   Maszyna wirtualna jest umieszczona wewnątrz **podsieci Contoso**.
-   **Podsieci contoso** jest skojarzony z **sieciowa grupa zabezpieczeń podsieci**.
-   Interfejsu sieciowego maszyny Wirtualnej jest również skojarzony **sieciowa grupa zabezpieczeń maszyny Wirtualnej**.

![Sieciowa grupa zabezpieczeń z usługą Site Recovery](./media/concepts-network-security-group-with-site-recovery/site-recovery-with-network-security-group.png)

W tym przykładzie dla ruchu przychodzącego sieciowej grupy zabezpieczeń z podsiecią jest stosowana jako pierwsza. Cały ruch dozwolony przez sieciową grupę zabezpieczeń z podsiecią jest następnie oceniany przez sieciową grupę zabezpieczeń z maszyną Wirtualną. Odwrotna ma zastosowanie dla ruchu wychodzącego z sieciowymi grupami zabezpieczeń maszyny Wirtualnej jest stosowana jako pierwsza. Cały ruch dozwolony przez sieciową grupę zabezpieczeń maszyny Wirtualnej jest następnie oceniany przez sieciową grupę zabezpieczeń w podsieci.

Umożliwia to szczegółowe reguły aplikacji. Na przykład można zezwolić na dostęp do Internetu dla ruchu przychodzącego do kilku maszyn wirtualnych aplikacji (na przykład frontonu maszyn wirtualnych) w ramach podsieci, ale ograniczyć dostęp do Internetu dla ruchu przychodzącego do innych maszyn wirtualnych (takich jak bazy danych i innych maszyn wirtualnych zaplecza). W takim przypadku można mieć regułę bardziej łagodne na podsieci sieciową grupę zabezpieczeń, dzięki czemu ruch internetowy i ograniczyć dostęp dla konkretnych maszyn wirtualnych przy odmowie dostępu w sieciowej grupie zabezpieczeń maszyny Wirtualnej. Taki sam, mogą być stosowane dla ruchu wychodzącego.

Podczas konfigurowania tych konfiguracji sieciowej grupy zabezpieczeń, upewnij się, że prawidłowe priorytety są stosowane do [reguły zabezpieczeń](../virtual-network/security-overview.md#security-rules). Reguły są przetwarzane w kolejności priorytetów. Im niższy numer, tym wyższy priorytet, więc te o niższych numerach są przetwarzane przed tymi o wyższych numerach. Kiedy ruch jest zgodny z regułą, przetwarzanie zostaje zatrzymane. W związku z tym żadne istniejące reguły o niższych priorytetach (wyższych numerach), które mają takie same atrybuty jak reguły o wyższych priorytetach, nie będą przetwarzane.

Możesz czasami nie wiedzieć, że grupy zabezpieczeń sieci są stosowane do interfejsu sieciowego i podsieci. Można zweryfikować reguły agregowane stosowane do interfejsu sieciowego, wyświetlając [obowiązujących reguł zabezpieczeń](../virtual-network/virtual-network-network-interface.md#view-effective-security-rules) dla interfejsu sieciowego. Można również użyć [weryfikowanie przepływu protokołu IP](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md) możliwości są dostępne w [Azure Network Watcher](../network-watcher/network-watcher-monitoring-overview.md) do określenia, czy komunikacja jest dozwolona na lub z interfejsem sieciowym. Narzędzie informuje, czy komunikacja jest dozwolona, oraz która reguła zabezpieczeń sieci zezwala lub nie zezwala na ruch.

## <a name="on-premises-to-azure-replication-with-nsg"></a>Środowiska lokalnego do replikacji platformy Azure z sieciowymi grupami zabezpieczeń

Usługa Azure Site Recovery umożliwia odzyskiwanie po awarii i migracji na platformę Azure dla lokalnych [maszyn wirtualnych funkcji Hyper-V](hyper-v-azure-architecture.md), [maszyn wirtualnych VMware](vmware-azure-architecture.md), i [serwerów fizycznych](physical-azure-architecture.md). Dla całego środowiska lokalnego do platformy Azure scenariuszy dane replikacji są wysyłane do i przechowywane na koncie usługi Azure Storage. Podczas replikacji nie płać opłatami związanymi z maszyny wirtualnej. Po uruchomieniu trybu failover na platformie Azure Site Recovery automatycznie tworzy maszyny wirtualne IaaS platformy Azure.

Po utworzeniu maszyn wirtualnych po przejściu w tryb failover Azure sieciowych grup zabezpieczeń może służyć do ograniczania ruchu sieciowego do sieci wirtualnej i maszynach wirtualnych. Usługa Site Recovery nie tworzy sieciowe grupy zabezpieczeń w ramach operacji trybu failover. Zaleca się utworzenie wymaganych sieciowych grup zabezpieczeń platformy Azure, przed rozpoczęciem pracy awaryjnej. Następnie można skojarzyć sieciowych grup zabezpieczeń do przełączone w tryb failover maszyny wirtualne automatycznie podczas pracy w trybie failover przy użyciu zaawansowanych skryptów automatyzacji za pomocą usługi Site Recovery [planów odzyskiwania](site-recovery-create-recovery-plans.md).

Na przykład, jeśli konfiguracja maszyny Wirtualnej po przejściu do trybu failover jest podobny do [przykładowy scenariusz](concepts-network-security-group-with-site-recovery.md#using-network-security-groups) powyższym opisem:
-   Możesz utworzyć **sieci wirtualnej firmy Contoso** i **podsieci Contoso** w ramach planowania dla elementu docelowego regionu świadczenia usługi Azure odzyskiwania po awarii.
-   Możesz również utworzyć i skonfigurować zarówno **sieciowa grupa zabezpieczeń podsieci** także **sieciowa grupa zabezpieczeń maszyny Wirtualnej** jako część tego samego odzyskiwania po awarii planowania.
-   **Sieciowa grupa zabezpieczeń podsieci** może następnie zostać natychmiast skojarzony **podsieci Contoso**, podsieci i sieciowej grupy zabezpieczeń są już dostępne.
-   **Sieciowa grupa zabezpieczeń maszyny Wirtualnej** może być skojarzony z maszyn wirtualnych podczas trybu failover przy użyciu planów odzyskiwania.

Po utworzeniu i skonfigurowaniu sieciowych grup zabezpieczeń zaleca się uruchamianie [testowanie trybu failover](site-recovery-test-failover-to-azure.md) Aby zweryfikować inicjowanych przez skrypty skojarzenia sieciowej grupy zabezpieczeń i łączność maszyn wirtualnych po przejściu do trybu failover.

## <a name="azure-to-azure-replication-with-nsg"></a>Replikacji Azure – Azure z sieciowymi grupami zabezpieczeń

Usługa Azure Site Recovery umożliwia odzyskiwanie po awarii [maszyn wirtualnych platformy Azure](azure-to-azure-architecture.md). Po włączeniu replikacji dla maszyn wirtualnych platformy Azure, Usługa Site Recovery można utworzyć repliki sieci wirtualnych (w tym podsieci i podsieć bramy) w regionie docelowym i Utwórz wymagane mapowania między źródłem i docelowych, sieciami wirtualnymi. Można również wstępnie utworzyć sieci po stronie docelowej i podsieci i używać tego samego podczas włączania replikacji. Usługa Site Recovery nie tworzy żadnych maszyn wirtualnych na przed region platformy Azure docelowego [trybu failover](azure-to-azure-tutorial-failover-failback.md).

Replikacja maszyny Wirtualnej platformy Azure, upewnij się, że reguły sieciowej grupy zabezpieczeń na region platformy Azure "source" Zezwalaj na [łączności wychodzącej](azure-to-azure-about-networking.md#outbound-connectivity-for-ip-address-ranges) dla ruchu związanego z replikacją. Można też testować i weryfikować tych reguł wymagany przez to [przykładowej konfiguracji sieciowej grupy zabezpieczeń](azure-to-azure-about-networking.md#example-nsg-configuration).

Usługa Site Recovery nie tworzy ani nie Replikuj sieciowych grup zabezpieczeń w ramach operacji trybu failover. Zaleca się utworzenie wymaganych grup zabezpieczeń sieci w miejscu docelowym regionem świadczenia usługi Azure, przed rozpoczęciem pracy awaryjnej. Następnie można skojarzyć sieciowych grup zabezpieczeń do przełączone w tryb failover maszyny wirtualne automatycznie podczas pracy w trybie failover przy użyciu zaawansowanych skryptów automatyzacji za pomocą usługi Site Recovery [planów odzyskiwania](site-recovery-create-recovery-plans.md).

Biorąc pod uwagę [przykładowy scenariusz](concepts-network-security-group-with-site-recovery.md#using-network-security-groups) opisanej wcześniej:
-   Usługa Site Recovery można utworzyć repliki **sieci wirtualnej firmy Contoso** i **podsieci Contoso** dla elementu docelowego regionu platformy Azure po włączeniu replikacji dla maszyny Wirtualnej.
-   Można utworzyć żądanego replik **sieciowa grupa zabezpieczeń podsieci** i **sieciowa grupa zabezpieczeń maszyny Wirtualnej** (o nazwie, na przykład **sieciowej grupy zabezpieczeń w podsieci docelowej** i **sieciowej grupy zabezpieczeń docelowej maszyny Wirtualnej**, odpowiednio) w elemencie docelowym regionie platformy Azure, pozwalając na wszelkie dodatkowe reguły wymagane w regionie docelowym.
-   **Sieciowa grupa zabezpieczeń podsieci docelowej** może następnie być natychmiast skojarzoną z podsiecią regionu docelowego, podsieci i sieciowej grupy zabezpieczeń są już dostępne.
-   **Docelowa maszyna wirtualna sieciowej grupy zabezpieczeń** może być skojarzony z maszyn wirtualnych podczas trybu failover przy użyciu planów odzyskiwania.

Po utworzeniu i skonfigurowaniu sieciowych grup zabezpieczeń zaleca się uruchamianie [testowanie trybu failover](azure-to-azure-tutorial-dr-drill.md) Aby zweryfikować inicjowanych przez skrypty skojarzenia sieciowej grupy zabezpieczeń i łączność maszyn wirtualnych po przejściu do trybu failover.

## <a name="next-steps"></a>Kolejne kroki
-   Dowiedz się więcej o [sieciowych grup zabezpieczeń](../virtual-network/security-overview.md#network-security-groups).
-   Dowiedz się więcej na temat sieciowych grup zabezpieczeń [reguły zabezpieczeń](../virtual-network/security-overview.md#security-rules).
-   Dowiedz się więcej o [obowiązujących reguł zabezpieczeń](../virtual-network/diagnose-network-traffic-filter-problem.md) dla sieciowej grupy zabezpieczeń.
-   Dowiedz się więcej o [planów odzyskiwania](site-recovery-create-recovery-plans.md) do automatyzowania trybu failover w aplikacji.
