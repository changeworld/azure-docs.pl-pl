---
title: Zachowaj adresów IP podczas przechodzenie w tryb failover maszyn wirtualnych platformy Azure do innego regionu Azure | Dokumentacja firmy Microsoft
description: Opisuje sposób zachowania adresów IP dla scenariuszy pracy awaryjnej Azure do platformy Azure z usługą Azure Site Recovery
services: site-recovery
documentationcenter: ''
author: mayanknayar
manager: rochakm
editor: ''
ms.assetid: ''
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2018
ms.author: manayar
ms.openlocfilehash: 3e23bab6d67cc4911dd46c226ebc9b87e40e2fa2
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="ip-address-retention-for-azure-virtual-machine-failover"></a>Przechowywania adresów IP pracy w trybie failover maszyny wirtualnej platformy Azure

Usługa Azure Site Recovery umożliwia odzyskiwanie po awarii dla maszyn wirtualnych platformy Azure. Gdy przechodzenie w tryb failover z jednego regionu Azure do innego, klienci często wymagają przechowywania ich konfiguracje adresów IP. Usługi Site Recovery domyślnie naśladuje źródła sieci wirtualnej i struktura podsieci podczas tworzenia tych zasobów na region docelowy. Dla maszyn wirtualnych platformy Azure jest skonfigurowany z prywatnych adresów IP usługi Site Recovery zapewnia starań, próba udostępnianie tego samego prywatnego adresu IP na docelowej maszynie Wirtualnej, jeśli tego adresu IP nie jest już zablokowany przez zasobów platformy Azure lub zreplikowanej maszyny Wirtualnej.

Proste aplikacje powyżej domyślna konfiguracja jest są wystarczające. Bardziej złożonych aplikacji dla przedsiębiorstw klientów, może być konieczne zainicjowanie obsługi dodatkowych zasobów sieci w celu zapewnienia pracy awaryjnej post łączności z innymi składnikami infrastruktury. W tym artykule opisano wymagania sieciowe związane z niepowodzeniem na maszynach wirtualnych platformy Azure z jednego regionu do innego przy zachowaniu adresów IP maszyny Wirtualnej.

## <a name="azure-to-azure-connectivity"></a>Łączność Azure do platformy Azure

W przypadku pierwszego scenariusza, możemy rozważ **firmy A** mający swoją infrastrukturę aplikacji działających na platformie Azure. Ze względów biznesowych ciągłości i zgodności **firmy A** decyduje o tym użyć usługi Azure Site Recovery do ochrony jego zastosowań.

Biorąc pod uwagę wymagania przechowywania adresu IP (np. dla powiązań aplikacji), firmy A ma tego samego wirtualnego struktury sieci i podsieci na region docelowy. Aby jeszcze bardziej ograniczyć celu czasu odzyskiwania (RTO) **firmy A** korzysta z repliki węzły SQL Always ON, kontrolery domeny, itp. i repliki, te węzły są umieszczane w innej sieci wirtualnej na region docelowy. Użycie miejsca na inny adres dla węzłów repliki **firmy A** nawiązać połączenie sieci VPN typu lokacja lokacja między regionami źródłowych i docelowych, które w przeciwnym razie nie będzie możliwe, jeśli takie same przestrzeni adresów jest używana na obu końcach .

Oto, jak wygląda architektury sieci przed trybu failover:
- Maszyny wirtualne aplikacji znajdują się w usłudze Azure Azja Wschodnia, przy użyciu sieci wirtualnej platformy Azure z 10.1.0.0/16 przestrzeni adresowej. Ta sieć wirtualna o nazwie **sieci wirtualnej źródła**.
- Obciążeń aplikacji są podzielone na trzy podsieci — 10.1.1.0/24, 10.1.2.0/24, 10.1.3.0/24 odpowiednio o nazwie **podsieć 1**, **podsieci 2**, **3 podsieci**.
- Azure Azja południowo-wschodni region docelowy i ma sieć wirtualną odzyskiwania i naśladuje konfiguracji miejsca i podsieć adresu w źródle. Ta sieć wirtualna o nazwie **sieci wirtualnej odzyskiwania**.
- Węzły repliki, takich jak zawsze na kontrolerze domeny, itp. znajdują się w sieci wirtualnej z 10.2.0.0/16 miejsca adresów w podsieci 4 z 10.2.4.0/24 adres. Sieć wirtualna o nazwie **sieci wirtualnej Azure** i jest na Azja południowo-wschodnia Azure.
- **Źródło sieci wirtualnej** i **sieci wirtualnej Azure** są połączone za pośrednictwem połączenie sieci VPN typu lokacja lokacja.
- **Sieć wirtualna odzyskiwania** nie jest połączony z innych sieci wirtualnej.
- **Firmy, A** przypisuje/sprawdza, czy docelowy adres IP dla zreplikowanych elementów. W tym przykładzie docelowy adres IP jest taki sam jak źródłowy adres IP dla każdej maszyny Wirtualnej.

![Łączność Azure do platformy Azure przed trybu failover](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-before-failover2.png)

### <a name="full-region-failover"></a>Pełna region trybu failover

W przypadku regionalnej awarii **firmy A** można odzyskać jego całą wdrożenie szybko i łatwo za pomocą portalu usługi Azure Site Recovery zaawansowane [planów odzyskiwania](site-recovery-create-recovery-plans.md). Już ustawiony docelowy adres IP dla każdej maszyny Wirtualnej przed trybu failover, **firmy A** można organizować tryb failover i zautomatyzować ustanawianie połączenia między siecią wirtualną odzyskiwania i sieci wirtualnej Azure, jak pokazano w poniższych diagramu.

![Trybu failover pełne regionu Azure do platformy Azure łączności](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-full-region-failover2.png)

W zależności od wymagań aplikacji połączeń między dwiema sieciami wirtualnymi, na region docelowy może być nawiązane przed, podczas (jako etap pośredni) lub po pracy awaryjnej. Użyj [planów odzyskiwania](site-recovery-create-recovery-plans.md) do dodania skryptów i zdefiniuj kolejność pracy awaryjnej.

Firmy, A także może wybrać, czy ustanowić połączenie między siecią wirtualną odzyskiwania i sieci wirtualnej platformy Azure przy użyciu sieci wirtualnej komunikacji równorzędnej lub sieci VPN typu lokacja-lokacja. W przypadku komunikacji równorzędnej sieci wirtualnych nie jest używana brama sieci VPN i są z nią związane inne ograniczenia. Ponadto [ceny dotyczące komunikacji równorzędnej sieci wirtualnych](https://azure.microsoft.com/pricing/details/virtual-network) są obliczane inaczej niż [ceny dotyczące usługi VPN Gateway połączenia sieć wirtualna-sieć wirtualna](https://azure.microsoft.com/pricing/details/vpn-gateway). Dla trybu failover, zazwyczaj zalecane jest aby naśladował łączności źródła, w tym typ połączenia, aby zminimalizować nieprzewidywalne zdarzenia powstałe w wyniku zmiany w sieci.

### <a name="isolated-application-failover"></a>Tryb failover izolowanych aplikacji

W niektórych warunkach użytkownicy mogą potrzebować trybu failover części infrastruktury aplikacji. Przykładem takiego pracę awaryjną określonych aplikacji lub warstwy, który jest przechowywany w dedykowanym podsieci. Podczas pracy awaryjnej podsieci z przechowywaniem IP jest możliwe, nie jest zalecane w większości sytuacji, ponieważ znacznie zwiększa niespójności łączności. Również utraci łączność podsieci do innych podsieci w tej samej sieci wirtualnej platformy Azure.

Lepszy sposób, aby uwzględnić wymagania dotyczące aplikacji na poziomie podsieci trybu failover ma użycia adresów IP inny element docelowy dla trybu failover (jeśli jest to połączenie jest wymagany do innych podsieci w sieci wirtualnej źródłowego) lub izolowanie każdej aplikacji własne dedykowane wirtualnego sieć w źródle. Drugie podejście możesz ustanowić łączność sieciową między komputerami w źródle i takie same emulować przy awarii region docelowy.

Do projektowania poszczególnych aplikacji zapewnia odporność na awarie, zaleca się do przechowywania aplikacji w własne dedykowane sieci wirtualnej i nawiązywać połączenie między te sieci wirtualne zgodnie z wymaganiami. Umożliwia to aplikacji izolowanych trybu failover przy zachowaniu oryginalnej prywatnych adresów IP.

Następnie konfiguracji trybu failover sprzed wygląda następująco:
- Maszyny wirtualne aplikacji znajdują się w Azure Azja Wschodnia, przy użyciu sieci wirtualnej platformy Azure z 10.1.0.0/16 przestrzeni adresowej dla pierwszej aplikacji i 10.2.0.0/16 drugi aplikacji. Sieci wirtualne są nazywane **VNet1 źródła** i **VNet2 źródła** na podstawie pierwszego i drugiego odpowiednio.
- Każda sieć wirtualną dalsze jest podzielony na dwie podsieci.
- Azure Azja południowo-wschodni region docelowy i ma sieci wirtualnych odzyskiwania VNet1 odzyskiwania i VNet2 odzyskiwania.
- Węzły repliki, takich jak zasoby potrzebne do zawsze włączone, kontroler domeny, itp. są umieszczane w sieci wirtualnej z 10.3.0.0/16 przestrzeni adresowej wewnątrz **4 podsieci** z 10.3.4.0/24 adres. Sieć wirtualna nosi nazwę sieci wirtualnej platformy Azure i jest na platformie Azure Azja południowo-wschodnia.
- **Źródło VNet1** i **sieci wirtualnej Azure** są połączone za pośrednictwem połączenie sieci VPN typu lokacja lokacja. Podobnie **VNet2 źródła** i **sieci wirtualnej Azure** również są połączone za pośrednictwem połączenie sieci VPN typu lokacja lokacja.
- **Źródło VNet1** i **VNet2 źródła** również są połączone za pośrednictwem połączenia VPN S2S w tym przykładzie. Ponieważ dwie sieci wirtualne są w tym samym regionie, równorzędna sieci wirtualnej można również zamiast S2S sieci VPN.
- **Odzyskiwanie VNet1** i **VNet2 odzyskiwania** nie są połączone z innych sieci wirtualnej.
- Aby zmniejszyć celu czasu odzyskiwania (RTO), bramy sieci VPN są skonfigurowane na **VNet1 odzyskiwania** i **VNet2 odzyskiwania** przed trybu failover.

![Łączność Azure do platformy Azure izolowane aplikację przed trybu failover](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-isolated-application-before-failover2.png)

W przypadku sytuacji po awarii, która dotyczy tylko jednej aplikacji (w tym przykładzie znajdujących się w VNet2 źródłowego) A firmy można odzyskać programu w następujący sposób:
- Połączenia sieci VPN między **VNet1 źródła** i **VNet2 źródła**, a między **VNet2 źródła** i **sieci wirtualnej Azure** jest odłączony.
- Ustanowiono połączenia sieci VPN między **VNet1 źródła** i **VNet2 odzyskiwania**, a między **VNet2 odzyskiwania** i **sieci wirtualnej Azure**.
- Maszyny wirtualne z **VNet2 źródła** są awarii **VNet2 odzyskiwania**.

![Łączność Azure do platformy Azure izolowany aplikacji po trybu failover](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-isolated-application-after-failover2.png)

Powyżej izolowanego trybu failover przykładzie można rozszerzyć, aby dołączyć więcej aplikacji i połączenia sieciowe. To zalecanie służy do wykonania model połączenia podobnych przypominającej, o ile to możliwe, w przypadku przechodzenie w tryb failover z źródłowego do docelowego.

### <a name="further-considerations"></a>Dodatkowe uwagi

Bramy sieci VPN korzystają z publicznych adresów IP i przeskoków bramy w celu nawiązania połączenia. Jeśli nie chcesz używać publicznego adresu IP i/lub aby uniknąć dodatkowych przeskoków, możesz użyć Azure [sieci wirtualnej komunikacji równorzędnej](../virtual-network/virtual-network-peering-overview.md) -to-peer sieci wirtualnych w [obsługiwane regiony platformy Azure](../virtual-network/virtual-network-manage-peering.md#cross-region).

## <a name="on-premises-to-azure-connectivity"></a>Łączność w lokalnym do Azure

Drugi scenariusz, możemy należy wziąć pod uwagę **firmy B** mający część swoją infrastrukturę aplikacji działających na Azure i pozostałej uruchamiane lokalnie. Ze względów biznesowych ciągłości i zgodności **firmy B** decyduje o tym użyć usługi Azure Site Recovery do ochrony jego aplikacje działające na platformie Azure.

Oto, jak wygląda architektury sieci przed trybu failover:
- Maszyny wirtualne aplikacji znajdują się w usłudze Azure Azja Wschodnia, przy użyciu sieci wirtualnej platformy Azure z 10.1.0.0/16 przestrzeni adresowej. Ta sieć wirtualna o nazwie **sieci wirtualnej źródła**.
- Obciążeń aplikacji są podzielone na trzy podsieci — 10.1.1.0/24, 10.1.2.0/24, 10.1.3.0/24 odpowiednio o nazwie **podsieć 1**, **podsieci 2**, **3 podsieci**.
- Azure Azja południowo-wschodni region docelowy i ma sieć wirtualną odzyskiwania i naśladuje konfiguracji miejsca i podsieć adresu w źródle. Ta sieć wirtualna o nazwie **sieci wirtualnej odzyskiwania**.
- Maszyny wirtualne w Azja Wschodnia Azure są połączone z lokalnego centrum danych za pośrednictwem programu ExpressRoute lub sieci VPN typu lokacja-lokacja.
- Aby zmniejszyć celu czasu odzyskiwania (RTO), firmy B inicjuje bram w sieci wirtualnej odzyskiwania w Azja południowo-wschodnia Azure przed trybu failover.
- **Firmy B** przypisuje/sprawdza, czy docelowy adres IP dla zreplikowanych elementów. W tym przykładzie docelowy adres IP jest taki sam jak źródłowy adres IP dla każdej maszyny Wirtualnej

![Łączność w lokalnym do Azure przed trybu failover](./media/site-recovery-retain-ip-azure-vm-failover/on-premises-to-azure-connectivity-before-failover2.png)

### <a name="full-region-failover"></a>Pełna region trybu failover

W przypadku regionalnej awarii **firmy B** można odzyskać jego całą wdrożenie szybko i łatwo za pomocą portalu usługi Azure Site Recovery zaawansowane [planów odzyskiwania](site-recovery-create-recovery-plans.md). Już ustawiony docelowy adres IP dla każdej maszyny Wirtualnej przed trybu failover, **firmy B** można organizować tryb failover i zautomatyzować ustanawianie połączenia między siecią wirtualną odzyskiwania i lokalnego centrum danych, jak pokazano poniżej diagramu.

Oryginalne połączenie między Azja Wschodnia Azure i lokalnego centrum danych musi być odłączony przed nawiązaniem połączenia między Azja południowo-wschodnia Azure i lokalnego centrum danych. Lokalną routingu jest również tak skonfigurować, aby wskazać region docelowy i bram post trybu failover.

![Łączność w lokalnym do Azure po trybu failover](./media/site-recovery-retain-ip-azure-vm-failover/on-premises-to-azure-connectivity-after-failover2.png)

### <a name="subnet-failover"></a>Tryb failover podsieci

W odróżnieniu od scenariusza Azure do platformy Azure opisano dla **firmy A**, tryb failover na poziomie podsieci nie jest możliwe w takim przypadku dla **firmy B**. Jest to spowodowane przestrzeni adresowej sieci wirtualnej źródła i odzyskiwania jest taka sama i oryginalne źródło połączenia lokalnego jest aktywny.

Aby osiągnąć odporność aplikacji, zaleca się, że każdej aplikacji są przechowywane w własne dedykowane sieci wirtualnej platformy Azure. Aplikacje można następnie można przełączyć w izolacji i wymagane lokalnej do połączeń ze źródłem może być kierowane do region docelowy zgodnie z powyższym opisem.

## <a name="next-steps"></a>Kolejne kroki
- Dowiedz się więcej o [planów odzyskiwania](site-recovery-create-recovery-plans.md).
