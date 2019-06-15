---
title: Za pomocą usługi Azure ExpressRoute za pomocą usługi Azure Site Recovery na potrzeby odzyskiwania po awarii i migracji — informacje | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak używać usługi Azure ExpressRoute za pomocą usługi Azure Site Recovery na potrzeby odzyskiwania po awarii i migracji.
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 4/18/2019
ms.author: mayg
ms.openlocfilehash: bf4cce8a224db81b8db7fae6a69b8b578bb3d47a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60772321"
---
# <a name="azure-expressroute-with-azure-site-recovery"></a>Usługa ExpressRoute systemu Azure za pomocą usługi Azure Site Recovery

Usługa Microsoft Azure ExpressRoute umożliwia rozszerzanie sieci lokalnych na chmurę Microsoft za pośrednictwem połączenia prywatnego obsługiwanego przez dostawcę połączenia. Dzięki usłudze ExpressRoute można ustanowić połączenia z usługami Microsoft w chmurze, np. Microsoft Azure, Office 365 i Dynamics 365.

W tym artykule opisano, jak używać usługi Azure ExpressRoute z usługi Azure Site Recovery na potrzeby odzyskiwania po awarii i migracji.

## <a name="expressroute-circuits"></a>Obwody usługi ExpressRoute

Obwód usługi ExpressRoute reprezentuje połączenie logiczne między lokalną infrastrukturą i usługami chmurowymi firmy Microsoft za pośrednictwem dostawcy łączności. Może zamówić łączność obejmującą wiele obwodów usługi ExpressRoute. Każdy obwód może znajdować się w tej samej lub różnych regionach i mogą być połączone z lokalnym za pośrednictwem połączenia różnych dostawców. Dowiedz się więcej o obwodów usługi ExpressRoute [tutaj](../expressroute/expressroute-circuit-peerings.md).

## <a name="expressroute-routing-domains"></a>Domeny routingu usługi ExpressRoute

Obwód usługi ExpressRoute ma wiele domen routingu skojarzone z nią:
-   [Prywatnej komunikacji równorzędnej Azure](../expressroute/expressroute-circuit-peerings.md#privatepeering) — usługi Azure compute usług, a mianowicie maszyn wirtualnych (IaaS) i usług w chmurze (PaaS), które zostały wdrożone w ramach sieci wirtualnej mogą być połączone za pośrednictwem prywatnej komunikacji równorzędnej domeny. Prywatnej komunikacji równorzędnej domeny jest uznawane za zaufane rozszerzenie sieci podstawowej w Microsoft Azure.
-   [Publicznej komunikacji równorzędnej Azure](../expressroute/expressroute-circuit-peerings.md#publicpeering) -usług, takich jak Azure Storage, bazy danych SQL i witryn sieci Web są oferowane na publicznych adresów IP. Prywatnie można połączyć do usług hostowanych na publicznych adresów IP, w tym VIP usługi w chmurze za pośrednictwem publicznej komunikacji równorzędnej domeny routingu. Publiczna komunikacja równorzędna jest przestarzała dla utworzenie nowych kont i Peering firmy Microsoft należy użyć dla usług PaaS platformy Azure.
-   [Komunikacja równorzędna firmy Microsoft](../expressroute/expressroute-circuit-peerings.md#microsoftpeering) — łączność z usługami online firmy Microsoft (usługi Office 365, Dynamics 365 i PaaS platformy Azure) jest za pośrednictwem komunikacji równorzędnej firmy Microsoft. Komunikacja równorzędna firmy Microsoft jest zalecane domeny routingu, aby połączyć się z usługami PaaS platformy Azure.

Dowiedz się więcej o i porównaj domeny routingu usługi ExpressRoute [tutaj](../expressroute/expressroute-circuit-peerings.md#peeringcompare).

## <a name="on-premises-to-azure-replication-with-expressroute"></a>Środowiska lokalnego do platformy Azure replikacji przy użyciu usługi ExpressRoute

Usługa Azure Site Recovery umożliwia odzyskiwanie po awarii i migracji na platformę Azure dla lokalnych [maszyn wirtualnych funkcji Hyper-V](hyper-v-azure-architecture.md), [maszyn wirtualnych VMware](vmware-azure-architecture.md), i [serwerów fizycznych](physical-azure-architecture.md). Dla całego środowiska lokalnego do platformy Azure scenariuszy dane replikacji są wysyłane do i przechowywane na koncie usługi Azure Storage. Podczas replikacji nie płać opłatami związanymi z maszyny wirtualnej. Po uruchomieniu trybu failover na platformie Azure Site Recovery automatycznie tworzy maszyny wirtualne IaaS platformy Azure.

Usługa Site Recovery replikuje dane do konta usługi Azure Storage za pośrednictwem publicznego punktu końcowego. Za pomocą usługi ExpressRoute dla replikacji usługi Site Recovery, możesz użyć [publicznej komunikacji równorzędnej](../expressroute/expressroute-circuit-peerings.md#publicpeering) (uznane za przestarzałe w utworzenie nowych kont) lub [komunikacji równorzędnej firmy Microsoft](../expressroute/expressroute-circuit-peerings.md#microsoftpeering). Komunikacja równorzędna firmy Microsoft jest zalecanym domen routingu replikacji. Upewnij się, że [wymagania sieciowe](vmware-azure-configuration-server-requirements.md#network-requirements) spełnione są również do replikacji. Po maszyn wirtualnych lub serwerach pracy awaryjnej z siecią wirtualną platformy Azure, można z nich korzystać przy użyciu [prywatnej komunikacji równorzędnej](../expressroute/expressroute-circuit-peerings.md#privatepeering). Replikacja nie jest obsługiwana za pośrednictwem prywatnej komunikacji równorzędnej.

W przypadku używania serwera proxy w lokalnych i chcesz korzystać z usługi ExpressRoute dla ruchu związanego z replikacją, musisz skonfigurować listę obejścia serwera Proxy na serwerze konfiguracji i serwerów przetwarzania. Wykonaj poniższe kroki:

- Pobierz narzędzie PsExec z [tutaj](https://aka.ms/PsExec) do dostępu do kontekstu użytkownika systemu.
- Otwórz program Internet Explorer w kontekście użytkownika w systemie, uruchamiając następujące wiersza polecenia programu psexec -s -i "%programfiles%\Internet Explorer\iexplore.exe"
- Dodaj ustawienia serwera proxy w programie Internet Explorer
- Na liście obejścia, Dodaj adres URL usługi Azure storage *. blob.core.windows.net

Pozwoli to zagwarantować, że tylko ruch związany z replikacją przepływy za pośrednictwem usługi ExpressRoute podczas przejść komunikację za pośrednictwem serwera proxy.

Połączone scenariusz jest reprezentowane na poniższym diagramie: ![W lokalnym — z platformą Azure przy użyciu usługi ExpressRoute](./media/concepts-expressroute-with-site-recovery/site-recovery-with-expressroute.png)

## <a name="azure-to-azure-replication-with-expressroute"></a>Replikacji Azure – Azure przy użyciu usługi ExpressRoute

Usługa Azure Site Recovery umożliwia odzyskiwanie po awarii [maszyn wirtualnych platformy Azure](azure-to-azure-architecture.md). W zależności od tego, czy maszyn, sieci wirtualnej platformy Azure użyj [usługi Azure Managed Disks](../virtual-machines/windows/managed-disks-overview.md), dane replikacji są wysyłane do konta usługi Azure Storage lub repliki z dysków zarządzanych w elemencie docelowym regionem świadczenia usługi Azure. Mimo że punkty końcowe replikacji są publiczne, ruch związany z replikacją dla replikacji maszyny Wirtualnej platformy Azure, domyślnie nie przechodzi przez Internet, niezależnie od tego, który region świadczenia usługi Azure źródłowa sieć wirtualna istnieje w. Można zastąpić domyślną trasę systemową platformy Azure dla prefiksu adresu 0.0.0.0/0 za pomocą [trasy niestandardowej](../virtual-network/virtual-networks-udr-overview.md#custom-routes) przekierowywać ruch maszyny Wirtualnej do środowiska lokalnego wirtualnego urządzenia sieciowego (WUS), ale ta konfiguracja nie jest zalecana dla usługi Site Recovery Replikacja. Jeśli używasz niestandardowych tras, wykonaj następujące czynności [Tworzenie punktu końcowego usługi sieci wirtualnej](azure-to-azure-about-networking.md#create-network-service-endpoint-for-storage) w wirtualnej sieci dla "Magazyn", aby ruch związany z replikacją nie opuszczają platformy Azure.

Do odzyskiwania po awarii maszyny Wirtualnej platformy Azure Domyślnie usługi ExpressRoute nie jest wymagana na potrzeby replikacji. Po maszyny wirtualne awaryjnie do obiektu docelowego regionu świadczenia usługi Azure, można z nich korzystać przy użyciu [prywatnej komunikacji równorzędnej](../expressroute/expressroute-circuit-peerings.md#privatepeering).

Jeśli usługa ExpressRoute jest już używany do łączenia z lokalnym centrum danych z maszyn wirtualnych platformy Azure w regionie źródłowym, można zaplanować dla kolejnego ustanawiania połączenia usługi ExpressRoute w regionie docelowym w tryb failover. Można użyć tego samego obwodu usługi ExpressRoute do łączenia region docelowy za pomocą nowego połączenia sieci wirtualnej lub korzystanie z oddzielnych obwód usługi ExpressRoute i połączeń na potrzeby odzyskiwania po awarii. Opisano różne możliwe scenariusze [tutaj](azure-vm-disaster-recovery-with-expressroute.md#fail-over-azure-vms-when-using-expressroute).

Można replikować maszyny wirtualne platformy Azure do dowolnego regionu systemu Azure, w tym samym klastrze geograficznej zgodnie z opisem [tutaj](../site-recovery/azure-to-azure-support-matrix.md#region-support). Wybrany element docelowy region platformy Azure nie mieści się w tym samym regionie geograficznymi jako źródło, może być konieczne Włączanie ExpressRoute Premium. Aby uzyskać więcej informacji, sprawdź [lokalizacji usługi ExpressRoute](../expressroute/expressroute-locations.md#azure-regions-to-expressroute-locations-within-a-geopolitical-region) i [ExpressRoute — cennik](https://azure.microsoft.com/pricing/details/expressroute/).

## <a name="next-steps"></a>Kolejne kroki
- Dowiedz się więcej o [obwodów usługi ExpressRoute](../expressroute/expressroute-circuit-peerings.md).
- Dowiedz się więcej o [domeny routingu usługi ExpressRoute](../expressroute/expressroute-circuit-peerings.md#peeringcompare).
- Dowiedz się więcej o [lokalizacji usługi ExpressRoute](../expressroute/expressroute-locations.md).
- Dowiedz się więcej na temat odzyskiwania po awarii [maszyn wirtualnych platformy Azure przy użyciu usługi ExpressRoute](azure-vm-disaster-recovery-with-expressroute.md).
