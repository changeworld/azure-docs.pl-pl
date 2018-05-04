---
title: Przy użyciu usługi ExpressRoute w usłudze odzyskiwania po awarii maszyny wirtualnej platformy Azure | Dokumentacja firmy Microsoft
description: Informacje dotyczące używania Azure ExpressRoute z usługą Azure Site Recovery dla maszyny wirtualnej platformy Azure, odzyskiwania po awarii
services: site-recovery
documentationcenter: ''
author: mayanknayar
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 04/30/2018
ms.author: manayar
ms.openlocfilehash: 8b4c7bb561d311796f59b94a6e2fbcd9b571d2cd
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/01/2018
---
# <a name="using-expressroute-with-azure-virtual-machine-disaster-recovery"></a>Przy użyciu usługi ExpressRoute w usłudze odzyskiwania po awarii maszyny wirtualnej platformy Azure

Usługa Microsoft Azure ExpressRoute umożliwia rozszerzanie sieci lokalnych na chmurę Microsoft za pośrednictwem połączenia prywatnego obsługiwanego przez dostawcę połączenia. W tym artykule opisano, jak używać usługi ExpressRoute z usługą Site Recovery do odzyskiwania po awarii maszyn wirtualnych systemu Azure.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że rozumiesz:
-   ExpressRoute [obwody](../expressroute/expressroute-circuit-peerings.md)
-   ExpressRoute [domen routingu](../expressroute/expressroute-circuit-peerings.md#expressroute-routing-domains)
-   Maszyna wirtualna platformy Azure [architektura replikacji](azure-to-azure-architecture.md)
-   [Konfigurowanie replikacji](azure-to-azure-tutorial-enable-replication.md) maszyn wirtualnych platformy Azure
-   [Przechodzenie w tryb failover](azure-to-azure-tutorial-failover-failback.md) maszyn wirtualnych platformy Azure

## <a name="expressroute-and-azure-virtual-machine-replication"></a>ExpressRoute, jak i Azure replikację maszyny wirtualnej

Podczas ochrony maszyn wirtualnych platformy Azure z usługą Site Recovery, dane replikacji są wysyłane do konta usługi Azure Storage lub repliki dysku zarządzanego w celu region platformy Azure, w zależności od tego, czy sieci wirtualnej platformy Azure maszyny użyj [dysków zarządzanych Azure](../virtual-machines/windows/managed-disks-overview.md). Mimo że replikacji punktów końcowych, które są publiczne, ruch związany z replikacją dla replikacji maszyny Wirtualnej platformy Azure, domyślnie nie przechodzą przez Internet, niezależnie od tego, który region platformy Azure w sieci wirtualnej źródło istnieje w.

Do odzyskiwania po awarii maszyny Wirtualnej platformy Azure jak dane replikacji nie opuszczają granicy Azure ExpressRoute nie jest wymagane dla replikacji. Po maszyny wirtualne przełączyć się z obiektem docelowym region platformy Azure, użytkownik może uzyskiwać do nich dostęp przy użyciu [prywatnej komunikacji równorzędnej](../expressroute/expressroute-circuit-peerings.md#azure-private-peering).

## <a name="replicating-azure-deployments"></a>Replikowanie wdrożeń platformy Azure

Wcześniej [artykułu](site-recovery-retain-ip-azure-vm-failover.md#on-premises-to-azure-connectivity), opisane prostą konfigurację obejmującą jednej podłączonej sieci wirtualnej platformy Azure do klienta lokalnego centrum danych za pośrednictwem usługi ExpressRoute. Wdrożenia typowego przedsiębiorstwa mają obciążeń podzielone między wieloma sieciami wirtualnymi platformy Azure i koncentrator centralnej łączności ustanawia łączność zewnętrzną, z Internetem i wdrożeniach lokalnych.

W tym przykładzie przedstawiono topologii gwiazdy, co jest typowe w przypadku wdrożeń w przedsiębiorstwie:
-   Wdrożenie jest w **Azja Wschodnia Azure** regionu i lokalnego centrum danych ma połączenie obwodu ExpressRoute za pośrednictwem krawędzi Hongkong partnera.
-   Wdrożenia aplikacji w sieciach wirtualnych dwóch gwiazdy — **VNet1 źródła** z 10.1.0.0/24 przestrzeni adresowej i **VNet2 źródła** z 10.2.0.0/24 przestrzeni adresowej.
-   Centrum sieci wirtualnej, a **sieci wirtualnej Centrum źródła**, z przestrzeni adresowej 10.10.10.0/24 działa jako kontroler. Cała komunikacja między podsieciami przechodzi przez koncentratora.
-   Sieć wirtualna Centrum ma dwie podsieci — **podsieci NVA** z 10.10.10.0/25 przestrzeni adresowej i **podsieci bramy** z 10.10.10.128/25 przestrzeni adresowej.
-   **Podsieci NVA** ma urządzenie wirtualne sieci o adresie IP 10.10.10.10.
-   **Podsieci bramy** nawiązał połączenie bramy ExpressRoute połączenia ExpressRoute, który kieruje do klienta lokalnego centrum danych za pośrednictwem prywatnej komunikacji równorzędnej domeny routingu.
-   Każdy gwiazdy sieci wirtualnej jest podłączony do sieci wirtualnej w Centrum i wszystkie routingu w tej topologii sieci jest kontrolowany za pomocą tabel tras platformy Azure (przez). Ruch wychodzący z jedną sieć wirtualną do sieci wirtualnej lub w lokalnym centrum danych jest kierowany przez analizę NVA.

![Na lokalnym do Azure z ExpressRoute przed trybu Failover](./media/azure-vm-disaster-recovery-with-expressroute/site-recovery-with-expressroute-before-failover.png)

### <a name="hub-and-spoke-peering"></a>Koncentrator i równorzędna gwiazdy

Gwiazdy do koncentratora równorzędna ma następującą konfigurację:
-   Zezwalaj na adres sieci wirtualnej: włączone
-   Zezwalaj na związanego z przekazywaniem ruchu: włączone
-   Zezwalaj na bramy przesyłania: wyłączone
-   Użyj Usuń bram: włączone

 ![Gwiazda Centrum konfiguracji komunikacji równorzędnej](./media/azure-vm-disaster-recovery-with-expressroute/spoke-to-hub-peering-configuration.png)

Koncentrator do Gwiazda równorzędna ma następującą konfigurację:
-   Zezwalaj na adres sieci wirtualnej: włączone
-   Zezwalaj na związanego z przekazywaniem ruchu: włączone
-   Zezwalaj na bramy przesyłania: włączone
-   Użyj Usuń bram: wyłączone

 ![Koncentrator do Gwiazda konfiguracji komunikacji równorzędnej](./media/azure-vm-disaster-recovery-with-expressroute/hub-to-spoke-peering-configuration.png)

### <a name="enabling-replication-for-the-deployment"></a>Włączanie replikacji dla wdrożenia

Powyżej instalacji pierwszego [Konfigurowanie odzyskiwania po awarii](azure-to-azure-tutorial-enable-replication.md) dla każdej maszyny wirtualnej przy użyciu usługi Site Recovery. Usługa Site Recovery można utworzyć repliki sieci wirtualnych (w tym podsieci i podsieci bramy) na region docelowy i utworzyć wymagane mapowania między sieciami wirtualnymi źródłowe i docelowe. Można wstępnie utworzyć sieci po stronie docelowej i podsieci i używać tego samego podczas włączania replikacji.

Usługa Site Recovery nie jest replikowany, tabele tras, bramy sieci wirtualnej, połączenia bramy sieci wirtualnej, sieci wirtualnej komunikacji równorzędnej, lub wszelkie inne sieci zasobów lub połączeń. Te i inne zasoby, które nie należą do [proces replikacji](azure-to-azure-architecture.md#replication-process) muszą zostać utworzone w trakcie lub przed trybu failover i podłączony do odpowiednich zasobów. Korzystając z usługi Azure Site Recovery zaawansowane [planów odzyskiwania](site-recovery-create-recovery-plans.md) można zautomatyzować tworzenie i łączenie dodatkowych zasobów przy użyciu skryptów automatyzacji.

Domyślnie ruch związany z replikacją nie opuszczają granicy Azure. Zazwyczaj wdrożeń NVA również definiować trasa domyślna (0.0.0.0/0), która wymusza wychodzący ruch internetowy przepływają przez analizę NVA. W takim przypadku urządzenia mogą uzyskać ograniczony, jeśli cały ruch replikacji przechodzi przez analizę NVA. To samo dotyczy również w przypadku używania trasy domyślne dla routingu cały ruch maszyny Wirtualnej platformy Azure do wdrożenia lokalnego. Firma Microsoft zaleca [tworzenie punkt końcowy usługi sieci wirtualnej](azure-to-azure-about-networking.md#create-network-service-endpoint-for-storage) w wirtualnej sieci "Magazynu", aby ruch związany z replikacją nie opuszczają granicy Azure.

## <a name="failover-models-with-expressroute"></a>Modele trybu failover z usługi ExpressRoute

W przypadku maszyn wirtualnych platformy Azure są Failover w innym regionie, istniejące połączenie ExpressRoute do źródłowej sieci wirtualnej jest nie automatycznie przetransferować do docelowej sieci wirtualnej na region odzyskiwania. Nowe połączenie, jest wymagany do połączenia ExpressRoute do docelowej sieci wirtualnej.

Umożliwia replikowanie maszyn wirtualnych platformy Azure na dowolnym regionie Azure, w tym samym klastrze geograficznej zgodnie z opisem [tutaj](azure-to-azure-support-matrix.md#region-support). Wybranego celu region platformy Azure nie mieści się w tym samym regionie geograficznymi jako źródła, należy włączyć Premium usługi ExpressRoute, jeśli używasz jednego obwodu ExpressRoute łączności region źródłowa i docelowa. Aby uzyskać więcej informacji, sprawdź [lokalizacje ExpressRoute](../expressroute/expressroute-locations.md#azure-regions-to-expressroute-locations-within-a-geopolitical-region) i [cennik usługi ExpressRoute](https://azure.microsoft.com/en-us/pricing/details/expressroute/).

### <a name="two-expressroute-circuits-in-two-different-expressroute-peering-locations"></a>Dwa obwody usługi ExpressRoute w dwóch różnych lokalizacjach komunikacji równorzędnej ExpressRoute
-   Ta konfiguracja jest użyteczna, jeśli chcesz upewnić się przed awariami głównej obwodu ExpressRoute i przed na dużą skalę regionalnej awarii, których można również wpływać na działanie lokalizacje komunikacji równorzędnej ExpressRoute i zakłócić jego głównej obwodu ExpressRoute.
-   Zwykle obwodu podłączone do środowiska produkcyjnego służy jako obwodu głównej i dodatkowej obwodu jest odporność i zazwyczaj o mniejszej przepustowości sieci. W przypadku awarii po lokacji dodatkowej musi mieć nad jako podstawowy można zwiększyć przepustowość pomocniczej.
-   W tej konfiguracji można nawiązać połączenia z pomocniczego obwodu ExpressRoute siecią wirtualną w docelowej po pracy awaryjnej lub połączeń ustanowionych i gotowa do deklaracji po awarii zmniejszenie ogólnego czasu odzyskiwania. Jednoczesnych połączeń podstawowych i region docelowy sieci wirtualne upewnić, że lokalnej routingu używa połączenia tylko po pracy awaryjnej i dodatkowej obwodu.
-   Źródłowa i docelowa sieci wirtualnej maszyny wirtualne chronione przy użyciu usługi Site Recovery może mieć tej samej lub różnych adresów IP w trybie failover na wymagań. W obu przypadkach można ustanowić połączenia dodatkowej przed trybu failover.

### <a name="two-expressroute-circuits-in-the-same-expressroute-peering-location"></a>Dwa obwody usługi ExpressRoute w tej samej lokalizacji komunikacji równorzędnej ExpressRoute
-   W tej konfiguracji należy upewnić się przed awariami głównej obwodu usługi expressroute, ale nie na dużą skalę regionalnej awarii, który może mieć wpływ na lokalizacje komunikacji równorzędnej ExpressRoute. Za można pobrać wpływ na obwody podstawowego i pomocniczego.
-   Inne warunki dla połączeń i adresy IP pozostaną takie same jak w przypadku wcześniejszych. Można mieć równoczesnych połączeń z lokalnego centrum danych do źródłowej sieci wirtualnej z obiegu podstawowego i do docelowej sieci wirtualnej z obwodem dodatkowej. Jednoczesnych połączeń podstawowych i region docelowy sieci wirtualne upewnić, że lokalnej routingu używa połączenia tylko po pracy awaryjnej i dodatkowej obwodu.
-   Gdy obwody są tworzone w tej samej lokalizacji komunikacji równorzędnej, nie można połączyć zarówno obwody do tej samej sieci wirtualnej.

### <a name="single-expressroute-circuit"></a>Pojedynczy obwodu usługi expressroute
-   Ta konfiguracja nie upewnić się na dużą skalę regionalnych awarii, który może mieć wpływ na lokalizację komunikacji równorzędnej ExpressRoute.
-   Z pojedynczego obwodu ExpressRoute nie można połączyć źródłowego i docelowych sieci wirtualnych równocześnie z obwodem, jeśli jest używany z tą samą przestrzenią adresów IP, na region docelowy.
-   W przypadku tą samą przestrzenią adresów IP na region docelowy połączenia po stronie źródła musi być odłączony, a później nawiązać połączenia po stronie docelowej. Ta zmiana połączenia umożliwia pisanie skryptów w ramach planu odzyskiwania.
-   Regionalne błąd jeśli regionu podstawowego jest niedostępny, operację rozłączania może zakończyć się niepowodzeniem. Takich awarii może mieć wpływ na region docelowy tworzenia połączenia, gdy tą samą przestrzenią adresów IP jest używany w docelowej sieci wirtualnej.
-   Tworzenie połączenia zakończy się pomyślnie na docelowym regionie podstawowym odzyskuje później, jeśli dwa połączenia próbę nawiązania połączenia z tą samą przestrzenią adresów można stają przed porzucania pakietów. Aby zapobiec porzucania pakietów, podstawowe połączenie powinno być zakończone natychmiast. Post powrotu po awarii maszyn wirtualnych do regionu podstawowego, może połączeniem podstawowym w ponownie ustanowić po odłączeniu połączenia dodatkowej.
-   Użycie miejsca na inny adres w docelowej sieci wirtualnej, następnie można jednocześnie połączyć się ze źródłem i docelowych sieci wirtualne od tego samego obwodu usługi expressroute.

## <a name="recovering-azure-deployments"></a>Odzyskiwanie wdrożeń platformy Azure
Należy wziąć pod uwagę model trybu failover z dwóch różnych obwody usługi ExpressRoute w dwóch różnych lokalizacjach komunikacji równorzędnej i przechowywania prywatnych adresów IP dla chronionych maszyn wirtualnych platformy Azure. Region docelowy odzyskiwania jest Azja południowo-wschodnia Azure i dodatkowej połączenia obwodu ExpressRoute zostanie nawiązane za pośrednictwem krawędzi partnera w Singapurze.

Aby zautomatyzować odzyskiwania całego wdrożenia, oprócz replikowanie maszyn wirtualnych i sieci wirtualne innych odpowiednich zasobów sieciowych i połączeń również należy utworzyć. W starszych gwiazdy topologii sieci następujące dodatkowe kroki należy podjąć w trakcie lub po [pracy awaryjnej](azure-to-azure-tutorial-failover-failback.md) operacji:
1.  Utwórz bramę usługi ExpressRoute Azure w sieci wirtualnej Centrum region docelowy. Bramę usługi ExpressRoute jest wymagane do połączenia z sieci wirtualnej koncentrator docelowy obwodu usługi expressroute.
2.  Utwórz połączenie wirtualnej sieci z siecią wirtualną koncentrator docelowy do docelowego obwodu usługi expressroute.
3.  Konfigurowanie komunikacji równorzędnych sieci wirtualnej między centrum region docelowy i gwiazdy sieci wirtualnych. Właściwości komunikacji równorzędnej, na region docelowy będą takie same jak w regionie źródłowym.
4.  Skonfiguruj Udr w Centrum w sieci wirtualnej i gwiazdy dwie sieci wirtualne. Właściwości po stronie docelowej Udr są takie same jak te na stronie źródło podczas używania tego samego adresu IP adresów. Z adresami IP inny element docelowy Udr powinno zostać zmodyfikowane w związku z tym.

Powyższe kroki mogą być przetwarzane przez skrypty jako część [planu odzyskiwania](site-recovery-create-recovery-plans.md). W zależności od wymagań dotyczących czasu odzyskiwania i łączności aplikacji powyższe kroki mogą być wykonywane przed rozpoczęciem pracy awaryjnej.

Odzyskiwanie maszyn wirtualnych i innych spełnienie łączności, środowisko odzyskiwania wygląda następująco: ![na lokalnym do Azure z usługi ExpressRoute po pracy awaryjnej](./media/azure-vm-disaster-recovery-with-expressroute/site-recovery-with-expressroute-after-failover.png)

Przykład prostą topologię, odzyskiwania po awarii maszyny Wirtualnej platformy Azure z pojedynczego obwodu ExpressRoute, z tego samego adresu IP na docelowych maszyn wirtualnych, są szczegółowe [tutaj](site-recovery-retain-ip-azure-vm-failover.md#on-premises-to-azure-connectivity).

## <a name="recovery-time-objective-rto-considerations"></a>Zagadnienia dotyczące celu czasu (RTO) odzyskiwanie
Aby zmniejszyć całkowity czas odzyskiwania dla danego wdrożenia, zaleca się udostępniania i wdrażanie region docelowy dodatkowych [składników sieciowych](azure-vm-disaster-recovery-with-expressroute.md#enabling-replication-for-the-deployment) takich jak wcześniej bram sieci wirtualnej. Małych przestoju jest skojarzone z wdrożeniem dodatkowych zasobów, a ten czas przestoju może mieć wpływ na całkowity czas odzyskiwania, jeśli nie miał podczas planowania.

Zaleca się uruchomienie regular [testowanie odzyskiwania po awarii](azure-to-azure-tutorial-dr-drill.md) chronione wdrożeń. Przechodzenie do szczegółów weryfikuje strategii replikacji bez utraty danych lub czas przestoju i nie ma wpływu na środowisko produkcyjne. Także uruchomiona Przechodzenie do szczegółów pozwala uniknąć problemów z konfiguracją ostatnich, które może niekorzystnie wpływać na celu czasu odzyskiwania. Zalecamy użycie osobnej sieci maszyny Wirtualnej platformy Azure do testowania trybu failover, zamiast domyślnej sieci, który został skonfigurowany po włączeniu replikacji.

Jeśli używasz jednego obwodu ExpressRoute, firma Microsoft zaleca, aby uniknąć problemów ustanowienia połączenia podczas regionalnej awarii przy użyciu różnych przestrzeń adresów IP dla sieci wirtualnej docelowej. Przy użyciu różnych adresów IP nie jest realne dla środowiska produkcyjnego odzyskane, awaryjnego odzyskiwania szczegółowego testowania trybu failover ma się odbywać w oddzielnych testu sieci z różnymi adresami IP zgodnie z nakładającymi się IP nie można połączyć dwie sieci wirtualne przestrzeni adresowej do tego samego obwodu usługi expressroute.

## <a name="next-steps"></a>Kolejne kroki
- Dowiedz się więcej o [obwody usługi ExpressRoute](../expressroute/expressroute-circuit-peerings.md).
- Dowiedz się więcej o [domeny routingu usługi ExpressRoute](../expressroute/expressroute-circuit-peerings.md#expressroute-routing-domains).
- Dowiedz się więcej o [lokalizacje ExpressRoute](../expressroute/expressroute-locations.md).
- Dowiedz się więcej o [planów odzyskiwania](site-recovery-create-recovery-plans.md) Aby zautomatyzować tryb failover aplikacji.
