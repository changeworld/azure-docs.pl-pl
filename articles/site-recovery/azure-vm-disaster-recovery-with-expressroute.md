---
title: Za pomocą odzyskiwania po awarii maszyn wirtualnych platformy Azure przy użyciu usługi ExpressRoute | Dokumentacja firmy Microsoft
description: Opisuje sposób używania usługi Azure ExpressRoute za pomocą usługi Azure Site Recovery do odzyskiwania po awarii maszyn wirtualnych platformy Azure
services: site-recovery
documentationcenter: ''
author: mayanknayar
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 07/06/2018
ms.author: manayar
ms.openlocfilehash: 73514b524f554affb9730ba63ccd608491497af2
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2018
ms.locfileid: "37920474"
---
# <a name="using-expressroute-with-azure-virtual-machine-disaster-recovery"></a>Przy użyciu usługi ExpressRoute za pomocą odzyskiwania po awarii maszyn wirtualnych platformy Azure

Usługa Microsoft Azure ExpressRoute umożliwia rozszerzanie sieci lokalnych na chmurę Microsoft za pośrednictwem połączenia prywatnego obsługiwanego przez dostawcę połączenia. W tym artykule opisano, jak mogą używać usługi ExpressRoute z usługą Site Recovery do odzyskiwania po awarii maszyn wirtualnych platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że rozumiesz:
-   Usługa ExpressRoute [obwodów](../expressroute/expressroute-circuit-peerings.md)
-   Usługa ExpressRoute [domen routingu](../expressroute/expressroute-circuit-peerings.md#expressroute-routing-domains)
-   Maszyna wirtualna platformy Azure [architektura replikacji](azure-to-azure-architecture.md)
-   [Konfigurowanie replikacji](azure-to-azure-tutorial-enable-replication.md) maszyn wirtualnych platformy Azure
-   [Przechodzenie w tryb failover](azure-to-azure-tutorial-failover-failback.md) maszyn wirtualnych platformy Azure

## <a name="expressroute-and-azure-virtual-machine-replication"></a>Replikacja maszyny wirtualnej usługi ExpressRoute i Azure

W przypadku ochrony maszyn wirtualnych platformy Azure z usługą Site Recovery, dane replikacji są wysyłane do konta usługi Azure Storage lub repliki z dysków zarządzanych w elemencie docelowym regionie platformy Azure, w zależności od tego, czy maszyn, sieci wirtualnej platformy Azure użyj [usługi Azure Managed Disks](../virtual-machines/windows/managed-disks-overview.md). Mimo że punkty końcowe replikacji są publiczne, ruch związany z replikacją dla replikacji maszyny Wirtualnej platformy Azure, domyślnie nie przechodzi przez Internet, niezależnie od tego, który region świadczenia usługi Azure źródłowa sieć wirtualna istnieje w.

Do odzyskiwania po awarii maszyny Wirtualnej platformy Azure jak dane replikacji nie opuszczają platformy Azure, usługi ExpressRoute nie jest wymagana na potrzeby replikacji. Po maszyny wirtualne awaryjnie do obiektu docelowego regionu świadczenia usługi Azure, można z nich korzystać przy użyciu [prywatnej komunikacji równorzędnej](../expressroute/expressroute-circuit-peerings.md#azure-private-peering).

## <a name="replicating-azure-deployments"></a>Replikowanie wdrożeń platformy Azure

Wcześniej [artykułu](site-recovery-retain-ip-azure-vm-failover.md#on-premises-to-azure-connectivity), opisano prostą konfigurację obejmującą jednej sieci wirtualnej platformy Azure podłączonej do klienta w lokalnym centrum danych za pośrednictwem usługi ExpressRoute. Wdrożenia typowego przedsiębiorstwa mają obciążeń podzielone między wieloma sieciami wirtualnymi platformy Azure i koncentrator centralny łączności ustanawia łączność zewnętrzną z Internetem i wdrożeń lokalnych.

W tym przykładzie przedstawiono topologii gwiazdy, co jest typowe w przypadku wdrożeń w przedsiębiorstwie:
-   Wdrażanie jest w **Azja Wschodnia Azure** region i lokalnym centrum danych ma połączenie obwodu usługi ExpressRoute za pośrednictwem przewagę partnera w Hongkongu.
-   Aplikacje są wdrażane na dwie sieci wirtualne będące szprychami — **VNet1 źródła** z 10.1.0.0/24 przestrzeni adresowej i **VNet2 źródła** z 10.2.0.0/24 przestrzeni adresowej.
-   Sieci wirtualnej koncentratora **źródła piastą**, się na przestrzeń adresową 10.10.10.0/24 pełni rolę strażnika. Cała komunikacja między podsieciami odbywa się za pośrednictwem Centrum.
-   Centralnej sieci wirtualnej ma dwie podsieci — **podsieci urządzenie WUS** z 10.10.10.0/25 przestrzeni adresowej i **podsieci bramy** z 10.10.10.128/25 przestrzeni adresowej.
-   **Podsieci urządzenie WUS** ma wirtualnego urządzenia sieciowego za pomocą adresu IP. 10.10.10.10.
-   **Podsieci bramy** połączył bramę usługi ExpressRoute do połączenia usługi ExpressRoute, która kieruje do klientów w lokalnym centrum danych za pośrednictwem prywatnej komunikacji równorzędnej domeny routingu.
-   Każda sieć wirtualna gwiazdy jest podłączony do sieci wirtualnej koncentratora i wszystkie routing w tej topologii sieci jest kontrolowany przy użyciu tabel tras platformy Azure (UDR). Ruch wychodzący z jednej sieci wirtualnej druga sieć wirtualna lub w lokalnym centrum danych odbywa się za pośrednictwem urządzenia NVA.

![W lokalnym — z platformą Azure przy użyciu usługi ExpressRoute, przed włączeniem trybu Failover](./media/azure-vm-disaster-recovery-with-expressroute/site-recovery-with-expressroute-before-failover.png)

### <a name="hub-and-spoke-peering"></a>Centrum i komunikacji równorzędnej gwiazdy

Szprychy do Centrum komunikacji równorzędnej ma następującą konfigurację:
-   Zezwalaj na adres sieci wirtualnej: włączone
-   Zezwalaj na ruch przesłany dalej: włączone
-   Zezwalaj na tranzyt bramy: wyłączone
-   Użyj usunąć bramy: włączone

 ![Typu gwiazda Centrum konfiguracji komunikacji równorzędnej](./media/azure-vm-disaster-recovery-with-expressroute/spoke-to-hub-peering-configuration.png)

Centrum do typu gwiazda komunikacji równorzędnej ma następującą konfigurację:
-   Zezwalaj na adres sieci wirtualnej: włączone
-   Zezwalaj na ruch przesłany dalej: włączone
-   Zezwalaj na tranzyt bramy: włączone
-   Użyj usunąć bramy: wyłączone

 ![Centrum typu gwiazda konfiguracji komunikacji równorzędnej](./media/azure-vm-disaster-recovery-with-expressroute/hub-to-spoke-peering-configuration.png)

### <a name="enabling-replication-for-the-deployment"></a>Włączanie replikacji dla wdrożenia

Powyższe instalacji pierwszego [Konfigurowanie odzyskiwania po awarii](azure-to-azure-tutorial-enable-replication.md) dla każdej maszyny wirtualnej przy użyciu Site Recovery. Usługa Site Recovery można utworzyć replikę sieci wirtualnych (w tym podsieci i podsieć bramy) w regionie docelowym i tworzyć wymagane mapowania między sieciami wirtualnymi źródłowym i docelowym. Można wstępnie utworzyć sieci po stronie docelowej i podsieci i używać tego samego podczas włączania replikacji.

Usługa Site Recovery nie jest replikowany, tabele tras, bramy sieci wirtualnej, połączeń bramy sieci wirtualnej, wirtualnych sieci równorzędnych, lub wszelkich innych sieci zasobów lub połączenia. Te i inne zasoby, które nie należą do [proces replikacji](azure-to-azure-architecture.md#replication-process) muszą zostać utworzone w lub przed włączeniem trybu failover i podłączone do odpowiednich zasobów. Można użyć usługi Azure Site Recovery zaawansowane [planów odzyskiwania](site-recovery-create-recovery-plans.md) zautomatyzować tworzenie i dołączanie dodatkowych zasobów za pomocą skryptów automatyzacji.

Domyślnie ruch związany z replikacją nie opuszczają platformy Azure. Zazwyczaj urządzenia WUS wdrożeń również zdefiniować trasy domyślnej (0.0.0.0/0), która wymusza ruch wychodzący z Internetu do przepływu przez urządzenie WUS. W takim przypadku urządzenie może być ograniczona w przypadku wszystkich ruch związany z replikacją przechodzi przez urządzenia WUS. To samo dotyczy także w przypadku używania tras domyślnych dla routingu cały ruch maszyny Wirtualnej platformy Azure do lokalnych wdrożeń. Firma Microsoft zaleca [Tworzenie punktu końcowego usługi sieci wirtualnej](azure-to-azure-about-networking.md#create-network-service-endpoint-for-storage) w wirtualnej sieci dla "Magazyn", aby ruch związany z replikacją nie opuszczają platformy Azure.

## <a name="failover-models-with-expressroute"></a>Modele trybu failover przy użyciu usługi ExpressRoute

W przypadku maszyn wirtualnych platformy Azure są przełączone w tryb failover w innym regionie, istniejące połączenie usługi ExpressRoute w sieci wirtualnej źródła jest nie automatycznie przenoszona do docelowej sieci wirtualnej w regionie odzyskiwania. Nowe połączenie jest wymagane do połączenia z usługi ExpressRoute docelowa sieć wirtualna.

Można replikować maszyny wirtualne platformy Azure do dowolnego regionu systemu Azure, w tym samym klastrze geograficznej zgodnie z opisem [tutaj](azure-to-azure-support-matrix.md#region-support). Wybrany element docelowy region platformy Azure nie mieści się w tym samym regionie geograficznymi jako źródło, należy włączyć ExpressRoute Premium, jeśli używasz jednego obwodu usługi ExpressRoute dla łączności regionie źródłowym i docelowym. Aby uzyskać więcej informacji, sprawdź [lokalizacji usługi ExpressRoute](../expressroute/expressroute-locations.md#azure-regions-to-expressroute-locations-within-a-geopolitical-region) i [ExpressRoute — cennik](https://azure.microsoft.com/pricing/details/expressroute/).

### <a name="two-expressroute-circuits-in-two-different-expressroute-peering-locations"></a>Dwa obwody usługi ExpressRoute w dwóch różnych lokalizacjach komunikacji równorzędnej usługi ExpressRoute
-   Ta konfiguracja jest użyteczna, jeśli chcesz upewnić się, awariami głównej obwodu usługi ExpressRoute i przed na dużą skalę regionalnej awarii, które może również wpływać na działanie lokalizacje komunikacji równorzędnej usługi ExpressRoute i zakłócać, podstawowy obwód usługi ExpressRoute.
-   Zwykle obwodu podłączone do środowiska produkcyjnego jest używany jako obwód podstawowy, a obwód pomocniczy jest odporność i zazwyczaj o mniejszej przepustowości sieci. W przypadku awarii, gdy pomocnicza musi przejąć jako podstawowy, można zwiększyć przepustowość pomocnicza.
-   W przypadku tej konfiguracji można nawiązać połączenia z pomocniczego obwodu usługi ExpressRoute do docelowej sieci wirtualnej po pracy awaryjnej lub połączeń ustanowionych i gotowa do deklaracji po awarii, zmniejszyć ogólny czas odzyskiwania. Za pomocą jednoczesnych połączeń podstawowych i sieci wirtualnych w regionie docelowym upewnić się, że lokalne routingu używa pomocniczego obwodu i połączenie tylko po włączeniu trybu failover.
-   Źródło i cel sieci wirtualnej dla maszyn wirtualnych chronionych przy użyciu usługi Site Recovery może mieć tych samych lub różnych adresów IP w trybie failover dla Twojego zapotrzebowania. W obu przypadkach dodatkowych połączeń, może zostać nawiązana przed trybu failover.

### <a name="two-expressroute-circuits-in-the-same-expressroute-peering-location"></a>Dwa obwody usługi ExpressRoute w tej samej lokalizacji komunikacji równorzędnej usługi ExpressRoute
-   W przypadku tej konfiguracji upewnij się, awariami głównej obwodu usługi ExpressRoute, ale nie względem na dużą skalę regionalnej awarii, co może mieć wpływ na lokalizacje komunikacji równorzędnej usługi ExpressRoute. W drugiej kolejności mogą mieć wpływ na obwodów podstawowego i pomocniczego.
-   Inne warunki dla połączeń i adresy IP pozostaną takie same, jak w przypadku wcześniejszych. Może mieć jednocześnie połączenia z lokalnym centrum danych, źródłowa sieć wirtualna za pomocą obwód podstawowy i docelowa sieć wirtualna za pomocą obwód pomocniczy. Za pomocą jednoczesnych połączeń podstawowych i sieci wirtualnych w regionie docelowym upewnić się, że lokalne routingu używa pomocniczego obwodu i połączenie tylko po włączeniu trybu failover.
-   Nie można połączyć zarówno obwodów do tej samej sieci wirtualnej, gdy obwodów tworzonych w tej samej lokalizacji komunikacji równorzędnej.

### <a name="single-expressroute-circuit"></a>Pojedynczy obwód usługi ExpressRoute
-   Ta konfiguracja nie upewnij się, względem dużych regionalnej awarii, które mogą mieć wpływ na lokalizację komunikacji równorzędnej usługi ExpressRoute.
-   Za pomocą jednego obwodu usługi ExpressRoute nie można połączyć źródłowego i docelowy sieci wirtualnych równocześnie z obwodem, jeśli tą samą przestrzenią adresów IP jest używany w regionie docelowym.
-   Stosowania tą samą przestrzenią adresów IP w regionie docelowym musi być odłączony połączenia po stronie źródła, a później nawiązać połączenia po stronie docelowej. Ta zmiana połączenia umożliwia pisanie skryptów, w ramach planu odzyskiwania.
-   W przypadku regionalnej awarii jeśli region podstawowy jest niedostępny, może nie działać operację rozłączania. Takie awaria może mieć wpływ na tworzenie połączenia w regionie docelowym, kiedy tą samą przestrzenią adresów IP jest używany w docelowej sieci wirtualnej.
-   Jeśli tworzenie połączenia zakończy się pomyślnie w elemencie docelowym i regionu podstawowego odzyskuje później, może napotkać pakietów docelowych, jeśli dwa połączenia próbują nawiązać połączenia z tą samą przestrzenią adresów. Aby zapobiec pakietów docelowych, połączenia podstawowego powinien zostać zakończony natychmiast. Wpis powrotu po awarii maszyn wirtualnych do regionu podstawowego, można połączenia podstawowego znów można ustanowić po odłączeniu połączenia pomocniczego.
-   Jeśli miejsca inny adres jest używany w docelowej sieci wirtualnej, następnie można jednocześnie połączenia ze źródłem i docelowymi sieciami wirtualnych z tego samego obwodu usługi ExpressRoute.

## <a name="recovering-azure-deployments"></a>Odzyskiwanie wdrożeń platformy Azure
Należy wziąć pod uwagę model trybu failover z dwóch różnych obwodów usługi ExpressRoute w dwóch różnych lokalizacjach komunikacji równorzędnej i przechowywania prywatnych adresów IP dla chronionych maszyn wirtualnych platformy Azure. Docelowy region odzyskiwania to wersja platformy Azure, Azja południowo-wschodnia i nawiązaniu połączenia pomocniczego obwodu usługi ExpressRoute za pośrednictwem przewagę partnera w Singapurze.

Aby zautomatyzować odzyskiwanie całe wdrożenie, oprócz replikowania maszyn wirtualnych i sieci wirtualne, połączenia i inne odpowiednie zasoby sieciowe, również należy tworzyć. Dla starszych gwiazdy topologia sieci następujące dodatkowe kroki należy wykonać podczas lub po [trybu failover](azure-to-azure-tutorial-failover-failback.md) operacji:
1.  Utwórz bramę usługi ExpressRoute platformy Azure w sieci wirtualnej koncentratora regionu docelowego. Brama usługi ExpressRoute jest wymagane do połączenia z sieci wirtualnej koncentratora docelowy obwód usługi ExpressRoute.
2.  Tworzenie połączenia sieci wirtualnej z sieci wirtualnej koncentratora docelowy do obiektu docelowego obwód usługi ExpressRoute.
3.  Konfigurowanie komunikacji równorzędnej sieci wirtualnej między centrum regionu docelowego i sieci wirtualne będące szprychami. Właściwości komunikacji równorzędnej w regionie docelowym będą takie same, jak w regionie źródłowym.
4.  Konfigurowanie tras zdefiniowanych przez użytkownika w sieci wirtualnej serwera centralnego i dwie sieci wirtualne będące szprychami. Właściwości po stronie docelowej tras zdefiniowanych przez użytkownika są takie same, jak po stronie źródła podczas przy użyciu tego samego adresu IP adresów. Za pomocą różnych docelowych adresów IP trasy Udr należy odpowiednio zmodyfikować.

Powyższe kroki umożliwia pisanie skryptów, jako część [planu odzyskiwania](site-recovery-create-recovery-plans.md). W zależności od aplikacji łączności i wymagań w zakresie czasu odzyskiwania powyższe kroki mogą być wykonywane przed rozpoczęciem pracy w trybie failover.

Odzyskiwanie maszyn wirtualnych i innych spełnienie łączności, środowisko odzyskiwania wygląda w następujący sposób: ![na lokalnym — z platformą Azure przy użyciu usługi ExpressRoute, po włączeniu trybu Failover](./media/azure-vm-disaster-recovery-with-expressroute/site-recovery-with-expressroute-after-failover.png)

Topologia prosty przykład odzyskiwanie po awarii maszyny Wirtualnej platformy Azure z jednego obwodu usługi ExpressRoute za pomocą tego samego adresu IP na docelowych maszynach wirtualnych, została szczegółowo opisana [tutaj](site-recovery-retain-ip-azure-vm-failover.md#on-premises-to-azure-connectivity).

## <a name="recovery-time-objective-rto-considerations"></a>Zagadnienia dotyczące odzyskiwania cel czasu (RTO)
Aby zmniejszyć ogólny czas odzyskiwania dla danego wdrożenia, zaleca się aprowizowanie i wdrażanie region docelowy dodatkowych [składników sieciowych](azure-vm-disaster-recovery-with-expressroute.md#enabling-replication-for-the-deployment) takich jak wcześniej bramy sieci wirtualnej. Małe przestój jest skojarzony z wdrażania dodatkowych zasobów, a tej awarii mogą mieć wpływ na ogólny czas odzyskiwania, jeśli nie uwzględnić podczas planowania.

Zalecamy regularne uruchamianie [odzyskiwanie po awarii](azure-to-azure-tutorial-dr-drill.md) chronione wdrożeń. Przechodzenie do szczegółów weryfikuje strategię replikacji bez utraty danych ani przestoju i nie ma wpływu na środowisko produkcyjne. Również uruchomione testowania odzyskiwania po awarii pozwala uniknąć problemów z konfiguracją ostatniej chwili, które może niekorzystnie wpłynąć na cel czasu odzyskiwania. Zalecamy używanie oddzielnej sieci maszyn wirtualnych platformy Azure do testowania trybu failover, a nie domyślnej sieci skonfigurowanej podczas włączania replikacji.

Jeśli używasz jednego obwodu usługi ExpressRoute firma Microsoft zaleca, przy użyciu różnych przestrzeń adresów IP dla docelowej sieci wirtualnej, aby uniknąć problemów ustanowienia połączenia podczas awarii regionalnego. Jeśli przy użyciu różnych adresów IP nie jest możliwe w środowisku produkcyjnym odzyskane, awaryjnego odzyskiwania Przechodzenie do testowania trybu failover ma się odbywać na osobna sieć testowa z różnymi adresami IP zgodnie z dwiema sieciami wirtualnymi nie można połączyć z nakładającymi się adresów IP przestrzeń adresowa z tym samym obwodem usługi ExpressRoute.

## <a name="next-steps"></a>Kolejne kroki
- Dowiedz się więcej o [obwodów usługi ExpressRoute](../expressroute/expressroute-circuit-peerings.md).
- Dowiedz się więcej o [domeny routingu usługi ExpressRoute](../expressroute/expressroute-circuit-peerings.md#expressroute-routing-domains).
- Dowiedz się więcej o [lokalizacji usługi ExpressRoute](../expressroute/expressroute-locations.md).
- Dowiedz się więcej o [planów odzyskiwania](site-recovery-create-recovery-plans.md) do automatyzowania trybu failover w aplikacji.
