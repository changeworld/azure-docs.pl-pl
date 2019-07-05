---
title: Za pomocą usługi Azure ExpressRoute za pomocą usługi Azure Site Recovery na potrzeby odzyskiwania po awarii i migracji — informacje | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak używać usługi Azure ExpressRoute za pomocą usługi Azure Site Recovery na potrzeby odzyskiwania po awarii i migracji.
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 6/27/2019
ms.author: mayg
ms.openlocfilehash: 35fa26112a6026ab05bd59b38621de7ee802c715
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/01/2019
ms.locfileid: "67491896"
---
# <a name="azure-expressroute-with-azure-site-recovery"></a>Usługa ExpressRoute systemu Azure za pomocą usługi Azure Site Recovery

Usługa Microsoft Azure ExpressRoute umożliwia rozszerzanie sieci lokalnych na chmurę Microsoft za pośrednictwem połączenia prywatnego obsługiwanego przez dostawcę połączenia. Dzięki usłudze ExpressRoute można ustanowić połączenia z usługami Microsoft w chmurze, np. Microsoft Azure, Office 365 i Dynamics 365.

W tym artykule opisano, jak używać usługi Azure ExpressRoute z usługi Azure Site Recovery na potrzeby odzyskiwania po awarii i migracji.

## <a name="expressroute-circuits"></a>Obwody usługi ExpressRoute

Obwód usługi ExpressRoute reprezentuje połączenie logiczne między lokalną infrastrukturą i usługami chmurowymi firmy Microsoft za pośrednictwem dostawcy łączności. Może zamówić łączność obejmującą wiele obwodów usługi ExpressRoute. Każdy obwód może znajdować się w tej samej lub różnych regionach i mogą być połączone z lokalnym za pośrednictwem połączenia różnych dostawców. Dowiedz się więcej o obwodów usługi ExpressRoute [tutaj](../expressroute/expressroute-circuit-peerings.md).

Obwód usługi ExpressRoute ma wiele domen routingu skojarzonych z nim. Dowiedz się więcej o i porównaj domeny routingu usługi ExpressRoute [tutaj](../expressroute/expressroute-circuit-peerings.md#peeringcompare).

## <a name="on-premises-to-azure-replication-with-expressroute"></a>Środowiska lokalnego do platformy Azure replikacji przy użyciu usługi ExpressRoute

Usługa Azure Site Recovery umożliwia odzyskiwanie po awarii i migracji na platformę Azure dla lokalnych [maszyn wirtualnych funkcji Hyper-V](hyper-v-azure-architecture.md), [maszyn wirtualnych VMware](vmware-azure-architecture.md), i [serwerów fizycznych](physical-azure-architecture.md). Dla całego środowiska lokalnego do platformy Azure scenariuszy dane replikacji są wysyłane do i przechowywane na koncie usługi Azure Storage. Podczas replikacji nie płać opłatami związanymi z maszyny wirtualnej. Po uruchomieniu trybu failover na platformie Azure Site Recovery automatycznie tworzy maszyny wirtualne IaaS platformy Azure.

Usługa Site Recovery replikuje dane do konta usługi Azure Storage lub repliki z dysków zarządzanych dla elementu docelowego regionu platformy Azure za pośrednictwem publicznego punktu końcowego. Za pomocą usługi ExpressRoute dla ruchu replikacji usługa Site Recovery, możesz użyć [komunikacji równorzędnej firmy Microsoft](../expressroute/expressroute-circuit-peerings.md#microsoftpeering) lub istniejącej [publicznej komunikacji równorzędnej](../expressroute/expressroute-circuit-peerings.md#publicpeering) (uznane za przestarzałe w utworzenie nowych kont). Komunikacja równorzędna firmy Microsoft jest zalecanym domen routingu replikacji. Należy pamiętać, że replikacja nie jest obsługiwana za pośrednictwem prywatnej komunikacji równorzędnej.

Upewnij się, że [wymagania sieciowe](vmware-azure-configuration-server-requirements.md#network-requirements) również są spełnione dla serwera konfiguracji. Łączność z określonych adresów URL jest wymagane przez serwer konfiguracji do organizowania replikacji usługi Site Recovery. Nie można użyć usługi ExpressRoute dla tego połączenia. 

W przypadku używania serwera proxy w lokalnych i chcesz korzystać z usługi ExpressRoute dla ruchu związanego z replikacją, musisz skonfigurować listę obejścia serwera Proxy na serwerze konfiguracji i serwerów przetwarzania. Wykonaj poniższe kroki:

- Pobierz narzędzie PsExec z [tutaj](https://aka.ms/PsExec) do dostępu do kontekstu użytkownika systemu.
- Otwórz program Internet Explorer w kontekście użytkownika w systemie, uruchamiając następujące wiersza polecenia programu psexec -s -i "%programfiles%\Internet Explorer\iexplore.exe"
- Dodaj ustawienia serwera proxy w programie Internet Explorer
- Na liście obejścia, Dodaj adres URL usługi Azure storage *. blob.core.windows.net

Pozwoli to zagwarantować, że tylko ruch związany z replikacją przepływy za pośrednictwem usługi ExpressRoute podczas przejść komunikację za pośrednictwem serwera proxy.

Po maszyn wirtualnych lub serwerach pracy awaryjnej z siecią wirtualną platformy Azure, można z nich korzystać przy użyciu [prywatnej komunikacji równorzędnej](../expressroute/expressroute-circuit-peerings.md#privatepeering). 

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
