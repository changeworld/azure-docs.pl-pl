---
title: Zachowaj adresów IP dla trybu failover maszyny Wirtualnej platformy Azure | Dokumentacja firmy Microsoft
description: Opisuje sposób zachować adresy IP, gdy przejść w tryb failover maszyn wirtualnych platformy Azure do odzyskiwania po awarii do regionu pomocniczego przy użyciu usługi Azure Site Recovery
ms.service: site-recovery
ms.date: 10/16/2018
author: mayurigupta13
ms.topic: conceptual
ms.author: mayg
ms.openlocfilehash: 86adaa21a069c168b512231ba231940bfa2ef9e8
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/29/2018
ms.locfileid: "50213036"
---
# <a name="ip-address-retention-for-azure-vm-failover"></a>Przechowywanie adresów IP dla trybu failover maszyny Wirtualnej platformy Azure

Usługa Azure Site Recovery umożliwia odzyskiwanie po awarii dla maszyn wirtualnych platformy Azure. Podczas przechodzenia w tryb failover między regionami platformy Azure do innego, klienci często wymagają przechowywania ich konfiguracje adresów IP. Site Recovery, domyślnie naśladuje źródłowa sieć wirtualna i struktura podsieci podczas tworzenia tych zasobów w regionie docelowym. Skonfigurowano statyczne prywatne adresy IP maszyn wirtualnych platformy Azure Usługa Site Recovery sprawia, że optymalnego próby aprowizacji tego samego prywatnego adresu IP na docelowej maszynie Wirtualnej, jeśli ten adres IP nie jest już zablokowany przez zasób platformy Azure lub zreplikowanej maszyny Wirtualnej.

W przypadku prostych aplikacji powyżej domyślna konfiguracja polega na wszystko, co jest potrzebne. Bardziej złożonych aplikacji dla przedsiębiorstw klientów, może być konieczne zainicjowanie obsługi dodatkowych zasobów sieciowych w celu zapewnienia łączności po przełączeniu w tryb failover z innymi składnikami infrastruktury. W tym artykule opisano wymagania sieciowe na potrzeby przechodzenia przez maszyny wirtualne platformy Azure z jednego regionu do innego przy zachowaniu adresy IP maszyn wirtualnych.

## <a name="azure-to-azure-connectivity"></a>Łączność platformy Azure do platformy Azure

Dla pierwszego scenariusza, firma Microsoft należy wziąć pod uwagę **firmy A** ma swoją infrastrukturę aplikacji działających na platformie Azure. Ze względów biznesowych ciągłości i zgodności **firmy A** zdecyduje się użyć usługi Azure Site Recovery do ochrony jego aplikacji.

Biorąc pod uwagę wymagania przechowywania adresów IP (np. powiązania aplikacji), firma A ma tego samego wirtualnego strukturę sieci i podsieci w regionie docelowym. Aby jeszcze bardziej ograniczyć cel czasu odzyskiwania (RTO) **firmy A** korzysta z repliki węzły programu SQL Always ON, kontrolery domeny, itp. i repliki, te węzły są umieszczane w innej sieci wirtualnej w regionie docelowym. Korzystanie z różnymi przestrzeniami adresowymi w węzłach repliki umożliwia **firmy A** nawiązać połączenie z siecią VPN lokacja lokacja między regionami źródłowych i docelowych, które w przeciwnym razie nie będzie może wystąpić, jeśli takie same przestrzeń adresowa jest używana na obu końcach .

Oto jak wygląda architektura sieci przed włączeniem trybu failover:
- Maszyn wirtualnych aplikacji znajdują się w usłudze Azure Azja Wschodnia, przy użyciu sieci wirtualnej platformy Azure przy użyciu 10.1.0.0/16 przestrzeni adresowej. Ta sieć wirtualna ma nazwę **źródłowa sieć wirtualna**.
- Obciążenia aplikacji są dzielone między trzema podsieciami — 10.1.1.0/24, 10.1.2.0/24 10.1.3.0/24, odpowiednio o nazwie **podsieci 1**, **podsieci 2**, **podsieci 3**.
- Azja Południowa platformy Azure jest region docelowy i sieć wirtualną odzyskiwania, który naśladuje konfiguracji miejsca i podsieć adresu w źródle. Ta sieć wirtualna ma nazwę **sieć odzyskiwania**.
- Węzłach repliki, takich jak zawsze włączone, kontroler domeny, itd. znajdują się w sieci wirtualnej z 10.2.0.0/16 przestrzeni adresów wewnątrz 4 podsieci z 10.2.4.0/24 adres. Nosi nazwę sieci wirtualnej **sieci wirtualnej platformy Azure** i na platformie Azure Azja południowo-wschodnia.
- **Źródła sieci wirtualnej** i **sieci wirtualnej platformy Azure** są połączone za pośrednictwem połączenia lokacja lokacja sieci VPN.
- **Sieć wirtualna odzyskiwania** nie jest połączony z innej sieci wirtualnej.
- **Firmy A** przypisuje/sprawdza docelowy adres IP dla zreplikowanych elementów. Na przykład docelowy adres IP jest taki sam jak źródłowy adres IP dla każdej maszyny Wirtualnej.

![Łączność platformy Azure do platformy Azure, przed włączeniem trybu failover](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-before-failover2.png)

### <a name="full-region-failover"></a>Pełne regionu pracy awaryjnej

W przypadku regionalnej awarii **firmy A** może odzyskać swoich całe wdrożenie szybko i łatwo przy użyciu usługi Azure Site Recovery zaawansowanych [planów odzyskiwania](site-recovery-create-recovery-plans.md). Już ustawiony docelowy adres IP dla każdej maszyny Wirtualnej przed przejścia w tryb failover **firmy A** można organizować tryb failover i zautomatyzować ustanawiania połączenia między siecią wirtualną odzyskiwania i siecią wirtualną platformy Azure, jak pokazano na poniższym diagramie.

![Tryb failover region pełną łączność platformy Azure do platformy Azure](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-full-region-failover2.png)

W zależności od wymagań aplikacji, połączeń między dwiema sieciami wirtualnymi w regionie docelowym może być ustalonych wcześniej, podczas (jako pośredniego kroku) lub po włączeniu trybu failover. Użyj [planów odzyskiwania](site-recovery-create-recovery-plans.md) dodawać skrypty i zdefiniuj kolejność pracy awaryjnej.

Firmy A ma także wybór ustanowić połączenie między siecią wirtualną odzyskiwania i siecią wirtualną platformy Azure za pomocą komunikacji równorzędnej sieci wirtualnej lub sieci VPN typu lokacja-lokacja. W przypadku komunikacji równorzędnej sieci wirtualnych nie jest używana brama sieci VPN i są z nią związane inne ograniczenia. Ponadto [ceny dotyczące komunikacji równorzędnej sieci wirtualnych](https://azure.microsoft.com/pricing/details/virtual-network) są obliczane inaczej niż [ceny dotyczące usługi VPN Gateway połączenia sieć wirtualna-sieć wirtualna](https://azure.microsoft.com/pricing/details/vpn-gateway). Dla pracy w trybie Failover jest na ogół do naśladowania źródła łączności, włącznie z typu połączenia, aby zminimalizować nieprzewidziane zdarzenia powstałe w wyniku zmiany w sieci.

### <a name="isolated-application-failover"></a>Aplikacja izolowana trybu failover

W pewnych okolicznościach użytkowników może być konieczne do trybu failover części infrastruktury aplikacji. Przykładem takiego pracę awaryjną określonej aplikacji lub warstwę, która mieści się w dedykowanej podsieci. Podczas pracy awaryjnej podsieci IP przechowywanie ich przez jest możliwe, nie jest zalecane w większości sytuacji, ponieważ znacznie zwiększa niespójności łączności. Również utraci łączność podsieci do innych podsieci w ramach tej samej sieci wirtualnej platformy Azure.

Lepszy sposób, aby uwzględnić wymagania trybu failover aplikacji na poziomie podsieci jest użycia adresów IP w inne miejsce docelowe dla trybu failover (jeśli jest wymagana łączność do innych podsieci w sieci wirtualnej źródła) lub izolowanie każdej aplikacji swój własny dedykowany wirtualny sieć w źródle. Drugie podejście możesz ustanowić połączenia między sieciami w źródle i emulować takie same, podczas przechodzenia w tryb failover do regionu docelowego.

Architektury poszczególnych aplikacji, aby zapewnić odporność, zalecane jest do przechowywania aplikacji w swoje własne dedykowane sieci wirtualnej i poprawnie ustanowić połączenia między tymi sieciami wirtualnymi, zgodnie z potrzebami. Dzięki temu aplikacja izolowana trybu failover przy zachowaniu oryginalnej prywatnych adresów IP.

Następnie za pomocą konfiguracji trybu failover pre wygląda następująco:
- Maszyn wirtualnych aplikacji znajdują się w usłudze Azure Azja Wschodnia, przy użyciu sieci wirtualnej platformy Azure przy użyciu adresów 10.1.0.0/16 miejsca dla pierwszej aplikacji i 10.2.0.0/16 dla drugiej aplikacji. Sieci wirtualne są nazywane **VNet1 źródła** i **VNet2 źródła** aplikacji pierwszego i drugiego, odpowiednio.
- Każda sieć wirtualna dalsze jest podzielona na dwie podsieci.
- Azja Południowa platformy Azure jest region docelowy i ma sieci wirtualne odzyskiwania VNet1 odzyskiwania oraz VNet2 odzyskiwania.
- Węzłach repliki, takich jak dla zawsze włączone, kontroler domeny, itp., są umieszczane w sieci wirtualnej z 10.3.0.0/16 przestrzeni adresowej wewnątrz **4 podsieci** z 10.3.4.0/24 adresu. Sieć wirtualna nosi nazwę sieci wirtualnej platformy Azure i jest na platformie Azure Azja południowo-wschodnia.
- **Źródło VNet1** i **sieci wirtualnej platformy Azure** są połączone za pośrednictwem połączenia lokacja lokacja sieci VPN. Podobnie **VNet2 źródła** i **sieci wirtualnej platformy Azure** również są połączone za pośrednictwem połączenia lokacja lokacja sieci VPN.
- **Źródło VNet1** i **VNet2 źródła** są również połączone za pośrednictwem sieci VPN S2S w tym przykładzie. Ponieważ dwie sieci wirtualne znajdują się w tym samym regionie, komunikacja równorzędna sieci wirtualnych można również zamiast połączenia VPN S2S.
- **Odzyskiwanie VNet1** i **VNet2 odzyskiwania** nie są połączone z usługą virtual network.
- Aby zmniejszyć cel czasu odzyskiwania (RTO), bramy sieci VPN są skonfigurowane na **VNet1 odzyskiwania** i **VNet2 odzyskiwania** przed trybu failover.

![Łączność platformy Azure do platformy Azure odizolowane aplikacji przed włączeniem trybu failover](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-isolated-application-before-failover2.png)

W przypadku sytuacji po awarii, która wpływa na tylko jedną aplikację (w tym przykładzie znajdujących się w sieci VNet2 źródła) firma A można odzyskać w danej aplikacji w następujący sposób:
- Połączenia sieci VPN między **VNet1 źródła** i **VNet2 źródła**oraz między **VNet2 źródła** i **sieci wirtualnej platformy Azure** są odłączone.
- Połączenia sieci VPN są ustanawiane między **VNet1 źródła** i **VNet2 odzyskiwania**oraz między **VNet2 odzyskiwania** i **sieci wirtualnej platformy Azure**.
- Maszyny wirtualne z **VNet2 źródła** są przełączone w tryb failover **VNet2 odzyskiwania**.

![Łączność platformy Azure do platformy Azure odizolowane aplikacji po włączeniu trybu failover](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-isolated-application-after-failover2.png)

Powyżej izolowanego trybu failover przykładu można rozszerzyć, aby uwzględnić więcej aplikacji i połączenia sieciowe. Zaleca się postępuj zgodnie z modelem podobne jak połączenie, o ile to możliwe, podczas przechodzenia w tryb failover ze źródła do docelowego.

### <a name="further-considerations"></a>Dodatkowe uwagi

Bramy sieci VPN korzystanie z publicznych adresów IP i przeskoków bramy do nawiązywania połączeń. Jeśli nie chcesz używać publiczny adres IP i/lub aby uniknąć dodatkowych przeskoków, możesz użyć usługi Azure [wirtualne sieci równorzędne](../virtual-network/virtual-network-peering-overview.md) nawiązać komunikację równorzędną sieci wirtualnych między [obsługiwane regiony platformy Azure](../virtual-network/virtual-network-manage-peering.md#cross-region).

## <a name="on-premises-to-azure-connectivity"></a>Łączność w lokalnym — z platformą Azure

W drugim scenariuszu, firma Microsoft należy wziąć pod uwagę **firmy B** zawierający część swojej infrastruktury aplikacji, uruchomione na platformie Azure i resztę, uruchamiane lokalnie. Ze względów biznesowych ciągłości i zgodności **firmy B** zdecyduje się użyć usługi Azure Site Recovery do ochrony jego aplikacji działających na platformie Azure.

Oto jak wygląda architektura sieci przed włączeniem trybu failover:
- Maszyn wirtualnych aplikacji znajdują się w usłudze Azure Azja Wschodnia, przy użyciu sieci wirtualnej platformy Azure przy użyciu 10.1.0.0/16 przestrzeni adresowej. Ta sieć wirtualna ma nazwę **źródłowa sieć wirtualna**.
- Obciążenia aplikacji są dzielone między trzema podsieciami — 10.1.1.0/24, 10.1.2.0/24 10.1.3.0/24, odpowiednio o nazwie **podsieci 1**, **podsieci 2**, **podsieci 3**.
- Azja Południowa platformy Azure jest region docelowy i sieć wirtualną odzyskiwania, który naśladuje konfiguracji miejsca i podsieć adresu w źródle. Ta sieć wirtualna ma nazwę **sieć odzyskiwania**.
- Maszyny wirtualne w Azja Wschodnia Azure są połączone z lokalnym centrum danych za pośrednictwem usługi ExpressRoute lub sieci VPN typu lokacja-lokacja.
- Aby zmniejszyć cel czasu odzyskiwania (RTO), B firmy aprowizuje bramy w sieci wirtualnej odzyskiwania w usłudze Azure Azja południowo-wschodnia przed trybu failover.
- **Firma B** przypisuje/sprawdza docelowy adres IP dla zreplikowanych elementów. Na przykład docelowy adres IP jest taki sam jak źródłowy adres IP dla każdej maszyny Wirtualnej

![Łączność w lokalnym — z platformą Azure, przed włączeniem trybu failover](./media/site-recovery-retain-ip-azure-vm-failover/on-premises-to-azure-connectivity-before-failover2.png)

### <a name="full-region-failover"></a>Pełne regionu pracy awaryjnej

W przypadku regionalnej awarii **firmy B** może odzyskać swoich całe wdrożenie szybko i łatwo przy użyciu usługi Azure Site Recovery zaawansowanych [planów odzyskiwania](site-recovery-create-recovery-plans.md). Już ustawiony docelowy adres IP dla każdej maszyny Wirtualnej przed przejścia w tryb failover **firmy B** można organizować tryb failover i zautomatyzować ustanawiania połączenia między siecią wirtualną odzyskiwania i lokalnym centrum danych, jak pokazano na poniższym diagramie.

Oryginalne połączenie między Azja Wschodnia platformy Azure i lokalnym centrum danych musi być odłączony przed nawiązaniem połączenia między Azja Południowa platformy Azure i lokalnym centrum danych. Lokalne routingu jest konfigurowana ponownie również wskaż region docelowy i bram publikowanie trybu failover.

![Łączność w lokalnym — z platformą Azure po włączeniu trybu failover](./media/site-recovery-retain-ip-azure-vm-failover/on-premises-to-azure-connectivity-after-failover2.png)

### <a name="subnet-failover"></a>Tryb failover podsieci

Inaczej niż w przypadku scenariuszy Azure – Azure opisane dla **firmy A**, poziomie podsieci przejścia w tryb failover nie jest możliwy w tym przypadku **firmy B**. Jest to spowodowane przestrzeni adresowej sieci wirtualnej źródła i odzyskiwania jest taka sama i oryginalne źródło, aby połączenie lokalne jest aktywny.

Umożliwiającego osiągnięcie odporności aplikacji, zaleca się, że każda aplikacja są przechowywane w swoje własne dedykowane sieci wirtualnej platformy Azure. Aplikacje można następnie przełączone w tryb failover w izolacji i wymaganego środowiska lokalnego do połączeń ze źródłami mogą być kierowani do regionu docelowego, zgodnie z powyższym opisem.

## <a name="next-steps"></a>Kolejne kroki
- Dowiedz się więcej o [planów odzyskiwania](site-recovery-create-recovery-plans.md).
